---
title: Serwery
description: Dowiedz się więcej na temat serwerów Azure SQL Database i zarządzania nimi.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 7557ed43d9ecb8fc7a584e7e8239bc7ccb972e99
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647173"
---
# <a name="azure-sql-database-servers-and-their-management"></a>Serwery Azure SQL Database i ich zarządzanie

## <a name="what-is-an-azure-sql-database-server"></a>Co to jest serwer Azure SQL Database

Serwer SQL Database to logiczna konstrukcja, która działa jako centralny punkt administracyjny dla wielu pojedynczych lub w [puli](sql-database-elastic-pool.md) baz danych, [logowań](sql-database-manage-logins.md), [reguł zapory](sql-database-firewall-configure.md), [reguł inspekcji](sql-database-auditing.md), [zasad wykrywania zagrożeń](sql-database-threat-detection.md)i [grup trybu failover](sql-database-auto-failover-group.md) serwer programu SQL Database może znajdować się w innym regionie niż jego grupa zasobów. Aby można było utworzyć bazę danych Azure SQL, musi istnieć serwer SQL Database. Wszystkie bazy danych zarządzane przez serwer SQL Database są tworzone w tym samym regionie, w którym znajduje się serwer SQL Database.

Serwer SQL Database różni się od wystąpienia SQL Server, które może być znane w lokalnym świecie. W związku z tym usługa SQL Database nie udziela żadnych gwarancji dotyczących lokalizacji baz danych w odniesieniu do serwera SQL Database, który zarządza nimi, i nie ujawnia dostępu na poziomie wystąpienia ani funkcji. W przeciwieństwie do bazy danych wystąpień w wystąpieniu zarządzanym wszystkie te obiekty są w taki sam sposób, jak w przypadku SQL Server na świecie lokalnym.

Podczas tworzenia serwera SQL Database należy podać konto logowania do serwera i hasło z uprawnieniami administracyjnymi do bazy danych Master na tym serwerze i wszystkich baz danych utworzonych na tym serwerze. To początkowe konto jest kontem logowania SQL. Azure SQL Database obsługuje uwierzytelnianie SQL i Azure Active Directory uwierzytelniania na potrzeby uwierzytelniania. Informacje o logowaniach i uwierzytelnianiu znajdują się [w temacie Zarządzanie bazami danych i nazwami logowania w Azure SQL Database](sql-database-manage-logins.md). Uwierzytelnianie systemu Windows nie jest obsługiwane.

Serwer SQL Database:

- Jest tworzony w ramach subskrypcji platformy Azure, ale można go przenieść wraz z zawartymi w nim zasobami do innej subskrypcji
- Jest zasobem nadrzędnym dla baz danych, pul elastycznych i magazynów danych
- Zapewnia przestrzeń nazw dla baz danych, pul elastycznych i magazynów danych
- Jest kontenerem logicznym z semantyką silnego okresu istnienia — usunięcie serwera i usunięcie zawartych w nim baz danych, pul elastycznych i magazynów danych
- Uczestnictwo w [usłudze kontroli dostępu opartej na rolach (RBAC) na platformie Azure](/azure/role-based-access-control/overview) — bazy danych, pule elastyczne i magazyny danych na serwerze dziedziczą prawa dostępu z serwera
- To wysoce uporządkowany element tożsamości baz danych, pul elastycznych i magazynów danych na potrzeby zarządzania zasobami platformy Azure (patrz schemat adresu URL dla baz danych i pul)
- Rozmieszcza zasoby w regionie
- Udostępnia punkt końcowy połączenia dla dostępu do baz danych (`<serverName>`.database.windows.net)
- Zapewnia dostęp do metadanych dotyczących zawartych zasobów przy użyciu dynamicznych widoków zarządzania, łącząc się z główną bazą danych
- Zapewnia zakres zasad zarządzania, które mają zastosowanie do swoich baz danych — logowania, zapory, inspekcji, wykrywania zagrożeń i takich
- Jest ograniczony przez przydział w ramach subskrypcji nadrzędnej (domyślnie sześć serwerów na subskrypcję — [Zobacz limity dotyczące subskrypcji tutaj](../azure-resource-manager/management/azure-subscription-service-limits.md))
- Zapewnia zakres przydziału bazy danych i limit przydziału jednostek DTU lub rdzeń wirtualny dla zasobów, które zawiera (na przykład 45 000 jednostek DTU)
- Jest zakresem przechowywania wersji dla funkcji włączonych w zawartych zasobach
- Logowania główne na poziomie serwera mogą zarządzać wszystkimi bazami danych na serwerze
- Może zawierać logowania podobne do tych w lokalnych wystąpieniach programu SQL Server, którym udzielono dostępu do co najmniej jednej bazy danych na serwerze, i może otrzymać ograniczone prawa administracyjne. Aby uzyskać więcej informacji, zobacz temat [Logowania](sql-database-manage-logins.md).
- Domyślne sortowanie dla wszystkich baz danych utworzonych na serwerze SQL Database jest `SQL_LATIN1_GENERAL_CP1_CI_AS`, gdzie `LATIN1_GENERAL` jest angielski (Stany Zjednoczone), `CP1` jest stroną kodową 1252, `CI` nie jest rozróżniana wielkość liter, a `AS` jest uwzględniana w akcentie.

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-portal"></a>Zarządzanie serwerami SQL, bazami danych i zaporami platformy Azure przy użyciu Azure Portal

