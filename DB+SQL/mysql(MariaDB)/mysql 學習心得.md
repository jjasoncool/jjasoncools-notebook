----------------------------------------
## basic ##
----------------------------------------
- UNION
  - UNION 會將重複資料剔除，UNION ALL 則全部顯示重複資料

```sql
SELECT 'foo' AS bar UNION SELECT 'foo' AS bar;
SELECT 'foo' AS bar UNION ALL SELECT 'foo' AS bar;
```

----------------------------------------
## function ##
----------------------------------------
```sql
-- 字串
    -- 連接字串
        CONCAT("string1","string2","string3")
    -- 使用某個"膠水"字串，連接其他字串
        CONCAT_WS(separator, str1, str2,...)
    -- 查詢字串長度
        LENGTH("column")
    -- 剪切字串
        SUBSTR 或 SUBSTRING 相等
        SUBSTR(string, start, length)
        -- start 從 1 開始
    -- 搜尋字串位置 取代 like%
        LOCATE("被搜尋字串","完整字串或被搜尋欄位")
        -- 若此值大於0，代表有被搜尋到

    -- 搜尋字串回傳boolean，可利用此在 where 條件 同上取代like
    POSITION( "被搜尋字串" IN "完整字串或被搜尋欄位" )

    -- 補滿字串
        LPAD(str, len, padstr)
        -- 將字串補滿到指定長度，padstr為使用何種字串補滿

    -- 刪除空白
        TRIM(str)
        -- 將字串頭尾空白去掉，可拿來對付BIG5 許功蓋結尾會補一個 slash\避免跳脫query字串
        TRIM('\ ') -- 就可以成功塞入反斜線

-- 時間日期
    -- 變換時區
        CONVERT_TZ("欲變換時間", "目前時區", "欲轉換時區")
        -- select convert_tz("2018-07-28 13:26","+00:00","+02:00") from dual

    -- where 條件下datetime = 0000-00-00 00:00:00
        SELECT * FROM myTable WHERE datetimeCol = CONVERT(0,DATETIME)

    -- 字串轉換
        -- 字串變日期
        STR_TO_DATE(要轉換的日期字串, '%Y/%m/%d')
        -- 日期變字串
        DATE_FORMAT(要轉換的日期, '%d/%c/%y')
        -- 對應使用方式如下表
            -- TIME
            %k	Hour (0..23)
            %l	Hour (1..12)
            %H	Hour (00..23)
            %h	Hour (01..12)
            %I	Hour (01..12)
            %i	Minutes, numeric (00..59)
            %S	Seconds (00..59)
            %s	Seconds (00..59)

            -- DATE
            %Y	Year, numeric, four digits
            %y	Year, numeric (two digits)
            %M	Month name (January..December)
            %c	Month, numeric (0..12)
            %m	Month, numeric (00..12)
            %e	Day of the month, numeric (0..31)
            %d	Day of the month, numeric (00..31)
            %D	Day of the month with English suffix (0th, 1st, 2nd, 3rd, …)
            %w	Day of the week (0=Sunday..6=Saturday)
            %a	Abbreviated weekday name (Sun..Sat)
            %W	Weekday name (Sunday..Saturday)

    -- 時間區間比較
        DATE_ADD(時間, INTERVAL 增減值 時間單位)
        DATE_SUB(時間, INTERVAL 增減值 時間單位)
        -- 對應單位
            MICROSECOND
            SECOND
            MINUTE
            HOUR
            DAY
            WEEK
            MONTH
            QUARTER
            YEAR
            SECOND_MICROSECOND
            MINUTE_MICROSECOND
            MINUTE_SECOND
            HOUR_MICROSECOND
            HOUR_SECOND
            HOUR_MINUTE
            DAY_MICROSECOND
            DAY_SECOND
            DAY_MINUTE
            DAY_HOUR
            YEAR_MONTH
        -- 範例
            SELECT
            DATE_ADD(DATE(NOW()), INTERVAL -10 DAY),
            DATE(NOW()),
            DATE_SUB(DATE(NOW()), INTERVAL -10 DAY)
            FROM dual;

-- 加密
    SELECT SHA2('abc', 224);

-- function 進階使用
    -- 日期比較(未滿一年不算)
        YEAR(NOW())-YEAR(STR_TO_DATE(SUBSTRING(a.COMPLETED,-5,5), '%c/%y'))-(MONTH(NOW())<MONTH(STR_TO_DATE(SUBSTRING(a.COMPLETED,-5,5), '%c/%y'))) AS year

    -- 時間比較
        -- 幾歲
        SELECT TIMESTAMPDIFF(YEAR, STR_TO_DATE('1992/05/11', '%Y/%m/%d'), DATE(NOW())) FROM DUAL;
        SELECT TIMESTAMPDIFF(MINUTE, '2038-01-19 03:14:07', '2038-01-19 03:15:07') FROM DUAL;
        SELECT (TIMEDIFF('2038-01-19 03:15:07','2038-01-19 03:14:07')/60) FROM DUAL;

    -- 數字由小數點轉為整數
        SELECT FORMAT(FLOOR(columnName),0) FROM myTable WHERE

    -- 欄位編碼
        -- 更改資料庫表格編碼
        ALTER TABLE sv2.`risk` COLLATE='utf8_general_ci', CONVERT TO CHARSET utf8;

        -- 若是原先資料庫編碼為 latin1，撈取資料庫顯示會變成亂碼，不修改資料庫直接撈取中文的辦法為
            CONVERT(CAST( [欲撈取欄位名稱] as binary) USING big5)，就可以變成中文了
            ※ 注意:mysql 必須安裝包含big5的編碼包，否則無法使用
            CAST 為資料型別轉換使用
            CONVERT 為字串編碼轉換使用
```

