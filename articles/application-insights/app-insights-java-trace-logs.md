<properties 
    pageTitle="Application Insights を使用した Java トレース ログの探索" 
    description="Application Insights を使用して Log4J または Logback のトレースを検索します" 
    services="application-insights" 
    documentationCenter="java"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/21/2015" 
    ms.author="awills"/>


# Application Insights を使用した Java トレース ログの探索

トレース用に Logback または Log4J (v1.2 または v2.0) を使用している場合は、トレース ログを自動的に Application Insights に送信して、Application Insights でトレース ログを探索および検索できます。

インストール [Application Insights SDK for Java ][java], をまだ完了していない場合は、です。


## プロジェクトへのログ ライブラリの追加

*プロジェクトに適した方法を選択してください。*

#### Maven を使用している場合:

プロジェクトが既に Maven を使用してビルドする設定になっている場合は、pom.xml ファイルに次のいずれかのコード スニペットをマージします。

次に、バイナリがダウンロードされるように、プロジェクトの依存関係を更新します。

*Logback*

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[1.0,)</version>
       </dependency>
    </dependencies>

*Log4J v2.0*

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[1.0,)</version>
       </dependency>
    </dependencies>

*Log4J v1.2*

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[1.0,)</version>
       </dependency>
    </dependencies>

#### Gradle を使用している場合:

プロジェクトはビルドの Gradle を使用して既に設定されて場合に、次の行のいずれかを追加、 `の依存関係` build.gradle ファイルにグループ化します。

次に、バイナリがダウンロードされるように、プロジェクトの依存関係を更新します。

**Logback**

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '1.0.+'

**Log4J v2.0**

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '1.0.+'

**Log4J v1.2**

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '1.0.+'

#### それ以外の場合:

適切なアペンダーをダウンロードして展開し、適切なライブラリをプロジェクトに追加します。


 ロガー| ダウンロード| ライブラリ
----|----|----
 Logback| [Logback アペンダーを含む SDK](http://dl.windowsazure.com/applicationinsights/javabin/logbackAppender.zip)| applicationinsights-logging-logback
 Log4J v2.0| [Log4J v2 アペンダーを含む SDK](http://dl.windowsazure.com/applicationinsights/javabin/log4j2Appender.zip)| applicationinsights-logging-log4j2
 Log4j v1.2| [Log4J v1.2 アペンダーを含む SDK](http://dl.windowsazure.com/applicationinsights/javabin/log4j1_2Appender.zip)| applicationinsights-logging-log4j1_2



## ログ フレームワークへのアペンダーの追加

トレースの取得を開始するには、適切なコード スニペットを Log4J または Logback の構成ファイルに追加します。

*Logback*

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>

*Log4J v2.0*


    <Appenders>
      <ApplicationInsightsAppender name="aiAppender" />
    </Appenders>
    <Loggers>
      <Root level="trace">
        <AppenderRef ref="aiAppender"/>
      </Root>
    </Loggers>

*Log4J v1.2*

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>

Application Insights のアペンダーは、上記のコード サンプルに示されているように、構成済みの任意のロガーによって参照でき、必ずしもルート ロガーを使用する必要はありません。

## Application Insights ポータルでのトレースの探索

これで、Application Insights にトレースを送信するプロジェクトを構成して、表示および Application Insights ポータルでこれらのトレースを検索、 [診断検索の ][diagnostic] ブレードです。

![Application Insights ポータルで診断検索を開きます](./media/app-insights-java-trace-logs/10-diagnostics.png)

## 次のステップ

[診断検索の ][diagnostic]




[diagnostic]: app-insights-diagnostic-search.md 
[java]: app-insights-java-get-started.md 

