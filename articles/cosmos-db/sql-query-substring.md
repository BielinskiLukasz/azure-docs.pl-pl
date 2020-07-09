---
title: Podciąg w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej o podciągu funkcji systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d4462fc407093b23510bddfae4d9f55d68f8c0fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "78303702"
---
# <a name="substring-azure-cosmos-db"></a>Podciąg (Azure Cosmos DB)
 Zwraca część wyrażenia ciągu rozpoczynającą się od określonego znaku na podstawie zera i kontynuuje określoną długość lub na końcu ciągu.  
  
## <a name="syntax"></a>Składnia
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu.
  
*num_expr1*  
   Jest wyrażeniem liczbowym służącym do określenia znaku początkowego. Wartość 0 jest pierwszym znakiem *str_expr*.
  
*num_expr2*  
   Jest wyrażeniem liczbowym określającym maksymalną liczbę znaków *str_expr* do zwrócenia. Wartość 0 lub mniej skutkuje ciągiem pustym.

## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie ciągu.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca podciąg "ABC" zaczynający się od 1 i o długości 1 znaku.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy) , jeśli pozycja początkowa jest `0` .

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
