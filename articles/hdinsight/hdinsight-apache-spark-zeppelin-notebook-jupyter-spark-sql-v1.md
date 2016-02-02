<properties 
    pageTitle="HDInsight への Spark クラスターのプロビジョニングと、Zeppelin および Jupyter から Spark SQL を使用した対話型の分析 |Azure" 
    description="HDInsight に Apache Spark クラスターをすばやくプロビジョニングし、Zeppelin および Jupyter Notebook から Spark SQL を使用して対話型クエリを実行する手順を説明します。" 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="paulettm" 
    editor="cgronlun"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/01/2015" 
    ms.author="nitinme"/>



# クイック スタート: HDInsight の Apache Spark のプロビジョニングと Spark SQL を使用した対話型クエリの実行

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [HDInsight での Apache Spark のプロビジョニングし、Spark SQL を使用して対話型クエリの実行](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)

簡易作成] オプションを使用した HDInsight での Apache Spark クラスターをプロビジョニングする方法について説明し、web ベースを使用して [Zeppelin](https://zeppelin.incubator.apache.org) と [Jupyter](https://jupyter.org) ノート パソコンで Spark クラスターに対して Spark SQL 対話型クエリを実行します。


   ![HDInsight の Apache Spark の使用を開始](. media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI/です。GetStartedFlow.Spark.png"開始 HDInsight チュートリアルで Apache Spark を使用します。 手順: ストレージ アカウントの作成クラスターをプロビジョニングします。Spark SQL ステートメントを実行して")

**前提条件:**

このチュートリアルを開始する前に、Azure サブスクリプションが必要です。 参照してください [取得 Azure 無料試用版](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)します。

## <a name="storage"></a>Azure ストレージ アカウントを作成します。

HDInsight で HDInsight クラスターをプロビジョニングするときに、Azure ストレージ アカウントを指定します。 このアカウントの特定の BLOB ストレージ コンテナーが、既定のファイル システムとして設定されます。 既定では、HDInsight クラスターは、指定されたストレージ アカウントと同じデータ センターにプロビジョニングされます。 詳細については、次を参照してください。 [HDInsight ][hdinsight-storage]します。


**Azure ストレージ アカウントを作成するには**

1. サインイン、 [Azure Classic Portal ][azure-management-portal]します。
2. 左下隅にある **[新規]** をクリックし、次の図に示すように、値を入力します。

    ![簡易作成を使用して新しいストレージ アカウントを設定できる Azure クラシック ポータル。](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.StorageAccount.QuickCreate.png "Azure Classic Portal where you can use Quick Create to set up a new storage account")

>[AZURE.NOTE]  ストレージ アカウントは、クラスターでサポートされる場所

一覧の新しいストレージ アカウントを選択し、ページの下部の **[アクセス キーの管理]** をクリックします。 **[プライマリ アクセス キー]** (または **[セカンダリ アクセス キー]** 動作しているいずれかのキー) の値を書き留めます。 この情報は後で必要になります。 詳細については、次を参照してください。 [をストレージ アカウント ][azure-create-storageaccount] します。

## <a name="provision"></a>HDInsight の Spark クラスターをプロビジョニングします。

このセクションでは、Spark バージョン 1.3.1 に基づいて HDInsight バージョン 3.2 クラスターをプロビジョニングします。 HDInsight バージョンとその Sla については、次を参照してください。 [HDInsight コンポーネントのバージョン](hdinsight-component-versioning.md)します。
>[AZURE.NOTE] この記事の手順では、基本の構成設定を使用して HDInsight に Apache Spark クラスターを作成します。 (追加の記憶域、Azure の仮想ネットワークまたはメタストアを使用して、Hive 用) などの他のクラスター構成設定の詳細については、次を参照してください。 [HDInsight クラスターのプロビジョニングのカスタム オプションを使用して](hdinsight-apache-spark-provision-clusters.md)します。


**Spark クラスターをプロビジョニングするには**

1. サインイン、 [Azure Classic Portal ][azure-management-portal]します。

2. 左下隅にある **[新規]** をクリックし、次の図に示すように、値を入力します。

    ![HDInsight に Spark クラスターを作成します](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.QuickCreateCluster.png "Create a Spark cluster in HDInsight")


## <a name="zeppelin"></a>Zeppelin notebook を使用して対話型 Spark SQL クエリを実行します。

クラスターをプロビジョニングした後、Web ベースの Zeppelin Notebook を使用して、Spark HDInsight クラスターに対して Spark SQL の対話型クエリを実行できます。 このセクションでは、クラスターにおいて既定で使用できるサンプル データ ファイル (hvac.csv) を使用していくつかの対話型 Spark SQL クエリを実行します。
>[AZURE.NOTE] 以下の説明に従って作成した Notebook も、クラスターにおいて既定で利用できます。 Zeppelin を起動した後、**Zeppelin HVAC tutorial** という名前でこの Notebook を検索します。

1. [Azure ポータル](https://portal.azure.com/), 、スタート ボードで、タイルをクリックして、Spark クラスターの場合、スタート画面に固定表示)。 **[すべて参照]** > **[HDInsight クラスター]** でクラスターに移動することもできます。

2. Spark クラスター ブレードで、**[クイック リンク]** をクリックし、**[クラスター ダッシュボード]** ブレードで **[Zeppelin Notebook]** をクリックします。 入力を求められたら、クラスターの管理者資格情報を入力します。
    > [AZURE.NOTE] ブラウザーで次の URL を開き、クラスターの Zeppelin Notebook にアクセスすることもできます。 __CLUSTERNAME__ をクラスターの名前に置き換えます。
    >
    > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. 新しい Notebook を作成します。 ヘッダー ウィンドウで **[Notebook]** をクリックし、**[Note の新規作成]** をクリックします。

    ![新しい Zeppelin Notebook を作成します](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.CreateNewNote.png "Create a new Zeppelin notebook")

    同じページの **[Notebook]** 見出しの下に、**Note XXXXXXXXX** で名前が始まる新しい Notebook が表示されます。 新しい Notebook をクリックします。

3. 新しい Notebook の Web ページで、見出しをクリックし、必要に応じて Notebook の名前を変更します。 Enter キーを押して名前の変更を保存します。 また、Notebook のヘッダーの右上隅に **[接続]** というステータスが表示されることを確認します。

    ![Zeppelin Notebook のステータス](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.NewNote.Connected.png "Zeppelin notebook status")

4. サンプル データを一時テーブルに読み込みます。 HDInsight の Spark クラスターをプロビジョニングすると、サンプル データ ファイル **hvac.csv** が関連するストレージ アカウントの **\HdiSamples\SensorSampleData\hvac** にコピーされます。

 新しい Notebook に既定で作成される空の段落に、次のスニペットを貼り付けます。

     // Create an RDD using the default Spark context, sc
     val hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
     // Define a schema
     case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
    
     // Map the values in the .csv file to the schema
     val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
         s => Hvac(s(0), 
                 s(1),
                 s(2).toInt,
                 s(3).toInt,
                 s(6)
         )
     ).toDF()
    
     // Register as a temporary table called "hvac"
     hvac.registerTempTable("hvac")

 **Shift + Enter** キーを押すか、段落の **[プレイ]** ボタンをクリックして、スニペットを実行します。 段落の右上隅にあるステータスが、[準備完了]、[保留中]、[実行中]、[完了] の順に進行します。 出力が同じ段落の下に表示されます。 スクリーンショットは次のようになります。

 ![生データから一時テーブルを作成します](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Note.LoadDataIntoTable.png "Create a temporary table from raw data")

 各段落にタイトルを指定することもできます。 右上隅の **[設定]** アイコンをクリックし、**[タイトルの表示]** をクリックします。

5. **hvac** テーブルに対して Spark SQL ステートメントを実行できます。 次のクエリを新しい段落に貼り付けます。 このクエリは、ビル ID と、特定の日の各ビルの目標温度と実温度の差を取得します。 **Shift + Enter** キーを押します。

        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date 
        from hvac
        where date = "6/1/13" 

    **%Sql** 先頭にあるステートメントには、Spark SQL インタープリターを使用する notebook がように指示します。 定義済みのインタープリターは、Notebook ヘッダーの **[インタープリター]** タブで見ることができます。

    次のスクリーンショットでは出力を示します。

    ![Notebook を使用して Spark SQL ステートメントを実行します](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Note.SparkSQLQuery1.png "Run a Spark SQL statement using the notebook")

     表示オプション (四角で囲ってある部分) をクリックして、同じ出力の異なる表現に切り替えることができます。 **[設定]** をクリックして、出力のキーと値の構成を選択します。 上記の画面キャプチャでは、**buildingID** をキーとして使用し、**temp_diff** の平均を値として使用しています。

6. クエリの変数を使用して Spark SQL ステートメントを実行することもできます。 次のスニペットでは、クエリで変数 **Temp** と照会できる値を定義する方法を示します。 初めてクエリを実行すると、変数に指定した値がドロップダウンに自動的に設定されます。

        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
        from hvac
        where targettemp > "${Temp = 65,65|75|85}" 

    このスニペットを新しい段落に貼り付けて、**Shift + Enter** キーを押します。 次のスクリーンショットでは出力を示します。

    ![Notebook を使用して Spark SQL ステートメントを実行します](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Note.SparkSQLQuery2.png "Run a Spark SQL statement using the notebook")

    後続のクエリでは、ドロップダウンから新しい値を選択し、クエリを再実行できます。 **[設定]** をクリックして、出力のキーと値の構成を選択します。 上の画面キャプチャでは、**buildingID** をキーとして、**temp_diff** の平均を値として、**targettemp** をグループとして使用しています。

7. Spark SQL インタープリターを再起動して、アプリケーションを終了します。 上部の **[インタープリター]** タブをクリックし、Spark インタープリターの **[再起動]** をクリックします。

    ![Zeppelin インタープリターを再起動します](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Zeppelin.Restart.Interpreter.png "Restart the Zeppelin intepreter")

## <a name="jupyter"></a>Jupyter notebook を使用して Spark SQL クエリを実行します。

このセクションでは、Jupyter Notebook を使用して、Spark クラスターに対して Spark SQL クエリを実行します。
>[AZURE.NOTE] 以下の説明に従って作成した Notebook も、クラスターにおいて既定で利用できます。 Jupyter を起動した後、**HVACTutorial.ipynb** という名前でこの Notebook を検索します。

1. [Azure ポータル](https://portal.azure.com/), 、スタート ボードで、タイルをクリックして、Spark クラスターの場合、スタート画面に固定表示)。 **[すべて参照]** > **[HDInsight クラスター]** でクラスターに移動することもできます。

2. Spark クラスター ブレードで、**[クイック リンク]** をクリックし、**[クラスター ダッシュボード]** ブレードで **[Jupyter Notebook]** をクリックします。 入力を求められたら、クラスターの管理者資格情報を入力します。
    > [AZURE.NOTE] ブラウザーで次の URL を開き、クラスターの Jupyter Notebook にアクセスすることもできます。 __CLUSTERNAME__ をクラスターの名前に置き換えます。
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 新しい Notebook を作成します。 **[新規]** をクリックし、**[Python2]** をクリックします。

    ![新しい Jupyter Notebook を作成します](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Note.Jupyter.CreateNotebook.png "Create a new Jupyter notebook")

3. Untitled.pynb という名前の新しい Notebook が作成されて開かれます。 上部の Notebook 名をクリックし、わかりやすい名前を入力します。

    ![Notebook の名前を指定します](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Note.Jupyter.Notebook.Name.png "Provide a name for the notebook")

4. 必要なモジュールをインポートし、Spark コンテキストと SQL コンテキストを作成します。 次のスニペットを空のセルに貼り付けて、**Shift + Enter** キーを押します。

     from pyspark import SparkContext
     from pyspark.sql import SQLContext
     from pyspark.sql.types import *
    
     # Create Spark and SQL contexts
     sc = SparkContext('spark://headnodehost:7077', 'pyspark')
     sqlContext = SQLContext(sc)

 Jupyter でジョブを実行するたびに、Web ブラウザー ウィンドウのタイトルに **[(ビジー)]** ステータスと Notebook のタイトルが表示されます。 また、右上隅にある **Python 2** というテキストの横に塗りつぶされた円も表示されます。 ジョブが完了すると、白抜きの円に変化します。

  ![Jupyter Notebook ジョブのステータス](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Jupyter.Job.Status.png "Status of a Jupyter notebook job")

4. サンプル データを一時テーブルに読み込みます。 HDInsight の Spark クラスターをプロビジョニングすると、サンプル データ ファイル **hvac.csv** が関連するストレージ アカウントの **\HdiSamples\SensorSampleData\hvac** にコピーされます。

 次のスニペットを空のセルに貼り付けて、**Shift + Enter** キーを押します。 このスニペットは、**hvac** という一時テーブルにデータを登録します。

     # Load the data
     hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
     # Create the schema
     hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
    
     # Parse the data in hvacText
     hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
    
     # Create a data frame
     hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
    
     # Register the data fram as a table to run queries against
     hvacdf.registerAsTable("hvac")
    
     # Run queries against the table and display the data
     data = sqlContext.sql("select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = \"6/1/13\"")
     data.show()

5. ジョブが正常に完了すると、次の出力が表示されます。

        buildingID temp_diff date  
        4          8         6/1/13
        3          2         6/1/13
        7          -10       6/1/13
        12         3         6/1/13
        7          9         6/1/13
        7          5         6/1/13
        3          11        6/1/13
        8          -7        6/1/13
        17         14        6/1/13
        16         -3        6/1/13
        8          -8        6/1/13
        1          -1        6/1/13
        12         11        6/1/13
        3          14        6/1/13
        6          -4        6/1/13
        1          4         6/1/13
        19         4         6/1/13
        19         12        6/1/13
        9          -9        6/1/13
        15         -10       6/1/13

6. カーネルを再起動してアプリケーションを終了します。 上部のメニュー バーから、**[カーネル]** をクリックし、**[再起動]** をクリックして、プロンプトで再び **[再起動]** をクリックします。

    ![Jupyter カーネルを再起動します](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Jupyter.Restart.Kernel.png "Restart the Jupyter Kernel")


## <a name="seealso"></a>関連項目

* [Azure HDInsight での Apache Spark の概要:](hdinsight-apache-spark-overview.md)
* [HDInsight クラスターでの Spark をプロビジョニングします。](hdinsight-apache-spark-provision-clusters.md)
* [BI ツールを使用して HDInsight で Spark を使用して対話型データ分析を実行します。](hdinsight-apache-spark-use-bi-tools.md)
* [Machine learning アプリケーションを構築するための HDInsight の Spark を使用します。](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [リアルタイム ストリーミング アプリケーションを構築するための HDInsight の Spark を使用します。](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Azure HDInsight で Apache Spark クラスターのリソースを管理します。](hdinsight-apache-spark-resource-manager.md)



[hdinsight-versions]: ../hdinsight-component-versioning/ 
[hdinsight-upload-data]: ../hdinsight-upload-data/ 
[hdinsight-storage]: ../hdinsight-use-blob-storage/ 
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/ 
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/ 
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/ 
[azure-management-portal]: https://manage.windowsazure.com/ 
[azure-create-storageaccount]: ../storage-create-storage-account/ 

