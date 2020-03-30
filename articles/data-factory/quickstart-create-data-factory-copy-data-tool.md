---
title: Kopiowanie danych przy użyciu narzędzia Azure Copy Data
description: Utwórz fabrykę danych platformy Azure, a następnie za pomocą narzędzia do kopiowania danych skopiuj dane z jednej lokalizacji w usłudze Azure Blob Storage do innej lokalizacji.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 03/18/2020
ms.openlocfilehash: 26169755fbe252a4be2626dae50d40c005c7c6db
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80130924"
---
# <a name="quickstart-use-the-copy-data-tool-to-copy-data"></a>Szybki start: kopiowanie danych za pomocą narzędzia Kopiuj dane

> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Bieżąca wersja](quickstart-create-data-factory-copy-data-tool.md)

W tym przewodniku Szybki start utworzysz fabrykę danych przy użyciu witryny Azure Portal. Następnie za pomocą narzędzia do kopiowania danych utworzysz potok służący do kopiowania danych z folderu w magazynie Azure Blob Storage do innego folderu. 

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, przed wykonaniem kroków zawartych w tym przewodniku Szybki start zobacz [Wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md). 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
1. Przejdź do [witryny Azure portal](https://portal.azure.com). 
1. Z menu portalu platformy Azure wybierz pozycję **Utwórz fabrykę** > **danych****analizy** > zasobów:

    ![Tworzenie nowej fabryki danych](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Na stronie **Nowa fabryka danych** wprowadź wartość **ADFTutorialDataFactory** w polu **Nazwa**. 
 
   Nazwa fabryki danych platformy Azure musi być *unikatowa globalnie.* Jeśli widzisz następujący błąd, zmień nazwę fabryki danych (na przykład ** &lt;nazwę&gt;ADFTutorialDataFactory)** i spróbuj utworzyć ponownie. Artykuł [Usługa Data Factory — reguły nazewnictwa](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.
  
   ![Komunikat o błędzie występujący, jeśli nazwa jest niedostępna](./media/doc-common-process/name-not-available-error.png)
1. W obszarze **Subskrypcja** wybierz subskrypcję platformy Azure, w której chcesz utworzyć fabrykę danych. 
1. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:
     
   - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy. 
   - Wybierz **pozycję Utwórz nowy**i wprowadź nazwę grupy zasobów.   
         
   Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
1. W obszarze **Wersja** wybierz pozycję **V2**.
1. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych.

   Ta lista zawiera tylko lokalizacje, które są obsługiwane przez usługę Data Factory i w których będą przechowywane metadane usługi Azure Data Factory. Skojarzone magazyny danych (takie jak usługa Azure Storage i usługa Azure SQL Database) oraz obliczenia (takie jak usługa Azure HDInsight), których używa usługa Data Factory, mogą być uruchamiane w innych regionach.

1. Wybierz **pozycję Utwórz**.

1. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych.** Wybierz kafelek **Tworzenie i monitorowanie**, aby na osobnej karcie uruchomić aplikację interfejsu użytkownika usługi Azure Data Factory.
   
   ![Strona główna fabryki danych z kafelkiem „Tworzenie i monitorowanie”](./media/doc-common-process/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>Uruchamianie narzędzia do kopiowania danych

1. Na stronie **Wprowadzenie** wybierz kafelek **Kopiowanie danych**, aby uruchomić narzędzie do kopiowania danych. 

   ![Kafelek „Kopiowanie danych”](./media/doc-common-process/get-started-page.png)

1. Na stronie **Właściwości** narzędzia do kopiowania danych możesz określić nazwę potoku i jego opis, a następnie wybrać przycisk **Dalej**. 

   ![Strona „Właściwości”](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
1. Na stronie **Źródłowy magazyn danych** wykonaj następujące czynności:

    a. Kliknij **pozycję + Utwórz nowe połączenie,** aby dodać połączenie.

    b. Wybierz typ usługi połączonej, który chcesz utworzyć dla połączenia źródłowego. W tym samouczku używamy **usługi Azure Blob Storage**. Wybierz go z galerii, a następnie wybierz pozycję **Kontynuuj**.
    
    ![Wybierz obiekt blob](./media/quickstart-create-data-factory-copy-data-tool/select-blob-source.png)

    d. Na stronie **Nowa usługa połączona (usługa Azure Blob Storage)** określ nazwę połączonej usługi. Wybierz konto magazynu z listy **Nazwa konta magazynu,** przetestuj połączenie, a następnie wybierz pozycję **Utwórz**. 

    ![Konfigurowanie konta usługi Azure Blob Storage](./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png)

    d. Wybierz nowo utworzoną usługę połączony jako źródło, a następnie kliknij przycisk **Dalej**.


1. Na stronie **Wybieranie pliku lub folderu wejściowego** wykonaj następujące czynności:

   a. Kliknij **przycisk Przeglądaj,** aby przejść do folderu **adftutorial/input,** wybierz plik **emp.txt,** a następnie kliknij przycisk **Wybierz**. 

   d. Zaznacz pole wyboru **Kopia binarna,** aby skopiować plik w stanie, w jakim się znajduje, a następnie wybierz pozycję **Dalej**. 

   ![Strona „Wybieranie pliku lub folderu wejściowego”](./media/quickstart-create-data-factory-copy-data-tool/select-binary-copy.png)


1. Na stronie **Docelowy magazyn danych** wybierz utworzoną usługę **Azure Blob Storage,** a następnie wybierz pozycję **Dalej**. 

1. Na stronie **Wybierz plik wyjściowy lub folder** wprowadź **adftutorial/output** dla ścieżki folderu, a następnie wybierz pozycję **Dalej**. 

   ![Strona „Wybieranie pliku lub folderu wyjściowego”](./media/quickstart-create-data-factory-copy-data-tool/configure-sink-path.png) 

1. Na stronie **Ustawienia** wybierz przycisk **Dalej**, aby użyć konfiguracji domyślnych. 

1. Sprawdź wszystkie ustawienia na stronie **Podsumowanie**, a następnie wybierz przycisk **Dalej**. 

1. Na stronie **Zakończono wdrożenie** wybierz pozycję **Monitoruj**, aby monitorować utworzony potok. 

    ![Strona „Zakończono wdrożenie”](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)

1. Aplikacja przełączy się na kartę **Monitor.** Stan potoku zostanie wyświetlony na tej **Refresh** karcie. Kliknij łącze w obszarze **NAZWA POTOKU,** aby wyświetlić szczegóły uruchomienia działania lub ponownie uruchomić potok. 
   
    ![Odśwież potok](./media/quickstart-create-data-factory-copy-data-tool/refresh-pipeline.png)

1. Na stronie Przebiegi działania wybierz **łącze Szczegóły** (ikona okularów) w kolumnie **NAZWA DZIAŁANIA,** aby uzyskać więcej informacji na temat operacji kopiowania. Aby uzyskać więcej informacji o właściwościach, zobacz [Omówienie działania kopiowania](copy-activity-overview.md). 

1. Aby wrócić do widoku Przebiegi potoku, wybierz łącze **WSZYSTKIE uruchomienia potoku** w menu naciągu. Aby odświeżyć widok, wybierz pozycję **Odśwież**. 

1. Sprawdź, czy plik **emp.txt** został utworzony w folderze **output** w kontenerze **adftutorial**. Jeśli folder wyjściowy nie istnieje, usługa Data Factory automatycznie go tworzy. 

1. Przejdź do karty **Tworzenie** powyżej karty **Monitorowanie** na lewym panelu, aby móc edytować połączone usługi, zestawy danych i potoki. Aby dowiedzieć się więcej o edytowaniu tych elementów w interfejsie użytkownika usługi Data Factory, zobacz [Tworzenie fabryki danych za pomocą witryny Azure Portal](quickstart-create-data-factory-portal.md).

    ![Karta Wybierz pozycję Autor](./media/quickstart-create-data-factory-copy-data-tool/select-author.png)

## <a name="next-steps"></a>Następne kroki
Potok w tym przykładzie kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Azure Blob Storage. Zapoznaj się z [samouczkami](tutorial-copy-data-portal.md), aby dowiedzieć się więcej o korzystaniu z usługi Data Factory w dalszych scenariuszach. 
