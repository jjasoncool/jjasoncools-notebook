# CentOS7 & fedora #
## 以下設定若為 fedora 專有套件管理 將會使用dnf指令 ##

### 基本設定 ###
- 禁止 root 使用 ssh 登入
    >   CentOS 7 預設容許任何帳號透過 ssh 登入，包括 root 和一般帳號，為了不讓 root 帳號被黑客暴力入侵，我們必須禁止 root 帳號的 ssh 功能，事實上 root 也沒有必要 ssh 登入伺服器，因為只要使用 su 或 sudo (當然需要輸入 root 的密碼) 普通帳號便可以擁有 root 的權限。
  1. 使用 vim (或任何文本編輯器) 開啓 **/etc/ssh/sshd_config**，尋找：\
  `PermitRootLogin yes`
  2. 修改為：\
  `PermitRootLogin no`
  3. 最後輸入以下指令重新啟動 sshd：\
  `systemctl restart sshd.service`
  - 指令自動完成
  `yum install bash-completion*`

- 啟用/設定網路
  - `ip link` 列出所有網路卡
  - `ip link set eth0 up` 開啟 eth0 的網路卡
  - `vi /etc/sysconfig/network-scripts/ifcfg-enp0s3` 將ONBOOT改為=yes

- ssh 免密碼登入(git 的 ssh 設定也是相同原理)
  - `ssh-keygen`
    >   Generating public/private rsa key pair.
    >   Enter file in which to save the key (/home/jason/.ssh/id_rsa): /home/jason/.ssh/id_rsa.rasp
  - `ssh-copy-id -i ~/.ssh/id_rsa.rasp.pub jason@192.168.50.201`
  - vscode ssh config
    ```config
        Host 192.168.50.201
        HostName 192.168.50.201
        User jason
        IdentityFile ~/.ssh/id_rsa.rasp
    ```
  - [git 的 ssh 設定](../VCS/Git%20學習心得.md)

- **最小型安裝**設定螢幕省電
  - 建立一個bash file (ex:/usr/local/sbin/screenblank.sh)
  - `setterm --term linux --blank 3 --powerdown 5 >> /var/log/crontab/screenblank.log 2>&1 > /dev/tty0`
  - `echo hello world! > /dev/tty0` 可以知道 `> /dev/tty0` 可以將指令輸出至螢幕內
  - 可以將此行命令放至crontab中讓開機可被執行 (使用 root)
  - `@reboot` 參數代表開機會執行的動作
  - `@reboot sh /usr/local/sbin/screenblank.sh`

- **repository**
  - 列出目前已安裝套件
    `rpm -qa`
    `dnf list --installed gnome*`
  - 列出自訂新增的 repository
    - `yum repolist`
    - 刪除或新增設定檔 `/etc/yum.repos.d/`
  - 安裝舊版的軟體包
    `dnf downgrade code-1.56.2`

- 安裝**桌面GUI**
  - `yum groupinstall GNOME "X Window System" fonts`
  - GNOME 一些好用管理工具 `yum install gnome-tweaks gnome-shell-extension-dash-to-dock gnome-shell-extension-top-icons`

- 安裝**xrdp** (方便可以使用windows rdp)
  - `yum install epel-release xrdp xorgxrdp -y`
  - 需先安裝此套件再行安裝NVDIA驅動程式
  - 修改主要配置檔 **/etc/xrdp/xrdp.ini**
    ```ini

    ```
  - 新增防火牆允許範圍
    `firewall-cmd --permanent --add-port=3389/tcp`
    `firewall-cmd --reload`
  - 移除防火牆特定port
    `firewall-cmd --zone=public --remove-port=3389/tcp`
    `firewall-cmd --runtime-to-permanent`
    `firewall-cmd --reload`
  - 更多限制(如IP)
    `firewall-cmd --permanent --zone=public --add-rich-rule='rule family="ipv4" source address="1.2.3.4/32" port protocol="tcp" port="4567" accept'`
  - 若需要限制使用者則需要依據群組限制那些使用者可以登入那些不能 **/etc/xrdp/sesman.ini**
    ```ini
    [Security]
    AllowRootLogin=false
    TerminalServerUsers=tsusers
    AlwaysGroupCheck=true
    ```

  - **X11桌面配置**
    - 桌面有哪些 session 放在 **/usr/share/xsessions**
    - 若要配置不同的預設GUI介面
      - `echo "cinnamon-session" > ~/.xsession && chmod a+x ~/.xsession`
      - `echo "cinnamon-session" > ~/.Xclients && chmod a+x ~/.Xclients`
    - 啟動xrdp的bash **/usr/libexec/xrdp/startwm.sh**
    - 新版linux(centos 8)要可以用 xorg 連線需要新增 `vi /etc/X11/Xwrapper.config`
      - 新增參數如下
        ```ini
        allowed_users =anybody
        ```
    - 避免RDP連線很多跳窗需要到 **/etc/polkit-1/localauthority/50-local.d**
      - 新增 **color.pkla** 內容如下
        ```ini
        [Allow Colord all Users]
        Identity=unix-user:*
        Action=org.freedesktop.color-manager.create-device;org.freedesktop.color-manager.create-profile;org.freedesktop.color-manager.delete-device;org.freedesktop.color-manager.delete-profile;org.freedesktop.color-manager.modify-device;org.freedesktop.color-manager.modify-profile;org.debian.pcsc-lite.access_pcsc;org.freedesktop.packagekit.system-sources-refresh
        ResultAny=no
        ResultInactive=no
        ResultActive=yes
        ```
      - 重啟該服務 `systemctl restart polkit`