----------------------------------------
## 設定語法的使用 ##
----------------------------------------
```sql
-- 新增 table
    CREATE TABLE `table` (
        `id` BIGINT(20) UNSIGNED NOT NULL AUTO_INCREMENT,
        `user` VARCHAR(30) NULL DEFAULT NULL,
        `type` TINYINT(1) UNSIGNED NOT NULL DEFAULT '0',
        `subtype` TINYINT(4) NOT NULL DEFAULT '0',
        `name` VARCHAR(100) NULL DEFAULT NULL,
        PRIMARY KEY (`id`),
        UNIQUE INDEX `user` (`user`)
    )
    COLLATE='utf8mb4_general_ci'
    ENGINE=InnoDB;
    -- 複製別的 table 資料格式
    CREATE TABLE trace.keelung_work_log AS SELECT *,'' AS `kind`, '' AS `user`, NOW() AS `updatetime` FROM trace.keelung_work;
    ALTER TABLE trace.keelung_work_log
        MODIFY COLUMN `kind` VARCHAR(20) COLLATE 'utf8mb4_general_ci',
        MODIFY COLUMN `user` VARCHAR(20) COLLATE 'utf8mb4_general_ci';

-- 修改 table
    -- 新增欄位
        ALTER TABLE compcert.CERT
            ADD COLUMN BT_POSITION tinyint DEFAULT 0 AFTER AS_ON,
            ADD COLUMN SITE_SURVEYOR VARCHAR(30) AFTER SURVEYED;
        ALTER TABLE dcs.tablename
            ADD COLUMN `server_id` INT NOT NULL AFTER `update_time`, ADD COLUMN `server_update` TIMESTAMP NOT NULL default 0 AFTER `server_id`;
    -- 修改欄位型別
        ALTER TABLE part2 MODIFY COLUMN IBC_17 tinyint;
    -- 修改欄位位置
        ALTER TABLE trace.tpsi MODIFY column seatrydate date AFTER launchbill;
    -- 修改欄位名稱
        ALTER TABLE creditCard CHANGE `bankID` `bankcode` int(3) ZEROFILL NOT NULL;
    -- 刪除欄位
        ALTER TABLE compcert.CERT DROP COLUMN PS_POSITION;

    -- 新增欄位註解 (這是一個好習慣)
        -- add/modify 都通用，注意的是COMMENT必須在AFTER
        ALTER TABLE pa.ACCOUNT
            MODIFY COLUMN SCF_permission VARCHAR(1) DEFAULT 'R' NOT NULL COMMENT 'O=original file, L=limited and file protected, R=read only' after column;

    -- 欄位預設值自動填0，填到達成該欄位長度為止
        ZEROFILL

    -- 欄位在update 的時候自動更新timestamp
        ON UPDATE CURRENT_TIMESTAMP

    -- 同時新增/修改多個欄位
        ALTER TABLE compcert.CERT
            MODIFY COLUMN AS_AT VARCHAR(30),
            MODIFY COLUMN AS_AT_BT VARCHAR(30),
            MODIFY COLUMN AS_AT_PS VARCHAR(30),
            MODIFY COLUMN AS_AT_BS VARCHAR(30);

    -- 重新命名
        RENAME TABLE `group` TO `member`;

    -- 修改欄位字元排序
        ALTER TABLE trace.`tpsquote_file` COLLATE='utf8mb4_general_ci';

-- key,constrain
    -- 自動產生ID相關
        SELECT LAST_INSERT_ID();
        -- 可以直接知道下一個可以塞入的ID

    -- PRIMARY KEY (PK)
    ALTER TABLE `table` ADD PRIMARY KEY(id);
    -- UNIQUE KEY (UK)
    ALTER TABLE `risk` ADD UNIQUE INDEX `IMO_NO` (`IMO_NO`);

    -- PK與UK的主要差異
    -- | keys | PK | UK |
    -- | --- | --- | --- |
    -- | 用途 | 該欄位不可出現重複行資料 | 該欄位不可出現重複行資料 |
    -- | 資料表 | 整個表格只可以有一個PK | 表格內可有多個UK |
    -- | 欄位 | 不可為null | 可為null |

    ALTER TABLE test2.ss_all ADD UNIQUE `unique_index`(`cr_no`, `status`, `c1`);
    ALTER TABLE `ss_all` DROP INDEX `unique_index`;

    -- 新增 refereence key
        -- 對應動作選向
        CASCADE:
            如果是 parent紀錄被刪除了，那對應FK的chlid紀錄也一併刪除；
            如果是 parent FK更新了新值，則child 對應的FK會更新。
        SET NULL:
            刪除更新 parent都會導致 child的 FK紀錄設為 Null，如果設定為 SET NULL 記得 「child FK 欄位不要加 NOT NULL 會衝突」。
        RESTRICT:
            只要child中有包含該FK值，完全不能刪除該筆parent或更新parent的FK欄位。
        NO ACTION:
            在MySQL等同於 RESTRICT。
        SET DEFAULT:
            在MySQL InnoDB中不支援。
        -- 我也不知道為什麼一定要使用`符號，但不用會出錯(mysql 5.7)
        ALTER TABLE sv2.psc_item ADD CONSTRAINT `psc_fk` FOREIGN KEY (`pid`) REFERENCES sv2.psc (`id`) ON UPDATE CASCADE ON DELETE CASCADE;

