## 使用BeautifulSoup模組
本章節介紹BeautifulSoup模組解析HTML文件。目前使用第四版beautifulsoup4，用以下方式安裝:
```
pip install beautfulsoup4
```
在Python導入模組時是用以下方式導入:
```Python
import bs4
```
#### 建立BeautifulSoup物件
以PPT省錢版為例https://www.ptt.cc/bbs/Lifeismoney/index.html ，首先使用requests.get語法抓取URL中的HTML文件，bs4.BeautifulSoup第一個參數放入解析目標HTML文件，第二個參數目的是註明解析HTML文件的方法通常有"lxml"速度快相容性高需額外安裝lxml、'html.parser'較老舊方法相容性較差、'html5lib'速度慢但解析能力強需額外安裝html5lib，使用下列語法建立:
```Python
import requests, bs4

response = requests.get('https://www.ptt.cc/bbs/Lifeismoney/index.html')
objSoup = bs4.BeautifulSoup(response.text,'lxml')
print("列印BeautifulSoup物件資料型態 ", type(objSoup))
# 列印BeautifulSoup物件資料型態  <class 'bs4.BeautifulSoup'>
```
#### 基本HTML文件解析
在真實爬蟲開始前先以一個簡單的HTML文件開始解析，同學可以存檔在本地端開啟看看，並且將HTML文件存檔成'myhtml.html'。
```HTML
<!doctype html>
<html>
<head>
   <meta charset="utf-8">
   <title>黃献竤介紹</title>
   <style>
      h1#author { width:400px; height:50px; text-align:center;
	     background:linear-gradient(to right,yellow,rgb(26, 0, 128));
      }
	  h1#content { width:400px; height:50px;
		 background:linear-gradient(to right,yellow,rgb(26, 0, 128)); 
      }
      
   </style>
</head>
<body>
<h1 id="author">黃献竤</h1>
<p>主要我專長是專攻數據分析， 107年從中山大學研究所數學系畢業已修過教育學程，目前在工研院軟體工程師年資5年，
   做過的大型軟體開發以及分析有鉅亨網、中國信託、富邦人壽、台灣大哥大、...還有很多小案子，如果是以實際數據分析的經驗是非常充足的，
   主要還是數據分析上面</p>
<section>
   <h1 id="content">Face book</h1>
   <p>id : Sian-Hong Huang</p>
</section>
<section>
   <h1 id="content">Line</h1>
   <p>id : fxp87257</p>
</section>
</body>
</html>
```
<img width="762" alt="image" src="https://user-images.githubusercontent.com/27804948/169684737-e3de602f-fd22-4a64-be47-076048098cdf.png">

如果將以上的HTML文件用相關性節點表示:  

<img width="375" alt="image" src="https://user-images.githubusercontent.com/27804948/169684846-3a72612e-9acf-40a9-8442-6419c5ab4e43.png">

#### 網頁屬性
##### title屬性
Beautiful物件的title屬性可以傳回網頁標題的\<title\>標籤內容

