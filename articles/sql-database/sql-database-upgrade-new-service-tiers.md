<properties 
    pageTitle="SQL Database の Web/Business データベースを新しいサービス階層にアップグレードする" 
    description="Azure SQL Database の Web または Business データベースを新しい Azure SQL Database の Basic、Standard、および Premium サービス階層とパフォーマンス レベルにアップグレードします。" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags 
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/08/2015" 
    ms.author="sstein" 
    ms.workload="data-management" 
    ms.topic="article" 
    ms.tgt_pltfrm="NA"/>


# SQL Database の Web/Business データベースを新しいサービス階層にアップグレードする

Azure SQL [Web と Business データベースの使用を中止](sql-database-web-business-sunset-faq.md) ように既存の Web または Business データベースをアップグレードするときは、 [Basic、Standard、Premium、または弾力性のサービス階層](sql-database-service-tiers.md)します。


> [AZURE.IMPORTANT] Web または Business データベースを新しいサービス階層にアップグレードするはないデータベースはオフラインです。 データベースは、アップグレード操作中もオンライン状態であり使用できます。 


アップグレードする際、SQL Database サービスによって、各データベースに適したサービス層とパフォーマンス レベル (価格レベル) が推奨されます。 サービスは、各データベースの使用履歴を分析することによって、既存のデータベースのワークロードを実行するために最適なレベルを推奨します。 

各データベースに推奨される価格レベルは、Web または Business データベース サービス階層の変更処理中、または SQL Database V12 へのアップグレード中に提供されます。

一部またはすべてのデータベースにアップグレードする、特定の環境に応じて、サービスが推奨場合があります、 [弾力性データベース プール](sql-database-elastic-pool.md)します。

