<properties 
   pageTitle="PowerShell を使用して、Azure Data Lake Store を使用する HDInsight クラスターを構成する | Azure" 
   description="Azure PowerShell を使用して、Azure Data Lake を使用する HDInsight Hadoop クラスターを構成および使用します。" 
   services="data-lake" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/04/2015"
   ms.author="nitinme"/>

# Azure PowerShell を使用して、Data Lake Store を使用する HDInsight クラスターをプロビジョニングする

> [AZURE.SELECTOR]
- [ポータルの使用](data-lake-store-hdinsight-hadoop-use-portal.md)
- [PowerShell の使用](data-lake-store-hdinsight-hadoop-use-powershell.md)


Azure PowerShell を使用して、Azure Data Lake Store を使用するように HDInsight クラスター (Hadoop、HBase、Storm) を構成する方法について説明します。 このリリースに関する重要な考慮事項をいくつか以下に示します。

* **Hadoop クラスターと Storm クラスター (Windows および Linux) の**, 、Data Lake ストアは、追加のストレージ アカウントとしてのみ使用できます。 このようなクラスターの既定のストレージ アカウントは、Azure Storage BLOB (WASB) のままです。

* **HBase クラスターの場合 (Windows および Linux)**, 、データストア湖は既定のストレージまたはその他の記憶域として使用できます。


この記事では、追加のストレージとして Data Lake Store を使用して Hadoop クラスターをプロビジョニングします。

PowerShell を使用して、Data Lake Store を使用するように HDInsight を構成するには、次の手順が必要です。

* Azure Data Lake Store を作成する
* Data Lake Store へのロールベースのアクセスの認証を設定する
* Data Lake Store への認証を使用して HDInsight クラスターを作成する
* クラスター上でテスト ジョブを実行する

