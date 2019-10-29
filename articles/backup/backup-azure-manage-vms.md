---
title: Zarządzanie kopiami zapasowymi maszyn wirtualnych platformy Azure i monitorowanie ich przy użyciu usługi Azure Backup
description: Dowiedz się, jak zarządzać kopiami zapasowymi maszyny wirtualnej platformy Azure i monitorować je za pomocą usługi Azure Backup.
ms.reviewer: sogup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: dacurwin
ms.openlocfilehash: 3781ac1be547f6733417c4f0cae9f3e8681ea9e8
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969232"
---
# <a name="manage-azure-vm-backups-with-azure-backup-service"></a>Zarządzanie kopiami zapasowymi maszyn wirtualnych platformy Azure za pomocą usługi Azure Backup

W tym artykule opisano sposób zarządzania maszynami wirtualnymi platformy Azure, których kopia zapasowa została utworzona przy użyciu [usługi Azure Backup](backup-overview.md). Artykuł zawiera również podsumowanie informacji o kopii zapasowej, które można znaleźć na pulpicie nawigacyjnym magazynu.

W Azure Portal pulpit nawigacyjny magazynu Recovery Services zapewnia dostęp do informacji o magazynie, w tym:

* Najnowsza kopia zapasowa, która jest również najnowszym punktem przywracania.
* Zasady tworzenia kopii zapasowych.
* Łączny rozmiar wszystkich migawek kopii zapasowych.
* Liczba maszyn wirtualnych, dla których włączono obsługę kopii zapasowych.

Kopiami zapasowymi można zarządzać przy użyciu pulpitu nawigacyjnego i przechodzenia do szczegółów poszczególnych maszyn wirtualnych. Aby rozpocząć tworzenie kopii zapasowych maszyn, otwórz magazyn na pulpicie nawigacyjnym.

