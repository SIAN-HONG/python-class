## 第35課：Python中的並發編程-2

在上一課中我們說過，由於 GIL 的存在，CPython 中的多線程並不能發揮 CPU 的多核優勢，如果希望突破 GIL 的限制，可以考慮使用多進程。對於多進程的程序，每個進程都有一個屬於自己的 GIL，所以多進程不會受到 GIL 的影響。那麼，我們應該如何在 Python 程序中創建和使用多進程呢？

###創建進程

在 Python 中可以基於`Process`類來創建進程，雖然進程和線程有著本質的差別，但是`Process`類和`Thread`類的用法卻非常類似。在使用`Process`類的構造器創建對象時，也是通過`target`參數傳入一個函數來指定進程要執行的代碼，而`args`和`kwargs`參數可以指定該函數使用的參數值。

```Python
from multiprocessing import Process, current_process
from time import sleep


def sub_task(content, nums):
    # 通過current_process函數獲取當前進程對象
    # 通過進程對象的pid和name屬性獲取進程的ID號和名字
    print(f'PID: {current_process().pid}')
    print(f'Name: {current_process().name}')
    # 通過下面的輸出不難發現，每個進程都有自己的nums列表，進程之間本就不共享內存
    # 在創建子進程時復制了父進程的數據結構，三個進程從列表中pop(0)得到的值都是20
    counter, total = 0, nums.pop(0)
    print(f'Loop count: {total}')
    sleep(0.5)
    while counter < total:
        counter += 1
        print(f'{counter}: {content}')
        sleep(0.01)


def main():
    nums = [20, 30, 40]
    # 創建並啟動進程來執行指定的函數
    Process(target=sub_task, args=('Ping', nums)).start()
    Process(target=sub_task, args=('Pong', nums)).start()
    # 在主進程中執行sub_task函數
    sub_task('Good', nums)


if __name__ == '__main__':
    main()
```

> **說明**：上面的代碼通過`current_process`函數獲取當前進程對象，再通過進程對象的`pid`屬性獲取進程ID。在 Python 中，使用`os`模塊的`getpid`函數也可以達到同樣的效果。

如果願意，也可以使用`os`模塊的`fork`函數來創建進程，調用該函數時，操作系統自動把當前進程（父進程）複製一份（子進程），父進程的`fork`函數會返回子進程的ID，而子進程中的`fork`函數會返回`0`，也就是說這個函數調用一次會在父進程和子進程中得到兩個不同的返回值。需要注意的是，Windows 系統並不支持`fork`函數，如果你使用的是 Linux 或 macOS 系統，可以試試下面的代碼。

```Python
import os

print(f'PID: {os.getpid()}')
pid = os.fork()
if pid == 0:
    print(f'子進程 - PID: {os.getpid()}')
    print('Todo: 在子進程中執行的代碼')
else:
    print(f'父進程 - PID: {os.getpid()}')
    print('Todo: 在父進程中執行的代碼')
```

簡而言之，我們還是推薦大家通過直接使用`Process`類、繼承`Process`類和使用進程池（`ProcessPoolExecutor`）這三種方式來創建和使用多進程，這三種方式不同於上面的`fork`函數，能夠保證代碼的兼容性和可移植性。具體的做法跟之前講過的創建和使用多線程的方式比較接近，此處不再進行贅述。

### 多進程和多線程的比較

對於爬蟲這類 I/O 密集型任務來說，使用多進程並沒有什麼優勢；但是對於計算密集型任務來說，多進程相比多線程，在效率上會有顯著的提升，我們可以通過下面的代碼來加以證明。下面的代碼會通過多線程和多進程兩種方式來判斷一組大整數是不是質數，很顯然這是一個計算密集型任務，我們將任務分別放到多個線程和多個進程中來加速代碼的執行，讓我們看看多線程和多進程的代碼具體表現有何不同。

我們先實現一個多線程的版本，代碼如下所示。

```Python
import concurrent.futures

PRIMES = [
    1116281,
    1297337,
    104395303,
    472882027,
    533000389,
    817504243,
    982451653,
    112272535095293,
    112582705942171,
    112272535095293,
    115280095190773,
    115797848077099,
    1099726899285419
] * 5


def is_prime(n):
    """判斷素數"""
    for i in range(2, int(n ** 0.5) + 1):
        if n % i == 0:
            return False
    return n != 1


def main():
    """主函數"""
    with concurrent.futures.ThreadPoolExecutor(max_workers=16) as executor:
        for number, prime in zip(PRIMES, executor.map(is_prime, PRIMES)):
            print('%d is prime: %s' % (number, prime))


if __name__ == '__main__':
    main()
```

假設上面的代碼保存在名為`example.py`的文件中，在 Linux 或 macOS 系統上，可以使用`time python example.py`命令執行程序並獲得操作系統關於執行時間的統計，在我的 macOS 上，某次的運行結果的最後一行輸出如下所示。

```
python example09.py  38.69s user 1.01s system 101% cpu 39.213 total
```

從運行結果可以看出，多線程的代碼只能讓 CPU 利用率達到100%，這其實已經證明了多線程的代碼無法利用 CPU 多核特性來加速代碼的執行，我們再看看多進程的版本，我們將上面代碼中的線程池（`ThreadPoolExecutor`）更換為進程池（`ProcessPoolExecutor`）。

多進程的版本。

