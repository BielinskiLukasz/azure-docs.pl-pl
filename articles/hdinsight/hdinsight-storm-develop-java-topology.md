<properties
   pageTitle="Apache Storm の Java ベースのトポロジの開発 | Microsoft Azure"
   description="簡単なワード カウント トポロジを作成して、Java で Storm トポロジを作成する方法について説明します。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/04/2015"
   ms.author="larryfr"/>

#HDInsight で Apache Storm と Maven を使用する基本的なワード カウント アプリケーションの Java ベースのトポロジの開発

Maven を使用して HDInsight での Apache Storm の Java ベース トポロジを作成する基本的な手順を説明します。 Maven と Java を使用した基本的なワード カウント アプリケーションの作成手順をご覧いただけます。 ここでは Eclipse の使用を前提としていますが、お好きなテキスト エディターをお使いいただけます。

このドキュメントの手順を完了したら、HDInsight で Apache Storm にデプロイできる基本的なトポロジが完成します。

##前提条件

* <a href="https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html" target="_blank">Java Developer Kit (JDK) バージョン 7</a>

* <a href="https://maven.apache.org/download.cgi" target="_blank">Maven</a>: Maven は Java プロジェクトのプロジェクト ビルド システムです

* メモ帳、<a href="http://www.gnu.org/software/emacs/" target="_blank">Emacs<a>、<a href="http://www.sublimetext.com/" target="_blank">Sublime Text</a>、<a href="https://atom.io/" target="_blank">Atom.io</a>、<a href="http://brackets.io/" target="_blank">Brackets.io</a> などのテキスト エディター。 また、<a href="https://eclipse.org/" target="_blank">Eclipse</a> (バージョン Luna またはそれ以降) などの統合開発環境 (IDE) を使用することもできます。

    > [AZURE.NOTE] エディターまたは IDE には、このドキュメントで扱っていない Maven を操作するための特定の機能がある場合があります。 お使いの編集環境の機能に関する詳細は、製品のマニュアルをご覧ください。

##環境変数を構成する

Java と JDK をインストールするときに、次のような環境変数が設定される場合があります。 ただし、これらが存在するかどうかや、システムに対して適切な値が含まれているかを確認する必要があります。

* **JAVA_HOME** -Java ランタイム環境 (JRE) がインストールされているディレクトリを指す必要があります。 たとえば、Unix や Linux ディストリビューションの場合は、`/usr/lib/jvm/java-7-oracle` のような値になります。 Windows の場合は、`c:\Program Files (x86)\Java\jre1.7` のような値になります。

* **パス** -次のパスを含める必要があります。

    * **JAVA_HOME** (または同等のパス)

    * **Java_home \bin** (または同等のパス)

    * Maven がインストールされているディレクトリ

##新しい Maven プロジェクトを作成する

コマンド ラインからという名前の新しい Maven プロジェクトを作成する次のコードを使用して **WordCount**:

    mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

これはという名前の新しいディレクトリを作成 **WordCount** 基本的な Maven プロジェクトを含む、現在の位置。

 **WordCount** ディレクトリが、次の項目を格納します。

* **pom.xml**: Maven プロジェクトの設定が含まれます。

* **src \main\java\com\microsoft\example**: アプリケーション コードが含まれています。

* **src \test\java\com\microsoft\example**: アプリケーションのテストが含まれています。 今回の例では、テストは作成しません。

###サンプル コードを削除する

ここではアプリケーションを作成するため、生成されたテスト ファイルとアプリケーション ファイルを削除します。

*  **src\test\java\com\microsoft\example\AppTest.java**

*  **src\main\java\com\microsoft\example\App.java**

##依存関係を追加する

これは Storm トポロジであるため、Storm コンポーネントの依存関係を追加する必要があります。 開いている、 **pom.xml** ファイルし、次のコードを追加、 **& lt; の依存関係 >** セクション。

    <dependency>
      <groupId>org.apache.storm</groupId>
      <artifactId>storm-core</artifactId>
      <version>0.9.2-incubating</version>
      <!-- keep storm out of the jar-with-dependencies -->
      <scope>provided</scope>
    </dependency>

コンパイル時に、Maven が使用してこの情報を検索する **storm コア** Maven レポジトリでします。 まず、ローカル コンピューター上のレポジトリを検索します。 ファイルが見つからない場合は、パブリックの Maven レポジトリをダウンロードして、それをローカル レポジトリに保存します。

> [AZURE.NOTE] 通知、 `<scope>provided</scope>` 追加したセクション内の行。 これは除外する Maven **storm コア** すべての JAR ファイルからを作成する、システムによって提供されるためです。 これにより、パッケージを少し小さくするために作成して確実に使用すること、 **storm コア** HDInsight クラスター上の Storm に含まれているビットです。

