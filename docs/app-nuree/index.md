# app-nuree

Sistema da Nuree Negócios. Plataforma multi-produto composta de **módulos compartilhados**, sem misturar as bases de cada cliente. Quatro produtos:

- **Nuree Gestão** — implantação de gestão ágil e sprints (o núcleo já em produção).
- **Nuree Pessoas** — implantação de RH e gestão de pessoas (diagnóstico + planos de ação).
- **Nuree Lab** — desenvolvimento de autônomos (jornada Mergulho → Florescimento).
- **Nuree Pulse** — formação e mentoria de lideranças (prioridade de produto).

## Nesta seção

- **[Requisitos](requisitos.md)** — especificação funcional e não-funcional, em formato EARS com rastreabilidade.
- **[Características](arquitetura/caracteristicas.md)** — as *-ilities* dirigentes que reformam o desenho.
- **[Componentes](arquitetura/componentes.md)** — os módulos, ações, relações e eventos de domínio (fonte da verdade).
- **[Fitness functions](arquitetura/fitness-functions.md)** — testes de arquitetura que protegem as decisões.
- **[Diagramas](arquitetura/diagramas.md)** — acoplamento e topologia atuais (+ os anti-patterns preservados).

## Stack

| Camada | Escolha |
|---|---|
| Frontend | Next.js (consome a API REST) |
| API + worker | NestJS (mesma imagem, modo `api`/`worker`) |
| DB | Postgres + Prisma (tabelas por módulo via repositories) |
| Fila / cache | Redis + BullMQ |
| Blob | MinIO (presigned URL) |
| Proxy / deploy | Caddy · Docker Compose |
| Auth | JWT (access+refresh) + Google OAuth |
| E-mail | Resend (transacional: código OTP, convite, link de cadastro) |
| Integrações | WhatsApp *(provider a decidir)* · Google (OAuth + Calendar) · Resend (e-mail) |

## Em breve

Conforme polimos o desenho antes de implementar, entram aqui:

- Desenho da API REST (recursos, JWT, OAuth, convite)
- Diagramas de estado (ex.: ciclo de vida da sessão, do ciclo, da tarefa)
- Fluxos-chave (diagramas de sequência)
