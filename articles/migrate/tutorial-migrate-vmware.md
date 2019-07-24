---
title: Migrowanie lokalnych maszyn wirtualnych programu VMware na platformę Azure za pomocą migracji serwera Azure Migrate bez agentów | Microsoft Docs
description: W tym artykule opisano, jak przeprowadzić migrację lokalnych maszyn wirtualnych programu VMware do platformy Azure przy użyciu Azure Migrate i bez agentów.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 885c877f219f59ab5049cf7b8e01243077d6d3eb
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348387"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>Migrowanie maszyn wirtualnych VMware na platformę Azure (bez wykorzystania agentów)

W tym artykule opisano sposób migrowania lokalnych maszyn wirtualnych programu VMware na platformę Azure przy użyciu migracji bez wykorzystania agentów za pomocą narzędzia migracji Azure Migrate Server.

[Azure Migrate](migrate-services-overview.md) udostępnia centralne centrum do śledzenia odnajdywania, oceny i migracji lokalnych aplikacji i obciążeń oraz wystąpień maszyn wirtualnych AWS/GCP na platformę Azure. Centrum udostępnia Azure Migrate narzędzia do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm.

Ten samouczek jest trzecią częścią serii, która pokazuje, jak oceniać i migrować maszyny wirtualne VMware na platformę Azure przy użyciu funkcji oceny i migracji serwera Azure Migrate. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotuj maszyny wirtualne do migracji.
> * Dodaj narzędzie migracji serwera migracji platformy Azure.
> * Odnajdź maszyny wirtualne, które chcesz zmigrować.
> * Rozpocznij replikację maszyn wirtualnych.
> * Uruchom migrację testową, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
> * Uruchom pełną migrację maszyny wirtualnej.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="migration-methods"></a>Metody migracji

Maszyny wirtualne programu VMware można migrować do platformy Azure za pomocą narzędzia migracji Azure Migrate Server. To narzędzie oferuje kilka opcji migracji maszyn wirtualnych VMware:

- Migracja przy użyciu replikacji bez wykorzystania agentów. Migrowanie maszyn wirtualnych bez konieczności instalowania na nich jakichkolwiek elementów.
- Migracja z agentem na potrzeby replikacji. Zainstaluj agenta na maszynie wirtualnej na potrzeby replikacji.

Aby zdecydować, czy chcesz korzystać z migracji opartej na agencie, czy na agencie, zapoznaj się z następującymi artykułami:

