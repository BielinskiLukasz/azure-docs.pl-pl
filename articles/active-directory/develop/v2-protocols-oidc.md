---
title: Platforma tożsamości firmy Microsoft i protokół OpenID Connect Connect | Azure
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69aa2da29e18f99e75e09d8f21814b71cc95ef72
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852140"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Microsoft Identity platform i OpenID Connect Connect Protocol

OpenID Connect Connect to protokół uwierzytelniania oparty na protokole OAuth 2,0, którego można użyć do bezpiecznego logowania użytkownika do aplikacji sieci Web. W przypadku korzystania z programu OpenID Connect Connect dla punktu końcowego platformy tożsamości firmy Microsoft można dodać funkcję logowania i dostęp do interfejsu API do aplikacji sieci Web. W tym artykule pokazano, jak wykonać ten niezależny język oraz jak wysyłać i odbierać komunikaty HTTP bez używania bibliotek typu open source firmy Microsoft.

> [!NOTE]
> Punkt końcowy platformy tożsamości firmy Microsoft nie obsługuje wszystkich scenariuszy i funkcji usługi Azure Active Directory (Azure AD). Aby określić, czy należy używać punktu końcowego platformy tożsamości firmy Microsoft, przeczytaj artykuł [ograniczenia dotyczące platformy tożsamości firmy Microsoft](active-directory-v2-limitations.md).

