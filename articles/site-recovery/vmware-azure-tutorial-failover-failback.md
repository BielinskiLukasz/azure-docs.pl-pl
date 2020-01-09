---
title: Przechodzenie w tryb failover maszyn wirtualnych VMware na platformę Azure za pomocą Site Recovery
description: Dowiedz się, jak przełączać maszyny wirtualne VMware do trybu failover na platformie Azure w Azure Site Recovery
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.custom: MVC
ms.openlocfilehash: 8501bb1a998eb08984a118bfa5d52d1e3f3e4f84
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/26/2019
ms.locfileid: "75498078"
---
# <a name="fail-over--vmware-vms"></a>Praca awaryjna maszyn wirtualnych VMware

W tym artykule opisano, jak przełączyć lokalną maszynę wirtualną VMware na platformę Azure przy użyciu [Azure Site Recovery](site-recovery-overview.md).

Jest to piąty samouczek w serii, który pokazuje, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla maszyn lokalnych.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdź, czy właściwości maszyny wirtualnej VMware są zgodne z wymaganiami platformy Azure.
> * Przełączenie określonych maszyn wirtualnych do trybu failover na platformie Azure.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrożenia dla scenariusza. Korzystają one z opcji domyślnych, tam gdzie to możliwe, i nie wyświetlają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe informacje na temat pracy awaryjnej, zobacz [maszyny wirtualne i serwery fizyczne](site-recovery-failover.md)w trybie failover.

