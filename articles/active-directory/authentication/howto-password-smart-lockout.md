---
title: Zapobieganie atakom z wykorzystaniem bezproblemowego korzystania z usługi Azure AD Smart blokad — Azure Active Directory
description: Azure Active Directory inteligentna blokada pomaga chronić organizację przed atakami z wykorzystaniem wymuszenia próby odgadnięcia haseł
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a762009a7aaf1a965333ac573efe55d792c3f04b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125019"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory inteligentnego blokowania

Inteligentna blokada pomaga w zablokowaniu nieprawidłowych uczestników próbujących odgadnąć hasła użytkowników lub użyć metod w celu uzyskania dostępu do programu. Może ona rozpoznawać logowania pochodzące od prawidłowych użytkowników i traktować je inaczej niż osoby atakujące i inne nieznane źródła. Inteligentna blokada blokuje osoby atakujące, umożliwiając użytkownikom dalsze dostęp do swoich kont i zwiększa produktywność.

Domyślnie inteligentna blokada blokuje konto przed próbami logowania przez jedną minutę po 10 nieudanych próbach. Konto jest blokowane ponownie po każdej kolejnej nieudanej próbie logowania w ciągu jednej minuty na pierwszy czas i dłużej w kolejnych próbach.

Inteligentna blokada śledzi ostatnie trzy niewłaściwe skróty haseł, aby uniknąć zwiększenia licznika blokady dla tego samego hasła. Jeśli ktoś wprowadzi to samo złe hasło wielokrotnie, to zachowanie nie spowoduje zablokowania konta.

 > [!NOTE]
 > Funkcja śledzenia skrótów nie jest dostępna dla klientów z włączonym uwierzytelnianiem przekazującym, ponieważ uwierzytelnianie odbywa się lokalnie, a nie w chmurze.

Wdrożenia federacyjne wykorzystujące AD FS 2016 i AF FS 2019 mogą zapewnić podobne korzyści [AD FS przy użyciu blokady ekstranetu i](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)blokady inteligentnej w ekstranecie.

Inteligentna blokada jest zawsze włączona dla wszystkich klientów usługi Azure AD z tymi domyślnymi ustawieniami, które oferują odpowiednie połączenie zabezpieczeń i użyteczności. Dostosowanie ustawień blokady inteligentnej przy użyciu wartości specyficznych dla Twojej organizacji wymaga płatnych licencji usługi Azure AD dla użytkowników.

Korzystanie z funkcji inteligentnej blokady nie gwarantuje, że oryginalny użytkownik nigdy nie będzie zablokowany. Po zablokowaniu konta użytkownika przez inteligentną blokadę firma Microsoft zaleca, aby nie zablokować oryginalnego użytkownika. Usługa blokowania próbuje upewnić się, że niewłaściwe podmioty nie mogą uzyskać dostępu do oryginalnego konta użytkownika.  

* Każde Azure Active Directory centrum danych śledzi samodzielną blokadę. Użytkownik będzie miał (threshold_limit * datacenter_count) liczbę prób, jeśli użytkownik trafi każde centrum danych.
* Inteligentna blokada używa znanej lokalizacji a nieznanej lokalizacji, aby rozróżnić niewłaściwy aktora i oryginalny użytkownik. W przypadku nieznanych i znanych lokalizacji oba te liczniki są zależne od siebie.

Inteligentna blokada może być zintegrowana z wdrożeniami hybrydowymi, przy użyciu funkcji synchronizacji skrótów haseł lub uwierzytelniania przekazywanego w celu ochrony lokalnych kont Active Directory przed zablokowaniem przez osoby atakujące. Odpowiednio ustawiając zasady inteligentnego blokowania w usłudze Azure AD, ataki można odfiltrować przed osiągnięciem Active Directory lokalnego.

W przypadku korzystania z [uwierzytelniania przekazywanego](../hybrid/how-to-connect-pta.md)należy upewnić się, że:

