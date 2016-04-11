<properties
    pageTitle="DocumentDB の NoSQL Node.js チュートリアル | Microsoft Azure"
    description="DocumentDB Node.js SDK を使用してノード データベースとコンソール アプリケーションを作成する NoSQL Node.js チュートリアル。 DocumentDB は、JSON 用の NoSQL データベースです。"
    keywords="node.js tutorial, node database"
    services="documentdb"
    documentationCenter="node.js"
    authors="AndrewHoh"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="node"
    ms.topic="hero-article" 
    ms.date="11/18/2015"
    ms.author="anhoh"/>

# NoSQL Node.js チュートリアル: DocumentDB Node.js コンソール アプリケーション  

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

DocumentDB Node.js SDK の Node.js チュートリアルへようこそ。 このチュートリアルでは、Node データベースなどの DocumentDB リソースを作成してクエリするコンソール アプリケーションを作成します。

ここで説明する操作は以下のとおりです。

- DocumentDB アカウントを作成して接続する
- アプリケーションをセットアップする
- ノード データベースの作成
- コレクションを作成する
- JSON ドキュメントを作成する
- コレクションをクエリする
- ノード データベースの削除

時間がなくても 心配はありません。 完全なソリューションは [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started)します。 参照してください [の完全なソリューションの入手](#GetSolution) の簡単な手順です。 

Node.js チュートリアルを完了した後で、このページの上部または下部にある投票ボタンを使用して、フィードバックをお寄せください。 マイクロソフトから直接ご連絡を差し上げて問題がなければ、コメント欄に電子メール アドレスをご記入ください。

それでは始めましょう。

## 前提条件

以下のものがそろっていることを確認してください。

- アクティブな Azure アカウント サインアップすることがあるない場合、 [Azure の無料評価版](http://azure.microsoft.com/pricing/free-trial/)します。
- [Node.js](https://nodejs.org/) バージョン v0.10.29 以降。

## 手順 1: DocumentDB アカウントを作成する

DocumentDB アカウントを作成しましょう。 場合に使用するアカウントを既にある場合は、スキップする [Node.js アプリケーションをセットアップ](#SetupNode)します。

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupNode"></a> 手順 2: Node.js アプリケーションをセットアップする

1. お使いのターミナルを開きます。
2. Node.js アプリケーションを保存するフォルダーまたはディレクトリを見つけます。
3. 次のコマンドを使用して、2 つの空の JavaScript ファイルを作成します。
    - Windows:    
        * **fsutil file createnew app.js 0**
        * **fsutil file createnew config.js 0**
    - Linux/OS X: 
        * **touch app.js**
        * **touch config.js**
4. npm で documentdb モジュールをインストールします。 次のコマンドを使用します。
    * **npm install documentdb --save**

以上です。 これでセットアップは終了です。いくつかのコードの記述を開始しましょう。

##<a id="Config"></a> 手順 3: アプリの構成を設定する

開いている *config.js* 任意のテキスト エディターにします。 

という名前の空のオブジェクトを作成し、 *config* プロパティを設定および *config.endpoint* と *config.authKey* 、DocumentDB エンドポイントと承認キーへ。 これら両方の構成を参照して、 [Azure ポータル](https://portal.azure.com)します。

![Screen shot of the Azure Portal, showing a DocumentDB account, with the ACTIVE hub highlighted, the KEYS button highlighted on the DocumentDB account blade, and the URI, PRIMARY KEY and SECONDARY KEY values highlighted on the Keys blade][keys]

    var config = {}

    config.endpoint = "https://YOUR_ENDPOINT_URI.documents.azure.com:443/";
    config.authKey = "oqTveZeWlbtnJQ2yMj23HOAViIr0ya****YOUR_AUTHORIZATION_KEY****ysadfbUV+wUdxwDAZlCfcVzWp0PQg==";

追加してみましょう、 *データベース id*, 、*コレクション id*, 、および *JSON ドキュメント* に、 *config* オブジェクトです。 設定する場所の下、 *config.endpoint* と *config.authKey* プロパティは、次のコードを追加します。 DocumentDB を使用する場合は、データベースに保存したいデータがある、 [データ移行ツール](documentdb-import-data.md) ドキュメント定義を追加することがなく。

    config.dbDefinition = {"id": "FamilyRegistry"};
    config.collDefinition = {"id": "FamilyCollection"};

    var documents = {
      "Andersen": {
        "id": "AndersenFamily",
        "LastName": "Andersen",
        "Parents": [
          {
            "FirstName": "Thomas"
          },
          {
            "FirstName": "Mary Kay"
          }
        ],
        "Children": [
          {
            "FirstName": "Henriette Thaulow",
            "Gender": "female",
            "Grade": 5,
            "Pets": [
              {
                "GivenName": "Fluffy"
              }
            ]
          }
        ],
        "Address": {
          "State": "WA",
          "County": "King",
          "City": "Seattle"
        }
      },
      "Wakefield":   {
          "id": "WakefieldFamily",
          "Parents": [
            {
              "FamilyName": "Wakefield",
              "FirstName": "Robin"
            },
            {
              "FamilyName": "Miller",
              "FirstName": "Ben"
            }
          ],
          "Children": [
            {
              "FamilyName": "Merriam",
              "FirstName": "Jesse",
              "Gender": "female",
              "Grade": 8,
              "Pets": [
                {
                  "GivenName": "Goofy"
                },
                {
                  "GivenName": "Shadow"
                }
              ]
            },
            {
              "FamilyName": "Miller",
              "FirstName": "Lisa",
              "Gender": "female",
              "Grade": 1
            }
          ],
          "Address": {
            "State": "NY",
            "County": "Manhattan",
            "City": "NY"
          },
          "IsRegistered": false
        }
    };

    config.docsDefinitions = documents;

データベース、コレクション、およびドキュメント定義は、DocumentDB として機能する *データベース id*, 、*コレクション id*, 、およびドキュメントのデータです。

最後に、エクスポート、 *config* オブジェクト内で参照できるように、 *app.js* ファイルです。

    module.exports = config;

##<a id="Connect"></a>手順 4: DocumentDB アカウントに接続する

空で、開く *app.js* テキスト エディターでファイルです。 インポート、 *documentdb* モジュールと新たに作成した *config* モジュールです。

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");

次に、以前に保存したを使ってみましょう *config.endpoint* と *config.authKey* 新しい DocumentClient を作成します。

    var client = new documentClient(config.endpoint, {"masterKey": config.authKey});

これで、DocumentDB アカウントに接続できました。続いては、DocumentDB リソースの使用方法について説明します。

## 手順 5: ノード データベースを作成する
A [データベース](documentdb-resources.md#databases) を使用して作成できる、 [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) の関数、 **DocumentClient** クラスです。 データベースは、コレクションに分割されたドキュメント ストレージの論理上のコンテナーです。 App.js ファイルで、新しいデータベースを作成するための関数を追加、 *id* で指定されている、 *config* オブジェクトです。 最初にでは、同じデータベースを確認する確認を *FamilyRegistry* id が既に存在しません。 存在する場合は、新しいデータベースを作成する代わりに、そのデータベースが返されます。

    var getOrCreateDatabase = function(callback) {
        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: config.dbDefinition.id
            }]
        };

        client.queryDatabases(querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if (results.length === 0) {
                client.createDatabase(config.dbDefinition, function(err, created) {
                    if(err) return callback(err);

                    callback(null, created);
                });
            }
            else {
                callback(null, results[0]);
            }
        });
    };

##<a id="CreateColl"></a>手順 6: コレクションを作成する  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** 価格に影響を持つ新しい S1 コレクションが作成されます。 詳細については、次を参照してください、 [料金のページ](https://azure.microsoft.com/pricing/details/documentdb/)します。

A [コレクション](documentdb-resources.md#collections) を使用して作成できる、 [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) の関数、 **DocumentClient** クラスです。 コレクションには、JSON ドキュメントのほか、関連する JavaScript アプリケーション ロジックが格納されます。 新しく作成されたコレクションにマップする、 [S1 パフォーマンス レベル](documentdb-performance-levels.md)します。 App.js ファイルで、新しいコレクションを作成するための関数を追加、 *id* で指定されている、 *config* オブジェクトです。 再度、皆様の評価いたしますコレクションを作成することを確認して、同じ *FamilyCollection* id が既に存在しません。 存在する場合は、新しいコレクションを作成する代わりに、そのコレクションが返されます。

    var getOrCreateCollection = function(callback) {

        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: config.collDefinition.id
            }]
        };

        var dbUri = "dbs/" + config.dbDefinition.id;

        client.queryCollections(dbUri, querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if (results.length === 0) {
                client.createCollection(dbUri, config.collDefinition, function(err, created) {
                    if(err) return callback(err);
                    callback(null, created);
                });
            }
            else {
                callback(null, results[0]);
            }
        });
    };

