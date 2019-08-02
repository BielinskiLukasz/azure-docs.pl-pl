---
title: Monitoruj SQL Data Sync platformy Azure za pomocą dzienników Azure Monitor | Microsoft Docs
description: Dowiedz się, jak monitorować usługę Azure SQL Data Sync przy użyciu dzienników Azure Monitor
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: d1461a1bb026d478d51a5f79cc02b34172524db6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566419"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>Monitorowanie SQL Data Sync przy użyciu dzienników Azure Monitor 

Aby sprawdzić dziennik aktywności SQL Data Sync i wykryć błędy i ostrzeżenia, wcześniej trzeba było sprawdzić SQL Data Sync ręcznie w Azure Portal lub użyć programu PowerShell lub interfejsu API REST. Wykonaj kroki opisane w tym artykule, aby skonfigurować rozwiązanie niestandardowe, które poprawi środowisko monitorowania synchronizacji danych. Możesz dostosować to rozwiązanie tak, aby było zgodne z Twoim scenariuszem.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Omówienie usługi SQL Data Sync zawiera temat [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)](sql-database-sync-data.md).

> [!IMPORTANT]
> Usługa Azure SQL Data Sync **nie** obsługuje obecnie wystąpienia zarządzanego usługi Azure SQL Database.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Pulpit nawigacyjny monitorowania dla wszystkich grup synchronizacji 

Nie musisz już przeglądać dzienników każdej grupy synchronizacji, aby wyszukać problemy. Możesz monitorować wszystkie grupy synchronizacji z dowolnej subskrypcji w jednym miejscu przy użyciu niestandardowego widoku Azure Monitor. Ten widok prezentuje informacje, które są istotne dla SQL Data Sync klientów.

