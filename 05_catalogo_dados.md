# Catálogo de Dados — `fact_listing_dimensional`

Esta tabela é a fonte única de consulta para todas as análises do
projeto. Reúne 42.354 anúncios de hospedagem em São Paulo, com
atributos originais da fonte (Inside Airbnb) já limpos e tipados na
camada Silver, mais as chaves das dimensões `dim_neighbourhood` e
`dim_room_type`.

## Convenções desta tabela

- **Origem**: nome e camada de onde o campo vem antes de chegar à
  Gold.
- **Transformação**: o que foi feito ao campo entre a Bronze e a
  Silver (ver [`04_silver.md`](04_silver.md) para o código completo).
- **Nulos**: contagem absoluta sobre os 42.354 registros, quando
  auditada. Campos marcados como *"não auditado"* ainda precisam da
  contagem — o SQL para isso está na seção final deste documento.

---

## Dimensão: identificação

| Campo | Tipo | Origem | Transformação | Domínio / Nulos |
|---|---|---|---|---|
| `listing_id` | bigint | `id` (Bronze) | Renomeado para `listing_id` na camada Gold, para padronizar com `fact_listing_dimensional` | 42.354 valores distintos; 0 nulos |

## Dimensão: localização e tipo

| Campo | Tipo | Origem | Transformação | Domínio / Nulos |
|---|---|---|---|---|
| `neighbourhood_key` | int | Gerada em `dim_neighbourhood` (Gold) | Chave sequencial (`row_number`) sobre bairros distintos | 96 valores distintos |
| `neighbourhood_cleansed` | string | `neighbourhood_cleansed` (Bronze/Silver) | Mantida como string, sem padronização de texto | 96 categorias — lista completa disponível na tabela `dim_neighbourhood` |
| `room_type_key` | int | Gerada em `dim_room_type` (Gold) | Chave sequencial (`row_number`) sobre tipos distintos | 4 valores distintos |
| `room_type` | string | `room_type` (Bronze/Silver) | Mantida como string | 4 categorias: `Entire home/apt`, `Private room`, `Shared room`, `Hotel room` |

## Dimensão: capacidade e estrutura do imóvel

| Campo | Tipo | Origem | Transformação | Domínio / Nulos |
|---|---|---|---|---|
| `accommodates` | int | `accommodates` (Bronze) | Convertido para `int` na Silver | mínimo 1; máximo 16; 0 nulos |
| `bedrooms` | double | `bedrooms` (Bronze) | Convertido para `double` na Silver | mínimo 0; máximo 46; **5.666 nulos** |
| `beds` | double | `beds` (Bronze) | Convertido para `double` na Silver | mínimo 1; máximo 64; **572 nulos** |

## Dimensão: preço

| Campo | Tipo | Origem | Transformação | Domínio / Nulos |
|---|---|---|---|---|
| `price` | double | `price` (Bronze, formato texto com símbolo monetário, ex.: `"R$1,200.00"`) | Símbolo monetário e vírgula de milhar removidos via regex; convertido para `double` na Silver | mínimo R$ 11,53; máximo R$ 57.127,00; **522 nulos** |

**Regra de qualidade associada:** a camada Silver adiciona a coluna
`flag_price_invalid`, que sinaliza registros com `price <= 0` sem
removê-los da base — nenhum desses casos foi encontrado na auditoria
até o momento (ver `08_qualidade_dados.md`).

## Dimensão: disponibilidade

| Campo | Tipo | Origem | Transformação | Domínio / Nulos |
|---|---|---|---|---|
| `availability_30` | string | `availability_30` (Bronze) | Mantido como `string` na Silver (não convertido para numérico — ver Limitações) | domínio observado 0–30; 0 nulos |
| `availability_60` | string | `availability_60` (Bronze) | Idem | domínio observado 0–60; 0 nulos |
| `availability_90` | string | `availability_90` (Bronze) | Idem | domínio observado 0–90; 0 nulos |
| `availability_365` | int | `availability_365` (Bronze) | Convertido para `int` na Silver | domínio observado 0–365; 0 nulos |

## Dimensão: reputação e reservas

| Campo | Tipo | Origem | Transformação | Domínio / Nulos |
|---|---|---|---|---|
| `number_of_reviews` | int | `number_of_reviews` (Bronze) | Convertido para `int` na Silver | mínimo 0; máximo 3.314; 0 nulos |
| `review_scores_rating` | double | `review_scores_rating` (Bronze) | Convertido para `double` na Silver | mínimo 0; máximo 5; **5.771 nulos** |
| `host_is_superhost` | string | `host_is_superhost` (Bronze) | Mantido como `string` | domínio observado: `t` / `f`; 0 nulos |
| `instant_bookable` | string | `instant_bookable` (Bronze) | Mantido como `string` | **100% nulo (42.354 de 42.354)** — coluna vazia na própria fonte; excluída de todas as análises |

## Dimensão: controle

| Campo | Tipo | Origem | Transformação | Domínio / Nulos |
|---|---|---|---|---|
| `last_scraped` | string | `last_scraped` (Bronze) | Mantido como `string` (não convertido para `date`) | 3 valores distintos, entre 2026-06-14 e 2026-06-16; 0 nulos |

---

## Flags de qualidade (camada Silver, não presentes na Gold final)

A camada Silver adiciona 5 colunas de flag para apoiar a auditoria de
qualidade, que **não são propagadas para `fact_listing_dimensional`**
(ficam disponíveis em `silver_listings` para quem quiser investigar
registro a registro):

| Flag | Sinaliza |
|---|---|
| `flag_price_null` | `price` nulo |
| `flag_price_invalid` | `price` não nulo, porém ≤ 0 |
| `flag_bedrooms_null` | `bedrooms` nulo |
| `flag_beds_null` | `beds` nulo |
| `flag_rating_null` | `review_scores_rating` nulo |

---

## Auditoria de nulos — concluída

A auditoria completa de nulos foi rodada sobre os 42.354 registros de
`fact_listing_dimensional`. Resultado: `accommodates`,
`availability_30`, `availability_60`, `availability_90`,
`availability_365`, `number_of_reviews`, `host_is_superhost`,
`last_scraped`, `neighbourhood_cleansed` e `room_type` não apresentam
nenhum valor nulo. Os únicos campos com nulos no dataset são `price`
(522), `bedrooms` (5.666), `beds` (572), `review_scores_rating`
(5.771) e `instant_bookable` (100%, sem exceção) — todos já refletidos
nas tabelas acima.
