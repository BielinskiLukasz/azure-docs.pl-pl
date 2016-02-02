<properties 
    pageTitle="インデクサーを使用した Azure Search への Azure SQL Database の接続 | Microsoft Azure | ホスト型クラウド検索サービス" 
    description="インデクサーを使用して Azure SQL Database から Azure Search インデックスにデータを取得する方法について説明します。" 
    services="search" 
    documentationCenter="" 
    authors="chaosrealm" 
    manager="pablocas" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="11/04/2015" 
    ms.author="eugenesh"/>


# インデクサーを使用した Azure Search への Azure SQL Database の接続

Azure Search サービスはホスト型のクラウド検索サービスであり、これを利用すれば、優れた検索機能を簡単に提供できます。 検索を可能にするには、先に Azure Search インデックスにデータを入力する必要があります。 データが Azure SQL Database に存在する場合は、Azure Search の新しい **Azure SQL Database 用 Azure Search インデクサー** (**Azure SQL インデクサー**) でインデックス作成プロセスを自動化できます。 これは、記述するコードと対処するインフラストラクチャが減ることを意味します。

現時点では、インデクサーでのみ機能 Azure SQL Database、Azure の仮想マシンの SQL Server と [Azure DocumentDB](../documentdb/documentdb-search-indexer.md)します。 この記事では、Azure SQL Database で動作するインデクサーについて説明します。 表示したい場合は、追加のデータ ソースのサポートで、フィードバックを提供、 [Azure Search フィードバック フォーラム](http://feedback.azure.com/forums/263029-azure-search)します。

この記事ではインデクサーを使用するしくみについて説明しますが、SQL Database のみで使用できる機能と動作の詳細にも触れます (統合された変更追跡など)。

## インデクサーとデータ ソース

設定し、Azure SQL インデクサーを設定して、呼び出すことができます、 [Azure Search REST API](http://go.microsoft.com/fwlink/p/?LinkID=528173) を作成および管理 **インデクサー** と **データソース**します。

使用することも、 [Indexer クラス](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx) で、 [.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx), 、またはデータのインポート ウィザードで、 [Azure Classic Portal](https://portal.azure.com) を作成し、インデクサーのスケジュールを設定します。

**データ ソース**では、インデックスを作成するデータ、データにアクセスするために必要な資格情報、および Azure Search がデータの変更 (新しい行、変更された行、削除された行) を効率よく識別できるようにするポリシーを指定します。 データ ソースは、複数のインデクサーで使用できるように、独立したリソースとして定義します。

**インデクサー**は、データ ソースと検索対象のインデックスをつなげるリソースです。 インデクサーは次のように使用されます。

- 1 回限りのデータのコピーを実行して、インデックスを作成する。
- スケジュールに従ってデータ ソースの変更とインデックスを更新する。
- 必要に応じて実行し、インデックスを更新する。

## Azure SQL インデクサーを使用するとき

データに関連する複数の要因により、Azure SQL インデクサーを使用するのが適切な場合と、適切ではない場合があります。 データが次の要件を満たす場合、Azure SQL インデクサーを使用できます。

- すべてのデータが単一のテーブルまたはビューのものであること
    - データが複数のテーブルに分散している場合は、ビューを 1 つ作成して、そのビューをインデクサーで使用できます。 ただし、ビューを使用する場合は、SQL Server の統合変更検出を使用できないことに注意してください。 詳細については、このセクションを参照してください。
- データ ソースで使用されているデータ型をインデクサーがサポートしていること。 SQL のほとんどの型がサポートされていますが、すべてではありません。 詳細については、「 [Azure Search でのデータ型のマッピング](http://go.microsoft.com/fwlink/p/?LinkID=528105)します。
- 行の変更に対して、インデックスをほぼリアルタイムで更新する必要がないこと。
    - インデクサーがテーブルのインデックスを作成しなおすのに、最大で 5 分かかります。 使用をお勧め場合は、データが頻繁に変更され、秒または 1 分以内に、インデックスに反映されるまでに必要がある、 [Azure Search Index API](https://msdn.microsoft.com/library/azure/dn798930.aspx) 直接します。
- データ セットが大きく、インデクサーを計画的に実行する場合は、スキーマを使用することで変更された (および該当する場合は削除された) 行を効率よく識別できます。 詳細については、後の「新しい行、変更された行、削除された行の取得」を参照してください。
- 1 つの行でインデックスを作成されるフィールドのサイズが、Azure Search インデックス作成要求の最大サイズである 16 MB を超えないこと。

## Azure SQL インデクサーの作成と使用

最初に、データ ソースを作成します。

    POST https://myservice.search.windows.net/datasources?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key
    
    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }

接続文字列を取得することができます、 [Azure Classic Portal](https://portal.azure.com); を使用して、 `ADO.NET 接続文字列` オプション。

その後、ターゲットの Azure Search インデックスがまだない場合は、インデックスを作成します。 これを行うことができます、 [ポータル UI](https://portal.azure.com) またはを使用して、 [インデックス作成 API](https://msdn.microsoft.com/library/azure/dn798941.aspx)します。 ターゲット インデックスのスキーマとソース テーブルのスキーマに互換性があることを確認します。 SQL と Azure Search のデータ型のマッピングについては、次の表を参照してください。

**JSON データ型と Azure Search データ型の間のマッピング**

| SQL データ型| ターゲット インデックス フィールドに許可される型| メモ
|------|-----|----|
| ビット| Edm.Boolean、Edm.String| |
| int、smallint、tinyint| Edm.Int32、Edm.Int64、Edm.String| |
| bigint| Edm.Int64、Edm.String| |
| real、float| Edm.Double、Edm.String| |
| smallmoney、money decimal numeric| Edm.String| Azure Search では、小数点を Edm.Double に変換できません。精度が失われるためです。|
| char、nchar、varchar、nvarchar| Edm.String<br/>Collection(Edm.String)| string 列を Collection(Edm.String) に変換するには、プレビュー API バージョン 2015-02-28-Preview を使用する必要があります。参照してください [今回](search-api-indexers-2015-02-28-Preview.md#create-indexer) 詳細については|
| smalldatetime、datetime、datetime2、date、datetimeoffset| Edm.DateTimeOffset、Edm.String| |
| uniqueidentifer| Edm.String| |
| 地理| Edm.GeographyPoint| 型が POINT で SRID が 4326 (既定) の地理インスタンスのみがサポートされます。| |
| rowversion| 該当なし| 行バージョン列は検索インデックスに保存できませんが、変更追跡に利用できます。| |
| time、timespan、binary、varbinary、image、xml、geometry、CLR 型| 該当なし| サポートされていません|

最後に、名前を指定し、データ ソースとターゲット インデックスを参照することによって、インデクサーを作成します。

    POST https://myservice.search.windows.net/indexers?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key
    
    { 
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }

この方法で作成されたインデクサーには、スケジュールがありません。 作成するとすぐに自動的に 1 回実行されます。 **インデクサー実行**要求を使用して、いつでも再び実行できます。

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2015-02-28 
    api-key: admin-key

Azure サービスにデータベースへの接続を許可することが必要な場合があります。 参照してください [Azure からの接続](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) 手順を実行する方法についてです。

インデクサーの状態および実行履歴 (インデックスを作成された項目の数、エラーなど) を監視するには、**インデクサーの状態**要求を使用します。

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2015-02-28 
    api-key: admin-key

応答は、次のようになります。

    {
        "@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null 
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null 
            },
            ... earlier history items 
        ]
    }

実行履歴には、最近完了した 50 件の実行内容が含まれ、時系列の逆の順に並べられます (そのため、最近の実行内容が応答の最初に表示されます)。 
の応答に関する追加情報が見つかりません [インデクサー状態の取得](http://go.microsoft.com/fwlink/p/?LinkId=528198)

## スケジュールに従ったインデクサーの実行

スケジュールに従って定期的に実行するようにインデクサーを調整することもできます。 そのためには、インデクサーを作成または更新するときに、**schedule** プロパティを追加します。 次の例では、インデクサーを更新する PUT 要求を示します。

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key 
    
    { 
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**interval** パラメーターは必須です。 interval は、連続する 2 つのインデクサー実行の開始の時間間隔を示します。 許可される最短の間隔は 5 分です。最長は 1 日です。 XSD"dayTimeDuration"値としてフォーマットされている必要があります (の制限されたサブセットが [ISO 8601 期間](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 値)。 このパターンは、: `P(nD)(T(nH)(nM))`します。 例: `PT15M` 、15 分ごとに `PT2H` 2 時間ごとです。

省略可能な **startTime** は、スケジュールされた実行を開始するときを示します。省略すると、現在の UTC 時刻が使用されます。 この時刻は過去でもかまいません。その場合、最初の実行はインデクサーが startTime から継続的に実行されているかのようにスケジュールされます。

特定のインデクサーを実行できるのは一度に 1 つだけです。 次のインデクサーの開始予定時にインデクサーが既に実行されている場合、実行はスキップされ、他のインデクサー ジョブが実行していなければ、次の期間に再開されます。

さらに具体的な例を示します。 次のような時間単位のスケジュールを構成したものとします。

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

これは次のように動作します。

1. 最初のインデクサーの実行は、UTC で 2015 年 3 月 1 日午前 12 時前後に開始します。
1. この実行に 20 分 (または、1 時間未満) かかるものとします。
1. 第 2 の実行は、2015 年 3 月 1 日午前 1 時前後に開始します。
1. この実行に 1 時間以上かかるものとします。たとえば、70 分かかって、午前 2 時 10 分前後に終了します (実際にこのようなことが起こるには多数のドキュメントが必要ですが、便利な例です)。
1. 今は午前 2 時で、3 番目の実行が開始する時刻です。 ただし、午前 1 からの 2 番目の実行がまだ実行中であるため、3 番目の実行はスキップされます。 3 番目の実行は午前 3 時に開始します。

**PUT インデクサー**要求を使用することで、既存のインデクサーのスケジュールを追加、変更、削除できます。

## 新しい行、変更された行、削除された行の取得

スケジュールを使用していて、テーブルに多数の行が含まれる場合、データ変更検出ポリシーを使用して、インデクサーがテーブル全体のインデックスを再作成することなく新しいまたは変更された行のみを効率的に取得できるようにする必要があります。

### SQL 統合変更追跡ポリシー

SQL データベースをサポートする場合 [変更の追跡を](https://msdn.microsoft.com/library/bb933875.aspx), を使用することをお勧め **SQL 統合変更追跡ポリシー**します。 このポリシーは最も効率的な変更追跡を可能にし、テーブルに明示的な「ソフト削除」列を追加しなくても、Azure Search で削除済み行を識別できます。

統合変更追跡は、次の SQL Server データベース バージョン以降でサポートされます。

- Azure VM で SQL Server を使用している場合は、SQL Server 2008 R2 以降。
- Azure SQL Database を使用している場合は、Azure SQL Database V12。

SQL 統合変更追跡ポリシーを使用するときは、個別のデータ削除検出ポリシーを指定しないでください。個別のデータ削除ポリシーには、削除された行を識別するためのサポートが組み込まれています。

このポリシーはテーブルでのみ使用できます。ビューで使用することはできません。 このポリシーを使用する前に、使用しているテーブルの変更追跡を有効にする必要があります。 参照してください [を有効にして、変更の追跡を無効にする](https://msdn.microsoft.com/library/bb964713.aspx) についてです。

このポリシーを使用するには、データ ソースを次のように作成または更新します。

    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" }, 
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
      }
    }

### 高基準値変更検出ポリシー

SQL 統合変更追跡ポリシーが推奨されますが、データがビューにある場合、または古いバージョンの Azure SQL Database を使用している場合は、使用できません。 このような場合は、高基準ポリシーの使用を検討します。 このポリシーは、次の条件を満たす列がテーブルに含まれる場合に使用できます。

- すべての挿入は列の値を指定します。
- 項目を更新すると、列の値も変更されます。
- この列の値は変更のたびに増加します。
- 使用するクエリ、 `、` ような句 `場所 [High Watermark Column] > [現在の高基準値]` 効率的に実行できます。

たとえば、インデックスを作成された **rowversion** 列は、高基準列の最適な候補です。 
このポリシーを使用するには、データ ソースを次のように作成または更新します。

    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" }, 
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
      }
    }

### 論理削除列削除検出ポリシー

ソース テーブルから行が削除されると、おあそらく検索インデックスからも同様に行を削除する必要があります。 SQL 統合変更追跡ポリシーを使用する場合、これは自動的に行われます。 ただし、高基準変更追跡ポリシーでは行は削除されません。 どうすればよいでしょうか。

行がテーブルから物理的に削除される場合は、存在しなくなったレコードの存在を推論する方法はありません。 ただし、「論理削除」技法を使用し、列を追加してその列のマーカー値を使用して行を削除済みとしてマークすることにより、行をテーブルから削除しないで論理的に削除済みとしてマークできます。

論理削除手法を使用する場合は、データ ソースを作成または更新するときに、次のような論理削除ポリシーを指定できます。

    { 
        …, 
        "dataDeletionDetectionPolicy" : { 
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]", 
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]" 
        }
    }

**softDeleteMarkerValue** は文字列でなければならないことに注意してください。実際の値の文字列表現を使用してください。 たとえば、値 1 で削除された行がマークされている整数型の列があれば、使用 `「1」`削除された行がブール値の true とマークされているビット列があるかどうかに使用して `"True"`します。

## Azure SQL インデクサーのカスタマイズ

インデクサーの動作の一部をカスタマイズできます (たとえば、バッチ サイズ、インデクサーの実行が失敗する前にスキップできるドキュメントの数など)。 詳細については、次を参照してください。 [Azure Search Indexer のカスタマイズ](search-indexers-customization.md)します。

## よく寄せられる質問

**Q:** Azure の IaaS VM で実行する SQL Database で Azure SQL インデクサーを使用できますか?

A: はい、適切なポートを開いて Azure サービスがデータベースに接続できるようにすれば可能です。

**Q:** オンプレミスで実行する SQL Database で Azure SQL インデクサーを使用できますか?

A: この方法は推奨もサポートもされません。これを行うには、インターネット トラフィックに対してデータベースを開く必要があります。

**Q:** Azure の IaaS で実行している SQL Server 以外のデータベースで Azure SQL インデクサーを使用できますか?

A: SQL Server 以外のデータベースではインデクサーをテストしていないので、このシナリオはサポートされません。

**Q:** スケジュールに従って実行する複数のインデクサーを作成できますか?

A: はい。 ただし、1 つのノードで一度に実行できるインデクサーは 1 つだけです。 複数のインデクサーを同時に実行する必要がある場合は、複数の検索単位に検索サービスを拡大することを検討してください。

**Q:** インデクサーを実行するとクエリのワークロードに影響しますか?

A: はい。 インデクサーは検索サービス内のノードの 1 つで実行し、そのノードのリソースは、インデックスの作成、クエリ トラフィックの提供、およびその他の API 要求で共有されます。 大量のインデックス作成とクエリ ワークロードを実行し、503 エラーが高率で発生するか、または応答時間が長くなる場合は、検索サービスのスケールアップを検討してください。









