---
title: Zainstaluj program Visual Studio 2019
description: Zainstaluj program Visual Studio i narzędzia deweloperskie SQL Server (SSDT) dla Synapse SQL
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/11/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: e18a3628a2fbb9eee248851f2295000fd1f82532
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027294"
---
# <a name="getting-started-with-visual-studio-2019"></a>Wprowadzenie do programu Visual Studio 2019

Visual Studio **2019** SQL Server Data Tools (SSDT) to pojedyncze narzędzie, które umożliwia wykonywanie następujących czynności:

- Łączenie, wykonywanie zapytań i opracowywanie aplikacji
- Skorzystaj z Eksploratora obiektów, aby wizualnie eksplorować wszystkie obiekty w modelu danych, w tym tabele, widoki, procedury składowane itd.
- Generowanie skryptów języka T-SQL Data Definition Language (DDL) dla obiektów
- Opracowywanie magazynu danych przy użyciu metody opartej na stanie z projektami bazy danych SSDT
- Zintegruj projekt bazy danych z systemami kontroli źródła, takimi jak Git, za pomocą Azure Repos
- Konfigurowanie potoków ciągłej integracji i wdrażania z serwerami automatyzacji, takimi jak Azure DevOps

## <a name="install-visual-studio-2019"></a>Zainstaluj program Visual Studio 2019

Zobacz [Pobieranie programu Visual studio 2019](https://visualstudio.microsoft.com/downloads/) , aby pobrać i zainstalować program visual Studio **16,3 lub nowszy**. Podczas instalacji wybierz obciążenie magazynu i przetwarzania danych. Instalacja autonomiczna SSDT nie jest już wymagana w programie Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Nieobsługiwane funkcje w programie SSDT

Istnieją przypadki, w których wersje funkcji dla programu Synapse SQL mogą nie zawierać obsługi SSDT. Następujące funkcje nie są obecnie obsługiwane:


- [Zarządzanie obciążeniami](sql-data-warehouse-workload-management.md) — grupy obciążeń i klasyfikatory
- [Zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
  - Prześlij [bilet pomocy technicznej lub zagłosuj](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) , aby uzyskać obsługiwaną funkcję.
- [Dynamiczne maskowanie danych](/sql/relational-databases/security/dynamic-data-masking?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#defining-a-dynamic-data-mask)
   - Prześlij [bilet pomocy technicznej lub zagłosuj](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) , aby uzyskać obsługiwaną funkcję.
- Tabele z [kolumną tożsamości](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property?view=sql-server-ver15)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz najnowszą wersję programu SSDT, możesz [nawiązać połączenie](sql-data-warehouse-query-visual-studio.md) z pulą SQL.
