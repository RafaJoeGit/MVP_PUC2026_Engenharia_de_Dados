# Catálogo de dados --- fact_listing_dimensional

  -----------------------------------------------------------------------------------
  Campo                      Tipo              Descrição         Domínio/observação
  -------------------------- ----------------- ----------------- --------------------
  `listing_id`               bigint            Identificador do  42.354 distintos;
                                               anúncio           sem nulos

  `neighbourhood_key`        int               Chave da dimensão 96 distintos
                                               de bairro         

  `neighbourhood_cleansed`   string            Bairro do anúncio 96 distintos

  `room_type_key`            int               Chave da dimensão 4 distintos
                                               de acomodação     

  `room_type`                string            Tipo de           4 categorias
                                               acomodação        

  `price`                    double            Preço do anúncio  mínimo R\$ 11,53;
                                                                 máximo R\$
                                                                 57.127,00; 522 nulos

  `accommodates`             int               Capacidade de     mínimo 1; máximo 16
                                               hóspedes          

  `bedrooms`                 double            Quantidade de     mínimo 0; máximo 46;
                                               quartos           5.666 nulos

  `beds`                     double            Quantidade de     mínimo 1; máximo 64;
                                               camas             572 nulos

  `availability_30`          string            Dias disponíveis  domínio observado
                                               em 30 dias        0--30

  `availability_60`          string            Dias disponíveis  domínio observado
                                               em 60 dias        0--60

  `availability_90`          string            Dias disponíveis  domínio observado
                                               em 90 dias        0--90

  `availability_365`         int               Dias disponíveis  domínio observado
                                               em 365 dias       0--365

  `number_of_reviews`        int               Número de         mínimo 0; máximo
                                               avaliações        3.314

  `review_scores_rating`     double            Nota média do     mínimo 0; máximo 5;
                                               anúncio           5.771 nulos

  `host_is_superhost`        string            Indicador de      `t` / `f`
                                               Superhost         

  `instant_bookable`         string            Indicador de      42.354 nulos
                                               reserva           
                                               instantânea       

  `last_scraped`             string            Data da última    3 datas; 2026-06-14
                                               coleta            a 2026-06-16
  -----------------------------------------------------------------------------------

Os valores acima correspondem à auditoria realizada no Databricks.
