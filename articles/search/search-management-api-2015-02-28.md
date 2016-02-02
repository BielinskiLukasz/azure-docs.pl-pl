<properties pageTitle="Azure Search 管理 REST API バージョン 2015-02-28 | Microsoft Azure | ホスト型クラウド検索サービス" description="Azure Search 管理 REST API: バージョン 2015-02-28" services="search" documentationCenter="" authors="HeidiSteen" manager="mblythe" editor=""/>

<tags ms.service="search" ms.devlang="rest-api" ms.workload="search" ms.topic="article"  ms.tgt_pltfrm="na" ms.date="11/04/2015" ms.author="heidist" />


# 管理 API: バージョン 2015-02-28

Azure Search は Microsoft Azure のホスト型クラウド検索サービスです。 このドキュメントで説明、 ** 2015年-02-28* Azure Search 管理 REST API のバージョン。 その後、これより新しいバージョンで置き換えられています。 最新バージョンは、次を参照してください。 [Azure Search 管理 REST API 2015-08-19](https://msdn.microsoft.com/library/dn832684.aspx) MSDN にします。

## サービス管理操作

Azure Search 管理 REST API により、管理者はポータルから使用できる大半の機能にプログラムでアクセスすることで、次の操作を自動化できます。

- Azure Search サービスを作成または削除する。
- 作成、再生成、または取得 `api-key` 検索データ操作の認証に使用される管理キーへの定期的な変更を自動化します。
- クエリのボリュームまたはストレージ要件の変更に対応して Azure Search サービスのスケールを調整する。

で完全に管理、サービス プログラムでは、2 つの Api 必要があります。「管理 REST API の Azure Search と、一般的な [Azure リソース マネージャー REST API](https://msdn.microsoft.com/library/azure/dn790568.aspx)します。 リソース マネージャー API はサブスクリプション データのクエリや地理的場所の一覧表示など、サービス固有ではない一般的な目的の操作で使用されます。 サブスクリプションで Azure Search サービスを作成および管理するには、HTTP 要求にリソース マネージャーのエンドポイント、サブスクリプション ID、プロバイダー (この場合は Azure Search)、Search サービス固有の操作が含まれるようにします。

[Azure Search 管理 REST API を使ってみる](http://go.microsoft.com/fwlink/p/?linkID=516968) のアプリケーションの構成とサービス管理操作を示すサンプル コードに示します。 このサンプル アプリケーションでは、Azure リソース マネージャー API と Azure Search 用のサービス管理 API の両方に要求が発行されており、両方の API を使用するさまざまな構成要素をまとめて 1 つのアプリケーションを作成する方法が示されています。

### エンドポイント

サービス管理操作のエンドポイントは、Azure リソース マネージャーの URL、 `https://management.azure.com`します。

すべての管理 API には、サブスクリプション ID と API バージョンを含める必要があることに注意してください。

### バージョン

Azure Search 管理 REST API の現在のバージョンは `api バージョン 2015年-02-28 =`します。 以前のバージョンでは、 `api バージョン 2014年-07-31-preview を =` は使用されなくなりました。 次の数か月間は引き続きご利用いただけますが、できるだけ早く新しいバージョンに移行されることをお勧めします。

### 認証と Access Control

Azure Search 管理 REST API は、Azure リソース マネージャーの拡張機能であり、その依存関係を共有します。 そのため、Azure Search のサービス管理にとって、Active Directory は必須となります。 クライアント コードからのすべての管理要求は、リソース マネージャーに到達する前に、Azure Active Directory を使用して、認証される必要があります。

アプリケーション コードが *サービス管理操作* とインデックスまたはドキュメントの*データ操作*を処理する場合、それぞれの Azure Search API に対して 2 つの認証アプローチを使用することに注意してください。

- サービスとキーの管理では、リソース マネージャーとの依存関係のために、認証に Active Directory を使用します。
- インデックスの作成やドキュメントの検索などの Azure Search サービスのエンドポイントに対するデータ要求を使用して、 `api キー` 要求ヘッダーにします。 参照してください [Azure Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) については、データの要求を認証します。

記載されているサンプル アプリケーション [Azure Search 管理 REST API を使ってみる](http://go.microsoft.com/fwlink/p/?linkID=516968) 操作の種類ごとの認証方法を示しています。 Active Directory を使用するようにクライアント アプリケーションを構成する手順については、「Get started with Azure Search Management REST API」を参照してください。

Azure リソース マネージャーのアクセス制御では、組み込みの所有者、投稿者、および閲覧者のロールが使用されます。 既定では、すべてのサービス管理者が、所有者ロールのメンバーです。 詳細については、「 [Azure クラシック ポータルでロールベースのアクセス制御](../role-based-access-control-configure.md)します。


### API の概要

次の API が操作用に提供されています。

- <a name="CreateService">Search サービスを作成します。</a>

    `PUT https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28`

- <a name="GetService">Search サービスを取得します。</a>

    `Https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28 を取得します。`

- <a name="ListService">Search サービスの一覧</a>

    `Https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices?api-version=2015-02-28 を取得します。`

- <a name="DeleteService">Search サービスを削除します。</a>

    `Https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28 を削除します。`

- <a name="UpdateService">Search サービスを更新します。</a>

    `修正プログラム https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28`

- <a name="ListAdminKey">管理者キーの一覧表示</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listAdminKeys?api-version=2015-02-28`

- <a name="RegenAdminKey">管理者キーを再生成します。</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/regenerateAdminKey/[keyKind]?api-version=2015-02-28`

- <a name="CreateQueryKey">クエリ キーを作成します。</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/createQueryKey/[name]?api-version=2015-02-28`

- <a name="ListQueryKey">クエリ キーの一覧表示</a>

    `Https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listQueryKeys?api-version=2015-02-28 を取得します。`

- <a name="DeleteQueryKey">クエリ キーを削除します。</a>

    `Https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/deleteQueryKey/[key]?api-version=2015-02-28 を削除します。`

<a name="ServiceOps"></a>
## サービス操作

Azure サブスクリプションに対して HTTP 要求を発行することによって、Azure Search サービスをプロビジョニングまたはプロビジョニング解除できます。 これらの操作によって可能なシナリオには、カスタム管理ツールの作成や、エンドツーエンドの運用環境または開発環境の立ち上げ (サービスの作成から、インデックスの作成までのすべての過程) などがあります。 同様に、クラウド ソリューションを設計して販売するソリューション ベンダーは、新規顧客ごとにサービスをプロビジョニングするアプローチを自動化および反復可能にできます。

**サービスに対する操作**

サービス関連の操作には、次の API があります。

- <a name="CreateService">Search サービスを作成します。</a>
- <a name="GetService">Search サービスを取得します。</a>
- <a name="ListService">Search サービスの一覧</a>
- <a name="DeleteService">Search サービスを削除します。</a>
- <a name="UpdateService">Search サービスを更新します。</a>


<a name="CreateService"></a>
### Search サービスの作成

**Search サービスの作成**操作は、指定されたパラメーターで新しい Search サービスをプロビジョニングします。 この API は既存のサービス定義の更新にも使用することができます。

    PUT https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

#### 要求 URI のパラメーター

`subscriptionId`: が必要です。  `SubscriptionID` Azure ユーザーのです。 この値は、Azure リソース マネージャー API またはポータルから取得できます。

`resourceGroupName`: が必要です。 ユーザーのサブスクリプション内のリソース グループの名前。 この値は、Azure リソース マネージャー API またはポータルから取得できます。

`serviceName`: が必要です。指定されたリソース グループ内の Search サービスの名前。サービス名には小文字、数字、またはダッシュのみを含むことができ、最初の 2 文字または最後の 1 文字にダッシュを使用することはできません。また、ダッシュは連続して使用できず、長さは 2 文字から 15 文字にする必要があります。すべての名前終わる <name>. search.windows.net、サービス名はグローバルに一意である必要があります。サブスクリプション内、リソース グループ内、またはこれらにまたがって、2 つのサービスに同じ名前を付けることはできません。サービス名は作成後に変更できません。

`api バージョン`: が必要です。 この要求に使用されるプロトコルのバージョンを指定します。 現在のバージョンは `2015年-02-28`します。


#### 要求ヘッダー

`Content-type`: が必要です。 このヘッダーは application/json に設定します。

`x-ms-クライアントの要求の id`: 省略可能です。 クライアントが生成した、この要求を識別する GUID 値。 この値が指定されている場合、値は要求をマップする方法として応答情報に含まれます。


#### 要求本文

{ 
      "location":「search サービスの場所」 
      「タグ」: { 
        "key":"value"
        ...
      }, 
    "properties": { 
        "sku": { 
            "name":"無料 |標準的な |standard2" 
        }, 
        "replicaCount": 1 |2 |3 |4 |5 |第 6 
        "partitionCount": 1 |2 |3 |4 |6 |12 
    { 
{

#### 要求本文のパラメーター

`場所`: が必要です。 サポートおよび登録されている Azure の地理的位置 (米国西部、米国東部、東南アジアなど) の 1 つ。 リソースの場所は、作成後には変更できないことに注意してください。

`タグ`: 省略可能です。 リソースを説明するキーと値のペアの一覧。 これらのタグはリソース グループをまたがってリソースを表示およびグループ化する際に使用できます。 1 つのリソースに対して、最大 10 個のタグを指定できます。 各タグには 128 文字以内のキーと、256 文字以内の値が必要です。

`sku`: が必要です。 有効な値は `無料` と `標準`します。 `standard2` も有効ですが、マイクロソフトのサポートで、Azure サブスクリプションの有効な場合にのみ使用できます。 `無料` クラスターの共有サービスをプロビジョニングします。 `標準` 専用クラスターのサービスをプロビジョニングします。 無料の価格レベルでは Search サービスを 1 つしか作成できません。 追加のサービスは標準の価格レベルで作成する必要があります。 既定では、サービスに 1 つのパーティションと 1 つのレプリカが作成されます。 追加のパーティションとレプリカは検索単位で課金されます。 参照してください [制限および制約](search-limits-quotas-capacity.md) 詳細です。 変更することはできません、 `sku` サービスを作成するとします。

`replicaCount`: 省略可能です。 既定値は 1 です。 有効な値は 1 ～ 6 です。 場合にのみ有効 `sku` は `標準`します。

`partitionCount`: 省略可能です。 既定値は 1 です。 有効な値は 1、2、3、4、6、または 12 です。 場合にのみ有効 `sku` は `標準`します。


### Response

サービス定義が更新されると、HTTP 200 (OK) が返されます。 新しいサービスが作成されると、HTTP 201 (Created) が返されます。


#### レスポンス ヘッダー

`Content-type`: このヘッダーは常に application/json に設定します。

`x ms 要求 id`: サービスによって生成された現在の操作の一意の識別子。


#### 応答本文

HTTP 200 および 201 の応答本文には、サービス定義が含まれます。

    { 
      "id": "/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]", 
    
      "name": "service name", 
      "location": "location of search service", 
    "type": " Microsoft.Search/searchServices", 
      "tags": {
        "key": "value",
        ...
      },  
    "properties": { 
        "sku": { 
          "name": "free | standard | standard2" 
             }, 
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6, 
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12, 
        "status": "running | provisioning | deleting | degraded | disabled | error", 
        "statusDetails": "details of the status", 
        "provisioningState": "succeeded | provisioning | failed" 
      } 
    } 

#### 応答本文の要素

`id`: id が、この Search サービスの URL (ホスト名/スキームを除く)。

`名`: search サービスの名前。

`場所`: サポートされており、登録済みの Azure の地理的地域は (米国西部、米国東部、東南アジアなど) のいずれかです。

`タグ`: 表示およびリソース グループ間でリソースをグループ化で使用されるリソースを説明するキー/値ペアの一覧です。

`sku`: 価格レベルを示します。 有効な値は、次のとおりです。

- `無料`: 共有クラスター
- `標準`: 専用クラスター
- `standard2`: Microsoft サポートの指示の下でのみ使用します。

`replicaCount`: サービスがレプリカの数を示します。 有効な値は 1 ～ 6 です。

`partitionCount`: サービスがパーティションの数を示します。 有効な値は 1、2、3、4、6、または 12 です。

`ステータス`: 操作が呼び出された時点で検索サービスのステータス。 有効な値は、次のとおりです。

- `を実行している`: Search サービスを作成します。
- `プロビジョニング`: 検索サービスはプロビジョニング中です。
- `削除`: 検索サービスは削除中です。
- `低下`: 検索サービスの品質低下します。 これは、サービスが正しく動作することを阻害するようなエラーがクラスターで発生した場合に起きる可能性があります。
- `無効になっている`: Search サービスは無効です。 この状態では、サービスはすべての API 要求を拒否します。
- `エラー`: Search サービスがエラー状態にします。

**注**: 場合は、サービスが、 `低下`, 、`無効になっている`, 、または `エラー` 状態にある場合、Azure Search チームが基になる問題を調査してアクティブにします。 この状態の専用サービスは、プロビジョニングされた検索単位数に基づいて、引き続き課金対象になります。

`statusDetails`: 状態の詳細。

`provisioningState`: サービスのプロビジョニングの現在の状態を示します。 有効な値は、次のとおりです。

- `が成功した`: プロビジョニングは成功しました。
- `プロビジョニング`: サービスがプロビジョニング中です。
- `失敗`: サービスのプロビジョニングが失敗しました。

プロビジョニングは、サービスの容量が確立されるときに発生する中間的な状態です。 容量がセットアップした後 `provisioningState` 「成功」または「失敗」に変更します。 クライアント アプリケーションは操作が完了するタイミングを知るため、**Search サービスの取得**操作を使用してプロビジョニングの状態をポーリングすることができます (ポーリング間隔は 30 秒から 1 分までの間にすることをお勧めします)。 無料のサービスを使用している場合は、この値はサービス作成の呼び出しに対して多くの場合 "成功" が直接返されます。 これは、無料のサービスは既にセットアップされている容量を使用するためです。

<a name="GetService"></a>
### Search サービスの取得

**Search サービスの取得** 操作は、指定した Search サービスのプロパティを返します。 管理者キーは返されないことに注意してください。 管理者キーを取得するには、**管理者キーの取得**操作を使用してください。

    GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

#### 要求 URI

`subscriptionId`: が必要です。 Azure のユーザーのサブスクリプション Id を指定します。 この値は、Azure リソース マネージャー API またはポータルから取得できます。

`resourceGroupName`: が必要です。 ユーザーのサブスクリプション内のリソース グループの名前。 この値は、Azure リソース マネージャー API またはポータルから取得できます。

`serviceName`: が必要です。 指定されたリソース グループ内の Search サービスの名前。 サービス名がわからない場合は、Search サービスの一覧表示 (Azure Search API) から一覧を取得できます。

`api バージョン`: が必要です。 この要求に使用されるプロトコルのバージョンを指定します。 現在のバージョンは `2015年-02-28`します。

#### 要求ヘッダー

`x-ms-クライアントの要求の id`: 省略可能です。 クライアントが生成した、この要求を識別する GUID 値。 この値が指定されている場合、値は要求をマップする方法として応答情報に含まれます。


#### 要求本文

ありません。


#### 応答の状態コード

成功した場合は、HTTP 200 (OK)。


#### レスポンス ヘッダー

`Content-type`: このヘッダーは常に application/json に設定します。

`x ms 要求 id`: サービスによって生成された現在の操作の一意の識別子。

#### 応答本文

    {
      "id": "/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]",
    
      "name": "service name",
      "location": "location of search service",
    "type": " Microsoft.Search/searchServices",
      "tags": {
        "key": "value",
        ... 
      }, 
    "properties": {
        "sku": {
          "name": "free | standard | standard2"
        },
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
        "status": "running | provisioning | deleting | degraded | disabled | error",
        "statusDetails": "details of the status",
        "provisioningState": "succeeded | provisioning | failed"
      }
    }

#### 応答本文の要素

`id`: id が、この Search サービスの URL (ホスト名/スキームを除く)。

`名`: Search サービスの名前。

`場所`: リソースの場所。 サポートおよび登録されている Azure の地理的位置 (米国西部、米国東部、東南アジアなど) の 1 つです。

`タグ`: タグはリソースを説明するキー値のペアの一覧です。 これらのタグはリソース グループをまたがってリソースを表示およびグループ化する際に使用できます。

`sku`: 価格レベルを示します。 有効な値は、次のとおりです。

- `無料`: 共有クラスター
- `標準`: 専用クラスター
- `standard2`: Microsoft サポートの指示の下でのみ使用します。

`replicaCount`: サービスがレプリカの数を示します。 有効な値は 1 ～ 6 です。

`partitionCount`: サービスがパーティションの数を示します。 有効な値は 1、2、3、4、6、または 12 です。

`ステータス`: 操作が呼び出された時点で検索サービスのステータス。 有効な値は、次のとおりです。

- `を実行している`: Search サービスを作成します。
- `プロビジョニング`: 検索サービスはプロビジョニング中です。
- `削除`: 検索サービスは削除中です。
- `低下`: 検索サービスの品質低下します。 これは、サービスが正しく動作することを阻害するようなエラーがクラスターで発生した場合に起きる可能性があります。
- `無効になっている`: Search サービスは無効です。 この状態では、サービスはすべての API 要求を拒否します。
- `エラー`: Search サービスがエラー状態にします。

**注**: 場合は、サービスが、 `低下`, 、`無効になっている`, 、または `エラー` 状態にある場合、Azure Search チームが基になる問題を調査してアクティブにします。 この状態の専用サービスは、プロビジョニングされた検索単位数に基づいて、引き続き課金対象になります。

`statusDetails`: 状態の詳細。

`provisioningState`: 有効な値が含まれます。

- `が成功した`: プロビジョニングは成功しました。
- `プロビジョニング`: サービスがプロビジョニング中です。
- `失敗`: プロビジョニングに失敗しました。


<a name="ListService"></a>
### Search サービスの一覧表示

**サービスの一覧表示**操作は、特定のリソース グループのサブスクリプションの全 Search サービスの一覧を返します。 この操作は、サービス定義 (管理者 api-key を除く) を返します。 管理者キーを取得するには、**管理者キーの取得**操作を使用してください。

    GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices?api-version=2015-02-28

#### 要求 URI のパラメーター

`subscriptionId`: が必要です。  `SubscriptionID` Azure ユーザーのです。 この値は、Azure リソース マネージャー API またはポータルから取得できます。

`resourceGroupName`: が必要です。 ユーザーのサブスクリプション内のリソース グループの名前。 この値は、Azure リソース マネージャー API またはポータルから取得できます。

`api バージョン`: が必要です。 この要求に使用されるプロトコルのバージョンを指定します。 現在のバージョンは `2015年-02-28`します。

#### 要求ヘッダー

`x-ms-クライアントの要求の id`: 省略可能です。 クライアントが生成した、この要求を識別する GUID 値。 この値が指定されている場合、値は要求をマップする方法として応答情報に含まれます。

#### 要求本文

ありません。

#### Response

成功した場合のステータス コードは HTTP 200 (OK) です。

### レスポンス ヘッダー

`Content-type`: このヘッダーは常に application/json に設定します。

`x ms 要求 id`: サービスによって生成された現在の操作の一意の識別子。


#### 応答本文

応答本文はサービスの一覧であり、JSON 配列として返されます。各サービスは **Search サービスの取得**操作の形式に従います。

なお、 `nextLink` フィールドは、現在のバージョンはページングをサポートしないために常に null です。 空の値で返すことで将来の互換性が維持されます。

    {
      "value": [
        {
          "id": "id of service 1",
          "name": "service name",
          "location": "location of search service 1",
        "type": " Microsoft.Search/searchServices",
          "tags": {
            "key": "value",
            ...
          }, 
        "properties": {
            "sku": {
              "name": "free | standard | standard2"
            },
            "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
            "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
            "status": "running | provisioning | deleting | degraded | disabled | error",
            "statusDetails": "details of the status",
            "provisioningState": "succeeded | provisioning | failed"
        }
      },   
      {
          "id": "id of service 2",
          "name": "service name 2",
          "location": "location of search service 2",
        "type": " Microsoft.Search/searchServices",
          "tags": {
            "key": "value",
            ...
          },
        "properties": {
            "sku": { 
              "name": "free | standard | standard2"
            },
            "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
            "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
            "status": "running | provisioning | deleting | degraded | disabled | error",
            "statusDetails": "details of the status",
            "provisioningState": "succeeded | provisioning | failed"
        }
      }
    ],
    "nextLink": null
    }

<a name="DeleteService"></a>
### サービスの削除

**サービスの削除**操作は Search サービスと検索データを削除します。削除対象にはすべてのインデックスとドキュメントも含まれます。

    DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

**注:** 管理者および開発者は、運用サーバーからアプリケーション データを削除する前に、バックアップを作成することを必要とします。 Azure Search にバックアップ操作はないため、 アプリケーションのプライマリ ストレージとしてインデックスを使用している場合は、インデックス内にあるデータをすべて返す Search 操作を使用して、データを外部に格納する必要があります。

### 要求 URI のパラメーター

`subscriptionId`: が必要です。 Azure のユーザーのサブスクリプション Id を指定します。 この値は、Azure リソース マネージャー API またはポータルから取得できます。

`resourceGroupName`: が必要です。 ユーザーのサブスクリプション内のリソース グループの名前。 この値は、Azure リソース マネージャー API またはポータルから取得できます。

`serviceName`: が必要です。 指定されたリソース グループ内の Search サービスの名前。 サービス名がわからない場合は、Search サービスの一覧表示 (Azure Search API) から一覧を取得できます。

`api バージョン`: が必要です。 この要求に使用されるプロトコルのバージョンを指定します。 現在のバージョンは `2015年-02-28`します。

### 要求ヘッダー

`x-ms-クライアントの要求の id`: 省略可能です。 クライアントが生成した、この要求を識別する GUID 値。 この値が指定されている場合、値は要求をマップする方法として応答情報に含まれます。

### 要求本文

ありません。

### Response

HTTP 200 の場合、応答本文は空になります。 HTTP 200 (OK) は、リソースが存在しない場合の適切な応答です。

**Search サービスの取得 API** を使用して、削除中のサービスのステータスをポーリングできます。 ポーリング間隔は 30 秒から 1 分までの間にすることをお勧めします。

### レスポンス ヘッダー

`x ms 要求 id`: サービスによって生成された現在の操作の一意の識別子。

### 応答本文

ありません。

<a name="UpdateService"></a>
### サービスの更新

**サービスの更新**操作は、Search サービスの構成を変更します。 有効な変更にはタグ、パーティション、レプリカの数の変更が含まれます。これらを変更すると、課金対象のイベントとして、サービスに対して検索単位の追加または削除が行われます。 パーティションを既存の検索コーパスを格納するために必要な量より少なくしようとするとエラーが発生し、操作がブロックされます。 サービス トポロジへの変更は時間がかかることがあります。 データの再配置、データセンター内のクラスターの設定または設定解除も時間がかかります。

名前、場所、および sku は変更できません。 これらのプロパティを変更するには、新しいサービスを作成する必要があります。

    PATCH https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

または、PUT を使用することができます。

    PUT https://management.azure.com/subscriptions/[subscriptionId]/resourcegroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

### 要求 URI のパラメーター

`subscriptionId`: が必要です。  `SubscriptionID` Azure ユーザーのです。 この値は、Azure リソース マネージャー API またはポータルから取得できます。

`resourceGroupName`: が必要です。 ユーザーのサブスクリプション内のリソース グループの名前。 この値は、Azure リソース マネージャー API またはポータルから取得できます。

`serviceName`: が必要です。 指定されたリソース グループ内の Search サービスの名前。 サービス名がわからない場合は、Search サービスの一覧表示 (Azure Search API) から一覧を取得できます。

`api バージョン`: が必要です。 この要求に使用されるプロトコルのバージョンを指定します。 現在のバージョンは `2015年-02-28`します。

### 要求ヘッダー

`Content-type`: が必要です。 このヘッダーは application/json に設定します。

`x-ms-クライアントの要求の id`: 省略可能です。 クライアントが生成した、この要求を識別する GUID 値。 この値が指定されている場合、値は要求をマップする方法として応答情報に含まれます。

### 要求本文

    {
      "tags": {
        "key": "value",
        ...
      }, 
        "properties": {
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12
        }
    }

### 要求本文のパラメーター

`タグ`: 省略可能です。 リソースを説明するキーと値のペアの一覧。 これらのタグはリソース グループをまたがってこのリソースを表示およびグループ化する際に使用できます。 1 つのリソースに対して、最大 10 個のタグを指定できます。 各タグには 128 文字以内のキーと、256 文字以内の値が必要です。

`replicaCount`: 省略可能です。 既定値は 1 です。 有効な値は 1 ～ 6 です。 場合にのみ有効 `sku` は `標準`します。

`partitionCount`: 省略可能です。 既定値は 1 です。 有効な値は 1、2、3、4、6、または 12 です。 場合にのみ有効 `sku` は `標準`します。

### Response

操作が成功した場合、HTTP 200 (OK) が返されます。 **Search サービスの取得 API** を使用して、更新中のサービスのステータスをポーリングできます。 ポーリング間隔は 30 秒から 1 分までの間にすることをお勧めします。


### レスポンス ヘッダー

`Content-type`: このヘッダーは常に application/json に設定します。

`x ms 要求 id`: サービスによって生成された現在の操作の一意の識別子。

### 応答本文

応答本文には、更新されたサービス定義が含まれます。 例については、「**Search サービスの取得 API**」を参照してください。


<a name="KeyOps"></a>
## キー操作

Azure Search サービスの認証には、検索サービス URL および api-key の 2 つの情報が必要です。 api-key は、サービスが作成されたときに生成され、サービスがプロビジョニングされた後は必要に応じて再生成できます。 api-key には、次の 2 つの種類があります。

- 管理者キー: すべての操作に対するアクセス権を付与します (サービスあたり最大 2 個)。
- クエリ キー: クエリ要求のみを認証します (サービスあたり最大 50 個)。

Azure Search サービスの管理者キーとクエリ キーをプログラムで管理する機能を利用すると、カスタム ツールの構築、セキュリティ上のベスト プラクティスとしての定期的なキーのロールオーバー、従業員が会社を辞めたときのキーのロールオーバー、ソリューションのデプロイに対してスクリプトまたはプログラムによるアプローチを使用するときにサービスのプロビジョニング中にキーを生成および取得する処理が行えます。

クエリ キーは、取得、作成、および削除できます。 管理者キーの操作は、既存のキーの取得と再生成に制限されています。 管理者キーを削除すると、完全にサービスを利用できなくなる可能性があるため、削除操作は使用できません。

キーは、数字と大文字の英字をランダムに組み合わせた文字列です。 api-key を使用できるのは、そのキーを作成したサービスのみです。また、定期的に変更される可能性があります (セキュリティ上のベスト プラクティスとしてキーのロールオーバー戦略を採用している場合)。

api-key、特に管理者キーは、必ず機密データとして扱ってください。 管理者キーを取得したユーザーは、インデックスのデータを削除または読み取ることができます。

**キーに対する操作**

キーに関連する操作には、次の API があります。

- <a name="ListAdminKey">管理者キーの一覧表示</a>
- <a name="RegenAdminKey">管理者キーを再生成します。</a>
- <a name="CreateQueryKey">クエリ キーを作成します。</a>
- <a name="ListQueryKey">クエリ キーの一覧表示</a>
- <a name="DeleteQueryKey">クエリ キーを削除します。</a>


<a name="ListAdminKey"></a>
## 管理者キーの一覧表示

**管理者キーの一覧表示**操作は、指定した Search サービスのプライマリ管理者キーとセカンダリ管理者キーを返します。 この操作では読み取り/書き込みのキーが返されるため、POST メソッドが使用されます。

    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listAdminKeys?api-version=2015-02-28

管理者キーは、サービスで作成されます。 常にプライマリとセカンダリの 2 つのキーがあります。 これらのキーは再生成できますが、削除することはできません。

### 要求 URI のパラメーター

`subscriptionId`: が必要です。 Azure のユーザーのサブスクリプション Id を指定します。 この値は、Azure リソース マネージャー API またはポータルから取得できます。

`resourceGroupName`: が必要です。 ユーザーのサブスクリプション内のリソース グループの名前。 この値は、Azure リソース マネージャー API またはポータルから取得できます。

`serviceName`: が必要です。 指定されたリソース グループ内の Search サービスの名前。 サービス名がわからない場合は、Search サービスの一覧表示 (Azure Search API) から一覧を取得できます。

`api バージョン`: が必要です。 この要求に使用されるプロトコルのバージョンを指定します。 現在のバージョンは `2015年-02-28`します。

`listAdminKeys`: が必要です。 この操作は、Search サービスのプライマリおよびセカンダリの管理者キーを取得します。

### 要求ヘッダー

`x-ms-クライアントの要求の id`: 省略可能です。 クライアントが生成した、この要求を識別する GUID 値。 この値が指定されている場合、値は要求をマップする方法として応答情報に含まれます。

### 要求本文

ありません。

### Response

操作が成功した場合、HTTP 200 (OK) が返されます。

### レスポンス ヘッダー

`Content-type`: このヘッダーは常に application/json に設定します。

`x ms 要求 id`: サービスによって生成された現在の操作の一意の識別子。

### 応答本文

    {
      "primaryKey": "api key",
      "secondaryKey": "api key"
    }

<a name="RegenAdminKey"></a>
## 管理者キーの再生成

**管理者キーの再生成**操作では、プライマリ キーまたはセカンダリ キーが削除され、再生成されます。 一度に再生成できるのは 1 つのキーのみです。 キーを再生成する際には、サービスに対するアクセス権を維持する方法を考慮します。 セカンダリ キーが存在するので、プライマリ キーをロールオーバーした場合でも利用できるキーがあります。 すべてのサービスには必ず 2 つのキーがあります。 キーは再生成できますが、キーを削除したり、キーなしでサービスを実行したりすることはできません。

    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/regenerateAdminKey/[keyKind]?api-version=2015-02-28

### 要求 URI のパラメーター

`subscriptionId`: が必要です。 Azure のユーザーのサブスクリプション Id を指定します。 この値は、Azure リソース マネージャー API またはポータルから取得できます。

`resourceGroupName`: が必要です。 ユーザーのサブスクリプション内のリソース グループの名前。 この値は、Azure リソース マネージャー API またはポータルから取得できます。

`serviceName`: が必要です。 指定されたリソース グループ内の Search サービスの名前。 サービス名がわからない場合は、Search サービスの一覧表示 (Azure Search API) から一覧を取得できます。

`api バージョン`: が必要です。 この要求に使用されるプロトコルのバージョンを指定します。 現在のバージョンは `2015年-02-28`します。

`regenerateKey`: が必要です。 この操作では、プライマリまたはセカンダリの管理者キーの再生成を指定します。

`keyKind`: が必要です。 再生成するキーを指定します。 有効な値は、次のとおりです。

- `primary`
- `セカンダリ`

### 要求ヘッダー

`Content-type`: が必要です。 このヘッダーは application/json に設定します。

`x-ms-クライアントの要求の id`: 省略可能です。 クライアントが生成した、この要求を識別する GUID 値。 この値が指定されている場合、値は要求をマップする方法として応答情報に含まれます。

### 要求本文

ありません。

### Response

操作が成功した場合、HTTP 200 (OK) が返されます。

### レスポンス ヘッダー

`Content-type`: このヘッダーは常に application/json に設定します。

`x ms 要求 id`: サービスによって生成された現在の操作の一意の識別子。

### 応答本文

    {
      "primaryKey": "api key",
      "secondaryKey": "api key"
    }

### 応答本文の要素

`primaryKey`: プライマリ管理者キーを再生成された場合。

`secondaryKey`: セカンダリ管理者キーを再生成された場合。



<a name="CreateQueryKey"></a>
## クエリ キーの作成

**クエリ キーの作成**操作は Search サービスの新しいクエリ キーを生成します。 サービスごとに最大で 50 個のクエリ キーを作成できます。

    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/createQueryKey/[name]?api-version=2015-02-28

### 要求 URI のパラメーター

`subscriptionId`: が必要です。 Azure のユーザーのサブスクリプション Id を指定します。 この値は、Azure リソース マネージャー API またはポータルから取得できます。

`resourceGroupName`: が必要です。 ユーザーのサブスクリプション内のリソース グループの名前。 この値は、Azure リソース マネージャー API またはポータルから取得できます。

`serviceName`: が必要です。 指定されたリソース グループ内の Search サービスの名前。 サービス名がわからない場合は、Search サービスの一覧表示 (Azure Search API) から一覧を取得できます。

`api バージョン`: が必要です。 この要求に使用されるプロトコルのバージョンを指定します。 現在のバージョンは `2015年-02-28`します。

`createQueryKey`: が必要です。 この操作では、Search サービスのクエリ キーが作成されます。

`name`: 必須。 新しいキーの名前です。

### 要求ヘッダー

`x-ms-クライアントの要求の id`: 省略可能です。 クライアントが生成した、この要求を識別する GUID 値。 この値が指定されている場合、値は要求をマップする方法として応答情報に含まれます。

### 要求本文

ありません。

### Response

応答のステータス コードは、操作が成功した場合、HTTP 200 (OK) です。

### レスポンス ヘッダー

`Content-type`: このヘッダーは常に application/json に設定します。

`x ms 要求 id`: サービスによって生成された現在の操作の一意の識別子。

### 応答本文

    {
      "name": "name of key",
      "key": "api key"
    }

### 応答本文の要素

`名`: クエリ キーの名前。

`キー`: クエリ キーの値。

<a name="ListQueryKey"></a>
## クエリ キーの一覧表示

**クエリ キーの一覧表示**操作は、指定した Search サービスのクエリ キーを返します。 クエリ キーは、Search サービスに対してクエリ API (読み取り専用) の呼び出しを送信するために使用されます。 1 つのサービスにつき最大 50 のクエリ キーを使用できます。

    GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listQueryKeys?api-version=2015-02-28

### 要求 URI のパラメーター

`subscriptionId`: が必要です。 Azure のユーザーのサブスクリプション Id を指定します。 この値は、Azure リソース マネージャー API またはポータルから取得できます。

`resourceGroupName`: が必要です。 ユーザーのサブスクリプション内のリソース グループの名前。 この値は、Azure リソース マネージャー API またはポータルから取得できます。

`serviceName`: が必要です。 指定されたリソース グループ内の Search サービスの名前。 サービス名がわからない場合は、Search サービスの一覧表示 (Azure Search API) から一覧を取得できます。

`api バージョン`: が必要です。 この要求に使用されるプロトコルのバージョンを指定します。 現在のバージョンは `2015年-02-28`します。

`listQueryKeys`: が必要です。 この操作は、Search サービスのクエリ キーを取得します。

### 要求ヘッダー

`x-ms-クライアントの要求の id`: 省略可能です。 クライアントが生成した、この要求を識別する GUID 値。 この値が指定されている場合、値は要求をマップする方法として応答情報に含まれます。

### 要求本文

ありません。

### Response

応答のステータス コードは、操作が成功した場合、HTTP 200 (OK) です。

### レスポンス ヘッダー

`Content-type`: このヘッダーは常に application/json に設定します。

`x ms 要求 id`: サービスによって生成された現在の操作の一意の識別子。

### 応答本文

    {
      "value": [
        {
          "name": "name of key 1",
          "key": "key 1"
        },   
        {   
          "name": "name of key 2",
          "key": "key 2"
        }
      ],
    "nextLink": null
    }

### 応答本文の要素

`名`: クエリ キーの名前。

`キー`: クエリ キーの値。


<a name="DeleteQueryKey"></a>
## クエリ キーの削除

**クエリ キーの削除**操作は、指定されたクエリ キーを削除します。 クエリ キーはオプションであり、読み取り専用クエリに使用します。

    DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/deleteQueryKey/[key]?api-version=2015-02-28

管理者キーとは異なり、クエリ キーは再生成されません。 クエリ キーを再生成するには、キーを削除して再作成します。

### 要求 URI のパラメーター

`subscriptionId`: が必要です。 Azure のユーザーのサブスクリプション Id を指定します。 この値は、Azure リソース マネージャー API またはポータルから取得できます。

`resourceGroupName`: が必要です。 ユーザーのサブスクリプション内のリソース グループの名前。 この値は、Azure リソース マネージャー API またはポータルから取得できます。

`serviceName`: が必要です。 指定されたリソース グループ内の Search サービスの名前。 サービス名がわからない場合は、Search サービスの一覧表示 (Azure Search API) から一覧を取得できます。

`api バージョン`: が必要です。 この要求に使用されるプロトコルのバージョンを指定します。 現在のバージョンは `2015年-02-28`します。

`deleteQueryKey`: が必要です。 この操作では、Search サービスの既存のクエリ キーが削除されます。

`キー`: が必要です。 削除するキー。

### 要求ヘッダー

`x-ms-クライアントの要求の id`: 省略可能です。 クライアントが生成した、この要求を識別する GUID 値。 この値が指定されている場合、値は要求をマップする方法として応答情報に含まれます。

### 要求本文

ありません。

### Response

成功した場合の応答ステータス コードは HTTP 200 (OK) です。

### レスポンス ヘッダー

`Content-type`: このヘッダーは常に application/json に設定します。

`x ms 要求 id`: サービスによって生成された現在の操作の一意の識別子。

### 応答本文

ありません。







