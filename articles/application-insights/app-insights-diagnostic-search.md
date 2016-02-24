<properties 
    pageTitle="診断検索の使用" 
    description="個々のイベント、要求、ログ トレースを検索し、フィルター選択します。" 
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
    ms.date="11/23/2015" 
    ms.author="awills"/>
 
# Application Insights の診断検索の使用

Diagnostic Search is the blade in <bpt id="p1">[</bpt>Application Insights<ept id="p1">][start]</ept> that you use to find and explore individual telemetry items, such as page views, exceptions, or web requests. 診断検索を使用すると、作成したログ トレースやイベントを表示できます。

## 診断検索が表示されるタイミング

診断検索は、明示的に開くことができます。

![Open diagnostic search](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)


診断検索は、特定のグラフやグリッド項目をクリックしたときにも表示されます。 この場合、選択した項目の種類に合わせてフィルターが事前設定されます。 

たとえば、アプリケーションが Web サービスの場合は、概要ブレードに要求の量のグラフが表示されます。 このグラフをクリックすると、各 URL に対する要求の数を示すより詳細なグラフが表示されます。 いずれかの行をクリックすると、その URL に対する個々の要求の一覧が表示されます。

![Open diagnostic search](./media/app-insights-diagnostic-search/07-open-from-filters.png)


診断検索の本体は、テレメトリ項目 (サーバー要求、ページ ビュー、お客様が作成したカスタム イベントなど) の一覧です。 一覧の上部には、一定期間に発生したイベントの数を示す概要グラフが表示されます。

イベントは通常、メトリック エクスプローラーに表示される前に診断検索に表示されます。 ブレードは周期的に自動で更新されますが、特定のイベントを待機している場合、[最新の情報に更新] をクリックできます。


> <ph id="ph1">[AZURE.NOTE]</ph> If your app generates a lot of telemetry (and you are using the ASP.NET SDK version 2.0.0-beta3 or later), the adaptive sampling module will automatically reduce the volume that is sent to the portal by sending only a representative fraction of events. ただし、同じ要求に関連するイベントはグループ単位で選択または選択解除されるので、関連するイベントごとに操作できます。 
> <bpt id="p1">[</bpt>Learn about sampling<ept id="p1">](app-insights-sampling.md)</ept>.


## 個々の項目の確認

任意のテレメトリ項目を選択すると、キー フィールドと関連項目が表示されます。 フィールドの完全なセットを表示するには、[...] をクリックします。 

![Open diagnostic search](./media/app-insights-diagnostic-search/10-detail.png)

フィールドの完全なセットを検索するには、ワイルドカードを含まない単純な文字列を使用します。 使用できるフィールドは、テレメトリの種類に依存します。

## イベントの種類のフィルター選択

[フィルター] ブレードを開き、表示するイベントの種類を選択します  (後でブレードを開いたときに表示されるフィルターを復元するには、[リセット] をクリックします)。


![[フィルター] を選択し、テレメトリの種類を選択します](./media/app-insights-diagnostic-search/02-filter-req.png)


イベントの種類は、次のとおりです。

* <bpt id="p1">**</bpt>Trace<ept id="p1">**</ept> - Diagnostic logs including TrackTrace,  log4Net, NLog, and System.Diagnostic.Trace calls.
* <bpt id="p1">**</bpt>Request<ept id="p1">**</ept> - HTTP requests received by your server application, including pages, scripts, images, style files and data. これらのイベントは、要求と応答の概要グラフの作成に使用されます。
* <bpt id="p1">**</bpt>Page View<ept id="p1">**</ept> - Telemetry sent by the web client, used to create page view reports. 
* <bpt id="p1">**</bpt>Custom Event<ept id="p1">**</ept> - If you inserted calls to TrackEvent() in order to <bpt id="p2">[</bpt>monitor usage<ept id="p2">][track]</ept>, you can search them here.
* <bpt id="p1">**</bpt>Exception<ept id="p1">**</ept> - Uncaught exceptions in the server, and those that you log by using TrackException().

## プロパティの値に基づくフィルター選択

プロパティの値に基づいてイベントをフィルター選択できます。 使用可能なプロパティは、選択したイベントの種類によって異なります。 

たとえば、特定の応答コードを持つ要求を選択できます。

![プロパティを展開し、値を選択します](./media/app-insights-diagnostic-search/03-response500.png)

特定のプロパティの値を選択しない場合、すべての値を選択するのと同じ意味になり、そのプロパティに基づくフィルターはオフになります。


### 検索の絞り込み

フィルター値の右側の値は、現在のフィルター選択されたセットに含まれるイベントの発生回数を表します。 

この例では、500 エラーの大部分が `Reports/Employees` 要求であることがわかります。

![プロパティを展開し、値を選択します](./media/app-insights-diagnostic-search/04-failingReq.png)

Additionally if you want to also see what other events were happening during this time, you can check <bpt id="p1">**</bpt>Include events with undefined properties<ept id="p1">**</ept>.

## Bot と Web テストのトラフィックの除外

Use the filter <bpt id="p1">**</bpt>Real or synthetic traffic<ept id="p1">**</ept> and check <bpt id="p2">**</bpt>Real<ept id="p2">**</ept>.

You can also filter by <bpt id="p1">**</bpt>Source of synthetic traffic<ept id="p1">**</ept>.

## 個々の発生の確認

