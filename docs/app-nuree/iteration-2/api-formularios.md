---
hide:
  - navigation
---

# API de Formulários — o que as telas exigem

Levantamento do que a api precisa ganhar para sustentar a seção **`08 · Formulários`** do Figma
(`node-id=660-5949`, 44 frames). Rastreia a [E4](../requisitos.md#e4--criar-formulários-e-coletar-respostas)
e é a especificação do **backend**; o handoff de telas continua dono do vínculo desenho → front.

A api cobria E1, E2 e E3 (`modules/account`, `modules/auth`, `modules/task`) e nada de E4. Este
documento é o módulo `modules/form` inteiro — o levantamento que o originou e o registro do que foi
construído a partir dele.

!!! success "Estado: implementado, menos o que depende de decisão"
    O módulo `api/src/modules/form` está de pé: 7 modelos no Prisma, migration aplicada, 28 rotas no
    Swagger, e o `Media` (`src/infra/media/`) por trás dos anexos. 254 testes na suíte inteira, tudo
    verde.

    O `upload` funciona ponta a ponta: o `Media` foi implementado junto, com MinIO no compose (ver
    [Storage](#storage)). Fora ficaram **`sensitive`**, que depende da divergência 3, e
    **`JOURNEY_STAGE`**, que depende de E7 — o enum já nasce completo e a escrita recusa até lá.
    As demais divergências foram implementadas sob suposição explícita, marcada no código e listada
    em [Divergências](#divergencias).

## O que as telas mostram

| Grupo | Frames | O que pede da api |
|---|---|---|
| Biblioteca | `660:5950` (desktop), `664:5797` (mobile), + vazia, carregando, busca sem resultado | listar, buscar, filtrar, paginar; contadores derivados |
| Overlays da biblioteca | novo (`733:8886`), excluir (`670:6230`), filtros (`670:6260`), exportar (`733:8962`) | criar (em branco ou por cópia), soft-delete, link público + QR |
| Construtor | `677:6271`, `682:6386` (escolha única), `684:6497` (vazio), `684:7406` (arrastando), `685:6718` (pré-visualização), `717:8360` (quiz), `687:6890`/`688:6890` (mobile) | seções, campos de 8 tipos, config por tipo, reordenação, gabarito, tempo-limite |
| Atribuições | `701:7383` + overlay `704:7734` / `704:7733` (já atribuído) | pendurar o formulário em programa, etapa de jornada ou participação, com prazo próprio |
| Respostas (admin) | `708:7760` (por atribuição), `709:7872` (lista), `710:7996` (detalhe), `711:8108` (quiz), `712:8232` (escopo restrito), `713:8356`/`714:8360` (mobile) | quem respondeu e quem falta, detalhe por seção, pontuação, exportação, restrição de leitura |
| Responder (cliente) | `691:6897` (em branco), `692:7228` (preenchendo), `694:7050` (erro), `695:7115` (enviado), `696:7188` (prazo vencido), `697:7261` (desktop) | rascunho salvo sozinho, validação de obrigatórios, leitura pós-envio, fechamento por prazo |
| Responder quiz | `719:8481` (antes de iniciar), `720:8525` (respondendo), `720:8669` (tempo esgotado), `721:8645` (resultado) | cronômetro no servidor, envio automático, correção |

## Modelo de dados

Sete modelos novos no Prisma. Nomes em inglês, como o resto do schema.

### `Form`

A biblioteca é **da empresa**, não do programa — o overlay de criação diz *"ele nasce na biblioteca da
empresa e pode ser usado em quantos programas você quiser"*.

| Campo | Tipo | Origem na tela |
|---|---|---|
| `id` | uuid7 | |
| `name` | String | header do construtor, editável pelo lápis |
| `companyId` | FK Company | biblioteca é por empresa |
| `scored` | Boolean | toggle **Formulário avaliado**; vira o chip `AVALIADO` na lista |
| `timeLimitMinutes` | Int? | campo **TEMPO-LIMITE** (`20 min`), só aparece com `scored` |
| `sensitive` | Boolean | chip `SENSÍVEL` / `DADOS SENSÍVEIS` (`712:8232`) |
| `shareSlug` | String @unique | `app.nuree.com.br/f/diagnostico-rh-7k2m` |
| `active` / `deactivatedAt` | Boolean / DateTime? | soft-delete — *"dá para reverter antes da exclusão definitiva"* |
| `createdAt` / `updatedAt` | DateTime | coluna **CRIADO EM** |

Derivados, nunca colunas: `fieldCount`, `sectionCount`, `assignmentCount`, `responseCount`.

### `FormSection`

`id`, `formId`, `title`, `position`. O construtor mostra `SEÇÃO 1 · Contexto da área` e o
responder navega `SEÇÃO 2 DE 3`.

### `FormField`

`id`, `formId`, `sectionId`, `type`, `label`, `helpText?`, `required`, `position`.

```
enum FormFieldType {
  SHORT_TEXT     // texto curto
  LONG_TEXT      // texto longo
  SINGLE_CHOICE  // escolha única
  MULTI_CHOICE   // escolha múltipla
  SCALE          // escala
  NUMBER         // número
  DATE           // data
  UPLOAD         // upload
}
```

Configuração por tipo, vista no painel **CAMPO SELECIONADO**:

- `SCALE` — `scaleMin`, `scaleMax` (**INTERVALO ACEITO**, `1` até `5`) e `anchorMin`, `anchorMax`
  (**ÂNCORAS**, `nada` / `demais`).
- `SINGLE_CHOICE` / `MULTI_CHOICE` — lista de `FormFieldOption`.
- `LONG_TEXT` e `UPLOAD` ganham o chip `NÃO PONTUÁVEL` num formulário avaliado: **só campo de escolha
  entra na pontuação**.

### `FormFieldOption`

`id`, `fieldId`, `label`, `position`, `correct` (gabarito), `discontinuedAt?`.

O construtor marca uma opção com `EM USO` e avisa: *"'mais de 3 anos' já foi respondida. remover
descontinua a opção, não apaga as respostas."* Ou seja: **opção já respondida nunca é deletada** —
ganha `discontinuedAt`, some do formulário para quem responde e continua legível nas respostas antigas.

!!! warning "A mesma regra vale para campo e seção — e isso o desenho não diz"
    O desenho protege a **opção** respondida, mas o botão `Excluir campo` não tem nenhum aviso. Com
    `onDelete: Cascade`, apagar um campo respondido apagaria em cascata a resposta de quem já
    respondeu — e a aba ao lado mostra `12 RESPOSTAS` enquanto a aba Campos deixa excluir.

    `FormSection` e `FormField` ganharam `discontinuedAt` pelo mesmo motivo: **o que já foi
    respondido é descontinuado; o resto é apagado.** É a leitura consistente do princípio que a tela
    enuncia, não uma regra nova — mas é inferência minha, e merece confirmação.

### `FormAssignment`

O formulário é atribuído a um contexto, e *"cada atribuição tem prazo próprio. atribuir a um novo
contexto acrescenta — não move."*

| Campo | Tipo | Nota |
|---|---|---|
| `id` | uuid7 | |
| `formId` | FK Form | |
| `contextType` | enum `PROGRAM` · `JOURNEY_STAGE` · `ENROLLMENT` | as três opções do overlay de atribuir |
| `contextId` | String | id do programa, da etapa ou da participação |
| `dueAt` | DateTime? | **PRAZO · OPCIONAL**; sem prazo o formulário não fecha sozinho |
| `active` / `deactivatedAt` | | *"remover uma atribuição não apaga as respostas já enviadas"* |

`@@unique([formId, contextType, contextId])` — é o que produz o chip `JÁ ATRIBUÍDO` no picker e o
frame `704:7733`.

Derivados: `audienceCount` (quantos alcança) e `submittedCount` (`8 de 12 responderam`).

### `FormResponse`

`id`, `assignmentId`, `userId`, `status`, `startedAt?`, `submittedAt?`, `autoSubmitted`,
`correctCount?`, `blankCount?`, `scorePercent?`, `createdAt`, `updatedAt`.

```
enum FormResponseStatus { DRAFT  SUBMITTED }
```

**`Não iniciada` não é status** — é a ausência de linha. A tela de respostas lista a audiência inteira
da atribuição e cruza com as respostas existentes, como a situação de usuário já faz hoje.

`updatedAt` alimenta o `RASCUNHO SALVO ÀS 14:32`. `startedAt` é o disparo do cronômetro do quiz.

### `FormAnswer`

`id`, `responseId`, `fieldId`, e o valor conforme o tipo: `textValue`, `numberValue`, `dateValue`,
`scaleValue`, `optionIds String[]`, `fileKey` / `fileName` / `fileSize` / `fileMime`.

`@@unique([responseId, fieldId])`.

## Endpoints

Rotas em português, resposta em inglês, paginação por `src/pagination.ts` — a convenção que já vale
para o resto da api.

### Biblioteca · admin

| Rota | O que faz |
|---|---|
| `GET /formularios` | lista paginada. Filtros da toolbar: `busca`, `avaliado=sim\|nao\|todos`, `atribuido=programa\|etapa\|participacao\|sem\|todos`, `criadoDe`/`criadoAte`. Cada item traz `fieldCount`, `sectionCount`, `assignmentCount` e `assignmentsByContext` |
| `POST /formularios` | `{ nome, origem: 'branco' \| 'copia', formularioOrigemId? }` → **Criar e montar**. A cópia clona seções, campos e opções; nunca atribuições nem respostas |
| `GET /formularios/:formId` | o formulário |
| `PATCH /formularios/:formId` | `nome`, `avaliado`, `tempoLimiteMinutos`, `sensivel`, `active` |
| `POST /formularios/:formId/duplicar` | o ícone de copiar na linha da tabela |
| `DELETE /formularios/:formId` | soft-delete |
| `GET /formularios/:formId/compartilhamento` | `{ slug, url }` para o overlay Exportar. **O QR é desenhado no front** a partir da URL |

### Construtor

| Rota | O que faz |
|---|---|
| `GET /formularios/:formId/estrutura` | seções, campos e opções vivos. Cada campo e cada opção trazem `inUse` — é o que acende o `EM USO` |
| `PUT /formularios/:formId/estrutura` | a árvore inteira, reconciliada por id. **A ordem do array é a posição** |
| `GET /formularios/:formId/pre-visualizacao` | o que o participante veria, sem gabarito e sem gravar nada |

### Atribuições

| Rota | O que faz |
|---|---|
| `GET /formularios/:formId/atribuicoes` | paginada. `contextLabel`, `dueAt`, `audienceCount`, `submittedCount` |
| `POST /formularios/:formId/atribuicoes` | `{ tipo, contextoId, prazo? }`. **409** quando já existe; reatribuir um contexto removido reaproveita a mesma linha, com as respostas dela |
| `PATCH /formularios/:formId/atribuicoes/:assignmentId` | só o `prazo` |
| `DELETE /formularios/:formId/atribuicoes/:assignmentId` | soft; respostas ficam |

O picker do overlay reusa o que já existe: `GET /programas` (traz `enrollmentCount` e `endsAt`, que é
exatamente o `12 participantes · até 30 ago 2027` do desenho) e `GET /programas/:id/participacoes`.
**Etapa de jornada não tem de onde vir** — divergência 1.

### Respostas · admin

| Rota | O que faz |
|---|---|
| `GET /formularios/:formId/respostas/resumo` | os cards por atribuição |
| `GET /formularios/:formId/atribuicoes/:assignmentId/respostas` | paginada, com `busca` por nome ou e-mail. Rodapé em `totals`: `people`, `submitted`, `averageScore` |
| `GET /formularios/:formId/atribuicoes/:assignmentId/respostas.csv` | **Baixar respostas** |
| `GET /formularios/:formId/respostas/:responseId` | detalhe por seção; avaliado traz `questions`, a correção pergunta a pergunta |

Formulário com `sensitive` devolve as linhas e os totais e **omite o conteúdo** (`answeredCount: null`)
para quem não tem escopo no programa — a tela `712:8232` mostra `PROGRESSO` em `—`.

### Responder · cliente

| Rota | O que faz |
|---|---|
| `GET /meus-formularios` | paginada; o que me foi atribuído. `status: null` é "não iniciada" |
| `GET /formularios/:formId/atribuicoes/:assignmentId/minha-resposta` | rascunho, prazo, progresso e `secondsLeft` |
| `GET …/minha-resposta/estrutura` | os campos que devo responder, **sem gabarito** |
| `PATCH …/minha-resposta` | **autosave parcial**: manda-se o que mudou, campo ausente fica como está |
| `POST …/minha-resposta/envio` | valida obrigatórios. Faltando, **422** com `fields` nomeando cada um |
| `POST …/minha-resposta/inicio` | quiz: grava `startedAt`. **Uma tentativa** — segunda chamada é 409 |
| `GET …/minha-resposta/resultado` | o resultado do quiz |

Regras que o back decide, não a tela:

- **Enviado é imutável.** `PATCH` numa resposta `SUBMITTED` é 409.
- **Prazo vencido fecha.** Passado o `dueAt`, `PATCH` e envio são 409; o rascunho fica legível.
  Sem `dueAt`, nunca fecha.
- **Tempo esgotado envia sozinho**, com `autoSubmitted: true`, e o resto conta como em branco. O
  fechamento acontece na leitura, não numa fila: quem volta à tela depois do tempo não pode depender
  de um worker ter rodado.
- **Pontuação é calculada no envio**, só sobre campos de escolha com gabarito, e pelos campos **como
  eram** — descontinuados inclusive, senão refazer a conta mudaria a nota de quem já respondeu.
- **Fora da audiência é 404, não 403** — quem não está nela não deveria nem saber que a atribuição
  existe.

### Link público e QR

`app.nuree.com.br/f/<slug>` é **atalho, não porta aberta**. O slug é o nome reduzido mais quatro
caracteres sorteados, de um alfabeto sem `i`, `l`, `o`, `0` e `1` — ele é lido em voz alta e digitado
à mão de um QR impresso.

!!! note "A rota `/f/:slug` ainda não existe"
    `FormService.findBySlug` está implementado e o overlay Exportar já recebe a URL pronta. Falta
    decidir para onde o slug resolve quando o formulário tem **mais de uma** atribuição — o desenho
    mostra o overlay com um programa só no cabeçalho, e não diz o que acontece nos outros casos.

### Evento de envio

[RF-E4.5](../requisitos.md#rf-e4-5) pede evento no envio. O `BullMQ` já está de pé (`infra/email`,
`task/jobs`); a fila de eventos de formulário entra do mesmo jeito.

## Storage {#storage}

O `Media` já estava previsto na arquitetura e não existia em código. Foi implementado junto:
**MinIO no `docker-compose`**, `src/infra/media/` — infra, não módulo de domínio, ao lado de `email`
e `prisma`.

**A api nunca carrega o corpo do arquivo** ([FF-4](../arquitetura/fitness-functions.md)). Nem na
subida — o navegador faz `PUT` direto no bucket — nem na descida. Três tempos: pedir, subir,
confirmar.

| Rota | O que faz |
|---|---|
| `POST /formularios/:formId/atribuicoes/:assignmentId/minha-resposta/anexos` | `{ campoId, tipo }` → URL de `PUT`, chave e `Content-Type` a mandar |
| `PATCH …/minha-resposta` | a confirmação: a chave vai no campo `arquivo`, como qualquer outro valor |
| `GET …/minha-resposta/anexos/:fieldId` | link assinado para o próprio anexo — funciona depois do envio e do prazo |
| `GET /formularios/:formId/respostas/:responseId/anexos/:fieldId` | link assinado, para o admin. Num formulário sensível, 404 para quem não tem escopo: **anexo é conteúdo** |

Decisões que valem para os dois consumidores:

- **A chave é do servidor, nunca do cliente.** Escolher o caminho seria escolher em que resposta — ou
  em que empresa — escrever. Na volta, a chave é conferida contra o prefixo do dono.
- **O objeto é conferido antes de virar resposta.** Entre pedir o ticket e mandar a chave nada
  garante que o `PUT` aconteceu; sem a conferida daria para gravar uma referência para o vazio.
- **Tamanho e tipo saem do objeto, não do corpo.** Quem monta a requisição escolhe o que declarar.
- **Trocar o arquivo apaga o anterior.** Não se guardam os dois.

!!! warning "O teto de tamanho é conferido na volta, não na assinatura"
    Assinar `ContentLength` fixaria o upload num número **exato** de bytes, não num teto — a
    assinatura casa com o valor, não com o intervalo. Presigned POST com `content-length-range`
    prenderia de verdade, ao custo de o cliente montar um formulário multipart em vez de um `PUT`.

    Enquanto esse custo não for pago, o arquivo grande demais **chega** ao bucket e é recusado e
    apagado na confirmação. O que isso aceita: ocupar o bucket por alguns segundos. O que isso não
    aceita: virar resposta ou logo de alguém.

    O `Content-Type`, esse sim, é assinado — mandar outro dá 403 no bucket.

## Divergências {#divergencias}

Pela regra de ouro do handoff, estas **não se resolvem codando**. As quatro primeiras ficaram de
fora da implementação; as demais foram implementadas sob a suposição anotada em cada uma — todas
reversíveis sem refazer o módulo.

1. **Etapa de jornada não existe.** O overlay de atribuir oferece os três tipos de contexto, mas
   [E7](../requisitos.md#e7--montar-e-exibir-a-jornada-do-participante) não foi implementado — não há
   `JourneyStage` no schema nem rota que liste etapas. Ou E7 entra antes, ou a opção sai da tela, ou o
   `contextType` nasce com o enum completo e a opção fica desabilitada. **Não vou inventar a entidade.**

2. ~~**Upload não tem onde guardar.**~~ **Resolvida.** O `Media` foi implementado
   (`src/infra/media/`), com MinIO no compose, e o campo `upload` funciona ponta a ponta. Ver
   [Storage](#storage) abaixo.

3. **`SENSÍVEL` não está nos requisitos.** A tela `712:8232` restringe a leitura do conteúdo a *"quem
   tem escopo no programa"*, mas E4 não descreve essa regra e o `RolesGuard` atual só conhece
   `ADMIN` e `CLIENT`. Falta dizer: é papel, é vínculo com o programa, ou é uma terceira coisa?

4. **Seções não estão nos requisitos.** [RF-E4.1](../requisitos.md#rf-e4-1) e
   [RF-E4.2](../requisitos.md#rf-e4-2) falam de campos com rótulo, obrigatoriedade, opções e ordem —
   nunca de agrupar em seções. O Figma traz seção como estrutura de primeira classe (`3 SEÇÕES`,
   `SEÇÃO 2 DE 3`, progresso por seção no responder desktop). Confirmar que entra no escopo, e
   atualizar o requisito.
   <br>**Implementado como a tela pede:** `FormSection` é entidade, e todo campo pertence a uma.
   A tela é o Figma, não o requisito desatualizado.

5. **`UMA TENTATIVA` e o cronômetro.** [RF-E4.6](../requisitos.md#rf-e4-6) prevê tempo-limite
   opcional, mas nada sobre tentativa única nem sobre envio automático ao esgotar. As telas
   `719:8481` e `720:8669` tratam os dois como regra. Confirmar.
   <br>**Implementado como as telas pedem:** segundo `POST …/inicio` é 409, e o tempo esgotado
   fecha a resposta com `autoSubmitted: true`.

6. **O que é `RASCUNHO` no header do construtor.** A linha meta muda por aba: `7 CAMPOS · 3 SEÇÕES ·
   RASCUNHO` em Campos, `· 4 ATRIBUIÇÕES` em Atribuições, `· 12 RESPOSTAS` em Respostas. Não há botão
   de publicar em lugar nenhum. `RASCUNHO` é estado do formulário (e aí falta a ação de publicar), ou
   é só "tem alteração não salva"? Muda se um formulário em rascunho pode ou não ser atribuído.
   <br>**Assumido "alteração não salva":** não há coluna de status nem ação de publicar, e qualquer
   formulário pode ser atribuído. Se for estado, entra uma coluna e uma rota — e aí atribuir um
   rascunho passa a ser 409.

7. **O filtro `Período: sempre`** — período de quê? Criação do formulário, ou prazo das atribuições?
   <br>**Assumido criação:** `criadoDe`/`criadoAte`, que é a coluna `CRIADO EM` ao lado. Se for
   prazo, muda só o `where`.

8. **`INTERVALO ACEITO` no campo número.** O painel mostra o par min/máx para `escala`. O tipo
   `número` aceita intervalo também, ou é validação livre? O frame de número selecionado não foi
   desenhado.
   <br>**Implementado só em `escala`:** `número` aceita qualquer valor. Estender é acrescentar duas
   colunas, não refazer nada.

## O que foi construído

`api/src/modules/form`, espelhando a estrutura do módulo `task`: uma subpasta por agregado, com
controller, service, DTO e spec. `context.ts` e `scoring.ts` ficam na raiz porque atravessam mais de
um — três telas perguntam "que contexto é esse", e duas corrigem o mesmo quiz.

| Entregue | Onde |
|---|---|
| 7 modelos + 3 enums, migration aplicada | `prisma/schema.prisma`, `prisma/migrations/20260914190000_form_motor_de_formularios` |
| Biblioteca: criar, copiar, listar com os 3 filtros, duplicar, soft-delete, link curto | `form/` |
| Construtor: leitura, `PUT` da estrutura com reconciliação, pré-visualização | `structure/` |
| Atribuições: `PROGRAM` e `ENROLLMENT`, prazo próprio, 409 de duplicata | `assignment/` |
| Responder: rascunho parcial, validação, prazo, imutabilidade, quiz cronometrado | `answering/` |
| Leitura do admin: resumo, lista paginada, detalhe, correção, CSV | `response/` |
| 44 testes novos — 12 unitários, 32 de integração | `*.spec.ts` |

Duas coisas fora do módulo:

- **`AccountFacade.findActiveEnrollmentById`** — fronteira nova entre módulos, e a fachada avisa que
  "método novo aqui é fronteira nova". `Form` guarda só o `contextId`; quem traduz isso em pessoa e
  programa é o dono da tabela (FF-2).
- **`fitness-functions.spec.ts`** — `formId`, `assignmentId` e `responseId` entraram na lista de
  parâmetros de caminho permitidos, com a justificativa de como cada um se prende ao escopo. A regra
  existe para forçar essa revisão, e o comentário registra o que foi revisto.

!!! note "Duas violações que as fitness functions pegaram"
    As duas eram bugs reais, não falso-positivo do teste:

    1. **Três rotas devolviam coleção crua.** `GET /formularios/:id/atribuicoes`,
       `/respostas/resumo` e `/meus-formularios` retornavam array. Foram paginadas — a biblioteca já
       mostra `8 programas` numa linha, e o próprio handoff proíbe o argumento "são poucos".
    2. **Parâmetros de caminho fora da lista revisada.** Resolvido acima.

    Uma terceira só apareceu no teste de integração: `coerce` devolvia o formato da tela (`text`,
    `scale`) e o `upsert` esperava as colunas do Prisma (`textValue`, `scaleValue`). Virou uma
    tradução única em `toColumns`.

## O que falta

1. **`sensitive`** — o mecanismo está implementado e testado; o que falta é a **regra** de quem
   passa por ele (divergência 3). Hoje vale a leitura literal da tela: ter escopo no programa.
3. **`JOURNEY_STAGE`** — no enum, recusado na escrita, até E7 existir.
3. **`GET /f/:slug`** — falta decidir para onde resolve com mais de uma atribuição.
4. **Evento de envio** ([RF-E4.5](../requisitos.md#rf-e4-5)) — o `BullMQ` já está de pé em
   `infra/email` e `task/jobs`; a fila de eventos de formulário entra do mesmo jeito.

## Relacionados

- [Requisitos · E4](../requisitos.md#e4--criar-formulários-e-coletar-respostas)
- [Handoff de telas — Iteração 1](../iteration-1/handoff-telas.md)
- [Componentes](../arquitetura/componentes.md)
