<properties
   pageTitle="HDInsight での Hadoop Pig と Remote Desktop の使用 | Microsoft Azure"
   description="Pig コマンドを使用して、HDInsight の Windows ベースの Hadoop のクラスターへのリモート デスクトップ接続から Pig Latin ステートメントを実行する方法について説明します。"
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


# リモート デスクトップ接続から Pig ジョブを実行する

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

このドキュメントでは、Pig コマンドを使用して、Windows ベースの HDInsight クラスターへのリモート デスクトップ接続から Pig Latin ステートメントを実行するチュートリアルを提供します。 Pig Latin では、map 関数や reduce 関数ではなく、データ変換を記述することで MapReduce アプリケーションを作成できます。

このドキュメントでは、方法について説明します。

## <a id="prereq"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* Windows ベースの HDInsight (HDInsight で Hadoop を使用) クラスター

* Windows 10、Windows 8、Windows 7 を実行するクライアント コンピューター

## <a id="connect"></a>リモート デスクトップへの接続します。

HDInsight クラスターのリモート デスクトップを有効にし、」の手順に従ってした接続 [RDP を使用した HDInsight クラスターへの接続](hdinsight-administer-use-management-portal.md#rdp)します。

## <a id="pig"></a>Pig コマンドを使用します。

2. リモート デスクトップに接続したら、デスクトップ上のアイコンを使用して **Hadoop コマンド ライン**を開始します。

2. Pig コマンドを開始するには次のコマンドを使用します。

        %pig_home%\bin\pig

    表示されます、 `grunt >` プロンプトです。

3. 次のステートメントを入力します。

        LOGS = LOAD 'wasb:///example/data/sample.log';

    このコマンドは、sample.log ファイルの内容をログ ファイルに読み込みます。 ファイルの内容を表示するには、次のコマンドを使用します。

        DUMP LOGS;

4. 正規表現を適用してデータを変換し、各レコードのログ レベルのみを抽出します。

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    変換後のデータを表示するには、**DUMP** を使用します。 この場合、 `ダンプのレベル`します。

5. 次のステートメントを使用して、変換を適用します。 使用 `ダンプ` 各手順の後に、変換の結果を表示します。

   <table>
    <tr>
    <th>ステートメント</th><th>実行内容</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;</td><td>ログ レベルに null 値を含む行を削除し、結果を FILTEREDLEVELS に格納します。</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;</td><td>ログ レベルで列をグループ化し、結果を GROUPEDLEVELS に格納します。</td>
    </tr>
    <tr>
    <td>FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;</td><td>一意のログ レベル値とそれが出現した回数を含む新しい データ セットを作成します。これは FREQUENCIES に格納されます。</td>
    </tr>
    <tr>
    <td>RESULT = order FREQUENCIES by COUNT desc;</td><td>数が多い順にログ レベルを並べ替えて、RESULT に格納します。</td>
    </tr>
    </table>

6. 使用して、変換の結果を保存することも、 `ストア` ステートメントです。 たとえば、次のコマンドを保存、 `結果` に、 **/example/data/pigout** ディレクトリに、クラスターの既定のストレージ コンテナー。

        STORE RESULT into 'wasb:///example/data/pigout'

    > [AZURE.NOTE] データは、**part-nnnnn** という名前のファイルの指定したディレクトリに保存されます。 ディレクトリが既に存在する場合は、エラー メッセージが表示します。

7. エラーを解決するには、次のステートメントを入力します。

        QUIT;


### Pig Latin バッチ ファイル

Pig コマンドを使用して、ファイルに含まれた Pig Latin を実行することもできます。

3. エラーを解決したら、**メモ帳**を開き、**%PIG_HOME%** ディレクトリに **pigbatch.pig** という名前の新しいファイルを作成します。

4. 次の行を **pigbatch.pig** ファイルに入力するか貼り付けて、保存します。

        LOGS = LOAD 'wasb:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

5. 次の pig コマンドを使用して、**pigbatch.pig** ファイルを実行します。

        pig %PIG_HOME%\pigbatch.pig

    バッチ ジョブが完了したら、使用されている場合と同じである、次の出力が表示されます `DUMP RESULT;` 前の手順で。

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)


## <a id="summary"></a>概要

このように、Pig コマンドでは、MapReduce 操作を対話的に実行できるだけでなく、バッチ ファイルに格納された Pig Latin ジョブも実行できます。

## <a id="nextsteps"></a>次のステップ

HDInsight での Pig に関する全般的な情報:

* [HDInsight での Hadoop での Pig の使用](hdinsight-use-pig.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hadoop の Hive を使用します。](hdinsight-use-hive.md)

* [HDInsight での Hadoop の MapReduce を使用します。](hdinsight-use-mapreduce.md)





