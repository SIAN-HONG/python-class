## 第32課：用Python獲取網絡數據

網絡數據採集是 Python 語言非常擅長的領域，上節課我們講到，實現網絡數據採集的程序通常稱之為網絡爬蟲或蜘蛛程序。即便是在大數據時代，數據對於中小企業來說仍然是硬傷和短板，有些數據需要通過開放或付費的數據接口來獲得，其他的行業數據和競對數據則必須要通過網絡數據採集的方式來獲得。不管使用哪種方式獲取網絡數據資源，Python 語言都是非常好的選擇，因為 Python 的標準庫和三方庫都對網絡數據採集提供了良好的支持。

### requests庫

要使用 Python 獲取網絡數據，我們推薦大家使用名為`requests` 的三方庫，這個庫我們在之前的課程中其實已經使用過了。按照官方網站的解釋，`requests`是基於 Python 標準庫進行了封裝，簡化了通過 HTTP 或 HTTPS 訪問網絡資源的操作。上課我們提到過，HTTP 是一個請求響應式的協議，當我們在瀏覽器中輸入正確的 [URL](https://developer.mozilla.org/zh-CN/docs/Learn/Common_questions/What_is_a_URL)（通常也稱為網址）並按下 Enter 鍵時，我們就向網絡上的 [Web 服務器](https://developer.mozilla.org/zh-CN/docs/Learn/Common_questions/What_is_a_web_server)發送了一個 HTTP 請求，服務器在收到請求後會給我們一個 HTTP 響應。在 Chrome 瀏覽器中的菜單中打開“開發者工具”切換到“Network”選項卡就能夠查看 HTTP 請求和響應到底是什麼樣子的，如下圖所示。

![](https://gitee.com/jackfrued/mypic/raw/master/20210822093434.png)

通過`requests`庫，我們可以讓 Python 程序向瀏覽器一樣向 Web 服務器發起請求，並接收服務器返回的響應，從響應中我們就可以提取出想要的數據。瀏覽器呈現給我們的網頁是用 [HTML](https://developer.mozilla.org/zh-CN/docs/Web/HTML) 編寫的，瀏覽器相當於是 HTML 的解釋器環境，我們看到的網頁中的內容都包含在 HTML 的標籤中。在獲取到 HTML 代碼後，就可以從標籤的屬性或標籤體中提取內容。下面例子演示瞭如何獲取網頁 HTML 代碼，我們通過`requests`庫的`get`函數，獲取了搜狐首頁的代碼。

```Python
import requests

resp = requests.get('https://www.sohu.com/')
if resp.status_code == 200:
    print(resp.text)
```

> **說明**：上面代碼中的變量`resp`是一個`Response`對象（`requests`庫封裝的類型），通過該對象的`status_code`屬性可以獲取響應狀態碼，而該對象的`text`屬性可以幫我們獲取到頁面的 HTML 代碼。

由於`Response`對象的`text`是一個字符串，所以我們可以利用之前講過的正則表達式的知識，從頁面的 HTML 代碼中提取新聞的標題和鏈接，代碼如下所示。

```Python
import re

import requests

pattern = re.compile(r'<a.*?href="(.*?)".*?title="(.*?)".*?>')
resp = requests.get('https://www.sohu.com/')
if resp.status_code == 200:
    all_matches = pattern.findall(resp.text)
    for href, title in all_matches:
        print(href)
        print(title)
```

除了文本內容，我們也可以使用`requests`庫通過 URL 獲取二進制資源。下面的例子演示瞭如何獲取百度 Logo 並保存到名為`baidu.png`的本地文件中。可以在百度的首頁上右鍵點擊百度Logo，並通過“複製圖片地址”菜單項獲取圖片的 URL。

```Python
import requests

resp = requests.get('https://www.baidu.com/img/PCtm_d9c8750bed0b3c7d089fa7d55720d6cf.png')
with open('baidu.png', 'wb') as file:
    file.write(resp.content)
```

> **說明**：`Response`對象的`content`屬性可以獲得服務器響應的二進制數據。

`requests`庫非常好用而且功能上也比較強大和完整，具體的內容我們在使用的過程中為大家一點點剖析。想解鎖關於`requests`庫更多的知識，可以閱讀它的[官方文檔](https://docs.python-requests.org/zh_CN/latest/)。

### 編寫爬蟲代碼

接下來，我們以“豆瓣電影”為例，為大家講解如何編寫爬蟲代碼。按照上面提供的方法，我們先使用`requests`獲取到網頁的HTML代碼，然後將整個代碼看成一個長字符串，這樣我們就可以使用正則表達式的捕獲組從字符串提取我們需要的內容。下面的代碼演示瞭如何從[豆瓣電影](https://movie.douban.com/)獲取排前250名的電影的名稱。 [豆瓣電影Top250](https://movie.douban.com/top250)的頁面結構和對應代碼如下圖所示，可以看出，每頁共展示了25部電影，如果要獲取到 Top250 數據，我們共需要訪問10個頁面，對應的地址是<https://movie.douban.com/top250?start=xxx>，這裡的`xxx`如果為`0`就是第一頁，如果`xxx`的值是`100`，那麼我們可以訪問到第五頁。為了代碼簡單易讀，我們只獲取電影的標題和評分。

![](https://gitee.com/jackfrued/mypic/raw/master/20210822093447.png)

```Python
import random
import re
import time

import requests

for page in range(1, 11):
    resp = requests.get(
        url=f'https://movie.douban.com/top250?start={(page - 1) * 25}',
        # 如果不設置HTTP請求頭中的User-Agent，豆瓣會檢測出不是瀏覽器而阻止我們的請求。
        # 通過get函數的headers參數設置User-Agent的值，具體的值可以在瀏覽器的開發者工具查看到。
        # 用爬蟲訪問大部分網站時，將爬蟲偽裝成來自瀏覽器的請求都是非常重要的一步。
        headers={'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.159 Safari/537.36'}
    )
    # 通過正則表達式獲取class屬性為title且標籤體不以&開頭的span標籤並用捕獲組提取標籤內容
    pattern1 = re.compile(r'<span class="title">([^&]*?)</span>')
    titles = pattern1.findall(resp.text)
    # 通過正則表達式獲取class屬性為rating_num的span標籤並用捕獲組提取標籤內容
    pattern2 = re.compile(r'<span class="rating_num".*?>(.*?)</span>')
    ranks = pattern2.findall(resp.text)
    # 使用zip壓縮兩個列表，循環遍歷所有的電影標題和評分
    for title, rank in zip(titles, ranks):
        print(title, rank)
    # 隨機休眠1-5秒，避免爬取頁面過於頻繁
    time.sleep(random.random() * 4 + 1)
```

> **說明**：通過分析豆瓣網的robots協議，我們發現豆瓣網並不拒絕百度爬蟲獲取它的數據，因此我們也可以將爬蟲偽裝成百度的爬蟲，將`get`函數的`headers`參數修改為：`headers={'User-Agent': 'BaiduSpider'}`。

### 使用 IP 代理

讓爬蟲程序隱匿自己的身份對編寫爬蟲程序來說是比較重要的，很多網站對爬蟲都比較反感的，因為爬蟲會耗費掉它們很多的網絡帶寬並製造很多無效的流量。要隱匿身份通常需要使用**商業 IP 代理**（如蘑菇代理、芝麻代理、快代理等），讓被爬取的網站無法獲取爬蟲程序來源的真實 IP 地址，也就無法簡單的通過 IP 地址對爬蟲程序進行封禁。

下面以[蘑菇代理](http://www.moguproxy.com/)為例，為大家講解商業 IP 代理的使用方法。首先需要在該網站註冊一個賬號，註冊賬號後就可以[購買](http://www.moguproxy.com/buy)相應的套餐來獲得商業 IP 代理。作為商業用途，建議大家購買不限量套餐，這樣可以根據實際需要獲取足夠多的代理 IP 地址；作為學習用途，可以購買包時套餐或根據自己的需求來決定。蘑菇代理提供了兩種接入代理的方式，分別是 API 私密代理和 HTTP 隧道代理，前者是通過請求蘑菇代理的 API 接口獲取代理服務器地址，後者是直接使用統一的入口（蘑菇代理提供的域名）進行接入。

<img src="https://gitee.com/jackfrued/mypic/raw/master/20210829080647.png" width="75%">

下面，我們以HTTP隧道代理為例，為大家講解接入 IP 代理的方式，大家也可以直接參考蘑菇代理官網提供的代碼來為爬蟲設置代理。

```Python
import requests

APP_KEY = 'Wnp******************************XFx'
PROXY_HOST = 'secondtransfer.moguproxy.com:9001'

for page in range(1, 11):
    resp = requests.get(
        url=f'https://movie.douban.com/top250?start={(page - 1) * 25}',
        # 需要在HTTP請求頭設置代理的身份認證方式
        headers={
            'Proxy-Authorization': f'Basic {APP_KEY}',
            'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.159 Safari/537.36',
            'Accept-Language': 'zh-CN,zh;q=0.8,en-US;q=0.6,en;q=0.4'
        },
        # 設置代理服務器
        proxies={
            'http': f'http://{PROXY_HOST}',
            'https': f'https://{PROXY_HOST}'
        },
        verify=False
    )
    pattern1 = re.compile(r'<span class="title">([^&]*?)</span>')
    titles = pattern1.findall(resp.text)
    pattern2 = re.compile(r'<span class="rating_num".*?>(.*?)</span>')
    ranks = pattern2.findall(resp.text)
    for title, rank in zip(titles, ranks):
        print(title, rank)
```

> **說明**：上面的代碼需要修改`APP_KEY`為自己創建的訂單對應的`Appkey`值，這個值可以在用戶中心用戶訂單中查看到。蘑菇代理提供了免費的 API 代理和 HTTP 隧道代理試用，但是試用的代理接通率不能保證，建議大家還是直接購買一個在自己支付能力範圍內的代理服務來體驗。

###  簡單的總結

Python 語言能做的事情真的很多，就網絡數據採集這一項而言，Python 幾乎是一枝獨秀的，大量的企業和個人都在使用 Python 從網絡上獲取自己需要的數據，這可能也是你將來日常工作的一部分。另外，用編寫正則表達式的方式從網頁中提取內容雖然可行，但是寫出一個能夠滿足需求的正則表達式本身也不是件容易的事情，這一點對於新手來說尤為明顯。在下一節課中，我們將會為大家介紹另外兩種從頁面中提取數據的方法，雖然從性能上來講，它們可能不如正則表達式，但是卻降低了編碼的複雜性，相信大家會喜歡上它們的。
