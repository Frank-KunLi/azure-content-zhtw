<properties
   pageTitle="將您的 SQL 程式碼移轉至 SQL 資料倉儲 | Microsoft Azure"
   description="將 SQL 程式碼移轉至 Azure SQL 資料倉儲來開發解決方案的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/02/2016"
   ms.author="lodipalm;barbkess;sonyama;jrj"/>

# 將您的 SQL 程式碼移轉至 SQL 資料倉儲

將您的程式碼從另一個資料庫移轉到 SQL 資料倉儲時，您很可能需要變更您的程式碼基底。某些 SQL 資料倉儲功能設計為以分散式方式運作，可以大幅改善效能。不過，為了維持效能和延展性，某些功能也無法使用。

## 常見 T-SQL 限制

下列清單摘要說明 Azure SQL 資料倉儲中不支援的最常見功能。此連結會帶您前往不支援功能的因應措施：

- [更新時的 ANSI 聯結][]
- [刪除時的 ANSI 聯結][]
- [merge 陳述式][]
- 跨資料庫聯結
- [資料指標][]
- [SELECT..INTO][]
- [INSERT..EXEC][]
- output 子句
- 內嵌使用者定義函數
- 多重陳述式函式
- [通用資料表運算式](#Common-table-expressions)
- [遞迴通用資料表運算式 (CTE)](#Recursive-common-table-expressions-(CTE)
- CLR 函式和程序
- $partition 函式
- 資料表變數
- 資料表值參數
- 分散式交易
- 認可/回復工作
- 儲存交易
- 執行內容 (EXECUTE AS)
- [group by 子句搭配 rollup / cube / grouping sets 選項][]
- [巢狀層級超過 8][]
- [透過檢視表更新][]
- [使用 select 進行變數指派][]
- [動態 SQL 字串沒有 MAX 資料類型][]

幸好這些限制大部分都可以克服。上面提及的相關開發文章中已提供說明。

## 支援的 CTE 功能

SQL 資料倉儲針對通用資料表運算式 (CTE) 提供部分支援。目前支援下列 CTE 功能：

- CTE 可以指定於 SELECT 陳述式中。
- CTE 可以指定於 CREATE VIEW 陳述式中。
- CTE 可以指定於 CREATE TABLE AS SELECT (CTAS) 陳述式中。
- CTE 可以指定於 CREATE REMOTE TABLE AS SELECT (CRTAS) 陳述式中。
- CTE 可以指定於 CREATE EXTERNAL TABLE AS SELECT (CETAS) 陳述式中。
- 遠端資料表可以參考自 CTE。
- 外部資料表可以參考自 CTE。
- CTE 中可以定義多個 CTE 查詢定義。

## CTE 限制

通用資料表運算式在 SQL 資料倉儲中有某些限制，包含：

- CTE 後面必須接著單一 SELECT 陳述式。INSERT、UPDATE、DELETE 和 MERGE 陳述式不受支援。
- 包含自身參考 (遞迴通用資料表運算式) 的通用資料表運算式不受支援 (請參閱下一節)。
- 不允許在 CTE 中指定多個 WITH 子句。例如，如果 CTE\_query\_definition 包含子查詢，這個子查詢就不能包含定義另一個 CTE 的巢狀 WITH 子句。
- ORDER BY 子句不能用於 CTE\_query\_definition 中，除非有指定 TOP 子句。
- 當某批次中的陳述式使用 CTE 時，它前面的陳述式後面必須接著分號。
- 在用於 sp\_prepare 所準備的陳述式時，CTE 的行為會與 PDW 中的其他 SELECT 陳述式相同。不過，如果 CTE 是做為 sp\_prepare 所準備之 CETAS 中的一部分時，就會因為針對 sp\_prepare 實作繫結的方式而導致其行為與 SQL Server 和其他 PDW 陳述式不同。如果參考 CTE 的 SELECT 使用不存在於 CTE 的錯誤資料行，sp\_prepare 將會通過而不會偵測到錯誤，但在 sp\_execute 期間則會擲回錯誤。

## 遞迴 CTE

SQL 資料倉儲並不支援遞迴 CTE。針對遞迴 CTE 的移轉可以取得某種程度的完成度，而最佳做法便是細分成多個步驟。一般來說，您可以使用迴圈，並在逐一執行遞迴中間的查詢時填入暫存資料表。一旦暫存資料表填完之後，您可以將資料傳回做為單一結果集。類似的方法已被用來解決在[由含有 rollup / cube / grouping sets options 的子句組成的群組][]文章中的 `GROUP BY WITH CUBE`。

## 不支援的系統函式

另外還有一些不支援的系統函式。您通常可能會發現資料倉儲中使用的主要函式包括：

- NEWSEQUENTIALID()
- @@NESTLEVEL()
- @@IDENTITY()
- @@ROWCOUNT()
- ROWCOUNT\_BIG
- ERROR\_LINE()

這些問題中有部分可以解決。

## @@ROWCOUNT 因應措施

若要解決缺少 @@ROWCOUNT 支援的問題，請建立會從 sys.dm\_pdw\_request\_steps 擷取最後一個資料列計數的預存程序，然後在 DML 陳述式之後執行 `EXEC LastRowCount`。

```sql
CREATE PROCEDURE LastRowCount AS
WITH LastRequest as 
(   SELECT TOP 1    request_id
    FROM            sys.dm_pdw_exec_requests
    WHERE           session_id = SESSION_ID()
    AND             resource_class IS NOT NULL
    ORDER BY end_time DESC
),
LastRequestRowCounts as
(
    SELECT  step_index, row_count
    FROM    sys.dm_pdw_request_steps
    WHERE   row_count >= 0
    AND     request_id IN (SELECT request_id from LastRequest)
)
SELECT TOP 1 row_count FROM LastRequestRowCounts ORDER BY step_index DESC
;
```

## 後續步驟
如需所有所支援 T-SQL 陳述式的完整清單，請參閱 [Transact-SQL 主題][]。

<!--Image references-->

<!--Article references-->
[更新時的 ANSI 聯結]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[刪除時的 ANSI 聯結]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[merge 陳述式]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INSERT..EXEC]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Transact-SQL 主題]: ./sql-data-warehouse-reference-tsql-statements.md

[資料指標]: ./sql-data-warehouse-develop-loops.md
[SELECT..INTO]: ./sql-data-warehouse-develop-ctas.md#selectinto
[group by 子句搭配 rollup / cube / grouping sets 選項]: ./sql-data-warehouse-develop-group-by-options.md
[由含有 rollup / cube / grouping sets options 的子句組成的群組]: ./sql-data-warehouse-develop-group-by-options.md
[巢狀層級超過 8]: ./sql-data-warehouse-develop-transactions.md
[透過檢視表更新]: ./sql-data-warehouse-develop-views.md
[使用 select 進行變數指派]: ./sql-data-warehouse-develop-variable-assignment.md
[動態 SQL 字串沒有 MAX 資料類型]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0803_2016-->