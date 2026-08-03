---
hide:
  - navigation
---

# Características de arquitetura

As *-ilities* que dirigem o desenho. Poucas dirigentes; o resto é rebaixado de propósito. Cada uma
ancorada num [requisito](../requisitos.md); a priorização está no
[diário](../../reunioes/exploracao-arquitetura-2026-08-02.md).

## Dirigentes

Reformam o particionamento.

| Característica | Fonte | Por que dirige |
|---|---|---|
| **Segurança** (multi-tenant) | [RF-E1.6](../requisitos.md#rf-e1-6)/[1.7](../requisitos.md#rf-e1-7), [RNF-2](../requisitos.md#rnf-2)–[4](../requisitos.md#rnf-4) | ponto único de escopo que toda consulta atravessa |
| **Configurabilidade** | [RNF-1](../requisitos.md#rnf-1) | builder sem deploy — definição → aplicação → instância |
| **Composabilidade** | [RNF-1](../requisitos.md#rnf-1) + domínio | motor reusado; jornada compõe formulário/trilha/selo; produto compõe funções |
| **Extensibilidade** | 4 produtos, [E12](../requisitos.md#rf-e12-1) | nova capacidade inteira barata, sem quebrar as existentes |
| **Evolutividade** | [RNF-7](../requisitos.md#rnf-7) | a arquitetura mudar com segurança ao longo do tempo |

Configurabilidade → Composabilidade → Extensibilidade são graus de "mudança barata" (por dado, por
recomposição, por código novo); Evolutividade é o guarda-chuva; Segurança atravessa todas.

## De suporte

Obrigatórias, mas não reformam a fronteira.

| Característica | Fonte | Observação |
|---|---|---|
| **Confiabilidade** (async) | [RF-E9.3](../requisitos.md#rf-e9-3), [RF-E5.7](../requisitos.md#rf-e5-7) | WhatsApp/lembrete com status e retry — fila durável numa fronteira |
| **Recuperabilidade** | [RNF-8](../requisitos.md#rnf-8) | backup automático com cópia off-site |
| **Confidencialidade** | [RNF-4](../requisitos.md#rnf-4), [RNF-9](../requisitos.md#rnf-9) | dados sensíveis (Mergulho/diagnóstico); retenção e soft delete |
| **Acessibilidade** | [RNF-6](../requisitos.md#rnf-6) | contraste, foco, aria — frontend |

## Rebaixadas (de propósito)

| Característica | Motivo |
|---|---|
| **Escalabilidade** | carga previsível (dezenas–centenas por programa), sem pico público |
| **Performance** | idem — exceto entrega de mídia (blob/CDN), local ao `Media` |
| **Localização** | [RNF-5](../requisitos.md#rnf-5) fixa PT-BR; sem N idiomas |

## Descartadas

| Candidata | Por que sai |
|---|---|
| **Reusabilidade** avulsa | absorvida por Composabilidade |
| **Interoperabilidade** como dirigente | contrato de borda ([RNF-7](../requisitos.md#rnf-7), [RC-5](../requisitos.md#rc-5)); não molda partição |

## Relacionados

- [Requisitos](../requisitos.md) · [Componentes](componentes.md) · [Diário](../../reunioes/exploracao-arquitetura-2026-08-02.md)
