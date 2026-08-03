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

## Em breve

Conforme polimos o desenho antes de implementar, entram aqui:

- Desenho da API REST (recursos, JWT, OAuth, convite)
- Diagramas de estado (ex.: ciclo de vida da sessão, do ciclo, da tarefa)
- Fluxos-chave (diagramas de sequência)
