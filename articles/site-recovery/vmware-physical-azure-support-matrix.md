---
title: Macierz obsługi dla oprogramowania VMware/fizycznego odzyskiwania po awarii w Azure Site Recovery
description: Podsumowuje obsługę odzyskiwania po awarii maszyn wirtualnych programu VMware i serwera fizycznego na platformie Azure przy użyciu Azure Site Recovery.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 1/10/2020
ms.author: raynew
ms.openlocfilehash: 1d6f5df59a318388a7a5fea75d1fd17e18cb7745
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864336"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Macierz obsługi odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure

W tym artykule opisano obsługiwane składniki i ustawienia odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure przy użyciu [Azure Site Recovery](site-recovery-overview.md).

- [Dowiedz się więcej](vmware-azure-architecture.md) o architekturze odzyskiwania po awarii maszyny wirtualnej VMware/serwera fizycznego.
- Postępuj zgodnie z naszymi [samouczkami](tutorial-prepare-azure.md) , aby wypróbować odzyskiwanie po awarii.

## <a name="deployment-scenarios"></a>Scenariusze wdrażania

**Scenariusz** | **Szczegóły**
--- | ---
Odzyskiwanie po awarii maszyn wirtualnych VMware | Replikacja lokalnych maszyn wirtualnych programu VMware na platformę Azure. Ten scenariusz można wdrożyć w Azure Portal lub przy użyciu [programu PowerShell](vmware-azure-disaster-recovery-powershell.md).
Odzyskiwanie po awarii serwerów fizycznych | Replikacja lokalnych serwerów z systemem Windows/Linux do platformy Azure. Ten scenariusz można wdrożyć w Azure Portal.

## <a name="on-premises-virtualization-servers"></a>Lokalne serwery wirtualizacji