![Pulpit nawigacyjny monitorowania synchronizacji danych](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Zautomatyzowane powiadomienia E-mail

Nie trzeba już ręcznie sprawdzać dziennika w Azure Portal lub za pomocą programu PowerShell lub interfejsu API REST. Za pomocą [dzienników Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)można tworzyć alerty, które przechodzą bezpośrednio na adresy e-mail osób, które muszą je zobaczyć po wystąpieniu błędu.

![Powiadomienia e-mail dotyczące synchronizacji danych](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Jak skonfigurować te funkcje monitorowania? 

Zaimplementuj rozwiązanie niestandardowego Azure Monitor dzienników monitorowania dla SQL Data Sync w czasie krótszym niż godzina, wykonując następujące czynności:

Należy skonfigurować trzy składniki:

-   Element Runbook programu PowerShell, który umożliwia strumieniowe SQL Data Sync danych dziennika do Azure Monitor dzienników.

-   Alert Azure Monitor dotyczący powiadomień e-mail.

-   Widok Azure Monitor do monitorowania.

### <a name="samples-to-download"></a>Próbki do pobrania

Pobierz następujące dwie przykłady:

-   [Element Runbook programu PowerShell synchronizacji danych](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Widok Azure Monitor synchronizacji danych](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że skonfigurowano następujące elementy:

-   Konto Azure Automation

-   Obszar roboczy usługi Log Analytics

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>Element Runbook programu PowerShell umożliwiający pobieranie SQL Data Sync dziennika 

Użyj elementu Runbook programu PowerShell hostowanego w Azure Automation, aby ściągnąć SQL Data Sync dane dziennika i wysłać je do dzienników Azure Monitor. Przykładowy skrypt jest dołączony. Jako warunek wstępny musisz mieć konto Azure Automation. Następnie należy utworzyć element Runbook i zaplanować jego uruchomienie. 

### <a name="create-a-runbook"></a>Utwórz element Runbook

Aby uzyskać więcej informacji na temat tworzenia elementu Runbook, zobacz [pierwszy element Runbook programu PowerShell](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  W obszarze konto Azure Automation wybierz kartę **elementy Runbook** w obszarze Automatyzacja procesów.

2.  Wybierz pozycję **Dodaj element Runbook** w lewym górnym rogu strony elementów Runbook.

3.  Wybierz pozycję **Importuj istniejący element Runbook**.

4.  W obszarze **plik Runbook**Użyj danego `DataSyncLogPowerShellRunbook` pliku. Ustaw **Typ elementu Runbook** jako `PowerShell`. Nadaj elementowi Runbook nazwę.

5.  Wybierz pozycję **Utwórz**. Masz teraz element Runbook.

6.  W obszarze konto Azure Automation wybierz kartę **zmienne** w obszarze zasoby udostępnione.

7.  Wybierz pozycję **Dodaj zmienną** na stronie zmienne. Utwórz zmienną do przechowywania czasu ostatniego wykonania dla elementu Runbook. Jeśli masz wiele elementów Runbook, musisz mieć jedną zmienną dla każdego elementu Runbook.

8.  Ustaw nazwę zmiennej jako `DataSyncLogLastUpdatedTime` i ustaw jej typ jako DateTime.

9.  Wybierz element Runbook, a następnie kliknij przycisk Edytuj w górnej części strony.

10. Wprowadź zmiany wymagane dla Twojego konta i konfiguracji SQL Data Sync. (Aby uzyskać bardziej szczegółowe informacje, zobacz przykładowy skrypt).

    1.  Informacje o platformie Azure.

    2.  Informacje o grupie synchronizacji.

    3.  Azure Monitor informacje o dziennikach. Znajdź te informacje w witrynie Azure Portal | Ustawienia | Połączone źródła. Aby uzyskać więcej informacji na temat wysyłania danych do dzienników Azure Monitor, zobacz [wysyłanie danych do Azure monitor dzienników za pomocą interfejsu API modułu zbierającego dane http (wersja zapoznawcza)](../azure-monitor/platform/data-collector-api.md).

11. Uruchom element Runbook w okienku testów. Sprawdź, czy zakończyło się pomyślnie.

    Jeśli występują błędy, upewnij się, że zainstalowano najnowszy moduł programu PowerShell. Najnowszy moduł programu PowerShell można zainstalować w **galerii modułów** na koncie usługi Automation.

12. Kliknij pozycję **Publikuj** .

### <a name="schedule-the-runbook"></a>Planowanie elementu Runbook

Aby zaplanować element Runbook:

1.  W obszarze elementu Runbook wybierz kartę **harmonogramy** w obszarze zasoby.

2.  Na stronie harmonogramy wybierz pozycję **Dodaj harmonogram** .

3.  Wybierz pozycję **Połącz harmonogram z elementem Runbook**.

4.  Wybierz pozycję **Utwórz nowy harmonogram.**

5.  Ustaw **cykl** na cykliczny i Ustaw żądany interwał. Użyj tego samego interwału w skrypcie i w Azure Monitor dziennikach.

6.  Wybierz pozycję **Utwórz**.

### <a name="check-the-automation"></a>Sprawdź automatyzację

Aby monitorować, czy Automatyzacja działa zgodnie z oczekiwaniami, w obszarze **Przegląd** dla konta usługi Automation Znajdź widok **statystyki zadań** w obszarze **monitorowanie**. Przypnij ten widok do pulpitu nawigacyjnego w celu łatwego wyświetlania. Pomyślne uruchomienia elementu Runbook show jako "ukończone" i nieudane uruchomienia są wyświetlane jako "Niepowodzenie".

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>Utwórz alert czytnika Azure Monitor dla powiadomień E-mail

Aby utworzyć alert korzystający z dzienników Azure Monitor, wykonaj następujące czynności. Jako wymaganie wstępne należy mieć Azure Monitor dzienniki połączone z obszarem roboczym Log Analytics.

1.  W Azure Portal wybierz pozycję **przeszukiwanie dzienników**.

2.  Utwórz zapytanie, aby wybrać błędy i ostrzeżenia według grupy synchronizacji w wybranym interwale. Przykład:

    `Type=DataSyncLog\_CL LogLevel\_s!=Success| measure count() by SyncGroupName\_s interval 60minute`

3.  Po uruchomieniu zapytania wybierz dzwonek z informacją o **alercie**.

4.  W obszarze **Generuj alert na podstawie**wybierz pozycję **pomiar metryki**.

    1.  Ustaw wartość zagregowaną na **większą niż**.

    2.  Następnie **wprowadź**wartość progową, która ma upłynąć przed odebraniem powiadomień. Błędy przejściowe są oczekiwane w synchronizacji danych. Aby zmniejszyć szum, ustaw próg na 5.

5.  W obszarze **Akcje**ustaw opcję **powiadomień e-mail** na wartość "tak". Wprowadź żądanych adresatów wiadomości e-mail.

6.  Kliknij polecenie **Zapisz**. Określeni adresaci odbierają teraz powiadomienia e-mail w przypadku wystąpienia błędów.

## <a name="create-an-azure-monitor-view-for-monitoring"></a>Tworzenie widoku Azure Monitor na potrzeby monitorowania

Ten krok powoduje utworzenie widoku Azure Monitor w celu wizualnego monitorowania wszystkich określonych grup synchronizacji. Widok zawiera kilka składników:

-   Kafelek z omówieniem, który pokazuje, ile błędów, sukcesów i ostrzeżeń ma wszystkie grupy synchronizacji.

-   Kafelek dla wszystkich grup synchronizacji, który pokazuje liczbę błędów i ostrzeżeń dla każdej grupy synchronizacji. Grupy bez problemów nie są wyświetlane na tym kafelku.

-   Kafelek dla każdej grupy synchronizacji, który pokazuje liczbę błędów, sukcesów i ostrzeżeń oraz ostatnie komunikaty o błędach.

Aby skonfigurować widok Azure Monitor, wykonaj następujące czynności:

1.  Na stronie głównej Log Analytics obszarze roboczym wybierz pozycję Plus po lewej stronie, aby otworzyć **projektanta widoków**.

2.  Wybierz pozycję **Importuj** na górnym pasku projektanta widoków. Następnie wybierz plik z przykładem "DataSyncLogOMSView".

3.  Widok przykładowy służy do zarządzania dwiema grupami synchronizacji. Edytuj ten widok, aby dopasować go do Twojego scenariusza. Kliknij przycisk **Edytuj** i wprowadź następujące zmiany:

    1.  Utwórz nowe obiekty "pierścieniowe listy &" w galerii zgodnie z wymaganiami.

    2.  Na każdym kafelku zaktualizuj zapytania przy użyciu swoich informacji.

        1.  Na każdym kafelku Zmień interwał TimeStamp_t.

        2.  Na kafelkach dla każdej grupy synchronizacji należy zaktualizować nazwy grup synchronizacji.

    3.  Na każdym kafelku zaktualizuj tytuł odpowiednio do wymagań.

4.  Kliknij przycisk **Zapisz** i widok jest gotowy.

## <a name="cost-of-this-solution"></a>Koszt tego rozwiązania

W większości przypadków to rozwiązanie jest bezpłatne.

**Azure Automation:** W zależności od użycia może nastąpić poniesienie kosztu związanego z kontem Azure Automation. Pierwsze 500 minut czasu wykonywania zadania miesięcznie są bezpłatne. W większości przypadków użycie tego rozwiązania jest mniejsze niż 500 minut miesięcznie. Aby uniknąć naliczania opłat, Zaplanuj uruchomienie elementu Runbook w przedziale od 2 do kilku godzin. Aby uzyskać więcej informacji, zobacz [Cennik usługi Automation](https://azure.microsoft.com/pricing/details/automation/).

**Azure Monitor dzienników:** W zależności od użycia może istnieć koszt skojarzony z dziennikami Azure Monitor. Warstwa Bezpłatna obejmuje 500 MB pobieranych danych dziennie. W większości przypadków to rozwiązanie powinno pozyskać mniej niż 500 MB dziennie. Aby zmniejszyć użycie, użyj filtrowania tylko niepowodzeń zawartego w elemencie Runbook. Jeśli używasz ponad 500 MB dziennie, przeprowadź uaktualnienie do warstwy płatnej, aby uniknąć ryzyka zatrzymania analizy po osiągnięciu ograniczenia. Aby uzyskać więcej informacji, zobacz [Azure monitor rejestrować cenniki](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Przykłady kodu

Pobierz przykłady kodu opisane w tym artykule z następujących lokalizacji:

-   [Element Runbook programu PowerShell synchronizacji danych](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Widok Azure Monitor synchronizacji danych](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat usługi SQL Data Sync, zobacz:

-   Omówienie — [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync](sql-database-sync-data.md) (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)
-   Konfigurowanie synchronizacji danych
    - W portalu — [Tutorial: Set up SQL Data Sync to sync data between Azure SQL Database and SQL Server on-premises](sql-database-get-started-sql-data-sync.md) (Samouczek: konfigurowanie usługi SQL Data Sync w celu synchronizowania danych między usługą Azure SQL Database i lokalnym programem SQL Server)
    - Z programem PowerShell
        -  [Użycie programu PowerShell do synchronizowania wielu baz danych Azure SQL Database](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agent synchronizacji danych — [Data Sync Agent for Azure SQL Data Sync](sql-database-data-sync-agent.md) (Agent synchronizacji danych dla usługi Azure SQL Data Sync)
-   Najlepsze rozwiązania — [Best practices for Azure SQL Data Sync](sql-database-best-practices-data-sync.md) (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)
-   Rozwiązywanie problemów — [Troubleshoot issues with Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md) (Rozwiązywanie problemów z usługą Azure SQL Data Sync)
-   Aktualizowanie schematu synchronizacji
    -   Za pomocą języka Transact-SQL — [Automate the replication of schema changes in Azure SQL Data Sync](sql-database-update-sync-schema.md) (Automatyzowanie replikacji zmian schematu w usłudze Azure SQL Data Sync)
    -   Używanie programu PowerShell — [Używanie programu PowerShell do zaktualizowania schematu synchronizacji w istniejącej grupie synchronizacji](scripts/sql-database-sync-update-schema.md)

Aby uzyskać więcej informacji na temat usługi SQL Database, zobacz:

-   [Omówienie usługi SQL Database](sql-database-technical-overview.md)
-   [Database Lifecycle Management (Zarządzanie cyklem życia bazy danych)](https://msdn.microsoft.com/library/jj907294.aspx)
