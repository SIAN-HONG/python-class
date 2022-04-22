## 第28課：用Python處理圖像

### 入門知識

1. 顏色。如果你有使用顏料畫畫的經歷，那麼一定知道混合紅、黃、藍三種顏料可以得到其他的顏色，事實上這三種顏色就是美術中的三原色，它們是不能再分解的基本顏色。在計算機中，我們可以將紅、綠、藍三種色光以不同的比例疊加來組合成其他的顏色，因此這三種顏色就是色光三原色。在計算機系統中，我們通常會將一個顏色表示為一個RGB值或RGBA值（其中的A表示Alpha通道，它決定了透過這個圖像的像素，也就是透明度）。

   |    名稱     |      RGB值      |     名稱     |     RGB值     |
   | :---------: | :-------------: | :----------: | :-----------: |
   | White（白） | (255, 255, 255) |  Red（紅）   |  (255, 0, 0)  |
   | Green（綠） |   (0, 255, 0)   |  Blue（藍）  |  (0, 0, 255)  |
   | Gray（灰）  | (128, 128, 128) | Yellow（黃） | (255, 255, 0) |
   | Black（黑） |    (0, 0, 0)    | Purple（紫） | (128, 0, 128) |

2. 像素。對於一個由數字序列表示的圖像來說，最小的單位就是圖像上單一顏色的小方格，這些小方塊都有一個明確的位置和被分配的色彩數值，而這些一小方格的顏色和位置決定了該圖像最終呈現出來的樣子，它們是不可分割的單位，我們通常稱之為像素（pixel）。每一個圖像都包含了一定量的像素，這些像素決定圖像在屏幕上所呈現的大小，大家如果愛好拍照或者自拍，對像素這個詞就不會陌生。

### 用Pillow處理圖像

Pillow是由從著名的Python圖像處理庫PIL發展出來的一個分支，通過Pillow可以實現圖像壓縮和圖像處理等各種操作。可以使用下面的命令來安裝Pillow。

```Shell
pip install pillow
```

Pillow中最為重要的是`Image`類，可以通過`Image`模塊的`open`函數來讀取圖像並獲得`Image`類型的對象。

1. 讀取和顯示圖像

   ```Python
   from PIL import Image
   
   # 讀取圖像獲得Image對象
   image = Image.open('guido.jpg')
   # 通過Image對象的format屬性獲得圖像的格式
   print(image.format) # JPEG
   # 通過Image對象的size屬性獲得圖像的尺寸
   print(image.size)   # (500, 750)
   # 通過Image對象的mode屬性獲取圖像的模式
   print(image.mode)   # RGB
   # 通過Image對象的show方法顯示圖像
   image.show()
   ```

   <img src="https://gitee.com/jackfrued/mypic/raw/master/20210803202628.png" width="80%">

2. 剪裁圖像

   ```Python
   # 通過Image對象的crop方法指定剪裁區域剪裁圖像
   image.crop((80, 20, 310, 360)).show()
   ```

   <img src="https://gitee.com/jackfrued/mypic/raw/master/20210803202701.png" width="80%">

3. 生成縮略圖

   ```Python
   # 通過Image對象的thumbnail方法生成指定尺寸的縮略圖
   image.thumbnail((128, 128))
   image.show()
   ```

   <img src="https://gitee.com/jackfrued/mypic/raw/master/20210803202722.png" width="100%">

4. 縮放和黏貼圖像

   ```Python
   # 讀取駱昊的照片獲得Image對象
   luohao_image = Image.open('luohao.png')
   # 讀取吉多的照片獲得Image對象
   guido_image = Image.open('guido.jpg')
   # 從吉多的照片上剪裁出吉多的頭
   guido_head = guido_image.crop((80, 20, 310, 360))
   width, height = guido_head.size
   # 使用Image對象的resize方法修改圖像的尺寸
   # 使用Image對象的paste方法將吉多的頭粘貼到駱昊的照片上
   luohao_image.paste(guido_head.resize((int(width / 1.5), int(height / 1.5))), (172, 40))
   luohao_image.show()
   ```

   <img src="https://gitee.com/jackfrued/mypic/raw/master/20210803202749.png" width="80%">

