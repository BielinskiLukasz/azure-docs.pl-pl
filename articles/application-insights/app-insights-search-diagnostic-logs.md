<properties 
    pageTitle="Application Insights での ASP.NET のログ、例外、カスタム診断" 
    description="要求、例外、(トレース、NLog、Log4Net を使用して生成された) ログを検索することにより、ASP.NET Web Apps の問題を診断します。" 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/25/2015" 
    ms.author="awills"/>


# Application Insights での ASP.NET のログ、例外、カスタム診断

[Application Insights の ][start] 強力な [診断検索の ][diagnostic] を探索し、アプリケーションから Application Insights SDK によって送信されたテレメトリにドリル ダウンすることができます。 ユーザー ページ ビューなどの多数のイベントは、SDK によって自動的に送信されます。

カスタム イベント、例外レポート、およびトレースを送信するコードを記述することもできます。 log4J、log4net、NLog、System.Diagnostics.Trace などのログ記録フレームワークを既に使用している場合は、これらのログをキャプチャして検索に含めることができます。 このようにすると、ユーザーの操作、例外、その他のイベントをログ トレースと簡単に関連付けられるようになります。

## <a name="send"></a>カスタム テレメトリを記述する前に

まだインストールしていない場合 [Application Insights をプロジェクトの設定 ][start], はすぐに実行します。

アプリケーションを実行すると、アプリケーションがテレメトリを送信し、そのテレメトリが診断検索に表示されます。テレメトリには、サーバーが受信した要求、クライアント側で記録されたページ ビュー、キャッチされなかった例外などがあります。

診断検索を開くと、SDK から自動送信されたテレメトリが表示されます。

![](./media/app-insights-search-diagnostic-logs/appinsights-45diagnostic.png)

![](./media/app-insights-search-diagnostic-logs/appinsights-31search.png)

詳細は、アプリケーションの種類によって異なります。 個々のイベントをクリックして詳細情報を表示できます。

## サンプリング

アプリケーションが送信するデータ量が多く、Application Insights SDK for ASP.NET バージョン 2.0.0-beta3 以降を使用している場合は、アダプティブ サンプリング機能が動作して、テレメトリの一定の割合のみが送信される可能性があります。 [サンプリングについて説明します。](app-insights-sampling.md)


## <a name="events"></a>カスタム イベント

カスタム イベントは、両方に表示 [診断検索の ][diagnostic] し、[ [メトリック エクスプ ローラー ][metrics]します。 カスタム イベントは、デバイス、Web ページ、サーバー アプリケーションから送信できます。 診断用として使用する [[トラック] の使用パターンの把握][track]します。

カスタム イベントには名前があり、プロパティを持つこともできるため、数値の測定値と共にフィルター処理をすることができます。

JavaScript at client

    appInsights.trackEvent("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric measurements:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

サーバー側の C#

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};
    
    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);

サーバー側の VB

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)
    
    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)
    
    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)

### アプリを実行して結果を確認します。

診断検索を開きます。

カスタム イベントを選択し、特定のイベント名を選択します。

![](./media/app-insights-search-diagnostic-logs/appinsights-332filterCustom.png)


プロパティ値を検索用語として入力し、さらにデータをフィルター処理します。

![](./media/app-insights-search-diagnostic-logs/appinsights-23-customevents-5.png)

個々のイベントにドリルダウンして詳細なプロパティを確認します。

![](./media/app-insights-search-diagnostic-logs/appinsights-23-customevents-4.png)

## <a name="pages"></a> ページ ビュー

ページ ビュー テレメトリの送信に含まれる trackPageView() 呼び出しによって [[使用状況] web ページに挿入した JavaScript のスニペット][usage]します。 その主な目的は、概要ページに表示されるページ ビューの数に加えることです。

通常は各 HTML ページで 1 度呼び出されますが、複数の呼び出しを挿入できます。たとえば、シングル ページ アプリで、ユーザーがデータを取得するたびに新しいページを記録できます。

    appInsights.trackPageView(pageSegmentName, "http://fabrikam.com/page.htm"); 

診断検索でフィルターとして使用できるプロパティをアタッチしておくと便利な場合があります。

    appInsights.trackPageView(pageSegmentName, "http://fabrikam.com/page.htm",
     {Game: currentGame.name, Difficulty: currentGame.difficulty});

## <a name="trace"></a> トレース テレメトリ

トレース テレメトリは、特に診断ログを作成するためだけに挿入するコードです。

たとえば、次のような呼び出しを挿入できます。

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

#### ログ記録フレームワークにアダプターをインストールする

log4Net、NLog、System.Diagnostics.Trace といった、ログ記録フレームワークで生成されたログの検索もできます。

1. log4Net または NLog を使用する場合は、プロジェクト内にインストールします。
2. ソリューション エクスプローラーでプロジェクトを右クリックし、[**NuGet パッケージの管理**] を選択します。
3. [オンライン]、[すべて]、**[プレリリースを含める]** の順に選択し、"Microsoft.ApplicationInsights" を検索します。

    ![適切なパッケージのプレリリース バージョンを入手する](./media/app-insights-search-diagnostic-logs/appinsights-36nuget.png)

4. 次のいずれかの適切なパッケージを選択します。
  + Microsoft.ApplicationInsights.TraceListener (System.Diagnostics.Trace コールをキャプチャするため)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

