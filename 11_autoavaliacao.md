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

As principais dificuldades estiveram relacionadas ao tratamento de
valores ausentes, identificação de outliers, organização das camadas e
estruturação do modelo dimensional.

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
