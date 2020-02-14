---
title: Informacje o dzieleniu zmian Azure Active Directory | Microsoft Docs
description: Dowiedz się więcej na temat zmian wprowadzonych w protokołach usługi Azure AD, które mogą mieć wpływ na aplikację.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/24/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: c6ed72e5c94191411572c6ab67533141e2fe47d6
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185818"
---
# <a name="whats-new-for-authentication"></a>Co nowego w uwierzytelnianiu? 

>Otrzymuj powiadomienia o aktualizacjach na tej stronie. Po prostu Dodaj [ten adres URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) do czytnika kanału informacyjnego RSS.

System uwierzytelniania zmienia i dodaje funkcje na bieżąco w celu poprawy bezpieczeństwa i zgodności ze standardami. Aby zachować aktualność najnowszych zmian, ten artykuł zawiera informacje o następujących szczegółach:

- Najnowsze funkcje
- Znane problemy
- Zmiany protokołu
- Przestarzałe funkcje

> [!TIP] 
> Ta strona jest regularnie aktualizowana, więc często pojawia się. O ile nie zaznaczono inaczej, te zmiany są wprowadzane tylko dla nowo zarejestrowanych aplikacji.  

## <a name="upcoming-changes"></a>Nadchodzące zmiany

Brak zaplanowanych w tym momencie.  Poniżej znajdują się zmiany, które znajdują się w środowisku produkcyjnym lub znajdują się w nim. 

## <a name="february-2020"></a>Luty 2020 

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>Puste fragmenty zostaną dołączone do każdego przekierowania HTTP z punktu końcowego logowania. 

**Data wprowadzenia**: 8 lutego 2020

**Wpływ na punkty końcowe**: 1.0 i v 2.0

**Wpływ na protokół**: przepływy OAuth i OIDC, które używają response_type = Query — obejmuje to [przepływ kodu autoryzacji](v2-oauth2-auth-code-flow.md) w niektórych przypadkach i [niejawny przepływ](v2-oauth2-implicit-grant-flow.md). 

Po wysłaniu odpowiedzi uwierzytelniania z login.microsoftonline.com do aplikacji za pośrednictwem przekierowania HTTP usługa dołączy pusty fragment do adresu URL odpowiedzi.  Pozwala to zapobiec atakom klasy w celu przekierowania przez zagwarantowanie, że przeglądarka wyczyści wszystkie istniejące fragmenty w żądaniu uwierzytelniania.  Żadne aplikacje nie powinny być zależne od tego zachowania. 


## <a name="august-2019"></a>Sierpień 2019 r.

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>Semantyka formularza POST zostanie wymuszona z większą ilością miejsc, a cudzysłowy zostaną zignorowane

**Data wprowadzenia**: 2 września 2019

**Wpływ na punkty końcowe**: 1.0 i v 2.0

**Wpływ na protokół**: wpis wszędzie jest używany ([poświadczenia klienta](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow), [wykup kodu autoryzacji](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow), [ROPC](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc), [OBO](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)i [wykup tokenów](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token))

