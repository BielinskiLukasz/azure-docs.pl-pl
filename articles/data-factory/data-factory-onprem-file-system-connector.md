<properties 
    pageTitle="ファイル システムとの間でデータを移動する | Azure Data Factory" 
    description="Azure Data Factory を使用してオンプレミスのファイル システムとの間でデータを移動する方法を説明します。" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/09/2015" 
    ms.author="spelluru"/>


# Azure Data Factory を使用してオンプレミスのファイル システムとの間でデータを移動する

この記事では、データ ファクトリのコピー アクティビティを使用してオンプレミスのファイル システムとの間でデータを移動する方法について説明します。 この記事に基づき、 [データ移動アクティビティ](data-factory-data-movement-activities.md) コピー アクティビティとサポートされているデータ ストアの組み合わせでデータ移動の一般概要を説明する記事です。

データ ファクトリは、Data Management Gateway を使用してオンプレミスのファイル システムとの間の接続をサポートします。 参照してください [、オンプレミスの場所とクラウド間のデータ移動](data-factory-move-data-between-onprem-and-cloud.md) Data Management Gateway とステップ バイ ステップ手順については、ゲートウェイを設定する方法について学習します。

**注:** Data Management Gateway 以外に、オンプレミスのファイル システムとの間で通信するために他のバイナリをインストールする必要はありません。

## Linux ファイル共有

次の 2 つの手順を実行して、ファイル サーバーのリンクされているサービスで Linux ファイル共有を使用します。

- インストール [Samba](https://www.samba.org/) Linux サーバーにします。
- Windows サーバーに Data Management Gateway をインストールして構成します。 Linux サーバーへのゲートウェイのインストールはサポートされていません。

## サンプル: オンプレミスのファイル システムから Azure BLOB へのデータのコピー

下のサンプルで確認できる要素:

1.  型のリンクされたサービス [OnPremisesFileServer](data-factory-onprem-file-system-connector.md#onpremisesfileserver-linked-service-properties)します。
2.  の種類のリンクされたサービス [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)
3.  An input [dataset](data-factory-create-datasets.md) of type [FileShare](data-factory-onprem-file-system-connector.md#on-premises-file-system-dataset-type-properties).
4.  An output [dataset](data-factory-create-datasets.md) of type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  [パイプライン](data-factory-create-pipelines.md) を使用するコピー アクティビティの [FileSystemSource](data-factory-onprem-file-system-connector.md#file-share-copy-activity-type-properties) と [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)します。

次のサンプルは、時系列に属するデータを 1 時間ごとにオンプレミスのファイル システムから Azure BLOB にコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

最初の手順として行うセットアップの指示に従って data management gateway、 [、オンプレミスの場所とクラウド間のデータ移動](data-factory-move-data-between-onprem-and-cloud.md) 記事です。

**オンプレミスのファイル サーバーのリンクされたサービス:**

    {
      "Name": "OnPremisesFileServerLinkedService",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
          "userid": "Admin",
          "password": "123456",
          "gatewayName": "mygateway"
        }
      }
    }

ホストの場合、ファイル共有がゲートウェイ コンピューター自体にあれば **Local** または **localhost** を指定できます。 また、**userid** や **password** プロパティを使用するのではなく、**encryptedCredential** プロパティを使用することをお勧めします。 参照してください [ファイル システムのリンクされたサービス](#onpremisesfileserver-linked-service-properties) リンクされたサービスの詳細についてはこれです。

**Azure BLOB ストレージのリンクされたサービス:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**オンプレミスのファイル システムの入力データセット:**

新しいファイルから、時間粒度の特定の日時を反映するパスとファイル名のデータが 1 時間ごとに取得されます。

“external”: ”true” を設定して externalData ポリシーを指定すると、テーブルがデータ ファクトリに対して外部にあり、データ ファクトリのアクティビティでは生成されていないことが Azure Data Factory のサービスに通知されます。

    {
      "name": "OnpremisesFileSystemInput",
      "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
          "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
          "fileName": "{Hour}.csv",
          "partitionedBy": [
            {
              "name": "Year",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyy"
              }
            },
            {
              "name": "Month",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "%M"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "%d"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "%H"
              }
            }
          ]
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }

**Azure BLOB の出力データセット:**

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
          "partitionedBy": [
            {
              "name": "Year",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyy"
              }
            },
            {
              "name": "Month",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "%M"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "%d"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "%HH"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": "\t",
            "rowDelimiter": "\n"
          }
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**コピー アクティビティ:**

上記の入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティがパイプラインに含まれています。 パイプライン JSON 定義で、**source** 型が **FileSystemSource** に設定され、**sink** 型が **BlobSink** に設定されています。

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2015-06-01T18:00:00",
        "end":"2015-06-01T19:00:00",
        "description":"Pipeline for copy activity",
        "activities":[  
          {
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "OnpremisesFileSystemInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "FileSystemSource"
              },
              "sink": {
                "type": "BlobSink"
              }
            },
           "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
         ]
       }
    }

