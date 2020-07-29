---
title: Używanie etykiet do Instrumentacji zapytań
description: Porady dotyczące używania etykiet do Instrumentacji zapytań w puli SQL Synapse na potrzeby tworzenia rozwiązań.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d4459547300f5dfc7b7c22d1e531b928a13aa66c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213435"
---
# <a name="using-labels-to-instrument-queries-in-synapse-sql-pool"></a>Używanie etykiet do Instrumentacji zapytań w puli SQL Synapse

W tym artykule przedstawiono wskazówki dotyczące opracowywania rozwiązań przy użyciu etykiet do Instrumentacji zapytań w puli SQL.

Porady dotyczące używania etykiet do Instrumentacji zapytań w Azure SQL Data Warehouse do tworzenia rozwiązań.

## <a name="what-are-labels"></a>Co to są etykiety?

Pula SQL obsługuje koncepcję o nazwie etykiety zapytań. Przed przejściem do dowolnej głębokości Przyjrzyjmy się przykładowi:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Ostatni wiersz określa ciąg "My Query Label" w zapytaniu. Ten tag jest przydatny, ponieważ etykieta jest z możliwością zapytania przez widoków DMV.

Wykonywanie zapytań dotyczących etykiet zapewnia mechanizm lokalizowania zapytań o problemy oraz ułatwiający identyfikację postępu przez uruchomienie ELT.

Dobrą konwencją nazewnictwa są naprawdę pomocne. Na przykład uruchomienie etykiety z projektem, procedurą, INSTRUKCJą lub KOMENTARZEm jednoznacznie identyfikuje zapytanie między całym kodem w kontroli źródła.

Następujące zapytanie używa dynamicznego widoku zarządzania do wyszukiwania według etykiety:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Podczas wykonywania zapytań należy umieścić nawiasy kwadratowe lub podwójne cudzysłowy wokół etykiety wyrazu. Etykieta jest słowem zastrzeżonym i powoduje błąd, gdy nie zostanie on rozdzielony.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej porad programistycznych, zobacz [Omówienie projektowania](sql-data-warehouse-overview-develop.md).