-- view 視圖

-- index 索引 可以加速查詢速度
    -- 一般索引
        -- 如果是CHAR，VARCHAR型別，length可以小於欄位實際長度；如果是BLOB和TEXT型別，必須指定 length。
        CREATE INDEX indexName ON mytable(columnName(length));
        ALTER TABLE `table` ADD INDEX `indexName` (`columnName`);

    -- 唯一索引


-- 表格資料格式
    -- 查看其他欄位資料格式
        SELECT COLUMN_NAME,IS_NULLABLE,COLUMN_TYPE,DATA_TYPE,CHARACTER_MAXIMUM_LENGTH,CHARACTER_OCTET_LENGTH
        FROM INFORMATION_SCHEMA.COLUMNS
        WHERE TABLE_NAME = 'CERT' AND COLUMN_NAME = 'AS_POSITION';

    -- 利用查詢方式確認表格是否含有欄位
        SHOW COLUMNS FROM db.table_name WHERE FIELD IN ('server_id', 'server_update')

-- TABLE 組成
    -- DESC 替代方案
    -- 替代方案為直接使用SQL撈取資料
    SELECT * FROM information_schema.tables WHERE table_name='part2';
    SELECT * FROM information_schema.columns WHERE table_name='part2';
```

----------------------------------------
## 資料語法的使用 ##
----------------------------------------
```sql
-- 欄位條件 case when...
    select
    (case
    when paytype in (7,62) then km10*10+km8*8
    when paytype in (10,63) then km10*8+km8*6
    else money
    end) as amount from table

    -- 使用在對欄位條件做出不同的判斷與計算，可以精簡部分程式的邏輯運算


