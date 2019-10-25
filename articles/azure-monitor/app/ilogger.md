---
title: Eksplorowanie dzienników śledzenia .NET na platformie Azure Application Insights przy użyciu ILogger
description: Przykłady użycia dostawcy usługi Azure Application Insights ILogger z aplikacjami ASP.NET Core i konsolowymi.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cijothomas
ms.author: cithomas
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0e93e2a98d96ca7f436f20e579ab625341024686
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819482"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>Dzienniki usługi ApplicationInsightsLoggerProvider dla programu .NET Core ILogger

ASP.NET Core obsługuje interfejs API rejestrowania, który współpracuje z różnymi rodzajami wbudowanych dostawców rejestrowania i innych firm. Rejestrowanie odbywa się przez wywoływanie **dziennika ()** lub jego wariantu w wystąpieniach *ILogger* . W tym artykule pokazano, jak używać *ApplicationInsightsLoggerProvider* do przechwytywania dzienników ILogger w konsoli programu i aplikacjach ASP.NET Core. W tym artykule opisano również, jak ApplicationInsightsLoggerProvider integruje się z innymi danymi telemetrycznymi Application Insights.
Aby dowiedzieć się więcej, zobacz [Rejestrowanie w ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>ASP.NET Core aplikacji

ApplicationInsightsLoggerProvider jest domyślnie włączona w [Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) Version 2.7.0-beta3 (i nowszych) po włączeniu regularnego monitorowania Application Insights za pomocą jednej z metod standardowych:
- Wywołując metodę rozszerzenia **UseApplicationInsights** w IWebHostBuilder 
- Wywołując metodę rozszerzenia **AddApplicationInsightsTelemetry** w IServiceCollection

Dzienniki ILogger, które są przechwytywane przez ApplicationInsightsLoggerProvider, podlegają tej samej konfiguracji co inne zebrane dane telemetryczne. Mają ten sam zestaw TelemetryInitializers i TelemetryProcessors, używają tego samego TelemetryChannel i są skorelowane i próbkowane w taki sam sposób jak inne dane telemetryczne. Jeśli używasz wersji 2.7.0-beta3 lub nowszej, do przechwytywania dzienników ILogger nie jest wymagana żadna akcja.

Tylko *ostrzeżenia* i więcej dzienników ILogger (ze wszystkich kategorii) są domyślnie wysyłane do Application Insights. Można jednak [zastosować filtry w celu zmodyfikowania tego zachowania](#control-logging-level). Do przechwycenia dzienników ILogger z **program.cs** lub **Startup.cs**jest wymagane wykonanie dodatkowych czynności. (Zobacz [przechwytywanie dzienników ILogger z Startup.cs i program.cs w aplikacjach ASP.NET Core](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Jeśli używasz starszej wersji programu Microsoft. ApplicationInsights. AspNet SDK lub chcesz tylko korzystać z ApplicationInsightsLoggerProvider bez żadnych innych Application Insights monitorowania, wykonaj następującą procedurę:

1. Zainstaluj pakiet NuGet:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Zmodyfikuj **program.cs** , jak pokazano poniżej:

   ```csharp
   using Microsoft.AspNetCore;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Logging;

   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
         WebHost.CreateDefaultBuilder(args)
           .UseStartup<Startup>()
         .ConfigureLogging(
               builder =>
               {
                   // Providing an instrumentation key here is required if you're using
                   // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                   // or if you want to capture logs from early in the application startup
                   // pipeline from Startup.cs or Program.cs itself.
                   builder.AddApplicationInsights("ikey");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

Kod w kroku 2 konfiguruje `ApplicationInsightsLoggerProvider`. Poniższy kod przedstawia przykładową klasę kontrolera, która używa `ILogger` do wysyłania dzienników. Dzienniki są przechwytywane przez Application Insights.

```csharp
public class ValuesController : ControllerBase
{
    private readonly `ILogger` _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Przechwytywanie dzienników ILogger z Startup.cs i Program.cs w aplikacjach ASP.NET Core

> [!NOTE]
> W ASP.NET Core 3,0 i nowszych nie można już wstrzyknąć `ILogger` w Startup.cs i Program.cs. Aby uzyskać więcej informacji, zobacz https://github.com/aspnet/Announcements/issues/353.

Nowy ApplicationInsightsLoggerProvider może przechwytywać dzienniki wczesne w potoku uruchamiania aplikacji. Mimo że usługa ApplicationInsightsLoggerProvider jest automatycznie włączana w Application Insights (począwszy od wersji 2.7.0-beta3), nie ma skonfigurowanego klucza instrumentacji do późniejszego potoku. W związku z tym tylko dzienniki z klas/inne **kontrolera**będą przechwytywane. Aby przechwycić każdy dziennik zaczynający się od **program.cs** i **Startup.cs** , należy jawnie włączyć klucz Instrumentacji dla ApplicationInsightsLoggerProvider. Ponadto *TelemetryConfiguration* nie jest w pełni skonfigurowany podczas rejestrowania się z **program.cs** lub **Startup.cs** . Te dzienniki będą mieć minimalną konfigurację, która używa InMemoryChannel, bez próbkowania ani nie ma standardowych inicjatorów telemetrii ani procesorów.

Poniższe przykłady przedstawiają tę możliwość przy użyciu **program.cs** i **Startup.cs**.

#### <a name="example-programcs"></a>Przykład Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        // This will be picked up by AI
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Przykład Startup.cs

```csharp
public class Startup
{
    private readonly `ILogger` _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migrowanie ze starego ApplicationInsightsLoggerProvider

Wersje zestawu SDK Microsoft. ApplicationInsights. AspNet przed 2.7.0-beta2 obsługują dostawcę rejestrowania, który jest już przestarzały. Ten dostawca został włączony za pomocą metody rozszerzenia **AddApplicationInsights ()** ILoggerFactory. Zalecamy przeprowadzenie migracji do nowego dostawcy, który obejmuje dwa kroki:

1. Usuń wywołanie *ILoggerFactory. AddApplicationInsights ()* z metody **Startup. Configure ()** , aby uniknąć podwójnego rejestrowania.
2. Zastosuj ponownie wszystkie reguły filtrowania w kodzie, ponieważ nie będą one przestrzegane przez nowego dostawcę. Przeciążenia *ILoggerFactory. AddApplicationInsights ()* wymagały minimalnych funkcji LogLevel lub Filter. W przypadku nowego dostawcy filtrowanie jest częścią samej struktury rejestrowania. Nie zostało to zrobione przez dostawcę Application Insights. Należy usunąć wszystkie filtry, które są dostarczane za pośrednictwem przeciążeń *ILoggerFactory. AddApplicationInsights ()* . I reguły filtrowania powinny być podane zgodnie z instrukcjami [kontroli poziomu rejestrowania](#control-logging-level) . Jeśli używasz pliku *appSettings. JSON* do filtrowania rejestrowania, będzie on nadal działał z nowym dostawcą, ponieważ oba używają tego samego aliasu dostawcy, *ApplicationInsights*.

Nadal możesz używać starego dostawcy. (Zostanie on usunięty tylko w wersji głównej, zmiana na 3). *XX*), ale zalecamy przeprowadzenie migracji do nowego dostawcy z następujących powodów:

- Poprzedni dostawca nie obsługuje [zakresów dzienników](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). W nowym dostawcy właściwości z zakresu są automatycznie dodawane jako właściwości niestandardowe do zebranych danych telemetrycznych.
- Dzienniki można teraz przechwycić znacznie wcześniej w potoku uruchamiania aplikacji. Dzienniki z **programu** i klasy **uruchamiania** mogą teraz być przechwytywane.
- W przypadku nowego dostawcy Filtrowanie odbywa się na poziomie platformy. Dzienniki można filtrować do dostawcy Application Insights w taki sam sposób jak w przypadku innych dostawców, w tym w przypadku dostawców wbudowanych, takich jak konsole, debugowanie i tak dalej. Można również zastosować te same filtry do wielu dostawców.
- W ASP.NET Core (2,0 i nowszych) zalecanym sposobem [włączania dostawców rejestrowania](https://github.com/aspnet/Announcements/issues/255) jest użycie metod rozszerzających ILoggingBuilder w **program.cs** .

> [!Note]
> Nowy dostawca jest dostępny dla aplikacji przeznaczonych dla wersji STANDARD 2.0 lub nowszej. Jeśli aplikacja jest przeznaczona dla starszych wersji programu .NET Core, takich jak .NET Core 1,1, lub jeśli jest ona przeznaczona dla .NET Framework, Kontynuuj korzystanie z starego dostawcy.

## <a name="console-application"></a>Aplikacja konsolowa

> [!NOTE]
> Istnieje nowa wersja beta Application Insights SDK o nazwie [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) , która może służyć do włączania Application Insights (ILogger i innych Application Insights telemetrii) dla każdej aplikacji konsolowej. Zalecane jest korzystanie z tego pakietu i powiązanych instrukcji w [tym miejscu](../../azure-monitor/app/worker-service.md).
Poniższy przykład zostanie uznany za przestarzały po wydaniu stabilnej wersji tego nowego pakietu.

Poniższy kod przedstawia przykładową aplikację konsolową skonfigurowaną do wysyłania śladów ILogger do Application Insights.

Zainstalowane pakiety:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create the DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

W tym przykładzie jest wykorzystywany `Microsoft.Extensions.Logging.ApplicationInsights`pakiet autonomiczny. Domyślnie ta konfiguracja używa TelemetryConfiguration "bCzy minimum" do wysyłania danych do Application Insights. Minimum od zera oznacza, że InMemoryChannel jest używanym kanałem. Nie ma próbkowania ani standardowego TelemetryInitializers. Takie zachowanie można zastąpić dla aplikacji konsolowej, jak pokazano w poniższym przykładzie.

Zainstaluj ten dodatkowy pakiet:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

W poniższej sekcji pokazano, jak zastąpić domyślny TelemetryConfiguration za pomocą **usług. Skonfiguruj metodę\<TelemetryConfiguration > ()** . Ten przykład konfiguruje `ServerTelemetryChannel` i próbkowanie. Dodaje niestandardowy ITelemetryInitializer do TelemetryConfiguration.

```csharp
    // Create the DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Kontrola poziomu rejestrowania

ASP.NET Core *ILogger* infrastruktura ma wbudowany mechanizm zastosowania [filtrowania dzienników](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). Pozwala to kontrolować dzienniki wysyłane do każdego zarejestrowanego dostawcy, w tym dostawcę Application Insights. Filtrowanie można wykonać w konfiguracji (zazwyczaj przy użyciu pliku *appSettings. JSON* ) lub w kodzie. Ta funkcja jest dostarczana przez samą platformę. Nie jest on specyficzny dla dostawcy Application Insights.

Poniższe przykłady stosują reguły filtru do ApplicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Utwórz reguły filtru w konfiguracji za pomocą pliku appSettings. JSON

W przypadku ApplicationInsightsLoggerProvider alias dostawcy jest `ApplicationInsights`. Poniższa sekcja pliku *appSettings. JSON* konfiguruje dzienniki pod kątem *ostrzeżenia* i powyżej z wszystkich kategorii i *błędów* i powyżej od kategorii, które zaczynają się od "Microsoft" do wysłania do `ApplicationInsightsLoggerProvider`.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft": "Error"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="create-filter-rules-in-code"></a>Tworzenie reguł filtru w kodzie

Poniższy fragment kodu konfiguruje dzienniki pod kątem *ostrzeżenia* i powyżej ze wszystkich kategorii oraz dla *błędu* i powyżej z kategorii, które zaczynają się od "Microsoft" do wysłania do `ApplicationInsightsLoggerProvider`. Ta konfiguracja jest taka sama jak w poprzedniej sekcji pliku *appSettings. JSON*.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Jakie są stare i nowe wersje programu ApplicationInsightsLoggerProvider?

[Zestaw Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) zawiera wbudowaną ApplicationInsightsLoggerProvider (Microsoft. ApplicationInsights. AspNetCore. Logging. ApplicationInsightsLoggerProvider), która została włączona za pomocą **ILoggerFactory** metody rozszerzenia. Ten dostawca został oznaczony jako przestarzały w wersji 2.7.0-beta2. Zostanie on całkowicie usunięty w następnej zmianie wersji głównej. Sam pakiet [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) . Jest to wymagane do włączenia monitorowania żądań, zależności i tak dalej.

Sugerowana alternatywa to nowy pakiet autonomiczny [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), który zawiera udoskonalony ApplicationInsightsLoggerProvider ( Microsoft. Extensions. Logging. ApplicationInsights. ApplicationInsightsLoggerProvider) i metody rozszerzające na ILoggerBuilder, aby je włączyć.

[Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) wersja 2.7.0-beta3 przyjmuje zależność od nowego pakietu i włącza automatyczne przechwytywanie ILogger.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Dlaczego niektóre dzienniki ILogger są wyświetlane dwukrotnie w Application Insights?

Duplikowanie może wystąpić, jeśli masz starszą (teraz przestarzałą) wersję programu ApplicationInsightsLoggerProvider, wywołując `AddApplicationInsights` w `ILoggerFactory`. Sprawdź, czy metoda **konfiguracji** ma następujące elementy, i usuń ją:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Jeśli podczas debugowania z programu Visual Studio występuje podwójne rejestrowanie, ustaw dla `EnableDebugLogger` *wartość false* w kodzie, który włącza Application Insights w następujący sposób. To duplikowanie i naprawa ma zastosowanie tylko w przypadku debugowania aplikacji.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdkhttpswwwnugetorgpackagesmicrosoftapplicationinsightsaspnetcore-version-270-beta3-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Zaktualizowano do [Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) w wersji 2.7.0-beta3, a dzienniki z ILogger są przechwytywane automatycznie. Jak mogę wyłączyć tę funkcję całkowicie?

Zobacz sekcję [Kontrola poziomu rejestrowania](../../azure-monitor/app/ilogger.md#control-logging-level) , aby dowiedzieć się, jak ogólnie filtrować dzienniki. Aby wyłączyć ApplicationInsightsLoggerProvider, użyj `LogLevel.None`:

**W kodzie:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**W konfiguracji:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Dlaczego niektóre dzienniki ILogger mają takie same właściwości jak inne?

Application Insights przechwytuje i wysyła dzienniki ILogger przy użyciu tego samego TelemetryConfiguration, który jest używany przez każdą inną telemetrię. Ale wystąpił wyjątek. Domyślnie TelemetryConfiguration nie jest w pełni skonfigurowany podczas rejestrowania z **program.cs** lub **Startup.cs**. Dzienniki z tych miejsc nie mają konfiguracji domyślnej, dlatego nie będą działać wszystkie TelemetryInitializers i TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Używam autonomicznego pakietu Microsoft. Extensions. Logging. ApplicationInsights i chcę ręcznie rejestrować kilka dodatkowych danych telemetrycznych. Jak to zrobić?

W przypadku korzystania z pakietu autonomicznego `TelemetryClient` nie jest wprowadzany do kontenera DI, dlatego należy utworzyć nowe wystąpienie `TelemetryClient` i użyć tej samej konfiguracji, która jest używana przez dostawcę rejestratora, jak pokazano w poniższym kodzie. Gwarantuje to, że ta sama konfiguracja jest używana dla wszystkich niestandardowych danych telemetrycznych, a także do telemetrii z ILogger.

```csharp
public class MyController : ApiController
{
   // This telemtryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Jeśli używasz pakietu Microsoft. ApplicationInsights. AspNetCore do włączania Application Insights, zmodyfikuj ten kod, aby uzyskać `TelemetryClient` bezpośrednio w konstruktorze. Na przykład zapoznaj się z tematem [często zadawanych pytań](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Jaki Application Insights typ telemetrii jest produkowany z dzienników ILogger? Lub gdzie mogę zobaczyć ILogger dzienników w Application Insights?

ApplicationInsightsLoggerProvider przechwytuje dzienniki ILogger i tworzy TraceTelemetry z nich. Jeśli obiekt wyjątku jest przesyłany do metody **log ()** na ILogger, *ExceptionTelemetry* jest tworzony zamiast TraceTelemetry. Te elementy telemetrii mogą znajdować się w tych samych miejscach co inne TraceTelemetry lub ExceptionTelemetry dla Application Insights, w tym do portalu, analizy lub lokalnego debugera programu Visual Studio.

Jeśli wolisz zawsze wysyłać TraceTelemetry, użyj tego fragmentu kodu: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Nie mam zainstalowanego zestawu SDK i używam rozszerzenia Web Apps platformy Azure, aby włączyć Application Insights dla aplikacji ASP.NET Core. Jak mogę użyć nowego dostawcy? 

Rozszerzenie Application Insights na platformie Azure Web Apps używa starego dostawcy. Reguły filtrowania można modyfikować w pliku *appSettings. JSON* aplikacji. Aby skorzystać z nowego dostawcy, należy użyć Instrumentacji czasu kompilacji, pobierając zależność NuGet od zestawu SDK. Ten artykuł zostanie zaktualizowany, gdy rozszerzenie przełączy się do korzystania z nowego dostawcy.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Używam samodzielnego pakietu Microsoft. Extensions. Logging. ApplicationInsights i włączania dostawcy Application Insights przez wywoływanie **konstruktora. AddApplicationInsights ("iKey")** . Czy istnieje opcja pobrania klucza Instrumentacji z konfiguracji?


Zmodyfikuj Program.cs i appSettings. JSON w następujący sposób:

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .UseStartup<Startup>()
               .ConfigureLogging((hostingContext, logging) =>
               {
                   // hostingContext.HostingEnvironment can be used to determine environments as well.
                   var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                   logging.AddApplicationInsights(appInsightKey);
               });
   }
   ```

   Właściwa sekcja z `appsettings.json`:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Ten kod jest wymagany tylko w przypadku korzystania z autonomicznego dostawcy rejestrowania. Do regularnego monitorowania Application Insights klucz Instrumentacji jest ładowany automatycznie ze ścieżki konfiguracyjnej *ApplicationInsights: Instrumentationkey*. Plik appSettings. JSON powinien wyglądać następująco:

   ```json
   {
     "ApplicationInsights":
       {
           "Instrumentationkey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usługach:

* [Logowanie ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Dzienniki śledzenia .NET w Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
