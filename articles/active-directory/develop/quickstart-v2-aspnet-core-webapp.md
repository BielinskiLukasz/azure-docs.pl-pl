---
title: Przewodnik Szybki start dotyczący aplikacji internetowej ASP.NET Core usługi Azure AD w wersji 2.0 | Microsoft Docs
description: Dowiedz się, jak zaimplementować logowanie firmy Microsoft w aplikacji internetowej ASP.NET Core za pomocą protokołu OpenID Connect.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/09/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 4849ffcc6fd71a0b88b270f2e6cbdb23b18ecc76
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51611645"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Szybki start: dodawanie logowania przy użyciu konta Microsoft do aplikacji internetowej ASP.NET

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

W tym przewodniku Szybki start dowiesz się, w jaki sposób aplikacja internetowa ASP.NET Core może przeprowadzać logowanie kont osobistych (hotmail.com, outlook.com itp.) oraz kont służbowych z dowolnego wystąpienia usługi Azure Active Directory (Azure AD).

![Jak działa przykładowa aplikacja wygenerowana w tym przewodniku Szybki start](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.png)


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Istnieją dwie opcje uruchamiania aplikacji Szybki start:
> * [Expresowe] [Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ręczne] [Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Przejdź do witryny [Azure Portal — Rejestracja aplikacji (wersja zapoznawcza)](https://aka.ms/aspnetcore2-1-aad-quickstart-v2).
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby jednym kliknięciem pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
> Aby zarejestrować aplikację i ręcznie dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
> 1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
> 1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory**, a następnie pozycję **Rejestracje aplikacji (wersja zapoznawcza)** > **Nowa rejestracja**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
>    - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `AspNetCore-Quickstart`.
>    - W polu **Adres URL odpowiedzi** dodaj wartość `https://localhost:44321/`, a następnie wybierz polecenie **Zarejestruj**.
> 1. Wybierz menu **Uwierzytelnianie**, a następnie dodaj następujące informacje:
>    - W polu **Adres URL odpowiedzi** dodaj wartość `https://localhost:44321/signin-oidc`, a następnie wybierz polecenie **Zarejestruj**.
>    - W sekcji **Ustawienia zaawansowane** ustaw pole **Adres URL wylogowywania** na wartość `https://localhost:44321/signout-oidc`.
>    - W obszarze **Niejawne przyznanie** zaznacz pole wyboru **Tokeny Identyfikatorów**.
>    - Wybierz pozycję **Zapisz**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
> Aby przykład kodu w tym przewodniku Szybki Start działał, należy dodać adresy URL odpowiedzi jako `https://localhost:44321/` i `https://localhost:44321/signin-oidc`, dodać adres URL wylogowywania jako `https://localhost:44321/signout-oidc`, a następnie zażądać, aby tokeny Identyfikatorów były wystawiane przez punkt końcowy autoryzacji.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-aspnet-webapp/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-your-aspnet-core-project"></a>Krok 2. Pobieranie projektu ASP.NET Core

- [Pobierz rozwiązanie programu Visual Studio 2017.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Krok 3. Konfigurowanie projektu programu Visual Studio

1. Wyodrębnij plik zip do folderu lokalnego w ramach folderu głównego, na przykład **C:\Azure-Samples**.
1. Jeśli używasz programu Visual Studio 2017, otwórz rozwiązanie w programie Visual Studio (opcjonalne).
1. Edytuj plik **appsettings.json**. Znajdź plik `ClientId` i zastąp ciąg `Enter_the_Application_Id_here` wartością **identyfikatora aplikacji (klienta)** zarejestrowanej właśnie aplikacji. 

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "Enter_the_Tenant_Info_Here"
    ```

> [!div renderon="docs"]
> Gdzie:
> - `Enter_the_Application_Id_here` to **identyfikator aplikacji (klienta)** dla aplikacji zarejestrowanej w witrynie Azure Portal. **Identyfikator aplikacji (klienta)** możesz znaleźć na stronie aplikacji **Przegląd** aplikacji.
> - `Enter_the_Tenant_Info_Here` to jedna z następujących opcji:
>   - Jeśli aplikacja obsługuje tryb **Konta tylko w tym katalogu organizacyjnym**, zastąp tę wartość za pomocą wartości **Identyfikator dzierżawy** lub **Nazwa dzierżawy** (na przykład contoso.microsoft.com)
>   - Jeśli aplikacja obsługuje tryb**Konta w dowolnym katalogu organizacyjnym**, zastąp tę wartość za pomocą wartości `organizations`
>   - Jeśli aplikacja obsługuje tryb **Wszyscy użytkownicy kont Microsoft**, zastąp tę wartość za pomocą wartości `common`
>
> > [!TIP]
> > Aby znaleźć wartości **Identyfikator aplikacji (klienta)**, **Identyfikator katalogu (dzierżawy)** i **Obsługiwane typy kont**, przejdź do strony **Przegląd** w witrynie Azure Portal.

## <a name="more-information"></a>Więcej informacji

Ta sekcja zawiera omówienie kodu wymaganego do logowania użytkowników. Przydatne może być zrozumienie działania kodu i głównych argumentów oraz poznanie kwestii dotyczących tego, czy warto dodawać funkcję logowania do istniejącej aplikacji ASP.NET Core.

### <a name="startup-class"></a>Klasa początkowa

Oprogramowanie pośredniczące *Microsoft.AspNetCore.Authentication* używa klasy początkowej, która jest wykonywana podczas inicjowania procesu hostingu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
  services.Configure<CookiePolicyOptions>(options =>
  {
    // This lambda determines whether user consent for non-essential cookies is needed for a given request.
    options.CheckConsentNeeded = context => true;
    options.MinimumSameSitePolicy = SameSiteMode.None;
  });

  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
          .AddAzureAD(options => Configuration.Bind("AzureAd", options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
    options.Authority = options.Authority + "/v2.0/";         // Azure AD v2.0

    options.TokenValidationParameters.ValidateIssuer = false; // accept several tenants (here simplified)
  });

  services.AddMvc(options =>
  {
     var policy = new AuthorizationPolicyBuilder()
                     .RequireAuthenticatedUser()
                     .Build();
     options.Filters.Add(new AuthorizeFilter(policy));
  })
  .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
}
```

Metoda `AddAuthentication` służy do konfigurowania usługi pod kątem dodawania uwierzytelniania na podstawie plików cookie, które jest używane w scenariuszach przeglądarki, oraz do wysyłania wezwania względem protokołu OpenID Connect. 

Wiersz zawierający element `.AddAzureAd` dodaje uwierzytelnianie usługi Azure AD do aplikacji. Aplikacja jest po tym skonfigurowana do logowania przy użyciu punktu końcowego usługi Azure AD w wersji 2.0.

> |Lokalizacja  |  |
> |---------|---------|
> | ClientId  | Identyfikator aplikacji (klient) z aplikacji zarejestrowanej w witrynie Azure Portal. |
> | Urząd | Punkt końcowy usługi STS na potrzeby uwierzytelnienia użytkownika. Zazwyczaj jest to adres https://login.microsoftonline.com/{tenant}/v2.0 dla chmury publicznej, gdzie parametr {tenant} jest nazwą dzierżawy, identyfikatorem dzierżawy lub ma wartość *common* na potrzeby odwołania do wspólnego punktu końcowego (używany dla aplikacji z wieloma dzierżawami) |
> | TokenValidationParameters | Lista parametrów na potrzeby weryfikacji tokenu. W tym przypadku parametr `ValidateIssuer` ustawiono na wartość `false`, aby wskazać, że może akceptować logowania z dowolnych kont osobistych i służbowych. |

### <a name="protect-a-controller-or-a-controllers-method"></a>Ochrona kontrolera lub metody kontrolera

Kontroler lub jego metody można chronić za pomocą atrybutu `[Authorize]`. Ten atrybut ogranicza dostęp do kontrolera lub jego metod, zezwalając tylko na uwierzytelnionych użytkowników, co oznacza, że wezwanie do uwierzytelnienia może zostać uruchomione w celu uzyskania dostępu do kontrolera, jeśli użytkownik nie został uwierzytelniony.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z repozytorium GitHub na potrzeby tego przewodnika Szybki start dotyczącego platformy ASP.NET Core, aby uzyskać więcej informacji, w tym instrukcje związane ze sposobem dodawania uwierzytelniania do zupełnie nowej aplikacji internetowej ASP.NET Core:

> [!div class="nextstepaction"]
> [Przykładowy kod aplikacji internetowej ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

