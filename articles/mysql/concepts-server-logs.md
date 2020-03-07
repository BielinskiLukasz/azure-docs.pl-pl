---
title: Dzienniki wolnych zapytań — Azure Database for MySQL
description: Zawiera opis wolnych dzienników zapytań dostępnych w Azure Database for MySQL i dostępnych parametrów do włączania różnych poziomów rejestrowania.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 9a3a58cab2d9673a4660967e3a11d7f88900e718
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359604"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Wolne dzienniki zapytań w Azure Database for MySQL
W Azure Database for MySQL dziennik wolnych zapytań jest dostępny dla użytkowników. Dostęp do dziennika transakcji nie jest obsługiwany. Dziennik wolnych zapytań może służyć do identyfikowania wąskich gardeł wydajności w celu rozwiązywania problemów.

Aby uzyskać więcej informacji na temat dziennika wolnych zapytań programu MySQL, zobacz [sekcję dziennik wolnych zapytań](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)dotyczących usługi MySQL Reference.

## <a name="access-slow-query-logs"></a>Dostęp do dzienników wolnych zapytań
Można wyświetlać i pobierać Azure Database for MySQL wolnych dzienników zapytań przy użyciu Azure Portal i interfejsu wiersza polecenia platformy Azure.

W witrynie Azure portal wybierz usługi Azure Database for MySQL server. W obszarze nagłówek **monitorowania** wybierz stronę **Dzienniki serwera** .

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [Konfigurowanie i dostęp do dzienników wolnych zapytań za pomocą interfejsu wiersza polecenia platformy Azure](howto-configure-server-logs-in-cli.md)