**Serwer** | **Wymagania** | **Szczegóły**
--- | --- | ---
Program vCenter Server | Wersja 6,7, 6,5, 6,0 lub 5,5 | Zalecamy używanie serwera vCenter w ramach wdrożenia odzyskiwania po awarii.
hosty vSphere | Wersja 6,7, 6,5, 6,0 lub 5,5 | Zalecamy, aby hosty vSphere i serwery vCenter znajdowały się w tej samej sieci co serwer przetwarzania. Domyślnie serwer przetwarzania jest uruchamiany na serwerze konfiguracji. [Dowiedz się więcej](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Serwer konfiguracji Site Recovery

Serwer konfiguracji to komputer lokalny, na którym są uruchamiane składniki Site Recovery, w tym serwer konfiguracji, serwer przetwarzania oraz główny serwer docelowy.

- W przypadku maszyn wirtualnych VMware ustawiasz serwer konfiguracji, pobierając szablon OVF, aby utworzyć maszynę wirtualną VMware.
- W przypadku serwerów fizycznych należy ręcznie skonfigurować maszynę serwera konfiguracji.

**Składnik** | **Wymagania**
--- |---
Rdzenie procesora CPU | 8
Pamięć RAM | 16 GB
Liczba dysków | 3 dyski<br/><br/> Dyski obejmują dysk systemu operacyjnego, dysk pamięci podręcznej serwera przetwarzania i dysk przechowywania na potrzeby powrotu po awarii.
Wolne miejsce na dysku | 600 GB miejsca w pamięci podręcznej serwera przetwarzania.
Wolne miejsce na dysku | 600 GB miejsca na dysk przechowywania.
System operacyjny  | Windows Server 2012 R2 lub Windows Server 2016 z funkcją środowisko pulpitu <br/><br> Jeśli planujesz użycie wbudowanego głównego serwera docelowego tego urządzenia do powrotu po awarii, upewnij się, że wersja systemu operacyjnego jest taka sama lub wyższa niż zreplikowane elementy.|
Ustawienia regionalne systemu operacyjnego | Angielski (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Niewymagane w przypadku serwera konfiguracji w wersji [9,14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) lub nowszej. 
Role systemu Windows Server | Nie włączaj Active Directory Domain Services; Internet Information Services (IIS) lub Hyper-V. 
Zasady grupy| -Zapobiegaj dostępowi do wiersza polecenia. <br/> — Uniemożliwia dostęp do narzędzi do edytowania rejestru. <br/> — Logika zaufania dla plików załączników. <br/> — Włącz wykonywanie skryptu. <br/> - [Dowiedz się więcej](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Upewnij się, że:<br/><br/> — Nie masz wstępnie istniejącej domyślnej witryny sieci Web <br/> -Włącz [uwierzytelnianie anonimowe](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> -Włącz ustawienie [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br/> -Nie masz wstępnie istniejącej witryny sieci Web/aplikacja nasłuchujący na porcie 443<br/>
Typ karty sieciowej | VMXNET3 (po wdrożeniu jako maszyny wirtualnej VMware)
Typ adresu IP | Statyczny
Porty | 443 używane na potrzeby aranżacji kanału kontroli<br/>9443 do transportu danych

## <a name="replicated-machines"></a>Zreplikowane maszyny

Site Recovery obsługuje replikację wszystkich obciążeń uruchomionych na obsługiwanej maszynie.

> [!Note]
> W poniższej tabeli przedstawiono obsługę maszyn z rozruchem systemu BIOS. Zapoznaj się z sekcją [Magazyn](#storage) , aby uzyskać pomoc techniczną dotyczącą komputerów z interfejsem UEFI.

**Składnik** | **Szczegóły**
--- | ---
Ustawienia maszyny | Maszyny, które są replikowane na platformę Azure, muszą spełniać [wymagania dotyczące platformy Azure](#azure-vm-requirements).
Obciążenie maszyny | Site Recovery obsługuje replikację wszystkich obciążeń uruchomionych na obsługiwanej maszynie. [Dowiedz się więcej](https://aka.ms/asr_workload).
Windows Server 2019 | Obsługiwane przez [pakiet zbiorczy aktualizacji 34](https://support.microsoft.com/help/4490016) (wersja 9,22 usługi mobilności) lub nowszy.
Windows Server 2016 64 — bit | Obsługiwane dla serwera Server Core, serwera z funkcją środowisko pulpitu.
Windows Server 2012 R2/Windows Server 2012 | Obsługiwane.
System Windows Server 2008 R2 z dodatkiem SP1 lub nowszym. | Obsługiwane.<br/><br/> W wersji [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) agenta usługi mobilności wymagana jest obsługa aktualizacji [stosu (SSU)](https://support.microsoft.com/help/4490628) i [aktualizacji SHA-2](https://support.microsoft.com/help/4474419) zainstalowanych na maszynach z systemem Windows 2008 R2 z dodatkiem SP1 lub nowszym. Algorytm SHA-1 nie jest obsługiwany z września 2019 i jeśli podpisywanie kodu SHA-2 nie jest włączone, rozszerzenie agenta nie zostanie zainstalowane/uaktualnione zgodnie z oczekiwaniami. Dowiedz się więcej o [uaktualnieniu i wymaganiach algorytmu SHA-2](https://aka.ms/SHA-2KB).
Windows Server 2008 z dodatkiem SP2 lub nowszym (64-bitowy/32-bitowy) |  Obsługiwane tylko w przypadku migracji. [Dowiedz się więcej](migrate-tutorial-windows-server-2008.md).<br/><br/> W wersji [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) agenta usługi mobilności wymagana jest obsługa aktualizacji [stosu (SSU)](https://support.microsoft.com/help/4493730) i [aktualizacji SHA-2](h https://support.microsoft.com/help/4474419) zainstalowanych na komputerach z systemem Windows 2008 SP2. ISHA-1 nie jest obsługiwane z września 2019 i jeśli podpisywanie kodu SHA-2 nie jest włączone, rozszerzenie agenta nie zostanie zainstalowane/uaktualnione zgodnie z oczekiwaniami. Dowiedz się więcej o [uaktualnieniu i wymaganiach algorytmu SHA-2](https://aka.ms/SHA-2KB).
Windows 10, Windows 8.1, Windows 8 | Obsługiwane.
Windows 7 z dodatkiem SP1 64 — bit | Obsługiwane przez [pakiet zbiorczy aktualizacji 36](https://support.microsoft.com/help/4503156) (wersja 9,22 usługi mobilności) lub nowszy. </br></br> Od [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) agenta usługi mobilności potrzebna jest obsługa aktualizacji [stosu (SSU)](https://support.microsoft.com/help/4490628) i [aktualizacji SHA-2](https://support.microsoft.com/help/4474419) zainstalowanych na komputerach z systemem Windows 7 z dodatkiem SP1.  Algorytm SHA-1 nie jest obsługiwany z września 2019 i jeśli podpisywanie kodu SHA-2 nie jest włączone, rozszerzenie agenta nie zostanie zainstalowane/uaktualnione zgodnie z oczekiwaniami. Dowiedz się więcej o [uaktualnieniu i wymaganiach algorytmu SHA-2](https://aka.ms/SHA-2KB).
Linux | Obsługiwany jest tylko system 64-bitowy. 32 — system bitowy nie jest obsługiwany.<br/><br/>Każdy serwer z systemem Linux powinien mieć zainstalowane [składniki systemu Linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) . Jest to wymagane do uruchomienia serwera na platformie Azure po przetestowaniu trybu failover/przejścia w tryb failover. Jeśli brakuje składników usługi LIS, należy zainstalować [składniki](https://www.microsoft.com/download/details.aspx?id=55106) przed włączeniem replikacji dla maszyn na platformie Azure. <br/><br/> Site Recovery organizuje przejście w tryb failover na potrzeby uruchamiania serwerów z systemem Linux na platformie Azure. Jednak dostawcy systemu Linux mogą ograniczyć obsługę tylko wersji dystrybucji, które nie dotarły do końca cyklu życia.<br/><br/> W przypadku dystrybucji systemu Linux obsługiwane są tylko jądra, które są częścią wersji pomocniczej dystrybucji/aktualizacji.<br/><br/> Uaktualnianie chronionych maszyn w ramach głównych wersji dystrybucji systemu Linux nie jest obsługiwane. Aby przeprowadzić uaktualnienie, wyłącz replikację, Uaktualnij system operacyjny, a następnie ponownie Włącz replikację.<br/><br/> [Dowiedz się więcej](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) o pomocy technicznej dla systemów Linux i technologii open source na platformie Azure.
Linux Red Hat Enterprise | 5,2 do 5,11</b><br/> 6,1 do 6,10</b> </br> 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) <br/> Serwery z systemem Red Hat Enterprise Linux 5.2 — 5.11 & 6.1-6.10 nie mają wstępnie zainstalowanych [składników systemu Linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) . Przed włączeniem replikacji maszyn na platformie Azure upewnij się, że zostały zainstalowane [składniki](https://www.microsoft.com/download/details.aspx?id=55106) .
Linux: CentOS | 5,2 do 5,11</b><br/> 6,1 do 6,10</b><br/> 7,0 do 7,6<br/> <br/> Serwery z systemem CentOS 5.2-5.11 & 6.1-6.10 nie mają wstępnie zainstalowanych [składników usług integracji systemu Linux (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) . Przed włączeniem replikacji maszyn na platformie Azure upewnij się, że zostały zainstalowane [składniki](https://www.microsoft.com/download/details.aspx?id=55106) .
Ubuntu | Serwer Ubuntu 14,04 LTS [(Przejrzyj obsługiwane wersje jądra)](#ubuntu-kernel-versions)<br/><br/>Serwer Ubuntu 16,04 LTS [(Przejrzyj obsługiwane wersje jądra)](#ubuntu-kernel-versions) </br> Serwer Ubuntu 18,04 LTS [(Przejrzyj obsługiwane wersje jądra)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(Przejrzyj obsługiwane wersje jądra)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 z dodatkiem SP1, SP2, SP3 i SP4 [(Przejrzyj obsługiwane wersje jądra)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> Uaktualnianie zreplikowanych maszyn z programu SUSE Linux Enterprise Server 11 z dodatkiem SP3 do wersji SP4 nie jest obsługiwane. Aby przeprowadzić uaktualnienie, wyłącz replikację i włącz ją ponownie po uaktualnieniu.
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)<br/><br/> Uruchamianie jądra zgodnego z systemem Red Hat lub nieprzerwane wydanie jądra 3, 4 & 5 (UEK3, UEK4, UEK5) 

> [!Note]
> Dla każdej wersji systemu Windows Azure Site Recovery obsługuje tylko kompilacje [LTSC (Long-Term Servicing Channel)](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) .  W tej chwili nie są obecnie obsługiwane [półroczne wersje kanałów](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) .

### <a name="ubuntu-kernel-versions"></a>Wersje jądra Ubuntu

**Obsługiwana wersja** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
14,04 LTS | [9,31][9.31 UR] | 3.13.0-24-ogólny do 3.13.0-170-Generic,<br/>3.16.0-25-ogólny do 3.16.0-77-Generic,<br/>3.19.0-18-Generic do 3.19.0-80-Generic,<br/>4.2.0-18-Generic do 4.2.0-42-Generic,<br/>4.4.0-21-Generic to 4.4.0-148-Generic,<br/>4.15.0-1023-Azure do 4.15.0-1045 — Azure |
14,04 LTS | [9,30][9.30 UR] | 3.13.0-24-ogólny do 3.13.0-170-Generic,<br/>3.16.0-25-ogólny do 3.16.0-77-Generic,<br/>3.19.0-18-Generic do 3.19.0-80-Generic,<br/>4.2.0-18-Generic do 4.2.0-42-Generic,<br/>4.4.0-21-Generic to 4.4.0-148-Generic,<br/>4.15.0-1023-Azure do 4.15.0-1045 — Azure |
14,04 LTS | [9,29][9.29 UR]| 3.13.0-24-ogólny do 3.13.0-170-Generic,<br/>3.16.0-25-ogólny do 3.16.0-77-Generic,<br/>3.19.0-18-Generic do 3.19.0-80-Generic,<br/>4.2.0-18-Generic do 4.2.0-42-Generic,<br/>4.4.0-21-Generic to 4.4.0-148-Generic,<br/>4.15.0-1023-Azure do 4.15.0-1045 — Azure |
14,04 LTS | [9,28][9.28 UR]| 3.13.0-24-ogólny do 3.13.0-170-Generic,<br/>3.16.0-25-ogólny do 3.16.0-77-Generic,<br/>3.19.0-18-Generic do 3.19.0-80-Generic,<br/>4.2.0-18-Generic do 4.2.0-42-Generic,<br/>4.4.0-21-Generic to 4.4.0-148-Generic,<br/>4.15.0-1023-Azure do 4.15.0-1045 — Azure |
|||
16,04 LTS | [9,31][9.31 UR] | 4.4.0-21-Generic to 4.4.0-170-Generic,<br/>4.8.0-34-Generic do 4.8.0-58-Generic,<br/>4.10.0-14-Generic to 4.10.0-42-Generic,<br/>4.11.0-13-Generic do 4.11.0-14-Generic,<br/>4.13.0-16-ogólny do 4.13.0-45-Generic,<br/>4.15.0-13-Generic to 4.15.0-72-Generic<br/>4.11.0-1009-Azure to 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure to 4.13.0-1018-Azure <br/>4.15.0-1012-Azure to 4.15.0-1063-Azure|
16,04 LTS | [9,30][9.30 UR] | 4.4.0-21-Generic to 4.4.0-166-Generic,<br/>4.8.0-34-Generic do 4.8.0-58-Generic,<br/>4.10.0-14-Generic to 4.10.0-42-Generic,<br/>4.11.0-13-Generic do 4.11.0-14-Generic,<br/>4.13.0-16-ogólny do 4.13.0-45-Generic,<br/>4.15.0-13-Generic to 4.15.0-66-Generic<br/>4.11.0-1009-Azure to 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure to 4.13.0-1018-Azure <br/>4.15.0-1012-Azure to 4.15.0-1061-Azure|
16,04 LTS | [9,29][9.29 UR] | 4.4.0-21-Generic to 4.4.0-164-Generic,<br/>4.8.0-34-Generic do 4.8.0-58-Generic,<br/>4.10.0-14-Generic to 4.10.0-42-Generic,<br/>4.11.0-13-Generic do 4.11.0-14-Generic,<br/>4.13.0-16-ogólny do 4.13.0-45-Generic,<br/>4.15.0-13-Generic do 4.15.0-64-Generic<br/>4.11.0-1009-Azure to 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure to 4.13.0-1018-Azure <br/>4.15.0-1012-Azure to 4.15.0-1059-Azure|
16,04 LTS | [9,28][9.28 UR] | 4.4.0-21-Generic to 4.4.0-159-Generic,<br/>4.8.0-34-Generic do 4.8.0-58-Generic,<br/>4.10.0-14-Generic to 4.10.0-42-Generic,<br/>4.11.0-13-Generic do 4.11.0-14-Generic,<br/>4.13.0-16-ogólny do 4.13.0-45-Generic,<br/>4.15.0-13-Generic do 4.15.0-58-Generic<br/>4.11.0-1009-Azure to 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure to 4.13.0-1018-Azure <br/>4.15.0-1012-Azure to 4.15.0-1055-Azure|
|||
18,04 LTS | [9,31][9.31 UR]| 4.15.0-20-Generic to 4.15.0-72-Generic </br> 4.18.0-13-Generic to 4.18.0-25-Generic </br> 5.0.0-15-ogólny do 5.0.0-37-Generic </br> 5.3.0-19-Generic do 5.3.0-24-Generic </br> 4.15.0-1009-Azure to 4.15.0-1037-Azure </br> 4.18.0-1006-Azure do 4.18.0-1025-Azure </br> 5.0.0-1012-Azure to 5.0.0-1025-Azure </br> 5.3.0 — 1007 — Azure|
18,04 LTS | [9,30][9.30 UR] | 4.15.0-20-ogólny do 4.15.0-66-Generic </br> 4.18.0-13-Generic to 4.18.0-25-Generic </br> 5.0.0-15-ogólny do 5.0.0-32-generyczny </br> 4.15.0-1009-Azure to 4.15.0-1037-Azure </br> 4.18.0-1006-Azure do 4.18.0-1025-Azure </br> 5.0.0-1012-Azure to 5.0.0-1023-Azure|
18,04 LTS | [9,29][9.29 UR] | 4.15.0-20-ogólny do 4.15.0-62-Generic </br> 4.18.0-13-Generic to 4.18.0-25-Generic </br> 5.0.0-15-ogólny do 5.0.0-27-Generic </br> 4.15.0-1009-Azure to 4.15.0-1037-Azure </br> 4.18.0-1006-Azure do 4.18.0-1025-Azure </br> 5.0.0-1012-Azure to 5.0.0-1018-Azure|

### <a name="debian-kernel-versions"></a>Wersje jądra Debian


**Obsługiwana wersja** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
Debian 7 | [9,28][9.28 UR], [9,29][9.29 UR], [9,30][9.30 UR], [9,31][9.31 UR]| 3.2.0-4-amd64 do 3.2.0-6-amd64, 3.16.0 -0. BPO. 4-amd64 |
|||
Debian 8 | [9,30][9.30 UR], [9,31][9.31 UR] | 3.16.0-4-amd64 do 3.16.0-10-amd64, 4.9.0 -0. BPO. 4-amd64 do 4.9.0 -0. BPO. 11-amd64 |
Debian 8 | [9,28][9.28 UR], [9,29][9.29 UR] | 3.16.0-4-amd64 do 3.16.0-10-amd64, 4.9.0 -0. BPO. 4-amd64 do 4.9.0 -0. BPO. 9-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 obsługiwanych wersji jądra

**Wydanie** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,31][9.31 UR] | Obsługiwane są wszystkie [Zapasy SUSE 12 SP1, SP2, SP3 i SP4](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) .</br></br> 4.4.138-4,7-Azure do 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure do 4.12.14-6.29 — Azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,30][9.30 UR] | Obsługiwane są wszystkie [Zapasy SUSE 12 SP1, SP2, SP3 i SP4](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) .</br></br> 4.4.138-4,7-Azure do 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure do 4.12.14-6.26 — Azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,29][9.29 UR] | Obsługiwane są wszystkie [Zapasy SUSE 12 SP1, SP2, SP3 i SP4](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) .</br></br> 4.4.138-4,7-Azure do 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure do 4.12.14-6.23 — Azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,28][9.28 UR] | SP1 3.12.49-11 — domyślnie 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45 — domyślnie 3.12.74-60.64.118-default</br></br> SP2 4.4.21-69-domyślnie 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73 — domyślnie 4.4.121-92.117-default</br></br>SP3 4.4.73-5 — domyślnie 4.4.180-94.100-default</br></br>SP3 4.4.138-4.7-Azure to 4.4.180-4.31-Azure</br></br>SP4 4.12.14-94.41-domyślnie 4.12.14-95.29-default</br>SP4 4.12.14-6.3-Azure to 4.12.14-6.23-Azure |


## <a name="linux-file-systemsguest-storage"></a>Systemy plików Linux/magazyn gościa

**Składnik** | **Obsługiwane**
--- | ---
Systemy plików | ext3, EXT4, XFS
Menedżer woluminów | -LVM jest obsługiwana.<br/> -/Boot on LVM jest obsługiwany z [pakietu zbiorczego aktualizacji 31](https://support.microsoft.com/help/4478871/) (wersja 9,20 usługi mobilności) lub nowszej. Nie jest to obsługiwane we wcześniejszych wersjach usługi mobilności.<br/> — Wiele dysków systemu operacyjnego nie jest obsługiwanych.
Parawirtualne urządzeń magazynujących | Urządzenia eksportowane przez sterowniki parawirtualne nie są obsługiwane.
Wiele kolejek blokowych urządzeń we/wy | Bez pomocy technicznej.
Serwery fizyczne z kontrolerem magazynu HP CCISS | Bez pomocy technicznej.
Konwencja nazewnictwa urządzeń/punktów instalacji | Nazwa urządzenia lub nazwa punktu instalacji powinna być unikatowa.<br/> Upewnij się, że żadne dwa urządzenia/punkty instalacji nie mają nazw z rozróżnianiem wielkości liter. Na przykład nazewnictwo urządzeń dla tej samej maszyny wirtualnej jako *device1* i *device1* nie jest obsługiwane.
Katalogi | W przypadku korzystania z wersji usługi mobilności wcześniejszej niż wersja 9,20 (wydana w [pakiecie zbiorczym aktualizacji 31](https://support.microsoft.com/help/4478871/)) obowiązują następujące ograniczenia:<br/><br/> — Te katalogi (jeśli zostały skonfigurowane jako osobne partycje/systemy plików) muszą znajdować się na tym samym dysku systemu operacyjnego na serwerze źródłowym:/(root),/Boot,/usr,/usr/local,/var,/etc.</br> -Katalog/Boot powinien znajdować się na partycji dysku, a nie na woluminie LVM.<br/><br/> Te ograniczenia nie mają zastosowania w wersji 9,20 lub nowszej. 
Katalog rozruchowy | -Dyski rozruchowe nie może być w formacie partycji GPT. Jest to ograniczenie architektury platformy Azure. Dyski GPT są obsługiwane jako dyski danych.<br/><br/> Wiele dysków rozruchowych na maszynie wirtualnej nie jest obsługiwanych<br/><br/> -/Boot na woluminie LVM na więcej niż jeden dysk nie jest obsługiwany.<br/> -Nie można zreplikować maszyny bez dysku rozruchowego.
Wymagania dotyczące wolnego miejsca| 2 GB na partycji/root <br/><br/> 250 MB w folderze instalacyjnym
XFSv5 | Obsługiwane są funkcje XFSv5 w systemach plików XFS, takie jak suma kontrolna metadanych (usługa mobilności w wersji 9,10 lub nowszej).<br/> Użyj narzędzia xfs_info, aby sprawdzić blok XFS dla partycji. Jeśli `ftype` jest ustawiona na 1, funkcje XFSv5 są używane.
BTRFS | BTRFS jest obsługiwana z [pakietu zbiorczego aktualizacji 34](https://support.microsoft.com/help/4490016) (wersja 9,22 usługi mobilności) lub nowszej. BTRFS nie jest obsługiwane, jeśli:<br/><br/> -Wolumin systemu plików BTRFS jest zmieniany po włączeniu ochrony.</br> — System plików BTRFS jest rozłożony na wiele dysków.</br> — System plików BTRFS obsługuje macierz RAID.

## <a name="vmdisk-management"></a>Zarządzanie MASZYNami wirtualnymi/dyskami

**Akcja** | **Szczegóły**
--- | ---
Zmień rozmiar dysku na zreplikowanej maszynie wirtualnej | Obsługiwane przez źródłową maszynę wirtualną przed przełączeniem w tryb failover bezpośrednio we właściwościach maszyny wirtualnej. Nie trzeba wyłączać/ponownie włączać replikacji.<br/><br/> Zmiana źródłowej maszyny wirtualnej po przejściu do trybu failover powoduje, że zmiany nie będą przechwytywane.<br/><br/> W przypadku zmiany rozmiaru dysku maszyny wirtualnej platformy Azure po przejściu w tryb failover program Site Recovery tworzy nową maszynę wirtualną z aktualizacjami.
Dodawanie dysku do zreplikowanej maszyny wirtualnej | Bez pomocy technicznej.<br/> Wyłącz replikację dla maszyny wirtualnej, Dodaj dysk, a następnie ponownie Włącz replikację.

## <a name="network"></a>Network (Sieć)

**Składnik** | **Obsługiwane**
--- | ---
Tworzenie zespołu kart interfejsu sieciowego hosta | Obsługiwane przez maszyny wirtualne VMware. <br/><br/>Nieobsługiwane w przypadku replikacji maszyn fizycznych.
Sieć VLAN hosta | Tak.
Adres IPv4 sieci hosta | Tak.
Adres IPv6 sieci hosta | Nie.
Tworzenie zespołu kart interfejsu sieciowego gościa/serwera | Nie.
Adres IPv4 sieci gościa/serwera | Tak.
Adres IPv6 sieci gościa/serwera | Nie.
Statyczny adres IP gościa/serwera (system Windows) | Tak.
Statyczny adres IP gościa/serwera (Linux) | Tak. <br/><br/>Maszyny wirtualne są skonfigurowane do korzystania z protokołu DHCP podczas powrotu po awarii.
Sieć gościa/serwer z wieloma kartami sieciowymi | Tak.


## <a name="azure-vm-network-after-failover"></a>Sieć maszyn wirtualnych platformy Azure (po przejściu w tryb failover)

**Składnik** | **Obsługiwane**
--- | ---
Usługa ExpressRoute systemu Azure | Tak
ILB | Tak
ELB | Tak
Azure Traffic Manager | Tak
Multi-NIC | Tak
Zastrzeżony adres IP | Tak
IPv4 | Tak
Zachowaj źródłowy adres IP | Tak
Punkty końcowe usługi dla sieci wirtualnej platformy Azure<br/> | Tak
Wydajniejsze sieci | Nie

## <a name="storage"></a>Usługa Storage
**Składnik** | **Obsługiwane**
--- | ---
Dysk dynamiczny | Dysk systemu operacyjnego musi być dyskiem podstawowym. <br/><br/>Dyski danych mogą być dyskami dynamicznymi
Konfiguracja dysku platformy Docker | Nie
System plików NFS hosta | Tak dla oprogramowania VMware<br/><br/> Nie dla serwerów fizycznych
Sieć SAN hosta (iSCSI/FC) | Tak
Host vSAN | Tak dla oprogramowania VMware<br/><br/> N/A dla serwerów fizycznych
Wielościeżkowa hosta (MPIO) | Tak, przetestowano przy użyciu modułu Microsoft DSM, EMC PowerPath 5,7 SP4, EMC PowerPath DSM dla CLARiiON
Woluminy wirtualne hosta (VVols) | Tak dla oprogramowania VMware<br/><br/> N/A dla serwerów fizycznych
Gość/serwer VMDK | Tak
Udostępniony dysk klastra gościa/serwera | Nie
Dysk szyfrowany gościa/serwera | Nie
System plików NFS gościa/serwera | Nie
Interfejs iSCSI gościa/serwera | Dla migracji — tak<br/>W przypadku odzyskiwania po awarii — nie, iSCSI spowoduje powrót po awarii jako dołączony dysk do maszyny wirtualnej
Gość/serwer SMB 3,0 | Nie
RDM gościa/serwera | Tak<br/><br/> N/A dla serwerów fizycznych
Dysk gościa/serwer > 1 TB | Tak, dysk musi być większy niż 1024 MB<br/><br/>Do 8 192 GB podczas replikowania na dyski zarządzane (wersja 9,26)<br></br> Do 4 095 GB podczas replikowania na konta magazynu
Dysk gościa/serwer z rozmiarem sektora fizycznego i 4 KB w kilobajtach | Nie
Dysk gościa/serwer z logicznymi i 512mi 4K rozmiar sektora fizycznego | Nie
Wolumin gościa/serwer z dyskiem rozłożonym > 4 TB <br/><br/>Zarządzanie woluminami logicznymi (LVM)| Tak
Gość/serwer — miejsca do magazynowania | Nie
Gość/serwer gorąca Dodaj/Usuń dysk | Nie
Gość/serwer — wykluczanie dysku | Tak
Wielościeżkowa gość/serwer (MPIO) | Nie
Partycje typu GPT/serwer | Z [pakietem zbiorczym aktualizacji 37](https://support.microsoft.com/help/4508614/) są obsługiwane pięć partycji (wersja 9,25 usługi mobilności). Poprzednia wersja obsługiwała cztery.
ReFS | System plików jest odporny na błędy w wersji 9,23 lub nowszej
Gość/serwer EFI/rozruch UEFI | -Obsługiwane dla systemu Windows Server 2012 lub nowszego, SLES 12 SP4 i RHEL 8,0 z agentem mobilności w wersji 9,30 lub nowszej<br/> -Bezpieczny typ rozruchu UEFI nie jest obsługiwany. 

## <a name="replication-channels"></a>Kanały replikacji

|**Typ replikacji**   |**Obsługiwane**  |
|---------|---------|
|Odciążone transfery danych (ODX)    |       Nie  |
|Rozmieszczanie w trybie offline        |   Nie      |
| Urządzenie Azure Data Box | Nie

## <a name="azure-storage"></a>Azure Storage

**Składnik** | **Obsługiwane**
--- | ---
Magazyn lokalnie nadmiarowy | Tak
Magazyn geograficznie nadmiarowy | Tak
Magazyn geograficznie nadmiarowy dostępny do odczytu | Tak
Chłodny magazyn | Nie
Magazyn gorąca| Nie
Obiekty BLOB typu Block | Nie
Szyfrowanie — w spoczynku (SSE)| Tak
Szyfrowanie na poziomie spoczynku (CMK)| Tak (za pośrednictwem programu PowerShell AZ 3.3.0 module lub nowszym)
Premium Storage | Tak
Usługa importu/eksportu | Nie
Zapory usługi Azure Storage dla sieci wirtualnych | Tak.<br/> Skonfigurowane na docelowym koncie magazynu/pamięci podręcznej (używane do przechowywania danych replikacji).
Konta magazynu ogólnego przeznaczenia w wersji 2 (warstwy gorąca i chłodna) | Tak (koszt transakcji jest znacznie wyższy dla wersji 2 w porównaniu do v1)

## <a name="azure-compute"></a>Obliczenia na platformie Azure

**Funkcja** | **Obsługiwane**
--- | ---
Zbiory dostępności | Tak
Strefy dostępności | Nie
HUB | Tak
Dyski zarządzane | Tak

## <a name="azure-vm-requirements"></a>Wymagania dotyczące maszyny wirtualnej platformy Azure

Lokalne maszyny wirtualne replikowane na platformę Azure muszą spełniać wymagania dotyczące maszyny wirtualnej platformy Azure podsumowane w tej tabeli. Gdy Site Recovery uruchamia sprawdzanie wymagań wstępnych dotyczących replikacji, sprawdzenie zakończy się niepowodzeniem, jeśli niektóre wymagania nie zostaną spełnione.

**Składnik** | **Wymagania** | **Szczegóły**
--- | --- | ---
System operacyjny gościa | Sprawdź [obsługiwane systemy operacyjne](#replicated-machines) dla replikowanych maszyn. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Architektura systemu operacyjnego gościa | 64-bitowa. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Rozmiar dysku systemu operacyjnego | Do 2 048 GB. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Liczba dysków systemu operacyjnego | 1 | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Liczba dysków danych | 64 lub mniej. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Rozmiar dysku danych | Do 8 192 GB podczas replikowania na dysk zarządzany (wersja 9,26)<br></br>Do 4 095 GB podczas replikowania na konto magazynu| Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Karty sieciowe | Obsługiwane są wiele kart. |
Udostępniony wirtualny dysk twardy | Bez pomocy technicznej. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Dysk FC | Bez pomocy technicznej. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
BitLocker | Bez pomocy technicznej. | Aby włączyć replikację dla maszyny, należy wyłączyć funkcję BitLocker. |
Nazwa maszyny wirtualnej | Od 1 do 63 znaków.<br/><br/> Ograniczone do liter, cyfr i łączników.<br/><br/> Nazwa maszyny musi rozpoczynać się i kończyć literą lub cyfrą. |  Zaktualizuj wartość we właściwościach komputera w Site Recovery.

## <a name="resource-group-limits"></a>Limity grupy zasobów

Aby zrozumieć liczbę maszyn wirtualnych, które mogą być chronione w ramach jednej grupy zasobów, zapoznaj się z artykułem dotyczącym [limitów subskrypcji i przydziałów](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#resource-group-limits)

## <a name="churn-limits"></a>Limity zmian

W poniższej tabeli przedstawiono limity usługi Azure Site Recovery. 
- Limity te są oparte na naszych testach, ale nie obejmują wszystkich możliwych kombinacji operacji we/wy aplikacji.
- Rzeczywiste wyniki mogą różnić w zależności od kombinacji operacji we/wy aplikacji.
- Aby uzyskać najlepsze wyniki, zdecydowanie zalecamy uruchomienie [narzędzia planista wdrażania](site-recovery-deployment-planner.md)i przeprowadzenie testowania aplikacji przy użyciu testu pracy w trybie failover w celu uzyskania prawdziwego obrazu wydajności dla aplikacji.

**Lokalizacja docelowa replikacji** | **Średni rozmiar źródłowych operacji we/wy na dysku** |**Średni źródłowy współczynnik zmian danych na dysku** | **Łączny współczynnik zmian danych na dysku dziennie**
---|---|---|---
Standard Storage | 8 KB | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 8 KB  | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 16 KB | 4 MB/s |  336 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 32 KB lub większy | 8 MB/s | 672 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 8 KB    | 5 MB/s | 421 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 16 KB lub większy |20 MB/s | 1684 GB na dysk


**Źródłowy współczynnik zmian danych** | **Limit maksymalny**
---|---
Szczytowy współczynnik zmian danych na wszystkich dyskach na maszynie wirtualnej | 54 MB/s
Maksymalny współczynnik zmian danych dziennie obsługiwany przez serwer przetwarzania | 2 TB

- Są to średnie wartości przy założeniu 30-procentowego nakładania się operacji we/wy.
- Usługa Site Recovery może obsługiwać większą przepływność na podstawie zakresu nakładania się na siebie, większego rozmiaru operacji zapisu i rzeczywistego zachowania związanego z obciążeniem operacji we/wy.
- Te liczby zakładają typowy zaległości około pięciu minut. Oznacza to, że przekazane dane są przetwarzane i punkt odzyskiwania jest tworzony w ciągu pięciu minut.

## <a name="vault-tasks"></a>Zadania magazynu

**Akcja** | **Obsługiwane**
--- | ---
Przenoszenie magazynu między grupami zasobów | Nie
Przenoszenie magazynu w ramach subskrypcji i między subskrypcjami | Nie
Przenoszenie magazynu, sieci, maszyn wirtualnych platformy Azure między grupami zasobów | Nie
Przenoszenie magazynu, sieci, maszyn wirtualnych platformy Azure w ramach i między subskrypcjami. | Nie


## <a name="obtain-latest-components"></a>Uzyskaj najnowsze składniki

**Nazwa** | **Opis** | **Szczegóły**
--- | --- | ---
Serwer konfiguracji | Zainstalowany lokalnie.<br/> Koordynuje komunikację między lokalnymi serwerami VMware lub maszynami fizycznymi i platformą Azure. | - informacje [o](vmware-physical-azure-config-process-server-overview.md) serwerze konfiguracji.<br/> - [dowiedzieć się więcej o](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) uaktualnianiu do najnowszej wersji.<br/> - [dowiedzieć się więcej o](vmware-azure-deploy-configuration-server.md) konfigurowaniu serwera konfiguracji. 
Serwer przetwarzania | Domyślnie instalowany na serwerze konfiguracji.<br/> Odbiera dane replikacji, optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do platformy Azure.<br/> Wraz z rozwojem wdrożenia można dodać kolejne serwery przetwarzania do obsługi większych woluminów ruchu związanego z replikacją. | - informacje [o](vmware-physical-azure-config-process-server-overview.md) serwerze przetwarzania.<br/> - [dowiedzieć się więcej o](vmware-azure-manage-process-server.md#upgrade-a-process-server) uaktualnianiu do najnowszej wersji.<br/> - [dowiedzieć się więcej o](vmware-physical-large-deployment.md#set-up-a-process-server) konfigurowaniu serwerów przetwarzania skalowalnego w poziomie.
Usługa Mobility Service | Zainstalowane na maszynie wirtualnej VMware lub serwerach fizycznych, które chcesz replikować.<br/> Koordynuje replikację między lokalnymi serwerami VMware/serwerami fizycznymi i platformą Azure.| - [Dowiedz się więcej o](vmware-physical-mobility-service-overview.md) usłudze mobilności.<br/> - [dowiedzieć się więcej o](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) uaktualnianiu do najnowszej wersji.<br/> 



## <a name="next-steps"></a>Następne kroki
[Dowiedz się, jak](tutorial-prepare-azure.md) przygotować platformę Azure do odzyskiwania po awarii maszyn wirtualnych VMware.


[9.31 UR]: https://support.microsoft.com/en-in/help/4531426/update-rollup-42-for-azure-site-recovery
[9.30 UR]: https://support.microsoft.com/en-in/help/4531426/update-rollup-42-for-azure-site-recovery
[9.29 UR]: https://support.microsoft.com/en-in/help/4528026/update-rollup-41-for-azure-site-recovery
[9.28 UR]: https://support.microsoft.com/en-in/help/4521530/update-rollup-40-for-azure-site-recovery
[9.27 UR]: https://support.microsoft.com/en-in/help/4517283/update-rollup-39-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
