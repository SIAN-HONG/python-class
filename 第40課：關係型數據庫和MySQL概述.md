## 第40課：關係型數據庫和MySQL概述

### 關係型數據庫概述

1. 數據持久化 - 將數據保存到能夠長久保存數據的存儲介質中，在掉電的情況下數據也不會丟失。

2. 數據庫發展史 - 網狀數據庫、層次數據庫、關係數據庫、NoSQL 數據庫、NewSQL 數據庫。

   > 1970年，IBM的研究員E.F.Codd在*Communication of the ACM*上發表了名為*A Relational Model of Data for Large Shared Data Banks*的論文，提出了**關係模型**的概念，奠定了關係模型的理論基礎。後來Codd又陸續發表多篇文章，論述了範式理論和衡量關係系統的12條標準，用數學理論奠定了關係數據庫的基礎。

3. 關係數據庫特點。

   - 理論基礎：**關係代數**（關係運算、集合論、一階謂詞邏輯）。

   - 具體表象：用**二維表**（有行和列）組織數據。

   - 編程語言：**結構化查詢語言**（SQL）。

4. ER模型（實體關係模型）和概念模型圖。

   **ER模型**，全稱為**實體關係模型**（Entity-Relationship Model），由美籍華裔計算機科學家陳品山先生提出，是概念數據模型的高層描述方式，如下圖所示。

   <img src="https://gitee.com/jackfrued/mypic/raw/master/20210826003119.png" width="75%">

   - 實體 - 矩形框
   - 屬性 - 橢圓框
   - 關係 - 菱形框
   - 重數 - 1:1（一對一） / 1:N（一對多） / M:N（多對多）

   實際項目開發中，我們可以利用數據庫建模工具（如：PowerDesigner）來繪製概念數據模型（其本質就是 ER 模型），然後再設置好目標數據庫系統，將概念模型轉換成物理模型，最終生成創建二維表的 SQL（很多工具都可以根據我們設計的物理模型圖以及設定的目標數據庫來導出 SQL 或直接生成數據表）。

   ![](https://gitee.com/jackfrued/mypic/raw/master/20210826003212.png)

5. 關係數據庫產品。
   - [Oracle](https://www.oracle.com/index.html) - 目前世界上使用最為廣泛的數據庫管理系統，作為一個通用的數據庫系統，它具有完整的數據管理功能；作為一個關係數據庫，它是一個完備關係的產品；作為分佈式數據庫，它實現了分佈式處理的功能。在 Oracle 最新的 12c 版本中，還引入了多承租方架構，使用該架構可輕鬆部署和管理數據庫雲。
   - [DB2](https://www.ibm.com/analytics/us/en/db2/) - IBM 公司開發的、主要運行於 Unix（包括 IBM 自家的 [AIX](https://zh.wikipedia.org/wiki/AIX)）、Linux、以及 Windows 服務器版等系統的關係數據庫產品。 DB2 歷史悠久且被認為是最早使用 SQL 的數據庫產品，它擁有較為強大的商業智能功能。
   - [SQL Server](https://www.microsoft.com/en-us/sql-server/) - 由 Microsoft 開發和推廣的關係型數據庫產品，最初適用於中小企業的數據管理，但是近年來它的應用範圍有所擴展，部分大企業甚至是跨國公司也開始基於它來構建自己的數據管理系統。
   - [MySQL](https://www.mysql.com/) - MySQL 是開放源代碼的，任何人都可以在 GPL（General Public License）的許可下下載並根據個性化的需要對其進行修改。 MySQL 因為其速度、可靠性和適應性而備受關注。
   - [PostgreSQL]() - 在 BSD 許可證下發行的開放源代碼的關係數據庫產品。

### MySQL 簡介

MySQL 最早是由瑞典的 MySQL AB 公司開發的一個開放源碼的關係數據庫管理系統，該公司於2008年被昇陽微系統公司（Sun Microsystems）收購。在2009年，甲骨文公司（Oracle）收購昇陽微系統公司，因此 MySQL 目前也是 Oracle 旗下產品。

MySQL 在過去由於性能高、成本低、可靠性好，已經成為最流行的開源數據庫，因此被廣泛地應用於中小型網站開發。隨著 MySQL 的不斷成熟，它也逐漸被應用於更多大規模網站和應用，比如維基百科、谷歌（Google）、臉書（Facebook）、淘寶網等網站都使用了 MySQL 來提供數據持久化服務。

甲骨文公司收購後昇陽微系統公司，大幅調漲 MySQL 商業版的售價，且甲骨文公司不再支持另一個自由軟件項目 [OpenSolaris ](https://zh.wikipedia.org/wiki/OpenSolaris) 的發展，因此導致自由軟件社區對於 Oracle 是否還會持續支持 MySQL 社區版（MySQL 的各個發行版本中唯一免費的版本）有所擔憂，MySQL 的創始人麥克爾·維德紐斯以 MySQL 為基礎，創建了 [MariaDB](https://zh.wikipedia.org/wiki/MariaDB)（以他女兒的名字命名的數據庫）分支。有許多原來使用 MySQL 數據庫的公司（例如：維基百科）已經陸續完成了從 MySQL 數據庫到 MariaDB 數據庫的遷移。

### 安裝 MySQL

#### Windows 環境

1. 通過[官方網站](https://www.mysql.com/)提供的[下載鏈接](https://dev.mysql.com/downloads/windows/installer/8.0.html)下載“MySQL社區版服務器”安裝程序，如下圖所示，建議大家下載離線安裝版的MySQL Installer。

    <img src="https://gitee.com/jackfrued/mypic/raw/master/20211105230905.png" style="zoom:50%">

2. 運行 Installer，按照下面的步驟進行安裝。

    - 選擇自定義安裝。

    <img src="https://gitee.com/jackfrued/mypic/raw/master/20211105231152.jpg" style="zoom:35%">

    - 選擇需要安裝的組件。

    <img src="https://gitee.com/jackfrued/mypic/raw/master/20211105231255.jpg" style="zoom:35%">

    - 如果缺少依賴項，需要先安裝依賴項。

    <img src="https://gitee.com/jackfrued/mypic/raw/master/20211105231620.png" style="zoom:35%">

    - 準備開始安裝。

    <img src="https://gitee.com/jackfrued/mypic/raw/master/20211105231719.jpg" style="zoom:35%">

    - 安裝完成。

    <img src="https://gitee.com/jackfrued/mypic/raw/master/20211105232024.jpg" style="zoom:35%">

    - 準備執行配置嚮導。

    <img src="https://gitee.com/jackfrued/mypic/raw/master/20211105231815.jpg" style="zoom:35%">

3. 執行安裝後的配置嚮導。

    - 配置服務器類型和網絡。

    <img src="https://gitee.com/jackfrued/mypic/raw/master/20211105232109.jpg" style="zoom:35%">

    - 配置認證方法（保護密碼的方式）。

        <img src="https://gitee.com/jackfrued/mypic/raw/master/20211105232408.jpg" style="zoom:35%">

    - 配置用戶和角色。

        <img src="https://gitee.com/jackfrued/mypic/raw/master/20211105232521.jpg" style="zoom:35%">

    - 配置Windows服務名以及是否開機自啟。

        <img src="https://gitee.com/jackfrued/mypic/raw/master/20211105232608.jpg" style="zoom:35%">

    - 配置日誌。

        <img src="https://gitee.com/jackfrued/mypic/raw/master/20211105232641.jpg" style="zoom:35%">

    - 配置高級選項。

        <img src="https://gitee.com/jackfrued/mypic/raw/master/20211105232724.jpg" alt="ACAC15B8633133B65476286A49BFBD7E" style="zoom:35%">

    - 應用配置。

        <img src="https://gitee.com/jackfrued/mypic/raw/master/20211105232800.jpg" style="zoom:35%">

4. 可以在 Windows 系統的“服務”窗口中啟動或停止 MySQL。

    <img src="https://gitee.com/jackfrued/mypic/raw/master/20211105232926.jpg" style="zoom:50%">

5. 配置 PATH 環境變量，以便在命令行提示符窗口使用 MySQL 客戶端工具。

    - 打開 Windows 的“系統”窗口並點擊“高級系統設置”。

        <img src="https://gitee.com/jackfrued/mypic/raw/master/20211105233054.jpg" style="zoom:50%">

    - 在“系統屬性”的“高級”窗口，點擊“環境變量”按鈕。

        <img src="https://gitee.com/jackfrued/mypic/raw/master/20211105233312.jpg" style="zoom:50%">

    - 修改PATH環境變量，將MySQL安裝路徑下的`bin`文件夾的路徑配置到PATH環境變量中。

        <img src="https://gitee.com/jackfrued/mypic/raw/master/20211105233359.jpg" style="zoom:50%">

    - 配置完成後，可以嘗試在“命令提示符”下使用 MySQL 的命令行工具。

        <img src="https://gitee.com/jackfrued/mypic/raw/master/20211105233643.jpg" style="zoom:50%">

#### Linux 環境

下面以 CentOS 7.x 環境為例，演示如何安裝 MySQL 5.7.x，如果需要在其他 Linux 系統下安裝其他版本的 MySQL，請讀者自行在網絡上查找對應的安裝教程。

1. 安裝 MySQL。

   可以在 [MySQL 官方網站](<https://www.mysql.com/>)下載安裝文件。首先在下載頁面中選擇平台和版本，然後找到對應的下載鏈接，直接下載包含所有安裝文件的歸檔文件，解歸檔之後通過包管理工具進行安裝。

   ```Shell
   wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.26-1.el7.x86_64.rpm-bundle.tar
   tar -xvf mysql-5.7.26-1.el7.x86_64.rpm-bundle.tar
   ```

   如果系統上有 MariaDB 相關的文件，需要先移除 MariaDB 相關的文件。

   ```Shell
   yum list installed | grep mariadb | awk '{print $1}' | xargs yum erase -y
   ```

   更新和安裝可能用到的底層依賴庫。

   ```Bash
   yum update
   yum install -y libaio libaio-devel
   ```

   接下來可以按照如下所示的順序用 RPM（Redhat Package Manager）工具安裝 MySQL。

   ```Shell
   rpm -ivh mysql-community-common-5.7.26-1.el7.x86_64.rpm
   rpm -ivh mysql-community-libs-5.7.26-1.el7.x86_64.rpm
   rpm -ivh mysql-community-libs-compat-5.7.26-1.el7.x86_64.rpm
   rpm -ivh mysql-community-devel-5.7.26-1.el7.x86_64.rpm
   rpm -ivh mysql-community-client-5.7.26-1.el7.x86_64.rpm
   rpm -ivh mysql-community-server-5.7.26-1.el7.x86_64.rpm
   ```

   可以使用下面的命令查看已經安裝的 MySQL 相關的包。

   ```Shell
   rpm -qa | grep mysql
   ```

2. 配置 MySQL。

   MySQL 的配置文件在`/etc`目錄下，名為`my.cnf`，默認的配置文件內容如下所示。

   ```Shell
   cat /etc/my.cnf
   ```

   ```INI
   # For advice on how to change settings please see
   # http://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html
   
   [mysqld]
   #
   # Remove leading # and set to the amount of RAM for the most important data
   # cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
   # innodb_buffer_pool_size = 128M
   #
   # Remove leading # to turn on a very important data integrity option: logging
   # changes to the binary log between backups.
   # log_bin
   #
   # Remove leading # to set options mainly useful for reporting servers.
   # The server defaults are faster for transactions and fast SELECTs.
   # Adjust sizes as needed, experiment to find the optimal values.
   # join_buffer_size = 128M
   # sort_buffer_size = 2M
   # read_rnd_buffer_size = 2M
   datadir=/var/lib/mysql
   socket=/var/lib/mysql/mysql.sock
   
   # Disabling symbolic-links is recommended to prevent assorted security risks
   symbolic-links=0
   
   log-error=/var/log/mysqld.log
   pid-file=/var/run/mysqld/mysqld.pid
   ```

   通過配置文件，我們可以修改 MySQL 服務使用的端口、字符集、最大連接數、套接字隊列大小、最大數據包大小、日誌文件的位置、日誌過期時間等配置。當然，我們還可以通過修改配置文件來對 MySQL 服務器進行性能調優和安全管控。

3. 啟動 MySQL 服務。

   可以使用下面的命令來啟動 MySQL。

   ```Shell
   service mysqld start
   ```

   在 CentOS 7 中，更推薦使用下面的命令來啟動 MySQL。

   ```Shell
   systemctl start mysqld
   ```

   啟動 MySQL 成功後，可以通過下面的命令來檢查網絡端口使用情況，MySQL 默認使用`3306`端口。

   ```Shell
   netstat -ntlp | grep mysql
   ```

   也可以使用下面的命令查找是否有名為`mysqld`的進程。

   ```Shell
   pgrep mysqld
   ```

4. 使用 MySQL 客戶端工具連接服務器。

   命令行工具：

   ```Shell
   mysql -u root -p
   ```

   > 說明：啟動客戶端時，`-u`參數用來指定用戶名，MySQL 默認的超級管理賬號為`root`；`-p`表示要輸入密碼（用戶口令）；如果連接的是其他主機而非本機，可以用`-h`來指定連接主機的主機名或IP地址。

   如果是首次安裝 MySQL，可以使用下面的命令來找到默認的初始密碼。

   ```Shell
   cat /var/log/mysqld.log | grep password
   ```

   上面的命令會查看 MySQL 的日誌帶有`password`的行，在顯示的結果中`root@localhost:`後面的部分就是默認設置的初始密碼。

   進入客戶端工具後，可以通過下面的指令來修改超級管理員（root）的訪問口令為`123456`。

   ```SQL
   set global validate_password_policy=0;
   set global validate_password_length=6;
   alter user 'root'@'localhost' identified by '123456';
   ```

   > **說明**：MySQL 較新的版本默認不允許使用弱口令作為用戶口令，所以上面的代碼修改了驗證用戶口令的策略和口令的長度。事實上我們不應該使用弱口令，因為存在用戶口令被暴力破解的風險。近年來，**攻擊數據庫竊取數據和劫持數據庫勒索比特幣**的事件屢見不鮮，要避免這些潛在的風險，最為重要的一點是**不要讓數據庫服務器暴露在公網上**（最好的做法是將數據庫置於內網，至少要做到不向公網開放數據庫服務器的訪問端口），另外要保管好`root`賬號的口令，應用系統需要訪問數據庫時，通常不使用`root`賬號進行訪問，而是**創建其他擁有適當權限的賬號來訪問**。

   再次使用客戶端工具連接 MySQL 服務器時，就可以使用新設置的口令了。在實際開發中，為了方便用戶操作，可以選擇圖形化的客戶端工具來連接 MySQL 服務器，包括：

   - MySQL Workbench（官方工具）

       <img src="https://gitee.com/jackfrued/mypic/raw/master/20211106063939.png" style="zoom:50%">

   - Navicat for MySQL（界面簡單友好）

       <img src="https://gitee.com/jackfrued/mypic/raw/master/20210521152457.png" style="zoom:50%;">
   

#### macOS環境

macOS 系統安裝 MySQL 是比較簡單的，只需要從剛才說到的官方網站下載 DMG 安裝文件並運行就可以了，下載的時候需要根據自己使用的是 Intel 的芯片還是蘋果的 M1 芯片選擇下載鏈接，如下圖所示。

<img src="https://gitee.com/jackfrued/mypic/raw/master/20211121215901.png" style="zoom:50%;">

安裝成功後，可以在“系統偏好設置”中找到“MySQL”，在如下所示的畫面中，可以啟動和停止 MySQL 服務器，也可以對 MySQL 核心文件的路徑進行配置。

<img src="https://gitee.com/jackfrued/mypic/raw/master/20211121215153.png" style="zoom:40%;">

### MySQL 基本命令

#### 查看命令

1. 查看所有數據庫

```SQL
show databases;
```

2. 查看所有字符集

```SQL
show character set;
```

3. 查看所有的排序規則

```SQL
show collation;
```

4. 查看所有的引擎

```SQL
show engines;
```

5. 查看所有日誌文件

```SQL
show binary logs;
```

6. 查看數據庫下所有表

```SQL
show tables;
```

#### 獲取幫助

在 MySQL 命令行工具中，可以使用`help`命令或`?`來獲取幫助，如下所示。

1. 查看`show`命令的幫助。

    ```MySQL
    ? show
    ```

2. 查看有哪些幫助內容。

    ```MySQL
    ? contents
    ```

3. 獲取函數的幫助。

    ```MySQL
    ? functions
    ```

4. 獲取數據類型的幫助。

    ```MySQL
    ? data types
    ```

#### 其他命令

1. 新建/重建服務器連接 -`connect` / `resetconnection`。

2. 清空當前輸入 - `\c`。在輸入錯誤時，可以及時使用`\c`清空當前輸入並重新開始。

3. 修改終止符（定界符）- `delimiter`。默認的終止符是`;`，可以使用該命令修改成其他的字符，例如修改為`$`符號，可以用`delimiter $`命令。

4. 打開系統默認編輯器 - `edit`。編輯完成保存關閉之後，命令行會自動執行編輯的內容。

5. 查看服務器狀態 - `status`。

6. 修改默認提示符 - `prompt`。

7. 執行系統命令 - `system`。可以將系統命令跟在`system`命令的後面執行，`system`命令也可以縮寫為`\!`。

8. 執行 SQL 文件 - `source`。 `source`命令後面跟 SQL 文件路徑。

9. 重定向輸出 - `tee` / `notee`。可以將命令的輸出重定向到指定的文件中。

10. 切換數據庫 - `use`。

11. 顯示警告信息 - `warnings`。

12. 退出命令行 - `quit`或`exit`。
