---
title: Rozwiązywanie problemów ze składnikiem Azure Backup Agent
description: Rozwiązywanie problemów z instalowaniem i rejestrowaniem agenta usługi Azure Backup
services: backup
author: saurabhsensharma
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 7/25/2018
ms.author: saurse
ms.openlocfilehash: ae2afddbf6556947ae9fdc1eb65daaae18c2c26d
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51707082"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent"></a>Rozwiązywanie problemów z Agent usług Microsoft Azure Recovery Services (MARS)

Poniżej przedstawiono sposób naprawić błędy, które można napotkać podczas konfiguracji, rejestracji, tworzenia kopii zapasowej i przywracania.

## <a name="invalid-vault-credentials-provided"></a>Podano nieprawidłowe poświadczenia magazynu
| Szczegóły błędu | Możliwe przyczyny | Zalecane akcje |
| ---     | ---     | ---    |
| **Error** </br> *Podano nieprawidłowe poświadczenia magazynu. Plik jest uszkodzony lub jest nie ma najnowszych poświadczeń skojarzonych z usługą odzyskiwania. (ID: 34513)* | <ul><li> Poświadczenia magazynu są nieprawidłowe (oznacza to, że zostały pobrane przed upływem terminu rejestracji więcej niż 48 godzin).<li>Agenta usług MARS nie może pobrać pliki do katalogu Windows Temp. <li>Poświadczenia magazynu znajdują się w lokalizacji sieciowej. <li>Protokół TLS 1.0 jest wyłączony.<li> Skonfigurowany serwer proxy blokuje połączenia. <br> |  <ul><li>Pobierz nowe poświadczenia magazynu. (**Uwaga**: Jeśli wiele plików poświadczenia magazynu zostaną pobrane wcześniej, tylko najnowsze pobrany plik jest prawidłowy w ciągu 48 godzin.) <li>Przejdź do **Opcje internetowe** > **zabezpieczeń** > **Internet**. Następnie wybierz pozycję **Poziom niestandardowy**i przewijanie, aż zostanie wyświetlony plik, Pobierz sekcji. Następnie wybierz pozycję **Włącz**.<li>Może być również konieczne dodanie tych witryn w programie Internet Explorer [Zaufane witryny](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Zmień ustawienia, aby używać serwera proxy. Następnie szczegółowo Serwer proxy serwera. <li> Zgodna daty i godziny z komputera.<li>Jeśli otrzymasz komunikat o błędzie informujący, pobierania plików nie są dozwolone, jest prawdopodobne, że istnieją dużej liczby plików w katalogu C:/Windows/Temp.<li>Przejdź do C:/Windows/Temp, a następnie sprawdź, czy istnieją więcej niż 60 000 lub 65 000 pliki z rozszerzeniem .tmp. Jeśli istnieją, należy usunąć te pliki.<li>Upewnij się, że masz zainstalowane środowisko .NET framework 4.6.2. <li>Jeśli protokół TLS 1.0 zostało wyłączone z powodu zgodności ze standardami PCI, zapoznaj się z tym [strona rozwiązywania problemów](https://support.microsoft.com/help/4022913). <li>Jeśli masz oprogramowanie antywirusowe zainstalowane na serwerze, należy wyłączyć następujące pliki ze skanowania antywirusowego: <ul><li>CBengine.exe<li>CSC.exe, który jest powiązany z .NET Framework. Brak CSC.exe dla każdej wersji platformy .NET, który jest zainstalowany na serwerze. Wyklucz pliki CSC.exe, które są powiązane z wszystkich wersji programu .NET Framework na tym serwerze. <li>Lokalizacja folderu lub pamięci podręcznej plików tymczasowych. <br>*Domyślna lokalizacja dla tymczasowy folder lub ścieżka lokalizacji pamięci podręcznej to C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.<br><li>Folder bin C:\Program Files\Microsoft Azure Recovery Services Agent\Bin


## <a name="the-mars-agent-was-unable-to-connect-to-azure-backup"></a>Agenta usług MARS nie był w stanie połączyć się z usługi Azure Backup

| Szczegóły błędu | Możliwe przyczyny | Zalecane akcje |
| ---     | ---     | ---    |
| **Error** </br><ol><li>*Agent usług Microsoft Azure Recovery Service nie może nawiązać połączenia z programem Kopia zapasowa Microsoft Azure. (IDENTYFIKATOR: 100050) Sprawdź ustawienia sieci i upewnij się, że jesteś w stanie połączyć się z Internetem*<li>*Wymagane uwierzytelnianie serwera proxy (407)* |Serwer proxy blokuje połączenia. |  <ul><li>Przejdź do **Opcje internetowe** > **zabezpieczeń** > **Internet**. Następnie wybierz pozycję **Poziom niestandardowy** i przewijanie, aż zostanie wyświetlony plik, Pobierz sekcji. Wybierz pozycję **Włącz**.<li>Może być również konieczne dodanie tych witryn w programie Internet Explorer [Zaufane witryny](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Zmień ustawienia, aby używać serwera proxy. Następnie szczegółowo Serwer proxy serwera. <li>Jeśli masz oprogramowanie antywirusowe zainstalowane na serwerze, Wyklucz następujące pliki ze skanowania oprogramowania antywirusowego. <ul><li>CBEngine.exe (zamiast dpmra.exe).<li>CSC.exe (powiązanego z .NET Framework). Brak CSC.exe dla każdej wersji platformy .NET, który jest zainstalowany na serwerze. Wyklucz pliki CSC.exe, które są powiązane z wszystkich wersji programu .NET framework na tym serwerze. <li>Lokalizacja folderu lub pamięci podręcznej plików tymczasowych. <br>*Domyślna lokalizacja dla tymczasowy folder lub ścieżka lokalizacji pamięci podręcznej to C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.<li>Folder bin C:\Program Files\Microsoft Azure Recovery Services Agent\Bin


## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nie można ustawić klucza szyfrowania na potrzeby bezpiecznych kopii zapasowych

| Szczegóły błędu | Możliwe przyczyny | Zalecane akcje |
| ---     | ---     | ---    |      
| **Error** </br>*Nie można ustawić klucza szyfrowania na potrzeby bezpiecznych kopii zapasowych. Aktywacja nie powiodła się całkowicie, ale hasło szyfrowania został zapisany w następującym pliku*. |<li>Serwer jest już zarejestrowany w innym magazynie.<li>Podczas konfigurowania hasła jest uszkodzony.| Wyrejestruj serwer z magazynu i zarejestrować go ponownie nowe hasło.

## <a name="the-activation-did-not-complete-successfully"></a>Aktywacja nie została pomyślnie ukończona.

| Szczegóły błędu | Możliwe przyczyny | Zalecane akcje |
|---------|---------|---------|
|**Error** </br><ol>*Aktywacja nie została pomyślnie ukończona. Bieżąca operacja nie powiodła się z powodu wewnętrznego błędu usługi [0x1FC07]. Spróbuj ponownie wykonać operację po pewnym czasie. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną firmy Microsoft*     | <li> Folder tymczasowy znajduje się na woluminie, który ma za mało miejsca. <li> Folder tymczasowy został niepoprawnie przeniesiony do innej lokalizacji. <li> Brakuje pliku OnlineBackup.KEK.         | <li>Uaktualnianie do [najnowszej wersji](https://aka.ms/azurebackup_agent) agenta usług MARS.<li>Przenieś pliki tymczasowe lokalizacja folderu lub pamięci podręcznej do woluminu z ilością wolnego miejsca równa 5 – 10% całkowitego rozmiaru danych kopii zapasowej. Aby poprawnie przenieść lokalizację pamięci podręcznej, skorzystaj z procedury opisanej w [pytania dotyczące agenta usługi Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Upewnij się, że plik OnlineBackup.KEK jest obecny. <br>*Domyślna lokalizacja dla tymczasowy folder lub ścieżka lokalizacji pamięci podręcznej to C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Hasło szyfrowania nie zostały prawidłowo skonfigurowane

| Szczegóły błędu | Możliwe przyczyny | Zalecane akcje |
|---------|---------|---------|
|**Error** </br><ol>*Błąd 34506. Hasło szyfrowania zapisane na tym komputerze nie jest prawidłowo skonfigurowane*.    | <li> Folder tymczasowy znajduje się na woluminie, który ma za mało miejsca. <li> Folder tymczasowy został niepoprawnie przeniesiony do innej lokalizacji. <li> Brakuje pliku OnlineBackup.KEK.        | <li>Uaktualnianie do [najnowszej wersji](https://aka.ms/azurebackup_agent) agenta usług MARS.<li>Przenieś folder tymczasowy lub lokalizację pamięci podręcznej do woluminu z wolne miejsce odpowiadające 5 – 10% całkowitego rozmiaru danych kopii zapasowej. Aby poprawnie przenieść lokalizację pamięci podręcznej, skorzystaj z procedury opisanej w [pytania dotyczące agenta usługi Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Upewnij się, że plik OnlineBackup.KEK jest obecny. <br>*Domyślna lokalizacja dla tymczasowy folder lub ścieżka lokalizacji pamięci podręcznej to C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |


## <a name="backups-dont-run-according-to-the-schedule"></a>Tworzenie kopii zapasowych nie działają zgodnie z harmonogramem
Jeśli zaplanowane kopie zapasowe nie wyzwalane automatycznie, podczas ręcznego tworzenia kopii zapasowych działać bez problemów, spróbuj wykonać następujące czynności:

- Zobacz, jeśli program PowerShell 3.0 lub nowszej jest zainstalowane na serwerze. Aby sprawdzić wersję programu PowerShell, uruchom następujące polecenie i upewnij się, że *głównych* numer wersji jest równy lub większy niż 3.

  `$PSVersionTable.PSVersion`

- Sprawdź, czy następująca ścieżka jest częścią *PSMODULEPATH* zmiennej środowiskowej.

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Jeśli zasady wykonywania programu PowerShell dla *LocalMachine* jest ustawiony na ograniczone, polecenia cmdlet programu PowerShell, która powoduje uruchomienie zadania tworzenia kopii zapasowej może zakończyć się niepowodzeniem. Uruchom następujące polecenia w trybie podniesionych uprawnień, aby sprawdzić i ustawić zasady wykonywania na albo *Unrestricted* lub *RemoteSigned*.

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- Przejdź do **Panel sterowania** > **narzędzia administracyjne** > **harmonogram zadań**. Rozwiń **Microsoft**i wybierz **kopii zapasowej Online**. Kliknij dwukrotnie **Microsoft OnlineBackup**, a następnie przejdź do **wyzwalaczy** kartę. Upewnij się, że stan jest ustawiony na **włączone**. Jeśli nie, wybierz **Edytuj**i wybierz **włączone** pole wyboru. Na **ogólne** karty, wróć do **opcje zabezpieczeń**. Upewnij się, że konto użytkownika, wybrany do uruchomienia tego zadania jest **systemu** lub **grupy administratorów lokalnych** na serwerze.


> [!TIP]
> Aby upewnić się, że zmiany są stosowane spójnie, uruchom ponownie serwer po wykonaniu powyższych kroków.


## <a name="troubleshoot-restore-issues"></a>Rozwiązywanie problemów przywracania

Usługa Azure Backup nie może pomyślnie zainstalować woluminu odzyskiwania, nawet po kilku minutach. Może również komunikaty o błędach w procesie. Aby rozpocząć odzyskiwanie normalnie, wykonaj następujące kroki:

1.  Anuluj proces ciągłego instalacji, w przypadku, gdy została uruchomiona przez kilka minut.

2.  Zobacz, jeśli używasz najnowszej wersji agenta kopii zapasowej. Aby dowiedzieć się, wersji, na **akcje** okienku konsoli MARS wybierz **o Recovery Agent usług Microsoft Azure**. Upewnij się, że **wersji** liczba jest większa lub równa wyższa niż wersja, o których wspomniano w [w tym artykule](https://go.microsoft.com/fwlink/?linkid=229525). Możesz pobrać najnowszą wersję z [tutaj](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Przejdź do **Menedżera urządzeń** > **kontrolery magazynu**, a następnie zlokalizuj **inicjatora iSCSI firmy Microsoft**. Jeśli możesz znaleźć go, przejdź bezpośrednio do kroku 7.

4.  Jeśli nie można zlokalizować Usługa inicjatora iSCSI firmy Microsoft, spróbuj znaleźć pozycji w obszarze **Menedżera urządzeń** > **kontrolery magazynu** o nazwie **nieznane urządzenie**, za pomocą Identyfikatora sprzętu **ROOT\ISCSIPRT**.

5.  Kliknij prawym przyciskiem myszy **nieznane urządzenie**i wybierz **aktualizacji sterowników**.

6.  Aktualizacja sterownika, wybierając opcję **wyszukiwanie automatycznie oprogramowania zaktualizowanym sterowniku**. Zakończenie aktualizacji należy zmienić **nieznane urządzenie** do **inicjatora iSCSI firmy Microsoft**, jak pokazano poniżej.

    ![Zrzut ekranu usługi Azure Backup Menedżera urządzeń, za pomocą kontrolerów magazynu wyróżniony](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Przejdź do **Menedżera zadań** > **usługi (lokalne)** > **Usługa inicjatora iSCSI firmy Microsoft**.

    ![Zrzut ekranu usługi Azure Backup Menedżera zadań, za pomocą usługi (lokalne) wyróżniony](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Uruchom ponownie usługę inicjatora iSCSI firmy Microsoft. Aby to zrobić, kliknij prawym przyciskiem myszy usługę, wybierz opcję **zatrzymać**, a następnie ponownie kliknij prawym przyciskiem myszy i wybierz **Start**.

9.  Ponów próbę operacji odzyskiwania przy użyciu **natychmiastowe Przywracanie**.

Jeśli odzyskiwanie nadal kończy się niepowodzeniem, należy ponownie uruchomić serwer lub klienta. Jeśli nie chcesz wykonać ponowny rozruch lub odzyskiwania nadal kończy się niepowodzeniem nawet po zakończeniu ponownego uruchomienia serwera, należy spróbować odzyskiwania z alternatywnej maszyny. Postępuj zgodnie z instrukcjami w [w tym artykule](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną
Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej [sposób wykonywania kopii zapasowej systemu Windows Server przy użyciu agenta usługi Azure Backup](tutorial-backup-windows-server-to-azure.md).
* Jeśli chcesz przywrócić kopię zapasową, w tym artykule znajdziesz informacje dotyczące [przywracania plików na maszynę z systemem Windows](backup-azure-restore-windows-server.md).
