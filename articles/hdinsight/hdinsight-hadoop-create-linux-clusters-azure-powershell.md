<properties
    pageTitle="Azure PowerShell を使用した HDInsight 用の Linux ベースの Hadoop、HBase、または Storm クラスターの作成 | Microsoft Azure"
    description="Azure PowerShell を使用して、HDInsight 用の Linux ベースの Hadoop、HBase、または Storm クラスターを作成する方法について説明します。"
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="11/16/2015"
    ms.author="nitinme"/>

#Azure PowerShell を使用した HDInsight の Linux ベースのクラスターの作成

[AZURE.INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell は、Azure のワークロードのデプロイと管理を制御し自動化するために使用できる強力なスクリプティング環境です。 このドキュメントでは、Azure PowerShell を使用して Linux ベースの HDInsight クラスターをプロビジョニングする方法について説明し、スクリプト例を示します。

> [AZURE.NOTE] Azure PowerShell は、Windows のクライアントにできるだけです。 Linux、Unix、Mac OS X クライアントを使用している場合は、次を参照してください。 [Azure CLI を使用して Linux ベースの HDInsight クラスターを作成する](hdinsight-hadoop-create-linux-cluster-azure-cli.md) については、Azure CLI を使用してクラスターを作成します。

###前提条件

- **Azure サブスクリプション**します。 参照してください [取得 Azure 無料試用版](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)します。

- __Azure PowerSHell__します。 HDInsight Windows PowerShell コマンドレットを実行するワークステーションの構成方法の詳細については、次を参照してください。 [をインストールし、Azure PowerShell を構成](../install-configure-powershell.md)します。 HDInsight で Azure PowerShell の使用の詳細については、次を参照してください。 [PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md)します。 HDInsight Windows PowerShell コマンドレットの一覧で、次を参照してください。 [HDInsight コマンドレット リファレンス](https://msdn.microsoft.com/library/azure/dn858087.aspx)します。


##クラスターを作成する

Azure PowerShell を使用して HDInsight クラスターをプロビジョニングするには、以下の手順が必要です。

- Azure リソース グループの作成
- Azure Storage アカウントの作成
- Azure BLOB コンテナーの作成
- HDInsight クラスターの作成

Linux クラスターをプロビジョニングするために設定する必要がある最も重要な 2 つのパラメーターは、OS の種類と SSH ユーザーの詳細を指定するパラメーターです。

- 指定するかどうかを確認、 **- OSType** パラメーターとして **Linux**です。
- クラスターのリモート セッションで SSH を使用するには、SSH ユーザー パスワードまたは SSH 公開キーのいずれかを指定します。 SSH ユーザー パスワードと SSH 公開キーを両方とも指定すると、キーが無視されます。 リモート セッションで SSH キーを使用する場合は、入力を求められたら、空の SSH パスワードを指定する必要があります。 HDInsight での SSH の使用方法の詳細については、次の記事をご覧ください。
    
    * [Linux、Unix、または OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

次のスクリプトでは、新しいクラスターを作成する方法を示します。

    ###########################################
    # Create required items, if none exist
    ###########################################

    # Sign in
    Add-AzureRmAccount

    # Select the subscription to use
    $subscriptionID = "<SubscriptionName>"        # Provide your Subscription Name
    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    # Create an Azure Resource Group
    $resourceGroupName = "<ResourceGroupName>"      # Provide a Resource Group name
    $location = "<Location>"                        # For example, "West US"
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure Storage account
    $storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

    # Create an Azure Blob Storage container
    $containerName = "<ContainerName>"              # Provide a container name
    $storageAccountKey = Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
    $destContext = New-AzureRmStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    New-AzureRmStorageContainer -Name $containerName -Context $destContext

    ###########################################
    # Create an HDInsight Cluster
    ###########################################

    # Skip these variables if you just created them
    $resourceGroupName = "<ResourceGroupName>"      # Provide the Resource Group name
    $storageAccountName = "<StorageAcccountName>"   # Provide the Storage account name
    $containerName = "<ContainerName>"              # Provide the container name
    $storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }

    # Set these variables
    $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
    $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
    $credentials = Get-Credential
    $sshCredentials = Get-Credential

    # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
    $location = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

    # Create a new HDInsight cluster
    New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -OSType Linux -Version "3.2" -SshCredential $sshCredentials

指定する値 **$clusterCredentials** 、クラスターの Hadoop ユーザー アカウントの作成に使用されます。 このアカウントを使用してクラスターに接続します。 指定する値、 **$sshCredentials** 、クラスターの SSH ユーザーの作成に使用されます。 このアカウントを使用してクラスターでリモート SSH セッションを開始し、ジョブを実行します。

> [AZURE.IMPORTANT] このスクリプトでは、クラスターに含まれる worker ノードの数を指定する必要があります。 クラスター作成または作成後のスケーリングで 32 を超える worker ノードの使用を計画している場合は、コア数が 8 個以上で RAM が 14 GB 以上のサイズのヘッド ノードも指定する必要があります。
>
> ノードのサイズとそれに伴うコストに関する詳細については、次を参照してください。 [HDInsight 料金](https://azure.microsoft.com/pricing/details/hdinsight/)します。

プロビジョニングの完了までに、最大で 15 分かかることがあります。

##次のステップ

HDInsight クラスターが正常に作成されました。次に、クラスターの使用方法について、以下のトピックをご覧ください。

###Hadoop クラスター

* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)

###HBase クラスター

* [HDInsight での HBase の使用](hdinsight-hbase-tutorial-get-stared-linux.md)
* [HDInsight での HBase の Java アプリケーションの開発](hdinsight-hbase-build-java-maven-linux)

###Storm クラスター

* [HDInsight での Storm の Java トポロジの開発](hdinsight-storm-develop-java-topology.md)
* [HDInsight の Storm での Python コンポーネントの使用](hdinsight-storm-develop-python.md)
* [HDInsight の Storm を使用したトポロジのデプロイと監視](hdinsight-storm-deploy-monitor-topology-linux.md)

