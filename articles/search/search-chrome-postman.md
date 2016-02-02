<properties
    pageTitle="Azure Search での Chrome Postman の使用 | Microsoft Azure | ホステッド クラウド検索サービス"
    description="ホステッド クラウド検索サービスである Azure Search で Chrome Postman を使用します。Postman をインストールおよび構成するAzure Search インデックスを作成する。Postman を使用してドキュメントをインデックスにポストし、インデックスに対してクエリを実行します。"
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="11/10/2015"
    ms.author="heidist"/>


# Azure Search で Chrome Postman を使用する方法

> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [Postman](search-chrome-postman.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)


[Postman](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm "Chrome Postman") は Azure Search などのサービスを使用する REST ベースの API で効率的に作業を Google Chrome の一部として提供されるツールです。 Postman を使用すれば、コードを記述しなくても、Postman を介して API 呼び出しを送信することによって検索インデックスの作成および検索インデックスに対するクエリを容易に実行することができます。 この方法では、API について効率的に学習し、新しい機能を試すことができます。

![][1]

## 必要条件

Azure Search サービスを備えている必要があります。 Azure Search を使用するカスタム アプリケーションで必要になるため、URL、サービスと、管理者に `api キー` インデックスを作成できるようにします。 Search サービスの値を取得する方法については、次を参照してください。 [、ポータルでサービスを作成](search-create-service-portal.md)します。

## Postman をインストールするには

Postman をダウンロードするを参照してください、 [Google Chrome ストア](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm)します。 このページのリンクを使用して、Postman の REST クライアントをダウンロードしてインストールすることができます。 インストール後は、Chrome アプリ ランチャーから Postman を起動できます。

![][2]

## Azure Search に対してクエリを実行するように Postman を構成するには

Postman を構成するのには、次の手順を実行します。

1. "Enter request URL here (要求 URL をここに入力)" と表示されている場所に、Azure Search サービスの URL を入力します。
2. URL に付加: `ですか? api-version = 2015年-02-28`します。 別の API バージョンを指定することもできます。 参照してください [Azure Search サービスのバージョン管理](https://msdn.microsoft.com/library/azure/dn864560.aspx) 詳細です。
3. いることを確認 `取得` を選択します。
4. **[ヘッダー]** ボタンをクリックします。
5. 次の値を入力します。
    - `api キー`: [管理者キー]
    - `Content-type`: `、application/json; charset = utf-8`
6. **[送信]** をクリックして、Azure Search に REST 呼び出しを発行し、JSON 応答を表示します。

![][3]

## Postman で Azure Search インデックスを作成するには

次に、REST 呼び出しを発行して新しい Azure Search インデックスを作成することで、前の手順で実行した内容を発展させます。 前の呼び出しとは異なり、インデックスの作成には HTTP PUT と、インデックス スキーマの定義を含む JSON ドキュメントが必要です。 このサンプルでは、ハイキング トレイルの一覧を格納するインデックスを作成します。 これを行うには、次の手順を実行します。

1. URL を変更して: `https://[SEARCH SERVICE].search.windows.net/indexes/trails?api-version=2015-02-28` 検索サービス名を使用します。
2. 要求の種類から変更 `取得` に `配置`します。
3. RAW 本文のコンテンツに、次の JSON を入力します。

        {
        "name": "trails",
        "fields": [
        {"name": "id", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "name", "type": "Edm.String"},
        {"name": "county", "type": "Edm.String"},
        {"name": "elevation", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"} ]
        }

4. [**送信**] をクリックします。

![][4]

## Postman で Azure Search インデックスにドキュメントをポストするには

インデックスが作成されたので、ドキュメントを読み込むことができます。 そのために、米国地質調査所 (USGS) のデータセットにある 5 つのトレイルのデータを使用して、ドキュメントのグループをバッチでポストします。

1. URL を変更して: `https://[SEARCH SERVICE].windows.net/indexes/trails/docs/index?api-version=2015-02-28` 検索サービス名を使用します。 作成したインデックスへのパスが URL に含まれることに注意してください。
2. HTTP の種類に変更 `POST`します。
3. RAW 本文のコンテンツに、次の JSON を入力します。

        {
          "value": [
            {"@search.action": "upload", "id": "233358", "name": "Pacific Crest National Scenic Trail", "county": "San Diego", "elevation":1294, "location": { "type": "Point", "coordinates": [-120.802102,49.00021] }},
            {"@search.action": "upload", "id": "801970", "name": "Lewis and Clark National Historic Trail", "county": "Richland", "elevation":584, "location": { "type": "Point", "coordinates": [-104.8546903,48.1264084] }},
            {"@search.action": "upload", "id": "1144102", "name": "Intake Trail", "county": "Umatilla", "elevation":1076, "location": { "type": "Point", "coordinates": [-118.0468873,45.9981939] }},
            {"@search.action": "upload", "id": "1509897", "name": "Burke Gilman Trail", "county": "King", "elevation":10, "location": { "type": "Point", "coordinates": [-122.2754036,47.7059315] }},
            {"@search.action": "upload", "id": "1517508", "name": "Cavanaugh-Oso Truck Trail", "county": "Skagit", "elevation":339, "location": { "type": "Point", "coordinates": [-121.9470829,48.2981608] }}
          ]
        }

4. [**送信**] をクリックします。

![][5]

## Postman でインデックスのクエリを実行するには

最後の手順では、インデックスをクエリして、語 *trail* に対する簡単なフルテキストの検索要求を発行します。

1. URL で、次を入力します: `https://[SEARCH SERVICE].search.windows.net/indexes/trails/docs?api-version=2015-02-28 と検索の軌跡を =` 検索サービス名を使用します。URL には、通知、 `検索` クエリ パラメーターおよび検索語句が *証跡*します。
2. HTTP 要求の種類を変更 `取得`します。
3. [**送信**] をクリックします。

応答には、Azure Search から返された JSON 検索結果が表示されます。

![][6]

## 次のステップ

これで、Postman での Azure Search の使用に関するすべての基本事項について説明しました。次の手順で役立ついくつかの事項を次に示します。

1. Postman をサポートしています `コレクション`, 、発行された要求を頻繁に保存する便利な手段であります。 コレクションを他のユーザーと共有して、Postman の独自のコピーで発行することができます。
2. Azure Search のドキュメントでを HTTP 要求の種類を書き留めることを確認 (`取得`, 、`配置`, など) の各呼び出しに関連付けられているし、Postman では必要に応じて変更します。

REST API のドキュメントを参照して [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx)します。

アクセスすることも、 [ビデオとチュートリアルの一覧](search-video-demo-tutorial-list.md) 他の例についてです。



[1]: ./media/search-chrome-postman/full_postman_client.png 
[2]: ./media/search-chrome-postman/postman.png 
[3]: ./media/search-chrome-postman/configure.png 
[4]: ./media/search-chrome-postman/create_index.png 
[5]: ./media/search-chrome-postman/upload_documents.png 
[6]: ./media/search-chrome-postman/query.png 

