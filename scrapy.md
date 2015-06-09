- CRAWLER FULL:

product_view.css('.produto-principal img').xpath('@src').extract()[0]

$ scrapy crawl [SPIDER_NAME]
Ex.:
    $ scrapy crawl walmart

# CRAWLER APENAS UMA PÁGINA:

## Página que NÃO precisa de login:

    $ scrapy parse '[URL]' --spider=[SPIDER_NAME] --callback=[PARSE_CALLBACK_NAME] --pipelines

    Ex.:
        $ scrapy parse 'http://www.walmart.com.br/produto/Telefonia/Smartphones/Motorola/471929-smatphone-motorola-x1-xt1097-android-4-camera-13mp-tela-5-quad-core-32gb-3g-4g-wi-fi-bluetoot' --spider=walmart --callback=parse_product --pipelines

## Scrapy parse de uma callback onde a página precisa de um login:

 Ex.:

    ### ORIGINAL PARA SCRAPY CRAWL:

    def login(self, response):
        yield FormRequest.from_response(
            response,
            formxpath='id("LoginForm")',
            formdata={'LoginForm[email]': 'andrealvesviana@hotmail.com',
                      'LoginForm[password]': '208765'},
            callback=self.parse
        )

    ### WORKAROUND PARA SCRAPY PARSE:

        def login(self, response):
            # DONE: revert the parameters "callback" and "dont_filter" below
            #       to run normally in production the "scrapy crawl" command.
            yield FormRequest.from_response(
                response,
                formxpath='id("LoginForm")',
                formdata={'LoginForm[email]': 'andrealvesviana@hotmail.com',
                          'LoginForm[password]': '208765'},
                callback=self.parse_product,
                dont_filter=True
            )

    ### How to run the scrapy parse command for a page that requires login:

        $ scrapy parse -d 5 'http://www.westwing.com.br/JOGO-DE-TACAS-DE-AGUA-MADU-360ML-668851.html?c=engrandeca-refeicoes1' --spider=westwing --callback=login --pipelines

IMPORTANTE: A alteração na callback "login" acima é apenas para fazer o "scrapy parse", para usar normalmente o "scrapy crawl" eu devo deixar como está em "ORIGINAL PARA SCRAPY CRAWL."


# SNIPPETS

## Gets availability based on the existance of a "buy" button "alt" attribute:

```
product_view = response.css('.container2')
try:
    button_buy = product_view.css(
        	'#button-buy img').xpath('@alt').extract()[0]
except:
    button_buy = ''
available = True if button_buy.lower().strip() == 'comprar' else False
item.add_value('available', available)
```

## Gets availability based on the existance of an input with "value" attribute:

```
product_view = response.css('.single')
try:
    button_buy = product_view.css(
        'input.bt_green').xpath('@value').extract()[0]
except:
    button_buy = ''
available = True if button_buy.lower().strip() == 'comprar' else False
item.add_value('available', available)
```

## Gets availability based on the existance of an input with "name" attribute
```
product_view = response.css('.container')
btn_xpath = '//*[@id="bloco_preco"]/div/form/div[2]/div/input[4]/@name'
try:
    button_buy = product_view.xpath(btn_xpath).extract()[0]
except:
    button_buy = ''
available = True if button_buy.lower().strip() == 'submit' else False
item.add_value('available', available)
```

