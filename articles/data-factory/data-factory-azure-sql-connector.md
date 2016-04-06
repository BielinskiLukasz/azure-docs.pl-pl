<properties 
    pageTitle="Azure SQL との間でのデータの移動 | Azure Data Factory" 
    description="Azure Data Factory を使用して Azure SQL Database に、または Azure SQL Database からデータを移動する方法を説明します。" 
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
    ms.date="11/03/2015" 
    ms.author="spelluru"/>

# Azure Data Factory を使用した Azure SQL との間でのデータの移動

この記事では、Azure Data Factory のコピー アクティビティを利用し、Azure SQL と別のデータ ストアの間でデータを移動する方法について説明します。 この記事に基づき、 [データ移動アクティビティ](data-factory-data-movement-activities.md) コピー アクティビティとサポートされているデータ ストアの組み合わせでデータ移動の一般概要を説明する記事です。

## サンプル: Azure SQL から Azure BLOB にデータをコピーする

下のサンプルで確認できる要素:

1. 型のリンクされたサービス [AzureSqlDatabase](#azure-sql-linked-service-properties)します。
2. 型のリンクされたサービス [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)します。 
3. 入力 [データセット](data-factory-create-datasets.md) 型の [AzureSqlTable](#azure-sql-dataset-type-properties)します。 
4. 出力 [データセット](data-factory-create-datasets.md) 型の [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)します。
4. A [パイプライン](data-factory-create-pipelines.md) を使用するコピー アクティビティの [SqlSource](#azure-sql-copy-activity-type-properties) と [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)します。

このサンプルはある時系列に属するデータを 1 時間おきに Azure SQL データベースのテーブルから BLOB にコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。  

**Azure SQL のリンクされたサービス**

    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

参照してください、 [Azure SQL Linked Service](#azure-sql-linked-service-properties) このリンクされたサービスでサポートされるプロパティの一覧については、セクションです。 

**Azure BLOB ストレージのリンクされたサービス**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

参照してください、 [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) このリンクされたサービスでサポートされるプロパティの一覧については資料です。 

**Azure SQL の入力データセット**

このサンプルでは、Azure SQL で「MyTable」という名前のテーブルを作成し、時系列データ用に「timestampcolumn」という名前の列が含まれているものと想定しています。 

“external”: ”true” を設定して externalData ポリシーを指定すると、テーブルが Data Factory に対して外部にあり、Data Factory のアクティビティでは生成されてないことが Azure Data Factory のサービスに通知されます。

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

参照してください、 [Azure SQL データセットの type プロパティ](#azure-sql-dataset-type-properties) このデータセットの種類でサポートされるプロパティの一覧については、セクションです。  

**Azure BLOB の出力データセット**

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。 

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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

参照してください、 [Azure Blob データセットの type プロパティ](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) このデータセットの種類でサポートされるプロパティの一覧については、セクションです。  

**コピー アクティビティのあるパイプライン**

上記の入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティがパイプラインに含まれています。 パイプライン JSON 定義で、 **ソース** に設定されている型 **SqlSource** と **シンク** に設定されている型 **BlobSink**します。 指定された SQL クエリ、 **SqlReaderQuery** プロパティをコピーするには過去のデータを選択します。

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureSQLInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

> [AZURE.NOTE] 上記の例では、 **sqlReaderQuery** 、SqlSource に指定します。 コピー アクティビティでは、データを取得するために Azure SQL Database ソースに対してこのクエリを実行します。
>  
> または、指定することによってストアド プロシージャを指定することができます、 **sqlReaderStoredProcedureName** と **storedProcedureParameters** (この場合、ストアド プロシージャは、パラメーターを受け取ります)。
>  
> SqlReaderQuery や sqlReaderStoredProcedureName を指定しない場合は、Azure SQL Database に対して実行するクエリを作成するために、データセット JSON の構造セクションで定義された列が使用されます (mytable から column1 と column2 を選択)。 データセット定義に構造がない場合は、すべての列がテーブルから選択されます。 


参照してください、 [Sql ソース](#sqlsource) セクションと [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) SqlSource と BlobSink でサポートされるプロパティの一覧にします。 


## サンプル: Azure BLOB から Azure SQL にデータをコピーする

下のサンプルで確認できる要素:

1.  型のリンクされたサービス [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties)します。
2.  型のリンクされたサービス [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)します。
3.  入力 [データセット](data-factory-create-datasets.md) 型の [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)します。
4.  出力 [データセット](data-factory-create-datasets.md) 型の [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties)します。
4.  A [パイプライン](data-factory-create-pipelines.md) を使用するコピー アクティビティの [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) と [SqlSink](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties)します。

このサンプルはある時系列に属するデータを 1 時間おきに Azure BLOB から Azure SQL データベースのテーブルにコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。 


**Azure SQL のリンクされたサービス**
    
    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

参照してください、 [Azure SQL Linked Service](#azure-sql-linked-service-properties) このリンクされたサービスでサポートされるプロパティの一覧については、セクションです。 

**Azure BLOB ストレージのリンクされたサービス**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

参照してください、 [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) このリンクされたサービスでサポートされるプロパティの一覧については資料です。

**Azure BLOB の入力データセット**

データは新しい BLOB から 1 時間おきに取得されます (頻度: 時間、間隔: 1)。 BLOB のフォルダー パスとファイル名は、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダーのパスは、年、月、および日の開始時刻部分を使用し、ファイル名は開始時刻の時間部分を使用します。 "external":"true"」設定は、このテーブルがデータ ファクトリに対して外部にあり、data factory 内のアクティビティでは生成されないことを Data Factory サービスに通知します。

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
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

参照してください、 [Azure Blob データセットの type プロパティ](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) このデータセットの種類でサポートされるプロパティの一覧については、セクションです。

**Azure SQL の出力データセット**

このサンプルは Azure SQL の「MyTable」というテーブルにデータをコピーします。 BLOB CSV ファイルに含めることが予想される数の列で Azure SQL にテーブルを作成する必要があります。 新しい行は 1 時間ごとにテーブルに追加されます。 

    {
      "name": "AzureSqlOutput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

参照してください、 [Azure SQL データセットの type プロパティ](#azure-sql-dataset-type-properties) このデータセットの種類でサポートされるプロパティの一覧については、セクションです。

**コピー アクティビティのあるパイプライン**

上記の入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティがパイプラインに含まれています。 パイプライン JSON 定義で、 **ソース** に設定されている型 **BlobSource** と **シンク** に設定されている型 **SqlSink**します。

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureSqlOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource",
                "blobColumnSeparators": ","
              },
              "sink": {
                "type": "SqlSink"
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

参照してください、 [Sql シンク](#sqlsink) セクションと [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) SqlSink と BlobSource でサポートされるプロパティの一覧についてです。 


## Azure SQL のリンクされたサービスのプロパティ

次の表は、Azure SQL のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| type | type プロパティを AzureSqlDatabase に設定する必要があります。 | あり |
| connectionString | connectionString プロパティの Azure SQL Database インスタンスに接続するために必要な情報を指定します。 | あり |

**注:** を構成する必要があります。 [Azure SQL Database ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)します。 データベース サーバーを構成する必要があります。 [Azure サービス、サーバーへのアクセスを許可する](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)です。 また、Azure の外部から (たとえば、Data Factory ゲートウェイのあるオンプレミスのデータ ソースから) Azure SQL にデータをコピーする場合、Azure SQL にデータを送信するマシンに適切な IP アドレス範囲を設定する必要があります。 

## Azure SQL データセットの type プロパティ

セクションとデータセットの定義に使用できるプロパティの完全な一覧を参照してください、 [データセットを作成する](data-factory-create-datasets.md) 記事です。 データセット JSON の構造、可用性、ポリシーなどのセクションはすべてのデータセット型 (Azure SQL、Azure BLOB、Azure テーブルなど) で同じです。 

typeProperties セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。  **TypeProperties** 型のデータセットのセクション **AzureSqlTable** は次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| tableName | リンクされたサービスが参照する Azure SQL Database インスタンスのテーブルの名前です。 | あり |

## Azure SQL のコピー アクティビティの type プロパティ

セクションとアクティビティの定義に使用できるプロパティの完全な一覧を参照してください、 [パイプラインの作成](data-factory-create-pipelines.md) 記事です。 名前、説明、入力テーブル、出力テーブル、さまざまなポリシーなどのプロパティがあらゆる種類のアクティビティで利用できます。

> [AZURE.NOTE] コピー アクティビティを使用するを 1 つだけ入力を受け取り、1 つだけの出力が生成されます。

一方で、アクティビティの typeProperties セクションで利用できるプロパティはアクティビティの種類により異なり、コピー アクティビティの場合、source と sink の種類によって異なります。

### SqlSource

Source の種類がコピー アクティビティが発生した場合 **SqlSource** で、次のプロパティが利用 **typeProperties** セクション。

| プロパティ | 説明 | 使用できる値 | 必須 |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | カスタム クエリを使用してデータを読み取ります。 | SQL クエリ文字列。例: select * from MyTable 指定されていない場合に実行される SQL ステートメント: select from MyTable | いいえ |
| sqlReaderStoredProcedureName | ソース テーブルからデータを読み取るストアド プロシージャの名前。 | ストアド プロシージャの名前。 | いいえ |
| storedProcedureParameters | ストアド プロシージャのパラメーター。 | 名前と値のペア。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 | いいえ |

場合、 **sqlReaderQuery** 、SqlSource の指定は、コピー アクティビティでは、データを取得する Azure SQL データベースのソースに対してこのクエリを実行します。 

または、指定することによってストアド プロシージャを指定することができます、 **sqlReaderStoredProcedureName** と **storedProcedureParameters** (この場合、ストアド プロシージャは、パラメーターを受け取ります)。 

SqlReaderQuery や sqlReaderStoredProcedureName を指定しない場合は、Azure SQL Database に対して実行するクエリを作成するために、データセット JSON の構造セクションで定義された列が使用されます (mytable から column1 と column2 を選択)。 データセット定義に構造がない場合は、すべての列がテーブルから選択されます。 

### SqlSource の例

    "source": {
        "type": "SqlSource",
        "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
        "storedProcedureParameters": {
            "stringData": { "value": "str3" },
            "id": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
        }
    }

**ストアド プロシージャの定義:** 

    CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
    (
        @stringData varchar(20),
        @id int
    )
    AS
    SET NOCOUNT ON;
    BEGIN
         select *
         from dbo.UnitTestSrcTable
         where dbo.UnitTestSrcTable.stringData != stringData
        and dbo.UnitTestSrcTable.id != id
    END
    GO


### SqlSink 

**SqlSink** 、次のプロパティをサポートしています。

| プロパティ | 説明 | 使用できる値 | 必須 |
| -------- | ----------- | -------------- | -------- |
| writeBatchTimeout | タイムアウトする前に一括挿入操作の完了を待つ時間です。 | (単位 = 時間) 例: “00:30:00” (30 分) | いいえ | 
| writeBatchSize | バッファー サイズが writeBatchSize に達したときに SQL テーブルにデータを挿入します。 | 整数。 (単位 = 行数) | いいえ (既定値 = 10000)
| sqlWriterCleanupScript | 特定のスライスのデータを消去する方法で実行するコピー アクティビティのユーザー指定のクエリ。 詳細については、下にある繰り返し性のセクションを参照してください。 | クエリ ステートメント。  | いいえ |
| sliceIdentifierColumnName | コピー アクティビティで、自動生成スライス ID を入力する列のユーザー指定の名前。再実行時、特定のスライスのデータを消去するために使用されます。 詳細については、下にある繰り返し性のセクションを参照してください。 | バイナリ (32) のデータ型の列の列名。 | いいえ |
| sqlWriterStoredProcedureName | 対象テーブルにデータをアップサート (更新/挿入) するストアド プロシージャの名前。 | ストアド プロシージャの名前。 | いいえ |
| storedProcedureParameters | ストアド プロシージャのパラメーター。 | 名前と値のペア。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 | いいえ | 
| sqlWriterTableType | 上記のストアド プロシージャで使用するテーブル型のユーザー指定の名前。 コピー アクティビティでは、このテーブル型の一時テーブルでデータを移動できます。 その後、ストアド プロシージャのコードにより、コピーされたデータを既存のデータと結合できます。 | テーブルの種類の名前。 | いいえ |

#### SqlSink の例

    "sink": {
        "type": "SqlSink",
        "writeBatchSize": 1000000,
        "writeBatchTimeout": "00:05:00",
        "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
        "sqlWriterTableType": "CopyTestTableType",
        "storedProcedureParameters": {
            "id": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" },
            "decimalData": { "value": "1", "type": "Decimal" }
        }
    }


[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)] 

[AZURE.INCLUDE [data-factory-sql-invoke-stored-procedure](../../includes/data-factory-sql-invoke-stored-procedure.md)]
 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### SQL Server と Azure SQL の型のマッピング

説明したように、 [データ移動アクティビティ](data-factory-data-movement-activities.md) 記事コピー アクティビティは、シンクの次の 2 段階のアプローチで型を source から自動的に変換からの自動型変換を実行します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

Azure SQL、SQL Server、Sybase との間でデータを移動するとき、SQL 型から .NET 型へのマッピング (およびその逆) に次のマッピングが使用されます。

マッピングは ADO.NET の SQL Server データ型マッピングと同じです。

| SQL Server Databases エンジンの型 | .NET Framework 型 |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| binary | Byte[] |
| bit | Boolean |
| char | String、Char |
| date | DateTime |
| Datetime | DateTime |
| datetime2 | DateTime |
| Datetimeoffset | DateTimeOffset |
| Decimal | Decimal |
| FILESTREAM 属性 (varbinary(max)) | Byte[] |
| Float | Double |
| image | Byte[] | 
| int | Int32 | 
| money | Decimal |
| nchar | String、Char |
| ntext | String、Char |
| numeric | Decimal |
| nvarchar | String、Char |
| real | Single |
| rowversion | Byte[] |
| smalldatetime | DateTime |
| smallint | Int16 |
| smallmoney | Decimal | 
| sql_variant | Object * |
| text | String、Char |
| time | TimeSpan |
| timestamp | Byte[] |
| tinyint | Byte |
| uniqueidentifier | Guid |
| varbinary |  Byte[] |
| varchar | String、Char |
| xml | Xml |


[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]




     











