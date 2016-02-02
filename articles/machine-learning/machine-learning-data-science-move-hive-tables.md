<properties 
    pageTitle="データを作成して BLOB ストレージ から Hive テーブルに読み込む | Microsoft Azure" 
    description="Hive テーブルを作成し、BLOB 内のデータを Hive テーブルに読み込む" 
    services="machine-learning,storage" 
    documentationCenter="" 
    authors="hangzh-msft" 
    manager="jacob.spoelstra" 
    editor="cgronlun"  />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/12/2015" 
    ms.author="hangzh;bradsev" />



# データを作成して Azure BLOB ストレージから Hive テーブルに読み込む

この**メニュー**は、Cortana Analytics Process (CAP) でデータを保存および処理できるターゲット環境にデータを取り込む方法について説明するトピックにリンクしています。

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


## はじめに

**このドキュメント**には Hive テーブルを作成し、Azure BLOB ストレージからデータを読み込む汎用の Hive クエリがあります。 Hive テーブルをパーティション分割する方法や、Optimized Row Columnar (ORC) 形式を使用してクエリのパフォーマンスを向上させる方法についてのガイダンスも提供されます。

## 前提条件

この記事では、以下のことを前提としています。

* Azure のストレージ アカウントが作成されている。 手順を知りたい場合は、を参照してください [Azure ストレージ アカウントの作成](../hdinsight-get-started.md#storage)
* HDInsight サービスでカスタマイズされた Hadoop クラスターがプロビジョニングされている。 手順を知りたい場合は、次を参照してください。 [カスタマイズ Azure HDInsight Hadoop クラスターの高度な分析の](machine-learning-data-science-customize-hadoop-cluster.md)します。
* クラスターへのリモート アクセスを有効にし、ログインし、Hadoop コマンド ライン コンソールを開いている。 手順を知りたい場合は、次を参照してください。 [Hadoop クラスターのヘッド ノードにアクセス](machine-learning-data-science-customize-hadoop-cluster.md#headnode)します。

## Azure BLOB ストレージにデータをアップロードする

提供される指示に従って、Azure の仮想マシンを作成したかどうかは [高度な分析用の Azure の仮想マシンを設定](machine-learning-data-science-setup-virtual-machine.md), にこのスクリプト ファイルがダウンロードされている必要があります、* C:\\Users\\\<user name\>\\Documents\\Data サイエンス スクリプト * 仮想マシン上のディレクトリ。これらの Hive クエリに必要なことは、独自のデータ スキーマと Azure BLOB ストレージの構成を適切なフィールドに接続し、送信できるようにすることだけです。

ここでは、Hive テーブルのデータが**圧縮されていない**表形式であることと、Hadoop クラスターが使用するストレージ アカウントの既定の (または追加の) コンテナーにデータがアップロードされていることを想定しています。

タクシー乗車 Data_ _NYC _ で実践する場合は、最初に 24 時間をダウンロードしなければ <a href="http://www.andresmh.com/nyctaxitrips/" target="_blank">NYC タクシー乗車データ</a> ファイル (12 個の Trip ファイルと 12 個の Fare ファイル)、 **解凍** .csv ファイル、および Azure ストレージの既定 (または適切なコンテナー) にそのアカウントは、次のアップロードにすべてのファイルで説明した手順によって作成された、 [カスタマイズ Azure HDInsight Hadoop クラスターの Advanced Analytics Process and Technology 向け](machine-learning-data-science-customize-hadoop-cluster.md) トピックです。 これで、ストレージ アカウントの既定のコンテナーに .csv ファイルをアップロードするプロセスが見つかります [ページ](machine-learning-data-science-process-hive-walkthrough/#upload)します。

## <a name="submit"></a>Hive クエリを送信する方法

Hive クエリは、以下のものを使用して送信できます。

* クラスターのヘッドノードでの Hadoop コマンド ライン
* IPython Notebook
* Hive エディター
* Azure PowerShell スクリプト

Hive クエリは SQL に似ています。 SQL を使い慣れているユーザーを見つけることがあります、 <a href="http://hortonworks.com/wp-content/uploads/downloads/2013/08/Hortonworks.CheatSheet.SQLtoHive.pdf" target="_blank">SQL の Hive のチート シート</a> に便利です。

Hive クエリの送信時、Hive クエリの出力先を、画面上、ヘッド ノード上のローカル ファイル、または Azure BLOB のどれにするか制御できます。

### Hadoop クラスターのヘッド ノードにある Hadoop コマンド ライン コンソールを介して

クエリが複雑な場合は、一般に、Hadoop クラスターのヘッド ノードから直接 Hive クエリを送信すれば、Hive エディターで送信したり、Azure PowerShell スクリプトを使用した場合よりもターンアラウンド時間が短くなります。

Hadoop クラスターのヘッド ノードにログインし、ヘッド ノードのデスクトップで Hadoop コマンド ラインを開き、次のコマンドを入力します。

    cd %hive_home%\bin

Hadoop コマンド ライン コンソールで Hive クエリを送信する方法は 3 つあります。

* 直接 Hadoop コマンドラインから
* .hql ファイルの使用
* Hive コマンド コンソールから

#### Hive クエリを直接 Hadoop コマンドラインから送信する

ユーザーは、次のようなコマンドを実行して、

    hive -e "<your hive query>;

Hadoop コマンド ラインで簡単な Hive クエリを直接送信できます。 次に例を示します。ここで赤いボックスは Hive クエリを送信するコマンドを囲んでいます。緑色のボックスは Hive クエリの出力を囲んでいます。

![Create workspace](./media/machine-learning-data-science-process-hive-tables/run-hive-queries-1.png)

#### .hql ファイルで Hive クエリを送信する

Hive クエリがより複雑で、複数の行がある場合、Hadoop コマンド ラインまたは Hive コマンド コンソールでクエリを編集することは実用的ではありません。 別の方法は、Hadoop クラスターのヘッド ノードでテキスト エディターを使用して、ヘッド ノードのローカル ディレクトリにある .hql ファイルに Hive クエリを保存することです。 使用して、.hql ファイルで Hive クエリを送信し、 `-f` の引数、 `hive` コマンドを次のようにします。

    `hive -f "<path to the .hql file>"`

#### Hive クエリの進行状況ステータス画面の出力を抑制する

既定では、Hive クエリが Hadoop コマンド ライン コンソールで送信されると、 Map/Reduce ジョブの進行状況が画面に出力されます。 マップ/縮小ジョブの進行状況の画面出力を抑制するのには、引数を使用することができます `-s` (大文字小文字を区別) 次のようにコマンドライン引数。

    hive -S -f "<path to the .hql file>"
    hive -S -e "<Hive queries>"

#### Hive コマンド コンソールで Hive クエリを送信する。

ユーザーを実行して、Hive コマンド コンソールを入力できるも、  `hive` Hadoop コマンドラインからコマンドを使用しでの Hive コマンド コンソールから Hive クエリを実行し、 **hive >** プロンプトです。 たとえば次のようになります。

![Create workspace](./media/machine-learning-data-science-process-hive-tables/run-hive-queries-2.png)

この例では、2 つの赤いボックスは、それぞれ Hive コマンド コンソールに入るために使用するコマンドと、Hive コマンド コンソールで送信された Hive クエリを強調表示しています。 緑色のボックスは、Hive クエリからの出力を強調表示しています。

前の例では、画面上に直接 Hive クエリの結果を出力しています。 ユーザーは、ヘッド ノードにあるローカル ファイルや Azure BLOB に出力を書き込むこともできます。 その後、他のツールを使用して Hive クエリからの出力をさらに分析することもできます。

#### Hive クエリの結果をローカル ファイルに出力する。

Hive クエリの結果をヘッド ノード上のローカル ディレクトリに出力するには、次のように Hadoop コマンド ラインで Hive クエリを送信する必要があります。

    `hive -e "<hive query>" > <local path in the head node>`

#### Hive クエリの結果を Azure BLOB に出力する

ユーザーは、Hadoop クラスターの既定のコンテナー内にある Azure BLOB に Hive クエリの結果を出力することもできます。 これを行う Hive クエリは次のようになります。

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

次の例では、Hive クエリの出力が blob ディレクトリに書き込まれます `queryoutputdir` 内での Hadoop クラスターの既定のコンテナーです。 ここでは、ディレクトリ名のみを指定する必要があります。BLOB 名を指定する必要はありません。 エラーがスローされますディレクトリと blob 名の両方をように指定する場合は、 *wasb:///queryoutputdir/queryoutput.txt*します。

![Create workspace](./media/machine-learning-data-science-process-hive-tables/output-hive-results-2.png)

Azure ストレージ エクスプローラーの (またはそれと同等の) ツールを使用して、Hadoop クラスターの既定のコンテナーを開くと、Hive クエリの出力を BLOB ストレージで確認できます。 指定された文字が名前に含まれる BLOB のみを取得する場合は、フィルター (赤いボックスで強調表示) を適用できます。

![Create workspace](./media/machine-learning-data-science-process-hive-tables/output-hive-results-3.png)

### Hive エディターまたは Azure PowerShell コマンドを介して

ユーザーは、クエリのコンソール (Hive エディター) を使用するために、URL

*https://&#60;Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor*

を Web ブラウザーに入力することもできます。 ログインするために、Hadoop クラスターの資格情報の入力を求められます。

または、 [Hive クエリの実行 PowerShell を使用して](../hdinsight/hdinsight-hadoop-use-hive-powershell.md)します。


## <a name="create-tables"></a>Hive データベースとテーブルを作成します。

Hive クエリは内の共有、 [Github リポジトリ](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) 、そこからダウンロードすることができます。

Hive テーブルを作成する Hive クエリを次に示します。

    create database if not exists <database name>;
    CREATE EXTERNAL TABLE if not exists <database name>.<table name>
    (
        field1 string, 
        field2 int, 
        field3 float, 
        field4 double, 
        ...,
        fieldN string
    ) 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>' 
    STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

ユーザーが接続する必要があるフィールドとその他の構成の説明を次に示します。

- **&#60;データベース名>**: ユーザーが作成するデータベースの名前。 ユーザーが既定のデータベースを使用する場合、*create database...* クエリは省略してかまいません。
- **&#60;テーブル名>**: ユーザーが指定されたデータベース内に作成するテーブルの名前。 ユーザーが既定のデータベースを使用する場合、テーブルは *&#60;テーブル名>* で直接参照でき、&#60;データベース名> は不要です。
- **&#60;フィールド区切り記号>**: Hive テーブルにアップロードするデータ ファイル内のフィールドを区切る区切り記号。
- **&#60;行区切り記号>**: データ ファイル内の行を区切る区切り記号。
- **&#60;ストレージの場所>**: Hive テーブルのデータを保存する Azure Sorage の場所。 ユーザーが *LOCATION &#60;ストレージの場所>* を指定しなかった場合、既定では、データベースとテーブルは、Hive クラスターの既定のコンテナー内の *hive/warehouse/* ディレクトリに格納されます。 ユーザーがストレージの場所を指定する場合、ストレージの場所は、データベースとテーブルの既定のコンテナー内でなければなりません。 この場所は、クラスターの既定のコンテナーを基準として、*'wasb:///&#60;ディレクトリ 1>/'* や *'wasb:///&#60;directory 1>/&#60;ディレクトリ 2>/'* などの形式で参照する必要があります。クエリが実行されると、既定のコンテナー内に相対ディレクトリが作成されます。
- **TBLPROPERTIES("skip.header.line.count"="1")**: データ ファイルにヘッダー行が含まれる場合、ユーザーはこのプロパティを *create table* クエリの**最後に**追加する必要があります。 それ以外の場合、ヘッダー行はテーブルへのレコードとして読み込まれます。 データ ファイルにヘッダー行が含まれない場合は、クエリでこの構成を省略することができます。

## <a name="load-data"></a>Hive テーブルにデータを読み込む

Hive テーブルにデータを読み込む Hive クエリを次に示します。

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- **&#60;BLOB データのパス>**: Hive テーブルにアップロードする BLOB ファイルが HDInsight Hadoop クラスターの既定のコンテナーに存在する場合、*&#60;BLOB データのパス>* は *'wasb:///&#60;このコンテナー内のディレクトリ>/&#60;BLOB ファイル名>'* の形式にする必要があります。 BLOB ファイルは、HDInsight Hadoop クラスターの追加コンテナーに配置することもできます。 この場合、*&#60;BLOB データのパス>* は *'wasb://&#60;コンテナー名>@&#60;ストレージ アカウント名>.blob.core.windows.net/&#60;BLOB ファイル名>'* の形式にする必要があります。
    >[AZURE.NOTE] Hive テーブルにアップロードする BLOB データは、Hadoop クラスターのストレージ アカウントの既定のコンテナーまたは追加のコンテナーに配置されている必要があります。 それ以外の場合、*LOAD DATA* クエリはデータにアクセスできないために失敗します。 


## <a name="partition-orc"></a>拡張トピック: テーブルとストアの ORC 形式でハイブ データをパーティション分割

データのサイズが大きい場合、テーブルのいくつかのパーティションのみをスキャンすればよいクエリでは、テーブルをパーティション分割すると効果的です。 たとえば、Web サイトのログ データを日付別にパーティション分割することができます。

Hive テーブルをパーティション分割することに加え、Optimized Row Columnar (ORC) 形式で Hive データを格納することも効果的です。 ORC 形式の詳細については、次を参照してください。 <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">Hive が読み取り、書き込み、およびデータを処理するときに、を使用しておける ORC ファイルによってパフォーマンスが向上</a>します。

### パーティション テーブル

パーティション テーブルを作成し、そこにデータを読み込む Hive クエリを次に示します。

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name> 
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

パーティション条件を追加することが推奨パーティション分割されたテーブルのクエリを実行するときに、 **先頭** の `、` この句が大幅に検索の有効性が向上します。

    select 
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name> 
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>ORC 形式で Hive データを格納します。

ユーザーは ORC 形式で格納されているデータを直接 BLOB ストレージから Hive テーブルに読み込むことはできません。 以下に、ORC 形式で格納されているデータを Azure BLOB から Hive テーブルに読み込むためにユーザーが実行する必要がある手順を示します。

1. 外部テーブル **STORED AS TEXTFILE** を作成し、データを BLOB ストレージからテーブルに読み込みます。

     CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
     (
         field1 string,
         field2 int,
         ...
         fieldN date
     )
     ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' 
         lines terminated by '<line separator>' STORED AS TEXTFILE 
         LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");
    
     LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

2. 手順 1 の外部テーブルと同じスキーマを持つ内部テーブルを、同じフィールド区切り記号を使用して作成し、ORC 形式で Hive データを格納します。

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name> 
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        ) 
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

3. 手順 1 の外部テーブルからデータを選択し、ORC テーブルに挿入します。

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

    >[AZURE.NOTE] 場合 TEXTFILE テーブル *(& a) #60; データベース名 >. (& a) #60; 外部テキストファイル テーブル名 >* 手順 3 で、パーティションを持つ、 `選択 * < データベース名 > からです。 < 外部テキストファイル テーブル名 >` コマンドは、返されたデータ セット内のフィールドとしてパーティション変数を選択します。*&#60;データベース名>.&#60;ORC テーブル名>* にはテーブル スキーマのフィールドとしてパーティション変数が含まれないため、*&#60;データベース名>.&#60;ORC テーブル名>* に挿入すると失敗します。この場合、ユーザーは *&#60;データベース名>.&#60;ORC テーブル名>* に挿入するフィールドを次のように具体的に選択する必要があります。

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name> 
           WHERE <partition variable>=<partition value>;

4. すべてのデータが *&#60;データベース名>.&#60;ORC テーブル名>* に挿入された後に、次のクエリを使用して *&#60;外部テキストファイル テーブル名>* を削除するのが安全です。

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;


この手順が終了すれば、すぐに使用できる ORC 形式のデータを含むテーブルが手に入ったことになります。


## チューニングに関するセクションがここに入ります

最後のセクションでは、Hive クエリのパフォーマンスを向上させるためにユーザーが調整できるパラメーターについて説明します。




