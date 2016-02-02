<properties
    pageTitle="HDInsight Tools for Visual Studio を使用する方法 | Microsoft Azure"
    description="Visual Studio Hadoop Tools for HDInsight をインストールして、Hadoop クラスターに接続し、Hive クエリを実行する方法について説明します。"
    keywords="hadoop tools,hive query,visual studio"
    services="HDInsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="11/30/2015"
    ms.author="jgao"/>


# HDInsight Tools for Visual Studio を使用して Hive クエリを実行する

HDInsight Tools for Visual Studio を使用して HDInsight クラスターに接続し、Hive クエリを送信する方法について説明します。 HDInsight の使用方法の詳細については、次を参照してください。 [HDInsight][hdinsight.introduction] と [HDInsight][hdinsight.get.started]します。 Storm クラスターへの接続に関する詳細については、次を参照してください。 [Visual Studio][hdinsight.storm.visual.studio.tools]します。

**前提条件**

このチュートリアルを完了して、Visual Studio で Hadoop ツールを使用するには、次が必要になります。

- Azure HDInsight クラスター: Linux ベースまたは Windows ベースのクラスターは、このドキュメントの手順で動作します。 クラスター作成の詳細については、次のいずれかをご覧ください。

    - [Linux ベースの HDInsight を概要します。](hdinsight-hadoop-linux-tutorial-get-started.md)
    - [Windows ベースの HDInsight を概要します。](hdinsight-hadoop-tutorial-get-started-windows.md)

