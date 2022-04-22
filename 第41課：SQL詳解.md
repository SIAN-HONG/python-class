## 第41課：SQL詳解

我們通常可以將 SQL 分為四類，分別是 DDL（數據定義語言）、DML（數據操作語言）、DQL（數據查詢語言）和 DCL（數據控制語言）。 DDL 主要用於創建、刪除、修改數據庫中的對象，比如創建、刪除和修改二維表，核心的關鍵字包括`create`、`drop`和`alter`；DML 主要負責數據的插入、刪除和更新，關鍵詞包括`insert`、`delete`和`update`；DQL 負責數據查詢，最重要的一個關鍵詞是`select`；DCL 通常用於授予和召回權限，核心關鍵詞是`grant`和`revoke`。

> **說明**：SQL 是不區分大小寫的語言，為了書寫和識別方便，下面的 SQL 都使用了小寫字母來書寫。

### DDL（數據定義語言）

下面我們來實現一個選課系統的數據庫，如下所示的 SQL 創建了名為`school`的數據庫和五張表，分別是學院表（`tb_college`）、學生表（`tb_student`）、教師表（`tb_teacher`）、課程表（`tb_course`）和選課記錄表（`tb_record`），其中學生和教師跟學院之間是多對一關係，課程跟老師之間也是多對一關係，學生和課程是多對多關係，選課記錄表就是維持學生跟課程多對多關係的中間表。

```SQL
-- 如果存在名為school的數據庫就刪除它
drop database if exists `school`;

-- 創建名為school的數據庫並設置默認的字符集和排序方式
create database `school` default character set utf8mb4 collate utf8mb4_general_ci;

-- 切換到school數據庫上下文環境
use `school`;

-- 創建學院表
create table `tb_college`
(
`col_id` int unsigned auto_increment comment '編號',
`col_name` varchar(50) not null comment '名稱',
`col_intro` varchar(500) default '' comment '介紹',
primary key (`col_id`)
) engine=innodb auto_increment=1 comment '學院表';

-- 創建學生表
create table `tb_student`
(
`stu_id` int unsigned not null comment '學號',
`stu_name` varchar(20) not null comment '姓名',
`stu_sex` boolean default 1 not null comment '性別',
`stu_birth` date not null comment '出生日期',
`stu_addr` varchar(255) default '' comment '籍貫',
`col_id` int unsigned not null comment '所屬學院',
primary key (`stu_id`),
constraint `fk_student_col_id` foreign key (`col_id`) references `tb_college` (`col_id`)
) engine=innodb comment '學生表';

-- 創建教師表
create table `tb_teacher`
(
`tea_id` int unsigned not null comment '工號',
`tea_name` varchar(20) not null comment '姓名',
`tea_title` varchar(10) default '助教' comment '職稱',
`col_id` int unsigned not null comment '所屬學院',
primary key (`tea_id`),
constraint `fk_teacher_col_id` foreign key (`col_id`) references `tb_college` (`col_id`)
) engine=innodb comment '老師表';

-- 創建課程表
create table `tb_course`
(
`cou_id` int unsigned not null comment '編號',
`cou_name` varchar(50) not null comment '名稱',
`cou_credit` int not null comment '學分',
`tea_id` int unsigned not null comment '授課老師',
primary key (`cou_id`),
constraint `fk_course_tea_id` foreign key (`tea_id`) references `tb_teacher` (`tea_id`)
) engine=innodb comment '課程表';

-- 創建選課記錄表
create table `tb_record`
(
`rec_id` bigint unsigned auto_increment comment '選課記錄號',
`stu_id` int unsigned not null comment '學號',
`cou_id` int unsigned not null comment '課程編號',
`sel_date` date not null comment '選課日期',
`score` decimal(4,1) comment '考試成績',
primary key (`rec_id`),
constraint `fk_record_stu_id` foreign key (`stu_id`) references `tb_student` (`stu_id`),
constraint `fk_record_cou_id` foreign key (`cou_id`) references `tb_course` (`cou_id`),
constraint `uk_record_stu_cou` unique (`stu_id`, `cou_id`)
) engine=innodb comment '選課記錄表';
```

上面的DDL有幾個地方需要強調一下：