* Wartość progowa blokady usługi Azure AD jest **mniejsza** niż wartość progowa blokady konta Active Directory. Ustaw wartości tak, aby wartość progowa blokady konta Active Directory wynosić co najmniej dwa lub trzy razy dłużej niż wartość progowa blokady usługi Azure AD. 
* Czas trwania blokady usługi Azure AD musi być ustawiony na wartość większą niż Active Directory Resetuj licznik blokady konta po czasie trwania. Należy pamiętać, że czas trwania usługi Azure AD jest ustawiony w sekundach, podczas gdy czas trwania usługi AD jest ustawiany w minutach. 

Na przykład jeśli chcesz, aby licznik usługi Azure AD miał wartość większą niż AD, usługa Azure AD będzie 120 sekund (2 minuty), a w lokalnej usłudze AD jest ustawiona na 1 minutę (60 s).

> [!IMPORTANT]
> Obecnie administrator nie może odblokować kont w chmurze użytkowników, jeśli zostały one zablokowane przez funkcję inteligentnej blokady. Administrator musi poczekać na wygaśnięcie czasu trwania blokady. Użytkownik może jednak odblokować przy użyciu funkcji samoobsługowego resetowania hasła (SSPR) z zaufanego urządzenia lub lokalizacji.

## <a name="verify-on-premises-account-lockout-policy"></a>Weryfikuj Zasady blokady konta lokalnego

Aby sprawdzić lokalne zasady blokady konta Active Directory, należy wykonać następujące instrukcje:

1. Otwórz narzędzie do zarządzania zasady grupy.
2. Edytuj zasady grupy zawierające zasady blokady konta w organizacji, na przykład **domyślne zasady domeny**.
3. Przejdź do ustawień **Konfiguracja** > komputera > zasadyustawienia > **systemu Windows**ustawienia**zabezpieczeń**kontazasady > blokadykonta >  **Zasady**.
4. Sprawdź **próg blokady konta** i **Zresetuj licznik blokady konta po** wartościach.

![Modyfikowanie zasad blokady lokalnych kont Active Directory](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Zarządzanie wartościami inteligentnych blokad usługi Azure AD

W zależności od wymagań organizacyjnych może być konieczne dostosowanie wartości blokady inteligentnej. Dostosowanie ustawień blokady inteligentnej przy użyciu wartości specyficznych dla Twojej organizacji wymaga płatnych licencji usługi Azure AD dla użytkowników.

Aby sprawdzić lub zmodyfikować wartości blokady inteligentnej dla organizacji, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do **Azure Active Directory** > **metodami** > uwierzytelniania**Ochrona hasłem**.
1. Ustaw **próg blokady**w zależności od liczby nieudanych logowań dozwolonych na koncie przed pierwszym zablokowaniem. Wartość domyślna to 10.
1. Ustaw **czas trwania blokady (w sekundach**) na długość w sekundach każdej blokady. Wartość domyślna to 60 sekund (jedna minuta).

> [!NOTE]
> Jeśli pierwsze logowanie po zablokowaniu również zakończy się niepowodzeniem, konto zostanie ponownie zablokowane. Jeśli konto zostanie zablokowane wielokrotnie, czas trwania blokady wzrasta.

![Dostosuj zasady inteligentnego blokowania usługi Azure AD w Azure Portal](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Jak ustalić, czy funkcja inteligentnego blokowania działa, czy nie

Po wyzwoleniu progu inteligentnego blokowania zostanie wyświetlony następujący komunikat, gdy konto jest zablokowane:

**Twoje konto jest tymczasowo zablokowane, aby zapobiec nieautoryzowanemu użyciu. Spróbuj ponownie później, a jeśli nadal masz problemy, skontaktuj się z administratorem.**

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak zablokować złe hasła w organizacji przy użyciu usługi Azure AD.](howto-password-ban-bad.md)
* [Skonfiguruj Samoobsługowe resetowanie haseł, aby umożliwić użytkownikom odblokowywanie własnych kont.](quickstart-sspr.md)
