<properties 
    pageTitle="MySQL からデータを移動する | Azure Data Factory" 
    description="Azure Data Factory を使用して MySQL データベースからデータを移動する方法を説明します。" 
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
    ms.date="11/12/2015" 
    ms.author="spelluru"/>


# Azure Data Factory を使用して MySQL からデータを移動する

この記事では、Azure Data Factory のコピー アクティビティを使用して、MySQL と他のデータ ストアとの間でデータを移動する方法について説明します。 この記事に基づき、 [データ移動アクティビティ](data-factory-data-movement-activities.md) コピー アクティビティとサポートされているデータ ストアの組み合わせでデータ移動の一般概要を説明する記事です。

Data Factory のサービスでは、Data Management Gateway を使用したオンプレミスの MySQL ソースへの接続をサポートします。 参照してください [、オンプレミスの場所とクラウド間のデータ移動](data-factory-move-data-between-onprem-and-cloud.md) Data Management Gateway とゲートウェイを設定する方法の詳細な手順について学習します。

**注:** Azure IaaS VM でホストされている場合でも、MySQL への接続にゲートウェイを利用する必要があります。 クラウドでホストされている MySQL のインスタンスに接続しようとしている場合は、IaaS VM でゲートウェイ インスタンスをインストールすることもできます。

データ ファクトリは、他のデータ ストアから MySQL へのデータの移動ではなく、MySQL から他のデータ ストアへのデータの移動のみをサポートします。

## インストール

