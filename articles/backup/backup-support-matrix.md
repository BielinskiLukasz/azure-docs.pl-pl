---
title: Tabela obsługi usługi Azure Backup
description: Zawiera podsumowanie ustawień obsługi i ograniczeń dotyczących usługi Azure Backup.
ms.topic: conceptual
ms.date: 02/17/2019
ms.openlocfilehash: 2c33c71e579cc6fa5d01ba086fb1a9a4fc9c142c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172065"
---
# <a name="support-matrix-for-azure-backup"></a>Macierz obsługi dla Azure Backup

Za pomocą [Azure Backup](backup-overview.md) można tworzyć kopie zapasowe danych na platformie Microsoft Azure w chmurze. Ten artykuł zawiera podsumowanie ogólnych ustawień pomocy technicznej i ograniczeń dotyczących Azure Backup scenariuszy i wdrożeń.

Dostępne są inne macierze pomocy technicznej:

- Macierz obsługi dla [kopii zapasowej maszyny wirtualnej platformy Azure](backup-support-matrix-iaas.md)
- Macierz obsługi dla kopii zapasowej przy użyciu programu [System Center Data Protection Manager (DPM)/Microsoft Azure Backup Server (serwera usługi MAB)](backup-support-matrix-mabs-dpm.md)
- Macierz obsługi kopii zapasowej przy użyciu [agenta Microsoft Azure Recovery Services (MARS)](backup-support-matrix-mars-agent.md)

## <a name="vault-support"></a>Obsługa magazynu

Azure Backup używa magazynów Recovery Services, aby organizować kopie zapasowe i zarządzać nimi. Używa również magazynów do przechowywania danych kopii zapasowej.

W poniższej tabeli opisano funkcje magazynów Recovery Services:

**Funkcja** | **Szczegóły**
--- | ---
**Magazyny w subskrypcji** | Do 500 magazynów usługi Recovery Services w ramach jednej subskrypcji.
**Maszyny w magazynie** | Do 1 000 maszyn wirtualnych platformy Azure w jednym magazynie.<br/><br/> Do 50 serwerów serwera usługi MAB można zarejestrować w pojedynczym magazynie.
**Źródła danych w magazynie magazynu** | Maksymalna 54 400 GB. Nie ma limitu kopii zapasowych maszyn wirtualnych platformy Azure.
**Kopie zapasowe w magazynie** | **Maszyny wirtualne platformy Azure:** Raz dziennie.<br/><br/>**Maszyny chronione przez program DPM/serwera usługi MAB:** Dwa razy dziennie.<br/><br/> **Maszyny z kopią zapasową bezpośrednio przy użyciu agenta Mars:** Trzy razy dziennie.
**Kopie zapasowe między magazynami** | Kopia zapasowa znajduje się w regionie.<br/><br/> Potrzebujesz magazynu w każdym regionie świadczenia usługi Azure, który zawiera maszyny wirtualne, dla których chcesz utworzyć kopię zapasową. Nie można utworzyć kopii zapasowej w innym regionie.
**Przenoszenie magazynów** | [Magazyny można przenosić](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) między subskrypcjami lub między grupami zasobów w ramach tej samej subskrypcji.
**Przenoszenie danych między magazynami** | Przeniesienie danych kopii zapasowej między magazynami nie jest obsługiwane.
**Modyfikuj typ magazynu magazynów** | Możesz zmodyfikować typ replikacji magazynu (magazyn Geograficznie nadmiarowy lub Magazyn lokalnie nadmiarowy) dla magazynu przed zapisaniem kopii zapasowych. Po rozpoczęciu wykonywania kopii zapasowych w magazynie nie można zmienić typu replikacji.

## <a name="on-premises-backup-support"></a>Obsługa lokalnych kopii zapasowych

Oto nowości obsługiwane, jeśli chcesz utworzyć kopię zapasową maszyn lokalnych:

**Maszyna** | **Co to jest kopia zapasowa** | **Lokalizacja** | **Funkcje**
--- | --- | --- | ---
**Bezpośrednia kopia zapasowa maszyny z systemem Windows z agentem MARS** | Pliki, foldery, stan systemu | Utwórz kopię zapasową w magazynie Recovery Services. | Tworzenie kopii zapasowej trzy razy dziennie<br/><br/> Brak kopii zapasowej obsługującej aplikacje<br/><br/> Przywróć plik, folder, wolumin
**Bezpośrednie tworzenie kopii zapasowej maszyny z systemem Linux przy użyciu agenta MARS** | Kopia zapasowa nie jest obsługiwana
**Tworzenie kopii zapasowej w programie DPM** | Pliki, foldery, woluminy, stan systemu, dane aplikacji | Utwórz kopię zapasową do lokalnego magazynu programu DPM. Program DPM wykonuje kopie zapasowe w magazynie. | Migawki z uwzględnieniem aplikacji<br/><br/> Pełny stopień szczegółowości tworzenia kopii zapasowych i odzyskiwania<br/><br/> System Linux jest obsługiwany w przypadku maszyn wirtualnych (Hyper-V/VMware)<br/><br/> Oracle nie jest obsługiwany
**Utwórz kopię zapasową do serwera usługi MAB** | Pliki, foldery, woluminy, stan systemu, dane aplikacji | Utwórz kopię zapasową do magazynu lokalnego serwera usługi MAB. Usługa MABS wykonuje kopie zapasowe w magazynie. | Migawki z uwzględnieniem aplikacji<br/><br/> Pełny stopień szczegółowości tworzenia kopii zapasowych i odzyskiwania<br/><br/> System Linux jest obsługiwany w przypadku maszyn wirtualnych (Hyper-V/VMware)<br/><br/> Oracle nie jest obsługiwany

## <a name="azure-vm-backup-support"></a>Obsługa kopii zapasowych maszyny wirtualnej platformy Azure

### <a name="azure-vm-limits"></a>Limity maszyn wirtualnych platformy Azure

**Limit** | **Szczegóły**
--- | ---
**Dyski danych maszyny wirtualnej platformy Azure** | Limit wynoszący 16
**Rozmiar dysku danych maszyny wirtualnej platformy Azure** | Rozmiar poszczególnych dysków może należeć do 32 TB i maksymalnie 256 TB łączny dla wszystkich dysków w maszynie wirtualnej.

### <a name="azure-vm-backup-options"></a>Opcje kopii zapasowych maszyn wirtualnych platformy Azure

Oto nowości obsługiwane, jeśli chcesz utworzyć kopię zapasową maszyn wirtualnych platformy Azure:

**Maszyna** | **Co to jest kopia zapasowa** | **Lokalizacja** | **Funkcje**
--- | --- | --- | ---
**Kopia zapasowa maszyny wirtualnej platformy Azure przy użyciu rozszerzenia maszyny wirtualnej** | Cała maszyna wirtualna | Utwórz kopię zapasową w magazynie. | Rozszerzenie zainstalowane po włączeniu kopii zapasowej dla maszyny wirtualnej.<br/><br/> Utwórz kopię zapasową raz dziennie.<br/><br/> Kopia zapasowa obsługująca aplikacje dla maszyn wirtualnych z systemem Windows; kopia zapasowa spójna na plikach dla maszyn wirtualnych z systemem Linux. Można skonfigurować spójność aplikacji dla maszyn z systemem Linux za pomocą skryptów niestandardowych.<br/><br/> Przywróć maszynę wirtualną lub dysk.<br/><br/> Nie można utworzyć kopii zapasowej maszyny wirtualnej platformy Azure w lokalizacji lokalnej.
**Kopia zapasowa maszyny wirtualnej platformy Azure przy użyciu agenta MARS** | Pliki, foldery, stan systemu | Utwórz kopię zapasową w magazynie. | Utwórz kopię zapasową trzy razy dziennie.<br/><br/> Jeśli chcesz utworzyć kopię zapasową określonych plików lub folderów zamiast całej maszyny wirtualnej, Agent MARS może działać obok rozszerzenia maszyny wirtualnej.
**Maszyna wirtualna platformy Azure z programem DPM** | Pliki, foldery, woluminy, stan systemu, dane aplikacji | Wykonaj kopię zapasową do lokalnego magazynu maszyny wirtualnej platformy Azure, na którym działa program DPM. Program DPM wykonuje kopie zapasowe w magazynie. | Migawki z obsługą aplikacji.<br/><br/> Pełny stopień szczegółowości na potrzeby kopii zapasowych i odzyskiwania.<br/><br/> System Linux obsługujący maszyny wirtualne (Hyper-V/VMware).<br/><br/> Oprogramowanie Oracle nie jest obsługiwane.
**Maszyna wirtualna platformy Azure z usługą MABS** | Pliki, foldery, woluminy, stan systemu, dane aplikacji | Utwórz kopię zapasową do lokalnego magazynu maszyny wirtualnej platformy Azure z systemem serwera usługi MAB. Usługa MABS wykonuje kopie zapasowe w magazynie. | Migawki z obsługą aplikacji.<br/><br/> Pełny stopień szczegółowości na potrzeby kopii zapasowych i odzyskiwania.<br/><br/> System Linux obsługujący maszyny wirtualne (Hyper-V/VMware).<br/><br/> Oprogramowanie Oracle nie jest obsługiwane.

