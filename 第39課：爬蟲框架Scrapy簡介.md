## 第39課：爬蟲框架Scrapy簡介

當你寫了很多個爬蟲程序之後，你會發現每次寫爬蟲程序時，都需要將頁面獲取、頁面解析、爬蟲調度、異常處理、反爬應對這些代碼從頭至尾實現一遍，這裡面有很多工作其實都是簡單乏味的重複勞動。那麼，有沒有什麼辦法可以提升我們編寫爬蟲代碼的效率呢？答案是肯定的，那就是利用爬蟲框架，而在所有的爬蟲框架中，Scrapy 應該是最流行、最強大的框架。

### Scrapy 概述

Scrapy 是基於 Python 的一個非常流行的網絡爬蟲框架，可以用來抓取 Web 站點並從頁面中提取結構化的數據。下圖展示了 Scrapy 的基本架構，其中包含了主要組件和系統的數據處理流程（圖中帶數字的紅色箭頭）。

![](https://gitee.com/jackfrued/mypic/raw/master/20210824003638.png)

#### Scrapy的組件

我們先來說說 Scrapy 中的組件。

1. Scrapy 引擎（Engine）：用來控制整個系統的數據處理流程。
2. 調度器（Scheduler）：調度器從引擎接受請求並排序列入隊列，並在引擎發出請求後返還給它們。
3. 下載器（Downloader）：下載器的主要職責是抓取網頁並將網頁內容返還給蜘蛛（Spiders）。
4. 蜘蛛程序（Spiders）：蜘蛛是用戶自定義的用來解析網頁並抓取特定URL的類，每個蜘蛛都能處理一個域名或一組域名，簡單的說就是用來定義特定網站的抓取和解析規則的模塊。
5. 數據管道（Item Pipeline）：管道的主要責任是負責處理有蜘蛛從網頁中抽取的數據條目，它的主要任務是清理、驗證和存儲數據。當頁面被蜘蛛解析後，將被發送到數據管道，並經過幾個特定的次序處理數據。每個數據管道組件都是一個 Python 類，它們獲取了數據條目並執行對數據條目進行處理的方法，同時還需要確定是否需要在數據管道中繼續執行下一步或是直接丟棄掉不處理。數據管道通常執行的任務有：清理 HTML 數據、驗證解析到的數據（檢查條目是否包含必要的字段）、檢查是不是重複數據（如果重複就丟棄）、將解析到的數據存儲到數據庫（關係型數據庫或 NoSQL 數據庫）中。
6. 中間件（Middlewares）：中間件是介於引擎和其他組件之間的一個鉤子框架，主要是為了提供自定義的代碼來拓展 Scrapy 的功能，包括下載器中間件和蜘蛛中間件。

#### 數據處理流程

Scrapy 的整個數據處理流程由引擎進行控制，通常的運轉流程包括以下的步驟：

1. 引擎詢問蜘蛛需要處理哪個網站，並讓蜘蛛將第一個需要處理的 URL 交給它。

2. 引擎讓調度器將需要處理的 URL 放在隊列中。

3. 引擎從調度那獲取接下來進行爬取的頁面。

4. 調度將下一個爬取的 URL 返回給引擎，引擎將它通過下載中間件發送到下載器。

5. 當網頁被下載器下載完成以後，響應內容通過下載中間件被發送到引擎；如果下載失敗了，引擎會通知調度器記錄這個 URL，待會再重新下載。

6. 引擎收到下載器的響應並將它通過蜘蛛中間件發送到蜘蛛進行處理。

7. 蜘蛛處理響應並返回爬取到的數據條目，此外還要將需要跟進的新的 URL 發送給引擎。

8. 引擎將抓取到的數據條目送入數據管道，把新的 URL 發送給調度器放入隊列中。

上述操作中的第2步到第8步會一直重複直到調度器中沒有需要請求的 URL，爬蟲就停止工作。

### 安裝和使用Scrapy

可以使用 Python 的包管理工具`pip`來安裝 Scrapy。

```Shell
pip install scrapy
```

在命令行中使用`scrapy`命令創建名為`demo`的項目。

```Bash
scrapy startproject demo
```

項目的目錄結構如下圖所示。

```Shell
demo
|____ demo
|________ spiders
|____________ __init__.py
|________ __init__.py
|________ items.py
|________ middlewares.py
|________ pipelines.py
|________ settings.py
|____ scrapy.cfg
```

切換到`demo` 目錄，用下面的命令創建名為`douban`的蜘蛛程序。

```Bash
scrapy genspider douban movie.douban.com
```

#### 一個簡單的例子

接下來，我們實現一個爬取豆瓣電影 Top250 電影標題、評分和金句的爬蟲。

1. 在`items.py`的`Item`類中定義字段，這些字段用來保存數據，方便後續的操作。

   ```Python
   import scrapy
   
   
   class DoubanItem(scrapy.Item):
       title = scrapy.Field()
       score = scrapy.Field()
       motto = scrapy.Field()
   ```
   
2. 修改`spiders`文件夾中名為`douban.py` 的文件，它是蜘蛛程序的核心，需要我們添加解析頁面的代碼。在這裡，我們可以通過對`Response`對象的解析，獲取電影的信息，代碼如下所示。

   ```Python
   import scrapy
   from scrapy import Selector, Request
   from scrapy.http import HtmlResponse
   
   from demo.items import MovieItem
   
   
   class DoubanSpider(scrapy.Spider):
       name = 'douban'
       allowed_domains = ['movie.douban.com']
       start_urls = ['https://movie.douban.com/top250?start=0&filter=']
   
       def parse(self, response: HtmlResponse):
           sel = Selector(response)
           movie_items = sel.css('#content > div > div.article > ol > li')
           for movie_sel in movie_items:
               item = MovieItem()
               item['title'] = movie_sel.css('.title::text').extract_first()
               item['score'] = movie_sel.css('.rating_num::text').extract_first()
               item['motto'] = movie_sel.css('.inq::text').extract_first()
               yield item
   ```
   通過上面的代碼不難看出，我們可以使用 CSS 選擇器進行頁面解析。當然，如果你願意也可以使用 XPath 或正則表達式進行頁面解析，對應的方法分別是`xpath`和`re`。

   如果還要生成後續爬取的請求，我們可以用`yield`產出`Request`對象。 `Request`對像有兩個非常重要的屬性，一個是`url`，它代表了要請求的地址；一個是`callback`，它代表了獲得響應之後要執行的回調函數。我們可以將上面的代碼稍作修改。

   ```Python
   import scrapy
   from scrapy import Selector, Request
   from scrapy.http import HtmlResponse
   
   from demo.items import MovieItem
   
   
   class DoubanSpider(scrapy.Spider):
       name = 'douban'
       allowed_domains = ['movie.douban.com']
       start_urls = ['https://movie.douban.com/top250?start=0&filter=']
   
       def parse(self, response: HtmlResponse):
           sel = Selector(response)
           movie_items = sel.css('#content > div > div.article > ol > li')
           for movie_sel in movie_items:
               item = MovieItem()
               item['title'] = movie_sel.css('.title::text').extract_first()
               item['score'] = movie_sel.css('.rating_num::text').extract_first()
               item['motto'] = movie_sel.css('.inq::text').extract_first()
               yield item
   
           hrefs = sel.css('#content > div > div.article > div.paginator > a::attr("href")')
           for href in hrefs:
               full_url = response.urljoin(href.extract())
               yield Request(url=full_url)
   ```

   到這裡，我們已經可以通過下面的命令讓爬蟲運轉起來。

   ```Shell
   scrapy crawl movie
   ```

   可以在控制台看到爬取到的數據，如果想將這些數據保存到文件中，可以通過`-o`參數來指定文件名，Scrapy 支持我們將爬取到的數據導出成 JSON、CSV、XML 等格式。

   ```Shell
   scrapy crawl moive -o result.json
   ```

   不知大家是否注意到，通過運行爬蟲獲得的 JSON 文件中有`275`條數據，那是因為首頁被重複爬取了。要解決這個問題，可以對上面的代碼稍作調整，不在`parse`方法中解析獲取新頁面的 URL，而是通過`start_requests`方法提前準備好待爬取頁面的 URL，調整後的代碼如下所示。

   ```Python
   import scrapy
   from scrapy import Selector, Request
   from scrapy.http import HtmlResponse
   
   from demo.items import MovieItem
   
   
   class DoubanSpider(scrapy.Spider):
       name = 'douban'
       allowed_domains = ['movie.douban.com']
   
       def start_requests(self):
           for page in range(10):
               yield Request(url=f'https://movie.douban.com/top250?start={page * 25}')
   
       def parse(self, response: HtmlResponse):
           sel = Selector(response)
           movie_items = sel.css('#content > div > div.article > ol > li')
           for movie_sel in movie_items:
               item = MovieItem()
               item['title'] = movie_sel.css('.title::text').extract_first()
               item['score'] = movie_sel.css('.rating_num::text').extract_first()
               item['motto'] = movie_sel.css('.inq::text').extract_first()
               yield item
   ```

3. 如果希望完成爬蟲數據的持久化，可以在數據管道中處理蜘蛛程序產生的`Item`對象。例如，我們可以通過前面講到的`openpyxl`操作 Excel 文件，將數據寫入 Excel 文件中，代碼如下所示。

   ```Python
   import openpyxl
   
   from demo.items import MovieItem
   
   
   class MovieItemPipeline:
   
       def __init__(self):
           self.wb = openpyxl.Workbook()
           self.sheet = self.wb.active
           self.sheet.title = 'Top250'
           self.sheet.append(('名稱', '評分', '名言'))
   
       def process_item(self, item: MovieItem, spider):
           self.sheet.append((item['title'], item['score'], item['motto']))
           return item
   
       def close_spider(self, spider):
           self.wb.save('豆瓣電影數據.xlsx')
   ```

   上面的`process_item`和`close_spider`都是回調方法（鉤子函數）， 簡單的說就是 Scrapy 框架會自動去調用的方法。當蜘蛛程序產生一個`Item`對象交給引擎時，引擎會將該`Item`對象交給數據管道，這時我們配置好的數據管道的`parse_item`方法就會被執行，所以我們可以在該方法中獲取數據並完成數據的持久化操作。另一個方法`close_spider`是在爬蟲結束運行前會自動執行的方法，在上面的代碼中，我們在這個地方進行了保存 Excel 文件的操作，相信這段代碼大家是很容易讀懂的。

   總而言之，數據管道可以幫助我們完成以下操作：

   - 清理 HTML 數據，驗證爬取的數據。
   - 丟棄重複的不必要的內容。
   - 將爬取的結果進行持久化操作。

4. 修改`settings.py`文件對項目進行配置，主要需要修改以下幾個配置。

   ```Python
   # 用戶瀏覽器
   USER_AGENT = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.159 Safari/537.36'
   
   # 並發請求數量 
   CONCURRENT_REQUESTS = 4
   
   # 下載延遲
   DOWNLOAD_DELAY = 3
   # 隨機化下載延遲
   RANDOMIZE_DOWNLOAD_DELAY = True
   
   # 是否遵守爬蟲協議
   ROBOTSTXT_OBEY = True
   
   # 配置數據管道
   ITEM_PIPELINES = {
      'demo.pipelines.MovieItemPipeline': 300,
   }
   ```

   > **說明**：上面配置文件中的`ITEM_PIPELINES`選項是一個字典，可以配置多個處理數據的管道，後面的數字代表了執行的優先級，數字小的先執行。
