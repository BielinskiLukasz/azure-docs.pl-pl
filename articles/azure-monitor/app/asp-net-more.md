---
title: Lepiej wykorzystać możliwości usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Wprowadzenie do usługi Application Insights, w tym miejscu po Podsumowanie funkcji, które można eksplorować.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 7ec10a2d-c669-448d-8d45-b486ee32c8db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/03/2017
ms.author: mbullwin
ms.openlocfilehash: b81a555111ff49fcf2e14a75afdce81835d151bb
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54038553"
---
# <a name="more-telemetry-from-application-insights"></a>Więcej funkcji telemetrii z usługi Application Insights
Po utworzeniu [dodawane usługi Application Insights do kodu ASP.NET](../../azure-monitor/app/asp-net.md), istnieje kilka kwestii, które można zrobić, aby uzyskać jeszcze więcej funkcji telemetrii. 

| Akcja | Efekty|
|---|---|
|(Na serwerach usług IIS) [Instalowanie Monitora stanu](https://go.microsoft.com/fwlink/?LinkId=506648) na każdym komputerze serwera.<br/>(Aplikacje sieci web platformy azure) W Panelu sterowania platformy Azure dla aplikacji sieci web Otwórz blok usługi Application Insights.| [**Liczniki wydajności**](../../azure-monitor/app/performance-counters.md)<br/>[**Wyjątki** ](asp-net-exceptions.md) — szczegółowe ślady stosu<br/>[**Zależności**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Dodaj fragment kodu JavaScript do stron sieci web](../../azure-monitor/app/javascript.md)|[Wydajność strony](../../azure-monitor/app/usage-overview.md), wyjątków przeglądarki, wydajności AJAX. Niestandardowej telemetrii po stronie klienta.|
|[Tworzenie testów sieci web dostępności](../../azure-monitor/app/monitor-web-app-availability.md)|Otrzymuj alerty, jeśli witryna staje się niedostępny|
|[Upewnij się, buildinfo.config](https://msdn.microsoft.com/library/dn449058.aspx) jest generowany przez program MSBuild|[Twórz adnotacje w wykresy metryk](https://blogs.msdn.microsoft.com/visualstudioalm/2013/11/14/implementing-deployment-markers-in-application-insights/)
|[Pisanie niestandardowych zdarzeń i metryk](../../azure-monitor/app/api-custom-events-metrics.md)|Liczba zdarzeń biznesowych i metryki, śledzić szczegółowym zestawieniem użycia i nie tylko.|
|[Profil witryny na żywo](https://aka.ms/AIProfilerPreview)|Funkcja szczegółowe chronometrażu z aplikacji sieci web na żywo|






