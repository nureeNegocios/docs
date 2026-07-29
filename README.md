# nuree-docs

Documentação de engenharia da **Nuree Negócios**, publicada com [MkDocs](https://www.mkdocs.org/) + [Material](https://squidfunk.github.io/mkdocs-material/).

Objetivo: enxergar todo o caminho de cada funcionalidade — do requisito ao design (domínio, banco, casos de uso, estados), à implementação e aos testes.

Publicado em: https://nureenegocios.github.io/docs/

## Rodar local

```bash
pip install mkdocs-material
mkdocs serve
# http://127.0.0.1:8000
```

## Publicação

Push na branch `main` dispara o workflow `.github/workflows/deploy.yml`, que faz o build e publica no GitHub Pages.

## Estrutura

```
docs/
  index.md                     # portal
  convencoes/requisitos.md     # como escrevemos requisitos (EARS + rastreabilidade)
  app-nuree/
    index.md
    requisitos.md              # requisitos do app-nuree
```
