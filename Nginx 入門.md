---
tags: 黑客社, 簡報
title: Nginx 入門
---

[TOC]

## Nginx 入門

---

### 簡單介紹

----

- Nginx (Engine-X)
- 高效能網頁伺服器
    - 非同步（Async）
    - epoll I/O 模式
    - 靜態、模組化
通常使用 Nginx 最常用於**反向代理**與**伺服器快取**

---

非同步？
- 異步(Async) vs. 同步(Sync)
    - Async => 發送要求到收到結果的過程中可以做其他事情

----

- 很好懂的例子
    - 你填好單子送到櫃檯，然後……
    - 他辦完再叫你的號碼→Async
    - 要站在櫃檯等他辦完→Sync
    - Async 的狀況，因為你不用在櫃檯等待
    - 所以你可以跑很多的櫃檯送單子

###### [Source](https://medium.com/@hyWang/%E9%9D%9E%E5%90%8C%E6%AD%A5-asynchronous-%E8%88%87%E5%90%8C%E6%AD%A5-synchronous-%E7%9A%84%E5%B7%AE%E7%95%B0-c7f99b9a298a)

----

- Async 是一種辦事**模式**而非**能力**
    - 所以 Async != 可以一次處理很多需求

----

epoll 異步 I/O 模式？
- epoll 異步 I/O 模式
    - 取代 select
    - select 與 epoll
        - 飯店同時 checkin 的狀況
        - 一堆人在 checkin vs. 導遊幫所有人 checkin
    - Apache 採用 select
    - 但是 select 每個系統平台都有實做，epoll 並不是很普及

----

- 模組
    - 萬事皆為模組，連 http 也是模組
        - Nginx 本體負責轉發給模組～

----

- 靜態
    - 靜態的好處 -> 能夠預先讀取，加速載入
    - 壞處 -> 增加新模組時需要重新編譯一次
        - Nginx 1.19.11 之後雖然可以動態載入，但模組仍要與 Nginx 同時編譯
        - 這邊就變成 Apache 的好處了

---

### 基本操作

----

安裝它？
```bash=
sudo pacman -S nginx # Arch Linux
sudo apt install nginx # Ubuntu/Debian
```

----

啟動它？
```bash=
sudo nginx

# 如果你要用 systemd 管理的話
# 但要先sudo systemctl enable nginx
sudo systemctl start nginx 
```
停止它？
```bash=
sudo nginx -s quit 
sudo nginx -s stop # 快速結束
sudo systemctl stop nginx # 如果你想用 systemd 管理的話
```

----

