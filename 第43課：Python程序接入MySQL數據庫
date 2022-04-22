## 第43課：Python程序接入MySQL數據庫

在 Python3 中，我們可以使用`mysqlclient`或者`pymysql`三方庫來接入 MySQL 數據庫並實現數據持久化操作。二者的用法完全相同，只是導入的模塊名不一樣。我們推薦大家使用純 Python 的三方庫`pymysql`，因為它更容易安裝成功。下面我們仍然以之前創建的名為`hrs`的數據庫為例，為大家演示如何通過 Python 程序操作 MySQL 數據庫實現數據持久化操作。

### 建庫建表

```SQL
-- 創建名為hrs的數據庫並指定默認的字符集
create database `hrs` default character set utf8mb4;

-- 切換到hrs數據庫
use `hrs`;

-- 創建部門表
create table `tb_dept`
(
`dno` int not null comment '編號',
`dname` varchar(10) not null comment '名稱',
`dloc` varchar(20) not null comment '所在地',
primary key (`dno`)
);

-- 插入4個部門
insert into `tb_dept` values 
    (10, '會計部', '北京'),
    (20, '研發部', '成都'),
    (30, '銷售部', '重慶'),
    (40, '運維部', '深圳');

-- 創建員工表
create table `tb_emp`
(
`eno` int not null comment '員工編號',
`ename` varchar(20) not null comment '員工姓名',
`job` varchar(20) not null comment '員工職位',
`mgr` int comment '主管編號',
`sal` int not null comment '員工月薪',
`comm` int comment '每月補貼',
`dno` int not null comment '所在部門編號',
primary key (`eno`),
constraint `fk_emp_mgr` foreign key (`mgr`) references tb_emp (`eno`),
constraint `fk_emp_dno` foreign key (`dno`) references tb_dept (`dno`)
);

-- 插入14個員工
insert into `tb_emp` values 
    (7800, '張三豐', '總裁', null, 9000, 1200, 20),
    (2056, '喬峰', '分析師', 7800, 5000, 1500, 20),
    (3088, '李莫愁', '設計師', 2056, 3500, 800, 20),
    (3211, '張無忌', '程序員', 2056, 3200, null, 20),
    (3233, '丘處機', '程序員', 2056, 3400, null, 20),
    (3251, '張翠山', '程序員', 2056, 4000, null, 20),
    (5566, '宋遠橋', '會計師', 7800, 4000, 1000, 10),
    (5234, '郭靖', '出納', 5566, 2000, null, 10),
    (3344, '黃蓉', '銷售主管', 7800, 3000, 800, 30),
    (1359, '胡一刀', '銷售員', 3344, 1800, 200, 30),
    (4466, '苗人鳳', '銷售員', 3344, 2500, null, 30),
    (3244, '歐陽鋒', '程序員', 3088, 3200, null, 20),
    (3577, '楊過', '會計', 5566, 2200, null, 10),
    (3588, '朱九真', '會計', 5566, 2500, null, 10);
```

### 接入MySQL

首先，我們可以在命令行或者 PyCharm 的終端中通過下面的命令安裝`pymysql`，如果需要接入 MySQL 8，還需要安裝一個名為`cryptography`的三方庫來支持 MySQL 8 的密碼認證方式。

```Shell
pip install pymysql cryptography
```

使用`pymysql`操作 MySQL 的步驟如下所示：

1. 創建連接。 MySQL 服務器啟動後，提供了基於 TCP （傳輸控制協議）的網絡服務。我們可以通過`pymysql`模塊的`connect`函數連接 MySQL 服務器。在調用`connect`函數時，需要指定主機（`host`）、端口（`port`）、用戶名（`user`）、口令（`password`）、數據庫（`database`）、字符集（`charset`）等參數，該函數會返回一個`Connection`對象。
2. 獲取游標。連接 MySQL 服務器成功後，接下來要做的就是向數據庫服務器發送 SQL 語句，MySQL 會執行接收到的 SQL 並將執行結果通過網絡返回。要實現這項操作，需要先通過連接對象的`cursor`方法獲取游標（`Cursor`）對象。
3. 發出 SQL。通過游標對象的`execute`方法，我們可以向數據庫發出 SQL 語句。
4. 如果執行`insert`、`delete`或`update`操作，需要根據實際情況提交或回滾事務。因為創建連接時，默認開啟了事務環境，在操作完成後，需要使用連接對象的`commit`或`rollback`方法，實現事務的提交或回滾，`rollback`方法通常會放在異常捕獲代碼塊`except`中。如果執行`select`操作，需要通過游標對象抓取查詢的結果，對應的方法有三個，分別是：`fetchone`、`fetchmany`和`fetchall`。其中`fetchone`方法會抓取到一條記錄，並以元組或字典的方式返回；`fetchmany`和`fetchall`方法會抓取到多條記錄，以嵌套元組或列表裝字典的方式返回。
5. 關閉連接。在完成持久化操作後，請不要忘記關閉連接，釋放外部資源。我們通常會在`finally`代碼塊中使用連接對象的`close`方法來關閉連接。

