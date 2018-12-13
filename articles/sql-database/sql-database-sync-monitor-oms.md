---
title: Monitorowanie usługi Azure SQL Data Sync za pomocą usługi Log Analytics | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować usługi Azure SQL Data Sync za pomocą usługi Log Analytics
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: d6a1f2829352e8589c8d132a195a425edcbe62c3
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872948"
---
# <a name="monitor-sql-data-sync-with-log-analytics"></a>Monitorowanie SQL Data Sync za pomocą usługi Log Analytics 

Aby sprawdzić dziennik aktywności SQL Data Sync i wykrywać błędy i ostrzeżenia, należy wcześniej było ręcznie sprawdzić SQL Data Sync w witrynie Azure portal lub za pomocą programu PowerShell lub interfejsu API REST. Wykonaj kroki opisane w tym artykule, aby skonfigurować niestandardowe rozwiązanie, które zwiększa synchronizacji danych środowiska do monitorowania. Można dostosować to rozwiązanie, odpowiednio do scenariusza.

Omówienie usługi SQL Data Sync zawiera temat [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)](sql-database-sync-data.md).

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Pulpit nawigacyjny monitorowania dla wszystkich grup synchronizacji 

Nie potrzebujesz już szukać w dziennikach każdej grupy synchronizacji pojedynczo, aby wyszukać problemy. Wszystkie grupy synchronizacji można monitorować z poziomu dowolnej subskrypcji w jednym miejscu, za pomocą niestandardowych widok usługi Log Analytics. Ten widok udostępnia informacje, które są ważne klientom SQL Data Sync.

