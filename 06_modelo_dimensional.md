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

Contém o identificador do anúncio como `listing_id` (padronizado a
partir da coluna original `id` da Bronze/Silver) junto com os demais
atributos do anúncio, antes da junção com as dimensões. Foi
materializada como etapa intermediária da modelagem, servindo de base
para a construção da `fact_listing_dimensional`.

## Linhagem dos dados

- **Origem:** arquivo `listings.csv.gz`, obtido do portal Inside
  Airbnb (<https://insideairbnb.com/pt/get-the-data/>), referente à
  cidade de São Paulo, com dados coletados (scraped) entre
  2026-06-14 e 2026-06-16 (campo `last_scraped`).
- **Técnica de composição:** o arquivo já é disponibilizado pronto
  pelo Inside Airbnb (resultado de web scraping realizado pela própria
  organização mantenedora do projeto); não houve, deste lado, etapa de
  scraping adicional.
- **Trajetória no pipeline:**
  1. Ingestão do CSV compactado no Databricks Free Edition →
     `bronze_listings` (dado bruto, sem alterações).
  2. Limpeza, tipagem e padronização → `silver_listings` (ver
     `04_silver.md` para o detalhamento das transformações).
  3. Modelagem dimensional → `dim_neighbourhood`, `dim_room_type`,
     `fact_listing` e `fact_listing_dimensional`.
  4. Agregações analíticas → tabelas Gold específicas por pergunta de
     negócio (preço, avaliação, disponibilidade, oportunidade, perfil).

