- CRAWLER FULL:

product_view.css('.produto-principal img').xpath('@src').extract()[0]

$ scrapy crawl [SPIDER_NAME]
Exs.:
    - Simples: 	 
	    $ scrapy crawl walmart

    - Persistindo log:
	    $ nohup scrapy crawl walmart -s CONCURRENT_REQUESTS=24 -s CONCURRENT_REQUESTS_PER_DOMAIN=24 -s CONCURRENT_REQUESTS_PER_IP=24 > /spider_out/walmart.log

    - Persistindo log, e gerando um CSV com os itens scrapeados:
	    $ nohup scrapy crawl pontofrio -s CONCURRENT_REQUESTS=16 -s CONCURRENT_REQUESTS_PER_DOMAIN=16 -s CONCURRENT_REQUESTS_PER_IP=16 -s AUTOTHROTTLE_ENABLED=0  -o /spider_out/pontofrio.csv > /spider_out/pontofrio2.log &
	

# CRAWLER APENAS UMA PÁGINA:

## Página que NÃO precisa de login:

    $ scrapy parse '[URL]' --spider=[SPIDER_NAME] --callback=[PARSE_CALLBACK_NAME] --pipelines --rules --depth 20

    Ex.:
        $ scrapy parse 'http://www.ricardoeletro.com.br/Produto/Refrigerador-Geladeira-Electrolux-Frost-Free-2-Portas-380-Litros-Inox-DW42X/256-270-274-85169' --spider=ricardo_eletro --callback=parse_product --pipelines  --rules --depth 20

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

## Verificar quantas páginas estão sendo scrapeadas (efetivamente parseadas) por minuto:
	$ tail -f /spider_out/tireshop.log | grep scraped

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

CURIOSIDADE:

Poderia usar o wget para descobrir todos os produtos de um site:

	$ wget --wait 1 --recursive --no-clobber --convert-links --follow-tags=a --background --domains maisqpneu.com.br http://www.maisqpneu.com.br

Ele gera um arquivo wget[*].log. 

Nesse arquivo, eu posso aplicar um grep:

	$ tail -f wget-log | grep 'http:'

Para assim descobrir todas as URLs, para daí descobrir os padrões das que são de produto. 

---

Ao debugar um item com o scrapy parse, como pegar os valores coletados para ele:

	item.load_item()

	OR

	Suposing:
		item.add_css('name', '.productName::text')                                                                                                                 
		item.add_xpath('image_url',  'id("image-main")/@src')    

	I can get does values on ipdb with:
		item.get_collected_values('name')
		item.get_collected_values('image_url')

---

Pegar informações das tags meta property og (Facebook Open Graph) via xpath:

E.g. 1: <meta property="og:product:price:amount" content="R$2.399,00"/>
	response.xpath('/html/head/meta[@property="og:product:price:amount"]/@content').extract()

E.g. 2: <meta property="og:title" content="Roçadeira Florestal Husqvarna 345FR"/>
	response.xpath('/html/head/meta[@property="og:title"]/@content').extract()

---

Pegar o text de todos os li abaixo de um ul:
	
E.g.:
	response.xpath('/html/body/div/div/div[3]/div/div/div/div/ul/descendant::*/text()').extract()


---



