<properties
   pageTitle="HDInsight での Hadoop Hive と Remote Desktop の使用 | Microsoft Azure"
   description="リモート デスクトップを使用して HDInsight で Hadoop クラスターに接続し、Hive コマンド ライン インターフェイスを使用して Hive クエリを実行する方法について説明します。"
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


# リモート デスクトップによる HDInsight での Hive と Hadoop の使用

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

この記事では、リモート デスクトップを使用して HDInsight クラスターに接続し、Hive コマンド ライン インターフェイス (CLI) を使用して Hive クエリを実行する方法について説明します。
> [AZURE.NOTE] このドキュメントには、例で使用される HiveQL ステートメントで何が実行されるかに関する詳細は含まれていません。 この例で使用される HiveQL については、次を参照してください。 [HDInsight での Hadoop の Hive の使用](hdinsight-use-hive.md)します。

## <a id="prereq"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* Windows ベースの HDInsight (HDInsight で Hadoop を使用) クラスター

* Windows 10、Window 8、Windows 7 を実行するクライアント コンピューター

## <a id="connect"></a>リモート デスクトップへの接続します。

HDInsight クラスターのリモート デスクトップを有効にし、」の手順に従ってした接続 [RDP を使用した HDInsight クラスターへの接続](hdinsight-administer-use-management-portal.md#rdp)します。

## <a id="hive"></a>Hive コマンドを使用します。

HDInsight クラスターのデスクトップに接続したら、次の手順に従って Hive を使用します。

1. HDInsight デスクトップから、**Hadoop コマンド ライン**を起動します。

2. 次のコマンドを入力して、Hive CLI を起動します。

        %hive_home%\bin\hive

    CLI が起動する場合は、Hive CLI プロンプトが表示されます: `hive >`します。

3. CLI を使用して次のステートメントを入力し、サンプル データを使用して **log4jLogs** という名前の新しいテーブルを作成します。

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

    * **SELECT**: **t4** 列の値が **[ERROR]** であるすべての行の数を指定します。 ここでは、この値を含む行が 3 行あるため、**3** という値が返されています。

    * **INPUT__FILE__NAME LIKE '%.log'** - Hive に .log で終わるファイルのデータのみを返す必要があることを示します。 これにより、検索はデータを含む sample.log ファイルに制限され、定義したスキーマに一致しない他のサンプル データ ファイルのデータを返すことができなくなります。

4. 次のステートメントを使用して、**errorLogs** という名前の新しい "内部" テーブルを作成します。

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    これらのステートメントは次のアクションを実行します。

    * **CREATE TABLE IF NOT EXISTS**: 既存のテーブルがない場合、テーブルを作成します。 **EXTERNAL** キーワードが使用されていないため、これは内部テーブルであり、Hive のデータ保管先に格納され、完全に Hive によって管理されます。
        > [AZURE.NOTE] **EXTERNAL** テーブルとは異なり、内部デーブルを削除すると、基盤となるデータは削除されます。

    * **STORED AS ORC**: Optimized Row Columnar (ORC) 形式でデータを格納します。 この形式は、Hive にデータを格納するための、非常に効率的で適切な形式です。

    * **INSERT OVERWRITE ...選択**: 行を選択、 **log4jLogs** がテーブル **[ERROR]**, へのデータを挿入、 **errorLogs** テーブルです。

    **errorLogs** テーブルに格納された、t4 列に **[ERROR]** を含む列のみを確認するには、次のステートメントを使用して、**errorLogs** 列からすべての列を返します。

        選択 * errorLogs; から

    3 つのデータ行が返され、各行の t4 列には **[ERROR]** が含まれます。

## <a id="summary"></a>概要

このように、Hive コマンドを使用すると、HDInsight クラスターで簡単に対話的に Hive クエリを実行し、ジョブ ステータスを監視し、出力を取得できます。

## <a id="nextsteps"></a>次のステップ

HDInsight での Hive に関する全般的な情報

* [HDInsight での Hadoop の Hive を使用します。](hdinsight-use-hive.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hadoop での Pig の使用](hdinsight-use-pig.md)

* [HDInsight での Hadoop の MapReduce を使用します。](hdinsight-use-mapreduce.md)



[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md 
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
[hdinsight-provision]: hdinsight-provision-clusters.md 
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md 
[hdinsight-upload-data]: hdinsight-upload-data.md 
[powershell-install-configure]: ../powershell-install-configure.md 
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx 