- [Dowiedz się, w jaki sposób](server-migrate-overview.md) działa migracja bez agentów, i zapoznaj [się z ograniczeniami](server-migrate-overview.md#agentless-migration-limitations).
- [Przeczytaj ten artykuł](tutorial-migrate-vmware-agent.md) , jeśli chcesz użyć metody opartej na agencie.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy:

1. [Poznaj](migrate-architecture.md) architekturę migracji VMware.
2. [Wykonaj pierwszy samouczek](tutorial-prepare-vmware.md) z tej serii, aby skonfigurować platformę Azure i oprogramowanie VMware do migracji. W tym samouczku należy:
    - [Przygotuj platformę Azure](tutorial-prepare-vmware.md#prepare-azure) do migracji.
    - [Przygotuj środowisko lokalne](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration) do migracji.
    
3. Zalecamy, aby przed migracją na platformę Azure spróbować ocenić maszyny wirtualne VMware z oceną serwera Azure Migrate. Aby skonfigurować ocenę, [Wykonaj drugi samouczek](tutorial-assess-vmware.md) z tej serii. Jeśli nie chcesz oceniać maszyn wirtualnych, możesz pominąć ten samouczek. Chociaż zalecamy wypróbowanie oceny, ale nie trzeba uruchamiać oceny przed rozpoczęciem migracji.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Dodawanie narzędzia migracji Azure Migrate Server

Jeśli nie korzystasz z drugiego samouczka do oceny maszyn wirtualnych VMware, musisz [postępować zgodnie z poniższymi instrukcjami](how-to-add-tool-first-time.md) , aby skonfigurować projekt Azure Migrate, a następnie wybierz narzędzie do migracji Azure Migrate Server. 

Jeśli wykonano drugi samouczek i masz już skonfigurowany projekt Azure Migrate, Dodaj narzędzie do migracji Azure Migrate Server w następujący sposób:

1. W projekcie Azure Migrate kliknij pozycję **Przegląd**. 
2. W obszarze **odnajdywanie, ocenianie i**Migrowanie serwerów kliknij pozycję **Oceń i Przeprowadź migrację serwerów**.

     ![Ocenianie i Migrowanie serwerów](./media/tutorial-migrate-vmware/assess-migrate.png)

3. W obszarze **Narzędzia migracji**wybierz **pozycję kliknij tutaj, aby dodać narzędzie do migracji, gdy wszystko jest gotowe do migracji**.

    ![Wybór narzędzia](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. Z listy narzędzia wybierz **Azure Migrate:** **Narzędzie Dodawanie** do migracji > serwera

    ![Narzędzie do migracji serwera](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>Konfigurowanie urządzenia Azure Migrate

Migracja serwera Azure Migrate uruchamia lekkie urządzenie maszyny wirtualnej VMware. Urządzenie wykonuje odnajdywanie maszyn wirtualnych i wysyła metadane maszyny wirtualnej i dane wydajności do migracji serwera Azure Migrate. To samo urządzenie jest również używane przez narzędzie do oceny serwera Azure Migrate.

Jeśli wykonano drugi samouczek do oceny maszyn wirtualnych VMware, urządzenie zostało już skonfigurowane w tym samouczku. Jeśli nie korzystasz z tego samouczka, musisz skonfigurować urządzenie teraz. W tym celu: 

- Pobierz plik szablonu komórki jajowe i zaimportuj go do vCenter Server.
- Utwórz urządzenie i sprawdź, czy może nawiązać połączenie z oceną serwera Azure Migrate. 
- Skonfiguruj urządzenie po raz pierwszy i zarejestruj je w projekcie Azure Migrate.

Postępuj zgodnie z instrukcjami w [tym artykule](how-to-set-up-appliance-vmware.md) , aby skonfigurować urządzenie.


## <a name="prepare-vms-for-migration"></a>Przygotowywanie maszyn wirtualnych do migracji

Azure Migrate wymaga wprowadzenia zmian w maszynie wirtualnej w celu zapewnienia, że maszyny wirtualne można migrować do platformy Azure.

- W niektórych [systemach operacyjnych](server-migrate-overview.md#agentless-migration-limitations)Azure Migrate automatycznie wprowadza te zmiany.
- W przypadku migrowania maszyny wirtualnej, która nie ma jednego z tych systemów operacyjnych, postępuj zgodnie z instrukcjami, aby przygotować maszynę wirtualną.
- Ważne jest, aby wprowadzić te zmiany przed rozpoczęciem migracji. W przypadku migrowania maszyny wirtualnej przed wprowadzeniem zmiany, maszyna wirtualna może nie zostać uruchomiona na platformie Azure.
- Zmiany konfiguracji wprowadzone w lokalnych maszynach wirtualnych są replikowane na platformę Azure po włączeniu replikacji dla maszyny wirtualnej. Aby upewnić się, że zmiany są replikowane, należy się upewnić, że migrowany punkt odzyskiwania jest późniejszy niż czas, w którym zmiany konfiguracji zostały wprowadzone lokalnie.


### <a name="prepare-windows-server-vms"></a>Przygotowywanie maszyn wirtualnych z systemem Windows Server

**Akcja** | **Szczegóły** | **Wskazówek**
--- | --- | ---
Upewnij się, że woluminy systemu Windows na maszynie wirtualnej platformy Azure używają tych samych przypisań liter dysku co lokalna maszyna wirtualna. | Skonfiguruj zasady sieci SAN jako wszystkie. | 1. Zaloguj się do maszyny wirtualnej przy użyciu konta administratora, a następnie otwórz okno polecenia.<br/> 2. Wpisz polecenie **diskpart** , aby uruchomić narzędzie Diskpart.<br/> 3. Typ **zasad sieci San = OnlineAll**<br/> 4. Wpisz exit, aby opuścić program DiskPart, i Zamknij wiersz polecenia.
Włączanie konsoli dostępu szeregowego platformy Azure dla maszyny wirtualnej platformy Azure | Ułatwia to rozwiązywanie problemów. Nie ma potrzeby ponownego uruchamiania maszyny wirtualnej. Maszyna wirtualna platformy Azure rozpocznie rozruch przy użyciu obrazu dysku, co jest równoznaczne z ponownym uruchomieniem nowej maszyny wirtualnej. | Postępuj zgodnie z [tymi instrukcjami](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console) , aby włączyć.
Zainstaluj integrację gościa funkcji Hyper-V | W przypadku migrowania maszyn z systemem Windows Server 2003 należy zainstalować usługi integracji gościa funkcji Hyper-V w systemie operacyjnym maszyny wirtualnej. | [Dowiedz się więcej](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).
Pulpit zdalny | Włącz Pulpit zdalny na maszynie wirtualnej i sprawdź, czy Zapora systemu Windows nie blokuje dostępu Pulpit zdalny do żadnych profilów sieciowych. | [Dowiedz się więcej](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).

### <a name="prepare-linux-vms"></a>Przygotowywanie maszyn wirtualnych z systemem Linux

**Akcja** | **Szczegóły** 
--- | --- | ---
Zainstaluj usługi integracji funkcji Hyper-V w systemie Linux | Większość nowych wersji dystrybucji systemu Linux jest domyślnie uwzględnionych w tym systemie.
Ponowne kompilowanie obrazu init systemu Linux w celu uwzględnienia wymaganych sterowników funkcji Hyper-V | Dzięki temu maszyna wirtualna zostanie uruchomiona na platformie Azure i będzie wymagana tylko w przypadku niektórych dystrybucji.
Włącz rejestrowanie w konsoli szeregowej platformy Azure | Ułatwia to rozwiązywanie problemów. Nie ma potrzeby ponownego uruchamiania maszyny wirtualnej. Maszyna wirtualna platformy Azure rozpocznie rozruch przy użyciu obrazu dysku, co jest równoznaczne z ponownym uruchomieniem nowej maszyny wirtualnej.<br/> Postępuj zgodnie z [tymi instrukcjami](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) , aby włączyć.
Zaktualizuj plik mapy urządzeń | Zaktualizuj plik mapy urządzeń zawierający nazwę urządzenia do skojarzenia woluminu, aby użyć trwałych identyfikatorów urządzeń
Aktualizowanie wpisów fstab | Aktualizowanie wpisów w celu używania identyfikatorów woluminów trwałych.
Usuń regułę udev | Usuń wszystkie reguły udev, które rezerwują nazwy interfejsów na podstawie adresu MAC itp.
Aktualizowanie interfejsów sieciowych | Zaktualizuj interfejsy sieciowe, aby odbierać adresy IP na podstawie protokołu DHCP.
Włączanie protokołu SSH | Upewnij się, że protokół SSH jest włączony i że usługa SSHD jest uruchomiona automatycznie po ponownym uruchomieniu.<br/> Upewnij się, że przychodzące żądania połączenia SSH nie są blokowane przez zaporę systemu operacyjnego ani reguły obsługujące skrypty.

[Postępuj zgodnie z tym artykułem](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic) , który omawia te kroki w celu uruchomienia maszyny wirtualnej z systemem Linux na platformie Azure, i Dołącz instrukcje dotyczące niektórych popularnych dystrybucji systemu Linux.  


## <a name="replicate-vms"></a>Replikowanie maszyn wirtualnych

Po ukończeniu odnajdywania można rozpocząć replikację maszyn wirtualnych VMware na platformę Azure.

1. Na **serwerach** **Azure Migrate Project > Azure Migrate: Migracja**serwera, kliknij przycisk **replikacja**.

    ![Replikowanie maszyn wirtualnych](./media/tutorial-migrate-vmware/select-replicate.png)

2. W obszarze **replikacja**> **Ustawienia** > źródła**są zwirtualizowane na maszynach?** wybierz opcję **tak, z VMware vSphere**.
3. W **urządzeniu lokalnym**wybierz nazwę urządzenia Azure Migrate, które zostało skonfigurowane > **przycisk OK**. 

    ![Ustawienia źródła](./media/tutorial-migrate-vmware/source-settings.png)

    - W tym kroku zakłada się, że urządzenie zostało już skonfigurowane po zakończeniu pracy z samouczkiem.
    - Jeśli nie skonfigurowano urządzenia, postępuj zgodnie z instrukcjami w [tym artykule](how-to-set-up-appliance-vmware.md).

4. W obszarze **maszyny wirtualne**wybierz maszyny, które chcesz replikować.
    - Jeśli przeprowadzasz ocenę dla maszyn wirtualnych, możesz zastosować zalecenia dotyczące rozmiarów maszyn wirtualnych i typów dysków (Premium/standard) w wynikach oceny. W tym celu w obszarze **Importuj ustawienia migracji z Azure Migrate oceny?** wybierz opcję **tak** .
    - Jeśli nie uruchomiono oceny lub nie chcesz używać ustawień oceny, wybierz opcję **Brak** opcji.
    - W przypadku wybrania opcji korzystania z oceny wybierz grupę maszyn wirtualnych i nazwę oceny.

    ![Wybór oceny](./media/tutorial-migrate-vmware/select-assessment.png)

5. W obszarze **maszyny wirtualne**Wyszukaj maszyny wirtualne w razie potrzeby i sprawdź każdą maszynę wirtualną, którą chcesz zmigrować. Następnie kliknij **przycisk Dalej: Ustawienia**docelowe.

    ![Wybieranie maszyn wirtualnych](./media/tutorial-migrate-vmware/select-vms.png)

6. W obszarze **ustawienia docelowe**wybierz subskrypcję i docelowy region do migracji, a następnie określ grupę zasobów, w której będą znajdować się maszyny wirtualne platformy Azure po migracji. W **Virtual Network**wybierz sieć wirtualną platformy Azure, do której zostaną dołączone maszyny wirtualne platformy Azure po migracji.
7. W **korzyść użycia hybrydowego platformy Azure**:

    - Wybierz pozycję **nie** , jeśli nie chcesz stosować korzyść użycia hybrydowego platformy Azure. Następnie kliknij przycisk **Next** (Dalej).
    - Wybierz opcję **tak** , jeśli masz maszyny z systemem Windows Server, które są objęte aktywnym programem Software Assurance lub subskrypcjami systemu Windows Server, i chcesz zastosować korzyść do migrowanych maszyn. Następnie kliknij przycisk **Next** (Dalej).

    ![Ustawienia docelowe](./media/tutorial-migrate-vmware/target-settings.png)

8. W obszarze **obliczenia**Sprawdź nazwę, rozmiar, typ dysku systemu operacyjnego i zestaw dostępności maszyny wirtualnej. Maszyny wirtualne muszą być zgodne z [wymaganiami platformy Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Rozmiar maszyny wirtualnej**: Jeśli używasz zaleceń dotyczących oceny, lista rozwijana rozmiaru maszyny wirtualnej będzie zawierać zalecany rozmiar. W przeciwnym razie Azure Migrate wybierają rozmiar na podstawie najbliższego dopasowania w subskrypcji platformy Azure. Alternatywnie możesz wybrać rozmiar ręczny w **rozmiarze maszyny wirtualnej platformy Azure**. 
    - **Dysk systemu operacyjnego**: Określ dysk systemu operacyjnego (Boot) dla maszyny wirtualnej. Dysk systemu operacyjnego to dysk, na którym jest zainstalowany program inicjujący i Instalatora systemu operacyjnego. 
    - **Zestaw dostępności**: Jeśli maszyna wirtualna powinna znajdować się w zestawie dostępności platformy Azure po migracji, określ zestaw. Zestaw musi znajdować się w docelowej grupie zasobów określonej dla migracji.

    ![Ustawienia obliczeniowe maszyny wirtualnej](./media/tutorial-migrate-vmware/compute-settings.png)

9. W obszarze **dyski**Określ, czy dyski maszyn wirtualnych mają być replikowane na platformę Azure, a następnie wybierz typ dysku (standardowy dysk SSD lub dyski w warstwie Premium) na platformie Azure. Następnie kliknij przycisk **Next** (Dalej).
    - Dyski można wykluczać z replikacji.
    - Jeśli wykluczasz dyski, program nie będzie znajdował się na maszynie wirtualnej platformy Azure po migracji. 

    ![Dyski](./media/tutorial-migrate-vmware/disks.png)

10. W obszarze **Przegląd i rozpoczęcie replikacji**Sprawdź ustawienia, a następnie kliknij pozycję **Replikuj** , aby rozpocząć replikację początkową dla serwerów.

> [!NOTE]
> Ustawienia replikacji można aktualizować w dowolnym momencie przed rozpoczęciem replikacji w obszarze **Zarządzanie** > **maszynami replikowanymi**. Nie można zmienić ustawień po rozpoczęciu replikacji.

### <a name="provisioning-for-the-first-time"></a>Inicjowanie obsługi po raz pierwszy

Jeśli jest to pierwsza maszyna wirtualna, która jest replikowana w projekcie Azure Migrate, Azure Migrate migracji serwera automatycznie zainicjuje te zasoby w tej samej grupie zasobów co projekt.

- **Usługa Service Bus**: Migracja serwera Azure Migrate przy użyciu usługi Service Bus wysyła do urządzenia komunikaty aranżacji replikacji.
- **Konto magazynu bramy**: Migracja serwera używa konta magazynu bramy do przechowywania informacji o stanie replikowanych maszyn wirtualnych.
- **Konto magazynu dzienników**: Urządzenie Azure Migrate przekazuje dzienniki replikacji dla maszyn wirtualnych do konta magazynu dzienników. Azure Migrate stosuje informacje o replikacji do dysków zarządzanych repliki.
- **Magazyn kluczy**: Urządzenie Azure Migrate używa magazynu kluczy do zarządzania parametrami połączenia dla magistrali usług i kluczy dostępu dla kont magazynu używanych w replikacji. Należy skonfigurować uprawnienia, które Magazyn kluczy musi uzyskać dostęp do konta magazynu podczas przygotowywania. [Przejrzyj te uprawnienia](tutorial-prepare-vmware.md#assign-role-assignment-permissions).   


## <a name="track-and-monitor"></a>Śledzenie i monitorowanie


- Po kliknięciu przycisku **Replikuj** rozpocznie się zadanie uruchamiania replikacji. 
- Po pomyślnym zakończeniu zadania Rozpocznij replikację maszyny rozpoczynają replikację początkową na platformę Azure.
- Podczas replikacji początkowej tworzona jest migawka maszyny wirtualnej. Dane dysku z migawki są replikowane do dysków zarządzanych repliki na platformie Azure.
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Przyrostowe zmiany w dyskach lokalnych są okresowo replikowane do dysków repliki na platformie Azure.

Stan zadania można śledzić w powiadomieniach portalu.

Aby monitorować stan replikacji, należy kliknąć opcję **replikowanie serwerów** w **Azure Migrate: Migracja**serwera.
![Monitoruj replikację](./media/tutorial-migrate-vmware/replicating-servers.png)




## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej


Po rozpoczęciu replikacji różnicowej można przeprowadzić migrację testową dla maszyn wirtualnych przed uruchomieniem pełnej migracji na platformę Azure. Zdecydowanie zalecamy wykonanie tej czynności co najmniej raz na każdym komputerze, przed przeprowadzeniem migracji.

- Podczas przeprowadzania migracji testów migracja będzie działać zgodnie z oczekiwaniami, bez wpływu na maszyny lokalne, które pozostają w działaniu i kontynuują replikację. 
- Migracja testowa symuluje migrację, tworząc maszynę wirtualną platformy Azure przy użyciu zreplikowanych danych (zazwyczaj migrujesz do nieprodukcyjnej sieci wirtualnej w ramach subskrypcji platformy Azure).
- Możesz użyć zreplikowanego testowej maszyny wirtualnej platformy Azure, aby zweryfikować migrację, przeprowadzić testowanie aplikacji i rozwiązać wszelkie problemy przed pełną migracją.

Wykonaj migrację testową w następujący sposób:


1. W obszarze**serwery** > celówmigracji > Azure Migrate:  **Migracja**serwera, kliknij przycisk **Testuj zmigrowane serwery**.

     ![Testowanie migrowanych serwerów](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Kliknij prawym przyciskiem myszy maszynę wirtualną do przetestowania, a następnie kliknij pozycję **Testuj migrację**.

    ![Testowanie migracji](./media/tutorial-migrate-vmware/test-migrate.png)

3. W obszarze **migracja testowa**wybierz sieć wirtualną platformy Azure, w której zostanie umieszczona maszyna wirtualna platformy Azure po migracji. Zalecamy użycie nieprodukcyjnej sieci wirtualnej.
4. Rozpocznie się zadanie **migracji testowej** . Monitoruj zadanie w powiadomieniach portalu.
5. Po zakończeniu migracji Sprawdź zmigrowane maszyny wirtualne platformy Azure w **Virtual Machines** w Azure Portal. Nazwa maszyny ma **test**sufiksu.
6. Po zakończeniu testu kliknij prawym przyciskiem myszy maszynę wirtualną platformy Azure w obszarze **replikowanie maszyn**, a następnie kliknij pozycję **Oczyść test migracja**.

    ![Czyszczenie migracji](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Migrowanie maszyn wirtualnych

Po zweryfikowaniu, że migracja testowa działa zgodnie z oczekiwaniami, można przeprowadzić migrację maszyn lokalnych.

1. Na **serwerach** > AzureMigrateProject>AzureMigrate: **Migracja**serwera, kliknij przycisk **replikowanie serwerów**.

    ![Replikowanie serwerów](./media/tutorial-migrate-vmware/replicate-servers.png)

2. W obszarze **replikowanie maszyn**kliknij prawym przyciskiem myszy maszynę wirtualną > **migracji**.
3. W  > obszarze Migrowanie**Zamknij maszyny wirtualne i przeprowadź planowaną migrację bez utraty danych**wybierz pozycję **tak** > **OK**.
    - Domyślnie Azure Migrate zamyka lokalną maszynę wirtualną i uruchamia replikację na żądanie, aby synchronizować wszystkie zmiany maszyny wirtualnej, które wystąpiły od momentu ostatniej replikacji. Zapewnia to brak utraty danych.
    - Jeśli nie chcesz zamykać maszyny wirtualnej, wybierz pozycję **nie**
4. Rozpocznie się zadanie migracji dla maszyny wirtualnej. Śledź zadanie w powiadomieniach platformy Azure.
5. Po zakończeniu zadania można wyświetlić maszynę wirtualną i zarządzać nią na stronie **Virtual Machines** .

## <a name="complete-the-migration"></a>Kończenie migracji

1. Po zakończeniu migracji kliknij prawym przyciskiem myszy maszynę wirtualną > **Zatrzymaj migrację**. Spowoduje to zatrzymanie replikacji maszyny lokalnej i oczyszczenie informacji o stanie replikacji dla maszyny wirtualnej.
2. Zainstaluj agenta maszyny wirtualnej platformy Azure dla [systemu Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) lub [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) na zmigrowanych maszynach.
3. Po zakończeniu migracji wykonaj wszystkie potrzebne czynności konfiguracyjne, takie jak aktualizacja parametrów połączenia bazy danych i serwera sieci Web.
4. Wykonaj dla zmigrowanej aplikacji uruchomionej na platformie Azure testy końcowe aplikacji i akceptacji migracji.
5. Obcinaj ruch do zmigrowanego wystąpienia maszyny wirtualnej platformy Azure.
6. Usuń lokalne maszyny wirtualne z lokalnego spisu maszyn wirtualnych.
7. Usuń lokalne maszyny wirtualne z lokalnych kopii zapasowych.
8. Zaktualizuj wszystkie dokumenty wewnętrzne, aby wyświetlić nową lokalizację i adres IP maszyn wirtualnych platformy Azure. 

## <a name="post-migration-best-practices"></a>Najlepsze rozwiązania po migracji

- Aby zwiększyć elastyczność:
    - Zapewnij bezpieczeństwo danych – utwórz kopie zapasowe maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup. [Dowiedz się więcej](../backup/quick-backup-vm-portal.md).
    - Zadbaj, aby pakiety robocze były uruchomione i stale dostępne, replikując maszyny wirtualne platformy Azure do regionu pomocniczego za pomocą usługi Site Recovery. [Dowiedz się więcej](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Aby zwiększyć bezpieczeństwo:
    - Zablokuj i Ogranicz dostęp do ruchu przychodzącego za pomocą [administracji Azure Security Center w czasie](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Ogranicz ruch sieciowy do punktów końcowych zarządzania za pomocą [sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Wdróż usługę [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview), aby ułatwić zabezpieczenie dysków i zabezpieczyć dane przed kradzieżą lub nieautoryzowanym dostępem.
    - Przeczytaj więcej na temat [zabezpieczania zasobów IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) i skorzystaj z usługi [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Na potrzeby monitorowania i zarządzania:
-  Rozważ wdrożenie usługi [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview), aby monitorować użycie zasobu i wydatki.


## <a name="next-steps"></a>Kolejne kroki

Zbadaj [podróż migracji w chmurze](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) w strukturze wdrażania w chmurze platformy Azure.
