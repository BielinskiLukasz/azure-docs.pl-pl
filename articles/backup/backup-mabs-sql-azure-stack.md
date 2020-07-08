---
title: Tworzenie kopii zapasowych obciążeń SQL Server na Azure Stack
description: W tym artykule dowiesz się, jak skonfigurować serwer Microsoft Azure Backup (serwera usługi MAB) w celu ochrony SQL Serverymi bazami danych w Azure Stack.
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: b2d41bdccd67539205b74a0ce277b3b01a685c6c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84192977"
---
# <a name="back-up-sql-server-on-azure-stack"></a>Tworzenie kopii zapasowej SQL Server na Azure Stack

Ten artykuł służy do konfigurowania Microsoft Azure Backup Server (serwera usługi MAB) w celu ochrony SQL Server baz danych w Azure Stack.

Zarządzanie kopią zapasową bazy danych SQL Server na platformie Azure i odzyskiwanie z platformy Azure obejmuje trzy kroki:

1. Tworzenie zasad tworzenia kopii zapasowych w celu ochrony baz danych SQL Server
2. Tworzenie kopii zapasowych na żądanie
3. Odzyskiwanie bazy danych z dysków i z platformy Azure

## <a name="before-you-start"></a>Przed rozpoczęciem

[Zainstaluj i przygotuj Azure Backup Server](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Tworzenie zasad tworzenia kopii zapasowych w celu ochrony baz danych SQL Server na platformie Azure

1. W interfejsie użytkownika Azure Backup Server kliknij obszar roboczy **Ochrona** .

2. Na Wstążce narzędzi kliknij przycisk **New (nowy** ), aby utworzyć nową grupę ochrony.

    ![Utwórz grupę ochrony](./media/backup-azure-backup-sql/protection-group.png)

    Azure Backup Server uruchamia Kreatora grupy ochrony, który prowadzi użytkownika przez proces tworzenia **grupy ochrony**. Kliknij przycisk **Dalej**.

3. Na ekranie **Wybierz typ grupy ochrony** wybierz pozycję **serwery**.

    ![Wybierz typ grupy ochrony — "serwery"](./media/backup-azure-backup-sql/pg-servers.png)

4. Na ekranie **Wybieranie członków grupy** na liście dostępne elementy członkowskie są wyświetlane różne źródła danych. Kliknij, **+** Aby rozwinąć folder i wyświetlić podfoldery. Kliknij pole wyboru, aby wybrać element.

    ![Wybierz bazę danych SQL](./media/backup-azure-backup-sql/pg-databases.png)

    Wszystkie wybrane elementy pojawiają się na liście wybranych elementów członkowskich. Po wybraniu serwerów lub baz danych, które mają być chronione, kliknij przycisk **dalej**.

5. Na ekranie **Wybierz metodę ochrony danych** Podaj nazwę grupy ochrony i zaznacz pole wyboru **Chcę chronić w trybie online** .

    ![Metoda ochrony danych — dysk krótkoterminowy & online Azure](./media/backup-azure-backup-sql/pg-name.png)

6. Na ekranie **Określ cele krótkoterminowe** Uwzględnij niezbędne dane wejściowe, aby utworzyć punkty kopii zapasowej na dysku, a następnie kliknij przycisk **dalej**.

    W przykładzie **Zakres przechowywania** wynosi **5 dni**, a **częstotliwość synchronizacji** jest co **15 minut**, co jest częstotliwością tworzenia kopii zapasowych. **Ekspresowa pełna kopia zapasowa** jest ustawiona na **8:00 P. M**.

    ![Cele krótkoterminowe](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > W podanym przykładzie w dniu 8:00 PM codziennie, gdy punkt kopii zapasowej jest tworzony, transferuje zmodyfikowane dane z punktu ostatniej kopii zapasowej 8:00 PM. Ten proces jest nazywany **ekspresową pełną kopią zapasową**. Dzienniki transakcji są synchronizowane co 15 minut. Jeśli konieczne jest odzyskanie bazy danych o godzinie 9:00 PM, punkt jest tworzony z dzienników z ostatniego ekspresowej pełnej kopii zapasowej (8PM w tym przypadku).
   >
   >

7. Na ekranie **Przejrzyj przydział dysku** Sprawdź, czy dostępna jest ogólna ilość dostępnego miejsca do magazynowania i ilość miejsca na dysku. Kliknij przycisk **Dalej**.

8. W **Wybierz metodę tworzenia repliki**wybierz sposób tworzenia pierwszego punktu odzyskiwania. Początkową kopię zapasową można przenieść ręcznie (wyłączyć sieć), aby uniknąć przeciążenia przepustowości lub sieci. Jeśli zdecydujesz się na przeniesienie pierwszej kopii zapasowej, możesz określić godzinę wstępnego transferu. Kliknij przycisk **Dalej**.

    ![Metoda replikacji początkowej](./media/backup-azure-backup-sql/pg-manual.png)

    Początkowa kopia zapasowa wymaga przeniesienia całego źródła danych (SQL Server Database) z serwera produkcyjnego (SQL Server komputera) do Azure Backup Server. Te dane mogą być duże, a transfer danych przez sieć może przekroczyć przepustowość. Z tego powodu możesz wybrać opcję transferu początkowej kopii zapasowej: **ręcznie** (przy użyciu nośnika wymiennego), aby uniknąć przeciążenia przepustowości lub **automatycznie za pośrednictwem sieci** (w określonym czasie).

    Po zakończeniu początkowej kopii zapasowej pozostałe kopie zapasowe są przyrostowymi kopiami zapasowymi na początkowej kopii zapasowej. Przyrostowe kopie zapasowe mają być małe i łatwo przesyłane przez sieć.

9. Wybierz, kiedy ma być uruchamiane sprawdzanie spójności, a następnie kliknij przycisk **dalej**.

    ![Sprawdzanie spójności](./media/backup-azure-backup-sql/pg-consistent.png)

    Azure Backup Server przeprowadza sprawdzanie spójności na integralność punktu kopii zapasowej. Azure Backup Server oblicza sumę kontrolną pliku kopii zapasowej na serwerze produkcyjnym (SQL Server komputerze w tym scenariuszu) oraz dane kopii zapasowej dla tego pliku. W przypadku konfliktu zakłada się, że plik kopii zapasowej w Azure Backup Server jest uszkodzony. Azure Backup Server wznawia dane kopii zapasowej, wysyłając bloki odpowiadające niezgodności sumy kontrolnej. Ze względu na to, że sprawdzanie spójności ma intensywną wydajność, można zaplanować sprawdzanie spójności lub uruchomić je automatycznie.

10. Aby określić ochronę w trybie online dla źródeł danych, wybierz te, które mają być chronione na platformie Azure, a następnie kliknij przycisk **dalej**.

    ![Wybierz źródła danych](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Wybierz harmonogramy kopii zapasowych i zasady przechowywania odpowiednie dla zasad organizacji.

    ![Harmonogram i przechowywanie](./media/backup-azure-backup-sql/pg-schedule.png)

    W tym przykładzie kopie zapasowe są wykonywane raz dziennie o godzinie 12:00 PM i 8 PM (Dolna część ekranu).

    > [!NOTE]
    > Dobrym sposobem jest posiadanie kilku krótkoterminowych punktów odzyskiwania na dysku, co pozwala na szybkie odzyskiwanie. Te punkty odzyskiwania są używane do odzyskiwania operacyjnego. Platforma Azure służy jako dobra lokalizacja poza siedzibą firmy z wyższym umowy SLA i gwarantowaną dostępnością.
    >
    >

    **Najlepsze rozwiązanie**: w przypadku planowania tworzenia kopii zapasowych na platformie Azure do uruchomienia po zakończeniu tworzenia kopii zapasowych na dysku lokalnym najnowsze kopie zapasowe dysku są zawsze kopiowane na platformę Azure.

12. Wybierz harmonogram zasad przechowywania. Szczegółowe informacje na temat sposobu działania zasad przechowywania są podane w [Azure Backup w celu zastąpienia artykułu infrastruktury taśm](backup-azure-backup-cloud-as-tape.md).

    ![Zasady przechowywania](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    W tym przykładzie:

    * Kopie zapasowe są wykonywane raz dziennie o godzinie 12:00 PM i 8 PM (Dolna część ekranu) i są zachowywane przez 180 dni.
    * Kopia zapasowa w sobotę o godzinie 12:00 jest zachowywany przez 104 tygodni
    * Kopia zapasowa w ostatniej sobotę o godzinie 12:00 jest zachowywany przez 60 miesięcy
    * Kopia zapasowa w ostatniej sobotę marca o godzinie 12:00 jest przechowywany przez 10 lat
13. Kliknij przycisk **dalej** i wybierz odpowiednią opcję transferu początkowej kopii zapasowej na platformę Azure. Możesz wybrać opcję **automatycznie przez sieć**

14. Po przejrzeniu szczegółów zasad na ekranie **Podsumowanie** kliknij przycisk **Utwórz grupę** , aby zakończyć przepływ pracy. Możesz kliknąć przycisk **Zamknij** i monitorować postęp zadania w obszarze roboczym Monitorowanie.

    ![Tworzenie grupy ochrony w toku](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Tworzenie kopii zapasowej bazy danych SQL Server na żądanie

Podczas poprzednich kroków zostały utworzone zasady tworzenia kopii zapasowych, "punkt odzyskiwania" jest tworzony tylko w przypadku wystąpienia pierwszej kopii zapasowej. Zamiast czekać, aż harmonogram zostanie rozpoczęty, poniższe kroki wyzwalają ręczne utworzenie punktu odzyskiwania.

1. Przed utworzeniem punktu odzyskiwania poczekaj, aż w grupie ochrony zostanie wyświetlony stan **OK** dla bazy danych.

    ![Członkowie grupy ochrony](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie **Utwórz punkt odzyskiwania**.

    ![Utwórz punkt odzyskiwania w trybie online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Wybierz opcję **Ochrona online** w menu rozwijanym i kliknij przycisk **OK** , aby rozpocząć tworzenie punktu odzyskiwania na platformie Azure.

    ![Utwórz punkt odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Sprawdź postęp zadania w obszarze roboczym **monitorowanie** .

    ![Konsola monitorowania](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Odzyskiwanie bazy danych SQL Server z platformy Azure

Poniższe kroki są wymagane do odzyskania chronionej jednostki (SQL Server Database) z platformy Azure.

1. Otwórz konsolę zarządzania Azure Backup Server. Przejdź do obszaru roboczego **odzyskiwania** , w którym można zobaczyć chronione serwery. Przeglądaj wymaganą bazę danych (w tym przypadku ReportServer $ MSDPM2012). Wybierz **odzyskiwanie z** czasu określonego jako punkt **online** .

    ![Wybierz punkt odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Kliknij prawym przyciskiem myszy nazwę bazy danych, a następnie kliknij polecenie **Odzyskaj**.

    ![Odzyskaj z platformy Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. SERWERA usługi MAB wyświetla szczegóły punktu odzyskiwania. Kliknij przycisk **Dalej**. Aby zastąpić bazę danych, wybierz typ odzyskiwania **Odzyskaj do oryginalnego wystąpienia SQL Server**. Kliknij przycisk **Dalej**.

    ![Odzyskaj do oryginalnej lokalizacji](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    W tym przykładzie serwera usługi MAB odzyskuje bazę danych do innego wystąpienia SQL Server lub do autonomicznego folderu sieciowego.

4. Na ekranie **Określ opcje odzyskiwania** możesz wybrać opcje odzyskiwania, takie jak ograniczanie przepustowości sieci, aby ograniczyć przepustowość używaną przez funkcję odzyskiwania. Kliknij przycisk **Dalej**.

5. Na ekranie **Podsumowanie** widoczne są wszystkie konfiguracje odzyskiwania. Kliknij przycisk **Odzyskaj**.

    Stan odzyskiwania przedstawia odzyskaną bazę danych. Możesz kliknąć przycisk **Zamknij** , aby zamknąć kreatora i wyświetlić postęp w obszarze roboczym **monitorowanie** .

    ![Inicjowanie procesu odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Po zakończeniu odzyskiwania przywrócona baza danych jest spójna z aplikacją.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem [pliki i aplikacje kopii zapasowej](backup-mabs-files-applications-azure-stack.md) .
Zapoznaj się z artykułem [Tworzenie kopii zapasowej programu SharePoint w Azure Stack](backup-mabs-sharepoint-azure-stack.md) .
