---
hide:
  - navigation
---

# Diagramas

Os diagramas do desenho. Dois são a **fonte da verdade**; dois ficam como **anti-patterns**, de
propósito. Detalhe textual em [Componentes](componentes.md); o porquê no
[diário](../../reunioes/exploracao-arquitetura-2026-08-02.md).

## Fonte da verdade

**Acoplamento** — os componentes e o que fala com o quê; foi daqui que o estilo se derivou.

![Acoplamento de componentes](componentes-flat.drawio.png)

**Topologia** — o estilo cravado (1 quantum + worker + infra).

![Topologia do app-nuree](topologia.drawio.png)

## Anti-patterns

**v1 — *entity trap*.** Uma caixa por entidade; ainda sem o conceito de componente.

![Anti-pattern v1](componentes.drawio.png)

**v2 — serviços prematuros.** Componentes agrupados em serviços sem driver (solo dev, escala
rebaixada, acoplamento semântico).

![Anti-pattern v2](componentes-v2.drawio.png)

## Relacionados

- [Componentes](componentes.md) · [Características](caracteristicas.md) · [Fitness functions](fitness-functions.md)