この要求の名前をフィルター セットに追加して、そのイベントの個々の発生を確認できます。

![値を選択します](./media/app-insights-diagnostic-search/05-reqDetails.png)

要求イベントの場合、要求の処理中に発生した例外が詳細情報として表示されます。

例外をクリックすると、スタック トレースなど、詳細が表示されます。

![例外をクリックします](./media/app-insights-diagnostic-search/06-callStack.png)

## 同じプロパティを持つイベントの検索

同じプロパティ値を持つすべての項目を検索できます。

![プロパティを右クリックします](./media/app-insights-diagnostic-search/12-samevalue.png)

## メトリック値に基づく検索

応答時間が 5 秒を超えるすべての要求を取得します。  時間はティック単位で表されます。10,000 ティックが 1 ミリ秒です。

!["Response time":(threshold TO *)](./media/app-insights-diagnostic-search/11-responsetime.png)



## データの検索

すべてのプロパティ値について語句を検索できます。 This is particularly useful if you have written <bpt id="p1">[</bpt>custom events<ept id="p1">][track]</ept> with property values. 

時間範囲を設定することもできます。範囲が狭いほど、検索時間が短くなります。 

![Open diagnostic search](./media/app-insights-diagnostic-search/appinsights-311search.png)

部分文字列ではなく語句を検索することに注意してください。 語句とは、"."、"_" などのいくつかの句読文字を含む、英数字の文字列です。 次に例を示します。

語句|is <bpt id="p1">*</bpt>not<ept id="p1">*</ept> matched by|一致する
---|---|---
HomeController.About|about<br/>ホーム|h\*about<br/>home\*
IsLocal|local<br/>is<br/>\*local|isl\*<br/>islocal<br/>i\*l\*
New Delay|w d|new<br/>delay<br/>n\* AND d\*


使用できる検索表現を次に示します。

サンプル クエリ | 効果 
---|---
slow|日付範囲内でフィールドに "slow" という語句が含まれるすべてのイベントを検索します
database??|database01、databaseAB などに一致します<br/>? is not allowed at the start of a search term.
database*|database、database01、databaseNNNN に一致します<br/>* は、検索語句の先頭に使用できません
apple AND banana|両方の語句を含むイベントを検索します。 "and" ではなく大文字の "AND" を使用します。
apple OR banana<br/>apple banana|どちらかの語句を含むイベントを検索します。 Use "OR", not "or".&lt;/br/&gt;Short form.
apple NOT banana<br/>apple -banana|ある用語を含む一方で他方の用語を含まないイベントを検索します。<br/>短縮形。
app* AND banana -(grape pear)|論理演算子とかっこの使用。
"Metric": 0 TO 500<br/>"Metric" : 500 TO * | 値範囲内の指定された測定値を含むイベントを検索します。


## 検索条件の保存

必要なフィルターをすべて設定した後、この検索条件をお気に入りとして保存できます。 組織のアカウントで作業している場合は、これを他のチーム メンバーと共有するかどうかを選択できます。

![[お気に入り] をクリックし、名前を設定して [保存] をクリックします](./media/app-insights-diagnostic-search/08-favorite-save.png)


To see the search again, <bpt id="p1">**</bpt>go to the overview blade<ept id="p1">**</ept> and open Favorites:

![[お気に入り] タイル](./media/app-insights-diagnostic-search/09-favorite-get.png)

相対的な時間範囲を選択して保存した場合は、ブレードを開くたびに最新のデータが表示されます。 絶対的な時間範囲を選択して保存した場合は、毎回同じデータが表示されます。


## さらに多くのテレメトリを Application Insights に送信する

Application Insights SDK によって送信される標準のテレメトリに加えて、次の操作を実行できます。

* Capture log traces from your favorite logging framework in <bpt id="p1">[</bpt>.NET<ept id="p1">][netlogs]</ept> or <bpt id="p2">[</bpt>Java<ept id="p2">][javalogs]</ept>. これは、ログ トレースを検索し、ページ ビュー、例外、その他のイベントと関連付けることができることを意味します。 
* <bpt id="p1">[</bpt>Write code<ept id="p1">][track]</ept> to send custom events, page views, and exceptions. 

<bpt id="p1">[</bpt>Learn how to send logs and custom telemetry to Application Insights<ept id="p1">][trace]</ept>.


## <a name="questions"></a>Q & A

### <a name="limits"></a>保持されるデータの量はどのくらいですか

各アプリケーションで、1 秒あたり 500 イベントまでです。 イベントは 7 日間保持されます。

### サーバーの要求の POST データを表示するにはどうしたらよいですか

We don't log the POST data automatically, but you can use <bpt id="p1">[</bpt>TrackTrace or log calls<ept id="p1">][trace]</ept>. メッセージ パラメーターに POST データを格納します。 プロパティと同じ方法でメッセージをフィルター選択することはできませんが、サイズの制限が緩和されます。

## <a name="add"></a>次のステップ

* [Application Insights にログとカスタム テレメトリを送信する][trace]
* [可用性と応答性のテストの設定][availability]
* [トラブルシューティング][qna]



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[javalogs]: app-insights-java-trace-logs.md
[netlogs]: app-insights-asp-net-trace-logs.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md
[trace]: app-insights-search-diagnostic-logs.md
[track]: app-insights-spi-custom-events-metrics.md

 
