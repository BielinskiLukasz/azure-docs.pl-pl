<properties 
    pageTitle="Application Insights を使用したアプリケーションの状態と利用状況の監視" 
    description="Application Insights の使用を開始します。 オンプレミスの、または Microsoft Azure アプリケーションの使用状況、可用性、パフォーマンスを分析します。" 
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
 
# Web アプリケーションのパフォーマンスを監視する

*Application Insights はプレビュー段階です。*


アプリケーションが正常に実行されていること、および障害がすぐに検出されることを確認します。 [Application Insights][start] はパフォーマンスの問題や例外を通知し、検索したり、根本的原因を診断します。

Application Insights は、Java と ASP.NET の Web アプリケーションとサービス、WCF サービスの両方を監視できます。 それらは、オンプレミスで、仮想マシン上で、または Microsoft Azure Web サイトとしてホストできます。 

クライアント側で、Application Insights は、Web ページと、iOS、Android、Windows ストア アプリを含むさまざまなデバイスからテレメトリを取得できます。


## <a name="setup"></a>パフォーマンス モニターの設定

プロジェクトに Application Insights を追加していない場合 (つまり、ApplicationInsights.config がない場合)、以下のいずれかの方法で開始します。

* [ASP.NET Web アプリ](app-insights-asp-net.md)
 * [例外の監視を追加する](app-insights-asp-net-exceptions.md)
 * [依存関係の監視を追加する](app-insights-monitor-performance-live-website-now.md)
* [J2EE Web アプリ](app-insights-java-get-started.md)
 * [依存関係の監視を追加する](app-insights-java-agent.md)