- 安裝**NV顯示卡驅動**
  - 首先要具備一些依賴包 `yum install libglvnd* dkms gcc kernel-devel kernel-headers`
  - 開啟dkms `dkms autoinstall`
  - 新增檔案 /etc/modprobe.d/blacklist.conf 這檔案預設應該不存在
    - 內容為

        blacklist nouveau
        options nouveau modeset=0

  - 修改 /etc/default/grub 檔案，GRUB_CMDLINE_LINUX 設定裡面加上 rd.driver.blacklist=nouveau nouveau.modeset=0
  - `grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg && reboot`
  - `lsmod | grep nouveau` 最後要沒有出現任何模組才是對的！
  - `init 3` 退出X window 界面進入文字界面，使用root權限安裝驅動
    - 如果沒有辦法在現場下`init 3`指令，可以先重開機指定沒有X windows開機`systemctl set-default multi-user.target`
    - 安裝完成後復原 `systemctl set-default graphical.target`
  - `export CC=/usr/local/gcc-9.1.0/bin/gcc` 使用**自編編譯器**的時候需要加上這個

- 關閉某些輸入硬體功能(觸控螢幕)
  - 暫時的
    `xinput | grep 'ELAN Touchscreen' | grep -o --regexp=id=[0-9]* | cut -d= -f2 | xargs xinput disable`
  - 永久的
    `sudo vi /usr/share/X11/xorg.conf.d/40-libinput.conf`
    ```conf
        # 找到 Identifier "libinput touchscreen catchall" 新增
        Option "Ignore" "on"
    ```

- 安裝下載的RPM包\
    `yum localinstall *.rpm`

- 安裝epel
    - epel 提供許多實用套件，安裝僅需下指令\
    `yum install epel-release`
    - CENTOS 8 官方建議開啟 powertools
    `dnf install dnf-plugins-core`
    `dnf config-manager --set-enabled PowerTools`

