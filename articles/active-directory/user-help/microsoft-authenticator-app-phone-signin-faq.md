---
title: Zaloguj się do konta przy użyciu aplikacji Microsoft Authenticator — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Użyj aplikacji Microsoft Authenticator do logowania się na pracy lub konto służbowe lub osobiste Microsoft i kont innych niż Microsoft, za pomocą weryfikacji two-Factor Authentication lub logowanie za pomocą telefonu.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.openlocfilehash: 201487c9052ce8df9734e7e11e604cbae9102aab
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155837"
---
# <a name="sign-in-to-your-accounts-using-the-microsoft-authenticator-app"></a>Zaloguj się do konta przy użyciu aplikacji Microsoft Authenticator
Aplikacja Microsoft Authenticator pomaga logowania do kont użycie weryfikacji two-Factor Authentication. Weryfikacja Two-Factor Authentication ułatwia dostęp do Twoich kont bezpieczniejsze, szczególnie podczas wyświetlania informacji poufnych. Ponieważ hasła mogą zostać zapomniane, skradzione, lub ze złamanymi zabezpieczeniami, two-Factor Authentication Weryfikacja jest kroku dodatkowe zabezpieczenia, która pomaga chronić Twoje konto, co utrudnia innym osobom włamanie się.

Za pomocą aplikacji Microsoft Authenticator na wiele sposobów, w tym:

- Zapewnianie monit dla drugiej metody weryfikacji, po zalogowaniu się przy użyciu nazwy użytkownika i hasła.

- Zapewnianie logowania bez Wymaganie hasła, przy użyciu nazwy użytkownika i urządzenia przenośnego z odciskiem palca, powierzchni lub numeru PIN.

 >[!Important]
 >Metoda logowania telefon działa tylko z osobistych kont Microsoft. Ani konto służbowe służbowych i kont innych niż Microsoft wymagają użycia procesu standardowa weryfikacji two-Factor Authentication.

## <a name="prerequisites"></a>Wymagania wstępne
Zanim użyjesz aplikacji Microsoft Authenticator, musisz mieć:

 1. Pobierz i zainstaluj aplikację Microsoft Authenticator. Jeśli nie masz jeszcze ustanowionego jeszcze, zobacz [pobrać i zainstalować aplikację](microsoft-authenticator-app-how-to.md).

 2. Dodawanie konta służbowe, osobiste i innych firm do aplikacji Microsoft Authenticator. Aby uzyskać szczegółowe instrukcje, zobacz [Dodaj swoje konto służbowe lub szkolne](microsoft-authenticator-app-add-work-account.md), [dodawanie kont osobistych](microsoft-authenticator-app-add-personal-account.md), i [Dodawanie osobistych kont innych niż Microsoft](microsoft-authenticator-app-add-non-microsoft-account.md).

## <a name="turn-on-and-use-phone-sign-in-for-your-work-or-school-account"></a>Włącz i użyj logowanie za pomocą telefonu dla swojego konta firmowego lub szkolnego
Logowanie za pomocą telefonu jest typem weryfikacji dwuetapowej. Nadal należy zweryfikować swoją tożsamość, zapewniając rzeczy, które znasz i kolejności ma, ale phone umożliwia logowanie, możesz pominąć, wprowadzając hasło konta i wykonuje wszystkie weryfikację tożsamości na swoim urządzeniu przenośnym.

Zanim można włączyć logowanie za pomocą telefonu, należy wyłączyć weryfikację two-Factor Authentication. Aby uzyskać więcej informacji o tym, jak włączyć funkcję weryfikacji two-Factor Authentication dla konta, zobacz [Dodaj swoje konto służbowe lub szkolne](microsoft-authenticator-app-add-work-account.md) i [dodawanie kont osobistych](microsoft-authenticator-app-add-personal-account.md).

Logowanie za pomocą telefonu jest dostępna tylko w systemach iOS i Android urządzenia z systemem Android 6.0 lub nowszym.

### <a name="turn-on-phone-sign-in"></a>Włącz logowanie za pomocą telefonu 

