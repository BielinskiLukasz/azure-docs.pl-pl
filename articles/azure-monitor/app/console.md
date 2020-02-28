---
title: Application Insights platformy Azure dla aplikacji konsolowych | Microsoft Docs
description: Monitoruj aplikacje sieci Web pod kątem dostępności, wydajności i użycia.
ms.topic: conceptual
ms.date: 12/02/2019
ms.reviewer: lmolkova
ms.openlocfilehash: baaea0f8055eeff0314fcf5fde00729ea8091d12
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77655433"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights aplikacji konsolowych .NET

[Application Insights](../../azure-monitor/app/app-insights-overview.md) umożliwia monitorowanie aplikacji sieci Web pod kątem dostępności, wydajności i użycia.

Potrzebna jest subskrypcja z [Microsoft Azure](https://azure.com). Zaloguj się przy użyciu konto Microsoft, który może być używany w przypadku systemu Windows, usługi Xbox Live lub innych usług firmy Microsoft w chmurze. Twój zespół może mieć subskrypcję organizacyjną na platformie Azure: poproszenie właściciela o dodanie Cię do niego przy użyciu konto Microsoft.

> [!NOTE]
> Istnieje nowy Application Insights SDK o nazwie [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) , który może służyć do włączania Application Insights dla każdej aplikacji konsolowej. Zalecane jest korzystanie z tego pakietu i powiązanych instrukcji w [tym miejscu](../../azure-monitor/app/worker-service.md). Ten pakiet jest przeznaczony dla [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)i dlatego może być używany w środowisku .net Core 2,0 lub nowszym, a .NET Framework 4.7.2 lub wyższy.

## <a name="getting-started"></a>Wprowadzenie

* W witrynie [Azure Portal](https://portal.azure.com)[utwórz zasób usługi Application Insights](../../azure-monitor/app/create-new-resource.md). W obszarze Typ aplikacji wybierz pozycję **Ogólne**.
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

> [!NOTE]
> Dane telemetryczne nie są wysyłane natychmiast. Elementy telemetrii są wsadowe i wysyłane przez zestaw ApplicationInsights SDK. W aplikacjach konsolowych, które opuszczają bezpośrednio po wywołaniu metod `Track()`, dane telemetryczne mogą nie być wysyłane, chyba że `Flush()` i `Sleep` wykonywane przed zakończeniem działania aplikacji, jak pokazano w [pełnym przykładzie](#full-example) w dalszej części tego artykułu.


* Zainstaluj najnowszą wersję pakietu [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) — automatycznie śledzi protokół http, SQL lub inne zewnętrzne wywołania zależności.

Możesz inicjować i konfigurować Application Insights z kodu lub przy użyciu pliku `ApplicationInsights.config`. Upewnij się, że inicjalizacja odbywa się tak wcześnie, jak to możliwe. 

> [!NOTE]
> Instrukcje odwołujące się do **ApplicationInsights. config** mają zastosowanie tylko do aplikacji, które są przeznaczone dla .NET Framework i nie mają zastosowania do aplikacji .NET Core.

### <a name="using-config-file"></a>Korzystanie z pliku konfiguracji

Domyślnie Application Insights SDK szuka pliku `ApplicationInsights.config` w katalogu roboczym podczas tworzenia `TelemetryConfiguration`

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

* Podczas uruchamiania aplikacji Utwórz i skonfiguruj wystąpienie `DependencyTrackingTelemetryModule` — musi być ono pojedyncze i musi być zachowane w okresie istnienia aplikacji.

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

Jeśli konfiguracja została utworzona za pomocą zwykłego konstruktora `TelemetryConfiguration()`, należy również włączyć obsługę korelacji. **Nie jest to konieczne** , jeśli odczytasz konfigurację z pliku, użyto `TelemetryConfiguration.CreateDefault()` lub `TelemetryConfiguration.Active`.

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

            var telemetryClient = new TelemetryClient(configuration);
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
            // please check default settings in https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

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
