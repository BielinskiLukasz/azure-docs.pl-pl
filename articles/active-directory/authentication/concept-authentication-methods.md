---
title: Metody uwierzytelniania — usługi Azure Active Directory
description: Jakie metody uwierzytelniania są dostępne w usłudze Azure AD do uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6bd67e500756fe5a7ba5ee29db88b9aedb103e4
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315984"
---
# <a name="what-are-authentication-methods"></a>Jakie są metody uwierzytelniania?

Usługi Azure AD samoobsługowego resetowania haseł (SSPR) i usługi Multi-Factor Authentication (MFA) może poprosić o dodatkowe informacje, znane jako metody uwierzytelniania lub informacje zabezpieczające, aby potwierdzić, że osoby Załóżmy, że podczas używania funkcji skojarzone.

Administratorzy mogą definiować w zasadach, które metody uwierzytelniania są dostępne dla użytkowników funkcji samoobsługowego resetowania HASEŁ i uwierzytelniania Wieloskładnikowego. Niektórych metod uwierzytelniania nie może być dostępna dla wszystkich funkcji.

Firma Microsoft zdecydowanie zaleca się Administratorzy umożliwianie użytkownikom, aby wybrać więcej niż minimalna wymagana liczba metod uwierzytelniania w przypadku, gdy nie mają dostępu do jednego.

|Metoda uwierzytelniania|Sposób użycia|
| --- | --- |
| Hasło | Uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ |
| Pytania zabezpieczające | Tylko samoobsługowego resetowania HASEŁ |
| Adres e-mail | Tylko samoobsługowego resetowania HASEŁ |
| Aplikacja Microsoft Authenticator | Uwierzytelnianie wieloskładnikowe i publicznej wersji zapoznawczej na potrzeby samoobsługowego resetowania HASŁA |
| Token sprzętowy OATH | Publicznej wersji zapoznawczej dla uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ |
| SMS | Uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ |
| Połączenie głosowe | Uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ |
| Hasła aplikacji | Uwierzytelnianie wieloskładnikowe tylko w niektórych przypadkach |

