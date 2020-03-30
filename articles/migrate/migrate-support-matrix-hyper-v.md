---
title: Obsługa oceny funkcji Hyper-V w programie Azure Migrate
description: Dowiedz się więcej o obsłudze oceny funkcji Hyper-V za pomocą oceny serwera migracji usługi Azure
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 61d8e635a32024ba5afabb34fefa5bf169e2911f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336912"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Macierz wsparcia dla oceny funkcji Hyper-V

W tym artykule podsumowano wymagania wstępne i wymagania dotyczące pomocy technicznej dotyczące oceny maszyn wirtualnych funkcji Hyper-V w ramach przygotowań do migracji na platformę Azure. Jeśli chcesz przeprowadzić migrację maszyn wirtualnych z programem Hyper-V na platformę Azure, przejrzyj [macierz pomocy technicznej migracji](migrate-support-matrix-hyper-v-migration.md).

Maszyny wirtualne funkcji Hyper-V można ocenić za pomocą narzędzia [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Utwórz projekt migracji platformy Azure, a następnie dodaj narzędzie do projektu. Po dodaniu narzędzia można wdrożyć [urządzenie migracji platformy Azure](migrate-appliance.md). Urządzenie stale odnajduje maszyny lokalne i wysyła metadane maszyny i dane o wydajności na platformę Azure. Po odnajdowaniu maszyn można zebrać odnalezione maszyny do grup i uruchomić ocenę dla grupy.


## <a name="limitations"></a>Ograniczenia

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Limity oceny** | W jednym [projekcie migracji platformy Azure](migrate-support-matrix.md#azure-migrate-projects)można odkryć i ocenić do 35 000 maszyn wirtualnych funkcji Hyper V.
**Limity projektów** | Można utworzyć wiele projektów w ramach subskrypcji platformy Azure. Oprócz maszyn wirtualnych funkcji Hyper-V projekt może zawierać maszyny wirtualne VMware i serwery fizyczne, do limitów oceny dla każdego z nich.
**Odnajdywania** | Urządzenie migracji platformy Azure może odnajdować do 5000 maszyn wirtualnych funkcji Hyper V.<br/><br/> Urządzenie może łączyć się z maksymalnie 300 hostami funkcji Hyper-V.
**Ocena** | W jednej grupie można dodać do 35 000 maszyn.<br/><br/> Można ocenić do 35 000 maszyn wirtualnych w jednej ocenie dla grupy.

[Dowiedz się więcej](concepts-assessment-calculation.md) o ocenach.



## <a name="hyper-v-host-requirements"></a>Wymagania dotyczące hosta funkcji Hyper-V

| **Pomoc techniczna**                | **Szczegóły**               
| :-------------------       | :------------------- |
| **Host funkcji Hyper-V**       | Host funkcji Hyper-V może być autonomiczny lub wdrożony w klastrze.<br/><br/> Host funkcji Hyper-V może działać w systemach Windows Server 2019, Windows Server 2016 lub Windows Server 2012 R2.<br/> Nie można ocenić maszyn wirtualnych znajdujących się na hostach funkcji Hyper-V z systemem Windows Server 2012.
| **Uprawnienia**           | Potrzebne są uprawnienia administratora na hoście funkcji Hyper-V. <br/> Jeśli nie chcesz przypisywać uprawnień administratora, utwórz konto użytkownika lokalnego lub domeny i dodaj konto użytkownika do tych grup — użytkownicy zdalnego zarządzania, administratorzy funkcji Hyper-V i użytkownicy monitora wydajności. |
| **Komunikacja zdalna programu PowerShell**   | [Komunikacji zdalnej programu PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/enable-psremoting?view=powershell-7) musi być włączona na każdym hoście funkcji Hyper-V. |
| **Hyper-V Replica**       | Jeśli używasz repliki funkcji Hyper-V (lub masz wiele maszyn wirtualnych z tymi samymi identyfikatorami maszyn wirtualnych) i odkryjesz zarówno oryginalne, jak i replikowane maszyny wirtualne przy użyciu usługi Azure Migrate, ocena generowana przez usługę Azure Migrate może być nieprawidłowa. |


## <a name="hyper-v-vm-requirements"></a>Wymagania dotyczące maszyny wirtualnej funkcji Hyper-V

| **Pomoc techniczna**                  | **Szczegóły**               
| :----------------------------- | :------------------- |
| **System operacyjny** | Wszystkie systemy operacyjne [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) i [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) obsługiwane przez platformę Azure. |
| **Integration Services**       | [Usługi integracji funkcji Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) muszą być uruchomione na ocenianych maszynach wirtualnych w celu przechwytywania informacji o systemie operacyjnym. |


## <a name="azure-migrate-appliance-requirements"></a>Wymagania urządzenia usługi Azure Migrate

Usługa Azure Migrate używa [urządzenia migracji platformy Azure](migrate-appliance.md) do odnajdowania i oceny. Urządzenie można wdrożyć przy użyciu skompresowanego dysku VHd funkcji Hyper-V pobranego z portalu lub skryptu programu PowerShell.

- Dowiedz się więcej o [wymaganiach dotyczących urządzeń](migrate-appliance.md#appliance---hyper-v) dla funkcji Hyper-V.
- Dowiedz się więcej o [adresach URL,](migrate-appliance.md#url-access) do które urządzenie musi uzyskać dostęp.

## <a name="port-access"></a>Dostęp do portu

W poniższej tabeli podsumowano wymagania dotyczące portów do oceny.

**Urządzenie** | **Połączenia**
--- | ---
**Urządzenia** | Połączenia przychodzące na porcie TCP 3389 umożliwiające zdalne połączenia pulpitu z urządzeniem.<br/><br/> Połączenia przychodzące na porcie 44368 w celu zdalnego dostępu do aplikacji do zarządzania urządzeniami przy użyciu adresu URL:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Połączenia wychodzące na portach 443 (HTTPS), aby wysłać metadane odnajdywania i wydajności do usługi Azure Migrate.
**Host/klaster funkcji Hyper-V** | Połączenia przychodzące na portach WinRM 5985 (HTTP) i 5986 (HTTPS), aby pobierać metadane i dane dotyczące wydajności dla maszyn wirtualnych funkcji Hyper-V przy użyciu sesji wspólnego modelu informacyjnego (CIM).

## <a name="agentless-dependency-analysis-requirements"></a>Wymagania dotyczące analizy zależności bez agenta

[Analiza zależności](concepts-dependency-visualization.md) ułatwia identyfikowanie zależności między komputerami lokalnymi, które chcesz ocenić i przeprowadzić migrację na platformę Azure. W tabeli podsumowano wymagania dotyczące konfigurowania analizy zależności bez agenta. 

**Wymaganie** | **Szczegóły** 
--- | --- 
**Przed wdrożeniem** | Powinieneś mieć projekt migracji platformy Azure w miejscu, z narzędzia Azure Migrate: Server Assessment dodane do projektu.<br/><br/>  Wizualizacja zależności jest wdrażana po skonfigurowaniu urządzenia migracji platformy Azure w celu odnajdowania lokalnych komputerów VMWare.<br/><br/> [Dowiedz się, jak](create-manage-projects.md) utworzyć projekt po raz pierwszy.<br/> [Dowiedz się, jak](how-to-assess.md) dodać narzędzie do oceny do istniejącego projektu.<br/> [Dowiedz się, jak](how-to-set-up-appliance-vmware.md) skonfigurować urządzenie migracji platformy Azure do oceny maszyn wirtualnych VMware.
**Obsługa maszyn wirtualnych** | Obecnie obsługiwane tylko dla maszyn wirtualnych VMware.
**Maszyny wirtualne z systemem Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64-bitowy).
**Konto systemu Windows** |  Do analizy zależności urządzenie migracji platformy Azure potrzebuje konta administratora lokalnego lub domeny, aby uzyskać dostęp do maszyn wirtualnych systemu Windows.
**Maszyny wirtualne z systemem Linux** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14.04, 16.04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Konto Linuksa** | Do analizy zależności na komputerach z systemem Linux urządzenie usługi Azure Migrate musi mieć konto użytkownika z uprawnieniami root.<br/><br/> Alternatywnie konto użytkownika potrzebuje tych uprawnień do plików /bin/netstat i /bin/ls: CAP_DAC_READ_SEARCH i CAP_SYS_PTRACE.
**Wymaganych agentów** | Nie jest wymagany agent na komputerach, które chcesz analizować.
**Narzędzia VMware** | Narzędzia VMware Tools (później niż 10.2) muszą być zainstalowane i uruchomione na każdej maszynie wirtualnej, którą chcesz przeanalizować.
**Program vCenter Server** | Wizualizacja zależności wymaga konta serwera vCenter Server z dostępem tylko do odczytu i uprawnieniami włączonymi dla maszyn wirtualnych > operacji gościa. **Hosty ESXi:** na hostach ESXi z uruchomionymi maszynami wirtualnymi, które chcesz przeanalizować, urządzenie migracji platformy Azure musi mieć możliwość nawiązania połączenia z portem TCP 443.

## <a name="agent-based-dependency-analysis-requirements"></a>Wymagania analizy zależności oparte na agentach

[Analiza zależności](concepts-dependency-visualization.md) ułatwia identyfikowanie zależności między komputerami lokalnymi, które chcesz ocenić i przeprowadzić migrację na platformę Azure. W tabeli podsumowano wymagania dotyczące konfigurowania analizy zależności opartej na agentach. 

**Wymaganie** | **Szczegóły** 
--- | --- 
**Przed wdrożeniem** | Powinieneś mieć projekt migracji platformy Azure w miejscu, z narzędzia Azure Migrate: Server Assessment dodane do projektu.<br/><br/>  Wizualizacja zależności jest wdrażana po skonfigurowaniu urządzenia migracji platformy Azure w celu odnajdowania komputerów lokalnych<br/><br/> [Dowiedz się, jak](create-manage-projects.md) utworzyć projekt po raz pierwszy.<br/> [Dowiedz się, jak](how-to-assess.md) dodać narzędzie do oceny do istniejącego projektu.<br/> Dowiedz się, jak skonfigurować urządzenie migracji platformy Azure do oceny serwerów [hyper-V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)lub fizycznych.
**Azure Government** | Wizualizacja zależności nie jest dostępna w usłudze Azure Dla Instytucji.
**Analiza dzienników** | Usługa Azure Migrate używa rozwiązania [mapy usług](../operations-management-suite/operations-management-suite-service-map.md) w [dziennikach usługi Azure Monitor](../log-analytics/log-analytics-overview.md) do wizualizacji zależności.<br/><br/> Nowy lub istniejący obszar roboczy usługi Log Analytics jest skojarzony z projektem migracji platformy Azure. Obszaru roboczego dla projektu migracji platformy Azure nie można zmodyfikować po jego dodaniu. <br/><br/> Obszar roboczy musi być w tej samej subskrypcji co projekt migracji platformy Azure.<br/><br/> Obszar roboczy musi znajdować się w regionach Wschodnich Stanów Zjednoczonych, Azji Południowo-Wschodniej lub Europy Zachodniej. Obszarów roboczych w innych regionach nie można skojarzyć z projektem.<br/><br/> Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana mapa usługi](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> W usłudze Log Analytics obszar roboczy skojarzony z programem Azure Migrate jest oznaczony kluczem projektu migracji i nazwą projektu.
**Wymaganych agentów** | Na każdym komputerze, który chcesz przeanalizować, zainstaluj następujące środki:<br/><br/> [Agent monitorowania firmy Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [Agent zależności](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Jeśli komputery lokalne nie są połączone z Internetem, należy pobrać i zainstalować na nich bramę usługi Log Analytics.<br/><br/> Dowiedz się więcej o instalowaniu [agenta zależności](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) i [programu MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Obszar roboczy usługi Log Analytics** | Obszar roboczy musi być w tej samej subskrypcji co projekt migracji platformy Azure.<br/><br/> Usługa Azure Migrate obsługuje obszary robocze zamieszkałe w regionach wschodnich stanów USA, Azji Południowo-Wschodniej i Europy Zachodniej.<br/><br/>  Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana mapa usługi](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Obszaru roboczego dla projektu migracji platformy Azure nie można zmodyfikować po jego dodaniu.
**Koszty** | Rozwiązanie mapy usług nie ponosi żadnych opłat przez pierwsze 180 dni (od dnia skojarzenia obszaru roboczego usługi Log Analytics z projektem migracji platformy Azure)/<br/><br/> Po upływie 180 dni będą naliczane standardowe opłaty za korzystanie z usługi Log Analytics.<br/><br/> Przy użyciu dowolnego rozwiązania innego niż Mapa usługi w skojarzonym obszarze roboczym usługi Log Analytics poniesie [standardowe opłaty](https://azure.microsoft.com/pricing/details/log-analytics/) za usługę Log Analytics.<br/><br/> Po usunięciu projektu migracji platformy Azure obszar roboczy nie jest usuwany wraz z nim. Po usunięciu projektu użycie mapy usługi nie jest bezpłatne, a każdy węzeł zostanie obciążony zgodnie z płatną warstwą obszaru roboczego usługi Log Analytics/<br/><br/>Jeśli masz projekty utworzone przed migracją platformy Azure ogólnej dostępności (GA- 28 lutego 2018), być może ponieśli dodatkowe opłaty mapy usługi. Aby zapewnić płatność tylko po 180 dniach, zaleca się utworzenie nowego projektu, ponieważ istniejące obszary robocze przed ga są nadal obciążalne.
**Zarządzanie** | Podczas rejestrowania agentów w obszarze roboczym, należy użyć identyfikatora i klucza dostarczonego przez projekt migracji platformy Azure.<br/><br/> Można użyć obszaru roboczego usługi Log Analytics poza programem Azure Migrate.<br/><br/> Jeśli usuniesz skojarzony projekt migracji platformy Azure, obszar roboczy nie zostanie automatycznie usunięty. [Usuń go ręcznie](../azure-monitor/platform/manage-access.md).<br/><br/> Nie usuwaj obszaru roboczego utworzonego przez usługę Azure Migrate, chyba że usuniesz projekt migracji platformy Azure. Jeśli to zrobisz, funkcja wizualizacji zależności nie będzie działać zgodnie z oczekiwaniami.
**Łączność z Internetem** | Jeśli komputery nie są połączone z Internetem, należy zainstalować na nich bramę usługi Log Analytics.

## <a name="next-steps"></a>Następne kroki

[Przygotowanie do oceny maszyny wirtualnej funkcji Hyper-V](tutorial-prepare-hyper-v.md)
