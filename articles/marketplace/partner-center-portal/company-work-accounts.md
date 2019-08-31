---
title: Firmowe konta służbowe i centrum partnerskie
description: Jak sprawdzić, czy firma ma konto służbowe skonfigurowane w firmie Microsoft, utworzyć nowe konto służbowe lub skonfigurować wiele kont służbowych do użycia z centrum partnerskim.
author: ChJenk
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 744b4e4975c5d4cec5c705e639e9cc8d252a7dd6
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194282"
---
# <a name="company-work-accounts-and-partner-center"></a>Firmowe konta służbowe i centrum partnerskie

Centrum partnerskie korzysta z firmowych kont służbowych, znanych również jako dzierżawy Azure Active Directory (AD), do zarządzania dostępem do kont dla wielu użytkowników, kontroli uprawnień, grup hostów i aplikacji oraz zachowywania danych profilu. Łącząc firmową domenę kont poczty e-mail z kontem Centrum partnerskiego, pracownicy firmy mogą zalogować się do Centrum partnerskiego, aby zarządzać ofertami z witryny Marketplace przy użyciu własnych nazw użytkowników i haseł kont służbowych.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Sprawdź, czy Twoja firma ma już konto służbowe

Jeśli Twoja firma subskrybuje usługę firmy Microsoft w chmurze, taką jak Azure, Microsoft Intune lub Office 365, masz już służbową domenę konta e-mail (nazywaną również dzierżawą Azure Active Directory), która może być używana z centrum partnerskim.

Wykonaj następujące kroki, aby sprawdzić:
1. Zaloguj się do portalu administracyjnego platformy Azure pod https://portal.azure.com adresem.
2. Wybierz pozycję **Azure Active Directory** w menu nawigacji po lewej stronie, a następnie wybierz pozycję **niestandardowe nazwy domen**.
3. Jeśli masz już konto służbowe, nazwa domeny zostanie wyświetlona.

Jeśli firma nie ma jeszcze konta służbowego, zostanie ona utworzona w trakcie procesu rejestracji Centrum partnerskiego.

## <a name="set-up-multiple-work-accounts"></a>Konfigurowanie wielu kont służbowych

Przed podjęciem decyzji o użyciu istniejącego konta służbowego należy wziąć pod uwagę liczbę użytkowników, którzy będą musieli uzyskać dostęp do Centrum partnerskiego. Jeśli użytkownicy znajdują się na koncie służbowym, które nie muszą uzyskiwać dostępu do Centrum partnerskiego, warto rozważyć utworzenie wielu kont służbowych, tak aby tylko Ci użytkownicy, którzy musieli uzyskać dostęp do Centrum partnerskiego, zostali reprezentowani na określonym koncie.

## <a name="create-a-new-work-account"></a>Utwórz nowe konto służbowe

Aby utworzyć nowe konto służbowe dla swojej firmy, wykonaj poniższe kroki. Może być konieczne zażądanie pomocy od osoby, która ma uprawnienia administracyjne na koncie Microsoft Azure Twojej firmy.

1. Zaloguj się do [Portalu Microsoft Azure](https://portal.azure.com).
2. W menu nawigacji po lewej stronie wybierz pozycję**Użytkownicy** **Azure Active Directory** -> .
3. Wybierz pozycję **nowy użytkownik** i Utwórz nowe konto służbowe platformy Azure, wprowadzając nazwę i adres e-mail. Upewnij się, że **rola katalogu** jest ustawiona na **użytkownika** , a następnie zaznacz pole wyboru **Pokaż hasło** u dołu, aby wyświetlić i zanotować hasło generowane automatycznie.
4. Wybierz pozycję **Utwórz** , aby zapisać nowego użytkownika.

Adres e-mail konta użytkownika musi być zweryfikowaną nazwą domeny w Twoim katalogu. Aby wyświetlić listę wszystkich zweryfikowanych domen w katalogu, wybierz pozycję **Azure Active Directory** -> **niestandardowe nazwy domen** w menu nawigacji po lewej stronie.

Aby dowiedzieć się więcej o dodawaniu domen niestandardowych w Azure Active Directory, zobacz [Dodawanie lub kojarzenie domeny w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain).

## <a name="troubleshoot-work-email-sign-in"></a>Rozwiązywanie problemów z logowaniem do służbowej poczty e-mail

Jeśli masz problemy z logowaniem się do konta służbowego (znanego również jako dzierżawy usługi Azure AD), Znajdź scenariusz na poniższym diagramie, który najlepiej odpowiada Twojej sytuacji, i wykonaj zalecane kroki.

![Diagram służący do rozwiązywania problemów z logowaniem do konta służbowego](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie kontem komercyjnej witryny Marketplace w centrum partnerskim](./manage-account.md) 
