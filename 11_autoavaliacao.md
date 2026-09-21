# Autoavaliação

## Objetivos alcançados

- Coleta: concluída.
- Persistência: concluída.
- Bronze: concluída.
- Silver: concluída.
- Modelagem dimensional: concluída.
- Gold: concluída.
- Qualidade: concluída.
- Análises: concluídas.
- Discussão: concluída.
- Documentação: concluída.

Todas as 6 perguntas de negócio definidas no início do projeto foram
respondidas, cada uma com discussão dos resultados. A Pergunta 6
(índice de oportunidade) foi respondida com uma ressalva metodológica
explícita sobre a dependência dos pesos escolhidos.

## Dificuldades

As principais dificuldades estiveram relacionadas a decisões sem
resposta óbvia, que exigiram julgamento e não apenas execução técnica:

- **Valores ausentes:** optei por não imputar `price`, `bedrooms`,
  `beds` e `review_scores_rating` porque não havia uma base confiável
  (ex: média por bairro ou por tipo) que não introduzisse viés nas
  análises de preço e avaliação. Prefiri manter os nulos explícitos e
  excluí-los pontualmente nos cálculos que dependiam desses campos, a
  inventar um valor plausível que poderia distorcer as correlações.
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
- **Inconsistência de nomenclatura entre tabelas fato:** durante a
  revisão final, identifiquei que `fact_listing` e
  `fact_listing_dimensional` usavam nomes diferentes (`id` e
  `listing_id`) para o mesmo identificador de anúncio, resultado de
  uma renomeação aplicada só em parte do pipeline. Corrigi o notebook
  Gold para padronizar `listing_id` em ambas as tabelas.
- **Tabelas órfãs no workspace:** ao revisar o projeto, encontrei três
  tabelas Gold remanescentes de uma versão anterior do notebook (em
  português: `gold_preco_bairro`, `gold_preco_tipo_imovel`,
  `gold_avaliacao_preco`), que não eram mais geradas pelo pipeline
  atual mas continuavam no workspace. Um dos gráficos de análise ainda
  apontava para uma delas, o que poderia ter gerado uma inconsistência
  não percebida entre o gráfico e os resultados documentados. Corrigi
  o gráfico para usar a tabela oficial e removi as três tabelas
  órfãs, deixando o workspace com apenas as 15 tabelas realmente
  produzidas pelo pipeline atual.

## Decisões metodológicas

Valores ausentes não foram preenchidos artificialmente quando não
havia base confiável. Outliers de preço foram mantidos e
documentados. `instant_bookable`, por estar totalmente nulo, não foi
utilizado para conclusões. O identificador do anúncio foi padronizado
como `listing_id` em todas as tabelas fato do modelo.

## Melhorias futuras

- Converter os campos de disponibilidade de 30, 60 e 90 dias para
  tipos numéricos.
- Investigar a origem dos valores ausentes.
- Aprimorar o tratamento de outliers.
- Integrar novas fontes.
- Automatizar atualizações e monitoramento da qualidade, incluindo uma
  checagem periódica de tabelas órfãs no workspace.
