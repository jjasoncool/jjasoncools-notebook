----------------------------------------
## basic ##
----------------------------------------
```sql
-- 此處不示範基本語法

-- 特定排序依據某數值
    -- 類似mysql的 field()，但是全部都需要使用 case when 進行手動排序
    ORDER BY CASE item WHEN '8104150ML012PP' THEN 1 WHEN '81041STK034673' THEN 2 ELSE 5 END, ROW;

-- 從別的Table複製資料及欄位到新Table
    SELECT * INTO Table_New FROM Table_Old
    -- 包含新增欄位
    SELECT CAST(NULL AS DATETIME) AS LogDate, CAST('' AS NVARCHAR(50)) AS Mode, * INTO Table_New FROM Table_Old;
```
----------------------------------------
## function ##
----------------------------------------
```sql
-- 排序
    OVER()

-- 新ROWID
    SELECT newId() AS ColId FROM table1

```
----------------------------------------
## stored procedure ##
----------------------------------------
