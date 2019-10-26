---
title: Macierz obsługi odzyskiwania po awarii lokalnych maszyn wirtualnych funkcji Hyper-V na platformie Azure
description: Podsumowuje obsługiwane składniki i wymagania dotyczące odzyskiwania po awarii maszyny wirtualnej funkcji Hyper-V na platformie Azure przy użyciu Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: raynew
ms.openlocfilehash: e34140f9e014cfd41b0c14e980ca74e4d07d0c85
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933841"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Macierz obsługi odzyskiwania po awarii lokalnych maszyn wirtualnych funkcji Hyper-V na platformie Azure


W tym artykule zestawiono obsługiwane składniki i ustawienia odzyskiwania po awarii lokalnych maszyn wirtualnych funkcji Hyper-V na platformie Azure przy użyciu [Azure Site Recovery](site-recovery-overview.md).

> [!WARNING]
> Należy pamiętać, że obsługa usługi ASR do korzystania z konfiguracji SCVMM do konta będzie wkrótce przestarzała i dlatego zalecamy zapoznanie się z informacjami dotyczącymi [przestarzałych](scvmm-site-recovery-deprecation.md) przed kontynuowaniem.


## <a name="supported-scenarios"></a>Obsługiwane scenariusze

**Scenariusz** | **Szczegóły**
--- | ---
Funkcja Hyper-V z Virtual Machine Manager <br> **Ten scenariusz znajduje się w ścieżce wycofania.** <br>| Odzyskiwanie po awarii można przeprowadzić na platformie Azure dla maszyn wirtualnych działających na hostach funkcji Hyper-V zarządzanych w sieci szkieletowej System Center Virtual Machine Manager.<br/><br/> Ten scenariusz można wdrożyć w Azure Portal lub przy użyciu programu PowerShell.<br/><br/> Gdy hosty funkcji Hyper-V są zarządzane przez program Virtual Machine Manager, można także wykonać odzyskiwanie po awarii do lokacji dodatkowej. Aby dowiedzieć się więcej na temat tego scenariusza, Przeczytaj [ten samouczek](hyper-v-vmm-disaster-recovery.md).
Funkcja Hyper-V bez Virtual Machine Manager | Odzyskiwanie po awarii można przeprowadzić na platformie Azure dla maszyn wirtualnych działających na hostach funkcji Hyper-V, które nie są zarządzane przez Virtual Machine Manager.<br/><br/> Ten scenariusz można wdrożyć w Azure Portal lub przy użyciu programu PowerShell.

## <a name="on-premises-servers"></a>Serwery lokalne

**Serwer** | **Wymagania** | **Szczegóły**
--- | --- | ---
Funkcja Hyper-V (uruchomiona bez Virtual Machine Manager) |  Windows Server 2019, Windows Server 2016 (w tym instalacja Server Core), Windows Server 2012 R2 z najnowszymi aktualizacjami | Jeśli już skonfigurowano system Windows Server 2012 R2 z/lub SCVMM 2012 R2 z Azure Site Recovery i planujesz uaktualnić system operacyjny, postępuj zgodnie z [dokumentacją.](upgrade-2012R2-to-2016.md) 
Funkcja Hyper-V (uruchomiona z Virtual Machine Manager) | Virtual Machine Manager 2019, Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 | Jeśli Virtual Machine Manager jest używany, hosty systemu Windows Server 2019 powinny być zarządzane w Virtual Machine Manager 2019. Podobnie hosty systemu Windows Server 2016 powinny być zarządzane w Virtual Machine Manager 2016.<br/><br/>


## <a name="replicated-vms"></a>Zreplikowane maszyny wirtualne


Poniższa tabela zawiera podsumowanie obsługi maszyn wirtualnych. Site Recovery obsługuje wszelkie obciążenia działające w obsługiwanym systemie operacyjnym.

 **Składnik** | **Szczegóły**
