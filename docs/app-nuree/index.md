# app-nuree

Sistema da Nuree Negócios. Plataforma multi-produto composta de **módulos compartilhados**, sem misturar as bases de cada cliente. Quatro produtos:

- **Nuree Gestão** — implantação de gestão ágil e sprints (o núcleo já em produção).
- **Nuree Pessoas** — implantação de RH e gestão de pessoas (diagnóstico + planos de ação).
- **Nuree Lab** — desenvolvimento de autônomos (jornada Mergulho → Florescimento).
- **Nuree Pulse** — formação e mentoria de lideranças (prioridade de produto).

## Nesta seção

- **[Requisitos](requisitos.md)** — especificação funcional e não-funcional, em formato EARS com rastreabilidade.
- **Arquitetura** — diagramas-como-código, rastreados aos requisitos:
    - [Visão de arquitetura](arquitetura-visao.md) — contexto e contêineres (C4).
    - [Modelo de domínio](arquitetura-dominio.md) — as classes de cada motor.
    - [Modelo de banco](arquitetura-banco.md) — o ERD-alvo e as migrations propostas.
    - [Módulos da API](arquitetura-modulos.md) — os bounded contexts e a autorização central.

## Em breve

Conforme polimos o desenho antes de implementar, entram aqui:

- Event bus e jobs (catálogo de eventos de domínio e filas)
- Desenho da API REST (recursos, JWT, OAuth, convite)
- Diagramas de estado (ex.: ciclo de vida da sessão, do ciclo, da tarefa)
- Fluxos-chave (diagramas de sequência)
