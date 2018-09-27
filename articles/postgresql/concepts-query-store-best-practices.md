---
title: Query Store najlepsze rozwiązania w usłudze Azure Database for PostgreSQL
description: W tym artykule opisano najlepsze rozwiązania dotyczące Store zapytania w usłudze Azure Database for PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: c449aaff9741250cfcfaaa4ee490aafc857c4369
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47395308"
---
# <a name="best-practices-for-query-store"></a>Najlepsze rozwiązania dotyczące Query Store

**Dotyczy:** Azure Database for postgresql w warstwie 9.6 i 10

> [!IMPORTANT]
> Funkcja Query Store jest w publicznej wersji zapoznawczej w ograniczonej liczbie regionów.


W tym artykule opisano najlepsze rozwiązania dotyczące używania Store zapytania w usłudze Azure Database for PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Ustaw tryb przechwytywania optymalne zapytania
Umożliwiają Query Store przechwytywać dane, które są ważne. 

|**pg_qs.query_capture_mode** | **Scenariusz**|
|---|---|
|_Wszystkie_  |Analizowanie obciążenia dokładnie pod względem wszystkich zapytań i ich częstotliwości wykonywania i innych danych statystycznych. Identyfikowanie nowych zapytań w obciążenia. Wykryj, jeśli zapytań ad hoc są używane do identyfikowania możliwości dla użytkownika lub parametryzacji automatycznie. _Wszystkie_ dołączono zużycia zasobów zwiększone koszt. |
|_Do góry_  |Koncentrować na najpopularniejsze zapytania — tymi wystawionymi przez klientów.
|_Brak_ |Został już przechwycony zestawu zapytania i przedziału czasu, który chcesz zbadać i chcesz wyeliminować rozpraszania, mogą powodować inne zapytania. _Brak_ jest odpowiednie do testowania i oznaczanie testów porównawczych środowisk. _Brak_ należy używać ostrożnie, ponieważ możesz pominąć możliwość śledzenia i zoptymalizować ważnych nowych zapytań. Nie można odzyskać dane na tych ostatnich okna czasowe. |

Query Store umożliwia także magazynu statystyki oczekiwania. Brak kwerendy trybu przechwytywania dodatkowe, które regulują statystyki oczekiwania: **pgms_wait_sampling.query_capture_mode** można ustawić _Brak_ lub _wszystkich_. 

> [!NOTE] 
> **pg_qs.query_capture_mode** zastępuje **pgms_wait_sampling.query_capture_mode**. Jeśli jest pg_qs.query_capture_mode _Brak_, ustawienie pgms_wait_sampling.query_capture_mode nie ma wpływu. 


## <a name="keep-the-data-you-need"></a>Zachowaj dane, których potrzebujesz
**Pg_qs.retention_period_in_days** parametru wyrażaną w dniach okres przechowywania danych dla zapytania Store. Starsze dane zapytań i statystyki są usuwane. Domyślnie Query Store jest skonfigurowany do przechowywania danych przez 7 dni. Unikaj przechowywania danych historycznych, który nie ma korzystać. Zwiększ wartość, jeśli chcesz przechowywać dane dłużej.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>Ustaw częstotliwość statystyki oczekiwania próbkowania 
**Pgms_wait_sampling.history_period** parametr określa, jak często (w milisekundach) oczekiwania zdarzenia są próbkowane. Krótszy okres, czyli bardziej częste odzyskiwanie pamięci pobierania próbek. Więcej informacji jest pobierana, ale która nauczenia się większe użycie zasobów. Zwiększ tego okresu, jeśli serwer jest obciążony lub nie ma potrzeby stopień szczegółowości


## <a name="get-quick-insights-into-query-store"></a>Uzyskaj szybki wgląd w zapytanie Store
Możesz użyć [Query Performance Insight](concepts-query-performance-insight.md) w witrynie Azure portal, aby uzyskać szybki wgląd w dane w Query Store. Wizualizacje powierzchni najdłuższy uruchomionych zapytań i najdłużej Zaczekaj zdarzenia, wraz z upływem czasu.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak pobrać lub ustawić parametry przy użyciu [witryny Azure portal](howto-configure-server-parameters-using-portal.md) lub [wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md).