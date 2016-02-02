<properties 
    pageTitle="PowerShell で BACPAC ファイルをインポートして新しい Azure SQL Database を作成する" 
    description="PowerShell で BACPAC ファイルをインポートして新しい Azure SQL Database を作成する" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="12/01/2015"
    ms.author="sstein"/>


# PowerShell で BACPAC ファイルをインポートして新しい Azure SQL Database を作成する

**1 つのデータベース**

> [AZURE.SELECTOR]
- [Azure portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)



この記事は、PowerShell で BACPAC ファイルをインポートして Azure SQL Database を作成する手順について説明します。

BACPAC は、データベース スキーマとデータを含む .bacpac ファイルです。 詳細については、バックアップ パッケージ (.bacpac) を参照してください [データ層アプリケーション](https://msdn.microsoft.com/library/ee210546.aspx)します。

Azure Storage BLOB コンテナーからインポートされた BACPAC からデータベースが作成されます。 Azure ストレージに .bacpac ファイルがあるない場合は、次の手順に従って、1 つを作成 [作成と Azure SQL Database の BACPAC のエクスポート](sql-database-export-powershell.md)します。
> [AZURE.NOTE] Azure SQL Database では、復元できるすべてのユーザー データベースのバックアップが自動的に作成され、保守されます。 詳細については、「 [ビジネス継続性の概要](sql-database-business-continuity.md)します。


SQL Database をインポートするには、以下が必要です。

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合、このページの上部の**無料試用版**をクリックしてからこの記事に戻り、最後まで完了してください。
- 復元するデータベースの .bacpac ファイル (BACPAC)。 BACPAC がである必要があります、 [Azure ストレージ アカウント (クラシック)](storage-create-storage-account.md) blob コンテナーです。


> [AZURE.IMPORTANT] この記事には、バージョン 1.0 *未満*の Azure PowerShell に対応するコマンドが含まれています。 Azure PowerShell のバージョンは、**Get-Module azure | format-table version** コマンドで確認できます。



## 資格情報を構成してサブスクリプションを選択

まず、Azure アカウントへのアクセスを確立する必要があるため、PowerShell を起動してから以下のコマンドレットを実行します。 ログイン画面で、Azure ポータルへのサインインに使用しているものと同じ電子メールとパスワードを入力します。

    Add-AzureAccount

正常にサインインすると、サインインしている ID や使用中の Azure サブスクリプションを含む情報が画面に表示されます。


### Azure サブスクリプションを選択します。

サブスクリプションを選択するには、サブスクリプション ID が必要です。 サブスクリプション ID は前の手順で表示された情報からコピーできます。または、複数のサブスクリプションがあり、詳しい情報が必要な場合は、**Get-AzureSubscription** コマンドレットを実行して、結果セットから目的のサブスクリプション情報をコピーできます。 サブスクリプション ID を取得したら、次のコマンドレットを実行します。

    Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

**Select-AzureSubscription** を正常に実行すると、PowerShell プロンプトに戻ります。 サブスクリプションが複数ある場合は、**Get-AzureSubscription** を実行して、選択したサブスクリプションが **IsCurrent : True** と表示されていることを確認できます。


## 使用環境用の変数の設定

変数には、例の値を、使用するデータベースとストレージ アカウントの特定の値に置き換える必要があるものがいくつかあります。

サーバー名は、前の手順で選択したサブスクリプション内に現在存在しており、データベースを作成するサーバーを指定する必要があります。

データベース名は、新しいデータベースの名前です。

    $ServerName = "servername"
    $DatabaseName = "databasename"

BACPAC が配置されているストレージ アカウントの変数を以下に示します。  [Azure ポータル](https://portal.azure.com) をこれらの値を取得するストレージ アカウントに移動します。 プライマリ アクセス キーは、ストレージ アカウントのブレードで **[すべての設定]** をクリックしてから **[キー]** をクリックすることで検索できます。

BLOB 名は、データベースの作成元の、既存の .bacpac ファイルの名前です。 .bacpac 拡張子を含める必要があります。

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $BlobName = "filename.bacpac"
    $StorageKey = "primaryaccesskey"

## サーバーおよびストレージ アカウントを指すポインターの作成

**Get-Credential** コマンドレットを実行すると、ユーザー名とパスワードの入力を求めるウィンドウが開きます。 データベースを作成する SQL Server (上記の $ServerName) の管理ログインとパスワードを入力します。Azure アカウントのユーザー名とパスワードではありません。

    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential
    
    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx

## データベースのインポート

このコマンドでは、サービスにデータベース のインポート要求を送信します。 データベースのサイズに応じて、インポート操作の完了に時間がかかる場合があります。

    $importRequest = Start-AzureSqlDatabaseImport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName

## 操作の進行状況の監視

**Start-AzureSqlDatabaseImport** を実行した後、要求の状態を確認できます。

要求を送信した直後に状態を確認すると、通常は、**保留中**または**実行中**の状態が返され、現在の完了率が示されます。このため、出力に **Status : Completed** が表示されるまで何度も確認を実行できます。

このコマンドを実行すると、パスワードの入力を求められます。 SQL Server の管理ログインとパスワードを入力します。


    Get-AzureSqlDatabaseImportExportStatus -RequestId $ImportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName

## SQL Database の PowerShell 復元スクリプト

    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ServerName = "servername"
    $DatabaseName = "nameofnewdatabase"
    
    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $BlobName = "filename.bacpac"
    $StorageKey = "primaryaccesskey"
    
    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential
    
    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx
    
    $ImportRequest = Start-AzureSqlDatabaseImport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    
    Get-AzureSqlDatabaseImportExportStatus -RequestId $ImportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName

## 次のステップ

- [SQL Server Management Studio (SSMS) による接続します。](sql-database-connect-to-database.md)




## その他のリソース

- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [災害復旧の訓練](sql-database-disaster-recovery-drills.md)
- [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)





