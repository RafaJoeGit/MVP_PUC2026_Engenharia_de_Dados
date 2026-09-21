# 🏠 Pipeline de Dados em Nuvem — Inside Airbnb São Paulo

> MVP de Engenharia de Dados (PUC 2026) — construção de um pipeline completo
> na nuvem, do dado bruto à resposta de negócio, usando o Databricks Free
> Edition e dados públicos do Inside Airbnb.

---

## 📖 A história por trás do projeto

Imagine que você está pensando em anunciar um imóvel para locação por
temporada em São Paulo — ou já tem um anúncio no ar e quer saber se está
competitivo. Bairro faz diferença? Vale mais a pena um apartamento inteiro
ou um quarto privativo? Ter uma nota alta realmente ajuda a cobrar mais
caro? Essas perguntas parecem simples, mas sem dados por trás, a resposta
vira "achismo".

Este projeto nasceu para resolver exatamente isso: transformar os dados
públicos e brutos do Inside Airbnb — uma planilha gigante e desorganizada
de mais de 42 mil anúncios — em respostas confiáveis para quem precisa
tomar uma decisão real sobre hospedagem em São Paulo. Para chegar lá, o
projeto percorreu, na prática, todas as etapas que um Engenheiro de Dados
enfrenta no mercado: entender o problema antes de tocar em uma linha de
código, buscar e validar a fonte de dados, subir tudo para a nuvem,
organizar o caos em camadas de confiança progressiva, e só então analisar
e responder.

O resultado é um pipeline de ponta a ponta rodando inteiramente no
**Databricks Free Edition**, seguindo a arquitetura de Lakehouse com
camadas **Bronze → Silver → Gold**, hoje capaz de responder 6 perguntas de
negócio concretas sobre o mercado de hospedagem paulistano.

---

## 🎯 1. Objetivo

O mercado de hospedagem de curta temporada em São Paulo é altamente
heterogêneo: preços, avaliações e disponibilidade variam fortemente entre
bairros e tipos de acomodação. Um anfitrião decidindo onde anunciar um
novo imóvel — ou um investidor avaliando a viabilidade de uma propriedade
— precisa entender quais fatores realmente influenciam o desempenho de um
anúncio antes de agir.

Este projeto constrói um pipeline de dados em nuvem para apoiar exatamente
essa decisão, respondendo a 6 perguntas de negócio:

1. Quais bairros apresentam os maiores e menores preços médios?
2. Qual é a relação entre o tipo de acomodação e o preço?
3. Qual é a relação entre capacidade, quartos, camas e preço?
4. Anúncios com melhores avaliações apresentam preços maiores?
5. Como a disponibilidade varia por bairro e tipo de acomodação?
6. Quais perfis combinam preço, avaliação e disponibilidade de forma
   favorável?

Todas as perguntas foram mantidas mesmo quando alguma análise apresentou
limitações — a discussão de cada uma está na seção de Resultados.

📄 Detalhes completos: [`01_objetivo_e_perguntas.md`](01_objetivo_e_perguntas.md)

---

## 🔎 2. Coleta

A fonte de dados é o **Inside Airbnb**, projeto independente que publica
dados públicos e atualizados de anúncios de hospedagem em dezenas de
cidades pelo mundo — incluindo São Paulo. O arquivo utilizado foi o
`listings.csv.gz`, com dados coletados diretamente do site do Airbnb entre
14 e 16 de junho de 2026.

Os dados são disponibilizados sob licença **Creative Commons Attribution
4.0 International (CC BY 4.0)**, que permite uso acadêmico mediante
atribuição da fonte — condição respeitada em toda a documentação deste
projeto.

📄 Detalhes completos: [`02_coleta.md`](02_coleta.md)

---

## 🏗️ 3. Arquitetura — a jornada do dado bruto ao dado pronto

O pipeline segue a **Arquitetura Medalhão**, o padrão de organização em
camadas do Databricks:

```
Coleta → 🥉 Bronze → 🥈 Silver → 🔷 Modelagem Dimensional → 🥇 Gold → 📊 Análise
```

