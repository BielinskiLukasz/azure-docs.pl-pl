---
title: Optymalizowanie pamięci podręcznej Gen2
description: Dowiedz się, jak monitorować pamięć podręczną Gen2 przy użyciu Azure Portal.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: performance
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 257b33802ea95138ef8149ef9302b14ca379e7ac
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195572"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Jak monitorować pamięć podręczną Gen2

W tym artykule opisano sposób monitorowania i rozwiązywania problemów z niską wydajnością zapytań przez określenie, czy obciążenie jest optymalnie używane w pamięci podręcznej Gen2.

Architektura magazynu Gen2 automatycznie warstwuje najczęściej badane segmenty magazynu kolumn w pamięci podręcznej na podstawie dysków SSD opartych na interfejsie NVMe, które są przeznaczone dla Gen2 magazynów danych. Zwiększenie wydajności jest realizowane, gdy zapytania pobierają segmenty znajdujące się w pamięci podręcznej.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Rozwiązywanie problemów przy użyciu Azure Portal

Za pomocą Azure Monitor można wyświetlać metryki pamięci podręcznej Gen2 w celu rozwiązywania problemów z wydajnością zapytań. Najpierw przejdź do Azure Portal i kliknij pozycję **monitor**, **metryki** i **Wybierz zakres**:

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache-0.png)

Użyj słupków wyszukiwania i listy rozwijanej, aby zlokalizować magazyn danych. Następnie wybierz pozycję Zastosuj.

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache-1.png)

Kluczowe metryki dotyczące rozwiązywania problemów z pamięcią podręczną Gen2 są **procentami trafień pamięci podręcznej** i **procentowo używane**. Wybierz pozycję **Procent trafień pamięci podręcznej** , a następnie użyj przycisku **Dodaj metrykę** , aby dodać **procent użycia pamięci podręcznej** 

![Metryki pamięci podręcznej](./media/sql-data-warehouse-cache-portal/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>Procent trafień i użycia pamięci podręcznej

W poniższej macierzy opisano scenariusze oparte na wartościach metryk pamięci podręcznej:

|                                | **Wysoki procent trafień w pamięci podręcznej** | **Procent trafień niskiego poziomu pamięci podręcznej** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Procent użycia dużej pamięci podręcznej** |          Scenariusz 1           |          Scenariusz 2          |
| **Procent użycia niskiej pamięci podręcznej**  |          Scenariusz 3           |          Scenariusz 4          |

**Scenariusz 1:** Optymalnie korzystasz z pamięci podręcznej. [Rozwiązywanie problemów z](sql-data-warehouse-manage-monitor.md) innymi obszarami, które mogą spowalniać zapytania.

**Scenariusz 2:** Bieżący roboczy zestaw danych nie mieści się w pamięci podręcznej, co powoduje niską wartość procentową trafień pamięci podręcznej z powodu fizycznych operacji odczytu. Rozważ skalowanie w górę poziomu wydajności i ponowne uruchomienie obciążenia w celu wypełnienia pamięci podręcznej.

**Scenariusz 3:** Prawdopodobnie zapytanie działa wolno ze względu na przyczyny niezwiązane z pamięcią podręczną. [Rozwiązywanie problemów z](sql-data-warehouse-manage-monitor.md) innymi obszarami, które mogą spowalniać zapytania. Możesz również rozważyć [skalowanie wystąpienia](sql-data-warehouse-manage-monitor.md) , aby zmniejszyć rozmiar pamięci podręcznej w celu oszczędności kosztów. 

**Scenariusz 4:** Masz zimną pamięć podręczną, która może być przyczyną powolnego działania zapytania. Rozważ ponowne uruchomienie zapytania, ponieważ roboczy zestaw danych powinien teraz znajdować się w pamięci podręcznej. 

> [!IMPORTANT]
> Jeśli procent trafień pamięci podręcznej lub procent użycia pamięci podręcznej nie jest aktualizowany po rozpoczęciu obciążenia, zestaw roboczy może już znajdować się w pamięci. Tylko klastrowane tabele magazynu kolumn są buforowane.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat ogólnego dostrajania wydajności zapytań, zobacz [monitorowanie wykonywania zapytań](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md#monitor-query-execution).
