<properties
    pageTitle="Cortana Analytics Process の活用: Hadoop クラスターを使用する | Microsoft Azure"
    description="HDInsight Hadoop クラスターを使用したエンド ツー エンドのシナリオで Advanced Analytics Process and Technology (ADAPT) を使用し、公開されているデータセットを使ってモデルを作成してデプロイします。"
    services="machine-learning,hdinsight"
    documentationCenter=""
    authors="bradsev"
    manager="paulettm"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2015"
    ms.author="hangzh;bradsev" />


# Cortana Analytics Process の活用: HDInsight Hadoop クラスターを使用する

エンド ツー エンドのシナリオを使用して、Cortana Analytics 処理の使用このチュートリアルでは、 [Azure HDInsight Hadoop クラスター](http://azure.microsoft.com/services/hdinsight/) 格納するには、表示および機能のパブリックに使用可能なエンジニア リング データ [NYC タクシー乗車](http://www.andresmh.com/nyctaxitrips/) データセット、データをダウン サンプリングします。 データのモデルは、Azure Machine Learning を使用して作成され、二項分類、多クラス分類、回帰予測タスクを処理します。

ようなシナリオを使用して、HDInsight Hadoop クラスターでのデータ処理の大きい (1 テラバイト) データセットを処理する方法について説明するチュートリアルは、次を参照してください。 [Cortana Analytics プロセス - 1 TB のデータセットで Azure HDInsight Hadoop クラスターを使用して](machine-learning-data-science-process-hive-criteo-walkthrough.md)します。

IPython Notebook を使用して、このチュートリアルで説明する 1 TB のデータセットを使用したタスクを実行することもできます。 この方法を試してみたいユーザーを参照してください、 [Hive ODBC の接続を使用した Criteo チュートリアル](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) トピックです。


## <a name="dataset"></a>NYC タクシー乗車データセットの説明

NYC タクシー乗車データは、約 20 GB の圧縮されたコンマ区切り値 (CSV) ファイル (非圧縮で最大 48 GB) です。1 億 7300 万以上の個々の乗車と、各乗車に支払われた料金で構成されています。 各旅行レコードには、pickup (乗車) と drop-off (降車) の場所と時間、匿名化されたタクシー運転手の (運転) 免許番号、および medallion (タクシーの一意の ID) 番号が含まれています。 データには 2013 年のすべての乗車が含まれ、データは月ごとに次の 2 つのデータセットに用意されています。

1. 'trip_data' CSV ファイルには、乗車の詳細 (乗客数、乗車地点、降車地点、乗車時間、乗車距離など) が含まれています。 いくつかのサンプル レコードを次に示します。

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. 'trip_fare' CSV ファイルには、各乗車に対して支払われた料金の詳細 (支払いの種類、料金、追加料金、税、チップ、道路などの通行料、合計支払金額など) が含まれます。 いくつかのサンプル レコードを次に示します。

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

trip\_data と trip\_fare を結合するための一意のキーは、[medallion]、[hack\_licence] および [pickup\_datetime} の各フィールドで構成されています。

特定の乗車に関連する詳細をすべて取得、3 つのキーに参加するだけで十分です。"medallion"、"hack \_license"および"pickup \_datetime"です。

データの詳細については、この後で Hive テーブルにデータを格納するときに説明します。

## <a name="mltasks"></a>予測タスクの例
データ処理に取り掛かる際、分析に基づいて一種の予測を行うことを目標にすると、プロセスに含める必要があるタスクを明確にするのに役立ちます。
3 つの例の公式のに基づいて、このチュートリアルで扱う予測問題の次のとおり、 *\_amount*:

1. **二項分類**: チップがつまり乗車に対して支払われたかどうかを予測する *\_amount* 大きく $0 は、肯定的な例より、 *\_amount* $0 の場合は負の値の例です。

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0

2. **多クラス分類**: 乗車に対して支払われたチップの範囲を予測します。 分割、 *\_amount* を 5 つの箱つまりクラスにします。

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20

3. **回帰タスク**: 乗車で支払われたチップの金額を予測します。  


## <a name="setup"></a>高度な分析に HDInsight Hadoop クラスターの設定します。

>[AZURE.NOTE] これは、通常、 **Admin** タスクです。

HDInsight クラスターを使用する高度な分析用の Azure 環境は、次の 3 つの手順でセットアップできます。

1. [ストレージ アカウントの作成](../storage-whatis-account.md): このストレージ アカウントは Azure Blob ストレージにデータを格納するために使用します。 ここには、HDInsight クラスターで使用するデータも格納されます。

2. [Advanced Analytics Process and Technology 用の Azure HDInsight Hadoop クラスターをカスタマイズする](machine-learning-data-science-customize-hadoop-cluster.md)です。 この手順では、全ノードに 64 ビットの Anaconda Python 2.7 がインストールされた Azure HDInsight Hadoop クラスターを作成します。 HDInsight クラスターをカスタマイズする際、注意する必要のある 2 つの重要な手順があります。

    * 作成時に、手順 1. で作成したストレージ アカウントを HDInsight クラスターにリンクする必要があります。 このストレージ アカウントは、クラスター内で処理されるデータにアクセスするために使用されます。

    * クラスターを作成したら、クラスターのヘッド ノードへのリモート アクセスを有効にします。 移動し、 **構成** ] タブでをクリックし、 **リモートの有効化**します。 この手順で、リモート ログインに使用するユーザーの資格情報を指定します。

