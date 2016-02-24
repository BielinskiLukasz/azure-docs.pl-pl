<properties
   pageTitle="WAD と Operational Insights でログを収集する方法 | Microsoft Azure"
   description="この記事では、Azure で実行されている Service Fabric クラスターからログを収集するには、Windows Azure 診断とオペレーション インサイトをセットアップする方法について説明します"
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/20/2015"
   ms.author="kunalds"/>


# WAD (Windows Azure 診断) とオペレーション インサイトを使用して azure Service Fabric クラスターからのログの収集

Azure で Service Fabric クラスターを実行するときに、すべてのノードのログを 1 か所に収集する場合があります。 1 か所にログを収集すると、クラスターと、そのクラスターで実行されているアプリケーションやサービスで発生する問題の分析と解決が簡単になります。 ログを Azure テーブル ストレージにアップロードする WAD (Windows Azure 診断) の拡張機能を使用してアップロードし、ログを収集する方法の 1 つです。 Operational Insights (Microsoft Operations Management Suite の一部) は、ログの分析と検索が簡単になる SaaS ソリューションです。 以下の手順では、クラスター内の VM で WAD を設定してログを中央のストアにアップロードし、ログを取得するように Operational Insights を構成して、Operational Insights ポータルで確認する方法について説明します。 Operational Insights は、Service Fabric クラスターからアップロードされたさまざまなログのソースを格納されている Azure ストレージ テーブル名ごとに識別します。そのため、その Operational Insights が検索する名前を持つ Azure ストレージ テーブルにログをアップロードするように WAD を構成する必要があります。このドキュメントの構成設定例では、ストレージ テーブル名の例も示します。

