# Modelo dimensional

## Dimensão de bairro

`workspace.default.dim_neighbourhood`

  Campo                      Tipo
  -------------------------- --------
  `neighbourhood_key`        int
  `neighbourhood_cleansed`   string

## Dimensão de tipo de acomodação

`workspace.default.dim_room_type`

  Campo             Tipo
  ----------------- --------
  `room_type_key`   int
  `room_type`       string

Domínio observado de `room_type`: - Entire home/apt - Private room -
Shared room - Hotel room

## Fato dimensional

`workspace.default.fact_listing_dimensional`

Possui 42.354 registros e 18 atributos e reúne chaves das dimensões,
atributos dos anúncios e métricas usadas nas análises.

## Tabela fato

`workspace.default.fact_listing`

Foi materializada como parte da organização do pipeline antes/ao lado da
estrutura dimensional.
