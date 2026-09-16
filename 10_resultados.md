# Resultados e discussão

## Pergunta 1 --- Preços por bairro

A análise por `neighbourhood_cleansed` permite comparar preços médios
entre bairros e evidencia heterogeneidade de preços entre localizações.

Para maior representatividade, a comparação pode ser observada também
com o recorte de bairros que possuem pelo menos 20 anúncios.

**Status:** respondida.

## Pergunta 2 --- Tipo de acomodação e preço

Preços médios: Hotel room R\$ 574,13; Entire home/apt R\$ 438,22;
Private room R\$ 299,98; Shared room R\$ 151,52.

Há diferenças claras entre categorias. As médias não devem ser
interpretadas como efeito causal isolado do tipo de acomodação.

**Status:** respondida.

## Pergunta 3 --- Capacidade, quartos, camas e preço

Correlações: `accommodates` 0,2111; `bedrooms` 0,1641; `beds` 0,1172. As
relações são positivas e fracas.

**Status:** respondida.

## Pergunta 4 --- Avaliação e preço

Correlação `review_scores_rating` × `price`: **0,0018**. Não foi
identificada relação linear relevante entre as variáveis no conjunto
analisado.

**Status:** respondida.

## Pergunta 5 --- Disponibilidade

A disponibilidade foi analisada em janelas de 30, 60, 90 e 365 dias e
comparada por bairro e tipo de acomodação. Foram consideradas 119
combinações bairro × tipo com pelo menos 20 anúncios.

**Status:** respondida.

## Pergunta 6 --- Perfil favorável

Foi utilizado um índice de oportunidade combinando preço, avaliação e
disponibilidade, com pesos de 40% avaliação, 30% disponibilidade e 30% preço. O resultado
depende desses pesos.

**Status:** respondida com ressalva metodológica.

## Discussão geral

Os dados mostram diferenças relevantes de preço associadas a localização
e tipo de acomodação. Características físicas apresentam relações
positivas, porém fracas, com preço. A avaliação apresentou correlação
praticamente nula com preço. A disponibilidade varia entre diferentes
combinações de bairro e tipo de acomodação.

## Limitações

O conjunto representa um recorte temporal; possui valores ausentes e
outliers; `instant_bookable` não possui informação; três campos de
disponibilidade estão armazenados como string; e o índice de
oportunidade depende dos pesos definidos.
