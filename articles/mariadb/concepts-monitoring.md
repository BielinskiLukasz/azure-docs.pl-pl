---
title: Monitorowanie usługi Azure Database dla serwera MariaDB
description: W tym artykule opisano metryki dotyczące monitorowania i alertów dla usługi Azure Database dla serwera MariaDB, w tym statystyk procesora CPU, Magazyn i połączenia.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/10/2018
ms.openlocfilehash: f34e32b9bda83ac1185d0c7b7dcaaaa7b47161a1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082487"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Monitorowanie usługi Azure Database dla serwera MariaDB
Dane dotyczące serwerów monitorowania ułatwia rozwiązywanie problemów i zoptymalizować dla obciążenia. Usługa Azure Database dla serwera MariaDB oferuje różne metryki, które zapewniają wgląd w zachowania serwera.

## <a name="metrics"></a>Metryki
Wszystkie metryki usługi Azure mieć częstotliwość co minutę, a wszystkie metryki zapewnia 30 dni historii. Można skonfigurować alerty dotyczące metryk. Inne zadania obejmują skonfigurowanie zautomatyzowanych akcji, wykonywanie zaawansowanych analiz i archiwizowanie historii. Aby uzyskać więcej informacji, zobacz [Przegląd metryk usługi Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Aby uzyskać wskazówki krok po kroku, zobacz [jak skonfigurować alerty](howto-alert-metric.md).

### <a name="list-of-metrics"></a>Listy metryk
Te metryki są dostępne dla usługi Azure Database dla serwera MariaDB:

|Metryka|Nazwa wyświetlana metryki|Jednostka|Opis|
|---|---|---|---|
|cpu_percent|Procent użycia Procesora|Procent|Procent użycia Procesora w użyciu.|
|memory_percent|Procent pamięci|Procent|Procent pamięci w użyciu.|
|io_consumption_percent|% Operacji We/Wy|Procent|Procent we/wy w użyciu.|
|storage_percent|Procent użycia magazynu|Procent|Maksymalne przez wartość procentowa magazynu używane poza serwerem.|
|storage_used|Użyty magazyn|Bajty|Ilość miejsca w magazynie. Magazyn używany przez usługę mogą obejmować pliki bazy danych, dzienników transakcji i dzienniki serwera.|
|serverlog_storage_percent|Procent magazynu dziennika serwera|Procent|Wartość procentowa używane poza magazyn dzienników serwera maksymalna serwera magazynu dziennika serwera.|
|serverlog_storage_usage|Używany Magazyn dzienników serwera|Bajty|Ilość miejsca w magazynie dziennika na serwerze.|
|serverlog_storage_limit|Limit magazynu dziennika serwera|Bajty|Przechowywanie dziennika serwera maksymalną dla tego serwera.|
|storage_limit|Limit magazynu|Bajty|Maksymalny rozmiar magazynu dla tego serwera.|
|active_connections|Aktywne połączenia|Licznik|Liczba aktywnych połączeń z serwerem.|
|connections_failed|Połączenia zakończone niepowodzeniem|Licznik|Liczba połączeń z serwerem nie powiodło się.|
|network_bytes_egress|Sieć — wyjście|Bajty|Sieć się między aktywnych połączeń.|
|network_bytes_ingress|Sieć — wejście|Bajty|Sieć w różnych aktywnych połączeń.|

## <a name="server-logs"></a>Dzienniki serwera
Można włączyć wolnych zapytań rejestrowania na serwerze. Aby dowiedzieć się więcej na temat rejestrowania, odwiedź stronę [dzienniki serwera](concepts-server-logs.md) strony.

## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać więcej informacji na temat sposobu uzyskania dostępu i eksportowanie metryk przy użyciu witryny Azure portal, interfejsu API REST lub interfejsu wiersza polecenia, zobacz [Przegląd metryk usługi Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
  - Zobacz [jak skonfigurować alerty](howto-alert-metric.md) wskazówki dotyczące tworzenia alertu na metrykę.
