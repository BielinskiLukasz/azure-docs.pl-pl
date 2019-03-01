---
title: Dzienniki serwera usługi Azure Database for MySQL
description: W tym artykule opisano dzienników dostępnych w usłudze Azure Database for MySQL i dostępne parametry włączenie rejestrowania różnych poziomów.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: bc3dd038a21786a75d6b519ef8523dc328829705
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192892"
---
# <a name="server-logs-in-azure-database-for-mysql"></a>Dzienniki serwera w usłudze Azure Database for MySQL
W usłudze Azure Database for MySQL w dzienniku wolnych zapytań jest dostępna dla użytkowników. Dostęp do dziennika transakcji nie jest obsługiwane. Dziennik dotyczący wolnego zapytania może służyć do identyfikowania wąskich gardeł wydajności w celu rozwiązywania problemów. 

Więcej informacji na temat w dzienniku wolnych zapytań MySQL na ten temat można znaleźć w podręczniku odwołanie MySQL [wolne sekcji dziennika zapytań](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

## <a name="access-server-logs"></a>Dzienniki dostępu serwera
Można wyświetlić listę i Pobierz — Azure Database dla MySQL dzienniki serwera przy użyciu witryny Azure portal i interfejsu wiersza polecenia platformy Azure.

W witrynie Azure portal wybierz usługi Azure Database for MySQL server. W obszarze **monitorowanie** nagłówka, wybierz **dzienniki serwera** strony.

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [Konfiguruj i dostęp do dzienników serwera przy użyciu wiersza polecenia platformy Azure](howto-configure-server-logs-in-cli.md).

## <a name="log-retention"></a>Przechowywanie dzienników
Dzienniki są dostępne przez maksymalnie siedem dni od ich tworzenia. Jeśli całkowity rozmiar dostępne dzienniki przekracza 7 GB, najstarsze pliki zostaną usunięte do momentu zwolnienia miejsca. 

Dzienniki zostały obrócone co 24 godziny lub 7 GB, zależnie co nastąpi wcześniej.


## <a name="configure-logging"></a>Konfigurowanie rejestrowania 
Domyślnie dziennik wolnych zapytań jest wyłączony. Aby ją włączyć, ustaw slow_query_log na wartość ON.

Inne parametry, które można dostosować obejmują:

- **long_query_time**: Jeśli zapytanie dłużej niż long_query_time (w sekundach) to zapytanie jest rejestrowane. Wartość domyślna to 10 sekund.
- **log_slow_admin_statements**: Jeśli ON zawiera administracyjnej instrukcji takich jak ALTER_TABLE i ANALYZE_TABLE w instrukcjach zapisywane slow_query_log.
- **log_queries_not_using_indexes**: Określa, czy zapytań, które nie korzystają z indeksów są rejestrowane slow_query_log
- **log_throttle_queries_not_using_indexes**: Ten parametr ogranicza liczbę zapytań — indeksowanie, które mogą być zapisywane w dzienniku wolnych zapytań. Ten parametr staje się skuteczny po log_queries_not_using_indexes jest ustawiona na wartość ON.

Zobacz MySQL [wolne dokumentacji dziennika zapytań](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) pełne opisy parametrów dziennik wolnych zapytań.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne
Usługa Azure Database for MySQL jest zintegrowany z dzienników diagnostycznych usługi Azure Monitor. Po włączeniu dzienniki wolnych zapytań na Twoim serwerze MySQL możesz je emitowane dzienniki usługi Azure Monitor, usługa Event Hubs lub usługi Azure Storage. Aby dowiedzieć się więcej na temat włączania dzienników diagnostycznych, zobacz, jak części [dokumentacja dzienników diagnostycznych](../azure-monitor/platform/diagnostic-logs-overview.md).

> [!IMPORTANT]
> Ta funkcja diagnostyki dla dzienników serwera jest dostępna tylko w ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci [warstw cenowych](concepts-pricing-tiers.md).

W poniższej tabeli opisano, co znajduje się w każdym dzienniku. W zależności od danych wyjściowych metody, pola, znajdujące się i kolejność, w jakiej są wyświetlane mogą się różnić.

| **Właściwość** | **Opis** |
|---|---|---|
| TenantId | Identyfikator dzierżawy |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Sygnatura czasowa podczas rejestrowania w formacie UTC |
| Type | Typ dziennika. zawsze `AzureDiagnostics` |
| SubscriptionId | Identyfikator GUID dla subskrypcji, do której należy serwer |
| ResourceGroup | Nazwa grupy zasobów, do której należy serwer |
| ResourceProvider | Nazwa dostawcy zasobów. zawsze `MICROSOFT.DBFORMYSQL` |
| ResourceType | `Servers` |
| ResourceId | Identyfikator URI zasobu |
| Zasób | Nazwa serwera |
| Kategoria | `MySqlSlowLogs` |
| OperationName | `LogEvent` |
| Logical_server_name_s | Nazwa serwera |
| start_time_t [UTC] | Czas rozpoczęcia zapytania |
| query_time_s | Całkowity czas trwania zapytania zajęło wykonanie |
| lock_time_s | Całkowity czas trwania zapytania został zablokowany. |
| user_host_s | Nazwa użytkownika |
| rows_sent_s | Liczba wierszy, wysłane |
| rows_examined_s | Liczba wierszy, które badania |
| last_insert_id_s | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| insert_id_s | Wstaw identyfikator |
| sql_text_s | Pełne zapytanie w języku |
| server_id_s | Identyfikator serwera |
| thread_id_s | Identyfikator wątku |
| \_ResourceId | Identyfikator URI zasobu |

## <a name="next-steps"></a>Następne kroki
- [Jak skonfigurować i uzyskać dostęp do dzienników serwera z wiersza polecenia platformy Azure](howto-configure-server-logs-in-cli.md).
