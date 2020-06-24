---
title: Skonfiguruj Azure Backup Server rozwiązania VMware dla platformy Azure
description: Skonfiguruj środowisko rozwiązań VMware platformy Azure, aby utworzyć kopię zapasową maszyn wirtualnych przy użyciu Azure Backup Server.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: c56ebaff6b08f3d6586dfe025fdb2a5bfc708fa4
ms.sourcegitcommit: 1383842d1ea4044e1e90bd3ca8a7dc9f1b439a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/16/2020
ms.locfileid: "84816855"
---
# <a name="set-up-azure-backup-server-for-azure-vmware-solution"></a>Skonfiguruj Azure Backup Server rozwiązania VMware dla platformy Azure

Azure Backup Server to niezawodny system tworzenia kopii zapasowych i odzyskiwania danych w przedsiębiorstwie, który przyczynia się do strategii ciągłości działania i odzyskiwania po awarii (BCDR). Podczas korzystania z wersji zapoznawczej rozwiązania Azure VMware można skonfigurować tylko kopie zapasowe na poziomie maszyny wirtualnej za pomocą Azure Backup Server. 

Azure Backup Server mogą przechowywać dane kopii zapasowej w:

- **Dysk**: w celu przechowywania krótkoterminowego Azure Backup Server tworzenia kopii zapasowych danych w pulach dysków.
- **Azure**: zarówno krótkoterminowe, jak i długoterminowe magazyny magazynu, Azure Backup Server danych przechowywanych w pulach dysków można utworzyć kopie zapasowe w chmurze Microsoft Azure przy użyciu Azure Backup.

Gdy wystąpi awaria i dane źródłowe są niedostępne, można użyć Azure Backup Server, aby łatwo przywrócić dane do źródła lub lokalizacji alternatywnej. Dzięki temu, jeśli oryginalne dane są niedostępne z powodu planowanych lub nieoczekiwanych problemów, można łatwo przywrócić dane do lokalizacji alternatywnej.

W tym artykule pomożemy przygotować środowisko rozwiązań VMware platformy Azure do tworzenia kopii zapasowych maszyn wirtualnych za pomocą Azure Backup Server. Przeprowadzimy Cię przez kroki, aby: 

> [!div class="checklist"]
> * Określ Zalecany typ i rozmiar dysku maszyny wirtualnej.
> * Utwórz magazyn Recovery Services, w którym są przechowywane punkty odzyskiwania.
> * Ustaw replikację magazynu dla magazynu Recovery Servicesowego.
> * Dodaj magazyn do Azure Backup Server.

## <a name="supported-vmware-features"></a>Obsługiwane funkcje oprogramowania VMware

- **Tworzenie kopii zapasowej bez wykorzystania agentów:** Azure Backup Server nie wymaga zainstalowania agenta na serwerze vCenter lub ESXi w celu utworzenia kopii zapasowej maszyny wirtualnej. Zamiast tego wystarczy podać adres IP lub w pełni kwalifikowaną nazwę domeny (FQDN) oraz poświadczenia logowania używane do uwierzytelniania serwera VMware za pomocą Azure Backup Server.
- **Kopia zapasowa zintegrowana z chmurą:** Azure Backup Server chroni obciążenia dyskami i chmurą. Przepływ pracy tworzenia kopii zapasowych i odzyskiwania Azure Backup Server ułatwia zarządzanie długoterminowym przechowywaniem i kopią zapasową poza siedzibą firmy.
- **Wykrywanie i ochrona maszyn wirtualnych zarządzanych przez program vCenter:** Azure Backup Server wykrywa i chroni maszyny wirtualne wdrożone na serwerze vCenter lub ESXi. Azure Backup Server wykrywa również maszyny wirtualne zarządzane przez program vCenter, dzięki czemu można chronić duże wdrożenia.
- **Autoochrona na poziomie folderu:** program vCenter umożliwia organizowanie maszyn wirtualnych w folderach maszyn wirtualnych. Azure Backup Server wykrywa te foldery i można z niej korzystać w celu ochrony maszyn wirtualnych na poziomie folderów, które obejmują wszystkie podfoldery. W przypadku ochrony folderów Azure Backup Server nie tylko chroni maszyny wirtualne znajdujące się w tym folderze, ale również chroni maszyny wirtualne dodane później. Azure Backup Server wykrywa nowe maszyny wirtualne codziennie i chroni je automatycznie. Podczas organizowania maszyn wirtualnych w folderach cyklicznych Azure Backup Server automatycznie wykrywa i chroni nowe maszyny wirtualne wdrożone w folderach cyklicznych.
- **Azure Backup Server nadal chronią maszyny wirtualne vMotioned w klastrze:** Ponieważ maszyny wirtualne są vMotioned do równoważenia obciążenia w klastrze, Azure Backup Server automatycznie wykrywa i kontynuuje ochronę maszyny wirtualnej.
- **Szybsze odzyskiwanie niezbędnych plików:** Azure Backup Server może odzyskiwać pliki lub foldery z maszyny wirtualnej z systemem Windows bez odzyskiwania całej maszyny wirtualnej.

