---
title: Program VMware do architektury odzyskiwania po awarii platformy Azure w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie składników i architektury używanych podczas konfigurowania odzyskiwania po awarii lokalnych maszyn wirtualnych programu VMware na platformę Azure za pomocą usługi Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: raynew
ms.openlocfilehash: 58129ece0fb1b7f446e5f8d738b5d1135aa0256e
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212390"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>Program VMware do architektury odzyskiwania po awarii platformy Azure

W tym artykule opisano architekturę i procesów związanych z wdrożeniem replikacji odzyskiwania po awarii, tryb failover i odzyskiwanie maszyn wirtualnych VMware (VM) między lokalną lokacją programu VMware i platformy Azure za pomocą [usługi Azure Site Recovery](site-recovery-overview.md) servuce.


## <a name="architectural-components"></a>Składniki architektury

Poniższej tabeli i grafika przedstawia ogólny widok składniki używane do odzyskiwania po awarii programu VMware na platformę Azure.

**Składnik** | **Wymaganie** | **Szczegóły**
--- | --- | ---
**Azure** | Subskrypcja platformy Azure, konta usługi Azure Storage i sieci platformy Azure. | Replikowane dane z lokalnych maszyn wirtualnych są przechowywane na koncie magazynu. Maszyny wirtualne platformy Azure są tworzone z replikowanych danych, po uruchomieniu trybu failover ze środowiska lokalnego do platformy Azure. Maszyny wirtualne platformy Azure nawiązują połączenie z siecią wirtualną platformy Azure, gdy są tworzone.
**Konfiguracja komputera serwera** | Pojedynczy na komputerze lokalnym. Firma Microsoft zaleca, uruchom go jako maszyny Wirtualnej VMware, które mogą być wdrażane z pobranego szablonu OVF.<br/><br/> Komputer uruchamia wszystkie składniki usługi Site Recovery w środowisku lokalnym, które obejmują serwer konfiguracji, serwer przetwarzania oraz główny serwer docelowy. | **Serwer konfiguracji**: służy do koordynowania komunikacji między lokalną i platformą Azure oraz do zarządzania replikacją danych.<br/><br/> **Serwer przetwarzania**: instalowany domyślnie na serwerze konfiguracji. Odbiera dane replikacji; optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania; i wysyła je do usługi Azure Storage. Serwer przetwarzania instaluje także usługę mobilności usługi Azure Site Recovery na maszynach wirtualnych, którą chcesz replikować, i przeprowadza automatyczne odnajdywanie maszyn lokalnych. Wraz z rozwojem wdrożenia, możesz dodać dodatkowe, oddzielny proces serwerów w celu obsługi większych ilości ruchu związanego z replikacją.<br/><br/> **Główny serwer docelowy**: instalowany domyślnie na serwerze konfiguracji. Obsługuje on replikację danych podczas powrotu po awarii z platformy Azure. W przypadku dużych wdrożeń możesz dodać dodatkowe, oddzielny główny serwer docelowy do powrotu po awarii.
**Serwery VMware** | Maszyny wirtualne VMware są hostowane na serwerach lokalnych vSphere ESXi. Firma Microsoft zaleca serwera vCenter do zarządzania hostami. | Podczas wdrażania usługi Site Recovery serwery VMware są dodawane do magazynu usługi Recovery Services.
**Zreplikowane maszyny** | Usługa mobilności jest zainstalowana na każdej maszynie Wirtualnej VMware są replikowane. | Zaleca się, że zezwalają na automatycznej instalacji z serwera przetwarzania. Można też ręcznie zainstalować usługę, lub użyć metody wdrażania automatycznego, takie jak System Center Configuration Manager.

**Architektura VMware–Azure**

![Składniki](./media/vmware-azure-architecture/arch-enhanced.png)



## <a name="replication-process"></a>Proces replikacji

1. Po włączeniu replikacji dla maszyny Wirtualnej, rozpoczyna się Replikacja początkowa do usługi Azure storage, za pomocą zasad określonej replikacji. Pamiętaj o następujących kwestiach:
    - W przypadku maszyn wirtualnych VMware replikacja jest na poziomie bloku, w pobliżu ciągłe, przy użyciu agenta usługi mobilności, które są uruchomione na maszynie Wirtualnej.
    - Są stosowane żadne ustawienia zasad replikacji:
        - **Próg celu punktu odzyskiwania**. To ustawienie nie wpływa replikacji. Pomaga przy użyciu funkcji monitorowania. Zdarzenie jest zgłaszane, a opcjonalnie wysłana wiadomość e-mail, jeśli bieżący cel punktu odzyskiwania przekracza próg limitu, który określisz.
        - **Czas przechowywania punktu odzyskiwania**. To ustawienie określa, jak daleko wstecz w czasie, który ma nastąpić przejście po wystąpieniu przerwy w działaniu. Maksymalny czas przechowywania w magazynie premium storage wynosi 24 godziny. W magazynie standard storage to 72 godziny. 
        - **Migawki spójne z aplikacji**. Migawka spójności aplikacji może potrwać co 1 do 12 godzin, zależnie od potrzeb aplikacji. Migawki są migawki standardowych obiektów blob platformy Azure. Agenta mobilności uruchomionego na maszynie Wirtualnej żąda migawkę usługi VSS zgodnie z tego ustawienia i zakładki wskazujące punkt w czasie jako spójne na poziomie aplikacji w usłudze stream replikacji.

