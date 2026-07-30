# Requisitos — app-nuree

Especificação em formato **EARS** com metadados de rastreabilidade. Ver a [convenção de escrita](../convencoes/requisitos.md) para os padrões e o esquema de IDs.

!!! info "Fontes desta especificação"
    - **[Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md)** — Gabriel + Helô, 29/07/2026. Fonte primária.
    - **Inferência** — requisito derivado da natureza dos produtos, a validar com o negócio.
    - **Decisão de projeto** — decisão de arquitetura/tecnologia registrada.
    - **PRODUCT.md / AGENTS.md** — regras de marca e de projeto já vigentes.

!!! abstract "Papéis (coluna Ator)"
    - **Admin** — plataforma (Nuree); acesso cross-tenant, opera todos os construtores.
    - **Cliente** — usuário comum, restrito ao escopo do seu programa. Papéis de programa: **Participante**, **Mentor**, **Gestor**.
    - **Todos** — qualquer usuário autenticado (Admin ou Cliente).
    - **Sistema** — comportamento automático, sem ator humano.

    Os requisitos de escopo/permissão (RF-E1.6, RF-E1.7) aplicam-se ao **Cliente**; o **Admin** é a exceção que transita entre escopos.

**Leitura por dependência:** a coluna **Depende de** aponta os pré-requisitos de cada item. A base é sempre E1 (identidade, papéis e escopo): estabelecer *quem é o usuário e seu papel* vem **antes** das permissões, e as permissões vêm antes dos módulos que operam sobre dados. Prioridade em MoSCoW: `Alta` (must) · `Média` (should) · `Baixa` (could).

---

## E1 — Identidade, Programas e Escopo

Alicerce multi-tenant: quem é quem e o que cada um enxerga. É a base de que todos os outros épicos dependem.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| RF-E1.1 | O sistema deve representar Produtos a partir de um catálogo fixo (Gestão, Pessoas, Lab, Pulse). | Sistema | Alta | — | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Cada produto existe e é selecionável ao criar um programa. |
| RF-E1.2 | O sistema deve permitir criar Programas como instância de um Produto para uma Empresa, com período opcional. | Admin | Alta | RF-E1.1 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar um programa vinculado a produto+empresa e recuperá-lo. |
| RF-E1.2a | O sistema deve permitir que uma mesma Empresa tenha mais de um contrato simultâneo — cada contrato é um Programa — com dados isolados entre eles. | Sistema | Alta | RF-E1.2 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Uma empresa com dois programas não vaza dados de um para o outro. |
| RF-E1.4 | O sistema deve autenticar usuários por e-mail e senha e manter a sessão. | Todos | Alta | — | AGENTS.md | Login válido concede sessão; inválido é recusado. |
| RF-E1.4a | O sistema deve classificar cada usuário por um papel de plataforma (Admin ou Cliente), que determina seu nível de acesso. | Sistema | Alta | RF-E1.4 | Inferência | Usuário Admin e usuário Cliente têm níveis de acesso distintos. |
| RF-E1.3 | O sistema deve vincular usuários a um Programa (Participação) com um papel de programa (participante, mentor, gestor) e o pacote contratado. | Admin | Alta | RF-E1.2, RF-E1.4a | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Matricular um usuário como mentor e outro como participante no mesmo programa. |
| RF-E1.5 | Quando um usuário acessa pela primeira vez, o sistema deve exigir a troca de senha. | Todos | Média | RF-E1.4 | AGENTS.md | Primeiro login redireciona para troca de senha. |
| RF-E1.6 | O sistema deve restringir todo acesso a dados de um programa ao escopo (empresa/programa) do usuário. | Cliente | Alta | RF-E1.4a | AGENTS.md | Usuário de uma empresa não lê dados de outra. |
| RF-E1.7 | Se um usuário solicitar dados fora do seu escopo, então o sistema deve negar o acesso, exceto para o papel Admin. | Cliente | Alta | RF-E1.6 | AGENTS.md | Requisição fora de escopo retorna negação; Admin acessa. |
| RF-E1.8 | O sistema deve permitir a autenticação via conta Google (OAuth), de forma complementar ao login por e-mail e senha. | Todos | Média | RF-E1.4 | Decisão de projeto | Entrar com Google e, na mesma conta, entrar com e-mail e senha. |
| RF-E1.9 | Se um usuário autentica com Google e já existe conta com aquele e-mail, então o sistema deve vincular as identidades em vez de criar conta duplicada. | Todos | Média | RF-E1.8 | Decisão de projeto | Login Google com e-mail já cadastrado reutiliza a conta existente. |