## サンプル: Azure SQL からオンプレミスのファイル システムへのデータのコピー

下のサンプルで確認できる要素:

1.  AzureSqlDatabase 型のリンクされたサービス。
2.  OnPremisesFileServer 型のリンクされたサービス。
3.  AzureSqlTable 型の入力データセット。
3.  FileShare 型の出力データセット。
4.  SqlSource と FileSystemSink を使用するコピー アクティビティを含むパイプライン。

このサンプルはある時系列に属するデータを 1 時間おきに Azure SQL Database のテーブルからオンプレミスのファイル システムにコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Azure SQL のリンクされたサービス:**

    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

**オンプレミスのファイル サーバーのリンクされたサービス:**

    {
      "Name": "OnPremisesFileServerLinkedService",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
          "userid": "Admin",
          "password": "123456",
          "gatewayName": "mygateway"
        }
      }
    }

ホストの場合、ファイル共有がゲートウェイ コンピューター自体にあれば **Local** または **localhost** を指定できます。 また、**userid** や **password** プロパティを使用するのではなく、**encryptedCredential** プロパティを使用することをお勧めします。 参照してください [ファイル システムのリンクされたサービス](#onpremisesfileserver-linked-service-properties) リンクされたサービスの詳細についてはこれです。

**Azure SQL の入力データセット:**

このサンプルでは、Azure SQL で「MyTable」という名前のテーブルを作成し、時系列データ用に「timestampcolumn」という名前の列が含まれているものと想定しています。

設定"external":"true"と指定して externalData ポリシーことを通知 Data Factory サービスの表に、データ ファクトリに対して外部には、データ ファクトリのアクティビティでは生成されません。

    {
      "name": "AzureSqlInput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }

**オンプレミスのファイル システムの出力データセット:**

データは、時間粒度の特定の日時を反映する BLOB のパスの新しいファイルに 1 時間ごとにコピーされます。

    {
      "name": "OnpremisesFileSystemOutput",
      "properties": {
        "type": "FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
          "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
          "fileName": "{Hour}.csv",
          "partitionedBy": [
            {
              "name": "Year",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyy"
              }
            },
            {
              "name": "Month",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "%M"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "%d"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "%HH"
              }
            }
          ]
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }

**コピー アクティビティのあるパイプライン:**
上記の入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティがパイプラインに含まれています。 パイプライン JSON 定義で、**source** 型が **SqlSource** に設定され、**sink** 型が **FileSystemSink** に設定されています。 **SqlReaderQuery** プロパティに指定されている SQL クエリは過去のデータを選択してコピーします。


    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2015-06-01T18:00:00",
        "end":"2015-06-01T20:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureSQLInput"
              }
            ],
            "outputs": [
              {
                "name": "OnpremisesFileSystemOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
              },
              "sink": {
                "type": "FileSystemSink"
              }
            },
           "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 3,
              "timeout": "01:00:00"
            }
          }
         ]
       }
    }

