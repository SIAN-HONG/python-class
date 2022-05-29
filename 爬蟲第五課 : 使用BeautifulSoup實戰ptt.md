## 使用BeautifulSoup實戰ptt
### 找出ptt文章title
在ptt省錢版最新一頁https://www.ptt.cc/bbs/Lifeismoney/index.html ，將文章title下載。  
首先移至文章標題滑鼠右鍵點選檢查，會出現右邊HTML程式碼，並且標示出文章標題位置，可以發現在\<div id="main-container"\>包含了全部的文章，
接著進一步尋找文章標題的地方\<div class="title"\>。
先尋找class是'contents_box02'，因為我們在網頁中找到這裡紀錄威力彩結果。
![爬蟲title](https://user-images.githubusercontent.com/27804948/170863305-706b8598-7054-4baa-bd50-82ea4d5f233d.png)
<img width="948" alt="image" src="https://user-images.githubusercontent.com/27804948/170863443-281340fd-b6ff-4cb4-ac20-ff83ab02422f.png">

```Python
import requests, bs4
url = 'https://www.ptt.cc/bbs/Lifeismoney/index.html'
response = requests.get(url)
if response.status_code == requests.codes.ok:
    print("取得網頁內容成功")
    print("網頁內容大小 = ", len(response.text))
else:
    print("取得網頁內容失敗")
    print(response.status_code) 
objSoup = bs4.BeautifulSoup(response.text,'lxml')
dataTag = objSoup.find_all(id='main-container')         # 尋找id是main-container
ptt_title_tag = dataTag[0].find_all('div',class_='title')
text_list = []
for i in ptt_title_tag:
  text_list += [i.text]
text_list
# 取得網頁內容成功
# 網頁內容大小 =  20091
# ['\n[情報] SKII神仙水\n',
#  '\n[情報] 全聯周末 機能水/衛生紙 買一送一\n',
#  '\n[情報] 達美樂中和四維街特價199\n',
#  '\n[情報] 清原 ubereats 芋頭沙西米露買一送一\n',
#  '\n[情報] 寶島眼鏡優惠代碼50+50\n',
#  '\n[情報] Roborock S7+ 22399\n',
#  '\nRe: [討論] 共享機車之比較 goshare wemo\n',
#  '\nRe: [情報] 全國電子Dyson V12吸塵器$18400\n',
#  '\n[情報] 肯德基優惠\n',
#  '\n[討論] momo到價通知(Line)機器人\n',
#  '\n[情報] 7-11 瑞穗鮮乳紅豆雪糕 OP會員兌換\n',
#  '\nRe: [情報] 金車礦沛72入692\n',
#  '\nRe: [情報] 淘寶官方集運 快篩試劑上架\n',
#  '\n[情報] 05/30 汽油降0.1 柴油降0.1！\n',
#  '\nRe: [情報] 統一證券線上開戶 送Line Points 500\n',
#  '\n[公告] 近期版務宣導&違規檢舉區\n',
#  '\n[公告] 省錢板板規 2021年最新版\n',
#  '\n[情報] ==== 五月全台捐血贈品 ==== (5/26更新)\n',
#  '\n[活動]省錢板熱門資訊票選暨心得發文活動(發錢)\n',
#  '\n[公告] 未滿1元集點抽獎資訊集中文\n']
```
### 找出ptt的上頁網站
在ptt省錢版找到"上頁"點滑鼠右鍵，點選檢查，可以觀察到\<a class="btn wide" href="/bbs/Lifeismoney/index3616.html"\>\‹ 上頁\</a\>
<img width="601" alt="image" src="https://user-images.githubusercontent.com/27804948/170863773-123f44b2-815f-46c3-b584-9afd212b6178.png">
