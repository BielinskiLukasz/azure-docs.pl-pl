<properties
    pageTitle="Script Action を使った HDInsight クラスターのカスタマイズ | Microsoft Azure"
    description="スクリプト アクションを使用して Linux ベースの HDInsight クラスターにカスタム コンポーネントを追加する方法について説明します。 スクリプト アクションは、クラスター作成時に実行される Bash スクリプトであり、クラスター構成のカスタマイズや、サービスおよびユーティリティ (Hue、Solr、R など) の追加に使用できます。"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/20/2015"
    ms.author="larryfr"/>

# Script Action を使って HDInsight クラスターをカスタマイズする

HDInsight という構成オプションを提供する **Script Action** 作成プロセス中に、クラスターで実行するカスタマイズを定義するカスタム スクリプトを呼び出します。 これらのスクリプトを使用して、クラスター上に追加のソフトウェアをインストールしたり、クラスター上のアプリケーションの構成を変更したりできます。

> [AZURE.NOTE] この記事の情報は、Linux ベースの HDInsight クラスターに固有です。 この記事は Windows ベースのクラスターに固有のバージョンは、 [カスタマイズを使用した hdinsight Script Action (Windows)](hdinsight-hadoop-customize-cluster.md)

## クラスターの作成処理での Script Action

Script Action は、クラスターが作成中にのみ使用されます。 次の図は、作成処理中に Script Action が実行された場合を示しています。

![クラスター作成時の HDInsight クラスターのカスタマイズと段階][img-hdi-cluster-states]

スクリプトは HDInsight の構成中に実行されます。 この段階で、スクリプトは、クラスター内の指定されたすべてのノードで並列して実行され、それらのノードに対するルート権限で実行されます。

> [AZURE.NOTE] あるため、スクリプトは、するときに、クラスター ノードで root 権限を実行、停止や、Hadoop 関連のサービスを含むサービスの開始などの操作を行うことができます。 サービスを停止する場合は、スクリプトの実行が完了する前に Ambari サービスや他の Hadoop 関連のサービスが稼働していることを確認する必要があります。 これらのサービスでは、クラスターの作成時にクラスターが正常に稼動しているか確認する必要があります。

各クラスターは、指定された順序で呼び出される複数の Script Action を受け取ることができます。 スクリプトはヘッド ノード、ワーカー ノード、またはその両方で実行できます。

> [AZURE.IMPORTANT] 60 分内にスクリプトのアクションが完了する必要があります、またはタイムアウトになります。 ノードのプロビジョニング中、スクリプトは他のセットアップ プロセスや構成プロセスと同時に実行されます。 CPU 時間やネットワーク帯域幅などのリソースの競合が原因で、開発環境の場合よりスクリプトの完了に時間がかかる場合があります。
> 
> スクリプトの実行時間を最小限に抑えるために、ソースからアプリケーションをダウンロードしてコンパイルするなどのタスクを実行しないようにしてください。 代わりに、アプリケーションを事前にコンパイルし、バイナリを Azure BLOB ストレージに格納して、クラスターにすばやくダウンロードできるようにします。


## スクリプト アクションのサンプル スクリプト

Script Action のスクリプトは、Azure ポータル、Azure PowerShell、または HDInsight .NET SDK から使用できます。 この記事では、ポータルから Script Action を使用する方法を示します。 PowerShell と .NET SDK で Script Action を使用する方法については、次の表に示されている例を参照してください。

HDInsight は、HDInsight クラスターで、次のコンポーネントをインストールするためのいくつかのスクリプトを提供します。

