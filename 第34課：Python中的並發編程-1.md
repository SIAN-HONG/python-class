## 第34課：Python中的並發編程-1

現如今，我們使用的計算機早已是多 CPU 或多核的計算機，而我們使用的操作系統基本都支持“多任務”，這使得我們可以同時運行多個程序，也可以將一個程序分解為若干個相對獨立的子任務，讓多個子任務“並行”或“並發”的執行，從而縮短程序的執行時間，同時也讓用戶獲得更好的體驗。因此當下，不管用什麼編程語言進行開發，實現“並行”或“並發”編程已經成為了程序員的標配技能。為了講述如何在 Python 程序中實現“並行”或“並發”，我們需要先了解兩個重要的概念：進程和線程。

### 線程和進程

我們通過操作系統運行一個程序會創建出一個或多個進程，進程是具有一定獨立功能的程序關於某個數據集合上的一次運行活動。簡單的說，進程是操作系統分配存儲空間的基本單位，每個進程都有自己的地址空間、數據棧以及其他用於跟踪進程執行的輔助數據；操作系統管理所有進程的執行，為它們合理的分配資源。一個進程可以通過 fork 或 spawn 的方式創建新的進程來執行其他的任務，不過新的進程也有自己獨立的內存空間，因此兩個進程如果要共享數據，必須通過進程間通信機制來實現，具體的方式包括管道、信號、套接字等。

一個進程還可以擁有多個執行線索，簡單的說就是擁有多個可以獲得 CPU 調度的執行單元，這就是所謂的線程。由於線程在同一個進程下，它們可以共享相同的上下文，因此相對於進程而言，線程間的信息共享和通信更加容易。當然在單核 CPU 系統中，多個線程不可能同時執行，因為在某個時刻只有一個線程能夠獲得 CPU，多個線程通過共享 CPU 執行時間的方式來達到並發的效果。

在程序中使用多線程技術通常都會帶來不言而喻的好處，最主要的體現在提升程序的性能和改善用戶體驗，今天我們使用的軟件幾乎都用到了多線程技術，這一點可以利用系統自帶的進程監控工具（如 macOS 中的“活動監視器”、Windows 中的“任務管理器”）來證實，如下圖所示。

<img src="https://gitee.com/jackfrued/mypic/raw/master/20210822094243.png" width="80%">

這裡，我們還需要跟大家再次強調兩個概念：**並發**（concurrency）和**並行**（parallel）。 **並發**通常是指同一時刻只能有一條指令執行，但是多個線程對應的指令被快速輪換地執行。比如一個處理器，它先執行線程 A 的指令一段時間，再執行線程 B 的指令一段時間，再切回到線程 A 執行一段時間。由於處理器執行指令的速度和切換的速度極快，人們完全感知不到計算機在這個過程中有多個線程切換上下文執行的操作，這就使得宏觀上看起來多個線程在同時運行，但微觀上其實只有一個線程在執行。 **並行**是指同一時刻，有多條指令在多個處理器上同時執行，並行必須要依賴於多個處理器，不論是從宏觀上還是微觀上，多個線程可以在同一時刻一起執行的。很多時候，我們並不用嚴格區分並發和並行兩個詞，所以我們有時候也把 Python 中的多線程、多進程以及異步 I/O 都視為實現並發編程的手段，但實際上前面兩者也可以實現並行編程，當然這裡還有一個全局解釋器鎖（GIL）的問題，我們稍後討論。

### 多線程編程

Python 標準庫中`threading`模塊的`Thread`類可以幫助我們非常輕鬆的實現多線程編程。我們用一個聯網下載文件的例子來對比使用多線程和不使用多線程到底有什麼區別，代碼如下所示。

不使用多線程的下載。

```Python
import random
import time


def download(*, filename):
    start = time.time()
    print(f'開始下載 {filename}.')
    time.sleep(random.randint(3, 6))
    print(f'{filename} 下載完成.')
    end = time.time()
    print(f'下載耗時: {end - start:.3f}秒.')


def main():
    start = time.time()
    download(filename='Python從入門到住院.pdf')
    download(filename='MySQL從刪庫到跑路.avi')
    download(filename='Linux從精通到放棄.mp4')
    end = time.time()
    print(f'總耗時: {end - start:.3f}秒.')


if __name__ == '__main__':
    main()
```

> **說明**：上面的代碼並沒有真正實現聯網下載的功能，而是通過`time.sleep()`休眠一段時間來模擬下載文件需要一些時間上的開銷，跟實際下載的狀況比較類似。

運行上面的代碼，可以得到如下所示的運行結果。可以看出，當我們的程序只有一個工作線程時，每個下載任務都需要等待上一個下載任務執行結束才能開始，所以程序執行的總耗時是三個下載任務各自執行時間的總和。

