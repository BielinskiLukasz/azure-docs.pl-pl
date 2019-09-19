---
title: Omówienie faktury za umowę klienta firmy Microsoft na platformie Azure
description: Dowiedz się, jak interpretować treść rachunku za umowę klienta firmy Microsoft na platformie Azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: a5f77120c1d4e8a6721f3bc207132bee19a7772f
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "68383536"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>Terminy na fakturze za umowę klienta firmy Microsoft

Ten artykuł dotyczy konta rozliczeniowego platformy Azure związanego z umową klienta firmy Microsoft. [Sprawdź, czy masz dostęp do umowy klienta firmy Microsoft](#check-access-to-a-microsoft-customer-agreement).

Faktura zawiera podsumowanie opłat i instrukcje dotyczące płatności. Można ją pobrać w formacie PDF (Portable Document Format) z witryny [Azure Portal](https://portal.azure.com/) lub otrzymać w wiadomości e-mail. Aby uzyskać więcej informacji, zobacz [Wyświetlanie i pobieranie faktury platformy Microsoft Azure](billing-download-azure-invoice.md).

## <a name="billing-period"></a>Okres rozliczeniowy

Faktury są wystawiane co miesiąc. Aby dowiedzieć się, w który dzień miesiąca są wystawiane faktury, sprawdź *datę faktury* w obszarze właściwości profilu rozliczeniowego w witrynie [Azure Portal](https://portal.azure.com/). Opłaty naliczane od końca okresu rozliczeniowego do daty faktury są uwzględniane w następnym okresie rozliczeniowym, czyli na fakturze za kolejny miesiąc. Data początkowa i data końcowa okresu rozliczeniowego są widoczne nad sekcją **Podsumowanie rozliczeń** w pliku PDF z fakturą.

## <a name="invoice-terms-and-descriptions"></a>Terminy na fakturze wraz z opisami

W poniższych sekcjach wymieniono i opisano ważne terminy występujące na fakturze.

### <a name="invoice-summary"></a>Podsumowanie faktury

**Podsumowanie faktury** znajduje się w górnej części pierwszej strony i zawiera informacje o profilu rozliczeniowym oraz sposobie płatności.

![Sekcja podsumowania faktury](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| Termin | Opis |
| --- | --- |
| Sprzedano dla |Adres podmiotu prawnego, wyświetlany we właściwościach konta rozliczeniowego|
| Rachunek dla |Adres do rozliczeń w profilu rozliczeniowym, do którego jest wysyłana faktura, wyświetlany we właściwościach tego profilu|
| Profil rozliczeniowy |Nazwa profilu rozliczeniowego, do którego jest wysyłana faktura |
| Numer zamówienia zakupu |Opcjonalny numer zamówienia zakupu przypisany do Ciebie na potrzeby śledzenia |
| Numer faktury |Unikatowy, wygenerowany przez firmę Microsoft numer faktury używany na potrzeby śledzenia |
| Data faktury |Data wygenerowania faktury. Zazwyczaj jest to od 5 do 12 dni po zakończeniu okresu rozliczeniowego. Datę faktury można sprawdzić we właściwościach profilu rozliczeniowego.|
| Warunki płatności |Sposób płatności rachunku od firmy Microsoft. Zapis *Do 30 dni* oznacza, że płatność powinna nastąpić w ciągu 30 dni od daty faktury. |

### <a name="billing-summary"></a>Podsumowanie rozliczeń

Sekcja **Podsumowanie rozliczeń** zawiera opłaty naliczone od poprzedniego okresu rozliczeniowego, wszystkie zastosowane środki, podatek i łączną kwotę należności.

![Sekcja podsumowania rozliczeń](./media/billing-understand-your-invoice-mca/billingsummary.png)

| Termin | Opis |
| --- | --- |
| Opłaty|Łączna kwota opłat naliczonych na rzecz firmy Microsoft dla tego profilu rozliczeniowego od ostatniego okresu rozliczeniowego |
| Środki |Środki otrzymane ze zwrotów |
| Zastosowane środki na korzystanie z platformy Azure | Środki na korzystanie z platformy Azure, automatycznie uwzględniane podczas naliczania opłat w każdym okresie rozliczeniowym |
| Suma częściowa |Kwota należności przed opodatkowaniem |
| Podatek |Typ i kwota podatku w zależności od kraju/regionu wprowadzonego w profilu rozliczeniowym. W przypadku braku obowiązku płacenia podatku faktura nie zawiera kwoty podatku. |
| Szacowane łączne oszczędności |Szacowana, łączna kwota zaoszczędzona dzięki rabatom. Jeśli ma to zastosowanie, zastosowane rabaty są wyświetlane poniżej pozycji zakupów w sekcji Szczegóły według faktury. |

### <a name="invoice-sections"></a>Sekcje faktury

Każda sekcja faktury w profilu rozliczeniowym zawiera opłaty, zastosowane środki na korzystanie z platformy Azure, podatek oraz łączną kwotę należności.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Sekcja szczegółów według faktury

Szczegóły przedstawiają koszty w poszczególnych sekcjach faktury z podziałem na zamówienia produktów. W ramach każdego zamówienia produktu koszty są podzielone na typy usług. Informacje dotyczące opłat za dzienne użycie produktów i usług można znaleźć w witrynie Azure Portal oraz pliku CSV zawierającym opłaty i użycie zasobów platformy Azure. Aby dowiedzieć się więcej, zobacz [Opłaty na fakturze za umowę klienta firmy Microsoft](billing-mca-understand-your-bill.md).

Łączną kwotę należności w poszczególnych rodzinach usług oblicza się, odejmując wartość *środków na korzystanie z platformy Azure* od wartości *Środki/opłaty* i dodając wartość *podatku*:


![Sekcja szczegółów według faktury](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| Termin |Opis |
| --- | --- |
| Cena jednostkowa | Obowiązująca cena jednostkowa usługi (w walucie podanej w cenniku) używana do oceniania użycia. Jest ona unikatowa dla produktu, rodziny usług, miernika i oferty. |
| Ilość | Ilość zakupiona lub zużyta w okresie rozliczeniowym |
| Opłaty/środki | Opłaty netto po zastosowaniu środków/zwrotów |
| Środki na korzystanie z platformy Azure | Wartość środków na korzystanie z platformy Azure uwzględniana podczas obliczania wartości opłat/środków|
| Stawka podatku | Stawka podatku w zależności od kraju/regionu |
| Kwota podatku | Kwota naliczonego podatku zależna od stawki podatku |
| Łącznie | Łączna kwota należności za zakup |

### <a name="how-to-pay"></a>Sposób płatności

W dolnej części faktury znajdują się instrukcje dotyczące płatności. Rachunek można opłacić czekiem, przelewem bankowym lub w trybie online. W przypadku płatności w trybie online można skorzystać z karty kredytowej lub środków na korzystanie z platformy Azure, jeśli to możliwe.

### <a name="publisher-information"></a>Informacje o wydawcy

Jeśli rachunek obejmuje usługi innych firm, u dołu faktury są widoczne nazwa i adres każdego wydawcy.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- [Informacje o opłatach na fakturze w profilu rozliczeniowym](billing-mca-understand-your-bill.md)
- [Jak uzyskać fakturę rozliczeniową za platformę Azure i dane dziennego użycia](billing-download-azure-invoice-daily-usage-date.md)
- [Wyświetlanie cennika platformy Azure obowiązującego w organizacji](billing-ea-pricing.md)
- [Wyświetlanie dokumentów podatkowych dla profilu rozliczeniowego](billing-mca-download-tax-document.md)
