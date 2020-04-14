---
title: Omówienie funkcji Zarządzanie kosztami i rozliczenia na platformie Azure | Microsoft Docs
description: Funkcja Zarządzanie kosztami i rozliczenia na platformie Azure umożliwia wykonywanie zadań administracyjnych dotyczących rozliczeń oraz pozwala zarządzać dostępem do rozliczeń w celu śledzenia kosztów. Ponadto funkcja ta umożliwia monitorowanie i kontrolowanie wydatków na platformie Azure oraz optymalizowanie użycia zasobów platformy Azure.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: overview
ms.service: cost-management-billing
ms.custom: ''
ms.openlocfilehash: 2f96208ff3f9664d82bfc1d9ddf9bc5b9aec37c3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879092"
---
# <a name="what-is-azure-cost-management-and-billing"></a>Co to jest Zarządzanie kosztami i rozliczenia na platformie Azure?

W przypadku produktów i usług platformy Azure płaci się wyłącznie za użyte zasoby. W miarę tworzenia i używania zasobów platformy Azure są za nie naliczane opłaty. Funkcja Zarządzanie kosztami i rozliczenia na platformie Azure umożliwia wykonywanie zadań administracyjnych dotyczących rozliczeń oraz pozwala zarządzać dostępem do rozliczeń w celu śledzenia kosztów. Ponadto funkcja ta umożliwia monitorowanie i kontrolowanie wydatków na platformie Azure oraz optymalizowanie użycia zasobów platformy Azure.

## <a name="understand-azure-billing"></a>Informacje na temat rozliczeń na platformie Azure

Funkcje rozliczeń na platformie Azure umożliwiają przeglądanie zafakturowanych kosztów i zarządzanie dostępem do informacji dotyczących rozliczeń. W większych organizacjach zadania związane z rozliczeniami są zwykle wykonywane przez zespoły ds. zamówień i finansów.

Konto rozliczeniowe jest tworzone podczas rejestrowania się w celu korzystania z platformy Azure. Konta rozliczeniowego można używać do zarządzania fakturami i płatnościami oraz do śledzenia kosztów. Możesz mieć dostęp do wielu kont rozliczeniowych. Możesz na przykład zarejestrować się na platformie Azure, aby móc pracować nad projektami osobistymi. W związku z tym może istnieć indywidualna subskrypcja platformy Azure z kontem rozliczeniowym. Dostęp jest również możliwy w ramach zawartej przez organizację umowy Enterprise Agreement lub Umowy klienta firmy Microsoft. W każdym scenariuszu używane jest oddzielne konto rozliczeniowe.

### <a name="billing-accounts"></a>Konta rozliczeniowe

W witrynie Azure Portal są obecnie obsługiwane następujące typy kont rozliczeniowych:

- **Microsoft Online Services Program**: Indywidualne konto rozliczeniowe dla programu Microsoft Online Services Program jest tworzone podczas rejestrowania się na platformie Azure za pomocą witryny internetowej Azure. Na przykład po zarejestrowaniu się w celu uzyskania [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [konta ze stawkami płatności zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/) lub jako [subskrybent programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Umowa Enterprise Agreement**: Konto rozliczeniowe dla umowy Enterprise Agreement jest tworzone, gdy organizacja podpisuje [umowę Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) w celu korzystania z platformy Azure.

- **Umowa klienta firmy Microsoft**: Konto rozliczeniowe dla umowy klienta firmy Microsoft jest tworzone, gdy organizacja współpracuje z przedstawicielem firmy Microsoft w celu podpisania umowy klienta firmy Microsoft. Niektórzy klienci w wybranych regionach, którzy zarejestrują się za pomocą witryny internetowej Azure w celu [utworzenia konta ze stawkami płatności zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/) lub dokonają podwyższenia poziomu swojego [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), mogą mieć również konto rozliczeniowe do umowy klienta firmy Microsoft. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z kontem rozliczeniowym do umowy klienta firmy Microsoft](./understand/mca-overview.md).

### <a name="scopes-for-billing-accounts"></a>Zakresy kont rozliczeniowych
Zakres to węzeł w ramach konta rozliczeniowego umożliwiający wyświetlanie rozliczeń i zarządzanie nimi. Jest to miejsce, w którym zarządzasz danymi rozliczeniowymi, płatnościami i fakturami oraz przeprowadzasz ogólne czynności dotyczące zarządzania kontami.

#### <a name="microsoft-online-services-program"></a>Microsoft Online Services Program

|Zakres  |Definicja  |
|---------|---------|
|Konto billingowe     | Reprezentuje pojedynczego właściciela (administratora konta) co najmniej jednej subskrypcji platformy Azure. Administrator konta jest autoryzowany do wykonywania różnych zadań rozliczeniowych, takich jak tworzenie subskrypcji, wyświetlanie faktur lub zmiana rozliczeń dla subskrypcji.  |
|Subskrypcja     |  Reprezentuje zgrupowanie zasobów platformy Azure. W zakresie subskrypcji są generowane faktury. Ma swoje formy płatności za faktury.|


#### <a name="enterprise-agreement"></a>Enterprise Agreement

|Zakres  |Definicja  |
|---------|---------|
|Konto billingowe    | Reprezentuje rejestrację umów Enterprise Agreement. W zakresie konta rozliczeniowego są generowane faktury. W skład jego struktury wchodzą działy i konta rejestracji.  |
|Dział     |  Opcjonalne zgrupowanie kont rejestracji.      |
|Konto rejestracji     |  Reprezentuje pojedynczego właściciela konta. W zakresie konta rejestracji są tworzone subskrypcje platformy Azure.  |


#### <a name="microsoft-customer-agreement"></a>Umowa klienta firmy Microsoft

|Zakres  |Zadania  |
|---------|---------|
|Konto billingowe     |   Reprezentuje umowę klienta dla wielu produktów i usług firmy Microsoft. W skład struktury konta rozliczeniowego wchodzą profile rozliczeniowe i sekcje faktur.   |
|Profil rozliczeniowy     |  Reprezentuje fakturę i formy płatności za nią. W tym zakresie jest generowana faktura. Profil rozliczeniowy może zawierać wiele sekcji faktur.      |
|Sekcja faktury     |   Reprezentuje grupę kosztów na fakturze. Z zakresem sekcji faktury są skojarzone subskrypcje i inne zakupy.    |


## <a name="understand-azure-cost-management"></a>Omówienie usługi Azure Cost Management
Zarządzanie kosztami to proces efektywnego planowania i kontrolowania kosztów związanych z działalnością. Zadania zarządzania kosztami są zazwyczaj wykonywane przez zespoły ds. finansów, zarządzania i aplikacji. Funkcja Zarządzanie kosztami i rozliczenia na platformie Azure pomaga organizacjom w planowaniu z uwzględnieniem kosztów. Ułatwia ona również efektywne analizowanie kosztów i podejmowanie działań mających na celu optymalizację wydatków dotyczących chmury. Aby dowiedzieć się więcej na temat podejścia do zarządzania kosztami w organizacji, zapoznaj się z artykułem [Azure Cost Management best practices](./costs/cost-mgt-best-practices.md) (Najlepsze rozwiązania w usłudze Azure Cost Management).

Obejrzyj [wideo z przeglądem usługi Azure Cost Management](https://www.youtube.com/watch?v=el4yN5cHsJ0), aby zapoznać się z krótkim omówieniem, jak usługa Azure Cost Management może pomóc w zaoszczędzeniu pieniędzy na platformie Azure. Aby obejrzeć inne wideo, odwiedź [kanał usługi Cost Management w serwisie YouTube](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

Mimo że te czynności są powiązane, rozliczanie różni się od zarządzania kosztami. Rozliczanie jest procesem fakturowania klientów za towary lub usługi oraz zarządzania relacjami komercyjnymi.

Usługa Cost Management przedstawia koszty i wzorce użycia w organizacji za pomocą zaawansowanej analizy. Raporty w usłudze Cost Management pokazują koszty użycia usług platformy Azure i ofert innych firm z portalu Marketplace. Koszty są oparte na wynegocjowanych cenach i współczynniku rezerwacji oraz uwzględniają rabaty z tytułu Korzyści użycia hybrydowego platformy Azure. Zbiorczo raporty przedstawiają wewnętrzne i zewnętrzne koszty użycia i opłat w witrynie Azure Marketplace. Inne opłaty, takie jak zakupy rezerwacji, pomoc techniczna i podatki, nie są jeszcze uwzględniane w raportach. Raporty pomagają w zrozumieniu wydatków i użycia zasobów, a także ułatwiają wyszukiwanie nietypowych wydatków. Dostępna jest również analiza predykcyjna. Usługa Cost Management używa grup zarządzania, budżetów i rekomendacji platformy Azure, aby w czytelny sposób przedstawić, jak wydatki są zorganizowane i jak można zmniejszyć koszty.

Witryny Azure Portal i różnych interfejsów API można używać do automatyzacji eksportu w celu zintegrowania danych kosztów z procesami i systemami zewnętrznymi. Dostępna jest również funkcja zautomatyzowanego eksportu danych rozliczeń oraz raporty zaplanowane.

### <a name="plan-and-control-expenses"></a>Planowanie i kontrolowanie kosztów

Usługa Cost Management ułatwia planowanie i kontrolowanie kosztów, oferując następujące funkcje: analiza kosztów, budżety, rekomendacje i eksportowanie danych zarządzania kosztami.

Analiza kosztów pozwala na eksplorowanie i analizowanie kosztów organizacyjnych. Można wyświetlić zagregowane koszty według organizacji, aby dowiedzieć się, gdzie występują koszty, i zidentyfikować trendy wydatków. Ponadto zakumulowane koszty w czasie można przeglądać w celu oszacowania miesięcznych, kwartalnych, a nawet rocznych trendów kosztów w odniesieniu do budżetu.

Budżety ułatwiają planowanie i spełnianie założeń odpowiedzialności finansowej w organizacji. Pomagają one zapobiegać przekroczeniom limitów lub progów kosztów. Budżety mogą również ułatwiać informowanie innych osób o ich wydatkach, aby aktywnie zarządzać kosztami. I można przy ich użyciu zobaczyć, jak kształtują się wydatki w czasie.

Rekomendacje pokazują, jak można przeprowadzić optymalizację i zwiększyć wydajność, identyfikując zasoby w stanie bezczynności i niedostatecznie używane. Mogą również dotyczyć tańszych opcji zasobów. Pracując z rekomendacjami, zmieniasz sposób używania zasobów, aby zaoszczędzić pieniądze. Aby rozpocząć pracę, należy najpierw zapoznać się z rekomendacjami dotyczącymi optymalizacji kosztów, aby zidentyfikować potencjalne przypadki nieefektywnego użycia. Następnie należy skorzystać z rekomendacji, aby zmodyfikować użycie zasobów platformy Azure do tańszej opcji. Na koniec należy zweryfikować wykonane akcje, aby upewnić się, że wprowadzanie zmian zakończyło się powodzeniem.

Jeśli używasz systemów zewnętrznych do uzyskiwania dostępu do danych zarządzania kosztami lub ich przeglądania, możesz łatwo wyeksportować dane z platformy Azure. Możesz również ustawić dzienny zaplanowany eksport w formacie CSV i przechowywać pliki danych w usłudze Azure Storage. Dane będą w tej sytuacji dostępne z systemu zewnętrznego.

### <a name="cloudyn-deprecation"></a>Wycofywanie rozwiązania Cloudyn

[Cloudyn](./cloudyn/overview.md) to usługa platformy Azure powiązana z usługą Cost Management, która zostanie wycofana do końca 2020 r. Istniejące funkcje usługi Cloudyn są zintegrowane bezpośrednio z witryną Azure Portal, gdzie tylko jest to możliwe. W tej chwili nie ma żadnych nowych klientów, ale produkt będzie nadal obsługiwany do czasu całkowitego wycofania.
 
Obejrzyj [wideo z porównaniem usług Azure Cost Management i Cloudyn](https://www.youtube.com/watch?v=15DzKPMBRxM), aby dowiedzieć się więcej o przypadkach, w których należy skorzystać z usługi Azure Cost Management lub Cloudyn w zależności od potrzeb firmy. Aby obejrzeć inne wideo, odwiedź [kanał usługi Cost Management w serwisie YouTube](https://www.youtube.com/c/AzureCostManagement).
 
>[!VIDEO https://www.youtube.com/embed/15DzKPMBRxM]

### <a name="additional-azure-tools"></a>Dodatkowe narzędzia platformy Azure

Platforma Azure oferuje inne narzędzia, które nie są częścią zestawu funkcji Zarządzanie kosztami i rozliczenia na platformie Azure. Pełnią one jednak ważną rolę w procesie zarządzania kosztami. Aby dowiedzieć się więcej o tych narzędziach, skorzystaj z poniższych linków.

- [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) — to narzędzie umożliwia szacowanie początkowych kosztów chmury.
- [Usługa Azure Migrate](../migrate/migrate-overview.md) — umożliwia ocenianie bieżącego obciążenia centrum danych w celu uzyskania szczegółowych informacji o potrzebach w zakresie zastępczego rozwiązania platformy Azure.
- [Usługa Azure Advisor](../advisor/advisor-overview.md) — umożliwia zidentyfikowanie nieużywanych maszyn wirtualnych i zapoznanie się z rekomendacjami w zakresie zakupów wystąpień zarezerwowanych platformy Azure.
- [Korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) — umożliwia oszczędzanie dzięki użyciu bieżących licencji systemu Windows Server lub programu SQL Server dla maszyn wirtualnych na platformie Azure.


## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już funkcje Zarządzanie kosztami i rozliczenia na platformie Azure, następnym krokiem jest rozpoczęcie pracy z tą usługą.

- Zacznij korzystać z usługi Azure Cost Management, aby [analizować koszty](./costs/quick-acm-cost-analysis.md).
- Możesz również dowiedzieć się więcej na temat [najlepszych rozwiązań w usłudze Azure Cost Management](./costs/cost-mgt-best-practices.md).