--- | ---
Konfiguracja maszyny wirtualnej | Maszyny wirtualne, które są replikowane na platformę Azure, muszą spełniać [wymagania dotyczące platformy Azure](#azure-vm-requirements).
System operacyjny gościa | Wszystkie systemy operacyjne gościa [obsługiwane przez platformę Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-guestos-update-matrix#family-5-releases)..<br/><br/> System Windows Server 2016 nano Server nie jest obsługiwany.


## <a name="vmdisk-management"></a>Zarządzanie MASZYNami wirtualnymi/dyskami

**Akcja** | **Szczegóły**
--- | ---
Zmień rozmiar dysku na zreplikowanej maszynie wirtualnej funkcji Hyper-V | Nieobsługiwane. Wyłącz replikację, wprowadź zmianę, a następnie ponownie Włącz replikację dla maszyny wirtualnej.
Dodawanie dysku do zreplikowanej maszyny wirtualnej funkcji Hyper-V | Nieobsługiwane. Wyłącz replikację, wprowadź zmianę, a następnie ponownie Włącz replikację dla maszyny wirtualnej.

## <a name="hyper-v-network-configuration"></a>Konfiguracja sieci funkcji Hyper-V

**Składnik** | **Funkcja Hyper-V z Virtual Machine Manager** | **Funkcja Hyper-V bez Virtual Machine Manager**
--- | --- | ---
Sieć hosta: Tworzenie zespołu kart interfejsu sieciowego | Tak | Tak
Sieć hosta: VLAN | Tak | Tak
Sieć hosta: IPv4 | Tak | Tak
Sieć hosta: IPv6 | Nie | Nie
Sieć VMNETWORK gościa: Tworzenie zespołu kart interfejsu sieciowego | Nie | Nie
Sieć VMNETWORK gościa: IPv4 | Tak | Tak
Sieć VMNETWORK gościa: IPv6 | Nie | Tak
Sieć VMNETWORK gościa: statyczny adres IP (system Windows) | Tak | Tak
Sieć VMNETWORK gościa: statyczny adres IP (Linux) | Nie | Nie
Sieć VMNETWORK gościa: wiele kart sieciowych | Tak | Tak



## <a name="azure-vm-network-configuration-after-failover"></a>Konfiguracja sieci maszyny wirtualnej platformy Azure (po przejściu w tryb failover)

**Składnik** | **Funkcja Hyper-V z Virtual Machine Manager** | **Funkcja Hyper-V bez Virtual Machine Manager**
--- | --- | ---
Usługa ExpressRoute systemu Azure | Tak | Tak
ILB | Tak | Tak
ELB | Tak | Tak
Azure Traffic Manager | Tak | Tak
Wiele kart sieciowych | Tak | Tak
Zastrzeżony adres IP | Tak | Tak
Adresów | Tak | Tak
Zachowaj źródłowy adres IP | Tak | Tak
Punkty końcowe usługi Azure Virtual Network<br/> (bez zapór usługi Azure Storage) | Tak | Tak
Accelerated Networking | Nie | Nie


## <a name="hyper-v-host-storage"></a>Magazyn hosta funkcji Hyper-V

**Magazyn** | **Funkcja Hyper-V z Virtual Machine Manager** | **Funkcja Hyper-V bez Virtual Machine Manager**
--- | --- | --- 
NFS | Nie dotyczy | Nie dotyczy
SMB 3.0 | Tak | Tak
SIEĆ SAN (ISCSI) | Tak | Tak
Wiele ścieżek (MPIO). Przetestowano przy użyciu:<br></br> Microsoft DSM, EMC PowerPath 5,7 SP4<br/><br/> EMC PowerPath DSM dla CLARiiON | Tak | Tak

## <a name="hyper-v-vm-guest-storage"></a>Magazyn maszyny wirtualnej funkcji Hyper-V

**Magazyn** | **Funkcja Hyper-V z Virtual Machine Manager** | **Funkcja Hyper-V bez Virtual Machine Manager**
--- | --- | ---
VMDK | Nie dotyczy | Nie dotyczy
DYSK VHD/VHDX | Tak | Tak
Maszyna wirtualna generacji 2 | Tak | Tak
INTERFEJS EFI/UEFI| Tak | Tak
Udostępniony dysk klastra | Nie | Nie
Zaszyfrowany dysk | Nie | Nie
NFS | Nie dotyczy | Nie dotyczy
SMB 3.0 | Nie | Nie
RDM | Nie dotyczy | Nie dotyczy
Dysk > 1 TB | Tak, do 4 095 GB | Tak, do 4 095 GB
Dysk: sektor logiczny i fizyczny 4K | Nieobsługiwane: Gen 1/Gen 2 | Nieobsługiwane: Gen 1/Gen 2
Dysk: sektor fizyczny i 512 b KB | Tak |  Tak
Zarządzanie woluminami logicznymi (LVM). LVM jest obsługiwana tylko na dyskach danych. Platforma Azure udostępnia tylko jeden dysk systemu operacyjnego. | Tak | Tak
Wolumin z dyskiem rozłożonym > 1 TB | Tak | Tak
Miejsca do magazynowania | Nie | Nie
Gorące Dodawanie/usuwanie dysku | Nie | Nie
Wykluczanie dysku | Tak | Tak
Wiele ścieżek (MPIO) | Tak | Tak

## <a name="azure-storage"></a>Azure Storage

**Składnik** | **Funkcja Hyper-V z Virtual Machine Manager** | **Funkcja Hyper-V bez Virtual Machine Manager**
--- | --- | ---
Magazyn lokalnie nadmiarowy | Tak | Tak
Magazyn geograficznie nadmiarowy | Tak | Tak
Magazyn Geograficznie nadmiarowy do odczytu | Tak | Tak
Chłodny magazyn | Nie | Nie
Magazyn gorąca| Nie | Nie
Obiekty BLOB typu Block | Nie | Nie
Szyfrowanie w spoczynku (SSE)| Tak | Tak
Premium Storage | Tak | Tak
Usługa importu/eksportu | Nie | Nie
Konta usługi Azure Storage z włączoną zaporą | Tak. Dla magazynu docelowego i pamięci podręcznej. | Tak. Dla magazynu docelowego i pamięci podręcznej.
Modyfikowanie konta magazynu | Nie. Nie można zmodyfikować docelowego konta usługi Azure Storage po włączeniu replikacji. Aby zmodyfikować, Wyłącz, a następnie włącz ponownie funkcję odzyskiwania po awarii. | Nie


## <a name="azure-compute-features"></a>Funkcje obliczeniowe platformy Azure

**Funkcja** | **Funkcja Hyper-V z Virtual Machine Manager** | **Funkcja Hyper-V bez Virtual Machine Manager**
--- | --- | ---
Zbiory dostępności | Tak | Tak
Centralny | Tak | Tak  
Dyski zarządzane | Tak, w przypadku trybu failover.<br/><br/> Powrót po awarii dysków zarządzanych nie jest obsługiwany. | Tak, w przypadku trybu failover.<br/><br/> Powrót po awarii dysków zarządzanych nie jest obsługiwany.

## <a name="azure-vm-requirements"></a>Wymagania dotyczące maszyny wirtualnej platformy Azure

Lokalne maszyny wirtualne replikowane na platformę Azure muszą spełniać wymagania dotyczące maszyny wirtualnej platformy Azure podsumowane w tej tabeli.

**Składnik** | **Wymagania** | **Szczegóły**
--- | --- | ---
System operacyjny gościa | Site Recovery obsługuje wszystkie systemy operacyjne, które są [obsługiwane przez platformę Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli nie jest obsługiwana
Architektura systemu operacyjnego gościa | 32-bitowy (Windows Server 2008)/64-bit | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli nie jest obsługiwana
Rozmiar dysku systemu operacyjnego | Do 2 048 GB dla maszyn wirtualnych 1. generacji.<br/><br/> Do 300 GB dla maszyn wirtualnych 2. generacji.  | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli nie jest obsługiwana
Liczba dysków systemu operacyjnego | 1 | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli nie jest obsługiwana
Liczba dysków danych | 16 lub mniej  | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli nie jest obsługiwana
Rozmiar dysku VHD z danymi | Do 4 095 GB | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli nie jest obsługiwana
Karty sieciowe | Obsługiwana jest konfiguracja z wieloma kartami sieciowymi |
Udostępniony wirtualny dysk twardy | Brak obsługi | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli nie jest obsługiwana
Dysk FC | Brak obsługi | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli nie jest obsługiwana
Format dysku twardego | VHD <br/><br/> DYSKU | W przypadku przełączenia w tryb failover na platformie Azure Site Recovery automatycznie konwertuje plik VHDX na dysk VHD. Po powrocie po awarii do lokalnego programu maszyny wirtualne nadal używają formatu VHDX.
BitLocker | Brak obsługi | Aby włączyć replikację dla maszyny wirtualnej, należy wyłączyć funkcję BitLocker.
Nazwa maszyny wirtualnej | Od 1 do 63 znaków. Ograniczone do liter, cyfr i łączników. Nazwa maszyny wirtualnej musi zaczynać się i kończyć literą lub cyfrą. | Zaktualizuj wartość we właściwościach maszyny wirtualnej w Site Recovery.
Typ maszyny wirtualnej | Generacja 1<br/><br/> Generacja 2 — Windows | Maszyny wirtualne generacji 2 z typem dysku systemu operacyjnego Basic (zawierającym jeden lub dwa woluminy danych sformatowane jako VHDX) i mniej niż 300 GB miejsca na dysku są obsługiwane.<br></br>Maszyny wirtualne 2. generacji systemu Linux nie są obsługiwane. [Dowiedz się więcej](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Akcje magazynu Recovery Services

**Akcja** |  **Funkcja Hyper-V z programem VMM** | **Funkcja Hyper-V bez programu VMM**
--- | --- | ---
Przenoszenie magazynu między grupami zasobów<br/><br/> W ramach i między subskrypcjami | Nie | Nie
Przenoszenie magazynu, sieci, maszyn wirtualnych platformy Azure między grupami zasobów<br/><br/> W ramach i między subskrypcjami | Nie | Nie

> [!NOTE]
> Podczas replikowania maszyn wirtualnych funkcji Hyper-v z lokalnego do platformy Azure można replikować tylko do jednej dzierżawy usługi AD z jednego określonego środowiska — lokacji funkcji Hyper-lub funkcji Hyper-V z programem VMM, zgodnie z wymaganiami.


## <a name="provider-and-agent"></a>Dostawca i agent

Aby upewnić się, że wdrożenie jest zgodne z ustawieniami w tym artykule, upewnij się, że korzystasz z najnowszej wersji dostawcy i agenta.

**Nazwa** | **Opis** | **Szczegóły**
--- | --- | --- 
Dostawca Azure Site Recovery | Koordynuje komunikację między serwerami lokalnymi i platformą Azure <br/><br/> Funkcja Hyper-V z Virtual Machine Manager: zainstalowane na serwerach Virtual Machine Manager<br/><br/> Funkcja Hyper-V bez Virtual Machine Manager: zainstalowana na hostach funkcji Hyper-V| Najnowsza wersja: 5.1.2700.1 (dostępna w Azure Portal)<br/><br/> [Najnowsze funkcje i poprawki](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Agent Microsoft Azure Recovery Services | Koordynuje replikację między maszynami wirtualnymi funkcji Hyper-V i systemem Azure<br/><br/> Zainstalowane na lokalnych serwerach funkcji Hyper-V (z Virtual Machine Manager lub bez niego) | Najnowszy Agent dostępny w portalu






## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [przygotować platformę Azure](tutorial-prepare-azure.md) do odzyskiwania po awarii lokalnych maszyn wirtualnych funkcji Hyper-V.
