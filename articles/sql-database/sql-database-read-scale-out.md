---
title: Odczytaj zapytania w replikach
description: Azure SQL Database zapewnia możliwość równoważenia obciążenia obciążeń tylko do odczytu przy użyciu pojemności replik tylko do odczytu — o nazwie odczyt skalowalny w poziomie.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: 1f47b01c4a9227d0e2ee45b17645b2ae97e4ba3d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821229"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Korzystanie z replik tylko do odczytu w celu równoważenia obciążenia obciążeń zapytań tylko do odczytu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W ramach [architektury wysokiej dostępności](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)każda baza danych w warstwie usług Premium i krytyczne dla działania firmy jest automatycznie obsługiwana przy użyciu repliki podstawowej i kilku replik pomocniczych. Pomocnicze repliki są obsługiwane z tym samym rozmiarem obliczeniowym co replika podstawowa. Funkcja **Odczyt skalowalny** w poziomie umożliwia równoważenie obciążenia SQL Database obciążeniami tylko do odczytu przy użyciu pojemności jednej z replik tylko do odczytu, a nie udostępnianie repliki do odczytu i zapisu. Dzięki temu obciążenie tylko do odczytu zostanie odizolowane od głównego obciążenia do odczytu i zapisu i nie będzie wpływać na jego wydajność. Ta funkcja jest przeznaczona dla aplikacji, które zawierają logicznie oddzielone, tylko do odczytu obciążenia, takie jak analiza. W warstwach usług premium i Krytyczne dla działania firmy aplikacje mogą uzyskać korzyści z wydajności przy użyciu tej dodatkowej pojemności bez dodatkowych kosztów.

Funkcja **Odczytaj skalowalny** w poziomie jest również dostępna w warstwie usługi w ramach skalowania po utworzeniu co najmniej jednej repliki pomocniczej. Jeśli obciążenia tylko do odczytu wymagają więcej zasobów niż dostępne w jednej replice pomocniczej, można użyć wielu replik pomocniczych. Architektura usługi Basic, standard i Ogólnego przeznaczenia o wysokiej dostępności nie obejmuje żadnych replik. Funkcja **odczytu skalowalnego** w poziomie nie jest dostępna w tych warstwach usług.

Poniższy diagram ilustruje go przy użyciu bazy danych Krytyczne dla działania firmy.