### 代碼實操

下面，我們通過代碼實操的方式為大家演示上面說的五個步驟。

#### 插入數據

```Python
import pymysql

no = int(input('部門編號: '))
name = input('部門名稱: ')
location = input('部門所在地: ')

# 1. 創建連接（Connection）
conn = pymysql.connect(host='127.0.0.1', port=3306,
                       user='guest', password='Guest.618',
                       database='hrs', charset='utf8mb4')
try:
    # 2. 獲取游標對象（Cursor）
    with conn.cursor() as cursor:
        # 3. 通過游標對象向數據庫服務器發出SQL語句
        affected_rows = cursor.execute(
            'insert into `tb_dept` values (%s, %s, %s)',
            (no, name, location)
        )
        if affected_rows == 1:
            print('新增部門成功!!!')
    # 4. 提交事務（transaction）
    conn.commit()
except pymysql.MySQLError as err:
    # 4. 回滾事務
    conn.rollback()
    print(type(err), err)
finally:
    # 5. 關閉連接釋放資源
    conn.close()
```

> **說明**：上面的`127.0.0.1`稱為回環地址，它代表的是本機。下面的`guest`是我提前創建好的用戶，該用戶擁有對`hrs`數據庫的`insert`、`delete`、`update`和`select`權限。我們不建議大家在項目中直接使用`root`超級管理員賬號訪問數據庫，這樣做實在是太危險了。我們可以使用下面的命令創建名為`guest`的用戶並為其授權。
>
> ```SQL
> create user 'guest'@'%' identified by 'Guest.618';
> grant insert, delete, update, select on `hrs`.* to 'guest'@'%';
> ```

如果要插入大量數據，建議使用游標對象的`executemany`方法做批處理（一個`insert`操作後面跟上多組數據），大家可以嘗試向一張表插入10000條記錄，然後看看不使用批處理一條條的插入和使用批處理有什麼差別。游標對象的`executemany`方法第一個參數仍然是 SQL 語句，第二個參數可以是包含多組數據的列表或元組。

#### 刪除數據

```Python
import pymysql

no = int(input('部門編號: '))

# 1. 創建連接（Connection）
conn = pymysql.connect(host='127.0.0.1', port=3306,
                       user='guest', password='Guest.618',
                       database='hrs', charset='utf8mb4',
                       autocommit=True)
try:
    # 2. 獲取游標對象（Cursor）
    with conn.cursor() as cursor:
        # 3. 通過游標對象向數據庫服務器發出SQL語句
        affected_rows = cursor.execute(
            'delete from `tb_dept` where `dno`=%s',
            (no, )
        )
        if affected_rows == 1:
            print('刪除部門成功!!!')
finally:
    # 5. 關閉連接釋放資源
    conn.close()
```

> **說明**：如果不希望每次 SQL 操作之後手動提交或回滾事務，可以`connect`函數中加一個名為`autocommit`的參數並將它的值設置為`True`，表示每次執行 SQL 成功後自動提交。但是我們建議大家手動提交或回滾，這樣可以根據實際業務需要來構造事務環境。如果不願意捕獲異常並進行處理，可以在`try`代碼塊後直接跟`finally`塊，省略`except`意味著發生異常時，代碼會直接崩潰並將異常棧顯示在終端中。

#### 更新數據