```
開始下載Python從入門到住院.pdf.
Python從入門到住院.pdf下載完成.
下載耗時: 3.005秒.
開始下載MySQL從刪庫到跑路.avi.
MySQL從刪庫到跑路.avi下載完成.
下載耗時: 5.006秒.
開始下載Linux從精通到放棄.mp4.
Linux從精通到放棄.mp3下載完成.
下載耗時: 6.007秒.
總耗時: 14.018秒.
```

事實上，上面的三個下載任務之間並沒有邏輯上的因果關係，三者是可以“並發”的，下一個下載任務沒有必要等待上一個下載任務結束，為此，我們可以使用多線程編程來改寫上面的代碼。

```Python
import random
import time
from threading import Thread


def download(*, filename):
    start = time.time()
    print(f'開始下載 {filename}.')
    time.sleep(random.randint(3, 6))
    print(f'{filename} 下載完成.')
    end = time.time()
    print(f'下載耗時: {end - start:.3f}秒.')


def main():
    threads = [
        Thread(target=download, kwargs={'filename': 'Python從入門到住院.pdf'}),
        Thread(target=download, kwargs={'filename': 'MySQL從刪庫到跑路.avi'}),
        Thread(target=download, kwargs={'filename': 'Linux從精通到放棄.mp4'})
    ]
    start = time.time()
    # 啟動三個線程
    for thread in threads:
        thread.start()
    # 等待線程結束
    for thread in threads:
        thread.join()
    end = time.time()
    print(f'總耗時: {end - start:.3f}秒.')


if __name__ == '__main__':
    main()
```

某次的運行結果如下所示。

```
開始下載 Python從入門到住院.pdf.
開始下載 MySQL從刪庫到跑路.avi.
開始下載 Linux從精通到放棄.mp4.
MySQL從刪庫到跑路.avi 下載完成.
下載耗時: 3.005秒.
Python從入門到住院.pdf 下載完成.
下載耗時: 5.006秒.
Linux從精通到放棄.mp4 下載完成.
下載耗時: 6.003秒.
總耗時: 6.004秒.
```

通過上面的運行結果可以發現，整個程序的執行時間幾乎等於耗時最長的一個下載任務的執行時間，這也就意味著，三個下載任務是並發執行的，不存在一個等待另一個的情況，這樣做很顯然提高了程序的執行效率。簡單的說，如果程序中有非常耗時的執行單元，而這些耗時的執行單元之間又沒有邏輯上的因果關係，即 B 單元的執行不依賴於 A 單元的執行結果，那麼 A 和 B 兩個單元就可以放到兩個不同的線程中，讓他們並發的執行。這樣做的好處除了減少程序執行的等待時間，還可以帶來更好的用戶體驗，因為一個單元的阻塞不會造成程序的“假死”，因為程序中還有其他的單元是可以運轉的。

#### 使用 Thread 類創建線程對象

通過上面的代碼可以看出，直接使用`Thread`類的構造器就可以創建線程對象，而線程對象的`start()`方法可以啟動一個線程。線程啟動後會執行`target`參數指定的函數，當然前提是獲得 CPU 的調度；如果`target`指定的線程要執行的目標函數有參數，需要通過`args`參數為其進行指定，對於關鍵字參數，可以通過`kwargs`參數進行傳入。 `Thread`類的構造器還有很多其他的參數，我們遇到的時候再為大家進行講解，目前需要大家掌握的，就是`target`、`args`和`kwargs`。

#### 繼承 Thread 類自定義線程

除了上面的代碼展示的創建線程的方式外，還可以通過繼承`Thread`類並重寫`run()`方法的方式來自定義線程，具體的代碼如下所示。

```Python
import random
import time
from threading import Thread


class DownloadThread(Thread):

    def __init__(self, filename):
        self.filename = filename
        super().__init__()

    def run(self):
        start = time.time()
        print(f'開始下載 {self.filename}.')
        time.sleep(random.randint(3, 6))
        print(f'{self.filename} 下載完成.')
        end = time.time()
        print(f'下載耗時: {end - start:.3f}秒.')


def main():
    threads = [
        DownloadThread('Python從入門到住院.pdf'),
        DownloadThread('MySQL從刪庫到跑路.avi'),
        DownloadThread('Linux從精通到放棄.mp4')
    ]
    start = time.time()
    # 啟動三個線程
    for thread in threads:
        thread.start()
    # 等待線程結束
    for thread in threads:
        thread.join()
    end = time.time()
    print(f'總耗時: {end - start:.3f}秒.')


if __name__ == '__main__':
    main()
```

#### 使用線程池