##ビルド構成

Maven プラグインでは、プロジェクトのコンパイル方法や、JAR ファイルへのパッケージ方法といったプロジェクトのビルド ステージをカスタマイズできます。 開いている、 **pom.xml** ファイルし、直上の次のコードを追加、 `</project>` 行です。

    <build>
      <plugins>
      </plugins>
    </build>

このセクションは、プラグインや他のビルド構成オプションを追加する際に使用します。

###プラグインの追加

Storm トポロジの場合、<a href="http://mojo.codehaus.org/exec-maven-plugin/" target="_blank">Exec Maven プラグイン</a>が便利です。Exec Maven プラグインを使用すると、開発環境でトポロジをローカルに実行することが簡単にできます。 次のコードを追加、 `<plugins>` のセクションで、 **pom.xml** Exec Maven プラグインを含むファイル。

    <plugin>
      <groupId>org.codehaus.mojo</groupId>
      <artifactId>exec-maven-plugin</artifactId>
      <executions>
        <execution>
        <goals>
          <goal>exec</goal>
        </goals>
        </execution>
      </executions>
      <configuration>
        <executable>java</executable>
        <includeProjectDependencies>true</includeProjectDependencies>
        <includePluginDependencies>false</includePluginDependencies>
        <classpathScope>compile</classpathScope>
        <mainClass>${storm.topology}</mainClass>
      </configuration>
    </plugin>

別の役立つプラグインとして <a href="http://maven.apache.org/plugins/maven-compiler-plugin/" target="_blank">Apache Maven Compiler プラグイン</a>があり、コンパイル オプションを変更するために使用します。 これは主に、Maven がアプリケーションのソースとターゲットに使用する Java バージョンを変更する際に必要になります。 使用するバージョンは、1.7 です。

次の追加、 `<plugins>` のセクション、 **pom.xml** ファイル Apache Maven Compiler プラグインを追加し、ソースとターゲットのバージョンを 1.7 に設定します。

    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

##トポロジを作成する

Java ベースの Storm トポロジは、作成か依存関係として参照する必要のある 3 つのコンポーネントで構成されます。

* **スパウト**: 外部からのデータ ソースに読み取りデータのストリームをトポロジに出力します。

* **ボルト**: スパウトや他のボルトから出力されるストリームの処理を実行し、1 つまたは複数のストリームを出力します。

* **トポロジ**: 方法を定義、スパウトとボルトの配置し、トポロジのエントリ ポイントを提供します。

###スパウトを作成する

外部データソースの設定に必要な要件を軽減するため、次のスパウトは単純にランダムにセンテンスを出力します。 提供されているスパウトの変更済みバージョンは、(<a href="https://github.com/apache/storm/blob/master/examples/storm-starter/" target="_blank">Storm Starter 例</a>)。

> [AZURE.NOTE] 外部データ ソースから読み取りを行うスパウトの例は、次の例のいずれかを参照してください。
>
> * <a href="https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java" target="_blank">TwitterSampleSpout</a>: Twitter から読み取りを行うスパウトの例
>
> * <a href="https://github.com/apache/storm/tree/master/external/storm-kafka" target="_blank">Storm Kafka</a>: Kafka から読み取りを行うスパウトの例

という名前の新しいファイルを作成、スパウトの **RandomSentenceSpout.java** で、 **src \main\java\com\microsoft\example** ディレクトリと次の内容として使用します。

    /**
     * Licensed to the Apache Software Foundation (ASF) under one
     * or more contributor license agreements.  See the NOTICE file
     * distributed with this work for additional information
     * regarding copyright ownership.  The ASF licenses this file
     * to you under the Apache License, Version 2.0 (the
     * "License"); you may not use this file except in compliance
     * with the License.  You may obtain a copy of the License at
     *
     * http://www.apache.org/licenses/LICENSE-2.0
     *
     * Unless required by applicable law or agreed to in writing, software
     * distributed under the License is distributed on an "AS IS" BASIS,
     * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     * See the License for the specific language governing permissions and
     * limitations under the License.
     */

     /**
      * Original is available at https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/storm/starter/spout/RandomSentenceSpout.java
      */

    package com.microsoft.example;

    import backtype.storm.spout.SpoutOutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichSpout;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Values;
    import backtype.storm.utils.Utils;

    import java.util.Map;
    import java.util.Random;

    //This spout randomly emits sentences
    public class RandomSentenceSpout extends BaseRichSpout {
      //Collector used to emit output
      SpoutOutputCollector _collector;
      //Used to generate a random number
      Random _rand;

      //Open is called when an instance of the class is created
      @Override
      public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
      //Set the instance collector to the one passed in
        _collector = collector;
        //For randomness
        _rand = new Random();
      }

      //Emit data to the stream
      @Override
      public void nextTuple() {
      //Sleep for a bit
        Utils.sleep(100);
        //The sentences that will be randomly emitted
        String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
            "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
        //Randomly pick a sentence
        String sentence = sentences[_rand.nextInt(sentences.length)];
        //Emit the sentence
        _collector.emit(new Values(sentence));
      }

      //Ack is not implemented since this is a basic example
      @Override
      public void ack(Object id) {
      }

      //Fail is not implemented since this is a basic example
      @Override
      public void fail(Object id) {
      }

      //Declare the output fields. In this case, an sentence
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("sentence"));
      }
    }

