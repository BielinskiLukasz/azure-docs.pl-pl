---
title: ASIN w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o tym, jak Funkcja systemowa SQL ASIN (sinus) w Azure Cosmos DB zwraca kąt w radianach, którego sinus jest określonym wyrażeniem liczbowym
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b70738a439b6c64a84a63adf63c83995530e92e
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302699"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
 Zwraca kąt w radianach, którego sinus jest określonym wyrażeniem liczbowym. Jest to również nazywane arcus sinus.  
  
## <a name="syntax"></a>Składnia
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   To wyrażenie liczbowe.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość wyrażenia liczbowego.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca `ASIN`-1.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
