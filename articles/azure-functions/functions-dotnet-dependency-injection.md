---
title: Używanie iniekcji zależności w programie .NET Azure Functions
description: Dowiedz się, jak używać iniekcji zależności do rejestrowania i używania usług w usłudze .NET Functions
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: a17ff15e71251e781cd30c33a5616af85e4f4eb9
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76260087"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Używanie iniekcji zależności w programie .NET Azure Functions

Azure Functions obsługuje wzorzec projektowania oprogramowania dla iniekcji zależności (DI), który jest techniką do osiągnięcia [niewersji kontroli (IOC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.

- Iniekcja zależności w Azure Functions jest oparta na funkcjach wstrzykiwania zależności platformy .NET Core. Zaleca się znajomość [iniekcji zależności .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) . Istnieją jednak różnice, w jaki sposób przesłaniasz zależności i jak są odczytywane wartości konfiguracyjne Azure Functions na planie zużycia.

- Obsługa iniekcji zależności rozpoczyna się od Azure Functions 2. x.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było użyć iniekcji zależności, należy zainstalować następujące pakiety NuGet:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Pakiet Microsoft. NET. Sdk. Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) w wersji 1.0.28 lub nowszej

## <a name="register-services"></a>Zarejestruj usługi

Aby zarejestrować usługi, należy utworzyć metodę konfigurowania i dodawania składników do wystąpienia `IFunctionsHostBuilder`.  Azure Functions host tworzy wystąpienie `IFunctionsHostBuilder` i przekazuje je bezpośrednio do metody.

Aby zarejestrować metodę, Dodaj atrybut zestawu `FunctionsStartup`, który określa nazwę typu używaną podczas uruchamiania.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

### <a name="caveats"></a>Zastrzeżenia

Seria kroków rejestracji jest uruchamiana przed i po przetworzeniu klasy startowej przez środowisko uruchomieniowe. W związku z tym należy pamiętać o następujących elementach:

- *Klasa startowa jest przeznaczona tylko do konfiguracji i rejestracji.* Unikaj korzystania z usług zarejestrowanych podczas uruchamiania podczas uruchamiania. Na przykład nie należy próbować rejestrować komunikatu w rejestratorze, który jest rejestrowany podczas uruchamiania. Ten punkt procesu rejestracji jest zbyt wczesny, aby Twoje usługi były dostępne do użycia. Po uruchomieniu metody `Configure` środowisko uruchomieniowe funkcji kontynuuje rejestrowanie dodatkowych zależności, co może wpłynąć na działanie usług.

- *Kontener iniekcji zależności zawiera tylko jawnie zarejestrowane typy*. Jedyne usługi dostępne jako typy z możliwością iniekcji to ustawienia konfiguracji w metodzie `Configure`. W związku z tym typy specyficzne dla funkcji, takie jak `BindingContext` i `ExecutionContext`, nie są dostępne podczas instalacji ani jako typy wstrzykiwane.

## <a name="use-injected-dependencies"></a>Użyj wstrzykiwanych zależności

Iniekcja konstruktora służy do udostępniania zależności w funkcji. Użycie iniekcji konstruktora wymaga, aby nie używać klas statycznych.

Poniższy przykład demonstruje, jak zależności `IMyService` i `HttpClient` są wstrzykiwane do funkcji wyzwalanej przez protokół HTTP. W tym przykładzie zastosowano pakiet [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) wymagany do zarejestrowania `HttpClient` podczas uruchamiania.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

## <a name="service-lifetimes"></a>Okresy istnienia usługi

Aplikacje Azure Functions zapewniają te same okresy istnienia usługi jak [iniekcja zależności ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes). W przypadku aplikacji funkcji różne okresy istnienia usługi działają w następujący sposób:

- **Przejściowe**: usługi przejściowe są tworzone na podstawie każdego żądania usługi.
- W **zakresie**: okres istnienia usługi w zakresie jest zgodny z okresem istnienia funkcji. Usługi w zakresie są tworzone raz na wykonanie. Późniejsze żądania dla tej usługi podczas wykonywania ponownie użyją istniejącego wystąpienia usługi.
- **Pojedyncze**: okres istnienia usługi pojedynczej jest zgodny z okresem istnienia hosta i jest ponownie używany w ramach wykonywania funkcji w tym wystąpieniu. Dla połączeń i klientów zaleca się używanie pojedynczych usług okresu istnienia, na przykład `SqlConnection` lub `HttpClient` wystąpień.

Wyświetl lub Pobierz [przykład różnych okresów istnienia usługi](https://aka.ms/functions/di-sample) w serwisie GitHub.

## <a name="logging-services"></a>Usługi rejestrowania

Jeśli potrzebujesz własnego dostawcy rejestrowania, Zarejestruj niestandardowy typ jako wystąpienie `ILoggerProvider`. Application Insights jest automatycznie dodawane przez Azure Functions.

> [!WARNING]
> - Nie należy dodawać `AddApplicationInsightsTelemetry()` do kolekcji usług, ponieważ rejestruje ona usługi, które powodują konflikt z usługami udostępnianymi przez środowisko.
> - Nie rejestruj własnych `TelemetryConfiguration` ani `TelemetryClient`, jeśli używasz wbudowanej funkcji Application Insights.

## <a name="function-app-provided-services"></a>Usługi funkcji dostarczone przez aplikację

Host funkcji rejestruje wiele usług. Następujące usługi są bezpieczne do podjęcia jako zależność w aplikacji:

|Typ usługi|Okres istnienia|Opis|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Pojedynczego|Konfiguracja środowiska uruchomieniowego|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Pojedynczego|Osoba odpowiedzialna za podanie identyfikatora wystąpienia hosta|

Jeśli istnieją inne usługi, dla których chcesz wziąć zależność, [Utwórz problem i Zaproponuj je w serwisie GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Zastępowanie usług hosta

Zastępowanie usług dostarczonych przez hosta nie jest obecnie obsługiwane.  Jeśli istnieją usługi, które chcesz przesłonić, [Utwórz problem i Zaproponuj je w serwisie GitHub](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Praca z opcjami i ustawieniami

Wartości zdefiniowane w [ustawieniach aplikacji](./functions-how-to-use-azure-function-app-settings.md#settings) są dostępne w wystąpieniu `IConfiguration`, co umożliwia odczytywanie wartości ustawień aplikacji w klasie startowej.

Można wyodrębnić wartości z wystąpienia `IConfiguration` do typu niestandardowego. Kopiowanie wartości ustawień aplikacji do typu niestandardowego ułatwia testowanie usług dzięki wprowadzeniu tych wartości do dodania. Ustawienia odczytywane w wystąpieniu konfiguracji muszą być prostymi parami klucz/wartość.

Rozważmy następującą klasę, która zawiera właściwość o nazwie zgodnej z ustawieniem aplikacji:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

I plik `local.settings.json`, który może być strukturą niestandardowego ustawienia w następujący sposób:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

Wewnątrz metody `Startup.Configure` można wyodrębnić wartości z wystąpienia `IConfiguration` do typu niestandardowego przy użyciu następującego kodu:

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

Wywołanie `Bind` kopiuje wartości, które mają pasujące nazwy właściwości z konfiguracji do wystąpienia niestandardowego. Wystąpienie opcji jest teraz dostępne w kontenerze IoC, aby wstrzyknąć do funkcji.

Obiekt options jest wstrzykiwany do funkcji jako wystąpienie ogólnego interfejsu `IOptions`. Użyj właściwości `Value`, aby uzyskać dostęp do wartości znajdujących się w konfiguracji.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

Zapoznaj się z [wzorcem opcji w ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) , aby uzyskać więcej informacji dotyczących pracy z opcjami.

> [!WARNING]
> Należy unikać próby odczytu wartości z plików, takich jak *Local. Settings. JSON* lub *appSettings. { środowisko}. JSON* w planie zużycia. Wartości odczytane z tych plików związanych z połączeniami wyzwalaczy nie są dostępne, ponieważ aplikacja jest skalowana, ponieważ infrastruktura hostingu nie ma dostępu do informacji o konfiguracji.

## <a name="next-steps"></a>Następne kroki

Więcej informacji zawierają następujące zasoby:

- [Jak monitorować aplikację funkcji](functions-monitoring.md)
- [Najlepsze rozwiązania dotyczące funkcji](functions-best-practices.md)
