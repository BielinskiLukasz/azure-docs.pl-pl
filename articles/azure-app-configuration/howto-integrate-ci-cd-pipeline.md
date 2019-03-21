---
title: Integracja z potoku ciągłej integracji i dostarczania za pomocą konfiguracji aplikacji platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można wygenerować pliku konfiguracji przy użyciu danych w konfiguracji aplikacji platformy Azure podczas ciągłej integracji i ciągłego dostarczania
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: cb9fe6dc234c317daa5eabec01812324e7c81663
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224332"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integracja z potokiem ciągłej integracji/ciągłego wdrażania

Możesz zwiększyć odporność aplikacji możliwość zdalnego nie będą mogli nawiązać połączenie konfiguracji aplikacji platformy Azure. Aby to zrobić, Utwórz pakiet bieżące dane konfiguracji do pliku, który jest wdrożony z aplikacją i ładowanie lokalnie podczas jego uruchamiania. Ta metoda gwarantuje, że Twoja aplikacja ma domyślne ustawienie wartości co najmniej. Te wartości są zastępowane przez wszystkie nowsze zmiany wprowadzone w konfiguracji sklepu z aplikacjami, gdy będzie ona dostępna.

Za pomocą [wyeksportować](./howto-import-export-data.md#export-data) funkcję konfiguracji aplikacji platformy Azure, możesz zautomatyzować proces pobierania bieżące dane konfiguracji jako pojedynczy plik. Następnie osadź ten plik w kroku kompilacji lub wdrożenia ciągłej integracji i ciągłego wdrażania (CI/CD) potoku.

Poniższy przykład pokazuje, jak konfiguracja aplikacji obejmują dane jako kompilację krok dla aplikacji sieci web, wprowadzona w Szybki Start. Przed kontynuowaniem należy zakończyć [tworzenie aplikacji platformy ASP.NET Core z konfiguracji aplikacji](./quickstart-aspnet-core-app.md) pierwszy.

Wykonaj kroki w tym przewodniku Szybki Start, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) jest doskonałą opcją dostępne w Windows, macOS i platformy Linux.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli tworzysz lokalnie, Pobierz i zainstaluj [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Jeśli jeszcze go.

Aby wykonać kompilację w chmurze, za pomocą usługi Azure DevOps na przykład, upewnij się, [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) jest zainstalowany w systemie kompilacji.

## <a name="export-an-app-configuration-store"></a>Eksportowanie konfiguracji sklepu z aplikacjami

1. Otwórz swoje *.csproj* pliku i Dodaj następujący skrypt:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    Dodaj *ConnectionString* skojarzonych z Twoim sklepie z aplikacjami konfiguracji jako zmienną środowiskową.

2. Otwórz plik Program.cs i zaktualizuj `CreateWebHostBuilder` metodę wyeksportowany plik JSON, wywołując `config.AddJsonFile()` metody.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var directory = System.IO.Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location);
                var settings = config.Build();

                config.AddJsonFile(Path.Combine(directory, "azureappconfig.json"));
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString**i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. Korzystając z wiersza polecenia Windows, uruchom następujące polecenie i ponownie uruchom wiersz polecenia, aby umożliwić zmiana zaczęła obowiązywać:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Jeśli korzystasz z systemem macOS lub Linux, uruchom następujące polecenie:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Aby skompilować aplikację przy użyciu interfejsu wiersza polecenia platformy .NET Core, uruchom następujące polecenie w powłoce poleceń:

        dotnet build

3. Po pomyślnym zakończeniu kompilacji, uruchom następujące polecenie, aby lokalnie uruchomić aplikację sieci web:

        dotnet run

4. Otwórz okno przeglądarki i przejdź do `http://localhost:5000`, która jest domyślny adres URL aplikacji sieci web hostowanych lokalnie.

    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Kolejne kroki

* [Zarządzanych tożsamości dla integracji zasobów platformy Azure](./integrate-azure-managed-service-identity.md)
