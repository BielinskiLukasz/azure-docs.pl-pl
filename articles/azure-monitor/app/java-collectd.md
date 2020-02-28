---
title: Monitorowanie wydajności aplikacji sieci Web w języku Java w systemie Linux — Azure | Microsoft Docs
description: Rozszerzone monitorowanie wydajności aplikacji w witrynie Java za pomocą zebranej wtyczki dla Application Insights.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 687f97c305bffdfb408feb314ccded4f93ac574a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660737"
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>zebrane: metryki wydajności systemu Linux w Application Insights


Aby poznać metryki wydajności systemu Linux w [Application Insights](../../azure-monitor/app/app-insights-overview.md), należy zainstalować [zebrane](https://collectd.org/)dane wraz z wtyczką Application Insights. To rozwiązanie "open source" gromadzi różne statystyki systemu i sieci.

Zazwyczaj należy używać zebranych danych, jeśli [Instrumentacja usługi sieci Web języka Java][java]została już zastosowana za pomocą Application Insights. Zapewnia więcej danych, które ułatwiają zwiększenie wydajności aplikacji lub diagnozowanie problemów. 

## <a name="get-your-instrumentation-key"></a>Pobieranie klucza Instrumentacji
W [Microsoft Azure Portal](https://portal.azure.com)otwórz zasób [Application Insights](../../azure-monitor/app/app-insights-overview.md) , w którym mają być wyświetlane dane. (Lub [Utwórz nowy zasób](../../azure-monitor/app/create-new-resource.md )).

Skopiuj klucz instrumentacji, który identyfikuje zasób.

![Przeglądaj wszystko, Otwórz zasób, a następnie na liście rozwijanej podstawowe dane wybierz i skopiuj klucz Instrumentacji](./media/java-collectd/instrumentation-key-001.png)

## <a name="install-collectd-and-the-plug-in"></a>Instalacja zebrana i wtyczka
Na komputerach z systemem Linux:

1. Zainstaluj [zebraną](https://collectd.org/) wersję 5.4.0 lub nowszą.
2. Pobierz [wtyczkę Application Insights zebranych modułów zapisujących](https://github.com/microsoft/ApplicationInsights-Java/tree/master/collectd/src/main/java/com/microsoft/applicationinsights/collectd/internal). Zanotuj numer wersji.
3. Skopiuj wtyczkę JAR do `/usr/share/collectd/java`.
4. Edytuj `/etc/collectd/collectd.conf`:
   * Upewnij się, że [Wtyczka Java](https://collectd.org/wiki/index.php/Plugin:Java) jest włączona.
   * Zaktualizuj JVMArg dla elementu Java. Class. Path, aby dołączyć następujący plik JAR. Zaktualizuj numer wersji, aby dopasować go do pobranego:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Dodaj ten fragment kodu przy użyciu klucza Instrumentacji z zasobu:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Oto część przykładowego pliku konfiguracji:

```XML

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"

      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
    ...
```

Skonfiguruj inne [zebrane wtyczki](https://collectd.org/wiki/index.php/Table_of_Plugins), które mogą zbierać różne dane z różnych źródeł.

Uruchom ponownie zebrane zgodnie z jego [instrukcją ręczną](https://collectd.org/wiki/index.php/First_steps).

## <a name="view-the-data-in-application-insights"></a>Wyświetlanie danych w Application Insights
W zasobie Application Insights Otwórz [metryki i Dodaj wykresy][metrics], wybierając metryki, które mają być wyświetlane z kategorii niestandardowej.

Domyślnie metryki są agregowane we wszystkich maszynach hostów, z których zostały zebrane metryki. Aby wyświetlić metryki na hosta, w bloku szczegóły wykresu Włącz grupowanie, a następnie wybierz pozycję Grupuj według hosta.

## <a name="to-exclude-upload-of-specific-statistics"></a>Aby wykluczyć przekazywanie określonych statystyk
Domyślnie wtyczka Application Insights wysyła wszystkie dane zbierane przez wszystkie włączone zebrane Dodatki "read". 

Aby wykluczyć dane z określonych wtyczek lub źródeł danych:

* Edytuj plik konfiguracji. 
* W `<Plugin ApplicationInsightsWriter>`Dodaj wiersze dyrektywy w następujący sposób:

| — Dyrektywa | Efekt |
| --- | --- |
| `Exclude disk` |Wyklucz wszystkie dane zebrane przez wtyczkę `disk` |
| `Exclude disk:read,write` |Wykluczanie źródeł o nazwie `read` i `write` z wtyczki `disk`. |

Oddzielne dyrektywy z wierszem nowego wiersza.

## <a name="problems"></a>Problemy?
*Nie widzę danych w portalu*

* Otwórz [Wyszukiwanie][diagnostic] , aby sprawdzić, czy zdarzenia RAW dotarły do Ciebie. Czasami pojawiają się one dłużej w Eksploratorze metryk.
* Może być konieczne [ustawienie wyjątków zapory dla danych wychodzących](../../azure-monitor/app/ip-addresses.md)
* Włącz śledzenie w dodatku Application Insights. Dodaj następujący wiersz w `<Plugin ApplicationInsightsWriter>`:
  * `SDKLogger true`
* Otwórz Terminal i Rozpocznij zbieranie w trybie pełnym, aby wyświetlić wszystkie problemy, które zgłasza:
  * `sudo collectd -f`

## <a name="known-issue"></a>Znany problem

Wtyczka zapisu Application Insights jest niezgodna z pewnymi wtyczkami odczytu. Niektóre wtyczki czasami wysyłają "NaN", gdzie wtyczka Application Insights oczekuje liczby zmiennoprzecinkowej.

Objaw: zebrany dziennik przedstawia błędy, które zawierają wartość "AI:... SyntaxError: Nieoczekiwany token N ".

Obejście: wykluczanie danych zebranych przez wtyczki zapisu problemu. 

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md


