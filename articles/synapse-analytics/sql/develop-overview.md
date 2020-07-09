---
title: Zasoby służące do opracowywania funkcji SQL Synapse
description: Koncepcje dotyczące programowania, decyzje projektowe, zalecenia i techniki kodowania dla języka SQL Synapse.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: ba6ceec3064c5c876ca899ab58881e23913b9701
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81429020"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Projektowanie decyzji i technik kodowania dla funkcji SQL Synapse w usłudze Azure Synapse Analytics
W tym artykule znajdziesz listę zasobów dla puli SQL i funkcji SQL na żądanie (wersja zapoznawcza) usługi Synapse SQL. Zalecane artykuły są podzielone na dwie sekcje: najważniejsze decyzje projektowe i techniki programowania i kodowania.

Celem tych artykułów jest ułatwienie opracowywania optymalnego podejścia technicznego dla składników SQL Synapse w programie Synapse Analytics.

## <a name="key-design-decisions"></a>Najważniejsze decyzje projektowe
Artykuły poniżej wyróżniają koncepcje i decyzje projektowe dotyczące programowania w programie SQL Synapse:

|                                                          |   Pula SQL   | SQL na żądanie |
| -----------------------------------------------------    | ---- | ---- |
| [Połączenia](connect-overview.md)                    | Tak | Tak |
| [Klasy zasobów i współbieżność](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Yes    | Nie |
| [Transakcje](develop-transactions.md)              | Yes | Nie |
| [Schematy definiowane przez użytkownika](develop-user-defined-schemas.md) | Tak | Tak |
| [Dystrybucja tabel](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Yes | Nie |
| [Indeksy tabel](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Yes | Nie |
| [Partycje tabeli](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Yes | Nie |
| [Statystyki](develop-tables-statistics.md)            | Tak | Tak |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Yes | Nie |
| [Tabele zewnętrzne](develop-tables-external-tables.md) | Tak | Tak |
| [CETAS](develop-tables-cetas.md)                     | Tak | Tak |


## <a name="recommendations"></a>Zalecenia

Poniżej znajdziesz podstawowe artykuły, które podkreślają konkretne techniki kodowania, porady i zalecenia dotyczące programowania:

|                                            | Pula SQL | SQL na żądanie |
| ------------------------------------------ | ------------------ | ----------------------- |
| [Procedury składowane](develop-stored-procedures.md)  | Yes                | Nie                      |
| [Etykiety](develop-label.md)                           | Yes                | Nie                      |
| [Widoki](develop-views.md)                             | Tak                | Tak                     |
| [Tabele tymczasowe](develop-tables-temporary.md)       | Tak                | Tak                     |
| [Dynamiczny język SQL](develop-dynamic-sql.md)                 | Tak                | Tak                     |
| [Zapętlenie](develop-loops.md)                         | Tak                | Tak                     |
| [Grupuj według opcji](develop-group-by-options.md)       | Yes                | Nie                      |
| [Przypisanie zmiennej](develop-variable-assignment.md) | Tak                | Tak                     |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz [instrukcje usługi SQL Pool T-SQL](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