- Otwórz aplikację Microsoft Authenticator, przejdź do pracy lub konta służbowego i włączyć logowanie za pomocą telefonu:

    - **Jeśli ta ikona jest widoczna ![ikonę, która wskazuje, możesz teraz skonfigurować](media/microsoft-authenticator-app-phone-signin-faq/icon.png).** Jeśli ta ikona pojawia się obok pracy lub nazwy konta służbowego, oznacza to, że już skonfigurowano logowanie telefonem dla konta. Może być konieczne podanie dodać powiadomienia wypychane dla swojego konta, dzięki czemu użytkownik może zostać poinformowany o żądaniach uwierzytelniania poza aplikacją.

    - **Jeśli używasz aplikacji do weryfikacji two-Factor Authentication.** Jeśli już korzystasz aplikacji i weryfikacja two-Factor Authentication, użytkownik wybierz strzałkę listy rozwijanej obok nazwy konta, a następnie wybierz **Włącz logowanie za pomocą telefonu**.
    
    - **Jeśli nie możesz znaleźć swojego konta firmowego lub szkolnego.** Jeśli nie możesz znaleźć swojego konta firmowego lub szkolnego na **kont** ekran aplikacji, oznacza to, że nie dodano go do aplikacji jeszcze. Dodaj swoje konto służbowe lub szkolne, wykonując kroki opisane w [Dodaj swoje konto służbowe lub szkolne](microsoft-authenticator-app-add-work-account.md) artykułu.

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>Zaloguj się do konta za pomocą logowanie telefonem
Po włączeniu logowanie za pomocą telefonu, można zalogować się przy użyciu tylko aplikacji Microsoft Authenticator.

1. Zaloguj się do swojego konta firmowego lub szkolnego.

    Po wpisaniu nazwy użytkownika, **zatwierdzanie logowania** pojawi się ekran pokazuje liczbę dwucyfrową i prośbą o logowaniu się za pomocą aplikacji Microsoft Authenticator. Jeśli nie chcesz użyć tego znaku w metodzie, możesz wybrać **zamiast tego użyj hasła**i zaloguj się przy użyciu hasła.

    ![Zatwierdź okno logowania na komputerze](media/microsoft-authenticator-app-phone-signin-faq/microsoft-auth-app-sign-in.png)

2. Otwórz powiadomienie lub aplikacji Microsoft Authenticator na urządzeniu, a następnie naciśnij pozycję numer odpowiadający numer widoczny na komputerze PC **zatwierdzić logowanie** ekranu.

    ![Zatwierdź okno logowania na urządzeniu](media/microsoft-authenticator-app-phone-signin-faq/microsoft-auth-app-sign-in-numbers.png)

3. Wybierz **Zatwierdź** jeśli rozpoznaje próby logowania. W przeciwnym razie wybierz **Odmów**.

4. Aby zakończyć uwierzytelnianie, należy użyć numeru PIN na swoim telefonie lub klucz biometryczne.

## <a name="turn-on-and-use-phone-sign-in-for-your-personal-microsoft-accounts"></a>Włącz i użyj logowanie telefonem dla osobistych kont Microsoft
Można włączyć logowanie za pomocą telefonu dla Twojego osobistego konta Microsoft, takich jak konto, którego używasz do logowania do usługi Outlook.com, Xbox lub Skype.

>[!NOTE]
>Aby lepiej chronić Twoje konto, aplikację Microsoft Authenticator wymaga numeru PIN lub biometrycznych blokady na urządzeniu. Jeśli zachowasz telefonu odblokowane, aplikacja wymaga skonfigurowania blokady bezpieczeństwa przed włączeniem logowanie za pomocą telefonu.

### <a name="turn-on-phone-sign-in"></a>Włącz logowanie za pomocą telefonu 

