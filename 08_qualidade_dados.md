# Qualidade dos dados

A `fact_listing_dimensional` possui 42.354 registros.

  Atributo                      Nulos
  -------------------------- --------
  `listing_id`                      0
  `neighbourhood_key`               0
  `neighbourhood_cleansed`          0
  `room_type_key`                   0
  `room_type`                       0
  `price`                         522
  `accommodates`                    0
  `bedrooms`                    5.666
  `beds`                          572
  `availability_30`                 0
  `availability_60`                 0
  `availability_90`                 0
  `availability_365`                0
  `number_of_reviews`               0
  `review_scores_rating`        5.771
  `host_is_superhost`               0
  `instant_bookable`           42.354
  `last_scraped`                    0

## Tratamentos

Valores ausentes não foram artificialmente imputados quando não havia
base confiável. Foram identificados 3.116 outliers de preço; eles foram
mantidos e considerados na interpretação.

`instant_bookable` está 100% nulo e, portanto, não foi utilizado para
conclusões. `availability_30`, `availability_60` e `availability_90`
estão tipados como string apesar de representarem quantidades de dias;
isso é registrado como oportunidade de melhoria.
