<properties 
    pageTitle="ApplicationInsights.config または .xml を使った Application Insights SDK の構成" 
    description="データ コレクション モジュールを有効または無効にし、パフォーマンス カウンターとその他のパラメーターを追加します" 
    services="application-insights"
    documentationCenter="" 
    authors="OlegAnaniev-MSFT"
    editor="alancameronwills" 
    manager="douge"/>
 
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/05/2015" 
    ms.author="awills"/>

# ApplicationInsights.config または .xml を使った Application Insights SDK の構成

Application Insights .NET SDK は、いくつかの NuGet パッケージで構成されます。 この 
[コア パッケージ](http://www.nuget.org/packages/Microsoft.ApplicationInsights) にテレメトリを送信するため、API を提供 
Application Insights します。 [その他のパッケージ](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) 提供 
製品利用統計情報 _モジュール_ と _初期化子_ のアプリケーションとそのコンテキストからテレメトリを自動的に追跡します。 別 
構成ファイルを調整することができますを有効にして無効を切り替えテレメトリ モジュールと初期化子、パラメーターを設定 
これらの一部です。

構成ファイルの名前は `ApplicationInsights.config` または `ApplicationInsights.xml`, の種類に応じて、
想定しています。 プロジェクトに自動的に追加時にする [ほとんどのバージョンの SDK をインストール][start]します。 Web アプリにも追加されます。 
によって [IIS サーバーに Status Monitor][redfield], 、Appplication Insights を選択した場合、または 
[Azure の web サイトまたは VM の拡張機能][azure]です。

コントロールを同等のファイルがない、 [web ページの SDK][client]します。

このドキュメントでは、構成ファイルの各セクション、SDK のコンポーネントの制御方法、それらのコンポーネントを読み込む NuGet パッケージについて説明します。

## テレメトリ モジュール (ASP.NET)

各テレメトリ モジュールは特定の種類のデータを回収し、コア API を利用してデータを送信します。 モジュールはさまざまな NuGet パッケージによりインストールされます。NuGet パッケージはまた、必要な行を .config ファイルに追加します。

各モジュールの構成ファイルにノードが存在します。 モジュールを無効にするには、ノードを削除するか、コメント アウトします。



### 依存関係の追跡

[依存関係の追跡](app-insights-dependencies.md) データベースとの外部サービスとデータベースに対してアプリが呼び出しに関する製品利用統計情報を収集します。 このモジュールを IIS サーバーで機能を許可するのには、する必要があります [Status Monitor をインストール][redfield]します。 Azure web アプリまたは Vm で使用する [Application Insights 拡張機能の選択][azure]します。

追跡コードを使用して、独自の依存関係を記述することも、 [TrackDependency API](app-insights-api-custom-events-metrics.md#track-dependency)します。


* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet パッケージ。

### パフォーマンス コレクター

[システムのパフォーマンス カウンターを収集](app-insights-web-monitor-performance.md#system-performance-counters) メモリおよびネットワークで IIS のインストール環境から負荷、CPU などです。 自分で設定したパフォーマンス カウンターなど、回収するカウンターを指定できます。

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet パッケージ。


### Application Insights 診断テレメトリ

`DiagnosticsTelemetryModule` は Application Insights インストルメンテーション コード自体のエラーを報告します。 たとえば、次のように入力します。 
コードのパフォーマンス カウンターにアクセスできない場合、または場合、 `ITelemetryInitializer` 例外をスローします。 トレース テレメトリ 
これによって追跡モジュールは、 [診断検索][diagnostic]します。
 
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet パッケージ。 このパッケージのみをインストールする場合、ApplicationInsights.config ファイルは自動作成されません。 

### 開発者モード

`DeveloperModeWithDebuggerAttachedTelemetryModule` は、デバッガーがアプリケーション プロセスに接続されているときに、Application Insights の `TelemetryChannel` にデータを即座に、一度に 1 つのテレメトリ項目を送信するよう強制します。 これにより、アプリケーションによるテレメトリの追跡時や、アプリケーションが Application Insights ポータルに表示されるときの時間間隔が削減されます。 ここでは、CPU とネットワーク帯域幅のオーバーヘッドが著しく費やされます。

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights の Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet パッケージ

### Web 要求の追跡

レポート、 [応答時間と結果コード](app-insights-asp-net.md) HTTP 要求のです。 

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet パッケージ

### 例外の追跡

`ExceptionTrackingTelemetryModule` は Web アプリで処理されない例外を追跡します。 参照してください [障害と例外][exceptions]します。

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet パッケージ


* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` -トラック [タスク例外を無視された](http://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx)します。
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` - worker ロール、Windows サービス、コンソール アプリケーションの未処理例外を追跡します。
* [Application Insights の Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet パッケージ。

### コア API

コア パッケージの提供、 [API のコア](https://msdn.microsoft.com/library/mt420197.aspx) sdk。 その他のテレメトリ モジュールを使用して、これとすることもできます [それを使用して、独自のテレメトリを定義](app-insights-api-custom-events-metrics.md)します。

* ApplicationInsights.config にエントリがありません。
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet パッケージ。 この NuGet だけをインストールする場合、.config ファイルは生成されません。

## テレメトリ チャネル

テレメトリ チャネルにより、テレメトリのバッファリングと Application Insights サービスへの送信が管理されます。 

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` がサービスの既定のチャネルです。 メモリにデータをバッファーします。
* `Microsoft.ApplicationInsights.PersistenceChannel` はコンソール アプリケーションの代替です。 アプリが停止したときにフラッシュされていないデータを永続ストレージに保存できます。また、アプリが再開したときにそのデータを送信します。


## テレメトリの初期化子 (ASP.NET)

テレメトリの初期化子は、テレメトリのあらゆるアイテムと共に送信されるコンテキスト プロパティを設定します。 

実行できます [独自の初期化子を記述](app-insights-api-filtering-sampling.md#add-properties) コンテキスト プロパティを設定します。

標準の初期化子は Web または WindowsServer NuGet パッケージによりすべて設定されます。


* `AccountIdTelemetryInitializer` は AccountId プロパティを設定します。
* `AuthenticatedUserIdTelemetryInitializer` は JavaScript SDK による設定に基づき AuthenticatedUserId プロパティを設定します。
* `AzureRoleEnvironmentTelemetryInitializer` は、Azure ランタイム環境から抽出された情報により、すべてのテレメトリ項目の `Device` コンテキストの `RoleName` および `RoleInstance` プロパティを更新します。
* `BuildInfoConfigComponentVersionTelemetryInitializer` は、MS ビルドによって生成された `BuildInfo.config` ファイルから抽出された値を使用して、すべてのテレメトリ項目の `Component` コンテキストの `Version` プロパティを更新します。
* `ClientIpHeaderTelemetryInitializer` は、要求の `X-Forwarded-For` HTTP ヘッダーに基づいて、すべてのテレメトリ項目の `Location` コンテキストの `Ip` プロパティを更新します。
* `DeviceTelemetryInitializer` は、すべてのテレメトリ項目の `Device` コンテキストの次のプロパティを更新します。
 - `Type` は "PC" に設定します
 - `Id` は Web アプリケーションが実行中のコンピューターのドメイン名に設定します。
 - `OemName` は WMI を使用して `Win32_ComputerSystem.Manufacturer` フィールドから抽出された値に設定します。
 - `Model` は WMI を使用して `Win32_ComputerSystem.Model` フィールドから抽出された値に設定します。
 - `NetworkType` は `NetworkInterface` から抽出された値に設定します。
 - `Language` は `CurrentCulture` の名前に設定します。
* `DomainNameRoleInstanceTelemetryInitializer` 更新プログラム、 `RoleInstance` のプロパティ、 `Device` すべてのコンテキスト
web アプリケーションが実行されているコンピューターのドメイン名で製品利用統計情報項目。
* `OperationNameTelemetryInitializer` は、HTTP メソッドのほか、ASP.NET MVC コントローラーの名前、要求の処理のために呼び出されるアクションに基づいて、すべてのテレメトリ項目の`RequestTelemetry` の `Name` プロパティと `Operation` コンテキストの `Name` プロパティを更新します。
* `OperationIdTelemetryInitializer` は、追跡されたすべてのテレメトリ アイテムの `Operation.Id` コンテキスト プロパティを更新し、自動生成された `RequestTelemetry.Id` が付いた要求を処理します。
* `SessionTelemetryInitializer` は、ユーザーのブラウザーで実行する Application Insights JavaScript インストルメンテーション コードが生成する `ai_session` Cookie から抽出された値を使用して、すべてのテレメトリ項目の `Session` コンテキストの `Id` プロパティを更新します。 
* `SyntheticTelemetryInitializer` は、可用性テストや検索エンジン ボットなど、合成ソースからの要求の処理時に追跡されるすべてのテレメトリ項目の `User`、`Session`、および `Operation` コンテキスト プロパティを更新します。 既定では、 [メトリックス エクスプ ローラー](app-insights-metrics-explorer.md) 合成テレメトリは表示されません。
* `UserAgentTelemetryInitializer` は、要求の `User-Agent` HTTP ヘッダーに基づいて、すべてのテレメトリ項目の `User` コンテキストの `UserAgent` プロパティを更新します。
* `UserTelemetryInitializer` は、ユーザーのブラウザーで実行する Application Insights JavaScript インストルメンテーション コードが生成する `ai_user` Cookie から抽出された値を使用して、すべてのテレメトリ項目の `User` コンテキストの `Id` および `AcquisitionDate` プロパティを更新します。


## テレメトリ プロセッサ (ASP.NET)

テレメトリ プロセッサは、SDK からポータルに送信される直前の各テレメトリ アイテムをフィルター処理し、変更できます。

実行できます [独自のテレメトリのプロセッサを作成する](app-insights-api-filtering-sampling.md#filtering)です。


#### アダプティブ サンプリング テレメトリ プロセッサー (2.0.0-beta3 以降)

この機能は、既定では有効になっています。 アプリが多数のテレメトリを送信する場合、このプロセッサはその一部を削除します。

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

パラメーターは、アルゴリズムが実現しようとするターゲットを指定します。 SDK の各インスタンスは独立して機能するため、サーバーが複数のコンピューターのクラスターである場合、テレメトリの実際の量もそれに応じて増加します。

[詳細については、サンプリング](app-insights-sampling.md)します。



#### 固定レート サンプリング テレメトリ プロセッサー (2.0.0-beta1 以降)

標準もあります [テレメトリ プロセッサをサンプリング](app-insights-api-filtering-sampling.md#sampling) (から 2.0.1)。

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## チャネル パラメーター (Java)

これらのパラメーターは、Java SDK が収集するテレメトリ データの格納とフラッシュする方法に影響します。

#### MaxTelemetryBufferCapacity

SDK のメモリー内ストレージに格納できるテレメトリ項目の数。 この数に達すると、テレメトリのバッファーがフラッシュされます。つまり、テレメトリの項目が、Application Insights サーバーに送信されます。

-   最小: 1
-   最大: 1000
-   既定値: 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### FlushIntervalInSeconds 

メモリー内ストレージに格納されているデータがフラッシュされる (Application Insights に送信される) 頻度を決定します。

-   最小: 1
-   最大: 300
-   既定値: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### MaxTransmissionStorageCapacityInMB

ローカル ディスクの永続的なストレージに割り当てられる MB 単位の最大サイズを決定します。 このストレージは、Application Insights のエンドポイントへの転送に失敗したテレメトリの項目を保存するために使用されます。 ストレージのサイズが満たされている場合は、テレメトリの新しい項目は破棄されます。

-   最小: 1
-   最大: 100
-   既定値: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```



## InstrumentationKey

これは、データが表示される Application Insights のリソースを決定します。 通常、アプリケーションごとに、個別のキーを持つリソースを作成します。

キーを動的に設定する場合 (たとえば、アプリケーションからの結果を別のリソースに送信する場合)、構成ファイルからキーを省略し、代わりにコードで設定することができます。

TelemetryClient のすべてのインスタンスのキーを設定するには (標準のテレメトリ モジュールを含む)、TelemetryConfiguration.Active でキーを設定します。 ASP.NET サービスの global.aspx.cs など、初期化メソッドでキーの設定を行います。

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

特定のイベント セットを異なるリソースに送信する場合、特定の TelemetryClient のキーを設定できます。

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

[API の詳細について][api]します。

新しいキーを取得する [Application Insights ポータルで新しいリソースを作成][new]します。

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md


