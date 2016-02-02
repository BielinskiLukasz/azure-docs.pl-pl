<properties
   pageTitle="SQL Data Warehouse での同時実行とワークロード管理 | Microsoft Azure"
   description="ソリューション開発のための Azure SQL Data Warehouse での同時実行とワークロード管理を理解します。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor="jrowlandjones"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>


# SQL Data Warehouse での同時実行とワークロード管理

SQL Data Warehouse では、予測可能なパフォーマンスを広く提供するために、ワークロードの同時実行とコンピューティング リソースの割り当ての両方を管理するメカニズムを実装しています。

この記事では、同時実行とワークロード管理の機能がどのように実装されたか、そしてこれらの機能をデータ ウェアハウスでどのように制御できるかについて説明しながら、両機能の概念を紹介します。

## 同時実行

SQL Data Warehouse が、**同時実行クエリ**と**同時実行スロット**の 2 つの概念によって管理されていることを理解することは重要です。

同時実行クエリは、同時に実行されるクエリの数を指します。 SQL Data Warehouse では最大 32 の**同時実行クエリ**がサポートされています。 各クエリの実行は、直列クエリ  (単一スレッド) または並列クエリ (マルチスレッド) かどうかに関わらず、1 つのクエリとしてカウントされます。 これは固定された上限数であり、すべてのサービス レベルとクエリに適用されます。

同時実行スロットはさらに動的な概念であり、データ ウェアハウスの Data Warehouse ユニット (DWU) サービス レベル目標に対して相対的です。 SQL Data Warehouse に割り当てられている DWU 数を増やすと、コンピューティング リソースが追加で割り当てられます。 ただし、DWU を増やすと、使用可能な**同時実行スロット**の数も増加します。

原則として、同時実行の各クエリでは、1 つ以上の同時実行スロットが使用されます。 スロットの正確な使用数は次の 3 つの要因によって異なります。

1. SQL Data Warehouse の DWU 設定
2. ユーザーが属する**リソース クラス**
3. クエリまたは操作が同時実行スロット モデルによって管理されるかどうか

