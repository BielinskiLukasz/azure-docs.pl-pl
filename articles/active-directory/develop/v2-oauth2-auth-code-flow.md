---
title: Microsoft Identity platform oraz przepływ kodu autoryzacji OAuth | Azure
description: Tworzenie aplikacji sieci Web przy użyciu implementacji platformy tożsamości firmy Microsoft w ramach protokołu uwierzytelniania OAuth 2,0.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ae1d7d86-7098-468c-aa32-20df0a10ee3d
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: abdf60d92f51fdb34f36599aa7f4019b2adb8475
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852243"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Przepływ kodu autoryzacji Microsoft Identity platform i OAuth 2,0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Przyznanie kodu autoryzacji OAuth 2,0 może być stosowane w aplikacjach zainstalowanych na urządzeniu w celu uzyskania dostępu do chronionych zasobów, takich jak interfejsy API sieci Web. Korzystając z implementacji Microsoft Identity platform w ramach uwierzytelniania OAuth 2,0, można dodać funkcję logowania i dostęp do interfejsu API do aplikacji mobilnych i klasycznych. Ten przewodnik jest niezależny od języka i zawiera opis sposobu wysyłania i odbierania komunikatów HTTP bez użycia żadnych [bibliotek uwierzytelniania typu open-source platformy Azure](reference-v2-libraries.md).

> [!NOTE]
> Nie wszystkie scenariusze Azure Active Directory & funkcje są obsługiwane przez punkt końcowy platformy tożsamości firmy Microsoft. Aby określić, czy należy używać punktu końcowego platformy tożsamości firmy Microsoft, przeczytaj artykuł [ograniczenia dotyczące platformy tożsamości firmy Microsoft](active-directory-v2-limitations.md).

