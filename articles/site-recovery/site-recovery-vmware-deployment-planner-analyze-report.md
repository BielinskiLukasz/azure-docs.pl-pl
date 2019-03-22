---
title: Analizowanie raportu Planisty wdrożenia usługi Azure Site Recovery do odzyskiwania po awarii programu VMware do platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak analizować raportu generowany przez Planistę wdrażania usługi Azure Site Recovery dla odzyskiwania po awarii programu VMware na platformę Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/20/2019
ms.author: mayg
ms.openlocfilehash: cbea6785239c70a3cdb229d0811497f051224238
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286350"
---
# <a name="analyze-the-azure-site-recovery-deployment-planner-report-for-vmware-disaster-recovery-to-azure"></a>Analizowanie raportu Planisty wdrożenia usługi Azure Site Recovery dla odzyskiwania po awarii programu VMware na platformę Azure

Wygenerowany raport programu Microsoft Excel zawiera następujące arkusze:
## <a name="on-premises-summary"></a>Podsumowanie środowiska lokalnego
Arkusz Podsumowanie środowiska lokalnego zawiera omówienie profilowanego środowiska VMware.

![Podsumowanie środowiska lokalnego VMware](media/site-recovery-vmware-deployment-planner-analyze-report/on-premises-summary-v2a.png)

**Data rozpoczęcia** i **Data zakończenia**: Daty rozpoczęcia i zakończenia profilowania danych na potrzeby generowania raportu. Domyślnie data rozpoczęcia to data rozpoczęcia profilowania, a data zakończenia to data zatrzymania profilowania. Mogą to być wartości „StartDate” i „EndDate”, jeśli raport jest generowany przy użyciu tych parametrów.

**Całkowita liczba dni profilowania**: Całkowita liczba dni profilowania od daty rozpoczęcia i zakończenia, dla których jest generowany raport.

**Liczba zgodnych maszyn wirtualnych**: Całkowita liczba zgodnych maszyn wirtualnych, dla których są obliczane wymagana przepustowość sieci oraz wymagana liczba kont magazynu, rdzeni Microsoft Azure, serwerów konfiguracji i dodatkowych serwerów przetwarzania.

**Łączna liczba dysków wszystkich zgodnych maszyn wirtualnych**: Numer który jest używany jako dane wejściowe do określania liczby serwerów konfiguracji i dodatkowych serwerów przetwarzania do użycia we wdrożeniu.

**Średnia liczba dysków na zgodną maszynę wirtualną**: Średnia liczba dysków obliczana dla wszystkich zgodnych maszyn wirtualnych.

**Średni rozmiar dysku (GB)**: Średni rozmiar dysku obliczany dla wszystkich zgodnych maszyn wirtualnych.

**Żądany cel punktu odzyskiwania (w minutach)**: Albo domyślny cel punktu odzyskiwania lub przekazana dla parametru "DesiredRPO" podczas generowania raportu umożliwiająca oszacowanie wymaganej przepustowości wartość.

**Żądana przepustowość (MB/s)**: Wartość przekazana dla parametru "Bandwidth" podczas generowania raportu umożliwiająca oszacowanie osiągalnego celu punktu odzyskiwania.

**Zaobserwowany Typowy współczynnik zmian danych dziennie (GB)**: Średni współczynnik zmian danych zaobserwowany we wszystkie dni profilowania. Jest on używany jako dane wejściowe do określania liczby serwerów konfiguracji i dodatkowych serwerów przetwarzania do użycia we wdrożeniu.

## <a name="recommendations"></a>Zalecenia

>[!Note]
>Podczas replikowania bezpośrednio do usługi managed disks, zignoruj zalecenie dla liczba kont magazynu.

Arkusz Zalecenia raportu dotyczącego replikacji z programu VMware do platformy Azure zawiera następujące informacje zgodnie z wybranym żądanym celem punktu odzyskiwania:

