---
author: baanders
description: plik dołączany dla limitów cyfrowych bliźniaczych reprezentacji na platformie Azure
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 3035bd71a91f7cad6fb951d74081b77d8445a81f
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133878"
---
### <a name="functional-limits"></a>Limity funkcjonalne

W poniższej tabeli wymieniono funkcjonalne limity usługi Azure Digital bliźniaczych reprezentacji w bieżącej wersji zapoznawczej.

| Obszar | Możliwość | Limit domyślny | Wraz? |
| --- | --- | --- | --- |
| Zasób platformy Azure | Liczba wystąpień bliźniaczych reprezentacji cyfrowych platformy Azure w regionie, na subskrypcję | 10 | Tak |
| Digital bliźniaczych reprezentacji | Liczba bliźniaczych reprezentacji w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji | 200,000 | Tak |
| Routing | Liczba punktów końcowych dla pojedynczego wystąpienia usługi Azure Digital bliźniaczych reprezentacji | 6 | Nie |
| Routing | Liczba tras dla pojedynczego wystąpienia usługi Azure Digital bliźniaczych reprezentacji | 6 | Tak |
| Modele | Liczba modeli w pojedynczym wystąpieniu usługi Azure Digital bliźniaczych reprezentacji | 10 000 | Tak |
| Modele | Liczba modeli, które mogą być przekazane w jednym wywołaniu interfejsu API | 250 | Nie |
| Modele | Liczba elementów zwróconych na jednej stronie | 100 | Nie |
| Zapytanie | Liczba elementów zwróconych na jednej stronie | 100 | Nie |
| Zapytanie | Liczba `AND`  /  `OR` wyrażeń w zapytaniu | 50 | Tak |
| Zapytanie | Liczba elementów tablicy w `IN`  /  `NOT IN` klauzuli | 50 | Tak |
| Zapytanie | Liczba znaków w zapytaniu | 8000 | Tak |
| Zapytanie | Liczba `JOINS` w zapytaniu | 1 | Tak |

### <a name="rate-limits"></a>Limity szybkości

Ta tabela odzwierciedla limity szybkości różnych interfejsów API.

| Interfejs API | Możliwość | Limit domyślny | Wraz? |
| --- | --- | --- | --- |
| Interfejs API modeli | Liczba żądań na sekundę | 100 | Tak |
| Interfejs API Digital bliźniaczych reprezentacji | Liczba żądań na sekundę | 1000 | Tak |
| Interfejs API zapytań | Liczba żądań na sekundę | 500 | Tak |
| Interfejs API zapytań | Liczba jednostek zapytania na sekundę | 4000 | Tak |
| Interfejs API tras zdarzeń | Liczba żądań na sekundę | 100 | Tak |

### <a name="other-limits"></a>Inne limity

Limity dotyczące typów danych i pól w DTDL dokumentach dla modeli usługi Azure Digital bliźniaczych reprezentacji można znaleźć w dokumentacji dotyczącej specyfikacji w serwisie GitHub: [*Digital bliźniaczych reprezentacji Definition Language (DTDL) — wersja 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Szczegóły opóźnienia zapytania i inne wskazówki dotyczące pisania zapytań w trakcie korzystania z wersji zapoznawczej można znaleźć w temacie [*How to: Query The bliźniaczy Graf*](../articles/digital-twins/how-to-query-graph.md).