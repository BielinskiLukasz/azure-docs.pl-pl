---
title: Kopiowanie danych lokalnych za pomocą narzędzia do kopiowania danych platformy Azure | Microsoft Docs
description: Utwórz fabrykę danych platformy Azure, a następnie za pomocą narzędzia do kopiowania danych skopiuj dane z lokalnej bazy danych programu SQL Server do usługi Azure Blob Storage.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/04/2018
ms.author: jingwang
ms.openlocfilehash: 91c6939e42f0a8a5126883e5258017b2c38e6f2a
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613974"
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Kopiowanie danych z lokalnej bazy danych programu SQL Server do usługi Azure Blob Storage za pomocą narzędzia do kopiowania danych
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Bieżąca wersja](tutorial-hybrid-copy-data-tool.md)

W tym samouczku utworzysz fabrykę danych za pomocą witryny Azure Portal. Następnie za pomocą narzędzia do kopiowania danych utworzysz potok, który kopiuje dane z lokalnej bazy danych programu SQL Server do usługi Azure Blob Storage.

> [!NOTE]
> - Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Data Factory](introduction.md).

Ten samouczek obejmuje wykonanie następujących kroków:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku za pomocą narzędzia do kopiowania danych.
> * Monitorowanie uruchomień potoku i działań.

## <a name="prerequisites"></a>Wymagania wstępne
### <a name="azure-subscription"></a>Subskrypcja platformy Azure
Jeśli nie masz jeszcze subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Role platformy Azure
Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi mieć przypisaną rolę *współautora* lub *właściciela* albo być *administratorem* subskrypcji platformy Azure. 

Aby wyświetlić swoje uprawnienia do subskrypcji, przejdź do witryny Azure Portal. W prawym górnym rogu wybierz swoją nazwę użytkownika, a następnie wybierz pozycję **Uprawnienia**. Jeśli masz dostęp do wielu subskrypcji, wybierz odpowiednią subskrypcję. Przykładowe instrukcje dotyczące sposobu dodawania użytkownika do roli można znaleźć w temacie [Manage access using RBAC and the Azure portal](../role-based-access-control/role-assignments-portal.md) (Zarządzanie dostępem przy użyciu kontroli dostępu na podstawie ról i witryny Azure Portal).

### <a name="sql-server-2014-2016-and-2017"></a>Program SQL Server 2014, 2016 oraz 2017
Podczas pracy z tym samouczkiem użyjesz lokalnej bazy danych programu SQL Server jako *źródłowego* magazynu danych. Potok usługi Data Factory tworzony w tym samouczku kopiuje dane z tej lokalnej bazy danych programu SQL Server (źródła) do usługi Blob Storage (ujścia). Następnie utworzysz tabelę o nazwie **emp** w bazie danych programu SQL Server i wstawisz kilka przykładowych wpisów do tabeli. 