5. 旋轉和翻轉

   ```Python
   image = Image.open('guido.jpg')
   # 使用Image對象的rotate方法實現圖像的旋轉
   image.rotate(45).show()
   # 使用Image對象的transpose方法實現圖像翻轉
   # Image.FLIP_LEFT_RIGHT - 水平翻轉
   # Image.FLIP_TOP_BOTTOM - 垂直翻轉
   image.transpose(Image.FLIP_TOP_BOTTOM).show()
   ```

   <img src="https://gitee.com/jackfrued/mypic/raw/master/20210803202829.png" width="80%">

6. 操作像素

   ```Python
   for x in range(80, 310):
       for y in range(20, 360):
           # 通過Image對象的putpixel方法修改圖像指定像素點
           image.putpixel((x, y), (128, 128, 128))
   image.show()
   ```

   <img src="https://gitee.com/jackfrued/mypic/raw/master/20210803202932.png" width="80%">

7. 濾鏡效果

   ```Python
   from PIL import ImageFilter
   
   # 使用Image對象的filter方法對圖像進行濾鏡處理
   # ImageFilter模塊包含了諸多預設的濾鏡也可以自定義濾鏡
   image.filter(ImageFilter.CONTOUR).show()
   ```

   <img src="https://gitee.com/jackfrued/mypic/raw/master/20210803202953.png" width="80%">

### 使用Pillow繪圖

Pillow中有一個名為`ImageDraw`的模塊，該模塊的`Draw`函數會返回一個`ImageDraw`對象，通過`ImageDraw`對象的`arc`、`line`、`rectangle`、`ellipse`、`polygon`等方法，可以在圖像上繪製出圓弧、線條、矩形、橢圓、多邊形等形狀，也可以通過該對象的`text`方法在圖像上添加文字。

<img src="https://gitee.com/jackfrued/mypic/raw/master/20210803203016.png" width="80%">

要繪製如上圖所示的圖像，完整的代碼如下所示。

```Python
import random

from PIL import Image, ImageDraw, ImageFont


def random_color():
    """生成隨機顏色"""
    red = random.randint(0, 255)
    green = random.randint(0, 255)
    blue = random.randint(0, 255)
    return red, green, blue


width, height = 800, 600
# 創建一個800*600的圖像，背景色為白色
image = Image.new(mode='RGB', size=(width, height), color=(255, 255, 255))
# 創建一個ImageDraw對象
drawer = ImageDraw.Draw(image)
# 通過指定字體和大小獲得ImageFont對象
font = ImageFont.truetype('Kongxin.ttf', 32)
# 通過ImageDraw對象的text方法繪製文字
drawer.text((300, 50), 'Hello, world!', fill=(255, 0, 0), font=font)
# 通過ImageDraw對象的line方法繪製兩條對角直線
drawer.line((0, 0, width, height), fill=(0, 0, 255), width=2)
drawer.line((width, 0, 0, height), fill=(0, 0, 255), width=2)
xy = width // 2 - 60, height // 2 - 60, width // 2 + 60, height // 2 + 60
# 通過ImageDraw對象的rectangle方法繪製矩形
drawer.rectangle(xy, outline=(255, 0, 0), width=2)
# 通過ImageDraw對象的ellipse方法繪製橢圓
for i in range(4):
    left, top, right, bottom = 150 + i * 120, 220, 310 + i * 120, 380
    drawer.ellipse((left, top, right, bottom), outline=random_color(), width=8)
# 顯示圖像
image.show()
# 保存圖像
image.save('result.png')
```

> **注意**：上面代碼中使用的字體文件需要根據自己準備，可以選擇自己喜歡的字體文件並放置在代碼目錄下。

###  簡單的總結

使用Python語言做開發，除了可以用Pillow來處理圖像外，還可以使用更為強大的OpenCV庫來完成圖形圖像的處理，OpenCV（**Open** Source **C**omputer **V**ision Library）是一個跨平台的計算機視覺庫，可以用來開發實時圖像處理、計算機視覺和模式識別程序。在我們的日常工作中，有很多繁瑣乏味的任務其實都可以通過Python程序來處理，編程的目的就是讓計算機幫助我們解決問題，減少重複乏味的勞動。通過本章節的學習，相信大家已經感受到了使用Python程序繪圖P圖的樂趣，其實Python能做的事情還遠不止這些，繼續你的學習吧。
