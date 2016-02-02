<properties
   pageTitle="リソース マネージャーのテンプレートを使用してリソースをデプロイする |Microsoft Azure"
   services="azure-resource-manager"
   description="Azure リソース マネージャーを使用してリソースを Azure にデプロイします。テンプレートは JSON ファイルであり、ポータル、PowerShell、Azure コマンドライン インターフェイス (Mac、Linux、Windows 用)、または REST で使用できます。"
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
   ms.date="12/08/2015"
   ms.author="tomfitz"/>


# Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ

このトピックでは、Azure リソース マネージャーのテンプレートを使用して Azure にアプリケーションをデプロイする方法について説明します。 また、Azure PowerShell、Azure CLI、REST API、または Azure ポータルを使用して、アプリケーションをデプロイする方法も示します。

リソース マネージャーの概要を参照してください。 [Azure リソース マネージャーの概要](../resource-group-overview.md)します。 テンプレートの作成方法については、次を参照してください。 [Azure リソース マネージャーの作成テンプレート](resource-group-authoring-templates.md)します。

テンプレートを使用してアプリケーションをデプロイする場合は、パラメーター値を指定することで、リソースの作成方法をカスタマイズすることができます。 これらのパラメーターの値は、インラインか、パラメーター ファイルのどちらかで指定します。

## 増分デプロイと完全デプロイ

既定では、リソース マネージャーはデプロイメントをリソース グループへの増分更新として処理します。 増分デプロイメントでは、リソース マネージャーは次の処理を行います。

- リソース グループに存在するが、テンプレートに指定されていないリソースを**変更せず、そのまま残します**
- テンプレートに指定されているが、リソース グループに存在しないリソースを**追加します**
- テンプレートに定義されている同じ条件でリソース グループに存在するリソースを**再プロビジョニングしません**

Azure PowerShell または REST API を利用し、リソース グループへの完全更新を指定できます。 現在のところ、Azure CLI は完全デプロイメントに対応していません。 完全デプロイメントでは、リソース マネージャーは次の処理を行います。

- リソース グループに存在するが、テンプレートに指定されていないリソースを**削除します**
- テンプレートに指定されているが、リソース グループに存在しないリソースを**追加します**
- テンプレートに定義されている同じ条件でリソース グループに存在するリソースを**再プロビジョニングしません**

**[モード]** プロパティでデプロイメントの種類を指定します。

## PowerShell でデプロイする

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]


1. Azure アカウントにログインします。 資格情報を提供すると、コマンドがアカウントの情報を返します。

 Azure PowerShell 1.0:

      PS C:\> Login-AzureRmAccount
    
      Evironment : AzureCloud
      Account    : someone@example.com
      ...

2. 複数のサブスクリプションがある場合、**Select-AzureRmSubscription** コマンドでデプロイに使用するサブスクリプション ID を提供します。

        PS C:\> Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

