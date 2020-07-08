---
title: Często zadawane pytania — tworzenie kopii zapasowych baz danych platformy SAP HANA na maszynach wirtualnych platformy Azure
description: W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowych SAP HANA baz danych przy użyciu usługi Azure Backup.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: ddc4af9a164de3a822e8aebd6c0a4db769ec62a0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85262586"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Często zadawane pytania — tworzenie kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowych SAP HANA baz danych przy użyciu usługi Azure Backup.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>Ile pełnych kopii zapasowych jest obsługiwanych dziennie?

Obsługujemy tylko jedną pełną kopię zapasową dziennie. Nie można utworzyć różnicowej kopii zapasowej i pełnej kopii zapasowej w tym samym dniu.

### <a name="do-successful-backup-jobs-create-alerts"></a>Czy zakończone pomyślnie zadania tworzenia kopii zapasowej tworzą alerty?

Nie. Zakończone pomyślnie zadania tworzenia kopii zapasowej nie generują alertów. Alerty są wysyłane tylko w przypadku zadań tworzenia kopii zapasowej, które się nie powiodły. Szczegółowe zachowanie alertów portalu zostało udokumentowane w [tym miejscu](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor). Jeśli jednak interesuje Cię alerty nawet w przypadku zadań zakończonych powodzeniem, możesz użyć [Azure monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Czy można zobaczyć zaplanowane zadania tworzenia kopii zapasowej w menu zadania tworzenia kopii zapasowej?

W menu zadania tworzenia kopii zapasowej będą wyświetlane tylko zadania tworzenia kopii zapasowych ad hoc. W przypadku zaplanowanych zadań Użyj [Azure monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="are-future-databases-automatically-added-for-backup"></a>Czy przyszłe bazy danych są automatycznie dodawane do zadania tworzenia kopii zapasowej?

Nie, to nie jest obecnie obsługiwane.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Czy po usunięciu bazy danych z wystąpienia będą wykonywane kopie zapasowe?

Jeśli baza danych zostanie porzucona z wystąpienia SAP HANA, nadal będą podejmowane kopie zapasowe bazy danych. Oznacza to, że usunięta baza danych zaczyna wyglądać jak w złej kondycji w obszarze **elementy kopii zapasowej** i nadal jest chroniona.
Prawidłowym sposobem zatrzymania ochrony tej bazy danych jest wykonanie **operacji zatrzymania wykonywania kopii zapasowej z usuwaniem danych z** tej bazy danych.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Jeśli zmienię nazwę bazy danych po jej włączeniu, jakie będzie zachowanie?

Baza danych o zmienionej nazwie jest traktowana jako nowa baza danych. W związku z tym usługa będzie traktować tę sytuację tak, jakby baza danych nie została znaleziona, a kopie zapasowe kończą się niepowodzeniem. Baza danych o zmienionej nazwie będzie wyświetlana jako nowa baza danych i musi być skonfigurowana do ochrony.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Jakie są wymagania wstępne dotyczące tworzenia kopii zapasowych baz danych SAP HANA na maszynie wirtualnej platformy Azure?

Zapoznaj się z [wymaganiami wstępnymi](tutorial-backup-sap-hana-db.md#prerequisites) i [co robi skrypt przed rejestracją](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) .

### <a name="what-permissions-should-be-set-so-azure-can-back-up-sap-hana-databases"></a>Jakie uprawnienia należy ustawić, aby platforma Azure mogła tworzyć kopie zapasowe baz danych SAP HANA.

Uruchomienie skryptu przed rejestracją ustawia wymagane uprawnienia, aby system Azure mógł tworzyć kopie zapasowe baz danych SAP HANA. Możesz znaleźć więcej informacji na temat tego, co robi skrypt [here](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)przed rejestracją.

### <a name="will-backups-work-after-migrating-sap-hana-from-sdc-to-mdc"></a>Czy kopie zapasowe będą wykonywane po przeprowadzeniu migracji SAP HANA z SDC do MDC?

Zapoznaj się z [tą sekcją](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#sdc-to-mdc-upgrade-with-a-change-in-sid) przewodnika rozwiązywania problemów.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>Czy można utworzyć kopię zapasową platformy Azure HANA względem wirtualnego adresu IP (modułu równoważenia obciążenia), a nie maszyny wirtualnej?

Obecnie nie mamy możliwości skonfigurowania rozwiązania wyłącznie dla wirtualnego adresu IP. Potrzebujemy maszyny wirtualnej, aby wykonać rozwiązanie.

### <a name="how-can-i-move-an-on-demand-backup-to-the-local-file-system-instead-of-the-azure-vault"></a>Jak przenieść kopię zapasową na żądanie do lokalnego systemu plików zamiast z magazynu platformy Azure?

1. Zaczekaj na ukończenie aktualnie uruchomionej kopii zapasowej w wymaganej bazie danych (Sprawdź, czy w programie Studio została ukończona)
1. Wyłącz kopie zapasowe dzienników i ustaw kopię zapasową wykazu w **systemie plików** dla żądanej bazy danych, wykonując następujące czynności:
1. Kliknij dwukrotnie pozycję **SYSTEMDB**  ->  **Konfiguracja**SYSTEMDB  ->  **Wybierz pozycję Filtr bazy danych**  ->  **(Dziennik)**
    1. Ustaw enable_auto_log_backup na **nie**
    1. Ustaw log_backup_using_backint na **wartość false**
1. Wykonaj kopię zapasową na żądanie w wymaganej bazie danych i poczekaj na zakończenie tworzenia kopii zapasowych i wykazu.
1. Przywróć poprzednie ustawienia, aby umożliwić tworzenie kopii zapasowych w magazynie platformy Azure:
    1. Ustaw wartość enable_auto_log_backup na **tak**
    1. Ustaw log_backup_using_backint na **wartość true**

### <a name="how-can-i-use-sap-hana-backup-with-my-hana-replication-set-up"></a>Jak mogę użyć SAP HANA kopii zapasowej za pomocą konfiguracji replikacji platformy HANA?

Obecnie Azure Backup nie ma możliwości zrozumienia konfiguracji HSR. Oznacza to, że podstawowy i pomocniczy węzeł HSR będą traktowane jako dwie pojedyncze, niepowiązane maszyny wirtualne. Najpierw musisz skonfigurować kopię zapasową w węźle podstawowym. Po przełączeniu w tryb failover kopia zapasowa musi być skonfigurowana w węźle pomocniczym (który będzie teraz węzłem podstawowym). Nie istnieje automatyczne przełączanie kopii zapasowej do drugiego węzła.

Aby utworzyć kopię zapasową danych z aktywnego (podstawowego) węzła w dowolnym momencie, możesz **przełączyć ochronę** do węzła pomocniczego, który teraz stał się serwerem podstawowym po zakończeniu pracy awaryjnej.

Aby przeprowadzić tę **ochronę przed przełączeniem**, wykonaj następujące kroki:

- [Zatrzymaj ochronę](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) (z zachowaniem danych) na serwerze podstawowym
- Uruchamianie [skryptu przed rejestracją](https://aka.ms/scriptforpermsonhana) w węźle pomocniczym
- [Odnajdywanie baz danych](tutorial-backup-sap-hana-db.md#discover-the-databases) w węźle pomocniczym i Konfigurowanie na nich [kopii zapasowych](tutorial-backup-sap-hana-db.md#configure-backup)

Te kroki należy wykonać ręcznie po każdym przejściu w tryb failover. Te kroki można wykonać za pomocą wiersza polecenia/REST protokołu HTTP oprócz Azure Portal. Aby zautomatyzować te kroki, możesz użyć elementu Runbook platformy Azure.

Oto szczegółowy przykład sposobu, w jaki należy wykonać **ochronę przełącznika** :

W tym przykładzie masz dwa węzły — węzeł 1 (podstawowy) i węzeł 2 (pomocniczy) w konfiguracji HSR.  Kopie zapasowe są konfigurowane w węźle 1. Jak wspomniano powyżej, nie próbuj jeszcze konfigurować kopii zapasowych w węźle 2.

Gdy następuje pierwszy tryb failover, węzeł 2 stanie się podstawowym. Następnie

1. Zatrzymaj ochronę węzła 1 (poprzedni podstawowy) przy użyciu opcji Zachowaj dane.
1. Uruchom skrypt poprzedzający rejestrację w węźle 2 (który jest teraz podstawowym).
1. Odnajdywanie baz danych w węźle 2, przypisywanie zasad tworzenia kopii zapasowych i konfigurowanie kopii zapasowych.

Następnie pierwsza pełna kopia zapasowa jest wyzwalana w węźle 2 i po tym zakończeniu kopie zapasowe dziennika zaczynają się.

Po następnym przełączeniu w tryb failover węzeł 1 zmieni się na podstawowy, a węzeł 2 stanie się pomocniczy. Teraz Powtarzaj proces:

1. Zatrzymaj ochronę węzła 2 przy użyciu opcji Zachowaj dane.
1. Uruchom skrypt poprzedzający rejestrację w węźle 1 (który ponownie stał się podstawowym)
1. Następnie [Wznów tworzenie kopii zapasowej](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) w węźle 1 z wymaganymi zasadami (ponieważ kopie zapasowe zostały zatrzymane wcześniej w węźle 1).

Następnie zostanie wyzwolona pełna kopia zapasowa w węźle 1 i po zakończeniu wykonywania kopii zapasowych dziennika.

## <a name="restore"></a>Przywracanie

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Dlaczego nie widzę systemu HANA, do którego ma zostać przywrócona baza danych?

Sprawdź, czy zostały spełnione wszystkie wymagania wstępne dotyczące przywracania SAP HANA wystąpienia docelowego. Aby uzyskać więcej informacji, zobacz [wymagania wstępne — przywracanie SAP HANA baz danych na maszynie wirtualnej platformy Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Dlaczego operacja zastępowania bazy danych nie powiedzie się w przypadku mojej usługi?

Upewnij się, że opcja **Wymuszaj zastępowanie** jest zaznaczona podczas przywracania.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Dlaczego widzę błąd "systemy źródłowe i docelowe na potrzeby przywracania są niezgodne"?

Zapoznaj się z SAP HANA Uwaga [1642148](https://launchpad.support.sap.com/#/notes/1642148) , aby zobaczyć, jakie typy przywracania są obecnie obsługiwane.

### <a name="can-i-use-a-backup-of-a-database-running-on-sles-to-restore-to-an-rhel-hana-system-or-vice-versa"></a>Czy można użyć kopii zapasowej bazy danych działającej w usłudze SLES, aby przywrócić systemowi RHEL HANA lub odwrotnie?

Tak, można użyć kopii zapasowych przesyłania strumieniowego wyzwalanych w bazie danych HANA działającej w systemie SLES, aby przywrócić ją do systemu RHEL HANA i na odwrót. Oznacza to, że przywracanie między systemami operacyjnymi jest możliwe przy użyciu kopii zapasowych przesyłania strumieniowego. Należy jednak upewnić się, że system HANA, do którego ma zostać przywrócona, i system HANA używany do przywracania, są zgodne do przywracania w zależności od SAP. Zapoznaj się z SAP HANA Uwaga [1642148](https://launchpad.support.sap.com/#/notes/1642148) , aby zobaczyć, które typy przywracania są zgodne.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [tworzyć kopie zapasowe baz danych SAP HANA](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) działających na maszynach wirtualnych platformy Azure.