## <a name="linux-backup-support"></a>Obsługa kopii zapasowych systemu Linux

W tym miejscu nowości są obsługiwane, jeśli chcesz utworzyć kopię zapasową maszyn z systemem Linux:

**Typ kopii zapasowej** | **Linux (zatwierdzony przez Azure)**
--- | ---
**Bezpośrednie tworzenie kopii zapasowej maszyny lokalnej z systemem Linux** | Nieobsługiwane. Agenta MARS można zainstalować tylko na komputerach z systemem Windows.
**Używanie rozszerzenia agenta do tworzenia kopii zapasowej maszyny wirtualnej platformy Azure z systemem Linux** | Tworzenie kopii zapasowej spójnej na poziomie aplikacji przy użyciu [skryptów niestandardowych](backup-azure-linux-app-consistent.md).<br/><br/> Odzyskiwanie na poziomie plików.<br/><br/> Przywracanie przez utworzenie maszyny wirtualnej z punktu odzyskiwania lub dysku.
**Używanie programu DPM do tworzenia kopii zapasowej lokalnej lub maszyny wirtualnej platformy Azure z systemem Linux** | Spójna z plikami kopia zapasowa maszyn wirtualnych gościa systemu Linux w funkcji Hyper-V i oprogramowaniu VMWare.<br/><br/> Przywracanie maszyny wirtualnej z maszynami wirtualnymi funkcji Hyper-V i VMWare Linux.<br/><br/> Kopia zapasowa spójna na poziomie plików nie jest dostępna dla maszyny wirtualnej platformy Azure.
**Tworzenie kopii zapasowej maszyny lokalnej lub maszyny wirtualnej platformy Azure z systemem Linux przy użyciu programu serwera usługi MAB** | Spójna z plikami kopia zapasowa maszyn wirtualnych gościa systemu Linux w funkcji Hyper-V i oprogramowaniu VMWare.<br/><br/> Przywracanie maszyny wirtualnej z maszynami wirtualnymi funkcji Hyper-V i VMWare Linux.<br/><br/> Spójna na poziomie plików kopia zapasowa niedostępna dla maszyn wirtualnych platformy Azure.

## <a name="daylight-saving-time-support"></a>Obsługa czasu letniego

Azure Backup nie obsługuje automatycznego dostosowania zegara w przypadku czasu letniego dla kopii zapasowych maszyny wirtualnej platformy Azure. Zmodyfikuj ręcznie zasady tworzenia kopii zapasowych zgodnie z potrzebami.

## <a name="disk-deduplication-support"></a>Obsługa deduplikacji dysków

Obsługa deduplikacji dysku jest następująca:

- Funkcja deduplikacji dysku jest obsługiwana lokalnie, gdy program DPM lub serwera usługi MAB wykonuje kopie zapasowe maszyn wirtualnych funkcji Hyper-V z systemem Windows. System Windows Server wykonuje deduplikację danych (na poziomie hosta) na wirtualnych dyskach twardych (VHD) dołączonych do maszyny wirtualnej jako magazyn kopii zapasowych.
- Funkcja deduplikacji nie jest obsługiwana na platformie Azure dla żadnego składnika usługi Backup. Po wdrożeniu programu DPM i serwera usługi MAB na platformie Azure dyski magazynu dołączone do maszyny wirtualnej nie mogą być deduplikowane.

## <a name="security-and-encryption-support"></a>Obsługa zabezpieczeń i szyfrowania

Azure Backup obsługuje szyfrowanie danych w trakcie przesyłania i w czasie spoczynku.

### <a name="network-traffic-to-azure"></a>Ruch sieciowy do platformy Azure

- Ruch kopii zapasowych z serwerów do magazynu Recovery Services jest szyfrowany przy użyciu Advanced Encryption Standard 256.
- Dane kopii zapasowej są przesyłane za pośrednictwem bezpiecznego połączenia HTTPS.
- Dane kopii zapasowej są przechowywane w magazynie Recovery Services w postaci zaszyfrowanej.
- Tylko Ty dysponujesz hasłem do odblokowania tych danych. Firma Microsoft nie może odszyfrować danych kopii zapasowej w żadnym punkcie.

    > [!WARNING]
    > Po skonfigurowaniu magazynu tylko Ty masz dostęp do klucza szyfrowania. Firma Microsoft nigdy nie przechowuje kopii i nie ma dostępu do klucza. W przypadku utraty klucza firma Microsoft nie może odzyskać danych kopii zapasowej.

