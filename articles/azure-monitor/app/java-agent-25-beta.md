---
title: Monitorowanie wydajności aplikacji sieci Web Java na platformie Azure Application Insights (2.5.0-BETA) | Microsoft Docs
description: Rozszerzone monitorowanie wydajności i użycia witryny sieci Web w języku Java za pomocą Application Insights (2.5.0-BETA).
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: mbullwin
ms.openlocfilehash: 0596cc78c464deb117def3178827ba4945f857ac
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68298323"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Monitorowanie zależności, przechwycone wyjątki i czasy wykonywania metod w aplikacjach sieci Web Java


Jeśli masz [instrumentację aplikacji sieci Web w języku Java za pomocą Application Insights][java], możesz użyć agenta Java, aby uzyskać dokładniejszy wgląd, bez żadnych zmian w kodzie:

* **Tamten** Dane dotyczące wywołań, które aplikacja wykonuje w innych składnikach, w tym:
  * **Wychodzące wywołania http** wykonywane za pośrednictwem Apache HttpClient `java.net.HttpURLConnection` , OkHttp i są przechwytywane.
  * Przechwycono **wywołania Redis** wykonywane za pośrednictwem klienta Jedis.
  * **JDBC zapytania** — dla obiektów MySQL i PostgreSQL, jeśli wywołanie trwa dłużej niż 10 sekund, Agent zgłosi plan zapytania.

* **Rejestrowanie aplikacji:** Przechwytywanie i skorelowanie dzienników aplikacji przy użyciu żądań HTTP i innych danych telemetrycznych
  * **Log4j 1,2**
  * **Log4j2**
  * **Logback**

* **Lepsza nazwa operacji:** (używany do agregowania żądań w portalu)
  * Na`@RequestMapping`podstawie.
  * **Jax-RS** — na `@Path`podstawie. 

Aby korzystać z agenta Java, należy zainstalować go na serwerze. Aplikacje sieci Web muszą być Instrumentacją [Application Insights Java SDK][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Zainstaluj agenta Application Insights dla środowiska Java
1. Na maszynie z uruchomionym serwerem Java [pobierz agenta](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest). Upewnij się, że pobierasz tę samą wersję agenta Java co wersja pakietów podstawowego i internetowego zestawu Java SDK usługi Application Insights.
2. Edytuj skrypt uruchamiania serwera aplikacji i Dodaj następujący JVM ARG:
   
    `-javaagent:<full path to the agent JAR file>`
   
    Na przykład w Tomcat na komputerze z systemem Linux:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Uruchom ponownie serwer aplikacji.

## <a name="configure-the-agent"></a>Konfigurowanie agenta
Utwórz plik o nazwie `AI-Agent.xml` i umieść go w tym samym folderze, w którym znajduje się plik JAR agenta.

Ustaw zawartość pliku XML. Edytuj Poniższy przykład, aby dołączyć lub pominąć wybrane funkcje.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">

         <!-- capture logging via Log4j 1.2, Log4j2, and Logback, default is true -->
         <Logging enabled="true" />

         <!-- capture outgoing HTTP calls performed through Apache HttpClient, OkHttp,
              and java.net.HttpURLConnection, default is true -->
         <HTTP enabled="true" />

         <!-- capture JDBC queries, default is true -->
         <JDBC enabled="true" />

         <!-- capture Redis calls, default is true -->
         <Jedis enabled="true" />

         <!-- capture query plans for JDBC queries that exceed this value (MySQL, PostgreSQL),
              default is 10000 milliseconds -->
         <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>

      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="additional-config-spring-boot"></a>Dodatkowa konfiguracja (sprężynowy rozruch)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

W przypadku usługi Azure App Services wykonaj następujące czynności:

* Wybierz kolejno pozycje Ustawienia > Ustawienia aplikacji
* W obszarze Ustawienia aplikacji dodaj nową parę klucz-wartość:

Klucz: `JAVA_OPTS`Wartościami`-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.5.0-BETA.jar`

Aby uzyskać najnowszą wersję agenta Java, zapoznaj się z wydaniami [tutaj.](https://github.com/Microsoft/ApplicationInsights-Java/releases
) 

Agent musi być spakowany jako zasób w projekcie, w taki sposób, aby kończył się w sieci D:/Home/site/wwwroot/katalogu. Można potwierdzić, że Agent znajduje się w poprawnym katalogu App Service, przechodząc do**konsoli debugowania** narzędzia deweloperskie**Narzędzia** >  **programistyczne** > i sprawdzając zawartość katalogu witryn.    

* Zapisz ustawienia i ponownie uruchom aplikację. (Te kroki dotyczą tylko App Services uruchomionych w systemie Windows).

> [!NOTE]
> AI-Agent. XML i plik JAR agenta powinny znajdować się w tym samym folderze. Są one często umieszczane w `/resources` folderze projektu.  

#### <a name="enable-w3c-distributed-tracing"></a>Włącz śledzenie rozproszone W3C

Dodaj następujący kod do AI-Agent. XML:

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> Tryb zgodności z poprzednimi wersjami jest domyślnie włączony, a parametr enableW3CBackCompat jest opcjonalny i powinien być używany tylko wtedy, gdy chcesz go wyłączyć. 

W idealnym przypadku, gdy wszystkie usługi zostały zaktualizowane do nowszej wersji zestawów SDK obsługujących protokół W3C. Zdecydowanie zaleca się przejście do nowszej wersji zestawów SDK z obsługą W3C tak szybko, jak to możliwe.

Upewnij się, że **konfiguracje [przychodzące](correlation.md#w3c-distributed-tracing) i wychodzące (Agent)** są dokładnie takie same.

## <a name="view-the-data"></a>Wyświetlanie danych
W zasobie Application Insights zagregowane czasy wykonywania zdalnych zależności i metod są wyświetlane [w obszarze kafelka wydajność][metrics].

Aby wyszukać poszczególne wystąpienia zależności, wyjątków i raportów metod, Otwórz [Wyszukiwanie][diagnostic].

[Diagnozowanie problemów z zależnościami — Dowiedz się więcej](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Pytania? Problemy?
* Brak danych? [Ustawianie wyjątków zapory](../../azure-monitor/app/ip-addresses.md)
* [Rozwiązywanie problemów z technologią Java](java-troubleshoot.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
