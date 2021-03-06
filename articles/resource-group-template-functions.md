<properties
   pageTitle="資源管理員範本函數 | Microsoft Azure"
   description="描述要在 Azure 資源管理員範本中用來擷取值、搭配字串和數字使用，並擷取部署資訊的函數。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/11/2016"
   ms.author="tomfitz"/>

# Azure 資源管理員範本函數

本主題描述您可以在Azure Resource Manager 範本中使用的所有函式。

範本函數和其參數不區分大小寫。例如，資源管理員在解析 **variables('var1')** 和 **VARIABLES('VAR1')** 時，會將它們視為相同。評估時，除非函式明確修改大小寫 (例如 toUpper 或 toLower)，否則函式將會保留大小寫。特定資源類型可能有與評估函式方式無關的大小寫需求。

## 數值函式

資源管理員提供下列函式以使用整數：

- [新增](#add)
- [copyIndex](#copyindex)
- [div](#div)
- [int](#int)
- [mod](#mod)
- [mul](#mul)
- [sub](#sub)


<a id="add" />
### 新增

**add(operand1, operand2)**

傳回兩個所提供整數加總後的數字。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| operand1 | 是 | 要新增的第一個整數。
| operand2 | 是 | 要新增的第二個整數。

下列範例會新增兩個參數。

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "First integer to add"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Second integer to add"
        }
      }
    },
    ...
    "outputs": {
      "addResult": {
        "type": "int",
        "value": "[add(parameters('first'), parameters('second'))]"
      }
    }

<a id="copyindex" />
### copyIndex

**copyIndex(offset)**

傳回反覆項目迴圈目前的索引。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| Offset | 否 | 要新增目前反覆運算值的數量。

這個函式一律搭配 **copy** 物件使用。如需如何使用 **copyIndex** 的完整範例，請參閱[在 Azure Resource Manager 中建立資源的多個執行個體](resource-group-create-multiple.md)。

下列範例顯示複製迴圈以及名稱中所包含的索引值。

    "resources": [ 
      { 
        "name": "[concat('examplecopy-', copyIndex())]", 
        "type": "Microsoft.Web/sites", 
        "copy": { 
          "name": "websitescopy", 
          "count": "[parameters('count')]" 
        }, 
        ...
      }
    ]


<a id="div" />
### div

**div(operand1, operand2)**

傳回兩個所提供整數相除後的商。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| operand1 | 是 | 被除數 (整數)。
| operand2 | 是 | 除數 (整數)。不能為 0。

下列範例會使用一個參數除以另一個參數。

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "Integer being divided"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Integer used to divide"
        }
      }
    },
    ...
    "outputs": {
      "divResult": {
        "type": "int",
        "value": "[div(parameters('first'), parameters('second'))]"
      }
    }

<a id="int" />
### int

**int(valueToConvert)**

將指定的值轉換成整數。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| valueToConvert | 是 | 要轉換成整數的值。值的類型只能是字串或整數。

下列範例會將使用者提供的參數值轉換成整數。

    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    }


<a id="mod" />
### mod

**mod(operand1, operand2)**

傳回兩個所提供整數相除後的餘數。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| operand1 | 是 | 被除數 (整數)。
| operand2 | 是 | 除數不得為 0。

下列範例傳回的是一個參數除以另一個參數的餘數。

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "Integer being divided"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Integer used to divide"
        }
      }
    },
    ...
    "outputs": {
      "modResult": {
        "type": "int",
        "value": "[mod(parameters('first'), parameters('second'))]"
      }
    }

<a id="mul" />
### mul

**mul(operand1, operand2)**

傳回兩個所提供整數相乘後的數字。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| operand1 | 是 | 要相乘的第一個整數。
| operand2 | 是 | 要相乘的第二個整數。

