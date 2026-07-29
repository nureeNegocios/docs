# Requisitos — app-nuree

Especificação em formato **EARS** com metadados de rastreabilidade. Ver a [convenção de escrita](../convencoes/requisitos.md) para os padrões e o esquema de IDs.

!!! info "Fontes desta especificação"
    - **[Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md)** — Gabriel + Helô, 29/07/2026. Fonte primária.
    - **Inferência** — requisito derivado da natureza dos produtos, a validar com o negócio.
    - **Decisão de projeto** — decisão de arquitetura/tecnologia registrada.
    - **PRODUCT.md / AGENTS.md** — regras de marca e de projeto já vigentes.

Prioridade em MoSCoW: `Alta` (must) · `Média` (should) · `Baixa` (could).

---

## E1 — Identidade, Programas e Escopo

Alicerce multi-tenant: quem é quem e o que cada um enxerga.

| ID | Requisito | Prioridade | Fonte | Verificação |
|---|---|---|---|---|
| RF-E1.1 | O sistema deve representar Produtos a partir de um catálogo fixo (Gestão, Pessoas, Lab, Pulse). | Alta | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Cada produto existe e é selecionável ao criar um programa. |
| RF-E1.2 | O sistema deve permitir criar Programas como instância de um Produto para uma Empresa, com período opcional. | Alta | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar um programa vinculado a produto+empresa e recuperá-lo. |
| RF-E1.3 | O sistema deve vincular usuários a um Programa (Participação) com um papel (participante, mentor, gestor) e o pacote contratado. | Alta | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Matricular um usuário como mentor e outro como participante no mesmo programa. |
| RF-E1.4 | O sistema deve autenticar usuários por e-mail e senha e manter a sessão. | Alta | AGENTS.md | Login válido concede sessão; inválido é recusado. |
| RF-E1.5 | Quando um usuário acessa pela primeira vez, o sistema deve exigir a troca de senha. | Média | AGENTS.md | Primeiro login redireciona para troca de senha. |
| RF-E1.6 | O sistema deve restringir todo acesso a dados de um programa ao escopo (empresa/programa) do usuário. | Alta | AGENTS.md | Usuário de uma empresa não lê dados de outra. |
| RF-E1.7 | Se um usuário solicitar dados fora do seu escopo, então o sistema deve negar o acesso, exceto para o papel ADMIN. | Alta | AGENTS.md | Requisição fora de escopo retorna negação; ADMIN acessa. |

## E2 — Administração e configuração no-code

Painel onde o admin opera e **configura os motores sem depender do dev**.

| ID | Requisito | Prioridade | Fonte | Verificação |
|---|---|---|---|---|
| RF-E2.1 | O sistema deve permitir ao admin criar, editar e desativar Empresas. | Alta | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | CRUD completo de empresa. |
| RF-E2.2 | O sistema deve permitir ao admin criar e gerenciar usuários, incluindo papel e reset de senha. | Alta | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar usuário, alterar papel, resetar senha. |
| RF-E2.3 | O sistema deve permitir ao admin criar Programas e matricular participantes e mentores. | Alta | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar programa e matricular pessoas gerando participações. |
| RF-E2.4 | O sistema deve oferecer ao admin um ponto único de acesso aos construtores (formulários, jornadas, trilhas, selos, réguas, templates de ciclo) e à biblioteca de mídia. | Alta | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | A partir do hub, alcançar cada construtor. |
| RF-E2.5 | Onde o admin possui múltiplas empresas/programas, o sistema deve permitir alternar o contexto ativo. | Alta | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Trocar o contexto muda o quadro exibido. |
| RF-E2.6 | O sistema deve oferecer ao admin painéis de acompanhamento por Programa e por Pessoa. | Média | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Ver as tarefas/sessões de uma pessoa agrupadas. |
| RF-E2.7 | O sistema deve permitir clonar ou reaplicar um template (formulário, jornada, trilha, selo) a outro programa sem recriá-lo. | Média | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Aplicar um mesmo formulário a dois programas. |

## E3 — Tarefas e Ciclos

Base de acompanhamento (já em produção); passa a pertencer ao Programa.

