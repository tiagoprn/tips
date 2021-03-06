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

How to get the value from a hidden input from the "name" attribute. Eg.:

	html:
		<input type="hidden" name="productCodePost" value="431320">

	full xpath (the input is inside a form named "addToCartForm"):
		response.xpath('//*[@id="addToCartForm"]/input[@name="productCodePost"]/@value').extract()

---

How to get the "data-src" attribute from a img tag:
	E.g.: 
	<img class="lazyOwl" data-src="/medias/sys_master/media/media/hd1/h1d/h00/h00/8796739305502.jpg" alt="Pneu Goodyear Wrangler HP All Weather 235/60R18  103V " style="display: block;">

	CSS Path:
		response.css('.lazyOwl::attr("data-src")')[0].extract()

---


Para passar uma lista de URLs para ele crawler, a boa prática parece ser sobrescrever o método "start_requests" dos spiders desejados. (ou posso adicionar essa opção na classe pai). 
O Scrapy tem uma API para ser chamado por um outro script, por exemplo. Aqui está a API: http://doc.scrapy.org/en/latest/topics/api.html#topics-api


---

Good practices: http://doc.scrapy.org/en/latest/topics/practices.html

Command-line tool: http://doc.scrapy.org/en/latest/topics/commands.html

---

Pegar informações das tags meta via xpath:

E.g.:
	TAG.....: <meta itemprop="productID" content="sku:1025p" />

	XPATH...: response.xpath('.//*[@itemprop="productID"]/@content').extract()

---

How to strip html tags from a string to keep just the text:

(requires lxml:
    $ pip install lxml)


import lxml

# list of html tags with the element value inside of it
breadcumb = response.xpath('/html/body/div[4]/div[2]/'
                       'div[1]/ul/li/a').extract()

# generate a string from the list, and use lxml to strip the html tags.
# The result will be a lxml "element"
categories_element = lxml.html.document_fromstring(
','.join(breadcumb))

# Extract just the text from the element:
categories_txt_cleaned = categories_element.text_content()


NOTE: if you need to parse a real XML (not HTML), and get an attribute value,
you must use another method: lxml.etree.fromstring(). 

E.g.: 
    import lxml.etree
    xml_string = '<meta itemprop="seller/partnerName" content="MegaMamute"></meta>'
    # (if it complains of syntax error, remember XML needs a tag opening and 
    #  closing. E.g.: <meta></meta>. If you do not have the closing, you can 
    #  simply concatenate it. ;)
    meta_element = lxml.etree.fromstring(xml_string)                                                                                                                                                                                                        
    # suppose I want to get the value of the "content" attribute (which is "Megamamute"):
    print meta_element.get('content')

---

Descobrir quantidade mínima de produtos de um e-commerce (e.g. netshoes):

Fazer a seguinte query na busca do google:

    site:netshoes.com.br/produto

A lógica é: usar o prefixo "site:", seguido da URL geral de produtos do site.

Para descobrir a quantidade de urls do site (produtos ou não): 

    site:netshoes.com.br 

---

If you spider is slowly consuming all memory available, it may be that it's finding too many links to follow and therefore having to store them all in the interim period. 
So, the problem is that it is storing all of those links in memory instead of on disk and it eventually fills up all my memory.
The solution was to run scrapy with a job directory, and that forces them to be stored on disk where there is plenty of space.

$ scrapy crawl spider -s JOBDIR=somedirname

---

Como pegar um valor dentro de uma string de código javascript. 

Ex. (javascript): 

<script type="text/javascript">
    dataLayer.push({
        'event': 'productDetail',
        'ecommerce': {
            'detail': {
                'products': [{
                    'id': '11939',
                    'name': 'Gel de Espuma Dermo Lavante',
                    'category': '',
                    'price': '69.79',
                    'brand': 'Mustela',
                    'variant': '500 ml'
                }]
            },
            'impressions': []
        }
    });
</script>

Para extrair os dados com python, o segredo é eu manipular a string da função "dataLayer.push" para ficar somente com o valor entre chaves, que corresponde exatamente à um dict do python (basta eu aplicar um eval na string para extrair o dict e manipulá-lo). Ex.: 

    for script in response.xpath('//script/text()').extract():
        if 'dataLayer.push' in script:
            script_data = script.replace(
                'dataLayer.push(', '').replace(');', '').replace('\n','')
            script_data_as_dict = eval(script_data)
            try:
                price = script_data_as_dict[
                    'ecommerce']['detail']['products'][0]['price']
            except:
                price = ''

            try:
                brand = script_data_as_dict[
                    'ecommerce']['detail']['products'][0]['brand']
            except:
                brand = ''

            break
        else:
            continue

    if price:
        item.add_value('price', price)

    if brand:
        item.add_value('brand', brand)

---

Exemplo de como filtrar os links coletados por um crawler de uma forma mais precisa do que com simples regras "allow", "deny": 


