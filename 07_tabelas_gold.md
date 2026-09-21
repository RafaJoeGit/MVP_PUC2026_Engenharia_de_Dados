07 — Tabelas Gold

1. Objetivo da camada Gold

A camada Gold organiza os dados tratados da camada Silver em tabelas
orientadas às perguntas de negócio do MVP.

Nesta versão final do pipeline, a camada Gold é construída a partir da
fact_listing_dimensional e das dimensões de bairro e tipo de
acomodação.

A versão final do pipeline utiliza 7 tabelas Gold.

> **Importante:** este documento considera somente as tabelas
> efetivamente utilizadas e validadas na versão final do pipeline.
> Tabelas antigas ou órfãs não fazem parte da arquitetura final.

────────

2. Tabelas da camada Gold

────────

Tabela                              Finalidade

────────

gold_neighbourhood_price          Preço médio dos anúncios por
bairro, com quantidade de anúncios.

gold_room_type_price              Quantidade de anúncios e preço
médio por tipo de acomodação.

gold_preco_capacidade             Relação entre capacidade de
hóspedes e preço médio.

gold_availability                 Disponibilidade dos anúncios por
bairro e tipo de acomodação.

gold_review_analysis              Análise conjunta de avaliação e
preço dos anúncios.

gold_opportunity                  Índice analítico que combina
avaliação, disponibilidade e preço.

gold_perfil_anuncio               Consolidação dos perfis de anúncios por tipo de acomodação.

────────

3. Detalhamento das tabelas

3.1 gold_neighbourhood_price

Objetivo: analisar a distribuição de preços médios entre os bairros
de São Paulo.

Principais campos: - neighbourhood_cleansed — bairro
padronizado. - preco_medio — preço médio dos anúncios. -
quantidade_anuncios — quantidade de anúncios considerados.

Pergunta de negócio relacionada:

> Quais bairros apresentam os maiores e menores preços médios?

A análise final utiliza também um recorte de representatividade,
considerando bairros com pelo menos 20 anúncios quando indicado na
consulta analítica.

────────

3.2 gold_room_type_price

Objetivo: comparar os tipos de acomodação disponíveis e seus preços
médios.

Principais campos: - room_type — tipo de acomodação. -
quantidade_anuncios — quantidade de anúncios. - preco_medio —
preço médio.

Pergunta de negócio relacionada:

> Qual é a relação entre o tipo de acomodação e o preço?

Na análise final, os preços médios observados foram:

Tipo de acomodação     Preço médio

────────

Hotel room              R$ 574,13
Entire home/apt         R$ 438,22
Private room            R$ 299,98
Shared room             R$ 151,52

────────

3.3 gold_preco_capacidade

Objetivo: analisar como a capacidade de hospedagem se relaciona com
o preço.

Principais campos: - accommodates — capacidade de hóspedes. -
preco_medio — preço médio. - quantidade_anuncios — quantidade de
anúncios.

Pergunta de negócio relacionada:

> Qual é a relação entre capacidade, quartos, camas e preço?

A tabela é utilizada em conjunto com a análise de correlação produzida
na etapa final.

────────

3.4 gold_availability

Objetivo: analisar a disponibilidade dos anúncios de acordo com
bairro e tipo de acomodação.

Principais dimensões: - neighbourhood_cleansed - room_type

Métricas de disponibilidade: - disponibilidade em 30 dias; -
disponibilidade em 60 dias; - disponibilidade em 90 dias; -
disponibilidade em 365 dias.

Pergunta de negócio relacionada:

> Como a disponibilidade varia por bairro e tipo de acomodação?

A análise final considera combinações de bairro e tipo de acomodação com
quantidade mínima de anúncios para reduzir distorções causadas por
grupos muito pequenos.

────────

3.5 gold_review_analysis

Objetivo: analisar conjuntamente a avaliação dos anúncios e seus
preços.

Principais informações: - avaliação média; - preço; - quantidade de
anúncios; - agrupamentos utilizados na análise.

Pergunta de negócio relacionada:

> Anúncios com melhores avaliações apresentam preços maiores?

