---
title: Monitorowanie usługi Azure Functions
description: Dowiedz się, jak używać usługi Azure Application Insights z usługą Azure Functions do monitorowania wykonywania funkcji.
services: functions
author: ggailey777
manager: jeconnoc
keywords: usługa Azure Functions, funkcje, przetwarzanie zdarzeń, elementy webhook, obliczanie dynamiczne, architektura bez serwera
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: glenga
ms.openlocfilehash: 7220c72625697490d9c42dab7e18ca4bf64d3f43
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085625"
---
# <a name="monitor-azure-functions"></a>Monitorowanie usługi Azure Functions

[Azure Functions](functions-overview.md) oferuje wbudowaną integrację z [usługą Azure Application Insights](../azure-monitor/app/app-insights-overview.md) do monitorowania funkcji. W tym artykule opisano sposób konfigurowania Azure Functions do wysyłania generowanych przez system plików dzienników do Application Insights.

Zalecamy używanie Application Insights, ponieważ zbiera dane dotyczące dzienników, wydajności i błędów. Automatycznie wykrywa anomalie wydajności i oferuje zaawansowane narzędzia analityczne ułatwiające diagnozowanie problemów oraz zrozumienie sposobu korzystania z funkcji. Usługa ta pomaga w ciągłym udoskonalaniu wydajności i użyteczności tworzonych rozwiązań. Możesz nawet używać Application Insights podczas tworzenia projektu aplikacji funkcji lokalnych. Aby uzyskać więcej informacji, zobacz [co to jest Application Insights?](../azure-monitor/app/app-insights-overview.md).

Ponieważ wymagana Application Insights Instrumentacja jest wbudowana w Azure Functions, wystarczy, że jest to prawidłowy klucz instrumentacji, aby połączyć aplikację funkcji z Application Insights zasobem.

## <a name="application-insights-pricing-and-limits"></a>Application Insights ceny i limity

Możesz bezpłatnie wypróbować Application Insights integrację z aplikacjami funkcji. Istnieje dzienny limit ilości danych, które można przetworzyć bezpłatnie. Ten limit można napotkać podczas testowania. Platforma Azure udostępnia powiadomienia w portalu i wiadomości e-mail po zbliżaniu się dziennego limitu. Jeśli pominięto te alerty i osiągniesz limit, nowe dzienniki nie będą wyświetlane w Application Insights zapytaniach. Należy pamiętać o limicie, aby uniknąć niepotrzebnego czasu rozwiązywania problemów. Aby uzyskać więcej informacji, zobacz [Zarządzanie cenami i ilością danych w Application Insights](../azure-monitor/app/pricing.md).

## <a name="enable-application-insights-integration"></a>Włącz integrację Application Insights

Aby aplikacja funkcji mogła wysyłać dane do Application Insights, musi znać klucz Instrumentacji zasobu Application Insights. Klucz musi być w ustawieniu aplikacji o nazwie **APPINSIGHTS_INSTRUMENTATIONKEY**.

### <a name="new-function-app-in-the-portal"></a>Nowa aplikacja funkcji w portalu

Podczas [tworzenia aplikacji funkcji w Azure Portal](functions-create-first-azure-function.md)integracja Application Insights jest domyślnie włączona. Zasób Application Insights ma taką samą nazwę jak aplikacja funkcji i jest tworzony w tym samym regionie lub w najbliższym regionie.

