# MVP_PUC2026_Engenharia_de_Dados
[README.md](https://github.com/user-attachments/files/32299463/README.md)
# Pipeline de Dados em Nuvem --- Inside Airbnb São Paulo

## Sobre o projeto

Projeto individual de construção de um pipeline de dados em nuvem
utilizando dados públicos do Inside Airbnb, com foco nos anúncios de
hospedagem em São Paulo. O desenvolvimento foi realizado no Databricks
Free Edition.

Fluxo: **Coleta → Bronze → Silver → Modelagem Dimensional → Gold →
Análise**.

## Perguntas de negócio

1.  Quais bairros apresentam os maiores e menores preços médios?
2.  Qual é a relação entre o tipo de acomodação e o preço?
3.  Qual é a relação entre capacidade, quartos, camas e preço?
4.  Anúncios com melhores avaliações apresentam preços maiores?
5.  Como a disponibilidade varia por bairro e tipo de acomodação?
6.  Quais perfis combinam preço, avaliação e disponibilidade de forma
    favorável?

## Arquitetura

-   Bronze: `bronze_listings`
-   Silver: `silver_listings`
-   Fato: `fact_listing` e `fact_listing_dimensional`
-   Dimensões: `dim_neighbourhood` e `dim_room_type`
-   Gold: tabelas analíticas específicas para preço, avaliação,
    disponibilidade, oportunidade e perfil.

## Volume

A `fact_listing_dimensional` possui **42.354 registros e 18 atributos**.

## Qualidade

Foram identificados valores nulos em `price` (522), `bedrooms` (5.666),
`beds` (572) e `review_scores_rating` (5.771). `instant_bookable`
apresentou 42.354 nulos. Foram identificados 3.116 outliers de preço,
mantidos e considerados na interpretação.

## Principais resultados

Preço médio por tipo: - Hotel room: R\$ 574,13 - Entire home/apt: R\$
438,22 - Private room: R\$ 299,98 - Shared room: R\$ 151,52

Correlações com preço: - `accommodates`: 0,2111 - `bedrooms`: 0,1602 -
`beds`: 0,1041 - `review_scores_rating`: 0,0018

## Limitações

Há valores ausentes, outliers de preço, ausência completa de informação
em `instant_bookable` e campos de disponibilidade de 30, 60 e 90 dias
armazenados como `string`. Correlação não implica causalidade.

## Evidências

Foram produzidas **10 visualizações finais** durante a etapa de análise.
Nenhuma visualização adicional é necessária.
