---
title: Tworzenie i uruchamianie niestandardowych testów dostępności przy użyciu Azure Functions
description: Ten dokument zawiera informacje dotyczące sposobu tworzenia funkcji platformy Azure z TrackAvailability (), która będzie uruchamiana okresowo zgodnie z konfiguracją podaną w funkcji TimerTrigger. Wyniki tego testu zostaną wysłane do zasobu Application Insights, w którym będzie można wykonywać zapytania o dane dotyczące wyników dostępności i je otrzymywać. Dostosowane testy umożliwiają pisanie bardziej złożonych testów dostępności niż jest to możliwe za pomocą interfejsu użytkownika portalu, monitorowania aplikacji wewnątrz sieci wirtualnej platformy Azure, zmiany adresu punktu końcowego lub tworzenia testu dostępności, jeśli nie jest on dostępny w Twoim regionie.
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 11/22/2019
ms.openlocfilehash: 476d66c51c10a5fcfb3cb0319c47b3338d28812c
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665803"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Tworzenie i uruchamianie niestandardowych testów dostępności przy użyciu Azure Functions

W tym artykule opisano sposób tworzenia funkcji platformy Azure z TrackAvailability (), która będzie uruchamiana okresowo zgodnie z konfiguracją podaną w funkcji TimerTrigger z własną logiką biznesową. Wyniki tego testu zostaną wysłane do zasobu Application Insights, w którym będzie można wykonywać zapytania o dane dotyczące wyników dostępności i je otrzymywać. Dzięki temu można tworzyć niestandardowe testy podobne do tego, co można zrobić za pośrednictwem [monitorowania dostępności](../../azure-monitor/app/monitor-web-app-availability.md) w portalu. Dostosowane testy umożliwiają pisanie bardziej złożonych testów dostępności niż jest to możliwe za pomocą interfejsu użytkownika portalu, monitorowania aplikacji wewnątrz sieci wirtualnej platformy Azure, zmiany adresu punktu końcowego lub tworzenia testu dostępności, nawet jeśli ta funkcja nie jest dostępna w Twoim regionie.

> [!NOTE]
> Ten przykład został zaprojektowany wyłącznie w celu pokazywania Mechanics sposobu, w jaki wywołanie interfejsu API TrackAvailability () działa w ramach funkcji platformy Azure. Nie sposób pisania bazowego kodu testu HTTP/logiki biznesowej, które byłyby wymagane do przeprowadzenia tego w pełni funkcjonalnego testu dostępności. Domyślnie jeśli przejdziesz przez ten przykład, utworzysz Test dostępności, który będzie zawsze generował błąd.

## <a name="create-timer-triggered-function"></a>Utwórz funkcję wyzwalaną przez czasomierz

