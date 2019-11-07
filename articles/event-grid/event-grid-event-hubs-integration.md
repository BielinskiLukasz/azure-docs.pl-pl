---
title: 'Samouczek: wysyłanie danych Event Hubs do magazynu danych — Event Grid'
description: 'Samouczek: zawiera opis sposobu używania Azure Event Grid i Event Hubs do migrowania danych do SQL Data Warehouse. Do pobierania pliku przechwytywania służy funkcja platformy Azure.'
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 6f5bd129b175210cd5b9415a65b8db06d904e24d
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718181"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Samouczek: przesyłanie strumieniowe danych Big Data do magazynu danych
Usługa Azure [Event Grid](overview.md) jest inteligentną usługą routingu zdarzeń, która umożliwia reagowanie na powiadomienia (zdarzenia) z aplikacji i usług. Może na przykład spowodować, że funkcja platformy Azure będzie przetwarzać dane centrum zdarzeń, które zostały przechwycone przez usługę Azure Blob Storage lub usługę Azure Data Lake Storage, a także przeprowadzać migrację danych do innych repozytoriów danych. W tym [przykładzie integracji usług Event Hubs i Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) pokazano, jak bezproblemowo przeprowadzić migrację przechwyconych danych usługi Event Hubs z magazynu obiektów blob do usługi SQL Data Warehouse przy użyciu usług Event Hubs i Event Grid.

![Omówienie aplikacji](media/event-grid-event-hubs-integration/overview.png)

Ten diagram przedstawia przepływ pracy rozwiązania, które tworzysz w ramach tego samouczka: 

1. Dane wysłane do centrum zdarzeń platformy Azure są przechwytywane w magazynie obiektów blob Azure.
2. Po zakończeniu przechwytywania następuje wygenerowanie zdarzenia i wysłanie go do siatki zdarzeń Azure. 
3. Siatka zdarzeń przekazuje te dane zdarzeń do aplikacji funkcji platformy Azure.
4. Aplikacja funkcji pobiera obiekt blob z magazynu, używając adresu URL obiektu blob. 
5. Aplikacja funkcji przeprowadza migrację danych obiektów blob do magazynu danych SQL platformy Azure. 

W tym artykule wykonasz następujące kroki:

> [!div class="checklist"]
> * Wdrażanie infrastruktury: centrum zdarzeń, konta magazynu, aplikacji funkcji i magazynu danych SQL przy użyciu szablonu usługi Azure Resource Manager.
> * Tworzenie tabeli w magazynie danych.
> * Dodawanie kodu do aplikacji funkcji.
> * Subskrybowanie zdarzenia. 
> * Uruchamianie aplikacji, która wysyła dane do centrum zdarzeń.
> * Wyświetlanie zmigrowanych danych w magazynie danych.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* [Program Visual studio 2019](https://www.visualstudio.com/vs/) z obciążeniami dla: Programowanie aplikacji klasycznych platformy .NET, programowanie platformy Azure, programowanie ASP.NET i sieci Web, programowanie w języku Node. js i programowanie w języku Python.
* Pobierz na komputer [przykładowy projekt EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).

## <a name="deploy-the-infrastructure"></a>Wdrażanie infrastruktury
W tym kroku wdrożysz wymaganą infrastrukturę za pomocą [szablonu usługi Resource Manager](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Podczas wdrażania szablonu tworzone są następujące zasoby:

* Centrum zdarzeń z włączoną funkcją Capture
* Konto magazynu na potrzeby przechwyconych plików 
* Plan usługi aplikacji do hostowania aplikacji funkcji
* Aplikacja funkcji do przetwarzania zdarzeń
* Rozwiązanie SQL Server do hostowania magazynu danych
* Usługa SQL Data Warehouse do przechowywania migrowanych danych

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>Uruchamianie usługi Azure Cloud Shell w witrynie Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Wybierz przycisk **Cloud Shell** u góry.

    ![Azure Portal](media/event-grid-event-hubs-integration/azure-portal.png)
3. W dolnej części przeglądarki zostanie otwarta usługa Cloud Shell.

    ![Cloud Shell](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. Jeśli w usłudze Cloud Shell zostaną wyświetlone opcje **Bash** i **PowerShell** do wyboru, wybierz pozycję **Bash**. 
5. Jeśli używasz usługi Cloud Shell po raz pierwszy, utwórz konto magazynu, wybierając pozycję **Utwórz magazyn**. Usługa Azure Cloud Shell wymaga konta magazynu platformy Azure do przechowywania niektórych plików. 

    ![Tworzenie magazynu dla usługi Cloud Shell](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. Zaczekaj na zainicjowanie usługi Cloud Shell. 

    ![Tworzenie magazynu dla usługi Cloud Shell](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Utwórz grupę zasobów platformy Azure, uruchamiając następujące polecenie interfejsu wiersza polecenia: 
    1. Skopiuj poniższe polecenie i wklej je w oknie usługi Cloud Shell.

        ```azurecli
        az group create -l eastus -n <Name for the resource group>
        ```
    1. Określ nazwę **grupy zasobów**.
    2. Naciśnij klawisz **ENTER**. 

        Oto przykład:
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n ehubegridgrp
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/ehubegridgrp",
          "location": "eastus",
          "managedBy": null,
          "name": "ehubegridgrp",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. Wdróż wszystkie zasoby wymienione w poprzedniej sekcji (centrum zdarzeń, konto magazynu, aplikację funkcji, magazyn danych SQL), uruchamiając następujące polecenie interfejsu wiersza polecenia: 
    1. Skopiuj polecenie i wklej je w oknie usługi Cloud Shell. Możesz również skopiować polecenie i wkleić je do wybranego edytora, określić wartości, a następnie skopiować polecenie do usługi Cloud Shell. 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigrationSample \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. Określ wartości dla następujących jednostek:
        1. Nazwa utworzonej wcześniej grupy zasobów.
        2. Nazwa przestrzeni nazw centrum zdarzeń. 
        3. Nazwa centrum zdarzeń. Możesz pozostawić wartość bez zmian (hubdatamigration).
        4. Nazwa serwera SQL.
        5. Nazwa użytkownika i hasło SQL. 
        6. Nazwa magazynu danych SQL.
        7. Nazwa konta magazynu. 
        8. Nazwa aplikacji funkcji. 
    3.  W oknie usługi Cloud Shell naciśnij klawisz **ENTER**, aby uruchomić polecenie. Ten proces może zająć trochę czasu, ponieważ tworzysz wiele zasobów. W wyniku wykonania polecenia upewnij się, że nie wystąpiły żadne błędy. 
    

### <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

1. W usłudze Azure Cloud Shell przełącz się w tryb programu PowerShell. W lewym górnym rogu usługi Azure Cloud Shell wybierz strzałkę w dół, a następnie wybierz pozycję **PowerShell**.

    ![Przełączanie na program PowerShell](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. Utwórz grupę zasobów platformy Azure, uruchamiając następujące polecenie: 
    1. Skopiuj poniższe polecenie i wklej je w oknie usługi Cloud Shell.

        ```powershell
        New-AzResourceGroup -Name rgDataMigration -Location westcentralus
        ```
    2. Określ nazwę **grupy zasobów**.
    3. Naciśnij klawisz ENTER. 
3. Wdróż wszystkie zasoby wymienione w poprzedniej sekcji (centrum zdarzeń, konto magazynu, aplikację funkcji, magazyn danych SQL), uruchamiając następujące polecenie:
    1. Skopiuj polecenie i wklej je w oknie usługi Cloud Shell. Możesz również skopiować polecenie i wkleić je do wybranego edytora, określić wartości, a następnie skopiować polecenie do usługi Cloud Shell. 

        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
        ```
    2. Określ wartości dla następujących jednostek:
        1. Nazwa utworzonej wcześniej grupy zasobów.
        2. Nazwa przestrzeni nazw centrum zdarzeń. 
        3. Nazwa centrum zdarzeń. Możesz pozostawić wartość bez zmian (hubdatamigration).
        4. Nazwa serwera SQL.
        5. Nazwa użytkownika i hasło SQL. 
        6. Nazwa magazynu danych SQL.
        7. Nazwa konta magazynu. 
        8. Nazwa aplikacji funkcji. 
    3.  W oknie usługi Cloud Shell naciśnij klawisz **ENTER**, aby uruchomić polecenie. Ten proces może zająć trochę czasu, ponieważ tworzysz wiele zasobów. W wyniku wykonania polecenia upewnij się, że nie wystąpiły żadne błędy. 

### <a name="close-the-cloud-shell"></a>Zamykanie usługi Cloud Shell 
Zamknij usługę Cloud Shell, wybierając przycisk **Cloud Shell** w portalu (lub) przycisk **X** w prawym górnym rogu okna usługi Cloud Shell. 

### <a name="verify-that-the-resources-are-created"></a>Sprawdzanie, czy są tworzone zasoby

1. W witrynie Azure Portal wybierz pozycję **Grupy zasobów** w menu po lewej stronie. 
2. Przefiltruj listę grup zasobów, wprowadzając w polu wyszukiwania nazwę grupy zasobów. 
3. Wybierz grupę zasobów na liście.

    ![Wybieranie grupy zasobów](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Sprawdź, czy w grupie zasobów są wyświetlane następujące zasoby:

    ![Zasoby w grupie zasobów](media/event-grid-event-hubs-integration/resources-in-resource-group.png)

### <a name="create-a-table-in-sql-data-warehouse"></a>Tworzenie tabeli w usłudze SQL Data Warehouse
Utwórz tabelę w swoim magazynie danych, uruchamiając skrypt [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql). Do uruchomienia skryptu możesz użyć programu Visual Studio lub Edytora zapytań w portalu. W poniższych krokach pokazano, jak użyć Edytora zapytań: 

1. Na liście zasobów w grupie zasobów wybierz magazyn danych SQL. 
2. Na stronie magazynu danych SQL wybierz pozycję **Edytor zapytań (wersja zapoznawcza)** w menu po lewej stronie. 

    ![Strona magazynu danych SQL](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. Wprowadź nazwę **użytkownika** i **hasło** dla serwera SQL, a następnie wybierz pozycję **OK**. 

    ![Uwierzytelnianie serwera SQL](media/event-grid-event-hubs-integration/sql-server-authentication.png)
4. W oknie zapytania skopiuj i uruchom następujący skrypt SQL: 

    ```sql
    CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
        [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
        [MeasureTime] datetime NULL, 
        [GeneratedPower] float NULL, 
        [WindSpeed] float NULL, 
        [TurbineSpeed] float NULL
    )
    WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    ```

    ![Uruchamianie zapytania SQL](media/event-grid-event-hubs-integration/run-sql-query.png)
5. Nie zamykaj tej karty lub okna, aby na końcu tego samouczka można było sprawdzić, czy dane zostały utworzone. 


## <a name="publish-the-azure-functions-app"></a>Publikowanie aplikacji usługi Azure Functions

1. Uruchom program Visual Studio.
2. Otwórz rozwiązanie **EventHubsCaptureEventGridDemo.sln**, które zostało pobrane z repozytorium [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) w ramach wymagań wstępnych.
3. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **FunctionEGDWDumper** i wybierz polecenie **Opublikuj**.

   ![Publikowanie aplikacji funkcji](media/event-grid-event-hubs-integration/publish-function-app.png)
4. Jeśli zostanie wyświetlony poniższy ekran, wybierz pozycję **Uruchom**. 

   ![Przycisk uruchamiania publikowania](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. Na stronie **Wybierz miejsce docelowe publikacji** wybierz opcję **Wybierz istniejące**, a następnie wybierz pozycję **Utwórz profil**. 

   ![Wybieranie miejsca docelowego publikacji](media/event-grid-event-hubs-integration/publish-select-existing.png)
6. Na stronie usługi App Service wybierz swoją **subskrypcję platformy Azure**, wybierz **aplikację funkcji** w grupie zasobów, a następnie wybierz pozycję **OK**. 

   ![Strona usługi App Service](media/event-grid-event-hubs-integration/publish-app-service.png) 
1. Gdy program Visual Studio skonfiguruje profil, wybierz pozycję **Opublikuj**.

   ![Wybieranie pozycji Opublikuj](media/event-grid-event-hubs-integration/select-publish.png)

Po opublikowaniu funkcji możesz przystąpić do subskrybowania zdarzenia.

## <a name="subscribe-to-the-event"></a>Subskrybowanie zdarzenia

1. Na nowej karcie lub w nowym oknie przeglądarki internetowej przejdź do witryny [Azure Portal](https://portal.azure.com).
2. W witrynie Azure Portal wybierz pozycję **Grupy zasobów** w menu po lewej stronie. 
3. Przefiltruj listę grup zasobów, wprowadzając w polu wyszukiwania nazwę grupy zasobów. 
4. Wybierz grupę zasobów na liście.

    ![Wybieranie grupy zasobów](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Wybierz z listy plan usługi App Service. 
5. Na stronie Plan usługi App Service wybierz pozycję **Aplikacje** w menu po lewej stronie, a następnie wybierz aplikację funkcji. 

    ![Wybieranie aplikacji funkcji](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. Rozwiń aplikację funkcji, rozwiń funkcje, a następnie wybierz swoją funkcję. 

    ![Wybieranie funkcji platformy Azure](media/event-grid-event-hubs-integration/select-function-add-button.png)
7. Na pasku narzędzi wybierz pozycję **Dodaj subskrypcję siatki zdarzeń**. 
8. Na stronie **Tworzenie subskrypcji usługi Event Grid** wykonaj następujące czynności: 
    1. W sekcji **SZCZEGÓŁY TEMATU** wykonaj następujące czynności:
        1. Wybierz swoją subskrypcję platformy Azure.
        2. Wybierz grupę zasobów platformy Azure.
        3. Wybierz przestrzeń nazw usługi Event Hubs.
    2. Na stronie **SZCZEGÓŁY SUBSKRYPCJI ZDARZEŃ** wprowadź nazwę subskrypcji (na przykład: captureEventSub) i wybierz pozycję **Utwórz**. 

        ![Tworzenie subskrypcji usługi Event Grid](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>Uruchamianie aplikacji w celu wygenerowania danych
Konfiguracja centrum zdarzeń, magazynu danych SQL, aplikacji funkcji platformy Azure i subskrypcji zdarzeń została już ukończona. Przed uruchomieniem aplikacji, która generuje dane dla centrum zdarzeń, należy skonfigurować kilka wartości.

1. W witrynie Azure Portal jak poprzednio przejdź do grupy zasobów. 
2. Wybierz przestrzeń nazw usługi Event Hubs.
3. Na stronie **Przestrzeń nazw usługi Event Hubs** wybierz pozycję **Zasady dostępu współużytkowanego** w menu po lewej stronie.
4. Z listy zasad wybierz pozycję **RootManageSharedAccessKey**. 
5. Wybierz przycisk kopiowania obok pola tekstowego **Parametry połączenia — klucz podstawowy**. 

    ![Parametry połączenia dla przestrzeni nazw centrum zdarzeń](media/event-grid-event-hubs-integration/get-connection-string.png)
1. Wróć do rozwiązania programu Visual Studio. 
2. W projekcie WindTurbineDataGenerator otwórz plik **program.cs**.
5. Zastąp dwie wartości stałe. Dla obiektu **EventHubConnectionString** użyj wartości skopiowanej. Jako nazwy centrum zdarzeń użyj wartości **hubdatamigration**. Jeśli dla centrum zdarzeń użyto innej nazwy, określ tę nazwę. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Skompiluj rozwiązanie. Uruchom aplikację **WindTurbineGenerator.exe**. 
7. Po kilku minutach wyślij zapytanie do tabeli w magazynie danych dla migrowanych danych.

    ![Wyniki zapytania](media/event-grid-event-hubs-integration/query-results.png)

### <a name="event-data-generated-by-the-event-hub"></a>Dane zdarzeń wygenerowane przez centrum zdarzeń
Usługa Event Grid dystrybuuje dane zdarzenia do subskrybentów. Poniższy przykład przedstawia dane zdarzeń wygenerowane po przechwyceniu w obiekcie blob danych przesyłanych strumieniowo za pośrednictwem centrum zdarzeń. W szczególności zwróć uwagę, że właściwość `fileUrl` w obiekcie `data` wskazuje obiekt blob w magazynie. Aplikacja funkcji używa tego adresu URL do pobierania pliku obiektu blob zawierającego przechwycone dane.

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```


## <a name="next-steps"></a>Następne kroki

* Aby poznać różnice miedzy usługami obsługi komunikatów na platformie Azure, zobacz [Wybieranie między usługami platformy Azure dostarczającymi komunikaty](compare-messaging-services.md).
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby zapoznać się z wprowadzeniem do funkcji Event Hubs Capture, zobacz [Włączanie funkcji przechwytywania usługi Event Hubs przy użyciu witryny Azure Portal](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Aby uzyskać więcej informacji na temat konfigurowania i uruchamiania przykładu, zobacz [Przykład funkcji Event Hubs Capture i usługi Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
