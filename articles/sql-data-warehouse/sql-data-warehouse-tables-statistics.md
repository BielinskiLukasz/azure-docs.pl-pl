---
title: Tworzenie, aktualizowanie statystyk — Azure SQL Data Warehouse | Microsoft Docs
description: Zalecenia i przykłady dotyczące tworzenia i aktualizowania statystyk optymalizacji zapytań w tabelach w Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 05/09/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019
ms.openlocfilehash: 00643e303b3352ce9ce39e5a27fd8b42246aac51
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479166"
---
# <a name="table-statistics-in-azure-sql-data-warehouse"></a>Statystyka tabeli w Azure SQL Data Warehouse

Zalecenia i przykłady dotyczące tworzenia i aktualizowania statystyk optymalizacji zapytań w tabelach w Azure SQL Data Warehouse.

## <a name="why-use-statistics"></a>Dlaczego warto używać statystyk

Im więcej Azure SQL Data Warehouse wie o danych, tym szybciej może wykonywać zapytania. Po załadowaniu danych do SQL Data Warehouse zbieranie danych statystycznych jest jednym z najważniejszych rzeczy, które można wykonać, aby zoptymalizować zapytania. Optymalizator zapytań usługi SQL Data Warehouse to optymalizator oparty na kosztach. Porównuje koszt różnych planów zapytań, a następnie wybiera plan z najniższym kosztem. W większości przypadków wybiera plan, który będzie wykonywał najszybszy. Na przykład, jeśli optymalizator szacuje, że data filtrowania zapytania zwróci jeden wiersz, wybierze jeden plan. Jeśli szacuje się, że wybrana data zwróci 1 000 000 wierszy, zwróci inny plan.

## <a name="automatic-creation-of-statistic"></a>Automatyczne tworzenie statystyk

Gdy opcja AUTO_CREATE_STATISTICS bazy danych jest włączona, SQL Data Warehouse analizuje przychodzące zapytania użytkownika pod kątem brakujących statystyk. Jeśli brakuje statystyk, optymalizator zapytań tworzy statystyki dla poszczególnych kolumn w predykacie zapytania lub warunek sprzężenia, aby poprawić oszacowania kardynalności dla planu zapytania. Automatyczne tworzenie statystyk jest obecnie domyślnie włączone.

Aby sprawdzić, czy magazyn danych ma AUTO_CREATE_STATISTICS skonfigurowany, należy uruchomić następujące polecenie:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Jeśli magazyn danych nie ma skonfigurowanych AUTO_CREATE_STATISTICS, zalecamy włączenie tej właściwości, uruchamiając następujące polecenie:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Te instrukcje wyzwalają automatyczne tworzenie statystyk:

- WYBIERZ
- WSTAW — WYBIERZ
- CTAS
- UPDATE
- DELETE
- WYJAŚNIj, kiedy zostanie wykryta wartość Join lub obecność predykatu

> [!NOTE]
> Automatyczne tworzenie statystyk nie jest tworzone w tabelach tymczasowych lub zewnętrznych.

Automatyczne tworzenie statystyk jest wykonywane synchronicznie, dzięki czemu może nastąpić nieznacznie obniżenie wydajności zapytań, jeśli w kolumnach brakuje statystyk. Czas tworzenia statystyk dla pojedynczej kolumny zależy od rozmiaru tabeli. Aby uniknąć wymiernego obniżenia wydajności, szczególnie w przypadku oceny wydajności, należy upewnić się, że statystyki zostały utworzone najpierw przez wykonanie obciążenia testowego przed przeprowadzeniem profilowania systemu.

> [!NOTE]
> Tworzenie statystyk zostanie zarejestrowane w tabeli [sys. DM _pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) w innym kontekście użytkownika.

Po utworzeniu automatycznych statystyk będą one miały postać: _WA_Sys_< 8 Identyfikator kolumny cyfrowej w > szesnastkowym _ < 8 identyfikator tabeli cyfrowej w > szesnastkowym. Można wyświetlić statystyki, które zostały już utworzone, uruchamiając polecenie [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?view=azure-sqldw-latest) :

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

