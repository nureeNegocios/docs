---
hide:
  - navigation
---

# Fitness functions — app-nuree

Testes de arquitetura que **protegem** as características dirigentes e as decisões de estilo
(ver [diário de arquitetura](../../reunioes/exploracao-arquitetura-2026-08-02.md)). Cada uma existe
para que uma decisão não se degrade em silêncio conforme o código cresce.

Classificação (Ford/Richards): **atomic** (um aspecto) vs **holistic** (combinação); **static**
(sem executar) vs **dynamic** (em execução); **triggered** (no CI/PR) vs **continuous** (em
produção).

---

## FF-1 · Isolamento multi-tenant

| | |
|---|---|
| **Protege** | Segurança / `Scope` — [RNF-2](../requisitos.md#rnf-2), [E1.6](../requisitos.md#rf-e1-6)/[E1.7](../requisitos.md#rf-e1-7) |
| **Tipo** | atomic · static + dynamic · triggered + continuous |

- **Static:** regra que **proíbe consulta ao ORM fora da camada de escopo** — todo acesso passa por
  um repositório que injeta o filtro de tenant (`empresa/programa`). Falha o build se um módulo usa
  o client cru (`prisma.*.findMany`) sem o guard.
- **Dynamic (CI):** suíte que, autenticada como tenant **A**, tenta ler/escrever dados de **B** em
  cada endpoint e **exige negação** (403 ou conjunto vazio); Admin é a exceção esperada.
- **Continuous (prod):** interceptor que conta **queries sem cláusula de tenant** e dispara alerta
  (deve ser sempre zero).

## FF-2 · Dependência entre módulos (quantum coeso)

| | |
|---|---|
| **Protege** | módulos **donos das suas tabelas**; sem *integration database*; núcleo só **enfileira** para `Messaging` |
| **Tipo** | atomic · static · triggered |

- Regra de arquitetura (dependency-cruiser / ts-arch): **um módulo só acessa as próprias tabelas**;
  dado de outro módulo só via a **API pública** dele — proíbe `import` cruzado de camadas internas.
- **`Messaging` nunca é importado direto** por outro módulo — a única via é publicar na fila.
  Falha o build se algum módulo importa `messaging/*` fora do adapter de fila.
- **`Journey` não importa `Recognition`** (nem `Messaging`) — ele **emite evento** / enfileira. Falha
  o build se `journey/*` importa esses módulos (garante que `Journey` não vira orquestrador/god-module).
- Efeito: mantém a rota de migração para service-based barata (a fronteira do módulo já é limpa).

## FF-3 · `Form` estável e genérico

| | |
|---|---|
| **Protege** | Composabilidade — `Form` é afferent alto (`Diagnostic`, `Track`/quiz, `Checkin`, `Journey` o reusam) |
| **Tipo** | atomic · static · triggered |

- **`Form` não importa nenhum módulo-vertical** (`Track`, `Diagnostic`, `Checkin`, `Journey`) — o
  motor não conhece seus consumidores. Falha o build se conhecer (garante que scoring de quiz mora
  em `Track`, cálculo de maturidade em `Diagnostic`, etc.).
- **Contrato público versionado:** mudança no DTO/API de `Form` exige revisão explícita
  (CODEOWNERS + teste de contrato/snapshot), porque muitos dependem.

## FF-4 · `Media` só por presigned URL

| | |
|---|---|
| **Protege** | decisão de entrega de mídia sem serviço — o backend **não serve blob** |
| **Tipo** | atomic · static · triggered |

- Proíbe **proxy/stream de blob** no backend: nenhum handler retorna o corpo de um arquivo do
  MinIO. O endpoint de mídia responde **URL pré-assinada** (com expiração), nunca o binário.
- Garante que a característica de entrega (IO/banda) fica no MinIO/CDN e não compete com o núcleo.

## FF-5 · Integrações e trabalho pesado fora do request

| | |
|---|---|
| **Protege** | Confiabilidade — async só no worker; sync por padrão no núcleo |
| **Tipo** | atomic · static + dynamic · triggered |

- **Static:** proíbe chamar o cliente **WhatsApp** e disparar **jobs pesados** (PDF de certificado,
  rollover de ciclo) no código de **handlers de request** — só é permitido **enfileirar**.
- **Dynamic (CI):** ao concluir uma etapa/agendar sessão, o teste verifica que o handler **enfileira**
  (mock da fila) e **não** faz a chamada externa no caminho síncrono.

## FF-6 · Convenção de idioma

| | |
|---|---|
| **Protege** | [RNF-5](../requisitos.md#rnf-5) — rotas/UI em PT, código em EN |
| **Tipo** | atomic · static · triggered |

- Lint que as **rotas expostas** estão em português; identificadores de módulo/código em inglês
  (os nomes canônicos: `Form`, `Journey`, `Track`, …).

## FF-7 · Eventos de domínio sem ciclo

| | |
|---|---|
| **Protege** | o eixo de conclusão coreografado — evitar cascata/reentrância |
| **Tipo** | atomic · static + dynamic · triggered |

- **Static:** o grafo de **evento → listener → evento** é acíclico — nenhum listener emite um evento
  que reentra na própria cadeia. Falha o build se um ciclo é detectado.
- **Dynamic (CI):** teste que uma `EtapaConcluida` não dispara, transitivamente, outra
  `EtapaConcluida` da mesma participação (guarda de idempotência/reentrância).
- **Reações internas são in-process síncronas** (mesma transação); só `Messaging` vai para a fila.
  Falha se um handler de evento interno chama integração externa direto (deve enfileirar).

---

## Governança (não automatizável)

- **Rota para service-based preservada:** ao rever PRs que tocam fronteiras de módulo, checar se a
  extração futura de `Messaging`/`Document` continua barata (fronteira limpa, sem join cruzado). É
  *design review*, não fitness function.
- **Tiers de característica** ([caracteristicas](caracteristicas.md)) revisados a cada épico novo.