| Camada | O que acontece | Tabela(s) |
|---|---|---|
| 🥉 **Bronze** | O dado chega exatamente como veio da fonte, sem alterações — preservado para rastreabilidade | `bronze_listings` |
| 🥈 **Silver** | Limpeza e tipagem: preço convertido de texto para número, colunas tipadas corretamente, flags de qualidade adicionadas para sinalizar nulos e valores inválidos | `silver_listings` |
| 🔷 **Modelagem** | Estrutura dimensional construída a partir da Silver, com chaves e dimensões próprias | `fact_listing`, `fact_listing_dimensional`, `dim_neighbourhood`, `dim_room_type` |
| 🥇 **Gold** | Dados agregados e prontos para responder cada pergunta de negócio | tabelas específicas de preço, avaliação, disponibilidade, oportunidade e perfil |

📄 Detalhes completos: [`03_bronze.md`](03_bronze.md) ·
[`04_silver.md`](04_silver.md) ·
[`06_modelo_dimensional.md`](06_modelo_dimensional.md) ·
[`07_tabelas_gold.md`](07_tabelas_gold.md)

### Um detalhe de bastidor: consistência importa

Durante a revisão final do projeto, identificamos que as duas tabelas fato
(`fact_listing` e `fact_listing_dimensional`) usavam nomes diferentes para
o mesmo identificador de anúncio — resultado de uma renomeação aplicada
só em parte do pipeline original. Corrigimos o notebook Gold para
padronizar `listing_id` em ambas as tabelas, e reexecutamos todo o
pipeline para garantir que a correção se propagasse corretamente. Também
identificamos e removemos três tabelas remanescentes de uma versão
anterior do notebook Gold, que não eram mais geradas pelo pipeline atual
mas continuavam ocupando espaço no workspace. O inventário completo de
tabelas está documentado em `00_inventario_de_tabelas.ipynb`.

---

## 📐 4. Modelagem e Catálogo de Dados

O modelo segue um esquema dimensional com uma tabela fato larga
(`fact_listing_dimensional`, com **42.354 registros e 18 atributos**) e
duas dimensões (`dim_neighbourhood`, com 96 bairros, e `dim_room_type`,
com 4 tipos de acomodação). Cada campo está documentado no Catálogo de
Dados, incluindo domínio esperado, valores mínimos e máximos, e a
linhagem completa desde o arquivo original até a camada Gold.

📄 Detalhes completos: [`05_catalogo_dados.md`](05_catalogo_dados.md) ·
[`06_modelo_dimensional.md`](06_modelo_dimensional.md)

---

## ⚙️ 5. Carga (ETL)

A carga foi construída inteiramente em notebooks PySpark/SQL no
Databricks, com tabelas Delta em cada camada (`saveAsTable`, formato
`delta`, com `overwriteSchema` para permitir evolução de schema). As
transformações da camada Silver incluem tipagem de colunas, tratamento de
preço (remoção de símbolo monetário e conversão para número) e adição de
flags de qualidade que sinalizam, registro a registro, valores nulos ou
inválidos sem removê-los da base — dando suporte direto à análise de
qualidade da etapa seguinte.

📄 Detalhes completos: [`04_silver.md`](04_silver.md) ·
notebooks [`01_BRONZE.ipynb`](01_BRONZE.ipynb),
[`02_SILVER.ipynb`](02_SILVER.ipynb),
[`03_GOLD.ipynb`](03_GOLD.ipynb)

---

## 🔬 6. Qualidade dos Dados

Antes de confiar em qualquer resultado, avaliamos a qualidade de cada
atributo:

| Atributo | Nulos | Observação |
|---|---|---|
| `price` | 522 | Preservado como nulo, não imputado |
| `bedrooms` | 5.666 | Preservado como nulo, não imputado |
| `beds` | 572 | Preservado como nulo, não imputado |
| `review_scores_rating` | 5.771 | Preservado como nulo, não imputado |
| `instant_bookable` | 42.354 (100%) | Excluído de todas as conclusões |

Também identificamos **3.116 outliers de preço** — mantidos
intencionalmente, já que em um mercado real de hospedagem, preços muito
acima da média podem representar imóveis de luxo genuínos, não
necessariamente erros de cadastro.

📄 Detalhes completos: [`08_qualidade_dados.md`](08_qualidade_dados.md)

---

## 📊 7. Análises e Resultados

Com o dado limpo e confiável, chegou a hora de responder às perguntas
originais.

**Preço médio por tipo de acomodação:**
| Tipo | Preço médio |
|---|---|
| Hotel room | R$ 574,13 |
| Entire home/apt | R$ 438,22 |
| Private room | R$ 299,98 |
| Shared room | R$ 151,52 |

