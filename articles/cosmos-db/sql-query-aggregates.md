---
title: Funkcje agregujące w Azure Cosmos DB
description: Dowiedz się więcej na temat składni funkcji agregującej SQL, typów funkcji agregujących obsługiwanych przez Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: tisande
ms.openlocfilehash: 7c988f379e94bf2f69854c90d45af42fe2a7ec4f
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93332784"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Funkcje agregujące w Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Funkcje agregujące wykonują obliczenia na zestawie wartości w `SELECT` klauzuli i zwracają pojedynczą wartość. Na przykład następujące zapytanie zwraca liczbę elementów w `Families` kontenerze:

## <a name="examples"></a>Przykłady

```sql
    SELECT COUNT(1)
    FROM Families f
```

Wyniki są następujące:

```json
    [{
        "$1": 2
    }]
```

Możesz również zwrócić tylko wartość skalarną agregacji za pomocą słowa kluczowego VALUE. Na przykład poniższe zapytanie zwraca liczbę wartości jako pojedynczą liczbę:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Wyniki są następujące:

```json
    [ 2 ]
```

Można również łączyć agregacje z filtrami. Na przykład następujące zapytanie zwraca liczbę elementów ze stanem adresu `WA` .

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Wyniki są następujące:

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>Typy funkcji agregujących

Interfejs API SQL obsługuje następujące funkcje agregujące. `SUM` i `AVG` operować na wartościach liczbowych, i, `COUNT` `MIN` i `MAX` pracować na liczbach, ciągach, wartościach logicznych i wartościach null.

| Funkcja | Opis |
|-------|-------------|
| COUNT | Zwraca liczbę elementów w wyrażeniu. |
| SUM   | Zwraca sumę wszystkich wartości w wyrażeniu. |
| MIN   | Zwraca minimalną wartość w wyrażeniu. |
| MAX   | Zwraca maksymalną wartość w wyrażeniu. |
| AVG   | Zwraca średnią wartości w wyrażeniu. |

Można również agregować wyniki iteracji tablicy.

> [!NOTE]
> W Eksplorator danych Azure Portal zapytania agregacji mogą agregować częściowe wyniki tylko przez jedną stronę zapytania. Zestaw SDK tworzy pojedynczą wartość skumulowaną na wszystkich stronach. Aby wykonywać zapytania agregacji przy użyciu kodu, należy użyć zestawu .NET SDK 1.12.0, zestaw .NET Core SDK 1.1.0 lub zestawu Java SDK 1.9.5 lub nowszego.

## <a name="remarks"></a>Uwagi

Te zagregowane funkcje systemowe będą korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy). Jeśli oczekujesz,,, `COUNT` , `SUM` `MIN` `MAX` lub `AVG` na właściwość, należy [dołączyć odpowiednią ścieżkę do zasad indeksowania](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Funkcje systemowe](sql-query-system-functions.md)
- [Funkcje zdefiniowane przez użytkownika](sql-query-udfs.md)