---
title: Zainstaluj Azure Backup Server na Azure Stack | Microsoft Docs
description: Użyj Azure Backup Server do ochrony lub tworzenia kopii zapasowych obciążeń w Azure Stack.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: raynew
ms.openlocfilehash: 77f418a37c47850cf8a763dc399109d86925feff
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465260"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Instalowanie składnika Azure Backup Server w usłudze Azure Stack

W tym artykule wyjaśniono, jak zainstalować Azure Backup Server w Azure Stack. Za pomocą Azure Backup Server można chronić obciążenia infrastruktury jako usługi (IaaS), takie jak maszyny wirtualne działające w Azure Stack. Zaletą korzystania z Azure Backup Server do ochrony obciążeń jest możliwość zarządzania całą ochroną obciążeń z poziomu pojedynczej konsoli.

> [!NOTE]
> Aby dowiedzieć się więcej o możliwościach zabezpieczeń, zapoznaj się z [dokumentacją dotyczącą funkcji zabezpieczeń Azure Backup](backup-azure-security-feature.md).
>

## <a name="azure-backup-server-protection-matrix"></a>Macierz ochrony usługi Azure Backup Server
Azure Backup Server chroni następujące Azure Stack obciążeń maszyn wirtualnych.

| Chronione źródło danych | Ochrona i odzyskiwanie |
| --------------------- | ----------------------- |
| Półroczny kanał systemu Windows Server — centrum danych/Enterprise/Standard | Woluminy, pliki, foldery |
| Windows Server 2016 — Datacenter/Enterprise/Standard | Woluminy, pliki, foldery |
| Windows Server 2012 R2 — Datacenter/Enterprise/Standard | Woluminy, pliki, foldery |
| Windows Server 2012 — Datacenter/Enterprise/Standard | Woluminy, pliki, foldery |
| Windows Server 2008 R2 — Datacenter/Enterprise/Standard | Woluminy, pliki, foldery |
| SQL Server 2016 | Database (Baza danych) |
| SQL Server 2014 | Database (Baza danych) |
| SQL Server 2012 SP1 | Database (Baza danych) |
| Program SharePoint 2016 | Farma, baza danych, fronton, serwer sieci Web |
| SharePoint 2013 | Farma, baza danych, fronton, serwer sieci Web |
| SharePoint 2010 | Farma, baza danych, fronton, serwer sieci Web |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Wymagania wstępne dotyczące środowiska Azure Backup Server

Przed zainstalowaniem Azure Backup Server w środowisku Azure Stack należy wziąć pod uwagę zalecenia przedstawione w tej sekcji. Instalator Azure Backup Server sprawdza, czy środowisko ma wymagane wymagania wstępne, ale można zaoszczędzić czas przed zainstalowaniem programu.