```Python
import bs4

response = open('myhtml.html', encoding='utf-8')
objSoup = bs4.BeautifulSoup(response, 'lxml')
print("物件類型  = ", type(objSoup.title))
print("列印title = ", objSoup.title)
# 物件類型  =  <class 'bs4.element.Tag'>
# 列印title =  <title>黃献竤介紹</title>
```
##### text屬性
可以看到前面title還有不需要的文字\<title\>...\</title\>，此時我們可以使用text屬性獲得標籤的內容。
```Python
import bs4

response = open('myhtml.html', encoding='utf-8')
objSoup = bs4.BeautifulSoup(response, 'lxml')
print("title.text內容 = ", objSoup.title.text)
print("title.string內容 = ", objSoup.title.string)
# title.text內容 =  黃献竤介紹
# title.string內容 =  黃献竤介紹
```
<img width="476" alt="image" src="https://user-images.githubusercontent.com/27804948/169685439-7b80fe6c-a3af-48f1-b7f4-33e529acdfb2.png">
### 尋找符合的標籤
#### 尋找第一個標籤find()
這個函數可以找到第一個符合的標籤，例如find('h1')是要找第一個h1的標籤。如果有找到就回傳標籤字串，如果沒有回傳None。
```Python
import bs4

response = open('myhtml.html', encoding='utf-8')
objSoup = bs4.BeautifulSoup(response, 'lxml')
objTag = objSoup.find('h1')
print("資料型態       = ", type(objTag))
print("列印Tag        = ", objTag)
print("屬性內容   = ", objTag.text)
# 資料型態       =  <class 'bs4.element.Tag'>
# 列印Tag        =  <h1 id="author">黃献竤</h1>
# 屬性內容   =  黃献竤
```
##### 尋找全部標籤find_all()
這個函數可以找到全部符合的標籤，例如find_all('h1')是要找全部h1的標籤。如果有找到就回傳標籤串列(list)，如果沒有回傳空串列。
```Python
import bs4

response = open('myhtml.html', encoding='utf-8')
objSoup = bs4.BeautifulSoup(response, 'lxml')
objTag = objSoup.find_all('h1')
print("資料型態    = ", type(objTag))     # 列印資料型態
print("列印Tag串列 = ", objTag)           # 列印串列
print("以下是列印串列元素 : ")
for data in objTag:                       # 列印串列元素內容
    print(data.text)

# 資料型態    =  <class 'bs4.element.ResultSet'>
# 列印Tag串列 =  [<h1 id="author">黃献竤</h1>, <h1 id="content">Face book</h1>, <h1 id="content">Line</h1>]
# 以下是列印串列元素 : 
# 黃献竤
# Face book
# Line
```
<img width="477" alt="image" src="https://user-images.githubusercontent.com/27804948/169685785-e72128e8-5779-46df-9105-a77f8cc51638.png">

此外find_all()基本上是使用迴圈的方式尋找所有符合的標籤節點，也可以使用下列參數限制找尋的點數量:  
```
limit = n         # 限制尋找最多n個標籤節點
recursive = Fasle # 不使用遞迴搜尋，僅尋找次一層的子節點
```

```Python
import bs4
response = open('myhtml.html', encoding='utf-8')
objSoup = bs4.BeautifulSoup(response, 'lxml')
objTag = objSoup.find_all('h1', limit=2)
for data in objTag:                       # 列印串列元素內容
    print(data.text)
# 黃献竤
# Face book
```
### HTML屬性搜尋
我們可以依據HTML標籤屬性執行搜尋，可參考實例:
```Python
import bs4

response = open('myhtml.html', encoding='utf-8')
objSoup = bs4.BeautifulSoup(response, 'lxml')
objTag = objSoup.find(id='author')
print(objTag)
print(objTag.text)
# <h1 id="author">黃献竤</h1>
# 黃献竤
```
```Python
import bs4

response = open('myhtml.html', encoding='utf-8')
objSoup = bs4.BeautifulSoup(response, 'lxml')
objTag = objSoup.find_all(id='content')
for tag in objTag:
    print(tag)
    print(tag.text)
# <h1 id="content">Face book</h1>
# Face book
# <h1 id="content">Line</h1>
# Line
```
### select()

objSoup.select ('p')：找尋所有\<p\>標籤的元素。  
objSoup.select ('img')：找尋所有\<img\>標籤的元素。  
objSoup.select ('.happy')：找尋所有CSS class屬性為happy的元素。  
objSoup.select ('#author')：找尋所有CSS id屬性為author的元素。  
objSoup.select ('p #author')：找尋所有\<p\>且id屬性為author的元素。  
objSoup.select ('p .happy')：找尋所有\<p\>且class屬性為happy的元素。  
objSoup.select ('div strong')：找尋所有在\<section\>元素內的\<strong\>元素。  
objSoup.select ('div > strong')：所有在\<section\>內的\<strong\>元素，中間沒有其他元素。  
objSoup.select ('input[name]')：找尋所有\<input\>標籤且有name屬性的元素。  




