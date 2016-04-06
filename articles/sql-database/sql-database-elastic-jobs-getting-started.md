<properties
    pageTitle="エラスティック データベース ジョブの概要"
    description="エラスティック データベース ジョブの使用方法"
    services="sql-database"
    documentationCenter=""  
    manager="jeffreyg"
    authors="sidneyh"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/01/2015"
    ms.author="sidneyh; ddove" />

# Elastic Database ジョブの概要

Azure SQL Database の Elastic Database ジョブ (プレビュー) を使用すると、複数のデータベースに対して T-SQL スクリプトを確実に実行できます。また、スクリプトは自動的に再試行されるので、最終的な完了が保証されます。 エラスティック データベース ジョブ機能の詳細については、次を参照してください、 [機能の概要ページ](sql-database-elastic-jobs-overview.md)します。

このトピックで使用したサンプルを拡張する [エラスティック データベース ツールの概要](sql-database-elastic-scale-get-started.md)します。 このトピックを読むと、関連するデータベースのグループを管理するジョブを作成し、管理する方法を理解できます。

## 前提条件

ダウンロードし、実行、 [エラスティック データベース ツールのサンプルの概要](sql-database-elastic-scale-get-started.md)します。

## サンプル アプリケーションを使用してシャード マップ マネージャーを作成する

ここでは、シャード マップ マネージャーといくつかのシャードを作成し、シャードにデータを挿入します。 シャードがすでにセットアップされ、シャード データが取り込まれている場合は、以下の手順を省略して、次のセクションに進むことができます。

1. ビルドおよび実行する、 **エラスティック データベース ツールの概要** サンプル アプリケーションです。 手順の手順で 7 まで [をダウンロードして、サンプル アプリケーションを実行](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools)します。 手順 7 を終了すると、次のコマンド プロンプトが表示されます。

    ![コマンド プロンプト][1]

2.  コマンド ウィンドウに「1」のように入力し、キーを押して **Enter**します。 シャード マップ マネージャーが作成され、2 つのシャードがサーバーに追加されます。 入力「3」し、キーを押して **Enter**; この操作を 4 回を繰り返します。 これにより、サンプルのデータ行がシャードに挿入されます。

3.   [Azure ポータル](https://portal.azure.com) 、v12 サーバーに次の 3 つの新しいデータベースを表示する必要があります。

    ![Visual Studio の確認][2]

    この段階で、シャード マップのすべてのデータベースを反映するカスタム データベース コレクションを作成します。 こうすると、シャード全体に新しいテーブルを追加するジョブを作成し、実行できるようになります。

ここでシャード マップを通常作成の対象を使用して、 **新規 AzureSqlJobTarget** コマンドレットです。 シャード マップ マネージャー データベースをデータベース ターゲットとして設定する必要があります。指定したシャード マップがターゲットとして設定されます。 ここでは別の方法を利用し、サーバー内のすべてのデータベースを列挙して、マスター データベース以外のデータベースを新しいカスタム コレクションに追加します。

##カスタム コレクションを作成し、サーバー内のマスター以外のすべてのデータベースをカスタム コレクション ターゲットに追加します。


    $customCollectionName = "dbs_in_server"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 
    $ResourceGroupName = "ddove_samples"
    $ServerName = "samples"
    $dbsinserver = Get-AzureRMSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    $dbsinserver | %{
    $currentdb = $_.DatabaseName 
    $ErrorActionPreference = "Stop"
    Write-Output ""

    Try
    {
       New-AzureSqlJobTarget -ServerName $ServerName -DatabaseName $currentdb | Write-Output
    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason
                
        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already a database target." 
        }

        else
        {
            throw $_
        }
    
    }

    Try
    {
        if ($currentdb -eq "master")
        {
            Write-Host $currentdb "will not be added custom collection target" $CustomCollectionName "."
        }

        else
        {
            Add-AzureSqlJobChildTarget -CustomCollectionName $CustomCollectionName -ServerName $ServerName -DatabaseName $currentdb
            Write-Host $currentdb "was added to" $CustomCollectionName "."
        }

    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already in the custom collection target" $CustomCollectionName"."
        }

        else
        {
            throw $_
        }
    }
    $ErrorActionPreference = "Continue"
}
    