![Metod uwierzytelniania używanych na ekranie logowania](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| Tokeny sprzętowe OATH dla uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ i telefon komórkowy powiadomienia przez aplikacje lub kod aplikacji mobilnej jako metody haseł usługi Azure AD resetowania są w publicznej wersji zapoznawczej funkcji usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="password"></a>Hasło

Hasło do usługi Azure AD jest traktowany jako metodę uwierzytelniania. Jest jedną metodę, **nie można wyłączyć**.

## <a name="security-questions"></a>Pytania zabezpieczające

Pytania zabezpieczające są dostępne **tylko w haseł usługi Azure AD z samoobsługowego resetowania** do kont użytkowników niebędących administratorami.

Jeśli używasz pytań zabezpieczających, firma Microsoft zaleca używanie ich razem z innej metody. Pytania zabezpieczeń może być mniej bezpieczne niż inne metody, ponieważ niektóre osoby mogą znać odpowiedzi na pytania innego użytkownika.

> [!NOTE]
> Pytania zabezpieczające są przechowywane przez użytkowników i bezpiecznie w obiekcie użytkownika w katalogu, a tylko można uzyskać przez użytkowników podczas rejestracji. Nie ma możliwości dla administratora odczytać lub zmodyfikować pytań i odpowiedzi użytkownika.
>

### <a name="predefined-questions"></a>Wstępnie zdefiniowane pytania

* W jakim mieście poznałeś/poznałaś swoją pierwszą współmałżonkę lub partnerkę albo swojego pierwszego współmałżonka lub partnera?
* W jakim mieście poznali się Twoi rodzice?
* W jaki mieście mieszka Twoje najbliższe rodzeństwo?
* W jakim mieście urodził się Twój ojciec?
* W jakim mieście podjąłeś/podjęłaś swoją pierwszą pracę?
* W jakim mieście urodziła się Twoja matka?
* W jakim mieście byłeś/byłaś w Nowy Rok w 2000 r.?
* Jak miał na nazwisko Twój ulubiony nauczyciel w szkole średniej?
* Na jaką uczelnię próbowałeś/próbowałaś się dostać, ale się nie udało?
* Gdzie odbyło się Twoje pierwsze wesele?
* Jakie jest drugie imię Twojego ojca?
* Jakie jest Twoje ulubione danie?
* Jak ma na imię i nazwisko Twoja babcia od strony matki?
* Jakie jest drugie imię Twojej matki?
* Co to jest urodzin miesiąca i roku Twojego najstarszego rodzeństwa? (na przykład listopad 1985)
* Jakie jest drugie imię Twojego najstarszego rodzeństwa?
* Jak ma na imię i nazwisko Twój dziadek od strony ojca?
* Jakie jest drugie imię Twojego najmłodszego rodzeństwa?
* Do jakiej szkoły uczęszczałeś/uczęszczałaś w szóstej klasie?
* Jak miał na imię i nazwisko Twój najlepszy przyjaciel lub przyjaciółka w dzieciństwie?
* Jakie było imię i nazwisko Twojego pierwszego partnera lub Twojej pierwszej partnerki?
* Jak miał na nazwisko Twój ulubiony nauczyciel w szkole podstawowej?
* Jakie były marka i model Twojego pierwszego samochodu lub motocykla?
* Jak się nazywała Twoja pierwsza szkoła?
* Jak się nazywał szpital, w którym się urodziłeś/urodziłaś?
* Przy jakiej ulicy znajdował się Twój pierwszy dom z dzieciństwa?
* Jak się nazywał Twój bohater z dzieciństwa?
* Jak się nazywała Twoja ulubiona maskotka?
* Jak się wabiło Twoje pierwsze zwierzę domowe?
* Jaki był Twój pseudonim w dzieciństwie?
* Jaki był Twój ulubiony sport w szkole średniej?
* Jaka była Twoja pierwsza praca?
* Jakie były cztery ostatnie cyfry Twojego numeru telefonu z dzieciństwa?
* Kim chciałeś/chciałaś zostać w dzieciństwie, gdy dorośniesz?
* Jak się nazywa najpopularniejsza poznana przez Ciebie osoba?

Wstępnie zdefiniowane pytania zabezpieczające są przetłumaczone i zlokalizowane do pełnego zestawu języków usługi Office 365 na podstawie ustawień regionalnych przeglądarki przez użytkownika.

### <a name="custom-security-questions"></a>Niestandardowe pytania zabezpieczające

Niestandardowe pytania zabezpieczające nie są zlokalizowane. Wszystkie niestandardowe pytania są wyświetlane w tym samym języku wprowadzoną w interfejsie użytkownika administracyjnego, nawet wtedy, gdy ustawienia regionalne przeglądarki użytkownika jest inny. Jeśli potrzebujesz zlokalizowanych pytania, należy użyć wstępnie zdefiniowane pytania.

Maksymalna długość zapytania niestandardowe zabezpieczeń to 200 znaków.

### <a name="security-question-requirements"></a>Wymagania dotyczące pytania zabezpieczeń

* Odpowiedź minimalny limit znaków to trzy znaki.
* Odpowiedź maksymalny limit znaków to 40 znaków.
* Użytkownicy nie może odebrać tego samego zapytania więcej niż jeden raz.
* Użytkownicy nie można podać tej samej odpowiedzi na więcej niż jedno pytanie.
* Dowolny zestaw znaków może służyć do definiowania pytań i odpowiedzi, w tym znaki Unicode.
* Liczba pytań zdefiniowanych przez musi być większa lub równa liczbie pytań, które były potrzebne do zarejestrowania.

## <a name="email-address"></a>Adres e-mail

Adres e-mail jest dostępna **tylko w haseł usługi Azure AD z samoobsługowego resetowania**.

Firma Microsoft zaleca korzystanie z konta e-mail, który nie wymaga usługi Azure AD hasła dostępu do.

## <a name="microsoft-authenticator-app"></a>Aplikacja Microsoft Authenticator

Aplikacja Microsoft Authenticator zapewnia dodatkowy poziom zabezpieczeń do pracy usługi Azure AD lub konta służbowego lub konta Microsoft.

Aplikacja Microsoft Authenticator jest dostępna dla systemów [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) i [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071).

> [!NOTE]
> Użytkownicy nie będą mieli możliwość zarejestrowania aplikacji mobilnej, gdy rejestrowanie na potrzeby samoobsługowego resetowania haseł. Zamiast tego użytkownicy mogą rejestrować się w aplikacji mobilnej na [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup) lub w wersji zapoznawczej rejestracji informacji zabezpieczeń w [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo).
>

### <a name="notification-through-mobile-app"></a>Powiadomienie przez aplikację mobilną

Aplikacja Microsoft Authenticator może pomóc zapobiec nieautoryzowanemu dostępowi do konta i Zatrzymaj fałszywe transakcje, wypychanie powiadomień na smartfonie lub tablecie. Użytkownicy wyświetlić powiadomienie i jeśli jest to uzasadnione, wybierz opcję Sprawdź. W przeciwnym razie użytkownik może wybrać pozycję Odmów.

> [!WARNING]
> Samoobsługowe resetowanie haseł podczas tylko jedną z metod jest wymagana do resetowania, aby uzyskać kod weryfikacyjny jest jedyną opcją, dostępne dla użytkowników **aby zapewnić najwyższy poziom zabezpieczeń**.
>
> Gdy wymagane są dwie metody użytkownicy będą mogli resetować przy użyciu **albo** powiadomień **lub** kod weryfikacyjny, oprócz innych włączone metody.
>

Jeśli korzystanie z obu powiadomienia za pomocą aplikacji mobilnej oraz kodu weryfikacyjnego z aplikacji mobilnej, użytkowników, którzy rejestracji aplikacji Microsoft Authenticator, przy użyciu powiadomienia będą mogli Użyj powiadomienie i kod, aby zweryfikować swoją tożsamość.

### <a name="verification-code-from-mobile-app"></a>Kod weryfikacyjny z aplikacji mobilnej

Aplikacja Microsoft Authenticator lub innych aplikacjach innych firm może służyć jako token oprogramowania do wygenerowania kodu weryfikacyjnego OATH. Po wprowadzeniu nazwy użytkownika i hasła, możesz wprowadzić kod zapewnianych przez aplikację na ekranie logowania. Kod weryfikacyjny zawiera drugiej formy uwierzytelniania.

> [!WARNING]
> Dla samoobsługowego resetowania haseł podczas tylko jedną z metod jest wymagany na potrzeby resetowania kod weryfikacyjny jest jedyną opcją, dostępne dla użytkowników **aby zapewnić najwyższy poziom zabezpieczeń**.
>

Użytkownicy mogą mieć kombinację tokenów OATH do 5 w sprzętu lub aplikacji wystawcy uwierzytelnienia, takich jak aplikacja Microsoft Authenticator skonfigurowany do użycia w dowolnym momencie.

## <a name="oath-hardware-tokens-public-preview"></a>Tokeny sprzętowe OATH (publiczna wersja zapoznawcza)

OATH jest otwarty standard, który określa sposób jednorazowe hasła (OTP) kody są generowane. Usługa Azure AD będzie obsługiwać korzystanie z tokenów OATH-TOTP SHA-1 odmiany 30 sekund lub 60 sekund. Klienci mogą uzyskać te tokeny od dostawcy wybranych przez nich. Należy pamiętać, że klucze tajne są ograniczone do 128 znaków, które mogą nie być zgodna z wszystkich tokenów.

![Przekazywanie tokenów OATH do bloku tokenów OATH serwera usługi MFA w witrynie Azure portal](media/concept-authentication-methods/oath-tokens-azure-ad.png)

Tokeny sprzętowe OATH są są obsługiwane w ramach publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Po drogą kupna tokeny muszą być przesłane w formacie wartości rozdzielanych przecinkami (CSV) jako przykład poniżej przedstawiono w tym nazwy UPN, numer seryjny, klucz tajny, interwał czasu, producenta i modelu.

```
upn,serial number,secret key,timeinterval,manufacturer,model
Helga@contoso.com,1234567,1234567890abcdef1234567890abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Upewnij się, że wiersz nagłówka w pliku CSV, jak pokazano powyżej.

Raz niepoprawnie sformatowany jako plik CSV, administrator może, a następnie zaloguj się do witryny Azure portal i przejdź do **usługi Azure Active Directory**, **serwera MFA**, **tokenów OATH**, i Przekaż wynikowy plik CSV.

W zależności od rozmiaru pliku CSV może upłynąć kilka minut, aby przetworzyć. Kliknij przycisk **Odśwież** przycisk, aby wyświetlić bieżący stan. Jeśli występują błędy w pliku, masz możliwość pobrania plik CSV z listą wszelkie błędy, które należy rozwiązać.

Gdy zostały rozwiązane wszystkie błędy, administrator następnie można uruchomić każdy klucz klikając **Aktywuj** token zostanie uaktywniony i wprowadzanie kodu OTP jest wyświetlany w tokenie.

Użytkownicy mogą mieć kombinację tokenów OATH do 5 w sprzętu lub aplikacji wystawcy uwierzytelnienia, takich jak aplikacja Microsoft Authenticator skonfigurowany do użycia w dowolnym momencie.

## <a name="mobile-phone"></a>Telefon komórkowy

Dwie opcje są dostępne dla użytkowników przy użyciu telefonów komórkowych.

Jeśli użytkownicy nie chcą numeru telefonu komórkowego, która będzie widoczna w katalogu, ale mimo to chcą z niej korzystać w celu zresetowania hasła, wypełnij Administratorzy powinna nie w katalogu. Należy wypełnić użytkownikom ich **numer telefonu uwierzytelniania** atrybutu za pośrednictwem [portalu rejestracji resetowania haseł](https://aka.ms/ssprsetup). Administratorzy mogą zobaczyć te informacje w profilu użytkownika, ale nie jest publikowany gdzie indziej.

Aby działać poprawnie, numerów telefonów musi być w formacie *+ CountryCode PhoneNumber*, na przykład 4255551234 + 1.

> [!NOTE]
> Musi istnieć odstęp między kod kraju i numer telefonu.
>
> Resetowanie hasła nie obsługuje wewnętrzne numery telefonów. Nawet w formacie 12345 4255551234 + 1 X rozszerzenia zostaną usunięte przed wykonaniem wywołania.

### <a name="text-message"></a>Wiadomość SMS

Wiadomość SMS są wysyłane pod numerem telefonu komórkowego z kodem weryfikacyjnym. Wprowadź kod weryfikacyjny w interfejsie logowania, aby kontynuować.

### <a name="phone-call"></a>Połączenie telefoniczne

Automatyczne połączenie głosowe wykonano pod numer telefonu, których udzielasz. Odebranie połączenia i naciśnięcie przycisku # na klawiaturze telefonu w celu uwierzytelnienia

> [!IMPORTANT]
> Począwszy od marca 2019 opcji połączenia telefonicznego nie będą dostępne dla uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ użytkowników w dzierżawach bezpłatnej/wersji próbnej usługi Azure AD. Ta zmiana nie wpływa na wiadomości SMS. Połączenie telefoniczne będą nadal dostępne dla użytkowników w płatną dzierżaw usługi Azure AD. Ta zmiana ma wpływ tylko na dzierżaw bezpłatnej/wersji próbnej usługi Azure AD.

## <a name="office-phone"></a>Telefon służbowy

Automatyczne połączenie głosowe wykonano pod numer telefonu, których udzielasz. Odebrać połączenie i naciska klawisz # na klawiaturze telefonu w celu uwierzytelnienia.

Aby działać poprawnie, numerów telefonów musi być w formacie *+ CountryCode PhoneNumber*, na przykład 4255551234 + 1.

Atrybut telefonu pakietu office jest zarządzane przez administratora.

> [!IMPORTANT]
> Począwszy od marca 2019 opcji połączenia telefonicznego nie będą dostępne dla uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ użytkowników w dzierżawach bezpłatnej/wersji próbnej usługi Azure AD. Ta zmiana nie wpływa na wiadomości SMS. Połączenie telefoniczne będą nadal dostępne dla użytkowników w płatną dzierżaw usługi Azure AD. Ta zmiana ma wpływ tylko na dzierżaw bezpłatnej/wersji próbnej usługi Azure AD.

> [!NOTE]
> Musi istnieć odstęp między kod kraju i numer telefonu.
>
> Resetowanie hasła nie obsługuje wewnętrzne numery telefonów. Nawet w formacie 12345 4255551234 + 1 X rozszerzenia zostaną usunięte przed wykonaniem wywołania.

## <a name="app-passwords"></a>Hasła aplikacji

Niektóre aplikacje nie korzystające z przeglądarki nie obsługuje uwierzytelnianie wieloskładnikowe, jeśli użytkownik został włączony dla usługi Multi-Factor authentication i podejmują próbę użycia aplikacji niekorzystających z przeglądarki, są one nie można uwierzytelnić. Hasła aplikacji pozwala użytkownikom na uwierzytelnianie w dalszym ciągu

Jeśli wymuszanie uwierzytelniania wieloskładnikowego za pomocą zasad dostępu warunkowego, a nie za pomocą usługi MFA na użytkownika, nie można utworzyć hasła aplikacji. Aplikacje używające zasad dostępu warunkowego do kontrolowania dostępu do hasła aplikacji nie jest konieczne.

Jeśli Twoja organizacja jest sfederowana z logowania jednokrotnego z usługą Azure AD i zamierzasz używać usługi Azure MFA, następnie należy pamiętać o następujących szczegółach:

* Hasło aplikacji jest weryfikowana przez usługę Azure AD i w związku z tym pomija federacji. Federacyjny jest używana tylko podczas konfigurowania hasła aplikacji. Dla użytkowników federacyjnych (SSO) hasła są przechowywane w identyfikatorze organizacyjnym. Jeśli użytkownik opuści firmę, że informacje o musi przepływać do Identyfikatora organizacyjnego przy użyciu narzędzia DirSync. Wyłączenie/usunięcie konta może potrwać do trzech godzin do synchronizacji, w którym opóźnienie wyłączenia/usunięcia haseł aplikacji w usłudze Azure AD.
* Ustawienia lokalnej kontroli dostępu klienta nie są uznawane przez hasło aplikacji.
* Możliwość rejestrowania/inspekcji nie lokalnego uwierzytelniania jest dostępna dla hasła aplikacji.
* Niektóre zaawansowane architektury projekty mogą wymagać przy użyciu kombinacji organizacji nazwy użytkownika i hasła oraz haseł aplikacji, gdy z klientami, w zależności od tego, gdzie się uwierzytelniają przy użyciu weryfikacji dwuetapowej. Dla klientów, którzy uwierzytelniać się na infrastrukturę lokalną należy użyć organizacji użytkownika i hasło. W przypadku klientów, które przeprowadzają uwierzytelnianie w usłudze Azure AD należy użyć hasła aplikacji.
* Domyślnie użytkownicy nie mogą tworzyć hasła aplikacji. Jeśli chcesz zezwolić użytkownikom na tworzenie haseł aplikacji, wybierz opcję **Zezwalaj użytkownikom na tworzenie haseł aplikacji do logowania się do aplikacji niekorzystających z przeglądarki, opcja** w obszarze Ustawienia usługi.

## <a name="next-steps"></a>Kolejne kroki

[Włącz samoobsługowego resetowania hasła dla Twojej organizacji](quickstart-sspr.md)

[Włącz uwierzytelnianie wieloskładnikowe Azure dla Twojej organizacji](howto-mfa-getstarted.md)

[Zezwolenie na zarejestrowanie połączone w ramach dzierżawy](howto-registration-mfa-sspr-combined.md)

[Dokumentacja konfiguracji metody uwierzytelniania użytkownika](https://aka.ms/securityinfoguide)