- 創建數據庫時，我們通過`default character set utf8mb4`指定了數據庫默認使用的字符集為`utf8mb4`（最大`4`字節的`utf-8`編碼），我們推薦使用該字符集，它也是 MySQL 8.x 默認使用的字符集，因為它能夠支持國際化編碼，還可以存儲 Emoji 字符。可以通過下面的命令查看 MySQL 支持的字符集以及默認的排序規則。

  ```SQL
  show character set;
  ```

  ```
  +----------+---------------------------------+---------------------+--------+
  | Charset  | Description                     | Default collation   | Maxlen |
  +----------+---------------------------------+---------------------+--------+
  | big5     | Big5 Traditional Chinese        | big5_chinese_ci     |      2 |
  | dec8     | DEC West European               | dec8_swedish_ci     |      1 |
  | cp850    | DOS West European               | cp850_general_ci    |      1 |
  | hp8      | HP West European                | hp8_english_ci      |      1 |
  | koi8r    | KOI8-R Relcom Russian           | koi8r_general_ci    |      1 |
  | latin1   | cp1252 West European            | latin1_swedish_ci   |      1 |
  | latin2   | ISO 8859-2 Central European     | latin2_general_ci   |      1 |
  | swe7     | 7bit Swedish                    | swe7_swedish_ci     |      1 |
  | ascii    | US ASCII                        | ascii_general_ci    |      1 |
  | ujis     | EUC-JP Japanese                 | ujis_japanese_ci    |      3 |
  | sjis     | Shift-JIS Japanese              | sjis_japanese_ci    |      2 |
  | hebrew   | ISO 8859-8 Hebrew               | hebrew_general_ci   |      1 |
  | tis620   | TIS620 Thai                     | tis620_thai_ci      |      1 |
  | euckr    | EUC-KR Korean                   | euckr_korean_ci     |      2 |
  | koi8u    | KOI8-U Ukrainian                | koi8u_general_ci    |      1 |
  | gb2312   | GB2312 Simplified Chinese       | gb2312_chinese_ci   |      2 |
  | greek    | ISO 8859-7 Greek                | greek_general_ci    |      1 |
  | cp1250   | Windows Central European        | cp1250_general_ci   |      1 |
  | gbk      | GBK Simplified Chinese          | gbk_chinese_ci      |      2 |
  | latin5   | ISO 8859-9 Turkish              | latin5_turkish_ci   |      1 |
  | armscii8 | ARMSCII-8 Armenian              | armscii8_general_ci |      1 |
  | utf8     | UTF-8 Unicode| utf8_general_ci     |      3 |
  | ucs2     | UCS-2 Unicode                   | ucs2_general_ci     |      2 |
  | cp866    | DOS Russian                     | cp866_general_ci    |      1 |
  | keybcs2  | DOS Kamenicky Czech-Slovak      | keybcs2_general_ci  |      1 |
  | macce    | Mac Central European            | macce_general_ci    |      1 |
  | macroman | Mac West European               | macroman_general_ci |      1 |
  | cp852    | DOS Central European            | cp852_general_ci    |      1 |
  | latin7   | ISO 8859-13 Baltic              | latin7_general_ci   |      1 |
  | utf8mb4  | UTF-8 Unicode                   | utf8mb4_general_ci  |      4 |
  | cp1251   | Windows Cyrillic                | cp1251_general_ci   |      1 |
  | utf16    | UTF-16 Unicode                  | utf16_general_ci    |      4 |
  | utf16le  | UTF-16LE Unicode                | utf16le_general_ci  |      4 |
  | cp1256   | Windows Arabic                  | cp1256_general_ci   |      1 |
  | cp1257   | Windows Baltic                  | cp1257_general_ci   |      1 |
  | utf32    | UTF-32 Unicode                  | utf32_general_ci    |      4 |
  | binary   | Binary pseudo charset           | binary              |      1 |
  | geostd8  | GEOSTD8 Georgian                | geostd8_general_ci  |      1 |
  | cp932    | SJIS for Windows Japanese       | cp932_japanese_ci   |      2 |
  | eucjpms  | UJIS for Windows Japanese       | eucjpms_japanese_ci |      3 |
  | gb18030  | China National Standard GB18030 | gb18030_chinese_ci  |      4 |
  +----------+---------------------------------+---------------------+--------+
  41 rows in set (0.00 sec)
  ```

  如果要設置 MySQL 服務啟動時默認使用的字符集，可以修改MySQL的配置並添加以下內容。

  ```INI
  [mysqld]
  character-set-server=utf8
  ```

