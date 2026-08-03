---
hide:
  - navigation
---

# Registro de exploração de arquitetura — app-nuree

Diário **vivo** do desenho do app-nuree: o processo seguido, as decisões, e as alternativas
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

Varredura dos [requisitos](requisitos.md) → lista de candidatas → poda. Detalhe em
[características de arquitetura](caracteristicas.md).

- **Dirigentes:** Segurança, Configurabilidade, Composabilidade — e **Evolutibilidade**,
  percebida como implícita mais tarde.
- **Rebaixadas de propósito:** escalabilidade, performance (*exceto* streaming de mídia — que
  é problema de servir blob, local ao componente de Mídia), localização.
- **Descartadas:** reusabilidade/extensibilidade avulsas (absorvidas por Composabilidade e
  Evolutibilidade) e interoperabilidade como dirigente (é contrato de borda, não molda partição).

### 2. Componentes — critério e refinamento

- **Primeira tentativa falhou:** misturei três lentes (épicos + entidades + ações), o que deu
  uma colcha de retalhos incoerente. Corrigido ao escolher **uma** lente.
- **Critério escolhido: função de negócio.** Motivos do Gabriel: modela o negócio (não detalhe
  de implementação); fácil migrar para distribuído depois; equipe = 1 (technical partitioning
  não traz ganho de divisão por time); facilita conversar com a equipe não-técnica; e é o corte
  que ele quer aprender na prática.
- **Produtos (Gestão · Pessoas · Lab · Pulse) não são eixo de agrupamento** — são
  **composição** de funções (uma dimensão), pelos próprios requisitos: `Produto` é catálogo,
  `Programa` é a instância ([RF-E1.1](requisitos.md#rf-e1-1), [RF-E1.2](requisitos.md#rf-e1-2)),
  com módulos compartilhados.
- **Refinamentos aplicados:**
    - Mentoria + Eventos/Certificação **dissolvidos na Jornada** — um encontro é o mesmo
      conceito seja 1:1 ou presencial; o certificado é desfecho da jornada.
    - **Agendamento** virou função de **suporte** (disponibilidade, slots, calendar).
    - **Mídia** saiu do mapa de negócio (**infra técnica**), diferente de **Documentos**
      (função de negócio real).
    - **Documentos** (mini-drive por empresa, com remetente Nuree vs cliente) — novo requisito,
      virou o épico [E12](requisitos.md).
- **Resultado: 9 funções** — Contas & Contratos, Execução do trabalho, Avaliação & Diagnóstico,
  Jornada, Agendamento *(suporte)*, Aprendizagem, Reconhecimento, Comunicação, Documentos.
  Ver `componentes.drawio`.

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
| Particionamento **técnico** vs **domínio** | ponto de partida | Escolhido **domínio**. |
| **Monólito modular** (domínio) | simplicidade; ACID na composição; isolamento num ponto único; solo dev; preserva a rota de migração | Favorecido pelas características — **não cravado**. |
| **Service-based** (meio-termo) | flexível; DB único mantém ACID; variantes de topologia (UI/DB/API/lib, Fig 13-2..13-7) | Eixo real = **quantum de deploy**, não o banco. Sem ganho para solo dev + escala rebaixada. **Em aberto.** |
| **Microkernel** | (eu tendenciei para cá) | **Rejeitado** como enquadramento; o "núcleo" que propus era coarse-grained demais. |
| **Event-driven** (broker/mediator) | o domínio tem semântica de evento | Semântica de evento **≠** EDA. Sem mediador (reações são de 1 salto); async durável só na fronteira de **Comunicação**, por Confiabilidade. |

### 5. Insights que ficaram

- **Event bus / EDA é estilo de comunicação, não arquitetura** — vem *depois* das características.
- **Semântica de evento nos requisitos não obriga arquitetura de evento.**
- **Streaming** é entrega de mídia, não EDA.
- O **motor de formulários** é reusado por quiz, check-in, diagnóstico, Mergulho e PDI — logo
  não fala só com Tarefas. A granularidade (motor único vs motores que reusam um núcleo)
  continua **em aberto**.

---

## Decisões — status

| Decisão | Status |
|---|---|
| Ordem do processo (características → componentes → …) | Travada |
| Dirigentes: Segurança, Configurabilidade, Composabilidade | Travada |
| Evolutibilidade como característica | Adicionada — tier (dirigente vs suporte) a ratificar |
| Escala/performance/localização rebaixadas | Travada |
| Particionar por **função de negócio** | Travada |
| Produtos como dimensão de composição (não grupo) | Travada |
| Merge de Mentoria/Eventos na Jornada; Agendamento como suporte | Travada |
| Documentos como função (E12); Mídia como infra | Travada |

## Em aberto

- **Estilo de deploy:** monólito modular vs service-based (o eixo é o quantum de deploy).
  Orquestração (Docker Compose vs k8s) influencia o custo de separar funções.
- **Formulários:** motor único vs motores separados que reusam um núcleo.
- **Evolutibilidade:** dirigente ou suporte?
- **Plano de Ação:** fica em Avaliação ou vira ponte para Execução?
- **Mecanismo dos eventos:** observer in-process vs fila durável — decidir *por fronteira*.
- **Documentos:** detalhar (versionamento? tipos de arquivo? limites?).

## Referências

- [Requisitos](requisitos.md) · [Características](caracteristicas.md) · [Ações](acoes.md)
- `componentes.drawio` — mapa de funções de negócio e suas relações
