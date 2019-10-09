---
title: Eksplorowanie dzienników śledzenia Java na platformie Azure Application Insights | Microsoft Docs
description: Wyszukaj ślady Log4J lub Logback w Application Insights
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: fc0a9e2f-3beb-4f47-a9fe-3f86cd29d97a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/18/2019
ms.author: mbullwin
ms.openlocfilehash: 99a18125fa5ce07a46fcc1af554fd03746ebf110
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038155"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Eksplorowanie dzienników śledzenia Java w Application Insights
Jeśli używasz programu Logback lub Log4J (w wersji 1.2 lub 2.0) do śledzenia, dzienniki śledzenia mogą być automatycznie wysyłane do Application Insights, w którym można eksplorować i wyszukiwać.

> [!TIP]
> Musisz tylko raz ustawić klucz Instrumentacji Application Insights dla aplikacji. Jeśli używasz platformy, takiej jak źródło Java, być może klucz został już zarejestrowany w innym miejscu w konfiguracji aplikacji.

## <a name="using-the-application-insights-java-agent"></a>Korzystanie z Application Insights agenta Java

Można skonfigurować Application Insights agenta Java do automatycznego przechwytywania dzienników, włączając funkcję w pliku `AI-Agent.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">
         <Logging enabled="true" />
      </BuiltIn>
   </Instrumentation>
   <AgentLogger />
</ApplicationInsightsAgent>
```

Alternatywnie można postępować zgodnie z poniższymi instrukcjami.

## <a name="install-the-java-sdk"></a>Instalowanie zestawu Java SDK

Postępuj zgodnie z instrukcjami, aby zainstalować [Application INSIGHTS SDK dla języka Java][java], jeśli jeszcze tego nie zrobiono.

## <a name="add-logging-libraries-to-your-project"></a>Dodawanie bibliotek rejestrowania do projektu
*Wybierz odpowiedni sposób dla danego projektu.*

#### <a name="if-youre-using-maven"></a>Jeśli używasz narzędzia Maven...
Jeśli projekt jest już skonfigurowany do korzystania z Maven na potrzeby kompilacji, Scal jeden z następujących fragmentów kodu w pliku pliku pom. XML.

Następnie Odśwież zależności projektu, aby pobrać pliki binarne pobrane.

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v 2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v 1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Jeśli używasz narzędzia Gradle...
Jeśli projekt jest już skonfigurowany do korzystania z Gradle dla kompilacji, Dodaj jeden z następujących wierszy do grupy `dependencies` w pliku Build. Gradle:

Następnie Odśwież zależności projektu, aby pobrać pliki binarne pobrane.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v 2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J v 1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>W innym przypadku...
Postępuj zgodnie z instrukcjami, aby ręcznie zainstalować Application Insights Java SDK, Pobierz plik JAR (po nadejściu na stronie Maven Central kliknij link "Jar" w sekcji pobierania) dla odpowiedniego dołączania i Dodaj pobrany plik JAR dołączonego do projektu.

| Rejestratora | Pobierz | Biblioteka |
| --- | --- | --- |
| Logback |[Logback Dołącz do jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |ApplicationInsights-Logging-logback |
| Log4J v 2.0 |[Log4J v2 — plik JAR](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |ApplicationInsights-Logging-log4j2 |
| Log4J v 1.2 |[Log4J v 1.2 dołączanie jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |ApplicationInsights-Logging-log4j1_2 |


## <a name="add-the-appender-to-your-logging-framework"></a>Dodawanie dołączania do struktury rejestrowania
Aby rozpocząć pobieranie śladów, Scal odpowiedni fragment kodu z plikiem konfiguracji Log4J lub Logback: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v 2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" instrumentationKey="[APPLICATION_INSIGHTS_KEY]" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J v 1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
        <param name="instrumentationKey" value="[APPLICATION_INSIGHTS_KEY]" />
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

Do dołączania Application Insights można odwoływać się za pomocą dowolnego skonfigurowanego rejestratora, a nie musi on być rejestratorem głównym (jak pokazano w powyższym przykładzie kodu).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Eksplorowanie śladów w portalu Application Insights
Teraz, po skonfigurowaniu projektu do wysyłania śladów do Application Insights, można wyświetlić i przeszukać te ślady w portalu Application Insights, w bloku [wyszukiwania][diagnostic] .

Wyjątki przesłane za pośrednictwem rejestratorów będą wyświetlane w portalu jako dane telemetryczne wyjątku.

![W portalu Application Insights Otwórz pozycję Wyszukaj](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>Następne kroki
[Wyszukiwanie diagnostyczne][diagnostic]

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[java]: java-get-started.md