## E2 — Administração e configuração no-code

Painel onde o admin opera e **configura os motores sem depender do dev**.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| RF-E2.1 | O sistema deve permitir criar, editar e desativar Empresas. | Admin | Alta | RF-E1.4a | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | CRUD completo de empresa. |
| RF-E2.2 | O sistema deve permitir criar e gerenciar usuários, incluindo papel e reset de senha. | Admin | Alta | RF-E1.4a | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar usuário, alterar papel, resetar senha. |
| RF-E2.3 | O sistema deve permitir criar Programas e matricular participantes e mentores. | Admin | Alta | RF-E1.2, RF-E1.3 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar programa e matricular pessoas gerando participações. |
| RF-E2.4 | O sistema deve oferecer um ponto único de acesso aos construtores (formulários, jornadas, trilhas, selos, réguas, templates de ciclo) e à biblioteca de mídia. | Admin | Alta | RF-E1.4a | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | A partir do hub, alcançar cada construtor. |
| RF-E2.5 | Onde o admin possui múltiplas empresas/programas, o sistema deve permitir alternar o contexto ativo. | Admin | Alta | RF-E1.2a | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Trocar o contexto muda o quadro exibido. |
| RF-E2.6 | O sistema deve oferecer painéis de acompanhamento por Programa e por Pessoa. | Admin | Média | RF-E1.3 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Ver as tarefas/sessões de uma pessoa agrupadas. |
| RF-E2.7 | O sistema deve permitir clonar ou reaplicar um template (formulário, jornada, trilha, selo) a outro programa sem recriá-lo. | Admin | Média | RF-E2.4 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Aplicar um mesmo formulário a dois programas. |

## E3 — Tarefas e Ciclos

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
| RF-E3.1 | Quando um usuário cria uma tarefa, o sistema deve exigir o título; o responsável assume o criador e o status assume "a fazer" por padrão. | Cliente | Alta | RF-E1.6 | Existente | Criar tarefa só com título; responsável = criador, status = a fazer. |
| RF-E3.2 | O sistema deve permitir editar título, responsável, status, descrição, ciclo e prazo de uma tarefa. | Cliente | Alta | RF-E3.1 | Existente | Alterar cada campo e persistir. |
| RF-E3.3 | O sistema deve permitir excluir uma tarefa, removendo também suas subtarefas. | Cliente | Alta | RF-E3.1 | Inferência | Excluir tarefa a remove com suas subtarefas. |
| RF-E3.4 | O sistema deve permitir mover uma tarefa entre os estados a fazer, fazendo e feita. | Cliente | Alta | RF-E3.1 | Existente | Mover a tarefa pelos três estados. |
| RF-E3.5 | O sistema deve permitir adicionar, marcar como feita e remover subtarefas de uma tarefa. | Cliente | Média | RF-E3.1 | Existente | Adicionar subtarefa, marcá-la e removê-la. |
| RF-E3.6 | Quando um usuário cria um ciclo, o sistema deve exigir nome, data de início e data de fim. | Cliente | Alta | RF-E1.6 | Existente | Criar ciclo exige os três campos. |
| RF-E3.7 | O sistema deve permitir editar nome, data de início e data de fim de um ciclo. | Cliente | Alta | RF-E3.6 | Inferência | Alterar os campos do ciclo e persistir. |
| RF-E3.8 | O sistema deve permitir excluir um ciclo, devolvendo suas tarefas à sementeira. | Cliente | Média | RF-E3.6 | Inferência | Excluir ciclo com tarefas move-as para a sementeira. |
| RF-E3.9 | Quando um ciclo é encerrado, o sistema deve devolver as tarefas não concluídas à sementeira. | Sistema | Alta | RF-E3.6 | Existente | Encerrar ciclo com tarefa aberta a move para a sementeira. |
| RF-E3.10 | O sistema deve organizar as tarefas em ciclos e, quando sem ciclo, na sementeira. | Cliente | Alta | RF-E3.1, RF-E3.6 | Existente | Tarefa sem ciclo aparece na sementeira. |
| RF-E3.11 | O sistema deve exibir as tarefas em visões de lista, kanban e calendário. | Todos | Média | RF-E3.1 | Existente | Alternar entre as três visões. |
| RF-E3.12 | O sistema deve permitir definir templates de ciclo (ex.: Florescimento) e instanciá-los num programa, gerando ciclo e tarefas. | Admin | Alta | RF-E3.6, RF-E2.4 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Instanciar o template cria o ciclo com as tarefas do checklist. |