![Repliki tylko do odczytu](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

Funkcja odczyt skalowalny w poziomie jest domyślnie włączona w nowych bazach danych Premium, Krytyczne dla działania firmy i w skali. W przypadku tworzenia i skalowania jedna replika pomocnicza jest tworzona domyślnie dla nowych baz danych. Jeśli parametry połączenia SQL są skonfigurowane z `ApplicationIntent=ReadOnly`, aplikacja zostanie przekierowana przez bramę do repliki tylko do odczytu tej bazy danych. Aby uzyskać informacje na temat używania właściwości `ApplicationIntent`, zobacz [Określanie zamiaru aplikacji](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Jeśli chcesz upewnić się, że aplikacja nawiązuje połączenie z repliką podstawową, niezależnie od ustawienia `ApplicationIntent` w parametrach połączenia SQL, należy jawnie wyłączyć skalowanie w poziomie podczas tworzenia bazy danych lub zmiany konfiguracji. Jeśli na przykład baza danych jest uaktualniana z warstwy Standardowa lub Ogólnego przeznaczenia do warstwy Premium, Krytyczne dla działania firmy lub do skalowania i chcesz upewnić się, że wszystkie połączenia nadal przechodzą do repliki podstawowej, wyłącz opcję Odczytaj w poziomie. Aby uzyskać szczegółowe informacje na temat sposobu jego wyłączania, zobacz [Włączanie i wyłączanie skalowania](#enable-and-disable-read-scale-out)w poziomie.

> [!NOTE]
> Tworzenie zapytań dotyczących magazynu danych, zdarzeń rozszerzonych, programu SQL Profiler i funkcji inspekcji nie są obsługiwane w przypadku replik tylko do odczytu. 

## <a name="data-consistency"></a>Spójność danych

Jedną z korzyści wynikających z replik jest to, że repliki są zawsze w stanie spójnym w transakcjach, ale w różnych momentach mogą występować niewielkie opóźnienia między różnymi replikami. Skalowanie w poziomie nie obsługuje spójności poziomu sesji. Oznacza to, że jeśli sesja tylko do odczytu zostanie nawiązane ponownie po błędzie połączenia spowodowanym przez niedostępną replikę, może zostać przekierowana do repliki, która nie jest zgodna z 100% z repliką odczytu i zapisu. Podobnie, jeśli aplikacja zapisuje dane przy użyciu sesji odczytu i zapisu, a natychmiast odczytuje je przy użyciu sesji tylko do odczytu, możliwe jest, że najnowsze aktualizacje nie są natychmiast widoczne w replice. Opóźnienie jest spowodowane przez asynchroniczną operację wykonywania operacji w dzienniku transakcji.

> [!NOTE]
> Opóźnienia replikacji w regionie są niskie i ta sytuacja jest rzadkich.

## <a name="connect-to-a-read-only-replica"></a>Nawiązywanie połączenia z repliką tylko do odczytu

Po włączeniu skalowania w poziomie do bazy danych opcja `ApplicationIntent` w parametrach połączenia dostarczonych przez klienta wskazuje, czy połączenie jest kierowane do repliki zapisu, czy do repliki tylko do odczytu. Jeśli wartość `ApplicationIntent` jest `ReadWrite` (wartość domyślna), połączenie zostanie przekazane do repliki do odczytu i zapisu bazy danych. Jest to identyczne z istniejącym zachowaniem. Jeśli wartość `ApplicationIntent` jest `ReadOnly`, połączenie jest kierowane do repliki tylko do odczytu.

Na przykład następujące parametry połączenia łączą klienta z repliką tylko do odczytu (zastępując elementy w nawiasach kątowych prawidłowymi wartościami dla środowiska i upuszczając nawiasy kątowe):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Jeden z następujących parametrów połączenia łączy klienta z repliką odczytu i zapisu (zastępując elementy w nawiasach kątowych prawidłowymi wartościami dla danego środowiska i upuszczając nawiasy kątowe):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Weryfikowanie połączenia z repliką tylko do odczytu

Można sprawdzić, czy nawiązano połączenie z repliką tylko do odczytu, uruchamiając następujące zapytanie. Po nawiązaniu połączenia z repliką tylko do odczytu zwróci READ_ONLY.

```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> W danym momencie tylko jedna z replik AlwaysON jest dostępna dla sesji tylko do odczytu.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Monitorowanie i rozwiązywanie problemów z repliką tylko do odczytu

Po połączeniu z repliką tylko do odczytu można uzyskać dostęp do metryk wydajności przy użyciu `sys.dm_db_resource_stats` DMV. Aby uzyskać dostęp do statystyk planu zapytania, użyj `sys.dm_exec_query_stats`, `sys.dm_exec_query_plan` i `sys.dm_exec_sql_text` widoków DMV.

> [!NOTE]
> DMV `sys.resource_stats` w logicznej głównej bazie danych zwraca użycie procesora i dane magazynu repliki podstawowej.


## <a name="enable-and-disable-read-scale-out"></a>Włączanie i wyłączanie skalowania w poziomie odczytu

Skalowanie w poziomie do odczytu jest domyślnie włączone w warstwach usług premium, Krytyczne dla działania firmy i. Skalowanie w poziomie do odczytu nie może być włączone w warstwach usług podstawowa, standardowa lub Ogólnego przeznaczenia. Skalowanie w poziomie do odczytu jest automatycznie wyłączane w bazach danych w ramach skalowania skonfigurowanych przy użyciu 0 replik. 

Można wyłączyć i ponownie włączyć funkcję odczytywania skalowania w poziomie dla pojedynczych baz danych i baz danych puli elastycznej w warstwie Premium lub Krytyczne dla działania firmyj przy użyciu poniższych metod.

> [!NOTE]
> Możliwość wyłączenia odczytywania skalowalnego w poziomie jest zapewniana na potrzeby zgodności z poprzednimi wersjami.

### <a name="azure-portal"></a>Azure Portal

Ustawienie skalowanie odczyt w poziomie można zarządzać w bloku **Konfiguruj** bazę danych. 

### <a name="powershell"></a>PowerShell

Zarządzanie skalowaniem do odczytu w Azure PowerShell wymaga wydania z grudnia 2016 Azure PowerShell lub nowszego. Aby uzyskać najnowszą wersję programu PowerShell, zobacz [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Można wyłączyć lub ponownie włączyć funkcję odczytywania skalowania w Azure PowerShell przez wywołanie polecenia cmdlet [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) i przekazanie w pożądanej wartości — `Enabled` lub `Disabled`--dla parametru `-ReadScale`. 

Aby wyłączyć odczyt skalowalny w poziomie dla istniejącej bazy danych (zastępując elementy w nawiasach kątowych prawidłowymi wartościami dla danego środowiska i upuszczając nawiasy kątowe):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```
Aby wyłączyć odczyt skalowalny w poziomie dla nowej bazy danych (zastępując elementy w nawiasach kątowych prawidłowymi wartościami dla środowiska i upuszczając nawiasy kątowe):

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled -Edition Premium
```

Aby ponownie włączyć funkcję odczytywania skalowalnego w poziomie w istniejącej bazie danych (zastępując elementy w nawiasach kątowych wartościami prawidłowymi dla danego środowiska i porzuceniem nawiasów ostrych):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

### <a name="rest-api"></a>Interfejs API REST

Aby utworzyć bazę danych z wyłączonym skalowaniem do odczytu lub zmienić ustawienia dla istniejącej bazy danych, należy użyć następującej metody z właściwością `readScale` ustawioną na `Enabled` lub `Disabled` jak w poniższym przykładowym żądaniu.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Disabled"
   }
}
```

Aby uzyskać więcej informacji, zobacz [bazy danych — Tworzenie lub aktualizowanie](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>Używanie bazy danych TempDB w replice tylko do odczytu

Baza danych TempDB nie jest replikowana do replik tylko do odczytu. Każda replika ma własną wersję bazy danych TempDB, która jest tworzona podczas tworzenia repliki. Gwarantuje to, że baza danych TempDB jest aktualizowana i można ją modyfikować podczas wykonywania zapytania. Jeśli obciążenie tylko do odczytu zależy od używania obiektów TempDB, należy utworzyć te obiekty jako część skryptu zapytania. 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Używanie skalowania w poziomie z bazami danych z replikacją geograficzną

W przypadku korzystania z funkcji odczytu skalowalnego w poziomie do równoważenia obciążenia obciążeń przeznaczonych tylko do odczytu w bazie danych, która jest replikowana geograficznie (na przykład jako członek grupy trybu failover), należy się upewnić, że funkcja odczytywania skalowalnego w poziomie jest włączona zarówno w podstawowej, jak i w przypadku pomocniczych baz danych replikowanych geograficznie. Ta konfiguracja zapewnia, że to samo środowisko równoważenia obciążenia będzie kontynuowane, gdy aplikacja nawiąże połączenie z nowym serwerem podstawowym po przejściu w tryb failover. W przypadku nawiązywania połączenia z pomocniczą bazą danych z replikacją geograficzną z włączonym skalowaniem odczytu sesje z `ApplicationIntent=ReadOnly` będą kierowane do jednej z replik w taki sam sposób, w jaki kierujemy połączenia w podstawowej bazie danych.  Sesje bez `ApplicationIntent=ReadOnly` będą kierowane do podstawowej repliki pomocniczej replikacji geograficznej, która jest również tylko do odczytu. Ponieważ pomocnicza baza danych z replikacją geograficzną ma inny punkt końcowy niż podstawowa baza danych, historycznie uzyskuje dostęp do pomocniczego, niewymaganego do ustawienia `ApplicationIntent=ReadOnly`. Aby zapewnić zgodność z poprzednimi wersjami, `sys.geo_replication_links` DMV pokazuje `secondary_allow_connections=2` (dowolne połączenie z klientem jest dozwolone).

> [!NOTE]
> Operacje okrężne lub inne Routing z równoważeniem obciążenia między replikami lokalnymi pomocniczej bazy danych nie są obsługiwane.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat SQL Database funkcji tworzenia i skalowania, zobacz temat [skalowanie warstwy usług](./sql-database-service-tier-hyperscale.md).
