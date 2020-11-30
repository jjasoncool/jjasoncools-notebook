### 基本指令 ###
- 系統關機/重新啟動
    >shutdown [OPTION] TIME [MESSAGE]
  - 系統關機
    `shutdown -h 0`
  - 系統重新啟動
    `shutdown -r 0`

- 複製檔案
    `cp -fr /home/skjan/180418crweb/* /var/www/html`
- 修改檔名
  - 單一檔案修改
    `mv [options] filename1.ext filename2.ext`
  - 多重檔案修改
    `rename [options] expression replacement file`

- 讀檔內容show在command line裡面
    `cat filename`

- 修改檔案內容
  - **sed**
  - `sed -i 's/open-vsx.org\/vscode\/gallery/marketplace.visualstudio.com\/_apis\/public\/gallery/g; s/open-vsx.org\/vscode\/item/marketplace.visualstudio.com\/items/g;' /usr/share/codium/resources/app/product.json`

- 刪除檔案或資料夾
    | rm  | 說明 |
    | --- | ---- |
    | -r  | 遞迴 |
  - 自動回答yes
    `yes | rm -r folder`
  - 強制刪除(不會跳出警告)
    `rm -rf folder`

- 帳號管理
  - 密碼加密機制
    `authconfig --test | grep hashing`
  - 登出使用者 (請勿用來登出 root)
    `pkill`

- **vi(vim)** 程式編輯器
  - 指令模式
  - 編輯模式
  - 命令模式(打關鍵前導符號 `: ? /`)

- 別名
  - 列出目前所有定義別名
    `alias -p`

  - 要執行的指令又臭又長的時候可以自訂別名(但只有執行期間有效)
    `alias alias_name="command_to_run"`

  - 需要永久生效的話可以新增至該 user 的 .bashrc 裡面
    `cd ~ && vi .bashrc`

### 權限 ###
- 設定權限
    `chmod [權限模式] [檔案]`

- 防火牆
  - iptables
  - firewall-cmd *(centos)*
  - ufw *(ubuntu)*

### 套件管理 ###
- dnf / yum *(centos)*
- apt / apt-get *(ubuntu)*

### 網路 ###
- 檢查是否有通
  - ping
  - telnet
- 查看網卡
    `nmcli connection show`

### 排程 ###
- 設定排程 (for user)
    `crontab -e`

### shell ###
- 製作可開機 usb
  - 先列出可用來做usb之裝置
    `lsblk`
  - if 是來源檔案位置，of 是目標位置(通常是整個)usb磁碟
    `sudo dd bs=4M if=/home/jason/Downloads/CentOS-8.2.2004-x86_64-dvd1.iso of=/dev/sde conv=fdatasync status=progress`