!!! question "A confirmar"
    RF-E3.8 assume que excluir um ciclo **devolve** as tarefas à sementeira. Alternativa: **bloquear** a exclusão de ciclo que ainda tenha tarefas. Definir qual comportamento.

## E4 — Construtor de formulários

Motor único de formulários, reusado por Mergulho, tarefas de casa, assessment/PDI e diagnóstico.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| RF-E4.1 | O sistema deve permitir criar e editar formulários com campos de tipos variados (texto curto, texto longo, escolha única, escolha múltipla, escala, número, data e upload). | Admin | Alta | RF-E2.4 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar um formulário com um campo de cada tipo e persistir. |
| RF-E4.2 | O sistema deve permitir configurar cada campo quanto a rótulo, obrigatoriedade, opções e ordem. | Admin | Alta | RF-E4.1 | Inferência | Reordenar campos e marcar obrigatoriedade. |
| RF-E4.3 | O sistema deve permitir atribuir um formulário a um contexto (programa, etapa de jornada ou participação), com prazo. | Admin | Alta | RF-E4.1, RF-E1.2 | Inferência | Atribuir o mesmo formulário a dois contextos distintos. |
| RF-E4.4 | O sistema deve permitir responder um formulário atribuído, salvando rascunho antes do envio. | Participante | Alta | RF-E4.3, RF-E1.3 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Salvar rascunho, sair, retomar e enviar. |
| RF-E4.5 | Quando um participante envia uma resposta, o sistema deve registrá-la e emitir um evento para os módulos interessados. | Sistema | Alta | RF-E4.4 | Inferência | Envio dispara o evento de resposta enviada. |
| RF-E4.6 | Onde um formulário for do tipo avaliado (quiz), o sistema deve registrar gabarito e pontuação e, opcionalmente, um tempo-limite. | Admin/Sistema | Média | RF-E4.1 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Responder um quiz produz pontuação conforme o gabarito. |

## E5 — Agenda e sessões de mentoria

Auto-agendamento com regras + registro do que foi combinado.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| RF-E5.1 | O sistema deve permitir ao mentor publicar disponibilidade em slots finitos. | Mentor | Alta | RF-E1.3 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Publicar slots que ficam visíveis para agendamento. |
| RF-E5.2 | O sistema deve permitir agendar uma sessão num slot disponível, respeitando o limite do pacote. | Participante | Alta | RF-E5.1, RF-E1.3 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Agendar até o limite; além dele é bloqueado. |
| RF-E5.3 | O sistema não deve permitir a remarcação de sessões. | Sistema | Alta | RF-E5.2 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Não há ação de remarcar uma sessão agendada. |
| RF-E5.4 | O sistema deve permitir transferir uma sessão a um colega da mesma empresa, notificando os envolvidos. | Participante | Média | RF-E5.2 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Transferir a sessão para colega da mesma empresa; terceiros não. |
| RF-E5.5 | O sistema deve exibir a linha do tempo das suas sessões. | Participante | Média | RF-E5.2 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Ver 1ª, 2ª, 3ª sessão com datas e estado. |
| RF-E5.6 | Quando ocorre uma sessão, o sistema deve permitir o check-in e o registro do combinado (ex.: fez mais / fez menos). | Mentor | Alta | RF-E5.2 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Fazer check-in e gravar o combinado da sessão. |
| RF-E5.7 | Quando uma sessão é agendada, o sistema deve programar as mensagens de lembrete correspondentes. | Sistema | Média | RF-E5.2, RF-E9.1 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Agendamento cria disparos de lembrete (ver E9). |
| RF-E5.8 | O sistema deve permitir ao mentor conectar sua conta Google (Calendar) por OAuth. | Mentor | Média | RF-E1.3 | Decisão de projeto | Mentor conecta o Google e o vínculo persiste. |
| RF-E5.9 | Quando uma sessão é agendada, o sistema deve criar o evento correspondente no Google Calendar do mentor e do participante, com convite. | Sistema | Média | RF-E5.2, RF-E5.8 | Decisão de projeto | Agendar cria o evento e envia convite aos dois. |
| RF-E5.10 | Enquanto a agenda Google do mentor estiver conectada, o sistema deve considerar seus horários ocupados para bloquear slots em conflito. | Sistema | Média | RF-E5.8, RF-E5.1 | Decisão de projeto | Horário ocupado no Google não aparece como slot disponível. |

