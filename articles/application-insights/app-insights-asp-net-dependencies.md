<properties 
    pageTitle="Application Insights での依存関係の追跡" 
    description="オンプレミスまたは Microsoft Azure Web アプリケーションの使用状況、可用性、パフォーマンスを Application Insights で分析します。" 
    services="application-insights" 
    documentationCenter=".net"
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


# Application Insights の設定: 依存関係の追跡


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]



A *依存関係* アプリによって呼び出される外部コンポーネントです。 一般的には、HTTP を使用して呼び出されるサービス、またはデータベース、あるいはファイル システムです。 Visual Studio Application Insights では、アプリケーションが依存関係を待機する期間や、依存関係の呼び出しが失敗する頻度を簡単に確認できます。

![サンプルのグラフ](./media/app-insights-asp-net-dependencies/10-intro.png)

既定の依存関係モニターは、現在これらの種類の依存関係への呼び出しを報告します。

* ASP.NET
 * SQL データベース
 * HTTP ベースのバインドを使用する ASP.NET Web および WCF サービス
 * ローカルまたはリモートの HTTP 呼び出し
 * Azure DocumentDb、テーブル、Blob Storage、およびキュー
* Java
 * 使用してデータベースへの呼び出し、 [JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/) MySQL、SQL Server、PostgreSQL、SQLite などのドライバーです。

使用して他の依存関係を監視するための独自の SDK 呼び出しを記述する、 [TrackDependency API](app-insights-api-custom-events-metrics.md#track-dependency)します。


## 依存関係の監視をセットアップするには

必要な [Microsoft Azure](http://azure.com) サブスクリプションです。

### アプリが IIS サーバーで実行されている場合

1. IIS Web サーバーで、管理者の資格情報を使用してログインします。
2. ダウンロードし、実行、 [Status Monitor インストーラー](http://go.microsoft.com/fwlink/?LinkId=506648)します。
4. インストール ウィザードで、Microsoft Azure にサインインします。

    ![Microsoft アカウントの資格情報で Azure にサインインする](./media/app-insights-asp-net-dependencies/appinsights-035-signin.png)

    *接続エラーでしょうか。 参照してください [トラブルシューティング](#troubleshooting)します。*

5. 監視するインストール済みの Web アプリケーションまたは Web サイトを選択し、Application Insights ポータルで結果を表示するときに使用するリソースを構成します。

    ![アプリとリソースを選択します。](./media/app-insights-asp-net-dependencies/appinsights-036-configAIC.png)

    通常、新しいリソースを構成する」を選択し、 [リソース グループ][roles]します。

    既に設定した場合、それ以外の場合、既存のリソースを使用して [web テスト][availability] 、サイトのまたは [web クライアントの監視][client]します。

6. IIS を再起動します。

    ![ダイアログの上部にある [再起動] を選択します。](./media/app-insights-asp-net-dependencies/appinsights-036-restart.png)

    少しの間、Web サービスが中断されます。

6. 監視対象の Web アプリに ApplicationInsights.config が挿入されます。

    ![Web アプリのコード ファイルの横にある、.config ファイルを見つけます。](./media/app-insights-asp-net-dependencies/appinsights-034-aiconfig.png)

   これ以外にも、web.config にいくつかの変更が加えられます。

#### 後で (再) 構成する

ウィザードを完了した後、いつでも必要に応じてエージェントを再構成できます。 この方法は、エージェントをインストールした際の初期設定に問題があった場合にも使用できます。

![タスク バーの Application Insights のアイコンをクリックする](./media/app-insights-asp-net-dependencies/appinsights-033-aicRunning.png)


### アプリが Azure Web アプリとして実行されている場合

Azure の Web アプリのコントロール パネルで、Application Insights 拡張機能を追加します。

![Web アプリで、[設定]、[拡張機能]、[追加]、[Application Insights] の順に選択する](./media/app-insights-asp-net-dependencies/05-extend.png)


### Azure Cloud Services プロジェクトの場合

[スクリプトを web ロールとワーカー ロールに追加](app-insights-cloudservices.md)します。

## <a name="diagnosis"></a> 依存関係のパフォーマンスの問題の診断

サーバーでの要求のパフォーマンスを評価するには、次のようにします。

![Application Insights 内のアプリケーションの [概要] ページで、[パフォーマンス] タイルをクリックします](./media/app-insights-asp-net-dependencies/01-performance.png)

下方向へスクロールして、要求のグリッドを確認します。

![平均およびカウントを持つ要求の一覧](./media/app-insights-asp-net-dependencies/02-reqs.png)

上部にあるものは、非常に長い時間がかかります。 どこに時間がかかるか見つけられるか見てみましょう。

個々の要求イベントを表示するには、その行をクリックします。


![要求回数の一覧](./media/app-insights-asp-net-dependencies/03-instances.png)

実行時間の長いインスタンスがあればそれをクリックし、さらに検査します。

> [AZURE.NOTE] 1 ビット、インスタンスの選択が下にスクロールします。 パイプラインでの待機時間は、上部のインスタンスのデータが不完全であることを示す場合があります。

この要求に関連したリモート依存関係呼び出しまで下にスクロールします。

![リモートの依存関係への呼び出しを見つけ、異常な期間を特定します](./media/app-insights-asp-net-dependencies/04-dependencies.png)

この要求に使われた時間のほとんどが、ローカル サービスへの呼び出しに費やされたように見えます。 

さらに情報を得るには、その行をクリックします。


![そのリモートの依存関係をクリックし、問題の原因を特定します](./media/app-insights-asp-net-dependencies/05-detail.png)

詳細には、問題の診断に必要な情報が含まれています。



## エラー

失敗した要求がある場合は、グラフをクリックします。

![失敗した要求のグラフをクリックします。](./media/app-insights-asp-net-dependencies/06-fail.png)

要求の種類と要求のインスタンスをクリックし、リモートの依存関係への失敗した呼び出しを見つけます。


![要求の種類をクリックし、インスタンスをクリックして同じインスタンスの異なるビューを取得し、それをクリックして例外の詳細を取得します。](./media/app-insights-asp-net-dependencies/07-faildetail.png)


## カスタム依存関係の追跡

標準の依存関係追跡モジュールは、外部の依存関係 (データベース、REST API など) を自動的に検出しますが、 同じように扱える追加のコンポーネントが必要になる可能性もあります。 

同じを使用して、依存関係情報を送信するコードを記述できます [TrackDependency API](app-insights-api-custom-events-metrics.md#track-dependency) 標準のモジュールで使用されています。

たとえば、自分で記述していないアセンブリを使ってコードを作成する場合、それに対するすべての呼び出しを測定し、何が応答時間に貢献するかを知ることができます。 このデータを Application Insights 内の依存関係グラフに表示するには、データを `TrackDependency` を使用して送信します。

```C#

            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

標準の依存関係追跡モジュールをオフにする場合は、DependencyTrackingTelemetryModule への参照を削除する [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)します。

## 次のステップ

- [例外](../article/application-insights/app-insights-asp-net-exception-mvc.md#selector1)
- [ユーザーとページのデータ](../article/application-insights/app-insights-asp-net-client.md#selector1)
- [可用性](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-asp-net-dependencies.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 
