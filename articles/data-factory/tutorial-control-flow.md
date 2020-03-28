---
title: Rozgałęzianie w potoku usługi Azure Data Factory
description: W tym artykule przedstawiono sposób sterowania przepływem danych w usłudze Azure Data Factory przez rozgałęzianie działań i tworzenie łańcuchów działań.
services: data-factory
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 9/27/2019
ms.openlocfilehash: 7ba921656d0dad059b1d15f443bcefeff03ade50
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75977382"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Rozgałęzianie działań i tworzenie łańcuchów działań w potoku usługi Data Factory

W tym samouczku utworzysz potok fabryki danych, który prezentuje niektóre funkcje przepływu sterowania. Ten potok jest kopiowany z kontenera w usłudze Azure Blob Storage do innego kontenera na tym samym koncie magazynu. Jeśli działanie kopiowania zakończy się pomyślnie, potok wysyła szczegóły pomyślnej operacji kopiowania w wiadomości e-mail. Informacje te mogą obejmować ilość zapisanych danych. Jeśli działanie kopiowania nie powiedzie się, wysyła szczegóły błędu kopiowania, takie jak komunikat o błędzie, w wiadomości e-mail. W samouczku pokazano, jak przekazać parametry.

Ta grafika zawiera omówienie scenariusza:

![Omówienie](media/tutorial-control-flow/overview.png)

W tym samouczku pokazano, jak wykonać następujące zadania:

> [!div class="checklist"]
> * Tworzenie fabryki danych
> * Tworzenie połączonej usługi Azure Storage
> * Tworzenie zestawu danych obiektów blob platformy Azure
> * Tworzenie potoku zawierającego działanie kopiowania i działanie internetowe
> * Wysyłanie danych wyjściowych działań do kolejnych działań
> * Używanie przekazywania parametrów i zmiennych systemowych
> * Uruchamianie potoku
> * Monitorowanie uruchomień działań i potoku

W tym samouczku jest używany zestaw SDK platformy .NET. Można użyć innych mechanizmów do interakcji z usługi Azure Data Factory. Aby zapoznać się z przewodnikami Szybki start dla fabryki danych, zobacz [5-minutowe przewodniki Szybki start](/azure/data-factory/quickstart-create-data-factory-portal).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

