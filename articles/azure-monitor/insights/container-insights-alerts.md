---
title: Tworzenie alertów wydajności dla Azure Monitor kontenerów | Microsoft Docs
description: W tym artykule opisano sposób tworzenia niestandardowych alertów opartych na kwerendach dzienników dotyczących wykorzystania pamięci i procesora CPU z Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 5d73f4399d10683597fb2a2e8a3a2ab4ba0d1165
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75730929"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Jak skonfigurować alerty dotyczące problemów z wydajnością w Azure Monitor dla kontenerów

Azure Monitor dla kontenerów monitoruje wydajność obciążeń kontenera wdrożonych w celu Azure Container Instances lub do zarządzanych klastrów Kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS).

W tym artykule opisano sposób włączania alertów w następujących sytuacjach:

- Gdy użycie procesora CPU lub pamięci w węzłach klastra przekracza wartość progową
- Gdy użycie procesora lub pamięci w dowolnym kontenerze w kontrolerze przekracza próg w porównaniu z limitem ustawionym dla odpowiedniego zasobu
- Liczba węzłów stanu " *Nobieżniing* "
- Liczba *zakończonych niepowodzeniem*, *oczekujących*, *nieznanych*, *uruchomionych*lub *zakończonych powodzeniem*
- Gdy ilość wolnego miejsca na dysku w węzłach klastra przekracza wartość progową 

Aby otrzymywać alerty dotyczące wysokiego użycia procesora CPU lub pamięci lub małej ilości wolnego miejsca na dysku klastra, użyj zapytań dostarczonych do utworzenia alertu metryki lub alertu pomiaru metryki. Alerty metryk mają mniejsze opóźnienia niż alerty dzienników. Alerty dzienników zapewniają zaawansowane zapytania i większe złożoności. Zapytania dotyczące alertów dziennika porównują datę i godzinę do obecne przy użyciu operatora *Now* i cofają jedną godzinę. (Azure Monitor dla kontenerów przechowuje wszystkie daty w formacie uniwersalnego czasu koordynowanego (UTC).)

