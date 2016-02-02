<properties
    pageTitle="Linux ベースの HDInsight 用 Java MapReduce プログラムの開発 | Microsoft Azure"
    description="Java MapReduce プログラムを開発する方法と、それらのプログラムを Linux ベースの HDInsight にデプロイする方法について説明します。"
    services="hdinsight"
    editor="cgronlun"
    manager="paulettm"
    authors="Blackmist"
    documentationCenter=""
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="12/04/2015"
    ms.author="larryfr"/>


# HDInsight での Hadoop 用 Java MapReduce プログラムの開発

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-maven-mapreduce-selector.md)]

このドキュメントでは、Apache Maven を使用して MapReduce アプリケーションを作成し、HDInsight クラスターの Linux ベースの Hadoop にこれをデプロイして実行する手順について説明します。 HDInsight クラスターでの Windows ベースの Hadoop の使用方法の詳細については、を参照してください [(Windows) を HDInsight での Hadoop 用 Java MapReduce プログラム](hdinsight-develop-deploy-java-mapreduce.md)。

## <a name="prerequisites"></a>前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 またはそれ以降 (または同等の OpenJDK など)

- [Apache Maven](http://maven.apache.org/)

- **Azure サブスクリプション**

- **Azure CLI**: 詳細については、を参照してください [をインストールし、Azure cli](../xplat-cli-install.md)。

## 環境変数を構成する

Java と JDK をインストールするときに、次のような環境変数が設定される場合があります。 ただし、これらが存在するかどうかや、システムに対して適切な値が含まれているかを確認する必要があります。

* **JAVA_HOME** - Java ランタイム環境 (JRE) がインストールされているディレクトリを指している必要があります。 たとえば、OS X、Unix または Linux のシステムで値が表示のような `/usr/lib/jvm/java-7-oracle`します。 Windows ではのような値には `c:\Program Files (x86)\Java\jre1.7`

* **PATH** - 次のパスを含む必要があります。

    * **JAVA_HOME** または同等のパス

    * **JAVA_HOME\bin** または同等のパス

    * Maven がインストールされているディレクトリ

## 新しい Maven プロジェクトを作成する

1. ターミナル セッションまたは開発環境のコマンド ラインから、このプロジェクトを格納する場所にディレクトリを変更します。

3. Maven でインストールされた __mvn__ コマンドを使用し、プロジェクトのスキャフォールディングを生成します。

        mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    これにより、__artifactID__ パラメーターにより指定された名前で、新しいディレクトリが現在のディレクトリに作成されます (この例では **wordcountjava**)。 このディレクトリには、次の項目が含まれます。

    * __pom.xml__ - [プロジェクト オブジェクト モデル (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) プロジェクトをビルドに使用される情報と構成の詳細が含まれています。

    * __src__ - アプリケーションを作成する __main/java/org/apache/hadoop/examples__ ディレクトリを格納するディレクトリです。

3. __src/test/java/org/apache/hadoop/examples/apptest.java__ ファイルはこの例で使用しないため、削除します。

## 依存関係を追加する

1. 編集、 __pom.xml__ ファイルし、次の追加の内部、 `< 依存関係 >` セクション。

        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-examples</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-client-common</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-common</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>

    これは Maven でに対して (内に示される < artifactId\ >) (< バージョン] \ > 内に記載) 特定のバージョンとライブラリがプロジェクトに必要です。 これはコンパイル時に、既定の Maven リポジトリからダウンロードされます。 使用することができます、 [Maven リポジトリ検索](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) の詳細を表示します。

    `< 範囲 > が提供する </スコープ >` Maven に通知これらの依存関係をアプリケーションに付属せず、実行時に HDInsight クラスターで提供されます。

2. __pom.xml__ ファイルに次のコードを追加します。これは、中になければなりません、 `< プロジェクト >] </project >` タグは、ファイルなどの間で `</依存関係 >` と `</project >`します。

        <build>
          <plugins>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.3</version>
              <configuration>
                <transformers>
                  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                  </transformer>
                </transformers>
              </configuration>
              <executions>
                <execution>
                  <phase>package</phase>
                    <goals>
                      <goal>shade</goal>
                    </goals>
                </execution>
              </executions>
            </plugin>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
              <configuration>
               <source>1.7</source>
               <target>1.7</target>
              </configuration>
            </plugin>
          </plugins>
        </build>

    最初のプラグインを構成、 [Maven Shade Plugin](http://maven.apache.org/plugins/maven-shade-plugin/), を使用して uberjar (fatjar とも呼ばれます)、アプリケーションで必要な依存関係を含むをビルドします。 また、一部のシステムで問題が発生する可能性がある jar パッケージ内のライセンスの重複を防止します。

    2 番目のプラグインは Maven コンパイラを構成します。これは、HDInsight クラスターで使用されるバージョンに、このアプリケーションで必要な Java バージョンを設定するために使用されます。

3. __pom.xml__ ファイルを保存します。

## MapReduce アプリケーションを作成する

1. 移動して、 __wordcountjava/src/main/java/組織/apache/hadoop/例__ ディレクトリおよび名前の変更、 __App.java__  ファイルを __WordCount.java__します。

2. テキスト エディターで __WordCount.java__ ファイルを開き、内容を次のように置き換えます。

     package org.apache.hadoop.examples;
    
     import java.io.IOException;
     import java.util.StringTokenizer;
     import org.apache.hadoop.conf.Configuration;
     import org.apache.hadoop.fs.Path;
     import org.apache.hadoop.io.IntWritable;
     import org.apache.hadoop.io.Text;
     import org.apache.hadoop.mapreduce.Job;
     import org.apache.hadoop.mapreduce.Mapper;
     import org.apache.hadoop.mapreduce.Reducer;
     import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
     import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
     import org.apache.hadoop.util.GenericOptionsParser;
    
     public class WordCount {
    
       public static class TokenizerMapper
            extends Mapper<Object, Text, Text, IntWritable>{
    
         private final static IntWritable one = new IntWritable(1);
         private Text word = new Text();
    
         public void map(Object key, Text value, Context context
                         ) throws IOException, InterruptedException {
           StringTokenizer itr = new StringTokenizer(value.toString());
           while (itr.hasMoreTokens()) {
             word.set(itr.nextToken());
             context.write(word, one);
           }
         }
       }
    
       public static class IntSumReducer
            extends Reducer<Text,IntWritable,Text,IntWritable> {
         private IntWritable result = new IntWritable();
    
         public void reduce(Text key, Iterable<IntWritable> values,
                            Context context
                            ) throws IOException, InterruptedException {
           int sum = 0;
           for (IntWritable val : values) {
             sum += val.get();
           }
           result.set(sum);
           context.write(key, result);
         }
       }
    
       public static void main(String[] args) throws Exception {
         Configuration conf = new Configuration();
         String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
         if (otherArgs.length != 2) {
           System.err.println("Usage: wordcount <in> <out>");
           System.exit(2);
         }
         Job job = new Job(conf, "word count");
         job.setJarByClass(WordCount.class);
         job.setMapperClass(TokenizerMapper.class);
         job.setCombinerClass(IntSumReducer.class);
         job.setReducerClass(IntSumReducer.class);
         job.setOutputKeyClass(Text.class);
         job.setOutputValueClass(IntWritable.class);
         FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
         FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
         System.exit(job.waitForCompletion(true) ? 0 : 1);
       }
     }

 パッケージ名は **org.apache.hadoop.examples** で、クラス名は **WordCount** です。 これらの名前は MapReduce ジョブを送信するときに使用します。

3. ファイルを保存します。

## アプリケーションのビルド

1. __wordcountjava__ ディレクトリに移動します (現在、このディレクトリではない場合)。

2. 次のコマンドを使用して、アプリケーションを含む JAR ファイルをビルドします。

        mvn clean package

    これにより、前のビルド アーティファクトを整理し、まだインストールされていない依存関係をダウンロードして、アプリケーションをビルドおよびパッケージ化します。

3. コマンドが終了すると、__wordcountjava/target__ ディレクトリに __wordcountjava-1.0-SNAPSHOT.jar__ という名前のファイルが格納されます。
    > [AZURE.NOTE] __wordcountjava-1.0-SNAPSHOT.jar__ ファイルは uberjar です。これには、WordCount ジョブだけでなく、ジョブの実行時に必要な依存関係も含まれます。


## <a id="upload"></a>Jar をアップロードします。

次のコマンドを使用して、HDInsight ヘッドノードに jar ファイルをアップロードします。

    scp wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
    
    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

これにより、ファイルがローカル システムからヘッド ノードにコピーされます。
> [AZURE.NOTE] SSH アカウントのセキュリティ保護にパスワードを使用している場合は、パスワードの入力が求められます。 SSH キーを使用している場合は、使用する必要があります、 `-i` パラメーターと、秘密キーへのパス。 たとえば、 `scp-i/パス/に/プライベート/key wordcountjava 1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`します。

## <a name="run"></a>MapReduce ジョブを実行します。

1. 次の記事の説明に従って、SSH を使用して HDInsight に接続します。

    - [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH を使用します。](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Windows から Linux ベースの Hadoop で SSH を使用します。](hdinsight-hadoop-linux-use-ssh-windows.md)

2. SSH セッションから、次のコマンドを使用して MapReduce アプリケーションを実行します。

        hadoop jar wordcountjava.jar org.apache.hadoop.examples.WordCount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/wordcountout

    これにより davinci.txt ファイル内の単語をカウントするワード カウント MapReduce アプリケーションを使用し、結果の保存は __wasb:////例/データ/wordcountout__します。 入力ファイルと出力は、両方ともクラスターの既定のストレージに格納されます。

3. ジョブが完了したら、次のコマンドを使用して結果を表示します。

        hadoop fs -cat wasb:///example/data/wordcountout/*

    次のような文字と回数の値の一覧が表示されます。

        zeal    1
        zelus   1
        zenith  2


## <a id="nextsteps"></a>次のステップ

このドキュメントでは、Java MapReduce ジョブを作成する方法を説明しました。 HDInsight を使用する他の方法については、次のドキュメントを参照してください。

- [[Hdinsight を使用して hive] HDInsight での Hive を使用します。][hdinsight-use-hive]
- [[Hdinsight での pig の使用] を HDInsight での Pig を使用します。][hdinsight-use-pig]
- [HDInsight での MapReduce を使用します。](hdinsight-use-mapreduce.md)

詳細については、「関連項目、 [Java デベロッパー センター](http://azure.microsoft.com/develop/java/)します。


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/ 
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/ 
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/ 
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md 
[hdinsight-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md 
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md 
[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md 
[hdinsight-upload-data]: hdinsight-upload-data.md 
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md 
[hdinsight-use-hive]: hdinsight-use-hive.md 
[hdinsight-use-pig]: hdinsight-use-pig.md 
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md 
[powershell-pscredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx 