-- 插入資料
    -- insert into
    -- insert ignore
    -- replace into
    -- 插入多筆資料(此方法效能較佳)
        INSERT INTO example
            (example_id, name, value, other_value)
        VALUES
            (100, 'Name 1', 'Value 1', 'Other 1'),
            (101, 'Name 2', 'Value 2', 'Other 2'),
            (102, 'Name 3', 'Value 3', 'Other 3'),
            (103, 'Name 4', 'Value 4', 'Other 4');

        -- 插入的table比原先table多一些欄位
        INSERT INTO example_log
        SELECT *
        FROM example ex
        LEFT JOIN (SELECT 'add' AS `action`, 123 AS `user`, NOW() AS `updatetime` FROM DUAL) t ON TRUE
        WHERE ex.id = '1000';

-- join 條件
    -- 在ON條件的後面加上 AND 相當於 LEFT JOIN 一個有查詢條件的子查詢
        SELECT *
        FROM table1 t1
        LEFT JOIN table2 t2 ON t1.id=t2.t1_id AND t2.name!=''

-- EXISTS 條件
    -- 基本上， EXISTS 是用來測試內查詢有沒有產生任何結果。如果有的話，系統就會執行外查詢中的 SQL。若是沒有的話，那整個 SQL 語句就不會產生任何結果。
    -- EXISTS 的語法是：
        SELECT "欄位1"
        FROM "表格1"
        WHERE EXISTS
        (SELECT *
        FROM "表格2"
        WHERE "條件");
    -- 在內查詢中，我們並不一定要用 * 來選出所有的欄位。我們也可以選擇表格 2 中的任何欄位。這兩種做法最後的結果是一樣的。
    -- 只要內查詢成立，才會執行外查詢，外查詢的WHERE條件與內查詢並無關聯，除非有在內查詢內使用內查詢欄位=外查詢欄位


-- 特定排列
    -- 若是需要查詢結果按照特定順序排列，可利用FIELD回傳該值位於第幾個的特性
        SELECT * FROM foo f WHERE f.id IN (val2, val1, val3) ORDER BY FIELD(f.id, val2, val1, val3);

    -- 需要將每個 row 結果連接在一起，連接每個 row 值成為單欄位
    -- SEPARATOR 後面為使用的分隔符號，預設為','
        SELECT name,
        GROUP_CONCAT(DISTINCT test_score ORDER BY test_score DESC SEPARATOR ' ')
        FROM student
        GROUP BY name;

-- 從另一個資料表更新資料
    UPDATE table_A A
    INNER JOIN table_B B ON A.shop_id = B.shop_id
    SET A.category_id = B.cat_id
    WHERE A.category_id != B.cat_id;


-- 更新某部分字串
    UPDATE jijdbcdatasource
    SET connectionurl = REPLACE(connectionurl,'192.168.0.5','localhost'),username='jason', PASSWORD='123456'
    WHERE connectionurl LIKE 'jdbc:mysql://192.168.0.5%';


-- 刪除特定資料
-- Delete only the deadline rows:
DELETE `deadline` FROM `deadline` LEFT JOIN `job` ....

-- Delete the deadline and job rows:
DELETE `deadline`, `job` FROM `deadline` LEFT JOIN `job` ....
-- 例子
DELETE h FROM FCF_careservice.VolunteerHours h
LEFT JOIN FCF_careservice.Volunteer v ON h.MemberID=v.IDno
WHERE v.IDno IS NULL;

-- Delete only the job rows:
DELETE `job` FROM `deadline` LEFT JOIN `job` ....

-- 正規表示法撈取資料 (此例為撈取只有數字的資料)
select * from `tables` where colValue REGEXP '^[0-9]+$';

```

----------------------------------------
## mysql tunning ##
----------------------------------------
```sql

EXPLAIN

```

----------------------------------------
## 範例:example ##
----------------------------------------
```sql
-- 某一類別取出各類別最新資料
    SELECT * FROM rc.UP_FILE f
    INNER JOIN
    (SELECT cert_type, MAX(issue_date) AS issue_date
    FROM rc.UP_FILE files
    WHERE crno = '1000' AND `status`=0 AND file_kind=20 AND cert_type IN (1980,1981,1982,1970,1971,1972)
    GROUP BY cert_type) newest
    ON f.cert_type=newest.cert_type AND f.issue_date = newest.issue_date;

-- 對某類別排序
    -- @開頭代表變數的意思
    SELECT CASE
    WHEN @prevType = f.cert_type THEN @itemNum := @itemNum + 1
    WHEN @prevType := f.cert_type THEN @itemNum := 1
    END AS num, f.*
    FROM rc.UP_FILE f, (
    SELECT @itemNum := 1, @prevType := NULL) p
    WHERE f.crno='1000' AND f.file_kind = 20
    ORDER BY cert_type, issue_date;

