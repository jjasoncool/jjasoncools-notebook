### 基本指令 ###
- 系統關機/重新啟動
    >shutdown [OPTION] TIME [MESSAGE]
  - 系統關機
    `shutdown -P 0`
  - 系統重新啟動
    `shutdown -r 0`

- 複製檔案
    `cp -fr /home/skjan/folder/* /var/www/html/`
    `yes | cp -fr /home/skjan/folder/* /var/www/html/` (不會跳出是否覆蓋)

- 修改檔名
  - 單一檔案修改
    `mv [options] filename1.ext filename2.ext`
  - 多重檔案修改
    `rename [options] expression replacement file`

- 同步資料夾
    `rsync -avu --delete --exclude ".git/" --exclude "cfg.php" /home/user/A/ /home/user/B/`
    | flags             | Description                                                                                |
    | ----------------- | ------------------------------------------------------------------------------------------ |
    | -a                | Do the sync preserving all filesystem attributes                                           |
    | -v                | run verbosely                                                                              |
    | -u                | only copy files with a newer modification time (or size difference if the times are equal) |
    | --delete          | delete the files in target folder that do not exist in the source                          |
    | --exclude=PATTERN | exclude files matching PATTERN                                                             |
    | -n                | perform a trial run with no changes made                                                   |

- 建立連結(link)
  - Hard Link:連結至同一個inode區塊，故實際上的檔案與內容完全相同，且若多個硬連結其中一個消失，並不影響檔案完整性
    - 但不要使用在目錄上，因當前目錄./與父目錄../會是不同物件
    `ln <source file> <link file>`
  - Symbolic Link:即傳統捷徑，為創一單一檔案，並指向原始路徑，若原始路徑變更，則會找不到該原始路徑出錯
    - 須使用 `-s` 創立
    `ln -s <source file> <link file>`

- 讀檔內容show在command line裡面
    `cat filename`

- 修改檔案內容
  - **sed**
  - `sed -i 's/open-vsx.org\/vscode\/gallery/marketplace.visualstudio.com\/_apis\/public\/gallery/g; s/open-vsx.org\/vscode\/item/marketplace.visualstudio.com\/items/g;' /usr/share/codium/resources/app/product.json`
  - 分隔符號
    - 一般常見的是使用 **/**，但若方便區隔可利用較少使用之分隔符，如：**|**
  - 需跳脫的字元：
    - 除了分隔符需要跳脫之外，單引號', 括弧[]都需要注意，避免錯誤
  - 跳脫字元單引號
    - A single quote may not occur between single quotes, even when preceded by a backslash.
    - One trick is to use shell string concatenation of adjacent strings and escape the embedded quote using shell escaping:
    - `sed 's/ones/two'\''s/' <<< 'ones thing'`

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

- 輸出內容管理
  - 不輸出任何內容
    `指令 > /dev/null 2>&1`
    輸出到 /dev/null，並且將錯誤也導向正常輸出 (1:the standard output  2: the standard error)

- **dd** 磁碟備份
    | options | Description                    |
    | ------- | ------------------------------ |
    | if=     | 輸入來源                       |
    | of=     | 輸出來源                       |
    | bs=     | 複製區塊大小 default:512 bytes |

  - 先列出可用來做usb之裝置
    `lsblk`
  - 磁碟抹除
    - `sudo dd bs=4M if=/dev/zero of=/dev/sda`
  - 製作可開機 usb
    - if 是來源檔案位置，of 是目標位置(通常是整個)usb磁碟
      `sudo dd bs=4M if=/home/jason/Downloads/CentOS-8.2.2004-x86_64-dvd1.iso of=/dev/sde conv=fdatasync status=progress`

### 權限 ###
- 設定權限
    `chmod [權限模式] [檔案]`

- 防火牆
  - iptables
  - firewall-cmd *(centos)*
    - 檢視目前網路狀態
        `sudo firewall-cmd --zone=public --list-all`
    - 新增某網段ip可通
        `sudo firewall-cmd --permanent --zone=public --add-rich-rule='rule family=ipv4 source address=172.27.0.0/16 accept'`


  - ufw *(ubuntu)*

### 套件管理 ###
- dnf / yum *(centos)*
- apt / apt-get *(ubuntu)*

### 網路 ###
- 檢查是否有通
  - ping
  - telnet
    `telnet <ip or hostname> <port>`
- 查看網卡
    `nmcli connection show`

### 排程 ###
- 設定排程 (for user)
    `crontab -e`

### shell ###
