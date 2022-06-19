## 爬蟲實戰foodpanda
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
可以發現怎麼HTML下載的東西不如預期，也找不到<span class='name fn'>
## 下載foodpanda官網HTML
