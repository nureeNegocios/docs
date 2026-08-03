---
hide:
  - navigation
---

# Componentes — app-nuree

Resultado do *component identification* (Ford/Richards), consolidado em **módulos de um único
quantum** — monólito **particionado por domínio** (decisão de estilo em
[exploração de arquitetura](../../reunioes/exploracao-arquitetura-2026-08-02.md)). Cada módulo é
**dono das suas tabelas** (isolamento lógico num Postgres único). Nomes canônicos em **inglês**
(código); rotas e UI em português ([RNF-5](../requisitos.md#rnf-5)).

A coluna **Engloba** registra o que foi absorvido no colapso de *entity traps*.

---

## Módulos do núcleo

| Módulo | O que é · engloba | Ações | RF |
|---|---|---|---|
| **`Auth`** | Autenticação / identidade | autenticar e-mail+senha e manter sessão; OAuth Google + vincular identidade; troca de senha no 1º acesso; logout/expiração; classificar papel (Admin/Cliente) | [E1.4](../requisitos.md#rf-e1-4), 1.5, 1.8–1.11, 1.4a |
| **`Account`** | Cadastro / tenancy · *Empresa·Usuário·Programa/Participação·Produto* | catálogo de Produtos; criar Programas (contratos isolados) e matricular com pacote; CRUD empresas/usuários (papel, reset); alternar contexto; convite | [E1.1](../requisitos.md#rf-e1-1)–1.3, E2.1–2.3, E2.5, E2.8 |
| **`Form`** | Motor genérico de formulários | CRUD formulários com campos variados; configurar campo (rótulo/obrigatório/opções/ordem); atribuir a contexto com prazo; responder com rascunho+envio; registrar resposta e emitir evento | [E4.1](../requisitos.md#rf-e4-1)–4.5 |
| **`Diagnostic`** | Diagnóstico de maturidade | montar por dimensão em escala 1–5 (*reusa `Form`*); calcular maturidade por dimensão/geral e exibir; gerar plano de ação das dimensões fracas → cria `Task` | [E10.1](../requisitos.md#rf-e10-1)–10.3 |
| **`Journey`** | Jornada · *encontros · presença (`Attendance`)* | montar/editar/excluir jornadas com etapas (marco/encontro/formatura, data/offset); anexar `Form`/`Track`/selo a etapa; exibir jornada e progresso; registrar conclusão e emitir evento; **presença em evento por QR** e relatório | [E7.1](../requisitos.md#rf-e7-1)–7.4, [E11.1](../requisitos.md#rf-e11-1)–11.2 |
| **`Track`** | Trilhas · *itens · quiz · progresso* | CRUD trilhas com itens ordenados (texto/vídeo/leitura/quiz); configurar quiz (gabarito/pontuação/tempo) *sobre o motor*; consumir e marcar progresso; não bloquear por não-conclusão | [E6.1](../requisitos.md#rf-e6-1)–6.4, E4.6 |
| **`Recognition`** | Selos & Certificados · *critérios · concessão* | definir/editar/excluir selos e critérios; **conceder ao satisfazer o critério** — artefato: selo silencioso *ou* certificado PDF (critério "jornada concluída"); exibir | [E8.1](../requisitos.md#rf-e8-1)–8.3, E11.3 |
| **`Scheduling`** | Agenda de mentoria · *disponibilidade · slots · sessões · registro · sync · check-in de sessão* | publicar disponibilidade em slots; bloquear conflito com Google; agendar sessão no limite do pacote; não remarcar; transferir a colega (notifica); linha do tempo; confirmar realização e registrar combinado; conectar Google (OAuth) e criar evento com convite; **check-in da sessão** (*reusa `Form`*) | [E5.1](../requisitos.md#rf-e5-1)–5.13 |
| **`Document`** | Repositório por empresa · *pastas · remetente* | armazenar docs por empresa em pastas; enviar/baixar no escopo (blob via `Media`); registrar remetente (Nuree/cliente); CRUD pastas; restringir ao escopo | [E12.1](../requisitos.md#rf-e12-1)–12.5 |
| **`Task`** | Execução do trabalho · *tarefas · ciclos · rollover · templates · sementeira* | criar/editar/mover/excluir tarefas e subtarefas; visões lista/kanban/calendário; organizar em ciclos e na sementeira; encerrar ciclo com rollover; templates de ciclo (instanciar gera ciclo+tarefas) | [E3.1](../requisitos.md#rf-e3-1)–3.12 |

## Módulo assíncrono (módulo + worker)

| Módulo | O que é | Ações | RF |
|---|---|---|---|
| **`Messaging`** | Modelos + disparos WhatsApp | CRUD modelos (gatilho, canal, variáveis); agendar disparo no gatilho; enviar via WhatsApp com status e retry | [E9.1](../requisitos.md#rf-e9-1)–9.3 |

O **worker** (mesma imagem, `node worker`) também roda os jobs de domínio: rollover de `Cycle`
([E3.9](../requisitos.md#rf-e3-9)), PDF de `Recognition` ([E11.3](../requisitos.md#rf-e11-3)) e convite
de `Account` ([E2.8](../requisitos.md#rf-e2-8)).

## Infra (transversal, não é módulo de domínio)

| | O que é | RF |
|---|---|---|
| **`Media`** | armazenar/servir blobs via **presigned URL** (MinIO) — o cliente baixa direto | E6.2, E11.3 |
| **`Scope`** | JWT/RBAC, rate limit, CORS, validação; prende toda query ao escopo; nega fora de escopo (exceto Admin) | RNF-3, E1.6/1.7, RNF-2 |

---

## Leitura e apresentação (fora do domínio)

Não são módulos de domínio — não são donos de dados, só **compõem** (leem) os outros módulos.

- **`Reporting`** — painéis por Programa e por Pessoa ([E2.6](../requisitos.md#rf-e2-6)): *read-model*
  do lado de consulta que lê `Task`, `Journey`, `Scheduling`, `Recognition` via os services públicos.
  Efferent alto **de leitura** é a natureza de um dashboard, não um vício; não escreve nada.
- **Hub de construtores** ([E2.4](../requisitos.md#rf-e2-4)) — navegação de UI (o frontend conhece as
  rotas dos builders); sem backend próprio.

## Relações entre módulos

Só arestas sustentadas por RF, ancoradas no módulo exato. (As relações internas ao `Scheduling` —
sessão↔slot↔sync — deixam de ser inter-módulo com a fusão.)

| Origem → Destino | Semântica | Requisito |
|---|---|---|
| `Diagnostic` → `Task` | plano de ação vira tarefas | [E10.3](../requisitos.md#rf-e10-3) |
| `Diagnostic` → `Form` | reusa o motor (coleta em escala) | [E10.1](../requisitos.md#rf-e10-1) |
| `Scheduling` → `Form` | check-in da sessão reusa o motor | [E5.13](../requisitos.md#rf-e5-13) |
| `Journey` → `Form` | anexa formulário à etapa | [E7.2](../requisitos.md#rf-e7-2) |
| `Journey` → `Track` | anexa trilha à etapa | [E7.2](../requisitos.md#rf-e7-2) |
| `Journey` → `Recognition` | anexa selo à etapa | [E7.2](../requisitos.md#rf-e7-2) |
| `Journey` → `Recognition` | jornada concluída concede certificado | [E11.3](../requisitos.md#rf-e11-3) |
| `Journey` → `Scheduling` | encontro agenda sessão | [E7.1](../requisitos.md#rf-e7-1) |
| `Journey` → `Messaging` | gatilho antes/depois de encontro | [E9.2](../requisitos.md#rf-e9-2) |
| `Scheduling` → `Messaging` | gatilho de lembrete de sessão | [E5.7](../requisitos.md#rf-e5-7) |
| `Track` → `Form` | quiz reusa o motor | [E4.6](../requisitos.md#rf-e4-6) |
| `Track` → `Recognition` | conclusão concede selo | [E8.2](../requisitos.md#rf-e8-2) |

Dependências de infra: `Track`, `Recognition` (PDF) e `Document` consomem `Media`; **todos**
atravessam `Scope`.

**`Form`** é o de maior acoplamento de entrada (`Diagnostic`, `Track`/quiz, `Scheduling`/check-in,
`Journey` o reusam) → estável e genérico. **`Journey`** é o de maior acoplamento de saída →
orquestrador, barato de mudar.

---

## Eventos de domínio (eixo de conclusão)

As reações "quando X → então Y" são **coreografadas**, não orquestradas: o produtor **emite um
evento** (in-process, síncrono — `@nestjs/event-emitter`) e os interessados reagem. `Journey`
**emite** ("etapa/jornada concluída"), não chama `Recognition`/`Messaging`.

- **Reações internas** (selo/certificado/maturidade/progresso) rodam na **mesma transação** (ACID,
  Postgres compartilhado). `Recognition` avalia **os próprios critérios** com os dados do evento —
  nunca lê tabelas de outro módulo.
- **Efeitos externos/temporais** (mensagem) vão para a **fila** (`Messaging`), não pelo evento.

Não é EDA (estilo) — é um *dispatcher* de eventos **in-process**; continua 1 quantum, sync.

| Evento | Emissor | Reagem |
|---|---|---|
| `RespostaEnviada` ([E4.5](../requisitos.md#rf-e4-5)) | `Form` | `Diagnostic` (maturidade) · `Track` (quiz) · `Journey` (etapa c/ formulário) |
| `TrilhaConcluida` ([E6.3](../requisitos.md#rf-e6-3)) | `Track` | `Journey` · `Recognition` (critério de selo) |
| `EtapaConcluida` ([E7.4](../requisitos.md#rf-e7-4)) | `Journey` | `Recognition` |
| `JornadaConcluida` | `Journey` | `Recognition` (certificado) |
| `PresencaRegistrada` ([E11.1](../requisitos.md#rf-e11-1)) | `Journey` | `Journey` (etapa de evento) · `Recognition` (carga → certificado) |
| *gatilho de encontro/sessão* ([E9.2](../requisitos.md#rf-e9-2)/[E5.7](../requisitos.md#rf-e5-7)) | `Journey`/`Scheduling` | **fila** → `Messaging` |

> As arestas *concede*/*gatilho* da tabela de relações acima são **mediadas por evento/fila** — o
> emissor não conhece o reagente. As demais (*anexa*, *reusa motor*, *plano vira tarefas*) são
> chamadas síncronas diretas ao service público do módulo-alvo.

## Estilo (resumo)

**Um quantum de deploy** — monólito particionado por domínio; os módulos acima num único
deployable (imagem em modo `api` + modo `worker`). **Postgres único**, tabelas por módulo. **Sync
in-process por padrão**; **async só no worker** (`Messaging` + jobs de domínio). `Media` via
presigned URL. Detalhe e trade-offs no
[diário de arquitetura](../../reunioes/exploracao-arquitetura-2026-08-02.md).
