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
在Yahoo網站(https://tw.yahoo.com/)中下載圖片，可以發現在<img>裡面的src表示圖檔的網站，但是再進一步觀察發現第5筆資料 
<img alt="陳時中：預估10號疫情反轉" class="Cur(p) H(100%)" src="https://s.yimg.com/uu/api/res/1.2/ynhVAFzvgQX54SiMs0n9bw--~B/Zmk9ZmlsbDtoPTM4ODtweW9mZj0wO3c9NzIwO2FwcGlkPXl0YWNoeW9u/https://s.yimg.com/os/creatr-uploaded-images/2022-05/d7a2e840-e47f-11ec-9fbd-82fba7518450"/>
裡面的src網址並沒有圖片的附檔名(.jpg,.png...)，所以在這邊儲存圖片的時候須注意而外給副檔名再存儲。
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
#  <img alt="狂打工拚讀大學 26歲女上太空" class="Cur(p) H(100%)" src="https://s.yimg.com/uu/api/res/1.2/EDJEND_sK.tHitopycIIPQ--~B/Zmk9ZmlsbDtoPTM4ODtweW9mZj0wO3c9NzIwO2FwcGlkPXl0YWNoeW9u/https://s.yimg.com/os/creatr-uploaded-images/2022-05/904cf740-e481-11ec-9bfc-fb2a7dfd2c21"/>,
#  <img alt="「各縣市跌破萬例」中央剖析" class="Cur(p) H(100%)" src="https://s.yimg.com/uu/api/res/1.2/mPFuK9d0QLfHxY63qCRqjw--~B/Zmk9ZmlsbDtoPTM4ODtweW9mZj0wO3c9NzIwO2FwcGlkPXl0YWNoeW9u/https://s.yimg.com/os/creatr-uploaded-images/2022-05/2e1e9500-e3d8-11ec-bedf-da3f78dbc8bd"/>,
#  <img alt="大谷罕見強烈抗議 重播還清白" class="Cur(p) H(100%)" src="https://s.yimg.com/uu/api/res/1.2/70lrcSt7XQtO.UMPCbCnbg--~B/Zmk9ZmlsbDtoPTM4ODtweW9mZj0wO3c9NzIwO2FwcGlkPXl0YWNoeW9u/https://s.yimg.com/os/creatr-uploaded-images/2022-05/292b6bd0-e479-11ec-b757-12a3f07ef70e"/>,
#  <img alt="伍鐸關門成功 龍隊1分之差獲勝" class="Cur(p) H(100%)" src="https://s.yimg.com/uu/api/res/1.2/DcjDkhFfgnECbrEdJ8J2EQ--~B/Zmk9ZmlsbDtoPTM4ODtweW9mZj0wO3c9NzIwO2FwcGlkPXl0YWNoeW9u/https://s.yimg.com/os/creatr-uploaded-images/2022-06/04a38500-e473-11ec-bfd7-172d74643e77"/>,
#  <img alt="飛球直達手套 林承飛輕鬆背向接殺" class="Cur(p) H(100%)" src="https://s.yimg.com/uu/api/res/1.2/Gjwy_eAHSImSJ0FN1rNfwg--~B/Zmk9ZmlsbDtoPTM4ODtweW9mZj0wO3c9NzIwO2FwcGlkPXl0YWNoeW9u/https://s.yimg.com/os/creatr-uploaded-images/2022-06/2610f420-e473-11ec-aa7f-dff50581a771"/>,
#  <img alt="遭家暴離婚 台南Josh鬆一口氣" class="Cur(p) H(100%)" src="https://s.yimg.com/uu/api/res/1.2/7fp7zZ8nAZj70A3Lb8dR1w--~B/Zmk9ZmlsbDtoPTM4ODtweW9mZj0wO3c9NzIwO2FwcGlkPXl0YWNoeW9u/https://s.yimg.com/os/creatr-uploaded-images/2022-06/57358750-e482-11ec-affb-a4b2f861f657"/>,
#  <img alt="小6歲尪身分被肉搜 張娜拉發聲" class="Cur(p) H(100%)" src="https://s.yimg.com/uu/api/res/1.2/e47_JrNe64YPeHi9SjiMEg--~B/Zmk9ZmlsbDtoPTM4ODtweW9mZj0wO3c9NzIwO2FwcGlkPXl0YWNoeW9u/https://s.yimg.com/os/creatr-uploaded-images/2022-06/958d7350-e473-11ec-bfff-14b132eb329d"/>,
#  <img alt="甜曬楊丞琳剪影 尪連8年準時祝賀" class="Cur(p) H(100%)" src="https://s.yimg.com/uu/api/res/1.2/pmhdBiXYe8N3zj4PFWP60w--~B/Zmk9ZmlsbDtoPTM4ODtweW9mZj0wO3c9NzIwO2FwcGlkPXl0YWNoeW9u/https://s.yimg.com/os/creatr-uploaded-images/2022-06/b8946e30-e473-11ec-8fe4-5ec124eaa427"/>,
#  <img alt="年薪百萬 美女船員曝海上生活" class="Cur(p) H(100%)" src="https://s.yimg.com/uu/api/res/1.2/lhpIz7Rr17CIW3NbvTZ1bA--~B/Zmk9ZmlsbDtoPTM4ODtweW9mZj0wO3c9NzIwO2FwcGlkPXl0YWNoeW9u/https://s.yimg.com/os/creatr-uploaded-images/2022-05/a40100e0-e0be-11ec-b65d-e5647abf6646"/>,
#  <img alt="台小吃「入侵」歐市集 花千元噴淚" class="Cur(p) H(100%)" src="https://s.yimg.com/uu/api/res/1.2/8xXEjY3v3yQMsWmQUojdSw--~B/Zmk9ZmlsbDtoPTM4ODtweW9mZj0wO3c9NzIwO2FwcGlkPXl0YWNoeW9u/https://s.yimg.com/os/creatr-uploaded-images/2022-05/b5b155d0-da8b-11ec-aee3-3ebab978a7a0"/>,
#  <img alt="釣上「半個人」巨尾 細看秒放生" class="Cur(p) H(100%)" src="https://s.yimg.com/uu/api/res/1.2/Mg7qGIDidczYl0pCwnumiA--~B/Zmk9ZmlsbDtoPTM4ODtweW9mZj0wO3c9NzIwO2FwcGlkPXl0YWNoeW9u/https://s.yimg.com/os/creatr-uploaded-images/2022-05/2db768e0-ddb0-11ec-be9f-42a69fcdc55a"/>,
#  <img alt="比不運動更傷身 死亡率增30％" class="Cur(p) H(100%)" src="https://s.yimg.com/uu/api/res/1.2/Zp2fkafHeEfywAQY21yw.A--~B/Zmk9ZmlsbDtoPTM4ODtweW9mZj0wO3c9NzIwO2FwcGlkPXl0YWNoeW9u/https://s.yimg.com/os/creatr-uploaded-images/2022-05/728bcc00-e007-11ec-9275-f8b5c1731243"/>,
#  <img alt="娶到真的賺到 4星座幫夫旺家" class="Cur(p) H(100%)" src="https://s.yimg.com/uu/api/res/1.2/TUXhARU0NXkrmxhRVjhgDg--~B/Zmk9ZmlsbDtoPTM4ODtweW9mZj0wO3c9NzIwO2FwcGlkPXl0YWNoeW9u/https://s.yimg.com/os/creatr-uploaded-images/2022-03/f39ed2b0-9de0-11ec-abf9-8321e7f867b8"/>,
#  <img alt="超商門口停整排賓士 藏驚人巧合" class="Cur(p) H(100%)" src="https://s.yimg.com/uu/api/res/1.2/byt3kRAVRdNeO15.aTsFKw--~B/Zmk9ZmlsbDtoPTM4ODtweW9mZj0wO3c9NzIwO2FwcGlkPXl0YWNoeW9u/https://s.yimg.com/os/creatr-uploaded-images/2022-05/c0f07fd0-d738-11ec-addd-ff3835a1083e"/>,
#  <img alt="纏鬥半小時捕巨尾 身價竟12億" class="Cur(p) H(100%)" src="https://s.yimg.com/uu/api/res/1.2/XXecQl3TQ3q3CKpCpzSoMQ--~B/Zmk9ZmlsbDtoPTM4ODtweW9mZj0wO3c9NzIwO2FwcGlkPXl0YWNoeW9u/https://s.yimg.com/os/creatr-uploaded-images/2022-05/cb951650-dd8f-11ec-9577-b67ec6a7d62d"/>,
#  <img alt="染疫可使群體免疫嗎 台大教授解答" class="Cur(p) H(100%)" src="https://s.yimg.com/uu/api/res/1.2/az.Xfs.AB0mJvE_Rd.4oNA--~B/Zmk9ZmlsbDtoPTM4ODtweW9mZj0wO3c9NzIwO2FwcGlkPXl0YWNoeW9u/https://s.yimg.com/os/creatr-uploaded-images/2022-05/79eb3290-dbea-11ec-bf51-42b6480f4f5b"/>,
#  <img alt="間諜家家酒：祕密警察探訪 險穿幫" class="Cur(p) H(100%)" src="https://s.yimg.com/uu/api/res/1.2/.qcXXAOx3GRR9MvyFqCTug--~B/Zmk9ZmlsbDtoPTM4ODtweW9mZj0wO3c9NzIwO2FwcGlkPXl0YWNoeW9u/https://s.yimg.com/os/creatr-uploaded-images/2022-05/c8faa830-e0b7-11ec-bf9a-3de6b39ead7b"/>,
#  <img class="H(30px) W(a)" src="https://s.yimg.com/cv/apiv2/twfrontpage/logo/TW_desktop_tv_home.png"/>,
#  <img alt="中美航母軍演頻頻 印太地區美中台角力持續" class="W(106px) H(60px)" src="https://s.yimg.com/bt/api/res/1.2/AIpboTS.w2xH4yK9m_sPog--~B/Zmk9ZmlsbDtweW9mZj01MDtweG9mZj01MDt3PTEwNjtoPTYwO2FwcGlkPXl0YWNoeW9u/https://s.yimg.com/uu/api/res/1.2/OcQU34a34Tf5uuUjQD8ciw--~B/Zmk9ZmlsbDtweW9mZj0wO3c9NjQwO2g9MzYwO3NtPTE7YXBwaWQ9eXRhY2h5b24-/https://s.yimg.com/os/creatr-uploaded-images/2022-06/e1c04840-e2d1-11ec-abde-df0e7cbdef5f"/>,
#  <img alt="葛斯齊混進宋仲基 宋慧喬婚禮 被保鑣暴打針孔塞肛門過程曝光" class="W(106px) H(60px)" src="https://s.yimg.com/uu/api/res/1.2/r_SiafsYDydhF61JdFWnoA--~B/Zmk9ZmlsbDtweW9mZj0wO3c9MTA2O2g9NjA7c209MTthcHBpZD15dGFjaHlvbg--/https://media.zenfs.com/en/__rss_338/52a1ad2284c00787e96be71d8a1b2749"/>,
#  <img alt="台中第二市場美食攻略 銅板價火雞肉飯爆漿大滿足" class="W(106px) H(60px)" src="https://s.yimg.com/uu/api/res/1.2/rySwqd_o5cbN7hpx.SUnSw--~B/Zmk9ZmlsbDtweW9mZj0wO3c9MTA2O2g9NjA7c209MTthcHBpZD15dGFjaHlvbg--/https://s.yimg.com/os/creatr-uploaded-images/2022-05/c0c26f40-df6d-11ec-b5dd-dcf642c7d58b"/>,
#  <img alt="自創暗黑料理-新口味爆米花！沒有極限！同事評價兩極化？！" class="W(106px) H(60px)" src="https://s1.yimg.com/uu/api/res/1.2/bWK5.93Zz3U1LEilXGYf3Q--~B/Zmk9ZmlsbDtweW9mZj0wO3c9MTA2O2g9NjA7c209MTthcHBpZD15dGFjaHlvbg--/https://s.yimg.com/os/creatr-uploaded-images/2022-05/47354bb0-d140-11ec-bf7b-10a77c4c6146"/>,
#  <img alt="全網獨創脆皮五花肉，泡泡脆皮才是王道，化腐朽為神奇，越醜的五花肉，煎出來越好吃 (crunchy pork belly)" class="W(106px) H(60px)" src="https://s1.yimg.com/uu/api/res/1.2/5_nduE1M7Hxz7ArtW_Mr9A--~B/Zmk9ZmlsbDtweW9mZj0wO3c9MTA2O2g9NjA7c209MTthcHBpZD15dGFjaHlvbg--/https://s.yimg.com/os/creatr-uploaded-images/2022-05/45131280-d141-11ec-b36e-07a00f6bd70e"/>,
#  <img alt="🇯🇵日檢JLPT成績發表📖上榜落榜？接下來計畫？| 可凡kofan✩" class="W(106px) H(60px)" src="https://s1.yimg.com/uu/api/res/1.2/V4oJXQGPIYpJoq6xhgcz8w--~B/Zmk9ZmlsbDtweW9mZj0wO3c9MTA2O2g9NjA7c209MTthcHBpZD15dGFjaHlvbg--/https://s.yimg.com/os/creatr-uploaded-images/2022-05/e9872d50-d7dc-11ec-af7f-c76de6575fdb"/>,
#  <img alt="2022全新的澀谷街頭✨澀谷橫丁、宮下公園、PARCO、澀谷SCRAMBLE SQUARE展望台、PLAZA｜日本東京一日景點攻略・日本旅遊4K VLOG" class="W(106px) H(60px)" src="https://s.yimg.com/uu/api/res/1.2/AfpjxepC4OeXiHkTN8NNpw--~B/Zmk9ZmlsbDtweW9mZj0wO3c9MTA2O2g9NjA7c209MTthcHBpZD15dGFjaHlvbg--/https://s.yimg.com/os/creatr-uploaded-images/2022-05/ac4c1c50-dbb7-11ec-baff-863494f0126f"/>,
#  <img alt="【芒種補氣提神：海鮮鳳梨炒飯】帥哥主廚陳德烈 x 美女中醫彭溫雅｜Yahoo TV 節氣餐桌" class="W(106px) H(60px)" src="https://s1.yimg.com/uu/api/res/1.2/t1pjjXoHj39omT1PnWRBDw--~B/Zmk9ZmlsbDtweW9mZj0wO3c9MTA2O2g9NjA7c209MTthcHBpZD15dGFjaHlvbg--/https://s.yimg.com/os/creatr-uploaded-images/2022-06/82030260-e13e-11ec-bfdb-557a19d0c1b2"/>,
#  <img alt="精華｜害怕陷入愛情？這些星座對單身上癮！" class="W(106px) H(60px)" src="https://s.yimg.com/uu/api/res/1.2/piWZzuSEmKcy29eugzPXnQ--~B/Zmk9ZmlsbDtweW9mZj0wO3c9MTA2O2g9NjA7c209MTthcHBpZD15dGFjaHlvbg--/https://s.yimg.com/os/creatr-uploaded-images/2022-06/6895a800-e242-11ec-91f7-eef195bd6455"/>,
#  <img alt="預告｜初次約會「穿黑色」是大忌！？第一眼就用服裝展示自我：形象大改造開始！" class="W(106px) H(60px)" src="https://s1.yimg.com/uu/api/res/1.2/gYWbqzDajtqVn3nhuBAwFg--~B/Zmk9ZmlsbDtweW9mZj0wO3c9MTA2O2g9NjA7c209MTthcHBpZD15dGFjaHlvbg--/https://s.yimg.com/os/creatr-uploaded-images/2022-06/f428ff30-e219-11ec-bbb7-3df0513aa5ab"/>,
#  <img alt="從落地機場那一刻就開吃！ 澎湖花火節期間限定美食報你知" class="W(106px) H(60px)" src="https://s.yimg.com/uu/api/res/1.2/6HZKjONc7MuPouv2MqPxEQ--~B/Zmk9ZmlsbDtweW9mZj0wO3c9MTA2O2g9NjA7c209MTthcHBpZD15dGFjaHlvbg--/https://s.yimg.com/os/creatr-uploaded-images/2022-05/fa054820-dec0-11ec-bf6f-89dd1ebd160c"/>,
#  <img alt="小心！這些行為超容易讓人會錯意｜一起戀愛吧｜卓苡瑄｜男女適用" class="W(106px) H(60px)" src="https://s.yimg.com/uu/api/res/1.2/ImhxeNsWbMt8rLNWGDApdw--~B/Zmk9ZmlsbDtweW9mZj0wO3c9MTA2O2g9NjA7c209MTthcHBpZD15dGFjaHlvbg--/https://s.yimg.com/os/creatr-uploaded-images/2022-06/017dc0c0-e14e-11ec-bdad-5420fa8ea8ee"/>,
#  <img alt="傭兵比正規軍隊更好用？為何烏克蘭和俄國都找傭兵助陣？｜志祺七七" class="W(106px) H(60px)" src="https://s.yimg.com/uu/api/res/1.2/hct.z1e1eYMzCkX51SMiew--~B/Zmk9ZmlsbDtweW9mZj0wO3c9MTA2O2g9NjA7c209MTthcHBpZD15dGFjaHlvbg--/https://s.yimg.com/os/creatr-uploaded-images/2022-06/fe834980-e2ce-11ec-a7f7-a0eba6556956"/>,
#  <img alt="【日式糖心蛋】新手不失敗的異國美食" class="W(106px) H(60px)" src="https://s.yimg.com/uu/api/res/1.2/NLNMWZ.UDhIpBP1Ech4mRQ--~B/Zmk9ZmlsbDtweW9mZj0wO3c9MTA2O2g9NjA7c209MTthcHBpZD15dGFjaHlvbg--/https://s.yimg.com/os/creatr-uploaded-images/2022-06/0eca20f0-e20e-11ec-9dbf-2e59ac9af623"/>,
#  <img alt="05/30 ～06/05 雞、狗、豬的魔法開運秘方【沈嶸魔法生肖開運妙方】" class="W(106px) H(60px)" src="https://s1.yimg.com/uu/api/res/1.2/rWZ9NHmzpFl.9p_83Jn2xg--~B/Zmk9ZmlsbDtweW9mZj0wO3c9MTA2O2g9NjA7c209MTthcHBpZD15dGFjaHlvbg--/https://s.yimg.com/hd/cp-video-transcode/production/52582e25-c013-39e1-a3ae-cf3463e5c5a2/2022-05-28/02-33-22/30f4f35f-4026-515f-ad1a-d415f494ba34/stream_1920x1080x0_v2_3_0.jpg"/>,
#  <img alt="ITZY的她臉竟然比碗還要小?! 畫面曝光網友驚呆「怎麼可能」" class="W(106px) H(60px)" src="https://s.yimg.com/uu/api/res/1.2/P_mSHtRM4khN2_KpcRThqQ--~B/Zmk9ZmlsbDtweW9mZj0wO3c9MTA2O2g9NjA7c209MTthcHBpZD15dGFjaHlvbg--/https://media.zenfs.com/en/__rss_338/b58960189f4a66cfeadd463ff429d7de"/>,
#  <img alt="沒想到外島還能這樣玩 金門後浦老街古裝體驗" class="W(106px) H(60px)" src="https://s1.yimg.com/uu/api/res/1.2/XhswT9DU0qKXIMFqhDGllA--~B/Zmk9ZmlsbDtweW9mZj0wO3c9MTA2O2g9NjA7c209MTthcHBpZD15dGFjaHlvbg--/https://s.yimg.com/os/creatr-uploaded-images/2022-05/edc22940-debe-11ec-9f9d-91946b577733"/>,
#  <img alt="05/30 ～06/05 馬、羊、猴的魔法開運秘方【沈嶸魔法生肖開運妙方】" class="W(106px) H(60px)" src="https://s1.yimg.com/uu/api/res/1.2/81m2qxNsTe0lCsH.JTTo5w--~B/Zmk9ZmlsbDtweW9mZj0wO3c9MTA2O2g9NjA7c209MTthcHBpZD15dGFjaHlvbg--/https://s.yimg.com/hd/cp-video-transcode/production/1a7d5034-e71b-30c7-825a-da407a3c4829/2022-05-28/02-33-21/36a7b828-23c1-5a3f-9c40-4630ec52f440/stream_1920x1080x0_v2_3_0.jpg"/>,
#  <img alt="放手讓哥哥妹妹自己DIY做全家福手印，會變怎樣？" class="W(106px) H(60px)" src="https://s.yimg.com/uu/api/res/1.2/pTPjQ2v_uBJBAvUmCgFyLw--~B/Zmk9ZmlsbDtweW9mZj0wO3c9MTA2O2g9NjA7c209MTthcHBpZD15dGFjaHlvbg--/https://s.yimg.com/os/creatr-uploaded-images/2022-05/590e8de0-dcff-11ec-bddf-290b98168272"/>,
#  <img alt="2022不老騎士歐兜賣環台日記-預告" class="W(106px) H(60px)" src="https://s1.yimg.com/uu/api/res/1.2/e34ZfKghfoCtRJONDITPyA--~B/Zmk9ZmlsbDtweW9mZj0wO3c9MTA2O2g9NjA7c209MTthcHBpZD15dGFjaHlvbg--/https://s.yimg.com/hd/cp-video-transcode/production/85c723d3-be6e-352b-ad4a-7234f15132f2/2022-05-26/14-25-21/6dc40f5e-5a0a-55cd-b154-db8c9e393165/stream_1920x1080x0_v2_3_0.jpg"/>,
#  <img alt="channel-avatar" class="wafer-bind wafer-img Bdrs(50%) H(32px) W(32px)" data-wf-fallback-src="https://s.yimg.com/cv/apiv2/tv/fallback/icon-64-x-64/icon-64-x-64.png" data-wf-src="https://s.yimg.com/cv/apiv2/news02/______176X176.png" data-wf-state-src="[state.tv.avatar]"/>,
#  <img src="https://sb.scorecardresearch.com/p?c1=2&amp;c2=7241469&amp;c7=https%3A%2F%2Ftw.yahoo.com%2F&amp;c5=152963594&amp;cv=2.0&amp;cj=1&amp;c14=-1"/>]
```
