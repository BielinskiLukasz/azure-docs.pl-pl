---
title: Monitorowanie wydajności aplikacji sieci web Java w systemie Linux — Azure | Dokumentacja firmy Microsoft
description: Rozszerzone monitorowanie wydajności aplikacji witryny sieci Web Java za pomocą wtyczki zebrane dla usługi Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 40c68f45-197a-4624-bf89-541eb7323002
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/24/2016
ms.author: mbullwin
ms.openlocfilehash: 528b344063dd9f84720ee55c4b3663c20127e5fe
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52995050"
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>zebrane: metryki wydajności systemu Linux w usłudze Application Insights


Aby zapoznać się z metryk wydajności systemu Linux w [usługi Application Insights](app-insights-overview.md), zainstaluj [zebrane](https://collectd.org/), wraz z jej usługi Application Insights wtyczki. To rozwiązanie typu open-source zbiera różne statystyki systemu i sieciowych.

Zazwyczaj służy zebrane Jeśli masz już [Instrumentacji usługi sieci web Java za pomocą usługi Application Insights][java]. Oferuje on większej ilości danych, aby pomóc zwiększyć wydajność aplikacji lub diagnozować problemy. 

![Przykładowe wykresy](./media/app-insights-java-collectd/sample.png)

## <a name="get-your-instrumentation-key"></a>Uzyskaj klucz Instrumentacji
W [portalu Microsoft Azure](https://portal.azure.com), otwórz [usługi Application Insights](app-insights-overview.md) zasobów, w którym ma się pojawić się dane. (Lub [Utwórz nowy zasób](app-insights-create-new-resource.md).)

Wykonaj kopię klucza instrumentacji, który identyfikuje zasób.

![Przeglądaj wszystko, otwórz zasób usługi w rozwijanej podstawy, a następnie wybierz, skopiuj klucz Instrumentacji](./media/app-insights-java-collectd/02-props.png)

## <a name="install-collectd-and-the-plug-in"></a>Zainstaluj zebrane i wtyczki
Na komputerach serwerów Linux:

1. Zainstaluj [zebrane](https://collectd.org/) wersji 5.4.0 lub nowszej.
2. Pobierz [wtyczką składnik zapisywania usługi Application Insights zebrane](https://aka.ms/aijavasdk). Zanotuj numer wersji.
3. Wtyczka JAR do kopiowania `/usr/share/collectd/java`.
4. Edytuj `/etc/collectd/collectd.conf`:
   * Upewnij się, że [wtyczki Java](https://collectd.org/wiki/index.php/Plugin:Java) jest włączona.
   * Zaktualizuj JVMArg dla java.class.path, aby uwzględnić Poniższy plik JAR. Numer wersji do dopasowania, które można pobrać aktualizacji:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Dodaj następujący fragment kodu przy użyciu klucza Instrumentacji z zasobu:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

W tym miejscu znajduje się przykładowy plik konfiguracji:

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

Konfigurowanie innych [wtyczek zebrane](https://collectd.org/wiki/index.php/Table_of_Plugins), który może zbierać różne dane z różnych źródeł.

Uruchom ponownie zebrane zgodnie z opisem w jego [ręczne](https://collectd.org/wiki/index.php/First_steps).

## <a name="view-the-data-in-application-insights"></a>Wyświetlanie danych w usłudze Application Insights
Otwórz zasób usługi Application Insights [Eksploratora metryk i dodać wykresy][metrics], wybierając metryki mają być wyświetlane z kategorię niestandardową.

![](./media/app-insights-java-collectd/result.png)

Domyślnie metryki są agregowane dla wszystkich maszyn hosta, z których zostały pobrane metryki. Aby wyświetlić metryki na hoście, w bloku szczegóły wykresu, należy włączyć grupowanie, a następnie wybierz grupowanie według hosta zebrane.

## <a name="to-exclude-upload-of-specific-statistics"></a>Aby wyłączyć przekazywanie poszczególnych statystyk
Domyślnie wtyczka usługi Application Insights wysyła wszystkie dane zebrane przez wszystkie włączone zebrane odczytu wtyczek. 

Aby wykluczyć danych z konkretnych źródeł danych lub wtyczek:

* Przeprowadź edycję pliku konfiguracji. 
* W `<Plugin ApplicationInsightsWriter>`, Dodaj dyrektywę wiersze następująco:

| — Dyrektywa | Efekt |
| --- | --- |
| `Exclude disk` |Wyklucz wszystkie dane zebrane przez `disk` wtyczki |
| `Exclude disk:read,write` |Wyklucz źródła o nazwie `read` i `write` z `disk` wtyczki. |

Oddzielnych dyrektyw przy użyciu nowego wiersza.

## <a name="problems"></a>Problemy?
*Nie widzę danych w portalu*

* Otwórz [wyszukiwania] [ diagnostic] aby zobaczyć, jeśli nieuaktywnionego nieprzetworzonych zdarzeń. Czasami dłużej wyświetlane w Eksploratorze metryk.
* Konieczne może być [Ustaw wyjątki zapory dla danych wychodzących](app-insights-ip-addresses.md)
* Włącz śledzenie we wtyczce usługi Application Insights. Dodaj następujący wiersz w ramach `<Plugin ApplicationInsightsWriter>`:
  * `SDKLogger true`
* Otwórz terminal i uruchom zebrane w trybie informacji pełnej, aby zobaczyć wszelkie problemy, które są zgłaszane jest:
  * `sudo collectd -f`

## <a name="known-issue"></a>Znany problem

Wtyczkę usługi Application Insights zapisu jest niezgodna z niektórych wtyczek odczytu. Niektóre wtyczki Wyślij czasami "NaN", gdzie wtyczką Application Insights oczekuje, że liczba zmiennoprzecinkowa.

Objaw: Zebrane dziennik zawiera informacje o błędy, które zawierają "SI:... SyntaxError: nieoczekiwany token N ".

Obejście: Wykluczanie danych zbieranych przez wtyczki zapisu problem. 

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md


