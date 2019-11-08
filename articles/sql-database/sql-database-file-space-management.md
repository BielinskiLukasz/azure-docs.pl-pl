---
title: Zarządzanie przestrzenią plików z pojedynczym/pulą baz danych
description: Na tej stronie opisano sposób zarządzania przestrzenią plików z pojedynczymi bazami danych w puli w Azure SQL Database i przedstawiono przykłady kodu służące do określenia, czy należy zmniejszyć jedną lub bazę danych w puli, oraz jak wykonać operację zmniejszania bazy danych.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: a8fe58313bce6e9a21b07aa095672ec35ce572d2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73803053"
---
# <a name="manage-file-space-for-single-and-pooled-databases-in-azure-sql-database"></a>Zarządzanie obszarem plików dla jednej i puli baz danych w Azure SQL Database

W tym artykule opisano różne typy miejsc do magazynowania dla pojedynczych i w puli baz danych w Azure SQL Database oraz czynności, które można wykonać, gdy przestrzeń plików przydzieloną dla baz danych i pul elastycznych musi być jawnie zarządzana.

> [!NOTE]
> Ten artykuł nie dotyczy opcji wdrożenia wystąpienia zarządzanego w Azure SQL Database.

## <a name="overview"></a>Omówienie

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

Dzięki pojedynczym i w puli baz danych w Azure SQL Database istnieją wzorce obciążeń, w których alokacja źródłowych plików danych dla baz danych może być większa niż ilość używanych stron danych. Ten stan może wystąpić, gdy ilość używanego miejsca zwiększy się, a następnie dane zostaną usunięte. Przyczyną jest to, że przydzieloną przestrzeń plików nie jest automatycznie odzyskiwana po usunięciu danych.

Monitorowanie użycia miejsca na pliki i zmniejszania plików danych może być konieczne w następujących scenariuszach:

- Zezwalanie na wzrost ilości danych w elastycznej puli, gdy miejsce na pliki przydzielone dla jej baz danych osiągnie maksymalny rozmiar dla puli.
- Zezwalanie na zmniejszenie maksymalnego rozmiaru pojedynczej bazy danych lub elastycznej puli.
- Zezwalanie na zmianę warstwy usługi lub wydajności dla pojedynczej bazy danych lub elastycznej puli na warstwę obsługującą mniejszy rozmiar maksymalny.

### <a name="monitoring-file-space-usage"></a>Monitorowanie użycia przestrzeni plików

Większość metryk miejsca do magazynowania wyświetlana w Azure Portal i następujące interfejsy API mierzą tylko rozmiar użytych stron danych:

- Interfejsy API metryk opartych na Azure Resource Managerach, w tym PowerShell [Get-Metrics](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetric)
- T-SQL: [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

Jednak poniższe interfejsy API mierzą również rozmiar miejsca przydzieloną dla baz danych i pul elastycznych:

- T-SQL: [sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>Zmniejszanie plików danych

Usługa SQL Database nie zmniejsza automatycznie plików danych w celu odzyskiwania nieużywanych przyznanych miejsc ze względu na potencjalny wpływ na wydajność bazy danych.  Jednak klienci mogą zmniejszać pliki danych za pośrednictwem samoobsługowego wyboru, wykonując kroki opisane w artykule [odzyskiwanie nieużywanych przydzielonego miejsca](#reclaim-unused-allocated-space).

> [!NOTE]
> W przeciwieństwie do plików danych usługa SQL Database automatycznie zmniejsza pliki dziennika, ponieważ ta operacja nie ma wpływu na wydajność bazy danych. 

## <a name="understanding-types-of-storage-space-for-a-database"></a>Informacje o typach miejsca do magazynowania dla bazy danych

Zrozumienie następujących ilości miejsca do magazynowania jest istotne dla zarządzania przestrzenią plików bazy danych.

|Ilość bazy danych|Definicja|Komentarze|
|---|---|---|
|**Używane miejsce na dane**|Ilość miejsca używanego do przechowywania danych bazy danych na stronach 8 KB.|Ogólnie rzecz biorąc, zajęte miejsce zwiększa się (zmniejsza) przy wstawianiu (usuwanie). W niektórych przypadkach używane miejsce nie zmienia się po wstawianiu lub usuwaniu, w zależności od ilości i wzorca danych związanych z operacją i fragmentacją. Na przykład usunięcie jednego wiersza z każdej strony danych nie musi zmniejszać używanej przestrzeni.|
|**Przydzielono miejsce na danych**|Ilość zajętego miejsca na pliki, które jest dostępne do przechowywania danych bazy danych.|Przydzieloną ilość miejsca powiększa się automatycznie, ale nigdy nie maleje po usunięciu. Takie zachowanie gwarantuje, że przyszłe operacje wstawiania są szybsze, ponieważ nie trzeba ponownie sformatować miejsca.|
|**Miejsce na dane przydzieloną, ale nieużywane**|Różnica między ilością przydzieloną przestrzenią danych a używanym miejscem na dane.|Ta ilość reprezentuje maksymalną ilość wolnego miejsca, którą można odzyskiwać, zmniejszając pliki danych bazy danych.|
|**Maksymalny rozmiar danych**|Maksymalna ilość miejsca, która może być używana do przechowywania danych bazy danych.|Ilość przydzieloną przestrzeni danych nie może przekraczać maksymalnego rozmiaru danych.|
||||

Na poniższym diagramie przedstawiono relację między różnymi typami miejsc do magazynowania dla bazy danych.

![typy i relacje przestrzeni dyskowej](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>Wykonaj zapytanie dotyczące pojedynczej bazy danych w celu uzyskania informacji o miejscu do magazynowania

Poniższe zapytania mogą służyć do określenia ilości miejsca do magazynowania dla pojedynczej bazy danych.  

### <a name="database-data-space-used"></a>Używane miejsce na dane bazy danych

Zmodyfikuj następujące zapytanie, aby zwrócić ilość użytego miejsca na dane bazy danych.  Jednostki wyniku zapytania są w MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Przestrzeń danych bazy danych przyalokowana i niewykorzystane miejsce

Użyj następującego zapytania, aby zwrócić ilość przydzieloną miejsce na dane bazy danych i ilość niewykorzystanego miejsca.  Jednostki wyniku zapytania są w MB.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, 
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB 
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```
 
### <a name="database-data-max-size"></a>Maksymalny rozmiar danych bazy danych

Zmodyfikuj następujące zapytanie, aby przywrócić maksymalny rozmiar danych bazy danych.  Jednostki wyniku zapytania są w bajtach.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>Informacje o typach miejsca do magazynowania dla puli elastycznej

Zrozumienie następujących ilości miejsca do magazynowania jest istotne dla zarządzania przestrzenią plików puli elastycznej.

|Ilość elastycznej puli|Definicja|Komentarze|
|---|---|---|
|**Używane miejsce na dane**|Suma obszaru danych używanego przez wszystkie bazy danych w puli elastycznej.||
|**Przydzielono miejsce na danych**|Suma przestrzeni danych przydzielonej przez wszystkie bazy danych w puli elastycznej.||
|**Miejsce na dane przydzieloną, ale nieużywane**|Różnica między ilością przydzielonego miejsca na dane i miejscem na dane używanym przez wszystkie bazy danych w puli elastycznej.|Ta ilość reprezentuje maksymalną ilość miejsca przydzielonego dla puli elastycznej, którą można odzyskiwać, zmniejszając pliki danych bazy danych.|
|**Maksymalny rozmiar danych**|Maksymalna ilość miejsca na dane, która może być używana przez pulę elastyczną dla wszystkich swoich baz danych.|Przestrzeń przydzieloną dla puli elastycznej nie powinna przekraczać maksymalnego rozmiaru puli elastycznej.  Jeśli wystąpi ten warunek, przydzielone miejsce, które nie jest używane, może być odzyskiwane przez zmniejszenie plików danych bazy danych.|
||||

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Zbadaj pulę elastyczną w celu uzyskania informacji o miejscu do magazynowania

Poniższe zapytania mogą służyć do określenia ilości miejsca do magazynowania dla puli elastycznej.  

### <a name="elastic-pool-data-space-used"></a>Używane miejsce na dane puli elastycznej

Zmodyfikuj następujące zapytanie, aby zwrócić ilość zajętego miejsca na dane puli elastycznej.  Jednostki wyniku zapytania są w MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Miejsce na danych w puli elastycznej przydzielono i niewykorzystane miejsce

Zmodyfikuj Poniższy skrypt programu PowerShell, aby zwracał tabelę zawierającą przydzieloną przestrzeń i niewykorzystane miejsce dla każdej bazy danych w puli elastycznej. Tabela porządkuje bazy danych z tych baz danych z największą ilością niewykorzystanego miejsca do najmniejszej ilości niewykorzystanego miejsca.  Jednostki wyniku zapytania są w MB.  

Wyniki zapytania dotyczące określania miejsca przydzieloną dla każdej bazy danych w puli można dodać razem, aby określić łączną ilość miejsca przydzieloną dla puli elastycznej. Przydzieloną przestrzeń puli elastycznej nie powinna przekraczać maksymalnego rozmiaru puli elastycznej.  

Skrypt programu PowerShell wymaga modułu SQL Server PowerShell — zobacz [Pobieranie modułu PowerShell](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module) do instalacji.

```powershell
# Resource group name
$resourceGroupName = "rg1" 
# Server name
$serverName = "ls2" 
# Elastic pool name
$poolName = "ep1"
# User name for server
$userName = "name"
# Password for server
$password = "password"

# Get list of databases in elastic pool
$databasesInPool = Get-AzSqlElasticPoolDatabase `
    -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# For each database in the elastic pool,
# get its space allocated in MB and space allocated unused in MB.
  
foreach ($database in $databasesInPool)
{
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn `
        -Database $database.DatabaseName `
        -Username $userName `
        -Password $password `
        -Query $sqlCommand)
}
# Display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort `
    -Property DatabaseDataSpaceAllocatedUnusedInMB `
    -Descending | Format-Table
```

Poniższy zrzut ekranu przedstawia przykład danych wyjściowych skryptu:

![przydzieloną przestrzeń przydziału w puli elastycznej i nieużywaną przydzieloną przestrzeń](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Maksymalny rozmiar danych puli elastycznej

Zmodyfikuj następujące zapytanie T-SQL, aby przywrócić maksymalny rozmiar danych puli elastycznej.  Jednostki wyniku zapytania są w MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Odzyskaj nieużywane przydzieloną przestrzeń

> [!NOTE]
> To polecenie może mieć wpływ na wydajność bazy danych, gdy jest uruchomiona, a jeśli to możliwe, powinno być uruchamiane w okresach o niskim poziomie użycia.

### <a name="dbcc-shrink"></a>Polecenie DBCC Zmniejsz

Po zidentyfikowaniu baz danych do odzyskiwania niewykorzystanego miejsca przydzielenia zmodyfikuj nazwę bazy danych w poniższym poleceniu, aby zmniejszyć liczbę plików danych dla każdej bazy danych.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

To polecenie może mieć wpływ na wydajność bazy danych, gdy jest uruchomiona, a jeśli to możliwe, powinno być uruchamiane w okresach o niskim poziomie użycia.  

Aby uzyskać więcej informacji na temat tego polecenia, zobacz [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 

### <a name="auto-shrink"></a>Autozmniejszanie

Alternatywnie można włączyć funkcję autozmniejszania dla bazy danych.  Funkcja autozmniejszania zmniejsza złożoność zarządzania plikami i mniej wpływa na wydajność bazy danych niż `SHRINKDATABASE` lub `SHRINKFILE`.  Funkcja autozmniejszania może być szczególnie przydatna do zarządzania elastycznymi pulami z wieloma bazami danych.  Jednak funkcja autozmniejszania może być mniej skuteczna podczas odzyskiwania przestrzeni plików niż `SHRINKDATABASE` i `SHRINKFILE`.
Aby włączyć funkcję autozmniejszania, zmodyfikuj nazwę bazy danych w poniższym poleceniu.


```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON
```

Aby uzyskać więcej informacji na temat tego polecenia, zobacz Opcje [zestawu baz danych](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) . 

### <a name="rebuild-indexes"></a>Ponowne kompilowanie indeksów

Po usunięciu plików danych bazy danych indeksy mogą stać się pofragmentowane i utracić skuteczność optymalizacji wydajności. Jeśli wystąpi spadek wydajności, rozważ odbudowanie indeksów bazy danych. Aby uzyskać więcej informacji na temat fragmentacji i odbudowywania indeksów, zobacz [Reorganizowanie i](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)ponowne kompilowanie indeksów.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o maksymalnych rozmiarach baz danych, zobacz:
  - [Azure SQL Database limity modelu zakupu opartego na rdzeń wirtualny dla pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md)
  - [Limity zasobów dla pojedynczych baz danych korzystających z modelu zakupu opartego na jednostkach DTU](sql-database-dtu-resource-limits-single-databases.md)
  - [Azure SQL Database limity modelu zakupu opartego na rdzeń wirtualny dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md)
  - [Limity zasobów dla pul elastycznych przy użyciu modelu zakupu opartego na jednostkach DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Aby uzyskać więcej informacji na temat polecenia `SHRINKDATABASE`, zobacz [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 
- Aby uzyskać więcej informacji na temat fragmentacji i odbudowywania indeksów, zobacz [Reorganizowanie i](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)ponowne kompilowanie indeksów.
