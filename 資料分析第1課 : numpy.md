### 前言
今天主要會跟大家分享如何使用NumPy 來做矩陣的應用，這在後面進行資料分析或是機器學習都有很大的幫助

### 環境準備
如果想用自己的電腦來跑的話需要安裝 Package
```Python
# For anoconda (recommend)
$ conda install numpy

# For pip
$ pip install numpy
```
### NumPy
NumPy是Python語言的一個擴充程式庫。特色是它可以支援多維的陣列或矩陣，裏頭包含強大的數學函示庫，因為是平行運算，所以當資料量很大的話，可以比單純使用list，還要快上很多。
Numpy是參考CPyton創造出來的，因為Python在執行數學運算上，直譯器的程式碼會比編譯器來的慢許多，因此特別為NumPy引入多維矩陣與陣列的數學函式，使的它的速度可以跟編譯器一樣快。
註 : CPython是一種直譯器  

它是資料處理很重要的基礎，之後可運用在Pandas, SciPy, Scikit-learn...。  
* Numpy可運用在
1. 多維陣列
2. 線性代數
3. 傅立葉轉換
4. 亂數產生  
...  
#### 引入NumPy
要使用NumPy的話，需要引入這個library，習慣上會用 np 作為縮寫

```Python
import numpy as np
```
#### 創造陣列

1. numpy.array  
最基本用來創造array的方法，常用於初始化陣列
```Python
np.array(object, 
         dtype = None,
         copy = True, 
         order = None, 
         subok = False, 
         ndmin = 0)
```
object : 定義的陣列  
dtype : bool(布林值), int(整數), float(浮點數), complex(複數)  
copy : 複製array，預設為True，複製值的意思，原始的資料變動，不會影響到新產生的陣列，反之False的話，不僅複製值位置也一起複製了，因此原始資料變動，新陣列裡的元素也會改變  
order : 'C'(以行的順序儲存資料); 'F'(以列的順序儲存資料))  
subok : 預設為False，基本上不會用到，在此就不做介紹了  
ndmin : 最小維度 

2. numpy.arange  
此陣列以等差數列的形式產生，指定間隔  
```Python
numpy.arange(start, stop, step, dtype)
```
start : 從某數開始，預設為0  
stop : 到某數停止(不包含)  
step : 間格多少  
dtype : bool(布林值), int(整數), float(浮點數), complex(複數)  

3. numpy.linspace(等差數列)  
此陣列以等差數列的形式產生，指定個數  
```Python
np.linspace(start, stop, 
            num=50, 
            endpoint=True, 
            retstep=False, 
            dtype=None)
```            
start : 從某數開始  
stop : 到某數停止  
num : start到stop之間有多少數字  
endpoint : True(包含stop); False(不包含stop)，預設為True  
restep : 若是True，則會在array的後面，加上間隔多少，預設為False  
dtype : bool(布林值), int(整數), float(浮點數), complex(複數)  

4. numpy.logspace(等比數列)  
此陣列以等比數列的形式產生  
```Python
np.logspace(start, stop, num=50, endpoint=True, base=10.0, dtype=None)
```
start : 從某數開始  
stop : 到某數停止  
num : start到stop之間有多少數字  
endpoint : True(包含stop); False(不包含stop)，預設為True  
base : 以多少為底數，預設為10.0  
dtype : bool(布林值), int(整數), float(浮點數), complex(複數)  

```Python
a = np.array([1, 2, 3]) # 建立一維陣列
b = np.array([(2, 3, 4), (5, 6, 7)]) # 建立二維陣列
c = np.arange(10) # 0到9，不包含10
d = np.arange(1, 8, 2) # 1到7，間隔為2
e = np.zeros(3) # 全是0,dtype是float
f = np.ones(3) # 全是1,dtype是float
g = np.empty(2) # 空的，但非零，有殘留值
h = np.linspace(10, 20,  5, endpoint =  False) # 10到20(不包含)等差的5個數
i = np.logspace(10, 1000,  5, endpoint =  True) # 10到1000(包含)等比(以10為底)的五個數
```

#### 四則運算
array的大小一致的話，可以直接做加減乘除的運算，其運算是元素對元素  

```Python
a = np.array([1, 2, 3])
b = np.array([2, 3, 4])
```
\# 加法，c和d同義  
c = a + b  
d = np.add(a, b)

