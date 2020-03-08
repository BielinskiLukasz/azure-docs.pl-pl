---
title: Przepływ kodu autoryzacji — Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się, jak tworzyć aplikacje sieci Web za pomocą protokołu uwierzytelniania Azure AD B2C i OpenID Connect Connect.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 8248ca0abb1d633786b09b894bcd6b1089ab2d8c
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671802"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>Przepływ kodu autoryzacji OAuth 2,0 w Azure Active Directory B2C

Aby uzyskać dostęp do chronionych zasobów, takich jak interfejsy API sieci Web, można użyć przyznawania kodu autoryzacji OAuth 2,0 w aplikacjach zainstalowanych na urządzeniu. Korzystając z implementacji Azure Active Directory B2C (Azure AD B2C) protokołu OAuth 2,0, możesz dodać do aplikacji mobilnych i klasycznych inne zadania zarządzania tożsamościami, logowania i inne. Ten artykuł jest niezależny od języka. W tym artykule opisano sposób wysyłania i odbierania komunikatów HTTP bez używania bibliotek Open Source.

Przepływ kodu autoryzacji OAuth 2,0 został opisany w [sekcji 4,1 specyfikacji oauth 2,0](https://tools.ietf.org/html/rfc6749). Można jej używać do uwierzytelniania i autoryzacji w przypadku większości [typów aplikacji](application-types.md), w tym aplikacji sieci Web i natywnie zainstalowanych aplikacji. Przepływ kodu autoryzacji OAuth 2,0 umożliwia bezpieczne pozyskiwanie tokenów dostępu i odświeżanie tokenów aplikacji, które mogą być używane w celu uzyskania dostępu do zasobów zabezpieczonych przez [serwer autoryzacji](protocols-overview.md).  Token odświeżania pozwala klientowi uzyskać nowe tokeny dostępu (i odświeżania) po wygaśnięciu tokenu dostępu, zwykle po godzinie.

Ten artykuł koncentruje się na przepływie kodu autoryzacji protokołu OAuth 2,0 dla **klientów publicznych** . Klient publiczny to dowolna aplikacja kliencka, której nie można zaufać, aby bezpiecznie zachować integralność hasła tajnego. Obejmuje to aplikacje mobilne, aplikacje klasyczne oraz wszystkie aplikacje działające na urządzeniu i muszą uzyskiwać tokeny dostępu.

> [!NOTE]
> Aby dodać Zarządzanie tożsamościami do aplikacji sieci Web przy użyciu Azure AD B2C, użyj funkcji [OpenID Connect Connect](openid-connect.md) zamiast uwierzytelniania OAuth 2,0.

Azure AD B2C rozszerza standardowe przepływy protokołu OAuth 2,0, aby wykonywać więcej niż proste uwierzytelnianie i autoryzację. Wprowadza [przepływ użytkownika](user-flow-overview.md). Za pomocą przepływów użytkowników można używać protokołu OAuth 2,0 do dodawania środowisk użytkownika do aplikacji, takich jak rejestrowanie, logowanie i Zarządzanie profilem. Dostawcy tożsamości, którzy korzystają z protokołu OAuth 2,0, obejmują [Amazon](identity-provider-amazon.md), [Azure Active Directory](identity-provider-azure-ad-single-tenant.md), [Facebook](identity-provider-facebook.md), [GitHub](identity-provider-github.md), [Google](identity-provider-google.md)i [LinkedIn](identity-provider-linkedin.md).

Aby wypróbować żądania HTTP w tym artykule:

1. Zastąp `{tenant}` nazwą dzierżawy Azure AD B2C.
1. Zastąp `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` IDENTYFIKATORem aplikacji aplikacji, która została wcześniej zarejestrowana w dzierżawie Azure AD B2C.
1. Zastąp `{policy}` nazwą zasad utworzoną w dzierżawie, na przykład `b2c_1_sign_in`.

## <a name="1-get-an-authorization-code"></a>1. Pobierz kod autoryzacji
Przepływ kodu autoryzacji zaczyna się od klienta kierującego użytkownika do punktu końcowego `/authorize`. Jest to interaktywna część przepływu, w której użytkownik wykonuje akcję. W tym żądaniu klient wskazuje w `scope` parametru uprawnienia wymagane do uzyskania od użytkownika. Poniższe trzy przykłady (z podziałami wierszy na potrzeby czytelności) używają innego przepływu użytkownika.


```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
```


| Parametr | Wymagana? | Opis |
| --- | --- | --- |
|dzierżaw| Wymagany | Nazwa dzierżawy Azure AD B2C|
| zasad | Wymagany | Przepływ użytkownika do uruchomienia. Określ nazwę przepływu użytkownika utworzonego w dzierżawie Azure AD B2C. Na przykład: `b2c_1_sign_in`, `b2c_1_sign_up`lub `b2c_1_edit_profile`. |
| client_id |Wymagany |Identyfikator aplikacji przypisany do aplikacji w [Azure Portal](https://portal.azure.com). |
| response_type |Wymagany |Typ odpowiedzi, który musi zawierać `code` dla przepływu kodu autoryzacji. |
| redirect_uri |Wymagany |Identyfikator URI przekierowania aplikacji, w którym odpowiedzi uwierzytelniania są wysyłane i odbierane przez aplikację. Musi dokładnie pasować do jednego z identyfikatorów URI przekierowania zarejestrowanych w portalu, z tą różnicą, że musi być zakodowany w adresie URL. |
| scope |Wymagany |Rozdzielana spacjami lista zakresów. Wartość jednego zakresu wskazuje na Azure Active Directory (Azure AD) wymagane uprawnienia. Użycie identyfikatora klienta jako zakresu wskazuje, że aplikacja wymaga tokenu dostępu, który może być używany w odniesieniu do własnej usługi lub internetowego interfejsu API, reprezentowanego przez ten sam identyfikator klienta.  Zakres `offline_access` wskazuje, że aplikacja wymaga tokenu odświeżania na potrzeby długotrwałego dostępu do zasobów. Można również użyć zakresu `openid`, aby zażądać tokenu identyfikatora z Azure AD B2C. |
| response_mode |Zalecane |Metoda używana do wysyłania podanego kodu autoryzacji z powrotem do aplikacji. Może być `query`, `form_post`lub `fragment`. |
| state |Zalecane |Wartość zawarta w żądaniu, która może być ciągiem dowolnej zawartości, która ma być używana. Zwykle jest używana losowo wygenerowana unikatowa wartość, aby zapobiec atakom na fałszowanie żądań między lokacjami. Ten stan jest również używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia. Na przykład strona, na której znajduje się użytkownik, lub przepływ użytkownika, który był wykonywany. |
| pytać |Optional (Opcjonalność) |Typ interakcji z użytkownikiem, który jest wymagany. Obecnie jedyną prawidłową wartością jest `login`, co wymusza, aby użytkownik wprowadził swoje poświadczenia dla tego żądania. Logowanie jednokrotne nie zacznie obowiązywać. |

W tym momencie użytkownik jest proszony o ukończenie przepływu pracy przepływu użytkownika. Może to oznaczać, że użytkownik wprowadza nazwę użytkownika i hasło, logując się przy użyciu tożsamości społecznościowej, rejestruje się w katalogu lub dowolną inną liczbę kroków. Akcje użytkownika zależą od sposobu definiowania przepływu użytkownika.

Po zakończeniu przepływu użytkownika usługa Azure AD zwróci odpowiedź do aplikacji na wartość użytą dla `redirect_uri`. Używa metody określonej w parametrze `response_mode`. Odpowiedź jest dokładnie taka sama dla każdego scenariusza akcji użytkownika, niezależnie od przepływu użytkownika, który został wykonany.

Pomyślna odpowiedź wykorzystująca `response_mode=query` wygląda następująco:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parametr | Opis |
| --- | --- |
| code |Kod autoryzacji żądany przez aplikację. Aplikacja może użyć kodu autoryzacji do żądania tokenu dostępu dla zasobu docelowego. Kody autoryzacji są bardzo krótkie. Zazwyczaj wygasają po około 10 minutach. |
| state |Zapoznaj się z pełnym opisem w tabeli w poprzedniej sekcji. Jeśli w żądaniu zostanie uwzględniony parametr `state`, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy wartości `state` w żądaniu i odpowiedzi są identyczne. |

Odpowiedzi na błędy można także wysyłać do identyfikatora URI przekierowania, aby aplikacja mogła je odpowiednio obsłużyć:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, którego można użyć do klasyfikowania typów błędów, które wystąpiły. Można również użyć ciągu do reagowania na błędy. |
| error_description |Konkretny komunikat o błędzie, który może pomóc w zidentyfikowaniu głównej przyczyny błędu uwierzytelniania. |
| state |Zobacz pełny opis w powyższej tabeli. Jeśli w żądaniu zostanie uwzględniony parametr `state`, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy wartości `state` w żądaniu i odpowiedzi są identyczne. |

## <a name="2-get-a-token"></a>2. Uzyskiwanie tokenu
Po uzyskaniu kodu autoryzacji można zrealizować `code` tokenu do zamierzonego zasobu, wysyłając żądanie POST do punktu końcowego `/token`. W Azure AD B2C można [zażądać tokenów dostępu dla innych interfejsów API](access-tokens.md#request-a-token) , jak zwykle, określając ich zakresy w żądaniu.

Możesz również zażądać tokenu dostępu dla własnego internetowego interfejsu API zaplecza w ramach Konwencji przy użyciu identyfikatora klienta aplikacji jako żądanego zakresu (co spowoduje użycie tokenu dostępu z IDENTYFIKATORem klienta jako "odbiorcy"):

```HTTP
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parametr | Wymagana? | Opis |
| --- | --- | --- |
|dzierżaw| Wymagany | Nazwa dzierżawy Azure AD B2C|
|zasad| Wymagany| Przepływ użytkownika, który został użyty do uzyskania kodu autoryzacji. W tym żądaniu nie można użyć innego przepływu użytkownika. |
| client_id |Wymagany |Identyfikator aplikacji przypisany do aplikacji w [Azure Portal](https://portal.azure.com).|
| client_secret | Tak, w Web Apps | Wpis tajny aplikacji, który został wygenerowany w [Azure Portal](https://portal.azure.com/). Wpisy tajne klienta są używane w tym przepływie dla scenariuszy aplikacji sieci Web, w których klient może bezpiecznie przechowywać klucz tajny klienta. W przypadku scenariuszy aplikacji natywnych (klienta publicznego) klucze tajne klienta nie mogą być bezpiecznie przechowywane i w związku z tym nie są używane w tym wywołaniu. Jeśli używasz klucza tajnego klienta, zmień go okresowo. |
| grant_type |Wymagany |Typ dotacji. W przypadku przepływu kodu autoryzacji należy `authorization_code`typ dotacji. |
| scope |Zalecane |Rozdzielana spacjami lista zakresów. Pojedyncza wartość zakresu wskazuje na usługę Azure AD oba wymagane uprawnienia. Użycie identyfikatora klienta jako zakresu wskazuje, że aplikacja wymaga tokenu dostępu, który może być używany w odniesieniu do własnej usługi lub internetowego interfejsu API, reprezentowanego przez ten sam identyfikator klienta.  Zakres `offline_access` wskazuje, że aplikacja wymaga tokenu odświeżania na potrzeby długotrwałego dostępu do zasobów.  Można również użyć zakresu `openid`, aby zażądać tokenu identyfikatora z Azure AD B2C. |
| code |Wymagany |Kod autoryzacji uzyskany w pierwszym etapie przepływu. |
| redirect_uri |Wymagany |Identyfikator URI przekierowania aplikacji, w której został otrzymany kod autoryzacji. |

Pomyślna odpowiedź dotycząca tokenu wygląda następująco:

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametr | Opis |
| --- | --- |
| not_before |Godzina, o której token jest uznawany za ważny, w czasie epoki. |
| token_type |Wartość typu tokenu. Jedynym typem obsługiwanym przez usługę Azure AD jest znak. |
| access_token |Zażądano podpisanego tokenu sieci Web JSON (JWT). |
| scope |Zakresy, dla których token jest ważny. Można również użyć zakresów do buforowania tokenów do późniejszego użycia. |
| expires_in |Czas ważności tokenu (w sekundach). |
| refresh_token |Token odświeżania OAuth 2,0. Aplikacja może użyć tego tokenu, aby uzyskać dodatkowe tokeny po wygaśnięciu bieżącego tokenu. Tokeny odświeżania są długotrwałe. Można ich użyć, aby zachować dostęp do zasobów przez dłuższy czas. Aby uzyskać więcej informacji, zobacz [Informacje o tokenach Azure AD B2C](tokens-overview.md). |

Odpowiedzi na błędy wyglądają następująco:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, którego można użyć do klasyfikowania typów błędów, które wystąpiły. Można również użyć ciągu do reagowania na błędy. |
| error_description |Konkretny komunikat o błędzie, który może pomóc w zidentyfikowaniu głównej przyczyny błędu uwierzytelniania. |

## <a name="3-use-the-token"></a>3. Użyj tokenu
Po pomyślnym pobraniu tokenu dostępu można użyć tokenu w żądaniach do interfejsów API sieci Web zaplecza, dołączając je do nagłówka `Authorization`:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Odśwież token
Tokeny dostępu i tokeny identyfikatorów są krótkotrwałe. Po ich wygaśnięciu należy odświeżyć, aby nadal uzyskiwać dostęp do zasobów. W tym celu Prześlij kolejne żądanie POST do punktu końcowego `/token`. Tym razem Podaj `refresh_token`, a nie `code`:

```HTTP
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametr | Wymagana? | Opis |
| --- | --- | --- |
|dzierżaw| Wymagany | Nazwa dzierżawy Azure AD B2C|
|zasad |Wymagany |Przepływ użytkownika, który został użyty do uzyskania oryginalnego tokenu odświeżania. W tym żądaniu nie można użyć innego przepływu użytkownika. |
| client_id |Wymagany |Identyfikator aplikacji przypisany do aplikacji w [Azure Portal](https://portal.azure.com). |
| client_secret | Tak, w Web Apps | Wpis tajny aplikacji, który został wygenerowany w [Azure Portal](https://portal.azure.com/). Wpisy tajne klienta są używane w tym przepływie dla scenariuszy aplikacji sieci Web, w których klient może bezpiecznie przechowywać klucz tajny klienta. W przypadku scenariuszy aplikacji natywnych (klienta publicznego) klucze tajne klienta nie mogą być bezpiecznie przechowywane i w związku z tym nie są używane w tym wywołaniu. Jeśli używasz klucza tajnego klienta, zmień go okresowo. |
| grant_type |Wymagany |Typ dotacji. Dla tego etapu przepływu kodu autoryzacji należy `refresh_token`typ dotacji. |
| scope |Zalecane |Rozdzielana spacjami lista zakresów. Pojedyncza wartość zakresu wskazuje na usługę Azure AD oba wymagane uprawnienia. Użycie identyfikatora klienta jako zakresu wskazuje, że aplikacja wymaga tokenu dostępu, który może być używany w odniesieniu do własnej usługi lub internetowego interfejsu API, reprezentowanego przez ten sam identyfikator klienta.  Zakres `offline_access` wskazuje, że aplikacja będzie potrzebować tokenu odświeżania na potrzeby długotrwałego dostępu do zasobów.  Można również użyć zakresu `openid`, aby zażądać tokenu identyfikatora z Azure AD B2C. |
| redirect_uri |Optional (Opcjonalność) |Identyfikator URI przekierowania aplikacji, w której został otrzymany kod autoryzacji. |
| refresh_token |Wymagany |Oryginalny token odświeżania uzyskany w drugim etapie przepływu. |

Pomyślna odpowiedź dotycząca tokenu wygląda następująco:

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametr | Opis |
| --- | --- |
| not_before |Godzina, o której token jest uznawany za ważny, w czasie epoki. |
| token_type |Wartość typu tokenu. Jedynym typem obsługiwanym przez usługę Azure AD jest znak. |
| access_token |Zażądano podpisanego tokenu JWT. |
| scope |Zakresy, dla których token jest ważny. Można również użyć zakresów do buforowania tokenów do późniejszego użycia. |
| expires_in |Czas ważności tokenu (w sekundach). |
| refresh_token |Token odświeżania OAuth 2,0. Aplikacja może użyć tego tokenu, aby uzyskać dodatkowe tokeny po wygaśnięciu bieżącego tokenu. Tokeny odświeżania są długotrwałe i mogą być używane do przechowywania zasobów przez dłuższy czas. Aby uzyskać więcej informacji, zobacz [Informacje o tokenach Azure AD B2C](tokens-overview.md). |

Odpowiedzi na błędy wyglądają następująco:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, którego można użyć do klasyfikowania typów błędów, które występują. Można również użyć ciągu do reagowania na błędy. |
| error_description |Konkretny komunikat o błędzie, który może pomóc w zidentyfikowaniu głównej przyczyny błędu uwierzytelniania. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Korzystanie z własnego katalogu Azure AD B2C
Aby wypróbować te żądania samodzielnie, wykonaj następujące czynności. Zastąp przykładowe wartości używane w tym artykule własnymi wartościami.

1. [Utwórz katalog Azure AD B2C](tutorial-create-tenant.md). Użyj nazwy katalogu w żądaniach.
2. [Utwórz aplikację](tutorial-register-applications.md) w celu uzyskania identyfikatora aplikacji i identyfikatora URI przekierowania. Dołącz klienta natywnego do aplikacji.
3. [Tworzenie przepływów użytkowników](user-flow-overview.md) w celu uzyskania nazw przepływów użytkownika.
