# 3. Estilo de arquitetura

- **Status:** aceito
- **Data:** 2026-08-03
- **Depende de:** [ADR 1](0001-caracteristicas.md), [ADR 2](0002-componentes.md)

## Problema

Qual estilo de arquitetura, rodando as três determinações de Ford/Richards (Cap. 18): **quantum de
deploy**, **onde vivem os dados**, **sync vs async**?

## Opções consideradas

Os 8 estilos do catálogo:

- **Layered** — technical partitioning; contradiz a partição por domínio ([ADR 2](0002-componentes.md)).
- **Pipeline** — pipes & filters; não é um fluxo sequencial.
- **Microkernel** — a Configurabilidade *parecia* plug-in, mas aqui é **por-dado** (builder); o
  "núcleo" proposto era coarse demais.
- **Event-driven (EDA)** — semântica de evento no domínio **≠** estilo orientado a eventos; nada
  pede broker/mediator global.
- **Space-based / SOA** — escala e complexidade não pedem.
- **Microservices** — o acoplamento semântico (a jornada compõe *com contexto*) + escala rebaixada +
  solo dev tornam errar fronteira **caro**; sem driver.
- **Service-based** — considerado a sério; mas o único candidato a serviço separado (`Messaging`)
  paga o **imposto de distribuído cheio por ganho marginal** — um worker (mesma imagem) já entrega
  Confiabilidade/isolamento/escala.
- **Monólito particionado por domínio** ("modular monolith", fora do catálogo) — **vencedor**.

## Decisão

**Monólito particionado por domínio — 1 quantum.**

- **Quantum:** um deployable (imagem em modo `api` + `worker`). `Messaging` = módulo + worker (não
  serviço); `Media` = presigned URL (sem serviço); `Document` = módulo.
- **Dados:** **Postgres único**, tabelas por módulo (isolamento lógico, não *integration database*).
- **Comunicação:** **sync in-process por padrão**; async só no worker (fila `Messaging` + jobs). O
  **eixo de conclusão** é **coreografia por evento in-process** — `Journey` emite, os outros reagem;
  não é EDA (é um *dispatcher* local).

## Por que o vencedor

- Características **uniformes** no grosso do sistema → *single set → monolith* (Ford). Só
  `Messaging` (Confiabilidade) e `Media` (blob) divergem, e ambas se resolvem **local**.
- Segurança pede **ponto único de escopo**; a composição semântica prospera em chamadas
  **in-process/ACID** — o oposto de fragmentar em rede.
- Solo dev + escala rebaixada → o degrau mais baixo; **Docker Compose** basta, k8s de reserva.
- **Rota preservada:** as fronteiras limpas de módulo tornam a extração futura para service-based
  barata, se um driver aparecer.

## Consequências

- Deploy em Docker Compose ([RC-2](../../requisitos.md#rc-2)); worker na mesma imagem.
- As decisões acima ficam protegidas por (0004-fitness-functions.md)".
