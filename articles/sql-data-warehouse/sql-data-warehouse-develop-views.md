<properties
   pageTitle="SQL 資料倉儲中的檢視 | Microsoft Azure"
   description="在 Azure SQL 資料倉儲中使用 Transact-SQL 檢視開發解決方案的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/01/2016"
   ms.author="jrj;barbkess;sonyama"/>


# SQL 資料倉儲中的檢視

在 SQL 資料倉儲中檢視特別有用。以許多不同的方式使用，可以提升您的方案品質。本文特別強調幾個範例，說明如何以檢視來豐富您的解決方案，以及需要考量的限制。

> [AZURE.NOTE] 本文中不會討論 `CREATE VIEW` 的語法。如需此參考資訊，請參閱 MSDN 上的 [CREATE VIEW][] 文章。

## 架構抽象概念
極為常見的應用程式模式就是在載入資料時，使用後面接著物件重新命名模式的 CREATE TABLE AS SELECT (CTAS) 來重建資料表。

下列範例會將新的日期記錄加入至日期維度。請注意新的資料表 DimDate\_New 最初是如何建立，然後重新命名，以取代原始版本的資料表。

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

不過，此方法可能導致資料表在使用者的檢視中出現和消失，還有「資料表不存在」錯誤訊息。儘管基礎物件已重新命名，檢視可為使用者提供一致的展示層。透過檢視讓使用者存取資料，意味著使用者不需要看見基礎資料表。這會提供一致的使用者經驗，同時確保資料倉儲設計人員可以發展資料模型，也可在資料載入過程中使用 CTAS 充分發揮效能。

## 效能最佳化
檢視也可用來強化資料表之間的效能最佳化聯結。例如，檢視可以納入備援散發金鑰作為聯結準則的一部分，以將資料移動最小化。檢視的另一項好處是強制執行特定查詢或聯結提示。以這種方式使用檢視，可確保一律以最佳方式執行聯結，使用者不需要記住其聯結的正確建構。

## 限制
SQL 資料倉儲中的檢視僅限中繼資料使用。因此無法使用下列選項︰

- 	沒有結構描述繫結選項
- 	無法透過檢視更新基底資料表
- 	無法在暫存資料表上建立檢視
- 	不支援 EXPAND / NOEXPAND 提示
- 	SQL 資料倉儲中沒有索引檢視表


## 後續步驟
如需更多開發秘訣，請參閱 [SQL 資料倉儲開發概觀][]。如需 `CREATE VIEW` 語法，請參閱 [CREATE VIEW][]。

<!--Image references-->

<!--Article references-->
[SQL 資料倉儲開發概觀]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CREATE VIEW]: https://msdn.microsoft.com/zh-TW/library/ms187956.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0706_2016-->