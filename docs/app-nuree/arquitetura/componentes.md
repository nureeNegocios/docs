---
hide:
  - navigation
---

# Componentes

Os módulos do app-nuree, suas ações e relações. Um único quantum (monólito particionado por
domínio), cada módulo **dono das suas tabelas** num Postgres único; nomes canônicos em inglês,
rotas/UI em PT ([RNF-5](../requisitos.md#rnf-5)). Estilo e porquê no
[diário](../../reunioes/exploracao-arquitetura-2026-08-02.md); diagramas em [Diagramas](diagramas.md).

## Módulos do núcleo

| Módulo | O que é · engloba | Ações | RF |
|---|---|---|---|
| **`Auth`** | Autenticação / identidade | autenticar (e-mail+código OTP, OAuth Google, vincular identidade); emissão/expiração de código; logout/expiração; papel (Admin/Cliente) | [E1.4](../requisitos.md#rf-e1-4), 1.5, 1.8–1.11, 1.4a |
| **`Account`** | Cadastro / tenancy · *Empresa·Usuário·Programa/Participação·Produto* | catálogo de Produtos; Programas (contratos isolados) e matrícula; CRUD empresas/usuários; alternar contexto; convite | [E1.1](../requisitos.md#rf-e1-1)–1.3, E2.1–2.3, 2.5, 2.8 |
| **`Form`** | Motor genérico de formulários | CRUD de formulários e campos; atribuir a contexto com prazo; responder (rascunho+envio); registrar resposta e emitir evento | [E4.1](../requisitos.md#rf-e4-1)–4.5 |
| **`Diagnostic`** | Diagnóstico de maturidade | montar por dimensão (escala 1–5, *reusa `Form`*); calcular maturidade e exibir; gerar plano → cria `Task` | [E10.1](../requisitos.md#rf-e10-1)–10.3 |
| **`Journey`** | Jornada · *encontros · presença* | montar jornadas com etapas; anexar `Form`/`Track`/selo; exibir progresso; registrar conclusão e emitir evento; presença por QR e relatório | [E7.1](../requisitos.md#rf-e7-1)–7.4, [E11.1](../requisitos.md#rf-e11-1)–11.2 |
| **`Track`** | Trilhas · *itens · quiz · progresso* | CRUD de trilhas com itens (texto/vídeo/leitura/quiz); quiz (gabarito/pontuação) sobre o motor; consumir e marcar progresso; nunca bloquear | [E6.1](../requisitos.md#rf-e6-1)–6.4, E4.6 |
| **`Recognition`** | Selos & Certificados · *critérios · concessão* | definir selos e critérios; conceder ao satisfazer o critério — artefato: selo silencioso *ou* certificado PDF; exibir | [E8.1](../requisitos.md#rf-e8-1)–8.3, E11.3 |
| **`Scheduling`** | Agenda · *disponibilidade · slots · sessões · sync · check-in de sessão* | publicar disponibilidade/slots; agendar sessão (limite do pacote); transferir; registrar combinado; sync Google (OAuth+evento); check-in da sessão (*reusa `Form`*) | [E5.1](../requisitos.md#rf-e5-1)–5.13 |
| **`Document`** | Repositório por empresa · *pastas · remetente* | armazenar/baixar docs no escopo (blob via `Media`); pastas; remetente (Nuree/cliente) | [E12.1](../requisitos.md#rf-e12-1)–12.5 |
| **`Task`** | Execução · *tarefas · ciclos · rollover · templates · sementeira* | CRUD de tarefas e subtarefas; visões lista/kanban/calendário; ciclos; encerrar com rollover; templates de ciclo | [E3.1](../requisitos.md#rf-e3-1)–3.12 |

## Assíncrono

| Módulo | O que é | Ações | RF |
|---|---|---|---|
| **`Messaging`** | Modelos + disparos WhatsApp | CRUD de modelos; agendar disparo no gatilho; enviar com status e retry | [E9.1](../requisitos.md#rf-e9-1)–9.3 |

Roda num **worker** (mesma imagem, `node worker`), que também executa os jobs de domínio: rollover
de `Task` ([E3.9](../requisitos.md#rf-e3-9)), PDF de `Recognition` ([E11.3](../requisitos.md#rf-e11-3)),
convite de `Account` ([E2.8](../requisitos.md#rf-e2-8)).

**E-mail transacional** (código OTP de `Auth` — [E1.4/1.5](../requisitos.md#rf-e1-4); convite e link de cadastro de `Account` — [E2.8](../requisitos.md#rf-e2-8)/[2.9](../requisitos.md#rf-e2-9)) sai por **Resend**, também pelo worker. `Messaging` cobre só WhatsApp.

## Infra e leitura (fora do domínio)

| | Papel | RF |
|---|---|---|
| **`Media`** | armazenar/servir blobs via presigned URL (MinIO) | [E6.2](../requisitos.md#rf-e6-2), E11.3 |
| **`Scope`** | JWT/RBAC, rate limit, CORS, validação; prende toda query ao escopo | [RNF-3](../requisitos.md#rnf-3), [E1.6](../requisitos.md#rf-e1-6)/1.7, [RNF-2](../requisitos.md#rnf-2) |
| **`Reporting`** | *read-model* dos painéis por Programa/Pessoa — só lê `Task`/`Journey`/`Scheduling`/`Recognition` | [E2.6](../requisitos.md#rf-e2-6) |

O hub de construtores ([E2.4](../requisitos.md#rf-e2-4)) é navegação de UI, sem backend próprio.

## Relações

Chamada síncrona direta ao service público do módulo-alvo, salvo as marcadas *(evento)* / *(fila)*.

| Origem → Destino | Semântica | RF |
|---|---|---|
| `Diagnostic` → `Task` | plano de ação vira tarefas | [E10.3](../requisitos.md#rf-e10-3) |
| `Diagnostic` → `Form` | reusa o motor | [E10.1](../requisitos.md#rf-e10-1) |
| `Scheduling` → `Form` | check-in reusa o motor | [E5.13](../requisitos.md#rf-e5-13) |
| `Track` → `Form` | quiz reusa o motor | [E4.6](../requisitos.md#rf-e4-6) |
| `Journey` → `Form`/`Track`/`Recognition` | anexa à etapa | [E7.2](../requisitos.md#rf-e7-2) |
| `Journey` → `Scheduling` | encontro agenda sessão | [E7.1](../requisitos.md#rf-e7-1) |
| `Track` → `Recognition` | conclusão concede selo *(evento)* | [E8.2](../requisitos.md#rf-e8-2) |
| `Journey` → `Recognition` | jornada concluída concede certificado *(evento)* | [E11.3](../requisitos.md#rf-e11-3) |
| `Journey`/`Scheduling` → `Messaging` | gatilho de encontro/sessão *(fila)* | [E9.2](../requisitos.md#rf-e9-2), [E5.7](../requisitos.md#rf-e5-7) |

`Form` é o de maior acoplamento de entrada (todos os verticais o reusam) → estável e genérico.
`Journey` é o de maior acoplamento de saída → orquestra por **evento**, não por chamada.

## Eventos de domínio

Reações coreografadas: o emissor **emite**, os interessados reagem. Internas (selo/certificado/
maturidade) = evento in-process síncrono, mesma transação; externas/temporais = fila (`Messaging`).
`Journey` emite, não chama; `Recognition` avalia os próprios critérios. Não é EDA — é um *dispatcher*
in-process.

| Evento | Emissor | Reagem |
|---|---|---|
| `RespostaEnviada` ([E4.5](../requisitos.md#rf-e4-5)) | `Form` | `Diagnostic` · `Track` · `Journey` |
| `TrilhaConcluida` ([E6.3](../requisitos.md#rf-e6-3)) | `Track` | `Journey` · `Recognition` |
| `EtapaConcluida` ([E7.4](../requisitos.md#rf-e7-4)) | `Journey` | `Recognition` |
| `JornadaConcluida` | `Journey` | `Recognition` (certificado) |
| `PresencaRegistrada` ([E11.1](../requisitos.md#rf-e11-1)) | `Journey` | `Journey` · `Recognition` |
| gatilho de encontro/sessão ([E9.2](../requisitos.md#rf-e9-2)/[E5.7](../requisitos.md#rf-e5-7)) | `Journey`/`Scheduling` | **fila** → `Messaging` |

## Relacionados

- [Características](caracteristicas.md) · [Fitness functions](fitness-functions.md) · [Diagramas](diagramas.md) · [Diário](../../reunioes/exploracao-arquitetura-2026-08-02.md)