| ID | Requisito | Prioridade | Fonte | Verificação |
|---|---|---|---|---|
| RF-E3.1 | O sistema deve permitir criar, editar e mover tarefas entre os estados a fazer, fazendo e feita. | Alta | Existente | Mover uma tarefa pelos três estados. |
| RF-E3.2 | O sistema deve organizar tarefas em ciclos (com início e fim) e numa sementeira (tarefas sem ciclo). | Alta | Existente | Tarefa sem ciclo aparece na sementeira. |
| RF-E3.3 | Quando um ciclo é encerrado, o sistema deve devolver as tarefas não concluídas à sementeira. | Alta | Existente | Encerrar ciclo com tarefa aberta a move para a sementeira. |
| RF-E3.4 | O sistema deve exibir as tarefas em visões de lista, kanban e calendário. | Média | Existente | Alternar entre as três visões. |
| RF-E3.5 | O sistema deve atribuir um responsável a cada tarefa. | Alta | Existente | Toda tarefa tem responsável; troca no detalhe. |
| RF-E3.6 | O sistema deve permitir ao admin definir templates de ciclo (ex.: Florescimento) e instanciá-los num programa, gerando ciclo e tarefas. | Alta | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Instanciar o template cria o ciclo com as tarefas do checklist. |

## E4 — Construtor de formulários

Motor único de formulários, reusado por Mergulho, tarefas de casa, assessment/PDI e diagnóstico.

| ID | Requisito | Prioridade | Fonte | Verificação |
|---|---|---|---|---|
| RF-E4.1 | O sistema deve permitir ao admin criar e editar formulários com campos de tipos variados (texto curto, texto longo, escolha única, escolha múltipla, escala, número, data e upload). | Alta | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar um formulário com um campo de cada tipo e persistir. |
| RF-E4.2 | O sistema deve permitir configurar cada campo quanto a rótulo, obrigatoriedade, opções e ordem. | Alta | Inferência | Reordenar campos e marcar obrigatoriedade. |
| RF-E4.3 | O sistema deve permitir atribuir um formulário a um contexto (programa, etapa de jornada ou participação), com prazo. | Alta | Inferência | Atribuir o mesmo formulário a dois contextos distintos. |
| RF-E4.4 | O sistema deve permitir ao participante responder um formulário atribuído, salvando rascunho antes do envio. | Alta | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Salvar rascunho, sair, retomar e enviar. |
| RF-E4.5 | Quando um participante envia uma resposta, o sistema deve registrá-la e emitir um evento para os módulos interessados. | Alta | Inferência | Envio dispara o evento de resposta enviada. |
| RF-E4.6 | Onde um formulário for do tipo avaliado (quiz), o sistema deve registrar gabarito e pontuação e, opcionalmente, um tempo-limite. | Média | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Responder um quiz produz pontuação conforme o gabarito. |

## E5 — Agenda e sessões de mentoria

Auto-agendamento com regras + registro do que foi combinado.

| ID | Requisito | Prioridade | Fonte | Verificação |
|---|---|---|---|---|
| RF-E5.1 | O sistema deve permitir ao mentor publicar disponibilidade em slots finitos. | Alta | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Publicar slots que ficam visíveis para agendamento. |
| RF-E5.2 | O sistema deve permitir ao participante agendar uma sessão num slot disponível, respeitando o limite do seu pacote. | Alta | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Agendar até o limite; além dele é bloqueado. |
| RF-E5.3 | O sistema não deve permitir a remarcação de sessões. | Alta | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Não há ação de remarcar uma sessão agendada. |
| RF-E5.4 | O sistema deve permitir transferir uma sessão a um colega da mesma empresa, notificando os envolvidos. | Média | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Transferir a sessão para colega da mesma empresa; terceiros não. |
| RF-E5.5 | O sistema deve exibir ao participante a linha do tempo das suas sessões. | Média | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Ver 1ª, 2ª, 3ª sessão com datas e estado. |
| RF-E5.6 | Quando ocorre uma sessão, o sistema deve permitir o check-in e o registro do combinado (ex.: fez mais / fez menos). | Alta | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Fazer check-in e gravar o combinado da sessão. |
| RF-E5.7 | Quando uma sessão é agendada, o sistema deve programar as mensagens de lembrete correspondentes. | Média | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Agendamento cria disparos de lembrete (ver E9). |

## E6 — Conteúdo e trilhas de reforço

Biblioteca de reforço de aprendizagem, montada pelo admin.

| ID | Requisito | Prioridade | Fonte | Verificação |
|---|---|---|---|---|
| RF-E6.1 | O sistema deve permitir ao admin criar trilhas compostas por itens ordenados (pílula de texto, vídeo, leitura, quiz). | Média | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Montar uma trilha com um item de cada tipo. |
| RF-E6.2 | O sistema deve armazenar e servir a mídia dos itens de trilha. | Média | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Subir um vídeo e reproduzi-lo no item. |
| RF-E6.3 | O sistema deve permitir ao participante consumir os itens e marcar seu progresso. | Média | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Marcar item como concluído e ver o progresso. |
| RF-E6.4 | O sistema não deve bloquear conteúdo por não-conclusão (reforço, não vigilância). | Média | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Todos os itens acessíveis independentemente do progresso. |

## E7 — Jornada do participante

