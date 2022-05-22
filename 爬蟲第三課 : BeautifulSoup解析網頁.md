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
在真實爬蟲開始前先以一個簡單的HTML文件開始解析
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
<img src="python.png" width="200">
<section>
   <h1 id="content">Face book</h1>
   <p>id : Sian-Hong Huang</p>
   <img src="facebook.jpg" width="150">
</section>
<section>
   <h1 id="content">Line</h1>
   <p>id : fxp87257</p>
   <img src="line.jpg" width="150">
</section>
</body>
</html>
```
