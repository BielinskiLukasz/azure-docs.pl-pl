---
title: Samouczek dotyczący usługi Kubernetes na platformie Azure — tworzenie rejestru kontenerów
description: W tym samouczku dotyczącym usługi Azure Kubernetes Service (AKS) utworzysz wystąpienie usługi Azure Container Registry i przekażesz przykładowy obraz kontenera aplikacji.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 51cfc62adaf9d9c780888477aa6eab2a812fe98c
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53718037"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>Samouczek: Wdrażanie usługi Azure Container Registry i korzystanie z niej

Usługa Azure Container Registry (ACR) to prywatny rejestr obrazów kontenera. Prywatny rejestr kontenerów umożliwia bezpieczne tworzenie i wdrażanie aplikacji oraz niestandardowego kodu. W tym samouczku (część druga z siedmiu) wdrożysz wystąpienie usługi ACR i wypchniesz do niego obraz kontenera. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie wystąpienia usługi Azure Container Registry
> * Tagowanie obrazu kontenera na potrzeby usługi ACR
> * Przekazywanie obrazu do usługi ACR
> * Wyświetlanie obrazów w rejestrze

W dodatkowych samouczkach to wystąpienie usługi ACR zostanie zintegrowane z klastrem Kubernetes w usłudze AKS, a aplikacja zostanie wdrożona na podstawie obrazu.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W [poprzednim samouczku ][aks-tutorial-prepare-app] utworzono obraz kontenera na potrzeby prostej aplikacji do głosowania platformy Azure. Jeśli obraz aplikacji do głosowania platformy Azure nie został utworzony, wróć do artykułu [Samouczek 1 — Tworzenie obrazów kontenerów][aks-tutorial-prepare-app].

Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.53 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-an-azure-container-registry"></a>Tworzenie rejestru Azure Container Registry

Aby utworzyć rejestr Azure Container Registry, należy najpierw mieć grupę zasobów. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą polecenia [az group create][az-group-create]. W poniższym przykładzie grupa zasobów o nazwie *myResourceGroup* zostanie utworzona w regionie *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Utwórz wystąpienie usługi Azure Container Registry przy użyciu polecenia [az acr create][az-acr-create] i podaj własną nazwę rejestru. Nazwa rejestru musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych. W dalszej części tego samouczka wartość `<acrName>` zostanie użyta jako symbol zastępczy nazwy rejestru kontenerów. Podaj własną, unikatową nazwę rejestru. *Podstawowa* jednostka SKU to zoptymalizowany pod kątem kosztów punkt wejścia do celów programistycznych zapewniający równowagę między przestrzenią dyskową i przepływnością.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

## <a name="log-in-to-the-container-registry"></a>Logowanie do rejestru kontenerów

Aby użyć wystąpienia usługi ACR, należy się najpierw zalogować. Użyj polecenia [az acr login][az-acr-login] i podaj unikatową nazwę nadaną rejestrowi kontenerów w poprzednim kroku.

```azurecli
az acr login --name <acrName>
```

Polecenie zwraca komunikat *Logowanie pomyślne* po ukończeniu.

## <a name="tag-a-container-image"></a>Tagowanie obrazu kontenera

Aby wyświetlić listę bieżących obrazów lokalnych, użyj polecenia [docker images][docker-images]:

```
$ docker images

REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Aby użyć obrazu kontenera *azure-vote-front* z usługą ACR, obraz musi być otagowany za pomocą adresu serwera logowania rejestru. Ten tag jest używany na potrzeby kierowania podczas wypychania obrazów kontenerów do rejestru obrazów.

Aby uzyskać adres serwera logowania, użyj polecenia [az acr list][az-acr-list] i wykonaj zapytanie dotyczące wartości *loginServer* w następujący sposób:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Następnie otaguj lokalny obraz *azure-vote-front* za pomocą adresu *acrloginServer* rejestru kontenerów. Aby wskazać wersję obrazu, dodaj wartość *:v1* na końcu nazwy obrazu:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Aby sprawdzić, czy tagi zostały zastosowane, ponownie uruchom polecenie [docker images][docker-images]. Obraz jest tagowany za pomocą adresu wystąpienia usługi ACR i numeru wersji.

```
$ docker images

REPOSITORY                                           TAG           IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest        eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry.azurecr.io/azure-vote-front      v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest        a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask         788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Wypychanie obrazów do rejestru

Po skompilowaniu i otagowaniu obrazu *azure-vote-front* możesz wypchnąć go do wystąpienia usługi ACR. Użyj polecenia [docker push][docker-push] i podaj własny adres *acrLoginServer* na potrzeby nazwy obrazu w następujący sposób:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

Wypchnięcie obrazu do usługi ACR może potrwać kilka minut.

## <a name="list-images-in-registry"></a>Wyświetlanie listy obrazów w rejestrze

Aby zwrócić listę obrazów, które zostały wypchnięte do wystąpienia usługi ACR, użyj polecenia [az acr repository list][az-acr-repository-list]. Podaj własną nazwę `<acrName>` w następujący sposób:

```azurecli
az acr repository list --name <acrName> --output table
```

Poniższe przykładowe dane wyjściowe wyświetlają obraz *azure-vote-front* jako dostępny w rejestrze:

```
Result
----------------
azure-vote-front
```

Aby wyświetlić tagi dla określonego obrazu, użyj polecenia [az acr repository show-tags][az-acr-repository-show-tags] w następujący sposób:

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Poniższe przykładowe dane wyjściowe przedstawiają obraz *v1* otagowany w poprzednim kroku:

```
Result
--------
v1
```

Masz teraz obraz kontenera przechowywany w prywatnym wystąpieniu usługi Azure Container Registry. W kolejnym samouczku ten obraz zostanie wdrożony z usługi ACR do klastra Kubernetes.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono usługę Azure Container Registry oraz wypchnięto obraz w celu użycia w klastrze usługi AKS. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie wystąpienia usługi Azure Container Registry
> * Tagowanie obrazu kontenera na potrzeby usługi ACR
> * Przekazywanie obrazu do usługi ACR
> * Wyświetlanie obrazów w rejestrze

Przejdź do kolejnego samouczka, aby dowiedzieć się, jak wdrażać klaster Kubernetes na platformie Azure.

> [!div class="nextstepaction"]
> [Wdrażanie klastra Kubernetes][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#create
[az-acr-list]: /cli/azure/acr#list
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az-acr-login
[az-acr-list]: https://docs.microsoft.com/cli/azure/acr#az-acr-list
[az-acr-repository-list]: /cli/azure/acr/repository#list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#show-tags
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
