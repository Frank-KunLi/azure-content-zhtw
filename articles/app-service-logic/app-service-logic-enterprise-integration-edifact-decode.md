<properties 
	pageTitle="了解企業整合套件解碼 EDIFACT 訊息連接器 | Microsoft Azure App Service | Microsoft Azure" 
	description="了解如何使用夥伴搭配企業整合套件與 Logic Apps" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="padmavc" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/15/2016" 
	ms.author="padmavc"/>

# 開始使用解碼 EDIFACT 訊息

驗證 EDI 和夥伴特定屬性，產生每個交易集的 XML 文件以及已處理交易的確認。

## 建立連線

### 必要條件

* Azure 帳戶；您可以建立一個[免費帳戶](https://azure.microsoft.com/free)

* 需要有整合帳戶才能使用解碼 EDIFACT 訊息連接器。詳細資料請參閱如何建立[整合帳戶](./app-service-logic-enterprise-integration-create-integration-account.md)、[合作夥伴](./app-service-logic-enterprise-integration-partners.md)和 [EDIFACT 合約](./app-service-logic-enterprise-integration-edifact.md)

### 使用下列步驟連線至解碼 EDIFACT 訊息︰

1. [建立邏輯應用程式](./app-service-logic-create-a-logic-app.md)可提供範例。

2. 此連接器並沒有任何觸發程序。您可以使用其他觸發程序來啟動邏輯應用程式，例如 [要求] 觸發程序。在邏輯應用程式設計工具中，新增一個觸發程序，然後新增一個動作。從下拉式清單中選取 [顯示 Microsoft Managed API]，然後在搜尋方塊中輸入 "EDIFACT"。選取解碼 EDIFACT 訊息

	![搜尋 EDIFACT](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage1.png)
	
3. 如果您之前尚未建立與整合帳戶的任何連線，系統將會提示您輸入連線詳細資料

	![建立整合帳戶](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage2.png)

4. 輸入整合帳戶詳細資料。具有星號的屬性為必要項目

	| 屬性 | 詳細資料 |
	| -------- | ------- |
	| 連線名稱 * | 為連線輸入任何名稱 |
	| 整合帳戶 * | 輸入整合帳戶名稱。請確定您的整合帳戶和邏輯應用程式位於相同的 Azure 位置 |

	完成後，連線詳細資料看起來類似下圖

	![整合帳戶已建立](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage3.png)

5. 選取 [建立]

6. 請注意，已建立連線

	![整合帳戶連線詳細資料](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage5.png)

7. 選取要解碼的 EDIFACT 一般檔案訊息

	![提供必要欄位](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage5.png)

## EDIFACT 解碼處理以下項目

* 比對傳送者辨識符號和識別項，以及接收者辨識符號和識別項，以解析合約
* 將單一訊息中的多個交換分割成個別的交換分割。
* 針對交易夥伴合約驗證信封
* 解譯交換。
* 驗證 EDI 和夥伴特定屬性，包括
	* 驗證交換信封的結構。
	* 針對控制結構描述進行信封的結構描述驗證。
	* 針對訊息結構描述進行交易集資料元素的結構描述驗證。
	* 對交易集資料元素執行 EDI 驗證
* 驗證交換、群組和交易集控制編號並未重複 (若已設定)
	* 針對先前已接收的交換檢查交換控制編號。
	* 針對交換中的其他群組控制編號檢查群組控制編號。
	* 針對該群組中其他交易集控制編號檢查交易集控制編號。
* 產生每個交易集的 XML 文件。
* 將整個交換轉換為 XML
	* 將交換分割為交易集 - 暫停發生錯誤的交易集︰將交換中每個交易集剖析為個別的 XML 文件。如果交換中有一或多個交易集無法通過驗證，則 EDIFACT 解碼只會暫停那些交易集。
	* 將交換分割為交易集 - 暫停發生錯誤的交換︰將交換中每個交易集剖析為個別的 XML 文件。如果交換中有一或多個交易集無法通過驗證，則 EDIFACT 解碼會暫停整個交換。
	* 保留交換 - 暫停發生錯誤的交易集︰建立整個批次交換的 XML 文件。EDIFACT 解碼只會暫停未通過驗證的交易集，而繼續處理所有其他的交易集
	* 保留交換 - 暫停發生錯誤的交換︰建立整個批次交換的 XML 文件。如果交換中有一或多個交易集無法通過驗證，則 EDIFACT 解碼會暫停整個交換。
* 產生技術 (控制) 和/或功能確認 (若已設定)。
	* 技術確認或 CONTRL ACK 會報告完整接收的交換所進行的語法檢查結果。
	* 功能確認會確認接受或拒絕已接收的交換或群組

## 後續步驟

[深入了解企業整合套件](./app-service-logic-enterprise-integration-overview.md "了解企業整合套件")

<!---HONumber=AcomDC_0824_2016-->