```
# -*- coding: utf-8 -*-
import re
from functools import partial
from operator import methodcaller

from scrapy.contrib.spiders import CrawlSpider, Rule
from scrapy.contrib.linkextractors import LinkExtractor
from scrapy.contrib.loader.processor import Identity, MapCompose, TakeFirst

from ...item_loader import PrecificaItemLoader as ItemLoader
from ..items import ProductItem
from . import ExtraURLsSitemapSpider, PrecificaSpider


class PneuPlusOnlineSpider(PrecificaSpider, ExtraURLsSitemapSpider,
                           CrawlSpider):
    name = "pneu_plus_online"
    site = "Pneu Plus Online"
    compat_domain = "www.pneuplusonline.com.br"
    allowed_domains = ['pneuplusonline.com.br']
    start_urls = ['http://www.pneuplusonline.com.br/exibirpesquisa.php?'
                  'texto_pesquisa=pneu']
    sitemap_urls = ['http://www.pneuplusonline.com.br/sitemap.xml']

    duplicate_url_patterns = ['preco_ini', 'preco_fim',
                              'id_fabricante', 'rank',
                              'catalog']

    rules = [
        Rule(LinkExtractor(allow=['pesquisa'],
             deny=duplicate_url_patterns), follow=True,
             process_links='filter_links'),
        Rule(LinkExtractor(allow=['Pneu', 'Amortecedor', 'Camara', 'Roda'],
                           deny=duplicate_url_patterns),
             callback='parse_product',
             follow=True,
             process_links='filter_links'),
    ]

    # Fine tuning of the collected links, there are some I want to discard
    def filter_links(self, links):
        # baseDomain = self.get_base_domain( self.response_url)
        filteredLinks = []
        for link in links:
            pattern = re.compile('(id=\d+)', re.UNICODE | re.IGNORECASE)
            matches = re.findall(pattern, link.url)
            # More than 1 id means more than one filter, duplicating
            # requests and delaying the execution time. So, a url with
            # more than one id is ignored.
            if len(matches) < 2:
                filteredLinks.append(link)

        return filteredLinks

    def parse_product(self, response):
        item = ItemLoader(ProductItem(), response)
        item.default_output_processor = TakeFirst()

        item.add_css('name', '.titulo_h1_verproduto::text',
                     MapCompose(methodcaller('strip')))

        sku_info = response.css('#produtoCodigo::text').extract()
        for el in sku_info:
            matched = re.search(r'([digo])\s(?P<sku>.+)', el.strip())
            if matched:
                item.add_value('sku', matched.groupdict()['sku'])

        item.add_css('image_url', '#imagemMaior::attr(href)')

        item.add_css('description', '.new_produtoExtrastxt3.span::text')

        available = True if response.css('#btnAdicionar') else False
        item.add_value('available', available)

        yield item.load_item()
```

---

Maneira correta de fazer um seletor css explicitando toda a hierarquia dos
elementos: 

response.css('#fbits-breadcrumb>ol>li>a>span::text').extract()

---

Pegar o valor a partir de um name de um elemento. Ex.: 

<input type="hidden" name="idproduto" value="82802" /> 
ipdb> response.xpath("//input[contains(@name, 'idproduto')]//@value").extract_first()
u'82802'

<input type="hidden" name="valor" value="1.299,90" />
ipdb> response.xpath("//input[contains(@name, 'valor')]//@value").extract()
u'1.299,90'

<meta name="og:image" content="http://www.cdiscount-imagens.com.br/Control/ArquivoExibir.aspx?IdArquivo=178025669">
ipdb> response.xpath("//meta[contains(@name, 'og:image')]//@content").extract_first()                                                                                                                                                                                           
u'http://www.cdiscount-imagens.com.br/Control/ArquivoExibir.aspx?IdArquivo=178025669'

<span itemprop="sku">180</span>
ipdb> response.xpath("//span[contains(@itemprop, 'sku')]//text()").extract_first()
u'180'

---

HANDLE SCRAPY STATUS SUMMARY / ERRORS (OR: HOW TO ADD NEW STATUSES): 

from scrapy.spider import BaseSpider
from scrapy.xlib.pydispatch import dispatcher
from scrapy import signals

class MySpider(BaseSpider):
    handle_httpstatus_list = [404] 
    name = "myspider"
    allowed_domains = ["example.com"]
    start_urls = [
        'http://www.example.com/thisurlexists.html',
        'http://www.example.com/thisurldoesnotexist.html',
        'http://www.example.com/neitherdoesthisone.html'
    ]

    def __init__(self, category=None):
        self.failed_urls = []

    def parse(self, response):
        if response.status == 404:
            self.crawler.stats.inc_value('failed_url_count')
            self.failed_urls.append(response.url)

    def handle_spider_closed(spider, reason):
        self.crawler.stats.set_value('failed_urls', ','.join(spider.failed_urls))

    def process_exception(self, response, exception, spider):
        ex_class = "%s.%s" % (exception.__class__.__module__, exception.__class__.__name__)
        self.crawler.stats.inc_value('downloader/exception_count', spider=spider)
        self.crawler.stats.inc_value('downloader/exception_type_count/%s' % ex_class, spider=spider)

    dispatcher.connect(handle_spider_closed, signals.spider_closed)


