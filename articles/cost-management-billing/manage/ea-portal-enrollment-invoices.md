---
title: Faktury za rejestracje w usłudze Azure Enterprise
description: W tym artykule wyjaśniono, jak zarządzać fakturami za usługę Azure Enterprise i wykonywać związane z nimi działania.
author: bandersmsft
ms.author: banders
ms.date: 08/14/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 89b344a4fc97ccc65a123a2892eac998eaf18b28
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2020
ms.locfileid: "88244112"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Faktury za rejestracje w usłudze Azure Enterprise

W tym artykule wyjaśniono, jak zarządzać fakturami za usługę Azure Enterprise Agreement (Azure EA) i wykonywać związane z nimi działania. Faktura odzwierciedla naliczone opłaty. Przejrzyj ją pod kątem dokładności. Warto również zapoznać się z innymi zadaniami, które mogą być wymagane w związku z fakturami.

## <a name="change-a-po-number-for-an-upcoming-overage-invoice"></a>Zmiana numeru zamówienia zakupu na nadchodzącej fakturze nadwyżkowej

W witrynie Azure Enterprise Portal automatycznie generowany jest domyślny numer zamówienia zakupu, o ile administrator przedsiębiorstwa nie ustawił tego numeru przed datą wystawienia faktury. Administrator przedsiębiorstwa może zaktualizować numer zamówienia zakupu w ciągu siedmiu dni od otrzymania automatycznej wiadomości e-mail z powiadomieniem o fakturze.

### <a name="to-update-the-azure-services-purchase-order-number"></a>Aby zaktualizować numer zamówienia zakupu usług platformy Azure:

1. W witrynie Azure Enterprise Portal wybierz pozycję **Raport** > **Podsumowanie użycia**.
1. Wybierz pozycję **Edytuj numery zamówień zakupu** w prawym górnym rogu.
1. Wybierz przycisk radiowy **Usługi platformy Azure**.
1. Wybierz pozycję **Okres faktury** z menu rozwijanego zakresów dat.

   Numer zamówienia zakupu można edytować w ciągu siedmiu dni od otrzymania powiadomienia o fakturze, ale przed jej zapłaceniem.
1. Podaj nowy numer zamówienia zakupu w polu **Numer zamówienia zakupu**.
1. Wybierz pozycję **Zapisz**, aby przesłać zmianę.

### <a name="to-update-the-azure-marketplace-purchase-order-number"></a>Aby zaktualizować numer zamówienia zakupu na platformie Azure Marketplace:

1. W witrynie Azure Enterprise Portal wybierz pozycję **Raport** > **Podsumowanie użycia**.
1. Wybierz pozycję **Edytuj numery zamówień zakupu** w prawym górnym rogu.
1. Wybierz przycisk radiowy **Marketplace**.
1. Wybierz pozycję **Okres faktury** z menu rozwijanego zakresów dat.

   Numer zamówienia zakupu można edytować w ciągu siedmiu dni od otrzymania powiadomienia o fakturze, ale przed jej zapłaceniem.
1. Podaj nowy numer zamówienia zakupu w polu **Numer zamówienia zakupu**.
1. Wybierz pozycję **Zapisz**, aby przesłać zmianę.

## <a name="azure-enterprise-billing-frequency"></a>Częstotliwość fakturowania w usłudze Azure Enterprise

Firma Microsoft corocznie w dniu wejścia rejestracji w życie dokonuje rozliczeń dotyczących zakupów usług platformy Microsoft Azure w ramach przedpłaty. W przypadku użycia przekraczającego kwoty przedpłat firma Microsoft rozlicza zaległości.

- Opłaty dla przedpłat są określane na podstawie stawki miesięcznej i rozliczane rocznie z góry.
- Opłaty nadwyżkowe są obliczane w każdym miesiącu i rozliczane jako zaległości na końcu okresu rozliczeniowego.

### <a name="billing-intervals"></a>Interwał rozliczeń

Interwał rozliczeń zależy od wybranego sposobu dokonywania zakupów w ramach przedpłat. Termin przedpłaty rocznej jest identyczny z jednym z następujących:

- Data rocznicy rejestracji
- Data rozpoczęcia obowiązywania jednorocznego aneksu subskrypcji.

Data otrzymania faktury nadwyżkowej zależy od daty rozpoczęcia rejestracji i konfiguracji:

- **Rejestracje bezpośrednie z datą początkową przed 1 maja 2018 r.** :
  - Klienci z bezpośrednią umową Enterprise Azure (EA) są rozliczani w rocznym okresie rozliczeniowym za usługi platformy Azure (bez usług Azure Marketplace). Okres rozliczeniowy zależy od daty rocznicy, czyli daty wejścia w życie umowy.
  - W przypadku przekroczenia wartości 150% progu przedpłaty EA za platformę Azure nastąpi automatyczna konwersja na kwartalny okres rozliczeniowy zgodnie z datą rocznicy. Otrzymasz również fakturę za nadwyżkowe korzystanie z usług platformy Azure.
  - Jeśli nie przekroczysz 150% progu przedpłaty za platformę Azure, rejestracja pozostanie objęta rocznym okresem rozliczeniowym. Faktura nadwyżkowa zostanie wystawiona na koniec roku przedpłaty.

- **Rejestracje bezpośrednie z datą początkową po 1 maja 2018 r.** :
  - Faktury za użycie platformy Azure i opłaty rozliczane oddzielnie są objęte miesięcznym okresem rozliczeniowym.
  - Opłaty, które nie są objęte przedpłatą za platformę Azure, są należne jako płatności nadwyżkowe.  

- **Rejestracje pośrednie z datą początkową przed 1 maja 2018 r.** :

  Klienci z pośrednią umową Enterprise Azure (EA) z datą rozpoczęcia wcześniejszą niż 1 maja 2018 r. mają skonfigurowany kwartalny okres rozliczeniowy. Otrzymują oni faktury bezpośrednio od współpracujących z nimi partnerów handlowych (CP, channel partner).  

- **Rejestracje pośrednie z datą początkową po 1 maja 2018 r.** :

  Korzystasz z miesięcznego okresu rozliczeniowego.  

### <a name="increase-your-azure-prepayment"></a>Zwiększanie przedpłaty za platformę Azure

Przedpłatę można zwiększyć w dowolnym momencie. Opłaty zostaną naliczone za liczbę miesięcy pozostałą dla okresu przedpłaty w danym roku. Jeśli na przykład zarejestrujesz się w ramach jednorocznego aneksu subskrypcji i zwiększysz przedpłatę w ciągu szóstego miesiąca, faktura zostanie wystawiona za pozostałe sześć miesięcy tego okresu. Ilości dla Twojej przedpłaty zostaną wtedy zaktualizowane na ostatnie sześć miesięcy okresu przedpłaty. Te nowe ilości posłużą do ustalenia wszelkich opłat za użycie nadwyżkowe.

### <a name="overage"></a>Nadwyżka

W przypadku nadwyżki opłaty są naliczane za użycie lub rezerwacje, które przekroczyły przedpłatę w okresie rozliczeniowym. Aby wyświetlić szczegółowe informacje o sposobie obliczenia ilości nadwyżkowych dla poszczególnych pozycji, zapoznaj się z raportem podsumowania użycia lub skontaktuj się z partnerem handlowym.

Dla każdej pozycji na fakturze zobaczysz:

- **Wartość**: łączna opłata
- **Użycie przedpłaty**: kwota przedpłaty użyta do pokrycia opłat
- **Wartość netto**: kwota opłat przekraczających przedpłatę

Odpowiednie podatki są obliczane tylko od kwoty netto przekroczenia przedpłaty.

Wystawianie faktur nadwyżkowych jest zautomatyzowane. Czas wysyłania powiadomień i faktur zależy od daty zakończenia okresu rozliczeniowego.

