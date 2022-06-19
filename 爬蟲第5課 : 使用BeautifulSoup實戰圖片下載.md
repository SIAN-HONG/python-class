## 使用BeautifulSoup實戰圖片下載
由上次課堂上說明到<img>標籤對網路爬蟲設計很重要，因為可以由此獲得網頁的圖檔資訊，接下來進一步將圖片下載下來。  
#### 創建資料夾
首先先教學如何用python創建資料夾，可以使用os套件，其中os.path.exists(destDir)表示在程式執行的路徑底下尋找資料夾destDir，
回傳True表示已存在，False表示不存在，如果資料夾不存在，我們進一步使用os.mkdir(destDir)創建新的資料夾destDir。
```Python
# 設定未來儲存圖片的資料夾
destDir = 'png2'                                 
if os.path.exists(destDir) == False:
    # 建立資料夾供未來儲存圖片
    os.mkdir(destDir)                               
```

#### 儲存圖片
在網路上的圖片都有網址，在欲下載的圖片中點右鍵，在點選"在分頁中開啟圖片"，如下圖  
<img width="314" alt="image" src="https://user-images.githubusercontent.com/27804948/172036074-ecbf753d-668a-4432-8350-f60226d660a1.png">  
<img width="580" alt="image" src="https://user-images.githubusercontent.com/27804948/172036101-d13fd291-6af1-44b5-b0f9-849a8d8cb980.png">  
可以request.get(PictureUrl)，其中PictureUrl為圖片網址，此時picture已經是個照片的資料型態，with open(fileDir, 'wb') as file可將檔案fileDir打開，
進一步file.write(picture.content)將剛才的圖檔寫入。  
```Python
picture = requests.get(PictureUrl)
# 開啟資料夾及命名圖片檔
with open(fileDir, 'wb') as file:  
    # 寫入檔案
    file.write(picture.content)                                                    
```
### Yahoo網站圖片下載
在Yahoo網站(https://tw.yahoo.com/)中下載圖片，可以發現在<img>裡面的src表示圖檔的網站。
```Python
# ch5_14.py
import bs4, requests, os

url = 'https://tw.yahoo.com/'                    
html = requests.get(url)
print("網頁下載中 ...")
# 驗證網頁是否下載成功
html.raise_for_status()                                                   
print("網頁下載完成")
# 搜尋所有圖片檔案
imgTag = objSoup.select('img')                      
imgTag
# 網頁下載中 ...
# 網頁下載完成
# [<img alt="Yahoo奇摩" class="H(40px)!--sm1024 Mt(4px)!--sm1024 W(90px)!--sm1024 Mt(8px)" height="55px" src="https://s.yimg.com/cv/apiv2/twfrontpage/logo/Yahoo-TW-desktop-FP@2x.png" title="Yahoo奇摩" width="125px"/>,
#  <img alt="" aria-hidden="true" class="D(n)" src="https://s.yimg.com/cv/apiv2/twfrontpage/locker/sp-dialoguebox-176x312-20220530.jpg"/>,
#  <img alt="登記領購物金" class="B(0)" height="432" src="https://s.yimg.com/cv/apiv2/twfrontpage/appbanner/sp-banner-96x648-20220530.jpg" width="64"/>,
#  <img alt="" class="H(37px) W(70px) Pos(a) End(4px) Bdrs(8px) T(50%) TranslateY(-50%)" src="https://s.yimg.com/uu/api/res/1.2/A1WXmU2WOAEB9SOMTaMPVQ--~B/Zmk9ZmlsbDtoPTEyODtweW9mZj0wO3c9MjI4O2FwcGlkPXl0YWNoeW9u/https://s.yimg.com/os/creatr-uploaded-images/2022-05/a6346b50-e171-11ec-b6db-b4dc43b9563e.cf.webp"/>,
#  <img alt="陳時中：預估10號疫情反轉" class="Cur(p) H(100%)" src="https://s.yimg.com/uu/api/res/1.2/ynhVAFzvgQX54SiMs0n9bw--~B/Zmk9ZmlsbDtoPTM4ODtweW9mZj0wO3c9NzIwO2FwcGlkPXl0YWNoeW9u/https://s.yimg.com/os/creatr-uploaded-images/2022-05/d7a2e840-e47f-11ec-9fbd-82fba7518450"/>,
#  <img alt="狂打工拚讀大學 26歲女上太空" class="Cur(p) H(100%)" src="https://s.yimg.com/uu/api/res/1.2/EDJEND_sK.tHitopycIIPQ--~B/Zmk9ZmlsbDtoPTM4ODtweW9mZj0wO3c9NzIwO2FwcGlkPXl0YWNoeW9u/https://s.yimg.com/os/creatr-uploaded-images/2022-05/904cf740-e481-11ec-9bfc-fb2a7dfd2c21"/>,...]
```
但是再進一步觀察發現第5筆資料 
\<img alt="陳時中：預估10號疫情反轉" class="Cur(p) H(100%)" src="https://s.yimg.com/uu/api/res/1.2/ynhVAFzvgQX54SiMs0n9bw--~B/Zmk9ZmlsbDtoPTM4ODtweW9mZj0wO3c9NzIwO2FwcGlkPXl0YWNoeW9u/https://s.yimg.com/os/creatr-uploaded-images/2022-05/d7a2e840-e47f-11ec-9fbd-82fba7518450"/>
裡面的src網址並沒有圖片的附檔名(.jpg,.png...)，所以在這邊儲存圖片的時候須注意而外給副檔名再存儲。

```Python
# ch5_14.py
import bs4, requests, os

url = 'https://tw.yahoo.com/'                    # Yahoo網頁
html = requests.get(url)
print("網頁下載中 ...")
html.raise_for_status()                             # 驗證網頁是否下載成功                      
print("網頁下載完成")

destDir = 'download'                                 # 設定未來儲存圖片的資料夾
if os.path.exists(destDir) == False:
    os.mkdir(destDir)                               # 建立資料夾供未來儲存圖片

objSoup = bs4.BeautifulSoup(html.text, 'lxml')      # 建立BeautifulSoup物件

imgTag = objSoup.select('img')                      # 搜尋所有圖片檔案
print("搜尋到的圖片數量 = ", len(imgTag))           # 列出搜尋到的圖片數量
if len(imgTag) > 0:                                 # 如果有找到圖片則執行下載與儲存
    for i in range(len(imgTag)-1):                    # 迴圈下載圖片與儲存
        finUrl = imgTag[i].get('src')               # 取得圖片的路徑               # 取得圖片在Internet上的路徑       
        try:
            picture = requests.get(finUrl)          # 下載圖片
            picture.raise_for_status()              # 驗證圖片是否下載成功
            if finUrl.find('.png')==-1 or finUrl.find('.jpg'):
              finUrl += '.png'
            print("%s 圖片下載成功" % os.path.basename(finUrl))
            with open(os.path.join(destDir, os.path.basename(finUrl)), 'wb') as file:  # 開啟資料夾及命名圖片檔
                file.write(picture.content)                       # 關閉檔案
        except Exception as err:
            print(f"圖片下載失敗: {err}")
```
