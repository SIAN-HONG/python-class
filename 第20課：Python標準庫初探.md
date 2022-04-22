## 第20課：Python標準庫初探

Python語言最可愛的地方在於它的標準庫和三方庫實在是太豐富了，日常開發工作中的很多任務都可以通過這些標準庫或者三方庫直接解決。下面我們先介紹Python標準庫中的一些常用模塊，後面的課程中再陸陸續續為大家介紹Python常用三方庫的用途和用法。

### base64 - Base64編解碼模塊

**Base64**是一種基於64個可打印字符來表示二進制數據的方法。由於$log _{2}64=6$，所以Base64以6個比特（二進制位，可以表示0或1）為一個單元，每個單元對應一個可打印字符。對於3字節（24比特）的二進制數據，我們可以將其處理成對應於4個Base64單元，即3個字節可由4個可打印字符來表示。 Base64編碼可用來作為電子郵件的傳輸編碼，也可以用於其他需要將二進制數據轉成文本字符的場景，這使得在XML、JSON、YAML這些文本數據格式中傳輸二進制內容成為可能。在Base64中的可打印字符包括`A-Z`、`a-z`、`0-9`，這裡一共是62個字符，另外兩個可打印符號通常是`+`和`/`，`=`用於在Base64編碼最後進行補位。

