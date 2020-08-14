---
title: Przewodnik Szybki start dotyczący używania usługi Azure App Configuration z programem .NET Framework | Microsoft Docs
description: Przewodnik Szybki start dotyczący korzystania z usługi Azure App Configuration z aplikacjami .NET Framework
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: lcozzens
ms.openlocfilehash: 63b7f8b28e339abde326bf3dca34d9ee6d816a31
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2020
ms.locfileid: "88209917"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Szybki Start: Tworzenie aplikacji .NET Framework przy użyciu konfiguracji aplikacji platformy Azure

W tym przewodniku szybki start dołączysz konfigurację aplikacji platformy Azure do aplikacji konsolowej opartej na .NET Framework w celu scentralizowanego przechowywania i zarządzania ustawieniami aplikacji oddzielonymi od kodu.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wybierz pozycję **Eksplorator konfiguracji**  >  **Utwórz**  >  **klucz-wartość** , aby dodać następujące pary klucz-wartość:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:Message | Dane z usługi Azure App Configuration |

    Dla tej pory pozostaw pustą **etykietę** i **Typ zawartości** .

7. Wybierz przycisk **Zastosuj**.

## <a name="create-a-net-console-app"></a>Tworzenie aplikacji konsolowej platformy .NET

1. Uruchom program Visual Studio i wybierz pozycję **plik**  >  **Nowy**  >  **projekt**.

1. W obszarze **Utwórz nowy projekt**odfiltruj typ projektu **konsoli** i kliknij pozycję **Aplikacja konsolowa (.NET Framework)**. Wybierz pozycję **Dalej**.

1. W obszarze **Konfigurowanie nowego projektu**wprowadź nazwę projektu. W obszarze **Struktura**wybierz pozycję **.NET Framework 4.7.1** lub wyższy. Wybierz pozycję **Utwórz**.

## <a name="connect-to-an-app-configuration-store"></a>Nawiązywanie połączenia z magazynem konfiguracji aplikacji

1. Kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**. Na karcie **Przeglądaj** Wyszukaj i Dodaj następujące pakiety NuGet do swojego projektu.

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 or later
    System.Configuration.ConfigurationManager version 4.6.0 or later
    ```

1. Zaktualizuj plik *App.config* projektu w następujący sposób:

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   Parametry połączenia magazynu konfiguracji aplikacji są odczytywane ze zmiennej środowiskowej `ConnectionString` . Dodaj `Environment` konstruktora konfiguracji przed `MyConfigStore` `configBuilders` właściwością w `appSettings` sekcji.

1. Otwórz *program.cs*i zaktualizuj metodę, `Main` Aby użyć konfiguracji aplikacji przez wywołanie `ConfigurationManager` .

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString** na parametry połączenia magazynu konfiguracji aplikacji. Jeśli używasz wiersza polecenia systemu Windows, uruchom następujące polecenie:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```
1. Uruchom ponownie program Visual Studio, aby zmiany zaczęły obowiązywać. Naciśnij kombinację klawiszy CTRL + F5, aby skompilować i uruchomić aplikację konsolową.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono nowy magazyn konfiguracji aplikacji i używał go z aplikacją konsolową .NET Framework. Wartość `AppSettings` `ConfigurationManager` nie zostanie zmieniona po uruchomieniu aplikacji. Biblioteka dostawcy konfiguracji .NET Standard konfiguracji aplikacji, ale również może być używana w aplikacji .NET Framework. Aby dowiedzieć się, jak włączyć dynamiczne Odświeżanie ustawień konfiguracji aplikacji .NET Framework, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Włączanie dynamicznej konfiguracji](./enable-dynamic-configuration-dotnet.md)
