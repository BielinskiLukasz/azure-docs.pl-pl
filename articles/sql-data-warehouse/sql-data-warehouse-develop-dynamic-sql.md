---
title: Za pomocą dynamiczny język SQL w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Porady dotyczące używania dynamiczny język SQL w usłudze Azure SQL Data Warehouse do opracowywania rozwiązań.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 6793fba1476595918ac20c0484a661e3af7897d7
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43247304"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Dynamiczny język SQL w usłudze SQL Data Warehouse
Porady dotyczące używania dynamiczny język SQL w usłudze Azure SQL Data Warehouse do opracowywania rozwiązań.

## <a name="dynamic-sql-example"></a>Przykład dynamiczny język SQL

Podczas tworzenia kodu aplikacji dla usługi SQL Data Warehouse, może być konieczne Użyj dynamiczny język sql, aby dostarczać rozwiązania, elastyczna, ogólnego i moduły. Usługa SQL Data Warehouse nie obsługuje typów danych obiektów blob w tym momencie. Nie obsługuje typów danych obiektów blob może być ograniczenie rozmiaru Twoimi ciągami, ponieważ typy danych obiektów blob obejmują typy varchar(max) i nvarchar(max). Jeśli używasz tych typów w kodzie aplikacji do tworzenia dużych ciągów, musisz podział kodu na fragmenty i zamiast tego użyj instrukcji EXEC.

Prosty przykład:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Jeśli ciąg jest krótkim, możesz użyć [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) w zwykły sposób.

> [!NOTE]
> Instrukcje wykonywane jako dynamiczny język SQL będą nadal podlegać wszystkie reguły sprawdzania poprawności TSQL.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej porad programistycznych, zobacz [omówienie programowania w usłudze](sql-data-warehouse-overview-develop.md).

