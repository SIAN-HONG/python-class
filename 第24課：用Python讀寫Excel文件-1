## 第24課：用Python讀寫Excel文件-1

### Excel簡介

Excel是Microsoft（微軟）為使用Windows和macOS操作系統開發的一款電子表格軟件。 Excel憑藉其直觀的界面、出色的計算功能和圖表工具，再加上成功的市場營銷，一直以來都是最為流行的個人計算機數據處理軟件。當然，Excel也有很多競品，例如Google Sheets、LibreOffice Calc、Numbers等，這些競品基本上也能夠兼容Excel，至少能夠讀寫較新版本的Excel文件，當然這些不是我們討論的重點。掌握用Python程序操作Excel文件，可以讓日常辦公自動化的工作更加輕鬆愉快，而且在很多商業項目中，導入導出Excel文件都是特別常見的功能。

Python操作Excel需要三方庫的支持，如果要兼容Excel 2007以前的版本，也就是`xls`格式的Excel文件，可以使用三方庫`xlrd`和`xlwt`，前者用於讀Excel文件，後者用於寫Excel文件。如果使用較新版本的Excel，即操作`xlsx`格式的Excel文件，可以使用`openpyxl`庫，當然這個庫不僅僅可以操作Excel，還可以操作其他基於Office Open XML的電子表格文件。

本章我們先講解基於`xlwt`和`xlrd`操作Excel文件，大家可以先使用下面的命令安裝這兩個三方庫以及配合使用的工具模塊`xlutils`。

```Bash
pip install xlwt xlrd xlutils
```

### 讀Excel文件

例如在當前文件夾下有一個名為“阿里巴巴2020年股票數據.xls”的Excel文件，如果想讀取並顯示該文件的內容，可以通過如下所示的代碼來完成。

```Python
import xlrd

# 使用xlrd模塊的open_workbook函數打開指定Excel文件並獲得Book對象（工作簿）
wb = xlrd.open_workbook('阿里巴巴2020年股票數據.xls')
# 通過Book對象的sheet_names方法可以獲取所有表單名稱
sheetnames = wb.sheet_names()
print(sheetnames)
# 通過指定的表單名稱獲取Sheet對象（工作表）
sheet = wb.sheet_by_name(sheetnames[0])
# 通過Sheet對象的nrows和ncols屬性獲取表單的行數和列數
print(sheet.nrows, sheet.ncols)
for row in range(sheet.nrows):
    for col in range(sheet.ncols):
        # 通過Sheet對象的cell方法獲取指定Cell對象（單元格）
        # 通過Cell對象的value屬性獲取單元格中的值
        value = sheet.cell(row, col).value
        # 對除首行外的其他行進行數據格式化處理
        if row > 0:
            # 第1列的xldate類型先轉成元組再格式化為“年月日”的格式
            if col == 0:
                # xldate_as_tuple函數的第二個參數只有0和1兩個取值
                # 其中0代表以1900-01-01為基準的日期，1代表以1904-01-01為基準的日期
                value = xlrd.xldate_as_tuple(value, 0)
                value = f'{value[0]}年{value[1]:>02d}月{value[2]:>02d}日'
            # 其他列的number類型處理成小數點後保留兩位有效數字的浮點數
            else:
                value = f'{value:.2f}'
        print(value, end='\t')
    print()
# 獲取最後一個單元格的數據類型
# 0 - 空值，1 - 字符串，2 - 數字，3 - 日期，4 - 布爾，5 - 錯誤
last_cell_type = sheet.cell_type(sheet.nrows - 1, sheet.ncols - 1)
print(last_cell_type)
# 獲取第一行的值（列表）
print(sheet.row_values(0))
# 獲取指定行指定列範圍的數據（列表）
# 第一個參數代表行索引，第二個和第三個參數代表列的開始（含）和結束（不含）索引
print(sheet.row_slice(3, 0, 5))
```

> **提示**：上面代碼中使用的Excel文件“阿里巴巴2020年股票數據.xls”可以通過後面的百度雲盤地址進行獲取。鏈接:https://pan.baidu.com/s/1rQujl5RQn9R7PadB2Z5g_g 提取碼:e7b4。