## <a name="view"></a>パフォーマンス メトリックの監視

 [Azure ポータル](https://portal.azure.com), 、アプリケーションを設定する Application Insights リソースを参照します。 概要ブレードに、基本的なパフォーマンス データが表示されます。



詳細を表示したり、より長い期間の結果を表示したりするには、グラフをクリックします。 たとえば、[要求] タイルをクリックして、次の時間範囲を選択します。


![クリックしてより多くのデータを表示し、時間範囲を選択する](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

グラフをクリックして表示するメトリックを選択するか、新しいグラフを追加してそのメトリックを選択します。

![グラフをクリックし、メトリックを選択する](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [AZURE.NOTE] **すべてのメトリックをオフに** には、使用可能なすべての選択項目を参照してください。 メトリックはグループに分けられ、グループ内のあるメンバーが選択されると、そのグループのメンバーのみが表示されます。


## <a name="metrics"></a>どのような意味がありますか? パフォーマンス タイルとレポート

多様なパフォーマンス メトリックを取得できます。 まず、アプリケーション ブレードに既定で表示されるメトリックから始めます。


### 要求

指定の期間に受け取った HTTP 要求の数です。 これを他のレポートにおける結果と比較して、負荷が変化するとアプリケーションの動作がどのように変わるかを確認します。

HTTP 要求には、ページ、データ、画像に関するすべての GET 要求または POST 要求が含まれます。

タイルをクリックして、特定の URL のカウントを取得します。

### 平均応答時間

アプリケーションに入力した Web 要求と返された応答の間の時間を測定します。

各ポイントは移動平均を示しています。 多くの要求がある場合、グラフ内の明確なピークや下落以外に、平均から逸脱している要求が存在することがあります。

異常なピークを探します。 通常、要求の数が多くなれば応答時間も長くなることが想定されます。 上昇の形が不均衡な場合、アプリケーションがリソース制限 (CPU、および CPU が使用するサービス機能など) に達した可能性があります。

タイルをクリックして、特定の URL の時間を取得します。

![](./media/app-insights-web-monitor-performance/appinsights-42reqs.png)


### 最も遅い要求

![](./media/app-insights-web-monitor-performance/appinsights-44slowest.png)

パフォーマンス チューニングが必要となる可能性がある要求を示します。


### 失敗した要求

![](./media/app-insights-web-monitor-performance/appinsights-46failed.png)

受信できない例外がスローされた要求の数。

タイルをクリックして、特定の障害の詳細を表示したり、個別の要求を選択してその詳細を表示したりします。 

障害の代表的な例だけが、それぞれの検査用に保持されます。

### その他のメトリック

表示できる他のメトリックを確認するには、グラフをクリックし、すべてのメトリックを選択解除してすべてのメトリック セットを表示します。 (i) をクリックし、各メトリック定義を表示します。

![すべてのメトリックを選択解除し、全セットを表示する](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)


いずれかのメトリックを選択すると、同じグラフ上に表示できない他のメトリックは無効になります。

## システム パフォーマンス カウンター

いくつかのメトリックから選択できます [パフォーマンス カウンター](http://www.codeproject.com/Articles/8590/An-Introduction-To-Performance-Counters)します。 Windows にはさまざまなパフォーマンス カウンターが用意されていますが、ユーザーが独自に定義することもできます。

この例では、既定で使用できるパフォーマンス カウンターを表示します。 ある [ごとにグラフを追加](app-insights-metrics-explorer.md#editing-charts-and-grids) カウンターごとに組織図に名前付き [をお気に入りとして保存](app-insights-metrics-explorer.md#editing-charts-and-grids):

![](./media/app-insights-web-monitor-performance/sys-perf.png)


必要なカウンターがプロパティ一覧にない場合は、SDK の収集セットにそれらを追加できます。 ApplicationInsights.config を開き、パフォーマンス コレクター ディレクティブを次のように編集します。

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCollector.PerformanceCollectorModule, Microsoft.ApplicationInsights.Extensibility.PerfCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(electronics)\# Items Sold" ReportAs="Item sales"/>
      </Counters>
    </Add>

形式は `\Category(instance)\Counter"` です。インスタンスが存在しないカテゴリの場合は、単に `\Category\Counter` です。 システムで使用できるカウンターについて説明を確認するを参照してください [に関するこの紹介](http://www.codeproject.com/Articles/8590/An-Introduction-To-Performance-Counters)します。

カウンターの名前に、英字、丸かっこ、スラッシュ、ハイフン、アンダー スコア、スペース、ドットの文字以外が含まれている場合は、`ReportAs` が必要です。

インスタンスを指定した場合は、報告されるメトリックの "CounterInstanceName" プロパティとして収集されます。

必要に応じて、同じ効果を持つ次のようなコードを記述できます。

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(electronics)\# Items Sold", "Items sold"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);

さらに、システム パフォーマンス カウンターを収集し、それらを Application Insights にプッシュする場合は、次のスニペットを使用できます。

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\.NET CLR Memory([replace-with-application-process-name])\# GC Handles", "GC Handles")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);

### 例外の数

*例外レートと例外のメトリックの違いは何ですか*

* *例外レート* システム パフォーマンス カウンターです。 CLR ではスローされた処理済みおよび未処理の例外をすべてカウントし、特定のサンプリング時間間隔での合計をその時間間隔の長さで除算します。 Application Insights SDK では、この結果を収集し、ポータルに送信します。
* *例外* 、ポータルで、グラフのサンプリング間隔で受信した TrackException レポートのカウントです。 TrackException 呼び出しをコードで設定して、すべてを記述した処理済みの例外だけが含まれている [未処理の例外](app-insights-asp-net-exceptions.md)します。 

## アラートの設定

すべてのメトリックの異常な値を電子メールで通知するには、アラートを追加します。 アカウント管理者または特定の電子メール アドレスのいずれかに電子メールを送信することを選択できます。

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

その他のプロパティの前に、リソースを設定します。 パフォーマンスまたは使用状況のメトリックにアラートを設定する場合、Web テスト リソースは選択しないでください。

しきい値を入力するように求められたら、単位に注意してください。

*[アラートの追加] ボタンが表示されません。* -これは、グループ アカウントが読み取り専用アクセスがあるでしょうか。 アカウント管理者にご確認ください。

## <a name="diagnosis"></a>問題の診断

パフォーマンス上の問題を検出して診断するためのいくつかのヒントを以下に記します。

* セットアップ [web テスト][availability] で web サイトがダウンしたか応答速度が低下したり正しくない場合に警告が発生します。 
* 要求の数を他のメトリックと比較し、障害や応答速度の低下が負荷と関連しているかどうかを確認します。
* [挿入し、トレース ステートメントの検索][diagnostic] 問題の特定に役立つをコードにします。

## <a name="next"></a>次のステップ

[Web テスト][availability] -web 要求を世界中から一定の間隔でアプリケーションに送信します。

[キャプチャして診断トレースを検索][diagnostic] - トレース呼び出しを挿入し、問題を特定の結果を詳しく調べています。

[使用状況の追跡][usage] -ユーザーがアプリケーションを使用する方法について説明します。

[トラブルシューティング][qna] - および Q & A

## ビデオ

[AZURE.VIDEO performance-monitoring-application-insights]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md

 


