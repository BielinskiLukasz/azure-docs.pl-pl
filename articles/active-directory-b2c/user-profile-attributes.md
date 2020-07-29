---
title: Atrybuty profilu użytkownika w Azure Active Directory B2C
description: Dowiedz się więcej o atrybutach typu zasobu użytkownika obsługiwanych przez profil użytkownika katalogu Azure AD B2C. Dowiedz się więcej na temat wbudowanych atrybutów, rozszerzeń i sposobu mapowania atrybutów na Microsoft Graph.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1e6965e15b7482935148ae7fcd2edf0f3cc722b2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83738561"
---
# <a name="user-profile-attributes"></a>Atrybuty profilu użytkownika

Profil użytkownika katalogu usługi Azure Active Directory (Azure AD) B2C zawiera wbudowany zestaw atrybutów, takich jak imię i nazwisko, nazwisko, miasto, kod pocztowy i numer telefonu. Profil użytkownika można rozłożyć na własne dane aplikacji bez konieczności korzystania z zewnętrznego magazynu danych. Większość atrybutów, które mogą być używane z Azure AD B2C profile użytkowników, jest również obsługiwanych przez Microsoft Graph. W tym artykule opisano obsługiwane Azure AD B2C atrybuty profilu użytkownika. Są w nim również opisane atrybuty, które nie są obsługiwane przez Microsoft Graph, a także atrybuty Microsoft Graph, które nie powinny być używane z Azure AD B2C.

> [!IMPORTANT]
> Nie należy używać atrybutów wbudowanych ani rozszerzeń do przechowywania poufnych danych osobowych, takich jak poświadczenia konta, numery identyfikacyjne instytucji rządowych, dane dotyczące posiadaczy kart, dane konta finansowego, informacje o opiece zdrowotnej lub poufne informacje w tle.

Można także zintegrować z systemami zewnętrznymi. Na przykład można użyć Azure AD B2C do uwierzytelniania, ale delegować do zewnętrznego źródła zarządzania relacjami z klientami (CRM) lub z usługi lojalnościowej klienta jako autorytatywne źródło danych klienta. Aby uzyskać więcej informacji, zobacz Rozwiązanie [profilu zdalnego](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) .