- Jeśli masz zasób Application Insights:
    - Domyślnie Azure Functions tworzy zasób Application Insights, ale jeśli chcesz użyć jednego z już utworzonych zasobów, musisz określić, że podczas tworzenia.
    - Postępuj zgodnie z instrukcjami dotyczącymi sposobu [tworzenia zainicjowanych zasobów Azure Functions i czasomierza](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (Zatrzymaj przed oczyszczeniem) przy użyciu następujących opcji.
        -  Wybierz kartę **monitorowanie** w górnej części strony.

            ![ Tworzenie aplikacji Azure Functions przy użyciu własnego zasobu usługi App Insights](media/availability-azure-functions/create-function-app.png)

        - Zaznacz pole listy rozwijanej Application Insights i wpisz lub wybierz nazwę zasobu.

            ![Wybieranie istniejącego zasobu Application Insights](media/availability-azure-functions/app-insights-resource.png)

        - Wybieranie opcji **Recenzja + tworzenie**
- Jeśli nie masz jeszcze utworzonego zasobu Application Insights dla funkcji wyzwalanej przez czasomierz:
    - Domyślnie podczas tworzenia aplikacji Azure Functions zostanie utworzony zasób Application Insights.
    - Postępuj zgodnie z instrukcjami dotyczącymi sposobu [tworzenia zasobu Azure Functions i wyzwalanej funkcji timer](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (Zatrzymaj przed oczyszczeniem).

## <a name="sample-code"></a>Przykładowy kod

Skopiuj poniższy kod do pliku Run. CSX (spowoduje to zastąpienie istniejącego kodu). W tym celu przejdź do aplikacji Azure Functions i wybierz funkcję wyzwalacza czasomierz po lewej stronie.

>[!div class="mx-imgBorder"]
>![CSX funkcji platformy Azure w Azure Portal](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> Adres punktu końcowego, który ma być używany: `EndpointAddress= https://dc.services.visualstudio.com/v2/track`. Jeśli zasób nie znajduje się w regionie, takim jak Azure Government lub Chiny platformy Azure, w tym przypadku zapoznaj się z tym artykułem, aby zastąpić [domyślne punkty końcowe](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) i wybrać odpowiedni punkt końcowy kanału telemetrii dla danego regionu.

```C#
#load "runAvailabilityTest.csx"
 
using System;
using System.Diagnostics;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
 
// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
// DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string instrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");
 
//[CONFIGURATION_REQUIRED]
// If your resource is in a region like Azure Government or Azure China, change the endpoint address accordingly.
// Visit https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification for more details.
private const string EndpointAddress = "https://dc.services.visualstudio.com/v2/track";
 
private static readonly TelemetryConfiguration telemetryConfiguration = new TelemetryConfiguration(instrumentationKey, new InMemoryChannel { EndpointAddress = EndpointAddress });
private static readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
 
public async static Task Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Entering Run at: {DateTime.Now}");
 
    if (myTimer.IsPastDue)
    {
        log.LogWarning($"[Warning]: Timer is running late! Last ran at: {myTimer.ScheduleStatus.Last}");
    }
 
    // [CONFIGURATION_REQUIRED] provide {testName} accordingly for your test function
    string testName = "AvailabilityTestFunction";
 
    // REGION_NAME is a default environment variable that comes with App Service
    string location = Environment.GetEnvironmentVariable("REGION_NAME");
 
    log.LogInformation($"Executing availability test run for {testName} at: {DateTime.Now}");
    string operationId = Guid.NewGuid().ToString("N");
 
    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = testName,
        RunLocation = location,
        Success = false
    };
 
    var stopwatch = new Stopwatch();
    stopwatch.Start();
 
    try
    {
        await RunAvailbiltyTestAsync(log);
        availability.Success = true;
    }
    catch (Exception ex)
    {
        availability.Message = ex.Message;
 
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", testName);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        telemetryClient.TrackException(exceptionTelemetry);
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;
 
        telemetryClient.TrackAvailability(availability);
        // call flush to ensure telemetry is sent
        telemetryClient.Flush();
    }
}

```

Po prawej stronie w obszarze Wyświetl pliki wybierz pozycję **Dodaj**. Wywołaj nowy plik **Funkcja. proj** z następującą konfiguracją.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.8.2" /> <!-- Ensure you’re using the latest version -->
    </ItemGroup>
</Project>

```

>[!div class="mx-imgBorder"]
>![po prawej stronie wybierz pozycję Dodaj. Nazwij plik Function. proj](media/availability-azure-functions/addfile.png)

Po prawej stronie w obszarze Wyświetl pliki wybierz pozycję **Dodaj**. Wywołaj nowy plik **runAvailabilityTest. CSX** z następującą konfiguracją.

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>Sprawdź dostępność

Aby upewnić się, że wszystko działa, możesz przyjrzeć się grafowi na karcie dostępność zasobu Application Insights.

> [!NOTE]
> Jeśli zaimplementowano własną logikę biznesową w programie runAvailabilityTest. CSX, zobaczysz pomyślne wyniki podobne do poniższych zrzutów ekranu, jeśli nie zobaczysz, że wyniki nie zostaną wyświetlone.

>[!div class="mx-imgBorder"]
>Karta dostępność ![z pomyślnymi wynikami](media/availability-azure-functions/availtab.png)

Po skonfigurowaniu testu przy użyciu Azure Functions można zauważyć, że w przeciwieństwie do użycia **Dodaj test** na karcie dostępność nazwa testu nie zostanie wyświetlona i nie będzie można z nim korzystać. Wyniki są wizualizowane, ale można uzyskać widok podsumowania, a nie ten sam widok szczegółowy, który uzyskuje się podczas tworzenia testu dostępności za pośrednictwem portalu.

Aby wyświetlić szczegółowe informacje o transakcjach, wybierz pozycję **powodzenie** lub **Niepowodzenie** w obszarze drążenie do, a następnie wybierz przykład. Możesz również uzyskać szczegółowe informacje o transakcjach, wybierając punkt danych na wykresie.

>[!div class="mx-imgBorder"]
>![wybrać](media/availability-azure-functions/sample.png) testu dostępności

>[!div class="mx-imgBorder"]
>![szczegółowe informacje o transakcjach](media/availability-azure-functions/end-to-end.png)

Jeśli uruchomiono wszystko (bez dodawania logiki biznesowej), zobaczysz, że test zakończył się niepowodzeniem.

## <a name="query-in-logs-analytics"></a>Zapytanie w dziennikach (analiza)

Dzienników (analiz) można używać do wyświetlania wyników dostępności, zależności i innych. Aby dowiedzieć się więcej o dziennikach, odwiedź stronę [Omówienie zapytania dziennika](../../azure-monitor/log-query/log-query-overview.md).

>[!div class="mx-imgBorder"]
>wyniki dostępności ![](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![Zależności](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Następne kroki

- [Mapa aplikacji](../../azure-monitor/app/app-map.md)
- [Diagnostyka transakcji](../../azure-monitor/app/transaction-diagnostics.md)