- 在創建表的時候，可以自行選擇底層的存儲引擎。 MySQL 支持多種存儲引擎，可以通過`show engines`命令進行查看。 MySQL 5.5 以後的版本默認使用的存儲引擎是 InnoDB，它是我們推薦大家使用的存儲引擎（因為更適合當下互聯網應用對高並發、性能以及事務支持等方面的需求），為了 SQL 語句的向下兼容性，我們可以在建表語句結束處右圓括號的後面通過`engine=innodb`來指定使用 InnoDB 存儲引擎。

  ```SQL
  show engines\G
  ```

  ```
  *************************** 1. row ***************************
        Engine: InnoDB
       Support: DEFAULT
       Comment: Supports transactions, row-level locking, and foreign keys
  Transactions: YES
            XA: YES
    Savepoints: YES
  *************************** 2. row ***************************
        Engine: MRG_MYISAM
       Support: YES
       Comment: Collection of identical MyISAM tables
  Transactions: NO
            XA: NO
    Savepoints: NO
  *************************** 3. row ***************************
        Engine: MEMORY
       Support: YES
       Comment: Hash based, stored in memory, useful for temporary tables
  Transactions: NO
            XA: NO
    Savepoints: NO
  *************************** 4. row ***************************
        Engine: BLACKHOLE
       Support: YES
       Comment: /dev/null storage engine (anything you write to it disappears)
  Transactions: NO
            XA: NO
    Savepoints: NO
  *************************** 5. row ***************************
        Engine: MyISAM
       Support: YES
       Comment: MyISAM storage engine
  Transactions: NO
            XA: NO
    Savepoints: NO
  *************************** 6. row ***************************
        Engine: CSV
       Support: YES
       Comment: CSV storage engine
  Transactions: NO
            XA: NO
    Savepoints: NO
  *************************** 7. row ***************************
        Engine: ARCHIVE
       Support: YES
       Comment: Archive storage engine
  Transactions: NO
            XA: NO
    Savepoints: NO
  *************************** 8. row ***************************
        Engine: PERFORMANCE_SCHEMA
       Support: YES
       Comment: Performance Schema
  Transactions: NO
            XA: NO
    Savepoints: NO
  *************************** 9. row ***************************
        Engine: FEDERATED
       Support: NO
       Comment: Federated MySQL storage engine
  Transactions: NULL
            XA: NULL
    Savepoints: NULL
  9 rows in set (0.00 sec)
  ```

  下面的表格對MySQL幾種常用的數據引擎進行了簡單的對比。

  | 特性         | InnoDB       | MRG_MYISAM | MEMORY | MyISAM |
  | ------------ | ------------ | ---------- | ------ | ------ |
  | 存儲限制     | 有           | 沒有       | 有     | 有     |
  | 事務         | 支持         |            |        |        |
  | 鎖機制       | 行鎖         | 表鎖       | 表鎖   | 表鎖   |
  | B樹索引      | 支持         | 支持       | 支持   | 支持   |
  | 哈希索引     |              |            | 支持   |        |
  | 全文檢索     | 支持（5.6+） |            |        | 支持   |
  | 集群索引     | 支持         |            |        |        |
  | 數據緩存     | 支持         |            | 支持   |        |
  | 索引緩存     | 支持         | 支持       | 支持   | 支持   |
  | 數據可壓縮   |              |            |        | 支持   |
  | 內存使用     | 高           | 低         | 中     | 低     |
  | 存儲空間使用 | 高           | 低         |        | 低     |
  | 批量插入性能 | 低           | 高         | 高     | 高     |
  | 是否支持外鍵 | 支持         ||        |        |

  通過上面的比較我們可以了解到，InnoDB 是唯一能夠支持外鍵、事務以及行鎖的存儲引擎，所以我們之前說它更適合互聯網應用，而且在較新版本的 MySQL 中，它也是默認使用的存儲引擎。

- 在定義表結構為每個字段選擇數據類型時，如果不清楚哪個數據類型更合適，可以通過 MySQL 的幫助系統來了解每種數據類型的特性、數據的長度和精度等相關信息。

  ```SQL
  ? data types
  ```

  ```
  You asked for help about help category: "Data Types"
  For more information, type 'help <item>', where <item> is one of the following
  topics:
     AUTO_INCREMENT
     BIGINT
     BINARY
     BIT
     BLOB
     BLOB DATA TYPE
     BOOLEAN
     CHAR
     CHAR BYTE
     DATE
     DATETIME
     DEC
     DECIMAL
     DOUBLE
     DOUBLE PRECISION
     ENUM
     FLOAT
     INT
     INTEGER
     LONGBLOB
     LONGTEXT
     MEDIUMBLOB
     MEDIUMINT
     MEDIUMTEXT
     SET DATA TYPE
     SMALLINT
     TEXT
     TIME
     TIMESTAMP
     TINYBLOB
     TINYINT
     TINYTEXT
     VARBINARY
     VARCHAR
     YEAR DATA TYPE
  ```

  ```SQL
  ? varchar
  ```

  ```
  Name: 'VARCHAR'
  Description:
  [NATIONAL] VARCHAR(M) [CHARACTER SET charset_name] [COLLATE
  collation_name]
  
  A variable-length string. M represents the maximum column length in
  characters. The range of M is 0 to 65,535. The effective maximum length
  of a VARCHAR is subject to the maximum row size (65,535 bytes, which is
  shared among all columns) and the character set used. For example, utf8
  characters can require up to three bytes per character, so a VARCHAR
  column that uses the utf8 character set can be declared to be a maximum
  of 21,844 characters. See
  http://dev.mysql.com/doc/refman/5.7/en/column-count-limit.html.
  
  MySQL stores VARCHAR values as a 1-byte or 2-byte length prefix plus
  data. The length prefix indicates the number of bytes in the value. A
  VARCHAR column uses one length byte if values require no more than 255
  bytes, two length bytes if values may require more than 255 bytes.
  
  *Note*:
  
  MySQL follows the standard SQL specification, and does not remove
  trailing spaces from VARCHAR values.
  
  VARCHAR is shorthand for CHARACTER VARYING. NATIONAL VARCHAR is the
  standard SQL way to define that a VARCHAR column should use some
  predefined character set. MySQL uses utf8 as this predefined character
  set. http://dev.mysql.com/doc/refman/5.7/en/charset-national.html.
  NVARCHAR is shorthand for NATIONAL VARCHAR.
  
  URL: http://dev.mysql.com/doc/refman/5.7/en/string-type-overview.html
  ```

  在數據類型的選擇上，保存字符串數據通常都使用`VARCHAR`和`CHAR`兩種類型，前者通常稱為變長字符串，而後者通常稱為定長字符串；對於 InnoDB 存儲引擎，行存儲格式沒有區分固定長度和可變長度列，因此`VARCHAR`類型和`CHAR`類型沒有本質區別，後者不一定比前者性能更好。如果要保存的很大字符串，可以使用`TEXT`類型；如果要保存很大的字節串，可以使用`BLOB`（二進制大對象）類型。在 MySQL 中，`TEXT`和`BLOB`又分別包括`TEXT`、`MEDIUMTEXT`、`LONGTEXT`和`BLOB`、`MEDIUMBLOB`、`LONGBLOB`三種不同的類型，它們主要的區別在於存儲數據的最大大小不同。保存浮點數可以用`FLOAT`或`DOUBLE`類型，`FLOAT`已經不推薦使用了，而且在 MySQL 後續的版本中可能會被移除掉。而保存定點數應該使用`DECIMAL`類型。如果要保存時間日期，`DATETIME`類型優於`TIMESTAMP`類型，因為前者能表示的時間日期範圍更大。

