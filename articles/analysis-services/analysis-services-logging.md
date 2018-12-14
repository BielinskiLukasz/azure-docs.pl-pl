---
title: Rejestrowanie Diganostic dla usług Azure Analysis Services | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o konfigurowaniu rejestrowania diagnostycznego dla usług Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f3a3261fa1b7aa2075a05d7b87ea3fc29c5b9fab
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53385877"
---
# <a name="setup-diagnostic-logging"></a>Konfigurowanie rejestrowania diagnostycznego

Ważną częścią dowolnego rozwiązania usług Analysis Services to monitorowania, jak działają serwery. Za pomocą [dzienników diagnostycznych usługi Azure resource](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md), monitorować i wysyłanie dzienników do [usługi Azure Storage](https://azure.microsoft.com/services/storage/), ich do usługi stream [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)i wyeksportować je do [dziennika Analiza](https://azure.microsoft.com/services/log-analytics/), usługi [Azure](https://www.microsoft.com/cloud-platform/operations-management-suite). 

![Rejestrowanie diagnostyczne do magazynu, usługa Event Hubs lub usługi Log Analytics](./media/analysis-services-logging/aas-logging-overview.png)


## <a name="whats-logged"></a>Co to jest rejestrowane?

Możesz wybrać **aparatu**, **usługi**, i **metryki** kategorii.

### <a name="engine"></a>Aparat

Wybieranie **aparatu** rejestruje wszystkie [xEvents](https://docs.microsoft.com/sql/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Nie można wybrać poszczególne zdarzenia. 

|Kategorie systemu XEvent |Nazwa zdarzenia  |
|---------|---------|
|Inspekcja zabezpieczeń    |   Inspekcja logowania      |
|Inspekcja zabezpieczeń    |   Inspekcja wylogowania      |
|Inspekcja zabezpieczeń    |   Audit Server uruchamia i zatrzymuje      |
|Sprawozdania z postępu prac     |   Początek raportu postępu      |
|Sprawozdania z postępu prac     |   Koniec raportu postępu      |
|Sprawozdania z postępu prac     |   Bieżący raport postępu      |
|Zapytania     |  Początek zapytania       |
|Zapytania     |   Koniec zapytania      |
|Polecenia     |  Polecenie Begin       |
|Polecenia     |  Koniec polecenia       |
|Błędy i ostrzeżenia     |   Błąd      |
|Wykrywanie     |   Odkryj zakończenia      |
|Powiadomienia     |    Powiadomienia     |
|Sesja     |  Inicjowanie sesji       |
|Blokady    |  Zakleszczenia       |
|Przetwarzanie zapytań     |   VertiPaq SE Query Begin      |
|Przetwarzanie zapytań     |   VertiPaq SE Query End      |
|Przetwarzanie zapytań     |   VertiPaq SE Query Cache Match      |
|Przetwarzanie zapytań     |   Początek zapytania bezpośredniego      |
|Przetwarzanie zapytań     |  Koniec zapytania bezpośredniego       |

### <a name="service"></a>Usługa

|Nazwa operacji  |Występuje, gdy  |
|---------|---------|
|ResumeServer     |    Wznów serwera     |
|SuspendServer    |   Wstrzymaj serwera      |
|DeleteServer     |    Usuwanie serwera     |
|RestartServer    |     Użytkownika spowoduje ponowne uruchomienie serwera za pomocą programu SSMS lub PowerShell    |
|GetServerLogFiles    |    Użytkownik eksportuje dziennika serwera za pomocą programu PowerShell     |
|ExportModel     |   Użytkownik eksportuje model w witrynie portal, za pomocą polecenia Otwórz w programie Visual Studio     |

### <a name="all-metrics"></a>Wszystkie metryki

Kategoria metryk rejestruje takie same [metryk serwera](analysis-services-monitor.md#server-metrics) wyświetlane w metrykach.

## <a name="setup-diagnostics-logging"></a>Konfigurowanie rejestrowania diagnostycznego

### <a name="azure-portal"></a>Azure Portal

1. W [witryny Azure portal](https://portal.azure.com) > serwera, kliknij przycisk **dzienniki diagnostyczne** w nawigacji po lewej stronie, a następnie kliknij przycisk **Włącz diagnostykę**.

    ![Włączanie rejestrowania diagnostycznego usługi Azure Cosmos DB w witrynie Azure portal](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. W **ustawień diagnostycznych**, określ następujące opcje: 

    * **Nazwa**. Wprowadź nazwę dla dzienników w celu tworzenia.

    * **Zarchiwizuj na koncie magazynu**. Aby użyć tej opcji, należy istniejące konto magazynu, aby nawiązać połączenie. Zobacz [Tworzenie konta magazynu](../storage/common/storage-create-storage-account.md). Postępuj zgodnie z instrukcjami, aby utworzyć Menedżera zasobów, konto ogólnego przeznaczenia, a następnie wybierz swoje konto magazynu, zwracając do tej strony w portalu. Może upłynąć kilka minut, zanim konta nowo utworzonego magazynu pojawią się w menu rozwijanym.
    * **Stream do usługi event hub**. Aby użyć tej opcji, należy istniejącego Centrum zdarzeń przestrzeni nazw i Centrum zdarzeń, aby nawiązać połączenie. Aby dowiedzieć się więcej, zobacz [tworzenie przestrzeni nazw usługi Event Hubs i Centrum zdarzeń za pomocą witryny Azure portal](../event-hubs/event-hubs-create.md). Następnie wróć do tej strony w portalu, aby wybrać nazwę przestrzeni nazw i zasad Centrum zdarzeń.
    * **Wysyłanie do usługi Log Analytics**. Aby użyć tej opcji, użyj istniejącego obszaru roboczego albo utwórz nowy obszar roboczy usługi Log Analytics, wykonując następujące kroki, aby [Utwórz nowy obszar roboczy](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace) w portalu. Aby uzyskać więcej informacji dotyczących przeglądania dzienników w usłudze Log Analytics, zobacz [widoku dzienników w usłudze Log Analytics](#view-logs-in-log-analytics) w tym artykule.

    * **Engine**. Wybierz tę opcję, aby rejestrować xEvents. Jeśli masz archiwizacji na koncie magazynu można wybrać okres przechowywania dla dzienników diagnostycznych. Dzienniki są autodeleted, po upływie okresu przechowywania.
    * **Usługa**. Wybierz tę opcję, aby rejestrować zdarzenia na poziomie usługi. Jeśli archiwizacji na koncie magazynu można wybrać okres przechowywania dla dzienników diagnostycznych. Dzienniki są autodeleted, po upływie okresu przechowywania.
    * **Metryki**. Wybierz tę opcję, aby przechowywać pełne dane w [metryki](analysis-services-monitor.md#server-metrics). Jeśli archiwizacji na koncie magazynu można wybrać okres przechowywania dla dzienników diagnostycznych. Dzienniki są autodeleted, po upływie okresu przechowywania.

3. Kliknij pozycję **Zapisz**.

    Jeśli otrzymasz komunikat o błędzie, który jest wyświetlany komunikat "nie można zaktualizować diagnostykę dla \<nazwa obszaru roboczego >. Subskrypcja \<identyfikator subskrypcji > nie jest zarejestrowana do używania microsoft.insights. " Postępuj zgodnie z [Rozwiązywanie problemów z usługi Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) instrukcjami, aby zarejestrować konto, następnie ponów próbę wykonania tej procedury.

    Jeśli chcesz zmienić, jak dzienników diagnostycznych są zapisywane w dowolnym momencie w przyszłości, można zwrócić się do tej strony, aby zmodyfikować ustawienia.

### <a name="powershell"></a>PowerShell

Poniżej przedstawiono podstawowe polecenia, które umożliwią Ci pracę. Jeśli chcesz, aby pomoc krok po kroku dotyczące konfigurowania logowania na koncie magazynu przy użyciu programu PowerShell, zapoznaj się z samouczkiem, w dalszej części tego artykułu.

Aby włączyć metryki i Diagnostyka rejestrowania przy użyciu programu PowerShell, użyj następujących poleceń:

- Aby włączyć magazyn dzienniki diagnostyczne na koncie magazynu, użyj tego polecenia:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   Identyfikator konta magazynu jest identyfikator zasobu dla konta magazynu, w której chcesz wysłać dzienniki.

- Aby włączyć strumieniowe przesyłanie dzienników diagnostycznych do Centrum zdarzeń, użyj tego polecenia:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Identyfikator reguły usługi Azure Service Bus jest ciągiem o następującym formacie:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Aby włączyć wysyłanie dzienników diagnostycznych do obszaru roboczego usługi Log Analytics, użyj tego polecenia:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Identyfikator zasobu obszaru roboczego usługi Log Analytics można uzyskać za pomocą następującego polecenia:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Można połączyć te parametry, aby włączyć wiele opcji danych wyjściowych.

### <a name="rest-api"></a>Interfejs API REST

Dowiedz się, jak [zmiany ustawień diagnostycznych przy użyciu interfejsu API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Szablon usługi Resource Manager

Dowiedz się, jak [włączanie ustawień diagnostycznych podczas tworzenia zasobów przy użyciu szablonu usługi Resource Manager](../azure-monitor/platform/diagnostic-logs-stream-template.md). 

## <a name="manage-your-logs"></a>Zarządzasz dziennikami

Dzienniki są zwykle dostępne w ciągu kilku godzin konfigurowania rejestrowania. To Ty zarządzasz dziennikami na swoim koncie magazynu:

* Użyj standardowych metod kontroli dostępu platformy Azure w celu zabezpieczenia dzienników, wprowadzając ograniczenia co do tego, kto może uzyskiwać do nich dostęp.
* Usuń dzienniki, których nie chcesz już przechowywać na koncie magazynu.
* Pamiętaj ustawić okres przechowywania, tak, aby stare dzienniki są usuwane z konta magazynu.

## <a name="view-logs-in-log-analytics"></a>Wyświetlanie dzienników w usłudze Log Analytics

Zdarzenia metryki i serwera są zintegrowane z systemu xEvents w usłudze Log Analytics do analizy side-by-side. Usługa log Analytics można skonfigurować w taki sposób, aby odbierać zdarzenia z innymi usługami platformy Azure, zapewniając całościowy obraz danych dzienników diagnostycznych architektury.

Aby wyświetlić dane diagnostyczne w usłudze Log Analytics, otwórz stronę przeszukiwania dzienników z menu po lewej stronie lub z obszaru zarządzania, jak pokazano poniżej.

![Opcje wyszukiwania dzienników w witrynie Azure portal](./media/analysis-services-logging/aas-logging-open-log-search.png)

Teraz, gdy zbieranie danych zostało włączone w **wyszukiwanie w dzienniku**, kliknij przycisk **wszystkie zebrane dane**.

W **typu**, kliknij przycisk **AzureDiagnostics**, a następnie kliknij przycisk **Zastosuj**. AzureDiagnostics zawiera zdarzenia silnika i usług. Należy zauważyć, że zapytanie usługi Log Analytics jest tworzony na bieżąco. EventClass\_s pole zawiera nazwy systemu xEvent, które mogą wygląda znajomo, jeśli wcześniej używano systemu xEvents dla lokalnego rejestrowania.

Kliknij przycisk **EventClass\_s** lub jedną z nazw zdarzeń i Log Analytics jest kontynuowane konstruowanie zapytania. Pamiętaj zapisać zapytania do późniejszego.

Pamiętaj zobaczyć Log Analytics, która zawiera witrynę sieci Web za pomocą rozszerzonych, dashboarding, funkcje zapytań i zgłaszania alertów zebranych danych.

### <a name="queries"></a>Zapytania

Istnieją setki zapytań, których można użyć. Poniżej przedstawiono kilka ułatwią Ci rozpoczęcie pracy.
Aby dowiedzieć się więcej o korzystaniu z nowego języka zapytań wyszukiwania w dziennikach, zobacz [Understanding log wyszukiwania w usłudze Log Analytics](../log-analytics/log-analytics-log-search-new.md). 

* Zapytanie zwraca zapytania przesyłane do usług Azure Analysis Services trwało ponad pięć minut (300 000 milisekund) do wykonania.

    ```
    search * | where ( Type == "AzureDiagnostics" ) | where ( EventClass_s == "QUERY_END" ) | where toint(Duration_s) > 300000
    ```

* Zidentyfikuj skalowania w poziomie replik.

    ```
    search * | summarize count() by ServerName_s
    ```
    Korzystając z skalowalnego w poziomie, można zidentyfikować repliki tylko do odczytu, ponieważ nazwa serwera jest\_wartości pól s ma numer wystąpienia repliki dołączana do nazwy. Pole zasobów zawiera nazwa zasobu platformy Azure, która jest zgodna z nazwą serwera, które widzą użytkownicy. Pola IsQueryScaleoutReadonlyInstance_s jest równa true dla replik.



> [!TIP]
> Masz świetny zapytanie usługi Log Analytics, którą chcesz się podzielić? Jeśli masz konto usługi GitHub, można dodać go do tego artykułu. Po prostu kliknij **Edytuj** w prawym górnym rogu tej strony.


## <a name="tutorial---turn-on-logging-by-using-powershell"></a>Samouczek — Włączanie rejestrowania przy użyciu programu PowerShell
W ramach tego szybkiego samouczka utworzysz konto magazynu w tej samej subskrypcji i grupie zasobów co serwer usług Analysis Service. Następnie należy użyć Set-AzureRmDiagnosticSetting, aby włączyć diagnostykę, rejestrowanie, wysyłanie danych wyjściowych do nowego konta magazynu.

### <a name="prerequisites"></a>Wymagania wstępne
Do ukończenia tego samouczka, musi mieć następujące zasoby:

* Istniejący serwer usług Azure Analysis Services. Aby uzyskać instrukcje na temat tworzenia zasobu serwera, zobacz [Utwórz serwer w witrynie Azure portal](analysis-services-create-server.md), lub [Tworzenie serwera usługi Azure Analysis Services przy użyciu programu PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Nawiązać połączenie z subskrypcji

Uruchom sesję programu PowerShell Azure i zaloguj się na konto platformy Azure przy użyciu następującego polecenia:  

```powershell
Connect-AzureRmAccount
```

W podręcznym oknie przeglądarki wprowadź nazwę użytkownika i hasło dla konta platformy Azure. Program Azure PowerShell pobierze wszystkie subskrypcje, które są skojarzone z tym kontem, i domyślnie użyje pierwszej.

Jeśli masz wiele subskrypcji, określ konkretne konto, które zostało użyte do utworzenia usługi Azure Key Vault. Wpisz następujące polecenie, aby zobaczyć subskrypcje dla swojego konta:

```powershell
Get-AzureRmSubscription
```

Następnie aby określić subskrypcję, która jest skojarzona z konta usług Azure Analysis Services, który się loguje, wpisz:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Jeśli masz wiele subskrypcji skojarzonych z Twoim kontem, jest ważne określić subskrypcję.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Tworzenie nowego konta magazynu dla dzienników

Można użyć istniejącego konta magazynu dla dzienników, pod warunkiem znajduje się w tej samej subskrypcji co serwer. W tym samouczku utworzysz nowe konto magazynu dedykowanego dzienników usług Analysis Services. Aby ułatwić, szczegółów konta magazynu są przechowywane w zmiennej o nazwie **sa**.

Możesz również użyć tej samej grupie zasobów, która zawiera serwer usług Analysis Services. Zastąp wartości `awsales_resgroup`, `awsaleslogs`, i `West Central US` własnymi wartościami:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Określ konto serwera dla dzienników

Ustaw nazwę konta do zmiennej o nazwie **konta**, gdzie ResourceName jest nazwa konta.

```powershell
$account = Get-AzureRmResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Włącz rejestrowanie

Aby włączyć rejestrowanie, należy użyć polecenia cmdlet Set-AzureRmDiagnosticSetting wraz ze zmiennymi dla nowego konta magazynu, konto serwera i kategorii. Uruchom następujące polecenie, ustawienie **— włączone** flaga **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

Dane wyjściowe powinny wyglądać następująco:

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

Będzie to potwierdzenie, że włączono rejestrowanie na serwerze, informacje są zapisywane na koncie magazynu.

Można również ustawić zasady przechowywania dla dzienników, aby starsze dzienniki są automatycznie usuwane. Na przykład Ustaw zasady przechowywania **- Retentionenable** flaga **$true**i ustaw **- RetentionInDays** parametr **90**. Dzienniki starsze niż 90 dni zostaną automatycznie usunięte.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [rejestrowania diagnostycznego usługi Azure resource](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

Zobacz [Set-AzureRmDiagnosticSetting](https://docs.microsoft.com/powershell/module/azurerm.insights/Set-AzureRmDiagnosticSetting) w Pomocy programu PowerShell.
