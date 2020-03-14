---
title: OpenID Connect Connect Protocol — Microsoft Identity platform | Azure
description: Tworzenie aplikacji sieci Web przy użyciu implementacji platformy tożsamości firmy Microsoft w ramach protokołu uwierzytelniania OpenID Connect Connect.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a4875997-3aac-4e4c-b7fe-2b4b829151ce
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0ed1cb6a080a35fa81c6a859f88d987020c8504c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262297"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Microsoft Identity platform i OpenID Connect Connect Protocol

OpenID Connect Connect to protokół uwierzytelniania oparty na protokole OAuth 2,0, którego można użyć do bezpiecznego logowania użytkownika do aplikacji sieci Web. W przypadku korzystania z programu OpenID Connect Connect dla punktu końcowego platformy tożsamości firmy Microsoft można dodać funkcję logowania i dostęp do interfejsu API do aplikacji sieci Web. W tym artykule pokazano, jak wykonać ten niezależny język oraz jak wysyłać i odbierać komunikaty HTTP bez używania bibliotek typu open source firmy Microsoft.

> [!NOTE]
> Punkt końcowy platformy tożsamości firmy Microsoft nie obsługuje wszystkich scenariuszy i funkcji usługi Azure Active Directory (Azure AD). Aby określić, czy należy używać punktu końcowego platformy tożsamości firmy Microsoft, przeczytaj artykuł [ograniczenia dotyczące platformy tożsamości firmy Microsoft](active-directory-v2-limitations.md).

