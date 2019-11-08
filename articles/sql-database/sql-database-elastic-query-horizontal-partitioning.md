---
title: Raportowanie w skalowanych bazach danych w chmurze
description: Jak skonfigurować zapytania elastyczne na partycjach poziomych
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/03/2019
ms.openlocfilehash: 79abaade22fc107fa4c848607ff48232eeeb58ad
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823764"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Raportowanie w skalowanych bazach danych w chmurze (wersja zapoznawcza)

![Zapytanie w fragmentów][1]

Bazy danych podzielonej na fragmenty dystrybuują wiersze w warstwie danych skalowanych w poziomie. Schemat jest identyczny we wszystkich uczestniczących bazach danych, nazywany również partycjonowaniem poziomym. Przy użyciu zapytania elastycznego można tworzyć raporty obejmujące wszystkie bazy danych w bazie danych podzielonej na fragmenty.

Aby zapoznać się z przewodnikiem Szybki Start, zobacz [raportowanie w skalowalnych bazach danych w chmurze](sql-database-elastic-query-getting-started.md).

W przypadku baz danych innych niż podzielonej na fragmenty zobacz [zapytania w bazach danych w chmurze z różnymi schematami](sql-database-elastic-query-vertical-partitioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz mapę fragmentu przy użyciu biblioteki klienta Elastic Database. Zobacz [Zarządzanie mapami fragmentu](sql-database-elastic-scale-shard-map-management.md). Lub użyj przykładowej aplikacji w temacie Rozpoczynanie [pracy z narzędziami Elastic Database](sql-database-elastic-scale-get-started.md).
* Możesz też zapoznać się z tematem [Migrowanie istniejących baz danych do skalowalnych baz danych](sql-database-elastic-convert-to-use-elastic-tools.md).
* Użytkownik musi mieć uprawnienie Zmień każde zewnętrzne źródło danych. To uprawnienie jest dołączone do uprawnienia ALTER DATABASE.
* Aby odwołać się do bazowego źródła danych, należy zmienić wszystkie uprawnienia zewnętrznych źródeł danych.

## <a name="overview"></a>Omówienie

Te instrukcje tworzą reprezentację metadanych warstwy danych podzielonej na fragmenty w bazie danych zapytań elastycznych.

1. [UTWÓRZ KLUCZ GŁÓWNY](https://msdn.microsoft.com/library/ms174382.aspx)
2. [UTWÓRZ POŚWIADCZENIA W ZAKRESIE BAZY DANYCH](https://msdn.microsoft.com/library/mt270260.aspx)
3. [UTWÓRZ ZEWNĘTRZNE ŹRÓDŁO DANYCH](https://msdn.microsoft.com/library/dn935022.aspx)
4. [TWORZENIE TABELI ZEWNĘTRZNEJ](https://msdn.microsoft.com/library/dn935021.aspx)

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1,1 Tworzenie klucza głównego i poświadczeń w zakresie bazy danych

To poświadczenie jest używane przez zapytanie elastyczne do łączenia się ze zdalnymi bazami danych.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Upewnij się, że *"\<username\>"* nie zawiera żadnego sufiksu *"\@ServerName"* .

## <a name="12-create-external-data-sources"></a>1,2 Tworzenie zewnętrznych źródeł danych

Obowiązuje

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH
            (TYPE = SHARD_MAP_MANAGER,
                       LOCATION = '<fully_qualified_server_name>',
            DATABASE_NAME = ‘<shardmap_database_name>',
            CREDENTIAL = <credential_name>,
            SHARD_MAP_NAME = ‘<shardmapname>’
                   ) [;]

### <a name="example"></a>Przykład

    CREATE EXTERNAL DATA SOURCE MyExtSrc
    WITH
    (
        TYPE=SHARD_MAP_MANAGER,
        LOCATION='myserver.database.windows.net',
        DATABASE_NAME='ShardMapDatabase',
        CREDENTIAL= SMMUser,
        SHARD_MAP_NAME='ShardMap'
    );

Pobierz listę bieżących zewnętrznych źródeł danych:

    select * from sys.external_data_sources;

Zewnętrzne źródło danych odwołuje się do mapy fragmentu. Elastyczne zapytanie używa zewnętrznego źródła danych i źródłowej mapy fragmentu do wyliczania baz danych, które uczestniczą w warstwie danych.
Te same poświadczenia są używane do odczytywania mapy fragmentu i uzyskiwania dostępu do danych w fragmentów podczas przetwarzania zapytania elastycznego.

## <a name="13-create-external-tables"></a>1,3 Tworzenie tabel zewnętrznych

Obowiązuje  

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])
        { WITH ( <sharded_external_table_options> ) }
    ) [;]  

    <sharded_external_table_options> ::=
      DATA_SOURCE = <External_Data_Source>,
      [ SCHEMA_NAME = N'nonescaped_schema_name',]
      [ OBJECT_NAME = N'nonescaped_object_name',]
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

**Przykład**

    CREATE EXTERNAL TABLE [dbo].[order_line](
         [ol_o_id] int NOT NULL,
         [ol_d_id] tinyint NOT NULL,
         [ol_w_id] int NOT NULL,
         [ol_number] tinyint NOT NULL,
         [ol_i_id] int NOT NULL,
         [ol_delivery_d] datetime NOT NULL,
         [ol_amount] smallmoney NOT NULL,
         [ol_supply_w_id] int NOT NULL,
         [ol_quantity] smallint NOT NULL,
         [ol_dist_info] char(24) NOT NULL
    )

    WITH
    (
        DATA_SOURCE = MyExtSrc,
         SCHEMA_NAME = 'orders',
         OBJECT_NAME = 'order_details',
        DISTRIBUTION=SHARDED(ol_w_id)
    );

Pobierz listę tabel zewnętrznych z bieżącej bazy danych:

    SELECT * from sys.external_tables;

Aby porzucić tabele zewnętrzne:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Uwagi

Klauzula źródła danych\_definiuje zewnętrzne źródło danych (mapę fragmentu), które jest używane dla tabeli zewnętrznej.  

W SCHEMAcie\_nazwy i obiektu\_klauzule NAME mapują definicję tabeli zewnętrznej na tabelę w innym schemacie. W przypadku pominięcia założono, że schemat obiektu zdalnego jest "dbo", a jego nazwa jest taka sama jak zdefiniowana nazwa tabeli zewnętrznej. Jest to przydatne, jeśli nazwa tabeli zdalnej jest już wykonywana w bazie danych, w której ma zostać utworzona tabela zewnętrzna. Na przykład, chcesz zdefiniować tabelę zewnętrzną, aby uzyskać Zagregowany widok widoków wykazu lub widoków DMV w warstwie danych skalowanych w poziomie. Ponieważ widoki wykazu i widoków DMV już istnieją lokalnie, nie można używać ich nazw dla definicji tabeli zewnętrznej. Zamiast tego należy użyć innej nazwy i użyć nazwy widoku wykazu lub DMV w SCHEMAcie\_nazwy i/lub\_klauzule nazw obiektów. (Zobacz Poniższy przykład).

Klauzula DISTRIBUTION określa dystrybucję danych używaną dla tej tabeli. Procesor zapytań wykorzystuje informacje podane w klauzuli DISTRIBUTION do tworzenia najbardziej wydajnych planów zapytań.

1. **Podzielonej na fragmenty** oznacza, że dane są partycjonowane w poziomie między bazami danych. Kluczem partycjonowania dystrybucji danych jest **< sharding_column_name >** parametr.
2. **Zreplikowane** oznacza, że identyczne kopie tabeli są obecne w każdej bazie danych. Jest odpowiedzialny za zapewnienie, że repliki są identyczne w bazach danych.
3. " **ROUND\_Robin** " oznacza, że tabela jest w poziomie partycjonowana przy użyciu metody dystrybucji zależnej od aplikacji.

**Odwołanie do warstwy danych**: tablica zewnętrzna DDL odwołuje się do zewnętrznego źródła danych. Zewnętrzne źródło danych określa mapę fragmentu, która dostarcza tabelę zewnętrzną z informacjami niezbędnymi do lokalizowania wszystkich baz danych w warstwie danych.

### <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami

Użytkownicy z dostępem do tabeli zewnętrznej automatycznie uzyskują dostęp do podstawowych tabel zdalnych w ramach poświadczeń podanych w definicji zewnętrznego źródła danych. Unikaj niepożądanego podniesienia uprawnień za pomocą poświadczeń zewnętrznego źródła danych. Użyj uprawnienia GRANT lub REVOKE dla tabeli zewnętrznej, tak jakby była to zwykła tabela.  

Po zdefiniowaniu zewnętrznego źródła danych i tabel zewnętrznych można teraz używać pełnych instrukcji języka T-SQL dla tabel zewnętrznych.

## <a name="example-querying-horizontal-partitioned-databases"></a>Przykład: wykonywanie zapytań w poziomie partycjonowanych baz danych

Następujące zapytanie wykonuje trójwymiarowe sprzężenie między magazynami, zamówieniami i wierszami zamówienia i używa kilku agregacji i filtru selektywnego. Przyjęto założenie, że (1) skalowanie w poziomie (fragmentowania) i (2) te magazyny, zamówienia i linie zamówień są podzielonej na fragmenty przez kolumnę identyfikator magazynu, a elastyczne zapytanie może współistnieć z fragmentów i przetworzyć kosztowną część zapytania na fragmentów równoległ.

```sql
    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount,
         min(ol_delivery_d) as min_deliv_date
    from warehouse
    join orders
    on w_id = o_w_id
    join order_line
    on o_id = ol_o_id and o_w_id = ol_w_id
    where w_id > 100 and w_id < 200
    group by w_id, o_c_id
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Procedura składowana dla zdalnego wykonywania T-SQL: SP\_execute_remote

Zapytanie elastyczne zawiera również procedurę składowaną, która zapewnia bezpośredni dostęp do fragmentów. Procedura składowana jest nazywana [sp\_execute \_Remote](https://msdn.microsoft.com/library/mt703714) i może służyć do wykonywania zdalnych procedur składowanych lub kodu t-SQL w zdalnych bazach danych. Przyjmuje następujące parametry:

* Nazwa źródła danych (nvarchar): Nazwa zewnętrznego źródła danych typu RDBMS.
* Query (nvarchar): zapytanie T-SQL, które ma zostać wykonane na każdym fragmentu.
* Deklaracja parametru (nvarchar)-Optional: ciąg z definicjami typu danych dla parametrów używanych w parametrze zapytania (na przykład sp_executesql).
* Lista wartości parametrów — opcjonalne: rozdzielana przecinkami lista wartości parametrów (takich jak sp_executesql).

Polecenie Sp\_Execute\_zdalnego używa zewnętrznego źródła danych podanego w parametrach wywołania, aby wykonać daną instrukcję T-SQL w zdalnych bazach danych. Używa poświadczeń zewnętrznego źródła danych, aby nawiązać połączenie z bazą danych programu shardmap Manager i zdalnymi bazami danych.  

Przykład:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Łączność dla narzędzi

Użyj zwykłych parametrów połączenia SQL Server, aby połączyć swoją aplikację, narzędzia integracji danych i analizy biznesowej z bazą danych przy użyciu definicji tabeli zewnętrznej. Upewnij się, że SQL Server jest obsługiwane jako źródło danych dla narzędzia. Następnie odwołują się do bazy danych zapytań elastycznych, takiej jak jakakolwiek inna SQL Server baza danych połączona z narzędziem, i użyj zewnętrznych tabel z narzędzia lub aplikacji tak, jakby znajdowały się one w tabelach lokalnych.

## <a name="best-practices"></a>Najlepsze praktyki

* Upewnij się, że baza danych punktu końcowego zapytania elastycznego uzyskała dostęp do bazy danych shardmap i wszystkie fragmentów przez zapory SQL DB.  
* Sprawdzanie poprawności lub wymuszanie dystrybucji danych zdefiniowanej przez tabelę zewnętrzną. Jeśli rzeczywista Dystrybucja danych różni się od dystrybucji określonej w definicji tabeli, zapytania mogą dać nieoczekiwane wyniki.
* Zapytanie elastyczne obecnie nie wykonuje eliminacji fragmentu, gdy predykaty za pośrednictwem klucza fragmentowania umożliwią mu bezpieczne wykluczenie niektórych fragmentów z przetwarzania.
* Elastyczne zapytanie działa najlepiej w przypadku zapytań, w których większość obliczeń można wykonać w fragmentów. Zwykle uzyskuje się najlepszą wydajność zapytań z predykatami filtru selektywnego, które mogą być oceniane na fragmentów lub sprzężeń przez klucze partycjonowania, które mogą być wykonywane w sposób wyrównany do partycji na wszystkich fragmentów. Inne wzorce zapytań mogą wymagać załadowania dużych ilości danych z fragmentów do węzła głównego i mogą działać źle

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z omówieniem zapytania elastycznego, zobacz [Omówienie zapytania elastycznego](sql-database-elastic-query-overview.md).
* Aby zapoznać się z pionowym samouczkiem partycjonowania, zobacz [Rozpoczynanie pracy z kwerendą między bazami danych (partycjonowanie pionowe)](sql-database-elastic-query-getting-started-vertical.md).
* Aby poznać składnię i przykładowe zapytania dotyczące danych partycjonowanych pionowo, zobacz [wykonywanie zapytań dotyczących partycjonowanych danych w pionie.](sql-database-elastic-query-vertical-partitioning.md)
* Aby zapoznać się z samouczkiem dotyczącym partycjonowania poziomego (fragmentowania), zobacz [wprowadzenie do elastycznego zapytania na potrzeby partycjonowania poziomego (fragmentowania)](sql-database-elastic-query-getting-started.md).
* Zobacz [sp\_wykonaj \_zdalnego](https://msdn.microsoft.com/library/mt703714) dla procedury składowanej, która wykonuje instrukcję języka Transact-SQL w ramach jednej zdalnej Azure SQL Database lub zestawu baz danych służących jako fragmentów w poziomym schemacie partycjonowania.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
