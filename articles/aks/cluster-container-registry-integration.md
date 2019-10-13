---
title: Integracja Azure Container Registry z usługą Azure Kubernetes Service
description: Dowiedz się, jak zintegrować usługę Azure Kubernetes Service (AKS) z usługą Azure Container Registry (ACR)
services: container-service
author: mlearned
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 09/17/2018
ms.author: mlearned
ms.openlocfilehash: bbd08e49256886a1df334cbf36e6e468bb8f3895
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286787"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Uwierzytelnianie za pomocą Azure Container Registry z usługi Azure Kubernetes

W przypadku korzystania z Azure Container Registry (ACR) z usługą Azure Kubernetes Service (AKS) należy nawiązać mechanizm uwierzytelniania. W tym artykule przedstawiono przykłady konfigurowania uwierzytelniania między tymi dwiema usługami platformy Azure.

Za pomocą interfejsu wiersza polecenia platformy Azure można skonfigurować AKS do integracji z ACR w kilku prostych poleceniach.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Te przykłady wymagają:

* Rola **właściciela** lub **administratora konta platformy Azure** w **subskrypcji platformy Azure**
* Interfejs wiersza polecenia platformy Azure w wersji 2.0.73 lub nowszej

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Utwórz nowy klaster AKS z integracją ACR

Integrację AKS i ACR można skonfigurować podczas początkowego tworzenia klastra AKS.  Aby umożliwić klastrowi AKS współdziałanie z ACR, używana jest jednostka **usługi** Azure Active Directory. Następujące polecenie interfejsu wiersza polecenia pozwala autoryzować istniejące ACR w ramach subskrypcji i skonfigurować odpowiednią rolę **ACRPull** dla jednostki usługi. Podaj prawidłowe wartości parametrów poniżej.  Parametry w nawiasach są opcjonalne.
```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --attach-acr $MYACR

```
Alternatywnie można określić nazwę ACR przy użyciu identyfikatora zasobu ACR, który ma następujący format:

/subscriptions/\<subscription-ID @ no__t-1/resourceGroups/\<resource-Group-Name @ no__t-3/Providers/Microsoft. ContainerRegistry/rejestrs/\<name @ no__t-5 
 
```azurecli
az aks create -n myAKSCluster -g myResourceGroup --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Wykonanie tego kroku może potrwać kilka minut.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Konfigurowanie integracji usługi ACR dla istniejących klastrów AKS

Zintegruj istniejący ACR z istniejącymi klastrami AKS, dostarczając prawidłowe wartości dla **ACR-Name** lub **ACR-Resource-ID** w następujący sposób.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
```
Oraz
```
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Można również usunąć integrację między usługą ACR a klastrem AKS z następującymi elementami:
```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
```
lub
```
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Praca z ACR & AKS

### <a name="import-an-image-into-your-acr"></a>Importowanie obrazu do ACR

Zaimportuj obraz z usługi Docker Hub do ACR, wykonując następujące czynności:


```azurecli
az acr import  -n <myContainerRegistry> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Wdrażanie przykładowego obrazu z ACR do AKS

Upewnij się, że masz odpowiednie poświadczenia AKS

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Utwórz plik o nazwie **ACR-Nginx. YAML** , który zawiera następujące elementy:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <replace this image property with you acr login server, image and tag>
        ports:
        - containerPort: 80
```

Następnie Uruchom to wdrożenie w klastrze AKS:
```
kubectl apply -f acr-nginx.yaml
```

Wdrożenie można monitorować, uruchamiając następujące:
```
kubectl get pods
```
Należy mieć dwa uruchomione zasobniki.
```
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
