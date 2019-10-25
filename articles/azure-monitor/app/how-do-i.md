---
title: Jak mogę... na platformie Azure Application Insights | Microsoft Docs
description: Często zadawane pytania w Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/04/2017
ms.openlocfilehash: 28881403e4938376cc1912227bdff51aa5f069cf
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72817366"
---
# <a name="how-do-i--in-application-insights"></a>Jak mogę (...) w usłudze Application Insights?
## <a name="get-an-email-when-"></a>Otrzymuj wiadomość e-mail, gdy...
### <a name="email-if-my-site-goes-down"></a>Wyślij wiadomość e-mail, jeśli moja witryna ulegnie awarii
Ustaw [test sieci Web dostępności](../../azure-monitor/app/monitor-web-app-availability.md).

### <a name="email-if-my-site-is-overloaded"></a>Wyślij wiadomość e-mail, jeśli moja witryna jest przeciążona
Ustaw [alert](../../azure-monitor/app/alerts.md) dotyczący **czasu odpowiedzi serwera**. Wartość progowa z przedziału od 1 do 2 sekund powinna być poprawna.

![](./media/how-do-i/030-server.png)

Aplikacja może również wyświetlać oznaki szczepu, zwracając kody błędów. Ustaw Alert dla **żądań zakończonych niepowodzeniem**.

Jeśli chcesz ustawić alert dla **wyjątków serwera**, może być konieczne wykonanie [dodatkowych czynności konfiguracyjnych](../../azure-monitor/app/asp-net-exceptions.md) w celu wyświetlenia danych.

### <a name="email-on-exceptions"></a>Poczta e-mail przy wyjątkach
1. [Skonfiguruj Monitorowanie wyjątków](../../azure-monitor/app/asp-net-exceptions.md)
2. [Ustawianie alertu](../../azure-monitor/app/alerts.md) dotyczącego metryki liczby wyjątków

### <a name="email-on-an-event-in-my-app"></a>Wyślij wiadomość e-mail dotyczącą zdarzenia w mojej aplikacji
Załóżmy, że chcesz otrzymać wiadomość e-mail po wystąpieniu określonego zdarzenia. Application Insights nie zapewnia bezpośrednio tej funkcji, ale może [wysłać Alert, gdy Metryka przekroczy próg](../../azure-monitor/app/alerts.md).

Alerty można ustawiać w [niestandardowych metrykach](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric), chociaż nie zdarzeniach niestandardowych. Napisz kod, aby zwiększyć metrykę w przypadku wystąpienia zdarzenia:

    telemetry.TrackMetric("Alarm", 10);

oraz

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Ponieważ alerty mają dwa stany, należy wysłać wartość niską, gdy zostanie zgłoszony alert, który zakończył się:

    telemetry.TrackMetric("Alarm", 0.5);

Utwórz wykres w [Eksploratorze metryk](../../azure-monitor/app/metrics-explorer.md) , aby zobaczyć alarm:

![](./media/how-do-i/010-alarm.png)

Teraz Ustaw Alert do uruchomienia, gdy Metryka spadnie powyżej wartości średniej przez krótki czas:

![](./media/how-do-i/020-threshold.png)

Ustaw średni okres jako minimum.

Będziesz otrzymywać wiadomości e-mail zarówno wtedy, gdy Metryka spadnie powyżej, jak i poniżej wartości progowej.

Oto niektóre ważne kwestie:

* Alert ma dwa Stany ("Alert" i "dobra kondycja"). Stan jest oceniany tylko wtedy, gdy otrzymano metrykę.
* Wiadomość e-mail jest wysyłana tylko wtedy, gdy stan zmieni się. Jest to dlatego, że konieczne jest wysłanie metryk o wysokiej i niskiej wartości.
* Aby oszacować alert, średnia jest pobierana z wartości odebranych w poprzednim okresie. Jest to wykonywane za każdym razem, gdy otrzymasz metrykę, więc wiadomości e-mail mogą być wysyłane częściej niż ustawiony okres.
* Ponieważ wiadomości e-mail są wysyłane zarówno w "alertach", jak i w dobrej kondycji, warto rozważyć przemyśleć wydarzenie z jednym z nich jako warunek dwustanowy. Na przykład zamiast zdarzenia "ukończenie zadania" występuje warunek "zadanie w toku", w którym można otrzymywać wiadomości e-mail na początku i na końcu zadania.

