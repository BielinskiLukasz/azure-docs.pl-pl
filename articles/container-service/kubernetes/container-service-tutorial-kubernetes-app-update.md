---
title: Samouczek usługi Azure Container Service — aktualizowanie aplikacji
description: Samouczek usługi Azure Container Service — aktualizowanie aplikacji
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: f54179329b521cc861e90f023ff0b010b7ce1f75
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="update-an-application-in-kubernetes"></a>Aktualizowanie aplikacji w usłudze Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Po wdrożeniu aplikacji w usłudze Kubernetes można ją zmodyfikować, określając nowy obraz kontenera lub wersję obrazu. W takiej sytuacji aktualizacja jest przygotowywana tak, aby tylko część była współbieżnie aktualizowana. Ta aktualizacja etapowa umożliwia kontynuowanie działania podczas aktualizacji aplikacji. Udostępnia ona również mechanizm wycofywania w przypadku niepowodzenia wdrożenia. 

W tym samouczku (część szósta z siedmiu) aktualizowana jest przykładowa aplikacja do głosowania platformy Azure. Zadania do wykonania to na przykład:

> [!div class="checklist"]
> * Aktualizowanie kodu aplikacji frontonu
> * Tworzenie zaktualizowanego obrazu kontenera
> * Wypychanie obrazu kontenera do usługi Azure Container Registry
> * Wdrażanie zaktualizowanego obrazu kontenera

W kolejnych samouczkach usługa Log Analytics zostanie skonfigurowana do monitorowania klastra usługi Kubernetes.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poprzednich samouczkach aplikacja została spakowana w postaci obrazu kontenera, obraz został przekazany do usługi Azure Container Registry i utworzono klaster usługi Kubernetes. Następnie uruchomiono aplikację w klastrze usługi Kubernetes. 

Sklonowano również repozytorium aplikacji, w tym kod źródłowy aplikacji i utworzony wcześniej plik narzędzia Docker Compose używany w tym samouczku. Sprawdź, czy został utworzony klon repozytorium oraz czy katalogi zostały zmienione na sklonowany katalog. Zawiera on katalog o nazwie `azure-vote` i plik o nazwie `docker-compose.yml`.

Jeśli nie wykonano tych kroków, a chcesz kontynuować pracę, wróć do części [Samouczek 1 — tworzenie obrazów kontenera](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="update-application"></a>Aktualizowanie aplikacji

W tym samouczku wprowadzana jest zmiana aplikacji, a zaktualizowana aplikacja jest wdrażana w klastrze usługi Kubernetes. 

Kod źródłowy aplikacji znajduje się wewnątrz katalogu `azure-vote`. Otwórz plik `config_file.cfg` w dowolnym edytorze kodu lub tekstu. W tym przykładzie używamy programu `vi`.

```bash
vi azure-vote/azure-vote/config_file.cfg
```

Zmień wartości elementów `VOTE1VALUE` i `VOTE2VALUE`, a następnie zapisz plik.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Zapisz i zamknij plik.

## <a name="update-container-image"></a>Aktualizowanie obrazu kontenera

Użyj narzędzia [docker-compose](https://docs.docker.com/compose/), aby ponownie utworzyć obraz frontonu i uruchomić zaktualizowaną aplikację. Argument `--build` jest używany w celu poinstruowania narzędzia Docker Compose o konieczności ponownego utworzenia obrazu aplikacji.

```bash
docker-compose up --build -d
```

## <a name="test-application-locally"></a>Testowanie aplikacji w środowisku lokalnym

Przejdź do adresu http://localhost:8080, aby wyświetlić zaktualizowaną aplikację.

![Obraz przedstawiający klaster Kubernetes na platformie Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Tagowanie i wypychanie obrazów

Otaguj obraz `azure-vote-front` wartością loginServer rejestru kontenerów. 

Pobierz nazwę serwera logowania przy użyciu polecenia [az acr list](/cli/azure/acr#az_acr_list).

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Użyj polecenia [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) w celu otagowania obrazu. Zastąp element `<acrLoginServer>` nazwą serwera logowania usługi Azure Container Registry lub nazwą hosta rejestru publicznego. Zwróć również uwagę, że obraz został zaktualizowany do wersji `redis-v2`.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

Użyj polecenia [docker push](https://docs.docker.com/engine/reference/commandline/push/) w celu przekazania obrazu do rejestru. Zastąp element `<acrLoginServer>` nazwą serwera logowania usługi Azure Container Registry.

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v2
```

## <a name="deploy-update-application"></a>Wdrażanie zaktualizowanej aplikacji

Aby zapewnić maksymalny czas działania, należy uruchomić wiele wystąpień zasobnika aplikacji. Sprawdź tę konfigurację przy użyciu polecenia [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```bash
kubectl get pod
```

Dane wyjściowe:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Jeśli obraz azure-vote-front nie został uruchomiony w wielu zasobnikach, skaluj wdrożenie `azure-vote-front`.


```azurecli-interactive
kubectl scale --replicas=3 deployment/azure-vote-front
```

Aby zaktualizować aplikację, użyj polecenia [kubectl set](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#set). Zaktualizuj element `<acrLoginServer>` przy użyciu nazwy serwera logowania lub nazwy hosta rejestru kontenerów.

```azurecli-interactive
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:redis-v2
```

Aby monitorować wdrożenie, użyj polecenia [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get). Podczas wdrażania aplikacji działanie zasobników jest przerywane, a następnie są one ponownie tworzone przy użyciu obrazu kontenera.

```azurecli-interactive
kubectl get pod
```

Dane wyjściowe:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>Testowanie zaktualizowanej aktualizacji

Pobierz zewnętrzny adres IP usługi `azure-vote-front`.

```azurecli-interactive
kubectl get service azure-vote-front
```

Przejdź do adresu IP, aby wyświetlić zaktualizowaną aplikację.

![Obraz przedstawiający klaster Kubernetes na platformie Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku zaktualizowano aplikację i wydano tę aktualizację do klastra Kubernetes. Wykonano następujące zadania:

> [!div class="checklist"]
> * Zaktualizowano kod aplikacji frontonu
> * Utworzono zaktualizowany obraz kontenera
> * Wypchnięto obraz kontenera do usługi Azure Container Registry
> * Wdrożono zaktualizowaną aplikację

Przejdź do następnego samouczka, aby dowiedzieć się, jak monitorować usługę Kubernetes przy użyciu usługi Log Analytics.

> [!div class="nextstepaction"]
> [Monitorowanie rozwiązania Kubernetes za pomocą usługi Log Analytics](./container-service-tutorial-kubernetes-monitor.md)