> [AZURE.NOTE] すべてのクエリが同時実行スロットのクエリ規則によって管理されるわけではありません。 ただし、ほとんどのユーザーのクエリに適用されます。 クエリや操作によっては、同時実行スロットをまったく消費しません。 ただし、これらのクエリや操作は同時実行クエリの上限によって制限されているため、両方のルールについて説明しています。 参照してください、 [リソース クラスの例外](#exceptions) 詳細については後述します。 

次の表は、同時実行クエリと同時実行スロットの両方の上限を示しています。この表では、クエリのリソースが管理されていることを想定しています。



| 同時実行スロットの使用| DW100| DW200| DW300| DW400| DW500| DW600| DW1000| DW1200| DW1500| DW2000|
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- |
| 同時クエリの最大数| 32| 32| 32| 32| 32| 32| 32| 32| 32| 32|
| 同時実行スロットの最大数| 4| 8| 12| 16| 20| 24| 40| 48| 60| 80|

SQL Data Warehouse のクエリ ワークロードは、これらのしきい値範囲内で稼働する必要があります。 同時実行クエリの数が 32 を超えたり、同時実行スロットの数が超過した場合は、両方のしきい値が満たされるまで、クエリはキューに配置されます。

## ワークロード管理

SQL Data Warehouse では、ワークロード管理の実装の一環として、4 つの異なるリソース クラスを**データベース ロール**の形式で公開しています。

これらのロールを次に示します。

- smallrc
- mediumrc
- largerc
- xlargerc

リソース クラスは SQL Data Warehouse のワークロード管理に不可欠です。 これらは、クエリに割り当てられたコンピューティング リソースを管理します。

既定では、各ユーザーは小規模リソース クラス (smallrc) のメンバーです。 ただし、いずれのユーザーも、より高いレベルの 1 つ以上のリソース クラスに追加できます。 原則として、SQL Data Warehouse では、最高レベルのロールのメンバーシップをクエリ実行します。 ユーザーをより高いレベルのリソース クラスに追加すると、そのユーザーのリソースが増加しますが、使用される同時実行スロット数も増えます。そのため、同時実行が制限されてしまう可能性があります。 この理由は、1 つのクエリに割り当てるリソースが増えると、他のクエリに使用されるリソースをシステムが制限する必要があるからです。 無限に使用することはできません。

高いレベルのリソース クラスによって管理される最も重要なリソースは、メモリです。 実用的なあるあらゆる規模のデータ ウェアハウスのテーブルのほとんどでは、クラスター化列ストア インデックスが使用されます。 これによって、データ ウェアハウスのワークロード処理で最高のパフォーマンスを発揮できますが、これらのインデックスを維持するにはメモリ使用量が増大します。 インデックスの再構築などのデータ管理操作には、より高いリソース クラスを使用するとメリットがある場合が多いです。

SQL Data Warehouse には、データベース ロールを使用してリソース クラスが実装されています。 高いレベルのリソース クラスのメンバーになり、メモリを増やして優先順位を上げるには、単にデータベース ユーザーを前述のいずれかのロールやリソース クラスに割り当てるだけです。

### リソース クラスのメンバーシップ

リストにログインを追加したり、自分で、ワークロード管理ロールを使用してデータベースへの削除、 `sp_addrolemember` と `sp_droprolemember` プロシージャです。 注意が必要になります `ALTER ROLE` これを行うアクセス許可。 ALTER ROLE DDL 構文は使用できません。 前述のストアド プロシージャを使用する必要があります。 この記事の最後のユーザーの管理セクションで、ログインとユーザーを作成する方法について、完全な例を示します。
> [AZURE.NOTE] ワークロード管理グループにユーザーを追加したり削除したりするより、より高いリソース クラスに永続的に割り当てられた独立したログインまたはユーザーを使用して、多くのメモリを必要とする操作を開始するほうが簡単な場合があります。

### メモリの割り当て

次の表では、各クエリで使用可能なメモリの増加を示しています。これは、メモリ増加を実行するユーザーに適用されているリソース クラスに影響を受けます。





| 使用可能なメモリ (ディスクあたり)| DW100| DW200| DW300| DW400| DW500| DW600| DW1000| DW1200| DW1500| DW2000|
| :-------------------------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ |
| smallrc (既定) (s)| 100 MB| 100 MB| 100 MB| 100 MB| 100 MB| 100 MB| 100 MB| 100 MB| 100 MB| 100 MB|
| mediumrc (m)| 100 MB| 200 MB| 200 MB| 400 MB| 400 MB| 400 MB| 800 MB| 800 MB| 800 MB| 1600 MB|
| largerc (l)| 200 MB| 400 MB| 400 MB| 800 MB の領域| 800 MB| 800 MB| 1600 MB| 1600 MB| 1600 MB| 3200 MB|
| xlargerc (xl)| 400 MB| 800 MB| 800 MB| 1600 MB| 1600 MB| 1600 MB| 3200 MB| 3200 MB| 3200 MB| 6400 MB|

### 使用される同時実行スロット数

さらに、先に説明したように、ユーザーに割り当てられたリソース クラスが高いレベルであればあるほど、同時実行スロットの使用数は増加します。 次の表は、指定されたリソース クラスのクエリで使用される同時実行スロットを示しています。



| 消費量| DW100| DW200| DW300| DW400| DW500| DW600| DW1000| DW1200| DW1500| DW2000|
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- |
| 同時クエリの最大数| 32| 32| 32| 32| 32| 32| 32| 32| 32| 32|
| 同時実行スロットの最大数| 4| 8| 12| 16| 20| 24| 40| 48| 60| 80|
| smallrc (既定) (s)| 1| 1| 1| 1| 1| 1| 1| 1| 1| 1|
| mediumrc (m)| 1| 2| 2| 4| 4| 4| 8| 8| 8| 16|
| largerc (l)| 2| 4| 4| 8| 8| 8| 16| 16| 16| 32|
| xlargerc (xl)| 4| 8| 8| 16| 16| 16| 32| 32| 32| 64|

### 例外

高いレベルのリソース クラスのメンバーシップでは、クエリまたは操作に割り当てられたリソースが変更されない場合があります。 これは通常、アクションの実行に必要なリソースが不足している場合に発生します。 このような場合、ユーザーに割り当てられたリソースのクラスに関係なく、既定値または小規模リソース クラス (smallrc) が常に使用されます。 たとえば、 `CREATE LOGIN` smallrc で常に実行されます。 この操作の実行に必要なリソースが非常に低い場合、同時実行スロット モデルにクエリを含めても効果はありません。 この処理に大量のメモリを事前に割り当てるのは無駄になります。 除外することで `CREATE LOGIN` 同時実行スロット モデルからは、SQL Data Warehouse をはるかに効率的にすることができます。

リソース クラスによって**管理される**ステートメントと操作の一覧を次に示します。

- INSERT-SELECT
- UPDATE
- 削除
- SELECT (ユーザー テーブルのクエリを実行する場合)
- ALTER INDEX REBUILD
- ALTER INDEX REORGANIZE
- ALTER TABLE REBUILD
- CREATE INDEX
- CREATE CLUSTERED COLUMNSTORE INDEX
- CREATE TABLE AS SELECT
- データの読み込み
- Data Movement Service (DMS) によって実行されるデータ移動操作

次のステートメントでは、リソース クラスは**考慮されません。**

- CREATE TABLE
- ALTER TABLE ...SWITCH PARTITION
- ALTER TABLE ...SPLIT PARTITION
- ALTER TABLE ...MERGE PARTITION
- DROP TABLE
- ALTER INDEX DISABLE
- DROP INDEX
- CREATE STATISTICS
- UPDATE STATISTICS
- DROP STATISTICS
- TRUNCATE TABLE
- ALTER AUTHORIZATION
- CREATE LOGIN
- CREATE USER
- ALTER USER
- DROP USER
- CREATE PROCEDURE
- ALTER PROCEDURE
- DROP PROCEDURE
- CREATE VIEW
- DROP VIEW
- INSERT VALUES
- SELECT (システム ビューと DMV から)
- EXPLAIN
- DBCC



> [AZURE.NOTE] 強調表示されて `選択` 動的管理ビューおよびカタログ ビューに対してのみ実行するクエリは、 **いない** リソース クラスによって管理されます。

エンド ユーザーのクエリの大半がリソース クラスで管理される可能性が高いことにご注意ください。 一般的に、処理中のクエリのワークロードは、プラットフォームで特別に除外されている場合を除き、同時実行クエリと同時実行スロットの両方のしきい値内に収まる必要があります。 エンド ユーザーは、同時実行スロット モデルからクエリ除外を選択することはできません。 いずれかのしきい値を超えたクエリは、キューに配置されます。 キューに配置されたクエリは、まず優先順位に従って、次に送信時刻に従って処理されます。

### 内部

SQL Data Warehouse のワークロード内部では、管理はもう少し複雑になります。 リソース クラスは、リソース ガバナー内の一般的なワークロード管理グループ セットに動的にマッピングされます。 使用されるグループは、ウェアハウスの DWU 値によって異なります。 ただし、SQL Data Warehouse で使用されるワークロード グループは合計 8 個あります。 次に例を示します。

- SloDWGroupC00
- SloDWGroupC01
- SloDWGroupC02
- SloDWGroupC03
- SloDWGroupC04
- SloDWGroupC05
- SloDWGroupC06
- SloDWGroupC07

使用される同時実行スロットには、この 8 個のグループが対応します。

| ワークロード グループ| 同時実行スロットのマッピング| 優先順位のマッピング|
| :------------  | :----------------------- | :--------------- |
| SloDWGroupC00| 1| 中|
| SloDWGroupC01| 2| 中|
| SloDWGroupC02| 4| 中|
| SloDWGroupC03| 8| 中|
| SloDWGroupC04| 16| 高|
| SloDWGroupC05| 32| 高|
| SloDWGroupC06| 64| 高|
| SloDWGroupC07| 128| 高|

たとえば、DW500 が、SQL Data Warehouse の現在の DWU 設定である場合、アクティブなワークロード グループが次のようにリソース クラスにマッピングされます。

| リソース クラス| ワークロード グループ| 使用される同時実行スロット数| [重要度]|
| :------------- | :------------- | :---------------------   | :--------- |
| smallrc| SloDWGroupC00| 1| 中|
| mediumrc| SloDWGroupC02| 4| 中|
| largerc| SloDWGroupC03| 8| 中|
| xlargerc| SloDWGroupC04| 16| 高|

リソース ガバナーの観点からメモリ リソースの割り当ての違いを詳細に確認するには、次のクエリを使用します。

```
WITH rg
AS
(   SELECT  pn.name                                 AS node_name
    ,       pn.[type]                               AS node_type
    ,       pn.pdw_node_id                          AS node_id
    ,       rp.name                                 AS pool_name
    ,       rp.max_memory_kb*1.0/1024               AS pool_max_mem_MB
    ,       wg.name                                 AS group_name
    ,       wg.importance                           AS group_importance
    ,       wg.request_max_memory_grant_percent     AS group_request_max_memory_grant_pcnt
    ,       wg.max_dop                              AS group_max_dop
    ,       wg.effective_max_dop                    AS group_effective_max_dop
    ,       wg.total_request_count                  AS group_total_request_count
    ,       wg.total_queued_request_count           AS group_total_queued_request_count
    ,       wg.active_request_count                 AS group_active_request_count
    ,       wg.queued_request_count                 AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                    AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn                                     ON  wg.pdw_node_id  = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
    AND     rp.name = 'SloDWPool'
) 
SELECT  pool_name
,       pool_max_mem_MB
,       group_name
,       group_importance
,       (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,       node_name
,       node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY 
    node_name
,   group_request_max_memory_grant_pcnt
,   group_importance
;
```

> [AZURE.NOTE] 上記のクエリを使用すると、トラブルシューティングを行うときに、ワークロード グループのアクティブな使用状況と過去の使用状況を分析することもできます。 

## ワークロード管理の例

このセクションでは、ユーザーの管理と、キューに格納されるクエリの検出について、いくつかの例を使用して説明します。

### ユーザーの管理

ユーザーに SQL Data Warehouse へのアクセスを許可するには、まずログインする必要があります。

SQL Data Warehouse のマスター データベースへの接続を開き、次のコマンドを実行します。

```
CREATE LOGIN newperson WITH PASSWORD = 'mypassword'

CREATE USER newperson for LOGIN newperson
```

[AZURE です。[メモ] Azure SQL database と SQL Data Warehouse の両方を使用する場合、マスター データベースでログイン用のユーザーを作成することをお勧めします。 このレベルでは 2 つのサーバー ロールを利用できます。これらのロールは、メンバーシップを許可するためには、ログインがマスターにユーザーを持つ必要があります。 この役割は `Loginmanager` と `dbmanager`します。 これらのロールは、Azure SQL Database と SQL Data Warehouse の両方で、ログインを管理し、データベースを作成する権限を付与します。 この点が SQL Server とは異なります。 詳細については、[データベースの管理と Azure SQL Database 内のログイン] をご覧ください詳細については資料です。

ログインを作成したら、ユーザー アカウントを追加する必要があります。

SQL Data Warehouse データベースへの接続を開き、次のコマンドを実行します。

```
CREATE USER newperson FOR LOGIN newperson
```

ユーザーに対して、1 回完全なアクセス許可が付与される必要があります。 付与次の例 `コントロール` SQL Data Warehouse データベースにします。 `コントロール` データベース レベルは、の db_owner SQL Server に相当します。

```
GRANT CONTROL ON DATABASE::MySQLDW to newperson
```

ワークロード管理ロールを確認するには、次のクエリを使用します。

```
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

増加ワークロード管理ロールにユーザーを追加するには、次のクエリを使用します。

``` 
EXEC sp_addrolemember 'largerc', 'newperson' 
```

ワークロード管理ロールからユーザーを削除するのには、次のクエリを使用します。

``` 
EXEC sp_droprolemember 'largerc', 'newperson' 
```

> [AZURE.NOTE] smallrc からユーザーを削除することはできません。

どのユーザーが特定のロールのメンバーであるかを確認するには、次のクエリを使用します。

```
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id     = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id   = m.principal_id
WHERE   r.name IN ('mediumrc','largerc', 'xlargerc')
;
```

### キューに置かれたクエリの検出

常に参照することができます、同時実行キューに配置されているクエリを識別するために、 `sys.dm_pdw_exec_requests` DMV。

```
SELECT   r.[request_id]                                 AS Request_ID
        ,r.[status]                                     AS Request_Status
        ,r.[submit_time]                                AS Request_SubmitTime
        ,r.[start_time]                                 AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time])        AS Request_InitiateDuration_ms
        ,r.resource_class                               AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

