# Camada Silver

## Tabela

`workspace.default.silver_listings`

## Transformações aplicadas

A partir da `bronze_listings`, foram aplicadas as seguintes
transformações:

- **Tipagem de colunas:** `price` teve o símbolo monetário e vírgulas
  de milhar removidos via expressão regular antes da conversão para
  `double`; `bedrooms`, `beds` e `review_scores_rating` convertidos
  para `double`; `accommodates`, `availability_365` e
  `number_of_reviews` convertidos para `int`. `neighbourhood_cleansed`,
  `room_type`, `host_is_superhost`, `instant_bookable`,
  `availability_30`, `availability_60`, `availability_90` e
  `last_scraped` foram mantidos como `string`.
- **Flags de qualidade:** foram adicionadas colunas booleanas
  (`flag_price_null`, `flag_price_invalid`, `flag_bedrooms_null`,
  `flag_beds_null`, `flag_rating_null`) para sinalizar, registro a
  registro, valores nulos ou inválidos (ex.: preço ≤ 0) sem removê-los
  da base — essas flags dão suporte direto à análise de qualidade
  feita na etapa seguinte.
- **Verificação de duplicatas:** comparação entre o total de registros
  e a contagem de valores distintos de `id`. Resultado: **0 registros
  duplicados**.
- **Tratamento de nulos:** valores ausentes em `price`, `bedrooms`,
  `beds` e `review_scores_rating` foram **preservados como nulos**
  (não imputados), por não haver base confiável para substituição sem
  introduzir viés nas análises posteriores.

## Observação sobre `instant_bookable` e disponibilidade

- `instant_bookable` chegou 100% nula na fonte e foi mantida como está
  na Silver; não foi utilizada em nenhuma análise posterior.
- `availability_30`, `availability_60` e `availability_90` foram
  mantidas como `string` nesta camada (não convertidas para `int`) —
  registrado como oportunidade de melhoria na Autoavaliação.

## Observação sobre o identificador do anúncio

O identificador chega na Silver como `id`. Em etapas posteriores da
modelagem dimensional, esse campo é renomeado para `listing_id`. Veja
`06_modelo_dimensional.md` para o detalhamento dessa transição e uma
inconsistência de nomenclatura ainda pendente entre `fact_listing` e
`fact_listing_dimensional`.