Table_name jest nazwą tabeli zawierającej statystyki do wyświetlenia. Nie może to być tabela zewnętrzna. Obiekt docelowy jest nazwą docelowego indeksu, statystyki lub kolumny, dla którego mają być wyświetlone informacje statystyczne.

## <a name="updating-statistics"></a>Aktualizowanie statystyk

Najlepszym rozwiązaniem jest aktualizacja statystyk dla kolumn dat każdego dnia, gdy dodawane są nowe daty. Za każdym razem, gdy nowe wiersze są ładowane do magazynu danych, dodawane są nowe daty ładowania lub daty transakcji. Te zmiany dystrybucji danych i statystyk są nieaktualne. Z drugiej strony, statystyki w kolumnie kraj/region w tabeli Customer mogą nigdy nie być aktualizowane, ponieważ dystrybucja wartości nie jest zwykle zmieniana. Przy założeniu, że dystrybucja jest stała między klientami, dodanie nowych wierszy do odmiany tabeli nie powoduje zmiany dystrybucji danych. Jeśli jednak magazyn danych zawiera tylko jeden kraj/region, a dane są przechowywane z nowego kraju/regionu, w wyniku czego dane pochodzą z wielu krajów/regionów, należy zaktualizować statystyki w kolumnie kraj/region.

Poniżej przedstawiono zalecenia dotyczące aktualizowania statystyk:

|||
|-|-|
| **Częstotliwość aktualizacji statystyk**  | Zachowawcze Codziennie </br> Po załadowaniu lub przeprowadzeniu przekształcenia danych |
| **Próbkowanie** |  Mniej niż 1 000 000 000 wierszy, Użyj domyślnego próbkowania (20 procent). </br> Mając więcej niż 1 000 000 000 wierszy, użyj próbkowania dwóch procent. |

Jednym z pierwszych pytań, które należy zadać w przypadku rozwiązywania problemów z kwerendą jest **"czy statystyki są aktualne?"**

To pytanie nie jest takie, które może zostać odebrane w wieku danych. Aktualny obiekt statystyk może być stary, jeśli nie wprowadzono żadnych istotnych zmian w danych źródłowych. Gdy liczba wierszy uległa znacznej zmianie lub w dystrybucji wartości dla kolumny istnieje istotna zmiana, *należy ją zaktualizować* .

Nie istnieje dynamiczny widok zarządzania, aby określić, czy dane w tabeli uległy zmianie od czasu ostatniego aktualizowania statystyk. Poznanie wieku Twoich statystyk może stanowić część obrazu. Przy użyciu następującego zapytania można określić czas ostatniej aktualizacji statystyk w każdej tabeli.

> [!NOTE]
> W przypadku zmiany materiału w dystrybucji wartości dla kolumny należy zaktualizować statystyki bez względu na czas ostatniej aktualizacji.

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

**Kolumny dat** w magazynie danych, na przykład zazwyczaj potrzebują częste aktualizacje statystyk. Za każdym razem, gdy nowe wiersze są ładowane do magazynu danych, dodawane są nowe daty ładowania lub daty transakcji. Te zmiany dystrybucji danych i statystyk są nieaktualne. Z drugiej strony statystyki w kolumnie płeć w tabeli klienta nigdy nie muszą być aktualizowane. Przy założeniu, że dystrybucja jest stała między klientami, dodanie nowych wierszy do odmiany tabeli nie powoduje zmiany dystrybucji danych. Jeśli jednak magazyn danych zawiera tylko jedną płeć, a nowy wymóg skutkuje wieloma płciami, należy zaktualizować statystyki w kolumnie płeć.

Aby uzyskać więcej informacji, zobacz Ogólne wskazówki dotyczące [statystyk](/sql/relational-databases/statistics/statistics).

## <a name="implementing-statistics-management"></a>Implementowanie zarządzania statystykami

Często dobrym pomysłem jest rozbudowa procesu ładowania danych w celu zagwarantowania, że statystyki są aktualizowane po zakończeniu ładowania. Ładowanie danych jest w przypadku, gdy tabele często zmieniają ich rozmiar i/lub rozkład wartości. W związku z tym jest to logiczne miejsce do implementowania niektórych procesów zarządzania.

