---
title: Słowa kluczowe SQL dla Azure Cosmos DB
description: Dowiedz się więcej na temat słów kluczowych SQL dla Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: 711e961bd5eb1607e2e6f11b0b5762423d78c0e7
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898762"
---
# <a name="keywords-in-azure-cosmos-db"></a>Słowa kluczowe w Azure Cosmos DB
W tym artykule opisano słowa kluczowe, które mogą być używane w zapytaniach Azure Cosmos DB SQL.

## <a name="between"></a>ZAKRESU

Podobnie jak w przypadku języka ANSI SQL, można użyć słowa kluczowego BETWEEN do ekspresowych zapytań względem zakresów ciągu lub wartości liczbowych. Na przykład następujące zapytanie zwraca wszystkie elementy, w których Klasa pierwszego elementu podrzędnego to 1-5 włącznie.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

W przeciwieństwie do języka SQL ANSI, można również użyć klauzuli BETWEEN w klauzuli FROM, jak w poniższym przykładzie.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

W przypadku interfejsu SQL API, w przeciwieństwie do ANSI SQL, można wyznaczać zapytania zakresowe względem właściwości typów mieszanych. Na przykład `grade` może być liczbą, taką jak `5` w niektórych elementach, a ciąg, taki jak `grade4` w innych. W takich przypadkach, podobnie jak w języku JavaScript, porównanie między dwoma różnymi typami skutkuje `Undefined`, więc element zostanie pominięty.

> [!TIP]
> Aby przyspieszyć wykonywanie zapytań, Utwórz zasady indeksowania, które używają typu indeksu zakresu względem wszystkich właściwości liczbowych lub ścieżek, które są filtry klauzuli BETWEEN.

## <a name="distinct"></a>ITP

Słowo kluczowe DISTINCT eliminuje duplikaty w projekcji zapytania.

W tym przykładzie wartości projektów zapytania dla każdej nazwiska:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Wyniki są następujące:

```json
[
    "Andersen"
]
```

Możesz również projektować unikatowe obiekty. W takim przypadku pole lastName nie istnieje w jednym z dwóch dokumentów, więc zapytanie zwraca pusty obiekt.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Wyniki są następujące:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

Elementu DISTINCT można także użyć w projekcji w podzapytaniu:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

To zapytanie bada tablicę zawierającą wszystkie elementy podrzędne o podanym elemencie z usuniętymi duplikatami. Ta tablica jest aliasem jako ChildNames i jest rzutowana w zewnętrznym zapytaniu.

Wyniki są następujące:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

Zapytania z zagregowaną funkcją systemową i podzapytaniem z instrukcją DISTINCT nie są obsługiwane. Na przykład następujące zapytanie nie jest obsługiwane:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

Użyj słowa kluczowego IN, aby sprawdzić, czy określona wartość pasuje do dowolnej wartości na liście. Na przykład następujące zapytanie zwraca wszystkie elementy rodziny, w których `id` jest `WakefieldFamily` lub `AndersenFamily`.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

Poniższy przykład zwraca wszystkie elementy, w których stan jest dowolną z określonych wartości:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

Interfejs API SQL zapewnia obsługę [iteracji w tablicach JSON](sql-query-object-array.md#Iteration), a nowa konstrukcja dodana za pośrednictwem słowa kluczowego in w źródle from.

Jeśli klucz partycji zostanie uwzględniony w filtrze `IN`, zapytanie zostanie automatycznie przefiltrowane na odpowiednie partycje.

## <a name="top"></a>Do góry

Słowo kluczowe TOP zwraca pierwszą `N` liczbę wyników zapytania w niezdefiniowanej kolejności. Najlepszym rozwiązaniem jest użycie TOP z klauzulą ORDER BY, aby ograniczyć wyniki do pierwszej `N` liczbę uporządkowanych wartości. Połączenie tych dwóch klauzul jest jedynym sposobem przewidywania, które wiersze mają największe wpływ.

Możesz użyć TOP z wartością stałą, jak w poniższym przykładzie, lub z wartością zmiennej przy użyciu zapytań parametrycznych.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Wyniki są następujące:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>Następne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [Łącze](sql-query-join.md)
- [Podzapytania](sql-query-subquery.md)