## データベース全体に対して実行する T-SQL スクリプトを作成する

    $scriptName = "NewTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'Test')
    BEGIN
        CREATE TABLE Test(
            TestId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO Test(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

##カスタムのデータベース グループ全体に対してスクリプトを実行するジョブを作成する

    $jobName = "create on server dbs"
    $scriptName = "NewTable"
    $customCollectionName = "dbs_in_server"
    $credentialName = "ddove66"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job


##ジョブを実行する 

既存のジョブを実行するには、次の PowerShell スクリプトを使用できます。

次の変数を使用して、実行する目的のジョブ名を反映します。

    $jobName = "create on server dbs"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution
 
## 1 つのジョブ実行の状態を取得する

同じ使用 **Get AzureSqlJobExecution** コマンドレットと、 **includechildren で** パラメーターの各ジョブの実行、ジョブの対象となる各データベースに対して特定の状態つまり子ジョブの実行の状態を表示します。

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## 複数のジョブ実行全体の状態を確認する

 **Get AzureSqlJobExecution** コマンドレットは、指定されたパラメーターでフィルター処理、複数のジョブの実行を表示するために使用する複数の省略可能なパラメーターです。 次に、Get-AzureSqlJobExecution の使用例を示します。

すべてのアクティブな最上位レベル ジョブ実行を取得します。

    Get-AzureSqlJobExecution

非アクティブなジョブ実行を含む、すべての最上位レベルのジョブ実行を取得します。

    Get-AzureSqlJobExecution -IncludeInactive

非アクティブなジョブ実行を含め、指定したジョブ実行 ID のすべての子ジョブ実行を取得します。

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId –IncludeInactive -IncludeChildren

非アクティブなジョブ実行を含め、スケジュールとジョブの組み合わせを使用して作成したすべてのジョブ実行を取得します。

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

非アクティブなジョブを含め、指定したシャード マップを対象とするすべてのジョブ実行を取得します。

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive

非アクティブなジョブを含め、指定したカスタム コレクションを対象とするすべてのジョブ実行を取得します。

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive
 
指定したジョブ実行内のジョブ タスク実行の一覧を取得します。

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

ジョブ タスク実行の詳細を取得します。

次の PowerShell スクリプトを使用すると、ジョブ タスク実行の詳細を確認できます。実行エラーをデバッグするときに特に役立ちます。

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## ジョブ タスク実行内のエラーを取得します。

JobTaskExecution オブジェクトには、タスクの Lifecycle のプロパティと Message プロパティが含まれています。 ジョブ タスクの実行に失敗した場合、ライフ サイクルのプロパティに設定されます *失敗* し、結果として得られる例外メッセージとスタックにメッセージ プロパティが設定されます。 ジョブが成功しなかった場合、特定のジョブが成功しなかったジョブ タスクの詳細を確認することをお勧めします。

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## ジョブ実行が完了するまで待機する

次の PowerShell スクリプトを使用すると、ジョブ タスクが完了するまで待機することができます。

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## カスタム実行ポリシーを作成する

Elastic Database ジョブは、ジョブの開始時に適用できるカスタム実行ポリシーの作成をサポートしています。
  
現在、実行ポリシーでは以下を定義できます。

* 名前: 実行ポリシーの識別子。
* ジョブのタイムアウト: Elastic Database ジョブによってジョブが取り消されるまでの合計時間。
* 初期再試行間隔: 最初の再試行前に待機する間隔。
* 最大再試行間隔: 使用する再試行間隔の上限。
* 再試行間隔のバックオフ係数: 次回の再試行間隔の計算に使用される係数。  (初期再試行間隔) * Math.pow((間隔のバックオフ係数), (再試行回数) - 2) という式が使用されます。 
* 最大試行回数: 1 つのジョブ内で実行する最大再試行回数。

既定の実行ポリシーでは、次の値を使用します。

* 名前: 既定の実行ポリシー
* ジョブのタイムアウト: 1 週間
* 最初の再試行の間隔: 100 ミリ秒
* 最大再試行間隔: 30 分
* 再試行間隔係数: 2
* 最大試行回数: 2,147, 483,647

必要な実行ポリシーを作成します。

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy

### カスタム実行ポリシーを更新する

更新対象の実行ポリシーを更新します。

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy
 
## ジョブを取り消す

Elastic Database ジョブは、ジョブ取り消し要求をサポートしています。  Elastic Database ジョブで、現在実行されているジョブの取り消し要求が検出されると、ジョブの停止が試行されます。

Elastic Database ジョブには、2 種類の取り消し方法があります。

1. 現在実行中のタスクを取り消す: タスクの実行中に取り消しが検出されると、現在実行中のタスクの範囲内で取り消しが試行されます。  たとえば、取り消しを試行したときに、実行時間が長いクエリが実行中の場合、クエリの取り消しが試行されます。
2. タスクの再試行を取り消す: タスクの実行が開始される前に、コントロール スレッドによって取り消しが検出されると、タスクの開始が回避され、要求は取り消し済みと宣言されます。

親ジョブについてジョブの取り消しが要求されると、親ジョブとそのすべての子ジョブについて、取り消し要求が受け入れられます。
 
キャンセル要求を送信する、 **Stop AzureSqlJobExecution** コマンドレットを設定し、 **JobExecutionId** パラメーター。

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## ジョブの名前と履歴を使用してジョブを削除する

Elastic Database ジョブは、ジョブの非同期削除をサポートしています。 ジョブを削除対象としてマークすることができます。マークされたジョブのすべてのジョブ実行が完了すると、ジョブとそのジョブ履歴は削除されます。 アクティブなジョブ実行は自動的に取り消されません。  

アクティブなジョブ実行を取り消すには、Stop-AzureSqlJobExecution を呼び出す必要があります。

ジョブの削除をトリガーするには、使用、 **削除 AzureSqlJob** コマンドレットを設定し、 **JobName** パラメーター。

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
 
## カスタム データベース ターゲットを作成する
Elastic Database ジョブでは、カスタム データベース ターゲットを定義できます。このターゲットは、直接の実行、またはカスタム データベース グループ内に含まれる実行に使用できます。  **エラスティック データベース プール** はまだ直接サポートされていません、PowerShell Api を介した単純に作成するカスタム データベースのターゲットと、プール内のすべてのデータベースがすべて含まれているデータベースのカスタム コレクションの対象です。

目的のデータベース情報を反映するように、次の変数を設定します。

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## カスタム データベース コレクション ターゲットを作成する
定義済みの複数のデータベース ターゲットに対する実行を有効にするようにカスタム データベース コレクション ターゲットを定義できます。 データベース グループを作成すると、データベースをカスタム コレクション ターゲットに関連付けることができます。

目的のカスタム コレクション ターゲットの構成を反映するように次の変数を設定します。

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### カスタム データベース コレクション ターゲットにデータベースを追加する

データベース ターゲットをカスタム データベース コレクション ターゲットに関連付けて、データベース グループを作成できます。 カスタム データベース コレクション ターゲットを対象とするジョブが作成されると、ジョブの実行時に、対象がそのグループに関連付けられているデータベースにまで拡張されます。

目的のデータベースを特定のカスタム コレクションに追加します。

    $serverName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### カスタム データベース コレクション ターゲット内のデータベースを確認する

使用して、 **Get AzureSqlJobTarget** コマンドレットをその子内のデータベース、データベースのカスタム コレクションの対象を取得します。 
 
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### カスタム データベース コレクション ターゲット全体に対してスクリプトを実行するジョブを作成する

使用して、 **新規 AzureSqlJob** コマンドレットをデータベースのカスタム コレクションの対象が定義されているデータベースのグループに対してジョブを作成します。 Elastic Database ジョブによって、カスタム データベース コレクション ターゲットに関連付けられているデータベースに対応する複数の子ジョブにまでジョブの対象が拡張されるので、各データベースに対して確実にスクリプトが実行されます。 ここでも、スクリプトをべき等にして、再試行に対して回復力を持たせるようにすることが重要です。

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## データベース全体のデータ収集

**弾力性データベース ジョブ** グループ全体のデータベースのクエリの実行をサポートし、指定されたデータベースのテーブルに結果を送信します。 その結果の後にテーブルに対してクエリを実行して、各データベースのクエリ結果を確認することができます。 この方法で、多数のデータベース全体に対してクエリを実行する非同期メカニズムを実現できます。 データベースのいずれかが一時的に使用できないなど、失敗が発生した場合、再試行で自動的に処理されます。

指定した対象テーブルは、返される結果セットのスキーマに対応する既存のテーブルがない場合、自動的に作成されます。 スクリプト実行から複数の結果セットが返された場合、Elastic Database ジョブからは、1 つの結果セットのみが指定した対象テーブルに送信されます。

次の PowerShell スクリプトを使用すると、指定したテーブルに結果を収集するスクリプトを実行できます。 このスクリプトは、単一の結果セットを出力する T-SQL スクリプトが作成済みで、カスタム データベース コレクション ターゲットが作成済みであると想定しています。

目的のスクリプト、資格情報、および実行対象を反映するように、以下を設定します。

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $executionCredentialName = "{Execution Credential Name}"
    $customCollectionName = "{Custom Collection Name}"
    $destinationCredentialName = "{Destination Credential Name}"
    $destinationServerName = "{Destination Server Name}"
    $destinationDatabaseName = "{Destination Database Name}"
    $destinationSchemaName = "{Destination Schema Name}"
    $destinationTableName = "{Destination Table Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName

### データ収集シナリオのジョブを作成して開始する
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## ジョブ トリガーを使用してジョブ実行のスケジュールを作成する

次の PowerShell スクリプトを使用すると、繰り返し実行されるスケジュールを作成できます。 このスクリプトでは 1 分間隔を使用していますが、New-AzureSqlJobSchedule は -DayInterval、-HourInterval、-MonthInterval、-WeekInterval の各パラメーターもサポートしています。 1 回だけ実行するスケジュールを作成するには、-OneTime を渡します。

新しいスケジュールを作成します。

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime 
    Write-Output $schedule

### 時間スケジュールに従ってジョブを実行するようにジョブ トリガーを作成する

時間スケジュールに従ってジョブを実行するようにジョブ トリガーを定義できます。 次の PowerShell スクリプトを使用すると、ジョブ トリガーを作成できます。

目的のジョブとスケジュールに対応するように次の変数を設定します。

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName –JobName $jobName
    Write-Output $jobTrigger

### スケジュールされた関連付けを解除してジョブのスケジュール実行を停止する

ジョブ トリガーによるジョブ実行の繰り返しを停止するには、ジョブ トリガーを削除します。 
使用してスケジュールに従って実行されるジョブを停止するジョブ トリガーを削除、 **削除 AzureSqlJobTrigger** コマンドレットです。

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName





## エラスティック データベース クエリの結果を Excel にインポートする

 クエリの結果は Excel ファイルにインポートすることができます。

1. Excel 2013 を起動します。
2.  移動し、 **データ** リボンです。
3.  クリックして **その他のソース** ] をクリック **SQL Server から**します。

    ![他のソースから Excel へのインポート][5]
4.   **データ接続ウィザード** サーバー名とログインの資格情報を入力します。 クリックして **次**します。
5.  ダイアログ ボックスで **データを含むデータベースを選択して**, を選択、 **ElasticDBQuery** データベースです。
6.  選択、 **顧客** リスト ビューでテーブルを **次**します。 クリックして **完了**します。
7.   **データのインポート** フォームの **ブックでこのデータを表示する方法を選択**, [ **テーブル** ] をクリック **[ok]**します。

すべての行を **顧客** さまざまなシャードに格納されているテーブルが Excel シートを作成します。

## 次のステップ
これで Excel の強力なデータ機能を使用できるようになりました。 サーバー名、データベース名、および資格情報が含まれる接続文字列を使用して、BI とデータ統合ツールをエラスティック クエリ データベースに接続することができます。 ご使用のツールのデータ ソースとして SQL Server がサポートされていることを確認してください。 エラスティック クエリ データベースおよび外部テーブル (ツールを使用して接続するその他の SQL Server データベースおよび SQL Server テーブルのような) を参照できます。

### コスト
Elastic Database クエリ機能を使用する場合に追加の料金は発生しません。 ただし、この時点で、この機能はエンド ポイントとして動作するプレミアム データベースでしか利用できません。ただし、シャードのサービス階層に制限はありません。

料金情報については、次を参照してください。 [SQL Database の料金詳細](http://azure.microsoft.com/pricing/details/sql-database/)します。


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->


