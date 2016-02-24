<properties
   pageTitle="リソース マネージャーのテンプレートの式 | Microsoft Azure"
   description="値の取得、文字列の処理、デプロイ情報の取得のために、Azure リソース マネージャーのテンプレートで使用する関数について説明します。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/07/2015"
   ms.author="tomfitz"/>

# Azure リソース マネージャーのテンプレートの式

このトピックでは、Azure リソース マネージャーのテンプレートで使用できるすべての式について説明します。

テンプレート式とそのパラメーターでは大文字と小文字が区別されません。 たとえば、リソース マネージャーの解決 **variables('var1')** と **VARIABLES('VAR1')** が同じです。 評価の際、式は、大文字/小文字を明確に変更する (toUpper、toLower など) 場合を除き、大文字/小文字を保持します。 特定のリソースの種類では、式の評価方法とは無関係に、大文字/小文字の要件が存在する場合があります。

## 数値式

リソース マネージャーは、整数を操作する次の式を提供します。

- [追加](#add)
- [copyIndex](#copyindex)
- [div](#div)
- [int](#int)
- [length](#length)
- [mod](#mod)
- [mul](#mul)
- [sub](#sub)


<a id="add" />
### 追加

**add(operand1, operand2)**

指定された 2 つ整数の合計を返します。

| パラメーター                          | 必須 | 説明
| :--------------------------------: | :------: | :----------
| operand1                           |   あり    | 使用する最初のオペランドです。
| operand2                           |   あり    | 使用する 2 番目のオペランドです。


<a id="copyindex" />
### copyIndex

**copyIndex(offset)**

反復処理のループの現在のインデックスを返します。 

この式は常に併用、 **コピー** オブジェクトです。 使用例について **copyIndex**, を参照してください [Azure リソース マネージャーでリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)です。


<a id="div" />
### div

**div(operand1, operand2)**

指定された 2 つの整数の整数除算を返します。

| パラメーター                          | 必須 | 説明
| :--------------------------------: | :------: | :----------
| operand1                           |   あり    | 割られる数。
| operand2                           |   あり    | 割る数。ゼロ (0) 以外にする必要があります。


<a id="int" />
### int

**int(valueToConvert)**

指定された値を整数に変換します。

| パラメーター                          | 必須 | 説明
| :--------------------------------: | :------: | :----------
| valueToConvert                     |   あり    | 整数に変換する値。 値の種類は文字列か整数になります。

次の例では、ユーザー指定のパラメーター値を整数に変換します。

    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    }


<a id="length" />
### length

**length(配列または文字列)**

配列の要素数または文字列の文字数を返します。 この関数を配列と共に使用して、リソースを作成するときのイテレーション数を指定できます。 次の例では、パラメーター **siteNames** は web サイトを作成するときに使用する名前の配列を参照してください。

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

配列がこの関数の使い方の詳細については、次を参照してください。 [Azure リソース マネージャーでリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)です。

次のように、文字列と共に使用することもできます。

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "nameLength": "[length(parameters('appName'))]"
    }


<a id="mod" />
### mod

**mod(operand1, operand2)**

指定された 2 つの整数を使用した整数除算の剰余を返します。

| パラメーター                          | 必須 | 説明
| :--------------------------------: | :------: | :----------
| operand1                           |   あり    | 割られる数。
| operand2                           |   あり    | 割る数。ゼロ (0) 以外にする必要があります。



<a id="mul" />
### mul

**mul(operand1, operand2)**

指定された 2 つの整数の乗算を返します。

| パラメーター                          | 必須 | 説明
| :--------------------------------: | :------: | :----------
| operand1                           |   あり    | 使用する最初のオペランドです。
| operand2                           |   あり    | 使用する 2 番目のオペランドです。


<a id="sub" />
### sub

**sub(operand1, operand2)**

指定された 2 つの整数の減算を返します。

| パラメーター                          | 必須 | 説明
| :--------------------------------: | :------: | :----------
| operand1                           |   あり    | 減算される数値。
| operand2                           |   あり    | 減算する数値。


## 文字列式

リソース マネージャーは、文字列を操作する次の式を提供します。

- [base64](#base64)
- [concat](#concat)
- [padLeft](#padleft)
- [replace](#replace)
- [split](#split)
- [文字列](#string)
- [toLower](#tolower)
- [toUpper](#toupper)
- [trim](#trim)
- [uniqueString](#uniquestring)
- [uri](#uri)

文字列または配列に文字数を取得するには、「 [長さ](#length)です。

<a id="base64" />
### base64

**base64 (inputString)**

入力文字列の base64 表現を返します。

| パラメーター                          | 必須 | 説明
| :--------------------------------: | :------: | :----------
| inputString                        |   あり    | Base 64 形式として返す文字列の値。

次の例は、base64 関数を使用する方法を示します。

    "variables": {
      "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
      "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

<a id="concat" />
### concat

**concat (arg1, arg2, arg3, ...)**

複数の文字列値を結合し、結果の文字列値を返します。 この関数は、任意の数の引数を取ることができます。

次の例は、複数の値を結合して 1 つの値を返す方法を示しています。

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }

<a id="padleft" />
### padLeft

**padLeft(stringToPad, totalLength, paddingCharacter)**

指定された長さに到達するまで左側に文字を追加していくことで、右揃えの文字列を返します。
  
| パラメーター                          | 必須 | 説明
| :--------------------------------: | :------: | :----------
| stringToPad                        |   あり    | 右揃えにする文字列。
| totalLength                        |   あり    | 返される文字列の文字合計数。
| paddingCharacter                   |   あり    | 左余白の長さに到達するまで使用する文字。

次の例では、文字列が 10 文字に達するまでゼロ文字を追加することで、ユーザー指定のパラメーター値に埋め込む方法を示します。 元のパラメーター値が 10 文字より長い場合、文字は追加されません。

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
    }

<a id="replace" />
### replace

**replace(originalString, oldCharacter, newCharacter)**

指定された文字列内で、1 文字を別の文字で置き換えたすべてのインスタンスを含む新しい文字列を返します。

| パラメーター                          | 必須 | 説明
| :--------------------------------: | :------: | :----------
| originalString                     |   あり    | 1 文字を別の文字で置き換えたすべてのインスタンスを含む文字列。
| oldCharacter                       |   あり    | 元の文字列から削除する文字。
| newCharacter                       |   あり    | 削除された文字の代わりに追加する文字。

次の例では、ユーザーが指定した文字列からすべてのダッシュ (-) を削除する方法を示します。

    "parameters": {
        "identifier": { "type": "string" }
    },
    "variables": { 
        "newidentifier": "[replace(parameters('identifier'),'-','')]"
    }

<a id="split" />
### split

**分割 (inputString、区切り記号)**
**分割 (inputString、[区切り記号])**

送信された区切り記号で区切られた、入力文字列の部分文字列が格納されている、文字列の配列を返します。

| パラメーター                          | 必須 | 説明
| :--------------------------------: | :------: | :----------
| inputString                        |   あり    | 分割する文字列。
| delimiter                          |   あり    | 使用する区切り記号。1 つの文字列または文字列の配列を指定できます。

次の例では、入力文字列をコンマで分割します。

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "stringPieces": "[split(parameters('inputString'), ',')]"
    }

<a id="string" />
### 文字列

**string(valueToConvert)**

指定された値を文字列に変換します。

| パラメーター                          | 必須 | 説明
| :--------------------------------: | :------: | :----------
| valueToConvert                     |   あり    | 文字列に変換する値。 値の種類はブール値、整数、文字列のいずれかになります。

次の例では、ユーザー指定のパラメーター値を文字列に変換します。

    "parameters": {
        "appId": { "type": "int" }
    },
    "variables": { 
        "stringValue": "[string(parameters('appId'))]"
    }

<a id="tolower" />
### toLower

**toLower(stringToChange)**

指定された文字列を小文字に変換します。

| パラメーター                          | 必須 | 説明
| :--------------------------------: | :------: | :----------
| stringToChange                     |   あり    | 小文字に変換する文字列。

次の例では、ユーザー指定のパラメーター値を小文字に変換します。

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "lowerCaseAppName": "[toLower(parameters('appName'))]"
    }

<a id="toupper" />
### toUpper

**toUpper(stringToChange)**

指定した文字列を大文字に変換します。

| パラメーター                          | 必須 | 説明
| :--------------------------------: | :------: | :----------
| stringToChange                     |   あり    | 大文字に変換する文字列。

次の例では、ユーザー指定のパラメーター値を大文字に変換します。

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "upperCaseAppName": "[toUpper(parameters('appName'))]"
    }

<a id="trim" />
### trim

**trim (stringToTrim)**

指定された文字列から先頭と末尾の空白文字をすべて削除します。

| パラメーター                          | 必須 | 説明
| :--------------------------------: | :------: | :----------
| stringToTrim                       |   あり    | トリムする文字列。

次の例では、ユーザー指定のパラメーター値から空白文字を削除します。

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "trimAppName": "[trim(parameters('appName'))]"
    }

<a id="uniquestring" />
### uniqueString

**uniqueString (stringForCreatingUniqueString, ...)**

指定された文字列の 64 ビット ハッシュを実行し、一意の文字列を作成します。 この関数は、リソースの一意の名前を作成する必要がある場合に便利です。 結果の一意性のレベルを表すパラメーターの値を指定します。 サブスクリプション、リソース グループ、またはデプロイメントに関して名前が一意であるかどうかを指定できます。 

| パラメーター                          | 必須 | 説明
| :--------------------------------: | :------: | :----------
| stringForCreatingUniqueString      |   あり    | 一意の文字列を作成するためにハッシュ関数で使用される基本文字列。
| 必要に応じて追加のパラメーター    | いいえ       | 文字列をいくつでも追加して、一意性のレベルを指定する値を作成できます。

返される値は完全にランダムな文字列ではなく、ハッシュ関数の結果になります。 返される値は、13 文字です。 グローバルに一意であるかは保証されません。 命名規則にある何らかのプレフィックスをこの値と組み合わせて、よりわかりやすい名前を作成することもできます。

次の例は、uniqueString を使用して、一般的に使用されている別のレベル用に一意の値を作成する方法を示しています。

サブスクリプションに基づいて一意

    "[uniqueString(subscription().subscriptionId)]"

リソース グループに基づいて一意

    "[uniqueString(resourceGroup().id)]"

リソース グループのデプロイメントに基づいて一意

    "[uniqueString(resourceGroup().id, deployment().name)]"
    
次の例は、リソース グループに基づいてストレージ アカウントの一意の名前を作成する方法を示しています。

    "resources": [{ 
        "name": "[concat('ContosoStorage', uniqueString(resourceGroup().id))]", 
        "type": "Microsoft.Storage/storageAccounts", 
        ...

<a id="uri" />
### uri

**uri (baseUri, relativeUri)**

baseUri と relativeUri の文字列を組み合わせることにより、絶対 URI を作成します。

| パラメーター                          | 必須 | 説明
| :--------------------------------: | :------: | :----------
| baseUri                            |   あり    | ベース URI 文字列。
| relativeUri                        |   あり    | ベース URI 文字列に追加する相対 URI 文字列。

値、 **baseUri** パラメーターは、特定のファイルを含めることができますが、URI の作成時のベース パスのみが使用されます。 たとえば、渡す **http://contoso.com/resources/azuredeploy.json** baseUri としてパラメーターがのベース URI **http://contoso.com/resources/**します。

次の例は、親テンプレートの値に基づいて、入れ子になったテンプレートへのリンクを作成する方法を示しています。

    "templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"



## デプロイの値式

リソース マネージャーは、テンプレートのセクションから値を取得し、デプロイに関連する値を取得するために次の式を提供します。

- [デプロイ](#deployment)
- [parameters](#parameters)
- [variables](#variables)

値を取得するリソース、リソース グループ、またはサブスクリプションから、次を参照してください。 [リソース式](#resource-expressions)します。

<a id="deployment" />
### デプロイ

**デプロイ()**

現在のデプロイ操作に関する情報を返します。

この式は、デプロイ中に渡されたオブジェクトを返します。 返されるオブジェクトのプロパティは、デプロイ オブジェクトがリンクとして渡されたか、インライン オブジェクトとして渡されたかによって異なります。 展開オブジェクトが渡された場合、インラインなどを使用する場合、 **- TemplateFile** ローカル ファイルをポイントする Azure PowerShell のパラメーターは、返されたオブジェクトは、次の形式で。

    {
        "name": "",
        "properties": {
            "template": {
                "$schema": "",
                "contentVersion": "",
                "resources": [
                ],
                "outputs": {}
            },
            "parameters": {},
            "mode": "",
            "provisioningState": ""
        }
    }

経過すると、オブジェクトがリンクとしてなどを使用する場合、 **- TemplateUri** 、リモート オブジェクトを指すパラメーター オブジェクトは、次の形式で返されます。 

    {
        "name": "",
        "properties": {
            "templateLink": {
                "uri": "",
                "contentVersion": ""
            },
            "mode": "",
            "provisioningState": ""
        }
    }

次の例は、deployment() を使用し、親テンプレートの URI に基づいて別のテンプレートにリンクする方法を示しています。

    "variables": {  
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
    }  


<a id="parameters" />
### parameters

**parameters (parameterName)**

パラメーター値を返します。 指定したパラメーター名は、テンプレートのパラメーター セクションで定義されている必要があります。

| パラメーター                          | 必須 | 説明
| :--------------------------------: | :------: | :----------
| parameterName                      |   あり    | 返されるパラメーターの名前。

次の例では、parameters 関数の簡単な使用方法を示しています。

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
### variables

**variables (variableName)**

変数の値を返します。 指定した変数名は、テンプレートの変数セクションで定義されている必要があります。

| パラメーター                          | 必須 | 説明
| :--------------------------------: | :------: | :----------
| variable Name                      |   あり    | 返す変数の名前。



## リソース式

リソース マネージャーは、リソース式を取得する次の式を提供します。

- [listkeys](#listkeys)
- [プロバイダー](#providers)
- [reference](#reference)
- [resourceGroup](#resourcegroup)
- [resourceId](#resourceid)
- [subscription](#subscription)

値を取得するパラメーター、変数、または現在の展開から、次を参照してください。 [展開値式](#deployment-value-expressions)します。

<a id="listkeys" />
### listKeys

**listKeys (resourceName or resourceIdentifier, [apiVersion])**

ストレージ アカウントのキーを返します。 使用して、リソース Id を指定することができます、 [resourceId 関数](./#resourceid) または形式を使用して **providerNamespace/リソースの種類/resourceName**します。 関数を使用して、PrimaryKey と secondaryKey を取得できます。
  
| パラメーター                          | 必須 | 説明
| :--------------------------------: | :------: | :----------
| resourceName または resourceIdentifier |   あり    | ストレージ アカウントの一意の識別子。
| apiVersion                         |   あり    | リソースのランタイム状態の API バージョン。

次の例は、出力のセクションで、ストレージ アカウントからキーを返す方法を示しています。

    "outputs": { 
      "exampleOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2015-05-01-preview')]", 
        "type" : "object" 
      } 
    } 

<a id="providers" />
### プロバイダー

**providers (providerNamespace, [resourceType])**

リソース プロバイダーとサポートされているそのリソースの種類に関する情報を返します。 種類が指定されない場合は、サポートされているすべての種類が返されます。

| パラメーター                          | 必須 | 説明
| :--------------------------------: | :------: | :----------
| providerNamespace                  |   あり    | プロバイダーの名前空間
| resourceType                       |   いいえ     | 指定した名前空間内にあるリソースの種類。

サポートされている各種類は、次の形式で返されます。

    {
        "resourceType": "",
        "locations": [ ],
        "apiVersions": [ ]
    }

次の例は、provider 関数の使用方法を示しています。

    "outputs": {
        "exampleOutput": {
            "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
            "type" : "object"
        }
    }

<a id="reference" />
### reference

**reference (resourceName or resourceIdentifier, [apiVersion])**

式が別のリソースのランタイム状態から値を取得できるようにします。

| パラメーター                          | 必須 | 説明
| :--------------------------------: | :------: | :----------
| resourceName または resourceIdentifier |   あり    | 名前またはリソースの一意の識別子。
| apiVersion                         |   いいえ     | 指定したリソースの API バージョンです。 同じテンプレート内でリソースがプロビジョニングされないとき、このパラメーターを追加する必要があります。

 **参照** 関数はその値をランタイム状態から取得し、変数セクションで使用することはできません。 これは、テンプレートの出力セクションで使用できます。

参照式を使用して、参照先のリソースが同じテンプレート内でプロビジョニングされる場合に、1 つのリソースが他のリソースに依存していることを暗黙的に宣言します。 使用してもする必要はありません、 **dependsOn** プロパティです。 
参照先のリソースがデプロイを完了するまでは、式は評価されません。

次の例では、同じテンプレートでデプロイされるストレージ アカウントが参照されます。

    "outputs": {
        "NewStorage": {
            "value": "[reference(parameters('storageAccountName'))]",
            "type" : "object"
        }
    }

次の例では、このテンプレートでデプロイされないが、デプロイされるリソースと同じリソース グループ内に存在するストレージ アカウントが参照されます。

    "outputs": {
        "ExistingStorage": {
            "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15')]",
            "type" : "object"
        }
    }

下の図のように、BLOB エンドポイント URI など、返されたオブジェクトから特定の値を取得できます。

    "outputs": {
        "BlobUri": {
            "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15').primaryEndpoints.blob]",
            "type" : "string"
        }
    }

使用する場合は、テンプレートに API バージョンを直接指定する場合は、 **プロバイダー** 式と、1 つに、次のように、最新のバージョンなどの値を取得します。

    "outputs": {
        "BlobUri": {
            "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).primaryEndpoints.blob]",
            "type" : "string"
        }
    }

次の例では、別のリソース グループにあるストレージ アカウントが参照されます。

    "outputs": {
        "BlobUri": {
            "value": "[reference(resourceId(parameters('relatedGroup'), 'Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15').primaryEndpoints.blob]",
            "type" : "string"
        }
    }

<a id="resourcegroup" />
### resourceGroup

**resourceGroup()**

現在のリソース グループを表す、構造化オブジェクトを返します。 オブジェクトは次の形式になります。

    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
      "name": "{resourceGroupName}",
      "location": "{resourceGroupLocation}",
    }

次の例では、リソース グループの場所を使用して、Web サイトの場所を割り当てます。

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

**resourceId ([resourceGroupName], resourceType, resourceName1, [resourceName2]...)**

リソースの一意の識別子を返します。 リソース名があいまいであるか、同じテンプレート内でプロビジョニングされていないときに、この関数を使用します。 識別子は、次の形式で返されます。

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}
      
| パラメーター         | 必須 | 説明
| :---------------: | :------: | :----------
| resourceGroupName |   いいえ     | 省略可能なリソース グループの名前。 既定値は、現在のリソース グループです。 別のリソース グループ内のリソースを取得するときには、この値を指定します。
| resourceType      |   あり    | リソース プロバイダーの名前空間を含むリソースの種類。
| resourceName1     |   あり    | リソースの名前。
| resourceName2     |   いいえ     | リソースが入れ子になっている場合、次のリソース名セグメント。

次の例では、Web サイトとデータベースのリソース ID を取得する方法を示します。 という名前のリソース グループに、web サイトが存在する **myWebsitesGroup** とこのテンプレートの現在のリソース グループにデータベースが存在します。

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'),parameters('databaseName'))]
    
代替のリソース グループで、ストレージ アカウントや仮想ネットワークを使用するときには、多くの場合にこの関数を使用する必要があります。 ストレージ アカウントや仮想ネットワークは、複数のリソース グループ間で使用される場合があるので、単一のリソース グループを削除するときにそれらを削除しないでください。 次の例は、外部のリソース グループのリソースを簡単に使用する方法を示しています。

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
### subscription

**subscription()**

サブスクリプションの詳細を次の形式で返します。

    {
        "id": "/subscriptions/#####"
        "subscriptionId": "#####"
    }

次の例は、出力セクションで呼び出される  subscription 関数を示しています。 

    "outputs": { 
      "exampleOutput": { 
          "value": "[subscription()]", 
          "type" : "object" 
      } 
    } 


## 次のステップ
- Azure リソース マネージャー テンプレート内のセクションの説明は、次を参照してください [Azure リソース マネージャーの作成テンプレート。](resource-group-authoring-templates.md)
- 複数のテンプレートをマージするを参照してください [Azure リソース マネージャーでリンクされたテンプレートを使用。](resource-group-linked-templates.md)
- 指定した回数だけ反復処理するリソースの種類を作成する場合は、次を参照してください [Azure リソース マネージャーでリソースの複数のインスタンスを作成します。。](resource-group-create-multiple.md)
- 作成したテンプレートをデプロイする方法を確認するには、次を参照してください [Azure リソース マネージャー テンプレートを使用してアプリケーションを配置。](resource-group-template-deploy.md)


