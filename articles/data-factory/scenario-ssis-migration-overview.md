---
title: Migrowanie lokalnych obciążeń usług SSIS do usług SSIS w Azure Data Factory
description: Migrowanie lokalnych obciążeń usług SSIS do usług SSIS w podajniku ADF.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 52629b8e2e190cc041116e6f65488480712baf01
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929805"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Migrowanie lokalnych obciążeń usług SSIS do usług SSIS w podajniku APD

## <a name="overview"></a>Przegląd

W przypadku migrowania obciążeń bazy danych z SQL Server lokalnych do usług Azure Database Services, mianowicie Azure SQL Database lub Azure SQL Database wystąpienia zarządzanego, obciążenia ETL na SQL Server Integration Services (SSIS) jako jedną z wartości podstawowych — dodano należy także przeprowadzić migrację usług.

Azure-SSIS Integration Runtime (IR) w Azure Data Factory (ADF) obsługuje uruchamianie pakietów usług SSIS. Po zainicjowaniu Azure-SSIS IR można używać znanych narzędzi, takich jak SQL Server narzędzia do obsługi danych (SSDT)/SQL Server Management Studio (SSMS) oraz narzędzi wiersza polecenia, takich jak dtinstall/dtutil/dtexec, aby wdrażać i uruchamiać pakiety na platformie Azure. Aby uzyskać więcej informacji, zobacz temat [usługa Azure SSIS Wind-and-Shift — Omówienie](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

W tym artykule przedstawiono proces migracji obciążeń ETL z lokalnych usług SSIS do usług SSIS w podajniku ADF. Proces migracji składa się z dwóch faz: **oceny** i **migracji**.

## <a name="assessment"></a>Ocena

Aby ustanowić Kompletny plan migracji, gruntowna Ocena pomoże zidentyfikować problemy ze źródłowym pakietem SSIS, które uniemożliwią pomyślne przeprowadzenie migracji.

Data Migration Assistant (DMA) to bezpłatnie dostępne narzędzie do pobrania, które można zainstalować i uruchomić lokalnie. Projekt oceny DMA typów **usług Integration Services** można utworzyć w celu oceny pakietów SSIS w partiach i zidentyfikowania problemów ze zgodnością, które są przedstawione w następujących kategoriach:

- Blokowanie migracji: są to problemy ze zgodnością, które blokują uruchamianie pakietów źródłowych migracji na Azure-SSIS IR. Dostęp DMA zawiera wskazówki ułatwiające rozwiązywanie tych problemów.

- Problemy informacyjne: są to częściowo obsługiwane lub przestarzałe funkcje, które są używane w pakietach źródłowych. Usługa DMA zapewnia kompleksowy zestaw zaleceń, alternatywne podejścia dostępne na platformie Azure i ograniczanie czynności do rozwiązania.

### <a name="four-storage-types-for-ssis-packages"></a>Cztery typy magazynów dla pakietów SSIS

- Katalog SSIS (SSISDB). Wprowadzono ją w SQL Server 2012 i zawiera zestaw procedur składowanych, widoków i funkcji zwracających tabele służących do pracy z projektami/pakietami SSIS.
- File System.
- Baza danych systemu SQL Server (MSDB).
- Magazyn pakietów SSIS. To jest warstwa zarządzania pakietami na dwóch podtypów:
  - MSDB, która jest systemową bazą danych w SQL Server używany do przechowywania pakietów SSIS.
  - Zarządzany system plików, który jest określonym folderem w ścieżce instalacji SQL Server używany do przechowywania pakietów SSIS.

W usłudze DMA jest obecnie obsługiwana Ocena partii pakietów przechowywanych w **systemie plików**, **magazynie pakietów**i **katalogu usług SSIS** od czasu **DMA w wersji 2.0**.

Uzyskaj [dostęp DMA](https://docs.microsoft.com/sql/dma/dma-overview)i [Przeprowadź ocenę pakietu](https://docs.microsoft.com/sql/dma/dma-assess-ssis).

## <a name="migration"></a>Migracja

W zależności od [typów magazynu](#four-storage-types-for-ssis-packages) źródłowych pakietów SSIS i lokalizacji docelowej migracji obciążeń bazy danych, kroki służące do migracji **pakietów SSIS** i **SQL Server zadania agenta** , które zaplanują wykonywanie pakietów SSIS. Istnieją dwa scenariusze:

- [**Azure SQL Database wystąpienie zarządzane** jako miejsce docelowe obciążenia bazy danych](#azure-sql-database-managed-instance-as-database-workload-destination)
- [**Azure SQL Database** jako miejsce docelowe obciążeń bazy danych](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-database-managed-instance-as-database-workload-destination"></a>**Azure SQL Database wystąpienie zarządzane** jako miejsce docelowe obciążenia bazy danych

| **Typ magazynu pakietów** |Jak przeprowadzić migrację wsadową pakietów SSIS|Jak przeprowadzić migrację wsadową zadań SSIS|
|-|-|-|
|SSISDB|[Migruj **SSISDB**](scenario-ssis-migration-ssisdb-mi.md)|[Migrowanie zadań SSIS do Azure SQL Database agenta wystąpienia zarządzanego](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-azure-sql-database-managed-instance-agent)|
|System plików|Wdróż je ponownie w udziałach plików/Azure Files za pośrednictwem dtinstall/dtutil/Manual Copy lub aby zachować dostęp do systemów plików za pośrednictwem sieci wirtualnej/samoobsługowego środowiska IR. Aby uzyskać więcej informacji, zobacz [Narzędzie dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Przekonwertuj je na potoki ADF/działania/wyzwalacze za pośrednictwem skryptów/narzędzia SSMS/ADF Portal. Aby uzyskać więcej informacji, zobacz [Funkcja planowania programu SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Wyeksportuj je do systemów plików/udziałów plików/Azure Files za pośrednictwem programu SSMS/dtutil. Aby uzyskać więcej informacji, zobacz [Eksportowanie pakietów SSIS](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service).|Przekonwertuj je na potoki ADF/działania/wyzwalacze za pośrednictwem skryptów/narzędzia SSMS/ADF Portal. Aby uzyskać więcej informacji, zobacz [Funkcja planowania programu SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Magazyn pakietów|Wyeksportuj je do systemów plików/udziałów plików/Azure Files za pomocą narzędzia SSMS/dtutil lub wdróż je ponownie w udziałach plików/Azure Files za pośrednictwem dtinstall/dtutil/ręcznie Skopiuj lub Zachowaj dostęp do systemów plików za pośrednictwem sieci wirtualnej/samoobsługowego środowiska IR. Aby uzyskać więcej informacji, zobacz Narzędzie dtutil. Aby uzyskać więcej informacji, zobacz [Narzędzie dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Przekonwertuj je na potoki ADF/działania/wyzwalacze za pośrednictwem skryptów/narzędzia SSMS/ADF Portal. Aby uzyskać więcej informacji, zobacz [Funkcja planowania programu SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Azure SQL Database** jako miejsce docelowe obciążeń bazy danych

| **Typ magazynu pakietów** |Jak przeprowadzić migrację wsadową pakietów SSIS|Jak przeprowadzić migrację wsadową zadań|
|-|-|-|
|SSISDB|Wdróż ponownie na platformie Azure — SSISDB za pośrednictwem SSDT/SSMS. Aby uzyskać więcej informacji, zobacz [wdrażanie pakietów usług SSIS na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial).|Przekonwertuj je na potoki ADF/działania/wyzwalacze za pośrednictwem skryptów/narzędzia SSMS/ADF Portal. Aby uzyskać więcej informacji, zobacz [Funkcja planowania programu SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|System plików|Wdróż je ponownie w udziałach plików/Azure Files za pośrednictwem dtinstall/dtutil/Manual Copy lub aby zachować dostęp do systemów plików za pośrednictwem sieci wirtualnej/samoobsługowego środowiska IR. Aby uzyskać więcej informacji, zobacz [Narzędzie dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Przekonwertuj je na potoki ADF/działania/wyzwalacze za pośrednictwem skryptów/narzędzia SSMS/ADF Portal. Aby uzyskać więcej informacji, zobacz [Funkcja planowania programu SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Wyeksportuj je do systemów plików/udziałów plików/Azure Files za pośrednictwem programu SSMS/dtutil. Aby uzyskać więcej informacji, zobacz [Eksportowanie pakietów SSIS](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service).|Przekonwertuj je na potoki ADF/działania/wyzwalacze za pośrednictwem skryptów/narzędzia SSMS/ADF Portal. Aby uzyskać więcej informacji, zobacz [Funkcja planowania programu SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Magazyn pakietów|Wyeksportuj je do systemów plików/udziałów plików/Azure Files za pomocą narzędzia SSMS/dtutil lub wdróż je ponownie w udziałach plików/Azure Files za pośrednictwem dtinstall/dtutil/ręcznie Skopiuj lub Zachowaj dostęp do systemów plików za pośrednictwem sieci wirtualnej/samoobsługowego środowiska IR. Aby uzyskać więcej informacji, zobacz Narzędzie dtutil. Aby uzyskać więcej informacji, zobacz [Narzędzie dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Przekonwertuj je na potoki ADF/działania/wyzwalacze za pośrednictwem skryptów/narzędzia SSMS/ADF Portal. Aby uzyskać więcej informacji, zobacz [Funkcja planowania programu SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Asystent migracji bazy danych](https://docs.microsoft.com/sql/dma/dma-overview)
- [Podnieś i Przenieś obciążenia SSIS do chmury](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [Migrowanie pakietów SSIS do Azure SQL Database wystąpienia zarządzanego](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [Wdróż ponownie pakiety do Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>Następne kroki

- [Weryfikowanie pakietów usług SSIS wdrożonych na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Uruchamianie pakietów SSIS wdrożonych na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Monitorowanie środowiska Azure SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [Zaplanuj wykonywanie pakietów SSIS na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