Espinha que amarra encontros, formulários, trilhas e selos numa linha do tempo. Também um construtor.

| ID | Requisito | Prioridade | Fonte | Verificação |
|---|---|---|---|---|
| RF-E7.1 | O sistema deve permitir ao admin montar uma jornada com etapas ordenadas (marco, encontro, formatura), cada uma com data ou offset. | Média | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar jornada com marco zero, encontros e formatura. |
| RF-E7.2 | O sistema deve permitir anexar a uma etapa um formulário, uma trilha e/ou um selo. | Média | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Anexar os três a uma etapa. |
| RF-E7.3 | O sistema deve exibir ao participante sua jornada e o progresso nela. | Média | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Participante vê a linha do tempo e onde está. |
| RF-E7.4 | Quando o participante conclui uma etapa, o sistema deve registrar a conclusão e emitir um evento. | Média | Inferência | Concluir etapa dispara o evento de etapa concluída. |

## E8 — Gamificação editorial

Selos por pilar/competência, silenciosos e editoriais.

| ID | Requisito | Prioridade | Fonte | Verificação |
|---|---|---|---|---|
| RF-E8.1 | O sistema deve permitir ao admin definir selos (por pilar ou competência) e seus critérios de concessão. | Média | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar selo com critério vinculado a uma etapa/trilha. |
| RF-E8.2 | Quando um critério de selo é satisfeito, o sistema deve conceder o selo ao participante. | Média | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Cumprir o critério concede o selo automaticamente. |
| RF-E8.3 | O sistema deve exibir os selos do participante em estética silenciosa, sem ranking, streak ou confete. | Média | PRODUCT.md | Tela de selos sem elementos competitivos. |

## E9 — Régua de comunicação (WhatsApp)

Mensagens automáticas antes e depois de cada encontro.

| ID | Requisito | Prioridade | Fonte | Verificação |
|---|---|---|---|---|
| RF-E9.1 | O sistema deve permitir ao admin criar modelos de mensagem com gatilho, canal WhatsApp e variáveis. | Média | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Criar modelo com variáveis e gatilho. |
| RF-E9.2 | Quando um gatilho ocorre (antes/depois de encontro, ou X horas antes de uma sessão), o sistema deve agendar o disparo. | Média | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Gatilho cria um disparo agendado para o momento certo. |
| RF-E9.3 | O sistema deve enviar as mensagens via WhatsApp e registrar o status (agendado, enviado, falhou), com novas tentativas em caso de falha. | Média | Decisão de projeto | Disparo enviado atualiza status; falha gera retry. |

## E10 — Diagnóstico e planos de ação (Nuree Pessoas)

Em grande parte, composição de E4 (formulário) + E3 (tarefas).

| ID | Requisito | Prioridade | Fonte | Verificação |
|---|---|---|---|---|
| RF-E10.1 | O sistema deve permitir aplicar um diagnóstico como formulário especializado de subsistemas e maturidade. | Média | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Aplicar o diagnóstico a uma empresa e coletar respostas. |
| RF-E10.2 | O sistema deve calcular e exibir o resultado do diagnóstico (maturidade por subsistema). | Média | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Ver o nível de maturidade por subsistema após respostas. |
| RF-E10.3 | O sistema deve gerar um plano de ação cujos itens são tarefas acompanháveis. | Média | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Gerar plano e acompanhar seus itens como tarefas. |

## E11 — Presença e certificação

Para formações presenciais. Sem pagamento na plataforma.

| ID | Requisito | Prioridade | Fonte | Verificação |
|---|---|---|---|---|
| RF-E11.1 | O sistema deve registrar presença em eventos por check-in (QR/scan), associando a participação. | Média | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Escanear registra a presença do participante no evento. |
| RF-E11.2 | O sistema deve gerar um relatório de presença exportável para o cliente. | Média | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Exportar a lista de presença de um evento. |
| RF-E11.3 | Quando um participante cumpre a carga horária, o sistema deve gerar um certificado em PDF. | Baixa | [Reunião de discovery - produtos nuree 29/07](../reunioes/discovery-produtos-2026-07-29.md) | Cumprir a carga gera o certificado em PDF. |

---

## Requisitos não-funcionais

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

---

## Fora de escopo (por enquanto)

- **Pagamentos, ingressos e inscrição paga** — ticket alto; a venda não precisa ocorrer na plataforma. Do que seria esse módulo, só o check-in de eventos permanece (E11).
- **Artefato gerado por IA** (Mapa da Essência ao fim do Mergulho) — a repensar.
- **Software de RH para o cliente** (folha, ponto) — o Nuree Pessoas ensina a implantar, não constrói o RH da empresa.
- **Recorrência, anexos genéricos, analytics avançado, histórico/auditoria ampla, realtime, app mobile** — não agora.
