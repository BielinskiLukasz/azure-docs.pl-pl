---
title: Monitorowanie wydajności przy użyciu widoków DMV
description: Informacje na temat wykrywania i diagnozowania typowych problemów z wydajnością przy użyciu dynamicznych widoków zarządzania do monitorowania Microsoft Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 04/19/2020
ms.openlocfilehash: 6f33f49be74419a8f0cd31d973d64798f5d76a2c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81683019"
---
# <a name="monitoring-performance-azure-sql-database-using-dynamic-management-views"></a>Monitorowanie wydajności Azure SQL Database przy użyciu dynamicznych widoków zarządzania

Microsoft Azure SQL Database umożliwia podzbiór dynamicznych widoków zarządzania do diagnozowania problemów z wydajnością, co może być spowodowane przez zablokowane lub długotrwałe zapytania, wąskie gardła zasobów, słabe plany zapytań itd. Ten temat zawiera informacje dotyczące wykrywania typowych problemów z wydajnością przy użyciu dynamicznych widoków zarządzania.

SQL Database częściowo obsługuje trzy kategorie dynamicznych widoków zarządzania:

- Dynamiczne widoki zarządzania powiązane z bazami danych.
- Dynamiczne widoki zarządzania powiązane z wykonywaniem.
- Dynamiczne widoki zarządzania powiązane z transakcjami.