MySQL データベースに接続するための Data Management gateway をインストールする必要があります。、 [MySQL コネクタ/net 6.6.5 for Microsoft Windows](http://go.microsoft.com/fwlink/?LinkId=278885) Data Management Gateway と同じシステムにします。

## サンプル: MySQLから Azure BLOB にデータをコピーする

下のサンプルで確認できる要素:

1.  型のリンクされたサービス [OnPremisesMySql](data-factory-onprem-mysql-connector.md#mysql-linked-service-properties)します。
2.  型のリンクされたサービス [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)します。
3.  入力 [データセット](data-factory-create-datasets.md) 型の [RelationalTable](data-factory-onprem-mysql-connector.md#mysql-dataset-type-properties)します。
4.  An output [dataset](data-factory-create-datasets.md) of type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  A [パイプライン](data-factory-create-pipelines.md) を使用するコピー アクティビティの [RelationalSource](data-factory-onprem-mysql-connector.md#mysql-copy-activity-type-properties) と [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)します。

このサンプルは MySQL データベースのクエリ結果のデータを BLOB に 1 時間ごとにコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

最初の手順としての指示に従って data management gateway を設定してください、 [、オンプレミスの場所とクラウド間のデータ移動](data-factory-move-data-between-onprem-and-cloud.md) 記事です。

**MySQL のリンクされたサービス**

    {
      "name": "OnPremMySqlLinkedService",
      "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
          "server": "<server name>",
          "database": "<database name>",
          "schema": "<schema name>",
          "authenticationType": "<authentication type>",
          "userName": "<user name>",
          "password": "<password>",
          "gatewayName": "<gateway>"
        }
      }
    }

**Azure ストレージのリンクされたサービス**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**MySQL の入力データセット**

このサンプルでは、MySQL で「MyTable」という名前のテーブルを作成し、時系列データ用に「timestampcolumn」という名前の列が含まれているものと想定しています。

“external”: ”true” を設定して externalData ポリシーを指定すると、テーブルが Data Factory に対して外部にあり、データ ファクトリのアクティビティでは生成されていないことが Data Factory のサービスに通知されます。

    {
        "name": "MySqlDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremMySqlLinkedService",
            "typeProperties": {},
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }

**Azure BLOB の出力データセット**

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

    {
        "name": "AzureBlobMySqlDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
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
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }

**コピー アクティビティのあるパイプライン**

上記の入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティがパイプラインに含まれています。 パイプライン JSON 定義で、**source** 型が **RelationalSource** に設定され、**sink** 型が **BlobSink** に設定されています。 **query** プロパティに指定されている SQL クエリは過去のデータを選択してコピーします。

    {
        "name": "CopyMySqlToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "MySqlDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobMySqlDataSet"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MySqlToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }

## MySQL のリンクされたサービスのプロパティ

次の表は、MySQL のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ| 説明| 必須|
| -------- | ----------- | -------- |
| type| type プロパティを **OnPremisesMySql** に設定する必要があります| あり|
| server| MySQL サーバーの名前です。| あり|
| database| MySQL データベースの名前です。| はい|
| schema| データベース内のスキーマの名前です。| いいえ|
| authenticationType| MySQL データベースへの接続に使用される認証の種類です。Anonymous、Basic、Windows のいずれかの値になります。| あり|
| username| Basic または Windows 認証を使用している場合は、ユーザー名を指定します。| いいえ|
| パスワード| ユーザー名に指定したユーザー アカウントのパスワードを指定します。| いいえ|
| gatewayName| Data Factory サービスが、オンプレミスの MySQL データベースへの接続に使用するゲートウェイの名前です。| はい|

参照してください [資格情報の設定とセキュリティ](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security) の詳細については、内部設置型 MySQL のデータ ソースの資格情報を設定します。

## MySQL データセットの type プロパティ

セクションとデータセットの定義に使用できるプロパティの一覧については、次を参照してください。、 [データセットを作成する](data-factory-create-datasets.md) 記事です。データセット JSON の構造、可用性、ポリシーなどのセクションはすべてのデータセット型 (Azure SQL、Azure BLOB、Azure テーブルなど) で同じです。

**typeProperties** セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。 **RelationalTable** 型のデータセット (MySQL データセットを含む) の typeProperties セクションには次のプロパティがあります

| プロパティ| 説明| 必須|
| -------- | ----------- | -------- |
| tableName| リンクされたサービスが参照する MySQL Databases インスタンスのテーブルの名前です。| いいえ ( **RelationalSource** の **クエリ** が指定されている場合)|

## MySQL のコピー アクティビティの type プロパティ

セクションとアクティビティの定義に使用できるプロパティの一覧については、次を参照してください。、 [パイプラインの作成](data-factory-create-pipelines.md) 記事です。名前、説明、入力テーブル、出力テーブル、さまざまなポリシーなどのプロパティがあらゆる種類のアクティビティで利用できます。

一方で、アクティビティの typeProperties セクションで利用できるプロパティはアクティビティの種類により異なり、コピー アクティビティの場合、source と sink の種類によって異なります。

コピー アクティビティで、source の種類が **RelationalSource** (MySQL を含む) である場合は、typeProperties セクションで次のプロパティを使用できます。

| プロパティ| 説明| 使用できる値| 必須|
| -------- | ----------- | -------------- | -------- |
| query| カスタム クエリを使用してデータを読み取ります。| SQL クエリ文字列。例: Select * from MyTable。| いいえ (**データセット**の **tableName** が指定されている場合)|

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### MySQL の型マッピング

説明したように、 [データ移動アクティビティ](data-factory-data-movement-activities.md) 資料、コピー アクティビティは、シンクの次の 2 段階のアプローチで型を source から自動的に変換を実行します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

MySQL にデータを移動する場合、MySQL 型から .NET 型に対する次のマッピングが使用されます。

| MySQL Databases 型| .NET Framework 型|
| ------------------- | ------------------- |
| 符号なしの bigint| 小数点|
| bigint| Int64|
| ビット| 小数点|
| BLOB| Byte[]|
| bool| Boolean|
| char| String|
| date| Datetime|
| datetime| Datetime|
| 小数点| 小数点|
| double precision| Double|
| double| Double|
| enum| String|
| float| Single|
| 符号なしの int| Int64|
| int| Int32|
| 符号なしの integer| Int64|
| integer| Int32|
| long varbinary| Byte[]|
| long varchar| String|
| longblob| Byte[]|
| longtext| String|
| mediumblob| Byte[]|
| 符号なしの mediumint| Int64|
| mediumint| Int32|
| mediumtext| String|
| 数値| 小数点|
| real| Double|
| set| String|
| 符号なしの smallint| Int32|
| smallint| Int16|
| テキスト| String|
| time| TimeSpan|
| timestamp| Datetime|
| tinyblob| Byte[]|
| 符号なしの tinyint| Int16|
| tinyint| Int16|
| tinytext| String|
| varchar| String|
| year| Int|


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]