## <a name="limitations"></a>Ograniczenia

- Pakiet zbiorczy aktualizacji 1 dla Azure Backup Server v3 musi być zainstalowany.
- Nie można utworzyć kopii zapasowej migawek użytkowników przed pierwszym Azure Backup Server kopii zapasowej. Po Azure Backup Server zakończeniu tworzenia pierwszej kopii zapasowej można utworzyć kopię zapasową migawek użytkownika.
- Azure Backup Server nie może chronić maszyn wirtualnych VMware z dyskami przekazującymi i fizycznymi mapowaniami urządzeń nieprzetworzonych (pRDMs).
- Azure Backup Server nie może wykryć ani chronić programu VMware oprogramowania vApps.

Aby skonfigurować Azure Backup Server rozwiązania VMware dla platformy Azure, należy wykonać następujące czynności:

- Skonfiguruj wymagania wstępne i środowisko.
- Utwórz magazyn usługi Recovery Services.
- Pobierz i Zainstaluj Azure Backup Server.
- Dodaj magazyn do Azure Backup Server.

### <a name="deployment-architecture"></a>Architektura wdrożenia

Azure Backup Server jest wdrażana jako maszyna wirtualna platformy Azure jako usługa (IaaS) w celu ochrony maszyn wirtualnych rozwiązań VMware platformy Azure.

:::image type="content" source="media/avs-backup/deploy-mabs-avs-diagram.png" alt-text="Architektura wdrożenia automatycznej synchronizacji" border="false":::

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Wymagania wstępne dotyczące środowiska Azure Backup Server

Przed zainstalowaniem Azure Backup Server w środowisku platformy Azure należy wziąć pod uwagę zalecenia przedstawione w tej sekcji.

### <a name="azure-virtual-network"></a>Azure Virtual Network

Upewnij się, że [skonfigurowano sieć dla chmury prywatnej VMware na platformie Azure](tutorial-configure-networking.md).

### <a name="determine-the-size-of-the-virtual-machine"></a>Określanie rozmiaru maszyny wirtualnej

Należy utworzyć maszynę wirtualną z systemem Windows w sieci wirtualnej utworzonej w poprzednim kroku. Po wybraniu serwera do uruchamiania Azure Backup Server, Zacznij od obrazu galerii systemu Windows Server 2019 Datacenter. Samouczek [Tworzenie pierwszej maszyny wirtualnej z systemem Windows w Azure Portal](../virtual-machines/windows/quick-create-portal.md) pozwala rozpocząć pracę z ZALECAną maszyną wirtualną na platformie Azure, nawet jeśli nie korzystasz z platformy Azure.

Poniższa tabela zawiera podsumowanie maksymalnej liczby chronionych obciążeń dla każdego Azure Backup Server rozmiaru maszyny wirtualnej. Informacje są oparte na wewnętrznych testach wydajności i skali z wartościami kanonicznymi rozmiaru i zmian obciążenia. Rzeczywisty rozmiar obciążenia może być większy, ale powinien być uwzględniany przez dyski dołączone do maszyny wirtualnej Azure Backup Server.

| Maksymalna liczba chronionych obciążeń | Średni rozmiar obciążenia | Średnia wartość zmian obciążenia (dziennie) | Minimalna liczba operacji we/wy na sekundę | Zalecany typ dysku/rozmiar      | Zalecany rozmiar maszyny wirtualnej |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100 GB                | 5% zmian netto                   | 2000             | HDD w warstwie Standardowa (rozmiar: 8 TB lub więcej na dysk)  | A4V2       |
| 40                      | 150 GB                | 10% zmian netto                  | 4500             | SSD w warstwie Premium * (rozmiar 1 TB lub więcej na dysk) | DS3_V2     |
| 60                      | 200 GB                | 10% zmian netto                  | 10 500            | SSD w warstwie Premium * (rozmiar na dysku o rozmiarze 8 TB lub większym) | DS3_V2     |

