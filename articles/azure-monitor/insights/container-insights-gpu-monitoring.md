---
title: Konfigurowanie monitorowania procesora GPU przy użyciu Azure Monitor dla kontenerów | Microsoft Docs
description: W tym artykule opisano, jak można skonfigurować monitorowanie klastrów Kubernetes z węzłami z obsługą procesorów NVIDIA i AMD przy użyciu Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 958f5ab33edcd280f5673391eba907728f1153c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80373312"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>Konfigurowanie monitorowania procesora GPU przy użyciu Azure Monitor dla kontenerów

Począwszy od wersji agenta *ciprod03022019*, usługa Azure monitor for Containers Integrated Agent obsługuje teraz monitorowanie procesora GPU (graficzne jednostki przetwarzania) w węzłach klastra KUBERNETES z procesorem GPU i monitorowanie obciążeń/kontenerów żądających i korzystających z zasobów procesora GPU.

## <a name="supported-gpu-vendors"></a>Obsługiwani dostawcy procesora GPU

Azure Monitor kontenerów obsługuje monitorowanie klastrów procesora GPU od następujących dostawców GPU:

- [GRAFICZNY](https://developer.nvidia.com/kubernetes-gpu)

- [PROCESORÓW](https://github.com/RadeonOpenCompute/k8s-device-plugin)

Azure Monitor dla kontenerów automatycznie uruchamia monitorowanie użycia procesora GPU w węzłach oraz procesor GPU żądający zasobników i obciążeń, zbierając następujące metryki w interwałach 60sec i przechowując je w tabeli **InsightMetrics** :

|Nazwa metryki |Wymiar metryki (Tagi) |Opis |
|------------|------------------------|------------|
|containerGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, ContainerName, gpuId, gpuModel, gpuVendor|Procent czasu w ciągu ostatniej próbki (60 sekund), w którym procesor GPU był zajęty/aktywnie przetwarzany dla kontenera. Cykl cła to liczba z zakresu od 1 do 100. |
|containerGpuLimits |container.azm.ms/clusterId, container.azm.ms/clusterName, ContainerName |Każdy kontener może określać limity jako jeden lub więcej procesorów GPU. Nie jest możliwe zażądanie lub ograniczenie ułamka procesora GPU. |
|containerGpuRequests |container.azm.ms/clusterId, container.azm.ms/clusterName, ContainerName |Każdy kontener może zażądać jednego lub większej liczby procesorów GPU. Nie jest możliwe zażądanie lub ograniczenie ułamka procesora GPU.|
|containerGpumemoryTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, ContainerName, gpuId, gpuModel, gpuVendor |Ilość pamięci procesora GPU dostępna do użycia dla określonego kontenera. |
|containerGpumemoryUsedBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, ContainerName, gpuId, gpuModel, gpuVendor |Ilość pamięci procesora GPU w bajtach używanych przez określony kontener. |
|nodeGpuAllocatable |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Liczba procesorów GPU w węźle, które mogą być używane przez Kubernetes. |
|nodeGpuCapacity |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Łączna liczba procesorów GPU w węźle. |

## <a name="gpu-performance-charts"></a>Wykresy wydajności procesora GPU 

Azure Monitor kontenerów zawiera wstępnie skonfigurowane wykresy dla metryk wymienionych wcześniej w tabeli jako skoroszyt GPU dla każdego klastra. Można znaleźć **procesor GPU węzła podrzędnego** bezpośrednio z klastra AKS, wybierając **skoroszyty** z okienka po lewej stronie, a następnie z listy rozwijanej **Wyświetl skoroszyty** w szczegółowej części.

## <a name="next-steps"></a>Następne kroki

- Zobacz [Korzystanie z procesorów GPU w przypadku obciążeń intensywnie korzystających z obliczeń w usłudze Azure Kubernetes Service](../../aks/gpu-cluster.md) (AKS), aby dowiedzieć się, jak WDROŻYĆ klaster AKS zawierający węzły z obsługą procesora GPU.

- Dowiedz się więcej o [jednostkach SKU zoptymalizowanych pod kątem procesora GPU w Microsoft Azure](../../virtual-machines/sizes-gpu.md).

- Zapoznaj się z [obsługą procesora GPU w Kubernetes](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) , aby dowiedzieć się więcej o eksperymentalnej obsłudze Kubernetes do zarządzania procesorami GPU w jednym lub wielu węzłach w klastrze