# Estrutura de documento

Padrão dos documentos (arquitetura e afins): **começo → meio → fim**, enxuto. Cada documento tem
**um dono** e não repete o que já está em outro.

## Começo

- Frontmatter (`hide: navigation`) e `# Título`.
- **Uma frase** de propósito — o que o documento contém. Se algo mora em outro doc, **aponte**
  ("ver X"), não repita.

## Meio

- O conteúdo, em seções `##`, tabelas quando couber.
- Só o necessário. Sem reexplicar teoria nem redizer decisões — isso é do diário.

## Fim

- `## Relacionados` — links para os documentos irmãos. Nada mais.

## Regras

- **Um dono por assunto:** características → `caracteristicas` · módulos/relações/eventos →
  `componentes` · testes → `fitness-functions` · imagens → `diagramas` · porquê/decisões → diário.
- **Rastreie** ao requisito (RF/RNF) em vez de explicar.
- **Não se repita** entre documentos.
