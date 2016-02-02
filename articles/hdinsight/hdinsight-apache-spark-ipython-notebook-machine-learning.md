<properties 
    pageTitle="Apache Spark を使用した HDInsight での Machine Learning アプリケーションの作成 | Microsoft Azure" 
    description="Apache Spark で Notebook を使用して Machine Learning アプリケーションを作成する手順を説明します" 
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
    ms.date="09/30/2015" 
    ms.author="nitinme"/>



# Azure HDInsight の Apache Spark を使用した Machine Learning アプリケーションの構築

HDInsight の Apache Spark クラスターを使用して Machine Learning アプリケーションを作成する方法を説明します。 この記事では、クラスターで使用できる Jupyter Notebook を使用してアプリケーションを作成およびテストする方法を説明します。 このアプリケーションでは、すべてのクラスターにおいて既定で利用可能なサンプル HVAC.csv データを使用します。

**前提条件:**

次のものが必要です。

- Azure サブスクリプション。 参照してください [取得 Azure 無料試用版](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)します。
- Apache Spark クラスター。 手順については、次を参照してください。 [Azure HDInsight でのクラスターの Apache Spark のプロビジョニング](hdinsight-apache-spark-provision-clusters.md)します。

## <a name="data"></a>データを表示します。

アプリケーションの作成を始める前に、データの構造およびデータに対して実行する分析の種類を説明します。

この記事では、すべての HDInsight クラスターにおいて既定で使用できる **HVAC.csv** サンプル データ ファイル (**\HdiSamples\SensorSampleData\hvac**) を使用します。 CSV ファイルをダウンロードして開き、データのスナップショットを取得します。

![HVAC データのスナップショット](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.ML.Show.Data.png "Snapshot of the HVAC data")

データは、HVAC システムがインストールされているビルの目標温度と実際の温度を示します。 **[System]** 列はシステム ID を表し、**[SystemAge]** 列は HVAC システムがビルに設置されてからの年数を表します。

このデータを使用し、システム ID とシステム使用年数から得られる目標温度を基にしてビルが暑すぎるか寒すぎるかを予測します。

## <a name="app"></a>Spark MLlib を使用して機械学習アプリケーションを作成します。