## 推奨される記事
* [Windows Azure 診断](../cloud-services/cloud-services-dotnet-diagnostics.md) (クラウド サービスは、関連するが、いくつかの有益な情報と例については、)
* [Operational Insights](https://azure.microsoft.com/services/operational-insights/)
* [Azure リソース マネージャー](https://azure.microsoft.com/documentation/articles/resource-group-overview/)

## 前提条件
これらのツールが、このドキュメントの一部の操作の実行に使用されます。
* [Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* [ARM クライアント](https://github.com/projectkudu/ARMClient)

## 収集することができるさまざまなログ ソース
1. Service Fabric ログ: プラットフォームから標準の ETW と EventSource チャネルに対して生成されます。 次の種類があります。
  - 操作イベント: Service Fabric プラットフォームで実行される操作のログです。 たとえば、アプリケーションとサービスの作成、ノードの状態変化、アップグレード情報などです。
  - [Actor プログラミング モデル イベント](https://azure.microsoft.com/documentation/articles/service-fabric-reliable-actors-diagnostics/)
  - [Reliable Services プログラミング モデル イベント](https://azure.microsoft.com/documentation/articles/service-fabric-reliable-services-diagnostics/)
2. アプリケーション イベント: サービス コードから生成され、Visual Studio テンプレートで指定された EventSource ヘルパー クラスを使用して出力されるイベントです。 作成する方法に関する詳細については、アプリケーションからのログを参照してくださいこの [記事](https://azure.microsoft.com/documentation/articles/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/)します。


## WAD (Windows Azure 診断) を収集し、ログをアップロードする Service Fabric クラスターに展開します。
ログ収集の最初の手順は、Service Fabric クラスター内の各 VM に WAD 拡張機能をデプロイすることです。 WAD によって各 VM 上のログが収集され、指定したストレージ アカウントにアップロードされます。 ポータルを使用するか ARM を使用するかによって、またクラスター作成の一環としてのデプロイか、既に存在するクラスターの場合かによって、一部の手順が変わります。 各シナリオの手順を見てみましょう。

### ポータルを使用してクラスター作成の一環で WAD をデプロイする
クラスター作成の一環でクラスター内の VM に WAD をデプロイするには、下図の [診断設定] を使用します。 これは、既定では ON です。
![ポータルのクラスター作成の WAD 設定](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/portal-cluster-creation-diagnostics-setting.png)

### ARM を使用してクラスター作成の一環で WAD をデプロイする
ARM を使用してクラスターを作成するには、WAD 構成 JSON を Full クラスター ARM テンプレートに追加してから、クラスターを作成する必要があります。 WAD の構成は、ARM テンプレートのサンプルの一部として追加されるとサンプル 5 VM クラスター ARM テンプレートを提供すること、Azure のサンプル ギャラリーのこの場所で確認できます。 [WAD ARM テンプレートのサンプルの 5 つのノード クラスター](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-1-nodetype-wad)します。 

ARM テンプレートの WAD 設定を確認するには、"WadCfg" を検索します。 このテンプレートを使用してクラスターを作成するには、上のリンクにある [Azure にデプロイ] ボタンをクリックしてください。
または、ARM サンプルをダウンロードし、変更を加え、Azure PowerShell ウィンドウで `New-AzureResourceGroupDeployment` コマンドを使用して、変更したテンプレートでクラスターを作成する方法もあります。 コマンドで渡す必要があるパラメーターについては、後述します。 また、このデプロイメント コマンドを呼び出す前に、必要に応じて、Azure アカウントの追加 (`Add-AzureAccount`)、サブスクリプションの選択 (`Select-AzureSubscription`)、ARM モードへの切り替え (`Switch-AzureMode AzureResourceManager`)、まだリソース グループがない場合には作成 (`New-AzureResourceGroup`) などの設定を実行します。

```powershell
New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploywadarm"></a>WAD を既存のクラスターに展開します。
WAD がデプロイされていない既存のクラスターがある場合は、次の手順で WAD を追加できます。
次の JSON で WadConfigUpdate.json と WadConfigUpdateParams.json という 2 つのファイルを作成します。

##### WadConfigUpdate.json
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",

  "parameters": {
        "vmNamePrefix": {
      "type": "string"
    },
        "applicationDiagnosticsStorageAccountName": {
      "type": "string"
    },
        "vmCount": {
            "type": "int"
    }
  },

  "resources": [
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(parameters('vmNamePrefix'),copyIndex(0),'/Microsoft.Insights.VMDiagnosticsSettings')]",
            "location": "[resourceGroup().location]",
      "properties": {
        "type": "IaaSDiagnostics",
                "typeHandlerVersion": "1.5",
        "publisher": "Microsoft.Azure.Diagnostics",
                "autoUpgradeMinorVersion": true,
        "settings": {
                    "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
                "EtwProviders": {
                    "EtwEventSourceProviderConfiguration": [
                        {
                            "provider": "Microsoft-ServiceFabric-Actors",
                            "scheduledTransferKeywordFilter": "1",
                                        "scheduledTransferPeriod": "PT5M",
                            "DefaultEvents": { "eventDestination": "ServiceFabricReliableActorEventTable" }
                        },
                        {
                            "provider": "Microsoft-ServiceFabric-Services",
                            "DefaultEvents": { "eventDestination": "ServiceFabricReliableServiceEventTable" },
                                        "scheduledTransferPeriod": "PT5M"
                        }
                    ],
                    "EtwManifestProviderConfiguration": [
                        {
                            "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                            "scheduledTransferKeywordFilter": "4611686018427387904",
                                        "scheduledTransferLogLevelFilter": "Information",
                                        "scheduledTransferPeriod": "PT5M",
                            "DefaultEvents": { "eventDestination": "ServiceFabricSystemEventTable" }
                        }
                    ]
                }
            }
    },
                    "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountNamee')]"
                },
                "protectedSettings": {
                    "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountNamee')]",
                    "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                    "storageAccountEndPoint": "https://core.windows.net/"
                }
            },
            "copy": {
                "name": "vmExtensionLoop",
                "count": "[parameters('vmCount')]"
            }
        }
    ],

    "outputs": {
    }
}
```

##### WadConfigUpdateParams.json
クラスターの作成時に、vmNamePrefix を VM 名に選択したプロファイルに置き換えます。また、vmStorageAccountName を編集して、VM のログをアップロードするストレージ アカウントに変更します。
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmNamePrefix": {
            "value": "VM"
        },
        "applicationDiagnosticsStorageAccountName": {
            "value": "testdiagacc"
        },
        "vmCount": {
            "value": 5
        }
    }
}
```
Json の作成後は、上記としてファイルし、変更して、環境の詳細について、Service Fabric クラスターのリソース グループの名前を渡して、このコマンドを呼び出します。 このコマンドの実行に成功すると、WAD はすべての VM にデプロイされ、指定した Azure ストレージ アカウントのテーブルに、クラスターのログがアップロードされるようになります。 Azure アカウントを追加するなどのセットアップを行う必要がありますこの配置コマンドを呼び出す前にさらに、(`Add-AzureAccount`)、適切なサブスクリプションを選択する (`Select-AzureSubscription`) と ARM モードに切り替え (`Switch-AzureMode AzureResourceManager`)。
```powershell
New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToWADConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

## クラスター ログの表示と検索のために Operational Insights を設定する
クラスターに WAD が設定され、ログがストレージ アカウントにアップロードされたら、次の手順は、Operational Insights のポータル UI ですべてのクラスター ログの表示、検索、およびクエリを行うように Operational Insights を設定することです。

### Operational Insights ワークスペースを作成する
Operational Insights ワークスペースの作成手順については、以下の記事を参照してください。 この記事では、Azure ポータルとサブスクリプション ベースのアプローチという、ワークスペースを作成する 2 種類の方法が説明されています。 このドキュメントの以降のセクションでは、Operational Insights ワークスペース名が必要です。 ストレージ アカウントなど、すべてのクラスター リソースの作成に使用したものと同じサブスクリプションを使用して、Operational Insights ワークスペースを作成します。

[Operational Insights の運用](https://technet.microsoft.com/library/mt484118.aspx)

### クラスター ログを表示するように Operational Insights ワークスペースを構成する
前述のように Operational Insights ワークスペースを作成したら、次に、WAD によってクラスターからアップロードされる Azure テーブルのログを取得するようにワークスペースを構成します。 現在、この構成は、Operational Insights ポータルでは使用できません。PowerShell コマンドを使用する必要があります。 この PS スクリプトを実行します。
```powershell
<#
    This script will configure an Operations Management Suite workspace (aka Operational Insights workspace) to read
    Windows Azure Diagnostics from an Azure storage account.

    It will enable all supported data types (currently Windows Event Logs, Syslog, Service Fabric Events, ETW Events and IIS Logs).

    It supports both classic and ARM storage accounts.

    If you have more than one OMS workspace you will be prompted for the workspace to configure.

    If you have more than one storage account you will be prompted for which storage account to configure.
#>
Add-AzureAccount

Switch-AzureMode -Name AzureResourceManager

$validTables = "WADServiceFabric*EventTable", "WADETWEventTable"

function Select-Workspace {

    $workspace = ""

    $allWorkspaces = Get-AzureOperationalInsightsWorkspace  

    switch ($allWorkspaces.Count) {
        0 {Write-Error "No Operations Management Suite workspaces found"}
        1 {return $allWorkspaces}
        default {
            $uiPrompt = "Enter the number corresponding to the workspace you want to configure.`n"

            $count = 1
            foreach ($workspace in $allWorkspaces) {
                $uiPrompt += "$count. " + $workspace.Name + " (" + $workspace.CustomerId + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt) - 1
            $workspace = $allWorkspaces[$answer]
        }  
    }
    return $workspace
}

function Select-StorageAccount {

    $storage = ""

    $allStorageAccounts = (get-azureresource -ResourceType Microsoft.ClassicStorage/storageAccounts) + (get-azureresource -ResourceType Microsoft.Storage/storageAccounts)

    switch ($allStorageAccounts.Count) {
        0 {Write-Error "No storage accounts found"}
        1 {$storage = $allStorageAccounts}
        default {

            $uiPrompt = "Enter the number corresponding to the storage account with diagnostics.`n"

            $count = 1
            foreach ($storageAccount in $allStorageAccounts) {
                $uiPrompt += "$count. " + $storageAccount.Name + " (" + $storageAccount.Location + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt)

            $storage = $allStorageAccounts[$answer - 1]
        }
    }
    return $storage
}

$workspace = Select-Workspace
$storageAccount = Select-StorageAccount

$insightsName = $storageAccount.Name + $workspace.Name

$existingConfig = ""

try
{
    $existingConfig = Get-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -ErrorAction Stop
}
catch [Hyak.Common.CloudException]
{
    # HTTP Not Found is returned if the storage insight doesn't exist
}

if ($existingConfig) {
    Set-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -Tables $validTables -Containers $validContainers

} else {
    if ($storageAccount.ResourceType -eq "Microsoft.ClassicStorage/storageAccounts") {
        Switch-AzureMode -Name AzureServiceManagement
        $key = (Get-AzureStorageKey -StorageAccountName $storageAccount.Name).Primary
        Switch-AzureMode -Name AzureResourceManager
    } else {
        $key = (Get-AzureStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.Name).Key1
    }
    New-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -StorageAccountResourceId $storageAccount.ResourceId -StorageAccountKey $key -Tables $validTables -Containers $validContainers
}
```
ストレージ アカウントで Azure テーブルから読み取るオペレーション インサイト ワークスペースを構成した後は、Azure ポータルにログインし、Operational Insights のリソースの [ストレージ] タブを検索します。 次のように表示されます。
![Azure ポータルの Operational Insights ストレージの構成](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/oi-connected-tables-list.png)

### Operational Insights のログの検索と表示
指定したストレージ アカウントからログを読み取るように Operational Insights ワークスペースを構成してから、Operational Insights UI にログが表示されるまでに、最大で 10 分かかることがあります。 新しいログが生成されていることを確認するには、Service Fabric プラットフォームから運用イベントが生成されるように、Service Fabric アプリケーションをクラスターにデプロイする必要があります。

1. ログを表示するには、オペレーション インサイト ポータルのメイン ページで、LogSearch を選択します。
![Operational Insights の [ログの検索] オプション](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/log-search-button-oi.png)

2. ログの検索ページで、"Type=ServiceFabricOperationalEvent" というクエリを使用します。次のようにクラスターの運用ログが表示されます。 プログラミング モデルのログを照会する必要があるすべてのアクターを表示する"の種類 = ServiceFabricReliableActorEvent"Reliable Services プログラミング モデルの種類からのすべてのログを参照して"の種類 = ServiceFabricReliableServiceEvent"
![Operational Insights のログのクエリと表示](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/view-logs-oi.png)

### 問題解決に役立つサンプル クエリ
ここでは、いくつかのシナリオと、そのトラブルシューティングに使用できるクエリの例を紹介します。

1. Service Fabric から特定のサービスに対して RunAsync が呼び出されたかどうかを確認する場合。 この場合、サービスの起動時にクラッシュが発生した場合を除外する必要があるときに、この手順を実行します。 この検索を実行するには、次のようなクエリを使用して、サービスとアプリケーションの名前を実際にデプロイした名前に置き換え、結果が返されるかどうかを確認します。

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateless1" AND "RunAsync has been invoked"
    ```

2. ステートフル サービスを実行中で、そのサービスから Service Fabric によるエラーとマークされた例外がスローされたかどうかを確認するには、次のようなクエリでイベントを検索します。

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateful1" AND TaskName=StatefulRunAsyncFailure
    ```

3. すべてのデプロイされたアプリケーションとサービスで Actor メソッドからスローされた例外に対応するイベントを検索するには、次のようなクエリを使用できます。

    ```
    Type=ServiceFabricReliableActorEvent AND TaskName=ActorMethodThrewException
    ```

## 新しい EventSource チャネルのログを収集およびアップロードするように WAD を更新する
WAD ログを収集する新しい EventSource からを更新するだけに展開するしようとしている新しいアプリケーションを表すチャネルに必要と同じ手順を実行する、 [前のセクション](#deploywadarm) 既存のクラスターの WAD のセットアップを記述します。 WadConfigUpdate.json の EtwEventSourceProviderConfiguration セクションを更新して、新しい EventSource のエントリを追加してから、ARM コマンドで構成の更新を適用する必要があります。 アップロードのテーブルは、アプリケーションの ETW イベントを読み取るように Operational Insights 用に構成されたテーブルなので、同じ (ETWEventTable) です。


## 次のステップ
チェック アウトの出力される診断イベント [高信頼アクター](service-fabric-reliable-actors-diagnostics.md) と [信頼性の高いサービス](service-fabric-reliable-services-diagnostics.md) の問題をトラブルシューティングするときにどのようなイベントをさらに詳しく理解するを調べてください。