[OpenID Connect Connect](https://openid.net/specs/openid-connect-core-1_0.html) rozszerza protokół *autoryzacji* OAuth 2,0 do użycia jako protokół *uwierzytelniania* , dzięki czemu można przeprowadzić Logowanie jednokrotne przy użyciu protokołu OAuth. OpenID Connect Connect wprowadza koncepcję *tokenu identyfikatora*, który jest tokenem zabezpieczającym umożliwiającym klientowi zweryfikowanie tożsamości użytkownika. Token identyfikatora pobiera również podstawowe informacje o profilu użytkownika. Ponieważ OpenID Connect Connect rozszerza uwierzytelnianie OAuth 2,0, aplikacje mogą bezpiecznie uzyskać *tokeny dostępu*, które mogą być używane w celu uzyskania dostępu do zasobów zabezpieczonych przez [serwer autoryzacji](active-directory-v2-protocols.md#the-basics). Punkt końcowy platformy tożsamości firmy Microsoft umożliwia także aplikacjom innych firm, które są zarejestrowane w usłudze Azure AD, aby wystawiać tokeny dostępu dla zabezpieczonych zasobów, takich jak interfejsy API sieci Web. Aby uzyskać więcej informacji na temat sposobu konfigurowania aplikacji w celu wystawiania tokenów dostępu, zobacz [jak zarejestrować aplikację w punkcie końcowym platformy tożsamości firmy Microsoft](quickstart-register-app.md). Zalecamy korzystanie z programu OpenID Connect Connect, jeśli tworzysz [aplikację sieci Web](v2-app-types.md#web-apps) , która jest hostowana na serwerze i jest dostępna za pośrednictwem przeglądarki.

## <a name="protocol-diagram-sign-in"></a>Diagram protokołów: Logowanie

Najbardziej podstawowy przepływ logowania zawiera kroki przedstawione na następnym diagramie. Każdy krok został szczegółowo opisany w tym artykule.

![Protokół OpenID Connect Connect: Logowanie](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Pobieranie dokumentu OpenID Connect Connect Metadata

OpenID Connect Connect opisuje dokument metadanych zawierający większość informacji wymaganych do zalogowania się do aplikacji. Obejmuje to takie informacje, jak adresy URL, które mają być używane, oraz lokalizację publicznych kluczy podpisywania usługi. W przypadku punktu końcowego platformy tożsamości firmy Microsoft jest to dokument metadanych OpenID Connect Connect, którego należy użyć:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP]
> Wypróbuj! Kliknij [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) ,`common` aby wyświetlić konfigurację dzierżawców.

`{tenant}` Może przyjmować jedną z czterech wartości:

| Value | Opis |
| --- | --- |
| `common` |Użytkownicy korzystający zarówno z osobistego konto Microsoft, jak i konta służbowego z usługi Azure AD mogą zalogować się do aplikacji. |
| `organizations` |Tylko użytkownicy z kontami służbowymi z usługi Azure AD mogą logować się do aplikacji. |
| `consumers` |Tylko użytkownicy z konto Microsoft osobistym mogą logować się do aplikacji. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` lub `contoso.onmicrosoft.com` | Tylko użytkownicy z określonej dzierżawy usługi Azure AD (bez względu na to, czy są członkami w katalogu przy użyciu konta służbowego, czy są Gośćmi w katalogu z osobistą konto Microsoft) mogą zalogować się do aplikacji. Można użyć przyjaznej nazwy domeny dzierżawy usługi Azure AD lub identyfikatora GUID dzierżawcy. Możesz również użyć dzierżawy `9188040d-6c67-4c5b-b112-36a304b66dad`klienta, zamiast `consumers` dzierżawcy.  |

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

Jeśli aplikacja ma niestandardowe klucze podpisywania w wyniku użycia funkcji [mapowania oświadczeń](active-directory-claims-mapping.md) , należy dołączyć `appid` parametr zapytania zawierający identyfikator aplikacji, aby `jwks_uri` wskazać informacje o kluczu podpisywania aplikacji. Na przykład: `https://login.microsoftonline.com/{tenant}/.well-known/v2.0/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` `jwks_uri` zawiera `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

Zazwyczaj ten dokument metadanych służy do konfigurowania biblioteki lub zestawu SDK OpenID Connect Connect. Biblioteka będzie używać metadanych do wykonywania swoich zadań. Jeśli jednak nie używasz wstępnie skompilowanej biblioteki OpenID Connect Connect, możesz wykonać czynności opisane w dalszej części tego artykułu, aby wykonać logowanie w aplikacji sieci Web przy użyciu punktu końcowego platformy tożsamości firmy Microsoft.

## <a name="send-the-sign-in-request"></a>Wyślij żądanie logowania

Gdy aplikacja sieci Web wymaga uwierzytelnienia użytkownika, może kierować użytkownika do `/authorize` punktu końcowego. To żądanie jest podobne do pierwszego etapu [przepływu kodu autoryzacji OAuth 2,0](v2-oauth2-auth-code-flow.md), z następującymi istotnymi różnicami:

* Żądanie musi zawierać `openid` zakres `scope` w parametrze.
* Parametr `response_type` musi zawierać `id_token`wartość.
* Żądanie musi zawierać `nonce` parametr.

> [!IMPORTANT]
> Aby pomyślnie zażądać tokenu identyfikatora z punktu końcowego/Authorization, Rejestracja aplikacji w [portalu rejestracji](https://portal.azure.com) musi mieć niejawny przydział id_tokens włączony na karcie uwierzytelnianie (która ustawia `oauth2AllowIdTokenImplicitFlow` flagę w [ manifest aplikacji](reference-app-manifest.md) do `true`). Jeśli nie jest włączona, `unsupported_response` zostanie zwrócony błąd: "Podana wartość parametru wejściowego" response_type "nie jest dozwolona dla tego klienta. Oczekiwana wartość to "Code" "

Przykład:

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
> Kliknij następujący link, aby wykonać to żądanie. Po zalogowaniu przeglądarka zostanie przekierowana do `https://localhost/myapp/`folderu z tokenem ID na pasku adresu. Należy zauważyć, że to `response_mode=fragment` żądanie używa (tylko do celów demonstracyjnych). Zalecamy korzystanie `response_mode=form_post`z programu.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize..</a>

| Parametr | Warunek | Opis |
| --- | --- | --- |
| `tenant` | Wymagane | Możesz użyć `{tenant}` wartości w ścieżce żądania, aby kontrolować, kto może zalogować się do aplikacji. Dozwolone wartości to `common`, `organizations`, `consumers`i identyfikator dzierżawy. Aby uzyskać więcej informacji, zobacz temat [podstawowe](active-directory-v2-protocols.md#endpoints)informacje o protokole. |
| `client_id` | Wymagane | **Identyfikator aplikacji (klienta)** , który [Azure Portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) środowisko przypisane do aplikacji. |
| `response_type` | Wymagane | Musi zawierać `id_token` do logowania za OpenID Connect Connect. Może również zawierać inne `response_type` wartości, `code`np. |
| `redirect_uri` | Zalecane | Identyfikator URI przekierowania aplikacji, w którym odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Musi dokładnie pasować do jednego z identyfikatorów URI przekierowania zarejestrowanego w portalu, z tą różnicą, że musi on być zakodowany w adresie URL. Jeśli nie istnieje, punkt końcowy wybiera jeden zarejestrowany parametr redirect_uri losowo, aby wysłać użytkownika z powrotem do. |
| `scope` | Wymagane | Rozdzielana spacjami lista zakresów. W przypadku programu OpenID Connect Connect musi zawierać zakres `openid`, który jest tłumaczy na uprawnienie "Logowanie użytkownika" w interfejsie użytkownika wyrażania zgody. Do żądania zgody można także uwzględnić inne zakresy w tym żądaniu. |
| `nonce` | Wymagane | Wartość uwzględniona w żądaniu wygenerowanym przez aplikację, która zostanie uwzględniona w wyniku id_token jako jako roszczeń. Aplikacja może zweryfikować tę wartość, aby zmniejszyć ataki metodą powtórzeń tokenu. Zwykle jest to losowy, unikatowy ciąg, który może służyć do identyfikowania pochodzenia żądania. |
| `response_mode` | Zalecane | Określa metodę, która ma zostać użyta do wysłania podanego kodu autoryzacji z powrotem do aplikacji. Możliwe wartości to `form_post` i `fragment`. W przypadku aplikacji sieci Web zalecamy korzystanie `response_mode=form_post`z programu w celu zapewnienia najbezpieczniejszego transferu tokenów do aplikacji. |
| `state` | Zalecane | Wartość zawarta w żądaniu, która również zostanie zwrócona w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Losowo generowana unikatowa wartość jest używana w celu [zapobiegania atakom na fałszerstwo żądań między lokacjami](https://tools.ietf.org/html/rfc6749#section-10.12). Ten stan jest również używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takiego jak strona lub widok użytkownika. |
| `prompt` | Optional | Wskazuje typ interakcji użytkownika, która jest wymagana. Jedyne prawidłowe wartości w tym momencie to `login`, `none`, i `consent`. W `prompt=login` ramach tego żądania użytkownik musi wprowadzić swoje poświadczenia, co oznacza negację logowania jednokrotnego. `prompt=none` Jest to przeciwieństwo. To oświadczenia gwarantuje, że użytkownik nie jest wyświetlany z żadnym interakcyjnym monitem. Jeśli żądanie nie może zostać zakończone dyskretnie przy użyciu logowania jednokrotnego, punkt końcowy platformy tożsamości firmy Microsoft zwraca błąd. `prompt=consent` Po zalogowaniu się użytkownika zostaje wyzwolone okno dialogowe zgody na uwierzytelnianie OAuth. Okno dialogowe prosi użytkownika o przyznanie uprawnień aplikacji. |
| `login_hint` | Optional | Tego parametru można użyć do wstępnego wypełnienia pola Nazwa użytkownika i adres e-mail na stronie logowania użytkownika, jeśli znasz nazwę użytkownika przed czasem. Często aplikacje używają tego parametru podczas ponownego uwierzytelniania, po już wyodrębnieniu nazwy użytkownika z wcześniejszego logowania przy użyciu tego `preferred_username` żądania. |
| `domain_hint` | Optional | Obszar użytkownika w katalogu federacyjnym.  Pomija to proces odnajdywania na podstawie poczty e-mail, który użytkownik przechodzi na stronie logowania, aby nieco bardziej usprawnić środowisko użytkownika. W przypadku dzierżawców federacyjnych za pomocą katalogu lokalnego, takiego jak AD FS, często powoduje to bezproblemowe logowanie z powodu istniejącej sesji logowania. |

W tym momencie użytkownik zostanie poproszony o wprowadzenie poświadczeń i zakończenie uwierzytelniania. Punkt końcowy platformy tożsamości firmy Microsoft sprawdza, czy użytkownik wyraził zgodę na uprawnienia wskazane w `scope` parametrze zapytania. Jeśli użytkownik nie wyraził zgody na żadne z tych uprawnień, punkt końcowy platformy tożsamości firmy Microsoft poprosi użytkownika o zgodę na wymagane uprawnienia. Więcej informacji o uprawnieniach [, zgodzie i aplikacjach](v2-permissions-and-consent.md)wielodostępnych można znaleźć w części.

Po uwierzytelnieniu i udzieleniu zgody przez użytkownika punkt końcowy platformy tożsamości firmy Microsoft zwraca odpowiedź do aplikacji na wskazanym identyfikatorze URI przekierowania przy użyciu metody określonej w `response_mode` parametrze.

### <a name="successful-response"></a>Pomyślna odpowiedź

Pomyślna odpowiedź `response_mode=form_post` jest następująca:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametr | Opis |
| --- | --- |
| `id_token` | Token identyfikatora, którego żądała aplikacja. Możesz użyć parametru, `id_token` aby zweryfikować tożsamość użytkownika i rozpocząć sesję z użytkownikiem. Aby uzyskać więcej informacji o tokenach identyfikatorów i ich zawartości, zobacz [ `id_tokens` odwołanie](id-tokens.md). |
| `state` | `state` Jeśli parametr zostanie uwzględniony w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy wartości stanu w żądaniu i odpowiedzi są identyczne. |

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

W poniższej tabeli opisano kody błędów, które mogą być zwracane w `error` parametrze odpowiedzi na błąd:

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

Tylko otrzymanie id_token nie wystarcza do uwierzytelnienia użytkownika; należy sprawdzić poprawność podpisu id_token's i zweryfikować oświadczenia w tokenie zgodnie z wymaganiami aplikacji. Punkt końcowy platformy tożsamości firmy Microsoft korzysta z [tokenów sieci Web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) i kryptografii klucza publicznego w celu podpisania tokenów i sprawdzenia, czy są one prawidłowe.

Można sprawdzić poprawność `id_token` kodu klienta, ale typowe rozwiązanie polega na `id_token` wysłaniu do serwera wewnętrznej bazy danych i zakończeniu weryfikacji. Po sprawdzeniu poprawności podpisu id_token istnieje kilka oświadczeń, które będą wymagane do zweryfikowania. Zobacz odwołanie, aby uzyskać więcej informacji, w tym [Sprawdzanie poprawności tokenów](id-tokens.md#validating-an-id_token) i [ważnych informacji na temat przerzucania klucza podpisywania](active-directory-signing-key-rollover.md). [ `id_token` ](id-tokens.md) Zalecamy korzystanie z biblioteki do analizowania i weryfikowania tokenów — istnieje co najmniej jedna dostępna dla większości języków i platform.

Możesz również chcieć sprawdzić poprawność dodatkowych oświadczeń w zależności od danego scenariusza. Niektóre typowe walidacje obejmują:

* Upewnienie się, że użytkownik/organizacja zarejestrowali się w aplikacji.
* Upewnienie się, że użytkownik ma odpowiednie uprawnienia/autoryzacja
* Upewnienie się, że wystąpiło pewne silne uwierzytelnianie, takie jak uwierzytelnianie wieloskładnikowe.

Po sprawdzeniu poprawności id_token można rozpocząć sesję z użytkownikiem i użyć oświadczeń w id_token, aby uzyskać informacje o użytkowniku w aplikacji. Te informacje mogą służyć do wyświetlania, rekordów, personalizacji itp.

## <a name="send-a-sign-out-request"></a>Wyślij żądanie wylogowania

Gdy chcesz wylogować użytkownika z aplikacji, nie wystarcza do wyczyszczenia plików cookie aplikacji lub zakończenie sesji użytkownika. Musisz również przekierować użytkownika do punktu końcowego platformy tożsamości firmy Microsoft, aby się wylogować. Jeśli tego nie zrobisz, użytkownik ponownie uwierzytelni się w aplikacji bez konieczności ponownego wprowadzania poświadczeń, ponieważ będzie mieć prawidłową sesję logowania jednokrotnego z punktem końcowym platformy tożsamości firmy Microsoft.

Możesz przekierować użytkownika do `end_session_endpoint` listy w dokumencie OpenID Connect Connect Metadata:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parametr | Warunek | Opis |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Zalecane | Adres URL, do którego użytkownik jest przekierowywany po pomyślnym wylogowaniu. Jeśli parametr nie jest uwzględniony, użytkownik zostanie pokazany komunikat ogólny, który jest generowany przez punkt końcowy platformy tożsamości firmy Microsoft. Ten adres URL musi być zgodny z jednym z identyfikatorów URI przekierowania zarejestrowanych dla aplikacji w portalu rejestracji aplikacji. |

## <a name="single-sign-out"></a>Wylogowanie jednokrotne

Po przekierowaniu użytkownika do programu `end_session_endpoint`punkt końcowy platformy tożsamości firmy Microsoft czyści sesję użytkownika z przeglądarki. Jednak użytkownik może nadal być zalogowany do innych aplikacji korzystających z kont Microsoft do uwierzytelniania. Aby umożliwić tym aplikacjom jednoczesne podpisywanie użytkownika, punkt końcowy platformy tożsamości firmy Microsoft wysyła żądanie HTTP GET do zarejestrowanych `LogoutUrl` wszystkich aplikacji, do których użytkownik jest aktualnie zalogowany. Aplikacje muszą odpowiedzieć na to żądanie przez wyczyszczenie każdej sesji identyfikującej użytkownika i zwracającej `200` odpowiedź. Jeśli chcesz obsługiwać Logowanie jednokrotne w aplikacji, musisz zaimplementować takie jak `LogoutUrl` w kodzie aplikacji. Możesz ustawić w `LogoutUrl` portalu rejestracji aplikacji.

## <a name="protocol-diagram-access-token-acquisition"></a>Diagram protokołów: Pozyskiwanie tokenów dostępu

Wiele aplikacji sieci Web musi nie tylko podpisać użytkownika w programie, ale również uzyskać dostęp do usługi sieci Web w imieniu użytkownika przy użyciu protokołu OAuth. Ten scenariusz łączy OpenID Connect Connect for User Authentication, jednocześnie pobierając kod autoryzacji, za pomocą którego można uzyskać tokeny dostępu, jeśli używasz przepływu kodu autoryzacji OAuth.

Pełna rejestracja w usłudze OpenID Connect Connect i przepływ pozyskiwania tokenów wygląda podobnie do następnego diagramu. Szczegółowo opisano każdy krok w następnych sekcjach artykułu.

![Protokół OpenID Connect Connect: Pozyskiwanie tokenu](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

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
> Kliknij następujący link, aby wykonać to żądanie. Po zalogowaniu przeglądarka zostanie przekierowana do `https://localhost/myapp/`, z tokenem ID i kodem na pasku adresu. Należy zauważyć, że to `response_mode=fragment` żądanie służy tylko do celów demonstracyjnych. Zalecamy korzystanie `response_mode=form_post`z programu.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize..</a>

Dzięki dołączeniu zakresów uprawnień w żądaniu oraz za `response_type=id_token code`pomocą, punkt końcowy platformy tożsamości firmy Microsoft gwarantuje, że użytkownik wyraził zgodę na uprawnienia wskazane `scope` w parametrze zapytania. Zwraca kod autoryzacji do aplikacji w celu wymiany tokenu dostępu.

### <a name="successful-response"></a>Pomyślna odpowiedź

Pomyślna odpowiedź z `response_mode=form_post` używania wygląda następująco:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametr | Opis |
| --- | --- |
| `id_token` | Token identyfikatora, którego żądała aplikacja. Możesz użyć tokenu identyfikatora, aby zweryfikować tożsamość użytkownika i rozpocząć sesję z użytkownikiem. Znajdziesz więcej szczegółowych informacji o tokenach identyfikatorów i ich zawartości w [ `id_tokens` odwołaniu](id-tokens.md). |
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
