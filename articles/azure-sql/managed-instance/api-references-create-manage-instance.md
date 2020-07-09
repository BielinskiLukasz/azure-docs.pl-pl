---
title: Dokumentacja interfejsu API zarządzania dla wystąpienia zarządzanego usługi Azure SQL
description: Dowiedz się więcej na temat tworzenia i konfigurowania wystąpień zarządzanych wystąpienia zarządzanego Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 3f0eb2c1ecb26475b25cd5ca41c9c61d5e2b47a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84695610"
---
# <a name="managed-api-reference-for-azure-sql-managed-instance"></a>Dokumentacja zarządzanego interfejsu API dla wystąpienia zarządzanego usługi Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Zarządzane wystąpienia wystąpienia zarządzanego usługi Azure SQL można tworzyć i konfigurować przy użyciu Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsów API REST i języka Transact-SQL. Ten artykuł zawiera omówienie funkcji i interfejsu API, za pomocą których można tworzyć i konfigurować wystąpienia zarządzane.

## <a name="azure-portal-create-a-managed-instance"></a>Azure Portal: Tworzenie wystąpienia zarządzanego

Aby zapoznać się z przewodnikiem Szybki Start pokazujący, jak utworzyć wystąpienie zarządzane, zobacz [Szybki Start: Tworzenie wystąpienia zarządzanego](instance-create-quickstart.md).

## <a name="powershell-create-and-configure-managed-instances"></a>PowerShell: Tworzenie i Konfigurowanie wystąpień zarządzanych

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRM są zasadniczo identyczne.

Aby tworzyć wystąpienia zarządzane i zarządzać nimi za pomocą Azure PowerShell, użyj następujących poleceń cmdlet programu PowerShell. Jeśli musisz zainstalować lub uaktualnić program PowerShell, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Przykładowe skrypty programu PowerShell można znaleźć w temacie [skrypt szybkiego startu: Tworzenie wystąpienia zarządzanego przy użyciu biblioteki programu PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Polecenie cmdlet | Opis |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Tworzy wystąpienie zarządzane. |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Zwraca informacje dotyczące wystąpienia zarządzanego.|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Ustawia właściwości wystąpienia zarządzanego.|
|[Remove-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Usuwa wystąpienie zarządzane.|
|[New-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Tworzy bazę danych wystąpienia zarządzanego SQL.|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Zwraca informacje o bazie danych wystąpienia zarządzanego SQL.|
|[Remove-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Usuwa bazę danych wystąpienia zarządzanego SQL.|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Przywraca bazę danych wystąpienia zarządzanego SQL.|

## <a name="azure-cli-create-and-configure-managed-instances"></a>Interfejs wiersza polecenia platformy Azure: Tworzenie i Konfigurowanie wystąpień zarządzanych

Aby utworzyć i skonfigurować wystąpienia zarządzane za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure), użyj następujących [poleceń interfejsu wiersza polecenia platformy Azure dla wystąpienia zarządzanego SQL](/cli/azure/sql/mi). Użyj [Azure Cloud Shell](/azure/cloud-shell/overview) , aby uruchomić interfejs wiersza polecenia w przeglądarce, lub [Zainstaluj](/cli/azure/install-azure-cli) go w systemie MacOS, Linux lub Windows.

> [!TIP]
> Przewodnik Szybki Start dotyczący interfejsu wiersza polecenia platformy Azure zawiera temat [Praca z wystąpieniem zarządzanym SQL przy użyciu interfejsu wiersza polecenia platformy Azure](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)

| Polecenie cmdlet | Opis |
| --- | --- |
|[AZ SQL mi Create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Tworzy wystąpienie zarządzane.|
|[AZ SQL mi list](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Wyświetla dostępne wystąpienia zarządzane.|
|[AZ SQL mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Pobiera szczegóły wystąpienia zarządzanego.|
|[AZ SQL mi Update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Aktualizuje wystąpienie zarządzane.|
|[AZ SQL mi Delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Usuwa wystąpienie zarządzane.|
|[AZ SQL MidB Create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Tworzy zarządzaną bazę danych.|
|[AZ SQL MidB list](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Wyświetla listę dostępnych zarządzanych baz danych.|
|[AZ SQL MidB Restore](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Przywraca zarządzaną bazę danych.|
|[AZ SQL MidB Delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Usuwa zarządzaną bazę danych.|

## <a name="transact-sql-create-and-configure-instance-databases"></a>Transact-SQL: Tworzenie i Konfigurowanie baz danych wystąpień

Aby utworzyć i skonfigurować wystąpienia baz danych po utworzeniu wystąpienia zarządzanego, należy użyć następujących poleceń języka T-SQL. Te polecenia można wydać przy użyciu Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is), [Visual Studio Code](https://code.visualstudio.com/docs)lub dowolnego innego programu, który może nawiązać połączenie z serwerem i przekazać polecenia języka Transact-SQL.

> [!TIP]
> Przewodniki Szybki Start pokazujące, jak skonfigurować i połączyć się z wystąpieniem zarządzanym przy użyciu SQL Server Management Studio w systemie Microsoft Windows, zobacz [Szybki Start: Konfigurowanie maszyny wirtualnej platformy Azure do nawiązywania połączenia z wystąpieniem zarządzanym Azure SQL](connect-vm-instance-configure.md) i [Szybki Start: Konfigurowanie połączenia punkt-lokacja z wystąpieniem zarządzanym Azure SQL z lokalnego](point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Nie można utworzyć lub usunąć wystąpienia zarządzanego przy użyciu języka Transact-SQL.

| Polecenie | Opis |
| --- | --- |
|[UTWÓRZ BAZĘ DANYCH](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Tworzy nową bazę danych wystąpienia w wystąpieniu zarządzanym SQL. Aby utworzyć nową bazę danych, musisz mieć połączenie z bazą danych Master.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Modyfikuje bazę danych wystąpienia w wystąpieniu zarządzanym SQL.|

## <a name="rest-api-create-and-configure-managed-instances"></a>Interfejs API REST: Tworzenie i Konfigurowanie wystąpień zarządzanych

Aby utworzyć i skonfigurować wystąpienia zarządzane, Użyj tych żądań interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Wystąpienia zarządzane — Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Tworzy lub aktualizuje wystąpienie zarządzane.|
|[Wystąpienia zarządzane — usuwanie](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Usuwa wystąpienie zarządzane.|
|[Wystąpienia zarządzane — Pobierz](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Pobiera wystąpienie zarządzane.|
|[Wystąpienia zarządzane — lista](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Zwraca listę wystąpień zarządzanych w ramach subskrypcji.|
|[Wystąpienia zarządzane — lista według grupy zasobów](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Zwraca listę wystąpień zarządzanych w grupie zasobów.|
|[Wystąpienia zarządzane — aktualizacja](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Aktualizuje wystąpienie zarządzane.|

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat migrowania bazy danych SQL Server na platformę Azure, zobacz [Migrowanie do Azure SQL Database](../database/migrate-to-database-from-sql-server.md).
- Informacje dotyczące obsługiwanych funkcji można znaleźć w temacie [Funkcje](../database/features-comparison.md).
