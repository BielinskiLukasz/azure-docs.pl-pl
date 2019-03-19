---
title: Włączanie rozszerzenia Snapshot Debugger dla aplikacji .NET usługi Azure Service Fabric, usługa w chmurze i maszyn wirtualnych | Dokumentacja firmy Microsoft
description: Włączanie rozszerzenia Snapshot Debugger dla aplikacji .NET usługi Azure Service Fabric, usługa w chmurze i maszyn wirtualnych
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: brahmnes
ms.openlocfilehash: 4041bee71a41cee06243d53de128bcceecda5618
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/15/2019
ms.locfileid: "58001829"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Włączanie rozszerzenia Snapshot Debugger dla aplikacji .NET usługi Azure Service Fabric, usługa w chmurze i maszyn wirtualnych

Jeśli Twoje ASP.NET lub ASP.NET core uruchamiania aplikacji w usłudze Azure App Service, w poniższych instrukcjach można również. Chyba że aplikacja wymaga dostosowanej konfiguracji rozszerzenia Snapshot Debugger, zdecydowanie zaleca się [włączyć rozszerzenie Snapshot Debugger za pomocą strony portalu usługi Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json). Jeśli aplikacja działa w usłudze Azure Service Fabric, usługa w chmurze, maszyn wirtualnych lub maszyn w środowisku lokalnym, należy użyć poniższych instrukcji. 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Konfigurowanie zbierania migawek dla aplikacji ASP.NET

1. [Włącz usługę Application Insights w aplikacji sieci web](../../azure-monitor/app/asp-net.md), jeśli nie została ona jeszcze zrobione.

2. Obejmują [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pakietu NuGet w swojej aplikacji.

3. W razie potrzeby dostosować dodane do konfiguracji rozszerzenia Snapshot Debugger [plik ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Domyślna konfiguracja rozszerzenia Snapshot Debugger przede wszystkim jest pusta, a wszystkie ustawienia są opcjonalne. Oto przykład przedstawiający konfigurację równoważne w domyślnej konfiguracji:

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>3</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Migawki są zbierane tylko na wyjątki, które są zgłaszane do usługi Application Insights. W niektórych przypadkach (na przykład starsze wersje platformy .NET), konieczne może być [Konfigurowanie zbierania wyjątków](../../azure-monitor/app/asp-net-exceptions.md#exceptions) aby zobaczyć wyjątki, przy użyciu migawek w portalu.


## <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Konfigurowanie zbierania migawek dla aplikacji ASP.NET Core 2.0

1. [Włącz usługę Application Insights w aplikacji sieci web platformy ASP.NET Core](../../azure-monitor/app/asp-net-core.md), jeśli nie została ona jeszcze zrobione.

    > [!NOTE]
    > Należy się upewnić, że aplikacja odwołuje się do wersji 2.1.1 lub nowszego, pakietu Microsoft.ApplicationInsights.AspNetCore.

2. Obejmują [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pakietu NuGet w swojej aplikacji.

3. Modyfikowanie aplikacji `Startup` klasy można dodawać i konfigurować modułu zbierającego migawki danych telemetrycznych procesora.

    Dodaj następujące instrukcje using `Startup.cs`

   ```csharp
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   using Microsoft.ApplicationInsights.AspNetCore;
   using Microsoft.ApplicationInsights.Extensibility;
   ```

   Dodaj następujący kod `SnapshotCollectorTelemetryProcessorFactory` klasy `Startup` klasy.

   ```csharp
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           private readonly IServiceProvider _serviceProvider;

           public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
               _serviceProvider = serviceProvider;

           public ITelemetryProcessor Create(ITelemetryProcessor next)
           {
               var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
               return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
           }
       }
       ...
    ```
    Dodaj `SnapshotCollectorConfiguration` i `SnapshotCollectorTelemetryProcessorFactory` usług do uruchomienia potoku:

    ```csharp
       // This method gets called by the runtime. Use this method to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           // Configure SnapshotCollector from application settings
           services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

           // Add SnapshotCollector telemetry processor.
           services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

           // TODO: Add other services your application needs here.
       }
   }
   ```

4. W razie potrzeby dostosować konfigurację rozszerzenia Snapshot Debugger, dodając sekcji SnapshotCollectorConfiguration do pliku appsettings.json. Wszystkie ustawienia w konfiguracji rozszerzenia Snapshot Debugger są opcjonalne. Oto przykład przedstawiający konfigurację równoważne w domyślnej konfiguracji:

   ```json
   {
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

## <a name="configure-snapshot-collection-for-other-net-applications"></a>Konfigurowanie zbierania migawek dla innych aplikacji .NET

1. Jeśli aplikacja nie jest już za pomocą usługi Application Insights, zacznij od [włączenie usługi Application Insights i ustawić dla klucza Instrumentacji](../../azure-monitor/app/windows-desktop.md).

2. Dodaj [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pakietu NuGet w swojej aplikacji.

3. Migawki są zbierane tylko na wyjątki, które są zgłaszane do usługi Application Insights. Może być konieczne zmodyfikowanie kodu na zgłoszenie ich. Kodu obsługi wyjątków zależy od struktury aplikacji, ale przykład znajduje się poniżej:
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```

## <a name="next-steps"></a>Kolejne kroki

- Generowanie ruchu do aplikacji, które mogą wyzwalać wyjątek. Poczekaj 10 do 15 minut dla migawek do wysłania do wystąpienia usługi Application Insights.
- Zobacz [migawek](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json) w witrynie Azure portal.
- Aby uzyskać pomoc dotyczącą rozwiązywania problemów Profiler, zobacz [Rozwiązywanie problemów z rozszerzenia Snapshot Debugger](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).