\# 減法，e和f同義  
e = a - b  
f = np.subtract(a, b) 

\# 乘法，g和h同義  
g = a * b  
h = np.multiply(a, b)

\# 除法，i和j同義  
i = a / b  
j = np.divide(a, b)  

#### 基礎數學運算
NumPy提供的數學運算極多，這裡僅舉一些常見的例子，像是開根號、取log、高斯運算  

```Python
a = np.arange(3) # 產生一個一維陣列
b = np.sqrt(a) # 對每個數值開根號
c = np.exp(a) # 以e為底，數值為指數
d = np.log(a) # 將數值取log
e = np.sin(a) # 將數值取sin
f = np.cos(a) # 將數值取cos
x = np.random.randn(3) # 隨機產生3個數值的一維陣列
y = np.random.randn(3) # 隨機產生3個數值的一維陣列

m = np.maximum(x, y) # 取出x, y中比大的數值
n = np.sign(x) # 判斷x的正負號，1.為正，-1.為負
p = np.floor(x) # 地板函數，為不大於數值的最大整數
q = np.ceil(x) # 天花板函數，為不小於數值的最小整數
```
#### 廣播
在四則運算時，我們提到大小一致的話，可以直接進行運算，那如果不一致的話，難道就不行嗎？答案是可以的，只要用廣播的技巧即可  
如果兩個陣列的維度不一樣，則會在維度較小的陣列最前面補上一個維度  
只有 1 可以拓展上去  
拓展所有為 1 的地方使得兩個矩陣相對應的維度一樣  
如果拓展玩完還是不同或是沒有機會拓則不能做廣播  
```Python
'''
First:
    shape of a: (4, 3)
    shape of b: (3)
    
Start broadcast:
    b (3) -> (1, 3) # Add 1 at the first dimension
    b (1, 3) -> (4, 3) # Broadcast 1 to the corresponding number
'''

a = np.array([[ 0, 0, 0],
             [10,10,10],
             [20,20,20],
             [30,30,30]])             
b = np.array([1,2,3])

print(f"New result is {a+b}\nShape of the new result is {(a+b).shape}")
```
#### 布林遮罩
布林遮罩的意思是用一個判斷式，判斷是Ture還是Flase，常常用來做資料的分析  
舉個例子來說，現在有十萬筆的薪資資料，可以使用布林遮罩的方式將大於平均薪資的資料留下  

```Python
a = np.array([2, 3, 4, 5, 6, 7])
b = np.array([2, 3, 4, 5, 7, 8])

print(a == b) # 按元素判斷 
print(np.array_equal(a, b)) # 按陣列判斷
x = np.array([1, 2, 3, 4, 5, 6])
print(x > 3) # 按元素判斷 [False False False  True  True  True]

y = x[x > 3] # 將符合條件的元素取出
print(y) # [4 5 6]

x[x < 3] = 3 # 將符合條件的元素改成某值
print(x) # [3, 3, 3, 4, 5, 6]
```
#### 改變類型
可以利用astype的方式，將原本的type改成自己想要的type  

```Python
a = np.array([[1, 2, 3], [4, 5, 6]])
print(a.dtype) # 查看a的type int64

b = np.array([[1, 2, 3], [4, 5, 6]], dtype = np.float64)
print(b.dtype) # 查看b的type float64

c = a.astype(np.float64) # 將a的type改成float64，並指派給c
print(c.dtype) # 查看c的type float64

d = a.astype(b.dtype) # 將a的type改成跟b的type一樣，並指派給d
print(data4.dtype) # 查看d的type float64
```
#### 聚合函數
聚合函數常常用於統計的用途，像是常見的平均值、標準差，都包含在裏頭  

```Python
a = np.array([(2, 3, 4), (5, 6, 7)])

b = a.sum() # a元素的總和
c = a.sum(axis=0) # 以列為主，往下加
d = a.sum(axis=1) # 以行為主，往右加
e = a.cumsum(axis=0) # 以列為主，往下累加
f = a.cumsum(axis=1) # 以行為主，往右累加

g = a.max() # a元素的最大值
h = a.min() # a元素的最小值

i = np.median(a) # a元素的中位數
j = np.mean(a) # a元素的平均值
k = np.std(a) # a元素的標準差
l = np.var(a) # a元素的變異數
```
#### 索引及切片
索引及切片常用來查看或改變元素。索引是利用座標的概念，找出元素，而切片，是利用slice(start, stop, step)，對原陣列進行切割  

