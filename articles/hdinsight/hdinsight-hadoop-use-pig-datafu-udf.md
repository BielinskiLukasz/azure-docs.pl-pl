<properties
pageTitle="HDInsight の Pig で DataFu を使用する"
description="DataFu は、Hadoop で使用するライブラリのコレクションです。HDInsight クラスターの Pig で DataFu を使用する方法について説明します。"
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="11/06/2015"
ms.author="larryfr"/>


# HDInsight の Pig で DataFu を使用する

DataFu は、Hadoop で使用するオープン ソース ライブラリのコレクションです。 このドキュメントでは、HDInsight クラスターで DataFu を使用する方法、および Pig で DataFu ユーザー定義関数 (UDF) を使用する方法を説明します。

## 前提条件

* Azure サブスクリプション。

* Azure HDInsight クラスター (Linux または Windows ベース)

* の基礎知識 [HDInsight で Pig を使用して](hdinsight-use-pig.md)

## Linux ベースの HDInsight に DataFu をインストールする

> [AZURE.NOTE] DataFu は、Windows ベースの HDInsight クラスターにプレインストールされます。 Windows ベースのクラスターを使用している場合は、このセクションをスキップしてください。

DataFu は、Maven リポジトリからダウンロードしてインストールできます。 HDInsight クラスターに DataFu を追加するには、次の手順を使用します。

1. SSH を使用して、Linux ベースの HDInsight クラスターに接続します。 HDInsight での SSH の使用方法の詳細については、次のいずれかのドキュメントをご覧ください。

    * [Unix、Linux、OS X から HDInsight 上の Linux ベースの Hadoop で SSH を使用します。](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Windows から Linux ベースの Hadoop で SSH を使用します。](hdinsight-hadoop-linux-use-ssh-unix.md)

2. 次のコマンドを使用して wget ユーティリティで DataFu jar ファイルをダウンロードするか、またはリンクをコピーしてブラウザーに貼り付けてダウンロードを開始します。

        wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar

3. 次に、HDInsight クラスターの既定のストレージにファイルをアップロードします。 これにより、クラスターのすべてのノードでファイルを使用できるようになり、クラスターを削除して再作成してもファイルはストレージに残っています。

        hdfs dfs -put datafu-1.2.0.jar /example/jars

    > [AZURE.NOTE] 上記の例で jar ファイルを格納する `wasb:///例/jar` クラスター記憶域上、このディレクトリが既に存在するためです。 HDInsight クラスター ストレージのどこでも使用できます。

## Pig で DataFu を使用する

読者は HDInsight での Pig の使用に慣れていることが前提なので、このセクションの手順では Pig Latin ステートメントのみを示し、クラスターでそれを使用する方法の手順は示しません。 HDInsight での Pig の使用に関する詳細については、次を参照してください。 [HDInsight での Pig の使用](hdinsight-use-pig.md)します。
> [AZURE.IMPORTANT] Linux ベースの HDInsight クラスター上の Pig から DataFu を使用する場合は、最初に、次の Pig Latin ステートメントを使用して jar ファイルを登録する必要があります。
>
> `wasb:///example/jars/datafu-1.2.0.jar を登録します。`
>
> Windows ベースの HDInsight クラスターでは、DataFu は既定で登録されます。

通常は、DataFu の関数にエイリアスを定義します。 次に例を示します。

    DEFINE SHA datafu.pig.hash.SHA();

という名前のエイリアスを定義 `SHA` の SHA ハッシュ関数。 その後は、Pig Latin スクリプトでこれを使用して、入力データのハッシュを生成できます。 たとえば、次の例では入力データ内の名前をハッシュ値に置き換えています。

    raw = LOAD '/data/raw/' USING PigStorage(',') AS  
        (name:chararray, 
        int1:int, 
        int2:int,
        int3:int); 
    mask = FOREACH raw GENERATE SHA(name), int1, int2, int3; 
    DUMP mask;

これを次の入力データで使用すると、

    Lana Zemljaric,5,9,1
    Qiong Zhong,9,3,6
    Sandor Harsanyi,0,7,3
    Roko Petkovic,2,6,2
    Tibor Rozsa,8,0,0
    Lea Hrastovsek,6,3,6
    Regina Toth,2,1,2
    Eva Makay,8,9,2
    Shi Liao,4,6,0
    Tjasa Zemljaric,0,2,5

次のような出力が生成されます。

    (c1a743b0f34d349cfc2ce00ef98369bdc3dba1565fec92b4159a9cd5de186347,5,9,1)
    (713d030d621ab69aa3737c8ea37a2c7c724a01cd0657a370e103d8cdecac6f99,9,3,6)
    (7a5f5abdd281f68168199319d98a1a662535f988d1443b3a3c497010937bac89,0,7,3)
    (a94818e93807e12079c4b35f8f3c8c8ef8e8acd1954e7f0476bc1a3a86fc96a9,2,6,2)
    (894ead4f48af91df7e088241218a23157bede7c52115272e417e95c046d48902,8,0,0)
    (6f99f163af3448fda672087db306f363e27a98a9e49c1f274a0860e303f8aec4,6,3,6)
    (a03de92a28be3c6a984c7a153fa9ed81c0413f76a9401955b5f7e04a5dd0ab9f,2,1,2)
    (6ceab977c8fb48d9ad0dc413e6bc646cabd89f22e7ab97a6b0133f3d225c6013,8,9,2)
    (fa9c436469096ff1bd297e182831f460501b826272ae97e921f5f6e3f54747e8,4,6,0)
    (bc22db7c238b86c37af79a62c78f61a304b35143f6087eb99c34040325865654,0,2,5)

## 次のステップ

DataFu または Pig の詳細については、次のドキュメントを参照してください。

* [Apache DataFu Pig ガイド](http://datafu.incubator.apache.org/docs/datafu/guide.html)します。

* [HDInsight での Pig を使用します。](hdinsight-use-pig.md)