>>> Output (the downloader/exception_count *stats will only appear if exceptions are actually thrown* - I simulated them by trying to run the spider after I'd turned off my wireless adapter):

2012-12-10 11:15:26+0000 [myspider] INFO: Dumping Scrapy stats:
    {'downloader/exception_count': 15,
     'downloader/exception_type_count/twisted.internet.error.DNSLookupError': 15,
     'downloader/request_bytes': 717,
     'downloader/request_count': 3,
     'downloader/request_method_count/GET': 3,
     'downloader/response_bytes': 15209,
     'downloader/response_count': 3,
     'downloader/response_status_count/200': 1,
     'downloader/response_status_count/404': 2,
     'failed_url_count': 2,
     'failed_urls': 'http://www.example.com/thisurldoesnotexist.html, http://www.example.com/neitherdoesthisone.html'
     'finish_reason': 'finished',
     'finish_time': datetime.datetime(2012, 12, 10, 11, 15, 26, 874000),
     'log_count/DEBUG': 9,
     'log_count/ERROR': 2,
     'log_count/INFO': 4,
     'response_received_count': 3,
     'scheduler/dequeued': 3,
     'scheduler/dequeued/memory': 3,
     'scheduler/enqueued': 3,
     'scheduler/enqueued/memory': 3,
     'spider_exceptions/NameError': 2,
     'start_time': datetime.datetime(2012, 12, 10, 11, 15, 26, 560000)}

(reference: http://www.unknownerror.org/opensource/scrapy/scrapy/q/stackoverflow/13724730/how-to-get-the-scrapy-failure-urls)

---

HOW TO SELECT THE TEXT OF ALL ELEMENTS BELOW A CERTAIN ELEMENT: 

This is quite useful to avoid the use of lxml, keeping the parsers simple. 
The technique resumes to using "/descendant" on the desired xpath. 

E.g., let's suppose I want the text of all elements below ".box-price": 

    response.xpath("//div[@class ='box-price']/descendant::text()").extract()

I could also exclude some elements from this list using XPATH conditionals: 

    response.xpath("//div[@class='box-price']/descendant::text()[not(parent::div/@class='infobox')]").extract()

(in the last example, I am excluding all elements that have the class "infobox".)

--- 

HOW TO USE REGULAR EXPRESSIONS WITH response.css() and response.xpath(): 

Basically, after css, you can chain the methods "re()" or "re_first()" (instead of
"extract()" or "extract_first()". E.g.:

    response.css('#ctl00_Body_boxParcelamento .e .bold').re_first(r'([0-9]{1,2})X+')

---

EXAMPLE OF XPATH "contains" query: 

response.xpath('//a[contains(@href,"Filtro")]//@href').extract()

There are other useful queries, like "starts-with" and others. Instead of
looping through the elements, notice that it is more efficient and much more
interesting to use those xpath queries, so that scrapy recomends using xpaths
for the most complex selectors, just because of this ability to have methods
like contains, starts-with and potentially many others (it must be useful to 
study xpaths queries in depth to learn all of its advanced tricks). 

---

ANOTHER WAY TO GET XPATH ON A PARTIAL ELEMENT VALUE: 

The secret here is "contains...", combined with "following-sibling". 

<table>
    ...   
    <tr>
        <th>
            Código de Barras
        </th>
        <td>
            7896584052084, 7896584052091
        </td>
    </tr>
</table>

Suppose I want to get the value of the "Código de Barras" cell. The XPATH
expression would have to be written like that: 

ean = response.xpath(
	'//th[contains(text(), "de Barras")]/'
	'following-sibling'
	'::td/text()').extract_first().replace('\n', '').strip()

---

HOW TO ALLOW SCRAPY TO MAKE A 2ND REQUEST TO THE SAME URL ON PURPOSE 
(FORCING IT TO BYPASS THE FILTER_DUPLICATE_URLS SETTING): 

# the param "dont_filter" below is necessary to allow to
# pass a second time through the product url.
yield Request(meta['original_product_url'],
				callback=self.parse_product,
				meta=meta,
				dont_filter=True)

---

HOW TO SELECT AN ELEMENT WITH CSS AND XPATH: 

Suppose the following element: 

```
<a class="ultima " data-pagina="16" href="http://www.ricardoeletro.com.br/Loja/Cama-Mesa-e-Banho/Jogo-de-Cama/2438-2439?p=16">
```

To get the value of the property "data-pagina" on this element, I can do: 

```
response.css('.ultima').xpath('@data-pagina').extract_first()
```

---