2. Ruch są replikowane do usługi Azure storage publicznych punktów końcowych za pośrednictwem Internetu. Alternatywnie można użyć usługi Azure ExpressRoute za pomocą [publicznej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#azure-public-peering). Replikowanie ruchu za pośrednictwem lokacja lokacja wirtualnej sieci prywatnej (VPN) z lokacji lokalnej do platformy Azure nie jest obsługiwane.
3. Po zakończeniu replikacji początkowej rozpoczyna się replikacja zmian różnicowych do platformy Azure. Śledzone zmiany dla maszyny są wysyłane do serwera przetwarzania.
4. Komunikacja odbywa się w następujący sposób:

    - Maszyny wirtualne komunikować się z lokalnym serwerem konfiguracji na porcie HTTPS 443 dla ruchu przychodzącego na potrzeby zarządzania replikacją.
    - Serwer konfiguracji organizuje replikację za pomocą platformy Azure za pośrednictwem portu HTTPS 443 dla ruchu wychodzącego.
    - Maszyny wirtualne wysyłają dane replikacji do serwera przetwarzania (uruchomionego na komputerze serwera konfiguracji) na porcie HTTPS 9443 dla ruchu przychodzącego. Ten port może być modyfikowany.
    - Serwer przetwarzania odbiera dane replikacji, optymalizuje je szyfruje i wysyła je do usługi Azure storage za pośrednictwem portu 443 wychodzących.




**Z programu VMware na proces replikacji platformy Azure**

![Proces replikacji](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proces pracy w trybie failover i podczas powrotu po awarii

Po skonfigurowaniu replikacji i uruchamianie próbnego odzyskiwania po awarii (Testowanie trybu failover), aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami, możesz uruchomić tryb failover i powrotu po awarii na potrzeby.

1. Uruchamiania kończą się niepowodzeniem dla pojedynczego komputera lub utworzyć odzyskiwania plany, aby przełączyć wiele maszyn wirtualnych w tym samym czasie. Zaletą planu odzyskiwania zamiast jednej maszyny w tryb failover obejmują:
    - Model z zależności aplikacji, umieszczając wszystkie maszyny wirtualne w aplikacji w pojedynczym planie odzyskiwania.
    - Można dodać skrypty, elementy runbook usługi Azure i wstrzymania dla działania ręczne.
2. Po wyzwoleniem początkowej trybu failover, należy zatwierdzić je, aby można było rozpocząć uzyskiwanie dostępu do obciążenia maszyny wirtualnej platformy Azure.
3. Gdy w lokacji głównej w środowisku lokalnym będzie znowu dostępna, można przygotować się do powrotu po awarii. Aby powrót po awarii, musisz skonfigurować infrastrukturę powrotu po awarii, w tym:

    * **Tymczasowy serwer przetwarzania na platformie Azure**: Aby zakończyć się niepowodzeniem, powrót po awarii z platformy Azure, należy skonfigurować Maszynę wirtualną platformy Azure do działania jako serwer przetwarzania do obsługi replikacji z platformy Azure. Po zakończeniu powrotu po awarii można usunąć tę maszynę wirtualną.
    * **Połączenie sieci VPN**: do powrotu po awarii, potrzebujesz połączenia sieci VPN (lub usługi ExpressRoute) z sieci platformy Azure do lokacji lokalnej.
    * **Osobny główny serwer docelowy**: Domyślnie główny serwer docelowy, który został zainstalowany na serwerze konfiguracji w lokalnych zasobów programu VMware maszyny Wirtualnej obsługuje powrót po awarii. Jeśli musisz zakończyć się niepowodzeniem ponownie dużych ilości ruchu należy skonfigurować oddzielny lokalny główny serwer docelowy do tego celu.
    * **Zasady powrotu po awarii**: aby móc przeprowadzić ponowną replikację do lokacji lokalnej, należy utworzyć zasady powrotu po awarii. Te zasady są tworzone podczas tworzenia zasad replikacji ze środowiska lokalnego do platformy Azure.
4. Po składniki znajdują się w miejscu, powrót po awarii odbywa się w trzy czynności:

    - Etap 1: Ponowne włączanie ochrony maszyn wirtualnych platformy Azure, dzięki czemu mają być replikowane na platformie Azure do lokalnych maszyn wirtualnych VMware.
    -  Etap 2: Uruchamianie trybu failover do lokacji lokalnej.
    - Etap 3: Po obciążeń przywrócono po awarii, możesz ponownie włączyć replikację dla maszyn wirtualnych w środowisku lokalnym.
    
 
**Podczas powrotu po awarii programu VMware na platformie Azure**

![Powrót po awarii](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Kolejne kroki

Postępuj zgodnie z [w tym samouczku](vmware-azure-tutorial.md) umożliwiające VMware do platformy Azure replikacji.
