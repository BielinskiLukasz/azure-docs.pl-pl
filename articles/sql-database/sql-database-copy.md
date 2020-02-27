---
title: Kopiowanie bazy danych
description: Utwórz transakcyjnie spójną kopię istniejącej bazy danych Azure SQL na tym samym serwerze lub na innym serwerze.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 02/24/2020
ms.openlocfilehash: f27042679280581dc3a03113d75c5fb787bbf711
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616005"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Kopiowanie spójnej transakcyjnie kopii bazy danych Azure SQL Database

Azure SQL Database oferuje kilka metod tworzenia spójnej i niefunkcjonalnej kopii istniejącej bazy danych Azure SQL Database ([pojedyncza baza danych](sql-database-single-database.md)) na tym samym serwerze lub na innym serwerze. Bazę danych SQL można skopiować za pomocą Azure Portal, PowerShell lub T-SQL.

## <a name="overview"></a>Omówienie

Kopia bazy danych jest migawką źródłowej bazy danych w czasie żądania kopiowania. Możesz wybrać ten sam serwer lub inny serwer. Można również wybrać opcję utrzymania warstwy usługi i rozmiaru obliczeń lub użyć innego rozmiaru obliczeniowego w ramach tej samej warstwy usług (Edition). Po zakończeniu kopiowania zostanie ona w pełni funkcjonalna, niezależna baza danych. W tym momencie można go uaktualnić lub zmienić na starszą wersję. Logowania, użytkownicy i uprawnienia mogą być zarządzane niezależnie. Kopia jest tworzona przy użyciu technologii replikacji geograficznej, a po zakończeniu umieszczania zostanie wykonane automatyczne działanie łącza replikacji geograficznej. Wszystkie wymagania dotyczące korzystania z replikacji geograficznej dotyczą operacji kopiowania bazy danych. Szczegółowe informacje znajdują się w temacie [Omówienie aktywnej replikacji geograficznej](sql-database-active-geo-replication.md) .

> [!NOTE]
> [Automatyczne kopie zapasowe bazy danych](sql-database-automated-backups.md) są używane podczas tworzenia kopii bazy danych.

## <a name="logins-in-the-database-copy"></a>Nazwy logowania w kopii bazy danych

Podczas kopiowania bazy danych na ten sam serwer SQL Database można używać tych samych identyfikatorów logowania w obu bazach danych. Podmiot zabezpieczeń, który jest używany do kopiowania bazy danych, zostaje właścicielem bazy danych w nowej bazie danych. 

Podczas kopiowania bazy danych na inny serwer SQL Database, podmiot zabezpieczeń, który zainicjował operację kopiowania na serwerze docelowym, zostaje właścicielem nowej bazy danych. 

Bez względu na to, że serwer docelowy, wszyscy użytkownicy bazy danych, ich uprawnienia i identyfikatory zabezpieczeń (SID) są kopiowane do kopii bazy danych. Korzystanie z [użytkowników zawartej bazy danych](sql-database-manage-logins.md) na potrzeby dostępu do danych gwarantuje, że skopiowana baza danych ma te same poświadczenia użytkownika, więc po zakończeniu kopiowania można natychmiast uzyskać do niego dostęp przy użyciu tych samych poświadczeń.

