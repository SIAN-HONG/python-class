## 第29課：用Python發送郵件和短信

在前面的課程中，我們已經教會大家如何用Python程序自動的生成Excel、Word、PDF文檔，接下來我們還可以更進一步，就是通過郵件將生成好的文檔發送給指定的收件人，然後用短信告知對方我們發出了郵件。這些事情利用Python程序也可以輕鬆愉快的解決。

### 發送電子郵件

在即時通信軟件如此發達的今天，電子郵件仍然是互聯網上使用最為廣泛的應用之一，公司向應聘者發出錄用通知、網站向用戶發送一個激活賬號的鏈接、銀行向客戶推廣它們的理財產品等幾乎都是通過電子郵件來完成的，而這些任務應該都是由程序自動完成的。

我們可以用HTTP（超文本傳輸協議）來訪問網站資源，HTTP是一個應用級協議，它建立在TCP（傳輸控制協議）之上，TCP為很多應用級協議提供了可靠的數據傳輸服務。如果要發送電子郵件，需要使用SMTP（簡單郵件傳輸協議），它也是建立在TCP之上的應用級協議，規定了郵件的發送者如何跟郵件服務器進行通信的細節。 Python通過名為`smtplib`的模塊將這些操作簡化成了`SMTP_SSL`對象，通過該對象的`login`和`send_mail`方法，就能夠完成發送郵件的操作。

我們先嘗試一下發送一封極為簡單的郵件，該郵件不包含附件、圖片以及其他超文本內容。發送郵件首先需要接入郵件服務器，我們可以自己架設郵件服務器，這件事情對新手並不友好，但是我們可以選擇使用第三方提供的郵件服務。例如，我在<www.126.com>已經註冊了賬號，登錄成功之後，就可以在設置中開啟SMTP服務，這樣就相當於獲得了郵件服務器，具體的操作如下所示。

<img src="https://gitee.com/jackfrued/mypic/raw/master/20210820190307.png" alt="image-20210820190306861" width="95%">

