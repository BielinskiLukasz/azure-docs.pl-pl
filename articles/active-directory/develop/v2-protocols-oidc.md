---
title: Azure Active Directory w wersji 2.0 i protokołu OpenID Connect | Dokumentacja firmy Microsoft
description: Tworzenie aplikacji sieci web za pomocą usługi Azure AD v2.0 implementacji protokołu uwierzytelniania OpenID Connect.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a4875997-3aac-4e4c-b7fe-2b4b829151ce
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c20ae6acaf600cdde6e168c6db96deb7a28e9fa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58112708"
---
# <a name="azure-active-directory-v20-and-the-openid-connect-protocol"></a>Azure Active Directory w wersji 2.0 i protokołu OpenID Connect

OpenID Connect to protokół uwierzytelniania, oparte na protokołu OAuth 2.0, który służy do bezpiecznego logowania użytkownika do aplikacji sieci web. Korzystając z punktu końcowego v2.0 wdrażania protokołu OpenID Connect, możesz dodać do aplikacji sieci web logowania i dostępu do interfejsu API. W tym artykule pokazano, jak to zrobić to niezależne od języka i opisano, jak wysyłać i odbierać komunikaty HTTP bez użycia żadnych bibliotek typu open source firmy Microsoft.

> [!NOTE]
> Nie obsługuje punktu końcowego v2.0, wszystkie scenariusze usługi Azure Active Directory (Azure AD) i funkcje. Aby ustalić, czy należy używać punktu końcowego v2.0, przeczytaj temat [ograniczenia v2.0](active-directory-v2-limitations.md).

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) rozszerza OAuth 2.0 *autoryzacji* protokół do użycia jako *uwierzytelniania* protokołu, dzięki czemu możesz zrobić pojedynczego logowania jednokrotnego przy użyciu protokołu OAuth. OpenID Connect wprowadzono pojęcie *tokenu Identyfikacyjnego*, który jest token zabezpieczający, który umożliwia klientowi do zweryfikowania tożsamości danego użytkownika. Identyfikator tokenu zapewnia również podstawowych informacji o profilu użytkownika. Ponieważ OpenID Connect rozszerza OAuth 2.0, aplikacje mogą bezpiecznie uzyskiwać *tokeny dostępu*, której można uzyskać dostęp do zasobów, które są zabezpieczone przez [serwera autoryzacji](active-directory-v2-protocols.md#the-basics). Punkt końcowy v2.0 umożliwia także aplikacji innych firm, które są zarejestrowane w usłudze Azure AD, aby wystawiać tokeny dostępu do zabezpieczonych zasobów, takich jak interfejsy API sieci Web. Aby uzyskać więcej informacji o tym, jak skonfigurować aplikację do wystawiania tokenów dostępu, zobacz [jak zarejestrować aplikację za pośrednictwem punktu końcowego v2.0](quickstart-v2-register-an-app.md). Zaleca się, że używasz uwierzytelniania OpenID Connect, jeśli tworzysz [aplikacji sieci web](v2-app-types.md#web-apps) który jest hostowany na serwerze i dostępne za pośrednictwem przeglądarki.

## <a name="protocol-diagram-sign-in"></a>Diagram protokołu: Zaloguj się

Najbardziej podstawowa przepływ logowania ma kroki opisane w następnej diagramu. Każdy krok jest szczegółowo opisane w tym artykule.

![Protokół OpenID Connect: Zaloguj się](./media/v2-protocols-oidc/convergence_scenarios_webapp.png)

## <a name="fetch-the-openid-connect-metadata-document"></a>Pobierz dokument metadanych OpenID Connect

OpenID Connect zawiera opis dokumentu metadanych, który zawiera większość informacji wymaganych dla aplikacji do wykonywania logowania. Obejmuje to informacje, takie jak adresy URL do użycia i lokalizację usługi publiczne klucze podpisywania. W przypadku punktu końcowego v2.0 jest OpenID Connect dokument metadanych, który należy użyć:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP]
> Wypróbuj! Kliknij przycisk [ https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration ](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) się `common` konfiguracji dzierżawy.

`{tenant}` Może mieć jedną z czterech wartości:

