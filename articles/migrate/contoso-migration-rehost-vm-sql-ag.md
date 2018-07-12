---
title: Ponowne hostowanie aplikacji firmy Contoso, migrując je do grupy dostępności maszyn wirtualnych platformy Azure i programu SQL Server AlwaysOn | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso rehost aplikację w środowisku lokalnym, migrując je do grupy dostępności maszyn wirtualnych platformy Azure i programu SQL Server AlwaysOn
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: raynew
ms.openlocfilehash: 33ab56003f2d9428816ea0f32cfd6381ea857df3
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611292"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms-and-sql-server-alwayson-availability-group"></a>Migracja Contoso: ponowne hostowanie aplikacji w środowisku lokalnym, do grupy dostępności maszyn wirtualnych platformy Azure i programu SQL Server AlwaysOn

W tym artykule przedstawiono, jak Contoso ponowne hostowanie aplikacji SmartHotel na platformie Azure. Migrowania aplikacji frontonu maszyny Wirtualnej na Maszynie wirtualnej platformy Azure i bazę danych aplikacji na maszynie Wirtualnej platformy Azure programu SQL Server, działające w klastrze trybu failover systemu Windows Server z użyciem grup dostępności AlwaysOn programu SQL Server.

W tym dokumencie jest jednym z serii artykułów, które pokazują, jak fikcyjnej firmy Contoso migruje swoje zasoby lokalne do chmury Microsoft Azure. Seria zawiera dodatkowe informacje i scenariusze, które ilustrują konfigurowania infrastruktury migracji, ocena zasobów lokalnych do migracji i z różnymi typami na potrzeby migracji. Scenariusze zwiększanie się stopnia skomplikowania, a dodamy dodatkowe artykuły, wraz z upływem czasu.

