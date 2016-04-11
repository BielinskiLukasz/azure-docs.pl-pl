<properties 
    pageTitle="Media Services REST API の概要 | Microsoft Azure" 
    description="Media Services REST API の概要" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="12/05/2015"
    ms.author="juliako"/>


# Media Services REST API の概要 

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Microsoft Azure Media Services は OData ベースの HTTP 要求を受け付けるサービスであり、詳細 JSON または atom+pub で応答が可能です。 Media Services は、Azure 設計ガイドラインに準拠しているため、Media Services に接続するときに各クライアントが使用する必要がある必須 HTTP ヘッダーのセットと、使用できる省略可能なヘッダーのセットがあります。 次のセクションでは、要求を作成したり Media Services から応答を受信したりするときに使用できるヘッダーと HTTP 動詞について説明します。


## Media Services でサポートされている標準の HTTP 要求ヘッダー

Media Services に対して行うそれぞれの呼び出しについて、要求に含める必要がある必須のヘッダーのセットと、含める可能性がある省略可能なヘッダーのセットがあります。 次の表に、それらの必須のヘッダーを示します。


ヘッダー|型|値
---|---|---
Authorization|ベアラ|ベアラは、唯一許容される承認のメカニズムです。 値には、ACS によって提供されるアクセス トークンを含める必要もあります。
x-ms-version|小数点|2.11
DataServiceVersion|小数点|3.0
MaxDataServiceVersion|小数点|3.0



>[AZURE.NOTE] Media Services は、OData を使用して、REST Api を介して基になっているアセット メタデータ リポジトリを公開するため、DataServiceVersion および MaxDataServiceVersion ヘッダーは、すべての要求に含めるかただし、間違っている場合は、その後現在 Media Services は使用中の DataServiceVersion 値を 3.0。

省略可能なヘッダーのセットを次に示します。

ヘッダー|型|値
---|---|---
Date|RFC 1123 の日付|要求のタイムスタンプ
Accept|コンテンツの種類|次のような応答に対する要求のコンテンツの種類:<p> --application/json; odata = verbose<p> -アプリケーション フィードおよび atom + xml<p> 応答には、blob フェッチのように、さまざまなコンテンツの種類があります。正常な応答にはペイロードなどの blob ストリームが含まれます。
Accept-Encoding|Gzip、deflate|GZIP Encoding および DEFLATE Encoding　(該当する場合)。 注: 大きなリソースでは、Media Services はこのヘッダーを無視し、圧縮されていないデータを返す場合があります。
Accept-Language|"en"、"es" など。|応答の優先言語を指定します。
Accept-Charset|"UTF-8"　などの文字の種類|既定値は UTF-8 です。
X-HTTP-Method|HTTP メソッド|PUT や DELETE などの HTTP メソッドをサポートしないクライアントやファイアウォールが、GET 呼び出しを通じてトンネリングされたこれらのメソッドを使用できます。
Content-Type|コンテンツの種類|PUT または POST 要求の本文のコンテンツの種類。
client-request-id|String|指定した要求を識別する、呼び出し元で定義された値。 指定されている場合、この値は、要求をマッピングする方法として、応答メッセージに含まれます。 <p><p>**重要**<p>値は、2096b (2k)　に制限されます。

## Media Services でサポートされている標準の HTTP 応答ヘッダー

要求したリソースと実行を意図した操作によって返されるヘッダーのセットを次に示します。


ヘッダー|型|値
---|---|---
request-id|String|現在の操作、生成されたサービスのための一意の識別子。
client-request-id|String|元の要求の呼び出し元によって指定された識別子 (存在する場合)。
Date|RFC 1123 の日付|要求が処理された日付。
Content-Type|多様|応答本文のコンテンツの種類。
Content-Encoding|多様|Gzip またはデフレート (必要に応じて)。


## Media Services でサポートされている標準の HTTP 動詞

HTTP 要求を行うときに使用できる HTTP 動詞の完全な一覧を次に示します。


動詞|説明
---|---
GET|オブジェクトの現在の値を返します。
POST|提供されるデータに基づくオブジェクトを作成、またはコマンドを送信します。
PUT|オブジェクトの置き換え、または名前付きのオブジェクトの作成を行います (使用している場合)。
削除|オブジェクトを削除します。
MERGE|名前付きプロパティを変更して、既存のオブジェクトを更新します。
HEAD|GET 応答に対するオブジェクトのメタデータを返します。


## Media Services のモデルの検出

Media Services のエンティティを見つけやすくするには、$metadata 操作を使用できます。 これによって、すべての有効なエンティティの種類、エンティティのプロパティ、関連付け、関数、アクションなどを取得できます。 次の例は、URI を作成する方法を示します: https://media.windows.net/API/$ メタデータ。

ブラウザーにメタデータを表示する場合は　URI の末尾に "?api-version=2.x" を追加してください。または、要求に　x-ms-version　を含めないでください。



##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

  
  [Azure Classic Portal]: http://manage.windowsazure.com/



 