* Aby uzyskać wymagane operacje we/wy, należy użyć minimalnych dysków o rozmiarze zalecanym lub wyższym. Dyski o mniejszych rozmiarach oferują mniejsze liczby operacji we/wy na sekundę.

> [!NOTE]
> Azure Backup Server jest przeznaczony do działania na dedykowanym, wyznaczonym do tego celu serwerze. Nie można zainstalować Azure Backup Server na komputerze, który:
> * Działa jako kontroler domeny.
> * Ma zainstalowaną rolę serwera aplikacji.
> * Jest System Center Operations Manager serwerem zarządzania.
> * Uruchamia program Exchange Server.
> * Jest węzłem klastra.

### <a name="disks-and-storage"></a>Dyski i magazyn

Azure Backup Server wymaga dysków do instalacji, w tym plików systemowych, plików instalacyjnych, wstępnie wymaganego oprogramowania, plików bazy danych i dysków dedykowanych dla puli magazynów.

| Wymaganie                      | Zalecany rozmiar  |
|----------------------------------|-------------------------|
| Instalacja Azure Backup Server                | Lokalizacja instalacji: 3 GB<br />Dysk na pliki bazy danych: 900 MB<br />Dysk systemowy: 1 GB na potrzeby instalacji SQL Server<br /><br />Musisz również mieć miejsce na Azure Backup Server, aby skopiować katalog plików do tymczasowej lokalizacji instalacji podczas archiwizowania.      |
| Dysk puli magazynów<br />(Używa woluminów podstawowych, nie może znajdować się na dysku dynamicznym) | Dwa do trzech razy więcej niż rozmiar chronionych danych.<br />Aby uzyskać szczegółowe obliczenia magazynu, zobacz [planista wydajności programu DPM](https://www.microsoft.com/download/details.aspx?id=54301).   |

Aby dowiedzieć się, jak dołączyć nowy dysk danych zarządzanych do istniejącej maszyny wirtualnej platformy Azure, zobacz [dołączanie zarządzanego dysku danych do maszyny wirtualnej z systemem Windows przy użyciu Azure Portal](../virtual-machines/windows/attach-managed-disk-portal.md).

> [!NOTE]
> Pojedynczy Azure Backup Server ma limit niezmienny 120 TB dla puli magazynów.

### <a name="store-backup-data-on-local-disk-and-in-azure"></a>Przechowywanie danych kopii zapasowej na dysku lokalnym i na platformie Azure

Przechowywanie danych kopii zapasowej na platformie Azure zmniejsza infrastrukturę tworzenia kopii zapasowych na maszynie wirtualnej Azure Backup Server. W przypadku odzyskiwania operacyjnego program Azure Backup Server przechowuje dane kopii zapasowych na dyskach platformy Azure dołączonych do maszyny wirtualnej. Gdy dyski i miejsce do magazynowania zostaną dołączone do maszyny wirtualnej, Azure Backup Server zarządza magazynem. Ilość miejsca w magazynie danych kopii zapasowej zależy od liczby i rozmiaru dysków dołączonych do każdej maszyny wirtualnej platformy Azure. Każdy rozmiar maszyny wirtualnej platformy Azure ma maksymalną liczbę dysków, które można dołączać. Na przykład a2 to cztery dyski, A3 to osiem dysków, a A4 to 16 dysków. Ponownie rozmiar i liczba dysków określają całkowitą pojemność puli magazynów kopii zapasowych.

> [!IMPORTANT]
> Danych odzyskiwania operacyjnego *nie* należy przechowywać na dyskach dołączonych Azure Backup Server przez więcej niż pięć dni. Jeśli dane są starsze niż pięć dni, należy je zapisać w magazynie Recovery Services.

Aby przechowywać dane kopii zapasowej na platformie Azure, Utwórz magazyn Recovery Services lub użyj go. Podczas przygotowywania do wykonania kopii zapasowej obciążenia Azure Backup Server należy [skonfigurować magazyn Recovery Services](#create-a-recovery-services-vault). Po skonfigurowaniu usługi za każdym razem, gdy zostanie uruchomione zadanie tworzenia kopii zapasowej online, do magazynu zostanie utworzony punkt odzyskiwania. Każdy magazyn Recovery Services zawiera maksymalnie 9 999 punktów odzyskiwania. W zależności od liczby utworzonych punktów odzyskiwania i czasu ich przechowywania można zachować dane kopii zapasowej przez wiele lat. Można na przykład utworzyć miesięczne punkty odzyskiwania i zachować je przez pięć lat.

> [!IMPORTANT]
> Niezależnie od tego, czy dane kopii zapasowej są wysyłane do platformy Azure, czy przechowywane lokalnie, należy zarejestrować Azure Backup Server z magazynem Recovery Services.

### <a name="scale-deployment"></a>Skalowanie wdrożenia

Jeśli chcesz skalować wdrożenie, masz następujące opcje:

- **Skalowanie w górę**: zwiększanie rozmiaru maszyny wirtualnej Azure Backup Server z serii do serii DS3 i zwiększanie magazynu lokalnego.
- **Odciążanie danych**: Wysyłanie starszych danych na platformę Azure i zachowywanie tylko najnowszych danych w magazynie dołączonym do maszyny Azure Backup Server.
- **Skalowanie w poziomie**: Dodaj więcej maszyn Azure Backup Server, aby chronić obciążenia.

### <a name="net-framework"></a>.NET Framework

Na maszynie wirtualnej musi być zainstalowany program .NET Framework 3,5 z dodatkiem SP1 lub nowszy.

### <a name="join-a-domain"></a>Przyłączanie się do domeny

Maszyna wirtualna Azure Backup Server musi być przyłączona do domeny, a użytkownik domeny z uprawnieniami administratora na maszynie wirtualnej musi zainstalować Azure Backup Server.

Chociaż nie jest obsługiwany w wersji zapoznawczej, Azure Backup Server wdrożona na maszynie wirtualnej platformy Azure może tworzyć kopie zapasowe obciążeń na maszynach wirtualnych w rozwiązaniu VMware platformy Azure. Obciążenia powinny znajdować się w tej samej domenie, aby można było włączyć operację tworzenia kopii zapasowej.

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn Recovery Services jest jednostką magazynową, która przechowuje punkty odzyskiwania utworzone w czasie. Zawiera również zasady tworzenia kopii zapasowych, które są skojarzone z elementami chronionymi.

1. Zaloguj się do subskrypcji w witrynie [Azure Portal](https://portal.azure.com/).

1. W menu po lewej stronie wybierz pozycję **Wszystkie usługi**.

   ![Wybieranie pozycji Wszystkie usługi](../backup/media/backup-create-rs-vault/click-all-services.png)

1. W oknie dialogowym **wszystkie usługi** wprowadź **Recovery Services** a następnie wybierz pozycję **magazyny Recovery Services** z listy.

   ![Wprowadzanie i wybieranie pozycji Magazyny usługi Recovery Services](../backup/media/backup-create-rs-vault/all-services.png)

   Zostanie wyświetlona lista magazynów usługi Recovery Services w ramach subskrypcji.

1. Na pulpicie nawigacyjnym **Recovery Services magazynów** wybierz pozycję **Dodaj**.

   ![Dodawanie magazynu Recovery Services](../backup/media/backup-create-rs-vault/add-button-create-vault.png)

   Zostanie otwarte okno dialogowe **magazyn Recovery Services** .

1. Wprowadź wartości dla **nazwy**, **subskrypcji**, **grupy zasobów**i **lokalizacji**.

   ![Konfigurowanie magazynu Recovery Services](../backup/media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Nazwa**: wprowadź przyjazną nazwę identyfikującą magazyn. Nazwa musi być unikatowa dla subskrypcji platformy Azure. Określ nazwę, która ma co najmniej dwa znaki, ale nie więcej niż 50 znaków. Nazwa musi rozpoczynać się od litery i zawierać tylko litery, cyfry i łączniki.
   - **Subskrypcja**: wybierz subskrypcję do użycia. Jeśli jesteś członkiem tylko jednej subskrypcji, zobaczysz tę nazwę. Jeśli nie masz pewności, której subskrypcji użyć, Użyj domyślnej (sugerowanej) subskrypcji. Istnieje wiele opcji, które są dostępne tylko wtedy, gdy konto służbowe jest skojarzone z więcej niż jedną subskrypcją platformy Azure.
   - **Grupa zasobów**: Użyj istniejącej grupy zasobów lub Utwórz nową. Aby wyświetlić listę dostępnych grup zasobów w ramach subskrypcji, wybierz pozycję **Użyj istniejącej**, a następnie wybierz zasób z listy rozwijanej. Aby utworzyć nową grupę zasobów, wybierz pozycję **Utwórz nową** i wprowadź nazwę.
   - **Lokalizacja**: Wybierz region geograficzny magazynu. Aby utworzyć magazyn do ochrony maszyn wirtualnych rozwiązań VMware platformy Azure, magazyn *musi* znajdować się w tym samym regionie co chmurę prywatną rozwiązania VMware platformy Azure.

1. Gdy wszystko będzie gotowe do utworzenia magazynu Recovery Services, wybierz pozycję **Utwórz**.

   ![Tworzenie magazynu Recovery Services](../backup/media/backup-create-rs-vault/click-create-button.png)

   Utworzenie magazynu Recovery Services może chwilę potrwać. Monitoruj powiadomienia o stanie w obszarze **powiadomienia** w prawym górnym rogu portalu. Po utworzeniu magazynu będzie on widoczny na liście magazynów Recovery Services. Jeśli Twój magazyn nie jest widoczny, wybierz pozycję **Odśwież**.

   ![Odświeżanie listy magazynów kopii zapasowych](../backup/media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-replication"></a>Ustaw replikację magazynu

Opcja replikacja magazynu umożliwia wybranie między magazynem geograficznie nadmiarowym (domyślnym) i magazynem lokalnie nadmiarowym. Magazyn Geograficznie nadmiarowy kopiuje dane z konta magazynu do regionu pomocniczego, co sprawia, że dane są trwałe. Magazyn lokalnie nadmiarowy jest tańszą opcją, która nie jest trwała. Aby dowiedzieć się więcej o opcjach magazynu geograficznie nadmiarowego i lokalnie nadmiarowego, zobacz [nadmiarowość usługi Azure Storage](../storage/common/storage-redundancy.md).

> [!IMPORTANT]
> Przed skonfigurowaniem kopii zapasowych w magazynie należy najpierw zmienić ustawienie **typu replikacji magazynu lokalnie nadmiarowego i geograficznie** nadmiarowego dla magazynu Recovery Services. Po skonfigurowaniu kopii zapasowych, opcja modyfikacji jest wyłączona i nie można zmienić typu replikacji magazynu.

1. W obszarze **magazyny Recovery Services**wybierz nowy magazyn. 

1. W obszarze **Ustawienia** wybierz pozycję **Właściwości**. W obszarze **Konfiguracja kopii zapasowej**wybierz pozycję **Aktualizuj**.

1. Wybierz typ replikacji magazynu i wybierz pozycję **Zapisz**.

   ![Ustaw konfigurację magazynu dla nowego magazynu](../backup/media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="download-and-install-the-software-package"></a>Pobierz i zainstaluj pakiet oprogramowania

Wykonaj kroki opisane w tej sekcji, aby pobrać, wyodrębnić i zainstalować pakiet oprogramowania.

### <a name="download-the-software-package"></a>Pobierz pakiet oprogramowania

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Jeśli masz już otwarty magazyn Recovery Services, przejdź do następnego kroku. Jeśli nie masz otwartego magazynu Recovery Services ale jesteś w Azure Portal, w menu głównym wybierz pozycję **Przeglądaj**.

   1. Na liście zasobów wprowadź **Recovery Services**.

   1. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Gdy widzisz **Recovery Services magazynów**, wybierz ją.

   ![Utwórz magazyn Recovery Services krok 1](../backup/media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

1. Wybierz magazyn z listy magazynów Usług odzyskiwania.

   Zostanie otwarty pulpit nawigacyjny wybranego magazynu.

   ![Otwórz pulpit nawigacyjny magazynu](../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

   Opcja **Ustawienia** zostanie domyślnie otwarta. Jeśli jest zamknięty, wybierz pozycję **Ustawienia** , aby go otworzyć.

   ![Otwórz opcję ustawień magazynu](../backup/media/backup-azure-microsoft-azure-backup/vault-setting.png)

1. Wybierz pozycję **kopia zapasowa** , aby otworzyć kreatora **wprowadzenie** .

   ![Zostanie otwarta kopia zapasowa Kreator Wprowadzenie](../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

1. W otwartym oknie wykonaj następujące czynności:

   1. W menu **gdzie jest uruchomione Twoje obciążenie?** wybierz pozycję **lokalnie**.

      :::image type="content" source="media/avs-backup/deploy-mabs-on-premises-workload.png" alt-text="Gdzie działa Twoje obciążenie?":::

   1. Z menu **co chcesz utworzyć kopię zapasową?** wybierz obciążenia, które chcesz chronić za pomocą Azure Backup Server.

   1. Wybierz pozycję **Przygotuj infrastrukturę** , aby pobrać i zainstalować Azure Backup Server i poświadczenia magazynu.

      :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure.png" alt-text="Przygotuj infrastrukturę":::

1. W otwartym oknie **infrastruktura przygotowania** wykonaj następujące czynności:

   1. Wybierz link **pobierania** , aby zainstalować Azure Backup Server.

   1. Pobierz poświadczenia magazynu, zaznaczając pole wyboru **już pobrane lub używając najnowszej instalacji Azure Backup Server** , a następnie wybierz pozycję **Pobierz**. Poświadczenia magazynu są używane podczas rejestracji Azure Backup Server w magazynie Recovery Services. Linki prowadzą do centrum pobierania, w którym pobierasz pakiet oprogramowania.

   :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure2.png" alt-text="Przygotuj infrastrukturę — Azure Backup Server":::

1. Na stronie Pobieranie zaznacz wszystkie pliki i wybierz pozycję **dalej**.

   > [!NOTE]
   > Musisz pobrać wszystkie pliki do tego samego folderu. Ponieważ rozmiar pobieranych plików jest większy niż 3 GB, ukończenie pobierania może zająć do 60 minut. 

   ![Wybierz pliki w centrum pobierania](../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png)

### <a name="extract-the-software-package"></a>Wyodrębnij pakiet oprogramowania

Jeśli pakiet oprogramowania został pobrany na inny serwer, skopiuj pliki na maszynę wirtualną, która została utworzona w celu wdrożenia Azure Backup Server.

> [!WARNING]
> Do wyodrębnienia plików instalacyjnych wymagane jest co najmniej 4 GB wolnego miejsca.

1. Po pobraniu wszystkich plików kliknij dwukrotnie przycisk **MicrosoftAzureBackupInstaller.exe** , aby otworzyć kreatora instalacji **Microsoft Azure Backup** , a następnie wybierz przycisk **dalej**.

1. Wybierz lokalizację, w której mają zostać wyodrębnione pliki, a następnie wybierz pozycję **dalej**.

1. Wybierz pozycję **Wyodrębnij** , aby rozpocząć proces wyodrębniania.

   ![Kreator instalacji Microsoft Azure Backup](../backup/media/backup-azure-microsoft-azure-backup/extract/03.png)

1. Po wyodrębnieniu wybierz opcję **wykonywania setup.exe** a następnie wybierz pozycję **Zakończ**.

> [!TIP]
> Plik setup.exe można również zlokalizować z folderu, w którym został wyodrębniony pakiet oprogramowania.

### <a name="install-the-software-package"></a>Zainstaluj pakiet oprogramowania

1. W oknie instalatora w obszarze **Instalacja**wybierz pozycję **Microsoft Azure Backup** , aby otworzyć Kreatora instalacji.

   ![Przycisk instalacji kreatora instalacji Microsoft Azure Backup](../backup/media/backup-azure-microsoft-azure-backup/launch-screen2.png)

1. Na ekranie **powitalnym** wybierz pozycję **dalej** , aby przejść do strony **Sprawdzanie wymagań wstępnych** .

1. Wybierz pozycję **Sprawdź ponownie** , aby określić, czy zostały spełnione wymagania wstępne dotyczące sprzętu i oprogramowania dla Azure Backup Server. Jeśli zostało spełnione pomyślnie, wybierz pozycję **dalej**.

   ![Sprawdzanie wymagań wstępnych Azure Backup Server](../backup/media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

1. Pakiet instalacyjny Azure Backup Server jest powiązany z odpowiednimi SQL Server plikami binarnymi, które są potrzebne. Po uruchomieniu nowej instalacji Azure Backup Server wybierz opcję **Zainstaluj nowe wystąpienie SQL Server z tą konfiguracją** . Następnie wybierz pozycję **Sprawdź i zainstaluj**.

   ![Sprawdzanie SQL Server Azure Backup Server](../backup/media/backup-azure-microsoft-azure-backup/sql/01.png)

   > [!NOTE]
   > Jeśli chcesz użyć własnego wystąpienia SQL Server, obsługiwane wersje SQL Server są SQL Server 2014 z dodatkiem SP1 lub nowszym, 2016 i 2017. Wszystkie SQL Server wersje powinny mieć wersję Standard lub Enterprise 64-bit. Azure Backup Server nie działa z wystąpieniem SQL Server zdalnego. Wystąpienie używane przez Azure Backup Server musi być lokalne. Jeśli używasz istniejącego wystąpienia SQL Server dla Azure Backup Server, Instalator obsługuje tylko *nazwane wystąpienia* SQL Server.

   Jeśli wystąpi błąd z zaleceniem ponownego uruchomienia maszyny, zrób to, a następnie wybierz pozycję **Sprawdź ponownie**. W przypadku problemów z konfiguracją SQL Server ponownie skonfiguruj SQL Server zgodnie z wytycznymi SQL Server. Następnie ponów próbę instalacji lub uaktualnienia Azure Backup Server przy użyciu istniejącego wystąpienia SQL Server.

   **Konfiguracja ręczna**

   W przypadku korzystania z własnego wystąpienia SQL Server upewnij się, że dodano Builtin\administratorzy do roli sysadmin do bazy danych Master.

   **Konfiguracja usługi SSRS w SQL Server 2017**

   W przypadku korzystania z własnego wystąpienia SQL Server 2017 należy ręcznie skonfigurować usługi SQL Server 2017 Reporting Services (SSRS). Po skonfigurowaniu usług SSRS upewnij się, że właściwość **IsInitialized** usług SSRS ma **wartość true**. Gdy ta właściwość ma **wartość true**, Azure Backup Server zakłada, że usługa SSRS jest już skonfigurowana i pomija konfigurację usług SSRS.

   Aby sprawdzić stan konfiguracji usług SSRS, uruchom następujące polecenie:

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   W przypadku konfiguracji usług SSRS należy użyć następujących wartości:

   * **Konto usługi**: **Użyj wbudowanego konta** powinno być **Usługa sieciowa**.
   * **Adres URL usługi sieci Web**: **katalog wirtualny** powinien być **ReportServer_ \<SQLInstanceName> **.
   * **Baza danych**: **DatabaseName** powinna być **reportserver \<SQLInstanceName> $**.
   * **Adres URL portalu sieci Web**: **katalog wirtualny** powinien być **Reports_ \<SQLInstanceName> **.

   [Dowiedz się więcej](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) o konfiguracji usług SSRS.

   > [!NOTE]
   > [Warunki użytkowania usług online firmy Microsoft](https://www.microsoft.com/licensing/product-licensing/products) (ost) regulują licencjonowanie SQL Server używanych jako baza danych Azure Backup Server. Zgodnie z elementem OST SQL Server dołączone do Azure Backup Server może być używany tylko jako baza danych Azure Backup Server.

1. Po pomyślnym zakończeniu instalacji wybierz pozycję **dalej**.

1. Podaj lokalizację instalacji plików serwera Microsoft Azure Backup, a następnie wybierz pozycję **dalej**.

   > [!NOTE]
   > Lokalizacja tymczasowa jest wymagana na potrzeby tworzenia kopii zapasowych na platformie Azure. Upewnij się, że lokalizacja tymczasowa to co najmniej 5% danych, których kopię zapasową zaplanowano do chmury. W przypadku ochrony dysków należy skonfigurować oddzielne dyski po zakończeniu instalacji. Aby uzyskać więcej informacji na temat pul magazynów, zobacz [Konfigurowanie pul magazynów i magazynu dyskowego](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)).

   ![Ustawienia instalacji Microsoft Azure Backup](../backup/media/backup-azure-microsoft-azure-backup/space-screen.png)

1. Podaj silne hasło dla kont użytkowników lokalnych z ograniczeniami, a następnie wybierz pozycję **dalej**.

   ![Ustawienia zabezpieczeń konfiguracji Microsoft Azure Backup](../backup/media/backup-azure-microsoft-azure-backup/security-screen.png)

1. Wybierz, czy chcesz użyć Microsoft Update do sprawdzenia dostępności aktualizacji, a następnie wybierz przycisk **dalej**.

   > [!NOTE]
   > Zalecamy, aby Windows Update przekierować do Microsoft Update, który oferuje bezpieczeństwo i ważne aktualizacje systemu Windows oraz innych produktów, takich jak Azure Backup Server.

   ![Konfiguracja Microsoft Azure Backup Microsoft Update zgodę](../backup/media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

1. Przejrzyj **Podsumowanie ustawień**, a następnie wybierz pozycję **Zainstaluj**.

   Instalacja odbywa się w fazach. Pierwsza faza instaluje agenta Microsoft Azure Recovery Services, a druga etap sprawdza łączność z Internetem. Jeśli dostępna jest łączność z Internetem, można kontynuować instalację. W przeciwnym razie musisz podać szczegóły serwera proxy, aby połączyć się z Internetem. Końcowa faza sprawdza wstępnie wymagane oprogramowanie. Jeśli nie jest zainstalowana, wszystkie brakujące oprogramowanie zostanie zainstalowane wraz z agentem Microsoft Azure Recovery Services.

1. Wybierz pozycję **Przeglądaj** , aby zlokalizować swoje poświadczenia magazynu, aby zarejestrować maszynę w magazynie Recovery Services, a następnie wybierz pozycję **dalej**.

1. Wybierz pozycję Przekaż frazę, aby zaszyfrować lub odszyfrować dane wysyłane między platformą Azure i Twoim środowiskiem lokalnym.

   > [!TIP]
   > Możesz automatycznie generować hasło lub podać własne, 16-znakowe hasło.

1. Wprowadź lokalizację, w której ma zostać zapisane hasło, a następnie wybierz pozycję **dalej** , aby zarejestrować serwer.

   > [!IMPORTANT]
   > Zapisz frazę Pass w bezpiecznej lokalizacji innej niż serwer lokalny. Zdecydowanie zalecamy używanie Azure Key Vault do przechowywania frazy.

   Po zakończeniu instalacji agenta Microsoft Azure Recovery Services krok instalacji przejdzie do instalacji i konfiguracji SQL Server i składników Azure Backup Server.

   ![Instalacja instalacji Microsoft Azure Backup](../backup/media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

1. Po zakończeniu kroku instalacji wybierz pozycję **Zamknij**.

### <a name="install-update-rollup-1"></a>Zainstaluj pakiet zbiorczy aktualizacji 1

Przed przystąpieniem do ochrony obciążeń należy zainstalować pakiet zbiorczy aktualizacji 1 dla Azure Backup Server v3. Aby wyświetlić listę poprawek błędów i instrukcje dotyczące instalacji pakietu zbiorczego aktualizacji Azure Backup Server v3 1, zobacz artykuł w bazie wiedzy [4534062](https://support.microsoft.com/en-us/help/4534062/).

## <a name="add-storage-to-azure-backup-server"></a>Dodawanie magazynu do usługi Azure Backup Server

Azure Backup Server wersja 3 obsługuje Nowoczesny magazyn kopii zapasowych oferujący:

-  Oszczędności magazynu wynoszące 50%.
-  Kopie zapasowe są trzy razy szybsze.
-  Wydajniejszy magazyn.
-  Magazyn z obsługą obciążeń.

### <a name="volumes-in-azure-backup-server"></a>Woluminy w Azure Backup Server

Dodaj dyski danych z wymaganą pojemnością magazynu do maszyny wirtualnej Azure Backup Server, jeśli nie został jeszcze dodany.

Azure Backup Server v3 akceptuje tylko woluminy magazynu. Po dodaniu woluminu Program Azure Backup Server formatuje wolumin w systemie plików ReFS, który Nowoczesny magazyn kopii zapasowych wymaga.

### <a name="add-volumes-to-azure-backup-server-disk-storage"></a>Dodawanie woluminów do magazynu Azure Backup Server dysku

1. W okienku **zarządzania** ponownie Przeskanuj magazyn, a następnie wybierz pozycję **Dodaj**. 

1. Wybierz spośród dostępnych woluminów, które mają zostać dodane do puli magazynów. 

1. Po dodaniu dostępnych woluminów nadaj im przyjazną nazwę ułatwiającą zarządzanie nimi. 

1. Wybierz **przycisk OK** , aby sformatować te woluminy do systemu plików ReFS, aby Azure Backup Server mogły korzystać z zalet nowoczesny magazyn kopii zapasowych.

![Dodaj dostępne woluminy](../backup/media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego samouczka, aby dowiedzieć się, jak skonfigurować tworzenie kopii zapasowych maszyn wirtualnych VMware działających na platformie Azure VMware przy użyciu Azure Backup Server.

> [!div class="nextstepaction"]
> [Konfigurowanie kopii zapasowych maszyn wirtualnych rozwiązań VMware platformy Azure](backup-avs-vms-with-mabs.md)

