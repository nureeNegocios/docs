# Modelo de domínio

As classes do domínio Nuree, organizadas por motor. É a arquitetura ideal descrita nos
[requisitos](requisitos.md) — o [modelo de banco](arquitetura-banco.md) é a sua projeção
em tabelas, e os [módulos da API](arquitetura-modulos.md) são o seu recorte em bounded contexts.

## O padrão que se repete: definição → aplicação → resposta

Quase todos os motores (formulários, jornadas, trilhas, selos, réguas, templates de ciclo)
seguem **três camadas** — e é isso que dá ao admin o poder de configurar sem depender do dev
([RNF-1](requisitos.md#rnf-1)):

1. **Definição / Template** — o admin cria no construtor, reutilizável (`Formulario`,
   `Jornada`, `Trilha`, `Selo`, `ModeloMensagem`, `TemplateCiclo`).
2. **Aplicação / Atribuição** — a definição é anexada a um contexto (`Programa`, `Etapa`,
   `Participacao`).
3. **Instância / Resposta / Progresso** — o dado real do cliente (`RespostaFormulario`,
   `ProgressoEtapa`, `SeloConcedido`).

Adicionar "mais um formulário" ou "mais uma trilha" é **criar dado**, não escrever código.

!!! info "Soft delete global"
    Todo agregado carrega `isDeleted` + `excluidoEm` ([RNF-9](requisitos.md#rnf-9)): a
    exclusão marca o registro (some da UI) e o hard delete só ocorre após ~3 meses. Nos
    diagramas abaixo o campo aparece nas raízes de agregado; presuma-o nas demais.

## Núcleo de identidade, tenancy e tarefas

`Produto` é um catálogo fixo (enum); `Programa` é a instância concreta de um produto para
uma empresa e o **container** de tudo; `Participacao` matricula um `Usuario` num `Programa`
com o **pacote contratado** — sem papel próprio, já que os únicos papéis são Admin e Cliente
([RF-E1.1](requisitos.md#rf-e1-1)..[RF-E1.3](requisitos.md#rf-e1-3),
[RF-E1.4a](requisitos.md#rf-e1-4a)).

```mermaid
classDiagram
    class Empresa {
        +UUID id
        +string nome
        +bool isDeleted
    }
    class Usuario {
        +UUID id
        +string nome
        +string email
        +Papel papelPlataforma
        +bool precisaTrocarSenha
        +bool isDeleted
    }
    class ContaOAuth {
        +UUID id
        +string provedor
        +string providerUserId
    }
    class Produto {
        <<enumeration>>
        GESTAO
        PESSOAS
        LAB
        PULSE
    }
    class Programa {
        +UUID id
        +Produto produto
        +string nome
        +date inicio
        +date fim
        +StatusPrograma status
        +bool isDeleted
    }
    class Participacao {
        +UUID id
        +json pacote
        +bool isDeleted
    }
    class Ciclo {
        +UUID id
        +string nome
        +date inicio
        +date fim
        +datetime encerradoEm
        +bool isDeleted
    }
    class Tarefa {
        +UUID id
        +string titulo
        +string descricao
        +date prazo
        +StatusTarefa status
        +bool isDeleted
    }
    class Subtarefa {
        +UUID id
        +string texto
        +bool feita
    }

    Empresa "1" --> "*" Usuario
    Usuario "1" --> "*" ContaOAuth
    Empresa "1" --> "*" Programa : contrata
    Programa "*" --> "1" Produto
    Programa "1" --> "*" Participacao
    Participacao "*" --> "1" Usuario
    Programa "1" --> "*" Ciclo
    Ciclo "1" --> "*" Tarefa
    Programa "1" --> "*" Tarefa : escopo
    Tarefa "1" --> "*" Subtarefa
    Tarefa "*" --> "1" Usuario : responsavel
```

Uma tarefa sem ciclo vive na **sementeira**. Encerrar um ciclo move as tarefas abertas para
o próximo ([RF-E3.9](requisitos.md#rf-e3-9)). `TemplateCiclo`/`TemplateTarefa` (não
mostrados) instanciam um ciclo pré-preenchido, como o Florescimento
([RF-E3.12](requisitos.md#rf-e3-12)).

## Motor de formulários

Motor único reusado por Mergulho, tarefas de casa, assessment/PDI e diagnóstico. O modo
avaliado (quiz) é uma extensão dos campos de escolha ([RF-E4.1](requisitos.md#rf-e4-1)..[RF-E4.6](requisitos.md#rf-e4-6)).

```mermaid
classDiagram
    class Formulario {
        +UUID id
        +string titulo
        +bool avaliado
        +bool isDeleted
    }
    class Campo {
        +UUID id
        +TipoCampo tipo
        +string rotulo
        +bool obrigatorio
        +int ordem
        +json opcoes
        +json gabarito
        +int pontuacao
    }
    class AtribuicaoFormulario {
        +UUID id
        +ContextoTipo contexto
        +UUID contextoId
        +date prazo
    }
    class RespostaFormulario {
        +UUID id
        +StatusResposta status
        +datetime enviadaEm
        +int pontuacaoTotal
    }
    class RespostaCampo {
        +UUID id
        +json valor
    }

    Formulario "1" --> "*" Campo
    Formulario "1" --> "*" AtribuicaoFormulario
    AtribuicaoFormulario "1" --> "*" RespostaFormulario
    RespostaFormulario "1" --> "*" RespostaCampo
    RespostaCampo "*" --> "1" Campo
    RespostaFormulario "*" --> "1" Participacao
```

## Agenda e sessões de mentoria

O mentor é o **Admin**: ele publica a disponibilidade e confirma a realização; o cliente só
agenda e (no máximo) transfere para um colega da mesma empresa. Sem remarcação
([RF-E5.1](requisitos.md#rf-e5-1)..[RF-E5.12](requisitos.md#rf-e5-12)).

```mermaid
classDiagram
    class Disponibilidade {
        +UUID id
        +datetime inicio
        +datetime fim
        +bool isDeleted
    }
    class Slot {
        +UUID id
        +datetime inicio
        +datetime fim
        +StatusSlot status
    }
    class Sessao {
        +UUID id
        +datetime inicio
        +StatusSessao status
        +bool checkIn
        +bool isDeleted
    }
    class RegistroSessao {
        +UUID id
        +string combinado
        +string fezMais
        +string fezMenos
    }
    class ContaGoogle {
        +UUID id
        +string calendarId
    }

    Programa "1" --> "*" Disponibilidade : publicada pelo Admin
    Disponibilidade "1" --> "*" Slot
    Slot "1" --> "0..1" Sessao
    Sessao "*" --> "1" Participacao : cliente
    Sessao "*" --> "1" Usuario : mentor(Admin)
    Sessao "1" --> "0..1" RegistroSessao
    Usuario "1" --> "0..1" ContaGoogle : mentor
```

## Conteúdo, jornada, selos e comunicação

Os motores editoriais. **Jornada** é a espinha que amarra formulário, trilha e selo por
etapa; **Gamificação** apenas reage a eventos de conclusão — não conhece a jornada
([RF-E6.1](requisitos.md#rf-e6-1)..[RF-E9.3](requisitos.md#rf-e9-3)).

```mermaid
classDiagram
    class Trilha {
        +UUID id
        +string titulo
        +bool isDeleted
    }
    class ItemTrilha {
        +UUID id
        +TipoItem tipo
        +int ordem
    }
    class ProgressoItem {
        +UUID id
        +bool concluido
        +datetime concluidoEm
    }
    class Jornada {
        +UUID id
        +string titulo
        +bool isDeleted
    }
    class Etapa {
        +UUID id
        +TipoEtapa tipo
        +int ordem
        +int offsetDias
    }
    class ProgressoEtapa {
        +UUID id
        +bool concluida
        +datetime concluidaEm
    }
    class Selo {
        +UUID id
        +string nome
        +string pilar
        +bool isDeleted
    }
    class CriterioSelo {
        +UUID id
        +TipoCriterio tipo
        +UUID alvoId
    }
    class SeloConcedido {
        +UUID id
        +datetime concedidoEm
    }
    class ModeloMensagem {
        +UUID id
        +string gatilho
        +string corpo
        +bool isDeleted
    }
    class Disparo {
        +UUID id
        +datetime agendadoPara
        +StatusDisparo status
    }
    class Midia {
        +UUID id
        +string chaveS3
        +string tipo
    }

    Trilha "1" --> "*" ItemTrilha
    ItemTrilha "0..1" --> "1" Midia
    ItemTrilha "1" --> "*" ProgressoItem
    ProgressoItem "*" --> "1" Participacao

    Jornada "1" --> "*" Etapa
    Etapa "0..1" --> "1" Formulario
    Etapa "0..1" --> "1" Trilha
    Etapa "0..1" --> "1" Selo
    Etapa "1" --> "*" ProgressoEtapa
    ProgressoEtapa "*" --> "1" Participacao

    Selo "1" --> "1" CriterioSelo
    Selo "1" --> "*" SeloConcedido
    SeloConcedido "*" --> "1" Participacao

    ModeloMensagem "1" --> "*" Disparo
    Disparo "*" --> "1" Participacao
```

## Diagnóstico, planos de ação e presença

Diagnóstico é, em grande parte, **composição**: um formulário especializado (dimensões +
escala de maturidade 1–5) cujo resultado gera um `PlanoAcao` acompanhado como `Tarefa`s
([RF-E10.1](requisitos.md#rf-e10-1)..[RF-E10.3](requisitos.md#rf-e10-3)). Presença cobre
formações presenciais, sem pagamento ([RF-E11.1](requisitos.md#rf-e11-1)..[RF-E11.3](requisitos.md#rf-e11-3)).

```mermaid
classDiagram
    class Diagnostico {
        +UUID id
        +string titulo
        +bool isDeleted
    }
    class Dimensao {
        +UUID id
        +string nome
    }
    class ResultadoDiagnostico {
        +UUID id
        +json maturidadePorDimensao
        +float maturidadeGeral
    }
    class PlanoAcao {
        +UUID id
        +bool isDeleted
    }
    class Evento {
        +UUID id
        +string nome
        +int cargaHoraria
        +bool isDeleted
    }
    class CheckinPresenca {
        +UUID id
        +datetime registradoEm
    }
    class Certificado {
        +UUID id
        +datetime emitidoEm
    }

    Diagnostico "1" --> "*" Dimensao
    Diagnostico "1" --> "1" Formulario : especializa
    Diagnostico "1" --> "*" ResultadoDiagnostico
    ResultadoDiagnostico "1" --> "0..1" PlanoAcao
    PlanoAcao "1" --> "*" Tarefa : itens

    Evento "1" --> "*" CheckinPresenca
    CheckinPresenca "*" --> "1" Participacao
    Participacao "1" --> "*" Certificado
    Certificado "0..1" --> "1" Midia : PDF
```

## Rastreabilidade

| Grupo de classes | Épico | Requisitos |
|---|---|---|
| Empresa, Usuario, Produto, Programa, Participacao, ContaOAuth | E1 | [RF-E1.1](requisitos.md#rf-e1-1)..[RF-E1.11](requisitos.md#rf-e1-11) |
| Ciclo, Tarefa, Subtarefa, TemplateCiclo | E3 | [RF-E3.1](requisitos.md#rf-e3-1)..[RF-E3.12](requisitos.md#rf-e3-12) |
| Formulario, Campo, Atribuicao, Resposta | E4 | [RF-E4.1](requisitos.md#rf-e4-1)..[RF-E4.6](requisitos.md#rf-e4-6) |
| Disponibilidade, Slot, Sessao, RegistroSessao, ContaGoogle | E5 | [RF-E5.1](requisitos.md#rf-e5-1)..[RF-E5.12](requisitos.md#rf-e5-12) |
| Trilha, ItemTrilha, ProgressoItem | E6 | [RF-E6.1](requisitos.md#rf-e6-1)..[RF-E6.4](requisitos.md#rf-e6-4) |
| Jornada, Etapa, ProgressoEtapa | E7 | [RF-E7.1](requisitos.md#rf-e7-1)..[RF-E7.4](requisitos.md#rf-e7-4) |
| Selo, CriterioSelo, SeloConcedido | E8 | [RF-E8.1](requisitos.md#rf-e8-1)..[RF-E8.3](requisitos.md#rf-e8-3) |
| ModeloMensagem, Disparo | E9 | [RF-E9.1](requisitos.md#rf-e9-1)..[RF-E9.3](requisitos.md#rf-e9-3) |
| Diagnostico, Dimensao, ResultadoDiagnostico, PlanoAcao | E10 | [RF-E10.1](requisitos.md#rf-e10-1)..[RF-E10.3](requisitos.md#rf-e10-3) |
| Evento, CheckinPresenca, Certificado | E11 | [RF-E11.1](requisitos.md#rf-e11-1)..[RF-E11.3](requisitos.md#rf-e11-3) |
| Midia | transversal | [RF-E6.2](requisitos.md#rf-e6-2), [RF-E11.3](requisitos.md#rf-e11-3) |
