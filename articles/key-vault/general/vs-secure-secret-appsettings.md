---
title: Bezpieczne Zapisywanie ustawień tajnych aplikacji dla aplikacji sieci Web — Azure Key Vault | Microsoft Docs
description: Sposób bezpiecznego zapisywania ustawień tajnych aplikacji, takich jak poświadczenia platformy Azure lub klucze interfejsu API innych firm za pomocą ASP.NET Core Key Vault Provider, klucza tajnego użytkownika lub konstruktorów konfiguracji programu .NET 4.7.1
services: visualstudio
author: cawaMS
manager: paulyuk
editor: ''
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: cawa
ms.openlocfilehash: bcacd5d2ed9e325383ec7ae75002ae0a6213111c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81429761"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Bezpieczne Zapisywanie ustawień tajnych aplikacji dla aplikacji sieci Web

## <a name="overview"></a>Omówienie
W tym artykule opisano sposób bezpiecznego zapisywania ustawień konfiguracji tajnych aplikacji dla aplikacji platformy Azure.

Tradycyjnie wszystkie ustawienia konfiguracji aplikacji sieci Web są zapisywane w plikach konfiguracji, takich jak Web.config. To rozwiązanie prowadzi do sprawdzania ustawień tajnych, takich jak poświadczenia w chmurze, do publicznych systemów kontroli źródła, takich jak GitHub. W tym czasie może być trudne przestrzeganie najlepszych rozwiązań w zakresie zabezpieczeń z powodu obciążenia wymaganego do zmiany kodu źródłowego i ponownego skonfigurowania ustawień deweloperskich.

Aby upewnić się, że proces tworzenia jest bezpieczny, biblioteki narzędzi i struktur struktury są tworzone w celu bezpiecznego zapisywania ustawień tajnych aplikacji z minimalnym lub niezmienionym kodem źródłowym.

## <a name="aspnet-and-net-core-applications"></a>Aplikacje ASP.NET i .NET Core

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Zapisz ustawienia tajne w magazynie kluczy tajnych użytkownika, który znajduje się poza folderem kontroli źródła
Jeśli wykonujesz szybki prototyp lub nie masz dostępu do Internetu, Rozpocznij od przeniesienia ustawień tajnych poza folder kontroli źródła do magazynu kluczy tajnych użytkownika. Magazyn kluczy tajnych użytkownika to plik zapisany w folderze profilera użytkownika, więc wpisy tajne nie są zaewidencjonowane do kontroli źródła. Na poniższym diagramie przedstawiono sposób działania [klucza tajnego użytkownika](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio) .