##<a id="CreateDoc"></a>手順 7: ドキュメントを作成する
A [ドキュメント](documentdb-resources.md#documents) を使用して作成できる、 [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) の関数、 **DocumentClient** クラスです。 ドキュメントは、ユーザー定義の (ユーザーが自由に定義できる) JSON コンテンツです。 これで、DocumentDB にドキュメントを挿入できます。

App.js に保存される JSON データを含むドキュメントを作成するために関数を次に、追加、 *config* オブジェクトです。 ここでも、同じ ID を持つドキュメントが存在しないことが確認されます。

    var getOrCreateDocument = function(document, callback) {
        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: document.id
            }]
        };

        var collectionUri = "dbs/" + config.dbDefinition.id + "/colls/" + config.collDefinition.id;

        client.queryDocuments(collectionUri, querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if(results.length === 0) {
                client.createDocument(collectionUri, document, function(err, created) {
                    if(err) return callback(err);

                    callback(null, created);
                });
            } else {
                callback(null, results[0]);
            }
        });
    };

ご利用ありがとうございます。 DocumentDB でデータベース、コレクション、およびドキュメントを作成するための関数ができました。

![アカウント、データベース、コレクション、およびドキュメントの間の階層関係を示す図](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

##<a id="Query"></a>手順 8: DocumentDB リソースをクエリする

DocumentDB では [、豊富なクエリ](documentdb-sql-query.md) 各コレクションに格納された JSON ドキュメントに対するします。 次のサンプル コードは、コレクションでドキュメントに対して実行できるクエリを示しています。 次の関数を追加、 *app.js* ファイルです。 以下のとおり、DocumentDB は SQL に似たクエリをサポートしています。 複雑なクエリを構築する方法の詳細については、チェック アウト、 [クエリのプレイ グラウンド](https://www.documentdb.com/sql/demo) と [ドキュメントを照会](documentdb-sql-query.md)します。

    var queryCollection = function(documentId, callback) {
      var querySpec = {
          query: 'SELECT * FROM root r WHERE r.id=@id',
          parameters: [{
              name: '@id',
              value: documentId
          }]
      };

      var collectionUri = "dbs/" + config.dbDefinition.id + "/colls/" + config.collDefinition.id;

      client.queryDocuments(collectionUri, querySpec).toArray(function(err, results) {
          if(err) return callback(err);

          callback(null, results);
      });
    };

次の図は、作成したコレクションに対して DocumentDB SQL クエリ構文がどのように呼び出されるかを示しています。

![クエリのスコープおよび意味を示す図](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

 [FROM](documentdb-sql-query.md/#from-clause) キーワードは、単一のコレクションを DocumentDB クエリのスコープは既にあるため、クエリでは省略可能です。 したがって、"FROM Families f" は "FROM root r" または他の任意の変数名に置き換えることができます。 DocumentDB は、Families、root、または任意の変数名が、既定で現在のコレクションを参照しているものと推測します。

##<a id="DeleteDatabase"></a>手順 9: ノード データベースを削除する

作成したデータベースを削除すると、データベースとすべての子リソース (コレクション、ドキュメントなど) が削除されます。 データベースを削除するには、次のコード スニペットを追加します。

    // WARNING: this function will delete your database and all its children resources: collections and documents
    function cleanup(callback) {
        client.deleteDatabase("dbs/" + config.dbDefinition.id, function(err) {
            if(err) return callback(err);

            callback(null);
        });
    }

##<a id="Build"></a>手順 10: 1 つにまとめる

これで、アプリケーションに必要なすべての関数を設定できました。続いては、関数を呼び出しましょう。

関数呼び出しの順序になります 
    * *getOrCreateDatabase*
        * *getOrCreateCollection*
            * *getOrCreateDocument*
                * *getOrCreateDocument*
                    * *queryCollection*
                        * *クリーンアップ*

コードの下に次のコード スニペットを追加 *app.js*します。

    getOrCreateDatabase(function(err, db) {
        if(err) return console.log(err);
        console.log('Read or created db:\n' + db.id + '\n');

        getOrCreateCollection(function(err, coll) {
            if(err) return console.log(err);
            console.log('Read or created collection:\n' + coll.id + '\n');

            getOrCreateDocument(config.docsDefinitions.Andersen, function(err, doc) {
                if(err) return console.log(err);
                console.log('Read or created document:\n' + doc.id + '\n');

                getOrCreateDocument(config.docsDefinitions.Wakefield, function(err, doc) {
                    if(err) return console.log(err);
                    console.log('Read or created document:\n' + doc.id + '\n');

                    queryCollection("AndersenFamily", function(err, results) {
                        if(err) return console.log(err);
                        console.log('Query results:\n' + JSON.stringify(results, null, '\t') + '\n');

                        cleanup(function(err) {
                            if(err) return console.log(err);
                            console.log('Done.');
                        });
                    });
                });
            });
        });
    });

##<a id="Run"></a>手順 11: Node.js アプリケーションを実行する

これで、Node.js アプリケーションを実行する準備が整いました。

ターミナルでを検索、 *app.js* ファイル、コマンドを実行して: **ノード app.js**

開始したアプリケーションの出力が表示されます。 出力は、次のテキスト例のようなものになるはずです。

    Read or created db:
    FamilyRegistry

    Read or created collection:
    FamilyCollection

    Read or created document:
    AndersenFamily

    Read or created document:
    WakefieldFamily

    Query results:
    [
            {
                    "id": "AndersenFamily",
                    "LastName": "Andersen",
                    "Parents": [
                            {
                                    "FirstName": "Thomas"
                            },
                            {
                                    "FirstName": "Mary Kay"
                            }
                    ],
                    "Children": [
                            {
                                    "FirstName": "Henriette Thaulow",
                                    "Gender": "female",
                                    "Grade": 5,
                                    "Pets": [
                                            {
                                                    "GivenName": "Fluffy"
                                            }
                                    ]
                            }
                    ],
                    "Address": {
                            "State": "WA",
                            "County": "King",
                            "City": "Seattle"
                    },
                    "_rid": "tOErANjwoQcBAAAAAAAAAA==",
                    "_ts": 1444327141,
                    "_self": "dbs/tOErAA==/colls/tOErANjwoQc=/docs/tOErANjwoQcBAAAAAAAAAA==/",
                    "_etag": "\"00001200-0000-0000-0000-5616aee50000\"",
                    "_attachments": "attachments/"
            }
    ]

    Done.

ご利用ありがとうございます。 以上で Node.js チュートリアルが完了し、初めての DocumentDB コンソール アプリケーションが完成しました。 

##<a id="GetSolution"></a>完全なソリューションを取得する
この記事のすべてのサンプルを含む GetStarted ソリューションをビルドするには、以下が必要です。

-   [DocumentDB アカウント][documentdb-create-account]します。
-    [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) ソリューション GitHub で入手できます。

インストール、 **documentdb** npm でモジュールです。 次のコマンドを使用します。
* **npm install documentdb --save**

次に、 *config.js* ファイルを」の説明に従って、config.endpoint および config.authKey の値を更新 [手順 3: アプリの構成を設定](#Config)します。

## 次のステップ

-   さらに複雑な Node.js のサンプルが必要ですか。 参照してください [DocumentDB を使用した Node.js web アプリケーションの構築](documentdb-nodejs-application.md)します。
-   学習方法 [DocumentDB アカウントの監視](documentdb-monitor-accounts.md)します。
-   サンプル データセットに対してクエリを実行、 [クエリのプレイ グラウンド](https://www.documentdb.com/sql/demo)します。
-   開発」のプログラミング モデルについての詳細については、 [DocumentDB ドキュメント ページ](../../services/documentdb/)します。

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png


