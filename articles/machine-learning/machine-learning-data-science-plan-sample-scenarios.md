<properties
    pageTitle="Azure Machine Learning の Advanced Analytics Process and Technology 用のシナリオ | Microsoft Azure"
    description="Azure Machine Learning で高度な予測分析プロセスを行うための適切なシナリオを選択します。"
    services="machine-learning"
    documentationCenter=""
    authors="msolhab"
    manager="paulettm"
    editor="" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na" 
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2015"
    ms.author="msolhab;bradsev" />


# Azure Machine Learning での高度な分析のシナリオ

この記事では、Cortana Analytics Process (CAP) で処理できる多様なデータ ソースとターゲット シナリオの例について概要を説明します。 データの特性、ソースの場所、および Azure でのターゲット リポジトリによって決まる、シーケンス処理で使用できるオプションを示します。

 **デシジョン ツリー** の最後のセクションに表示されますが、データと目標に適したサンプル シナリオを選択するとします。

>[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


以下の各セクションに、サンプル シナリオを示します。 シナリオごとに、考えられるデータ サイエンスまたは高度な分析のフローと、サポートする Azure リソースの説明があります。

>[AZURE.NOTE] **、次のシナリオのすべてでは、する必要があります。**

*   [ストレージ アカウントの作成](storage-whatis-account.md)
*   [Azure ML ワークスペースを作成する](machine-learning/machine-learning-create-workspace.md)


## <a name="smalllocal"></a>シナリオ \#1: 小規模から中規模の表形式データセットをローカル ファイル

![小規模から中規模のローカル ファイル][1]

#### その他の Azure リソース: なし

1.  サインイン、 [Azure Machine Learning Studio](https://studio.azureml.net/)します。

2.  データセットをアップロードします。

3.  アップロードされたデータセットで始まる Azure Machine Learning の実験フローを構築します。

## <a name="smalllocalprocess"></a>シナリオ \#2: 小規模から中規模のデータセットの処理が必要なローカル ファイルの

![処理中の小規模から中規模のローカル ファイル][2]

#### その他の Azure リソース: Azure 仮想マシン (IPython Notebook サーバー)

1.  IPython Notebook を実行する Azure 仮想マシンを作成します。

2.  Azure ストレージ コンテナーにデータをアップロードします。

3.  Azure からデータにアクセスする IPython Notebook でデータを前処理とクリーニング
    ストレージ コンテナーです。

4.  クリーニングされたデータを表形式に変換します。

5.  変換されたデータを Azure BLOB に保存します。

6.  サインイン、 [Azure Machine Learning Studio](https://studio.azureml.net/)します。

7.  [リーダー] [リーダー] モジュールを使用して Azure blob からデータを読み取ります。

8. 統合されたデータセットで始まる Azure Machine Learning の実験フローを構築します。

## <a name="largelocal"></a>シナリオ \#3: Azure の Blob を対象とするローカルのファイルの大規模データセット

![大規模なローカル ファイル][3]

#### その他の Azure リソース: Azure 仮想マシン (IPython Notebook サーバー)

1.  IPython Notebook を実行する Azure 仮想マシンを作成します。

2.  Azure ストレージ コンテナーにデータをアップロードします。

3.  IPython Notebook でデータの前処理とクリーニングを行い、Azure BLOB からデータにアクセスします。

4.  必要に応じて、クリーニングされたデータを表形式に変換します。

5.  データを探索し、必要に応じて機能を作成します。

6.  小規模から中規模のデータ サンプルを抽出します。

7.  サンプリングされたデータを Azure BLOB に保存します。

8. サインイン、 [Azure Machine Learning Studio](https://studio.azureml.net/)します。

9. [リーダー] [リーダー] モジュールを使用して Azure blob からデータを読み取ります。

10. 統合されたデータセットで始まる Azure ML の実験フローを構築します。


## <a name="smalllocaltodb"></a>シナリオ \#4: Azure Virtal マシンで SQL Server を対象とするローカルのファイルの中規模のデータセットに小さな

![Azure の SQL DB への小規模から中規模のローカル ファイル][4]

#### その他の Azure リソース: Azure 仮想マシン (SQL Server / IPython Notebook サーバー)

1.  SQL Server と IPython Notebook を実行する Azure 仮想マシンを作成します。

2.  Azure ストレージ コンテナーにデータをアップロードします。

3.  IPython Notebook を使用して、Azure ストレージ コンテナーでデータの前処理とクリーニングを行います。

4.  必要に応じて、クリーニングされたデータを表形式に変換します。

5.  データをローカルの VM ファイルに保存します (IPython Notebook は VM で実行し、ローカル ドライブは VM ドライブを参照します)。

6.  Azure VM で実行している SQL Server データベースにデータを読み込みます。

    a.  \#1 のオプション: SQL Server Management Studio を使用します。

        i.  Login to SQL Server VM
        ii. Run SQL Server Management Studio.
        iii. Create database and target tables.
        iv. Use one of the bulk import methods to load the data from VM-local files.

    b.  \#2 のオプション: ないのでは [中]、サイズも大きく: IPython Notebook を使用します。
        datasets

        i.  Use ODBC connection string to access SQL Server on VM.
        ii. Create database and target tables.
        iii. Use one of the bulk import methods to load the data from VM-local files.

7.  データを探索し、必要に応じて機能を作成します。 機能をデータベース テーブルで具体化する必要はありません。 作成に必要なクエリに注意してください。

8. 必要に応じて、データのサンプル サイズを決定します。

9. サインイン、 [Azure Machine Learning Studio](https://studio.azureml.net/)します。

10. [リーダー] [リーダー] モジュールを使用して SQL Server から直接データを読み取ります。 フィールドの抽出、機能を作成し、[リーダー] [リーダー] クエリに直接必要な場合は、データをサンプリングするために必要なクエリを貼り付けます。

11. 統合されたデータセットで始まる Azure ML の実験フローを構築します。

## <a name="largelocaltodb"></a>シナリオ \#5: ローカル ファイルの大規模データセットは、Azure VM 内の SQL Server を対象

![Azure の SQL DB への大規模なローカル ファイル][5]

#### その他の Azure リソース: Azure 仮想マシン (SQL Server / IPython Notebook サーバー)

1.  SQL Server と IPython Notebook サーバーを実行する Azure 仮想マシンを作成します。

2.  Azure ストレージ コンテナーにデータをアップロードします。

3.  (省略可能) データの前処理とクリーニングを行います。

    a.  Azure からデータにアクセスする IPython Notebook でデータを前処理とクリーニング
        blob です。

    b.  必要に応じて、クリーニングされたデータを表形式に変換します。

    c.  データをローカルの VM ファイルに保存します (IPython Notebook は VM で実行し、ローカル ドライブは VM ドライブを参照します)。

4.  Azure VM で実行している SQL Server データベースにデータを読み込みます。

    a.  SQL Server VM にログインします。

    b.  データが保存されていない、データ ファイルを Azure からダウンロードします。
        ローカルの VM フォルダーにストレージ コンテナーです。

    c.  SQL Server Management Studio を実行します。

    d.  データベースとターゲット テーブルを作成します。

    e.  いずれかの一括インポート方法を使用してデータを読み込みます。

    f.  テーブルの結合が必要な場合は、インデックスを作成して処理時間を短縮します。

 > [AZURE.NOTE] 大きなサイズのデータの読み込みを高速、お勧めをパーティション分割されたテーブルを作成し、一括を並行してデータをインポートします。 詳細については、次を参照してください。 [SQL パーティション テーブルにデータの並列インポート](machine-learning/machine-learning-data-science-parallel-load-sql-partitioned-tables.md)します。

5.  データを探索し、必要に応じて機能を作成します。 機能をデータベース テーブルで具体化する必要はありません。 作成に必要なクエリに注意してください。

6.  必要に応じて、データのサンプル サイズを決定します。

7.  サインイン、 [Azure Machine Learning Studio](https://studio.azureml.net/)します。

8. [リーダー] [リーダー] モジュールを使用して SQL Server から直接データを読み取ります。 フィールドの抽出、機能を作成し、[リーダー] [リーダー] クエリに直接必要な場合は、データをサンプリングするために必要なクエリを貼り付けます。

9. アップロードされたデータセットで始まる Azure ML の実験フローを構築します。

## <a name="largedbtodb"></a>シナリオ \#6: SQL Server 上に設置型データベース、Azure 仮想マシンで SQL Server を対象とする大規模なデータセット

![Azure の SQL DB へのオンプレミスの大規模な SQL DB][6]

#### その他の Azure リソース: Azure 仮想マシン (SQL Server / IPython Notebook サーバー)

1.  SQL Server と IPython Notebook サーバーを実行する Azure 仮想マシンを作成します。

2.  いずれかのデータ エクスポート方法を使用して、SQL Server からダンプ ファイルにデータをエクスポートします。

    a.  注: 内部設置型のデータベースからすべてのデータを移動する場合
        別の (高速な) 方法を完全なデータベースを移動する、
        Azure で SQL Server インスタンス。 データをエクスポートする手順をスキップします。
        データベースを作成し、ターゲット データベースにデータの読み込み/インポートし、
        別の方法に従います。

3.  ダンプ ファイルを Azure ストレージ コンテナーにアップロードします。

4.  Azure 仮想マシンで実行している SQL Server データベースにデータを読み込みます。

    a.  SQL Server VM にログインします。

    b.  データ ファイルを Azure ストレージ コンテナーからローカルの VM フォルダーにダウンロードします。

    c.  SQL Server Management Studio を実行します。

    d.  データベースとターゲット テーブルを作成します。

    e.  いずれかの一括インポート方法を使用してデータを読み込みます。

    f.  テーブルの結合が必要な場合は、インデックスを作成して処理時間を短縮します。

> [AZURE.NOTE] 大きなデータ サイズの読み込みを高速がパーティション分割されたテーブルを作成しを一括、並行してデータをインポートします。 詳細については、次を参照してください。 [SQL パーティション テーブルにデータの並列インポート](machine-learning/machine-learning-data-science-parallel-load-sql-partitioned-tables.md)します。

5.  データを探索し、必要に応じて機能を作成します。 機能をデータベース テーブルで具体化する必要はありません。 作成に必要なクエリに注意してください。

6.  必要に応じて、データのサンプル サイズを決定します。

7.  サインイン、 [Azure Machine Learning Studio](https://studio.azureml.net/)します。

8. [リーダー] [リーダー] モジュールを使用して SQL Server から直接データを読み取ります。 フィールドの抽出、機能を作成し、[リーダー] [リーダー] クエリに直接必要な場合は、データをサンプリングするために必要なクエリを貼り付けます。

9. アップロードされたデータセットで始まる Azure ML の実験フローを構築します。

### 内部設置型 SQL Server から Azure SQL Database にデータベース全体をコピーする別の方法

![ローカル DB をデタッチし、Azure の SQL DB にアタッチする][7]

#### その他の Azure リソース: Azure 仮想マシン (SQL Server / IPython Notebook サーバー)

SQL Server VM で SQL Server データベース全体をレプリケートするには、データベースを 1 つの場所/サーバーから別の場所にコピーする必要があり、データベースが一時的にオフラインになることを想定しています。 これは、SQL Server Management Studio のオブジェクト エクスプローラー GUI か、同等の TRANSACT-SQL コマンドを使用して行います。

1. ソースの場所にあるデータベースをデタッチします。 詳細については、次を参照してください。 [データベースをデタッチ](https://technet.microsoft.com/library/ms191491(v=sql.110).aspx)します。
2. Windows エクスプローラーや Windows コマンド プロンプト ウィンドウで、デタッチされたデータベース ファイルとログ ファイルを Azure の SQL Server VM 上のターゲットの場所にコピーします。
3. コピーしたファイルを対象の SQL Server インスタンスにアタッチします。 詳細については、次を参照してください。 [データベースをアタッチする](https://technet.microsoft.com/library/ms190209(v=sql.110).aspx)です。

[デタッチし、アタッチ操作 (TRANSACT-SQL) を使用してデータベースを移動します。](https://technet.microsoft.com/library/ms187858(v=sql.110).aspx)

## <a name="largedbtohive"></a>シナリオ \#7: ローカル ファイルのビッグ データは、Azure HDInsight Hadoop クラスターの Hive データベースを対象

![ローカル ターゲット Hive のビッグ データ][9]

#### その他の Azure リソース: Azure HDInsight Hadoop クラスターと Azure 仮想マシン (IPython Notebook サーバー)

1.  IPython Notebook サーバーを実行する Azure 仮想マシンを作成します。

2.  Azure HDInsight Hadoop クラスターを作成します。

3.  (省略可能) データの前処理とクリーニングを行います。

    a.  Azure からデータにアクセスする IPython Notebook でデータを前処理とクリーニング
        blob です。

    b.  必要に応じて、クリーニングされたデータを表形式に変換します。

    c.  データをローカルの VM ファイルに保存します (IPython Notebook は VM で実行し、ローカル ドライブは VM ドライブを参照します)。

4.  手順 2 で選択されている Hadoop クラスターの既定のコンテナーにデータをアップロードします。

5.  Azure HDInsight Hadoop クラスターの Hive データベースにデータを読み込みます。

    a.  Hadoop クラスターのヘッド ノードにログインします。

    b.  Hadoop コマンド ラインを開きます。

    c.  Hadoop コマンド ラインで、コマンド `cd %hive_home%\bin` を使用して Hive ルート ディレクトリを入力します。

    d.  Hive クエリを実行してデータベースとテーブルを作成し、BLOB ストレージから Hive テーブルにデータを読み込みます。

    > [AZURE.NOTE] データが大きい場合、ユーザーは、パーティションを持つ Hive テーブルを作成できます。 次に、ヘッド ノードの Hadoop コマンド ラインで `for` ループを使用し、パーティション分割された Hive テーブルにデータを読み込むことができます。

6.  Hadoop コマンド ラインでデータを探索し、必要に応じて機能を作成します。 機能をデータベース テーブルで具体化する必要はありません。 作成に必要なクエリに注意してください。

    a.  Hadoop クラスターのヘッド ノードにログインします。

    b.  Hadoop コマンド ラインを開きます。

    c.  Hadoop コマンド ラインで、コマンド `cd %hive_home%\bin` を使用して Hive ルート ディレクトリを入力します。

    d.  Hadoop クラスターのヘッド ノードの Hadoop コマンド ラインで Hive クエリを実行してデータを探索し、必要に応じて機能を作成します。

7.  必要に応じて、Azure Machine Learning Studio に適したデータをサンプリングします。

8.  サインイン、 [Azure Machine Learning Studio](https://studio.azureml.net/)します。

9. 直接データを読み取る、 `Hive Queries` [リーダー] [リーダー] モジュールを使用します。 フィールドの抽出、機能を作成し、[リーダー] [リーダー] クエリに直接必要な場合は、データをサンプリングするために必要なクエリを貼り付けます。

10. アップロードされたデータセットで始まる Azure ML の実験フローを構築します。

## <a name="decisiontree"></a>シナリオを選択するためのデシジョン ツリー
------------------------

次の図は、上記で説明したシナリオと、各シナリオを選択するための Advanced Analytics Process and Technology の選択肢をまとめたものです。 データ処理、探索、特徴エンジニアリング、およびサンプリングは、1 つ以上のメソッド/環境 (ソース、中間、またはターゲット環境) で発生する場合があり、必要に応じて繰り返し実行される可能性があります。 図は、考えられるフローの一部のみを示しており、すべてを網羅しているわけではありません。

![サンプル DS プロセスのチュートリアル シナリオ][8]

### 高度な分析の活用の例

Advanced Analytics Process and Technology とパブリック データセットを使用する Azure Machine Learning の完全な チュートリアルについては、以下を参照してください。


* [Cortana Analytics プロセスの操作: SQL Server を使用して](machine-learning/machine-learning-data-science-process-sql-walkthrough.md)します。
* [Cortana Analytics プロセスの操作: HDInsight Hadoop クラスターを使用して](machine-learning/machine-learning-data-science-process-hive-walkthrough.md)します。


[1]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

