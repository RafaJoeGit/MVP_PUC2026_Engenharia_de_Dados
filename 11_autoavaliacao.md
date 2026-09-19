# Autoavaliação

## Objetivos alcançados

-   Coleta: concluída.
-   Persistência: concluída.
-   Bronze: concluída.
-   Silver: concluída.
-   Modelagem dimensional: concluída.
-   Gold: concluída.
-   Qualidade: concluída.
-   Análises: concluídas.
-   Discussão: concluída.
-   Documentação: em fase final de organização da entrega.

## Dificuldades

As principais dificuldades estiveram relacionadas a decisões sem
resposta óbvia, que exigiram julgamento e não apenas execução técnica:

- **Valores ausentes:** optei por não imputar `price`, `bedrooms`,
  `beds` e `review_scores_rating` porque não havia uma base confiável
  (ex: média por bairro ou por tipo) que não introduzisse viés nas
  análises de preço e avaliação. Prefiri manter os nulos explícitos e
  excluí-los pontualmente nos cálculos que dependiam desses campos,
  a inventar um valor plausível que poderia distorcer as correlações.
- **Outliers de preço:** os 3.116 outliers identificados não foram
  removidos porque, num mercado real de hospedagem, preços muito acima
  da média podem refletir imóveis de luxo genuínos, não
  necessariamente erros de cadastro. Removê-los sem uma regra de
  negócio clara poderia esconder um segmento real do mercado.
- **`instant_bookable` 100% nulo:** essa coluna chegou completamente
  vazia na fonte. Decidi não tentar inferir esse valor a partir de
  outras colunas e simplesmente excluí-la das conclusões, deixando
  isso registrado como uma limitação do dataset, não do pipeline.
- **Organização das camadas:** definir onde terminava a
  responsabilidade da Silver e começava a da Gold exigiu iteração —
  inicialmente algumas agregações estavam sendo feitas cedo demais
  (ainda na Silver), o que foi corrigido para manter a Silver apenas
  com dados limpos e tipados, sem agregações de negócio.
- **Modelo dimensional:** decidir entre um esquema estrela mais
  granular (com mais dimensões) ou uma tabela dimensional mais larga
  foi outro ponto de dúvida; optei pela segunda abordagem
  (`fact_listing_dimensional` mais larga) dado o volume moderado de
  dados (42.354 registros) e o tempo disponível para o MVP.


## Decisões metodológicas

Valores ausentes não foram preenchidos artificialmente quando não havia
base confiável. Outliers de preço foram mantidos e documentados.
`instant_bookable`, por estar totalmente nulo, não foi utilizado para
conclusões.

## Melhorias futuras

-   converter os campos de disponibilidade de 30, 60 e 90 dias para
    tipos numéricos;
-   investigar a origem dos valores ausentes;
-   aprimorar o tratamento de outliers;
-   integrar novas fontes;
-   automatizar atualizações e monitoramento da qualidade.
