---
hide:
  - navigation
---

# Requisitos — app-nuree

Formato, metadados e esquema de IDs em [convenção de escrita](../convencoes/requisitos.md).

---

## E1 — Login, papéis e acesso aos dados por empresa e programa

Alicerce multi-tenant: quem é quem e o que cada um enxerga. É a base de que todos os outros épicos dependem.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| <a id="rf-e1-1">RF-E1.1</a> | O sistema deve representar Produtos a partir de um catálogo fixo (Gestão, Pessoas, Lab, Pulse). | Sistema | Alta | — | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Cada produto existe e é selecionável ao criar um programa. |
| <a id="rf-e1-2">RF-E1.2</a> | O sistema deve permitir criar Programas como instância de um Produto para uma Empresa, com período opcional. | Admin | Alta | [RF-E1.1](#rf-e1-1) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar um programa vinculado a produto+empresa e recuperá-lo. |
| <a id="rf-e1-2a">RF-E1.2a</a> | O sistema deve permitir que uma mesma Empresa tenha mais de um contrato simultâneo — cada contrato é um Programa — com dados isolados entre eles. | Sistema | Alta | [RF-E1.2](#rf-e1-2) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Uma empresa com dois programas não vaza dados de um para o outro. |
| <a id="rf-e1-3">RF-E1.3</a> | O sistema deve vincular usuários a um Programa (Participação), com o pacote contratado. | Admin | Alta | [RF-E1.2](#rf-e1-2) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Matricular um cliente num programa com o seu pacote. |
| <a id="rf-e1-4">RF-E1.4</a> | O sistema deve autenticar usuários por e-mail e senha e manter a sessão. | Todos | Alta | — | AGENTS.md | Login válido concede sessão; inválido é recusado. |
| <a id="rf-e1-4a">RF-E1.4a</a> | O sistema deve classificar cada usuário por um papel de plataforma (Admin ou Cliente), que determina seu nível de acesso. | Sistema | Alta | [RF-E1.4](#rf-e1-4) | Inferência | Usuário Admin e usuário Cliente têm níveis de acesso distintos. |
| <a id="rf-e1-5">RF-E1.5</a> | Quando um usuário acessa pela primeira vez, o sistema deve exigir a troca de senha. | Todos | Média | [RF-E1.4](#rf-e1-4) | AGENTS.md | Primeiro login redireciona para troca de senha. |
| <a id="rf-e1-6">RF-E1.6</a> | O sistema deve restringir todo acesso a dados de um programa ao escopo (empresa/programa) do usuário. | Cliente | Alta | [RF-E1.4a](#rf-e1-4a) | AGENTS.md | Usuário de uma empresa não lê dados de outra. |
| <a id="rf-e1-7">RF-E1.7</a> | Se um usuário solicitar dados fora do seu escopo, então o sistema deve negar o acesso, exceto para o papel Admin. | Cliente | Alta | [RF-E1.6](#rf-e1-6) | AGENTS.md | Requisição fora de escopo retorna negação; Admin acessa. |
| <a id="rf-e1-8">RF-E1.8</a> | O sistema deve permitir a autenticação via conta Google (OAuth), de forma complementar ao login por e-mail e senha. | Todos | Média | [RF-E1.4](#rf-e1-4) | Decisão de projeto | Entrar com Google e, na mesma conta, entrar com e-mail e senha. |
| <a id="rf-e1-9">RF-E1.9</a> | Se um usuário autentica com Google e já existe conta com aquele e-mail, então o sistema deve vincular as identidades em vez de criar conta duplicada. | Todos | Média | [RF-E1.8](#rf-e1-8) | Decisão de projeto | Login Google com e-mail já cadastrado reutiliza a conta existente. |
| <a id="rf-e1-10">RF-E1.10</a> | O sistema deve permitir ao usuário encerrar a sessão (logout). | Todos | Média | [RF-E1.4](#rf-e1-4) | Inferência | Logout encerra a sessão e exige novo login. |
| <a id="rf-e1-11">RF-E1.11</a> | O sistema deve expirar a sessão após período de inatividade, exigindo novo login. | Sistema | Média | [RF-E1.4](#rf-e1-4) | Inferência | Após inatividade, o acesso pede login de novo. |

## E2 — Administrar empresas, usuários e programas

Painel onde o admin opera e **configura os motores sem depender do dev**.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| <a id="rf-e2-1">RF-E2.1</a> | O sistema deve permitir criar, editar e desativar Empresas. | Admin | Alta | [RF-E1.4a](#rf-e1-4a) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | CRUD completo de empresa (exclusão é soft — RNF-9). |
| <a id="rf-e2-2">RF-E2.2</a> | O sistema deve permitir criar, editar e desativar usuários, incluindo papel e reset de senha. | Admin | Alta | [RF-E1.4a](#rf-e1-4a) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar usuário, alterar papel, resetar senha, desativar. |
| <a id="rf-e2-3">RF-E2.3</a> | O sistema deve permitir criar Programas e matricular clientes. | Admin | Alta | [RF-E1.2](#rf-e1-2), [RF-E1.3](#rf-e1-3) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar programa e matricular pessoas gerando participações. |
| <a id="rf-e2-4">RF-E2.4</a> | O sistema deve oferecer um ponto único de acesso aos construtores (formulários, jornadas, trilhas, selos, réguas, templates de ciclo) e à biblioteca de mídia. | Admin | Alta | [RF-E1.4a](#rf-e1-4a) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | A partir do hub, alcançar cada construtor. |
| <a id="rf-e2-5">RF-E2.5</a> | Onde o admin possui múltiplas empresas/programas, o sistema deve permitir alternar o contexto ativo. | Admin | Alta | [RF-E1.2a](#rf-e1-2a) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Trocar o contexto muda o quadro exibido. |
| <a id="rf-e2-6">RF-E2.6</a> | O sistema deve oferecer painéis de acompanhamento por Programa e por Pessoa. | Admin | Média | [RF-E1.3](#rf-e1-3) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Ver as tarefas/sessões de uma pessoa agrupadas. |
| <a id="rf-e2-7">RF-E2.7</a> | O sistema deve permitir clonar ou reaplicar um template (formulário, jornada, trilha, selo) a outro programa sem recriá-lo. | Admin | Média | [RF-E2.4](#rf-e2-4) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Aplicar um mesmo formulário a dois programas. |
| <a id="rf-e2-8">RF-E2.8</a> | Quando o admin cria um usuário, o sistema deve enviar um e-mail de convite com o acesso inicial. | Sistema | Média | [RF-E2.2](#rf-e2-2) | Decisão de projeto | Criar usuário dispara e-mail de convite com acesso. |

## E3 — Organizar e acompanhar tarefas em ciclos

Base de acompanhamento (já em produção); passa a pertencer ao Programa. CRUD completo de tarefas e ciclos.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| <a id="rf-e3-1">RF-E3.1</a> | Quando um usuário cria uma tarefa, o sistema deve exigir o título; o responsável assume o criador e o status assume "a fazer" por padrão. | Cliente | Alta | [RF-E1.6](#rf-e1-6) | Existente | Criar tarefa só com título; responsável = criador, status = a fazer. |
| <a id="rf-e3-2">RF-E3.2</a> | O sistema deve permitir editar título, responsável, status, descrição, ciclo e prazo de uma tarefa. | Cliente | Alta | [RF-E3.1](#rf-e3-1) | Existente | Alterar cada campo e persistir. |
| <a id="rf-e3-3">RF-E3.3</a> | O sistema deve permitir excluir uma tarefa, removendo também suas subtarefas. | Cliente | Alta | [RF-E3.1](#rf-e3-1) | Inferência | Excluir tarefa a remove com suas subtarefas. |
| <a id="rf-e3-4">RF-E3.4</a> | O sistema deve permitir mover uma tarefa entre os estados a fazer, fazendo e feita. | Cliente | Alta | [RF-E3.1](#rf-e3-1) | Existente | Mover a tarefa pelos três estados. |
| <a id="rf-e3-5">RF-E3.5</a> | O sistema deve permitir adicionar, marcar como feita e remover subtarefas de uma tarefa. | Cliente | Média | [RF-E3.1](#rf-e3-1) | Existente | Adicionar subtarefa, marcá-la e removê-la. |
| <a id="rf-e3-6">RF-E3.6</a> | Quando o admin cria um ciclo, o sistema deve exigir nome, data de início e data de fim. | Admin | Alta | [RF-E1.4a](#rf-e1-4a) | Existente | Criar ciclo exige os três campos; só admin cria. |
| <a id="rf-e3-7">RF-E3.7</a> | O sistema deve permitir ao admin editar nome, data de início e data de fim de um ciclo. | Admin | Alta | [RF-E3.6](#rf-e3-6) | Inferência | Admin altera os campos do ciclo; cliente não. |
| <a id="rf-e3-8">RF-E3.8</a> | O sistema deve permitir ao admin excluir um ciclo, excluindo também suas tarefas e as subtarefas delas. | Admin | Média | [RF-E3.6](#rf-e3-6) | Decisão de projeto | Excluir um ciclo remove também suas tarefas e subtarefas; só admin. |
| <a id="rf-e3-9">RF-E3.9</a> | O sistema deve permitir ao admin encerrar um ciclo; as tarefas não concluídas passam automaticamente para o próximo ciclo (ou à sementeira, se não houver). | Admin | Alta | [RF-E3.6](#rf-e3-6) | Decisão de projeto | Encerrar move as tarefas abertas para o próximo ciclo. |
| <a id="rf-e3-10">RF-E3.10</a> | O sistema deve organizar as tarefas em ciclos e, quando sem ciclo, na sementeira. | Cliente | Alta | [RF-E3.1](#rf-e3-1), [RF-E3.6](#rf-e3-6) | Existente | Tarefa sem ciclo aparece na sementeira. |
| <a id="rf-e3-11">RF-E3.11</a> | O sistema deve exibir as tarefas em visões de lista, kanban e calendário. | Todos | Média | [RF-E3.1](#rf-e3-1) | Existente | Alternar entre as três visões. |
| <a id="rf-e3-12">RF-E3.12</a> | O sistema deve permitir definir templates de ciclo (ex.: Florescimento) e instanciá-los num programa, gerando ciclo e tarefas. | Admin | Alta | [RF-E3.6](#rf-e3-6), [RF-E2.4](#rf-e2-4) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Instanciar o template cria o ciclo com as tarefas do checklist. |

## E4 — Criar formulários e coletar respostas

Motor único de formulários, reusado por Mergulho, tarefas de casa, assessment/PDI e diagnóstico.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| <a id="rf-e4-1">RF-E4.1</a> | O sistema deve permitir criar, editar e excluir formulários com campos de tipos variados (texto curto, texto longo, escolha única, escolha múltipla, escala, número, data e upload). | Admin | Alta | [RF-E2.4](#rf-e2-4) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar um formulário com um campo de cada tipo e persistir. |
| <a id="rf-e4-2">RF-E4.2</a> | O sistema deve permitir configurar cada campo quanto a rótulo, obrigatoriedade, opções e ordem. | Admin | Alta | [RF-E4.1](#rf-e4-1) | Inferência | Reordenar campos e marcar obrigatoriedade. |
| <a id="rf-e4-3">RF-E4.3</a> | O sistema deve permitir atribuir um formulário a um contexto (programa, etapa de jornada ou participação), com prazo. | Admin | Alta | [RF-E4.1](#rf-e4-1), [RF-E1.2](#rf-e1-2) | Inferência | Atribuir o mesmo formulário a dois contextos distintos. |
| <a id="rf-e4-4">RF-E4.4</a> | O sistema deve permitir responder um formulário atribuído, salvando rascunho antes do envio. | Cliente | Alta | [RF-E4.3](#rf-e4-3), [RF-E1.3](#rf-e1-3) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Salvar rascunho, sair, retomar e enviar. |
| <a id="rf-e4-5">RF-E4.5</a> | Quando um cliente envia uma resposta, o sistema deve registrá-la e emitir um evento para os módulos interessados. | Sistema | Alta | [RF-E4.4](#rf-e4-4) | Inferência | Envio dispara o evento de resposta enviada. |
| <a id="rf-e4-6">RF-E4.6</a> | Onde um formulário for do tipo avaliado (quiz), o sistema deve registrar gabarito e pontuação e, opcionalmente, um tempo-limite. | Admin/Sistema | Média | [RF-E4.1](#rf-e4-1) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Responder um quiz produz pontuação conforme o gabarito. |

## E5 — Agendar e registrar sessões de mentoria

Auto-agendamento com regras + registro do que foi combinado. A gestão das sessões e da disponibilidade é do admin (o mentor).

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| <a id="rf-e5-1">RF-E5.1</a> | O sistema deve permitir ao admin publicar a disponibilidade de mentoria em slots finitos. | Admin | Alta | [RF-E1.2](#rf-e1-2) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Publicar slots que ficam visíveis para agendamento. |
| <a id="rf-e5-2">RF-E5.2</a> | O sistema deve permitir ao cliente agendar uma sessão num slot disponível, respeitando o limite do pacote. | Cliente | Alta | [RF-E5.1](#rf-e5-1), [RF-E1.3](#rf-e1-3) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Agendar até o limite; além dele é bloqueado. |
| <a id="rf-e5-3">RF-E5.3</a> | O sistema não deve permitir a remarcação de sessões. | Sistema | Alta | [RF-E5.2](#rf-e5-2) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Não há ação de remarcar uma sessão agendada. |
| <a id="rf-e5-4">RF-E5.4</a> | O sistema deve permitir ao cliente transferir uma sessão a um colega da mesma empresa, notificando os envolvidos. | Cliente | Média | [RF-E5.2](#rf-e5-2) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Transferir a sessão para colega da mesma empresa; terceiros não. |
| <a id="rf-e5-5">RF-E5.5</a> | O sistema deve exibir ao cliente a linha do tempo das suas sessões. | Cliente | Média | [RF-E5.2](#rf-e5-2) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Ver 1ª, 2ª, 3ª sessão com datas e estado. |
| <a id="rf-e5-6">RF-E5.6</a> | Quando ocorre uma sessão, o sistema deve permitir ao admin confirmar a realização e registrar o combinado (ex.: fez mais / fez menos). | Admin | Alta | [RF-E5.2](#rf-e5-2) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Confirmar a sessão e gravar o combinado. |
| <a id="rf-e5-7">RF-E5.7</a> | Quando uma sessão é agendada, o sistema deve programar as mensagens de lembrete correspondentes. | Sistema | Média | [RF-E5.2](#rf-e5-2), [RF-E9.1](#rf-e9-1) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Agendamento cria disparos de lembrete (ver E9). |
| <a id="rf-e5-8">RF-E5.8</a> | O sistema deve permitir ao admin conectar a conta Google (Calendar) do mentor por OAuth. | Admin | Média | [RF-E1.4a](#rf-e1-4a) | Decisão de projeto | Conectar o Google e o vínculo persiste. |
| <a id="rf-e5-9">RF-E5.9</a> | Onde a agenda Google estiver conectada e uma sessão for agendada, o sistema deve criar o evento correspondente no Google Calendar do mentor e do cliente, com convite. | Sistema | Média | [RF-E5.2](#rf-e5-2), [RF-E5.8](#rf-e5-8) | Decisão de projeto | Com Google conectado, agendar cria o evento e envia convite. |
| <a id="rf-e5-10">RF-E5.10</a> | Enquanto a agenda Google estiver conectada, o sistema deve considerar seus horários ocupados para bloquear slots em conflito. | Sistema | Média | [RF-E5.8](#rf-e5-8), [RF-E5.1](#rf-e5-1) | Decisão de projeto | Horário ocupado no Google não aparece como slot disponível. |
| <a id="rf-e5-11">RF-E5.11</a> | O sistema deve permitir ao admin cancelar ou remover sessões e criar, editar ou remover a disponibilidade. | Admin | Média | [RF-E5.1](#rf-e5-1) | Decisão de projeto | Admin cancela sessão e gerencia os slots. |
| <a id="rf-e5-12">RF-E5.12</a> | Se um cliente tentar cancelar, editar ou remover uma sessão ou a disponibilidade, então o sistema deve negar. | Cliente | Média | [RF-E5.11](#rf-e5-11) | Decisão de projeto | Cliente não cancela/edita/remove sessão nem slot. |
| <a id="rf-e5-13">RF-E5.13</a> | O sistema deve permitir ao cliente responder um check-in (formulário) vinculado a uma sessão online, com estrutura própria distinta do check-in presencial de eventos. | Cliente | Média | [RF-E5.2](#rf-e5-2), [RF-E4.3](#rf-e4-3) | [Reunião de exploração de arquitetura 02/08](../reunioes/exploracao-arquitetura-2026-08-02.md) | Responder um check-in atrelado a uma sessão agendada. |

## E6 — Publicar conteúdo e trilhas de reforço

Biblioteca de reforço de aprendizagem, montada pelo admin.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| <a id="rf-e6-1">RF-E6.1</a> | O sistema deve permitir criar, editar e excluir trilhas compostas por itens ordenados (pílula de texto, vídeo, leitura, quiz — via RF-E4.6). | Admin | Média | [RF-E2.4](#rf-e2-4), [RF-E4.6](#rf-e4-6) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Montar uma trilha com um item de cada tipo. |
| <a id="rf-e6-2">RF-E6.2</a> | O sistema deve armazenar e servir a mídia dos itens de trilha. | Sistema | Média | [RF-E6.1](#rf-e6-1) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Subir um vídeo e reproduzi-lo no item. |
| <a id="rf-e6-3">RF-E6.3</a> | O sistema deve permitir ao cliente consumir os itens e marcar o progresso. | Cliente | Média | [RF-E6.1](#rf-e6-1), [RF-E1.3](#rf-e1-3) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Marcar item como concluído e ver o progresso. |
| <a id="rf-e6-4">RF-E6.4</a> | O sistema não deve bloquear conteúdo por não-conclusão (reforço, não vigilância). | Sistema | Média | [RF-E6.3](#rf-e6-3) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Todos os itens acessíveis independentemente do progresso. |

## E7 — Montar e exibir a jornada do participante

Espinha que amarra encontros, formulários, trilhas e selos numa linha do tempo. Também um construtor.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| <a id="rf-e7-1">RF-E7.1</a> | O sistema deve permitir montar, editar e excluir jornadas com etapas ordenadas (marco, encontro, formatura), cada uma com data ou offset. | Admin | Média | [RF-E2.4](#rf-e2-4) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar jornada com marco zero, encontros e formatura. |
| <a id="rf-e7-2">RF-E7.2</a> | O sistema deve permitir anexar a uma etapa um formulário, uma trilha e/ou um selo. | Admin | Média | [RF-E7.1](#rf-e7-1), [RF-E4.1](#rf-e4-1), [RF-E6.1](#rf-e6-1), [RF-E8.1](#rf-e8-1) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Anexar os três a uma etapa. |
| <a id="rf-e7-3">RF-E7.3</a> | O sistema deve exibir ao cliente a sua jornada e o progresso nela. | Cliente | Média | [RF-E7.1](#rf-e7-1), [RF-E1.3](#rf-e1-3) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Cliente vê a linha do tempo e onde está. |
| <a id="rf-e7-4">RF-E7.4</a> | Quando o cliente conclui uma etapa, o sistema deve registrar a conclusão e emitir um evento. | Sistema | Média | [RF-E7.3](#rf-e7-3) | Inferência | Concluir etapa dispara o evento de etapa concluída. |

## E8 — Conceder selos por progresso (gamificação)

Selos por pilar/competência, silenciosos e editoriais.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| <a id="rf-e8-1">RF-E8.1</a> | O sistema deve permitir definir, editar e excluir selos (por pilar ou competência) e seus critérios de concessão. | Admin | Média | [RF-E2.4](#rf-e2-4) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar selo com critério vinculado a uma etapa/trilha. |
| <a id="rf-e8-2">RF-E8.2</a> | Quando um critério de selo é satisfeito, o sistema deve conceder o selo ao cliente. | Sistema | Média | [RF-E8.1](#rf-e8-1), [RF-E7.4](#rf-e7-4) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Cumprir o critério concede o selo automaticamente. |
| <a id="rf-e8-3">RF-E8.3</a> | O sistema deve exibir os selos do cliente em estética silenciosa, sem ranking, streak ou confete. | Cliente | Média | [RF-E8.2](#rf-e8-2) | PRODUCT.md | Tela de selos sem elementos competitivos. |

## E9 — Enviar mensagens automáticas por WhatsApp

Mensagens automáticas antes e depois de cada encontro.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| <a id="rf-e9-1">RF-E9.1</a> | O sistema deve permitir criar, editar e excluir modelos de mensagem com gatilho, canal WhatsApp e variáveis. | Admin | Média | [RF-E2.4](#rf-e2-4) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar modelo com variáveis e gatilho. |
| <a id="rf-e9-2">RF-E9.2</a> | Quando um gatilho ocorre (antes/depois de encontro, ou X horas antes de uma sessão), o sistema deve agendar o disparo. | Sistema | Média | [RF-E9.1](#rf-e9-1), [RF-E7.1](#rf-e7-1) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Gatilho cria um disparo agendado para o momento certo. |
| <a id="rf-e9-3">RF-E9.3</a> | O sistema deve enviar as mensagens via WhatsApp e registrar o status (agendado, enviado, falhou), com novas tentativas em caso de falha. | Sistema | Média | [RF-E9.2](#rf-e9-2) | Decisão de projeto | Disparo enviado atualiza status; falha gera retry. |

## E10 — Diagnosticar áreas da empresa e gerar planos de ação

Diagnóstico de maturidade de qualquer área da empresa (RH no Nuree Pessoas, gestão/OKR no Nuree Gestão, etc.) + planos de ação. Em grande parte, composição de E4 (formulário) + E3 (tarefas).

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| <a id="rf-e10-1">RF-E10.1</a> | O sistema deve permitir montar um diagnóstico organizando as perguntas por dimensão/área, respondidas numa escala de maturidade de 1 a 5. | Admin | Média | [RF-E4.1](#rf-e4-1) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Montar diagnóstico com dimensões e perguntas em escala de 1 a 5. |
| <a id="rf-e10-2">RF-E10.2</a> | O sistema deve calcular a maturidade por dimensão e a geral a partir das respostas e exibir o resultado. | Sistema | Média | [RF-E10.1](#rf-e10-1), [RF-E4.4](#rf-e4-4) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Ver a maturidade por dimensão e a geral após as respostas. |
| <a id="rf-e10-3">RF-E10.3</a> | O sistema deve permitir gerar o plano de ação a partir das dimensões de menor maturidade, com itens acompanhados como tarefas. | Admin | Média | [RF-E10.2](#rf-e10-2), [RF-E3.1](#rf-e3-1) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Gerar plano a partir das dimensões fracas e acompanhar os itens como tarefas. |

## E11 — Registrar presença em eventos e emitir certificados

Para formações presenciais. Sem pagamento na plataforma.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| <a id="rf-e11-1">RF-E11.1</a> | O sistema deve registrar a presença do cliente em eventos por check-in (QR/scan), associando a participação. | Cliente | Média | [RF-E1.3](#rf-e1-3) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Escanear registra a presença do cliente no evento. |
| <a id="rf-e11-2">RF-E11.2</a> | O sistema deve gerar um relatório de presença exportável para o cliente. | Admin | Média | [RF-E11.1](#rf-e11-1) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Exportar a lista de presença de um evento. |
| <a id="rf-e11-3">RF-E11.3</a> | Quando um cliente conclui a jornada (cumpre os critérios de conclusão — presença em eventos, trilhas e quizzes concluídos), o sistema deve conceder um certificado em PDF, pelo mesmo mecanismo de concessão dos selos. | Sistema | Baixa | [RF-E11.1](#rf-e11-1), [RF-E7.4](#rf-e7-4), [RF-E8.2](#rf-e8-2) | [Reunião de exploração de arquitetura 02/08](../reunioes/exploracao-arquitetura-2026-08-02.md) | Concluir a jornada concede o certificado em PDF. |

## E12 — Trocar documentos por empresa

Repositório de arquivos por empresa, tipo mini-drive: pastas e envio/recebimento com o remetente visível (Nuree ou empresa cliente). Levantado na exploração de arquitetura.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| <a id="rf-e12-1">RF-E12.1</a> | O sistema deve armazenar documentos vinculados a uma Empresa, organizados em pastas com hierarquia. | Todos | Média | [RF-E1.6](#rf-e1-6) | Decisão de projeto | Criar pasta e guardar um documento nela. |
| <a id="rf-e12-2">RF-E12.2</a> | O sistema deve permitir enviar e baixar documentos dentro do escopo da empresa. | Todos | Média | [RF-E12.1](#rf-e12-1) | Decisão de projeto | Upload e download de um documento. |
| <a id="rf-e12-3">RF-E12.3</a> | O sistema deve registrar e exibir o remetente de cada documento, distinguindo se foi enviado pela Nuree (Admin) ou pela empresa cliente. | Sistema | Média | [RF-E12.2](#rf-e12-2) | Decisão de projeto | Um documento exibe quem enviou (Nuree/cliente). |
| <a id="rf-e12-4">RF-E12.4</a> | O sistema deve permitir criar, renomear e remover pastas. | Todos | Baixa | [RF-E12.1](#rf-e12-1) | Decisão de projeto | CRUD de pastas dentro da empresa. |
| <a id="rf-e12-5">RF-E12.5</a> | O sistema deve restringir o acesso aos documentos ao escopo da empresa/programa. | Sistema | Alta | [RF-E1.6](#rf-e1-6) | Decisão de projeto | Empresa não acessa documentos de outra. |

---

## Requisitos não-funcionais

Aplicam-se ao sistema como um todo (sem ator específico).

| ID | Requisito | Prioridade | Fonte | Verificação |
|---|---|---|---|---|
| <a id="rnf-1">RNF-1</a> | Funcionalidades de estrutura e conteúdo devem ser configuráveis pelo admin sem novo deploy. | Alta | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar formulário/jornada/trilha novos sem alterar código. |
| <a id="rnf-2">RNF-2</a> | O sistema deve isolar os dados entre empresas/programas em toda consulta. | Alta | AGENTS.md | Nenhuma consulta retorna dados fora do escopo. |
| <a id="rnf-3">RNF-3</a> | O sistema deve proteger o acesso com JWT (access + refresh), RBAC, rate limiting, CORS restrito e validação de entrada. | Alta | Decisão de projeto | Testes de autenticação, autorização e limites de borda. |
| <a id="rnf-4">RNF-4</a> | O sistema deve tratar dados de Mergulho e diagnóstico como sensíveis, com acesso restrito por escopo e política de retenção. | Alta | Inferência | Dados sensíveis só acessíveis ao escopo autorizado. |
| <a id="rnf-5">RNF-5</a> | A interface, o domínio, as rotas e a copy devem estar em português. | Alta | AGENTS.md | Revisão de idioma. |
| <a id="rnf-6">RNF-6</a> | A interface deve respeitar contraste, foco visível, prefers-reduced-motion e rótulos aria em português. | Média | PRODUCT.md | Auditoria de acessibilidade. |
| <a id="rnf-7">RNF-7</a> | A API deve expor um contrato REST/OpenAPI estável, apto a servir um futuro app mobile. | Média | Decisão de projeto | Spec OpenAPI publicada e versionada. |
| <a id="rnf-8">RNF-8</a> | O sistema deve ter backups automáticos do banco e da mídia, com cópia off-site. | Alta | Decisão de projeto | Restauração de backup validada. |
| <a id="rnf-9">RNF-9</a> | Toda exclusão/desativação é soft delete: o registro é marcado como excluído (isDeleted) e retido por ~3 meses antes do hard delete. | Alta | Decisão de projeto | Registro "excluído" some da UI mas persiste no banco; hard delete após ~3 meses. |

## Restrições de projeto

| ID | Restrição | Fonte |
|---|---|---|
| <a id="rc-1">RC-1</a> | O backend é uma API dedicada NestJS (REST/OpenAPI), substituindo as Server Actions. | Decisão de projeto |
| <a id="rc-2">RC-2</a> | Infra self-hosted em VPS via Docker Compose: Postgres + Prisma, Redis + BullMQ, MinIO (S3), Caddy. | Decisão de projeto |
| <a id="rc-3">RC-3</a> | Sem BaaS e sem processamento de pagamentos na plataforma. | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) / Decisão de projeto |
| <a id="rc-4">RC-4</a> | Módulos se comunicam por event bus de domínio; trabalho assíncrono roda em worker. | Decisão de projeto |
| <a id="rc-5">RC-5</a> | Integrações externas: WhatsApp (régua) e Google (login OAuth + Calendar). | Decisão de projeto |
</content>
</invoke>
