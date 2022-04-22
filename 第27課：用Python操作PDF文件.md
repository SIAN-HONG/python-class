## 第27課：用Python操作PDF文件

PDF是Portable Document Format的縮寫，這類文件通常使用`.pdf`作為其擴展名。在日常開發工作中，最容易遇到的就是從PDF中讀取文本內容以及用已有的內容生成PDF文檔這兩個任務。

### 從PDF中提取文本

在Python中，可以使用名為`PyPDF2`的三方庫來讀取PDF文件，可以使用下面的命令來安裝它。

```Bash
pip install PyPDF2
```

`PyPDF2`沒有辦法從PDF文檔中提取圖像、圖表或其他媒體，但它可以提取文本，並將其返回為Python字符串。

```Python
import PyPDF2

reader = PyPDF2.PdfFileReader('test.pdf')
page = reader.getPage(0)
print(page.extractText())
```

> **提示**：上面代碼中使用的PDF文件“test.pdf”以及下面的代碼中需要用到的PDF文件，也可以通過下面的百度雲盤地址進行獲取。鏈接:https://pan.baidu.com/s/1rQujl5RQn9R7PadB2Z5g_g 提取碼:e7b4。

當然，`PyPDF2`並不是什麼樣的PDF文檔都能提取出文字來，這個問題就我所知並沒有什麼特別好的解決方法，尤其是在提取中文的時候。網上也有很多講解從PDF中提取文字的文章，推薦大家自行閱讀[《三大神器助力Python提取pdf文檔信息》](https://cloud.tencent.com/developer/article/1395339)一文進行了解。

要從PDF文件中提取文本也可以直接使用三方的命令行工具，具體的做法如下所示。

```Bash
pip install pdfminer.six
pdf2text.py test.pdf
```

### 旋轉和疊加頁面

上面的代碼中通過創建`PdfFileReader`對象的方式來讀取PDF文檔，該對象的`getPage`方法可以獲得PDF文檔的指定頁並得到一個`PageObject`對象，通過`PageObject`對象的`rotateClockwise`和`rotateCounterClockwise`方法可以實現頁面的順時針和逆時針方向旋轉，通過`PageObject`對象的`addBlankPage`方法可以添加一個新的空白頁，代碼如下所示。

```Python
import PyPDF2

from PyPDF2.pdf import PageObject

# 創建一個讀PDF文件的Reader對象
reader = PyPDF2.PdfFileReader('resources/XGBoost.pdf')
# 創建一個寫PDF文件的Writer對象
writer = PyPDF2.PdfFileWriter()
# 對PDF文件所有頁進行循環遍歷
for page_num in range(reader.numPages):
    # 獲取指定頁碼的Page對象
    current_page = reader.getPage(page_num)  # type: PageObject
    if page_num % 2 == 0:
        # 奇數頁順時針旋轉90度
        current_page.rotateClockwise(90)
    else:
        # 偶數頁反時針旋轉90度
        current_page.rotateCounterClockwise(90)
    writer.addPage(current_page)
# 最後添加一個空白頁並旋轉90度
page = writer.addBlankPage()  # type: PageObject
page.rotateClockwise(90)
# 通過Writer對象的write方法將PDF寫入文件
with open('resources/XGBoost-modified.pdf', 'wb') as file:
    writer.write(file)
```

### 加密PDF文件

使用`PyPDF2`中的`PdfFileWrite`對象可以為PDF文檔加密，如果需要給一系列的PDF文檔設置統一的訪問口令，使用Python程序來處理就會非常的方便。

```Python
import PyPDF2

reader = PyPDF2.PdfFileReader('resources/XGBoost.pdf')
writer = PyPDF2.PdfFileWriter()
for page_num in range(reader.numPages):
    writer.addPage(reader.getPage(page_num))
# 通過encrypt方法加密PDF文件，方法的參數就是設置的密碼
writer.encrypt('foobared')
with open('resources/XGBoost-encrypted.pdf', 'wb') as file:
    writer.write(file)
```

### 批量添加水印

上面提到的`PageObject`對像還有一個名為`mergePage`的方法，可以兩個PDF頁面進行疊加，通過這個操作，我們很容易實現給PDF文件添加水印的功能。例如要給上面的“XGBoost.pdf”文件添加一個水印，我們可以先準備好一個提供水印頁面的PDF文件，然後將包含水印的`PageObject`讀取出來，然後再循環遍歷“XGBoost.pdf”文件的每個頁，獲取到`PageObject`對象，然後通過`mergePage`方法實現水印頁和原始頁的合併，代碼如下所示。

```Python
import PyPDF2

from PyPDF2.pdf import PageObject

reader1 = PyPDF2.PdfFileReader('resources/XGBoost.pdf')
reader2 = PyPDF2.PdfFileReader('resources/watermark.pdf')
writer = PyPDF2.PdfFileWriter()
# 獲取水印頁
watermark_page = reader2.getPage(0)
for page_num in range(reader1.numPages):
    current_page = reader1.getPage(page_num)  # type: PageObject
    current_page.mergePage(watermark_page)
    # 將原始頁和水印頁進行合併
    writer.addPage(current_page)
# 將PDF寫入文件
with open('resources/XGBoost-watermarked.pdf', 'wb') as file:
    writer.write(file)
```

如果願意，還可以讓奇數頁和偶數頁使用不同的水印，大家可以自己思考下應該怎麼做。

### 創建PDF文件

創建PDF文檔需要三方庫`reportlab`的支持，安裝的方法如下所示。

```Bash
pip install reportlab
```

下面通過一個例子為大家展示`reportlab`的用法。

```Python
from reportlab.lib.pagesizes import A4
from reportlab.pdfbase import pdfmetrics
from reportlab.pdfbase.ttfonts import TTFont
from reportlab.pdfgen import canvas

pdf_canvas = canvas.Canvas('resources/demo.pdf', pagesize=A4)
width, height = A4

# 繪圖
image = canvas.ImageReader('resources/guido.jpg')
pdf_canvas.drawImage(image, 20, height - 395, 250, 375)

# 顯示當前頁
pdf_canvas.showPage()

# 註冊字體文件
pdfmetrics.registerFont(TTFont('Font1', 'resources/fonts/Vera.ttf'))
pdfmetrics.registerFont(TTFont('Font2', 'resources/fonts/青呱石頭體.ttf'))

# 寫字
pdf_canvas.setFont('Font2', 40)
pdf_canvas.setFillColorRGB(0.9, 0.5, 0.3, 1)
pdf_canvas.drawString(width // 2 - 120, height // 2, '你好，世界！')
pdf_canvas.setFont('Font1', 40)
pdf_canvas.setFillColorRGB(0, 1, 0, 0.5)
pdf_canvas.rotate(18)
pdf_canvas.drawString(250, 250, 'hello, world!')

# 保存
pdf_canvas.save()
```

上面的代碼如果不太理解也沒有關係，等真正需要用Python創建PDF文檔的時候，再好好研讀一下`reportlab`的[官方文檔](https://www.reportlab.com/docs/reportlab-userguide.pdf)就可以了。

> **提示**：上面代碼中用到的圖片和字體，也可以通過下面的百度雲盤鏈接獲取。鏈接:https://pan.baidu.com/s/1rQujl5RQn9R7PadB2Z5g_g 提取碼:e7b4。

###  簡單的總結

在學習完上面的內容之後，相信大家已經知道像合併多個PDF文件這樣的工作應該如何用Python代碼來處理了，趕緊自己動手試一試吧。