### <a name="set-up-alerts-automatically"></a>Automatyczne Konfigurowanie alertów
[Tworzenie nowych alertów za pomocą programu PowerShell](../../azure-monitor/app/alerts.md#automation)

## <a name="use-powershell-to-manage-application-insights"></a>Zarządzanie Application Insights przy użyciu programu PowerShell
* [Tworzenie nowych zasobów](../../azure-monitor/app/powershell-script-create-resource.md)
* [Utwórz nowe alerty](../../azure-monitor/app/alerts.md#automation)

## <a name="separate-telemetry-from-different-versions"></a>Oddzielna Telemetria od różnych wersji

* Wiele ról w aplikacji: Użyj jednego zasobu Application Insights i przefiltruj w [cloud_Rolename](../../azure-monitor/app/app-map.md).
* Rozdzielenie wersji deweloperskich, testowych i wydań: Użyj różnych zasobów Application Insights. Pobierz klucze Instrumentacji z pliku Web. config. [Dowiedz się więcej](../../azure-monitor/app/separate-resources.md)
* Wersje kompilacji raportowania: Dodaj właściwość przy użyciu inicjatora telemetrii. [Dowiedz się więcej](../../azure-monitor/app/separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Monitorowanie serwerów zaplecza i aplikacji klasycznych
[Użyj modułu Windows Server SDK](../../azure-monitor/app/windows-desktop.md).

## <a name="visualize-data"></a>Wizualizowanie danych
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Pulpit nawigacyjny z metrykami z wielu aplikacji
* W [Eksploratorze metryki](../../azure-monitor/app/metrics-explorer.md)Dostosuj wykres i Zapisz go jako ulubiony. Przypnij ją do pulpitu nawigacyjnego platformy Azure.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Pulpit nawigacyjny z danymi z innych źródeł i Application Insights
* [Wyeksportuj dane telemetryczne do Power BI](../../azure-monitor/app/export-power-bi.md ).

Lub

* Użyj programu SharePoint jako pulpitu nawigacyjnego, wyświetlając dane w składnikach sieci Web programu SharePoint. [Użyj eksportu ciągłego i Stream Analytics, aby wyeksportować do bazy danych SQL](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md).  Użyj PowerView, aby przeanalizować bazę danych i utworzyć składnik Web Part programu SharePoint dla PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Odfiltruj użytkowników anonimowych lub uwierzytelnionych
Jeśli użytkownicy logują się, możesz ustawić [Identyfikator uwierzytelnionego użytkownika](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users). (Nie odbywa się automatycznie).

Następnie można:

* Wyszukaj określone identyfikatory użytkowników

![](./media/how-do-i/110-search.png)

* Filtruj metryki do anonimowych lub uwierzytelnionych użytkowników

![](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Modyfikowanie nazw lub wartości właściwości
Utwórz [Filtr](../../azure-monitor/app/api-filtering-sampling.md#filtering). Pozwala to na modyfikowanie i filtrowanie danych telemetrycznych przed wysłaniem ich z aplikacji do Application Insights.

## <a name="list-specific-users-and-their-usage"></a>Wyświetlanie listy określonych użytkowników i ich użycie
Jeśli chcesz tylko [wyszukać określonych użytkowników](#search-specific-users), możesz ustawić [Identyfikator uwierzytelnionego użytkownika](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users).

Jeśli chcesz, aby lista użytkowników z danymi, takich jak strony, na których się pojawiają lub jak często się logują, są dostępne dwie opcje:

* [Ustaw identyfikator uwierzytelnionego użytkownika](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users), [Eksportuj do bazy danych](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) i użyj odpowiednich narzędzi do analizowania danych użytkownika.
* Jeśli masz tylko niewielką liczbę użytkowników, Wyślij zdarzenia niestandardowe lub metryki, używając danych interesujących jako wartość metryki lub nazwa zdarzenia i ustawiając identyfikator użytkownika jako właściwość. Aby analizować widoki stron, Zastąp standardowe wywołanie JavaScript trackPageView. Aby analizować dane telemetryczne po stronie serwera, użyj inicjatora telemetrii w celu dodania identyfikatora użytkownika do wszystkich danych telemetrycznych serwera. Następnie można filtrować i segmentować metryki oraz wyszukiwania według identyfikatora użytkownika.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Zmniejsz ruch z mojej aplikacji do Application Insights
* W [pliku ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)Wyłącz wszystkie moduły, które nie są potrzebne, takie jak moduł zbierający dane licznika wydajności.
* Użyj [próbkowania i filtrowania](../../azure-monitor/app/api-filtering-sampling.md) w zestawie SDK.
* Na stronach sieci Web Ogranicz liczbę wywołań AJAX raportowanych dla każdego widoku strony. W fragmencie kodu skryptu po `instrumentationKey:...` Wstaw: `,maxAjaxCallsPerView:3` (lub odpowiednią liczbę).
* Jeśli używasz [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric), Oblicz zagregowane dane partii wartości metryk przed wysłaniem wyniku. Istnieje Przeciążenie TrackMetric (), które zapewnia dla tego.

Dowiedz się więcej na temat [cen i przydziałów](../../azure-monitor/app/pricing.md).

## <a name="disable-telemetry"></a>Wyłącz telemetrię
Aby **dynamicznie zatrzymać i uruchomić** zbieranie danych telemetrycznych z serwera:

### <a name="aspnet-classic-applications"></a>ASP.NET klasyczne aplikacje

```csharp
    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

### <a name="other-applications"></a>Inne aplikacje
Nie zaleca się używania `TelemetryConfiguration.Active` pojedynczych w aplikacjach konsolowych lub ASP.NET Core.
Jeśli utworzono wystąpienie `TelemetryConfiguration`, `DisableTelemetry` do `true`.

W przypadku aplikacji ASP.NET Core można uzyskać dostęp do `TelemetryConfiguration` wystąpienia przy użyciu [iniekcji zależności ASP.NET Core](/aspnet/core/fundamentals/dependency-injection/). Więcej informacji znajdziesz w artykule [ApplicationInsights for ASP.NET Core Applications](../../azure-monitor/app/asp-net-core.md) .

## <a name="disable-selected-standard-collectors"></a>Wyłącz wybrane moduły standardowe
Można wyłączyć moduły zbierające standardowe (na przykład liczniki wydajności, żądania HTTP lub zależności).

* **ASP.NET aplikacje** — Usuń lub Dodaj komentarz do odpowiednich wierszy w [pliku ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)
* **ASP.NET Core Applications** — Użyj opcji konfiguracji modułów telemetrycznych w programie [ApplicationInsights ASP.NET Core](../../azure-monitor/app/asp-net-core.md#configuring-or-removing-default-telemetrymodules)

## <a name="view-system-performance-counters"></a>Wyświetlanie liczników wydajności systemu
Wśród metryk, które można wyświetlić w Eksploratorze metryk jest zestaw liczników wydajności systemu. Istnieje wstępnie zdefiniowany blok zatytułowany **serwery** , które wyświetlają kilka z nich.

![Otwórz zasób Application Insights i kliknij pozycję Serwery.](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Jeśli nie widzisz danych licznika wydajności
* **Serwer IIS** na komputerze lub na maszynie wirtualnej. [Zainstaluj Monitor stanu](../../azure-monitor/app/monitor-performance-live-website-now.md).
* **Witryna sieci Web platformy Azure** — nie obsługujemy jeszcze liczników wydajności. Istnieje kilka metryk dostępnych jako standardowa część panelu sterowania witryny sieci Web systemu Azure.
* **Serwer Unix** - [instalacji zebranej](../../azure-monitor/app/java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Aby wyświetlić więcej liczników wydajności
* Najpierw [Dodaj nowy wykres](../../azure-monitor/app/metrics-explorer.md) i sprawdź, czy licznik znajduje się w podstawowym zestawie oferowanym przez nas.
* W przeciwnym razie [Dodaj licznik do zestawu zebranego przez moduł licznika wydajności](../../azure-monitor/app/performance-counters.md).
