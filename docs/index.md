# Documentação de Engenharia Nuree

Este é o portal de engenharia da **Nuree Negócios**. A ideia é enxergar, num só lugar, **todo o caminho de cada funcionalidade** — do requisito, passando pelo desenho (domínio, banco, casos de uso, estados), até a implementação e os testes.

## O que tem aqui

- **[Convenção de escrita de requisitos](convencoes/requisitos.md)** — como especificamos requisitos (formato EARS + metadados de rastreabilidade). Leia antes de escrever ou revisar requisitos.
- **[app-nuree](app-nuree/index.md)** — o sistema Nuree (Gestão, Pessoas, Lab, Pulse). Começa pela página de **[Requisitos](app-nuree/requisitos.md)**.

## Como esta doc evita ficar desatualizada

Documentação apodrece quando é escrita à parte do código e sem responsabilidade clara. Aqui a aposta é o contrário:

1. **Docs como código** — versionadas em Git, revisadas em PR, evoluindo junto do sistema.
2. **Diagramas como código** — UML, banco, casos de uso e estados em [Mermaid](https://mermaid.js.org/)/PlantUML, não imagens exportadas. São diffáveis e não apodrecem em silêncio.
3. **Diagrama de banco gerado do schema** — a modelagem de dados nasce do `schema.prisma` real, então acompanha o código automaticamente.
4. **Rastreabilidade por ID** — cada requisito tem um identificador estável (`RF-…`) referenciado em commits, testes e issues. É isso que permite seguir o fio do requisito até o teste que o cobre.

## Convenções gerais

- Idioma: **português** (código, domínio, rotas e copy).
- Sem emojis; hierarquia por tipografia e estrutura.
- Cada artefato aponta para as fontes que o originaram (rastreabilidade para cima) e para o que ele habilita (rastreabilidade para baixo).