## E6 — Conteúdo e trilhas de reforço

Biblioteca de reforço de aprendizagem, montada pelo admin.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| RF-E6.1 | O sistema deve permitir criar trilhas compostas por itens ordenados (pílula de texto, vídeo, leitura, quiz). | Admin | Média | RF-E2.4 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Montar uma trilha com um item de cada tipo. |
| RF-E6.2 | O sistema deve armazenar e servir a mídia dos itens de trilha. | Sistema | Média | RF-E6.1 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Subir um vídeo e reproduzi-lo no item. |
| RF-E6.3 | O sistema deve permitir consumir os itens e marcar o progresso. | Participante | Média | RF-E6.1, RF-E1.3 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Marcar item como concluído e ver o progresso. |
| RF-E6.4 | O sistema não deve bloquear conteúdo por não-conclusão (reforço, não vigilância). | Sistema | Média | RF-E6.3 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Todos os itens acessíveis independentemente do progresso. |

## E7 — Jornada do participante

Espinha que amarra encontros, formulários, trilhas e selos numa linha do tempo. Também um construtor.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| RF-E7.1 | O sistema deve permitir montar uma jornada com etapas ordenadas (marco, encontro, formatura), cada uma com data ou offset. | Admin | Média | RF-E2.4 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar jornada com marco zero, encontros e formatura. |
| RF-E7.2 | O sistema deve permitir anexar a uma etapa um formulário, uma trilha e/ou um selo. | Admin | Média | RF-E7.1, RF-E4.1, RF-E6.1, RF-E8.1 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Anexar os três a uma etapa. |
| RF-E7.3 | O sistema deve exibir a jornada e o progresso nela. | Participante | Média | RF-E7.1, RF-E1.3 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Participante vê a linha do tempo e onde está. |
| RF-E7.4 | Quando o participante conclui uma etapa, o sistema deve registrar a conclusão e emitir um evento. | Sistema | Média | RF-E7.3 | Inferência | Concluir etapa dispara o evento de etapa concluída. |

## E8 — Gamificação editorial

Selos por pilar/competência, silenciosos e editoriais.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| RF-E8.1 | O sistema deve permitir definir selos (por pilar ou competência) e seus critérios de concessão. | Admin | Média | RF-E2.4 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar selo com critério vinculado a uma etapa/trilha. |
| RF-E8.2 | Quando um critério de selo é satisfeito, o sistema deve conceder o selo ao participante. | Sistema | Média | RF-E8.1, RF-E7.4 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Cumprir o critério concede o selo automaticamente. |
| RF-E8.3 | O sistema deve exibir os selos do participante em estética silenciosa, sem ranking, streak ou confete. | Participante | Média | RF-E8.2 | PRODUCT.md | Tela de selos sem elementos competitivos. |

## E9 — Régua de comunicação (WhatsApp)

Mensagens automáticas antes e depois de cada encontro.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| RF-E9.1 | O sistema deve permitir criar modelos de mensagem com gatilho, canal WhatsApp e variáveis. | Admin | Média | RF-E2.4 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar modelo com variáveis e gatilho. |
| RF-E9.2 | Quando um gatilho ocorre (antes/depois de encontro, ou X horas antes de uma sessão), o sistema deve agendar o disparo. | Sistema | Média | RF-E9.1, RF-E7.1 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Gatilho cria um disparo agendado para o momento certo. |
| RF-E9.3 | O sistema deve enviar as mensagens via WhatsApp e registrar o status (agendado, enviado, falhou), com novas tentativas em caso de falha. | Sistema | Média | RF-E9.2 | Decisão de projeto | Disparo enviado atualiza status; falha gera retry. |

## E10 — Diagnóstico e planos de ação (Nuree Pessoas)

