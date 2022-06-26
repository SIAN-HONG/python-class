### 前言
今天是講解如何使用 pandas，pandas 是非常重要讀取、處理資料的工具！\
* Pandas
能夠有效率的對資料進行篩選及分析
Pandas主要分成兩類
Series(一維)
DataFrame(二維)
就像Excel 試算表的想法，在Python上呈現

能夠快速地進行對資料的前處理，例如缺失值、異質，進行取代或刪除
能夠從檔案中讀取資料，在整理好後，也能進行輸出
是分析資料不可或缺的好幫手

環境準備
如果想用自己的電腦來跑的話需要安裝 Package

# For anoconda (recommend)
$ conda install pandas

# For pip
$ pip install pandas
引入Pandas
要使用Pandas的話，需要引入這個library，習慣上會用 pd 作為縮寫。

import pandas as pd
一維(Series)
一維的資料，就像列表，習慣上每個Series會放相同類型的資料，例如姓名、年紀、性別之類的，通常Series不會單獨出現，會搭配DataFrame(下面會介紹到)。

建立Series
建立Series的方法，可對應到Pyhon中的list，使用[]，將資料輸入。

實作
data = pd.Series([2, 4, 6, 8, 10]) 
更改索引
Pandas在創造Series時會預設好索引值，由0開始，但有時為了方便尋找資料，或想要有更有意義的索引值，可以自己定義索引值，特別注意，在定義時，數量要與資料一樣，且不能重複，不然會有error產生。

實作
data = pd.Series([1, 2, 3, 4, 5]) # 使用預設的索引
data1 = pd.Series([1, 2, 3, 4, 5], index = ["a", "b", "c", "d", "e"]) # 自訂索引
查看資料屬性
在分析資料上，查看屬性常常是第一步，有時我們以為是int的資料，其實是String，這時怎麼分析都不對，因此建議在對資料進行分析時，事先都先了解每個資料型態，當然，其他屬性也有一定的重要性。

實作
data = pd.Series([2, 4, 6, 8, 10], index = ["a", "b", "c", "d", "e"]) # 原始資料

print(data.dtype) # 查看資料型態
print(data.size) # 查看資料筆數
print(data.index) # 查看資料索引
查看資料
想查看特定的筆數時，有兩種方法可以查看，第一種是利用預設的順序查看，由0開始，往下遞增，如果有自己定義索引，則可以用新的索引查看資料。

實作
data = pd.Series([1, 2, 3, 4, 5], index = ["a", "b", "c", "d", "e"]) # 自訂索引
print(data[0]) # 查看第一筆資料，按照順序
print(data["a"])# 查看第一筆資料，按照索引
數值統計分析
在上一篇我們介紹過Numpy有統計分析的功能，Pandas也不例外，基本的統計函數皆有包含。

實作
data = pd.Series([10, 22, 27, 13, 24], index = ["a", "b", "c", "d", "e"]) # 原始資料

print(data.sum()) # 列印出所有資料的和
print(data.mean()) # 列印出資料的中位數
print(data.std()) # 列印出資料的標準差
print(data.var()) # 列印出資料的變異數
print(data.prod()) # 列印出所有資料的乘積
print(data.max()) # 列印出資料的最大值
print(data.min()) # 列印出資料的最小值
print(data.nlargest(2)) # 列印出資料前兩大的值
print(data.nsmallest(2)) # 列印出資料前兩小的值
字串分析
當原始資料的字母大小寫不一時，在分析上會變得很困難，因此可以利用lower()，upper()，將字串改成統一的大小寫。或者，有時想將資料中字串進行轉換，例如把所有的"嗨"，改成"Hi"，使用Pandas都可以完成

所以字串的操作都定義在str(string)底下，因此在處理字串時，都要加上str

實作
data = pd.Series(["Apple", "Banana", "Cherry"])