名前 | スクリプト
----- | -----
**Hue のインストール** | https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/install-hue-uber-v01.sh します。 参照してください [HDInsight での色合いがクラスターのインストールおよび使用](hdinsight-hadoop-hue-linux.md)します。
**Spark のインストール** | https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh します。 参照してください [HDInsight での Spark クラスターをインストールして使用する](hdinsight-hadoop-spark-install-linux.md)です。
**R のインストール** | https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh します。 参照してください [インストールし、HDInsight クラスターで R を使用して](hdinsight-hadoop-r-scripts-linux.md)します。
**Solr のインストール** | https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh します。 参照してください [インストールして使用する HDInsight クラスターに Solr](hdinsight-hadoop-solr-install-linux.md)します。
**Giraph のインストール** | https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh します。 参照してください [インストールして使用する HDInsight クラスターに Giraph](hdinsight-hadoop-giraph-install-linux.md)します。

## Azure ポータルからスクリプト アクションを使用する

1. 」の説明に従って、クラスターの作成を開始 [作成の Hadoop クラスターの HDInsight](hdinsight-provision-clusters.md#portal)します。

2. [ __オプションの構成__, の **スクリプトのアクション** ブレードで、をクリックして **[スクリプト アクションの追加** 次に示すように、スクリプト アクションの詳細を提供します。

    ![Script Action を使ってクラスターをカスタマイズする](./media/hdinsight-hadoop-customize-cluster-linux/HDI.CreateCluster.8.png)

    | プロパティ | 値 |
    | -------- | ----- |
    | 名前 | スクリプト アクションの名前を指定します。 |
    | スクリプト URI | クラスターのカスタマイズのために呼び出されるスクリプトへの URI を指定します。 |
    | ヘッド/ワーカー | ノードを指定 (**ヘッド**, 、**ワーカー**, 、または **ZooKeeper**) のカスタマイズ スクリプトを実行します。 |
    | パラメーター | スクリプトで必要な場合は、パラメーターを指定します。 |

    クラスターに複数のコンポーネントをインストールするには、Enter キーを押して複数のスクリプト アクションを追加します。

3. クリックして **選択** スクリプト アクションの構成を保存し、クラスターの作成を続行します。

## Azure リソース マネージャーのテンプレートからスクリプト アクションを使用する

このセクションでは、Azure リソース マネージャー (ARM) テンプレートを使用して HDInsight クラスターを作成します。また、スクリプト アクションを使用してクラスターにカスタム コンポーネント (この例では R) をインストールします。 このセクションでは、スクリプト アクションを使用してクラスターを作成する ARM テンプレートの例を示します。

### 開始する前に

* HDInsight Powershell コマンドレットを実行するワークステーションの構成方法の詳細については、次を参照してください。 [をインストールし、Azure PowerShell を構成](../powershell-install-configure.md)します。
* ARM テンプレートを作成する方法の手順については、次を参照してください。 [Azure リソース マネージャーの作成テンプレート](../resource-group-authoring-templates.md)します。
* 使用したがない Azure PowerShell リソース マネージャーで、表示 [Azure リソース マネージャーで Azure PowerShell を使用して](../powershell-azure-resource-manager.md)します。

### スクリプト アクションを使用したクラスターの作成

1. コンピューター上の場所に次のテンプレートをコピーします。 このテンプレートにより、クラスター内のヘッド ノードとワーカー ノードに R がインストールされます。 JSON テンプレートが有効かどうかも確認できます。 テンプレートにコンテンツを貼り付ける [JSONLint](http://jsonlint.com/), 、オンラインの JSON 検証ツールです。

            {
            "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "clusterLocation": {
                    "type": "string",
                    "defaultValue": "West US",
                    "allowedValues": [ "West US" ]
                },
                "clusterName": {
                    "type": "string"
                },
                "clusterUserName": {
                    "type": "string",
                    "defaultValue": "admin"
                },
                "clusterUserPassword": {
                    "type": "securestring"
                },
                "sshUserName": {
                    "type": "string",
                    "defaultValue": "username"
                },
                "sshPassword": {
                    "type": "securestring"
                },
                "clusterStorageAccountName": {
                    "type": "string"
                },
                "clusterStorageAccountResourceGroup": {
                    "type": "string"
                },
                "clusterStorageType": {
                    "type": "string",
                    "defaultValue": "Standard_LRS",
                    "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Standard_ZRS"
                    ]
                },
                "clusterStorageAccountContainer": {
                    "type": "string"
                },
                "clusterHeadNodeCount": {
                    "type": "int",
                    "defaultValue": 1
                },
                "clusterWorkerNodeCount": {
                    "type": "int",
                    "defaultValue": 2
                }
            },
            "variables": {
            },
            "resources": [
                {
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('clusterLocation')]",
                    "apiVersion": "2015-05-01-preview",
                    "dependsOn": [ ],
                    "tags": { },
                    "properties": {
                        "accountType": "[parameters('clusterStorageType')]"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('clusterLocation')]",
                    "apiVersion": "2015-03-01-preview",
                    "dependsOn": [
                        "[concat('Microsoft.Storage/storageAccounts/', parameters('clusterStorageAccountName'))]"
                    ],
                    "tags": { },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "hadoop",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterUserPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [
                                {
                                    "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                    "isDefault": true,
                                    "container": "[parameters('clusterStorageAccountContainer')]",
                                    "key": "[listKeys(resourceId(parameters('clusterStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).key1]"
                                }
                            ]
                        },
                        "computeProfile": {
                            "roles": [
                                {
                                    "name": "headnode",
                                    "targetInstanceCount": "[parameters('clusterHeadNodeCount')]",
                                    "hardwareProfile": {
                                        "vmSize": "Large"
                                    },
                                    "osProfile": {
                                        "linuxOperatingSystemProfile": {
                                            "username": "[parameters('sshUserName')]",
                                            "password": "[parameters('sshPassword')]"
                                        }
                                    },
                                    "scriptActions": [
                                        {
                                            "name": "installR",
                                            "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
                                            "parameters": ""
                                        }
                                    ]
                                },
                                {
                                    "name": "workernode",
                                    "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                    "hardwareProfile": {
                                        "vmSize": "Large"
                                    },
                                    "osProfile": {
                                        "linuxOperatingSystemProfile": {
                                            "username": "[parameters('sshUserName')]",
                                            "password": "[parameters('sshPassword')]"
                                        }
                                    },
                                    "scriptActions": [
                                        {
                                            "name": "installR",
                                            "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
                                            "parameters": ""
                                        }
                                    ]
                                }
                            ]
                        }
                    }
                }
            ],
            "outputs": {
                "cluster":{
                    "type" : "object",
                    "value" : "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                }
            }
        }

