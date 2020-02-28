---
title: Model danych telemetrii wyjątku usługi Azure Application Insights
description: Application Insights model danych dla telemetrii wyjątku
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: fa9bc4efd9549cbcb1d50439989c5dea79d9d60c
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671940"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Dane telemetryczne wyjątku: Application Insights model danych

W [Application Insights](../../azure-monitor/app/app-insights-overview.md)wystąpienie wyjątku reprezentuje obsłużony lub nieobsługiwany wyjątek, który wystąpił podczas wykonywania monitorowanej aplikacji.

## <a name="problem-id"></a>Identyfikator problemu

Identyfikator, pod którym został zgłoszony wyjątek w kodzie. Używany do grupowania wyjątków. Zwykle kombinacja typu wyjątku i funkcji ze stosu wywołań.

Maksymalna długość: 1024 znaków

## <a name="severity-level"></a>Poziom ważności

Poziom ważności śledzenia. Wartość może być `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>Szczegóły wyjątku

(Do rozszerzenia)

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Pomiary niestandardowe

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Następne kroki

- Zobacz [model danych](data-model.md) dla typów Application Insights i modelu danych.
- Dowiedz się, jak [zdiagnozować wyjątki w aplikacjach sieci Web za pomocą Application Insights](../../azure-monitor/app/asp-net-exceptions.md).
- Sprawdź [platformy](../../azure-monitor/app/platforms.md) obsługiwane przez Application Insights.
