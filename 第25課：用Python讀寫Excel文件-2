## 第25課：用Python讀寫Excel文件-2

### Excel簡介

Excel是Microsoft（微軟）為使用Windows和macOS操作系統開發的一款電子表格軟件。 Excel憑藉其直觀的界面、出色的計算功能和圖表工具，再加上成功的市場營銷，一直以來都是最為流行的個人計算機數據處理軟件。當然，Excel也有很多競品，例如Google Sheets、LibreOffice Calc、Numbers等，這些競品基本上也能夠兼容Excel，至少能夠讀寫較新版本的Excel文件，當然這些不是我們討論的重點。掌握用Python程序操作Excel文件，可以讓日常辦公自動化的工作更加輕鬆愉快，而且在很多商業項目中，導入導出Excel文件都是特別常見的功能。

本章我們繼續講解基於另一個三方庫`openpyxl`如何進行Excel文件操作，首先需要先安裝它。

```Bash
pip install openpyxl
```

`openpyxl`的優點在於，當我們打開一個Excel文件後，既可以對它進行讀操作，又可以對它進行寫操作，而且在操作的便捷性上是優於`xlwt`和`xlrd`的。此外，如果要進行樣式編輯和公式計算，使用`openpyxl`也遠比上一個章節我們講解的方式更為簡單，而且`openpyxl`還支持數據透視和插入圖表等操作，功能非常強大。有一點需要再次強調，`openpyxl`並不支持操作Office 2007以前版本的Excel文件。

### 讀取Excel文件

例如在當前文件夾下有一個名為“阿里巴巴2020年股票數據.xlsx”的Excel文件，如果想讀取並顯示該文件的內容，可以通過如下所示的代碼來完成。

```Python
import datetime

import openpyxl

# 加載一個工作簿 ---> Workbook
wb = openpyxl.load_workbook('阿里巴巴2020年股票數據.xlsx')
# 獲取工作表的名字
print(wb.sheetnames)
# 獲取工作表 ---> Worksheet
sheet = wb.worksheets[0]
# 獲得單元格的範圍
print(sheet.dimensions)
# 獲得行數和列數
print(sheet.max_row, sheet.max_column)

# 獲取指定單元格的值
print(sheet.cell(3, 3).value)
print(sheet['C3'].value)
print(sheet['G255'].value)

# 獲取多個單元格（嵌套元組）
print(sheet['A2:C5'])

# 讀取所有單元格的數據
for row_ch in range(2, sheet.max_row + 1):
    for col_ch in 'ABCDEFG':
        value = sheet[f'{col_ch}{row_ch}'].value
        if type(value) == datetime.datetime:
            print(value.strftime('%Y年%m月%d日'), end='\t')
        elif type(value) == int:
            print(f'{value:<10d}', end='\t')
        elif type(value) == float:
            print(f'{value:.4f}', end='\t')
        else:
            print(value, end='\t')
    print()
```

> **提示**：上面代碼中使用的Excel文件“阿里巴巴2020年股票數據.xlsx”可以通過後面的百度雲盤地址進行獲取。鏈接:https://pan.baidu.com/s/1rQujl5RQn9R7PadB2Z5g_g 提取碼:e7b4。

需要提醒大家一點，`openpyxl`獲取指定的單元格有兩種方式，一種是通過`cell`方法，需要注意，該方法的行索引和列索引都是從`1`開始的，這是為了照顧用慣了Excel的人的習慣；另一種是通過索引運算，通過指定單元格的坐標，例如`C3`、`G255`，也可以取得對應的單元格，再通過單元格對象的`value`屬性，就可以獲取到單元格的值。通過上面的代碼，相信大家還注意到了，可以通過類似`sheet['A2:C5']`或`sheet['A2':'C5']`這樣的切片操作獲取多個單元格，該操作將返回嵌套的元組，相當於獲取到了多行多列。

### 寫Excel文件

下面我們使用`openpyxl`來進行寫Excel操作。

```Python
import random

import openpyxl

# 第一步：創建工作簿（Workbook）
wb = openpyxl.Workbook()

# 第二步：添加工作表（Worksheet）
sheet = wb.active
sheet.title = '期末成績'

titles = ('姓名', '語文', '數學', '英語')
for col_index, title in enumerate(titles):
    sheet.cell(1, col_index + 1, title)

names = ('關羽', '張飛', '趙雲', '馬超', '黃忠')
for row_index, name in enumerate(names):
    sheet.cell(row_index + 2, 1, name)
    for col_index in range(2, 5):
        sheet.cell(row_index + 2, col_index, random.randrange(50, 101))

# 第四步：保存工作簿
wb.save('考試成績表.xlsx')
```

