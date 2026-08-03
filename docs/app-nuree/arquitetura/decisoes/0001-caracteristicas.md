# 1. Características de arquitetura dirigentes

- **Status:** aceito
- **Data:** 2026-08-02

## Problema

Quais *-ilities* devem **dirigir** o particionamento? A disciplina de Ford/Richards é *poucas
dirigentes* — marcar tudo como importante é não priorizar nada.

## Opções consideradas

Candidatas levantadas dos [requisitos](../../requisitos.md): Segurança, Configurabilidade,
Composabilidade, Extensibilidade, Evolutividade, Escalabilidade, Performance, Localização,
Reusabilidade, Interoperabilidade, Confiabilidade, Recuperabilidade, Confidencialidade,
Acessibilidade.

## Decisão

**Dirigentes: Segurança, Configurabilidade, Composabilidade, Extensibilidade, Evolutividade.** As
demais viram suporte, rebaixadas ou descartadas. Detalhe em [Características](../caracteristicas.md).

## Por que — e o que foi descartado

- **Escalabilidade / Performance → rebaixadas.** Carga previsível (dezenas–centenas por programa),
  sem pico público. A performance de mídia é local (presigned/CDN), não puxa a arquitetura.
- **Localização → rebaixada.** [RNF-5](../../requisitos.md#rnf-5) fixa PT-BR; sem N idiomas.
- **Reusabilidade → descartada.** Absorvida por Composabilidade (seria contar duas vezes).
- **Interoperabilidade → não-dirigente.** OpenAPI/mobile é contrato de **borda**, não molda a
  partição; vale como suporte.
- **Confiabilidade, Recuperabilidade, Confidencialidade, Acessibilidade → suporte.** Obrigatórias,
  mas não reformam a fronteira dos componentes.

## Consequências

- Empurra o corte **por domínio** (não técnico) — ver [ADR 2](0002-componentes.md).
- Segurança impõe um **ponto único de escopo** (`Scope`) que toda consulta atravessa.
- Configurabilidade impõe *builder-como-dado*; Composabilidade empurra o **motor reusado** (`Form`).
