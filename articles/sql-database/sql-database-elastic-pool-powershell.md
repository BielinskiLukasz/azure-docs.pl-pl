<properties 
    pageTitle="エラスティック データベース プールを使用したリソースのスケールアウト | Microsoft Azure" 
    description="PowerShell を使用し、複数のデータベースを管理するエラスティック データベース プールを作成して、Azure SQL Database リソースをスケールアウトする方法について説明します。" 
    keywords="multiple databases,scale-out"    
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="12/01/2015"
    ms.author="adamkr; sstein"/>

# PowerShell を使用してエラスティック データベース プールを作成し、複数の SQL Database のリソースをスケールアウトする 

> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-elastic-pool-portal.md)
- [C#](sql-database-elastic-pool-csharp.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)

複数のデータベースを作成することで管理する方法について、 [弾力性データベース プール](sql-database-elastic-pool.md) PowerShell コマンドレットを使用します。 

> [AZURE.NOTE] エラスティック データベース プールは現在プレビューで SQL Database V12 サーバーでのみ使用できます。 SQL Database V11 サーバーがある場合は、 [V12 にアップグレードし、プールを作成する PowerShell を使用して](sql-database-upgrade-server.md) で 1 つの手順です。

エラスティック データベース プールを使用すると、複数の SQL Database のデータベース リソースと管理をスケールアウトすることができます。

この記事では、Azure サブスクリプションを除き、エラスティック データベース プールを作成して構成するために必要なものすべて (V12 サーバーを含む) を作成する方法を説明します。 をクリックする必要がある場合、Azure サブスクリプションだけで **無料評価版** これの上部にあるページをこの記事を完了する通します。




Azure PowerShell のエラスティック データベース プールを作成するための手順は、わかりやすさを重視し、細かく分けて説明しています。 必要な方だけでコマンドの正確な一覧を参照してください、 **正規表現のまとめ** この記事下部のセクションです。


PowerShell コマンドレットを実行するには、Azure PowerShell をインストールし、実行している必要があります。 詳細については、次を参照してください。 [をインストールして、Azure PowerShell を構成する方法](../powershell-install-configure.md)します。




## 資格情報を構成してサブスクリプションを選択

これで、Azure リソース マネージャー モジュールが実行され、エラスティック データベース プールを作成して構成するために必要なすべてのコマンドレットにアクセスできるようになりました。 はじめに Azure アカウントへのアクセスを確立する必要があります。 次を実行すると資格情報を入力するサインイン画面が表示されます。 Azure ポータルへのサインインに使用しているものと同じ電子メールとパスワードを使用します。

    Add-AzureRmAccount

正常にサインインすると、サインインしている ID や使用中の Azure サブスクリプションを含む情報が画面に表示されます。


### Azure サブスクリプションを選択します。

サブスクリプションを選択する必要があります、サブスクリプション Id とサブスクリプション名 (**-subscriptionname**)。 前の手順からコピーするか、複数のサブスクリプションがある場合は、実行、 **Get-azuresubscription** コマンドレット、結果セットから目的のサブスクリプション情報をコピーします。 ご利用のサブスクリプションを取得したら次のコマンドレットを実行します。

    Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000


## リソース グループ、サーバー、ファイアウォール規則の作成

これでご利用の Azure サブスクリプションに対してコマンドレットを実行する準備ができましたので、次にエラスティック データベース プールを作成するサーバーを含むリソース グループを作成し、そこに複数のデータベースを含めます。 次のコマンドを編集して選択した任意の有効な場所で使用できます。 実行 **(Get AzureRmLocation | where-object {$_ です。名前を付けます-eq"Microsoft.Sql/servers"})。場所** 有効な場所の一覧を取得します。

すでにリソース グループがある場合は次の手順に進みます。次のコマンドを実行して新しいリソース グループを作ることもできます。

    New-AzureRmResourceGroup -Name "resourcegroup1" -Location "West US"

### サーバーの作成 

エラスティック データベース プールは Azure SQL Database サーバーの内部で作成されます。 サーバーがあれば次の手順に進むことができますか、実行、 [新規 AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx) コマンドレットを新しい V12 サーバーを作成します。 ServerName をご利用のサーバー名に置き換えます。 サーバー名が既に使われている場合はエラーが発生する可能性があるため、Azure SQL Server で一意のサーバー名を使用する必要があります。 このコマンドは完了するまでに数分かかる場合があることに注意してください。 サーバーが正常に作成されると、サーバーの詳細と PowerShell のプロンプトが表示されます。 選択した任意の有効な場所で使用するコマンドを編集することができます。

    New-AzureRmSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

入力を求めるこのコマンドを実行すると、ウィンドウが開き、 **ユーザー名** と **パスワード**します。 これは、Azure 資格情報ではありません、ユーザー名を入力し、新しいサーバーを作成する管理者の資格情報となるパスワードします。  


### サーバーのファイアウォール規則を構成し、サーバーへのアクセスを許可します。

ファイアウォール規則を確立してサーバーにアクセスします。 実行、 [新規 AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603586.aspx) コマンド開始と終了 IP アドレス、コンピューターの有効な値です。

他の Azure サービスへのアクセスを許可するように、サーバーが必要な場合は、追加、 **- AllowAllAzureIPs** 特別なファイアウォール規則を追加し、サーバーへのすべての azure トラフィック アクセスを許可するスイッチです。

    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

詳細については、次を参照してください。 [Azure SQL Database ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx)します。


## エラスティック データベース プールとエラスティック データベースの作成

これでリソース グループ、サーバー、ファイアウォール規則が構成され、サーバーにアクセスできるようになりました。  [新規 AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) コマンドレットは、エラスティック データベース プールを作成します。 このコマンドで合計 400 eDTU を共有するプールが作成されます。 プールの各データベースは常に 10 eDTU 使用できることが保証されます (DatabaseDtuMin)。 プールの各データベースは最大 100 eDTU を使用できます (DatabaseDtuMax)。 パラメーターの詳細説明を参照してください。 [Azure SQL Database のエラスティック プール](sql-database-elastic-pool.md)します。 


    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


### エラスティック データベースを作成するか、エラスティック データベース プールに追加する

前の手順で作成したプールはまだ空であり、エラスティック データベースが含まれていません。 このセクションでは、プール内で新しいエラスティック データベース作成する方法と、既存のデータベースをプールに追加する方法を示します。

*プールを作成した後、プールに新しい弾力性データベースを作成して、プールとの間の既存のデータベースの移動の Transact SQL を使用することもできます。 詳細情報を参照してください、 [Transact SQL の弾力性データベース プール参照](sql-database-elastic-pool-reference.md#Transact-SQL)します。*

### エラスティック データベース プールでの新しい エラスティック データベースの作成

プール内で直接新しいデータベースを作成するには、使用、 [新規 AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) コマンドレットを設定し、 **ElasticPoolName** パラメーター。


    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



### 既存のデータベースをエラスティック データベース プールに移動

プールには、既存のデータベースを移動するには、使用、 [セット AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) コマンドレットを設定し、 **ElasticPoolName** パラメーター。 


デモでは、エラスティック データベース プールに含まれていないデータベースを作成します。

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -Edition "Standard"

エラスティック データベース プールで既存のデータベースに移動します。

    Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## エラスティック データベース プールのパフォーマンス設定の変更


    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## エラスティック データベースとエラスティック データベース プールの監視
エラスティック データベース プールにはメトリック レポートが用意されており、複数のデータベースを管理する作業をスケールアウトできます。


### エラスティック データベース プールの操作の状態の取得

作成や更新など、エラスティック データベース プール操作の状態を確認できます。 

    Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


### エラスティック データベースをエラスティック データベース プールに出し入れする際の状態の取得

    Get-AzureRmSqlElasticPoolDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

### エラスティック データベース プールのリソース消費メトリックの取得

リソース プールの制限値のパーセンテージとして取得できるメトリックを以下に示します。   

* 平均 CPU 使用率 - cpu_percent 
* 平均 IO 使用率 - data_io_percent 
* 平均 Log 使用率 - log_write_percent 
* 平均メモリ使用率 - memory_percent 
* 平均 eDTU 使用率 (CPU、IO、Log 使用率の最大値として) - DTU_percent 
* 同時ユーザー リクエストの最大数 (ワーカー) - max_concurrent_requests 
* 同時ユーザー セッションの最大数 - max_concurrent_sessions 
* エラスティック プールの合計ストレージ容量 - storage_in_megabytes 


メトリックの単位と保有期間を以下に示します。

* 5 分間隔でデータが返されます。  
* データは 14 日間保持されます。  


このコマンドレットと API で 1 度の呼び出しで取得できる行数が 1000 行 (5 分間隔で 3 日分のデータ) に制限されます。 ただし、このコマンドは何度も呼び出すことができ、より多くのデータを取得するためにさまざまな開始から終了までの間隔で指定できます。 


次のメトリックを取得します。

    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

呼び出しを繰り返し、次のようなデータを追加することで日数を追加できます。

    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
テーブルの書式を設定します。

    $table = Format-MetricsAsTable $metrics 

CSV ファイルにエクスポートします。

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

### エラスティック データベースのリソース消費メトリックの取得

これらの API は、次のセマンティックな相違点を除き、スタンドアロン データベースのリソース使用率の監視に使用する現在の (V12) API と同じです。 

* 取得されたこの API メトリックは、エラスティック データベース プールの databaseDtuMax (または CPU、IO など基盤となるメトリックと同等の上限) セットのパーセンテージとして表されます。 たとえば、これらのメトリックいずれかの使用率が 50% であることは、特定のリソース消費量が、DB の上限の 50% (親のエラスティック データベース プールで該当するリソースの制限) であることを示しています。 

メトリックを取得します。

    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

呼び出しを繰り返し、次のようなデータを追加することで、必要に応じて日数を追加できます。

    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

テーブルの書式を設定します。

    $table = Format-MetricsAsTable $metrics 

CSV ファイルにエクスポートします。

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## まとめ


    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000
    New-AzureRmResourceGroup -Name "resourcegroup1" -Location "West US"
    New-AzureRmSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"
    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100
    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1" -MaxSizeBytes 10GB
    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 
    
    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## 次のステップ

エラスティック データベース プールを作成した後は、エラスティック ジョブを作成してプール内のエラスティック データベース を管理できます。 エラスティック ジョブはプールのデータベースの任意の数に対して T-SQL スクリプトの実行を容易にします。 詳細については、次を参照してください。 [弾力性データベース ジョブの概要](sql-database-elastic-jobs-overview.md)します。


## エラスティック データベースのリファレンス

エラスティック データベースとエラスティック データベース プールに API とエラーの詳細などの詳細については、次を参照してください。 [弾力性データベース プールのリファレンス](sql-database-elastic-pool-reference.md)します。
