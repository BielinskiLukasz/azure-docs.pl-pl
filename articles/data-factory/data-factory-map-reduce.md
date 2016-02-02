<properties 
    pageTitle="Azure Data Factory から MapReduce プログラムを起動する" 
    description="Azure Data Factory から Azure HDInsight クラスターで MapReduce プログラムを実行してデータを処理する方法について説明します。" 
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


# Data Factory から MapReduce プログラムを起動する

この記事では、**HDInsight の MapReduce アクティビティ**を使用して、Azure Data Factory パイプラインから **MapReduce** プログラムを起動する方法について説明しています。

## はじめに

Azure Data Factory のパイプラインは、リンクされたコンピューティング サービスを使用して、リンクされたストレージ サービス内のデータを処理します。 各アクティビティが特定の処理操作を実行するアクティビティのシーケンスが含まれています。 この記事では、HDInsight MapReduce アクティビティを使用する方法について説明しています。

参照してください [Pig](data-factory-pig-activity) と [Hive](data-factory-hive-activity.md) の詳細については、Windows または Linux ベースの HDInsight で Pig と Hive スクリプトを実行するための Azure data factory パイプラインから HDInsight Pig および Hive アクティビティを使用して、クラスターの記事です。

## HDInsight MapReduce アクティビティの JSON

HDInsight アクティビティの JSON の定義で、以下を設定します。

1. **activity** の **type** を **HDInsight** に設定します。
3. **className** プロパティでクラスの名前を指定します。
4. **jarFilePath** プロパティでファイル名を含む JAR ファイルへのパスを指定します。
5. **jarLinkedService** プロパティで、JAR ファイルを含む Azure BLOB Storage を参照する、リンクされたサービスを指定します。
6. **arguments** セクションで MapReduce プログラムに任意の引数を指定します。 実行時に、MapReduce フレームワークのいくつかの引数 (mapreduce.job.tags など) が表示されます。 MapReduce の引数を使用して、引数を区別するためには、次の例で示すように、引数としてオプションと値の両方を使用を検討してください (- s,--入力 - 出力など、その値の直後にオプション)。

        {
            "name": "MahoutMapReduceSamplePipeline",
            "properties": {
                "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
                "activities": [
                    {
                        "type": "HDInsightMapReduce",
                        "typeProperties": {
                            "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                            "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                            "jarLinkedService": "StorageLinkedService",
                            "arguments": [
                                "-s",
                                "SIMILARITY_LOGLIKELIHOOD",
                                "--input",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                                "--output",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                                "--maxSimilaritiesPerItem",
                                "500",
                                "--tempDir",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                            ]
                        },
                        "inputs": [
                            {
                                "name": "MahoutInput"
                            }
                        ],
                        "outputs": [
                            {
                                "name": "MahoutOutput"
                            }
                        ],
                        "policy": {
                            "timeout": "01:00:00",
                            "concurrency": 1,
                            "retry": 3
                        },
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        },
                        "name": "MahoutActivity",
                        "description": "Custom Map Reduce to generate Mahout result",
                        "linkedServiceName": "HDInsightLinkedService"
                    }
                ],
                "start": "2014-01-03T00:00:00Z",
                "end": "2014-01-04T00:00:00Z",
                "isPaused": false,
                "hubName": "mrfactory_hub",
                "pipelineMode": "Scheduled"
            }
        }




HDInsight MapReduce アクティビティを使用して、HDInsight クラスター上で MapReduce jar ファイルを実行できます。 次のサンプルのパイプラインの JSON 定義では、Mahout JAR ファイルを実行するために HDInsight アクティビティが構成されます。

## GitHub 上のサンプル

HDInsight MapReduce アクティビティを使用するためのサンプルをダウンロードすることができます: [Data Factory Samples on GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample)します。

## Word Count プログラムの実行

このチュートリアルのパイプラインでは、Azure HDInsight クラスターで Word Count Map/Reduce プログラムを実行します。

### リンクされたサービス

最初に、Azure HDInsight クラスターによって使用される Azure Storage を Azure データ ファクトリにリンクする、リンクされたサービスを作成します。 次のコードをコピー/貼り付けする場合は、**アカウント名**と**アカウント キー**を、使用する Azure Storage のアカウント名とアカウント キーに必ず置き換えてください。

#### ストレージにリンクされたサービス

    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
            }
        }
    }

#### Azure HDInsight のリンクされたサービス

次に、Azure HDInsight クラスターを Azure データ ファクトリにリンクする、リンクされたサービスを作成します。 次のコードをコピー/貼り付けする場合は、**HDInsight クラスター名**を使用する HDInsight クラスターの名前に置き換え、ユーザー名とパスワードを変更してください。

    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "StorageLinkedService"
            }
        }
    }

### データセット

#### 出力データセット

この例のパイプラインには入力はありません。 HDInsight MapReduce アクティビティ用の出力データセットを指定する必要があります。 これは、パイプラインのスケジュールを実行するために必要となる単なるダミーのデータセットです。

    {
        "name": "MROutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "fileName": "WordCountOutput1.txt",
                "folderPath": "example/data/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### パイプライン

この例のパイプラインには、HDInsightMapReduce という種類のアクティビティが 1 つだけあります。 JSON の重要なプロパティの一部を次に示します。

 プロパティ| メモ
:-------- | :-----
 type| type には、**HDInsightMapReduce** を設定する必要があります。
 className| クラスの名前は **wordcount** です。
 jarFilePath| 上記のクラスを含む jar ファイルのパス。次のコードをコピー/貼り付けする場合は、クラスターの名前を必ず変更してください。
 jarLinkedService| jar ファイルが含まれるAzure Storage のリンクされたサービス。これは、HDInsight クラスターに関連付けられるストレージです。
 arguments| wordcount プログラムは、入力と出力という2 つの引数を使用します。入力ファイルは davinci.txt ファイルです。
 frequency/interval| これらのプロパティの値は、出力データセットと一致します。
 linkedServiceName| 前に作成した HDInsight のリンクされたサービスを参照します。

    {
        "name": "MRSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run the Word Count Program",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "wordcount",
                        "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "/example/data/gutenberg/davinci.txt",
                            "/example/data/WordCountOutput1"
                        ]
                    },
                    "outputs": [
                        {
                            "name": "MROutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "MRActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-03T00:00:00Z",
            "end": "2014-01-04T00:00:00Z"
        }
    }


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908 
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456 
[adfgetstarted]: data-factory-get-started.md 
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline 
[adftutorial]: data-factory-tutorial.md 
[developer reference]: http://go.microsoft.com/fwlink/?LinkId=516908 
[azure classic portal]: http://portal.azure.com 

