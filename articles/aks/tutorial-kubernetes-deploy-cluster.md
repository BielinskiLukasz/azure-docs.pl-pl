---
title: Samouczek dotyczący usługi Kubernetes na platformie Azure — wdrażanie klastra
description: W tym samouczku dotyczącym usługi Azure Kubernetes Service (AKS) utworzysz klaster usługi AKS i nawiążesz połączenie z węzłem głównym usługi Kubernetes za pomocą narzędzia kubectl.
services: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.custom: mvc
ms.openlocfilehash: e0163b844bf677f0a74e308c7c873c42e6e57c8c
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593227"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Samouczek: wdrażanie klastra usługi Azure Kubernetes Service (AKS)

Usługa Kubernetes zapewnia rozproszoną platformę dla konteneryzowanych aplikacji. Za pomocą usługi AKS można szybko utworzyć klaster Kubernetes gotowy do użycia w środowisku produkcyjnym. W tym samouczku (część trzecia z siedmiu) w usłudze AKS jest wdrażany klaster Kubernetes. Omawiane kwestie:

> [!div class="checklist"]
> * Wdrażanie klastra Kubernetes AKS, który może być uwierzytelniany w usłudze Azure Container Registry
> * Instalowanie interfejsu wiersza polecenia rozwiązania Kubernetes (kubectl)
> * Konfigurowanie narzędzia kubectl w celu nawiązania połączenia z klastrem AKS

W dodatkowych samouczkach aplikacja Azure Vote jest wdrażana w klastrze, skalowana i aktualizowana.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poprzednich samouczkach utworzono obraz kontenera i przekazano go do wystąpienia usługi Azure Container Registry. Jeśli nie wykonano tych kroków, a chcesz skorzystać z [samouczka 1 — Tworzenie obrazów kontenerów][aks-tutorial-prepare-app].

Ten samouczek wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.75 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-a-kubernetes-cluster"></a>Tworzenie klastra Kubernetes

Klastry usługi AKS mogą używać kontroli dostępu opartej na rolach (RBAC) rozwiązania Kubernetes. Te kontrolki umożliwiają zdefiniowanie dostępu do zasobów na podstawie ról przypisanych użytkownikom. Uprawnienia są łączone, jeśli użytkownikowi przypisano wiele ról, a zakres uprawnień można ograniczyć do jednej przestrzeni nazw lub do całego klastra. Domyślnie interfejs wiersza polecenia platformy Azure automatycznie włącza kontrolę dostępu opartą na rolach podczas tworzenia klastra usługi AKS.

Utwórz klaster usługi AKS za pomocą polecenia [az aks create][]. W poniższym przykładzie tworzony jest klaster o nazwie *myAKSCluster* w grupie zasobów o nazwie *myResourceGroup*. Ta grupa zasobów została utworzona w [poprzednim samouczku][aks-tutorial-prepare-acr]. Aby umożliwić klastrowi AKS współdziałanie z innymi zasobami platformy Azure, zostanie automatycznie utworzona jednostka usługi Azure Active Directory, ponieważ nie została ona określona. W tym miejscu ta jednostka usługi ma uprawnienia [do ściągania obrazów][container-registry-integration] z wystąpienia Azure Container Registry (ACR) utworzonego w poprzednim samouczku.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

Po kilku minutach wdrażanie zostanie zakończone i zwróci informacje o wdrożeniu usługi AKS w formacie JSON.

> [!NOTE]
> Aby zapewnić niezawodne działanie klastra, należy uruchomić co najmniej 2 (dwa) węzły.

## <a name="install-the-kubernetes-cli"></a>Instalowanie interfejsu wiersza polecenia rozwiązania Kubernetes

Aby nawiązać połączenie z klastrem Kubernetes z komputera lokalnego, należy użyć [polecenia kubectl][kubectl], klienta wiersza polecenia Kubernetes.

Jeśli korzystasz z usługi Azure Cloud Shell, narzędzie `kubectl` jest już zainstalowane. Możesz także zainstalować je lokalnie za pomocą polecenia [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Nawiązywanie połączenia z klastrem przy użyciu narzędzia kubectl

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][]. Poniższy przykład umożliwia pobranie poświadczeń dla nazwy klastra usługi AKS *myAKSCluster* w grupie zasobów *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Aby sprawdzić połączenie z klastrem, uruchom polecenie [polecenia kubectl Get nodes][kubectl-get] , aby zwrócić listę węzłów klastra:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-12345678-0   Ready    agent   32m   v1.13.10
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono klaster Kubernetes w usłudze AKS i skonfigurowano narzędzie `kubectl` w celu nawiązania z nim połączenia. W tym samouczku omówiono:

> [!div class="checklist"]
> * Wdrażanie klastra Kubernetes AKS, który może być uwierzytelniany w usłudze Azure Container Registry
> * Instalowanie interfejsu wiersza polecenia rozwiązania Kubernetes (kubectl)
> * Konfigurowanie narzędzia kubectl w celu nawiązania połączenia z klastrem AKS

Przejdź do następnego samouczka, aby dowiedzieć się, jak wdrożyć aplikację w klastrze.

> [!div class="nextstepaction"]
> [Uruchamianie aplikacji w usłudze Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