W poniższej tabeli wymieniono atrybuty [typu zasobu użytkownika](https://docs.microsoft.com/graph/api/resources/user) , które są obsługiwane przez profil użytkownika katalogu Azure AD B2C. Podaje następujące informacje dotyczące każdego atrybutu:

- Nazwa atrybutu używana przez Azure AD B2C (po którym następuje nazwa Microsoft Graph w nawiasach, jeśli różni się)
- Typ danych atrybutu
- Opis atrybutu
- Jeśli atrybut jest dostępny w Azure Portal
- Jeśli atrybut może być używany w przepływie użytkownika
- Jeśli atrybut może być używany w ramach zasad niestandardowych [profilu usługi Azure AD](active-directory-technical-profile.md) i w którym sekcji ( &lt; InputClaims &gt; , &lt; OutputClaims &gt; lub &lt; PersistedClaims &gt; )

|Nazwa     |Typ     |Opis|Azure Portal|Przepływy użytkowników|Zasady niestandardowe|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boolean|Niezależnie od tego, czy konto użytkownika jest włączone, czy wyłączone: **prawda** , jeśli konto jest włączone, w przeciwnym razie **false**.|Yes|Nie|Utrwalony, wyjściowy|
|grupa_wiekowa        |String|Grupa wiekowa użytkownika. Możliwe wartości: null, undefined, moll, dorosły, NotAdult.|Yes|Nie|Utrwalony, wyjściowy|
|alternativeSecurityId ([tożsamości](manage-user-accounts-graph-api.md#identities-property))|String|Tożsamość pojedynczego użytkownika od zewnętrznego dostawcy tożsamości.|Nie|Nie|Dane wejściowe, utrwalone i wyjściowe|
|alternativeSecurityIds ([tożsamości](manage-user-accounts-graph-api.md#identities-property))|Alternatywna kolekcja securityId|Kolekcja tożsamości użytkowników od zewnętrznych dostawców tożsamości.|Nie|Nie|Utrwalony, wyjściowy|
|city            |String|Miasto, w którym znajduje się użytkownik. Maksymalna długość 128.|Tak|Tak|Utrwalony, wyjściowy|
|consentProvidedForMinor|String|Czy podano zgodę dla elementu pomocniczego. Dozwolone wartości: null, udzielono, odmowy lub notRequired.|Yes|Nie|Utrwalony, wyjściowy|
|country         |String|Kraj/region, w którym znajduje się użytkownik. Przykład: "US" lub "UK". Maksymalna długość 128.|Tak|Tak|Utrwalony, wyjściowy|
|createdDateTime|DateTime|Data utworzenia obiektu użytkownika. Tylko do odczytu.|Nie|Nie|Utrwalony, wyjściowy|
|Jeżeli    |String|Jeśli konto użytkownika zostało utworzone jako konto lokalne dla dzierżawy Azure Active Directory B2C, wartość to LocalAccount lub nameCoexistence. Tylko do odczytu.|Nie|Nie|Utrwalony, wyjściowy|
|dateOfBirth     |Data|Data urodzenia.|Nie|Nie|Utrwalony, wyjściowy|
|działu,      |String|Nazwa działu, w którym pracuje użytkownik. Maksymalna długość 64.|Yes|Nie|Utrwalony, wyjściowy|
|displayName     |String|Nazwa wyświetlana użytkownika. Maksymalna długość 256.|Tak|Tak|Utrwalony, wyjściowy|
|facsimileTelephoneNumber<sup>1</sup>|String|Numer telefonu służbowego komputera faksowego użytkownika.|Yes|Nie|Utrwalony, wyjściowy|
|givenName       |String|Imię i nazwisko (imię) użytkownika. Maksymalna długość 64.|Tak|Tak|Utrwalony, wyjściowy|
|Stanowiska        |String|Stanowisko użytkownika. Maksymalna długość 128.|Tak|Tak|Utrwalony, wyjściowy|
|immutableId     |String|Identyfikator, który jest zazwyczaj używany w przypadku użytkowników migrowanych z Active Directory lokalnych.|Nie|Nie|Utrwalony, wyjściowy|
|legalAgeGroupClassification|String|Klasyfikacja grupy wiek prawny. Tylko do odczytu i obliczone na podstawie właściwości grupy wiekowej i consentProvidedForMinor. Dozwolone wartości: null, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult i Dorosła.|Yes|Nie|Utrwalony, wyjściowy|
|legalCountry<sup>1</sup>  |String|Kraj/region do celów prawnych.|Nie|Nie|Utrwalony, wyjściowy|
|mail (poczta)            |String|Adres SMTP użytkownika, na przykład " bob@contoso.com ". Tylko do odczytu.|Nie|Nie|Utrwalony, wyjściowy|
|mailNickName    |String|Alias poczty dla użytkownika. Maksymalna długość 64.|Nie|Nie|Utrwalony, wyjściowy|
|Mobile (mobilePhone) |String|Podstawowy numer telefonu komórkowego użytkownika. Maksymalna długość 64.|Yes|Nie|Utrwalony, wyjściowy|
|netId           |String|Identyfikator sieci.|Nie|Nie|Utrwalony, wyjściowy|
|Obiektu        |String|Unikatowy identyfikator globalny (GUID), który jest unikatowym identyfikatorem dla użytkownika. Przykład: 12345678-9ABC-DEF0-1234-56789abcde. Tylko do odczytu, niezmienny.|Tylko do odczytu|Tak|Dane wejściowe, utrwalone i wyjściowe|
|otherMails      |Kolekcja ciągów|Lista dodatkowych adresów e-mail dla użytkownika. Przykład: [" bob@contoso.com ", " Robert@fabrikam.com "].|Tak (alternatywny adres e-mail)|Nie|Utrwalony, wyjściowy|
|hasło        |String|Hasło konta lokalnego podczas tworzenia użytkownika.|Nie|Nie|Trwały|
|passwordPolicies     |String|Zasady dotyczące hasła. Jest to ciąg składający się z różnych nazw zasad oddzielonych przecinkami. tj. "DisablePasswordExpiration, DisableStrongPassword".|Nie|Nie|Utrwalony, wyjściowy|
|physicalDeliveryOfficeName (officeLocation)|String|Lokalizacja biura w miejscu pracy użytkownika. Maksymalna długość 128.|Yes|Nie|Utrwalony, wyjściowy|
|Pocztowy      |String|Kod pocztowy dla adresu pocztowego użytkownika. Kod pocztowy jest specyficzny dla kraju/regionu użytkownika. W Stany Zjednoczone w Ameryce ten atrybut zawiera kod pocztowy. Maksymalna długość 40.|Yes|Nie|Utrwalony, wyjściowy|
|preferredLanguage    |String|Preferowany język użytkownika. Powinien być zgodny z kodem ISO 639-1. Przykład: "en-US".|Nie|Nie|Utrwalony, wyjściowy|
|refreshTokensValidFromDateTime|DateTime|Wszystkie tokeny odświeżania wystawione przed tym czasem są nieprawidłowe, a w przypadku korzystania z nieprawidłowego tokenu odświeżania aplikacje będą otrzymywać błędy. W takim przypadku aplikacja będzie musiała uzyskać nowy token odświeżania, wysyłając żądanie do punktu końcowego autoryzacji. Tylko do odczytu.|Nie|Nie|Dane wyjściowe|
|signInNames ([tożsamości](manage-user-accounts-graph-api.md#identities-property)) |String|Unikatowa nazwa logowania użytkownika konta lokalnego dowolnego typu w katalogu. Użyj tej opcji, aby uzyskać użytkownika z wartością logowania bez określenia typu konta lokalnego.|Nie|Nie|Dane wejściowe|
|signInNames. userName ([tożsamości](manage-user-accounts-graph-api.md#identities-property)) |String|Unikatowa nazwa użytkownika konta lokalnego w katalogu. Użyj tego, aby utworzyć lub pobrać użytkownika z określoną nazwą użytkownika logowania. Określenie tej opcji w PersistedClaims samej podczas operacji patch spowoduje usunięcie innych typów signInNames. Jeśli chcesz dodać nowy typ signInNames, musisz również zachować istniejące signInNames.|Nie|Nie|Dane wejściowe, utrwalone i wyjściowe|
|signInNames. numer telefonu ([tożsamości](manage-user-accounts-graph-api.md#identities-property)) |String|Unikatowy numer telefonu użytkownika konta lokalnego w katalogu. Użyj tego, aby utworzyć lub uzyskać użytkownika z określonym numerem telefonu logowania. Określenie tej opcji w PersistedClaims samej podczas operacji patch spowoduje usunięcie innych typów signInNames. Jeśli chcesz dodać nowy typ signInNames, musisz również zachować istniejące signInNames.|Nie|Nie|Dane wejściowe, utrwalone i wyjściowe|
|signInNames. emailAddress ([tożsamości](manage-user-accounts-graph-api.md#identities-property))|String|Unikatowy adres e-mail użytkownika konta lokalnego w katalogu. Użyj tego elementu, aby utworzyć lub uzyskać użytkownika z określonym adresem e-mail logowania. Określenie tej opcji w PersistedClaims samej podczas operacji patch spowoduje usunięcie innych typów signInNames. Jeśli chcesz dodać nowy typ signInNames, musisz również zachować istniejące signInNames.|Nie|Nie|Dane wejściowe, utrwalone i wyjściowe|
|state           |String|Województwo w adresie użytkownika. Maksymalna długość 128.|Tak|Tak|Utrwalony, wyjściowy|
|streetAddress   |String|Ulica siedziby firmy użytkownika. Maksymalna długość 1024.|Tak|Tak|Utrwalony, wyjściowy|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|String|Dodatkowy numer telefonu użytkownika używany do uwierzytelniania wieloskładnikowego.|Yes|Nie|Utrwalony, wyjściowy|
|strongAuthenticationEmailAddress<sup>1</sup>|String|Adres SMTP użytkownika. Przykład: " bob@contoso.com " ten atrybut jest używany do logowania przy użyciu zasad username, aby przechowywać adres e-mail użytkownika. Adres e-mail używany w ramach przepływu resetowania hasła.|Yes|Nie|Utrwalony, wyjściowy|
|strongAuthenticationPhoneNumber<sup>1</sup>|String|Podstawowy numer telefonu użytkownika używany do uwierzytelniania wieloskładnikowego.|Yes|Nie|Utrwalony, wyjściowy|
|surname         |String|Nazwisko użytkownika (Nazwa rodziny lub nazwisko). Maksymalna długość 64.|Tak|Tak|Utrwalony, wyjściowy|
|numer telefonu (pierwszy wpis businessPhones)|String|Podstawowy numer telefonu miejsca pracy użytkownika.|Yes|Nie|Utrwalony, wyjściowy|
|userPrincipalName    |String|Główna nazwa użytkownika (UPN). Nazwa UPN jest nazwą logowania użytkownika w stylu internetowym opartą na standardzie internetowym RFC 822. Domena musi być obecna w kolekcji zweryfikowanych domen dzierżawcy. Ta właściwość jest wymagana podczas tworzenia konta. Niezmienne.|Nie|Nie|Dane wejściowe, utrwalone i wyjściowe|
|usageLocation   |String|Wymagane dla użytkowników, którzy będą przypisani do licencji z powodu wymogu prawnego do sprawdzenia dostępności usług w krajach/regionach. Nie dopuszcza wartości null. Dwuliterowy kod kraju/regionu (ISO standard 3166). Przykłady: "US", "JP" i "GB".|Yes|Nie|Utrwalony, wyjściowy|
|userType        |String|Wartość ciągu, która może służyć do klasyfikowania typów użytkowników w katalogu. Wartość musi być elementem członkowskim. Tylko do odczytu.|Tylko do odczytu|Nie|Utrwalony, wyjściowy|
|userState (externalUserState)<sup>2</sup>|String|Tylko w przypadku konta usługi Azure AD B2B wskazuje, czy zaproszenie jest PendingAcceptance czy zaakceptowane.|Nie|Nie|Utrwalony, wyjściowy|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|Pokazuje sygnaturę czasową dla najnowszej zmiany właściwości UserState.|Nie|Nie|Utrwalony, wyjściowy|
|<sup>1</sup> Nieobsługiwane przez Microsoft Graph<br><sup>2</sup> Nie powinien być używany z Azure AD B2C||||||


## <a name="extension-attributes"></a>Atrybuty rozszerzenia

Często trzeba utworzyć własne atrybuty, tak jak w następujących przypadkach:

- Aplikacja mająca dostęp do klienta musi zachować dostęp do atrybutu, takiego jak **LoyaltyNumber**.
- Dostawca tożsamości ma unikatowy identyfikator użytkownika, taki jak **uniqueUserGUID** , który musi zostać zapisany.
- Niestandardowa podróż użytkownika musi utrzymywać się w stanie użytkownika, na przykład **migrationStatus**.

Azure AD B2C rozszerza zestaw atrybutów przechowywanych na poszczególnych kontach użytkowników. Atrybuty rozszerzenia [rozszerzają schemat](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions) obiektów użytkownika w katalogu. Atrybuty rozszerzenia mogą być rejestrowane tylko w obiekcie aplikacji, nawet jeśli mogą zawierać dane dla użytkownika. Atrybut rozszerzenia jest dołączany do aplikacji o nazwie B2C-Extensions-App. Nie należy modyfikować tej aplikacji, ponieważ jest ona używana przez Azure AD B2C do przechowywania danych użytkownika. Tę aplikację można znaleźć w obszarze Azure Active Directory Rejestracje aplikacji.

> [!NOTE]
> - Do 100 atrybutów rozszerzeń można zapisywać na dowolnym koncie użytkownika.
> - Jeśli aplikacja B2C-Extensions-App zostanie usunięta, te atrybuty rozszerzenia zostaną usunięte ze wszystkich użytkowników wraz z wszelkimi zawartymi w nich danymi.
> - Jeśli atrybut rozszerzenia zostanie usunięty przez aplikację, zostanie on usunięty z wszystkich kont użytkowników i wartości są usuwane.
> - Podstawowa nazwa atrybutu rozszerzenia jest generowana w formacie "Extension_" + Identyfikator aplikacji + "_" + nazwa atrybutu. Na przykład, jeśli utworzysz atrybut rozszerzenia LoyaltyNumber, a identyfikator aplikacji B2C-Extensions-App to 831374b3-bd50-41bf-aa54-263ec9e050fc, nazwa podstawowego atrybutu rozszerzenia będzie: extension_831374b3bd5041bfaa54263ec9e050fc_LoyaltyNumber. Nazwa podstawowa jest używana podczas uruchamiania zapytań interfejs API programu Graph w celu utworzenia lub zaktualizowania kont użytkowników.

Podczas definiowania właściwości w rozszerzeniu schematu obsługiwane są następujące typy danych:

|Typ właściwości |Uwagi  |
|--------------|---------|
|Boolean    | Możliwe wartości: **true** lub **false**. |
|DateTime   | Musi być określony w formacie ISO 8601. Będą przechowywane w formacie UTC.   |
|Integer    | 32 — wartość bitowa.               |
|String     | maksymalnie 256 znaków.     |

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat atrybutów rozszerzenia:
- [Rozszerzenia schematu](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [Definiowanie atrybutów niestandardowych przy użyciu przepływu użytkownika](user-flow-custom-attributes.md)
- [Definiowanie atrybutów niestandardowych przy użyciu zasad niestandardowych](custom-policy-custom-attributes.md)
