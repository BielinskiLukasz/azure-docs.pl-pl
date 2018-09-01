---
title: Aplikacje jednej strony, przy użyciu niejawny przepływ w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć aplikacje jednostronicowe bezpośrednio przy użyciu niejawnego przepływu OAuth 2.0 usługa Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b00eb1b2d25187dc50be53425ebae347edde33b4
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344815"
---
# <a name="azure-ad-b2c-single-page-app-sign-in-by-using-oauth-20-implicit-flow"></a>Usługa Azure AD B2C: Jednej strony aplikacji zaloguj się przy użyciu niejawnego przepływu OAuth 2.0

Wiele nowoczesnych aplikacji ma aplikacja jednostronicowa fronton, który jest napisany głównie w języku JavaScript. Często przy użyciu struktury, takich jak AngularJS, Ember.js lub Durandal napisano aplikację. Aplikacje jednostronicowe i innych aplikacji JavaScript, które działają głównie w przeglądarce mają pewne dodatkowe problemy dotyczące uwierzytelniania:

* Właściwości zabezpieczeń te aplikacje są znacznie różnią się od tradycyjnych oparte na serwerze sieci web aplikacji.
* Wiele serwerów autoryzacji i dostawców tożsamości nie obsługują współużytkowanie zasobów (między źródłami CORS) żądania udostępniania.
* Przekierowuje przeglądarki postać od aplikacji może być znacznie inwazyjne do środowiska użytkownika.

