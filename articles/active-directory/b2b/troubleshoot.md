---
title: Rozwiązywanie problemów ze współpracą B2B — Azure Active Directory | Microsoft Docs
description: Rozwiązywanie typowych problemów związanych z Azure Active Directory współpracy B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: mimart
author: v-miegge
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: f91ddee8668316df69c98ed14fbcabcb06b6da82
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983397"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Rozwiązywanie problemów Azure Active Directory współpracy B2B

Poniżej przedstawiono niektóre zagadnienia dotyczące typowych problemów związanych z usługą współpracy między firmami Azure Active Directory (Azure AD).

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Dodaliśmy użytkownika zewnętrznego, ale nie są one widoczne w mojej globalnej książce adresowej lub w selektorze osób

W przypadku, gdy użytkownicy zewnętrzni nie są wypełnini na liście, replikacja może zająć kilka minut.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Użytkownik gościa B2B nie jest wyświetlany w selektorze usługi SharePoint Online/OneDrive osoby

Możliwość wyszukiwania istniejących użytkowników-Gości w selektorze osób usługi SharePoint Online (SPO) jest domyślnie wyłączona, aby dopasować starsze zachowanie.

Tę funkcję można włączyć za pomocą ustawienia "ShowPeoplePickerSuggestionsForGuestUsers" na poziomie dzierżawy i kolekcji witryn. Funkcję można ustawić za pomocą poleceń cmdlet Set-SPOTenant i Set-SPOSite, które umożliwiają członkom przeszukiwanie wszystkich istniejących użytkowników-Gości w katalogu. Zmiany w zakresie dzierżawy nie mają wpływu na już inicjowane witryny SPO.

## <a name="invitations-have-been-disabled-for-directory"></a>Zaproszenia zostały wyłączone dla katalogu

Jeśli otrzymasz powiadomienie, że nie masz uprawnień do zapraszania użytkowników, sprawdź, czy konto użytkownika jest autoryzowane do zapraszania użytkowników zewnętrznych w obszarze Azure Active Directory > Ustawienia użytkownika > użytkowników zewnętrznych > zarządzania ustawieniami współpracy zewnętrznej:

![Zrzut ekranu przedstawiający ustawienia użytkowników zewnętrznych](media/troubleshoot/external-user-settings.png)

Jeśli ostatnio zmodyfikowano te ustawienia lub przypisano do użytkownika rolę zapraszania gościa, może upłynąć do 15-60 minut, zanim zmiany zaczną obowiązywać.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>Użytkownik, który zaprosił, otrzymuje błąd podczas realizacji

Typowe błędy:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>Administrator osoby zaproszonej nie zezwolił na tworzenie EmailVerified użytkowników w swojej dzierżawie

Gdy zapraszasz użytkowników, których organizacja używa Azure Active Directory, ale gdzie konto określonego użytkownika nie istnieje (na przykład użytkownik nie istnieje w usłudze Azure AD contoso.com). Administrator contoso.com może mieć zasady, które uniemożliwiają tworzenie użytkowników. Użytkownik musi skontaktować się z administratorem, aby określić, czy jest dozwolony dostęp użytkowników zewnętrznych. Administrator użytkownika zewnętrznego może wymagać zezwolenia użytkownikom zweryfikowanym pocztą E-mail w swojej domenie (zobacz ten [artykuł](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) na temat zezwalania na zweryfikowane wiadomości e-mail).

