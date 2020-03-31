---
title: Tworzenie wystąpienia poufnej aplikacji klienckiej (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć tworzenie wystąpienia poufnej aplikacji klienckiej z opcjami konfiguracji przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1a520c5a1002e401f880fba84f8fc02a0a678133
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084739"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Tworzenie wystąpienia poufnej aplikacji klienckiej z opcjami konfiguracji przy użyciu MSAL.NET

W tym artykule opisano sposób tworzenia wystąpienia [poufnej aplikacji klienckiej](msal-client-applications.md) przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).  Tworzenie wystąpienia aplikacji z opcjami konfiguracji zdefiniowanymi w pliku ustawień.

Przed zainicjowaniem aplikacji należy najpierw [ją zarejestrować,](quickstart-register-app.md) aby aplikacja mogła być zintegrowana z platformą tożsamości firmy Microsoft. Po rejestracji mogą być potrzebne następujące informacje (które można znaleźć w witrynie Azure portal):

- Identyfikator klienta (ciąg reprezentujący identyfikator GUID)
- Adres URL dostawcy tożsamości (o nazwie wystąpienie) i grupa odbiorców logowania dla aplikacji. Te dwa parametry są łącznie znane jako organ.
- Identyfikator dzierżawy, jeśli piszesz wiersz aplikacji biznesowej wyłącznie dla twojej organizacji (również o nazwie aplikacja z jedną dzierżawą).
- Klucz tajny aplikacji (tajny ciąg klienta) lub certyfikat (typu X509Certificate2), jeśli jest to aplikacja klienta poufnego.
- W przypadku aplikacji sieci web, a czasami dla publicznych aplikacji klienckich (w szczególności gdy aplikacja musi używać brokera), należy również ustawić redirectUri, gdzie dostawca tożsamości skontaktuje się z powrotem aplikacji za pomocą tokenów zabezpieczających.

## <a name="configure-the-application-from-the-config-file"></a>Konfigurowanie aplikacji z pliku konfiguracyjnego
Nazwa właściwości opcji w MSAL.NET odpowiada nazwie właściwości `AzureADOptions` w ASP.NET Core, więc nie trzeba pisać żadnego kodu kleju.

Konfiguracja aplikacji ASP.NET Core jest opisana w pliku *appsettings.json:*

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

Począwszy od MSAL.NET wersji 3.x, można skonfigurować poufną aplikację kliencką z pliku konfiguracyjnego.

W klasie, w której chcesz skonfigurować i utworzyć wystąpienia `ConfidentialClientApplicationOptions` aplikacji, należy zadeklarować obiekt.  Powiąż odczyt konfiguracji ze źródła (w tym pliku appconfig.json) `IConfigurationRoot.Bind()` z wystąpieniem opcji aplikacji, używając metody z [pakietu nuget Microsoft.Extensions.Configuration.Binder:](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder)

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Dzięki temu zawartość sekcji "AzureAD" pliku *appsettings.json,* aby powiązać z `ConfidentialClientApplicationOptions` odpowiednimi właściwościami obiektu.  Następnie zbuduj `ConfidentialClientApplication` obiekt:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Dodawanie konfiguracji środowiska uruchomieniowego
W poufnej aplikacji klienckiej zwykle masz pamięć podręczną na użytkownika. W związku z tym należy uzyskać pamięci podręcznej skojarzone z użytkownikiem i poinformować konstruktora aplikacji, że chcesz go używać. W ten sam sposób może mieć dynamicznie obliczane przekierowanie URI. W takim przypadku kod jest następujący:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