print(data.str.lower()) # 將所有字串的字母變成小寫列印出來
print(data.str.upper()) # 將所有字串的字母變成大寫列印出來
print(data.str.cat(sep = ",")) # 將所有字串串接，並用逗號隔開，列印出來
print(data.str.contains("a")) # 判斷各個字串是否包含"A"，列印初布林值
print(data.str.replace("a","A")) # 把各個字串中的"a"取代成"A"列印出來
print(data.str.len()) # 列印出每個字串的長度
二維(DataFrame)
二維的資料，就像表格，DataFrame是由很多個Series組裝出來的，因此Series和DataFrame可互相轉換。因為是由Series組成，因此很多的寫法跟Series相似。

建立DataFrame
它的寫法可以對應到Python中的字典，每個key，都有相對應的value，且key不能重複。

實作
data = pd.DataFrame({
    "name" : ["Justin", "Joyce"],
    "gender" : ["boy", "girl"],
    "score" : [59, 100]
})
查看資料
因為DataFrame是二維的資料，因此在查看時，分為列(橫)查看及行(直)查看，如果輸入key值查看資料的話，會是行(直)查看，若想要列(橫)查看的話，又分為兩種，一種是利用預設的索引，另一種是利用自定義的索引。而當資料量很多的時候，有時想查看前(後)幾筆，觀察資料就好，則可以使用head()、tail()。

實作
data = pd.DataFrame({
    "fruit" : ["apple", "banana", "peach"],
    "price" : [10, 5, 30]
}, index = ["a", "b", "c"])

print(data["fruit"]) # 查看"fruit"的資料，一整行
print(data.iloc[0]) # 按照預設索引，查看第一列的資料
print(data.loc["b"]) # 按照自定義索引，查看"b"一整列的資料
data = pd.DataFrame({
    "fruit" : ["apple", "banana", "peach", "cherry", "coconut", "grape", "guava"],
    "price" : [10, 5, 30, 20, 50, 25, 15]
})


print(data.head()) # 如果沒有輸入數字，預設是5，即查看前五筆資料
print(data.tail()) # 如果沒有輸入數字，預設是5，即查看最後五筆資料
print(data.head(3)) # 查看前三筆資料
print(data.tail(3)) # 查看後三筆資料
查看資料屬性
與Series一樣，在分析前先查看資料的屬性是很重要的一環，可利用size、shape等查看，或者**info()查看所有的資料的屬性。

實作
data = pd.DataFrame({
    "fruit" : ["apple", "banana", "peach", "cherry", "coconut", "grape", "guava"],
    "price" : [10, 5, 30, 20, 50, 25, 15]
})

print(data.size) # 查看資料的總資料數 
print(data.shape) # 查看資料的大小(即幾乘幾)
print(data.columns) # 查看所有的標題(key)
print(data.info()) # 查看所有資料的屬性，以每個Series顯示
更改資料
當DataFrame已經建立好時，偶爾會需要要修改標題(key)，或是資料的內容，Pandas也有提關相關的函數。

實作
data = pd.DataFrame({
    "name" : ["Justin", "Joyce"],
    "gender" : ["boy", "girl"],
    "score" : [59, 100]
}) # 原始資料

data.columns = ["Name", "Gender", "score"] # 更改標題，用此方法需要重新輸入所有標題，即使只需修改一個
data.rename(columns = {"score":"Score"},inplace = True) # 更改標題，只需更改要改變的值

data.loc[0, "Score"] = 30 # 修改第一列，"score"的值
data.loc[0] = ["Alice", "girl", 90] # 修改第一列全部的值
inplace = True 不會創立新的物件，直接對原資料進行修改
inplace = Flase 更改後給新的物件，原資料不變
預設為Flase

更改索引
這裡也與Series一樣，如果沒有自定義索引的話，會使用預設索引，想要改變索引可在字典後加上index。

實作
data = pd.DataFrame({
    "name" : ["Justin", "Joyce"],
    "gender" : ["boy", "girl"],
    "score" : [59, 100]
}) # 使用預設索引

