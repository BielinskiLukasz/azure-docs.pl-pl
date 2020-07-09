---
title: Odnajdywanie, ocenianie i migrowanie maszyn wirtualnych usługi Amazon Web Services (AWS) na platformę Azure
description: W tym artykule opisano sposób migrowania maszyn wirtualnych AWS na platformę Azure przy użyciu Azure Migrate.
ms.topic: tutorial
ms.date: 06/16/2020
ms.custom: MVC
ms.openlocfilehash: 5d697c2146144ca7f4b9a8739b6863ba31845f4e
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165434"
---
# <a name="discover-assess-and-migrate-amazon-web-services-aws-vms-to-azure"></a>Odnajdywanie, ocenianie i migrowanie maszyn wirtualnych usługi Amazon Web Services (AWS) na platformę Azure

W tym samouczku pokazano, jak odkrywać, oceniać i migrować maszyny wirtualne Amazon Web Services (AWS) do maszyn wirtualnych platformy Azure przy użyciu Azure Migrate: narzędzia do oceny serwera i migracji serwera

> [!NOTE]
> Podczas migrowania maszyn wirtualnych AWS na platformę Azure maszyny wirtualne są traktowane jak serwery fizyczne. W celu migrowania maszyn fizycznych za pomocą przepływu migracji serwera można migrować maszyny wirtualne AWS na platformę Azure.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Sprawdź wymagania wstępne dotyczące migracji.
> * Przygotowywanie zasobów platformy Azure za pomocą Azure Migrate: Migracja serwera. Skonfiguruj uprawnienia dla konta i zasobów platformy Azure do pracy z Azure Migrate.
> * Przygotuj wystąpienia usługi AWS EC2 do migracji.
> * Dodaj Azure Migrate: Narzędzia migracji serwera w centrum Azure Migrate.
> * Skonfiguruj urządzenie do replikacji i Wdróż serwer konfiguracji.
> * Zainstaluj usługę mobilności na maszynach wirtualnych AWS, które chcesz zmigrować.
> * Włączanie replikacji maszyn wirtualnych
> * Śledź i monitoruj stan replikacji. 
> * Uruchom migrację testową, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
> * Uruchom pełną migrację na platformę Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="discover-and-assess-aws-vms"></a>Odkrywanie i ocenianie maszyn wirtualnych AWS  

Przed przeprowadzeniem migracji na platformę Azure zalecamy wykonanie funkcji odnajdywania maszyn wirtualnych i oceny migracji. Ta ocena pomaga w wykorzystaniu odpowiedniego rozmiaru maszyn wirtualnych AWS na potrzeby migracji na platformę Azure i oszacowania potencjalnych kosztów uruchamiania platformy Azure.

Skonfiguruj ocenę w następujący sposób:

1. Ocenę można przeprowadzić przez traktowanie maszyn wirtualnych AWS jako maszyn fizycznych na potrzeby przeprowadzania oceny przy użyciu narzędzia do oceny serwera Azure Migrate:. Postępuj zgodnie z [samouczkiem](./tutorial-prepare-physical.md) , aby skonfigurować platformę Azure i przygotować maszyny wirtualne AWS do oceny.
2. Następnie postępuj zgodnie z tym [samouczkiem](./tutorial-assess-physical.md) , aby skonfigurować Azure Migrate projekt i urządzenie w celu odnajdywania i oceniania maszyn wirtualnych AWS.

Chociaż zalecamy wypróbowanie oceny, wykonanie oceny nie jest obowiązkowym krokiem, aby móc migrować maszyny wirtualne.

## <a name="migrate-aws-vms"></a>Migrowanie maszyn wirtualnych AWS   

## <a name="1-prerequisites-for-migration"></a>1. wymagania wstępne dotyczące migracji