### DML（數據操作語言）

我們通過如下所示的 SQL 給上面創建的表添加數據。

```SQL
use school;

-- 插入學院數據
insert into `tb_college` 
    (`col_name`, `col_intro`) 
values 
    ('計算機學院', '計算機學院1958年設立計算機專業，1981年建立計算機科學系，1998年設立計算機學院，2005年5月，為了進一步整合教學和科研資源，學校決定，計算機學院和軟件學院行政班子合併統一運作、實行教學和學生管理獨立運行的模式。 學院下設三個系：計算機科學與技術系、物聯網工程系、計算金融系；兩個研究所：圖像圖形研究所、網絡空間安全研究院（2015年成立）；三個教學實驗中心：計算機基礎教學實驗中心、IBM技術中心和計算機專業實驗中心。'),
    ('外國語學院', '外國語學院設有7個教學單位，6個文理兼收的本科專業；擁有1個一級學科博士授予點，3個二級學科博士授予點，5個一級學科碩士學位授權點，5個二級學科碩士學位授權點，5個碩士專業授權領域，同時還有2個碩士專業學位（MTI）專業；有教職員工210餘人，其中教授、副教授80餘人，教師中獲得中國國內外名校博士學位和正在職攻讀博士學位的教師比例佔專任教師的60%以上。'),
    ('經濟管理學院', '經濟學院前身是創辦於1905年的經濟科；已故經濟學家彭迪先、張與九、蔣學模、胡寄窗、陶大鏞、胡代光，以及當代學者劉詩白等曾先後在此任教或學習。');

-- 插入學生數據
insert into `tb_student` 
    (`stu_id`, `stu_name`, `stu_sex`, `stu_birth`, `stu_addr`, `col_id`) 
values
    (1001, '楊過', 1, '1990-3-4', '湖南長沙', 1),
    (1002, '任我行', 1, '1992-2-2', '湖南長沙', 1),
    (1033, '王語嫣', 0, '1989-12-3', '四川成都', 1),
    (1572, '岳不群', 1, '1993-7-19', '陝西咸陽', 1),
    (1378, '紀嫣然', 0, '1995-8-12', '四川綿陽', 1),
    (1954, '林平之', 1, '1994-9-20', '福建莆田', 1),
    (2035, '東方不敗', 1, '1988-6-30', null, 2),
    (3011, '林震南', 1, '1985-12-12', '福建莆田', 3),
    (3755, '項少龍', 1, '1993-1-25', '四川成都', 3),
    (3923, '楊不悔', 0, '1985-4-17', '四川成都', 3);

-- 插入老師數據
insert into `tb_teacher` 
    (`tea_id`, `tea_name`, `tea_title`, `col_id`) 
values 
    (1122, '張三豐', '教授', 1),
    (1133, '宋遠橋', '副教授', 1),
    (1144, '楊逍', '副教授', 1),
    (2255, '范遙', '副教授', 2),
    (3366, '韋一笑', default, 3);

-- 插入課程數據
insert into `tb_course` 
    (`cou_id`, `cou_name`, `cou_credit`, `tea_id`) 
values 
    (1111, 'Python程序設計', 3, 1122),
    (2222, 'Web前端開發', 2, 1122),
    (3333, '操作系統', 4, 1122),
    (4444, '計算機網絡', 2, 1133),
    (5555, '編譯原理', 4, 1144),
    (6666, '算法和數據結構', 3, 1144),
    (7777, '經貿法語', 3, 2255),
    (8888, '成本會計', 2, 3366),
    (9999, '審計學', 3, 3366);

-- 插入選課數據
insert into `tb_record` 
    (`stu_id`, `cou_id`, `sel_date`, `score`) 
values 
    (1001, 1111, '2017-09-01', 95),
    (1001, 2222, '2017-09-01', 87.5),
    (1001, 3333, '2017-09-01', 100),
    (1001, 4444, '2018-09-03', null),
    (1001, 6666, '2017-09-02', 100),
    (1002, 1111, '2017-09-03', 65),
    (1002, 5555, '2017-09-01', 42),
    (1033, 1111, '2017-09-03', 92.5),
    (1033, 4444, '2017-09-01', 78),
    (1033, 5555, '2017-09-01', 82.5),
    (1572, 1111, '2017-09-02', 78),
    (1378, 1111, '2017-09-05', 82),
    (1378, 7777, '2017-09-02', 65.5),
    (2035, 7777, '2018-09-03', 88),
    (2035, 9999, '2019-09-02', null),
    (3755, 1111, '2019-09-02', null),
    (3755, 8888, '2019-09-02', null),
    (3755, 9999, '2017-09-01', 92);
```

