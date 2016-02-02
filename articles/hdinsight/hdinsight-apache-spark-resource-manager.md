<properties 
    pageTitle="リソース マネージャーを使用した HDInsight の Apache Spark クラスターへのリソースの割り当て | Microsoft Azure" 
    description="リソース マネージャーを使用して HDInsight の Spark クラスターのパフォーマンスを向上させる方法について説明します。" 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="paulettm" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/03/2015" 
    ms.author="nitinme"/>



# Azure HDInsight での Apache Spark クラスターのリソースの管理

リソース マネージャーは Spark クラスター ダッシュボードのコンポーネントであり、クラスターで実行される各アプリケーションによって使用されるコアや RAM などのリソースを管理できます。

## <a name="launchrm"></a>リソース マネージャーの起動方法

1. [Azure ポータル](https://ms.portal.azure.com/), 、スタート ボードで、タイルをクリックして、Spark クラスターの場合、スタート画面に固定表示)。 **[すべて参照]** > **[HDInsight クラスター]** でクラスターに移動することもできます。

2. Spark クラスター ブレードで、**[ダッシュ ボード]** をクリックします。 入力を求められたら、Spark クラスターの管理者資格情報を入力します。

    ![リソース マネージャーを起動する](./media/hdinsight-apache-spark-resource-manager/HDI.Cluster.Launch.Dashboard.png "Start Resource Manager")

## <a name="scenariosrm"></a>リソース マネージャーを使用してこれらの問題を修復する方法を教えてください。

ここでは、Spark クラスターでよく発生することがある状況と、リソース マネージャーを使用してそれに対処する方法を説明します。

### HDInsight の Spark クラスターが遅い

HDInsight の Apache Spark クラスターはマルチテナント用に設計されているので、リソースは複数のコンポーネントに分割されます (Notebook、ジョブ サーバーなど)。 これにより、コンポーネントが実行するためのリソースを取得できないことを心配することなくすべての Spark コンポーネントを同時に使用できますが、リソースがフラグメント化されるため各コンポーネントは遅くなります。 これは、ニーズに基づいて調整できます。


### Spark クラスターを含む Jupyter Notebook のみを使用します。どうすればすべてのリソースを割り当てることができますか。

1. **[Spark ダッシュボード]** で **[Spark UI]** タブをクリックして、アプリケーションに割り当てることができる最大コア数と最大 RAM を調べます。

    ![リソースの割り当て](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.Resource.png "Find resources allocated to a Spark cluster")

    上の画面キャプチャでは、割り当て可能な最大コア数は 7 (合計 8 コアのうち 1 つは使用中) であり、割り当て可能な最大 RAM は 9 GB (合計 12 GB の RAM のうち、2 GB はシステム用、1 GB は他のアプリケーションで使用中) です。

    実行しているすべてのアプリケーションを考慮する必要もあります。 実行中のアプリケーションは、**[Spark UI]** タブで確認できます。

    ![アプリケーションの実行](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.Running.Apps.png "Applications running on the cluster")

2. HDInsight Spark ダッシュ ボードで、**[リソース マネージャー]** タブをクリックし、**[既定のアプリケーション コア数]** および **[ワーカー ノードごとの既定の実行プログラム メモリ]** の値を指定します。 他のプロパティは 0 に設定します。

    ![リソースの割り当て](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.Allocate.Resources.png "Allocate resources to your applications")

### Spark クラスターで BI ツールを使用していません。どのようにしてリソースを取り戻せばいいですか。

Thrift サーバーのコア数と Thrift サーバーの実行プログラム メモリを 0 と指定します。 コアやメモリを割り当てないと、Thrift サーバーは**待機**状態になります。

![リソースの割り当て](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.No.Thrift.png "No resources to the thrift server")

## <a name="seealso"></a>関連項目

* [Azure HDInsight での Apache Spark の概要:](hdinsight-apache-spark-overview.md)
* [HDInsight クラスターでの Spark をプロビジョニングします。](hdinsight-apache-spark-provision-clusters.md)
* [BI ツールを使用して HDInsight で Spark を使用して対話型データ分析を実行します。](hdinsight-apache-spark-use-bi-tools.md)
* [Machine learning アプリケーションを構築するための HDInsight の Spark を使用します。](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [リアルタイム ストリーミング アプリケーションを構築するための HDInsight の Spark を使用します。](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)



[hdinsight-versions]: ../hdinsight-component-versioning/ 
[hdinsight-upload-data]: ../hdinsight-upload-data/ 
[hdinsight-storage]: ../hdinsight-use-blob-storage/ 
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/ 
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/ 
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/ 
[azure-management-portal]: https://manage.windowsazure.com/ 
[azure-create-storageaccount]: ../storage-create-storage-account/ 