1. [Azure ポータル](https://portal.azure.com/), 、スタート ボードで、タイルをクリックして、Spark クラスターの場合、スタート画面に固定表示)。 **[すべて参照]** > **[HDInsight クラスター]** でクラスターに移動することもできます。

2. Spark クラスター ブレードで、**[クイック リンク]** をクリックし、**[クラスター ダッシュボード]** ブレードで **[Jupyter Notebook]** をクリックします。 入力を求められたら、クラスターの管理者資格情報を入力します。
    > [AZURE.NOTE] ブラウザーで次の URL を開き、クラスターの Jupyter Notebook にアクセスすることもできます。 __CLUSTERNAME__ をクラスターの名前に置き換えます。
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 新しい Notebook を作成します。 **[新規]** をクリックし、**[Python 2]** をクリックします。

    ![新しい Jupyter Notebook を作成します](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.Note.Jupyter.CreateNotebook.png "Create a new Jupyter notebook")

3. Untitled.pynb という名前の新しい Notebook が作成されて開かれます。 上部の Notebook 名をクリックし、わかりやすい名前を入力します。

    ![Notebook の名前を指定します](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.Note.Jupyter.Notebook.Name.png "Provide a name for the notebook")

3. Machine Learning アプリケーションの作成を始めます。 このアプリケーションでは、Spark ML パイプラインを使用して、ドキュメントの分類を実行します。 パイプラインでは、ドキュメントを単語に分割し、単語を数値特徴ベクトルに変換して、最後に特徴ベクトルとラベルを使用して予測モデルを作成します。

 アプリケーションの作成を始めるには、最初に、必要なモジュールをインポートし、アプリケーションにリソースを割り当てます。 新しい Notebook の空のセルに次のスニペットを貼り付けてから、**Shift + Enter** キーを押します。

     from pyspark.ml import Pipeline
     from pyspark.ml.classification import LogisticRegression
     from pyspark.ml.feature import HashingTF, Tokenizer
     from pyspark.sql import Row, SQLContext
    
     import os
     import sys
     from pyspark import SparkConf
     from pyspark import SparkContext
     from pyspark.sql import SQLContext
     from pyspark.sql.types import *
    
     from pyspark.mllib.classification import LogisticRegressionWithSGD
     from pyspark.mllib.regression import LabeledPoint
     from numpy import array
    
     # Assign resources to the application
     conf = SparkConf()
     conf.setMaster('spark://headnodehost:7077')
     conf.setAppName('pysparkregression')
     conf.set("spark.cores.max", "4")
     conf.set("spark.executor.memory", "4g")
    
     sc = SparkContext(conf=conf)
     sqlContext = SQLContext(sc)

 Jupyter でジョブを実行するたびに、Web ブラウザー ウィンドウのタイトルに **[(ビジー)]** ステータスと Notebook のタイトルが表示されます。 また、右上隅にある **Python 2** というテキストの横に塗りつぶされた円も表示されます。 ジョブが完了すると、白抜きの円に変化します。

  ![Jupyter Notebook ジョブのステータス](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.Jupyter.Job.Status.png "Status of a Jupyter notebook job")

4. ここで、データ (hvac.csv) を読み込み、解析し、それを使用してモデルをトレーニングする必要があります。 そのためには、ビルの実際の温度が目標温度より高いかどうかを確認する関数を定義します。 実際の温度の方が高い場合、ビルは暑く、値 **1.0** で示されます。 実際の温度の方が低い場合、ビルは寒く、値 **0.0** で示されます。

 次のスニペットを空のセルに貼り付けて、**Shift + Enter** キーを押します。

     # List the structure of data for better understanding. Becuase the data will be
     # loaded as an array, this structure makes it easy to understand what each element
     # in the array corresponds to
    
     # 0 Date
     # 1 Time
     # 2 TargetTemp
     # 3 ActualTemp
     # 4 System
     # 5 SystemAge
     # 6 BuildingID
    
     LabeledDocument = Row("BuildingID", "SystemInfo", "label")
    
     # Define a function that parses the raw CSV file and returns an object of type LabeledDocument
    
     def parseDocument(line):
         values = [str(x) for x in line.split(',')]
         if (values[3] > values[2]):
             hot = 1.0
         else:
             hot = 0.0        
    
         textValue = str(values[4]) + " " + str(values[5])
    
         return LabeledDocument((values[6]), textValue, hot)
    
     # Load the raw HVAC.csv file, parse it using the function
     data = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
     documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
     training = documents.toDF()

5. トークナイザー、hashingTF、lr という 3 つのステージで構成される Spark 機械学習パイプラインを構成します。 パイプラインの概要と「」を参照動作の詳細については <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Spark 機械学習パイプライン</a>します。

    次のスニペットを空のセルに貼り付けて、**Shift + Enter** キーを押します。

        tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
        hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
        lr = LogisticRegression(maxIter=10, regParam=0.01)
        pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

6. パイプラインをトレーニング ドキュメントに適合させます。 次のスニペットを空のセルに貼り付けて、**Shift + Enter** キーを押します。

        model = pipeline.fit(training)

7. トレーニング ドキュメントを検証してアプリケーションでの進行状況をチェックポイントします。 次のスニペットを空のセルに貼り付けて、**Shift + Enter** キーを押します。

        training.show()

    出力は次のようになります。

        BuildingID SystemInfo label
        4          13 20      0.0  
        17         3 20       0.0  
        18         17 20      1.0  
        15         2 23       0.0  
        3          16 9       1.0  
        4          13 28      0.0  
        2          12 24      0.0  
        16         20 26      1.0  
        9          16 9       1.0  
        12         6 5        0.0  
        15         10 17      1.0  
        7          2 11       0.0  
        15         14 2       1.0  
        6          3 2        0.0  
        20         19 22      0.0  
        8          19 11      0.0  
        6          15 7       0.0  
        13         12 5       0.0  
        4          8 22       0.0  
        7          17 5       0.0

    戻り、生の CSV ファイルに対して出力を確認します。 たとえば、CSV ファイルの最初の行のデータは次のとおりです。

    ![HVAC データのスナップショット](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.ML.Show.Data.First.Row.png "Snapshot of the HVAC data")

    実際の温度は目標温度より低く、ビルが寒いことを示します。 そのため、トレーニングの出力では、最初の行の **label** の値は **0.0** であり、ビルが暑くないことを意味します。

8.  トレーニング済みのモデルを実行するようにデータ セットを準備します。 そのためには、システム ID とシステム経過年数 (トレーニング出力では **SystemInfo** として示されます) を渡し、モデルはそのシステム ID とシステム経過年数のビルが暑すぎるか (1.0 で示されます) または寒すぎるか (0.0 で示されます) を予測します。

    次のスニペットを空のセルに貼り付けて、**Shift + Enter** キーを押します。

        # SystemInfo here is a combination of system ID followed by system age
        Document = Row("id", "SystemInfo")
        test = sc.parallelize([(1L, "20 25"),
                      (2L, "4 15"),
                      (3L, "16 9"),
                      (4L, "9 22"),
                      (5L, "17 10"),
                      (6L, "7 22")]) \
            .map(lambda x: Document(*x)).toDF() 

9. 最後に、テスト データで予測を行います。 次のスニペットを空のセルに貼り付けて、**Shift + Enter** キーを押します。

        # Make predictions on test documents and print columns of interest
        prediction = model.transform(test)
        selected = prediction.select("SystemInfo", "prediction", "probability")
        for row in selected.collect():
            print row

10. 次のような出力が表示されます。

        Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
        Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
        Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
        Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
        Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
        Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))

    予測の 1 行目からは、ID が 20 でシステム経過年数が 25 年の HVAC システムではビルが暑い (**prediction=1.0**) ことがわかります。 DenseVector (0.49999) の最初の値は予測 0.0 に対応し、2 番目の値 (0.5001) は予測 1.0 に対応します。 出力では、2 番目の値はわずかに高いだけですが、モデルは **prediction=1.0** を示します。