3. [Azure Machine Learning ワークスペースを作成する](machine-learning-create-workspace.md): この Azure Machine Learning ワークスペースは machine learning のモデルの作成に使用します。 このタスクは、HDInsight クラスターを使用した初期データの探索とダウンサンプリングの完了後に対処されます。

## <a name="getdata"></a>公開されているソースからデータを取得する

>[AZURE.NOTE] これは、通常、 **Admin** タスクです。

取得する、 [NYC タクシー乗車](http://www.andresmh.com/nyctaxitrips/) 公開されている場所からのデータセットを使用することがで説明したメソッドの [と Azure Blob ストレージの間でデータ移動](machine-learning-data-science-move-azure-blob.md) マシンにデータをコピーします。

ここでは、AzCopy を使用してデータを含むファイルを転送する方法について説明します。 AzCopy をダウンロードしてインストールするには、以下の」の手順に [AzCopy コマンド ライン ユーティリティの概要](../storage-use-azcopy.md)します。

1. コマンド プロンプト ウィンドウを置き換えて、次の AzCopy コマンドを発行 *< path_to_data_folder >* を目的の転送先。


        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

2. コピーが完了すると、合計 24 個の Zip ファイルが選択したデータのフォルダー内に見つかります。 ダウンロードされたファイルをローカル コンピューター上の同じディレクトリに解凍します。 圧縮されていないファイルが存在するフォルダーをメモしておきます。 このフォルダーとして参照される、 *< path\_to\_unzipped_data\_files\ >* です。


## <a name="upload"></a>データを Azure HDInsight Hadoop クラスターの既定のコンテナーにアップロードする

>[AZURE.NOTE] これは、通常、 **Admin** タスクです。

次の AzCopy コマンドでは、Hadoop クラスターを作成してデータ ファイルを解凍したときに指定した実際の値で次のパラメーターを置き換えます。

* ***& #60; path_to_data_folder >*** 上のディレクトリ (パス) を解凍データ ファイルを含むコンピューター  
* ***& #60; Hadoop クラスターのストレージ アカウント名 >*** 、HDInsight クラスターに関連付けられているストレージ アカウント
* ***& #60; Hadoop クラスターの既定のコンテナー >*** 、クラスターで使用する既定のコンテナーです。 通常、既定のコンテナーの名前は、クラスター自体と同じ名前です。 たとえば、"abc123.azurehdinsight.net" というクラスターの場合、既定のコンテナーは abc123 です。
* ***& #60; ストレージ アカウント キー >*** 、クラスターで使用されるストレージ アカウント キー

コンピューターのコマンド プロンプトまたは Windows PowerShell ウィンドウで、次の 2 つの AzCopy コマンドを実行します。

このコマンドに乗車データをアップロード ***nyctaxitripraw*** ディレクトリに、Hadoop クラスターの既定のコンテナーです。

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

このコマンドに料金データをアップロード ***nyctaxifareraw*** ディレクトリに、Hadoop クラスターの既定のコンテナーです。

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

これで、データが Azure BLOB ストレージに格納され、HDInsight クラスター内で利用できるようになりました。

## <a name="#download-hql-files"></a>Hadoop クラスターのヘッド ノードにログインし、探索的データ分析の準備

>[AZURE.NOTE] これは、通常、 **Admin** タスクです。

探索的データ分析とダウン サンプリング データのクラスターのヘッド ノードにアクセスするに記載されている手順に従います [Hadoop クラスターのヘッド ノードにアクセス](machine-learning-data-science-customize-hadoop-cluster.md#headnode)します。

このチュートリアルでは、主に使用で記述されたクエリ [Hive](https://hive.apache.org/), 、事前のデータ探索を実行する、SQL に似たクエリ言語です。 Hive クエリは .hql ファイルに保存されています。 その後、モデル作成のために、Azure Machine Learning で使用されるこのデータをダウンサンプリングします。

関連する Hive スクリプトを含む .hql ファイルをダウンロードしてクラスターを準備する、探索的データ分析のため、 [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) ヘッド ノード上のローカル ディレクトリ (C:\temp) にします。 これを行うには、開く、 **コマンド プロンプト** から、クラスターのヘッド ノード内で次の 2 つのコマンドを発行します。

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

これら 2 つのコマンドはローカルのディレクトリには、このチュートリアルで必要なすべての .hql ファイルをダウンロード ***C:\temp & #92;*** ヘッド ノードにします。

## <a name="#hive-db-tables"></a>Hive データベースと月ごとにパーティション分割されたテーブルを作成する

>[AZURE.NOTE] これは、通常、 **Admin** タスクです。

これで、NYC タクシー データセットの Hive テーブルを作成する準備ができました。
Hadoop クラスターのヘッド ノードを開き、 ***Hadoop コマンド ライン*** ヘッド ノードのデスクトップでのコマンドを入力して Hive ディレクトリを入力し、

    cd %hive_home%\bin

>[AZURE.NOTE] **このチュートリアルでは、上記の Hive bin からすべての Hive コマンドを実行/ディレクトリ プロンプトです。これは自動的にすべてのパスの問題に対処します。使用用語「Hive ディレクトリ プロンプト」、"Hive bin/ディレクトリ プロンプト"、「Hadoop コマンドライン」このチュートリアルでは同じ意味で。**

Hive ディレクトリ プロンプトから、ヘッド ノードの Hadoop コマンド ラインで次のコマンドを入力し、Hive クエリを送信して Hive データベースとテーブルを作成します。

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

コンテンツをここでは、 ***C:\temp\sample\_hive\_create\_db\_and\_tables.hql*** Hive データベースを作成するファイル ***nyctaxidb*** とテーブル ***トリップ*** と ***fare***します。

    create database if not exists nyctaxidb;

    create external table if not exists nyctaxidb.trip
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double)  
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

    create external table if not exists nyctaxidb.fare
    (
        medallion string,
        hack_license string,
        vendor_id string,
        pickup_datetime string,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double)
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

この Hive スクリプトでは 2 つのテーブルが作成されます。

* テーブル "trip" には、乗車ごとの詳細 (運転手の詳細、乗車時間、乗車距離、乗車回数) が含まれます。
* テーブル "fare" には、料金の詳細 (料金、チップ、道路などの通行料、追加料金) が含まれます。

これらのプロシージャについてさらに支援を必要するか、または別のものを調査する場合は、セクションを参照してください。 [Hive クエリを送信 Hadoop コマンド ラインから直接 ](machine-learning-data-science-process-hive-tables.md#submit)します。

## <a name="#load-data"></a>パーティションごとのハイブ テーブルにデータを読み込む

>[AZURE.NOTE] これは、通常、 **Admin** タスクです。

NYC タクシー データセットは、月ごとに自然にパーティション分割されているので、これを使用することで処理を高速化し、クエリ時間を短縮できます。 次の PowerShell コマンド (、Hive を使用してディレクトリから発行された、 **Hadoop コマンド ライン**) 月別にパーティション分割"trip"および"fare"Hive テーブルにデータを読み込みます。

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

 *Sample\_hive\_load\_data\_by\_partitions.hql* ファイルには、次が含まれています。 **ロード** コマンドです。

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

この探索プロセスで使用する多くの Hive クエリは、1 つのパーティションのみまたはいくつかのパーティションのみを対象としていますが、 これらのクエリをデータ全体に対して実行することもできます。

### <a name="#show-db"></a>HDInsight Hadoop クラスターでのデータベースを表示します。

Hadoop コマンド ライン ウィンドウ内に HDInsight Hadoop クラスターで作成されたデータベースを表示するには、Hadoop コマンド ラインで次のコマンドを実行します。

    hive -e "show databases;"

### <a name="#show-tables"></a>Nyctaxidb データベースの Hive テーブルを表示します。

nyctaxidb データベースのテーブルを表示するには、Hadoop コマンドラインで次のコマンドを実行します。

    hive -e "show tables in nyctaxidb;"

次のコマンドを発行して、テーブルがパーティション分割されていることを確認できます。

    hive -e "show partitions nyctaxidb.trip;"

予想される出力を次に示します。

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 2.075 seconds, Fetched: 12 row(s)

同様に、次のコマンドを発行して、fare テーブルがパーティション分割されていることを確認できます。

    hive -e "show partitions nyctaxidb.fare;"

予想される出力を次に示します。

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 1.887 seconds, Fetched: 12 row(s)

## <a name="#explore-hive"></a>Hive におけるデータの探索と特徴エンジニアリング

>[AZURE.NOTE] これは、通常、 **データ サイエンティスト** タスクです。

Hive テーブルに読み込まれるデータのデータの探索タスクおよび特徴エンジニアリング タスクは、Hive クエリを使用して実現します。 このセクションのチュートリアルで行うこのようなタスクの例を次に示します。

- 両方のテーブルの、上位 10 個のレコードを表示する。
- さまざまな期間で、いくつかのフィールドのデータの分布を探索する。
- [経度] フィールドと [緯度] フィールドのデータの品質を調査する。
- 基づくバイナリおよび多クラス分類ラベルを生成する、 **\_amount**します。
- 直線乗車距離を計算して、特徴を生成する。

### 探索: trip テーブルの上位 10 個のレコードを表示する

>[AZURE.NOTE] これは、通常、 **データ サイエンティスト** タスクです。

データがどのように表示されるかを確認するために、各テーブルから 10 個のレコードを確認します。 レコードを検査するには、Hadoop コマンド ライン コンソールの Hive ディレクトリ プロンプトから次の 2 つのクエリを実行します。

最初の月から "trip" テーブルの上位 10 個のレコードを取得するには、次のクエリを実行します。

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

最初の月から "fare" テーブルの上位 10 個のレコードを取得するには、次のクエリを実行します。

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

レコードをファイルに保存すると、レコードを簡単に表示できるので便利です。 これを実現するには、上記のクエリを少し変更します。

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### 探索: 12 個のそれぞれのパーティションのレコードの数を表示する

>[AZURE.NOTE] これは、通常、 **データ サイエンティスト** タスクです。

関心事は、年間で乗車数がどのように変化するかです。 月ごとにグループ化することで、乗車のこの分布がどのように表示されるかを見ることができます。

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

これにより、次のような結果が表示されます。

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 283.406 seconds, Fetched: 12 row(s)

ここでは、最初の列は月で、2 番目の列はその月の乗車数を表しています。

Hive ディレクトリ プロンプトで次のコマンドを発行して、乗車データ セットの合計レコード数をカウントすることもできます。

    hive -e "select count(*) from nyctaxidb.trip;"

次が生成されます。

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

乗車データ セットの場合と同様のコマンドを使用して、Hive ディレクトリ プロンプトから Hive クエリを発行し、料金データ セットのレコード数を検証できます。

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

これにより、次のような結果が表示されます。

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 253.955 seconds, Fetched: 12 row(s)

2 つのデータ セットでまったく同じ月ごとの乗車数が返されています。 これで、データが正しく読み込まれていることが検証されました。

Hive ディレクトリ プロンプトから次のコマンドを使用して、料金データ セット内の合計レコード数をカウントできます。

    hive -e "select count(*) from nyctaxidb.fare;"

次の結果が生成されます。

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

2 つのテーブルの合計レコード数も同じです。 これで、データが正しく読み込まれていることが再度検証されました。

### 探索: medallion (タクシー番号) ごとの乗車回数の分布

>[AZURE.NOTE] これは、通常、 **データ サイエンティスト** タスクです。

この例では、指定した期間内で乗車回数が 100 を超える medallion (タクシー番号) を識別します。 パーティション変数が条件が設定されるためクエリがパーティション分割されたテーブルへのアクセスからメリット **月**します。 クエリ結果は、ヘッド ノード上の `C:\temp` にあるローカル ファイル queryoutput.tsv に書き込まれます。

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

内容を次に示します *sample\_hive\_trip\_count\_by\_medallion.hql* 検査用のファイルです。

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

NYC タクシーのデータ セット内にある medallion (タクシー番号) は、一意のタクシーを識別します。 特定の期間に特定の乗車数を超えるタクシーを確認することで、"忙しい" タクシーを特定できます。 次の例では、最初の 3 か月間で乗車回数が 100 を超えるタクシーを識別し、クエリ結果を C:\temp\queryoutput.tsv ローカル ファイルに保存します。

内容を次に示します *sample\_hive\_trip\_count\_by\_medallion.hql* 検査用のファイルです。

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Hive ディレクトリ プロンプトで次のコマンドを発行します。

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### 探索: medallion および hack_license ごとの乗車回数の分布

>[AZURE.NOTE] これは、通常、 **データ サイエンティスト** タスクです。

データセットの探索時に、値のグループの出現回数を確認する必要があることがよくあります。 このセクションでは、タクシーと運転手に対してこれを行う方法の一例を紹介します。

 *Sample\_hive\_trip\_count\_by\_medallion\_license.hql* ファイルは、"medallion"と"hack_license"で、料金データ セットをグループ化し、それぞれの組み合わせの数を返します。 その内容を次に示します。

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

このクエリは、タクシーと特定の運転手の組み合わせを乗車回数の多い順に返します。

Hive ディレクトリ プロンプトで次のコマンドを実行します。

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

クエリ結果は、ローカル ファイル C:\temp\queryoutput.tsv に書き込まれます。

### 探索: 無効な経度と緯度のレコードをチェックしてデータの品質を評価する

>[AZURE.NOTE] これは、通常、 **データ サイエンティスト** タスクです。

探索的データ分析の共通の目標は、無効または正しくないレコードを除外することです。 このセクションの例では、緯度または経度のいずれかのフィールドに NYC 領域から大きく外れる値が含まれていないかどうかを判断します。 そのようなレコードには緯度と経度の値にエラーがある可能性が高いため、モデリングに使用するすべてのデータからそれらを排除します。

内容を次に示します *sample\_hive\_quality\_assessment.hql* 検査用のファイルです。

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


Hive ディレクトリ プロンプトで次のコマンドを実行します。

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

 *-S* このコマンドに含まれている引数は、Hive の Map/reduce ジョブのステータス画面の印刷結果を表示しないようにします。 このコマンドは、Hive クエリの画面出力を読みやすくするので役立ちます。

### 探索: チップの二項分類の分布

**注:** これは、通常、 **データ サイエンティスト** タスクです。

説明されている二項分類の問題、 [予測タスクの例](machine-learning-data-science-process-hive-walkthrough.md#mltasks) ] セクションがあるかどうかチップが支払われたかを知っておくと便利です。 このチップの分布は、次の二項です。

* 指定されたヒント (クラス 1、\_amount > $0)  
* ない tip (Class 0, \_amount = $0)。

 *Sample\_hive\_tipped\_frequencies.hql* は、次のファイルです。

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

Hive ディレクトリ プロンプトで次のコマンドを実行します。

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### 探索: 多クラス設定での分類分布

**注:** これは、通常、 **データ サイエンティスト** タスクです。

説明されている多クラス分類問題、 [予測タスクの例](machine-learning-data-science-process-hive-walkthrough.md#mltasks) セクションでこのデータ セットにも対処支払われるチップの金額を予測する今回は、自然な分類します。 箱を使って、クエリのチップの範囲を定義できます。 さまざまなチップの範囲のクラス分布を取得するには使用、 *sample\_hive\_tip\_range\_frequencies.hql* ファイルです。 その内容を次に示します。

    SELECT tip_class, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount=0, 0,
            if(tip_amount>0 and tip_amount<=5, 1,
            if(tip_amount>5 and tip_amount<=10, 2,
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

Hadoop コマンド ライン コンソールから、次のコマンドを実行します。

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### 探索: 経度緯度の 2 つの場所の直線距離を計算する

**注:** これは、通常、 **データ サイエンティスト** タスクです。

直線距離を測定することで、その距離と実際の乗車距離の違いを確認できます。 運転手が意図的に長いルートを走行したことに乗客が気づいた場合に、チップが支払われる可能性が低くなることを指摘するために、この機能が開発されました。

実際の乗車距離の間で比較して、 [Haversine distance](http://en.wikipedia.org/wiki/Haversine_formula) 2 つの地点経度緯度 (「丸い」距離) を使用して利用可能な三角関数、Hive 内でため。

    set R=3959;
    set pi=radians(180);

    insert overwrite directory 'wasb:///queryoutputdir'

    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
     *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
     *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
     /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
     +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
     pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
    from nyctaxidb.trip
    where month=1
    and pickup_longitude between -90 and -30
    and pickup_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
    and dropoff_latitude between 30 and 90;

上記のクエリで R は地球半径をマイルで表したもので、pi はラジアンに変換されます。 緯度経度の 2 つの地点は NYC 領域から大きく外れた値を除外するために "フィルター" されます。

この場合、"queryoutputdir" というディレクトリに結果が書き込まれます。 次に示す一連のコマンドでは、まずこの出力ディレクトリを作成してから、Hive コマンドを実行します。

Hive ディレクトリ プロンプトで次のコマンドを実行します。

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


クエリの結果が 9 つの Azure blob に書き込まれる ***queryoutputdir/000000\_0*** に  ***queryoutputdir/000008\_0*** 、Hadoop クラスターの既定のコンテナーにあります。

個々の BLOB のサイズを表示するには、Hive ディレクトリ プロンプトで次のコマンドを実行します。

    hdfs dfs -ls wasb:///queryoutputdir

指定されたファイルの内容を参照してください、000000\_0 と答えると、Hadoop の使用して `copyToLocal` コマンドを使います。

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

**警告:** `copyToLocal` サイズの大きいファイルは、非常に遅くなることができ、それらで使用は推奨されません。  

このデータがあること、Azure blob 内の主な利点は、[リーダー] [リーダー] モジュールを使用して Azure Machine Learning 内でデータを探索できることです。


## <a name="#downsample"></a>Azure Machine Learning でデータとビルド モデルのサンプルを

**注:** これは、通常、 **データ サイエンティスト** タスクです。

探索的データ分析フェーズが終了したら、Azure Machine Learning.でデータをダウンサンプリングし、モデルを作成できます。 このセクションでは、Hive クエリを使用して、ダウン サンプリング、データは、後は、Azure Machine Learning で [リーダー] [リーダー] モジュールからアクセスする方法を説明します。

### データをダウンサンプリングする

これには、2 つの手順があります。 結合先、 **nyctaxidb.trip** と **nyctaxidb.fare** 上のすべてのレコードに存在する 3 つのキーのテーブル:"medallion"、"hack \_license"および"pickup \_datetime"です。 二項分類ラベルを生成します **tipped** と多クラス分類ラベル **[tip \_class]**します。

[リーダー] [リーダー] モジュールから直接ダウン サンプリングされたデータを Azure Machine Learning で使用できるようにするには、内部の Hive テーブルには、上記のクエリの結果を格納する必要があります。 以下では、内部の Hive テーブルを作成し、結合されダウンサンプリングされたデータでその内容を設定します。

クエリには、日、週、曜日 (1 は月曜日、7 は日曜日の略)、"pickup \_datetime"フィールドと、ピックアップとドロップオフの場所間の直線距離からの 1 時間を生成するには、直接の標準の Hive 関数が適用されます。 ユーザーが参照できます [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) このような関数の完全な一覧についてです。

さらに、クエリはデータをダウンサンプリングして、クエリの結果が Azure Machine Learning Studio に合うようにします。 元のデータセットの約 1% だけが Studio にインポートされます。

内容を次に示します *sample\_hive\_prepare\_for\_aml\_full.hql* ファイルを Azure Machine Learning でモデル作成用データを準備します。

        set R = 3959;
        set pi=radians(180);

        create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
        )
        row format delimited fields terminated by ','
        lines terminated by '\n'
        stored as textfile;

        --- now insert contents of the join into the above internal table

        insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
        select
        t.medallion,
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        t.direct_distance,
        f.payment_type,
        f.fare_amount,
        f.surcharge,
        f.mta_tax,
        f.tip_amount,
        f.tolls_amount,
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

        from
        (
        select
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
        ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
        rand() as sample_key

        from nyctaxidb.trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select
        medallion,
        hack_license,
        vendor_id,
        pickup_datetime,
        payment_type,
        fare_amount,
        surcharge,
        mta_tax,
        tip_amount,
        tolls_amount,
        total_amount
        from nyctaxidb.fare
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01

このクエリは、Hive ディレクトリ プロンプトで次のように実行します。

    hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

ここである内部テーブル"nyctaxidb.nyctaxi_downsampled_dataset"が、Azure Machine Learning から [リーダー] [リーダー] モジュールを使用してアクセスできます。 さらに、このデータセットを使って Machine Learning モデルを作成できます。  

### Azure Machine Learning のリーダー モジュールを使ってダウンサンプリングされたデータにアクセスする

Azure Machine Learning の [リーダー] [リーダー] モジュールで Hive クエリを発行するための前提条件として Azure Machine Learning ワークスペースへのアクセスと、クラスターとその関連付けられたストレージ アカウントの資格情報へのアクセスが必要です。

[リーダー] [リーダー] モジュールと入力するパラメーターの詳細がいくつかについて

**HCatalog サーバー URI**: かどうかは、クラスター名が abc123 の場合、これは単に、: https://abc123.azurehdinsight.net

**Hadoop ユーザー アカウント名** : クラスターに選択したユーザー名 (**いない** リモート アクセスのユーザー名)

**Hadoop ユーザー アカウントのパスワード** : クラスターに選択したパスワード (**いない** リモート アクセスのパスワード)

**出力データの場所** : Azure になるよう選択されます。

**Azure ストレージ アカウント名** : クラスターに関連付けられている既定のストレージ アカウントの名前。

**Azure コンテナー名** : クラスターの場合、既定のコンテナー名で、通常、クラスター名と同じです。 "abc123" というクラスターの場合、これは abc123 になります。

**重要な注意事項:** **ので [リーダー] [リーダー] モジュールを使用して、Azure Machine Learning で照会する任意のテーブルは内部テーブルである必要があります。**D.db データベース内のテーブル T が内部テーブルかどうかを判断するヒントを次に示します。

Hive ディレクトリ プロンプトで次のコマンドを発行します。

    hdfs dfs -ls wasb:///D.db/T

テーブルが内部テーブルであり、テーブルにデータが設定されている場合は、その内容がここで表示される必要があります。 テーブルが内部テーブルかどうかを判断する別の方法は、Azure ストレージ エクスプローラーを使用することです。 Azure ストレージ エクスプローラーを使用してクラスターの既定のコンテナー名に移動し、テーブル名でフィルターします。 テーブルとその内容が表示されれば、内部テーブルであることがわかります。

Hive クエリと [リーダー] [リーダー] モジュールのスナップショットを次に示します。

![](./media/machine-learning-data-science-process-hive-walkthrough/1eTYf52.png)

以降、ダウン サンプリングされたデータは、既定のコンテナーが存在する、Azure Machine Learning から作成した Hive クエリは非常に単純で、だけが、"選択 * nyctaxidb.nyctaxi\_downsampled\_data から"です。

このデータセットを Machine Learning モデル作成のための出発点として使用します。

### <a name="mlmodel"></a>Azure Machine Learning でモデルをビルドする

これでモデルの構築とでモデルの配置に進むことができました [Azure Machine Learning](https://studio.azureml.net)します。 データも、以前に特定した予測問題への対応に使用できる状態になりました。

**1.二項分類**: 乗車に対してチップが支払われたかどうかを予測します。

**使用する学習者:** 2 クラス ロジスティック回帰

a. この問題では、ターゲット (またはクラス) ラベルは "tipped" です 元のダウンサンプリングされたデータセットには、この分類実験用のターゲット リークであるいくつかの列があります。 具体的には: [tip \_class]、\_amount、および total\_amount テスト時では利用できないターゲット ラベルについての情報を表示します。 [プロジェクト列] の [プロジェクト列] モジュールを使用して考慮の対象からこれらの列を削除します。

次のスナップショットは、特定の乗車でチップが支払われたかどうかを予測するための実験を示しています。

![](./media/machine-learning-data-science-process-hive-walkthrough/QGxRz5A.png)

b. この実験では、ターゲット ラベルの分布がほぼ 1:1 です。

次のスナップショットは、二項分類の問題のための、チップのクラス ラベルの分布を示しています。

![](./media/machine-learning-data-science-process-hive-walkthrough/9mM4jlD.png)

その結果、次の図に示す 0.987 の AUC を取得します。

![](./media/machine-learning-data-science-process-hive-walkthrough/8JDT0F8.png)

**2.多クラス分類**: 以前に定義したクラスを使用して、乗車で支払われたチップの金額の範囲を予測します。

**使用する学習者:** 多クラス ロジスティック回帰

a. この問題をターゲット (またはクラス) ラベルは「[tip \_class]」を 5 つの値 (0,1,2,3,4) のいずれかを実行します。 二項分類の場合と同様に、この実験用のターゲット リークであるいくつかの列があります。 具体的には: total\_amount tipped、\_amount、テスト時では利用できないターゲット ラベルについての情報を表示します。 [プロジェクト列] の [プロジェクト列] モジュールを使用してこれらの列を削除します。

次のスナップショットは、チップが少なくなる可能性の高い箱 ( クラス 0: チップ = $0、クラス 1 : チップ > $0 とチップ <= $5、クラス 2 : チップ > $5 とチップ <= $10、クラス 3 : チップ > $10 とチップ <= $20、クラス 4 : チップ > $20) を予測する実験を示しています。

![](./media/machine-learning-data-science-process-hive-walkthrough/5ztv0n0.png)

実際のテスト クラスの分布がどのようになるかを次に示します。 クラス 0 とクラス 1 は一般的ですが、他のクラスはまれであることがわかります。

![](./media/machine-learning-data-science-process-hive-walkthrough/Vy1FUKa.png)

b. この実験では、混同行列を使って、予測精度を確認します。 一般的なイメージを以下に示します。

![](./media/machine-learning-data-science-process-hive-walkthrough/cxFmErM.png)

一般的なクラスのクラス精度がかなり良い一方で、そのモデルはまれなクラスでは "学習" がうまくいっていないことに注意してください。


**3.回帰タスク**: 乗車で支払われたチップの金額を予測します。

**使用する学習者:** ブースト デシジョン ツリー

a. この問題をターゲット (またはクラス) ラベルは"\_amount"です。 ターゲット リークは、ここでは: tipped、total\_amount; [tip \_class]これらすべての変数では、テスト時に通常使用されるチップの金額についての情報が表示されます。 [プロジェクト列] の [プロジェクト列] モジュールを使用してこれらの列を削除します。

次のスナップショットは、支払われるチップの金額を予測する実験を示しています。

![](./media/machine-learning-data-science-process-hive-walkthrough/11TZWgV.png)

b. 回帰の問題については、予測や決定係数での二乗誤差を見ることで予測の精度を測定します。 これらについて以下に示します。

![](./media/machine-learning-data-science-process-hive-walkthrough/Jat9mrz.png)

決定係数が 0.709 であり、これは約 71% という分散がモデル係数で説明されていることを意味しています。

**重要な注意事項:** Azure Machine Learning とそれを使用してアクセスする方法に関する詳細についてを参照してください [Machine Learning は何ですか?](machine-learning-what-is-machine-learning.md)します。 一連の Azure Machine Learning を Machine Learning の実験を再生するための非常に便利なリソースは、 [Cortana Analytics ギャラリー](https://gallery.azureml.net/)します。 ギャラリーには、すべての実験についての説明があり、Azure Machine Learning の機能範囲が詳しく説明されています。

## ライセンス情報

このサンプルのチュートリアルとそれに付随するスクリプトは、MIT ライセンスの下で Microsoft と共有されています。 詳細については、GitHub のサンプル コードのディレクトリにある LICENSE.txt ファイルを確認してください。

## 参照

•   [Andrés Monroy NYC タクシー乗車ダウンロード ページ](http://www.andresmh.com/nyctaxitrips/)  
•   [NYC のでは、乗車データをタキシング Chris する (whong) によって](http://chriswhong.com/open-data/foil_nyc_taxi/)   
•   [ニューヨーク市タクシーおよびリムジン コミッション調査および統計](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[2]: ./media/machine-learning-data-science-process-hive-walkthrough/output-hive-results-3.png
[11]: ./media/machine-learning-data-science-process-hive-walkthrough/hive-reader-properties.png
[12]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-training.png
[13]: ./media/machine-learning-data-science-process-hive-walkthrough/create-scoring-experiment.png
[14]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-scoring.png
[15]: ./media/machine-learning-data-science-process-hive-walkthrough/amlreader.png

<!-- Module References -->
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

