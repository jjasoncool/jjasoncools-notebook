# Apache Install #

## Windows ##

- 注意：以下心得適用於PHP7 免安裝(PHP5.2以前請使用安裝版本)
- 注意：Apache 32或64位元需與PHP相符合，因在讀取dll檔會需要相容性，且apache 與 php 的編譯VC 須為同一版本
  - **PHP5 並沒有完整支援64位元，請使用32位元版本**
  - **若PHP沒有編譯，只跑出純文字頁面，可檢查mod_headers.so是否有開啟**

- Unzip the Apache24 folder to c:/Apache24 (that is the ServerRoot in the config).
- Default folder for your your webpages is DocumentRoot "c:/Apache24/htdocs"
- Start apache in a DOS box (CMD):
    >   cd /d [Apache安裝目錄/bin]\
    >   httpd.exe -k start 啟動\
    >   httpd.exe -k shutdown 關閉
   - Windows 安裝：若啟動時發生Unclean shutdown of previous Apache run錯誤，記得查看windows的環境變數設定

★ Install as a service:
  - 必須先安裝 C++ Redistributable Visual Studio 對應版本
    (此版本對應C++ Redistributable Visual Studio 2017)
    >   httpd.exe -k install -n "service name" 安裝服務
    >   httpd.exe -k uninstall -n "service name" 移除服務
    If the "service name" option is omitted, the service name "Apache2.4" is used as the default name of the service.

    NET START "service name" 啟動服務
    NET STOP "service name" 關閉服務

    或者可以至控制台→系統及安全性→系統管理工具→服務，是否有Apache2.4存在

可以觀察apache的執行狀況
ApacheMonitor:
    Double click ApacheMonitor.exe, or put it in your Startup folder.

## LINUX 適用 ##

1. 修改 [Apache_PATH]/conf/httpd.conf (另外修改conf 後只須重啟服務即可)

        搜尋 Listen 將 80 更改為 ServerName 後的 port 號碼 (若要監聽很多port號時，可以增加多個Listen)
        例如:Listen 有 80 和 8090
        則 Localhost 則網址可以輸入 localhost:80 與 localhost:8090
        此設定影響是否能在瀏覽器瀏覽apache伺服器
        範例：
    ```conf
        Listen 80
        Listen 8090
    ```

2. 搜尋 ServerName 字串

    將#註解去掉並將www.example:80修改為127.0.0.1:8090(port 號隨Listen修改)
    此設定為讓Server辨別自身使用，因此建議設定實際IP位址，避免伺服器啟動異常
    範例：
    ```conf
        ServerName 127.0.0.1:8090
    ```

3. 若需更改安裝目錄以及文件放置目錄，則須修改**httpd.conf**內的目錄位址

    ServerName gives the name and port that the server uses to identify itself.
    This can often be determined automatically, but we recommend you specify
    it explicitly to prevent problems during startup.
    範例：
    ```conf
        DocumentRoot "E:\website-PHP"
        <Directory "E:\website-PHP">
    ```

    When you unzip to an other location, change ServerRoot in the httpd.conf,
    and change in httpd.conf the Documenroot, Directories, ScriptAlias,
    also when you use the extra folder config file(s) change to your location there.

    並修改預設開啟頁面，多加上index.php，這樣只需要到目錄存在此兩種檔案便會自動開啟
    DirectoryIndex index.html index.php

5. fastcgi

    新增這句對應 PHP_FCGI_MAX_REQUESTS，若值設定太少有可能 request 很多的時候進程會被關閉並且回報錯誤
    FcgidMaxRequestsPerProcess 10000


