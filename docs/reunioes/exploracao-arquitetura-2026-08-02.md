---
hide:
  - navigation
---

# Reunião — Exploração de arquitetura (app-nuree)

- **Data:** 2026-08-02
- **Tipo:** individual (Gabriel)
- **Escopo:** app-nuree

Diário **vivo** desta sessão de desenho: o processo seguido, as decisões, e as alternativas
consideradas (inclusive as descartadas). Existe para que o *porquê* não se perca — um desenho
sem o raciocínio apodrece.

Ordem seguida (Ford/Richards, *Fundamentals of Software Architecture*):
**características → componentes → comunicação → estilo**.

## Princípios do processo

- **Catalogar fatos dos requisitos antes de decidir estrutura.** Não antecipar mecanismo nem
  topologia (façade, fila, broker, storage, agrupamento, quantum de deploy) enquanto ainda se
  mapeia — mesmo que "provavelmente vá ser assim".
- **Poucas características dirigentes** — marcar tudo como importante é não priorizar nada.
- **Cada componente e relação ancorado num requisito** (RF/RNF citado).
- Decisões ficam **explícitas e revisáveis**; alternativas descartadas ficam registradas com o
  motivo, para não voltarem sem querer.

---

## Sessão — 2026-08-02

### 1. Características a partir dos requisitos

Varredura dos [requisitos](../app-nuree/requisitos.md) → lista de candidatas → poda. Detalhe em
[características de arquitetura](../app-nuree/arquitetura/caracteristicas.md).

- **Dirigentes:** Segurança, Configurabilidade, Composabilidade — e **Extensibilidade** e
  **Evolutividade** (a família de "mudança barata"), percebidas depois.
- **Rebaixadas de propósito:** escalabilidade, performance (*exceto* streaming de mídia — que
  é problema de servir blob, local ao componente de Mídia), localização.
- **Descartadas:** reusabilidade avulsa (absorvida por Composabilidade) e interoperabilidade
  como dirigente (é contrato de borda, não molda partição).

### 2. Componentes — critério e refinamento

- **Decisão — particionar por domínio (não técnico).** Motivos do Gabriel: modela o negócio
  (não um detalhe de implementação); fácil migrar para distribuído depois; equipe = 1
  (technical partitioning não traz o ganho de dividir por time); facilita conversar com a
  equipe não-técnica; e é o corte que ele quer aprender na prática.
- **Primeira tentativa de blocos falhou:** misturei três lentes (épicos + entidades + ações),
  o que deu uma colcha de retalhos incoerente. Corrigido ao escolher **uma** lente.