![](https://i.imgur.com/POw99hg.png)

----

重新載入設定
```bash=
sudo nginx -s reload
sudo systemctl restart nginx # 如果你想用 systemd 管理的話
```

---

### 設定檔

----

在 Arch Linux 的位置：
```bash=
/etc/nginx/nginx.conf
```
[詳細內容](https://gist.github.com/Nesquate/3a5dce49a1c5a4a44fdb6ffb37cfc4bd)

---

### 改一些設定？

----

Ex : 改根目錄  

預設的位置（`/usr/share/nginx/html`）讓我很難管理網站，  
那我只好改一下根目錄的位置。

----

在 `http -> server -> location / -> root` 的那一項：

```nginx=
root   /usr/share/nginx/html;
```

----

我改成這樣：

```nginx=
root   /home/www;
```

放在 /home 底下我比較好管...

----

但如果我今天目錄長這樣：
<pre>
www
├── <font color="#3465A4"><b>html</b></font>
│   ├── index.html
└── <font color="#3465A4"><b>images</b></font>
    └── <font color="#75507B"><b>anPicture.png</b></font>
</pre>

----

網站根目錄可以改 root 就好了  
可是 images 怎麼辦？

----

那就再加一個 location 啊！

```nginx=
location /images {
    root   /home/www;
}
```

----

當瀏覽器傳送訪問 `http://127.0.0.1/images` 的時候，  
Nginx 就會先去 `/home/www` 底下找  
而不是先找 `/home/www/html`  

----

如果有多個 location block 的話， Nginx 會以 location 後面**名稱最長**的優先。

---

### Virtual Host 實做

----

你的伺服器應該不太可能會只掛一個站點吧？

----

使用 `server` block directive 來管理多個站點

----

為了方便管理，可以在 `/etc/nginx` 新建兩個資料夾：
- sites-available : 可用的站點
- sites-enabled：已經上線的站點

透過多資料夾來管理不同站點的狀態 

----

首先在`nginx.conf` 上的 `http` 的 block 內新增  
```nginx=
include site-enabled/*.conf;
```

----

接著在`site-available`資料夾底下新增站點設定檔  

----

從頭自幹設定檔會不太輕鬆，那可以把`nginx.conf`的`server` block 給複製過去  

----

然後再稍微修改一下：
```nginx=
server {
        listen       80;
        # 設定 server name 指向網址
        server_name  test.nex www.test.nex;
        # root 資料夾也要改一下
        root /home/www/test.nex;

        location / {
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
    }
```

----

新增了站點設定檔之後別忘了要做硬連結：
```bash=
sudo ln /etc/nginx/sites-available/test.nex.conf /etc/nginx/sites-enabled/test.nex.conf
```

----

通通完成之後記得**重啟 Nginx**  
但在這之前先進行一下文法檢查：
```bash=
sudo nginx -t
```
出現
```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```
就可以重啟了

----

然後在指定的 root 資料夾新增 `index.html` 來測試一下  
確定有跑出網頁就完成了 :D  

---

### 反向代理

----

#### 正向代理 vs. 反向代理

----

簡單來說  
正向代理：Server 不知道 Client 是誰  
反向代理：Client 不知道 Server 是誰

----

Nginx 可以幫你做反向代理  
達到快取以及負載平衡的效果

----

實做：
```nginx=
server {
        listen       80;
        server_name  py.nex www.py.nex;
	
	# root /home/www/html;

        location / {
            index  index.html index.htm;
            # Proxy 反向代理指定位置
            proxy_pass http://127.0.0.1:8000; 
            
            # 設定要傳什麼 header 給伺服器
            # 什麼都沒設定的話只會傳送 Nginx 伺服器的資訊
            proxy_set_header X-Real-IP $remote_addr; # 傳 Client 的 IP，可偽造
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; # HTTP request 真實 IP，可偽造
            proxy_set_header X-Forwarded-Proto $http_x_forwarded_proto; # 識別協定，http or https
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
    }
```

---

### Nginx + PHP

----

Nginx 只有負責靜態網頁的部份  
如果需要動態網頁的話，這時候就需要 FastCGI

----

CGI (Common Gateway Interface)  
通用閘道器介面  
提供一個橋樑讓 Web Server  
與 Web Application 做溝通

----

可是 CGI 有效能慢的問題  
所以又生出了 FastCGI 改善原 CGI 的效能問題

----

#### 安裝與設定 PHP-FPM

----

安裝：
```bash=
sudo pacman -S php-fpm
# pacman 會順便幫你把 php 7.4 裝好
```
啟動它：
```bash=
sudo systemctl enable php-fpm
sudo systemctl start php-fpm
```

----

設定 `nginx.conf`（在`server` block中加入）：
```nginx=
location ~ \.php$ { # 處理所有副檔名為 .php 的檔案
        # 用 unix domain socket 比 tcp socket 快
        fastcgi_pass   unix:/var/run/php-fpm/php-fpm.sock;
        
        # 如果有 http://localhost/abc.php/ 這種狀況
        # 那就預設導引到 index.php 上
        fastcgi_index  index.php;
        
        # 引用常見大家會設定的設定檔
        # 設定檔由 Nginx 提供
        include        fastcgi.conf;
 	}
```

----

最後記得
- 重啟 Nginx
- 重啟 PHP-FPM

然後新增 php 檔案，給一些 php 的內容
```php=
<?php phpinfo(); ?>
```
有跑出來就安裝成功了

---

End...