#### 調整樣式和公式計算

在使用`openpyxl`操作Excel時，如果要調整單元格的樣式，可以直接通過單元格對象（`Cell`對象）的屬性進行操作。單元格對象的屬性包括字體（`font`）、對齊（`alignment`）、邊框（`border`）等，具體的可以參考`openpyxl`的[官方文檔](https://openpyxl.readthedocs.io/en/stable/index.html)。在使用`openpyxl`時，如果需要做公式計算，可以完全按照Excel中的操作方式來進行，具體的代碼如下所示。

```Python
import openpyxl
from openpyxl.styles import Font, Alignment, Border, Side

# 對齊方式
alignment = Alignment(horizontal='center', vertical='center')
# 邊框線條
side = Side(color='ff7f50', style='mediumDashed')

wb = openpyxl.load_workbook('考試成績表.xlsx')
sheet = wb.worksheets[0]

# 調整行高和列寬
sheet.row_dimensions[1].height = 30
sheet.column_dimensions['E'].width = 120

sheet['E1'] = '平均分'
# 設置字體
sheet.cell(1, 5).font = Font(size=18, bold=True, color='ff1493', name='華文楷體')
# 設置對齊方式
sheet.cell(1, 5).alignment = alignment
# 設置單元格邊框
sheet.cell(1, 5).border = Border(left=side, top=side, right=side, bottom=side)
for i in range(2, 7):
    # 公式計算每個學生的平均分
    sheet[f'E{i}'] = f'=average(B{i}:D{i})'
    sheet.cell(i, 5).font = Font(size=12, color='4169e1', italic=True)
    sheet.cell(i, 5).alignment = alignment

wb.save('考試成績表.xlsx')
```

### 生成統計圖表

通過`openpyxl`庫，可以直接向Excel中插入統計圖表，具體的做法跟在Excel中插入圖表大體一致。我們可以創建指定類型的圖表對象，然後通過該對象的屬性對圖表進行設置。當然，最為重要的是為圖表綁定數據，即橫軸代表什麼，縱軸代表什麼，具體的數值是多少。最後，可以將圖表對象添加到表單中，具體的代碼如下所示。

```Python
from openpyxl import Workbook
from openpyxl.chart import BarChart, Reference

wb = Workbook(write_only=True)
sheet = wb.create_sheet()

rows = [
    ('類別', '銷售A組', '銷售B組'),
    ('手機', 40, 30),
    ('平板', 50, 60),
    ('筆記本', 80, 70),
    ('外圍設備', 20, 10),
]

# 向表單中添加行
for row in rows:
    sheet.append(row)

# 創建圖表對象
chart = BarChart()
chart.type = 'col'
chart.style = 10
# 設置圖表的標題
chart.title = '銷售統計圖'
# 設置圖表縱軸的標題
chart.y_axis.title = '銷量'
# 設置圖表橫軸的標題
chart.x_axis.title = '商品類別'
# 設置數據的範圍
data = Reference(sheet, min_col=2, min_row=1, max_row=5, max_col=3)
# 設置分類的範圍
cats = Reference(sheet, min_col=1, min_row=2, max_row=5)
# 給圖表添加數據
chart.add_data(data, titles_from_data=True)
# 給圖表設置分類
chart.set_categories(cats)
chart.shape = 4
# 將圖表添加到表單指定的單元格中
sheet.add_chart(chart, 'A10')

wb.save('demo.xlsx')
```

運行上面的代碼，打開生成的Excel文件，效果如下圖所示。

<img src="https://gitee.com/jackfrued/mypic/raw/master/20210819235009.png" alt="image-20210819235009026" width="75%">

###  簡單的總結

掌握了Python程序操作Excel的方法，可以解決日常辦公中很多繁瑣的處理Excel電子表格工作，最常見就是將多個數據格式相同的Excel文件合併到一個文件以及從多個Excel文件或表單中提取指定的數據。如果數據體量較大或者處理數據的方式比較複雜，我們還是推薦大家使用Python數據分析神器之一的`pandas`庫。
