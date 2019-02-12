---
title: Zapłać z góry za rdzeni wirtualnych usługi Azure SQL Database w celu zaoszczędzenia pieniędzy | Dokumentacja firmy Microsoft
description: Dowiedz się, jak kupić pojemność usługi Azure SQL Database, zarezerwowane, aby zaoszczędzić na kosztach infrastruktury obliczeniowej.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 8066285e90903870937f28a62ef9fe8b2f6be55c
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005492"
---
# <a name="prepay-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Zapłać z góry za zasoby obliczeniowe bazy danych SQL Database o pojemności usługi Azure SQL Database, zarezerwowane

Oszczędzaj pieniądze dzięki usłudze Azure SQL Database przez prepaying za zasoby obliczeniowe, w porównaniu z cenami zgodnie z rzeczywistym użyciem. Pojemność usługi Azure SQL Database, zarezerwowana możesz składają zobowiązanie w bazie danych SQL w danym okresie rok lub trzy lata na uzyskanie znacznych rabatów na kosztach obliczeń. Aby kupić pojemność zastrzeżone bazy danych SQL, należy określić region platformy Azure, typ wdrożenia, warstwa wydajności i termin.

Nie trzeba przypisać rezerwacji do konkretnych wystąpień bazy danych SQL (pojedynczych baz danych, pul elastycznych lub wystąpienia zarządzanego). Dopasowywanie wystąpienia bazy danych SQL, które zostały już uruchomione lub tych, które są nowo wdrożone, będą automatycznie korzystać. Po zakupie rezerwacji, jesteś góry za koszty operacji obliczeniowych w danym okresie rok lub trzy lata. Tak szybko, jak można kupić rezerwację, bazy danych SQL, opłaty za zasoby obliczeniowe, które odpowiadają atrybuty rezerwacji nie jest już wynoszą płatności — jako — można przejść stawki. Rezerwacja nie obejmuje opłaty za oprogramowanie, sieci lub magazynu skojarzonego z wystąpieniem bazy danych SQL. Na koniec okresu rezerwacji korzyściami dotyczącymi rozliczeń wygasa, a bazy danych SQL są naliczane płatności — jako — możesz Przejdź opłaty. Rezerwacji nie automatycznego odnawiania. Aby uzyskać informacje o cenach, zobacz [bazy danych SQL zastrzeżone oferty pojemności](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Możesz kupić pojemność usługi Azure SQL Database, zarezerwowane [witryny Azure portal](https://portal.azure.com). Aby kupić pojemność zastrzeżone bazy danych SQL:

- Musisz być w roli właściciela dla co najmniej jeden Enterprise lub subskrypcję z płatnością za rzeczywiste użycie.
- W przypadku subskrypcji Enterprise **Dodawanie wystąpień zarezerwowanych** musi być włączona w [portalu EA portal](https://ea.azure.com). Lub, jeśli to ustawienie jest wyłączone, musi być administratorem subskrypcji umowy EA.
- Dla programu Cloud Solution Provider (CSP) tylko agentów administratora lub agentom sprzedaży można kupić pojemność bazy danych SQL zastrzeżone.

Zobacz szczegółowe informacje na jak rozliczane są klientów korporacyjnych i klientów płatności za zakupy rezerwacji [użycia platformy Azure rezerwacji dla Twojej rejestracji Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md) i [zrozumieć rezerwacji platformy Azure Użycie subskrypcji z płatnością za rzeczywiste użycie](../billing/billing-understand-reserved-instance-usage.md).

## <a name="determine-the-right-sql-size-before-purchase"></a>Rozmiar jest określany na prawo SQL przed dokonaniem zakupu

Rozmiar rezerwacji powinna być oparta na łącznej ilości użytych przez istniejące lub wkrótce do--wdrażanych pojedynczych baz danych, pul elastycznych lub wystąpienia zarządzanego w określonym regionie i przy użyciu tej samej generacji sprzętu i warstwy wydajności zasobów obliczeniowych.

Na przykład załóżmy, że używasz jednego ogólnego przeznaczenia 5. generacji — 16 rdzeniach wirtualnych elastycznej puli oraz dwóch krytyczne dla działania, 5. generacji — 4 pojedynczych baz danych (rdzeń wirtualny). Dodatkowo załóżmy powinien planujesz wdrożenie w ramach następnego miesiąca, dodatkowe ogólnego przeznaczenia, 5. generacji — 16 rdzeniach wirtualnych elastycznej puli i jedną krytyczne dla działania, 5. generacji — 32 puli elastycznej (rdzeń wirtualny). Ponadto Załóżmy, że wiesz, że te zasoby będą potrzebne do co najmniej 1 rok. W takim przypadku należy kupić 32 rdzenie wirtualne (2 x 16 pikseli), 1 rok zastrzeżenie dla pojedynczej bazy danych/elastycznej puli ogólnego przeznaczenia — 5. generacji i 40 (2 x 4 + 32) rdzeń wirtualny rezerwacji 1 rok dla pojedynczej bazy danych/elastycznej puli krytyczne dla działania — 5. generacji.

## <a name="buy-sql-database-reserved-capacity"></a>Kup pojemność zastrzeżone bazy danych SQL

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług** > **rezerwacje**.
3. Wybierz **Dodaj** a następnie w okienku wybierz typ produktu wybierz **bazy danych SQL** kupić nowe zastrzeżenie dla bazy danych SQL.
4. Wypełnij wymagane pola. Istniejące lub nowe pojedynczych baz danych, pul elastycznych lub zarządzanych wystąpień, które odpowiadają atrybuty, które możesz wybrać kwalifikują się uzyskać rabat w wysokości rezerwowanie pojemności. Rzeczywista liczba wystąpień bazy danych SQL, które Rabat zależy od tego, zakresu i ilość wybrana.

   ![Zrzut ekranu, przed przesłaniem bazy danych SQL zastrzeżone zakupu pojemności](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

    | Pole      | Opis|
    |:------------|:--------------|
    |Name (Nazwa)        |Nazwa tego zastrzeżenia.|
    |Subskrypcja|Subskrypcja użytej do zapłacenia za rezerwację pojemności bazy danych SQL zastrzeżone. Metodę płatności dla subskrypcji jest naliczana opłata z góry kosztów rezerwacji wydajności bazy danych SQL zastrzeżone. Typ subskrypcji musi być umowy enterprise agreement (oferują liczb: MS-AZR-0017P lub MS-AZR - 0148 P) lub płatność za rzeczywiste użycie (oferują liczb: MS-AZR-0003P lub MS-AZR - 0023 P). W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka. W przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty obciążają kartę kredytową lub metodę płatności faktury powiązaną z subskrypcją.|
    |Zakres       |Zakres rezerwacji pamięci rdzeń wirtualny może obejmować subskrypcji jednej lub wielu subskrypcji (zakres udostępniony). Jeśli wybierzesz: <br/><br/>**Subskrypcja pojedyncza** — rabat związany z rezerwacją pamięci rdzeń wirtualny jest stosowany do wystąpienia bazy danych SQL w ramach tej subskrypcji. <br/><br/>**Udostępnione subskrypcji** — rabat związany z rezerwacją pamięci rdzeń wirtualny jest stosowany do wystąpień bazy danych SQL w ramach dowolnej subskrypcji w ramach kontekstu rozliczeń. Dla klientów korporacyjnych zakres udostępniony jest rejestracji i zawiera wszystkie subskrypcje w ramach rejestracji. Dla klientów rozliczana według bieżącego użycia udostępnionych zakres jest wszystkie subskrypcje płatność za rzeczywiste użycie utworzonego przez administratora konta.|
    |Region      |Region platformy Azure, który pasuje do żadnego z bazą danych SQL zastrzeżone rezerwacji wydajności.|
    |Typ wdrożenia|Typ zasobu SQL, który chcesz kupić rezerwację dla.|
    |Warstwa wydajności|Warstwy usług dla wystąpień baz danych SQL.
    |Termin        |Jeden rok lub trzy lata.|
    |Ilość    |Liczba wystąpień, które zostały zakupione w ramach bazy danych SQL zastrzeżone rezerwacji wydajności. Ilość jest liczby uruchomionych wystąpień bazy danych SQL, które mogą uzyskać rabat rozliczeń. Na przykład jeśli używasz 10 wystąpień bazy danych SQL w regionie wschodnie stany USA, następnie należy określić ilość jako 10, aby zmaksymalizować korzyści dla wszystkich działających maszyn. |
    |||

5. Przegląd koszt bazy danych SQL zastrzeżone rezerwacji pojemności **koszty** sekcji.
6. Wybierz pozycję **Kup**.
7. Wybierz **Wyświetl tę rezerwację** Aby wyświetlić stan zakupu.

## <a name="cancellations-and-exchanges"></a>Anulowanie i wymiany

Jeśli potrzebujesz anulować bazy danych SQL zastrzeżone rezerwację zdolności produkcyjnych, może być 12% zakończenia opłatę za wcześniejsze zakończenie. Zwroty zależą od niższej kwoty z dwóch: cena zakupu i bieżąca cena rezerwacji. Zwroty są ograniczone do 50 000 USD rocznie. Otrzymany zwrot to pozostałe, obliczone proporcjonalnie saldo pomniejszone o 12% opłatę za wcześniejsze zakończenie. Aby zażądać anulowania, przejdź do rezerwacji w witrynie Azure portal i wybierz pozycję **zwrot** do utworzenia żądania obsługi.

Jeśli musisz zmienić rezerwację pojemności zarezerwowanej usługi SQL Database na inny region, typ wdrożenia, warstwę wydajności lub okres, możesz wymienić ją na inną rezerwację o takiej samej lub większej wartości. Data rozpoczęcia okresu nowej rezerwacji nie jest przenoszona z wymienionej rezerwacji. Okres 1 lub 3 lat rozpoczyna się po utworzeniu nowej rezerwacji. Aby zażądać programu exchange, przejdź do rezerwacji w witrynie Azure portal i wybierz **Exchange** do utworzenia żądania obsługi.

## <a name="vcore-size-flexibility"></a>elastyczność rozmiaru (rdzeń wirtualny)

elastyczność rozmiaru (rdzeń wirtualny) ułatwia skalowanie w górę lub w dół w ramach warstwy wydajności i region, bez utraty korzyści rezerwowanie pojemności. Wydajności bazy danych SQL zastrzeżone również zapewnia elastyczność tymczasowo przenieść gorąca bazy danych między pulami i pojedynczych baz danych w ramach normalnych operacji (w ramach tego samego regionu i wydajność warstwy) bez utraty rezerwowanie pojemności korzyści. Przechowując buforu bez zastosowanych w rezerwacji, umożliwiają wydajne zarządzanie wzrostów wydajności, nie przekraczając Twojego budżetu.

## <a name="next-steps"></a>Kolejne kroki

Rabat związany z rezerwacją pamięci rdzeń wirtualny jest automatycznie stosowany do wystąpień bazy danych SQL, które odpowiadają zakresem rezerwacji wydajności bazy danych SQL zastrzeżone i atrybutów. Można zaktualizować zakresu rezerwacji wydajności bazy danych SQL zastrzeżone za pośrednictwem [witryny Azure portal](https://portal.azure.com), PowerShell, interfejsu wiersza polecenia lub przy użyciu interfejsu API.

Aby dowiedzieć się, jak zarządzać bazą danych SQL zastrzeżone rezerwacji pojemności, zobacz [zarządzanie wydajnością bazy danych SQL zastrzeżone](../billing/billing-manage-reserved-vm-instance.md).

Aby dowiedzieć się więcej na temat rezerwacji Azure, zobacz następujące artykuły:

- [Co to jest Azure rezerwacje?](../billing/billing-save-compute-costs-reservations.md)
- [Zarządzanie usługą Azure Reservations](../billing/billing-manage-reserved-vm-instance.md)
- [Świadomość, że rezerwacje platformy Azure z rabatami](../billing/billing-understand-reservation-charges.md)
- [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](../billing/billing-understand-reserved-instance-usage.md)
- [Opis zastrzeżenia dla Twojej rejestracji Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)
- [Rezerwacje platformy Azure w programie partnera Centrum Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

