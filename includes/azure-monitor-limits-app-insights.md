---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: fb57d8322cfd3f72862dc8edd1d2e231338a66de
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "67305315"
---
Istnieją pewne ograniczenia dotyczące liczby metryk i zdarzeń dla aplikacji, czyli według klucza Instrumentacji. Ograniczenia zależą od wybranego [planu cenowego](https://azure.microsoft.com/pricing/details/application-insights/).

| Resource | Limit domyślny | Uwaga
| --- | --- | --- |
| Łączna ilość danych na dzień | 100 GB | Ilość danych możesz zmniejszyć, ustawiając limit. Jeśli potrzebujesz więcej danych, możesz zwiększyć limit w portalu, do 1 000 GB. W przypadku pojemności większej niż 1 000 GB Wyślij wiadomość e-mail AIDataCap@microsoft.comna adres.
| Ograniczanie przepływności | 32 000 zdarzeń/sekundę | Limit jest mierzony przez minutę.
| Przechowywanie danych | 90 dni | Ten zasób jest przeznaczony na potrzeby [wyszukiwania](../articles/azure-monitor/app/diagnostic-search.md), [analizy](../articles/azure-monitor/app/analytics.md) i [Eksploratora metryk](../articles/azure-monitor/app/metrics-explorer.md).
| Przechowywanie szczegółowych wyników [wieloetapowego testu dostępności](../articles/azure-monitor/app/availability-multistep.md) | 90 dni | Ten zasób zapewnia szczegółowe wyniki każdego kroku.
| Maksymalny rozmiar zdarzenia | 64,000 |
| Długość nazwy właściwości i metryki | 150 | Zobacz [schematy typów](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Długość ciągu wartości właściwości | 8192 | Zobacz [schematy typów](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Długość komunikatu śledzenia i wyjątku | 32 768  | Zobacz [schematy typów](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Liczba [testów dostępności](../articles/azure-monitor/app/monitor-web-app-availability.md) na aplikację | 100 |
| Przechowywanie danych [profilera](../articles/azure-monitor/app/profiler.md) | 5 dni |
| Dane [profilera](../articles/azure-monitor/app/profiler.md) wysłane dziennie | 10 GB |

Aby uzyskać informacje, zobacz [Informacje o zarządzaniu cenami i limitami przydziału dla usługi Application Insights](../articles/azure-monitor/app/pricing.md).