關於Base64編碼的細節，大家可以參考[《Base64筆記》](http://www.ruanyifeng.com/blog/2008/06/base64.html)一文，Python標準庫中的`base64`模塊提供了`b64encode`和`b64decode`兩個函數，專門用於實現Base64的編碼和解碼，下面演示了在**Python的交互式環境**中執行這兩個函數的效果。

```Python
>>> import base64
>>> 
>>> content = 'Man is distinguished, not only by his reason, but by this singular passion from other animals, which is a lust of the mind, that by a perseverance of delight in the continued and indefatigable generation of knowledge, exceeds the short vehemence of any carnal pleasure.'
>>> base64.b64encode(content.encode())
b'TWFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5IGhpcyByZWFzb24sIGJ1dCBieSB0aGlzIHNpbmd1bGFyIHBhc3Npb24gZnJvbSBvdGhlciBhbmltYWxzLCB3aGljaCBpcyBhIGx1c3Qgb2YgdGhlIG1pbmQsIHRoYXQgYnkgYSBwZXJzZXZlcmFuY2Ugb2YgZGVsaWdodCBpbiB0aGUgY29udGludWVkIGFuZCBpbmRlZmF0aWdhYmxlIGdlbmVyYXRpb24gb2Yga25vd2xlZGdlLCBleGNlZWRzIHRoZSBzaG9ydCB2ZWhlbWVuY2Ugb2YgYW55IGNhcm5hbCBwbGVhc3VyZS4='
>>> content = b'TWFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5IGhpcyByZWFzb24sIGJ1dCBieSB0aGlzIHNpbmd1bGFyIHBhc3Npb24gZnJvbSBvdGhlciBhbmltYWxzLCB3aGljaCBpcyBhIGx1c3Qgb2YgdGhlIG1pbmQsIHRoYXQgYnkgYSBwZXJzZXZlcmFuY2Ugb2YgZGVsaWdodCBpbiB0aGUgY29udGludWVkIGFuZCBpbmRlZmF0aWdhYmxlIGdlbmVyYXRpb24gb2Yga25vd2xlZGdlLCBleGNlZWRzIHRoZSBzaG9ydCB2ZWhlbWVuY2Ugb2YgYW55IGNhcm5hbCBwbGVhc3VyZS4='
>>> base64.b64decode(content).decode()
'Man is distinguished, not only by his reason, but by this singular passion from other animals, which is a lust of the mind, that by a perseverance of delight in the continued and indefatigable generation of knowledge, exceeds the short vehemence of any carnal pleasure.'
```

### collections - 容器數據類型模塊

`collections`模塊提供了諸多非常好用的數據結構，主要包括：

- `namedtuple`：命令元組，它是一個類工廠，接受類型的名稱和屬性列表來創建一個類。
- `deque`：雙端隊列，是列表的替代實現。 Python中的列表底層是基於數組來實現的，而`deque`底層是雙向鍊錶，因此當你需要在頭尾添加和刪除元素是，`deque`會表現出更好的性能，漸近時間複雜度為$O(1)$。
- `Counter`：`dict`的子類，鍵是元素，值是元素的計數，它的`most_common()`方法可以幫助我們獲取出現頻率最高的元素。 `Counter`和`dict`的繼承關係我認為是值得商榷的，按照CARP原則，`Counter`跟`dict`的關係應該設計為關聯關係更為合理。
- `OrderedDict`：`dict`的子類，它記錄了鍵值對插入的順序，看起來既有字典的行為，也有鍊錶的行為。
- `defaultdict`：類似於字典類型，但是可以通過默認的工廠函數來獲得鍵對應的默認值，相比字典中的`setdefault()`方法，這種做法更加高效。

下面是在**Python交互式環境中**使用`namedtuple`創建撲克牌類的例子。

```Python
>>> from collections import namedtuple
>>>
>>> Card = namedtuple('Card', ('suite', 'face'))
>>> card1 = Card('紅桃', 5)
>>> card2 = Card('草花', 9)
>>> card1
Card(suite='紅桃', face=5)
>>> card2
Card(suite='草花', face=9)
>>> print(f'{card1.suite}{card1.face}')
紅桃5
>>> print(f'{card2.suite}{card2.face}')
草花9
```

下面是使用`Counter`類統計列表中出現次數最多的三個元素的例子。

```Python
from collections import Counter

words = [
    'look', 'into', 'my', 'eyes', 'look', 'into', 'my', 'eyes',
    'the', 'eyes', 'the', 'eyes', 'the', 'eyes', 'not', 'around',
    'the', 'eyes', "don't", 'look', 'around', 'the', 'eyes',
    'look', 'into', 'my', 'eyes', "you're", 'under'
]
counter = Counter(words)
# 打印words列表中出現頻率最高的3個元素及其出現次數
for elem, count in counter.most_common(3):
    print(elem, count)
```

### hashlib - 哈希函數模塊

哈希函數又稱哈希算法或散列函數，是一種為已有的數據創建“數字指紋”（哈希摘要）的方法。哈希函數把數據壓縮成摘要，對於相同的輸入，哈希函數可以生成相同的摘要（數字指紋），需要注意的是這個過程並不可逆（不能通過摘要計算出輸入的內容）。一個優質的哈希函數能夠為不同的輸入生成不同的摘要，出現哈希衝突（不同的輸入產生相同的摘要）的概率極低，[MD5](https://zh.wikipedia.org/wiki/MD5)、[SHA家族]([https://zh.wikipedia.org/wiki/SHA%E5%AE%B6%E6%97%8F](https://zh.wikipedia.org/wiki/SHA家族))就是這類好的哈希函數。

> **說明**：在2011年的時候，RFC 6151中已經禁止將MD5用作密鑰散列消息認證碼，這個問題不在我們討論的範圍內。

Python標準庫的`hashlib`模塊提供了對哈希函數的封裝，通過使用`md5`、`sha1`、`sha256`等類，我們可以輕鬆的生成“數字指紋”。舉一個簡單的例子，用戶註冊時我們希望在數據庫中保存用戶的密碼，很顯然我們不能將用戶密碼直接保存在數據庫中，這樣可能會導致用戶隱私的洩露，所以在數據庫中保存用戶密碼時，通常都會將密碼的“指紋”保存起來，用戶登錄時通過哈希函數計算密碼的“指紋”再進行匹配來判斷用戶登錄是否成功。

```Python
import hashlib

# 計算字符串"123456"的MD5摘要
print(hashlib.md5('123456'.encode()).hexdigest())

# 計算文件"Python-3.7.1.tar.xz"的MD5摘要
hasher = hashlib.md5()
with open('Python-3.7.1.tar.xz', 'rb') as file:
    data = file.read(512)
    while data:
        hasher.update(data)
        data = file.read(512)
print(hasher.hexdigest())
```

> **說明**：很多網站在下載鏈接的旁邊都提供了哈希摘要，完成文件下載後，我們可以計算該文件的哈希摘要並檢查它與網站上提供的哈希摘要是否一致（指紋比對）。如果計算出的哈希摘要與網站提供的並不一致，很有可能是下載出錯或該文件在傳輸過程中已經被篡改，這時候就不應該直接使用這個文件。

### heapq - 堆排序模塊

`heapq`模塊實現了堆排序算法，如果希望使用堆排序，尤其是要解決**TopK問題**（從序列中找到K個最大或最小元素），直接使用該模塊即可，代碼如下所示。

```Python
import heapq

list1 = [34, 25, 12, 99, 87, 63, 58, 78, 88, 92]
# 找出列表中最大的三個元素
print(heapq.nlargest(3, list1))
# 找出列表中最小的三個元素
print(heapq.nsmallest(3, list1))

list2 = [
    {'name': 'IBM', 'shares': 100, 'price': 91.1},
    {'name': 'AAPL', 'shares': 50, 'price': 543.22},
    {'name': 'FB', 'shares': 200, 'price': 21.09},
    {'name': 'HPQ', 'shares': 35, 'price': 31.75},
    {'name': 'YHOO', 'shares': 45, 'price': 16.35},
    {'name': 'ACME', 'shares': 75, 'price': 115.65}
]
# 找出價格最高的三隻股票
print(heapq.nlargest(3, list2, key=lambda x: x['price']))
# 找出持有數量最高的三隻股票
print(heapq.nlargest(3, list2, key=lambda x: x['shares']))
```

### itertools - 迭代工具模塊

`itertools`可以幫助我們生成各種各樣的迭代器，大家可以看看下面的例子。

```Python
import itertools

# 產生ABCD的全排列
for value in itertools.permutations('ABCD'):
    print(value)

# 產生ABCDE的五選三組合
for value in itertools.combinations('ABCDE', 3):
    print(value)

# 產生ABCD和123的笛卡爾積
for value in itertools.product('ABCD', '123'):
    print(value)

# 產生ABC的無限循環序列
it = itertools.cycle(('A', 'B', 'C'))
print(next(it))
print(next(it))
print(next(it))
print(next(it))
```

### random - 隨機數和隨機抽樣模塊

這個模塊我們之前已經用過很多次了，生成隨機數、實現隨機亂序和隨機抽樣，下面是常用函數的列表。

- `getrandbits(k)`：返回具有`k`個隨機比特位的整數。
- `randrange(start, stop[, step])`：從`range(start, stop, step)` 返回一個隨機選擇的元素，但實際上並沒有構建一個`range`對象。
- `randint(a, b)`：返回隨機整數`N`滿足`a <= N <= b`，相當於`randrange(a, b+1)`。
- `choice(seq)`：從非空序列`seq`返回一個隨機元素。如果`seq`為空，則引發`IndexError`。
- `choices(population, weight=None, *, cum_weights=None, k=1)`：從`population`中選擇替換，返回大小為`k`的元素列表。如果`population`為空，則引發`IndexError`。
- `shuffle(x[, random])`：將序列`x`隨機打亂位置。
- `sample(population, k)`：返回從總體序列或集合中選擇`k`個不重複元素構造的列表，用於無重複的隨機抽樣。
- `random()`：返回`[0.0, 1.0)`範圍內的下一個隨機浮點數。
- `expovariate(lambd)`：指數分佈。
- `gammavariate(alpha, beta)`：伽瑪分佈。
- `gauss(mu, sigma)` / `normalvariate(mu, sigma)`：正態分佈。
- `paretovariate(alpha)`：帕累托分佈。
- `weibullvariate(alpha, beta)`：威布爾分佈。

### os.path - 路徑操作相關模塊

`os.path`模塊封裝了操作路徑的工具函數，如果程序中需要對文件路徑做拼接、拆分、獲取以及獲取文件的存在性和其他屬性，這個模塊將會非常有幫助，下面為大家羅列一些常用的函數。

- `dirname(path)`：返迴路徑`path`的目錄名稱。
- `exists(path)`：如果`path`指向一個已存在的路徑或已打開的文件描述符，返回 `True`。
- `getatime(path)` / `getmtime(path)` / `getctime(path)`：返回`path`的最後訪問時間/最後修改時間/創建時間。
- `getsize(path)`：返回`path`的大小，以字節為單位。如果該文件不存在或不可訪問，則拋出`OSError`異常。
- `isfile(path)`：如果`path`是普通文件，則返回 `True`。
- `isdir(path)`：如果`path`是目錄（文件夾），則返回`True`。
- `join(path, *paths)`：合理地拼接一個或多個路徑部分。返回值是`path`和`paths`所有值的連接，每個非空部分後面都緊跟一個目錄分隔符 (`os.sep`)，除了最後一部分。這意味著如果最後一部分為空，則結果將以分隔符結尾。如果參數中某個部分是絕對路徑，則絕對路徑前的路徑都將被丟棄，並從絕對路徑部分開始連接。
- `splitext(path)`：將路徑`path`拆分為一對，即`(root, ext)`，使得`root + ext == path`，其中`ext`為空或以英文句點開頭，且最多包含一個句點。

### uuid - UUID生成模塊

`uuid`模塊可以幫助我們生成全局唯一標識符（Universal Unique IDentity）。該模塊提供了四個用於生成UUID的函數，分別是：

- `uuid1()`：由MAC地址、當前時間戳、隨機數生成，可以保證全球範圍內的唯一性。
- `uuid3(namespace, name)`：通過計算命名空間和名字的MD5哈希摘要（“指紋”）值得到，保證了同一命名空間中不同名字的唯一性，和不同命名空間的唯一性，但同一命名空間的同一名字會生成相同的UUID。
- `uuid4()`：由偽隨機數生成UUID，有一定的重複概率，該概率可以計算出來。
- `uuid5()`：算法與`uuid3`相同，只不過哈希函數用SHA-1取代了MD5。

由於`uuid4`存在概率型重複，那麼在真正需要全局唯一標識符的地方最好不用使用它。在分佈式環境下，`uuid1`是很好的選擇，因為它能夠保證生成ID的全局唯一性。下面是在**Python交互式環境中**使用`uuid1`函數生成全局唯一標識符的例子。

```Python
>>> import uuid
>>> uuid.uuid1().hex
'622a8334baab11eaaa9c60f81da8d840'
>>> uuid.uuid1().hex
'62b066debaab11eaaa9c60f81da8d840'
>>> uuid.uuid1().hex
'642c0db0baab11eaaa9c60f81da8d840'
```

###  簡單的總結

Python標準庫中有大量的模塊，日常開發中有很多常見的任務在Python標準庫中都有封裝好的函數或類可供使用，這也是Python這門語言最可愛的地方。
