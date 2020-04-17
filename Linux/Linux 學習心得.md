### 基本指令 ###
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

- **vi(vim)** 程式編輯器
  - 指令模式
  - 編輯模式
  - 命令模式(打關鍵前導符號 `: ? /`)

### 權限 ###
- 設定排程


### 排程 ###
- 設定排程
    `chmod`

### shell ###