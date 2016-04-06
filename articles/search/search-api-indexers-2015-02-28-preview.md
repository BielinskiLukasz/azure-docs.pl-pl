<properties 
pageTitle="インデクサー操作 (Azure Search サービス REST API: 2015-02-28-Preview) | Microsoft Azure | ホスト型クラウド検索サービス" 
description="インデクサー操作 (Azure Search サービス REST API: 2015-02-28-Preview)" 
services="search" 
documentationCenter="" 
authors="HeidiSteen" 
manager="mblythe" 
editor="" />

<tags 
ms.service="search" 
ms.devlang="rest-api" 
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na" 
ms.date="11/04/2015" 
ms.author="heidist" />

#インデクサー操作 (Azure Search サービス REST API: 2015-02-28-Preview)#

> [AZURE.NOTE] この記事のインデクサーの説明、 [2015年-02-28-preview](./search-api-2015-02-28-preview)します。 現在の違いはありません、 `2015-02-28` で [MSDN](http://go.mirosoft.com/fwlink/p/?LinkID=528173) と `2015-02-28-Preview` バージョンがここで説明します。 この API に変更がない場合でも、この記事では `2015-02-28-Preview` の完全なドキュメント セットを提供します。

## 概要 ##

Azure Search は Microsoft Azure のホスト型クラウド検索サービスです。 Azure Search は一部の共通データ ソースと直接統合できます。データにインデックスを作成するためにコードを記述する必要はありません。 このアップを設定するには、作成および管理する Azure Search API を呼び出すことができます **インデクサー** と **データソース**します。 

 **インデクサー** はデータ ソースと検索対象のインデックスをつなげるリソースです。 インデクサーは次のように使用されます。 

- 1 回限りのデータのコピーを実行して、インデックスを作成する。
- スケジュールに従ってデータ ソースの変更とインデックスを同期する。 スケジュールは、インデクサーの定義の一部です。
- 必要に応じて呼び出し、インデックスを更新する。 

 **インデクサー** はのインデックスを定期的に更新する場合に便利です。 インデクサーの定義の一部としてインライン スケジュールを設定するかを使用してオンデマンドで実行する [インデクサーの実行](#RunIndexer)します。 

A **データソース** インデックスを作成する必要がデータあるをデータ、および (変更または削除されたデータベース テーブルの行など) のデータの変更を効率的に識別するために Azure Search を有効にするポリシーにアクセスする資格情報します。 データ ソースは複数のインデクサーで使用できるように独立したリソースとして定義します。

現在、次のデータ ソースがサポートされています。

- Azure SQL Database と Azure VM の SQL Server
- Azure DocumentDB 

将来的にサポートするデータ ソースを増やす予定です。 これらの決定事項の優先順位を付けるために、フィードバックをご提供の [Azure Search フィードバック フォーラム](http://feedback.azure.com/forums/263029-azure-search)します。

参照してください [サービスの制限](search-limits-quotas-capacity.md) インデクサーとデータ ソースのリソースに関連する上限についてです。

## 一般的な使用フロー ##

特定の `data source` または `indexer` リソースに簡単な HTTP 要求 (POST、GET、PUT、DELETE) を実行することでインデクサーとデータ ソースを作成し、管理できます。

自動インデックス作成の設定は通常、4 つの段階から構成されます。

1. インデックスを作成する必要があるデータを含むデータ ソースを特定します。 Azure Search はお使いのデータ ソースに存在するデータ型の一部に対応していない場合があることにご留意ください。 参照してください [サポートされるデータ型](https://msdn.microsoft.com/library/azure/dn798938.aspx) 一覧については、です。

2. スキーマがお使いのデータ ソースに対応する Azure Search インデックスを作成します。
  
3. Azure Search データ ソースの作成」の説明に従って [データ ソースの作成](#CreateDataSource)します。
  
4. 説明に従って、Azure Search インデクサーを作成する [インデクサーの作成](#CreateIndexer)します。

ターゲット インデックスとデータ ソースの組み合わせごとにインデクサーを 1 つ作成するように設計する必要があります。 複数のインデクサーが同じインデックスに書き込みできます。複数のインデクサーに同じデータ ソースを再利用できます。 ただし、インデクサーが 1 回に利用できるデータ ソースは 1 つだけです。そして、書き込めるインデックスは 1 つだけです。 

インデクサーを作成すると、その実行状態を取得できます、 [インデクサー状態の取得](#GetIndexerStatus) 操作します。 使用して、(の代わりに、またはスケジュールに従って定期的な実行に加えて) でもインデクサーを実行することも、 [インデクサーの実行](#RunIndexer) 操作します。

<!-- MSDN has 2 art files plus a API topic link list -->


## データ ソースの作成 ##

Azure Search では、インデクサーでデータ ソースが使用され、ターゲット インデックスのアドホックまたはスケジュールされたデータ更新用の接続情報が提供されます。 HTTP POST 要求を実行し、Azure Search サービス内に新しいデータ ソースを作成できます。
    
    POST https://[service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

または、PUT を使用し、URI でデータ ソース名を指定できます。 データ ソースが存在しない場合、作成されます。

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]

**注**: 許可されるデータ ソースの最大数は価格レベルによって異なります。 無料サービスの場合、最大 3 つのデータ ソースが許可されます。 Standard サービスの場合、50 のデータ ソースが許可されます。 参照してください [サービスの制限](search-limits-quotas-capacity.md) 詳細です。

**要求**

HTTPS はすべてのサービス要求に必要です。  **データ ソースの作成** 要求は POST または PUT メソッドを使用して作成できます。 POST を使用するとき、データ ソースの定義とともに、要求本文にデータ ソースの名前を指定する必要があります。 PUT の場合、名前は URL の一部です。 データ ソースが存在しない場合、作成されます。 既に存在する場合は、新しい定義に更新されます。 

データ ソースの名前は小文字にします。文字または数字で始めます。スラッシュとドットは使用できません。文字数の制限は 128 文字です。 データ ソースの名前は文字または数字で始め、残りの部分には文字、数字、ダッシュを使用できます。ダッシュは連続することができません。 参照してください [名前付け規則](https://msdn.microsoft.com/library/azure/dn857353.aspx) 詳細です。

`api-version` は必須です。 現行バージョンは `2015-02-28` です。 [Azure Search バージョン管理](https://msdn.microsoft.com/library/azure/dn864560.aspx) 説明および代替バージョンに関する詳細情報を持ちます。

**要求ヘッダー**

次の一覧は、必須と任意の要求ヘッダーについてまとめたものです。 

- `Content-Type`: 必須。 これを `application/json` に設定します
- `api-key`: 必須。 `api-key` は Search サービスに対する要求の認証に使用されます。 これはサービスに固有の文字列値です。  **データ ソースの作成** 要求を含める必要があります、 `api-key` ヘッダーには (クエリ キー) ではなく管理者キーに設定します。 
 
要求 URL を作成するにはサービス名も必要です。 両方のサービス名を取得することができ、 `api-key` サービス ダッシュ ボードから、 [Azure Classic Portal](https://portal.azure.com/)します。 参照してください [ポータルで Search サービスの作成](search-create-service-portal.md) のページのナビゲーション ヘルプについてです。

<a name="CreateDataSourceRequestSyntax"></a>
**要求本文の構文**

要求本文にはデータ ソースの定義が含まれます。この定義には、データ ソースの種類、データを読むための資格情報、定期的にスケジュールされたインデクサーで使用するときに、データ ソースで変更または削除されたデータを効率的に識別するための任意のデータ変更検出/データ削除検出ポリシーが含まれます。 


要求ペイロードを構築するための構文は次のとおりです。 サンプルの要求はこのトピックをさらに進むと登場します。

    { 
        "name" : "Required for POST, optional for PUT. The name of the data source",
        "description" : "Optional. Anything you want, or nothing at all",
        "type" : "Required. Must be 'azuresql' or 'documentdb'",
        "credentials" : { "connectionString" : "Required. Connection string for your data source" },
        "container" : { "name" : "Required. The name of the table or collection you wish to index" },
        "dataChangeDetectionPolicy" : { Optional. See below for details }, 
        "dataDeletionDetectionPolicy" : { Optional. See below for details }
    }

要求には次のプロパティが含まれます。 

- `name`: 必須。 データ ソースの名前。 データ ソース名には小文字、数字、ダッシュのみを使用できます。最初と最後の文字をダッシュにすることはできません。文字数の制限は 128 文字です。
- `description`: 任意の説明。 
- `type`: 必須。 サポートされているデータ ソースの種類のいずれかを指定する必要があります。
    - `azuresql` - Azure SQL Database または Azure VM の SQL Server
    - `documentdb` - Azure DocumentDB
- `credentials`:
    - 必須の `connectionString` プロパティはデータ ソースの接続文字列を指定します。 接続文字列の形式はデータ ソースの種類によって異なります。 
        - Azure SQL の場合、通常の SQL Server 接続文字列です。 Azure クラシック ポータルを利用して接続文字列を取得する場合、`ADO.NET connection string` オプションを使用します。
        - DocumentDB の場合、接続文字列は `"AccountEndpoint=https://[your account name].documents.azure.com;AccountKey=[your account key];Database=[your database id]"` の形式にする必要があります。 すべての値が必須です。 コントロールで、 [Azure Classic Portal](https://portal.azure.com/)します。   
        
- `container`: 
    - 必須の `name` プロパティにより、インデックスが作成されるテーブルまたはビュー (Azure SQL データ ソースの場合) あるいはコレクション (DocumentDB データ ソースの場合) が指定されます。 
    - SQL データ ソースでは、dbo. などのスキーマのプレフィックスを省略して、コンテナーがテーブルまたはビューの名前だけで構成されるようにします。
    - DocumentDB データ ソースは任意の `query` プロパティをサポートします。このプロパティにより、Azure Search がインデックスを作成できるフラット スキーマに任意の JSON ドキュメント レイアウトをフラット化するクエリを指定できます。   
- 任意の `dataChangeDetectionPolicy` と `dataDeletionDetectionPolicy` の説明は下にあります。

<a name="DataChangeDetectionPolicies"></a>
**データ変更検出ポリシー**

データ変更の検出ポリシーの目的は、変更されたデータ項目を効率的に識別することです。 サポートされているポリシーは、データ ソースの種類によって異なります。 以下のセクションでは、各ポリシーについて説明します。 

**注:** 切り替えるデータ検出ポリシー インデクサーを作成した後に使用して、 [インデクサーのリセット](#ResetIndexer) API です。

***高基準値変更検出ポリシー*** 

このポリシーは、データ ソースに含まれる列またはプロパティが次の条件を満たす場合に使用します。
 
- すべての挿入は列の値を指定します。 
- 項目を更新すると、列の値も変更されます。 
- この列の値は変更のたびに増加します。
- 次の `WHERE [High Water Mark Column] > [Current High Water Mark Value]` と同じようなフィルター句を使用するクエリを効率的に実行できます。

たとえば、Azure SQL データ ソースを使用するとき、インデックス付き `rowversion` 列は高基準値ポリシーとともに使用するのに最適な候補となります。 

DocumentDB データ ソースを使用するとき、DocumentDB が与える `_ts` プロパティを使用する必要があります。
 
このポリシーは次のように指定できます。

    { 
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
    } 

***SQL 統合変更検出ポリシー***

SQL データベースをサポートする場合 [変更の追跡を](https://msdn.microsoft.com/library/bb933875.aspx), 、SQL 統合変更追跡ポリシーを使用することをお勧めします。 このポリシーは最も効率的な変更追跡を可能にし、スキーマに明示的な「ソフト削除」列がなくても、Azure Search で削除済み行を識別できます。

統合変更追跡は、次の SQL Server データベース バージョン以降でサポートされます。 
- SQL Server 2008 R2、Azure Vm で SQL Server を使用している場合。
- Azure SQL Database を使用している場合は、Azure SQL Database V12。  

SQL 統合変更追跡ポリシーを使用するときは、個別のデータ削除検出ポリシーを指定しないでください。個別のデータ削除ポリシーには、削除された行を識別するためのサポートが組み込まれています。 

このポリシーはテーブルでのみ使用できます。ビューで使用することはできません。 このポリシーを使用する前に、使用しているテーブルの変更追跡を有効にする必要があります。 参照してください [を有効にして、変更の追跡を無効にする](https://msdn.microsoft.com/library/bb964713.aspx) についてです。    
 
作成するとき、 **データ ソースの作成** 要求と、SQL 統合変更追跡ポリシーは次のように指定することができます。

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
    }

<a name="DataDeletionDetectionPolicies"></a>
**データ削除検出ポリシー**

データ削除の検出ポリシーの目的は、削除されたデータ項目を効率的に識別することです。 現在、唯一サポートされているポリシーは `Soft Delete` ポリシーです。このポリシーでは、削除された項目をデータ ソースの `soft delete` 列またはプロパティに基づいて識別できます。 このポリシーは次のように指定できます。

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the column that specifies whether a row was deleted", 
        "softDeleteMarkerValue" : "the value that identifies a row as deleted" 
    }

**注:** 文字列、整数、またはブール値を持つ列のみがサポートされています。 `softDeleteMarkerValue` として使用される値は、それに対応する列に整数またはブール値が含まれているとしても、文字列にする必要があります。 たとえば、データ ソースに表示される値が 1 の場合、`"1"` を `softDeleteMarkerValue` として使用します。    

<a name="CreateDataSourceRequestExamples"></a>
**要求本文の例**

スケジュールに基づいて実行されるインデクサーでデータ ソースを使用する場合、この例を見れば、変更/削除検出ポリシーの指定方法がわかります。 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy", "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }

データの 1 回限りのコピーのためにデータ ソースを使用する場合、ポリシーは省略できます。

    { 
        "name" : "asqldatasource",
        "description" : "anything you want, or nothing at all",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" }
    } 

**Response**

要求成功の場合: 201 作成されました。 

<a name="UpdateDataSource"></a>
## データ ソースの更新 ##

HTTP PUT 要求を使用して既存のデータ ソースを更新できます。 要求 URI に更新するデータ ソースの名前を指定します。

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

`api-version` は必須です。 現行バージョンは `2015-02-28` です。 [Azure Search バージョン管理](https://msdn.microsoft.com/library/azure/dn864560.aspx) 説明および代替バージョンに関する詳細情報を持ちます。

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 認証セクションを参照してください [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) をキーに関する詳細について参照してください。 [ポータルで Search サービスの作成](search-create-service-portal.md) サービスの URL と、要求で使用される主要なプロパティを取得する方法について説明します。

**要求**
要求本文の構文は同じである [データ ソースの作成を要求](#CreateDataSourceRequestSyntax)します。

**Response**
要求成功の場合: 新しいデータ ソースが作成された場合は「201 作成されました」で、既存のデータ ソースが更新された場合は「204 コンテンツがありません」。

**メモ:**
一部のプロパティは既存のデータ ソースで更新できません。 たとえば、既存のデータ ソースの種類は変更できません。  

<a name="ListDataSource"></a>
## データ ソースの一覧表示 ##

 **データ ソースを一覧表示** 操作は、Azure Search サービスでデータ ソースの一覧を返します。 

    GET https://[service name].search.windows.net/datasources?api-version=[api-version]
    api-key: [admin key]

`api-version` は必須です。 現行バージョンは `2015-02-28` です。 [Azure Search バージョン管理](https://msdn.microsoft.com/library/azure/dn864560.aspx) 説明および代替バージョンに関する詳細情報を持ちます。

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 認証セクションを参照してください [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) をキーに関する詳細について参照してください。 [ポータルで Search サービスの作成](search-create-service-portal.md) サービスの URL と、要求で使用される主要なプロパティを取得する方法について説明します。

**Response**

要求成功の場合: 200 OK。

次は応答本文の例です。

    {
      "value" : [
        {
          "name": "datasource1",
          "type": "azuresql",
          ... other data source properties
        }]
    }

フィルター処理で応答を興味のあるプロパティだけに絞り込むことができます。 たとえば、データ ソース名の一覧だけを必要とする場合、OData `$select` クエリ オプションを使用します。

    GET /datasources?api-version=205-02-28&$select=name

この場合は、上記の例の応答は次のように表示されます。 

    {
      "value" : [ { "name": "datasource1" }, ... ]
    }

これは、Search サービスにデータ ソースがたくさんある場合、帯域幅を節約する便利な方法となります。

<a name="GetDataSource"></a>
## データ ソースの取得 ##

 **データ ソースの取得** 操作は、Azure Search からデータ ソースの定義を取得します。

    GET https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

`api-version` は必須です。 現行バージョンは `2015-02-28` です。 [Azure Search バージョン管理](https://msdn.microsoft.com/library/azure/dn864560.aspx) 説明および代替バージョンに関する詳細情報を持ちます。

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 認証セクションを参照してください [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) をキーに関する詳細について参照してください。 [ポータルで Search サービスの作成](search-create-service-portal.md) サービスの URL と、要求で使用される主要なプロパティを取得する方法について説明します。

**Response**

状態コード: 応答の成功に対して「200 OK」が返されます。

応答はの例のような [データ ソースの作成の要求例](#CreateDataSourceRequestExamples): 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted", 
            "softDeleteMarkerValue" : "true" }
    }

**注** 設定しないでください、 `Accept` 要求ヘッダーを `application/json;odata.metadata=none` ときにこれを行うと、この API を呼び出すと、 `@odata.type` 属性が、応答から省略されはデータの変更とさまざまな種類のデータ削除検出ポリシーを区別できません。 

<a name="DeleteDataSource"></a>
## データ ソースの削除 ##

 **データ ソースの削除** 操作は、Azure Search サービスからデータ ソースを削除します。

    DELETE https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

**注** インデクサーが削除しようとして、データ ソースを参照する場合、削除操作がでも続行します。 ただし、そのインデクサーの状態が次の実行でエラーに変わります。  

`api-version` は必須です。 現行バージョンは `2015-02-28` です。 [Azure Search バージョン管理](https://msdn.microsoft.com/library/azure/dn864560.aspx) 説明および代替バージョンに関する詳細情報を持ちます。

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 認証セクションを参照してください [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) をキーに関する詳細について参照してください。 [ポータルで Search サービスの作成](search-create-service-portal.md) サービスの URL と、要求で使用される主要なプロパティを取得する方法について説明します。

**Response**

状態コード: 応答の成功に対して「204 コンテンツがありません」が返されます。

<a name="CreateIndexer"></a>
## インデクサーの作成 ##

HTTP POST 要求を実行し、Azure Search サービス内に新しいインデクサーを作成できます。
    
    POST https://[service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

または、PUT を使用し、URI でデータ ソース名を指定できます。 データ ソースが存在しない場合、作成されます。

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]

**注**: 許可されるインデクサーの最大数は価格レベルによって異なります。 無料サービスの場合、最大 3 つのインデクサーが許可されます。 Standard サービスの場合、50 のインデクサーが許可されます。 参照してください [サービスの制限](search-limits-quotas-capacity.md) 詳細です。

`api-version` は必須です。 現行バージョンは `2015-02-28` です。 [Azure Search バージョン管理](https://msdn.microsoft.com/library/azure/dn864560.aspx) 説明および代替バージョンに関する詳細情報を持ちます。

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 認証セクションを参照してください [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) をキーに関する詳細について参照してください。 [ポータルで Search サービスの作成](search-create-service-portal.md) サービスの URL と、要求で使用される主要なプロパティを取得する方法について説明します。


<a name="CreateIndexerRequestSyntax"></a>
**要求本文の構文**

要求の本文には、インデックス作成のためのデータ ソースとターゲット インデックスを指定するインデクサー定義と、任意でインデックス作成のスケジュールとパラメーターが含まれます。 


要求ペイロードを構築するための構文は次のとおりです。 サンプルの要求はこのトピックをさらに進むと登場します。

    { 
        "name" : "Required for POST, optional for PUT. The name of the indexer",
        "description" : "Optional. Anything you want, or null",
        "dataSourceName" : "Required. The name of an existing data source",
        "targetIndexName" : "Required. The name of an existing index",
        "schedule" : { Optional. See Indexing Schedule below. },
        "parameters" : { Optional. See Indexing Parameters below. },
        "fieldMappings" : { Optional. See Field Mappings below. },
        "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.  
    }

**インデクサーのスケジュール**

インデクサーには、必要に応じてスケジュールを指定できます。 スケジュールが存在する場合、インデクサーはスケジュールに従って定期的に実行されます。 スケジュールには次の属性があります。

- `interval`: 必須。 インデクサーが実行される間隔または期間を指定する時間の値。 許可される最短の間隔は 5 分です。最長は 1 日です。 XSD"dayTimeDuration"値としてフォーマットされている必要があります (の制限されたサブセット、 [ISO 8601 期間](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 値)。 使用されるパターンは、`P[nD][T[nH][nM]]` です。 たとえば、15 分ごとの場合は `PT15M`、2 分ごとの場合は `PT2H` です。 

- `startTime`: 必須。 インデクサーの実行を開始する UTC 日時。 

**インデクサーのパラメーター**

インデクサーには任意でパラメーターを指定し、インデクサーの動作を変えることができます。 パラメーターはすべて任意です。  

- `maxFailedItems`: インデックス作成失敗がこの項目数に到達すると、そのインデクサー実行は失敗であるとみなされます。 既定値は 0 です。 によって失敗した項目に関する情報が返される、 [インデクサー状態の取得](#GetIndexerStatus) 操作します。 

- `maxFailedItemsPerBatch`: バッチごとのインデックス作成失敗がこの項目数に到達すると、そのインデクサー実行は失敗であるとみなされます。 既定値は 0 です。

- `base64EncodeKeys`: ドキュメント キーを base 64 でエンコードするかどうかを指定します。 Azure Search では、ドキュメント キーの文字に制約があります。 ただし、ソース データの値には無効な文字を含めることができます。 そのような値にドキュメント キーとしてインデックスを作成する必要がある場合、このフラグを true に設定できます。 既定値は `false` です。

**フィールド マッピング**

フィールド マッピングを使用すると、データ ソース内のフィールド名をターゲット インデックスの別のフィールド名にマッピングできます。 たとえば、ソース テーブルに `_id` というフィールドがあるとします。 Azure Search では下線で始まるフィールド名は許可されません。そのため、このフィールドの名前を変更する必要があります。 次のようにインデクサーの `fieldMappings` プロパティを使用すれば名前を変更できます。 
    
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 

複数のフィールド マッピングを指定できます。 

    "fieldMappings" : [ 
        { "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
     ]

ソースとターゲットの両方のフィールド名で大文字と小文字が区別されます。

<a name="FieldMappingFunctions"></a>
***フィールド マッピング関数***

使用してソース フィールドの値を変換するものフィールド マップをできます *マッピング関数*します。

このような関数としては現在、`jsonArrayToStringCollection` のみがサポートされています。 JSON 配列として書式設定されている文字列を含むフィールドを解析してターゲット インデックスの Collection(Edm.String) フィールドにします。 特に Azure SQL インデクサーで使用することを意図しています。SQL にはネイティブのコレクション データ型がないためです。 次のように使用できます。 

    "fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

たとえば、ソース フィールドに文字列 `["red", "white", "blue"]` が含まれている場合、`Collection(Edm.String)` 型のターゲット フィールドに `"red"`、`"white"`、`"blue"` の 3 つの値が入力されます。

`targetFieldName` プロパティは任意です。省略した場合、`sourceFieldName` 値が使用されます。 

<a name="CreateIndexerRequestExamples"></a>
**要求本文の例**

次の例では、UTC 2015 年 1 月 1 日から 1 時間ごとに実行されるスケジュールで `ordersds` データ ソースによって参照されるテーブルのデータを `orders` インデックスにコピーするインデクサーを作成します。 インデックス作成に失敗した項目が各バッチで 5 つ以下で、合計で 10 以下あれば、各インデクサーの呼び出しは成功となります。 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }

**Response**

要求成功の場合: 201 作成されました。


<a name="UpdateIndexer"></a>
## インデクサーの更新 ##

HTTP PUT 要求を使用して既存のインデクサーを更新できます。 要求 URI に更新するインデクサーの名前を指定します。

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

`api-version` は必須です。 現行バージョンは `2015-02-28` です。 [Azure Search バージョン管理](https://msdn.microsoft.com/library/azure/dn864560.aspx) 説明および代替バージョンに関する詳細情報を持ちます。

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 認証セクションを参照してください [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) をキーに関する詳細について参照してください。 [ポータルで Search サービスの作成](search-create-service-portal.md) サービスの URL と、要求で使用される主要なプロパティを取得する方法について説明します。

**要求**

要求本文の構文は同じである [インデクサーの作成を要求](#CreateIndexerRequestSyntax)します。

**Response**

要求成功の場合: 新しいインデクサーが作成された場合は「201 作成されました」で、既存のインデクサーが更新された場合は「204 コンテンツがありません」。


<a name="ListIndexers"></a>
## インデクサーの一覧表示 ##

 **インデクサーの一覧表示** 操作は、Azure Search サービスのインデクサーの一覧を返します。 

    GET https://[service name].search.windows.net/indexers?api-version=[api-version]
    api-key: [admin key]


`api-version` は必須です。 プレビュー バージョンは `2015-02-28-Preview` です。 [Azure Search バージョン管理](https://msdn.microsoft.com/library/azure/dn864560.aspx) 説明および代替バージョンに関する詳細情報を持ちます。

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 認証セクションを参照してください [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) をキーに関する詳細について参照してください。 [ポータルで Search サービスの作成](search-create-service-portal.md) サービスの URL と、要求で使用される主要なプロパティを取得する方法について説明します。

**Response**

要求成功の場合: 200 OK。

次は応答本文の例です。

    {
      "value" : [
      {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        ... other indexer properties
      }]
    }

フィルター処理で応答を興味のあるプロパティだけに絞り込むことができます。 たとえば、インデクサー名の一覧だけを必要とする場合、OData `$select` クエリ オプションを使用します。

    GET /indexers?api-version=2014-10-20-Preview&$select=name

この場合は、上記の例の応答は次のように表示されます。 

    {
      "value" : [ { "name": "myindexer" } ]
    }

これは、Search サービスにインデクサーがたくさんある場合、帯域幅を節約する便利な方法となります。


<a name="GetIndexer"></a>
## インデクサーの取得 ##

 **インデクサーの取得** 操作は、Azure Search からインデクサーの定義を取得します。

    GET https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

`api-version` は必須です。 プレビュー バージョンは `2015-02-28-Preview` です。 [Azure Search バージョン管理](https://msdn.microsoft.com/library/azure/dn864560.aspx) 説明および代替バージョンに関する詳細情報を持ちます。

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 認証セクションを参照してください [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) をキーに関する詳細について参照してください。 [ポータルで Search サービスの作成](search-create-service-portal.md) サービスの URL と、要求で使用される主要なプロパティを取得する方法について説明します。

**Response**

状態コード: 応答の成功に対して「200 OK」が返されます。

応答はの例のような [インデクサーの作成の要求例](#CreateIndexerRequestExamples): 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }


<a name="DeleteIndexer"></a>
## インデクサーの削除 ##

 **インデクサーの削除** 操作は、Azure Search サービスからインデクサーを削除します。

    DELETE https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

インデクサーが削除されるとき、その時点で進行中のインデクサー実行は完了しますが、後続の実行はスケジュールされません。 存在しないインデクサーを使用しようとすると、HTTP 状態コードの「404 見つかりません」が表示されます。 
 
`api-version` は必須です。 プレビュー バージョンは `2015-02-28-Preview` です。 [Azure Search バージョン管理](https://msdn.microsoft.com/library/azure/dn864560.aspx) 説明および代替バージョンに関する詳細情報を持ちます。

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 認証セクションを参照してください [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) をキーに関する詳細について参照してください。 [ポータルで Search サービスの作成](search-create-service-portal.md) サービスの URL と、要求で使用される主要なプロパティを取得する方法について説明します。

**Response**

状態コード: 応答の成功に対して「204 コンテンツがありません」が返されます。

<a name="RunIndexer"></a>
## インデクサーの実行 ##

定期的に実行されるスケジュール、だけでなく、インデクサー呼び出すこともできますからオンデマンドで、 **インデクサーの実行** 操作。 

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [admin key]

`api-version` は必須です。 プレビュー バージョンは `2015-02-28-Preview` です。 [Azure Search バージョン管理](https://msdn.microsoft.com/library/azure/dn864560.aspx) 説明および代替バージョンに関する詳細情報を持ちます。

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 認証セクションを参照してください [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) をキーに関する詳細について参照してください。 [ポータルで Search サービスの作成](search-create-service-portal.md) サービスの URL と、要求で使用される主要なプロパティを取得する方法について説明します。

**Response**

状態コード: 応答の成功に対して「202 受理されました」が返されます。

<a name="GetIndexerStatus"></a>
## インデクサー状態の取得 ##

 **インデクサー状態の取得** 操作、インデクサーの現在の状態と実行の履歴を取得します。 

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [admin key]


`api-version` は必須です。 プレビュー バージョンは `2015-02-28-Preview` です。 [Azure Search バージョン管理](https://msdn.microsoft.com/library/azure/dn864560.aspx) 説明および代替バージョンに関する詳細情報を持ちます。

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 認証セクションを参照してください [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) をキーに関する詳細について参照してください。 [ポータルで Search サービスの作成](search-create-service-portal.md) サービスの URL と、要求で使用される主要なプロパティを取得する方法について説明します。

**Response**

状態コード: 応答の成功に対して「200 OK」が返されます。

応答本文にはインデクサーの全体的な正常性状態、インデクサーの最後の呼び出し、(存在する場合は) インデクサー呼び出しの最近の履歴に関する情報が含まれます。 

サンプルの応答本文は次のようになります。 

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

**インデクサーの状態**

インデクサーの状態は、次の値のいずれかになります。

- `running` は、インデクサーが通常どおり実行されていることを示します。 一部のインデクサー実行が失敗している可能性があります。`lastResult` プロパティも確認しておけば安心です。 

- `error` は、ユーザーの介入なしでは修正できないエラーがインデクサーに発生したことを示します。 たとえば、データ ソースの資格情報の有効期限が切れている、データ ソースまたはターゲット インデックスのスキーマに大幅に変更された、などです。 

**インデクサー実行の結果**

インデクサー実行の結果には、1 回のインデクサー実行に関する情報が含まれています。 最新の結果はインデクサーの状態に `lastResult` プロパティとして表示されます。 最近の結果がその他にも存在する場合、インデクサーの状態の `executionHistory` プロパティとして返されます。 

インデクサー実行の結果には次のプロパティが含まれます。 

- `status`: 実行の状態。 参照してください [インデクサー実行の状態](#IndexerExecutionStatus) 以下詳細です。 

- `errorMessage`: 失敗した実行のエラー メッセージ。 

- `startTime`: この実行を開始した UTC 時刻。

- `endTime`: この実行を終了した UTC 時刻。 実行が進行中の場合、この値は設定されません。

- `errors`: 項目レベルのエラーがあれば、その一覧。 各エントリには、ドキュメント キー(`key` プロパティ) とエラー メッセージ (`errorMessage` プロパティ) が含まれています。 

- `itemsProcessed`: この実行の間にインデクサーがインデックス作成を試行したデータ ソース項目 (テーブル行など) の数。 

- `itemsFailed`: この実行の間に失敗した項目の数。  
 
- `initialTrackingState`: 最初のインデクサー実行に対して、あるいは使用されるデータ ソースでデータ変更追跡ポリシーが有効になっていない場合、常に `null`。 データ変更追跡ポリシーが有効になっている場合、後続の実行で、この値はこの実行で処理された最初の (最低) 変更追跡値を示します。 

- `finalTrackingState`: 使用されるデータ ソースでデータ変更追跡ポリシーが有効になっていない場合、常に `null`。 有効になっている場合、この実行で処理された最後の (最高) 変更追跡値を示します。 

<a name="IndexerExecutionStatus"></a>
**インデクサー実行の状態**

インデクサー実行の状態は、1 回のインデクサー実行の状態を記録したものです。 次の値が入ります。

- `success` は、インデクサー実行が正常に完了したことを示します。

- `inProgress` は、インデクサー実行が進行中であることを示します。 

- `transientFailure` は、インデクサー実行が失敗したことを示します。 詳細については、`errorMessage` プロパティを参照してください。 エラーには、それを修正するのに人間の介入が必要なものと不要なものがあります。たとえば、データ ソースとターゲット インデックス間のスキーマの互換性を修正するにはユーザーのアクションが必要ですが、データ ソースが一時的にダウンしている場合はユーザーのアクションは不要です。 スケジュールがあれば、スケジュールに基づいてインデクサーの呼び出しが続行されます。 

- `persistentFailure` は、インデクサーでエラーが発生し、ユーザーの介在を必要していることを示します。 スケジュールされているインデクサーの実行は停止します。 問題を解決した後に、インデクサーのリセット API を使用し、スケジュールされている実行を再開します。 

- `reset` は、インデクサーをリセットする API (下記参照) の呼び出しによりインデクサーがリセットされたことを示します。 

<a name="ResetIndexer"></a>
## インデクサーのリセット ##

 **インデクサーのリセット** 操作は変更の追跡のインデクサーに関連付けられている状態をリセットします。 リセットすることで、インデックス再作成を最初から始めたり (データ ソース スキーマが変更された場合など)、インデクサーに関連付けられているデータ ソースのデータ変更追跡ポリシーを変えたりできます。   

    POST https://[service name].search.windows.net/indexers/[indexer name]/reset?api-version=[api-version]
    api-key: [admin key]

`api-version` は必須です。 プレビュー バージョンは `2015-02-28-Preview` です。 [Azure Search バージョン管理](https://msdn.microsoft.com/library/azure/dn864560.aspx) 説明および代替バージョンに関する詳細情報を持ちます。

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 認証セクションを参照してください [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) をキーに関する詳細について参照してください。 [ポータルで Search サービスの作成](search-create-service-portal.md) サービスの URL と、要求で使用される主要なプロパティを取得する方法について説明します。

**Response**

状態コード: 応答の成功に対して「204 コンテンツがありません」が返されます。

## JSON データ型と Azure Search データ型の間のマッピング ##

<table style="font-size:12">
<tr>
<td>SQL データ型</td>  
<td>ターゲット インデックス フィールドに許可される型</td>
<td>メモ</td>
</tr>
<tr>
<td>ビット</td>
<td>Edm.Boolean、Edm.String</td>
<td></td>
</tr>
<tr>
<td>int、smallint、tinyint</td>
<td>Edm.Int32、Edm.Int64、Edm.String</td>
<td></td>
</tr>
<tr>
<td>bigint</td>
<td>Edm.Int64、Edm.String</td>
<td></td>
</tr>
<tr>
<td>real、float</td>
<td>Edm.Double、Edm.String</td>
<td></td>
</tr>
<tr>
<td>smallmoney、money<br>小数点<br>数値
</td>
<td>Edm.String</td>
<td>Azure Search では、小数点を Edm.Double に変換できません。精度が失われるためです。
</td>
</tr>
<tr>
<td>char、nchar、varchar、nvarchar</td>
<td>Edm.String<br/>Collection(Edm.String)</td>
<td>参照してください [Field Mapping Functions](#FieldMappingFunctions) 文字列型の列を collection(edm.string に変換する方法の詳細については、このドキュメントで</td>
</tr>
<tr>
<td>smalldatetime、datetime、datetime2、date、datetimeoffset</td>
<td>Edm.DateTimeOffset、Edm.String</td>
<td></td>
</tr>
<tr>
<td>uniqueidentifer</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>地理</td>
<td>Edm.GeographyPoint</td>
<td>型が POINT で SRID が 4326 (既定) の地理インスタンスのみがサポートされます。</td>
</tr>
<tr>
<td>rowversion</td>
<td>該当なし</td>
<td>行バージョン列は検索インデックスに保存できませんが、変更追跡に利用できます。</td>
</tr>
<tr>
<td>time、timespan<br>binary、varbinary、image<br>xml、geometry、CLR 型</td>
<td>該当なし</td>
<td>サポートされていません</td>
</tr>
</table>

## JSON データ型と Azure Search データ型間のマッピング ##

<table style="font-size:12">
<tr>
<td>JSON データ型</td> 
<td>ターゲット インデックス フィールドに許可される型</td>
<td>メモ</td>
</tr>
<tr>
<td>bool</td>
<td>Edm.Boolean、Edm.String</td>
<td></td>
</tr>
<tr>
<td>整数</td>
<td>Edm.Int32、Edm.Int64、Edm.String</td>
<td></td>
</tr>
<tr>
<td>浮動小数点数</td>
<td>Edm.Double、Edm.String</td>
<td></td>
</tr>
<tr>
<td>文字列</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>プリミティブ型の配列。例 [ "a"、"b"、"c" ]</td>
<td>Collection(Edm.String)</td>
<td></td>
</tr>
<tr>
<td>日付などの文字列</td>
<td>Edm.DateTimeOffset、Edm.String</td>
<td></td>
</tr>
<tr>
<td>GeoJSON ポイント オブジェクト</td>
<td>Edm.GeographyPoint</td>
<td>GeoJSON ポイントは { "type" : "Point", "coordinates" : [long, lat] } 形式の JSON オブジェクトです。 </td>
</tr>
<tr>
<td>その他の JSON オブジェクト</td>
<td>該当なし</td>
<td>サポートされていません。Azure Search では現在のところ、プリミティブ型と文字列の集合のみをサポートしています。</td>
</tr>
</table>

