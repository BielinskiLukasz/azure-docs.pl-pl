---
title: Samouczek dotyczący rozwiązania Kubernetes na platformie Azure — skalowanie aplikacji
description: W tym samouczku dotyczącym usługi Azure Kubernetes Service (AKS) dowiesz się, jak skalować węzły i zasobniki w rozwiązaniu Kubernetes oraz implementować automatyczne skalowanie zasobników w poziomie.
services: container-service
ms.topic: tutorial
ms.date: 01/14/2019
ms.custom: mvc
ms.openlocfilehash: 5c1cbebd671568d200321615ad34f52cb636c6c8
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878089"
---
# <a name="tutorial-scale-applications-in-azure-kubernetes-service-aks"></a>Samouczek: Skalowanie aplikacji w usłudze Azure Kubernetes Service (AKS)

Jeśli wykonujesz kolejno zadania z samouczków, masz już działający klaster Kubernetes w usłudze AKS z wdrożoną przykładową aplikacją do głosowania platformy Azure. Ta część samouczka, piąta z siedmiu, obejmuje skalowanie w poziomie zasobników w tej aplikacji oraz skalowanie automatyczne. Dowiesz się również, jak przez skalowanie liczby węzłów maszyny wirtualnej platformy Azure zmieniać możliwości hostowania obciążeń w klastrze. Omawiane kwestie:

> [!div class="checklist"]
> * Skalowanie węzłów rozwiązania Kubernetes
> * Ręczne skalowanie zasobników rozwiązania Kubernetes, w ramach których działa Twoja aplikacja
> * Konfigurowanie automatycznie skalowanych zasobników, w ramach których działa fronton aplikacji

W dodatkowych samouczkach aplikacja Azure Vote jest aktualizowana do nowej wersji.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poprzednich samouczkach aplikacja była spakowana do obrazu kontenera. Ten obraz został przekazany do usługi Azure Container Registry i utworzono klaster usługi AKS. Aplikacja została następnie wdrożona w klastrze usługi AKS. Jeśli nie wykonano tych kroków, a chcesz kontynuować pracę, zacznij od części [Samouczek 1 — tworzenie obrazów kontenera][aks-tutorial-prepare-app].

Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.53 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="manually-scale-pods"></a>Ręczne skalowanie zasobników

W momencie wdrożenia frontonu aplikacji Azure Vote i wystąpienia pamięci podręcznej Redis w poprzednich samouczkach została utworzona pojedyncza replika. Aby wyświetlić liczbę i stan zasobników w klastrze, użyj polecenia [kubectl get][kubectl-get] w następujący sposób:

```console
kubectl get pods
```

Poniższe przykładowe dane wyjściowe zawierają jeden zasobnik frontonu i jeden zasobnik zaplecza:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Aby ręcznie zmienić liczbę zasobników w ramach wdrożenia aplikacji *azure-vote-front*, użyj polecenia [kubectl scale][kubectl-scale]. W poniższym przykładzie liczba zasobników frontonu jest zwiększana do *5*:

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

Ponownie uruchom polecenie [kubectl get][kubectl-get], aby sprawdzić, czy rozwiązanie AKS tworzy dodatkowe zasobniki. Po upływie około minuty dodatkowe zasobniki będą dostępne w Twoim klastrze:

```console
kubectl get pods

                                    READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Automatyczne skalowanie zasobników

Rozwiązanie Kubernetes obsługuje [automatyczne skalowanie zasobników w poziomie][kubernetes-hpa], umożliwiające dostosowywanie liczby zasobników we wdrożeniu do użycia procesora lub innych wybranych metryk. [Serwer metryk][metrics-server] służy do zapewnienia wykorzystania zasobów w usłudze Kubernetes i jest automatycznie wdrożony w klastrach AKS w wersji 1.10 lub nowszej. Aby wyświetlić wersję klastra AKS, użyj polecenia [az aks show][az-aks-show], jak pokazano w poniższym przykładzie:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query kubernetesVersion --output table
```

