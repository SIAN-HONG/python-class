## 第26課：用Python操作Word和PowerPoint

在日常工作中，有很多簡單重複的勞動其實完全可以交給Python程序，比如根據樣板文件（模板文件）批量的生成很多個Word文件或PowerPoint文件。 Word是微軟公司開發的文字處理程序，相信大家都不陌生，日常辦公中很多正式的文檔都是用Word進行撰寫和編輯的，目前使用的Word文件後綴名一般為`.docx`。 PowerPoint是微軟公司開發的演示文稿程序，是微軟的Office系列軟件中的一員，被商業人士、教師、學生等群體廣泛使用，通常也將其稱之為“幻燈片”。在Python中，可以使用名為`python-docx` 的三方庫來操作Word，可以使用名為`python-pptx`的三方庫來生成PowerPoint。

### 操作Word文檔

我們可以先通過下面的命令來安裝`python-docx`三方庫。

```bash
pip install python-docx
```

按照[官方文檔](https://python-docx.readthedocs.io/en/latest/)的介紹，我們可以使用如下所示的代碼來生成一個簡單的Word文檔。

```Python
from docx import Document
from docx.shared import Cm, Pt

from docx.document import Document as Doc

# 創建代表Word文檔的Doc對象
document = Document()  # type: Doc
# 添加大標題
document.add_heading('快快樂樂學Python', 0)
# 添加段落
p = document.add_paragraph('Python是一門非常流行的編程語言，它')
run = p.add_run('簡單')
run.bold = True
run.font.size = Pt(18)
p.add_run('而且')
run = p.add_run('優雅')
run.font.size = Pt(18)
run.underline = True
p.add_run('。')

# 添加一級標題
document.add_heading('Heading, level 1', level=1)
# 添加帶樣式的段落
document.add_paragraph('Intense quote', style='Intense Quote')
# 添加無序列表
document.add_paragraph(
    'first item in unordered list', style='List Bullet'
)
document.add_paragraph(
    'second item in ordered list', style='List Bullet'
)
# 添加有序列表
document.add_paragraph(
    'first item in ordered list', style='List Number'
)
document.add_paragraph(
    'second item in ordered list', style='List Number'
)

# 添加圖片（注意路徑和圖片必須要存在）
document.add_picture('resources/guido.jpg', width=Cm(5.2))

# 添加分節符
document.add_section()

records = (
    ('駱昊', '男', '1995-5-5'),
    ('孫美麗', '女', '1992-2-2')
)
# 添加表格
table = document.add_table(rows=1, cols=3)
table.style = 'Dark List'
hdr_cells = table.rows[0].cells
hdr_cells[0].text = '姓名'
hdr_cells[1].text = '性別'
hdr_cells[2].text = '出生日期'
# 為表格添加行
for name, sex, birthday in records:
    row_cells = table.add_row().cells
    row_cells[0].text = name
    row_cells[1].text = sex
    row_cells[2].text = birthday

# 添加分頁符
document.add_page_break()

# 保存文檔
document.save('demo.docx')
```

> **提示**：上面代碼第7行中的註釋`# type: Doc`是為了在PyCharm中獲得代碼補全提示，因為如果不清楚對象具體的數據類型，PyCharm無法在後續代碼中給出`Doc`對象的代碼補全提示。

執行上面的代碼，打開生成的Word文檔，效果如下圖所示。

<img src="https://gitee.com/jackfrued/mypic/raw/master/20210820002742.png" alt="image-20210820002742341" width="40%">&nbsp;&nbsp;<img src="https://gitee.com/jackfrued/mypic/raw/master/20210820002843.png" alt="image-20210820002843696" width="40%">

對於一個已經存在的Word文件，我們可以通過下面的代碼去遍歷它所有的段落並獲取對應的內容。

```Python
from docx import Document
from docx.document import Document as Doc

doc = Document('resources/離職證明.docx')  # type: Doc
for no, p in enumerate(doc.paragraphs):
    print(no, p.text)
```

> **提示**：如果需要上面代碼中的Word文件，可以通過下面的百度雲盤地址進行獲取。鏈接:https://pan.baidu.com/s/1rQujl5RQn9R7PadB2Z5g_g 提取碼:e7b4。

讀取到的內容如下所示。

```
0 
1 離 職 證 明
2 
3 茲證明 王大錘 ，身份證號碼： 100200199512120001 ，於 2018 年 8 月 7 日至 2020 年 6 月 28 日在我單位  開發部 部門擔任 Java開發工程師 職務，在職期間無不良表現。因 個人 原因，於 2020 年 6 月 28 日起終止解除勞動合同。現已結清財務相關費用，辦理完解除勞動關係相關手續，雙方不存在任何勞動爭議。
4 
5 特此證明！
6 
7 
8 公司名稱（蓋章）:成都風車車科技有限公司
9    			2020 年 6 月 28 日
```

講到這裡，相信很多讀者已經想到了，我們可以把上面的離職證明製作成一個模板文件，把姓名、身份證號、入職和離職日期等信息用佔位符代替，這樣通過對占位符的替換，就可以根據實際需要寫入對應的信息，這樣就可以批量的生成Word文檔。

按照上面的思路，我們首先編輯一個離職證明的模板文件，如下圖所示。

<img src="https://gitee.com/jackfrued/mypic/raw/master/20210820004223.png" alt="image-20210820004223731" width="75%" style="border:1px solid black"/>

接下來我們讀取該文件，將佔位符替換為真實信息，就可以生成一個新的Word文檔，如下所示。

```Python
from docx import Document
from docx.document import Document as Doc

# 將真實信息用字典的方式保存在列表中
employees = [
    {
        'name': '駱昊',
        'id': '100200198011280001',
        'sdate': '2008年3月1日',
        'edate': '2012年2月29日',
        'department': '產品研發',
        'position': '架構師',
        'company': '成都華為技術有限公司'
    },
    {
        'name': '王大錘',
        'id': '510210199012125566',
        'sdate': '2019年1月1日',
        'edate': '2021年4月30日',
        'department': '產品研發',
        'position': 'Python開發工程師',
        'company': '成都谷道科技有限公司'
    },
    {
        'name': '李元芳',
        'id': '2102101995103221599',
        'sdate': '2020年5月10日',
        'edate': '2021年3月5日',
        'department': '產品研發',
        'position': 'Java開發工程師',
        'company': '同城企業管理集團有限公司'
    },
]
# 對列表進行循環遍歷，批量生成Word文檔 
for emp_dict in employees:
    # 讀取離職證明模板文件
    doc = Document('resources/離職證明模板.docx')  # type: Doc
    # 循環遍歷所有段落尋找佔位符
    for p in doc.paragraphs:
        if '{' not in p.text:
            continue
        # 不能直接修改段落內容，否則會丟失樣式
        # 所以需要對段落中的元素進行遍歷並進行查找替換
        for run in p.runs:
            if '{' not in run.text:
                continue
            # 將佔位符換成實際內容
            start, end = run.text.find('{'), run.text.find('}')
            key, place_holder = run.text[start + 1:end], run.text[start:end + 1]
            run.text = run.text.replace(place_holder, emp_dict[key])
    # 每個人對應保存一個Word文檔
    doc.save(f'{emp_dict["name"]}離職證明.docx')
```

執行上面的代碼，會在當前路徑下生成三個Word文檔，如下圖所示。

<img src="https://gitee.com/jackfrued/mypic/raw/master/20210820004825.png" alt="image-20210820004825183" width="50%">

### 生成PowerPoint

首先我們需要安裝名為`python-pptx`的三方庫，命令如下所示。

```Bash
pip install python-pptx
```

用Python操作PowerPoint的內容，因為實際應用場景不算很多，我不打算在這裡進行贅述，有興趣的讀者可以自行閱讀`python-pptx`的[官方文檔](https://python-pptx.readthedocs.io/en/latest/)，下面僅展示一段來自於官方文檔的代碼。

```Python
from pptx import Presentation

# 創建幻燈片對象
pres = Presentation()

# 選擇母版添加一頁
title_slide_layout = pres.slide_layouts[0]
slide = pres.slides.add_slide(title_slide_layout)
# 獲取標題欄和副標題欄
title = slide.shapes.title
subtitle = slide.placeholders[1]
# 編輯標題和副標題
title.text = "Welcome to Python"
subtitle.text = "Life is short, I use Python"

# 選擇母版添加一頁
bullet_slide_layout = pres.slide_layouts[1]
slide = pres.slides.add_slide(bullet_slide_layout)
# 獲取頁面上所有形狀
shapes = slide.shapes
# 獲取標題和主體
title_shape = shapes.title
body_shape = shapes.placeholders[1]
# 編輯標題
title_shape.text = 'Introduction'
# 編輯主體內容
tf = body_shape.text_frame
tf.text = 'History of Python'
# 添加一個一級段落
p = tf.add_paragraph()
p.text = 'X\'max 1989'
p.level = 1
# 添加一個二級段落
p = tf.add_paragraph()
p.text = 'Guido began to write interpreter for Python.'
p.level = 2

# 保存幻燈片
pres.save('test.pptx')
```

運行上面的代碼，生成的PowerPoint文件如下圖所示。

<img src="https://gitee.com/jackfrued/mypic/raw/master/20210820010306.png" alt="image-20210820010306008" width="75%" />

### 簡單的總結

用Python程序解決辦公自動化的問題真的非常酷，它可以將我們從繁瑣乏味的勞動中解放出來。寫這類代碼就是去做一件一勞永逸的事情，寫代碼的過程即便不怎麼愉快，使用這些代碼的時候應該是非常開心的。