1. Uruchom program SQL Server Management Studio. Jeśli program nie jest jeszcze zainstalowany na używanej maszynie, przejdź do strony [pobierania programu SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

1. Połącz się z wystąpieniem programu SQL Server przy użyciu swoich poświadczeń. 

1. Utwórz przykładową bazę danych. W widoku drzewa kliknij prawym przyciskiem myszy pozycję **Bazy danych**, a następnie wybierz pozycję **Nowa baza danych**. 

1. W oknie **Nowa baza danych** wprowadź nazwę bazy danych, a następnie wybierz przycisk **OK**. 

1. Aby utworzyć tabelę **emp** i wstawić do niej przykładowe dane, uruchom następujący skrypt zapytania w bazie danych. W widoku drzewa kliknij prawym przyciskiem myszy utworzoną bazę danych, a następnie wybierz pozycję **Nowe zapytanie**.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO
    
    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Konto magazynu Azure
W tym samouczku używasz konta usługi Azure Storage ogólnego przeznaczenia (konkretnie usługi Blob Storage) jako magazynu danych: docelowego/ujścia. Jeśli nie masz konta magazynu ogólnego przeznaczenia, zobacz [Tworzenie konta magazynu](../storage/common/storage-quickstart-create-account.md), aby dowiedzieć się, jak je utworzyć. Potok usługi Data Factory tworzony w tym samouczku kopiuje dane z lokalnej bazy danych programu SQL Server (źródła) do tej usługi Blob Storage (ujścia). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Pobieranie nazwy konta i klucza konta magazynu
W tym samouczku używasz nazwy i klucza swojego konta magazynu. Pobierz nazwę i klucz konta magazynu, wykonując następujące kroki: 

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu nazwy użytkownika i hasła do konta platformy Azure. 

1. W okienku po lewej stronie wybierz pozycję **Więcej usług**. Zastosuj filtrowanie według słowa kluczowego **Magazyn**, a następnie wybierz pozycję **Konta magazynu**.

    ![Wyszukiwanie kont magazynu](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

1. Na liście kont magazynu odfiltruj swoje konto magazynu, jeśli to konieczne. Następnie wybierz swoje konto magazynu. 

1. W oknie **Konto magazynu** wybierz pozycję **Klucze dostępu**.

    ![Klawisze dostępu](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

1. Skopiuj wartości z pól **Nazwa konta magazynu** i **klucz1** i wklej je do Notatnika lub innego edytora do późniejszego użycia z tym samouczkiem. 

#### <a name="create-the-adftutorial-container"></a>Tworzenie kontenera adftutorial 
W tej sekcji utworzysz kontener obiektów blob o nazwie **adftutorial** w usłudze Blob Storage. 

1. W oknie **Konto magazynu** przełącz się do widoku **Przegląd**, a następnie wybierz pozycję **Obiekty blob**. 

    ![Wybieranie opcji Obiekty blob](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. W oknie **Blob Service** wybierz pozycję **Kontener**. 

    ![Przycisk kontenera](media/tutorial-hybrid-copy-powershell/add-container-button.png)

1. W oknie **Nowy kontener** w polu **Nazwa** wpisz nazwę **adftutorial**, a następnie wybierz pozycję **OK**. 

    ![Nowy kontener](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

1. Kliknij pozycję **adftutorial** na liście kontenerów.

    ![Wybór kontenera](media/tutorial-hybrid-copy-powershell/select-adftutorial-container.png)

1. Pozostaw otwarte okno **kontenera** dla **adftutorial**. Będzie ona używana do weryfikacji danych wyjściowych na końcu tego samouczka. Usługa Data Factory automatycznie tworzy folder wyjściowy w tym kontenerze, więc Ty nie musisz go tworzyć.

    ![Okno kontenera](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Z menu po lewej stronie wybierz kolejno pozycje **Nowy** > **Dane i analiza** > **Fabryka danych**. 
  
   ![Tworzenie nowej fabryki danych](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory-menu.png)
1. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**. 
   
     ![Nowa fabryka danych](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory.png)

   Nazwa fabryki danych musi być *globalnie unikatowa*. Jeśli dla pola nazwy zobaczysz poniższy komunikat o błędzie, zmień nazwę fabryki danych (np. twojanazwaADFTutorialDataFactory). Reguły nazewnictwa dla artefaktów usługi Data Factory można znaleźć w artykule [Data Factory — reguły nazewnictwa](naming-rules.md).

   ![Nazwa nowej fabryki danych](./media/tutorial-hybrid-copy-data-tool/name-not-available-error.png)
1. Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć fabrykę danych. 
1. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:
  
      - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.

      - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów. 
        
      Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/resource-group-overview.md).
1. W obszarze **Wersja** wybierz pozycję **V2**.
1. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (np. usługi Azure Storage i SQL Database) oraz jednostki obliczeniowe (np. usługa Azure HDInsight) używane przez usługę Data Factory mogą mieścić się w innych lokalizacjach/regionach.
1. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**. 
1. Wybierz pozycję **Utwórz**.
1. Na pulpicie nawigacyjnym jest widoczny następujący kafelek ze stanem **Wdrażanie fabryki danych**:

    ![Kafelek Wdrażanie fabryki danych](media/tutorial-hybrid-copy-data-tool/deploying-data-factory.png)
1. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**, jak pokazano na poniższej ilustracji.
  
    ![Strona główna fabryki danych](./media/tutorial-hybrid-copy-data-tool/data-factory-home-page.png)
1. Wybierz pozycję **Tworzenie i monitorowanie**, aby na osobnej karcie uruchomić interfejs użytkownika usługi Data Factory. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Tworzenie potoku za pomocą narzędzia do kopiowania danych

1. Na stronie **Wprowadzenie** wybierz pozycję **Kopiowanie danych**, aby uruchomić narzędzie do kopiowania danych. 

   ![Kafelek narzędzia do kopiowania danych](./media/tutorial-hybrid-copy-data-tool/copy-data-tool-tile.png)

1. Na stronie **Właściwości** narzędzia do kopiowania danych wprowadź w polu **Nazwa zadania** wartość **CopyFromOnPremSqlToAzureBlobPipeline**. Następnie wybierz przycisk **Dalej**. Narzędzie do kopiowania danych tworzy potok o nazwie określonej w tym polu. 

   ![Nazwa zadania](./media/tutorial-hybrid-copy-data-tool/properties-page.png)

1. Na stronie **Źródłowy magazyn danych** kliknij pozycję **Utwórz nowe połączenie**. 

   ![Tworzenie nowej połączonej usługi](./media/tutorial-hybrid-copy-data-tool/create-new-source-data-store.png)

1. W obszarze **Nowa połączona usługa** wyszukaj usługę **SQL Server**, a następnie wybierz przycisk **Dalej**. 

   ![Wybór pozycji SQL Server](./media/tutorial-hybrid-copy-data-tool/select-source-data-store.png)

1. W polu **Nazwa**** w oknie Nowa połączona usługa (SQL Server) wpisz **SqlServerLinkedService**. Wybierz pozycję **+Nowy** w polu **Połącz za pośrednictwem środowiska Integration Runtime**. Należy utworzyć środowisko Integration Runtime (Self-hosted), pobrać je na komputer i zarejestrować w usłudze Data Factory. Środowisko Integration Runtime (Self-hosted) kopiuje dane między środowiskiem lokalnym a chmurą.

   ![Tworzenie własnego środowiska Integration Runtime](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-link.png)

1. W oknie dialogowym **instalatora środowiska Integration Runtime** wybierz pozycję **Sieć prywatna**. Następnie wybierz przycisk **Dalej**. 

   ![](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog0.png)

1. W oknie dialogowym **instalatora środowiska Integration Runtime** w polu **Nazwa** wpisz **TutorialIntegrationRuntime**. Następnie wybierz przycisk **Dalej**. 

   ![Nazwa środowiska Integration Runtime](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog.png)

1. Wybierz pozycję **Kliknij tutaj, aby uruchomić instalację ekspresową dla tego komputera**. Ta akcja instaluje na komputerze środowisko Integration Runtime i rejestruje je w usłudze Data Factory. Ewentualnie można użyć opcji instalacji ręcznej w celu pobrania pliku instalacyjnego, uruchomienia go i zarejestrowania środowiska Integration Runtime za pomocą klucza. 

    ![Link do uruchamiania instalacji ekspresowej dla tego komputera](./media/tutorial-hybrid-copy-data-tool/launch-express-setup-link.png)

1. Uruchom pobraną aplikację. W oknie zostanie wyświetlony stan instalacji ekspresowej. 

    ![Stan instalacji ekspresowej](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)

1. Upewnij się, że w polu **Integration Runtime** wybrano pozycję **TutorialIntegrationRuntime**.

    ![Wybrane środowisko Integration Runtime](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)

1. Na stronie **Określanie lokalnej bazy danych programu SQL Server** wykonaj następujące czynności: 

      a. W polu **Nazwa** wprowadź wartość **SqlServerLinkedService**.

      b. W polu **Nazwa serwera** wprowadź nazwę lokalnego wystąpienia programu SQL Server.

      d. Wprowadź nazwę swojej lokalnej bazy danych w polu **Nazwa bazy danych**.

      d. Wybierz odpowiedni typ uwierzytelniania w polu **Typ uwierzytelniania**.

      e. Wprowadź nazwę użytkownika z dostępem do lokalnego programu SQL Server w polu **Nazwa użytkownika**.

      f. Wprowadź **hasło** dla użytkownika. Wybierz pozycję **Finish** (Zakończ). 

1. Wybierz opcję **Dalej**.

     ![](./media/tutorial-hybrid-copy-data-tool/select-source-linked-service.png)

1. Na stronie **Wybieranie tabel, z których mają być kopiowane dane, lub używanie zapytania niestandardowego** wybierz z listy tabelę **[dbo].[emp]**, a następnie wybierz pozycję **Dalej**. Możesz wybrać dowolną inną tabelę, odpowiednio do bazy danych.

     ![Wybór tabeli produktów](./media/tutorial-hybrid-copy-data-tool/select-emp-table.png)

1. Na stronie **Docelowy magazyn danych** wybierz pozycję **Utwórz nowe połączenie**.

     //image create-new-sink-connection.png

     ![Tworzenie docelowej połączonej usługi](./media/tutorial-hybrid-copy-data-tool/create-new-sink-connection.png)

1. W obszarze **Nowa połączona usługa** wyszukaj i wybierz pozycję **Azure Blob Storage**, a następnie wybierz pozycję **Kontynuuj**. 

     ![Wybór usługi Blob Storage](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)

1. W oknie dialogowym **Nowa połączona usługa (Azure Blob Storage)** wykonaj następujące kroki: 

     a. W polu **Nazwa**** wpisz **AzureStorageLinkedService**.

     b. W obszarze **Połącz za pośrednictwem środowiska Integration Runtime** wybierz pozycję **TutorialIntegrationRuntime**.

     d. W polu **Nazwa konta magazynu** wybierz z listy rozwijanej swoje konto magazynu. 

     d. Wybierz opcję **Dalej**.

     ![Określanie konta magazynu](./media/tutorial-hybrid-copy-data-tool/specify-azure-blob-storage-account.png)

1. W oknie dialogowym **Docelowy magazyn danych** wybierz pozycję **Dalej**. W obszarze **Właściwości połączenia** w polu **Usługa Azure Storage** wybierz pozycję **Azure Blob Storage**. Wybierz opcję **Dalej**. 

     ![Właściwości połączenia](./media/tutorial-hybrid-copy-data-tool/select-connection-properties.png)

1. W oknie dialogowym **Wybieranie wyjściowego pliku lub folderu** w obszarze **Ścieżka folderu** wprowadź wartość **adftutorial/fromonprem**. Jako jedno z wymagań wstępnych został utworzony kontener **adftutorial**. Jeśli folder wyjściowy (w tym przykładzie **fromonprem**) nie istnieje, usługa Data Factory utworzy go automatycznie. Możesz również za pomocą przycisku **Przeglądaj** przejść do magazynu obiektów blob i jego kontenerów/folderów. Jeśli nie wprowadzisz żadnej wartości w polu **Nazwa pliku**, domyślnie zostanie użyta nazwa źródła (w tym przykładzie **dbo.emp**).
           
     ![Wybieranie pliku lub folderu wyjściowego](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)

1. W oknie dialogowym **Ustawienia formatu pliku** wybierz przycisk **Dalej**. 

     ![Strona Ustawienia formatu pliku](./media/tutorial-hybrid-copy-data-tool/file-format-settings-page.png)

1. W oknie dialogowym **Ustawienia** wybierz przycisk **Dalej**. 

     ![Strona Ustawienia](./media/tutorial-hybrid-copy-data-tool/settings-page.png)

1. W oknie dialogowym **Podsumowanie** sprawdź wartości wszystkich ustawień, a następnie wybierz pozycję **Dalej**. 

     ![Strona podsumowania](./media/tutorial-hybrid-copy-data-tool/summary-page.png)

1. Na stronie **Wdrażanie** wybierz pozycję **Monitorowanie**, aby monitorować utworzony potok lub zadanie.

     ![Strona Wdrażanie](./media/tutorial-hybrid-copy-data-tool/deployment-page.png)

1. Na karcie **Monitorowanie** można wyświetlić stan utworzonego potoku. Możesz użyć linków w kolumnie **Akcja**, aby wyświetlić uruchomienia działań skojarzonych z uruchomieniem potoku oraz ponownie uruchamiać potok. 

     ![Monitorowanie uruchomień potoku](./media/tutorial-hybrid-copy-data-tool/monitor-pipeline-runs.png)

1. Wybierz link **Wyświetl uruchomienia działania** w kolumnie **Akcje**, aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku. Aby wyświetlić szczegółowe informacje na temat operacji kopiowania, wybierz link **Szczegóły** (ikona okularów) w kolumnie **Akcje**. Aby przełączyć się z powrotem do widoku **Uruchomienia potoków**, wybierz pozycję **Potoki** u góry ekranu.

     ![Monitorowanie uruchomień działania](./media/tutorial-hybrid-copy-data-tool/monitor-activity-runs.png)

1. Upewnij się, że w folderze **fromonprem** kontenera **adftutorial** znajduje się plik wyjściowy. 

     ![Wyjściowy obiekt blob](./media/tutorial-hybrid-copy-data-tool/output-blob.png)

1. Wybierz kartę **Edycja** po lewej stronie, aby przełączyć się w tryb edytora. Za pomocą edytora można zaktualizować usługi połączone, zestawy danych i potoki utworzone przez narzędzie. Wybierz pozycję **Kod**, aby wyświetlić kod JSON skojarzony z jednostką otwartą w edytorze. Aby uzyskać szczegółowe informacje dotyczące sposobu edytowania tych jednostek w interfejsie użytkownika usługi Data Factory, zobacz [wersję witryny Azure Portal używaną w tym samouczku](tutorial-copy-data-portal.md).

     ![Karta Edycja](./media/tutorial-hybrid-copy-data-tool/edit-tab.png)


## <a name="next-steps"></a>Następne kroki
Potok w tym przykładzie kopiuje dane z lokalnej bazy danych programu SQL Server do usługi Blob Storage. W tym samouczku omówiono: 

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku za pomocą narzędzia do kopiowania danych.
> * Monitorowanie uruchomień potoku i działań.

Lista magazynów danych obsługiwanych przez usługę Data Factory znajduje się w artykule [Obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).

Aby dowiedzieć się, jak zbiorczo kopiować dane z lokalizacji źródłowej do docelowej, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Zbiorcze kopiowanie danych](tutorial-bulk-copy-portal.md)