Aby umożliwić obsługę tych aplikacji usługi Azure Active Directory B2C (Azure AD B2C) używa niejawnego przepływu OAuth 2.0. Przepływ przyznawanie niejawne autoryzacji OAuth 2.0 opisano w [sekcji 4.2 specyfikacji protokołu OAuth 2.0](http://tools.ietf.org/html/rfc6749). W niejawny przepływ aplikacja odbiera tokenów, bezpośrednio z usługi Azure Active Directory (Azure AD) autoryzować punktu końcowego, bez żadnych serwer do serwera programu exchange. Wszystkie logiki uwierzytelniania i obsługi przyjmuje sesji należy umieścić w całości klienta JavaScript, bez dodatkowych Strona przekierowania.

Usługa Azure AD B2C rozszerza standardowe niejawnego przepływu OAuth 2.0 do więcej niż proste uwierzytelnianie i autoryzacja. Usługa Azure AD B2C wprowadza [parametru zasad](active-directory-b2c-reference-policies.md). Za pomocą parametru zasad można użyć protokołu OAuth 2.0 do dodania środowisk użytkowników do aplikacji, takich jak rejestracji, logowania i zarządzania profilami. W tym artykule pokazujemy, jak na potrzeby wdrażania każdego z tych środowisk w aplikacji jednostronicowej niejawny przepływ i Azure AD. Aby pomóc Ci rozpocząć pracę, zapoznaj się z naszym [Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi) i [programu Microsoft .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi) przykładów.

W żądaniach przykład HTTP, w tym artykule, stosujemy nasze przykładowe katalogu usługi Azure AD B2C, **fabrikamb2c.onmicrosoft.com**. Możemy również użyć naszych przykładowych aplikacji i zasad. Możesz spróbować żądania samodzielnie za pomocą tych wartości, lub można je zastąpić własnymi wartościami.
Dowiedz się, jak [własnego katalogu usługi Azure AD B2C, aplikacji i zasad](#use-your-own-b2c-tenant).


## <a name="protocol-diagram"></a>Diagram protokołu

Niejawny przepływ logowania wygląda podobnie jak na poniższej ilustracji. Każdy krok jest opisane szczegółowo w dalszej części tego artykułu.

![OpenID Connect torów](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Wysyłanie żądań uwierzytelniania
W przypadku aplikacji sieci web wymaga uwierzytelnienia użytkownika i wykonywanie zasad, kieruje go użytkownikowi `/authorize` punktu końcowego. Jest to interaktywne części przepływu, gdy użytkownik wykona akcję, zależnie od zasad. Użytkownik uzyskuje identyfikator tokenu z punktu końcowego usługi Azure AD.

W tym żądaniu określa klienta w programie `scope` parametru uprawnienia, które należy uzyskać od użytkownika. W `p` parametru, oznacza to, zasady do wykonania. Poniższe trzy przykłady (z podziałów wiersza, aby zwiększyć czytelność) każdego użyć różnych zasad. Aby uzyskać pewne pojęcie dla działania każdego żądania, spróbuj wklejanie żądania do przeglądarki i uruchomiania go.

### <a name="use-a-sign-in-policy"></a>Użyj zasad logowania
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-policy"></a>Użyj zasad rejestracji
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-policy"></a>Użyj zasad edycji profilu
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parametr | Wymagany? | Opis |
| --- | --- | --- |
| client_id |Wymagane |Identyfikator aplikacji przypisany do aplikacji w [witryny Azure portal](https://portal.azure.com). |
| response_type |Wymagane |Musi zawierać `id_token` dla logowania OpenID Connect. To również obejmować opcję Typ odpowiedzi `token`. Jeśli używasz `token`, aplikacja może bezpośrednio odbierać token dostępu z punktu końcowego autoryzacji, bez konieczności szukania drugie żądanie do punktu końcowego autoryzacji.  Jeśli używasz `token` typ odpowiedzi `scope` parametr musi zawierać zakres, który wskazuje, którego zasobu należy wystawić token dla. |
| redirect_uri |Zalecane |Przekierowania URI aplikacji, gdzie odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Jego musi dokładnie odpowiadać jeden przekierowania identyfikatory URI, który został zarejestrowany w portalu, z tą różnicą, że musi być zakodowane w adresie URL. |
| response_mode |Zalecane |Określa metodę używaną do wysyłania wynikowy token wstecz do swojej aplikacji.  Niejawne przepływów, można użyć `fragment`. |
| zakres |Wymagane |Rozdzielonej spacjami listy zakresów. Wskazuje wartość pojedynczy zakres do usługi Azure AD zarówno uprawnień, które są żądane. `openid` Zakresu wskazuje uprawnienia, aby zalogować użytkownika i pobieranie danych o użytkowniku w formie tokenów Identyfikatora. (Omówimy to w bardziej w dalszej części tego artykułu.) `offline_access` Zakres jest opcjonalny w przypadku aplikacji sieci web. Oznacza to, że Twoja aplikacja wymaga tokenu odświeżania długotrwałe dostępu do zasobów. |
| stan |Zalecane |Wartość uwzględnione w żądaniu, który jest zwracany w odpowiedzi tokenu. Może być ciągiem żadnej zawartości, którego chcesz używać. Zazwyczaj wartość losowo generowany, unikatowy służy, aby zapobiec atakom fałszowanie żądań między witrynami. Stan jest również używane do kodowania informacje o stanie użytkownika w aplikacji, zanim żądanie uwierzytelniania wystąpił, takich jak strony znajdowały się w. |
| Identyfikator jednorazowy |Wymagane |Wartość uwzględnione w żądaniu (generowany przez aplikację), który znajduje się w jego identyfikator tokenu jako oświadczenia. Aplikacja może zweryfikować tę wartość, aby uniknąć powtarzania tokenu ataków. Zazwyczaj wartość jest losowego, unikatowy ciąg, który może służyć do identyfikowania pochodzenia żądania. |
| p |Wymagane |Zasady do wykonania. Jest to nazwa, zasad, który jest tworzony w dzierżawie usługi Azure AD B2C. Wartość nazwy zasad powinien zaczynać się od **b2c\_1\_**. Aby uzyskać więcej informacji, zobacz [wbudowane zasady usługi Azure AD B2C](active-directory-b2c-reference-policies.md). |
| wiersz |Opcjonalne |Typ interakcji z użytkownikiem, który jest wymagany. Obecnie jest jedyną prawidłową wartością `login`. Wymusza użytkownika o wprowadzenie poświadczeń dla tego żądania. Logowanie jednokrotne nie zostanie zastosowana. |

W tym momencie użytkownik jest monitowany do ukończenia przepływu pracy zasad. Może to obejmować użytkownika wprowadzania nazwy użytkownika i hasła, logowanie się przy użyciu tożsamości społecznościowej, założeniem katalogu lub dowolną liczbę kroków. Akcje użytkownika, zależą od tego, jak jest zdefiniowany zasad.

Po wypełnieniu zasad przez użytkownika, usługi Azure AD, zwraca odpowiedź do aplikacji na wartość używana przez `redirect_uri`. Używa metody podanej w `response_mode` parametru. Odpowiedź jest dokładnie taka sama dla wszystkich scenariuszy akcji użytkownika, niezależnie od zasady, które zostało wykonane.

### <a name="successful-response"></a>Odpowiedź oznaczająca Powodzenie
Odpowiedź oznaczająca Powodzenie, który używa `response_mode=fragment` i `response_type=id_token+token` wygląda podobnie do następujących, podziałami wierszy w celu uzyskania czytelności:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| Parametr | Opis |
| --- | --- |
| access_token |Token dostępu, który zażądał aplikacji.  Nie należy zdekodowany ani w przeciwnym razie sprawdził tokenu dostępu. Mogą być traktowane jako nieprzezroczysty ciąg. |
| token_type |Wartość atrybutu typ tokenu. Jedynym typem, który obsługuje usługi Azure AD jest elementu nośnego. |
| expires_in |Długość czasu, przez jaki token dostępu jest prawidłowy (w sekundach). |
| zakres |Token jest prawidłowy dla zakresów. Również zakresów można użyć do tokenów pamięci podręcznej do późniejszego użycia. |
| id_token |Identyfikator tokenu, który zażądał aplikacji. Identyfikator tokenu można użyć do zweryfikowania tożsamości użytkownika i rozpocząć sesję z użytkownikiem. Aby uzyskać więcej informacji o identyfikatorze tokeny i ich zawartość, zobacz [odwołania do tokenu usługi Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| stan |Jeśli `state` parametru jest uwzględnione w żądaniu, tę samą wartość powinna zostać wyświetlona w odpowiedzi. Aplikacja powinna upewnij się, że `state` wartości żądania i odpowiedzi są identyczne. |

### <a name="error-response"></a>Odpowiedzi na błąd
Odpowiedzi na błędy również mogą być wysyłane do identyfikatora URI przekierowania, dzięki czemu aplikacja może je odpowiednio obsługiwać:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Opis |
| --- | --- |
| błąd |Służy do klasyfikowania rodzajów błędów występujących ciągu kodu błędu. Kod błędu: Możesz również użyć do obsługi błędów. |
| error_description |Komunikat błędu, który pomoże Ci identyfikować przyczyny błędu uwierzytelniania. |
| stan |Zobacz pełny opis w powyższej tabeli. Jeśli `state` parametru jest uwzględnione w żądaniu, tę samą wartość powinna zostać wyświetlona w odpowiedzi. Aplikacja powinna upewnij się, że `state` wartości żądania i odpowiedzi są identyczne.|

## <a name="validate-the-id-token"></a>Sprawdzanie poprawności tokenu Identyfikacyjnego
Odbieranie tokenu Identyfikacyjnego nie wystarcza do uwierzytelnienia użytkownika. Należy sprawdzić poprawności podpisu tokenu Identyfikacyjnego i weryfikować oświadczenia w tokenie na wymagania dotyczące Twojej aplikacji. Usługa Azure AD B2C używa [tokenów sieci Web JSON (tokenów Jwt)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) i kryptografii klucza publicznego do podpisywania tokenów i sprawdź, czy są prawidłowe.

Wiele bibliotek typu open source są dostępne do weryfikacji tokenów Jwt, w zależności od języka, którego chcesz użyć. Należy wziąć pod uwagę Eksplorowanie dostępnych bibliotek typu open source, a nie Implementowanie logiki walidacji. Aby uzyskać więcej informacji, jak prawidłowo używać tych bibliotek znajdziesz informacje w tym artykule.

Usługa Azure AD B2C ma punkt końcowy metadanych OpenID Connect. Aplikacja może używać punktu końcowego można pobrać informacji na temat usługi Azure AD B2C w środowisku uruchomieniowym. Informacje te obejmują punkty końcowe, zawartość tokenów i tokenów kluczy podpisywania. Brak dokumentu metadanych JSON dla poszczególnych zasad w dzierżawie usługi Azure AD B2C. Na przykład dokument metadanych dla zasad b2c_1_sign_in w dzierżawie fabrikamb2c.onmicrosoft.com znajduje się w:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Jedna z właściwości tego dokumentu konfiguracji jest `jwks_uri`. Wartość dla tych samych zasad będzie:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

Aby określić zasady, które zostało użyte do podpisywania tokenu Identyfikacyjnego (i gdzie można pobrać metadanych z), masz dwie opcje. Po pierwsze, nazwa zasad jest dołączona do `acr` oświadczenia w `id_token`. Aby dowiedzieć się, jak analizować oświadczeń z tokenu Identyfikatora, zobacz [odwołania do tokenu usługi Azure AD B2C](active-directory-b2c-reference-tokens.md). Drugą opcją jest do zakodowania zasad w wartości `state` parametru w przypadku wysyłania żądania. Następnie dekodowania `state` parametru, aby określić zasady, które zostało użyte. Każda z tych metod jest prawidłowy.

Po nabyciu dokumentu metadanych z punktu końcowego metadanych OpenID Connect, służy klucze publiczne RSA-256 (znajdujący się w tym punkcie końcowym) można zweryfikować podpisu tokenu Identyfikacyjnego. Może istnieć wiele kluczy wymienione w tym punkcie końcowym w dowolnym momencie, identyfikowanych przez `kid`. Nagłówek `id_token` zawiera także `kid` oświadczenia. Wskazuje, które z tych kluczy był używany do podpisywania tokenu Identyfikacyjnego. Aby uzyskać więcej informacji, w tym zapoznać się z platformą [sprawdzanie poprawności tokenów](active-directory-b2c-reference-tokens.md#token-validation), zobacz [odwołania do tokenu usługi Azure AD B2C](active-directory-b2c-reference-tokens.md).
<!--TODO: Improve the information on this-->

Po sprawdzeniu podpis tokenu Identyfikacyjnego kilka oświadczeń wymagają weryfikacji. Na przykład:

* Sprawdź poprawność `nonce` oświadczenia zapobiec atakom powtarzania tokenu. Wartość powinna być określona w żądaniu logowania.
* Sprawdź poprawność `aud` oświadczenia upewnić się, czy token identyfikator został wystawiony dla aplikacji. Jej wartość powinna być identyfikator aplikacji dla swojej aplikacji.
* Sprawdź poprawność `iat` i `exp` oświadczeń upewnić się, że tokenu Identyfikacyjnego nie wygasł.

Kilka więcej operacji sprawdzania poprawności, które należy wykonać są szczegółowo opisane w [OpenID Connect podstawowej specyfikacji](http://openid.net/specs/openid-connect-core-1_0.html). Można również sprawdzić dodatkowe oświadczenia, zależnie od scenariusza. Niektórych typowych operacji sprawdzania poprawności obejmują:

* Zapewnienie, że użytkownika lub organizacja zarejestrowała się w aplikacji.
* Zapewnienie, że użytkownik ma odpowiednią autoryzacją i uprawnień.
* Zapewnienie, że siły uwierzytelniania wystąpił, takie jak za pomocą usługi Azure Multi-Factor Authentication.

Aby uzyskać więcej informacji na temat oświadczenia w tokenie identyfikator zobacz [odwołania do tokenu usługi Azure AD B2C](active-directory-b2c-reference-tokens.md).

Po całkowicie poprawności tokenu Identyfikacyjnego można rozpocząć sesji z użytkownikiem. W swojej aplikacji użyj oświadczenia w tokenie identyfikator, aby uzyskać informacje o użytkowniku. Te informacje mogą służyć ekranu, rekordy, autoryzacji i tak dalej.

## <a name="get-access-tokens"></a>Uzyskiwanie tokenów dostępu
W przypadku jedyną czynnością, aplikacji sieci web musi wykonać wykonania zasady, możesz pominąć kilka następnych sekcji. Informacje przedstawione w poniższych sekcjach są stosowane tylko do aplikacji sieci web, które mają wykonywanie uwierzytelnionych wywołań interfejsu API sieci web i które są chronione przez usługę Azure AD B2C.

Skoro zalogowaniu użytkownika do aplikacji jednostronicowej, mogą uzyskiwać tokeny dostępu, wywoływania internetowych interfejsów API, które są zabezpieczone przez usługę Azure AD. Nawet wtedy, gdy już uzyskały tokenu przy użyciu `token` typ odpowiedzi można użyć tej metody można uzyskać tokenów, aby uzyskać dodatkowe zasoby bez przekierowanie użytkownika do logowania się ponownie.

W przepływie aplikacja typowej sieci web, czy w tym celu kieruje żądanie do `/token` punktu końcowego.  Punkt końcowy nie obsługuje jednak żądań CORPS, więc wykonywanie wywołań AJAX, pobieranie i tokenów odświeżania nie jest opcją. Zamiast tego służy niejawny przepływ w ukrytym elemencie iframe kodu HTML w celu uzyskania nowych tokenów innych interfejsów API sieci web. Oto przykład z podziały wiersza w celu uzyskania czytelności:

```

https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
&p=b2c_1_sign_in
```

| Parametr | Wymagany? | Opis |
| --- | --- | --- |
| client_id |Wymagane |Identyfikator aplikacji przypisany do aplikacji w [witryny Azure portal](https://portal.azure.com). |
| response_type |Wymagane |Musi zawierać `id_token` dla logowania OpenID Connect.  Typ odpowiedzi może również zawierać `token`. Jeśli używasz `token` , aplikacja może bezpośrednio odbierać token dostępu z punktu końcowego autoryzacji bez wprowadzania drugie żądanie do punktu końcowego autoryzacji. Jeśli używasz `token` typ odpowiedzi `scope` parametr musi zawierać zakres, który wskazuje, którego zasobu należy wystawić token dla. |
| redirect_uri |Zalecane |Przekierowania URI aplikacji, gdzie odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Jego musi dokładnie odpowiadać jeden zarejestrowany w portalu, identyfikatory URI przekierowania z tym wyjątkiem, że musi być zakodowane w adresie URL. |
| zakres |Wymagane |Rozdzielonej spacjami listy zakresów.  W celu uzyskania tokenów, obejmują wszystkie zakresy, które wymagają zamierzony zasobu. |
| response_mode |Zalecane |Określa metodę, która służy do wysyłania wynikowy token wstecz do swojej aplikacji.  Może być `query`, `form_post`, lub `fragment`. |
| stan |Zalecane |Wartość uwzględnione w żądaniu, który jest zwracany w odpowiedzi tokenu.  Może być ciągiem żadnej zawartości, którego chcesz używać.  Zazwyczaj wartość losowo generowany, unikatowy służy, aby zapobiec atakom fałszowanie żądań między witrynami.  Stan jest również używane do kodowania informacje o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia. Na przykład strony lub widoku użytkownik był na. |
| Identyfikator jednorazowy |Wymagane |Wartości zawarte w żądaniu wygenerowane przez aplikację, który znajduje się w jego identyfikator tokenu jako oświadczenia.  Aplikacja może zweryfikować tę wartość, aby uniknąć powtarzania tokenu ataków. Zazwyczaj wartość jest losowego, unikatowy ciąg, który identyfikuje pochodzenia żądania. |
| wiersz |Wymagane |Odświeżanie i uzyskiwanie tokenów w ukrytym elemencie iframe, użyj `prompt=none` do upewnij się, że element iframe nie zatrzymywane na stronie logowania, zwraca natychmiast. |
| login_hint |Wymagane |Odświeżanie i uzyskiwanie tokenów w ukrytym elemencie iframe, nazwa użytkownika, należy uwzględnić w niej tę wskazówkę rozróżnienie między wiele sesji, które użytkownik może mieć w danym momencie. Można wyodrębnić nazwy użytkownika z wcześniejszych logowania za pomocą `preferred_username` oświadczenia. |
| Element domain_hint |Wymagane |Możliwe wartości to `consumers` i `organizations`.  Odświeżanie i uzyskiwanie tokenów w ukrytym elemencie iframe, należy uwzględnić `domain_hint` wartość w żądaniu.  Wyodrębnij `tid` oświadczeń z tokenu Identyfikatora wcześniej logowania można określić wartość, która do użycia.  Jeśli `tid` oświadczenia, wartość jest `9188040d-6c67-4c5b-b112-36a304b66dad`, użyj `domain_hint=consumers`.  W przeciwnym razie użyj `domain_hint=organizations`. |

Ustawiając `prompt=none` parametr, to żądanie albo kończy się powodzeniem lub natychmiast kończy się niepowodzeniem i zwraca do aplikacji.  Odpowiedź oznaczająca Powodzenie są wysyłane do aplikacji na wskazanych przekierowania URI, korzystając z metody podanej w `response_mode` parametru.

### <a name="successful-response"></a>Odpowiedź oznaczająca Powodzenie
Odpowiedź oznaczająca Powodzenie za pomocą `response_mode=fragment` wygląda podobnie do następującego:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| Parametr | Opis |
| --- | --- |
| access_token |Token, który zażądał aplikacji. |
| token_type |Typ tokenu zawsze będzie elementu nośnego. |
| stan |Jeśli `state` parametru jest uwzględnione w żądaniu, tę samą wartość powinna zostać wyświetlona w odpowiedzi. Aplikacja powinna upewnij się, że `state` wartości żądania i odpowiedzi są identyczne. |
| expires_in |Jak długo token dostępu jest prawidłowy (w sekundach). |
| zakres |Token dostępu jest prawidłowy dla zakresów. |

### <a name="error-response"></a>Odpowiedzi na błąd
Odpowiedzi na błędy również mogą być wysyłane do identyfikatora URI przekierowania, aby mógł być je obsłużyć odpowiednio w aplikacji.  Aby uzyskać `prompt=none`, oczekiwany błąd wygląda następująco:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametr | Opis |
| --- | --- |
| błąd |Ciągu kodu błędu, który może służyć do klasyfikowania typy błędów, które występują. Możesz również użyć ciągu, aby reagować na błędy. |
| error_description |Komunikat błędu, który pomoże Ci identyfikować przyczyny błędu uwierzytelniania. |

Jeśli ten błąd jest wyświetlany w żądaniu iframe, użytkownik interakcyjnie zalogować się ponownie do pobrania nowego tokenu. Problem można rozwiązać w sposób, który ma sens dla swojej aplikacji.

## <a name="refresh-tokens"></a>Tokenów odświeżania
Identyfikator tokenów i tokenów dostępu wygasa po krótkim czasie. Aplikacja musi być przygotowany do tych tokenów odświeżania okresowo.  Aby odświeżyć albo typ tokenu, użyto tego samego żądania ukrytego elementu iframe w wykonać poprzedniego przykładu, przy użyciu `prompt=none` parametru do kontrolowania kroków w usłudze Azure AD.  Aby otrzymać nowy `id_token` wartość, należy użyć `response_type=id_token` i `scope=openid`, a `nonce` parametru.

## <a name="send-a-sign-out-request"></a>Wyślij żądanie wylogowania
Do logowania użytkownika z aplikacji, należy przekierować użytkownika do usługi Azure AD, aby się wylogować. Jeśli tego nie zrobisz, użytkownik może mieć możliwość ponownego uwierzytelnienia do aplikacji bez konieczności ponownego wprowadzania poświadczeń. Jest tak, ponieważ mają one nieprawidłowy pojedynczego logowania jednokrotnego sesji z usługą Azure AD.

Możesz po prostu przekierować użytkownika do `end_session_endpoint` , znajduje się w tej samej OpenID Connect dokument metadanych opisane w [weryfikacji tokenu Identyfikacyjnego](#validate-the-id-token). Na przykład:

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametr | Wymagany? | Opis |
| --- | --- | --- |
| p |Wymagane |Zasady służące do logowania użytkownika z aplikacji. |
| post_logout_redirect_uri |Zalecane |Adres URL, który użytkownik powinno zostać przekierowane do po pomyślnym wylogowania. Jeśli nie jest uwzględniony, usługi Azure AD B2C wyświetla ogólny komunikat dla użytkownika. |

> [!NOTE]
> Kierowanie użytkowników do `end_session_endpoint` czyści część użytkownika pojedynczego logowania jednokrotnego stanu za pomocą usługi Azure AD B2C. Jednak go nie Zaloguj użytkownika z sesji dostawcy tożsamości społecznościowych użytkownika. Jeśli użytkownik wybierze takie same dostawcy podczas kolejne logowania, zaleceniem użytkownika jest ponownie uwierzytelniane — bez konieczności wprowadzania swoich poświadczeń. Jeśli użytkownik chce Wyloguj się z aplikacji usługi Azure AD B2C, go nie musi oznaczać, że mają być całkowicie Wyloguj się ze swojego konta w serwisie Facebook, na przykład. Jednak w przypadku kont lokalnych sesji użytkownika zostanie zakończona prawidłowo.
> 
> 

## <a name="use-your-own-azure-ad-b2c-tenant"></a>Użyć własnej dzierżawy usługi Azure AD B2C
Aby samodzielnie wypróbować te żądania, wykonaj następujące trzy kroki. Zastąp przykładowe wartości, używane w tym artykule własnymi wartościami:

1. [Tworzenie dzierżawy usługi Azure AD B2C](active-directory-b2c-get-started.md). Użyj nazwy dzierżawy w żądaniach.
2. [Tworzenie aplikacji](active-directory-b2c-app-registration.md) uzyskać identyfikator aplikacji i `redirect_uri` wartość. Obejmują aplikację sieci web lub interfejsu API sieci web w aplikacji. Opcjonalnie można utworzyć wpis tajny aplikacji.
3. [Tworzenie zasad](active-directory-b2c-reference-policies.md) można uzyskać nazwy zasad.

## <a name="samples"></a>Przykłady

* [Tworzenie aplikacji jednostronicowej przy użyciu środowiska Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi)
* [Tworzenie aplikacji jednostronicowej przy użyciu platformy .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi)

