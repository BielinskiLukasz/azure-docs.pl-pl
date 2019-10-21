---
title: Migrowanie interfejsów API sieci Web opartych na OWIN do b2clogin.com-Azure Active Directory B2C
description: Dowiedz się, jak włączyć interfejs API sieci Web platformy .NET, aby obsługiwał tokeny wystawione przez wielu wystawców tokenów podczas migrowania aplikacji do usługi b2clogin.com.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a8a6b4f90fe3f1e60341cc59e7d81870c82e843b
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533765"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom"></a>Migrowanie internetowego interfejsu API opartego na OWIN do usługi b2clogin.com

W tym artykule opisano technikę włączania obsługi wielu wystawców tokenów w interfejsach API sieci Web implementujących [otwarty interfejs sieci Web dla platformy .NET (Owin)](http://owin.org/). Obsługa wielu punktów końcowych tokenów jest przydatna w przypadku migrowania Azure Active Directory B2C (Azure AD B2C) interfejsów API i ich aplikacji z *login.microsoftonline.com* do *b2clogin.com*.

Dzięki dodaniu obsługi w interfejsie API do akceptowania tokenów wystawionych przez zarówno b2clogin.com, jak i login.microsoftonline.com, można migrować aplikacje sieci Web w sposób przygotowany przed usunięciem obsługi tokenów wystawionych przez login.microsoftonline.com z interfejsu API.

Poniższe sekcje przedstawiają przykład sposobu włączania wielu wystawców w interfejsie API sieci Web, który używa składników oprogramowania pośredniczącego [Microsoft Owin][katana] (Katana). Chociaż przykłady kodu są specyficzne dla oprogramowania pośredniczącego Microsoft OWIN, ogólna technika powinna być stosowana do innych bibliotek OWIN.

> [!NOTE]
> Ten artykuł jest przeznaczony dla klientów Azure AD B2C z aktualnie wdrożonymi interfejsami API i `login.microsoftonline.com` aplikacjami, które odwołują się do programu `b2clogin.com` i które chcą migrować do zalecanego punktu końcowego. Jeśli konfigurujesz nową aplikację, użyj [b2clogin.com](b2clogin.md) jako kierowany.

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem kroków opisanych w tym artykule potrzebne są następujące Azure AD B2C zasoby:

* [Przepływy użytkownika](tutorial-create-user-flows.md) lub [zasady niestandardowe](active-directory-b2c-get-started-custom.md) utworzone w dzierżawie

## <a name="get-token-issuer-endpoints"></a>Pobierz punkty końcowe wystawcy tokenów

Najpierw należy uzyskać identyfikatory URI punktu końcowego wystawcy tokenów dla każdego wystawcy, który ma być obsługiwany w interfejsie API. Aby uzyskać punkty końcowe *b2clogin.com* i *login.microsoftonline.com* obsługiwane przez dzierżawę Azure AD B2C, użyj poniższej procedury w Azure Portal.

Zacznij od wybrania jednego z istniejących przepływów użytkownika:

1. Przejdź do dzierżawy Azure AD B2C w [Azure Portal](https://portal.azure.com)
1. W obszarze **zasady**wybierz pozycję **przepływy użytkownika (zasady).**
1. Wybierz istniejące zasady, na przykład *B2C_1_signupsignin1*, a następnie wybierz pozycję **Uruchom przepływ użytkownika** .
1. Pod nagłówkiem **Uruchom przepływ użytkownika** w górnej części strony wybierz hiperlink, aby przejść do punktu końcowego odnajdywania programu OpenID Connect Connect dla tego przepływu użytkownika.

    ![Dobrze znane hiperłącze URI na stronie uruchamiania teraz Azure Portal](media/multi-token-endpoints/portal-01-policy-link.png)

1. Na stronie, która zostanie otwarta w przeglądarce, Zapisz `issuer` wartość, na przykład:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. Użyj listy rozwijanej **Wybierz domenę** , aby wybrać drugą domenę, następnie ponownie wykonaj dwa poprzednie kroki i Zapisz jej `issuer` wartość.

Teraz powinny być zarejestrowane dwa identyfikatory URI, które są podobne do następujących:

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>Zasady niestandardowe

Jeśli masz zasady niestandardowe zamiast przepływów użytkownika, możesz użyć podobnego procesu, aby uzyskać identyfikatory URI wystawcy.

1. Przejdź do dzierżawy Azure AD B2C
1. Wybierz **platformę obsługi tożsamości**
1. Wybierz jedną z zasad jednostki uzależnionej, na przykład *B2C_1A_signup_signin*
1. Użyj listy rozwijanej **Wybierz domenę** , aby wybrać domenę, na przykład *yourtenant.b2clogin.com*
1. Wybierz hiperlink wyświetlany w obszarze **punkt końcowy odnajdywania programu OpenID Connect Connect**
1. `issuer` Zapisz wartość
1. Wykonaj kroki 4-6 dla innej domeny, na przykład *login.microsoftonline.com*

## <a name="get-the-sample-code"></a>Pobieranie przykładowego kodu

Teraz, gdy masz oba identyfikatory URI punktu końcowego tokenu, musisz zaktualizować swój kod, aby określić, że oba punkty końcowe są prawidłowymi wystawcami. Aby zapoznać się z przykładem, Pobierz lub Sklonuj przykładową aplikację, a następnie zaktualizuj przykład, aby obsługiwać oba punkty końcowe jako prawidłowych wystawców.

Pobierz archiwum: [Active-Directory-B2C-dotnet-webapp-and-WebAPI-Master. zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Włączanie wielu wystawców w interfejsie API sieci Web

W tej sekcji należy zaktualizować kod, aby określić, że oba punkty końcowe wystawcy tokenu są prawidłowe.

1. Otwórz rozwiązanie **B2C-WebAPI-dotnet. sln** w programie Visual Studio
1. W projekcie **TaskService** Otwórz plik *TaskService\\App_Start\\ **Startup.auth.cs** w* edytorze
1. Dodaj następującą `using` dyrektywę na początku pliku:

    `using System.Collections.Generic;`
1. [`ValidIssuers`][validissuers] Dodaj [Właściwość`TokenValidationParameters`][tokenvalidationparameters] do definicji i określ oba identyfikatory URI zarejestrowane w poprzedniej sekcji:

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters`jest dostarczany przez MSAL.NET i jest zużywany przez oprogramowanie pośredniczące OWIN w następnej sekcji kodu w *Startup.auth.cs*. W przypadku wybrania wielu prawidłowych wystawców potok aplikacji OWIN ma świadomość, że oba punkty końcowe tokenu są prawidłowymi wystawcami.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

Jak wspomniano wcześniej, inne biblioteki OWIN zazwyczaj oferują podobną funkcję do obsługi wielu wystawców. Chociaż udostępnianie przykładów dla każdej biblioteki jest poza zakresem tego artykułu, można użyć podobnej techniki dla większości bibliotek.

## <a name="switch-endpoints-in-web-app"></a>Przełączanie punktów końcowych w aplikacji sieci Web

Przy użyciu obu identyfikatorów URI teraz obsługiwanych przez internetowy interfejs API, musisz teraz zaktualizować aplikację sieci Web tak, aby pobierali tokeny z punktu końcowego b2clogin.com.

Przykładowo można skonfigurować przykładową aplikację sieci Web, aby korzystała z nowego punktu końcowego, `ida:AadInstance` modyfikując wartość w *pliku\\TaskWebApp **Web. config** projektu* **TaskWebApp** .

Zmień wartość w *pliku Web. config* elementu TaskWebApp tak, aby odwoływał `{your-b2c-tenant-name}.b2clogin.com` `login.microsoftonline.com`się do niego zamiast. `ida:AadInstance`

Przed:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

Po (Zamień `{your-b2c-tenant}` na nazwę dzierżawy B2C):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

W przypadku konstruowania ciągów punktów końcowych podczas wykonywania aplikacji sieci Web, b2clogin.com punkty końcowe są używane, gdy żąda tokenów.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono metodę konfigurowania internetowego interfejsu API implementującego oprogramowanie Microsoft OWIN pośredniczące (Katana) w celu akceptowania tokenów z wielu punktów końcowych wystawcy. Jak można zauważyć, istnieje kilka innych ciągów w plikach *Web. config* projektów TaskService i TaskWebApp, które należy zmienić, jeśli chcesz skompilować i uruchomić te projekty względem własnej dzierżawy. Przyjrzyjmy się odpowiednim modyfikacjom projektów, jeśli chcesz, aby były one widoczne w działaniu, jednak pełny wgląd w to robi to poza zakresem tego artykułu.

Aby uzyskać więcej informacji na temat różnych typów tokenów zabezpieczających emitowanych przez Azure AD B2C, zobacz [Omówienie tokenów w Azure Active Directory B2C](active-directory-b2c-reference-tokens.md).

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/
[validissuers]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