data = pd.DataFrame({
    "name" : ["Justin", "Joyce"],
    "gender" : ["boy", "girl"],
    "score" : [59, 100]
}, index = ["a", "b"]) # 使用自定義索引
增加新欄位(行)
在設定資料時，或分析資料時，有時會想要新增欄位，讓資料更完整，或更加方便判斷，新增的方式很簡單，因為DataFrame是由一個Series組成，因此想法上就像在增加一個Series，新加入的欄位會由最後面加上，如果要插在中間的話，需要使用insert這個函數。

實作
data = pd.DataFrame({
    "fruit" : ["apple", "banana", "peach"],
    "price" : [10, 5, 30]
})

data["cp"] = [3, 2, 5] # 利用list的方式，增加新的欄位
data["from"] = pd.Series(["Taipei", "Taichung", "Tainan"]) # 利用Series的方式，增加新的欄位

data.insert(2, "amount", 0) # 將"amount"插入在第二行，將初始值令為0
data["amount"] = [100, 300, 200]# 更改"amount"的值
增加新欄位(列)
有時，會要加入新的資料，可以使用append的方式，在最後面加上資料。

實作
data = pd.DataFrame({
    "fruit" : ["apple", "banana", "peach"],
    "price" : [10, 5, 30]
}) # 原始資料

new_data = pd.DataFrame({
    "fruit" : "cherry",
    "price" : 20
},index = [3]) # 新的資料內容

data = data.append(new_data) # 加上新的資料
刪除欄位(行及列)
不需要或錯誤的資料，為了分析方便，可以視情況把它們刪除掉，利用drop函數，可以刪除一行或一列

建議原始的資料不要動到，如果要動到的話，可以先複製一份，更改複製的版本

實作
data = pd.DataFrame({
    "name" : ["Justin", "Joyce"],
    "gender" : ["boy", "girl"],
    "score" : [59, 100]
}) # 原始資料

data.drop(["gender"], axis=1, inplace=True) # 以行為軸，刪除"gender"這一行

data.drop([0], inplace=True) # 以列為軸，利用預設的索引值，刪除第一筆資料
讀取檔案
在實際的操作上，我們會希望能從檔案中讀取資料進行分析，Pandas能讀取csv、html、json等，最常見的是csv檔，因此接下來都用csv檔進行舉例，其他的檔案也是類似的方式。

csv檔連結
實作
data = pd.read_csv("spilt0.csv")  
在讀取檔案時，須將檔案放在與執行檔的同一個資料夾，或是用相對(絕對)的路徑去讀取
如果是在colab上執行的話，可將檔案先放入雲端上讀取

檢查資料
有時資料會有空值、缺失值，我們需要進行資料的前處理，常見的方式有兩種，第一種是刪除，第二種是補齊

函數形式
DataFrame.dropna(axis=0, how='any', thresh=None, subset=None, inplace=False)
DataFrame.fillna(value=None, method=None, axis=None, inplace=False, limit=None, downcast=None, **kwargs)
裏頭的參數有不同的功能，要使用的時候，再去查看資料就行了，這裡就不再多說明。

實作
data.dropna(inplace = True) # 如果有列存在缺失值整列刪除
data.dropna(axis=1, inplace = True) # 如果有行存在缺失值整行刪除

data.fillna(value = "NULL", inplace = True) # 如果有資料有缺失值則填入"NULL"
匯出檔案
在處理完資料，獲得結果後，最後一步就是輸出結果了

實作
data.to_csv("output.csv", index = False) # 將data輸出成csv檔，名稱叫"output.csv"
index = Flase 表示在輸出時，不要保留索引值，預設為True

結語
在這篇文章中，介紹了Pandas的基本用法，在處理大筆的資料上會很有幫助，可以順帶結合前一篇NumPy的用法，結合應用，Pandas的應用範圍很廣，所包含的函數也非常的多，但大範圍的想法都包含在本篇的內容了，想要更深入了解的話，可以去Pandas的官網查看。
