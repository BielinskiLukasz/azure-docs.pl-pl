---
title: Omówienie szablonów usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak szybko rozpocząć pracę z usługą Azure Data Factory za pomocą wstępnie zdefiniowanych szablonów.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: c3571aec55d5050df01f740a163db18e9c6c1095
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967618"
---
# <a name="templates"></a>Szablony

Szablony to wstępnie zdefiniowane potoki usługi Azure Data Factory, które pozwalają na szybkie rozpoczynanie pracy z usługą Data Factory. Szablony są przydatne, jeśli jesteś nowym użytkownikiem usługi Data Factory i chcesz szybko rozpocząć pracę. Te szablony skrócić czas projektowania do tworzenia projekty integracji danych, a tym samym poprawę produktywności deweloperów.

## <a name="create-data-factory-pipelines-from-templates"></a>Tworzenie potoków usługi Data Factory przy użyciu szablonów

Pracę można rozpocząć tworzenia potoku usługi fabryka danych na podstawie szablonu w następujących dwóch sposobów:

1.  Wybierz **Utwórz potok z szablonu** na stronie przeglądu, aby otworzyć galerię szablonów.

    ![Otwórz galerię szablonów ze strony Przegląd](media/solution-templates-introduction/templates-intro-image1.png)

1.  Na karcie autora w Eksploratorze zasobów wybierz **+**, następnie **potoku za pomocą szablonu** do otwierania galerii szablonów.

    ![Otwórz galerię szablonów z karty autora](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Galeria szablonów

![Galeria szablonów](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Poza pole szablony usługi Data Factory

Data Factory przy użyciu szablonów usługi Azure Resource Manager do zapisywania szablony potoku fabryki danych. Możesz zobaczyć wszystkie szablony usługi Resource Manager, wraz z plikiem manifestu, używane poza pole szablony usługi Data Factory, w [oficjalne repozytorium Azure Data Factory w witrynie Github](https://github.com/Azure/Azure-DataFactory/tree/master/templates). Wstępnie zdefiniowanych szablonów, które są obsługiwane przez firmę Microsoft obejmują, ale nie są ograniczone do następujących elementów:

-   Skopiuj szablony:

    -   [Zbiorcze kopiowanie danych z bazy danych](solution-template-bulk-copy-with-control-table.md)

    -   [Kopiowanie wielu kontenerów plików między magazynami oparte na plikach](solution-template-copy-files-multiple-containers.md)

    -   [Kopiowanie danych różnicowych z bazy danych](solution-template-delta-copy-with-control-table.md)

    -   Skopiuj z \<źródła\> do \<docelowego\>

        -   Z usługi Amazon S3 do usługi Azure Data Lake Store Gen 2

        -   Google Big zapytania dla usługi Azure Data Lake Store Gen 2

        -   Z HDF do usługi Azure Data Lake Store Gen 2

        -   Z Netezza do usługi Azure Data Lake Store Gen 1

        -   Z programu SQL Server w środowisku lokalnym, do usługi Azure SQL Database

        -   Z programu SQL Server w środowisku lokalnym, do usługi Azure SQL Data Warehouse

        -   Od firmy Oracle w środowisku lokalnym, do usługi Azure SQL Data Warehouse

-   Szablony usług SSIS

    -   Harmonogram środowiska Azure-SSIS Integration Runtime umożliwia wykonywanie pakietów usług SSIS

-   Przekształcanie szablonów

    -   [ETL za pomocą usługi Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Moje szablony

Potok może Zapisz jako szablon, wybierając **Zapisz jako szablon** na karcie potoku.

![Zapisz potoku jako szablon](media/solution-templates-introduction/templates-intro-image4.png)

Możesz wyświetlić potoki zapisywane jako szablony **Moje szablony** sekcji galerii szablonów. Można również wyświetlić je w **szablony** sekcji w Eksploratorze zasobów.

![Moje szablony](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Aby użyć funkcji Moje szablony, należy włączyć integrację z usługą GIT. Obsługiwane są zarówno GIT DevOps platformy Azure i usługi GitHub.
