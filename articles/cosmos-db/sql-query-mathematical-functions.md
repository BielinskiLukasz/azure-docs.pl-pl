---
title: Funkcje matematyczne w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcjach matematycznych w usłudze Azure Cosmos DB, aby wykonać obliczenia, na podstawie wartości wejściowych, które są dostarczane jako argumenty, i zwrócić wartość liczbową.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a04867d356aaf2f55dbe900d2e35b42f74206851
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873271"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Funkcje matematyczne (Azure Cosmos DB)  

Każda z funkcji matematycznych wykonuje obliczenie na podstawie wartości wejściowych, które są podawane jako argumenty, i zwraca wartość liczbową.

Można uruchamiać kwerendy, takie jak w poniższym przykładzie:

```sql
    SELECT VALUE ABS(-4)
```

Rezultatem jest:

```json
    [4]
```

## <a name="functions"></a>Funkcje

Następujące obsługiwane wbudowane funkcje matematyczne wykonują obliczenia, zwykle oparte na argumentach wejściowych, i zwracają wyrażenie liczbowe.
  
||||  
|-|-|-|  
|[ABS](sql-query-abs.md)|[ACOS](sql-query-acos.md)|[ASIN](sql-query-asin.md)|  
|[ATAN](sql-query-atan.md)|[ATN2](sql-query-atn2.md)|[CEILING](sql-query-ceiling.md)|  
|[COS](sql-query-cos.md)|[COT](sql-query-cot.md)|[DEGREES](sql-query-degrees.md)|  
|[Exp](sql-query-exp.md)|[FLOOR](sql-query-floor.md)|[Dziennika](sql-query-log.md)|  
|[LOG10](sql-query-log10.md)|[PI](sql-query-pi.md)|[POWER](sql-query-power.md)|  
|[RADIANS](sql-query-radians.md)|[RAND](sql-query-rand.md)|[ROUND](sql-query-round.md)|
|[SIGN](sql-query-sign.md)|[Grzechu](sql-query-sin.md)|[SQRT](sql-query-sqrt.md)|
|[SQUARE](sql-query-square.md)|[TAN](sql-query-tan.md)|[TRUNC](sql-query-trunc.md)||  
  
Wszystkie funkcje matematyczne, z wyjątkiem funkcji LOS, są funkcjami deterministycznymi. Oznacza to, że zwracają te same wyniki za każdym razem, gdy są wywoływane z określonego zestawu wartości wejściowych.

## <a name="next-steps"></a>Następne kroki

- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Funkcje zdefiniowane przez użytkownika](sql-query-udfs.md)
- [Agregacje](sql-query-aggregates.md)
