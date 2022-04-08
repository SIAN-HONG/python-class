## 第01課：初識Python

### Python簡介

Python是由荷蘭人吉多·範羅蘇姆（Guido von Rossum）發明的一種編程語言，是目前世界上最受歡迎和擁有最多用戶群體的編程語言。

#### Python的歷史

1. 1989年聖誕節：Guido開始寫Python語言的編譯器。
2. 1991年2月：第一個Python解釋器誕生，它是用C語言實現的，可以調用C語言的庫函數。
3. 1994年1月：Python 1.0正式發布。
4. 2000年10月：Python 2.0發布，Python的整個開發過程更加透明，生態圈開始慢慢形成。
5. 2008年12月：Python 3.0發布，引入了諸多現代編程語言的新特性，但並不完全兼容之前的Python代碼。
6. 2020年1月：在Python 2和Python 3共存了11年之後，官方停止了對Python 2的更新和維護，希望用戶盡快過渡到Python 3。

> **說明**：大多數軟件的版本號一般分為三段，形如A.B.C，其中A表示大版本號，當軟件整體重寫升級或出現不向後兼容的改變時，才會增加A；B表示功能更新，出現新功能時增加B；C表示小的改動（例如：修復了某個Bug），只要有修改就增加C。

#### Python的優缺點

Python的優點很多，簡單為大家列出幾點。

1. 簡單明確，跟其他很多語言相比，Python更容易上手。
2. 能用更少的代碼做更多的事情，提升開發效率。
3. 開放源代碼，擁有強大的社區和生態圈。
4. 能夠做的事情非常多，有極強的適應性。
5. 能夠在Windows、macOS、Linux等各種系統上運行。

Python最主要的缺點是執行效率低，但是當我們更看重產品的開發效率而不是執行效率的時候，Python就是很好的選擇。

#### Python的應用領域

目前Python在Web服務器應用開發、雲基礎設施開發、**網絡數據採集**（爬蟲）、**數據分析**、量化交易、**機器學習**、**深度學習**、自動化測試、自動化運維等領域都有用武之地。

### 安裝Python環境

想要開始你的Python編程之旅，首先得在計算機上安裝Python環境，簡單的說就是得安裝運行Python程序的工具，通常也稱之為Python解釋器。我們強烈建議大家安裝Python 3的環境，很明顯它是目前更好的選擇。

#### Windows環境

可以在[Python官方網站](https://www.python.org/downloads/)找到下載鏈接並下載Python 3的安裝程序。

![](https://gitee.com/jackfrued/mypic/raw/master/20210719222940.png)

對於Windows操作系統，可以下載“executable installer”。需要注意的是，如果在Windows 7環境下安裝Python 3，需要先安裝Service Pack 1補丁包，大家可以在Windows的“運行”中輸入`winver`命令，從彈出的窗口上可以看到你的系統是否安裝了該補丁包。如果沒有該補丁包，一定要先通過“Windows Update”或者類似“CCleaner”這樣的工具自動安裝該補丁包，安裝完成後通常需要重啟你的Windows系統，然後再開始安裝Python環境。

![](https://gitee.com/jackfrued/mypic/raw/master/20210719222956.png)

雙擊運行剛才下載的安裝程序，會打開Python環境的安裝嚮導。在執行安裝嚮導的時候，記得勾選“Add Python 3.x to PATH”選項，這個選項會幫助我們將Python的解釋器添加到PATH環境變量中（不理解沒關係，照做就行），具體的步驟如下圖所示。

![](https://gitee.com/jackfrued/mypic/raw/master/20210719223007.png)

![](https://gitee.com/jackfrued/mypic/raw/master/20210719223021.png)

![](https://gitee.com/jackfrued/mypic/raw/master/20210719223317.png)

![](https://gitee.com/jackfrued/mypic/raw/master/20210719223332.png)

安裝完成後可以打開Windows的“命令行提示符”工具（或“PowerShell”）並輸入`python --version`或`python -V`來檢查安裝是否成功，命令行提示符可以在“運行”中輸入`cmd`來打開或者在“開始菜單”的附件中找到它。如果看了Python解釋器對應的版本號（如：Python 3.7.8），說明你的安裝已經成功了，如下圖所示。

![](https://gitee.com/jackfrued/mypic/raw/master/20210719223350.png)

> **說明**：如果安裝過程顯示安裝失敗或執行上面的命令報錯，很有可能是因為你的Windows系統缺失了一些動態鏈接庫文件或C構建工具導致的問題。可以在[微軟官網](https://www.microsoft.com/zh-cn/download/details.aspx?id=48145)下載Visual C++ Redistributable for Visual Studio 2015文件進行修復，64位的系統需要下載有x64標記的安裝文件。也可以通過下面的百度雲盤地址獲取修復工具，運行修復工具，按照如下圖所示的方式進行修復，鏈接: https://pan.baidu.com/s/1iNDnU5UVdDX5sKFqsiDg5Q 提取碼: cjs3。
>
> ![QQ20210711-0](https://gitee.com/jackfrued/mypic/raw/master/20210816234614.png)

除此之外，你還應該檢查一下Python的包管理工具是否已經可用，對應的命令是`pip --version`。

#### macOS環境

macOS自帶了Python 2，但是我們需要安裝和使用的是Python 3。可以通過Python官方網站提供的[下載鏈接](<https://www.python.org/downloads/release/python-376/>)找到適合macOS的“macOS installer”來安裝Python 3，安裝過程基本不需要做任何勾選，直接點擊“下一步”即可。安裝完成後，可以在macOS的“終端”工具中輸入`python3`命令來調用Python 3解釋器，因為如果直接輸入`python`，將會調用Python 2的解釋器。

### 總結

Python語言可以做很多的事情，也值得我們去學習。要使用Python語言，首先需要在自己的計算機上安裝Python環境，也就是運行Python程序的Python解釋器。
