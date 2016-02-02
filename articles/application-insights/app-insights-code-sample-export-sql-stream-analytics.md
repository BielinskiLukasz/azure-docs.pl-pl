<properties 
    pageTitle="チュートリアル: Application Insights から SQL Database へのテレメトリのエクスポート" 
    description="Stream Analytics を使用して Application Insights データを SQL へ継続的にエクスポートします。" 
    services="application-insights" 
    documentationCenter=""
    authors="noamben" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/07/2015" 
    ms.author="awills"/>


# チュートリアル: Stream Analytics を使用した Application Insights から SQL へのエクスポート

この記事からテレメトリ データを移動する方法を説明する [Visual Studio Application Insights ][start] を使用して Azure SQL データベースに [連続エクスポート ][export] と [Azure Stream Analytics](http://azure.microsoft.com/services/stream-analytics/)します。

連続エクスポートにより、JSON 形式でテレメトリ データが Azure Storage に移動されます。 Azure Stream Analytics を使って JSON オブジェクトを解析し、データベース テーブルに行を作成します。

(一般的に、連続エクスポートは、アプリが Application Insights に送信するテレメトリの独自の分析を行うための手段です。 データの集合など、エクスポートされたテレメトリで他の処理を行うように、このコード サンプルを適合させることもできます。)

監視対象のアプリが存在していることを想定して説明を始めます。


この例では、ページ ビュー データを使用しますが、同じパターンを簡単に他のデータ型 (カスタム イベントや例外など) に拡張できます。


## Application Insights SDK の追加

アプリケーションを監視する [Application Insights SDK ][start] をアプリケーションにします。 プラットフォーム、IDE、および言語ごとにそれぞれ異なる SDK やヘルパー ツールがあります。 Web ページ、Java または ASP.NET Web サーバー、および各種モバイル デバイスを監視できます。 すべての Sdk にテレメトリを送信する、 [Application Insights ポータルの ][portal], 、ここで、強力な分析ツールおよび診断ツールを使用してストレージにデータをエクスポートします。

作業を開始するには:

1. 取得、 [Microsoft Azure でアカウント](http://azure.microsoft.com/pricing/)します。
2. [Azure ポータルの ][portal], 、アプリの新しい Application Insights リソースを追加します。

    ![[新規]、](./media/app-insights-code-sample-export-sql-stream-analytics/010-new-asp.png)

    (対象とするアプリの種類やお使いのサブスクリプションは異なる可能性があります。)
3. クイック スタートを開き、アプリの種類に応じて SDK をセットアップする方法を調べます。

    ![クイック スタートを選択し、指示に従います](./media/app-insights-code-sample-export-sql-stream-analytics/020-quick.png)

    アプリの種類が表示されない場合を見て、 [はじめに ][start] ページです。

4. この例では、Web アプリを監視しますので、Visual Studio の Azure ツールを使用して SDK をインストールします。 Application Insights リソースの名前を指定します。

    ![Visual Studio ソリューション エクスプ ローラーで、プロジェクトを右クリックし、Application Insights の追加] を選択します。 [Send telemetry to を選択、新しいリソースを作成または既存のものを使用します。](. media/app-insights-code-sample-export-sql-stream-analytics/appinsights-d012-addbrown.png/)

5. アプリを発行して Application Insights のリソースに表示されるテレメトリ データを確認します。


## Azure でのストレージの作成

連続エクスポートでは、常に Azure のストレージ アカウントにデータが出力されるため、まずストレージを作成する必要があります。

1. サブスクリプションの「昔ながらの」ストレージ アカウントの作成、 [Azure ポータルの ][portal]します。

    ![Azure ポータルでの [新規]、[データ ストレージ] を選択します。 [クラシック] を選択し、[作成] をクリックします。 ストレージ名を指定します。](. media/app-insights-code-sample-export-sql-stream-analytics/040-store.png/)

2. コンテナーを作成する

    ![新しいストレージで、](./media/app-insights-code-sample-export-sql-stream-analytics/050-container.png)

3. ストレージ アクセス キーのコピー

    これは、ストリーム分析サービスへの入力のセットアップのためにすぐに必要になります。

    ![ストレージで、](./media/app-insights-code-sample-export-sql-stream-analytics/21-storage-key.png)

## Azure ストレージへの連続エクスポートの開始

1. Azure ポータルで、アプリケーション用に作成した Application Insights リソースを参照します。

    ![[参照]、](./media/app-insights-code-sample-export-sql-stream-analytics/060-browse.png)

2. 連続エクスポートを作成します。

    ![[設定]、](./media/app-insights-code-sample-export-sql-stream-analytics/070-export.png)

    以前に作成したストレージ アカウントを選択します。

    ![エクスポート先の設定](./media/app-insights-code-sample-export-sql-stream-analytics/080-add.png)

    表示するイベントの種類を設定します。

    ![イベントの種類の選択](./media/app-insights-code-sample-export-sql-stream-analytics/085-types.png)

3. データを蓄積します。 しばらく待機し、ユーザーにアプリケーションを使用してもらいます。 テレメトリが開始し、統計グラフが表示されます [メトリックス エクスプ ローラー](app-insights-metrics-explorer.md) と個々 のイベントが [診断検索](app-insights-diagnostic-search.md)します。

    また、データはストレージにもエクスポートされます。

4. エクスポートされたデータを、ポータルまたは Visual Studio で調べます。ポータルの場合は、**[参照]**、ストレージ アカウント、**[コンテナー]**の順に選択します。 Visual Studio で、**[表示]、[Cloud Explorer]** の順に選択します。[Azure]、[Storage] の順に開きます  (このメニュー オプションがない場合は、Azure SDK をインストールする必要があります。[新しいプロジェクト] ダイアログを開き、[Visual c#]、[クラウド]、[Microsoft Azure SDK for .NET の取得] の順に開きます)。

    ![Visual Studio で次の順に開きます。](./media/app-insights-code-sample-export-sql-stream-analytics/087-explorer.png)

    パス名の共通部分を書き留めます。共通部分はアプリケーションの名前とインストルメンテーション キーから派生します。

イベントが JSON 形式で BLOB ファイルに書き込まれます。 各ファイルに 1 つ以上のイベントが含まれる場合があります。 このため、イベント データを読み取って必要なフィールドをフィルター処理します。 データの処理に関して行えることはありますが、今日の計画は、Stream Analytics を使用してデータを SQL database に移動することです。 それにより、興味深い多くのクエリを実行しやすくなります。

## Azure SQL Database の作成

サブスクリプションからもう一度開始 [Azure ポータルの ][portal], 、データベースの作成 (と新しいサーバーのいずれかが既にある場合を除き、)、データを出力します。

![[新規]、](./media/app-insights-code-sample-export-sql-stream-analytics/090-sql.png)


データベース サーバーに Azure サービスがアクセス可能であることをご確認ください。


![[参照]、](./media/app-insights-code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## Azure SQL DB でのテーブルの作成

任意の管理ツールを使って前のセクションで作成したデータベースに接続します。 このチュートリアルで使用するので [SQL Server 管理ツール](https://msdn.microsoft.com/ms174173.aspx) (SSMS)。

![](./media/app-insights-code-sample-export-sql-stream-analytics/31-sql-table.png)

新しいクエリを作成し、次の T-SQL を実行します。

```SQL

CREATE TABLE [dbo].[PageViewsTable](
    [pageName] [nvarchar](max) NOT NULL,
    [viewCount] [int] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlDataPort] [int] NULL,
    [urlDataprotocol] [nvarchar](50) NULL,
    [urlDataHost] [nvarchar](50) NULL,
    [urlDataBase] [nvarchar](50) NULL,
    [urlDataHashTag] [nvarchar](max) NULL,
    [eventTime] [datetime] NOT NULL,
    [isSynthetic] [nvarchar](50) NULL,
    [deviceId] [nvarchar](50) NULL,
    [deviceType] [nvarchar](50) NULL,
    [os] [nvarchar](50) NULL,
    [osVersion] [nvarchar](50) NULL,
    [locale] [nvarchar](50) NULL,
    [userAgent] [nvarchar](max) NULL,
    [browser] [nvarchar](50) NULL,
    [browserVersion] [nvarchar](50) NULL,
    [screenResolution] [nvarchar](50) NULL,
    [sessionId] [nvarchar](max) NULL,
    [sessionIsFirst] [nvarchar](50) NULL,
    [clientIp] [nvarchar](50) NULL,
    [continent] [nvarchar](50) NULL,
    [country] [nvarchar](50) NULL,
    [province] [nvarchar](50) NULL,
    [city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
    [eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)
```

![](./media/app-insights-code-sample-export-sql-stream-analytics/34-create-table.png)

このサンプルでは、ページ ビューからデータを使用します。 使用可能なその他のデータを表示する、JSON の出力を検査しを参照してください、 [データ モデルをエクスポート](app-insights-export-data-model.md)します。

## Azure Stream Analytics インスタンスの作成

[従来の Azure ポータル](https://manage.windowsazure.com/), Azure Stream Analytics サービスを選択して、新しい Stream Analytics ジョブを作成します。


![](./media/app-insights-code-sample-export-sql-stream-analytics/37-create-stream-analytics.png)



![](./media/app-insights-code-sample-export-sql-stream-analytics/38-create-stream-analytics-form.png)

新しいジョブが作成された後、その詳細を展開します。

![](./media/app-insights-code-sample-export-sql-stream-analytics/41-sa-job.png)

#### BLOB の場所の設定

連続エクスポート BLOB から入力を取るよう設定します。

![](./media/app-insights-code-sample-export-sql-stream-analytics/42-sa-wizard1.png)

ここで、ストレージ アカウントからのプライマリ アクセス キーが必要になります。これは前にメモしておいたものです。 ストレージ アカウント キーとしてこれを設定します。

![](./media/app-insights-code-sample-export-sql-stream-analytics/46-sa-wizard2.png)

#### パスのプレフィックス パターンの設定

![](./media/app-insights-code-sample-export-sql-stream-analytics/47-sa-wizard3.png)

日付の書式は **YYYY-MM-DD** (**ダッシュ**付き) に設定してください。

パスのプレフィックス パターンは、Stream Analytics がストレージ内の入力ファイルを検索する方法を指定します。 連続エクスポートによるデータ格納方法と一致するように設定する必要があります。 次のように設定します。

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

次の点に注意してください。

* `webapplication27` Application Insights リソースの名前を指定 **すべて小文字に**します。
* `1234...` Application Insights リソースのインストルメンテーション キーは、 **削除ダッシュで区切って**します。
* `PageViews` を分析するデータの種類です。 使用可能な種類は、連続エクスポートで設定するフィルターによって異なります。 エクスポートされたデータを他の利用可能な型を参照して調べる、 [データ モデルをエクスポート](app-insights-export-data-model.md)します。
* `{date}/{time}` はそのまま書き込まれるパターンです。

Application Insights リソースの名前と iKey を取得するには、概要ページの [Essentials] を開くか、[設定] を開きます。

#### 初期セットアップの完了

シリアル化の書式を確認します。

![[確認]をクリックしてウィザードをして閉じます](./media/app-insights-code-sample-export-sql-stream-analytics/48-sa-wizard4.png)

ウィザードを閉じ、セットアップが完了するまで待機します。
>[AZURE.TIP] 入力パスが正しく設定されていることを確認するには、Sample 関数を使用します。 失敗した場合: 選択したサンプルの時間範囲でストレージにデータがあることを確認します。 入力定義を編集し、ストレージ アカウント、パスのプレフィックス、日付形式が正しく設定されていることを確認します。

## クエリの設定

クエリ セクションを開きます。

![ストリーム分析で、](./media/app-insights-code-sample-export-sql-stream-analytics/51-query.png)

既定のクエリを次のもので置き換えます。

```SQL

    SELECT flat.ArrayValue.name as pageName
    , flat.ArrayValue.count as viewCount
    , flat.ArrayValue.url as url
    , flat.ArrayValue.urlData.port as urlDataPort
    , flat.ArrayValue.urlData.protocol as urlDataprotocol
    , flat.ArrayValue.urlData.host as urlDataHost
    , flat.ArrayValue.urlData.base as urlDataBase
    , flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat
```

最初のいくつかのプロパティはページ ビュー データに固有のプロパティです。 他のテレメトリの種類のエクスポートにはそれぞれ異なるプロパティがあります。 を参照してください [プロパティの型と値のデータ モデルの参照を詳しく説明します](app-insights-export-data-model.md)。

## データベースへの出力のセットアップ

SQL を出力として選択します。

![ストリーム分析で、](./media/app-insights-code-sample-export-sql-stream-analytics/53-store.png)

SQL データベースを指定します。


![データベースの詳細を入力します](./media/app-insights-code-sample-export-sql-stream-analytics/55-output.png)

ウィザードを閉じ、出力がセットアップされたことを示す通知を待機します。

## 処理の開始

ジョブを操作バーから開始します。

![ストリーム分析で、](./media/app-insights-code-sample-export-sql-stream-analytics/61-start.png)

データの処理を今から開始するか、以前のデータを使って開始するかを選択できます。 連続エクスポートの実行を開始してから既に時間が経過している場合は、後者がお勧めです。


![ストリーム分析で、](./media/app-insights-code-sample-export-sql-stream-analytics/63-start.png)

数分後、SQL Server Management Tools に戻り、流れているデータを監視します。 たとえば、次のようなクエリを使用します。

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]

## 関連記事:

* [ワーカー ロールを使用して SQL にエクスポートします。](app-insights-code-sample-export-telemetry-sql-database.md)
* [Stream Analytics を使用して power bi にエクスポートします。](app-insights-export-power-bi.md)
* [詳細なデータ モデルのプロパティの型と値の参照。](app-insights-export-data-model.md)
* [Application Insights での連続エクスポート](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)




[diagnostic]: app-insights-diagnostic-search.md 
[export]: app-insights-export-telemetry.md 
[metrics]: app-insights-metrics-explorer.md 
[portal]: http://portal.azure.com/ 
[start]: app-insights-overview.md 