### <a name="determining-size-of-virtual-machine"></a>Określanie rozmiaru maszyny wirtualnej
Aby uruchomić Azure Backup Server na maszynie wirtualnej Azure Stack, Użyj rozmiaru a2 lub większego. Aby uzyskać pomoc w wyborze rozmiaru maszyny wirtualnej, Pobierz [Kalkulator rozmiaru maszyny wirtualnej Azure Stack](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Sieci wirtualne na Azure Stack maszynach wirtualnych
Wszystkie maszyny wirtualne używane w obciążeniu Azure Stack muszą należeć do tej samej sieci wirtualnej platformy Azure i subskrypcji platformy Azure.

### <a name="azure-backup-server-vm-performance"></a>Azure Backup Server wydajności maszyny wirtualnej
Jeśli są udostępniane innym maszynom wirtualnym, wpływ rozmiaru konta magazynu i liczby operacji we/wy na wydajność maszyny wirtualnej Azure Backup Server. Z tego powodu należy użyć oddzielnego konta magazynu dla maszyny wirtualnej Azure Backup Server. Agent Azure Backup uruchomiony na Azure Backup Server potrzebuje magazynu tymczasowego dla:
- własne użycie (lokalizacja pamięci podręcznej),
- przywrócono dane z chmury (lokalny obszar przemieszczania)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Konfigurowanie Azure Backup tymczasowego magazynu dyskowego
Każda Azure Stack maszyny wirtualnej zawiera tymczasowy magazyn dyskowy, który jest dostępny dla użytkownika jako wolumin `D:\`. Lokalny obszar przemieszczania wymagany przez Azure Backup można skonfigurować do przechowywania w programie `D:\`, a lokalizacja pamięci podręcznej może zostać `C:\`umieszczona. W ten sposób nie trzeba używać miejsca magazynu z dysków danych podłączonych do maszyny wirtualnej Azure Backup Server.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Przechowywanie danych kopii zapasowej na dysku lokalnym i na platformie Azure
Azure Backup Server przechowuje dane kopii zapasowych na dyskach platformy Azure dołączonych do maszyny wirtualnej na potrzeby odzyskiwania operacyjnego. Gdy dyski i miejsce do magazynowania zostaną dołączone do maszyny wirtualnej, Azure Backup Server zarządza magazynem. Ilość miejsca w magazynie danych kopii zapasowej zależy od liczby i rozmiaru dysków dołączonych do każdej [Azure Stack maszyny wirtualnej](/azure-stack/user/azure-stack-storage-overview). Każdy rozmiar maszyny wirtualnej Azure Stack ma maksymalną liczbę dysków, które można dołączyć do maszyny wirtualnej. Na przykład a2 to cztery dyski. A3 to osiem dysków. A4 to 16 dysków. Ponownie rozmiar i liczba dysków określają całkowitą pulę magazynów kopii zapasowych.

> [!IMPORTANT]
> Danych odzyskiwania operacyjnego (Backup) **nie** należy przechowywać na dyskach dołączonych Azure Backup Server przez więcej niż pięć dni.
>

Przechowywanie danych kopii zapasowej na platformie Azure zmniejsza infrastrukturę tworzenia kopii zapasowych na Azure Stack. Jeśli dane są starsze niż pięć dni, powinny być przechowywane na platformie Azure.

Aby przechowywać dane kopii zapasowej na platformie Azure, Utwórz magazyn Recovery Services lub użyj go. Podczas przygotowywania do tworzenia kopii zapasowej obciążenia Azure Backup Server należy [skonfigurować magazyn Recovery Services](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). Po skonfigurowaniu programu przy każdym uruchomieniu zadania tworzenia kopii zapasowej w magazynie zostaje utworzony punkt odzyskiwania. Każdy magazyn Recovery Services zawiera maksymalnie 9999 punktów odzyskiwania. W zależności od liczby utworzonych punktów odzyskiwania i czasu ich przechowywania można zachować dane kopii zapasowej przez wiele lat. Można na przykład utworzyć miesięczne punkty odzyskiwania i zachować je przez pięć lat.
 
### <a name="scaling-deployment"></a>Skalowanie wdrożenia
Jeśli chcesz skalować wdrożenie, masz następujące opcje:
  - Skalowanie w górę — zwiększenie rozmiaru Azure Backup Server maszyny wirtualnej z serii do D i zwiększenie magazynu lokalnego [na Azure Stack instrukcji maszyny wirtualnej](/azure-stack/user/azure-stack-manage-vm-disks).
  - Odciążanie danych — Wysyłanie starszych danych na platformę Azure i zachowywanie tylko najnowszych danych w magazynie dołączonym do Azure Backup Server.
  - Skalowanie w poziomie — Dodaj więcej serwerów Azure Backup, aby chronić obciążenia.

### <a name="net-framework"></a>.NET Framework

Na maszynie wirtualnej musi być zainstalowany .NET Framework 3,5 z dodatkiem SP1 lub nowszy.

### <a name="joining-a-domain"></a>Przyłączanie do domeny

Maszyna wirtualna Azure Backup Server musi być przyłączona do domeny. Użytkownik domeny z uprawnieniami administratora musi zainstalować Azure Backup Server na maszynie wirtualnej.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Używanie maszyny wirtualnej IaaS w Azure Stack

Wybierając serwer dla Azure Backup Server, Zacznij od obrazu z galerii systemu Windows Server 2012 R2 Datacenter lub Windows Server 2016 Datacenter. W tym artykule opisano [Tworzenie pierwszej maszyny wirtualnej z systemem Windows w Azure Portal, w](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)której znajduje się samouczek z wprowadzeniem do zalecanej maszyny wirtualnej. Zalecane minimalne wymagania dotyczące maszyny wirtualnej serwera: A2 Standard z dwoma rdzeniami i 3,5 GB pamięci RAM.

Ochrona obciążeń za pomocą Azure Backup Server ma wiele wszystkie szczegóły. W tym artykule [Zainstaluj program DPM jako maszynę wirtualną platformy Azure, a także](https://technet.microsoft.com/library/jj852163.aspx)Wyjaśnij te wszystkie szczegóły. Przed wdrożeniem maszyny zapoznaj się z tym artykułem całkowicie.

> [!NOTE]
> Azure Backup Server jest przeznaczony do działania na dedykowanej maszynie wirtualnej o pojedynczym przeznaczeniu. Nie można zainstalować Azure Backup Server na:
> - Komputer działa jako kontroler domeny
> - Komputer, na którym jest zainstalowana rola serwera aplikacji
> - Komputer, na którym jest uruchomiony program Exchange Server
> - Komputer, który jest węzłem klastra

Zawsze dołączaj Azure Backup Server do domeny. Jeśli musisz przenieść Azure Backup Server do innej domeny, najpierw zainstaluj Azure Backup Server, a następnie Dołącz do nowej domeny. Po wdrożeniu Azure Backup Server nie można przenieść go do nowej domeny.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Konfigurowanie replikacji magazynu

Opcja replikacji magazynu Recovery Services Storage umożliwia wybranie między magazynem geograficznie nadmiarowym i magazynem lokalnym nadmiarowym. Domyślnie magazyny Recovery Services korzystają z magazynu geograficznie nadmiarowego. Jeśli magazyn jest magazynem podstawowym, pozostaw opcję magazynu ustawioną na magazyn Geograficznie nadmiarowy. Wybierz pozycję Magazyn lokalnie nadmiarowy, jeśli chcesz skorzystać z tańszej opcji, która jest mniej trwała. Więcej informacji o opcjach magazynu [geograficznie nadmiarowego](../storage/common/storage-redundancy-grs.md) i [lokalnie nadmiarowego](../storage/common/storage-redundancy-lrs.md) można znaleźć w temacie [Azure Storage replication overview](../storage/common/storage-redundancy.md) (Omówienie replikacji usługi Azure Storage).

Aby edytować ustawienia replikacji magazynu:

1. Wybierz swój magazyn, aby otworzyć pulpit nawigacyjny magazynu i menu Ustawienia. Jeśli menu **Ustawienia** nie jest otwarte, kliknij pozycję **wszystkie ustawienia** na pulpicie nawigacyjnym magazynu.
2. W menu **Ustawienia** kliknij pozycję **Utwórz** > kopię**zapasową** infrastruktury zapasowej, aby otworzyć menu **Konfiguracja kopii zapasowej** . W menu **Konfiguracja kopii zapasowej** wybierz opcję replikacja magazynu dla swojego magazynu.

    ![Lista magazynów kopii zapasowych](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Pobierz instalatora Azure Backup Server

Istnieją dwa sposoby pobrania instalatora Azure Backup Server. Instalatora Azure Backup Server można pobrać z [Centrum pobierania Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=55269). Możesz również pobrać Instalatora Azure Backup Server podczas konfigurowania magazynu Recovery Services. Poniższe kroki przeprowadzą Cię przez proces pobierania instalatora z Azure Portal podczas konfigurowania magazynu Recovery Services.

1. Na maszynie wirtualnej Azure Stack Zaloguj się [do subskrypcji platformy Azure w Azure Portal](https://portal.azure.com/).
2. W menu po lewej stronie wybierz pozycję **wszystkie usługi**.

    ![Wybierz opcję Wszystkie usługi w menu głównym](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. W oknie dialogowym **wszystkie usługi** wpisz *Recovery Services*. Po rozpoczęciu wpisywania dane wejściowe filtrują listę zasobów. Po wyświetleniu tej opcji wybierz pozycję **magazyny Recovery Services**.

    ![Wpisz Recovery Services w oknie dialogowym wszystkie usługi](./media/backup-mabs-install-azure-stack/all-services.png)

    Zostanie wyświetlona lista magazynów usługi Recovery Services w ramach subskrypcji.

4. Z listy Recovery Services magazynów wybierz swój magazyn, aby otworzyć jego pulpit nawigacyjny.

    ![Wpisz Recovery Services w oknie dialogowym wszystkie usługi](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. W menu Wprowadzenie magazynu kliknij pozycję **kopia zapasowa** , aby otworzyć Kreatora wprowadzenie.

    ![Wprowadzenie do kopii zapasowej](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Zostanie otwarte menu kopia zapasowa.

    ![Tworzenie kopii zapasowych — cele domyślne — otwierane](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. W menu kopia zapasowa, w menu **gdzie jest uruchomione Twoje obciążenie** , wybierz pozycję **lokalnie**. Z menu rozwijanego **co chcesz utworzyć kopię zapasową?** wybierz obciążenia, które mają być chronione przy użyciu Azure Backup Server. Jeśli nie masz pewności, które obciążenia do wyboru, wybierz **Virtual Machines funkcji Hyper-V** , a następnie kliknij pozycję **Przygotuj infrastrukturę**.

    ![lokalne i obciążenia jako cele](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    Zostanie otwarte menu **Przygotuj infrastrukturę** .

7. W menu **Przygotowywanie infrastruktury** kliknij pozycję **Pobierz** , aby otworzyć stronę sieci Web w celu pobrania plików instalacyjnych programu Azure Backup Server.

    ![Zmiana kreatora Wprowadzenie](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    Zostanie otwarta strona sieci Web firmy Microsoft, która hostuje pliki do pobrania dla Azure Backup Server.

8. Na stronie pobierania serwera Microsoft Azure Backup wybierz język, a następnie kliknij pozycję **Pobierz**.

    ![Zostanie otwarte Centrum pobierania](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Instalator Azure Backup Server składa się z ośmiu plików — Instalatora i siedmiu plików bin. Sprawdź **nazwę pliku** , aby zaznaczyć wszystkie wymagane pliki, a następnie kliknij przycisk **dalej**. Pobierz wszystkie pliki do tego samego folderu.

    ![Centrum pobierania 1](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    Rozmiar pobierania wszystkich plików instalacyjnych jest większy niż 3 GB. W przypadku linku pobierania 10 MB/s pobieranie wszystkich plików instalacyjnych może potrwać do 60 minut. Pliki są pobierane do określonej lokalizacji pobierania.

## <a name="extract-azure-backup-server-install-files"></a>Wyodrębnij pliki instalacji Azure Backup Server

Po pobraniu wszystkich plików na maszynę wirtualną Azure Stack przejdź do lokalizacji pobierania. Pierwszym etapem instalacji Azure Backup Server jest wyodrębnienie plików.

![Centrum pobierania 1](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Aby rozpocząć instalację, z listy pobranych plików kliknij plik **MicrosoftAzureBackupserverInstaller. exe**.

    > [!WARNING]
    > Do wyodrębnienia plików instalacyjnych wymagane jest co najmniej 4 GB wolnego miejsca.
    >

2. W Kreatorze Azure Backup Server kliknij przycisk **dalej** , aby kontynuować.

    ![Kreator instalacji Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Wybierz ścieżkę do Azure Backup Server plików, a następnie kliknij przycisk **dalej**.

   ![Kreator instalacji Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Sprawdź lokalizację wyodrębniania, a następnie kliknij pozycję Wyodrębnij.

   ![Kreator instalacji Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. Kreator wyodrębnia pliki i odczytuje proces instalacji.

   ![Kreator instalacji Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Po zakończeniu procesu wyodrębniania kliknij przycisk **Zakończ**. Domyślnie jest wybrana wartość **Wykonaj plik Setup. exe** . Po kliknięciu przycisku **Zakończ**program Setup. exe zainstaluje Microsoft Azure Backup Server w określonej lokalizacji.

   ![Kreator instalacji Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Zainstaluj pakiet oprogramowania

W poprzednim kroku kliknięto przycisk **Zakończ** , aby zakończyć fazę wyodrębniania, i uruchomić kreatora instalacji Azure Backup Server.

![Kreator instalacji Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Azure Backup Server udostępnia kod z Data Protection Manager. W instalatorze Azure Backup Server pojawią się odwołania do Data Protection Manager i programu DPM. Chociaż Azure Backup Server i Data Protection Manager są oddzielnymi produktami, te produkty są ściśle powiązane.

1. Aby uruchomić Kreatora instalacji, kliknij przycisk **Microsoft Azure Backup Server**.

   ![Kreator instalacji Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Na ekranie **powitalnym** kliknij przycisk **dalej**.

    ![Azure Backup Server-Witamy i sprawdzanie wymagań wstępnych](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Na ekranie **Sprawdzanie wymagań wstępnych** kliknij przycisk **Sprawdź** , aby określić, czy zostały spełnione wymagania wstępne dotyczące sprzętu i oprogramowania Azure Backup Server.

    ![Azure Backup Server-Witamy i sprawdzanie wymagań wstępnych](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Jeśli środowisko ma wymagane wymagania wstępne, zostanie wyświetlony komunikat informujący o tym, że komputer spełnia wymagania. Kliknij przycisk **Dalej**.  

    ![Zakończono sprawdzanie wymagań wstępnych Azure Backup Server](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Jeśli środowisko nie spełnia wymagań wstępnych, zostaną określone problemy. Wymagania wstępne, które nie zostały spełnione, są również wymienione w dzienniku DpmSetup. log. Usuń błędy wymagań wstępnych, a następnie **ponownie uruchom sprawdzenie**. Nie można przeprowadzić instalacji, dopóki nie zostaną spełnione wszystkie wymagania wstępne.

    ![Azure Backup Server — wymagania wstępne instalacji nie zostały spełnione](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Serwer Microsoft Azure Backup wymaga SQL Server. Pakiet instalacyjny Azure Backup Server jest powiązany z odpowiednimi SQL Server plikami binarnymi. Jeśli chcesz użyć własnej instalacji programu SQL, możesz. Zaleca się jednak, aby Instalator dodał nowe wystąpienie SQL Server. Aby upewnić się, że wybór działa z Twoim środowiskiem, kliknij przycisk **Sprawdź i zainstaluj**.

   > [!NOTE]
   > Azure Backup Server nie będzie działał ze zdalnym wystąpieniem SQL Server. Wystąpienie używane przez Azure Backup Server musi być lokalne.
   >

    ![Azure Backup Server-Witamy i sprawdzanie wymagań wstępnych](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Po sprawdzeniu, czy maszyna wirtualna ma wymagane wymagania wstępne dotyczące instalacji Azure Backup Server, kliknij przycisk **dalej**.

    ![Azure Backup Server-Witamy i sprawdzanie wymagań wstępnych](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Jeśli wystąpi błąd z zaleceniem ponownego uruchomienia komputera, należy ponownie uruchomić maszynę. Po ponownym uruchomieniu maszyny Uruchom ponownie Instalatora i po wyświetleniu ekranu **Ustawienia SQL** kliknij przycisk **Sprawdź ponownie**.

5. W **ustawieniach instalacji**Podaj lokalizację instalacji plików serwera Microsoft Azure Backup i kliknij przycisk **dalej**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    Lokalizacja tymczasowa jest wymagana do utworzenia kopii zapasowej na platformie Azure. Upewnij się, że rozmiar lokalizacji tymczasowej odpowiada co najmniej 5% danych planowanych do utworzenia kopii zapasowej na platformie Azure. W przypadku ochrony dysków należy skonfigurować oddzielne dyski po zakończeniu instalacji. Aby uzyskać więcej informacji na temat pul magazynów, zobacz [Konfigurowanie pul magazynów i magazynu dyskowego](https://technet.microsoft.com/library/hh758075.aspx).

6. Na ekranie **Ustawienia zabezpieczeń** Podaj silne hasło dla kont użytkowników lokalnych z ograniczeniami, a następnie kliknij przycisk **dalej**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. Na ekranie **nieMicrosoft Update** wybierz, czy chcesz użyć *Microsoft Update* do sprawdzenia dostępności aktualizacji, a następnie kliknij przycisk **dalej**.

   > [!NOTE]
   > Zalecamy, aby Windows Update przekierować do Microsoft Update, który oferuje bezpieczeństwo i ważne aktualizacje systemu Windows oraz innych produktów, takich jak Microsoft Azure Backup Server.
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Przejrzyj *Podsumowanie ustawień* i kliknij przycisk **Instaluj**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Po zakończeniu instalacji Azure Backup Server Instalator natychmiast uruchomi Instalatora agenta Microsoft Azure Recovery Services.

9. Zostanie otwarty Instalator agenta Microsoft Azure Recovery Services i zostanie wyszukany łączność z Internetem. Jeśli dostępna jest łączność z Internetem, Kontynuuj instalację. W przypadku braku łączności Podaj szczegóły serwera proxy, aby połączyć się z Internetem. Po określeniu ustawień serwera proxy kliknij przycisk **dalej**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Aby zainstalować agenta Microsoft Azure Recovery Services, kliknij przycisk **Instaluj**.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    Agent Microsoft Azure Recovery Services, nazywany również agentem Azure Backup, konfiguruje Azure Backup Server do magazynu Recovery Services. Po skonfigurowaniu Azure Backup Server będzie zawsze tworzyć kopie zapasowe danych w tym samym magazynie Recovery Services.

11. Po zakończeniu instalacji agenta Microsoft Azure Recovery Services kliknij przycisk **dalej** , aby rozpocząć następną fazę: rejestrowanie Azure Backup Server z magazynem Recovery Services.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    Instalator uruchamia **Kreatora rejestrowania serwera**.

12. Przejdź do swojej subskrypcji platformy Azure i magazynu Recovery Services. W menu **Przygotowywanie infrastruktury** kliknij pozycję **Pobierz** , aby pobrać poświadczenia magazynu. Jeśli przycisk **Pobierz** w kroku 2 nie jest aktywny, wybierz opcję **już pobrane lub użyj najnowszej instalacji Azure Backup Server** , aby aktywować przycisk. Poświadczenia magazynu są pobierane do lokalizacji, w której są przechowywane pliki do pobrania. Należy pamiętać o tej lokalizacji, ponieważ będzie ona potrzebna w następnym kroku.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. W menu **Identyfikacja magazynu** kliknij przycisk **Przeglądaj** , aby znaleźć poświadczenia magazynu Recovery Services.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    W oknie dialogowym **Wybieranie poświadczeń magazynu** przejdź do lokalizacji pobierania, wybierz swoje poświadczenia magazynu, a następnie kliknij przycisk **Otwórz**.

    Ścieżka do poświadczeń pojawia się w menu identyfikacja magazynu. Kliknij przycisk **dalej** , aby przejść do ustawienia szyfrowania.

14. W oknie dialogowym **Ustawienia szyfrowania** Podaj hasło do szyfrowania kopii zapasowych, a następnie kliknij przycisk **dalej**.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Możesz podać własne hasło lub użyć generatora hasła, aby je utworzyć. Hasło jest Twoje, a firma Microsoft nie zapisuje tego hasła ani nie będzie nim zarządzać. Aby przygotować się do awarii, Zapisz hasło w dostępnej lokalizacji.

    Po kliknięciu **przycisku dalej**Azure Backup Server zostanie zarejestrowana w magazynie Recovery Services. Instalator kontynuuje instalowanie SQL Server i Azure Backup Server.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Po zakończeniu działania Instalatora stan pokazuje, że całe oprogramowanie zostało pomyślnie zainstalowane.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    Po zakończeniu instalacji na pulpicie serwera są tworzone Azure Backup Server konsoli i Azure Backup Server programu PowerShell.

## <a name="add-backup-storage"></a>Dodawanie magazynu kopii zapasowych

Pierwsza kopia zapasowa jest przechowywana w magazynie dołączonym do maszyny Azure Backup Server. Aby uzyskać więcej informacji na temat dodawania dysków, zobacz [Dodawanie nowoczesnej kopii zapasowej magazynu](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-1801).

> [!NOTE]
> Należy dodać magazyn kopii zapasowych, nawet jeśli planujesz wysyłać dane na platformę Azure. W architekturze Azure Backup Server magazyn Recovery Services przechowuje *drugą* kopię danych, podczas gdy magazyn lokalny przechowuje pierwszą (i obligatoryjną) kopię zapasową.
>
>

## <a name="network-connectivity"></a>Łączność sieciowa

Azure Backup Server wymaga łączności z usługą Azure Backup, aby produkt działał pomyślnie. Aby sprawdzić, czy komputer ma łączność z platformą Azure, użyj ```Get-DPMCloudConnection``` polecenia cmdlet w konsoli programu PowerShell w Azure Backup Server. Jeśli dane wyjściowe polecenia cmdlet mają wartość PRAWDA, połączenie istnieje, a w przeciwnym razie nie ma łączności.

W tym samym czasie subskrypcja platformy Azure musi być w dobrej kondycji. Aby sprawdzić stan subskrypcji i zarządzać nią, zaloguj się do [portalu subskrypcji](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

Po uzyskaniu informacji o stanie łączności z platformą Azure i subskrypcji platformy Azure Możesz użyć poniższej tabeli, aby dowiedzieć się, jak to miało wpływ na oferowane funkcje tworzenia kopii zapasowej/przywracania.

| Stan łączności | Subskrypcja platformy Azure | Tworzenie kopii zapasowej na platformie Azure | Utwórz kopię zapasową na dysku | Przywróć z platformy Azure | Przywracanie z dysku |
| --- | --- | --- | --- | --- | --- |
| Połączono |Aktywne |Dopuszczeni |Dopuszczeni |Dopuszczeni |Dopuszczeni |
| Połączono |Wygaśnięcie |Zatrzymano |Zatrzymano |Dopuszczeni |Dopuszczeni |
| Połączono |Anulowanie aprowizacji |Zatrzymano |Zatrzymano |Zatrzymane i usunięte punkty odzyskiwania platformy Azure |Zatrzymano |
| Utracono łączność > 15 dni |Aktywne |Zatrzymano |Zatrzymano |Dopuszczeni |Dopuszczeni |
| Utracono łączność > 15 dni |Wygaśnięcie |Zatrzymano |Zatrzymano |Dopuszczeni |Dopuszczeni |
| Utracono łączność > 15 dni |Anulowanie aprowizacji |Zatrzymano |Zatrzymano |Zatrzymane i usunięte punkty odzyskiwania platformy Azure |Zatrzymano |

### <a name="recovering-from-loss-of-connectivity"></a>Odzyskiwanie po utracie łączności

Jeśli zapora lub serwer proxy uniemożliwia dostęp do platformy Azure, dozwolonych następujące adresy domeny w profilu zapory/serwera proxy:

- `http://www.msftncsi.com/ncsi.txt`
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*. microsoftonline.com
- \*. windows.net

Po przywróceniu łączności z platformą Azure do Azure Backup Server stan subskrypcji platformy Azure określa operacje, które można wykonać. Po **nawiązaniu połączenia**z serwerem Użyj tabeli w obszarze [łączność sieciowa](backup-mabs-install-azure-stack.md#network-connectivity) , aby wyświetlić dostępne operacje.

### <a name="handling-subscription-states"></a>Obsługa stanów subskrypcji

Istnieje możliwość zmiany stanu subskrypcji platformy Azure z *wygasłej* lub *wstrzymanej* w stanie *aktywnym* . Stan subskrypcji nie jest *aktywny*:

- Anulowanie aprowizacji subskrypcji powoduje utratę jej funkcjonalności. Przywracanie subskrypcji do *aktywnego*programu powoduje przywrócenie funkcji tworzenia kopii zapasowej/przywracania. Jeśli dane kopii zapasowej na dysku lokalnym zostały zachowane z wystarczającą ilością okresu przechowywania, dane kopii zapasowej można pobrać. Jednak dane kopii zapasowej na platformie Azure są irretrievably tracone po przejściu subskrypcji w stan *anulowania* aprowizacji.
- Subskrypcja wygasła. Zaplanowane kopie zapasowe nie są uruchamiane, gdy subskrypcja wygasła.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli Microsoft Azure Backup serwer ulegnie awarii z błędami podczas fazy instalacji (lub tworzenia kopii zapasowej lub przywracania), zobacz [dokument kody błędów](https://support.microsoft.com/kb/3041338).
Możesz również odwoływać się do [Azure Backup powiązanych często zadawanych pytań](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Następne kroki

Artykuł przygotowywanie [środowiska dla programu DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1801)zawiera informacje o obsługiwanych konfiguracjach Azure Backup Server.

Poniższe artykuły umożliwiają dokładniejsze zrozumienie ochrony obciążeń przy użyciu serwera Microsoft Azure Backup.

- [SQL Server kopia zapasowa](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
- [Kopia zapasowa programu SharePoint Server](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Alternatywna kopia zapasowa serwera](backup-azure-alternate-dpm-server.md)
