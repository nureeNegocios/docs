---
hide:
  - navigation
---

# Características de arquitetura — app-nuree

As características ("-ilities") que **dirigem** o desenho, na ordem do processo
características → componentes → comunicação. Cada uma é ancorada num
[requisito](requisitos.md); as que não se sustentam estão listadas como **descartadas**,
de propósito, para não inflar a lista.

A disciplina aqui é a de Ford/Richards: **poucas dirigentes**. Marcar tudo como importante
é o mesmo que não priorizar nada.

---

## Dirigentes

O que realmente reforma o particionamento de componentes.

| Característica | Fonte | Por que dirige |
|---|---|---|
| **Segurança** (isolamento multi-tenant + autorização) | [RF-E1.6](requisitos.md#rf-e1-6), [RF-E1.7](requisitos.md#rf-e1-7), [RNF-2](requisitos.md#rnf-2), [RNF-3](requisitos.md#rnf-3), [RNF-4](requisitos.md#rnf-4) | Quase toda ação do Cliente é presa ao escopo. Impõe um ponto único de aplicação de escopo que todo componente atravessa. |
| **Configurabilidade** | [RNF-1](requisitos.md#rnf-1) | Admin cria formulário/jornada/trilha/selo sem deploy. Impõe o padrão definição → aplicação → instância (builder como dado). |
| **Composabilidade** (reuso + composição) | domínio (motor único de formulários; produtos compõem motores), [RNF-1](requisitos.md#rnf-1) | Diagnóstico reusa Formulários; a Jornada compõe formulário+trilha+selo; um produto é uma composição de funções. Empurra o corte **por domínio/função**. |
| **Evolutibilidade** *(acrescentada nesta rodada)* | os 4 produtos + crescimento da plataforma; [RNF-7](requisitos.md#rnf-7) | Adicionar uma **nova** capacidade (ex.: Documentos) barato, sem quebrar as existentes. |

As três primeiras foram travadas antes; **Evolutibilidade** foi percebida como implícita e
entra agora. Repara que Configurabilidade, Composabilidade e Evolutibilidade são **três faces
da mesma virtude — mudança barata**, em níveis diferentes:

- **Configurabilidade** — mudança por **dado**, sem código (novo formulário).
- **Composabilidade** — mudança por **recomposição** do que já existe (novo produto a partir dos motores).
- **Evolutibilidade** — mudança por **código novo** isolado (nova função inteira).

E a **Segurança** é a guarda que atravessa as três: por mais fácil que seja mudar, nada
pode vazar escopo.

---

## De suporte

Obrigatórias — o sistema tem de satisfazê-las — mas **não reformam** a fronteira dos
componentes. São baseline operacional, não critério de partição.

| Característica | Fonte | Observação |
|---|---|---|
| **Confiabilidade** (entrega assíncrona) | [RF-E9.3](requisitos.md#rf-e9-3), [RF-E5.7](requisitos.md#rf-e5-7) | WhatsApp/lembrete/PDF com status e retry. Justifica fila durável **numa fronteira** (Comunicação), não como estilo global. |
| **Recuperabilidade / Continuidade** | [RNF-8](requisitos.md#rnf-8) | Backup automático com cópia off-site. |
| **Confidencialidade** (dados sensíveis) | [RNF-4](requisitos.md#rnf-4), [RNF-9](requisitos.md#rnf-9) | Mergulho e diagnóstico; retenção e soft delete. Parente da Segurança, mantida à parte por causa da retenção. |
| **Acessibilidade** | [RNF-6](requisitos.md#rnf-6) | Contraste, foco, aria — escopo de frontend. |

---

## Rebaixadas (de propósito)

Otimizar isto aqui seria over-engineering.

| Característica | Motivo |
|---|---|
| **Escalabilidade / elasticidade** | Dezenas a centenas de usuários por programa, carga previsível, sem pico público. |
| **Performance** | Idem — **exceto entrega de mídia** (streaming de vídeo em Aprendizagem). Mas isso é problema de *servir blob* eficiente (range requests, CDN), local ao componente de Mídia — **não** puxa a arquitetura toda. |
| **Localização** | [RNF-5](requisitos.md#rnf-5) fixa tudo em PT-BR. É requisito, mas não pede flexibilidade estrutural (sem N idiomas). |

---

## Descartadas

Candidatas que apareceram na exploração e **não se sustentam** como característica própria —
registradas para não voltarem sem querer.

| Candidata | Por que sai |
|---|---|
| **Reusabilidade** e **Extensibilidade** (avulsas) | Absorvidas por **Composabilidade** e **Evolutibilidade**. Mantê-las separadas seria contar a mesma coisa três vezes. |
| **Interoperabilidade** (como dirigente) | OpenAPI/mobile ([RNF-7](requisitos.md#rnf-7)) e integrações externas ([RC-5](requisitos.md#rc-5)) são reais, mas **não moldam o particionamento** — são contrato de borda. Vale como suporte, não como dirigente. |

---

## Nota — evento não é arquitetura de evento

Alguns requisitos usam semântica de evento ("*quando* X *então* Y" — [RF-E4.5](requisitos.md#rf-e4-5),
[RF-E8.2](requisitos.md#rf-e8-2), [RF-E9.2](requisitos.md#rf-e9-2)). Isso é um **fato do
domínio**, não uma decisão de arquitetura orientada a eventos. Como escala e performance estão
rebaixadas, **nada nas características dirigentes exige** broker/mediator ou mensageria
assíncrona global. As reações são notificações de um salto (etapa conclui → concede selo), que
podem ser honradas com a coisa mais simples possível; o assíncrono durável só se paga na
fronteira de **Comunicação**, por Confiabilidade. Decisão local, não estilo.
