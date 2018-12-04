---
title: Użyj usługi Azure AD w wersji 2.0, aby logować użytkowników za pomocą ROPC | Dokumentacja firmy Microsoft
description: Obsługa przeglądarki bez uwierzytelniania są przekazywane przy użyciu przyznania poświadczeń hasła właściciela zasobu.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 4eb6850e4b6e267e0b4ef83f7639e90308cee989
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52841442"
---
# <a name="azure-active-directory-v20-and-the-oauth-20-resource-owner-password-credential"></a>Azure Active Directory w wersji 2.0 i poświadczeń hasła właściciela zasobów OAuth 2.0

Usługa Azure Active Directory (Azure AD) obsługuje [przyznania poświadczeń hasła właściciela zasobu (ROPC)](https://tools.ietf.org/html/rfc6749#section-4.3), co umożliwia aplikacji do logowania użytkownika dzięki obsłudze bezpośrednio swoje hasło. Przepływ ROPC wymaga wysokiego stopnia narażenia zaufania i użytkowników i deweloperów należy używać tylko ten przepływ, gdy nie można użyć innych, bardziej bezpiecznymi przepływów.

> [!Important]
> * Punktu końcowego v2.0 usługi Azure AD obsługuje tylko ROPC dla dzierżaw usługi Azure AD, nie osobistych kont. Oznacza to, że trzeba korzystać z punktem końcowym specyficznym dla dzierżawy (`https://login.microsoftonline.com/{TenantId_or_Name}`) lub `organizations` punktu końcowego.
> * Konta osobiste, które są zaproszeni do dzierżawy usługi Azure AD nie można użyć ROPC.
> * Konta, które nie mają hasła nie może zalogować się za pomocą ROPC. W tym scenariuszu zaleca się używać inny przepływ dla aplikacji zamiast tego.
> * Jeśli użytkownicy muszą użyć uwierzytelniania wieloskładnikowego (MFA) do logowania do aplikacji, będą blokowane zamiast tego.

## <a name="protocol-diagram"></a>Diagram protokołu

Na poniższym diagramie przedstawiono przepływ ROPC.

![ROPC przepływu](media/v2-oauth2-ropc/v2-oauth-ropc.png)

## <a name="authorization-request"></a>Żądanie autoryzacji

Przepływ ROPC jest pojedyncze żądanie&mdash;wysyła do klienta w identyfikacji i poświadczenia użytkownika do dostawcy tożsamości, a następnie otrzymuje w odpowiedzi tokeny. Klient musi żądać adres e-mail użytkownika (UPN) i hasło przed jej wprowadzeniem. Natychmiast po żądania zakończonego powodzeniem klient powinien gwarantuje bezpieczne wydawanie poświadczenia użytkownika z pamięci. Nigdy nie należy zapisać je.

```
// Line breaks and spaces are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token?

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&client_secret=wkubdywbc2894u
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| Parametr | Warunek | Opis |
| --- | --- | --- |
| `tenant` | Wymagane | Chcesz się zalogować użytkownika do dzierżawy katalogu. Może to być w formacie przyjaznej nazwy lub identyfikatora GUID. Nie można ustawić ten parametr `common` lub `consumers`, ale może być ustawiona na `organizations`. |
| `grant_type` | Wymagane | Musi być równa `password`. |
| `username` | Wymagane | Adres e-mail użytkownika. |
| `password` | Wymagane | Hasło użytkownika. |
| `scope` | Zalecane | Listę rozdzielonych spacjami [zakresy](v2-permissions-and-consent.md), lub uprawnienia, których wymaga aplikacja. Te zakresy muszą wyrażono zgodę wcześniej przez administratora lub przez użytkownika w przepływie interaktywne. |

### <a name="successful-authentication-response"></a>Pomyślne uwierzytelnienie odpowiedzi

Poniżej przedstawiono przykład pomyślnej odpowiedzi tokenu:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parametr | Format | Opis |
| --------- | ------ | ----------- |
| `token_type` | Ciąg | Zawsze ustawiony na wartość `Bearer`. |
| `scope` | Ciągi oddzielone spacjami | Jeśli został zwrócony token dostępu, a ten parametr zawiera listę zakresów, który token dostępu jest prawidłowy dla. |
| `expires_in`| int | Liczba sekund, które token dostępu dołączony jest nieprawidłowa dla. |
| `access_token`| Nieprzezroczysty ciąg | Wystawiony dla [zakresy](v2-permissions-and-consent.md) którego zażądano. |
| `id_token` | JWT | Jeśli wystawionych oryginalny `scope` parametru `openid` zakresu. |
| `refresh_token` | Nieprzezroczysty ciąg | Jeśli wystawionych oryginalny `scope` parametru `offline_access`. |

Można użyć tokenu odświeżania do uzyskania nowych tokenów dostępu i Odśwież tokeny przy użyciu tego samego przepływu, opisane w [dokumentacji przepływu OAuth kodów](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Odpowiedzi na błąd

Jeśli użytkownik udzielona poprawna nazwa użytkownika lub hasło lub nie odebrał żądanego zgody, uwierzytelnianie nie powiedzie się.

| Błąd | Opis | Akcja klienta |
|------ | ----------- | -------------|
| `invalid_grant` | Uwierzytelnianie nie powiodło się | Poświadczenia są niepoprawne lub klient nie ma wyrażania zgody dla żądanych zakresów. Jeśli zakresy nie są przyznawane, `consent_required` suberror zostaną zwrócone. W takim przypadku klient powinien wysłać użytkownika do monitu interakcyjnego przy użyciu widoku sieci Web lub w przeglądarce. |
| `invalid_request` | Żądanie zostało nieprawidłowo skonstruowany. | Typ udzielania nie jest obsługiwana na `/common` lub `/consumers` kontekst uwierzytelniania.  Użyj `/organizations` zamiast tego. |
| `invalid_client` | Aplikacja jest nieprawidłowo skonfigurowana | Może się to zdarzyć, jeśli `allowPublicClient` właściwość nie jest ustawiona wartość true w [manifest aplikacji](reference-app-manifest.md). `allowPublicClient` Właściwość jest niezbędne, ponieważ przydział ROPC nie ma identyfikatora URI przekierowania. Usługa Azure AD nie może określić, jeśli aplikacja jest aplikacji publicznych klienta lub poufne klienta, chyba że właściwość jest ustawiona. Należy pamiętać, ROPC jest obsługiwana tylko w przypadku aplikacji publicznych klienta. |

## <a name="learn-more"></a>Dowiedz się więcej

* ROPC osobiście wypróbować przy użyciu [Przykładowa aplikacja konsoli](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Aby ustalić, czy należy używać punktu końcowego v2.0, przeczytaj temat [ograniczenia v2.0](active-directory-v2-limitations.md).
