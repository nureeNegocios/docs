# Convenção de escrita de requisitos

Requisito bem escrito é aquele que **todos os leitores interpretam do mesmo jeito** e que corresponde ao que o autor quis dizer. Linguagem natural pura é ambígua, vaga e difícil de testar; por isso adotamos uma **linguagem natural semi-estruturada**.

A base teórica é a taxonomia de templates de requisitos de Hnaini et al. (SoftEng 2023).[^1] Dela escolhemos, de forma pragmática:

- **EARS** (*Easy Approach to Requirements Syntax*, Mavin et al.) para o **enunciado** — mata ambiguidade com cinco padrões simples.
- Um **boilerplate de metadados** (inspirado em Volere/Nayak e no template de Mazo et al.) para a **rastreabilidade** — de onde veio, prioridade, como se verifica, de que depende.

[^1]: Hnaini, Mazo, Vallejo, Galindo, Champeau. *Taxonomy of Requirements Specification Templates.* SoftEng 2023. HAL-04105054.

## Formato do enunciado (EARS)

Todo requisito funcional segue **um** destes cinco padrões. A palavra-chave de obrigatoriedade é **"deve"** (equivalente a *shall*).

| Padrão | Quando usar | Forma |
|---|---|---|
| **Ubíquo** | comportamento sempre ativo | O `<sistema>` **deve** `<resposta>`. |
| **Dirigido a evento** | disparado por um gatilho | **Quando** `<gatilho>`, o `<sistema>` **deve** `<resposta>`. |
| **Dirigido a estado** | válido durante um estado | **Enquanto** `<estado>`, o `<sistema>` **deve** `<resposta>`. |
| **Comportamento indesejado** | tratar erro/exceção | **Se** `<condição>`, **então** o `<sistema>` **deve** `<resposta>`. |
| **Opcional** | só quando um recurso existe | **Onde** `<recurso presente>`, o `<sistema>` **deve** `<resposta>`. |

Padrões **complexos** combinam os acima (ex.: *Quando … e enquanto … o sistema deve …*). Prefira sempre o padrão mais simples que expresse a intenção.

!!! example "Exemplos"
    - **Ubíquo:** O sistema deve restringir o acesso a dados de um programa ao escopo do usuário.
    - **Evento:** Quando um participante envia uma resposta, o sistema deve registrá-la e notificar os módulos interessados.
    - **Estado:** Enquanto um ciclo estiver encerrado, o sistema deve impedir a criação de novas tarefas nele.
    - **Indesejado:** Se um usuário solicitar dados fora do seu escopo, então o sistema deve negar o acesso.
    - **Opcional:** Onde um formulário for do tipo avaliado, o sistema deve registrar gabarito e pontuação.

## Metadados (rastreabilidade)

Cada requisito carrega, além do enunciado:

| Campo | Descrição |
|---|---|
| **ID** | Identificador estável e único. Ver esquema abaixo. |
| **Prioridade** | `Alta` · `Média` · `Baixa` (MoSCoW: must / should / could). |
| **Fonte** | De onde o requisito veio. É a **rastreabilidade para cima**. Ex.: `Reunião 1`, `Inferência`, `Decisão de projeto`, `PRODUCT.md`. |
| **Verificação** | Critério observável que prova que o requisito foi atendido (torna o requisito testável). |
| **Depende de** | Outros requisitos pré-requisito (quando houver). |

## Esquema de IDs

- `RF-E<épico>.<n>` — **R**equisito **F**uncional, agrupado por épico. Ex.: `RF-E4.1`.
- `RNF-<n>` — **R**equisito **N**ão-**F**uncional (segurança, desempenho, privacidade, idioma…).
- `RC-<n>` — **R**estrição de projeto / **C**onstraint (decisões de arquitetura e tecnologia já tomadas).

IDs **não são reciclados**: se um requisito é removido, seu ID é aposentado, não reaproveitado. Isso mantém a rastreabilidade histórica.

## Rastreabilidade ponta a ponta

O ID é o fio que costura tudo:

```
Fonte (reunião, decisão)  ──►  Requisito (RF-…)  ──►  Design (domínio, banco, casos de uso)
                                     │
                                     ├──►  Implementação (commit / PR que cita o ID)
                                     └──►  Teste (caso de teste que cita o ID)
```

Regras práticas:

- **Commits e PRs** que implementam um requisito citam o ID na descrição (ex.: `implementa RF-E5.3`).
- **Testes** referenciam o(s) ID(s) que cobrem, no nome ou num comentário.
- **Issues** de trabalho nascem de um ou mais requisitos e os citam.

Assim, partindo de qualquer requisito, dá para navegar até a decisão que o originou e até o teste que o garante.