![image-20210820190816557](https://gitee.com/jackfrued/mypic/raw/master/20210820190816.png)

用手機掃碼上面的二維碼可以通過發送短信的方式來獲取授權碼，短信發送成功後，點擊“我已發送”就可以獲得授權碼。授權碼需要妥善保管，因為一旦洩露就會被其他人冒用你的身份來發送郵件。接下來，我們就可以編寫發送郵件的代碼了，如下所示。

```Python
import smtplib
from email.header import Header
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText

# 創建郵件主體對象
email = MIMEMultipart()
# 設置發件人、收件人和主題
email['From'] = 'xxxxxxxxx@126.com'
email['To'] = 'yyyyyy@qq.com;zzzzzz@1000phone.com'
email['Subject'] = Header('上半年工作情況匯報', 'utf-8')
# 添加郵件正文內容
content = """據德國媒體報導，當地時間9日，德國火車司機工會成員進行了投票，
定於當地時間10日起進行全國性罷工，貨運交通方面的罷工已於當地時間10日19時開始。
此後，從11日凌晨2時到13日凌晨2時，德國全國范圍內的客運和鐵路基礎設施將進行48小時的罷工。 """
email.attach(MIMEText(content, 'plain', 'utf-8'))

# 創建SMTP_SSL對象（連接郵件服務器）
smtp_obj = smtplib.SMTP_SSL('smtp.126.com', 465)
# 通過用戶名和授權碼進行登錄
smtp_obj.login('xxxxxxxxx@126.com', '郵件服務器的授權碼')
# 發送郵件（發件人、收件人、郵件內容（字符串））
smtp_obj.sendmail(
    'xxxxxxxxx@126.com',
    ['yyyyyy@qq.com', 'zzzzzz@1000phone.com'],
    email.as_string()
)
```

如果要發送帶有附件的郵件，只需要將附件的內容處理成BASE64編碼，那麼它就和普通的文本內容幾乎沒有什麼區別。 BASE64是一種基於64個可打印字符來表示二進制數據的表示方法，常用於某些需要表示、傳輸、存儲二進制數據的場合，電子郵件就是其中之一。對這種編碼方式不理解的同學，推薦閱讀[《Base64筆記》](http://www.ruanyifeng.com/blog/2008/06/base64.html)一文。在之前的內容中，我們也提到過，Python標準庫的`base64`模塊提供了對BASE64編解碼的支持。

下面的代碼演示瞭如何發送帶附件的郵件。

```Python
import smtplib
from email.header import Header
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from urllib.parse import quote

# 創建郵件主體對象
email = MIMEMultipart()
# 設置發件人、收件人和主題
email['From'] = 'xxxxxxxxx@126.com'
email['To'] = 'zzzzzzzz@1000phone.com'
email['Subject'] = Header('請查收離職證明文件', 'utf-8')
# 添加郵件正文內容（帶HTML標籤排版的內容）
content = """<p>親愛的前同事：</p>
<p>你需要的離職證明在附件中，請查收！ </p>
<br>
<p>祝，好！ </p>
<hr>
<p>孫美麗 即日</p>"""
email.attach(MIMEText(content, 'html', 'utf-8'))
# 讀取作為附件的文件
with open(f'resources/王大錘離職證明.docx', 'rb') as file:
    attachment = MIMEText(file.read(), 'base64', 'utf-8')
    # 指定內容類型
    attachment['content-type'] = 'application/octet-stream'
    # 將中文文件名處理成百分號編碼
    filename = quote('王大錘離職證明.docx')
    # 指定如何處置內容
    attachment['content-disposition'] = f'attachment; filename="{filename}"'

# 創建SMTP_SSL對象（連接郵件服務器）
smtp_obj = smtplib.SMTP_SSL('smtp.126.com', 465)
# 通過用戶名和授權碼進行登錄
smtp_obj.login('xxxxxxxxx@126.com', '郵件服務器的授權碼')
# 發送郵件（發件人、收件人、郵件內容（字符串））
smtp_obj.sendmail(
    'xxxxxxxxx@126.com',
    'zzzzzzzz@1000phone.com',
    email.as_string()
)
```

為了方便大家用Python實現郵件發送，我將上面的代碼封裝成了函數，使用的時候大家只需要調整郵件服務器域名、端口、用戶名和授權碼就可以了。

```Python
import smtplib
from email.header import Header
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from urllib.parse import quote

# 郵件服務器域名（自行修改）
EMAIL_HOST = 'smtp.126.com'
# 郵件服務端口（通常是465）
EMAIL_PORT = 465
# 登錄郵件服務器的賬號（自行修改）
EMAIL_USER = 'xxxxxxxxx@126.com'
# 開通SMTP服務的授權碼（自行修改）
EMAIL_AUTH = '郵件服務器的授權碼'


def send_email(*, from_user, to_users, subject='', content='', filenames=[]):
    """發送郵件
    
    :param from_user: 發件人
    :param to_users: 收件人，多個收件人用英文分號進行分隔
    :param subject: 郵件的主題
    :param content: 郵件正文內容
    :param filenames: 附件要發送的文件路徑
    """
    email = MIMEMultipart()
    email['From'] = from_user
    email['To'] = to_users
    email['Subject'] = subject

    message = MIMEText(content, 'plain', 'utf-8')
    email.attach(message)
    for filename in filenames:
        with open(filename, 'rb') as file:
            pos = filename.rfind('/')
            display_filename = filename[pos + 1:] if pos >= 0 else filename
            display_filename = quote(display_filename)
            attachment = MIMEText(file.read(), 'base64', 'utf-8')
            attachment['content-type'] = 'application/octet-stream'
            attachment['content-disposition'] = f'attachment; filename="{display_filename}"'
            email.attach(attachment)

    smtp = smtplib.SMTP_SSL(EMAIL_HOST, EMAIL_PORT)
    smtp.login(EMAIL_USER, EMAIL_AUTH)
    smtp.sendmail(from_user, to_users.split(';'), email.as_string())
```

### 發送短信

發送短信也是項目中常見的功能，網站的註冊碼、驗證碼、營銷信息基本上都是通過短信來發送給用戶的。發送短信需要三方平台的支持，下面我們以[螺絲帽平台](https://luosimao.com/)為例，為大家介紹如何用Python程序發送短信。註冊賬號和購買短信服務的細節我們不在這裡進行贅述，大家可以諮詢平台的客服。

![image-20210820194420911](https://gitee.com/jackfrued/mypic/raw/master/20210820194421.png)

接下來，我們可以通過`requests`庫向平台提供的短信網關發起一個HTTP請求，通過將接收短信的手機號和短信內容作為參數，就可以發送短信，代碼如下所示。

```Python
import random

import requests


def send_message_by_luosimao(tel, message):
    """發送短信（調用螺絲帽短信網關）"""
    resp = requests.post(
        url='http://sms-api.luosimao.com/v1/send.json',
        auth=('api', 'key-註冊成功後平台分配的KEY'),
        data={
            'mobile': tel,
            'message': message
        },
        timeout=10,
        verify=False
    )
    return resp.json()


def gen_mobile_code(length=6):
    """生成指定長度的手機驗證碼"""
    return ''.join(random.choices('0123456789', k=length))


def main():
    code = gen_mobile_code()
    message = f'您的短信驗證碼是{code}，打死也不能告訴別人喲！ 【Python小課】'
    print(send_message_by_luosimao('13500112233', message))


if __name__ == '__main__':
    main()
```

上面請求螺絲帽的短信網關`http://sms-api.luosimao.com/v1/send.json`會返回JSON格式的數據，如果返回`{'error': 0, 'msg': 'OK'}`就說明短信已經發送成功了，如果`error`的值不是`0`，可以通過查看官方的[開發文檔](https://luosimao.com/docs/api/)了解到底哪個環節出了問題。螺絲帽平台常見的錯誤類型如下圖所示。

<img src="https://gitee.com/jackfrued/mypic/raw/master/20210820195505.png" alt="image-20210820195505761" style="zoom:50%;">

目前，大多數短信平台都會要求短信內容必須附上簽名，下圖是我在螺絲帽平台配置的短信簽名“【Python小課】”。有些涉及到敏感內容的短信，還需要提前配置短信模板，有興趣的讀者可以自行研究。一般情況下，平台為了防範短信被盜用，還會要求設置“IP白名單”，不清楚如何配置的可以諮詢平台客服。

![image-20210820194653785](https://gitee.com/jackfrued/mypic/raw/master/20210820194653.png)

當然國內的短信平台很多，讀者可以根據自己的需要進行選擇（通常會考慮費用預算、短信達到率、使用的難易程度等指標），如果需要在商業項目中使用短信服務建議購買短信平台提供的套餐服務。

### 簡單的總結

其實，發送郵件和發送短信一樣，也可以通過調用三方服務來完成，在實際的商業項目中，建議自己架設郵件服務器或購買三方服務來發送郵件，這個才是比較靠譜的選擇。