| Wartość | Opis |
| --- | --- |
| `common` |Za pomocą osobistego konta Microsoft i konto służbowe lub szkolne, z usługi Azure Active Directory (Azure AD) można logowania do aplikacji. |
| `organizations` |Tylko użytkownicy z pracy lub kont służbowych z usługi Azure AD można zalogować się do aplikacji. |
| `consumers` |Tylko użytkownicy z osobistego konta Microsoft zalogować się do aplikacji. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` lub `contoso.onmicrosoft.com` |Tylko użytkownicy przy użyciu konta służbowego z określonej usługi Azure AD dzierżawy można zalogować się do aplikacji. Przyjazna nazwa domeny dzierżawy usługi Azure AD albo identyfikator GUID dzierżawy może służyć. |

Metadane są proste dokumentu JavaScript Object Notation (JSON). Zobacz poniższy fragment kodu, na przykład. Zawartość w tym fragmencie kodu są szczegółowo opisane [specyfikacją z OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/common\/discovery\/v2.0\/keys",

  ...

}
```

Zazwyczaj ten dokument metadanych umożliwiają konfigurowanie biblioteki uwierzytelniania OpenID Connect lub zestawu SDK; biblioteki użyje metadane, aby wykonać swoją pracę. Jednak jeśli nie używasz bibliotekę prekompilacji OpenID Connect, możesz wykonać kroki opisane w dalszej części tego artykułu, aby wykonać logowania w aplikacji sieci web przy użyciu punktu końcowego v2.0.

## <a name="send-the-sign-in-request"></a>Wyślij żądanie logowania

Gdy Twoja aplikacja sieci web musi uwierzytelnić użytkownika, można je skierować użytkownika `/authorize` punktu końcowego. To żądanie jest podobna do pierwszej gałęzi [przepływ kodu autoryzacji OAuth 2.0](v2-oauth2-auth-code-flow.md), ze te ważne różnice:

* Żądanie musi zawierać `openid` zakresu z `scope` parametru.
* `response_type` Parametr musi zawierać `id_token`.
* Żądanie musi zawierać `nonce` parametru.