- Powiadomienie o nadwyżce jest zwykle wysyłane siedem dni po dacie zakończenia rozliczenia.
- Faktury nadwyżkowe są wysyłane od siedmiu do dziewięciu dni po powiadomieniu.
- W ciągu siedmiu dni między powiadomieniem i zafakturowaniem możesz przejrzeć opłaty i zaktualizować wygenerowane przez system numery zamówień zakupu.

### <a name="azure-marketplace"></a>Azure Marketplace

Od okresu rozliczeniowego przypadającego na kwiecień 2019 r. klienci zaczęli otrzymywać pojedynczą fakturę dla platformy Azure, ponieważ połączyliśmy wszystkie opłaty za platformę Azure i witrynę Azure Marketplace w ramach jednej faktury, która zastępuje dwie oddzielne faktury. Ta zmiana nie ma wpływu na klientów w Australii, Japonii ani Singapurze.

Podczas przejścia do faktury skonsolidowanej otrzymasz częściową fakturę za witrynę Azure Marketplace. Końcowa oddzielna faktura będzie zawierać opłaty związane z witryną Azure Marketplace sprzed daty aktualizacji rozliczeń. Opłaty związane z witryną Azure Marketplace po tej dacie będą widoczne na fakturze za platformę Azure. Po okresie przejściowym zobaczysz wszystkie opłaty za platformę Azure i witrynę Azure Marketplace na skonsolidowanej fakturze.  

### <a name="adjust-billing-frequency"></a>Zmiana częstotliwości fakturowania

Klienci otrzymują faktury w cyklu rocznym, kwartalnym lub miesięcznym. Cykl rozliczeniowy jest określany podczas podpisywania umowy przez klienta. Rozliczanie miesięczne to najkrótszy cykl rozliczeniowy.

