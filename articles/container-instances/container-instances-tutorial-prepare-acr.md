---
title: Samouczek usługi Azure Container Instances — przygotowywanie usługi Azure Container Registry
description: Samouczek usługi Azure Container Instances (część 2 z 3) — przygotowywanie usługi Azure Container Registry
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: e01c736896043ac7639a374c4f75390c4a0e2e52
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422216"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>Samouczek: wdrażanie usługi Azure Container Registry i korzystanie z niej

Niniejszy samouczek jest drugą częścią trzyczęściowej serii. [W części pierwszej](container-instances-tutorial-prepare-app.md) samouczka utworzono obraz kontenera Docker dla aplikacji internetowej środowiska Node.js. W tym samouczku obraz zostanie wypchnięty do usługi Azure Container Registry. Jeśli obraz kontenera nie został jeszcze utworzony, wróć do artykułu [Samouczek 1 — Tworzenie obrazu kontenera](container-instances-tutorial-prepare-app.md).

Usługa Azure Container Registry jest prywatnym rejestrem platformy Docker na platformie Azure. W tym samouczku zostanie utworzone wystąpienie usługi Azure Container Registry w subskrypcji, a następnie zostanie do niego wypchnięty wcześniej utworzony obraz kontenera. W tym artykule, który jest drugą częścią trzyczęściowej serii, zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Utworzenie wystąpienia usługi Azure Container Registry
> * Otagowanie obrazu kontenera na potrzeby usługi Azure Container Registry
> * Przekazanie obrazu do rejestru

W następnym artykule, czyli ostatnim z tej serii, będzie miało miejsce wdrożenie kontenera z poziomu rejestru prywatnego do usługi Azure Container Instances.

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="create-azure-container-registry"></a>Tworzenie rejestru kontenerów platformy Azure

Przed utworzeniem rejestru kontenerów należy zapewnić *grupę zasobów*, w której zostanie wdrożony. Grupa zasobów to kolekcja logiczna przeznaczona do wdrażania wszystkich zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą polecenia [az group create][az-group-create]. W poniższym przykładzie grupa zasobów o nazwie *myResourceGroup* zostanie utworzona w regionie *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Po utworzeniu grupy zasobów utwórz rejestr kontenerów platformy Azure za pomocą polecenia [az acr create][az-acr-create]. Nazwa rejestru kontenerów musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych. Zamień wartość `<acrName>` na unikatową nazwę rejestru:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic --admin-enabled true
```

Oto przykład danych wyjściowych z nowego rejestru kontenerów platformy Azure o nazwie *mycontainerregistry082* (w tym miejscu pokazywany po obcięciu):

```console
$ az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
...
{
  "adminUserEnabled": true,
  "creationDate": "2018-03-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Dalsza część tego samouczka odnosi się do wartości `<acrName>` jako symbolu zastępczego dla nazwy rejestru kontenerów, która została wybrana w tym kroku.

## <a name="log-in-to-container-registry"></a>Logowanie do rejestru kontenerów

Należy zalogować się do wystąpienia usługi Azure Container Registry przed wypchnięciem do niego obrazów. Aby wykonać tę operację, użyj polecenia [az acr login][az-acr-login]. Należy wprowadzić unikatową nazwę wybraną dla rejestru kontenerów podczas jego tworzenia.

```azurecli
az acr login --name <acrName>
```

Po ukończeniu polecenie zwraca ciąg `Login Succeeded`:

```console
$ az acr login --name mycontainerregistry082
Login Succeeded
```

## <a name="tag-container-image"></a>Tagowanie obrazu kontenera

Aby wypchnąć obraz kontenera do prywatnego rejestru, takiego jak usługa Azure Container Registry, najpierw musisz otagować obraz pełną nazwą serwera logowania rejestru.

Najpierw pobierz pełną nazwę serwera logowania dla rejestru kontenerów platformy Azure. Uruchom następujące polecenie [az acr show][az-acr-show] i zastąp wartość `<acrName>` nazwą utworzonego rejestru:

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Przykładowo jeśli rejestr ma nazwę *mycontainerregistry082*:

```console
$ az acr show --name mycontainerregistry082 --query loginServer --output table
Result
------------------------
mycontainerregistry082.azurecr.io
```

Teraz wyświetl listę obrazów lokalnych przy użyciu polecenia [docker images][docker-images]:

```bash
docker images
```

Wraz z innymi obrazami na maszynie powinien zostać wyświetlony obraz *aci-tutorial-app*, który został utworzony w [poprzednim samouczku](container-instances-tutorial-prepare-app.md):

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 minutes ago    68.1 MB
```

Otaguj obraz *aci-tutorial-app* z wartością loginServer rejestru kontenerów. Ponadto dodaj tag `:v1` na końcu nazwy obrazu, aby wskazać numer wersji obrazu. Zastąp wartość `<acrLoginServer>` wynikiem polecenia [az acr show][az-acr-show], które zostało wykonane wcześniej.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Uruchom polecenie `docker images` ponownie, aby zweryfikować operację tagowania:

```console
$ docker images
REPOSITORY                                            TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app                                      latest    5c745774dfa9    39 minutes ago    68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app    v1        5c745774dfa9    7 minutes ago     68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Wypychanie obrazu do usługi Azure Container Registry

Teraz po otagowaniu obrazu *aci-tutorial-app* przy użyciu pełnej nazwy serwera logowania prywatnego rejestru możesz wypchnąć obraz do rejestru przy użyciu polecenia [docker push][docker-push]. Zastąp wartość `<acrLoginServer>` pełną nazwa serwera logowania uzyskaną we wcześniejszym kroku.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

Operacja `push` może trwać od kilku sekund do kilku minut w zależności od połączenia internetowego, a jej dane wyjściowe są podobne następujących:

```console
$ docker push mycontainerregistry082.azurecr.io/aci-tutorial-app:v1
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Wyświetlanie listy obrazów w usłudze Azure Container Registry

Aby sprawdzić, czy właśnie wypchnięty obraz rzeczywiście znalazł się w rejestrze kontenerów platformy Azure, wyświetl listę obrazów w rejestrze, korzystając z polecenia [az acr repository list][az-acr-repository-list]. Zastąp wartość `<acrName>` nazwą rejestru kontenerów.

```azurecli
az acr repository list --name <acrName> --output table
```

Na przykład:

```console
$ az acr repository list --name mycontainerregistry082 --output table
Result
----------------
aci-tutorial-app
```

Aby wyświetlić *tagi* dla określonego obrazu, użyj polecenia [az acr repository show-tags][az-acr-repository-show-tags].

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Powinny zostać wyświetlone dane wyjściowe podobne do następujących:

```console
$ az acr repository show-tags --name mycontainerregistry082 --repository aci-tutorial-app --output table
Result
--------
v1
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przygotowano rejestr kontenerów platformy Azure do używania z usługą Azure Container Instances oraz wypchnięto obraz kontenera do rejestru. Wykonano następujące czynności:

> [!div class="checklist"]
> * Wdrażanie wystąpienia usługi Azure Container Registry
> * Tagowanie obrazu kontenera na potrzeby usługi Azure Container Registry
> * Przekazywanie obrazu do usługi Azure Container Registry

Przejdź do kolejnego samouczka, aby uzyskać więcej informacji o sposobie wdrażania kontenera za pomocą usługi Azure Container Instances:

> [!div class="nextstepaction"]
> [Wdrażanie kontenera w usłudze Azure Container Instances](container-instances-tutorial-deploy-app.md)

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
