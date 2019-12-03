---
title: Konfigurowanie aplikacji ASP.NET Core systemu Linux
description: Dowiedz się, jak skonfigurować wstępnie utworzony kontener ASP.NET Core dla aplikacji. W tym artykule przedstawiono najczęstsze zadania konfiguracyjne.
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: d26c490ad37b25785ff1347cccf1e2be21bba277
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670470"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Konfigurowanie aplikacji ASP.NET Core systemu Linux dla Azure App Service

Aplikacje ASP.NET Core muszą być wdrożone jako skompilowane pliki binarne. Narzędzie do publikowania programu Visual Studio kompiluje rozwiązanie, a następnie wdraża skompilowane pliki binarne bezpośrednio, natomiast aparat wdrażania App Service najpierw wdraża repozytorium kodu, a następnie kompiluje pliki binarne.

Ten przewodnik zawiera najważniejsze pojęcia i instrukcje dla ASP.NET Core deweloperów korzystających z wbudowanego kontenera systemu Linux w programie App Service. Jeśli nie korzystasz z Azure App Service, postępuj zgodnie z przewodnikiem [szybki start ASP.NET Core](quickstart-dotnetcore.md) i [ASP.NET Core z samouczkiem SQL Database](tutorial-dotnetcore-sqldb-app.md) .

## <a name="show-net-core-version"></a>Pokaż wersję .NET Core

Aby wyświetlić bieżącą wersję programu .NET Core, uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Aby wyświetlić wszystkie obsługiwane wersje programu .NET Core, uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Ustaw wersję .NET Core

Uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com) , aby ustawić wersję .NET Core na 2,1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>Uzyskiwanie dostępu do zmiennych środowiskowych

W App Service można [ustawić ustawienia aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) poza kodem aplikacji. Następnie można uzyskać do nich dostęp w dowolnej klasie przy użyciu wzorca iniekcji zależności standardowej ASP.NET Core:

```csharp
include Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve App Service app setting
            var myAppSetting = _configuration["MySetting"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

W przypadku skonfigurowania ustawienia aplikacji o tej samej nazwie w App Service i w pliku *appSettings. JSON*na przykład wartość App Service ma pierwszeństwo przed wartością *appSettings. JSON* . Lokalna wartość *appSettings. JSON* umożliwia debugowanie aplikacji lokalnie, ale App Service wartość umożliwia uruchamianie aplikacji w produkcie z ustawieniami produkcyjnymi. Parametry połączenia działają w ten sam sposób. W ten sposób można zachować wpisy tajne aplikacji poza repozytorium kodu i uzyskać dostęp do odpowiednich wartości bez konieczności zmiany kodu.

## <a name="get-detailed-exceptions-page"></a>Strona pobierania szczegółowych wyjątków

Gdy aplikacja ASP.NET generuje wyjątek w debugerze programu Visual Studio, przeglądarka wyświetla szczegółową stronę wyjątku, ale w App Service tej stronie jest zastępowany przez ogólny błąd **protokołu HTTP 500** lub **Wystąpił błąd podczas przetwarzania żądania.** Komunikat. Aby wyświetlić szczegółową stronę wyjątku w App Service, Dodaj ustawienia aplikacji `ASPNETCORE_ENVIRONMENT` do aplikacji, uruchamiając następujące polecenie w <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Wykrywanie sesji protokołu HTTPS

W usłudze App Service [kończenie żądań SSL](https://wikipedia.org/wiki/TLS_termination_proxy) odbywa się w modułach równoważenia obciążenia sieciowego, dzięki czemu wszystkie żądania HTTPS docierają do aplikacji jako niezaszyfrowane żądania HTTP. Jeśli logika aplikacji musi wiedzieć, czy żądania użytkowników są szyfrowane, należy skonfigurować oprogramowanie pośredniczące w programie *Startup.cs*:

- Skonfiguruj oprogramowanie pośredniczące w programie [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) , aby przekazywać `X-Forwarded-For` i `X-Forwarded-Proto` nagłówki w `Startup.ConfigureServices`.
- Dodaj zakresy prywatnych adresów IP do znanych sieci, dzięki czemu oprogramowanie pośredniczące może ufać App Service Module równoważenia obciążenia.
- Wywołaj metodę [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) w `Startup.Configure` przed wywołaniem innych middlewares.

Umieszczenie wszystkich trzech elementów razem, kod wygląda podobnie do poniższego przykładu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

Aby uzyskać więcej informacji, zobacz [konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="deploy-multi-project-solutions"></a>Wdróż rozwiązania obejmujące wiele projektów

Po wdrożeniu repozytorium ASP.NET do aparatu wdrożenia z plikiem *. csproj* w katalogu głównym aparat wdraża projekt. Po wdrożeniu repozytorium ASP.NET z plikiem *. sln* w katalogu głównym Aparat wybiera pierwszą witrynę sieci Web lub projekt aplikacji sieci Web, który znajduje się jako aplikacja App Service. Jest możliwe, że aparat nie wybierze żądanego projektu.

Aby wdrożyć rozwiązanie z obsługą wielu projektów, można określić projekt do użycia w App Service na dwa różne sposoby:

### <a name="using-deployment-file"></a>Przy użyciu pliku. Deployment

Dodaj plik *. Deployment* do katalogu głównego repozytorium i Dodaj następujący kod:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Korzystanie z ustawień aplikacji

W <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>Dodaj ustawienie aplikacji do aplikacji App Service, uruchamiając następujące polecenie interfejsu wiersza polecenia. Zastąp *\<App-name >* , *\<> grupy zasobów*, a *\<Project-Name >* z odpowiednimi wartościami.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otwórz sesję SSH w przeglądarce

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: ASP.NET Core aplikacji z SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [App Service Linux — często zadawane pytania](app-service-linux-faq.md)