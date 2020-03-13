---
title: Śledzenie rozproszone na platformie Azure Application Insights | Microsoft Docs
description: Zawiera informacje o pomocy technicznej firmy Microsoft w zakresie śledzenia rozproszonego za pomocą usługi przesyłania dalej i partnerstwa w projekcie OpenCensus
ms.topic: conceptual
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: d9f67250a232d4666e4655f83e9bdc7622754331
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136744"
---
# <a name="what-is-distributed-tracing"></a>Co to jest śledzenie rozproszone?

Pojawieniu nowoczesnej architektury chmury i [mikrousług](https://azure.com/microservices) ma podstawę prostych, niezależnie wdrażanych usług, które mogą pomóc w obniżeniu kosztów przy jednoczesnym zwiększeniu dostępności i przepływności. Jednak chociaż te przesunięcia zostały łatwiejsze do zrozumienia w całości, są one bardziej trudne do odłożenia i debugowania.

W przypadku architektur monolitycznych wykorzystamy do debugowania z stosów wywołań. Stosy wywołań to doskonały narzędzia do wyświetlania przepływu wykonania (Metoda wywołana metoda B, która nazywana metodą C), wraz ze szczegółami i parametrami dotyczącymi każdego z tych wywołań. Jest to doskonałe rozwiązanie dla monolitów lub usług działających w ramach jednego procesu, ale jak debugować, gdy wywołanie odbywa się na granicy procesu, a nie po prostu odwołaniem do lokalnego stosu? 

Jest to miejsce, w którym nastąpi śledzenie rozproszone.  

Śledzenie rozproszone to odpowiednik stosów wywołań dla nowoczesnych architektur chmury i mikrousług, z dodaniem profilera wydajności uproszczony w systemie. W Azure Monitor udostępniamy dwa środowiska do konsumowania dystrybuowanych danych śledzenia. Pierwszy to nasz widok [diagnostyki transakcji](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) , który przypomina stos wywołań z wymiarem czasu dodanym w. Widok Diagnostyka transakcji umożliwia wgląd w jedną transakcję/żądanie i ułatwia znalezienie głównej przyczyny problemów z niezawodnością oraz wąskich gardeł wydajności na podstawie żądania.

Azure Monitor oferuje również widok [mapy aplikacji](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) , który agreguje wiele transakcji, aby pokazać widok topologiczny, w jaki sposób działają systemy, oraz obliczyć średnią wydajność i częstotliwość błędów. 

## <a name="how-to-enable-distributed-tracing"></a>Jak włączyć śledzenie rozproszone

Włączenie śledzenia rozproszonego w ramach usług w aplikacji jest tak proste jak dodanie odpowiedniego zestawu SDK lub biblioteki do każdej usługi na podstawie języka, w którym usługa została zaimplementowana.

## <a name="enabling-via-application-insights-sdks"></a>Włączanie za pośrednictwem zestawów SDK Application Insights

Zestawy SDK Application Insights dla platform .NET, .NET Core, Java, Node. js i JavaScript obsługują śledzenie rozproszone natywnie. Instrukcje dotyczące instalowania i konfigurowania każdego zestawu Application Insights SDK są dostępne poniżej:

* [.NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* [Node.js](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)
* [Python](opencensus-python.md)

Po zainstalowaniu i skonfigurowaniu odpowiedniego zestawu Application Insights SDK informacje o śledzeniu są automatycznie zbierane dla popularnych struktur, bibliotek i technologii przez automatyczne zbieranie zależności zestawu SDK. Pełną listę obsługiwanych technologii można znaleźć w dokumentacji dotyczącej [autozbierania zależności](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies).

 Ponadto jakakolwiek technologia może być śledzona ręcznie z wywołaniem [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) na [TelemetryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics).

## <a name="enable-via-opencensus"></a>Włącz za pośrednictwem OpenCensus

Oprócz zestawów SDK Application Insights, Application Insights obsługuje również śledzenie rozproszone za pomocą [OpenCensus](https://opencensus.io/). OpenCensus to model typu open source, Vendor-niezależny od, pojedynczej dystrybucji bibliotek, aby zapewnić zbieranie metryk i rozproszone śledzenie dla usług. Umożliwia również społeczności typu "open source" Włączenie śledzenia rozproszonego przy użyciu popularnych technologii, takich jak Redis, Memcached lub MongoDB. [Firma Microsoft współpracuje z usługą OpenCensus przy użyciu kilku innych partnerów monitorujących i w chmurze](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

[Python](opencensus-python.md) 

Witryna sieci Web OpenCensus przechowuje dokumentację referencyjną interfejsu API dla języka [Python](https://opencensus.io/api/python/trace/usage.html) i języka [go](https://godoc.org/go.opencensus.io), a także różne różne przewodniki dotyczące korzystania z OpenCensus. 

## <a name="next-steps"></a>Następne kroki

* [Przewodnik po użyciu języka Python OpenCensus](https://opencensus.io/api/python/trace/usage.html)
* [Mapa aplikacji](./../../azure-monitor/app/app-map.md)
* [Kompleksowe monitorowanie wydajności](./../../azure-monitor/learn/tutorial-performance.md)