6. 安全性

    目錄在沒有預設首頁時，是否顯示目錄結構
    Options Indexes FollowSymLinks  更改為  Options FollowSymLinks
    當所在頁面或目錄不存在時，回送404 Not Found
    [Apache2.2] Options None 則是不允許存取
    [Apache2.4] Options None 則是頁面或目錄不存在時，不允許存取，回送403 Forbidde

    [Apache2.4] 將403轉成404
    `ErrorDocument 403 /404`

    隱藏伺服器版本資訊，避免有心他人攻擊
    `ServerTokens Prod`

    在httpd-ssl.conf httpd.conf httpd-vhosts.conf之內的設定
    DocumentRoot 都需要設定一樣的

    X-Frame-Options HTTP 回應標頭 (header) 用來指示文件是否能夠載入 <frame>, <iframe> 以及 <object>
    網站可以利用 X-Frame-Options 來確保本身內容不會遭惡意嵌入道其他網站、避免 clickjacking 攻擊
    `Header always append X-Frame-Options SAMEORIGIN`

    SSL module:
    ```conf
    <IfModule ssl_module>
        SSLRandomSeed startup builtin
        SSLRandomSeed connect builtin
    </IfModule>
    ```

7. 虛擬目錄設定

   - 可以將虛擬的目錄指向特定資料夾
    ```conf
        Alias /virtualfolder "D:/datafolder"
    ```

8. Apache Upgrading

   - Upgrading from 2.2.x See [Upgrading](httpd.apache.org/docs/2.4/upgrading.html) and [New features](httpd.apache.org/docs/2.4/new_features_2_4.html).

   - Updating from 2.3.x copy all the files over, except your changed .conf files.