Jeśli nie masz doświadczenia w Azure Monitor alertów, zobacz [Omówienie alertów w usłudze Microsoft Azure](../platform/alerts-overview.md) przed rozpoczęciem. Aby dowiedzieć się więcej o alertach korzystających z zapytań dzienników, zobacz [alerty dzienników w Azure monitor](../platform/alerts-unified-log.md). Aby uzyskać więcej informacji na temat alertów metryk, zobacz [alerty metryk w Azure monitor](../platform/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Zapytania wyszukiwania dzienników wykorzystania zasobów

Zapytania w tej sekcji obsługują każdy scenariusz związany z alertami. Są one używane w kroku 7 sekcji [Tworzenie alertu](#create-an-alert-rule) w tym artykule.

Następujące zapytanie oblicza średnie użycie procesora jako średnie użycie procesora CPU przez węzły Członkowskie co minutę.  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

Poniższe zapytanie oblicza średnie użycie pamięci jako średnią wartość wykorzystania pamięci węzłów członkowskich co minutę.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>Poniższe zapytania używają wartości symboli zastępczych \<nazwy klastra > i \<nazwę-kontrolera-> do reprezentowania klastra i kontrolera. Zastąp je wartościami specyficznymi dla danego środowiska podczas konfigurowania alertów.

Następujące zapytanie oblicza średnie wykorzystanie procesora CPU przez wszystkie kontenery w kontrolerze jako średnie wykorzystanie procesora CPU każdego wystąpienia kontenera w kontrolerze co minutę. Pomiar jest wartością procentową limitu skonfigurowanego dla kontenera.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

Poniższe zapytanie oblicza średnie użycie pamięci przez wszystkie kontenery w kontrolerze jako średnie użycie pamięci przez każde wystąpienie kontenera w kontrolerze co minutę. Pomiar jest wartością procentową limitu skonfigurowanego dla kontenera.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

Następujące zapytanie zwraca wszystkie węzły i zliczenia o stanie *gotowe* i bez *bieżnika*.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
Następujące zapytanie zwraca liczbę faz w oparciu o wszystkie fazy: *Niepowodzenie*, *oczekiwanie*, *nieznane*, *uruchomione*lub *zakończone powodzeniem*.  

```kusto
let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let clusterName = '<your-cluster-name>';
    KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                 by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>Aby ostrzec o niektórych fazach, takich jak *oczekujące*, *zakończone niepowodzeniem*lub *nieznane*, należy zmodyfikować ostatni wiersz zapytania. Na przykład, aby ostrzec o *FailedCount* użyciu: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

Następujące zapytanie zwraca dyski węzłów klastra, które przekraczają 90% wolnego miejsca. Aby uzyskać identyfikator klastra, najpierw uruchom następujące zapytanie i skopiuj wartość z właściwości `ClusterId`:

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>Tworzenie reguły alertu

Wykonaj następujące kroki, aby utworzyć alert dziennika w Azure Monitor przy użyciu jednej z reguł przeszukiwania dzienników, które zostały podane wcześniej. Aby utworzyć szablon przy użyciu szablonu ARM, zobacz [Przykładowy dziennik tworzenia alertu przy użyciu szablonu zasobów platformy Azure](../platform/alerts-log.md#sample-log-alert-creation-using-azure-resource-template).

>[!NOTE]
>Poniższa procedura umożliwia utworzenie reguły alertu dotyczącego użycia zasobów kontenera. w tym celu należy przełączyć się do nowego interfejsu API alertów dziennika, zgodnie z opisem w [preferencjach przełącznika interfejsu API dla alertów dzienników](../platform/alerts-log-api-switch.md).
>

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. W Azure Portal Wyszukaj i wybierz pozycję **log Analytics obszary robocze**.
3. Na liście obszarów roboczych Log Analytics wybierz obszar roboczy obsługujący Azure Monitor kontenerów. 
4. W okienku po lewej stronie wybierz pozycję **dzienniki** , aby otworzyć stronę Dzienniki Azure monitor. Ta strona służy do pisania i wykonywania zapytań Log Analytics platformy Azure.
5. Na stronie **dzienniki** wklej jedno z [zapytań](#resource-utilization-log-search-queries) dostarczonych wcześniej do pola **zapytania wyszukiwania** , a następnie wybierz polecenie **Uruchom** , aby sprawdzić poprawność wyników. Jeśli ten krok nie zostanie wykonane, opcja **+ Nowy alert** nie jest dostępna do wybrania.
6. Wybierz pozycję **+ Nowy alert** , aby utworzyć alert dziennika.
7. W sekcji **warunek** wybierz opcję zawsze, **gdy niestandardowa wyszukiwarka dziennika ma \<logiki niezdefiniowana >** wstępnie zdefiniowany warunek dziennika niestandardowego. Typ sygnału **niestandardowego wyszukiwania w dzienniku** jest automatycznie wybierany, ponieważ tworzymy regułę alertu bezpośrednio na stronie dzienników Azure monitor.  
8. Wklej jedno z [zapytań](#resource-utilization-log-search-queries) dostarczonych wcześniej do pola **zapytania wyszukiwania** .
9. Skonfiguruj alert w następujący sposób:

    1. Z listy rozwijanej **Na podstawie** wybierz pozycję **Pomiar metryki**. Pomiar metryki tworzy alert dla każdego obiektu w zapytaniu, którego wartość przekracza nasz określony próg.
    1. W obszarze **warunek**wybierz opcję **większe niż**i wprowadź **75** jako początkowy **próg** punktu odniesienia dla alertów użycia procesora CPU i pamięci. W przypadku alertu o małej ilości miejsca na dysku wprowadź **90**. Lub wprowadź inną wartość, która spełnia kryteria.
    1. W sekcji **alert wyzwalacza na podstawie** wybierz pozycję **kolejne naruszenia**. Z listy rozwijanej wybierz pozycję **większe niż**, a następnie wprowadź wartość **2**.
    1. Aby skonfigurować alert dotyczący użycia procesora lub pamięci kontenera, w obszarze **agregowanie**wybierz pozycję **ContainerName**. Aby skonfigurować alert niskiego dysku węzła klastra, wybierz pozycję **ClusterId**.
    1. W sekcji **oceniane na podstawie** ustaw wartość **okresu** na **60 minut**. Reguła będzie działać co 5 minut i zwracać rekordy, które zostały utworzone w ciągu ostatniej godziny od bieżącego czasu. Ustawianie przedziału czasu dla kont okien szerokich dla potencjalnych opóźnień danych. Zapewnia również, że zapytanie zwraca dane, aby uniknąć fałszywych wartości ujemnych, w których alert nigdy nie jest uruchamiany.

10. Wybierz pozycję **gotowe** , aby zakończyć regułę alertu.
11. Wprowadź nazwę w polu **Nazwa reguły alertu** . Podaj **Opis** , który zawiera szczegóły dotyczące alertu. I wybierz odpowiedni poziom ważności z dostępnych opcji.
12. Aby natychmiast aktywować regułę alertu, Zaakceptuj wartość domyślną dla **reguły Włącz przy tworzeniu**.
13. Wybierz istniejącą **grupę akcji** lub Utwórz nową grupę. Ten krok zapewnia, że te same akcje są wykonywane za każdym razem, gdy zostanie wyzwolony alert. Skonfiguruj w zależności od tego, jak zespół IT lub DevOps Operations zarządza incydentami.
14. Wybierz pozycję **Utwórz regułę alertu** , aby zakończyć regułę alertu. Rozpoczyna ona działanie od razu.

## <a name="next-steps"></a>Następne kroki

- Wyświetl [przykłady zapytań dotyczących dzienników](container-insights-log-search.md#search-logs-to-analyze-data) , aby zobaczyć wstępnie zdefiniowane zapytania i przykłady do oszacowania lub dostosowania do tworzenia alertów, wizualizacji lub analizowania klastrów.
- Aby dowiedzieć się więcej na temat Azure Monitor i sposobu monitorowania innych aspektów klastra Kubernetes, zobacz [Wyświetlanie Kubernetes klastra Performance](container-insights-analyze.md) and [View Kubernetes Cluster Health](container-insights-health.md).
