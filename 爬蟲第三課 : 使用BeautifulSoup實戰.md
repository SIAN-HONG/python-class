## 使用BeautifulSoup實戰
### 找出台灣彩券公司最新一期威力彩開獎結果
在台灣彩券公司https://www.taiwanlottery.com.tw/index_new.aspx ，搜尋最新一期威力採開獎結果，
先尋找class是'contents_box02'，因為我們在網頁中找到這裡紀錄威力彩結果。
<img width="742" alt="image" src="https://user-images.githubusercontent.com/27804948/169689438-456ed8b9-8da2-4ef5-839a-d432053f8ddf.png">

```
pip install beautfulsoup4
```
在Python導入模組時是用以下方式導入:
```Python
# ch5_16.py
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
#### 
