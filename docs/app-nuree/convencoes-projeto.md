# Convenções do projeto

!!! note "Fonte canônica"
    `AGENTS.md` no repositório app-nuree (carregado pelos agentes de código). Espelhado aqui para leitura centralizada.

O app está evoluindo de um app de tarefas (núcleo do **Nuree Gestão**) para uma plataforma multi-produto (Gestão · Pessoas · Lab · Pulse) de módulos compartilhados — ver [requisitos](requisitos.md) e a [reunião de discovery](../reunioes/discovery-produtos-2026-07-29.md). Simplicidade segue como requisito de produto: cada módulo nasce pequeno.

## Regras obrigatórias

- **Idioma**: código, rotas, copy e commits em português. Nomes de domínio em PT (`Tarefa`, `Subtarefa`, `Usuario`, `entrar`, `sair`).
- **Sem emojis** em lugar nenhum: UI, copy, commits, seeds. Ludicidade vem de tipografia, cor e ícones (lucide-react).
- **Tom da copy**: acolhedor, informal, minúsculas, metáforas leves de florescimento ("tarefinhas", "passinhos", "floresceram"). Nunca corporativo. Sem travessão em copy de UI.
- **Paleta** (tokens em `src/app/globals.css`): `navy` #081D35 (texto/estrutura), `verde` #1E8A70 (ação primária), `flor` #B62269 (destaque, o "ponto" do logo), `creme` #FAF9F5 (fundo). Não introduzir outras cores.
- **Fontes**: DM Sans (corpo, `font-sans`) + Fraunces itálica (acentos, `font-display`). Nada de Inter/Geist/Arial.
- **Stack**: front em Next.js App Router + Tailwind v4 (tema via `@theme` no CSS, sem tailwind.config). Dados via **API dedicada REST/OpenAPI (NestJS) self-hosted em VPS** + Prisma + Postgres. **Arquitetura em transição**: o código atual ainda usa Server Actions e está sendo migrado pra essa API. Ao mexer em código existente, siga o padrão local; ao criar backend novo, use a API.
- **Autorização**: tarefas e ciclos pertencem à `Empresa`; todo acesso filtra pelo `empresaId` (e, com o multi-produto, `programaId`) da sessão, exceto para `papel: ADMIN`. A checagem vive numa camada central (Server Action hoje; guard da API depois). Nunca confiar em ids vindos do cliente sem esse filtro.
- **Responsável**: toda tarefa tem um `responsavelId` (usuário da mesma empresa). Quem cria a tarefa vira responsável; troca-se no modal de detalhe. `/admin/tarefas?pessoa=` filtra por responsável.
- **Admin**: não existe registro aberto; contas e empresas são criadas pelo admin em `/admin`. Cada `Usuario` cliente pertence a uma `Empresa` e vê o quadro compartilhado dela; só o admin navega entre empresas (cookie `nuree_empresa`, que define o quadro que `/tarefas` mostra para ele).
- **Vocabulário de produto**: sprint = **ciclo** (período com início e fim), backlog = **sementeira** (tarefas com `cicloId` nulo). Encerrar um ciclo devolve o que não foi feito para a sementeira. Visões da página de tarefas: lista, kanban e calendário (`?ciclo=` e `?visao=`).
- **Escopo**: papéis (Admin, Cliente e papéis de programa) e novos módulos existem conforme os requisitos e o roadmap; cada módulo nasce pequeno. Não adicionar analytics, recorrência, anexos ou histórico fora do escopo definido sem pedido explícito.

## Rodar local

```
npm run db:up        # Postgres em localhost:5434 (docker)
npm run db:migrate   # migrations Prisma
npm run db:seed      # demo: gabi@nuree.com / florescer (cliente) e admin@nuree.com / florescer (admin)
npm run dev          # http://localhost:3002
```
