# 2. Componentes — partição e granularidade

- **Status:** aceito
- **Data:** 2026-08-03
- **Depende de:** [ADR 1](0001-caracteristicas.md)

## Problema

Como cortar os componentes a partir das [características](0001-caracteristicas.md), evitando o
*entity trap* (uma caixa por entidade)?

## Opções consideradas

- **Partição:** técnica (camadas) **vs** por domínio.
- **Lente de agrupamento:** épicos · entidades · ações · função de negócio.
- **Produtos** (Gestão/Pessoas/Lab/Pulse) como **eixo de agrupamento** vs como **composição**.
- **Granularidade** dos limítrofes (Itens, Certificado, Check-in, Task/Cycle, console do Account…).

## Decisão

**Partição por domínio; a lente de "função de negócio" refinada em módulos** (capacidade com
comportamento próprio e dono de dados). **10 módulos** de núcleo + `Messaging` (async) + `Reporting`
(read-model) + infra (`Media`, `Scope`); **produtos são composição**, não grupo. Nomes canônicos em
inglês. Catálogo em [Componentes](../componentes.md).

## Por que — e o que foi descartado

- **Partição técnica (layered) → descartada.** Não modela o negócio nem facilita conversar com a
  equipe não-técnica; é detalhe de implementação.
- **v1 — *entity trap* → anti-pattern.** Uma caixa por entidade (Usuário, Certificado, Remetente…);
  faltava o conceito do que é um componente.
- **v2 — serviços prematuros → anti-pattern.** Agrupar componentes em serviços sem driver.
- **Colapsos** (régua: componente = capacidade + dono de dados, nome de negócio, alta coesão):
  `Itens`→`Track`; `Certificado`→`Recognition` (é artefato da concessão, não módulo);
  `Check-in`→`Scheduling`; `Task`+`Cycle`→`Task`; console→`Reporting`;
  `Produto`/`Sementeira`/`Critérios`/`Pastas`/`Remetente` → seus donos.

## Consequências

- `Form` = maior acoplamento de **entrada** (afferent) → estável e genérico.
- `Journey` = maior acoplamento de **saída** (efferent) → compõe, e coordena por **evento**
  (ver [ADR 3](0003-estilo.md)), não por chamada.
- `Reporting` é read-model (só lê); o hub de construtores é UI.
