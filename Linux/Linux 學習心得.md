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

- 刪除檔案或資料夾
    **r:遞迴**
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

### 權限 ###
- 設定權限
    `chmod [權限模式] [檔案]`


### 排程 ###
- 設定排程 (for user)
    `crontab -e`

### shell ###
- 製作可開機 usb
  - 先列出可用來做usb之裝置
    `lsblk`
  - if 是來源檔案位置，of 是目標位置(通常是整個)usb磁碟
    `sudo dd bs=4M if=/home/jason/Downloads/CentOS-8.2.2004-x86_64-dvd1.iso of=/dev/sde conv=fdatasync status=progress`
