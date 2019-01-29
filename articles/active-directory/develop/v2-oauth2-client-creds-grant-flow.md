---
title: Umożliwia dostęp do zasobów bezpiecznych bez interakcji z użytkownikiem usługi Azure AD w wersji 2.0 | Dokumentacja firmy Microsoft
description: Tworzenie aplikacji sieci web za pomocą usługi Azure AD implementacji protokołu uwierzytelniania OAuth 2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 9b7cfbd7-f89f-4e33-aff2-414edd584b07
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 090f9771bf8d1010e4249d97d5768891f02c54b3
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2019
ms.locfileid: "55096606"
---
# <a name="azure-active-directory-v20-and-the-oauth-20-client-credentials-flow"></a>Azure Active Directory w wersji 2.0 i przepływ poświadczeń klienta OAuth 2.0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Możesz użyć [przyznawanie poświadczeń klienta OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.4) określony w RFC 6749, czasami nazywane *bokami dwóch OAuth*, aby uzyskać dostęp do zasobów hostowanych w sieci web, przy użyciu tożsamości aplikacji. Tego rodzaju grant często służy do interakcji do serwera, które muszą być uruchamiane w tle, bez natychmiastowego interakcji z użytkownikiem. Tego rodzaju aplikacje często są nazywane *demonów* lub *kont usługi*.

Poświadczenia klienta OAuth 2.0 udzielić zezwolenia przepływ usługi sieci web (poufne klienta), aby używanie jej własnych poświadczeń, zamiast personifikacji użytkownika, do uwierzytelniania podczas wywoływania innej usługi sieci web. W tym scenariuszu klient jest zazwyczaj usługą sieci web warstwy środkowej, usługą demona lub witryny sieci web. Na wyższy poziom gwarancji usługi Azure Active Directory (Azure AD) umożliwia wywoływania usługi do korzystania z certyfikatu (zamiast wspólny klucz tajny) jako poświadczenie.

> [!NOTE]
> Punkt końcowy v2.0 nie obsługuje wszystkich scenariuszy usługi Azure AD i funkcji. Aby ustalić, czy należy używać punktu końcowego v2.0, przeczytaj temat [ograniczenia v2.0](active-directory-v2-limitations.md).

W przypadku bardziej typowych *OAuth 3 z bokami*, aplikacja kliencka udzielono uprawnień do uzyskania dostępu do zasobu w imieniu określonego użytkownika. Uprawnienie jest delegowane przez użytkownika do aplikacji, zwykle w ciągu [zgody](v2-permissions-and-consent.md) procesu. Jednak w przepływ poświadczeń klienta, uprawnienia są przyznawane bezpośrednio w samej aplikacji. Gdy przedstawia aplikacja wymusza tokenu do zasobu, zasób, które aplikacja ma autoryzacji do wykonania akcji, a nie użytkownik ma autoryzację.

## <a name="protocol-diagram"></a>Diagram protokołu

Przepływ poświadczeń klienta w całej przypomina poniższy diagram. Opisano poszczególne kroki w dalszej części tego artykułu.

