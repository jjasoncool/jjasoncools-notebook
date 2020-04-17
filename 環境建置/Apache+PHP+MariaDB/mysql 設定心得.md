----------------------------------------
            MYSQL基礎設定
----------------------------------------
## 安裝服務(windows) ##
語法為：`mysqld install (服務名稱) --defaults-file="(抓取設定檔 ini 位址)"`
範例：`mysqld install MySQL --defaults-file="D:\xampp\mysql-5.7.24-winx64\my.ini"`
此行代表 執行mysqld (必須先建立path 才可以直接呼叫執行)
`SET PATH=%PATH%;D:\xampp\mysql-5.7.24-winx64\bin`

如果要刪除服務：
    shell> mysqld -remove

#若沒有指定my.ini時，可利用以下方法：
修改註冊表：HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\MySQL\ImagePath

修改為：
"[MySQL路徑]\bin\mysqld"--defaults-file="[MySQL路徑]\my.ini"MySQL
這樣配置就可以使用net start mysql命令正常啟動MySQL。

## 安裝服務(linux) ##

## my.ini / my.cfg 設定資訊 ##
```ini
[mysqld]
...
# 設置mysql的安裝目錄
basedir="D:\mysql-5.0.96-win32"
# 設置mysql資料庫的數據的存放目錄
datadir="D:\mysql-5.0.96-win32\data"
# 設定db name 區分大小寫
lower_case_table_names = 0
# 0: 區分大小寫(windows不可用) 1:只有小寫 2:區分大小寫，但以小寫形式判斷
# the default value of lower_case_table_names is 0.
# On Windows, the default value is 1.
# On OS X, the default value is 2.

# 服務端使用的字符集預設為utf8mb4編碼 (utf8mb4可以包含字元比utf8多，即使是非法字元也可以照樣塞入)
# Example: Changing the default character set to UTF-8
# To change the default character set from latin1 to UTF-8, the following settings should be specified in the my.cnf(windows 為 my.ini) configuration file.
[client]
...
default-character-set=utf8mb4
...
[mysql]
...
default-character-set=utf8mb4
...
[mysqld]
...
character-set-server = utf8mb4
collation-server = utf8mb4_general_ci
init-connect='SET NAMES utf8'

# 可以查詢sql的類型
SHOW TABLE STATUS;

# SQL 其他設定
# 單個 row 過大 (欄位太多 innoDB無法儲存)
innodb_strict_mode = 0
# 獨立表空間的優點是減少對表空間IO資源的爭用，容易管理及備份，回收空間容易
innodb_file_per_table = 1
# 關閉 strict SQL Mode (通常是字串沒有轉碼導致不合法字元被當作資料，但我們資料庫必須轉)
sql_mode=IGNORE_SPACE,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
# 嚴格模式也會導致日期塞入資料的時候發生錯誤，因此若是空的日期必須轉成null才可以塞到資料庫裏面
```

----------------------------------------
            資料庫類型
----------------------------------------
- InnoDB 可以使用begin 與 commit， MyISAM則無法
- MyISAM 是 Table-level lock，當有寫入時其他人無法讀取 (有少數例外，像是 bulk insert)。而 InnoDB 設計成 Row-level lock，在寫入時有很大機會還是可以讀取。

----------------------------------------
            編碼查詢選項
----------------------------------------
MYSQL 編碼選項
    mysql> SHOW CHARACTER SET
>   MySQL除了支援各種不同的字元集，讓資料庫可以儲存不同語言的文字外，每一種字元集都可以依照實際需要，搭配不同的Collation設定。你可以使用下列的指令查詢MySQL支援的Collation資訊
    mysql> SHOW COLLATION

>   可以查詢目前mysql編碼選項
    mysql> \s


如果要設定編碼選項:
    mysql> set names 'utf8'(看是甚麼編碼);
    即可在php內轉換，但服務重啟後失效
    mysql>SHOW VARIABLES LIKE 'character_set%';
    mysql>SHOW VARIABLES LIKE 'collation%';
    可以查詢目前資料庫設定的字元編碼

修改database編碼:
    mysql> ALTER DATABASE [databasename] CHARACTER SET utf8 COLLATE utf8_general_ci;
    mysql> ALTER DATABASE [databasename] CHARACTER SET big5 COLLATE big5_chinese_ci;