**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: omówienie](contoso-migration-overview.md) | Zawiera omówienie strategii migracji firmy Contoso, serię artykułów i przykładowe aplikacje, używanych przez firmę Microsoft. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | W tym artykule opisano, jak Contoso przygotowuje jej w środowisku lokalnym i infrastruktury platformy Azure do migracji. Tej samej infrastruktury jest używany dla wszystkich artykułów migracji. | Dostępne
[Artykuł 3: Ocena zasobów lokalnych](contoso-migration-assessment.md)  | Pokazuje, jak firmy Contoso jest wykonywany oceny aplikacji SmartHotel dwuwarstwowej lokalnych działających w oprogramowaniu firmy VMware. Contoso ocenia maszynach wirtualnych usługi app przy użyciu [usługi Azure Migrate](migrate-overview.md) usługi bazy danych oraz aplikacji programu SQL Server za pomocą [Asystenta migracji bazy danych](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Dostępne
[Artykuł 4: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i wystąpienie zarządzane SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Pokazuje, jak firmy Contoso jest uruchamiana lift-and-shift migracja na platformę Azure dla aplikacji SmartHotel. Firma Contoso jest migrowana aplikację frontonu maszyn wirtualnych za pomocą [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)i bazy danych aplikacji do wystąpienia zarządzanego SQL, przy użyciu [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Dostępne
[Artykuł 5: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Pokazuje, jak Contoso migrację aplikacji SmartHotel maszyn wirtualnych tylko przy użyciu Site Recovery.
Artykuł 6: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i programu SQL Server zawsze włączonej grupy dostępności (w tym artykule) | Pokazuje, jak Contoso migruje SmartHotel aplikacji. Firma Contoso używa Usługa Site Recovery do migrowania aplikacji, maszyny wirtualne i usługi migracji bazy danych do migracji bazy danych aplikacji do klastra programu SQL Server chronione przez grupy dostępności AlwaysOn. | Dostępne
[Artykuł 7: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure](contoso-migration-rehost-linux-vm.md) | Pokazuje, jak firmy Contoso jest lift-and-shift migracji aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure, przy użyciu Site Recovery | Planowany
[Artykuł 8: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure i serwer Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Pokazuje, jak Contoso migruje aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure przy użyciu funkcji odzyskiwania lokacji i bazy danych aplikacji jest migrowana do wystąpienia serwera usługi Azure MySQL za pomocą aplikacji MySQL Workbench. | Dostępne



W tym artykule Contoso migrować Windows dwuwarstwowy. Aplikacja NET SmartHotel działających na maszynach wirtualnych VMware na platformę Azure. Jeśli chcesz korzystać z tej aplikacji, znajduje się jako "open source" i można go pobrać z [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Czynniki biznesowe

Zespół kierowniczy IT ma ściśle współpracowała z ich partnerów biznesowych, aby zrozumieć, czego chcą osiągnąć za pomocą tej migracji:

- **Adres rozwój**: Contoso się, a w rezultacie istnieje nacisk na swoich systemów lokalnych i infrastruktury.
- **Zwiększenie wydajności**: Contoso musi usunąć niepotrzebne procedur i usprawniać procesy dla deweloperów i użytkowników.  Potrzeby biznesowe IT szybkie i nie odpadów czasu lub pieniądze, w związku z tym dostarczania szybciej na wymagania klientów.
- **Zwiększenie elastyczności**: Contoso IT musi być w reakcji na potrzeby biznesowe. Musi być w stanie szybciej niż zmiany w witrynie marketplace, aby umożliwić sukces w globalnej gospodarki reagować.  Nie może pobrać w taki sposób, lub stają się blocker biznesowych.
- **Skala**: w miarę rozwoju firmy pomyślnie, IT firmy Contoso, musisz podać systemów, które można rozwijać w tym samym tempie.

## <a name="migration-goals"></a>Cele migracji

Zespół chmury firmy Contoso ma przypięte dół cele tej migracji. Te cele zostały użyte w celu ustalenia najlepszej metody migracji:

- Po migracji aplikacji na platformie Azure mają te same możliwości wydajności, jak obecnie w środowisku VMWare.  Aplikacja pozostanie tak krytyczne w chmurze, ponieważ jest w środowisku lokalnym.
- Contoso nie chcesz inwestować w tej aplikacji.  Ważne jest, aby firmy, ale w obecnej formie po prostu chcą bezpiecznie przenieść je do chmury.
- Lokalna baza danych dla aplikacji miał problemy z dostępnością. Chcieliby zobaczyć wdrożonych na platformie Azure jako klaster o wysokiej dostępności, możliwości trybu failover.
- Firma Contoso chce uaktualnić ich bieżącej platformy SQL Server 2008 R2 do programu SQL Server 2017.
- Contoso nie zamierza korzystać z usługi Azure SQL Database dla tej aplikacji, a szuka alternatyw.

## <a name="proposed-architecture"></a>Proponowana architektury

W tym scenariuszu:

- Aplikacja jest rozmieszczone warstwowo na dwie maszyny wirtualne (WEBVM i SQLVM).
- Maszyny wirtualne znajdują się na hoście VMware ESXi **contosohost1.contoso.com** (w wersji 6.5)
- Środowisko VMware jest zarządzane przez program vCenter Server 6.5 (**vcenter.contoso.com**) uruchomionego na maszynie Wirtualnej.
- Firma Contoso ma lokalne centrum danych (contoso-datacenter), lokalnego kontrolera domeny (**contosodc1**).
- Lokalne maszyny wirtualne w centrum danych firmy Contoso zostanie zamknięty, po zakończeniu migracji.

![Architektura scenariusza](media/contoso-migration-rehost-vm-sql-ag/architecture.png) 

### <a name="azure-services"></a>Usługi platformy Azure

**Usługa** | **Opis** | **Koszty**
--- | --- | ---
[Usługa zarządzania bazy danych](https://docs.microsoft.com/azure/dms/dms-overview) | Contoso spowoduje migrację warstwy danych aplikacji przy użyciu usługi DMS. Usługa DMS nawiązywania połączenia lokalnej maszynie SQLVM w sieci VPN lokacja lokacja i migracji DMS umożliwia bezproblemową migrację z wielu źródłowych baz danych na platformach danych platformy Azure, przy minimalnych przestojach. | Dowiedz się więcej o [obsługiwane regiony](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) DMS i get [cennik](https://azure.microsoft.com/pricing/details/database-migration/).
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Contoso będzie używać usługa Site Recovery jest migracja lift-and-shift frontonu aplikacji maszyny Wirtualnej. Usługa Site Recovery organizuje zarządza migracji i odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure i lokalnych maszyn wirtualnych i serwerów fizycznych.  | Podczas replikacji do platformy Azure są naliczane opłaty za magazyn Azure.  Maszyny wirtualne platformy Azure są tworzone i naliczenia opłat, po przejściu do trybu failover. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/site-recovery/) o Naliczanie opłat i ceny.

 

## <a name="migration-process"></a>Proces migracji

- Contoso zostaną zmigrowane maszyny wirtualne aplikacji na platformie Azure.
- Będą oni migrować maszyny Wirtualnej serwera sieci Web do maszyny Wirtualnej platformy Azure przy użyciu Site Recovery:
    - Pierwszym krokiem one przygotowywanie i konfigurowanie składników platformy Azure i przygotowywanie lokalnej infrastruktury VMware.
    - Ze wszystkim, co przygotowany można uruchomić, replikacja maszyny Wirtualnej.
    - Po włączeniu replikacji i pracy, migrowania maszyny Wirtualnej przez przechodzenia go w tryb failover na platformie Azure.
- Będą oni migrować bazy danych do klastra programu SQL Server na platformie Azure przy użyciu Data Migration Service (DMS).
    - Pierwszym krokiem one potrzebne do aprowizowania maszyny wirtualne SQL Server na platformie Azure, konfigurowanie klastra i wewnętrznego modułu równoważenia obciążenia i konfigurowanie grup dostępności AlwaysOn.
    - Dzięki temu w miejscu można migrować bazy danych
- Po zakończeniu migracji umożliwi ochronę AlwaysOn dla bazy danych.

![Proces migracji](media/contoso-migration-rehost-vm-sql-ag/migration-process.png) 
 
## <a name="prerequisites"></a>Wymagania wstępne

Oto, co Ty (i firmy Contoso) potrzebne do uruchomienia tego scenariusza:

**Wymagania** | **Szczegóły**
--- | ---
**Subskrypcja platformy Azure** | Powinna już utworzono subskrypcję podczas wykonywania oceny w pierwszym artykułu w tej serii. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Jeśli bezpłatne konto właśnie zostało utworzone, jesteś administratorem subskrypcji i możesz wykonywać wszystkie akcje.<br/><br/> Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem, musisz skontaktować się z administratorem w celu uprawnień właściciela lub współautora.<br/><br/> Jeśli potrzebujesz bardziej szczegółowych uprawnień, zapoznaj się z [w tym artykule](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastruktura platformy Azure** | [Dowiedz się, jak](contoso-migration-infrastructure.md) Contoso Konfigurowanie infrastruktury platformy Azure.<br/><br/> Dowiedz się więcej o określonych [sieci](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) i [magazynu](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) wymagania dotyczące usługi Site Recovery.
**Usługa Site recovery (lokalnego)** | Usługi na lokalnym serwerze vCenter powinny działać w wersji 5.5, 6.0 lub 6.5<br/><br/> Host ESXi w wersji 5.5, 6.0 lub 6.5<br/><br/> Co najmniej jeden maszyn wirtualnych programu VMware działające na hoście ESXi.<br/><br/> Maszyny wirtualne muszą spełniać [wymagania dotyczące usługi Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Obsługiwane [sieci](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) i [magazynu](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfiguracji.<br/><br/> Chcesz replikować maszyny wirtualne muszą spełniać [wymagania dotyczące usługi Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).
**USŁUGA DMS** | Usługa DMS należy [zgodne lokalnego urządzenia sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Należy skonfigurować lokalne urządzenie sieci VPN. Musi mieć dostępny zewnętrznie publiczny adres IPv4, a adres nie może znajdować się za urządzeniem translatora adresów Sieciowych.<br/><br/> Upewnij się, że masz dostęp do lokalnej bazy danych programu SQL Server.<br/><br/> Zapora Windows powinien móc dostęp do aparatu bazy danych źródłowych. [Dowiedz się więcej](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Jeśli występuje zapora, przed komputerze bazy danych, należy dodać reguły zezwalające na dostęp do bazy danych i plików za pośrednictwem portu 445 SMB.<br/><br/> Poświadczenia używane do połączenia ze źródłem programu SQL Server oraz zarządzane wystąpienia docelowego muszą być członkami roli serwera sysadmin.<br/><br/> Potrzebujesz sieci udostępnianie w sieci lokalnej bazy danych usługi DMS umożliwia tworzenie kopii zapasowej źródłowej bazy danych.<br/><br/> Upewnij się, że konto usługi, uruchamiające źródłowe wystąpienie programu SQL Server ma uprawnienia do zapisu w udziale sieciowym.<br/><br/> Należy zapamiętać Windows użytkownika (i hasło), który ma uprawnienia do pełnej kontroli w udziale sieciowym. Azure Database Migration Service personifikuje te poświadczenia użytkownika w celu przekazania plików kopii zapasowej do kontenera usługi Azure storage.<br/><br/> Proces instalacji programu SQL Server Express protokół TCP/IP ustawia **wyłączone** domyślnie. Upewnij się, że jest włączone.


## <a name="scenario-steps"></a>Kroki w scenariuszu

Poniżej przedstawiono, jak Uruchom migrację w Contoso:

> [!div class="checklist"]
> * **Krok 1: Tworzenie maszyn wirtualnych programu SQL Server na platformie Azure**: wysokiej dostępności, firma Contoso ma zostać wdrożony z klastrowanej bazy danych na platformie Azure. Wdrażanych na dwie maszyny wirtualne programu SQL Server i Azure wewnętrznego modułu równoważenia obciążenia.
> * **Krok 2: Wdrażanie klastra**: po wdrożeniu maszyny wirtualne programu SQL Server, ich przygotowywanie klastra usługi Azure SQL Server.  Będą oni migrować swoje bazy danych w tym wstępnie utworzonego klastra.
> * **Krok 3: Przygotowanie usługi DMS**: w celu przygotowania DMS zarejestrowanie dostawcy migracji bazy danych, tworzenie wystąpienia usługi DMS i projektu. Skonfigurowane sygnatury dostępu współdzielonego (SAS) identyfikator (URI). Usługa DMS używa identyfikatora URI skojarzeń zabezpieczeń dostępu do kontenera konta magazynu, do którego usługa przekazuje te pliki kopii zapasowych programu SQL Server.
> * **Krok 4: Przygotowywanie platformy Azure dla usługi Site Recovery**: tworzą konta usługi Azure storage do przechowywania replikowanych danych oraz magazyn usługi Recovery Services.
> * **Krok 5: Przygotowywanie lokalnych zasobów programu VMware do odzyskiwania lokacji**: przygotowywanie konta do maszyny Wirtualnej odnajdywanie i zainstalować agenta i przygotowywanie lokalnych maszyn wirtualnych, tak aby umożliwić im połączenie z maszynami wirtualnymi platformy Azure po włączeniu trybu failover.
> * **Krok 6: Replikowanie maszyn wirtualnych**: Konfigurowanie ustawień replikacji i włączanie replikacji maszyny Wirtualnej.
> * **Krok 7: Migrację bazy danych przy użyciu usługi DMS**: one migracji bazy danych.
> * **Krok 8: Migrowanie maszyn wirtualnych z usługą Site Recovery**: najpierw uruchom testowanie trybu failover, aby upewnić się, że wszystko działa, następuje pełną trybu failover, aby przeprowadzić migrację maszyny Wirtualnej.


## <a name="step-1-prepare-a-sql-server-alwayson-availability-group-cluster"></a>Krok 1: Przygotowanie klastra grupy dostępności AlwaysOn programu SQL Server

Firma Contoso chce Planuj z wyprzedzeniem, wdrażając bazy danych w klastrze na platformie Azure. Można następnie użyć tego klastra dla innych baz danych. 

- Maszyny wirtualne programu SQL Server zostanie wdrożony w grupie zasobów ContosoRG (używane dla zasobów w środowisku produkcyjnym).
- Oprócz unikatowych nazw zarówno maszyny wirtualne używają tych samych ustawień.
- Wewnętrzny moduł równoważenia obciążenia, zostaną Wdrożeni ContosoNetworkingRG (używane w przypadku zasobów sieciowych).
- Maszyny wirtualne będą uruchamiane systemu Windows Server 2016 z programu SQL Server 2017 Enterprise Edition. Contoso nie ma licencji dla tego systemu operacyjnego, dlatego użyjemy obrazu na rynku platformy Azure, udostępniająca licencji jako opłata za zobowiązanie umowy EA platformy Azure.

Poniżej przedstawiono, jak skonfigurować klaster Contoso:

1. Tworzą dwie maszyny wirtualne programu SQL Server, wybierając obrazu programu SQL Server 2017 Enterprise systemu Windows Server 2016 w witrynie Azure Marketplace. 

    ![JEDNOSTKA SKU MASZYNY WIRTUALNEJ SQL](media/contoso-migration-rehost-vm-sql-ag/sql-vm-sku.png)

2. W **Utwórz maszynę wirtualną z kreatora** > **podstawy**, mogą skonfigurować:

    - Nazwy maszyn wirtualnych: **SQLAOG1** i **SQLAOG2**.
    - Ponieważ maszyn są krytyczne dla prowadzonej działalności, umożliwiają one dyski SSD dla typ dysku maszyny Wirtualnej.
    - Określają poświadczeń maszyny.
    - Mogą wdrażać maszyny wirtualne w głównej wschodnie stany USA 2 regionu, w grupie zasobów ContosoRG.

3. W **rozmiar**, zaczynają D2s_V3 jednostki SKU dla obu maszyn wirtualnych. Będzie skalowanie zgodnie z zapotrzebowaniem na później.
4. W **ustawienia**, ich wykonaj następujące czynności:

    - Ponieważ te maszyny wirtualne są krytyczne baz danych dla aplikacji, używają dysków zarządzanych.
    - Ich umieszczenia maszyn w sieci produkcyjnej, wschodnie stany USA 2 podstawowego regionu (**VNET-PROD-EUS2**), w podsieci bazy danych (**PROD-DB-EUS2**).
    - Tworzą nowy zestaw dostępności: **SQLAOGAVSET**z dwóch domenach błędów i pięcioma domenami aktualizacji.

    ![MASZYNY WIRTUALNEJ SQL](media/contoso-migration-rehost-vm-sql-ag/sql-vm-settings.png)

4. W **ustawień programu SQL Server**, łączność z serwerem SQL w sieci wirtualnej (prywatny), ich limit domyślnego portu 1433. Dla uwierzytelniania używają tych samych poświadczeń co u klienta (**contosoadmin**).

    ![MASZYNY WIRTUALNEJ SQL](media/contoso-migration-rehost-vm-sql-ag/sql-vm-db.png)

**Potrzebujesz dodatkowej pomocy?**

- [Uzyskaj Pomoc](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision#1-configure-basic-settings) inicjowania obsługi administracyjnej maszyny Wirtualnej programu SQL Server.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-prereq#create-sql-server-vms) konfigurowania maszyn wirtualnych dla różnych wersji serwera SQL.

## <a name="step-2-deploy-the-failover-cluster"></a>Krok 2: Wdrażanie klastra trybu failover

Poniżej przedstawiono, jak skonfigurować klaster Contoso:

1. Do działania jako monitor w chmurze mogą skonfigurować konto usługi Azure storage.
2. Dodają maszyny wirtualne programu SQL Server do domeny usługi Active Directory w lokalnym centrum danych firmy Contoso.
3. Tworzą klaster na platformie Azure.
4. Mogą skonfigurować monitor w chmurze.
5. Ponadto umożliwiają one SQL zawsze włączone grupy dostępności.

### <a name="set-up-a-storage-account-as-cloud-witness"></a>Konfigurowanie konta magazynu jako monitor w chmurze

Aby skonfigurować monitor w chmurze, firma Contoso potrzebuje konta usługi Azure Storage, w którym będą przechowywane w pliku obiektu blob, używanego do arbitrażu klastra. Tego samego konta magazynu może służyć do konfigurowania monitora w chmurze dla wielu klastrów. 

Contoso tworzy konto magazynu w następujący sposób:

1. Określają one rozpoznawalną nazwę konta (**contosocloudwitness**).
2. Konto uniwersalny ogólne, dzięki magazynowi LRS ich wdrażanie.
3. Są to konto należy umieścić w trzeciego regionu — południowo-środkowe stany USA. Mogą umieścić poza region podstawowy oraz pomocniczy, dzięki czemu pozostanie dostępna w przypadku regionalnej awarii.
4. Mogą umieścić w swojej grupy zasobów, która zawiera zasoby infrastruktury — **ContosoInfraRG**.

    ![Monitor w chmurze](media/contoso-migration-rehost-vm-sql-ag/witness-storage.png)

5. Podczas tworzenia konta magazynu podstawowego i pomocniczy dostęp w celu klucze są generowane dla niego. Muszą one podstawowy klucz dostępu do utworzenia monitora w chmurze. Klucz jest wyświetlany w obszarze nazwy konta magazynu > **klucze dostępu**.

    ![Klucz dostępu](media/contoso-migration-rehost-vm-sql-ag/access-key.png)

### <a name="add-sql-server-vms-to-contoso-domain"></a>Dodaj maszyny wirtualne SQL Server do domeny Contoso

1. Contoso dodaje SQLAOG1 i SQLAOG2 do domeny contoso.com.
2. Następnie na każdej maszynie Wirtualnej mogą zainstalować funkcję klastra pracy awaryjnej Windows i narzędzia.

## <a name="set-up-the-cluster"></a>Konfigurowanie klastra

Przed rozpoczęciem konfigurowania klastra, Contoso tworzy migawkę dysku systemu operacyjnego na każdym komputerze.

![migawka](media/contoso-migration-rehost-vm-sql-ag/snapshot.png)

2. Następnie ich za pomocą skryptu została ze sobą w celu utworzenia klastra pracy awaryjnej Windows.

    ![Tworzenie klastra](media/contoso-migration-rehost-vm-sql-ag/create-cluster1.png)

3. Po utworzeniu klastra, okaże się, że maszyny wirtualne są wyświetlane jako węzły klastra.

     ![Tworzenie klastra](media/contoso-migration-rehost-vm-sql-ag/create-cluster2.png)

## <a name="configure-the-cloud-witness"></a>Konfigurowanie monitora w chmurze

1. Contoso skonfigurować monitor w chmurze przy użyciu **Kreatora konfiguracji kworum** w Menedżerze klastra trybu Failover.
2. W kreatorze należy wybrać, aby utworzyć Monitor w chmurze za pomocą konta magazynu.
3. Po skonfigurowaniu monitora w chmurze w pojawia się w przystawce Menedżer klastra trybu Failover.

    ![Monitor w chmurze](media/contoso-migration-rehost-vm-sql-ag/cloud-witness.png)
            

## <a name="enable-sql-server-always-on-availability-groups"></a>Włącz grup dostępności programu SQL Server Always On

Contoso — teraz można włączyć zawsze włączone:

1. W Menedżerze konfiguracji programu SQL Server umożliwiają one **zawsze włączonych grup dostępności** dla **programu SQL Server (MSSQLSERVER)** usługi.

    ![Włącz funkcję AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/enable-alwayson.png)

2. One ponownie uruchomić usługę, aby zmiany zaczęły obowiązywać.

Za pomocą włączone (AlwaysOn) Contoso można skonfigurować grupy dostępności (AlwaysOn), która będzie chronić bazę danych SmartHotel.

**Potrzebujesz dodatkowej pomocy?**

- [Przeczytaj o](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) monitora i konfigurowania konta magazynu dla niego w chmurze.
- [Uzyskaj instrukcje](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial) Konfigurowanie klastra i tworzenia grupy dostępności.

## <a name="step-3-deploy-the-azure-load-balancer"></a>Krok 3: Wdrażanie modułu równoważenia obciążenia platformy Azure

Contoso można teraz wdrożyć wewnętrzny moduł równoważenia obciążenia, który znajduje się w miejscu dostępnym dla węzłów klastra. Moduł równoważenia obciążenia nasłuchuje ruchu i kieruje go do odpowiedniego węzła.

![Równoważenie obciążenia](media/contoso-migration-rehost-vm-sql-ag/architecture-lb.png)

Tworzą modułu równoważenia obciążenia w następujący sposób:

1. W witrynie Azure portal > **sieć** > **usługi Load Balancer**, konfigurowania nowego wewnętrznego modułu równoważenia obciążenia: **wewnętrznego modułu równoważenia obciążenia-PROD-DB-EUS2-SQLAOG**.
2. Ich umieszczenie modułu równoważenia obciążenia w sieci produkcyjnej **VNET-PROD-EUS2**, w podsieci bazy danych **PROD-DB-EUS2**.
3. On przypisać statyczny adres IP: 10.245.40.100.
4. Jako element sieci wdrażanych na moduł równoważenia obciążenia w grupie zasobów sieciowych **ContosoNetworkingRG**.

    ![Równoważenie obciążenia](media/contoso-migration-rehost-vm-sql-ag/lb-create.png)

Po wdrożeniu wewnętrznego modułu równoważenia obciążenia firmy Contoso należy je skonfigurować. One tworzenie puli adresów zaplecza, konfigurowanie sondy kondycji i konfigurowanie reguły równoważenia obciążenia.

### <a name="add-a-backend-pool"></a>Dodawanie puli zaplecza

Aby dystrybuować ruch do maszyn wirtualnych w klastrze, Contoso skonfiguruj pulę adresów zaplecza, która zawiera adresy IP kart sieciowych dla maszyn wirtualnych, które będą odbierać ruch sieciowy z modułu równoważenia obciążenia.

1. W obszarze Ustawienia modułu równoważenia obciążenia w portalu firmy Contoso Dodawanie puli zaplecza: **ILB-PROD-DB-EUS-SQLAOG-BEPOOL**.
2. Pula skojarzenia z zestawu dostępności SQLAOGAVSET. Maszyny wirtualne w zestawie (**SQLAOG1** i **SQLAOG2**) są dodawane do puli.

    ![Pula zaplecza](media/contoso-migration-rehost-vm-sql-ag/backend-pool.png)

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Contoso tworzy sondę kondycji, aby moduł równoważenia obciążenia można monitorować kondycję aplikacji. Dynamicznie dodaje lub usuwa maszyny wirtualne w rotacji modułu równoważenia obciążenia, w oparciu o sposobach reagowania do kontroli kondycji.

Sondy mogą utworzyć w następujący sposób: 

1. W obszarze Ustawienia modułu równoważenia obciążenia w portalu firmy Contoso utworzona sonda kondycji: **SQLAlwaysOnEndPointProbe**.
2. Ustawiają sondy do monitorowania maszyn wirtualnych na porcie TCP 59999.
3. Ustawiają interwału wynoszącego 5 sekund między sondy a próg liczby 2. W przypadku awarii dwóch sond, maszyna wirtualna zostanie uwzględniony w złej kondycji.

    ![Sondy](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

### <a name="configure-the-load-balancer-to-receive-traffic"></a>Konfigurowanie modułu równoważenia obciążenia mógł odbierać ruch


Teraz firmy Contoso musi regułę modułu równoważenia obciążenia do defins, sposób dystrybucji ruchu do maszyn wirtualnych.

- Adres IP frontonu obsługuje ruch przychodzący.
- Pula adresów IP zaplecza odbiera ruch.

Reguły mogą utworzyć w następujący sposób:

1. W obszarze Ustawienia modułu równoważenia obciążenia w portalu dodają nowe reguły równoważenia obciążenia: **SQLAlwaysOnEndPointListener**.
2. Contoso ustawia frontonu odbiornika do odbierania ruchu przychodzącego klienta SQL na porcie TCP 1433.
3. Określają puli zaplecza, do których ruch będzie kierowany i port, na którym maszyny wirtualne nasłuchują ruchu.
4. Contoso umożliwia pływającego adresu IP (bezpośredni zwrot serwera). Zawsze jest to wymagane dla funkcji SQL AlwaysOn.

    ![Sondy](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

**Potrzebujesz dodatkowej pomocy?**

- [Zapoznaj się z omówieniem](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) modułu równoważenia obciążenia platformy Azure.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-basic-internal-portal) Tworzenie modułu równoważenia obciążenia.



## <a name="step-4-prepare-azure-for-the-site-recovery-service"></a>Krok 4: Przygotowywanie platformy Azure dla usługi Site Recovery

Oto składniki platformy Azure, firma Contoso potrzebuje do wdrożenia usługi Site Recovery:

- Sieć wirtualna, w którym zostaną umieszczone podczas ich tworzenia podczas pracy awaryjnej.
- Konto magazynu platformy Azure do przechowywania replikowanych danych. 
- Magazyn usługi Recovery Services na platformie Azure.

Mogą skonfigurować je w następujący sposób:

1.  Contoso już utworzono sieć/podsieć, używają usługi Site Recovery podczas ich [infrastruktury platformy Azure wdrożone](contoso-migration-rehost-vm-sql-ag.md).

    - Aplikacja SmartHotel jest aplikacji produkcyjnej i WEBVM zostaną zmigrowane do sieci platformy Azure środowiska produkcyjnego (sieć wirtualna-PROD-EUS2) w regionie wschodnie stany USA 2 podstawowym.
    - WEBVM zostaną umieszczone w grupie zasobów ContosoRG, który jest używany dla zasobów w środowisku produkcyjnym, i w środowisku produkcyjnym podsieci (PROD-FE-EUS2).

2. Contoso tworzy konto usługi Azure storage (contosovmsacc20180528) w regionie podstawowym.

    - Używają konto ogólnego przeznaczenia z magazynu w warstwie standardowa i replikacją LRS.
    - Konto musi być w tym samym regionie co magazyn.

    ![Site Recovery magazynu](media/contoso-migration-rehost-vm-sql-ag/asr-storage.png)

3. Za pomocą konta magazynu i sieci w miejscu magazynu usługi Recovery Services teraz utworzyć (**ContosoMigrationVault**) i umieścić go w **ContosoFailoverRG** grupę zasobów, w regionie podstawowym wschodnie stany USA 2 .

    ![Magazyn usługi Recovery Services](media/contoso-migration-rehost-vm-sql-ag/asr-vault.png)

**Potrzebujesz dodatkowej pomocy?**

[Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) konfigurowaniu platformy Azure dla usługi Site Recovery.


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Krok 4: Przygotowanie lokalnego wdrożenia oprogramowania VMware do odzyskiwania lokacji

Oto, co Contoso przygotowuje lokalnie:

- Konto vCenter server lub hosta vSphere ESXi, do zautomatyzowania odnajdowania maszyn wirtualnych.
- Konta, które umożliwia automatyczną instalację usługi mobilności na maszynach wirtualnych VMware, którą chcesz replikować.
- Lokalne ustawienia maszyny Wirtualnej, tak aby Contoso może połączyć się z replikowanej maszyny Wirtualnej platformy Azure po pracy awaryjnej.


### <a name="prepare-an-account-for-automatic-discovery"></a>Przygotowywanie konta do automatycznego odnajdowania

Usługa Site Recovery musi mieć dostęp do serwerów VMware w następujących celach:

- Automatyczne odnajdywanie maszyn wirtualnych. 
- Organizowanie replikacji, trybu failover i powrotu po awarii.
- Wymagane jest co najmniej konto tylko do odczytu. Wymagane jest konto, które można uruchamiać operacje, takie jak tworzenie i usuwanie dysków i włączanie na maszynach wirtualnych.

Contoso konfiguruje konto w następujący sposób:

1. Contoso tworzy rolę na poziomie vCenter.
2. Contoso następnie przypisuje wymagane uprawnienia tej roli.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Usługa Mobility musi być zainstalowana na każdej maszynie Wirtualnej.

- Site Recovery może zrobić wypychania automatycznego instalowania tego składnika, po włączeniu replikacji dla maszyny Wirtualnej.
- Wymagane jest konto, którego usługa Site Recovery umożliwia dostęp do maszyny Wirtualnej na potrzeby instalacji wypychanej. To konto można określić podczas konfigurowania replikacji w konsoli platformy Azure.
- Konto może być domeny lub lokalnego z uprawnieniami do instalowania na maszynie Wirtualnej.




### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Po włączeniu trybu failover firma Contoso chce nawiązać połączenie z maszynami wirtualnymi platformy Azure. Aby to zrobić, ich wykonaj następujące czynności przed migracją:

1. Aby uzyskać dostęp za pośrednictwem Internetu one:

 - Włącz protokół RDP na maszynie Wirtualnej w środowisku lokalnym przed włączeniem trybu failover
 - Upewnij się, że reguły TCP i UDP zostały dodane do **publicznych** profilu.
 - Sprawdź, czy RDP jest dozwolona w **zapory Windows** > **dozwolone aplikacje** we wszystkich profilach.
 
2. Aby uzyskać dostęp za pośrednictwem sieci VPN typu lokacja lokacja są:

 - Włącz protokół RDP na maszynie lokalnej.
 - Zezwalaj na RDP w **zapory Windows** -> **dozwolone aplikacje i funkcje**, aby uzyskać **domena i prywatne** sieci.
 - Ustaw zasady sieci SAN systemu operacyjnego na maszynie Wirtualnej w środowisku lokalnym **OnlineAll**.

Ponadto po uruchomieniu trybu failover muszą sprawdzenie następujących kwestii:

- Powinno być nie oczekujących aktualizacji Windows na maszynie Wirtualnej podczas wyzwalania trybu failover. Jeśli, nie będzie mógł zalogować się do maszyny Wirtualnej do momentu ukończenia aktualizacji.
- Po włączeniu trybu failover, można sprawdzić **diagnostykę rozruchu** Aby wyświetlić zrzut ekranu maszyny wirtualnej. Jeśli to nie rozwiąże problemu, należy sprawdzić czy maszyna wirtualna jest uruchomiona i przejrzyj te [wskazówki dotyczące rozwiązywania problemów](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Potrzebujesz dodatkowej pomocy?**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) tworzenie i przypisywanie roli do automatycznego odnajdowania.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) tworzenia konta dla instalacji wypychanej usługi mobilności.


## <a name="step-5-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>Krok 5: Replikowanie lokalnych maszyn wirtualnych do platformy Azure z usługą Site Recovery

Przed uruchomieniem przez nich migracji na platformę Azure, firma Contoso trzeba skonfigurować i włączyć replikację.

### <a name="set-a-replication-goal"></a>Ustaw cel replikacji

1. W magazynie w obszarze nazwy magazynu (ContosoVMVault) wybierają cel replikacji (**wprowadzenie** > **Site Recovery** > **Przygotowywanie infrastruktury** .
2. Określają, czy ich maszyn w środowisku lokalnym, działające w programie VMware oraz replikacji do platformy Azure.

    ![Cel replikacji](./media/contoso-migration-rehost-vm-sql-ag/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Potwierdzanie planowania wdrożenia

Aby kontynuować, należy potwierdzić, że został ukończony planowania wdrożenia, wybierając **tak, ma to zostało zrobione**. W tym scenariuszu firma Contoso są tylko migrowania maszyny Wirtualnej, a nie ma potrzeby planowania wdrożenia.

### <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Firma Contoso potrzebuje skonfigurować swoje środowisko źródłowe. Aby to zrobić, ich Pobierz szablon OVF i użyć go do wdrożenia serwera konfiguracji usługi Site Recovery w trybie wysokiej dostępności, lokalnej maszyny Wirtualnej VMware. Po skonfigurowaniu i uruchomieniu na serwerze konfiguracji mogą zarejestrować go w magazynie.

Serwer konfiguracji jest uruchamiana jest wiele składników:

- Składnik serwera konfiguracji, który służy do koordynowania komunikacji między lokalną i platformą Azure oraz do zarządzania replikacją danych.
- Serwer przetwarzania, który działa jako brama replikacji. Odbiera dane replikacji, optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do usługi Azure Storage.
- Serwer przetwarzania instaluje także usługę mobilności na maszynach wirtualnych, które będą replikowane, i automatycznie odnajduje lokalne maszyny wirtualne VMware.

Contoso wykonaj następujące kroki w następujący sposób:


1. W magazynie, będą oni mogli pobrać szablonu pakietu OVF z **Przygotuj infrastrukturę** > **źródła** > **serwera konfiguracji**.
    
    ![Pobieranie pakietu OVF](./media/contoso-migration-rehost-vm-sql-ag/add-cs.png)

2. One zaimportuj szablon do programu VMware, aby utworzyć i wdrożyć maszynę Wirtualną.

    ![Szablon OVF](./media/contoso-migration-rehost-vm-sql-ag/vcenter-wizard.png)

3. Po włączeniu maszyny Wirtualnej po raz pierwszy jej zostanie uruchomiona do środowiska instalacji systemu Windows Server 2016. Zaakceptuj Umowę licencyjną i wprowadź hasło administratora.
4. Po zakończeniu instalacji, logowania się do maszyny Wirtualnej jako administrator. Przy pierwszym logowaniu narzędzie konfiguracji usługi Azure Site Recovery uruchamia się domyślnie.
5. W narzędziu określają nazwy do rejestrowania serwera konfiguracji w magazynie.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia logowania do subskrypcji platformy Azure. Użyte poświadczenia muszą zapewniać dostęp do magazynu, w którym chcesz zarejestrować serwer konfiguracji.

    ![Rejestrowanie serwera konfiguracji](./media/contoso-migration-rehost-vm-sql-ag/config-server-register2.png)

7. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie.
8. Logowania z maszyną ponownie, a następnie automatycznie uruchomiony Kreator zarządzania serwerem konfiguracji.
9. W Kreatorze one wybierz kartę Sieciową, aby odbierać ruch związany z replikacją. Nie można zmienić to ustawienie, po skonfigurowaniu go.
10. Wybierają subskrypcji, grupy zasobów i Magazyn, w którym można zarejestrować serwer konfiguracji.
        ![Vault](./media/contoso-migration-rehost-vm-sql-ag/cswiz1.png) 

10. One, a następnie Pobierz i zainstaluj serwer MySQL, a program VMWare PowerCLI. 
11. Po zakończeniu walidacji określają nazwy FQDN lub adres IP vCenter server lub hoście vSphere. Pozostaw domyślny port i określ przyjazną nazwę dla serwera vCenter.
12. Określ ich konta, utworzonego do automatycznego odnajdowania i poświadczenia, które są używane do automatycznego instalowania usługi mobilności. W przypadku maszyn Windows konto wymaga uprawnień administratora lokalnego na maszynach wirtualnych.

    ![vCenter](./media/contoso-migration-rehost-vm-sql-ag/cswiz2.png)

7. Po zakończeniu rejestracji w witrynie Azure portal firmy Contoso double sprawdza, czy serwer konfiguracji i serwer VMware są wyświetlane na **źródła** strony w magazynie. Odnajdywanie może potrwać 15 minut lub dłużej. 
8. Usługa Site Recovery nawiąże połączenie z serwerami VMware przy użyciu podanych ustawień i odnajdzie maszyny wirtualne.

### <a name="set-up-the-target"></a>Konfigurowanie obiektu docelowego

Teraz Contoso określa ustawienia replikacji obiektu docelowego.

1. W **przygotowanie infrastruktury** > **docelowej**, wybierają ustawienia wartości docelowej.
2. Usługa Site Recovery sprawdza, czy istnieje konto usługi Azure storage i sieci w określonej lokalizacji docelowej.

### <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

Nie, Contoso, można utworzyć zasad replikacji.

1. W **przygotowanie infrastruktury** > **ustawień replikacji** > **zasad replikacji** >  **Utwórz i Skojarz**, tworzą zasadę **ContosoMigrationPolicy**.
2. Używają domyślnych ustawień:
    - **Próg celu punktu odzyskiwania**: domyślnej wartości 60 minut. Ta wartość określa częstość tworzenia punktów odzyskiwania. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
    - **Czas przechowywania punktu odzyskiwania**. Domyślnej wartości 24 godzin. Ta wartość określa, jak długo trwa okno przechowywania dla każdego punktu odzyskiwania. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w tym oknie.
    - **Częstotliwość migawek spójności aplikacji**. Domyślnie co godzinę. Ta wartość określa częstotliwość, z jaką są tworzone migawki spójne z aplikacjami.
 
        ![Tworzenie zasad replikacji](./media/contoso-migration-rehost-vm-sql-ag/replication-policy.png)

5. Zasady zostaną automatycznie skojarzone z serwerem konfiguracji. 

    ![Skojarz zasady replikacji](./media/contoso-migration-rehost-vm-sql-ag/replication-policy2.png)



### <a name="enable-replication"></a>Włączanie replikacji

Teraz firma Contoso może rozpocząć replikowanie WebVM.

1. W **Replikowanie aplikacji** > **źródła** > **+ Replikuj** wybierają ustawienia źródła.
2. Wskazują, że chce włączyć maszyn wirtualnych, wybierz serwer vCenter, a serwer konfiguracji.

    ![Włączanie replikacji](./media/contoso-migration-rehost-vm-sql-ag/enable-replication1.png)

3. Teraz określają ustawienia docelowego, łącznie z grupy zasobów i sieć wirtualna i konta magazynu, w którym przechowywane będą replikowane dane.

     ![Włączanie replikacji](./media/contoso-migration-rehost-vm-sql-ag/enable-replication2.png)

3. Contoso zaznacza WebVM replikacji, sprawdza, czy zasady replikacji oraz włączania replikacji. Usługa Site Recovery instaluje usługę mobilności na maszynie Wirtualnej, po włączeniu replikacji.
 
    ![Włączanie replikacji](./media/contoso-migration-rehost-vm-sql-ag/enable-replication3.png)

4. Śledzą postęp replikacji w **zadań**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.
5. W **Essentials** w witrynie Azure portal firmy Contoso można wyświetlić jego strukturę dla maszyn wirtualnych replikowanych do platformy Azure.

    ![Widok infrastruktury](./media/contoso-migration-rehost-vm-sql-ag/essentials.png)


**Potrzebujesz dodatkowej pomocy?**

- Może odczytywać kompletnym instruktażem wszystkie te kroki [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych VMware w środowisku lokalnym](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Szczegółowe instrukcje są dostępne ułatwić [Konfigurowanie środowiska źródłowego](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [wdrażanie serwera konfiguracji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), i [Konfigurowanie ustawień replikacji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- Możesz dowiedzieć się więcej [włączenie replikacji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-5-install-the-database-migration-assistant-dma"></a>Krok 5: Zainstaluj Asystenta migracji bazy danych (DMA)

Contoso zostaną zmigrowane SmartHotel bazy danych do maszyny Wirtualnej platformy Azure **SQLAOG1** przy użyciu narzędzia DMA. One skonfigurowane DMA w następujący sposób:

1. Będą oni mogli pobrać narzędzia z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) do maszyny Wirtualnej w środowisku lokalnym SQL Server (**SQLVM**).
2. Instalatora (DownloadMigrationAssistant.msi), są one uruchomione na maszynie Wirtualnej.
3. Na **Zakończ** strony, wybierają **Uruchom program Microsoft Data Migration Assistant** przed zakończeniem pracy kreatora.

## <a name="step-6-migrate-the-database-with-dma"></a>Krok 6: Migrację bazy danych za pomocą programu DMA

1. W narzędziu DMA działają nowej migracji - **SmartHotel**.
2. Wybierają **typ serwera docelowego** jako **programu SQL Server na maszynach wirtualnych Azure**. 

    ![PROGRAM DMA](media/contoso-migration-rehost-vm-sql-ag/dma-1.png)

3. Informacje dotyczące migracji, co zwiększa **SQLVM** co serwer źródłowy i **SQLAOG1** jako element docelowy. One Określ poświadczenia dla poszczególnych maszyn.

     ![PROGRAM DMA](media/contoso-migration-rehost-vm-sql-ag/dma-2.png)

4. Tworzą lokalny udział informacji bazy danych i konfiguracji. Musi być dostępny z dostępem do zapisu dla konta usługi SQL na SQLVM i SQLAOG1.

    ![PROGRAM DMA](media/contoso-migration-rehost-vm-sql-ag/dma-3.png)

5. Contoso wybiera dane logowania, które mają zostać poddane migracji, a następnie rozpoczyna migrację. Po zakończeniu program DMA pokazuje migracji pomyślne.

    ![PROGRAM DMA](media/contoso-migration-rehost-vm-sql-ag/dma-4.png)

6. Sprawdź, czy baza danych jest uruchomiona na **SQLAOG1**.

    ![PROGRAM DMA](media/contoso-migration-rehost-vm-sql-ag/dma-5.png)

Usługa DMS łączy maszynę Wirtualną w środowisku lokalnym SQL Server przez połączenie VPN lokacja lokacja między centrum danych firmy Contoso i platformą Azure, a następnie przeprowadza migrację bazy danych.

## <a name="step-7-protect-the-database"></a>Krok 7: Ochrony bazy danych

Z bazą danych aplikacji uruchomionych na **SQLAOG1**, Contoso mogą teraz chronić go przy użyciu zawsze włączonych grup dostępności. Ich konfigurowanie zawsze włączonych za pomocą programu SQL Management Studio, a następnie przypisz odbiornik korzystania z klastra Windows. 

### <a name="create-an-alwayson-availability-group"></a>Tworzenie grupy dostępności AlwaysOn

1. W programie SQL Management Studio, mogą kliknąć prawym przyciskiem myszy **zawsze na wysoką dostępność** można uruchomić **Kreatora nowej grupy dostępności**.
2. W **Określ opcje**, ich nazwa grupy dostępności **SHAOG**. W **wybierz bazy danych**, wybierają SmartHotel bazy danych.

    ![Zawsze włączonej grupy dostępności](media/contoso-migration-rehost-vm-sql-ag/aog-1.png)

3. W **Określanie replik**, dodaj dwa węzły SQL jako repliki dostępności i skonfigurować je, aby zapewnić automatyczną pracę awaryjną zatwierdzania synchronicznego.

     ![Zawsze włączonej grupy dostępności](media/contoso-migration-rehost-vm-sql-ag/aog-2.png)

4. Skonfigurowana odbiornika dla grupy (**SHAOG**) i portu. Adres IP wewnętrznego modułu równoważenia obciążenia jest dodawany jako statyczny adres IP (10.245.40.100).

    ![Zawsze włączonej grupy dostępności](media/contoso-migration-rehost-vm-sql-ag/aog-3.png)

5. W **wybierz synchronizację danych**, umożliwiają one automatyczne wstępne wypełnianie. Po wybraniu tej opcji programu SQL Server automatycznie tworzy repliki pomocnicze dla każdej bazy danych w grupie, więc firmy Contoso, nie trzeba ręcznie utworzyć kopię zapasową i przywrócić je. Po zakończeniu walidacji Tworzenie grupy dostępności.

    ![Zawsze włączonej grupy dostępności](media/contoso-migration-rehost-vm-sql-ag/aog-4.png)

6. Contoso napotkał problem podczas tworzenia grupy. Nie korzystają z zabezpieczeń Windows zintegrowane usługi Active Directory i dlatego trzeba udzielać uprawnień do logowania SQL, aby utworzyć role klastra pracy awaryjnej Windows.

    ![Zawsze włączonej grupy dostępności](media/contoso-migration-rehost-vm-sql-ag/aog-5.png)

6. Po utworzeniu grupy Contoso mogli je zobaczyć w programie SQL Management Studio.

### <a name="configure-a-listener-on-the-cluster"></a>Skonfiguruj odbiornik w klastrze

Jako ostatni etap konfigurowania wdrożenia SQL firmy Contoso konfiguruje wewnętrznego modułu równoważenia obciążenia jako odbiornik w klastrze i udostępnia odbiornika w trybie online. W tym celu mogą użyć skryptu.

![Odbiornik klastra](media/contoso-migration-rehost-vm-sql-ag/cluster-listener.png)


### <a name="verify-the-configuration"></a>Sprawdź, czy konfiguracja

Wszystko, konfigurowanie Contoso powstał grupy funkcjonalnej dostępności na platformie Azure, która używa zmigrowana baza danych. Są to sprawdzić przez nawiązanie połączenia wewnętrznego modułu równoważenia obciążenia w programie SQL Management Studio.

![Łączenie z wewnętrznym modułem równoważenia obciążenia](media/contoso-migration-rehost-vm-sql-ag/ilb-connect.png)

**Potrzebujesz dodatkowej pomocy?**
- Dowiedz się więcej o tworzeniu [grupy dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#create-the-availability-group) i [odbiornika](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#configure-listener).
- Ręcznie [skonfigurowany klaster do używania adresu IP modułu równoważenia obciążenia](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener#configure-the-cluster-to-use-the-load-balancer-ip-address).
- [Dowiedz się więcej](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2) dotyczących tworzenia i używania sygnatur dostępu Współdzielonego.


## <a name="step-8-migrate-the-vm-with-site-recovery"></a>Krok 8: Migrowanie maszyny Wirtualnej z usługą Site Recovery

Contoso uruchomić szybkie testowanie trybu failover, a następnie przeprowadzić migrację maszyny Wirtualnej.

### <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Uruchamianie testu trybu failover pomaga zapewnić, że wszystko działa zgodnie z oczekiwaniami przed migracją. 

1. Contoso uruchamia test trybu failover do najnowszego dostępnego punktu w czasie (**najnowszy przetworzony**).
2. Wybierają **Zamknij maszynę przed rozpoczęciem pracy awaryjnej**, dzięki czemu usługa Site Recovery próbuje zamknąć źródłową maszynę Wirtualną przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. 
3. Testy trybu failover: 

    - Sprawdzanie wymagań wstępnych, przebiegów, aby upewnić się, że wszystkie warunki wymagane do migracji w miejscu.
    - Tryb failover przetwarza dane, aby umożliwić utworzenie maszyny wirtualnej platformy Azure. Jeśli zostanie wybrany najnowszy punkt odzyskiwania, punkt odzyskiwania zostanie utworzony na podstawie danych.
    - Tworzona jest maszyna wirtualna platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.

3. Po zakończeniu trybu failover w witrynie Azure portal pojawi się repliki maszyn wirtualnych platformy Azure. Contoso sprawdza, czy maszyna wirtualna jest odpowiedni rozmiar, który jest połączony z odpowiednią siecią i czy działa. 
4. Po zweryfikowaniu, Contoso wyczyścić pracy awaryjnej, Zarejestruj i zapisz wszelkie obserwacje. 

### <a name="run-a-failover"></a>Uruchamianie trybu failover

1. Po sprawdzeniu, czy test trybu failover zadziałała zgodnie z oczekiwaniami, Contoso utworzyć plan odzyskiwania do migracji i Dodaj WEBVM zgodnie z planem.

     ![Plan odzyskiwania](./media/contoso-migration-rehost-vm-sql-ag/recovery-plan.png)

2. Działają one przejścia w tryb failover dla planu. Wybierz najnowszy punkt odzyskiwania i określ, czy usługa Site Recovery należy dążyć do zamknięcia lokalną maszynę Wirtualną przed wyzwoleniem trybu failover.

    ![Tryb failover](./media/contoso-migration-rehost-vm-sql-ag/failover1.png)

3. Po włączeniu trybu failover ich Sprawdź, czy maszyna wirtualna platformy Azure znajduje się w oczekiwany sposób w witrynie Azure portal.

    ![Plan odzyskiwania](./media/contoso-migration-rehost-vm-sql-ag/failover2.png)

6. Po zweryfikowaniu maszyny Wirtualnej na platformie Azure, mogą przeprowadzić migrację do zakończenia procesu migracji, zatrzymanie replikacji maszyny Wirtualnej i zatrzymać rozliczanie Site Recovery dla maszyny Wirtualnej.

    ![Tryb failover](./media/contoso-migration-rehost-vm-sql-ag/failover3.png)

### <a name="update-the-connection-string"></a>Zaktualizuj parametry połączenia

Ostatni krok w procesie migracji Contoso zaktualizować parametry połączenia aplikacji, aby wskazywał zmigrowana baza danych, systemem odbiornika SHAOG. Ta konfiguracja zostanie zmieniony na WEBVM teraz działających na platformie Azure.  Tej konfiguracji znajduje się w pliku web.config aplikacji ASP. 

1. Zlokalizuj plik w rozmiarze C:\inetpub\SmartHotelWeb\web.config.  Zmień nazwę serwera, aby odzwierciedlić nazwę FQDN AOG: shaog.contoso.com.

    ![Tryb failover](./media/contoso-migration-rehost-vm-sql-ag/failover4.png)  

2. Po aktualizowania pliku i zapisanie go, ich ponowne uruchomienie usług IIS na WEBVM. Mogą to zrobić za pomocą /RESTART polecenie IISRESET w wierszu polecenia.
2. Po ponownym uruchomieniu usług IIS aplikacji teraz używa bazy danych, systemem wystąpienia Zarządzanego SQL.


**Potrzebujesz dodatkowej pomocy?**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) Uruchamianie testu trybu failover. 
- [Dowiedz się,](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) Tworzenie planu odzyskiwania.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) przechodzenia w tryb failover na platformie Azure.

## <a name="clean-up-after-migration"></a>Wyczyść zasoby po ukończeniu migracji

Po zakończeniu migracji aplikacja SmartHotel jest uruchomiona na Maszynie wirtualnej platformy Azure, a SmartHotel baza danych znajduje się w klastrze usługi Azure SQL.

Teraz firma Contoso potrzebuje do wykonania tych kroków czyszczenia:  

- Usuń lokalne maszyny wirtualne z magazynu programu vCenter.
- Usuń maszyny wirtualne z lokalnego zadania tworzenia kopii zapasowej.
- Zaktualizowanie dokumentacji wewnętrzny, aby wyświetlić nowe lokalizacje i adresy IP dla maszyn wirtualnych.
- Przejrzyj wszystkie zasoby, które wchodzić w interakcje z wycofany maszynami wirtualnymi i zaktualizuj wszelkie odpowiednie ustawienia lub dokumentacji, aby odzwierciedlić nową konfigurację.
- Dodaj dwa nowe maszyny wirtualne (SQLAOG1 i SQLAOG2) powinna być dodana do monitorowania systemów w środowisku produkcyjnym.

## <a name="review-the-deployment"></a>Przegląd wdrożenia

Z migrowanych zasobów na platformie Azure firma Contoso potrzebuje do w pełni operacjonalizacji i zabezpieczanie ich nowej infrastruktury.

### <a name="security"></a>Bezpieczeństwo

Zespół ds. zabezpieczeń Contoso przegląda WEBVM maszyn wirtualnych platformy Azure, SQLAOG1 i SQLAOG2 określić wszelkie problemy z zabezpieczeniami. 

- Sieciowe grupy zabezpieczeń (NSG) przeglądu dla maszyny Wirtualnej w celu kontrolowania dostępu. Sieciowe grupy zabezpieczeń są używane do zapewnienia, że można przekazać tylko ruch do aplikacji.
- Rozważanych zabezpieczania danych na dysku za pomocą usługi Azure Disk Encryption i magazynu kluczy.
- Powinny ocena technologii transparent data encryption (TDE) i włącz je w bazie danych SmartHotel, uruchomionej na nowe AOG SQL. [Dowiedz się więcej](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017).

[Dowiedz się więcej](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) o rozwiązania w zakresie zabezpieczeń dla maszyn wirtualnych.

### <a name="backups"></a>Tworzenie kopii zapasowych

Firma Contoso zamierza kopii zapasowej danych na WEBVM, SQLAOG1 i SQLAOG2 za pomocą usługi Azure Backup. [Dowiedz się więcej](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Optymalizacja licencjonowania i kosztów

1. Firma Contoso ma istniejącej licencji dla ich WEBVM i będzie korzystać z korzyści użycia hybrydowego platformy Azure.  Będziesz ich konwersji istniejących maszyn wirtualnych platformy Azure, aby móc korzystać z tej ceny.
2. Contoso — spowoduje to włączenie usługi Azure Cost Management licencjonowana przez firmę Cloudyn, podmiot zależny firmy Microsoft. To rozwiązanie do zarządzania kosztami wielu chmur, które ułatwia najwydajniejsze korzystanie z oraz zarządzania platformą Azure i innych zasobów w chmurze.  [Dowiedz się więcej](https://docs.microsoft.com/azure/cost-management/overview) o usłudze Azure Cost Management. 

## <a name="conclusion"></a>Podsumowanie

W tym artykule Contoso rehosted aplikacji SmartHotel na platformie Azure przy użyciu funkcji migracji aplikacji frontonu maszyn wirtualnych na platformie Azure przy użyciu usługi Site Recovery. Migracja bazy danych aplikacji do klastra programu SQL Server aprowizowane na platformie Azure i chroniony w grupie dostępności AlwaysOn programu SQL Server.

## <a name="next-steps"></a>Kolejne kroki

W następnym artykule z tej serii firma Microsoft pokazano, jak Contoso ponowne hostowanie aplikacji osTicket technicznej usługi działającej w systemie Linux i wdrożona za pomocą bazy danych MySQL.


