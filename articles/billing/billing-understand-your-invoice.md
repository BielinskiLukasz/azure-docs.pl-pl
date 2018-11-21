---
title: Omówienie usługi Azure faktury | Dokumentacja firmy Microsoft
description: Dowiedz się, jak czytać i zrozumieć dane użycia oraz rachunek dotyczące subskrypcji platformy Azure
services: ''
documentationcenter: ''
author: tonguyen10
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: cwatson
ms.openlocfilehash: de5cb85e751ad1d0c25ea31b185a3266eb9de05d
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275405"
---
# <a name="understand-terms-on-your-microsoft-azure-invoice"></a>Zrozumienie warunki na fakturze Microsoft Azure

Faktury zawiera podsumowanie opłat i zawiera instrukcje dotyczące płatności. Jest dostępna do pobrania w Portable Document Format (PDF) z [witryny Azure portal](https://portal.azure.com/) lub mogą być wysyłane za pośrednictwem poczty e-mail. Aby uzyskać więcej informacji, zobacz [sposobu uzyskania usługi Azure billing faktury i dane dziennego użycia](billing-download-azure-invoice-daily-usage-date.md).

Kilka kwestii, które należy zwrócić uwagę:

-   Jeśli używasz bezpłatnej subskrypcji próbnej, można uzyskać informacje o szczegółowym zestawieniem użycia w witrynie Azure portal, ale nie masz faktury.

-   Maksymalnie 24 godziny użycia na końcu poprzedniego okresu rozliczeniowego może być wyświetlane w Twojej bieżącej faktury.

-   Opłaty wymienione na zestawienia faktur dla klientów międzynarodowych służą wyłącznie do szacowania. Banki mogą mieć różne koszty współczynniki konwersji.

>[!VIDEO https://www.youtube.com/embed/jWG1lyJe3Mg]

## <a name="detailed-terms-and-descriptions-of-your-invoice"></a>Szczegółowy opis warunków wraz z opisami faktury
W poniższych sekcjach wymieniono ważne pojęcia, które widzisz na faktury i opisy dla każdego okresu.

### <a name="account-information"></a>Informacje o koncie

Sekcja informacji o koncie faktury znajduje się na szczycie pierwszej strony i zawiera informacje o profilu i subskrypcji.

![Sekcja informacji o koncie faktury](./media/billing-understand-your-invoice/1.png)

| Termin | Opis |
| --- | --- |
| Nr zamówienia klienta |Numer zamówienia zakupu opcjonalne przypisanych przez użytkownika do śledzenia |
| Nr faktury |Unikatowe, numer faktury Microsoft generowane używanych na potrzeby śledzenia |
| Cykl rozliczeń |Zakres dat, która obejmuje faktury |
| Data faktury |Data wygenerowania faktury, zwykle dzień po zakończeniu cyklu rozliczeniowym |
| Metoda płatności |Typ płatności używany na koncie (faktura lub karta kredytowa) |
| Rachunek dla |Adres do faktury, który znajduje się na konto |
| Subskrypcję oferty ("płatność za rzeczywiste użycie") |Typ oferty subskrypcji, które zostało zakupione (płatność za rzeczywiste użycie, BizSpark Plus, Azure — dostęp próbny itp.). Aby uzyskać więcej informacji, zobacz [typów ofert Azure](https://azure.microsoft.com/support/legal/offer-details/). |
| Adres e-mail właściciela konta | Adres e-mail konta, w ramach którego zarejestrowane jest konto platformy Microsoft Azure. <br /><br />Aby zmienić adres e-mail, zobacz [jak zmienić informacje o profilu konta platformy Azure, takich jak kontaktowy adres e-mail, adres i numer telefonu](billing-how-to-change-azure-account-profile.md). |

### <a name="understand-the-invoice-summary"></a>Opis sekcji podsumowania faktury
**Podsumowanie faktury** sekcji faktury wyświetla kwoty łączna liczba transakcji od ostatniego okresu rozliczeniowego oraz opłat za bieżące użycie.

![Sekcja Podsumowanie faktury](./media/billing-understand-your-invoice/2.png)

Nazwa subskrypcji ("produkcji Magazyn") jest nazwą subskrypcji dla tej faktury.

#### <a name="understand-the-previous-charges"></a>Informacje o opłatach poprzedniej
Poprzednie saldo, płatności i zaległego salda sekcji faktury zawiera podsumowanie transakcji od ostatniego okresu rozliczeniowego.

| Termin | Opis |
| --- | --- |
| Poprzednie saldo |Łączna kwota należności z ostatniego okresu rozliczeniowego |
| Płatności |Łączne płatności i kredyty zastosowane do ostatniego okresu rozliczeniowego |
| Zaległe saldo (od poprzedniego cyklu rozliczeń) |Środki na korzystanie z dowolnej pozostałe saldo w ramach Twojego konta od ostatniego okresu rozliczeniowego |

#### <a name="understand-the-current-charges"></a>Opis sekcji opłat bieżących
Faktury sekcja opłaty bieżące zawiera szczegóły dotyczące Twoich miesięcznych opłat dla bieżącego okresu rozliczeniowego.

| Termin | Opis |
| --- | --- |
| Opłaty za zużycie |Opłaty za zużycie to łączne miesięczne opłaty za subskrypcję dla bieżącego okresu rozliczeniowego|
| Rabaty |Rabaty na usługi stosowane do bieżącego okresu rozliczeniowego|
| Korekty |Różne środki (bezpłatnego użycia, środki na korzystanie z itp.) lub opłaty zaległe stosowane do bieżącego okresu rozliczeniowego.<br/><br/>Na przykład w przypadku Visual Studio Enterprise z oferty MSDN zobaczysz miesięcznych środków. Jeśli anulujesz subskrypcję, możesz zobaczyć wszystkie miesięczne opłaty za użycie przekraczające miesięczną ilość środków, której można korzystać z oferty subskrypcji. Opłaty są naliczane na początku bieżącego okresu rozliczeniowego do daty anulowania subskrypcji. |

#### <a name="sold-to-and-payment-instructions"></a>Sprzedano dla i instrukcje dokonywania płatności

W poniższej tabeli opisano sold się i instrukcje dokonywania płatności na drugiej stronie fakturze.

| Termin |Opis |
| --- | --- |
| Sprzedano dla |Adres profil, który znajduje się na konto. <br/><br/>Jeśli musisz zmienić adres, zobacz [jak zmienić informacje o profilu konta platformy Azure, takich jak kontaktowy adres e-mail, adres i numer telefonu](billing-how-to-change-azure-account-profile.md).|
| Instrukcje dokonywania płatności |Instrukcje na temat sposobu zapłaty w zależności od metody płatności (takie jak przez środki karty lub przy użyciu faktury). |

#### <a name="usage-charges"></a>Opłaty za zużycie

Sekcja opłaty za użycie faktury Wyświetla informacje poziomu miernika opłat.

![Sekcja opłaty za użycie](./media/billing-understand-your-invoice/3.png)

W poniższej tabeli opisano nagłówki kolumn opłaty za użycie na fakturze.

| Termin |Opis |
| --- | --- |
| Name (Nazwa) |Określa usługę najwyższego poziomu do użycia |
| Typ |Definiuje typ usługi platformy Azure, który może wpływać na stawkę |
| Zasób |Określa jednostkę miary dla mierników, są używane |
| Region |Określa lokalizację centrum danych pewnych usług, które są wyceniane na podstawie lokalizacji centrum danych |
| Zużyte |Ilość miernika, używany w trakcie okresu rozliczeniowego |
| Dołączono |Wartość licznika, który jest dołączony, bez dodatkowych opłat w trakcie bieżącego okresu rozliczeniowego |
| Płatne |Przedstawiono różnice między użytej ilości i ilość uwzględnione. Wyświetlany jest wystawiany na tę kwotę. W przypadku ofert płatności użyciem bez żadnej kwoty wliczonej w ofertę ta jest taka sama jak użytej ilości |
| Stawka |Szybkość, z którą są naliczane za naliczaną na jednostkę |
| Wartość |Wyświetla wynik mnożenia wartości z kolumny ilość nadwyżkowe użycie przez wartość z kolumny stawka. Jeśli ilość zużyte nie przekracza ilość uwzględnione, nie ma opłat w tej kolumnie. |
| Sumy częściowej |Suma wszystkich Twoje opłaty przed opodatkowaniem dla tego okresu rozliczeniowego |
| Suma końcowa |Suma opłat po opodatkowaniu dla tego okresu rozliczeniowego |

## <a name="how-do-i-make-sure-that-the-charges-in-my-invoice-are-correct"></a>Jak upewnić się, że opłaty w mojej fakturze są poprawne?
Jeśli na który chcesz uzyskać więcej informacji znajduje się na fakturze jest opłaty, zobacz [opis zawartości rachunku dla systemu Microsoft Azure.](billing-understand-your-bill.md)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.
Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.