3. 既存のリソース グループがない場合は、**New-AzureRmResourceGroup** コマンドで新しいリソース グループを作成します。 ソリューションに必要なリソース グループと場所の名前を指定します。 新しいリソース グループの概要が返されます。

     PS C:\> New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
    
     ResourceGroupName : ExampleResourceGroup
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     Permissions       :
                 Actions  NotActions
                 =======  ==========
                 *
     ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. リソース グループに新しいデプロイを作成するには、**New-AzureRmResourceGroupDeployment** コマンドを実行して必要なパラメーターを指定します。 パラメーターにはデプロイの名前、リソース グループの名前、作成したテンプレートへのパスや URL、シナリオに必要なその他のパラメーターが含まれます。 **[モード]** パラメーターは指定されません。つまり、既定値の **[増分]** が使用されます。

     次のオプションを使用してパラメーターの値を提供できます。

     - インライン パラメーターを使用する

            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

     - パラメーター オブジェクトを使用する

            PS C:\> $parameters = @{"<ParameterName>"="<Parameter Value>"}
            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

     - パラメーター ファイルの使用 テンプレート ファイルについては、次を参照してください。 [パラメーター ファイル](./#parameter-file)します。

            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

     リソース グループをデプロイすると、デプロイの概要が表示されます。

          DeploymentName    : ExampleDeployment
          ResourceGroupName : ExampleResourceGroup
          ProvisioningState : Succeeded
          Timestamp         : 4/14/2015 7:00:27 PM
          Mode              : Incremental
          ...

     完全デプロイメントを実行するには、**[モード]** を **[完全]** に設定します。

          PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -Mode Complete
          Confirm
          Are you sure you want to use the complete deployment mode? Resources in the resource group 'ExampleResourceGroup' which are not
          included in the template will be deleted.
          [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


6. デプロイ エラーに関する情報を取得するには

        PS C:\> Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -Name ExampleDeployment



### ビデオ

このビデオでは、PowerShell を使用したリソース マネージャーのテンプレートの利用方法を説明しています。

[AZURE.VIDEO deploy-an-application-with-azure-resource-manager-template]


## Mac、Linux、および Windows 用の Azure CLI でデプロイする

使用したがない Azure CLI リソース マネージャーで、表示 [Mac、Linux、および Azure リソース管理で Windows 用 Azure CLI の使用](xplat-cli-azure-resource-manager.md)します。

1. Azure アカウントにログインします。 資格情報を提供すると、コマンドがログインの結果を返します。

     azure login
    
     ...
     info:    login command OK

2. 複数のサブスクリプションがある場合、デプロイに使用するサブスクリプション ID を提供します。

        azure account set <YourSubscriptionNameOrId>

3. Azure リソース マネージャー モジュールに切り替えます。 新しいモードの確認が表示されます。

     azure config mode arm
    
     info:     New mode is arm

4. 既存のリソース グループがない場合は、新しいリソース グループを作成します。 ソリューションに必要なリソース グループと場所の名前を指定します。 新しいリソース グループの概要が返されます。

     azure group create -n ExampleResourceGroup -l "West US"
    
     info:    Executing command group create
     + Getting resource group ExampleResourceGroup
     + Creating resource group ExampleResourceGroup
     info:    Created resource group ExampleResourceGroup
     data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
     data:    Name:                ExampleResourceGroup
     data:    Location:            westus
     data:    Provisioning State:  Succeeded
     data:    Tags:
     data:
     info:    group create command OK

5. リソース グループに新しいデプロイを作成するには、次のコマンドを実行して必要なパラメーターを指定します。 パラメーターにはデプロイの名前、リソース グループの名前、作成したテンプレートへのパスや URL、シナリオに必要なその他のパラメーターが含まれます。

     次のオプションを使用してパラメーターの値を提供できます。

     - インライン パラメーターとローカルのテンプレートを使用します。 各パラメーターの形式が: `"ParameterName": {"value":"ParameterValue"}`します。 次の例では、エスケープ文字を含むパラメーターを示します。

             azure group deployment create -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment

     - インライン パラメーターとローカルのテンプレートへのリンクを使用します。

             azure group deployment create --template-uri <LinkToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment

     - パラメーター ファイルを使用します。 テンプレート ファイルについては、次を参照してください。 [パラメーター ファイル](./#parameter-file)します。

             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     リソース グループをデプロイすると、デプロイの概要が表示されます。

           info:    Executing command group deployment create
           + Initializing template configurations and parameters
           + Creating a deployment
           ...
           info:    group deployment create command OK


6. 最新のデプロイに関する情報を取得するには

         azure group log show -l ExampleResourceGroup

7. デプロイ エラーに関する詳細情報を取得するには

         azure group log show -l -v ExampleResourceGroup


## REST API でデプロイする

1. 設定 [共通のパラメーターとヘッダー](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common), 、認証トークンを含みます。
2. 既存のリソース グループがない場合は、新しいリソース グループを作成します。 ソリューションに必要なサブスクリプション ID、新しいリソース グループの名前、および場所を指定します。 詳細については、次を参照してください。 [リソース グループを作成](https://msdn.microsoft.com/library/azure/dn790525.aspx)します。

         PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
           <common headers>
           {
             "location": "West US",
             "tags": {
               "tagname1": "tagvalue1"
             }
           }

3. リソース グループの新しいデプロイを作成します。 サブスクリプション ID、デプロイするリソース グループの名前、デプロイの名前、およびテンプレートの場所を指定します。 テンプレート ファイルについては、次を参照してください。 [パラメーター ファイル](./#parameter-file)します。 リソース グループを作成する REST API の詳細については、次を参照してください。 [テンプレート デプロイの作成](https://msdn.microsoft.com/library/azure/dn790564.aspx)します。 完全デプロイメントを実行するには、**[モード]** を **[完全]** に設定します。

         PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
            <common headers>
            {
              "properties": {
                "templateLink": {
                  "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                  "contentVersion": "1.0.0.0",
                },
                "mode": "Incremental",
                "parametersLink": {
                  "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                  "contentVersion": "1.0.0.0",
                }
              }
            }

4. テンプレートのデプロイの状態を取得します。 詳細については、次を参照してください。 [テンプレートの展開に関する情報を取得](https://msdn.microsoft.com/library/azure/dn790565.aspx)します。

         GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>


## Visual Studio でのデプロイ

Visual Studio では、リソース グループ プロジェクトを作成して、それをユーザー インターフェイスから Azure にデプロイできます。 プロジェクトに含めるリソースの種類を選択すると、それらのリソースがリソース マネージャー テンプレートに自動的に追加されます。 プロジェクトでは、テンプレートをデプロイするための PowerShell スクリプトも提供されます。

Visual Studio を使用して、リソース グループでの概要については、を参照してください [作成および Visual Studio を使用して Azure リソース グループを展開する](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。

## ポータルでデプロイする

ご存知でしょうか。 作成したすべてのアプリケーション、 [ポータル](https://portal.azure.com/) Azure リソース マネージャーのテンプレートに基づきます。 ポータルを使用して仮想マシン、Virtual Network、Storage アカウント、App Service、またはデータベースを作成するだけで、追加の操作を行わなくても、Azure リソース マネージャーの利点を活用できます。 
**[新規]** アイコンを選択するだけで、Azure リソース マネージャーを使用してアプリケーションのデプロイを進めることができます。

![新規](./media/resource-group-template-deploy/new.png)

詳細については、Azure リソース マネージャーで、ポータルを使用して、次を参照してください。 [Azure ポータルを使用して、Azure リソースを管理する](azure-portal/resource-group-portal.md)します。


## パラメーター ファイル

デプロイ中にパラメーター ファイルを使用してパラメーター値をテンプレートに渡す場合は、次の例に示すような形式の JSON ファイルを作成する必要があります。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "webSiteName": {
                "value": "ExampleSite"
            },
            "webSiteHostingPlanName": {
                "value": "DefaultPlan"
            },
            "webSiteLocation": {
                "value": "West US"
            }
       }
    }

パラメーター ファイルのサイズは、64 KB 以下である必要があります。

## 次のステップ

- 例については、.NET クライアント ライブラリからリソースをデプロイする、を参照してください [.NET ライブラリとテンプレートを使用してリソースのデプロイ](arm-template-deployment.md)。
- アプリケーションのデプロイの詳細な例を参照してください [プロビジョニング Azure で予測可能なマイクロ サービスをデプロイ](app-service-web/app-service-deploy-complex-application-predictably.md)
- さまざまな環境をソリューションを展開する方法のガイダンスについては、次を参照してください。 [Microsoft Azure での開発およびテスト環境](solution-dev-test-environments-preview-portal.md)します。
- Azure リソース マネージャー テンプレートのセクションでは、詳細については、を参照してください [のテンプレートの作成](resource-group-authoring-templates.md)。
- Azure リソース マネージャー テンプレートで使用できる関数の一覧は、を参照してください [テンプレート関数](resource-group-template-functions.md)。







