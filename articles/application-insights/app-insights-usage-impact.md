---
title: Usługi Azure Application Insights użycia wpływ | Dokumentacja firmy Microsoft
description: Analizowanie jak różne właściwości potencjalnie wpływu na prędkość konwersji dla części aplikacji.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 01/25/2018
ms.reviewer: daviste
ms.author: mbullwin
ms.openlocfilehash: 9188776fdd213f01523069b08bd898f48bee57a4
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091485"
---
# <a name="impact-analysis-with-application-insights"></a>Analiza wpływu za pomocą usługi Application Insights

Wpływ analizuje, jak krótszy czas ładowania i inne właściwości wpływ współczynników konwersji dla różnych części aplikacji. Aby przełączyć bardziej precyzyjnie, odnajduje jak **żadnego wymiaru** z **widok strony**, **zdarzenie niestandardowe**, lub **żądania** ma wpływ na użycie różne **widok strony** lub **zdarzenie niestandardowe**. 

![Narzędzie wpływ](./media/app-insights-usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>Nadal nie masz pewności wpływu na działanie?

Jest jednym ze sposobów myślenia wpływu ultimate narzędziem do rozliczania argumenty osobom o jak wpływ na spowolnienie w pewien aspekt witryny czy użytkownicy trzymaj się zespołu. Podczas gdy użytkownicy mogą tolerować pewna ilość wolnego, wpływ udostępnia wgląd w najlepszy sposób równoważyć optymalizacji i wydajność, aby zmaksymalizować konwersji użytkownika.

Ale analizowanie wydajności określony podzbiór możliwości jego wpływu. Ponieważ wpływ obsługuje niestandardowe zdarzenia i wymiarów, odpowiadanie na pytania, takie jak sposób wyboru przeglądarki użytkownika użytymi przy użyciu różnych szybkości konwersji są zaledwie kilku kliknięć.

![Zrzut ekranu konwersja przez przeglądarki](./media/app-insights-usage-impact/0004-browsers.png)

> [!NOTE]
> Zasób usługi Application Insights musi zawierać wyświetleń stron lub zdarzeń niestandardowych, aby użyć narzędzia wpływu. [Dowiedz się, jak skonfigurować aplikację do zbierania wyświetleń stron, które automatycznie za pomocą zestawu SDK języka JavaScript usługi Application Insights](app-insights-javascript.md). Należy pamiętać o tym, ponieważ analizujesz korelacji, spraw rozmiar próbki.
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>To czas ładowania strony wpływające na ile osób przekonwertować na mojej stronie?

Aby rozpocząć, odpowiadanie na pytania za pomocą narzędzia wpływu, wybierz widok strony początkowej, zdarzenie niestandardowe lub żądanie.

![Narzędzie wpływ](./media/app-insights-usage-impact/0002-dropdown.png)

1. Wybierz widok strony z **dla widoku strony** listy rozwijanej.
2. Pozostaw **analizować sposób jego** listy rozwijanej na domyślny wybór **czas trwania** (w tym kontekście **czas trwania** jest aliasem dla **czas ładowania strony**.)
3. Aby uzyskać **ma wpływ na użycie** listy rozwijanej wybierz zdarzenie niestandardowe. To zdarzenie powinna odpowiadać elementu interfejsu użytkownika widoku strony, który został wybrany w kroku 1.

![Zrzut ekranu przedstawiający wyniki](./media/app-insights-usage-impact/0003-results.png)

W tym wystąpieniu jako **stronę produktu** czas ładowania zwiększa szybkość przy konwersji **zakupu produktu kliknięto** ulegnie awarii. Oparta na dystrybucji powyżej, czas trwania obciążenia optymalne strony 3,5 sekund może przeznaczone do osiągnięcia potencjalnych współczynnik konwersji 55%. Dodatkowe ulepszenia wydajności, aby skrócić czas ładowania poniżej sekundy 3.5 nie obecnie skorelować z konwersji dodatkowe korzyści.

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>Co zrobić, jeśli monitorowane wyświetleń stron lub czasów ładowania w niestandardowy sposób?

Wpływ obsługuje standardowe i niestandardowe właściwości i pomiarów. Użyć dowolnej nazwy. Zamiast czas trwania stosowanie filtrów na zdarzeniach podstawowego i pomocniczego, aby uzyskać bardziej szczegółowe informacje.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Czy użytkownicy z różnych krajów lub regionów są konwertowane w różnym tempie?

1. Wybierz widok strony z **dla widoku strony** listy rozwijanej.
2. Wybierz "Kraj lub region" **analizować sposób jego** listy rozwijanej
3. Aby uzyskać **ma wpływ na użycie** listy rozwijanej wybierz niestandardowe zdarzenia, które odnosi się do elementu interfejsu użytkownika widoku strony wybrana w kroku 1.

W tym przypadku wyniki nie pasuje do modelu ciągłą osią x tak samo, jak w pierwszym przykładzie. Zamiast tego są prezentowane wizualizacji, które są podobne do posegmentowanych lejka. Sortuj według **użycia** Aby wyświetlić zmiany konwersji, aby Twoje niestandardowe zdarzenia na podstawie kraju.


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>Jak narzędzie wpływ obliczyć te współczynniki konwersji?

Kulisy, narzędzie wpływ opiera się na [współczynnik korelacji] (https://en.wikipedia.org/wiki/Pearson_correlation_coefficient). Wyniki są obliczane od -1 do 1 na -1, reprezentujący zero korelacji i 1 reprezentujących dodatnią korelacji.

Podział podstawowe działania analizy wpływu jest następująca:

Pozwól _A_ = strona główna widok/niestandardowego zdarzenia/żądania wybierz z pierwszej listy rozwijanej. (**Dla widoku strony**).

Pozwól _B_ = dodatkowej strony widoku/niestandardowe zdarzenie, możesz wybrać (**ma wpływ na użycie**).

Wpływ patrzy na przykład wszystkie sesje użytkowników w wybranym zakresie czasu. Dla każdej sesji wygląda dla każdego wystąpienia _A_.

Sesje, następnie są podzielone na dwa różne rodzaje elementu _subsessions_ na podstawie jednej z dwóch sytuacji:

- Sesję przekonwertowanego podrzędną składa się z sesji, kończąc _B_ zdarzeń i obejmują wszystkie _A_ zdarzenia występujące w programach starszych niż program _B_.
- Nieprzekonwertowane subsession występuje podczas wszystkich _A_użytkownika ma miejsce bez terminalu _B_.

Ostatecznie obliczania wpływ różni się w zależności od tego, czy analizujemy według metryki lub według wymiaru. Dla wszystkich metryk _A_użytkownika w sesję podrzędną są uśredniane. Natomiast dla wymiarów wartość każdego _A_ wspiera _1/N_ wartość przypisana do _B_ gdzie _N_ jest liczba _A_użytkownika w sesję nadrzędną.

## <a name="next-steps"></a>Kolejne kroki

- Aby umożliwić użycie środowiska, Rozpocznij wysyłanie [zdarzenia niestandardowe](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) lub [wyświetlenia strony](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Jeśli już wysyłać niestandardowe zdarzenia lub wyświetlenia stron, zapoznaj się z narzędzia obciążenia, aby dowiedzieć się, jak używać usługi przez użytkowników.
    - [Lejki](usage-funnels.md)
    - [Przechowywanie](app-insights-usage-retention.md)
    - [User Flows (Przepływy użytkowników)](app-insights-usage-flows.md)
    - [Skoroszyty](app-insights-usage-workbooks.md)
    - [Dodawanie kontekstu użytkownika](app-insights-usage-send-user-context.md)