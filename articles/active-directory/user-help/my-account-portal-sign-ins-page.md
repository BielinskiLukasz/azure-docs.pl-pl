---
title: Wyświetlanie i wyszukiwanie ostatnich działań związanych z logowaniem ze strony Moje logowania — Azure Active Directory | Microsoft Docs
description: Szczegółowe informacje na temat sposobu wyświetlania i wyszukiwania ostatnich działań związanych z logowaniem ze strony Moje logowania w portalu My account.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 08/03/2020
ms.author: curtand
ms.openlocfilehash: d9023579b6627e9dab9feac8dfaccd94dc9f5c12
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798129"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-page"></a>Wyświetlanie i wyszukiwanie ostatnich działań związanych z logowaniem ze strony Moje logowania

Możesz wyświetlić wszystkie ostatnie działania związane z logowaniem do konta służbowego, ze strony **Moje logowania** w portalu **My Account** . Przeglądanie historii logowania ułatwia sprawdzanie nietypowego działania, pomagając w Poznaniu się z:

- Jeśli ktoś próbuje odgadnąć hasło.
- Jeśli osoba atakująca pomyślnie zalogował się na konto i z lokalizacji.
- Jakie aplikacje próbowały uzyskać dostęp osoby atakującej.

## <a name="view-your-recent-sign-in-activity"></a>Wyświetlanie ostatniej aktywności logowania

1. Zaloguj się do swojego konta służbowego, a następnie przejdź do https://myaccount.microsoft.com/ strony.

2. Wybierz pozycję **Moje logowania** w lewym okienku nawigacji lub wybierz link **Przejrzyj ostatnie działanie** z bloku **Moje logowania** .

    ![Strona Moje konto z wyróżnionymi ostatnimi linkami działania](media/my-account-portal/my-account-portal-sign-ins.png)

3. Rozwiń i Przejrzyj każdy z elementów logowania, aby upewnić się, że rozpoznajesz każdą z nich. Jeśli znajdziesz element logowania, który nie jest znany, Zmień hasło, aby chronić je w przypadku naruszenia zabezpieczeń konta.

    ![Ostatnia strona działania z rozwiniętymi szczegółami logowania](media/my-account-portal-sign-ins-page/recent-activity.png)

### <a name="if-you-see-a-successful-sign-in"></a>Jeśli zobaczysz Pomyślne logowanie

Czasami podczas przeglądania normalnego działania związanego z logowaniem można zobaczyć Pomyślne logowanie z nieznanej lokalizacji, przeglądarki lub systemu operacyjnego. Nieznane logowania mogą oznaczać, że osoba atakująca uzyska dostęp do Twojego konta. Jeśli zobaczysz nieautoryzowane działanie, zalecamy natychmiastowe zmianę hasła, a następnie przejście do [informacji zabezpieczających](https://mysignins.microsoft.com/security-info) w celu zaktualizowania ustawień zabezpieczeń.

Przed określeniem elementu jest niepoprawna, upewnij się, że nie widzisz fałszywych wartości dodatnich (gdzie element wygląda na wątpliwy). Na przykład określamy przybliżoną lokalizację i mapę na podstawie adresu IP. Sieci komórkowe są szczególnie trudne do lokalizowania, ponieważ czasami kierują ruch przez odległe lokalizacje. Nawet jeśli zalogujesz się przy użyciu urządzenia przenośnego w stanie Waszyngton, w lokalizacji może być widoczne logowanie pochodzące z Polski. Zdecydowanie zalecamy, aby sprawdzać szczegóły poza lokalizacją. Upewnij się, że system operacyjny, przeglądarka i aplikacja są również zrozumiałe.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Jeśli zobaczysz nieudane logowanie

Jeśli zalogowanie nie powiedzie się, może to oznaczać, że poświadczenia zostały wpisane w nieprawidłowy sposób. Może to również oznaczać, że osoba atakująca próbuje odgadnąć hasło. Aby odpowiedzieć na to ryzyko, nie musisz zmieniać hasła, ale zalecamy zarejestrowanie się w usłudze Azure Multi-Factor Authentication (MFA). Za pomocą uwierzytelniania wieloskładnikowego, nawet jeśli haker eliminuje hasło, nie wystarczy uzyskać dostępu do konta.

![Niepowodzenie kafelka logowania](media/my-account-portal-sign-ins-page/unsuccessful.png)

Jeśli zalogowanie nie powiedzie się, oznacza to, że w obszarze **działania sesji** znajduje się informacja, `Additional verification failed, invalid code` że podstawowe poświadczenia uwierzytelniania zakończyły się powodzeniem, ale nie można przeprowadzić uwierzytelniania wieloskładnikowego. Ten stan może oznaczać, że osoba atakująca prawidłowo odgadnąć hasło, ale nie mogła przekazać wyzwania usługi wieloskładnikowe uwierzytelnianie. Zalecamy zmianę hasła, ponieważ osoba atakująca może już to zrobić, a następnie przejść do strony [informacje zabezpieczające](https://mysignins.microsoft.com/security-info) , aby zaktualizować ustawienia zabezpieczeń.

## <a name="search-for-specific-sign-in-activity"></a>Wyszukiwanie określonego działania związanego z logowaniem

Ostatnie działania związane z logowaniem można wyszukać, korzystając z dowolnych dostępnych informacji. Można na przykład wyszukać ostatnie działania związane z logowaniem według systemu operacyjnego, lokalizacji, aplikacji i tak dalej.

1. Na stronie **Przejrzyj ostatnią aktywność** wpisz informacje, które chcesz wyszukać na pasku **wyszukiwania** . Na przykład wpisz, `Unsuccessful` Aby wyszukać wszystkie nieudane działania związane z logowaniem zbierane przez aplikację My account.

2. Wybierz przycisk **Wyszukaj** , aby rozpocząć wyszukiwanie.

    ![Strona Ostatnia aktywność, pokazująca wyróżniony pasek wyszukiwania, przycisk wyszukiwania i wyniki](media/my-account-portal-sign-ins-page/sign-in-search.png)

### <a name="confirm-unusual-activity"></a>Potwierdź nietypowe działanie

Logowania oflagowane jako nietypowe działanie można potwierdzić na kafelku tego działania na stronie **Moje logowania** .

![Nietypowy kafelek logowania dla potwierdzenia, że zalogowanie zostało wykonane lub nie podjęto próby](media/my-account-portal-sign-ins-page/this-wasnt-me.png)

## <a name="next-steps"></a>Następne kroki

Po wyświetleniu najnowszego działania związanego z logowaniem można:

- Wyświetl [informacje zabezpieczające](./security-info-setup-signin.md)lub zarządzaj nimi.

- Wyświetl podłączone [urządzenia](my-account-portal-devices-page.md)lub zarządzaj nimi.

- Wyświetl swoje [organizacje](my-account-portal-organizations-page.md)lub zarządzaj nimi.

- Zobacz, w jaki sposób organizacja [używa danych związanych z prywatnością](my-account-portal-privacy-page.md).

- Zmień [Ustawienia portalu My Account](my-account-portal-settings.md)