Aby przejrzeć tworzony zasób Application Insights, zaznacz go, aby rozwinąć okno **Application Insights** . **Nową nazwę zasobu** można zmienić lub wybrać inną **lokalizację w lokalizacji** geograficznej [platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , w której mają być przechowywane dane.

![Włącz Application Insights podczas tworzenia aplikacji funkcji](media/functions-monitoring/enable-ai-new-function-app.png)

Po wybraniu opcji **Utwórz**zasób Application Insights zostanie utworzony za pomocą aplikacji funkcji, która ma `APPINSIGHTS_INSTRUMENTATIONKEY` ustawioną wartość ustawienia aplikacji. Wszystko jest gotowe do użycia.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Dodawanie do istniejącej aplikacji funkcji 

Podczas tworzenia aplikacji funkcji przy użyciu [interfejsu wiersza polecenia platformy Azure](functions-create-first-azure-function-azure-cli.md), [programu Visual Studio](functions-create-your-first-function-visual-studio.md)lub [Visual Studio Code](functions-create-first-function-vs-code.md)należy utworzyć zasób Application Insights. Następnie można dodać klucz Instrumentacji z tego zasobu jako ustawienia aplikacji w aplikacji funkcji.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Wczesne wersje funkcji używały wbudowanego monitorowania, które nie jest już zalecane. Podczas włączania integracji Application Insights dla takiej aplikacji funkcji należy również [wyłączyć wbudowane funkcje rejestrowania](#disable-built-in-logging).  

## <a name="view-telemetry-in-monitor-tab"></a>Wyświetlanie telemetrii na karcie Monitor

Z [włączoną integracją Application Insights](#enable-application-insights-integration)można wyświetlić dane telemetryczne na karcie **monitorowanie** .

1. Na stronie aplikacja funkcji wybierz funkcję, która została uruchomiona co najmniej raz po skonfigurowaniu Application Insights. Następnie wybierz kartę **monitorowanie** .

   ![Wybierz kartę Monitor](media/functions-monitoring/monitor-tab.png)

1. Wybierz pozycję **Odśwież** okresowo, dopóki nie zostanie wyświetlona lista wywołań funkcji.

   Wyświetlenie listy w czasie, gdy klient telemetrii wysyła dane do serwera, może upłynąć do 5 minut. (Opóźnienie nie ma zastosowania do [Live Metrics Stream](../azure-monitor/app/live-stream.md). Ta usługa nawiązuje połączenie z hostem funkcji podczas ładowania strony, dlatego dzienniki są przesyłane strumieniowo bezpośrednio do strony.

   ![Lista wywołań](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. Aby wyświetlić dzienniki dla określonego wywołania funkcji, wybierz łącze kolumna **daty** dla tego wywołania.

   ![Link szczegółów wywołania](media/functions-monitoring/invocation-details-link-ai.png)

   Dane wyjściowe rejestrowania dla tego wywołania pojawiają się na nowej stronie.

   ![Szczegóły wywołania](media/functions-monitoring/invocation-details-ai.png)

Można zobaczyć, że obie strony mają **uruchomioną Application Insights** link do zapytania Application Insights Analytics pobierającego dane.

![Uruchom w usłudze Application Insights](media/functions-monitoring/run-in-ai.png)

Zostanie wyświetlone następujące zapytanie. Można zobaczyć, że lista wywołań jest ograniczona do 30 ostatnich dni. Lista pokazuje nie więcej niż 20 wierszy (`where timestamp > ago(30d) | take 20`). Lista szczegóły wywołania jest w ciągu ostatnich 30 dni bez limitu.

![Lista wywołań analizy Application Insights](media/functions-monitoring/ai-analytics-invocation-list.png)

Aby uzyskać więcej informacji, zobacz temat [wysyłanie danych](#query-telemetry-data) telemetrycznych w dalszej części tego artykułu.

## <a name="view-telemetry-in-application-insights"></a>Wyświetlanie danych telemetrycznych w Application Insights

Aby otworzyć Application Insights z aplikacji funkcji w Azure Portal, przejdź do strony **Przegląd** aplikacji funkcji. W obszarze **skonfigurowane funkcje**wybierz pozycję **Application Insights**.

![Otwórz Application Insights na stronie przeglądu aplikacji funkcji](media/functions-monitoring/ai-link.png)

Informacje o sposobach używania Application Insights można znaleźć w [dokumentacji Application Insights](https://docs.microsoft.com/azure/application-insights/). W tej sekcji przedstawiono kilka przykładów sposobu wyświetlania danych w Application Insights. Jeśli znasz już Application Insights, możesz przejść bezpośrednio do [sekcji dotyczącej sposobu konfigurowania i dostosowywania danych](#configure-categories-and-log-levels)telemetrycznych.

![Karta przegląd Application Insights](media/functions-monitoring/metrics-explorer.png)

Następujące obszary Application Insights mogą być pomocne podczas oceny zachowania, wydajności i błędów w funkcjach:

| Tab | Opis |
| ---- | ----------- |
| **[Powodzenia](../azure-monitor/app/asp-net-exceptions.md)** |  Twórz wykresy i alerty na podstawie błędów funkcji i wyjątków serwera. **Nazwa operacji** to nazwa funkcji. Błędy w zależnościach nie są wyświetlane, chyba że zaimplementowano niestandardową telemetrię dla zależności. |
| **[Skuteczności](../azure-monitor/app/performance-counters.md)** | Analizuj problemy z wydajnością. |
| **Serwery** | Wyświetlanie wykorzystania zasobów i przepływności na serwer. Te dane mogą być przydatne w scenariuszach debugowania, w których funkcje boggingją bazowe zasoby. Serwery są określane jako **wystąpienia roli w chmurze**. |
| **[Pomiar](../azure-monitor/app/metrics-explorer.md)** | Tworzenie wykresów i alertów opartych na metrykach. Metryki obejmują liczbę wywołań funkcji, czas wykonywania i współczynnik sukcesu. |
| **[Transmisja strumieniowa metryk na żywo](../azure-monitor/app/live-stream.md)** | Wyświetlanie danych metryk w miarę ich tworzenia w czasie rzeczywistym. |

## <a name="query-telemetry-data"></a>Zapytanie danych telemetrycznych

[Application Insights Analytics](../azure-monitor/app/analytics.md) zapewnia dostęp do wszystkich danych telemetrycznych w formie tabel w bazie danych. Analiza zawiera język zapytań służący do wyodrębniania, manipulowania i wizualizacji danych.

![Wybieranie analiz](media/functions-monitoring/select-analytics.png)

![Przykład analizy](media/functions-monitoring/analytics-traces.png)

Oto przykład zapytania, który pokazuje rozkład żądań na proces roboczy w ciągu ostatnich 30 minut.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

Dostępne tabele są wyświetlane na karcie **schemat** po lewej stronie. Dane generowane przez wywołania funkcji można znaleźć w następujących tabelach:

| Tabela | Opis |
| ----- | ----------- |
| **ścieżki** | Dzienniki utworzone przez środowisko uruchomieniowe i kod funkcji. |
| **żądań** | Jedno żądanie wywołania funkcji. |
| **wyłączenia** | Wszystkie wyjątki zgłoszone przez środowisko uruchomieniowe. |
| **customMetrics** | Liczba zakończonych powodzeniem i niepowodzeniem wywołań, współczynnik sukcesu i czas trwania. |
| **customEvents** | Zdarzenia śledzone przez środowisko uruchomieniowe, na przykład: Żądania HTTP wyzwalające funkcję. |
| **Liczniki wydajności** | Informacje o wydajności serwerów, na których działają funkcje. |

Inne tabele są przeznaczone dla testów dostępności, a dane telemetryczne klienta i przeglądarki. Możesz zaimplementować niestandardową telemetrię, aby dodać do nich dane.

W każdej tabeli niektóre dane specyficzne dla funkcji znajdują się w `customDimensions` polu.  Na przykład następujące zapytanie pobiera wszystkie ślady mające poziom `Error`rejestrowania.

```
traces 
| where customDimensions.LogLevel == "Error"
```

Środowisko uruchomieniowe udostępnia `customDimensions.LogLevel` pola `customDimensions.Category` i. W dziennikach można podać dodatkowe pola, które można napisać w kodzie funkcji. Zobacz [Rejestrowanie strukturalne](#structured-logging) w dalszej części tego artykułu.

## <a name="configure-categories-and-log-levels"></a>Konfigurowanie kategorii i poziomów dziennika

Application Insights można używać bez żadnej konfiguracji niestandardowej. Konfiguracja domyślna może powodować duże ilości danych. W przypadku korzystania z subskrypcji programu Visual Studio Azure można osiągnąć limit danych Application Insights. W dalszej części tego artykułu dowiesz się, jak skonfigurować i dostosować dane wysyłane przez funkcje do Application Insights. W przypadku aplikacji funkcji rejestrowanie jest konfigurowane w pliku [host. JSON] .

### <a name="categories"></a>Categories

Rejestrator Azure Functions obejmuje *kategorię* dla każdego dziennika. Kategoria wskazuje, która część kodu środowiska uruchomieniowego lub kod funkcji zapisał dziennik. 

Środowisko uruchomieniowe funkcji tworzy dzienniki z kategorią rozpoczynającą się od "host". W wersji `function started`1. x, `function executed`, i `function completed` dzienniki mają kategorię `Host.Executor`. Począwszy od wersji 2. x, te dzienniki mają kategorię `Function.<YOUR_FUNCTION_NAME>`.

W przypadku pisania dzienników w kodzie funkcji kategoria jest `Function` w wersji 1. x środowiska uruchomieniowego funkcji. W wersji 2. x kategoria to `Function.<YOUR_FUNCTION_NAME>.User`.

### <a name="log-levels"></a>Poziomy dziennika

Rejestrator Azure Functions obejmuje również *poziom dziennika* z każdym dziennikiem. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) jest wyliczeniem, a kod liczby całkowitej wskazuje na ważność względną:

|LogLevel    |Kod|
|------------|---|
|Ślad       | 0 |
|Debugowanie       | 1 |
|Information | 2 |
|Ostrzeżenie     | 3 |
|Błąd       | 4 |
|Krytyczny    | 5 |
|Brak        | 6 |

Poziom `None` dziennika został wyjaśniony w następnej sekcji. 

### <a name="log-configuration-in-hostjson"></a>Konfiguracja dziennika w pliku host. JSON

Plik [host. JSON] określa, ile rejestrowania aplikacja funkcji wysyła do Application Insights. Dla każdej kategorii należy określić minimalny poziom rejestrowania do wysłania. Istnieją dwa przykłady: pierwszy przykład jest przeznaczony dla środowiska [uruchomieniowego w wersji 2. x](functions-versions.md#version-2x) (.NET Core), a drugi przykład dotyczy wersji 1. x.

### <a name="version-2x"></a>Wersja 2. x

Środowisko uruchomieniowe v2. x używa [hierarchii filtrów rejestrowania programu .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

### <a name="version-1x"></a>Wersja 1. x

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

Ten przykład konfiguruje następujące reguły:

* W przypadku dzienników z `Host.Results` kategorią lub `Function`Wyślij `Error` poziom tylko do Application Insights. Dzienniki na `Warning` poziomie i poniżej zostały zignorowane.
* W przypadku dzienników z `Host.Aggregator`kategorią Wyślij wszystkie dzienniki do Application Insights. Poziom dziennika jest taki sam jak w przypadku niektórych rejestratorów `Verbose`, ale należy użyć `Trace` w pliku [host. JSON] `Trace`
* Dla wszystkich innych dzienników wysyłaj tylko `Information` poziom i powyżej do Application Insights.

Wartość kategorii w kontrolce pliku [host. JSON] rejestruje wszystkie kategorie, które zaczynają się od tej samej wartości. `Host`w kontrolce pliku [host. JSON] są `Host.Executor`rejestrowane `Host.Results`dane dla `Host.General`,, i tak dalej.

Jeśli plik [host. JSON] zawiera wiele kategorii, które zaczynają się od tego samego ciągu, są one dopasowywane jako pierwsze. Załóżmy, że chcesz, aby wszystkie elementy uruchomieniowe były wykonywane `Error` z wyjątkiem `Host.Aggregator` rejestrowania na poziomie `Host.Aggregator` , ale `Information` chcesz zalogować się na poziomie:

### <a name="version-2x"></a>Wersja 2. x 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

### <a name="version-1x"></a>Wersja 1. x 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Aby pominąć wszystkie dzienniki dla kategorii, można użyć poziomu `None`dziennika. Żadne dzienniki nie są zapisywane w tej kategorii i nie ma żadnego poziomu dziennika.

W poniższych sekcjach opisano główne kategorie dzienników tworzonych przez środowisko uruchomieniowe. 

### <a name="category-hostresults"></a>Kategoria hosta. wyniki

Te dzienniki są wyświetlane jako "żądania" w Application Insights. Wskazują one powodzenie lub Niepowodzenie funkcji.

![Wykres żądań](media/functions-monitoring/requests-chart.png)

Wszystkie te dzienniki są zapisywane na `Information` poziomie. W przypadku filtrowania na `Warning` poziomie lub wyższym nie będą widoczne żadne z tych danych.

### <a name="category-hostaggregator"></a>Category Host.Aggregator

Te dzienniki zapewniają liczniki i średnie wywołania funkcji w [konfigurowalnym](#configure-the-aggregator) okresie. Domyślny okres to 30 sekund lub 1 000 wyników, zależnie od tego, co nastąpi wcześniej. 

Dzienniki są dostępne w tabeli **customMetrics** w Application Insights. Przykłady to liczba przebiegów, Częstotliwość powodzeń i czas trwania.

![customMetrics zapytanie](media/functions-monitoring/custom-metrics-query.png)

Wszystkie te dzienniki są zapisywane na `Information` poziomie. W przypadku filtrowania na `Warning` poziomie lub wyższym nie będą widoczne żadne z tych danych.

### <a name="other-categories"></a>Inne kategorie

Wszystkie dzienniki dla kategorii innych niż te, które są już wymienione, są dostępne w tabeli TRACES w Application Insights.

![zapytanie śledzenia](media/functions-monitoring/analytics-traces.png)

Wszystkie dzienniki z kategoriami zaczynającymi `Host` się od programu są zapisywane przez środowisko uruchomieniowe funkcji. Dzienniki "funkcja uruchomiona" i "funkcja została ukończona" mają `Host.Executor`kategorię. W przypadku pomyślnych uruchomień `Information` te dzienniki są poziomami. Wyjątki są rejestrowane na `Error` poziomie. Środowisko uruchomieniowe tworzy `Warning` również dzienniki poziomów, na przykład: komunikaty w kolejce wysyłane do kolejki trującej.

Dzienniki zapisane przez kod funkcji mają kategorię `Function` i mogą być dowolnego poziomu dziennika.

## <a name="configure-the-aggregator"></a>Konfigurowanie agregatora

Jak wskazano w poprzedniej sekcji, środowisko uruchomieniowe agreguje dane dotyczące wykonywania funkcji w danym okresie czasu. Domyślny okres to 30 sekund lub 1 000 uruchomienia, w zależności od tego, co nastąpi wcześniej. To ustawienie można skonfigurować w pliku [host. JSON] .  Oto przykład:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Konfiguruj próbkowanie

Application Insights zawiera funkcję [próbkowania](../azure-monitor/app/sampling.md) , która umożliwia ochronę przed generowaniem zbyt dużej ilości danych telemetrycznych w przypadku zakończonych wykonań w czasie szczytowego ładowania. Gdy częstotliwość wykonywania przychodzących przekracza określony próg, Application Insights zaczyna losowo ignorować niektóre wykonania przychodzące. Domyślne ustawienie maksymalnej liczby wykonań na sekundę to 20 (pięć w wersji 1. x). Próbkowanie można skonfigurować w pliku [host. JSON].  Oto przykład:

### <a name="version-2x"></a>Wersja 2. x 

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20
      }
    }
  }
}
```

### <a name="version-1x"></a>Wersja 1. x 

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

> [!NOTE]
> [Próbkowanie](../azure-monitor/app/sampling.md) jest domyślnie włączone. Jeśli wydaje się, że brakuje danych, może być konieczne dostosowanie ustawień próbkowania do określonego scenariusza monitorowania.

## <a name="write-logs-in-c-functions"></a>Zapisywanie dzienników w C# funkcjach

Dzienniki można napisać w kodzie funkcji, który jest wyświetlany jako ślady w Application Insights.

### <a name="ilogger"></a>ILogger

Użyj parametru [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) w funkcjach, `TraceWriter` a nie parametru. Dzienniki utworzone za pomocą `TraceWriter` polecenia przejdź do Application Insights, `ILogger` ale umożliwiają [Rejestrowanie strukturalne](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Przy użyciu `Log<level>` obiektu wywoływanie [metod rozszerzających ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) umożliwia tworzenie dzienników. `ILogger` Poniższy kod zapisuje `Information` dzienniki z kategorią "Function".

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Rejestrowanie strukturalne

Kolejność symboli zastępczych, a nie ich nazw, określa parametry, które są używane w komunikacie dziennika. Załóżmy, że masz następujący kod:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

W przypadku zachowania tego samego ciągu komunikatu i odwrócenia kolejności parametrów, otrzymany tekst komunikatu będzie miał wartości w niewłaściwych miejscach.

Symbole zastępcze są obsługiwane w ten sposób, dzięki czemu można przeprowadzić rejestrowanie strukturalne. Application Insights przechowuje pary nazwa-wartość parametru i ciąg komunikatu. Wynikiem tego jest to, że argumenty komunikatów staną się polami, w których można wykonywać zapytania.

Jeśli wywołanie metody rejestratora wygląda jak w poprzednim przykładzie, można wysłać zapytanie do pola `customDimensions.prop__rowKey`. `prop__` Prefiks jest dodawany, aby upewnić się, że nie ma kolizji między polami, które dodaje i dodaje kod funkcji.

Możesz również wykonać zapytanie dotyczące oryginalnego ciągu wiadomości, odwołując się `customDimensions.prop__{OriginalFormat}`do pola.  

Poniżej przedstawiono przykładową reprezentację `customDimensions` danych w formacie JSON:

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="custom-metrics-logging"></a>Rejestrowanie metryk niestandardowych

W C# funkcjach skryptu można użyć `LogMetric` metody rozszerzenia w `ILogger` programie, aby utworzyć metryki niestandardowe w Application Insights. Oto przykładowe wywołanie metody:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Ten kod jest alternatywą dla wywołania `TrackMetric` przy użyciu interfejsu API Application Insights dla platformy .NET.

## <a name="write-logs-in-javascript-functions"></a>Zapisywanie dzienników w funkcjach JavaScript

W funkcjach środowiska Node. js `context.log` Użyj do pisania dzienników. Rejestrowanie strukturalne nie jest włączone.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Rejestrowanie metryk niestandardowych

Gdy korzystasz z [wersji 1. x](functions-versions.md#creating-1x-apps) środowiska uruchomieniowego funkcji, funkcje języka Node. js mogą używać `context.log.metric` metody do tworzenia niestandardowych metryk w Application Insights. Ta metoda nie jest obecnie obsługiwana w wersji 2. x. Oto przykładowe wywołanie metody:

```javascript
context.log.metric("TestMetric", 1234);
```

Ten kod jest alternatywą dla wywoływania `trackMetric` przy użyciu zestawu SDK środowiska Node. js dla Application Insights.

## <a name="log-custom-telemetry-in-c-functions"></a>Rejestruj niestandardową C# telemetrię w funkcjach

Możesz użyć pakietu NuGet [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) do wysyłania niestandardowych danych telemetrycznych do Application Insights. Poniższy C# przykład używa [niestandardowego interfejsu API](../azure-monitor/app/api-custom-events-metrics.md)telemetrii. Przykład dotyczy biblioteki klas .NET, ale kod Application Insights jest taki sam dla C# skryptu.

### <a name="version-2x"></a>Wersja 2. x

Środowisko uruchomieniowe w wersji 2. x używa nowszych funkcji w Application Insights do automatycznego skorelowania telemetrii z bieżącą operacją. Nie ma potrzeby ręcznego ustawiania operacji `Id`, `ParentId`lub `Name` pól.

```cs
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Track an Event
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
            this.telemetryClient.TrackMetric(metric);

            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

### <a name="version-1x"></a>Wersja 1. x

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

Nie wywołuj `TrackRequest` lub `StartOperation<RequestTelemetry>` ponieważ zobaczysz zduplikowane żądania wywołania funkcji.  Środowisko uruchomieniowe funkcji automatycznie śledzi żądania.

Nie ustawiono `telemetryClient.Context.Operation.Id`. To ustawienie globalne powoduje nieprawidłowe korelację, gdy wiele funkcji jest uruchomionych jednocześnie. Zamiast tego należy utworzyć nowe wystąpienie telemetrii `EventTelemetry`(`DependencyTelemetry`,) i `Context` zmodyfikować jego właściwość. Następnie Przekaż wystąpienie `Track` telemetrii do odpowiedniej metody w `TelemetryClient` (`TrackDependency()`, `TrackEvent()`). Ta metoda zapewnia, że Telemetria ma poprawne szczegóły korelacji dla bieżącego wywołania funkcji.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Rejestruj niestandardową telemetrię w funkcjach JavaScript

Oto przykładowy fragment kodu, który wysyła niestandardową telemetrię przy użyciu [zestawu SDK Application Insights Node. js](https://github.com/microsoft/applicationinsights-node.js):

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    context.done();
};
```

Parametr ustawia wartość `operation_Id` na identyfikator wywołania funkcji. `tagOverrides` To ustawienie pozwala skorelować wszystkie automatycznie generowane i niestandardową telemetrię dla danego wywołania funkcji.

## <a name="dependencies"></a>Zależności

Funkcja v2 automatycznie zbiera zależności dla żądań HTTP, ServiceBus i SQL.

Można napisać niestandardowy kod, aby wyświetlić zależności. Przykłady można znaleźć w sekcji przykład kodu w [ C# niestandardowej](#log-custom-telemetry-in-c-functions)telemetrii. Przykładowy kod powoduje, że *Mapa aplikacji* w Application Insights wygląda jak na poniższej ilustracji:

![Mapa aplikacji](./media/functions-monitoring/app-map.png)

## <a name="report-issues"></a>Zgłoś problemy

Aby zgłosić problem związany z integracją Application Insights w usłudze Functions lub uzyskać sugestię lub żądanie, [Utwórz problem w usłudze GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Dzienniki przesyłania strumieniowego

Podczas tworzenia aplikacji często zachodzi potrzeba zapisywania w dziennikach w czasie niemal rzeczywistym podczas uruchamiania na platformie Azure.

Istnieją dwa sposoby wyświetlania strumienia plików dziennika generowanych przez wykonania funkcji.

* **Wbudowane przesyłanie strumieniowe dzienników**: platforma App Service umożliwia wyświetlenie strumienia plików dziennika aplikacji. Jest to równoznaczne z danymi wyjściowymi wyświetlanymi podczas debugowania funkcji podczas [lokalnego tworzenia](functions-develop-local.md) i korzystania z karty **test** w portalu. Wyświetlane są wszystkie informacje oparte na dzienniku. Aby uzyskać więcej informacji, zobacz [jak przesyłać dzienniki strumieniowo](../app-service/troubleshoot-diagnostic-logs.md#streamlogs). Ta metoda przesyłania strumieniowego obsługuje tylko jedno wystąpienie i nie może być używana z aplikacją działającą w systemie Linux w planie zużycia.

* **Live Metrics Stream**: gdy aplikacja funkcji jest [połączona z Application Insights](#enable-application-insights-integration), można wyświetlać dane dziennika i inne metryki w czasie niemal rzeczywistym w Azure Portal przy użyciu [Live Metrics Stream](../azure-monitor/app/live-stream.md). Użyj tej metody, gdy funkcje monitorowania działają w wielu wystąpieniach lub w systemie Linux w planie zużycia. Ta metoda używa [danych próbkowanych](#configure-sampling).

Strumienie dzienników można wyświetlać zarówno w portalu, jak i w większości lokalnych środowisk programistycznych. 

### <a name="portal"></a>Portal

Oba typy strumieni dzienników można wyświetlić w portalu.

#### <a name="built-in-log-streaming"></a>Wbudowane przesyłanie strumieniowe dzienników

Aby wyświetlić dzienniki przesyłania strumieniowego w portalu, wybierz kartę **funkcje platformy** w aplikacji funkcji. Następnie w obszarze **monitorowanie**wybierz pozycję **Rejestruj strumieniowo**.

![Włączanie dzienników przesyłania strumieniowego w portalu](./media/functions-monitoring/enable-streaming-logs-portal.png)

Spowoduje to połączenie aplikacji z usługą przesyłania strumieniowego dzienników, a Dzienniki aplikacji są wyświetlane w oknie. Można przełączać się między **dziennikami aplikacji** a **dziennikami serwera sieci Web**.  

![Wyświetlanie dzienników przesyłania strumieniowego w portalu](./media/functions-monitoring/streaming-logs-window.png)

#### <a name="live-metrics-stream"></a>Live Metrics Stream

Aby wyświetlić Live Metrics Stream aplikacji, wybierz kartę **Przegląd** aplikacji funkcji. Gdy Application Insights włączasz, zobaczysz link **Application Insights** w obszarze **skonfigurowane funkcje**. Ten link prowadzi do strony Application Insights Twojej aplikacji.

W Application Insights wybierz pozycję **Live Metrics Stream**. [Przykładowe wpisy dziennika](#configure-sampling) są wyświetlane w obszarze **przykładowe**dane telemetryczne.

![Wyświetlanie Live Metrics Stream w portalu](./media/functions-monitoring/live-metrics-stream.png) 

### <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="core-tools"></a>Narzędzia podstawowe

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Dzienniki przesyłania strumieniowego można włączyć za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Użyj następujących poleceń, aby się zalogować, wybierz swoją subskrypcję i pliki dziennika przesyłania strumieniowego:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

Dzienniki przesyłania strumieniowego można włączyć za pomocą [Azure PowerShell](/powershell/azure/overview). W przypadku programu PowerShell Użyj następujących poleceń, aby dodać swoje konto platformy Azure, wybierz subskrypcję i pliki dziennika przesyłania strumieniowego:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>Wyłącz wbudowane rejestrowanie

Po włączeniu Application Insights Wyłącz wbudowane rejestrowanie korzystające z usługi Azure Storage. Wbudowane rejestrowanie jest przydatne do testowania z lekkimi obciążeniami, ale nie jest przeznaczone do użycia w środowisku produkcyjnym. W celu monitorowania produkcji zalecamy Application Insights. Jeśli Wbudowana funkcja rejestrowania jest używana w środowisku produkcyjnym, rekord rejestrowania może być niekompletny z powodu ograniczania w usłudze Azure Storage.

Aby wyłączyć wbudowane rejestrowanie, Usuń `AzureWebJobsDashboard` ustawienie aplikacji. Aby uzyskać informacje dotyczące sposobu usuwania ustawień aplikacji w Azure Portal, zobacz sekcję **Ustawienia aplikacji** w temacie [jak zarządzać aplikacją funkcji](functions-how-to-use-azure-function-app-settings.md#settings). Przed usunięciem ustawienia aplikacji upewnij się, że żadne funkcje w tej samej aplikacji funkcji nie używają ustawienia dla wyzwalaczy lub powiązań usługi Azure Storage.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące zasoby:

* [Application Insights](/azure/application-insights/)
* [Rejestrowanie ASP.NET Core](/aspnet/core/fundamentals/logging/)

[host. JSON]: functions-host-json.md