## OnPremisesFileServer のリンクされたサービスのプロパティ

オンプレミスのファイル サーバーのリンクされているサービスで、オンプレミスのファイル システムを Azure Data Factory にリンクできます。 次の表は、オンプレミスのファイル サーバーのリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

 プロパティ| 説明| 必須
-------- | ----------- | --------
 type| type プロパティは、**OnPremisesFileServer** に設定されます。| あり
 host| サーバーのホスト名。使用する '\' 次の例のようにエスケープ文字として: 共有の場合: \\servername、\\\servername を指定します。<p>ファイル システムがゲートウェイ コンピューターにローカルの場合は、Local または localhost を使用しています。ファイル システムがゲートウェイ コンピューターと異なるサーバー上にある場合は、\\\servername を使用します。</p>| はい
 userid| サーバーにアクセスするユーザーの ID を指定します| No (encryptedCredential を選択する場合)
 パスワード| ユーザー (userid) のパスワードを指定します| No (encryptedCredential を選択する場合)
 encryptedCredential| 新規 AzureRmDataFactoryEncryptValue コマンドレットを実行して取得できる暗号化された資格情報を指定<p>**注:** バージョン 0.8.14 の Azure PowerShell を使用する必要があります以上を OnPremisesFileSystemLinkedService に型パラメーターのセットで新規 AzureRmDataFactoryEncryptValue などのコマンドレットを使用するには</p>| No (プレーン テキストでユーザー ID とパスワードを指定する場合)
 gatewayName| Data Factory サービスが、オンプレミスのファイル サーバーへの接続に使用するゲートウェイの名前| あり

参照してください [資格情報の設定とセキュリティ](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security) の詳細については、内部設置型ファイル システム データ ソースの資格情報を設定します。

**例: プレーン テキストでのユーザー名とパスワードの使用**

    {
      "Name": "OnPremisesFileServerLinkedService",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "\\\\Contosogame-Asia",
          "userid": "Admin",
          "password": "123456",
          "gatewayName": "mygateway"
        }
      }
    }

**例: encryptedcredential の使用**

    {
      "Name": " OnPremisesFileServerLinkedService ",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "localhost",
          "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
          "gatewayName": "mygateway"
        }
      }
    }

## オンプレミスのファイル システムのデータセット型のプロパティ

セクションとデータセットの定義に使用できるプロパティの一覧については、次を参照してください。、 [データセットを作成する](data-factory-create-datasets.md) 記事です。データセット JSON の構造、可用性、ポリシーなどのセクションはすべてのデータセット型 (Azure SQL、Azure BLOB、Azure テーブル、オンプレミスのファイル システムなど) で同じです。

typeProperties セクションはデータセット型ごとに異なり、データ ストアのデータの場所や書式などに関する情報を提供します。 **FileShare** 型のデータセットの typeProperties セクションには次のプロパティがあります。

 プロパティ| 説明| 必須