2. Azure PowerShell を起動し、Azure アカウントにログインします。 資格情報を提供すると、コマンドがアカウントの情報を返します。

        Add-AzureRmAccount

        Id                             Type       ...
        --                             ----
        someone@example.com            User       ...

3. 複数のサブスクリプションがある場合、デプロイに使用するサブスクリプション ID を提供します。

        Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE] 使用する `Get-AzureRmSubscription` 個々 のサブスクリプション Id が含まれているアカウントに関連付けられているすべてのサブスクリプションの一覧を取得します。

5. 既存のリソース グループがない場合は、新しいリソース グループを作成します。 ソリューションに必要なリソース グループと場所の名前を指定します。 新しいリソース グループの概要が返されます。

        New-AzureRmResourceGroup -Name myresourcegroup -Location "West US"

        ResourceGroupName : myresourcegroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup


6. リソース グループに新しいデプロイメントを作成するには、実行、 **新規 AzureRmResourceGroupDeployment** コマンドを使用し、必要なパラメーターを指定します。 パラメーターにはデプロイメントの名前、リソース グループの名前、作成したテンプレートへのパスまたは URL が含まれます。 テンプレートでパラメーターが必要な場合は、それらのパラメーターも渡す必要があります。 この場合は、クラスターに R をインストールするスクリプト アクションでパラメーターは必要ありません。


        New-AzureRmResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>


    You will be prompted to provide values for the parameters defined in the template.

7. リソース グループをデプロイすると、デプロイの概要が表示されます。

          DeploymentName    : mydeployment
          ResourceGroupName : myresourcegroup
          ProvisioningState : Succeeded
          Timestamp         : 8/17/2015 7:00:27 PM
          Mode              : Incremental
          ...

