---
title: Możliwości i zagadnienia dotyczące transakcji komercyjnych w portalu Marketplace | Azure
description: W tym artykule opisano zagadnienia dotyczące cennika, rozliczeń, fakturowania i wypłaty dla typu oferty.
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
author: yijenj
manager: nuno costa
ms.service: marketplace
ms.topic: article
ms.date: 10/29/2018
ms.author: pabutler
ms.openlocfilehash: 6991ebfa58099a42b09e482d11c0d3c3983decce
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69535058"
---
# <a name="commercial-marketplace-transaction-capabilities-and-considerations"></a>Komercyjne możliwości i zagadnienia dotyczące transakcji w witrynie Marketplace

W tym artykule omówiono następujące tematy związane z handlem dla portalu komercyjnego

* Opcje publikowania witryny Marketplace
* Ogólne omówienie języka Transact
* Modele rozliczeń Transact
* Wymagania dotyczące języka Transact

## <a name="marketplace-publishing-options"></a>Opcje publikowania witryny Marketplace

Poniższe opcje publikowania są dostępne dla komercyjnych wydawców portalu Marketplace.

### <a name="list--trial-publishing-options"></a>Wyświetl listę opcji publikowania & wersji próbnej

Wydawcy mogą korzystać z opcji publikowania list, wersji próbnej i BYOL na potrzeby promocji i pozyskiwania użytkowników. Dzięki tym opcjom firma Microsoft nie uczestniczy bezpośrednio w transakcjach licencji na oprogramowanie wydawcy i nie ma żadnej skojarzonej opłaty za transakcje. Wydawca jest odpowiedzialny za obsługę wszystkich aspektów transakcji związanych z licencją na oprogramowanie, w tym między innymi: kolejności, realizacji, pomiaru, rozliczeń, fakturowania, płatności i kolekcji. Dzięki opcjom publikacji list i wersji próbnej wydawcy przechowują opłaty za licencje na oprogramowanie wydawcy 100% pobrane od klienta. 

### <a name="transact-publishing-option"></a>Opcja publikowania Transact

Oprócz opcji publikowania listy i wersji próbnej opcja publikacji Transact jest dostępna dla wydawców. Umożliwia to korzystanie z dostępnych globalnie możliwości handlowych firmy Microsoft i umożliwia firmie Microsoft hostowanie transakcji w chmurze w imieniu wydawcy.

## <a name="transact-general-overview"></a>Ogólne omówienie języka Transact

W przypadku korzystania z opcji publikowania Transact firma Microsoft umożliwia sprzedaż oprogramowania innych firm oraz wdrażanie niektórych typów ofert do subskrypcji platformy Azure klienta. W przypadku wybrania modelu rozliczeń i typu oferty Wydawca musi rozważyć rozliczenie opłat za infrastrukturę oraz własne opłaty za korzystanie z licencjonowania oprogramowania.

Opcja publikowania Transact jest obecnie obsługiwana dla następujących typów ofert: Virtual Machines, aplikacje platformy Azure i aplikacje SaaS.