NuGet パッケージは、必要なアセンブリをインストールし、web.config や app.config も変更します。

#### <a name="pepper"></a>診断ログの呼び出しを挿入します。

System.Diagnostics.Trace を使用する場合、通常の呼び出しは次のようになります。

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

log4net または NLog を使用する場合:

    logger.Warn("Slow response - database01");

アプリをデバッグ モードで実行するかデプロイします。

トレース フィルターを選択すると、診断検索にメッセージが表示されます。

### <a name="exceptions"></a>例外

Application Insights で例外レポートを取得すると、特に、失敗した要求と例外間をナビゲートして例外スタックを確認できるようになるため、非常に強力なエクスペリエンスが提供されます。

場合によってにする必要があります。 [数行のコード ][exceptions] 、例外が自動的にキャッチされているかどうかを確認します。

例外テレメトリを送信する明示的なコードの記述もできます。

JavaScript

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C#

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};
    
       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};
    
       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)
    
      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)
    
      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

プロパティと測定値のパラメーターは省略可能ですが、フィルター処理と、特別な情報を追加するのに便利です。 たとえば、複数のゲームを実行できるアプリケーションを使用している場合、1 つのゲームに関連する例外レポートをすべて検索できます。 必要な数だけ項目を各辞書に追加できます。

#### 例外の表示

概要ブレードに表示される例外の概要をクリックすると、詳細が表示されます。 次に例を示します。


![](./media/app-insights-search-diagnostic-logs/appinsights-039-1exceptions.png)[]

例外の種類をクリックすると、実際に発生した例外について表示されます。

![](./media/app-insights-search-diagnostic-logs/appinsights-333facets.png)[]

診断検索を直接開き、例外でフィルター処理し、表示する例外の種類を選択することもできます。

### ハンドルされていない例外のレポート

Application Insights のレポート未処理の例外をどこから入手できる、デバイス、 [web ブラウザー ][usage], 、によってインストルメント化されたかどうかの web サーバー、または [状態の監視 ][redfield] または [Application Insights SDK ][greenbrown]します。

ただし、.NET フレームワークが例外をキャッチする場合もあるため、必ずレポートされるというわけではありません。 すべての例外を確実に表示するために、小さな例外ハンドラーを記述する必要があります。 最良の対処方法は、テクノロジによって異なります。 参照してください [ASP.NET][exceptions] 詳細です。

### ビルドとの関連付け

診断ログを参照するとき、ソース コードは、現在のコードがデプロイされた後に変更されている可能性があります。

したがって、現在のバージョンの URL などのビルド情報を例外やトレースと共にプロパティに配置しておくと役に立ちます。

すべての例外呼び出しにプロパティを個別に追加する代わりに、既定のコンテキストに情報を設定できます。

    // Telemetry initializer class
    public class MyTelemetryInitializer : IContextInitializer
    {
        public void Initialize (TelemetryContext context)
        {
            context.Properties["AppVersion"] = "v2.1";
        }
    }

アプリ初期化子 (Global.asax.cs など) 内:

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyTelemetryInitializer());
    }

### <a name="requests"></a> サーバーの Web 要求

製品利用統計情報が自動的に送信を要求する [Status Monitor を web サーバー ][redfield], 、またはする [Application Insights を web プロジェクト ][greenbrown]します。 また、このテレメトリは、メトリック エクスプローラーの要求と応答のタイムチャートや概要ページにもフィードされます。

その他のイベントを送信する場合は、TrackRequest() API を使用します。

## <a name="questions"></a>Q & A

### <a name="emptykey"></a>「インストルメンテーション キーを空にすることはできません」というエラーが表示されます。

Application Insights をインストールしないでログ アダプターの Nuget パッケージをインストールした可能性があります。

ソリューション エクスプ ローラーで右クリック `ApplicationInsights.config` 選択 **Application Insights の更新**します。 Azure へのサインインを促すダイアログが表示されます。または、Application Insights のリソースを作成するか、既存のリソースを再利用します。 これで問題は修正されます。

### <a name="limits"></a>データの量が保持されますか。

各アプリケーションで、1 秒あたり 500 イベントまでです。 イベントは 7 日間保持されます。

### マイ イベントまたはトレースの一部が表示されません

アプリケーションが送信するデータ量が多く、Application Insights SDK for ASP.NET バージョン 2.0.0-beta3 以降を使用している場合は、アダプティブ サンプリング機能が動作して、テレメトリの一定の割合のみが送信される可能性があります。 [サンプリングについて説明します。](app-insights-sampling.md)


## <a name="add"></a>次のステップ

* [可用性と応答性のテスト ][availability]
* [トラブルシューティング ][qna]








[availability]: app-insights-monitor-web-app-availability.md 
[diagnostic]: app-insights-diagnostic-search.md 
[exceptions]: app-insights-web-failures-exceptions.md 
[greenbrown]: app-insights-asp-net.md 
[metrics]: app-insights-metrics-explorer.md 
[qna]: app-insights-troubleshoot-faq.md 
[redfield]: app-insights-monitor-performance-live-website-now.md 
[start]: app-insights-overview.md 
[track]: app-insights-api-custom-events-metrics.md 
[usage]: app-insights-web-track-usage.md 

