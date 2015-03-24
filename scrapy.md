- CRAWLER FULL:

$ scrapy crawl [SPIDER_NAME]
Ex.:
    $ scrapy crawl walmart

- CRAWLER APENAS UMA PÁGINA:

$ scrapy parse '[URL]' --spider=[SPIDER_NAME] --callback=[PARSE_CALLBACK_NAME] --pipelines
Ex.:
    $ scrapy parse 'http://www.walmart.com.br/produto/Telefonia/Smartphones/Motorola/471929-smatphone-motorola-x1-xt1097-android-4-camera-13mp-tela-5-quad-core-32gb-3g-4g-wi-fi-bluetoot' --spider=walmart --callback=parse_product --pipelines