![Raport Zalecenia dotyczący replikacji z programu VMware do platformy Azure](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

### <a name="profiled-data"></a>Profilowane dane
![Widok profilowanych danych w planiście wdrożenia](media/site-recovery-vmware-deployment-planner-analyze-report/profiled-data-v2a.png)

**Okres profilowanych danych**: Okres, w którym zostało uruchomione profilowanie. Domyślnie narzędzie uwzględnia w obliczeniu wszystkie profilowane dane, o ile nie jest generowany raport za okres wyznaczony przy użyciu opcji StartDate i EndDate podczas generowania raportu.

**Nazwa serwera**: Nazwa lub adres IP VMware vCenter lub hoście ESXi, jest generowany raport maszyn wirtualnych.

**Żądany cel punktu odzyskiwania**: Cel punktu odzyskiwania dla danego wdrożenia. Domyślnie wymagana przepustowość sieci jest obliczana dla wartości celu punktu odzyskiwania równych 15, 30 i 60 minut. Odpowiednie wartości są aktualizowane w arkuszu zgodnie z wybraną wartością. W przypadku użycia parametru *DesiredRPOinMin* podczas generowania raportu ta wartość jest wyświetlana w obszarze wyniku żądanego celu punktu odzyskiwania.

### <a name="profiling-overview"></a>Omówienie profilowania

![Wyniki profilowania w planiście wdrożenia](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-v2a.png)

**Łączna liczba profilowanych maszyn wirtualnych**: Całkowita liczba maszyn wirtualnych, których profilowane dane są dostępne. Jeśli parametr VMListFile zawiera nazwy nieprofilowanych maszyn wirtualnych, te maszyny wirtualne nie zostaną uwzględnione podczas generowania raportów i zostaną wykluczone z łącznej liczby profilowanych maszyn wirtualnych.

**Zgodne maszyny wirtualne**: Liczba maszyn wirtualnych, które mogą być chronione na platformie Azure przy użyciu usługi Site Recovery. Jest to łączna liczba zgodnych maszyn wirtualnych, dla których są obliczane: wymagana przepustowość sieci, liczba kont magazynu, liczba rdzeni platformy Azure oraz liczba serwerów konfiguracji i dodatkowych serwerów przetwarzania. Szczegóły wszystkich maszyn wirtualnych są dostępne w sekcji „Zgodne maszyny wirtualne”.

**Niezgodne maszyny wirtualne**: Liczba profilowanych maszyn wirtualnych, które są zgodne na potrzeby ochrony za pomocą usługi Site Recovery. Przyczyny niezgodności wymieniono w sekcji „Niezgodne maszyny wirtualne”. Jeśli plik VMListFile zawiera nazwy maszyn wirtualnych, które nie były profilowane, te maszyny wirtualne są wykluczane z liczby niezgodnych maszyn wirtualnych. Dla tych maszyn wirtualnych jest wyświetlany tekst „Nie znaleziono danych” na końcu sekcji „Niezgodne maszyny wirtualne”.

**Żądany cel punktu odzyskiwania**: Żądany odzyskiwania punktu cel, w ciągu kilku minut. Raport jest generowany dla trzech wartości celu punktu odzyskiwania: 15 (ustawienie domyślne), 30 i 60 minut. Zalecenie dotyczące przepustowości w raporcie zmienia się zgodnie z pozycją wybraną z listy rozwijanej żądanego celu punktu odzyskiwania w prawym górnym rogu arkusza. Jeśli raport został wygenerowany przy użyciu parametru *-DesiredRPO* z wartością niestandardową, ta wartość będzie wyświetlana jako domyślna na liście rozwijanej żądanego celu punktu odzyskiwania.

### <a name="required-network-bandwidth-mbps"></a>Wymagana przepustowość sieci (Mb/s)

![Wymagana przepustowość sieci w planiście wdrożenia](media/site-recovery-vmware-deployment-planner-analyze-report/required-network-bandwidth-v2a.png)

**Aby osiągnąć cel punktu odzyskiwania przez 100 procent czasu:** Zalecana przepustowość w MB/s do przydzielenia, aby osiągnąć żądany cel punktu odzyskiwania 100 procent czasu. Taka przepustowość musi zostać przeznaczona na stałą replikację przyrostową wszystkich zgodnych maszyn wirtualnych, aby uniknąć naruszeń celu punktu odzyskiwania.

**Aby osiągnąć cel punktu odzyskiwania przez 90 procent czasu**: Ze względu na cenami połączeń szerokopasmowych lub innych powodów Jeśli nie można ustawić przepustowości wymaganej do osiągnąć żądany cel punktu odzyskiwania 100 procent czasu, można wybierać do korzystania z ustawienia mniejszej przepustowości, który spełnia wymagania w zakresie żądany cel punktu odzyskiwania przez 90 procent czasu. Aby umożliwić poznanie skutków ustawienia mniejszej przepustowości, w raporcie przedstawiono analizę warunkową liczby i czasu trwania naruszeń celu punktu odzyskiwania.

**Osiągnięta przepływność:** Przepływność z serwera, na którym uruchomiono polecenie GetThroughput regionem Microsoft Azure, w którym znajduje się na koncie magazynu. Ta wartość przepływności wskazuje przybliżony poziom, który można osiągnąć podczas ochrony zgodnych maszyn wirtualnych za pomocą usługi Site Recovery, jeśli charakterystyka magazynu i sieci serwera konfiguracji/serwera przetwarzania pozostają takie same jak w przypadku serwera, na którym uruchomiono narzędzie.

W przypadku replikacji należy ustawić zalecaną przepustowość tak, aby spełniała wymagania celu punktu odzyskiwania przez 100 procent czasu. Jeśli nie widzisz wzrostu osiągniętej przepływności zgłoszonej przez narzędzie po ustawieniu przepustowości, sprawdź następujące kwestie:

1. Ustal, czy w sieci jest używana technologia QoS (Quality of Service) ograniczająca przepływność usługi Site Recovery.

2. Ustal, czy magazyn usługi Site Recovery znajduje się w najbliższym obsługiwanym fizycznym regionie platformy Microsoft Azure, aby zminimalizować opóźnienie sieci.

3. Sprawdź charakterystyki magazynu lokalnego, aby ustalić, czy można ulepszyć sprzęt (np. zastąpić dysk twardy dyskiem SSD).

4. Zmień ustawienia usługi Site Recovery na serwerze przetwarzania, aby [zwiększyć przepustowość sieci na potrzeby replikacji](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

Jeśli narzędzie zostało uruchomione na serwerze konfiguracji lub serwerze przetwarzania, który ma już chronione maszyny wirtualne, należy uruchomić narzędzie kilka razy. Wartość osiągniętej przepływności zmienia się w zależności od wielkości współczynnika zmian przetwarzanego w danym momencie.

W przypadku wszystkich wdrożeń usługi Site Recovery w przedsiębiorstwach zalecamy użycie usługi [ExpressRoute](https://aka.ms/expressroute).

### <a name="required-storage-accounts"></a>Wymagane konta magazynu
Ten wykres przedstawia łączną liczbę kont magazynu (w warstwach Standardowa i Premium) wymaganych do ochrony wszystkich zgodnych maszyn wirtualnych. Aby dowiedzieć się, którego konta magazynu używać dla poszczególnych maszyn wirtualnych, zobacz sekcję „Rozmieszczenie maszyny wirtualnej względem magazynu”.

![Wymagane konta magazynu w planiście wdrożenia](media/site-recovery-vmware-deployment-planner-analyze-report/required-storage-accounts-v2a.png)

### <a name="required-number-of-azure-cores"></a>Wymagana liczba rdzeni platformy Azure
Wynik to łączna liczba rdzeni do skonfigurowania przed rozpoczęciem pracy w trybie failover lub testem pracy w trybie failover dla wszystkich zgodnych maszyn wirtualnych. Jeśli liczba rdzeni w ramach subskrypcji jest zbyt mała, usługa Site Recovery nie może utworzyć maszyn wirtualnych w czasie pracy w trybie failover lub testu pracy w trybie failover.

![Wymagana liczba rdzeni platformy Azure w planiście wdrożenia](media/site-recovery-vmware-deployment-planner-analyze-report/required-cores-v2a.png)

### <a name="required-on-premises-infrastructure"></a>Wymagana infrastruktura lokalna
Ta wartość to łączna liczba serwerów konfiguracji i dodatkowych serwerów przetwarzania do skonfigurowania w celu zapewnienia ochrony wszystkich zgodnych maszyn wirtualnych. W zależności od obsługiwanych [zaleceń dotyczących rozmiaru serwera konfiguracji](https://aka.ms/asr-v2a-on-prem-components) narzędzie może zalecić dodatkowe serwery. Zalecenie to opiera się na większej z następujących wartości: dziennego współczynnika zmian lub maksymalnej liczby chronionych maszyn wirtualnych (przy założeniu średnio trzech dysków na maszynę wirtualną) zależnie od tego, który z tych limitów zostanie osiągnięty jako pierwszy na serwerze konfiguracji lub dodatkowym serwerze przetwarzania. Szczegółowe informacje o łącznym dziennym współczynniku zmian oraz łącznej liczbie chronionych dysków znajdują się w sekcji „Podsumowanie środowiska lokalnego”.

![Wymagana infrastruktura lokalna w planiście wdrożenia](media/site-recovery-vmware-deployment-planner-analyze-report/required-on-premises-components-v2a.png)

### <a name="what-if-analysis"></a>Analiza warunkowa
Ta analiza przedstawia liczbę naruszeń, które mogą wystąpić podczas profilowania w przypadku ustawienia mniejszej przepustowości umożliwiającej osiągnięcie żądanego celu punktu odzyskiwania tylko przez 90 procent czasu. W danym dniu może wystąpić co najmniej jedno naruszenie. Wykres przedstawia szczyt celu punktu odzyskiwania w danym dniu.
Na podstawie tej analizy można zdecydować, czy liczba naruszeń celu punktu odzyskiwania we wszystkich dniach i szczytowa wartość celu punktu odzyskiwania na dzień jest dopuszczalna przy określonej mniejszej przepustowości. Jeśli tak, można przydzielić mniejszą przepustowość na potrzeby replikacji. W przeciwnym razie przydziel większą przepustowość zgodnie z propozycją, aby osiągnąć żądany cel punktu odzyskiwania przez 100 procent czasu.

![Analiza warunkowa w planiście wdrożenia](media/site-recovery-vmware-deployment-planner-analyze-report/what-if-analysis-v2a.png)

### <a name="recommended-vm-batch-size-for-initial-replication"></a>Zalecany rozmiar partii maszyn wirtualnych na potrzeby replikacji początkowej
W tej sekcji zalecamy liczbę maszyn wirtualnych, które mogą być chronione równolegle w celu ukończenia replikacji początkowej w ciągu 72 godzin przy proponowanej wydajności, aby spełniać wymagania celu punktu odzyskiwania przez 100 procent ustawianego czasu. Tę wartość można konfigurować. Aby zmienić ją podczas generowania raportu, użyj parametru *GoalToCompleteIR*.

Wykres w tym miejscu przedstawia zakres wartości przepustowości i obliczony rozmiar partii maszyn wirtualnych umożliwiające ukończenie replikacji początkowej w ciągu 72 godzin na podstawie wykrytego średniego rozmiaru wszystkich zgodnych maszyn wirtualnych.

W publicznej wersji zapoznawczej raport nie zawiera informacji o tym, które maszyny wirtualne mają być uwzględniane w partii. Możesz użyć rozmiaru dysku widocznego w sekcji „Zgodne maszyny wirtualne”, aby znaleźć rozmiar każdej maszyny wirtualnej i wybrać maszyny wirtualne dla partii albo na podstawie charakterystyki znanych obciążeń. Czas ukończenia replikacji początkowej zmienia się proporcjonalnie do rzeczywistego rozmiaru dysku maszyny wirtualnej, używanego miejsca na dysku i dostępnej przepływności sieci.

![Zalecany rozmiar partii zadań maszyny wirtualnej](media/site-recovery-vmware-deployment-planner-analyze-report/ir-batching-v2a.png)

### <a name="cost-estimation"></a>Szacowanie kosztów
Na wykresie przedstawiono podsumowanie szacowanych łącznych kosztów odzyskiwania po awarii (DR) na platformie Azure w wybranym regionie docelowym w walucie określonej na potrzeby generowania raportu.

![Podsumowanie szacowania kosztów](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

Podsumowanie pomaga zrozumieć ponoszone koszty magazynowania, obliczania, użycia sieci i licencji w przypadku ochrony wszystkich zgodnych maszyn wirtualnych na platformie Azure przy użyciu usługi Azure Site Recovery. Koszt jest obliczany dla zgodnych maszyn wirtualnych, a nie dla wszystkich profilowanych maszyn wirtualnych.  

Koszt można wyświetlić w rozliczeniu miesięcznym lub rocznym. Dowiedz się więcej o [obsługiwanych regionach docelowych](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) i [obsługiwanych walutach](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).

**Koszt według składników** łączny koszt odzyskiwania po awarii jest dzielony na cztery składniki: Koszt licencji usługi obliczeniowe, Magazyn, sieci i usługi Azure Site Recovery. Koszt jest obliczany na podstawie wykorzystania powstałego podczas replikacji oraz w czasie testowania odzyskiwania po awarii dla mocy obliczeniowych, magazynu (warstwa Premium i Standardowa), usługi ExpressRoute/sieci VPN skonfigurowanej między lokacją lokalną i platformą Azure oraz dla licencji usługi Azure Site Recovery.

**Koszt według stanów**: łączny koszt odzyskiwania po awarii (DR) jest dzielony na kategorie na podstawie dwóch różnych stanów — replikacji i testowania odzyskiwania po awarii.

**Koszt replikacji**:  Koszt, który zostanie naliczony podczas replikacji. Obejmuje on koszt magazynu, użycia sieci i licencji usługi Azure Site Recovery.

**Koszt testowania odzyskiwania po awarii**: Koszt, który zostanie naliczony podczas testów pracy w trybie Failover. Usługa Azure Site Recovery uruchamia maszyny wirtualne podczas testu pracy w trybie failover. Koszt testowania odzyskiwania po awarii obejmuje koszt magazynu i mocy obliczeniowej działających maszyn wirtualnych.

**Koszt magazynu platformy Azure na miesiąc/rok**: obejmuje łączny koszt magazynu, który zostanie naliczony dla magazynów w warstwie Premium i Standardowa podczas replikacji i testowania odzyskiwania po awarii.
W arkuszu [Szacowanie kosztów](site-recovery-vmware-deployment-planner-cost-estimation.md) możesz wyświetlić szczegółową analizę kosztów dla poszczególnych maszyn wirtualnych.

### <a name="growth-factor-and-percentile-values-used"></a>Używane wartości współczynnika wzrostu i percentyla
W tej sekcji w dolnej części arkusza pokazano wartość percentyla używaną przez wszystkie liczniki wydajności profilowanych maszyn wirtualnych (domyślnie jest używany 95. percentyl) i współczynnik wzrostu (wartość domyślna to 30 procent) używany we wszystkich obliczeniach.

![Używane wartości współczynnika wzrostu i percentyla](media/site-recovery-vmware-deployment-planner-analyze-report/growth-factor-v2a.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Zalecenia dotyczące danych wejściowych w postaci dostępnej przepustowości

![Zalecenia dotyczące danych wejściowych w postaci dostępnej przepustowości](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-bandwidth-input-v2a.png)

Może wystąpić sytuacja, w której nie można ustawić przepustowości większej niż x Mb/s na potrzeby replikacji usługi Site Recovery. Narzędzie umożliwia wprowadzenie dostępnej przepustowości (za pomocą parametru -Bandwidth podczas generowania raportu) i uzyskać osiągalny cel punktu odzyskiwania w minutach. Osiągalna wartość celu punktu odzyskiwania pozwala zdecydować, czy trzeba ustawić dodatkową przepustowość, czy rozwiązanie odzyskiwania po awarii może używać tego celu punktu odzyskiwania.

![Osiągalny cel punktu odzyskiwania dla przepustowości wynoszącej 500 Mb/s](media/site-recovery-vmware-deployment-planner-analyze-report/achievable-rpo-v2a.png)

## <a name="vm-storage-placement"></a>Rozmieszczenie maszyny wirtualnej względem magazynu

>[!Note]
>Podczas replikowania bezpośrednio do usługi managed disks, nie trzeba już martwić się o liczbę kont magazynu. W przypadku usługi storage w systemie tylko zalecenie typ magazynu (standardowa / Premium). Ten sam typ ma zastosowanie do dysków zarządzanych.

![Rozmieszczenie maszyny wirtualnej względem magazynu](media/site-recovery-vmware-deployment-planner-analyze-report/vm-storage-placement-v2a.png)

**Typ magazynu dysków**: Każda standardowa lub premium konta magazynu, który jest używany do replikacji wszystkich odpowiednich maszyn wirtualnych wymienionych w **maszyny wirtualne do rozmieszczenia** kolumny.

**Sugerowany prefiks**: Sugerowany 3 znakowy prefiks, który może służyć do nazwy konta magazynu. Możesz użyć własnego prefiksu, ale propozycja narzędzia będzie zgodna z [konwencją nazewnictwa partycji dla kont magazynu](https://aka.ms/storage-performance-checklist).

**Sugerowana nazwa konta**: Nazwa konta magazynu po uwzględnieniu proponowanego prefiksu. Zastąp nazwę w nawiasach kątowych (< i >) niestandardowymi danymi wejściowymi.

**Konto magazynu dzienników**: Wszystkie dzienniki replikacji są przechowywane na koncie magazynu w warstwie standardowa. W przypadku maszyn wirtualnych replikowanych do konta magazynu w warstwie Premium skonfiguruj dodatkowe konto magazynu w warstwie Standardowa na potrzeby magazynu dzienników. Jedno konto magazynu dzienników w warstwie Standardowa może być używane przez wiele kont magazynu replikacji w warstwie Premium. Maszyny wirtualne replikowane do kont magazynu w warstwie Standardowa używają tego samego konta magazynu dla dzienników.

**Sugerowana nazwa konta dzienników**: Twoja nazwa konta dzienników magazynu po uwzględnieniu proponowanego prefiksu. Zastąp nazwę w nawiasach kątowych (< i >) niestandardowymi danymi wejściowymi.

**Podsumowanie rozmieszczania**: Podsumowanie łączna liczba maszyn wirtualnych obciążenia na koncie magazynu podczas replikacji i testowania trybu failover lub pracy awaryjnej. Obejmuje ono łączną liczbę maszyn wirtualnych mapowanych na konto magazynu, łączną liczbę operacji we/wy odczytu i zapisu na sekundę dla wszystkich maszyn wirtualnych umieszczanych na tym koncie magazynu, łączną liczbę operacji we/wy zapisu na sekundę (replikacja), łączny skonfigurowany rozmiar wszystkich dysków oraz łączną liczbę dysków.

**Maszyny wirtualne do rozmieszczenia**: Lista wszystkich maszyn wirtualnych, które powinny zostać umieszczone na danym koncie magazynu w celu uzyskania optymalnej wydajności i użycia.

## <a name="compatible-vms"></a>Zgodne maszyny wirtualne
![Arkusz kalkulacyjny programu Excel zawierający zgodne maszyny wirtualne](media/site-recovery-vmware-deployment-planner-analyze-report/compatible-vms-v2a.png)

**Nazwa maszyny Wirtualnej**: Nazwa maszyny Wirtualnej lub adres IP, który jest używany w pliku VMListFile podczas generowania raportu. Ta kolumna obejmuje też dyski (VMDK) dołączone do maszyn wirtualnych. Aby wyróżnić maszyny wirtualne vCenter o zduplikowanych nazwach lub adresach IP, nazwy zawierają nazwę hosta ESXi. Wymieniony host ESXi to host, na którym umieszczono maszynę wirtualną odnaleziono w trakcie okresu profilowania.

**Zgodność maszyny Wirtualnej**: Wartości są **tak** i **tak**\*. **Tak** \* dla wystąpień, w których maszyna wirtualna odpowiada [premium SSD](../virtual-machines/windows/disks-types.md). Tutaj profilowany dysk o dużym współczynniku zmian lub dużej liczbie operacji we/wy należy do kategorii P20 lub P30, ale z powodu swojego rozmiaru jest mapowany w dół do kategorii P10 lub P20. Decyzja o tym, do którego typu dysku magazynu Premium będzie mapowany dysk, jest podejmowana na podstawie jego rozmiaru na poziomie konta magazynu. Na przykład:
* Mniej niż 128 GB — P10.
* 128 GB do 256 GB — P15.
* 256 GB do 512 GB — P20.
* 512 GB do 1024 GB — P30.
* 1025 GB do 2048 GB — P40.
* 2049 GB do 4095 GB — P50.

Na przykład jeśli charakterystyki obciążenia dysku powodują umieszczenie go w kategorii P20 lub P30, ale z powodu rozmiaru jest mapowany w dół do niższego typu magazynu Premium, narzędzie oznacza daną maszynę wirtualną jako **Tak**\*. Narzędzie zaleca również zmianę rozmiaru dysku źródłowego tak, aby mieścił się w zalecanym typie dysku Premium Storage lub zmianę docelowego typu dysku po zakończeniu pracy w trybie failover.

**Typ magazynu**: Standardowa lub premium.

**Sugerowany prefiks**: Konto magazynu 3 znakowy prefiks.

**Konto magazynu**: Nazwa która używa prefiks sugerowana konta magazynu.

**Szczytowy odczytu i zapisu na SEKUNDĘ (ze współczynnikiem wzrostu)**: Szczytowe obciążenia odczytu i zapisu operacji We/Wy na dysku (domyślnie jest używany 95. percentyl) wraz z przyszłym współczynnikiem wzrostu (wartość domyślna to 30 procent). Pamiętaj, że łączna liczba operacji we/wy odczytu i zapisu maszyny wirtualnej nie zawsze jest sumą operacji we/wy odczytu i zapisu z poszczególnych dysków maszyny wirtualnej, ponieważ szczytowa liczba operacji we/wy odczytu i zapisu maszyny wirtualnej to wartość szczytowa sumy operacji we/wy odczytu i zapisu poszczególnych dysków z każdej minuty okresu profilowania.

**Szczytowy współczynnik zmian danych w MB/s (ze współczynnikiem wzrostu)**: Szczytowy współczynnik zmian danych na dysku (domyślnie jest używany 95. percentyl) wraz z przyszłym współczynnikiem wzrostu (wartość domyślna to 30 procent). Pamiętaj, że łączny współczynnik zmian danych maszyny wirtualnej nie zawsze jest sumą współczynników zmian danych z poszczególnych dysków maszyny wirtualnej, ponieważ szczytowy współczynnik zmian danych maszyny wirtualnej to wartość szczytowa sumy współczynników zmian poszczególnych dysków z każdej minuty okresu profilowania.

**Rozmiar maszyny Wirtualnej platformy Azure**: Idealnie zamapowany rozmiar maszyny wirtualnej usług Azure Cloud Services dla tej maszyny Wirtualnej w środowisku lokalnym. Mapowanie jest oparte na wielkości pamięci, liczbie dysków/rdzeni/kart sieciowych oraz liczbie operacji we/wy zapisu i odczytu lokalnej maszyny wirtualnej. Zawsze zalecany jest najmniejszy rozmiar maszyny wirtualnej platformy Azure zgodny ze wszystkimi charakterystykami lokalnej maszyny wirtualnej.

**Liczba dysków**: Całkowita liczba dysków (Vmdk) maszyny wirtualnej, na maszynie Wirtualnej.

**Rozmiar dysku (GB)**: Łączny skonfigurowany rozmiar wszystkich dysków maszyny wirtualnej. W narzędziu jest też wyświetlany rozmiar poszczególnych dysków maszyny wirtualnej.

**Rdzenie**: Liczba rdzeni procesora CPU na maszynie Wirtualnej.

**Pamięć (MB)**: Pamięć RAM maszyny Wirtualnej.

**NICs**: Liczba kart sieciowych na maszynie Wirtualnej.

**Typ rozruchu**: Typ rozruchu maszyny wirtualnej. Dozwolone wartości to BIOS i EFI.  Obecnie usługa Azure Site Recovery obsługuje maszyny wirtualne EFI systemu Windows Server (Windows Server 2012, 2012 R2 i 2016) pod warunkiem, że liczba partycji na dysku rozruchowym jest mniejsza niż 4, a rozmiar sektora rozruchowego wynosi 512 bajtów. Aby można było chronić maszyny wirtualne EFI, wersja usługi mobilności Azure Site Recovery musi być równa 9.13 lub wyższa. Dla maszyn wirtualnych EFI jest obsługiwane tylko przejście w tryb failover. Powrót po awarii nie jest obsługiwany.  

**Typ systemu operacyjnego**: Jest to typ systemu operacyjnego maszyny wirtualnej. Może to być system Windows, Linux lub inny, zależnie od szablonu wybranego z oprogramowania VMware vSphere podczas tworzenia maszyny wirtualnej.  

## <a name="incompatible-vms"></a>Niezgodne maszyny wirtualne

![Arkusz kalkulacyjny programu Excel zawierający niezgodne maszyny wirtualne
](media/site-recovery-vmware-deployment-planner-analyze-report/incompatible-vms-v2a.png)

**Nazwa maszyny Wirtualnej**: Nazwa maszyny Wirtualnej lub adres IP, który jest używany w pliku VMListFile podczas generowania raportu. Ta kolumna obejmuje też dyski VMDK dołączone do maszyn wirtualnych. Aby wyróżnić maszyny wirtualne vCenter o zduplikowanych nazwach lub adresach IP, nazwy zawierają nazwę hosta ESXi. Wymieniony host ESXi to host, na którym umieszczono maszynę wirtualną odnaleziono w trakcie okresu profilowania.

**Zgodność maszyny Wirtualnej**: Wskazuje, dlaczego danej maszyny Wirtualnej jest zgodna na potrzeby użycia z usługą Site Recovery. Niezgodność każdego dysku na podstawie opublikowanych [limitów magazynów](https://aka.ms/azure-storage-scalbility-performance) może wynikać z dowolnej spośród następujących przyczyn:

* Rozmiar dysku jest większy niż 4095 GB. Usługa Azure Storage obecnie nie obsługuje dysków danych większych niż 4095 GB.

* Rozmiar dysku systemu operacyjnego jest większy niż 2048 GB. Usługa Azure Storage obecnie nie obsługuje dysków systemów operacyjnych większych niż 2048 GB.

* Łączny rozmiar maszyny wirtualnej (suma replikacji i testu pracy w trybie failover) przekracza obsługiwany limit rozmiaru konta magazynu (35 TB). Ta niezgodność występuje przeważnie, jeśli wartość charakterystyki wydajności pojedynczego dysku maszyny wirtualnej przekracza maksymalny obsługiwany limit standardowego magazynu platformy Azure lub usługi Site Recovery. Takie wystąpienie powoduje przeniesienie do strefy magazynów Premium Storage. Jednak maksymalny obsługiwany rozmiar konta magazynu Premium Storage jest równy 35 TB i jedna chroniona maszyna wirtualna nie może być chroniona na wielu kontach magazynu. Zauważ również, że przeprowadzenie testu pracy w trybie failover na chronionej maszynie wirtualnej powoduje również uruchomienie go na koncie magazynu z trwającą replikacją. W takiej sytuacji skonfiguruj podwojony rozmiar dysku na potrzeby równoległej kontynuacji replikacji i pomyślnie przeprowadzonego testu pracy w trybie failover.

* Źródłowe operacje we/wy na sekundę przekraczają obsługiwany limit operacji we/wy na sekundę magazynu wynoszący 7500 operacji na dysk.

* Źródłowe operacje we/wy na sekundę przekraczają obsługiwany limit operacji we/wy na sekundę maszyny wirtualnej wynoszący 80 000 operacji na dysk.

* Średni współczynnik zmian danych przekracza obsługiwany limit współczynnika zmian danych usługi Site Recovery wynoszący 10 MB/s dla średniego rozmiaru operacji we/wy na dysku.

* Średni współczynnik zmian danych przekracza obsługiwany limit współczynnika zmian danych usługi Site Recovery wynoszący 25 MB/s dla średniego rozmiaru operacji we/wy dla maszyny wirtualnej (suma współczynników zmian na wszystkich dyskach).

* Szczytowy współczynnik zmian danych dla wszystkich dysków na maszynie wirtualnej przekracza maksymalny obsługiwany limit szczytowego współczynnika zmian danych usługi Site Recovery wynoszący 54 MB/s na maszynę wirtualną.

* Średnia liczba operacji we/wy zapisu na sekundę przekracza obsługiwany limit operacji we/wy na sekundę usługi Site Recovery wynoszący 840 operacji na dysk.

* Obliczony magazyn migawek przekracza obsługiwany limit magazynu migawek wynoszący 10 TB.

* Łączny współczynnik zmian danych dziennie przekracza obsługiwany limit współczynnika zmian dziennie wynoszący 2 TB dla serwera przetwarzania.


**Szczytowy odczytu i zapisu na SEKUNDĘ (ze współczynnikiem wzrostu)**: Szczytowego obciążenia operacji We/Wy na dysku (domyślnie jest używany 95. percentyl) wraz z przyszłym współczynnikiem wzrostu (wartość domyślna to 30 procent). Pamiętaj, że łączna liczba operacji we/wy odczytu i zapisu maszyny wirtualnej nie zawsze jest sumą operacji we/wy odczytu i zapisu z poszczególnych dysków maszyny wirtualnej, ponieważ szczytowa liczba operacji we/wy odczytu i zapisu maszyny wirtualnej to wartość szczytowa sumy operacji we/wy odczytu i zapisu poszczególnych dysków z każdej minuty okresu profilowania.

**Szczytowy współczynnik zmian danych w MB/s (ze współczynnikiem wzrostu)**: Szczytowy współczynnik zmian danych na dysku (domyślnie używany 95. percentyl) wraz z przyszłym współczynnikiem wzrostu (wartość domyślna 30 procent). Pamiętaj, że łączny współczynnik zmian danych maszyny wirtualnej nie zawsze jest sumą współczynników zmian danych z poszczególnych dysków maszyny wirtualnej, ponieważ szczytowy współczynnik zmian danych maszyny wirtualnej to wartość szczytowa sumy współczynników zmian poszczególnych dysków z każdej minuty okresu profilowania.

**Liczba dysków**: Całkowita liczba dysków Vmdk maszyny Wirtualnej.

**Rozmiar dysku (GB)**: Łączny skonfigurowany rozmiar wszystkich dysków maszyny wirtualnej. W narzędziu jest też wyświetlany rozmiar poszczególnych dysków maszyny wirtualnej.

**Rdzenie**: Liczba rdzeni procesora CPU na maszynie Wirtualnej.

**Pamięć (MB)**: Ilość pamięci RAM na maszynie Wirtualnej.

**NICs**: Liczba kart sieciowych na maszynie Wirtualnej.

**Typ rozruchu**: Typ rozruchu maszyny wirtualnej. Dozwolone wartości to BIOS i EFI.  Obecnie usługa Azure Site Recovery obsługuje maszyny wirtualne EFI systemu Windows Server (Windows Server 2012, 2012 R2 i 2016) pod warunkiem, że liczba partycji na dysku rozruchowym jest mniejsza niż 4, a rozmiar sektora rozruchowego wynosi 512 bajtów. Aby można było chronić maszyny wirtualne EFI, wersja usługi mobilności Azure Site Recovery musi być równa 9.13 lub wyższa. Dla maszyn wirtualnych EFI jest obsługiwane tylko przejście w tryb failover. Powrót po awarii nie jest obsługiwany.

**Typ systemu operacyjnego**:  Jest to typ systemu operacyjnego maszyny wirtualnej. Może to być system Windows, Linux lub inny, zależnie od szablonu wybranego z oprogramowania VMware vSphere podczas tworzenia maszyny wirtualnej.

## <a name="azure-site-recovery-limits"></a>Limity usługi Azure Site Recovery
W poniższej tabeli przedstawiono limity usługi Azure Site Recovery. Limity te są oparte na naszych testach, ale nie obejmują wszystkich możliwych kombinacji operacji we/wy aplikacji. Rzeczywiste wyniki mogą różnić w zależności od kombinacji operacji we/wy aplikacji. Aby uzyskać najlepsze wyniki nawet po zakończeniu planowania wdrożenia, zawsze zalecamy dokładne przetestowanie aplikacji przy użyciu testu pracy w trybie failover w celu uzyskania prawdziwych informacji o wydajności aplikacji.

**Cel magazynu replikacji** | **Średni rozmiar źródłowych operacji we/wy na dysku** |**Średni źródłowy współczynnik zmian danych na dysku** | **Łączny współczynnik zmian danych na dysku dziennie**
---|---|---|---
Standard Storage | 8 KB | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 8 KB  | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 16 KB | 4 MB/s |  336 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 32 KB lub większy | 8 MB/s | 672 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 8 KB    | 5 MB/s | 421 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 16 KB lub większy | 20 MB/s | 1684 GB na dysk

**Źródłowy współczynnik zmian danych** | **Limit maksymalny**
---|---
Średni współczynnik zmian danych na maszynę wirtualną| 25 MB/s
Szczytowy współczynnik zmian danych na wszystkich dyskach na maszynie wirtualnej | 54 MB/s
Maksymalny współczynnik zmian danych dziennie obsługiwany przez serwer przetwarzania | 2 TB

Są to średnie wartości przy założeniu 30-procentowego nakładania się operacji we/wy. Usługa Site Recovery może obsługiwać większą przepływność na podstawie zakresu nakładania się na siebie, większego rozmiaru operacji zapisu i rzeczywistego zachowania związanego z obciążeniem operacji we/wy. Poprzednie liczby zakładają typowe zaległości wynoszące około pięć minut. Oznacza to, że przekazane dane są przetwarzane i punkt odzyskiwania jest tworzony w ciągu pięciu minut.


## <a name="cost-estimation"></a>Szacowanie kosztów
Dowiedz się więcej na temat [szacowania kosztów](site-recovery-vmware-deployment-planner-cost-estimation.md).


## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [szacowania kosztów](site-recovery-vmware-deployment-planner-cost-estimation.md).
