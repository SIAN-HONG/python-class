## 使用BeautifulSoup實戰
### 找出台灣彩券公司最新一期威力彩開獎結果
在台灣彩券公司https://www.taiwanlottery.com.tw/index_new.aspx ，搜尋最新一期威力採開獎結果，
先尋找class是'contents_box02'，因為我們在網頁中找到這裡紀錄威力彩結果。
<img width="742" alt="image" src="https://user-images.githubusercontent.com/27804948/169689438-456ed8b9-8da2-4ef5-839a-d432053f8ddf.png">

```Python
import bs4, requests

url = 'http://www.taiwanlottery.com.tw'
html = requests.get(url)
print("網頁下載中 ...")
html.raise_for_status()                             # 驗證網頁是否下載成功                      
print("網頁下載完成")

objSoup = bs4.BeautifulSoup(html.text, 'lxml')      # 建立BeautifulSoup物件

dataTag = objSoup.select('.contents_box02')         # 尋找class是contents_box02
print("串列長度", len(dataTag))
for i in range(len(dataTag)):                       # 列出含contents_box02的串列                 
    print(dataTag[i])
        
# 找尋開出順序與大小順序的球
balls = dataTag[0].find_all('div', {'class':'ball_tx ball_green'})
print("開出順序 : ", end='')
for i in range(6):                                  # 前6球是開出順序
    print(balls[i].text, end='   ')

print("\n大小順序 : ", end='')
for i in range(6,len(balls)):                       # 第7球以後是大小順序
    print(balls[i].text, end='   ')

# 找出第二區的紅球                   
redball = dataTag[0].find_all('div', {'class':'ball_red'})
print("\n第二區   :", redball[0].text)


```
### 列出Yahoo焦點新聞和超連結

滑鼠移至第一條焦點新聞，按滑鼠右鍵，點集檢查，可以進入chrome開發工具模式，我們可以找出焦點新聞是在\<div id="panel0-content"...\>，同時可以在\<a\>標籤忠看到超連結herf屬性和\<p\>內容。
<img width="839" alt="image" src="https://user-images.githubusercontent.com/27804948/169689871-e01e1ec0-a954-43e7-8637-b0b0e76e7f69.png">

