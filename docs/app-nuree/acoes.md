---
hide:
  - navigation
---

# Ações do sistema — app-nuree

Mapa das ações por ator, na altitude de **capacidade** (não de tela nem de campo).
Detalhes implícitos ficam de fora de propósito: "entrar com Google" está contido em
**autenticar**, "salvar rascunho" em **responder formulário**, e assim por diante. O objetivo
é enxergar os **primeiros componentes** e as **características de arquitetura** que eles exigem.

Cada grupo aponta os [requisitos](requisitos.md) de origem. O agrupamento em componentes fica
para depois.

---

## Cliente

O participante matriculado num programa. Tudo que faz é preso ao seu escopo.

| Grupo | Ações | Requisitos |
|---|---|---|
| Acesso | Autenticar (código), sair | E1 |
| Tarefas | Criar, editar, mover de estado, excluir; gerir subtarefas; alternar visões (lista/kanban/calendário) | E3 |
| Formulários | Responder formulário atribuído (Mergulho, tarefa de casa, assessment/PDI, quiz) | E4 |
| Mentoria | Ver disponibilidade, agendar sessão, transferir a colega da empresa, ver a linha do tempo | E5 |
| Conteúdo | Consumir itens de trilha e marcar progresso | E6 |
| Jornada | Ver a própria jornada, concluir etapas | E7 |
| Selos | Ver os selos conquistados | E8 |
| Diagnóstico | Responder diagnóstico, ver o resultado de maturidade | E10 |
| Presença | Fazer check-in em evento (QR), baixar certificado | E11 |

**Não pode:** remarcar, cancelar, editar ou remover sessão/disponibilidade — essas são do
admin ([RF-E5.3](requisitos.md#rf-e5-3), [RF-E5.12](requisitos.md#rf-e5-12)).

---

## Admin

Opera e **configura os motores sem depender do dev**. Transita entre empresas/programas.

| Grupo | Ações | Requisitos |
|---|---|---|
| Tenancy | CRUD de empresas, usuários (papel, reenvio de acesso) e programas; matricular clientes com pacote; alternar contexto ativo | E1, E2 |
| Acompanhamento | Ver painéis por programa e por pessoa; clonar/reaplicar templates | E2 |
| Ciclos & tarefas | CRUD de ciclos, encerrar ciclo (rollover), templates de ciclo; mesmas ações de tarefa do cliente | E3 |
| Form builder | CRUD de formulários e campos; atribuir a contexto com prazo; configurar quiz (gabarito, pontuação) | E4 |
| Agenda do mentor | Publicar/gerir disponibilidade; confirmar sessão e registrar o combinado; cancelar sessão; conectar Google Calendar | E5 |
| Conteúdo | CRUD de trilhas e itens; gerir biblioteca de mídia | E6 |
| Jornada builder | Montar jornadas com etapas; anexar formulário/trilha/selo a cada etapa | E7 |
| Selos builder | Definir selos e seus critérios de concessão | E8 |
| Comunicação | CRUD de modelos de mensagem WhatsApp (gatilho, variáveis) | E9 |
| Diagnóstico | Montar diagnóstico por dimensão (escala 1–5); gerar plano de ação das dimensões fracas | E10 |
| Presença | Registrar eventos; exportar relatório de presença | E11 |

---

## Sistema

Reage a eventos e executa o trabalho que ninguém dispara à mão. É o que puxa o **event bus**,
o **worker** e as **integrações externas**.

| Grupo | Ações | Requisitos |
|---|---|---|
| Autorização | Autenticar, classificar papel, prender consultas ao escopo, expirar sessão, vincular identidade Google | E1 |
| Onboarding | Enviar convite ao criar usuário | E2 |
| Tarefas | Fazer rollover de tarefas ao encerrar ciclo | E3 |
| Formulários | Registrar resposta e emitir evento; pontuar quiz | E4 |
| Agenda | Programar lembretes; criar evento no Google Calendar; bloquear slots em conflito | E5 |
| Conteúdo | Servir mídia; nunca bloquear por não-conclusão | E6 |
| Jornada/Selos | Registrar conclusão de etapa; conceder selo ao cumprir critério | E7, E8 |
| Comunicação | Agendar e enviar WhatsApp, com status e retry | E9 |
| Diagnóstico | Calcular maturidade por dimensão e geral | E10 |
| Presença | Gerar certificado PDF ao cumprir carga | E11 |
| Dados | Soft delete com retenção; backups automáticos | RNF-8, RNF-9 |

---

## O que isso exige da arquitetura

Lendo os clusters, as características de arquitetura que se destacam:

- **Segurança / isolamento multi-tenant** — quase toda ação do Cliente é *preso ao escopo*;
  o Admin é a exceção. É o eixo dominante ([RF-E1.6](requisitos.md#rf-e1-6),
  [RNF-2](requisitos.md#rnf-2)) e justifica o `EscopoGuard` central.
- **Configurabilidade sem deploy** — metade das ações do Admin são *builders* (formulário,
  jornada, trilha, selo, mensagem, ciclo). O dado descreve a estrutura, não o código
  ([RNF-1](requisitos.md#rnf-1)).
- **Evolutividade / desacoplamento** — as ações do Sistema são reações a eventos, não chamadas
  diretas. Novos motores entram como consumidores do bus sem tocar os produtores
  ([RC-4](requisitos.md#rc-4)).
- **Confiabilidade do assíncrono** — WhatsApp, lembretes, certificado e rollover rodam fora do
  request; precisam de fila com retry e status observável ([RF-E9.3](requisitos.md#rf-e9-3)).
- **Interoperabilidade** — Google (OAuth + Calendar) e WhatsApp são fronteiras externas com
  falha e latência próprias; isolar atrás de adaptadores ([RC-5](requisitos.md#rc-5)).

> As demais características (performance, elasticidade) são secundárias aqui: a carga é de
> dezenas a centenas de usuários por programa, não de escala pública.
