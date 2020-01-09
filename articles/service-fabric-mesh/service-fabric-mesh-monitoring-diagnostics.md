---
title: Monitorowanie i Diagnostyka w aplikacjach Service Fabric siatki platformy Azure
description: Dowiedz się więcej o monitorowaniu i diagnozowaniu aplikacji w Service Fabric siatki na platformie Azure.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 247a1de4d00668371337295616d31caf101f0cc5
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/26/2019
ms.locfileid: "75498147"
---
# <a name="monitoring-and-diagnostics"></a>Monitorowanie i diagnostyka
Usługa Azure Service Fabric Mesh to w pełni zarządzana usługa, która pozwala deweloperom na wdrażanie aplikacji mikrousług bez zarządzania maszynami wirtualnymi, magazynem i siecią. Monitorowanie i Diagnostyka dla Service Fabric siatki jest podzielone na trzy główne typy danych diagnostycznych:

- Dzienniki aplikacji — są one definiowane jako dzienniki z aplikacji w kontenerze, w zależności od tego, jak dysponujesz Instrumentacją aplikacji (np. dzienniki platformy Docker)
- Zdarzenia platformy — zdarzenia z platformy siatki odpowiednie dla operacji kontenera, obecnie w tym aktywowanie kontenera, dezaktywację i zakończenie.
- Metryki kontenera — wykorzystanie zasobów i metryki wydajności dla kontenerów (statystyki platformy Docker)

W tym artykule omówiono opcje monitorowania i diagnostyki dostępne w najnowszej wersji zapoznawczej.

## <a name="application-logs"></a>Dzienniki aplikacji

Możesz wyświetlić dzienniki platformy Docker ze wdrożonych kontenerów na podstawie każdego kontenera. W modelu aplikacji Service Fabric siatki każdy kontener jest pakietem kodu w aplikacji. Aby wyświetlić skojarzone dzienniki z pakietem kodu, użyj następującego polecenia:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Aby uzyskać nazwę repliki, można użyć polecenia "AZ siatk Service-Replica". Nazwy replik zwiększają liczbę całkowitą z zakresu od 0.

Oto, co jest podobne do wyświetlania dzienników z kontenera VotingWeb. Code z aplikacji do głosowania:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Metryki kontenera 

Środowisko siatki przedstawia kilku metryki wskazujące, jak działają kontenery. Następujące metryki są dostępne za pośrednictwem interfejsu wiersza polecenia Azure Portal i Azure Monitor:

| Metryka | Opis | Jednostki|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu jako wartość procentowa | % |
| MemoryUtilization | ActualMem/AllocatedMem jako wartość procentowa | % |
| AllocatedCpu | Procesor przydzielony do Azure Resource Manager szablonu | Millicores |
| AllocatedMemory | Pamięć przydzieloną zgodnie z szablonem Azure Resource Manager | MB |
| ActualCpu | Użycie procesora CPU | Millicores |
| ActualMemory | Użycie pamięci | MB |
| ContainerStatus | 0 — nieprawidłowe: stan kontenera jest nieznany <br> 1 — oczekujące: zaplanowano uruchomienie kontenera <br> 2 — rozpoczęcie: trwa proces uruchamiania kontenera <br> 3 — rozpoczęto: kontener został pomyślnie uruchomiony <br> 4 — zatrzymywanie: Trwa przerywanie kontenera <br> 5 — zatrzymano: kontener został zatrzymany pomyślnie | ND |
| ApplicationStatus | 0-nieznany: stan nie umożliwia pobierania <br> 1 — gotowe: aplikacja działa prawidłowo <br> 2 — uaktualnianie: trwa uaktualnianie <br> 3 — Tworzenie: aplikacja jest tworzona <br> 4 — Usuwanie: aplikacja jest usuwana <br> 5 — niepowodzenie: wdrażanie aplikacji nie powiodło się | ND |
| Bajty | 0-nieprawidłowa: usługa nie ma obecnie stanu kondycji <br> 1 — OK: usługa jest w dobrej kondycji  <br> 2-ostrzeżenie: być może wystąpił problem z nieprawidłowym wymaganiem <br> 3 — błąd: Wystąpił problem, który wymaga badania <br> 4-nieznany: stan nie umożliwia pobierania | ND |
| ServiceReplicaStatus | 0-nieprawidłowa: replika nie ma obecnie stanu kondycji <br> 1 — OK: usługa jest w dobrej kondycji  <br> 2-ostrzeżenie: być może wystąpił problem z nieprawidłowym wymaganiem <br> 3 — błąd: Wystąpił problem, który wymaga badania <br> 4-nieznany: stan nie umożliwia pobierania | ND | 
| RestartCount | Liczba ponownych uruchomień kontenera | ND |

> [!NOTE]
> Wartości servicestatus i ServiceReplicaStatus są takie same jak dla [atrybutu HealthState](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) w Service Fabric. 

Każda Metryka jest dostępna w różnych wymiarach, dzięki czemu można zobaczyć zagregowane na różnych poziomach. Bieżąca lista wymiarów jest następująca:

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> Wymiar CodePackageName nie jest dostępny dla aplikacji systemu Linux. 

Każdy wymiar odpowiada różnym składnikom [modelu aplikacji Service Fabric](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Interfejs wiersza polecenia Azure Monitor

Pełna lista poleceń jest dostępna w dokumentacji [interfejsu wiersza polecenia Azure monitor](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) , ale zawarto kilka przydatnych przykładów poniżej 

W każdym przykładzie identyfikator zasobu jest zgodny z tym wzorcem

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Użycie procesora CPU przez kontenery w aplikacji

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Użycie pamięci dla każdej repliki usługi
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Ponowne uruchamianie dla każdego kontenera w oknie 1 godziny 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Średnie użycie procesora CPU przez usługi o nazwie "VotingWeb" w oknie 1 godziny
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Eksplorator metryk

Eksplorator metryk jest blokiem w portalu, w którym można wizualizować wszystkie metryki dla aplikacji siatki. Ten blok jest dostępny na stronie aplikacji w portalu i w bloku usługi Azure monitor, który służy do wyświetlania metryk dla wszystkich zasobów platformy Azure, które obsługują Azure Monitor. 

![Eksplorator metryk](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, przeczytaj [omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md).
* Aby dowiedzieć się więcej na temat poleceń metryk Azure Monitor, zapoznaj się z dokumentacją [interfejsu wiersza polecenia Azure monitor](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list).