```Python
import requests
import bs4

response = requests.get('https://tw.yahoo.com/')
objSoup = bs4.BeautifulSoup(response.text, 'lxml')

dataTag = objSoup.select('#panel0-content')         # 尋找id是panel0-content
print("串列長度", len(dataTag))                     # 得到串列長度
  
headline_news = dataTag[0].find_all('a')            # 焦點新聞是在標籤<a>
for h in headline_news:
    print("焦點新聞 : " + h.text)                   # 列出焦點新聞標題
    print("新聞網址 : " + h.get('href'))            # 列出新聞網址
# 串列長度 1
# 焦點新聞 : 醫預測：台走向紐西蘭模式
# 新聞網址 : https://tw.news.yahoo.com/%E5%96%AE%E6%97%A5%E4%B8%8D%E5%88%B010%E8%90%AC-%E8%B5%B0%E5%90%91%E7%B4%90%E8%A5%BF%E8%98%AD%E6%A8%A1%E5%BC%8F-%E5%B0%88%E5%AE%B6-%E5%89%A910%E5%A4%A9%E5%88%B0%E9%AB%98%E5%B3%B0-053020598.html
# 焦點新聞 : 蔡親自致電新北醫：拜託協調
# 新聞網址 : # https://tw.news.yahoo.com/%E3%80%8C%E6%B0%91%E7%9C%BE%E6%9C%9F%E5%BE%85%E7%9A%84%E6%98%AF%E6%94%9C%E6%89%8B%E8%A7%A3%E6%B1%BA%E5%95%8F%E9%A1%8C%E3%80%8D-%E8%94%A1%E7%B8%BD%E7%B5%B1%E8%A6%AA%E8%87%AA%E8%87%B4%E9%9B%BB%E9%9B%99%E5%8C%97%E5%9F%BA%E5%B1%A4%E9%86%AB%E5%B8%AB-%E7%9B%BC%E4%B8%AD%E5%A4%AE%E8%88%87%E5%9C%B0%E6%96%B9%E5%90%88%E4%BD%9C%E9%98%B2%E7%96%AB-035319169.html
# 焦點新聞 : 快篩陽=確診 擬下周全民適用
# 新聞網址 : # https://tw.news.yahoo.com/%E9%99%B3%E6%99%82%E4%B8%AD%EF%BC%9A%E5%BF%AB%E7%AF%A9%E9%99%BD%E6%AF%94%E7%85%A7%E7%A2%BA%E8%A8%BA%E7%B5%A6%E8%97%A5-%E6%9C%80%E5%BF%AB%E4%B8%8B%E5%91%A8%E9%96%8B%E6%94%BE%E5%85%A8%E6%B0%91%E9%81%A9%E7%94%A8-064751426.html
# 焦點新聞 : 用郭董口水機篩陽 柯揭症狀
# 新聞網址 : https://tw.news.yahoo.com/%E5%BF%AB%E7%AF%A9%E9%99%BD%E6%80%A7-%E6%9F%AF%E6%96%87%E5%93%B2%E5%96%89%E5%9A%A8%E5%8D%A1%E5%8D%A1%E5%A0%B1%E5%B9%B3%E5%AE%89-%E9%99%A4%E5%92%B3%E5%97%BD%E6%B2%92%E5%A4%AA%E6%98%8E%E9%A1%AF%E7%97%87%E7%8B%80-064330241.html
# 焦點新聞 : 萬人進香擠爆⋯醫：平行時空
# 新聞網址 : # https://tw.news.yahoo.com/%E9%80%B2%E9%A6%99%E6%B4%BB%E5%8B%95%E8%90%AC%E4%BA%BA%E6%93%A0%E7%88%86%EF%BC%81%E6%80%A5%E8%A8%BA%E9%86%AB%E6%AD%8E%EF%BC%9A%E9%86%AB%E8%AD%B7%E7%9C%9F%E7%9A%84%E9%9C%80%E8%A6%81%E5%96%98%E6%81%AF-054316859.html
# 焦點新聞 : 本土增79441例略降 添53死
# 新聞網址 : https://tw.news.yahoo.com/%E7%9B%B4%E6%92%AD%EF%BC%8Fcovid-19-%E6%9C%AC%E5%9C%9F%E6%9C%80%E6%96%B0%E7%96%AB%E6%83%85-%E9%99%B3%E6%99%82%E4%B8%AD%E4%B8%8B%E5%8D%88%E8%AA%AA%E6%98%8E-024157610.html
# 焦點新聞 : 大戰3局擊敗陳雨菲 小戴奪冠
# 新聞網址 : # https://tw.news.yahoo.com/%E6%88%B4%E8%B3%87%E7%A9%8E%E4%B8%89%E5%B1%80%E5%A4%A7%E6%88%B0%E6%93%8A%E6%95%97%E9%99%B3%E9%9B%A8%E8%8F%B2-%E6%B3%B0%E5%9C%8B%E7%BE%BD%E7%90%83%E8%B3%BD%E5%A5%AA%E5%86%A0-074245598.html
# 焦點新聞 : 確診連3降 中央：疫處高原期
# 新聞網址 : https://tw.news.yahoo.com/%E7%A2%BA%E8%A8%BA%E6%95%B8%E9%80%A3%E4%B8%89%E5%A4%A9%E4%B8%8B%E9%99%8D-%E9%99%B3%E6%99%82%E4%B8%AD%E6%9B%9D-%E7%96%AB%E6%83%85%E8%99%95%E9%AB%98%E5%8E%9F%E6%9C%9F-070010486.html
```

