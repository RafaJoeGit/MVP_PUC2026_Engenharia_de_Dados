# Camada Silver

## Tabela

`workspace.default.silver_listings`

## Transformações aplicadas

A partir da `bronze_listings`, foram aplicadas as seguintes
transformações:

- **Tipagem de colunas:** conversão de `price` (removendo símbolo
  monetário e convertendo para `double`), `bedrooms` e `beds`
  (convertidos para `double`), `review_scores_rating` (`double`).
- **Padronização de texto:** normalização de `neighbourhood_cleansed`
  (remoção de espaços extras / padronização de capitalização, se
  aplicável).
- **Remoção de duplicatas:** verificação de `listing_id` duplicado
  (nenhuma duplicata encontrada / X duplicatas removidas — *preencher
  com o resultado real*).
- **Tratamento de nulos:** valores ausentes em `price`, `bedrooms`,
  `beds` e `review_scores_rating` foram **preservados como nulos**
  (não imputados), por não haver base confiável para substituição sem
  introduzir viés nas análises posteriores.
- **Metadados de controle:** adicionada coluna de data de ingestão
  para rastreabilidade (*se aplicável no seu notebook*).

## Observação sobre `instant_bookable` e disponibilidade

- `instant_bookable` chegou 100% nula na fonte e foi mantida como está
  na Silver; não foi utilizada em nenhuma análise posterior.
- `availability_30`, `availability_60` e `availability_90` foram
  mantidas como `string` nesta camada (não convertidas para `int`) —
  registrado como oportunidade de melhoria na Autoavaliação.