我們還可以通過線程池的方式將任務放到多個線程中去執行，通過線程池來使用線程應該是多線程編程最理想的選擇。事實上，線程的創建和釋放都會帶來較大的開銷，頻繁的創建和釋放線程通常都不是很好的選擇。利用線程池，可以提前準備好若干個線程，在使用的過程中不需要再通過自定義的代碼創建和釋放線程，而是直接復用線程池中的線程。 Python 內置的`concurrent.futures`模塊提供了對線程池的支持，代碼如下所示。

```Python
import random
import time
from concurrent.futures import ThreadPoolExecutor
from threading import Thread


def download(*, filename):
    start = time.time()
    print(f'開始下載 {filename}.')
    time.sleep(random.randint(3, 6))
    print(f'{filename} 下載完成.')
    end = time.time()
    print(f'下載耗時: {end - start:.3f}秒.')


def main():
    with ThreadPoolExecutor(max_workers=4) as pool:
        filenames = ['Python從入門到住院.pdf', 'MySQL從刪庫到跑路.avi', 'Linux從精通到放棄.mp4']
        start = time.time()
        for filename in filenames:
            pool.submit(download, filename=filename)
    end = time.time()
    print(f'總耗時: {end - start:.3f}秒.')


if __name__ == '__main__':
    main()
```

### 守護線程

所謂“守護線程”就是在主線程結束的時候，不值得再保留的執行線程。這裡的不值得保留指的是守護線程會在其他非守護線程全部運行結束之後被銷毀，它守護的是當前進程內所有的非守護線程。簡單的說，守護線程會跟隨主線程一起掛掉，而主線程的生命週期就是一個進程的生命週期。如果不理解，我們可以看一段簡單的代碼。

```Python
import time
from threading import Thread


def display(content):
    while True:
        print(content, end='', flush=True)
        time.sleep(0.1)


def main():
    Thread(target=display, args=('Ping', )).start()
    Thread(target=display, args=('Pong', )).start()


if __name__ == '__main__':
    main()
```

> **說明**：上面的代碼中，我們將`print`函數的參數`flush`設置為`True`，這是因為`flush`參數的值如果為`False`，而`print`又沒有做換行處理，就會導致每次`print`輸出的內容被放到操作系統的輸出緩衝區，直到緩衝區被輸出的內容塞滿，才會清空緩衝區產生一次輸出。上述現像是操作系統為了減少 I/O 中斷，提升 CPU 利用率做出的設定，為了讓代碼產生直觀交互，我們才將`flush`參數設置為`True`，強制每次輸出都清空輸出緩衝區。

上面的代碼運行起來之後是不會停止的，因為兩個子線程中都有死循環，除非你手動中斷代碼的執行。但是，如果在創建線程對象時，將名為`daemon`的參數設置為`True`，這兩個線程就會變成守護線程，那麼在其他線程結束時，即便有死循環，兩個守護線程也會掛掉，不會再繼續執行下去，代碼如下所示。

 ```Python
 import time
 from threading import Thread
 
 
 def display(content):
     while True:
         print(content, end='', flush=True)
         time.sleep(0.1)
 
 
 def main():
     Thread(target=display, args=('Ping', ), daemon=True).start()
     Thread(target=display, args=('Pong', ), daemon=True).start()
     time.sleep(5)
 
 
 if __name__ == '__main__':
     main()
 ```

上面的代碼，我們在主線程中添加了一行`time.sleep(5)`讓主線程休眠5秒，在這個過程中，輸出`Ping`和`Pong`的守護線程會持續運轉，直到主線程在5秒後結束，這兩個守護線程也被銷毀，不再繼續運行。

> **思考**：如果將上面代碼第12行的`daemon=True`去掉，代碼會怎樣執行？有興趣的讀者可以嘗試一下，並看看實際執行的結果跟你想像的是否一致。

### 資源競爭

在編寫多線程代碼時，不可避免的會遇到多個線程競爭同一個資源（對象）的情況。在這種情況下，如果沒有合理的機制來保護被競爭的資源，那麼就有可能出現非預期的狀況。下面的代碼創建了`100`個線程向同一個銀行賬戶（初始餘額為`0`元）轉賬，每個線程轉賬金額為`1`元。在正常的情況下，我們的銀行賬戶最終的餘額應該是`100`元，但是運行下面的代碼我們並不能得到`100`元這個結果。

```Python
import time

from concurrent.futures import ThreadPoolExecutor


class Account(object):
    """銀行賬戶"""

    def __init__(self):
        self.balance = 0.0

    def deposit(self, money):
        """存錢"""
        new_balance = self.balance + money
        time.sleep(0.01)
        self.balance = new_balance


def main():
    """主函數"""
    account = Account()
    with ThreadPoolExecutor(max_workers=16) as pool:
        for _ in range(100):
            pool.submit(account.deposit, 1)
    print(account.balance)


if __name__ == '__main__':
    main()
```

