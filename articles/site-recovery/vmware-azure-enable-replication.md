---
title: Włącz maszyny wirtualne VMware na potrzeby odzyskiwania po awarii przy użyciu Azure Site Recovery
description: W tym artykule opisano sposób włączania replikacji maszyn wirtualnych VMware na potrzeby odzyskiwania po awarii przy użyciu usługi Azure Site Recovery
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 06/28/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 10b3e572ec61d1eff342f24a6a5a7bcba6276983
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79257318"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Włącz replikację do platformy Azure dla maszyn wirtualnych VMware

W tym artykule opisano sposób włączania replikacji lokalnych maszyn wirtualnych programu VMware na platformę Azure.

## <a name="resolve-common-issues"></a>Rozwiązywanie typowych problemów

* Każdy dysk powinien być mniejszy niż 4 TB.
* Dysk systemu operacyjnego powinien być dyskiem podstawowym, a nie dyskiem dynamicznym.
* W przypadku maszyn wirtualnych generacji 2/UEFI, Rodzina systemów operacyjnych powinna być systemem Windows, a dysk rozruchowy powinien być mniejszy niż 300 GB.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz:

- [Skonfiguruj lokalne środowisko źródłowe](vmware-azure-set-up-source.md).
- [Skonfiguruj środowisko docelowe na platformie Azure](vmware-azure-set-up-target.md).
- Przed rozpoczęciem [Sprawdź, czy zostały spełnione wymagania i wymagania wstępne](vmware-physical-azure-support-matrix.md) . Ważne rzeczy do zanotowania:
    - [Obsługiwane systemy operacyjne](vmware-physical-azure-support-matrix.md#replicated-machines) dla replikowanych maszyn.
    - Obsługa [magazynu i dysku](vmware-physical-azure-support-matrix.md#storage) .
    - [Wymagania dotyczące platformy Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements) , które powinny być zgodne z maszynami lokalnymi.


## <a name="before-you-start"></a>Przed rozpoczęciem
Podczas replikowania maszyn wirtualnych VMware należy pamiętać o następujących kwestiach:

* Twoje konto użytkownika platformy Azure musi mieć określone [uprawnienia](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) , aby umożliwić replikację nowej maszyny wirtualnej na platformie Azure.
* Maszyny wirtualne VMware są odnajdywane co 15 minut. Po przeprowadzeniu odnajdywania maszyny wirtualne mogą pojawić się w Azure Portal po 15 minutach lub dłużej. Podobnie odnajdywanie może potrwać 15 minut lub dłużej po dodaniu nowego serwera vCenter lub hosta vSphere.
* Wprowadzenie zmian w środowisku maszyny wirtualnej (na przykład instalacji narzędzi VMware) w portalu może potrwać 15 minut lub dłużej.
* Możesz sprawdzić czas ostatniego wykrycia maszyn wirtualnych VMware: zobacz **ostatni kontakt w** polu na stronie **serwery konfiguracji** dla hosta vCenter Server/vSphere.
* Aby dodać maszyny wirtualne do replikacji bez oczekiwania na zaplanowane odnajdywanie, wyróżnij serwer konfiguracji (ale nie klikaj go), a następnie wybierz pozycję **Odśwież**.
* Po włączeniu replikacji, jeśli maszyna wirtualna jest przygotowana, serwer przetwarzania automatycznie zainstaluje na nim usługę mobilności Azure Site Recovery.

## <a name="enable-replication"></a>Włączanie replikacji

Przed wykonaniem kroków opisanych w tej sekcji należy zwrócić uwagę na następujące informacje:
* Azure Site Recovery teraz replikuje dane bezpośrednio do dysków zarządzanych w przypadku wszystkich nowych replikacji. Serwer przetwarzania zapisuje dzienniki replikacji na koncie magazynu pamięci podręcznej w regionie docelowym. Te dzienniki są używane do tworzenia punktów odzyskiwania na dyskach zarządzanych repliki z konwencją nazewnictwa asrseeddisk.
* Obsługa replikacji z dyskami zarządzanymi w programie PowerShell jest dostępna za pomocą polecenia [AZ. RecoveryServices w wersji 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) 
* W momencie przejścia w tryb failover wybrany punkt odzyskiwania jest używany do tworzenia dysku zarządzanego przez użytkownika.
* Nie ma to wpływu na maszyny wirtualne, które zostały wcześniej skonfigurowane do replikowania na docelowe konta magazynu.
* Replikacja do kont magazynu dla nowej maszyny wirtualnej jest dostępna tylko za pośrednictwem interfejsu API REST (Representational State Transfer) i programu PowerShell. Aby przeprowadzić replikację do kont magazynu, użyj interfejsu API REST platformy Azure w wersji 2016-08-10 lub 2018-01-10.

Wykonaj poniższe kroki, aby włączyć replikację:
1. Przejdź do **kroku 2: Replikuj** **Źródło** > aplikacji. Po włączeniu replikacji po raz pierwszy wybierz pozycję **+ Replikuj** w magazynie, aby włączyć replikację dla dodatkowych maszyn wirtualnych.
2. Na stronie **źródłowej** > **Źródło**wybierz serwer konfiguracji.
3. W obszarze **Typ maszyny**wybierz pozycję **Virtual Machines** lub **maszyny fizyczne**.
4. W obszarze **vCenter/vSphere Hypervisor** wybierz serwer vCenter zarządzający hostem vSphere lub wybierz tego hosta. To ustawienie nie ma zastosowania, jeśli są replikowane komputery fizyczne.
5. Wybierz serwer przetwarzania. Jeśli nie utworzono żadnych dodatkowych serwerów przetwarzania, na liście rozwijanej będą dostępne skompilowany serwer przetwarzania na serwerze konfiguracji. Stan kondycji każdego serwera przetwarzania jest wskazany zgodnie z zalecanymi limitami i innymi parametrami. Wybierz serwer przetwarzania w dobrej kondycji. Nie można wybrać serwera przetwarzania [krytycznego](vmware-physical-azure-monitor-process-server.md#process-server-alerts) . Możesz [rozwiązywać problemy i](vmware-physical-azure-troubleshoot-process-server.md) rozwiązywać te błędy **albo** skonfigurować [serwer przetwarzania skalowalny](vmware-azure-set-up-process-server-scale.md)w poziomie.
    ![włączyć okna źródła replikacji](media/vmware-azure-enable-replication/ps-selection.png)

> [!NOTE]
> W [wersji 9,24](service-updates-how-to.md#links-to-currently-supported-update-rollups)wprowadzono dodatkowe alerty w celu zwiększenia poziomu alertów dotyczących kondycji serwera przetwarzania. Uaktualnij składniki Site Recovery do wersji 9,24 lub nowszej dla wszystkich alertów do wygenerowania.

6. W obszarze **cel**wybierz subskrypcję i grupę zasobów, w której chcesz utworzyć maszyny wirtualne w trybie failover. Wybierz model wdrażania, którego chcesz użyć na platformie Azure dla maszyn wirtualnych zakończonych niepowodzeniem.
2. Wybierz sieć i podsieć platformy Azure, z którą maszyny wirtualne platformy Azure będą łączyć się po przejściu do trybu failover. Sieć musi znajdować się w tym samym regionie co magazyn usługi Site Recovery.

   Wybierz pozycję **Konfiguruj teraz dla wybranych maszyn** , aby zastosować ustawienia sieci do wszystkich maszyn wirtualnych wybranych do ochrony. Wybierz pozycję **Konfiguruj później** , aby wybrać sieć platformy Azure na maszynę wirtualną. Jeśli nie masz sieci, musisz ją utworzyć. Aby utworzyć sieć przy użyciu Azure Resource Manager, wybierz pozycję **Utwórz nowy**. Wybierz podsieć, jeśli ma zastosowanie, a następnie wybierz przycisk **OK**.
   
   ![Włącz okno celu replikacji](./media/vmware-azure-enable-replication/enable-rep3.png)

1. W przypadku **maszyn wirtualnych** > **Wybieranie maszyn wirtualnych**zaznacz każdą maszynę wirtualną, którą chcesz zreplikować. Można wybrać tylko maszyny wirtualne, dla których można włączyć replikację. Następnie wybierz przycisk **OK**. Jeśli nie możesz wyświetlić lub wybrać żadnej konkretnej maszyny wirtualnej, zobacz [maszyna źródłowa nie znajduje się na liście w Azure Portal](https://aka.ms/doc-plugin-VM-not-showing) , aby rozwiązać ten problem.

    ![Okno włączania replikacji Wybieranie maszyn wirtualnych](./media/vmware-azure-enable-replication/enable-replication5.png)

1. Aby uzyskać **właściwości** > **konfigurowania właściwości**, wybierz konto, za pomocą którego serwer przetwarzania automatycznie zainstaluje Site Recovery usługę mobilności na maszynie wirtualnej. Ponadto wybierz typ docelowego dysku zarządzanego do replikacji na podstawie wzorców zmian danych.
10. Domyślnie są replikowane wszystkie dyski źródłowej maszyny wirtualnej. Aby wykluczyć dyski z replikacji, usuń zaznaczenie pola wyboru **Dołącz** dla wszystkich dysków, które nie mają być replikowane. Następnie wybierz przycisk **OK**. Później możesz skonfigurować dodatkowe właściwości. Dowiedz się więcej na temat [wykluczania dysków](vmware-azure-exclude-disk.md).

    ![Okno włączania konfiguracji replikacji](./media/vmware-azure-enable-replication/enable-replication6.png)

1. W obszarze **Ustawienia replikacji** > **konfigurowania ustawień replikacji**Sprawdź, czy wybrano odpowiednie zasady replikacji. Ustawienia zasad replikacji można modyfikować w **ustawieniach** > **zasady replikacji** > ***Nazwa zasad*** > **Edytowanie ustawień**. Zmiany stosowane do zasad dotyczą również replikowania i nowych maszyn wirtualnych.
1. Włączenie **spójności między maszynami** wirtualnymi umożliwia zebranie maszyn wirtualnych w grupie replikacji. Określ nazwę grupy, a następnie wybierz przycisk **OK**.

    > [!NOTE]
    >    * Maszyny wirtualne w grupie replikacji są replikowane razem i udostępniają punkty odzyskiwania spójne pod kątem awarii i spójne z aplikacjami po przełączeniu w tryb failover.
    >    * Zbierz maszyny wirtualne i serwery fizyczne w taki sposób, aby odzwierciedlały obciążenia. Włączenie spójności między MASZYNami wirtualnymi może wpłynąć na wydajność obciążeń. Zrób to tylko wtedy, gdy maszyny wirtualne działają w tym samym obciążeniu i potrzebujesz spójności.

    ![Włącz okno replikacji](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. Wybierz pozycję **Włącz replikację**. Postęp zadania **Włącz ochronę** można śledzić w **ustawieniach** > **zadania** > zadań **Site Recovery**. Po uruchomieniu zadania **finalizowania ochrony** maszyna wirtualna jest gotowa do pracy w trybie failover.

## <a name="view-and-manage-vm-properties"></a>Wyświetlanie właściwości maszyny wirtualnej i zarządzanie nimi

Następnie sprawdź właściwości źródłowej maszyny wirtualnej. Należy pamiętać, że nazwa maszyny wirtualnej platformy Azure musi być zgodna z [wymaganiami dotyczącymi maszyn wirtualnych platformy Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Przejdź do pozycji **ustawienia** > **zreplikowane elementy**, a następnie wybierz maszynę wirtualną. Na stronie **podstawowe** informacje o ustawieniach i stanie maszyny wirtualnej.
1. W obszarze **Właściwości** możesz wyświetlić informacje dotyczące replikacji i trybu failover dla danej maszyny wirtualnej.
1. We **właściwościach obliczeniowych** **obliczeniowych i > sieciowych** można zmienić wiele właściwości maszyny wirtualnej. 

    ![Okno właściwości obliczania i sieci](./media/vmware-azure-enable-replication/vmproperties.png)

    * Nazwa maszyny wirtualnej platformy Azure: zmodyfikuj nazwę, aby spełniała wymagania platformy Azure, w razie potrzeby.
    * Docelowy rozmiar maszyny wirtualnej lub typ maszyny wirtualnej: domyślny rozmiar maszyny wirtualnej jest wybierany na podstawie kilku parametrów, które obejmują liczbę dysków, liczbę kart sieciowych, liczbę rdzeni procesora CPU, pamięć i dostępne rozmiary ról maszyny wirtualnej w docelowym regionie platformy Azure. Azure Site Recovery wybiera pierwszy dostępny rozmiar maszyny wirtualnej, który spełnia wszystkie kryteria. Przed przejściem w tryb failover możesz wybrać inny rozmiar maszyny wirtualnej w zależności od potrzeb. Należy pamiętać, że rozmiar dysku maszyny wirtualnej jest również oparty na rozmiarze dysku źródłowego i można go zmienić tylko po przejściu do trybu failover. Dowiedz się więcej na temat rozmiarów dysków i szybkości operacji we/wy na [potrzeby skalowalności i wydajności dla dysków maszyn wirtualnych w systemie Windows](../virtual-machines/windows/disk-scalability-targets.md).

    *  Grupa zasobów: można wybrać [grupę zasobów](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines), z której maszyna wirtualna stanie się częścią przełączenia w tryb failover. To ustawienie można zmienić w dowolnym momencie przed przełączeniem w tryb failover. Po przejściu w tryb failover w przypadku migrowania maszyny wirtualnej do innej grupy zasobów ustawienia ochrony dla tej maszyny wirtualnej zostaną przerwane.
    * Zestaw dostępności: można wybrać [zestaw dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) , jeśli maszyna wirtualna musi być częścią wpisu do trybu failover. Po wybraniu zestawu dostępności należy pamiętać o następujących kwestiach:

        * Wyświetlane są tylko zestawy dostępności należące do określonej grupy zasobów.  
        * Maszyny wirtualne, które znajdują się w różnych sieciach wirtualnych, nie mogą być częścią tego samego zestawu dostępności.
        * Tylko maszyny wirtualne o tym samym rozmiarze mogą być częścią zestawu dostępności.
1. Możesz również dodać informacje dotyczące sieci docelowej, podsieci i adresu IP przypisanego do maszyny wirtualnej platformy Azure.
2. Na **dyskach**można zobaczyć system operacyjny i dyski danych na maszynie wirtualnej, która zostanie zreplikowana.

### <a name="configure-networks-and-ip-addresses"></a>Konfigurowanie sieci i adresów IP

Możesz ustawić docelowy adres IP. Jeśli nie podano adresu, maszyna wirtualna w trybie failover będzie korzystać z protokołu DHCP. Jeśli ustawisz adres, który nie jest dostępny w trybie failover, praca w trybie failover nie działa. Jeśli adres jest dostępny w sieci testowej pracy w trybie failover, można użyć tego samego docelowego adresu IP dla testowej pracy w trybie failover.

Liczba kart sieciowych zależy od rozmiaru określonego dla docelowej maszyny wirtualnej w następujący sposób:

- Jeśli liczba kart sieciowych w źródłowej maszynie wirtualnej jest mniejsza lub równa liczbie kart, które są dozwolone dla rozmiaru docelowej maszyny wirtualnej, obiekt docelowy ma taką samą liczbę kart sieciowych jak źródło.
- Jeśli liczba kart sieciowych dla źródłowej maszyny wirtualnej przekracza liczbę dozwoloną dla rozmiaru docelowej maszyny wirtualnej, używany jest maksymalny rozmiar docelowy. Na przykład jeśli źródłowa maszyna wirtualna ma dwie karty sieciowe, a rozmiar docelowej maszyny wirtualnej obsługuje cztery, docelowa maszyna wirtualna ma dwie karty. Jeśli źródłowa maszyna wirtualna ma dwie karty sieciowe, ale rozmiar docelowy obsługuje tylko jeden z nich, docelowa maszyna wirtualna ma tylko jedną kartę sieciową.
- Jeśli maszyna wirtualna ma kilka kart sieciowych, wszystkie będą łączyć się z tą samą siecią. Ponadto pierwsza karta wyświetlana na liście będzie *domyślną* kartą sieciową na maszynie wirtualnej platformy Azure. 

### <a name="azure-hybrid-benefit"></a>Korzyść użycia hybrydowego platformy Azure

Klienci programu Microsoft Software Assurance mogą korzystać z Korzyść użycia hybrydowego platformy Azure, aby zaoszczędzić na kosztach licencjonowania na komputerach z systemem Windows Server migrowanych do platformy Azure. Korzyść dotyczy również odzyskiwania po awarii platformy Azure. Jeśli masz uprawnienia, możesz przypisać korzyść do maszyny wirtualnej, która Site Recovery utworzona w przypadku przejścia w tryb failover. W tym celu wykonaj następujące kroki:
1. Przejdź do **właściwości komputera i sieci** zreplikowanej maszyny wirtualnej.
2. Odpowiedź na pytanie, czy masz licencję systemu Windows Server, która uprawnia do Korzyść użycia hybrydowego platformy Azure.
3. Upewnij się, że masz uprawniającą licencję systemu Windows Server z programem Software Assurance, której możesz użyć do zastosowania korzyści dla maszyny wirtualnej, która zostanie utworzona w trybie failover.
4. Zapisz ustawienia dla zreplikowanej maszyny wirtualnej.

Dowiedz się więcej o [korzyść użycia hybrydowego platformy Azure](https://aka.ms/azure-hybrid-benefit-pricing).



## <a name="next-steps"></a>Następne kroki

Gdy maszyna wirtualna osiągnie stan chroniony, spróbuj przełączyć się do [trybu failover](site-recovery-failover.md) , aby sprawdzić, czy aplikacja jest wyświetlana na platformie Azure.

* Dowiedz się, jak [wyczyścić ustawienia rejestracji i ochrony](site-recovery-manage-registration-and-protection.md) , aby wyłączyć replikację.
* Dowiedz się [, jak zautomatyzować replikację maszyn wirtualnych przy użyciu programu PowerShell](vmware-azure-disaster-recovery-powershell.md).