> **注意**：上面的`insert`語句使用了批處理的方式來插入數據，這種做法插入數據的效率比較高。

### DQL（數據查詢語言）

接下來，我們完成如下所示的查詢。

```SQL
-- 查詢所有學生的所有信息
select * from `tb_student`;

-- 查詢學生的學號、姓名和籍貫(投影)
select `stu_id`, `stu_name`, `stu_addr` from `tb_student`;

-- 查詢所有課程的名稱及學分(投影和別名)
select `cou_name` as 課程名稱, `cou_credit` as 學分 from `tb_course`;

-- 查詢所有女學生的姓名和出生日期(篩選)
select `stu_name`, `stu_birth` from `tb_student` where `stu_sex`=0;

-- 查詢籍貫為“四川成都”的女學生的姓名和出生日期(篩選)
select `stu_name`, `stu_birth` from `tb_student` where `stu_sex`=0 and `stu_addr`='四川成都';

-- 查詢籍貫為“四川成都”或者性別為“女生”的學生
select `stu_name`, `stu_birth` from `tb_student` where `stu_sex`=0 or `stu_addr`='四川成都';

-- 查詢所有80後學生的姓名、性別和出生日期(篩選)
select `stu_name`, `stu_sex`, `stu_birth` from `tb_student` 
where `stu_birth`>='1980-1-1' and `stu_birth`<='1989-12-31';

select `stu_name`, `stu_sex`, `stu_birth` from `tb_student` 
where `stu_birth` between '1980-1-1' and '1989-12-31';

-- 補充：將表示性別的 1 和 0 處理成 “男” 和 “女”
select 
    `stu_name` as 姓名, 
    if(`stu_sex`, '男', '女') as 性別, 
    `stu_birth` as 出生日期
from `tb_student` 
where `stu_birth` between '1980-1-1' and '1989-12-31';

select 
    `stu_name` as 姓名, 
    case `stu_sex` when 1 then '男' else '女' end as 性別, 
    `stu_birth` as 出生日期
from `tb_student` 
where `stu_birth` between '1980-1-1' and '1989-12-31';

-- 查詢學分大於2的課程的名稱和學分(篩選)
select `cou_name`, `cou_credit` from `tb_course` where `cou_credit`>2;

-- 查詢學分是奇數的課程的名稱和學分(篩選)
select `cou_name`, `cou_credit` from `tb_course` where `cou_credit`%2<>0;

select `cou_name`, `cou_credit` from `tb_course` where `cou_credit` mod 2<>0;

-- 查詢選擇選了1111的課程考試成績在90分以上的學生學號(篩選)
select `stu_id` from `tb_record` where `cou_id`=1111 and `score`>90;

-- 查詢名字叫“楊過”的學生的姓名和性別
select `stu_name`, `stu_sex` from `tb_student` where `stu_name`='楊過';
    
-- 查詢姓“楊”的學生姓名和性別(模糊)
-- % - 通配符（wildcard），它可以匹配0個或任意多個字符
select `stu_name`, `stu_sex` from `tb_student` where `stu_name` like '楊%';

-- 查詢姓“楊”名字兩個字的學生姓名和性別(模糊)
-- _ - 通配符（wildcard），它可以精確匹配一個字符
select `stu_name`, `stu_sex` from `tb_student` where `stu_name` like '楊_';

-- 查詢姓“楊”名字三個字的學生姓名和性別(模糊)
select `stu_name`, `stu_sex` from `tb_student` where `stu_name` like '楊__';

-- 查詢名字中有“不”字或“嫣”字的學生的姓名(模糊)
select `stu_name` from `tb_student` where `stu_name` like '%不%' or `stu_name` like '%嫣%';

-- 將“岳不群”改名為“岳不嫣”，比較下面兩個查詢的區別
update `tb_student` set `stu_name`='岳不嫣' where `stu_id`=1572;

select `stu_name` from `tb_student` where `stu_name` like '%不%'
union 
select `stu_name` from `tb_student` where `stu_name` like '%嫣%';

select `stu_name` from `tb_student` where `stu_name` like '%不%'
union all 
select `stu_name` from `tb_student` where `stu_name` like '%嫣%';

-- 查詢姓“楊”或姓“林”名字三個字的學生的姓名(正則表達式模糊查詢)
select `stu_name` from `tb_student` where `stu_name` regexp '[楊林].{2}';

-- 查詢沒有錄入籍貫的學生姓名(空值處理)
select `stu_name` from `tb_student` where `stu_addr` is null;

select `stu_name` from `tb_student` where `stu_addr` <=> null;

-- 查詢錄入了籍貫的學生姓名(空值處理)
select `stu_name` from `tb_student` where `stu_addr` is not null;

-- 下面的查詢什麼也查不到，三值邏輯 --> true / false / unknown
select `stu_name` from `tb_student` where `stu_addr`=null or `stu_addr`<>null;

-- 查詢學生選課的所有日期(去重)
select distinct `sel_date` from `tb_record`;

-- 查詢學生的籍貫(去重)
select distinct `stu_addr` from `tb_student` where `stu_addr` is not null;

-- 查詢男學生的姓名和生日按年齡從大到小排列(排序)
-- 升序：從小到大 - asc，降序：從大到小 - desc
select `stu_id`, `stu_name`, `stu_birth` from `tb_student` 
where `stu_sex`=1 order by `stu_birth` asc, `stu_id` desc;

-- 補充：將上面的生日換算成年齡(日期函數、數值函數)
select 
    `stu_id` as 學號,
    `stu_name` as 姓名, 
    floor(datediff(curdate(), `stu_birth`)/365) as 年齡
from `tb_student` 
where `stu_sex`=1 order by 年齡 desc, `stu_id` desc;

-- 查詢年齡最大的學生的出生日期(聚合函數)
select min(`stu_birth`) from `tb_student`;

-- 查詢年齡最小的學生的出生日期(聚合函數)
select max(`stu_birth`) from `tb_student`;

-- 查詢編號為1111的課程考試成績的最高分(聚合函數)
select max(`score`) from `tb_record` where `cou_id`=1111;

-- 查詢學號為1001的學生考試成績的最低分(聚合函數)
select min(`score`) from `tb_record` where `stu_id`=1001;

-- 查詢學號為1001的學生考試成績的平均分(聚合函數)
select avg(`score`) from `tb_record` where `stu_id`=1001;

select sum(`score`) / count(`score`) from `tb_record` where `stu_id`=1001;

-- 查詢學號為1001的學生考試成績的平均分，如果有null值，null值算0分(聚合函數)
select sum(`score`) / count(*) from `tb_record` where `stu_id`=1001;

select avg(ifnull(`score`, 0)) from `tb_record` where `stu_id`=1001;

-- 查詢學號為1001的學生考試成績的標準差(聚合函數)
select std(`score`), variance(`score`) from `tb_record` where `stu_id`=1001;

-- 查詢男女學生的人數(分組和聚合函數)
select 
    case `stu_sex` when 1 then '男' else '女' end as 性別,
    count(*) as 人數
from `tb_student` group by`stu_sex`;

-- 查詢每個學院學生人數(分組和聚合函數)
select 
    `col_id` as 學院,
    count(*) as 人數
from `tb_student` group by `col_id` with rollup;

-- 查詢每個學院男女學生人數(分組和聚合函數)
select 
    `col_id` as 學院,
    if(`stu_sex`, '男', '女') as 性別,
    count(*) as 人數
from `tb_student` group by `col_id`, `stu_sex`;

-- 查詢每個學生的學號和平均成績(分組和聚合函數)
select 
    `stu_id`, 
    round(avg(`score`), 1) as avg_score
from `tb_record` group by `stu_id`;

-- 查詢平均成績大於等於90分的學生的學號和平均成績
-- 分組以前的篩選使用where子句，分組以後的篩選使用having子句
select 
    `stu_id`, 
    round(avg(`score`), 1) as avg_score
from `tb_record`
group by `stu_id` having avg_score>=90;

-- 查詢1111、2222、3333三門課程平均成績大於等於90分的學生的學號和平均成績
select 
    `stu_id`, 
    round(avg(`score`), 1) as avg_score
from `tb_record` where `cou_id` in (1111, 2222, 3333)
group by `stu_id` having avg_score>=90;

-- 查詢年齡最大的學生的姓名(子查詢/嵌套查詢)
-- 嵌套查詢：把一個select的結果作為另一個select的一部分來使用
select `stu_name` from `tb_student` 
where `stu_birth`=(
    select min(`stu_birth`) from `tb_student`
);

-- 查詢選了兩門以上的課程的學生姓名(子查詢/分組條件/集合運算)
select `stu_name` from `tb_student` 
where `stu_id` in (
    select `stu_id` from `tb_record` 
    group by `stu_id` having count(*)>2
);

-- 查詢學生的姓名、生日和所在學院名稱
select `stu_name`, `stu_birth`, `col_name` 
from `tb_student`, `tb_college` 
where `tb_student`.`col_id`=`tb_college`.`col_id`;

select `stu_name`, `stu_birth`, `col_name` 
from `tb_student` inner join `tb_college` 
on `tb_student`.`col_id`=`tb_college`.`col_id`;

select `stu_name`, `stu_birth`, `col_name` 
from `tb_student` natural join `tb_college`;

-- 查詢學生姓名、課程名稱以及成績(連接查詢/聯結查詢)
select `stu_name`, `cou_name`, `score` 
from `tb_student`, `tb_course`, `tb_record` 
where `tb_student`.`stu_id`=`tb_record`.`stu_id` 
and `tb_course`.`cou_id`=`tb_record`.`cou_id` 
and `score` is not null;

select `stu_name`, `cou_name`, `score` from `tb_student` 
inner join `tb_record` on `tb_student`.`stu_id`=`tb_record`.`stu_id` 
inner join `tb_course` on `tb_course`.`cou_id`=`tb_record`.`cou_id` 
where `score` is not null;

select `stu_name`, `cou_name`, `score` from `tb_student` 
natural join `tb_record` 
natural join `tb_course`
where `score` is not null;

-- 補充：上面的查詢結果取前5條數據(分頁查詢)
select `stu_name`, `cou_name`, `score` 
from `tb_student`, `tb_course`, `tb_record` 
where `tb_student`.`stu_id`=`tb_record`.`stu_id` 
and `tb_course`.`cou_id`=`tb_record`.`cou_id` 
and `score` is not null 
order by `score` desc 
limit 0,5;

-- 補充：上面的查詢結果取第6-10條數據(分頁查詢)
select `stu_name`, `cou_name`, `score` 
from `tb_student`, `tb_course`, `tb_record` 
where `tb_student`.`stu_id`=`tb_record`.`stu_id` 
and `tb_course`.`cou_id`=`tb_record`.`cou_id` 
and `score` is not null 
order by `score` desc 
limit 5 offset 5;

-- 補充：上面的查詢結果取第11-15條數據(分頁查詢)
select `stu_name`, `cou_name`, `score` 
from `tb_student`, `tb_course`, `tb_record` 
where `tb_student`.`stu_id`=`tb_record`.`stu_id` 
and `tb_course`.`cou_id`=`tb_record`.`cou_id` 
and `score` is not null 
order by `score` desc 
limit 5 offset 10;

-- 查詢選課學生的姓名和平均成績(子查詢和連接查詢)
select `stu_name`, `avg_score` 
from `tb_student` inner join (
    select `stu_id` as `sid`, round(avg(`score`), 1) as avg_score 
    from `tb_record` group by `stu_id`
) as `t2` on `stu_id`=`sid`;

-- 查詢學生的姓名和選課的數量
select `stu_name`, `total` from `tb_student` as `t1`
inner join (
    select `stu_id`, count(*) as `total`
    from `tb_record` group by `stu_id`
) as `t2` on `t1`.`stu_id`=`t2`.`stu_id`;

-- 查詢每個學生的姓名和選課數量(左外連接和子查詢)
-- 左外連接：左表（寫在join左邊的表）的每條記錄都可以查出來，不滿足連表條件的地方填充null。
select `stu_name`, coalesce(`total`, 0) as `total`
from `tb_student` as `t1`
left outer join (
    select `stu_id`, count(*) as `total`
    from `tb_record` group by `stu_id`
) as `t2` on `t1`.`stu_id`=`t2`.`stu_id`;

-- 修改選課記錄表，去掉 stu_id 列的外鍵約束
alter table `tb_record` drop foreign key `fk_record_stu_id`;

-- 插入兩條新紀錄（注意：沒有學號為 5566 的學生）
insert into `tb_record` 
values
    (default, 5566, 1111, '2019-09-02', 80),
    (default, 5566, 2222, '2019-09-02', 70);

-- 右外連接：右表（寫在join右邊的表）的每條記錄都可以查出來，不滿足連表條件的地方填充null。
select `stu_name`, `total` from `tb_student` as `t1`
right outer join (
    select `stu_id`, count(*) as `total`
    from `tb_record` group by `stu_id`
) as `t2` on `t1`.`stu_id`=`t2`.`stu_id`;

-- 全外連接：左表和右表的每條記錄都可以查出來，不滿足連表條件的地方填充null。
-- 說明：MySQL不支持全外連接，所以用左外連接和右外連接的並集來表示。
select `stu_name`, `total`
from `tb_student` as `t1`
left outer join (
    select `stu_id`, count(*) as `total`
    from `tb_record` group by `stu_id`
) as `t2` on `t1`.`stu_id`=`t2`.`stu_id`
union 
select `stu_name`, `total` from `tb_student` as `t1`
right outer join (
    select `stu_id`, count(*) as `total`
    from `tb_record` group by `stu_id`
) as `t2` on `t1`.`stu_id`=`t2`.`stu_id`;
```

