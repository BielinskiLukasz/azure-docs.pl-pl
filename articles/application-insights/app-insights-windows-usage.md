<properties 
    pageTitle="Application Insights による Windows ストア アプリおよび Windows Phone アプリの使用状況の監視" 
    description="Application Insights を使用して Windows デバイス アプリの使用状況を分析します。" 
    services="application-insights" 
    documentationCenter="windows"
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

#  Application Insights による Windows ストア アプリと Windows Phone アプリの使用状況の監視

*Application Insights はプレビュー段階です。*

ユーザー数と、ユーザーがアプリ内で参照しているページを確認します。 Application Insights は、すぐには、そのデータを提供します。 また、アプリに数行のコードを挿入することで、ユーザーがアプリにアクセスした経路と、そのアプリで実現している内容についても詳しく把握できます。

これは既に完了していない、追加 [アプリ プロジェクトに Application Insights][windows], を再発行します。 


## <a name="usage"></a>使用状況の追跡

概要タイムラインで、ユーザーとセッションのグラフをクリックして、分析の詳細を表示します。


![](./media/app-insights-windows-usage/appinsights-d018-oview.png)

* **ユーザー** さまざまなデバイスで同じユーザーが 2 回カウントされますので、匿名で追跡されます。
* A **セッション** 、アプリが中断されたときに (偶発的な中断がカウントされないように、簡単な間隔よりも) はカウントされます。

#### セグメント化

グラフを分割して、さまざまな条件で内訳を取得します。 たとえば、アプリの各バージョンを使用しているユーザー数を表示するには、ユーザーのグラフを開き、アプリケーションのバージョンでセグメント化します。 

![ユーザーのグラフでセグメント化を切り替えて、アプリケーションのバージョンを選択する](./media/app-insights-windows-usage/appinsights-d25-usage.png)


#### ページ ビュー

経路をアプリを使用してユーザーを探索する挿入 [ページ ビュー テレメトリ][api] をコードにします。

    var telemetry = new TelemetryClient();
    telemetry.TrackPageView("GameReviewPage");

ページ ビュー グラフでその結果を確認し、その詳細を開きます。

![](./media/app-insights-windows-usage/appinsights-d27-pages.png)

ページをクリックして特定のデータの詳細を表示します。

#### カスタム イベント

アプリからカスタム イベントを送信するコードを挿入することで、ユーザーの操作と、特定の機能やシナリオの使用状況を追跡できます。 

For example:

    telemetry.TrackEvent("GameOver");

The data will appear in the Custom Events grid. メトリックス エクスプローラーで集計ビューを表示するか、いずれかのイベントをクリックして特定のデータを表示できます。

![](./media/app-insights-windows-usage/appinsights-d28-events.png)


イベントには文字列や数値のプロパティを追加できます。


    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"Game", currentGame.Name}, {"Difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("GameOver", properties, measurements);


任意のデータをクリックすると、自身で定義したプロパティを含め、その詳細なプロパティが表示されます。


![](./media/app-insights-windows-usage/appinsights-d29-eventProps.png)

参照してください [API リファレンス][api] の詳細については、カスタム イベントです。

## セッション

セッションは、 Application Insights の基本的な概念であり、クラッシュやユーザーがコード化したカスタム イベントなどのすべてのテレメトリ イベントを特定のユーザーセッションに関連付けます。 

デバイスの特性や地理的位置、オペレーティング システムなど、各セッションについての豊富なコンテキスト情報が収集されます。

 [問題を診断する][diagnostic], 、セッションで、問題の発生など、すべての要求とイベント、例外またはログに記録されたトレースに関連するすべてのテレメトリを見つけることができます。

セッションは、デバイス、オペレーティング システム、場所などのコンテキストの人気度について詳細な情報を提供します。 たとえば、デバイスでグループ化されたセッション数を表示することで、ページ ビューをカウントするよりも、アプリでそのデバイスが使用される頻度をより正確に把握することができます。 これはデバイス固有の問題の優先順位を決定する便利なインプットになります。


#### セッションとは何ですか。

セッションは、ユーザーとアプリケーション間の単一のエンカウンターを表します。 最も単純な形式のセッションは、ユーザーがアプリを起動してから終了するまでとなります。 モバイル アプリの場合、セッションが終了 (背景ページに移動)、アプリが中断された場合に 20 秒以上にします。 アプリが再開される場合は、新しいセッションが開始されます。 Naturally, a user may have multiple sessions in a day, or even in a single hour. 

**セッションの期間** のセッションの最初と最後のテレメトリ項目の間の時間範囲を表す指標です。 (It doesn't include the timeout period.)


**セッション数** 、一定の間隔では、この間隔中にアクティビティとの一意のセッション数として定義します。 過去 1 週間の日次セッション数などの長い時間範囲を確認する場合は、通常、セッションの合計数と同じです。 

ただし、1 時間ごとなどの短い時間範囲を確認する場合、複数の時間にわたる長いセッションは、セッションがアクティブな状態だった時間ごとにカウントされます。 

## ユーザーとユーザー数

各ユーザーのセッションは、アプリを使用するときに生成され、デバイスのローカル ストレージに保持される一意のユーザー ID に関連付けられます。 複数のデバイスを使用しているユーザーは複数回カウントされます。

 **ユーザー カウント** この間隔中に記録されたアクティビティと、一定の間隔でメトリックを一意のユーザーの数として定義されます。 その結果、時間範囲を1 時間未満に設定した場合、長いセッションを持つユーザーは複数回カウントされることがあります。

**New Users** counts the users whose first sessions with the app occurred during this interval. 


## <a name="debug"></a>デバッグ モードとリリース モードの比較

#### デバッグ

デバッグ モードでビルドする場合、イベントは生成されるとすぐに送信されます。 インターネット接続が切断され、接続が復旧する前にアプリを終了した場合、オフラインのテレメトリは破棄されます。

#### リリース

リリース構成でビルドする場合、イベントはデバイスに格納され、アプリケーション再開時に送信されます。 データは、アプリケーションの初回使用時にも送信されます。 起動時にインターネット接続されていない場合は、前のテレメトリだけでなく、現在のライフサイクルのテレメトリも格納され、次の再開時に送信されます。

## <a name="next"></a>次のステップ

[ユーザーを知る][knowUsers]

[メトリックス エクスプローラーの詳細情報][metrics]


[トラブルシューティング][qna]




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[windows]: app-insights-windows-get-started.md


 

