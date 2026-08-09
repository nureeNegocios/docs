---
hide:
  - navigation
---

# Handoff de telas — Iteração 1

Mapa das telas desenhadas no Figma **`003 - NUREE`** para o front a ser construído do zero: rota sugerida, o que é, estados e rastreio ao requisito. Marca e tokens em [Diretrizes de Design](../design.md); comportamento em [Requisitos](../requisitos.md); módulos em [Componentes](../arquitetura/componentes.md). Este documento é dono só do vínculo **desenho → implementação**.

Arquivo Figma: `https://www.figma.com/design/mnfgdTOT1I22KW34oUogEI/003---NUREE`. Cada frame abre com `?node-id=<id>` (ex.: `…?node-id=328-2490`).

## Implementação · Iteração 1

Estas telas cobrem a **Iteração 1** do roadmap, entregue como dois épicos no repositório `app`:

- **[#3 · E1 — Login, papéis e acesso por empresa/programa](https://github.com/nureeNegocios/app/issues/3)** — telas de **Auth** + **Home**.
- **[#4 · E2 — Administrar empresas, usuários e programas](https://github.com/nureeNegocios/app/issues/4)** — **console admin** + **cadastro por link**.

Os épicos englobam **backend e frontend**. Este handoff é a especificação do **frontend**: telas **pixel-perfect** sobre o design system, prontas para implementar direto a partir dele.

!!! danger "Regra de ouro: a tela é o Figma, não a sua interpretação dele"
    **Cada tela implementada tem que ficar idêntica ao frame.** Não é "inspirada em", não é "equivalente": é a mesma tela. Abra o node, meça, e reproduza.

    **Não invente nada.** Nada de campo a mais, coluna a mais, botão a mais, estado vazio improvisado, ícone trocado, cor "parecida", espaçamento "que ficou melhor", texto reescrito ou ordem de campos alterada. Se está no frame, existe; se não está, não existe.

    Isso vale inclusive quando o desenho parecer errado ou incompleto. **Divergência não se resolve codando** — se algo não fecha (o frame pede um dado que a API não tem, um estado não foi desenhado, dois frames se contradizem), **pare e pergunte**. Um palpite implementado vira retrabalho nas duas pontas: refazer o código e refazer o Figma.

    Toda copy sai do frame, letra por letra. Toda medida sai da escala Fibonacci abaixo. Todo ícone é o Lucide indicado. O que não estiver desenhado — hover, foco, loading, erro, lista vazia — **pergunte antes**, não preencha por conta.

!!! warning "Os critérios de #3/#4 ainda citam senha"
    Os checklists das issues nasceram antes da decisão de **auth passwordless por código (OTP)**. Onde lê "e-mail e senha", "reset de senha" ou "troca de senha no primeiro acesso", vale o modelo atual dos [Requisitos](../requisitos.md#rf-e1-4): código OTP + confirmação de e-mail.

## Fundações

Fonte única da marca: [Diretrizes de Design](../design.md). Resumo operável (valores extraídos dos frames de auth):

| Token | Valor | Uso |
|---|---|---|
| navy | `#0D1422` | texto, superfícies escuras, botão primário, ícones |
| flor (magenta) | `#C23A7A` | acento, links, estado ativo, "o ponto", destrutivo |
| verde | `#14574E` | sucesso, acento intencional |
| creme / paper | `#F6F4EC` | superfícies suaves |
| white | `#FFFFFF` | fundo |
| ink | navy @ 0.40–0.85 | texto secundário (opacidade sobre navy) |
| hairline | navy @ 0.06–0.16 | bordas de input, divisores |
| grid | navy @ **0.08** | overlay Fibonacci (padronizado; ver Home) |

- **Tipografia:** Montserrat (display/corpo: título 40 Medium, corpo 16 Regular, botão 15 SemiBold, link 13 SemiBold). **Courier Prime** só em labels/metadados/números/IDs, CAIXA ALTA, `letter-spacing` ~8% (ex.: label 12px `tracking 0.96`).
- **Raio:** sm `6`, md `10`. Sem pills, sem elipses (exceto dots de status 8px e o ponto do wordmark).
- **Espaçamento:** escala Fibonacci `8·13·21·34·55·89·144·233`. Gap do form `20`; largura do form `400` (desktop) / `326` (mobile).
- **Ícones:** Lucide, **SVG inline**, stroke 1.6–1.8, cap/join round. Nunca emojis.
- **Wordmark:** sempre o vetor `logo/`, nunca texto.

!!! important "Botões — regra dos claros"
    - **Primário:** fundo navy sólido, texto branco.
    - **Secundário (claro):** fundo **branco** + borda **navy @ 0.16, peso 1**. É o **único** estilo de borda para botão claro (o do "Novo usuário").
    - **Nunca** borda cheia (navy @ 1.0) nem peso 1.5 em botão claro — era o desvio corrigido no master `button/tipo=secondary`.
    - Fluxos em etapas trazem um indicador **`PASSO x DE y` + barra de progresso** no topo do card (carômetro; criar programa → matricular).

## Convenções de implementação

- **Stack** (ver [Visão geral](../index.md)): Next.js consumindo a API REST (NestJS). Sem lógica de domínio no front.
- **Idioma:** rotas, domínio e copy em PT ([RNF-5](../requisitos.md)). Domínio em PT: `Empresa`, `Usuario`, `Programa`, `Ciclo`, `entrar`, `sair`.
- **Copy:** acolhedora, minúscula, metáforas leves de florescimento; sem travessão; sem frases-aplauso.
- **Responsivo:** cada tela abaixo tem par desktop + mobile — **é uma tela só, dois breakpoints**, não telas distintas.

## API · como ligar as telas

A api está implementada e é a fonte do contrato. **O Swagger é a referência viva**, não este documento: `http://localhost:3000/documentacao` (só fora de produção). Se algo aqui divergir do Swagger, o Swagger vence.

Nada de lógica de domínio no front: o back já decide papel, escopo, unicidade e situação. O front pede e desenha.

### Sessão

A sessão vive em **dois cookies `httpOnly`** e **nunca no corpo da resposta** — não há token para guardar em `localStorage`, e JavaScript não consegue lê-los, o que é de propósito.

- Toda chamada vai com **`credentials: 'include'`**. Sem isso o login "funciona" e a próxima chamada volta 401.
- A origem do front precisa estar em `CORS_ORIGINS` no `.env` da api.
- `401` numa chamada qualquer significa access token expirado: chame `POST /auth/atualizar` e repita a original. Se o refresh também falhar, mande para `/entrar`.
- `403` é escopo ou papel — **não** tente refresh, e não esconda: é bug de navegação se acontecer numa tela que o usuário deveria alcançar.

| Rota | Uso |
|---|---|
| `POST /auth/codigo` | pede o código. **Responde 204 exista a conta ou não**, e leva o mesmo tempo nos dois casos: a tela segue para o passo do código sem checar nada. Reenvio dentro de 60s não gera código novo |
| `POST /auth/entrar` | `{ email, codigo }` → abre a sessão. Acertar o código também confirma o e-mail |
| `POST /auth/google` | `{ credencial }` — o ID token que o SDK do Google entrega ao navegador |
| `POST /auth/atualizar` | rotaciona a sessão |
| `POST /auth/sair` | encerra |
| `GET /auth/eu` | usuário da sessão |

### Listagens

**Toda listagem pagina, busca e filtra no servidor.** Não traga a coleção inteira para filtrar em memória — nem por conveniência, nem "porque são poucos".

Resposta: `{ items, total, page, pageSize }`. Query em português: `?pagina=2&tamanho=20&busca=ana`, mais os filtros de cada rota. O `total` é o que alimenta o paginador numerado.

| Rota | Filtros | Alimenta |
|---|---|---|
| `GET /empresas` | `busca`, `status=ativas\|inativas\|todas` | aba **Empresas**. Cada item traz `programCount` e `userCount` |
| `GET /usuarios` | `busca` (nome ou e-mail), `papel`, `ativo` | aba **Usuários** — atravessa empresas, e cada item traz `companyName` |
| `GET /programas` | `busca`, `produto`, `ativo` | aba **Programas** — cada item traz `companyName` e `enrollmentCount` |
| `GET /programas/:id/participacoes` | `busca` | grade de participantes; cada item traz `userName` e `userEmail` |
| `GET /empresas/:id/usuarios` · `/programas` | os mesmos | as mesmas listas, presas a uma empresa |

!!! important "Situação é derivada, não é campo"
    Não existe coluna `status` no banco, e a API não devolve uma. A tela calcula:

    - **Usuário:** `active` + `emailConfirmedAt` → *Ativo*; `active` sem `emailConfirmedAt` → *Convite pendente*; `active: false` → *Inativo*.
    - **Empresa:** `active` → *Ativa* / *Inativa*.
    - **Programa:** `active` com `endsAt` no passado → *Encerrado*.

### Escrever

Ler atravessa empresas; **escrever é sempre pela empresa**, porque criar usuário ou programa exige dizer de qual empresa ele é.

| Ação | Rota |
|---|---|
| Nova empresa · editar · desativar | `POST /empresas` · `PATCH` · `DELETE /empresas/:id` |
| Novo usuário (dispara convite) | `POST /empresas/:id/usuarios` — `sendInvite: false` cadastra sem avisar |
| Editar papel · desativar | `PATCH` · `DELETE /empresas/:id/usuarios/:userId` |
| Reenviar acesso | `POST /empresas/:id/usuarios/:userId/reenviar-acesso` → 202 |
| Novo programa (passo 1) | `POST /empresas/:id/programas` — **o nome não vai no corpo**, é gerado |
| Matricular (passo 2) | `POST /programas/:id/participacoes` |
| Link de cadastro | `POST · GET · DELETE /empresas/:id/link-cadastro` — devolve a URL pronta; **a api não envia o link**, o admin copia e distribui |
| Carômetro | `GET · PATCH /perfil` (o próprio) · `GET · PATCH /empresas/:id/usuarios/:userId/perfil` (pelo admin) |
| Contexto ativo | `GET /contextos` · `GET · PUT · DELETE /contextos/ativo` |

- **Desativar é soft** ([RNF-9](../requisitos.md)): a linha some da lista e o cadastro continua. A tela de detalhe ainda abre, para reativar com `PATCH { active: true }`.
- **Carômetro grava parcial:** manda-se o passo, não o formulário inteiro. Campo ausente fica como está; campo enviado como `null` apaga. `completedAt` nulo é o sinal de que ainda falta responder.
- **Cadastro por link** é público: `GET /cadastro/:token` diz de qual empresa é, `POST /cadastro/:token` cadastra. **Responde 204 tenha a pessoa conta ou não** — não mostre "e-mail já cadastrado", isso é anti-enumeração e não é negociável. Depois, siga para `/auth/codigo`.

!!! warning "Errar o e-mail no autocadastro se corrige refazendo o cadastro"
    O botão "E-mail errado? Corrigir" **volta ao formulário de cadastro**, não pede outro código. `/auth/codigo` com e-mail sem conta responde 204 e não envia nada — a pessoa esperaria para sempre.

### O que ainda não existe na api

Estas telas **não têm backend** e não devem ser ligadas a nada inventado — se entrarem na iteração, entram com dado estático e combinado, nunca com endpoint imaginado:

- **Painéis** de acompanhamento por programa e por pessoa ([E2.6](../requisitos.md#rf-e2-6)).
- **Biblioteca de mídia** e hub de construtores ([E2.4](../requisitos.md#rf-e2-4)).
- **Clonar/reaplicar template** a outro programa ([E2.7](../requisitos.md#rf-e2-7)).
- **Ordenação por clique no cabeçalho** da tabela ([app#28](https://github.com/nureeNegocios/app/issues/28)) — a paginação e os filtros existem; ordenar escolhendo a coluna, ainda não.

## Auth · `nuree / auth`

Fluxo **passwordless por código** ([RF-E1.4](../requisitos.md#rf-e1-4), [E1.5](../requisitos.md#rf-e1-5), [E1.8–1.9](../requisitos.md#rf-e1-8)): e-mail → código OTP no e-mail → sessão. Google OAuth é complementar. Layout split: painel branco (form, largura 400/326) + painel navy com a arte do jardim (só desktop; mobile usa trilhos + borboletas).

| Tela | node-id (desktop / mobile) | Rota sugerida | Conteúdo | Estados |
|---|---|---|---|---|
| Passo 1 · login | `328-2490` / `328-2585` | `/entrar` | e-mail + `Entrar` + divisor + `Continuar com Google` + `Criar conta` | erro (e-mail inválido), loading do botão |
| Passo 2 · código | `332-65` / `332-152` | `/entrar/codigo` | subtítulo com o e-mail digitado + 6 caixas OTP (Courier Prime) + `Reenviar` + `Confirmar` + `Usar outro e-mail` | preenchendo (caixa ativa magenta+cursor), **código inválido**, **expirado**, loading, reenvio com contador — *estes 4 ainda a desenhar* |

- OAuth Google: o callback é responsabilidade da API; o front só inicia o fluxo e recebe a sessão.
- "Esqueci minha senha" **não existe** neste modelo — o passo 1 não tem campo de senha.

## Home · `nuree / home`

Empty state autenticado: **header + grid Fibonacci** ao fundo (grid padronizado para navy @ **0.08** em todos os 6 frames — antes estava em 0.55). Duas audiências pelo papel ([RF-E1.4a](../requisitos.md#rf-e1-4a)).

| Tela | node-id (desktop / mobile) | Rota sugerida | Observação |
|---|---|---|---|
| Home cliente | `335-2896` / `328-2415` | `/` | header: Início · Tarefas · Calendário + avatar |
| Home admin | `267-3` / `269-10` | `/admin` (landing) | header admin |
| Trocar empresa (admin) | `307-95` / `307-264` | overlay do header / sheet no mobile | seletor de contexto ativo ([RF-E2.5](../requisitos.md#rf-e2-5)) |

## Admin · `nuree / admin`

Console de tenancy ([E2](../requisitos.md)), organizado no Figma em 4 seções na ordem de fluxo. Desktop = tela cheia com modal; mobile = tela + bottom-sheet. Modais/sheets são overlays das telas de lista (usar rotas interceptadas do App Router).

**01 · Desktop — telas (conta)**

| Tela | node-id | Rota sugerida | RF |
|---|---|---|---|
| conta · empresas | `297-1679` | `/admin/empresas` | [E2.1](../requisitos.md#rf-e2-1) |
| conta · usuários | `297-1971` | `/admin/usuarios` | [E2.2](../requisitos.md#rf-e2-2) |
| conta · programas | `294-2111` | `/admin/programas` | [E2.3](../requisitos.md#rf-e2-3) |

**02 · Desktop — modais** (overlay da tela de origem)

| Modal | node-id | Origem | RF |
|---|---|---|---|
| Nova empresa | `298-878` | empresas | [E2.1](../requisitos.md#rf-e2-1) |
| Desativar empresa | `298-1383` | empresas (soft-delete, [RNF-9](../requisitos.md)) | [E2.1](../requisitos.md#rf-e2-1) |
| Novo usuário | `298-1025` | usuários (dispara convite, [E2.8](../requisitos.md#rf-e2-8)); campos nome, **telefone**, e-mail, papel, empresa | [E2.2](../requisitos.md#rf-e2-2), [E1.12](../requisitos.md#rf-e1-12) |
| Link de cadastro | `347-3325` | usuários → botão **"Link de cadastro"** ao lado de "Novo usuário"; escolhe empresa e gera o link aberto | [E2.9](../requisitos.md#rf-e2-9), [E2.10](../requisitos.md#rf-e2-10) |
| Novo programa | `296-1182` | programas · **passo 1 de 2** (indicador de etapa no topo) | [E2.3](../requisitos.md#rf-e2-3) |
| Matricular participantes | `296-1331` | **passo 2 de 2** do fluxo acima (`Continuar` → matrícula → `Criar programa`) → gera Participação | [E1.3](../requisitos.md#rf-e1-3), [E2.3](../requisitos.md#rf-e2-3) |
| Carômetro | `298-1202` | programas → grade de participantes (os usuários da empresa, para escolher) | [E2.6](../requisitos.md#rf-e2-6) |

> **Entrada em usuários (desktop e mobile):** **"Link de cadastro"** é o botão **primário** (escuro, à direita) e **"Novo usuário"** o **secundário** (claro, com "+").

**03 · Mobile — telas**

| Tela | node-id | Corresponde a |
|---|---|---|
| console · menu (drawer) | `299-1807` | navegação admin |
| conta · empresas | `299-1677` | `/admin/empresas` |
| conta · usuários | `300-1792` | `/admin/usuarios` |
| conta · programas | `300-1919` | `/admin/programas` |

**04 · Mobile — sheets** (equivalente mobile dos modais)

| Sheet | node-id | Corresponde a |
|---|---|---|
| Nova empresa | `301-1832` | modal Nova empresa |
| Desativar empresa | `302-2187` | modal Desativar empresa |
| Novo usuário | `301-1970` | modal Novo usuário (com **telefone**) |
| Link de cadastro | `366-2978` | modal Link de cadastro |
| pessoa (ações) | `303-1995` | ações de linha de um usuário (editar papel, reenviar acesso, desativar) |
| Novo programa | `302-1921` | modal Novo programa |
| Matricular | `302-2061` | passo 1 de matricular (escolher programa/pacote) |
| matricular (pessoas) | `303-2137` | passo 2 de matricular (selecionar pessoas) |
| Carômetro | `301-2111` | modal Carômetro |

> **Variações:** não há duplicatas a descartar. Cada linha acima é a versão **canônica**; desktop e mobile são o mesmo requisito em dois breakpoints, e `Matricular` + `matricular (pessoas)` são dois passos do mesmo fluxo, não repetições.

## Cadastro por link · `nuree / onboarding`

Fluxo de autocadastro aberto ([RF-E2.9–2.11](../requisitos.md#rf-e2-9), [E1.12](../requisitos.md#rf-e1-12)): **admin gera o link de uma empresa → envia aos futuros usuários → qualquer um se cadastra já vinculado à empresa (não editável) → confirma o e-mail (código) → no primeiro acesso preenche o carômetro, passo a passo.** Cadastro e telefone são obrigatórios; a confirmação aqui é só de e-mail (telefone fica para depois, [E1.13](../requisitos.md#rf-e1-13)).

| Tela | node-id (desktop / mobile) | Rota sugerida | Conteúdo | RF |
|---|---|---|---|---|
| Cadastro | `352-66` / `352-153` | `/cadastro/[token]` | empresa **bloqueada** (chip "via link") + nome + e-mail + telefone + `Criar conta` | [E2.10](../requisitos.md#rf-e2-10) |
| Confirmar e-mail | `352-213` / `352-302` | `/cadastro/[token]/confirmar` | mesmo padrão do OTP + subtítulo com o e-mail + footer **"E-mail errado? Corrigir"** | [E1.12](../requisitos.md#rf-e1-12) |
| Carômetro (1º acesso) | `355-65` / `355-214` | overlay na home / bottom-sheet | passo a passo (`PASSO x DE y` + barra de progresso). **Sem foto; nome já vem do cadastro** | [E2.11](../requisitos.md#rf-e2-11) |

O carômetro é sobre convivência, não sobre cargo. São estes os campos, e só eles:

| Campo | Tipo | Observação |
|---|---|---|
| Data de aniversário | data | |
| Tamanho de camisa | `PP` · `P` · `M` · `G` · `GG` · `XGG` | lista fechada |
| Comida favorita | texto | |
| Tem filhos | sim/não | |
| Quantos filhos | número | só aparece com "tem filhos" = sim |
| O que você curte | texto livre | a pergunta mais aberta; sem categorias |

- **Preenchimento parcial:** cada passo salva o que tem, e sair no meio não perde o anterior. A api
  expõe isso em `PATCH /perfil` — campo ausente fica como está, campo enviado como `null` apaga.
- **`completedAt`** só é preenchido quando não falta nenhuma resposta, e é por ele que a tela decide
  se ainda conduz o preenchimento. Editar depois não move a data.
- **Quem edita:** a própria pessoa em `/perfil` e o admin em
  `/empresas/:id/usuarios/:userId/perfil`. Serve ao modal de hoje e à página de perfil que vier
  depois — a rota não sabe de tela.
- Mobile: cadastro reusa o layout split (trilhos + borboletas); carômetro é bottom-sheet com grabber.

## E-mails · `nuree / emails`

| Template | node-id | Uso |
|---|---|---|
| Código OTP | `362-66` | e-mail transacional com o wordmark, o código em Courier Prime e validade de 10 min ([E1.4/1.5](../requisitos.md#rf-e1-4)). Base para os demais transacionais (convite, link de cadastro) |

## Decisões e pendências

- **Auth passwordless por código:** requisitos já atualizados ([RF-E1.4/1.5/1.8](../requisitos.md#rf-e1-4), [E2.2](../requisitos.md#rf-e2-2)); não há mais senha nem "troca no 1º acesso". Reexportar as imagens de [Diagramas](../arquitetura/diagramas.md) a partir dos `.drawio` (rótulo de Auth já corrigido para "código OTP").
- **Estados de auth a desenhar:** código inválido, código expirado, loading e reenvio com contador (ver tabela de Auth). Valem também para a tela de confirmar e-mail.
- **Carômetro:** os campos estão definidos (ver tabela em *Cadastro por link*) e implementados na
  api; falta desenhar os passos além do primeiro e decidir como os campos se distribuem entre eles.
- **Ajuste pendente:** o placeholder do campo telefone no *sheet* mobile de Novo usuário ainda mostra e-mail (é sublayer do componente `input`; ajustar no master).
- **Rotas** acima são sugestão; confirmar antes de fixar a árvore de pastas.
- **Ids do Figma** podem mudar com a edição do arquivo; na dúvida, localize pelo nome do frame.

## Relacionados

- [Diretrizes de Design](../design.md) · [Requisitos](../requisitos.md) · [Ações do sistema](../acoes.md) · [Componentes](../arquitetura/componentes.md) · [Diagramas](../arquitetura/diagramas.md)