### <a name="data-security"></a>Bezpieczeństwo danych

- Podczas tworzenia kopii zapasowych maszyn wirtualnych platformy Azure należy skonfigurować szyfrowanie *w ramach* maszyny wirtualnej.
- Usługa Azure Backup obsługuje usługę Azure Disk Encryption, która używa funkcji BitLocker na maszynach wirtualnych z systemem Windows, a programu **dm-crypt** na maszynach wirtualnych z systemem Linux.
- Na zapleczu Azure Backup używa [platformy Azure szyfrowanie usługi Storage](../storage/common/storage-service-encryption.md), która chroni dane przechowywane w spoczynku.

**Maszyna** | **Przesyłanie** | **Magazynowanie**
--- | --- | ---
**Lokalne maszyny z systemem Windows bez programu DPM/serwera usługi MAB** | ![Tak][green] | ![Tak][green]
**Maszyny wirtualne platformy Azure** | ![Tak][green] | ![Tak][green]
**Lokalne komputery z systemem Windows lub maszyny wirtualne platformy Azure z programem DPM** | ![Tak][green] | ![Tak][green]
**Lokalne komputery z systemem Windows lub maszyny wirtualne platformy Azure z usługą serwera usługi MAB** | ![Tak][green] | ![Tak][green]

## <a name="compression-support"></a>Obsługa kompresji

Funkcja Backup obsługuje kompresję ruchu kopii zapasowej, jak przedstawiono w poniższej tabeli.

- W przypadku maszyn wirtualnych platformy Azure rozszerzenie maszyny wirtualnej odczytuje dane bezpośrednio z konta usługi Azure Storage za pośrednictwem sieci magazynu, co nie wymaga kompresowania tego ruchu.
- Jeśli używasz programu DPM lub serwera usługi MAB, możesz zaoszczędzić przepustowość, kompresując dane przed utworzeniem ich kopii zapasowej.

**Maszyna** | **Kompresja do usługi MABS/programu DPM (TCP)** | **Kompresuj do magazynu (HTTPS)**
--- | --- | ---
**Bezpośrednia kopia zapasowa lokalnych maszyn z systemem Windows** | Nie dotyczy | ![Tak][green]
**Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure przy użyciu rozszerzenia maszyny wirtualnej** | Nie dotyczy | Nie dotyczy
**Tworzenie kopii zapasowych na maszynach lokalnych/na platformie Azure przy użyciu serwera usługi MAB/DPM** | ![Tak][green] | ![Tak][green]

## <a name="retention-limits"></a>Limity przechowywania

**Ustawienie** | **Limity**
--- | ---
**Maksymalna liczba punktów odzyskiwania na chronione wystąpienie (maszyna lub obciążenie)** | 9 999
**Maksymalny czas wygaśnięcia dla punktu odzyskiwania** | Bez ograniczeń
**Maksymalna częstotliwość tworzenia kopii zapasowych w programie DPM/serwera usługi MAB** | Co 15 minut dla programu SQL Server<br/><br/> Raz na godzinę dla innych obciążeń
**Maksymalna częstotliwość tworzenia kopii zapasowych w magazynie** | **Lokalne komputery z systemem Windows lub maszyny wirtualne platformy Azure z systemem Mars:** Trzy dziennie<br/><br/> **program DPM/serwera usługi MAB:** Dwa dziennie<br/><br/> **Kopia zapasowa maszyny wirtualnej platformy Azure:** Jeden dziennie
**Przechowywanie punktów odzyskiwania** | Codziennie, co tydzień, co miesiąc, co rok
**Maksymalny okres przechowywania** | Zależnie od częstotliwości wykonywania kopii zapasowych
**Punkty odzyskiwania na dysku programu DPM/serwera usługi MAB** | 64 dla serwerów plików; 448 dla serwerów aplikacji <br/><br/>Nieograniczona liczba punktów odzyskiwania na taśmie dla lokalnego programu DPM

## <a name="next-steps"></a>Następne kroki

- [Przejrzyj macierz pomocy technicznej](backup-support-matrix-iaas.md) dla kopii zapasowej maszyny wirtualnej platformy Azure.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