8. デプロイメントに失敗した場合は、次のコマンドレットを使用してエラーに関する情報を取得できます。

        Get-AzureRmResourceGroupDeployment -ResourceGroupName myresourcegroup -ProvisioningState Failed

## Azure PowerShell からスクリプト アクションを使用する

このセクションでは使用して、 [追加 AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) コマンドレットをクラスターのカスタマイズに Script Action を使用して、スクリプトを使用します。 次に進む前に、Azure PowerShell をインストールして構成したことを確認します。 HDInsight PowerShell コマンドレットを実行するワークステーションの構成方法の詳細については、次を参照してください。 [をインストールし、Azure PowerShell を構成](../powershell-install-configure.md)します。

次の手順に従います。

1. Azure PowerShell コンソールを開き、次の変数を宣言します。

        # PROVIDE VALUES FOR THESE VARIABLES
        $subscriptionId = "<SubscriptionId>"        # ID of the Azure subscription
        $clusterName = "<HDInsightClusterName>"         # HDInsight cluster name
        $storageAccountName = "<StorageAccountName>"    # Azure storage account that hosts the default container
        $storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
        $containerName = $clusterName
        $location = "<MicrosoftDataCenter>"             # Location of the HDInsight cluster. It must be in the same data center as the storage account.
        $clusterNodes = <ClusterSizeInNumbers>          # The number of nodes in the HDInsight cluster.
        $resourceGroupName = "<ResourceGroupName>"      # The resource group that the HDInsight cluster will be created in

2. クラスター内のノードや使用する既定のストレージなどの構成値を指定します。

        # SPECIFY THE CONFIGURATION OPTIONS
        Select-AzureRmSubscription -SubscriptionId $subscriptionId
        $config = New-AzureRmHDInsightClusterConfig
        $config.DefaultStorageAccountName="$storageAccountName.blob.core.windows.net"
        $config.DefaultStorageAccountKey=$storageAccountKey

3. 使用 **追加 AzureRmHDInsightScriptAction** コマンドレットをスクリプトを使用します。 次の例では、クラスターに R をインストールするスクリプトを使用します。

        # INVOKE THE SCRIPT USING THE SCRIPT ACTION FOR HEADNODE AND WORKERNODE
        $config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install R"  -NodeType HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
        $config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install R"  -NodeType WorkerNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh

     **追加 AzureRmHDInsightScriptAction** コマンドレットは、次のパラメーターを受け取ります。

    | パラメーター | 定義 |
    | --------- | ---------- |
    | Config | スクリプト アクション情報が追加される構成オブジェクト。 |
    | 名前 | スクリプト アクションの名前。 |
    | NodeType | カスタマイズ スクリプトが実行されるノードを指定します。 有効な値は **ヘッドノード** (ヘッド ノードにインストール) する、 **WorkerNode** (すべてのデータ ノードにインストール) する場合、または **ZookeeperNode** (zookeeper ノードにインストール) します。 |
    | パラメーター | スクリプトで必要なパラメーター。 |
    | Uri | 実行されるスクリプトへの URI を指定します。 |

4. クラスターの admin/HTTPS ユーザーを設定します。

        $httpCreds = get-credential
        
    プロンプトが表示されたら、名前に「admin」と入力し、パスワードを入力します。

5. SSH 資格情報を設定します。

        $sshCreds = get-credential
    
    プロンプトが表示されたら、SSH ユーザー名とパスワードを入力します。 パスワードではなく証明書を使用して SSH アカウントをセキュリティで保護する場合は、パスワードを空にして、使用する証明書の公開キーの内容を `$sshPublicKey` に設定します。 次に例を示します。
    
        $sshPublicKey = Get-Content .\path\to\public.key -Raw
    
