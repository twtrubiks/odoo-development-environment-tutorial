# odoo-development-environment-tutorial

建立 odoo 開發環境 ( source code )

本篇文章將教大家如何使用 odoo source code 建立開發環境,

如果你想要用 docker 建立, 可參考 [利用 docker 快速建立 odoo 環境](https://github.com/twtrubiks/odoo-docker-tutorial).

延伸閱讀 [手把手教大家撰寫 odoo 的 addons - 進階篇](https://github.com/twtrubiks/odoo-demo-addons-tutorial)

## 目錄

1. [建立 odoo 開發環境](https://github.com/twtrubiks/odoo-development-environment-tutorial#odoo-development-environment-tutorial) - [Youtube Tutorial - 如何建立 odoo 開發環境 - odoo13 - 從無到有](https://youtu.be/Yazci5Rd0p4)

2. [Odoo VSCode Debug](https://github.com/twtrubiks/odoo-development-environment-tutorial#odoo-vscode-debug) - [Youtube Tutorial - 如何在 VS Code 中 Debug - odoo13](https://youtu.be/cV8Sm5yYR38)

環境為 Ubuntu 18.04, 非常不推薦 Windows (你會踩雷):scream:

## Prepare

需要準備 Ubuntu 18.04 + odoo (source code) + postgresql + wkhtmltopdf + VS Code。

odoo 我們留在最後再安裝:smile:

### Ubuntu 18.04

前面說過了, 不推薦 Windows 開發環境, 你會被 debug 搞死, 相信我:smirk:

而且會遇到不少很怪的問題。

注意:exclamation::exclamation: 如果你是使用 Ubuntu 20.04, 預設的 python 是 3.8,

對 odoo 來說太新了, 安裝會有很多問題, 建議安裝 python3.6 (這版本裝 odoo 比較好建立環境)

`sudo apt-get install -y python3.6-venv`

### postgresql

這邊你可以選擇安裝到本機, 但我自己是喜歡把 db 這部份安裝到 docker,

[docker-compose.yml](https://github.com/twtrubiks/odoo-development-environment-tutorial/blob/master/odoo-db/docker-compose.yml)

```ymal
version: '3.5'
services:

  db:
    image: postgres:10.9
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_DB=postgres
      - POSTGRES_USER=odoo
      - POSTGRES_PASSWORD=odoo
      - PGDATA=/var/lib/postgresql/data/pgdata
    volumes:
      - odoo-db-data:/var/lib/postgresql/data/pgdata

volumes:
  odoo-db-data:
```

直接執行 `docker-compose up` 即可, 也可以讓他在背景跑 `docker-compose up -d`。

![alt tag](https://i.imgur.com/IcSt8mn.png)

然後建議依照 [在 Linux 中自動啟動 docker](https://github.com/twtrubiks/docker-tutorial/tree/master/docker-auto-run-linux) 讓他開機自動啟動。

(這樣就不用每次都要去啟動 db 了)

### pgadmin4

可參考 [利用 docker 快速建立 pgadmin4 以及 Ubuntu 本機如何安裝 pgadmin4](https://github.com/twtrubiks/docker-pgadmin4-tutorial)

建議把 postgresql-client-12 安裝起來, 不然 odoo 可能無法還原備份 db.

`sudo apt install postgresql-client-12`

這邊提醒一下, 主要是看你的 postgresql 是使用哪個版本的,

如果你同時安裝了 postgresql12 以及 postgresql13,

你就再安裝 `sudo apt install postgresql-client-13`.

可以到 `/usr/share/postgresql` 確認你安裝了哪些版本.

### wkhtmltopdf

尋找對應的版本安裝即可 [wkhtmltopdf](https://wkhtmltopdf.org/downloads.html)

```cmd
wkhtmltopdf --version
```

![alt tag](https://i.imgur.com/aLaE0Dh.png)

這邊推薦的版本為 0.12.5。

如果你在 odoo 列印 PDF 時, 遇到以下的錯誤

```text
Wkhtmltopdf failed (error code: -8). Message: b'' error
```

![alt tag](https://i.imgur.com/0uac0Ok.png)

這是字型的問題, 請安裝字型

```cmd
sudo apt install ttf-mscorefonts-installer
```

### odoo

clone odoo repo

```cmd
git clone https://github.com/odoo/odoo.git
```

確認是否為 odoo 13 branch ( 預設為 odoo 13 )

![alt tag](https://i.imgur.com/hYmP7W4.png)

接著不要急, 來建立 odoo python 環境

```cmd
python3 -m venv odoo13
```

![alt tag](https://i.imgur.com/TrIwBRY.png)

如果出現以下錯誤,

![alt tag](https://i.imgur.com/wRxLIjU.png)

請安裝 python3-venv

```cmd
sudo apt-get install python3-venv
```

然後再次執行 `python3 -m venv odoo13` 即可。

接著啟動環境,

```cmd
source odoo13/bin/activate
```

![alt tag](https://i.imgur.com/Ci0F62d.png)

接著是安裝 requirements.txt ( 這個檔案在 odoo 的 source code 中 )

```cmd
pip3 install -r requirements.txt
```

我會建議安裝 requirements.txt 前, 先把 wheel 裝起來

```cmd
pip3 install wheel
```

![alt tag](https://i.imgur.com/CwW8PiY.png)

安裝 requirements.txt 時, 可能會出現類似下面的錯誤訊息。

可能出現的錯誤一,

```cmd
error: command 'x86_64-linux-gnu-gcc' failed with exit status 1
```

執行以下指令即可, 可參考 [issues/2115](https://github.com/scrapy/scrapy/issues/2115)

```cmd
sudo apt-get install python3 python-dev python3-dev \
     build-essential libssl-dev libffi-dev \
     libxml2-dev libxslt1-dev zlib1g-dev \
     python-pip
```

這邊補充一下, 建議依照你的 python 版本安裝, 例如你今天安裝了 python3.8,

就執行 `sudo apt-get install python3.8-dev`

以此類推.

可能出現的錯誤二,

python-ldap 安裝失敗,執行以下指令即可,

```cmd
sudo apt-get install libsasl2-dev python-dev libldap2-dev libssl-dev
```

可能出現的錯誤三,

有時候 psycopg2 會安裝失敗(odoo15), 建議可以改安裝 psycopg2-binary.

```cmd
pip3 install psycopg2-binary
```

可能出現的錯誤四,

有時候更新 python 時 (自己遇過一次), 某個套件竟然自己壞了,

錯誤訊息如下,

```text
ImportError: /python3.6/site-packages/lxml/etree.cpython-36m-x86_64-linux-gnu.so: undefined symbol: PyFPE_jbuf
```

解法方法,

```cmd
pip3 install --upgrade --force-reinstall --no-binary :all: lxml==3.7.1
```

基本上到這邊可以稍微休息一下.

解決後再重新安裝 requirements.txt 即可.

### odoo16

這邊紀錄一下, odoo16 環境的建立,

首先是 python 的版本, python 3.9 or python 3.8 都可以,

如果出現底下的錯誤訊息,

```text
ERROR: Failed building wheel for python-ldap
```

一樣安裝下面指令就對了(請注意自己的 python 版本)

```python
sudo apt-get install build-essential python3.9-dev libsasl2-dev libldap2-dev libssl-dev
```

另外是如果你有佈署, 會發現有一點點不一樣,

因為 odoo16 開始 longpolling 變 websocket 了 (效能更好),

所以設定上會有點不同, 詳細說明請參考官方文件

[https://www.odoo.com/documentation/16.0/administration/install/deploy.html](https://www.odoo.com/documentation/16.0/administration/install/deploy.html)

### 設定 odoo 環境

先確認 db 有啟動。

設定 [odoo.conf](odoo.conf), 我會建議建立一個資料夾, 比較好維護,

```conf
[options]
addons_path = /home/twtrubiks/work/odoo13/addons
data_dir = /home/twtrubiks/Downloads/odoo-git/odoo-data
db_host = localhost
```

`addons_path` 就是 addons 的位置，通常都會有很多，使用逗號隔開即可。

( 我自己是都會給 addons 權限 `sudo chmod -R 777 folder` , 因為之前有踩過雷 )

`data_dir` 保存 odoo 資料夾。

`db_host` 為 db 的 host。

![alt tag](https://i.imgur.com/LntDbDj.png)

執行以下指令

```cmd
python3 odoo-bin -w odoo -r odoo -c /home/twtrubiks/work/odoo13/config/odoo.conf
```

`-w` db_password

`-r` db_user

`-c` config 路徑. 當然, 你也可以不要用 config , 直接在 command 中下設定

`--stop-after-init` 當初始化結束後, 就會自動關閉 server (不管安裝成功或失敗)

更多可參考 [Command-line interface: odoo-bin](https://www.odoo.com/documentation/13.0/reference/cmdline.html)。

![alt tag](https://i.imgur.com/gNkDfJ3.png)

瀏覽 [http://0.0.0.0:8069/](http://0.0.0.0:8069/), 成功看到畫面了,

![alt tag](https://i.imgur.com/xEALOWg.png)

### Odoo VSCode Debug

[Youtube Tutorial - 如何在 VS Code 中 Debug - odoo13](https://youtu.be/cV8Sm5yYR38)

開發環境非常建議使用 Linux , 我使用 Ubuntu 18.04。

( 注意, windows 用戶需要做其他的設定, 否則你會發現無論如何啟用 debug 時, 都會直接關閉 )

如果不懂 VSCode, 可參考 [Python in Visual Studio Code](https://github.com/twtrubiks/vscode_python_note)。

VSCode 的設定可參考 [launch.json](https://github.com/twtrubiks/odoo-development-environment-tutorial/blob/master/.vscode/launch.json)

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "odoo",
            "type": "python",
            "request": "launch",
            // "stopOnEntry": true,
            "pythonPath": "/home/twtrubiks/Documents/venv/odoo13/bin/python3",
            "program": "${workspaceFolder}/odoo-bin",
            "args": [
                "-w",
                "odoo",
                "-r",
                "odoo",
                // "-d",
                // "odoo",
                "-c",
                "/home/twtrubiks/work/odoo13/config/odoo.conf"
            ]
        }
    ]
}
```

`pythonPath` 設定 python 環境 ( venv 的位置 )。

`program` odoo-bin 的位置。

`-d` 指定 odoo database。

先把 Expense (hr_expense) 裝起來 (我們將使用它來測試中斷點)，

![alt tag](https://i.imgur.com/Yf4nHww.png)

建立中斷點，中斷位置是 `addons/hr_expense/models/hr_expense.py` 裡的 `def create`
(當建立檔案時, 會進入這個 function 中)

![alt tag](https://i.imgur.com/SV4glxI.png)

開啟 VSCode debug ( 快捷按鍵 F5 ),

![alt tag](https://i.imgur.com/9jU39HC.png)

到 odoo 中建立一筆 hr_expense, 當你要產生一筆 sheet 時,

![alt tag](https://i.imgur.com/ptXxcXa.png)

成功進入中斷點

![alt tag](https://i.imgur.com/QPc994W.png)

#### Odoo VSCode Debug - attach

這邊補充另一種方法 debug, 使用 attach 的方式,

`request` 主要有 `launch` 和 `attach`,

需要安裝 `pip install debugpy`,

可參考 [Python Debugger](https://github.com/twtrubiks/vscode_python_note?tab=readme-ov-file#python-debugger),

[launch.json](https://github.com/twtrubiks/odoo-development-environment-tutorial/blob/master/.vscode/launch.json) 設定如下,

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "odoo attach",
            "type": "debugpy",
            "request": "attach",
            "connect": {
                "host": "localhost",
                "port": 19000
            },
        },
    ]
}
```

先打開一個 terminal 執行

```cmd
python3 -m debugpy --listen 0.0.0.0:19000 odoo-bin -d odoo -c config/odoo.conf
```

注意兩邊設定的 port 要一樣, 這邊統一設定 19000 port,

再開一個 terminal 去執行 odoo vscode 的中斷點, 即可順利 debug.

通常這種方式使用在已經執行的外部程式, 或是你執行在 docker 中.

### 說明 dbfilter

這邊特別說明一下 [odoo.conf](odoo.conf) 中的 dbfilter

```conf
[options]
......
;The '^' means start of line and the '$' end of line,
;so '^%d$' only filter db with name 'hostname' following the example.
;dbfilter = ^%d$
```

簡單說, 就是可以透過 dbfilter 去選擇(濾)對應的 db (透過網址),

因為一個 odoo 可以有非常多的 db, 但你可能不想讓A客戶看到B客戶的資料庫,

就很適合使用 dbfilter 這個參數.

`dbfilter = ^%d$` 這個 `^` 代表的是開始的符號, `$` 代表的是結束的符號.

所以如果今天這樣設定, db 名稱有, `twtrubiks`  `twtrubiks01`  `twtrubiks02`,

然後你的網址是 `www.twtrubiks.com`, 當你進去網址時, 只會看到 `twtrubiks` 這個 db:exclamation:

如果你也想看到 `twtrubiks01`  `twtrubiks02`, 這樣要改成 `dbfilter = ^%d`

也就是把 `$` 拿掉.

看文字說明可能很複雜:scream: 大家可以自己玩玩看, 其實是不難的哦:smile:

延伸閱讀 [手把手教大家撰寫 odoo 的 addons - 進階篇](https://github.com/twtrubiks/odoo-demo-addons-tutorial)

## Reference

* [odoo13 documentation](https://www.odoo.com/documentation/13.0/setup/install.html#linux)
* [Odoo](https://www.odoo.com/)

## Donation

文章都是我自己研究內化後原創，如果有幫助到您，也想鼓勵我的話，歡迎請我喝一杯咖啡:laughing:

綠界科技ECPAY ( 不需註冊會員 )

![alt tag](https://payment.ecpay.com.tw/Upload/QRCode/201906/QRCode_672351b8-5ab3-42dd-9c7c-c24c3e6a10a0.png)

[贊助者付款](http://bit.ly/2F7Jrha)

歐付寶 ( 需註冊會員 )

![alt tag](https://i.imgur.com/LRct9xa.png)

[贊助者付款](https://payment.opay.tw/Broadcaster/Donate/9E47FDEF85ABE383A0F5FC6A218606F8)

## 贊助名單

[贊助名單](https://github.com/twtrubiks/Thank-you-for-donate)

## License

MIT license