- Upewnij się, że maszyny wirtualne AWS, które chcesz zmigrować, mają obsługiwaną wersję systemu operacyjnego. Maszyny wirtualne AWS są traktowane jak maszyny fizyczne na potrzeby migracji. Zapoznaj się z [obsługiwanymi systemami operacyjnymi](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) dla przepływu pracy migracji serwera fizycznego. Zalecamy wykonanie migracji testowej (test pracy w trybie failover), aby sprawdzić, czy maszyna wirtualna działa zgodnie z oczekiwaniami przed kontynuowaniem rzeczywistej migracji.
- Upewnij się, że maszyny wirtualne AWS są zgodne z [obsługiwanymi konfiguracjami](./migrate-support-matrix-physical-migration.md#physical-server-requirements) migracji na platformę Azure.
- Sprawdź, czy maszyny wirtualne AWS replikowane na platformę Azure są zgodne z [wymaganiami maszyny wirtualnej platformy Azure.](./migrate-support-matrix-physical-migration.md#azure-vm-requirements)
- Przed przeprowadzeniem migracji na platformę Azure potrzebne są pewne zmiany na maszynach wirtualnych.
    - W niektórych systemach operacyjnych Azure Migrate automatycznie wprowadza te zmiany.
    - Ważne jest, aby wprowadzić te zmiany przed rozpoczęciem migracji. W przypadku migrowania maszyny wirtualnej przed wprowadzeniem zmiany, maszyna wirtualna może nie zostać uruchomiona na platformie Azure.
Przejrzyj zmiany w [systemach Windows](prepare-for-migration.md#windows-machines) i [Linux](prepare-for-migration.md#linux-machines) , które należy wprowadzić.

## <a name="2-prepare-azure-resources-for-migration"></a>2. Przygotuj zasoby platformy Azure do migracji

Przygotuj platformę Azure do migracji przy użyciu Azure Migrate: Narzędzia migracji serwera.

**Zadanie** | **Szczegóły**
--- | ---
**Tworzenie projektu Azure Migrate** | Twoje konto platformy Azure musi mieć uprawnienia współautora lub właściciela, aby utworzyć projekt.
**Weryfikowanie uprawnień do konta platformy Azure** | Twoje konto platformy Azure wymaga uprawnień do utworzenia maszyny wirtualnej i zapisu na dysku zarządzanym platformy Azure.

### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu

1. W Azure Portal Otwórz subskrypcję i wybierz pozycję **Kontrola dostępu (IAM)**.
2. W obszarze **Sprawdź dostęp**Znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.
3. Należy mieć uprawnienia **współautora** lub **właściciela** .
    - Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać rolę.

### <a name="assign-azure-account-permissions"></a>Przypisywanie uprawnień konta platformy Azure

Przypisz rolę współautora maszyny wirtualnej do konta platformy Azure. Zapewnia to następujące uprawnienia:

- Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
- Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
- Zapisz na dysku zarządzanym platformy Azure. 

### <a name="create-an-azure-network"></a>Tworzenie sieci platformy Azure

[Skonfiguruj](../virtual-network/manage-virtual-network.md#create-a-virtual-network) sieć wirtualną platformy Azure. Podczas replikowania na platformę Azure utworzone maszyny wirtualne platformy Azure są przyłączone do sieci wirtualnej platformy Azure, która została określona podczas konfigurowania migracji.

## <a name="3-prepare-aws-instances-for-migration"></a>3. Przygotuj wystąpienia AWS na potrzeby migracji

Aby przygotować się do migracji do AWS do platformy Azure, należy przygotować i wdrożyć urządzenie replikacji na potrzeby migracji.

### <a name="prepare-a-machine-for-the-replication-appliance"></a>Przygotuj komputer do urządzenia replikacji

Azure Migrate: Migracja serwera używa urządzenia replikacji do replikowania maszyn na platformę Azure. Urządzenie replikacji uruchamia następujące składniki.

- **Serwer konfiguracji**: serwer konfiguracji koordynuje komunikację między środowiskiem AWS i platformą Azure oraz zarządza replikacją danych.
- **Serwer przetwarzania**: serwer przetwarzania działa jako brama replikacji. Odbiera dane replikacji, optymalizuje je za pomocą buforowania, kompresji i szyfrowania, a następnie wysyła je do konta magazynu pamięci podręcznej na platformie Azure.

Przygotuj się do wdrożenia urządzenia w następujący sposób:

- Skonfiguruj oddzielną maszynę wirtualną EC2 do hostowania urządzenia replikacji. To wystąpienie musi działać pod kontrolą systemu Windows Server 2012 R2 lub Windows Server 2016. [Zapoznaj](./migrate-replication-appliance.md#appliance-requirements) się z wymaganiami dotyczącymi sprzętu, oprogramowania i sieci dla urządzenia.
- Urządzenia nie należy instalować na źródłowej maszynie wirtualnej, która ma być replikowana. Należy ją wdrożyć na innej maszynie wirtualnej.
- Źródłowe maszyny wirtualne AWS, które mają zostać zmigrowane, powinny mieć sieć linii wglądu do urządzenia replikacji. Skonfiguruj niezbędne reguły grupy zabezpieczeń, aby je włączyć. Zaleca się, aby urządzenie replikacji zostało wdrożone w tym samym VPC co źródłowe maszyny wirtualne do migracji. Jeśli urządzenie replikacji musi znajdować się w innym VPC, VPCs muszą być połączone za pomocą komunikacji równorzędnej VPC.
- Źródłowe maszyny wirtualne AWS komunikują się z urządzeniem replikacji na portach HTTPS 443 (aranżacja kanału kontroli) i TCP 9443 (transport danych) przychodzących na potrzeby zarządzania replikacją i transferu danych replikacji. Urządzenie replikacji z kolei organizuje i wysyła dane replikacji do platformy Azure przez port HTTPS 443 dla ruchu wychodzącego. Aby skonfigurować te reguły, Edytuj reguły ruchu przychodzącego/wychodzącego grupy zabezpieczeń przy użyciu odpowiednich portów i źródłowych informacji o adresie IP.

   ![AWS grupy zabezpieczeń ](./media/tutorial-migrate-aws-virtual-machines/aws-security-groups.png)
     
 
   ![Edytuj ustawienia zabezpieczeń ](./media/tutorial-migrate-aws-virtual-machines/edit-security-settings.png)

- Urządzenie do replikacji używa programu MySQL. Zapoznaj się z [opcjami](migrate-replication-appliance.md#mysql-installation) instalacji bazy danych MySQL na urządzeniu.
- Przejrzyj adresy URL platformy Azure wymagane przez urządzenie replikacji, aby uzyskać dostęp do chmur [publicznych](migrate-replication-appliance.md#url-access) i [instytucji rządowych](migrate-replication-appliance.md#azure-government-url-access) .

## <a name="4-add-the-server-migration-tool"></a>4. Dodaj narzędzie do migracji serwera

Skonfiguruj projekt Azure Migrate, a następnie Dodaj do niego narzędzie do migracji serwera.

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.
3. W obszarze **Przegląd** kliknij pozycję **Ocena i migracja serwerów**.
4. W obszarze **odnajdywanie, ocenianie i Migrowanie serwerów**kliknij pozycję **Oceń i Przeprowadź migrację serwerów**.

    ![Odnajdywanie i ocenianie serwerów](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. W obszarze **Odnajdywanie, ocena i migracja serwerów** kliknij pozycję **Dodaj narzędzia**.
6. W obszarze **Projekt migracji**wybierz subskrypcję platformy Azure i utwórz grupę zasobów, jeśli jej nie masz.
7. W obszarze **Szczegóły projektu** określ nazwę projektu i lokalizację geograficzną, w której chcesz utworzyć projekt, a następnie kliknij przycisk **Dalej**. Przejrzyj obsługiwane lokalizacje geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [instytucji rządowych](migrate-support-matrix.md#supported-geographies-azure-government).
    - Lokalizacja geograficzna projektu służy tylko do przechowywania metadanych zebranych z maszyn AWS.
    - Podczas przeprowadzania migracji można wybrać dowolny region docelowy.

    ![Tworzenie projektu Azure Migrate](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

8. W **narzędziu Wybierz ocenę**wybierz pozycję **Pomiń Dodawanie narzędzia do oceny teraz**  >  **Next**.
9. W obszarze **Wybieranie narzędzia migracji**wybierz **Azure Migrate: Migracja serwera**  >  **dalej**.
10. W obszarze **Przegląd i dodawanie narzędzi** przejrzyj ustawienia, a następnie kliknij pozycję **Dodaj narzędzia**
11. Po dodaniu narzędzia pojawia się ono w Azure Migrate **Servers**  >  **Narzędzia migracji**serwerów > projektu.

## <a name="5-set-up-the-replication-appliance"></a>5. Konfigurowanie urządzenia do replikacji

Pierwszym krokiem migracji jest skonfigurowanie urządzenia do replikacji. Aby skonfigurować urządzenie do migracji maszyn wirtualnych AWS, należy pobrać plik Instalatora dla urządzenia, a następnie uruchomić go na [przygotowanej maszynie wirtualnej](#prepare-a-machine-for-the-replication-appliance).

### <a name="download-the-replication-appliance-installer"></a>Pobierz Instalatora urządzenia replikacji

1. Na **serwerach**Azure Migrate Project >, w **Azure Migrate: Migracja serwera**, kliknij przycisk **odkryj**.

    ![Odnajdywanie maszyn wirtualnych](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

2. W obszarze **odnajdywanie**maszyn  >  **są zwirtualizowane maszyny?** kliknij pozycję **niezwirtualizowane/inne**.
3. W **obszarze region docelowy**wybierz region świadczenia usługi Azure, do którego chcesz przeprowadzić migrację maszyn.
4. Wybierz pozycję **Potwierdź, że docelowy region migracji to <regionu>nazwa **.
5. Kliknij pozycję **Utwórz zasoby**. Spowoduje to utworzenie magazynu Azure Site Recovery w tle.
    - Jeśli już skonfigurowano migrację z Azure Migrate migracji do serwera, nie można skonfigurować opcji docelowej, ponieważ zasoby zostały wcześniej skonfigurowane.
    - Po kliknięciu tego przycisku nie można zmienić regionu docelowego dla tego projektu.
    - Aby przeprowadzić migrację maszyn wirtualnych do innego regionu, należy utworzyć nowy/inny projekt Azure Migrate.

6. W **czy chcesz zainstalować nowe urządzenie do replikacji?** wybierz pozycję **Zainstaluj urządzenie replikacji**.
7. W obszarze **pobieranie i instalowanie oprogramowania do replikacji urządzenia**Pobierz Instalatora urządzenia i klucz rejestracji. Aby zarejestrować urządzenie, musisz być kluczem. Klucz jest ważny przez pięć dni od jego pobrania.

    ![Pobierz dostawcę](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

8. Skopiuj plik Instalatora urządzenia i plik klucza do maszyny wirtualnej systemu Windows Server 2016 lub Windows Server 2012 AWS utworzonej dla urządzenia replikacji.
9. Uruchom plik instalacyjny urządzenia replikacji, zgodnie z opisem w następnej procedurze.  
    9.1. W obszarze **Przed rozpoczęciem** wybierz pozycję **Zainstaluj serwer konfiguracji i serwer przetwarzania**, a następnie wybierz opcję **Dalej**.   
    9,2 w **licencji na oprogramowanie innych**firm wybierz opcję **Akceptuję Umowę licencyjną innej firmy**, a następnie wybierz przycisk **dalej**.   
    9,3 w obszarze **rejestracja**wybierz pozycję **Przeglądaj**, a następnie przejdź do lokalizacji, w której zostanie umieszczony plik klucza rejestracji magazynu. Wybierz opcję **Dalej**.  
    9,4 w obszarze **Ustawienia internetowe**wybierz opcję **Połącz z Azure Site Recovery bez serwera proxy**, a następnie wybierz przycisk **dalej**.  
    9,5 na stronie **Sprawdzanie wymagań wstępnych** są uruchamiane sprawdzenia kilku elementów. Po zakończeniu wybierz opcję **Dalej**.  
    9,6 w obszarze **Konfiguracja programu MySQL**Podaj hasło dla bazy danych MySQL, a następnie wybierz przycisk **dalej**.  
    9,7 w obszarze **szczegóły środowiska**wybierz pozycję **nie**. Nie musisz chronić maszyn wirtualnych. Następnie wybierz przycisk **dalej**.  
    9,8 w **lokalizacji instalacji**wybierz pozycję **dalej** , aby zaakceptować wartość domyślną.  
    9,9 w **obszarze Wybór sieci**wybierz pozycję **dalej** , aby zaakceptować wartość domyślną.  
    9,10 w obszarze **Podsumowanie**wybierz pozycję **Zainstaluj**.   
    9,11 **postęp instalacji** pokazuje informacje o procesie instalacji. Po zakończeniu wybierz opcję **Zakończ**. Zostanie wyświetlone okno z komunikatem o konieczności ponownego uruchomienia. Wybierz pozycję **OK**.   
    9,12 następnie w oknie zostanie wyświetlony komunikat dotyczący hasła połączenia z serwerem konfiguracji. Skopiuj hasło do schowka i Zapisz hasło w tymczasowym pliku tekstowym na źródłowych maszynach wirtualnych. To hasło będzie potrzebne później, podczas procesu instalacji usługi mobilności.
10. Po zakończeniu instalacji Kreator konfiguracji urządzenia zostanie uruchomiony automatycznie (można również uruchomić Kreatora ręcznie przy użyciu skrótu cspsconfigtool utworzonego na pulpicie urządzenia). Za pomocą karty Zarządzanie kontami kreatora można dodać szczegóły konta do użycia podczas instalacji wypychanej usługi mobilności. W tym samouczku będziemy ręcznie instalować usługę mobilności na źródłowych maszynach wirtualnych do replikacji, więc Utwórz fikcyjne konto w tym kroku i przejdź dalej.
11. Po ponownym uruchomieniu urządzenia po zakończeniu instalacji w obszarze **odnajdywanie maszyn**wybierz nowe urządzenie na liście **Wybierz serwer konfiguracji**, a następnie kliknij pozycję **finalizowanie rejestracji**. Finalizowanie rejestracji wykonuje kilka zadań końcowych w celu przygotowania urządzenia do replikacji.

    ![Finalizowanie rejestracji](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

## <a name="6-install-the-mobility-service"></a>6. Instalowanie usługi mobilności

Agent usługi mobilności musi być zainstalowany na źródłowej maszyny wirtualnej AWS do migracji. Instalatory agentów są dostępne na urządzeniu replikacji. Możesz znaleźć odpowiedni Instalator i zainstalować agenta na każdej maszynie, która ma zostać zmigrowana. Wykonaj następujące czynności:

1. Zaloguj się do urządzenia replikacji.
2. Przejdź do **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Znajdź Instalatora dla źródłowego systemu operacyjnego AWS maszyn wirtualnych i wersji. Przejrzyj [obsługiwane systemy operacyjne](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines).
4. Skopiuj plik Instalatora do źródłowej maszyny wirtualnej AWS, którą chcesz zmigrować.
5. Upewnij się, że masz zapisany plik tekstowy hasła, który został utworzony podczas instalacji urządzenia replikacji.
    - Jeśli nie pamiętasz zapisać hasła, możesz wyświetlić w tym kroku hasło do urządzenia replikacji. W wierszu polecenia Uruchom polecenie **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe-v** , aby wyświetlić bieżące hasło.
    - Teraz Skopiuj to hasło do schowka i Zapisz je w tymczasowym pliku tekstowym na źródłowych maszynach wirtualnych.

### <a name="installation-guide-for-windows-aws-vms"></a>Przewodnik instalacji maszyn wirtualnych z systemem Windows AWS

1. Wyodrębnij zawartość pliku Instalatora do folderu lokalnego (na przykład C:\Temp) na maszynie wirtualnej AWS w następujący sposób:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```  

2. Uruchom Instalatora usługi mobilności:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```  

3. Zarejestruj agenta przy użyciu urządzenia replikacji:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="installation-guide-for-linux-aws-vms"></a>Przewodnik instalacji maszyn wirtualnych AWS systemu Linux

1. Wyodrębnij zawartość Instalatora plik tar do folderu lokalnego (na przykład/tmp/MobSvcInstaller) na maszynie wirtualnej AWS w następujący sposób:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```  

2. Uruchom skrypt Instalatora:
    ```
    sudo ./install -r MS -q
    ```  

3. Zarejestruj agenta przy użyciu urządzenia replikacji:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="7-enable-replication-for-aws-vms"></a>7. Włącz replikację dla maszyn wirtualnych AWS

> [!NOTE]
> Za pośrednictwem portalu można jednocześnie dodać do 10 maszyn wirtualnych. Aby zreplikować więcej maszyn wirtualnych jednocześnie, można dodać je w partiach 10.

1. Na **serwerach**Azure Migrate project > **Azure Migrate: Migracja serwera**, kliknij przycisk **replikacja**.

    ![Replikowanie maszyn wirtualnych](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. W obszarze **replikacja**> **Ustawienia źródła**  >  **są zwirtualizowane na maszynach?** wybierz opcję **niezwirtualizowane/inne**.
3. W **urządzeniu lokalnym**wybierz nazwę skonfigurowanego urządzenia Azure Migrate.
4. W obszarze **serwer przetwarzania**wybierz nazwę urządzenia replikacji.
6. W obszarze **poświadczenia gościa**należy określić fikcyjne konto, które będzie używane do ręcznego instalowania usługi mobilności (instalacja wypychana nie jest obsługiwana). Następnie kliknij przycisk **Dalej: maszyny wirtualne**.

    ![Replikowanie maszyn wirtualnych](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. W **Virtual Machines**w **ustawieniach migracji importu z oceny?** pozostaw ustawienie domyślne **nie, określ ustawienia migracji ręcznie**.
8. Sprawdź wszystkie maszyny wirtualne, które chcesz zmigrować. Następnie kliknij przycisk **Dalej: ustawienia docelowe**.

    ![Wybieranie maszyn wirtualnych](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)

9. W obszarze **Ustawienia elementu docelowego** wybierz subskrypcję i docelowy region migracji, a następnie określ grupę zasobów, w której będą znajdować się maszyny wirtualne platformy Azure po migracji.
10. W obszarze **Sieć wirtualna** wybierz sieć wirtualną/podsieć platformy Azure, do której zostaną dołączone maszyny wirtualne platformy Azure po migracji.
11. W obszarze **Korzyść użycia hybrydowego platformy Azure**:
    - Wybierz pozycję **Nie**, jeśli nie chcesz stosować korzyści użycia hybrydowego platformy Azure. Następnie kliknij przycisk **Dalej**.
    - Wybierz opcję **Tak**, jeśli masz maszyny z systemem Windows Server, które są objęte aktywnym programem Software Assurance lub subskrypcjami systemu Windows Server, i chcesz zastosować korzyść do migrowanych maszyn. Następnie kliknij przycisk **Dalej**.

    ![Ustawienia docelowe](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. W obszarze **Obliczenia** sprawdź nazwę, rozmiar, typ dysku systemu operacyjnego i zestaw dostępności maszyny wirtualnej. Maszyny wirtualne muszą być zgodne z [wymaganiami platformy Azure](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **Rozmiar maszyny wirtualnej**: domyślnie migracja serwera Azure Migrate wybiera rozmiar na podstawie najbliższego dopasowania w subskrypcji platformy Azure. Alternatywnie możesz wybrać rozmiar ręczny w obszarze **rozmiaru maszyny wirtualnej platformy Azure**.
    - **Dysk systemu operacyjnego**: Określ dysk systemu operacyjnego (Boot) dla maszyny wirtualnej. Dysk systemu operacyjnego to dysk, na którym jest zainstalowany program ładujący i instalator systemu operacyjnego. 
    - **Zestaw dostępności**: Jeśli maszyna wirtualna powinna znajdować się w zestawie dostępności platformy Azure po migracji, określ zestaw. Zestaw musi znajdować się w docelowej grupie zasobów określonej dla migracji.

    ![Ustawienia obliczeń](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. W obszarze **dyski**Określ, czy dyski maszyn wirtualnych mają być replikowane na platformę Azure, a następnie wybierz typ dysku (standardowy dysk SSD lub dyski w warstwie Premium) na platformie Azure. Następnie kliknij przycisk **Dalej**.
    - Dyski można wykluczyć z replikacji.
    - Jeśli wykluczysz dyski, nie będą one znajdować się na maszynie wirtualnej platformy Azure po migracji. 

    ![Ustawienia dysku](./media/tutorial-migrate-physical-virtual-machines/disks.png)

14. W obszarze **Przegląd i rozpoczynanie replikacji** sprawdź ustawienia, a następnie kliknij pozycję **Replikuj**, aby uruchomić replikację początkową dla serwerów.

> [!NOTE]
> Ustawienia replikacji można aktualizować w dowolnym momencie przed rozpoczęciem replikacji, **Zarządzanie**  >  **maszynami replikowanymi**. Ustawień nie można zmienić po rozpoczęciu replikacji.

## <a name="8-track-and-monitor-replication-status"></a>8. śledzenie i monitorowanie stanu replikacji

- Po kliknięciu przycisku **Replikuj** rozpocznie się zadanie uruchamiania replikacji.
- Po pomyślnym zakończeniu zadania Rozpocznij replikację maszyny wirtualne rozpoczynają replikację początkową do platformy Azure.
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Przyrostowe zmiany w dyskach maszyn wirtualnych AWS są okresowo replikowane do dysków repliki na platformie Azure.

Stan zadania można śledzić w powiadomieniach portalu.

Aby monitorować stan replikacji, należy kliknąć opcję **replikowanie serwerów** w **Azure Migrate: Migracja serwera**.  

![Monitorowanie replikacji](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="9-run-a-test-migration"></a>9. Przeprowadź migrację testową

Po rozpoczęciu replikacji różnicowej można przeprowadzić migrację testową dla maszyn wirtualnych przed uruchomieniem pełnej migracji na platformę Azure. Migracja testowa jest wysoce zalecana i zapewnia możliwość wykrywania potencjalnych problemów i ich naprawy przed kontynuowaniem rzeczywistej migracji. Przed przeprowadzeniem migracji zaleca się wykonanie tej czynności co najmniej raz dla każdej maszyny wirtualnej.

- Podczas przeprowadzania migracji testów migracja będzie działać zgodnie z oczekiwaniami, bez wywierania wpływu na maszyny wirtualne AWS, które pozostają w działaniu i kontynuują replikację.
- Migracja testowa symuluje migrację, tworząc maszynę wirtualną platformy Azure przy użyciu zreplikowanych danych (zazwyczaj migrujesz do nieprodukcyjnej sieci wirtualnej w ramach subskrypcji platformy Azure).
- Możesz użyć zreplikowanego testowej maszyny wirtualnej platformy Azure, aby zweryfikować migrację, przeprowadzić testowanie aplikacji i rozwiązać wszelkie problemy przed pełną migracją.

Wykonaj migrację testową w następujący sposób:

1. W obszarze serwery **celów migracji**  >  **Servers**  >  **Azure Migrate: Migracja serwera**, kliknij przycisk **Testuj zmigrowane serwery**.

     ![Serwery z przeprowadzoną migracją testową](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Kliknij prawym przyciskiem myszy maszynę wirtualną do przetestowania, a następnie kliknij pozycję **Testuj migrację**.

    ![Testowanie migracji](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. W obszarze **Testowanie migracji** wybierz sieć wirtualną platformy Azure, w której zostanie umieszczona maszyna wirtualna platformy Azure po migracji. Zalecamy użycie nieprodukcyjnej sieci wirtualnej.
4. Zostanie uruchomione zadanie **Testowanie migracji**. Monitoruj zadanie w powiadomieniach portalu.
5. Po zakończeniu migracji sprawdź zmigrowane maszyny wirtualne platformy Azure w obszarze **Maszyny wirtualne** w witrynie Azure Portal. Nazwa maszyny ma sufiks **-Test**.
6. Po zakończeniu testu kliknij prawym przyciskiem myszy maszynę wirtualną platformy Azure w obszarze **Replikowanie maszyn**, a następnie kliknij pozycję **Wyczyść migrację testową**.

    ![Czyszczenie migracji](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="10-migrate-aws-vms"></a>10. Migrowanie maszyn wirtualnych AWS

Po zweryfikowaniu, że migracja testowa działa zgodnie z oczekiwaniami, można migrować maszyny wirtualne AWS.

1. W Azure Migrate serwery > Project **Servers**  >  **Azure Migrate: Migracja serwera**, kliknij przycisk **replikowanie serwerów**.

    ![Replikowanie serwerów](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. W obszarze **Replikowanie maszyn** kliknij prawym przyciskiem myszy maszynę wirtualną > **Migruj**.
3. W obszarze **Migrowanie**  >  **Zamknij maszyny wirtualne i przeprowadź planowaną migrację bez utraty danych**wybierz pozycję **tak**  >  **OK**.
    - Jeśli nie chcesz zamykać maszyny wirtualnej, wybierz pozycję **nie**.
4. Zostanie uruchomione zadanie migracji maszyny wirtualnej. Aby wyświetlić stan zadania, kliknij ikonę powiadomienia dzwonka w prawym górnym rogu strony portalu lub przejdź do strony zadania narzędzia migracji serwera (kliknij przycisk przegląd na kafelku narzędzia > wybierz pozycję zadania w menu po lewej).
5. Po zakończeniu zadania możesz wyświetlić maszynę wirtualną i zarządzać nią na stronie Maszyny wirtualne.

### <a name="complete-the-migration"></a>Kończenie migracji

1. Po zakończeniu migracji kliknij prawym przyciskiem myszy maszynę wirtualną > **Zatrzymaj migrację**. Spowoduje to wykonanie następujących czynności:
    - Kończy replikację maszyny wirtualnej AWS.
    - Usuwa maszynę wirtualną AWS z liczby **serwerów replikacji** w Azure Migrate: Migracja serwera.
    - Czyści informacje o stanie replikacji maszyny wirtualnej.
2. Zainstaluj agenta systemu [Linux](../virtual-machines/extensions/agent-linux.md) na zmigrowanych maszynach. Agent maszyny wirtualnej platformy Azure jest wstępnie zainstalowany podczas procesu migracji.
3. Po zakończeniu migracji wykonaj wszystkie potrzebne czynności konfiguracyjne, takie jak aktualizacja parametrów połączenia bazy danych i serwera sieci Web.
4. Wykonaj dla zmigrowanej aplikacji uruchomionej na platformie Azure testy końcowe aplikacji i akceptacji migracji.
5. Obcinaj ruch do zmigrowanego wystąpienia maszyny wirtualnej platformy Azure.
6. Zaktualizuj wszystkie dokumenty wewnętrzne, aby wyświetlić nową lokalizację i adres IP maszyn wirtualnych platformy Azure. 

## <a name="post-migration-best-practices"></a>Najlepsze rozwiązania po migracji

- Aby zwiększyć elastyczność:
    - Zapewnij bezpieczeństwo danych – utwórz kopie zapasowe maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup. [Dowiedz się więcej](../backup/quick-backup-vm-portal.md).
    - Zadbaj, aby pakiety robocze były uruchomione i stale dostępne, replikując maszyny wirtualne platformy Azure do regionu pomocniczego za pomocą usługi Site Recovery. [Dowiedz się więcej](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Aby zwiększyć bezpieczeństwo:
    - Zablokuj i Ogranicz dostęp do ruchu przychodzącego za pomocą [administracji Azure Security Center w czasie](../security-center/security-center-just-in-time.md).
    - Ogranicz ruch sieciowy do punktów końcowych zarządzania za pomocą [sieciowych grup zabezpieczeń](../virtual-network/security-overview.md).
    - Wdróż usługę [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md), aby ułatwić zabezpieczenie dysków i zabezpieczyć dane przed kradzieżą lub nieautoryzowanym dostępem.
    - Przeczytaj więcej na temat [zabezpieczania zasobów IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) i skorzystaj z usługi [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Na potrzeby monitorowania i zarządzania:
    - Rozważ wdrożenie usługi [Azure Cost Management](../cost-management-billing/cloudyn/overview.md), aby monitorować użycie zasobu i wydatki.

## <a name="next-steps"></a>Następne kroki

Zbadaj [podróż migracji w chmurze](/azure/architecture/cloud-adoption/getting-started/migrate) w strukturze wdrażania w chmurze platformy Azure.

## <a name="troubleshooting--tips"></a>Rozwiązywanie problemów/porady

**Pytanie:** Nie widzę mojej maszyny wirtualnej AWS na odnalezionej liście serwerów do migracji   
**Odpowiedź:** Sprawdź, czy urządzenie do replikacji spełnia wymagania. Upewnij się, że Agent mobilności został zainstalowany na źródłowej maszynie wirtualnej do migracji i jest zarejestrowany na serwerze konfiguracji. Sprawdź ustawienia sieci i reguły zapory, aby włączyć ścieżkę sieciową między urządzeniem replikacji a źródłową maszyną wirtualną AWS.  

**Pytanie:** Jak mogę sprawdzić, czy moja maszyna wirtualna została pomyślnie zmigrowana   
**Odpowiedź:** Po migracji można wyświetlić maszynę wirtualną i zarządzać nią na stronie Virtual Machines. Nawiąż połączenie z zmigrowanym maszyną wirtualną w celu zweryfikowania.  

**Pytanie:** Nie mogę zaimportować maszyn wirtualnych do migracji z wcześniej utworzonych wyników oceny serwera   
**Odpowiedź:** Obecnie nie obsługujemy importowania oceny dla tego przepływu pracy. Aby obejść ten krok, można wyeksportować ocenę, a następnie ręcznie wybrać zalecenie dotyczące maszyn wirtualnych podczas kroku włączania replikacji.
  
**Pytanie:** Otrzymuję komunikat o błędzie "nie można pobrać identyfikatora GUID systemu BIOS" podczas próby odnalezienia maszyn wirtualnych AWS   
**Odpowiedź:** Przejrzyj obsługiwane systemy operacyjne dla maszyn wirtualnych AWS.  

**Pytanie:** Mój stan replikacji nie jest postępem    
**Odpowiedź:** Sprawdź, czy urządzenie do replikacji spełnia wymagania. Upewnij się, że włączono wymagane porty na porcie TCP 9443 i HTTPS 443 dla transportu danych. Upewnij się, że nie ma żadnych starych zduplikowanych wersji urządzenia replikacji połączonego z tym samym projektem.   

**Pytanie:** Nie mogę wykryć wystąpień usługi AWS przy użyciu Azure Migrate ze względu na kod stanu HTTP 504 z zdalnej usługi zarządzania systemu Windows    
**Odpowiedź:** Zapoznaj się z wymaganiami dotyczącymi migracji na platformę Azure i potrzebami dostępu do adresów URL. Upewnij się, że żadne ustawienia serwera proxy nie blokują rejestracji urządzenia.   
