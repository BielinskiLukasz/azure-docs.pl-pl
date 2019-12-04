---
title: Odzyskiwanie po awarii i tryb failover konta magazynu (wersja zapoznawcza) — Azure Storage
description: Usługa Azure Storage obsługuje tryb failover (wersja zapoznawcza) konta magazynu geograficznie nadmiarowego. Korzystając z trybu failover konta, można zainicjować proces trybu failover dla konta magazynu, jeśli podstawowy punkt końcowy stanie się niedostępny.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: d9daff390aa1678c25f4bf9c29b0293d96c43f48
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74775932"
---
# <a name="disaster-recovery-and-storage-account-failover-preview-in-azure-storage"></a>Odzyskiwanie po awarii i tryb failover konta magazynu (wersja zapoznawcza) w usłudze Azure Storage

Firma Microsoft dąży do zapewnienia, że usługi platformy Azure są zawsze dostępne. Może jednak wystąpić nieplanowana awaria usługi. Jeśli aplikacja wymaga odporności, firma Microsoft zaleca korzystanie z magazynu geograficznie nadmiarowego, dzięki czemu dane są replikowane w drugim regionie. Ponadto klienci powinni mieć plan odzyskiwania po awarii na potrzeby obsługi regionalnej awarii usługi. Ważna część planu odzyskiwania po awarii jest przygotowywana do przełączenia w tryb failover do pomocniczego punktu końcowego w przypadku, gdy podstawowy punkt końcowy stał się niedostępny. 

Usługa Azure Storage obsługuje tryb failover (wersja zapoznawcza) konta magazynu geograficznie nadmiarowego. Korzystając z trybu failover konta, można zainicjować proces trybu failover dla konta magazynu, jeśli podstawowy punkt końcowy stanie się niedostępny. Tryb failover aktualizuje pomocniczy punkt końcowy, aby stał się podstawowym punktem końcowym dla konta magazynu. Po zakończeniu pracy w trybie failover klienci mogą rozpocząć zapisywanie do nowego podstawowego punktu końcowego.

W tym artykule opisano koncepcje i procesy związane z trybem failover konta oraz omówiono sposób przygotowania konta magazynu do odzyskania przy minimalnym wpływie na klienta. Aby dowiedzieć się, jak zainicjować tryb failover konta w Azure Portal lub PowerShell, zobacz [inicjowanie trybu failover konta (wersja zapoznawcza)](storage-initiate-account-failover.md).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>Wybierz opcję z odpowiednią nadmiarowością

Wszystkie konta magazynu są replikowane pod kątem nadmiarowości. Wybrana opcja nadmiarowości dla konta zależy od stopnia odporności. Aby chronić przed awarią regionalną, wybierz magazyn Geograficznie nadmiarowy z lub bez opcji dostępu do odczytu z regionu pomocniczego:  

**Magazyn Geograficznie nadmiarowy (GRS)** replikuje dane asynchronicznie w dwóch regionach geograficznych, które znajdują się w odległości co najmniej setki kilometrów. Jeśli region podstawowy cierpi na awarię, region pomocniczy służy jako nadmiarowe źródło danych. Możesz zainicjować tryb failover, aby przekształcić pomocniczy punkt końcowy w podstawowy punkt końcowy.

**Magazyn Geograficznie nadmiarowy dostępny do odczytu (RA-GRS)** zapewnia Geograficznie nadmiarowy magazyn z dodatkową korzyścią dla dostępu do odczytu do pomocniczego punktu końcowego. W przypadku wystąpienia awarii podstawowego punktu końcowego aplikacje skonfigurowane dla usługi RA-GRS i zaprojektowane pod kątem wysokiej dostępności mogą nadal czytać z pomocniczego punktu końcowego. Firma Microsoft zaleca, aby dla aplikacji uzyskać maksymalną odporność na GRS.

