---
title: Przywracanie stanu systemu do systemu Windows Server
description: Krok po kroku wyjaśnienie przywracania stanu systemu Windows Server z kopii zapasowej na platformie Azure.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 08/18/2017
ms.openlocfilehash: 6d46a091a4e620e26d05735f12a201009663e65d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77602471"
---
# <a name="restore-system-state-to-windows-server"></a>Przywracanie stanu systemu w systemie Windows Server

W tym artykule wyjaśniono, jak przywrócić kopie zapasowe stanu systemu Windows Server z magazynu usług Azure Recovery Services. Aby przywrócić stan systemu, musisz mieć kopię zapasową stanu systemu (utworzoną przy użyciu instrukcji w [obszarze Kopia zapasowa stanu systemu](backup-azure-system-state.md#back-up-windows-server-system-state)i upewnij się, że zainstalowano [najnowszą wersję agenta usług microsoft azure recovery services (MARS).](https://aka.ms/azurebackup_agent) Odzyskiwanie danych o stanie systemu Windows Server z magazynu usług Azure Recovery Services jest procesem dwuetapowym:

1. Przywróć stan systemu jako pliki z usługi Azure Backup. Podczas przywracania stanu systemu jako plików z usługi Azure Backup można:
   * Przywróć stan systemu na tym samym serwerze, na którym zostały wykonane kopie zapasowe, lub
   * Przywróć plik stanu systemu na serwerze alternatywnym.

2. Zastosuj przywrócone pliki stanu systemu do systemu Windows Server.

## <a name="recover-system-state-files-to-the-same-server"></a>Odzyskiwanie plików stanu systemu na tym samym serwerze

W poniższych krokach wyjaśniono, jak przywrócić konfigurację systemu Windows Server do poprzedniego stanu. Przewijając konfigurację serwera z powrotem do znanego, stabilnego stanu, może być niezwykle cenne. Poniższe kroki przywracają stan systemu serwera z magazynu usług odzyskiwania.

1. Otwórz przystawkę **Microsoft Azure Backup**. Jeśli nie wiesz, gdzie została zainstalowana przystawka, wyszukaj komputer lub serwer w **poszukiwaniu kopii zapasowej platformy Microsoft Azure**.

    Aplikacja klasyczna powinna pojawić się w wynikach wyszukiwania.

2. Kliknij **przycisk Odzyskaj dane,** aby uruchomić kreatora.

    ![Odzyskiwanie danych](./media/backup-azure-restore-windows-server/recover.png)

3. W okienku **Wprowadzenie,** aby przywrócić dane na tym samym serwerze lub komputerze, wybierz pozycję **Ten serwer`<server name>`( )** i kliknij przycisk **Dalej**.

    ![Wybierz tę opcję serwera, aby przywrócić dane na tym samym komputerze](./media/backup-azure-restore-system-state/samemachine.png)

4. W okienku **Wybierz tryb odzyskiwania** wybierz pozycję Stan **systemu,** a następnie kliknij przycisk **Dalej**.

    ![Przeglądanie plików](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. W kalendarzu w okienku **Wybierz wolumin i data** wybierz punkt odzyskiwania.

    Można przywrócić z dowolnego punktu odzyskiwania w czasie. Daty **pogrubione** wskazują dostępność co najmniej jednego punktu odzyskiwania. Po wybraniu daty, jeśli dostępnych jest wiele punktów odzyskiwania, wybierz określony punkt odzyskiwania z menu rozwijanego **Czas.**

    ![Objętość i data](./media/backup-azure-restore-system-state/select-date.png)

6. Po wybraniu punktu odzyskiwania do przywrócenia kliknij przycisk **Dalej**.

    Usługa Azure Backup umożliwia zainstalowanie lokalnego punktu odzyskiwania i używa go jako woluminu odzyskiwania.

7. W następnym okienku określ miejsce docelowe odzyskanych plików o stanie systemu i kliknij przycisk **Przeglądaj,** aby otworzyć Eksploratora Windows i znaleźć żądane pliki i foldery. Opcja **Utwórz kopie tak, aby były dostępne w obu wersjach,** tworzy kopie poszczególnych plików w istniejącym archiwum plików państwa systemu zamiast tworzenia kopii całego archiwum stanu systemu.

    ![Opcje odzyskiwania](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Sprawdź szczegóły odzyskiwania w okienku **Potwierdzenia** i kliknij przycisk **Odzyskaj**.

   ![kliknij przycisk Odzyskaj, aby potwierdzić akcję odzyskania](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Skopiuj katalog *WindowsImageBackup* w miejscu docelowym odzyskiwania do woluminu niekrytycznym serwera. Zazwyczaj wolumin systemu operacyjnego Systemu Windows jest woluminem krytycznym.

10. Po pomyślnym odzyskaniu wykonaj kroki opisane w sekcji [Zastosuj przywrócone pliki stanu systemu do systemu Windows Server](backup-azure-restore-system-state.md), aby zakończyć proces odzyskiwania stanu systemu.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Odzyskiwanie plików stanu systemu na serwerze alternatywnym

Jeśli system Windows Server jest uszkodzony lub niedostępny i chcesz przywrócić go do stanu stabilnego, odzyskując stan systemu Windows Server, można przywrócić stan systemu uszkodzonego serwera z innego serwera. Aby przywrócić stan systemu na oddzielnym serwerze, należy wykonać następujące kroki.  

Terminologia użyta w tych krokach obejmuje:

* *Komputer źródłowy* — oryginalny komputer, z którego została pobrana kopia zapasowa i która jest obecnie niedostępna.
* *Maszyna docelowa* — maszyna, do której dane są odzyskiwane.
* *Przykładowy magazyn* — magazyn usług odzyskiwania, do którego są zarejestrowane *maszyny źródłowej* i *docelowej.* <br/>

> [!NOTE]
> Kopii zapasowych pobranych z jednego komputera nie można przywrócić na komputerze z wcześniejszą wersją systemu operacyjnego. Na przykład kopii zapasowych pobranych z komputera z systemem Windows Server 2016 nie można przywrócić do systemu Windows Server 2012 R2. Jednak odwrotność jest możliwa. Do przywrócenia systemu Windows Server 2016 można użyć kopii zapasowych z systemu Windows Server 2012 R2.
>

1. Otwórz przystawkę **Kopia zapasowa platformy Microsoft Azure** na komputerze *docelowym*.
2. Upewnij się, że *komputer docelowy* i *komputer źródłowy* są zarejestrowane w tym samym magazynie usług odzyskiwania.
3. Kliknij **przycisk Odzyskaj dane,** aby zainicjować przepływ pracy.
4. Wybierz **inny serwer**

    ![Inny serwer](./media/backup-azure-restore-system-state/anotherserver.png)

5. Podaj plik poświadczeń przechowalni odpowiadający *przechowalni próbkowania*. Jeśli plik poświadczeń magazynu jest nieprawidłowy (lub wygasł), pobierz nowy plik poświadczeń magazynu z *przykładowego magazynu* w witrynie Azure portal. Po podaniu pliku poświadczeń przechowalni pojawi się przechowalnia usług odzyskiwania skojarzona z plikiem poświadczeń przechowalni.

6. W okienku Wybierz serwer kopii zapasowej wybierz *komputer źródłowy* z listy wyświetlanych maszyn.
7. W okienku Wybierz tryb odzyskiwania wybierz pozycję **Stan systemu** i kliknij przycisk **Dalej**.

    ![Wyszukiwanie](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. W kalendarzu w okienku **Wybierz wolumin i datę** wybierz punkt odzyskiwania. Można przywrócić z dowolnego punktu odzyskiwania w czasie. Daty **pogrubione** wskazują dostępność co najmniej jednego punktu odzyskiwania. Po wybraniu daty, jeśli dostępnych jest wiele punktów odzyskiwania, wybierz określony punkt odzyskiwania z menu rozwijanego **Czas.**

    ![Wyszukiwanie elementów](./media/backup-azure-restore-system-state/select-date.png)

9. Po wybraniu punktu odzyskiwania do przywrócenia kliknij przycisk **Dalej**.

10. W okienku **Wybierz tryb odzyskiwania stanu systemu** określ miejsce docelowe, w którym mają zostać odzyskane pliki o stanie systemu, a następnie kliknij przycisk **Dalej**.

    ![Szyfrowanie](./media/backup-azure-restore-system-state/recover-as-files.png)

    Opcja **Utwórz kopie tak, aby były dostępne w obu wersjach,** tworzy kopie poszczególnych plików w istniejącym archiwum plików państwa systemu zamiast tworzenia kopii całego archiwum stanu systemu.

11. Sprawdź szczegóły odzyskiwania w okienku Potwierdzenia i kliknij przycisk **Odzyskaj**.

    ![kliknij przycisk Odzyskaj, aby potwierdzić proces odzyskiwania](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Skopiuj katalog *WindowsImageBackup* na niekrytyczny wolumin serwera\)(na przykład D: . Zazwyczaj wolumin systemu operacyjnego Windows jest woluminem krytycznym.

13. Aby zakończyć proces odzyskiwania, użyj poniższej sekcji, aby [zastosować przywrócone pliki stanu systemu w systemie Windows Server](#apply-restored-system-state-on-a-windows-server).

## <a name="apply-restored-system-state-on-a-windows-server"></a>Stosowanie przywróconego stanu systemu w systemie Windows Server

Po odzyskaniu stanu systemu jako plików przy użyciu agenta usług Azure Recovery Services użyj narzędzia Kopia zapasowa systemu Windows Server, aby zastosować odzyskany stan systemu do systemu Windows Server. Narzędzie Kopia zapasowa systemu Windows Server jest już dostępne na serwerze. W poniższych krokach wyjaśniono, jak zastosować odzyskany stan systemu.

1. Użyj następujących poleceń, aby ponownie uruchomić serwer w *trybie naprawy usług katalogowych*. W wierszu polecenia z podwyższonym poziomem uprawnień:

    ```cmd
    Bcdedit /set safeboot dsrepair
    Shutdown /r /t 0
    ```

2. Po ponownym uruchomieniu otwórz przystawkę Kopia zapasowa systemu Windows Server. Jeśli nie wiesz, gdzie została zainstalowana przystawka, wyszukaj na komputerze lub serwerze **hasło Kopia zapasowa systemu Windows Server**.

    Aplikacja klasyczna pojawi się w wynikach wyszukiwania.

3. W przystawce wybierz pozycję **Kopia zapasowa lokalna**.

    ![wybierz lokalną kopię zapasową, aby przywrócić stamtąd](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. Na konsoli Kopia zapasowa lokalna w **okienku Akcje**kliknij pozycję **Odzyskaj,** aby otworzyć Kreatora odzyskiwania.

5. Wybierz opcję **Kopia zapasowa przechowywana w innej lokalizacji**i kliknij przycisk **Dalej**.

   ![wybrać, aby odzyskać do innego serwera](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. Określając typ lokalizacji, wybierz **zdalny folder udostępniony,** jeśli kopia zapasowa stanu systemu została odzyskana na innym serwerze. Jeśli stan systemu został odzyskany lokalnie, wybierz pozycję **Dyski lokalne**.

    ![wybierz, czy chcesz odzyskać z serwera lokalnego, czy z innego](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Wprowadź ścieżkę do katalogu *WindowsImageBackup* lub wybierz dysk lokalny zawierający ten katalog (na przykład D:\WindowsImageBackup), odzyskany w ramach odzyskiwania plików o stanie systemu przy użyciu agenta usług odzyskiwania platformy Azure i kliknij przycisk **Dalej**.

    ![ścieżka do udostępnionego pliku](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Wybierz wersję stanu systemu, którą chcesz przywrócić, a następnie kliknij przycisk **Dalej**.

9. W okienku Wybierz typ odzyskiwania wybierz pozycję **Stan systemu** i kliknij przycisk **Dalej**.

10. Aby uzyskać lokalizację odzyskiwania stanu systemu, wybierz pozycję **Oryginalna lokalizacja**i kliknij przycisk **Dalej**.

11. Przejrzyj szczegóły potwierdzenia, sprawdź ustawienia ponownego uruchomienia i kliknij przycisk **Odzyskaj,** aby zastosować przywrócone pliki stanu systemu.

    ![uruchamianie plików stanu systemu przywracania](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Uwagi szczególne dotyczące odzyskiwania stanu systemu na serwerze usługi Active Directory

Kopia zapasowa stanu systemu zawiera dane usługi Active Directory. Aby przywrócić usługę domenową Active Directory (AD DS) z bieżącego stanu do poprzedniego stanu, należy wykonać następujące czynności.

1. Uruchom ponownie kontroler domeny w trybie przywracania usług katalogowych (DSRM).
2. Wykonaj [następujące](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-nonauthoritative-restore) kroki, aby odzyskać usługi AD DS za pomocą poleceń cmdlet kopii zapasowej systemu Windows Server.

## <a name="troubleshoot-failed-system-state-restore"></a>Rozwiązywanie problemów w przypadku niepowodzenia przywracania stanu systemu

Jeśli poprzedni proces stosowania stanu systemu nie zakończy się pomyślnie, użyj środowiska odzyskiwania systemu Windows (Win RE), aby odzyskać system Windows Server. Poniższe kroki wyjaśniają, jak odzyskać za pomocą Win RE. Tej opcji należy używać tylko wtedy, gdy system Windows Server nie uruchamia się normalnie po przywróceniu stanu systemu. Poniższy proces usuwa dane niesystemowe, należy zachować ostrożność.

1. Uruchom system Windows Server w środowisku odzyskiwania systemu Windows (Win RE).

2. Wybierz opcję Rozwiązywanie problemów z trzech dostępnych opcji.

    ![menu otwierające](./media/backup-azure-restore-system-state/winre-1.png)

3. Na ekranie **Opcje zaawansowane** wybierz wiersz **polecenia** i podaj nazwę użytkownika i hasło administratora serwera.

   ![menu otwierające](./media/backup-azure-restore-system-state/winre-2.png)

4. Podaj nazwę użytkownika i hasło administratora serwera.

    ![menu otwierające](./media/backup-azure-restore-system-state/winre-3.png)

5. Po otwarciu wiersza polecenia w trybie administratora uruchom następujące polecenie, aby uzyskać wersje kopii zapasowej stanu systemu.

    ```cmd
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```

    ![pobierz wersje kopii zapasowych stanu systemu](./media/backup-azure-restore-system-state/winre-4.png)

6. Uruchom następujące polecenie, aby uzyskać wszystkie woluminy dostępne w kopii zapasowej.

    ```cmd
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![pobierz wersje kopii zapasowych stanu systemu](./media/backup-azure-restore-system-state/winre-5.png)

7. Następujące polecenie odzyskuje wszystkie woluminy, które są częścią kopii zapasowej stanu systemu. Należy zauważyć, że ten krok odzyskuje tylko woluminy krytyczne, które są częścią stanu systemu. Wszystkie dane niesystemowe zostaną usunięte.

    ```cmd
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```

     ![pobierz wersje kopii zapasowych stanu systemu](./media/backup-azure-restore-system-state/winre-6.png)

## <a name="next-steps"></a>Następne kroki

* Po odzyskaniu plików i folderów można [zarządzać kopiami zapasowymi](backup-azure-manage-windows-server.md).
