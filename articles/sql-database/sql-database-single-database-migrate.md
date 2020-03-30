---
title: Migracja bazy danych programu SQL Server do pojedynczej/puli bazy danych
description: Dowiedz się, jak o migracji bazy danych programu SQL Server do pojedynczej bazy danych lub puli elastycznej w bazie danych SQL Azure.
keywords: migracja bazy danych,migracja bazy danych programu sql server,narzędzia migracji bazy danych,migracja bazy danych,migracja bazy danych sql
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/11/2019
ms.openlocfilehash: 9cec91ccc80b9072b1a3da756f26f47eb88b951c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268615"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>Migracja bazy danych programu SQL Server do bazy danych SQL Azure

W tym artykule dowiesz się o podstawowych metodach migracji bazy danych programu SQL Server 2005 lub nowszej do pojedynczej lub puli bazy danych w bazie danych SQL Azure. Aby uzyskać informacje dotyczące migracji do wystąpienia zarządzanego, zobacz [Migrowanie do wystąpienia programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database](sql-database-managed-instance-migrate.md). Aby uzyskać informacje dotyczące migracji z innych platform, zobacz [Przewodnik migracji bazy danych platformy Azure](https://datamigration.microsoft.com/).

## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>Migrowanie do pojedynczej bazy danych lub puli bazy danych

Istnieją dwie podstawowe metody migracji bazy danych programu SQL Server 2005 lub nowszej do pojedynczej lub puli bazy danych w bazie danych SQL Azure. Pierwsza metoda jest prostsza, ale wymaga pewnego, prawdopodobnie znaczącego, przestoju podczas migracji. Druga metoda jest bardziej skomplikowana, ale znacznie eliminuje przestój podczas migracji.

W obu przypadkach należy upewnić się, że źródłowa baza danych jest zgodna z usługą Azure SQL Database przy użyciu [Asystenta migracji danych (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). Baza danych SQL V12 zbliża się do [parzystości funkcji](sql-database-features.md) z programem SQL Server, inne niż problemy związane z operacjami na poziomie serwera i między bazami danych. Bazy danych i aplikacje oparte na [częściowo obsługiwanych lub nieobsługiwanych funkcjach](sql-database-transact-sql-information.md) muszą zostać w pewnym stopniu [przeprojektowane, aby usunąć niezgodności](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues) i umożliwić migrację bazy danych programu SQL Server.

> [!NOTE]
> Aby wykonać migrację bazy danych innej niż baza danych programu SQL Server, w tym bazy danych Microsoft Access, Sybase, MySQL Oracle i DB2, do usługi Azure SQL Database, zobacz temat [Asystent migracji programu SQL Server](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/).

## <a name="method-1-migration-with-downtime-during-the-migration"></a>Metoda 1. Migracja z przestojem podczas migracji

 Ta metoda służy do migracji do pojedynczej lub puli bazy danych, jeśli można sobie pozwolić na pewne przestoje lub wykonujesz migrację testową produkcyjnej bazy danych do późniejszej migracji. Aby zapoznać się z samouczkiem, zobacz [Migrowanie bazy danych programu SQL Server](../dms/tutorial-sql-server-to-azure-sql.md).

Poniższa lista zawiera ogólny przepływ pracy dla migracji bazy danych programu SQL Server pojedynczej lub puli bazy danych przy użyciu tej metody. Aby przejść migrację do wystąpienia zarządzanego, zobacz [Migracja do wystąpienia zarządzanego](sql-database-managed-instance-migrate.md).

  ![Diagram migracji VSSSDT](./media/sql-database-cloud-migrate/azure-sql-migration-sql-db.png)

1. [Ocena](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) zgodności bazy danych przy użyciu najnowszej wersji [Asystenta migracji danych (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
2. Przygotowanie wszelkich niezbędnych poprawek jako skryptów języka Transact-SQL.
3. Zrób spójną transakcyjnie kopię źródłowej bazy danych, która jest migrowana, lub zatrzymaj nowe transakcje w źródłowej bazie danych podczas migracji. Metody do wykonania tej ostatniej opcji obejmują wyłączenie łączności klienta lub tworzenie [migawki bazy danych](https://msdn.microsoft.com/library/ms175876.aspx). Po migracji można użyć replikacji transakcyjnej do zaktualizowania zmigrowanych baz danych ze zmianami, które występują po punkcie odcięcia migracji. Zobacz [Migrowanie przy użyciu migracji transakcyjnej](sql-database-single-database-migrate.md#method-2-use-transactional-replication).  
4. Wdrożenie skryptów języka Transact-SQL w celu zastosowania poprawek do kopii bazy danych.
5. [Migrowanie](https://docs.microsoft.com/sql/dma/dma-migrateonpremsql) kopii bazy danych do nowej bazy danych SQL Azure przy użyciu Asystenta migracji danych.

> [!NOTE]
> Zamiast używać DMA, można również użyć pliku BACPAC. Zobacz [Importowanie pliku BACPAC do nowej bazy danych SQL Azure](sql-database-import.md).

### <a name="optimizing-data-transfer-performance-during-migration"></a>Optymalizowanie wydajności transferu danych podczas migracji

Poniższa lista zawiera zalecenia pozwalające uzyskać najlepszą wydajność podczas procesu importowania.

- Wybierz najwyższą warstwę usług i rozmiar obliczeń, który pozwala zmaksymalizować wydajność transferu. Po zakończeniu migracji będzie można dokonać skalowania w dół, aby nie ponosić nadmiernych kosztów.
- Zminimalizuj odległość między plikiem BACPAC a docelowym centrum danych.
- Wyłącz automatyczne statystyki na czas migracji.
- Partycjonuj tabele i indeksy.
- Usuń poindeksowane widoki i utwórz je ponownie po zakończeniu.
- Przenieś rzadko wyszukiwane dane historyczne do innej bazy danych i zmigruj te dane historyczne do oddzielnej bazy danych Azure SQL Database. Następnie będzie można wykonywać zapytania o te dane historyczne za pomocą [zapytań elastycznych](sql-database-elastic-query-overview.md).

### <a name="optimize-performance-after-the-migration-completes"></a>Optymalizacja wydajności po zakończeniu migracji

[Zaktualizuj statystyki](https://docs.microsoft.com/sql/t-sql/statements/update-statistics-transact-sql) poprzez pełne skanowanie po zakończeniu migracji.

## <a name="method-2-use-transactional-replication"></a>Metoda 2. Użycie replikacji transakcyjnej

Gdy nie możesz sobie pozwolić na usunięcie bazy danych programu SQL Server ze środowiska produkcyjnego na czas migracji, jako rozwiązania do migracji możesz wykorzystać replikację transakcyjną programu SQL Server. Aby użyć tej metody, źródłowa baza danych musi spełniać [wymagania dotyczące replikacji transakcyjnej](https://msdn.microsoft.com/library/mt589530.aspx) i być zgodna z usługą Azure SQL Database. Aby uzyskać informacje o replikacji SQL przy funkcji Zawsze włączone, zobacz [Konfigurowanie replikacji dla grup zawsze włączonych dostępności (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

Aby użyć tego rozwiązania, usługę Azure SQL Database konfiguruje się jako subskrybenta wystąpienia programu SQL Server do migracji. Dystrybutor replikacji transakcyjnej synchronizuje dane z bazy danych do synchronizacji (wydawcy), podczas gdy cały czas realizowane są nowe transakcje.

Przy replikacji transakcyjnej wszystkie zmiany wprowadzane do danych lub schematu pojawiają się w usłudze Azure SQL Database. Gdy synchronizacja zostanie zakończona i wszystko będzie gotowe do migracji, zmień parametry połączenia swoich aplikacji w taki sposób, aby wskazać im usługę Azure SQL Database. Gdy w wyniku replikacji transakcyjnej wszystkie zmiany pozostałe w źródłowej bazie danych zostaną pobrane, a wszystkie aplikacje będą wskazywać bazę danych platformy Azure, można odinstalować replikację transakcyjną. Usługa Azure SQL Database stanie się Twoim systemem produkcyjnym.

 ![Diagram SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

> [!TIP]
> Replikacji transakcyjnej możesz także używać do migrowania podzestawu źródłowej bazy danych. Publikacja replikowana do usługi Azure SQL Database może być ograniczona do podzbioru tabel w replikowanej bazie danych. Dla każdej replikowanej tabeli dane można ograniczyć do podzestawu wierszy i/lub podzestawu kolumn.

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Przepływ pracy migracji do usługi SQL Database za pomocą replikacji transakcyjnej

> [!IMPORTANT]
> Używaj najnowszej wersji programu SQL Server Management Studio, aby zachować synchronizację z aktualizacjami platformy Microsoft Azure i usługi SQL Database. Starsze wersje programu SQL Server Management Studio nie mogą skonfigurować usługi SQL Database jako subskrybenta. [Zaktualizuj program SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

1. Konfigurowanie dystrybucji
   - [Przy użyciu programu SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   - [Przy użyciu języka Transact-SQL](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)

2. Tworzenie publikacji
   - [Przy użyciu programu SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   - [Przy użyciu języka Transact-SQL](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. Tworzenie subskrypcji
   - [Przy użyciu programu SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   - [Przy użyciu języka Transact-SQL](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

Niektóre wskazówki i różnice dotyczące migracji do usługi SQL Database

- Użycie dystrybutora lokalnego:
  - W ten sposób powoduje wpływ na wydajność serwera.
  - Jeśli negatywny wpływ na wydajność jest nieakceptowalny, można użyć innego serwera, ale komplikuje to zarządzanie i administrowanie.
- Wybierając folder migawki, upewnij się, że wybrany folder jest wystarczająco duży, aby pomieścić kopię BCP każdej tabeli, którą chcesz zreplikować.
- Tworzenie migawki powoduje zablokowanie skojarzonych tabel do czasu zakończenia operacji, dlatego tworzenie migawek należy odpowiednio zaplanować.
- Usługa Azure SQL Database obsługuje wyłącznie subskrypcje wypychane. Subskrybentów można dodawać wyłącznie ze źródłowej bazy danych.

## <a name="resolving-database-migration-compatibility-issues"></a>Rozwiązywanie problemów ze zgodnością migracji bazy danych

Może wystąpić wiele różnych problemów ze zgodnością, w zależności od wersji programu SQL Server w źródłowej bazie danych oraz złożoności migrowanej bazy danych. Starsze wersje programu SQL Server mają więcej problemów ze zgodnością. Oprócz ukierunkowanego wyszukiwania w Internecie za pomocą preferowanej wyszukiwarki użyj następujących zasobów:

- [Funkcje bazy danych programu SQL Server nieobsługiwane przez usługę Azure SQL Database](sql-database-transact-sql-information.md)
- [Nieobsługiwane funkcje aparatu bazy danych w programie SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Nieobsługiwane funkcje aparatu bazy danych w programie SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Nieobsługiwane funkcje aparatu bazy danych w programie SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Nieobsługiwane funkcje aparatu bazy danych w programie SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [Nieobsługiwane funkcje aparatu bazy danych w programie SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Oprócz wyszukiwania w Internecie i użycia wymienionych zasobów, możesz skorzystać z [forów społeczności programu SQL Server w sieci MSDN](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) lub witryny [StackOverflow](https://stackoverflow.com/).

> [!IMPORTANT]
> Wystąpienie zarządzane bazy danych SQL umożliwia migrację istniejącego wystąpienia programu SQL Server i jego baz danych przy minimalnych do żadnych problemów ze zgodnością. Zobacz [Co to jest wystąpienie zarządzane](sql-database-managed-instance.md).

## <a name="next-steps"></a>Następne kroki

- Użyj skryptu z blogu SQL EMEA Engineers, aby [monitorować użycie bazy danych tempdb podczas migracji](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/).
- Użyj skryptu z blogu SQL EMEA Engineers, aby [monitorować obszar rejestrowania transakcji Twojej bazy danych w czasie trwania migracji](https://docs.microsoft.com/archive/blogs/azuresqlemea/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database).
- Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).
- Aby uzyskać informacje na temat pracy z czasem UTC po migracji, zobacz [Modifying the default time zone for your local time zone](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/) (Modyfikowanie domyślnej strefy czasowej dla Twojej lokalnej strefy czasowej).
- Aby uzyskać informacje na temat zmieniania domyślnego języka bazy danych po migracji, zobacz [How to change the default language of Azure SQL Database](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/) (Jak zmienić domyślny język usługi Azure SQL Database).