> [!NOTE]
> Jeśli klaster AKS jest mniejszy niż *1.10,* serwer metryki nie jest instalowany automatycznie. Aby go zainstalować, sklonuj repozytorium GitHub `metrics-server` i zainstaluj przykładowe definicje zasobów. Aby wyświetlić zawartość tych definicji YAML, zobacz [program Metrics Server dla platformy Kuberenetes w wersji 1.8 lub nowszej][metrics-server-github].
> 
> ```console
> git clone https://github.com/kubernetes-incubator/metrics-server.git
> kubectl create -f metrics-server/deploy/1.8+/
> ```

Aby korzystać z skalowania automatycznego, wszystkie kontenery w zasobnikach i zasobnikach muszą mieć zdefiniowane żądania procesora CPU i limity. We wdrożeniu aplikacji `azure-vote-front` kontener frontonu wymaga już 0,25 CPU, a limit wynosi 0,5 CPU. Te żądania zasobów i limity są zdefiniowane w taki sposób, jak pokazano w poniższym przykładowym fragmencie kodu:

```yaml
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

W poniższym przykładzie użyto polecenia [kubectl autoscale][kubectl-autoscale] w celu przeprowadzenia automatycznego skalowania liczby zasobników we wdrożeniu aplikacji *azure-vote-front*. Jeśli średnie wykorzystanie procesora CPU we wszystkich zasobnikach przekracza 50% ich żądanego użycia, skalowanie automatyczne zwiększa zasobników do maksymalnie *10* wystąpień. Dla wdrożenia zostaną następnie zdefiniowane przynajmniej *3* wystąpienia:

```console
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Alternatywnie można utworzyć plik manifestu, aby zdefiniować zachowanie skalowania automatycznego i limity zasobów. Poniżej przedstawiono przykład pliku `azure-vote-hpa.yaml`manifestu o nazwie .

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-back-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-back
  targetCPUUtilizationPercentage: 50 # target CPU utilization


apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-front-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-front
  targetCPUUtilizationPercentage: 50 # target CPU utilization
```

Służy `kubectl apply` do stosowania skalowania automatycznego zdefiniowanego w pliku manifestu. `azure-vote-hpa.yaml`

```
kubectl apply -f azure-vote-hpa.yaml
```

Aby wyświetlić stan skalowania automatycznego, użyj polecenia `kubectl get hpa` w następujący sposób:

```
kubectl get hpa

NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Po upływie kilku minut przy minimalnym obciążeniu aplikacji Azure Vote liczba replik zasobników zostanie automatycznie zmniejszona do 3. Możesz ponownie użyć polecenia `kubectl get pods`, aby sprawdzić stan usuwania niepotrzebnych zasobników.

## <a name="manually-scale-aks-nodes"></a>Ręczne skalowanie węzłów usługi AKS

Jeśli klaster kubernetes został utworzony przy użyciu poleceń w poprzednim samouczku, ma dwa węzły. Jeśli planujesz zwiększenie lub zmniejszenie liczby obciążeń kontenerów w klastrze, możesz ręcznie dostosować liczbę węzłów.

W poniższym przykładzie liczba węzłów w klastrze Kubernetes o nazwie *myAKSCluster* zostanie zwiększona do trzech. Wykonanie tego polecenia może zająć kilka minut.

```azurecli
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 3
```

Po pomyślnym skalowaniu klastra dane wyjściowe będą podobne do poniższego przykładu:

```
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myAKSCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto różnych funkcji skalowania w klastrze Kubernetes. W tym samouczku omówiono:

> [!div class="checklist"]
> * Ręczne skalowanie zasobników rozwiązania Kubernetes, w ramach których działa Twoja aplikacja
> * Konfigurowanie automatycznie skalowanych zasobników, w ramach których działa fronton aplikacji
> * Ręczne skalowanie węzłów rozwiązania Kubernetes

Przejdź do następnego samouczka, aby dowiedzieć się, jak zaktualizować aplikację w rozwiązaniu Kubernetes.

> [!div class="nextstepaction"]
> [Aktualizowanie aplikacji w usłudze Kubernetes][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[metrics-server-github]: https://github.com/kubernetes-incubator/metrics-server/tree/master/deploy/1.8%2B
[metrics-server]: https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/#metrics-server

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