Inne opcje nadmiarowości usługi Azure Storage obejmują magazyn strefowo nadmiarowy (ZRS), który replikuje dane między strefami dostępności w jednym regionie i lokalnie nadmiarowy magazyn (LRS), który replikuje dane w jednym centrum danych w jednym regionie. Jeśli konto magazynu jest skonfigurowane pod kątem ZRS lub LRS, możesz przekonwertować to konto, aby użyć GRS lub RA-GRS. Skonfigurowanie konta dla magazynu geograficznie nadmiarowego wiąże się z dodatkowymi kosztami. Aby uzyskać więcej informacji, zobacz [Replikacja usługi Azure Storage](storage-redundancy.md).

> [!NOTE]
> Magazyn strefy Geograficznie nadmiarowy (GZRS) oraz Geograficznie nadmiarowy magazyn (RA-GZRS) są obecnie w wersji zapoznawczej, ale nie są jeszcze dostępne w tych samych regionach co konto zarządzane przez klienta w trybie failover. Z tego powodu klienci nie mogą obecnie zarządzać zdarzeniami trybu failover konta przy użyciu kont GZRS i RA-GZRS. W ramach wersji zapoznawczej firma Microsoft będzie zarządzać wszystkimi zdarzeniami trybu failover wpływającymi na konta GZRS/RA-GZRS.

> [!WARNING]
> Magazyn Geograficznie nadmiarowy wykonuje ryzyko utraty danych. Dane są replikowane do regionu pomocniczego asynchronicznie, co oznacza, że istnieje opóźnienie między zapisaniem danych w regionie podstawowym do regionu pomocniczego. W przypadku awarii operacje zapisu w podstawowym punkcie końcowym, które nie zostały jeszcze zreplikowane do pomocniczego punktu końcowego, zostaną utracone.

## <a name="design-for-high-availability"></a>Projektowanie na potrzeby wysokiej dostępności

Ważne jest, aby zaprojektować aplikację pod kątem wysokiej dostępności od początku. Zapoznaj się z tymi zasobami platformy Azure, aby uzyskać wskazówki dotyczące projektowania aplikacji i planowania odzyskiwania po awarii:

* [Projektowanie odpornych aplikacji na platformę Azure](/azure/architecture/checklist/resiliency-per-service): przegląd najważniejszych pojęć dotyczących tworzenia aplikacji o wysokiej dostępności na platformie Azure.
* [Lista kontrolna dostępności](/azure/architecture/checklist/resiliency-per-service): Lista kontrolna służąca do sprawdzania, czy aplikacja implementuje najlepsze rozwiązania w zakresie projektowania wysokiej dostępności.
* [Projektowanie aplikacji o wysokiej dostępności przy użyciu usługi RA-GRS](storage-designing-ha-apps-with-ragrs.md): wskazówki dotyczące projektowania do kompilowania aplikacji w celu skorzystania z usługi RA-GRS.
* [Samouczek: Tworzenie aplikacji o wysokiej dostępności przy użyciu magazynu obiektów BLOB](../blobs/storage-create-geo-redundant-storage.md): samouczek pokazujący sposób tworzenia aplikacji o wysokiej dostępności, która automatycznie przełącza się między punktami końcowymi w miarę awarii i odzyskiwania. 

Ponadto należy pamiętać o najlepszych rozwiązaniach dotyczących utrzymania wysokiej dostępności dla danych usługi Azure Storage:

* **Dyski:** Użyj [Azure Backup](https://azure.microsoft.com/services/backup/) , aby utworzyć kopię zapasową dysków maszyny wirtualnej używanych przez maszyny wirtualne platformy Azure. Należy również rozważyć użycie [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) do ochrony maszyn wirtualnych w przypadku awarii regionalnej.
* **Blokowe obiekty blob:** Włącz opcję [usuwania nietrwałego](../blobs/storage-blob-soft-delete.md) , aby chronić przed usuwaniem na poziomie obiektów i zastępowaniem, lub kopiować blokowe obiekty blob na inne konto magazynu w innym regionie przy użyciu [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md)lub [biblioteki przenoszenia danych platformy Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* **Pliki:** Użyj [AzCopy](storage-use-azcopy.md) lub [Azure PowerShell](storage-powershell-guide-full.md) , aby skopiować pliki do innego konta magazynu w innym regionie.
* **Tabele:** Użyj [AzCopy](storage-use-azcopy.md) , aby wyeksportować dane tabeli do innego konta magazynu w innym regionie.

## <a name="track-outages"></a>Śledź przerwy

Klienci mogą subskrybować [pulpit nawigacyjny Azure Service Health](https://azure.microsoft.com/status/) , aby śledzić kondycję i stan usługi Azure Storage i innych usług platformy Azure.

Firma Microsoft zaleca również zaprojektowanie aplikacji w celu przygotowania się do wystąpienia błędów zapisu. Aplikacja powinna ujawniać błędy zapisu w taki sposób, aby ostrzegał o możliwości przestoju w regionie podstawowym.

## <a name="understand-the-account-failover-process"></a>Opis procesu przełączania do trybu failover dla konta

Tryb failover konta zarządzanego przez klienta (wersja zapoznawcza) umożliwia niepowodzenie całego konta magazynu w odniesieniu do regionu pomocniczego, jeśli podstawowy serwer stał się niedostępny z jakiegokolwiek powodu. Po wymuszeniu przejścia w tryb failover do regionu pomocniczego klienci mogą rozpocząć zapisywanie danych do pomocniczego punktu końcowego po zakończeniu pracy w trybie failover. Przełączenie w tryb failover trwa zwykle o godzinę.

### <a name="how-an-account-failover-works"></a>Jak działa tryb failover konta

W normalnych warunkach klient zapisuje dane na koncie usługi Azure Storage w regionie podstawowym, a dane są replikowane asynchronicznie do regionu pomocniczego. Na poniższej ilustracji przedstawiono scenariusz, w którym jest dostępny region podstawowy:

![Klienci zapisują dane na koncie magazynu w regionie podstawowym](media/storage-disaster-recovery-guidance/primary-available.png)

Jeśli podstawowy punkt końcowy jest niedostępny z jakiegoś powodu, klient nie będzie już mógł zapisywać na koncie magazynu. Na poniższej ilustracji przedstawiono scenariusz, w którym podstawowy stał się niedostępny, ale nie wystąpiło jeszcze odzyskiwanie:

![Podstawowa jest niedostępna, dlatego klienci nie mogą zapisywać danych](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

Klient inicjuje przejście w tryb failover konta do pomocniczego punktu końcowego. Proces trybu failover aktualizuje wpis DNS udostępniony przez usługę Azure Storage, aby pomocniczy punkt końcowy stał się nowym podstawowym punktem końcowym dla konta magazynu, jak pokazano na poniższej ilustracji:

![Klient inicjuje przejście w tryb failover konta do pomocniczego punktu końcowego](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

Dostęp do zapisu jest przywracany dla kont GRS i RA-GRS po aktualizacji wpisu DNS, a żądania są kierowane do nowego podstawowego punktu końcowego. Istniejące punkty końcowe usługi magazynu dla obiektów blob, tabel, kolejek i plików pozostają takie same po przejściu w tryb failover.

> [!IMPORTANT]
> Po zakończeniu pracy w trybie failover konto magazynu zostanie skonfigurowane jako lokalnie nadmiarowy w nowym podstawowym punkcie końcowym. Aby wznowić replikację do nowego elementu pomocniczego, skonfiguruj konto, aby ponownie używać magazynu geograficznie nadmiarowego (RA-GRS lub GRS).
>
> Należy pamiętać, że przekonwertowanie konta LRS na RA-GRS lub GRS ponosi koszt. Ten koszt dotyczy aktualizacji konta magazynu w nowym regionie podstawowym do korzystania z RA-GRS lub GRS po przejściu do trybu failover.  

### <a name="anticipate-data-loss"></a>Oczekiwanie na utratę danych

> [!CAUTION]
> Przełączenie w tryb failover jest zazwyczaj związane z utratą danych. Ważne jest, aby zrozumieć implikacje inicjowania trybu failover konta.  

Ponieważ dane są zapisywane asynchronicznie z regionu podstawowego do regionu pomocniczego, zawsze jest opóźnienie, zanim zapis w regionie podstawowym zostanie zreplikowany do regionu pomocniczego. Jeśli region podstawowy przestanie być dostępny, najnowsze zapisy mogą jeszcze nie zostać zreplikowane do regionu pomocniczego.

Po wymuszeniu przejścia w tryb failover wszystkie dane w regionie podstawowym zostaną utracone, ponieważ region pomocniczy stał się nowym regionem podstawowym, a konto magazynu jest skonfigurowane do lokalnego nadmiarowego. Wszystkie dane, które zostały już zreplikowane do pomocniczego, są zachowywane w przypadku przełączenia w tryb failover. Jednak wszystkie dane zapisywane w podstawowym, które nie zostały również zreplikowane do pomocniczej, zostaną trwale utracone. 

Właściwość **czas ostatniej synchronizacji** wskazuje, że ostatni czas, w którym dane z regionu podstawowego ma zostać zapisany w regionie pomocniczym. Wszystkie dane zapisywane przed upływem czasu ostatniej synchronizacji są dostępne na serwerze pomocniczym, a dane zapisywane po ostatniej synchronizacji mogą nie zostać zapisaną na pomocniczą i mogą zostać utracone. Użyj tej właściwości w przypadku przestoju, aby oszacować ilość utraconych danych, które mogą zostać naliczone przez zainicjowanie konta w trybie failover. 

Najlepszym rozwiązaniem jest zaprojektowanie aplikacji tak, aby można było użyć czasu ostatniej synchronizacji do oszacowania oczekiwanej utraty danych. Na przykład, Jeśli rejestrujesz wszystkie operacje zapisu, można porównać czas ostatniej operacji zapisu w czasie ostatniej synchronizacji, aby określić, które zapisy nie zostały zsynchronizowane z serwerem pomocniczym.

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Należy zachować ostrożność w przypadku powrotu po awarii do oryginalnego podstawowego

Po przełączeniu w tryb failover z poziomu podstawowego do regionu pomocniczego konto magazynu zostanie skonfigurowane jako lokalnie nadmiarowy w nowym regionie podstawowym. Można ponownie skonfigurować konto dla nadmiarowości geograficznej przez zaktualizowanie go do korzystania z usługi GRS lub RA-GRS. Jeśli konto jest skonfigurowane pod kątem nadmiarowości geograficznej ponownie po przejściu w tryb failover, nowy region podstawowy natychmiast rozpocznie replikację danych do nowego regionu pomocniczego, który był podstawą przed pierwotnym trybem failover. Jednak może upłynąć pewien czas, zanim istniejące dane w podstawowym są w pełni zreplikowane do nowej pomocniczej.

Po ponownym skonfigurowaniu konta magazynu pod kątem nadmiarowości geograficznej możliwe jest zainicjowanie kolejnej pracy w trybie failover z powrotem do nowego elementu pomocniczego. W takim przypadku pierwotny region podstawowy przed przełączeniem w tryb failover będzie ponownie regionem podstawowym i zostanie skonfigurowany do lokalnego nadmiarowego. Wszystkie dane w regionie podstawowym w trybie failover (oryginalna wersja pomocnicza) są następnie tracone. Jeśli większość danych znajdujących się na koncie magazynu nie została zreplikowana do nowego elementu pomocniczego przed powrotem po awarii, może to ponieść znaczną utratę danych. 

Aby uniknąć poważnej utraty danych, należy sprawdzić wartość właściwości **czas ostatniej synchronizacji** przed powrotem po awarii. Porównaj czas ostatniej synchronizacji z ostatnim czasem zapisania danych w nowym podstawowym celu oszacowania oczekiwanej utraty danych. 

## <a name="initiate-an-account-failover"></a>Inicjowanie trybu failover konta

Konto można zainicjować w trybie failover z poziomu Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API dostawcy zasobów usługi Azure Storage. Aby uzyskać więcej informacji na temat inicjowania trybu failover, zobacz [Inicjowanie konta trybu failover (wersja zapoznawcza)](storage-initiate-account-failover.md).

## <a name="about-the-preview"></a>Informacje o wersji zapoznawczej

Tryb failover konta jest dostępny w wersji zapoznawczej dla wszystkich klientów korzystających z usługi GRS lub RA-GRS z wdrożeniami Azure Resource Manager. Obsługiwane są tylko typy kont ogólnego przeznaczenia w wersji 1, ogólnego przeznaczenia w wersji 2 i BLOB Storage. Tryb failover konta jest obecnie dostępny w następujących regionach:

- Azja Wschodnia
- Azja Południowo-wschodnia
- Australia Wschodnia
- Australia Południowo-Wschodnia
- Środkowe stany USA
- Wschodnie stany USA 2
- Zachodnio-środkowe stany USA
- Zachodnie stany USA 2

Wersja zapoznawcza jest przeznaczona wyłącznie do użytku nieprodukcyjnego. Umowy dotyczące poziomu usług produkcyjnych (umowy SLA) nie są obecnie dostępne.

### <a name="register-for-the-preview"></a>Zarejestruj się w wersji zapoznawczej

Aby zarejestrować się w celu korzystania z wersji zapoznawczej, uruchom następujące polecenia w programie PowerShell. Pamiętaj, aby zastąpić symbol zastępczy w nawiasach własnym IDENTYFIKATORem subskrypcji:

```powershell
Connect-AzAccount -SubscriptionId <subscription-id>
Register-AzProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

Zatwierdzenie w wersji zapoznawczej może potrwać od 5-7 dni. Aby sprawdzić, czy rejestracja została zatwierdzona, uruchom następujące polecenie:

```powershell
Get-AzProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

### <a name="additional-considerations"></a>Dodatkowe zagadnienia 

Zapoznaj się z dodatkowymi zagadnieniami opisanymi w tej sekcji, aby zrozumieć, w jaki sposób można mieć wpływ na aplikacje i usługi w przypadku wymuszenia przejścia w tryb failover w okresie zapoznawczym

#### <a name="azure-virtual-machines"></a>Maszyny wirtualne platformy Azure

Usługi Azure Virtual Machines (VM) nie są przełączane w tryb failover w ramach pracy awaryjnej konta. Jeśli region podstawowy stał się niedostępny i przejdziesz w tryb failover do regionu pomocniczego, należy ponownie utworzyć wszystkie maszyny wirtualne po przejściu w tryb pracy awaryjnej. 

#### <a name="azure-unmanaged-disks"></a>Dyski niezarządzane platformy Azure

Najlepszym rozwiązaniem jest to, że firma Microsoft zaleca konwersję dysków niezarządzanych na dyski zarządzane. Jeśli jednak zachodzi potrzeba przełączenia konta zawierającego dyski niezarządzane dołączone do maszyn wirtualnych platformy Azure, należy zamknąć maszynę wirtualną przed zainicjowaniem trybu failover.

Dyski niezarządzane są przechowywane jako stronicowe obiekty blob w usłudze Azure Storage. Gdy maszyna wirtualna jest uruchomiona na platformie Azure, wszystkie dyski niezarządzane dołączone do maszyny wirtualnej są dzierżawione. Nie można przeprowadzić przejścia w tryb failover dla konta, gdy istnieje dzierżawa w obiekcie blob. Aby przeprowadzić przejście w tryb failover, wykonaj następujące kroki:

1. Przed rozpoczęciem należy zwrócić uwagę na nazwy wszystkich dysków niezarządzanych, ich numerów jednostek logicznych (LUN) oraz maszynę wirtualną, do której są dołączone. Wykonanie tej czynności ułatwi ponowne dołączenie dysków po przejściu do trybu failover. 
2. Zamknij maszynę wirtualną.
3. Usuń maszynę wirtualną, ale Zachowaj pliki VHD dla dysków niezarządzanych. Zanotuj godzinę usunięcia maszyny wirtualnej.
4. Poczekaj na zaktualizowanie **czasu ostatniej synchronizacji** i jest późniejsza niż godzina usunięcia maszyny wirtualnej. Ten krok jest ważny, ponieważ jeśli pomocniczy punkt końcowy nie został w pełni zaktualizowany przy użyciu plików VHD w przypadku przełączenia w tryb failover, maszyna wirtualna może nie działać poprawnie w nowym regionie podstawowym.
5. Zainicjuj tryb failover konta.
6. Poczekaj na zakończenie pracy w trybie failover konta i region pomocniczy staje się nowym regionem podstawowym.
7. Utwórz maszynę wirtualną w nowym regionie podstawowym i ponownie podłącz wirtualne dyski twarde.
8. Uruchom nową maszynę wirtualną.

Należy pamiętać, że wszystkie dane przechowywane na dysku tymczasowym zostaną utracone podczas zamykania maszyny wirtualnej.

### <a name="unsupported-features-or-services"></a>Nieobsługiwane funkcje lub usługi
Następujące funkcje lub usługi nie są obsługiwane w przypadku przełączania do trybu failover dla konta w wersji zapoznawczej:

- Azure File Sync nie obsługuje trybu failover dla konta magazynu. Konta magazynu zawierające udziały plików platformy Azure używane jako punkty końcowe chmury w Azure File Sync nie powinny być przenoszone do trybu failover. Wykonanie tej operacji spowoduje, że synchronizacja przestanie działać, a także może spowodować nieoczekiwaną utratę danych w przypadku nowych plików warstwowych.  
- Nie można przełączyć konta magazynu zawierającego zarchiwizowane obiekty blob w tryb failover. Obsługa zarchiwizowanych obiektów BLOB na oddzielnym koncie magazynu, które nie są planowane do trybu failover.
- Nie można przełączyć konta magazynu zawierającego blokowe obiekty blob w warstwie Premium. Konta magazynu obsługujące blokowe obiekty blob w warstwie Premium nie obsługują obecnie nadmiarowości geograficznej.
- Po zakończeniu pracy w trybie failover następujące funkcje przestaną działać, jeśli zostały początkowo włączone: [subskrypcje zdarzeń](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-overview), [Zasady cyklu życia](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts), [Rejestrowanie analityka magazynu](https://docs.microsoft.com/rest/api/storageservices/about-storage-analytics-logging).

## <a name="copying-data-as-an-alternative-to-failover"></a>Kopiowanie danych jako alternatywy dla trybu failover

Jeśli konto magazynu jest skonfigurowane dla usługi RA-GRS, masz dostęp do odczytu danych przy użyciu pomocniczego punktu końcowego. Jeśli wolisz przejść do trybu failover w przypadku awarii w regionie podstawowym, możesz użyć narzędzi, takich jak [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md)lub [Biblioteka przenoszenia danych platformy Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) , aby skopiować dane z konta magazynu w regionie pomocniczym do innego konta magazynu w nienaruszonym regionie. Następnie możesz wskazać swoje aplikacje na tym koncie magazynu, aby uzyskać dostęp do odczytu i zapisu.

## <a name="microsoft-managed-failover"></a>Tryb failover zarządzany przez firmę Microsoft

W skrajnych okolicznościach, gdy region zostanie utracony ze względu na znaczną awarię, firma Microsoft może zainicjować regionalną pracę w trybie failover. W takim przypadku nie jest wymagana żadna akcja z Twojej strony. Do momentu ukończenia pracy w trybie failover zarządzanej przez firmę Microsoft nie będziesz mieć dostępu do zapisu na koncie magazynu. Aplikacje mogą odczytywać z regionu pomocniczego, jeśli konto magazynu jest skonfigurowane dla usługi RA-GRS. 

## <a name="see-also"></a>Zobacz także

* [Inicjowanie trybu failover konta (wersja zapoznawcza)](storage-initiate-account-failover.md)
* [Projektowanie wysoko dostępnych aplikacji przy użyciu magazynu RA-GRS](storage-designing-ha-apps-with-ragrs.md)
* [Samouczek: Tworzenie aplikacji o wysokiej dostępności przy użyciu magazynu obiektów BLOB](../blobs/storage-create-geo-redundant-storage.md) 