![Klucz tajny użytkownika utrzymuje ustawienia tajne poza kontrolą źródła](../media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

W przypadku korzystania z aplikacji konsolowej programu .NET Core należy bezpiecznie zapisać tajne hasło przy użyciu Key Vault.

### <a name="save-secret-settings-in-azure-key-vault"></a>Zapisz ustawienia tajne w Azure Key Vault
Jeśli opracowujesz projekt i chcesz bezpiecznie udostępniać kod źródłowy, użyj [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

1. Utwórz Key Vault w ramach subskrypcji platformy Azure. Wypełnij wszystkie wymagane pola w interfejsie użytkownika, a następnie kliknij pozycję *Utwórz* w dolnej części bloku.

    ![Utwórz Azure Key Vault](../media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Udziel użytkownikowi i członkom zespołu dostępu do Key Vault. Jeśli masz duży zespół, możesz utworzyć [grupę Azure Active Directory](../../active-directory/active-directory-groups-create-azure-portal.md) i dodać tę grupę zabezpieczeń do Key Vault. Na liście rozwijanej uprawnienia do wpisów *tajnych* Sprawdź pozycję *Pobierz* i *wystaw* w obszarze *operacje zarządzania kluczami tajnymi*.
Jeśli masz już utworzoną aplikację sieci Web, przyznaj aplikacji sieci Web dostęp do Key Vault, aby mógł uzyskać dostęp do magazynu kluczy bez zapisywania wpisu tajnego w ustawieniach lub plikach aplikacji. Wyszukaj swoją aplikację sieci Web według jej nazwy i Dodaj ją w taki sam sposób, jak przyznano użytkownikom dostęp.

    ![Dodawanie zasad dostępu Key Vault](../media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Dodaj klucz tajny do Key Vault na Azure Portal. W przypadku zagnieżdżonych ustawień konfiguracji Zastąp ciąg ":" "--", aby nazwa tajnego Key Vault była prawidłowa. znak ":" nie może być nazwą wpisu tajnego Key Vault.

    ![Dodaj Key Vault tajny](../media/vs-secure-secret-appsettings/add-keyvault-secret.png)

    > [!NOTE]
    > Przed rozpoczęciem korzystania z programu Visual Studio 2017 15.6 w wersji zazalecamy zainstalowanie rozszerzenia uwierzytelniania usług platformy Azure dla programu Visual Studio. Jest to jednak przestarzałe, ponieważ funkcje są zintegrowane w ramach programu Visual Studio. W związku z tym, jeśli używasz starszej wersji programu Visual Studio 2017, Zalecamy zaktualizowanie do programu VS 2017 15,6 lub w taki sposób, aby można było używać tych funkcji natywnie i uzyskać dostęp do magazynu kluczy z użyciem tożsamości logowania programu Visual Studio.
    >

4. Dodaj następujące pakiety NuGet do projektu:

    ```
    Microsoft.Azure.KeyVault
    Microsoft.Azure.Services.AppAuthentication
    Microsoft.Extensions.Configuration.AzureKeyVault
    ```
5. Dodaj następujący kod do pliku Program.cs:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
             Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((ctx, builder) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        builder.AddAzureKeyVault(
                        keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```
6. Dodaj adres URL Key Vault do launchsettings.jsna pliku. Nazwa zmiennej środowiskowej *KEYVAULT_ENDPOINT* jest zdefiniowana w kodzie, który został dodany w kroku 6.

    ![Dodaj adres URL Key Vault jako zmienną środowiskową projektu](../media/vs-secure-secret-appsettings/add-keyvault-url.png)

7. Rozpocznij debugowanie projektu. Powinien zostać uruchomiony pomyślnie.

## <a name="aspnet-and-net-applications"></a>Aplikacje ASP.NET i .NET

Platforma .NET 4.7.1 obsługuje konstruktory konfiguracji Key Vault i wpisów tajnych, co gwarantuje, że wpisy tajne mogą być przenoszone poza folder kontroli źródła bez wprowadzania zmian w kodzie.
Aby to zrobić, [Pobierz program .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) i Migruj swoją aplikację, jeśli używasz starszej wersji programu .NET Framework.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Zapisz ustawienia tajne w pliku tajnym, który znajduje się poza folderem kontroli źródła
Jeśli piszesz krótki prototyp i nie chcesz udostępniać zasobów platformy Azure, przejdź do tej opcji.

1. Zainstaluj następujący pakiet NuGet w projekcie
    ```
    Microsoft.Configuration.ConfigurationBuilders.Base
    ```

2. Utwórz plik podobny do poniższego. Zapisz ją w lokalizacji poza folderem projektu.

    ```xml
    <root>
        <secrets ver="1.0">
            <secret name="secret1" value="foo_one" />
            <secret name="secret2" value="foo_two" />
        </secrets>
    </root>
    ```

3. Zdefiniuj plik tajny jako konstruktora konfiguracji w pliku Web.config. Umieść tę sekcję przed sekcją *AppSettings* .

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. Określ sekcję appSettings przy użyciu programu Secret Configuration Builder. Upewnij się, że istnieje wpis dla ustawienia tajnego z wartością fikcyjną.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Debuguj aplikację. Powinien zostać uruchomiony pomyślnie.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Zapisywanie ustawień tajnych w Azure Key Vault
Postępuj zgodnie z instrukcjami w sekcji ASP.NET Core, aby skonfigurować Key Vault dla projektu.

1. Zainstaluj następujący pakiet NuGet w projekcie
   ```
   Microsoft.Configuration.ConfigurationBuilders.UserSecrets
   ```

2. Zdefiniuj Key Vault konstruktora konfiguracji w Web.config. Umieść tę sekcję przed sekcją *AppSettings* . Zastąp wartość Nazwa *magazynu* nazwą Key Vault, jeśli key Vault znajduje się na publicznej platformie Azure, lub pełny identyfikator URI w przypadku korzystania z chmury suwerennej.

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>
    <configBuilders>
        <builders>
            <add name="AzureKeyVault" vaultName="Test911" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```
3. Określ sekcję appSettings przy użyciu konstruktora konfiguracji Key Vault. Upewnij się, że dla ustawienia tajnego istnieje wpis z wartością fikcyjną.

   ```xml
   <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="secret" value="" />
   </appSettings>
   ```

4. Rozpocznij debugowanie projektu. Powinien zostać uruchomiony pomyślnie.