Przepływ kodu autoryzacji OAuth 2,0 został opisany w [sekcji 4,1 specyfikacji oauth 2,0](https://tools.ietf.org/html/rfc6749). Służy do uwierzytelniania i autoryzacji w większości typów aplikacji, w tym [aplikacji sieci Web](v2-app-types.md#web-apps) i [natywnie zainstalowanych aplikacji](v2-app-types.md#mobile-and-native-apps). Przepływ umożliwia aplikacjom bezpieczne pozyskiwanie access_tokens, które mogą być używane do uzyskiwania dostępu do zasobów zabezpieczonych przez punkt końcowy platformy tożsamości firmy Microsoft.

## <a name="protocol-diagram"></a>Diagram protokołu

Na wysokim poziomie cały przepływ uwierzytelniania dla aplikacji natywnej/mobilnej wygląda następująco:

![Przepływ kodu uwierzytelniania OAuth](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="request-an-authorization-code"></a>Żądanie kodu autoryzacji

Przepływ kodu autoryzacji zaczyna się od klienta kierującego użytkownika do `/authorize` punktu końcowego. W tym żądaniu klient wskazuje uprawnienia wymagane do uzyskania przez użytkownika:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345
```

> [!TIP]
> Kliknij link poniżej, aby wykonać to żądanie. Po zalogowaniu przeglądarka powinna zostać przekierowana do `https://localhost/myapp/` `code` folderu na pasku adresu.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parametr    | Wymagane/opcjonalne | Opis |
|--------------|-------------|--------------|
| `tenant`    | wymagane    | `{tenant}` Wartość w ścieżce żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to `common`, `organizations`, `consumers`i identyfikator dzierżawy. Aby uzyskać więcej informacji, zobacz temat [podstawowe informacje](active-directory-v2-protocols.md#endpoints)o protokole.  |
| `client_id`   | wymagane    | **Identyfikator aplikacji (klienta)** , który [Azure Portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) środowisko przypisane do aplikacji.  |
| `response_type` | wymagane    | Musi zawierać `code` do przepływu kodu autoryzacji.       |
| `redirect_uri`  | wymagane | Redirect_uri aplikacji, gdzie odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Musi dokładnie odpowiadać jednemu z redirect_uris zarejestrowanych w portalu, z wyjątkiem tego, że musi on być zakodowany w adresie URL. W przypadku natywnych aplikacji mobilnych & należy użyć wartości `https://login.microsoftonline.com/common/oauth2/nativeclient`domyślnej.   |
| `scope`  | wymagane    | Rozdzielana spacjami lista [zakresów](v2-permissions-and-consent.md) , do których użytkownik ma wyrazić zgodę.  W przypadku `/authorize` gałęzi żądania może to obejmować wiele zasobów, co pozwala aplikacji na uzyskanie zgody na wiele interfejsów API sieci Web, które mają być wywoływane. |
| `response_mode`   | Rekomendowane | Określa metodę, która ma zostać użyta do wysłania zwróconego tokenu z powrotem do aplikacji. Może to być jeden z następujących elementów:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query`udostępnia kod jako parametr ciągu zapytania w identyfikatorze URI przekierowania. Jeśli żądasz tokenu identyfikatora przy użyciu niejawnego przepływu, nie możesz użyć `query` określonego w [specyfikacji OpenID Connect](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Jeśli żądasz tylko kodu, możesz użyć `query`, `fragment`, lub `form_post`. `form_post`wykonuje wpis zawierający kod dla identyfikatora URI przekierowania. Aby uzyskać więcej informacji, zobacz [OpenID Connect Connect Protocol](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  |
| `state`                 | Rekomendowane | Wartość uwzględniona w żądaniu, która również zostanie zwrócona w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Losowo wygenerowana unikatowa wartość jest zwykle używana w celu [zapobiegania atakom na fałszerstwo żądań między witrynami](https://tools.ietf.org/html/rfc6749#section-10.12). Ta wartość może również kodować informacje o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelniania, takie jak strona lub widok. |
| `prompt`  | opcjonalne    | Wskazuje typ interakcji użytkownika, która jest wymagana. Jedyne prawidłowe wartości w tym momencie to `login`, `none`, i `consent`.<br/><br/>- `prompt=login`wymusić użytkownikowi wprowadzanie poświadczeń na to żądanie, negację logowania jednokrotnego.<br/>- `prompt=none`jest przeciwieństwem do siebie, upewnia się, że użytkownik nie jest wyświetlany z żadnym interaktywnym monitem. Jeśli żądanie nie może zostać zakończone dyskretnie przy użyciu logowania jednokrotnego, punkt końcowy platformy tożsamości firmy Microsoft zwróci `interaction_required` błąd.<br/>- `prompt=consent`spowoduje wyzwolenie okna dialogowego zgody na uwierzytelnianie OAuth po zalogowaniu się użytkownika, z prośbą o udzielenie uprawnień do aplikacji. |
| `login_hint`  | opcjonalne    | Może służyć do wstępnego wypełniania pola Nazwa użytkownika/adres e-mail strony logowania dla użytkownika, jeśli znana jest jego nazwa użytkownika przed czasem. Często aplikacje będą używać tego parametru podczas ponownego uwierzytelniania, ponieważ już wyodrębnili nazwę użytkownika z poprzedniego logowania przy użyciu tego `preferred_username` żądania.   |
| `domain_hint`  | opcjonalne    | Może być jedną z `consumers` lub `organizations`.<br/><br/>W przypadku pominięcia zostanie pominięty proces odnajdywania na podstawie poczty e-mail, który użytkownik przejdzie na stronie logowania, co prowadzi do nieco bardziej usprawnionego środowiska użytkownika. Często aplikacje będą używać tego parametru podczas ponownego uwierzytelniania przez wyodrębnienie `tid` z wcześniejszego logowania. Jeśli wartość jest `9188040d-6c67-4c5b-b112-36a304b66dad`równa, należy użyć `domain_hint=consumers`. `tid` W przeciwnym razie `domain_hint=organizations`Użyj.  |
| `code_challenge_method` | opcjonalne    | Metoda używana do kodowania `code_verifier` `code_challenge` parametru. Może być jedną z następujących wartości:<br/><br/>- `plain` <br/>- `S256`<br/><br/>Jeśli jest wykluczony, przyjmuje się, że jest `code_challenge` to zwykły tekst, `code_challenge` jeśli jest uwzględniony. Platforma tożsamości firmy Microsoft obsługuje `plain` programy `S256`i. Aby uzyskać więcej informacji, zobacz [dokument RFC PKCE](https://tools.ietf.org/html/rfc7636). |
| `code_challenge`  | opcjonalne | Używane do zabezpieczania kodu autoryzacji za pośrednictwem klucza testowego dla wymiany kodu (PKCE) z klienta natywnego. Wymagane, `code_challenge_method` jeśli jest dołączony. Aby uzyskać więcej informacji, zobacz [dokument RFC PKCE](https://tools.ietf.org/html/rfc7636). |

W tym momencie użytkownik zostanie poproszony o wprowadzenie poświadczeń i zakończenie uwierzytelniania. Punkt końcowy platformy tożsamości firmy Microsoft sprawdzi również, czy użytkownik wyraził zgodę na uprawnienia wskazane w `scope` parametrze zapytania. Jeśli użytkownik nie wyraził zgody na żadne z tych uprawnień, poprosił użytkownika o zgodę na wymagane uprawnienia. Szczegółowe informacje o [uprawnieniach, zgodzie i aplikacjach wielodostępnych są dostępne tutaj](v2-permissions-and-consent.md).

Gdy użytkownik uwierzytelni się i udzieli zgody, punkt końcowy platformy tożsamości firmy Microsoft zwróci odpowiedź do aplikacji we wskazanym `redirect_uri`miejscu przy użyciu metody określonej `response_mode` w parametrze.

#### <a name="successful-response"></a>Pomyślna odpowiedź

Pomyślna odpowiedź `response_mode=query` przy użyciu wygląda następująco:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parametr | Opis  |
|-----------|--------------|
| `code` | Authorization_code, którego żądała aplikacja. Aplikacja może użyć kodu autoryzacji, aby zażądać tokenu dostępu dla zasobu docelowego. Authorization_codes są krótkotrwałe, zazwyczaj wygasają po około 10 minutach. |
| `state` | Jeśli w żądaniu zostanie uwzględniony parametr stanu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy wartości stanu w żądaniu i odpowiedzi są identyczne. |

#### <a name="error-response"></a>Odpowiedź na błąd

Odpowiedzi na błędy mogą być również wysyłane do `redirect_uri` , aby aplikacja mogła je odpowiednio obsłużyć:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis  |
|----------|------------------|
| `error`  | Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i mogą być używane do reagowania na błędy. |
| `error_description` | Konkretny komunikat o błędzie, który może ułatwić deweloperom zidentyfikowanie głównej przyczyny błędu uwierzytelniania. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Kody błędów dla błędów punktu końcowego autoryzacji

W poniższej tabeli opisano różne kody błędów, które mogą być zwracane w `error` parametrze odpowiedzi na błąd.

| Kod błędu  | Opis    | Akcja klienta   |
|-------------|----------------|-----------------|
| `invalid_request` | Błąd protokołu, taki jak brak wymaganego parametru. | Napraw i ponownie prześlij żądanie. Jest to błąd programistyczny zwykle przechwycony podczas wstępnego testowania. |
| `unauthorized_client` | Aplikacja kliencka nie może zażądać kodu autoryzacji. | Ten błąd występuje zazwyczaj, gdy aplikacja kliencka nie jest zarejestrowana w usłudze Azure AD lub nie została dodana do dzierżawy usługi Azure AD użytkownika. Aplikacja może monitować użytkownika z instrukcją dotyczącą instalowania aplikacji i dodawania jej do usługi Azure AD. |
| `access_denied`  | Niedozwolona zgoda właściciela zasobu  | Aplikacja kliencka może powiadomić użytkownika, że nie może wykonać tej czynności, chyba że użytkownik wyraził zgodę. |
| `unsupported_response_type` | Serwer autoryzacji nie obsługuje typu odpowiedzi w żądaniu. | Napraw i ponownie prześlij żądanie. Jest to błąd programistyczny zwykle przechwycony podczas wstępnego testowania.  |
| `server_error`  | Serwer napotkał nieoczekiwany błąd.| Ponów żądanie. Te błędy mogą wynikać z warunków tymczasowych. Aplikacja kliencka może wyjaśnić użytkownikowi, że odpowiedź jest opóźniona z błędem tymczasowym. |
| `temporarily_unavailable`   | Serwer jest tymczasowo zbyt zajęty, aby obsłużyć żądanie. | Ponów żądanie. Aplikacja kliencka może wyjaśnić użytkownikowi, że jego odpowiedź jest opóźniona ze względu na tymczasowy warunek. |
| `invalid_resource`  | Zasób docelowy jest nieprawidłowy, ponieważ nie istnieje, usługa Azure AD nie może go odnaleźć lub nie jest poprawnie skonfigurowana. | Ten błąd wskazuje, że zasób (jeśli istnieje) nie został skonfigurowany w dzierżawie. Aplikacja może monitować użytkownika z instrukcją dotyczącą instalowania aplikacji i dodawania jej do usługi Azure AD. |
| `login_required` | Zbyt wiele lub nie znaleziono użytkowników | Klient zażądał uwierzytelniania dyskretnego (`prompt=none`), ale nie można odnaleźć pojedynczego użytkownika. Może to oznaczać, że wielu użytkowników jest aktywnych w sesji lub nie ma żadnych użytkowników. Uwzględnia to wybraną dzierżawę (na przykład jeśli istnieją dwa aktywne konta usługi Azure AD i jeden konto Microsoft i `consumers` zostanie wybrana opcja uwierzytelnianie dyskretne zostanie wykonane). |
| `interaction_required` | Żądanie wymaga interakcji z użytkownikiem. | Wymagany jest dodatkowy krok uwierzytelniania lub zgoda. Ponów żądanie bez `prompt=none`. |

## <a name="request-an-access-token"></a>Żądanie tokenu dostępu

Teraz, gdy pozyskasz authorization_code i przyznano użytkownikowi uprawnienia, możesz zrealizować `code` `access_token` dla danego zasobu. W tym celu należy wysłać `POST` żądanie `/token` do punktu końcowego:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [!TIP]
> Spróbuj wykonać to żądanie w programie Poster! (Nie zapomnij zastąpić `code`) [ ![spróbuj uruchomić to żądanie w programie Poster](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| Parametr  | Wymagane/opcjonalne | Opis     |
|------------|-------------------|----------------|
| `tenant`   | wymagane   | `{tenant}` Wartość w ścieżce żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to `common`, `organizations`, `consumers`i identyfikator dzierżawy. Aby uzyskać więcej informacji, zobacz temat [podstawowe informacje](active-directory-v2-protocols.md#endpoints)o protokole.  |
| `client_id` | wymagane  | Identyfikator aplikacji (klienta), którą strona [Rejestracje aplikacji Azure Portala —](https://go.microsoft.com/fwlink/?linkid=2083908) jest przypisana do aplikacji. |
| `grant_type` | wymagane   | Musi być `authorization_code` dla przepływu kodu autoryzacji.   |
| `scope`      | wymagane   | Rozdzielana spacjami lista zakresów. Zakresy żądane w tym etapie muszą być równoważne lub podzbiorem zakresów żądanych w pierwszym etapie. Wszystkie zakresy muszą pochodzić z pojedynczego zasobu, wraz z zakresami OIDC (`profile`, `openid`, `email`). Aby uzyskać bardziej szczegółowy opis zakresów, zapoznaj się z [uprawnieniami, zgodą i zakresami](v2-permissions-and-consent.md). |
| `code`          | wymagane  | Authorization_code pobrana w pierwszym etapie przepływu. |
| `redirect_uri`  | wymagane  | Ta sama wartość redirect_uri, która została użyta w celu uzyskania authorization_code. |
| `client_secret` | wymagane dla aplikacji sieci Web | Wpis tajny aplikacji utworzony w portalu rejestracji aplikacji dla aplikacji. Nie należy używać wpisu tajnego aplikacji w aplikacji natywnej, ponieważ client_secrets nie może być niezawodnie przechowywana na urządzeniach. Jest to wymagane w przypadku aplikacji sieci Web i interfejsów API sieci Web, co umożliwia bezpieczne przechowywanie client_secret po stronie serwera.  Wpis tajny klienta musi być zakodowany przy użyciu adresu URL przed wysłaniem.  |
| `code_verifier` | opcjonalne  | Ten sam code_verifier, który został użyty w celu uzyskania authorization_code. Wymagane, jeśli w żądaniu udzielenia uprawnienia do kodu autoryzacji użyto PKCE. Aby uzyskać więcej informacji, zobacz [dokument RFC PKCE](https://tools.ietf.org/html/rfc7636). |

### <a name="successful-response"></a>Pomyślna odpowiedź

Pomyślna odpowiedź dotycząca tokenu będzie wyglądać następująco:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fuser.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Parametr     | Opis   |
|---------------|------------------------------|
| `access_token`  | Żądany token dostępu. Aplikacja może używać tego tokenu do uwierzytelniania w zabezpieczonym zasobie, takim jak internetowy interfejs API.  |
| `token_type`    | Wskazuje wartość typu tokenu. Jedynym typem obsługiwanym przez usługę Azure AD jest znak |
| `expires_in`    | Jak długo token dostępu jest prawidłowy (w sekundach). |
| `scope`         | Zakresy, dla których access_token jest prawidłowy. |
| `refresh_token` | Token odświeżania OAuth 2,0. Aplikacja może używać tego tokenu, uzyskując dodatkowe tokeny dostępu po wygaśnięciu bieżącego tokenu dostępu. Refresh_tokens są długotrwałe i mogą być używane w celu zachowania dostępu do zasobów przez dłuższy czas. Aby uzyskać więcej informacji na temat odświeżania tokenu dostępu, zapoznaj się z [sekcją poniżej](#refresh-the-access-token). <br> **Uwaga:** Dostępne tylko wtedy `offline_access` , gdy żądany został zakres. |
| `id_token`      | Token sieci Web JSON (JWT). Aplikacja może zdekodować segmenty tego tokenu, aby zażądać informacji o użytkowniku, który się zalogował. Aplikacja może buforować wartości i wyświetlać je, ale nie należy polegać na nich w przypadku jakichkolwiek ograniczeń autoryzacji lub zabezpieczeń. Aby uzyskać więcej informacji na temat id_tokens, [`id_token reference`](id-tokens.md)Zobacz. <br> **Uwaga:** Dostępne tylko wtedy `openid` , gdy żądany został zakres. |

### <a name="error-response"></a>Odpowiedź na błąd

Odpowiedzi na błędy będą wyglądać następująco:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametr         | Opis    |
|-------------------|----------------|
| `error`       | Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i mogą być używane do reagowania na błędy. |
| `error_description` | Konkretny komunikat o błędzie, który może ułatwić deweloperom zidentyfikowanie głównej przyczyny błędu uwierzytelniania. |
| `error_codes` | Lista kodów błędów specyficznych dla usługi STS, które mogą pomóc w diagnostyce.  |
| `timestamp`   | Godzina wystąpienia błędu. |
| `trace_id`    | Unikatowy identyfikator żądania, które może pomóc w diagnostyce. |
| `correlation_id` | Unikatowy identyfikator żądania, które może pomóc w diagnostyce między składnikami. |

### <a name="error-codes-for-token-endpoint-errors"></a>Kody błędów dla błędów punktu końcowego tokenu

| Kod błędu         | Opis        | Akcja klienta    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Błąd protokołu, taki jak brak wymaganego parametru. | Napraw i ponownie prześlij żądanie   |
| `invalid_grant`    | Kod autoryzacji lub weryfikator kodu PKCE jest nieprawidłowy lub wygasł. | Wypróbuj nowe żądanie do `/authorize` punktu końcowego i sprawdź, czy parametr code_verifier jest poprawny.  |
| `unauthorized_client` | Uwierzytelniony klient nie ma autoryzacji do korzystania z tego typu udzielania autoryzacji. | Zwykle dzieje się tak, gdy aplikacja kliencka nie jest zarejestrowana w usłudze Azure AD lub nie została dodana do dzierżawy usługi Azure AD użytkownika. Aplikacja może monitować użytkownika z instrukcją dotyczącą instalowania aplikacji i dodawania jej do usługi Azure AD. |
| `invalid_client` | Uwierzytelnianie klienta nie powiodło się.  | Poświadczenia klienta są nieprawidłowe. Aby rozwiązać ten problem, administrator aplikacji aktualizuje poświadczenia.   |
| `unsupported_grant_type` | Serwer autoryzacji nie obsługuje typu przydzielenia autoryzacji. | Zmień typ dotacji w żądaniu. Ten typ błędu powinien wystąpić tylko podczas opracowywania i być wykrywany podczas wstępnego testowania. |
| `invalid_resource` | Zasób docelowy jest nieprawidłowy, ponieważ nie istnieje, usługa Azure AD nie może go odnaleźć lub nie jest poprawnie skonfigurowana. | Wskazuje to, że zasób, jeśli istnieje, nie został skonfigurowany w dzierżawie. Aplikacja może monitować użytkownika z instrukcją dotyczącą instalowania aplikacji i dodawania jej do usługi Azure AD.  |
| `interaction_required` | Żądanie wymaga interakcji z użytkownikiem. Na przykład wymagany jest dodatkowy krok uwierzytelniania. | Spróbuj ponownie wykonać żądanie przy użyciu tego samego zasobu.  |
| `temporarily_unavailable` | Serwer jest tymczasowo zbyt zajęty, aby obsłużyć żądanie. | Ponów żądanie. Aplikacja kliencka może wyjaśnić użytkownikowi, że jego odpowiedź jest opóźniona ze względu na tymczasowy warunek. |

## <a name="use-the-access-token"></a>Korzystanie z tokenu dostępu

Teraz `access_token`, po pomyślnym pobraniu, możesz użyć tokenu w żądaniach do interfejsów API sieci Web, dołączając je `Authorization` do nagłówka:

> [!TIP]
> Wykonaj to żądanie w programie Poster! (Najpierw Zastąp `Authorization` nagłówek) [ ![próba uruchomienia tego żądania w programie Poster](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Odświeżanie tokenu dostępu

Access_tokens są krótkotrwałe i należy je odświeżyć po wygaśnięciu, aby nadal uzyskiwać dostęp do zasobów. Można to zrobić przez przesłanie kolejnego `POST` żądania `/token` do punktu końcowego `refresh_token` , tym razem zamiast `code`.  Tokeny odświeżania są prawidłowe dla wszystkich uprawnień, do których klient otrzymał już zgodę — w tym celu token odświeżania wystawiony przez `scope=mail.read` żądanie może zostać użyty do zażądania nowego tokenu dostępu `scope=api://contoso.com/api/UseResource`dla.  

Tokeny odświeżania nie mają określonych okresów istnienia. Zwykle okresy istnienia tokenów odświeżania są stosunkowo długie. Jednak w niektórych przypadkach tokeny odświeżania wygasną, są odwoływane lub nie ma wystarczających uprawnień do żądanej akcji. Aplikacja musi oczekiwać i obsłużyć [błędy zwrócone przez punkt końcowy](#error-codes-for-token-endpoint-errors) wystawiania tokenów. 

Chociaż tokeny odświeżania nie są odwoływane, gdy są używane do uzyskiwania nowych tokenów dostępu, oczekuje się, że stary token odświeżania zostanie odrzucony. [Specyfikacja OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-6) brzmi: "Serwer autoryzacji może wydać nowy token odświeżania, w takim przypadku klient musi odrzucić stary token odświeżania i zastąpić go nowym tokenem odświeżania. Serwer autoryzacji może odwołać stary token odświeżania po wydaniu nowego tokenu odświeżania do klienta ".  

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps
```

> [!TIP]
> Spróbuj wykonać to żądanie w programie Poster! (Nie zapomnij zastąpić `refresh_token`) [ ![spróbuj uruchomić to żądanie w programie Poster](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
> 

| Parametr     |                | Opis        |
|---------------|----------------|--------------------|
| `tenant`        | wymagane     | `{tenant}` Wartość w ścieżce żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to `common`, `organizations`, `consumers`i identyfikator dzierżawy. Aby uzyskać więcej informacji, zobacz temat [podstawowe informacje](active-directory-v2-protocols.md#endpoints)o protokole.   |
| `client_id`     | wymagane    | **Identyfikator aplikacji (klienta)** , który [Azure Portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) środowisko przypisane do aplikacji. |
| `grant_type`    | wymagane    | Musi być `refresh_token` dla tego odcinka przepływu kodu autoryzacji. |
| `scope`         | wymagane    | Rozdzielana spacjami lista zakresów. Zakresy żądane w tym etapie muszą być równoważne lub podzbiorem zakresów żądanych w pierwotnym etapie żądania authorization_code. Jeśli zakresy określone w tym żądaniu obejmują wiele serwerów zasobów, punkt końcowy platformy tożsamości firmy Microsoft zwróci token dla zasobu określonego w pierwszym zakresie. Aby uzyskać bardziej szczegółowy opis zakresów, zapoznaj się z [uprawnieniami, zgodą i zakresami](v2-permissions-and-consent.md). |
| `refresh_token` | wymagane    | Składnik refresh_token uzyskany w drugim etapie przepływu. |
| `client_secret` | wymagane dla aplikacji sieci Web | Wpis tajny aplikacji utworzony w portalu rejestracji aplikacji dla aplikacji. Nie powinna być używana w aplikacji natywnej, ponieważ client_secrets nie może być niezawodnie przechowywana na urządzeniach. Jest to wymagane w przypadku aplikacji sieci Web i interfejsów API sieci Web, co umożliwia bezpieczne przechowywanie client_secret po stronie serwera. |

#### <a name="successful-response"></a>Pomyślna odpowiedź

Pomyślna odpowiedź dotycząca tokenu będzie wyglądać następująco:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fuser.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parametr     | Opis         |
|---------------|-------------------------------------------------------------|
| `access_token`  | Żądany token dostępu. Aplikacja może używać tego tokenu do uwierzytelniania w zabezpieczonym zasobie, takim jak internetowy interfejs API. |
| `token_type`    | Wskazuje wartość typu tokenu. Jedynym typem obsługiwanym przez usługę Azure AD jest znak |
| `expires_in`    | Jak długo token dostępu jest prawidłowy (w sekundach).   |
| `scope`         | Zakresy, dla których access_token jest prawidłowy.    |
| `refresh_token` | Nowy token odświeżania protokołu OAuth 2,0. Należy zastąpić stary token odświeżania nowym, pobranym tokenem odświeżania, aby upewnić się, że tokeny odświeżania pozostają prawidłowe, tak długo, jak to możliwe. <br> **Uwaga:** Dostępne tylko wtedy `offline_access` , gdy żądany został zakres.|
| `id_token`      | Niepodpisany token sieci Web JSON (JWT). Aplikacja może zdekodować segmenty tego tokenu, aby zażądać informacji o użytkowniku, który się zalogował. Aplikacja może buforować wartości i wyświetlać je, ale nie należy polegać na nich w przypadku jakichkolwiek ograniczeń autoryzacji lub zabezpieczeń. Aby uzyskać więcej informacji na temat id_tokens, [`id_token reference`](id-tokens.md)Zobacz. <br> **Uwaga:** Dostępne tylko wtedy `openid` , gdy żądany został zakres. |

#### <a name="error-response"></a>Odpowiedź na błąd

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametr         | Opis                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i mogą być używane do reagowania na błędy. |
| `error_description` | Konkretny komunikat o błędzie, który może ułatwić deweloperom zidentyfikowanie głównej przyczyny błędu uwierzytelniania.           |
| `error_codes` |Lista kodów błędów specyficznych dla usługi STS, które mogą pomóc w diagnostyce. |
| `timestamp` | Godzina wystąpienia błędu. |
| `trace_id` | Unikatowy identyfikator żądania, które może pomóc w diagnostyce. |
| `correlation_id` | Unikatowy identyfikator żądania, które może pomóc w diagnostyce między składnikami. |

Opis kodów błędów i zalecanej akcji klienta można znaleźć w temacie [kody błędów dla błędów punktu końcowego tokenu](#error-codes-for-token-endpoint-errors).