-------- | ----------- | --------
 folderPath| フォルダーへのパス。例: myfolder<p>使用するエスケープ文字 '\' 文字列内の特殊文字にします。例: 場合は folder \subfolder、folder\\\subfolder を指定し、d:\samplefolder d:\\\samplefolder を指定します。</p><p>でこれを組み合わせることができます **partitionBy** にフォルダーをパスのスライスに基づく開始/終了時刻の日時にします。</p>| はい
 fileName| テーブルでフォルダー内の特定のファイルを参照するには、**folderPath** にファイルの名前を指定します。このプロパティの値を指定しないと、テーブルは、フォルダー内のすべてのファイルを指します。<p>、生成されたファイルの名前を次にも見られる出力データセットに fileName が指定されていないときに次の形式: </p><p>データ。<Guid>します。txt (例:: 付けられます-93ff-4c6f-b3be-f69616f1df7a.txt</p>| いいえ
 partitionedBy| partitionedBy を利用して時系列データに動的な folderPath と fileName を指定できます。たとえば、1 時間ごとのデータに対して folderPath がパラメーター化されます。| いいえ
 形式| **TextFormat** と **AvroFormat** の 2 種類の形式がサポートされています。形式の下にある type プロパティをいずれかの値に設定する必要があります。AvroFormat が TextFormat のとき、形式に追加で任意のプロパティを指定できます。詳細については、下にある形式のセクションを参照してください。| いいえ
 fileFilter| すべてのファイルではなく、folderPath 内のファイルのサブセットを選択するために使用するフィルターを指定します。<p>使用可能な値: * (複数の文字) とでしょうか。(1 文字) です。</p><p>例 1:"fileFilter":"* .log"</p>例 2:"fileFilter": 2014 - 1 - ですか。txt"</p><p>**注**: fileFilter は入力の FileShare データセットに適用</p>| いいえ
| compression| データの圧縮の種類とレベルを指定します。サポートされる種類: GZip、Deflate、および BZip2。サポートされるレベル: Optimal および Fastest。参照してください [圧縮サポート](#compression-support) 詳細についてです。| いいえ|

> [AZURE.NOTE] fileName と fileFilter は、同時に使用することができません。

### partitionedBy プロパティの活用

前述のように、partitionedBy を使用して時系列データに動的な folderPath、fileName を指定できます。 これは、Data Factory のマクロ、および指定したデータ スライスの論理的な期間を示す システム変数 SliceStart と SliceEnd で行うことができます。

参照してください [データセットの作成](data-factory-create-datasets.md), 、[スケジュールと実行](data-factory-scheduling-and-execution.md), 、および [パイプラインの作成](data-factory-create-pipelines.md) 時系列データセット、スケジュール設定の詳細について理解する」の記事、スライスします。

#### サンプル 1:

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy": 
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

上記の例では、{Slice} は、指定された形式 (YYYYMMDDHH) で、Data Factory システム変数の SliceStart の値で置換されます。 SliceStart はスライスの開始時刻です。 folderPath はスライスごとに異なります。 例: wikidatagateway/wikisampledataout/2014100103 または wikidatagateway/wikisampledataout/2014100104

#### サンプル 2:

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

上記の例では、SliceStart の年、月、日、時刻が folderPath プロパティと fileName プロパティで使用される個別の変数に抽出されます。

### TextFormat の指定

書式が **TextFormat** に設定されている場合、次の**任意の**プロパティを **typeProperties** セクション内の **Format** セクションで指定できます。

 プロパティ| 説明| 必須
-------- | ----------- | --------
 columnDelimiter| ファイルの列の区切り記号として使用される文字です。既定値はコンマです (,)。| いいえ
 rowDelimiter| ファイルの行の区切り記号として使用される文字です。既定値は [“\r\n”, “\r”,” \n”] のいずれかになります。| いいえ
 escapeChar| コンテンツに表示される列区切り記号のエスケープに使用される特殊文字です。既定値はありません。このプロパティの 2 つ以上の文字を指定する必要があります。<p>たとえば、列区切り記号としてコンマ (,) がテキストにもコンマ文字が必要 (例:「こんにちは, world」)、エスケープ文字として '$' を定義して文字列を使用して「hello$, world」ソースの</p>。<p>EscapeChar と quoteChar のテーブルの両方を指定できません。</p>| いいえ
 quoteChar| この特殊文字は文字列値を引用符で囲むために使用されます。引用符文字内の列区切り文字と行区切り文字は文字列値の一部として処理されます。既定値はありません。このプロパティの 2 つ以上の文字を指定する必要があります。<p>たとえば、列区切り記号としてコンマ (,) がテキストにもコンマ文字が必要 (例: <Hello, world>) を定義することができます '"' 引用符として文字し、文字列を使用して <"Hello, world"> ソースにします。このプロパティは、入力と出力の両方のテーブルに適用されます。</p><p>EscapeChar と quoteChar のテーブルの両方を指定できません。</p>| いいえ
 nullValue| BLOB ファイル コンテンツで null 値を表すために使用する文字です。既定値は “\N” です。> | いいえ
 encodingName| エンコード名の指定。有効なエンコード名の一覧については、Encoding.EncodingName プロパティを参照してください。<p>例: windows-1250 または shift_jis。既定値: utf-8 です。</p>| いいえ

#### サンプル:

次の例は、**TextFormat** の format プロパティの一部を示します。

    "typeProperties":
    {
        "folderPath": "MyFolder",
        "fileName": "MyFileName"
        "format":
        {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": ";",
            "quoteChar": "\"",
            "NullValue": "NaN"
        }
    },

quoteChar ではなく escapeChar を使用するには、quoteChar の行を次で置き換えます。

    "escapeChar": "$",

### AvroFormat の指定

形式が **AvroFormat** に設定されている場合、typeProperties セクション内の Format セクションにプロパティを指定する必要はありません。 例:

    "format":
    {
        "type": "AvroFormat",
    }

後続の Hive テーブルで Avro 形式を使用して、参照してください [Apache Hive のチュートリアル](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe)します。

[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## ファイル共有のコピー アクティビティの type プロパティ

**FileSystemSource** では次のプロパティがサポートされます。

| プロパティ| 説明| 使用できる値| 必須|
| -------- | ----------- | -------------- | -------- |
| recursive| データをサブ フォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。| True、False (既定値)| いいえ|

**FileSystemSink** では次のプロパティがサポートされます。

| プロパティ| 説明| 使用できる値| 必須|
| -------- | ----------- | -------------- | -------- |
| copyBehavior| ソースが BlobSource または FileSystem である場合のコピー動作を定義します。| <p>CopyBehavior プロパティの 3 つの値があります。</p><ul><li>**PreserveHierarchy:** 保持対象のフォルダー、つまり、ソース フォルダーに対するソース ファイルの相対パスのファイル階層はターゲット フォルダーに対するターゲット ファイルの相対パスと同じです</li>。<li>**FlattenHierarchy:** ソース フォルダーからすべてのファイルは、ターゲット フォルダーの最初のレベルになります。ターゲット ファイルは、自動生成された名前になります。</li><li>**MergeFiles:** ソース フォルダーからすべてのファイルを 1 つのファイルにマージします。マージされたファイル名が指定した名前になりますファイル/blob の名前が指定されている場合自動生成されたファイル名がそれ以外の場合、でしょう。</li></ul>| いいえ|

### recursive と copyBehavior の例

このセクションでは、recursive 値と copyBhavior 値の組み合わせごとに、Copy 操作で行われる動作について説明します。

 recursive| copyBehavior| 行われる動作
--------- | ------------ | --------
 true| preserveHierarchy| <p>次の構造のソース フォルダ Folder1 に:</p> <p>Folder1<br/>& nbsp; & nbsp; & nbsp; & nbsp;File1<br/>& nbsp; & nbsp; & nbsp; & nbsp;File2<br/>& nbsp; & nbsp; & nbsp; & nbsp;Subfolder1<br/>& nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; & nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp;ファイル 3 と指定<br/>& nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; & nbsp;File4<br/>& nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; & nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp;File5</p>Folder1 先フォルダーは、ソースと同じ構造になります<p>Folder1<br/>& nbsp; & nbsp; & nbsp; & nbsp;File1<br/>& nbsp; & nbsp; & nbsp; & nbsp;File2<br/>& nbsp; & nbsp; & nbsp; & nbsp;Subfolder1<br/>& nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; & nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp;ファイル 3 と指定<br/>& nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; & nbsp;File4<br/>& nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; & nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp;File5</p>.
 true| flattenHierarchy| <p>次の構造のソース フォルダ Folder1 に:</p> <p>Folder1<br/>& nbsp; & nbsp; & nbsp; & nbsp;File1<br/>& nbsp; & nbsp; & nbsp; & nbsp;File2<br/>& nbsp; & nbsp; & nbsp; & nbsp;Subfolder1<br/>& nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; & nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp;ファイル 3 と指定<br/>& nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; & nbsp;File4<br/>& nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; & nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp;File5</p>Folder1 ターゲットは、次の構造になります: <p>Folder1<br/>(& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; ファイル 1 の名前を自動生成された<br/>(& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; File2 の名前を自動生成された<br/>& nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; ファイル 3 と指定の名前を自動生成された<br/>(& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; File4 の名前を自動生成された<br/>(& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; File5 の名前を自動生成されました。</p>
 true| mergeFiles| <p>次の構造のソース フォルダ Folder1 に:</p> <p>Folder1<br/>& nbsp; & nbsp; & nbsp; & nbsp;File1<br/>& nbsp; & nbsp; & nbsp; & nbsp;File2<br/>& nbsp; & nbsp; & nbsp; & nbsp;Subfolder1<br/>& nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; & nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp;ファイル 3 と指定<br/>& nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; & nbsp;File4<br/>& nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; & nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp;File5</p>Folder1 ターゲットは、次の構造になります: <p>Folder1<br/>& nbsp; & nbsp; & nbsp; & nbsp;File1 + File2 + ファイル 3 と指定 + File4 + 5 ファイルの内容が自動生成されたファイル名を持つ 1 つのファイルにマージします。</p>
 false| preserveHierarchy| <p>次の構造のソース フォルダ Folder1 に:</p> <p>Folder1<br/>& nbsp; & nbsp; & nbsp; & nbsp;File1<br/>& nbsp; & nbsp; & nbsp; & nbsp;File2<br/>& nbsp; & nbsp; & nbsp; & nbsp;Subfolder1<br/>& nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; & nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp;ファイル 3 と指定<br/>& nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; & nbsp;File4<br/>& nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; & nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp;File5</p>Folder1 先フォルダーは、次の構造になります<p>Folder1<br/>& nbsp; & nbsp; & nbsp; (& a) nbsp;File1<br/>& nbsp; & nbsp; & nbsp; & nbsp;File2<br/></p><p>ファイル 3 と指定、File4、File5 と Subfolder1 がデータ型が選択されていない</p>します。
 false| flattenHierarchy| <p>次の構造のソース フォルダ Folder1 に:</p> <p>Folder1<br/>& nbsp; & nbsp; & nbsp; & nbsp;File1<br/>& nbsp; & nbsp; & nbsp; & nbsp;File2<br/>& nbsp; & nbsp; & nbsp; & nbsp;Subfolder1<br/>& nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; & nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp;ファイル 3 と指定<br/>& nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; & nbsp;File4<br/>& nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; & nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp;File5</p>Folder1 先フォルダーは、次の構造になります<p>Folder1<br/>& nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; File1 の名前を自動生成された<br/>(& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; File2 の名前を自動生成された<br/></p><p>ファイル 3 と指定、File4、File5 と Subfolder1 がデータ型が選択されていない</p>します。
 false| mergeFiles| <p>次の構造のソース フォルダ Folder1 に:</p> <p>Folder1<br/>& nbsp; & nbsp; & nbsp; & nbsp;File1<br/>& nbsp; & nbsp; & nbsp; & nbsp;File2<br/>& nbsp; & nbsp; & nbsp; & nbsp;Subfolder1<br/>& nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; & nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp;ファイル 3 と指定<br/>& nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; & nbsp;File4<br/>& nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp; & nbsp; (& a) nbsp; (& a) nbsp; (& a) nbsp;File5</p>Folder1 先フォルダーは、次の構造になります<p>Folder1<br/>& nbsp; & nbsp; & nbsp; (& a) nbsp;File1 + File2 内容は、自動生成されたファイル名を持つ 1 つのファイルに結合されます。ファイル 1 の名前を自動生成された</p><p>ファイル 3 と指定、File4、File5 と Subfolder1 がデータ型が選択されていない</p>します。


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]














