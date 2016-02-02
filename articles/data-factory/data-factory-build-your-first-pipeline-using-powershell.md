<properties
    pageTitle="Azure PowerShell を使用した初めての Azure Data Factory パイプラインの作成"
    description="このチュートリアルでは、Azure PowerShell を使用して、サンプルの Azure Data Factory パイプラインを作成します。"
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
    ms.topic="hero-article"
    ms.date="12/08/2015"
    ms.author="spelluru"/>


# Azure PowerShell を使用した初めての Azure Data Factory パイプラインの作成

> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)



この記事では、Azure PowerShell を使用して最初のパイプラインを作成する方法を学習します。 このチュートリアルの手順は次のとおりです。

1.  データ ファクトリを作成する。
2.  リンクされたサービス (データ ストア、計算) とデータセットを作成する。
3.  パイプラインを作成する。

> [AZURE.IMPORTANT] 
> この記事では、Azure Data Factory サービスの概念については説明しません。 サービスの詳細については、次を参照してください。 [Azure Data Factory の概要](data-factory-introduction.md)します。 
> 
> この記事では、すべての Data Factory コマンドレットを取り上げているわけではありません。 参照してください [Data Factory コマンドレット リファレンス](https://msdn.microsoft.com/library/dn820234.aspx) Data Factory コマンドレットに関する包括的なドキュメントについてです。

## 前提条件

チュートリアルの「概要」トピックに記載されている前提条件とは別に、次をインストールする必要があります。

- **Azure PowerShell** 指示に従って [をインストールして、Azure PowerShell を構成する方法](../powershell-install-configure.md) 記事をお使いのコンピューターに Azure PowerShell の最新バージョンをインストールします。

Azure PowerShell を使用している場合 **バージョン < 1.0**, 、記載されているコマンドレットを使用する必要があります [ここ ][cmdlet-reference]します。また、Data Factory コマンドレットを使用する前に、次のコマンドを実行する必要があります。

1. Azure PowerShell を起動し、次のコマンドを実行します。 Azure PowerShell は、このチュートリアルが終わるまで開いたままにしておいてください。 Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。
    1. 実行 **Add-azureaccount** ユーザー名と、Azure ポータルにサインインするために使用するパスワードを入力します。
    2. **Get-AzureSubscription** を実行して、このアカウントのサブスクリプションをすべて表示します。
    3. **Select-AzureSubscription** を実行して、使用するサブスクリプションを選択します。 このサブスクリプションは、Azure ポータルで使用したものと同じである必要があります。
4. Azure Data Factory コマンドレットは AzureResourceManager モードでのみ使用できるため、**Switch-AzureMode AzureResourceManager** を実行して、このモードに切り替えます。


## 手順 1: データ ファクトリを作成する

この手順では、Azure PowerShell を使用して、ADFTutorialDataFactoryPSH という名前の Azure Data Factory を作成します。

1. Azure PowerShell を起動し、次のコマンドを実行します。 Azure PowerShell は、このチュートリアルが終わるまで開いたままにしておいてください。 Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。
    - 実行 **ログイン AzureRmAccount** ユーザー名と、Azure ポータルにサインインするために使用するパスワードを入力します。
    - **Get-AzureSubscription** を実行して、このアカウントのサブスクリプションをすべて表示します。
    - 実行 * * Select-azuresubscription <Name of the subscription>* * を使用するサブスクリプションを選択します。このサブスクリプションは、Azure ポータルで使用したものと同じである必要があります。
3. 次のコマンドを実行して、**ADFTutorialResourceGroup** という名前の Azure リソース グループを作成します。

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    このチュートリアルの一部の手順は、ADFTutorialResourceGroup という名前のリソース グループを使用することを前提としています。 異なるリソース グループを使用する場合は、このチュートリアルで ADFTutorialResourceGroup の代わりにそのリソース グループを使用する必要があります。
4. **New-AzureRmDataFactory** コマンドレットを実行し、DataFactoryMyFirstPipelinePSH という名前の Data Factory を作成します。

        New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH –Location "West US"

    > [AZURE.IMPORTANT] Azure Data Factory の名前はグローバルに一意にする必要があります。 "**"DataFactoryMyFirstPipelinePSH" という名前のデータ ファクトリは使用できません**" というエラーが発生した場合は、名前を変更します (yournameADFTutorialDataFactoryPSH など)。 このチュートリアルの手順の実行中に、この名前を ADFTutorialFactoryPSH の代わりに使用します。 参照してください [Data Factory - 名前付け規則](data-factory-naming-rules.md) Data Factory アーティファクトの名前付け規則についてのトピックです。
    > 
    > データ ファクトリの名前は今後、DNS 名として登録される可能性があるため、一般ユーザーに表示される場合があります。

以降の手順では、このチュートリアルで使用するリンクされたサービス、データセット、およびパイプラインを作成する方法を説明します。

## 手順 2: リンクされたサービスとデータセットを作成する

この手順では、Azure ストレージ アカウントとオンデマンド Azure HDInsight クラスターをデータ ファクトリにリンクした後、Hive 処理からの出力データを表すデータセットを作成します。

### Azure Storage のリンクされたサービスを作成する

1.  次の内容で C:\ADFGetStartedPSH フォルダーに「StorageLinkedService.json」という名前の JSON ファイルを作成します。 ADFGetStartedPSH フォルダーがない場合は作成します。

        {
            "name": "StorageLinkedService",
            "properties": {
                "type": "AzureStorage",
                "description": "",
                "typeProperties": {
                    "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            }
        }

    **accountname** は Azure ストレージ アカウントの名前に、**accountkey** は Azure ストレージ アカウントのアクセス キーに置き換えます。 ストレージ アクセス キーを取得する方法については、次を参照してください。 [表示、コピーおよび再生成するストレージ アクセス キー](http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys)します。

2.  Azure PowerShell で ADFGetStartedPSH フォルダーに切り替えます。
3.  **New-AzureRmDataFactoryLinkedService** コマンドレットを使用して、リンクされたサービスを作成できます。 このコマンドレットと、このチュートリアルで使用する他の Data Factory コマンドレットでは、*ResourceGroupName* および *DataFactoryName* パラメーターの値を渡す必要があります。 または、**Get-AzureRmDataFactory** を使用して **DataFactory** オブジェクトを取得すると、コマンドレットを実行するたびに *ResourceGroupName* と *DataFactoryName* を入力しなくてもオブジェクトを渡すことができます。 **Get-AzureRmDataFactory** コマンドレットの出力を **$df** 変数に割り当てるには、次のコマンドを実行します。

        $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH

4.  これで、**New-AzureRmDataFactoryLinkedService** コマンドレットを実行して、リンクされた **StorageLinkedService** サービスを作成できます。

        New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

    **Get-AzureRmDataFactory** コマンドレットを実行して出力を **$df** 変数に割り当てていない場合は、*ResourceGroupName* および *DataFactoryName* パラメーターの値を次のように指定する必要があります。

        New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

    チュートリアルの途中で Azure PowerShell を閉じた場合、次に Azure PowerShell を起動したときに、**Get-AzureRmDataFactory** コマンドレットを実行してチュートリアルを完了する必要があります。

### Azure HDInsight のリンクされたサービスを作成する

次に、Hive スクリプトの実行に使用するオンデマンド Azure HDInsight クラスター用のリンクされたサービスを作成します。

1.  次の内容で C:\ADFGetStartedPSH に「HDInsightOnDemandLinkedService.json」という名前の JSON ファイルを作成します。

        {
          "name": "HDInsightOnDemandLinkedService",
          "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
              "version": "3.2",
              "clusterSize": 1,
              "timeToLive": "00:30:00",
              "linkedServiceName": "StorageLinkedService"
            }
          }
        }

    次の表に、このスニペットで使用される JSON プロパティの説明を示します。

     プロパティ| 説明
    -------- | -----------
     バージョン| 作成された HDInsight のバージョンが 3.2 になるように指定します。
     ClusterSize| 1 ノードの HDInsight クラスターを作成します。
     TimeToLive| 削除されるまでの HDInsight クラスターのアイドル時間を指定します。
     linkedServiceName| HDInsight によって生成されるログを保存するために使用されるストレージ アカウントを指定します。

2. **New-AzureRmDataFactoryLinkedService** コマンドレットを実行して、HDInsightOnDemandLinkedService という名前のリンクされたサービスを作成します。

        New-AzureRmDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json



### 出力データセットを作成する

次に、Azure BLOB ストレージに格納されるデータを表す出力データセットを作成します。

1.  以下の内容を記述した「OutputTable.json」という名前の JSON ファイルを C:\ADFGetStartedPSH フォルダー内に作成します。

        {
          "name": "AzureBlobOutput",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
              "folderPath": "data/partitioneddata",
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "availability": {
              "frequency": "Month",
              "interval": 1
            }
          }
        }

    前の例では、**AzureBlobOutput** というデータセットを作成し、Hive スクリプトによって生成されるデータの構造を指定しています。 さらに、**data** という BLOB コンテナーと **partitioneddata** というフォルダーに結果を保存することを指定します。 **availability** セクションでは、出力データセットが 1 か月ごとに生成されることを指定します。