[OpenID Connect Connect](https://openid.net/specs/openid-connect-core-1_0.html) rozszerza protokół *autoryzacji* OAuth 2,0 do użycia jako protokół *uwierzytelniania* , dzięki czemu można przeprowadzić Logowanie jednokrotne przy użyciu protokołu OAuth. OpenID Connect Connect wprowadza koncepcję *tokenu identyfikatora*, który jest tokenem zabezpieczającym umożliwiającym klientowi zweryfikowanie tożsamości użytkownika. Token identyfikatora pobiera również podstawowe informacje o profilu użytkownika. Ponieważ OpenID Connect Connect rozszerza uwierzytelnianie OAuth 2,0, aplikacje mogą bezpiecznie uzyskać *tokeny dostępu*, które mogą być używane w celu uzyskania dostępu do zasobów zabezpieczonych przez [serwer autoryzacji](active-directory-v2-protocols.md#the-basics). Punkt końcowy platformy tożsamości firmy Microsoft umożliwia także aplikacjom innych firm, które są zarejestrowane w usłudze Azure AD, aby wystawiać tokeny dostępu dla zabezpieczonych zasobów, takich jak interfejsy API sieci Web. Aby uzyskać więcej informacji na temat sposobu konfigurowania aplikacji w celu wystawiania tokenów dostępu, zobacz [jak zarejestrować aplikację w punkcie końcowym platformy tożsamości firmy Microsoft](quickstart-register-app.md). Zalecamy korzystanie z programu OpenID Connect Connect, jeśli tworzysz [aplikację sieci Web](v2-app-types.md#web-apps) , która jest hostowana na serwerze i jest dostępna za pośrednictwem przeglądarki.

## <a name="protocol-diagram-sign-in"></a>Diagram protokołów: Logowanie

Najbardziej podstawowy przepływ logowania zawiera kroki przedstawione na następnym diagramie. Każdy krok został szczegółowo opisany w tym artykule.

![OpenID Connect Connect Protocol: Logowanie](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Pobieranie dokumentu OpenID Connect Connect Metadata

OpenID Connect Connect opisuje dokument metadanych zawierający większość informacji wymaganych do zalogowania się do aplikacji. Obejmuje to takie informacje, jak adresy URL, które mają być używane, oraz lokalizację publicznych kluczy podpisywania usługi. W przypadku punktu końcowego platformy tożsamości firmy Microsoft jest to dokument metadanych OpenID Connect Connect, którego należy użyć:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP]
> Wypróbuj! Kliknij przycisk [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) , aby wyświetlić konfigurację `common` dzierżawców.

`{tenant}` może przyjmować jedną z czterech wartości:

| Wartość | Opis |
| --- | --- |
| `common` |Użytkownicy korzystający zarówno z osobistego konto Microsoft, jak i konta służbowego z usługi Azure AD mogą zalogować się do aplikacji. |
| `organizations` |Tylko użytkownicy z kontami służbowymi z usługi Azure AD mogą logować się do aplikacji. |
| `consumers` |Tylko użytkownicy z konto Microsoft osobistym mogą logować się do aplikacji. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` lub `contoso.onmicrosoft.com` | Tylko użytkownicy z określonej dzierżawy usługi Azure AD (bez względu na to, czy są członkami w katalogu przy użyciu konta służbowego, czy są Gośćmi w katalogu z osobistą konto Microsoft) mogą zalogować się do aplikacji. Można użyć przyjaznej nazwy domeny dzierżawy usługi Azure AD lub identyfikatora GUID dzierżawcy. Zamiast dzierżawcy `consumers`, można również użyć dzierżawy klienta `9188040d-6c67-4c5b-b112-36a304b66dad`.  |

Metadane są prostym dokumentem JavaScript Object Notation (JSON). Zapoznaj się z poniższym fragmentem kodu. Zawartość tego fragmentu kodu jest w pełni opisana w [specyfikacji OpenID Connect Connect](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/{tenant}\/discovery\/v2.0\/keys",

  ...

}
```

Jeśli aplikacja ma niestandardowe klucze podpisywania w wyniku użycia funkcji [mapowania oświadczeń](active-directory-claims-mapping.md) , należy dołączyć parametr zapytania `appid` zawierający identyfikator aplikacji w celu uzyskania `jwks_uri` wskazującego informacje o kluczu podpisywania aplikacji. Na przykład: `https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` zawiera `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

Zazwyczaj ten dokument metadanych służy do konfigurowania biblioteki lub zestawu SDK OpenID Connect Connect. Biblioteka będzie używać metadanych do wykonywania swoich zadań. Jeśli jednak nie używasz wstępnie skompilowanej biblioteki OpenID Connect Connect, możesz wykonać czynności opisane w dalszej części tego artykułu, aby wykonać logowanie w aplikacji sieci Web przy użyciu punktu końcowego platformy tożsamości firmy Microsoft.

## <a name="send-the-sign-in-request"></a>Wyślij żądanie logowania

Gdy aplikacja sieci Web wymaga uwierzytelnienia użytkownika, może kierować użytkownika do punktu końcowego `/authorize`. To żądanie jest podobne do pierwszego etapu [przepływu kodu autoryzacji OAuth 2,0](v2-oauth2-auth-code-flow.md), z następującymi istotnymi różnicami:

* Żądanie musi zawierać zakres `openid` w parametrze `scope`.
* Parametr `response_type` musi zawierać `id_token`.
* Żądanie musi zawierać parametr `nonce`.

> [!IMPORTANT]
> Aby można było pomyślnie zażądać tokenu identyfikatora z punktu końcowego/Authorization, Rejestracja aplikacji w [portalu rejestracji](https://portal.azure.com) musi mieć niejawne przyznanie id_tokens włączony na karcie uwierzytelnianie (co ustawia flagę `oauth2AllowIdTokenImplicitFlow` w [manifeście aplikacji](reference-app-manifest.md) na `true`). Jeśli nie jest włączona, zostanie zwrócony błąd `unsupported_response`: "podana wartość parametru wejściowego" response_type "nie jest dozwolona dla tego klienta. Oczekiwana wartość to "Code" "

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
> Kliknij następujący link, aby wykonać to żądanie. Po zalogowaniu przeglądarka zostanie przekierowana do `https://localhost/myapp/`z tokenem ID na pasku adresu. Należy zauważyć, że to żądanie używa `response_mode=fragment` (tylko w celach demonstracyjnych). Zalecamy używanie `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parametr | Warunek | Opis |
| --- | --- | --- |
| `tenant` | Wymagany | Możesz użyć wartości `{tenant}` w ścieżce żądania, aby kontrolować, kto może logować się do aplikacji. Dozwolone wartości to `common`, `organizations`, `consumers`i identyfikator dzierżawy. Aby uzyskać więcej informacji, zobacz temat podstawowe informacje o [protokole](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Wymagany | **Identyfikator aplikacji (klienta)** , który [Azure Portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) środowisko przypisane do aplikacji. |
| `response_type` | Wymagany | Musi zawierać `id_token` do logowania za OpenID Connect Connect. Może również zawierać inne `response_type` wartości, takie jak `code`. |
| `redirect_uri` | Zalecane | Identyfikator URI przekierowania aplikacji, w którym odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Musi dokładnie pasować do jednego z identyfikatorów URI przekierowania zarejestrowanego w portalu, z tą różnicą, że musi on być zakodowany w adresie URL. Jeśli nie istnieje, punkt końcowy wybiera jeden zarejestrowany redirect_uri losowo, aby wysłać użytkownika z powrotem do. |
| `scope` | Wymagany | Rozdzielana spacjami lista zakresów. W przypadku programu OpenID Connect Connect musi zawierać zakres `openid`, który tłumaczy na uprawnienie "Logowanie do Ciebie" w interfejsie użytkownika wyrażania zgody. Do żądania zgody można także uwzględnić inne zakresy w tym żądaniu. |
| `nonce` | Wymagany | Wartość uwzględniona w żądaniu wygenerowanym przez aplikację, która zostanie uwzględniona w wyniku id_token wartość jako żądanie. Aplikacja może zweryfikować tę wartość, aby zmniejszyć ataki metodą powtórzeń tokenu. Zwykle jest to losowy, unikatowy ciąg, który może służyć do identyfikowania pochodzenia żądania. |
| `response_mode` | Zalecane | Określa metodę, która ma zostać użyta do wysłania podanego kodu autoryzacji z powrotem do aplikacji. Możliwe wartości to `form_post` i `fragment`. W przypadku aplikacji sieci Web zalecamy używanie `response_mode=form_post`, aby zapewnić najbardziej bezpieczny transfer tokenów do aplikacji. |
| `state` | Zalecane | Wartość zawarta w żądaniu, która również zostanie zwrócona w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Losowo generowana unikatowa wartość jest używana w celu [zapobiegania atakom na fałszerstwo żądań między lokacjami](https://tools.ietf.org/html/rfc6749#section-10.12). Ten stan jest również używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takiego jak strona lub widok użytkownika. |
| `prompt` | Optional (Opcjonalność) | Wskazuje typ interakcji użytkownika, która jest wymagana. Jedyne prawidłowe wartości w tym momencie to `login`, `none`i `consent`. W ramach tego żądania `prompt=login` użytkownik może wprowadzić poświadczenia, które negacją logowania jednokrotnego. `prompt=none`m jest przeciwieństwem. To oświadczenia gwarantuje, że użytkownik nie jest wyświetlany z żadnym interakcyjnym monitem. Jeśli żądanie nie może zostać zakończone dyskretnie przy użyciu logowania jednokrotnego, punkt końcowy platformy tożsamości firmy Microsoft zwraca błąd. Po zalogowaniu się użytkownika zostanie wyzwolone okno dialogowe zgody na `prompt=consent`. Okno dialogowe prosi użytkownika o przyznanie uprawnień aplikacji. |
| `login_hint` | Optional (Opcjonalność) | Tego parametru można użyć do wstępnego wypełnienia pola Nazwa użytkownika i adres e-mail na stronie logowania użytkownika, jeśli znasz nazwę użytkownika przed czasem. Często aplikacje używają tego parametru podczas ponownego uwierzytelniania, po już wyodrębnieniu nazwy użytkownika ze starszej rejestracji przy użyciu `preferred_username`. |
| `domain_hint` | Optional (Opcjonalność) | Obszar użytkownika w katalogu federacyjnym.  Pomija to proces odnajdywania na podstawie poczty e-mail, który użytkownik przechodzi na stronie logowania, aby nieco bardziej usprawnić środowisko użytkownika. W przypadku dzierżawców federacyjnych za pomocą katalogu lokalnego, takiego jak AD FS, często powoduje to bezproblemowe logowanie z powodu istniejącej sesji logowania. |

W tym momencie użytkownik zostanie poproszony o wprowadzenie poświadczeń i zakończenie uwierzytelniania. Punkt końcowy platformy tożsamości firmy Microsoft sprawdza, czy użytkownik wyraził zgodę na uprawnienia wskazane w `scope` parametr zapytania. Jeśli użytkownik nie wyraził zgody na żadne z tych uprawnień, punkt końcowy platformy tożsamości firmy Microsoft poprosi użytkownika o zgodę na wymagane uprawnienia. Więcej informacji o [uprawnieniach, zgodzie i aplikacjach wielodostępnych](v2-permissions-and-consent.md)można znaleźć w części.

Po uwierzytelnieniu i udzieleniu zgody użytkownik końcowy platformy tożsamości firmy Microsoft zwraca odpowiedź do aplikacji na wskazanym identyfikatorze URI przekierowania przy użyciu metody określonej w parametrze `response_mode`.

### <a name="successful-response"></a>Pomyślna odpowiedź

Pomyślna odpowiedź w przypadku używania `response_mode=form_post` wygląda następująco:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametr | Opis |
| --- | --- |
| `id_token` | Token identyfikatora, którego żądała aplikacja. Możesz użyć parametru `id_token`, aby zweryfikować tożsamość użytkownika i rozpocząć sesję z użytkownikiem. Aby uzyskać więcej informacji o tokenach identyfikatorów i ich zawartości, zobacz [informacje dotyczące`id_tokens`](id-tokens.md). |
| `state` | Jeśli w żądaniu zostanie uwzględniony parametr `state`, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy wartości stanu w żądaniu i odpowiedzi są identyczne. |

### <a name="error-response"></a>Odpowiedź na błąd

Odpowiedzi na błędy mogą być również wysyłane do identyfikatora URI przekierowania, aby aplikacja mogła je obsłużyć. Odpowiedź na błąd wygląda następująco:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| `error` | Ciąg kodu błędu, którego można użyć do klasyfikowania typów błędów, które występują, oraz do reagowania na błędy. |
| `error_description` | Konkretny komunikat o błędzie, który może pomóc w zidentyfikowaniu głównej przyczyny błędu uwierzytelniania. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Kody błędów dla błędów punktu końcowego autoryzacji

W poniższej tabeli opisano kody błędów, które mogą być zwracane w `error` parametru odpowiedzi na błąd:

| Kod błędu | Opis | Akcja klienta |
| --- | --- | --- |
| `invalid_request` | Błąd protokołu, taki jak brak wymaganego parametru. |Napraw i ponownie prześlij żądanie. Jest to błąd programistyczny, który zwykle jest przechwytywany podczas wstępnego testowania. |
| `unauthorized_client` | Aplikacja kliencka nie może zażądać kodu autoryzacji. |Zwykle dzieje się tak, gdy aplikacja kliencka nie jest zarejestrowana w usłudze Azure AD lub nie została dodana do dzierżawy usługi Azure AD użytkownika. Aplikacja może monitować użytkownika z instrukcjami dotyczącymi instalowania aplikacji i dodawania jej do usługi Azure AD. |
| `access_denied` | Właściciel zasobu odmówił zgody. |Aplikacja kliencka może powiadomić użytkownika, że nie może wykonać tej czynności, chyba że użytkownik wyraził zgodę. |
| `unsupported_response_type` |Serwer autoryzacji nie obsługuje typu odpowiedzi w żądaniu. |Napraw i ponownie prześlij żądanie. Jest to błąd programistyczny, który zwykle jest przechwytywany podczas wstępnego testowania. |
| `server_error` | Serwer napotkał nieoczekiwany błąd. |Ponów żądanie. Te błędy mogą wynikać z warunków tymczasowych. Aplikacja kliencka może wyjaśnić użytkownikowi, że odpowiedź jest opóźniona z powodu tymczasowego błędu. |
| `temporarily_unavailable` | Serwer jest tymczasowo zbyt zajęty, aby obsłużyć żądanie. |Ponów żądanie. Aplikacja kliencka może wyjaśnić użytkownikowi, że jego odpowiedź jest opóźniona ze względu na tymczasowy warunek. |
| `invalid_resource` | Zasób docelowy jest nieprawidłowy, ponieważ nie istnieje, usługa Azure AD nie może go odnaleźć lub nie jest poprawnie skonfigurowana. |Oznacza to, że zasób, jeśli istnieje, nie został skonfigurowany w dzierżawie. Aplikacja może monitować użytkownika z instrukcjami dotyczącymi instalowania aplikacji i dodawania jej do usługi Azure AD. |

## <a name="validate-the-id-token"></a>Weryfikowanie tokenu identyfikatora

Tylko otrzymanie id_token nie wystarcza do uwierzytelnienia użytkownika; należy sprawdzić poprawność podpisu id_token i zweryfikować oświadczenia w tokenie zgodnie z wymaganiami aplikacji. Punkt końcowy platformy tożsamości firmy Microsoft korzysta z [tokenów sieci Web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) i kryptografii klucza publicznego w celu podpisania tokenów i sprawdzenia, czy są one prawidłowe.

Można sprawdzić poprawność `id_token` w kodzie klienta, ale typowe rozwiązanie polega na wysłaniu `id_token` do serwera wewnętrznej bazy danych i zakończeniu weryfikacji. Po sprawdzeniu poprawności podpisu id_token istnieje kilka oświadczeń, które będą wymagane do zweryfikowania. Zobacz [odwołanie`id_token`](id-tokens.md) , aby uzyskać więcej informacji, w tym [Sprawdzanie poprawności tokenów](id-tokens.md#validating-an-id_token) i [ważnych informacji na temat przerzucania kluczy podpisywania](active-directory-signing-key-rollover.md). Zalecamy korzystanie z biblioteki do analizowania i weryfikowania tokenów — istnieje co najmniej jedna dostępna dla większości języków i platform.

Możesz również chcieć sprawdzić poprawność dodatkowych oświadczeń w zależności od danego scenariusza. Niektóre typowe walidacje obejmują:

* Upewnienie się, że użytkownik/organizacja zarejestrowali się w aplikacji.
* Upewnienie się, że użytkownik ma odpowiednie uprawnienia/autoryzacja
* Upewnienie się, że wystąpiło pewne silne uwierzytelnianie, takie jak uwierzytelnianie wieloskładnikowe.

Po sprawdzeniu id_token można rozpocząć sesję z użytkownikiem i użyć oświadczeń w id_token, aby uzyskać informacje o użytkowniku w aplikacji. Te informacje mogą służyć do wyświetlania, rekordów, personalizacji itp.

## <a name="send-a-sign-out-request"></a>Wyślij żądanie wylogowania

Gdy chcesz wylogować użytkownika z aplikacji, nie wystarcza do wyczyszczenia plików cookie aplikacji lub zakończenie sesji użytkownika. Musisz również przekierować użytkownika do punktu końcowego platformy tożsamości firmy Microsoft, aby się wylogować. Jeśli tego nie zrobisz, użytkownik ponownie uwierzytelni się w aplikacji bez konieczności ponownego wprowadzania poświadczeń, ponieważ będzie mieć prawidłową sesję logowania jednokrotnego z punktem końcowym platformy tożsamości firmy Microsoft.

Możesz przekierować użytkownika do `end_session_endpoint` wymienionego w dokumencie metadanych OpenID Connect Connect:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parametr | Warunek | Opis |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Zalecane | Adres URL, do którego użytkownik jest przekierowywany po pomyślnym wylogowaniu. Jeśli parametr nie jest uwzględniony, użytkownik zostanie pokazany komunikat ogólny, który jest generowany przez punkt końcowy platformy tożsamości firmy Microsoft. Ten adres URL musi być zgodny z jednym z identyfikatorów URI przekierowania zarejestrowanych dla aplikacji w portalu rejestracji aplikacji. |

## <a name="single-sign-out"></a>Wylogowanie jednokrotne

Po przekierowaniu użytkownika do `end_session_endpoint`punkt końcowy platformy tożsamości firmy Microsoft czyści sesję użytkownika z przeglądarki. Jednak użytkownik może nadal być zalogowany do innych aplikacji korzystających z kont Microsoft do uwierzytelniania. Aby umożliwić tym aplikacjom jednoczesne podpisywanie użytkownika, punkt końcowy platformy tożsamości firmy Microsoft wysyła żądanie HTTP GET do zarejestrowanej `LogoutUrl` wszystkich aplikacji, do których użytkownik jest aktualnie zalogowany. Aplikacje muszą odpowiedzieć na to żądanie przez wyczyszczenie każdej sesji, która identyfikuje użytkownika i zwróci odpowiedź `200`. Jeśli chcesz obsługiwać Logowanie jednokrotne w aplikacji, musisz zaimplementować takie `LogoutUrl` w kodzie aplikacji. `LogoutUrl` można ustawić w portalu rejestracji aplikacji.

## <a name="protocol-diagram-access-token-acquisition"></a>Diagram protokołów: dostęp do uzyskiwania tokenu dostępu

Wiele aplikacji sieci Web musi nie tylko podpisać użytkownika w programie, ale również uzyskać dostęp do usługi sieci Web w imieniu użytkownika przy użyciu protokołu OAuth. Ten scenariusz łączy OpenID Connect Connect for User Authentication, jednocześnie pobierając kod autoryzacji, za pomocą którego można uzyskać tokeny dostępu, jeśli używasz przepływu kodu autoryzacji OAuth.

Pełna rejestracja w usłudze OpenID Connect Connect i przepływ pozyskiwania tokenów wygląda podobnie do następnego diagramu. Szczegółowo opisano każdy krok w następnych sekcjach artykułu.

![OpenID Connect Connect Protocol: pozyskiwanie tokenu](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-access-tokens"></a>Uzyskaj tokeny dostępu
Aby uzyskać tokeny dostępu, zmodyfikuj żądanie logowania:

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
> Kliknij następujący link, aby wykonać to żądanie. Po zalogowaniu przeglądarka zostanie przekierowana do `https://localhost/myapp/`, z tokenem ID i kodem na pasku adresu. Należy zauważyć, że to żądanie używa tylko `response_mode=fragment` tylko do celów demonstracyjnych. Zalecamy używanie `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

Dzięki dołączeniu zakresów uprawnień w żądaniu i przy użyciu `response_type=id_token code`punkt końcowy platformy tożsamości firmy Microsoft zapewnia, że użytkownik wyraził zgodę na uprawnienia wskazane w parametrze `scope` Query. Zwraca kod autoryzacji do aplikacji w celu wymiany tokenu dostępu.

### <a name="successful-response"></a>Pomyślna odpowiedź

Pomyślna odpowiedź z używania `response_mode=form_post` wygląda następująco:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametr | Opis |
| --- | --- |
| `id_token` | Token identyfikatora, którego żądała aplikacja. Możesz użyć tokenu identyfikatora, aby zweryfikować tożsamość użytkownika i rozpocząć sesję z użytkownikiem. Więcej informacji o tokenach identyfikatorów i ich zawartości można znaleźć w [dokumentacji`id_tokens`](id-tokens.md). |
| `code` | Kod autoryzacji żądany przez aplikację. Aplikacja może użyć kodu autoryzacji, aby zażądać tokenu dostępu dla zasobu docelowego. Kod autoryzacji jest krótki. Zazwyczaj kod autoryzacji wygasa około 10 minut. |
| `state` | Jeśli w żądaniu zostanie uwzględniony parametr stanu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy wartości stanu w żądaniu i odpowiedzi są identyczne. |

### <a name="error-response"></a>Odpowiedź na błąd

Odpowiedzi na błędy mogą być również wysyłane do identyfikatora URI przekierowania, aby aplikacja mogła je odpowiednio obsłużyć. Odpowiedź na błąd wygląda następująco:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| `error` | Ciąg kodu błędu, którego można użyć do klasyfikowania typów błędów, które występują, oraz do reagowania na błędy. |
| `error_description` | Konkretny komunikat o błędzie, który może pomóc w zidentyfikowaniu głównej przyczyny błędu uwierzytelniania. |

Opis możliwych kodów błędów i zalecanych odpowiedzi klientów można znaleźć w temacie [kody błędów dla błędów punktu końcowego autoryzacji](#error-codes-for-authorization-endpoint-errors).

Jeśli masz kod autoryzacji i token identyfikatora, możesz podpisać użytkownika w usłudze i uzyskać tokeny dostępu w ich imieniu. Aby podpisać użytkownika w programie, należy sprawdzić poprawność tokenu identyfikatora [dokładnie zgodnie z opisem](id-tokens.md#validating-an-id_token). Aby uzyskać tokeny dostępu, wykonaj kroki opisane w [dokumentacji dotyczącej przepływu kodu OAuth](v2-oauth2-auth-code-flow.md#request-an-access-token).