- 安裝 [GCC](https://gcc.gnu.org/mirrors.html) (編譯原始碼的工具)
  - 選擇其中一個站點，進入releases/ 之下 選擇一個版本下載，檔案為.tar.gz檔案\
    直接用 `yum install gcc*` 比較快

- 安裝 **chromium** `yum install chromium`

- 通用解 **fcitx 輸入法殼層** 配套新酷音
    `sudo dnf install fcitx fcitx-configtool fcitx-chewing`
    - fcitx-configtool 可能會找不到，可以到 [fedora project](https://src.fedoraproject.org/projects/rpms/*) 下載 rpm 安裝(fc33版本)
    - fcitx是一個在X Window中使用的輸入法框架，直接新增新酷音輸入法就可以使用了
    - centos 使用 gnome 介面的話，可使用 `dnf install im-chooser`
    - `mv /etc/xdg/autostart/org.gnome.SettingsDaemon.Keyboard.desktop /etc/xdg/autostart/org.gnome.SettingsDaemon.Keyboard.desktop.backup` (wayland 3.x 版才需要)
    - 使用 `imsettings-switch fcitx`
    - `mv /etc/xdg/autostart/org.gnome.SettingsDaemon.Keyboard.desktop.backup /etc/xdg/autostart/org.gnome.SettingsDaemon.Keyboard.desktop` (wayland 3.x 版才需要)
    - 很酷的功能 ctrl+： 可以有剪貼簿暫存功能

- 安裝可以遠端rdp的client端 `sudo dnf install remmina`
  - centos 8 要額外執行指令 `dnf copr enable castor/remmina`
  - 關於 copr 額外套件說明:
    - First, make sure you have installed dnf core plugins:\
        `dnf install dnf-plugins-core`
    - Enable your repository:\
        `dnf copr enable your_name/test-project`
    - And install the package:\
        `dnf install package`
    - To uninstall your package:\
        `dnf remove package`
    - And to disable your repository:\
        `dnf copr disable your_name/test-project`
- 要解h264問題直接安裝vlc撥放器

- **home底下資料夾語言**
  - 執行 `LANG=en_US.UTF-8 xdg-user-dirs-update --force`

### 檔案與檔案系統管理 ###
- **rclone**
  - 先到官網下載[rclone](https://rclone.org/downloads/)\
  - 使用 `rclone config` 開始新建需要掛載的雲端
  - 修改 /etc/fuse.conf 增加 `user_allow_other`
  - 記得先建立掛載目錄資料夾，如`/home/jason/Google_Drive`
  - **服務安裝** 在/etc/systemd/system目錄下新增rclone.service檔案，內容如下
    ```ini
    [Unit]
    Description=RClone Service
    After=network-online.target
    Wants=network-online.target

    [Service]
    User=jason
    Group=jason
    Type=notify
    Restart=1

    ExecStart=/usr/local/bin/rclone \
    mount Google_Drive:/ \
    /home/jason/Google_Drive \
    --config /home/jason/.config/rclone/rclone.conf \
    --vfs-cache-mode full \
    --umask 002 \
    --dir-cache-time 96h \
    --poll-interval 15s \
    --timeout 1h \
    --allow-non-empty \
    --allow-other

    ExecStop=/usr/bin/fusermount -uz /home/jason/Google_Drive

    [Install]
    WantedBy=multi-user.target
    ```
  - 重新載入service檔案 `sudo systemctl daemon-reload `
  - 啟動服務 `sudo systemctl start rclone.service`

- 支援**exfat**隨身碟
  - `sudo dnf install exfat-utils fuse-exfat`
  -
- 支援**NTFS**隨身碟
  - `sudo dnf install ntfs-3g`

### SSL 憑證自動更新透過 haproxy ###
- 安裝haproxy, nginx 在需要提供 SSL 憑證的伺服器上
- `sudo yum install nginx haproxy certbot`
- 自動產生SSL憑證
  - 注意，centos 的selinux 原則會管制 http 只能走哪些port `semanage port -l | grep http_port_t`
  - 若要加上特殊port 需要用 `semanage port -a -t http_port_t  -p tcp 58588`
  - 記得先修改haproxy內的conf設定，並將DNS指向該伺服器，才可以順利驗證憑證
  - **產憑證指令** `certbot certonly --webroot -w /usr/share/nginx/html/ -v --email test@gmail.com --agree-tos -d test.domain.com`

### 使用者隔離(用分開的使用者執行不同的service) ###
- 創建系統服務使用者
`useradd -M -d /usr/local/apache -r -s /sbin/nologin apache`
`useradd -M -d /usr/local/php -r -s /sbin/nologin php-fpm`

### 編譯 下載的原始碼包(tarball) ###
- 安裝目錄若不在 /usr/local/ 之下，需要額外設定 **$PATH**，才可以讓程式抓到，設定方法如下
  1. 需要先把修改的path參數放置到 /etc/bashrc 的底部
      `echo "export PATH=/usr/local/git/bin:$PATH" >> /etc/bashrc`
  2. 將修改好的 bashrc 馬上套用
      `source /etc/bashrc`
  3. 比較正確的做法是在 etc/profile.d 下新增PATH的執行檔案
      `echo 'pathmunge /usr/local/git/bin' > /etc/profile.d/git.sh`
      `chmod +x /etc/profile.d/git.sh`

- **tarball 安裝方式**
  - 通常會先解壓縮之後cd到該解壓縮的目錄
  - 然後下`./configure --prefix=[安裝路徑] --[其他參數]`
  - `make clean` 清掉編譯的檔案
  - `make install` 使用編譯檔案安裝
    1. 安裝**apache**
       - 首先也必須先安裝好 openssl
       - 必須先把**APR**和**APR-Util**解壓縮放到apache原始檔的srclib資料夾裡面，並將資料夾分別命名為**apr**和**apr-util**
       - 若要支援 fastCGI 必須下載 Apache mod_fcgid FastCGI module 並且放到httpd之下(散的檔案直接合併至apache的source裡面) `./buildconf`
       - 若跳錯 /usr/bin/env: ‘python’: No such file or directory -> `dnf install -y python3 && alternatives --set python /usr/bin/python3`
       - **centos8** 可使用 `alternatives --set python /usr/bin/python3`，**注意：update-alternatives 是 alternatives 的 link**
       - `./configure --prefix=/usr/local/apache --with-included-apr --with-pcre --enable-module=so --enable-expires=shared --enable-rewrite=shared --enable-fcgid`
       - `make && make install`
       - 若有缺少的套件都用yum裝一裝 `yum install libxml2-devel pcre-devel`
       - 到prefix目錄切換ROOT權限啟動服務 `/usr/local/apache/bin/apachectl start`
       - **服務安裝** 在/etc/systemd/system目錄下新增httpd.service檔案，內容如下
         - (在**CentOS8** 內，服務沒有安裝在 /usr/local 之下，selinux 就會擋你，**selinux** 我還不會開例外，所以先遵循規定)
            ```ini
            [Unit]
            Description=The Apache HTTP Server
            After=network.target

            [Service]
            Type=forking
            PIDFile=/usr/local/apache/logs/httpd.pid
            ExecStart=/usr/local/apache/bin/apachectl -k start
            ExecReload=/usr/local/apache/bin/apachectl -k graceful
            ExecStop=/usr/local/apache/bin/apachectl -k graceful-stop
            PrivateTmp=true

            [Install]
            WantedBy=multi-user.target
            ```
            1. 這邊說明一下，**PIDFile**會記錄服務啟動之pid編號

        - 預設啟動服務 `systemctl enable httpd.service`
        - 重啟指令 `systemctl restart httpd.service`
        - **使用 FAST-CGI**
            ### UNIX SOCKET ###
            - **注意** LoadModule fcgid_module modules/mod_fcgid.so 這行要放在 LoadModule unixd_module modules/mod_unixd.so 後面
            - httpd.conf 設定
                ```conf
                LoadModule fcgid_module modules/mod_fcgid.so
                <IfModule fcgid_module>
                    FcgidMaxRequestsPerProcess 10000

                    <FilesMatch \.php$>
                        SetHandler "proxy:unix:/usr/local/php/var/run/php-fpm/php-fpm.sock|fcgi://localhost/"
                    </FilesMatch>
                </IfModule>
                ```
            - 開啟 LoadModule mod_proxy.so, mod_proxy_fcgi.so
            - 新增檔案 `/usr/local/apache/cgi-bin/php.fastcgi`
                ```sh
                #!/bin/sh
                # Set desired PHP_FCGI_* environment variables.
                # Example:
                # PHP FastCGI processes exit after 500 requests by default.
                PHP_FCGI_MAX_REQUESTS=10000
                export PHP_FCGI_MAX_REQUESTS

                # Replace with the path to your FastCGI-enabled PHP executable
                exec /usr/local/php/bin/php-cgi
                ```
            - virtualhost 設定
                ```conf
                <VirtualHost *:80>
                    ServerAdmin nameyearbirthday@gmail.com
                    DocumentRoot "/var/local/web"
                    ServerName myweb
                    <Directory "/var/local/web">
                        AddHandler fcgid-script .php
                        Options +ExecCGI
                        FcgidWrapper /usr/local/apache/cgi-bin/php.fastcgi .php
                        AllowOverride None
                        Require all granted
                    </Directory>
                    ErrorLog "logs/apps-error_log"
                    CustomLog "logs/apps-access_log" common
                </VirtualHost>
                ```
            ### TCP SOCKET ###
            ```conf
                <FilesMatch \.php$>
                    SetHandler "proxy:fcgi://php-fpm:9000"
                </FilesMatch>
            ```

    2. 安裝**PHP**
       - makefile 與 編譯問題:
         - 編譯libiconv `./configure --prefix=/usr/local/libiconv && make && make install`
         - configure: error: jpeglib.h not found. -> `yum install -y libjpeg-devel bzip2-devel`
         - configure: error: freetype-config not found. -> `yum install freetype-devel`
         - Unable to detect ICU prefix or no failed. Please verify ICU install prefix and make sure icu-config works. -> `yum install -y libicu-devel`
         - configure: error: Cannot find ldap.h -> `yum install -y openldap openldap-devel`
         - configure: error: xslt-config not found. Please reinstall the libxslt >= 1.1.0 distribution -> `yum install libxslt-devel`
         - error: utf8_mime2text() has new signature, but U8T_CANONICAL is missing. -> `yum install -y libc-client-devel`
         - configure: error: system libzip must be upgraded to version >= 0.11 -> 自己編譯最新版本
           - 最新的 libzip 需使用 cmake 安裝，所以先安裝 cmake：`./configure --system-libs --no-system-jsoncpp --prefix=/usr/local/cmake && make && make install`
           - 安裝 cmake 需要的套件 `yum install rhash-devel libcurl-devel expat-devel libarchive-devel libuv-devel -y`
           - **centos7 請用cmake3.14版本避免找自己麻煩**
           - **centos8** `dnf install libzip-devel`
           - 修改 /etc/bashrc (請參考下面修改PATH的方法)
           - `mkdir build && cd build && cmake -DCMAKE_INSTALL_PREFIX=/usr/local/libzip .. && make install`
           - 修改 /etc/ld.so.conf.d `echo "/usr/local/libzip/lib64" >> /etc/ld.so.conf && ldconfig`
           - 讓PHP可以**抓套件版本**資訊 `export PKG_CONFIG_PATH=/usr/local/libzip/lib64/pkgconfig:$PKG_CONFIG_PATH`
         - configure: error: LDAP build check failed. Please check config.log for more information. -> gcc-c++ 要安裝 yum 套件
         - 缺少一些套件(在最小安裝可能會遇到)`yum install -y openssl-devel libc-client-devel curl-devel gcc-c++`
         - (7.3) `./configure --prefix=/usr/local/php --with-apxs2=/usr/local/apache/bin/apxs --with-libdir=lib64 --with-config-file-path=/usr/local/php/etc --with-bz2 --with-curl --with-gettext --with-iconv-dir=/usr/local/libiconv --with-openssl --with-imap --with-imap-ssl --with-kerberos --with-gd --with-freetype-dir --with-jpeg-dir --with-ldap --with-libzip --with-mhash --with-mysqli --with-png-dir --with-pdo-mysql --with-pcre-regex --with-xmlrpc --with-xsl --with-zlib --enable-bcmath --enable-calendar --enable-exif --enable-ftp --enable-intl --enable-libxml --enable-mbstring --enable-pcntl --enable-soap --enable-sockets --enable-xml --enable-zip --enable-fpm`
         - 讓PHP編譯的時候可以抓到 /usr/local/libzip/include `make CPPFLAGS="-I/usr/local/libzip/include"`

       - **php.ini** 檔案放在原始碼的檔案包裡面
       - **使用 FAST-CGI** prefix:/usr/local/php/
         - 編譯時要多加上 `--enable-fpm`
         - 將 sbin/php-fpm 註冊為服務，在/etc/systemd/system目錄下新增php-fpm.service檔案
            ```ini
            [Unit]
            Description=The PHP FastCGI Process Manager
            After=syslog.target network.target

            [Service]
            Type=forking
            PIDFile=/usr/local/php/var/run/php-fpm.pid
            ExecStart=/usr/local/php/sbin/php-fpm --fpm-config /usr/local/php/etc/php-fpm.conf
            ExecReload=/bin/kill -HUP $MAINPID
            PrivateTmp=true

            [Install]
            WantedBy=multi-user.target
            ```
         - 在 etc/php-fpm.conf.default 和 etc/php-fpm.d/www.conf.default 需要將檔案去掉.default 編輯設定
           - 打開 pid 與 error_log 註解
           - 記得在`/usr/local/php/var/run/` 下創 php-fpm 資料夾
         - 設定`/usr/local/php/etc/php-fpm.d/www.conf`透過 unix-socket 接收FastCGI request
            ``` ini
            # 這邊是設定誰可以來存取這個 socket
            listen = var/run/php-fpm/php-fpm.sock
            listen.owner = apache
            listen.group = apache
            listen.mode = 0660
            ```
         - 預設啟動服務 `systemctl enable php-fpm.service`
         - 重啟指令 `systemctl restart php-fpm.service`

    3. 安裝**mariaDB**
       - 官方 repositories yum 套件
          - [至官網](https://downloads.mariadb.org/mariadb/repositories/)
          - Copy and paste it into a file under /etc/yum.repos.d/ (we suggest naming the file MariaDB.repo or something similar).
               ```ini
               # MariaDB 10.3 CentOS repository list - created 2019-05-15 18:59 UTC
               # http://downloads.mariadb.org/mariadb/repositories/
               [mariadb]
               name = MariaDB
               baseurl = http://yum.mariadb.org/10.3/centos7-amd64
               gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
               gpgcheck=1
               ```
          - After the file is in place, install MariaDB with:`sudo yum install MariaDB-server MariaDB-client`
          - 預設啟動服務 `systemctl enable mariadb`
          - 啟動服務 `systemctl start mariadb`
          - 初始化設定(必須用管理者權限執行) `mysql_secure_installation`
          - 記得開啟防火牆 3306(mysql)
          - 新增一個可以遠端的使用者 `mysql -u root -p` -> `create user 'jason'@'192.168.%' identified by 'password';`
          - `grant all on *.* to 'jason'@'192.168.%' identified by 'password';`
          - `FLUSH PRIVILEGES;`
          - 修改**my.cfg**
            - 設定檔路徑放在 /etc/my.cnf
            - default data directory for the MariaDB database server is /var/lib/mysql
            - [可參考](../環境建置/Apache+PHP+MariaDB/mysql&#32;設定心得.md)

       - 編譯指令(嘗試過但不穩定)
         - `mkdir build && cd build && cmake ../ -DCMAKE_INSTALL_PREFIX=/usr/local/mariaDB -DMYSQL_DATADIR=/usr/local/mariaDB/data -DPLUGIN_TOKUDB=NO`
         - `-DPLUGIN_TOKUDB=NO 是因為有BUG [參考](https://jira.mariadb.org/browse/MDEV-18273)`

    4. 安裝**git** `./configure --prefix=/usr/local/git`
       - 安裝gitea，自己的程式碼庫，只需要下載執行檔直接執行即可(必須先安裝好git)

    5. 安裝**GCC**庫 (不推薦，會影響預設GCC)
       - `./contrib/download_prerequisites` 下載相依套件
       - `mkdir gcc-build && cd gcc-build && ../configure --enable-checking=release --enable-languages=c,c++ --disable-multilib --prefix=/usr/local/gcc-9.1.0`
            ```
            –enable-checking=release 增加一些检查，也可以–disable-checking生成的编译器在编译过程中不做额外检查
            –enable-languages=c,c++ 你要让你的gcc支持的编程语言
            –disable-multilib 取消多目标库编译(取消32位库编译)
            ```
       - `echo "/usr/local/gcc-9.1.0/lib64" >> /etc/ld.so.conf && ldconfig`
       - 編譯問題:
         - ldconfig: /usr/local/gcc-9.1.0/lib64/libstdc++.so.6.0.26-gdb.py 不是一個 ELF 檔 - 其開頭的魔術位元組是錯的。-> 直接將這個檔案刪除，然後再重新ldconfig即可
            dkmsmake
            gcc-gfortran
            libquadmath-deve
            libtool
            systemtap
            systemtap-devel

### 檔案與磁碟管理 ###
- 列出目前硬碟掛載與結構
    `lsblk -f`

- 將硬碟先切出partition
  - `gdisk /dev/sdb` 選擇n新增分割(以下為常用)
    - **8300** Linux filesystem
    - **fd00** Linux RAID
    - **8e00** Linux LVM
  - `mkfs.xfs -f -b size=4096 /dev/sdb1`將分割區格式化成xfs系統

- 組raid陣列
  - 使用 `gdisk /dev/sda` 將硬碟 format 成 **Linux RAID**
  - `mdadm --create /dev/md1 --auto=yes --level=6 --raid-devices=4 /dev/sda1 /dev/sdb1`
  - 列出詳細資料 `mdadm --detail /dev/md1`
  - 從 RAID 陣列移除一個元件分割區。例如，若要移除 /dev/sda1，請輸入 `sudo mdadm /dev/md1 --fail /dev/sda1 --remove /dev/sda1`
  - 再次將分割區新增至 RAID 陣列。例如，若要新增 /dev/sda1，請輸入 `sudo mdadm -a /dev/md1 /dev/sda1`
  - 上述新增會是成為 spare ，使用此指令加入成員 `mdadm -v --grow --raid-devices=[總共幾顆硬碟] /dev/md1`
  - 若是擴大硬碟，或是全部更換更大硬碟，可以使用 `mdadm --grow /dev/md1 -z max`
    - Change the active size of devices in an array.
      This is useful if all devices have been replaced
      with larger devices.   Value is in Kilobytes, or
      the special word 'max' meaning 'as large as possible'.
  - mount 至需要的位置，修改 /etc/fstab 新增 `UUID=[array UUID] /raid6_array            xfs     defaults        0 0`
  - 原有磁碟陣列的硬碟，因重灌OS須恢復陣列 `mdadm --assemble --scan [或知道那些硬碟直接列出]`

- 依序組 PV VG LV
  - PV (實體 partion) `pvcreate /dev/md1`
    - 更新空間 `pvresize /dev/md1`
    - `pvs` 列出目前 pv 狀況
  - VG (建立在PV之上，虛擬的硬碟，可以整合多個PV成為一個VG) `vgcreate backup /dev/md1`
    - vgcreate ：就是主要建立 VG 的指令啦！他的參數比較多，等一下介紹。
    - vgscan ：搜尋系統上面是否有 VG 存在？
    - vgdisplay ：顯示目前系統上面的 VG 狀態
    - vgextend ：在 VG 內增加額外的 **PV**
    - vgreduce ：在 VG 內移除 **PV**
    - vgchange ：設定 VG 是否啟動 (active)
    - vgremove ：刪除 VG
  - LV (邏輯磁區，從VG中分割出的一塊空間) `lvcreate -l 100%FREE -n lvbackup backup`
    - 刪除邏輯磁區 `lvremove /dev/vg_raid6/lv_raid6`
    - 若需要加大邏輯磁區 `lvresize -l +100%FREE /dev/vg_raid6/lv_raid6`
    - 已經有格式化的磁區也一併擴大 `xfs_growfs /mountpoint`


- 將組好的LV 切成 xfs 分割區
  - `mkfs.xfs /dev/backup/lvbackup`
- mount 到 指定路徑
  - `mkdir /backup && mount /dev/backup/lvbackup /backup`
  - 需要修改 `/etc/fstab` 開機掛載硬碟
  - `/dev/mapper/backup-lvbackup   /backup           xfs     defaults        0 0`

- 使用 sshfs mount 遠端資料夾
  - `sshfs [user@]hostname:[directory] mountpoint`
    - `sshfs 192.168.55.200:/home/jason/developEnv/ /home/jason/remote/192.168.55.200/`
  - `fusermount -u mountpoint`
    - `fusermount -u /home/jason/remote/192.168.55.200/`
  - 開機時 mount (修改 /etc/fstab 檔案)
    - `jason@192.168.55.200:/home/jason/developEnv/ /home/jason/remote/192.168.55.200/ fuse.sshfs defaults,allow_other,_netdev 0 0`
    - `mount -a`

### 排程、資料同步 ###
- 例行性排程
  - 例如每天執行php語法
    - `crontab -e`
    - `0 0 * * * /usr/local/php/bin/php /var/local/web/getCA.php` 代表在每天的 00:00 執行php程式

### KVM ###
- linux 內建虛擬機器
  - 安裝 qmeu `yum install qemu qemu-kvm qemu-img virt-manager libvirt libvirt-client virt-install virt-viewer bridge-utils`
- WINDOWS 啟用 virtio
- CENTOS 8 最小安裝後，安裝 GNOME，後面需要安裝xorg 驅動才可以縮放螢幕(檢視->縮放顯示->將VM自動縮放...大小) `dnf install xorg*drv* spice-vdagent`

### WINE ###
- ※注意：這邊安裝的時候都不要直接使用root帳號，否則整個wine會被安裝到 root(/) 之下，這邊使用 sudo 取得權限，安裝仍然會安裝在/home/user之下
- **(centos)**
    1. 相關依賴套件
        `dnf install libvkd3d-devel vulkan-loader-devel.{i686,x86_64} ocl-icd-devel.{i686,x86_64} glibc-devel.i686 dbus-devel.i686 freetype-devel.i686 pulseaudio-libs-devel.i686 libX11-devel.i686 mesa-libGLU-devel.i686 libICE-devel.i686 libXext-devel.i686 libXcursor-devel.i686 libXi-devel.i686 libXxf86vm-devel.i686 libXrender-devel.i686 libXinerama-devel.i686 libXcomposite-devel.i686 libXrandr-devel.i686 mesa-libGL-devel.i686 mesa-libOSMesa-devel.i686 libxml2-devel.i686 zlib-devel.i686 gnutls-devel.i686 ncurses-devel.i686 sane-backends-devel.i686 libv4l-devel.i686 libgphoto2-devel.i686 libexif-devel.i686 lcms2-devel.i686 gettext-devel.i686 cups-devel.i686 fontconfig-devel.i686 gsm-devel.i686 libjpeg-turbo-devel.i686 libtiff-devel.i686 unixODBC.i686 openldap-devel.i686 alsa-lib-devel.i686 freeglut-devel.i686 giflib-devel.i686 gstreamer1-devel.i686 gstreamer1-plugins-base-devel.i686 libXmu-devel.i686 libXxf86dga-devel.i686 libieee1284-devel.i686 libpng-devel.i686 librsvg2-devel.i686 libstdc++-devel.i686 libusb-devel.i686 unixODBC-devel.i686 libpcap-devel.i686 libXfixes-devel.{x86_64,i686} gstreamer1-plugins-base-devel.{x86_64,i686} gstreamer1-devel.{x86_64,i686} systemd-devel.{x86_64,i686} glibc-devel.{i686,x86_64} libgcc.{i686,x86_64} libX11-devel.{i686,x86_64} freetype-devel.{i686,x86_64} gnutls-devel.{i686,x86_64} libxml2-devel.{i686,x86_64} libjpeg-turbo-devel.{i686,x86_64} libpng-devel.{i686,x86_64} libXrender-devel.{i686,x86_64} alsa-lib-devel.{i686,x86_64} glib2-devel.{i686,x86_64} libSM-devel.{i686,x86_64} libjpeg-turbo-devel libtiff-devel freetype-devel glibc-devel libstdc++-devel icoutils openal-soft-devel  gstreamer1-plugins-base-devel gstreamer1-devel ImageMagick-devel fontpackages-devel libv4l-devel gsm-devel giflib-devel libXxf86dga-devel mesa-libOSMesa-devel libgphoto2-devel fontforge libusb-devel lcms2-devel audiofile-devel -y`
    2. 解壓縮 wine 包並 cd 至該目錄之下執行安裝
        `mkdir -p wine32 wine64 && cd wine64 && ../configure --enable-win64 && make -j8 && cd ../wine32 && PKG_CONFIG_PATH=/usr/lib/pkgconfig ../configure --with-wine64=../wine64 && make -j8 && make install && cd ../wine64 && make install`

    3. 移除
        `cd ./wine32 && make uninstall && cd ../wine64 && make uninstall`

- **(ubuntu)**
    1. 新增 repository key\
        `wget -nc https://dl.winehq.org/wine-builds/winehq.key`\
    2. 可以在 home 之下看到winehq.key這個檔案\
        `sudo apt-key add winehq.key`
    3. 新增 repository (移除則要加上 --remove)\
        `sudo apt-add-repository (--remove) 'deb https://dl.winehq.org/wine-builds/ubuntu/ bionic main'`
    4. 使用 apt 套件管理工具安裝 WINE\
        `sudo apt update`
    5. Stable branch:\
        `sudo apt install --install-recommends winehq-stable`

- **(fedora)**
    1. 新增 repository (移除則要加上 --remove)\
        `dnf config-manager --add-repo https://dl.winehq.org/wine-builds/fedora/30/winehq.repo`
    2. 使用 dnf/yum 套件管理工具安裝 WINE\
        `dnf install winehq-stable`

  - 接下來依照步驟指示安裝即可，移除則反過來也使用套件管理工具進行
  - fedora 改版更新
    1. `sudo dnf upgrade --refresh`
    2. `sudo dnf install dnf-plugin-system-upgrade`
    3. `sudo dnf system-upgrade download --releasever=33`
    4. `sudo dnf system-upgrade reboot`

- **字型問題** 因為在wine 環境下沒有對應中文字型\
  所以需要利用regedit(wine的windows註冊表)
  1. 先新增一個文件如下 (例如存為chinese.reg 字型需要打檔案名稱)
  ```reg
  REGEDIT4

  [HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\FontLink\SystemLink]
  "Lucida Sans Unicode"="NotoSansCJKtc-Regular.otf"
  "Microsoft Sans Serif"="NotoSansCJKtc-Regular.otf"
  "MS Sans Serif"="NotoSansCJKtc-Regular.otf"
  "Tahoma"="NotoSansCJKtc-Regular.otf"
  "Tahoma Bold"="NotoSansCJKtc-Regular.otf"
  "SimSun"="NotoSansCJKtc-Regular.otf"
  "Arial"="NotoSansCJKtc-Regular.otf"
  "Arial Black"="NotoSansCJKtc-Regular.otf"
  ```
  1. 下載中文字型放到 /home/$USER/.wine/drive_c/windows/Fonts
  2. 使用regedit chinese.reg 就可以成功看到正常文字了

- **Between 應用程式**
  1. winecfg 調整成 windows7 模式
  2. 安裝 winetricks
    `cd ~/`
    `wget  https://raw.githubusercontent.com/Winetricks/winetricks/master/src/winetricks`
    `chmod +x winetricks`
    `sudo cp winetricks /usr/local/bin/winetricks`
  3. 依據 wine app database 指示執行 (meiryo 包含在 pptfonts 裡面)
  	`sudo yum install cabextract`
      **centos8 沒有 cabextract，需自行編譯**
    `winetricks corefonts meiryo`
  4. 正常安裝即可運作

- 移除已安裝的windows程式
    `wine uninstaller`

### 除錯 ###
- YUM 安裝到一半重開機 (導致無法順利更新)
  - `yum complete-transaction`
  - `package-cleanup --problems` 列出有問題的套件
  - `package-cleanup --dupes` 列出重複套件 (通常有問題)
  - `package-cleanup --cleandupes` 清除重複套件

# UBUNTU (20.04 LTS) #

### 安裝注意 ###
- /boot 不可以使用 XFS 模式，因為grub2 無法辨識 XFS 硬碟格式
- 移除 pub key `sudo apt-key del <id>`
  - id 用 apt-key list 列出，取後兩段 為id

### 基本設定 ###
- add repo
``

- remove repo
    remove the ppa file from **/etc/apt/sources.list.d**
    Or, edit the **/etc/apt/sources.list**

- 升級套件(僅升級目前套件，不解決相關依賴，production 適用，避免因為更新造成現有服務不可用)
`sudo apt-get update && sudo apt-get upgrade`
- 智能升級套件(通過新版本自動處理不斷變化的依賴關係，會依據依賴性刪除或新增 package，被視為是有風險的升級)
`sudo apt-get dist-upgrade`

- H.264 libary 安裝\
`$ sudo apt-get install gstreamer1.0-libav`

- 更改預設編輯器
`sudo update-alternatives --config editor`

- 直接指定編輯器
`sudo update-alternatives --set editor [程式路徑]`

- python 預設使用 python3
`sudo update-alternatives --set python /usr/bin/python3`
`sudo update-alternatives --install /usr/bin/pip pip /usr/bin/pip3 1`

### 輸入法 ###
  - 通用解 fcitx 配套新酷音
    `sudo apt install fcitx fcitx-chewing`
    - fcitx是一個在X Window中使用的輸入法框架，直接新增新酷音輸入法就可以使用了

### git ###
- For the latest stable version for your release of Debian/Ubuntu
`apt-get install git`

- For Ubuntu, this PPA provides the latest stable upstream Git version
`add-apt-repository ppa:git-core/ppa # apt update; apt install git`

### raspberry pi 專屬 ###
- 更新韌體後，需要先執行
    `cd /boot/firmware/`
    `zcat vmlinuz > vmlinux`
  - 自動運行(每次更新後都做這件事情)
  - Add a new script to the *boot partition* called **auto_decompress_kernel** with the following (`sudo chmod +x auto_decompress_kernel`):
    ```bash
    #!/bin/bash -e

    #Set Variables
    BTPATH=/boot/firmware
    CKPATH=$BTPATH/vmlinuz
    DKPATH=$BTPATH/vmlinux

    #Check if compression needs to be done.
    if [ -e $BTPATH/check.md5 ]; then
        if md5sum --status --ignore-missing -c $BTPATH/check.md5; then
        echo -e "\e[32mFiles have not changed, Decompression not needed\e[0m"
        exit 0
        else echo -e "\e[31mHash failed, kernel will be compressed\e[0m"
        fi
    fi

    #Backup the old decompressed kernel
    mv $DKPATH $DKPATH.bak

    if [ ! $? == 0 ]; then
        echo -e "\e[31mDECOMPRESSED KERNEL BACKUP FAILED!\e[0m"
        exit 1
    else 	echo -e "\e[32mDecompressed kernel backup was successful\e[0m"
    fi

    #Decompress the new kernel
    echo "Decompressing kernel: "$CKPATH".............."

    zcat $CKPATH > $DKPATH

    if [ ! $? == 0 ]; then
        echo -e "\e[31mKERNEL FAILED TO DECOMPRESS!\e[0m"
        exit 1
    else
        echo -e "\e[32mKernel Decompressed Succesfully\e[0m"
    fi

    #Hash the new kernel for checking
    md5sum $CKPATH $DKPATH > $BTPATH/check.md5

    if [ ! $? == 0 ]; then
        echo -e "\e[31mMD5 GENERATION FAILED!\e[0m"
        else echo -e "\e[32mMD5 generated Succesfully\e[0m"
    fi

    #Exit
    exit 0
    ```
  - Create a script in the */ect/apt/apt.conf.d/* directory and call it **999_decompress_rpi_kernel**. The script should contain the following (`sudo chmod +x 999_decompress_rpi_kernel`):
    ```bash
    DPkg::Post-Invoke {"/bin/bash /boot/firmware/auto_decompress_kernel"; };
    ```

- 刪除回報問題
    `sudo rm /var/crash/*`
