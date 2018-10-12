---
title: Konfigurowanie usługi Azure Multi-Factor Authentication | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania ustawień usługi Azure Multi-Factor Authentication w witrynie Azure portal
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: a66a7537632aac2190cd39f13665bcd8d4ed6ce7
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114995"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Konfigurowanie ustawień usługi Azure Multi-Factor Authentication

Ten artykuł ułatwia zarządzanie usługi Azure Multi-Factor Authentication, teraz, kiedy jest uruchomiona. Poruszono w nim różne tematy, które ułatwiają maksymalnie wykorzystać możliwości usługi Azure Multi-Factor Authentication. Nie wszystkie funkcje są dostępne w każdym [wersję usługi Azure Multi-Factor Authentication](concept-mfa-whichversion.md#what-features-do-i-need).

| Cecha | Opis | 
|:--- |:--- |
| [Blokować i odblokowywać użytkowników](#block-and-unblock-users) |Aby uniemożliwić użytkownikom odbieranie żądań uwierzytelniania, należy użyć funkcji Blokowanie/odblokowywanie użytkowników. |
| [Alert oszustwa](#fraud-alert) |Konfigurowanie funkcji alertów oszustwa, dzięki czemu użytkownicy mogą raportować fałszywe próbuje uzyskać dostęp do zasobów. |
| [Jednorazowe obejście](#one-time-bypass) |Użyj funkcji jednorazowe obejście, aby umożliwić użytkownikom uwierzytelnianie jeden raz przez _pomijanie_ Multi-Factor Authentication. |
| [Niestandardowe wiadomości głosowe](#custom-voice-messages) |Funkcja wiadomości niestandardowych voice własnych nagrania lub greetings za pomocą uwierzytelniania wieloskładnikowego. |
| [Pamięć podręczna](#caching-in-azure-multi-factor-authentication) |Funkcja buforowania można ustawić w określonym przedziale czasu, dlatego, że kolejne próby uwierzytelniania powiedzie się automatycznie. |
| [Zaufane adresy IP](#trusted-ips) |Administratorzy dzierżawy zarządzanych lub federacyjnego funkcja zaufane adresy IP pominięciu weryfikacji dwuetapowej dla użytkowników, którzy zalogować się z firmową siecią intranet. |
| [Hasła aplikacji](#app-passwords) |Funkcja haseł aplikacji do włączenia aplikacji pominąć uwierzytelnianie wieloskładnikowe i kontynuować pracę. |
| [Należy pamiętać, uwierzytelnianie wieloskładnikowe dla zaufanych urządzeń i przeglądarki](#remember-multi-factor-authentication-for-trusted-devices) |Użyj tej funkcji do zapamiętania zaufanego urządzenia i przeglądarki przez liczbę dni, po użytkownik pomyślnie zalogowany przy użyciu usługi Multi-Factor Authentication. |
| [Metody weryfikacji można wybierać](#selectable-verification-methods) |Użyj tej funkcji, aby wybrać listę metod uwierzytelniania, które użytkownicy będą mogli używać. |

## <a name="block-and-unblock-users"></a>Blokować i odblokowywać użytkowników

Użyj _blokować i odblokowywać użytkowników_ funkcję, aby uniemożliwić użytkownikom odbieranie żądań uwierzytelniania. Wszelkie próby uwierzytelnienia dla zablokowanych użytkowników są automatycznie odrzucane. Użytkownicy zostaną zablokowane przez 90 dni od momentu zablokowania.

### <a name="block-a-user"></a>Zablokuj użytkownika

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **usługi Azure Active Directory** > **serwera MFA** > **Blokowanie/odblokowywanie użytkowników**.
3. Wybierz **Dodaj** aby uniemożliwić użytkownikowi.
4. Wybierz **grupy replikacji**. Wprowadź nazwę użytkownika dla użytkownika zablokowany jako **username<span></span>@domain.com**. Wprowadź komentarz w **Przyczyna** pola.
5. Wybierz **Dodaj** zakończenie zablokowania użytkownika.

### <a name="unblock-a-user"></a>Odblokuj użytkownika

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **usługi Azure Active Directory** > **serwera MFA** > **Blokowanie/odblokowywanie użytkowników**.
3. Wybierz **odblokowanie** w **akcji** kolumnę obok wyświetlenie monitu o odblokowanie.
4. Wprowadź komentarz w **przyczyny odblokowania** pola.
5. Wybierz **odblokowanie** zakończenie odblokowania użytkownika.

## <a name="fraud-alert"></a>Alert dotyczący wykrycia oszustwa

Konfigurowanie _oszustwa_ funkcji, dzięki czemu użytkownicy mogą raportować fałszywe próbuje uzyskać dostęp do zasobów. Użytkownicy mogą raportować prób oszustwa, przy użyciu aplikacji mobilnej lub przez telefon.

### <a name="turn-on-fraud-alerts"></a>Włączanie alertów oszustwa

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **usługi Azure Active Directory** > **serwera MFA** > **oszustwa**.

   ![Włączanie alertów oszustwa](./media/howto-mfa-mfasettings/fraudalert.png)

3. Ustaw **Zezwól użytkownikom na przesłanie alertów oszustwa** ustawienie **na**.
4. Wybierz pozycję **Zapisz**.

### <a name="configuration-options"></a>Opcje konfiguracji

- **Blokuj użytkownika, gdy zostaje zgłoszone oszustwo**: Jeśli użytkownik zgłosi oszustwa, jego konto zostało zablokowane przez 90 dni lub dopóki administrator odblokowuje swojego konta. Administrator może przejrzeć logowania za pomocą raportów logowania i podjąć odpowiednie działania w celu zapobiegania oszustwom w przyszłości. Administrator może następnie [odblokować](#unblock-a-user) konta użytkownika.
- **Kod zgłoszenia oszustwa w trakcie początkowego pozdrowienia**: gdy użytkownicy otrzymają rozmowę telefoniczną w celu weryfikacji dwuetapowej, zwykle naciśnięciu **#** o potwierdzenie ich logowania. Zgłaszanie nadużyć użytkownik musi wprowadzić kod przed naciśnięcie **#**. Ten kod jest **0** domyślnie, ale można go dostosować.

  >[!NOTE]
  >Pozdrowienia głosowe domyślnej od firmy Microsoft poinstruować użytkowników o naciśnięcie **0#** przesłać oszustwa. Jeśli chcesz użyć innych niż kod **0**, rejestrowania i przekazać własne niestandardowe pozdrowienia głosowe odpowiednie instrukcje dla użytkowników.
  >

### <a name="view-fraud-reports"></a>Wyświetl raporty dotyczące oszustwa

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **usługi Azure Active Directory** > **logowania**. Raport oszustwa jest teraz częścią raportu standardowego logowania usługi Azure AD.

## <a name="one-time-bypass"></a>Jednokrotne obejście

_Jednorazowe obejście_ funkcji umożliwia użytkownikowi uwierzytelnienie jeden raz bez konieczności przeprowadzania weryfikacji dwuetapowej. Obejście jest tymczasowe i wygasa po określonej liczbie sekund. W sytuacjach, w którym aplikacji mobilnej lub telefon nie odbiera powiadomienie lub połączenie telefoniczne można zezwolić jednorazowe obejście, aby użytkownik może uzyskiwać dostęp do żądanego zasobu.

### <a name="create-a-one-time-bypass"></a>Utwórz jednorazowe obejście

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **usługi Azure Active Directory** > **serwera MFA** > **jednorazowe obejście**.

   ![Utwórz jednorazowe obejście](./media/howto-mfa-mfasettings/onetimebypass.png)

3. Wybierz pozycję **Dodaj**.
4. Jeśli to konieczne, zaznacz grupę replikacji obejście.
5. Wprowadź nazwę użytkownika jako **username<span></span>@domain.com**. Wprowadź liczbę sekund, które powinno trwać obejście. Podaj przyczynę obejścia. 
6. Wybierz pozycję **Dodaj**. Limit czasu zacznie obowiązywać natychmiast. Użytkownik musi się zalogować przed wygaśnięciem jednorazowego obejścia. 

### <a name="view-the-one-time-bypass-report"></a>Wyświetlanie raportów jednorazowe obejście

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do **usługi Active Directory** > **serwera MFA** > **jednorazowe obejście**.

## <a name="custom-voice-messages"></a>Niestandardowe wiadomości głosowe
Można użyć własnych nagrania lub greetings weryfikacji dwuetapowej za pomocą _niestandardowe wiadomości głosowe_ funkcji. Te komunikaty można dodatkowo aby lub zastąpić nagrania firmy Microsoft.

Przed rozpoczęciem należy pamiętać o następujących ograniczeniach:

* Obsługiwane formaty plików to .wav lub .mp3.
* Limit rozmiaru pliku to 5 MB.
* Uwierzytelnianie wiadomości powinien być krótszy niż 20 sekund. Komunikaty, które są dłuższe niż 20 sekund może spowodować, że weryfikacja nie powiedzie się. Użytkownik może nie odpowiadać przed zakończeniem komunikat i weryfikacja upłynie limit czasu.

### <a name="custom-message-language-behavior"></a>Zachowanie języka niestandardowego komunikatu

Podczas odtwarzania wiadomości głosowej niestandardowych dla użytkownika, język wiadomości zależy od od następujących czynników:

* Język bieżącego użytkownika.
   * Język, wykrytych przez przeglądarkę użytkownika.
   * Inne scenariusze uwierzytelniania może zachowywać się inaczej.
* Język dostępnych niestandardowych komunikatów o.
   * Ten język jest wybierany przez administratora, gdy niestandardowy komunikat zostanie dodany.

Na przykład, jeśli istnieje tylko jeden niestandardowy komunikat o języku niemieckim:

* Użytkownik, który jest uwierzytelniany w języku niemieckim usłyszysz niestandardowy komunikat niemieckim.
* Użytkownik, który jest uwierzytelniany w języku angielskim usłyszysz wiadomość standard w języku angielskim.

### <a name="set-up-a-custom-message"></a>Skonfigurować niestandardowy komunikat

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
1. Przejdź do **usługi Azure Active Directory** > **serwera MFA** > **ustawień połączenia telefonicznego**.

   ![Rekord telefon niestandardowy wiadomości](./media/howto-mfa-mfasettings/phonecallsettings.png)

1. Wybierz **Dodaj powitanie**.
1. Wybierz typ pozdrowienia. 
1. Wybierz język.
1. Wybierz plik MP3 lub wav dźwiękowego do przekazania.
1. Wybierz pozycję **Dodaj**.

## <a name="caching-in-azure-multi-factor-authentication"></a>Buforowanie w usłudze Azure Multi-Factor Authentication

Możesz ustawić okres, aby umożliwić prób uwierzytelnienia po użytkownik jest uwierzytelniany przy użyciu _buforowania_ funkcji. Kolejnych próbach uwierzytelnienia użytkownika w określonym przedziale czasu powiodła się automatycznie. Buforowanie jest używany głównie do systemów lokalnych, np. sieć VPN wysyłania wielu żądań weryfikacji, gdy pierwsze żądanie jest nadal w toku. Ta funkcja umożliwia kolejne żądania zakończyło się sukcesem automatycznie po użytkownik zakończy się pomyślnie pierwszej weryfikacji w toku. 

>[!NOTE]
>Funkcję pamięci podręcznej nie mają być używane do logowania do usługi Azure Active Directory (Azure AD).

### <a name="set-up-caching"></a>Konfigurowanie pamięci podręcznej 

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **usługi Azure Active Directory** > **serwera MFA** > **reguły buforowania**.

   ![Skonfiguruj reguły buforowania](./media/howto-mfa-mfasettings/cachingrules.png)

3. Wybierz pozycję **Dodaj**.
4. Wybierz **typ pamięci podręcznej** z listy rozwijanej. Wprowadź maksymalną liczbę **sekund w pamięci podręcznej**. 
5. Jeśli to konieczne, wybierz typ uwierzytelniania i określ aplikację. 
6. Wybierz pozycję **Dodaj**.

## <a name="trusted-ips"></a>Zaufane adresy IP

_Zaufane adresy IP_ funkcji usługi Azure Multi-Factor Authentication jest używana przez administratorów dzierżawy zarządzanych lub federacyjnego. Funkcja Pomija weryfikację dwuetapową dla użytkowników, którzy zalogować się z firmową siecią intranet. Ta funkcja jest dostępna z pełną wersję usługi Azure Multi-Factor Authentication i nie bezpłatną wersją dla administratorów. Aby uzyskać szczegółowe informacje dotyczące sposobu uzyskania pełną wersję usługi Azure Multi-Factor Authentication, zobacz [usługi Azure Multi-Factor Authentication](multi-factor-authentication.md).

Jeśli Twoja organizacja wdraża rozszerzenia serwera NPS w celu zapewnienia uwierzytelniania Wieloskładnikowego Uwaga aplikacji w środowisku lokalnym źródłowy adres IP będzie zawsze pojawiają się jako serwer NPS uwierzytelnianie próba odbywa się za pośrednictwem.

| Typ dzierżawy usługi Azure AD | Zaufane adresy IP opcji |
|:--- |:--- |
| Zarządzane |**Konkretnego zakresu adresów IP**: Administratorzy, określić zakres adresów IP, które można pominąć weryfikację dwuetapową dla użytkowników, którzy zalogować się z firmową siecią intranet.|
| Federacyjne |**Wszyscy użytkownicy federacyjnych**: wszystkich użytkowników federacyjnych, którzy Zaloguj się za pomocą wewnątrz organizacji, można pominąć weryfikację dwuetapową. Użytkownicy pomijania weryfikacji przy użyciu oświadczeń, wystawiony przez usługi Active Directory Federation Services (AD FS).<br/>**Konkretnego zakresu adresów IP**: Administratorzy, określić zakres adresów IP, które można pominąć weryfikację dwuetapową dla użytkowników, którzy zalogować się z firmową siecią intranet. |

Zaufane adresy IP obejścia działa tylko z wewnątrz firmową siecią intranet. Jeśli wybierzesz **wszystkich użytkowników federacyjnych** opcji i użytkownik zaloguje się z poza firmową siecią intranet, użytkownik będzie musiał uwierzytelnić przy użyciu weryfikacji dwuetapowej. Proces jest taki sam, nawet wtedy, gdy użytkownik przedstawia oświadczenia usług AD FS. 

**Środowisko użytkownika końcowego wewnątrz sieci firmowej**

Po wyłączeniu funkcji zaufane adresy IP Weryfikacja dwuetapowa była wymagana w przypadku przepływów przeglądarki. Hasła aplikacji są wymagane dla starszych aplikacji wzbogaconego klienta. 

Po włączeniu funkcji zaufane adresy IP, weryfikacja dwuetapowa była *nie* wymagane dla przepływów przeglądarki. Hasła aplikacji są *nie* jest wymagana dla starszych aplikacji wzbogaconego klienta, pod warunkiem, że użytkownik nie utworzył hasło aplikacji. Po hasło aplikacji jest używany, hasło pozostaje wymagane. 

**Środowisko użytkownika końcowego poza siecią firmową**

Niezależnie od tego, czy włączona jest funkcja zaufane adresy IP weryfikacji dwuetapowej jest wymagane dla przepływów przeglądarki. Hasła aplikacji są wymagane dla starszych aplikacji wzbogaconego klienta. 

### <a name="enable-named-locations-by-using-conditional-access"></a>Włącz nazwane lokalizacje przy użyciu dostępu warunkowego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, wybierz **usługi Azure Active Directory** > **dostępu warunkowego** > **lokalizacje z nazwą**.
3. Wybierz **nową lokalizację**.
4. Wprowadź nazwę dla lokalizacji.
5. Wybierz **Oznacz jako zaufaną lokalizację**.
6. Wprowadź zakres adresów IP w notacji CIDR, np. **192.168.1.1/24**.
7. Wybierz pozycję **Utwórz**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Włącz funkcję zaufane adresy IP przy użyciu dostępu warunkowego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, wybierz **usługi Azure Active Directory** > **dostępu warunkowego** > **lokalizacje z nazwą**.
3. Wybierz **skonfigurować uwierzytelnianie wieloskładnikowe zaufane adresy IP**.
4. Na **ustawienia usługi** w obszarze **zaufane adresy IP**, wybrać dowolny z poniższych dwóch opcji:
   
   * **W przypadku żądań od użytkowników federacyjnych pochodzące z moim intranecie**: Aby wybrać tę opcję, zaznacz pole wyboru. Federacyjna wszystkich użytkowników, którzy logowania z sieci firmowej pominięciu weryfikacji dwuetapowej za pomocą wydanego przez usługi AD FS oświadczenia. Upewnij się, że usług AD FS ma regułę Dodaj oświadczenie sieci intranet na odpowiedni ruch. Jeśli zasada nie istnieje, utwórz następującą regułę w usługach AD FS:<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```
     
   * **Dla żądań z określonego zakresu publicznych adresów IP**: Aby wybrać tę opcję, wprowadź adresy IP w polu tekstowym za pomocą notacji CIDR.
   
     * Adresy IP, które znajdują się w xxx.xxx.xxx.1 zakres za pośrednictwem xxx.xxx.xxx.254, można użyć w notacji, takich jak **xxx.xxx.xxx.0/24**.
     * Dla pojedynczego adresu IP, użyj notacji, takich jak **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Wprowadź maksymalnie 50 zakresów adresów IP. Użytkownicy logujący się z tych adresów IP pominięciu weryfikacji dwuetapowej.

5. Wybierz pozycję **Zapisz**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Włącz funkcję zaufane adresy IP przy użyciu ustawień usługi

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, wybierz **usługi Azure Active Directory** > **użytkowników**.
3. Wybierz **uwierzytelnianie wieloskładnikowe**.
4. W obszarze uwierzytelniania wieloskładnikowego wybierz **ustawienia usługi**.
5. Na **ustawienia usługi** w obszarze **zaufane adresy IP**, wybierz jeden lub oba następujące dwie opcje:
   
   * **W przypadku żądań od użytkowników federacyjnych w moim intranecie**: Aby wybrać tę opcję, zaznacz pole wyboru. Federacyjna wszystkich użytkowników, którzy logowania z sieci firmowej pominięciu weryfikacji dwuetapowej za pomocą wydanego przez usługi AD FS oświadczenia. Upewnij się, że usług AD FS ma regułę Dodaj oświadczenie sieci intranet na odpowiedni ruch. Jeśli zasada nie istnieje, utwórz następującą regułę w usługach AD FS:<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```

   * **Dla żądań z określonego zakresu adresów IP podsieci adresów**: Aby wybrać tę opcję, wprowadź adresy IP w polu tekstowym za pomocą notacji CIDR. 
     
     * Adresy IP, które znajdują się w xxx.xxx.xxx.1 zakres za pośrednictwem xxx.xxx.xxx.254, można użyć w notacji, takich jak **xxx.xxx.xxx.0/24**.
     * Dla pojedynczego adresu IP, użyj notacji, takich jak **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Wprowadź maksymalnie 50 zakresów adresów IP. Użytkownicy logujący się z tych adresów IP pominięciu weryfikacji dwuetapowej.

6. Wybierz pozycję **Zapisz**.

![Włącz zaufane adresy IP przy użyciu ustawień usługi](./media/howto-mfa-mfasettings/trustedips3.png)

## <a name="app-passwords"></a>Hasła aplikacji

Niektóre aplikacje, takie jak pakiet Office 2010 lub starszy i Apple Mail nie obsługują weryfikacji dwuetapowej. Aplikacje nie są skonfigurowane do akceptowania drugą weryfikację. Aby korzystać z tych aplikacji, skorzystaj z zalet _haseł aplikacji_ funkcji. Hasło aplikacji zamiast tradycyjnych hasła służy do Zezwalaj na aplikację, aby pominąć weryfikację dwuetapową i kontynuować pracę.

Nowoczesne uwierzytelnianie jest obsługiwane dla klientów pakietu Microsoft Office 2013 lub nowszy. Klientach pakietu Office 2013, Outlook, w tym obsługa protokołów nowoczesnego uwierzytelniania i można włączyć weryfikację dwuetapową. Po włączeniu klient hasła aplikacji nie są wymagane dla klienta.

### <a name="considerations-about-app-passwords"></a>Zagadnienia dotyczące haseł aplikacji

Podczas korzystania z haseł aplikacji należy wziąć pod uwagę następujące ważne kwestie:

* Hasła aplikacji tylko są wprowadzane raz na aplikację. Użytkownicy nie musieli śledzić hasła i wprowadzić je za każdym razem.
* Rzeczywiste hasło jest generowane automatycznie i nie jest podany przez użytkownika. Automatycznie wygenerowane hasło jest trudniejsze do odgadnięcia osoba atakująca i większe bezpieczeństwo.
* Istnieje limit 40 haseł na użytkownika. 
* Aplikacje, które haseł w pamięci podręcznej i ich używać w scenariuszach lokalnych mogą kończą się niepowodzeniem, ponieważ hasła aplikacji nie jest znany poza konta firmowego lub szkolnego. Przykładem tego scenariusza jest wiadomości e-mail programu Exchange, które są w środowisku lokalnym, ale gdy poczta jest archiwizowana w chmurze. W tym scenariuszu tego samego hasła nie działa.
* Po włączeniu uwierzytelniania wieloskładnikowego na koncie użytkownika hasła aplikacji można z klientami najbardziej niekorzystających z przeglądarki, takich jak Outlook i Microsoft Skype dla firm. Nie można wykonać działania administracyjne przy użyciu hasła aplikacji przy użyciu aplikacji niekorzystających z przeglądarki, takich jak Windows PowerShell. Nie można wykonać akcje, nawet wtedy, gdy użytkownik ma konto administracyjne. Aby uruchamiać skrypty programu PowerShell, Utwórz konto usługi z silnym hasłem, a nie włączaj konta włączono weryfikację dwuetapową.

>[!WARNING]
>Hasła aplikacji nie działają w środowiskach hybrydowych, gdzie klienci komunikują się ze swoich lokalnych i automatycznego wykrywania punkty końcowe w chmurze. Hasła domeny są wymagane do uwierzytelnienia w środowisku lokalnym. Hasła aplikacji są wymagane do uwierzytelniania w usłudze w chmurze.
>

### <a name="guidance-for-app-password-names"></a>Wskazówki dotyczące nazwy haseł aplikacji

Nazwy haseł aplikacji odzwierciedlały urządzenie, na którym są używane. W przypadku komputera przenośnego, który ma aplikacji niekorzystających z przeglądarki, takich jak Outlook, Word i Excel, należy utworzyć hasło aplikacji o nazwie **laptopa** dla tych aplikacji. Utwórz hasło aplikacji o nazwie **pulpitu** dla tych samych aplikacji, które są uruchamiane na komputerze stacjonarnym. 

>[!NOTE]
>Zaleca się utworzenie hasła aplikacji na urządzenie zamiast hasła aplikacji na aplikację.

### <a name="federated-or-single-sign-on-app-passwords"></a>Pojedynczych lub federacyjnego logowania jednokrotnego hasła aplikacji

Usługa Azure AD obsługuje federacyjnego lub logowania jednokrotnego (SSO) z lokalnego systemu Windows Server domenowe Active Directory (AD DS). Jeśli Twoja organizacja jest sfederowana z usługą Azure AD i używasz usługi Azure Multi-Factor Authentication, należy wziąć pod uwagę następujące kwestie dotyczące haseł aplikacji.

>[!NOTE]
>Następujące kwestie dotyczą tylko klientów usługi federacyjnej (SSO).

* Hasła aplikacji są weryfikowane przez usługę Azure AD i w związku z tym, pominąć Federację. Federacja jest aktywnie używana tylko wtedy, gdy konfigurowania hasła aplikacji.
* Dostawcy tożsamości (IdP) jest nie kontaktowano się dla użytkowników federacyjnych (SSO), inaczej niż w przypadku przepływu pasywnego. Hasła aplikacji są przechowywane w ramach konta firmowego lub szkolnego. Jeśli użytkownik opuści firmę, informacje o użytkowniku są przekazywane do konta służbowego przy użyciu **DirSync** w czasie rzeczywistym. Wyłączenie/usunięcie konta może potrwać do trzech godzin, aby wykonać synchronizację, który można opóźnienie wyłączenia/usunięcia hasła aplikacji w usłudze Azure AD.
* Ustawienia kontroli dostępu klienta w środowisku lokalnym nie są uznawane za pomocą funkcji haseł aplikacji.
* Nie uwierzytelniania lokalnego, funkcja rejestrowania/inspekcji jest dostępna do użycia z funkcją haseł aplikacji.
* Niektóre zaawansowane architektury wymaga kombinacji poświadczeń do weryfikacji dwuetapowej z klientami. Poświadczenia te mogą obejmować utwór lub nazwa użytkownika konta służbowego i hasła hasła aplikacji. Wymagania zależą od tego, jak uwierzytelnianie jest wykonywane. Dla klientów, którzy uwierzytelniać się na infrastrukturę lokalną pracy lub nauki konta użytkownika i hasło wymagane. Dla klientów, które przeprowadzają uwierzytelnianie w usłudze Azure AD wymagane jest hasło aplikacji.

  Na przykład załóżmy, że masz następujące architektury:

  * Wystąpienie lokalne usługi Active Directory są Sfederowane z usługą Azure AD.
  * Używasz programu Exchange online.
  * Używasz programu Skype dla firm w środowisku lokalnym.
  * Używasz usługi Azure Multi-Factor Authentication.

  ![Korzystanie z haseł aplikacji w organizacji federacyjnego](./media/howto-mfa-mfasettings/federated.png)

  W tym scenariuszu można użyć następujących poświadczeń:

  * Aby zalogować się do usługi Skype dla firm, należy użyć służbowy lub nazwa użytkownika konta służbowego i hasło.
  * Aby uzyskać dostęp z książką adresową na kliencie programu Outlook, która łączy się z programem Exchange online, należy użyć hasła aplikacji.

### <a name="allow-users-to-create-app-passwords"></a>Zezwalaj użytkownikom na tworzenie haseł aplikacji

Domyślnie użytkownicy nie mogą tworzyć hasła aplikacji. Musi być włączona funkcja haseł aplikacji. W celu umożliwienia użytkownikom na tworzenie haseł aplikacji, użyj następującej procedury:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, wybierz **usługi Azure Active Directory** > **użytkowników i grup** > **wszyscy użytkownicy**.
3. Wybierz **uwierzytelnianie wieloskładnikowe**.
4. W obszarze uwierzytelniania wieloskładnikowego wybierz **ustawienia usługi**.
5. Na **ustawienia usługi** wybierz opcję **Zezwalaj użytkownikom na tworzenie haseł aplikacji do logowania do aplikacji niekorzystających z przeglądarki** opcji.

   ![Zezwalaj użytkownikom na tworzenie haseł aplikacji](./media/howto-mfa-mfasettings/trustedips3.png)

### <a name="create-app-passwords"></a>Tworzenie haseł aplikacji

Użytkownicy mogą tworzyć hasła aplikacji podczas ich wstępnej rejestracji. Użytkownik ma możliwość tworzenia haseł aplikacji, pod koniec procesu rejestracji.

Użytkownicy mogą również tworzyć hasła aplikacji po rejestracji. Hasła aplikacji można zmienić za pomocą ustawień w witrynie Azure portal lub portalu usługi Office 365. Aby uzyskać więcej informacji oraz szczegółowy opis kroków dla użytkowników, zobacz [co to są hasła aplikacji w usłudze Azure Multi-Factor Authentication?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

<a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>
## <a name="remember-multi-factor-authentication-for-trusted-devices"></a>Należy pamiętać, uwierzytelnianie wieloskładnikowe dla zaufanych urządzeń
_Należy pamiętać, uwierzytelnianie wieloskładnikowe_ funkcja dla urządzeń i przeglądarek, które są zaufane przez użytkownika jest bezpłatną funkcją, dla wszystkich użytkowników usługi Multi-Factor Authentication. Użytkownicy mogą pomijać kolejne weryfikacje określoną liczbę dni, po ich został pomyślnie zalogowany do urządzenia przy użyciu usługi Multi-Factor Authentication. Ta funkcja zwiększa użyteczność, minimalizując liczbę razy użytkownik ma przeprowadzić weryfikację dwuetapową na tym samym urządzeniu.

>[!IMPORTANT]
>W przypadku naruszenia zabezpieczeń konta lub urządzenia, uzupełnij uwierzytelniania wieloskładnikowego na zaufanych urządzeniach mogą wpłynąć na bezpieczeństwo. Jeśli narażona konta firmowego lub zaufanego urządzenia zostanie utracone lub skradzione, wykonaj następujące czynności [Przywróć uwierzytelnianie wieloskładnikowe na wszystkich urządzeniach](howto-mfa-userdevicesettings.md#restore-mfa-on-all-remembered-devices-for-a-user).
>
>Akcja przywracania odwołuje zaufanego stanu ze wszystkich urządzeń, a użytkownik musi ponownie weryfikacji dwuetapowej. Można również poinstruować użytkowników, aby przywrócić usługi Multi-Factor Authentication na urządzeniach z instrukcjami w [Zarządzanie ustawieniami weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted).
>

### <a name="how-the-feature-works"></a>Jak działa funkcja

Zapamiętaj funkcji uwierzytelniania wieloskładnikowego ustawia trwały plik cookie w przeglądarce, gdy użytkownik wybierze **nie pytaj ponownie przez X dni** opcji podczas logowania. Użytkownik nie jest monitowany ponownie dla uwierzytelniania wieloskładnikowego z tej samej przeglądarki do momentu wygaśnięcia ważności pliku cookie. Jeśli użytkownik otwiera innej przeglądarki, w tym samym urządzeniu lub czyści ich pliki cookie, otrzymuje monit ponownie zweryfikować.

**Nie pytaj ponownie przez X dni** opcja nie jest wyświetlana w aplikacjach nie korzystających z przeglądarki, niezależnie od tego, czy aplikacja obsługuje nowoczesnego uwierzytelniania. Te aplikacje korzystają _tokenów odświeżania_ dostarczające nowych tokenów dostępu co godzinę. Podczas weryfikowania tokenu odświeżania usługi Azure AD sprawdza ostatniej weryfikacji dwuetapowej, które wystąpiły w ciągu określonej liczby dni.

Ta funkcja zmniejsza liczbę uwierzytelnień w aplikacji sieci web, które zwykle monit o każdym razem, gdy. Ta funkcja zwiększa liczbę uwierzytelnień dla klientów z nowoczesnego uwierzytelniania, które zwykle monit co 90 dni. Może również zwiększyć liczbę uwierzytelnień, w połączeniu z zasadami dostępu warunkowego.

>[!IMPORTANT]
>**Należy pamiętać, uwierzytelnianie wieloskładnikowe** funkcja nie jest zgodny z **nie wylogowuj mnie** funkcji usług AD FS, gdy użytkownicy wykonają weryfikację dwuetapową dla usług AD FS za pomocą usługi Azure Multi-Factor Authentication Serwer uwierzytelniania lub rozwiązanie innej firmy, uwierzytelnianie wieloskładnikowe.
>
>Jeśli użytkownicy wybierają **nie wylogowuj mnie** na usług AD FS oraz Oznacz urządzenie jako zaufany dla usługi Multi-Factor Authentication, użytkownik nie jest automatycznie zweryfikowana po **należy pamiętać, uwierzytelnianie wieloskładnikowe**liczba dni wygaśnięcia. Usługa Azure AD żądań weryfikacji dwuetapowej świeże, ale usługi AD FS zwraca token z oryginalnego oświadczenia uwierzytelniania wieloskładnikowego i daty, a nie zostanie wykonana ponownie weryfikacji dwuetapowej. Tej reakcji ustawia poza pętlę weryfikacji między platformą Azure AD i AD FS.
>

### <a name="enable-remember-multi-factor-authentication"></a>Należy pamiętać, Włącz uwierzytelnianie wieloskładnikowe

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, wybierz **usługi Azure Active Directory** > **użytkowników i grup** > **wszyscy użytkownicy**.
3. Wybierz **uwierzytelnianie wieloskładnikowe**.
4. W obszarze uwierzytelniania wieloskładnikowego wybierz **ustawienia usługi**.
5. Na **ustawienia usługi** w obszarze **zarządzania należy pamiętać, uwierzytelnianie wieloskładnikowe**, wybierz opcję **Zezwalaj użytkownikom na zapamiętywanie danych uwierzytelniania wieloskładnikowego na urządzeniach ufają**opcji.

   ![Należy pamiętać, uwierzytelnianie wieloskładnikowe dla zaufanych urządzeń](./media/howto-mfa-mfasettings/remember.png)

6. Ustaw liczbę dni, aby umożliwić urządzeniom zaufany pominięciu weryfikacji dwuetapowej. Wartość domyślna to 14 dni.
7. Wybierz pozycję **Zapisz**.

### <a name="mark-a-device-as-trusted"></a>Oznacz urządzenie jako zaufany

Po włączeniu funkcji uwierzytelniania wieloskładnikowego Zapamiętaj, użytkownicy mogą oznaczać urządzenia jako zaufany podczas logowania się przez wybranie **nie pytaj ponownie**.

![Wybierz opcję "Nie pytaj ponownie" dla zaufanych urządzeń](./media/howto-mfa-mfasettings/trusted.png)

## <a name="selectable-verification-methods"></a>Metody weryfikacji można wybierać

Można wybrać metody weryfikacji, które są dostępne dla użytkowników za pomocą _metod weryfikacji można wybierać_ funkcji. Poniższa tabela zawiera krótkie omówienie metod.

Gdy użytkownicy rejestrują swoje konta dla usługi Azure Multi-Factor Authentication, których wybierać ich preferowana metoda weryfikacji opcje, które mają włączone. Wskazówki dotyczące procesu rejestracji użytkownika znajduje się w [Skonfiguruj moje konto na potrzeby weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user-first-time.md).

| Metoda | Opis |
|:--- |:--- |
| Połączenie z telefonem |Przełącza automatyczne połączenie głosowe. Użytkownik odbierze połączenie i naciska klawisz # na klawiaturze telefonu w celu uwierzytelnienia. Numer telefonu nie są zsynchronizowane z usługą Active Directory w środowisku lokalnym. |
| Wiadomość SMS na telefon |Wysyła wiadomość SMS zawierającą kod weryfikacyjny. Użytkownik jest monitowany o wprowadź kod weryfikacyjny w interfejsie logowania. Ten proces jest nazywany jednokierunkowa wiadomość SMS. Dwukierunkowa wiadomość SMS oznacza to, czy użytkownik musi ponownie tekstu określonego kodu. Dwukierunkowa wiadomość SMS jest przestarzała i nie jest obsługiwane po 14 listopada 2018 r. Użytkownicy, którzy są skonfigurowane do obsługi dwukierunkowa wiadomość SMS są automatycznie przełączone do _połączenie numerem telefonu_ weryfikacji, w tym czasie.|
| Powiadomienie przez aplikację mobilną |Wysyła powiadomienie wypychane na telefonie lub zarejestrowanym urządzeniu. Użytkownik przegląda powiadomienia i wybiera **Sprawdź** aby ukończyć weryfikację. Aplikacja Microsoft Authenticator jest dostępna dla [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), i [iOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Kod weryfikacyjny z aplikacji mobilnej |Aplikacja Microsoft Authenticator generuje nowego kodu weryfikacyjnego OATH co 30 sekund. Użytkownik wprowadza kod weryfikacyjny w interfejsie logowania. Aplikacja Microsoft Authenticator jest dostępna dla [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), i [iOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Włączanie i wyłączanie metod weryfikacji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, wybierz **usługi Azure Active Directory** > **użytkowników i grup** > **wszyscy użytkownicy**.
3. Wybierz **uwierzytelnianie wieloskładnikowe**.
4. W obszarze uwierzytelniania wieloskładnikowego wybierz **ustawienia usługi**.
5. Na **ustawienia usługi** w obszarze **opcje weryfikacji**, zaznacz/Usuń zaznaczenie metody, aby zapewnić użytkownikom.

   ![Wybieranie metod weryfikacji](./media/howto-mfa-mfasettings/authmethods.png)

6. Kliknij pozycję **Zapisz**.