2. 次のコマンドを Azure PowerShell で実行して、Data Factory データセットを作成します。

        New-AzureRmDataFactoryDataset $df -File .\OutputTable.json


## 手順 3: 最初のパイプラインを作成する

この手順では、最初のパイプラインを作成します。

1.  以下の内容を記述した「MyFirstPipelinePSH.json」という名前の JSON ファイルを C:\ADFGetStartedPSH フォルダー内に作成します。
    > [AZURE.IMPORTANT] 置換 **storageaccountname** 、JSON でストレージ アカウントの名前に置き換えます。

        {
          "name": "MyFirstPipeline",
          "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
              {
                "type": "HDInsightHive",
                "typeProperties": {
                  "scriptPath": "script/partitionweblogs.hql",
                  "scriptLinkedService": "StorageLinkedService",
                  "defines": {
                    "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
                  }
                },
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "policy": {
                  "concurrency": 1,
                  "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
              }
            ],
            "start": "2014-01-01",
            "end": "2014-01-02"
          }
        }

    前の例では、Hive を使用して HDInsight クラスターのデータを処理する 1 つのアクティビティで構成されるパイプラインを作成します。

    Hive スクリプト ファイル partitionweblogs.hql は、Azure ストレージ アカウント (scriptLinkedService によって指定され、StorageLinkedService という名前) および **script** という名前のコンテナーに格納されます。

    **defines** セクションは、Hive 構成値 (例: ${hiveconf:PartitionedData}) として Hive スクリプトに渡される実行時設定を指定するために使用されます。

    パイプラインの **start** および **end** プロパティでは、パイプラインのアクティブな期間を指定します。

    アクティビティ JSON では、Hive スクリプトがリンクされたサービス **HDInsightOnDemandLinkedService** によって指定されたコンピューターで実行することを指定します。