Podobnie można potokować dzienniki, aby Azure Monitor przy użyciu dzienników diagnostycznych. Aby uzyskać więcej informacji, zobacz [poniżej](concepts-server-logs.md#diagnostic-logs) .

## <a name="log-retention"></a>Przechowywanie dziennika
Dzienniki są dostępne przez maksymalnie siedem dni od ich utworzenia. Jeśli całkowity rozmiar dostępnych dzienników przekracza 7 GB, najstarsze pliki zostaną usunięte do momentu udostępnienia miejsca. 

Dzienniki są obracane co 24 godziny lub 7 GB, w zależności od tego, co nastąpi wcześniej.

## <a name="configure-slow-query-logging"></a>Skonfiguruj rejestrowanie wolnych zapytań 
Domyślnie dziennik wolnych zapytań jest wyłączony. Aby ją włączyć, ustaw wartość slow_query_log na włączone.

Inne parametry, które można dostosować, obejmują:

- **long_query_time**: Jeśli zapytanie trwa dłużej niż long_query_time (w sekundach), rejestrowane jest zapytanie. Wartość domyślna to 10 sekund.
- **log_slow_admin_statements**: Jeśli on zawiera instrukcje administracyjne, takie jak ALTER_TABLE i ANALYZE_TABLE w instrukcjach zapisanych do slow_query_log.
- **log_queries_not_using_indexes**: określa, czy zapytania, które nie używają indeksów są rejestrowane w slow_query_log
- **log_throttle_queries_not_using_indexes**: ten parametr ogranicza liczbę zapytań, które nie są indeksami, które można zapisać w dzienniku wolnych zapytań. Ten parametr zacznie obowiązywać, gdy log_queries_not_using_indexes jest ustawiona na wartość włączone.
- **log_output**: Jeśli "plik" umożliwia zapisanie dziennika wolnych zapytań do lokalnego magazynu serwera i Azure monitor dzienników diagnostycznych. W przypadku braku "Dziennik wolnych zapytań zostanie zapisany tylko w przypadku dzienników diagnostyki Azure Monitor. 

> [!IMPORTANT]
> Jeśli tabele nie są indeksowane, ustawienie parametrów `log_queries_not_using_indexes` i `log_throttle_queries_not_using_indexes` na wartość włączone może wpłynąć na wydajność programu MySQL, ponieważ wszystkie zapytania uruchomione względem tych nieindeksowanych tabel zostaną zazapisywane w dzienniku wolnych zapytań.<br><br>
> Jeśli planujesz rejestrowanie wolnych zapytań przez dłuższy czas, zaleca się ustawienie `log_output` na "Brak". W przypadku wybrania opcji "plik" te dzienniki są zapisywane w magazynie serwera lokalnego i mogą mieć wpływ na wydajność programu MySQL. 

Więcej opisów parametrów dziennika wolnych zapytań można znaleźć w [dokumentacji dziennika wolnych zapytań](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) programu MySQL.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne
Azure Database for MySQL jest zintegrowana z Azure Monitor dzienników diagnostycznych. Po włączeniu wolnych dzienników zapytań na serwerze MySQL można wybrać opcję ich emisji do Azure Monitor dzienników, Event Hubs lub Azure Storage. Aby dowiedzieć się więcej na temat włączania dzienników diagnostycznych, zobacz sekcję jak [znaleźć w dokumentacji dzienników diagnostycznych](../azure-monitor/platform/platform-logs-overview.md).

W poniższej tabeli opisano zawartość poszczególnych dzienników. W zależności od metody Output pola uwzględnione i kolejność ich wyświetlania mogą się różnić.

| **Właściwość** | **Opis** |
|---|---|
| `TenantId` | Identyfikator dzierżawy |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Sygnatura czasowa, gdy dziennik został zarejestrowany w formacie UTC |
| `Type` | Typ dziennika. Zawsze `AzureDiagnostics` |
| `SubscriptionId` | Identyfikator GUID subskrypcji, do której należy serwer |
| `ResourceGroup` | Nazwa grupy zasobów, do której należy serwer |
| `ResourceProvider` | Nazwa dostawcy zasobów. Zawsze `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identyfikator URI zasobu |
| `Resource` | Nazwa serwera |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Nazwa serwera |
| `start_time_t` [UTC] | Godzina rozpoczęcia zapytania |
| `query_time_s` | Łączny czas (w sekundach) wykonywania zapytania |
| `lock_time_s` | Łączny czas w sekundach, przez który zapytanie zostało zablokowane |
| `user_host_s` | Nazwa użytkownika |
| `rows_sent_s` | Liczba wysłanych wierszy |
| `rows_examined_s` | Liczba badanych wierszy |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Wstaw identyfikator |
| `sql_text_s` | Pełne zapytanie |
| `server_id_s` | Identyfikator serwera |
| `thread_id_s` | Identyfikator wątku |
| `\_ResourceId` | Identyfikator URI zasobu |

> [!Note]
> W przypadku `sql_text`Dziennik zostanie obcięty, jeśli przekracza 2048 znaków.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analizowanie dzienników w dziennikach Azure Monitor

Po powolnych dziennikach zapytań do Azure Monitor dzienników za poorednictwem dzienników diagnostycznych można przeprowadzić dalsze analizy wolnych zapytań. Poniżej przedstawiono kilka przykładowych zapytań, które pomogą Ci rozpocząć pracę. Pamiętaj o zaktualizowaniu poniższej nazwy serwera.

- Zapytania dłuższe niż 10 sekund na określonym serwerze

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Wyświetlanie 5 najważniejszych zapytań na określonym serwerze

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Podsumuj wolne zapytania według minimalnej, maksymalnej, średniej i odchylenia standardowego czasu zapytania na określonym serwerze

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- Tworzenie grafu wolnej dystrybucji zapytań na określonym serwerze

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- Wyświetlaj zapytania dłużej niż 10 sekund na wszystkich serwerach MySQL z włączonymi dziennikami diagnostycznymi

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Następne kroki
- [Jak skonfigurować dziennik wolnych zapytań z Azure Portal](howto-configure-server-logs-in-portal.md)
- [Jak konfigurować dzienniki wolnych zapytań z poziomu interfejsu wiersza polecenia platformy Azure](howto-configure-server-logs-in-cli.md).