- Aby zmienić okres rozliczeniowy z rocznego na kwartalny w przypadku rejestracji bezpośrednich, wymagane jest **zatwierdzenie** przez administratora przedsiębiorstwa. W przypadku rejestracji pośrednich wymagane jest zatwierdzenie przez administratora partnera. Zmiana zacznie obowiązywać po zakończeniu bieżącego kwartału rozliczeniowego.
- Aby zmienić roczny lub kwartalny okres rozliczeniowy na miesięczny, wymagana jest **poprawka** do umowy.  Każda zmiana aktualnego okresu rozliczeniowego dla rejestracji wymaga zatwierdzenia przez administratora przedsiębiorstwa lub przez osobę wskazaną jako „Kontakt odbiorcy faktury”.
- **Prześlij** zatwierdzenie do [pomocy technicznej witryny Azure Enterprise Portal](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Wybierz kategorię problemu: **Rozliczenia i fakturowanie**.

Zmiana zacznie obowiązywać po zakończeniu bieżącego kwartału rozliczeniowego.

W przypadku podpisania poprawki M503 można zmienić częstotliwość fakturowania dla dowolnej umowy na miesięczną. 

### <a name="request-an-invoice-copy"></a>Żądanie kopii faktury

Aby zażądać kopii faktury, skontaktuj się z partnerem.

## <a name="credits-and-adjustments"></a>Środki i korekty

Wszystkie środki i korekty zastosowane do rejestracji są widoczne w sekcji **Raporty** w witrynie [Azure Enterprise Portal](https://ea.azure.com).

Aby wyświetlić środki:

1. W witrynie [Azure Enterprise Portal](https://ea.azure.com) wybierz sekcję **Raporty**.
1. Wybierz pozycję **Podsumowanie użycia**.
1. W prawym górnym rogu zmień widok **M** na **C**.
1. Rozwiń pole korekty w tabeli przedpłaty za usługi platformy Azure.
1. Są tu podane środki zastosowane do rejestracji i krótkie wyjaśnienie. Przykład: Środki dla umowy dotyczącej poziomu usług.

## <a name="pay-your-overage-with-your-azure-prepayment"></a>Płatność nadwyżki przy użyciu przedpłaty za platformę Azure

Aby zastosować przedpłatę za platformę Azure do nadwyżek, należy spełnić następujące kryteria:

- Klient ma naliczone, ale nieuregulowane opłaty za nadwyżki, a od daty zakończenia rozliczanej usługi nie minął jeszcze jeden rok.
- Dostępna kwota przedpłaty za platformę Azure pokrywa w całości naliczone opłaty, w tym wszystkie poprzednie nieuregulowane faktury za platformę Azure.
- Rozliczeniowy przedział czasu wybrany do uzupełnienia jest całkowicie zamknięty. Rozliczenia są całkowicie zamykane po 5. dniu każdego miesiąca.
- Okres rozliczeniowy wybrany do przesunięcia jest całkowicie zamknięty.
- Rabaty za przedpłaty za platformę Azure opierają się na rzeczywistej wysokości nowej przedpłaty, od której odejmuje się wszelkie środki przeznaczone na opłacenie poprzedniego użycia. To wymaganie dotyczy tylko naliczonych opłat za użycie nadwyżkowe. To rozwiązanie można zastosować tylko do usług rozliczanych z przedpłaty za platformę Azure — nie dotyczy ono opłat związanych z witryną Azure Marketplace. Opłaty związane z witryną Azure Marketplace są rozliczane osobno.

Aby sfinalizować przesunięcie opłat za użycie nadwyżkowe, klient lub zespół opiekujący się kontem może utworzyć wniosek o pomoc techniczną. Wymagane jest wysłane pocztą e-mail zatwierdzenie od administratora przedsiębiorstwa lub osoby wskazanej jako „Kontakt odbiorcy faktury”.

## <a name="move-charges-to-another-enrollment"></a>Przenoszenie opłat do innej rejestracji

Dane dotyczące użycia są przenoszone tylko wtedy, gdy przeniesienie ma datę wsteczną. Istnieją dwie opcje przenoszenia danych dotyczących użycia z jednej rejestracji do innej:

- Przeniesienie kont między rejestracjami.
- Przeniesienie rejestracji między rejestracjami.

W przypadku każdej z tych opcji należy przesłać [wniosek o pomoc techniczną](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) do zespołu pomocy technicznej EA. 

## <a name="enterprise-agreement-usage-calculations"></a>Obliczenia użycia umowy Enterprise Agreement

Zapoznaj się z [usługami platformy Azure](https://azure.microsoft.com/services/) i [cennikiem platformy Azure](https://azure.microsoft.com/pricing/), aby uzyskać podstawowe informacje o cenach publicznych i jednostkach miary oraz poznać często zadawane pytania i wskazówki dotyczące raportowania użycia dla poszczególnych usług. Więcej informacji na temat obliczeń dotyczących umowy EA można znaleźć w poniższych sekcjach.

### <a name="enterprise-agreement-units-of-measure"></a>Jednostki miary dotyczące umowy Enterprise Agreement

Jednostki miary w przypadku umowy Enterprise Agreement są często inne niż w przypadku innych programów, takich jak Microsoft Online Services Agreement (MOSA). Te różnice oznaczają, że dla pewnej liczby usług jednostka miary jest agregowana w celu zapewnienia znormalizowanych cen. Jednostka miary wyświetlana w widoku Podsumowanie użycia w witrynie Enterprise Portal jest zawsze miarą dla umowy Enterprise. Pełna lista bieżących jednostek miary i konwersji dla każdej usługi jest podana w pliku programu Excel [przyjaznych nazw usług](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx).

### <a name="rounding-rules"></a>Reguły zaokrąglania

Zaokrąglenia w witrynie Azure Enterprise Portal są wykonywane przy użyciu standardowej logiki zaokrąglania bankierskiego, czyli gaussowskiego, zgodnej z normą organizacji IEEE. W jej przypadku wartości połówkowe są zaokrąglane do najbliższej cyfry parzystej. Popularniejsze zaokrąglanie od połowy w górę zawsze zaokrągla wartość połówkową w górę do pełnej cyfry. Ta stosowana w witrynie Azure Enterprise Portal metoda daje dokładniejszą sumę łączną dla grupy w porównaniu ze standardową logiką programu Excel.

W celu zilustrowania: gdy pierwsza odrzucana cyfra to 5 i nie ma po niej cyfr lub są to zera, zaokrągla się do najbliższej parzystej cyfry. Na przykład liczby 2,315 i 2,325 po zaokrągleniu do najbliższej setnej to 2,32.

W poniższej tabeli przedstawiono formuły programu Excel, których można użyć do modelowania reguł witryny Azure Enterprise Portal na potrzeby zaokrąglania i konwersji:

| Scenariusz | Wzór dla logiki bankierskiej |
| --- | --- |
| Użycie zaokrąglania | =MROUND({_source_}, 0.0002) |
| Ceny zaokrąglone (2 miejsca dziesiętne) | =MROUND({_source_}, 0.02) |
| Ceny zaokrąglone (0 miejsc dziesiętnych) | =MROUND({_source_}, 2) |

### <a name="conversion-between-usage-detail-report-and-the-usage-summary-page"></a>Konwersja między raportem Szczegóły użycia a stroną Podsumowanie użycia

W pobranym raporcie danych użycia można sprawdzić nieprzetworzone użycie zasobów podawane z dokładnością do sześciu miejsc dziesiętnych. Jednak dane użycia przedstawiane w witrynie Azure Enterprise Portal są zaokrąglane do czterech miejsc dziesiętnych dla jednostek przedpłaty i do wartości całkowitych dla jednostek nadwyżki. Nieprzetworzone dane użycia są najpierw zaokrąglane do czterech cyfr przed konwersją na jednostki używane w witrynie Azure Enterprise Portal. Następnie przekonwertowane jednostki Enterprise są ponownie zaokrąglane do czterech cyfr. Rzeczywista liczba użytych godzin przed konwersją jest podana tylko w pobranym raporcie danych użycia, a nie w samej witrynie Azure Enterprise Portal.

Przykład: w raporcie Szczegóły użycia zgłoszono 694,533404 godziny rzeczywistego użycia programu SQL Server. Te jednostki są następnie konwertowane na 6,94533404 ze 100 godzin obliczeniowych, które są następnie zaokrąglane do wartości 6,9453 i wyświetlane w witrynie Azure Enterprise Portal.

- W celu określenia rozszerzonej kwoty rozliczenia te wyświetlane jednostki są następnie mnożone przez cenę jednostkową przedpłaty, a wynik jest obcinany do dwóch miejsc dziesiętnych. W przypadku jena japońskiego (JPY) i wona koreańskiego (KRW) rozszerzona kwota jest zaokrąglana do jedności.
- W przypadku nadwyżki rozliczane jednostki są obcinane do sześciu cyfr, a następnie mnożone przez cenę jednostki nadwyżki w celu określenia rozszerzonej kwoty rozliczenia.
- W przypadku rozliczeń dla dostawcy usług zarządzanych (MSP, Managed Service Provider) całe użycie skojarzone z działem oznaczonym jako dostawca usług zarządzanych jest zaokrąglane do jednostek po konwersji na jednostkę miary umowy EA. W wyniku suma tego użycia może być niższa niż łączna suma całego użycia zgłoszonego w witrynie Azure Enterprise Portal. Jest to zależne od tego, czy dostawca usług zarządzanych nadal mieści się w swoim saldzie przedpłaty za platformę Azure, czy też jest już objęty nadwyżką.

### <a name="graduated-pricing"></a>Ceny stopniowane

Cennik umowy Enterprise Agreement nie obejmuje obecnie cen stopniowanych (gdzie opłata za jednostkę zmniejsza się w miarę wzrostu użycia). W ramach przechodzenia z programu MOSA z cenami stopniowanymi do umowy Enterprise Agreement ceny są określane proporcjonalnie do warstwy podstawowej usługi po uwzględnieniu wszelkich stosownych rabatów w ramach umowy Enterprise Agreement.

### <a name="partial-hour-billing"></a>Częściowe naliczanie godzinowe

Całe użycie jest rozliczane według minut przekonwertowanych na częściowe godziny w miejsce przyrostów godzinowych. Podane stawki godzinowe Enterprise są stosowane do łącznej liczby godzin i godzin częściowych.

### <a name="average-daily-consumption"></a>Średnie użycie dzienne

Opłaty za niektóre usługi dotyczą użycia miesięcznego, ale użycie jest raportowane codziennie. W tych przypadkach użycie jest oceniane raz dziennie, dzielone przez 31 i sumowane dla liczby dni w danym miesiącu rozliczeniowym. Powoduje to, że stawki nigdy nie są wyższe niż oczekiwane dla każdego miesiąca i nieco niższe dla miesięcy krótszych niż 31 dni.

### <a name="compute-hours-conversion"></a>Konwersja godzin obliczeniowych

Przed 28 stycznia 2016 r. informacje o użyciu maszyn wirtualnych i usług w chmurze opcji A0, A2, A3 i A4 w warstwach Standardowa i Podstawowa były generowane jako minuty mierników maszyn wirtualnych A1. Maszyny wirtualne A0 były zliczane jako ułamki maszyn wirtualnych A1, podczas gdy A2, A3 i A4 były konwertowane na wielokrotności. Było to czasem mylące dla klientów, więc wdrożyliśmy zmianę polegającą na przypisaniu użycia na minutę do dedykowanych mierników dla opcji A0, A2, A3 i A4.

Nowe pomiary zaczęły obowiązywać w okresie między 27 stycznia a 28 stycznia 2016 r. W pobranym pliku CSV z informacjami o użyciu w tym okresie przejściowym znajdują się obie pozycje:

- Wygenerowane użycie wskazane przez miernik A1.
- Wygenerowane użycie wskazane przez nowy, dedykowany miernik odpowiadający rozmiarowi wdrożenia.

| **Rozmiar wdrożenia** | **Użycie wygenerowane jako wielokrotność opcji A1 do 26 stycznia 2016 r.** | **Wygenerowane użycie wskazane przez dedykowany miernik od 27 stycznia 2016 r.** |
| --- | --- | --- |
| A0 | 0,25 godziny opcji A1 | 1 godzina opcji A0 |
| A2 | 2 godziny opcji A1 | 1 godzina opcji A2 |
| A3 | 4 godziny opcji A1 | 1 godzina opcji A3 |
| A4 | 8 godzin opcji A1 | 1 godzina opcji A4 |

### <a name="regions"></a>Regiony

Dla usług, w przypadku których strefa i region mają wpływ na ceny, poniższa tabela przedstawia mapowanie dla obszarów geograficznych i regionów:

| Obszar geograficzny | Regiony transferu danych | Regiony usługi CDN |
| --- | --- | --- |
| Strefa 1 | Europa Północna <br> Europa Zachodnia <br> Zachodnie stany USA <br> Wschodnie stany USA <br> Północno-środkowe stany USA <br> Południowo-środkowe stany USA <br> Wschodnie stany USA 2 <br> Środkowe stany USA | Ameryka Północna <br> Europa |
| Strefa 2 | Wschodni region Azji i Pacyfiku <br> Południowo-Wschodni region Azji i Pacyfiku <br> Japonia Wschodnia <br> Japonia Zachodnia <br> Australia Wschodnia <br> Australia Południowo-Wschodnia | Azja i Pacyfik <br> Japonia <br> Ameryka Łacińska <br> Bliski Wschód/Afryka <br> Australia Wschodnia <br> Australia Południowo-Wschodnia |
| Strefa 3 | Brazylia Południowa |   |

Nie ma opłat za ruch wychodzący między usługami hostowanymi w tym samym centrum danych. Na przykład Microsoft 365 i Azure.

### <a name="azure-prepayment-and-unbilled-usage"></a>Przedpłata za platformę Azure i nierozliczone opłaty za użycie

Przedpłata za platformę Azure to uiszczana z góry opłata za usługi platformy Azure. Przedpłata za platformę Azure jest wykorzystywana w miarę używania usług. Usługi platformy Azure dostarczane przez firmę Microsoft są rozliczane przy użyciu przedpłaty za platformę Azure. Jednak niektóre opłaty są rozliczane oddzielnie, a usługi witryny Azure Marketplace nie zużywają przedpłaty za platformę Azure.

### <a name="charges-billed-separately"></a>Opłaty rozliczane oddzielnie

Niektóre produkty i usługi udostępniane ze źródeł innych firm nie używają przedpłaty za platformę Azure. Zamiast tego te pozycje są rozliczane osobno w ramach faktury nadwyżkowej dla standardowego okresu rozliczeniowego.

Połączyliśmy wszystkie opłaty za platformę Azure i witrynę Azure Marketplace w pojedynczą fakturę, której okres odpowiada okresowi rozliczeniowemu rejestracji. Połączone faktury nie mają zastosowania do klientów w Australii, Japonii ani Singapurze.

Na połączonej fakturze wykazane jest najpierw użycie platformy Azure, a następnie wszelkie opłaty związane z witryną Azure Marketplace. W przypadku klientów w Australii, Japonii lub Singapurze opłaty związane z witryną Azure Marketplace znajdują się na oddzielnej fakturze.

Jeśli na końcu standardowego okresu rozliczeniowego nie ma użycia nadwyżkowego, opłaty naliczane osobno znajdują się na oddzielnej fakturze. Ten proces ma zastosowanie do klientów w Australii, Japonii i Singapurze.

Następujące usługi są rozliczane oddzielnie:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop Registered User
- Openlogic
- Remote Access Rights XenApp Essentials Registered User
- Ubuntu Advantage
- Visual Studio Enterprise (miesięczny)
- Visual Studio Enterprise (roczny)
- Visual Studio Professional (miesięczny)
- Visual Studio Professional (roczny)

## <a name="what-to-expect-after-change-of-channel-partner"></a>Czego można oczekiwać po zmianie partnera kanału

Jeśli zmiana partnera kanału nastąpi w połowie miesiąca, klient otrzyma fakturę za użycie u poprzedniego powiązanego partnera i kolejną fakturę za użycie u nowego partnera.

Faktury zostaną wystawione po miesiącu, w którym kończy się okres rozliczeniowy. Jeśli rozliczenia są miesięczne, faktura za wrzesień zostanie wystawiona dla obu partnerów w październiku. Jeśli cykl rozliczeniowy jest kwartalny lub roczny, klient może oczekiwać faktury dla poprzedniego powiązanego partnera za użycie w okresie współpracy z nim, a reszta zostanie naliczona dla nowego partnera zgodnie z okresem rozliczeniowym.

## <a name="next-steps"></a>Następne kroki

- Następujące pliki programu Excel zawierają szczegółowe informacje dotyczące usług platformy Azure. Są one aktualizowane szóstego i dwudziestego dnia każdego miesiąca:

   | Tytuł | Opis | Nazwa pliku |
   | --- | --- | --- |
   | [Przyjazne nazwy usług](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | Lista wszystkich aktywnych usług. Obejmuje następujące informacje: <br>  <ul><li>Kategoria usługi</li>   <li>Przyjazna nazwa usługi</li>   <li>Nazwa przedpłaty i numer części</li> <li>Nazwa użycia i numer części</li>   <li>Jednostki miary</li>   <li>Współczynniki konwersji między raportowanym użyciem a prezentowanym użyciem witryny Enterprise Portal</li></ul> | Friendly\_Service\_Names.xlsx |
   | [Pola pobierania usługi](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | Ten arkusz kalkulacyjny zawiera listę wszystkich możliwych kombinacji pól dotyczących usługi w pobieranym raporcie użycia. | Service\_Download\_Fields.xlsx |

- Aby uzyskać informacje na temat faktur i opłat, zobacz [Omówienie rachunku za umowę Azure Enterprise Agreement](../understand/review-enterprise-agreement-bill.md).
- Aby dowiedzieć się, jak rozpocząć korzystanie z witryny Azure Enterprise Portal, zobacz [Wprowadzenie do witryny Azure EA Portal](ea-portal-get-started.md).