SQL Data Warehouse には、同時実行を評価するための特定の待機の種類があります。

次に例を示します。

- LocalQueriesConcurrencyResourceType
- UserConcurrencyResourceType
- DmsConcurrencyResourceType
- BackupConcurrencyResourceType

LocalQueriesConcurrencyResourceType は、同時実行スロットのフレームワークの外に配置されたクエリを示します。 DMV クエリおよびシステムなどの関数 `SELECT @@VERSION` ローカル クエリの例を示します。

UserConcurrencyResourceType は、同時実行スロットのフレームワーク内に配置されたクエリを示します。 エンドユーザー テーブルに対するクエリは、このリソースの種類を使用した例です。

DmsConcurrencyResourceType は、データ移動操作に起因する待機を示します。

BackupConcurrencyResourceType は、データベースのバックアップ実行中に表示されます。 この種類のリソースの最大値は 1 です。 同時に複数のバックアップが要求された場合は、他の要求はキューに配置されます。


要求は、どのようなリソースを検索する現在キューに置かれたクエリの分析を実行するを待つを参照してください、 `sys.dm_pdw_waits` DMV。

```
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                        AS Current_session
,       s.[status]                                          AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                         AS Request_command
,       r.[label]
,       r.[status]                                          AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])         AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])      AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE   w.[session_id] <> SESSION_ID()
;
```

特定のクエリを参照することによって使用されるリソースの待機のみを表示する、 `sys.dm_pdw_resource_waits` DMV。 リソースの待機時間では、リソースが提供されるまでの時間のみが考慮されますが、シグナルの待機時間は、基本の SQL Server リソースに対するクエリが CPU にスケジュール設定されるまでの時間です。

```
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE   [session_id] <> SESSION_ID()
;
```

最後に、傾向を分析を待機し、SQL data Warehouse の提供、 `sys.dm_pdw_wait_stats` DMV。

```
SELECT  w.[pdw_node_id]
,       w.[wait_name]
,       w.[max_wait_time]
,       w.[request_count]
,       w.[signal_time]
,       w.[completed_count]
,       w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## 次のステップ

他の開発のヒントを参照してください。 [開発の概要 []][]します。












[development overview]: sql-data-warehouse-overview-develop.md 
[managing databases and logins in azure sql database]: https://msdn.microsoft.com/library/azure/ee336235.aspx 

