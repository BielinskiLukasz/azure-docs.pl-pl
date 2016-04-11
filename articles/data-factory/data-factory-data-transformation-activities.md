<properties 
    pageTitle="データ変換のアクティビティ | Microsoft Azure" 
    description="Azure Data Factory サービスを使用してデータを変換、分析する方法について説明します。" 
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
    ms.date="11/04/2015" 
    ms.author="spelluru"/>

# Azure Data Factory を使用した変換と分析

## 概要
Azure Data Factory の変換アクティビティにより、未加工のデータを変換して処理することで、予測や把握が容易になります。 変換アクティビティは、Azure HDInsight クラスターや Azure Batch などのコンピューティング環境で実行されます。 Azure Data Factory に追加できるは次の変換アクティビティをサポートしている [パイプライン](data-factory-create-pipelines.md) か、個別にまたは他のアクティビティと連結します。


変換アクティビティ |  Compute 環境 
:----------------------- | :--------------------
[Hive](data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Pig](data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop ストリーミング](data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[Machine Learning のアクティビティ: バッチの実行と更新リソース](data-factory-azure-ml-batch-execution-activity.md) | Azure VM 
[ストアド プロシージャ](data-factory-stored-proc-activity.md) | Azure SQL |
[Data Lake Analytics U-SQL](data-factory-usql-activity.md) | Azure Data Lake Analytics 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] または Azure Batch
   

変換アクティビティを定義するときには、コンピューティング環境のリンクされたサービスを作成したうえで、そのサービスを使用する必要があります。 Data Factory でサポートされているコンピューティング環境は 2 種類あります。 

1. **オンデマンドで**: ここで、コンピューティング環境には完全に Data Factory でが管理します。 データを処理するためのジョブが送信される前に Data Factory サービスにより自動的に作成され、ジョブの完了時に削除されます。 ユーザーは、ジョブの実行、クラスターの管理、ブートストラップ アクションなどについて、オンデマンドのコンピューティング環境の詳細設定を構成および制御できます。 
2. **Bring Your Own**: この場合は、Data Factory のリンクされたサービスとして、独自のコンピューティング環境 (HDInsight クラスターなど) を登録できます。 このコンピューティング環境はユーザーが自分で管理することになります。Data Factory サービスは、アクティビティを実行にこの環境を使用します。 

参照してください [コンピューティングのリンクされたサービス](data-factory-compute-linked-services.md) Data Factory でサポートされているリンクされたコンピューティング サービスについて学習します。 