```Python
import pymysql

no = int(input('部門編號: '))
name = input('部門名稱: ')
location = input('部門所在地: ')

# 1. 創建連接（Connection）
conn = pymysql.connect(host='127.0.0.1', port=3306,
                       user='guest', password='Guest.618',
                       database='hrs', charset='utf8mb4')
try:
    # 2. 獲取游標對象（Cursor）
    with conn.cursor() as cursor:
        # 3. 通過游標對象向數據庫服務器發出SQL語句
        affected_rows = cursor.execute(
            'update `tb_dept` set `dname`=%s, `dloc`=%s where `dno`=%s',
            (name, location, no)
        )
        if affected_rows == 1:
            print('更新部門信息成功!!!')
    # 4. 提交事務
    conn.commit()
except pymysql.MySQLError as err:
    # 4. 回滾事務
    conn.rollback()
    print(type(err), err)
finally:
    # 5. 關閉連接釋放資源
    conn.close()
```

#### 查詢數據

1. 查詢部門表的數據。

```Python
import pymysql

# 1. 創建連接（Connection）
conn = pymysql.connect(host='127.0.0.1', port=3306,
                       user='guest', password='Guest.618',
                       database='hrs', charset='utf8mb4')
try:
    # 2. 獲取游標對象（Cursor）
    with conn.cursor() as cursor:
        # 3. 通過游標對象向數據庫服務器發出SQL語句
        cursor.execute('select `dno`, `dname`, `dloc` from `tb_dept`')
        # 4. 通過游標對象抓取數據
        row = cursor.fetchone()
        while row:
            print(row)
            row = cursor.fetchone()
except pymysql.MySQLError as err:
    print(type(err), err)
finally:
    # 5. 關閉連接釋放資源
    conn.close()
```
>**說明**：上面的代碼中，我們通過構造一個`while`循環實現了逐行抓取查詢結果的操作。這種方式特別適合查詢結果有非常多行的場景。因為如果使用`fetchall`一次性將所有記錄抓取到一個嵌套元組中，會造成非常大的內存開銷，這在很多場景下並不是一個好主意。如果不願意使用`while`循環，還可以考慮使用`iter`函數構造一個迭代器來逐行抓取數據，有興趣的讀者可以自行研究。

2. 分頁查詢員工表的數據。

```Python
import pymysql

page = int(input('頁碼: '))
size = int(input('大小: '))

# 1. 創建連接（Connection）
con = pymysql.connect(host='127.0.0.1', port=3306,
                      user='guest', password='Guest.618',
                      database='hrs', charset='utf8')
try:
    # 2. 獲取游標對象（Cursor）
    with con.cursor(pymysql.cursors.DictCursor) as cursor:
        # 3. 通過游標對象向數據庫服務器發出SQL語句
        cursor.execute(
            'select `eno`, `ename`, `job`, `sal` from `tb_emp` order by `sal` desc limit %s,%s',
            ((page - 1) * size, size)
        )
        # 4. 通過游標對象抓取數據
        for emp_dict in cursor.fetchall():
            print(emp_dict)
finally:
    # 5. 關閉連接釋放資源
    con.close()
```

### 案例講解

下面我們為大家講解一個將數據庫表數據導出到 Excel 文件的例子，我們需要先安裝`openpyxl`三方庫，命令如下所示。

```Bash
pip install openpyxl
```

接下來，我們通過下面的代碼實現了將數據庫`hrs`中所有員工的編號、姓名、職位、月薪、補貼和部門名稱導出到一個 Excel 文件中。

```Python
import openpyxl
import pymysql

# 創建工作簿對象
workbook = openpyxl.Workbook()
# 獲得默認的工作表
sheet = workbook.active
# 修改工作表的標題
sheet.title = '員工基本信息'
# 給工作表添加表頭
sheet.append(('工號', '姓名', '職位', '月薪', '補貼', '部門'))
# 創建連接（Connection）
conn = pymysql.connect(host='127.0.0.1', port=3306,
                       user='guest', password='Guest.618',
                       database='hrs', charset='utf8mb4')
try:
    # 獲取游標對象（Cursor）
    with conn.cursor() as cursor:
        # 通過游標對象執行SQL語句
        cursor.execute(
            'select `eno`, `ename`, `job`, `sal`, coalesce(`comm`, 0), `dname` '
            'from `tb_emp` natural join `tb_dept`'
        )
        # 通過游標抓取數據
        row = cursor.fetchone()
        while row:
            # 將數據逐行寫入工作表中
            sheet.append(row)
            row = cursor.fetchone()
    # 保存工作簿
    workbook.save('hrs.xlsx')
except pymysql.MySQLError as err:
    print(err)
finally:
    # 關閉連接釋放資源
    conn.close()
```
