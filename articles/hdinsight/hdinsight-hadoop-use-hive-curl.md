<properties
   pageTitle="HDInsight での Hadoop Hive と Curl の使用 | Microsoft Azure"
   description="Curl を使用して Pig ジョブを HDInsight にリモートで送信する方法について説明します。"
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


# Curl を使用した HDInsight の Hadoop での Hive クエリの実行

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

このドキュメントでは、Curl を使用して Azure HDInsight クラスターの Hadoop で Hive クエリを実行する方法について説明します。

Curl は、未加工の HTTP 要求を使用して HDInsight を操作し、Hive クエリを実行、監視し、その結果を取得する方法を指定するために使用します。 これは、HDInsight クラスターで提供される WebHCat REST API (旧称: Templeton) を使用することで機能します。
> [AZURE.NOTE] Linux ベースの Hadoop サーバーの使用に慣れているが HDInsight は、という場合は、次を参照してください。 [Linux ベースの HDInsight での Hadoop について知っておく必要がある](hdinsight-hadoop-linux-information.md)します。

## <a id="prereq"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* HDInsight クラスター (Linux または Windows ベース) の Hadoop

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Curl を使用した Hive クエリを実行します。

> [AZURE.NOTE] Curl、または WebHCat を使用したその他の REST 通信を使用する場合は、HDInsight クラスター管理者のユーザー名とパスワードを指定して要求を認証する必要があります。 また、サーバーへの要求の送信に使用する Uniform Resource Identifier (URI) にクラスター名を含める必要があります。
>
> このセクションのコマンドでは、**USERNAME** をクラスターを認証するユーザーの名前に、**PASSWORD** をユーザー アカウントのパスワードに置き換えてください。 **CLUSTERNAME** をクラスターの名前に置き換えます。
>
> 使用して REST API を保護 [基本認証](http://en.wikipedia.org/wiki/Basic_access_authentication)します。 資格情報をサーバーに安全に送信するには、必ずセキュア HTTP (HTTPS) を使用して要求を行う必要があります。

1. コマンド ラインで次のコマンドを使用して、HDInsight クラスターに接続できることを確認します。

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    次のような応答を受け取ります。

        {"status":"ok","version":"v1"}

    このコマンドで使用されるパラメーターの意味は次のとおりです。

    * **-u**: 要求の認証に使用するユーザー名とパスワード
    * **-G**: GET 要求であることを示します。

    URL の先頭 **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, 、すべての要求に対して同じになります。 パス  **/status** は、要求がサーバー用の WebHCat (別名: Templeton) の状態を返すことを示します。 次のコマンドを使用して、Hive のバージョンを要求することもできます。

        curl-u ユーザー名: パスワードの-g https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive

    次のような応答が返されます。

        {「モジュール」:「ハイブ」、「バージョン」:「0.13.0.2.1.6.0-2103」}

2. 次のコマンドを使用して、**log4jLogs** という名前の新しいテーブルを作成します。

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'wasb:///example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    このコマンドで使用されるパラメーターの意味は次のとおりです。

    * **-d** 以降 `-g` は使用されず、要求は既定で POST メソッドです。 `-d` 要求で送信されるデータ値を指定します。

        * **user.name**: コマンドを実行するユーザー

        * **execute**: 実行する HiveQL ステートメント

        * **statusdir**: ジョブのステータスが書き込まれるディレクトリ

    これらのステートメントは次のアクションを実行します。

    * **DROP TABLE**: テーブルが既存の場合にテーブルとデータ ファイルを削除します。

    * **CREATE EXTERNAL TABLE**: Hive に新しく '外部' テーブルを作成します。 外部テーブルは Hive にテーブル定義のみを格納します。 データは元の場所に残されます。
        > [AZURE.NOTE] 基盤となるデータを外部ソースによって更新する (データの自動アップロード処理など) 場合や別の MapReduce 操作によって更新する場合に、Hive クエリで最新のデータを使用する場合は、外部テーブルを使用する必要があります。
        >
        > 外部テーブルを削除しても、データは削除**されません**。テーブル定義のみが削除されます。

    * **ROW FORMAT** - Hive にデータの形式を示します。 ここでは、各ログのフィールドは、スペースで区切られています。

    * **STORED AS TEXTFILE LOCATION** - Hive に、データの格納先 (example/data ディレクトリ) と、データはテキストとして格納されていることを示します。

    * **SELECT** - **t4** 列の値が **[ERROR]** であるすべての行の数を指定します。 ここでは、この値を含む行が 3 行あるため、**3** という値が返されています。
    > [AZURE.NOTE] HiveQL ステートメントのスペースで置き換えられる通知、 `+` Curl を使用したとき。 区切り記号などのスペースを含む引用符で囲まれた値を置き換えることがない必要があります `+`します。

    * **INPUT__FILE__NAME LIKE '%25.log'** - 検索で .log で終わるファイルのみが使用されます。 このファイルがない場合は、Hive により、このディレクトリとそのサブディレクトリ内のすべてのファイル (このテーブルに定義された列スキーマに一致しないファイルを含む) の検索が試行されます。
    > [AZURE.NOTE] 実際の条件は、その %25 は URL エンコード形式 % の `'%.log' をください`します。 URL で特殊文字と見なされるため、% は URL エンコードである必要があります。

    このコマンドは、ジョブのステータスの確認に使用できる ジョブ ID を返します。

        {"id":"job_1415651640909_0026"}

3. ジョブのステータスを確認するには、次のコマンドを使用します。 **JOBID** を前の手順で返された値に置き換えます。 たとえば、戻り値が `{"id":"job_1415651640909_0026"}`, 、し **JOBID** は `job_1415651640909_0026`します。

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    ジョブが完了している場合、ステータスは **SUCCEEDED** になります。
    > [AZURE.NOTE] この Curl 要求では、ジョブに関する情報が記載された JavaScript Object Notation (JSON) ドキュメントが返されます。状態値のみを取得するには jq を使用します。

4. ジョブのステータスが **SUCCEEDED** に変わったら、Azure BLOB ストレージからジョブの結果を取得できます。  `Statusdir` クエリで渡されるパラメーターには、ここでは、出力ファイルの場所が含まれています。 **wasb:////例/curl**します。 このアドレスではジョブの出力は、HDInsight クラスターが使用する既定のストレージ コンテナーの **example/curl** ディレクトリに保存されます。

    一覧表示しを使用してこれらのファイルをダウンロードすることができます、 [Mac、Linux および Windows 用 Azure CLI](../xplat-cli-install.md)します。 たとえば、**example/curl** 内のファイルを一覧表示するには、次のコマンドを使用します。

        azure storage blob list <container-name> example/curl

    ファイルをダウンロードするには、次のコマンドを使用します。

        azure storage blob download <container-name> <blob-name> <destination-file>

    > [AZURE.NOTE] 使用して blob を格納するストレージ アカウント名を指定する必要がありますか、 `-a` と `-k` パラメーター、またはセット、 **\_storage\_account** と **azure \_storage\_access\_key** 環境変数です。詳細情報については、「<a href="hdinsight-upload-data.md" target="_blank"」 をご覧ください。

6. 次のステートメントを使用して、**errorLogs** という名前の新しい "内部" テーブルを作成します。

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log';SELECT+*+from+errorLogs;" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    これらのステートメントは次のアクションを実行します。

    * **CREATE TABLE IF NOT EXISTS** - 既存のテーブルがない場合、テーブルを作成します。 **EXTERNAL** キーワードが使用されていないため、これは内部テーブルであり、Hive のデータ保管先に格納され、完全に Hive によって管理されます。
        > [AZURE.NOTE] 外部テーブルとは異なり、内部テーブルを削除すると、基盤となるデータも削除されます。

    * **STORED AS ORC** - Optimized Row Columnar (ORC) 形式でデータを格納します。 この形式は、Hive にデータを格納するための、非常に効率的で適切な形式です。
    * **INSERT OVERWRITE ...選択** の行を選択、 **log4jLogs** がテーブル **[ERROR]**, へのデータを挿入、 **errorLogs** テーブルです。
    * **SELECT**: 新しい **errorLogs** テーブルからすべての行を選択します。

7. 返されたジョブ ID を使用して、ジョブのステータスを確認します。 確認できたら、前述のように Mac、Linux、Windows 用 Azure CLI を使用して、結果をダウンロードし、表示します。 出力には、それぞれに **[ERROR]** が含まれた 3 つの行が返されます。


## <a id="summary"></a>概要

このドキュメントを参照して、未加工の HTTP 要求を使用して、HDInsight クラスターで Hive ジョブを実行、監視し、その結果を表示できます。

この記事で使用される REST インターフェイスの詳細については、次を参照してください。、 <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference" target="_blank">WebHCat リファレンス</a>します。

## <a id="nextsteps"></a>次のステップ

HDInsight での Hive に関する全般的な情報

* [HDInsight での Hadoop の Hive を使用します。](hdinsight-use-hive.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hadoop での Pig の使用](hdinsight-use-pig.md)

* [HDInsight での Hadoop の MapReduce を使用します。](hdinsight-use-mapreduce.md)



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
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx 

