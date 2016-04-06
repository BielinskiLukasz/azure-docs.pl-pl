<properties 
    pageTitle="Azure テーブルとの間でのデータの移動 | Azure Data Factory" 
    description="Azure Data Factory を使用して Azure Table Storage に、または Azure Table Storage からデータを移動する方法を説明します。" 
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
    ms.date="10/06/2015" 
    ms.author="spelluru"/>

# Azure Data Factory を使用した Azure テーブルとの間でのデータの移動

この記事では、Azure Data Factory のコピー アクティビティを使用し、Azure テーブルと別のデータ ストアの間でデータを移動する方法について説明します。 この記事に基づき、 [データ移動アクティビティ](data-factory-data-movement-activities.md) コピー アクティビティとサポートされているデータ ストアの組み合わせでデータ移動の一般概要を説明する記事です。

## サンプル: Azure テーブルから Azure BLOB にデータをコピーする

下のサンプルで確認できる要素:

1.  型のリンクされたサービス [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) (テーブルと blob の両方に使用)。
2.  入力 [データセット](data-factory-create-datasets.md) 型の [AzureTable](#azure-table-dataset-type-properties)します。
3.  出力 [データセット](data-factory-create-datasets.md) 型の [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)します。 
3.   [パイプライン](data-factory-create-pipelines.md) を使用するコピー アクティビティの [AzureTableSource](#azure-table-copy-activity-type-properties) と [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)します。 

このサンプルは Azure テーブルのデフォルト パーティションに属するデータを 1 時間ごとに BLOB にコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Azure Storage のリンクされたサービス:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Azure テーブルの入力データセット:**

このサンプルでは、Azure テーブルに「MyTable」という名前のテーブルが作成されているものと想定しています。
 
“external”: ”true” を設定して externalData ポリシーを指定すると、これがデータ ファクトリに対して外部にあるテーブルで、データ ファクトリのアクティビティでは生成されていないことが Data Factory に通知されます。

    {
      "name": "AzureTableInput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
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

**Azure BLOB の出力データセット:**

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。 

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**コピー アクティビティのあるパイプライン:**

上記の入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティがパイプラインに含まれています。 パイプライン JSON 定義で、 **ソース** に設定されている型 **AzureTableSource** と **シンク** に設定されている型 **BlobSink**します。 指定された SQL クエリ **AzureTableSourceQuery** プロパティ データを選択して、既定のパーティションから 1 時間ごとにコピーします。

    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2014-06-01T18:00:00",
            "end":"2014-06-01T19:00:00",
            "description":"pipeline for copy activity",
            "activities":[  
                {
                    "name": "AzureTabletoBlob",
                    "description": "copy activity",
                    "type": "Copy",
                    "inputs": [
                        {
                            "name": "AzureTableInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "AzureTableSource",
                            "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
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

## サンプル: Azure BLOB から Azure テーブルにデータをコピーする

下のサンプルで確認できる要素:

1.  型のリンクされたサービス [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) (テーブルと blob の両方に使用)
3.  入力 [データセット](data-factory-create-datasets.md) 型の [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)します。
4.  出力 [データセット](data-factory-create-datasets.md) 型の [AzureTable](#azure-table-dataset-type-properties)します。 
4.   [パイプライン](data-factory-create-pipelines.md) を使用するコピー アクティビティの [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) と [AzureTableSink](#azure-table-copy-activity-type-properties)します。 


このサンプルはある時系列に属するデータを 1 時間おきに Azure BLOB から Azure テーブル データベースのテーブルにコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Azure Storage のリンクされたサービス (Azure テーブルと BLOB の両方で使用):**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Azure BLOB の入力データセット:**

データは新しい BLOB から 1 時間おきに取得されます (頻度: 時間、間隔: 1)。 BLOB のフォルダー パスとファイル名は、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダーのパスは、年、月、および日の開始時刻部分を使用し、ファイル名は開始時刻の時間部分を使用します。 "external":"true"」設定は、このテーブルがデータ ファクトリに対して外部にあり、data factory 内のアクティビティでは生成されないことを Data Factory サービスに通知します。
    
    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": "\n"
          }
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

**Azure テーブルの出力データセット:**

このサンプルは Azure テーブルの「MyTable」というテーブルにデータをコピーします。 BLOB CSV ファイルに含めることが予想される数の列で Azure テーブルにテーブルを作成する必要があります。 新しい行は 1 時間ごとにテーブルに追加されます。 

    {
      "name": "AzureTableOutput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**コピー アクティビティのあるパイプライン:**

上記の入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティがパイプラインに含まれています。 パイプライン JSON 定義で、 **ソース** に設定されている型 **BlobSource** と **シンク** に設定されている型 **AzureTableSink**します。 


    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureTableOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "AzureTableSink",
                "writeBatchSize": 100,
                "writeBatchTimeout": "01:00:00"
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

## Azure Storage のリンクされたサービスのプロパティ

Azure Storage のリンクされたサービスを利用し、Azure ストレージ アカウントを Azure Data Factory にリンクできます。 次の表は、Azure Storage のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| type | type プロパティを AzureStorage に設定する必要があります。 | あり |
| connectionString | connectionString プロパティのために Azure ストレージに接続するために必要な情報を指定します。 Azure クラシック ポータルから Azure ストレージの connectionString を取得できます。 | あり |

## Azure テーブル データセットの type プロパティ

セクションとデータセットの定義に使用できるプロパティの一覧については、次を参照してください。、 [データセットを作成する](data-factory-create-datasets.md) 記事です。 データセット JSON の構造、可用性、ポリシーなどのセクションはすべてのデータセット型 (Azure SQL、Azure BLOB、Azure テーブルなど) で同じです。  

typeProperties セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。  **TypeProperties** 型のデータセットのセクション **AzureTable** は次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| tableName | リンクされたサービスが参照する Azure テーブル データベース インスタンスのテーブルの名前です。 | あり

## Azure テーブルのコピー アクティビティの type プロパティ

セクションとアクティビティの定義に使用できるプロパティの一覧については、次を参照してください。、 [パイプラインの作成](data-factory-create-pipelines.md) 記事です。 名前、説明、入力テーブル、出力テーブル、さまざまなポリシーなどのプロパティがあらゆる種類のアクティビティで利用できます。 

一方で、アクティビティの typeProperties セクションで利用できるプロパティはアクティビティの種類により異なり、コピー アクティビティの場合、source と sink の種類によって異なります。

**AzureTableSource** typeProperties セクションで、次のプロパティをサポートしています。

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | -------- 
azureTableSourceQuery | カスタム クエリを使用してデータを読み取ります。 | <p>Azure テーブルのクエリ文字列。</p>**次に例を示します。**<br/> "azureTableSourceQuery":"PartitionKey eq 'DefaultPartitionKey'" <br/><br/>"azureTableSourceQuery":"$$text.format('partitionkey ('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\ 'と PartitionKey le \\'{0:yyyyMMddHH00_9999}\\')', SliceStart)" | いいえ
azureTableSourceIgnoreTableNotFound | テーブルが存在しないという例外を受け入れるかどうかを示します。 | TRUE<br/>FALSE | いいえ |

**AzureTableSink** typeProperties セクションで、次のプロパティをサポートしています。


プロパティ | 説明 | 使用できる値 | 必須  
-------- | ----------- | -------------- | -------- 
azureTableDefaultPartitionKeyValue | シンクで使用できる既定のパーティション キー値です。 | 文字列の値。 | いいえ 
azureTablePartitionKeyName | ユーザーが指定した列名です。列の値がパーティション キーとして使用されます。 指定しない場合、AzureTableDefaultPartitionKeyValue がパーティション キーとして使用されます。 | 列の名前。 | いいえ |
azureTableRowKeyName | ユーザーが指定した列名です。列の値が行キーとして使用されます。 指定しない場合、各行に GUID を使用します。 | 列の名前。 | いいえ  
azureTableInsertType | Azure テーブルにデータを挿入する方法です。 | マージ<br/>replace | いいえ 
writeBatchSize | writeBatchSize または writeBatchTimeout に達したときに、Azure テーブルにデータを挿入します。 | 1 ～ 100 の整数 (単位 = 行数) | いいえ (既定値 = 100) 
writeBatchTimeout | writeBatchSize または writeBatchTimeout に達したときに、Azure テーブルにデータを挿入します。 | (単位 = 時間)例: "00:20:00" (20 分) | No (既定値はストレージ クライアントの既定のタイムアウト値の 90 秒)

### azureTablePartitionKeyName
azureTablePartitionKeyName として宛先列を使用する前に、translator JSON プロパティを使用して、ソース列を宛先列にマップする必要があります。

次の例では、ソース列の DivisionID が宛先列の DivisionID にマップされます。  

    "translator": {
        "type": "TabularTranslator",
        "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
    } 

EmpID は、パーティション キーとして指定されます。 

    "sink": {
        "type": "AzureTableSink",
        "azureTablePartitionKeyName": "DivisionID",
        "writeBatchSize": 100,
        "writeBatchTimeout": "01:00:00"
    }


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Azure テーブルの型のマッピング

説明したように、 [データ移動アクティビティ](data-factory-data-movement-activities.md) 資料、コピー アクティビティは、シンクの次の 2 段階のアプローチで型を source から自動的に変換から自動的に変換を実行します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

Azure テーブル、次のデータを移動するとき [Azure Table サービスで定義されているマップ](https://msdn.microsoft.com/library/azure/dd179338.aspx) から Azure テーブル OData 型と .NET 型が使用されます。 

| OData データ型 | .NET 型 | 詳細 |
| --------------- | --------- | ------- |
| Edm.Binary | byte[] | バイトの配列 (最大 64 KB)。 |
| Edm.Boolean | bool | ブール値。 |
| Edm.DateTime | DateTime | 世界協定時刻 (UTC) を表す 64 ビット値。 サポートされている DateTime 範囲は西暦 1601 年 1 月 1 日 UTC 深夜 12:00 から 始まります。 この範囲は 9999 年 12 月 31 日に終了します。 |
| Edm.Double | double | 64 ビットの浮動小数点値。 |
| Edm.Guid | Guid | グローバルで一意となる 128 ビットの識別子。 |
| Edm.Int32 | Int32 または int | 32 ビットの整数。 |
| Edm.Int64 | Int64 または long |  64 ビットの整数。 |
| Edm.String | String | UTF-16 エンコードの値。 文字列値は最大 64 KB になります。 |

### 型変換の例

次は型変換で Azure BLOB から Azure テーブルにデータをコピーする操作のサンプルです。 

BLOB のデータセットは CSV 形式で、3 つの列を含んでいるとします。 列のうち 1 つは、カスタム日付/時刻形式の datetime 型の列です。名前は週の曜日を表すフランス語の省略形を使用しています。 

次のようにコピー元の BLOB データセットと列の型を定義します。
    
    {
        "name": " AzureBlobInput",
        "properties":
        {
             "structure": 
              [
                    { "name": "userid", "type": "Int64"},
                    { "name": "name", "type": "String"},
                    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
              ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/myfolder",
                "fileName":"myfile.csv",
                "format":
                {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "external": true,
            "availability":
            {
                "frequency": "Hour",
                "interval": 1,
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

上記の Azure テーブル OData 型から .NET 型への型マッピングを所与とすると、次のスキーマで Azure テーブルのテーブルを定義します。 

**Azure テーブル スキーマ:**

列名 | 型
----------- | --------
userid | Edm.Int64
name | Edm.String 
lastlogindate | Edm.DateTime

続いて、次のように Azure テーブルのデータセットを定義します。 基になるデータ ストアで既に指定されているため、“structure” セクションのタイプ情報を指定する必要はありません。

    {
      "name": "AzureTableOutput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

ここでは、データを BLOB から Azure テーブルに移動するときに、Data Factory はカスタム日付/時刻を持つ Datetime フィールドを含むタイプ変換を fr-fr カルチャを使用して自動的に実行します。



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]











