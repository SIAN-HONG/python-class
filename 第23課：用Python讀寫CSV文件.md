## 第23課：用Python讀寫CSV文件

### CSV文件介紹

CSV（Comma Separated Values）全稱逗號分隔值文件是一種簡單、通用的文件格式，被廣泛的應用於應用程序（數據庫、電子表格等）數據的導入和導出以及異構系統之間的數據交換。因為CSV是純文本文件，不管是什麼操作系統和編程語言都是可以處理純文本的，而且很多編程語言中都提供了對讀寫CSV文件的支持，因此CSV格式在數據處理和數據科學中被廣泛應用。

CSV文件有以下特點：

1. 純文本，使用某種字符集（如[ASCII](https://zh.wikipedia.org/wiki/ASCII)、[Unicode](https://zh.wikipedia.org/wiki/Unicode)、[GB2312](https://zh.wikipedia.org/wiki/GB2312)）等）；
2. 由一條條的記錄組成（典型的是每行一條記錄）；
3. 每條記錄被分隔符（如逗號、分號、製表符等）分隔為字段（列）；
4. 每條記錄都有同樣的字段序列。

CSV文件可以使用文本編輯器或類似於Excel電子表格這類工具打開和編輯，當使用Excel這類電子表格打開CSV文件時，你甚至感覺不到CSV和Excel文件的區別。很多數據庫系統都支持將數據導出到CSV文件中，當然也支持從CSV文件中讀入數據保存到數據庫中，這些內容並不是現在要討論的重點。

### 將數據寫入CSV文件

現有五個學生三門課程的考試成績需要保存到一個CSV文件中，要達成這個目標，可以使用Python標準庫中的`csv`模塊，該模塊的`writer`函數會返回一個`csvwriter`對象，通過該對象的`writerow`或`writerows`方法就可以將數據寫入到CSV文件中，具體的代碼如下所示。

```Python
import csv
import random

with open('scores.csv', 'w') as file:
    writer = csv.writer(file)
    writer.writerow(['姓名', '語文', '數學', '英語'])
    names = ['關羽', '張飛', '趙雲', '馬超', '黃忠']
    for name in names:
        scores = [random.randrange(50, 101) for _ in range(3)]
        scores.insert(0, name)
        writer.writerow(scores)
```

生成的CSV文件的內容。

```
姓名,語文,數學,英語
關羽,98,86,61
張飛,86,58,80
趙雲,95,73,70
馬超,83,97,55
黃忠,61,54,87
```

需要說明的是上面的`writer`函數，除了傳入要寫入數據的文件對像外，還可以`dialect`參數，它表示CSV文件的方言，默認值是`excel`。除此之外，還可以通過`delimiter`、`quotechar`、`quoting`參數來指定分隔符（默認是逗號）、包圍值的字符（默認是雙引號）以及包圍的方式。其中，包圍值的字符主要用於當字段中有特殊符號時，通過添加包圍值的字符可以避免二義性。大家可以嘗試將上面第5行代碼修改為下面的代碼，然後查看生成的CSV文件。

```Python
writer = csv.writer(file, delimiter='|', quoting=csv.QUOTE_ALL)
```

生成的CSV文件的內容。

```
"姓名"|"語文"|"數學"|"英語"
"關羽"|"88"|"64"|"65"
"張飛"|"76"|"93"|"79"
"趙雲"|"78"|"55"|"76"
"馬超"|"72"|"77"|"68"
"黃忠"|"70"|"72"|"51"
```

### 從CSV文件讀取數據

如果要讀取剛才創建的CSV文件，可以使用下面的代碼，通過`csv`模塊的`reader`函數可以創建出`csvreader`對象，該對像是一個迭代器，可以通過`next`函數或`for-in`循環讀取到文件中的數據。

```Python
import csv

with open('scores.csv', 'r') as file:
    reader = csv.reader(file, delimiter='|')
    for data_list in reader:
        print(reader.line_num, end='\t')
        for elem in data_list:
            print(elem, end='\t')
        print()
```

> **注意**：上面的代碼對`csvreader`對像做`for`循環時，每次會取出一個列表對象，該列表對象包含了一行中所有的字段。

###  簡單的總結

將來如果大家使用Python做數據分析，很有可能會用到名為`pandas`的三方庫，它是Python數據分析的神器之一。 `pandas`中封裝了名為`read_csv`和`to_csv`的函數用來讀寫CSV文件，其中`read_CSV`會將讀取到的數據變成一個`DataFrame`對象，而`DataFrame`就是`pandas`庫中最重要的類型，它封裝了一系列用於數據處理的方法（清洗、轉換、聚合等）；而`to_csv`會將`DataFrame`對像中的數據寫入CSV文件，完成數據的持久化。 `read_csv`函數和`to_csv`函數遠遠比原生的`csvreader`和`csvwriter`強大。
