<properties 
    pageTitle="Azure DocumentDB で地理空間データを扱う | Microsoft Azure" 
    description="Azure DocumentDB を使用した空間オブジェクトの作成、インデックス作成、クエリの方法について説明します。" 
    services="documentdb" 
    documentationCenter="" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="10/27/2015" 
    ms.author="arramac"/>
    
# Azure DocumentDB で地理空間データを扱う

この記事の地理空間機能の概要については、 [Azure DocumentDB](http://azure.microsoft.com/services/documentdb/)します。 この記事では次の方法を取り上げています。

- 空間データを Azure DocumentDB に保存する方法
- Azure DocumentDB 内の地理空間データを SQL や LINQ で照会する方法
- DocumentDB の空間インデックスを有効または無効にする方法

参照してください [Github プロジェクト](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Queries.Spatial) コード サンプルについてです。

## 空間データの概要

空間データは、物体の空間における位置と形状を表現します。 ほとんどのアプリケーションにおける空間データは、地球上の物体、つまり地理空間データに対応します。 空間データを使用することで、人物の位置や、関心のある場所、都市や湖の境界を表現することができます。 代表的な使用例に近接検索クエリがあります (例: "現在地付近に存在するコーヒー ショップをすべて検索")。 

### GeoJSON
DocumentDB インデックス作成とを使用して表現が、地理空間ポイントのデータのクエリをサポートしています、 [GeoJSON 仕様](http://geojson.org/geojson-spec.html)します。 GeoJSON データの構造は常に有効な JSON オブジェクトであるため、特殊なツールやライブラリがなくても、DocumentDB を使って保存したりクエリを実行したりすることができます。 DocumentDB SDK には、空間データを簡単に扱うことができるヘルパー クラスとヘルパー メソッドが用意されています。 

### Point、LineString、Polygon
A **ポイント** 空間内の 1 つの位置を表します。 スーパーやキオスクの所在地、自動車、都市などの位置は、地理空間データにおけるポイントによって正確に表されます。  GeoJSON (と DocumentDB) では、ポイントがその座標ペアまたは経度/緯度によって表されます。 次に示したのは、ポイントを表す JSON の例です。

**DocumentDB におけるポイント**

    {
       "type":"Point",
       "coordinates":[ 31.9, -4.8 ]
    }

>[AZURE.NOTE] GeoJSON 仕様経度を指定する 1 つ目と緯度 2 番目です。 他の地図作成アプリケーションと同様、経度と緯度は角度であり、度 (°) の単位で表されます。 経度値は、グリニッジ子午線を基準とする -180°～ 180.0°の範囲で測定され、緯度値は、赤道を基準とする -90.0°～ 90.0°の範囲で測定されます。 
>
> DocumentDB では座標が WGS-84 測地系で解釈されます。 座標参照系の詳細については、以下を参照してください。

このデータは、DocumentDB ドキュメントに埋め込むことができます。次の例では、位置情報データを含んだユーザー プロファイルにデータを埋め込んでいます。

**位置データを DocumentDB に保存したプロファイルの使用例**

    {
       "id":"documentdb-profile",
       "screen_name":"@DocumentDB",
       "city":"Redmond",
       "topics":[ "NoSQL", "Javascript" ],
       "location":{
          "type":"Point",
          "coordinates":[ 31.9, -4.8 ]
       }
    }

GeoJSON は、Point に加え、LineString と Polygon をサポートしています。 **Linestring** 領域とそれらを接続する直線セグメントの 2 つ以上のポイントの系列を表します。 地理空間データでは、幹線道路や川を表すために LineString がよく使用されます。 A **多角形** 閉じた LineString を形成する接続ポイントの境界です。 ポリゴンは通常、湖や行政区 (市区町村、都道府県など) など、自然な形状を表す目的で使用されます。 以下に示したのは、DocumentDB におけるポリゴンの例です。 

**DocumentDB におけるポリゴン**

    {
       "type":"Polygon",
       "coordinates":[
           [ 31.8, -5 ],
           [ 31.8, -4.7 ],
           [ 32, -4.7 ],
           [ 32, -5 ],
           [ 31.8, -5 ]
       ]
    }

>[AZURE.NOTE] GeoJSON 仕様では、いる有効な多角形の提供される最後の座標ペアと同じである、最初は、閉じた図形を作成が必要です。
>
>ポリゴン内のポイントは、反時計回りに指定する必要があります。 時計回りに指定されたポリゴンは、その中の領域を逆にしたものを表します。

ポイント、LineString、Polygon、だけでなく GeoJSON も指定の表現として地理位置情報による任意のプロパティに関連付ける方法と、複数の地理的場所にグループ化する方法の **機能**です。 これらのオブジェクトは有効な JSON であるため、いずれも DocumentDB で保存、処理することができます。 ただし、DocumentDB では、ポイントのインデックス作成のみサポートします。

### 座標参照系

地球の形状は不規則であるため、地理空間データの座標は多く座標参照系 (CRS) で表され、それぞれ独自の基準系と測定単位が存在します。 たとえば、"National Grid of Britain" は、英国ではきわめて精度の高い座標系ですが、英国外では精度が下がります。 

使用中で最も人気のある CRS は現在、世界測地システム  [WGS 84](http://earth-info.nga.mil/GandG/wgs84/)します。 WGS-84 は、GPS 装置や、多くの地図サービス (Google マップ、Bing マップの API など) で使用されています。 DocumentDB でサポートされるのは、WGS-84 CRS を使用した地理空間データのインデックスとクエリだけです。 

## 空間データを使用したドキュメントの作成
GeoJSON の値を含んだドキュメントを作成すると、対応するコレクションのインデックス作成ポリシーに従い、空間インデックスを使用して自動的にインデックスが作成されます。 Python や Node.js など動的に型付けされる言語で DocumentDB SDK を使用している場合、有効な GeoJSON を作成する必要があります。

**地理空間データを含んだドキュメントの作成 (Node.js)**

    var userProfileDocument = {
       "name":"documentdb",
       "location":{
          "type":"Point",
          "coordinates":[ -122.12, 47.66 ]
       }
    };

    client.createDocument(collectionLink, userProfileDocument, function (err, created) {
        // additional code within the callback
    });

.NET (または Java) SDK の場合、Microsoft.Azure.Documents.Spatial 名前空間の新しい Point クラスと Polygon クラスを使って、アプリケーションのオブジェクト内に位置情報を埋め込むことができます。 これらのクラスを使用して簡単に、空間データを GeoJSON にシリアル化したり、逆シリアル化したりすることができます。

**地理空間データを含んだドキュメントの作成 (.NET)**

    using Microsoft.Azure.Documents.Spatial;
    
    public class UserProfile
    {
        [JsonProperty("name")]
        public string Name { get; set; }

        [JsonProperty("location")]
        public Point Location { get; set; }
        
        // More properties
    }
    
    await client.CreateDocumentAsync(
        collection.SelfLink, 
        new UserProfile 
        { 
            Name = "documentdb", 
            Location = new Point (-122.12, 47.66) 
        });

緯度情報と経度情報がなくても、物理的な住所や所在地名 (都市、国など) があれば、Bing マップ REST サービスなどのジオコーディング サービスを使って実際の座標を検索することができます。 詳細については、Bing Maps ジオコーディング [ここ](https://msdn.microsoft.com/library/ff701713.aspx)します。

## 空間データ型のクエリ

地理空間データの挿入方法がわかったら、SQL と LINQ で DocumentDB のデータを検索してみましょう。

### 空間 SQL 組み込み関数
DocumentDB は、以下の Open Geospatial Consortium (OGC) 組み込み関数を使った地理空間検索をサポートしています。 SQL 言語での組み込み関数の完全なセットの詳細についてを参照してください [DocumentDB のクエリ](documentdb-sql-query.md)します。

<table>
<tr>
  <td><strong>使用法</strong></td>
  <td><strong>説明</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>2 つの GeoJSON ポイント式間の距離を返します。</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>第 1 引数に指定された GeoJSON ポイントが、第 2 引数の GeoJSON ポリゴン内に存在するかどうかを示すブール式を返します。</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>指定された GeoJSON ポイントまたはポリゴン式が有効かどうかを示すブール値を返します。</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>指定された GeoJSON ポイントまたはポリゴン式が有効であるかどうかのブール値を含んだ JSON 値を返します。無効である場合はさらに、その理由が文字列値として返されます。</td>
</tr>
</table>

空間関数を使用すると、空間データに対して近接検索クエリを実行することができます。 指定された場所の 30 km 圏内に存在するすべての世帯ドキュメントを ST_DISTANCE 組み込み関数で取得するクエリの例を以下に示します。 

**クエリ**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**結果**

    [{
      "id": "WakefieldFamily"
    }]

インデックス作成ポリシーに空間インデックスを含めた場合、"距離クエリ" はインデックスを使って効率的に実行されます。 空間インデックスの詳細については、以降のセクションを参照してください。 指定されたパスに空間インデックスがない場合でも、`x-ms-documentdb-query-enable-scan` 要求ヘッダーの値を "true" に設定して指定することによって空間クエリを実行することはできます。 .NET では、そのため、オプションを渡すことによって **FeedOptions** 引数を使用したクエリを [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) true に設定します。 

ポイントがポリゴン内に存在するかどうかは、ST_WITHIN を使用してチェックできます。 通常ポリゴンは、郵便番号、都道府県の境界など、自然な形状の範囲を表す目的で使用されます。 インデックス作成ポリシーに空間インデックスを含めた場合、"範囲内" 検索はインデックスを使って効率的に実行されます。 

ST_WITHIN のポリゴン引数に指定できるのは、単一のリングだけです。つまり、環の内側に穴が含まれているポリゴンは指定できません。 チェック、 [DocumentDB の制限](documentdb-limits.md) ST_WITHIN クエリの多角形に使用できるポイントの最大数にします。

**クエリ**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**結果**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE] 場所の値は、いずれかの引数が正しくないか無効である場合に評価されますで指定された場合、DocumentDB クエリでどのように対応していない型は同様 **未定義** とクエリ結果からスキップするドキュメントを評価します。 クエリから結果が返されなかった場合は、ST_ISVALIDDETAILED を実行して、空間データ型が無効である理由をデバッグしてください。     

空間オブジェクトが有効であるかどうかは、ST_ISVALID と ST_ISVALIDDETAILED を使用してチェックできます。 たとえば以下のクエリでは、範囲外の緯度値 (-132.8) を指定して、ポイントの有効性をチェックしています。 ST_ISVALID で返されるのはブール値だけであるのに対し、ST_ISVALIDDETAILED では、ブール値に加え、無効と考えられる理由の文字列が返されます。

** クエリ **

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**結果**

    [{
      "$1": false
    }]

これらの関数を使用して、ポリゴンを検証することもできます。 以下の例では、閉じた形状になっていないポリゴンを ST_ISVALIDDETAILED で検証しています。 

**クエリ**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**結果**

    [{
       "$1": { 
          "valid": false, 
          "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
        }
    }]
    
### .NET SDK での LINQ クエリ

DocumentDB .NET SDK には、LINQ 式の中で使用するための、`Distance()` と `Within()` というスタブ メソッドも用意されています。 これらのメソッド呼び出しは、DocumentDB LINQ プロバイダーによって等価な SQL 組み込み関数呼び出し (それぞれ ST_DISTANCE と ST_WITHIN) に変換されます。 

以下に示したのは、DocumentDB コレクションから、"location" 値が、指定されたポイントの半径 30 km 圏内にあるドキュメントをすべて検索する LINQ クエリの例です。

**距離検索の LINQ クエリ**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(collection.SelfLink)
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

同様に、"location" が、指定されたボックス/ポリゴン内に存在するドキュメントをすべて検索するクエリを以下に示します。 

**範囲検索の LINQ クエリ**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(collection.SelfLink)
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


以上、LINQ と SQL を使ってドキュメントを検索する方法を見てきました。今度は、DocumentDB に対して空間インデックスを構成する方法について見ていきましょう。

## インデックス作成

説明したよう、 [Azure DocumentDB をスキーマに依存しないインデックス](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) ホワイト ペーパーでは、DocumentDB のデータベース エンジンに対するを設計しましたスキーマに依存しない本当にし、JSON の最高品質のサポートを提供します。 書き込みに最適化された DocumentDB のデータベース エンジンは現在、GeoJSON 標準で表された空間データをネイティブに認識できるようになっています。

簡単に言うと、ジオメトリが 2D 平面上の測地座標から投影しを使用してセルに段階的に分割する **quadtree**します。 これらのセルは、内におけるセルの場所に基づいて 1d にマップされて、 **ヒルベルト空間充てん曲線**, 、ポイントの局所性が保護されます。 さらに場所のデータにインデックスを作成、たどりますと呼ばれるプロセス **テセレーション**, 、つまりすべてのセルの場所と交差するが識別され、DocumentDB のインデックス内のキーとして格納します。 クエリの実行時、ポイントやポリゴンなどの引数は、同様にテセレーションを経て関連するセル ID 範囲が抽出された後、インデックスからデータを取得するために使用されます。

/* (すべてのパス) の空間インデックスを含んだインデックス作成ポリシーを指定した場合、コレクション内で検出されたすべてのポイントがインデックス化され、効率的な空間クエリ (ST_WITHIN および ST_DISTANCE) の実行が可能となります。 空間インデックスには精度値がありません。必ず既定の精度値を使用してください。

以下の JSON スニペットは、空間インデックスを有効にしたインデックス作成ポリシーを示したものです。ドキュメント内に見つかったすべての GeoJSON ポイントのインデックスが作成され、空間クエリに使用されます。 Azure ポータルを使用してインデックス作成ポリシーを変更する場合、インデックス作成ポリシーで以下の JSON を指定するとコレクションの空間インデックスを有効にすることができます。

**インデックス作成ポリシーの JSON でコレクションの空間インデックスを有効にする**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Hash",
                   "dataType":"String",
                   "precision":3
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                }
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

以下の .NET コードでは、ポイントを含んだすべてのパスを対象に、空間インデックスを有効にしてコレクションを作成しています。 

**空間インデックスを有効にしてコレクションを作成する**

    IndexingPolicy spatialIndexingPolicy = new IndexingPolicy();
    spatialIndexingPolicy.IncludedPaths.Add(new IncludedPath
    {
        Path = "/*",
        Indexes = new System.Collections.ObjectModel.Collection<Index>()
            {
                new RangeIndex(DataType.Number) { Precision = -1 },
                new RangeIndex(DataType.String) { Precision = -1 },
                new SpatialIndex(DataType.Point)
            }
    });

    Console.WriteLine("Creating new collection...");
    collection = await client.CreateDocumentCollectionAsync(dbLink, collectionDefinition);

以下の例では、ドキュメント内に保存されたすべてのポイントで空間インデックスを利用するように既存のコレクションに変更を加えています。

**既存のコレクションの空間インデックスを有効にする**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = spatialIndexingPolicy; 
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(collection.SelfLink);
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [AZURE.NOTE] GeoJSON 値、ドキュメント内の場所が間違った形式または無効の場合は、し、それはインデックス付けされません空間を照会するため。 ST_ISVALID と ST_ISVALIDDETAILED を使用して location 値を検証できます。

## 次のステップ
DocumentDB の地理空間機能の基本的な使い方を身に付けたら、次に段階に進みましょう。

- 使用したコーディングを開始、 [Github の地理空間の .NET コード サンプル](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Queries.Spatial)
- 上でクエリを実行する地理的に手を取得、 [DocumentDB クエリのプレイ グラウンド](http://www.documentdb.com/sql/demo#geospatial)
- 詳細について [DocumentDB クエリ](documentdb-sql-query.md)
- 詳細について [DocumentDB インデックス作成ポリシー](documentdb-indexing-policies.md)

