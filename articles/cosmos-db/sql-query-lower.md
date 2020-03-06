---
title: NIŻSZY w Azure Cosmos DB języku zapytań
description: Zapoznaj się z NIŻSZą funkcją systemu SQL w Azure Cosmos DB, aby zwrócić wyrażenie ciągu po przekonwertowaniu wielkich liter na małe litery
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 80dba57d4fe05630eb5ae4f8fc96bd0aa214c6d4
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302274"
---
# <a name="lower-azure-cosmos-db"></a>NIŻSZY (Azure Cosmos DB)
 Zwraca wyrażenie ciągu po przekonwertowaniu danych znakowych wielkich liter na małe litery.  

NIŻSZA Funkcja systemowa nie korzysta z indeksu. Jeśli planujesz częste porównywanie bez uwzględniania wielkości liter, niższa Funkcja systemowa może zużywać znaczną ilość jednostek RU. W takim przypadku zamiast używać MNIEJSZEj funkcji systemowej do normalizacji danych za każdym razem w przypadku porównań, można znormalizować wielkość liter po wstawieniu. Następnie zapytanie takie jak SELECT * FROM c, gdzie LOWER (c. Name) = "Bob", po prostu wybiera * z c, gdzie c.name = "Bob".

## <a name="syntax"></a>Składnia
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie ciągu.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje, jak używać `LOWER` w zapytaniu.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
