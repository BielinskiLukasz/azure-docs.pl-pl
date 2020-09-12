---
title: Monitorowanie usługi Azure Storage | Microsoft Docs
description: Dowiedz się, jak monitorować wydajność i dostępność usługi Azure Storage. Monitoruj dane usługi Azure Storage, Poznaj konfigurację i analizuj dane dotyczące metryk i dzienników.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 0edb50fd72622d3d7d628e0e02ef2c3737f8713a
ms.sourcegitcommit: 206629373b7c2246e909297d69f4fe3728446af5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2020
ms.locfileid: "89500423"
---
# <a name="monitoring-azure-storage"></a>Monitorowanie usługi Azure Storage

Jeśli masz krytyczne aplikacje i procesy biznesowe, które opierają się na zasobach platformy Azure, chcesz monitorować te zasoby pod kątem ich dostępności, wydajności i operacji. W tym artykule opisano dane monitorowania, które są generowane przez usługę Azure Storage, oraz sposób używania funkcji Azure Monitor do analizowania alertów dotyczących tych danych.

> [!NOTE]
> Dzienniki usługi Azure Storage w Azure Monitor są w publicznej wersji zapoznawczej i są dostępne do testowania wersji zapoznawczej we wszystkich regionach chmury publicznej. Aby zarejestrować się w wersji zapoznawczej, zobacz [Tę stronę](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Ta wersja zapoznawcza umożliwia korzystanie z dzienników dla obiektów BLOB (w tym Azure Data Lake Storage Gen2), plików, kolejek i tabel. Ta funkcja jest dostępna dla wszystkich kont magazynu utworzonych za pomocą modelu wdrażania Azure Resource Manager. Zobacz [Omówienie konta magazynu](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Przegląd monitora

Na stronie **Przegląd** w Azure Portal poszczególnych zasobów magazynu znajduje się krótki widok użycia zasobów, na przykład żądania i rozliczenia godzinowe. Te informacje są przydatne, ale dostępna jest tylko niewielka ilość danych monitorowania. Niektóre z tych danych są zbierane automatycznie i są dostępne do analizy zaraz po utworzeniu zasobu magazynu. Dodatkowe typy zbierania danych można włączyć za pomocą jakiejś konfiguracji.

## <a name="what-is-azure-monitor"></a>Co to jest Azure Monitor?
Usługa Azure Storage tworzy dane monitorowania za pomocą [Azure monitor](../../azure-monitor/overview.md), który jest pełną usługą monitorowania stosu na platformie Azure. Azure Monitor oferuje pełny zestaw funkcji służących do monitorowania zasobów i zasobów platformy Azure w innych chmurach i lokalnych. 

Rozpocznij od artykułu [monitorowanie zasobów platformy Azure za pomocą Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md) , w którym opisano następujące kwestie:

- Co to jest Azure Monitor?
- Koszty związane z monitorowaniem
- Monitorowanie danych zebranych na platformie Azure
- Konfigurowanie zbierania danych
- Standardowe narzędzia na platformie Azure na potrzeby analizowania danych monitorowania i powiadamiania o nich

Poniższe sekcje dotyczą tego artykułu, opisując szczegółowe dane zebrane z usługi Azure Storage. Przykłady pokazują, jak skonfigurować zbieranie danych i analizować je za pomocą narzędzi platformy Azure.

## <a name="monitoring-data-from-azure-storage"></a>Monitorowanie danych z usługi Azure Storage

Usługa Azure Storage zbiera te same dane monitorowania jak inne zasoby platformy Azure, które są opisane w temacie [monitorowanie danych z zasobów platformy Azure](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Aby uzyskać więcej informacji na temat dzienników i metryk utworzonych przez usługę Azure Storage, zobacz [Informacje o danych monitorowania usługi Azure Storage](monitor-storage-reference.md).

Metryki i dzienniki w Azure Monitor obsługują tylko Azure Resource Manager kont magazynu. Azure Monitor nie obsługuje klasycznych kont magazynu. Jeśli chcesz użyć metryk lub dzienników na klasycznym koncie magazynu, musisz przeprowadzić migrację do konta magazynu Azure Resource Manager. Zobacz [Migrowanie do Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

Jeśli chcesz, możesz nadal korzystać z klasycznych metryk i dzienników. W rzeczywistości klasyczne metryki i dzienniki są dostępne równolegle z metrykami i dziennikami w Azure Monitor. Obsługa będzie obowiązywać do momentu zakończenia usługi Azure Storage w ramach starszych metryk i dzienników.

### <a name="logs-in-azure-monitor-preview"></a>Dzienniki w Azure Monitor (wersja zapoznawcza)

Wpisy dziennika są tworzone tylko wtedy, gdy istnieją żądania skierowane do punktu końcowego usługi. Na przykład jeśli konto magazynu ma aktywność w swoim punkcie końcowym obiektu BLOB, ale nie znajduje się w jego punktach końcowych tabeli lub kolejki, tworzone są tylko dzienniki odnoszące się do usługi BLOB Service. Dzienniki usługi Azure Storage zawierają szczegółowe informacje na temat żądań zakończonych powodzeniem i zakończonych niepowodzeniem w usłudze magazynu. Tych informacji można używać na potrzeby monitorowania poszczególnych żądań i diagnozowania problemów z usługą magazynu. Żądania są rejestrowane na podstawie najlepszego wysiłku.

#### <a name="log-authenticated-requests"></a>Rejestruj uwierzytelnione żądania

 Rejestrowane są następujące typy żądań uwierzytelnionych:

- Żądania zakończone powodzeniem
- Żądania zakończone niepowodzeniem, w tym błędy limitu czasu, ograniczania przepustowości, sieci, autoryzacji i inne błędy
- Żądania używające sygnatury dostępu współdzielonego (SAS) lub OAuth, w tym żądania zakończone niepowodzeniem i zakończone powodzeniem
- Żądania danych analitycznych (klasyczne dane dziennika w **$Logs** kontenera i danych metryk klas w tabelach **$Metric** )

Żądania wykonywane przez samą usługę magazynu, takie jak tworzenie lub usuwanie dziennika, nie są rejestrowane. Aby uzyskać pełną listę zarejestrowanych danych, zobacz [zarejestrowane operacje magazynu i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) oraz [format dziennika magazynu](monitor-storage-reference.md).

#### <a name="log-anonymous-requests"></a>Rejestruj anonimowe żądania

 Rejestrowane są następujące typy żądań anonimowych:

- Żądania zakończone powodzeniem
- Błędy serwera
- Błędy limitu czasu dla klienta i serwera
- Żądania GET zakończone niepowodzeniem z kodem błędu 304 (nie zmodyfikowano)

Wszystkie inne Nieudane żądania anonimowe nie są rejestrowane. Aby uzyskać pełną listę zarejestrowanych danych, zobacz [zarejestrowane operacje magazynu i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) oraz [format dziennika magazynu](monitor-storage-reference.md).

## <a name="configuration"></a>Konfigurowanie

Metryki platformy i dziennik aktywności są zbierane automatycznie, ale należy utworzyć ustawienie diagnostyczne, aby zbierać dzienniki zasobów lub przesyłać je dalej poza Azure Monitor. Aby proces tworzenia ustawień diagnostycznych przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell, zobacz [Tworzenie ustawień diagnostycznych w celu zbierania dzienników platformy i metryk na platformie Azure](../../azure-monitor/platform/diagnostic-settings.md).

Podczas tworzenia ustawienia diagnostycznego wybierz typ magazynu, dla którego chcesz włączyć dzienniki, takie jak obiekt BLOB, kolejka, tabela lub plik. Data Lake Storage Gen2 nie jest wyświetlana jako typ magazynu. Jest to spowodowane tym, że Data Lake Storage Gen2 jest zestawem funkcji dostępnych dla magazynu obiektów BLOB. 

W przypadku utworzenia ustawienia diagnostycznego w Azure Portal można wybrać zasób z listy. Jeśli używasz programu PowerShell lub interfejsu wiersza polecenia platformy Azure, musisz użyć identyfikatora zasobu typu magazyn. Identyfikator zasobu można znaleźć w Azure Portal, otwierając stronę **Właściwości** konta magazynu.

Należy również określić kategorie operacji, dla których mają być zbierane dzienniki. Kategorie usługi Azure Storage są wymienione w tej tabeli.

| Kategoria | Opis |
|:---|:---|
| StorageRead | Operacje odczytu na obiektach. |
| StorageWrite | Operacje zapisu w obiektach. |
| StorageDelete | Operacje usuwania obiektów. |

## <a name="analyzing-metric-data"></a>Analizowanie danych metryki

Metryki usługi Azure Storage można analizować za pomocą metryk z innych usług platformy Azure, korzystając z Eksplorator metryk. Otwórz Eksplorator metryk, wybierając **metryki** z menu **Azure monitor** . Aby uzyskać szczegółowe informacje na temat korzystania z tego narzędzia, zobacz [Rozpoczynanie pracy z usługą Azure Eksplorator metryk](../../azure-monitor/platform/metrics-getting-started.md). 

Ten przykład pokazuje sposób wyświetlania **transakcji** na poziomie konta.

![Zrzut ekranu przedstawiający dostęp do metryk w Azure Portal](./media/monitor-storage/access-metrics-portal.png)

W przypadku metryk, które obsługują wymiary, można filtrować metrykę przy użyciu żądanej wartości wymiaru. Ten przykład pokazuje, jak wyświetlić **transakcje** na poziomie konta w określonej operacji, wybierając wartości dla wymiaru **nazwa interfejsu API** .

![Zrzut ekranu przedstawiający dostęp do metryk z wymiarem w Azure Portal](./media/monitor-storage/access-metrics-portal-with-dimension.png)

Aby uzyskać pełną listę wymiarów obsługiwanych przez usługę Azure Storage, zobacz [Dimension Metrics](monitor-storage-reference.md#metrics-dimensions).

Wszystkie metryki usługi Azure Storage znajdują się w następujących przestrzeniach nazw:

- Microsoft. Storage/storageAccounts
- Microsoft. Storage/storageAccounts/blobServices
- Microsoft. Storage/storageAccounts/fileServices
- Microsoft. Storage/storageAccounts/queueServices
- Microsoft. Storage/storageAccounts/tableServices

Aby uzyskać listę wszystkich Azure Monitor metryki pomocy technicznej, w tym usługi Azure Storage, zobacz [Azure monitor obsługiwane metryki](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported).


### <a name="accessing-metrics"></a>Uzyskiwanie dostępu do metryk

> [!TIP]
> Aby wyświetlić przykłady interfejsu wiersza polecenia platformy Azure lub platformy .NET, wybierz odpowiednie karty wymienione w tym miejscu.

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Lista definicji metryk

Można wyświetlić listę definicji metryk konta magazynu lub poszczególnych usług magazynu, takich jak obiekt BLOB, plik, tabela lub usługa kolejki. Użyj polecenia cmdlet [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition?view=azps-3.3.0) .

W tym przykładzie Zastąp `<resource-ID>` symbol zastępczy identyfikatorem zasobu całego konta magazynu lub identyfikatorem zasobu pojedynczej usługi magazynu, takim jak obiekt BLOB, plik, tabela lub usługa kolejki. Te identyfikatory zasobów można znaleźć na stronach **Właściwości** konta magazynu w Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Odczytywanie wartości metryk

Można odczytywać wartości metryk na poziomie konta magazynu lub poszczególnych usług magazynu, takich jak obiekt BLOB, plik, tabela lub usługa kolejki. Użyj polecenia cmdlet [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric?view=azps-3.3.0) .

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Utwórz listę definicji metryk na poziomie konta

Można wyświetlić listę definicji metryk konta magazynu lub poszczególnych usług magazynu, takich jak obiekt BLOB, plik, tabela lub usługa kolejki. Użyj polecenia [AZ monitor Metric list-](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list-definitions) Definitions.
 
W tym przykładzie Zastąp `<resource-ID>` symbol zastępczy identyfikatorem zasobu całego konta magazynu lub identyfikatorem zasobu pojedynczej usługi magazynu, takim jak obiekt BLOB, plik, tabela lub usługa kolejki. Te identyfikatory zasobów można znaleźć na stronach **Właściwości** konta magazynu w Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Odczytaj wartości metryk na poziomie konta

Można odczytać wartości metryk konta magazynu lub poszczególnych usług magazynu, takich jak obiekt BLOB, plik, tabela lub usługa kolejki. Użyj polecenia [AZ monitor Metric list](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) .

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor udostępnia [zestaw .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) do odczytywania definicji metryk i wartości. [Przykładowy kod](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) pokazuje, jak używać zestawu SDK z innymi parametrami. Aby `0.18.0-preview` uzyskać metryki magazynu, należy użyć lub nowszej wersji.
 
W tych przykładach Zastąp `<resource-ID>` symbol zastępczy identyfikatorem zasobu całego konta magazynu lub identyfikatorem zasobu pojedynczej usługi magazynu, takim jak obiekt BLOB, plik, tabela lub usługa kolejki. Te identyfikatory zasobów można znaleźć na stronach **Właściwości** konta magazynu w Azure Portal.

Zastąp `<subscription-ID>` ZMIENNĄ identyfikatorem subskrypcji. Aby uzyskać wskazówki dotyczące uzyskiwania wartości dla `<tenant-ID>` , `<application-ID>` i `<AccessKey>` , zobacz [Korzystanie z portalu do tworzenia aplikacji usługi Azure AD i nazwy głównej usługi, która może uzyskiwać dostęp do zasobów](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/). 

#### <a name="list-the-account-level-metric-definition"></a>Utwórz listę definicji metryk na poziomie konta

Poniższy przykład pokazuje, jak wyświetlić definicję metryki na poziomie konta:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

#### <a name="reading-account-level-metric-values"></a>Odczytywanie wartości metryk na poziomie konta

Poniższy przykład pokazuje, jak odczytywać `UsedCapacity` dane na poziomie konta:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            // Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

#### <a name="reading-multidimensional-metric-values"></a>Odczytywanie wartości metryk wielowymiarowych

W przypadku metryk wielowymiarowych należy zdefiniować filtry metadanych, jeśli chcesz odczytywać dane metryk dla określonych wartości wymiarów.

Poniższy przykład pokazuje, jak odczytywać dane metryki z obsługą wielowymiarową metryki:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

---

## <a name="analyzing-log-data"></a>Analizowanie danych dziennika

Można uzyskać dostęp do dzienników zasobów jako obiektów BLOB na koncie magazynu, jako dane zdarzenia lub za pomocą zapytań analitycznych dzienników.

Aby uzyskać szczegółowe informacje na temat pól, które pojawiają się w tych dziennikach, zobacz [dokumentacja danych monitorowania usługi Azure Storage](monitor-storage-reference.md).

> [!NOTE]
> Dzienniki usługi Azure Storage w Azure Monitor są w publicznej wersji zapoznawczej i są dostępne do testowania wersji zapoznawczej we wszystkich regionach chmury publicznej. Aby zarejestrować się w wersji zapoznawczej, zobacz [Tę stronę](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Ta wersja zapoznawcza umożliwia korzystanie z dzienników obiektów BLOB (w tym Azure Data Lake Storage Gen2), plików, kolejek, tabel, kont magazynu w warstwie Premium w celu ogólnego przeznaczenia w wersji 1 i w wersji 2. Klasyczne konta magazynu nie są obsługiwane.

### <a name="accessing-logs-in-a-storage-account"></a>Uzyskiwanie dostępu do dzienników na koncie magazynu

Dzienniki są wyświetlane jako obiekty blob przechowywane w kontenerze na docelowym koncie magazynu. Dane są zbierane i przechowywane w pojedynczym obiekcie BLOB jako ładunek JSON rozdzielany wierszami. Nazwa obiektu BLOB jest zgodna z tą konwencją nazewnictwa:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Oto przykład:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Uzyskiwanie dostępu do dzienników w centrum zdarzeń

Dzienniki wysyłane do centrum zdarzeń nie są przechowywane jako plik, ale można sprawdzić, czy centrum zdarzeń otrzymało informacje dziennika. W Azure Portal przejdź do centrum zdarzeń i sprawdź, czy liczba **przychodzących komunikatów** jest większa od zera. 

![Dzienniki inspekcji](media/monitor-storage/event-hub-log.png)

Możesz uzyskiwać dostęp do danych dziennika, które są wysyłane do centrum zdarzeń, i je odczytywać, korzystając z informacji o zabezpieczeniach i narzędzi do monitorowania. Aby uzyskać więcej informacji, zobacz [co można zrobić przy użyciu danych monitorowania wysyłanych do centrum zdarzeń?](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Uzyskiwanie dostępu do dzienników w obszarze roboczym Log Analytics

Można uzyskać dostęp do dzienników wysyłanych do obszaru roboczego Log Analytics przy użyciu zapytań dziennika Azure Monitor.

Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z log Analytics w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

Dane są przechowywane w tych tabelach.

| Tabela | Opis |
|:---|:---|
|StorageBlobLogs | Dzienniki opisujące działanie w usłudze BLOB Storage. |
|StorageFileLogs | Dzienniki opisujące działanie w udziałach plików. |
|StorageQueueLogs | Dzienniki opisujące działanie w kolejkach.|
|StorageTableLogs| Dzienniki opisujące działanie w tabelach.|

Dzienniki dla Data Lake Storage Gen2 nie są wyświetlane w dedykowanej tabeli. Jest to spowodowane tym, że Data Lake Storage Gen2 nie jest usługą. Jest to zestaw funkcji, które można włączyć na koncie magazynu obiektów BLOB. Jeśli te funkcje zostały włączone, dzienniki będą nadal wyświetlane w tabeli StorageBlobLogs. 

### <a name="azure-storage-log-analytics-queries-in-azure-monitor"></a>Log Analytics zapytań usługi Azure Storage w Azure Monitor

Poniżej przedstawiono niektóre zapytania, które można wprowadzić na pasku **przeszukiwania dzienników** , aby ułatwić monitorowanie kont usługi Azure Storage. Te zapytania działają w [nowym języku](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

Użyj tych zapytań, aby ułatwić monitorowanie kont usługi Azure Storage:

* Aby wyświetlić listę 10 najczęstszych błędów w ciągu ostatnich trzech dni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* Aby wyświetlić 10 najważniejszych operacji, które spowodowały najwięcej błędów w ciągu ostatnich trzech dni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* Aby wyświetlić 10 najważniejszych operacji z najdłuższym opóźnieniem zakończonym w ciągu ostatnich trzech dni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* Aby wyświetlić listę wszystkich operacji, które spowodowały błędy ograniczania wydajności po stronie serwera w ciągu ostatnich trzech dni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* Aby wyświetlić listę wszystkich żądań z dostępem anonimowym w ciągu ostatnich trzech dni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* Aby utworzyć wykres kołowy operacji używanych w ciągu ostatnich trzech dni.
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>Często zadawane pytania

**Czy usługa Azure Storage obsługuje metryki dla dysków Managed Disks lub niezarządzanych?**

Nie. Usługa Azure COMPUTE obsługuje metryki na dyskach. Aby uzyskać więcej informacji, zobacz [metryki na dysku dla dysków zarządzanych i niezarządzanych](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat dzienników i metryk utworzonych przez usługę Azure Storage, zobacz [Informacje o danych monitorowania usługi Azure Storage](monitor-storage-reference.md).
- Aby uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure, zobacz [monitorowanie zasobów platformy Azure za pomocą Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md).
- Aby uzyskać więcej informacji na temat migracji metryk, zobacz [migracja metryk usługi Azure Storage](./storage-metrics-migration.md).