* konto usługi Azure Storage. Magazyn obiektów blob służy jako magazynu danych źródłowych. Jeśli nie masz konta magazynu platformy Azure, zobacz [Tworzenie konta magazynu](../storage/common/storage-account-create.md).
* Eksplorator usługi Azure Storage. Aby zainstalować to narzędzie, zobacz [Eksplorator usługi Azure Storage](https://storageexplorer.com/).
* usługą Azure SQL Database. Baza danych jest używana jako magazyn danych będący ujściem. Jeśli nie masz bazy danych SQL Azure, zobacz [Tworzenie bazy danych SQL platformy Azure.](../sql-database/sql-database-get-started-portal.md)
* Program Visual Studio. W tym artykule użyto programu Visual Studio 2019.
* Zestaw SDK platformy Azure .net. Pobierz i zainstaluj [zestaw SDK platformy Azure .NET](https://azure.microsoft.com/downloads/).

Aby uzyskać listę regionów platformy Azure, w których usługa Data Factory jest obecnie dostępna, zobacz [Produkty dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych i obliczenia mogą znajdować się w innych regionach. Magazyny obejmują usługi Azure Storage i usługi Azure SQL Database. Obliczenia obejmują HDInsight, którego używa fabryka danych.

Tworzenie aplikacji w sposób opisany w [obszarze Tworzenie aplikacji usługi Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Przypisz aplikację do roli **współautora,** postępując zgodnie z instrukcjami w tym samym artykule. Będziesz potrzebować kilku wartości dla późniejszych części tego samouczka, takich jak **identyfikator aplikacji (klienta)** i **identyfikator katalogu (dzierżawy).**

### <a name="create-a-blob-table"></a>Tworzenie tabeli obiektów blob

1. Otwórz edytor tekstów. Skopiuj następujący tekst i zapisz go lokalnie jako *input.txt*.

   ```
   Ethel|Berg
   Tamika|Walsh
   ```

1. Otwórz Eksploratora usługi Azure Storage. Rozwiń swoje konto magazynu. Kliknij prawym przyciskiem myszy pozycję **Kontenery obiektów blob** i wybierz polecenie **Utwórz kontener obiektów blob**.
1. Nazwij nowy kontener *adfv2branch* i wybierz **pozycję Przekaż,** aby dodać plik *input.txt* do kontenera.

## <a name="create-visual-studio-project"></a>Tworzenie projektu programu Visual Studio<a name="create-visual-studio-project"></a>

Utwórz aplikację konsoli języka C#:

1. Uruchom program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.
1. W **obszarze Tworzenie nowego projektu**wybierz pozycję Aplikacja konsoli **(.NET Framework)** dla języka C# i wybierz pozycję **Dalej**.
1. Nazwij projekt *ADFv2BranchTutorial*.
1. Wybierz **opcję .NET w wersji 4.5.2** lub **wyższej,** a następnie wybierz pozycję Utwórz .

### <a name="install-nuget-packages"></a>Instalowanie pakietów NuGet

1. Wybierz **opcję Konsola** > Menedżera**pakietów Menedżera** > **pakietów**Narzędzia NuGet .
1. W oknie **Konsola menedżera pakietów** uruchom następujące polecenia, aby zainstalować pakiety. Szczegółowe informacje można znaleźć w [pakiecie nuget Microsoft.Azure.Management.DataFactory.](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/)

   ```powershell
   Install-Package Microsoft.Azure.Management.DataFactory
   Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

### <a name="create-a-data-factory-client"></a>Tworzenie klienta fabryki danych

1. Otwórz *Program.cs* i dodaj następujące instrukcje:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ResourceManager;
   using Microsoft.Azure.Management.DataFactory;
   using Microsoft.Azure.Management.DataFactory.Models;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```

1. Dodaj te zmienne statyczne do `Program` klasy. Zastąp symbole zastępcze własnymi wartościami.

   ```csharp
   // Set variables
   static string tenantID = "<tenant ID>";
   static string applicationId = "<application ID>";
   static string authenticationKey = "<Authentication key for your application>";
   static string subscriptionId = "<Azure subscription ID>";
   static string resourceGroup = "<Azure resource group name>";

   static string region = "East US";
   static string dataFactoryName = "<Data factory name>";

   // Specify the source Azure Blob information
   static string storageAccount = "<Azure Storage account name>";
   static string storageKey = "<Azure Storage account key>";
   // confirm that you have the input.txt file placed in th input folder of the adfv2branch container.
   static string inputBlobPath = "adfv2branch/input";
   static string inputBlobName = "input.txt";
   static string outputBlobPath = "adfv2branch/output";
   static string emailReceiver = "<specify email address of the receiver>";

   static string storageLinkedServiceName = "AzureStorageLinkedService";
   static string blobSourceDatasetName = "SourceStorageDataset";
   static string blobSinkDatasetName = "SinkStorageDataset";
   static string pipelineName = "Adfv2TutorialBranchCopy";

   static string copyBlobActivity = "CopyBlobtoBlob";
   static string sendFailEmailActivity = "SendFailEmailActivity";
   static string sendSuccessEmailActivity = "SendSuccessEmailActivity";
   ```

1. Dodaj następujący kod do metody `Main`: Ten kod tworzy `DataFactoryManagementClient` wystąpienie klasy. Następnie można użyć tego obiektu do utworzenia fabryki danych, połączonej usługi, zestawów danych i potoku. Można również użyć tego obiektu do monitorowania szczegółów uruchomienia potoku.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
   ```

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Dodaj `CreateOrUpdateDataFactory` metodę do *pliku Program.cs:*

   ```csharp
   static Factory CreateOrUpdateDataFactory(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating data factory " + dataFactoryName + "...");
       Factory resource = new Factory
       {
           Location = region
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));

       Factory response;
       {
           response = client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, resource);
       }

       while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
       {
           System.Threading.Thread.Sleep(1000);
       }
       return response;
   }
   ```

1. Dodaj następujący wiersz `Main` do metody, która tworzy fabrykę danych:

   ```csharp
   Factory df = CreateOrUpdateDataFactory(client);
   ```

## <a name="create-an-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage

1. Dodaj `StorageLinkedServiceDefinition` metodę do *pliku Program.cs:*

   ```csharp
   static LinkedServiceResource StorageLinkedServiceDefinition(DataFactoryManagementClient client)
   {
      Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");
      AzureStorageLinkedService storageLinkedService = new AzureStorageLinkedService
      {
          ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
      };
      Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
      LinkedServiceResource linkedService = new LinkedServiceResource(storageLinkedService, name:storageLinkedServiceName);
      return linkedService;
   }
   ```

1. Dodaj następujący wiersz `Main` do metody, która tworzy usługę połączony usługi Azure Storage:

   ```csharp
   client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
   ```

Aby uzyskać więcej informacji na temat obsługiwanych właściwości i szczegółów, zobacz [Połączone właściwości usługi](connector-azure-blob-storage.md#linked-service-properties).

## <a name="create-datasets"></a>Tworzenie zestawów danych

W tej sekcji utworzysz dwa zestawy danych, jeden dla źródła i jeden dla ujścia.

### <a name="create-a-dataset-for-a-source-azure-blob"></a>Tworzenie zestawu danych dla źródłowego obiektu blob platformy Azure

Dodaj metodę, która tworzy *zestaw danych obiektów blob platformy Azure*. Aby uzyskać więcej informacji na temat obsługiwanych właściwości i szczegółów, zobacz [właściwości zestawu danych obiektów Blob platformy Azure](connector-azure-blob-storage.md#dataset-properties).

Dodaj `SourceBlobDatasetDefinition` metodę do *pliku Program.cs:*

```csharp
static DatasetResource SourceBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    {
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName,
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name:blobSourceDatasetName);
    return dataset;
}
```

Należy zdefiniować zestaw danych reprezentujący źródło danych w obiekcie blob platformy Azure. Ten zestaw danych obiektu blob odwołuje się do usługi połączonej usługi Azure Storage obsługiwane w poprzednim kroku. Zestaw danych obiektów Blob opisuje lokalizację obiektu blob do skopiowania z: *FolderPath* i *FileName*.

Zwróć uwagę na użycie parametrów dla *FolderPath*. `sourceBlobContainer`jest nazwą parametru, a wyrażenie jest zastępowane wartościami przekazanymi w przebiegu potoku. Składnia umożliwiająca zdefiniowanie parametrów: `@pipeline().parameters.<parameterName>`

### <a name="create-a-dataset-for-a-sink-azure-blob"></a>Tworzenie zestawu danych dla ujścia obiektu Blob platformy Azure

1. Dodaj `SourceBlobDatasetDefinition` metodę do *pliku Program.cs:*

   ```csharp
   static DatasetResource SinkBlobDatasetDefinition(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
       AzureBlobDataset blobDataset = new AzureBlobDataset
       {
           FolderPath = new Expression { Value = "@pipeline().parameters.sinkBlobContainer" },
           LinkedServiceName = new LinkedServiceReference
           {
               ReferenceName = storageLinkedServiceName
           }
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
       DatasetResource dataset = new DatasetResource(blobDataset, name: blobSinkDatasetName);
       return dataset;
   }
   ```

1. Dodaj następujący kod `Main` do metody, która tworzy zarówno źródło obiektów blob platformy Azure, jak i zestawy danych ujścia.

   ```csharp
   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
   ```

## <a name="create-a-c-class-emailrequest"></a>Tworzenie klasy języka C#: EmailRequest

W projekcie języka C# utwórz klasę o nazwie `EmailRequest`. Ta klasa definiuje, jakie właściwości potok wysyła w żądaniu treści podczas wysyłania wiadomości e-mail. W tym samouczku potok wysyła cztery właściwości z potoku do wiadomości e-mail:

* Komunikat. Treść wiadomości e-mail. Dla pomyślnej kopii ta właściwość zawiera ilość danych zapisanych. W przypadku kopii nie powiodło się ta właściwość zawiera szczegóły błędu.
* Nazwa fabryki danych. Nazwa fabryki danych.
* Nazwa potoku. Nazwa potoku.
* Odbiornik. Parametr, który przechodzi. Ta właściwość określa odbiorcę wiadomości e-mail.

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```

## <a name="create-email-workflow-endpoints"></a>Tworzenie punktów końcowych przepływu pracy poczty e-mail

Aby wyzwolić wysyłanie wiadomości e-mail, zdefiniuj przepływ pracy przy użyciu usługi [Logic Apps](../logic-apps/logic-apps-overview.md). Aby uzyskać szczegółowe informacje na temat tworzenia przepływu pracy aplikacji logiki, zobacz [Jak utworzyć aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

### <a name="success-email-workflow"></a>Przepływ pracy wiadomości e-mail z informacją o powodzeniu

W [witrynie Azure portal](https://portal.azure.com)utwórz przepływ pracy aplikacji logiki o nazwie *CopySuccessEmail*. Zdefiniuj `When an HTTP request is received`wyzwalacz przepływu pracy jako . W wyzwalaczu żądania wypełnij pole `Request Body JSON Schema` przy użyciu następującego kodu JSON:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

Przepływ pracy wygląda mniej więcej w następującym przykładzie:

![Przepływ pracy wiadomości e-mail z informacją o powodzeniu](media/tutorial-control-flow/success-email-workflow-trigger.png)

Ta zawartość JSON jest `EmailRequest` wyrównana z klasą utworzoną w poprzedniej sekcji.

Dodaj akcję `Office 365 Outlook – Send an email`. Dla **akcji Wyślij wiadomość e-mail** dostosuj sposób formatowania wiadomości e-mail przy użyciu właściwości przekazanych w schemacie JSON **treści** żądania. Oto przykład:

![Projektant aplikacji logiki — wysyłanie akcji e-mail](media/tutorial-control-flow/customize-send-email-action.png)

Po zapisaniu przepływu pracy skopiuj i zapisz wartość **adresu URL HTTP POST** z wyzwalacza.

## <a name="fail-email-workflow"></a>Przepływ pracy wiadomości e-mail z informacją o niepowodzeniu

**Sklonuj CopySuccessEmail** jako inny przepływ pracy aplikacji logiki o nazwie *CopyFailEmail*. W wyzwalaczu żądania element `Request Body JSON schema` jest taki sam. Zmień format wiadomości e-mail, na przykład element `Subject`, aby przekształcić wiadomość e-mail w wiadomość z informacją o niepowodzeniu. Oto przykład:

![Projektant aplikacji logiki — niepowodzenie przepływu pracy poczty e-mail](media/tutorial-control-flow/fail-email-workflow.png)

Po zapisaniu przepływu pracy skopiuj i zapisz wartość **adresu URL HTTP POST** z wyzwalacza.

Teraz powinny być dwa adresy URL przepływu pracy, takie jak następujące przykłady:

```csharp
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-pipeline"></a>Tworzenie potoku

Wróć do projektu w programie Visual Studio. Teraz dodamy kod, który tworzy potok z `DependsOn` działaniem kopiowania i właściwością. W tym samouczku potok zawiera jedno działanie, działanie kopiowania, które przyjmuje w zestawie danych obiektów blob jako źródło i inny zestaw danych obiektu blob jako ujście. Jeśli działanie kopiowania zakończy się powodzeniem lub niepowodzeniem, wywołuje różne zadania poczty e-mail.

W tym potoku użyto następujących funkcji:

* Parametry
* Aktywność w sieci Web
* Zależność działania
* Używanie danych wyjściowych z działania jako danych wejściowych do innego działania

1. Dodaj tę metodę do projektu. Poniższe sekcje zawierają bardziej szczegółowe informacje.

    ```csharp
    static PipelineResource PipelineDefinition(DataFactoryManagementClient client)
            {
                Console.WriteLine("Creating pipeline " + pipelineName + "...");
                PipelineResource resource = new PipelineResource
                {
                    Parameters = new Dictionary<string, ParameterSpecification>
                    {
                        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "receiver", new ParameterSpecification { Type = ParameterType.String } }

                    },
                    Activities = new List<Activity>
                    {
                        new CopyActivity
                        {
                            Name = copyBlobActivity,
                            Inputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSourceDatasetName
                                }
                            },
                            Outputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSinkDatasetName
                                }
                            },
                            Source = new BlobSource { },
                            Sink = new BlobSink { }
                        },
                        new WebActivity
                        {
                            Name = sendSuccessEmailActivity,
                            Method = WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/00000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Succeeded" }
                                }
                            }
                        },
                        new WebActivity
                        {
                            Name = sendFailEmailActivity,
                            Method =WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Failed" }
                                }
                            }
                        }
                    }
                };
                Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));
                return resource;
            }
    ```

1. Dodaj następujący wiersz `Main` do metody, która tworzy potok:

   ```csharp
   client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
   ```

### <a name="parameters"></a>Parametry

Pierwsza sekcja naszego kodu rurociągu definiuje parametry.

* `sourceBlobContainer`. Źródłowy zestaw danych obiektów blob zużywa ten parametr w potoku.
* `sinkBlobContainer`. Zestaw danych obiektu blob ujścia zużywa ten parametr w potoku.
* `receiver`. Dwa działania sieci Web w potoku, które wysyłają wiadomości e-mail o powodzenie lub niepowodzenie do odbiornika używać tego parametru.

```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```

### <a name="web-activity"></a>Aktywność w sieci Web

Działanie sieci Web umożliwia wywołanie dowolnego punktu końcowego REST. Aby uzyskać więcej informacji na temat działania, zobacz [Aktywność sieci Web w usłudze Azure Data Factory](control-flow-web-activity.md). Ten potok używa działania sieci web do wywołania przepływu pracy poczty e-mail aplikacji logiki. Tworzysz dwa działania internetowe: jedno, `CopySuccessEmail` które wywołuje przepływ `CopyFailWorkFlow`pracy i jedno, które wywołuje plik .

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```

