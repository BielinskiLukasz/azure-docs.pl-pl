---
title: Konfigurowanie znaczenia obciążenia
description: Dowiedz się, jak ustawić ważność poziomu żądania w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 83170f4090909e3edcc163312383773d088d8c57
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85212126"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Konfigurowanie ważności obciążenia w usłudze Azure Synapse Analytics

Ustawienie znaczenia w Synapse SQL dla usługi Azure Synapse pozwala na wpływ na planowanie zapytań. Zapytania o wyższym znaczeniu zostaną zaplanowane do uruchomienia przed zapytaniami o niższej ważności. Aby przypisać ważność do zapytań, należy utworzyć klasyfikator obciążeń.

## <a name="create-a-workload-classifier-with-importance"></a>Tworzenie klasyfikatora obciążeń o ważności

Często w scenariuszu hurtowni danych masz użytkowników, którzy korzystają z systemu, który musi szybko uruchamiać swoje zapytania.  Użytkownik może być dyrektorem firmy, którzy muszą uruchamiać raporty lub użytkownik może być analitykiem, na którym działa zapytanie ad hoc. Aby przypisać ważność, należy utworzyć klasyfikator obciążeń i znaczenie jest przypisane do zapytania.  W poniższych przykładach użyto składni [klasyfikatora tworzenia obciążenia](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) , aby utworzyć dwa klasyfikatory. `Membername`może być pojedynczym użytkownikiem lub grupą.  Aby znaleźć istniejących użytkowników magazynu danych, uruchom polecenie:

```sql
Select name from sys.sysusers
```

Aby utworzyć klasyfikator obciążeń dla użytkownika o wyższej ważności:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = above_normal);
```

Aby utworzyć klasyfikator obciążeń dla użytkownika, na którym są wykonywane zapytania ad hoc z niższym przebiegiem ważności:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = below_normal);
```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat zarządzania obciążeniem, zobacz [Klasyfikacja obciążeń](sql-data-warehouse-workload-classification.md)
- Aby uzyskać więcej informacji na temat ważności, zobacz [ważność obciążenia](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Przejdź do pozycji Zarządzaj i monitoruj ważność obciążenia](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
