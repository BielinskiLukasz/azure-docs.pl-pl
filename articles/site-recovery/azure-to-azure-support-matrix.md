---
title: Macierz obsługi usługi Azure Site Recovery do odzyskiwania po awarii maszyn wirtualnych IaaS platformy Azure między regionami platformy Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Zawiera podsumowanie obsługiwanych systemów operacyjnych i konfiguracji do usługi Azure Site Recovery replikacji maszyn wirtualnych (VM) z jednego regionu do innego na potrzeby odzyskiwania po awarii.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 10/28/2018
ms.author: raynew
ms.openlocfilehash: ad6bf2db44996684096deb2b351301e450d80249
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2018
ms.locfileid: "50747970"
---
# <a name="support-matrix-for-replicating-from-one-azure-region-to-another"></a>Macierz obsługi replikacji między regionami platformy Azure do innego

Ten artykuł zawiera podsumowanie obsługiwanych konfiguracji i składników podczas wdrażania odzyskiwania po awarii dzięki replikacji, trybu failover i odzyskiwania maszyn wirtualnych platformy Azure między regionami platformy Azure do innego, za pomocą [usługi Azure Site Recovery](site-recovery-overview.md) usługi.


## <a name="deployment-method-support"></a>Obsługa metody wdrażania

**Metoda wdrażania** |  **Obsługiwane / nieobsługiwane**
--- | ---
**Azure Portal** | Obsługiwane
**Program PowerShell** | [Replikacji Azure – Azure przy użyciu programu PowerShell](azure-to-azure-powershell.md)
**Interfejs API REST** | Nie jest obecnie obsługiwany
**Interfejs wiersza polecenia** | Nie jest obecnie obsługiwany


## <a name="resource-support"></a>Obsługa zasobów

**Akcja zasobu** | **Szczegóły**
--- | --- | ---
**Przenoszenie magazynu między grupami zasobów** | Nieobsługiwane
**Przenoszenie zasobów obliczeniowych i magazynu/sieci między grupami zasobów** | Nieobsługiwane.<br/><br/> Po maszyny Wirtualnej jest replikowany, przenieść Maszynę wirtualną lub skojarzone składniki, takie jak storage i sieci, należy wyłączyć i ponownie włączyć replikację dla maszyny Wirtualnej.
**Replikowanie maszyn wirtualnych platformy Azure z jednej subskrypcji do innej na potrzeby odzyskiwania po awarii** | Obsługiwane w ramach tej samej dzierżawie usługi Azure Active Directory.
**Migrowanie maszyn wirtualnych między regionami w ramach klastrów geograficzne obsługiwane (wewnątrz i między subskrypcjami)** | Obsługiwane w ramach tej samej dzierżawie usługi Azure Active Directory.
**Migrowanie maszyn wirtualnych w tym samym regionie** | Nieobsługiwane.

# <a name="region-support"></a>Obsługa regionu

Można replikować i odzyskiwanie maszyn wirtualnych między wszystkie dwóch regionach w obrębie tego samego klastra geograficznego.

**Geograficzne klastra** | **Regiony platformy Azure**
-- | --
Ameryka | Kanada Wschodnia, środkowe stany USA Kanada środkowe, południowo-, Zachodnia środkowe stany USA, wschodnie stany USA, wschodnie stany USA 2, zachodnie stany USA, zachodnie stany USA 2, środkowe stany USA, Północnośrodkowa
Europa | Zachodnie Zjednoczone Królestwo, południowe Zjednoczone Królestwo, Europa Północna, Europa Zachodnia, Francja środkowa, Francja Południowa
Azja | Indie Południowe, Indie środkowe, Azja południowo-wschodnia, Azja Wschodnia, Japonia Wschodnia, Japonia Zachodnia, Korea środkowa, Korea Południowa
Australia   | Australia Wschodnia, Australia Południowo-Wschodnia, Australia Środkowa, Australia Środkowa 2
Azure Government    | Administracja USA — Wirginia, administracja USA — Iowa, administracja USA — Arizona, administracja USA — Teksas, US DOD wschodnie stany, US dod — środkowe
Niemcy | Niemcy środkowe, Niemcy północno-wschodnie
Chiny | Chiny wschodnie, Chiny Północne

