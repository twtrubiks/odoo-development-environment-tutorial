# odoo-development-environment-tutorial

建立 odoo 開發環境 ( source code )

* [Youtube Tutorial - 如何建立 odoo 開發環境 - odoo13 - 從無到有](https://youtu.be/Yazci5Rd0p4)

本篇文章將教大家如何使用 odoo source code 建立開發環境,

如果你想要用 docker 建立, 可參考 [利用 docker 快速建立 odoo 環境](https://github.com/twtrubiks/odoo-docker-tutorial)

環境為 Ubuntu 18.04, 非常不推薦 Windows (你會踩雷):scream:

## Prepare

需要準備 Ubuntu 18.04 + odoo (source code) + postgresql + wkhtmltopdf + VS Code。

odoo 我們留在最後再安裝:smile:

### Ubuntu 18.04

前面說過了, 不推薦 Windows 開發環境, 你會被 debug 搞死, 相信我:smirk:

而且會遇到不少很怪的問題。

### postgresql

這邊你可以選擇安裝到本機, 但我自己是喜歡把 db 這部份安裝到 docker,

[docker-compose.yml](https://github.com/twtrubiks/odoo-development-environment-tutorial/blob/master/odoo-db/docker-compose.yml)

```ymal
version: '3.5'
services:

  db:
    image: postgres:10
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

### wkhtmltopdf

尋找對應的版本安裝即可 [wkhtmltopdf](https://wkhtmltopdf.org/downloads.html)

```cmd
wkhtmltopdf --version
```

![alt tag](https://i.imgur.com/aLaE0Dh.png)

這邊推薦的版本為 0.12.5。

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

可能出現的錯誤二,

python-ldap 安裝失敗,執行以下指令即可,

```cmd
sudo apt-get install libsasl2-dev python-dev libldap2-dev libssl-dev
```

基本上到這邊可以稍微休息一下。

解決後再重新安裝 requirements.txt 即可。

### 設定 odoo 環境

先確認 db 有啟動。

設定 `odoo.conf`, 我會建議建立一個資料夾, 比較好維護,

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

`-w` db_password。

`-r` db_user。

`-c` config 路徑。 當然, 你也可以不要用 config , 直接在 command 中下設定。

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
