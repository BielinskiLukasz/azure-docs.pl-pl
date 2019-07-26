---
title: Różnice między MSAL. js i ADAL. js | Azure
description: Dowiedz się więcej o różnicach między biblioteką uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL. js) i biblioteki uwierzytelniania usługi Azure AD dla języka JavaScript (ADAL. js) i sposobu wybierania, który ma być używany.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 328db116cebda5eb288f04cc89e2c85550c38083
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68421190"
---
# <a name="differences-between-msal-js-and-adal-js"></a>Różnice między MSAL JS i ADAL JS

Zarówno Biblioteka uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL. js), jak i Biblioteka Azure AD Authentication Library dla języka JavaScript (ADAL. js) są używane do uwierzytelniania jednostek usługi Azure AD i żądania tokenów z usługi Azure AD. Do tej pory większość deweloperów pracowała z usługą Azure AD dla deweloperów (v 1.0) do uwierzytelniania tożsamości usługi Azure AD (konta służbowe), żądając tokenów przy użyciu biblioteki ADAL. Teraz korzystając z MSAL. js, można uwierzytelnić szerszy zbiór tożsamości firmy Microsoft (tożsamości usługi Azure AD i kont Microsoft, a także konta społecznościowe i lokalne za pośrednictwem Azure AD B2C) za pośrednictwem platformy tożsamości firmy Microsoft (v 2.0).

W tym artykule opisano sposób wybierania biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL. js) i biblioteki Azure AD Authentication Library dla języka JavaScript (ADAL. js) i porównywania dwóch bibliotek.

## <a name="choosing-between-adaljs-and-msaljs"></a>Wybieranie między bibliotekami ADAL. js i MSAL. js

W większości przypadków chcesz używać platformy tożsamości firmy Microsoft i MSAL. js, która jest najnowszą generacji bibliotek uwierzytelniania firmy Microsoft. Korzystając z MSAL. js, uzyskujesz tokeny dla użytkowników logujących się do aplikacji przy użyciu usługi Azure AD (konta służbowe), kont Microsoft (osobistych) lub Azure AD B2C.

Jeśli znasz już punkt końcowy v 1.0 (i ADAL. js), możesz zapoznać się z [informacjami o tym, co się dzieje w punkcie końcowym v 2.0?](active-directory-v2-compare.md).

Jednak nadal musisz użyć biblioteki ADAL. js, jeśli aplikacja musi zalogować się użytkowników przy użyciu wcześniejszych wersji [Active Directory Federation Services (AD FS)](/windows-server/identity/active-directory-federation-services).

## <a name="key-differences-in-authentication-with-msaljs"></a>Kluczowe różnice w uwierzytelnianiu za pomocą MSAL. js

### <a name="core-api"></a>Podstawowy interfejs API

* Biblioteka ADAL. js używa [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) jako reprezentacji wystąpienia połączenia aplikacji z serwerem autoryzacji lub dostawcą tożsamości za pośrednictwem adresu URL urzędu certyfikacji. W przeciwieństwie do interfejsu API MSAL. js jest przeznaczony dla aplikacji klienckiej agenta użytkownika (forma publicznej aplikacji klienckiej, w której kod klienta jest wykonywany w agencie użytkownika, takim jak przeglądarka sieci Web). Udostępnia `UserAgentApplication` klasę reprezentującą wystąpienie kontekstu uwierzytelniania aplikacji z serwerem autoryzacji. Aby uzyskać więcej informacji, zobacz [Inicjowanie przy użyciu MSAL. js](msal-js-initializing-client-applications.md).

* W bibliotece ADAL. js metody uzyskiwania tokenów są skojarzone z jednym zestawem uprawnień w `AuthenticationContext`. W MSAL. js żądania tokenu pozyskiwania mogą przyjmować różne wartości urzędu niż to, `UserAgentApplication`co jest ustawione w. Dzięki temu MSAL. js może pobierać i buforować tokeny oddzielnie dla wielu dzierżawców i kont użytkowników w tej samej aplikacji.

* Metoda uzyskiwania i odnawiania tokenów dyskretnie bez monitowania użytkowników o nazwę `acquireToken` w bibliotece ADAL. js. W MSAL. js ta metoda nazywa `acquireTokenSilent` się bardziej opisową funkcją.

### <a name="authority-value-common"></a>Wartość urzędu`common`

W wersji 1.0 korzystanie z `https://login.microsoftonline.com/common` urzędu umożliwi użytkownikom logowanie się przy użyciu dowolnego konta usługi Azure AD (dla dowolnej organizacji).

W wersji 2.0 przy użyciu `https://login.microsoftonline.com/common` urzędu użytkownicy będą mogli zalogować się przy użyciu dowolnego konta organizacji usługi Azure AD lub konta osobistego firmy Microsoft (MSA). Aby ograniczyć logowanie tylko do kont usługi Azure AD (takie samo zachowanie jak w przypadku biblioteki ADAL. js), należy użyć `https://login.microsoftonline.com/organizations`programu. Aby uzyskać szczegółowe informacje, `authority` zobacz opcja konfiguracji [zainicjowana przy użyciu MSAL. js](msal-js-initializing-client-applications.md).

### <a name="scopes-for-acquiring-tokens"></a>Zakresy do pozyskiwania tokenów
* Zakres zamiast parametru zasobu w żądaniach uwierzytelnienia w celu uzyskania tokenów

    Protokół v 2.0 używa zakresów zamiast zasobów w żądaniach. Innymi słowy, gdy aplikacja musi zażądać tokenów mających uprawnienia do zasobu, takiego jak MS Graph, różnica wartości przesyłanych do metod biblioteki jest następująca:

    v 1.0: Resource = https\://Graph.Microsoft.com

    v 2.0: Scope = https\://Graph.Microsoft.com/user.Read

    Możesz zażądać zakresów dla dowolnego interfejsu API zasobów przy użyciu identyfikatora URI interfejsu API w tym formacie: appidURI/Scope na przykład: https\/:/mytenant.onmicrosoft.com/myapi/API.Read

    Tylko dla programu MS interfejs API programu Graph wartość `user.read` zakresu jest mapowana na https://graph.microsoft.com/User.Read i może być używana zamiennie.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Zakresy dynamiczne na potrzeby przyrostowej zgody.

    Podczas kompilowania aplikacji przy użyciu wersji 1.0 należy zarejestrować pełen zestaw uprawnień (zakresy statyczne) wymagane przez aplikację, aby użytkownik mógł wyrazić zgodę na zalogowanie się. W wersji 2.0 można użyć parametru scope, aby zażądać uprawnień w odpowiednim czasie. Są one nazywane zakresami dynamicznymi. Dzięki temu użytkownik może zapewnić przyrostową zgodę na zakresy. Tak więc jeśli na początku użytkownik chce tylko zalogować się do aplikacji i nie ma żadnego rodzaju dostępu, możesz to zrobić. Jeśli później potrzebujesz możliwości odczytywania kalendarza użytkownika, możesz zażądać zakresu kalendarza w metodach acquireToken i uzyskać zgodę użytkownika. Przykład:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Zakresy interfejsów API 1.0

    Podczas uzyskiwania tokenów dla interfejsów API języka v 1.0 przy użyciu MSAL. js można zażądać wszystkich statycznych zakresów zarejestrowanych w interfejsie API `.default` przez dołączenie do identyfikatora URI aplikacji interfejsu API jako zakresu. Na przykład:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz [porównanie v 1.0 i 2.0](active-directory-v2-compare.md).