インベントリから削除されたデータベースの推奨サービス階層を表示するには、使用することができます、 [Azure ポータル](https://portal.azure.com) または PowerShell。 詳細な手順は以下を参照してください。

- [SQL Database V12 にアップグレードする (Azure ポータル)](sql-database-v12-upgrade.md)
- [SQL Database V12 にアップグレードする (PowerShell)](sql-database-upgrade-server.md)


注意する必要がある重要なこととして、SQL Database は特定のサービス階層やパフォーマンス レベルに固定されるわけではありません。そのため、データベースの要件の変化に応じて、使用可能なサービス階層とパフォーマンス レベル間での変更が簡単に行えます。 実際、Basic、Standard、Premium の SQL Database は時間単位で課金され、各データベースは 24 時間に 4 回スケールアップまたはスケールダウンすることができます。 つまり、アプリケーションの要件と変化するワークロードに基づいて、データベースのパフォーマンス ニーズ、機能セット、コストが最適化されるようにサービス階層とパフォーマンス レベルを調整できます。 また、データベースのサービス階層とパフォーマンス レベルの評価と変更 (スケールアップとスケールダウン) は継続的なプロセスであり、スケジュールされたメンテナンスとパフォーマンス チューニング ルーチンの一部とする必要があります。
 
Web または Business と新しいサービス階層での違いの詳細については、追加の移行の詳細については、ダウンロード、 [Web および Business Database Migration Guidance Cookbook](http://download.microsoft.com/download/3/C/9/3C9FF3D8-E702-4F5D-A38C-5EFA1DBA73E6/Azure_SQL_Database_Migration_Cookbook.pdf)します。



## 概要

<p> Azure Web と Business SQL データベースは、データベースの予約済みリソース容量なしで、共有のマルチテナント環境で動作します。 この共有リソース環境内の他のデータベースのアクティビティによってパフォーマンスが影響を受けることがあります。 特定の時点でのリソースの可用性は、システムで同時に実行されている他のワークロードに大きく依存します。 そのため、データベース アプリケーションのパフォーマンスはさまざまに変化し、予想が不可能です。 お客様からは、この予測不可能なパフォーマンスは管理が難しく、より予測可能なパフォーマンスを希望するとの声をいただきました。 

このフィードバックに対処するには、Azure SQL Database サービスは新しいデータベース サービス階層を導入しました。 [(Basic、Standard および Premium)](sql-database-service-tiers.md), 、予測可能なパフォーマンスやさまざまなビジネス継続性とセキュリティの新機能を提供します。 これらの新しいサービス階層では、他の顧客がその環境内で実行する他のワークロードに関係なく、データベースのワークロードに対して指定したレベルのリソースが提供できます。 これによりパフォーマンス動作の高度な予測が可能になります。 

これらの変更に伴い、現在の Web と Business (W/B) データベースに最適な新しいサービス階層を評価して決定するにはどうしたらよいか、実際のアップグレード処理はどのようになるのかと疑問に思われるかもしれません。

最終的には、機能、パフォーマンス、コストの最適なバランスを実現しながらビジネス ニーズとアプリケーションの要件を完全に満たすようなサービス階層とパフォーマンス レベルの組み合わせが、最適な選択です。

このホワイトペーパーでは、Web または Business データベースを新しいいずれかのサービス階層/パフォーマンス レベルにアップグレードするための方法をガイド付きで紹介します。


## Web と Business データベースのアップグレード

Web または Business データベースを新しいサービス階層/パフォーマンス レベルにアップグレードするために、データベースをオフラインにする必要はありません。 アップグレード処理中もデータベースを引き続き利用できます。 新しいパフォーマンス レベルに実際に切り替えるときには、データベースへの接続が非常に短い時間 (通常は数秒)、一時的に解除される場合があります。 接続が終了した場合に発生する一時的なエラーに対処するための機能がアプリケーションに備わっている場合は、アップグレードの終了時の接続解除に対して保護するだけで十分です。 

Web または Business データベースを新しいサービス階層にアップグレードするには、次の手順が必要になります。


1. 機能に基づいてサービス階層を決定する
2. 過去のリソース使用率に基づいて、許容可能なパフォーマンス レベルを決定する
3. Web または Business データベースの現在のパフォーマンスが、より高い Premium レベルに対応する理由を確認する
4. 低いパフォーマンス レベルに合わせてワークロードを調整する
5. *新しいサービス階層/パフォーマンス レベルにアップグレードする*
6. 新しいサービス階層/パフォーマンス レベルへのアップグレードを監視する
7. アップグレード後のデータベースを監視する



## 1.機能に基づいてサービス階層を決定する

Basic、Standard、Premium のサービス階層はそれぞれ異なる機能セットを提供します。適切な階層を選択するための最初の手順は、アプリケーションとビジネスに必要な最小レベルの機能を提供するサービス階層を特定することです。 

たとえば、長時間のバックアップを保持する必要があるかどう [標準またはアクティブなジオレプリケーション](sql-database-business-continuity.md) 機能が必要か、必要な全体の最大データベース サイズなどです。このような要件によって、最小限のサービス階層として選択する階層が決定します。

非常に小さな、アクティビティの少ないデータベースの場合は、主に "Basic" 階層が使用されます。 そのため、サービス階層を選択するときは、通常は使用する機能に基づいて最初に "Standard" 階層または "Premium" 階層を選択します。

次の表では、新しいサービス階層の機能とパフォーマンス レベルをまとめて比較しています。

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

**サービス階層とパフォーマンス レベルを比較するためのその他のリソース:**

| 記事 | 説明 |
|:--|:--|
|[Azure SQL データベースのサービス階層とパフォーマンス レベル](sql-database-service-tiers.md)| 各サービス階層の概要、メトリック、機能 (およびクラシック ポータルで DMV を使用してデータベースの使用率を監視する方法)。 |
|[Azure SQL Database のビジネス継続性](sql-database-business-continuity.md)|それぞれのサービス階層のビジネス継続性と障害復旧機能 (特定の時点への復元、地理的復元、geo レプリケーション) の詳細。||
|[SQL データベースの料金](http://azure.microsoft.com/pricing/details/sql-database/)|それぞれのサービス階層とパフォーマンス レベルの詳細な価格の情報。|

<br>

データベースの要件を満たす適切なサービス階層を選択したら、次のステップは、実際のデータベースのワークロードに対して許容可能なパフォーマンス レベルを選択することです。 



## 2.過去のリソース使用率に基づいて、許容可能なパフォーマンス レベルを決定する

Azure SQL Database サービスでは、クラシック ポータルとシステム ビューに情報が表示され、既存の Web または Business データベースに相当する新しいサービス階層とパフォーマンス レベルが提示されます。

Web と Business データベースには保証された DTU/リソース制限が関連付けられていないため、S2 パフォーマンス レベルのデータベースで利用可能なリソースの量で、パーセンテージの値を正規化します。 特定の間隔におけるデータベースの DTU 消費の平均の割合は、その間隔における CPU、IO、ログ使用状況の中で最も高いパーセント値として計算されます。


Azure ポータルを使用して DTU 使用割合の概要を表示し、システム ビューを使用して詳細を確認します。 

また、Azure SQL Database V12 にサーバーをアップグレードする際に、Azure ポータルを使用して、Web または Business データベースの推奨されるサービス階層を表示することもできます。

### Azure ポータルで推奨されるサービス階層を表示する方法
Azure ポータルでは、SQL Database V12 へのサーバーのアップグレード プロセス時に、Web または Business データベースに適したサービス階層が推奨されます。 この推奨事項は、データベースのリソース消費の履歴分析に基づくものです。

**新しい管理ポータル**

1. ログオン、 [Azure ポータル](https://portal.azure.com) し、Web または Business データベースを含むサーバーを参照します。
2. クリックして、 **最新の更新プログラム** サーバー ブレードの一部です。
3. クリックして **このサーバーをアップグレード**します。

 **このサーバーをアップグレード** ブレードは、推奨サービス階層と共に、サーバーに Web または Business データベースの一覧を表示するようになりました。



### 管理ポータルで DTU 使用量を表示する方法
管理ポータルでは、既存の Web または Business データベースの DTU 使用量を詳細に把握できます。
DTU 情報は現在の Azure ポータルで利用可能です。


**クラシック ポータル**

1. ログオン、 [旧ポータル](https://manage.windowsazure.com) し既存の Web または Business データベースに移動します。
2. クリックして、 **モニター** ] タブをクリックします。
3. クリックして **メトリックの追加**します。
4. 選択 **DTU 割合** 確認の下部にあるチェック マークをクリックします。

表示することを確認したら、 **DTU 割合** テーブル内のデータです。 これは、Standard(S2) レベルのデータベースの DTU (50 DTU) と比較した割合です。

また、このデータは、5 分ごとに取得されたサンプルの平均であることに注意してください。そのため、サンプルとサンプルの間に発生した短期間のアクティビティの急増がメトリックに反映されないことがあります。

![DTU 割合のデータ][2]

上記の例のデータは、およそ 10 DTU (50 の 19.23%) の平均使用量、28 DTU (55.83% x 50) の最大 DTU 割合を示しています。 
このデータが典型的なワークロードを表すと想定した場合、最初のアップグレードでは Standard(S1) を選択することになります。 Standard(S0) では、平均使用量である 10 DTU が提供されますが、データベースが平均で 100% の容量で実行されることを意味するため、適切な計画ではありません。 平均使用量からは S1 が適切な選択であると思われますが、最大使用量に達した場合は、どうなるでしょうか。 夜間のメンテナンス処理によって使用量が急増することがわかっていますが、実際にユーザーの使用量は影響を受けないため、メンテナンス中のパフォーマンス低下は許容できると判断します。 しかし、いつ最大使用量に達するかはわからないので、DTU 使用割合についてはさらに分析が必要です。

データベースのリソース消費について詳細を確認するには、提供されるシステム ビューを使用できます。


### システム ビュー


Web および Business のデータベース リソース消費データは、 [sys.resource_stats](http://msdn.microsoft.com/library/azure/dn269979.aspx) 、現在のデータベースが存在する論理サーバーの master データベースで表示します。 パフォーマンス レベルの制限に占める割合で、リソース消費データが表示されます。 このビューでは、最大で過去 14 日分のデータを 5 分間隔で表示できます。  

> [AZURE.NOTE] 使用できるように、 [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) Web および Business データベースのリソース消費データの上位の粒度ビュー (15 秒ごと) で表示します。 sys.dm_db_resource_stats では 1 時間の履歴データのみを保持するため、1 時間ごとにこの DMV を照会し、さらに分析するためにデータを格納できます。

データベースの平均 DTU 消費を取得するには、マスター データベースで次のクエリを実行します。

 
                   
     SELECT start_time, end_time
     ,(SELECT Max(v)
         FROM (VALUES (avg_cpu_percent)
                    , (avg_physical_data_read_percent)
                    , (avg_log_write_percent)
           ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.resource_stats
    WHERE database_name = '<your db name>'
    ORDER BY end_time DESC;

によって返されるデータ [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) と [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) Standard S2 パフォーマンス層に関するパーセンテージ Web および Business 階層についてです。 たとえば、Web または Business データベースに対する実行時の戻り値が 70% の場合、S2 階層制限の 70% であることを示します。 さらに、Web および Business では、割合が 100% (これも S2 階層制限に基づく) を超える数値を反映している場合があります。

S2 データベース レベルでの DTU 消費情報を使用すると、Web および Business データベースの現在の消費を新しい階層のデータベースで正規化でき、最適な階層を判断できます。 たとえば、DTU 消費の平均的な割合が 80% であれば、S2 パフォーマンス レベルのデータベース制限の 80% の割合で DTU を消費していることになります。 100% より大きい値を表示する場合は、 **sys.resource_stats** ビュー必要があるパフォーマンス レベル S2 よりも大きいことを意味します。 たとえば、ピーク DTU の割合の値が 300% であるとします。  これは、S2 で使用できるリソースの 3 倍のリソースを使用していることを表します。 妥当な初期サイズを決定するためには、S2 (50 DTU) で使用可能な DTU を次に大きなサイズ (S3/P1 = 100 DTU、つまり S2 の 200%、P2 = 200 DTU、つまり S2 の 400%) と比較します。 S2 の 300% となっているため、初期サイズを P2 として再テストします。 

DTU の使用率とワークロードに合わせるに必要な最大エディションに基づき、指定できますのデータベース ワークロードに最適なサービス階層とパフォーマンス レベル (DTU の割合とさまざまなの相対 DTU の累乗とおり [パフォーマンス レベル)](sql-database-service-tiers.md)します。 次の表は、Web/Business リソース消費の割合とそれに等しい新しいパフォーマンス レベルのマッピングを表しています。 

![リソース消費][4]

> **注:**
> さまざまなパフォーマンス レベル間の相対 DTU 数がに基づいて、 [Azure SQL データベース ベンチマーク](http://msdn.microsoft.com/library/azure/dn741327.aspx) ワークロード。 データベースのワークロードはベンチマークと異なることがあるため、最初に Web/Business データベースに合う新しい階層を求めるときには、上述の計算をガイドラインとして使用する必要があります。 データベースを新しい階層に移動させたら、前のセクションに説明のあった処理を使用して、ワークロードのニーズに最適なサービス階層を検証/調整します。
> 
> 推奨される新しいエディションの階層/パフォーマンス レベルには過去 １４ 日分のデータベース アクティビティが考慮されますが、このデータは 5 分間の平均リソース消費データに基づくものです。 つまり 5 分に満たない短期間のアクティビティの急増が考慮されないことがあります。 そのため、このガイダンスはデータベースをアップグレードするための開始点として使用する必要があります。 推奨される階層にデータベースをアップグレードしたら、さらなる監視、テスト、検証を行う必要があります。必要に応じてデータベースを別の階層/パフォーマンス レベルに変更します。

次は、Web/Business 階層データベースの計算を実行するマスター データベースでのクエリです。5 分間隔のそれぞれのサンプル データのワークロードに合うと思われるエディションを提案します。

> **注:** このクエリは Web/business データベースとはに対してのみ利用 **いない** 新しい階層内のデータベースの正しい結果が得られます。

    WITH DTU_mapping AS
    ( SELECT *
        FROM ( VALUES (1, 10,'Basic'), (2, 20,'S0'), (3, 40,'S1'), (4, 100, 'S2')
                    , (5, 200, 'S3/P1'), (6, 1600,'Premium')
           ) AS t(id, percent_of_S2, target_edition)
    ), rc as
    ( SELECT start_time, end_time
           , (SELECT Max(v)
                FROM (VALUES (avg_cpu_percent)
                            , (avg_physical_data_read_percent)
                            , (avg_log_write_percent)
                   ) AS value(v)) as [avg_DTU_percent]
        FROM sys.resource_stats 
       WHERE database_name = 'WebDB'
    )
    SELECT rc.*
         , (SELECT TOP(1) t.target_edition
              FROM DTU_mapping AS t
             WHERE t.percent_of_S2 > CAST(1.2*rc.avg_DTU_percent as int)
             ORDER BY t.percent_of_S2) as target_edition
    FROM rc;

**サンプル結果:**

![サンプル結果](media/sql-database-upgrade-new-service-tiers/sample_result.png)

時間の経過と共に平均 DTU 使用割合の傾向をグラフで確認できます。 次は、全体的には S2 レベル内にあるが、一部のピーク アクティビティが P1 データベース レベルに達しているデータベースのグラフの例です。  時間の経過に伴い、DTU 消費は「Basic」から「P1」に変化しています。 新しい階層にこのデータベースを完全に合わせるには、「P1」パフォーマンス レベルの Premium サービス階層のデータベースが必要です。 これに対して、S2 レベル データベースでは、P1 レベルへ増加がまれな場合を処理できます。

![DTU の使用状況](media/sql-database-upgrade-new-service-tiers/DTU_usage.png)

**メモリ パフォーマンスへの影響:** データベース操作で使用可能なメモリを使用する SQL データベースが設計されたメモリは、DTU の評価に影響する、リソースの 1 つが、します。 そのため、メモリの消費は上記クエリの DTU 平均消費には含まれません。 一方、より低いパフォーマンス レベルにダウンサイズすると、データベースで利用できるメモリも少なくなります。 そうすると、IO 消費が大きくなり、消費される DTU が影響を受けることになります。 そのため、低いパフォーマンス レベルにダウンサイズする場合は、IO の割合に十分な余裕があることを確認してください。 使用 [sys.dm_ db _ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) DMV がこれを監視する前に説明しました。



## 3.Web または Business データベースの現在のパフォーマンスが、より高い Premium レベルに対応する理由を確認する

Web と Business データベースの場合、個々のデータベース用に特定のリソース容量が予約されているわけではありません。 さらに、Web または Business データベースにはお客様がパフォーマンスをスケールアップまたはスケールダウンするためのメカニズムもありません。 このため、Web と Business データベースのパフォーマンスは、きわめて遅いレベルから Premium レベルまでの範囲に渡ります。 このパフォーマンスのさまざまな範囲が *一定でない* リソースを共有するマルチ テナント環境内で他のデータベースによるで任意の時点のリソース消費の全体的なレベルに依存します。  

当然ながら Azure SQL Database サービスは、すべての Web と Business データベースで、可能な限り 100% に近い最適な実行を目標にしています。 その結果、Web と Business データベースの平均パフォーマンス レベルを Premium レベル以上に維持する効果がありました。 既存のデータベースのパフォーマンスが現在の Premium レベルに対応付けられるのはそのためです。 あいにく、このことが、どのサービス階層にアップグレードするかを評価するために Web と Business データベースを新しいサービス階層/パフォーマンス レベルと比較する際の、いくぶん非現実的な期待につながっています。

Web/Business と Basic、Standard、Premium サービス階層との違いをより明確に理解するために、次の図をご覧ください。 共有リソースの Web/Business モデルで実行されている 9 つの SQL Database と、Basic、Standard、Premium サービス階層モデルで実行されている 9 つの SQL Database を比較します。 Web/Business モデルでは、'noisy neighbors' がこの共有リソース プールに参加している他のデータベースに与える影響が明白です。 1 つのデータベースでリソースを集中的に使用するワークロードを実行しているとき、プール内のその他すべてのデータベースが影響を受け、使用可能なリソースは減少し始めます。 Basic、Standard、および Premium のサービスには、特定の量のリソースが階層化 ***は*** 共有環境内の他のデータベースが本質的に迷惑な問題から特定して、データベースの選択されたパフォーマンス レベルによってバインドされるため、各データベースに割り当てられています。 

![新しいサービス階層の予測可能なパフォーマンス][3]

全体的な DTU 割合が極端に高い場合は、DTU の詳細なメトリックを調べる必要があります。特に、データベースのログ I/O やメモリ使用量を詳しく検討します。 それにより、最適化して DTU 使用量を削減できる可能性がある領域が判明することがあります。


## 4. 低いパフォーマンス レベルに合わせてデータベースのワークロードを調整する
データベースの過去のリソース使用量を分析した結果、希望したよりもコストが高いパフォーマンス レベルへのアップグレードの必要性が示された場合は、追加のパフォーマンス チューニングが効果を発揮する領域を探します。 

アプリケーションの詳細に関する自らの知識に基づく判断として、典型的なワークロードで想定される使用量に比べてリソース使用量が極端に多いと思われる場合は、パフォーマンス チューニングによってアプリケーションのリソース使用量を削減できる可能性があります。

実際に、パフォーマンス チューニングをもう 1 回行うことは、すべてのデータベースに効果をもたらす可能性があります。

インデックスや実行計画の分析などの一般的なチューニングを行うメンテナンス作業に加えて、最大のターゲット Azure SQL Database へのデータ アクセス ルーチンを最適化する必要があります。 

| 記事 | 説明 |
|:--|:--|
| [Azure SQL データベースのパフォーマンス ガイダンス](http://msdn.microsoft.com/library/dn369873.aspx) | 「アプリケーションの調整」セクションでは、Azure SQL Database のパフォーマンス チューニングに関する詳細な推奨事項と手法を記載しています。|
|[パフォーマンス監視とチューニング ツール](https://msdn.microsoft.com/library/ms179428.aspx)| SQL Server のイベントの監視や物理データベース デザインのチューニングに使用できるツールに関するリンクと説明を記載しています。|
|[パフォーマンスの監視とチューニング](https://msdn.microsoft.com/library/ms189081.aspx)|MSDN の SQL Server 2014 のパフォーマンス チューニング セクション。|
| [Troubleshooting Performance Problems in SQL Server 2008 (SQL Server 2008 のパフォーマンスに関する問題のトラブルシューティング)](https://msdn.microsoft.com/library/dd672789.aspx) | 発行日は以前でも内容的には古くないこのホワイトペーパーでは、パフォーマンスに関する一般的な問題をトラブルシューティングするためのガイダンスを提供します。メモリや CPU のボトルネックのトラブルシューティングに関する役に立つ情報が含まれています。|
|[Troubleshoot and Optimize Queries with Azure SQL Database (Azure SQL Database を使用したクエリのトラブルシューティングと最適化)](http://social.technet.microsoft.com/wiki/contents/articles/1104.troubleshoot-and-optimize-queries-with-azure-sql-database.aspx)|公開日は以前ですが内容的には古くない、動的管理ビューと、動的管理ビューを使用したトラブルシューティングの方法に関するトピック。|



## 5.新しいサービス階層/パフォーマンス レベルにアップグレードする
お使いの Web と Business データベースに適切なサービス階層とパフォーマンス レベルを決定した後、データベースを新しい階層にアップグレードするには、いくつかの方法があります。

| 管理ツール | データベースのサービス階層とパフォーマンス レベルを変更します。||
| :---| :---|
| [Azure クラシック ポータル](https://manage.windowsazure.com) | クリックして、 **スケール** データベースのダッシュ ボード] ページのタブをクリックします。 |
| [Azure PowerShell](http://msdn.microsoft.com/library/azure/dn546726.aspx) | 使用して、 [セット AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) コマンドレットです。 |
| [REST API](https://msdn.microsoft.com/library/azure/mt163571.aspx) | 使用して、 [Create または Update Database](https://msdn.microsoft.com/library/azure/mt163685.aspx) コマンドです。|
| [Transact-SQL](http://msdn.microsoft.com/library/azure/bb510741.aspx) | 使用して、 [ALTER DATABASE (TRANSACT-SQL)](http://msdn.microsoft.com/library/azure/ms174269.aspx) ステートメントです。 |

詳細については、「 [を変更するデータベースのサービス階層とパフォーマンス レベル](sql-database-scale-up.md)


## 6. 新しいサービス階層/パフォーマンス レベルへのアップグレードを監視する
Azure SQL Database は、現在のデータベースが存在する論理サーバーの master データベースの sys.dm_operation_status 動的管理ビューでデータベースに対して実行される (CREATE、ALTER、DROP) のような管理操作の進行状況に関する情報を提供 [確認 _operation _status のドキュメントを参照してください。](http://msdn.microsoft.com/library/azure/dn270022.aspx) データベースのアップグレード操作の進行状況を確認するには、操作ステータス DMV を使用します。 このサンプル クエリには、データベース上で実行されるすべての管理操作が表示されます。

    SELECT o.operation, o.state_desc, o.percent_complete
    , o.error_code, o.error_desc, o.error_severity, o.error_state
    , o.start_time, o.last_modify_time
    FROM sys.dm_operation_status AS o
    WHERE o.resource_type_desc = 'DATABASE'
    and o.major_resource_id = '<database_name>'
    ORDER BY o.last_modify_time DESC;

クラシック ポータルを使用してアップグレードを行った場合は、ポータル内に操作に関する通知が表示されます。

## 7. アップグレード後のデータベースを監視する
Web/Business データベースを新しい階層にアップグレードした後は、アプリケーションが希望のパフォーマンスで実行されていることを確認し、必要に応じて最適化できるように、データベースを積極的に監視することをお勧めします。 データベースを監視する場合は、次の追加の手順を実行することもお勧めします。


**リソース消費データ:** Basic、Standard、および Premium のデータベースと呼ばれる新しい DMV からより詳細なリソース消費データがある [sys.dm_ db _ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) ユーザー データベース内。 この DMV は、直近 1 時間の操作に関する 15 秒おきのほぼリアル タイムのリソース消費情報を提供します。 1 つの間隔内の DTU 消費割合は、CPU、IO、ログにおける最大消費割合として算出されます。 次は、過去 1 時間における DTU の平均消費割合を算出するクエリです。

    SELECT end_time
         , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
           ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

 
追加 [ドキュメント](http://msdn.microsoft.com/library/dn800981.aspx) この DMV を使用する方法の詳細が含まれます。  [Azure SQL データベース パフォーマンス ガイダンス](http://msdn.microsoft.com/library/azure/dn369873.aspx) を監視し、アプリケーションをチューニングする方法について説明します。


- **警告:** にアップグレードしたデータベースの DTU 消費が特定の高レベルに近づいたときに通知する Azure クラシック ポータルで 'Alerts' を設定します。 データベース アラートは、Azure クラシック ポータルで、DTU、CPU、IO、ログなどのさまざまなパフォーマンス指標に対して設定できます。 

    たとえば、DTU の平均割合の値が 5 分間にわたって 75% を超過したような場合に、「DTU 割合」に関する電子メール アラートを送るように設定できます。 参照してください [警告通知を受け取る](insights-receive-alert-notifications.md) をアラートの通知を構成する方法の詳細を参照してください。


- **パフォーマンス レベルのアップグレード/ダウンをグレードのスケジュール:** してアプリケーションのパフォーマンスが向上のみ日/週の特定の時間を必要とする特定のシナリオの場合 [Azure Automation](https://azure.microsoft.com/documentation/services/automation/) アップサイズ/ダウンサイズする、データベースの計画的な操作とより高い/低いパフォーマンス レベル。

    たとえば、週に 1 度のバッチ/メンテナンス ジョブを行う間はデータベースをより高いパフォーマンス レベルにアップグレードし、ジョブが終了したらダウンサイズするようにも設定できます。 このようなスケジューリングは、データの読み込み、インデックスのリビルドといった大量のリソースを消費する操作にも便利です。Azure SQL Database の課金モデルはサービス階層/パフォーマンス レベルの 1 時間あたりの使用状況をベースにしています。 このような柔軟性が備わっていることで、よりコスト効率の高いアップグレードのスケジュール設定、計画が可能になります。



## 概要
Azure SQL Database サービスではテレメトリ データとツールを提供することで、Web/Business データベースのワークロードを評価し、アップグレードに最適なサービス階層を決定できます。 アップグレード処理は非常に簡単で、データベースをオフラインにしたり、データを損失したりすることなしに実行できます。 アップグレードしたデータベースではパフォーマンスの予想が可能になり、新しいサービス階層によって提供される追加の機能を使用できます。




<!--Image references-->
[2]: ./media/sql-database-upgrade-new-service-tiers/portal-dtus.JPG
[3]: ./media/sql-database-upgrade-new-service-tiers/web-business-noisy-neighbor.png
[4]: ./media/sql-database-upgrade-new-service-tiers/resource_consumption.png

 

