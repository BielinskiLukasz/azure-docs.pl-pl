---
title: Rejestrowanie diagnostyczne usługi Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Użyj tego samouczka, aby pomóc Ci rozpocząć pracę z usługą Azure Cosmos DB rejestrowania.
services: cosmos-db
author: SnehaGunda
manager: kfile
tags: azure-resource-manager
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2018
ms.author: sngun
ms.openlocfilehash: 68eb567235897641d5d4027160f62c5aa6e7e4f9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963393"
---
# <a name="azure-cosmos-db-diagnostic-logging"></a>Usługa Azure Cosmos DB rejestrowanie diagnostyczne

Po uruchomieniu używać jednego lub więcej baz danych Azure Cosmos DB, warto monitorować sposobem i czasem baz danych są dostępne. Ten artykuł zawiera omówienie dzienników, które są dostępne na platformie Azure. Dowiesz się, jak włączyć rejestrowanie diagnostyczne do celów, aby wysłać dzienniki do monitorowania [usługi Azure Storage](https://azure.microsoft.com/services/storage/), jak przesyłać strumieniowo dzienniki, aby [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)i jak dzienniki, aby wyeksportować [usługi Azure Log Analytics ](https://azure.microsoft.com/services/log-analytics/).

## <a name="logs-available-in-azure"></a>Dzienniki dostępnych na platformie Azure

Zanim będziemy wyjaśniać, jak monitorować konto usługi Azure Cosmos DB, możemy wyjaśnić kilka kwestii dotyczących rejestrowania i monitorowania. Istnieją różne typy dzienników na platformie Azure. Istnieją [dzienników aktywności platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [dzienniki diagnostyczne platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), [metryki platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), zdarzenia, pulsu, dzienniki operacji i tak dalej. Istnieje mnóstwo dzienniki. Można wyświetlić pełną listę dzienników w [usługi Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/) w witrynie Azure portal. 

Na poniższej ilustracji przedstawiono różne rodzaje dzienniki platformy Azure, które są dostępne:

![Różne rodzaje dzienniki platformy Azure](./media/logging/azurelogging.png)

Na ilustracji **zasoby obliczeniowe** reprezentują zasoby platformy Azure, w których mogą uzyskiwać dostęp systemu operacyjnego gościa firmy Microsoft. Na przykład maszyny wirtualne platformy Azure, maszyny wirtualnej zestawy skalowania, Azure Container Service i itd., czy zasoby obliczeniowe uważane. Obliczeniowe zasobów generować Dzienniki aktywności, dzienniki diagnostyczne i dzienniki aplikacji. Aby dowiedzieć się więcej, zapoznaj się [źródła danych na platformie Azure monitorowania](../monitoring/monitoring-data-sources.md#) artykułu.

**Zasobów obliczeniowych bez** zasobów, w których nie dostęp do podstawowego systemu operacyjnego i pracy bezpośrednio z zasobem. Na przykład sieciowe grupy zabezpieczeń, Logic Apps i tak dalej. Usługa Azure Cosmos DB jest zasobem poza obliczeniowymi. Możesz wyświetlić dzienniki za zasoby obliczeniowe bez w dzienniku aktywności lub Włącz opcję dzienników diagnostycznych, w portalu. Aby dowiedzieć się więcej, zapoznaj się [źródeł danych w usłudze Azure Monitor](../monitoring/monitoring-data-sources.md) artykułu.

Dziennik aktywności rejestruje operacje na poziomie subskrypcji dla usługi Azure Cosmos DB. Operacje, takie jak klucze list, DatabaseAccounts zapisu i inne są rejestrowane. Dzienniki diagnostyczne zapewniają bardziej szczegółowego rejestrowania i pozwalają na DataPlaneRequests (tworzenia, odczytu, zapytania i tak dalej) i MongoRequests dziennika.


W tym artykule skupimy się na dziennika aktywności platformy Azure, dzienniki diagnostyczne platformy Azure i metryki platformy Azure. Jaka jest różnica między te trzy dzienniki? 

### <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure

Dziennik aktywności platformy Azure jest Dziennik subskrypcji, który zapewnia wgląd w zdarzenia na poziomie subskrypcji, które miały miejsce w systemie Azure. Dziennik aktywności raporty zdarzeń płaszczyznę kontroli dla subskrypcji w obszarze kategorii administracyjnej. Dziennika aktywności można użyć, aby określić ", co, kto i kiedy" dla wszelkie zapisu operacji (PUT, POST, DELETE) dla zasobów w ramach subskrypcji. Dodatkowo użytkownik rozumie stanu operacji i inne odpowiednie właściwości. 

Dziennik aktywności różni się od dzienników diagnostycznych. Dziennik aktywności zawiera dane dotyczące operacji dla zasobu z zewnątrz ( _płaszczyznę kontroli_). W kontekście usługi Azure Cosmos DB płaszczyznę kontroli, które operacje obejmują tworzenie kontenera, klucze listy, usuwanie kluczy, bazy danych z listy i tak dalej. Dzienniki diagnostyczne są emitowane przez zasób i podaj informacje o działaniu tego zasobu ( _płaszczyzny danych_). Niektóre przykłady operacje płaszczyzny danych w dzienniku diagnostyczne są Delete, Insert i ReadFeed.

Dzienniki aktywności (operacje warstwy kontroli) może być bardziej rozbudowane z natury i mogą obejmować pełny adres e-mail obiektu wywołującego, adres IP obiektu wywołującego, nazwa zasobu, nazwy operacji, identyfikator dzierżawy i więcej. Dziennik aktywności zawiera kilka [kategorie](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) danych. Aby uzyskać szczegółowe informacje o wypełniana z tych kategorii, zobacz [schemat zdarzeń dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema). Jednak dzienniki diagnostyczne mogą być ograniczające z natury danych osobowych jest często usunięte z tych dzienników. Może mieć adres IP obiektu wywołującego, ale ostatni octant zostanie usunięty.

### <a name="azure-metrics"></a>Metryki platformy Azure

[Metryki platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) mają typ najważniejszych danych telemetrycznych platformy Azure (nazywanych również _liczniki wydajności_) emitowanej zasobów najbardziej platformy Azure. Metryki umożliwiają wyświetlanie informacji o przepływności, magazynu, spójnością, dostępnością i opóźnieniem równym zasobami usługi Azure Cosmos DB. Aby uzyskać więcej informacji, zobacz [monitorowania i debugowania przy użyciu metryk w usłudze Azure Cosmos DB](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Dzienniki diagnostyczne platformy Azure

Dzienniki diagnostyczne platformy Azure są emitowane przez zasób i zapewniają bogate, często dane o działaniu tego zasobu. Zawartość tych dzienników jest zależna od typu zasobu. Zasób poziom dzienniki diagnostyczne różnią się także z poziomu systemu operacyjnego gościa, dzienniki diagnostyczne. System operacyjny gościa, dzienniki diagnostyczne są zbierane przez agenta, który działa wewnątrz maszyny wirtualnej lub inne obsługiwane typu zasobu. Dzienniki diagnostyczne na poziomie zasobów wymagają nie agenta i przechwytywania danych specyficznych dla zasobów z samą platformę Azure. Dzienniki diagnostyczne na poziomie systemu operacyjnego gościa przechwytywać dane z systemu operacyjnego i aplikacji, które są uruchomione na maszynie wirtualnej.

![Rejestrowanie diagnostyczne do magazynu, usługa Event Hubs lub usługi Log Analytics](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Co to jest rejestrowany przez dzienniki diagnostyczne platformy Azure?

* Wszystkie żądania uwierzytelnionych w wewnętrznej bazie danych (TCP/REST) wszystkich interfejsów API, są rejestrowane, w tym żądań zakończonych niepowodzeniem w wyniku uprawnienia dostępu, błędów systemowych lub żądaniach. Pomoc techniczna dla żądania programu Graph, bazy danych Cassandra i interfejsu API tabel zainicjowanego przez użytkownika nie są obecnie dostępne.
* Operacje w bazie danych, które obejmują operacje CRUD na wszystkie dokumenty, kontenery i baz danych.
* Operacje na kluczach konta, które obejmują tworzenie, modyfikowanie lub usuwanie kluczy.
* Nieuwierzytelnione żądania, które powodują uzyskanie odpowiedzi 401. Na przykład żądania, które nie mają tokenu elementu nośnego, są nieprawidłowo sformułowane, wygasły lub mają nieprawidłowy token.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Włącz rejestrowanie w witrynie Azure portal

Aby włączyć rejestrowanie diagnostyczne, musi mieć następujące zasoby:

* Istniejące usługi Azure Cosmos DB konta, bazy danych i kontenerów. Aby uzyskać instrukcje dotyczące tworzenia tych zasobów, zobacz [utworzyć konto bazy danych przy użyciu witryny Azure portal](create-sql-api-dotnet.md#create-a-database-account), [przykłady interfejsu wiersza polecenia platformy Azure](cli-samples.md), lub [przykłady programu PowerShell](powershell-samples.md).

Aby włączyć rejestrowanie diagnostyczne w witrynie Azure portal, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com), w swojej usłudze Azure Cosmos DB konta, wybierz **dzienniki diagnostyczne** w nawigacji po lewej stronie, a następnie wybierz **Włącz diagnostykę**.

    ![Włączanie rejestrowania diagnostycznego usługi Azure Cosmos DB w witrynie Azure portal](./media/logging/turn-on-portal-logging.png)

2. W **ustawień diagnostycznych** wykonaj następujące czynności: 

    * **Nazwa**: Wprowadź nazwę dla dzienników w celu tworzenia.

    * **Zarchiwizuj na koncie magazynu**: Aby użyć tej opcji, należy istniejące konto magazynu, aby nawiązać połączenie. Aby utworzyć nowe konto magazynu w portalu, zobacz [Tworzenie konta magazynu](../storage/common/storage-create-storage-account.md) i postępuj zgodnie z instrukcjami, aby utworzyć usługi Azure Resource Manager, konto ogólnego przeznaczenia. Następnie wróć do tej strony w portalu wybierz konto magazynu. Może upłynąć kilka minut, zanim konta nowo utworzonego magazynu pojawią się w menu rozwijanym.
    * **Stream do usługi event hub**: Aby użyć tej opcji, należy istniejącej usługi Event Hubs przestrzeni nazw i Centrum zdarzeń nawiązać połączenia. Aby utworzyć obszar nazw usługi Event Hubs, zobacz [tworzenie przestrzeni nazw usługi Event Hubs i Centrum zdarzeń przy użyciu witryny Azure portal](../event-hubs/event-hubs-create.md). Następnie wróć do tej strony w portalu, aby wybrać nazwę przestrzeni nazw i zasad usługi Event Hubs.
    * **Wysyłanie do usługi Log Analytics**: Aby użyć tej opcji, użyj istniejącego obszaru roboczego albo utwórz nowy obszar roboczy usługi Log Analytics, wykonując następujące kroki, aby [Utwórz nowy obszar roboczy](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) w portalu. Aby uzyskać więcej informacji na temat wyświetlania dzienników w usłudze Log Analytics, zobacz [widoku dzienników w usłudze Log Analytics](#view-in-loganalytics).
    * **Zaloguj się DataPlaneRequests**: Wybierz tę opcję, aby rejestrować żądania zaplecza z podstawowej usługi Azure Cosmos DB platformy rozproszone dla kont usługi SQL, programu Graph, bazy danych MongoDB, Cassandra i interfejsu API tabel. Jeśli masz archiwizacji na koncie magazynu można wybrać okres przechowywania dla dzienników diagnostycznych. Dzienniki są usuwane automatycznie po upływie okresu przechowywania.
    * **Zaloguj się MongoRequests**: Wybierz tę opcję, aby rejestrować żądania zainicjowanego przez użytkownika z usługi Azure Cosmos DB frontonu dla obsługi konta interfejsu API usługi MongoDB. Jeśli masz archiwizacji na koncie magazynu można wybrać okres przechowywania dla dzienników diagnostycznych. Dzienniki są usuwane automatycznie po upływie okresu przechowywania.
    * **Metryka żądania**: Wybierz tę opcję, aby przechowywać pełne dane w [metryki platformy Azure](../monitoring-and-diagnostics/monitoring-supported-metrics.md). Jeśli masz archiwizacji na koncie magazynu można wybrać okres przechowywania dla dzienników diagnostycznych. Dzienniki są usuwane automatycznie po upływie okresu przechowywania.

3. Wybierz pozycję **Zapisz**.

    Jeśli otrzymasz komunikat o błędzie, który jest wyświetlany komunikat "nie można zaktualizować diagnostykę dla \<nazwa obszaru roboczego >. Subskrypcja \<identyfikator subskrypcji > nie jest zarejestrowana do używania microsoft.insights, "wykonaj [Rozwiązywanie problemów z usługi Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) instrukcjami, aby zarejestrować konto, a następnie ponów próbę wykonania tej procedury.

    Jeśli chcesz zmienić, jak dzienników diagnostycznych są zapisywane w dowolnym momencie w przyszłości, wróć do tej strony, aby zmodyfikować ustawienia dziennika diagnostycznego konta.

## <a name="turn-on-logging-by-using-azure-cli"></a>Włącz rejestrowanie przy użyciu wiersza polecenia platformy Azure

Aby włączyć rejestrowanie diagnostyczne i metryki za pomocą wiersza polecenia platformy Azure, użyj następujących poleceń:

- Aby włączyć magazyn dzienników diagnostycznych w ramach konta magazynu, użyj tego polecenia:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   `resourceId` Jest nazwą konta usługi Azure Cosmos DB. `storageId` Jest nazwą konta magazynu, do którego chcesz wysłać dzienniki.

- Aby włączyć strumieniowe przesyłanie dzienników diagnostycznych do Centrum zdarzeń, użyj tego polecenia:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   `resourceId` Jest nazwą konta usługi Azure Cosmos DB. `serviceBusRuleId` Jest ciągiem o następującym formacie:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Aby włączyć wysyłanie dzienników diagnostycznych do obszaru roboczego usługi Log Analytics, użyj tego polecenia:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Można połączyć te parametry, aby włączyć wiele opcji danych wyjściowych.

## <a name="turn-on-logging-by-using-powershell"></a>Włącz rejestrowanie przy użyciu programu PowerShell

Aby włączyć rejestrowanie diagnostyczne przy użyciu programu PowerShell, musisz mieć programu Azure Powershell przy użyciu minimalnej wersji 1.0.1.

Aby zainstalować program Azure PowerShell i skojarzyć go z subskrypcją platformy Azure, zobacz [Sposób instalowania i konfigurowania programu Azure PowerShell](/powershell/azure/overview).

Jeśli został już zainstalowany program Azure PowerShell, a nie wiadomo, wersja, z typu konsoli programu PowerShell `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Nawiązywanie połączenia z subskrypcjami
Uruchom sesję programu PowerShell Azure i zaloguj się na konto platformy Azure przy użyciu następującego polecenia:  

```powershell
Connect-AzureRmAccount
```

W podręcznym oknie przeglądarki wprowadź nazwę użytkownika i hasło dla konta platformy Azure. Program Azure PowerShell pobierze wszystkie subskrypcje, które są skojarzone z tym kontem i domyślnie użyje pierwszej.

Jeśli masz więcej niż jedną subskrypcję, może być do określenia konkretnej subskrypcji, który został użyty do utworzenia magazynu kluczy Azure. Aby zobaczyć subskrypcje dla swojego konta, wpisz następujące polecenie:

```powershell
Get-AzureRmSubscription
```

Następnie aby określić subskrypcję, która jest skojarzona z konta usługi Azure Cosmos DB, która jest rejestruje, wpisz następujące polecenie:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Jeśli masz więcej niż jedną subskrypcję, która jest skojarzona z kontem, jest ważne, aby określić subskrypcję, dla której chcesz użyć.
>
>

Aby uzyskać więcej informacji o sposobie konfigurowania programu Azure PowerShell, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

### <a id="storage"></a>Tworzenie nowego konta magazynu dla dzienników
Chociaż można używać istniejącego konta magazynu dla dzienników, w tym samouczku, możemy utworzyć nowe konto magazynu, przeznaczonego dla dzienników usługi Azure Cosmos DB. Dla wygody, są przechowywane szczegóły konta magazynu w zmiennej o nazwie **sa**.

W celu ułatwienia zarządzania, w tym samouczku używamy tej samej grupie zasobów, która zawiera bazę danych usługi Azure Cosmos DB. Podstaw wartości **ContosoResourceGroup**, **contosocosmosdblogs**, i **północno-środkowe stany USA** parametrów, zgodnie z wymaganiami:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Jeśli zdecydujesz się użyć istniejącego konta magazynu, konto musi używać tej samej subskrypcji co Twoja subskrypcja usługi Azure Cosmos DB. To konto należy również użyć modelu wdrażania usługi Resource Manager, a nie klasycznego modelu wdrażania.
>
>

### <a id="identify"></a>Określ konto usługi Azure Cosmos DB dla dzienników
Ustaw nazwę konta usługi Azure Cosmos DB do zmiennej o nazwie **konta**, gdzie **ResourceName** jest nazwą konta usługi Azure Cosmos DB.

```powershell
$account = Get-AzureRmResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Włączanie rejestrowania
Aby włączyć rejestrowanie dla usługi Azure Cosmos DB, należy użyć `Set-AzureRmDiagnosticSetting` polecenia cmdlet, za pomocą zmiennych dla nowego konta magazynu, konto usługi Azure Cosmos DB i kategorię Aby włączyć rejestrowanie. Uruchom następujące polecenie, a następnie ustaw **— włączone** flaga **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

Dane wyjściowe polecenia powinien przypominać następujący przykład:

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

Dane wyjściowe polecenia potwierdza, że włączono rejestrowanie dla bazy danych i informacji jest zapisywany do swojego konta magazynu.

Opcjonalnie można także ustawić zasady przechowywania dla dzienników, tak, aby starsze dzienniki są automatycznie usuwane. Na przykład ustawić zasady przechowywania, przy użyciu **- Retentionenable** flaga jest ustawiona na **$true**. Ustaw **- RetentionInDays** parametr **90** tak, aby dzienniki starsze niż 90 dni są automatycznie usuwane.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Uzyskiwanie dostępu do dzienników
Usługa Azure Cosmos DB w dziennikach **DataPlaneRequests** kategorii są przechowywane w **insights — dzienniki — — płaszczyzna — żądania danych** kontenera na koncie magazynu, podane. 

Najpierw utwórz zmienną dla nazwy kontenera. Zmienna jest używana w tym przewodniku.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Aby wyświetlić listę wszystkich obiektów blob w tym kontenerze, wpisz:

```powershell
Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Dane wyjściowe polecenia powinien przypominać następujący przykład:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

Jak widać na tych danych wyjściowych, obiekty BLOB konwencją nazewnictwa: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Wartości daty i godziny używają czasu UTC.

Ponieważ tego samego konta magazynu może służyć do zbierania dzienników dla wielu zasobów, można użyć w pełni kwalifikowanego Identyfikatora zasobu w nazwie obiektu blob do konkretnych obiektów blob, które należy pobrać. Zanim do tego, firma Microsoft omówiony sposób pobierania wszystkich obiektów blob.

Najpierw utwórz folder w celu pobrania obiektów blob. Na przykład:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Następnie Uzyskaj listę wszystkich obiektów blob:  

```powershell
$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Prześlij tę listę, za pośrednictwem `Get-AzureStorageBlobContent` polecenie, aby pobrać obiekty BLOB w folderze docelowym:

```powershell
$blobs | Get-AzureStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

Po uruchomieniu tego drugiego polecenia **/** ogranicznika w nazwach obiektów blob utworzy pełną strukturę folderów w folderze docelowym. Tej struktury folderów służy do pobierania i przechowywania obiektów blob jako plików.

Aby selektywnie pobierać obiekty blob, użyj symboli wieloznacznych. Na przykład:

* Jeśli masz wiele baz danych i chcesz pobrać dzienniki dla tylko jednej bazy danych o nazwie **CONTOSOCOSMOSDB3**, użyj polecenia:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Jeśli masz wiele grup zasobów i chcesz pobrać dzienniki dla tylko jednej grupy zasobów, użyj polecenia `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Jeśli chcesz pobrać wszystkie dzienniki na miesiąc lipca 2017 r., użyj polecenia `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Można również uruchomić następujące polecenia:

* Aby wykonać zapytanie o stan ustawień diagnostycznych dla zasobu bazy danych, użyj polecenia `Get-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`.
* Aby wyłączyć rejestrowanie **DataPlaneRequests** kategorię dla zasobu konta bazy danych, użyj polecenia `Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`.


Obiekty BLOB, które są zwracane w każdym z tych zapytań są przechowywane jako tekst i sformatowane jako obiekt blob JSON, jak pokazano w poniższym kodzie:

```json
{
    "records":
    [
        {
           "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
           "resourceId": "contosocosmosdb",
           "category": "DataPlaneRequests",
           "operationName": "Query",
           "resourceType": "Database",
           "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
           "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
           "resourceType": "Database","statusCode": "200","documentResourceId": "",`
           "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
           "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
        }
    ]
}
```

Aby uzyskać informacje dotyczące danych w poszczególnych obiektów blob JSON, zobacz [interpretowanie dzienników usługi Azure Cosmos DB](#interpret).

## <a name="manage-your-logs"></a>Zarządzasz dziennikami

Dzienniki diagnostyczne są udostępniane w ramach Twojego konta do dwóch godzin od momentu zgłaszający operacji usługi Azure Cosmos DB. To Ty zarządzasz dziennikami na swoim koncie magazynu:

* Za pomocą metod kontroli dostępu w usłudze Azure standard zabezpieczenia dzienników i ograniczyć, którzy mogą uzyskiwać do nich dostęp.
* Usuń dzienniki, których nie chcesz już przechowywać na koncie magazynu.
* Okres przechowywania dla żądań płaszczyzny danych, które są archiwizowane na konto magazynu jest skonfigurowane w portalu po **DataPlaneRequests dziennika** wybrane ustawienie. Aby zmienić to ustawienie, zobacz [mogą włączyć rejestrowanie w witrynie Azure portal](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-log-analytics"></a>Wyświetlanie dzienników w usłudze Log Analytics

W przypadku wybrania **wysyłanie do usługi Log Analytics** opcji po włączeniu rejestrowania diagnostycznego diagnostyczne dane z kontenera jest przekazywany do usługi Log Analytics w ciągu dwóch godzin. Jeśli przyjrzymy się usługi Log Analytics bezpośrednio po włączeniu rejestrowania, nie zobaczysz żadnych danych. Po prostu Zaczekaj dwie godziny i spróbuj ponownie. 

Zanim będzie wyświetlić dzienniki, sprawdź i zobacz, jeśli obszar roboczy usługi Log Analytics został uaktualniony używać nowego języka zapytań usługi Log Analytics. Aby sprawdzić, otwórz [witryny Azure portal](https://portal.azure.com), wybierz opcję **usługi Log Analytics** daleko po lewej, następnie wybierz nazwę obszaru roboczego, jak pokazano na następnej ilustracji. **Obszaru roboczego pakietu OMS** zostanie wyświetlona strona:

![Usługa log Analytics w witrynie Azure portal](./media/logging/azure-portal.png)

Jeśli zobaczysz następujący komunikat na **obszaru roboczego pakietu OMS** stronie obszaru roboczego, nie został uaktualniony do korzystania z nowego języka. Aby uzyskać więcej informacji na temat sposobu uaktualniania do nowego języka zapytań, zobacz [uaktualnienia obszaru roboczego usługi Azure Log Analytics na nową funkcją przeszukiwania dzienników](../log-analytics/log-analytics-log-search-upgrade.md). 

![Komunikat o uaktualnieniu usługi log Analytics](./media/logging/upgrade-notification.png)

Aby wyświetlić dane diagnostyczne w usłudze Log Analytics, otwórz **wyszukiwanie w dzienniku** strony w menu po lewej stronie lub **zarządzania** obszar strony, jak pokazano na poniższej ilustracji:

![Opcje wyszukiwania dzienników w witrynie Azure portal](./media/logging/log-analytics-open-log-search.png)

Po włączeniu zbierania danych uruchomić następujące przykładowe wyszukiwanie w dziennikach za pomocą nowego języka zapytań, aby wyświetlić dzienniki ostatnich 10 `AzureDiagnostics | take 10`.

![Przykładowe wyszukiwanie w dzienniku 10 najnowszych dzienników](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Zapytania

Poniżej przedstawiono kilka dodatkowych zapytań, które można wprowadzić do **wyszukiwanie w dzienniku** pole, które ułatwiają monitorowanie kontenerów usługi Azure Cosmos DB. Te zapytania pracować [nowy język](../log-analytics/log-analytics-log-search-upgrade.md). 

Aby dowiedzieć się znaczenie danych, który jest zwracany przez każdy przeszukiwania dzienników, zobacz [interpretowanie dzienników usługi Azure Cosmos DB](#interpret).

* Aby wykonać zapytanie dotyczące wszystkich dzienników diagnostycznych z usługi Azure Cosmos DB w określonym przedziale czasu:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Aby wysłać zapytanie 10 najbardziej niedawno zarejestrowane zdarzenia:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Aby wykonać zapytanie dla wszystkich operacji, pogrupowane według typu operacji:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* Do wykonywania zapytań dla wszystkich operacji, pogrupowane według **zasobów**:

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Aby wykonać zapytanie dla wszystkich działań użytkowników, pogrupowane według zasobu:

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
    > [!NOTE]
    > To polecenie jest dla dziennika aktywności nie dziennik diagnostyczny.

* Aby wykonać zapytanie, dla którego trwać dłużej niż 3 milisekundy operacje:

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Aby wykonać zapytanie, które agent jest uruchomiony operacje:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Aby wyszukać podczas długotrwałych operacji zostały wykonane:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

Aby uzyskać więcej informacji o sposobie używania nowego języka wyszukiwania w dziennikach, zobacz [omówienie wyszukiwań w dziennikach w usłudze Log Analytics](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>Interpretowanie dzienników usługi

Dane diagnostyczne są przechowywane w usłudze Azure Storage i usługi Log Analytics używa schematu podobne. 

W poniższej tabeli opisano zawartość każdego wpisu dziennika.

| Usługa Azure Storage pola lub właściwości | Właściwości analizy dzienników | Opis |
| --- | --- | --- |
| **czas** | **TimeGenerated** | Data i godzina (UTC), gdy wystąpienia operacji. |
| **resourceId** | **Zasób** | Konto usługi Azure Cosmos DB, dla którego dzienniki są włączone.|
| **category** | **Kategoria** | W przypadku dzienników usługi Azure Cosmos DB **DataPlaneRequests** jest jedyną dostępną wartością. |
| **OperationName** | **OperationName** | Nazwa operacji. Ta wartość może być dowolną z następujących operacji: tworzenia, aktualizacji, odczytu, ReadFeed, Delete, Replace, Execute, SqlQuery, zapytania, JSQuery, Head, HeadFeed lub Upsert.   |
| **Właściwości** | Nie dotyczy | Zawartość tego pola są opisane w kolejnych wierszy. |
| **Identyfikator działania** | **activityId_g** | Unikatowy identyfikator GUID dla zarejestrowanych operacji. |
| **userAgent** | **userAgent_s** | Ciąg, który określa agent użytkownika klienta, który wykonuje żądanie. Format to {nazwa agenta użytkownika} / {version}.|
| **resourceType** | **ResourceType** | Typ dostęp do zasobów. Ta wartość może być dowolną z następujących zasobów: bazy danych, kontenerów, dokument, załącznika, użytkownika, uprawnienie, StoredProcedure, wyzwalacza, UserDefinedFunction lub oferty. |
| **statusCode** | **statusCode_s** | Stan odpowiedzi operacji. |
| **requestResourceId** | **ResourceId** | Identyfikator zasobu, które odnoszą się do żądania. Wartość może wskazywać databaseRid, collectionRid lub documentRid w zależności od operacji wykonywanych.|
| **clientIpAddress** | **clientIpAddress_s** | Adres IP klienta. |
| **requestCharge** | **requestCharge_s** | Liczba jednostek żądań, które są używane przez operację |
| **collectionRid** | **collectionId_s** | Unikatowy identyfikator dla kolekcji.|
| **Czas trwania** | **duration_s** | Czas trwania operacji w dziesięciomilionowych częściach sekundy. |
| **requestLength** | **requestLength_s** | Długość żądania, w bajtach. |
| **responseLength** | **responseLength_s** | Długość odpowiedzi w bajtach.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Ta wartość jest pusta, gdy [tokenów zasobów](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) są używane do uwierzytelniania. Wartość wskazuje identyfikator zasobu użytkownika. |

## <a name="next-steps"></a>Kolejne kroki

- Aby zrozumieć, jak włączyć rejestrowanie i kategorie metryk i dzienników, które są obsługiwane przez różne usługi platformy Azure, przeczytaj zarówno [Przegląd metryk w systemie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) i [Omówienie programu Azure dzienników diagnostycznych ](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) artykułów.
- Przeczytaj następujące artykuły, aby dowiedzieć się więcej o usłudze event hubs:
   - [Co to jest usługa Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Rozpoczynanie pracy z usługą Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Odczyt [pobrać metryk i dzienników diagnostycznych z usługi Azure Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
- Odczyt [omówienie wyszukiwań w dziennikach w usłudze Log Analytics](../log-analytics/log-analytics-log-search-new.md).
