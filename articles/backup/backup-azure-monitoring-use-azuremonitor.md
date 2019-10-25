---
title: 'Azure Backup: monitorowanie Azure Backup za pomocą Azure Monitor'
description: Monitoruj Azure Backup obciążenia i twórz niestandardowe alerty przy użyciu Azure Monitor.
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: Log Analytics; Azure Backup; Alerty Ustawienia diagnostyczne; Grupy akcji
ms.service: backup
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: dacurwin
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 2b951c6660143b1bd2f6502a5441aec3ba8d71e1
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792840"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Monitorowanie na dużą skalę przy użyciu Azure Monitor

Azure Backup udostępnia [wbudowane funkcje monitorowania i alertów](backup-azure-monitoring-built-in-monitor.md) w magazynie Recovery Services. Te możliwości są dostępne bez dodatkowej infrastruktury zarządzania. Jednak ta wbudowana usługa jest ograniczona w następujących scenariuszach:

- W przypadku monitorowania danych z wielu Recovery Services magazynów między subskrypcjami
- Jeśli preferowany kanał powiadomień *nie* jest adresem e-mail
- Jeśli użytkownicy chcą mieć alerty w celu uzyskania większej liczby scenariuszy
- Jeśli chcesz wyświetlić informacje z składnika lokalnego, takiego jak System Center Data Protection Manager na platformie Azure, które nie są wyświetlane w obszarze [**zadania tworzenia kopii zapasowej**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) lub [**alerty kopii zapasowych**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Używanie Log Analytics obszaru roboczego

> [!NOTE]
> Dane z kopii zapasowych maszyn wirtualnych platformy Azure, agenta Azure Backup, programu System Center Data Protection Manager, kopii zapasowych SQL na maszynach wirtualnych platformy Azure i tworzenia kopii zapasowych Azure Files udostępniania są pompy w obszarze roboczym Log Analytics za pośrednictwem ustawień diagnostycznych. Obsługa Microsoft Azure Backup Server (serwera usługi MAB) zostanie dodana wkrótce

Aby monitorować/raportować na dużą skalę, potrzebne są możliwości dwóch usług platformy Azure. *Ustawienia diagnostyczne* umożliwiają wysyłanie danych z wielu zasobów Azure Resource Manager do innego zasobu. *Log Analytics* generuje niestandardowe alerty, w których można używać grup akcji do definiowania innych kanałów powiadomień.

W poniższych sekcjach szczegółowo opisano, jak używać Log Analytics do monitorowania Azure Backup w odpowiedniej skali.

### <a name="configure-diagnostic-settings"></a>Konfigurowanie ustawień diagnostycznych

Azure Resource Manager zasoby, takie jak magazyn Recovery Services, rejestruje informacje o operacjach zaplanowanych i operacjach wyzwalanych przez użytkownika jako danych diagnostycznych.

W sekcji monitorowanie wybierz pozycję **Ustawienia diagnostyczne** i określ cel dla danych diagnostycznych magazynu Recovery Services.

![Ustawienie diagnostyczne Recovery Services magazynu, określanie wartości docelowej Log Analytics](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

Możesz wskazać obszar roboczy Log Analytics z innej subskrypcji. Aby monitorować magazyny między subskrypcjami w jednym miejscu, wybierz ten sam obszar roboczy Log Analytics dla wielu magazynów Recovery Services. Aby obkanałować wszystkie informacje dotyczące Azure Backup do obszaru roboczego Log Analytics, wybierz **AzureBackupReport** jako dziennik.

> [!IMPORTANT]
> Po zakończeniu konfiguracji należy odczekać 24 godziny na zakończenie wypychania danych. Po pomyślnym wypchnięciu danych wszystkie zdarzenia są wypychane zgodnie z opisem w dalszej części tego artykułu, w [sekcji częstotliwość](#diagnostic-data-update-frequency).

### <a name="deploy-a-solution-to-the-log-analytics-workspace"></a>Wdróż rozwiązanie w obszarze roboczym Log Analytics

> [!IMPORTANT]
> Firma Microsoft udostępniła zaktualizowany [szablon](https://azure.microsoft.com/resources/templates/101-backup-la-reporting/) z wielojęzycznym widokiem dla monitorowania i raportowania opartego na programie La, w Azure Backup. Należy pamiętać, że użytkownicy, którzy korzystali z [wcześniejszego rozwiązania](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) , będą nadal widzieć je w swoich obszarach roboczych nawet po wdrożeniu nowego rozwiązania. Jednak stare rozwiązanie może zapewnić niedokładne wyniki ze względu na pewne drobne zmiany schematu. W związku z tym użytkownicy muszą wdrożyć nowy szablon.

Gdy dane są w obszarze roboczym Log Analytics, [Wdróż szablon GitHub](https://azure.microsoft.com/resources/templates/101-backup-la-reporting/) , aby log Analytics do wizualizacji danych. Aby prawidłowo zidentyfikować obszar roboczy, upewnij się, że nadajesz mu tę samą grupę zasobów, nazwę obszaru roboczego i lokalizację obszaru roboczego. Następnie zainstaluj ten szablon w obszarze roboczym.

### <a name="view-azure-backup-data-by-using-log-analytics"></a>Wyświetlanie danych Azure Backup przy użyciu Log Analytics

Po wdrożeniu szablonu rozwiązanie do monitorowania i raportowania w Azure Backup będzie widoczne w obszarze Podsumowanie obszaru roboczego. Aby przejść do podsumowania, wykonaj jedną z następujących ścieżek:

- **Azure monitor**: w sekcji **Insights** wybierz pozycję **więcej** , a następnie wybierz odpowiedni obszar roboczy.
- **Log Analytics obszary robocze**: wybierz odpowiedni obszar roboczy, a następnie w obszarze **Ogólne**wybierz pozycję **Podsumowanie obszaru roboczego**.

![Kafelki monitorowania Log Analytics i raportowania](media/backup-azure-monitoring-laworkspace/la-azurebackup-overview-dashboard.png)

Po wybraniu dowolnego z kafelków przeglądu można wyświetlić dalsze informacje. Oto niektóre z raportów, które zobaczysz:

- Zadania tworzenia kopii zapasowej bez dziennika

   ![Wykresy Log Analytics dla zadań tworzenia kopii zapasowej](media/backup-azure-monitoring-laworkspace/la-azurebackup-backupjobsnonlog.png)

- Alerty z kopii zapasowej zasobów platformy Azure

   ![Log Analytics Graph dla zadań przywracania](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertsazure.png)

Podobnie, klikając inne kafelki, zobaczysz raporty dotyczące zadań przywracania, magazynu w chmurze, elementów kopii zapasowych, alertów z kopii zapasowych zasobów lokalnych i zadań tworzenia kopii zapasowych dziennika.

Te wykresy są udostępniane z szablonem. Jeśli zachodzi taka potrzeba, możesz edytować wykresy lub dodać więcej wykresów.

### <a name="create-alerts-by-using-log-analytics"></a>Tworzenie alertów przy użyciu Log Analytics

W Azure Monitor można tworzyć własne alerty w obszarze roboczym Log Analytics. W obszarze roboczym możesz użyć *grup akcji platformy Azure* , aby wybrać preferowany mechanizm powiadamiania.

> [!IMPORTANT]
> Aby uzyskać informacje na temat kosztów tworzenia tego zapytania, zobacz [Azure monitor Cennik](https://azure.microsoft.com/pricing/details/monitor/).

Wybierz dowolne wykresy, aby otworzyć sekcję **dzienniki** obszaru roboczego log Analytics. W sekcji **dzienniki** Edytuj zapytania i Utwórz z nich alerty.

![Tworzenie alertu w obszarze roboczym Log Analytics](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

Po wybraniu **nowej reguły alertu**zostanie otwarta strona tworzenie alertu Azure monitor, jak pokazano na poniższej ilustracji. W tym miejscu zasób został już oznaczony jako obszar roboczy Log Analytics, a podano integrację z grupą akcji.

![Strona tworzenia alertu Log Analytics](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Warunek alertu

Definiowanie charakterystyki alertu to jego warunek wyzwalania. Wybierz opcję **warunek** , aby automatycznie załadować zapytanie Kusto na stronie **dzienników** , jak pokazano na poniższej ilustracji. W tym miejscu możesz edytować warunek, aby odpowiadał Twoim potrzebom. Aby uzyskać więcej informacji, zobacz [przykładowe zapytania Kusto](#sample-kusto-queries).

![Konfigurowanie warunku alertu](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

W razie potrzeby można edytować zapytanie Kusto. Wybierz próg, okres i częstotliwość. Próg określa, kiedy zostanie zgłoszony alert. Okres jest oknem czasu, w którym jest uruchamiane zapytanie. Na przykład jeśli próg jest większy niż 0, okres wynosi 5 minut, a częstotliwość wynosi 5 minut, a następnie reguła uruchamia kwerendę co 5 minut, przeglądając poprzedni 5 minut. Jeśli liczba wyników jest większa od 0, otrzymasz powiadomienie za pomocą wybranej grupy akcji.

#### <a name="alert-action-groups"></a>Grupy akcji alertów

Użyj grupy akcji, aby określić kanał powiadomień. Aby wyświetlić dostępne mechanizmy powiadomień, w obszarze **grupy akcji**wybierz pozycję **Utwórz nowy**.

![Dostępne mechanizmy powiadamiania w oknie "Dodawanie grupy akcji"](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Wszystkie wymagania związane z alertami i monitorowaniem można spełnić wyłącznie przed Log Analytics lub użyć Log Analytics, aby uzupełnić wbudowane powiadomienia.

Aby uzyskać więcej informacji, zobacz [Tworzenie i wyświetlanie alertów dzienników oraz zarządzanie nimi przy użyciu Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) i [Tworzenie grup akcji w Azure Portal oraz zarządzanie nimi](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).

### <a name="sample-kusto-queries"></a>Przykładowe zapytania Kusto

Wykresy domyślne dają Kusto zapytania dotyczące podstawowych scenariuszy, w których można tworzyć alerty. Możesz również zmodyfikować zapytania, aby pobrać dane, które mają być wyzwalane. Wklej następujące przykładowe zapytania Kusto na stronie **dzienniki** , a następnie utwórz alerty dla zapytań:

- Wszystkie zadania tworzenia kopii zapasowej zakończone powodzeniem

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- Wszystkie zadania tworzenia kopii zapasowej zakończone niepowodzeniem

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- Wszystkie pomyślne zadania tworzenia kopii zapasowej maszyny wirtualnej platformy Azure

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Wszystkie pomyślne zadania tworzenia kopii zapasowej dziennika SQL

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Wszystkie pomyślne zadania agenta Azure Backup

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

### <a name="diagnostic-data-update-frequency"></a>Częstotliwość aktualizacji danych diagnostycznych

Dane diagnostyczne z magazynu są napompowane do obszaru roboczego Log Analytics z pewnym opóźnieniem. Każde zdarzenie dociera do obszaru roboczego Log Analytics od *20 do 30 minut* po wypchnięciu z magazynu Recovery Services. Poniżej znajdują się dalsze szczegółowe informacje na temat opóźnienia:

- W przypadku wszystkich rozwiązań alerty wbudowane w usłudze Backup są wypychane po ich utworzeniu. Tak więc zazwyczaj pojawiają się w obszarze roboczym Log Analytics po 20 – 30 minutach.
- W przypadku wszystkich rozwiązań zadania tworzenia kopii zapasowych ad hoc i zadania przywracania są wypychane zaraz po *zakończeniu*.
- W przypadku wszystkich rozwiązań z wyjątkiem kopii zapasowej SQL zaplanowane zadania tworzenia kopii zapasowej są wypychane zaraz po *zakończeniu*.
- W przypadku kopii zapasowej SQL, ponieważ kopie zapasowe dzienników mogą odbywać się co 15 minut, informacje dotyczące wszystkich ukończonych zadań tworzenia kopii zapasowej, w tym dzienników, są przetwarzane wsadowe i wypychane co 6 godzin.
- W przypadku wszystkich rozwiązań, inne informacje, takie jak element kopii zapasowej, zasady, punkty odzyskiwania, magazyn itd., są wypychane co najmniej *raz dziennie.*
- Zmiana konfiguracji kopii zapasowej (na przykład zmiana zasad lub zasad edycji) wyzwala wypychanie wszystkich powiązanych informacji o kopii zapasowej.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Korzystanie z dzienników aktywności magazynu Recovery Services

> [!CAUTION]
> Poniższe kroki dotyczą tylko *kopii zapasowych maszyn wirtualnych platformy Azure.* Nie można użyć tych kroków dla rozwiązań takich jak Agent Azure Backup, kopie zapasowe SQL na platformie Azure lub Azure Files.

Dzienników aktywności można także użyć do uzyskania powiadomień o zdarzeniach, takich jak powodzenie wykonywania kopii zapasowej. Aby rozpocząć, wykonaj następujące kroki:

1. Zaloguj się do Azure Portal.
1. Otwórz odpowiedni magazyn Recovery Services.
1. W oknie właściwości magazynu Otwórz sekcję **Dziennik aktywności** .

Aby zidentyfikować odpowiedni dziennik i utworzyć alert:

1. Sprawdź, czy otrzymujesz dzienniki aktywności dla udanych kopii zapasowych, stosując filtry pokazane na poniższej ilustracji. W razie potrzeby zmień wartość **TimeSpan** , aby wyświetlić rekordy.

   ![Filtrowanie w poszukiwaniu dzienników aktywności dla kopii zapasowych maszyny wirtualnej platformy Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. Wybierz nazwę operacji, aby wyświetlić odpowiednie szczegóły.
1. Wybierz pozycję **Nowa reguła alertu** , aby otworzyć stronę **Tworzenie reguły** .
1. Utwórz alert, wykonując czynności opisane w temacie [Tworzenie i wyświetlanie alertów dziennika aktywności oraz zarządzanie nimi za pomocą Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

   ![Nowa reguła alertu](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

W tym miejscu zasób jest magazynem Recovery Services. Powtórz te same kroki dla wszystkich magazynów, w których chcesz otrzymywać powiadomienia za pomocą dzienników aktywności. Warunek nie będzie miał wartości progowej, kropki lub częstotliwości, ponieważ ten alert jest oparty na zdarzeniach. Po wygenerowaniu odpowiedniego dziennika aktywności zostanie zgłoszony alert.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Monitorowanie na dużą skalę przy użyciu Log Analytics

Można wyświetlić wszystkie alerty utworzone na podstawie dzienników aktywności i obszarów roboczych Log Analytics w Azure Monitor. Po prostu otwórz okienko **alerty** po lewej stronie.

Mimo że można otrzymywać powiadomienia za pośrednictwem dzienników aktywności, zdecydowanie zalecamy używanie Log Analytics, a nie dzienników aktywności do monitorowania w odpowiedniej skali. Oto dlaczego:

- **Ograniczone scenariusze**: powiadomienia za pomocą dzienników aktywności dotyczą tylko kopii zapasowych maszyn wirtualnych platformy Azure. Powiadomienia muszą zostać skonfigurowane dla każdego magazynu Recovery Services.
- **Dopasowanie definicji**: działanie zaplanowanej kopii zapasowej nie jest zgodne z najnowszą definicją dzienników aktywności. Zamiast tego są wyrównane z [dziennikami zasobów](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-resource-logs-in-a-workspace). To wyrównanie powoduje nieoczekiwane skutki, gdy dane przepływają przez kanał dziennika aktywności są zmieniane.
- **Problemy z kanałem dziennika aktywności**: w magazynach Recovery Services, dzienniki aktywności, które są pompy Azure Backup podążają za nowym modelem. Niestety ta zmiana ma wpływ na generowanie dzienników aktywności w Azure Government, na platformie Azure (Niemcy) i na platformie Azure (Chiny). Jeśli użytkownicy tych usług w chmurze tworzą lub konfigurują alerty z dzienników aktywności w Azure Monitor, alerty nie zostaną wyzwolone. Ponadto w przypadku wszystkich regionów publicznych platformy Azure, jeśli użytkownik [zbiera Recovery Services dzienników aktywności w obszarze roboczym log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), te dzienniki nie będą wyświetlane.

Użyj Log Analytics obszaru roboczego do monitorowania i generowania alertów na dużą skalę dla wszystkich obciążeń chronionych przez Azure Backup.

## <a name="next-steps"></a>Następne kroki

Aby utworzyć niestandardowe zapytania, zobacz [log Analytics model danych](backup-azure-log-analytics-data-model.md).