## 前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**します。 参照してください [取得 Azure 無料試用版](https://azure.microsoft.com/pricing/free-trial/)します。
- **Azure サブスクリプションを有効にする** 湖のデータ ストアのパブリック プレビューします。 参照してください [指示](data-lake-store-get-started-portal.md#signup)します。
- **Windows SDK**します。 インストールすることができます [ここ](https://dev.windows.com/en-us/downloads)します。 この機能は、セキュリティ証明書の作成に使用します。


##Azure PowerShell 1.0 以上をインストールする

最初に、バージョン 0.9x の Azure PowerShell をアンインストールする必要があります。 インストールされている PowerShell のバージョンを確認するには、PowerShell ウィンドウから次のコマンドを実行します。

    Get-Module *azure*
    
以前のバージョンをアンインストールするには、実行 **プログラムと機能** PowerShell 1.0 よりも前である場合は、インストールされているバージョンを削除し、コントロール パネルでします。 

Azure PowerShell をインストールするための主な 2 つのオプションは次のとおりです。 

- [PowerShell Gallery](https://www.powershellgallery.com/)します。 管理者特権の PowerShell ISE または管理者特権の Windows PowerShell コンソールから、次のコマンドを実行します。

        # Install the Azure Resource Manager modules from PowerShell Gallery
        Install-Module AzureRM
        Install-AzureRM
        
        # Install the Azure Service Management module from PowerShell Gallery
        Install-Module Azure
        
        # Import AzureRM modules for the given version manifest in the AzureRM module
        Import-AzureRM
        
        # Import Azure Service Management module
        Import-Module Azure

    詳細については、次を参照してください。 [PowerShell Gallery](https://www.powershellgallery.com/)します。

- [Microsoft Web Platform Installer (WebPI)](http://aka.ms/webpi-azps)します。 Azure PowerShell 0.9.x をインストールしている場合は、0.9.x のアンインストールを要求するメッセージが表示されます Azure PowerShell モジュールを PowerShell ギャラリーからインストールした場合、一貫した Azure PowerShell 環境を保つため、インストーラーにより、インストール前にモジュールを削除することが求められます。 手順については、次を参照してください。 [WebPI を使用して Azure PowerShell 1.0 をインストール](https://azure.microsoft.com/blog/azps-1-0/)します。

WebPI は月次の更新プログラムを受け取ります。 PowerShell ギャラリーは、継続的に更新プログラムを受け取ります。 PowerShell ギャラリーからのインストールを選んだ場合は、これが Azure PowerShell で最新および最良の点について情報を取得できる最初のチャネルになります。
 

## Azure Data Lake Store を作成する

Data Lake Store を作成するには、次の手順に従います。

1. デスクトップで、新しい Azure PowerShell ウィンドウを開き、次のスニペットを入力します。 ログインを求められたら、必ず、サブスクリプションの管理者または所有者としてログインしてください。

        # Log in to your Azure account
        Login-AzureRmAccount
        
        # List all the subscriptions associated to your account
        Get-AzureRmSubscription
        
        # Select a subscription 
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

3. Azure Data Lake Store アカウントは、Azure リソース グループに関連付けられます。 まず、Azure リソース グループを作成します。

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Azure リソース グループを作成する](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "Create an Azure Resource Group")

2. Azure Data Lake Store アカウントを作成します。 指定するアカウント名には、小文字と数字のみを含める必要があります。

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Azure Data Lake アカウントの作成](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "Create an Azure Data Lake account")

3. アカウントが正常に作成されたことを確認します。

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    この出力になります。 **True**します。

4. いくつかのサンプル データを Azure Data Lake にアップロードします。 このサンプル データは、HDInsight クラスターからデータにアクセスできることを確認するために、この記事の後半で使用します。 アップロードするには、いくつかのサンプル データを探している場合は取得できます、 **救急データ** フォルダーから、 [Azure Data Lake Git リポジトリ](https://github.com/MicrosoftBigData/AzureDataLake/tree/master/SQLIPSamples/SampleData/AmbulanceData)します。

        
        $myrootdir = "/"
        Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## Data Lake Store へのロールベースのアクセスの認証を設定する

各 Azure サブスクリプションは Azure Active Directory と関連付けられます。 Azure クラシック ポータルか Azure リソース マネージャー API を使ってサブスクリプションのリソースにアクセスするユーザーやサービスは、最初に Azure Active Directory での認証を実行する必要があります。 Azure のサブスクリプションやサービスにアクセス権を付与するには、Azure リソースに対する該当するロールを割り当てます。  サービスの場合は、サービス プリンシパルにより、Azure Active Directory (AAD) 内のサービスが識別されます。 このセクションでは、Azure PowerShell を使用してアプリケーションのサービス プリンシパルを作成し、作成したサービス プリンシパルにロールを割り当てることで、HDInsight のようなアプリケーション サービスに Azure のリソース (先ほど作成した Azure Data Lake Store アカウント) へのアクセス権を付与する方法を説明します。

Azure Data Lake の Active Directory 認証を設定するには、次のタスクを行う必要があります。

* 自己署名証明書の作成
* Azure Active Directory でのアプリケーションの作成、およびサービス プリンシパルの作成

### 自己署名証明書の作成

した確認 [Windows SDK](https://dev.windows.com/en-us/downloads) このセクションの手順に進む前にインストールします。 必要がありますも、ディレクトリを作成したなど **C:\mycertdir**, 、証明書が作成されます。

1. PowerShell ウィンドウから、Windows SDK をインストールした場所に移動 (通常、 `C:\Program Files (x86)\Windows Kits\10\bin\x86` を使用して、 [MakeCert][makecert] ユーティリティは、自己署名証明書と秘密キーを作成します。 次のコマンドを使用します。

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir
        $startDate = (Get-Date).ToString('MM/dd/yyyy')
        $endDate = (Get-Date).AddDays(365).ToString('MM/dd/yyyy')

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -b $startDate -e $endDate -r -len 2048

    秘密キーのパスワードを入力するよう求められます。 実行した後のコマンドは、正常に表示されます、 **CertFile.cer** と **mykey.pvk** 、証明書を指定したディレクトリにします。

4. 使用して、 [Pvk2Pfx][pvk2pfx] MakeCert 作成 .pvk と .cer ファイルを .pfx ファイルに変換するユーティリティ。 次のコマンドを実行します。

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    メッセージが表示されたら、先ほど指定した秘密キーのパスワードを入力します。 指定した値、 **po** パラメーターは、.pfx ファイルに関連付けられているパスワードです。 コマンドが正常に完了すると、指定した証明書ディレクトリに CertFile.pfx も表示されます。

###  Azure Active Directory とサービス プリンシパルの作成

このセクションでは、Azure Active Directory アプリケーションのサービス プリンシパルを作成し、そのサービス プリンシパルにロールを割り当てて、証明書を指定することでサービス プリンシパルとして認証する手順を実行します。 Azure Active Directory でアプリケーションを作成するには、次のコマンドを実行します。 

1. PowerShell コンソール ウィンドウで、次のコマンドレットを貼り付けます。 指定した値を確認、 **-displayname** プロパティは一意です。 値も、 **- ホームページ** と **- IdentiferUris** がプレース ホルダーの値では検証されません。 

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"
        
        $password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file
        
        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)
        
        $rawCertificateData = $certificatePFX.GetRawCertData()
        
        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
                    -DisplayName "HDIADL" ` 
                    -HomePage "https://contoso.com" `
                    -IdentifierUris "https://mycontoso.com" `
                    -KeyValue $credential  `
                    -KeyType "AsymmetricX509Cert"  `
                    -KeyUsage "Verify"  `
                    -StartDate $startDate  `
                    -EndDate $endDate

        $applicationId = $application.ApplicationId

2. アプリケーション ID を使用してサービス プリンシパルを作成します。

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId
        
        $objectId = $servicePrincipal.Id

3. 先ほど作成した Data Lake Store へのアクセス権をサービス プリンシパルに付与します。
        
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All

    プロンプトで、次のように入力します。 **Y** を確認します。

## Data Lake Store への認証を使用して HDInsight クラスターを作成する

このセクションでは、HDInsight Hadoop クラスターを作成します。 このリリースでは、HDInsight クラスターと Data Lake Store は同じ場所 (米国東部 2) にある必要があります。

1. 最初に、サブスクリプションのテナント ID を取得します。 この情報は後で必要になります。

        $tenantID = (Get-AzureRmContext).Tenant.TenantId

2. このリリースでは、Hadoop クラスターの場合、Data Lake Store はクラスターの追加のストレージとしてのみ使用できます。 既定のストレージは、Azure Storage BLOB (WASB) のままです。 そのため、クラスターに必要なストレージ アカウントとストレージ コンテナーを最初に作成します。

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
        
        New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS
 
        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $containerName -Context $destContext

3. HDInsight クラスターを作成します。 次のコマンドレットを使用します。

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $rdpCredentials = Get-Credential
        
        New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.2" -RdpCredential $rdpCredentials -RdpAccessExpiry (Get-Date).AddDays(14) -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    コマンドレットが正常に完了すると、次のような出力が表示されます。

        Name                      : hdiadlcluster
        Id                        : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/hdiadlgroup/providers/Mi
                                    crosoft.HDInsight/clusters/hdiadlcluster
        Location                  : East US 2
        ClusterVersion            : 3.2.7.707
        OperatingSystemType       : Windows
        ClusterState              : Running
        ClusterType               : Hadoop
        CoresUsed                 : 16
        HttpEndpoint              : hdiadlcluster.azurehdinsight.net
        Error                     :
        DefaultStorageAccount     :
        DefaultStorageContainer   :
        ResourceGroup             : hdiadlgroup
        AdditionalStorageAccounts : 

## Data Lake Store を使用する HDInsight クラスターでテスト ジョブを実行する

HDInsight クラスターを構成した後は、クラスターでテスト ジョブを実行し、HDInsight クラスターが Data Lake Store にアクセス可能であるかどうかをテストできます。 これを行うには、前に Data Lake Store にアップロードしたサンプル データを使用してテーブルを作成するサンプル Hive ジョブを実行します。

Hive クエリを実行するには、次のコマンドレットを使用します。 このクエリでは、Data Lake Store 内のデータからテーブルを作成し、作成されたテーブルに対して SELECT クエリを実行します。

    $queryString = "DROP TABLE vehicles;" + "CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://$dataLakeStoreName.azuredatalakestore.net:443/';" + "SELECT * FROM vehicles LIMIT 10;"
    
    $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString

    $hiveJob = Start-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $httpCredentials

    Wait-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $httpCredentials

これによる出力は次のとおりです。 **値** の出力では 0、ジョブが正常に完了したことを提案します。

    Cluster         : hdiadlcluster.
    HttpEndpoint    : hdiadlcluster.azurehdinsight.net
    State           : SUCCEEDED
    JobId           : job_1445386885331_0012
    ParentId        :
    PercentComplete :
    ExitValue       : 0
    User            : admin
    Callback        :
    Completed       : done

次のコマンドレットを使用して、ジョブから出力を取得します。 

    Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -DefaultContainer $containerName -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -ClusterCredential $httpCredentials

ジョブの出力は次のようになります。

    1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
    1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
    1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
    1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
    1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
    1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
    1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
    1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
    1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
    1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


    

## HDFS コマンドを使用して Data Lake Store にアクセスする

Data Lake Store を使用するように HDInsight クラスターを構成したら、HDFS シェル コマンドを使用してストアにアクセスできます。

1. 新しいへのサインオン [Azure ポータル](https://portal.azure.com)します。

2. をクリックして **参照**, 、] をクリックして **HDInsight クラスター**, 、作成した HDInsight クラスターをクリックします。

3. クラスター] ブレードで [ **リモート デスクトップ**, 、し、次に、 **リモート デスクトップ** ブレードで、をクリックして **接続**します。

    ![HDI クラスターにリモートから接続する](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.HDI.PS.Remote.Desktop.png "Create an Azure Resource Group")

    メッセージが表示されたら、リモート デスクトップ ユーザーに対して指定した資格情報を入力します。 

4. リモート セッションで、Windows PowerShell を起動し、HDFS ファイル システムのコマンドを使用して、Azure Data Lake のファイルを一覧表示します。

        hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

    これにより、以前に Data Lake Store にアップロードしたファイルが一覧表示されます。

        15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
        Found 1 items
        -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/vehicle1_09142014.csv

    また、`hdfs dfs -put` コマンドを使用して Azure Data Lake にいくつかのファイルをアップロードしてから、`hdfs dfs -ls` を使用してそのファイルが正常にアップロードされたことを確認することもできます。

## 関連項目

* [ポータル: Data Lake Store を使用する HDInsight クラスターを作成する](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx


