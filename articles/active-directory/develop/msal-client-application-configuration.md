---
title: Konfiguracja aplikacji klienta (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Informacje o opcjach konfiguracji dla klienta publicznego i poufnych aplikacji klienckich przy użyciu biblioteki uwierzytelniania firmy Microsoft (MSAL).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3fd70757fe5ce8761fd3444f3f0dd31e2bfcc3b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917067"
---
# <a name="application-configuration-options"></a>Opcje konfiguracji aplikacji

W kodzie można zainicjować nową publiczną lub poufną aplikację kliencką (lub agenta użytkownika dla MSAL. js) do uwierzytelniania i pozyskiwania tokenów. Po zainicjowaniu aplikacji klienckiej w bibliotece uwierzytelniania firmy Microsoft (MSAL) można ustawić kilka opcji konfiguracji. Te opcje są dostępne w dwóch grupach:

- Opcje rejestracji, w tym:
    - [Urząd](#authority) (składający się z [wystąpienia](#cloud-instance) dostawcy tożsamości oraz [odbiorców](#application-audience) logowania dla aplikacji i prawdopodobnie identyfikatora dzierżawy).
    - [Identyfikator klienta](#client-id).
    - [Identyfikator URI przekierowania](#redirect-uri).
    - [Klucz tajny klienta](#client-secret) (w przypadku poufnych aplikacji klienckich).
- [Opcje rejestrowania](#logging), w tym poziom rejestrowania, Kontrola danych osobowych i nazwa składnika przy użyciu biblioteki programu.

## <a name="authority"></a>Urząd

Urząd to adres URL wskazujący katalog, z którego MSAL może żądać tokenów. Typowe urzędy są następujące:

- https\://login.microsoftonline.com/\<dzierżawcy\>/, gdzie &lt;dzierżawcy&gt; to identyfikator dzierżawcy dzierżawcy Azure Active Directory (Azure AD) lub domeny skojarzonej z tą dzierżawą usługi Azure AD. Służy tylko do logowania użytkowników określonej organizacji.
- https\://login.microsoftonline.com/common/. Służy do logowania użytkowników przy użyciu kont służbowych lub osobistych kont Microsoft.
- https\://login.microsoftonline.com/organizations/. Służy do logowania użytkowników przy użyciu kont służbowych.
- https\://login.microsoftonline.com/consumers/. Służy do logowania użytkowników tylko przy użyciu osobistych kont Microsoft (znanych wcześniej jako konta usługi Windows Live ID).

Ustawienie urzędu musi być zgodne z informacjami zadeklarowanymi w portalu rejestracji aplikacji.

Adres URL urzędu składa się z wystąpienia i odbiorców.

Urząd może:
- Urząd usługi Azure AD w chmurze.
- Urząd Azure AD B2C. Zobacz [szczegóły B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- Urząd Active Directory Federation Services (AD FS). Zobacz [Pomoc techniczna AD FS](https://aka.ms/msal-net-adfs-support).

Urzędy chmury usługi Azure AD mają dwie części:
- *Wystąpienie* dostawcy tożsamości
- *Odbiorcy* logowania dla aplikacji

Wystąpienie i odbiorcy mogą być połączone i udostępniane jako adres URL urzędu certyfikacji. W wersjach programu MSAL.NET starszej niż MSAL 3. *x*, musisz samodzielnie złożyć urząd w oparciu o docelową chmurę oraz odbiorców logowania.  Ten diagram przedstawia sposób tworzenia adresu URL urzędu:

![Jak składają się adres URL urzędu](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Wystąpienie chmury

To *wystąpienie* służy do określenia, czy aplikacja loguje użytkowników z chmury publicznej platformy Azure lub z chmur krajowych. Korzystając z MSAL w kodzie, można ustawić wystąpienie chmury platformy Azure przy użyciu wyliczenia lub przekazując adres URL do [wystąpienia chmury krajowej](authentication-national-cloud.md#azure-ad-authentication-endpoints) jako członka `Instance` (jeśli jest znany).

MSAL.NET zgłosi jawny wyjątek, jeśli określono zarówno `Instance`, jak i `AzureCloudInstance`.

Jeśli nie określisz wystąpienia, aplikacja będzie ukierunkowana na wystąpienie chmury publicznej platformy Azure (wystąpienie adresu URL `https://login.onmicrosoftonline.com`).

## <a name="application-audience"></a>Odbiorcy aplikacji

Odbiorcy logowania zależą od potrzeb firmy dla Twojej aplikacji:
- Jeśli jesteś deweloperem biznesowym (LOB), prawdopodobnie utworzysz aplikację z jedną dzierżawą, która będzie używana tylko w organizacji. W takim przypadku należy określić organizację jako identyfikator dzierżawy (identyfikator wystąpienia usługi Azure AD) lub nazwę domeny skojarzoną z wystąpieniem usługi Azure AD.
- Jeśli jesteś dostawcą niezależnego dostawcy oprogramowania, możesz chcieć zalogować użytkowników przy użyciu kont służbowych w dowolnej organizacji lub w niektórych organizacjach (aplikacji wielodostępnej). Może jednak być również konieczne zalogowanie się przy użyciu osobistych kont Microsoft.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Jak określić odbiorców w kodzie/konfiguracji

Używając MSAL w kodzie, należy określić odbiorców przy użyciu jednej z następujących wartości:
- Wyliczenie odbiorców urzędu usługi Azure AD
- Identyfikator dzierżawy, który może być:
  - Identyfikator GUID (identyfikator wystąpienia usługi Azure AD) dla aplikacji z jedną dzierżawą
  - Nazwa domeny skojarzona z wystąpieniem usługi Azure AD (również w przypadku aplikacji z jedną dzierżawą)
- Jeden z tych symboli zastępczych jako identyfikator dzierżawy zamiast wyliczenia odbiorców urzędu usługi Azure AD:
    - `organizations` aplikacji wielodostępnej
    - `consumers` logowania użytkowników tylko przy użyciu ich kont osobistych
    - `common` logowania użytkowników przy użyciu kont służbowych lub osobistych kont Microsoft

MSAL zgłosi znaczący wyjątek, jeśli określono zarówno odbiorców urzędu usługi Azure AD, jak i identyfikator dzierżawy.

Jeśli nie określisz odbiorców, Twoja aplikacja będzie ukierunkowana na usługę Azure AD i osobiste konta Microsoft jako odbiorców. (Oznacza to, że `common` zostały określone.)

### <a name="effective-audience"></a>Czynni odbiorcy

Obowiązującymi odbiorcami aplikacji będzie minimalny (jeśli istnieje część wspólna) odbiorców ustawionych w aplikacji oraz odbiorców określonych w ramach rejestracji aplikacji. W rzeczywistości środowisko [rejestracje aplikacji](https://aka.ms/appregistrations) umożliwia określenie odbiorców (obsługiwanych typów kont) dla aplikacji. Aby uzyskać więcej informacji, zobacz [Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](quickstart-register-app.md).

Obecnie jedynym sposobem na uzyskanie aplikacji do logowania użytkowników tylko przy użyciu osobistych kont Microsoft jest skonfigurowanie obu tych ustawień:
- Ustaw odbiorców rejestracji aplikacji na `Work and school accounts and personal accounts`.
- Ustaw odbiorców w kodzie/konfiguracji do `AadAuthorityAudience.PersonalMicrosoftAccount` (lub `TenantID` = "konsumenci").

## <a name="client-id"></a>Identyfikator klienta

Identyfikator klienta jest unikatowym IDENTYFIKATORem aplikacji przypisanym do aplikacji przez usługę Azure AD po zarejestrowaniu aplikacji.

## <a name="redirect-uri"></a>Identyfikator URI przekierowania

Identyfikator URI przekierowania to identyfikator URI, do którego dostawca tożsamości wyśle ponownie tokeny zabezpieczające.

### <a name="redirect-uri-for-public-client-apps"></a>Identyfikator URI przekierowania dla publicznych aplikacji klienckich

Jeśli jesteś publicznym deweloperem aplikacji klienta korzystającym z usługi MSAL:
- Chcesz używać `.WithDefaultRedirectUri()` w aplikacjach klasycznych lub platformy UWP (MSAL.NET 4.1 +). Ta metoda ustawi właściwość URI przekierowania aplikacji publicznej na domyślny zalecany identyfikator URI przekierowania dla publicznych aplikacji klienckich. 

  Platforma  | Identyfikator URI przekierowania  
  ---------  | --------------
  Aplikacja klasyczna (.NET PD) | `https://login.microsoftonline.com/common/oauth2/nativeclient` 
  Platforma UWP | wartość `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`. Umożliwia to Logowanie jednokrotne w przeglądarce przez ustawienie wartości na wynik WebAuthenticationBroker. GetCurrentApplicationCallbackUri (), który należy zarejestrować.
  .NET Core | `https://localhost`. Umożliwia to użytkownikowi korzystanie z przeglądarki systemu na potrzeby uwierzytelniania interaktywnego, ponieważ program .NET Core nie ma obecnie interfejsu użytkownika dla osadzonego widoku sieci Web.

- Nie musisz dodawać identyfikatora URI przekierowania, jeśli tworzysz aplikację platformy Xamarin dla systemu Android i iOS, która nie obsługuje brokera (identyfikator URI przekierowania jest automatycznie ustawiany na `msal{ClientId}://auth` dla platformy Xamarin Android i iOS

- Należy skonfigurować identyfikator URI przekierowania w [rejestracje aplikacji](https://aka.ms/appregistrations):

   ![Identyfikator URI przekierowania w Rejestracje aplikacji](media/msal-client-application-configuration/redirect-uri.png)

Identyfikator URI przekierowania można zastąpić za pomocą właściwości `RedirectUri` (na przykład jeśli używasz brokerów). Poniżej przedstawiono kilka przykładów identyfikatorów URI przekierowania dla tego scenariusza:

- `RedirectUriOnAndroid` = "msauth-5a434691-CCB2-4fd1-b97b-b64bcfbc03fc://com.Microsoft.Identity.Client.sample";
- `RedirectUriOnIos` = $ "msauth. {Pakiet. ID}://auth ";

Aby uzyskać dodatkowe informacje na temat systemu iOS, zobacz [Migrowanie aplikacji systemu iOS, które używają Microsoft Authenticator od ADAL.NET do MSAL.NET](msal-net-migration-ios-broker.md) i korzystania [z brokera w systemie iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS).
Aby uzyskać dodatkowe informacje dotyczące systemu Android, zobacz [uwierzytelnianie przez brokera w systemie Android](brokered-auth.md).

### <a name="redirect-uri-for-confidential-client-apps"></a>Identyfikator URI przekierowania dla poufnych aplikacji klienckich

W przypadku aplikacji sieci Web identyfikator URI przekierowania (lub identyfikator URI odpowiedzi) to identyfikator URI, który będzie używany przez usługę Azure AD do wysyłania tokenu z powrotem do aplikacji. Ten identyfikator URI może być adresem URL aplikacji sieci Web/internetowego interfejsu API, jeśli jest to jedna z nich. Identyfikator URI przekierowania musi być zarejestrowany w rejestracji aplikacji. Ta rejestracja jest szczególnie ważna w przypadku wdrożenia aplikacji, która została wstępnie przetestowana lokalnie. Następnie należy dodać adres URL odpowiedzi wdrożonej aplikacji w portalu rejestracji aplikacji.

W przypadku aplikacji demonów nie trzeba określać identyfikatora URI przekierowania.

## <a name="client-secret"></a>Klucz tajny klienta

Ta opcja określa klucz tajny klienta dla poufnej aplikacji klienckiej. Ten wpis tajny (hasło aplikacji) jest udostępniany przez portal rejestracji aplikacji lub dostarczany do usługi Azure AD podczas rejestracji aplikacji przy użyciu programu PowerShell AzureAD, programu PowerShell AzureRM lub interfejsu wiersza polecenia platformy Azure.

## <a name="logging"></a>Rejestrowanie

Inne opcje konfiguracji umożliwiają rejestrowanie i rozwiązywanie problemów. Zobacz artykuł [Rejestrowanie](msal-logging.md) , aby uzyskać szczegółowe informacje na temat korzystania z nich.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [tworzeniu wystąpień aplikacji klienckich za pomocą MSAL.NET](msal-net-initializing-client-applications.md).
Dowiedz się więcej o [tworzeniu wystąpień aplikacji klienckich przy użyciu MSAL. js](msal-js-initializing-client-applications.md).