>[!NOTE]
>
> Dla regionu Brazylia Południowa, możesz zreplikować i trybie Failover do jednej z następujących czynności: południowo-środkowe stany USA, zachodnio-środkowe stany USA, wschodnie stany USA, wschodnie stany USA 2, zachodnie stany USA, zachodnie stany USA 2 i regionów północno-środkowe stany USA.

## <a name="cache-storage"></a>Magazyn pamięci podręcznej

Ta tabela zawiera podsumowanie obsługi dla konta magazynu pamięci podręcznej, które są używane przez usługę Site Recovery podczas replikacji.

**Ustawienie** | **Szczegóły**
--- | ---
Kont ogólnego przeznaczenia V2 storage (gorąca i chłodna warstwa) | Nieobsługiwane. | To ograniczenie istnieje dla magazynu pamięci podręcznej, ponieważ koszty transakcji dla wersji 2 są znacznie wyższe niż w wersji 1 konta magazynu.
Usługa Azure Storage zapory dla sieci wirtualnych  | Nie | Zezwalanie na dostęp do określonych sieci wirtualnej platformy Azure dla kont magazynu pamięci podręcznej używane do przechowywania replikowanych danych nie jest obsługiwane.



## <a name="replicated-machine-operating-systems"></a>Systemy operacyjne replikowanych maszyn

Usługa Site Recovery obsługuje replikację maszyn wirtualnych platformy Azure z systemami operacyjnymi, wymienione w tej sekcji.

### <a name="windows"></a>Windows

**System operacyjny** | **Szczegóły**
--- | ---
Windows Server 2016  | Instalacja Server Core, serwer ze środowiskiem pulpitu
Windows Server 2012 R2 |
Windows Server 2012 |
Windows Server 2008 R2 | Z dodatkiem SP1 lub nowszym

#### <a name="linux"></a>Linux