![Pulpit nawigacyjny monitorowania synchronizacji danych](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Zautomatyzowane powiadomienia E-mail

Nie trzeba sprawdzić dziennik ręcznie w witrynie Azure portal lub za pomocą programu PowerShell lub interfejsu API REST. Za pomocą [usługi Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), możesz utworzyć alerty, które bardziej szczegółowo bezpośrednio na adresy e-mail osób, którym chcesz wyświetlać je po wystąpieniu błędu.

![Powiadomienia e-mail synchronizacji danych](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Jak można skonfigurować te funkcje? 

Implementowanie niestandardowego Log Analytics, rozwiązanie do monitorowania dla SQL Data Sync w mniej niż godzinę, wykonując następujące czynności:

Należy skonfigurować trzy składniki:

-   Element runbook programu PowerShell do źródła danych dziennika SQL Data Sync do usługi Log Analytics.

-   Alert dotyczący powiadomienia e-mail w ramach usługi Log Analytics.

-   Widoki usługi Log Analytics do monitorowania.

### <a name="samples-to-download"></a>Pobierz przykłady

Pobierz poniższe dwa przykłady:

-   [Dane synchronizacji dziennika elementu Runbook z programu PowerShell](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Widoki usługi Log Analytics Data Sync](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że zostały skonfigurowane następujące elementy:

-   Konto usługi Azure Automation

-   Obszar roboczy usługi Log Analytics

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>Element Runbook programu PowerShell można pobrać dziennik synchronizacji danych SQL 

Użyj elementu runbook programu PowerShell, hostowana w usłudze Azure Automation do ściągania danych dziennika SQL Data Sync i wysyłać je do usługi Log Analytics. Przykładowy skrypt jest dołączony. Jako warunek wstępny musisz mieć konto usługi Azure Automation. Następnie należy utworzyć element runbook i zaplanuj jego uruchomienie. 

### <a name="create-a-runbook"></a>Tworzenie elementu runbook

Aby uzyskać więcej informacji na temat tworzenia elementu runbook, zobacz [Mój pierwszy element runbook programu PowerShell](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  W ramach konta usługi Azure Automation wybierz **elementów Runbook** kartę w obszarze automatyzacji procesów.

2.  Wybierz **Dodaj element Runbook** w lewym górnym rogu strony elementów Runbook.

3.  Wybierz **importowanie istniejącego elementu Runbook**.

4.  W obszarze **plikowi elementu Runbook**, użyj podanego `DataSyncLogPowerShellRunbook` pliku. Ustaw **typ elementu Runbook** jako `PowerShell`. Nadaj elementowi runbook nazwę.

5.  Wybierz pozycję **Utwórz**. Masz teraz elementu runbook.

6.  W ramach konta usługi Azure Automation wybierz **zmienne** kartę w obszarze udostępnionych zasobów.

7.  Wybierz **Dodaj zmienną** na zmienne. Utwórz zmienną do przechowywania ostatniego czasu wykonania elementu runbook. Jeśli masz wiele elementów runbook, należy jednej zmiennej dla każdego elementu runbook.

8.  Ustaw nazwę zmiennej jako `DataSyncLogLastUpdatedTime` i ustaw jej typ jako daty/godziny.

9.  Wybierz element runbook, a następnie kliknij przycisk edycji, w górnej części strony.

10. Zmiany wymagane dla Twojego konta i konfiguracji usługi SQL Data Sync. (Aby uzyskać szczegółowe informacje, zobacz przykładowy skrypt).

    1.  Informacje o usłudze Azure.

    2.  Informacje o grupie synchronizacji.

    3.  Rejestrowanie informacji analitycznych. Te informacje można znaleźć w witrynie Azure Portal | Ustawienia | Połączone źródła. Aby uzyskać więcej informacji na temat wysyłania danych do usługi Log Analytics, zobacz [wysyłać dane do usługi Log Analytics przy użyciu interfejsu API modułu zbierającego dane HTTP (wersja zapoznawcza)](../azure-monitor/platform/data-collector-api.md).

11. Uruchom element runbook w okienku testu. Sprawdź, upewnij się, że było pomyślne.

    Jeśli błędy, upewnij się, że masz zainstalowany najnowszy moduł programu PowerShell. Można zainstalować najnowszy moduł programu PowerShell w **Galeria modułów** na koncie usługi Automation.

12. Kliknij przycisk **publikowania**

### <a name="schedule-the-runbook"></a>Harmonogram elementu runbook

Aby zaplanować uruchamianie elementu runbook:

1.  W obszarze elementu runbook, zaznacz **harmonogramy** kartę Resources.

2.  Wybierz **Dodaj harmonogram** na stronie harmonogramów.

3.  Wybierz **powiązania harmonogramu z elementem runbook**.

4.  Wybierz **Utwórz nowy harmonogram.**

5.  Ustaw **cyklu** cyklicznie i ustaw interwał chcesz. Użyj tego samego interwału w tym miejscu, w skrypcie, a w usłudze Log Analytics.

6.  Wybierz pozycję **Utwórz**.

### <a name="check-the-automation"></a>Sprawdź usługi automation

Do monitorowania, czy automatyzacji działa zgodnie z oczekiwaniami, w obszarze **Przegląd** konta usługi automation można znaleźć **statystyki zadania** wyświetlona w obszarze **monitorowanie**. Przypnij ten widok do pulpitu nawigacyjnego w celu łatwego wyświetlania. Udane uruchomienia Pokaż element runbook jako "Ukończone" i niepowodzenie uruchomienia wyświetlane jako "Nieudane".

## <a name="create-a-log-analytics-reader-alert-for-email-notifications"></a>Tworzenie alertu Czytelnik usługi Log Analytics dla powiadomień E-mail

Aby utworzyć alert, który korzysta z usługi Log Analytics, wykonaj następujące czynności. Jako warunek wstępny musi być połączona z obszaru roboczego usługi Log Analytics w usłudze Log Analytics.

1.  W witrynie Azure portal wybierz **wyszukiwanie w dzienniku**.

2.  Utwórz zapytanie, aby wybrać błędy i ostrzeżenia, według grupy synchronizacji w przedziale czasu, który wybrano. Na przykład:

    `Type=DataSyncLog\_CL LogLevel\_s!=Success| measure count() by SyncGroupName\_s interval 60minute`

3.  Po uruchomieniu zapytania, wybierz ikonę dzwonka informujący, że **alertu**.

4.  W obszarze **Generuj alert na podstawie**, wybierz opcję **pomiar metryki**.

    1.  Wartość agregacji **większa**.

    2.  Po **większa**, wprowadź próg upłynąć, zanim otrzymywać powiadomienia. Oczekiwano błędów przejściowych podczas synchronizacji danych. Aby ograniczyć szum, należy ustawić próg do 5.

5.  W obszarze **akcje**ustaw **powiadomienia E-mail** "Yes". Wprowadź adresatów żądaną wiadomości e-mail.

6.  Kliknij pozycję **Zapisz**. Określonych odbiorców teraz odbierać powiadomienia e-mail, jeśli wystąpią błędy.

## <a name="create-a-log-analytics-view-for-monitoring"></a>Utwórz widoki usługi Log Analytics do monitorowania

Spowoduje to utworzenie wizualnego monitorowania wszystkich grup synchronizacji określony widok usługi Log Analytics. Widok zawiera kilka składników:

-   Kafelek przeglądu, który pokazuje, jak wiele błędów, sukcesów i ostrzeżenia mają wszystkie grupy synchronizacji.

-   Kafelek wszystkie grupy synchronizacji, która pokazuje liczbę błędów i ostrzeżeń dla każdej grupy synchronizacji. Grupy bez problemów, nie pojawiają się na tym kafelku.

-   Kafelek dla każdej grupy synchronizacji, która pokazuje liczbę błędów, sukcesów i ostrzeżenia i ostatnie komunikaty o błędach.

Aby skonfigurować widok usługi Log Analytics, wykonaj następujące czynności:

1.  Na stronie głównej usługi Log Analytics wybierz znak plus po lewej stronie, aby otworzyć **Projektant widoków**.

2.  Wybierz **importu** na górnym pasku Projektant widoków. Następnie wybierz przykładowy plik "DataSyncLogOMSView".

3.  Przykładowy widok jest do zarządzania dwóch grup synchronizacji. Edytuj ten widok, odpowiednio do scenariusza. Kliknij przycisk **Edytuj** i wprowadź następujące zmiany:

    1.  Tworzenie nowych obiektów "Wykres pierścieniowy i lista" z galerii, zgodnie z potrzebami.

    2.  W każdym kafelków należy zaktualizować zapytania z informacjami o.

        1.  Na każdym kafelku zmienić interwał TimeStamp_t zgodnie z potrzebami.

        2.  Na kafelkach dla każdej grupy synchronizacji należy zaktualizować nazwy grupy synchronizacji.

    3.  Na każdym kafelku zaktualizować tytuł, zgodnie z potrzebami.

4.  Kliknij przycisk **Zapisz** i widok jest gotowy.

## <a name="cost-of-this-solution"></a>Koszt tego rozwiązania

W większości przypadków to rozwiązanie jest bezpłatna.

**Usługa Azure Automation:** może być koszt przy użyciu konta usługi Azure Automation, w zależności od użycia. Pierwszych 500 minut zadań miesięcznie jest bezpłatnych. W większości przypadków to rozwiązanie powinien używać mniej niż 500 minut na miesiąc. Aby uniknąć opłat, Zaplanuj uruchomienie elementu runbook w odstępie dwie lub więcej godzin. Aby uzyskać więcej informacji, zobacz [cennik usługi Automation](https://azure.microsoft.com/pricing/details/automation/).

**Usługa log Analytics:** może być koszt związany z usługą Log Analytics w zależności od użycia. Warstwy bezpłatna jest zapewniany 500 MB danych pozyskiwanych danych dziennie. W większości przypadków to rozwiązanie powinien pozyskiwania mniejszy niż 500 MB dziennie. Aby zmniejszyć użycie, należy użyć tylko do awarii filtrowania uwzględnione w elementu runbook. Jeśli używasz więcej niż 500 MB dziennie uaktualnienie do warstwy płatnej w celu uniknięcia ryzyka analytics po osiągnięciu ograniczenia. Aby uzyskać więcej informacji, zobacz [cen usługi Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Przykłady kodu

Pobierz przykłady kodu, opisane w tym artykule w następujących lokalizacjach:

-   [Dane synchronizacji dziennika elementu Runbook z programu PowerShell](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Widoki usługi Log Analytics Data Sync](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

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