- **Lente escolhida: função de negócio** (dentro do particionamento por domínio).
- **Produtos (Gestão · Pessoas · Lab · Pulse) não são eixo de agrupamento** — são
  **composição** de funções (uma dimensão), pelos próprios requisitos: `Produto` é catálogo,
  `Programa` é a instância ([RF-E1.1](../app-nuree/requisitos.md#rf-e1-1), [RF-E1.2](../app-nuree/requisitos.md#rf-e1-2)),
  com módulos compartilhados.
- **Refinamentos aplicados:**
    - Mentoria + Eventos/Certificação **dissolvidos na Jornada** — um encontro é o mesmo
      conceito seja 1:1 ou presencial; o certificado é desfecho da jornada.
    - **Agendamento** virou função de **suporte** (disponibilidade, slots, calendar).
    - **Mídia** saiu do mapa de negócio (**infra técnica**), diferente de **Documentos**
      (função de negócio real).
    - **Documentos** (mini-drive por empresa, com remetente Nuree vs cliente) — novo requisito,
      virou o épico [E12](../app-nuree/requisitos.md).
- **Resultado: 9 funções** — Contas & Contratos, Execução do trabalho, Avaliação & Diagnóstico,
  Jornada, Agendamento *(suporte)*, Aprendizagem, Reconhecimento, Comunicação, Documentos.
  Ver `app-nuree/arquitetura/componentes.drawio`.

!!! warning "Ainda não são 'os componentes'"
    Os blocos de hoje são um **mapa de domínio por função de negócio**, não o resultado do
    *component identification*. Vira desenho de componentes quando o corte (o que agrupa com o
    quê, e a responsabilidade de cada um) for decidido. Alguns blocos são entidades (Usuário,
    Certificado) e podem ser absorvidos — cuidado com o *entity trap*.

### 3. Conexões / comunicação

- Só arestas sustentadas por RF: `plano vira tarefas`, Jornada `anexa` formulário/trilha/selo e
  dispara `encontros`/mensagens, `conclusão concede` selo, `quiz conclui trilha`.
- **Observações estruturais:**
    - Requisitos **equilibrados** entre as funções → não há *god-component*.
    - **Jornada** = alto acoplamento de **saída** (efferent); pouca coisa aponta de volta. É o
      orquestrador **instável** — mudá-la é barato. É onde a **Composabilidade** se materializa.
    - **Formulários** (em Avaliação) e **Contas** = alto acoplamento de **entrada** (afferent):
      muitos dependem deles → têm de ser **estáveis** (change-averse).

### 4. Arquiteturas consideradas

| Estilo | Levantado por | Situação |
|---|---|---|
| **Monólito modular** (domínio) | simplicidade; ACID na composição; isolamento num ponto único; solo dev; preserva a rota de migração | Favorecido pelas características — **não cravado**. |
| **Service-based** (meio-termo) | flexível; DB único mantém ACID; variantes de topologia (UI/DB/API/lib, Fig 13-2..13-7) | Eixo real = **quantum de deploy**, não o banco. Sem ganho para solo dev + escala rebaixada. **Em aberto.** |
| **Microkernel** | (eu tendenciei para cá) | **Rejeitado** como enquadramento; o "núcleo" que propus era coarse-grained demais. |
| **Event-driven** (broker/mediator) | o domínio tem semântica de evento | Semântica de evento **≠** EDA. Sem mediador (reações são de 1 salto); async durável só na fronteira de **Comunicação**, por Confiabilidade. |

### 5. Insights que ficaram

- **Mecanismo × estilo:** um *event bus* é um mecanismo de comunicação; *event-driven
  architecture* **é um estilo de arquitetura**. A escolha do estilo vem *depois* das
  características — e as nossas não a pedem.
- **Semântica de evento nos requisitos não obriga adotar EDA.**
- **Streaming** é entrega de mídia, não EDA.
- O **motor de formulários** é reusado por quiz, check-in, diagnóstico, Mergulho e PDI — logo
  não fala só com Tarefas. A granularidade (motor único vs motores que reusam um núcleo)
  continua **em aberto**.

---

## Sessão — 2026-08-03

### 6. Refinamento dos componentes (colapsos de entity-trap)

- `Itens` colapsa em `Trilhas`; e `Sementeira`, `Registro do combinado`, `Critérios`, `Pastas`,
  `Remetente`, `Produto`, `Check-in (form)` colapsam nos respectivos donos.
- **`Certificado` não é componente** — é artefato do **mesmo mecanismo de concessão** dos selos
  (critério "jornada concluída"); a `Presença` alimenta via conclusão de etapa.
- Dois check-ins **distintos**: **presencial** ([RF-E11.1](../app-nuree/requisitos.md#rf-e11-1), QR) e
  **de sessão online** (novo [RF-E5.13](../app-nuree/requisitos.md#rf-e5-13)); ambos reusam o motor
  genérico de `Form` (scoring de quiz mora em `Trilhas`, não no motor).
- `Etapas` → renomeado **`Jornada`** (etapas são entidade interna).
- **Nomes canônicos em inglês** (EN · singular · PascalCase): `Form`, `Journey`, `Track`,
  `Diagnostic`, `Recognition`, `Task`, `Cycle`, `Session`, `Scheduling`, `CalendarSync`, `Checkin`,
  `Attendance`, `Messaging`, `Document`, `Account`, `Auth`, `Media`, `Scope`. Tensão com
  [RNF-5](../app-nuree/requisitos.md#rnf-5): código EN, rotas/UI em PT.

### 7. Escolha do estilo (Ford/Richards, Cap. 18)

Rodamos *Choosing the Appropriate Architecture Style*. Os pré-requisitos já estavam preenchidos;
o trabalho foi nas **três determinações**.

- **A — quantum de deploy: UM.** Monólito **particionado por domínio** (o "modular monolith", que
  **não está no catálogo dos 8 estilos** — é o buraco entre *Layered* (técnico) e *Service-based*
  (domínio, distribuído)).
    - `Messaging` fica como **módulo + worker** (mesma imagem, `node worker`), **não serviço**: as
      características divergentes (Confiabilidade/retry, isolamento da falha do provider, escala de
      rajada) já vêm do worker (nível *b*); os ganhos exclusivos de um serviço (deploy independente,
      reuso externo) **não têm driver**. Um serviço só = imposto de distribuído cheio por ganho marginal.
    - `Media` via **presigned URL** (MinIO serve direto) → sem serviço.
    - `Document` = módulo (sem característica operacional divergente).
- **B — dados:** **Postgres único**, **tabelas por módulo** (propriedade/isolamento lógico, não
  *integration database*); Redis/BullMQ (fila); MinIO (blobs).
- **C — comunicação:** **sync in-process por padrão** (a composição semântica prospera assim);
  **async só no worker** — jobs de domínio (rollover [E3.9](../app-nuree/requisitos.md#rf-e3-9),
  PDF de certificado [E11.3](../app-nuree/requisitos.md#rf-e11-3), convite
  [E2.8](../app-nuree/requisitos.md#rf-e2-8)) e WhatsApp.

**Rota preservada:** service-based é a evolução barata se um driver aparecer (extrair `Messaging` ou
`Document`). k8s de reserva; hoje Compose basta ([RC-2](../app-nuree/requisitos.md#rc-2) confirmado).

**Nota de taxonomia:** dos 8 estilos, os monolíticos (Layered/Pipeline/Microkernel) são
technical-partitioning ou de propósito específico; o único que casa com domain-partitioning é
**Service-based** — do qual o nosso monólito domain-partitioned é o degrau imediatamente abaixo.

### 8. Eixo de conclusão — coreografia in-process

Como as reações "quando X → então Y" (conceder selo/certificado, disparar mensagem) acontecem:
**coreografia por evento, não orquestração**. Decidido *por fronteira*:

- **Reações internas** (selo/certificado/maturidade/progresso) → **evento de domínio in-process
  síncrono** (`@nestjs/event-emitter`), na mesma transação (ACID, Postgres compartilhado).
- **Efeitos externos/temporais** (mensagem) → **fila durável** (`Messaging`).

`Journey` **emite** (`EtapaConcluida`/`JornadaConcluida`), não chama `Recognition`/`Messaging` →
some o risco de god-module. `Recognition` avalia **os próprios critérios** com os dados do evento →
não fura a propriedade de tabelas. Catálogo de eventos em
[`componentes.md`](../app-nuree/arquitetura/componentes.md); blindagem em
[`fitness-functions.md`](../app-nuree/arquitetura/fitness-functions.md) (FF-7, anti-ciclo).

**Não é EDA** (estilo) — é um *dispatcher* in-process (observer); continua 1 quantum, sync. Semântica
de evento no domínio ≠ arquitetura orientada a eventos.

Refinamento de granularidade nesta rodada: `Checkin` fundiu em `Scheduling`; `Task`+`Cycle` viraram
`Task`; "clonar template" saiu de `Account` (é um `clone()` em cada builder).

---

## Decisões — status

| Decisão | Status |
|---|---|
| Ordem do processo (características → componentes → …) | Travada |
| Dirigentes: Segurança, Configurabilidade, Composabilidade | Travada |
| Extensibilidade e Evolutividade como características | Adicionadas — tier (dirigente vs suporte) a ratificar |
| Escala/performance/localização rebaixadas | Travada |
| Particionar por **domínio** (não técnico) | Travada |
| Agrupar (dentro de domínio) por **função de negócio** | Travada |
| Produtos como dimensão de composição (não grupo) | Travada |
| Merge de Mentoria/Eventos na Jornada; Agendamento como suporte | Travada |
| Documentos como função (E12); Mídia como infra | Travada |
| **Estilo: monólito particionado por domínio (1 quantum) + worker async** | Travada (03/08) |
| `Messaging` como módulo+worker (nível *b*), não serviço | Travada (03/08) |
| Dados: Postgres único, tabelas por módulo | Travada (03/08) |
| Sync in-process por padrão; async só no worker | Travada (03/08) |
| Eixo de conclusão: coreografia por **evento in-process** (reações internas) + **fila** (Messaging) | Travada (03/08) |
| Granularidade: `Checkin`→`Scheduling`; `Task`+`Cycle`→`Task`; clone por builder | Travada (03/08) |
| `Account` = tenancy + user; console sai → `Reporting` (read-model) + hub de UI | Travada (03/08) |
| `Media` via presigned URL (sem serviço); `Document` = módulo | Travada (03/08) |
| Certificado = concessão (não é componente); `Itens`→`Trilhas`; check-in presencial vs online | Travada (03/08) |
| Motor de `Form` único e genérico; verticais (quiz/check-in/diagnóstico) reusam | Travada (03/08) |
| Nomes canônicos em inglês | Aprovada por ora |

## Em aberto

- **Extensibilidade e Evolutividade:** dirigentes ou suporte? (a ratificar)
- **Documentos:** detalhar (versionamento? tipos de arquivo? limites?).
- **Documentar** cada módulo em detalhe (contratos/tabelas) quando entrar em desenvolvimento.

## Artefatos desta exploração

- [`componentes.md`](../app-nuree/arquitetura/componentes.md) — módulos (nomes canônicos EN), ações e relações.
- [`fitness-functions.md`](../app-nuree/arquitetura/fitness-functions.md) — testes que protegem as decisões.
- `app-nuree/arquitetura/topologia.drawio` — topologia-alvo (1 quantum + worker + infra).
- `app-nuree/arquitetura/componentes-flat.drawio` — vista de acoplamento (derivou o estilo).
- `app-nuree/arquitetura/componentes.drawio` — **anti-pattern** preservado (entity-trap por caixa).

## Referências

- [Requisitos](../app-nuree/requisitos.md) · [Características](../app-nuree/arquitetura/caracteristicas.md) · [Ações](../app-nuree/acoes.md)
- `app-nuree/arquitetura/componentes.drawio` — mapa de funções de negócio e suas relações