```Python
a = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])

print(a[0][2]) # 查看row0 查看row0 col2的元素 (3)
print(a[0,2]) # 查看row0 查看row0 col2的元素 (3)

print(a[1]) # [4 5 6] # 查看row1的所有元素

a[1] = 5 # 將row1全部改成5
print(a[1]) # [5 5 5]
a = np.arange(10)
b = slice(2,7,2) # 切出2到7(不包含)，間隔維2的元素
c = a[2:7:2] # 切出a中，2到7(不包含)，間隔維2的元素

print(a[b]) # [2 4 6]
print(c) # [2 4 6]
a = np.array([[1, 2, 3, 4, 5], 
              [6, 7, 8, 9, 10], 
              [11, 12, 13, 14, 15], 
              [16, 17, 18, 19, 20], 
              [21, 22, 23, 24, 25]])
              
b = a[(0, 1, 2, 3, 4), (1, 1, 2, 3, 4)] # 取出(0,1),(1,1),(2,2),(3,3),(4,4)的元素(藍色圈圈)
c = a[2:, [0, 2]] # 取出第2列以後，第0行及第2行的所有元素(紅色圈圈)
d = a[3:, 3:] # 取出第三列以後，第3行以後的所有元素(綠色圈圈)
```Python

冒號(:) start:stop(不包含)，若省略stop即代表到結尾  
舉例來說  
2: 表示從2開始到結束  
2:5 表示從2開始到4結束  

#### 改變陣列形狀
有時候為了方便，會將多維資料的矩陣，改成一維的陣列，或者其他大小，這時reshape()就派上用場了，需特別注意，改變時，元素的數目需一樣多，不然會有error喔  

```Python
x = np.array([1, 2, 3, 4, 5, 6]) # x為一維陣列，員數個數是6個
y = x.reshape([2, 3]) # 將x改成2*3的矩陣，元素個數是6個，並指派給y
z = x.reshape([3, 2]) # 將x改成3*2的矩陣，元素個數是6個，並指派給z
```
#### 迭代
迭代的意思是重複執行一段程式碼，例如對所有元素進行迭代，那麼就會執行到沒有元素後才會停止  

```Python
a = np.arange(0,60,5) # 產生一個array，0到60(不包含)，以5為間隔，有12個元素
a = a.reshape(3,4) # 將這12個元素改成3*4的矩陣

for x in np.nditer(a, order =  'C'): # 用迭代的方式讀取a裡的元素，並且以列的順序讀取
    print (x, end=" " )

for x in np.nditer(a, order =  'F'): # 用迭代的方式讀取a裡的元素，並且以行的順序讀取
    print (x, end=" " )
```Python
#### 矩陣運算
基本的矩陣運算，NumPy皆有包含，像是轉置、內積  

```Python
a = np.arange(12).reshape(3,4) # 產生元素為0~12(不包含)，3*4的矩陣

b = a.transpose() # 將a轉置
c = a.T # 將a轉置

d = np.dot(a, b) # 內積a*b
e = a @ b # 內積a*b

f = inv(a) # a的反函數

g = det(a) # a的行列式
a = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])
b = np.array([3, 2, 1])

x = solve(a, b) # 解方程式 ax = b，x的值
```
#### 整理資料
##### 排序
排序是整理資料的一個重要步驟，使用sort()的函數，可將資料由小排到大，或是由大排到小  
##### 不重複
有時候，重複的資料我們只需要一筆，這時，便是unique()派上用場的時候  
舉例來說，現在的資料有很多id(有重複)的購買紀錄，我們想查看到底有多少id，這時用unique()，裡面元素的個數便是id數(不重複)
```
a = np.array([7, 2, 5, 4, 3, 6])

b = a.sort() # 將a的元素由小排到大，並指派給b

c = a.sort(reverse=True) # 將a的元素由大排到小，並指派給c
reverse預設為Flase

a = np.array([7, 2, 5, 7, 2, 6])
b = np.unique(a1) # 將a的不重複的元素取出，並指派給b
```
### 結語
上面介紹了很多用法，看似零散，但在處理資料上，都是十分便利的工具。當然，這只是NumPy的基本知識，它還有更深更廣的用途，有興趣的話，可以去專研看看。