相信通過上面的代碼，大家已經了解到瞭如何讀取一個Excel文件，如果想知道更多關於`xlrd`模塊的知識，可以閱讀它的[官方文檔](https://xlrd.readthedocs.io/en/latest/)。

### 寫Excel文件

寫入Excel文件可以通過`xlwt` 模塊的`Workbook`類創建工作簿對象，通過工作簿對象的`add_sheet`方法可以添加工作表，通過工作表對象的`write`方法可以向指定單元格中寫入數據，最後通過工作簿對象的`save`方法將工作簿寫入到指定的文件或內存中。下面的代碼實現了將`5`個學生`3`門課程的考試成績寫入Excel文件的操作。

```Python
import random

import xlwt

student_names = ['關羽', '張飛', '趙雲', '馬超', '黃忠']
scores = [[random.randrange(50, 101) for _ in range(3)] for _ in range(5)]
# 創建工作簿對象（Workbook）
wb = xlwt.Workbook()
# 創建工作表對象（Worksheet）
sheet = wb.add_sheet('一年級二班')
# 添加表頭數據
titles = ('姓名', '語文', '數學', '英語')
for index, title in enumerate(titles):
    sheet.write(0, index, title)
# 將學生姓名和考試成績寫入單元格
for row in range(len(scores)):
    sheet.write(row + 1, 0, student_names[row])
    for col in range(len(scores[row])):
        sheet.write(row + 1, col + 1, scores[row][col])
# 保存Excel工作簿
wb.save('考試成績表.xls')
```

#### 調整單元格樣式

在寫Excel文件時，我們還可以為單元格設置樣式，主要包括字體（Font）、對齊方式（Alignment）、邊框（Border）和背景（Background）的設置，`xlwt`對這幾項設置都封裝了對應的類來支持。要設置單元格樣式需要首先創建一個`XFStyle`對象，再通過該對象的屬性對字體、對齊方式、邊框等進行設定，例如在上面的例子中，如果希望將表頭單元格的背景色修改為黃色，可以按照如下的方式進行操作。

```Python
header_style = xlwt.XFStyle()
pattern = xlwt.Pattern()
pattern.pattern = xlwt.Pattern.SOLID_PATTERN
# 0 - 黑色、1 - 白色、2 - 紅色、3 - 綠色、4 - 藍色、5 - 黃色、6 - 粉色、7 - 青色
pattern.pattern_fore_colour = 5
header_style.pattern = pattern
titles = ('姓名', '語文', '數學', '英語')
for index, title in enumerate(titles):
    sheet.write(0, index, title, header_style)
```

如果希望為表頭設置指定的字體，可以使用`Font`類並添加如下所示的代碼。

```Python
font = xlwt.Font()
# 字體名稱
font.name = '華文楷體'
# 字體大小（20是基准單位，18表示18px）
font.height = 20 * 18
# 是否使用粗體
font.bold = True
# 是否使用斜體
font.italic = False
# 字體顏色
font.colour_index = 1
header_style.font = font
```

> **注意**：上面代碼中指定的字體名（`font.name`）應當是本地系統有的字體，例如在我的電腦上有名為“華文楷體”的字體。

如果希望表頭垂直居中對齊，可以使用下面的代碼進行設置。

```Python
align = xlwt.Alignment()
# 垂直方向的對齊方式
align.vert = xlwt.Alignment.VERT_CENTER
# 水平方向的對齊方式
align.horz = xlwt.Alignment.HORZ_CENTER
header_style.alignment = align
```

如果希望給表頭加上黃色的虛線邊框，可以使用下面的代碼來設置。

```Python
borders = xlwt.Borders()
props = (
    ('top', 'top_colour'), ('right', 'right_colour'),
    ('bottom', 'bottom_colour'), ('left', 'left_colour')
)
# 通過循環對四個方向的邊框樣式及顏色進行設定
for position, color in props:
    # 使用setattr內置函數動態給對象指定的屬性賦值
    setattr(borders, position, xlwt.Borders.DASHED)
    setattr(borders, color, 5)
header_style.borders = borders
```

如果要調整單元格的寬度（列寬）和表頭的高度（行高），可以按照下面的代碼進行操作。

```Python
# 設置行高為40px
sheet.row(0).set_style(xlwt.easyxf(f'font:height {20 * 40}'))
titles = ('姓名', '語文', '數學', '英語')
for index, title in enumerate(titles):
    # 設置列寬為200px
    sheet.col(index).width = 20 * 200
    # 設置單元格的數據和样式
    sheet.write(0, index, title, header_style)
```

#### 公式計算

對於前面打開的“阿里巴巴2020年股票數據.xls”文件，如果要統計全年收盤價（Close字段）的平均值以及全年交易量（Volume字段）的總和，可以使用Excel的公式計算即可。我們可以先使用`xlrd`讀取Excel文件夾，然後通過`xlutils`三方庫提供的`copy`函數將讀取到的Excel文件轉成`Workbook`對象進行寫操作，在調用`write`方法時，可以將一個`Formula`對象寫入單元格。

實現公式計算的代碼如下所示。

```Python
import xlrd
import xlwt
from xlutils.copy import copy

wb_for_read = xlrd.open_workbook('阿里巴巴2020年股票數據.xls')
sheet1 = wb_for_read.sheet_by_index(0)
nrows, ncols = sheet1.nrows, sheet1.ncols
wb_for_write = copy(wb_for_read)
sheet2 = wb_for_write.get_sheet(0)
sheet2.write(nrows, 4, xlwt.Formula(f'average(E2:E{nrows})'))
sheet2.write(nrows, 6, xlwt.Formula(f'sum(G2:G{nrows})'))
wb_for_write.save('阿里巴巴2020年股票數據匯總.xls')
```

> **說明**：上面的代碼有一些小瑕疵，有興趣的讀者可以自行探索並思考如何解決。

###  簡單的總結

掌握了Python程序操作Excel的方法，可以解決日常辦公中很多繁瑣的處理Excel電子表格工作，最常見就是將多個數據格式相同的Excel文件合併到一個文件以及從多個Excel文件或表單中提取指定的數據。當然，如果要對錶格數據進行處理，使用Python數據分析神器之一的`pandas`庫可能更為方便。
