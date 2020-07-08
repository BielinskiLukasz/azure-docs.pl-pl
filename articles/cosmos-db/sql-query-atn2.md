---
title: ATN2 w języku zapytań Azure Cosmos DB
description: Dowiedz się, w jaki sposób Funkcja systemowa SQL ATN2 w Azure Cosmos DB zwraca wartość główną tangensa łuku dla y/x, wyrażoną w radianach
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 696e14e75998ead04c99fab2b84fc4c742d5f54a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302665"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (Azure Cosmos DB)
 Zwraca wartość główną styczną ARC dla y/x, wyrażoną w radianach.  
  
## <a name="syntax"></a>Składnia
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Jest wyrażeniem liczbowym.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład oblicza ATN2 dla określonych składników x i y.  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