-- 確保join的時候不會汙染其他資料表
-- ss_all cr_no status     compcert CR_NO CERT_TYPE
--        4214  ITM                 4214  SMC
--        4214  SRV
    SELECT s.cr_no, s.`status`, c.CR_NO, c.CERT_TYPE
    FROM test2.ss_all s
    -- 這句非常關鍵，再JOIN的時候把條件切掉 AND s.`status`!='ITM'
    LEFT JOIN compcert.CERT c ON c.CR_NO=s.cr_no AND c.CERT_TYPE='SMC' AND s.`status`!='ITM'
    WHERE s.`status` IN ('SRV','ITM') AND s.cr_no='4214'
    ORDER BY s.cr_no

-- 想要搜尋一些 table 有哪些欄位的時候 (不需要自己去一個一個看)
SELECT
    c.TABLE_NAME,
    GROUP_CONCAT(DISTINCT c.COLUMN_NAME ORDER BY FIELD(c.COLUMN_NAME, 'jobno', 'surveyor') SEPARATOR ', ') AS 'search column',
    GROUP_CONCAT(DISTINCT c.COLUMN_TYPE ORDER BY FIELD(c.COLUMN_NAME, 'jobno', 'surveyor') SEPARATOR ', ') AS 'type'
FROM INFORMATION_SCHEMA.COLUMNS c
WHERE c.TABLE_SCHEMA = 'trace'
    AND c.COLUMN_NAME IN ('jobno', 'surveyor')
    AND c.TABLE_NAME IN ('tpsa','tpsb','tpsc','tpsd','tpse','tpsf','tpsg','tpsh','tpsi','tpsj',
    'tpsk','tps_cga','kaoi','kaom','kaof','tpta','tptb','tptc','tpv','tppa','tpqd','tpqe','iso','tppb')
GROUP BY c.TABLE_NAME;

-- 比較兩個table有哪些欄位不同
SELECT `TABLE_NAME`,`COLUMN_NAME`,DATA_TYPE,COLUMN_TYPE FROM
(
    SELECT
        `TABLE_NAME`,`COLUMN_NAME`,DATA_TYPE,COLUMN_TYPE,COUNT(1) AS rowcount
    FROM information_schema.`COLUMNS`
    WHERE TABLE_SCHEMA='pa'
    AND TABLE_NAME IN ('ACCOUNT','ACCOUNT_LOG')
    GROUP BY
        COLUMN_NAME,DATA_TYPE,COLUMN_TYPE
    HAVING rowcount=1
    ORDER BY `COLUMN_NAME`, `TABLE_NAME`
) `table`;

-- 統計同一張表某些條件數量
SELECT
    SUM(CASE WHEN a.Acc_area='Taipei' THEN 1 ELSE 0 END) AS Taipei,
    SUM(CASE WHEN a.Acc_area='Kaohsiung' THEN 1 ELSE 0 END) AS Kaohsiung
FROM FCF_careservice.Memberdata m
INNER JOIN FCF_careservice.Accuser a ON m.AdminID=a.IDno
WHERE YEAR(m.keydate) = YEAR(CURRENT_DATE) AND MONTH(m.keydate) = MONTH(CURRENT_DATE - INTERVAL 1 MONTH);

-- 搜尋一些重複的資料
SELECT * FROM FCF_careservice.Memberdata WHERE C_idno IN (SELECT C_idno FROM FCF_careservice.Memberdata GROUP BY C_idno HAVING COUNT(1)>1);

-- 刪除重複資料
DELETE
FROM FCF_careservice.Volunteer
WHERE IDno IN (
    SELECT DISTINCT n1.IDno
    FROM FCF_careservice.Volunteer t1, FCF_careservice.Volunteer t2
    WHERE t1.IDno > t2.IDno AND t1.MemberID = t2.MemberID
);
```

----------------------------------------
## 錯誤處理 ##
----------------------------------------
**嚴格模式也會導致日期塞入資料的時候發生錯誤，因此若是空的日期必須轉成null才可以塞到資料庫裏面**

- Invalid datetime format: 1292 Incorrect date value: ''
  - 這個錯誤代表日期不可以使用空字串，除非關閉 mysql 的嚴格模式
-