2. 次のコマンドを実行して、Data Factory テーブルを作成します。

        New-AzureRmDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json

5. これで、Azure PowerShell を使用して最初のパイプラインを作成できました。

### <a name="MonitorDataSetsAndPipeline"></a> データセットとパイプラインを監視します。

このステップでは、Azure PowerShell を使用して、Azure Data Factory の状況を監視します。

1.  **Get-AzureRmDataFactory** を実行して **$df** 変数に出力を割り当てます。

        $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH

2.  **Get-AzureRmDataFactorySlice** を実行し、**EmpSQLTable** のすべてのスライスの詳細を表示します。これは、パイプラインの出力テーブルです。

        Get-AzureRmDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2014-01-01

    ここで指定した StartDateTime がパイプライン JSON で指定されている開始時刻と同じであることに注意してください。 次のような出力が表示されます。

        ResourceGroupName : ADFTutorialResourceGroup
        DataFactoryName   : DataFactoryMyFirstPipelinePSH
        TableName         : AzureBlobOutput
        Start             : 1/1/2014 12:00:00 AM
        End               : 2/1/2014 12:00:00 AM
        RetryCount        : 0
        Status            : InProgress
        LatencyStatus     :
        LongRetryCount    : 0

3.  **Get-AzureRmDataFactoryRun** を実行して、特定のスライスに関するアクティビティの実行の詳細を取得します。

        Get-AzureRmDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2014-01-01

    次のような出力が表示されます。

        Id                  : 4dbc6a07-537d-4005-a53e-6b9a4b844089_635241312000000000_635268096000000000_AzureBlobOutput
        ResourceGroupName   : ADFTutorialResourceGroup
        DataFactoryName     : DataFactoryMyFirstPipelinePSH
        TableName           : AzureBlobOutput
        ProcessingStartTime : 7/7/2015 1:14:18 AM
        ProcessingEndTime   : 12/31/9999 11:59:59 PM
        PercentComplete     : 0
        DataSliceStart      : 1/1/2014 12:00:00 AM
        DataSliceEnd        : 2/1/2014 12:00:00 AM
        Status              : AllocatingResources
        Timestamp           : 7/7/2015 1:14:18 AM
        RetryAttempt        : 0
        Properties          : {}
        ErrorMessage        :
        ActivityName        : RunSampleHiveActivity
        PipelineName        : MyFirstPipeline
        Type                : Script

    スライスが Ready 状態または Failed 状態になるまでこのコマンドレットを実行させたままにできます。 スライスが Ready 状態になったら、BLOB ストレージの data コンテナーの partitioneddata フォルダーで出力データを調べます。 オンデマンド HDInsight クラスターの作成には通常しばらく時間がかかることに注意してください。

参照してください [Data Factory コマンドレット リファレンス](https://msdn.microsoft.com/library/azure/dn820234.aspx) Data Factory コマンドレットに関する包括的なドキュメントについてです。



## 次のステップ

この記事では、オンデマンド Azure HDInsight クラスターで Hive スクリプトを実行する変換アクティビティ (HDInsight アクティビティ) を含むパイプラインを作成しました。 Azure Blob から Azure SQL にデータをコピーするコピー アクティビティを使用する方法を確認するには、次を参照してください。 [チュートリアル: Azure Blob から Azure SQL にデータをコピー](./data-factory-get-started.md)します。



[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx 