We `Url` właściwości wklej punkty końcowe **adresu URL HTTP POST** z przepływów pracy aplikacji logiki. We `Body` właściwości przekazać wystąpienie `EmailRequest` klasy. Żądanie wiadomości e-mail zawiera następujące właściwości:

* Komunikat. Przekazuje wartość `@{activity('CopyBlobtoBlob').output.dataWritten`. Uzyskuje dostęp do właściwości poprzedniego działania kopiowania `dataWritten`i przekazuje wartość . W przypadku wiadomości dotyczącej niepowodzenia przekaż dane wyjściowe błędu zamiast elementu `@{activity('CopyBlobtoBlob').error.message`.
* Nazwa fabryki danych. Przekazuje wartość `@{pipeline().DataFactory}` Tej zmiennej systemowej umożliwia dostęp do odpowiedniej nazwy fabryki danych. Aby uzyskać listę zmiennych systemowych, zobacz [Zmienne systemowe](control-flow-system-variables.md).
* Nazwa potoku. Przekazuje wartość `@{pipeline().Pipeline}`. Ta zmienna systemowa umożliwia dostęp do odpowiedniej nazwy potoku.
* Odbiornik. Przekazuje wartość `"@pipeline().parameters.receiver"`. Uzyskuje dostęp do parametrów potoku.

