<properties 
    pageTitle="Application Insights による Web アプリケーションの利用状況の分析" 
    description="Application Insights による Web アプリの利用状況分析の概要" 
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


# Application Insights による Web アプリケーションの利用状況の分析

ユーザーによるアプリケーションの使用状況を把握することで、ユーザーにとって最も重要なシナリオに重点を置いて開発作業を行うことができ、実現するのが簡単またはより困難であるとユーザーが捉えている目標を理解することができます。

Visual Studio Application Insights は、2 つのレベルの使用状況の追跡を提供します。

* **ユーザー、セッション、およびページ ビュー データ** - 既定で提供されています。
* **カスタム テレメトリ** -する [[api] のコードを記述][api] 、アプリのユーザー エクスペリエンスを使用してユーザーを追跡します。

## 設定

Application Insights リソースを開く、 [Azure ポータル](https://portal.azure.com), 空ブラウザー ページ読み込みグラフをクリックし、セットアップの指示に従って、します。

[詳細については](app-insights-javascript.md)


## Web アプリケーションの人気度を調べる

サインイン、 [Azure ポータルの ][portal], 、アプリケーションのリソースを参照し、[使用状況] をクリックします。

![](./media/app-insights-web-track-usage/14-usage.png)

* **ユーザー:** グラフの時間範囲内で変化する別個のアクティブ ユーザーの数。
* **セッション:** アクティブなセッションの数
* **ページ ビュー**: trackPageView() の呼び出し数をカウントします。通常、各 Web ページで 1 回呼び出されます。

グラフをクリックすると、詳細が表示されます。 グラフの時間範囲を変更できます。

### ユーザーの居住地を調べる

[使用状況] ブレードで [ユーザー] グラフをクリックして詳細を表示します。

![[使用状況] ブレードで ](./media/app-insights-web-track-usage/02-sessions.png)

### 使用されているブラウザーまたはオペレーティング システムを調べる

プロパティ (ブラウザー、オペレーティング システム、市区町村など) に基づいてデータをグループ化 (分割) できます。

![1 つのメトリックを表示するグラフを選択します。](./media/app-insights-web-track-usage/03-browsers.png)


## セッション

セッションは、 Application Insights の基本的な概念であり、要求、ページ ビュー、例外、ユーザーがコード化したカスタム イベントなどのすべてのテレメトリ イベントを特定のユーザーセッションに関連付けます。

デバイスの特性や地理的位置、オペレーティング システムなど、各セッションについての豊富なコンテキスト情報が収集されます。

クライアントとサーバーの両方をインストルメント化する場合 ([ASP.NET][greenbrown] または [J2EE ][java])、Sdk は、両方の側でイベントを関連付けられるように、クライアントとサーバー間のセッション id に反映されます。

[[診断] の問題の診断][diagnostic], 、セッションで、問題の発生など、すべての要求とイベント、例外またはログに記録されたトレースに関連するすべてのテレメトリを見つけることができます。

セッションは、デバイス、オペレーティング システム、場所などのコンテキストの人気度について詳細な情報を提供します。 たとえば、デバイスでグループ化されたセッション数を表示することで、ページ ビューをカウントするよりも、アプリでそのデバイスが使用される頻度をより正確に把握することができます。 これはデバイス固有の問題の優先順位を決定する便利なインプットになります。


#### セッションとは何ですか。

セッションは、ユーザーとアプリケーション間の単一のエンカウンターを表します。 最も単純な形式のセッションは、ユーザーがアプリを起動してから終了するまでとなります。 Web アプリの場合、既定では、セッションは、非アクティブな状態が 30 分続いた場合、またはアクティビティが 24 時間続いた場合に終了します。

これらの既定値は、コード スニペットを編集して変更できます。

    <script type="text/javascript">
        var appInsights= ... { ... }({
            instrumentationKey: "...",
            sessionRenewalMs: 3600000,
            sessionExpirationMs: 172800000
        });

* `sessionRenewalMs` : 時間 (ミリ秒単位) のユーザーの非アクティブなセッションを期限切れにします。 既定値: 30 分。
* `sessionExpirationMs` : セッションの最大長さ (ミリ秒単位)。 ユーザーはこの時間以降もアクティブなままである場合は、別のセッションがカウントされます。 既定値: 24 時間。

**セッション期間** は、 [メトリック ][metrics] セッションの最初と最後のテレメトリ項目の時間範囲を記録します。 (タイムアウト期間は含まれません)。

特定の間隔の**セッション数**は、この間隔でのアクティビティとの一意のセッション数として定義されます。 過去 1 週間の日次セッション数などの長い時間範囲を確認する場合は、通常、セッションの合計数と同じです。

ただし、1 時間ごとなどの短い時間範囲を確認する場合、複数の時間にわたる長いセッションは、セッションがアクティブな状態だった時間ごとにカウントされます。

## ユーザーとユーザー数

各ユーザーのセッションは、一意のユーザー ID に関連付けられます。

既定では、ユーザーは、Cookie を配置することによって識別されます。 複数のブラウザーやデバイスを使用しているユーザーは複数回カウントされます。 (が [認証されたユーザー](#authenticated-users)


特定の間隔の**ユーザー数**メトリックは、この間隔の間に記録されたアクティビティとの一意のユーザー数として定義されます。 その結果、時間範囲を1 時間未満に設定した場合、長いセッションを持つユーザーは複数回カウントされることがあります。

**新しいユーザー**は、最初のセッションに、この間隔の間に発生したアプリを持つユーザーをカウントします。 Cookie によってユーザーをカウントする既定のメソッドを使用した場合、Cookie をクリアするか、新しいデバイスやブラウザーを使用して最初にアプリにアクセスしているユーザーも含まれます。
![[使用状況] ブレードで ](./media/app-insights-web-track-usage/031-dual.png)

### 認証されたユーザー

ユーザーがサインインできる Web アプリの場合、Application Insights に一意のユーザー識別子を提供することで、より正確な数値を取得できます。 このユーザー識別子は、ユーザーの名前である必要も、アプリで使用しているのと同じ ID である必要もありません。 アプリでユーザーが識別されたら、次のコードを使用します。


*クライアント側の JavaScript*

      appInsights.setAuthenticatedUserContext(userId);

アカウントにアプリのグループ ユーザーがある場合、アカウントの識別子も渡すことができます。

      appInsights.setAuthenticatedUserContext(userId, accountId);

ユーザーとアカウント id には、空白または文字 `,、; = |`


[メトリックス エクスプ ローラー](app-insights-metrics-explorer.md), のグラフを作成する **Authenticated Users** と **アカウント**します。

## 合成トラフィック

合成トラフィックには、可用性とロード テスト、検索エンジンのクローラーやその他のエージェントからの要求が含まれています。

Application Insights は、合成トラフィックを自動的に判定して分類したうえで、適切にそのマーキングを試みます。 ほとんどの場合、合成トラフィックは JavaScript SDK を呼び出しません。そのため、このアクティビティはユーザーとセッションのカウントから除外されます。

ただし、Application Insights の [web テストの ][availability], ユーザー id が自動的に設定に基づいて POP の場所、およびセッション id は、テストの実行 id に基づいて設定されます。 既定のレポートでは、合成のトラフィックは既定により、フィルター処理され、これらのユーザーとセッションは除外されます。 ただし、合成トラフィックが含まれる場合は、全体的なユーザーとセッション数はわずかに増加する可能性があります。

## ページ利用状況

ページ ビュー グラフをクリックすると、その拡大バージョンと、最も人気のあるページの内訳が表示されます。


![概要ブレードで、](./media/app-insights-web-track-usage/05-games.png)

上の例はゲーム Web サイトの情報を示しています。 この情報から、次のことがすぐにわかります。

* 利用状況はこの 1 週間改善されていません。 おそらく、検索エンジンの最適化について検討する必要があります。
* ホーム ページよりもゲーム ページの閲覧人数がはるかに少なくなっています。 アクセスした人がゲームをプレイしたくなるような魅力なホーム ページにしましょう。
* "Crossword" は最も人気のあるゲームです。 このゲームに優先的に新しいアイデアと改良を加える必要があります。

## カスタムの追跡

それぞれのゲームを別個の Web ページで実装する代わりに、ほとんどの機能が Web ページの JavaScript としてコーディングされた同じ単一ページ アプリにこれらのゲームをすべてリファクタリングするとします。 これにより、ユーザーは、ゲームをすばやく切り替えたり、1 ページ上に複数のゲームを表示したりできます。

ただし、Application Insights では、各ゲームが開かれた回数をログに記録する際、ゲームが個別の Web ページにある場合とまったく同じ方法で行います。 これを実現するのは簡単です。新しい "ページ" が開かれたことを記録する JavaScript コードにテレメトリ モジュールの呼び出しを挿入するだけです。

    appInsights.trackPageView(game.Name);

## カスタム イベント

カスタム イベントを使用できます。 カスタム イベントは、デバイスのアプリ、Web ページ、または Web サーバーから送信できます。

*JavaScript*

    appInsights.trackEvent("GameEnd");

*C#*

    var tc = new Microsoft.ApplicationInsights.TelemetryClient(); 
    tc.TrackEvent("GameEnd");

*VB*

    Dim tc = New Microsoft.ApplicationInsights.TelemetryClient()
    tc.TrackEvent("GameEnd")

最もよく使用されるカスタム イベントは、概要ブレードに示されます。

![概要ブレードで、下へスクロールし、](./media/app-insights-web-track-usage/04-events.png)

テーブルの先頭をクリックすると、イベントの合計数が表示されます。 イベント名などのさまざまな属性によって、グラフを分割することができます。

![Select a chart that shows just one metric. Switch on Grouping. Choose a property. Not all properties are available.](./media/app-insights-web-track-usage/06-eventsSegment.png)

タイムラインには、変化を他のメトリックやイベントに関連付けることができるという便利な機能があります。 たとえば、プレイするゲームが多くなると、破棄されたゲームも多くなると予想されます。 しかし、破棄されたゲーム数の増加率には偏りがあります。そこで、高い負荷が原因となってユーザーが許容できない問題が発生しているかどうかを確認してみましょう。

## 特定のイベントのドリルダウン

一般的なセッションの状況についてより深く理解するためには、特定の種類のイベントが含まれている特定のユーザー セッションに注目します。

この例では、ユーザーが実際にゲームを開始することなくログアウトした場合に呼び出されるカスタム イベント "NoGame" をコーディングしました。 なぜユーザーはゲームを開始せずにログアウトするのでしょうか。 おそらく、発生した特定のイベントをいくつかドリルダウンすると、手がかりが得られます。

アプリから受信したカスタム イベントは、概要ブレードに名前別に表示されます。


![概要ブレードで、いずれかのカスタム イベントの種類をクリックします。](./media/app-insights-web-track-usage/07-clickEvent.png)

目的のイベントをクリックし、最近の特定の発生箇所を選択します。


![概要グラフの下の一覧で、イベントをクリックします](./media/app-insights-web-track-usage/08-searchEvents.png)

ここで、この特定の NoGame イベントが発生したセッションのすべてのテレメトリを見てみましょう。


![[セッションのすべてのテレメトリ] をクリックします](./media/app-insights-web-track-usage/09-relatedTelemetry.png)

例外はありません。ここから、ユーザーのプレイを妨げるような障害が発生していないことがわかります。

このセッションのページ ビューを除くすべての種類のテレメトリをフィルターで除外できます。


![](./media/app-insights-web-track-usage/10-filter.png)

ここから、このユーザーは最新のスコアを確認するためにのみログオンしたことがわかります。 スコアの確認を簡単にするユーザー ストーリーの作成を検討することが必要である可能性があります  (加えて、この特定のストーリーが発生したことを報告するカスタム イベントを実装する必要があります)。

## プロパティを使用してデータをフィルター処理、検索、および分割する

イベントには任意のタグと数値をアタッチできます。


*クライアント側の JavaScript*

```JavaScript

    appInsights.trackEvent("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric measurements:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
    );
```

*サーバー側の C#*

```C#

    // Set up some properties:
    var properties = new Dictionary <string, string> 
        {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
        {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);
```

*サーバー側の VB*

```VB

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)
```

次のようにページ ビューを同じ方法でプロパティに結び付けます。

*クライアント側の JavaScript*

```JS

    appInsights.trackPageView("Win", 
        url,
        {Game: currentGame.Name}, 
        {Score: currentGame.Score});
```

診断検索で、イベントのそれぞれの発生箇所をクリックしてプロパティを表示します。


![イベントの一覧で、イベントを開き、](./media/app-insights-web-track-usage/11-details.png)

[検索] ボックスを使用して、特定のプロパティ値を持つイベントを表示します。


![[検索] ボックスに値を入力します](./media/app-insights-web-track-usage/12-searchEvents.png)


## A | B テスト

ある機能のどちらのバージョンが成功するかわからない場合は、その両方をリリースして、それぞれを異なるユーザーが利用できるようにします。 各バージョンの成功の度合いを測定してから、統合したバージョンに移行します。

この手法では、アプリの各バージョンから送信されるすべてのテレメトリに異なるタグをアタッチします。 これは、アクティブな TelemetryContext のプロパティを定義することで実行できます。 このような既定のプロパティは、カスタム メッセージだけでなく、標準のテレメトリも同様に、アプリケーションから送信されるすべてのテレメトリ メッセージに追加されます。

Application Insights ポータルでは、タグに基づいてデータをフィルター選択およびグループ化 (分割) して、異なるバージョンを比較できます。

*サーバー側の C#*

```C#

    using Microsoft.ApplicationInsights.DataContracts;

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame");
```

*サーバー側の VB*

```VB

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame")
```

個々のテレメトリは、既定値を無視することができます。

すべての新しい TelemetryClients が自動的にコンテキストを使用できるように、汎用の初期化子を設定できます。

```C#

    // Telemetry initializer class
    public class MyTelemetryInitializer : IContextInitializer
    {
        public void Initialize (TelemetryContext context)
        {
            context.Properties["AppVersion"] = "v2.1";
        }
    }
```

アプリ初期化子 (Global.asax.cs など) 内:

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyTelemetryInitializer());
    }
```


## ビルド、評価、学習

分析は、単に問題の解決に役立つだけのものではなく、開発サイクルの重要な部分を構成します。 いくつかのヒントを次に示します。

* アプリケーションの主なメトリックを決定する。 できるだけ多くのユーザーにプレイしてもらいたいのか、それとも、少数のユーザーに十分満足してもらえればいいのか。 アクセス数と売り上げのどちらを最重要視するのか、を検討します。
* 各ストーリーの評価を計画する。 新しいユーザー ストーリーや機能の概要を作成する場合、または既存のユーザー ストーリーや機能の更新を計画する場合は、変更の成功を評価する方法について必ず検討してください。 コーディングの開始前に、"成功した場合に、メトリックにどのような影響があるか。 新しいイベントを追跡する必要があるか」 を考えます。
当然ながら、機能が実装されたら、分析に目を向け、その結果に基づいて行動するようにしてください。
* 他のメトリックを重要なメトリックに関連付ける。 たとえば、"お気に入り" 機能を追加した場合は、ユーザーがお気に入りを追加する頻度を知りたくなります。 しかし、ユーザーがお気に入りに再びアクセスする頻度を知る方が、おそらくより興味深い結果が得られるでしょう。 さらに、最も重要なのは、お気に入りを使用する顧客が最終的により多くの製品を購入してくれるかどうかです。
* カナリア テストを実行する。 新しい機能を一部のユーザーのみに表示できるようにする機能スイッチをセットアップします。 Application Insights を使用して、その新しい機能が予想どおりに使用されているかどうかを確認してください。 調整後に、対象ユーザーの範囲を広げてリリースします。
* ユーザーと会話をする。 分析は、それだけでは不十分ですが、良好な顧客関係を保つための補完的役割を果たします。


## 参照

* [API の使用 - 概要 ][api]
* [JavaScript API リファレンス](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)

## ビデオ

> [AZURE.VIDEO usage-monitoring-application-insights]





[api]: app-insights-api-custom-events-metrics.md 
[availability]: app-insights-monitor-web-app-availability.md 
[client]: app-insights-javascript.md 
[diagnostic]: app-insights-diagnostic-search.md 
[greenbrown]: app-insights-asp-net.md 
[java]: app-insights-java-get-started.md 
[metrics]: app-insights-metrics-explorer.md 
[portal]: http://portal.azure.com/ 
[windows]: app-insights-windows-get-started.md 

