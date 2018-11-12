---
title: Przenoszenie danych do i z usługi Azure Blob Storage za pomocą łączników SSIS | Dokumentacja firmy Microsoft
description: Przenoszenie danych do i z usługi Azure Blob Storage za pomocą łączników SSIS.
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 96a1b5fb-34d1-4b9b-8d99-2bb8289e0398
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 2fe57480f7d8e22cfffafec6845372718587958e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258429"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Przenoszenie danych do i z usługi Azure Blob Storage za pomocą łączników SSIS
[Programu SQL Server Integration Services Feature Pack dla systemu Azure](https://msdn.microsoft.com/library/mt146770.aspx) zawiera składniki do połączenia z platformą Azure, transfer danych między Azure i lokalnych źródeł danych i przetwarzania danych przechowywanych na platformie Azure.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Gdy klienci przeniesione dane lokalne do chmury, są do niego dostęp z dowolnej usługi platformy Azure, aby korzystać z pełnego zestawu technologii platformy Azure. Może być używane, na przykład w usłudze Azure Machine Learning lub w klastrze usługi HDInsight.

Zazwyczaj jest to być pierwszym działaniem wykonywanym dla [SQL](sql-walkthrough.md) i [HDInsight](hive-walkthrough.md) wskazówki.

Omówienie canonical scenariuszy, w których korzystanie z usług SSIS do wykonywania potrzeb biznesowych, często używany w scenariusze integracji danych hybrydowych, zobacz [robić więcej przy użyciu programu SQL Server Integration Services Feature Pack dla systemu Azure](https://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) blogu.

> [!NOTE]
> Pełne wprowadzenie do usługi Azure blob storage, zapoznaj się [podstawowe informacje o usłudze Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) i [usługi Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
Aby wykonać zadania opisane w tym artykule, musi mieć subskrypcję platformy Azure i konto magazynu platformy Azure, skonfigurować. Musisz wiedzieć, aby usługa Azure storage konta nazwy i klucza konta usługi przekazywanie lub pobieranie danych.

* Aby skonfigurować **subskrypcji platformy Azure**, zobacz [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* Aby uzyskać instrukcje dotyczące tworzenia **konta magazynu** i zobacz, w celu uzyskania konta i informacje o kluczu, [kontach magazynu Azure o](../../storage/common/storage-create-storage-account.md).

Aby użyć **łączników SSIS**, należy pobrać:

* **SQL Server 2014 lub 2016 — wersje Standard (lub nowszy)**: instalacja obejmuje SQL Server Integration Services.
* **Microsoft SQL Server 2014 lub 2016 Integration Services Feature Pack dla systemu Azure**: te mogą być pobierane, odpowiednio, z [usługi integracji programu SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=47366) i [Integracja programu SQL Server 2016 Usługi](https://www.microsoft.com/download/details.aspx?id=49492) stron.

> [!NOTE]
> SSIS został zainstalowany przy użyciu programu SQL Server, ale nie znajduje się w wersji Express. Aby uzyskać informacji na temat aplikacji, które znajdują się w różnych wersjach programu SQL Server, zobacz [wersjach programu SQL Server](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

Materiały szkoleniowe na SSIS, zobacz [ręce na szkolenie dotyczące usług SSIS](https://www.microsoft.com/sql-server/training-certification)

Aby uzyskać informacje dotyczące sposobu uzyskania w górę i uruchomiona za pomocą SISS tworzyć proste wyodrębniania, przekształcania i ładowania (ETL) pakietów, zobacz [SSIS samouczek: Tworzenie prostego pakietu ETL](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Pobierz zestaw danych taksówek NYC
Przykładu opisanego w tym miejscu użyć publicznie dostępnego zestawu danych — [rund taksówek NYC](http://www.andresmh.com/nyctaxitrips/) zestawu danych. Zestaw danych składa się z około 173 milionów było taksówek w NYC w roku 2013. Istnieją dwa typy danych: podróży szczegóły danych i taryfy danych. Ponieważ ma pliku dla każdego miesiąca, mamy 24 pliki we wszystkich, z których każdy ma wielkość około 2GB, bez kompresji.

## <a name="upload-data-to-azure-blob-storage"></a>Przekazywanie danych do usługi Azure blob storage
Aby przenieść dane przy użyciu usług SSIS są wyposażone w pakiecie ze środowiska lokalnego do usługi Azure blob storage, możemy użyć wystąpienia programu [ **zadanie przekazywania obiektów Blob Azure**](https://msdn.microsoft.com/library/mt146776.aspx), pokazano poniżej:

![Konfigurowanie — — — maszyna wirtualna analizy danych](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

Parametry, które używa zadania są opisane poniżej:

| Pole | Opis |
| --- | --- |
| **AzureStorageConnection** |Określa istniejący Menedżer połączeń magazynu Azure lub utworzony zostaje nowy indeks, który odwołuje się do konta usługi Azure storage, który wskazuje, gdzie znajdują się pliki obiektów blob. |
| **BlobContainer** |Określa nazwę kontenera obiektów blob, które przekazane pliki jako obiekty BLOB. |
| **BlobDirectory** |Określa katalog obiektu blob przekazanego pliku jest przechowywania jako blokowe obiekty blob. Katalog obiektów blob znajduje się wirtualny hierarchicznej struktury. Jeśli istnieje już obiekt blob, it ia zastąpione. |
| **LocalDirectory** |Określa katalog lokalny, który zawiera pliki do przekazania. |
| **FileName** |Określa filtr nazw, aby wybrać pliki za pomocą wzorca określonej nazwy. Na przykład MySheet\*xls\* zawiera pliki, takie jak MySheet001.xls i MySheetABC.xlsx |
| **TimeRangeFrom/TimeRangeTo** |Określa filtr przedziału czasu. Pliki zmodyfikowane po *TimeRangeFrom* i przed *TimeRangeTo* są uwzględniane. |

> [!NOTE]
> **AzureStorageConnection** poświadczenia muszą być prawidłowe i **BlobContainer** musi istnieć przed próbą przeniesienia.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Pobierz dane z magazynu obiektów blob platformy Azure
Aby pobrać dane z usługi Azure blob storage do magazynu lokalnego przy użyciu funkcji SSIS, należy użyć wystąpienia [zadania pobierania obiektów Blob Azure](https://msdn.microsoft.com/library/mt146779.aspx).

## <a name="more-advanced-ssis-azure-scenarios"></a>Bardziej zaawansowanych scenariuszy SSIS Azure
Pakiet funkcji SSIS umożliwia bardziej złożonych przepływów, które mają być obsługiwane przez zadania tworzenia pakietów, razem. Na przykład danych obiektów blob można źródła danych bezpośrednio w klastrze usługi HDInsight, w której dane wyjściowe mogły zostać pobrane do obiektu blob, a następnie do lokalnego magazynu. SSIS można uruchamiać zadania Hive i Pig, w klastrze usługi HDInsight za pomocą łączników SSIS dodatkowe:

* Aby uruchomić skrypt programu Hive w klastrze usługi Azure HDInsight przy użyciu funkcji SSIS, należy użyć [zadań Hive HDInsight Azure](https://msdn.microsoft.com/library/mt146771.aspx).
* Aby uruchomić skrypt programu Pig w klastrze usługi Azure HDInsight przy użyciu funkcji SSIS, użyj [Azure HDInsight Pig zadań](https://msdn.microsoft.com/library/mt146781.aspx).

