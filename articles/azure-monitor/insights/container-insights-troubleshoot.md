---
title: Jak rozwiązywanie problemów z usługi Azure Monitor dla kontenerów (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak rozwiązywać problemy i rozwiązywanie problemów z usługą Azure Monitor dla kontenerów.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2018
ms.author: magoedte
ms.openlocfilehash: da35f88550bb7bd1a4543f03936dcd52ddd926d9
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2018
ms.locfileid: "52308363"
---
# <a name="troubleshooting-azure-monitor-for-containers-preview"></a>Rozwiązywanie problemów z usługi Azure Monitor dla kontenerów (wersja zapoznawcza)

Po skonfigurowaniu monitorowania klastra usługi Azure Kubernetes Service (AKS) z usługą Azure Monitor dla kontenerów, można napotkać problem uniemożliwia zbieranie danych i raportowania stanu. Ten artykuł szczegółowo opisuje niektóre typowe problemy i kroki rozwiązywania problemów.

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Usługa Azure Monitor dla kontenerów jest włączona, ale nie zgłasza żadnych informacji
Jeśli usługi Azure Monitor dla kontenerów została pomyślnie włączona i skonfigurowana, ale nie można wyświetlić informacje o stanie lub żadne wyniki nie są zwracane z zapytań dzienników usługi Log Analytics, można zdiagnozować problem, wykonaj następujące czynności: 

1. Sprawdź stan agenta, uruchamiając polecenie: 

    `kubectl get ds omsagent --namespace=kube-system`

    Dane wyjściowe powinien przypominać poniższe polecenie, co oznacza, że została prawidłowo wdrożona:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Sprawdź stan wdrożenia za pomocą wersji agenta *06072018* lub później za pomocą polecenia:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Dane wyjściowe powinny podobne do następującego przykładu, co oznacza, że została prawidłowo wdrożona:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Sprawdź stan zasobników, aby sprawdzić, czy działa przy użyciu polecenia: `kubectl get pods --namespace=kube-system`

    Dane wyjściowe powinny być podobne do następującego przykładu, ze stanem *systemem* dla omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Sprawdź dzienniki agenta. Gdy agent konteneryzowanych zostanie wdrożona, uruchamia szybkie sprawdzenie, uruchamiając polecenia OMI i wyświetla wersję agenta i dostawcy. 

5. Aby sprawdzić, czy agent zakończy się powodzeniem dołączone, uruchom polecenie: `kubectl logs omsagent-484hw --namespace=kube-system`

    Stan powinien wyglądać następująco:

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="error-messages"></a>Komunikaty o błędach

Poniższa tabela zawiera podsumowanie znanych błędów, które mogą wystąpić podczas korzystania z usługi Azure Monitor dla kontenerów.

| Komunikaty o błędach  | Akcja |  
| ---- | --- |  
| Komunikat o błędzie `No data for selected filters`  | Może potrwać kilka nawiązać monitorowania przepływu danych w przypadku nowo utworzonego klastrów. Poczekaj co najmniej 10 do 15 minut, zanim dane zostaną wyświetlone dla klastra. |   
| Komunikat o błędzie `Error retrieving data` | Gdy klaster usługi Azure Kubenetes Service to skonfigurowanie do monitorowania kondycji i wydajności, połączenie zostaje nawiązane między klastra i obszaru roboczego usługi Azure Log Analytics. Obszar roboczy usługi Log Analytics służy do przechowywania wszystkich danych monitorowania dla klastra. Ten błąd może wystąpić, gdy obszar roboczy usługi Log Analytics został usunięty lub utracony. Sprawdź, czy Twój obszar roboczy jest dostępna, przeglądając [zarządzanie dostępem](../../log-analytics/log-analytics-manage-access.md?toc=/azure/azure-monitor/toc.json#workspace-information). Jeśli brakuje obszaru roboczego, należy ponownie dołączyć klastra za pomocą usługi Azure Monitor dla kontenerów. Składanie-dołączania, konieczne będzie [wyłączyć](container-insights-optout.md) monitorowania dla klastra i [Włącz](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-for-a-new-cluster) usługi Azure Monitor dla kontenerów ponownie. |  
| `Error retrieving data` Po dodaniu usługi Azure Monitor dla kontenerów za pomocą interfejsu wiersza polecenia az aks | Podczas dołączania za pomocą `az aks cli`, bardzo rzadko, usługi Azure Monitor dla kontenerów może nie być poprawnie dołączone. Sprawdź, czy rozwiązanie jest dołączona. Aby to zrobić, przejdź do obszaru roboczego usługi Log Analytics i sprawdzić, czy rozwiązanie jest dostępne, wybierając **rozwiązania** w okienku po lewej stronie. Aby rozwiązać ten problem, należy ponownie wdrożyć rozwiązanie, postępując zgodnie z instrukcjami wyświetlanymi [sposób wdrażania usługi Azure Monitor dla kontenerów](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json) |  

Aby pomóc w zdiagnozowaniu problemu, udostępniliśmy skrypt rozwiązywania problemów dostępne [tutaj](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script).  

## <a name="next-steps"></a>Kolejne kroki
Przy użyciu funkcji monitorowania, włączyć przechwytywania metryk kondycji dla węzłów klastra usługi AKS i zasobników, te metryki kondycji są dostępne w witrynie Azure portal. Aby dowiedzieć się, jak używać usługi Azure Monitor dla kontenerów, zobacz [widok usługi Azure Kubernetes Service health](container-insights-analyze.md).