> [!IMPORTANT]
> Aby można było pomyślnie żądania identyfikator tokenu rejestracji aplikacji w [portalu rejestracji](https://apps.dev.microsoft.com) musi mieć **[przyznawanie niejawne](v2-oauth2-implicit-grant-flow.md)** włączone dla klienta sieci Web. Jeśli nie jest włączona, `unsupported_response` zostanie zwrócony błąd: "Wartość podana dla parametru wejściowego"response_type"jest niedozwolone dla tego klienta. Oczekiwana wartość to "code" "

Na przykład:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [!TIP]
> Kliknij poniższy link, aby wykonać tego żądania. Po zalogowaniu się w przeglądarce zostanie przekierowany do `https://localhost/myapp/`, przy użyciu tokenu Identyfikatora, w pasku adresu. Należy zauważyć, że używa tego żądania `response_mode=fragment` (tylko w celach demonstracyjnych). Firma Microsoft zaleca użycie `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parametr | Warunek | Opis |
| --- | --- | --- |
| dzierżawa |Wymagane |Możesz użyć `{tenant}` wartość w polu Ścieżka żądania w celu kontrolowania, kto zalogować się do aplikacji. Dozwolone wartości to `common`, `organizations`, `consumers`i identyfikatorów dzierżawy. Aby uzyskać więcej informacji, zobacz [protokołu podstawy](active-directory-v2-protocols.md#endpoints). |
| client_id |Wymagane |Identyfikator aplikacji [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) przypisany do aplikacji. |
| response_type |Wymagane |Musi zawierać `id_token` dla logowania OpenID Connect. Może to również obejmować inne `response_type` wartości, takich jak `code`. |
| redirect_uri |Zalecane |Przekierowania URI aplikacji, gdzie odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Jego musi dokładnie odpowiadać jeden zarejestrowany w portalu, identyfikatory URI przekierowania z tym wyjątkiem, że musi być zakodowane w adresie URL. |
| scope |Wymagane |Rozdzielonej spacjami listy zakresów. Dla protokołu OpenID Connect, musi on zawierać zakres `openid`, co przekłada się na uprawnienia "Logowanie się w" w zgody interfejsu użytkownika. W tym żądaniu żądanie zgody, mogą również obejmować inne zakresy. |
| Identyfikator jednorazowy |Wymagane |Wartości zawarte w żądaniu wygenerowane przez aplikację, która zostanie uwzględniona w wynikowej wartości id_token jako oświadczenia. Aplikację można sprawdzić tę wartość, aby uniknąć powtarzania tokenu ataków. Wartość jest zazwyczaj losowego, unikatowy ciąg, który może służyć do identyfikowania pochodzenia żądania. |
| response_mode |Zalecane |Określa metodę, które mają być używane do odesłania wynikowy kod autoryzacji do aplikacji. Możliwe wartości to `form_post` i `fragment`. Dla aplikacji sieci web, zaleca się używanie `response_mode=form_post`, aby zapewnić najbardziej bezpieczny transfer tokenów do aplikacji. |
| state |Zalecane |Wartość uwzględnione w żądaniu, które również zostaną zwrócone w odpowiedzi tokenu. Może być ciągiem żadnej zawartości, którą chcesz. Losowo generowany unikatową wartość jest zazwyczaj używany do [fałszerstwo żądania międzywitrynowego atakami](https://tools.ietf.org/html/rfc6749#section-10.12). Stan jest również używane do kodowania informacje o stanie użytkownika w aplikacji, zanim żądanie uwierzytelniania wystąpił, takich jak strony lub widok, który użytkownik był na. |
| wiersz |Optional (Opcjonalność) |Wskazuje typ interakcji z użytkownikiem, który jest wymagany. Jedyne prawidłowe wartości w tym momencie są `login`, `none`, i `consent`. `prompt=login` Oświadczenia wymusza użytkownika o wprowadzenie poświadczeń na żądanie i neguje logowania jednokrotnego. `prompt=none` Oświadczeń jest przeciwieństwem. To oświadczenie gwarantuje, że użytkownik nie zobaczy wszystkie interaktywne monity w inny sposób. Jeśli żądanie nie można ukończyć w trybie dyskretnym za pomocą logowania jednokrotnego, punktu końcowego v2.0 zwraca błąd. `prompt=consent` Oświadczenia wyzwala okno dialogowe ze zgodą OAuth po użytkownik loguje się. Okno dialogowe z monitem o przyznanie uprawnień do aplikacji. |
| login_hint |Optional (Opcjonalność) |Ten parametr umożliwia wstępnie wypełnić pole nazwy użytkownika i adres e-mail adres strony logowania dla użytkownika, jeśli znasz nazwę użytkownika, wcześniej. Często aplikacje tego parametru należy użyć podczas ponownego uwierzytelniania po już wyodrębniania nazwy użytkownika z wcześniejszych logowania za pomocą `preferred_username` oświadczenia. |
| Element domain_hint |Optional (Opcjonalność) |Ta wartość może być `consumers` lub `organizations`. Jeśli uwzględniony, pomija proces odnajdywania bazujące na poczcie e-mail, który użytkownik przechodzi w witrynie v2.0 logowania nieco bardziej usprawnione środowisko użytkownika. Często aplikacje tego parametru należy użyć podczas ponownego uwierzytelniania, wyodrębniając `tid` oświadczeń z tokenu Identyfikatora. Jeśli `tid` oświadczenia, wartość jest `9188040d-6c67-4c5b-b112-36a304b66dad` (Account Microsoft dzierżawcy konsumenta), użyj `domain_hint=consumers`. W przeciwnym razie użyj `domain_hint=organizations`. |

W tym momencie użytkownik jest monitowany, aby wprowadzić swoje poświadczenia i wykonania uwierzytelnienia. Punktu końcowego v2.0 sprawdza, czy użytkownik wyraził zgodę na uprawnienia czcionką `scope` parametr zapytania. Jeśli użytkownik nie wyraził zgody do dowolnego z tych uprawnień, punktu końcowego v2.0 monituje użytkownika o zgodę na wymagane uprawnienia. Przeczytaj więcej o [uprawnienia, wyrażania zgody i aplikacje wielodostępne](v2-permissions-and-consent.md).

Po użytkownik jest uwierzytelniany i przyznaje zgody, punktu końcowego v2.0 zwraca odpowiedź do aplikacji, na wskazany identyfikator URI przekierowania za pomocą metody podanej w `response_mode` parametru.

### <a name="successful-response"></a>Pomyślna odpowiedź

Odpowiedź oznaczająca Powodzenie, gdy używasz `response_mode=form_post` wygląda podobnie do następującego:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametr | Opis |
| --- | --- |
| id_token |Identyfikator tokenu, który zażądał aplikacji. Możesz użyć `id_token` parametru, aby zweryfikować tożsamość użytkownika i rozpocząć sesję z użytkownikiem. Aby uzyskać więcej informacji o identyfikatorze tokeny i ich zawartość, zobacz [ `id_tokens` odwołania](id-tokens.md). |
| state |Jeśli `state` parametru jest uwzględnione w żądaniu, tę samą wartość powinna zostać wyświetlona w odpowiedzi. Aplikację należy sprawdzić, czy wartości stanu żądania i odpowiedzi są identyczne. |

### <a name="error-response"></a>Odpowiedzi na błąd

Odpowiedzi na błędy mogą zostać wysłane do identyfikatora URI przekierowania, dzięki czemu aplikacja będzie mógł je obsłużyć. Odpowiedź na błąd wygląda następująco:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| error |Ciągu kodu błędu, który służy do klasyfikowania typy błędów, które występują i reagować na błędy. |
| error_description |Komunikat błędu, który pomoże Ci identyfikować przyczyny błędu uwierzytelniania. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Kody błędów dla błędów punktu końcowego autoryzacji

W poniższej tabeli opisano kody błędów, które mogą być zwracane w `error` parametru odpowiedzi na błąd:

| Kod błędu | Opis | Akcja klienta |
| --- | --- | --- |
| invalid_request |Błąd protokołu, np. Brak wymaganego parametru. |Usuń i ponownie prześlij żądanie. Jest to błąd programowania, który zazwyczaj jest przechwytywane w początkowej fazie testowania. |
| unauthorized_client |Aplikacja kliencka nie mogą zażądać kod autoryzacji. |Dzieje się tak zazwyczaj, gdy aplikacja kliencka nie jest zarejestrowany w usłudze Azure AD lub nie została dodana do dzierżawy usługi Azure AD przez użytkownika. Aplikacja może monitować użytkownika z instrukcjami, aby zainstalować aplikację i dodać go do usługi Azure AD. |
| access_denied |Właściciel zasobu odmowa zgody. |Aplikacja kliencka może powiadomić użytkownika, którego nie można kontynuować, chyba że użytkownik wyrazi na to zgody. |
| unsupported_response_type |Serwer autoryzacji nie obsługuje typu odpowiedzi w żądaniu. |Usuń i ponownie prześlij żądanie. Jest to błąd programowania, który zazwyczaj jest przechwytywane w początkowej fazie testowania. |
| server_error |Serwer napotkał nieoczekiwany błąd. |Ponów żądanie. Te błędy mogą być wynikiem tymczasowe warunki. Aplikacja kliencka może wyjaśnić użytkownikowi, że odpowiedzi przez punkt końcowy jest opóźnione ze względu na tymczasowy błąd. |
| temporarily_unavailable |Serwer jest tymczasowo zbyt zajęty, aby obsłużyć żądanie. |Ponów żądanie. Aplikacja kliencka może wyjaśnić użytkownikowi, że odpowiedzi przez punkt końcowy jest opóźnione ze względu na tymczasowy warunek. |
| invalid_resource |Zasób docelowy jest nieprawidłowy, ponieważ nie istnieje, usługa Azure AD nie może okazać się lub nie jest poprawnie skonfigurowana. |Oznacza to, że zasób, jeśli istnieje, nie skonfigurowano w dzierżawie. Aplikacja może monitować użytkownika instrukcje dotyczące instalowania aplikacji, a następnie dodanie go do usługi Azure AD. |

## <a name="validate-the-id-token"></a>Sprawdzanie poprawności tokenu Identyfikacyjnego

Po prostu odbiera id_token nie wystarcza do uwierzytelnienia użytkownika; należy sprawdzić poprawności podpisu id_token i weryfikować oświadczenia w tokenie na wymagania dotyczące Twojej aplikacji. Korzysta z punktu końcowego v2.0 [tokenów sieci Web JSON (tokenów Jwt)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) i kryptografii klucza publicznego do podpisywania tokenów i sprawdź, czy są prawidłowe.

Można wybrać sprawdzić poprawność `id_token` w kliencie kod, ale powszechną praktyką jest wysłanie `id_token` do serwera wewnętrznej bazy danych i zweryfikować istnieje. Po zweryfikowaniu podpis id_token, istnieje kilka oświadczenia, które będzie trzeba zweryfikować. Zobacz [ `id_token` odwołania](id-tokens.md) uzyskać więcej informacji, w tym [sprawdzania poprawności tokenów](id-tokens.md#validating-an-id_token) i [ważnych informacji dotyczących podpisywania Przerzucanie klucza](active-directory-signing-key-rollover.md). Firma Microsoft zaleca korzystające z biblioteki do analizowania i sprawdzanie poprawności tokenów — Brak co najmniej jeden dostępny dla większości platform i języków.
<!--TODO: Improve the information on this-->

Możesz również sprawdzić dodatkowe oświadczenia w zależności od scenariusza. Niektórych typowych operacji sprawdzania poprawności obejmują:

* Zapewnienie użytkownika/organizacja po zarejestrowaniu w aplikacji.
* Zapewnienie użytkownika ma odpowiednie zezwolenia/uprawnień
* Zapewnienie siły uwierzytelniania wystąpił, takie jak uwierzytelnianie wieloskładnikowe.

Po zweryfikowaniu całkowicie id_token, można rozpocząć sesji z użytkownikiem i korzystanie z oświadczeń id_token, aby uzyskać informacje o użytkowniku w swojej aplikacji. Te informacje mogą służyć do wyświetlania rekordów, personalizacji itp.

## <a name="send-a-sign-out-request"></a>Wyślij żądanie wylogowania

Jeśli chcesz się wylogować użytkownika z Twojej aplikacji jest niewystarczająca do wyczyszczenie plików cookie w swojej aplikacji lub w przeciwnym razie kończenie sesji użytkownika. Należy również przekierować użytkownika do punktu końcowego v2.0, aby się wylogować. Jeśli nie możesz tego zrobić, użytkownik uwierzytelniają ponownie do aplikacji bez konieczności wprowadzania ich poświadczenia ponownie, ponieważ mają one nieprawidłowy jednej sesji logowania z punktem końcowym v2.0.

Można przekierować użytkownika do `end_session_endpoint` wymienione w tym dokumencie metadanych OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parametr | Warunek | Opis |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | Zalecane | Adres URL, który użytkownik jest przekierowywany do po wylogowaniu się pomyślnie. Jeśli parametr nie jest uwzględniony, użytkownik jest wyświetlany ogólny komunikat, który jest generowany przez punktu końcowego v2.0. Ten adres URL musi odpowiadać przekierowania URI zarejestrowanej aplikacji w portalu rejestracji aplikacji. |

## <a name="single-sign-out"></a>Wylogowanie jednokrotne

Gdy przekierowania użytkownika `end_session_endpoint`, punktu końcowego v2.0 czyści sesji użytkownika w przeglądarce. Jednak użytkownik może nadal być zalogowany do innych aplikacji, które używają kont Microsoft do uwierzytelniania. Aby włączyć te aplikacje do logowania użytkownika się równocześnie, v2.0 punktu końcowego wysyła żądanie HTTP GET do zarejestrowaną `LogoutUrl` wszystkich aplikacji, które użytkownik jest aktualnie zalogowany. Aplikacje należy odpowiedzieć na to żądanie, czyszcząc żadnych sesji, która identyfikuje użytkownika i zwracanie `200` odpowiedzi. Jeśli chcesz obsługiwać pojedynczego wylogowania w aplikacji, musisz zaimplementować takie `LogoutUrl` w kodzie twojej aplikacji. Możesz ustawić `LogoutUrl` z poziomu portalu rejestracji aplikacji.

## <a name="protocol-diagram-access-token-acquisition"></a>Diagram protokołu: Uzyskanie tokenu dostępu

Wiele aplikacji sieci web muszą się nie tylko użytkownika w, ale także dostęp do usługi sieci web w imieniu użytkownika przy użyciu protokołu OAuth. Ten scenariusz łączy OpenID Connect do uwierzytelniania użytkowników podczas jednoczesnego pobierania kodu autoryzacji, który umożliwia uzyskiwanie tokenów dostępu, jeśli używasz przepływ kodu autoryzacji OAuth.

Pełny przepływ logowania i tokenu pozyskiwania protokołu OpenID Connect wygląda podobnie do następny diagram. Opisano każdy krok szczegółowo w kolejnych sekcjach tego artykułu.

![Protokół OpenID Connect: Uzyskanie tokenu](./media/v2-protocols-oidc/convergence_scenarios_webapp_webapi.png)

## <a name="get-access-tokens"></a>Uzyskiwanie tokenów dostępu
Uzyskanie tokenów dostępu, należy zmodyfikować żądanie logowania:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs  
offline_access%20                                         
https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> Kliknij poniższy link, aby wykonać tego żądania. Po zalogowaniu się w przeglądarce jest przekierowywane do `https://localhost/myapp/`, za pomocą tokenu Identyfikacyjnego i kodu na pasku adresu. Należy zauważyć, że używa tego żądania `response_mode=fragment` tylko w celach demonstracyjnych. Firma Microsoft zaleca użycie `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

Przy tym zakresy uprawnień w żądaniu oraz przy użyciu `response_type=id_token code`, punktu końcowego v2.0 gwarantuje, że użytkownik wyraził zgodę na uprawnienia czcionką `scope` parametr zapytania. Zwraca kod autoryzacji do aplikacji do wymiany dla tokenu dostępu.

### <a name="successful-response"></a>Pomyślna odpowiedź

Odpowiedź oznaczająca Powodzenie korzystania z `response_mode=form_post` wygląda podobnie do następującego:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametr | Opis |
| --- | --- |
| id_token |Identyfikator tokenu, który zażądał aplikacji. Identyfikator tokenu można użyć do zweryfikowania tożsamości użytkownika i rozpocząć sesję z użytkownikiem. Znajdziesz więcej szczegółów na temat tokeny Identyfikatora i ich zawartość w [ `id_tokens` odwołania](id-tokens.md). |
| kod |Kod autoryzacji, który zażądał aplikacji. Aplikacja może używać kodu autoryzacji do żądania tokenu dostępu dla zasobu docelowego. Kod autoryzacji jest bardzo krótkotrwałe. Zazwyczaj kod autoryzacji wygaśnie po upływie około 10 minut. |
| state |Jeśli parametr Stan jest uwzględniony w żądaniu, tę samą wartość powinna pojawić się w odpowiedzi. Aplikację należy sprawdzić, czy wartości stanu żądania i odpowiedzi są identyczne. |

### <a name="error-response"></a>Odpowiedzi na błąd

Odpowiedzi na błędy mogą zostać wysłane do identyfikatora URI przekierowania, dzięki czemu aplikacja może je odpowiednio obsługiwać. Odpowiedź na błąd wygląda następująco:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| error |Ciągu kodu błędu, który służy do klasyfikowania typy błędów, które występują i reagować na błędy. |
| error_description |Komunikat błędu, który pomoże Ci identyfikować przyczyny błędu uwierzytelniania. |

Aby uzyskać opis możliwe kody błędów i opartej na zalecanym kliencie odpowiedzi, zobacz [kody błędów dla błędów punktu końcowego autoryzacji](#error-codes-for-authorization-endpoint-errors).

Jeśli masz kod autoryzacji i identyfikator tokenu, można zalogować użytkownika i uzyskiwanie tokenów dostępu w ich imieniu. Do logowania użytkownika, musisz zweryfikować tokenu Identyfikacyjnego [dokładnie zgodnie z opisem](id-tokens.md#validating-an-id_token). Uzyskiwanie tokenów dostępu, wykonaj czynności opisane w [dokumentacji przepływu kodu OAuth](v2-oauth2-auth-code-flow.md#request-an-access-token).
