---
title: Zapoznaj się z usługą Azure Application Insights | Microsoft Docs
description: Po wprowadzeniem do Application Insights poniżej przedstawiono podsumowanie funkcji, które można eksplorować.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: 3c4a9a736f34d4f7dbfeb004d0837f1f2efa55dc
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666028"
---
# <a name="more-telemetry-from-application-insights"></a>Więcej danych telemetrycznych z Application Insights
Po [dodaniu Application Insights do kodu ASP.NET](../../azure-monitor/app/asp-net.md)istnieje kilka rzeczy, które można zrobić, aby uzyskać jeszcze więcej danych telemetrycznych. 

| Akcja | Efekty|
|---|---|
|(Serwery IIS) [Zainstaluj Monitor stanu](https://go.microsoft.com/fwlink/?LinkId=506648) na każdym komputerze serwera.<br/>(Aplikacje sieci Web platformy Azure) W panelu sterowania platformy Azure dla aplikacji sieci Web otwórz blok Application Insights.| [**Liczniki wydajności**](../../azure-monitor/app/performance-counters.md)<br/>[**Wyjątki**](asp-net-exceptions.md) — szczegółowe dane śledzenia stosu<br/>[**Tamten**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Dodaj fragment kodu JavaScript do stron sieci Web](../../azure-monitor/app/javascript.md)|[Wydajność strony](../../azure-monitor/app/usage-overview.md), wyjątki przeglądarki i wydajność AJAX. Niestandardowa Telemetria po stronie klienta.|
|[Tworzenie testów dostępności sieci Web](../../azure-monitor/app/monitor-web-app-availability.md)|Otrzymywanie alertów, jeśli witryna jest niedostępna|
|[Upewnij się, że plik BuildInfo. config](https://msdn.microsoft.com/library/dn449058.aspx) jest generowany przez program MSBuild|[Tworzenie adnotacji na wykresach metryk](https://docs.microsoft.com/azure/azure-monitor/app/annotations)
|[Zapisz niestandardowe zdarzenia i metryki](../../azure-monitor/app/api-custom-events-metrics.md)|Zliczaj zdarzenia biznesowe i metryki, śledź szczegółowe użycie i nie tylko.|
|[Profilowanie aktywnej witryny](https://aka.ms/AIProfilerPreview)|Szczegółowe terminy funkcji z działającej aplikacji sieci Web|