下列範例會使用一個參數乘以另一個參數。

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "First integer to multiply"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Second integer to multiply"
        }
      }
    },
    ...
    "outputs": {
      "mulResult": {
        "type": "int",
        "value": "[mul(parameters('first'), parameters('second'))]"
      }
    }

<a id="sub" />
### sub

**sub(operand1, operand2)**

傳回兩個所提供整數相減後的數字。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| operand1 | 是 | 被減數 (整數)。
| operand2 | 是 | 減數 (整數)。

下列範例會對一個參數減去另一個參數。

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "Integer subtracted from"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Integer to subtract"
        }
      }
    },
    ...
    "outputs": {
      "subResult": {
        "type": "int",
        "value": "[sub(parameters('first'), parameters('second'))]"
      }
    }

## 字串函數

資源管理員提供下列函式以使用字串：

- [base64](#base64)
- [concat](#concat)
- [length](#lengthstring)
- [padLeft](#padleft)
- [replace](#replace)
- [skip](#skipstring)
- [分割](#split)
- [字串](#string)
- [substring](#substring)
- [take](#takestring)
- [toLower](#tolower)
- [toUpper](#toupper)
- [修剪](#trim)
- [uniqueString](#uniquestring)
- [uri](#uri)


<a id="base64" />
### base64

**base64 (inputString)**

傳回輸入字串的 base64 表示法。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| inputString | 是 | 要以 base64 表示法傳回的字串值。

下列範例顯示如何使用 base64 函式。

    "variables": {
      "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
      "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

<a id="concat" />
### concat - 字串

**concat (string1、string2、string3、…)**

結合多個字串值，並傳回串連的字串。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| string1 | 是 | 要串連的字串值。
| 其他字串 | 否 | 要串連的字串值。

此函式可以接受任意數目的引數，並且可針對參數接受字串或陣列。如需串連陣列的範例，請參閱 [concat - 陣列](#concatarray)。

下列範例顯示如何結合多個字串值來傳回串連的字串。

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://', reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }


<a id="lengthstring" />
### length - 字串

**length(string)**

傳回字串中的字元數。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| string | 是 | 取得字元數所用的字串值。

如需 length 與陣列搭配使用的範例，請參閱 [length - 陣列](#length)。

下列範例傳回字串中的字元數。

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "nameLength": "[length(parameters('appName'))]"
    }
        

<a id="padleft" />
### padLeft

**padLeft(valueToPad, totalLength, paddingCharacter)**

藉由將字元新增至左邊，直到到達指定的總長度，以傳回靠右對齊的字串。
  
| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| valueToPad | 是 | 要靠右對齊的字串或 int。
| totalLength | 是 | 傳回字串中的字元總數。
| paddingCharacter | 否 | 要用於左側填補直到達到總長度的字元。預設值是空格。

下列範例顯示如何藉由新增零個字元，直到字傳達到 10 個字元，以填補使用者提供的參數值。如果原始參數值超過 10 個字元，就不新增任何字元。

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
    }

<a id="replace" />
### 取代

**replace(originalString, oldCharacter, newCharacter)**

在由另一個字元取代的指定字串中，傳回具備一個字元的所有執行個體的新字串。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| originalString | 是 | 具有由另一個字元取代之某個字元的所有執行個體的字串。
| oldCharacter | 是 | 要從原始字串中移除的字元。
| newCharacter | 是 | 要新增來取代移除字元的字元。

下列範例顯示如何從使用者提供的字串中移除所有的連字號。

    "parameters": {
        "identifier": { "type": "string" }
    },
    "variables": { 
        "newidentifier": "[replace(parameters('identifier'),'-','')]"
    }

<a id="skipstring" />
### skip - 字串
**skip(originalValue, numberToSkip)**

傳回在字串中指定數字之後之所有字元的字串。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| originalValue | 是 | 略過所用的字串。
| numberToSkip | 是 | 要略過的字元數。如果此值為 0 或更小，則會傳回字串中的所有字元。如果此值大於字串的長度，則會傳回空白陣列。 

如需 skip 和陣列搭配使用的範例，請參閱 [skip - 陣列](#skip)。

下列範例會略過字串中指定的字元數目。

    "parameters": {
      "first": {
        "type": "string",
        "metadata": {
          "description": "Value to use for skipping"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of characters to skip"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "string",
        "value": "[skip(parameters('first'),parameters('second'))]"
      }
    }


<a id="split" />
### split

**split(inputString, delimiterString)**

**split(inputString, delimiterString)**

傳回包含輸入字串之子字串的字串陣列，其中的子字串已使用指定的分隔符號分隔。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| inputString | 是 | 要分割的字串。
| 分隔符號 | 是 | 使用的分隔符號可以是單一字串或字串的陣列。

下列範例會使用逗號來分割輸入字串。

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "stringPieces": "[split(parameters('inputString'), ',')]"
    }

下一個範例使用逗號或分號分割輸入的字串。

    "variables": {
      "stringToSplit": "test1,test2;test3",
      "delimiters": [ ",", ";" ]
    },
    "resources": [ ],
    "outputs": {
      "exampleOutput": {
        "value": "[split(variables('stringToSplit'), variables('delimiters'))]",
        "type": "array"
      }
    }

<a id="string" />
### string

**string(valueToConvert)**

將指定的值轉換成字串。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| valueToConvert | 是 | 要轉換成字串的值。任何類型的值均可轉換，包括物件和陣列。

下列範例會將使用者提供的參數值轉換成字串。

    "parameters": {
      "jsonObject": {
        "type": "object",
        "defaultValue": {
          "valueA": 10,
          "valueB": "Example Text"
        }
      },
      "jsonArray": {
        "type": "array",
        "defaultValue": [ "a", "b", "c" ]
      },
      "jsonInt": {
        "type": "int",
        "defaultValue": 5
      }
    },
    "variables": { 
      "objectString": "[string(parameters('jsonObject'))]",
      "arrayString": "[string(parameters('jsonArray'))]",
      "intString": "[string(parameters('jsonInt'))]"
    }

<a id="substring" />
### substring

**substring(stringToParse, startIndex, length)**

傳回起始於指定字元位置的子字串，其中包含指定的字元數。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| stringToParse | 是 | 要用來擷取子字串的原始字串。
| startIndex | 否 | 起始字元位置為零的子字串。
| length | 否 | 子字串的字元數。

下列範例擷取了參數的前三個字元。

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "prefix": "[substring(parameters('inputString'), 0, 3)]"
    }

<a id="takestring" />
### take - 字串
**take(originalValue, numberToTake)**

傳回字串開頭之指定字元數目的字串。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| originalValue | 是 | 要從其中擷取字元的字串。
| numberToTake | 是 | 要擷取的字元數。如果此值為 0 或更小，則會傳回空白字串。如果此值大於指定字串的長度，則會傳回字串中的所有字元。

如需 take 和陣列搭配使用的範例，請參閱 [ - 陣列](#take)。

下列範例會從字串中擷取指定的字元數。

    "parameters": {
      "first": {
        "type": "string",
        "metadata": {
          "description": "Value to use for taking"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of characters to take"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "string",
        "value": "[take(parameters('first'), parameters('second'))]"
      }
    }

<a id="tolower" />
### toLower

**toLower(stringToChange)**

將指定的字串轉換為小寫。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| stringToChange | 是 | 要轉換成小寫的字串。

下列範例會將使用者提供的參數值轉換為小寫。

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "lowerCaseAppName": "[toLower(parameters('appName'))]"
    }

<a id="toupper" />
### toUpper

**toUpper(stringToChange)**

將指定的字串轉換為大寫。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| stringToChange | 是 | 要轉換成大寫的字串。

下列範例會將使用者提供的參數值轉換為大寫。

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "upperCaseAppName": "[toUpper(parameters('appName'))]"
    }

<a id="trim" />
### 修剪

**trim (stringToTrim)**

從指定的字串中移除所有開頭和尾端空白字元。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| stringToTrim | 是 | 要修剪的字串。

下列範例會修剪由使用者提供之參數值的空白字元。

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "trimAppName": "[trim(parameters('appName'))]"
    }

<a id="uniquestring" />
### uniqueString

**uniqueString (baseString, ...)**

根據當作參數提供的值，建立具決定性的雜湊字串。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| baseString | 是 | 雜湊函式中用來建立唯一字串的字串。
| 視需要，也會使用其他參數 | 否 | 您可以視需要新增多個字串，來建立指定唯一性層級的值。

當您需要建立資源的唯一名稱時，這個函式很有幫助。您提供限制結果唯一性範圍的參數值。您可以指定名稱對於訂用帳戶、資源群組或部署是否唯一。

傳回的值不是隨機字串，而是雜湊函式的結果。傳回的值為 13 個字元長。它不是全域唯一的。建議您將值與來自命名慣例的前置詞結合，建立有意義的名稱。下列範例顯示傳回值的格式。當然，實際值會根據提供的參數而有所不同。

    tcvhiyu5h2o5o

下列範例顯示如何使用 uniqueString 來建立常用層級的唯一值。

在訂用帳戶範圍內是唯一

    "[uniqueString(subscription().subscriptionId)]"

在資源群組範圍內是唯一

    "[uniqueString(resourceGroup().id)]"

在資源群組的部署範圍內是唯一

    "[uniqueString(resourceGroup().id, deployment().name)]"
    
下列範例示範如何根據資源群組建立儲存體帳戶的唯一名稱 (若以相同方式建構，則在此資源群組內名稱不是唯一的)。

    "resources": [{ 
        "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]", 
        "type": "Microsoft.Storage/storageAccounts", 
        ...



<a id="uri" />
### uri

**uri (baseUri, relativeUri)**

藉由結合 baseUri 和 relativeUri 字串建立絕對 URI。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| baseUri | 是 | 基底 uri 的字串。
| relativeUri | 是 | 要加入至基底 uri 字串的相對 uri 字串。

**baseUri** 參數的值可包含特定檔案，但在建構 URI 時只會使用基底路徑。例如，將 **http://contoso.com/resources/azuredeploy.json** 作為 baseUri 參數傳遞時，會產生 http://contoso.com/resources/** 的基底 URI**。

下列範例顯示如何根據上層範本的值建構巢狀範本的連結。

    "templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"

## 陣列函數

資源管理員提供下列函式以使用陣列值。

- [concat](#concatarray)
- [length](#length)
- [skip](#skip)
- [take](#take)

若要取得以值分隔的字串值陣列，請參閱[分割](#split)。

<a id="concatarray" />
### concat - 陣列

**concat (array1, array2, array3, ...)**

結合多個陣列，並傳回串連的陣列。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| array1 | 是 | 要串連的陣列。
| 其他的陣列 | 否 | 要串連的陣列。

此函式可以接受任意數目的引數，並且可針對參數接受字串或陣列。如需串連字串值的範例，請參閱 [concat - 字串](#concat)。

下一個範例顯示如何結合兩個陣列。

    "parameters": {
        "firstarray": {
            type: "array"
        }
        "secondarray": {
            type: "array"
        }
     },
     "variables": {
         "combinedarray": "[concat(parameters('firstarray'), parameters('secondarray'))]
     }
        

<a id="length" />
### length - 陣列

**length(array)**

傳回陣列中的元素數目。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| array | 是 | 取得元素數目所用的陣列。

建立資源時，您可在陣列中使用此函式指定反覆運算的數量。下列範例中，參數 **siteNames** 會參考在建立網站時要使用的名稱陣列。

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

如需有關在此陣列中使用函式的詳細資訊，請參閱[在 Azure 資源管理員中建立資源的多個執行個體](resource-group-create-multiple.md)。

如需字串值與 length 搭配的範例，請參閱 [length - 字串](#lengthstring)。

<a id="skip" />
### skip - 陣列
**skip(originalValue, numberToSkip)**

傳回陣列中指定的元素數之後的所有元素的陣列。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| originalValue | 是 | 用於略過的陣列。
| numberToSkip | 是 | 要略過的元素數目。如果此值為 0 或更小，則會傳回陣列中的所有元素。如果此值大於陣列的長度，則會傳回空白陣列。 

如需 skip 和字串搭配使用的範例，請參閱 [skip - 陣列](#skipstring)。

下列範例會略過陣列中指定的元素數目。

    "parameters": {
      "first": {
        "type": "array",
        "metadata": {
          "description": "Values to use for skipping"
        },
        "defaultValue": [ "one", "two", "three" ]
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of elements to skip"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "array",
        "value": "[skip(parameters('first'), parameters('second'))]"
      }
    }

<a id="take" />
### take - 陣列
**take(originalValue, numberToTake)**

傳回其中包含從陣列開頭算起指定的元素數目的陣列。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| originalValue | 是 | 要從其中擷取元素的陣列。
| numberToTake | 是 | 要擷取的元素數目。如果此值為 0 或更小，則會傳回空白陣列。如果此值大於指定陣列的長度，則會傳回陣列中的所有元素。

如需 take 和字串搭配使用的範例，請參閱 [ - 字串](#takestring)。

下列範例會從陣列中取得指定的元素數目。

    "parameters": {
      "first": {
        "type": "array",
        "metadata": {
          "description": "Values to use for taking"
        },
        "defaultValue": [ "one", "two", "three" ]
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of elements to take"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "array",
        "value": "[take(parameters('first'),parameters('second'))]"
      }
    }

## 部署值函式

資源管理員提供下列函式，以從與部署相關的範本和值的區段中取得值：

- [部署](#deployment)
- [參數](#parameters)
- [變數](#variables)

若要從資源、資源群組或訂用帳戶中取得值，請參閱[資源函式](#resource-functions)。

<a id="deployment" />
### 部署

**deployment()**

傳回目前部署作業的相關資訊。

此函式會傳回部署期間所傳遞的物件。視部署物件是以連結或內嵌物件形式傳遞，所傳回物件中的屬性將有所不同。

部署物件以內嵌形式傳遞時 (例如使用 Azure PowerShell 中的 **-TemplateFile** 參數指向本機檔案時)，所傳回的物件為下列格式：

    {
        "name": "",
        "properties": {
            "template": {
                "$schema": "",
                "contentVersion": "",
                "parameters": {},
                "variables": {},
                "resources": [
                ],
                "outputs": {}
            },
            "parameters": {},
            "mode": "",
            "provisioningState": ""
        }
    }

部署物件以連結形式傳遞時 (例如使用 **-TemplateUri** 參數指向遠端檔案時)，所傳回的物件為下列格式：

    {
        "name": "",
        "properties": {
            "templateLink": {
                "uri": ""
            },
            "template": {
                "$schema": "",
                "contentVersion": "",
                "parameters": {},
                "variables": {},
                "resources": [],
                "outputs": {}
            },
            "parameters": {},
            "mode": "",
            "provisioningState": ""
        }
    }

下列範例說如何根據上層範本 URI，使用部署() 連結至另一個範本。

    "variables": {  
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
    }  

<a id="parameters" />
### 參數

**parameters (parameterName)**

傳回參數值。指定的參數名稱必須定義於範本的 parameters 區段中。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| parameterName | 是 | 要傳回的參數名稱。

下列範例顯示 parameters 函數的簡化用法。

    "parameters": { 
      "siteName": {
          "type": "string"
      }
    },
    "resources": [
       {
          "apiVersion": "2014-06-01",
          "name": "[parameters('siteName')]",
          "type": "Microsoft.Web/Sites",
          ...
       }
    ]

<a id="variables" />
### 變數

**variables (variableName)**

傳回變數的值。指定的變數名稱必須定義於範本的 variables 區段中。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| 變數名稱 | 是 | 要傳回的變數名稱。

下列範例會使用變數值。

    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
      {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
      }
    ],

## 資源函式

資源管理員提供下列函式以取得資源值：

- [listKeys 和 list{Value}](#listkeys)
- [提供者](#providers)
- [reference](#reference)
- [resourceGroup](#resourcegroup)
- [resourceId](#resourceid)
- [訂用帳戶](#subscription)

若要從參數、變數或目前的部署中取得值，請參閱[部署值函式](#deployment-value-functions)。

<a id="listkeys" /> <a id="list" />
### listKeys 和 list{Value}

**listKeys (resourceName or resourceIdentifier, apiVersion)**

**list{Value} (resourceName or resourceIdentifier, apiVersion)**

對支援 list 作業的任何資源類型傳回值。最常見的用法是 **listKeys**。
  
| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| resourceName 或 resourceIdentifier | 是 | 資源的唯一識別碼。
| apiVersion | 是 | 資源執行階段狀態的 API 版本。

開頭為 **list** 的任何作業都可在您的範本中用為函式。可用作業不只包含 **listKeys**、還可包含像 **list**、**listAdminKeys** 和 **listStatus** 等作業。為判斷哪一個資源類型具有清單作業，請使用以下 PowerShell 命令。

    Get-AzureRmProviderOperation -OperationSearchString *  | where {$_.Operation -like "*list*"} | FT Operation

或者，請擷取具有 Azure CLI 的清單。以下範例會擷取 **apiapps** 的所有作業，並使用 JSON 公用程式 [jq](http://stedolan.github.io/jq/download/) 來只篩選出 list 作業。

    azure provider operations show --operationSearchString */apiapps/* --json | jq ".[] | select (.operation | contains("list"))"

使用 [resourceId](./#resourceid) 函式或使用格式 **{providerNamespace}/{resourceType}/{resourceName}**，即可指定 resourceId。

下列範例顯示如何在 outputs 區段中從儲存體帳戶傳回主要和次要金鑰。

    "outputs": { 
      "listKeysOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2016-01-01')]", 
        "type" : "object" 
      } 
    } 

從 listKeys 傳回的物件具有下列格式︰

    {
      "keys": [
        {
          "keyName": "key1",
          "permissions": "Full",
          "value": "{value}"
        },
        {
          "keyName": "key2",
          "permissions": "Full",
          "value": "{value}"
        }
      ]
    }

<a id="providers" />
### 提供者

**providers (providerNamespace, [resourceType])**

傳回資源提供者和其所支援資源類型的相關資訊。如果未提供資源類型，則函式會傳回資源提供者所有的支援類型。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| providerNamespace | 是 | 提供者的命名空間
| resourceType | 否 | 所指定命名空間內的資源類型。

每個支援類型都會以下列格式傳回。不保證陣列排序。

    {
        "resourceType": "",
        "locations": [ ],
        "apiVersions": [ ]
    }

下列範例顯示如何使用 provider 函數：

    "outputs": {
	    "exampleOutput": {
		    "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
		    "type" : "object"
	    }
    }

<a id="reference" />
### reference

**reference (resourceName or resourceIdentifier, [apiVersion])**

傳回代表另一個資源執行階段狀態的物件。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| resourceName 或 resourceIdentifier | 是 | 資源的名稱或唯一識別碼。
| apiVersion | 否 | 指定的資源的 API 版本。如果在相同的範本內未供應資源，則請包含此參數。

**reference** 函數會從執行階段狀態衍生其值，因此不能用在 variables 區段中。它可以用於範本的 outputs 區段中。

如果在相同的範本內佈建所參考的資源，則可使用 reference 函式來隱含宣告一個資源相依於另一個資源。您不需要同時使用 **dependsOn** 屬性。所參考的資源完成部署之前不會評估函式。

下列範例會參考相同的範本中部署的儲存體帳戶。

    "outputs": {
		"NewStorage": {
			"value": "[reference(parameters('storageAccountName'))]",
			"type" : "object"
		}
	}

下列範例會參考未部署在此範本中，但是當部署資源時存在於相同資源群組內的儲存體帳戶。

    "outputs": {
		"ExistingStorage": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01')]",
			"type" : "object"
		}
	}

您可以從傳回的物件 (例如 blob 端點 URI) 擷取特定的值，如下範例所示。

    "outputs": {
		"BlobUri": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
			"type" : "string"
		}
	}

下列範例會參考不同的資源群組中的儲存體帳戶。

    "outputs": {
		"BlobUri": {
			"value": "[reference(resourceId(parameters('relatedGroup'), 'Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
			"type" : "string"
		}
	}

傳回物件上的內容會因資源類型而有所不同。

<a id="resourcegroup" />
### resourceGroup

**resourceGroup()**

傳回代表目前資源群組的物件。

傳回的物件會使用下列格式：

    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
      "name": "{resourceGroupName}",
      "location": "{resourceGroupLocation}",
      "tags": {
      },
      "properties": {
        "provisioningState": "{status}"
      }
    }

下列範例使用資源群組位置來指派網站的位置。

    "resources": [
       {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          ...
       }
    ]

<a id="resourceid" />
### resourceId

**resourceId ([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)**

傳回資源的唯一識別碼。
      
| 參數 | 必要 | 說明
| :---------------: | :------: | :----------
| subscriptionId | 否 | 預設值為目前的訂用帳戶。需要擷取另一個訂用帳戶中的資源群組時，請指定此值。
| resourceGroupName | 否 | 預設值為目前資源群組。需要擷取另一個訂用帳戶中的資源群組時，請指定此值。
| resourceType | 是 | 資源的類型 (包括資源提供者命名空間)。
| resourceName1 | 是 | 資源的名稱。
| resourceName2 | 否 | 如果是巢狀資源，則為下一個資源名稱區段。

如果資源名稱不確定或未佈建在相同的範本內，請使用此函數。識別碼會以下列格式傳回：

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}

下列範例顯示如何擷取網站和資料庫的資源識別碼。網站存在於名稱為 **myWebsitesGroup** 的資源群組中，而資料庫存在於此範本的目前資源群組中。

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]
    
通常，在替代資源群組中使用儲存體帳戶或虛擬網路時，需要使用此函數。儲存體帳戶或虛擬網路可能用於多個資源群組中；因此，您不想要在刪除單一資源群組時刪除它們。下列範例顯示如何輕鬆地使用外部資源群組中的資源：

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]
    }

<a id="subscription" />
### 訂用帳戶

**subscription()**

以下列格式傳回訂用帳戶的詳細資料。

    {
        "id": "/subscriptions/#####",
        "subscriptionId": "#####",
        "tenantId": "#####"
    }

下列範例顯示在 outputs 區段中所呼叫的 subscription 函式。

    "outputs": { 
      "exampleOutput": { 
          "value": "[subscription()]", 
          "type" : "object" 
      } 
    } 


## 後續步驟
- 如需有關 Azure 資源管理員範本中各區段的說明，請參閱[編寫 Azure 資源管理員範本](resource-group-authoring-templates.md)
- 若要合併多個範本，請參閱[透過 Azure 資源管理員使用連結的範本](resource-group-linked-templates.md)
- 建立資源類型時若要逐一查看指定的次數，請參閱[在 Azure 資源管理員中建立資源的多個執行個體](resource-group-create-multiple.md)。
- 若要了解如何部署已建立的範本，請參閱[使用 Azure 資源管理員範本部署應用程式](resource-group-template-deploy.md)

<!----HONumber=AcomDC_0907_2016-->