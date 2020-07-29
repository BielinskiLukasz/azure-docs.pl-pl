---
title: Najlepsze rozwiązania w zakresie magazynu zapytań w Azure Database for PostgreSQL-pojedynczym serwerze
description: W tym artykule opisano najlepsze rozwiązania dotyczące magazynu zapytań w ramach Azure Database for PostgreSQL-jednego serwera.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 51239f4cf49784dd47470e1272b90508eaf25e6f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "70764231"
---
# <a name="best-practices-for-query-store"></a>Najlepsze rozwiązania dotyczące magazynu zapytań

**Dotyczy:** Azure Database for PostgreSQL — jeden serwer w wersji 9,6, 10, 11

W tym artykule opisano najlepsze rozwiązania dotyczące korzystania z magazynu zapytań w programie Azure Database for PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Ustawianie optymalnego trybu przechwytywania zapytania
Pozwól, aby Magazyn zapytań przechwytł dane. 

|**pg_qs. query_capture_mode** | **Scenariusz**|
|---|---|
|_Wszystko_  |Dokładne analizowanie obciążeń pod względem wszystkich zapytań i ich częstotliwości wykonywania oraz innych statystyk. Zidentyfikuj nowe zapytania w obciążeniu. Wykrywaj, czy zapytania ad hoc służą do identyfikowania możliwości użytkownika lub autoparametryzacja. _Wszystko_ to zapewnia zwiększony koszt zużycia zasobów. |
|_Pierwsze_  |Należy skoncentrować się na najważniejszych zapytaniach — tych, które są wystawiane przez klientów.
|_Brak_ |Przechwycono już zestaw zapytań i przedział czasu, które chcesz zbadać, i chcesz wyeliminować rozpraszanie, które mogą zostać wprowadzone przez inne zapytania. _Żadna nie_ jest odpowiednia do testowania i oznaczania na kanapie. _Nie_ należy stosować z zachowaniem ostrożności, ponieważ możesz pominąć szansę śledzenia i optymalizacji ważnych nowych zapytań. Nie można odzyskać danych w oknach, które przeszły czas. |

Magazyn zapytań zawiera również magazyn do statystyk oczekiwania. Istnieje dodatkowe zapytanie w trybie przechwytywania, które zarządza statystyką oczekiwania: **pgms_wait_sampling. query_capture_mode** można ustawić na _none_ lub _All_. 

> [!NOTE] 
> **pg_qs. query_capture_mode** zastępuje **pgms_wait_sampling. query_capture_mode**. Jeśli pg_qs. query_capture_mode ma _wartość None_, pgms_wait_sampling. query_capture_mode ustawienie nie ma żadnego wpływu. 


## <a name="keep-the-data-you-need"></a>Zachowaj potrzebne dane
Parametr **pg_qs. retention_period_in_days** określa w dniach okres przechowywania danych dla magazynu zapytań. Starsze dane dotyczące zapytań i statystyk są usuwane. Domyślnie magazyn zapytań jest skonfigurowany tak, aby dane były przechowywane przez 7 dni. Unikaj przechowywania danych historycznych, których nie planujesz używać. Zwiększ wartość, jeśli chcesz, aby dane były dłużej przechowywane.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>Ustaw częstotliwość próbkowania statystyk oczekiwania 
Parametr **pgms_wait_sampling. history_period** określa, jak często (w milisekundach) zdarzenia oczekiwania są próbkowane. Im krótszy okres, tym częstsze próbkowanie. Pobrano więcej informacji, ale jest to koszt większego zużycia zasobów. Zwiększ ten okres, jeśli serwer jest objęty obciążeniem lub nie potrzebujesz stopnia szczegółowości


## <a name="get-quick-insights-into-query-store"></a>Uzyskaj szybki wgląd w szczegółowe informacje na temat magazynu zapytań
Możesz użyć [szczegółowe informacje o wydajności zapytań](concepts-query-performance-insight.md) w Azure Portal, aby szybko uzyskać wgląd w dane w magazynie zapytań. Wizualizacje powierzchni są najdłuższymi uruchomionymi zapytaniami i najdłuższymi zdarzeniami oczekiwania w czasie.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak pobrać lub ustawić parametry za pomocą [Azure Portal](howto-configure-server-parameters-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md).