Em grande parte, composição de E4 (formulário) + E3 (tarefas).

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| RF-E10.1 | O sistema deve permitir aplicar um diagnóstico como formulário especializado de subsistemas e maturidade. | Admin | Média | RF-E4.1 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Aplicar o diagnóstico a uma empresa e coletar respostas. |
| RF-E10.2 | O sistema deve calcular e exibir o resultado do diagnóstico (maturidade por subsistema). | Sistema | Média | RF-E10.1 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Ver o nível de maturidade por subsistema após respostas. |
| RF-E10.3 | O sistema deve gerar um plano de ação cujos itens são tarefas acompanháveis. | Admin | Média | RF-E10.2, RF-E3.1 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Gerar plano e acompanhar seus itens como tarefas. |

## E11 — Presença e certificação

Para formações presenciais. Sem pagamento na plataforma.

| ID | Requisito | Ator | Prioridade | Depende de | Fonte | Verificação |
|---|---|---|---|---|---|---|
| RF-E11.1 | O sistema deve registrar presença em eventos por check-in (QR/scan), associando a participação. | Participante | Média | RF-E1.3 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Escanear registra a presença do participante no evento. |
| RF-E11.2 | O sistema deve gerar um relatório de presença exportável para o cliente. | Admin | Média | RF-E11.1 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Exportar a lista de presença de um evento. |
| RF-E11.3 | Quando um participante cumpre a carga horária, o sistema deve gerar um certificado em PDF. | Sistema | Baixa | RF-E11.1 | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Cumprir a carga gera o certificado em PDF. |

---

## Requisitos não-funcionais

Aplicam-se ao sistema como um todo (sem ator específico).

| ID | Requisito | Prioridade | Fonte | Verificação |
|---|---|---|---|---|
| RNF-1 | Funcionalidades de estrutura e conteúdo devem ser configuráveis pelo admin sem novo deploy. | Alta | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar formulário/jornada/trilha novos sem alterar código. |
| RNF-2 | O sistema deve isolar os dados entre empresas/programas em toda consulta. | Alta | AGENTS.md | Nenhuma consulta retorna dados fora do escopo. |
| RNF-3 | O sistema deve proteger o acesso com JWT (access + refresh), RBAC, rate limiting, CORS restrito e validação de entrada. | Alta | Decisão de projeto | Testes de autenticação, autorização e limites de borda. |
| RNF-4 | O sistema deve tratar dados de Mergulho e diagnóstico como sensíveis, com acesso restrito por escopo e política de retenção. | Alta | Inferência | Dados sensíveis só acessíveis ao escopo autorizado. |
| RNF-5 | A interface, o domínio, as rotas e a copy devem estar em português. | Alta | AGENTS.md | Revisão de idioma. |
| RNF-6 | A interface deve respeitar contraste, foco visível, prefers-reduced-motion e rótulos aria em português. | Média | PRODUCT.md | Auditoria de acessibilidade. |
| RNF-7 | A API deve expor um contrato REST/OpenAPI estável, apto a servir um futuro app mobile. | Média | Decisão de projeto | Spec OpenAPI publicada e versionada. |
| RNF-8 | O sistema deve ter backups automáticos do banco e da mídia, com cópia off-site. | Alta | Decisão de projeto | Restauração de backup validada. |

## Restrições de projeto

| ID | Restrição | Fonte |
|---|---|---|
| RC-1 | O backend é uma API dedicada NestJS (REST/OpenAPI), substituindo as Server Actions. | Decisão de projeto |
| RC-2 | Infra self-hosted em VPS via Docker Compose: Postgres + Prisma, Redis + BullMQ, MinIO (S3), Caddy. | Decisão de projeto |
| RC-3 | Sem BaaS e sem processamento de pagamentos na plataforma. | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) / Decisão de projeto |
| RC-4 | Módulos se comunicam por event bus de domínio; trabalho assíncrono roda em worker. | Decisão de projeto |
| RC-5 | Integrações externas: WhatsApp (régua) e Google (login OAuth + Calendar). | Decisão de projeto |

---

## Fora de escopo (por enquanto)

- **Pagamentos, ingressos e inscrição paga** — ticket alto; a venda não precisa ocorrer na plataforma. Do que seria esse módulo, só o check-in de eventos permanece (E11).
- **Artefato gerado por IA** (Mapa da Essência ao fim do Mergulho) — a repensar.
- **Software de RH para o cliente** (folha, ponto) — o Nuree Pessoas ensina a implantar, não constrói o RH da empresa.
- **Recorrência, anexos genéricos, analytics avançado, histórico/auditoria ampla, realtime, app mobile** — não agora.
</content>
</invoke>