A análise final encontrou correlação de 0,0018 entre
review_scores_rating e price, indicando uma relação linear muito
próxima de zero no conjunto analisado.

────────

3.6 gold_opportunity

Objetivo: criar um índice analítico para identificar perfis de
anúncios que combinam três dimensões:

• avaliação;
• disponibilidade;
• preço.

O índice utilizado no projeto atribui:

• 40% para avaliação;
• 30% para disponibilidade;
• 30% para preço.

Pergunta de negócio relacionada:

> Quais perfis combinam preço, avaliação e disponibilidade de forma
> favorável?

Esse índice é uma métrica criada especificamente para o MVP. Portanto,
seus resultados devem ser interpretados dentro dos pesos definidos neste
projeto, e não como uma medida universal de qualidade ou oportunidade.

────────

3.7 gold_perfil_anuncio

Objetivo: consolidar os perfis de anúncios por tipo de acomodação
para apoiar a interpretação dos resultados do MVP.

A tabela permite comparar os diferentes tipos de acomodação considerando
as métricas produzidas nas etapas anteriores.

Perguntas de negócio relacionadas:

• Qual é o comportamento dos diferentes perfis de acomodação?
• Como preço, avaliação e disponibilidade se combinam entre os perfis?

────────

4. Validação da camada Gold

Na validação final apresentada no notebook, foram confirmadas as
seguintes quantidades:

Objeto                         Registros

────────

fact_listing                    42.354
fact_listing_dimensional        42.354
dim_neighbourhood                   96
dim_room_type                        4
gold_neighbourhood_price            96
gold_room_type_price                 4
gold_preco_capacidade               16
gold_availability                  246
gold_review_analysis            36.316
gold_opportunity                36.316
gold_perfil_anuncio                  4

A validação confirma a existência das tabelas e a quantidade de
registros produzida pela versão final do pipeline.

────────

5. Relação com o modelo dimensional

A camada Gold utiliza como base a estrutura dimensional criada no
projeto:

fact_listing_dimensional
        │
        ├── dim_neighbourhood
        │
        └── dim_room_type
                │
                ▼
              GOLD
                │
                ├── gold_neighbourhood_price
                ├── gold_room_type_price
                ├── gold_preco_capacidade
                ├── gold_availability
                ├── gold_review_analysis
                ├── gold_opportunity
                └── gold_perfil_anuncio

A separação permite que os dados tratados e modelados sejam reutilizados
para diferentes análises de negócio sem alterar a camada Silver.

────────

6. Observações técnicas

6.1 Representatividade

Nas análises por bairro e por combinação de bairro/tipo de acomodação,
são utilizados filtros de quantidade mínima de anúncios quando
necessário.

Isso reduz o risco de interpretar como representativo um grupo formado
por poucos anúncios.

6.2 Disponibilidade

Na implementação atual, availability_365 é tratado como campo
numérico, enquanto availability_30, availability_60 e
availability_90 permanecem como strings na fact_listing_dimensional.

Essa é uma limitação técnica identificada para evolução futura do
pipeline.

6.3 Valores ausentes

A camada Gold não elimina automaticamente todos os valores ausentes. As
análises utilizam filtros apropriados para cada pergunta, preservando a
informação disponível na camada Silver.

6.4 Outliers de preço

Os outliers de preço identificados na etapa de qualidade são mantidos no
pipeline e sinalizados/considerados na análise, em vez de serem
simplesmente excluídos.

────────

7. Tabelas que não fazem parte da versão final

As seguintes tabelas aparecem em versões anteriores ou em células
antigas do desenvolvimento, mas não fazem parte da arquitetura final
validada:

• gold_avaliacao_preco
• gold_preco_bairro
• gold_preco_tipo_imovel

Elas não devem ser consideradas na documentação da versão final do MVP.

────────

8. Resumo final

A camada Gold final é composta por 7 tabelas analíticas, construídas
sobre o modelo dimensional e direcionadas às seis perguntas de negócio
do MVP.

A arquitetura final mantém a separação entre:

Bronze → Silver → Modelo Dimensional → Gold → Análises

e evita a inclusão de tabelas antigas ou não utilizadas na versão final
do projeto.