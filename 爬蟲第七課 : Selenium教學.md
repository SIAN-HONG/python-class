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
先看看自己的Chrome是甚麼版本，點擊右上方<img width="14" alt="image" src="https://user-images.githubusercontent.com/27804948/173187276-561035cc-fd5c-473c-920f-d0c0f41efb4b.png">
接著[設定][關於Google Chrome]
<img width="959" alt="image" src="https://user-images.githubusercontent.com/27804948/173187216-7807bad0-67d9-4174-941b-3673a207471a.png">

點擊Chrome的下載連結後，選擇要執行的Chrome版本，如下圖：  
<img width="551" alt="image" src="https://user-images.githubusercontent.com/27804948/173187159-27bb0460-74a7-4ced-bb7a-d9a5ff2095b0.png">

接著，依據作業系統下載安裝檔，如下圖：  
<img width="584" alt="image" src="https://user-images.githubusercontent.com/27804948/173187358-823f5c99-a84e-451e-8bcf-dfaf30ce9ddd.png">  

解壓縮檔後，將其中的執行檔複製到專案的資料夾中即可，無需執行它。

## 使用Selenium打開網頁
回到專案，引用selenium套件(Package)中的webdriver模組(Module)，其中包含了常用的瀏覽器類別(Class)，而要自動啟動Chrome瀏覽器，需要建立Chrome類別(Class)的物件，傳入剛剛所下載的WebDriver路徑，接著即可透過get()方法(Method)前往網站，如下範例：
```Python
from selenium import webdriver
driver_path = r'C:\Users\fxp87\OneDrive\Desktop\爬蟲\程式碼\chromedriver.exe'
driver = webdriver.Chrome(driver_path)
url = 'https://www.google.com/'
driver.get(url)
```
selenium套件還可以輸入文字點擊搜尋。  
<img width="952" alt="image" src="https://user-images.githubusercontent.com/27804948/173188242-34a44235-7526-4ad6-a170-6a710a1a2151.png">

```Python
from selenium import webdriver
driver_path = r'C:\Users\fxp87\OneDrive\Desktop\爬蟲\程式碼\chromedriver.exe'
driver = webdriver.Chrome(driver_path)
url = 'https://www.google.com/'
driver.get(url)
# 定位搜尋框
element = driver.find_element_by_class_name("gLFyf.gsfi")
# 傳入字串
element.send_keys("Selenium Python")
```
<img width="956" alt="image" src="https://user-images.githubusercontent.com/27804948/173188076-b7c302e4-e22a-4ba4-834d-b7f5c3411897.png">  
進一步按下搜尋按鈕。  

```Python 
button = driver.find_element_by_class_name("gNO89b")
button.click()
```  
  
<img width="957" alt="image" src="https://user-images.githubusercontent.com/27804948/173188319-8905240f-bfb1-413a-998d-3d4a2a538d35.png">

