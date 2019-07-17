---
title: Przyrostowe kopiowanie danych przy użyciu usługi Azure Data Factory | Microsoft Docs
description: W tych samouczkach przedstawiono sposób przyrostowego kopiowania danych ze źródłowego magazynu danych do docelowego magazynu danych. W pierwszym z nich opisane jest kopiowanie danych z jednej tabeli.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: 87b5b30738451800da21736d7f139c4ba85ff998
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233696"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Przyrostowe ładowanie danych ze źródłowego do docelowego magazynu danych

W rozwiązaniu integracji danych przyrostowe (lub różnicowe) ładowanie danych po początkowym pełnym ładowaniu danych to powszechnie używany scenariusz. W samouczkach w tej sekcji opisano różne sposoby przyrostowego ładowania danych przy użyciu usługi Azure Data Factory.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Ładowanie danych różnicowych z bazy danych przy użyciu limitu
W takim przypadku należy zdefiniować limit w źródłowej bazie danych. Limit to kolumna, która zawiera znacznik czasu ostatniej aktualizacji lub klucz o zwiększającej się wartości. Rozwiązanie ładowania różnicowego służy do ładowania zmienionych danych między starą a nową wartością limitu. Na poniższym diagramie przedstawiono przepływ pracy dla tej metody: 

![Przepływ pracy dotyczący używania limitu](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Aby uzyskać instrukcje krok po kroku, zobacz następujące samouczki: 
- [Przyrostowe kopiowanie danych z jednej tabeli w bazie danych Azure SQL Database do magazynu Azure Blob Storage](tutorial-incremental-copy-powershell.md)
- [Przyrostowe kopiowanie danych z wielu tabel na lokalnym serwerze SQL Server do bazy danych Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md)

Dla szablonów zobacz następujące tematy:
- [Kopiowanie zmian za pomocą kontrolki tabeli](solution-template-delta-copy-with-control-table.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Ładowanie danych różnicowych z bazy danych SQL przy użyciu technologii Change Tracking
Technologia Change Tracking to rozwiązanie o małych wymaganiach w programie SQL Server i bazie danych Azure SQL Database, które zapewnia wydajny mechanizm śledzenia zmian na potrzeby aplikacji. Dzięki temu w aplikacji można łatwo zidentyfikować dane, które zostały wstawione, zaktualizowane lub usunięte. 

Na poniższym diagramie przedstawiono przepływ pracy dla tej metody:

![Przepływ pracy dotyczący używania technologii Change Tracking](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Aby uzyskać instrukcje krok po kroku, zobacz następujące samouczki: <br/>
- [Przyrostowe kopiowanie danych z bazy danych Azure SQL Database do magazynu Azure Blob Storage z użyciem technologii Change Tracking](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>Ładowanie nowych i zmienionych plików tylko przy użyciu parametru LastModifiedDate
Nowe i zmienione pliki można kopiować tylko przy użyciu Data ostatniej modyfikacji do docelowego magazynu. ADF skanowanie wszystkich plików z magazynu źródła, zastosuj filtr pliku przez ich Data ostatniej modyfikacji i tylko skopiuj plik na nowe i zaktualizowane od czasu ostatniego magazynu docelowego.  Należy pamiętać, jeśli umożliwiają ADF skanowania ogromne ilości plików, ale kopiować tylko kilka plików do lokalizacji docelowej, można nadal oczekiwać także czas trwania długiej ze względu na skanowanie plików jest czasochłonne.   

Aby uzyskać instrukcje krok po kroku, zobacz następujące samouczki: <br/>
- [Przyrostowe kopiowanie nowych i zmienionych plików na podstawie parametru LastModifiedDate z usługi Azure Blob Storage do usługi Azure Blob Storage](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

Dla szablonów zobacz następujące tematy:
- [Kopiuj nowe pliki, Data ostatniej modyfikacji](solution-template-copy-new-files-lastmodifieddate.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>Załaduj nowe pliki tylko za pomocą partycjonowanego na podstawie czasu folderu lub nazwy pliku.
Możesz kopiować tylko nowe pliki, gdy pliki lub foldery zostały już poddane partycjonowaniu na podstawie czasu za pomocą informacji o kwancie czasu jako części nazwy pliku lub folderu (na przykład /rrrr/mm/dd/plik.csv). Jest to większość podejście wydajności ładowania przyrostowego nowych plików. 

Aby uzyskać instrukcje krok po kroku, zobacz następujące samouczki: <br/>
- [Przyrostowe kopiowanie nowych plików na podstawie partycjonowanego na podstawie czasu folderu lub nazwy pliku z usługi Azure Blob Storage do usługi Azure Blob Storage](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>Następne kroki
Przejdź do następującego samouczka: 

> [!div class="nextstepaction"]
>[Przyrostowe kopiowanie danych z jednej tabeli w bazie danych Azure SQL Database do magazynu Azure Blob Storage](tutorial-incremental-copy-powershell.md)
