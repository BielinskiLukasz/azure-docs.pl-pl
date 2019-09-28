---
title: StringToArray w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji StringToArray systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2d1f90da50950ac6ff4f87ffe96ebad9f3d811cc
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349282"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
 Zwraca wyrażenie tłumaczone na tablicę. Jeśli wyrażenia nie można przetłumaczyć, funkcja zwraca wartość undefined.  
  
## <a name="syntax"></a>Składnia
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu, które ma być analizowane jako wyrażenie tablicy JSON. 
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie tablicy lub niezdefiniowane. 
  
## <a name="remarks"></a>Uwagi
  Zagnieżdżone wartości ciągu muszą być zapisywane z podwójnymi cudzysłowami, aby były prawidłowymi wartościami JSON. Aby uzyskać szczegółowe informacje o formacie JSON, zobacz [JSON.org](https://json.org/)
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje, jak `StringToArray` zachowuje się w różnych typach. 
  
 Poniżej przedstawiono przykłady z prawidłowymi danymi wejściowymi.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

W tym miejscu znajduje się zestaw wyników.

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Poniżej przedstawiono przykład nieprawidłowych danych wejściowych. 
   
 Pojedyncze cudzysłowy w tablicy nie są prawidłowymi formatami JSON.
Mimo że są one prawidłowe w ramach zapytania, nie będą analizowane do prawidłowych tablic. Ciągi w ciągu tablicy muszą mieć wartość ucieczki "[\\" \\ "]" lub otaczające cudzysłowy muszą być pojedynczymi "[" "]".

```sql
SELECT
    StringToArray("['5','6','7']")
```

W tym miejscu znajduje się zestaw wyników.

```json
[{}]
```

Poniżej przedstawiono przykłady nieprawidłowych danych wejściowych.
   
 Przesłane wyrażenie zostanie przeanalizowane jako tablica JSON; następujące elementy nie są oceniane do typu Array i w ten sposób zwracają wartość undefined.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

W tym miejscu znajduje się zestaw wyników.

```json
[{}]
```

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
