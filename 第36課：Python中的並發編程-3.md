## 第36課：Python中的並發編程-3

爬蟲是典型的 I/O 密集型任務，I/O 密集型任務的特點就是程序會經常性的因為 I/O 操作而進入阻塞狀態，比如我們之前使用`requests`獲取頁面代碼或二進制內容，發出一個請求之後，程序必須要等待網站返迴響應之後才能繼續運行，如果目標網站不是很給力或者網絡狀況不是很理想，那麼等待響應的時間可能會很久，而在這個過程中整個程序是一直阻塞在那裡，沒有做任何的事情。通過前面的課程，我們已經知道了可以通過多線程的方式為爬蟲提速，使用多線程的本質就是，當一個線程阻塞的時候，程序還有其他的線程可以繼續運轉，因此整個程序就不會在阻塞和等待中浪費了大量的時間。

事實上，還有一種非常適合 I/O 密集型任務的並發編程方式，我們稱之為異步編程，你也可以將它稱為異步 I/O。這種方式並不需要啟動多個線程或多個進程來實現並發，它是通過多個子程序相互協作的方式來提升 CPU 的利用率，解決了 I/O 密集型任務 CPU  利用率很低的問題，我一般將這種方式稱為“協作式並發”。這裡，我不打算探討操作系統的各種 I/O 模式，因為這對很多讀者來說都太過抽象；但是我們得先拋出兩組概念給大家，一組叫做“阻塞”和“非阻塞”，一組叫做“同步”和“異步”。

### 基本概念

#### 阻塞

阻塞狀態指程序未得到所需計算資源時被掛起的狀態。程序在等待某個操作完成期間，自身無法繼續處理其他的事情，則稱該程序在該操作上是阻塞的。阻塞隨時都可能發生，最典型的就是 I/O 中斷（包括網絡 I/O 、磁盤 I/O 、用戶輸入等）、休眠操作、等待某個線程執行結束，甚至包括在 CPU 切換上下文時，程序都無法真正的執行，這就是所謂的阻塞。

#### 非阻塞

程序在等待某操作過程中，自身不被阻塞，可以繼續處理其他的事情，則稱該程序在該操作上是非阻塞的。非阻塞並不是在任何程序級別、任何情況下都可以存在的。僅當程序封裝的級別可以囊括獨立的子程序單元時，它才可能存在非阻塞狀態。顯然，某個操作的阻塞可能會導程序耗時以及效率低下，所以我們會希望把它變成非阻塞的。

#### 同步

不同程序單元為了完成某個任務，在執行過程中需靠某種通信方式以協調一致，我們稱這些程序單元是同步執行的。例如前面講過的給銀行賬戶存錢的操作，我們在代碼中使用了“鎖”作為通信信號，讓多個存錢操作強制排隊順序執行，這就是所謂的同步。

#### 異步

不同程序單元在執行過程中無需通信協調，也能夠完成一個任務，這種方式我們就稱之為異步。例如，使用爬蟲下載頁面時，調度程序調用下載程序後，即可調度其他任務，而無需與該下載任務保持通信以協調行為。不同網頁的下載、保存等操作都是不相關的，也無需相互通知協調。很顯然，異步操作的完成時刻和先後順序並不能確定。