Grupę zasobów usługi Azure SQL Database można utworzyć przed czasem lub podczas tworzenia samego serwera. Istnieje wiele metod uzyskiwania informacji o nowym formularzu programu SQL Server, tworząc nowy serwer SQL lub w ramach tworzenia nowej bazy danych.

### <a name="create-a-blank-sql-database-server"></a>Utwórz pusty serwer SQL Database

Aby utworzyć serwer Azure SQL Database (bez bazy danych) przy użyciu [Azure Portal](https://portal.azure.com), przejdź do pustego formularza programu SQL Server (serwera logicznego).  

### <a name="create-a-blank-or-sample-sql-database"></a>Tworzenie pustej lub przykładowej bazy danych SQL

Aby utworzyć bazę danych Azure SQL przy użyciu [Azure Portal](https://portal.azure.com), przejdź do pustego formularza SQL Database i podaj żądane informacje. Grupę zasobów i serwer SQL Database usługi Azure SQL Database można utworzyć przed czasem lub podczas tworzenia samej bazy danych. Możesz utworzyć pustą bazę danych lub utworzyć przykładową bazę danych na podstawie firmy Adventure Works LT.

  ![tworzenie bazy danych 1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Aby uzyskać informacje na temat wybierania warstwy cenowej dla bazy danych, zobacz [model zakupów oparty na](sql-database-service-tiers-dtu.md) jednostkach DTU i [model zakupu oparty na rdzeń wirtualny](sql-database-service-tiers-vcore.md).

Aby utworzyć wystąpienie zarządzane, zobacz [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md)

### <a name="manage-an-existing-sql-server"></a>Zarządzanie istniejącym programem SQL Server

Aby zarządzać istniejącym serwerem, przejdź do serwera przy użyciu szeregu metod — na przykład z określonej strony bazy danych SQL, strony **serwery SQL** lub strony **wszystkie zasoby** .

Aby zarządzać istniejącą bazą danych, przejdź do strony **bazy danych SQL** , a następnie kliknij bazę danych, którą chcesz zarządzać. Poniższy zrzut ekranu pokazuje, jak rozpocząć ustawianie zapory na poziomie serwera dla bazy danych na stronie **Przegląd** dla bazy danych.

   ![reguła zapory serwera](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> Aby skonfigurować właściwości wydajności dla bazy danych, zobacz [model zakupów oparty na](sql-database-service-tiers-dtu.md) jednostkach DTU i [model zakupu oparty na rdzeń wirtualny](sql-database-service-tiers-vcore.md).
> [!TIP]
> Aby uzyskać Azure Portal przewodniku Szybki Start, zobacz [Tworzenie bazy danych Azure SQL Database w Azure Portal](sql-database-single-database-get-started.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-powershell"></a>Zarządzanie serwerami usługi Azure SQL, bazami danych i zaporami przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

Aby utworzyć i zarządzać usługą Azure SQL Server, bazami danych i zaporami przy użyciu Azure PowerShell, należy użyć następujących poleceń cmdlet programu PowerShell. Jeśli musisz zainstalować lub uaktualnić program PowerShell, zobacz [install Azure PowerShell module](/powershell/azure/install-az-ps). Aby utworzyć pule elastyczne i zarządzać nimi, zobacz [Pule elastyczne](sql-database-elastic-pool.md).

| Polecenie cmdlet | Opis |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Tworzy bazę danych |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Pobiera co najmniej jedną bazę danych|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Ustawia właściwości bazy danych lub przenosi istniejącą bazę danych do puli elastycznej|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Usuwa bazę danych|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Tworzy grupę zasobów|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Tworzy serwer|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Zwraca informacje o serwerach|
|[Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver)|Modyfikuje właściwości serwera|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Usuwa serwer|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Tworzy regułę zapory na poziomie serwera |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Pobiera reguły zapory dla serwera|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Modyfikuje regułę zapory na serwerze|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Usuwa regułę zapory z serwera.|
| New-AzSqlServerVirtualNetworkRule | Tworzy [*regułę sieci wirtualnej*](sql-database-vnet-service-endpoint-rule-overview.md)na podstawie podsieci, która jest punktem końcowym usługi Virtual Network. |

> [!TIP]
> Przewodnik Szybki Start dla programu PowerShell zawiera temat [Tworzenie pojedynczej bazy danych usługi Azure SQL przy użyciu programu PowerShell](sql-database-single-database-get-started.md). Przykładowe skrypty programu PowerShell można znaleźć [w temacie Używanie programu PowerShell do tworzenia pojedynczej bazy danych usługi Azure SQL i konfigurowania reguły zapory](scripts/sql-database-create-and-configure-database-powershell.md) oraz [monitorowania i skalowania pojedynczej bazy danych Azure SQL Database przy użyciu programu PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-cli"></a>Zarządzanie serwerami SQL, bazami danych i zaporami platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Aby utworzyć i zarządzać usługą Azure SQL Server, bazami danych i zaporami przy użyciu [interfejsu wiersza polecenia platformy](/cli/azure)Azure, użyj następujących poleceń [SQL Database interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/db) . Używaj usługi [Cloud Shell](/azure/cloud-shell/overview), aby uruchamiać interfejs wiersza polecenia w przeglądarce, albo [zainstaluj](/cli/azure/install-azure-cli) go w systemie macOS, Linux lub Windows. Aby utworzyć pule elastyczne i zarządzać nimi, zobacz [Pule elastyczne](sql-database-elastic-pool.md).

| Polecenie cmdlet | Opis |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Tworzy bazę danych|
|[Lista AZ SQL DB](/cli/azure/sql/db#az-sql-db-list)|Wyświetla listę wszystkich baz danych i magazynów danych na serwerze lub wszystkich baz danych w puli elastycznej|
|[AZ SQL DB list-Edition](/cli/azure/sql/db#az-sql-db-list-editions)|Wyświetla dostępne cele usługi i limity magazynu|
|[AZ SQL DB list-Usages](/cli/azure/sql/db#az-sql-db-list-usages)|Zwraca użycie bazy danych|
|[AZ SQL DB show](/cli/azure/sql/db#az-sql-db-show)|Pobiera bazę danych lub magazyn danych|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Aktualizuje bazę danych|
|[Usuń AZ SQL DB](/cli/azure/sql/db#az-sql-db-delete)|Usuwa bazę danych|
|[az group create](/cli/azure/group#az-group-create)|Tworzy grupę zasobów|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Tworzy serwer|
|[AZ SQL Server list](/cli/azure/sql/server#az-sql-server-list)|Wyświetla listę serwerów|
|[AZ SQL Server list-Usages](/cli/azure/sql/server#az-sql-server-list-usages)|Zwraca użycie serwera|
|[AZ SQL Server show](/cli/azure/sql/server#az-sql-server-show)|Pobiera serwer|
|[AZ SQL Server Update](/cli/azure/sql/server#az-sql-server-update)|Aktualizuje serwer|
|[AZ SQL Server Delete](/cli/azure/sql/server#az-sql-server-delete)|Usuwa serwer|
|[AZ SQL Server firewall-Rule Create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Tworzy regułę zapory serwera|
|[AZ SQL Server firewall-Rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Wyświetla listę reguł zapory na serwerze|
|[AZ SQL Server firewall-Rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Pokazuje szczegóły reguły zapory|
|[AZ SQL Server firewall-Rule Update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Aktualizuje regułę zapory|
|[AZ SQL Server firewall-Rule Delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Usuwa regułę zapory|

> [!TIP]
> Przewodnik Szybki Start dotyczący interfejsu wiersza polecenia platformy Azure zawiera temat [Tworzenie pojedynczej bazy danych usługi Azure SQL przy użyciu interfejsu wiersza polecenia platformy Azure](sql-database-cli-samples.md). Przykładowe skrypty interfejsu wiersza polecenia platformy Azure można znaleźć [w temacie Używanie interfejsu wiersza polecenia do tworzenia pojedynczej bazy danych usługi Azure SQL i konfigurowania reguły zapory](scripts/sql-database-create-and-configure-database-cli.md) oraz [do monitorowania i skalowania pojedynczej bazy danych SQL Azure przy użyciu interfejsu wiersza polecenia](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-transact-sql"></a>Zarządzanie serwerami, bazami danych i zaporami usługi Azure SQL Database przy użyciu języka Transact-SQL

Aby utworzyć i zarządzać programem Azure SQL Server, bazami danych i zaporami przy użyciu języka Transact-SQL, należy użyć następujących poleceń języka T-SQL. Te polecenia można wydać przy użyciu Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)lub innego programu, który może nawiązać połączenie z serwerem Azure SQL Database i przekazać polecenia języka Transact-SQL. Aby zarządzać pulami elastycznymi, zobacz [Pule elastyczne](sql-database-elastic-pool.md).

> [!IMPORTANT]
> Nie można utworzyć lub usunąć serwera za pomocą języka Transact-SQL.
>

| Polecenie | Opis |
| --- | --- |
|[Utwórz bazę danych (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Tworzy nową bazę danych. Aby utworzyć nową bazę danych, musisz mieć połączenie z bazą danych Master.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Modyfikuje bazę danych SQL Azure. |
|[ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Modyfikuje Azure SQL Data Warehouse.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Usuwa bazę danych.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Zwraca wersję (warstwę usług), cel usługi (warstwę cenową) i nazwę puli elastycznej (jeśli istnieje) dla usługi Azure SQL Database lub Azure SQL Data Warehouse. Jeśli użytkownik jest zalogowany do bazy danych Master na serwerze Azure SQL Database, zwraca informacje o wszystkich bazach danych. W przypadku Azure SQL Data Warehouse należy nawiązać połączenie z bazą danych Master.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Zwraca użycie procesora CPU, operacji we/wy i pamięci dla bazy danych Azure SQL Database. Jeden wiersz istnieje przez co 15 sekund, nawet jeśli w bazie danych nie ma żadnych działań.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Zwraca dane użycia procesora CPU i magazynu dla Azure SQL Database. Dane są zbierane i agregowane w ciągu pięciu minut.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Zawiera dane statystyczne dotyczące zdarzeń łączności z bazą danych SQL Database, które zawierają omówienie sukcesów i niepowodzeń połączeń z bazą danych. |
|[sys. event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Zwraca pomyślne Azure SQL Database połączenia z bazą danych, błędy połączeń i zakleszczenie. Te informacje służą do śledzenia i rozwiązywania problemów z działaniem bazy danych za pomocą SQL Database.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Tworzy lub aktualizuje ustawienia zapory na poziomie serwera dla serwera SQL Database. Ta procedura składowana jest dostępna tylko w bazie danych Master do nazwy logowania podmiotu zabezpieczeń na poziomie serwera. Regułę zapory na poziomie serwera można utworzyć tylko przy użyciu języka Transact-SQL po utworzeniu pierwszej reguły zapory na poziomie serwera przez użytkownika z uprawnieniami na poziomie platformy Azure.|
|[sys. firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Zwraca informacje o ustawieniach zapory na poziomie serwera skojarzonych z Microsoft Azure SQL Database.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Usuwa ustawienia zapory na poziomie serwera z serwera SQL Database. Ta procedura składowana jest dostępna tylko w bazie danych Master do nazwy logowania podmiotu zabezpieczeń na poziomie serwera.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Tworzy lub aktualizuje reguły zapory na poziomie bazy danych dla Azure SQL Database lub SQL Data Warehouse. Reguły zapory bazy danych można skonfigurować dla bazy danych Master oraz dla baz danych użytkowników na SQL Database. Reguły zapory bazy danych są przydatne w przypadku korzystania z użytkowników zawartej bazy danych. |
|[sys. database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Zwraca informacje o ustawieniach zapory na poziomie bazy danych skojarzonych z Microsoft Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Usuwa ustawienie zapory na poziomie bazy danych z Azure SQL Database lub SQL Data Warehouse. |

> [!TIP]
> Aby uzyskać szybki Start przy użyciu SQL Server Management Studio w systemie Microsoft Windows, zobacz [Azure SQL Database: używanie SQL Server Management Studio do nawiązywania połączenia i wykonywania zapytań dotyczących danych](sql-database-connect-query-ssms.md). Aby uzyskać szybki Start przy użyciu Visual Studio Code w macOS, Linux lub Windows, zobacz [Azure SQL Database: używanie Visual Studio Code do nawiązywania połączenia i wykonywania zapytań dotyczących danych](sql-database-connect-query-vscode.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-rest-api"></a>Zarządzanie serwerami, bazami danych i zaporami platformy Azure SQL przy użyciu interfejsu API REST

Aby utworzyć i zarządzać programem Azure SQL Server, bazami danych i zaporami, należy użyć tych żądań interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Serwery — Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Tworzy lub aktualizuje nowy serwer.|
|[Serwery — usuwanie](https://docs.microsoft.com/rest/api/sql/servers/delete)|Usuwa program SQL Server.|
|[Serwery — Pobierz](https://docs.microsoft.com/rest/api/sql/servers/get)|Pobiera serwer.|
|[Serwery — lista](https://docs.microsoft.com/rest/api/sql/servers/list)|Zwraca listę serwerów.|
|[Serwery — lista według grupy zasobów](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|Zwraca listę serwerów w grupie zasobów.|
|[Serwery — aktualizacja](https://docs.microsoft.com/rest/api/sql/servers/update)|Aktualizuje istniejący serwer.|
|[Bazy danych — Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Tworzy nową bazę danych lub aktualizuje istniejącą bazę danych.|
|[Bazy danych — usuwanie](https://docs.microsoft.com/rest/api/sql/databases/delete)|Usuwa bazę danych.|
|[Bazy danych — Pobierz](https://docs.microsoft.com/rest/api/sql/databases/get)|Pobiera bazę danych.|
|[Bazy danych — lista według elastycznej puli](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Zwraca listę baz danych w puli elastycznej.|
|[Bazy danych — lista według serwera](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Zwraca listę baz danych na serwerze.|
|[Bazy danych — aktualizacja](https://docs.microsoft.com/rest/api/sql/databases/update)|Aktualizuje istniejącą bazę danych.|
|[Reguły zapory — Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|Tworzy lub aktualizuje regułę zapory.|
|[Reguły zapory — usuwanie](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|Usuwa regułę zapory.|
|[Reguły zapory — Pobierz](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|Pobiera regułę zapory.|
|[Reguły zapory — lista według serwera](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|Zwraca listę reguł zapory.|

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat migrowania bazy danych SQL Server na platformę Azure, zobacz [Migrowanie do Azure SQL Database](sql-database-single-database-migrate.md).
- Informacje dotyczące obsługiwanych funkcji można znaleźć w temacie [Funkcje](sql-database-features.md).