9.  Apache virtual hosts
    1.  先至httpd.conf 取消Include conf/extra/httpd-vhosts.conf的註解
        ```conf
        # Virtual hosts
        Include conf/extra/httpd-vhosts.conf
        # ※注意：HttpProtocolOptions 在Apache 2.4版 defaults to Strict, 會拒絕servername 含有 "_"(底線)的名稱
        # HttpProtocolOptions now defaults to Strict, thus rejecting hostnames that have '_'
        HttpProtocolOptions Unsafe
        ```

    2.  編輯 httpd-vhosts.conf 檔案，新增以下虛擬HOST，此為共用IP模式(參照:https://httpd.apache.org/docs/trunk/vhosts/examples.html)
        ```conf
        # 在 <VirtualHost *:80> 標籤內，*號代表的是主機名稱或是IP。 例:<VirtualHost *:80 html_10:443>
        <VirtualHost *:80>
            DocumentRoot "E:/website-PHP"
            ServerName localhost
            ErrorLog "logs/local-error.log"
            CustomLog "logs/local-access.log" common
        </VirtualHost>

        <VirtualHost *:80>
            DocumentRoot "E:/website-PHP/html_2"
            ServerName html_2
            ErrorLog "logs/html_2-error.log"
            CustomLog "logs/html_2-access.log" common
        </VirtualHost>

        # 將主機的某 port 轉 80 port
        <VirtualHost *:80>
            ServerAdmin nameyearbirthday@gmail.com
            ServerName git.sk-tp.nctu.me
            ProxyPreserveHost On
            ProxyRequests off
            AllowEncodedSlashes NoDecode
            ProxyPass / http://localhost:3000/ nocanon
            ProxyPassReverse / http://localhost:3000/
            ErrorLog "logs/git-error_log"
            CustomLog "logs/git-access_log" common
        </VirtualHost>
        ```
    3.  若是有 SSL 相關的 port，應放在 httpd-ssl.conf
    ```ini
        # 若是SSL開啟的 443 port，則必須多出以下SSL開頭選項
        <VirtualHost *:443>
            ServerAdmin canceraway@gmail.com
            DocumentRoot "/var/local/web/test"
            ServerName test.canceraway.org.tw
            <Directory "/var/local/web/test">
                AddHandler fcgid-script .php
                Options +ExecCGI
                FcgidWrapper /srv/apache/cgi-bin/php.fastcgi .php
                AllowOverride None
                Require all granted
            </Directory>

            ErrorDocument 403 /pages/404.php
            ErrorDocument 404 /pages/404.php

            ErrorLog "logs/test-error_log"
            CustomLog "logs/test-access_log" common

            SSLEngine on
            SSLCertificateFile "conf/cert/test/certificate.crt"
            SSLCertificateKeyFile "conf/cert/test/private.key"
            SSLCertificateChainFile "conf/cert/test/ca_bundle.crt"

            <FilesMatch "\.(cgi|shtml|phtml|php)$">
                SSLOptions +StdEnvVars
            </FilesMatch>
            <Directory "/srv/apache/cgi-bin">
                SSLOptions +StdEnvVars
            </Directory>

            BrowserMatch "MSIE [2-5]" \
                nokeepalive ssl-unclean-shutdown \
                downgrade-1.0 force-response-1.0

            CustomLog "/srv/apache/logs/ssl_request_log" \
                    "%t %h %{SSL_PROTOCOL}x %{SSL_CIPHER}x \"%r\" %b"
        </VirtualHost>

        <VirtualHost *:443>
            DocumentRoot "E:/website-PHP/html_12"
            ServerName html_12
            ErrorLog "logs/html_12-error.log"
            CustomLog "logs/html_12-access.log" common
            SSLEngine on
            SSLProtocol all -SSLv2
            SSLCipherSuite HIGH:MEDIUM:!aNULL:!MD5
            SSLCertificateFile "D:/xampp/Apache2.2_win32/conf/server.crt"
            SSLCertificateKeyFile "D:/xampp/Apache2.2_win32/conf/server.pem"
        </VirtualHost>
    ```
    6.  **(WINDOWS)** 使用管理者權限編輯 C:\Windows\System32\drivers\etc\hosts檔案，新增host如下 `127.0.0.1 html_2`

    7.  即可至網頁直接輸入網址 http://html_2 開啟頁面

# Apache 連結 tomcat #

需要利用到 jk_module


# 安裝openSSL #

修改httpd.conf
```conf
    #載入模組mod_ssl.so，此模組是啟用SSL功能必須的。
    LoadModule ssl_module modules/mod_ssl.so
    #Apache2.4 需要多load此模組
    LoadModule socache_shmcb_module modules/mod_socache_shmcb.so
    #載入ssl的設定檔案
    Include conf/extra/httpd-ssl.conf
```


## 自我簽署(測試用) ##

1. 產生金鑰
  - 單行產出 key + crt 檔案方法:
    `openssl req -new -newkey rsa:2048 -sha256 -days 3650 -nodes -x509 -keyout server.key -out server.crt -config v3.ext -extensions v3_req`
    需要有 v3.ext 檔案 (解決GOOGLE 61版本直接封殺憑證的方法):
        [req]
        distinguished_name = req_distinguished_name
        x509_extensions = v3_req
        prompt = no

        [req_distinguished_name]
        C = TW
        ST = Taiwan
        L = Taipei
        O = CR
        OU = It
        CN = html_12

        [v3_req]
        keyUsage = nonRepudiation, digitalSignature, keyEncipherment
        extendedKeyUsage = serverAuth
        subjectAltName = @alt_names

        [alt_names]
        DNS.1=html_12

  - 傳統方法：
    產生RSA私鑰當作root CA
    使用CMD輸入
        `"[Apache安裝目錄]\bin" openssl genrsa -des3 -out rootCA.key 2048`
    這一行的意思，代表使用 openssl.exe 產生OpenSSL RSA 私鑰，使用 des3 加密模式，輸出為server.key檔案，長度為2048位元組

    自行簽署(rootCA).[會詢問如同產生CSR的問題]
        `"[Apache安裝目錄]\bin" openssl req -utf8 -x509 -new -nodes -key rootCA.key -sha256 -days 3560 -extensions v3_req -out rootCA.pem`

    產生裝置金鑰
        `"[Apache安裝目錄]\bin" openssl - genrsa -out server.key 2048`

    產生簽署需求檔 CSR (un-signed certificate called a CSR or Certificate Signing Request)
        `"[Apache安裝目錄]\bin" openssl req -new -sha256 -key server.key -out server.csr`
        使用CMD輸入
            `set OPENSSL_CONF=[Apache安裝目錄]\conf\openssl.cnf` (否則會找尋不到出錯)
            `"[Apache安裝目錄]\bin" openssl.exe req -new -sha256 -key server.key -out server.csr`
        此為使用 SHA2(sha256) 演算法，目前SHA1已被拋棄
        產生檔案的過程會詢問以下項目：
            Country Name (2 letter code) [AU]:TW
            State or Province Name (full name) [Some-State]:Taiwan
            Locality Name (eg, city) []:Taipei (城市)
            Organization Name (eg, company) [Internet Widgits Pty Ltd]:CR(組織，例如公司)
            Organizational Unit Name (eg, section) []:IT section (單位，例如公司部門等)
            Common Name (eg, server FQDN or YOUR name) []:localhost (主機的全名，也可用IP取代)
            Email Address []:cr@localhost (信箱)

            Please enter the following 'extra' attributes
            to be sent with your certificate request
            A challenge password []:(可按Enter即可)
            An optional company name []:(可按Enter即可)

        利用 rootCA 私鑰 簽署裝置的需求檔，產出產生自我簽署的憑證
        If the system is just for development and testing use, you may wish to proceed with just a self-signed certificate.
        Be aware that most browsers will inform the user that the trustworthiness of the certificate is in doubt, so this is not recommended for public-facing applications.
        "[Apache安裝目錄]\bin" openssl.exe x509 -req -utf8 -days 3650 -sha256 -extensions v3_req -in server.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out server.crt


  - 產生出PEM，無密碼加密的私鑰檔案 (開啟apache 即不須輸入密碼)
    使用CMD輸入
        `"[Apache安裝目錄]\bin" openssl.exe rsa -in server.key -out server.pem`
    This key must be protected carefully because it is used in key exchange. If the key is compromised, the system becomes vulnerable to a man in the middle attack


2. 安裝憑證
  - Copy the server.crt and server.pem into apache\conf directory
    設定 conf\extra\httpd-ssl.conf 修改如下：

```ini
    #   General setup for the virtual host
    DocumentRoot "E:/website-PHP"
    #   主機名稱(或IP) ※注意：若出現RSA server certificate CommonName (CN) does NOT match server name 1067
    #   須注意此選項是否有設定與憑證相同
    ServerName html_12:443
    #   自我簽署證書檔
    SSLCertificateFile "[Apache安裝目錄]/conf/server.crt"
    #   私鑰檔
    SSLCertificateKeyFile "[Apache安裝目錄]/conf/server.key"
```

  - 若有多個host都要使用ssl協定，則要準備多個證書
    並且複製以下設定 (此設定為指定html_10的設定)

```conf
    <VirtualHost html_10:443>

    DocumentRoot "E:/website-PHP/CRTestEnvirement/html_10"
    ServerName html_10:443
    ServerAdmin admin@example.com
    ErrorLog "D:/xampp/Apache24_win32/logs/error.log"
    TransferLog "D:/xampp/Apache24_win32/logs/access.log"

    SSLEngine on
    SSLCertificateFile "D:/xampp/Apache24_win32/conf/server_10.crt"
    SSLCertificateKeyFile "D:/xampp/Apache24_win32/conf/server_10.key"

    <FilesMatch "\.(cgi|shtml|phtml|php)$">
        SSLOptions +StdEnvVars
    </FilesMatch>
    <Directory "D:/xampp/Apache24_win32/cgi-bin">
        SSLOptions +StdEnvVars
    </Directory>

    BrowserMatch "MSIE [2-5]" \
            nokeepalive ssl-unclean-shutdown \
            downgrade-1.0 force-response-1.0

    CustomLog "D:/xampp/Apache24_win32/logs/ssl_request.log" \
            "%t %h %{SSL_PROTOCOL}x %{SSL_CIPHER}x \"%r\" %b"

    </VirtualHost>
```
  - 修改完成後重新啟動，即可使用https協定瀏覽網站

3. 使特定位址或資料夾強迫使用https瀏覽 **(Apache官網推薦做法)**
   - 使用virtualhost轉址給443
    ```conf
    <VirtualHost *:80>
        ServerName www.example.com
        Redirect "/" "https://www.example.com/"
    </VirtualHost>
    ```

4. rewrite模組，導向https範例 **(較不推薦)**
   - 編輯 [Apache安裝目錄]\conf\httpd.conf
   - 取消註解
        LoadModule rewrite_module modules/mod_rewrite.so
   - 並在<Directory> Tag 之內加上
    ```conf
        RewriteEngine On
        RewriteCond %{HTTPS} off
        # 代表主機名稱為html_12 才改寫 (若缺少這行改寫所有網頁時，使用HTTP的網頁會出現Mixed Content錯誤)
        RewriteCond %{SERVER_NAME} html_12
        RewriteRule ^(.*)$ https://%{SERVER_NAME}%{REQUEST_URI} [R=301,L]

    #-----------本機使用端-----------
        RewriteCond %{HTTPS} on
        RewriteCond %{SERVER_NAME} localhost [OR]
        RewriteCond %{SERVER_NAME} html_12
        # 當 server 名稱在本機的時候，同時已經開啟http
        RewriteCond %{REQUEST_URI} !CRTestEnvirement
        # 代表URI 不包含 CRTestEnvirement(我自己的測試目錄)，自動將位址轉到測試目錄之下，因為程式內位址為寫死
        RewriteRule ^(.*)$ https://%{SERVER_NAME}/CRTestEnvirement%{REQUEST_URI} [R=301,L]

    # 虛擬路徑
    <Directory "${FILEROOT}/CRTestEnvirement/dcs">
        RewriteEngine On
        # /dcs/1111111/foo.js => /foo.js?imono=1111111
        # /dcs/1111111/ => /?imono=1111111
        # GET會拿到資料，且 request uri 還會是 /dcs/1111111/
        RewriteRule "^([0-9]{7})/(.*)$" "$2?imono=$1"
        # 單純過濾 static
        RewriteRule "^([0-9]{7})/$" "index.php" [PT]
        RewriteRule "^([0-9]{7})/(.*)$" "$2" [PT]
    </Directory>
    ```

   - RewriteEngine 規則
        關於RewriteEngine 必須了解正規表示式
        利用 RewriteCond 設定轉址條件 (語法:RewriteCond [TestString] [CondPattern] [Flags])
            TestString 是指一個文本格式的條件，
            CondPattern 是條件參數，若是 [TestString] 條件吻合 [CondPattern]值，則執行在此句下面的 RewriteRule
            Flags 標識是是第三個參數，可以用來緊跟下一個條件，這兒用OR表示"或"，如果沒有[Flags]，則預設AND，表示"且"

        RewriteRule 則為設定轉址方式
        RewriteBase 則是設定需要修改的目錄，需在該目錄才會觸發RewriteRule條件

        L 意味著立即停止重寫操作，並不再應用其他重寫規則
        R=code (force redirect) 強制外部重定向，R=301 代表永久重新定址
        NC 不區分大小寫

  - RewriteRule 參數
    %{SERVER_NAME} = 伺服器名稱，一般為網站位址
    %{SERVER_PORT} = 使用port號
    %{DOCUMENT_ROOT} = 文件目錄，同apache 設定之原始目錄


4. 修改 httpd-vhosts.conf
    將需要修改的vhost 增加以下選項 (對照Apache virtual hosts 第2點)
        SSLEngine on
        SSLProtocol all -SSLv2
        SSLCipherSuite HIGH:MEDIUM:!aNULL:!MD5
        SSLCertificateFile "D:/xampp/Apache2.2_win32/conf/server.crt"
        SSLCertificateKeyFile "D:/xampp/Apache2.2_win32/conf/server.pem"

※補充：若出現Access to Font at 'XXX' from origin 'OOO' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.
    請取消註解
        LoadModule headers_module modules/mod_headers.so
    並在<Directory> Tag 之內加上
        Header set Access-Control-Allow-Origin "*"
        若加這個報錯 有可能 mod_headers.so 沒有被取消註解


# PHP Install #

## WINDOWS 適用 ##
- PHP解壓後無需其他安裝，只需修改相關文件即可：
  - ★**需修改apache內的httpd.conf** 新增至文件LoadModule的部分：
  - PHP7
    ```ini
    # load php module and setting file (php.ini)
    LoadModule php7_module "[PHP安裝路徑]/php7apache2_4.dll"
    PHPiniDir "[PHP安裝路徑]"
    # php file type support
    <IfModule php7_module>
        AddHandler application/x-httpd-php .php
        AddType application/x-httpd-php .php .html
    </IfModule>
    ```

  - PHP5 需另外從[此處](https://www.apachelounge.com/download/additional/)下載可對應apache 2.4的dll
    ```ini
    # windows需下載apache2.4 VC10
    LoadModule php5_module "[PHP安裝路徑]/php5apache2_4.dll"
    # configure the path to php.ini
    PHPIniDir "[PHP安裝路徑]"
    # Add to your httpd.conf
    <IfModule php5_module>
    AddHandler application/x-httpd-php .php
    AddType application/x-httpd-php .php .html
    </IfModule>
    ```
  - **fast-cgi** 設定
    ```ini
    LoadModule fcgid_module modules/mod_fcgid.so

    <IfModule fcgid_module>
        # 10 hrs: in case you have long running scripts, increase FcgidIOTimeout
        FcgidIOTimeout 36000
        FcgidBusyTimeout 3600
        FcgidConnectTimeout 16
        FcgidMaxRequestsPerProcess 0
        FcgidMaxProcesses 50
        FcgidMaxRequestLen 81310720
    </IfModule>

    # 這邊以下放到 virtualhost 依據不同網址用不同版本php開啟
    # Location php.ini:
    FcgidInitialEnv PHPRC "D:/xampp/php-5.3.29"
    FcgidInitialEnv PHP_FCGI_MAX_REQUESTS 1000
    <Files ~ "\.php$>"
        AddHandler fcgid-script .php
        Options +ExecCGI
        FcgidWrapper "D:/xampp/php-5.3.29/php-cgi.exe" .php
    </Files>

    ```
  - CURL 加密驗證
    ```ini
    # 這是Mozilla基金會憑證存放位置
    # 使用CURL來回傳遞機敏資料時，需要有此憑證
    # https://curl.haxx.se/ca/cacert.pem
    # 可以設定排程執行
    curl.cainfo = "憑證存放位置"
    ```
    ```php
    /* gets the data from a URL */
    function get_data($url) {
        $ch = curl_init();
        $timeout = 5;
        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, 0);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
        curl_setopt($ch, CURLOPT_CONNECTTIMEOUT, $timeout);
        curl_setopt($ch, CURLOPT_HTTP_VERSION, CURL_HTTP_VERSION_1_1);
        curl_setopt($ch, CURLOPT_HEADER, 0);
        $data = curl_exec($ch);
        curl_close($ch);
        return $data;
    }

    $returned_content = get_data('https://curl.haxx.se/ca/cacert.pem');

    // echo $returned_content;

    file_put_contents(__DIR__ . '/cacert.pem', $returned_content);
    ```

## LINUX 適用 ##
    ```ini
    # LINUX
    LoadModule php7_module        modules/libphp7.so
    ```

  - **fast-cgi** 設定
    - 設定 etc/php-fpm.conf 如下：

- 將php.ini-production文件改名為php.ini，並用文本編輯器將其打開做以下修改：
- LINUX 需要將原始檔內的php.ini丟至編譯時指定的路徑，**預設為PREFIX/lib**
  - 取消 extension_dir = "ext"的註解，這個是指向擴展庫目錄的路徑
    **預先編譯的模組不需要設定，這邊是要新增後來加入的動態模組**
    ```conf
    ; Directory in which the loadable extensions (modules) reside.
    ; http://php.net/extension-dir
    ; extension_dir = "./"
    ; On windows:
    extension_dir = "[PHP安裝路徑]/ext"

    再將需要的擴充套件取消註解
    在這邊我們將mysql 的擴充套件加入(將註解;消去)
    extension=php_mysqli.dll
    extension=php_pdo_mysql.dll
    extension=php_openssl.dll
    ```

- 設定php 執行的時區
    ```conf
    date.timezone = "Asia/Taipei"
    ```
- 設定是否顯示編譯錯誤
    ```conf
    display_errors = On
    ```

- 設定上傳檔案大小(有需求即更改)
    ```conf
    upload_max_filesize =2M
    post_max_size = 8M
    ```

- 設定上傳檔案的暫存位置
    ```conf
    upload_tmp_dir="E:\website-PHP\Temp"
    ```

- 設定PHP 可以辨識 <? 的簡寫Tag，避免程式內容為簡寫的TAG，導致出錯
    ```conf
    short_open_tag = On
    ```
    >備註：<?= 從PHP 5.4之後永遠預設開啟，無論 php.ini 是否有設定short_open_tag

- mysql 錯誤顯示
  - mysql.trace_mode
    預設不開啟，但在開發者模式會開啟提醒result set 是否有釋放
    可能會造成include 其他 PHP 報錯

- 安全性
  - 預設開啟，允許fopen URL物件像是開啟檔案，主要提供ftp或http檔案連結使用
    ```conf
    allow_url_fopen = On
    ```

  - 5.2新增選項，預設關閉，允許fopen使用以下函數開啟檔案 include, include_once, require, require_once
    ```conf
    allow_url_include = Off
    ```

  - 不要在回傳的header中顯示PHP的版本，避免有心人攻擊
    ```conf
    expose_php = Off
    ```

  > [升級5.3] mysqlnd cannot connect to MySQL 4.1+ using the old insecure authentication
  - mysql 內的設定：SHOW VARIABLES LIKE 'old_passwords'; 需要為off
    必須將所有密碼知道清楚之後，重新更新密碼
    (因為安全性需要將mysql 密碼加密方式更改為更強的方式，但PHP仍然可以連接)
    UPDATE mysql.USER SET PASSWORD = PASSWORD('密碼原始碼') WHERE USER = '使用者帳號';
    FLUSH PRIVILEGES;
    才可以解決此問題

  > [5.4後被刪除]register_globals
  - 可以不用寫 $_GET["變數"]，即可直接用 $變數 取得資料，但有安全性問題

**新增一檔案為php_info.php 內文<?php phpinfo(); ?>，開啟網頁測試是否可以開啟**

# PHP composer 安裝 #

## WINDOWS ##
**手動安裝**
1. 在 https://getcomposer.org/download/ 取得 composer.phar 檔案

2. 因為需要將 composer 加到環境變數，以利在windows系統可以直接下指令
   - 為了便利，將composer.phar放到xampp/php資料夾之下，只要利用PHP的path即可
   - 在 composer.phar 旁邊建立一個新的 composer.bat 檔案，在command line下
    `echo @php "%~dp0composer.phar" %*>composer.bat`
    會產生出一個**composer.bat**檔案
   - 下指令 `composer -V` 應會輸出
    >   Composer version 1.0.0 2016-01-10 20:34:53
    代表已設定成功

## LINUX ##



# PHP 開發環境 in VSCODE #

為啟用 VSCODE PHP DEBUG 功能 請至https://xdebug.org/download.php
下載符合PHP 版本號 以及是否為 thread-safe(TS)
將下載的DLL檔案放置至PHP的 ext 之下 (extension_dir = "[PHP安裝路徑]/ext" 必須優先設定好)
並在php.ini增加
```ini
    [XDebug]
    zend_extension = xdebug-2.5.5-7.1-vc14-x86_64.dll
    xdebug.remote_enable = 1
    xdebug.remote_autostart = 1
    # 陣列顯示深度(層數)
    xdebug.var_display_max_depth = 4
    # (PHP5.2 使用)zend_extension_ts = D:\xampp\php-5.2.17\ext\php_xdebug-2.1.2-5.2-vc6.dll
```
重啟Server
開啟php_info.php 找尋是否有Xdebug 字樣
檔案前面有**php_**不用加到zend_extension內，否則就要使用完整路徑

進入VSCODE 偵錯→新增組態→XDEBUG
setting 如下：
```json
    "launch": {
        "version": "0.2.0",
        "configurations": [
            /* 必須在php內安裝XDebug */
            {
                "name": "Listen for XDebug",
                "type": "php",
                "request": "launch",
                "port": 9000,
                "xdebugSettings": {
                    "max_children" : 50,
                    "max_data": 0,
                    "max_depth" : 3
                }
            }
        ]
    },
```
設定完成後即可下中斷點，利用code runner或是直接使用瀏覽器開啟該頁面執行時，即可針對斷點debug