Następujące zasady dotyczące identyfikatorów GUID są udostępniane do aktualizowania statystyk podczas procesu ładowania:

* Upewnij się, że każda załadowana tabela ma zaktualizowany co najmniej jeden obiekt statystyk. Spowoduje to zaktualizowanie informacji o rozmiarze tabeli (liczbie wierszy i liczbie stron) w ramach aktualizacji statystyk.
* Skup się na kolumnach uczestniczących w klauzulach JOIN, GROUP BY, ORDER BY i DISTINCT.
* Należy rozważyć aktualizowanie kolumn "Ascending Key", takich jak daty transakcji, ponieważ te wartości nie zostaną uwzględnione w histogramie statystyki.
* Należy rozważyć częste Aktualizowanie statycznych kolumn dystrybucji.
* Należy pamiętać, że każdy obiekt statystyki jest aktualizowany w kolejności. Po prostu `UPDATE STATISTICS <TABLE_NAME>` implementacja nie zawsze jest idealnym rozwiązaniem, szczególnie w przypadku szerokich tabel zawierających wiele obiektów statystyk.

Aby uzyskać więcej informacji, zobacz [oszacowanie kardynalności](/sql/relational-databases/performance/cardinality-estimation-sql-server).

## <a name="examples-create-statistics"></a>Przykłady: Tworzenie statystyk

W poniższych przykładach pokazano, jak używać różnych opcji tworzenia statystyk. Opcje, które są używane dla każdej kolumny, zależą od charakterystyki danych i sposobu użycia kolumny w zapytaniach.

### <a name="create-single-column-statistics-with-default-options"></a>Tworzenie statystyk z jedną kolumną z opcjami domyślnymi

Aby utworzyć statystyki dla kolumny, wystarczy podać nazwę dla obiektu statystyki i nazwę kolumny.

Ta składnia używa wszystkich opcji domyślnych. Domyślnie SQL Data Warehouse próbkuje **20 procent** tabeli podczas tworzenia statystyk.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Na przykład:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Utwórz statystyki pojedynczej kolumny, badając każdy wiersz

Domyślna częstotliwość próbkowania 20 procent jest wystarczająca w większości sytuacji. Można jednak dostosować częstotliwość próbkowania.

Aby pobrać pełną tabelę, użyj następującej składni:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Na przykład:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Utwórz statystyki pojedynczej kolumny, określając rozmiar próbki

Alternatywnie możesz określić rozmiar próbki jako procent:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Utwórz statystyki pojedynczej kolumny tylko dla niektórych wierszy

Możesz również utworzyć statystyki dotyczące części wierszy w tabeli. Ta nazwa jest nazywana przefiltrowanym statystyką.

Można na przykład użyć filtrowanych statystyk, gdy planujesz wykonać zapytanie dotyczące określonej partycji dużej partycjonowanej tabeli. Dzięki tworzeniu statystyk dotyczących tylko wartości partycji, dokładność statystyk zostanie zwiększona, a tym samym poprawi wydajność zapytań.

Ten przykład tworzy statystyki dotyczące zakresu wartości. Wartości mogą być łatwo zdefiniowane, aby odpowiadały zakresowi wartości w partycji.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Aby optymalizator zapytań mógł rozważyć użycie filtrowanych statystyk przy wyborze planu zapytania rozproszonego, zapytanie musi pasować do definicji obiektu statystyk. W poprzednim przykładzie klauzula WHERE zapytania musi określać wartości Kol1 z zakresu od 2000101 do 20001231.

### <a name="create-single-column-statistics-with-all-the-options"></a>Utwórz statystyki pojedynczej kolumny ze wszystkimi opcjami

Możesz również połączyć opcje razem. Poniższy przykład tworzy filtrowany obiekt statystyczny z niestandardowym rozmiarem próbki:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Aby uzyskać pełne informacje, zobacz [Tworzenie statystyk](/sql/t-sql/statements/create-statistics-transact-sql).