```Python
import concurrent.futures

PRIMES = [
    1116281,
    1297337,
    104395303,
    472882027,
    533000389,
    817504243,
    982451653,
    112272535095293,
    112582705942171,
    112272535095293,
    115280095190773,
    115797848077099,
    1099726899285419
] * 5


def is_prime(n):
    """判斷素數"""
    for i in range(2, int(n ** 0.5) + 1):
        if n % i == 0:
            return False
    return n != 1


def main():
    """主函數"""
    with concurrent.futures.ProcessPoolExecutor(max_workers=16) as executor:
        for number, prime in zip(PRIMES, executor.map(is_prime, PRIMES)):
            print('%d is prime: %s' % (number, prime))


if __name__ == '__main__':
    main()
```

> **提示**：運行上面的代碼時，可以通過操作系統的任務管理器（資源監視器）來查看是否啟動了多個 Python  解釋器進程。

我們仍然通過`time python example.py`的方式來執行上述代碼，運行結果的最後一行如下所示。

```
python example09.py 106.63s user 0.57s system 389% cpu 27.497 total
```

可以看出，多進程的版本在我使用的這台電腦上，讓 CPU 的利用率達到了將近400%，而運行代碼時用戶態耗費的 CPU 的時間（106.63秒）幾乎是代碼運行總時間（27.497秒）的4倍，從這兩點都可以看出，我的電腦使用了一款4核的 CPU。當然，要知道自己的電腦有幾個 CPU 或幾個核，可以直接使用下面的代碼。

```Python
import os

print(os.cpu_count())
```

綜上所述，多進程可以突破 GIL 的限制，充分利用 CPU 多核特性，對於計算密集型任務，這一點是相當重要的。常見的計算密集型任務包括科學計算、圖像處理、音視頻編解碼等，如果這些計算密集型任務本身是可以並行的，那麼使用多進程應該是更好的選擇。

### 進程間通信

在講解進程間通信之前，先給大家一個任務：啟動兩個進程，一個輸出“Ping”，一個輸出“Pong”，兩個進程輸出的“Ping”和“Pong”加起來一共有50個時，就結束程序。聽起來是不是非常簡單，但是實際編寫代碼時，由於多個進程之間不能夠像多個線程之間直接通過共享內存的方式交換數據，所以下面的代碼是達不到我們想要的結果的。

```Python
from multiprocessing import Process
from time import sleep

counter = 0


def sub_task(string):
    global counter
    while counter < 50:
        print(string, end='', flush=True)
        counter += 1
        sleep(0.01)

        
def main():
    Process(target=sub_task, args=('Ping', )).start()
    Process(target=sub_task, args=('Pong', )).start()


if __name__ == '__main__':
    main()
```

上面的代碼看起來沒毛病，但是最後的結果是“Ping”和“Pong”各輸出了50個。再次提醒大家，當我們在程序中創建進程的時候，子進程會復制父進程及其所有的數據結構，每個子進程有自己獨立的內存空間，這也就意味著兩個子進程中各有一個`counter`變量，它們都會從`0`加到`50`，所以結果就可想而知了。要解決這個問題比較簡單的辦法是使用`multiprocessing`模塊中的`Queue`類，它是可以被多個進程共享的隊列，底層是通過操作系統底層的管道和信號量（semaphore）機制來實現的，代碼如下所示。

```Python
import time
from multiprocessing import Process, Queue


def sub_task(content, queue):
    counter = queue.get()
    while counter < 50:
        print(content, end='', flush=True)
        counter += 1
        queue.put(counter)
        time.sleep(0.01)
        counter = queue.get()


def main():
    queue = Queue()
    queue.put(0)
    p1 = Process(target=sub_task, args=('Ping', queue))
    p1.start()
    p2 = Process(target=sub_task, args=('Pong', queue))
    p2.start()
    while p1.is_alive() and p2.is_alive():
        pass
    queue.put(50)


if __name__ == '__main__':
    main()
```

> **提示**：`multiprocessing.Queue`對象的`get`方法默認在隊列為空時是會阻塞的，直到獲取到數據才會返回。如果不希望該方法阻塞以及需要指定阻塞的超時時間，可以通過指定`block`和`timeout`參數進行設定。

上面的代碼通過`Queue`類的`get`和`put`方法讓三個進程（`p1`、`p2`和主進程）實現了數據的共享，這就是所謂的進程間的通信，通過這種方式，當`Queue`中取出的值已經大於等於`50`時，`p1`和`p2`就會跳出`while`循環，從而終止進程的執行。代碼第22行的循環是為了等待`p1`和`p2`兩個進程中的一個結束，這時候主進程還需要向`Queue`中放置一個大於等於`50`的值，這樣另一個尚未結束的進程也會因為讀到這個大於等於`50`的值而終止。

進程間通信的方式還有很多，比如使用套接字也可以實現兩個進程的通信，甚至於這兩個進程並不在同一台主機上，有興趣的讀者可以自行了解。

###  簡單的總結

在 Python 中，我們還可以通過`subprocess`模塊的`call`函數執行其他的命令來創建子進程，相當於就是在我們的程序中調用其他程序，這裡我們暫不探討這些知識，有興趣的讀者可以自行研究。

對於Python開發者來說，以下情況需要考慮使用多線程：

1. 程序需要維護許多共享的狀態（尤其是可變狀態），Python 中的列表、字典、集合都是線程安全的（多個線程同時操作同一個列表、字典或集合，不會引發錯誤和數據問題），所以使用線程而不是進程維護共享狀態的代價相對較小。
2. 程序會花費大量時間在 I/O 操作上，沒有太多並行計算的需求且不需佔用太多的內存。

那麼在遇到下列情況時，應該考慮使用多進程：

1. 程序執行計算密集型任務（如：音視頻編解碼、數據壓縮、科學計算等）。
2. 程序的輸入可以並行的分成塊，並且可以將運算結果合併。
3. 程序在內存使用方面沒有任何限制且不強依賴於 I/O 操作（如讀寫文件、套接字等）。