很多人都不太能準確的把握這幾個概念，這裡我們簡單的總結一下，同步與異步的關注點是**消息通信機制**，最終表現出來的是“有序”和“無序”的區別；阻塞和非阻塞的關注點是**程序在等待消息時狀態**，最終表現出來的是程序在等待時能不能做點別的。如果想深入理解這些內容，推薦大家閱讀經典著作[《UNIX網絡編程》](https://item.jd.com/11880047.html)，這本書非常的讚。

### 生成器和協程

前面我們說過，異步編程是一種“協作式並發”，即通過多個子程序相互協作的方式提升 CPU 的利用率，從而減少程序在阻塞和等待中浪費的時間，最終達到並發的效果。我們可以將多個相互協作的子程序稱為“協程”，它是實現異步編程的關鍵。在介紹協程之前，我們先通過下面的代碼，看看什麼是生成器。

```Python
def fib(max_count):
    a, b = 0, 1
    for _ in range(max_count):
        a, b = b, a + b
        yield a
```

上面我們編寫了一個生成斐波那契數列的生成器，調用上面的`fib`函數並不是執行該函數獲得返回值，因為`fib`函數中有一個特殊的關鍵字`yield`。這個關鍵字使得`fib`函數跟普通的函數有些區別，調用該函數會得到一個生成器對象，我們可以通過下面的代碼來驗證這一點。

```Python
gen_obj = fib(20)
print(gen_obj)
```

輸出：

```
<generator object fib at 0x106daee40>
```

我們可以使用內置函數`next`從生成器對像中獲取斐波那契數列的值，也可以通過`for-in`循環對生成器能夠提供的值進行遍歷，代碼如下所示。

```Python
for value in gen_obj:
    print(value)
```

生成器經過預激活，就是一個協程，它可以跟其他子程序協作。

```Python
def calc_average():
    total, counter = 0, 0
    avg_value = None
    while True:
        curr_value = yield avg_value
        total += curr_value
        counter += 1
        avg_value = total / counter


def main():
    obj = calc_average()
    # 生成器預激活
    obj.send(None)
    for _ in range(5):
        print(obj.send(float(input())))


if __name__ == '__main__':
    main()
```

上面的`main`函數首先通過生成器對象的`send`方法發送一個`None`值來將其激活為協程，也可以通過`next(obj)`達到同樣的效果。接下來，協程對象會接收`main`函數發送的數據並產出（`yield`）數據的平均值。通過上面的例子，不知道大家是否看出兩段子程序是怎麼“協作”的。

### 異步函數

Python 3.5版本中，引入了兩個非常有意思的元素，一個叫`async`，一個叫`await`，它們在Python 3.7版本中成為了正式的關鍵字。通過這兩個關鍵字，可以簡化協程代碼的編寫，可以用更為簡單的方式讓多個子程序很好的協作起來。我們通過一個例子來加以說明，請大家先看看下面的代碼。

```Python
import time


def display(num):
    time.sleep(1)
    print(num)


def main():
    start = time.time()
    for i in range(1, 10):
        display(i)
    end = time.time()
    print(f'{end - start:.3f}秒')


if __name__ == '__main__':
    main()
```

上面的代碼每次執行都會依次輸出`1`到`9`的數字，每個間隔`1`秒鐘，整個代碼需要執行大概需要`9`秒多的時間，這一點我相信大家都能看懂。不知道大家是否意識到，這段代碼就是以同步和阻塞的方式執行的，同步可以從代碼的輸出看出來，而阻塞是指在調用`display`函數發生休眠時，整個代碼的其他部分都不能繼續執行，必須等待休眠結束。

接下來，我們嘗試用異步的方式改寫上面的代碼，讓`display`函數以異步的方式運轉。

```Python
import asyncio
import time


async def display(num):
    await asyncio.sleep(1)
    print(num)


def main():
    start = time.time()
    objs = [display(i) for i in range(1, 10)]
    loop = asyncio.get_event_loop()
    loop.run_until_complete(asyncio.wait(objs))
    loop.close()
    end = time.time()
    print(f'{end - start:.3f}秒')


if __name__ == '__main__':
    main()
```

Python 中的`asyncio`模塊提供了對異步 I/O 的支持。上面的代碼中，我們首先在`display`函數前面加上了`async`關鍵字使其變成一個異步函數，調用異步函數不會執行函數體而是獲得一個協程對象。我們將`display`函數中的`time.sleep(1)`修改為`await asyncio.sleep(1)`，二者的區別在於，後者不會讓整個代碼陷入阻塞，因為`await`操作會讓其他協作的子程序有獲得 CPU 資源而得以運轉的機會。為了讓這些子程序可以協作起來，我們需要將他們放到一個事件循環（實現消息分派傳遞的系統）上，因為**當協程遭遇 I/O 操作阻塞時，就會到事件循環中監聽 I/O 操作是否完成，並註冊自身的上下文以及自身的喚醒函數（以便恢復執行），之後該協程就變為阻塞狀態**。上面的第12行代碼創建了`9`個協程對象並放到一個列表中，第13行代碼通過`asyncio`模塊的`get_event_loop`函數獲得了系統的事件循環，第14行通過`asyncio`模塊的`run_until_complete`函數將協程對象掛載到事件循環上。執行上面的代碼會發現，`9`個分別會阻塞`1`秒鐘的協程總共只阻塞了約`1`秒种的時間，因為**阻塞的協程對象會放棄對 CPU 的佔有而不是讓 CPU 處於閒置狀態，這種方式大大的提升了 CPU 的利用率**。而且我們還會注意到，數字並不是按照從`1`到`9`的順序打印輸出的，這正是我們想要的結果，說明它們是**異步執行**的。對於爬蟲這樣的 I/O 密集型任務來說，這種協作式並發在很多場景下是比使用多線程更好的選擇，因為這種做法減少了管理和維護多個線程以及多個線程切換所帶來的開銷。

### aiohttp庫

我們之前使用的`requests`三方庫並不支持異步 I/O，如果希望使用異步 I/O 的方式來加速爬蟲代碼的執行，我們可以安裝和使用名為`aiohttp`的三方庫。

安裝`aiohttp`。

```Bash
pip install aiohttp
```

下面的代碼使用`aiohttp`抓取了`10`個網站的首頁並解析出它們的標題。

```Python
import asyncio
import re

import aiohttp
from aiohttp import ClientSession

TITLE_PATTERN = re.compile(r'<title.*?>(.*?)</title>', re.DOTALL)


async def fetch_page_title(url):
    async with aiohttp.ClientSession(headers={
        'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/95.0.4638.69 Safari/537.36',
    }) as session:  # type: ClientSession
        async with session.get(url, ssl=False) as resp:
            if resp.status == 200:
                html_code = await resp.text()
                matcher = TITLE_PATTERN.search(html_code)
                title = matcher.group(1).strip()
                print(title)


def main():
    urls = [
        'https://www.python.org/',
        'https://www.jd.com/',
        'https://www.baidu.com/',
        'https://www.taobao.com/',
        'https://git-scm.com/',
        'https://www.sohu.com/',
        'https://gitee.com/',
        'https://www.amazon.com/',
        'https://www.usa.gov/',
        'https://www.nasa.gov/'
    ]
    objs = [fetch_page_title(url) for url in urls]
    loop = asyncio.get_event_loop()
    loop.run_until_complete(asyncio.wait(objs))
    loop.close()


if __name__ == '__main__':
    main()
```

輸出：

```
京東(JD.COM)-正品低價、品質保障、配送及時、輕鬆購物！
搜狐
淘寶網 - 淘！我喜歡
百度一下，你就知道
Gitee - 基於 Git 的代碼託管和研發協作平台
Git
NASA
Official Guide to Government Information and Services   &#124; USAGov
Amazon.com. Spend less. Smile more.
Welcome to Python.org
```

從上面的輸出可以看出，網站首頁標題的輸出順序跟它們的 URL 在列表中的順序沒有關係。代碼的第11行到第13行創建了`ClientSession`對象，通過它的`get`方法可以向指定的 URL 發起請求，如第14行所示，跟`requests`中的`Session`對象並沒有本質區別，唯一的區別是這裡使用了異步上下文。代碼第16行的`await`會讓因為 I/O 操作阻塞的子程序放棄對 CPU 的佔用，這使得其他的子程序可以運轉起來去抓取頁面。代碼的第17行和第18行使用了正則表達式捕獲組操作解析網頁標題。 `fetch_page_title`是一個被`async`關鍵字修飾的異步函數，調用該函數會獲得協程對象，如代碼第35行所示。後面的代碼跟之前的例子沒有什麼區別，相信大家能夠理解。

大家可以嘗試將`aiohttp`換回到`requests`，看看不使用異步 I/O 也不使用多線程，到底和上面的代碼有什麼區別，相信通過這樣的對比，大家能夠更深刻的理解我們之前強調的幾個概念：同步和異步，阻塞和非阻塞。