コードのコメントに目を通して、スパウトの仕組みを理解してください。

> [AZURE.NOTE] このトポロジでは、1 つだけのスパウトが、いくつかのトポロジにさまざまなソースからデータを供給する他のユーザーがあります。

###ボルトを作成する

ボルトは、データの処理を扱います。 このトポロジでは、次の 2 つがあります。

* **SplitSentence**: から出力されたセンテンス **RandomSentenceSpout** 個別の単語にします。

* **WordCount**: 各単語が発生した回数をカウントします。

> [AZURE.NOTE] ボルトは、実にあらゆる、たとえば、計算、永続化、または外部コンポーネントと通信を実行できます。

2 つの新しいファイル作成 **SplitSentence.java** と **WordCount.Java** で、 **src \main\java\com\microsoft\example** ディレクトリ。 ファイルの内容として、次を使用します。

**SplitSentence**

    package com.microsoft.example;

    import java.text.BreakIterator;

    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class SplitSentence extends BaseBasicBolt {

      //Execute is called to process tuples
      @Override
      public void execute(Tuple tuple, BasicOutputCollector collector) {
        //Get the sentence content from the tuple
        String sentence = tuple.getString(0);
        //An iterator to get each word
        BreakIterator boundary=BreakIterator.getWordInstance();
        //Give the iterator the sentence
        boundary.setText(sentence);
        //Find the beginning first word
        int start=boundary.first();
        //Iterate over each word and emit it to the output stream
        for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
          //get the word
          String word=sentence.substring(start,end);
          //If a word is whitespace characters, replace it with empty
          word=word.replaceAll("\\s+","");
          //if it's an actual word, emit it
          if (!word.equals("")) {
            collector.emit(new Values(word));
          }
        }
      }

      //Declare that emitted tuples will contain a word field
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("word"));
      }
    }

**WordCount**

    package com.microsoft.example;

    import java.util.HashMap;
    import java.util.Map;

    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class WordCount extends BaseBasicBolt {
      //For holding words and counts
        Map<String, Integer> counts = new HashMap<String, Integer>();

        //execute is called to process tuples
        @Override
        public void execute(Tuple tuple, BasicOutputCollector collector) {
          //Get the word contents from the tuple
          String word = tuple.getString(0);
          //Have we counted any already?
          Integer count = counts.get(word);
          if (count == null)
            count = 0;
          //Increment the count and store it
          count++;
          counts.put(word, count);
          //Emit the word and the current count
          collector.emit(new Values(word, count));
        }

        //Declare that we will emit a tuple containing two fields; word and count
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
          declarer.declare(new Fields("word", "count"));
        }
      }

コードのコメントに目を通して、ボルトの仕組みを理解してください。

###トポロジを作成する

トポロジは、コンポーネント間のデータのフローを定義するグラフにスパウトとボルトを結びつけます。 また、クラスター内にコンポーネントのインスタンスを作成するときに Storm が使用する並列処理のヒントも提供します。

次の図は、このトポロジのコンポーネントの基本的なグラフを示しています。

![スパウトとボルトの配置を示すダイアグラム](./media/hdinsight-storm-develop-java-topology/wordcount-topology.png)