4. 最後に、クラスターを作成します。
        
        New-AzureRmHDInsightCluster -config $config -clustername $clusterName -DefaultStorageContainer $containerName -Location $location -ResourceGroupName $resourceGroupName -ClusterSizeInNodes $clusterNodes -HttpCredential $httpCreds -SshCredential $sshCreds -OSType Linux
    
    SSH アカウントをセキュリティで保護するために公開キーを使用する場合、パラメーターとして `-SshPublicKey $sshPublicKey` も指定する必要があります。

クラスターが作成されるまでに数分かかる場合があります。

## HDInsight .NET SDK からスクリプト アクションを使用する

HDInsight .NET SDK は、.NET アプリケーションから HDInsight を簡単に操作できるクライアント ライブラリを提供します。 次の手順では、スクリプトを使用して HDInsight .NET SDK からクラスターをカスタマイズする方法を示します。

> [AZURE.IMPORTANT] 最初に自己署名証明書を作成してワークステーションにインストール、および Azure サブスクリプションにアップロードする必要があります。 手順については、次を参照してください。 [自己署名証明書を作成する](http://go.microsoft.com/fwlink/?LinkId=511138)です。


### Visual Studio プロジェクトを作成する


1. Visual Studio で、C# コンソール アプリケーションを作成します。
2. Nuget から **パッケージ マネージャー コンソール**, 、次のコマンドを実行します。

        Install-Package Microsoft.Azure.Common.Authentication -pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre

    これらのコマンドは、.NET ライブラリおよび .NET ライブラリへの参照を現在の Visual Studio プロジェクトに追加します。

3. 開いている **Program.cs**, 、以下のステートメントを使用します。

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Common.Authentication;
        using Microsoft.Azure.Common.Authentication.Factories;
        using Microsoft.Azure.Common.Authentication.Models;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;

4. クラスのコードを次のコードに置き換えます。

        private static HDInsightManagementClient _hdiManagementClient;

        private static Guid SubscriptionId = new Guid("<AZURE SUBSCRIPTION ID>");
        private const string ResourceGroupName = "<AZURE RESOURCEGROUP NAME>";

        private const string NewClusterName = "<HDINSIGHT CLUSTER NAME>";
        private const int NewClusterNumNodes = <NUMBER OF NODES>;
        private const string NewClusterLocation = "<LOCATION>";  // Must match the Azure Storage account location
        private const string NewClusterVersion = "3.2";
        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
        private const OSType NewClusterOSType = OSType.Linux;

        private const string ExistingStorageName = "<STORAGE ACCOUNT NAME>.blob.core.windows.net";
        private const string ExistingStorageKey = "<STORAGE ACCOUNT KEY>";
        private const string ExistingContainer = "<DEFAULT CONTAINER NAME>"; 

        private const string NewClusterUsername = "admin";
        private const string NewClusterPassword = "<HTTP USER PASSWORD>";

        private const string NewClusterSshUserName = "sshuser";
        private const string NewClusterSshPublicKey = @"---- BEGIN SSH2 PUBLIC KEY ----
            Comment: ""rsa-key-20150731""
            AAAAB3NzaC1yc2EAAAABJQAAAQEA4QiCRLqT7fnmUA5OhYWZNlZo6lLaY1c+IRsp
            gmPCsJVGQLu6O1wqcxRqiKk7keYq8bP5s30v6bIljsLZYTnyReNUa5LtFw7eauGr
            yVt3Pve6ejfWELhbVpi0iq8uJNFA9VvRkz8IP1JmjC5jsdnJhzQZtgkIrdn3w0e6
            WVfu15kKyY8YAiynVbdV51EB0SZaSLdMZkZQ81xi4DDtCZD7qvdtWEFwLa+EHdkd
            pzO36Mtev5XvseLQqzXzZ6aVBdlXoppGHXkoGHAMNOtEWRXpAUtEccjpATsaZhQR
            zZdZlzHduhM10ofS4YOYBADt9JohporbQVHM5w6qUhIgyiPo7w==
            ---- END SSH2 PUBLIC KEY ----"; //replace the public key with your own

        private static void Main(string[] args)
        {
            var tokenCreds = GetTokenCloudCredentials();
            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);

            CreateCluster();
        }

        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
        {
            var authFactory = new AuthenticationFactory();

            var account = new AzureAccount { Type = AzureAccount.AccountType.User };

            if (username != null && password != null)
                account.Id = username;

            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

            var accessToken =
                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
                    .AccessToken;

            return new TokenCloudCredentials(accessToken);
        }

        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
        {
            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
        }


        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
                SshUserName = NewClusterSshUserName,
                SshPublicKey = NewClusterSshPublicKey
            };

            ScriptAction rScriptAction = new ScriptAction("Install R",
                new Uri("https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode,new System.Collections.Generic.List<ScriptAction> { rScriptAction});
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { rScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }
        
6. クラス メンバーの値を置き換えます。

7. キーを押して **f5 キーを押して** アプリケーションを実行します。 コンソール ウィンドウが開き、アプリケーションの状態が表示されます。 Azure アカウントの資格情報の入力も求められます。 HDInsight クラスターの作成は数分かかる場合があります。


## トラブルシューティング

Ambari の Web UI を使用すると、クラスターの作成中に、スクリプトによってログに記録された情報を表示できます。 ただし、スクリプトで発生したエラーが原因でクラスターの作成に失敗した場合、クラスターに関連付けられた既定のストレージ アカウントのログを利用することもできます。 このセクションでは、これら両方のオプションを使用してログを取得する方法について説明します。

### Ambari Web UI を使用する

1. ブラウザーでは、https://CLUSTERNAME.azurehdinsight.net に移動します。 CLUSTERNAME を、使用する HDInsight クラスターの名前に置き換えます。

    プロンプトが表示されたら、クラスターの管理者アカウント名 (admin) とパスワードを入力します。 Web フォームで管理者の資格情報の再入力が必要な場合があります。

2. ページの上部にあるバーで、選択、 __ops__ エントリです。 これにより、Ambari を使用してクラスターで実行される、現在と過去の操作の一覧が表示されます。

    ![Ambari Web UI バーで OPS を選択](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. 持つエントリを検索 __run\_customscriptaction__ で、 __操作__ 列です。 これらは、スクリプト アクションの実行時に作成されます。

    ![操作のスクリーンショット](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    このエントリを選択してリンクをたどると、クラスターでスクリプトを実行したときに生成される STDOUT と STDERR の出力が表示されます。

### 既定のストレージ アカウントからログにアクセスする

スクリプトで発生したエラーが原因でクラスターの作成に失敗した場合、クラスターに関連付けられた既定のストレージ アカウントからスクリプト アクション ログに直接アクセスすることもできます。

* ストレージ ログは、`\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` にあります。 

    ![操作のスクリーンショット](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    この下で、ヘッド ノード、ワーカー ノード、および zookeeper ノードごとにログが整理されています。 次に例をいくつか示します。
    * **ヘッドノード** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`
    * **ワーカー ノード** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`
    * **Zookeeper ノード** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* 対応するホストのすべての stdout と stderr が、ストレージ アカウントにアップロードされます。 1 つである **出力 \*.txt** と **エラー \*.txt** スクリプト アクションごとにします。 output-*.txt ファイルには、ホストで実行されたスクリプトの URI に関する情報が含まれます。 たとえば、次のように入力します。

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* 同じ名前のスクリプト アクション クラスターを繰り返し作成できます。 そのような場合は、DATE フォルダー名に基づいて適切なログを識別できます。 たとえば、異なる日付で作成されるクラスターのフォルダー構造 (mycluster) は、次のようになります。
    * `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04`
    * `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* 同じ日に同じ名前のスクリプト アクション クラスターを作成する場合は、一意のプレフィックスを使用して該当するログ ファイルを識別できます。

* 1 日の最後にクラスターを作成すると、ログ ファイルが 2 日間にまたがる場合があります。 そのような場合は、同じクラスターに日付が異なる 2 つのフォルダーが作成されます。

* 既定のコンテナーへのログ ファイルのアップロードは、特に大きなクラスターの場合、最大 5 分かかることがあります。 そのため、ログにアクセスする必要がある場合は、スクリプト アクションが失敗したときにクラスターをすぐに削除しないでください。


## HDInsight クラスターで使用するオープン ソース ソフトウェアのサポート

Microsoft Azure HDInsight サービスは柔軟性に優れたプラットフォームであり、Hadoop を中心に形成されたオープン ソース テクノロジのエコシステムを利用し、クラウド内でビッグ データ アプリケーションを構築できます。 Microsoft Azure では、一般的なレベルのサポートのオープン ソース テクノロジで説明したように、 **サポート範囲** のセクションで、 [Azure サポート FAQ web サイト](http://azure.microsoft.com/support/faq/)します。 HDInsight サービスでは、次に説明するいくつかのコンポーネントについてさらに高いレベルのサポートを受けることができます。

HDInsight サービスで利用できるオープン ソース コンポーネントには、2 つの種類があります。

- **組み込みコンポーネント** -これらのコンポーネントは HDInsight クラスターにプレインストールされており、クラスターのコア機能を提供します。 たとえば、YARN リソース マネージャー、Hive クエリ言語 (HiveQL)、Mahout ライブラリなどがこのカテゴリに属します。 クラスタ コンポーネントの完全な一覧は、「[HDInsight で提供される Hadoop クラスター バージョンの新機能](hdinsight-component-versioning.md)」から入手できます。

- **カスタム コンポーネント** -インストールしたり、コミュニティで入手できるまたはユーザーによって作成された任意のコンポーネントをワークロードで使用する、クラスターのユーザーとして。

> [AZURE.WARNING] HDInsight クラスターと共に提供されるコンポーネントは完全にサポートしを特定し、これらのコンポーネントに関連する問題を解決するには Microsoft サポートが支援します。
>
> カスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。 これにより問題が解決する場合もあれば、オープン ソース テクノロジに関して、深い専門知識が入手できる場所への参加をお願いすることになる場合もあります。 たとえば、使用できますが、このような数多くのコミュニティ サイトがある: [HDInsight についての MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), 、[http://stackoverflow.com](http://stackoverflow.com)します。 Apache プロジェクトがプロジェクトのサイトを持つも [http://apache.org](http://apache.org), 、たとえば: [Hadoop](http://hadoop.apache.org/), 、[Spark](http://spark.apache.org/)します。

HDInsight サービスでは、カスタム コンポーネントを使用する方法をいくつか用意しています。 コンポーネントの用途やクラスターへのインストール方法にかかわらず、同じレベルのサポートが適用されます。 以下は、HDInsight クラスターでのカスタム コンポーネントの用途として、最も一般的な方法の一覧です。

1. ジョブの送信 - Hadoop や他の種類のジョブを、カスタム コンポーネントを実行または使用するクラスターに送信できます。

2. クラスターのカスタマイズ - クラスター作成時に、追加設定や、クラスター ノードにインストールするカスタム コンポーネントを指定できます。

3. サンプル - よく利用されるカスタム コンポーネントに対しては、それらを HDInsight クラスターで使用する方法について Microsoft やその他の提供者がサンプルを用意している場合があります。 これらのサンプルはサポートなしで提供されます。

## 次のステップ

クラスターをカスタマイズするスクリプトの作成と使用に関する情報と例については、次を参照してください。

- [HDInsight 用の Script Action スクリプトの開発](hdinsight-hadoop-script-actions-linux.md)
- [HDInsight クラスターで Spark をインストールして使用する](hdinsight-hadoop-spark-install-linux.md)
- [HDInsight クラスターに R をインストールして使用する](hdinsight-hadoop-r-scripts-linux.md)
- [HDInsight クラスターに Solr をインストールして使用する](hdinsight-hadoop-solr-install-linux.md)
- [HDInsight クラスターに Giraph をインストールして使用する](hdinsight-hadoop-giraph-install-linux.md)



[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Stages during cluster creation"