----------------------------------------
            使用者相關
----------------------------------------
- 設定/修改密碼
    shell> `mysqladmin -u root -p password "newpass"`
- 進入mySQL設定密碼
    mysql> `SET PASSWORD FOR '目標使用者'@'主機' = PASSWORD('密碼');`
    mysql> `create user '帳'@'localhost' identified by '密';`
- 給予所有權限
    mysql> `grant all on *.* to '帳'@'localhost' identified by '密';`
    >   *.* 為所有資料庫，也可以限制資料庫
    mysql> `grant all on database.* to '帳'@'localhost' identified by '密';`
- 取消權限
    mysql> `revoke all privileges on *.* from 'user'@'host';`
- 刪除使用者
    mysql> `DROP USER 'dbuser'@'localhost';`

- 只要修改使用者無論密碼或權限相關，必須執行
    mysql> `FLUSH PRIVILEGES;`

- 在mysql.user 內即可看到帳號的設定值
>   % means remote hosts can login to MySQL server from any other server whereas localhost means you can login to MySQL server only from same machine.

----------------------------------------
            備份與還原
----------------------------------------
備份 MySQL 資料庫主要分為兩個方法，一是將資料庫目錄完整備份：二是使用 MySQL 內建的 mysqldump 程式

雖然第一種方法十分簡單，但有一個問題存在，那就是在備份及復原時均需停止 MySQL 的運作，這樣對於實際應用十分不便。
再者，這樣備份出來的檔案，如果在相同版本的 MySQL Server 應該沒有問題，但移到版本不同的 Server 則不一定可以成功復原。

因為有以上的問題，MySQL 已經內建了備份工具，它就是 mysqldump。
mysqldump 的備份方法是將資料庫內的每個資料表結構及每筆資料產生 SQL 語句，然後存到文字檔。而且它可以自訂每一個資料表一個檔案，以及將資料表結構及資料分開儲存，以下是使用例子：
    shell> mysqldump -u root -p --databases db_name > ../backup/mysql_backup.sql
以上指令會使用 mysqldump 將 db_name 備份到 /backup/mysql_backup.sql，在輸入指令後，需要輸入 MySQL 的 root 密碼。

cmd 密碼-p選項不需要空格直接打密碼 ex:mysql -u user -p123456

如果你的 MySQL Server 不止一個資料庫，希望可以一次過將所有資料庫備份起來，可以寫一個簡單的 shell script 完成，又或者使用以下指令：
``` shell
    shell> mysqldump -u root -p --all-databases > ../backup/mysql_alldb.sql
```
這個 --all-databases 代表所有資料庫，這樣 mysqldump 便會將所有資料庫備份到 /backup/mysql_alldb.sql
若是出現錯誤: Got error: 1016: Can't open file: '.\ss\ss4664.frm' (errno: 24) when using LOCK TABLES
可以多加 --lock-tables=false

(建議可先刪除欲復原database，再行復原動作)
```shell
    shell> mysql -u root -p
    mysql> drop database db_name;
    mysql> create database db_name character set utf8;
```

至於復原資料同樣簡單，只要一句指令便完成：
    shell> mysql -u root -p --default-character-set=latin1 db_name < ../backup/mysql_backup.sql
以上指令會將 /backup/mysql_backup.sql 備份檔復原到 db_name 裡面。
若跳出error需要強制匯入時，可多下 --force 參數

**注意**若更新時出現 MySQL server has gone away 錯誤
則需修改 my.ini 內容
```ini
[mysqld]之下 max_allowed_packet = 20M
[mysqldump]之下 max_allowed_packet = 64M
```
----------------------------------------
                基本指令
----------------------------------------
- mysql_upgrade
    主要用來作為 MariaDB(Mysql) 升級使用(將舊有的資料轉換為與新資料庫相容)
    (5.0升級至5.5簡易資料庫沒問題，若有跳出error時，檢查mysqldump檔案，若無大礙即可下--force強制匯入)
    mysql_upgrade -u root -p

- mysql
    大部分的功能都用會到
    -e 執行後面連接的字串後退出
