---
title: Liczniki wydajności dla menedżera map fragmentów
description: ShardMapManager klas i danych zależnych routingu liczników wydajności
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 84bd1283020492ef6724aabd7daad4e153b11717
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043689"
---
# <a name="performance-counters-for-shard-map-manager"></a>Liczniki wydajności dla menedżera map fragmentów

Możesz przechwycić wydajności [Menedżera mapowań fragmentów](sql-database-elastic-scale-shard-map-management.md), zwłaszcza w przypadku korzystania [routing zależny od danych](sql-database-elastic-scale-data-dependent-routing.md). Liczniki są tworzone za pomocą metod klasy Microsoft.Azure.SqlDatabase.ElasticScale.Client.  

Liczniki są używane do śledzenia wydajności [routing zależny od danych](sql-database-elastic-scale-data-dependent-routing.md) operacji. Te liczniki są dostępne w Monitorze wydajności w obszarze "elastyczna baza danych: Kategoria zarządzania fragmentami".

**Aby uzyskać najnowszą wersję:** Przejdź do [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Zobacz też [uaktualnić aplikację do korzystania z najnowszych Biblioteka kliencka elastic database](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Aby utworzyć kategorii wydajności i liczników, użytkownik musi być częścią lokalnej **Administratorzy** grupy na komputerze hostującym aplikację.  
* Aby utworzyć wystąpienie licznika wydajności i zaktualizować liczników, użytkownik musi być członkiem **Administratorzy** lub **Użytkownicy monitora wydajności** grupy.

## <a name="create-performance-category-and-counters"></a>Tworzenie kategorii wydajności i liczników

Aby utworzyć liczników, wywołaj metodę CreatePerformanceCategoryAndCounters [klasy ShardMapManagementFactory](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx). Tylko administrator może wykonać metodę:

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Można również użyć [to](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) skrypt programu PowerShell, aby wykonać metodę.
Ta metoda tworzy następujące liczniki wydajności:  

* **Buforowane mapowania**: Liczba mapowań mapowania fragmentów w pamięci podręcznej.
* **Rekord DDR OP./s**: Częstotliwość zależne routingu operacje na danych dla mapowania fragmentów. Ten licznik jest aktualizowany po wywołaniu [OpenConnectionForKey()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) skutkuje udane połączenie do fragmentu docelowego.
* **Mapowanie trafień w pamięci podręcznej wyszukiwania na sekundę**: Szybkość przeprowadzania operacji wyszukiwania pomyślne pamięci podręcznej do mapowania w ramach mapowania fragmentów.
* **Mapowanie chybień w pamięci podręcznej wyszukiwania na sekundę**: Szybkość przeprowadzania operacji wyszukiwania w pamięci podręcznej nie powiodło się dla mapowania w ramach mapowania fragmentów.
* **Mapowania dodane lub zaktualizowane w pamięci podręcznej na sekundę**: Szybkość, w których mapowania są dodaniem lub zaktualizowaniem w pamięci podręcznej dla mapowania fragmentów.
* **Mapowania usuwane z pamięci podręcznej na sekundę**: Szybkość, z jaką mapowania są usuwane z pamięci podręcznej dla mapowania fragmentów.

Liczniki wydajności są tworzone dla każdego fragmentu pamięci podręcznej jest mapowany na proces.  

## <a name="notes"></a>Uwagi

Następujące zdarzenia wyzwolić tworzenie liczników wydajności:  

* Inicjowanie [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) z [wczesne ładowanie](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy.aspx), jeśli ShardMapManager zawiera żadnych mapowań fragmentów. Obejmują one [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx?f=255&MSPPError=-2147217396#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerFactory.GetSqlShardMapManager%28System.String,Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerLoadPolicy%29) i [TryGetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx) metody.
* Pomyślne wyszukiwania mapowania fragmentów w postaci (przy użyciu [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) lub [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)).
* Pomyślne utworzenie mapy fragmentów za pomocą CreateShardMap().

Liczniki wydajności zostaną zaktualizowane, wszystkie operacje pamięci podręcznej wykonać mapowania fragmentów i mapowania. Pomyślne usunięcie mapy fragmentów za pomocą DeleteShardMap() powoduje usunięcie wystąpienia liczników wydajności.  

## <a name="best-practices"></a>Najlepsze praktyki

* Tworzenie kategorii wydajności i liczniki powinny być wykonywane tylko raz przed tworzeniem ShardMapManager obiektu. Każdego wykonania polecenia CreatePerformanceCategoryAndCounters() czyści poprzednie liczniki (utraty danych przekazywanych przez wszystkie wystąpienia) i tworzy nowe.  
* Wystąpienia licznika wydajności są tworzone na proces. Wszelkie awarię aplikacji lub usunięcie mapowania fragmentów w postaci z pamięci podręcznej spowoduje usunięcie wystąpienia liczników wydajności.  

### <a name="see-also"></a>Zobacz także

[Elastic Database features overview (Omówienie funkcji Elastic Database)](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->