**System operacyjny** | **Szczegóły**
--- | ---
Red Hat Enterprise Linux | 6.7 6.8, 6,9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5   
CentOS | 6.5, 6.6, 6.7, 6.8, 6,9, 6.10, 7.0, 7.1, 7.2, 7.3,7.4, 7.5
Ubuntu 14.04 LTS Server | [Wersje obsługiwanych jądra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Ubuntu 16.04 LTS Server | [Wersja jądra obsługiwane](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Serwery systemu Ubuntu przy użyciu uwierzytelniania opartego na hasłach i logowania oraz pakietu cloud-init do konfigurowania chmur maszyn wirtualnych, może być logowania opartego na hasłach, wyłączona w trybie failover (w zależności od konfiguracji cloudinit). Logowania opartego na hasłach, można ponownie włączyć na maszynie wirtualnej poprzez zresetowanie hasła z obsługi > Rozwiązywanie problemów > menu Ustawienia (w trybie Failover maszyny Wirtualnej w witrynie Azure portal.
Debian 7 | [Wersje obsługiwanych jądra](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Wersje obsługiwanych jądra)](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | Z DODATKIEM SP1, SP2, Z DODATKIEM SP3. [Wersje obsługiwanych jądra](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | Z DODATKIEM SP3<br/><br/> Replikowanie maszyn z dodatkiem SP3 do SP4 uaktualnienie nie jest obsługiwane. Jeśli został uaktualniony replikowanej maszyny, należy wyłączyć replikację i ponownie włączyć replikację po uaktualnieniu.
SUSE Linux Enterprise Server 11 | Z DODATKIEM SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7<br/><br/> System Red Hat zgodne jądra lub podzielenie Enterprise jądra wersji 3 (UEK3).


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane wersje jądra systemu Ubuntu maszyn wirtualnych platformy Azure

**Wydania** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
14.04 LTS | 9.19 | 3.13.0-24-Generic do 3.13.0-153-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-131-generic |
14.04 LTS | 9.18 | 3.13.0-24-Generic do 3.13.0-151-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-128-generic |
14.04 LTS | 9.17 | 3.13.0-24-Generic do 3.13.0-147-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-124-generic |
14.04 LTS | 9.16 | 3.13.0-24-Generic do 3.13.0-144-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-119-generic |
|||
16.04 LTS | 9.19 | 4.4.0-21-Generic do 4.4.0-131-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-45-generic,<br/>4.15.0-13-Generic do 4.15.0-30-generic<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1018-azure <br/>4.15.0-1012-Azure do 4.15.0-1019-azure|
16.04 LTS | 9.18 | 4.4.0-21-Generic do 4.4.0-128-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-45-generic,<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1018-azure |
16.04 LTS | 9.17 | 4.4.0-21-Generic do 4.4.0-124-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-41-generic,<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1016-azure |
16.04 LTS | 9.16 | 4.4.0-21-Generic do 4.4.0-119-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-38-generic,<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1012-azure |


#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane wersje jądra systemu Debian dla maszyn wirtualnych platformy Azure

**Wydania** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
Debian 7 | 9.17,9.18,9.19 | 3.2.0-4-AMD64 do 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
Debian 7 | 9.16 | 3.2.0-4-AMD64 do 3.2.0-5-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.19 | 3.16.0-4-AMD64 do 3.16.0-6-amd64, 4.9.0-0.bpo.4-amd64 do 4.9.0-0.bpo.7-amd64 |
Debian 8 | 9.17, 9.18 | 3.16.0-4-AMD64 do 3.16.0-6-amd64, 4.9.0-0.bpo.4-amd64 do 4.9.0-0.bpo.6-amd64 |
Debian 8 | 9.16 | 3.16.0-4-AMD64 do 3.16.0-5-amd64, 4.9.0-0.bpo.4-amd64 do 4.9.0-0.bpo.5-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane wersje jądra systemu SUSE Linux Enterprise Server 12 maszyn wirtualnych platformy Azure

**Wydania** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP3 z dodatkiem SP1, SP2) | 9.19 | Z dodatkiem SP1 3.12.49-11-default do 3.12.74-60.64.40-default</br></br> 3.12.74-60.64.45-default SP1(LTSS) do 3.12.74-60.64.93-default</br></br> Z dodatkiem SP2 4.4.21-69-default do 4.4.120-92.70-default</br></br>4.4.121-92.73-default SP2(LTSS) do 4.4.121-92.80-default</br></br>Z dodatkiem SP3 4.4.73-5-default do 4.4.140-94.42-default |
SUSE Linux Enterprise Server 12 (SP3 z dodatkiem SP1, SP2) | 9.18 | Z dodatkiem SP1 3.12.49-11-default do 3.12.74-60.64.40-default</br></br> 3.12.74-60.64.45-default SP1(LTSS) do 3.12.74-60.64.93-default</br></br> Z dodatkiem SP2 4.4.21-69-default do 4.4.120-92.70-default</br></br>4.4.121-92.73-default SP2(LTSS) do 4.4.121-92.80-default</br></br>Z dodatkiem SP3 4.4.73-5-default do 4.4.138-94.39-default |
SUSE Linux Enterprise Server 12 (SP3 z dodatkiem SP1, SP2) | 9.17 | Z dodatkiem SP1 3.12.49-11-default do 3.12.74-60.64.40-default</br></br> 3.12.74-60.64.45-default SP1(LTSS) do 3.12.74-60.64.88-default</br></br> Z dodatkiem SP2 4.4.21-69-default do 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default</br></br>Z dodatkiem SP3 4.4.73-5-default do 4.4.126-94.22-default |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Replikowane maszyny - magazyn systemu/gościa pliku systemu Linux

* Systemy plików: ext3, ext4, ReiserFS (Suse Linux Enterprise Server tylko), XFS
* Menedżer woluminów: LVM2
* Oprogramowania wielościeżkowego: mapowania urządzenia


## <a name="replicated-machines---compute-settings"></a>Replikowane maszyny — ustawienia obliczeń

**Ustawienie** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Rozmiar | Dowolny rozmiar maszyny Wirtualnej platformy Azure, co najmniej 2 rdzeni procesora CPU i 1 GB pamięci RAM | Sprawdź [rozmiarów maszyn wirtualnych platformy Azure](../virtual-machines/windows/sizes.md).
Zestawy dostępności | Obsługiwane | Po włączeniu replikacji dla maszyny Wirtualnej platformy Azure z opcjami domyślnymi, zestaw dostępności jest tworzony automatycznie w oparciu o ustawienia regionu źródłowego. Te ustawienia można modyfikować.
Strefy dostępności | Nieobsługiwane | Obecnie nie można replikować maszyny wirtualne wdrożone w strefach dostępności.
Korzyści z używania hybrydowej (HUB) | Obsługiwane | Jeśli źródłowa maszyna wirtualna ma włączone, licencję Centrum testowania trybu failover lub przełączone w tryb failover maszyny Wirtualnej używa również licencji KONCENTRATORA.
Zestawy skalowania maszyn wirtualnych | Nieobsługiwane |
Obrazy z galerii platformy Azure — Microsoft opublikowane | Obsługiwane | Obsługiwane, jeśli maszyna wirtualna uruchamia się w obsługiwanym systemie operacyjnym.
Obrazy galerii platformy Azure — innych firm opublikowane | Obsługiwane | Obsługiwane, jeśli maszyna wirtualna uruchamia się w obsługiwanym systemie operacyjnym.
Obrazy niestandardowe - innych firm opublikowane | Obsługiwane | Obsługiwane, jeśli maszyna wirtualna uruchamia się w obsługiwanym systemie operacyjnym.
Maszyny wirtualne migrowane przy użyciu Site Recovery | Obsługiwane | Jeśli maszyna wirtualna VMware lub komputera fizycznego został zmigrowany na platformie Azure przy użyciu Site Recovery, musisz odinstalować starszej wersji usługi mobilności uruchomioną na maszynie i uruchom ponownie maszynę przed zreplikowaniem go do innego regionu platformy Azure.

## <a name="replicated-machines---disk-actions"></a>Replikowane maszyny - działań dysku

**Akcja** | **Szczegóły**
-- | ---
Zmiana rozmiaru dysku na zreplikowanej maszyny Wirtualnej | Obsługiwane
Dodawanie dysku do zreplikowanej maszyny Wirtualnej | Nieobsługiwane.<br/><br/> Użytkownik musi można wyłączyć replikacji dla maszyny Wirtualnej, Dodaj dysk, a następnie włącz ponownie replikację.

## <a name="replicated-machines---storage"></a>Replikowane maszyny - storage

Ta tabela zawiera podsumowanie obsługę dysku, dysk z danymi i tymczasowy dysk systemu operacyjnego maszyny Wirtualnej platformy Azure.

- Ważne jest, aby obserwować limity dysku maszyny Wirtualnej i elementy docelowe [Linux](../virtual-machines/linux/disk-scalability-targets.md) i [Windows](../virtual-machines/windows/disk-scalability-targets.md) maszyn wirtualnych, aby uniknąć problemów z wydajnością.
- W przypadku wdrożenia przy użyciu ustawień domyślnych, Usługa Site Recovery automatycznie tworzy disks i kont magazynu, w oparciu o ustawienia źródła.
- W przypadku dostosowania, upewnij się, że postępuj zgodnie z wytycznymi.

**Składnik** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Maksymalny rozmiar dysku systemu operacyjnego | 2048 GB | [Dowiedz się więcej](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms) dotyczące dysków maszyn wirtualnych.
Dysk tymczasowy | Nieobsługiwane | Dysk tymczasowy zawsze jest wykluczony z replikacji.<br/><br/> Nie wszystkie trwałych danych na dysku tymczasowym. [Dowiedz się więcej](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk).
Maksymalny rozmiar dysku danych | 4095 GB |
Maksymalna liczba dysków danych | Maksymalnie 64 zgodnie z pomocy technicznej dla określonego rozmiaru maszyny Wirtualnej platformy Azure | [Dowiedz się więcej](../virtual-machines/windows/sizes.md) o rozmiarach maszyn wirtualnych.
Częstotliwość zmian dysku danych | Maksymalnie 10 MB/s na dysk usługi premium storage. Maksymalnie 2 MB/s na dysk dla magazynu w warstwie standardowa. | Jeśli średni współczynnik zmian na dysku jest stale wyższa niż wartość maksymalna, replikacji nie będzie zapoznać się z nimi.<br/><br/>  Jednak maksymalny po przekroczeniu sporadycznie, replikację można zapoznać się z nimi, ale może zostać wyświetlony punktów odzyskiwania nieco opóźnione.
Dysk danych — konto magazynu w warstwie standardowa | Obsługiwane |
Dysk danych — konto magazynu premium storage | Obsługiwane | Maszyna wirtualna ma dyski rozkładają się na konta magazynu w warstwie standardowa i premium, po wybraniu innego docelowego konta magazynu dla każdego dysku, aby upewnić się, że mają taką samą konfigurację magazynu w regionie docelowym.
Dysk zarządzany — standardowa | Obsługiwane regiony systemu Azure, w których usługa Azure Site Recovery jest obsługiwany. |  
Dysk zarządzany — premium | Obsługiwane regiony systemu Azure, w których usługa Azure Site Recovery jest obsługiwany. |
Nadmiarowość | Usługi LRS i GRS są obsługiwane.<br/><br/> Magazyn ZRS nie jest obsługiwane.
Chłodnej i gorącej magazynu. | Nieobsługiwane | Dyski maszyny Wirtualnej nie są obsługiwane w chłodnej i gorącej magazynu.
Miejsca do magazynowania | Obsługiwane |         
Szyfrowanie danych magazynowanych (SSE) | Obsługiwane | Funkcja SSE jest ustawieniem domyślnym dla kont magazynu.   
Usługa Azure Disk Encryption (ADE) dla systemu operacyjnego Windows | Włączone dla maszyn wirtualnych [szyfrowanie za pomocą aplikacji usługi Azure AD](https://aka.ms/ade-aad-app) są obsługiwane |
Usługa Azure Disk Encryption (ADE) dla systemu Linux, systemu operacyjnego | Nieobsługiwane |
Dodaj lub usuń gorąco dysku | Nieobsługiwane | Dodaj lub Usuń dysk danych na maszynie Wirtualnej, należy wyłączyć replikację i włącz ponownie replikację maszyny Wirtualnej.
Wykluczanie dysku | Nieobsługiwane|   Dysk tymczasowy jest domyślnie wykluczony.
Bezpośrednie miejsca do magazynowania magazynu  | Nieobsługiwane|
Serwer plików skalowalny w poziomie  | Nieobsługiwane|
LRS | Obsługiwane |
GRS | Obsługiwane |
RA-GRS | Obsługiwane |
ZRS | Nieobsługiwane |  
Chłodnej i gorącej magazynu. | Nieobsługiwane | Dyski maszyny wirtualnej nie są obsługiwane w chłodnej i gorącej magazynu.
Usługa Azure Storage zapory sieci wirtualnej  | Yes | Jeśli jest ograniczenie dostępu sieci wirtualnej, dla kont magazynu, upewnij się, że zaufanych usług firmy Microsoft będą miały dostęp do konta magazynu.
Konta magazynu ogólnego przeznaczenia w wersji 2 (zarówno gorąca i chłodna warstwa) | Nie | Wzrost kosztów transakcji znacznie w porównaniu do ogólnego przeznaczenia w wersji 1 konta magazynu

>[!IMPORTANT]
> Upewnij się, że przestrzegasz maszyn wirtualnych dysku cele skalowalności i wydajności dla [Linux](../virtual-machines/linux/disk-scalability-targets.md) lub [Windows](../virtual-machines/windows/disk-scalability-targets.md) maszyn wirtualnych, aby uniknąć problemów z wydajnością. Jeśli stosujesz ustawienia domyślne, Usługa Site Recovery utworzy wymagane dyski i kont magazynu, w oparciu o konfigurację źródła. Dostosowywanie i wybrać własne ustawienia, upewnij się, wykonaj cele dotyczące skalowalności i wydajności dysku dla źródła maszyn wirtualnych.

## <a name="replicated-machines---networking"></a>Replikowane maszyny — sieci
**Konfiguracja** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
NIC | Maksymalna liczba obsługiwanych bierz określone rozmiaru maszyny Wirtualnej platformy Azure | Karty sieciowe są tworzone podczas tworzenia maszyny Wirtualnej podczas pracy awaryjnej.<br/><br/> Liczba kart sieciowych na maszynie Wirtualnej w trybie failover zależy od liczba kart sieciowych źródłowej maszyny Wirtualnej po włączeniu replikacji. Jeśli dodasz lub usuniesz kartę Sieciową po włączeniu replikacji, wpływa nie liczba kart sieciowych zreplikowaną maszyną Wirtualną po włączeniu trybu failover.
Internetowy moduł równoważenia obciążenia | Obsługiwane | Kojarzenie modułu równoważenia obciążenia wstępnie skonfigurowane, za pomocą skryptu usługi Azure Automation w planie odzyskiwania.
Wewnętrzny moduł równoważenia obciążenia | Obsługiwane | Kojarzenie modułu równoważenia obciążenia wstępnie skonfigurowane, za pomocą skryptu usługi Azure Automation w planie odzyskiwania.
Publiczny adres IP | Obsługiwane | Kojarzenie istniejącego publicznego adresu IP z karty sieciowej. Lub, tworzenie publicznego adresu IP i skojarz ją z kartą Sieciową za pomocą skryptu usługi Azure Automation w planie odzyskiwania.
Sieciowa grupa zabezpieczeń na karcie interfejsu Sieciowego | Obsługiwane | Kojarzenie sieciowej grupy zabezpieczeń z kartą Sieciową za pomocą skryptu usługi Azure Automation w planie odzyskiwania.  
Sieciowa grupa zabezpieczeń w podsieci | Obsługiwane | Kojarzenie sieciowej grupy zabezpieczeń z podsiecią, za pomocą skryptu usługi Azure Automation w planie odzyskiwania.
Zastrzeżony adres IP (statyczne) | Obsługiwane | Jeśli karta sieciowa źródłowej maszyny Wirtualnej ma statyczny adres IP, a podsieć docelowa ma ten sam adres IP dostępne, jest przypisany do w trybie Failover maszyny Wirtualnej.<br/><br/> Jeśli podsieć docelowa nie ma ten sam adres IP dostępny, jeden z dostępnych adresów IP w podsieci jest zarezerwowany dla maszyny Wirtualnej.<br/><br/> Można również określić stały adres IP i podsieci w **zreplikowane elementy** > **ustawienia** > **obliczenia i sieć**  >  **Interfejsy sieciowe**.
Dynamiczny adres IP | Obsługiwane | Karta sieciowa w źródle ma dynamicznych adresów IP kart Sieciowych w trybie Failover maszyny Wirtualnej jest również dynamiczne domyślnie.<br/><br/> Możesz zmodyfikować to stały adres IP w razie potrzeby.
Traffic Manager     | Obsługiwane | Tak, aby ruch jest kierowany do punktu końcowego w regionie źródłowym w regularnych odstępach czasu, a do punktu końcowego w regionie docelowym w przypadku trybu failover, można wstępnie skonfigurować usługi Traffic Manager.
System DNS platformy Azure | Obsługiwane |
Niestandardowe DNS  | Obsługiwane |    
Nieuwierzytelnione serwera Proxy | Obsługiwane | Zapoznaj się [dokument ze wskazówkami dotyczącymi sieci.](site-recovery-azure-to-azure-networking-guidance.md)    
Uwierzytelnionego serwera Proxy | Nieobsługiwane | Jeśli maszyna wirtualna korzysta z uwierzytelnionego serwera proxy dla połączenia wychodzącego, nie może być replikowane za pomocą usługi Azure Site Recovery.    
Sieć VPN między lokacjami, z lokalnych (z lub bez usługi ExpressRoute)| Obsługiwane | Upewnij się, że tras zdefiniowanych przez użytkownika i sieciowymi grupami zabezpieczeń są skonfigurowane w taki sposób, ruch odzyskiwania lokacji nie jest kierowany do sieci lokalnej. Zapoznaj się [dokument ze wskazówkami dotyczącymi sieci.](site-recovery-azure-to-azure-networking-guidance.md)  
Połączenie między sieciami Wirtualnymi | Obsługiwane | Zapoznaj się [dokument ze wskazówkami dotyczącymi sieci.](site-recovery-azure-to-azure-networking-guidance.md)  
Punkty końcowe usługi sieci wirtualnej | Obsługiwane | Jeśli jest ograniczenie dostępu sieci wirtualnej, dla kont magazynu, upewnij się, że zaufanych usług firmy Microsoft będą miały dostęp do konta magazynu. 
Accelerated Networking | Obsługiwane | Przyspieszona sieć musi być włączona na źródłowej maszynie Wirtualnej. [Dowiedz się więcej](azure-vm-disaster-recovery-with-accelerated-networking.md).



## <a name="next-steps"></a>Kolejne kroki
- Odczyt [wskazówki dotyczące replikowania maszyn wirtualnych platformy Azure networking](site-recovery-azure-to-azure-networking-guidance.md).
- Wdrażanie odzyskiwania po awarii przez [replikowanie maszyn wirtualnych platformy Azure](site-recovery-azure-to-azure.md).