![Błąd wskazujący, że dzierżawca nie zezwala na zweryfikowane wiadomości e-mail](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Użytkownik zewnętrzny nie istnieje już w domenie federacyjnej

Jeśli używasz uwierzytelniania federacyjnego, a użytkownik nie istnieje jeszcze w Azure Active Directory, nie można zaprosić użytkownika.

Aby rozwiązać ten problem, administrator użytkownika zewnętrznego musi zsynchronizować konto użytkownika, aby Azure Active Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Jak "\#", który nie jest zwykle prawidłowym znakiem, jest synchronizowany z usługą Azure AD?

"\#" jest zastrzeżonym znakiem w UPN dla współpracy z usługą Azure AD B2B lub użytkownikami zewnętrznymi, ponieważ user@contoso.com zaproszone konto zmieni się na user_contoso@fabrikam.onmicrosoft.com. com # EXT #. \# Z tego względu nazwy UPN pochodzące z lokalnego nie mogą zalogować się do Azure Portal. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Wystąpił błąd podczas dodawania użytkowników zewnętrznych do zsynchronizowanej grupy

Użytkowników zewnętrznych można dodawać tylko do grup "Assigned" lub "Security", a nie do grup, które są w środowisku lokalnym.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Mój użytkownik zewnętrzny nie otrzymał wiadomości e-mail do zrealizowania

Zapraszanie powinno sprawdzić swój filtr usługodawcy internetowego lub spamu, aby upewnić się, że dozwolony jest następujący adres:Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Zauważ, że wiadomość niestandardowa nie jest dołączona do wiadomości z zaproszeniem w czasie

W celu zapewnienia zgodności z przepisami dotyczącymi ochrony prywatności nasze interfejsy API nie zawierają niestandardowych wiadomości e-mail z zaproszeniem, gdy:

- Zapraszający nie ma adresu e-mail w ramach zapraszanej dzierżawy
- Gdy podmiot zabezpieczeń appService wysyła zaproszenie

Jeśli ten scenariusz jest istotny, możesz pominąć nasz adres e-mail zaproszenia do obsługi interfejsu API i wysłać go za pomocą wybranego przez siebie mechanizmu poczty e-mail. Skontaktuj się z prawnikami w organizacji, aby upewnić się, że wszystkie wysyłane wiadomości e-mail również są zgodne z przepisami dotyczącymi ochrony prywatności.

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>Podczas próby zalogowania się do zasobu platformy Azure pojawia się błąd "AADSTS65005"

Użytkownik, który ma konto gościa nie może się zalogować i otrzymuje następujący komunikat o błędzie:

    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.

Użytkownik ma konto użytkownika platformy Azure i jest porzuconym lub niezarządzanym dzierżawcą. Ponadto w dzierżawie nie ma żadnych administratorów globalnych ani firmowych.

Aby rozwiązać ten problem, musisz przejąć pozostałą dzierżawę. Zapoznaj się z artykułem [przejmowanie niezarządzanego katalogu jako administrator w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover). Aby zapewnić bezpośredni dowód, że masz kontrolę nad przestrzenią nazw, należy również uzyskać dostęp do internetowego serwera DNS dla danego sufiksu domeny. Gdy dzierżawa zostanie zwrócona do stanu zarządzanego, zapoznaj się z klientem, czy opuszczenie użytkowników i zweryfikowanej nazwy domeny jest najlepszą opcją dla swojej organizacji.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>Użytkownik-Gość z dzierżawcą "just in Time" lub "wirusowym" nie może zresetować hasła

Jeśli dzierżawa tożsamości jest dzierżawą just-in-Time (JIT) lub wirusową (co oznacza, że jest to oddzielna, niezarządzana dzierżawa platformy Azure), tylko użytkownik-Gość może zresetować swoje hasło. Czasami organizacja zajmie się [zarządzaniem wirusami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) , które są tworzone, gdy pracownicy używają służbowych adresów e-mail do rejestracji w usłudze. Gdy organizacja zajmie się wirusową dzierżawą, tylko administrator w tej organizacji może zresetować hasło użytkownika lub włączyć SSPR. Jeśli to konieczne, jako zapraszana organizacja możesz usunąć konto użytkownika-gościa z katalogu i ponownie wysłać zaproszenie.

## <a name="next-steps"></a>Następne kroki

[Uzyskaj pomoc techniczną dotyczącą współpracy B2B](get-support.md)