- Otwórz aplikację Microsoft Authenticator, przejdź do osobistego konta Microsoft i włączyć logowanie za pomocą telefonu:

    - **Jeśli ta ikona jest widoczna ![ikonę, która wskazuje, możesz teraz skonfigurować](media/microsoft-authenticator-app-phone-signin-faq/icon.png).** Jeśli ta ikona pojawia się obok swojej nazwy konta, oznacza to, że już skonfigurowano logowanie telefonem dla konta. Może być konieczne podanie dodać powiadomienia wypychane dla swojego konta, dzięki czemu użytkownik może zostać poinformowany o żądaniach uwierzytelniania poza aplikacją.

    - **Jeśli używasz aplikacji do weryfikacji two-Factor Authentication.** Jeśli już korzystasz aplikacji i weryfikacja two-Factor Authentication, użytkownik wybierz strzałkę listy rozwijanej obok nazwy konta, a następnie wybierz **Włącz logowanie za pomocą telefonu**.
    
    - **Jeśli nie można odnaleźć konta.** Jeśli Twoje konto nie można znaleźć na **kont** ekran aplikacji, oznacza to, że nie dodano go do aplikacji jeszcze. Dodaj osobiste konto Microsoft, wykonując kroki opisane w [Dodaj osobiste konto Microsoft](microsoft-authenticator-app-add-personal-account.md#add-your-personal-microsoft-account) artykułu.

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>Zaloguj się do konta za pomocą logowanie telefonem

1. Przejdź do osobistych Microsoft strony swojego konta logowania, a następnie zamiast wpisywać hasła, wybierz **zamiast tego użyj aplikacji Microsoft Authenticator** łącza. 

    Firma Microsoft wysyła powiadomienie na Twój telefon. 

2. Zatwierdź powiadomienie. 

## <a name="sign-in-using-two-factor-verification-for-your-account"></a>Zaloguj się przy użyciu weryfikacji two-Factor Authentication dla swojego konta
Metody standardowego weryfikacji two-Factor Authentication wymaga wprowadzenia nazwy użytkownika i hasła na urządzeniu, w przypadku logowania się do, a następnie wybierz czy aplikację Microsoft Authenticator wyśle powiadomienie, lub jeśli chcesz skopiować skojarzone weryfikacji Kod od **kont** ekranu aplikacji Microsoft Authenticator. Włącz weryfikację two-Factor Authentication dla Twojego konta jako część procesu, aby dodać konto do aplikacji Microsoft Authenticator.

>[!Note]
>Jeśli nie widzisz swojej pracy konta służbowego lub osobistego konta, które znajdują się na **kont** ekranu aplikacji Microsoft Authenticator, oznacza to, że nie dodano konto do aplikacji Microsoft Authenticator. Aby dodać swoje konto, zobacz [Dodaj swoje konto służbowe lub szkolne](microsoft-authenticator-app-add-work-account.md) lub [dodawanie kont osobistych](microsoft-authenticator-app-add-personal-account.md).

Kroki niezbędne do logowania się na pracy lub szkoły lub konto osobiste przy użyciu różnych metod weryfikacji two-Factor Authentication, zobacz [Zaloguj się przy użyciu dwuetapowej weryfikacji lub informacji zabezpieczających](user-help-sign-in.md).

## <a name="frequently-asked-questions"></a>Często zadawane pytania
|**Pytanie**|**Rozwiązanie**|
|--------------|-------------|
|**Jak jest logowanie przy użyciu Mój telefon bezpieczniejsze niż wpisanie hasła?**|Obecnie większość osób Zaloguj się do witryny sieci web lub aplikacji za pomocą nazwy użytkownika i hasła. Niestety hasła mogą być utracone, skradzione lub odgadnięty przez hakerów.<br><br>Po skonfigurowaniu aplikacji Microsoft Authenticator tworzy klucz na telefonie, aby odblokować konto chronionej przez Twój telefon numeru PIN lub biometrycznych blokady. Ten klucz jest następnie używany do potwierdzenia tożsamości podczas logowania.<br><br>**Ważne**<br>Twoje dane tylko są używane Chroń klucz lokalnie. Go nie ma nigdy nie wysyłane do lub przechowywane w chmurze.|
|**Logowanie za pomocą telefonu zastępuje weryfikację dwuetapową? Należy wyłączyć je?**|Logowanie za pomocą telefonu jest typem weryfikacji dwuetapowe, w której dwa kroki zarówno powinny być wykonywane dla urządzeń przenośnych. Należy zachować Weryfikacja obejmująca dwa kroki włączona, aby pomóc zapewnić większe bezpieczeństwo dla Twojego konta.|
|**Jeśli włączono weryfikację dwuetapową dla mojego konta mogę zachować, czy muszę zatwierdzić dwa powiadomienia?**| Nie. Logowania się do swojego konta Microsoft, również przy użyciu telefonu jest liczona jako weryfikację dwuetapową, więc ma nie drugi wymagane zatwierdzenie.|
|**Co zrobić, jeśli utracą Mój telefon lub nie jest ze mną? Jak uzyskać dostęp do mojego konta?**| Zawsze możesz wybrać użycie hasła utworzyć łącze na stronie logowania, aby wrócić do używał Twojego hasła. Jednak jeśli używasz weryfikacji dwuetapowej nadal należy zweryfikować Twoją tożsamość przy użyciu drugiej metody.<br><br>**Ważne**<br>Zdecydowanie zachęcamy do upewnij się, że masz więcej niż jeden, aktualne, metodę weryfikacji skojarzony z Twoim kontem.<br><br>Możesz zarządzać metody weryfikacji konta osobiste z Twojego [ustawienia zabezpieczeń](https://account.live.com/proofs/manage) strony. Dla konta służbowego lub szkolnego, możesz przejść do swojej organizacji [dodatkowej weryfikacji zabezpieczeń](https://aka.ms/MFASetup) strony lub **zabezpieczyć swoje konto** strony, jeśli administrator wyłączył informacji zabezpieczających. Aby uzyskać więcej informacji na temat informacji zabezpieczających, zobacz [Zarządzanie informacjom zabezpieczającym](security-info-manage-settings.md).<br><br>Jeśli nie możesz zarządzać metody weryfikacji, możesz skontaktować się z administratorem.|
|**Jak uniemożliwić korzystanie z tej funkcji i wrócić do korzystania z hasła?**|Dla osobistych kont, wybierz **zamiast tego użyj hasła** łącze podczas logowania. Wybór najbardziej aktualne jest zapamiętanych i oferowana domyślnie przy następnym logowaniu. Jeśli kiedykolwiek chcesz wrócić do korzystania logowanie telefonem, wybierz **zamiast tego użyj aplikacji** łącze podczas logowania.<br><br>Dla konta służbowego lub szkolnego, należy do strony weryfikacji zabezpieczeń (takich jak https://aka.ms/MFASetup) i Usuń aplikację Microsoft Authenticator.|
|**Dlaczego nie można użyć więcej niż jedno konto służbowe lub szkolne na logowanie za pomocą telefonu?**| Telefon musi być zarejestrowana do jednego konta firmowego lub szkolnego. Jeśli chcesz włączyć logowanie telefonem dla pracy lub konta służbowego, musisz najpierw wyrejestrować starym urządzeniem za pośrednictwem **ustawienia** strony.|
|**Czy mogę zalogować się do komputera za pomocą Mój telefon?**| Na komputerze firma Microsoft zaleca, logowanie przy użyciu usługi Windows Hello w systemie Windows 10. Windows Hello pozwala korzystać z rozpoznawania twarzy, odcisk palca lub numeru PIN do logowania.|
|**Czy mogę korzystać logowanie za pomocą telefonu z mojego Windows Phone?**| Nie. Ta funkcja nie jest obsługiwana przez aplikację Microsoft Authenticator na Windows Phone.|

## <a name="next-steps"></a>Kolejne kroki
- Jeśli masz więcej ogólne pytania dotyczące aplikacji, zobacz [Microsoft Authenticator często zadawane pytania](microsoft-authenticator-app-faq.md)

- Jeśli chcesz, aby dowiedzieć się więcej o weryfikację dwuetapową, zobacz [Skonfiguruj moje konto na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md)

- Jeśli chcesz, aby dowiedzieć się więcej o informacje o zabezpieczeniach, zobacz [Zarządzanie swoje informacje zabezpieczające](security-info-manage-settings.md)