11. カーネルを再起動して Notebook を終了します。 上部のメニュー バーから、**[カーネル]** をクリックし、**[再起動]** をクリックして、プロンプトで再び **[再起動]** をクリックします。

    ![Jupyter カーネルを再起動します](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.Jupyter.Restart.Kernel.png "Restart the Jupyter Kernel")


## <a name="anaconda"></a>Anaconda scikit を使用して、機械学習ライブラリを習得

HDInsight の Apache Spark クラスターには、Anaconda ライブラリが含まれます。 これには、機械学習用の **scikit-learn** ライブラリも含まれます。 ライブラリには、Jupyter Notebook からサンプル アプリケーションを直接作成するために使用できるさまざまなデータ セットも含まれます。 例については、scikit を使用する-ライブラリを参照してください [http://scikit-learn.org/stable/auto_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html)します。

## <a name="seealso"></a>関連項目

* [Azure HDInsight での Apache Spark の概要:](hdinsight-apache-spark-overview.md)
* [HDInsight クラスターでの Spark をプロビジョニングします。](hdinsight-apache-spark-provision-clusters.md)
* [BI ツールを使用して HDInsight で Spark を使用して対話型データ分析を実行します。](hdinsight-apache-spark-use-bi-tools.md)
* [リアルタイム ストリーミング アプリケーションを構築するための HDInsight の Spark を使用します。](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Azure HDInsight で Apache Spark クラスターのリソースを管理します。](hdinsight-apache-spark-resource-manager.md)



[hdinsight-versions]: ../hdinsight-component-versioning/ 
[hdinsight-upload-data]: ../hdinsight-upload-data/ 
[hdinsight-storage]: ../hdinsight-use-blob-storage/ 
[hdinsight-weblogs-sample]: ../hdinsight-hive-analyze-website-log/ 
[hdinsight-sensor-data-sample]: ../hdinsight-hive-analyze-sensor-data/ 
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/ 
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/ 
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/ 
[azure-management-portal]: https://manage.windowsazure.com/ 
[azure-create-storageaccount]: ../storage-create-storage-account/ 

