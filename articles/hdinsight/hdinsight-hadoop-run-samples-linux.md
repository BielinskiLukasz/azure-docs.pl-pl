<properties
    pageTitle="Linux ベースの HDInsight での Hadoop MapReduce サンプルの実行 | Microsoft Azure"
    description="Linux ベースの HDInsight で MapReduce サンプルの使用を開始します。 SSH を使用してクラスターに接続し、Hadoop コマンドを使用してサンプル ジョブを実行します。"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/04/2015"
    ms.author="larryfr"/>




#HDInsight での Hadoop のサンプルの実行

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Linux ベースの HDInsight クラスターには、Hadoop MapReduce ジョブの実行について理解するのに使用できる MapReduce のサンプル セットが用意されています。 このドキュメントでは、使用可能なサンプルと一部のサンプルの実行手順について説明します。

##前提条件

- **Azure サブスクリプション**: を参照してください [取得 Azure 無料試用版](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

- **Linux ベースの HDInsight クラスター**: を参照してください [Linux での HDInsight で Hive と Hadoop の使用を開始](hdinsight-hadoop-linux-tutorial-get-started.md)

- **SSH クライアント**: HDInsight で SSH の使用方法の詳細については、次の記事を参照してください。

    - [Linux、Unix、または OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

## サンプル ##

**場所**: サンプルは、HDInsight クラスター上にある  **/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar**

**内容**: 次のサンプルはこのアーカイブに含まれています。

- **aggregatewordcount**: する集計ベースの入力ファイル内の単語をカウントする map/reduce プログラム
- **aggregatewordhist**: する集計ベースの入力ファイル内の単語のヒストグラムを計算する map/reduce プログラム
- **bbp**: Bailey Borwein-Plouffe を使用して Pi の正確な数字を計算する map/reduce プログラム
- **dbcount**: データベースから、ページビューをカウントするサンプル ジョブのカウント
- **distbbp**: bbp 公式数式を使用して Pi の正確なビットを計算する map/reduce プログラム
- **正規表現**: 入力内の正規表現の一致項目をカウントする map/reduce プログラム
- **結合**: で並べ替えられ、均等に分割されたデータセットの結合に影響するジョブ
- **multifilewc**: いくつかのファイルから単語をカウントするジョブ
- **pentomino**: pentomino 問題の解決策を検索するプログラムを並べて表示する map/reduce
- **pi**: 準モンテカルロを使用して Pi を推定する map/reduce プログラム Carlo メソッド
- **randomtextwriter**: 10 GB の 1 つのノードのランダムなテキスト データを書き込む map/reduce プログラム
- **randomwriter**: 10 GB の 1 つのノードのランダムなデータを書き込む map/reduce プログラム
- **secondarysort**: reduce に 2 番目の並べ替えを定義する例
- **並べ替え**: ランダム ライターによって書き込まれたデータを並べ替える map/reduce プログラム
- **数独**: 数独問題を解く
- **teragen**: terasort のデータを生成
- **terasort**: terasort を実行します。
- **teravalidate**: terasort の結果を確認します。
- **wordcount**: 入力ファイル内の単語をカウントする map/reduce プログラム
- **wordmean**: 入力ファイル内の単語の平均の長さをカウントする map/reduce プログラム
- **wordmedian**: 入力ファイル内の単語の中央の長さをカウントする map/reduce プログラム
- **wordstandarddeviation**: 入力ファイル内の単語の長さの標準偏差をカウントする map/reduce プログラム

**ソース コード**: HDInsight クラスター上のこれらのサンプルのソース コードが含まれている **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples**

> [AZURE.NOTE]  `2.2.4.9-1` パスには、HDInsight クラスターの Hortonworks Data Platform のバージョンと、HDInsight が更新されると変更することがあります。

## サンプルの実行方法 ##

1. 次の記事の説明に従って、SSH を使用して HDInsight に接続します。

    - [Linux、Unix、または OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

2. `username@#######:~$` プロンプトで、次のコマンドを使用してサンプルの一覧を表示します。

        hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar

    これにより、このドキュメントの前のセクションのサンプル一覧が生成されます。

3. 次のコマンドを使用して、特定のサンプルのヘルプを表示します。 ここで、 **wordcount** サンプル。

        hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount

    次のメッセージが表示されます。

        Usage: wordcount <in> [<in>...] <out>

    これは、ソース ドキュメントに複数の入力パスを指定できることを示し、最後のパスは出力 (ソース ドキュメント内の単語数) が配置される場所を示しています。

4. 次のコマンドを使用して、クラスタでサンプルデータとして提供されているレオナルド·ダ·ヴィンチの手記の単語をすべてカウントします。

        hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount

    このジョブはからの入力 **wasb:///example/data/gutenberg/davinci.txt**します。

    次の例の出力 **wasb:////例/データ/davinciwordcount**します。

    > [AZURE.NOTE] 前述したワード カウント サンプルのヘルプで、複数の入力ファイルを指定することもできます。 たとえば、`hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` は davinci.txt と ulysses.txt の両方の単語をカウントします。

5. ジョブが完了したら、次のコマンドを使用して出力を表示します。

        hadoop fs -cat /example/data/davinciwordcount/*

    これにより、ジョブで生成されたすべての出力ファイルが連結して表示されます。 この基本的な例では、1 つのファイルがあるだけですが、複数のファイルがある場合、このコマンドはそのすべてを反復処理します。

    このコマンドから次のような出力が表示されます。

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    各行は、単語と入力データで発生した回数を表します。

## 数独

数独の例には、あまり役に立たない使用法の説明 "コマンド ラインにパズルを含める" があります。

[数独](https://en.wikipedia.org/wiki/Sudoku) の 9 つの 3 x 3 グリッドで構成される論理パズルです。 いくつかのグリッドのセルには数字がありますが、他のセルは空白になっています。空白のセルを解決するとゴールです。 パズルの詳細については、上記のリンクにありますが、このサンプルの目的は空白のセルを解決することです。 そのため、入力は次の形式のファイルである必要があります。

- 9 つの行と 9 つの列

- 各列には、数字または `?` (空白のセルを示す) を含めることができる

- セルはスペースで区切られる

ここで、1 つの数字を 1 つの列または行で繰り返すことができないという、数独パズルを作成する一定の方法があります。 ありがたいことに、HDInsight クラスターに正しく作成された例があります。 場所は **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta** 次が含まれています。

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

> [AZURE.NOTE]  `2.2.4.9-1` の HDInsight クラスターへの更新が行われると、パスの部分を変更することがあります。

数独の例でこれを実行するには、次のコマンドを使用します。

    hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta

結果は次のようになります。

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## Pi (π)

pi サンプルでは統計的手法 (準モンテカルロ法) に基づいて Pi の値を計算します。 単位正方形の内部にランダムに配置された点は、その正方形に内接する円の内部にも円の面積に等しい確率 (Pi/4) で配置されます。 Pi の値は 4R という値で計算されます。ここで R は、正方形の内部にある点の総数と、円の内部にある点の数との比率です。 サンプルの点の数が大きくなるほど、推定値の精度が上がります。

このサンプルの mapper は、単位正方形の内部にランダムに多数の点を生成し、円の内部にある点の数を計算します。

reducer は、mapper が計算した点の数を累計して、4R という公式から Pi の値を推定します。ここで R は、正方形の内部にある点の総数と、円の内部にある点の数との比率です。

次のコマンドを使用して、このサンプルを実行します。 これは、10,000, 000 のサンプルごとに 16 のマップを使用して、pi の値を推定します。

    hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000

これによって返される値はようになります **3.14159155000000000000**します。 参考までに、Pi の小数点以下 10 桁までは 3.1415926535 です。

##10GB Greysort

GraySort はベンチマーク ソートで、その評価尺度は、非常に大量のデータ、通常は最低でも 100 TB のデータをソートした際のソート速度 (TB/分) です。

このサンプルでは、比較的高速に実行できるように、中程度のサイズの 10 GB のデータを使用します。 使用する MapReduce アプリケーションは Owen O'Malley と Arun Murthy が開発したもので、2009 年にはテラバイト ソート ベンチマークの汎用目的 ("daytona") 部門で 0.578 TB/分 (173 分で 100 TB) という年間記録を樹立しました。 これも含めたソート ベンチマークの詳細については、次を参照してください。、 [Sortbenchmark](http://sortbenchmark.org/) サイトです。

このサンプルでは 3 組の MapReduce プログラムを使用します。

- **TeraGen**: ソートするデータの行を生成して MapReduce プログラム

- **TeraSort**: 入力データをサンプリングし、MapReduce を合計順に、データの並べ替えを使用して

    TeraSort は MapReduce 関数の標準ソートです。ただし、各 reduce のキー範囲を定義する N-1 個のサンプリングされたキーのソート済みリストを使用するカスタム partitioner を使用します。 特に、sample[i-1] <= key < sample[i] となるキーはすべて reduce i に送られます。 このため、reduce i の出力がすべて reduce i+1 の出力より小さくなることが保証されます。

- **TeraValidate**: 出力がグローバルにソートされていることを検証する MapReduce プログラム

    出力ディレクトリ内のファイルごとにマップを 1 つ作成します。各マップは各キーが前のキー以下であることを保証します。 map 関数は、各ファイルの最初のキーと最後のキーの記録も生成し、reduce 関数は、ファイル i の最初のキーがファイル i-1 の最後のキーよりも大きいことを確認します。 問題が見つかった場合は、reduce の出力として範囲外のキーがレポートされます。

次の手順を使用してデータを生成、ソートし、出力を検証します。

1. 10 GB ので、HDInsight クラスターの既定のストレージに格納されるデータを生成 **wasb://///データ/10 GB の並べ替えの入力例**:

        hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input

    `-Dmapred.map.tasks` は、このジョブに使用する map タスクの数を Hadoop に伝えます。 最後の 2 つのパラメーターを 10 GB 分のデータを作成し、それを格納するジョブを指示する **wasb://///データ/10 GB の並べ替えの入力例**します。

2. 次のコマンドを使用して、データをソートします。

        hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output

    `-Dmapred.reduce.tasks` は、このジョブに使用する reduce タスクの数を Hadoop に伝えます。 最後の 2 つのパラメーターは、単なるデータの入力と出力の場所です。

3. 次のコマンドを使用して、ソートによって生成されたデータを検証します。

        hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate

##次のステップ ##

この記事では、Linux ベースの HDInsight クラスターに付属するサンプルを実行する方法を説明しました。 HDInsight で Pig、Hive、MapReduce を使用する方法のチュートリアルについては、次のトピックをご覧ください。

* [HDInsight での Pig と Hadoop の使用][hdinsight-use-pig]
* [HDInsight での Hive と Hadoop の使用][hdinsight-use-hive]
* [HDInsight での MapReduce と Hadoop の使用] [hdinsight-use-mapreduce]



[hdinsight-errors]: hdinsight-debug-jobs.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