### <a name="create-multi-column-statistics"></a>Tworzenie statystyk wielokolumnowych

Aby utworzyć obiekt statystyk z wieloma kolumnami, wystarczy użyć powyższych przykładów, ale określić więcej kolumn.

> [!NOTE]
> Histogram, który jest używany do oszacowania liczby wierszy w wyniku zapytania, jest dostępny tylko dla pierwszej kolumny wymienionej w definicji obiektu statystyki.

W tym przykładzie histogram znajduje się w *kategorii Product\_(produkt*). Statystyki między kolumnami są obliczane na *podstawie\_kategorii produktu* i *sub_category produktu\_* :

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Ponieważ istnieje korelacja między *kategorią\_produktu* a *\_podrzędną\_kategorią produktu*, obiekt statystyk wielokolumnowych może być przydatny, jeśli dostęp do tych kolumn odbywa się w tym samym czasie.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Tworzenie statystyk dla wszystkich kolumn w tabeli

Jednym ze sposobów tworzenia statystyk jest wygenerowanie poleceń tworzenia statystyk po utworzeniu tabeli:

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Użyj procedury składowanej, aby utworzyć statystyki dla wszystkich kolumn w bazie danych

SQL Data Warehouse nie ma procedury składowanej systemowej równoważnej sp_create_stats w SQL Server. Ta procedura składowana tworzy obiekt statystyki jednokolumnowej dla każdej kolumny bazy danych, która nie ma jeszcze statystyk.

Poniższy przykład pomoże Ci rozpocząć pracę z projektem bazy danych. Śmiało, aby dostosować je do swoich potrzeb:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Aby utworzyć statystyki dla wszystkich kolumn w tabeli przy użyciu wartości domyślnych, wykonaj procedurę składowaną.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Aby utworzyć statystyki dla wszystkich kolumn w tabeli przy użyciu FULLSCAN, Wywołaj tę procedurę:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Aby utworzyć próbkowanie statystyk dla wszystkich kolumn w tabeli, wprowadź 3 i procent próbki. W tych procedurach jest stosowana częstotliwość próbkowania 20 procent.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

Aby utworzyć próbkowanie statystyk dla wszystkich kolumn

## <a name="examples-update-statistics"></a>Przykłady: Aktualizowanie statystyk

Aby zaktualizować dane statystyczne, możesz:

- Aktualizowanie jednego obiektu statystyki. Określ nazwę obiektu statystyki, który chcesz zaktualizować.
- Aktualizowanie wszystkich obiektów statystyk w tabeli. Określ nazwę tabeli zamiast jednego określonego obiektu statystyki.

### <a name="update-one-specific-statistics-object"></a>Aktualizowanie jednego określonego obiektu statystyki

Aby zaktualizować konkretny obiekt Statystyczny, należy użyć następującej składni:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Przykład:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Dzięki aktualizacji określonych obiektów statystyk można zminimalizować czas i zasoby wymagane do zarządzania statystykami. Jest to konieczne w przypadku wybrania najlepszych obiektów statystyk do zaktualizowania.

### <a name="update-all-statistics-on-a-table"></a>Aktualizowanie wszystkich statystyk w tabeli

Prostą metodą aktualizowania wszystkich obiektów statystyk w tabeli jest:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Na przykład:

```sql
UPDATE STATISTICS dbo.table1;
```

Instrukcja UPDATE STATISTICs jest łatwa w użyciu. Należy pamiętać, że aktualizuje *wszystkie* statystyki tabeli i dlatego może wykonywać więcej pracy niż jest to konieczne. Jeśli nie jest to problem, najłatwiejszym i najbardziej kompletnym sposobem zagwarantowania, że statystyki są aktualne.

> [!NOTE]
> Podczas aktualizowania wszystkich statystyk w tabeli SQL Data Warehouse skanuje w celu próbkowania tabeli dla każdego obiektu statystyki. Jeśli tabela jest duża i zawiera wiele kolumn i wiele statystyk, może być bardziej wydajna aktualizacja indywidualnych statystyk w zależności od potrzeb.

