---
title: Application Insights platformy Azure dla aplikacji konsolowych | Microsoft Docs
description: Monitoruj aplikacje sieci Web pod kątem dostępności, wydajności i użycia.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/30/2019
ms.reviewer: lmolkova
ms.author: mbullwin
ms.openlocfilehash: b6ecf1e9cece51635afc0bf0f8025b6e117438ee
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169453"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights aplikacji konsolowych .NET

[Application Insights](../../azure-monitor/app/app-insights-overview.md) umożliwia monitorowanie aplikacji sieci Web pod kątem dostępności, wydajności i użycia.

Potrzebna jest subskrypcja z [Microsoft Azure](https://azure.com). Zaloguj się przy użyciu konto Microsoft, który może być używany w przypadku systemu Windows, usługi Xbox Live lub innych usług firmy Microsoft w chmurze. Twój zespół może mieć subskrypcję organizacyjną na platformie Azure: poproszenie właściciela o dodanie Cię do niego przy użyciu konto Microsoft.

> [!NOTE]
> Istnieje nowa wersja beta Application Insights SDK o nazwie [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) , która może służyć do włączania Application Insights dla każdej aplikacji konsolowej. Zalecane jest korzystanie z tego pakietu i powiązanych instrukcji w [tym miejscu](../../azure-monitor/app/worker-service.md). Ten pakiet jest [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)przeznaczony dla tego pakietu i dlatego może być używany w środowisku .NET Core 2,0 lub nowszym oraz .NET Framework 4.7.2 lub wyższy.
Ten dokument zostanie uznany za przestarzały po wydaniu stabilnej wersji tego nowego pakietu.

## <a name="getting-started"></a>Wprowadzenie

* W witrynie [Azure Portal](https://portal.azure.com) [utwórz zasób usługi Application Insights](../../azure-monitor/app/create-new-resource.md). W obszarze Typ aplikacji wybierz pozycję **Ogólne**.
* Wykonaj kopię klucza instrumentacji. Znajdź klucz na liście rozwijanej **podstawowe** informacje o utworzonym nowym zasobie. 
* Zainstaluj najnowszy pakiet [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) .
* Ustaw klucz Instrumentacji w kodzie przed śledzeniem danych telemetrycznych (lub Ustaw zmienną środowiskową APPINSIGHTS_INSTRUMENTATIONKEY). Następnie powinno być możliwe ręczne śledzenie danych telemetrycznych i wyświetlanie ich w Azure Portal

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

* Zainstaluj najnowszą wersję pakietu [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) — automatycznie śledzi protokół http, SQL lub inne zewnętrzne wywołania zależności.

Możesz inicjować i konfigurować Application Insights z kodu lub przy użyciu `ApplicationInsights.config` pliku. Upewnij się, że inicjalizacja odbywa się tak wcześnie, jak to możliwe. 

> [!NOTE]
> Instrukcje odwołujące się do **ApplicationInsights. config** mają zastosowanie tylko do aplikacji, które są przeznaczone dla .NET Framework i nie mają zastosowania do aplikacji .NET Core.

### <a name="using-config-file"></a>Korzystanie z pliku konfiguracji
Domyślnie zestaw Application Insights SDK szuka `ApplicationInsights.config` pliku w katalogu roboczym, gdy `TelemetryConfiguration` jest tworzony

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

Możesz również określić ścieżkę do pliku konfiguracji.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

Aby uzyskać więcej informacji, zobacz [Dokumentacja pliku konfiguracji](configuration-with-applicationinsights-config.md).

Pełny przykład pliku konfiguracji można uzyskać, instalując najnowszą wersję pakietu [Microsoft. ApplicationInsights. WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) . Poniżej znajduje się **minimalna** konfiguracja kolekcji zależności, która jest równoważna z przykładem kodu.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>Konfigurowanie zbierania danych telemetrycznych z kodu
> [!NOTE]
> Odczytywanie pliku konfiguracji nie jest obsługiwane w programie .NET Core. Możesz rozważyć użycie [zestawu SDK Application Insights dla ASP.NET Core](../../azure-monitor/app/asp-net-core.md)

* Podczas uruchamiania aplikacji Utwórz i skonfiguruj `DependencyTrackingTelemetryModule` wystąpienie — muszą one być singleton i muszą zostać zachowane w okresie istnienia aplikacji.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Dodaj typowe inicjatory telemetrii

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Jeśli utworzono konfigurację z zwykłym `TelemetryConfiguration()` konstruktorem, należy również włączyć obsługę korelacji. **Nie jest to konieczne** , jeśli odczytasz konfigurację z pliku `TelemetryConfiguration.CreateDefault()` , `TelemetryConfiguration.Active`użyto lub.

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* Możesz również zainstalować i zainicjować moduł zbierający licznik wydajności, jak opisano [tutaj](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)


#### <a name="full-example"></a>Pełny przykład

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient();
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking so wait a bit
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

            // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

            // enable known dependency tracking, note that in future versions, we will extend this list. 
            // please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>Następne kroki
* [Monitoruj zależności](../../azure-monitor/app/asp-net-dependencies.md) , aby zobaczyć, czy REST, SQL lub inne zasoby zewnętrzne spowalniają pracę.
* [Użyj interfejsu API,](../../azure-monitor/app/api-custom-events-metrics.md) aby wysyłać własne zdarzenia i metryki w celu uzyskania bardziej szczegółowego widoku wydajności i użycia aplikacji.