[Dowiedz się więcej o](failover-failback-overview.md#types-of-failover) różnych typach trybu failover. Jeśli chcesz przejść do trybu failover wielu maszyn wirtualnych w planie odzyskiwania, zapoznaj się z [tym artykułem](site-recovery-failover.md).

## <a name="before-you-start"></a>Przed rozpoczęciem

Wykonaj poprzednie samouczki:

1. Upewnij się, że [skonfigurowano platformę Azure](tutorial-prepare-azure.md) do lokalnego odzyskiwania po awarii maszyn wirtualnych VMware, maszyn wirtualnych funkcji Hyper-V i maszyn fizycznych na platformie Azure.
2. Przygotuj lokalne środowisko [VMware](vmware-azure-tutorial-prepare-on-premises.md) na potrzeby odzyskiwania po awarii. 
3. Skonfiguruj odzyskiwanie po awarii dla [maszyn wirtualnych VMware](vmware-azure-tutorial.md).
4. Uruchom [drążenie odzyskiwania po awarii](tutorial-dr-drill-azure.md) , aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.

## <a name="verify-vm-properties"></a>Sprawdzanie właściwości maszyn wirtualnych

Przed uruchomieniem trybu failover Sprawdź właściwości maszyny wirtualnej, aby upewnić się, że maszyny wirtualne spełniają [wymagania platformy Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

Sprawdź właściwości w następujący sposób:

1. W obszarze **chronione elementy**wybierz pozycję **zreplikowane elementy**, a następnie wybierz maszynę wirtualną, którą chcesz zweryfikować.

2. Okienko **Zreplikowany element** zawiera podsumowanie informacji na temat maszyny wirtualnej, jej kondycję oraz najnowsze dostępne punkty odzyskiwania. Wybierz pozycję **Właściwości** , aby wyświetlić więcej szczegółów.

3. W obszarze **obliczenia i sieć**można modyfikować te właściwości zgodnie z wymaganiami:
    * Nazwa platformy Azure
    * Grupa zasobów
    * Rozmiar docelowy
    * [Zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md)
    * Ustawienia dysku zarządzanego

4. Można wyświetlać i modyfikować ustawienia sieci, w tym:

    * Sieć i podsieć, w której zostanie umieszczona maszyna wirtualna platformy Azure po przejściu do trybu failover.
    * Adres IP, który zostanie przypisany do niego.

5. W obszarze **Dyski** można znaleźć informacje dotyczące systemu operacyjnego i dysków danych maszyny wirtualnej.

## <a name="run-a-failover-to-azure"></a>Przełączanie do trybu failover na platformie Azure

1. W obszarze **ustawienia** > **zreplikowane elementy**wybierz maszynę wirtualną, która ma zostać przełączona w tryb failover, a następnie wybierz pozycję **Praca awaryjna**.
2. W obszarze **Tryb failover** wybierz **Punkt odzyskiwania**, do którego przełączenie w tryb failover ma zostać wykonane. Możesz użyć jednej z następujących opcji:
   * **Najnowszy**: ta opcja najpierw przetwarza wszystkie dane wysyłane do usługi Site Recovery. Zapewnia najniższy cel punktu odzyskiwania (RPO), ponieważ maszyna wirtualna platformy Azure utworzona po przejściu w tryb failover ma wszystkie dane, które zostały zreplikowane do Site Recovery podczas wyzwolenia trybu failover.
   * **Najnowsza przetworzony**: Ta opcja powoduje, że maszyna wirtualna nie jest w trybie failover do najnowszego punktu odzyskiwania przetworzonego przez Site Recovery. Ta opcja zapewnia niski RTO (cel czasu odzyskiwania), ponieważ nie trwa przetwarzanie nieprzetworzonych danych.
   * **Najnowsza spójna dla aplikacji**: Ta opcja nie powoduje przełączenia maszyny wirtualnej w tryb failover do najnowszego punktu odzyskiwania spójnego na poziomie aplikacji przetworzonego przez Site Recovery.
   * **Niestandardowe**: Ta opcja umożliwia określenie punktu odzyskiwania.

3. Wybierz opcję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover** , aby podjąć próbę zamknięcia źródłowych maszyn wirtualnych przed wyzwoleniem trybu failover. Tryb failover kontynuuje działanie nawet wtedy, gdy wyłączenie nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover.

W niektórych scenariuszach tryb failover wymaga dodatkowego przetwarzania, którego ukończenie trwa od 8 do 10 minut. Możesz zauważyć dłuższe czasy testowania pracy w trybie failover dla:

* Maszyny wirtualne VMware z uruchomioną wersją usługi mobilności starszą niż 9,8.
* Serwery fizyczne.
* Maszyny wirtualne VMware Linux.
* Maszyny wirtualne funkcji Hyper-V chronione jako serwery fizyczne.
* Maszyny wirtualne VMware, które nie mają włączonej usługi DHCP.
* Maszyny wirtualne VMware, które nie mają następujących sterowników rozruchowych: storvsc, VMBus, storflt, Intelide, ATAPI.

> [!WARNING]
> Nie Anuluj trybu failover w toku. Przed rozpoczęciem pracy w trybie failover zatrzymywana jest replikacja maszyny wirtualnej. Jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.

## <a name="connect-to-failed-over-vm"></a>Nawiązywanie połączenia z maszyną wirtualną w trybie failover

1. Jeśli chcesz nawiązać połączenie z maszynami wirtualnymi platformy Azure po przejściu w tryb failover przy użyciu usług Remote Desktop Protocol (RDP) i Secure Shell (SSH), [Sprawdź, czy wymagania zostały spełnione] ((ailover-powrót po awarii — przegląd. MD # Connect-to-Azure-After-failover).
2. Po przejściu w tryb failover przejdź do maszyny wirtualnej i sprawdź poprawność, [łącząc](../virtual-machines/windows/connect-logon.md) się z nią.
3. Jeśli po przejściu w tryb failover ma zostać użyty inny punkt odzyskiwania, użyj **Zmień punkt odzyskiwania** . Po zatwierdzeniu przejścia w tryb failover w następnym kroku ta opcja nie będzie już dostępna.
4. Po sprawdzeniu poprawności wybierz pozycję **Zatwierdź** , aby zakończyć punkt odzyskiwania maszyny wirtualnej po przejściu do trybu failover.
5. Po zatwierdzeniu wszystkie pozostałe dostępne punkty odzyskiwania zostaną usunięte. Ten krok powoduje zakończenie pracy w trybie failover.

>[!TIP]
> Jeśli występują problemy z łącznością po przejściu do trybu failover, postępuj zgodnie z [przewodnikiem rozwiązywania problemów](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Następne kroki

Po przejściu w tryb failover ponownie Włącz ochronę maszyn wirtualnych platformy Azure w środowisku lokalnym. Następnie po ponownym włączeniu ochrony maszyn wirtualnych i przeprowadzeniu replikacji do lokacji lokalnej powrót po awarii z platformy Azure zakończy się niepowodzeniem.

> [!div class="nextstepaction"]
> Ponowne [Włączanie ochrony maszyn wirtualnych platformy azure](vmware-azure-reprotect.md)
> [powrót po awarii z platformy Azure](vmware-azure-failback.md)
