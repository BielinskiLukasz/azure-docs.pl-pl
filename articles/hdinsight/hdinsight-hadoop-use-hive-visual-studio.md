<properties
   pageTitle="Hadoop tools for Visual Studio での Hive クエリ | Microsoft Azure"
   description="Visual Studio の Hadoop ツールを使用して HDInsight の Hadoop で Hive を使用する方法について説明します。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/04/2015"
   ms.author="larryfr"/>


# Visual Studio の HDInsight ツールを使用して Hive クエリを実行

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

この記事では、Visual Studio の HDInsight ツールを使用して、Hive クエリを HDInsight クラスターに送信する方法を説明します。
> [AZURE.NOTE] このドキュメントには、例で使用される HiveQL ステートメントで何が実行されるかに関する詳細は含まれていません。 この例で使用される HiveQL については、次を参照してください。 [HDInsight での Hadoop の Hive の使用](hdinsight-use-hive.md)します。

## <a id="prereq"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* Azure HDInsight (HDInsight での Hadoop) クラスター (Linux または Windows ベース)

* Visual Studio 2012 [アップデート 4](http://www.microsoft.com/download/details.aspx?id=39305), 、Visual Studio 2013 [Update 3](http://go.microsoft.com/fwlink/?LinkId=390465), 、または [Visual Studio Express 2013](http://www.microsoft.com/download/details.aspx?id=40769)

## <a id="run"></a> Visual Studio の HDInsight ツールを使用して Hive クエリを実行します。

1. **Visual Studio** を開き、**[新規]**、**[プロジェクト]**、**[HDInsight]**、**[Hive アプリケーション]** の順に選択します。 プロジェクトの名前を指定します。

2. このプロジェクトで作成した **Script.hql** ファイルを開き、次の HiveQL ステートメントを貼り付けます。

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    これらのステートメントは次のアクションを実行します。

    * **DROP TABLE**: テーブルが既存の場合にテーブルとデータ ファイルを削除します。
    * **CREATE EXTERNAL TABLE**: Hive に新しく '外部' テーブルを作成します。 外部テーブルは、Hive にテーブル定義のみを格納し、データは、元の場所に残します。
        > [AZURE.NOTE] 基盤となるデータを外部ソースによって更新する (データの自動アップロード処理など) 場合や別の MapReduce 操作によって更新する場合に、Hive クエリで最新のデータを使用する場合は、外部テーブルを使用する必要があります。
        >
        > 外部テーブルを削除しても、データは削除**されません**。テーブル定義のみが削除されます。

    * **ROW FORMAT**: Hive にデータの形式を示します。 ここでは、各ログのフィールドは、スペースで区切られています。
    * **STORED AS TEXTFILE LOCATION**: Hive に、データの格納先 (example/data ディレクトリ) と、データがテキストとして格納されていることを示します。
    * **SELECT** - **t4** 列の値が **[ERROR]** であるすべての行の数を指定します。 ここでは、この値を含む行が 3 行あるため、**3** という値が返されています。
    * **INPUT__FILE__NAME LIKE '%.log'** - Hive に .log で終わるファイルのデータのみを返す必要があることを示します。 これにより、検索はデータを含む sample.log ファイルに制限され、定義したスキーマに一致しない他のサンプル データ ファイルのデータを返すことができなくなります。

3. ツール バーで、このクエリに使用する **HDInsight クラスター**を選択し、**[送信]** を選択して、Hive ジョブとしてステートメントを実行します。 **[Hive ジョブの概要]** に実行しているジョブに関する情報が表示されます。 **[更新]** リンクを使用して、**[ジョブのステータス]** が **[完了]** に変更されるまで、ジョブの情報を更新します。

4. **[ジョブ出力]** リンクを使用して、このジョブの出力を表示します。 表示する `[ERROR] 3`, 、これは、SELECT ステートメントによって返される値。

5. プロジェクトを作成せずに Hive クエリを実行することもできます。 **サーバー エクスプローラー**を使用して **[Azure]**、**[HDInsight]** の順に展開し、HDInsight サーバーを右クリックして、**[Hive クエリを記述]** を選択します。

6. 表示された **temp.hql** ドキュメントに次の HiveQL ステートメントを追加します。

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    これらのステートメントは次のアクションを実行します。

    * **CREATE TABLE IF NOT EXISTS**: 既存のテーブルがない場合、テーブルを作成します。 **EXTERNAL** キーワードが使用されていないため、これは内部テーブルであり、Hive のデータ保管先に格納され、完全に Hive によって管理されます。
        > [AZURE.NOTE] **EXTERNAL** テーブルとは異なり、内部デーブルを削除すると、基盤となるデータは削除されます。

    * **STORED AS ORC**: Optimized Row Columnar (ORC) 形式でデータを格納します。 この形式は、Hive にデータを格納するための、非常に効率的で適切な形式です。
    * **INSERT OVERWRITE ...選択**: 行を選択、 **log4jLogs** がテーブル **[ERROR]**, へのデータを挿入、 **errorLogs** テーブルです。

7. ツール バーで **[送信]** を選択し、ジョブを実行します。 **[ジョブ ステータス]** で、ジョブが正常に完了したことを確認します。

8. ジョブが完了し、新しいテーブルが作成されたことを確認するには、**サーバー エクスプローラー**で **[Azure]**、**[HDInsight]**、HDInsight クラスター、**[Hive データベース]**、**[既定]** の順に展開します。 **errorLogs** および **log4jLogs** テーブルが表示されます。

## <a id="summary"></a>概要

このように、Visual Studio の HDInsight ツールを使用すると、HDInsight クラスターで簡単に Hive クエリを実行し、ジョブ ステータスを監視し、出力を取得できます。

## <a id="nextsteps"></a>次のステップ

HDInsight での Hive に関する全般的な情報

* [HDInsight での Hadoop の Hive を使用します。](hdinsight-use-hive.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hadoop での Pig の使用](hdinsight-use-pig.md)

* [HDInsight での Hadoop の MapReduce を使用します。](hdinsight-use-mapreduce.md)

Visual Studio の HDInsight ツールに関する詳細情報:

* [Visual Studio の HDInsight ツールの概要](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)



[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx 
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/ 
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/ 
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/ 
[apache-tez]: http://tez.apache.org 
[apache-hive]: http://hive.apache.org/ 
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j 
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez 
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/ 
[hdinsight-use-oozie]: hdinsight-use-oozie.md 
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md 
[hdinsight-storage]: hdinsight-use-blob-storage.md 
[hdinsight-provision]: hdinsight-provision-clusters.md 
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md 
[hdinsight-upload-data]: hdinsight-upload-data.md 
[hdinsight-get-started]: hdinsight-get-started.md 
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx 
[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png 
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png 
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png 