W przypadku korzystania z nazw logowania na poziomie serwera na potrzeby dostępu do danych i kopiowania bazy danych na inny serwer, dostęp oparty na logowaniu może nie zadziałał. Może się tak zdarzyć, ponieważ nazwy logowania nie istnieją na serwerze docelowym lub ponieważ ich hasła i identyfikatory zabezpieczeń (SID) są różne. Aby dowiedzieć się więcej o zarządzaniu nazwami logowania podczas kopiowania bazy danych na inny serwer SQL Database, zobacz [jak zarządzać zabezpieczeniami usługi Azure SQL Database po awarii](sql-database-geo-replication-security-config.md). Po pomyślnym przeprowadzeniu operacji kopiowania na inny serwer, a przed ponownym zamapowaniem innych użytkowników, tylko nazwa logowania skojarzona z właścicielem bazy danych lub administrator serwera może zalogować się do skopiowanej bazy danych. Aby rozwiązać nazwy logowania i ustanowić dostęp do danych po zakończeniu operacji kopiowania, zobacz temat [Rozwiązywanie logowań](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Kopiowanie bazy danych przy użyciu Azure Portal

Aby skopiować bazę danych przy użyciu Azure Portal, Otwórz stronę dla bazy danych, a następnie kliknij przycisk **Kopiuj**.

   ![Kopia bazy danych](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell-or-azure-cli"></a>Kopiowanie bazy danych przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure

Aby skopiować bazę danych, należy użyć następujących przykładów.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

W przypadku programu PowerShell należy użyć polecenia cmdlet [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) .

> [!IMPORTANT]
> Moduł programu PowerShell Azure Resource Manager (RM) jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. Moduł AzureRM będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020.  Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji o zgodności, zobacz [wprowadzenie do nowego Azure PowerShell AZ module](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

Kopia bazy danych jest operacją asynchroniczną, ale docelowa baza danych jest tworzona natychmiast po zaakceptowaniu żądania. Jeśli chcesz anulować operację kopiowania nadal w toku, Porzuć docelową bazę danych za pomocą polecenia cmdlet [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) .

Aby zapoznać się z kompletnym przykładowym skryptem programu PowerShell, zobacz [Kopiowanie bazy danych na nowy serwer](scripts/sql-database-copy-database-to-new-server-powershell.md).

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azure-cli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

Kopia bazy danych jest operacją asynchroniczną, ale docelowa baza danych jest tworzona natychmiast po zaakceptowaniu żądania. Jeśli chcesz anulować operację kopiowania nadal w toku, Porzuć docelową bazę danych za pomocą polecenia [AZ SQL DB Delete](/cli/azure/sql/db#az-sql-db-delete) .

* * *

## <a name="rbac-roles-to-manage-database-copy"></a>Role RBAC do zarządzania kopią bazy danych

Aby utworzyć kopię bazy danych, musisz mieć następujące role:

- Właściciel subskrypcji lub
- SQL Server rolę współautor lub
- Rola niestandardowa w źródłowej i docelowej bazie danych z następującymi uprawnieniami:

   Microsoft. SQL/serwery/bazy danych/Odczyt Microsoft. SQL/serwery/bazy danych/zapis

Aby anulować kopię bazy danych, musisz mieć następujące role:

- Właściciel subskrypcji lub
- SQL Server rolę współautor lub
- Rola niestandardowa w źródłowej i docelowej bazie danych z następującymi uprawnieniami:

   Microsoft. SQL/serwery/bazy danych/Odczyt Microsoft. SQL/serwery/bazy danych/zapis

Aby zarządzać kopią bazy danych przy użyciu Azure Portal, potrzebne są również następujące uprawnienia:

   Microsoft. resources/subscriptions/zasobów/Odczytaj Microsoft. resources/subscriptions/Resources/Write Microsoft. resources/Deployments/Odczytaj Microsoft. resources/Deployments/Write Microsoft. resources/Deployments

Jeśli chcesz zobaczyć operacje w obszarze wdrożenia w grupie zasobów portalu, operacje między wieloma dostawcami zasobów, w tym operacje SQL, będą potrzebne następujące dodatkowe role RBAC:

   Microsoft. resources/subscriptions/ResourceGroups/Deployments/Operations/Read Microsoft. resources/subscriptions/ResourceGroups/Deployments/operationstatuses/Read

## <a name="copy-a-database-by-using-transact-sql"></a>Kopiowanie bazy danych przy użyciu języka Transact-SQL

Zaloguj się do bazy danych Master przy użyciu identyfikatora logowania administratora serwera lub nazwy logowania, która utworzyła bazę danych, którą chcesz skopiować. Aby Kopiowanie bazy danych zakończyło się pomyślnie, nazwy logowania, które nie są administratora serwera, muszą należeć do roli `dbmanager`. Aby uzyskać więcej informacji na temat nazw logowania i łączenia się z serwerem, zobacz Zarządzanie nazwami [logowania](sql-database-manage-logins.md).

Rozpocznij kopiowanie źródłowej bazy danych za pomocą programu [CREATE DATABASE... JAKO kopia](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current#copy-a-database) instrukcji. Instrukcja T-SQL kontynuuje działanie do momentu ukończenia operacji kopiowania bazy danych.

> [!NOTE]
> Zakończenie instrukcji języka T-SQL nie kończy operacji kopiowania bazy danych. Aby zakończyć operację, Porzuć docelową bazę danych.
>

### <a name="copy-a-sql-database-to-the-same-server"></a>Kopiowanie bazy danych SQL na ten sam serwer

Zaloguj się do bazy danych Master przy użyciu identyfikatora logowania administratora serwera lub nazwy logowania, która utworzyła bazę danych, którą chcesz skopiować. Aby Kopiowanie bazy danych zakończyło się pomyślnie, nazwy logowania, które nie są administratora serwera, muszą należeć do roli `dbmanager`.

To polecenie kopiuje database1 do nowej bazy danych o nazwie Database2 na tym samym serwerze. W zależności od rozmiaru bazy danych operacja kopiowania może zająć trochę czasu.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-a-sql-database-to-a-different-server"></a>Kopiowanie bazy danych SQL na inny serwer

Zaloguj się do bazy danych Master serwera docelowego, na którym ma zostać utworzona nowa baza danych. Użyj nazwy logowania, która ma taką samą nazwę i hasło, jak właścicielem bazy danych źródłowej bazy danych na serwerze źródłowym. Identyfikator logowania na serwerze docelowym musi być również członkiem roli `dbmanager` lub być identyfikatorem logowania administratora serwera.

To polecenie kopiuje database1 na serwer1 do nowej bazy danych o nazwie Database2 na serwerze serwer2. W zależności od rozmiaru bazy danych operacja kopiowania może zająć trochę czasu.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Zapory obu serwerów muszą być skonfigurowane tak, aby zezwalały na połączenia przychodzące z adresu IP klienta wystawiającego bazę danych T-SQL... JAKO kopia polecenia.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Kopiowanie bazy danych SQL do innej subskrypcji

Aby skopiować bazę danych na serwer SQL Database w innej subskrypcji przy użyciu języka T-SQL, można wykonać kroki opisane w sekcji [Kopiowanie bazy danych SQL do innego serwera](#copy-a-sql-database-to-a-different-server) . Upewnij się, że używasz nazwy logowania, która ma taką samą nazwę i hasło, jak właścicielem bazy danych źródłowej bazy danych. Ponadto identyfikator logowania musi być członkiem roli `dbmanager` lub administratorem serwera zarówno na serwerze źródłowym, jak i docelowym.

> [!NOTE]
> [Azure Portal](https://portal.azure.com), PowerShell i interfejs wiersza polecenia platformy Azure nie obsługują kopiowania bazy danych do innej subskrypcji.

### <a name="monitor-the-progress-of-the-copying-operation"></a>Monitoruj postęp operacji kopiowania

Monitoruj proces kopiowania, wykonując zapytania dotyczące widoków [sys. baz danych](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql), [sys. dm_database_copies](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database.md)i [sys. dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database.md) . Podczas kopiowania jest w toku, kolumna **state_desc** widoku sys. databases dla nowej bazy danych jest ustawiona do **kopiowania**.

* Jeśli kopiowanie nie powiedzie się, kolumna **state_desc** widoku sys. databases dla nowej bazy danych jest ustawiona na **podejrzane**. Wykonaj instrukcję DROP w nowej bazie danych i spróbuj ponownie później.
* Jeśli kopiowanie powiedzie się, kolumna **state_desc** widoku sys. databases dla nowej bazy danych jest ustawiona na **online**. Kopiowanie zostało ukończone, a nowa baza danych jest zwykłą bazą danych, która może zostać zmieniona niezależnie od źródłowej bazy danych.

> [!NOTE]
> Jeśli zdecydujesz się anulować kopiowanie w trakcie wykonywania, wykonaj instrukcję [Drop Database](https://docs.microsoft.com/sql/t-sql/statements/drop-database-transact-sql) w nowej bazie danych.

> [!IMPORTANT]
> Jeśli trzeba utworzyć kopię z znacznie mniejszym celem usługi niż źródło, docelowa baza danych może nie mieć wystarczających zasobów do ukończenia procesu tworzenia i może spowodować niepowodzenie operacji kopiowania. W tym scenariuszu Użyj żądania przywracania geograficznego, aby utworzyć kopię na innym serwerze i/lub innym regionie. Więcej informacje można znaleźć w temacie [odzyskiwanie bazy danych Azure SQL Database przy użyciu kopii zapasowych bazy danych](sql-database-recovery-using-backups.md#geo-restore) .

## <a name="resolve-logins"></a>Rozwiązywanie logowań

Gdy nowa baza danych jest w trybie online na serwerze docelowym, użyj instrukcji [ALTER USER](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current) , aby ponownie zamapować użytkowników z nowej bazy danych na nazwy logowania na serwerze docelowym. Aby rozwiązać użytkowników oddzielonych, zobacz [Rozwiązywanie problemów z użytkownikami](https://docs.microsoft.com/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server)oddzielonymi. Zobacz również [jak zarządzać zabezpieczeniami usługi Azure SQL Database po odzyskiwaniu po awarii](sql-database-geo-replication-security-config.md).

Wszyscy użytkownicy w nowej bazie danych zachowują uprawnienia, które miały w źródłowej bazie danych. Użytkownik, który zainicjował kopię bazy danych, zostaje właścicielem bazy danych nowej bazy danych. Po pomyślnym zakończeniu kopiowania i wcześniejszym zamapowaniu użytkowników tylko właściciel bazy danych może zalogować się do nowej bazy danych.

Aby dowiedzieć się więcej o zarządzaniu użytkownikami i logowaniami podczas kopiowania bazy danych na inny serwer SQL Database, zobacz [jak zarządzać zabezpieczeniami usługi Azure SQL Database po awarii](sql-database-geo-replication-security-config.md).

## <a name="database-copy-errors"></a>Błędy kopiowania bazy danych

Podczas kopiowania bazy danych w Azure SQL Database można napotkać następujące błędy. Więcej informacji znajdziesz w artykule [Kopiowanie bazy danych usługi Azure SQL Database](sql-database-copy.md).

| Kod błędu | Ważność | Opis |
| ---:| ---:|:--- |
| 40635 |16 |Klient o adresie IP '%.&#x2a;ls' jest tymczasowo wyłączona. |
| 40637 |16 |Tworzenie kopii bazy danych jest obecnie wyłączone. |
| 40561 |16 |Kopiowanie bazy danych nie powiodło się. Źródłowa lub docelowa baza danych nie istnieje. |
| 40562 |16 |Kopiowanie bazy danych nie powiodło się. Źródłowa baza danych została porzucona. |
| 40563 |16 |Kopiowanie bazy danych nie powiodło się. Docelowa baza danych została porzucona. |
| 40564 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40565 |16 |Kopiowanie bazy danych nie powiodło się. Dozwolona jest nie więcej niż 1 współbieżna kopia bazy danych z tego samego źródła. Porzuć docelową bazę danych i spróbuj ponownie później. |
| 40566 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40567 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40568 |16 |Kopiowanie bazy danych nie powiodło się. Źródłowa baza danych stanie się niedostępna. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40569 |16 |Kopiowanie bazy danych nie powiodło się. Docelowa baza danych stanie się niedostępna. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40570 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie później. |
| 40571 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie później. |

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat logowań, zobacz Zarządzanie nazwami [logowania](sql-database-manage-logins.md) i [Zarządzanie zabezpieczeniami usługi Azure SQL Database po awarii](sql-database-geo-replication-security-config.md).
- Aby wyeksportować bazę danych, zobacz [Eksportowanie bazy danych do BACPAC](sql-database-export.md).