![Pełny widok pulpitu nawigacyjnego z suwakiem](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>Wyświetlanie maszyn wirtualnych na pulpicie nawigacyjnym

Aby wyświetlić maszyny wirtualne na pulpicie nawigacyjnym magazynu:

1. Zaloguj się do [portalu Azure](https://portal.azure.com/).
2. W menu centrum wybierz pozycję **Przeglądaj**. Na liście zasobów wpisz **Usługi odzyskiwania**. Podczas wpisywania lista jest filtrowana na podstawie danych wejściowych. Wybierz **Recovery Services magazynów**.

    ![Tworzenie magazynu usługi Recovery Services](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. Aby ułatwić sobie korzystanie z programu, kliknij prawym przyciskiem myszy magazyn i wybierz pozycję **Przypnij do pulpitu nawigacyjnego**.
4. Otwórz pulpit nawigacyjny magazynu.

    ![Otwórz blok pulpit nawigacyjny i ustawienia magazynu](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. Na kafelku **elementy kopii zapasowej** wybierz pozycję **Azure Virtual Machines**.

    ![Otwórz kafelek elementy kopii zapasowej](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. W bloku **elementy kopii zapasowej** można wyświetlić listę chronionych maszyn wirtualnych. W tym przykładzie magazyn chroni jedną maszynę wirtualną: demobackup.  

    ![Wyświetlanie bloku elementów kopii zapasowej](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. Na pulpicie nawigacyjnym elementu magazynu zmodyfikuj zasady tworzenia kopii zapasowych, uruchom kopię zapasową na żądanie, Zatrzymaj lub Wznów ochronę maszyn wirtualnych, Usuń dane kopii zapasowej, Wyświetl punkty przywracania i uruchom przywracanie.

    ![Pulpit nawigacyjny elementy kopii zapasowej i blok ustawienia](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>Zarządzanie zasadami tworzenia kopii zapasowych dla maszyny wirtualnej

Aby zarządzać zasadami tworzenia kopii zapasowych:

1. Zaloguj się do [portalu Azure](https://portal.azure.com/). Otwórz pulpit nawigacyjny magazynu.
2. Na kafelku **elementy kopii zapasowej** wybierz pozycję **Azure Virtual Machines**.

    ![Otwórz kafelek elementy kopii zapasowej](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. W bloku **elementy kopii zapasowej** można wyświetlić listę chronionych maszyn wirtualnych i stan ostatniej kopii zapasowej z najnowszym czasem punktów przywracania.

    ![Wyświetlanie bloku elementów kopii zapasowej](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. Na pulpicie nawigacyjnym elementu magazynu można wybrać zasady tworzenia kopii zapasowych.

   * Aby przełączyć zasady, wybierz inne zasady, a następnie wybierz pozycję **Zapisz**. Nowe zasady zostaną natychmiast zastosowane do magazynu.

     ![Wybieranie zasad kopii zapasowych](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>Uruchamianie kopii zapasowej na żądanie

Można uruchomić kopię zapasową maszyny wirtualnej na żądanie po skonfigurowaniu jej ochrony. Pamiętaj o tych szczegółach:

* Jeśli początkowa kopia zapasowa jest w stanie oczekiwania, kopia zapasowa na żądanie tworzy pełną kopię maszyny wirtualnej w magazynie Recovery Services.
* Jeśli początkowa kopia zapasowa zostanie ukończona, kopia zapasowa na żądanie będzie wysyłać tylko zmiany z poprzedniej migawki do magazynu Recovery Services. Oznacza to, że późniejsze kopie zapasowe są zawsze przyrostowe.
* Zakres przechowywania kopii zapasowej na żądanie jest wartością przechowywania określoną podczas wyzwalania kopii zapasowej.

Aby wyzwolić kopię zapasową na żądanie:

1. Na [pulpicie nawigacyjnym elementu magazynu](#view-vms-on-the-dashboard)w obszarze **chroniony element**wybierz pozycję **kopia zapasowa**.

    ![Opcja Utwórz kopię zapasową teraz](./media/backup-azure-manage-vms/backup-now-button.png)

2. W obszarze **Typ zarządzania kopiami zapasowymi**wybierz pozycję **maszyna wirtualna platformy Azure**. Zostanie wyświetlony blok **element kopia zapasowa (maszyna wirtualna platformy Azure)** .
3. Wybierz maszynę wirtualną i wybierz pozycję Utwórz **kopię zapasową teraz** , aby utworzyć kopię zapasową na żądanie. Zostanie wyświetlony blok **kopia zapasowa teraz** .
4. W polu **Zachowaj kopię zapasową** do Określ datę przechowywania kopii zapasowej.

    ![Kalendarz tworzenia kopii zapasowej teraz](./media/backup-azure-manage-vms/backup-now-check.png)

5. Wybierz **przycisk OK** , aby uruchomić zadanie tworzenia kopii zapasowej.

Aby śledzić postęp zadania, na pulpicie nawigacyjnym magazynu wybierz kafelek **zadania tworzenia kopii zapasowej** .

## <a name="stop-protecting-a-vm"></a>Zatrzymywanie ochrony maszyny wirtualnej

Istnieją dwa sposoby na zatrzymanie ochrony maszyny wirtualnej:

* **Zatrzymaj ochronę i Zachowaj dane kopii zapasowej**. Ta opcja spowoduje zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowej na podstawie ochrony maszyny wirtualnej; Jednak usługa Azure Backup będzie zachować kopie zapasowe punktów odzyskiwania.  Musisz zanieść opłaty za przechowywanie punktów odzyskiwania w magazynie (zobacz [Azure Backup cennika](https://azure.microsoft.com/pricing/details/backup/) , aby uzyskać szczegółowe informacje). W razie potrzeby będzie można przywrócić maszynę wirtualną. Jeśli zdecydujesz się na wznowienie ochrony maszyny wirtualnej, możesz użyć opcji *Wznów tworzenie kopii zapasowej* .
* **Zatrzymaj ochronę i Usuń dane kopii zapasowej**. Ta opcja spowoduje zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowej z ochrony maszyny wirtualnej i usunięcie wszystkich punktów odzyskiwania. Nie będzie można przywrócić maszyny wirtualnej ani użyć opcji *Wznów tworzenie kopii zapasowej* .

>[!NOTE]
>Jeśli usuniesz źródło danych bez zatrzymywania kopii zapasowych, nowe kopie zapasowe zakończą się niepowodzeniem. Stare punkty odzyskiwania wygasną zgodnie z zasadami, ale jeden ostatni punkt odzyskiwania będzie zawsze utrzymywany do momentu zatrzymania wykonywania kopii zapasowych i usunięcia danych.
>

### <a name="stop-protection-and-retain-backup-data"></a>Zatrzymywanie ochrony i zachowywanie danych kopii zapasowej

Aby zatrzymać ochronę i zachować dane maszyny wirtualnej:

1. Na [pulpicie nawigacyjnym elementu magazynu](#view-vms-on-the-dashboard)wybierz pozycję **Zatrzymaj tworzenie kopii zapasowej**.
2. Wybierz opcję **Zachowaj dane kopii zapasowej**i potwierdź wybór zgodnie z potrzebami. Dodaj komentarz, jeśli chcesz. Jeśli nie masz pewności co do nazwy elementu, umieść kursor nad wykrzyknikiem, aby wyświetlić nazwę.

    ![Zachowaj dane kopii zapasowej](./media/backup-azure-manage-vms/retain-backup-data.png)

Powiadomienie informuje, że zadania tworzenia kopii zapasowej zostały zatrzymane.

### <a name="stop-protection-and-delete-backup-data"></a>Zatrzymywanie ochrony i usuwanie danych kopii zapasowej

Aby zatrzymać ochronę i usunąć dane maszyny wirtualnej:

1. Na [pulpicie nawigacyjnym elementu magazynu](#view-vms-on-the-dashboard)wybierz pozycję **Zatrzymaj tworzenie kopii zapasowej**.
2. Wybierz pozycję **Usuń dane kopii zapasowej**i w razie konieczności Potwierdź wybór. Wprowadź nazwę elementu kopii zapasowej i Dodaj komentarz, jeśli chcesz.

    ![Usuwanie danych kopii zapasowej](./media/backup-azure-manage-vms/delete-backup-data1.png)

## <a name="resume-protection-of-a-vm"></a>Wznów ochronę maszyny wirtualnej

W przypadku wybrania opcji [Zatrzymaj ochronę i Zachowaj dane kopii zapasowej](#stop-protection-and-retain-backup-data) podczas zatrzymywania ochrony maszyny wirtualnej można użyć polecenie **Wznów tworzenie kopii zapasowej**. Ta opcja jest niedostępna w przypadku wybrania opcji [Zatrzymaj ochronę i Usuń dane kopii](#stop-protection-and-delete-backup-data) zapasowej lub [Usuń dane kopii zapasowej](#delete-backup-data).

Aby wznowić ochronę maszyny wirtualnej:

1. Na [pulpicie nawigacyjnym elementu magazynu](#view-vms-on-the-dashboard)wybierz pozycję **Wznów tworzenie kopii zapasowej**.

2. Wykonaj kroki opisane w sekcji [Zarządzanie zasadami tworzenia kopii zapasowych](#manage-backup-policy-for-a-vm) , aby przypisać zasady dla maszyny wirtualnej. Nie musisz wybierać zasad początkowej ochrony maszyny wirtualnej.
3. Po zastosowaniu zasad tworzenia kopii zapasowych do maszyny wirtualnej zostanie wyświetlony następujący komunikat:

    ![Komunikat wskazujący, że pomyślnie chroniona maszyna wirtualna](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Usuwanie danych kopii zapasowej

Istnieją dwa sposoby usuwania danych kopii zapasowej maszyny wirtualnej:

* Na pulpicie nawigacyjnym elementu magazynu wybierz pozycję Zatrzymaj tworzenie kopii zapasowej i postępuj zgodnie z instrukcjami dla opcji [Zatrzymaj ochronę i Usuń dane kopii zapasowej](#stop-protection-and-delete-backup-data) .

  ![Wybieranie pozycji Zatrzymaj tworzenie kopii zapasowej](./media/backup-azure-manage-vms/stop-backup-buttom.png)

* Na pulpicie nawigacyjnym elementu magazynu wybierz pozycję Usuń dane kopii zapasowej. Ta opcja jest włączona, jeśli wybrano opcję [zatrzymania ochrony i zachowania danych kopii zapasowej](#stop-protection-and-retain-backup-data) podczas zatrzymywania ochrony maszyny wirtualnej

  ![Wybierz pozycję Usuń kopię zapasową](./media/backup-azure-manage-vms/delete-backup-buttom.png)

  * Na [pulpicie nawigacyjnym elementu magazynu](#view-vms-on-the-dashboard)wybierz pozycję **Usuń dane kopii zapasowej**.
  * Wpisz nazwę elementu kopii zapasowej, aby potwierdzić, że chcesz usunąć punkty odzyskiwania.

    ![Usuwanie danych kopii zapasowej](./media/backup-azure-manage-vms/delete-backup-data1.png)

  * Aby usunąć dane kopii zapasowej dla elementu, wybierz pozycję **Usuń**. Komunikat z powiadomieniem informuje o tym, że dane kopii zapasowej zostały usunięte.

  > [!NOTE]
  > Usunięcie danych kopii zapasowej powoduje usunięcie wszystkich skojarzonych punktów odzyskiwania. Nie można wybrać określonych punktów odzyskiwania do usunięcia.

### <a name="backup-item-where-primary-data-source-no-longer-exists"></a>Element kopii zapasowej, w którym podstawowe źródło danych już nie istnieje

* Jeśli maszyny wirtualne platformy Azure skonfigurowane dla usługi Azure Backup zostaną usunięte lub przeniesione bez zatrzymywania ochrony, wówczas zarówno zaplanowane zadania tworzenia kopii zapasowej, jak i zadania tworzenia kopii zapasowych na żądanie (ad hoc) zakończą się niepowodzeniem z powodu błędu UserErrorVmNotFoundV2. Wstępne sprawdzanie kopii zapasowej będzie wyświetlane jako krytyczne tylko dla niezakończonych zadań tworzenia kopii zapasowych ad hoc (zakończone niepowodzeniem zaplanowane zadania nie są wyświetlane).
* Te elementy kopii zapasowej pozostają aktywne w systemie zgodnie z zasadami tworzenia kopii zapasowych i przechowywania ustawionych przez użytkownika. Dane kopii zapasowej dla tych maszyn wirtualnych platformy Azure będą przechowywane zgodnie z zasadami przechowywania. Wygasłe punkty odzyskiwania (z wyjątkiem ostatniego punktu odzyskiwania) są czyszczone zgodnie z zakresem przechowywania określonym w zasadach tworzenia kopii zapasowych.
* Użytkownicy są zalecani do usunięcia elementów kopii zapasowych, w których pierwotne źródło danych już nie istnieje, aby uniknąć dodatkowego kosztu, jeśli element kopii zapasowej/dane dla zasobów usuwania nie są już wymagane, ponieważ ostatni punkt odzyskiwania jest zachowywany w nieskończoność, a użytkownik jest obciążany jako według cennika kopii zapasowych.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć kopię zapasową maszyn wirtualnych platformy Azure z ustawień maszyny wirtualnej](backup-azure-vms-first-look-arm.md).
* Dowiedz się, jak [przywrócić maszyny wirtualne](backup-azure-arm-restore-vms.md).
* Dowiedz się, jak [monitorować kopie zapasowe maszyn wirtualnych platformy Azure](backup-azure-monitor-vms.md).