![Przepływ poświadczeń klienta](./media/v2-oauth2-client-creds-grant-flow/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>Pobierz bezpośrednich autoryzacji

Aplikacja odbiera zwykle bezpośredni autoryzacji dostępu do zasobu w jeden z dwóch sposobów: 

* Za pomocą listy kontroli dostępu (ACL) do danego zasobu
* Za pomocą przypisanie uprawnień aplikacji w usłudze Azure AD

Te dwie metody są najbardziej rozpowszechnione w usłudze Azure AD, a firma Microsoft zaleca ich klientów i zasoby, które wykonują klienta przepływ poświadczeń. Zasób można zezwolić klientom w inny sposób, jednak. Każdy serwer zasobów można wybrać metody, która jest najbardziej odpowiednie dla swojej aplikacji.

### <a name="access-control-lists"></a>Listy kontroli dostępu

Dostawca zasobów może wymuszać sprawdzania autoryzacji na podstawie listy (klient) identyfikatorów, które zna i przyznaje określony poziom dostępu do aplikacji. W przypadku zasobów odbiera token z punktu końcowego v2.0, można dekodować tokenu i wyodrębnić identyfikator aplikacji klienckiej z `appid` i `iss` oświadczeń. Następnie porównuje ochronę aplikacji przed listy kontroli dostępu (ACL), ta funkcja obsługuje. Poziom szczegółowości listy ACL i metody mogą znacząco różnią się między zasobami.

Typowy przypadek użycia jest uruchomić testy dla aplikacji sieci web lub interfejsu API sieci web za pomocą listy ACL. Internetowy interfejs API może udzielić tylko podzbioru pełnych uprawnień do określonego klienta. Aby uruchomić testy end-to-end w interfejsie API, należy utworzyć klienta testowego, który uzyskuje tokenów z punktem końcowym v2.0, a następnie wysyła je do interfejsu API. Interfejs API następnie sprawdza listy ACL dla Identyfikatora aplikacji klienta testowego w celu uzyskania pełnego dostępu do całej funkcji interfejsu API. Jeśli używasz tego rodzaju listy ACL, pamiętaj sprawdzić poprawność nie tylko wywołującego `appid` wartość, ale również sprawdzić, czy `iss` wartość tokenu jest zaufany.

Ten typ autoryzacji jest typowe dla demonów i kont usług, które wymagają dostępu do danych należących do użytkowników odbiorców, którzy mają osobistych kont Microsoft. W przypadku danych należących do organizacji firma Microsoft zaleca, możesz uzyskać wymagane zgody za pośrednictwem uprawnień aplikacji.

### <a name="application-permissions"></a>Uprawnienia aplikacji

Zamiast przy użyciu list kontroli dostępu, można użyć interfejsów API do udostępnienia zestaw uprawnień aplikacji. Uprawnienie aplikacji jest udzielany do aplikacji przez administratora organizacji i może służyć tylko dostęp do danych należących do organizacji i jej pracowników. Na przykład program Microsoft Graph udostępnia kilka uprawnienia aplikacji, wykonaj następujące czynności:

* Odczytuj pocztę we wszystkich skrzynkach pocztowych
* Odczytuj i zapisuj wiadomości e-mail we wszystkich skrzynkach pocztowych
* Wysyłaj wiadomości e-mail jako dowolny użytkownik
* Odczytaj dane katalogu

Aby uzyskać więcej informacji dotyczących uprawnień aplikacji, przejdź do [programu Microsoft Graph](https://developer.microsoft.com/graph).

Aby użyć uprawnień aplikacji w swojej aplikacji, wykonaj kroki omówione w kolejnych sekcjach.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Żądanie uprawnień w portalu rejestracji aplikacji

1. Rejestrowanie i tworzenie aplikacji za pośrednictwem [portalu rejestracji aplikacji](quickstart-v2-register-an-app.md) lub do nowej [środowisko rejestracji aplikacji (wersja zapoznawcza)](quickstart-register-app.md).
1. Przejdź do aplikacji w portalu, którego użyto do zarejestrowania lub utworzyć aplikację. Należy użyć co najmniej jeden klucz tajny aplikacji, podczas tworzenia aplikacji.
2. Znajdź **Microsoft Graph uprawnienia** sekcji, a następnie dodaj **uprawnienia aplikacji** wymaganych przez aplikację.
3. **Zapisz** rejestracji aplikacji.

#### <a name="recommended-sign-the-user-in-to-your-app"></a>Zalecane: Zalogować użytkownika do aplikacji

Zazwyczaj podczas kompilowania aplikacji korzystającej z uprawnienia aplikacji aplikacja wymaga strony lub widok, w którym administrator zatwierdzi uprawnienia aplikacji. Na tej stronie mogą być częścią aplikacji przepływ logowania, część ustawień aplikacji lub może być dedykowany przepływ "Połącz". W wielu przypadkach dobrym pomysłem dla aplikacji wyświetlić to "łączenie" Wyświetl tylko wtedy, gdy użytkownik zalogował się przy użyciu służbowego konta Microsoft.

Można zalogować użytkownika do aplikacji, po określeniu organizacji, do której należy użytkownik przed skontaktowaniem się użytkownikowi zatwierdzanie uprawnień aplikacji. Chociaż nie niezbędne, pomoże Ci utworzyć bardziej intuicyjne środowisko dla użytkowników. Aby utworzyć użytkownika, postępuj zgodnie z naszym [samouczki protocol w wersji 2.0](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Żądanie uprawnień od administratora katalogu

Gdy wszystko będzie gotowe zażądać uprawnień od administratora w organizacji, można przekierować użytkownika do v2.0 *punktu końcowego zgody administratora*.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the following request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parametr | Warunek | Opis |
| --- | --- | --- |
| `tenant` | Wymagany | Dzierżawy katalogu, który chcesz zażądać uprawnień z. Może to być w formacie przyjaznej nazwy lub identyfikatora GUID. Jeśli nie wiesz, którego użytkownik należy do dzierżawy i chcesz umożliwić im Zaloguj się przy użyciu dowolnej dzierżawy, należy użyć `common`. |
| `client_id` | Wymagany | Identyfikator aplikacji (klient), która jest przypisana do aplikacji. Te informacje można znaleźć w portalu, gdzie aplikacja została zarejestrowana. |
| `redirect_uri` | Wymagany | Identyfikator URI, którego odpowiedź do wysłania dla aplikacji do obsługi przekierowania. Jego musi dokładnie odpowiadać jeden przekierowania identyfikatory URI, który został zarejestrowany w portalu, z tą różnicą, że musi być zakodowane w adresie URL i może mieć segmenty ścieżki dodatkowe. |
| `state` | Zalecane | Wartość, która znajduje się w żądaniu, która jest także zwracany w odpowiedzi tokenu. Może być ciągiem żadnej zawartości, która ma. Stan jest używany do kodowania informacje o stanie użytkownika w aplikacji, zanim żądanie uwierzytelniania wystąpił, takich jak strony lub widoku, które znajdowały się w. |

W tym momencie usługa Azure AD wymusza, czy administrator dzierżawy może zalogować się do ukończenia żądania. Administrator jest proszony o zatwierdzania wszystkie uprawnienia bezpośrednich zastosowań, które żądanej aplikacji w portalu rejestracji aplikacji.

##### <a name="successful-response"></a>Odpowiedź oznaczająca Powodzenie

Jeśli administrator zatwierdza uprawnienia dla aplikacji, odpowiedź oznaczająca Powodzenie wygląda następująco:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametr | Opis |
| --- | --- |
| `tenant` | Dzierżawy katalogu, który uprawnień aplikacji, które wymagane, w formacie identyfikatora GUID. |
| `state` | Wartość, która znajduje się w żądaniu, który jest zwracany w odpowiedzi tokenu. Może być ciągiem żadnej zawartości, która ma. Stan jest używany do kodowania informacje o stanie użytkownika w aplikacji, zanim żądanie uwierzytelniania wystąpił, takich jak strony lub widoku, które znajdowały się w. |
| `admin_consent` | Ustaw **true**. |

##### <a name="error-response"></a>Odpowiedzi na błąd

Jeśli administrator nie zatwierdza uprawnienia dla aplikacji, odpowiedzi o niepowodzeniu wygląda następująco:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametr | Opis |
| --- | --- |
| `error` | Ciągu kodu błędu, który służy do klasyfikowania typy błędów, oraz tych, które można użyć, aby reagować na błędy. |
| `error_description` | Komunikat błędu, który pomoże Ci odkryć ich główną przyczynę błędu. |

Po otrzymaniu pomyślnej odpowiedzi z punktu końcowego inicjowania obsługi administracyjnej aplikacji, aplikacja zdobyła bezpośrednich uprawnienia dostępu do aplikacji, które go wymagane. Teraz możesz zażądać tokenu do zasobu, który chcesz.

## <a name="get-a-token"></a>Pobierz token

Po nabyciu autoryzację na odpowiednim dla twojej aplikacji, należy kontynuować uzyskiwanie tokenów dostępu do interfejsów API. Do pobrania tokenu przy użyciu klienta poświadczeń, Wyślij żądanie POST `/token` punktu końcowego v2.0:

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Pierwszy przypadek: Żądanie tokenu dostępu za pomocą wspólny klucz tajny

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Parametr | Warunek | Opis |
| --- | --- | --- |
| `tenant` | Wymagany | Dzierżawy katalogu aplikacji firma planuje działać, w formacie nazwy domeny lub identyfikator GUID. |
| `client_id` | Wymagany | Identyfikator aplikacji, która jest przypisana do aplikacji. Te informacje można znaleźć w portalu, gdzie aplikacja została zarejestrowana. |
| `scope` | Wymagany |Przekazana wartość `scope` parametru, w tym żądaniu powinien być identyfikator zasobów (URI Identyfikatora aplikacji) zasobu, umieszczone za pomocą `.default` sufiks. Na przykład program Microsoft Graph, wartość jest `https://graph.microsoft.com/.default`. Ta wartość informuje punktu końcowego v2.0, czy wszystkich bezpośrednich zastosowań uprawnień skonfigurowanych dla aplikacji, go należy wystawić tokenu dla tych skojarzone z zasobem, którego chcesz użyć. |
| `client_secret` | Wymagany | Klucz tajny aplikacji, który został wygenerowany dla aplikacji w portalu rejestracji aplikacji. Klucz tajny klienta musi być zakodowane w adresie URL przed wysłaniem. |
| `grant_type` | Wymagany | Musi być `client_credentials`. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Drugi przypadek: Żądanie tokenu dostępu przy użyciu certyfikatu

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| Parametr | Warunek | Opis |
| --- | --- | --- |
| `tenant` | Wymagany | Dzierżawy katalogu aplikacji firma planuje działać, w formacie nazwy domeny lub identyfikator GUID. |
| `client_id` | Wymagany |Identyfikator aplikacji, która jest przypisana do aplikacji. |
| `scope` | Wymagany | Przekazana wartość `scope` parametru, w tym żądaniu powinien być identyfikator zasobu (identyfikator URI aplikacji) zasobu, umieszczone za pomocą `.default` sufiks. Na przykład program Microsoft Graph, wartość jest `https://graph.microsoft.com/.default`. <br>Ta wartość informuje punktu końcowego v2.0, czy wszystkich bezpośrednich zastosowań uprawnień skonfigurowanych dla aplikacji, go należy wystawić tokenu dla tych skojarzone z zasobem, którego chcesz użyć. |
| `client_assertion_type` | Wymagany | Wartość musi być `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| `client_assertion` | Wymagany | Potwierdzenie (JSON Web Token), musisz utworzyć i podpisać za pomocą certyfikatu rejestracji w charakterze poświadczenia dla aplikacji. Przeczytaj o [certyfikatu poświadczeń](active-directory-certificate-credentials.md) informacje na temat rejestracji certyfikatu i format potwierdzenia.|
| `grant_type` | Wymagany | Musi być równa `client_credentials`. |

Należy zauważyć, że parametry są prawie takie same jak w przypadku żądania przez Wspólny klucz tajny, z tą różnicą, że parametr client_secret zostaje zastąpiona przez dwa parametry: client_assertion_type i client_assertion.

### <a name="successful-response"></a>Odpowiedź oznaczająca Powodzenie

Odpowiedź oznaczająca powodzenie wygląda następująco:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parametr | Opis |
| --- | --- |
| `access_token` | Token żądanego dostępu. Aplikacja może używać tego tokenu do uwierzytelniania do zabezpieczonych zasobów, takich jak interfejs API sieci Web. |
| `token_type` | Wskazuje typ tokenu. Jedynym typem, który obsługuje usługi Azure AD jest `bearer`. |
| `expires_in` | Ilość czasu, który token dostępu jest prawidłowy (w sekundach). |

### <a name="error-response"></a>Odpowiedzi na błąd

Odpowiedź na błąd wygląda następująco:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametr | Opis |
| --- | --- |
| `error` | Ciągu kodu błędu, który służy do klasyfikowania typy błędów, które występują i reagować na błędy. |
| `error_description` | Komunikat błędu, które mogą pomóc odkryć ich główną przyczynę błędu uwierzytelniania. |
| `error_codes` | Lista kodów błędów specyficznych dla usługi STS, które mogą pomóc przy użyciu diagnostyki. |
| `timestamp` | Czas, kiedy wystąpił błąd. |
| `trace_id` | Unikatowy identyfikator żądanie pomocy przy użyciu diagnostyki. |
| `correlation_id` | Unikatowy identyfikator żądanie pomocy przy użyciu diagnostyki składnikami. |

## <a name="use-a-token"></a>Użyj tokenu

Skoro nabyciu tokenu, użyj tokenu, aby wysyłać żądania do zasobu. Po wygaśnięciu ważności tokenu, powtórz żądanie `/token` punktu końcowego w celu uzyskania tokenu dostępu od nowa.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro tip: Try the following command! (Replace the token with your own.)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-sample"></a>Przykład kodu

Aby wyświetlić przykładową aplikacją, że implementuje przyznawanie poświadczeń klienta przy użyciu administratora zgody punktu końcowego, zobacz nasze [przykładowy kod demona v2.0](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
