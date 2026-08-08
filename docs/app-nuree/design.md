---
hide:
  - navigation
  - toc
---

# Nuree — Diretrizes de Design

Sistema visual da **Nuree Negócios** (consultoria em evolução para aceleradora; produtos Gestão, Pessoas, Lab, Pulse). Este documento é a fonte única das decisões de marca e UI. Arquivo Figma: `003 - NUREE`.

!!! note "Origem"
    Cópia do `DESIGN.md` do repositório **`nuree-brand-kit`**, onde vivem os assets-fonte (`logo/`, `motifs/`, `skill/nuree-art.md`). Referenciado pelo [Handoff de telas](iteration-1/handoff-telas.md); fora da navegação do site de propósito.

---

## 1. Essência da marca

A tensão central da Nuree é **rigor + cuidado**. Duas frases guiam tudo:

- **"Clarear o propósito e manter."**
- **"Ordem ao caos."** — a obra viva (pincelada pós-impressionista) revelada por cima de uma estrutura (grid de Fibonacci + espiral áurea).

A linha de design é **editorial-suíço com alma**: grid de Fibonacci visível, rail lateral, tipografia estrutural, e uma camada humana orgânica (arte de pincelada em duotone) que quebra o layout limpo. O acento é o **ponto magenta**.

---

## 2. Cores

### Marca (UI + arte)
| Nome | Hex | RGB | Uso |
|---|---|---|---|
| **Navy** | `#0D1422` | 13, 20, 34 | Texto, superfícies base, botões primários, ícones |
| **Magenta** | `#C23A7A` | 194, 58, 122 | Acento, estado ativo, "o ponto", ações destrutivas |
| **Green** | `#14574E` | 20, 87, 78 | Sucesso, acento intencional |
| **White** | `#FFFFFF` | 255, 255, 255 | Fundo principal |
| **Paper / creme** | `#F6F4EC` | 246, 244, 236 | Superfícies suaves, cards do kit |
| **Ink** | navy @ opacidade | — | Texto secundário (use navy com opacidade 0.4–0.85) |

**Hairlines / bordas:** navy com opacidade 0.06–0.16.

### Paleta estendida — SÓ na arte (nunca na UI)
Verdes `#0D2624 → #14574E → #29793F → #6BA98C → #B3D180`; ocre `#B8852A`; ouro `#E8BF54`; céu `deep #1F2E57` / `steel #476693` / `blue #7A99BD` / `pale #CCD1CC`. O magenta aparece na arte como papoulas / coroa do sol / espiral áurea.

---

## 3. Tipografia

- **Montserrat** — display e corpo (títulos, textos, botões).
- **Courier Prime** — apenas **labels, metadados, números e IDs**, sempre em CAIXA ALTA com `letter-spacing` ~8%. É o "mono" da marca.

Estilos de texto (Figma): `display · h1 · h2 · h3 · h4 · h5 · body-lg · body · body-sm · caption · button · label/mono`.

Regra: nunca digitar o wordmark `nuree.` como texto — usar sempre o vetor (`logo/`).

---

## 4. Espaçamento — escala Fibonacci

Tokens `nuree/space` fib-1…fib-8:

```
8 · 13 · 21 · 34 · 55 · 89 · 144 · 233
```

Padding, gaps e ritmo vertical saem dessa escala. Margens e colunas de grid seguem gaps de Fibonacci (55·89·144·233 a partir das bordas).

---

## 5. Raio de canto

Tokens `nuree/radius`: **sm = 6**, **md = 10**.

- **Sem pills** (nada totalmente arredondado).
- **Sem elipses decorativas.** Exceção: dots funcionais de status (8px) e o ponto magenta do wordmark. Estado ativo em navegação = **barra magenta**, não bolinha.

---

## 6. Grid & proporção áurea

Overlay de **grid de Fibonacci** (linhas navy @ ~8%) usado com leveza em telas. Sobre a arte orgânica, revela-se a ordem: quadrados de Fibonacci (hairline navy) + **espiral áurea em magenta**, com uma flor no olho da espiral. É o conceito "ordem ao caos".

---

## 7. Iconografia

- **Lucide**, como **SVG inline** (stroke 1.6–1.8, `stroke-linecap/join = round`).
- **Nunca emojis.**

---

## 8. Componentes (design system)

Fonte única na página `nuree / components` do Figma. Base: `button` (primary/secondary/green/magenta), `input`, `textarea`, `select`, `field`, `checkbox`, `radio`, `toggle`, `tab`, `breadcrumb`, `pagination-item`, `nav-item`, `header`, `footer`, `sidebar`, `marquee`, `alert`, `banner`, `tooltip`, `modal`, `empty-state`, `skeleton`, `rating`.

Console admin: `admin/sidebar`, `admin/topbar`, `admin/status`, `admin/icon-button`, `admin/search`, `admin/company-switcher`, `admin/card`.

Princípios: componentizar tudo e propagar via instâncias; editar sempre o master; reusar antes de criar.

---

## 9. Arte / motivos

**Jardim impressionista** (Van Gogh, Monet, Hassam) traduzido em **vetores de pincelada** na paleta Nuree — o contraste orgânico contra o layout limpo.

Princípio inegociável: **a forma nasce da pincelada** (dezenas/centenas de traços curvos em camadas de tom, do escuro ao claro). Nunca uma silhueta sólida por baixo. Para encorpar: mais pinceladas, não mais fill. Todo traço é curvo e com cap/join round.

Motivos: **papoula/bloom, oliveira/foliage, campo de trigo, cipreste, céu em redemoinho, sol** (coroa magenta), borboletas. Composição: compor uma paisagem com profundidade, não espalhar figurinhas.

A técnica completa (toolkit de código, receitas por motivo, gotchas) está em `skill/nuree-art.md`.

---

## 10. Voz & copy

- Interface, domínio, rotas e copy em **português** (PT-BR).
- Voz contida, sem frases-aplauso. Labels e metadados em mono (Courier) caixa alta.

---

## 11. Conteúdo deste kit

```
nuree-brand-kit/
├── DESIGN.md              (este arquivo)
├── logo/
│   ├── nuree-wordmark.svg / .png            (navy + ponto magenta)
│   └── nuree-wordmark-white.svg / .png      (para fundos escuros)
├── motifs/
│   ├── cypress.svg / .png
│   ├── sun.svg / .png
│   └── butterfly.svg / .png
└── skill/
    └── nuree-art.md        (técnica completa da arte)
```

SVGs são vetoriais e transparentes (ideais para escala/edição); PNGs a 3× para uso rápido em posts.
