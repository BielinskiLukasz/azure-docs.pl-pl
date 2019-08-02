---
title: Analiza aplikacji sieci Web w języku Java za pomocą usługi Azure Application Insights (2.5.0-BETA) | Microsoft Docs
description: 'Program Application Performance Monitoring for Java Web Apps Application Insights (2.5.0-BETA). '
services: application-insights
documentationcenter: java
author: lgayhardt
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: lagayhar
ms.openlocfilehash: c56b979bf5a4f1e0868dd0542cb71c2da6a85df7
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494468"
---
# <a name="get-started-with-application-insights-in-a-java-web-project-250-beta"></a>Wprowadzenie do Application Insights w projekcie sieci Web w języku Java (2.5.0-BETA)

[Application Insights](https://azure.microsoft.com/services/application-insights/) jest rozszerzalną usługą analizy dla deweloperów sieci Web, która ułatwia zrozumienie wydajności i użycia aktywnej aplikacji. Umożliwiają one [Automatyczne instrumentację żądań, śledzenie zależności oraz zbieranie liczników wydajności](auto-collect-dependencies.md#java), diagnozowanie problemów z wydajnością i wyjątków oraz [pisanie kodu][api] w celu śledzenia użytkowników, którzy korzystają z aplikacji. 

![Zrzut ekranu przedstawiający przykładowe dane omówienia](./media/java-get-started/overview-graphs.png)

Usługa Application Insights obsługuje aplikacje w języku Java działające w systemach Linux, Unix lub Windows.

Potrzebne elementy:

* Java 7 lub nowszy
* Subskrypcja platformy [Microsoft Azure](https://azure.microsoft.com/).

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Uzyskiwanie klucza instrumentacji usługi Application Insights
1. Zaloguj się do [Portalu Microsoft Azure](https://portal.azure.com).
2. Utwórz zasób usługi Application Insights. Jako typ aplikacji ustaw wartość Aplikacja internetowa Java.

3. Znajdź klucz instrumentacji nowego zasobu. Wkrótce będzie trzeba wkleić ten klucz do projektu kodu.

    ![W opisie nowego zasobu kliknij opcję Właściwości i skopiuj klucz instrumentacji](./media/java-get-started/instrumentation-key-001.png)

## <a name="2-add-the-application-insights-sdk-for-java-to-your-project"></a>2. Dodawanie zestawu SDK usługi Application Insights dla środowiska Java do projektu
*Wybierz odpowiedni sposób dla danego projektu.*

#### <a name="if-youre-using-maven-a-namemaven-setup-"></a>Jeśli używasz narzędzia Maven... <a name="maven-setup" />
Jeśli projekt jest już skonfigurowany do używania narzędzia Maven w celu kompilacji, scal następujący kod z plikiem pom.xml.

Następnie odśwież zależności projektu, aby pliki binarne zostały pobrane.

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.5.0-BETA</version>
      </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle-a-namegradle-setup-"></a>Jeśli używasz narzędzia Gradle... <a name="gradle-setup" />
Jeśli projekt jest już skonfigurowany do używania narzędzia Gradle w celu kompilacji, scal następujący kod z plikiem build.gradle.

Następnie odśwież zależności projektu, aby pliki binarne zostały pobrane.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.5.0-BETA'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

#### <a name="otherwise-if-you-are-manually-managing-dependencies-"></a>W innym przypadku, jeśli ręcznie zarządzasz zależnościami...
Pobierz [najnowszą wersję](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) i skopiuj niezbędne pliki do projektu, zastępując wszystkie poprzednie wersje.

### <a name="questions"></a>Pytania...
* *Jaka jest relacja między `-web-auto` `-web` składnikami i `-core` ?*
  * `applicationinsights-web-auto`Program udostępnia metryki do śledzenia liczby żądań i czasów odpowiedzi HTTP serwletu przez automatyczne zarejestrowanie filtru Application Insights serwletu w czasie wykonywania.
  * `applicationinsights-web`zapewnia również metryki, które śledzą liczby żądań HTTP serwletu i czasy odpowiedzi, ale wymagają ręcznej rejestracji filtru Application Insights serwletu w aplikacji.
  * `applicationinsights-core`zapewnia tylko bezobsługowy interfejs API, na przykład jeśli aplikacja nie jest oparta na serwletu.
  
* *Jak zaktualizować zestaw SDK do najnowszej wersji?*
  * Jeśli używasz narzędzia Gradle lub Maven...
    * Zaktualizuj plik kompilacji, aby określić najnowszą wersję.
  * Jeśli ręcznie zarządzasz zależnościami...
    * Pobierz najnowszy [Zestaw SDK usługi Application Insights dla środowiska Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) i zastąp nim stary. Zmiany są opisane w [informacjach o wersji zestawu SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## <a name="3-add-an-applicationinsightsxml-file"></a>3. Dodawanie pliku ApplicationInsights.xml
Dodaj plik ApplicationInsights.xml do folderu zasobów w projekcie lub upewnij się, że jest dodany do ścieżki klas wdrażania projektu. Skopiuj do niego następujący kod XML.

Zastąp klucz instrumentacji kluczem pobranym z portalu Azure.

```XML
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
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
   </TelemetryInitializers>

</ApplicationInsights>
```

Opcjonalnie plik konfiguracji może znajdować się w dowolnym miejscu, które jest dostępne dla aplikacji.  Właściwość systemu `-Dapplicationinsights.configurationDirectory` określa katalog, który zawiera plik ApplicationInsights.xml. Na przykład plik konfiguracji znajdujący się w lokalizacji `E:\myconfigs\appinsights\ApplicationInsights.xml` można skonfigurować za pomocą właściwości `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* Klucz instrumentacji jest wysyłany wraz z każdym elementem telemetrii i dzięki temu te elementy mogą być wyświetlane dla odpowiedniego zasobu usługi Application Insights.
* Składnik żądań HTTP jest opcjonalny. Powoduje automatyczne wysyłanie telemetrii dotyczącej żądań i czasów odpowiedzi do portalu.
* Korelacja zdarzenia jest dodatkiem do składnika żądań HTTP. Przypisuje identyfikator do każdego żądania odebranego przez serwer i dodaje go do każdego elementu telemetrii jako właściwość „Operation.Id”. Umożliwia skorelowanie telemetrii skojarzonej z każdym żądaniem przez ustawienie filtru w wyszukiwaniu [diagnostycznym][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Alternatywne sposoby ustawienia klucza instrumentacji
Zestaw SDK usługi Application Insights szuka klucza w następującej kolejności:

1. Właściwość systemu:-DAPPINSIGHTS_INSTRUMENTATIONKEY = your_ikey
2. Zmienna środowiskowa: APPINSIGHTS_INSTRUMENTATIONKEY
3. Plik konfiguracji: ApplicationInsights.xml

Możesz również [ustawić klucz w kodzie](../../azure-monitor/app/api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="4-add-agent"></a>4. Dodaj agenta

[Zainstaluj agenta Java](java-agent-25-beta.md) , aby przechwytywać wychodzące wywołania http, zapytania JDBC, rejestrowanie aplikacji i lepszą nazwę operacji.

## <a name="5-run-your-application"></a>5. Uruchamianie aplikacji
Uruchom aplikację w trybie debugowania na komputerze deweloperskim albo opublikuj na serwerze.

## <a name="6-view-your-telemetry-in-application-insights"></a>6. Wyświetlanie telemetrii w usłudze Application Insights
Wróć do zasobu usługi Application Insights w witrynie [Microsoft Azure Portal](https://portal.azure.com).

W bloku przeglądu zostaną wyświetlone dane żądań HTTP. (Jeśli ich tam nie ma, odczekaj kilka sekund, a następnie kliknij przycisk Odśwież).

![Zrzut ekranu przedstawiający przykładowe dane omówienia](./media/java-get-started/overview-graphs.png)

[Dowiedz się więcej o metrykach.][metrics]

Klikaj elementy wykresów, aby wyświetlać bardziej szczegółowe metryki zagregowane.

![Okienko niepowodzeń Application Insights z wykresami](./media/java-get-started/006-barcharts.png)

<!--
[TODO update image with 2.5.0-BETA operation naming provided by agent]
-->

### <a name="instance-data"></a>Dane wystąpienia
Kliknij określony typ żądania, aby wyświetlić poszczególne wystąpienia.

![Przechodzenie do szczegółów konkretnego widoku przykładowego](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Wersję Zaawansowany język zapytań
W miarę zgromadzenia większej ilości danych można uruchamiać zapytania zarówno w celu agregowania danych, jak i w celu znajdowania poszczególnych wystąpień.  [Analiza](../../azure-monitor/app/analytics.md) jest zaawansowanym narzędziem, którego można używać zarówno w celu poznania wydajności i użycia, jak i do celów diagnostycznych.

![Przykład analizy](./media/java-get-started/0025.png)

## <a name="7-install-your-app-on-the-server"></a>7. Instalowanie aplikacji na serwerze
Teraz opublikuj aplikację na serwerze, pozwól z niej korzystać innym osobom, a następnie obejrzyj telemetrię wyświetlaną w portalu.

* Upewnij się, że zapora pozwala aplikacji na wysłanie telemetrii do tych portów:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Jeśli ruch wychodzący ma być kierowany przez zaporę, zdefiniuj właściwości systemu `http.proxyHost` i `http.proxyPort`.

* Na serwerach systemu Windows zainstaluj:

  * [Pakiet Microsoft Visual C++ Redistributable](https://www.microsoft.com/download/details.aspx?id=40784)

    Ten składnik umożliwia działanie liczników wydajności.

## <a name="azure-app-service-config-spring-boot"></a>Konfiguracja Azure App Service (sprężynowy rozruch)

Aplikacje ze sprężyną rozruchu działające w systemie Windows wymagają dodatkowej konfiguracji do uruchomienia na platformie Azure App Services. Zmodyfikuj **plik Web. config** i Dodaj następujące elementy:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified"/>
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\AzureWebAppExample-0.0.1-SNAPSHOT.jar&quot;">
        </httpPlatform>
    </system.webServer>
</configuration>
```

## <a name="exceptions-and-request-failures"></a>Wyjątki i błędy żądań
Nieobsłużone wyjątki i niepowodzenia żądań są automatycznie zbierane przez Application Insights filtr sieci Web.

Aby zbierać dane na innych wyjątkach, można [wstawiać wywołania do metody trackexception () w kodzie][apiexceptions].

## <a name="monitor-method-calls-and-external-dependencies"></a>Monitorowanie wywołań metod i zależności zewnętrznych
[Zainstaluj agenta programu Java](java-agent.md) w celu rejestrowania określonych metod wewnętrznych i wywołań za pośrednictwem JDBC z danymi chronometrażu.

I dla automatycznego nazewnictwa operacji.

## <a name="w3c-distributed-tracing"></a>Śledzenie rozproszone W3C

Zestaw Application Insights Java SDK obsługuje teraz [rozproszone śledzenie W3C](https://w3c.github.io/trace-context/).

Konfiguracja przychodzącego zestawu SDK została omówiona dokładniej w naszym artykule [](correlation.md#w3c-distributed-tracing)dotyczącym korelacji.

Konfiguracja wychodzącego zestawu SDK jest zdefiniowana w pliku [AI-Agent. XML](java-agent.md) .

## <a name="performance-counters"></a>Liczniki wydajności
Otwórz **Badanie**, **metryki**, aby wyświetlić zakres liczników wydajności.

![Zrzut ekranu okienka metryk z wybranymi prywatnymi bajtami procesu](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Dostosowywanie zbierania danych liczników wydajności
Aby wyłączyć zbieranie standardowego zestawu liczników wydajności, dodaj następujący kod w węźle głównym pliku ApplicationInsights.xml:

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>Zbieranie danych dodatkowych liczników wydajności
Możesz określić dodatkowe liczniki wydajności do zbierania danych.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>Liczniki JMX (udostępniane przez maszynę wirtualną Java)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` — nazwa wyświetlana w portalu Application Insights.
* `objectName` — nazwa obiektu JMX.
* `attribute` — atrybut nazwy obiektu JMX do pobrania.
* `type` (opcjonalnie) — typ atrybutu obiektu JMX:
  * wartość domyślna: typ prosty, np. int lub long.
  * `composite`: dane licznika wydajności są w formacie „Atrybut.Dane”
  * `tabular`: dane licznika wydajności są w formacie wiersza tabeli

#### <a name="windows-performance-counters"></a>Liczniki wydajności systemu Windows
Każdy [licznik wydajności systemu Windows](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) jest składową kategorii (w taki sam sposób, w jaki pole jest składową klasy). Kategorie mogą być globalne lub mogą mieć wystąpienia numerowane lub nazwane.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName — nazwa wyświetlana w portalu Application Insights.
* categoryName — kategoria licznika wydajności (obiekt wydajności), z którą skojarzony jest ten licznik wydajności.
* counterName — nazwa licznika wydajności.
* instanceName — nazwa wystąpienia kategorii licznika wydajności lub ciąg pusty (""), jeśli kategoria zawiera jedno wystąpienie. Jeśli categoryName to Proces, a licznik wydajności, którego dane chcesz zbierać, pochodzi z bieżącego procesu maszyny JVM, na której jest uruchomiona aplikacja, podaj wartość `"__SELF__"`.

### <a name="unix-performance-counters"></a>Liczniki wydajności sytemu Unix
* [Zainstaluj program collectd z wtyczką Application Insights](java-collectd.md), aby uzyskać szeroką gamę danych na temat systemu i sieci.

## <a name="get-user-and-session-data"></a>Pobieranie danych użytkownika i sesji
Wysyłasz już telemetrię z serwera sieci Web. Teraz, aby zyskać pełny wgląd w dane aplikacji, możesz dodać więcej opcji monitorowania:

* [Dodaj telemetrię do stron sieci Web][usage] , aby monitorować wyświetlenia stron i metryki użytkowników.
* [Skonfiguruj testy sieci Web][availability] , aby upewnić się, że aplikacja pozostaje aktywna i będzie odpowiadać.

## <a name="send-your-own-telemetry"></a>Wysyłanie własnej telemetrii
Teraz, po zainstalowaniu zestawu SDK, możesz użyć interfejsu API do wysyłania własnej telemetrii.

* [Śledź niestandardowe zdarzenia i metryki][api] , aby dowiedzieć się, co użytkownicy robią z aplikacją.
* [Wyszukaj zdarzenia i dzienniki][diagnostic] , aby ułatwić diagnozowanie problemów.

## <a name="availability-web-tests"></a>Testy dostępności sieci Web
Usługa Application Insights może służyć do testowania witryny sieci Web w regularnych odstępach czasu, aby sprawdzić, czy witryna działa i odpowiada poprawnie.

[Dowiedz się więcej na temat konfigurowania testów dostępności sieci Web.][availability]

## <a name="questions-problems"></a>Pytania? Problemy?
[Rozwiązywanie problemów z technologią Java](java-troubleshoot.md)

## <a name="next-steps"></a>Następne kroki
* [Monitorowanie wywołań zależności](java-agent.md)
* [Monitorowanie liczników wydajności sytemu Unix](java-collectd.md)
* Dodawanie [monitorowania do stron sieci Web](javascript.md) w celu monitorowania czasów ładowania stron, wywołań AJAX i wyjątków przeglądarki
* Zapisywanie [niestandardowych danych telemetrycznych](../../azure-monitor/app/api-custom-events-metrics.md) w celu śledzenia użycia w przeglądarce lub na serwerze.
* Tworzenie zaawansowanych zapytań dotyczących telemetrii z poziomu aplikacji przy użyciu usługi [Analytics](../../azure-monitor/app/analytics.md)
* Aby uzyskać więcej informacji, odwiedź stronę [Azure dla deweloperów języka Java](/java/azure).

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#trackexception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: ../../azure-monitor/learn/java-quick-start.md
[javalogs]: java-trace-logs-25-beta.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[usage]: javascript.md