![[Obsługa transakcji w przedsiębiorstwie w portalu Azure Marketplace]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>Koszty infrastruktury rozliczeń

**Dla Virtual Machines i aplikacji platformy Azure**

W przypadku Virtual Machines i aplikacji platformy Azure opłaty za użycie infrastruktury platformy Azure są naliczane zgodnie z subskrypcją platformy Azure klienta.  Opłaty za użycie infrastruktury są wyceniane i prezentowane niezależnie od opłat za Licencjonowanie dostawcy oprogramowania na fakturze klienta.

**Dla aplikacji SaaS**

W przypadku aplikacji SaaS Wydawca musi uwzględnić opłaty za użycie infrastruktury platformy Azure oraz opłaty za licencję na oprogramowanie jako pojedynczy koszt.  Jest ona reprezentowana jako stała opłata dla klienta. Użycie infrastruktury platformy Azure jest zarządzane i rozliczane bezpośrednio do partnera.  Klient nie widzi rzeczywistych opłat za użycie infrastruktury.  Wydawcy zwykle wybierają opłaty za użycie infrastruktury platformy Azure do cen licencji na oprogramowanie.  Opłaty za licencje na oprogramowanie nie są naliczane ani zużywane.

## <a name="transact-billing-models"></a>Modele rozliczeń Transact

W zależności od używanej opcji transakcji można przedstawić opłaty za licencję na oprogramowanie wydawcy w następujący sposób:  

* Bezpłatne: Brak opłat za licencje na oprogramowanie. 

* Bring your own license (BYOL): Wszelkie stosowne opłaty za licencje na oprogramowanie są zarządzane bezpośrednio przez wydawcę i klienta. Firma Microsoft przechodzi wyłącznie przez opłaty za użycie infrastruktury platformy Azure. (Tylko aplikacje dla Virtual Machines i platformy Azure).

* Płatność zgodnie z rzeczywistym użyciem: Opłaty za licencję na oprogramowanie są prezentowane jako stawka cenowa za godzinę (vCPU) na podstawie używanej infrastruktury platformy Azure. Dotyczy to tylko aplikacji Virtual Machines i platformy Azure.

* • Cennik subskrypcji: Opłaty za licencję na oprogramowanie są prezentowane jako opłata miesięczna lub roczna. jest naliczana cena cykliczna jako stała stawka lub za stanowisko. Dotyczy to tylko aplikacji SaaS i aplikacji platformy Azure zarządzanych przez aplikacje.

* Bezpłatna wersja próbna oprogramowania: Brak opłat za licencje na oprogramowanie przez 30 dni lub 90 dni.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Bezpłatna i dowolna cena licencji (BYOL)

Przy publikowaniu oferty bezpłatnej licencji na swoją licencję firma Microsoft nie odgrywa roli w celu ułatwienia transakcji sprzedaży dla opłat za korzystanie z licencji na oprogramowanie. Podobnie jak w przypadku opcji publikowania listy i wersji próbnej, Wydawca zachowuje 100% opłat za licencje na oprogramowanie. 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Płatność zgodnie z rzeczywistym użyciem i subskrypcja (oparta na witrynie)

Cena za WPay i subskrypcję przy publikowaniu oferty z płatność zgodnie z rzeczywistym użyciem lub z subskrypcją subskrypcji firmy Microsoft zapewnia technologię i usługi do przetwarzania zakupów licencji na oprogramowanie, zwraca i obciążeń zwrotnych. W tym scenariuszu Wydawca autoryzuje firmę Microsoft do działania jako Agent w tym celu. Wydawca pozwala firmie Microsoft na ułatwienie transakcji licencjonowania oprogramowania przy zachowaniu ich wyznaczenia jako sprzedawcy, dostawcy, dystrybutora i licencjodawcy.

Firma Microsoft umożliwia klientom zamówienie, licencję i korzystanie z oprogramowania wydawcy, z uwzględnieniem warunków i postanowień firmy Microsoft komercyjnych Marketplace oraz umowy licencyjnej użytkownika oprogramowania wydawcy. Wydawcy muszą zapewnić swoją umowę licencyjną użytkownika końcowego lub wybrać [kontrakt standardowy](https://docs.microsoft.com/azure/marketplace/standard-contract) podczas tworzenia oferty.


### <a name="free-software-trials"></a>Bezpłatne wersje próbne oprogramowania

W przypadku scenariuszy publikowania Transact Wydawca może bezpłatnie udostępnić licencję na oprogramowanie przez 30 dni lub 90 dni. Ta funkcja dyskontowania nie obejmuje kosztu użycia infrastruktury platformy Azure, który jest oparty na użyciu rozwiązania partnerskiego.

### <a name="private-offers"></a>Oferty prywatne

Oprócz korzystania z typów ofert i modeli rozliczeń w celu Zarabiaj oferty, wydawcy mogą wykonać transakcję prywatną, zakończyć z użyciem wynegocjowanych, cen specyficznych dla transakcji lub konfiguracji niestandardowych. Oferty prywatne są obsługiwane przez wszystkie 3 opcje publikowania Transact.

Ta opcja umożliwia korzystanie z wyższych lub niższych cen niż publicznie dostępna oferta. Oferty prywatne mogą służyć do rabatu lub dodania wersji Premium oferty. Oferty prywatne można udostępnić jednemu lub większej liczbie klientów, wymieniając swoją subskrypcję platformy Azure na poziomie oferty.


### <a name="examples"></a>Przykłady

**Pay-As-You-Go** 

* Jeśli włączysz opcję płatność zgodnie z rzeczywistym użyciem, będziesz mieć poniższą strukturę kosztów.

|Koszt licencji  | $1,00 za godzinę  |
|---------|---------|
|Koszt użycia platformy Azure (D1/1-rdzeniowe)    |   $0,14 za godzinę     |
|*Klient jest rozliczany przez firmę Microsoft*    |  *$1,14 za godzinę*       |

* W tym scenariuszu firma Microsoft rozlicza $1,14 za godzinę w celu użycia opublikowanego obrazu maszyny wirtualnej.

|Rachunki firmy Microsoft  | $1,14 za godzinę  |
|---------|---------|
|Firma Microsoft płaci 80% kosztu licencji|   $0,80 za godzinę     |
|Firma Microsoft utrzymuje 20% kosztów licencji  |  $0,20 za godzinę       |
|Firma Microsoft utrzymuje 100% kosztów użycia platformy Azure | $0,14 za godzinę |

**Korzystanie z własnej licencji (BYOL)**

* Po włączeniu opcji BYOL będzie dostępna następująca struktura kosztów.

|Koszt licencji  | Opłata licencyjna wynegocjowana i rozliczona przez użytkownika  |
|---------|---------|
|Koszt użycia platformy Azure (D1/1-rdzeniowe)    |   $0,14 za godzinę     |
|*Klient jest rozliczany przez firmę Microsoft*    |  *$0,14 za godzinę*       |

* W tym scenariuszu firma Microsoft rozlicza $0,14 za godzinę w celu użycia opublikowanego obrazu maszyny wirtualnej.

|Rachunki firmy Microsoft  | $0,14 za godzinę  |
|---------|---------|
|Firma Microsoft utrzymuje koszt użycia platformy Azure    |   $0,14 za godzinę     |
|Firma Microsoft utrzymuje 0% kosztu licencji   |  $0,00 za godzinę       |

**Subskrypcja aplikacji SaaS**

Ta opcja musi być skonfigurowana do sprzedaży za pomocą firmy Microsoft i może być naliczana według stawki miesięcznej lub rocznej.
• Jeśli włączysz opcję sprzedaży za pomocą firmy Microsoft dla oferty SaaS, będziesz mieć poniższą strukturę kosztów.

|Koszt licencji       | $100,00 miesięcznie  |
|--------------|---------|
|Koszt użycia platformy Azure (D1/1-rdzeniowe)    | Rozliczana bezpośrednio z wydawcą, a nie klientem |
|*Klient jest rozliczany przez firmę Microsoft*    |  *$100,00 miesięcznie (Uwaga: Wydawca musi uwzględnić koszty związane z infrastrukturą lub przekazaniem usługi w ramach opłaty licencyjnej)*  |

* W tym scenariuszu firma Microsoft Bills $100,00 na potrzeby licencji na oprogramowanie i płaci wydawcę $80,00.
* Partnerzy, którzy zakwalifikowani do obniżonej opłaty za usługę Marketplace, będą widzieć obniżoną opłatę za transakcje w ofertach SaaS od maja 2019 do 2020 czerwca. W tym scenariuszu firma Microsoft Bills $100,00 na potrzeby licencji na oprogramowanie i płaci wydawcę $90,00.

|Rachunki firmy Microsoft  | $100,00 miesięcznie  |
|---------|---------|
|Firma Microsoft płaci 80% kosztu licencji <br> \*Firma Microsoft płaci 90% kosztu licencji dla wszystkich zakwalifikowanych aplikacji SaaS   |   $80,00 miesięcznie <br> \*$90,00 miesięcznie    |
|Firma Microsoft utrzymuje 20% kosztów licencji <br> \*Firma Microsoft utrzymuje 10% kosztów licencji na wszystkie kwalifikowane aplikacje SaaS.  |  $20,00 miesięcznie <br> \*$10,00     |

* **Zmniejszona opłata za usługę Marketplace:** W przypadku niektórych produktów SaaS opublikowanych na naszej komercyjnej witrynie Marketplace firma Microsoft obniży opłatę za usługę Marketplace z 20% (zgodnie z opisem w umowie Microsoft Publisher) do 10%.  Aby produkt mógł kwalifikować się, co najmniej jeden z Twoich produktów musi być wyznaczone przez firmę Microsoft jako gotowy do użycia w przypadku współsprzedażowego adresu IP lub do współsprzedawanego adresu IP. Aby otrzymać obniżoną opłatę za usługę Marketplace w danym miesiącu, należy spełnić co najmniej pięć (5) dni roboczych przed końcem poprzedniego miesiąca kalendarzowego. Zmniejszona opłata za usługę Marketplace nie będzie stosowana do maszyn wirtualnych, zarządzanych aplikacji ani innych produktów udostępnianych za pośrednictwem komercyjnej witryny Marketplace.  Ta zmniejszona opłata za usługę Marketplace będzie dostępna dla kwalifikujących się ofert, a opłaty licencyjne zebrane przez firmę Microsoft od 1 maja 2019 do 30 czerwca 2020.  Po upływie tego czasu opłata za usługę portalu Marketplace powróci do normalnej kwoty.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Faktury dla klientów, płatności, rozliczeń i kolekcji

**Fakturowanie i płatność**

Wydawca może użyć preferowanej metody fakturowania klienta, aby dostarczyć subskrypcję lub PAYGO opłaty za licencję na oprogramowanie.

**Umowa Enterprise Agreement** 

Jeśli preferowana metoda fakturowania klienta jest Umowa Enterprise firmy Microsoft, opłaty za licencje na oprogramowanie będą naliczane przy użyciu tej metody fakturowania jako koszt określony, niezależnie od kosztów użycia specyficznych dla platformy Azure.

**Karty kredytowe i Faktura miesięczna** 

Klienci mogą również skorzystać z karty kredytowej i faktury miesięcznej. W tym przypadku opłaty za licencję na oprogramowanie będą rozliczane w taki sam sposób jak scenariusz Umowa Enterprise, jako koszt określony, niezależnie od kosztów użycia specyficznych dla platformy Azure.

Na przykład jeśli klient kupił zakupy przy użyciu karty kredytowej:

|Opis    |    Date  |
|----------|----------|
|Okres zamówienia   | 15 sierpnia 2018 — 30 sierpnia, 2018 |
|Zakończenie okresu (miesiąc)   | 30 sie 2018 |
|Data rozliczenia | Września 1, 2018 |
|Data płatności klienta | Września 1, 2018 |
|Okres płatności (tylko karty kredytowe:, 30 dni) | Września 1, 2018 – września 30, 2018 |
|Początek okresu zbierania | Września 1, 2018 |
|Koniec okresu zbierania danych (maksymalnie 30 dni) | Września 30, 2018 |
|Data wyliczenia wypłaty (co miesiąc 15) | 1 października 2018 |
|Data wypłaty | 15 października 2018 |

Jeśli klient kupuje przy użyciu Umowa Enterprise:

| Opis |    Date  |
|----------|----------|
|Okres zamówienia | 15 sierpnia 2018 — 30 sierpnia, 2018 |
|Zakończenie okresu (kwartał) | Września 30, 2018 |
|Data rozliczenia | 15 października 2018 |
|Okres płatności (tylko karty kredytowe:, 30 dni) | Nie dotyczy |
|Początek okresu zbierania | 15 października 2018 |
|Koniec okresu zbierania danych (maksymalnie 90 dni) | 15 stycznia 2019 |
|Data płatności klienta | 30 grudnia, 2018 |
|Data wyliczenia wypłaty (co miesiąc 15) | 15 stycznia 2019 |
|Data wypłaty | 15 lutego 2019 |

**Bezpłatne kredyty i zobowiązania pieniężne** 

Niektórzy klienci wybierają przedpłatę platformy Azure z zobowiązaniem pieniężnym w Umowa Enterprise lub za bezpłatny kredyt na korzystanie z platformy Azure. Chociaż te kredyty mogą być używane do płacenia za użycie platformy Azure, nie mogą być używane do płacenia opłat za licencje na oprogramowanie wydawcy.

**Rozliczenia i kolekcje** 

Rozliczanie licencji na oprogramowanie wydawcy jest prezentowane przy użyciu wybranej metody fakturowania przez klienta i według osi czasu fakturowania. Klienci bez Umowa Enterprise są rozliczani miesięcznie za licencje na oprogramowanie w portalu Marketplace. Klienci z Umowa Enterprise są rozliczani miesięcznie za pośrednictwem faktury prezentowanej kwartalnie.

W przypadku wybrania modeli cenowych subskrypcja lub płatność zgodnie z rzeczywistym użyciem firma Microsoft działa jako agent wydawcy i odpowiada za wszystkie aspekty rozliczeń, płatności i kolekcji.

### <a name="publisher-payout-and-reporting"></a>Wypłata i raportowanie wydawcy

* Wszelkie opłaty za licencje na oprogramowanie zebrane przez firmę Microsoft jako agent podlegają 20% opłatą za transakcję, chyba że określono inaczej i są one odejmowane w momencie wypłaty wydawcy.

* Klienci zazwyczaj kupują przy użyciu Umowa Enterprise lub z włączoną kartą kredytową płatność zgodnie z rzeczywistym użyciem. Typ umowy określa rozliczenia, fakturowanie, zbieranie i czas wypłaty.

>[!NOTE] 
>Wszystkie raporty i szczegółowe informacje dotyczące opcji publikowania Transact są dostępne za pośrednictwem sekcji szczegółowe informacje w sekcji portal Cloud Partner lub Analytics Centrum partnerskiego.

#### <a name="billing-questions-and-support"></a>Pytania i pomoc techniczna dotyczące rozliczeń

Aby uzyskać więcej informacji i zasad prawnych, zobacz [umowę wydawcy](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (dostępną w Portal Cloud partner).

Aby uzyskać pomoc dotyczącą pytań dotyczących rozliczeń, skontaktuj się z [pomocą techniczną wydawcy portalu Marketplace](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Wymagania dotyczące języka Transact

Wymagania dotyczące różnych typów ofert są omówione w tej sekcji.

### <a name="requirements-for-all-offer-types"></a>Wymagania dotyczące wszystkich typów ofert

- Dla opcji publikowania Transact są wymagane konto Microsoft i informacje finansowe, niezależnie od modelu cen oferty.
- Obowiązkowe informacje finansowe obejmują konto płatne i profil podatkowy.

Aby uzyskać więcej informacji na temat konfigurowania tych kont, zobacz [Zarządzanie kontem w centrum partnerskim](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account#financial-details).


### <a name="requirements-for-specific-offer-types"></a>Wymagania dotyczące konkretnych typów ofert

Opcja publikowania Transact jest dostępna tylko dla następujących typów ofert w portalu Marketplace: 

**Maszyna wirtualna** 

Wybierz opcję bezpłatna, Przenieś własną licencję lub model z płatność zgodnie z rzeczywistym użyciem, a także jako jednostki SKU zdefiniowane na poziomie oferty. Na rachunku na platformie Azure klienta firma Microsoft przedstawia opłaty za licencję na oprogramowanie wydawcy niezależnie od opłat związanych z infrastrukturą platformy Azure. Opłaty za infrastrukturę platformy Azure są oparte na oprogramowaniu wydawcy.

**Aplikacje platformy Azure: Szablon rozwiązania lub zarządzana aplikacja** 

Należy udostępnić co najmniej jedną maszynę wirtualną i pobrać sumę cen maszyn wirtualnych. W przypadku aplikacji zarządzanych w ramach jednego planu można wybrać jako model cenowy miesięczną subskrypcję ryczałtową, a zamiast cennika maszyny wirtualnej. W niektórych przypadkach opłaty za użycie infrastruktury platformy Azure są przesyłane do klienta oddzielnie od opłat za licencje na oprogramowanie, ale w tej samej zestawie rozliczeń. Jednak w przypadku skonfigurowania oferty zarządzanej aplikacji dla dostawców niezależnego dostawcy oprogramowania zasoby platformy Azure są rozliczane dla wydawcy, a klient otrzymuje stałą opłatę obejmującą koszt infrastruktury, licencje na oprogramowanie i usługi zarządzania.

## <a name="next-steps"></a>Następne kroki

* Należy przejrzeć wymagania kwalifikacyjne w opcje publikowania przez sekcję typ oferty w celu sfinalizowania wyborem i konfiguracją, oferty.
* Przejrzyj wzorców publikowania w sklepie przykłady w sposób swoje rozwiązanie mapowania typu oferty i konfiguracji.