上面代碼中的`Account`類代表了銀行賬戶，它的`deposit`方法代表存款行為，參數`money`代表存入的金額，該方法通過`time.sleep`函數模擬受理存款需要一段時間。我們通過線程池的方式啟動了`100`個線程向一個賬戶轉賬，但是上面的代碼並不能運行出`100`這個我們期望的結果，這就是在多個線程競爭一個資源的時候，可能會遇到的數據不一致的問題。注意上面代碼的第`14`行，當多個線程都執行到這行代碼時，它們會在相同的餘額上執行加上存入金額的操作，這就會造成“丟失更新”現象，即之前修改數據的成果被後續的修改給覆蓋掉了，所以才得不到正確的結果。

要解決上面的問題，可以使用鎖機制，通過鎖對操作數據的關鍵代碼加以保護。 Python 標準庫的`threading`模塊提供了`Lock`和`RLock`類來支持鎖機制，這裡我們不去深究二者的區別，建議大家直接使用`RLock`。接下來，我們給銀行賬戶添加一個鎖對象，通過鎖對象來解決剛才存款時發生“丟失更新”的問題，代碼如下所示。

```Python
import time

from concurrent.futures import ThreadPoolExecutor
from threading import RLock


class Account(object):
    """銀行賬戶"""

    def __init__(self):
        self.balance = 0.0
        self.lock = RLock()

    def deposit(self, money):
        # 獲得鎖
        self.lock.acquire()
        try:
            new_balance = self.balance + money
            time.sleep(0.01)
            self.balance = new_balance
        finally:
            # 釋放鎖
            self.lock.release()


def main():
    """主函數"""
    account = Account()
    with ThreadPoolExecutor(max_workers=16) as pool:
        for _ in range(100):
            pool.submit(account.deposit, 1)
    print(account.balance)


if __name__ == '__main__':
    main()
```

上面代碼中，獲得鎖和釋放鎖的操作也可以通過上下文語法來實現，使用上下文語法會讓代碼更加簡單優雅，這也是我們推薦大家使用的方式。

```Python
import time

from concurrent.futures import ThreadPoolExecutor
from threading import RLock


class Account(object):
    """銀行賬戶"""

    def __init__(self):
        self.balance = 0.0
        self.lock = RLock()

    def deposit(self, money):
        # 通過上下文語法獲得鎖和釋放鎖
        with self.lock:
            new_balance = self.balance + money
            time.sleep(0.01)
            self.balance = new_balance


def main():
    """主函數"""
    account = Account()
    with ThreadPoolExecutor(max_workers=16) as pool:
        for _ in range(100):
            pool.submit(account.deposit, 1)
    print(account.balance)


if __name__ == '__main__':
    main()
```

> **思考**：將上面的代碼修改為5個線程向銀行賬戶存錢，5個線程從銀行賬戶取錢，取錢的線程在銀行賬戶餘額不足時，需要停下來等待存錢的線程將錢存入後再嘗試取錢。這裡需要用到線程調度的知識，大家可以自行研究下`threading`模塊中的`Condition`類，看看是否能夠完成這個任務。

### GIL問題

如果使用官方的 Python 解釋器（通常稱之為 CPython）運行 Python 程序，我們並不能通過使用多線程的方式將 CPU 的利用率提升到逼近400%（對於4核 CPU）或逼近800%（對於8核 CPU）這樣的水平，因為 CPython 在執行代碼時，會受到 GIL（全局解釋器鎖）的限制。具體的說，CPython 在執行任何代碼時，都需要對應的線程先獲得 GIL，然後每執行100條（字節碼）指令，CPython 就會讓獲得 GIL 的線程主動釋放 GIL，這樣別的線程才有機會執行。因為 GIL 的存在，無論你的 CPU 有多少個核，我們編寫的 Python 代碼也沒有機會真正並行的執行。

GIL 是官方 Python 解釋器在設計上的歷史遺留問題，要解決這個問題，讓多線程能夠發揮 CPU 的多核優勢，需要重新實現一個不帶 GIL 的 Python 解釋器。這個問題按照官方的說法，在 Python 發布4.0版本時會得到解決，就讓我們拭目以待吧。當下，對於 CPython 而言，如果希望充分發揮 CPU 的多核優勢，可以考慮使用多進程，因為每個進程都對應一個 Python 解釋器，因此每個進程都有自己獨立的 GIL，這樣就可以突破 GIL 的限制。在下一個章節中，我們會為大家介紹關於多進程的相關知識，並對多線程和多進程的代碼及其執行效果進行比較。