Aby uzyskać szczegółowe informacje na temat dynamicznych widoków zarządzania, zobacz [dynamiczne widoki zarządzania i funkcje (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) w SQL Server książki online.

## <a name="permissions"></a>Uprawnienia

W programie SQL Database wykonywanie zapytania dotyczącego dynamicznego widoku zarządzania wymaga uprawnień **Wyświetlanie stanu bazy danych** . Uprawnienie **Wyświetlanie stanu bazy danych** zwraca informacje o wszystkich obiektach w bieżącej bazie danych.
Aby udzielić uprawnienia do **wyświetlania stanu bazy danych** określonemu użytkownikowi bazy danych, uruchom następujące zapytanie:

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

W wystąpieniu SQL Server lokalnego, dynamiczne widoki zarządzania zwracają informacje o stanie serwera. W SQL Database zwracają informacje dotyczące tylko bieżącej logicznej bazy danych.

Ten artykuł zawiera kolekcję zapytań DMV, które można wykonać za pomocą SQL Server Management Studio lub Azure Data Studio w celu wykrycia następujących typów problemów z wydajnością zapytań:

- [Identyfikowanie zapytań dotyczących nadmiernego użycia procesora CPU](#identify-cpu-performance-issues)
- [PAGELATCH_ * i WRITE_LOG czekają na wąskie gardła we/wy](#identify-io-performance-issues)
- [PAGELATCH_ * czeka na bytTempDB rywalizację](#identify-tempdb-performance-issues)
- [RESOURCE_SEMAHPORE czeka na problemy z przydzieleniem pamięci](#identify-memory-grant-wait-performance-issues)
- [Identyfikowanie rozmiarów bazy danych i obiektów](#calculating-database-and-objects-sizes)
- [Pobieranie informacji o aktywnych sesjach](#monitoring-connections)
- [Pobierz informacje o użyciu zasobów dla całego systemu i bazy danych](#monitor-resource-use)
- [Pobieranie informacji o wydajności zapytań](#monitoring-query-performance)

## <a name="identify-cpu-performance-issues"></a>Identyfikowanie problemów z wydajnością procesora CPU

Jeśli użycie procesora CPU jest powyżej 80% przez dłuższy czas, należy wziąć pod uwagę następujące kroki rozwiązywania problemów:

### <a name="the-cpu-issue-is-occurring-now"></a>Problem z procesorem CPU występuje teraz

Jeśli problem występuje już teraz, istnieją dwa możliwe scenariusze:

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>Wiele indywidualnych zapytań, które łącznie zużywają duże użycie procesora CPU

Użyj następującego zapytania, aby zidentyfikować Najważniejsze skróty zapytania:

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM (SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
    FROM (SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
                CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
    GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>Długotrwałe zapytania, które zużywają procesor CPU, są nadal uruchomione

Użyj następującego zapytania, aby zidentyfikować te zapytania:

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
    CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>Wystąpił problem z procesorem CPU w przeszłości

Jeśli problem wystąpił w przeszłości i chcesz przeprowadzić analizę głównej przyczyny, użyj [magazynu zapytań](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store). Użytkownicy z dostępem do bazy danych mogą używać języka T-SQL do wykonywania zapytań dotyczących danych magazynu zapytań. Domyślne konfiguracje magazynu zapytań korzystają z stopnia szczegółowości 1 godziny. Użyj następującego zapytania, aby przyjrzeć się działaniu w przypadku dużych zapytań zużywających procesor CPU. To zapytanie zwraca 15 najważniejszych zapytań zużywających procesor CPU. Pamiętaj, aby `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()`zmienić:

```sql
-- Top 15 CPU consuming queries by query hash
-- note that a query  hash can have many query id if not parameterized or not parameterized properly
-- it grabs a sample query text by min
WITH AggregatedCPU AS (SELECT q.query_hash, SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec, SUM(count_executions * avg_cpu_time / 1000.0)/ SUM(count_executions) AS avg_cpu_millisec, MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec, MAX(max_logical_io_reads) max_logical_reads, COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans, COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids, SUM(CASE WHEN rs.execution_type_desc='Aborted' THEN count_executions ELSE 0 END) AS Aborted_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Regular' THEN count_executions ELSE 0 END) AS Regular_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Exception' THEN count_executions ELSE 0 END) AS Exception_Execution_Count, SUM(count_executions) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text
                       FROM sys.query_store_query_text AS qt
                            JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                            JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                            JOIN sys.query_store_runtime_stats AS rs ON rs.plan_id=p.plan_id
                            JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=rs.runtime_stats_interval_id
                       WHERE rs.execution_type_desc IN ('Regular', 'Aborted', 'Exception')AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                       GROUP BY q.query_hash), OrderedCPU AS (SELECT query_hash, total_cpu_millisec, avg_cpu_millisec, max_cpu_millisec, max_logical_reads, number_of_distinct_plans, number_of_distinct_query_ids, total_executions, Aborted_Execution_Count, Regular_Execution_Count, Exception_Execution_Count, sampled_query_text, ROW_NUMBER() OVER (ORDER BY total_cpu_millisec DESC, query_hash ASC) AS RN
                                                              FROM AggregatedCPU)
SELECT OD.query_hash, OD.total_cpu_millisec, OD.avg_cpu_millisec, OD.max_cpu_millisec, OD.max_logical_reads, OD.number_of_distinct_plans, OD.number_of_distinct_query_ids, OD.total_executions, OD.Aborted_Execution_Count, OD.Regular_Execution_Count, OD.Exception_Execution_Count, OD.sampled_query_text, OD.RN
FROM OrderedCPU AS OD
WHERE OD.RN<=15
ORDER BY total_cpu_millisec DESC;
```

Po zidentyfikowaniu problemów z zapytaniami czasowo dostroić te zapytania, aby zmniejszyć wykorzystanie procesora CPU.  Jeśli nie masz czasu na dostosowanie zapytań, możesz również uaktualnić cel bazy danych w celu obejścia tego problemu.

## <a name="identify-io-performance-issues"></a>Identyfikowanie problemów z wydajnością we/wy

Podczas identyfikowania problemów dotyczących wydajności operacji we/wy najważniejsze typy oczekiwania skojarzone z problemami we/wy są następujące:

- `PAGEIOLATCH_*`

  W przypadku problemów we/wy pliku `PAGEIOLATCH_SH`danych `PAGEIOLATCH_EX`( `PAGEIOLATCH_UP`w tym,,).  Jeśli nazwa typu oczekiwania zawiera we **/wy** , wskazuje na problem we/wy. Jeśli nie ma **operacji we/wy** w nazwie oczekiwania zamka strony, wskazuje na inny typ problemu (na przykład rywalizacja o bazę danych tempdb).

- `WRITE_LOG`

  W przypadku problemów z operacjami we/wy dziennika transakcji.

### <a name="if-the-io-issue-is-occurring-right-now"></a>Jeśli problem we/wy występuje już teraz

Użyj [widoku sys. dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) lub [sys. dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) , aby zobaczyć `wait_type` i `wait_time`.

#### <a name="identify-data-and-log-io-usage"></a>Identyfikowanie użycia operacji we/wy danych i dziennika

Użyj następującego zapytania, aby zidentyfikować dane i użycie operacji we/wy dziennika. Jeśli operacje we/wy danych lub dziennika są powyżej 80%, oznacza to, że użytkownicy korzystali z dostępnych operacji we/wy dla warstwy usługi SQL DB.

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

Jeśli limit operacji we/wy został osiągnięty, dostępne są dwie opcje:

- Opcja 1: uaktualnianie rozmiaru obliczeń lub warstwy usług
- Opcja 2: Identyfikowanie i dostrajanie zapytań zużywających najwięcej operacji we/wy.

#### <a name="view-buffer-related-io-using-the-query-store"></a>Wyświetlanie operacji we/wy związanych z buforem przy użyciu magazynu zapytań

W przypadku opcji 2 można użyć następującego zapytania względem magazynu zapytań dla operacji we/wy, aby wyświetlić ostatnie dwie godziny śledzonego działania:

```sql
-- top queries that waited on buffer
-- note these are finished queries
WITH Aggregated AS (SELECT q.query_hash, SUM(total_query_wait_time_ms) total_wait_time_ms, SUM(total_query_wait_time_ms / avg_query_wait_time_ms) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text, MIN(wait_category_desc) AS wait_category_desc
                    FROM sys.query_store_query_text AS qt
                         JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                         JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                         JOIN sys.query_store_wait_stats AS waits ON waits.plan_id=p.plan_id
                         JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=waits.runtime_stats_interval_id
                    WHERE wait_category_desc='Buffer IO' AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                    GROUP BY q.query_hash), Ordered AS (SELECT query_hash, total_executions, total_wait_time_ms, sampled_query_text, wait_category_desc, ROW_NUMBER() OVER (ORDER BY total_wait_time_ms DESC, query_hash ASC) AS RN
                                                        FROM Aggregated)
SELECT OD.query_hash, OD.total_executions, OD.total_wait_time_ms, OD.sampled_query_text, OD.wait_category_desc, OD.RN
FROM Ordered AS OD
WHERE OD.RN<=15
ORDER BY total_wait_time_ms DESC;
GO
```

#### <a name="view-total-log-io-for-writelog-waits"></a>Wyświetl łączny limit operacji we/wy dziennika dla WRITELOG

Jeśli typ oczekiwania to `WRITELOG`, użyj następującego zapytania, aby wyświetlić łączną liczbę operacji we/wy dziennika:

```sql
-- Top transaction log consumers
-- Adjust the time window by changing
-- rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
WITH AggregatedLogUsed
AS (SELECT q.query_hash,
           SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec,
           SUM(count_executions * avg_cpu_time / 1000.0) / SUM(count_executions) AS avg_cpu_millisec,
           SUM(count_executions * avg_log_bytes_used) AS total_log_bytes_used,
           MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec,
           MAX(max_logical_io_reads) max_logical_reads,
           COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans,
           COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Aborted' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Aborted_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Regular' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Regular_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Exception' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Exception_Execution_Count,
           SUM(count_executions) AS total_executions,
           MIN(qt.query_sql_text) AS sampled_query_text
    FROM sys.query_store_query_text AS qt
        JOIN sys.query_store_query AS q
            ON qt.query_text_id = q.query_text_id
        JOIN sys.query_store_plan AS p
            ON q.query_id = p.query_id
        JOIN sys.query_store_runtime_stats AS rs
            ON rs.plan_id = p.plan_id
        JOIN sys.query_store_runtime_stats_interval AS rsi
            ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
    WHERE rs.execution_type_desc IN ( 'Regular', 'Aborted', 'Exception' )
          AND rsi.start_time >= DATEADD(HOUR, -2, GETUTCDATE())
    GROUP BY q.query_hash),
     OrderedLogUsed
AS (SELECT query_hash,
           total_log_bytes_used,
           number_of_distinct_plans,
           number_of_distinct_query_ids,
           total_executions,
           Aborted_Execution_Count,
           Regular_Execution_Count,
           Exception_Execution_Count,
           sampled_query_text,
           ROW_NUMBER() OVER (ORDER BY total_log_bytes_used DESC, query_hash ASC) AS RN
    FROM AggregatedLogUsed)
SELECT OD.total_log_bytes_used,
       OD.number_of_distinct_plans,
       OD.number_of_distinct_query_ids,
       OD.total_executions,
       OD.Aborted_Execution_Count,
       OD.Regular_Execution_Count,
       OD.Exception_Execution_Count,
       OD.sampled_query_text,
       OD.RN
FROM OrderedLogUsed AS OD
WHERE OD.RN <= 15
ORDER BY total_log_bytes_used DESC;
GO
```

## <a name="identify-tempdb-performance-issues"></a>Identyfikowanie `tempdb` problemów z wydajnością

W przypadku identyfikowania problemów dotyczących wydajności operacji we/wy najważniejsze `tempdb` typy oczekiwania `PAGELATCH_*` skojarzone z `PAGEIOLATCH_*`problemami to (nie). Jednak `PAGELATCH_*` oczekiwania nie zawsze oznaczają `tempdb` rywalizację.  Taka odczekanie może również oznaczać, że masz zawartość strony danych obiektu użytkownika z powodu współbieżnych żądań przeznaczonych dla tej samej strony danych. Aby dodatkowo potwierdzić `tempdb` rywalizację, użyj wykazu [sys. dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) , aby potwierdzić, że wait_resource wartość `2:x:y` zaczyna się od `tempdb` , gdzie 2 jest identyfikatorem bazy danych, `x` jest identyfikatorem pliku i `y` jest identyfikatorem strony.  

W przypadku rywalizacji o bazę danych tempdb wspólna metoda polega na zmniejszeniu lub ponownym zapisaniu kodu aplikacji `tempdb`, na którym bazuje.  Typowe `tempdb` obszary użycia obejmują:

- Tabele tymczasowe
- Zmienne tabeli
- Parametry z wartościami przechowywanymi w tabeli
- Użycie magazynu wersji (powiązane z długotrwałymi transakcjami)
- Zapytania z planami zapytania, które używają sortowania, sprzężeń skrótów i buforów

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>Najpopularniejsze zapytania, które używają zmiennych tabeli i tabel tymczasowych

Użyj następującego zapytania, aby zidentyfikować najpopularniejsze zapytania, które używają zmiennych tabeli i tabel tymczasowych:

```sql
SELECT plan_handle, execution_count, query_plan
INTO #tmpPlan
FROM sys.dm_exec_query_stats
     CROSS APPLY sys.dm_exec_query_plan(plan_handle);
GO

WITH XMLNAMESPACES('http://schemas.microsoft.com/sqlserver/2004/07/showplan' AS sp)
SELECT plan_handle, stmt.stmt_details.value('@Database', 'varchar(max)') 'Database', stmt.stmt_details.value('@Schema', 'varchar(max)') 'Schema', stmt.stmt_details.value('@Table', 'varchar(max)') 'table'
INTO #tmp2
FROM(SELECT CAST(query_plan AS XML) sqlplan, plan_handle FROM #tmpPlan) AS p
    CROSS APPLY sqlplan.nodes('//sp:Object') AS stmt(stmt_details);
GO

SELECT t.plan_handle, [Database], [Schema], [table], execution_count
FROM(SELECT DISTINCT plan_handle, [Database], [Schema], [table]
     FROM #tmp2
     WHERE [table] LIKE '%@%' OR [table] LIKE '%#%') AS t
    JOIN #tmpPlan AS t2 ON t.plan_handle=t2.plan_handle;
```

### <a name="identify-long-running-transactions"></a>Zidentyfikuj długotrwałe transakcje

Użyj następującego zapytania, aby zidentyfikować długotrwałe transakcje. Długotrwałe transakcje uniemożliwiają czyszczenie magazynu wersji.

```sql
SELECT DB_NAME(dtr.database_id) 'database_name',
       sess.session_id,
       atr.name AS 'tran_name',
       atr.transaction_id,
       transaction_type,
       transaction_begin_time,
       database_transaction_begin_time transaction_state,
       is_user_transaction,
       sess.open_transaction_count,
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   txt.text,
                                                                   (req.statement_start_offset / 2) + 1,
                                                                   ((CASE req.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(txt.text)
                                                                         ELSE
                                                                             req.statement_end_offset
                                                                     END - req.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) Running_stmt_text,
       recenttxt.text 'MostRecentSQLText'
FROM sys.dm_tran_active_transactions AS atr
    INNER JOIN sys.dm_tran_database_transactions AS dtr
        ON dtr.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_tran_session_transactions AS sess
        ON sess.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_exec_requests AS req
        ON req.session_id = sess.session_id
           AND req.transaction_id = sess.transaction_id
    LEFT JOIN sys.dm_exec_connections AS conn
        ON sess.session_id = conn.session_id
    OUTER APPLY sys.dm_exec_sql_text(req.sql_handle) AS txt
    OUTER APPLY sys.dm_exec_sql_text(conn.most_recent_sql_handle) AS recenttxt
WHERE atr.transaction_type != 2
      AND sess.session_id != @@spid
ORDER BY start_time ASC;
```

## <a name="identify-memory-grant-wait-performance-issues"></a>Identyfikuj problemy z wydajnością odczekania pamięci

Jeśli typ oczekiwania to `RESOURCE_SEMAHPORE` i nie masz wysokiego problemu dotyczącego użycia procesora CPU, może wystąpić problem z przydzieleniem pamięci.

### <a name="determine-if-a-resource_semahpore-wait-is-a-top-wait"></a>Określanie, `RESOURCE_SEMAHPORE` czy oczekiwanie jest oczekiwanie na początku

Użyj następującego zapytania, aby określić, czy `RESOURCE_SEMAHPORE` oczekiwanie jest oczekiwanie na początku

```sql
SELECT wait_type,
       SUM(wait_time) AS total_wait_time_ms
FROM sys.dm_exec_requests AS req
    JOIN sys.dm_exec_sessions AS sess
        ON req.session_id = sess.session_id
WHERE is_user_process = 1
GROUP BY wait_type
ORDER BY SUM(wait_time) DESC;
```

### <a name="identity-high-memory-consuming-statements"></a>Wysoce czasochłonne instrukcje tożsamości

Użyj następującego zapytania, aby zidentyfikować następujące instrukcje zużywające pamięć:

```sql
SELECT IDENTITY(INT, 1, 1) rowId,
    CAST(query_plan AS XML) query_plan,
    p.query_id
INTO #tmp
FROM sys.query_store_plan AS p
    JOIN sys.query_store_runtime_stats AS r
        ON p.plan_id = r.plan_id
    JOIN sys.query_store_runtime_stats_interval AS i
        ON r.runtime_stats_interval_id = i.runtime_stats_interval_id
WHERE start_time > '2018-10-11 14:00:00.0000000'
      AND end_time < '2018-10-17 20:00:00.0000000';
GO
;WITH cte
AS (SELECT query_id,
        query_plan,
        m.c.value('@SerialDesiredMemory', 'INT') AS SerialDesiredMemory
    FROM #tmp AS t
        CROSS APPLY t.query_plan.nodes('//*:MemoryGrantInfo[@SerialDesiredMemory[. > 0]]') AS m(c) )
SELECT TOP 50
    cte.query_id,
    t.query_sql_text,
    cte.query_plan,
    CAST(SerialDesiredMemory / 1024. AS DECIMAL(10, 2)) SerialDesiredMemory_MB
FROM cte
    JOIN sys.query_store_query AS q
        ON cte.query_id = q.query_id
    JOIN sys.query_store_query_text AS t
        ON q.query_text_id = t.query_text_id
ORDER BY SerialDesiredMemory DESC;
```

### <a name="identify-the-top-10-active-memory-grants"></a>Zidentyfikuj 10 najważniejszych przydziałów pamięci

Użyj następującego zapytania, aby zidentyfikować 10 najważniejszych przydziałów pamięci:

```sql
SELECT TOP 10
    CONVERT(VARCHAR(30), GETDATE(), 121) AS runtime,
       r.session_id,
       r.blocking_session_id,
       r.cpu_time,
       r.total_elapsed_time,
       r.reads,
       r.writes,
       r.logical_reads,
       r.row_count,
       wait_time,
       wait_type,
       r.command,
       OBJECT_NAME(txt.objectid, txt.dbid) 'Object_Name',
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   text,
                                                                   (r.statement_start_offset / 2) + 1,
                                                                   ((CASE r.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(text)
                                                                         ELSE
                                                                             r.statement_end_offset
                                                                     END - r.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) stmt_text,
       mg.dop,                                               --Degree of parallelism
       mg.request_time,                                      --Date and time when this query requested the memory grant.
       mg.grant_time,                                        --NULL means memory has not been granted
       mg.requested_memory_kb / 1024.0 requested_memory_mb,  --Total requested amount of memory in megabytes
       mg.granted_memory_kb / 1024.0 AS granted_memory_mb,   --Total amount of memory actually granted in megabytes. NULL if not granted
       mg.required_memory_kb / 1024.0 AS required_memory_mb, --Minimum memory required to run this query in megabytes.
       max_used_memory_kb / 1024.0 AS max_used_memory_mb,
       mg.query_cost,                                        --Estimated query cost.
       mg.timeout_sec,                                       --Time-out in seconds before this query gives up the memory grant request.
       mg.resource_semaphore_id,                             --Non-unique ID of the resource semaphore on which this query is waiting.
       mg.wait_time_ms,                                      --Wait time in milliseconds. NULL if the memory is already granted.
       CASE mg.is_next_candidate --Is this process the next candidate for a memory grant
           WHEN 1 THEN
               'Yes'
           WHEN 0 THEN
               'No'
           ELSE
               'Memory has been granted'
       END AS 'Next Candidate for Memory Grant',
       qp.query_plan
FROM sys.dm_exec_requests AS r
    JOIN sys.dm_exec_query_memory_grants AS mg
        ON r.session_id = mg.session_id
           AND r.request_id = mg.request_id
    CROSS APPLY sys.dm_exec_sql_text(mg.sql_handle) AS txt
    CROSS APPLY sys.dm_exec_query_plan(r.plan_handle) AS qp
ORDER BY mg.granted_memory_kb DESC;
```

## <a name="calculating-database-and-objects-sizes"></a>Obliczanie rozmiarów baz danych i obiektów

Następujące zapytanie zwraca rozmiar bazy danych (w megabajtach):

```sql
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

Następujące zapytanie zwraca rozmiar pojedynczych obiektów (w megabajtach) w bazie danych:

```sql
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Monitorowanie połączeń

Możesz użyć widoku [sys. dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) , aby pobrać informacje o połączeniach ustanowionych z określonym serwerem Azure SQL Database i szczegóły dotyczące poszczególnych połączeń. Ponadto widok [sys. dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) jest przydatny podczas pobierania informacji o wszystkich aktywnych połączeniach użytkowników i zadaniach wewnętrznych.
Następujące zapytanie pobiera informacje dotyczące bieżącego połączenia:

```sql
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> W przypadku wykonywania widoków **sys. dm_exec_requests** i **sys. dm_exec_sessions**, jeśli masz uprawnienia do **wyświetlania stanu bazy** danych w bazie danych, zobaczysz wszystkie wykonywane sesje w bazie danych. w przeciwnym razie zostanie wyświetlona tylko bieżąca sesja.

## <a name="monitor-resource-use"></a>Monitoruj użycie zasobów

Użycie zasobów można monitorować przy użyciu [SQL Database szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md) i [magazynu zapytań](https://msdn.microsoft.com/library/dn817826.aspx).

Możesz również monitorować użycie przy użyciu następujących dwóch widoków:

- [sys. dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys. resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdm_db_resource_stats"></a>sys. dm_db_resource_stats

W każdej bazie danych SQL można użyć widoku [sys. dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) . Widok **sys. dm_db_resource_stats** pokazuje ostatnie dane użycia zasobów względem warstwy usług. Średnia wartość procentowa dla procesora CPU, operacji we/wy danych, zapisów dziennika i pamięci są rejestrowane co 15 sekund i są przechowywane przez 1 godzinę.

Ponieważ ten widok zapewnia bardziej szczegółowy wgląd w użycie zasobów, należy najpierw użyć wykazu **sys. dm_db_resource_stats** , aby uzyskać aktualną analizę stanu lub Rozwiązywanie problemów. Na przykład to zapytanie pokazuje średnie i maksymalne użycie zasobów dla bieżącej bazy danych w ciągu ostatniej godziny:

```sql
SELECT  
    AVG(avg_cpu_percent) AS 'Average CPU use in percent',
    MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
    AVG(avg_data_io_percent) AS 'Average data IO in percent',
    MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
    AVG(avg_log_write_percent) AS 'Average log write use in percent',
    MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
    AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
    MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
FROM sys.dm_db_resource_stats;  
```

Inne zapytania można znaleźć w przykładach w tabeli [sys. dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### <a name="sysresource_stats"></a>sys. resource_stats

Widok [sys. resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) w bazie danych **Master** zawiera dodatkowe informacje, które mogą pomóc w monitorowaniu wydajności bazy danych SQL w określonej warstwie usług i rozmiarze obliczeniowym. Dane są zbierane co 5 minut i są przechowywane przez około 14 dni. Ten widok jest przydatny do długoterminowej analizy historycznej, w jaki sposób baza danych SQL używa zasobów.

Na poniższym wykresie przedstawiono użycie zasobów procesora CPU dla bazy danych w warstwie Premium z wielkością obliczeniową P2 dla każdej godziny w tygodniu. Ten wykres zaczyna się w poniedziałek, pokazuje 5 dni roboczych, a następnie pokazuje weekend, gdy jest znacznie mniejszy w aplikacji.

![Użycie zasobów bazy danych SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Z danych ta baza danych ma obecnie szczytowe obciążenie procesora CPU znacznie ponad 50% użycia procesora CPU względem rozmiaru obliczeniowego P2 (środku dnia we wtorek). Jeśli procesor jest dominującym czynnikiem w profilu zasobów aplikacji, można zdecydować, że P2 jest odpowiednim rozmiarem obliczeniowym, aby zagwarantować, że obciążenie zawsze mieści się. Jeśli oczekujesz, że aplikacja zostanie powiększona z upływem czasu, dobrym pomysłem jest posiadanie dodatkowego buforu zasobów, dzięki czemu aplikacja nie osiągnie limitu poziomu wydajności. W przypadku zwiększenia rozmiaru obliczeń można zapobiec błędom widocznym dla klienta, które mogą wystąpić, gdy baza danych nie ma wystarczającej mocy, aby efektywnie przetwarzać żądania, szczególnie w środowiskach z uwzględnieniem opóźnień. Przykładem jest baza danych, która obsługuje aplikację, która maluje strony sieci Web na podstawie wyników wywołań bazy danych.

Inne typy aplikacji mogą interpretować ten sam wykres inaczej. Na przykład jeśli aplikacja próbuje przetwarzać dane listy płac codziennie i ma ten sam wykres, ten rodzaj modelu "zadanie wsadowe" może być bardziej przydatny w rozmiarze obliczeń P1. Rozmiar obliczeń P1 ma 100 DTU w porównaniu do 200 DTU w rozmiarze obliczeniowym P2. Rozmiar obliczeń P1 zapewnia połowę wydajności rozmiaru obliczeniowego P2. Tak więc 50 procent użycia procesora CPU w P2 równa się 100% użycia procesora CPU w P1. Jeśli aplikacja nie ma limitów czasu, być może nie ma znaczenia, czy zadanie trwa 2 godziny lub 2,5 godzin, jeśli zostanie zakończone dzisiaj. Aplikacja w tej kategorii prawdopodobnie może korzystać z rozmiaru obliczeń P1. Można wykorzystać fakt, że w ciągu dnia istnieją okresy, w których użycie zasobów jest niższe, dzięki czemu każdy "duży szczyt" może zostać przelana do jednego z troughsów w ciągu dnia. Rozmiar obliczeń P1 może być dobry dla tego rodzaju aplikacji (i oszczędności oszczędności), o ile zadania mogą zakończyć się codziennie.

Azure SQL Database uwidacznia informacje o wykorzystanych zasobach dla każdej aktywnej bazy danych w widoku **sys. resource_stats** bazy danych **Master** na każdym serwerze. Dane w tabeli są agregowane dla 5-minutowych interwałów. W przypadku warstw usług podstawowa, standardowa i Premium dane w tabeli mogą trwać więcej niż 5 minut, więc dane te są bardziej przydatne do analizy historycznej, a nie do analizy w czasie rzeczywistym. Wykonaj zapytanie dotyczące widoku **sys. resource_stats** , aby zobaczyć ostatnią historię bazy danych i sprawdzić, czy rezerwacja została wybrana, w razie potrzeby podano żądaną wydajność.

> [!NOTE]
> Aby zbadać **sys. resource_stats** w poniższych przykładach, musisz mieć połączenie z bazą danych **Master** serwera SQL Database.

Ten przykład pokazuje, jak są udostępniane dane w tym widoku:

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Widok wykazu sys. resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

W następnym przykładzie pokazano różne sposoby używania widoku wykazu **sys. resource_stats** , aby uzyskać informacje na temat używania zasobów przez bazę danych SQL:

1. Aby przyjrzeć się zasobom używanym w ostatnim tygodniu dla bazy danych userdb1, możesz uruchomić następujące zapytanie:

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. Aby oszacować, w jaki sposób obciążenie jest zgodne z rozmiarem obliczeń, należy przejść do szczegółów poszczególnych aspektów metryk zasobów: procesor, Odczyt, zapis, liczba procesów roboczych i liczba sesji. Oto poprawione zapytanie przy użyciu wykazu **sys. resource_stats** , aby zgłosić średnią i maksymalną wartość tych metryk zasobów:

    ```sql
    SELECT
        avg(avg_cpu_percent) AS 'Average CPU use in percent',
        max(avg_cpu_percent) AS 'Maximum CPU use in percent',
        avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
        max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
        avg(avg_log_write_percent) AS 'Average log write use in percent',
        max(avg_log_write_percent) AS 'Maximum log write use in percent',
        avg(max_session_percent) AS 'Average % of sessions',
        max(max_session_percent) AS 'Maximum % of sessions',
        avg(max_worker_percent) AS 'Average % of workers',
        max(max_worker_percent) AS 'Maximum % of workers'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

3. Dzięki tym informacjom dotyczącym średnich i maksymalnych wartości każdej metryki zasobów można ocenić, w jaki sposób obciążenie mieści się w wybranym rozmiarze. Zazwyczaj średnie wartości z wykazu **sys. resource_stats** zapewniają dobrą linię bazową do użycia względem rozmiaru docelowego. Powinna to być podstawowa pomiar. Przykładowo może być używana standardowa warstwa usługi z rozmiarem obliczeń S2. Średnie wartości procentowe użycia dla operacji odczytu i zapisu procesora CPU i we/wy są poniżej 40%, średnia liczba procesów roboczych poniżej 50, a średnia liczba sesji jest poniżej 200. Obciążenie może być zgodne z rozmiarem obliczeń S1. Można łatwo sprawdzić, czy baza danych mieści się w limicie procesów roboczych i sesji. Aby sprawdzić, czy baza danych mieści się w mniejszym rozmiarze obliczeniowym w odniesieniu do procesora CPU, odczytów i zapisów, Podziel liczbę jednostek DTU o mniejszym rozmiarze obliczeń przez liczbę jednostek DTU bieżącego rozmiaru obliczeń, a następnie pomnóż wynik przez 100:

    `S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40`

    Wynik jest względną różnicą wydajnościową między dwoma wielkościami obliczeniowymi wyrażoną w procentach. Jeśli użycie zasobów nie przekracza tej wartości, obciążenie może zmieścić się w mniejszym rozmiarze. Należy jednak przyjrzeć się wszystkim zakresom wartości użycia zasobów i określić procent, jak często obciążenie bazy danych zmieści się w mniejszym rozmiarze. Następujące zapytanie zwraca procent dopasowania na wymiar zasobu, na podstawie progu 40% obliczonego w tym przykładzie:

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Na podstawie warstwy usług bazy danych można zdecydować, czy obciążenie mieści się w mniejszym rozmiarze. Jeśli cel obciążenia bazy danych wynosi 99,9%, a poprzednie zapytanie zwraca wartości większe niż 99,9 procent dla wszystkich trzech wymiarów zasobów, obciążenie prawdopodobnie mieści się w mniejszym rozmiarze.

    Poszukiwanie wartości procentowej dopasowania zapewnia również wgląd w to, czy należy przenieść do kolejnego wyższego rozmiaru obliczeniowego, aby osiągnąć cel. Na przykład userdb1 pokazuje następujące użycie procesora CPU w ubiegłym tygodniu:

   | Średni procent procesora CPU | Maksymalny procent użycia procesora CPU |
   | --- | --- |
   | 24,5 |100,00 |

    Średni procesor CPU jest około kwartału limitu rozmiaru obliczeniowego, który może być również dopasowany do rozmiaru obliczeniowego bazy danych. Jednak wartość maksymalna pokazuje, że baza danych osiągnie limit rozmiaru obliczeń. Czy chcesz przejść do kolejnego większego rozmiaru obliczeń? Sprawdź, ile razy obciążenie osiągnie 100 procent, a następnie porównaj je z celem obciążenia bazy danych.

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Jeśli zapytanie zwróci wartość mniejszą niż 99,9 procent dla dowolnego z trzech wymiarów zasobów, należy rozważyć przeniesienie do następnego wyższego rozmiaru lub użycie technik dostrajania aplikacji w celu zmniejszenia obciążenia bazy danych SQL.

4. To ćwiczenie uwzględnia również przewidywany wzrost obciążenia w przyszłości.

W przypadku pul elastycznych można monitorować pojedyncze bazy danych w puli za pomocą metod opisanych w tej sekcji. Można jednak również monitorować pulę jako całość. Informacje na ten temat znajdziesz w artykule [Monitor and manage an elastic pool](sql-database-elastic-pool-manage-portal.md) (Monitorowanie puli elastycznej i zarządzanie nią).

### <a name="maximum-concurrent-requests"></a>Maksymalna liczba równoczesnych żądań

Aby sprawdzić liczbę równoczesnych żądań, Uruchom to zapytanie Transact-SQL w bazie danych SQL:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R;
```

Aby analizować obciążenie lokalnej bazy danych SQL Server, zmodyfikuj to zapytanie w celu odfiltrowania konkretnej bazy danych, którą chcesz analizować. Na przykład jeśli masz lokalną bazę danych o nazwie Moja baza danych, zapytanie Transact-SQL zwraca liczbę równoczesnych żądań w tej bazie danych:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R
INNER JOIN sys.databases D ON D.database_id = R.database_id
AND D.name = 'MyDatabase';
```

Jest to tylko migawka w jednym punkcie w czasie. Aby lepiej zrozumieć obciążenie i współbieżne wymagania dotyczące żądań, należy zebrać wiele próbek w czasie.

### <a name="maximum-concurrent-logins"></a>Maksymalna liczba współbieżnych logowań

Możesz analizować wzorce użytkownika i aplikacji, aby uzyskać pomysł z częstotliwością logowań. Możesz również uruchomić rzeczywiste obciążenia w środowisku testowym, aby upewnić się, że nie są to ani inne limity omawiane w tym artykule. Nie istnieje pojedyncze zapytanie ani dynamiczny widok zarządzania (DMV), który może wyświetlać współbieżne liczby logowań lub historię.

Jeśli wielu klientów używa tych samych parametrów połączenia, usługa uwierzytelnia każde logowanie. Jeśli 10 użytkowników jednocześnie nawiąże połączenie z bazą danych przy użyciu tej samej nazwy użytkownika i hasła, zostanie 10 współbieżnych logowań. Ten limit dotyczy tylko czasu trwania logowania i uwierzytelniania. Jeśli ten sam 10 użytkowników łączy się z bazą danych sekwencyjnie, liczba współbieżnych logowań nigdy nie będzie większa niż 1.

> [!NOTE]
> Obecnie ten limit nie ma zastosowania do baz danych w pulach elastycznych.

### <a name="maximum-sessions"></a>Maksymalna liczba sesji

Aby wyświetlić liczbę bieżących aktywnych sesji, Uruchom to zapytanie Transact-SQL w bazie danych SQL:

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections
```

W przypadku analizowania obciążenia SQL Server lokalnego należy zmodyfikować zapytanie, aby skoncentrować się na określonej bazie danych. To zapytanie pomaga określić możliwe potrzeby sesji dla bazy danych, Jeśli rozważasz przechodzenie do Azure SQL Database.

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections C
INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
INNER JOIN sys.databases D ON (D.database_id = S.database_id)
WHERE D.name = 'MyDatabase'
```

Ponownie te zapytania zwracają liczbę punktów w czasie. Jeśli zbierasz wiele przykładów z upływem czasu, będziesz mieć najlepszą wiedzę na temat użycia sesji.

W celu przeprowadzenia analizy SQL Database można uzyskać historyczne statystyki dotyczące sesji, badając widok [sys. resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) i przeglądając kolumnę **active_session_count** .

## <a name="monitoring-query-performance"></a>Monitorowanie wydajności zapytań

Zapytania powolne lub długotrwałe mogą zużywać znaczne zasoby systemowe. W tej sekcji przedstawiono sposób korzystania z dynamicznych widoków zarządzania w celu wykrywania kilku typowych problemów z wydajnością zapytań. Ze względu na rozwiązywanie problemów w witrynie Microsoft TechNet można rozwiązać [problemy z wydajnością w SQL Server 2008](https://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) .

### <a name="finding-top-n-queries"></a>Znajdowanie pierwszych N zapytań

Poniższy przykład zwraca informacje o pięciu pierwszych zapytania sklasyfikowanych według średniego czasu procesora CPU. Ten przykład agreguje zapytania według ich wartości skrótu zapytania, tak aby logicznie równoważne zapytania były pogrupowane według ich łącznego zużycia zasobów.

```sql
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
     MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
        SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
FROM sys.dm_exec_query_stats AS QS
CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Monitorowanie zablokowanych zapytań

Wykonywanie wolnych lub długotrwałych zapytań może współtworzyć nadmierne zużycie zasobów i być konsekwencją zablokowanych zapytań. Przyczyną blokowania może być słaba konstrukcja aplikacji, złe plany zapytań, brak przydatnych indeksów i tak dalej. Możesz użyć widoku sys. dm_tran_locks, aby uzyskać informacje o bieżącej aktywności blokowania w Azure SQL Database. Na przykład kod można znaleźć w temacie [sys. dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) w SQL Server Books Online.

### <a name="monitoring-query-plans"></a>Monitorowanie planów zapytań

Nieefektywny plan zapytania może również zwiększyć użycie procesora CPU. Poniższy przykład używa widoku [sys. dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) , aby określić, które zapytanie używa najbardziej SKUMULOWANEGO procesora CPU.

```sql
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Zobacz także

[Wprowadzenie do usługi SQL Database](sql-database-technical-overview.md)