Aby uzyskać implementację `UPDATE STATISTICS` procedury, zobacz [tabele tymczasowe](sql-data-warehouse-tables-temporary.md). Metoda implementacji różni się nieco od poprzedniej `CREATE STATISTICS` procedury, ale wynik jest taki sam.

Pełną składnię można znaleźć w temacie [Update Statistics](/sql/t-sql/statements/update-statistics-transact-sql).

## <a name="statistics-metadata"></a>Metadane statystyk

Istnieje kilka widoków i funkcji systemu, których można użyć, aby znaleźć informacje o statystyce. Można na przykład sprawdzić, czy obiekt statystyki może być nieaktualny przy użyciu funkcji Statystyka daty, aby zobaczyć, kiedy statystyki zostały ostatnio utworzone lub zaktualizowane.

### <a name="catalog-views-for-statistics"></a>Widoki wykazu dla statystyk

Te widoki systemowe zawierają informacje o statystyce:

| Widok wykazu | Opis |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql) |Jeden wiersz dla każdej kolumny. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |Jeden wiersz dla każdego obiektu w bazie danych. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |Jeden wiersz dla każdego schematu w bazie danych. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql) |Jeden wiersz dla każdego obiektu statystyki. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql) |Jeden wiersz dla każdej kolumny w obiekcie Statystyka. Linki z powrotem do widoku sys. Columns. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql) |Jeden wiersz dla każdej tabeli (zawiera tabele zewnętrzne). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql) |Jeden wiersz dla każdego typu danych. |

### <a name="system-functions-for-statistics"></a>Funkcje systemowe dla statystyk

Te funkcje systemowe są przydatne do pracy z statystykami:

| Funkcja systemowa | Opis |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql) |Data ostatniej aktualizacji obiektu statystyki. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql) |Poziom podsumowania i szczegółowe informacje o dystrybucji wartości zrozumiałe dla obiektu Statystyka. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Łączenie kolumn statystyk i funkcji w jeden widok

Ten widok umożliwia łączenie kolumn odnoszących się do statystyk i wyników z funkcji STATS_DATE ().

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-showstatistics-examples"></a>Przykłady DBCC SHOW_STATISTICS ()

Polecenie DBCC SHOW_STATISTICS () pokazuje dane przechowywane w obiekcie statystyk. Te dane wchodzą w skład trzech części:

- nagłówek
- Wektor gęstości
- Histogram

Metadane nagłówka dotyczące statystyk. Histogram wyświetla rozkład wartości w pierwszej kolumnie klucza obiektu Statystyka. Wektor gęstości mierzy korelację między kolumnami. SQL Data Warehouse oblicza oszacowania kardynalności przy użyciu dowolnych danych w obiekcie Statystyka.

### <a name="show-header-density-and-histogram"></a>Pokaż nagłówek, gęstość i histogram

Ten prosty przykład przedstawia wszystkie trzy części obiektu statystyki:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Przykład:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>Pokaż co najmniej jedną część instrukcji DBCC SHOW_STATISTICS ()

Jeśli interesuje Cię tylko wyświetlanie określonych części, użyj `WITH` klauzuli i określ, które części mają być widoczne:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Na przykład:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>Różnice DBCC SHOW_STATISTICS ()

Polecenie DBCC SHOW_STATISTICS () jest bardziej ściśle zaimplementowane w SQL Data Warehouse porównaniu do SQL Server:

- Nieudokumentowane funkcje nie są obsługiwane.
- Nie można użyć Stats_stream.
- Nie można dołączyć wyników dla określonych podzestawów danych statystycznych. Na przykład STAT_HEADER JOIN DENSITY_VECTOR.
- Nie można ustawić NO_INFOMSGS na potrzeby pomijania komunikatów.
- Nie można użyć nawiasów kwadratowych wokół nazw statystyk.
- Nie można używać nazw kolumn do identyfikowania obiektów statystyk.
- Błąd niestandardowy 2767 nie jest obsługiwany.

## <a name="next-steps"></a>Następne kroki

Aby zwiększyć wydajność zapytań, zobacz [monitorowanie obciążenia](sql-data-warehouse-manage-monitor.md)
