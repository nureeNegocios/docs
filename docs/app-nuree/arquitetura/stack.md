---
hide:
  - navigation
---

# Stack

As tecnologias do app-nuree, alinhadas à arquitetura (1 quantum, módulos por domínio). Restrições
de origem em [RC-1](../requisitos.md#rc-1)–[RC-5](../requisitos.md#rc-5); o porquê no
[diário](../../reunioes/exploracao-arquitetura-2026-08-02.md).

## Por camada

| Camada | Escolha | Nota |
|---|---|---|
| **Frontend** | Next.js | consome a API REST (sem Server Actions); PT-BR e acessibilidade ([RNF-5](../requisitos.md#rnf-5)/[6](../requisitos.md#rnf-6)) |
| **API** | NestJS (REST/OpenAPI) | `@Module` = módulo de domínio; `@nestjs/swagger` para o contrato ([RNF-7](../requisitos.md#rnf-7)) |
| **Worker** | mesma imagem NestJS (`node worker`) | 1 quantum, 2 processos |
| **Eventos** | `@nestjs/event-emitter` | dispatcher in-process (reações internas) |
| **DB** | Postgres + **Prisma** | único, tabelas por módulo (ver abaixo) |
| **Fila / cache** | Redis + BullMQ | delayed jobs (lembretes) e retry/status |
| **Blob** | MinIO (S3) | presigned URL — o `Media` não serve blob |
| **Proxy / TLS** | Caddy | — |
| **Deploy** | Docker Compose | k8s de reserva se uma fronteira crescer |
| **Auth** | JWT (access+refresh) + Google OAuth (Passport) | [RNF-3](../requisitos.md#rnf-3), [RC-5](../requisitos.md#rc-5) |
| **Validação** | class-validator / class-transformer | validação de entrada ([RNF-3](../requisitos.md#rnf-3)) |
| **Integrações** | WhatsApp *(provider a decidir)*, Google (OAuth + Calendar) | isoladas atrás de `Messaging` / `Scheduling` |
| **Backup** | `pg_dump` off-site + MinIO | [RNF-8](../requisitos.md#rnf-8) |
| **CI** | GitHub Actions | roda build e as fitness functions |

## Prisma + repositories

O Prisma tem schema/client central — não dá isolamento físico por módulo. A fronteira é **lógica**:

- Cada módulo tem um **repository** que é o **único** ponto a tocar as tabelas daquele módulo.
- Nenhum service acessa o `PrismaClient` para tabelas de outro módulo — só via a API pública dele.
- A [FF-2](fitness-functions.md) barra o acesso cruzado no build (uso do client cru fora do
  repository; referência a modelos de outro módulo).

## Fitness functions — ferramentas

`dependency-cruiser` / `ts-arch` (regras de módulo, FF-2/3), `Jest` + `supertest` (multi-tenant,
FF-1), rodando no CI.

## A decidir

- **Provider de WhatsApp:** Meta Cloud API (direto) vs BSP (Twilio/360dialog) — custo e aprovação
  de templates.

## Relacionados

- [Componentes](componentes.md) · [Características](caracteristicas.md) · [Fitness functions](fitness-functions.md) · [Requisitos](../requisitos.md)