Począwszy od tygodnia 9/2, żądania uwierzytelniania używające metody POST będą weryfikowane przy użyciu bardziej rygorystycznych standardów protokołu HTTP.  W przypadku opcji spacje i podwójne cudzysłowy (") nie będą już usuwane z wartości formularza żądania. Te zmiany nie są oczekiwane w celu podziału istniejących klientów i zapewniają niezawodne Obsługiwanie żądań wysyłanych do usługi Azure AD za każdym razem. W przyszłości (Zobacz powyżej) planujemy dodatkowo odrzucanie zduplikowanych parametrów i ignorowanie BOM w ramach żądań. 

Przykład:

Obecnie `?e=    "f"&g=h` są analizowane identycznie jak `?e=f&g=h`, `e` == `f`.  W przypadku tej zmiany teraz można ją analizować, tak aby `e` == `    "f"` — prawdopodobnie jest to prawidłowy argument, a żądanie nie powiedzie się. 


## <a name="july-2019"></a>Lipiec 2019 r.

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Tokeny tylko do aplikacji dla aplikacji z jedną dzierżawą są wystawiane tylko wtedy, gdy aplikacja kliencka istnieje w dzierżawie zasobu

**Data wprowadzenia**: 26 lipca 2019

**Wpływ na punkty końcowe**: [1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) i v [2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**Wpływ na protokół**: [poświadczenia klienta (tokeny tylko dla aplikacji)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

Nastąpiła zmiana zabezpieczeń na żywo 26 lipca, która zmienia sposób, w jaki są wydawane tokeny tylko dla aplikacji (za pośrednictwem przyznanych poświadczeń klienta). Wcześniej aplikacje mogły uzyskać tokeny wywołujące dowolną inną aplikację, niezależnie od obecności w dzierżawie lub rolach, które zostały wysłane do danej aplikacji.  To zachowanie zostało zaktualizowane, tak aby w przypadku zasobów (nazywanych czasem interfejsem API sieci Web) ustawić jedną dzierżawę (domyślnie), aplikacja kliencka musi znajdować się w dzierżawie zasobów.  Należy zauważyć, że istniejąca zgoda między klientem i interfejsem API nadal nie jest wymagana, a aplikacje nadal powinny przeprowadzać własne testy autoryzacji, aby upewnić się, że `roles` jest obecny i zawiera oczekiwaną wartość dla interfejsu API.

Komunikat o błędzie dla tego scenariusza jest obecnie stanem: 

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

Aby rozwiązać ten problem, użyj środowiska zgody administratora, aby utworzyć nazwę główną usługi aplikacji klienta w dzierżawie, lub utwórz ją ręcznie.  To wymaganie zapewnia, że dzierżawca udzielił uprawnienia aplikacji do działania w ramach dzierżawy.  

#### <a name="example-request"></a>Przykładowe żądanie

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...` w tym przykładzie dzierżawca zasobów (Urząd) to contoso.com, aplikacja zasobów jest aplikacją z jedną dzierżawą o nazwie `gateway.contoso.com/api` dla dzierżawcy firmy Contoso, a aplikacja kliencka jest `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`.  Jeśli aplikacja kliencka ma nazwę główną usługi w ramach Contoso.com, to żądanie może być kontynuowane.  W przeciwnym razie żądanie zakończy się niepowodzeniem z błędem powyżej.  

Jeśli jednak aplikacja bramy contoso była aplikacją z wieloma dzierżawcami, żądanie będzie kontynuowane bez względu na to, czy aplikacja kliencka ma nazwę główną usługi w Contoso.com.  

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Identyfikatory URI przekierowania mogą teraz zawierać parametry ciągu zapytania

**Data wprowadzenia**: 22 lipca 2019

**Wpływ na punkty końcowe**: 1.0 i v 2.0

**Wpływ na protokół**: wszystkie przepływy

Na [RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)aplikacje usługi Azure AD mogą teraz rejestrować i używać identyfikatorów URI przekierowania (odpowiedź) przy użyciu statycznych parametrów zapytania (takich jak https://contoso.com/oauth2?idp=microsoft) w przypadku żądań uwierzytelniania OAuth 2,0).  Dynamiczne identyfikatory URI przekierowania są nadal zabronione, ponieważ stanowią zagrożenie bezpieczeństwa i nie mogą być używane do zachowywania informacji o stanie w ramach żądania uwierzytelniania — w tym celu należy użyć parametru `state`.

Statyczny parametr zapytania jest uzależniony od ciągu dla identyfikatorów URI przekierowania, takich jak jakakolwiek inna część identyfikatora URI przekierowania — Jeśli nie jest zarejestrowany żaden ciąg pasujący do redirect_uri z zdekodowanym identyfikatorem URI, żądanie zostanie odrzucone.  Jeśli identyfikator URI zostanie znaleziony w rejestracji aplikacji, cały ciąg zostanie użyty do przekierowania użytkownika, łącznie z parametrem zapytania statycznego. 

Należy pamiętać, że w tej chwili (koniec lipca 2019) środowisko rejestracji aplikacji w Azure Portal nadal blokuje parametry zapytania.  Można jednak ręcznie edytować manifest aplikacji w celu dodania parametrów zapytania i przetestowania go w aplikacji.  


## <a name="march-2019"></a>Marzec 2019 r.

### <a name="looping-clients-will-be-interrupted"></a>Zapętlenie klientów zostanie przerwane

**Data wprowadzenia**: 25 marca 2019

**Wpływ na punkty końcowe**: 1.0 i v 2.0

**Wpływ na protokół**: wszystkie przepływy

Aplikacje klienckie mogą czasami niedziałać, wydając setki tego samego żądania logowania w krótkim czasie.  Te żądania mogą się nie powieść, ale wszystkie mają wpływ na słabe środowisko użytkownika i zwiększone obciążenia dla dostawcy tożsamości, zwiększając opóźnienia dla wszystkich użytkowników i redukując dostępność dostawcy tożsamości.  Te aplikacje działają poza granicami normalnego użycia i należy je zaktualizować, aby działały prawidłowo.  

Klienci, którzy wydają zduplikowane żądania wiele razy, będą wysyłać `invalid_grant` błąd: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`. 

Większość klientów nie będzie musiała zmienić zachowania, aby uniknąć tego błędu.  Ten błąd będzie miał wpływ tylko na niepoprawnie skonfigurowanych klientów (bez buforowania tokenu lub tych, które są już wyświetlane).  Klienci są śledzoni lokalnie na poszczególnych wystąpieniach (za pośrednictwem pliku cookie) na następujących czynnikach:

* Wskazówka użytkownika, jeśli istnieje

* Żądane zakresy lub zasoby

* Identyfikator klienta

* Identyfikator URI przekierowania

* Typ i tryb odpowiedzi

Aplikacje wykonujące wiele żądań (15 +) w krótkim czasie (5 minut) `invalid_grant` otrzymają komunikat o błędzie z informacją o tym, że są zapętlenia.  Żądane tokeny mają wystarczająco długie okresy istnienia (co najmniej 10 minut, domyślnie 60 minut), więc powtarzające się żądania w tym okresie są zbędne.  

Wszystkie aplikacje powinny obsłużyć `invalid_grant`, wyświetlając interaktywny monit, zamiast w trybie dyskretnym żądać tokenu.  Aby uniknąć tego błędu, klienci powinni upewnić się, że prawidłowo buforują otrzymywane tokeny.


## <a name="october-2018"></a>Październik 2018 r.

### <a name="authorization-codes-can-no-longer-be-reused"></a>Nie można już ponownie używać kodów autoryzacji

**Data wprowadzenia**: 15 listopada 2018

**Wpływ na punkty końcowe**: 1.0 i v 2.0

**Wpływ na protokół**: [przepływ kodu](v2-oauth2-auth-code-flow.md)

15 listopada 2018 r. usługi Azure AD będzie akceptować kody poprzednio używanych uwierzytelniania dla aplikacji. Ta zmiana zabezpieczeń pomaga do usługi Azure AD zgodnie ze specyfikacją protokołu OAuth i będzie wymuszany w punktach końcowych v1 i v2.

Jeśli aplikacja używa kody autoryzacji uzyskiwanie tokenów dla wielu zasobów, zalecamy Użyj kodu, aby uzyskać token odświeżania, a następnie używać tego tokenu odświeżania w celu pobrania dodatkowe tokeny dla innych zasobów. Kody autoryzacji należy używać tylko raz, ale tokenów odświeżania mogą być wielokrotnie używane w wielu zasobach. W przypadku każdej nowej aplikacji próbującej ponownie użyć kodu uwierzytelniania podczas przepływu kodu OAuth Wystąpił błąd invalid_grant.

Aby uzyskać więcej informacji na temat tokenów odświeżania, zobacz [odświeżanie tokenów dostępu](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  W przypadku używania biblioteki ADAL lub MSAL jest to obsługiwane przez bibliotekę — Zastąp drugie wystąpienie elementu "AcquireTokenByAuthorizationCodeAsync" elementem "AcquireTokenSilentAsync". 

## <a name="may-2018"></a>Maj 2018 r.

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Nie można używać tokenów identyfikatora dla przepływu OBO

**Data**: 1 maja, 2018

**Wpływ na punkty końcowe**: 1.0 i v 2.0

**Wpływ na protokoły**: niejawny przepływ i [w imieniu przepływu](v2-oauth2-on-behalf-of-flow.md)

Od 1 maja 2018 id_tokens nie może być używany jako potwierdzenie w przepływie OBO dla nowych aplikacji. Tokeny dostępu należy zamiast tego używać w celu zabezpieczania interfejsów API, nawet między klientem a warstwą środkową tej samej aplikacji. Aplikacje zarejestrowane przed 1 maja 2018 będą nadal działać i mogą być dostępne do wymiany id_tokens dla tokenu dostępu. Jednak ten wzorzec nie jest uważany za najlepsze rozwiązanie.

Aby obejść tę zmianę, można wykonać następujące czynności:

1. Utwórz internetowy interfejs API dla aplikacji z co najmniej jednym zakresem. Ten jawny punkt wejścia umożliwi dokładniejszą kontrolę i bezpieczeństwo.
1. W manifeście aplikacji, w [Azure Portal](https://portal.azure.com) lub [portalu rejestracji aplikacji](https://apps.dev.microsoft.com), upewnij się, że aplikacja może wystawiać tokeny dostępu za pośrednictwem niejawnego przepływu. Jest to kontrolowane przez klucz `oauth2AllowImplicitFlow`.
1. Gdy aplikacja kliencka żąda id_token za pośrednictwem `response_type=id_token`, należy również zażądać tokenu dostępu (`response_type=token`) dla internetowego interfejsu API utworzonego powyżej. Dlatego przy użyciu punktu końcowego v 2.0 parametr `scope` powinien wyglądać podobnie do `api://GUID/SCOPE`. W punkcie końcowym v 1.0 parametr `resource` powinien być identyfikatorem URI aplikacji interfejsu API sieci Web.
1. Przekaż ten token dostępu do warstwy środkowej zamiast id_token.  
