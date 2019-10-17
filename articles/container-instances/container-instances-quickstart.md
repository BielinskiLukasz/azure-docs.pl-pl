---
title: 'Szybki Start: wdrażanie kontenera Docker w Azure Container Instances — interfejs wiersza polecenia platformy Azure'
description: W tym przewodniku szybki start użyjesz interfejsu wiersza polecenia platformy Azure, aby szybko wdrożyć aplikację sieci Web, która działa w izolowanym wystąpieniu kontenera platformy Azure
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: danlep
ms.custom:
- seo-python-october2019
- seodec18
- mvc
ms.openlocfilehash: 65c067792499017a7e00fdbb6199e591981683f8
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429200"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-cli"></a>Szybki Start: Wdrażanie wystąpienia kontenera na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Używanie Azure Container Instances do uruchamiania kontenerów platformy Docker bez serwera na platformie Azure z prostotą i szybkością. Wdróż aplikację w wystąpieniu kontenera na żądanie, gdy nie potrzebujesz pełnej platformy aranżacji kontenerów, takiej jak usługa Azure Kubernetes.

W tym przewodniku szybki start użyjesz interfejsu wiersza polecenia platformy Azure, aby wdrożyć izolowany kontener platformy Docker i udostępnić swoją aplikację za pomocą w pełni kwalifikowanej nazwy domeny (FQDN). Kilka sekund po wykonaniu jednego polecenia wdrożenia można przejść do aplikacji działającej w kontenerze:

![Wyświetlanie aplikacji wdrożonej do Azure Container Instances w przeglądarce][aci-app-browser]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][azure-account].

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Podczas pracy z tym przewodnikiem Szybki start możesz użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Jeśli chcesz używać go lokalnie, zalecana jest wersja 2.0.55 lub nowsza. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-a-resource-group"></a>Utwórz grupę zasobów

Wystąpienia kontenerów platformy Azure, tak jak wszystkie zasoby platformy Azure, muszą zostać wdrożone w grupie zasobów. Grupy zasobów umożliwiają organizowanie powiązanych zasobów platformy Azure i zarządzanie nimi.

Najpierw utwórz grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* przy użyciu następującego polecenia [AZ Group Create][az-group-create] :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Tworzenie kontenera

Teraz, gdy masz już grupę zasobów, możesz uruchomić kontener na platformie Azure. Aby utworzyć wystąpienie kontenera za pomocą interfejsu wiersza polecenia platformy Azure, podaj nazwę grupy zasobów, nazwę wystąpienia kontenera i obraz kontenera Docker do polecenia [AZ Container Create][az-container-create] . W tym przewodniku szybki start użyjesz publicznego obrazu `mcr.microsoft.com/azuredocs/aci-helloworld`. Ten obraz zawiera małą aplikację internetową napisaną w języku Node.js, która udostępnia statyczną stronę HTML.

Możesz uwidocznić swoje kontenery w Internecie, określając co najmniej jeden port do otworzenia, etykietę nazwy DNS lub obie te informacje. W tym przewodniku Szybki start wdrożysz kontener z etykietą nazwy DNS, aby skonfigurować tę aplikację internetową jako publicznie dostępną.

Wykonaj polecenie podobne do poniższego, aby uruchomić wystąpienie kontenera. Ustaw wartość `--dns-name-label`, która jest unikatowa w regionie platformy Azure, w którym tworzysz wystąpienie. Jeśli zostanie wyświetlony komunikat o błędzie „Etykieta nazwy DNS nie jest dostępna”, spróbuj użyć innej etykiety nazwy DNS.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld --dns-name-label aci-demo --ports 80
```

W ciągu kilku sekund powinna pojawić się odpowiedź z interfejsu wiersza polecenia platformy Azure, wskazująca ukończenie wdrażania. Sprawdź swój stan za pomocą polecenia [AZ Container show][az-container-show] :

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

Po uruchomieniu polecenia zostanie wyświetlona w pełni kwalifikowana nazwa domeny (FQDN) kontenera i stan jego aprowizacji.

```console
$ az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

Jeśli @no__t kontenera **zakończyło się pomyślnie**, przejdź do jego nazwy FQDN w przeglądarce. Jeśli zostanie wyświetlona strona internetowa podobna do poniższej — gratulacje! Aplikacja działającą w kontenerze Docker została pomyślne wdrożona na platformie Azure.

![Wyświetlanie aplikacji wdrożonej do Azure Container Instances w przeglądarce][aci-app-browser]

Jeśli początkowo aplikacja nie jest widoczna, być może trzeba poczekać kilka sekund na zakończenie propagowania systemu DNS, a następnie spróbować odświeżyć stronę przeglądarki.

## <a name="pull-the-container-logs"></a>Ściąganie dzienników kontenera

Jeśli trzeba rozwiązać problem z kontenerem lub z uruchomioną na nim aplikacją (lub po prostu wyświetlić dane wyjściowe), zacznij od wyświetlenia dzienników wystąpienia kontenera.

Pobierz dzienniki wystąpienia kontenera za pomocą polecenia [AZ Container Logs][az-container-logs] :

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

W dziennikach kontenera powinny być widoczne żądania HTTP GET utworzone podczas przeglądania aplikacji w przeglądarce.

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
listening on port 80
::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="attach-output-streams"></a>Dołączanie strumieni wyjściowych

Oprócz wyświetlenia dzienników można dołączyć lokalne strumienie Standardowe wyjście i Błąd standardowy do ich odpowiedników należących do kontenera.

Najpierw uruchom polecenie [AZ Container Attach][az-container-attach] , aby dołączyć konsolę lokalną do strumieni wyjściowych kontenera:

```azurecli-interactive
az container attach --resource-group myResourceGroup --name mycontainer
```

Po jej dołączeniu odśwież kilka razy przeglądarkę, aby wygenerować trochę dodatkowych danych wyjściowych. Po zakończeniu odłącz swoją konsolę kombinacją klawiszy `Control+C`. Powinny zostać wyświetlone dane wyjściowe podobne do następujących:

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 17:27:20+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:24+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Started container

Start streaming logs:
listening on port 80

::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:47:01 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.56 - - [21/Mar/2019:17:47:12 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z kontenerem usuń go przy użyciu polecenia [AZ Container Delete][az-container-delete] :

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

Aby się upewnić, że kontener został usunięty, uruchom polecenie [az container list](/cli/azure/container#az-container-list):

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

W danych wyjściowych polecenia nie powinien pojawić się kontener **mycontainer**. Jeśli nie ma innych kontenerów w grupie zasobów, nie powinny zostać wyświetlone żadne dane wyjściowe.

Jeśli skończysz z grupą zasobów *i wszystkie zawarte w niej* zasoby, usuń je za pomocą polecenia [AZ Group Delete][az-group-delete] :

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono wystąpienie kontenera platformy Azure przy użyciu publicznego obrazu firmy Microsoft. Jeśli chcesz skompilować obraz kontenera i wdrożyć go za pomocą prywatnej usługi Azure Container Registry, przejdź do samouczka dotyczącego usługi Azure Container Instances.

> [!div class="nextstepaction"]
> [Samouczek dotyczący usługi Azure Container Instances](./container-instances-tutorial-prepare-app.md)

Aby wypróbować opcje uruchamiania kontenerów w systemie aranżacji na platformie Azure, zobacz Przewodniki Szybki Start dotyczący [usługi Azure Kubernetes Service (AKS)][container-service] .

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/view-an-application-running-in-an-azure-container-instance.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: https://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-container-list]: /cli/azure/container#az-container-list
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
