---
title: Model danych usługi Azure telemetria usługi Application Insights | Microsoft Docs
description: Omówienie modelu danych Application Insights
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/14/2019
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 50109d7ba4688606a5a4f1b813d15d78636b7817
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311780"
---
# <a name="application-insights-telemetry-data-model"></a>Application Insights model danych telemetrii

[Usługa Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) wysyła dane telemetryczne z aplikacji sieci web do Azure Portal, dzięki czemu można analizować wydajność i użycie aplikacji. Model telemetrii jest ustandaryzowany, dzięki czemu można utworzyć monitorowanie platformy i niezależnego od języka. 

Dane zbierane przez Application Insights modele są typowym wzorcem wykonywania aplikacji:

![Model aplikacji Application Insights](./media/data-model/application-insights-data-model.png)

Do monitorowania wykonywania aplikacji służą następujące typy telemetrii. Następujące trzy typy są zwykle automatycznie zbierane przez zestaw SDK Application Insights z platformy aplikacji sieci Web:

* [**Żądanie**](data-model-request-telemetry.md) — generowane w celu zarejestrowania żądania odebranego przez aplikację. Na przykład Application Insights Web SDK automatycznie generuje element telemetrii żądania dla każdego żądania HTTP, które otrzymuje aplikacja sieci Web. 

    **Operacja** jest wątkiem wykonywania, który przetwarza żądanie. Możesz również [napisać kod](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) do monitorowania innych typów operacji, takich jak "Wake up" w zadaniu lub funkcji sieci Web, która okresowo przetwarza dane.  Każda operacja ma identyfikator. Ten identyfikator, który może służyć do [grupowania](../../azure-monitor/app/correlation.md) wszystkich danych telemetrycznych generowanych podczas przetwarzania żądania przez aplikację. Każda operacja zakończy się powodzeniem lub kończy się niepowodzeniem i ma czas trwania.
* [**Exception**](data-model-exception-telemetry.md) — zazwyczaj reprezentuje wyjątek, który powoduje niepowodzenie operacji.
* [**Zależność**](data-model-dependency-telemetry.md) — reprezentuje wywołanie z aplikacji do zewnętrznej usługi lub magazynu, takiego jak interfejs API REST lub SQL. W ASP.NET, wywołania zależności do SQL są definiowane przez `System.Data`. Wywołania punktów końcowych HTTP są definiowane przez `System.Net`. 

Application Insights udostępnia trzy dodatkowe typy danych telemetrii niestandardowej:

* [Śledzenie](data-model-trace-telemetry.md) — używane bezpośrednio lub za pośrednictwem adaptera do zaimplementowania rejestrowania diagnostycznego przy użyciu struktury instrumentacji, która jest dla Ciebie znana, na przykład `Log4Net` lub `System.Diagnostics`.
* [Event](data-model-event-telemetry.md) — zwykle służy do przechwytywania interakcji użytkownika z usługą w celu przeanalizowania wzorców użycia.
* [Metryka](data-model-metric-telemetry.md) — służy do raportowania okresowe pomiary skalarne.

Każdy element telemetrii może definiować [informacje kontekstu](data-model-context.md) , takie jak wersja aplikacji lub identyfikator sesji użytkownika. Context to zestaw pól o jednoznacznie określonym typie, który odblokowuje niektóre scenariusze. Gdy wersja aplikacji jest prawidłowo zainicjowana, Application Insights może wykryć nowe wzorce w zachowaniu aplikacji skorelowane z ponownem wdrożeniem. Identyfikator sesji może służyć do obliczania przestoju lub wpływu na problemy użytkowników. Obliczanie odrębnej liczby wartości identyfikatora sesji dla niektórych zależności zakończonych niepowodzeniem, śledzenie błędów lub wyjątek krytyczny zapewnia dobrą wiedzę o wpływie.

Application Insights model telemetrii definiuje sposób [skorelowania](../../azure-monitor/app/correlation.md) telemetrii z operacją, której jest częścią. Na przykład żądanie może wykonać SQL Database wywołań i zarejestrowanych informacji diagnostycznych. Można ustawić kontekst korelacji dla tych elementów telemetrii, które są powiązane z powrotem do telemetrii żądania.

## <a name="schema-improvements"></a>Ulepszenia schematu

Application Insights model danych to prosta i podstawowa, jeszcze wydajna metoda modelowania telemetrii aplikacji. Staramy się utrzymać model prosty i Slim, aby wspierać podstawowe scenariusze i umożliwiać zwiększenie schematu do użycia zaawansowanego.

Aby zgłosić problemy z modelem lub schematem danych oraz sugestie dotyczące korzystania z repozytorium GitHub [ApplicationInsights-Home](https://github.com/Microsoft/ApplicationInsights-Home/issues) .

## <a name="next-steps"></a>Następne kroki

- [Napisz niestandardową telemetrię](../../azure-monitor/app/api-custom-events-metrics.md)
- Dowiedz się [, jak rozciągnąć i filtrować dane telemetryczne](../../azure-monitor/app/api-filtering-sampling.md).
- Użyj [próbkowania](../../azure-monitor/app/sampling.md) , aby zminimalizować ilość danych telemetrycznych opartych na modelu dane.
- Sprawdź [platformy](../../azure-monitor/app/platforms.md) obsługiwane przez Application Insights.
