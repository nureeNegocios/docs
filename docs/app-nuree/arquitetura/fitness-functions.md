---
hide:
  - navigation
---

# Fitness functions

Testes de arquitetura que **protegem** as decisões (o CI roda `mkdocs`/lint em *strict*). Cada um
existe para que uma decisão não se degrade em silêncio. Tipos: *static* (sem executar) ou *dynamic*;
*triggered* (CI/PR) ou *continuous* (produção).

## FF-1 · Isolamento multi-tenant

Protege a Segurança ([RNF-2](../requisitos.md#rnf-2), [E1.6](../requisitos.md#rf-e1-6)/[1.7](../requisitos.md#rf-e1-7)).

- *Static:* proíbe query ao ORM fora da camada de escopo (todo acesso injeta o filtro de tenant).
- *Dynamic:* autenticado como tenant A, cada endpoint nega dados de B (Admin é a exceção).
- *Continuous:* alerta em query sem cláusula de tenant.

## FF-2 · Dependência entre módulos

Protege o quantum coeso — cada módulo dono das suas tabelas.

- Um módulo só acessa as **próprias tabelas**; dado de outro só via a API pública dele.
- `Messaging` e `Recognition` **não são importados** por outros — `Journey` **emite/enfileira**;
  falha o build se `journey/*` importa esses módulos (garante que não vira orquestrador).

## FF-3 · `Form` estável e genérico

Protege a Composabilidade — `Form` é afferent alto.

- `Form` não importa nenhum vertical (`Track`/`Diagnostic`/`Scheduling`/`Journey`).
- Mudança no contrato público de `Form` exige revisão explícita (CODEOWNERS + teste de contrato).

## FF-4 · `Media` só por presigned URL

Protege a decisão de não servir blob no backend.

- Nenhum handler retorna o corpo de um arquivo — só URL pré-assinada (com expiração).

## FF-5 · Integrações e jobs fora do request

Protege a Confiabilidade — async só no worker.

- *Static:* proíbe chamar WhatsApp / disparar jobs pesados (PDF, rollover) em handler de request —
  só enfileirar.
- *Dynamic:* ao concluir etapa/agendar sessão, o handler **enfileira** e não chama a integração no
  caminho síncrono.

## FF-6 · Eventos de domínio sem ciclo

Protege o eixo de conclusão coreografado.

- O grafo evento → listener → evento é **acíclico**; nenhum listener reentra na própria cadeia.
- Handler de evento interno não chama integração externa direto — enfileira.

## FF-7 · Convenção de idioma

Protege o [RNF-5](../requisitos.md#rnf-5): rotas/UI em PT, código em EN.

- Rotas expostas em português; identificadores de módulo em inglês (os nomes canônicos).

## FF-8 · Complexidade ciclomática

Protege a **Evolutividade** ([RNF-7](../requisitos.md#rnf-7)) — a única característica dirigente que nenhuma das outras FF alcança, porque ela não é sobre fronteira entre módulos, e sim sobre o que acontece dentro de uma função.

- *Static, triggered:* nenhuma função passa de **10** de complexidade ciclomática. A medição é a regra `complexity` do ESLint, no `pnpm lint`.
- *Continuous:* `pnpm complexity` reporta a distribuição e as dez mais complexas. O teto sozinho não mostra a codebase inteira subindo de 3 para 8 — passa no lint e mesmo assim ficou pior.
- Um teste em [`src/fitness-functions.spec.ts`](https://github.com/nureeNegocios/api/blob/main/src/fitness-functions.spec.ts) garante que a regra e o script continuam configurados: tirá-los abriria a porteira em silêncio.

**Por que 10, e por que aqui.** Nenhuma decisão de arquitetura impede uma função de virar um nó de ramificações — o quantum continua coeso, os módulos continuam donos das próprias tabelas, e a mudança fica cara mesmo assim. Dez é o valor de referência usual, e hoje é encostado por três funções: a próxima ramificação nelas obriga a separar, que é o efeito pretendido.

A métrica conta `??`, `?:`, `||` e `&&`, então um montador de filtros com muitos campos opcionais pontua alto sem ser difícil de ler. Quando isso acontecer, o caminho é **dar nome ao trecho** — extrair a regra que estava implícita —, não silenciar a regra com um comentário de exceção.

## Relacionados

- [Componentes](componentes.md) · [Características](caracteristicas.md) · [Diário](../../reunioes/exploracao-arquitetura-2026-08-02.md)
