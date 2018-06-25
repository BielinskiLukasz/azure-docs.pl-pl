---
title: Tworzenie kopii zapasowej plików w usłudze Azure Files — często zadawane pytania
description: Ten artykuł zawiera szczegółowe informacje dotyczące ochrony plików w udziałach plików platformy Azure.
services: backup
author: markgalioto
ms.author: markgal
ms.date: 2/21/2018
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: 7429516538c892bea04d38a98abcfb13ba960d22
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36286404"
---
# <a name="questions-about-backing-up-azure-files"></a>Pytania dotyczące tworzenia kopii zapasowej plików w usłudze Azure Files
Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowej plików usługi Azure Files. W niektórych odpowiedziach znajdują się linki do artykułów zawierających szczegółowe informacje. Pytania dotyczące usługi Azure Backup można również zadawać na [forum dyskusyjnym](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Aby szybko przeskanować sekcje znajdujące się w tym artykule, użyj linków z prawej strony w obszarze **W tym artykule**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Konfigurowanie zadania tworzenia kopii zapasowej dla usługi Azure Files

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-azure-file-shares-br"></a>Dlaczego nie widzę niektórych kont magazynu, które chcę chronić, zawierających prawidłowe udziały plików platformy Azure? <br/>
W wersji zapoznawczej kopie zapasowe udziałów plików platformy Azure nie obsługują wszystkich typów kont magazynu. Lista obsługiwanych kont magazynu znajduje się [tutaj](troubleshoot-azure-files.md#limitations-for-azure-file-share-backup-during-preview). Być może konto magazynu, którego szukasz, jest już chronione lub zarejestrowane w innym magazynie. [Wyrejestruj się](troubleshoot-azure-files.md#configuring-backup) z magazynu, aby uwidocznić konto magazynu w innych magazynach w celu zapewnienia ochrony.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup-br"></a>Gdy próbuję skonfigurować kopię zapasową, nie widzę niektórych udziałów plików platformy Azure na koncie magazynu. Dlaczego? <br/>
Sprawdź, czy udział pliku platformy Azure nie jest już chroniony w tym samym magazynie usługi Recovery Services lub nie został ostatnio usunięty.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync-br"></a>Czy mogę chronić udziały plików połączonych z grupą synchronizacji w usłudze Azure Files Sync? <br/>
Tak. Ochrona udziałów plików platformy Azure połączonych z grupami synchronizacji została włączona i jest częścią publicznej wersji zapoznawczej.

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-did-not-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Podczas próby utworzenia kopii zapasowej udziałów plików konto usługi Storage zostało kliknięte w celu odnalezienia w nim udziałów plików. Ale nie zostały one jednak objęte ochroną. Jak mogę chronić te udziały plików w innym magazynie?
Wybranie konta usługi Storage w celu odnalezienia udziałów plików podczas próby utworzenia kopii zapasowej powoduje zarejestrowanie konta usługi Storage w obrębie magazynu, z poziomu którego wykonano tę czynność. Jeśli chcesz chronić udziały plików przy użyciu innego magazynu, [wyrejestruj](troubleshoot-azure-files.md#configuring-backup) wybrane konto usługi Storage z tego magazynu.

### <a name="can-i-change-the-vault-to-which-i-backup-my-file-shares"></a>Czy mogę zmienić magazyn, w którym tworzę kopię zapasową moich udziałów plików?
Tak. Musisz jednak [zatrzymać ochronę](backup-azure-files.md#stop-protecting-an-azure-file-share) na poziomie połączonego magazynu, [wyrejestrować](troubleshoot-azure-files.md#configuring-backup) to konto usługi Storage, a następnie włączyć ochronę z poziomu innego magazynu.

### <a name="in-which-geos-can-i-back-up-azure-file-shares-br"></a>W których obszarach geograficznych można tworzyć kopie zapasowe udziałów plików usługi Azure Files? <br/>
Kopie zapasowe udziałów plików usługi Azure Files są obecnie dostępne w wersji zapoznawczej tylko w następujących obszarach geograficznych: 
-   Australia Wschodnia (AE) 
- Australia Południowo-Wschodnia (ASE) 
- Brazylia Południowa (BRS)
- Kanada Środkowa (CNC)
-   Kanada Wschodnia (CE)
-   Środkowe stany USA (CUS)
-   Azja Wschodnia (EA)
-   Wschodnie stany USA (EUS)
-   Wschodnie stany USA 2 (EUS2)
- Japonia Wschodnia (JPE)
- Japonia Zachodnia (JPW)
-   Indie Środkowe (INC) 
- Indie Południowe (INS)
- Korea Środkowa (KRC)
- Korea Południowa (KRS)
-   Północno-środkowe stany USA (NCUS) 
-   Europa Północna (NE) 
-   Południowo-środkowe stany USA (SCUS) 
-   Azja Południowo-Wschodnia (SEA)
-   Południowe Zjednoczone Królestwo (UKS) 
-   Zachodnie Zjednoczone Królestwo (UKW) 
-   Europa Zachodnia (WE) 
-   Zachodnie stany USA (WUS)
-   Zachodnio-środkowe stany USA (WCUS)
-   Zachodnie stany USA 2 (WUS 2)

Napisz na adres [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com), jeśli chcesz używać tej usługi w określonym obszarze geograficznym, którego nie ma na powyższej liście.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vaultbr"></a>Ile udziałów plików platformy Azure można chronić w magazynie?<br/>
W wersji zapoznawczej można chronić w jednym magazynie udziały plików platformy Azure znajdujące się na maksymalnie 25 kontach magazynu. Maksymalna liczba udziałów plików platformy Azure w jednym magazynie wynosi 200.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Czy można chronić dwa różne udziały plików z poziomu tego samego konta usługi Storage w różnych magazynach?
Nie. Wszystkie udziały plików na koncie usługi Storage mogą być chronione tylko przez ten sam magazyn.

## <a name="backup"></a>Backup

### <a name="how-many-on-demand-backups-can-i-take-per-file-share-br"></a>Ile kopii zapasowych na żądanie można utworzyć na jeden udział plików? <br/>
W dowolnym momencie możesz mieć maksymalnie 200 migawek na jeden udział plików. Limit obejmuje migawki wykonane przez usługę Azure Backup na podstawie zasad. Jeśli tworzenie kopii zapasowych będzie kończyć się niepowodzeniem z powodu osiągnięcia tego limitu, usuń punkty przywracania utworzone na żądanie, aby zapewnić pomyślne tworzenie kopii zapasowych w przyszłości.

### <a name="after-enabling-virtual-networks-on-my-storage-account-the-backup-of-file-shares-in-the-account-started-failing-why"></a>Po włączeniu sieci wirtualnych na koncie magazynu tworzenie kopii zapasowej udziału plików na koncie kończy się niepowodzeniem. Dlaczego?
Kopie zapasowe udziałów plików platformy Azure nie obsługują kont usługi Storage z włączonymi sieciami wirtualnymi. Wyłącz sieci wirtualne na kontach magazynu, aby pomyślnie tworzyć kopie zapasowe. 

## <a name="restore"></a>Przywracanie

### <a name="can-i-recover-from-a-deleted-azure-file-share-br"></a>Czy można odzyskać dane z usuniętego udziału plików platformy Azure? <br/>
Podczas usuwania udziału plików platformy Azure zostanie wyświetlona lista kopii zapasowych, które zostaną usunięte, z prośbą o potwierdzenie. Nie można przywrócić usuniętego udziału plików platformy Azure.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share-br"></a>Czy można przywrócić dane z kopii zapasowych, jeśli zatrzymano ochronę udziału plików platformy Azure? <br/>
Tak. Jeśli podczas zatrzymywania ochrony została wybrana opcja **Zachowaj dane kopii zapasowej**, można przywrócić dane z wszystkich istniejących punktów przywracania.

## <a name="manage-backup"></a>Zarządzanie kopiami zapasowymi

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it-br"></a>Czy mogę uzyskać dostęp do migawek wykonywanych przez usługę Azure Backup i zainstalować je? <br/>
Do wszystkich migawek wykonanych przez usługę Azure Backup możesz uzyskać dostęp, wyświetlając migawki w portalu, w programie PowerShell lub przy użyciu interfejsu wiersza polecenia. Aby dowiedzieć się więcej na temat udostępniania migawek udziału usługi Azure Files, zobacz [Omówienie migawek udziału dla usługi Azure Files (wersja zapoznawcza)](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups-br"></a>Jaki jest maksymalny czas przechowywania, który mogę skonfigurować dla kopii zapasowych? <br/>
Kopie zapasowe udziałów plików platformy Azure tworzone codziennie można przechowywać przez maksymalnie 120 dni.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share-br"></a>Co się stanie, jeśli zmienię zasady tworzenia kopii zapasowych udziału plików platformy Azure? <br/>
Po zastosowaniu nowych zasad do udziału plików będzie obowiązywał harmonogram i okres przechowywania określony przez te nowe zasady. Jeśli okres przechowywania zostanie przedłużony, istniejące punkty odzyskiwania zostaną oznaczone, aby przechowywać je zgodnie z nowymi zasadami. W przypadku skrócenia okresu przechowywania są one oznaczane do oczyszczenia w ramach następnego zadania oczyszczania i usuwane.

## <a name="see-also"></a>Zobacz też
Te informacje dotyczą wyłącznie tworzenia kopii zapasowych plików usługi Azure Files. Aby dowiedzieć się więcej na temat innych obszarów usługi Azure Backup, zobacz inne najczęściej zadawane pytania dotyczące tworzenia kopii zapasowych:
-  [Recovery Services vault FAQ (Magazyn usługi Recovery Services — często zadawane pytania)](backup-azure-backup-faq.md)
-  [Azure VM backup FAQ (Tworzenie kopii zapasowych maszyn wirtualnych — często zadawane pytania)](backup-azure-vm-backup-faq.md)
-  [Azure Backup agent FAQ (Agent usługi Azure Backup — często zadawane pytania)](backup-azure-file-folder-backup-faq.md)
