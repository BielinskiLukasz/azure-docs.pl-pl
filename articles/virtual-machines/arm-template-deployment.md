<properties
    pageTitle="テンプレートを使用して Azure リソースをデプロイする | Microsoft Azure"
    description="Azure リソース管理ライブラリで利用可能ないくつかのクライアントを使用して、仮想マシン、仮想ネットワーク、ストレージ アカウントをデプロイする方法を学習します。"
    services="virtual-machines,virtual-networks,storage"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/25/2015"
    ms.author="davidmu"/>

# .NET ライブラリとテンプレートを使用した Azure リソースのデプロイ

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 従来のデプロイ モデルです。


リソース グループとテンプレートを使用すると、アプリケーションをサポートするすべてのリソースをまとめて管理できます。 このチュートリアルでは、Azure リソース管理ライブラリで利用可能なクライアントの一部を使用する方法と、仮想マシン、仮想ネットワーク、ストレージ アカウントをデプロイするためのテンプレートを作成する方法を示します。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

このチュートリアルを完了するには、以下に示すものも必要です。

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Azure ストレージ アカウント](../storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/en-us/download/details.aspx?id=34595) または [Windows Management Framework 4.0](http://www.microsoft.com/en-us/download/details.aspx?id=40855)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

これらの手順を実行するには約 30 分かかります。

## 手順 1. Azure AD にアプリケーションを追加し、アクセス許可を設定する

Azure AD を使用して Azure リソース マネージャーへの要求を認証するには、アプリケーションを既定のディレクトリに追加する必要があります。 アプリケーションを追加するには、次の手順に従います。

1. Azure PowerShell コマンド プロンプトを開き、このコマンドを実行し、メッセージが表示されたら、サブスクリプションの資格情報を入力します。

        Login-AzureRmAccount

2. 次のコマンド内の {password} を使用するパスワードに置き換え、このコマンドを実行してアプリケーションを作成します。

        New-AzureRmADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

    >[AZURE.NOTE] 次の手順に必要なため、アプリケーションを作成した後に返されるアプリケーション識別子を書き留めます。 アプリケーション ID は、Azure ポータルの Active Directory セクションにあるアプリケーションのクライアント ID フィールドでも確認できます。

3. {application-id} を記録しておいた ID に置き換えてから、次のようにアプリケーションのサービス プリンシパルを作成します。

        New-AzureRmADServicePrincipal -ApplicationId {application-id}

4. アプリケーションを使用するためのアクセス許可を設定します。

        New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## 手順 2: Visual Studio プロジェクト、テンプレート ファイル、パラメーター ファイルを作成する

###テンプレート ファイルを作成する

Azure リソース マネージャー テンプレートによって、リソースや関連するデプロイ パラメーターの JSON 記述を使用して、Azure リソースをまとめてデプロイし、管理することが可能になります。 Visual Studio で、次の手順を実行します。

1. クリックして **ファイル** > **新しい** > **プロジェクト**します。

2.  **テンプレート** > **Visual c#**, [ **コンソール アプリケーション**, 、プロジェクトの場所と名前を入力し、クリックして **[ok]**します。

3. ソリューション エクスプ ローラーでプロジェクト名を右クリックし、 **追加** > **[新しい項目の**です。

4.  [新しい項目の追加] ウィンドウで選択 **テキスト ファイル**, 、入力 *VirtualMachineTemplate.json* 名、およびクリック **追加**します。

5.  VirtualMachineTemplate.json ファイルを開き、開始と終了の角かっこ、必要なスキーマ要素、および必要な contentVersion 要素を追加します。

        {
            "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
            "contentVersion": "1.0.0.0",
        }

6. [パラメーター](../resource-group-authoring-templates.md#parameters) 必ずしも必要ではありませんが、テンプレートの管理が容易です。 パラメーターでは、値の型、必要な場合の既定値、および場合によっては許容される値を記述します。 このチュートリアルでは、仮想マシン、ストレージ アカウント、および仮想ネットワークの作成に使用されるパラメーターをテンプレートに追加します。

    contentVersion 要素の後に、parameters 要素とその子要素を追加します。

        {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
        }

7.  [変数](../resource-group-authoring-templates.md#variables) は頻繁に変更する値またはパラメーター値の組み合わせから作成する必要がある値を指定するテンプレートで使用できます。

    parameters セクションの後に、variables 要素を追加します。

        {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
          "variables": {
            "sourceImageName" : "[concat('/',parameters('subscriptionId'),'/services/images/',parameters('vmSourceImageName'))]",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',parameters('subnet1Name'))]"
          },
        }

8.  [リソース](../resource-group-authoring-templates.md#resources) など、仮想マシン、仮想ネットワーク、ストレージ アカウントが、テンプレートで定義次にします。

    variables セクションの後に、resources セクションを追加します。

        {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
          "variables": {
            "sourceImageName" : "[concat('/',parameters('subscriptionId'),'/services/images/',parameters('vmSourceImageName'))]",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',parameters('subnet1Name'))]"
          },
          "resources": [ {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('newStorageAccountName')]",
            "location": "[parameters('location')]",
            "properties": { "accountType": "[parameters('storageAccountType')]" }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
              "publicIPAllocationMethod": "[parameters('publicIPAddressType')]",
              "dnsSettings": { "domainNameLabel": "[parameters('dnsName')]" }
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
              "addressSpace": { "addressPrefixes": [ "[parameters('addressPrefix')]" ] },
              "subnets": [ {
                "name": "[parameters('subnet1Name')]",
                "properties": { "addressPrefix": "[parameters('subnet1Prefix')]" }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties": { "addressPrefix": "[parameters('subnet2Prefix')]" }
              } ]
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[parameters('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
              "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
            ],
            "properties": {
              "ipConfigurations": [ {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": {
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddressName'))]"
                  },
                  "subnet": { "id": "[variables('subnet1Ref')]" }
                }
              } ]
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
              "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
            ],
            "properties": {
              "hardwareProfile": { "vmSize": "[parameters('vmSize')]" },
              "osProfile": {
                "computername": "[parameters('vmName')]",
                "adminUsername": "[parameters('adminUsername')]",
                "adminPassword": "[parameters('adminPassword')]",
                "windowsProfile": { "provisionVMAgent": "true" }
              },
              "storageProfile": {
                "sourceImage": { "id": "[variables('sourceImageName')]" },
                "destinationVhdsContainer" : "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmStorageAccountContainerName'),'/')]"
              },
              "networkProfile": {
                "networkInterfaces" : [ {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                } ]
              }
            }
          } ]
        }

9.  作成したテンプレート ファイルを保存します。

###パラメーター ファイルの作成

テンプレートで定義されているリソース パラメーターの値を指定するには、値を含むパラメーター ファイルを作成し、テンプレートと共にリソース マネージャーに送信します。 Visual Studio で、次の手順を実行します。

1.  ソリューション エクスプ ローラーでプロジェクト名を右クリックし、 **追加**, 、し  **[新しい項目の**です。

2.  [新しい項目の追加] ウィンドウで選択 **テキスト ファイル**, 、入力 *Parameters.json* 名、およびクリック **追加**します。

3.  Parameters.json ファイルを開き、次の JSON コンテンツを追加します。

        {
          "contentVersion": "1.0.0.0",
          "parameters": {
            "vmName": { "value": "mytestvm1" },
            "newStorageAccountName": { "value": "mytestsa1" },
            "storageAccountType": { "value": "Standard_LRS" },
            "publicIPaddressName": { "value": "mytestip1" },
            "location": { "value": "West US" },
            "vmStorageAccountContainerName": { "value": "vhds" },
            "vmSize": { "value": "Standard_A1" },
            "subscriptionId": { "value": "{subscription-id}" },
            "vmSourceImageName": { "value": "{source-image-name}" },
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" },
            "virtualNetworkName": { "value": "mytestvn1" },
            "dnsName": { "value": "mytestdns1" },
            "nicName": { "value": "mytestnic1" }
          }
        }

    >[AZURE.NOTE] イメージの vhd 名は、仮想マシンを展開する現在のイメージ名を取得する必要がありますに、イメージ ギャラリーで定期的に変更します。 これを行うには、次を参照してください。 [仮想マシンのイメージについて](https://azure.microsoft.com/documentation/articles/virtual-machines-images/), 、し {ソース イメージ名} を使用する vhd ファイルの名前に置き換えます。 たとえば、"a699494373c04fc0bc8f2bb1389d6106__windows-server-2012-r2-201412.01-en.us-127gb.vhd など"です。 {subscription-id} を、サブスクリプションの ID に置き換えます。


4.  作成したパラメーター ファイルを保存します。

テンプレート ファイルとパラメーター ファイルには、Azure ストレージ アカウントから Azure リソース マネージャーを使用してアクセスします。 記憶域にファイルを配置するには、次のようにします。

1.  サーバー エクスプローラーを開き、ファイルを配置するストレージ アカウント内のコンテナーに移動します。 このチュートリアルでは、テンプレートを配置するコンテナーの名前は templates です。

2.  テンプレート コンテナー ウィンドウの右上隅にある Blob アップロード アイコンを作成した VirtualMachineTemplate.json ファイルを参照してクリックをクリックして **開く**します。

3. Blob アップロード アイコンをもう一度クリックして、をクリックし、作成した Parameters.json ファイルを見つけます **開く**します。

##手順 3: ライブラリをインストールする

NuGet パッケージを使用すると、このチュートリアルを完了するために必要なライブラリを簡単にインストールできます。 Azure リソース管理ライブラリと Azure Active Directory 認証ライブラリをインストールする必要があります。 Visual Studio でこれらのライブラリを入手するには、次の手順に従います。

1.  ソリューション エクスプ ローラーでプロジェクト名を右クリックし、 **NuGet パッケージの管理**します。

2.  型 *Active Directory* [検索] ボックスで、 **インストール** Active Directory 認証ライブラリのパッケージ化、およびパッケージをインストールする手順に従います。

3.  ページの上部にある次のように選択します。 **プレリリースを含める**します。 型 *Azure リソース管理* [検索] ボックスで、 **インストール** Microsoft Azure リソース管理ライブラリにし、パッケージをインストールする指示に従います。

これで、ライブラリを使用してアプリケーションの作成を開始する準備が整いました。

##手順 4: 要求の認証に使用する資格情報を作成する

Azure Active Directory アプリケーションを作成し、認証ライブラリをインストールしたので、次にアプリケーションの情報を使用して、Azure リソース マネージャーへの要求の認証に使用される資格情報を作成します。 以下の手順を実行します。

1.  作成したプロジェクトの Program.cs ファイルを開き、次の using ステートメントをファイルの先頭に追加します。

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.Resources.Models;

2.  資格情報の作成に必要なトークンを取得するために、次のメソッドを Program クラスに追加します。

        private static string GetAuthorizationHeader()
        {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
            var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
            var result = context.AcquireToken("https://management.azure.com/", cc);
          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.AccessToken;

          return token;
        }

    {application-id} を前に記録したアプリケーション ID に、{password} を AD アプリケーション用に選択したパスワードに、{tenant-id} をサブスクリプションのテナント ID に、それぞれ置き換えます。 テナント ID は Get-AzureSubscription を実行して確認できます。

3.  資格情報を作成するには、Program.cs ファイルの Main メソッドに次のコードを追加します。

        var token = GetAuthorizationHeader();
        var credential = new TokenCloudCredentials("{subscription-id}", token);

4.  Program.cs ファイルを保存します。


##手順 5: テンプレートをデプロイするためのコードを追加する

リソースは常にテンプレートからリソース グループにデプロイされます。 使用する、 [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) と [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) クラスに、リソースが配置されているリソース グループを作成します。

1.  リソース グループを作成するために、次のメソッドを Program クラスに追加します。

        public async static void CreateResourceGroup(TokenCloudCredentials credential)
        {
          Console.WriteLine("Creating the resource group...");
          var resourceGroup = new ResourceGroup { Location = "West US" };
          using (var resourceManagementClient = new ResourceManagementClient(credential))
          {
            var rgResult = await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync("mytestrg1", resourceGroup);
            Console.WriteLine(rgResult.StatusCode);
          }
        }

2.  追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

        CreateResourceGroup(credential);
        Console.ReadLine();

3.  定義したテンプレートを使用してリソースをリソース グループにデプロイするために、次のメソッドを Program クラスに追加します。

        public async static void CreateTemplateDeployment(TokenCloudCredentials credential)
        {
          Console.WriteLine("Creating the template deployment...");
          var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = new Uri("https://{storage-account-name}.blob.core.windows.net/templates/VirtualMachineTemplate.json")
            },
            ParametersLink = new ParametersLink
            {
              Uri = new Uri("https://{storage-account-name}.blob.core.windows.net/templates/Parameters.json")
            }
          };
          using (var templateDeploymentClient = new ResourceManagementClient(credential))
          {
            var dpResult = await templateDeploymentClient.Deployments.CreateOrUpdateAsync("mytestrg1", "mytestdp1", deployment);
            Console.WriteLine(dpResult.StatusCode);
          }
        }

    {storage-account-name} を、前にファイルを配置したアカウントの名前に置き換えます。

4.  追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

        CreateTemplateDeployment(credential);
        Console.ReadLine();

##手順 6: リソースを削除するコードを追加する

Azure で使用されるリソースに対して課金されるため、不要になったリソースは削除することを常にお勧めします。 リソース グループから各リソースを個別に削除する必要はありません。 リソース グループを削除すると、そのすべてのリソースが自動的に削除されます。

1.  リソース グループを削除するために、次のメソッドを Program クラスに追加します。

        public async static void DeleteResourceGroup(TokenCloudCredentials credential)
        {
          using (var resourceGroupClient = new ResourceManagementClient(credential))
          {
            var rgResult = await resourceGroupClient.ResourceGroups.DeleteAsync("mytestrg1");
            Console.WriteLine(rgResult.StatusCode);
          }
        }

2.  追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

        DeleteResourceGroup(credential);
        Console.ReadLine();

##手順 7: コンソール アプリケーションを実行する

1.  コンソール アプリケーションを実行するにはクリックして **開始** Visual Studio し、同じユーザー名と、サブスクリプションで使用するパスワードを使用して Azure AD にサインインします。

2.  キーを押して **Enter** 後、各リソースを作成する各状態コードが返されます。 仮想マシンが作成されたら、次の手順を実行した後、Enter キーを押してすべてのリソースを削除します。

    このコンソール アプリケーションが実行を開始してから完全に終了するまでには、約 5 分かかります。 Enter キーを押してリソースの削除を開始する前に、Azure ポータルでリソースの作成状況を確認することもできます。

3. Azure ポータルで監査ログを参照し、リソースの状況を確認します。

    ![AD アプリケーションの作成](./media/arm-template-deployment/crpportal.png)

