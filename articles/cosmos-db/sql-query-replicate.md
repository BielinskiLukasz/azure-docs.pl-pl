---
title: Replikuj w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej na temat replikacji funkcji systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 19fcde522c5cb0355e53a5616145f27fada7dad9
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302189"
---
# <a name="replicate-azure-cosmos-db"></a>REPLIKACJA (Azure Cosmos DB)
 Powtarza wartość ciągu określoną liczbę razy.
  
## <a name="syntax"></a>Składnia
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu.
  
*num_expr*  
   To wyrażenie liczbowe. Jeśli *num_expr* jest ujemna lub nieograniczona, wynik jest niezdefiniowany.
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie ciągu.
  
## <a name="remarks"></a>Uwagi
  Maksymalna długość wyniku to 10 000 znaków (długość (*str_expr*) * *num_expr*) < = 10 000.

## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje, jak używać `REPLICATE` w zapytaniu.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Tutaj znajduje się zestaw wyników.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