トポロジを実装するには、という名前の新しいファイルを作成 **WordCountTopology.java** で、 **src \main\java\com\microsoft\example** ディレクトリ。 ファイルの内容として、次を使用します。

    package com.microsoft.example;

    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.topology.TopologyBuilder;
    import backtype.storm.tuple.Fields;

    import com.microsoft.example.RandomSentenceSpout;

    public class WordCountTopology {

      //Entry point for the topology
      public static void main(String[] args) throws Exception {
      //Used to build the topology
        TopologyBuilder builder = new TopologyBuilder();
        //Add the spout, with a name of 'spout'
        //and parallelism hint of 5 executors
        builder.setSpout("spout", new RandomSentenceSpout(), 5);
        //Add the SplitSentence bolt, with a name of 'split'
        //and parallelism hint of 8 executors
        //shufflegrouping subscribes to the spout, and equally distributes
        //tuples (sentences) across instances of the SplitSentence bolt
        builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
        //Add the counter, with a name of 'count'
        //and parallelism hint of 12 executors
        //fieldsgrouping subscribes to the split bolt, and
        //ensures that the same word is sent to the same instance (group by field 'word')
        builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

        //new configuration
        Config conf = new Config();
        conf.setDebug(true);

        //If there are arguments, we are running on a cluster
        if (args != null && args.length > 0) {
          //parallelism hint to set the number of workers
          conf.setNumWorkers(3);
          //submit the topology
          StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
        }
        //Otherwise, we are running locally
        else {
          //Cap the maximum number of executors that can be spawned
          //for a component to 3
          conf.setMaxTaskParallelism(3);
          //LocalCluster is used to run locally
          LocalCluster cluster = new LocalCluster();
          //submit the topology
          cluster.submitTopology("word-count", conf, builder.createTopology());
          //sleep
          Thread.sleep(10000);
          //shut down the cluster
          cluster.shutdown();
        }
      }
    }

コードのコメントに目を通して、トポロジがどのように定義されて、クラスターに送信されているかを理解してください。

##ローカルでのトポロジのテスト

ファイルを保存したら、次のコマンドを使用してトポロジをローカルでテストします。

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology

実行中、スタートアップ情報が表示されます。 スパウトからセンテンスが出力されてボルトがそれを処理すると次のような行が表示されます。

    15398 [Thread-16-split] INFO  backtype.storm.daemon.executor - Processing received message source: spout:10, stream: default, id: {}, [an apple a day keeps thedoctor away]]
    15398 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [an]
    15399 [Thread-10-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [an]
    15399 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [apple]
    15400 [Thread-8-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [apple]
    15400 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [a]
    15399 [Thread-10-count] INFO  backtype.storm.daemon.task - Emitting: count default [an, 53]
    15400 [Thread-12-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [a]
    15400 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [day]
    15400 [Thread-8-count] INFO  backtype.storm.daemon.task - Emitting: count default [apple, 53]
    15401 [Thread-10-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [day]
    15401 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [keeps]
    15401 [Thread-12-count] INFO  backtype.storm.daemon.task - Emitting: count default [a, 53]

この出力からわかるように、次の内容が実行されました。

1. スパウトが「an apple a day keeps the doctor away」を出力する

2. Split ボルトがセンテンスから個別の単語を出力する

3. Count ボルトが各単語が出力された回数を出力する

Count ボルトで出力されたデータを見ると、apple が 53 回出力されたことがわかります。 カウントは、同じセンテンスがランダムに何回も出力される間トポロジの実行が終わるまで続行し、カウントがリセットされることはありません。

##Trident

Trident は Storm から提供される大枠の抽象概念です。 ステートフルな処理をサポートします。 Trident の主なメリットは、トポロジが受けるすべてのメッセージが一度しか処理されないよう保証できることです。 これは、メッセージが少なくとも一度は処理されることを保証する未加工型の Java のトポロジでは実現するのが難しい動作です。 他にも、ボルトを作成する代わりに使える組み込みのコンポーネントがあるなどの違いがあります。 実際には、ボルトはフィルター、プロジェクション、関数といった、あまり汎用でないコンポーネントで完全に置き換えられます。

Trident アプリケーションは Maven プロジェクトを使用して作成できます。 この記事で前述した同じ基本の手順の、コードのみを変更して作成できます。

Trident の詳細については、「<a href="http://storm.apache.org/documentation/Trident-API-Overview.html" target="_blank">Trident API の概要</a>」のページをご覧ください。

Trident アプリケーションの例は、次を参照してください。 [Twitter のトレンディング トピックでの Apache Storm の使用](hdinsight-storm-twitter-trending.md)します。

##次のステップ

ここまでで、Java を使用して Storm トポロジを作成する方法を説明しました。 続けて次の記事もご覧ください。

* [HDInsight での Apache Storm トポロジのデプロイと管理](hdinsight-storm-deploy-monitor-topology.md)

* [Visual Studio を使用して HDInsight で Apache Storm の C# トポロジを開発する](hdinsight-storm-develop-csharp-visual-studio-topology.md)

アクセスして、次の例も Storm トポロジを見つけることができます [HDInsight での Storm に関するトポロジ例](hdinsight-storm-example-topology.md)します。