**Correlações com preço:**
| Variável | Correlação |
|---|---|
| `accommodates` | 0,2111 |
| `bedrooms` | 0,1641 |
| `beds` | 0,1172 |
| `review_scores_rating` | 0,0018 |

O destaque mais contraintuitivo do projeto: **a avaliação de um anúncio
tem correlação praticamente nula com o preço.** Isso não significa que
avaliação não importa — significa que, isoladamente, ela não explica por
que um anúncio custa mais que outro. Já a capacidade de hóspedes
(`accommodates`) é o fator com relação mais forte, ainda que classificada
como fraca em termos absolutos — um lembrete de que preço em hospedagem é
multifatorial, e nenhuma variável isolada conta a história toda.

Para a Pergunta 6 (quais perfis combinam preço, avaliação e
disponibilidade de forma favorável), construímos um índice de
oportunidade ponderado — resultado que depende dos pesos escolhidos e é
apresentado com essa ressalva metodológica explícita.

Todas as 10 visualizações produzidas durante a análise, com a conclusão
de cada uma e a pergunta de negócio à qual respondem, estão documentadas
na pasta [`analises_graficos/`](analises_graficos/).

📄 Detalhes completos: [`09_analises.md`](09_analises.md) ·
[`10_resultados.md`](10_resultados.md)

---

## ⚠️ 8. Limitações

- Há valores ausentes em `price`, `bedrooms`, `beds` e
  `review_scores_rating`, não imputados por decisão metodológica.
- `instant_bookable` está 100% nula na fonte e não foi utilizada em
  nenhuma conclusão.
- Os campos `availability_30`, `availability_60` e `availability_90`
  permanecem como `string` na camada Silver — oportunidade de melhoria
  futura.
- Correlação não implica causalidade: os resultados numéricos apontam
  associações, não relações de causa e efeito.
- O índice de oportunidade da Pergunta 6 é sensível aos pesos escolhidos.

---

## 🔮 9. Trabalhos futuros

- Converter os campos de disponibilidade para tipos numéricos.
- Investigar a origem dos valores ausentes.
- Testar sensibilidade do índice de oportunidade com diferentes
  combinações de pesos.
- Integrar novas fontes de dados (ex: dados históricos de preço por
  temporada).
- Automatizar atualizações e monitoramento contínuo de qualidade,
  incluindo checagem periódica de tabelas órfãs no workspace.

📄 Autoavaliação completa, com discussão de dificuldades e decisões
metodológicas: [`11_autoavaliacao.md`](11_autoavaliacao.md)

---

## 🗂️ Estrutura do repositório

```
├── 01_objetivo_e_perguntas.md      → Objetivo e perguntas de negócio
├── 02_coleta.md                     → Fonte, licença e coleta dos dados
├── 01_BRONZE.ipynb                  → Notebook de ingestão Bronze
├── 03_bronze.md                     → Documentação da camada Bronze
├── 02_SILVER.ipynb                  → Notebook de limpeza Silver
├── 04_silver.md                     → Documentação da camada Silver
├── 05_catalogo_dados.md             → Catálogo de dados completo
├── 06_modelo_dimensional.md         → Modelo dimensional e linhagem
├── 03_GOLD.ipynb                    → Notebook de modelagem e agregação Gold
├── 07_tabelas_gold.md               → Documentação das tabelas Gold
├── 08_qualidade_dados.md            → Análise de qualidade por atributo
├── 09_analises.md                   → Análises realizadas
├── 10_resultados.md                 → Resultados e discussão por pergunta
├── 11_autoavaliacao.md              → Autoavaliação final
├── 00_inventario_de_tabelas.ipynb   → Inventário de tabelas do workspace
├── analises_graficos/               → 10 visualizações + conclusões
└── README.md                        → Este arquivo
```

---

## 🛠️ Stack técnica

- **Plataforma:** Databricks Free Edition
- **Processamento:** Apache Spark (PySpark e Spark SQL)
- **Armazenamento:** Delta Lake (formato Delta, transações ACID)
- **Linguagens:** Python e SQL
- **Fonte de dados:** [Inside Airbnb](https://insideairbnb.com) — São Paulo

---

**Autor:** Rafael Oliveira — MVP individual, PUC 2026, Engenharia de Dados.

