---
title: Co to jest usługa Azure Application Insights? | Microsoft Docs
description: Zarządzanie wydajnością aplikacji i śledzenie użycia działającej aplikacji internetowej.  Wykrywanie, klasyfikacja i diagnozowanie problemów, rozumienie, jak użytkownicy używają Twojej aplikacji.
ms.topic: overview
ms.date: 06/03/2019
ms.custom: mvc
ms.openlocfilehash: e7cde3ca47afd4db072b734c797a42ecd5a053aa
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79136803"
---
# <a name="what-is-application-insights"></a>Co to jest usługa Application Insights?
Usługa Application Insights, funkcja [usługi Azure Monitor,](../overview.md)jest usługą zarządzania wydajnością aplikacji (APM) dla deweloperów i specjalistów DevOps. Użyj go do monitorowania aplikacji na żywo. Automatycznie wykrywa anomalie wydajności i zawiera zaawansowane narzędzia analityczne ułatwiające diagnozowanie problemów i zrozumienie, co użytkownicy faktycznie robią z aplikacją.  Usługa ta pomaga w ciągłym doskonaleniu wydajności i użyteczności tworzonych rozwiązań. Działa w przypadku aplikacji na różnych platformach, w tym .NET, Node.js i Java EE, hostowanych lokalnie w środowisku hybrydowym lub w dowolnej chmurze publicznej. Integruje się ona z procesem DevOps i ma punkty połączenia z szeroką gamą narzędzi programistycznych. Może monitorować i analizować dane telemetryczne z aplikacji mobilnych dzięki integracji z usługą Visual Studio App Center.

## <a name="how-does-application-insights-work"></a>Jak działa usługa Application Insights?
Zainstaluj mały pakiet instrumentacji w swojej aplikacji i skonfiguruj zasób usługi Application Insights w witrynie Microsoft Azure Portal. Instrumentacja monitoruje aplikację i wysyła dane telemetryczne do usługi Azure Monitor. (Aplikację można uruchomić gdziekolwiek — nie musi być ona hostowana na platformie Azure).

Instrumentacja może obejmować nie tylko aplikację usługi sieci Web, ale również wszystkie składniki tła i skrypty JavaScript na samych stronach sieci Web. 

![Instrumentacja usługi Application Insights w Twojej aplikacji wysyła dane telemetryczne do zasobu usługi Application Insights.](./media/app-insights-overview/diagram.png)

Ponadto możesz ściągnąć dane telemetryczne ze środowisk hostów, takich jak liczniki wydajności, diagnostyka platformy Azure lub dzienniki platformy Docker. Możesz też skonfigurować testy sieci Web, które okresowo wysyłają syntetyczne żądania do usługi sieci Web.

Wszystkie te strumienie telemetryczne są zintegrowane z usługą Azure Monitor. W witrynie Azure portal można zastosować zaawansowane narzędzia analityczne i wyszukiwania do nieprzetworzonych danych.

### <a name="whats-the-overhead"></a>Co to jest narzut?
Wpływ na wydajność Twojej aplikacji jest bardzo mały. Wywołania śledzenia nie powodują blokowania oraz są łączone w partie i wysłane w oddzielnym wątku.

## <a name="what-does-application-insights-monitor"></a>Co monitoruje usługa Application Insights?

Usługa Application Insights jest przeznaczona dla zespołu deweloperów po to, aby pomóc zrozumieć, jak działa Twoja aplikacja i jak jest używana. Monitoruje ona:

* **Liczby żądań, czasy reakcji i współczynniki błędów** — dowiedz się, które strony są najbardziej popularne, o jakiej porze dnia i gdzie są Twoi użytkownicy. Zobacz, które strony działają najlepiej. Jeśli Twoje czasy odpowiedzi i częstotliwości awarii są duże, gdy jest więcej żądań, być może masz problem z zasobami. 
* **Współczynniki zależności, czasy reakcji i współczynniki błędów** — dowiedz się, czy usługi zewnętrzne nie spowalniają pracy.
* **Wyjątki** — analizowanie zagregowanych danych statystycznych lub wybieranie określonych wystąpień i przechodzenie do szczegółów śladu stosu i powiązanych żądań. Są zgłaszane zarówno wyjątki serwera, jak i przeglądarki.
* **Wydajność ładowania i wyświetleń stron** — zgłoszona przez przeglądarki użytkowników.
* **Wywołania AJAX** ze stron sieci Web — liczba, czasy reakcji i współczynniki błędów.
* **Liczby użytkowników i sesji**.
* **Liczniki wydajności** z serwerów systemu Windows lub Linux, takie jak użycie procesora CPU, pamięci i sieci. 
* **Diagnostyka hosta** z platformy Docker lub Azure. 
* **Diagnostyczne dzienniki śledzenia** z Twojej aplikacji — dzięki temu możesz skorelować zdarzenia śledzenia z żądaniami.
* **Niestandardowe zdarzenia i metryki**, które samodzielnie zapisujesz w kodzie klienta lub serwera, do śledzenia zdarzeń biznesowych, takich jak sprzedane towary lub wygrane gry.

## <a name="where-do-i-see-my-telemetry"></a>Gdzie mogę zobaczyć swoją telemetrię?

Istnieje wiele sposobów eksploracji danych. Zapoznaj się z następującymi artykułami:

|  |  |
| --- | --- |
| [**Inteligentne wykrywanie i ręczne alerty**](../../azure-monitor/app/proactive-diagnostics.md)<br/>Skonfiguruj automatyczne alerty, które dostosowują się do normalnych wzorców telemetrii aplikacji i wyzwalają, gdy istnieje coś poza zwykłym wzorcem. Można również [ustawić alerty](../../azure-monitor/app/alerts.md) na określonych poziomach metryk niestandardowych lub standardowych. |![Przykładowy alert](./media/app-insights-overview/alerts-tn.png) |
| [**Mapa aplikacji**](../../azure-monitor/app/app-map.md)<br/>Poznaj składniki aplikacji, za pomocą kluczowych metryk i alertów. |![Mapa aplikacji](./media/app-insights-overview/appmap-tn.png)  |
| [**Profiler**](../../azure-monitor/app/profiler.md)<br/>Inspekcja profilów wykonania próbek żądań. |![Profiler](./media/app-insights-overview/profiler.png) |
| [**Analiza użycia**](../../azure-monitor/app/usage-overview.md)<br/>Analiza segmentacji i utrzymywania użytkowników.|![Narzędzie utrzymywania](./media/app-insights-overview/retention.png) |
| [**Wyszukiwanie diagnostyczne danych dla wystąpienia**](../../azure-monitor/app/diagnostic-search.md)<br/>Wyszukiwanie i filtrowanie zdarzeń, takich jak żądania, wyjątki, wywołania zależności, dzienniki śledzenia i wyświetlenia stron.  |![Wyszukiwanie telemetrii](./media/app-insights-overview/search-tn.png) |
| [**Eksplorator metryk dla danych zagregowanych**](../../azure-monitor/app/metrics-explorer.md)<br/>Eksploruj, filtruj i segmentuj zagregowane dane, takie jak liczby żądań, błędów i wyjątków, czasy reakcji, czasy ładowania stron. |![Metryki](./media/app-insights-overview/metrics-tn.png) |
| [**Pulpity nawigacyjne**](../../azure-monitor/app/overview-dashboard.md)<br/>Połącz dane z wielu zasobów i udostępnij innym osobom. Opcja ta doskonale nadaje się dla aplikacji wieloskładnikowych i ciągłego wyświetlania w pomieszczeniu zespołu. |![Przykładowy pulpit nawigacyjny](./media/app-insights-overview/dashboard-tn.png) |
| [**Transmisja strumieniowa metryk na żywo**](../../azure-monitor/app/live-stream.md)<br/>Podczas wdrażania nowej kompilacji obejrzyj te wskaźniki wydajności prawie w czasie rzeczywistym, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami. |![Przykładowe metryki na żywo](./media/app-insights-overview/live-metrics-tn.png) |
| [**Analytics**](../../azure-monitor/app/analytics.md)<br/>Odpowiedz na trudne pytania dotyczące wydajności i użycia Twojej aplikacji za pomocą tego zaawansowanego języka zapytań. |![Przykładowa analiza](./media/app-insights-overview/analytics-tn.png) |
| [**Visual Studio**](../../azure-monitor/app/visual-studio.md)<br/>Zobacz dane dotyczące wydajności w kodzie. Przejdź do kodu ze śladów stosu.|![Visual Studio](./media/app-insights-overview/visual-studio-tn.png) |
| [**Debuger migawek**](../../azure-monitor/app/snapshot-debugger.md)<br/>Debuguje próbki migawek pobrane z operacji na żywo, używając wartości parametrów.|![Visual Studio](./media/app-insights-overview/snapshot.png) |
| [**Power BI**](../../azure-monitor/app/export-power-bi.md )<br/>Integruje metryki użycia z innymi analizami biznesowymi.| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**INTERFEJS API ODPOCZYNKU**](https://dev.applicationinsights.io/)<br/>Napisz kod, aby uruchamiać zapytania dla swoich metryk i danych pierwotnych.| ![Interfejs API REST](./media/app-insights-overview/rest-tn.png) |
| [**Eksport ciągły**](../../azure-monitor/app/export-telemetry.md)<br/>Zbiorczy eksport danych pierwotnych do magazynu zaraz po ich odebraniu. |![Eksportowanie](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>Jak mogę używać usługi Application Insights?

### <a name="monitor"></a>Monitorowanie
Zainstaluj usługę Application Insights w swojej aplikacji, skonfiguruj [testy sieci Web dostępności](../../azure-monitor/app/monitor-web-app-availability.md) oraz:

* Wyewidencjonuj domyślny [pulpit nawigacyjny aplikacji](../../azure-monitor/app/overview-dashboard.md) dla pokoju zespołu, aby mieć oko na obciążenie, czas reakcji i wydajność zależności, obciążenia strony i wywołania AJAX.
* Wykryj, jakie żądania są najwolniejsze i najczęściej kończą się niepowodzeniem.
* Obejrzyj [transmisję strumieniową na żywo](../../azure-monitor/app/live-stream.md) podczas wdrażania nowej wersji, aby od razu dowiedzieć się o wszelkich pogorszeniach.

### <a name="detect-diagnose"></a>Wykrywanie, diagnozowanie
Po odebraniu alertu lub odnalezieniu problemu:

* Oceń, na ilu użytkowników ma to wpływ.
* Skoreluj błędy z wyjątkami, wywołaniami zależności i śladami.
* Sprawdź profilera, migawki, zrzuty stosu i dzienniki śledzenia.

### <a name="build-measure-learn"></a>Kompilowanie, mierzenie, poznawanie
[Zmierz skuteczność](../../azure-monitor/app/usage-overview.md) każdej nowej funkcji, którą wdrażasz.

* Zaplanuj pomiar sposobu używania nowych funkcji środowiska użytkownika lub biznesowych przez klientów.
* Zapisz niestandardową telemetrię do kodu.
* Oprzyj następny cyklu rozwoju na twardych dowodach ze swojej telemetrii.

## <a name="get-started"></a>Wprowadzenie
Usługa Application Insights jest jedną z wielu usług hostowanych na platformie Microsoft Azure, a dane telemetryczne są przesyłane do analizy i prezentacji. Dlatego przed wykonaniem jakichkolwiek innych czynności będzie potrzebna subskrypcja platformy [Microsoft Azure](https://azure.com). Rejestracja jest bezpłatna, a jeśli wybierzesz podstawowy [plan cenowy](https://azure.microsoft.com/pricing/details/application-insights/) usługi Application Insights, nie będzie opłat do chwili, gdy aplikacja rozrośnie się na tyle, aby mieć znaczne wykorzystanie. Jeśli Twoja organizacja ma już subskrypcję, może ona dodać do niej Twoje konto Microsoft.

Istnieje kilka sposobów, aby rozpocząć. Zacznij od tego, co najlepiej sprawdza się w Twoim przypadku. Resztę możesz dodać później.

* **W czasie wykonywania: wdróż instrumentację aplikacji internetowej na serwerze.** Idealny dla aplikacji już wdrożonych. Pozwala uniknąć wszelkich aktualizacji kodu.
  * [**aplikacje ASP.NET lub ASP.NET Core hostowane w aplikacjach Azure Web Apps**](../../azure-monitor/app/azure-web-apps.md)
  * [**ASP.NET aplikacji hostowanych w usługach IIS na platformie Azure VM lub zestaw skalowania maszyny wirtualnej platformy Azure**](../../azure-monitor/app/azure-vm-vmss-apps.md)
  * [**ASP.NET aplikacji hostowanych w lokalnej maszynie wirtualnej usług IIS**](../../azure-monitor/app/monitor-performance-live-website-now.md)
* **W czasie tworzenia: dodaj usługę Application Insights do swojego kodu.** Umożliwia dostosowanie kolekcji telemetrii i wysłanie dodatkowych danych telemetrycznych.
  * [Aplikacje ASP.NET](../../azure-monitor/app/asp-net.md)
  * [ASP.NET podstawowe aplikacje](../../azure-monitor/app/asp-net-core.md)
  * [Aplikacje konsoli .NET](../../azure-monitor/app/console.md)
  * [Java](../../azure-monitor/app/java-get-started.md)
  * [Node.js](../../azure-monitor/app/nodejs.md)
  * [Python](../../azure-monitor/app/opencensus-python.md)
  * [Inne platformy](../../azure-monitor/app/platforms.md)
* **[Instrumentacja stron internetowych](../../azure-monitor/app/javascript.md)** dla widoku strony, wywołań AJAX i innej telemetrii po stronie klienta.
* **[Analizowanie użycia aplikacji mobilnej](../../azure-monitor/learn/mobile-center-quickstart.md)** dzięki integracji z usługą Visual Studio App Center.
* **[Testy dostępności](../../azure-monitor/app/monitor-web-app-availability.md)** — regularnie używaj polecenia ping dla witryny sieci Web z naszych serwerów.

## <a name="next-steps"></a>Następne kroki
Rozpocznij pracę w czasie wykonywania za pomocą rozwiązań:

* [Zestaw aplikacji hostowanych usługami IIS dla maszyn wirtualnych platformy Azure i platformy Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Serwer usług IIS](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Aplikacje azure web](../../azure-monitor/app/azure-web-apps.md)

Rozpocznij pracę w czasie programowania za pomocą rozwiązań:

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)


## <a name="support-and-feedback"></a>Pomoc techniczna i opinie
* Pytania i problemy:
  * [Rozwiązywanie problemów][qna]
  * [Forum MSDN](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
  * [Stackoverflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* Twoje sugestie:
  * [Platforma UserVoice](https://feedback.azure.com/forums/357324-application-insights/filters/top)
* Blog:
  * [Blog usługi Application Insights](https://azure.microsoft.com/blog/tag/application-insights)

<!--Link references-->

[android]: ../../azure-monitor/learn/mobile-center-quickstart.md
[azure]: ../../insights-perf-analytics.md
[client]: ../../azure-monitor/app/javascript.md
[desktop]: ../../azure-monitor/app/windows-desktop.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[ios]: ../../azure-monitor/learn/mobile-center-quickstart.md
[java]: ../../azure-monitor/app/java-get-started.md
[knowUsers]: app-insights-web-track-usage.md
[platforms]: ../../azure-monitor/app/platforms.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