上面的DQL有幾個地方需要加以說明：

1. MySQL目前的版本不支持全外連接，上面我們通過`union`操作，將左外連接和右外連接的結果求並集實現全外連接的效果。大家可以通過下面的圖來加深對連表操作的認識。

   <img src="https://gitee.com/jackfrued/mypic/raw/master/20211121135117.png" style="zoom:50%">

2. MySQL 中支持多種類型的運算符，包括：算術運算符（`+`、`-`、`*`、`/`、`%`）、比較運算符（`=`、`<>`、`<=>`、`<`、`<=`、`>`、`>=`、`BETWEEN...AND..`.、`IN`、`IS NULL`、`IS NOT NULL`、`LIKE`、`RLIKE`、`REGEXP`）、邏輯運算符（`NOT`、`AND`、`OR`、`XOR`）和位運算符（`&`、`|`、`^`、`~`、`>>`、`<<`），我們可以在 DML 中使用這些運算符處理數據。

3. 在查詢數據時，可以在`SELECT`語句及其子句（如`WHERE`子句、`ORDER BY`子句、`HAVING`子句等）中使用函數，這些函數包括字符串函數、數值函數、時間日期函數、流程函數等，如下面的表格所示。

   常用字符串函數。

   | 函數                        | 功能                                                  |
   | --------------------------- | ----------------------------------------------------- |
   | `CONCAT`                    | 將多個字符串連接成一個字符串                          |
   | `FORMAT`                    | 將數值格式化成字符串並指定保留幾位小數                |
   | `FROM_BASE64` / `TO_BASE64` | BASE64解碼/編碼                                       |
   | `BIN` / `OCT` / `HEX`       | 將數值轉換成二進制/八進制/十六進製字符串              |
   | `LOCATE`                    | 在字符串中查找一個子串的位置                          |
   | `LEFT` / `RIGHT`            | 返回一個字符串左邊/右邊指定長度的字符                 |
   | `LENGTH` / `CHAR_LENGTH`    | 返回字符串的長度以字節/字符為單位                     |
   | `LOWER` / `UPPER`           | 返回字符串的小寫/大寫形式                             |
   | `LPAD` / `RPAD`             | 如果字符串的長度不足，在字符串左邊/右邊填充指定的字符 |
   | `LTRIM` / `RTRIM`           | 去掉字符串前面/後面的空格                             |
   | `ORD` / `CHAR`              | 返回字符對應的編碼/返回編碼對應的字符                 |
   | `STRCMP`                    | 比較字符串，返回-1、0、1分別表示小於、等於、大於      |
   | `SUBSTRING`                 | 返回字符串指定範圍的子串                              |

   常用數值函數。

   | 函數                                                     | 功能                               |
   | -------------------------------------------------------- | ---------------------------------- |
   | `ABS`                                                    | 返回一個數的絕度值                 |
   | `CEILING` / `FLOOR`                                      | 返回一個數上取整/下取整的結果      |
   | `CONV`                                                   | 將一個數從一種進制轉換成另一種進制 |
   | `CRC32`                                                  | 計算循環冗餘校驗碼                 |
   | `EXP` / `LOG` / `LOG2` / `LOG10`                         | 計算指數/對數                      |
   | `POW`                                                    | 求冪                               |
   | `RAND`                                                   | 返回[0,1)範圍的隨機數              |
   | `ROUND`                                                  | 返回一個數四捨五入後的結果         |
   | `SQRT`                                                   | 返回一個數的平方根                 |
   | `TRUNCATE`                                               | 截斷一個數到指定的精度             |
   | `SIN` / `COS` / `TAN` / `COT` / `ASIN` / `ACOS` / `ATAN` | 三角函數                           |

   常用時間日期函數。

   | 函數                          | 功能                                  |
   | ----------------------------- | ------------------------------------- |
   | `CURDATE` / `CURTIME` / `NOW` | 獲取當前日期/時間/日期和時間          |
   | `ADDDATE` / `SUBDATE`         | 將兩個日期表達式相加/相減並返回結果   |
   | `DATE` / `TIME`               | 從字符串中獲取日期/時間               |
   | `YEAR` / `MONTH` / `DAY`      | 從日期中獲取年/月/日                  |
   | `HOUR` / `MINUTE` / `SECOND`  | 從時間中獲取時/分/秒                  |
   | `DATEDIFF` / `TIMEDIFF`       | 返回兩個時間日期表達式相差多少天/小時 |
   | `MAKEDATE` / `MAKETIME`       | 製造一個日期/時間                     |

   常用流程函數。

   | 函數     | 功能                                             |
   | -------- | ------------------------------------------------ |
   | `IF`     | 根據條件是否成立返回不同的值                     |
   | `IFNULL` | 如果為NULL則返回指定的值否則就返回本身           |
   | `NULLIF` | 兩個表達式相等就返回NULL否則返回第一個表達式的值 |

   其他常用函數。

   | 函數                       | 功能                          |
   | -------------------------- | ----------------------------- |
   | `MD5` / `SHA1` / `SHA2`    | 返回字符串對應的哈希摘要      |
   | `CHARSET` / `COLLATION`    | 返回字符集/校對規則           |
   | `USER` / `CURRENT_USER`    | 返回當前用戶                  |
   | `DATABASE`                 | 返回當前數據庫名              |
   | `VERSION`                  | 返回當前數據庫版本            |
   | `FOUND_ROWS` / `ROW_COUNT` | 返回查詢到的行數/受影響的行數 |
   | `LAST_INSERT_ID`           | 返回最後一個自增主鍵的值      |
   | `UUID` / `UUID_SHORT`      | 返回全局唯一標識符            |

### DCL（數據控制語言）

數據控制語言用於給指定的用戶授權或者從召回指定用戶的指定權限，這組操作對數據庫管理員來說比較重要，將一個用戶的權限最小化（剛好夠用）是非常重要的，對數據庫的安全至關重要。

```SQL
-- 創建名為 wangdachui 的賬號並為其指定口令，允許該賬號從任意主機訪問
create user 'wangdachui'@'%' identified by '123456';

-- 授權 wangdachui 可以對名為school的數據庫執行 select 和 insert 操作
grant select, insert on `school`.* to 'wangdachui'@'%';

-- 召回 wangdachui 對school數據庫的 insert 權限
revoke insert on `school`.* from 'wangdachui'@'%';
```

> **說明**：創建一個可以允許任意主機登錄並且具有超級管理員權限的用戶在現實中並不是一個明智的決定，因為一旦該賬號的口令洩露或者被破解，數據庫將會面臨災難級的風險。
