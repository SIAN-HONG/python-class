## 爬蟲實戰foodpanda
我們這次來做foodpanda的爬蟲，把你附近餐廳的菜單都爬回來吧!  
同學們可以先使用request.get試試看抓回的東西，有沒有怪怪的地方?  
<img width="696" alt="image" src="https://user-images.githubusercontent.com/27804948/174481239-a71efc7f-8d95-4df6-a8eb-7507605b2814.png">

```Python
import requests
import bs4
headers = { 'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.54 Safari/537.36', }
url = 'https://www.foodpanda.com.tw/restaurants/new?lat=24.8810776&lng=121.001876&vertical=restaurants&expedition=delivery'
response = requests.get(url, headers=headers)
print(response.raise_for_status())
objSoup = bs4.BeautifulSoup(response.content, 'lxml')
dataTag = objSoup.find_all('span', {'class':'name fn'})
print("串列長度", len(dataTag))
# None
# 串列長度 0
```
可以發現怎麼HTML下載的東西不如預期，也找不到\<span class="name fn"\>，那是因為foodpanda他網站架構的關係，他是屬於AJAX框架，會先傳送框架資訊再傳送資料，資料傳送方式是藉由另外的網址傳送，所以你用request.get會抓到網站框架並不是裡面店家的資訊喔!
## 下載foodpanda官網HTML
因為foodpanda是AJAX框架，又找不到網站中回傳資料的網址，所以我們需要用不同方式去抓取，就是Selenium來幫忙做HTML下載，搭配BeautifulSoup去解析HTML，抓取餐廳資訊。  
同學依據上次上課安裝了Selenium，並且下載好驅動程式了，那我們先第一步下載到有包含餐廳資料的HTML，觀察到\<ul class="vendor-list"\>包含了全部的餐廳，用.get_attribute('innerHTML')可以拿到HTML的資訊，此時driver_html就是Selenium抓下來的HTML摟。
<img width="693" alt="image" src="https://user-images.githubusercontent.com/27804948/174482294-cdf0b060-2022-4061-bd5a-796f66722ef7.png">
```Python
from selenium import webdriver
import requests
latitude = 24.8067738
longitude = 121.0476315
driver_path = r'C:\Users\fxp87\OneDrive\Desktop\爬蟲\程式碼\chromedriver.exe'
driver = webdriver.Chrome(driver_path)
url = 'https://www.foodpanda.com.tw/restaurants/new?lat={}&lng={}&vertical=restaurants&expedition=delivery'.format(latitude,longitude)
driver.get(url)
driver_html = driver.find_element_by_class_name("vendor-list").get_attribute('innerHTML')
driver.quit()
```
接下來換BeautifulSoup上場，因為driver_html現在已經是一般文字檔了，所以直接給BeautifulSoup就可以解析  
(1) .get('data-testid')可以抓到餐廳代碼以便之後到餐廳網址下載菜單及價錢，  
(2) .find('span', {'class' : 'name fn'})抓到餐廳的名稱，  
(3) .get('href')拿到餐廳的網址，  
最後用dictionary蒐集我們抓到的餐廳資訊  
```Python
objSoup = bs4.BeautifulSoup(driver_html, 'html.parser')
dataTag = objSoup.find_all('a')
restaurants_dict = {}
for i in range(len(dataTag)):
    restaurant_id = dataTag[i].get('data-testid').split('-')[-1]
    restaurants_nm = dataTag[i].find('span', {'class' : 'name fn'}).text
    restaurant_url = 'https://www.foodpanda.com.tw' + dataTag[i].get('href')
    restaurants_dict[restaurant_id] = (restaurants_nm,restaurant_url)
```
同學在這邊應該會發現說為甚麼已經有餐廳網址了還要載餐廳代碼幹嘛，同學可以用餐廳網址去試看看request.get(restaurant_url)，也是會有跟前面foodpanda首頁一樣的AJAX框架，所以你抓的不會是有資料的餐廳網址，也不能用Selenium一個一個餐廳抓，時間非常久，會有被當作是機器人的風險。