- 次のソフトウェアを搭載したワークステーション

    - Windows 8.1、Windows 8、Windows 7
    - 下記のいずれかのバージョンの Visual Studio
        Visual Studio 2013 Community/Professional/Premium または Ultimate [アップデート 4](https://www.microsoft.com/download/details.aspx?id=44921)
        -Visual Studio 2015 (コミュニティ/Enterprise)
    >[AZURE.NOTE] 現時点では HDInsight Tools for Visual Studio は英語版のみになります。


## HDInsight Tools for Visual Studio をインストールする

HDInsight Tools for Visual Studio と Microsoft Hive ODBC ドライバーは、Microsoft Azure SDK for .NET バージョン 2.5.1 以降に付属しています。 使用してインストールすることができます、 [Web Platform Installer](http://go.microsoft.com/fwlink/?LinkId=255386)します。 お使いの Visual Studio バージョンに対応するものを選択する必要があります。 Visual Studio がインストールされていない場合は、最新の Visual Studio Community と Azure SDK を使用してをインストールすることができます、 [Web Platform Installer](http://go.microsoft.com/fwlink/?LinkId=255386) または、次のリンクを使用します。

- [Microsoft Azure SDK と visual Studio Community 2015](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VS2015CommunityAzurePack.appids)
- [Microsoft Azure SDK と visual Studio Community 2013](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VS2013CommunityAzurePack.appids)
- [Microsoft Azure SDK for .NET (VS 2015)](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VWDOrVs2015AzurePack.appids)
- [Microsoft Azure SDK for .NET (VS 2013)](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VWDOrVs2013AzurePack.appids)

![Hadoop ツール: HDinsight Tools for Visual Studio Web Platform installer][1]

## Azure サブスクリプションに接続する

HDInsight Tools for Visual Studio を使用して、HDInsight クラスターへの接続、いくつかの基本的な管理操作の実行、Hive クエリの実行が可能です。
>[AZURE.NOTE] HDInsight Emulator に接続する方法については、次を参照してください。 [HDInsight Emulator の概要](../hdinsight-get-started-emulator.md/#vstools)します。

>[AZURE.NOTE] 汎用の Hadoop クラスター (プレビュー) に接続する方法については、次を参照してください。 [Visual Studio を使用して Hive クエリを送信](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx)します。


**Azure サブスクリプションに接続するには**

1.  Visual Studio を開きます。
2.  **[ビュー]** メニューで、**[サーバー エクスプローラー]** をクリックして、サーバー エクスプローラー ウィンドウを開きます。
3.  **[Azure]** を展開して、**[HDInsight]** を展開します。
    >[AZURE.NOTE]**[HDInsight タスク一覧]** ウィンドウが開きます。 ウィンドウが表示されない場合は、**[ビュー]** メニューで **[その他のウィンドウ]** をクリックし、次に **[HDInsight タスク一覧ウィンドウ]** をクリックします。  
4.  Azure サブスクリプションの資格情報を入力し、**[サインイン]** をクリックします。 この操作は、このワークステーションで、まだ一度も Visual Studio から Azure サブスクリプションに接続していない場合にのみ必要です。
5.  サーバー エクスプローラーに、既存の HDInsight クラスターの一覧が表示されます。 クラスターが 1 つもない場合は、Azure ポータル、Azure PowerShell、または HDInsight SDK を使用してプロビジョニングできます。 詳細については、次を参照してください。 [HDInsight クラスターのプロビジョニング ][hdinsight-provision]します。

    ![Hadoop ツール: HDInsight Tools for Visual Studio サーバー エクスプローラー クラスターの一覧][5]
6.  HDInsight クラスターを展開します。 **Hive データベース**、既定のストレージ アカウント、リンクされたストレージ アカウント、**Hadoop サービス ログ**が表示されます。 さらに、エンティティを展開できます。

Azure サブスクリプションに接続した後で、次を実行できます。

**Visual Studio から Azure ポータルに接続するには**

- サーバー エクスプローラーで、**[Azure]**、**[HDInsight]** の順に展開し、[HDInsight クラスター] を右クリックして、**[Manage Cluster in Azure Portal]** をクリックします。

**Visual Studio から質問をしたりフィードバックを提供したりするには**

- **[ツール]** メニューで、**[HDInsight]**、**[MSDN フォーラム]** の順にクリックして質問するか、**[Give Feedback]** をクリックします

## リンクしているリソースへの移動

サーバー エクスプローラーで、既定のストレージ アカウント、すべてのリンクされたストレージ アカウントを確認できます。 既定のストレージ アカウントを展開すると、そのストレージ アカウントのコンテナーを表示できます。 既定のストレージ アカウントと既定のコンテナーがマークされます。 コンテナーのいずれかの右クリックしてコンテンツを表示できます。

![HDInsight Tools for Visual Studio サーバー エクスプローラー クラスターの一覧][2]

## Hive クエリを実行する

[Apache Hive][apache.hive] データ ウェアハウス インフラストラクチャで Hadoop に構築されるデータの概要、クエリ、および分析を提供することができます。 HDInsight Tools for Visual Studio は Visual Studio からの Hive クエリの実行をサポートします。 Hive の詳細については、次を参照してください。 [HDInsight][hdinsight.hive]します。

HDInsight クラスターに対して Hive スクリプトをテストするには、 数分以上かかる場合があります。 HDInsight Tools for Visual Studio では、ライブ クラスターに接続しなくても、Hive スクリプトをローカルで検証できます。

加えて、HDInsight Tools for Visual Studio では、特定の Hive ジョブの YARN ログを収集して表示することで、Hive ジョブの内容を見ることができます。

### **hivesampletable** の表示

すべての HDInsight クラスターには、*hivesampletable* という Hive テーブルのサンプルが付属します。 このテーブルを使用して Hive テーブルの一覧表示、テーブル スキーマの表示、Hive テーブル内の行の一覧表示を行う方法を説明します。



**Hive テーブルを一覧表示し、Hive テーブル スキーマを表示するには**

1.  **サーバー エクスプローラー**から、**[Azure]**、**[HDInsight]**、任意のクラスター、**[Hive データベース]**、**[既定]**、**[hivesampletable]** の順に展開し、テーブル スキーマを表示します。
4.  **[hivesampletable]** を右クリックし、**[上位 100 行を表示]** をクリックして、行を一覧表示します。 これは、Hive ODBC ドライバーを使用して、次の Hive クエリを実行することと同等です。

        SELECT * FROM hivesampletable LIMIT 100

    行カウントをカスタマイズできます。

    ![Hadoop ツール:  HDinsight Hive Visual Studio スキーマ クエリ][6]

### Hive テーブルの作成

GUI を使用して Hive テーブルを作成するか、Hive クエリを使用できます。 Hive クエリの使用方法の詳細については、次を参照してください。 [Hive クエリの実行](#run.queries)します。

**Hive テーブルを作成するには**

1. **サーバー エクスプローラー**から、**[Azure]**、**[HDInsight クラスター]**、HDInsight クラスター、**[Hive データベース]** の順に展開し、**[既定]** を右クリックして、**[テーブルの作成]** をクリックします。
2. テーブルを構成します。
3. **[テーブルの作成]** をクリックして、新しい Hive テーブルを作成するためのジョブを送信します。

    ![Hadoop ツール: HDinsight Visual Studio ツールで Hive テーブルを作成する][7]

### <a name="run.queries"></a>検証し、Hive クエリの実行

Hive クエリを作成して実行するには次の 2 つの方法があります。

- アドホック クエリを作成する
- Hive アプリケーションを作成する

**アドホック クエリを作成、検証、実行するには**

1. **サービス エクスプローラー**から、**[Azure]**、**[HDInsight クラスター]** の順に展開します。
2. クエリを実行するクラスターを右クリックして、**[Hive クエリの作成]** をクリックします。
3. Hive クエリを入力します。 Hive エディターは Intellisense をサポートしています。 HDInsight Tools for Visual Studio で、Hive スクリプトの編集時にリモート メタデータの読み込みがサポートされます。 たとえば、"SELECT * FROM" と入力すると、Intellisense には推奨されるテーブル名が一覧表示されます。 テーブル名を指定すると、列名が Intellisense に一覧表示されます。 ツールは、ほとんどすべての Hive の DML ステートメント、サブクエリ、および組み込みの UDF をサポートします。

    ![Hadoop ツール: HDInsight Visual Studio Tools Intellisense][13]

    ![Hadoop ツール: HDInsight Visual Studio Tools Intellisense][14]
    > [AZURE.NOTE] クラスターのメタデータのうち、HDInsight のツール バーで選択したものだけが推奨として表示されます。
4. (任意) **[Validate Script]** をクリックして、スクリプトの構文エラーを確認します。

    ![Hadoop ツール: HDinsight Tools for Visual Studio ローカル検証][10]

4. **[送信]** または**[(高度な) 送信]** をクリックします。 高度な送信オプションの場合は、スクリプトの**ジョブ名**、**引数**、**追加の構成**、**ステータス ディレクトリ**を構成します。

    ![HDinsight Hadoop の Hive クエリ][9]

    ジョブを送信すると、**[Hive ジョブの概要]** ウィンドウが表示されます。

    ![HDInsight Hadoop の Hive クエリの概要][8]
5. **[更新]** ボタンを使用して、ジョブのステータスが **[完了]** に変更されるまで、ステータスを更新します。
6. ウィンドウ下部のリンクをクリックして次の **[ジョブ クエリ]**、**[ジョブ出力]**、**[ジョブのログ]** または **[Yarn ログ]** を表示します。



**Hive ソリューションを作成し、実行するには**

1. **[ファイル]** メニューの **[新規作成]** をポイントし、**[プロジェクト]** をクリックします。
2. 左側のウィンドウから **[HDInsight]** を選択し、中央のウィンドウで **[Hive アプリケーション]** を選択して、プロパティを入力し、**[OK]** をクリックしします。

    ![Hadoop ツール: HDinsight Visual Studio ツール新しい Hive プロジェクト][11]
3. **ソリューション エクスプローラー**で、**Script.hql** をダブルクリックして開きます。
4. Hive スクリプトを検証するには、**[Validate Script]** ボタンをクリックするか、Hive エディターでスクリプトを右クリックしてコンテキスト メニューから **[Validate Script]** をクリックします。


### Hive ジョブの表示

Hive ジョブのジョブ クエリ、ジョブ出力、ジョブのログ、Yarn ログを表示できます。 詳細については、先のスクリーンショットをご覧ください。

ツールの最新のリリースでは、提示の YARN ログを収集して、Hive ジョブの内容を確認できます。 YARN ログは、パフォーマンス問題の検証に役立ちます。 HDInsight YARN を収集する方法の詳細については、次のログを参照してください。 [アクセス HDInsight アプリケーション ログ Programmatically][hdinsight.access.application.logs]します。

**Hive ジョブの表示**

1. **サーバー エクスプローラー**から、**[Azure]**、**[HDInsight]** の順に展開します。
2. HDInsight クラスターを右クリックし、**[ジョブの表示]** をクリックします。 クラスター上で実行した Hive ジョブの一覧が表示されます。
3. ジョブの一覧でジョブをクリックして選択し、**[Hive ジョブの概要]** ウィンドウを使用して **[ジョブ クエリ]**、**[ジョブ出力]**、**[ジョブのログ]**、または **[Yarn ログ]** を開きます。

    ![Hadoop ツール: HDinsight Visual Studio ツールで新しい Hive ジョブを表示する][12]

### HiveServer2 による Hive 実行の高速化

>[AZURE.NOTE] この機能は、HDInsight クラスター バージョン 3.2 以降のみで動作します。

HDInsight Tools は以前、WebHCat (Templeton とも呼ばれます) を介して Hive ジョブを送信していました。 そのためジョブの詳細やエラー情報を返すのに長い時間がかかっていました。
このようなパフォーマンスの問題を解決するために、HDInsight Tools では、HiveServer2 を通して直接クラスターで Hive ジョブを実行することにより RDP/SSH をバイパスすることができるようになりました。 
パフォーマンスが向上するだけでなく、ユーザーは、Hive on Tez のグラフやタスクの詳細を表示することもできます。

HDInsight クラスター バージョン 3.2 以降では、**[HiveServer2 から実行]** ボタンが表示されます。

![hdinsight visual studio tools execute via hiveserver2](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.execute.via.hiveserver2.png)

ログがリアルタイムでストリームバックしていることを確認できます。また、Hive クエリが Tez で実行されている場合はジョブ グラフも確認できます。

![hdinsight visual studio tools fast path hive execution](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png)

**HiveServer2 を使用したクエリの実行と WebHCat を使用したクエリの送信の違い**

HiveServer2 を使用してクエリを実行した場合、パフォーマンス上の利点は多数ありますが、いくつかの制限もあります。 制限の一部は、運用環境での使用に適していません。 その違いを次の表に示します。

| | HiveServer2 を使用して実行した場合| WebHCat を使用して送信した場合|
|---|---|---|
| クエリの実行| WebHCat のオーバーヘッドが解消されます (これにより、"TempletonControllerob" という名前の MapReduce ジョブが開始されます)。| クエリが WebHCat を使用して実行される間、WebHCat によって MapReduce ジョブが開始されるため、待機時間がさらに長くなります。|
| ログの再ストリーミング| ほぼリアルタイムで行われます。| ジョブの実行ログを利用できるのは、ジョブが完了したときのみです。|
| ジョブ履歴の表示| HiveServer2 を使用してクエリを実行する場合、そのジョブ履歴 (ジョブのログ、ジョブの出力) は保持されません。アプリケーションは YARN UI で表示できますが、情報は限定的です。| WebHCat を使用してクエリを実行する場合、そのジョブ履歴 (ジョブのログ、ジョブの出力) は保持され、Visual Studio、HDInsight SDK、PowerShell を使用して表示できます。|
| ウィンドウを閉じる| HiveServer2 を使用した実行は "同期的な" 方法であるため、ウィンドウを開いたままにしておく必要があります。ウィンドウを閉じると、クエリの実行は取り消されます。| WebHCat を使用した送信は "非同期的な" 方法であるため、WebHCat を使用してクエリを送信し、Visual Studio を終了することができます。結果は、いつでも戻って確認できます。|


### Tez Hive ジョブのパフォーマンス グラフ

HDInsight Tools for Visual Studio は Tez 実行エンジンで実行された Hive ジョブのパフォーマンス グラフの表示をサポートしています。 Tez を有効にする方法については、次を参照してください。 [HDInsight][hdinsight.hive]します。 Visual Studio で Hive ジョブを送信した後、ジョブが完了すると、Visual Studio にグラフが表示されます。 最新のジョブの状態を取得するには、**[更新]**ボタンをクリックする必要があります。
> [AZURE.NOTE] この機能はバージョン 3.2.4.593 以上の HDInsight クラスターでのみ使用でき、完了したジョブでのみ有効です。 これは、Windows ベースと Linux ベースの両方のクラスターで機能します。

![hadoop hive tez パフォーマンス グラフ](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.hive.tez.performance.graph.png)

Hive クエリを理解しやすくするために、今回のリリースでツールに Hive 演算子表示機能が追加されました。 ジョブ グラフの頂点をダブルクリックするだけで、頂点内のすべての演算子を表示できます。 特定の演算子の上にマウス ポインターを置きことで、その演算子の詳細を表示することもできます。

### Hive on Tez ジョブのタスク実行ビュー

Hive on Tez ジョブのタスク実行ビューは、Hive ジョブの構造化および視覚化された情報の取得と、ジョブの詳細の取得に使用できます。ディレクトリ階層内に 
パフォーマンスの問題がある、さらに詳細を取得するビューを使用することができます。 各タスクの動作と各タスクに関する詳細情報など 
(データの読み取り/書き込み、スケジュール/開始/終了時刻など) ジョブの構成または視覚化された情報に基づいてシステム アーキテクチャが調整できるようにします。

![hdinsight visual studio tools task execution view](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png)

## Pig のスクリプトを実行する

HDInsight Tools for Visual Studio は、Pig スクリプトの作成と、HDInsight クラスターへの送信をサポートしています。 ユーザーは、テンプレートから Pig プロジェクトを作成して、HDInsight クラスターにスクリプトを送信できます。

## 次のステップ

この記事では、Hadoop ツール パッケージを使用して Visual Studio から HDInsight クラスターに接続し、Hive クエリを実行する方法を説明しました。 詳細については、次を参照してください。

- [HDInsight][hdinsight.hive]
- [HDInsight][hdinsight.get.started]
- [HDInsight][hdinsight.submit.jobs]
- [HDInsight][hdinsight.analyze.twitter.data]






[installation]: #installation 
[connect to your azure subscription]: #connect-to-your-azure-subscription 
[navigate the linked resources]: #navigate-the-linked-resources 
[run hive queries]: #run-hive-queries 
[next steps]: #next-steps 
[1]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png 
[2]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png 
[5]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png 
[6]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png 
[7]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png 
[8]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png 
[9]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png 
[10]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png 
[11]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png 
[12]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png 
[13]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png 
[14]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png 
[hdinsight-provision]: ../hdinsight/hdinsight-provision-clusters.md 
[hdinsight.introduction]: ../hdinsight-introduction.md 
[hdinsight.get.started]: ../hdinsight-get-started.md 
[hdinsight.hive]: ../hdinsight/hdinsight-use-hive.md 
[hdinsight.submit.jobs]: ../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md 
[hdinsight.analyze.twitter.data]: ../hdinsight/hdinsight-analyze-twitter-data.md 
[hdinsight.storm.visual.studio.tools]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md 
[hdinsight.access.application.logs]: ../hdinsight/hdinsight-hadoop-access-yarn-app-logs.md 
[apache.hive]: http://hive.apache.org 

