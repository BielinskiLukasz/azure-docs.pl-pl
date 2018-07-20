---
title: Administratorzy zarządzania użytkownikami i urządzeniami — usługi Azure MFA | Dokumentacja firmy Microsoft
description: Opisuje sposób zmiany ustawienia użytkownika, takie jak wymuszanie użytkowników w celu procesu Weryfikacja.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 2f73d9795ba807e5901568507ad2fae5b001c91a
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39161293"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Zarządzanie ustawieniami użytkownika przy użyciu usługi Azure Multi-Factor Authentication w chmurze

Jako administrator możesz zarządzać następującymi ustawieniami użytkowników i urządzeń:

* Wymagaj od użytkowników ponownego podania metod kontaktu
* Usuwanie hasła aplikacji
* Wymagać uwierzytelniania Wieloskładnikowego na wszystkie zaufane urządzenia 

## <a name="require-users-to-provide-contact-methods-again"></a>Wymagaj od użytkowników ponownego podania metod kontaktu
To ustawienie wymusza na użytkowniku, aby ukończyć proces rejestracji ponownie. Aplikacje korzystające z przeglądarki w dalszym ciągu działać, jeśli użytkownik ma hasła aplikacji dla nich.  Możesz usunąć hasła aplikacji użytkowników, wybierając również **Usuń wszystkie istniejące hasła aplikacji wygenerowane przez wybranych użytkowników**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Sposób wymagać od użytkowników ponownego podania metod kontaktu
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, wybierz **usługi Azure Active Directory** > **użytkowników i grup** > **wszyscy użytkownicy**.
3. Wybierz **uwierzytelnianie wieloskładnikowe**. Zostanie otwarta strona uwierzytelniania wieloskładnikowego. 
4. Zaznacz pole obok użytkowników, których chcesz zarządzać. Lista opcji szybkiego kroku są wyświetlane po prawej stronie. 
5. Wybierz **Zarządzaj ustawieniami użytkownika**.
6. Pole wyboru dla **Wymagaj od wybranych użytkowników ponownego podania metod kontaktu**.
   ![Podania metod kontaktu](./media/howto-mfa-userdevicesettings/reproofup.png)
7. Kliknij pozycję **Zapisz**.
8. Kliknij przycisk **Zamknij**.

## <a name="delete-users-existing-app-passwords"></a>Usuwanie użytkowników z istniejących haseł aplikacji
To ustawienie powoduje usunięcie wszystkich haseł aplikacji utworzonych przez użytkownika. Aplikacje korzystające z przeglądarki, które zostały skojarzone z haseł aplikacji działać dopiero po utworzeniu nowe hasło aplikacji.

### <a name="how-to-delete-users-existing-app-passwords"></a>Jak usunąć użytkowników z istniejących haseł aplikacji
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, wybierz **usługi Azure Active Directory** > **użytkowników i grup** > **wszyscy użytkownicy**.
3. Wybierz **uwierzytelnianie wieloskładnikowe**. Zostanie otwarta strona uwierzytelniania wieloskładnikowego. 
6. Zaznacz pole obok użytkowników, których chcesz zarządzać. Lista opcji szybkiego kroku są wyświetlane po prawej stronie. 
7. Wybierz **Zarządzaj ustawieniami użytkownika**.
8. Pole wyboru dla **Usuń wszystkie istniejące hasła aplikacji wygenerowane przez wybranych użytkowników**.
   ![Usuwanie hasła aplikacji](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
9. Kliknij pozycję **Zapisz**.
10. Kliknij przycisk **Zamknij**.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Przywróć uwierzytelnianie wieloskładnikowe na wszystkich zapamiętanych urządzeniach użytkownika
Jedna z funkcji można skonfigurować usługi Azure Multi-Factor Authentication jest przyznawanie użytkownikom możliwość oznaczania urządzenia jako zaufany. Aby uzyskać więcej informacji, zobacz [ustawienia skonfiguruj usługę Azure Multi-Factor Authentication](howto-mfa-mfasettings.md#remember-multi-factor-authentication-for-devices-that-users-trust).

Użytkownicy mogą zrezygnować z weryfikacji dwuetapowej dla konfigurowalną liczbę dni na swoich urządzeniach regularne. Jeśli konto zostanie naruszony lub zaufanego urządzenia zostaną utracone, musisz mieć możliwość usunięcia stanu zaufania i ponownie weryfikacji dwuetapowej.

**Przywracania usługi uwierzytelnianie wieloskładnikowe na wszystkich zapamiętanych urządzeniach** ustawienie oznacza, że użytkownik będzie zmagać się z weryfikacji dwuetapowej podczas następnego logowania, niezależnie od tego, czy wybrana do oznaczania urządzenia jako zaufany. 

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Jak przywrócić usługę MFA na wszystkich urządzeniach wstrzymania dla użytkownika
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, wybierz **usługi Azure Active Directory** > **użytkowników i grup** > **wszyscy użytkownicy**.
3. Wybierz **uwierzytelnianie wieloskładnikowe**. Zostanie otwarta strona uwierzytelniania wieloskładnikowego. 
6. Zaznacz pole obok użytkowników, których chcesz zarządzać. Lista opcji szybkiego kroku są wyświetlane po prawej stronie. 
7. Wybierz **Zarządzaj ustawieniami użytkownika**.
8. Pole wyboru dla **przywracania usługi uwierzytelnianie wieloskładnikowe na wszystkich zapamiętanych urządzeniach**
   ![Usuwanie hasła aplikacji](./media/howto-mfa-userdevicesettings/rememberdevices.png)
9. Kliknij pozycję **Zapisz**.
10. Kliknij przycisk **Zamknij**.

## <a name="next-steps"></a>Kolejne kroki

- Uzyskaj więcej informacji o sposobie [ustawienia skonfigurować uwierzytelnianie wieloskładnikowe systemu Azure](howto-mfa-mfasettings.md)

- Jeśli użytkownicy potrzebują pomocy, wskazujące kierunku [Podręcznik użytkownika na potrzeby weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user.md)
