## 安裝Selenium
Selenium是一個瀏覽器自動化的套件(Package)，可以利用Python撰寫自動化的腳本來執行各種的網站，包含開啟瀏覽器、填寫表單、點擊按鈕及取得網站內容等，多數用來執行網站功能的自動化測試，簡化繁瑣及耗時的網站測試工作，是Python自動化應用非常重要的套件(Package)。  
首先需要安裝套件 selenium，利用下幫程式碼安裝。  
```
python -m pip install selenium
```  
## 安裝Selenium WebDriver
而要啟動特定的瀏覽器，需要手動安裝相應的WebDriver。本文將以Chrome瀏覽器來作為教學範例。
首先，前往Python套件庫PyPI，搜尋selenium，進入套件說明畫面後，在下面Drivers的地方，列出了常用的瀏覽器Drivers，如下圖：
<img width="595" alt="image" src="https://user-images.githubusercontent.com/27804948/173187095-d05e4677-9a74-4989-bbb7-1d53cd833d67.png">

點擊Chrome的下載連結後，選擇要執行的Chrome版本，如下圖：
![image](https://user-images.githubusercontent.com/27804948/173187121-d3f9dc7d-8d35-4ea9-90fb-aeee7f466a84.png)

接著，依據作業系統下載安裝檔，如下圖：
![image](https://user-images.githubusercontent.com/27804948/173187126-52b6808b-a3a5-4346-9225-c4d7ba2c5451.png)

解壓縮檔後，將其中的執行檔複製到PySeleniumPost專案的資料夾中即可，無需執行它。
