---
title: Zarządzanie kopiami zapasowymi baz danych SAP HANAymi na maszynach wirtualnych platformy Azure
description: W tym artykule przedstawiono typowe zadania związane z zarządzaniem i monitorowaniem SAP HANA baz danych uruchomionych na maszynach wirtualnych platformy Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: a9462f8608fc5ae35255ac321a0742b3f1834fde
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75390637"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Zarządzanie kopiami zapasowymi baz danych platformy SAP HANA i ich monitorowanie

W tym artykule opisano typowe zadania związane z zarządzaniem i monitorowaniem SAP HANA baz danych, które są uruchomione na maszynie wirtualnej platformy Azure (VM) i których kopia zapasowa jest wykonywana w magazynie Recovery Services Azure Backup przez usługę [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) . Dowiesz się, jak monitorować zadania i alerty, wyzwalać kopie zapasowe na żądanie, edytować zasady, zatrzymywać i wznawiać ochronę bazy danych oraz wyrejestrować maszynę wirtualną z kopii zapasowych.

Jeśli nie skonfigurowano jeszcze kopii zapasowych dla SAP HANA baz danych, zobacz [Tworzenie kopii zapasowych SAP HANA baz danych na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorowanie zadań ręcznego tworzenia kopii zapasowej w portalu

Azure Backup przedstawia wszystkie zadania wyzwalane ręcznie w sekcji **zadania tworzenia kopii zapasowej** w Azure Portal.

![Sekcja zadań tworzenia kopii zapasowej](./media/sap-hana-db-manage/backup-jobs.png)

Zadania widoczne w tym portalu obejmują odnajdywanie i rejestrowanie bazy danych oraz operacje tworzenia kopii zapasowych i przywracania. Zaplanowane zadania, w tym kopie zapasowe dzienników, nie są wyświetlane w tej sekcji. Ręcznie wyzwolone kopie zapasowe z SAP HANA natywnych klientów (Panel sterowania Studio/Panel sterowania/DBA) również nie są wyświetlane w tym miejscu.

![Lista zadań tworzenia kopii zapasowej](./media/sap-hana-db-manage/backup-jobs-list.png)

Aby dowiedzieć się więcej o monitorowaniu, przejdź do [obszaru monitorowanie w Azure Portal](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) i [monitorowanie przy użyciu Azure monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="view-backup-alerts"></a>Wyświetlanie alertów dotyczących kopii zapasowych

Alerty to prosta metoda monitorowania kopii zapasowych baz danych SAP HANA. Alerty ułatwiają skoncentrowanie się na zdarzeniach, których nie można uzyskać w wielu przypadkach, które generują kopie zapasowe. Azure Backup umożliwia Ustawianie alertów i monitorowanie ich w następujący sposób:

* Zaloguj się do [portalu Azure](https://portal.azure.com/).
* Na pulpicie nawigacyjnym magazynu wybierz pozycję **alerty kopii zapasowych**.

  ![Alerty kopii zapasowej na pulpicie nawigacyjnym magazynu](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Będziesz mieć możliwość wyświetlenia alertów:

  ![Lista alertów dotyczących kopii zapasowych](./media/sap-hana-db-manage/backup-alerts-list.png)

* Kliknij alerty, aby wyświetlić więcej szczegółów:

  ![Szczegóły alertu](./media/sap-hana-db-manage/alert-details.png)

Obecnie Azure Backup umożliwia wysyłanie alertów za pośrednictwem poczty e-mail. Te alerty są następujące:

* Wyzwolone dla wszystkich błędów tworzenia kopii zapasowej.
* Skonsolidowane na poziomie bazy danych według kodu błędu.
* Wysyłane tylko do pierwszego błędu kopii zapasowej bazy danych.

Wysyłane Dowiedz się więcej o monitorowaniu, przejdź do [monitorowania w Azure Portal](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) i [monitorowanie przy użyciu Azure monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="management-operations"></a>Operacje zarządzania

Azure Backup umożliwia łatwe zarządzanie kopią zapasową bazy danych SAP HANA Database przy użyciu dużej ilości obsługiwanych operacji zarządzania. Te operacje zostały omówione bardziej szczegółowo w poniższych sekcjach.

### <a name="run-an-ad-hoc-backup"></a>Uruchamianie kopii zapasowej ad hoc

Kopie zapasowe są uruchamiane zgodnie z harmonogramem zasad. Kopię zapasową można uruchomić na żądanie w następujący sposób:

1. W menu magazyn kliknij pozycję **elementy kopii zapasowej**.
2. W obszarze **elementy kopii zapasowej**wybierz maszynę wirtualną z uruchomioną SAP HANA bazą danych, a następnie kliknij pozycję **Utwórz kopię zapasową teraz**.
3. W obszarze **kopia zapasowa**Użyj formantu kalendarza, aby wybrać ostatni dzień przechowywania punktu odzyskiwania. Następnie kliknij przycisk **OK**.
4. Monitoruj powiadomienia portalu. Postęp zadania można monitorować na pulpicie nawigacyjnym magazynu > **zadania tworzenia kopii zapasowej** > **w toku**. W zależności od rozmiaru bazy danych Tworzenie początkowej kopii zapasowej może chwilę potrwać.

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>Uruchamianie SAP HANA natywnej kopii zapasowej klienta w bazie danych z włączoną usługą Azure Backup

Jeśli chcesz utworzyć lokalną kopię zapasową (przy użyciu platformy HANA Studio/Panel sterowania) dla bazy danych, której kopia zapasowa jest tworzona przy użyciu Azure Backup, wykonaj następujące czynności:

1. Poczekaj na zakończenie wszystkich pełnych lub dzienników kopii zapasowych bazy danych. Sprawdź stan w SAP HANA Studio/Panel sterowania.
2. Wyłącz kopie zapasowe dzienników i ustaw wykaz kopii zapasowych w systemie plików dla odpowiedniej bazy danych.
3. Aby to zrobić, kliknij dwukrotnie pozycję **systemdb** > **Configuration** > **Wybierz pozycję Database** > **Filter (log)** .
4. Ustaw **enable_auto_log_backup** na wartość **nie**.
5. Ustaw **log_backup_using_backint** na **wartość false**.
6. Wykonaj pełną kopię zapasową bazy danych.
7. Poczekaj na zakończenie pełnej kopii zapasowej i kopii zapasowej wykazu.
8. Przywróć poprzednie ustawienia z powrotem do tych dla platformy Azure:
   * Ustaw wartość **enable_auto_log_backup** na **tak**.
   * Ustaw **log_backup_using_backint** na **wartość true**.

### <a name="change-policy"></a>Zmień zasady

Można zmienić podstawowe zasady dla SAP HANA elementu kopii zapasowej.

* Na pulpicie nawigacyjnym magazynu przejdź do **pozycji elementy kopii zapasowej**:

  ![Wybierz elementy kopii zapasowej](./media/sap-hana-db-manage/backup-items.png)

* Wybierz **SAP HANA na maszynie wirtualnej platformy Azure**

  ![Wybierz SAP HANA na maszynie wirtualnej platformy Azure](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Wybierz element kopii zapasowej, którego podstawowe zasady chcesz zmienić
* Kliknij istniejące zasady tworzenia kopii zapasowych

  ![Wybierz istniejące zasady tworzenia kopii zapasowych](./media/sap-hana-db-manage/existing-backup-policy.png)

* Zmień zasady, wybierając z listy. [Utwórz nowe zasady tworzenia kopii zapasowych w](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database#create-a-backup-policy) razie konieczności.

  ![Wybieranie zasad z listy rozwijanej](./media/sap-hana-db-manage/choose-backup-policy.png)

* Zapisz zmiany

  ![Zapisz zmiany](./media/sap-hana-db-manage/save-changes.png)

* Modyfikacje zasad będą mieć wpływ na wszystkie powiązane elementy kopii zapasowej i wyzwalają odpowiednie zadania **konfigurowania ochrony** .

>[!NOTE]
> Każda zmiana w okresie przechowywania będzie stosowana z mocą wsteczną do wszystkich starszych punktów odzyskiwania poza nowymi.
>
> Nie można używać zasad przyrostowej kopii zapasowej dla SAP HANA baz danych. Przyrostowa kopia zapasowa nie jest obecnie obsługiwana dla tych baz danych.

### <a name="stop-protection-for-an-sap-hana-database"></a>Zatrzymaj ochronę bazy danych SAP HANA

Można zatrzymać ochronę bazy danych SAP HANA na kilka sposobów:

* Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowych i usunięcie wszystkich punktów odzyskiwania.
* Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowej i pozostaw punkty odzyskiwania bez zmian.

Jeśli zdecydujesz się na pozostawienie punktów odzyskiwania, pamiętaj o następujących szczegółach:

* Wszystkie punkty odzyskiwania pozostaną nienaruszone, a całe oczyszczanie zatrzyma się przy zatrzymaniu ochrony z zachowaniem danych.
* Zostanie naliczona opłata za chronione wystąpienie i zużyty magazyn. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
* Jeśli usuniesz źródło danych bez zatrzymywania kopii zapasowych, nowe kopie zapasowe zakończą się niepowodzeniem.

Aby zatrzymać ochronę bazy danych:

* Na pulpicie nawigacyjnym magazynu wybierz pozycję **elementy kopii zapasowej**.
* W obszarze **Typ zarządzania kopiami zapasowymi**wybierz pozycję **SAP HANA na maszynie wirtualnej platformy Azure**

  ![Wybierz SAP HANA na maszynie wirtualnej platformy Azure](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Wybierz bazę danych, dla której chcesz zatrzymać ochronę:

  ![Wybierz bazę danych, aby zatrzymać ochronę](./media/sap-hana-db-manage/select-database.png)

* W menu Baza danych wybierz pozycję **Zatrzymaj tworzenie kopii zapasowej**.

  ![Wybierz pozycję Zatrzymaj tworzenie kopii zapasowej](./media/sap-hana-db-manage/stop-backup.png)

* W menu **Zatrzymaj kopię zapasową** wybierz, czy chcesz zachować, czy usunąć dane. Jeśli chcesz, podaj przyczynę i komentarz.

  ![Wybierz opcję Zachowaj lub Usuń dane](./media/sap-hana-db-manage/retain-backup-data.png)

* Wybierz pozycję **Zatrzymaj tworzenie kopii zapasowej**.

### <a name="resume-protection-for-an-sap-hana-database"></a>Wznów ochronę bazy danych SAP HANA

Po zatrzymaniu ochrony bazy danych SAP HANA, jeśli wybierzesz opcję **Zachowaj dane kopii zapasowej** , możesz później wznowić ochronę. Jeśli nie zachowasz kopii zapasowej danych, nie będziesz w stanie wznowić ochrony.

Aby wznowić ochronę bazy danych SAP HANA:

* Otwórz element kopii zapasowej i wybierz pozycję **Wznów wykonywanie kopii zapasowej**.

   ![Wybierz pozycję Wznów wykonywanie kopii zapasowej](./media/sap-hana-db-manage/resume-backup.png)

* W menu **Zasady kopii zapasowych** wybierz zasady, a następnie wybierz pozycję **Zapisz**.

### <a name="upgrading-from-sap-hana-10-to-20"></a>Uaktualnianie z SAP HANA 1,0 do 2,0

Dowiedz się, jak kontynuować tworzenie kopii zapasowej bazy danych SAP HANA [po uaktualnieniu z SAP HANA 1,0 do 2,0](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20).

### <a name="upgrading-without-a-sid-change"></a>Uaktualnianie bez zmiany identyfikatora SID

Dowiedz się, jak kontynuować tworzenie kopii zapasowej bazy danych SAP HANA, której [Identyfikator SID nie został zmieniony po uaktualnieniu](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change).

### <a name="unregister-an-sap-hana-database"></a>Wyrejestrowywanie bazy danych SAP HANA

Wyrejestruj wystąpienie SAP HANA po wyłączeniu ochrony, ale przed usunięciem magazynu:

* Na pulpicie nawigacyjnym magazynu w obszarze **Zarządzaj**wybierz pozycję **infrastruktura kopii zapasowych**.

   ![Wybieranie pozycji Infrastruktura zapasowa](./media/sap-hana-db-manage/backup-infrastructure.png)

* Wybierz **Typ zarządzania kopiami zapasowymi** jako **obciążenie na maszynie wirtualnej platformy Azure**

   ![Wybierz typ zarządzania kopiami zapasowymi jako obciążenie na maszynie wirtualnej platformy Azure](./media/sap-hana-db-manage/backup-management-type.png)

* W obszarze **serwery chronione**wybierz wystąpienie do wyrejestrowania. Aby usunąć magazyn, należy wyrejestrować wszystkie serwery i wystąpienia.

* Kliknij prawym przyciskiem myszy chronione wystąpienie i wybierz polecenie **Wyrejestruj**.

   ![Wybierz pozycję Wyrejestruj](./media/sap-hana-db-manage/unregister.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [rozwiązywać typowe problemy podczas tworzenia kopii zapasowych baz danych SAP HANA.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
