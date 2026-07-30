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
| <a id="rf-e1-4">RF-E1.4</a> | O sistema deve autenticar usuários por e-mail e senha e manter a sessão. | Todos | Alta | — | AGENTS.md | Login válido concede sessão; inválido é recusado. |
| <a id="rf-e1-4a">RF-E1.4a</a> | O sistema deve classificar cada usuário por um papel de plataforma (Admin ou Cliente), que determina seu nível de acesso. | Sistema | Alta | [RF-E1.4](#rf-e1-4) | Inferência | Usuário Admin e usuário Cliente têm níveis de acesso distintos. |
| <a id="rf-e1-3">RF-E1.3</a> | O sistema deve vincular usuários a um Programa (Participação) com um papel de programa (participante, mentor, gestor) e o pacote contratado. | Admin | Alta | [RF-E1.2](#rf-e1-2), [RF-E1.4a](#rf-e1-4a) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Matricular um usuário como mentor e outro como participante no mesmo programa. |
| <a id="rf-e1-5">RF-E1.5</a> | Quando um usuário acessa pela primeira vez, o sistema deve exigir a troca de senha. | Todos | Média | [RF-E1.4](#rf-e1-4) | AGENTS.md | Primeiro login redireciona para troca de senha. |
| <a id="rf-e1-6">RF-E1.6</a> | O sistema deve restringir todo acesso a dados de um programa ao escopo (empresa/programa) do usuário. | Cliente | Alta | [RF-E1.4a](#rf-e1-4a) | AGENTS.md | Usuário de uma empresa não lê dados de outra. |
| <a id="rf-e1-7">RF-E1.7</a> | Se um usuário solicitar dados fora do seu escopo, então o sistema deve negar o acesso, exceto para o papel Admin. | Cliente | Alta | [RF-E1.6](#rf-e1-6) | AGENTS.md | Requisição fora de escopo retorna negação; Admin acessa. |
| <a id="rf-e1-8">RF-E1.8</a> | O sistema deve permitir a autenticação via conta Google (OAuth), de forma complementar ao login por e-mail e senha. | Todos | Média | [RF-E1.4](#rf-e1-4) | Decisão de projeto | Entrar com Google e, na mesma conta, entrar com e-mail e senha. |
| <a id="rf-e1-9">RF-E1.9</a> | Se um usuário autentica com Google e já existe conta com aquele e-mail, então o sistema deve vincular as identidades em vez de criar conta duplicada. | Todos | Média | [RF-E1.8](#rf-e1-8) | Decisão de projeto | Login Google com e-mail já cadastrado reutiliza a conta existente. |

## E2 — Administrar empresas, usuários e programas

Painel onde o admin opera e **configura os motores sem depender do dev**.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| <a id="rf-e2-1">RF-E2.1</a> | O sistema deve permitir criar, editar e desativar Empresas. | Admin | Alta | [RF-E1.4a](#rf-e1-4a) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | CRUD completo de empresa. |
| <a id="rf-e2-2">RF-E2.2</a> | O sistema deve permitir criar e gerenciar usuários, incluindo papel e reset de senha. | Admin | Alta | [RF-E1.4a](#rf-e1-4a) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar usuário, alterar papel, resetar senha. |
| <a id="rf-e2-3">RF-E2.3</a> | O sistema deve permitir criar Programas e matricular participantes e mentores. | Admin | Alta | [RF-E1.2](#rf-e1-2), [RF-E1.3](#rf-e1-3) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar programa e matricular pessoas gerando participações. |
| <a id="rf-e2-4">RF-E2.4</a> | O sistema deve oferecer um ponto único de acesso aos construtores (formulários, jornadas, trilhas, selos, réguas, templates de ciclo) e à biblioteca de mídia. | Admin | Alta | [RF-E1.4a](#rf-e1-4a) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | A partir do hub, alcançar cada construtor. |
| <a id="rf-e2-5">RF-E2.5</a> | Onde o admin possui múltiplas empresas/programas, o sistema deve permitir alternar o contexto ativo. | Admin | Alta | [RF-E1.2a](#rf-e1-2a) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Trocar o contexto muda o quadro exibido. |
| <a id="rf-e2-6">RF-E2.6</a> | O sistema deve oferecer painéis de acompanhamento por Programa e por Pessoa. | Admin | Média | [RF-E1.3](#rf-e1-3) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Ver as tarefas/sessões de uma pessoa agrupadas. |
| <a id="rf-e2-7">RF-E2.7</a> | O sistema deve permitir clonar ou reaplicar um template (formulário, jornada, trilha, selo) a outro programa sem recriá-lo. | Admin | Média | [RF-E2.4](#rf-e2-4) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Aplicar um mesmo formulário a dois programas. |

## E3 — Organizar e acompanhar tarefas em ciclos

Base de acompanhamento (já em produção); passa a pertencer ao Programa. Abaixo, os campos das entidades e o CRUD completo de cada uma.

**Campos da entidade Tarefa**

| Campo | Obrigatório | Descrição |
|---|---|---|
| título | Sim | Nome da tarefa. |
| responsável | Sim | Usuário da mesma empresa; default = quem cria (RF-E1.3). |
| status | Sim | `a fazer` (default), `fazendo`, `feita`. |
| descrição | Não | Texto livre de detalhe. |
| ciclo | Não | Sem ciclo, a tarefa fica na sementeira. |
| prazo | Não | Data-alvo. |
| subtarefas | Não | Itens com texto e marcação de concluído. |

**Campos da entidade Ciclo**

| Campo | Obrigatório | Descrição |
|---|---|---|
| nome | Sim | Nome do ciclo. |
| data de início | Sim | Início do período. |
| data de fim | Sim | Fim do período. |
| encerrado | — | Estado definido ao encerrar o ciclo (não preenchido na criação). |

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| <a id="rf-e3-1">RF-E3.1</a> | Quando um usuário cria uma tarefa, o sistema deve exigir o título; o responsável assume o criador e o status assume "a fazer" por padrão. | Cliente | Alta | [RF-E1.6](#rf-e1-6) | Existente | Criar tarefa só com título; responsável = criador, status = a fazer. |
| <a id="rf-e3-2">RF-E3.2</a> | O sistema deve permitir editar título, responsável, status, descrição, ciclo e prazo de uma tarefa. | Cliente | Alta | [RF-E3.1](#rf-e3-1) | Existente | Alterar cada campo e persistir. |
| <a id="rf-e3-3">RF-E3.3</a> | O sistema deve permitir excluir uma tarefa, removendo também suas subtarefas. | Cliente | Alta | [RF-E3.1](#rf-e3-1) | Inferência | Excluir tarefa a remove com suas subtarefas. |
| <a id="rf-e3-4">RF-E3.4</a> | O sistema deve permitir mover uma tarefa entre os estados a fazer, fazendo e feita. | Cliente | Alta | [RF-E3.1](#rf-e3-1) | Existente | Mover a tarefa pelos três estados. |
| <a id="rf-e3-5">RF-E3.5</a> | O sistema deve permitir adicionar, marcar como feita e remover subtarefas de uma tarefa. | Cliente | Média | [RF-E3.1](#rf-e3-1) | Existente | Adicionar subtarefa, marcá-la e removê-la. |
| <a id="rf-e3-6">RF-E3.6</a> | Quando um usuário cria um ciclo, o sistema deve exigir nome, data de início e data de fim. | Cliente | Alta | [RF-E1.6](#rf-e1-6) | Existente | Criar ciclo exige os três campos. |
| <a id="rf-e3-7">RF-E3.7</a> | O sistema deve permitir editar nome, data de início e data de fim de um ciclo. | Cliente | Alta | [RF-E3.6](#rf-e3-6) | Inferência | Alterar os campos do ciclo e persistir. |
| <a id="rf-e3-8">RF-E3.8</a> | O sistema deve permitir excluir um ciclo, devolvendo suas tarefas à sementeira. | Cliente | Média | [RF-E3.6](#rf-e3-6) | Inferência | Excluir ciclo com tarefas move-as para a sementeira. |
| <a id="rf-e3-9">RF-E3.9</a> | Quando um ciclo é encerrado, o sistema deve devolver as tarefas não concluídas à sementeira. | Sistema | Alta | [RF-E3.6](#rf-e3-6) | Existente | Encerrar ciclo com tarefa aberta a move para a sementeira. |
| <a id="rf-e3-10">RF-E3.10</a> | O sistema deve organizar as tarefas em ciclos e, quando sem ciclo, na sementeira. | Cliente | Alta | [RF-E3.1](#rf-e3-1), [RF-E3.6](#rf-e3-6) | Existente | Tarefa sem ciclo aparece na sementeira. |
| <a id="rf-e3-11">RF-E3.11</a> | O sistema deve exibir as tarefas em visões de lista, kanban e calendário. | Todos | Média | [RF-E3.1](#rf-e3-1) | Existente | Alternar entre as três visões. |
| <a id="rf-e3-12">RF-E3.12</a> | O sistema deve permitir definir templates de ciclo (ex.: Florescimento) e instanciá-los num programa, gerando ciclo e tarefas. | Admin | Alta | [RF-E3.6](#rf-e3-6), [RF-E2.4](#rf-e2-4) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Instanciar o template cria o ciclo com as tarefas do checklist. |

!!! question "A confirmar"
    RF-E3.8 assume que excluir um ciclo **devolve** as tarefas à sementeira. Alternativa: **bloquear** a exclusão de ciclo que ainda tenha tarefas. Definir qual comportamento.

## E4 — Criar formulários e coletar respostas

Motor único de formulários, reusado por Mergulho, tarefas de casa, assessment/PDI e diagnóstico.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| <a id="rf-e4-1">RF-E4.1</a> | O sistema deve permitir criar e editar formulários com campos de tipos variados (texto curto, texto longo, escolha única, escolha múltipla, escala, número, data e upload). | Admin | Alta | [RF-E2.4](#rf-e2-4) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar um formulário com um campo de cada tipo e persistir. |
| <a id="rf-e4-2">RF-E4.2</a> | O sistema deve permitir configurar cada campo quanto a rótulo, obrigatoriedade, opções e ordem. | Admin | Alta | [RF-E4.1](#rf-e4-1) | Inferência | Reordenar campos e marcar obrigatoriedade. |
| <a id="rf-e4-3">RF-E4.3</a> | O sistema deve permitir atribuir um formulário a um contexto (programa, etapa de jornada ou participação), com prazo. | Admin | Alta | [RF-E4.1](#rf-e4-1), [RF-E1.2](#rf-e1-2) | Inferência | Atribuir o mesmo formulário a dois contextos distintos. |
| <a id="rf-e4-4">RF-E4.4</a> | O sistema deve permitir responder um formulário atribuído, salvando rascunho antes do envio. | Participante | Alta | [RF-E4.3](#rf-e4-3), [RF-E1.3](#rf-e1-3) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Salvar rascunho, sair, retomar e enviar. |
| <a id="rf-e4-5">RF-E4.5</a> | Quando um participante envia uma resposta, o sistema deve registrá-la e emitir um evento para os módulos interessados. | Sistema | Alta | [RF-E4.4](#rf-e4-4) | Inferência | Envio dispara o evento de resposta enviada. |
| <a id="rf-e4-6">RF-E4.6</a> | Onde um formulário for do tipo avaliado (quiz), o sistema deve registrar gabarito e pontuação e, opcionalmente, um tempo-limite. | Admin/Sistema | Média | [RF-E4.1](#rf-e4-1) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Responder um quiz produz pontuação conforme o gabarito. |

## E5 — Agendar e registrar sessões de mentoria

Auto-agendamento com regras + registro do que foi combinado.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| <a id="rf-e5-1">RF-E5.1</a> | O sistema deve permitir ao mentor publicar disponibilidade em slots finitos. | Mentor | Alta | [RF-E1.3](#rf-e1-3) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Publicar slots que ficam visíveis para agendamento. |
| <a id="rf-e5-2">RF-E5.2</a> | O sistema deve permitir agendar uma sessão num slot disponível, respeitando o limite do pacote. | Participante | Alta | [RF-E5.1](#rf-e5-1), [RF-E1.3](#rf-e1-3) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Agendar até o limite; além dele é bloqueado. |
| <a id="rf-e5-3">RF-E5.3</a> | O sistema não deve permitir a remarcação de sessões. | Sistema | Alta | [RF-E5.2](#rf-e5-2) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Não há ação de remarcar uma sessão agendada. |
| <a id="rf-e5-4">RF-E5.4</a> | O sistema deve permitir transferir uma sessão a um colega da mesma empresa, notificando os envolvidos. | Participante | Média | [RF-E5.2](#rf-e5-2) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Transferir a sessão para colega da mesma empresa; terceiros não. |
| <a id="rf-e5-5">RF-E5.5</a> | O sistema deve exibir a linha do tempo das suas sessões. | Participante | Média | [RF-E5.2](#rf-e5-2) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Ver 1ª, 2ª, 3ª sessão com datas e estado. |
| <a id="rf-e5-6">RF-E5.6</a> | Quando ocorre uma sessão, o sistema deve permitir o check-in e o registro do combinado (ex.: fez mais / fez menos). | Mentor | Alta | [RF-E5.2](#rf-e5-2) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Fazer check-in e gravar o combinado da sessão. |
| <a id="rf-e5-7">RF-E5.7</a> | Quando uma sessão é agendada, o sistema deve programar as mensagens de lembrete correspondentes. | Sistema | Média | [RF-E5.2](#rf-e5-2), [RF-E9.1](#rf-e9-1) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Agendamento cria disparos de lembrete (ver E9). |
| <a id="rf-e5-8">RF-E5.8</a> | O sistema deve permitir ao mentor conectar sua conta Google (Calendar) por OAuth. | Mentor | Média | [RF-E1.3](#rf-e1-3) | Decisão de projeto | Mentor conecta o Google e o vínculo persiste. |
| <a id="rf-e5-9">RF-E5.9</a> | Quando uma sessão é agendada, o sistema deve criar o evento correspondente no Google Calendar do mentor e do participante, com convite. | Sistema | Média | [RF-E5.2](#rf-e5-2), [RF-E5.8](#rf-e5-8) | Decisão de projeto | Agendar cria o evento e envia convite aos dois. |
| <a id="rf-e5-10">RF-E5.10</a> | Enquanto a agenda Google do mentor estiver conectada, o sistema deve considerar seus horários ocupados para bloquear slots em conflito. | Sistema | Média | [RF-E5.8](#rf-e5-8), [RF-E5.1](#rf-e5-1) | Decisão de projeto | Horário ocupado no Google não aparece como slot disponível. |

## E6 — Publicar conteúdo e trilhas de reforço

Biblioteca de reforço de aprendizagem, montada pelo admin.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| <a id="rf-e6-1">RF-E6.1</a> | O sistema deve permitir criar trilhas compostas por itens ordenados (pílula de texto, vídeo, leitura, quiz). | Admin | Média | [RF-E2.4](#rf-e2-4) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Montar uma trilha com um item de cada tipo. |
| <a id="rf-e6-2">RF-E6.2</a> | O sistema deve armazenar e servir a mídia dos itens de trilha. | Sistema | Média | [RF-E6.1](#rf-e6-1) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Subir um vídeo e reproduzi-lo no item. |
| <a id="rf-e6-3">RF-E6.3</a> | O sistema deve permitir consumir os itens e marcar o progresso. | Participante | Média | [RF-E6.1](#rf-e6-1), [RF-E1.3](#rf-e1-3) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Marcar item como concluído e ver o progresso. |
| <a id="rf-e6-4">RF-E6.4</a> | O sistema não deve bloquear conteúdo por não-conclusão (reforço, não vigilância). | Sistema | Média | [RF-E6.3](#rf-e6-3) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Todos os itens acessíveis independentemente do progresso. |

## E7 — Montar e exibir a jornada do participante

Espinha que amarra encontros, formulários, trilhas e selos numa linha do tempo. Também um construtor.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| <a id="rf-e7-1">RF-E7.1</a> | O sistema deve permitir montar uma jornada com etapas ordenadas (marco, encontro, formatura), cada uma com data ou offset. | Admin | Média | [RF-E2.4](#rf-e2-4) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar jornada com marco zero, encontros e formatura. |
| <a id="rf-e7-2">RF-E7.2</a> | O sistema deve permitir anexar a uma etapa um formulário, uma trilha e/ou um selo. | Admin | Média | [RF-E7.1](#rf-e7-1), [RF-E4.1](#rf-e4-1), [RF-E6.1](#rf-e6-1), [RF-E8.1](#rf-e8-1) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Anexar os três a uma etapa. |
| <a id="rf-e7-3">RF-E7.3</a> | O sistema deve exibir a jornada e o progresso nela. | Participante | Média | [RF-E7.1](#rf-e7-1), [RF-E1.3](#rf-e1-3) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Participante vê a linha do tempo e onde está. |
| <a id="rf-e7-4">RF-E7.4</a> | Quando o participante conclui uma etapa, o sistema deve registrar a conclusão e emitir um evento. | Sistema | Média | [RF-E7.3](#rf-e7-3) | Inferência | Concluir etapa dispara o evento de etapa concluída. |

## E8 — Conceder selos por progresso (gamificação)

Selos por pilar/competência, silenciosos e editoriais.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| <a id="rf-e8-1">RF-E8.1</a> | O sistema deve permitir definir selos (por pilar ou competência) e seus critérios de concessão. | Admin | Média | [RF-E2.4](#rf-e2-4) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar selo com critério vinculado a uma etapa/trilha. |
| <a id="rf-e8-2">RF-E8.2</a> | Quando um critério de selo é satisfeito, o sistema deve conceder o selo ao participante. | Sistema | Média | [RF-E8.1](#rf-e8-1), [RF-E7.4](#rf-e7-4) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Cumprir o critério concede o selo automaticamente. |
| <a id="rf-e8-3">RF-E8.3</a> | O sistema deve exibir os selos do participante em estética silenciosa, sem ranking, streak ou confete. | Participante | Média | [RF-E8.2](#rf-e8-2) | PRODUCT.md | Tela de selos sem elementos competitivos. |

## E9 — Enviar mensagens automáticas por WhatsApp

Mensagens automáticas antes e depois de cada encontro.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| <a id="rf-e9-1">RF-E9.1</a> | O sistema deve permitir criar modelos de mensagem com gatilho, canal WhatsApp e variáveis. | Admin | Média | [RF-E2.4](#rf-e2-4) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar modelo com variáveis e gatilho. |
| <a id="rf-e9-2">RF-E9.2</a> | Quando um gatilho ocorre (antes/depois de encontro, ou X horas antes de uma sessão), o sistema deve agendar o disparo. | Sistema | Média | [RF-E9.1](#rf-e9-1), [RF-E7.1](#rf-e7-1) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Gatilho cria um disparo agendado para o momento certo. |
| <a id="rf-e9-3">RF-E9.3</a> | O sistema deve enviar as mensagens via WhatsApp e registrar o status (agendado, enviado, falhou), com novas tentativas em caso de falha. | Sistema | Média | [RF-E9.2](#rf-e9-2) | Decisão de projeto | Disparo enviado atualiza status; falha gera retry. |

## E10 — Diagnosticar RH e gerar planos de ação

Em grande parte, composição de E4 (formulário) + E3 (tarefas).

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| <a id="rf-e10-1">RF-E10.1</a> | O sistema deve permitir aplicar um diagnóstico como formulário especializado de subsistemas e maturidade. | Admin | Média | [RF-E4.1](#rf-e4-1) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Aplicar o diagnóstico a uma empresa e coletar respostas. |
| <a id="rf-e10-2">RF-E10.2</a> | O sistema deve calcular e exibir o resultado do diagnóstico (maturidade por subsistema). | Sistema | Média | [RF-E10.1](#rf-e10-1) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Ver o nível de maturidade por subsistema após respostas. |
| <a id="rf-e10-3">RF-E10.3</a> | O sistema deve gerar um plano de ação cujos itens são tarefas acompanháveis. | Admin | Média | [RF-E10.2](#rf-e10-2), [RF-E3.1](#rf-e3-1) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Gerar plano e acompanhar seus itens como tarefas. |

## E11 — Registrar presença em eventos e emitir certificados

Para formações presenciais. Sem pagamento na plataforma.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| <a id="rf-e11-1">RF-E11.1</a> | O sistema deve registrar presença em eventos por check-in (QR/scan), associando a participação. | Participante | Média | [RF-E1.3](#rf-e1-3) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Escanear registra a presença do participante no evento. |
| <a id="rf-e11-2">RF-E11.2</a> | O sistema deve gerar um relatório de presença exportável para o cliente. | Admin | Média | [RF-E11.1](#rf-e11-1) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Exportar a lista de presença de um evento. |
| <a id="rf-e11-3">RF-E11.3</a> | Quando um participante cumpre a carga horária, o sistema deve gerar um certificado em PDF. | Sistema | Baixa | [RF-E11.1](#rf-e11-1) | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Cumprir a carga gera o certificado em PDF. |

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
