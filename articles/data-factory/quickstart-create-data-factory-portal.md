---
title: Tworzenie fabryki danych platformy Azure przy użyciu interfejsu użytkownika usługi Azure Data Factory
description: Tworzenie fabryki danych z potokiem, który kopiuje dane między lokalizacjami w usłudze Azure Blob Storage.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 02/25/2020
ms.author: jingwang
ms.openlocfilehash: 0afb0ddb65a4f27463e2bb5c1b9441d248c34415
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240758"
---
# <a name="quickstart-create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Szybki start: tworzenie fabryki danych przy użyciu interfejsu użytkownika usługi Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Bieżąca wersja](quickstart-create-data-factory-portal.md)

W tym przewodniku Szybki start opisano sposób używania interfejsu użytkownika usługi Azure Data Factory w celu tworzenia i monitorowania fabryki danych. Potok tworzony w tej fabryce danych *kopiuje* dane z jednego folderu do innego folderu w usłudze Azure Blob Storage. Aby zapoznać się z samouczkiem dotyczącym *przekształcania* danych za pomocą usługi Azure Data Factory, zobacz [Tutorial: Transform data by using Spark (Samouczek: przekształcanie danych przy użyciu usługi Spark)](tutorial-transform-data-spark-portal.md).

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, przed wykonaniem kroków zawartych w tym przewodniku Szybki start zobacz [Wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md). 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Film wideo 
Obejrzenie tego filmu wideo ułatwi zapoznanie się z interfejsem użytkownika usługi Data Factory: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
1. Przejdź do [witryny Azure portal](https://portal.azure.com). 
1. Z menu Portalu platformy Azure wybierz polecenie **Utwórz zasób**.
   
   ![Wybieranie opcji Utwórz zasób z menu portalu platformy Azure](./media/doc-common-process/create-a-resource.png)
1. Wybierz **pozycję Analytics**, a następnie wybierz pozycję **Fabryka danych**. 
   
   ![Wybór usługi Data Factory w okienku „Nowy”](./media/doc-common-process/new-azure-data-factory-menu.png)
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

1. Wybierz **pozycję Utwórz**. Po zakończeniu tworzenia wybierz pozycję **Przejdź do zasobu,** aby przejść do strony **Fabryka danych.** 

1. Wybierz kafelek **Tworzenie i monitorowanie**, aby na osobnej karcie uruchomić aplikację interfejsu użytkownika usługi Azure Data Factory.
   
   ![Strona główna fabryki danych z kafelkiem „Tworzenie i monitorowanie”](./media/doc-common-process/data-factory-home-page.png)
   
   > [!NOTE]
   > Jeśli widzisz, że przeglądarka internetowa utknęła na "Autoryzowanie", wyczyść pole wyboru **Blokuj pliki cookie i dane witryny.** Lub zachowaj jego wybraną opcję, utwórz wyjątek dla **login.microsoftonline.com**, a następnie spróbuj ponownie otworzyć aplikację.
   
1. Na stronie **Wprowadzenie** przejdź do karty **Tworzenie** na lewym panelu. 

    ![Strona „Wprowadzenie”](./media/doc-common-process/get-started-page-author-button.png)

## <a name="create-a-linked-service"></a>Tworzenie usługi połączonej
W tej procedurze utworzysz połączony serwis, aby połączyć swoje konto usługi Azure Storage z fabryką danych. Połączona usługa ma informacje o połączeniu, których usługa Data Factory używa w środowisku uruchomieniowym do nawiązywania z nią połączenia.

1. Wybierz **pozycję Połączenia**, a następnie wybierz przycisk **Nowy** na pasku narzędzi ( Przycisk**Połączenia** znajduje się u dołu lewej kolumny w obszarze **Zasoby fabryczne).** 

1. Na stronie **Nowa połączona usługa** wybierz pozycję **Azure Blob Storage**, a następnie wybierz pozycję **Dalej**. 

1. Na stronie Nowa usługa połączona (usługa Azure Blob Storage) wykonaj następujące kroki: 

   a. Wprowadź wartość **AzureStorageLinkedService** w polu **Nazwa**.

   b. W przypadku **nazwy konta magazynu**wybierz nazwę swojego konta usługi Azure Storage.

   d. Wybierz pozycję **Testuj połączenie**, aby sprawdzić, czy usługa Data Factory może nawiązać połączenie z kontem magazynu. 

   d. Wybierz **pozycję Utwórz,** aby zapisać usługę połączony. 

      ![Nowa połączona usługa](./media/quickstart-create-data-factory-portal/linked-service.png)


## <a name="create-datasets"></a>Tworzenie zestawów danych
W tej procedurze tworzone są dwa zestawy danych: **InputDataset** i **OutputDataset**. Te zestawy danych są typu **AzureBlob**. Odwołują się one do połączonej usługi Azure Storage utworzonej w poprzedniej sekcji. 

Wejściowy zestaw danych reprezentuje dane źródłowe w folderze wejściowym. W definicji wejściowego zestawu danych określany jest kontener obiektów blob (**adftutorial**), folder (**input**) i plik (**emp.txt**), który zawiera dane źródłowe. 

Wyjściowy zestaw danych reprezentuje dane, które są kopiowane do lokalizacji docelowej. W definicji wyjściowego zestawu danych określany jest kontener obiektów blob (**adftutorial**), folder (**output**) i plik, do którego kopiowane są dane. Każde uruchomienie potoku ma skojarzony ze sobą unikatowy identyfikator. Aby uzyskać dostęp do tego identyfikatora, skorzystaj ze zmiennej systemowej **RunId**. Nazwa pliku wyjściowego jest dynamicznie obliczana na podstawie identyfikatora uruchomienia potoku.   

W ustawieniach usługi połączonej określono konto usługi Azure Storage, które zawiera dane źródłowe. W ustawieniach zestawu danych źródłowych należy określić, gdzie dokładnie znajduje się źródło danych (kontener obiektów blob, folder i plik). W ustawieniach zestawu danych ujścia należy określić, gdzie kopiowane są dane (kontener obiektów blob, folder i plik). 
 
1. Wybierz **+** przycisk (plus), a następnie wybierz **pozycję Zestaw danych**.

   ![Menu do tworzenia zestawu danych](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
1. Na stronie **Nowy zestaw danych** wybierz pozycję Azure **Blob Storage**, a następnie wybierz pozycję **Kontynuuj**. 

1. Na stronie **Wybierz format** wybierz typ formatu danych, a następnie wybierz pozycję **Kontynuuj**. W takim przypadku wybierz **binary,** gdy kopiuje pliki w stanie takim, w jakim są bez analizowania zawartości.

   ![Wybierz format](./media/quickstart-create-data-factory-portal/select-format.png)
   
1. Na stronie **Ustaw właściwości** wykonaj następujące czynności:

    a. W obszarze **Nazwa**wprowadź **inputdataset**. 

    b. Wybierz pozycję **AzureStorageLinkedService** w polu **Połączona usługa**.

    d. Kliknij przycisk **Przeglądaj** w polu **Ścieżka pliku**.

    d. W oknie **Wybieranie pliku lub folderu** przejdź do folderu **wejściowego** w kontenerze **adftutorial,** wybierz plik **emp.txt,** a następnie wybierz przycisk **OK**.
    
    e. Kliknij przycisk **OK**.   

    ![Ustawianie właściwości zestawu danych wejściowych](./media/quickstart-create-data-factory-portal/set-properties-for-inputdataset.png)
1. Powtórz kroki, aby utworzyć wyjściowy zestaw danych:  

    a. Wybierz **+** przycisk (plus), a następnie wybierz **pozycję Zestaw danych**.

    b. Na stronie **Nowy zestaw danych** wybierz pozycję Azure **Blob Storage**, a następnie wybierz pozycję **Kontynuuj**.

    d. Na stronie **Wybierz format** wybierz typ formatu danych, a następnie wybierz pozycję **Kontynuuj**.

    d. Na stronie **Ustaw właściwości** określ zestaw **danych wyjściowych** dla nazwy. Wybierz **AzureStorageLinkedService** jako usługę połączona.

    e. W obszarze **Ścieżka pliku**wprowadź **adftutorial/output**. Jeśli folder **wyjściowy** nie istnieje, działanie kopiowania tworzy go w czasie wykonywania.

    f. Kliknij przycisk **OK**.   

    ![Ustawianie właściwości zestawu danych wyjściowych](./media/quickstart-create-data-factory-portal/set-properties-for-outputdataset.png)
## <a name="create-a-pipeline"></a>Tworzenie potoku 
Podczas tej procedury utworzysz potok i zweryfikujesz go za pomocą działania kopiowania, które korzysta z wejściowego i wyjściowego zestawu danych. Działanie kopiowania służy do kopiowania danych z pliku określonego w ustawieniach wejściowego zestawu danych do pliku określonego w ustawieniach wyjściowego zestawu danych. Jeśli wejściowy zestaw danych określa tylko folder (a nie nazwę pliku), działanie kopiowania kopiuje wszystkie pliki w folderze źródłowym do lokalizacji docelowej. 

1. Wybierz **+** przycisk (plus), a następnie wybierz pozycję **Potok**. 

1. Na karcie **Ogólne** określ wartość **CopyPipeline** w polu **Nazwa**. 

1. W przyborniku **Działania** rozwiń pozycję **Przenoszenie i przekształcanie**. Przeciągnij działanie **Kopiuj dane** z przybornika **Działania** na powierzchnię projektanta potoku. Możesz również wyszukać działania w przyborniku **Działania**. Wprowadź wartość **CopyFromBlobToBlob** w polu **Nazwa**.
   ![Tworzenie działania kopiowania danych](./media/quickstart-create-data-factory-portal/copy-activity.png)

1. Przejdź do karty **Źródło** w ustawieniach działania kopiowania, a następnie wybierz wartość **InputDataset** w polu **Zestaw danych źródłowych**.

1. Przejdź do karty **Ujście** w ustawieniach działania kopiowania, a następnie wybierz wartość **OutputDataset** w polu **Zestaw danych ujścia**.

1. Aby sprawdzić poprawność ustawień potoku, kliknij pozycję **Weryfikuj** na pasku narzędzi potoku powyżej kanwy. Sprawdź, czy potok został pomyślnie zweryfikowany. Aby zamknąć dane wyjściowe sprawdzania poprawności, wybierz przycisk (strzałka w **>>** prawo). 
   ![Sprawdzanie poprawności potoku](./media/quickstart-create-data-factory-portal/pipeline-validate.png)

## <a name="debug-the-pipeline"></a>Debugowanie potoku
W tym kroku przeprowadzisz debugowanie potoku przed jego wdrożeniem w usłudze Data Factory. 

1. Na pasku narzędzi nad kanwą potoku kliknij pozycję **Debugowanie**, aby wyzwolić przebieg testu. 
    
1. Sprawdź, czy w dolnej części karty **Dane wyjściowe** ustawień potoku wyświetlany jest stan przebiegu potoku. 
 
    ![Wyjście przebiegu potoku](./media/quickstart-create-data-factory-portal/pipeline-output.png)

1. Sprawdź, czy w folderze **output** kontenera **adftutorial** znajduje się plik wyjściowy. Jeśli folder wyjściowy nie istnieje, usługa Data Factory automatycznie go tworzy. 

## <a name="trigger-the-pipeline-manually"></a>Ręczne wyzwalanie potoku
Podczas tej procedury wdrożysz jednostki (połączone usługi, zestawy danych i potoki) w usłudze Azure Data Factory. Następnie ręcznie wyzwolisz przebieg potoku. 

1. Przed wyzwoleniem potoku należy opublikować jednostki w usłudze Data Factory. Aby opublikować, wybierz **pozycję Opublikuj wszystko** u góry. 
    ![Opublikuj wszystkie](./media/quickstart-create-data-factory-portal/publish-all.png)

1. Aby ręcznie wyzwolić potok, wybierz pozycję **Dodaj wyzwalacz** na pasku narzędzi potoku, a następnie wybierz pozycję **Wyzwalaj teraz**. Na stronie **Przebieg potoku** wybierz pozycję **Zakończ**.

## <a name="monitor-the-pipeline"></a>Monitorowanie potoku

1. Przejdź do karty **Monitorowanie** po lewej stronie. Kliknij przycisk **Odśwież**, aby odświeżyć listę.

   ![Karta do monitorowania przebiegów potoku](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
1. Wybierz łącze **CopyPipeline,** zobaczysz stan działania kopiowania uruchomionego na tej stronie. 

1. Aby wyświetlić szczegółowe informacje o operacji kopiowania, wybierz **łącze Szczegóły** (obraz okularów). Aby uzyskać więcej informacji o właściwościach, zobacz [Omówienie działania kopiowania](copy-activity-overview.md). 

   ![Szczegóły operacji kopiowania](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
1. Sprawdź, czy nowy plik jest widoczny w folderze **output**. 
1. Można przełączyć się z powrotem do widoku **Potok uruchamia** z **działania uruchamia** widok, wybierając **wszystkie przebiegi potoku** łącza. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Wyzwalanie potoku zgodnie z harmonogramem
W tym samouczku ta procedura jest opcjonalna. Możesz utworzyć *wyzwalacz harmonogramu*, aby zaplanować okresowe uruchamianie potoku (co godzinę, codziennie itd.). Podczas tej procedury utworzysz wyzwalacz, który będzie uruchamiany co minutę, aż do daty/godziny określonej jako data zakończenia. 

1. Przejdź do karty **Tworzenie**. 

1. Przejdź do potoku, wybierz pozycję **Dodaj wyzwalacz** na pasku narzędzi potoku, a następnie wybierz pozycję **Nowy/Edytuj**. 

1. Na stronie **Dodawanie wyzwalaczy** wybierz pozycję **Wybierz wyzwalacz**, a następnie wybierz przycisk **Nowy**. 

1. Na stronie **Nowy wyzwalacz** w obszarze **Koniec**wybierz pozycję **Na dacie**określ godzinę zakończenia kilka minut po bieżącym czasie, a następnie wybierz przycisk **OK**. 

   Za poszczególne uruchomienia potoku są naliczane opłaty, zatem określ czas zakończenia jako późniejszy tylko o kilka minut od czasu rozpoczęcia. Upewnij się, że przypada on tego samego dnia. Jednak upewnij się, że jest wystarczająco dużo czasu dla potoku do uruchomienia między czasem publikowania i godziną zakończenia. Wyzwalacz zaczyna obowiązywać dopiero po opublikowaniu rozwiązania w fabryce Data Factory, a nie po zapisaniu go w interfejsie użytkownika. 

1. Na stronie **Nowy wyzwalacz** zaznacz pole wyboru **Aktywowano,** a następnie wybierz przycisk **OK**. 

   ![Nowe ustawienie wyzwalacza](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
1. Przejrzyj komunikat ostrzegawczy i wybierz **przycisk OK**.

1. Wybierz **pozycję Opublikuj wszystkie,** aby opublikować zmiany w fabryce danych. 

1. Przejdź do karty **Monitorowanie** po lewej stronie. Wybierz pozycję **Odśwież**, aby odświeżyć listę. Potok będzie uruchamiany raz na minutę od czasu opublikowania do czasu zakończenia. 

   Zwróć uwagę na wartości w kolumnie **TRIGGERED BY.** Ręczne uruchomienie wyzwalacza pochodzi z kroku wykonanego wcześniej (**Wyzwól teraz**). 

1. Przełącz się do widoku **Uruchamianie wyzwalacza.** 

1. Sprawdź, czy plik wyjściowy jest tworzony w folderze **output** dla każdego uruchomienia potoku aż do określonej daty/godziny zakończenia. 

## <a name="next-steps"></a>Następne kroki
Potok w tym przykładzie kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Azure Blob Storage. Zapoznaj się z [samouczkami](tutorial-copy-data-portal.md), aby dowiedzieć się więcej o korzystaniu z usługi Data Factory w dalszych scenariuszach. 
