<properties
   pageTitle="Publish-WebApplicationVM | Microsoft Azure"
   description="仮想マシンに Web アプリケーションをデプロイする方法を学習します。 このスクリプトは、必要なリソースが Azure サブスクリプションに存在しない場合にそれらを作成します。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/30/2015"
   ms.author="tarcher" />

# Publish-WebApplicationVM (Windows PowerShell スクリプト)

仮想マシンに Web アプリケーションをデプロイします。 このスクリプトは、必要なリソースが Azure サブスクリプションに存在しない場合にそれらを作成します。

```
Publish-WebApplicationVM
–Configuration <configuration>
-SubscriptionName <subscriptionName>
-WebDeployPackage <packageName>
-VMPassword @{Name = "name"; Password = "password")
-DatabaseServerPassword @{Name = "name"; Password = "password"}
-SendHostMessagesToOutput
-Verbose
```

### 構成

デプロイの詳細が記述されている JSON 構成ファイルへのパス。

|別名|なし|
|---|---|
|必須|true|
|位置|named|
|既定値|なし|
|パイプライン入力を許可する|false|
|ワイルドカード文字を許可する|false|

### SubscriptionName

仮想マシンを作成する Azure サブスクリプションの名前。

|別名|なし|
|---|---|
|必須|false|
|位置|named|
|既定値|サブスクリプション ファイルで最初のサブスクリプションを使用する|
|パイプライン入力を許可する|false|
|ワイルドカード文字を許可する|false|

### WebDeployPackage

仮想マシンに発行する Web デプロイ パッケージへのパス。 Visual Studio で Web の発行ウィザードを使用して、このパッケージを作成できます。 参照してください [方法: Visual Studio で Web 配置パッケージを作成](https://msdn.microsoft.com/library/dd465323.aspx)します。

|別名|なし|
|---|---|
|必須|false|
|位置|named|
|既定値|なし|
|パイプライン入力を許可する|false|
|ワイルドカード文字を許可する|false|

### AllowUntrusted

True の場合は、信頼されたルート証明機関によって署名されていない証明書の使用が許可されます。

|別名|なし|
|---|---|
|必須|false|
|位置|named|
|既定値|false|
|パイプライン入力を許可する|false|
|ワイルドカード文字を許可する|false|

### VMPassword

仮想マシンアカウントの資格情報。 例: -VMPassword @{Name = "admin"; Password = "password"}

|別名|なし|
|---|---|
|必須|false|
|位置|named|
|既定値|なし|
|パイプライン入力を許可する|false|
|ワイルドカード文字を許可する|false|

### DatabaseServerPassword

Azure での SQL Database の資格情報。 例: -DatabaseServerPassword @{Name = "admin"; Password = "password"}

|別名|なし|
|---|---|
|必須|false|
|位置|named|
|既定値|なし|
|パイプライン入力を許可する|false|
|ワイルドカード文字を許可する|false|

### SendHostMessagesToOutput

true の場合、スクリプトからのメッセージは出力ストリームに出力されます。

|別名|なし|
|---|---|
|必須|false|
|位置|named|
|既定値|false|
|パイプライン入力を許可する|false|
|ワイルドカード文字を許可する|false|

## 解説

スクリプトを使用して作成する方法の詳細な説明について開発およびテスト環境を参照してください [開発環境およびテスト環境に発行する Windows PowerShell スクリプトを使用して](vs-azure-tools-publishing-using-powershell-scripts.md)します。

JSON 構成ファイルではデプロイ対象の詳細が指定されます。 これには、仮想マシンの名前、アフィニティ グループ、VHD イメージ、およびサイズなど、プロジェクトを作成したときに指定した情報が含まれています。 また、仮想マシン上のエンドポイント、プロビジョニングするデータベース (該当する場合)、Web デプロイメント パラメーターも含まれています。 次のコードは JSON 構成ファイルの例を示しています。

```
{
    "environmentSettings": {
        "cloudService": {
            "name": "myvmname",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myvmname",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [
                    {
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [
            {
                "connectionStringName": "",
                "databaseName": "",
                "serverName": "",
                "user": "",
                "password": ""
            }
        ],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

プロビジョニング対象が変更されるように JSON 構成ファイルを編集できます。 仮想マシンとクラウド サービスは必須ですが、データベースのセクションは省略可能です。