Ten kod tworzy nową zależność działania, która zależy od poprzedniego działania kopiowania.

## <a name="create-a-pipeline-run"></a>Tworzenie uruchomienia potoku

Dodaj następujący kod `Main` do metody, która wyzwala uruchomienie potoku.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="main-class"></a>Klasa metody Main

Twoja `Main` ostateczna metoda powinna wyglądać tak.

```csharp
// Authenticate and create a data factory management client
var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };

Factory df = CreateOrUpdateDataFactory(client);

client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));

Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

Skompiluj i uruchom program, aby wyzwolić uruchomienie potoku.

## <a name="monitor-a-pipeline-run"></a>Monitorowanie uruchomienia potoku

1. Dodaj następujący kod do metody `Main`:

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

    Ten kod stale sprawdza stan uruchomienia, dopóki nie zakończy kopiowanie danych.

1. Dodaj następujący kod `Main` do metody, która pobiera szczegóły uruchamiania działania kopiowania, na przykład rozmiar danych odczytu/zapisu:

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList();

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Uruchamianie kodu

Skompiluj i uruchom aplikację, a następnie zweryfikuj wykonywanie potoku.

Aplikacja wyświetla postęp tworzenia fabryki danych, połączonej usługi, zestawów danych, potoku i uruchomienia potoku. Następnie sprawdza stan uruchomienia potoku. Poczekaj na wyświetlenie szczegółów uruchomienia działania kopiowania z rozmiarem odczytanych/zapisanych danych. Następnie użyj narzędzi, takich jak Eksplorator usługi Azure Storage, aby sprawdzić obiekt blob został skopiowany do *outputBlobPath* z *inputBlobPath,* jak określono w zmiennych.

Dane wyjściowe powinny przypominać następującą próbkę:

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
  }
}
Creating dataset SourceStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sourceBlobContainer"
    },
    "fileName": "input.txt"
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating dataset SinkStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sinkBlobContainer"
    }
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating pipeline Adfv2TutorialBranchCopy...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SourceStorageDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SinkStorageDataset"
          }
        ],
        "name": "CopyBlobtoBlob"
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendSuccessEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Succeeded"
            ]
          }
        ]
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendFailEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Failed"
            ]
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      },
      "receiver": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 00000000-0000-0000-0000-0000000000000
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 20,
  "dataWritten": 20,
  "copyDuration": 4,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
}
{}

Press any key to exit...
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonaliśmy następujące zadania:

> [!div class="checklist"]
> * Tworzenie fabryki danych
> * Tworzenie połączonej usługi Azure Storage
> * Tworzenie zestawu danych obiektów blob platformy Azure
> * Tworzenie potoku zawierającego działanie kopiowania i działanie internetowe
> * Wysyłanie danych wyjściowych działań do kolejnych działań
> * Używanie przekazywania parametrów i zmiennych systemowych
> * Uruchamianie potoku
> * Monitorowanie uruchomień działań i potoku

Teraz możesz przejść do sekcji Pojęcia, aby uzyskać więcej informacji na temat usługi Azure Data Factory.
> [!div class="nextstepaction"]
>[Potoki i działania](concepts-pipelines-activities.md)
