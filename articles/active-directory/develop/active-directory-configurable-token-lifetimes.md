---
title: Konfigurowalne okresy istnienia tokenów usługi Azure AD
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak ustawiać okresy istnienia tokenów wystawionych przez usługę Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: f4138c4ae24ae599d4058c9fd06c33b69657fe38
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680069"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-preview"></a>Konfigurowalne okresy istnienia tokenu w Azure Active Directory (wersja zapoznawcza)

Możliwe jest określenie okresu istnienia tokenu wystawionego przez usługę Azure Active Directory (Azure AD). Okresy istnienia tokenów można ustawić dla wszystkich aplikacji w organizacji, dla aplikacji wielodostępnych (dla wielu organizacji) lub dla określonej jednostki usługi w organizacji.

> [!IMPORTANT]
> Po przesłuchaniu od klientów w wersji zapoznawczej wdrożono [funkcje zarządzania sesjami uwierzytelniania](https://go.microsoft.com/fwlink/?linkid=2083106) w usłudze Azure AD dostęp warunkowy. Ta nowa funkcja służy do konfigurowania okresów istnienia tokenu odświeżania przez ustawienie częstotliwości logowania. Po 1 maja 2020 nie będzie można używać konfigurowalnych zasad istnienia tokenu w celu konfigurowania tokenów sesji i odświeżania. Nadal można skonfigurować okresy istnienia tokenu dostępu po zakończeniu działania.

W usłudze Azure AD obiekt zasad reprezentuje zestaw reguł, które są wymuszane dla poszczególnych aplikacji lub we wszystkich aplikacjach w organizacji. Każdy typ zasad ma unikatową strukturę z zestawem właściwości, które są stosowane do obiektów, do których są przypisane.

Zasady można wyznaczyć jako zasady domyślne dla swojej organizacji. Zasady są stosowane do wszystkich aplikacji w organizacji, o ile nie zostały zastąpione przez zasady o wyższym priorytecie. Można również przypisać zasady do określonych aplikacji. Kolejność priorytetów różni się w zależności od typu zasad.

> [!NOTE]
> Zasady okresowego istnienia tokenu dotyczą tylko klientów mobilnych i komputerów stacjonarnych, które uzyskują dostęp do usług SharePoint Online i OneDrive dla firm i nie mają zastosowania do sesji przeglądarki sieci Web.
> Aby zarządzać okresem istnienia sesji przeglądarki sieci Web dla usług SharePoint Online i OneDrive dla firm, użyj funkcji [okres istnienia sesji dostępu warunkowego](../conditional-access/howto-conditional-access-session-lifetime.md) . Zapoznaj się z [blogiem usługi SharePoint Online](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) , aby dowiedzieć się więcej o konfigurowaniu limitów czasu bezczynności sesji.

## <a name="token-types"></a>Typy tokenów

Można ustawić zasady okresu istnienia tokenu dla tokenów odświeżania, tokenów dostępu, tokenów SAML, tokenów sesji i tokenów identyfikatorów.

### <a name="access-tokens"></a>Tokeny dostępu

Klienci używają tokenów dostępu w celu uzyskiwania dostępu do chronionego zasobu. Tokenu dostępu można używać tylko dla konkretnej kombinacji użytkownika, klienta i zasobu. Tokeny dostępu nie mogą zostać odwołane i są ważne do czasu ich wygaśnięcia. Złośliwy aktor, który uzyskał token dostępu, może korzystać z niego na potrzeby zakresu jego okresu istnienia. Dostosowanie okresu istnienia tokena dostępu jest kompromisem między zwiększeniem wydajności systemu i zwiększeniem czasu, przez jaki klient zachowuje dostęp po wyłączeniu konta użytkownika. Zwiększona wydajność systemu jest osiągana przez zredukowanie liczby prób uzyskania przez klienta nowego tokenu dostępu.  Wartość domyślna to 1 godzina — po 1 godzinie klient musi używać tokenu odświeżania do (zazwyczaj dyskretnie) uzyskać nowy token odświeżania i token dostępu. 

### <a name="saml-tokens"></a>Tokeny języka SAML

Tokeny SAML są używane przez wiele aplikacji SAAS opartych na sieci Web i są uzyskiwane Azure Active Directory przy użyciu punktu końcowego protokołu SAML2. Są one również używane przez aplikacje korzystające z protokołu WS-Federation. Domyślny okres istnienia tokenu to 1 godzina. W perspektywie aplikacji okres ważności tokenu jest określany przez wartość NotOnOrAfter `<conditions …>` elementu w tokenie. Po zakończeniu okresu ważności tokenu klient musi zainicjować nowe żądanie uwierzytelniania, które będzie często spełnione bez interakcyjnego logowania w wyniku tokenu sesji logowania jednokrotnego (SSO).

Wartość NotOnOrAfter można zmienić przy użyciu `AccessTokenLifetime` parametru w. `TokenLifetimePolicy` Zostanie ona ustawiona na okres istnienia skonfigurowany w ramach zasad, jeśli istnieje, oraz współczynnik pochylenia zegara wynoszący pięć minut.

Należy pamiętać, że konfiguracja okresu istnienia tokenu `<SubjectConfirmationData>` nie ma wpływ na potwierdzenie podmiotu NotOnOrAfter określone w elemencie. 

### <a name="refresh-tokens"></a>Odśwież tokeny

Gdy klient uzyskuje token dostępu w celu uzyskania dostępu do chronionego zasobu, klient otrzymuje również token odświeżenia. Token odświeżania służy do uzyskiwania nowych par tokenów dostępu/odświeżania w przypadku wygaśnięcia bieżącego tokenu dostępu. Token odświeżania jest powiązany z kombinacją użytkownika i klienta. Token odświeżania można [odwołać w dowolnym momencie](access-tokens.md#token-revocation), a ważność tokenu jest sprawdzana za każdym razem, gdy token jest używany.  Tokeny odświeżania nie są odwoływane, gdy są używane do pobierania nowych tokenów dostępu — najlepszym rozwiązaniem jest jednak, aby bezpiecznie usunąć stary token podczas uzyskiwania nowego. 

Ważne jest, aby wprowadzić rozróżnienie między poufnymi klientami i klientami publicznymi, co ma wpływ na to, jak długo można używać tokenów odświeżania. Aby uzyskać więcej informacji na temat różnych typów klientów, zobacz [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Okresy istnienia tokenów z poufnymi tokenami odświeżania klienta
Poufne klienci są aplikacjami, które mogą bezpiecznie przechowywać hasło klienta (poufne). Mogą oni udowodnić, że żądania pochodzą z zabezpieczonej aplikacji klienckiej, a nie od złośliwego aktora. Na przykład aplikacja sieci Web to poufny klient, ponieważ może przechowywać klucz tajny klienta na serwerze sieci Web. Nie jest on narażony. Ponieważ te przepływy są bezpieczniejsze, domyślne okresy istnienia tokenów odświeżania wystawionych dla `until-revoked`tych przepływów nie mogą być zmieniane przy użyciu zasad i nie zostaną odwołane w przypadku resetowania hasła dobrowolnego.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Okresy istnienia tokenów z publicznymi tokenami odświeżania klienta

Klienci publiczni nie mogą bezpiecznie przechowywać hasła klienta (poufne). Na przykład aplikacja dla systemu iOS/Android nie może zasłaniać wpisu tajnego z właściciela zasobu, więc jest traktowana jako klient publiczny. Można ustawić zasady dla zasobów, aby zapobiec tokenom odświeżania od klientów publicznych starszych niż określony okres od uzyskania nowej pary tokenów dostępu/odświeżenia. (W tym celu należy użyć właściwości maksymalny czas nieaktywności tokenu odświeżania (`MaxInactiveTime`).) Można również użyć zasad, aby ustawić okres, po którym tokeny odświeżania nie są już akceptowane. (W tym celu należy użyć właściwości maksymalny wiek tokenu odświeżania). Można dostosować okres istnienia tokenu odświeżania, aby określić, kiedy i jak często użytkownik musi ponownie wprowadzić poświadczenia, zamiast być w sposób dyskretny uwierzytelniany w przypadku korzystania z publicznej aplikacji klienckiej.

> [!NOTE]
> Właściwość Max Age to długość czasu, przez który można użyć pojedynczego tokenu. 

### <a name="id-tokens"></a>Tokeny identyfikatorów
Tokeny identyfikatorów są przesyłane do witryn sieci Web i natywnych klientów. Tokeny identyfikatorów zawierają informacje o profilu użytkownika. Token identyfikatora jest powiązany z określoną kombinacją użytkownika i klienta. Tokeny identyfikatorów są uznawane za ważne do momentu ich wygaśnięcia. Zwykle aplikacja sieci Web dopasowuje okres istnienia sesji użytkownika w aplikacji do okresu istnienia tokenu identyfikatora wydanego dla użytkownika. Możesz dostosować okres istnienia tokenu identyfikatora, aby określić, jak często aplikacja sieci Web wygaśnie w sesji aplikacji i jak często wymaga ponownego uwierzytelnienia użytkownika w usłudze Azure AD (dyskretnie lub interaktywnie).

### <a name="single-sign-on-session-tokens"></a>Tokeny sesji logowania jednokrotnego
Gdy użytkownik jest uwierzytelniany w usłudze Azure AD, zostaje ustanowiona sesja logowania jednokrotnego (SSO) z przeglądarką użytkownika i usługą Azure AD. Token logowania jednokrotnego w formie pliku cookie reprezentuje tę sesję. Token sesji logowania jednokrotnego nie jest powiązany z określonym zasobem/aplikacją kliencką. Tokeny sesji SSO można odwołać, a ich ważność jest sprawdzana za każdym razem, gdy są używane.

Usługa Azure AD używa dwóch rodzajów tokenów sesji SSO: trwałych i nietrwałych. Tokeny sesji trwałych są przechowywane jako trwałe pliki cookie w przeglądarce. Tokeny sesji nietrwałych są przechowywane jako pliki cookie sesji. (Pliki cookie sesji są niszczone po zamknięciu przeglądarki). Zwykle jest przechowywany token nietrwałych sesji. Jednak gdy użytkownik wybierze pole wyboru nie wylogowuj **mnie** podczas uwierzytelniania, jest przechowywany token sesji trwałej.

Tokeny sesji nietrwałych mają okres istnienia 24 godzin. Stałe tokeny mają okres istnienia wynoszący 180 dni. W czasie, gdy token sesji logowania jednokrotnego jest używany w okresie ważności, okres ważności jest rozszerzany o kolejne 24 godziny lub 180 dni, w zależności od typu tokenu. Jeśli token sesji SSO nie jest używany w jego okresie ważności, jest uznawany za wygasły i nie jest już akceptowany.

Można użyć zasad, aby ustawić czas po wydaniu pierwszego tokenu sesji, poza którym token sesji nie jest już akceptowany. (W tym celu należy użyć właściwości maksymalny wiek tokenu sesji). Możesz dostosować okres istnienia tokenu sesji, aby określić, kiedy i jak często użytkownik musi ponownie wprowadzić poświadczenia, zamiast być uwierzytelniany w trybie dyskretnym w przypadku korzystania z aplikacji sieci Web.

### <a name="token-lifetime-policy-properties"></a>Właściwości zasad okresu istnienia tokenu
Zasada okresu istnienia tokenu jest typem obiektu zasad, który zawiera reguły okresu istnienia tokenu. Użyj właściwości zasad do kontrolowania określonych okresów istnienia tokenu. Jeśli nie ustawiono żadnych zasad, system wymusza domyślną wartość okresu istnienia.

### <a name="configurable-token-lifetime-properties"></a>Konfigurowalne właściwości okresu istnienia tokenu
| Właściwość | Ciąg właściwości zasad | Mową | Domyślny | Minimalne | Maksimum |
| --- | --- | --- | --- | --- | --- |
| Okres istnienia tokenu dostępu |AccessTokenLifetime<sup>2</sup> |Tokeny dostępu, tokeny identyfikatorów, tokeny SAML2 |1 godzina |10 minut |1 dzień |
| Maksymalny czas nieaktywności tokenu odświeżania |MaxInactiveTime |Odśwież tokeny |90 dni |10 minut |90 dni |
| Maksymalny wiek tokenu odświeżania pojedynczego czynnika |MaxAgeSingleFactor |Odśwież tokeny (dla wszystkich użytkowników) |Do odwołania |10 minut |Do odwołania<sup>1</sup> |
| Maksymalny wiek tokenu wieloskładnikowego odświeżania |MaxAgeMultiFactor |Odśwież tokeny (dla wszystkich użytkowników) |Do odwołania |10 minut |Do odwołania<sup>1</sup> |
| Maksymalny wiek tokenu sesji pojedynczego czynnika |MaxAgeSessionSingleFactor |Tokeny sesji (trwałe i nietrwałe) |Do odwołania |10 minut |Do odwołania<sup>1</sup> |
| Maksymalny wiek tokenu sesji wieloskładnikowe |MaxAgeSessionMultiFactor |Tokeny sesji (trwałe i nietrwałe) |Do odwołania |10 minut |Do odwołania<sup>1</sup> |

* <sup>1</sup>365 dni to maksymalna jawna długość, którą można ustawić dla tych atrybutów.
* <sup>2</sup> Aby zapewnić działanie klienta sieci Web Microsoft Teams, zaleca się pozostawienie AccessTokenLifetime do ponad 15 minut dla Microsoft Teams.

### <a name="exceptions"></a>Wyjątki
| Właściwość | Mową | Domyślny |
| --- | --- | --- |
| Maksymalny wiek tokenu odświeżania (wystawiony dla użytkowników federacyjnych, którzy mają niewystarczające informacje o odwołaniu<sup>1</sup>) |Odśwież tokeny (wystawione dla użytkowników federacyjnych, którzy mają niewystarczające informacje o odwołaniu<sup>1</sup>) |12 godz. |
| Maksymalny czas nieaktywności tokenu odświeżania (wystawiony dla klientów poufnych) |Odśwież tokeny (wystawione dla klientów poufnych) |90 dni |
| Maksymalny wiek tokenu odświeżania (wystawiony dla klientów poufnych) |Odśwież tokeny (wystawione dla klientów poufnych) |Do odwołania |

* <sup>1</sup> Użytkownicy federacyjny, którzy mają niewystarczające informacje o odwołaniu, obejmują wszystkich użytkowników, którzy nie mają atrybutu "LastPasswordChangeTimestamp". Ci użytkownicy otrzymują ten krótki maksymalny wiek, ponieważ usługa AAD nie może zweryfikować, kiedy odwołać tokeny powiązane ze starym poświadczeniem (na przykład hasło, które zostało zmienione), i należy ponownie zaewidencjonować, aby upewnić się, że użytkownik i skojarzone tokeny są nadal w dobrym stanie. Aby ulepszyć to środowisko, Administratorzy dzierżaw muszą upewnić się, że synchronizują atrybut "LastPasswordChangeTimestamp" (można go ustawić dla obiektu użytkownika przy użyciu programu PowerShell lub za pośrednictwem AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Ocena zasad i priorytetyzacja
Można utworzyć i przypisać zasady czasu istnienia tokenu do określonej aplikacji, organizacji, a także do podmiotów usługi. Do określonej aplikacji mogą być stosowane wiele zasad. Zasady okresu istnienia tokenu, które obowiązują, są zgodne z następującymi regułami:

* Jeśli zasady są jawnie przypisane do nazwy głównej usługi, jest wymuszane.
* Jeśli żadna zasada nie zostanie jawnie przypisana do jednostki usługi, wymuszana jest zasada jawnie przypisana do organizacji nadrzędnej jednostki usługi.
* Jeśli żadna zasada nie zostanie jawnie przypisana do jednostki usługi lub do organizacji, zasady przypisane do aplikacji są wymuszane.
* Jeśli żadna zasada nie została przypisana do jednostki usługi, organizacji lub obiektu aplikacji, wartości domyślne są wymuszane. (Zobacz tabelę w obszarze [Właściwości konfigurowalnego okresu istnienia tokenu](#configurable-token-lifetime-properties)).

Aby uzyskać więcej informacji na temat relacji między obiektami aplikacji i obiektami głównej usługi, zobacz temat [obiekty główne aplikacji i usługi w Azure Active Directory](app-objects-and-service-principals.md).

Ważność tokenu jest oceniana w czasie używania tokenu. Zasady z najwyższym priorytetem w aplikacji, do której uzyskuje się dostęp, zaczynają obowiązywać.

Wszystkie używane tu przedziały czasu są sformatowane zgodnie z obiektem [TimeSpan](/dotnet/api/system.timespan) języka C#-D. hh: mm: SS.  Tak więc 80 dni i 30 minut `80.00:30:00`.  Interlinię D można porzucić, jeśli zero, więc 90 minut `00:90:00`.  

> [!NOTE]
> Oto przykładowy scenariusz.
>
> Użytkownik chce uzyskać dostęp do dwóch aplikacji sieci Web: aplikacji sieci Web A i aplikacji sieci Web B.
> 
> Elementy
> * Obie aplikacje sieci Web znajdują się w tej samej organizacji nadrzędnej.
> * Zasady okresu istnienia tokenu 1 z tokenem sesji maksymalny wiek równy osiem godzin jest ustawiany jako domyślna organizacja nadrzędna.
> * Aplikacja sieci Web A to regularna aplikacja sieci Web, która nie jest połączona z żadną zasadą.
> * Aplikacja sieci Web B jest używana w przypadku procesów wysoce poufnych. Jej jednostka usługi jest połączona z zasadą okresu istnienia tokenu 2, która ma maksymalny wiek tokenu sesji wynoszący 30 minut.
>
> O godzinie 12:00 PM użytkownik uruchamia nową sesję przeglądarki i próbuje uzyskać dostęp do aplikacji sieci Web A. Użytkownik zostanie przekierowany do usługi Azure AD i zostanie poproszony o zalogowanie się. Spowoduje to utworzenie pliku cookie z tokenem sesji w przeglądarce. Użytkownik zostanie przekierowany z powrotem do aplikacji sieci Web A przy użyciu tokenu identyfikatora, który umożliwia użytkownikowi dostęp do aplikacji.
>
> W 12:15 PM użytkownik próbuje uzyskać dostęp do aplikacji sieci Web B. Przeglądarka przekierowuje do usługi Azure AD, która wykrywa plik cookie sesji. Nazwa główna usługi aplikacji sieci Web B jest połączona z zasadą okresu istnienia tokenu 2, ale jest również częścią organizacji nadrzędnej z domyślnymi zasadami ważności tokenu 1. Zasady ważności tokenu 2 obowiązują, ponieważ zasady połączone z jednostkami usługi mają wyższy priorytet niż domyślne zasady organizacji. Token sesji został pierwotnie wydany w ciągu ostatnich 30 minut, więc jest uznawany za ważny. Użytkownik zostanie przekierowany z powrotem do aplikacji sieci Web B z tokenem identyfikatora, który przyznaje im dostęp.
>
> O godzinie 1:00 PM użytkownik próbuje uzyskać dostęp do aplikacji sieci Web A. Użytkownik zostanie przekierowany do usługi Azure AD. Aplikacja sieci Web A nie jest połączona z żadną zasadą, ale ponieważ należy do organizacji z domyślnymi zasadami istnienia tokenu 1, ta zasada zacznie obowiązywać. Wykryto plik cookie sesji, który został pierwotnie wystawiony w ciągu ostatnich ośmiu godzin. Użytkownik jest dyskretnie przekierowywany do aplikacji sieci Web A z nowym tokenem ID. Użytkownik nie musi uwierzytelniać się.
>
> Natychmiast użytkownik próbuje uzyskać dostęp do aplikacji sieci Web B. Użytkownik zostanie przekierowany do usługi Azure AD. Zgodnie z wcześniejszymi zasadami istnienia tokenu obowiązuje zasada 2. Ponieważ token został wystawiony ponad 30 minut temu, użytkownik jest monitowany o ponowne wprowadzenie poświadczeń logowania. Zostanie wystawiony token nowej sesji i token ID. Użytkownik może następnie uzyskać dostęp do aplikacji sieci Web B.
>
>

## <a name="configurable-policy-property-details"></a>Szczegóły właściwości zasad konfigurowalnych
### <a name="access-token-lifetime"></a>Okres istnienia tokenu dostępu
**Ciąg:** AccessTokenLifetime

**Ma wpływ na:** Tokeny dostępu, tokeny identyfikatorów, tokeny SAML

**Podsumowanie:** Ta zasada kontroluje, jak długo tokeny dostępu i identyfikatorów dla tego zasobu są uznawane za ważne. Zmniejszenie właściwości okresu istnienia tokenu dostępu zmniejsza ryzyko związane z tokenem dostępu lub tokenem ID używanym przez złośliwy aktor przez dłuższy czas. (Tokeny te nie mogą zostać odwołane). W wyniku tego jest niekorzystnie wpływać na wydajność, ponieważ tokeny muszą być częściej zastępowane.

### <a name="refresh-token-max-inactive-time"></a>Maksymalny czas nieaktywności tokenu odświeżania
**Ciąg:** MaxInactiveTime

**Ma wpływ na:** Odśwież tokeny

**Podsumowanie:** Ta zasada kontroluje, jak stara token odświeżania, zanim klient nie będzie mógł go już używać do pobierania nowej pary tokenów dostępu/odświeżania podczas próby uzyskania dostępu do tego zasobu. Ponieważ nowy token odświeżania jest zwykle zwracany, gdy używany jest token odświeżania, te zasady uniemożliwiają dostęp, jeśli klient próbuje uzyskać dostęp do dowolnego zasobu przy użyciu bieżącego tokenu odświeżania w określonym przedziale czasu.

Te zasady wymuszają, że użytkownicy, którzy nie zostali aktywni na kliencie w celu ponownego uwierzytelnienia w celu pobrania nowego tokenu odświeżania.

Właściwość maksymalny czas nieaktywności tokenu odświeżania musi być ustawiona na wartość niższą niż maksymalny wiek tokenu usługi Single-Factor i Maksymalna liczba właściwości wieku tokenu usługi wieloskładnikowej.

### <a name="single-factor-refresh-token-max-age"></a>Maksymalny wiek tokenu odświeżania pojedynczego czynnika
**Ciąg:** MaxAgeSingleFactor

**Ma wpływ na:** Odśwież tokeny

**Podsumowanie:** Ta zasada kontroluje, jak długo użytkownik może użyć tokenu odświeżania, aby uzyskać nową parę tokenów dostępu/odświeżenia po pomyślnym uwierzytelnieniu przy użyciu tylko jednego czynnika. Po uwierzytelnieniu i otrzymaniu nowego tokenu odświeżania użytkownik może użyć przepływu tokenu odświeżania przez określony czas. (Jest to prawdziwe, o ile bieżący token odświeżania nie jest odwołany i nie jest jeszcze nieużywany przez czas dłuższy niż nieaktywny). W tym momencie użytkownik jest zmuszony do ponownego uwierzytelnienia w celu otrzymania nowego tokenu odświeżania.

Skrócenie maksymalnego wieku zmusza użytkowników do częstego uwierzytelniania. Ponieważ uwierzytelnianie wieloskładnikowe jest uznawane za mniej bezpieczne niż w przypadku uwierzytelniania wieloskładnikowego, zalecamy ustawienie tej właściwości na wartość, która jest równa lub mniejsza niż Właściwość maksymalnego wieku tokena odświeżania.

### <a name="multi-factor-refresh-token-max-age"></a>Maksymalny wiek tokenu wieloskładnikowego odświeżania
**Ciąg:** MaxAgeMultiFactor

**Ma wpływ na:** Odśwież tokeny

**Podsumowanie:** Ta zasada kontroluje, jak długo użytkownik może użyć tokenu odświeżania, aby uzyskać nową parę tokenów dostępu/odświeżenia po pomyślnym uwierzytelnieniu przy użyciu wielu czynników. Po uwierzytelnieniu i otrzymaniu nowego tokenu odświeżania użytkownik może użyć przepływu tokenu odświeżania przez określony czas. (Wartość ta jest równa, o ile bieżący token odświeżania nie jest odwołany i nie jest używany dłużej niż czas nieaktywności). W tym momencie użytkownicy są zmuszeni do ponownego uwierzytelnienia w celu otrzymania nowego tokenu odświeżania.

Skrócenie maksymalnego wieku zmusza użytkowników do częstego uwierzytelniania. Ponieważ uwierzytelnianie wieloskładnikowe jest uznawane za mniej bezpieczne niż w przypadku uwierzytelniania wieloskładnikowego, zalecamy ustawienie tej właściwości na wartość, która jest równa lub większa niż Właściwość maksymalnego wieku tokena odświeżania.

### <a name="single-factor-session-token-max-age"></a>Maksymalny wiek tokenu sesji pojedynczego czynnika
**Ciąg:** MaxAgeSessionSingleFactor

**Ma wpływ na:** Tokeny sesji (trwałe i nietrwałe)

**Podsumowanie:** Ta zasada kontroluje, jak długo użytkownik może użyć tokenu sesji w celu uzyskania nowego identyfikatora i tokenu sesji po pomyślnym uwierzytelnieniu przy użyciu tylko jednego czynnika. Po uwierzytelnieniu i odebraniu nowego tokenu sesji użytkownik może użyć przepływu tokenu sesji przez określony czas. (Wartość ta jest równa, o ile token bieżącej sesji nie został odwołany i nie wygasł.) Po upływie określonego czasu użytkownik jest zmuszony do ponownego uwierzytelnienia w celu otrzymania nowego tokenu sesji.

Skrócenie maksymalnego wieku zmusza użytkowników do częstego uwierzytelniania. Ponieważ uwierzytelnianie wieloskładnikowe jest uznawane za mniej bezpieczne niż w przypadku uwierzytelniania wieloskładnikowego, zalecamy ustawienie tej właściwości na wartość, która jest równa lub mniejsza niż maksymalny wiek tokenu sesji wieloskładnikowej.

### <a name="multi-factor-session-token-max-age"></a>Maksymalny wiek tokenu sesji wieloskładnikowe
**Ciąg:** MaxAgeSessionMultiFactor

**Ma wpływ na:** Tokeny sesji (trwałe i nietrwałe)

**Podsumowanie:** Ta zasada kontroluje, jak długo użytkownik może użyć tokenu sesji w celu uzyskania nowego identyfikatora i tokenu sesji po ostatnim pomyślnym uwierzytelnieniu przy użyciu wielu czynników. Po uwierzytelnieniu i odebraniu nowego tokenu sesji użytkownik może użyć przepływu tokenu sesji przez określony czas. (Wartość ta jest równa, o ile token bieżącej sesji nie został odwołany i nie wygasł.) Po upływie określonego czasu użytkownik jest zmuszony do ponownego uwierzytelnienia w celu otrzymania nowego tokenu sesji.

Skrócenie maksymalnego wieku zmusza użytkowników do częstego uwierzytelniania. Ponieważ uwierzytelnianie wieloskładnikowe jest uznawane za mniej bezpieczne niż w przypadku usługi uwierzytelniania wieloskładnikowego, zalecamy ustawienie tej właściwości na wartość, która jest równa lub większa niż Właściwość maksymalnego wieku tokenu sesji.

## <a name="example-token-lifetime-policies"></a>Przykładowe zasady okresu istnienia tokenu
Wiele scenariuszy jest dostępnych w usłudze Azure AD, gdy można tworzyć okresy istnienia tokenów dla aplikacji, podmiotów usługi i całej organizacji oraz zarządzać nimi. W tej sekcji omówiono kilka typowych scenariuszy zasad, które mogą pomóc w nałożeniu nowych reguł dla:

* Czas życia tokenu
* Maksymalny czas nieaktywności tokenu
* Maksymalny wiek tokenu

W przykładach można dowiedzieć się, jak:

* Zarządzanie zasadami domyślnymi organizacji
* Tworzenie zasad logowania do sieci Web
* Tworzenie zasad dla aplikacji natywnej, która wywołuje interfejs API sieci Web
* Zarządzanie zaawansowanymi zasadami

### <a name="prerequisites"></a>Wymagania wstępne
W poniższych przykładach tworzysz, aktualizujesz, łączysz i usuwasz zasady dla aplikacji, podmiotów usługi i całej organizacji. Jeśli dopiero zaczynasz korzystać z usługi Azure AD, zalecamy zapoznanie się z tematem [jak uzyskać dzierżawę usługi Azure AD](quickstart-create-new-tenant.md) przed wykonaniem tych przykładów.  

Aby rozpocząć, wykonaj następujące czynności:

1. Pobierz najnowszą [wersję publicznej wersji zapoznawczej modułu programu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
2. Uruchom `Connect` polecenie, aby zalogować się do konta administratora usługi Azure AD. Uruchom to polecenie przy każdym uruchomieniu nowej sesji.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. Aby wyświetlić wszystkie zasady, które zostały utworzone w organizacji, uruchom następujące polecenie. Uruchom to polecenie po większości operacji w następujących scenariuszach. Uruchomienie polecenia pomaga również uzyskać * * * * dla zasad.

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Przykład: Zarządzanie zasadami domyślnymi organizacji
W tym przykładzie utworzysz zasady, które umożliwiają logowanie użytkowników rzadziej w całej organizacji. W tym celu należy utworzyć zasady istnienia tokenu dla tokenów odświeżania z jednym czynnikiem, które są stosowane w całej organizacji. Zasady są stosowane do każdej aplikacji w organizacji oraz do każdej jednostki usługi, która nie ma jeszcze zestawu zasad.

1. Utwórz zasady czasu istnienia tokenu.

    1. Ustaw token jednoskładnikowego odświeżania na "do odwołania". Token nie wygasa, dopóki dostęp nie zostanie odwołany. Utwórz następującą definicję zasad:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. Aby utworzyć zasady, uruchom następujące polecenie:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Aby usunąć wszystkie odstępy, uruchom następujące polecenie:

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. Aby wyświetlić nowe zasady i uzyskać identyfikator **objectid**zasad, uruchom następujące polecenie:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Zaktualizuj zasady.

    Możesz zdecydować, że pierwsze zasady ustawione w tym przykładzie nie są zgodne z wymaganiami usługi. Aby ustawić token odświeżania jednoskładnikowego wygaśnie w ciągu dwóch dni, uruchom następujące polecenie:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Przykład: Tworzenie zasad logowania do sieci Web

W tym przykładzie utworzysz zasady, które wymagają, aby użytkownicy uwierzytelniali się częściej w aplikacji sieci Web. Te zasady określają okres istnienia tokenów dostępu/identyfikatora oraz maksymalny wiek tokenu sesji wieloskładnikowej do jednostki usługi aplikacji sieci Web.

1. Utwórz zasady czasu istnienia tokenu.

    Te zasady dla logowania za pomocą sieci Web określają okres istnienia tokenu dostępu/identyfikatora oraz maksymalny wiek tokenu sesji pojedynczego czynnika na dwie godziny.

    1. Aby utworzyć zasady, uruchom następujące polecenie:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Aby wyświetlić nowe zasady i uzyskać identyfikator **objectid**zasad, uruchom następujące polecenie:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Przypisz zasady do nazwy głównej usługi. Należy również uzyskać identyfikator **objectid** nazwy głównej usługi.

    1. Użyj polecenia cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) , aby wyświetlić wszystkie nazwy główne usługi lub pojedynczą jednostkę usługi.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. Jeśli masz nazwę główną usługi, uruchom następujące polecenie:
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Przykład: Tworzenie zasad dla natywnej aplikacji, która wywołuje interfejs API sieci Web
W tym przykładzie utworzysz zasady, które wymagają, aby użytkownicy byli uwierzytelniani rzadziej. Zasada wydłuża również czas nieaktywności użytkownika, po upływie którego użytkownik musi ponownie przeprowadzić uwierzytelnienie. Zasady są stosowane do internetowego interfejsu API. Gdy aplikacja natywna zażąda internetowego interfejsu API jako zasobu, te zasady są stosowane.

1. Utwórz zasady czasu istnienia tokenu.

    1. Aby utworzyć ścisłe zasady dla internetowego interfejsu API, uruchom następujące polecenie:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Aby wyświetlić nowe zasady, uruchom następujące polecenie:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Przypisz zasady do internetowego interfejsu API. Należy również uzyskać identyfikator **objectid** aplikacji. Użyj polecenia cmdlet [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) , aby znaleźć **Identyfikator obiektu aplikacji**, lub Użyj [Azure Portal](https://portal.azure.com/).

    Pobierz identyfikator **objectid** aplikacji i przypisz zasady:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="example-manage-an-advanced-policy"></a>Przykład: Zarządzanie zaawansowanymi zasadami
W tym przykładzie utworzysz kilka zasad, aby dowiedzieć się, jak działa system priorytetów. Dowiesz się również, jak zarządzać wieloma zasadami, które są stosowane do kilku obiektów.

1. Utwórz zasady czasu istnienia tokenu.

    1. Aby utworzyć zasady domyślne dla organizacji, które określają okres istnienia tokenu odświeżania pojedynczego czynnika na 30 dni, uruchom następujące polecenie:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Aby wyświetlić nowe zasady, uruchom następujące polecenie:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Przypisz zasady do jednostki usługi.

    Teraz masz zasady, które mają zastosowanie do całej organizacji. Możesz chcieć zachować te 30-dniowe zasady dla określonej nazwy głównej usługi, ale zmienić domyślne zasady organizacji na górny limit "do odwołania".

    1. Aby wyświetlić wszystkie nazwy główne usługi w organizacji, należy użyć polecenia cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) .

    1. Jeśli masz nazwę główną usługi, uruchom następujące polecenie:

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. Ustaw `IsOrganizationDefault` flagę na wartość false:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. Utwórz nową zasadę domyślną dla organizacji:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Masz już oryginalne zasady połączone z jednostką usługi, a nowe zasady są ustawione jako domyślne zasady organizacji. Należy pamiętać, że zasady stosowane do jednostek usługi mają priorytet w porównaniu z zasadami domyślnymi organizacji.

## <a name="cmdlet-reference"></a>Dokumentacja poleceń cmdlet

### <a name="manage-policies"></a>Zarządzanie zasadami

Aby zarządzać zasadami, można użyć następujących poleceń cmdlet.

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

Tworzy nowe zasady.

```powershell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Definition</code> |Tablica JSON skonwertowanej, która zawiera wszystkie reguły zasad. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Ciąg nazwy zasad. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |W przypadku wartości true ustawia zasady jako domyślne zasady organizacji. W przypadku wartości false nic nie robi. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Typ zasad. W przypadku okresów istnienia tokenu zawsze używaj "TokenLifetimePolicy". | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>Obowiązkowe |Ustawia identyfikator alternatywny dla zasad. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Pobiera wszystkie zasady usługi Azure AD lub określone zasady.

```powershell
Get-AzureADPolicy
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code>Obowiązkowe |**Objectid (ID)** żądanych zasad. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Pobiera wszystkie aplikacje i jednostki usługi, które są połączone z zasadami.

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objectid (ID)** żądanych zasad. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Aktualizuje istniejące zasady.

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objectid (ID)** żądanych zasad. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Ciąg nazwy zasad. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code>Obowiązkowe |Tablica JSON skonwertowanej, która zawiera wszystkie reguły zasad. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code>Obowiązkowe |W przypadku wartości true ustawia zasady jako domyślne zasady organizacji. W przypadku wartości false nic nie robi. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code>Obowiązkowe |Typ zasad. W przypadku okresów istnienia tokenu zawsze używaj "TokenLifetimePolicy". |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>Obowiązkowe |Ustawia identyfikator alternatywny dla zasad. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
Usuwa określone zasady.

```powershell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objectid (ID)** żądanych zasad. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Zasady aplikacji
W przypadku zasad aplikacji można użyć następujących poleceń cmdlet.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
Łączy określone zasady z aplikacją.

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objectid (ID)** aplikacji. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** Identyfikator obiektu zasad. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Pobiera zasady przypisane do aplikacji.

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objectid (ID)** aplikacji. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Usuwa zasady z aplikacji.

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objectid (ID)** aplikacji. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** Identyfikator obiektu zasad. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Zasady dotyczące nazwy głównej usługi
W przypadku zasad głównych usługi można użyć następujących poleceń cmdlet.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Łączy określone zasady z jednostką usługi.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objectid (ID)** aplikacji. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** Identyfikator obiektu zasad. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Pobiera wszystkie zasady połączone z określoną jednostką usługi.

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objectid (ID)** aplikacji. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Usuwa zasady z określonej nazwy głównej usługi.

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objectid (ID)** aplikacji. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** Identyfikator obiektu zasad. | `-PolicyId <ObjectId of Policy>` |
