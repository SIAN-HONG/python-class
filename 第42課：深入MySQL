## 第42課：深入MySQL

### 索引

索引是關係型數據庫中用來提升查詢性能最為重要的手段。關係型數據庫中的索引就像一本書的目錄，我們可以想像一下，如果要從一本書中找出某個知識點，但是這本書沒有目錄，這將是意見多麼可怕的事情！我們估計得一篇一篇的翻下去，才能確定這個知識點到底在什麼位置。創建索引雖然會帶來存儲空間上的開銷，就像一本書的目錄會佔用一部分篇幅一樣，但是在犧牲空間後換來的查詢時間的減少也是非常顯著的。

MySQL 數據庫中所有數據類型的列都可以被索引。對於MySQL 8.0 版本的 InnoDB 存儲引擎來說，它支持三種類型的索引，分別是 B+ 樹索引、全文索引和 R 樹索引。這裡，我們只介紹使用得最為廣泛的 B+ 樹索引。使用 B+ 樹的原因非常簡單，因為它是目前在基於磁盤進行海量數據存儲和排序上最有效率的數據結構。 B+ 樹是一棵[平衡樹](https://zh.wikipedia.org/zh-cn/%E5%B9%B3%E8%A1%A1%E6%A0%91)，樹的高度通常為3或4，但是卻可以保存從百萬級到十億級的數據，而從這些數據裡面查詢一條數據，只需要3次或4次 I/O 操作。

B+ 樹由根節點、中間節點和葉子節點構成，其中葉子節點用來保存排序後的數據。由於記錄在索引上是排序過的，因此在一個葉子節點內查找數據時可以使用二分查找，這種查找方式效率非常的高。當數據很少的時候，B+ 樹只有一個根節點，數據也就保存在根節點上。隨著記錄越來越多，B+ 樹會發生分裂，根節點不再保存數據，而是提供了訪問下一層節點的指針，幫助快速確定數據在哪個葉子節點上。

在創建二維表時，我們通常都會為表指定主鍵列，主鍵列上默認會創建索引，而對於 MySQL InnoDB 存儲引擎來說，因為它使用的是索引組織表這種數據存儲結構，所以主鍵上的索引就是整張表的數據，而這種索引我們也將其稱之為**聚集索引**（clustered index）。很顯然，一張表只能有一個聚集索引，否則表的數據豈不是要保存多次。我們自己創建的索引都是二級索引（secondary index），更常見的叫法是**非聚集索引**（non-clustered index）。通過我們自定義的非聚集索引只能定位記錄的主鍵，在獲取數據時可能需要再通過主鍵上的聚集索引進行查詢，這種現象稱為“回表”，因此通過非聚集索引檢索數據通常比使用聚集索引檢索數據要慢。

接下來我們通過一個簡單的例子來說明索引的意義，比如我們要根據學生的姓名來查找學生，這個場景在實際開發中應該經常遇到，就跟通過商品名稱查找商品是一個道理。我們可以使用 MySQL 的`explain`關鍵字來查看 SQL 的執行計劃（數據庫執行 SQL 語句的具體步驟）。

```SQL
explain select * from tb_student where stuname='林震南'\G
```

```
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb_student
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 11
     filtered: 10.00
        Extra: Using where
1 row in set, 1 warning (0.00 sec)
```

在上面的 SQL 執行計劃中，有幾項值得我們關注：

1. `select_type`：查詢的類型。
    - `SIMPLE`：簡單 SELECT，不需要使用 UNION 操作或子查詢。
    - `PRIMARY`：如果查詢包含子查詢，最外層的 SELECT 被標記為 PRIMARY。
    - `UNION`：UNION 操作中第二個或後面的 SELECT 語句。
    - `SUBQUERY`：子查詢中的第一個 SELECT。
    - `DERIVED`：派生表的 SELECT 子查詢。
2. `table`：查詢對應的表。
3. `type`：MySQL 在表中找到滿足條件的行的方式，也稱為訪問類型，包括：`ALL`（全表掃描）、`index`（索引全掃描，只遍歷索引樹）、`range`（索引範圍掃描）、`ref`（非唯一索引掃描）、`eq_ref`（唯一索引掃描）、`const` / `system`（常量級查詢）、`NULL`（不需要訪問表或索引）。在所有的訪問類型中，很顯然 ALL 是性能最差的，它代表的全表掃描是指要掃描表中的每一行才能找到匹配的行。
4. `possible_keys`：MySQL 可以選擇的索引，但是**有可能不會使用**。
5. `key`：MySQL 真正使用的索引，如果為`NULL`就表示沒有使用索引。
6. `key_len`：使用的索引的長度，在不影響查詢的情況下肯定是長度越短越好。
7. `rows`：執行查詢需要掃描的行數，這是一個**預估值**。
8. `extra`：關於查詢額外的信息。
    - `Using filesort`：MySQL 無法利用索引完成排序操作。
    - `Using index`：只使用索引的信息而不需要進一步查表來獲取更多的信息。
    - `Using temporary`：MySQL 需要使用臨時表來存儲結果集，常用於分組和排序。
    - `Impossible where`：`where`子句會導致沒有符合條件的行。
    - `Distinct`：MySQL 發現第一個匹配行後，停止為當前的行組合搜索更多的行。
    - `Using where`：查詢的列未被索引覆蓋，篩選條件並不是索引的前導列。

從上面的執行計劃可以看出，當我們通過學生名字查詢學生時實際上是進行了全表掃描，不言而喻這個查詢性能肯定是非常糟糕的，尤其是在表中的行很多的時候。如果我們需要經常通過學生姓名來查詢學生，那麼就應該在學生姓名對應的列上創建索引，通過索引來加速查詢。

```SQL
create index idx_student_name on tb_student(stuname);
```

再次查看剛才的 SQL 對應的執行計劃。

```SQL
explain select * from tb_student where stuname='林震南'\G
```

```
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb_student
   partitions: NULL
         type: ref
possible_keys: idx_student_name
          key: idx_student_name
      key_len: 62
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
1 row in set, 1 warning (0.00 sec)
```

可以注意到，在對學生姓名創建索引後，剛才的查詢已經不是全表掃描而是基於索引的查詢，而且掃描的行只有唯一的一行，這顯然大大的提升了查詢的性能。 MySQL 中還允許創建前綴索引，即對索引字段的前N個字符創建索引，這樣的話可以減少索引佔用的空間（但節省了空間很有可能會浪費時間，**時間和空間是不可調和的矛盾**），如下所示。

```SQL
create index idx_student_name_1 on tb_student(stuname(1));
```

上面的索引相當於是根據學生姓名的第一個字來創建的索引，我們再看看 SQL 執行計劃。

```SQL
explain select * from tb_student where stuname='林震南'\G
```

```
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb_student
   partitions: NULL
         type: ref
possible_keys: idx_student_name
          key: idx_student_name
      key_len: 5
          ref: const
         rows: 2
     filtered: 100.00
        Extra: Using where
1 row in set, 1 warning (0.00 sec)
```

不知道大家是否注意到，這一次掃描的行變成了2行，因為學生表中有兩個姓“林”的學生，我們只用姓名的第一個字作為索引的話，在查詢時通過索引就會找到這兩行。

如果要刪除索引，可以使用下面的SQL。

```SQL
alter table tb_student drop index idx_student_name;
```

或者

```SQL
drop index idx_student_name on tb_student;
```

在創建索引時，我們還可以使用複合索引、函數索引（MySQL 5.7 開始支持），用好複合索引實現**索引覆蓋**可以減少不必要的排序和回表操作，這樣就會讓查詢的性能成倍的提升，有興趣的讀者可以自行研究。

我們簡單的為大家總結一下索引的設計原則：

1. **最適合**索引的列是出現在**WHERE子句**和連接子句中的列。
2. 索引列的基數越大（取值多、重複值少），索引的效果就越好。
3. 使用**前綴索引**可以減少索引佔用的空間，內存中可以緩存更多的索引。
4. **索引不是越多越好**，雖然索引加速了讀操作（查詢），但是寫操作（增、刪、改）都會變得更慢，因為數據的變化會導致索引的更新，就如同書籍章節的增刪需要更新目錄一樣。
5. 使用 InnoDB 存儲引擎時，表的普通索引都會保存主鍵的值，所以**主鍵要盡可能選擇較短的數據類型**，這樣可以有效的減少索引佔用的空間，提升索引的緩存效果。

最後，還有一點需要說明，InnoDB 使用的 B-tree 索引，數值類型的列除了等值判斷時索引會生效之外，使用`>`、`<`、`>=`、`<=`、`BETWEEN...AND... `、`<>`時，索引仍然生效；對於字符串類型的列，如果使用不以通配符開頭的模糊查詢，索引也是起作用的，但是其他的情況會導致索引失效，這就意味著很有可能會做全表查詢。

### 視圖

視圖是關係型數據庫中將一組查詢指令構成的結果集組合成可查詢的數據表的對象。簡單的說，視圖就是虛擬的表，但與數據表不同的是，數據表是一種實體結構，而視圖是一種虛擬結構，你也可以將視圖理解為保存在數據庫中被賦予名字的 SQL 語句。

使用視圖可以獲得以下好處：

1. 可以將實體數據表隱藏起來，讓外部程序無法得知實際的數據結構，讓訪問者可以使用表的組成部分而不是整個表，降低數據庫被攻擊的風險。
2. 在大多數的情況下視圖是只讀的（更新視圖的操作通常都有諸多的限制），外部程序無法直接透過視圖修改數據。
3. 重用 SQL 語句，將高度複雜的查詢包裝在視圖表中，直接訪問該視圖即可取出需要的數據；也可以將視圖視為數據表進行連接查詢。
4. 視圖可以返回與實體數據表不同格式的數據，在創建視圖的時候可以對數據進行格式化處理。

創建視圖。

```SQL
-- 創建視圖
create view `vw_avg_score` 
as 
    select `stu_id`, round(avg(`score`), 1) as `avg_score` 
    from `tb_record` group by `stu_id`;

-- 基於已有的視圖創建視圖
create view `vw_student_score` 
as 
    select `stu_name`, `avg_score` 
    from `tb_student` natural join `vw_avg_score`;
```

> **提示**：因為視圖不包含數據，所以每次使用視圖時，都必須執行查詢以獲得數據，如果你使用了連接查詢、嵌套查詢創建了較為複雜的視圖，你可能會發現查詢性能下降得很厲害。因此，在使用複雜的視圖前，應該進行測試以確保其性能能夠滿足應用的需求。

使用視圖。

```SQL
select * from `vw_student_score` order by `avg_score` desc;
```

```
+--------------+----------+
| stuname      | avgscore |
+--------------+----------+
| 楊過         |     95.6 |
| 任我行       |     53.5 |
| 王語嫣       |     84.3 |
| 紀嫣然       |     73.8 |
| 岳不群       |     78.0 |
| 東方不敗     |     88.0 |
| 項少龍       |     92.0 |
+--------------+----------+
```

既然視圖是一張虛擬的表，那麼視圖的中的數據可以更新嗎？視圖的可更新性要視具體情況而定，以下類型的視圖是不能更新的：

1. 使用了聚合函數（`SUM`、`MIN`、`MAX`、`AVG`、`COUNT`等）、`DISTINCT`、`GROUP BY`、`HAVING`、`UNION`或者`UNION ALL`的視圖。
2. `SELECT`中包含了子查詢的視圖。
3. `FROM`子句中包含了一個不能更新的視圖的視圖。
4. `WHERE`子句的子查詢引用了`FROM`子句中的表的視圖。

刪除視圖。

```SQL
drop view vw_student_score;
```

> **說明**：如果希望更新視圖，可以先用上面的命令刪除視圖，也可以通過`create or replace view`來更新視圖。

視圖的規則和限制。

1. 視圖可以嵌套，可以利用從其他視圖中檢索的數據來構造一個新的視圖。視圖也可以和表一起使用。
2. 創建視圖時可以使用`order by`子句，但如果從視圖中檢索數據時也使用了`order by`，那麼該視圖中原先的`order by`會被覆蓋。
3. 視圖無法使用索引，也不會激發觸發器（實際開發中因為性能等各方面的考慮，通常不建議使用觸發器，所以我們也不對這個概念進行介紹）的執行。

### 函數

MySQL 中的函數跟 Python 中的函數太多的差異，因為函數都是用來封裝功能上相對獨立且會被重複使用的代碼的。如果非要找出一些差別來，那麼 MySQL 中的函數是可以執行 SQL 語句的。下面的例子，我們通過自定義函數實現了截斷超長字符串的功能。

```SQL
delimiter $$

create function truncate_string(
    content varchar(10000),
    max_length int unsigned
) returns varchar(10000) no sql
begin
    declare result varchar(10000) default content;
    if char_length(content) > max_length then
        set result = left(content, max_length);
        set result = concat(result, '……');
    end if;
    return result;
end $$

delimiter ;
```

> **說明1**：函數聲明後面的`no sql`是聲明函數體並沒有使用 SQL 語句；如果函數體中需要通過 SQL 讀取數據，需要聲明為`reads sql data`。
>
> **說明2**：定義函數前後的`delimiter`命令是為了修改定界符，因為函數體中的語句都是用`;`表示結束，如果不重新定義定界符，那麼遇到的`;`的時候代碼就會被截斷執行，顯然這不是我們想要的效果。

在查詢中調用自定義函數。

```SQL
select truncate_string('和我在成都的街頭走一走，直到所有的燈都熄滅了也不停留', 10) as short_string;
```

```
+--------------------------------------+
| short_string                         |
+--------------------------------------+
| 和我在成都的街頭走一……                 |
+--------------------------------------+
```

### 過程

過程（又稱存儲過程）是事先編譯好存儲在數據庫中的一組 SQL 的集合，調用過程可以簡化應用程序開發人員的工作，減少與數據庫服務器之間的通信，對於提升數據操作的性能也是有幫助的。其實迄今為止，我們使用的 SQL 語句都是針對一個或多個表的單條語句，但在實際開發中經常會遇到某個操作需要多條 SQL 語句才能完成的情況。例如，電商網站在受理用戶訂單時，需要做以下一系列的處理。

1. 通過查詢來核對庫存中是否有對應的物品以及庫存是否充足。
2. 如果庫存有物品，需要鎖定庫存以確保這些物品不再賣給別人， 並且要減少可用的物品數量以反映正確的庫存量。
3. 如果庫存不足，可能需要進一步與供應商進行交互或者至少產生一條系統提示消息。
4. 不管受理訂單是否成功，都需要產生流水記錄，而且需要給對應的用戶產生一條通知信息。

我們可以通過過程將復雜的操作封裝起來，這樣不僅有助於保證數據的一致性，而且將來如果業務發生了變動，只需要調整和修改過程即可。對於調用過程的用戶來說，過程並沒有暴露數據表的細節，而且執行過程比一條條的執行一組 SQL 要快得多。

下面的過程實現了查詢某門課程的最高分、最低分和平均分。

```SQL
drop procedure if exists sp_score_stat;

delimiter $$

create procedure sp_score_stat(
	courseId int, 
	out maxScore decimal(4,1), 
	out minScore decimal(4,1),
	out avgScore decimal(4,1)
)
begin
	select max(score) into maxScore from tb_record where cou_id=courseId;
	select min(score) into minScore from tb_record where cou_id=courseId;
	select avg(score) into avgScore from tb_record where cou_id=courseId;
end $$

delimiter ;
```

> **說明**：在定義過程時，因為可能需要書寫多條 SQL，而分隔這些 SQL 需要使用分號作為分隔符，如果這個時候，仍然用分號表示整段代碼結束，那麼定義過程的 SQL 就會出現錯誤，所以上面我們用`delimiter $$`將整段代碼結束的標記定義為`$$`，那麼代碼中的分號將不再表示整段代碼的結束，整段代碼只會在遇到`end $$`時才會執行。在定義完過程後，通過`delimiter ;`將結束符重新改回成分號（恢復現場）。

上面定義的過程有四個參數，其中第一個參數是輸入參數，代表課程的編號，後面的參數都是輸出參數，因為過程不能定義返回值，只能通過輸出參數將執行結果帶出，定義輸出參數的關鍵字是`out`，默認情況下參數都是輸入參數。

調用過程。

```SQL
call sp_score_stat(1111, @a, @b, @c);
```

獲取輸出參數的值。

```SQL
select @a as 最高分, @b as 最低分, @c as 平均分;
```

刪除過程。

```SQL
drop procedure sp_score_stat;
```

在過程中，我們可以定義變量、條件，可以使用分支和循環語句，可以通過游標操作查詢結果，還可以使用事件調度器，這些內容我們暫時不在此處進行介紹。雖然我們說了很多過程的好處，但是在實際開發中，如果頻繁的使用過程並將大量複雜的運算放到過程中，會給據庫服務器造成巨大的壓力，而數據庫往往都是性能瓶頸所在，使用過程無疑是雪上加霜的操作。所以，對於互聯網產品開發，我們一般建議讓數據庫只做好存儲，複雜的運算和處理交給應用服務器上的程序去完成，如果應用服務器變得不堪重負了，我們可以比較容易的部署多台應用服務器來分攤這些壓力。

如果大家對上面講到的視圖、函數、過程包括我們沒有講到的觸發器這些知識有興趣，建議大家閱讀 MySQL 的入門讀物[《MySQL必知必會》](https://item.jd.com/12818982.html)進行一般性了解即可，因為這些知識點在大家將來的工作中未必用得上，學了也可能僅僅是為了應付面試而已。

### MySQL 新特性

#### JSON類型

很多開發者在使用關係型數據庫做數據持久化的時候，常常感到結構化的存儲缺乏靈活性，因為必須事先設計好所有的列以及對應的數據類型。在業務發展和變化的過程中，如果需要修改表結構，這絕對是比較麻煩和難受的事情。從 MySQL 5.7 版本開始，MySQL引入了對 JSON 數據類型的支持（MySQL 8.0 解決了 JSON 的日誌性能瓶頸問題），用好 JSON 類型，其實就是打破了關係型數據庫和非關係型數據庫之間的界限，為數據持久化操作帶來了更多的便捷。

JSON 類型主要分為 JSON 對象和 JSON數組兩種，如下所示。

1. JSON 對象

```JSON
{"name": "駱昊", "tel": "13122335566", "QQ": "957658"}
```

2. JSON 數組

```JSON
[1, 2, 3]
```

```JSON
[{"name": "駱昊", "tel": "13122335566"}, {"name": "王大錘", "QQ": "123456"}]
```

哪些地方需要用到JSON類型呢？舉一個簡單的例子，現在很多產品的用戶登錄都支持多種方式，例如手機號、微信、QQ、新浪微博等，但是一般情況下我們又不會要求用戶提供所有的這些信息，那麼用傳統的設計方式，就需要設計多個列來對應多種登錄方式，可能還需要允許這些列存在空值，這顯然不是很好的選擇；另一方面，如果產品又增加了一種登錄方式，那麼就必然要修改之前的表結構，這就更讓人痛苦了。但是，有了 JSON 類型，剛才的問題就迎刃而解了，我們可以做出如下所示的設計。

```SQL
create table `tb_test`
(
`user_id` bigint unsigned,
`login_info` json,
primary key (`user_id`)
) engine=innodb;

insert into `tb_test` values 
    (1, '{"tel": "13122335566", "QQ": "654321", "wechat": "jackfrued"}'),
    (2, '{"tel": "13599876543", "weibo": "wangdachui123"}');
```

如果要查詢用戶的手機和微信號，可以用如下所示的 SQL 語句。

```SQL
select 
    `user_id`,
    json_unquote(json_extract(`login_info`, '$.tel')) as 手機號,
    json_unquote(json_extract(`login_info`, '$.wechat')) as 微信 
from `tb_test`;
```

```
+---------+-------------+-----------+
| user_id | 手機號      | 微信       |
+---------+-------------+-----------+
|       1 | 13122335566 | jackfrued |
|       2 | 13599876543 | NULL      |
+---------+-------------+-----------+
```

因為支持 JSON 類型，MySQL 也提供了配套的處理 JSON 數據的函數，就像上面用到的`json_extract`和`json_unquote`。當然，上面的 SQL 還有更為便捷的寫法，如下所示。

```SQL
select 
	`user_id`,
    `login_info` ->> '$.tel' as 手機號,
    `login_info` ->> '$.wechat' as 微信
from `tb_test`;
```

再舉個例子，如果我們的產品要實現用戶畫像功能（給用戶打標籤），然後基於用戶畫像給用戶推薦平台的服務或消費品之類的東西，我們也可以使用 JSON 類型來保存用戶畫像數據，示意代碼如下所示。

創建畫像標籤表。

```SQL
create table `tb_tags`
(
`tag_id` int unsigned not null comment '標籤ID',
`tag_name` varchar(20) not null comment '標籤名',
primary key (`tag_id`)
) engine=innodb;

insert into `tb_tags` (`tag_id`, `tag_name`) 
values
    (1, '70後'),
    (2, '80後'),
    (3, '90後'),
    (4, '00後'),
    (5, '愛運動'),
    (6, '高學歷'),
    (7, '小資'),
    (8, '有房'),
    (9, '有車'),
    (10, '愛看電影'),
    (11, '愛網購'),
    (12, '常點外賣');
```

為用戶打標籤。

```SQL
create table `tb_users_tags`
(
`user_id` bigint unsigned not null comment '用戶ID',
`user_tags` json not null comment '用戶標籤'
) engine=innodb;

insert into `tb_users_tags` values 
    (1, '[2, 6, 8, 10]'),
    (2, '[3, 10, 12]'),
    (3, '[3, 8, 9, 11]');
```

接下來，我們通過一組查詢來了解 JSON 類型的巧妙之處。

1. 查詢愛看電影（有`10`這個標籤）的用戶ID。

    ```SQL
    select * from `tb_users` where 10 member of (user_tags->'$');
    ```

2. 查詢愛看電影（有`10`這個標籤）的80後（有`2`這個標籤）用戶ID。

    ```
    select * from `tb_users` where json_contains(user_tags->'$', '[2, 10]');

3. 查詢愛看電影或80後或90後的用戶ID。

    ```SQL
    select `user_id` from `tb_users_tags` where json_overlaps(user_tags->'$', '[2, 3, 10]');
    ```

> **說明**：上面的查詢用到了`member of`謂詞和兩個 JSON 函數，`json_contains`可以檢查 JSON 數組是否包含了指定的元素，而`json_overlaps`可以檢查 JSON 數組是否與指定的數組有重疊部分。

#### 窗口函數

MySQL 從8.0開始支持窗口函數，大多數商業數據庫和一些開源數據庫早已提供了對窗口函數的支持，有的也將其稱之為 OLAP（聯機分析和處理）函數，聽名字就知道跟統計和分析相關。為了幫助大家理解窗口函數，我們先說說窗口的概念。

窗口可以理解為記錄的集合，窗口函數也就是在滿足某種條件的記錄集合上執行的特殊函數，對於每條記錄都要在此窗口內執行函數。窗口函數和我們上面講到的聚合函數比較容易混淆，二者的區別主要在於聚合函數是將多條記錄聚合為一條記錄，窗口函數是每條記錄都會執行，執行後記錄條數不會變。窗口函數不僅僅是幾個函數，它是一套完整的語法，函數只是該語法的一部分，基本語法如下所示：

```SQL
<窗口函數> over (partition by <用於分組的列名> order by <用戶排序的列名>)
```

上面語法中，窗口函數的位置可以放以下兩種函數：

1. 專用窗口函數，包括：`lead`、`lag`、`first_value`、`last_value`、`rank`、`dense_rank`和`row_number`等。
2. 聚合函數，包括：`sum`、`avg`、`max`、`min`和`count`等。

下面為大家舉幾個使用窗口函數的簡單例子，我們先用如下所示的 SQL 建庫建表。

```SQL
-- 創建名為hrs的數據庫並指定默認的字符集
create database `hrs` default charset utf8mb4;

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

例子1：查詢按月薪從高到低排在第4到第6名的員工的姓名和月薪。

```SQL
select * from (
	select 
		`ename`, `sal`,
		row_number() over (order by `sal` desc) as `rank`
	from `tb_emp`
) `temp` where `rank` between 4 and 6;
```

> **說明**：上面使用的函數`row_number()`可以為每條記錄生成一個行號，在實際工作中可以根據需要將其替換為`rank()`或`dense_rank()`函數，三者的區別可以參考官方文檔或閱讀[《通俗易懂的學會：SQL窗口函數》](https://zhuanlan.zhihu.com/p/92654574)進行了解。在MySQL 8以前的版本，我們可以通過下面的方式來完成類似的操作。
>
> ```SQL
> select `rank`, `ename`, `sal` from (
>        select @a:=@a+1 as `rank`, `ename`, `sal` 
>        from `tb_emp`, (select @a:=0) as t1 order by `sal` desc
> ) as `temp` where `rank` between 4 and 6;
> ```

例子2：查詢每個部門月薪最高的兩名的員工的姓名和部門名稱。

```SQL
select `ename`, `sal`, `dname` 
from (
    select 
        `ename`, `sal`, `dno`,
        rank() over (partition by `dno` order by `sal` desc) as `rank`
    from `tb_emp`
) as `temp` natural join `tb_dept` where `rank`<=2;
```

> 說明：在MySQL 8以前的版本，我們可以通過下面的方式來完成類似的操作。
>
> ```SQL
> select `ename`, `sal`, `dname` from `tb_emp` as `t1` 
natural join `tb_dept` 
where (
        select count(*) from `tb_emp` as `t2` 
        where `t1`.`dno`=`t2`.`dno` and `t2`.`sal`>`t1`.`sal` 
)<2 order by `dno` asc, `sal` desc;
> ```

###  其他內容

#### 範式理論

範式理論是設計關係型數據庫中二維表的指導思想。

1. 第一範式：數據表的每個列的值域都是由原子值組成的，不能夠再分割。
2. 第二範式：數據表裡的所有數據都要和該數據表的鍵（主鍵與候選鍵）有完全依賴關係。
3. 第三範式：所有非鍵屬性都只和候選鍵有相關性，也就是說非鍵屬性之間應該是獨立無關的。

> **說明**：實際工作中，出於效率的考慮，我們在設計表時很有可能做出反範式設計，即故意降低方式級別，增加冗餘數據來獲得更好的操作性能。

#### 數據完整性

1. 實體完整性 - 每個實體都是獨一無二的

   - 主鍵（`primary key`） / 唯一約束（`unique`）
2. 引用完整性（參照完整性）- 關係中不允許引用不存在的實體

   - 外鍵（`foreign key`）
3. 域（domain）完整性 - 數據是有效的
   - 數據類型及長度

   - 非空約束（`not null`）

   - 默認值約束（`default`）

   - 檢查約束（`check`）

     > **說明**：在 MySQL 8.x 以前，檢查約束並不起作用。

#### 數據一致性

1. 事務：一系列對數據庫進行讀/寫的操作，這些操作要么全都成功，要么全都失敗。

2. 事務的 ACID 特性
   - 原子性：事務作為一個整體被執行，包含在其中的對數據庫的操作要么全部被執行，要么都不執行
   - 一致性：事務應確保數據庫的狀態從一個一致狀態轉變為另一個一致狀態
   - 隔離性：多個事務並發執行時，一個事務的執行不應影響其他事務的執行
   - 持久性：已被提交的事務對數據庫的修改應該永久保存在數據庫中

3. MySQL 中的事務操作

   - 開啟事務環境

     ```SQL
     start transaction
     ```

   - 提交事務

     ```SQL
     commit
     ```

   - 回滾事務

     ```SQL
     rollback
     ```

4. 查看事務隔離級別

    ```SQL
    show variables like 'transaction_isolation';
    ```

    ```
    +-----------------------+-----------------+
    | Variable_name         | Value           |
    +-----------------------+-----------------+
    | transaction_isolation | REPEATABLE-READ |
    +-----------------------+-----------------+
    ```

    可以看出，MySQL 默認的事務隔離級別是`REPEATABLE-READ`。

5. 修改（當前會話）事務隔離級別

    ```SQL
    set session transaction isolation level read committed;
    ```

    重新查看事務隔離級別，結果如下所示。

    ```
    +-----------------------+----------------+
    | Variable_name         | Value          |
    +-----------------------+----------------+
    | transaction_isolation | READ-COMMITTED |
    +-----------------------+----------------+
    ```

關係型數據庫的事務是一個很大的話題，因為當存在多個並發事務訪問數據時，就有可能出現三類讀數據的問題（臟讀、不可重複讀、幻讀）和兩類更新數據的問題（第一類丟失更新、第二類丟失更新）。想了解這五類問題的，可以閱讀我發佈在 CSDN 網站上的[《Java面試題全集（上）》](https://blog.csdn.net/jackfrued/article/details/44921941)一文的第80題。為了避免這些問題，關係型數據庫底層是有對應的鎖機制的，按鎖定對像不同可以分為表級鎖和行級鎖，按並發事務鎖定關係可以分為共享鎖和獨占鎖。然而直接使用鎖是非常麻煩的，為此數據庫為用戶提供了自動鎖機制，只要用戶指定適當的事務隔離級別，數據庫就會通過分析 SQL 語句，然後為事務訪問的資源加上合適的鎖。此外，數據庫還會維護這些鎖通過各種手段提高系統的性能，這些對用戶來說都是透明的。想了解 MySQL 事務和鎖的細節知識，推薦大家閱讀進階讀物[《高性能MySQL》](https://item.jd.com/11220393.html)，這也是數據庫方面的經典書籍。

ANSI/ISO SQL 92標准定義了4個等級的事務隔離級別，如下表所示。需要說明的是，事務隔離級別和數據訪問的並發性是對立的，事務隔離級別越高並發性就越差。所以要根據具體的應用來確定到底使用哪種事務隔離級別，這個地方沒有萬能的原則。

<img src="https://gitee.com/jackfrued/mypic/raw/master/20211121225327.png" style="zoom:50%;">

### 總結

關於 MySQL 的知識肯定遠遠不止上面列出的這些，比如 MySQL 性能調優、MySQL 運維相關工具、MySQL 數據的備份和恢復、監控 MySQL 服務、部署高可用架構等，這一系列的問題在這裡都沒有辦法逐一展開來討論，那就留到有需要的時候再進行講解吧，各位讀者也可以自行探索。
