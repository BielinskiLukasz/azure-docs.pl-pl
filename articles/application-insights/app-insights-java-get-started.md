<properties
    pageTitle="Java Web プロジェクトで Application Insights を使う | Microsoft Azure"
    description="Application Insights を使用して Java Web サイトのパフォーマンスと利用状況を監視します"
    services="application-insights"
    documentationCenter="java"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="11/24/2015"
    ms.author="awills"/>

# Java Web プロジェクトで Application Insights を使う

*Application Insights はプレビュー段階です。*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

[Application Insights](https://azure.microsoft.com/services/application-insights/) は、パフォーマンスと、ライブ アプリケーションの使用状況を理解に役立つ拡張可能な分析サービスです。 使用して [を検出し、例外とパフォーマンスの問題を診断](app-insights-detect-triage-diagnose.md), 、および [コードを記述][api] 、アプリケーションで実行するユーザーを追跡します。

![サンプル データ](./media/app-insights-java-get-started/5-results.png)

Application Insights は、Linux、Unix、Windows で動作する Java アプリをサポートします。

必要なものは次のとおりです。

* Oracle JRE 1.6 以降、または Zulu JRE 1.6 以降
* サブスクリプションを [Microsoft Azure](http://azure.microsoft.com/)します。 (の作業を開始できます、 [無料評価版](http://azure.microsoft.com/pricing/free-trial/).)

*代替手順が続くことができる場合が既に実行中、web アプリがある場合は、 [web サーバーで実行時に SDK を追加する](app-insights-java-live.md)です。 回避、コードを再構築が、ユーザーの利用状況を追跡するためにコードを記述するオプションをすることはありません。*


## 1.Application Insights のインストルメンテーション キーを取得する

1. サインイン、 [Microsoft Azure ポータル](https://portal.azure.com)します。
2. 新しい Application Insights リソースを作成します。

    ![[+] をクリックし、[Application Insights] を選択します](./media/app-insights-java-get-started/01-create.png)
3. アプリケーションの種類を [Java Web アプリケーション] に設定します。

    ![名前を入力し、[Java Web アプリケーション] を選択した後、[作成] をクリックします](./media/app-insights-java-get-started/02-create.png)
4. 新しいリソースのインストルメンテーション キーを見つけます。 このキーは、後でコード プロジェクトに貼り付けます。

    ![新しいリソース概要で、[プロパティ] をクリックし、インストルメンテーション キーをコピーします](./media/app-insights-java-get-started/03-key.png)

## 2.Application Insights SDK for Java をプロジェクトに追加する

*プロジェクトに適した方法を選択してください。*

#### Eclipse で動的 Web プロジェクトを作成している場合:

使用して、 [Application Insights SDK for Java プラグイン][eclipse]します。

#### Maven を使用している場合:

プロジェクトが既に Maven を使用してビルドする設定になっている場合は、pom.xml ファイルに次のコードをマージします。

次に、バイナリがダウンロードされるように、プロジェクトの依存関係を更新します。

    <repositories>
       <repository>
          <id>central</id>
          <name>Central</name>
          <url>http://repo1.maven.org/maven2</url>
       </repository>
    </repositories>

    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web</artifactId>
        <!-- or applicationinsights-core for bare API -->
        <version>[1.0,)</version>
      </dependency>
    </dependencies>


* *ビルドまたはチェックサムの検証エラーでしょうか。*特定のバージョンを試してください (例: `<version>1.0.n</version>`)。 最新バージョンがあります、 [SDK リリース ノート](app-insights-release-notes-java.md) または、 [Maven アーティファクト](http://search.maven.org/#search%7Cga%7C1%7Capplicationinsights)します。
* *新しい SDK に更新する必要がありますか。*プロジェクトの依存関係を更新します。

#### Gradle を使用している場合:

プロジェクトが既に Gradle を使用してビルドする設定になっている場合は、build.gradle ファイルに次のコードをマージします。

次に、バイナリがダウンロードされるように、プロジェクトの依存関係を更新します。

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '1.+'
      // or applicationinsights-core for bare API
    }

* *ビルド エラーまたはチェックサムの検証エラーが発生する場合は、特定のバージョンを試してください:* `version:'1.0.n'`。 *最新バージョンがあります、 [SDK リリース ノート](app-insights-release-notes-java.md)します。*
* *新しい SDK に更新するには*
 * プロジェクトの依存関係を更新します。

#### それ以外の場合:

SDK を手動で追加する:

1. ダウンロード、 [Application Insights SDK for Java](https://azuredownloads.blob.core.windows.net/applicationinsights/sdk.html)します。
2. zip ファイルからバイナリを抽出し、プロジェクトに追加します。

### 疑問がある場合...

* *間のリレーションシップは、どのような `-core` と `-web` zip 内のコンポーネントですか?*

 * `applicationinsights-core` は最小限の API を提供します。 これは常に必要です。
 * `applicationinsights-web` HTTP 要求数と応答時間を追跡するメトリックを提供します。 このテレメトリを自動的に収集しない場合、これは省略できます。 たとえば、独自に記述する場合です。

* *変更が発行されたときに SDK を更新するには*
 * 最新版をダウンロード [Application Insights SDK for Java](http://dl.windowsazure.com/lib/applicationinsights/javabin/sdk.zip) 古いものと置き換えます。
 * 変更内容は「、 [SDK リリース ノート](app-insights-release-notes-java.md)します。



## 3.Application Insights の xml ファイルを追加する

ApplicationInsights.xml をプロジェクトのリソース フォルダーに追加するか、プロジェクトのデプロイメント クラス パスに追加されていることを確認します。 次の XML をファイルにコピーします。

インストルメンテーション キーについては、Azure ポータルで入手したキーを使用してください。

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>


* インストルメンテーション キーは、テレメトリのすべての項目と共に送信されます。インストルメンテーション キーを受け取った Application Insights は、リソース内にこのキーを表示します。
* HTTP 要求コンポーネントはオプションです。 このコンポーネントは、要求と応答時間に関するテレメトリをポータルに自動的に送信します。
* イベントの関連付けは、HTTP 要求コンポーネントに対する追加の操作です。 この操作では、サーバーで受信した各要求に識別子を割り当てた後、この識別子をテレメトリのすべての項目に "Operation.Id" プロパティとして追加します。 フィルターを設定して、各要求に関連付けられているテレメトリに関連付けることができます [診断検索][diagnostic]します。

## 4.HTTP フィルターを追加する

最後の構成手順では、HTTP 要求コンポーネントが各 Web 要求をログに記録できるようにします  (単に最小限の API が必要な場合はこの手順を行う必要はありません)。

プロジェクトの web.xml ファイルを見つけて開きます。アプリケーション フィルターが構成されている web-app ノードの下に次のコードをマージします。

最も正確な結果を得るためには、他のすべてのフィルターの前にこのフィルターをマップする必要があります。

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>

#### MVC 3.1 以降を使用している場合

Application Insights パッケージを含めるように次の要素を編集します。

    <context:component-scan base-package=" com.springapp.mvc, com.microsoft.applicationinsights.web.spring"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.microsoft.applicationinsights.web.spring.RequestNameHandlerInterceptorAdapter" />
        </mvc:interceptor>
    </mvc:interceptors>

#### Struts 2 を使用している場合

次の項目を Struts 構成ファイルに追加します (通常は、struts.xml または struts-default.xml)。

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />

(既定のスタックにインターセプターが定義されている場合は、単にインターセプターをそのスタックに追加できます)。



## 5.アプリケーションを実行する

開発用コンピューターでデバッグ モードで実行するか、サーバーに発行します。

## 6.Application Insights でのテレメトリを表示する

Application Insights リソースに戻ります [Microsoft Azure ポータル](https://portal.azure.com)します。

HTTP 要求データが概要ブレードに表示されます  (表示されない場合は、数秒待ってから [最新の情報に更新] をクリックします)。

![サンプル データ](./media/app-insights-java-get-started/5-results.png)


任意のグラフをクリックして、より詳細なメトリックを表示します。

![](./media/app-insights-java-get-started/6-barchart.png)



要求のプロパティを表示すると、その要求に関連付けられているテレメトリ イベント (要求や例外など) が表示されます。

![](./media/app-insights-java-get-started/7-instance.png)



[メトリックの詳細についてはこちらをご覧ください。][metrics]

#### 洗練されたアドレス名の計算

Application Insights では、MVC アプリケーションの HTTP 要求の形式として、`VERB controller/action` が想定されます。


たとえば、`GET Home/Product/f9anuh81`、`GET Home/Product/2dffwrf5`、`GET Home/Product/sdf96vws` は、`GET Home/Product` にグループ化されます。

これにより、要求数や要求の平均実行時間など、要求の意味のある集計を行うことができます。


## 5.サーバーへのアプリのインストール

次に、サーバーにアプリを発行してユーザーがアプリを使用できるようにし、ポータルに表示されるテレメトリを監視します。

* アプリケーションがこれらのポートにテレメトリを送信できるようにファイアウォールが設定されていることを確認します。

 * dc.services.visualstudio.com:443
 * dc.services.visualstudio.com:80
 * f5.services.visualstudio.com:443
 * f5.services.visualstudio.com:80


* Windows サーバーに次のものをインストールします。

 * [Microsoft Visual C++ 再頒布可能パッケージ](http://www.microsoft.com/download/details.aspx?id=40784)

    (これにより、パフォーマンス カウンターが有効になります。)

## 例外と要求エラー

未処理の例外は、自動的に収集されます。

![Scroll down and click the Failures tile](./media/app-insights-java-get-started/21-exceptions.png)

その他の例外に関するデータを収集するには 2 つのオプションがあります。

* [TrackException() の呼び出しをコードに挿入][apiexceptions]します。 
* [Java エージェントをサーバーにインストール](app-insights-java-agent.md)します。 監視するメソッドを指定します。


## メソッドの呼び出しと外部依存関係の監視

[Java エージェントをインストール](app-insights-java-agent.md) 指定された内部メソッドと、JDBC を通じて呼び出しタイミング データと共にログに記録します。


## パフォーマンス カウンター

クリックして、 **サーバー** タイル、およびするパフォーマンス カウンターの範囲が表示されます。


![](./media/app-insights-java-get-started/11-perf-counters.png)

### パフォーマンス カウンター コレクションをカスタマイズする

パフォーマンス カウンターの標準セットのコレクションを無効にするには、ApplicationInsights.xml ファイルのルート ノードの下に次のコードを追加します。

    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>

### 追加のパフォーマンス カウンターを収集する

収集する追加のパフォーマンス カウンターを指定できます。

#### JMX カウンター (Java 仮想マシンによって公開されます)

    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>

*   `displayName` - Application Insights ポータルに表示される名前。
*   `objectName` - JMX オブジェクトの名前。
*   `attribute` - 取得する JMX オブジェクト名の属性
*   `type` (オプション) - JMX オブジェクトの属性の型。
 *  既定値: int、long などの単純型。
 *  `composite`: パフォーマンス カウンター データは、"Attribute.Data" 形式です。
 *  `tabular`: パフォーマンス カウンター データは、テーブル行形式です。



#### Windows パフォーマンス カウンター

各 [Windows パフォーマンス カウンターを](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) はカテゴリのメンバー (フィールドがクラスのメンバーである同様)。 カテゴリについては、グローバルに設定することも、数字または名前付きインスタンスを設定することもできます。

    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>

*   displayName - Application Insights ポータルに表示される名前。
*   categoryName - このパフォーマンス カウンターが関連付けられているパフォーマンス カウンターのカテゴリ (パフォーマンス オブジェクト)。
*   counterName - パフォーマンス カウンターの名前。
*   instanceName - パフォーマンス カウンター カテゴリ インスタンスの名前、または空の文字列 ("") (カテゴリにインスタンスが 1 つ含まれている場合)。 categoryName が Process であり、アプリが実行されている現在の JVM プロセスからパフォーマンス カウンターを収集する場合は、`"__SELF__"` を指定します。

パフォーマンス カウンターはでカスタム メトリックとして表示 [メトリックス エクスプ ローラー][metrics]します。

![](./media/app-insights-java-get-started/12-custom-perfs.png)


### Unix パフォーマンス カウンター

* [Application Insights プラグインを使用して collectd をインストール](app-insights-java-collectd.md) をさまざまなシステムとネットワークに関するデータを取得します。

## ユーザーとセッションのデータを取得する

Web サーバーからテレメトリを送信しようとしているところです。 ここで、アプリケーションの状態を完全に把握するために、監視を追加することもできます。

* [Web ページにテレメトリを追加][usage] モニター ページ ビューやユーザー メトリックをします。
* [Web テストを設定][availability] を確認する、アプリケーションが動作していて応答します。

## ログ トレースをキャプチャする

Application Insights を使用すると、Log4J、Logback、またはその他のログ フレームワークのログをさまざまな側面から分析できます。 ログは、HTTP 要求やその他のテレメトリに関連付けることができます。 [学習方法][javalogs]します。

## 独自のテレメトリを送信する

SDK をインストールすると、API を使用して独自のテレメトリを送信できるようになります。

* [カスタム イベントおよびメトリックスの追跡][api] をユーザーが行って、アプリケーションを参照してください。
* [イベントおよびログを検索][diagnostic] 問題の診断に役立ちます。


## 可用性 Web テスト

Application Insights では、Web サイトを定期的にテストして、Web サイトが正常に動作および応答していることを確認できます。 [設定する][availability], 、下にスクロールして [可用性] をクリックします。

![下にスクロール、[可用性] をクリック、[Web テストを追加]](./media/app-insights-java-get-started/31-config-web-test.png)

応答時間のグラフが表示されます。また、サイトがダウンしている場合はメールによる通知を受け取ります。

![Web テストの例](./media/app-insights-java-get-started/appinsights-10webtestresult.png)

[可用性 Web テストの詳細についてはこちら。][availability] 






## 疑問がある場合 問題が発生した場合

[Java のトラブルシューティング](app-insights-java-troubleshoot.md)

## 次のステップ

詳細については、次を参照してください。、 [Java デベロッパー センター](/develop/java/)します。

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md

