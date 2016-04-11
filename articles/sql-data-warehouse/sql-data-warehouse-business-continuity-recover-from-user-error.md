<properties
   pageTitle="SQL Data Warehouse でデータベースをユーザー エラーから復旧する |Microsoft Azure"
   description="SQL Data Warehouse でデータベースをユーザー エラーから復旧するための手順です。 "
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/23/2015"
   ms.author="sahajs"/>

# SQL Data Warehouse でのデータベースのユーザー エラーからの復旧

SQL Data Warehouse には、意図しないデータの破損または削除の原因となるユーザー エラーから復旧するための主な機能が 2 つあります。

- ライブ データベースの復元
- 削除されたデータベースの復元

これらの機能はいずれも、同じサーバー上の新しいデータベースに復元を実行します。

## ライブ データベースの復元
ユーザー エラーが意図しないデータ変更の原因になっている場合は、保有期間内の復元ポイントのいずれかにデータベースを復元できます。 ライブ データベースのデータベース スナップショットは 8 時間ごとに作成され、7 日間保持されます。 

### PowerShell

Azure PowerShell を使用して、プログラムでデータベースの復元を実行します。 Azure PowerShell モジュールをダウンロードするには、実行 [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)します。 

データベースを復元するには、 [Start-azuresqldatabaserestore][] コマンドレットです。 

1. Microsoft Azure PowerShell を開きます。
2. Azure アカウントに接続して、アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。
3. 復元するデータベースを含むサブスクリプションを選択します。
4. データベースの復元ポイントを一覧表示します (Azure リソース管理モードにする必要があります)。
5. RestorePointCreationDate を使用して、目的の復元ポイントを選択します。
6. 目的の復元ポイントに、データベースを復元します。
7. 復元の進行状況を監視します。

```

Add-AzureAccount
Get-AzureSubscription
Select-AzureSubscription -SubscriptionName "<Subscription_name>"

# List database restore points
Switch-AzureMode AzureResourceManager
Get-AzureSqlDatabaseRestorePoints -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>" -ResourceGroupName "<YourResourceGroupName>"

# Pick desired restore point using RestorePointCreationDate
$PointInTime = "<RestorePointCreationDate>"

# Get the specific database to restore
Switch-AzureMode AzureServiceManagement
$Database = Get-AzureSqlDatabase -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>"

# Restore database
$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceServerName "<YourServerName>" -SourceDatabase $Database -TargetDatabaseName "<NewDatabaseName>" -PointInTime $PointInTime

# Monitor progress of restore operation
Get-AzureSqlDatabaseOperation -ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

サーバーが foo.database.windows.net の場合は、上の PowerShell コマンドレットで -ServerName として "foo" を使用してください。

### REST API
プログラムでデータベースの復元を実行するには、REST を使用します。

1. 「データベース復元ポイントの取得」操作を使用して、データベースの復元ポイントの一覧を取得します。
2. 使用して、復元を開始、 [を作成するデータベースの復元要求][] 操作します。
3. 使用して、復元の状態を追跡、 [データベース操作の状態][] 操作します。

に従って、復旧されたデータベースを構成することができます、復元が完了した後、 [回復したデータベースの最終処理][] ガイドです。

## 削除されたデータベースの復旧
データベースが削除された場合、削除されたデータベースを削除時の状態に復元することができます。 Azure SQL Data Warehouse では、データベースが削除され 7 日間保持される前に、データベースのスナップショットが作成されます。

### PowerShell
Azure PowerShell を使用して、削除済みデータベースの復元をプログラムで実行します。 Azure PowerShell モジュールをダウンロードするには、実行 [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)します。 

削除済みデータベースを復元するには、 [Start-azuresqldatabaserestore][] コマンドレットです。

1. Microsoft Azure PowerShell を開きます。
2. Azure アカウントに接続して、アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。
3. 復元する削除済みデータベースを含むサブスクリプションを選択します。
4. 削除済みデータベースの一覧で、データベースと削除日を確認します。

```
Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>"
```

5. 削除された特定のデータベースを取得し、復元を開始します。

```
$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>" -DeletionDate "1/01/2015 12:00:00 AM"

$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName "<NewDatabaseName>"

Get-AzureSqlDatabaseOperation –ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

サーバーが foo.database.windows.net の場合は、上の PowerShell コマンドレットで -ServerName として "foo" を使用してください。

### REST API
プログラムでデータベースの復元を実行するには、REST を使用します。

1.  使用して、復元可能な削除済みデータベースのすべてを一覧表示、 [復元可能なリストにはデータベースが削除される][] 操作します。
2.  使用して復元する削除済みデータベースの詳細を取得、 [Get 復元可能なデータベースを削除する][] 操作します。
3.  使用して、復元を開始、 [を作成するデータベースの復元要求][] 操作します。
4.  使用して、復元の状態を追跡、 [データベース操作の状態][] 操作します。

に従って、復旧されたデータベースを構成することができます、復元が完了した後、 [回復したデータベースの最終処理][] ガイドです。


## 次のステップ
他の Azure SQL Database エディションのビジネス継続性機能については、読み取り、 [Azure SQL Database のビジネス継続性の概要][]します。


<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: sql-database/sql-database-business-continuity.md
[Finalize a recovered database]: sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[Create database restore request]: http://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: http://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: http://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: http://msdn.microsoft.com/library/azure/dn509562.aspx
[Start-AzureSqlDatabaseRestore]: https://msdn.microsoft.com/library/dn720218.aspx

<!--Other Web references-->


