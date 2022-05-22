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
以PPT省錢版為例https://www.ptt.cc/bbs/Lifeismoney/index.html，首先使用requests.get語法抓取URL中的HTML文件，bs4.BeautifulSoup第一個參數放入解析目標HTML文件，第二個參數目的是註明解析HTML文件的方法通常有"lxml"速度快相容性高需額外安裝lxml、'html.parser'較老舊方法相容性較差、'html5lib'速度慢但解析能力強需額外安裝html5lib，使用下列語法建立:
```Python
import requests, bs4

response = requests.get('https://www.ptt.cc/bbs/Lifeismoney/index.html')
objSoup = bs4.BeautifulSoup(response.text,'lxml')
print("列印BeautifulSoup物件資料型態 ", type(objSoup))
# 列印BeautifulSoup物件資料型態  <class 'bs4.BeautifulSoup'>
```

