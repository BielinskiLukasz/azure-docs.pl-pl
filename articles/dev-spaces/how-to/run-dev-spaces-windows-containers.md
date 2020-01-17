---
title: Interakcja z kontenerami systemu Windows
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Dowiedz się, jak uruchamiać Azure Dev Spaces w istniejącym klastrze przy użyciu kontenerów systemu Windows
keywords: Azure Dev Spaces, Spaces dev, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, kontenery systemu Windows
ms.openlocfilehash: 886f71dcaaca6a636b385ef6b101f0a893ff7035
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157002"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Korzystanie z kontenerów systemu Windows przy użyciu Azure Dev Spaces

Azure Dev Spaces można włączyć zarówno dla nowych, jak i istniejących przestrzeni nazw Kubernetes. Azure Dev Spaces uruchomi i Instrumentacja usług, które działają w kontenerach systemu Linux. Te usługi mogą również współdziałać z aplikacjami uruchamianymi w kontenerach systemu Windows w tej samej przestrzeni nazw. W tym artykule pokazano, jak za pomocą funkcji miejsca do magazynowania uruchamiać usługi w przestrzeni nazw z istniejącymi kontenerami systemu Windows. W tej chwili nie można debugować ani dołączać do kontenerów systemu Windows przy użyciu Azure Dev Spaces.

## <a name="set-up-your-cluster"></a>Konfigurowanie klastra

W tym artykule przyjęto założenie, że istnieje już klaster z pulami węzłów systemu Linux i Windows. Jeśli musisz utworzyć klaster z pulami węzłów systemu Linux i Windows, możesz postępować zgodnie z instrukcjami znajdującymi się [tutaj][windows-container-cli].

Nawiąż połączenie z klastrem za pomocą [polecenia kubectl][kubectl], Kubernetes klienta wiersza polecenia. Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][az-aks-get-credentials]. To polecenie powoduje pobranie poświadczeń i zastosowanie ich w konfiguracji interfejsu wiersza polecenia Kubernetes.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Aby sprawdzić połączenie z klastrem, użyj polecenia [kubectl get][kubectl-get], aby powrócić do listy węzłów klastra.

```azurecli-interactive
kubectl get nodes
```

Następujące przykładowe dane wyjściowe pokazują klaster z węzłem systemu Windows i Linux. Przed kontynuowaniem upewnij się, że stan jest *gotowy* dla każdego węzła.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Zastosuj [przybarwienie][using-taints] do węzłów systemu Windows. W przypadku węzłów systemu Windows nie można używać funkcji tworzenia obszarów programistycznych, które mogą być uruchamiane w węzłach systemu Windows. Poniższe polecenie przykładowe polecenie stosuje przebarwienie do węzła systemu Windows *aksnpwin987654* z poprzedniego przykładu.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> Po zastosowaniu zmiany czasu do węzła należy skonfigurować dopasowanie tolerowania w szablonie wdrożenia usługi, aby uruchomić usługę w tym węźle. Aplikacja Przykładowa jest już skonfigurowana z [dopasowaniem][sample-application-toleration-example] do celu, który został skonfigurowany w poprzednim poleceniu.

## <a name="run-your-windows-service"></a>Uruchamianie usługi systemu Windows

Uruchom usługę systemu Windows w klastrze AKS i sprawdź, czy jest w stanie *uruchomionym* . W tym artykule jest używana [Przykładowa aplikacja][sample-application] do zademonstrowania usługi systemu Windows i Linux działającej w klastrze.

Sklonuj przykładową aplikację z witryny GitHub i przejdź do katalogu `dev-spaces/samples/existingWindowsBackend/mywebapi-windows`:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

Aplikacja Przykładowa używa [Helm][helm-installed] do uruchamiania usługi systemu Windows w klastrze. Przejdź do katalogu `charts` i Użyj usługi Helm, aby uruchomić usługę systemu Windows:

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

Powyższe polecenie używa Helm do uruchamiania usługi systemu Windows w przestrzeni nazw *dev* . Jeśli nie masz przestrzeni nazw o nazwie *dev*, zostanie ona utworzona.

Użyj `kubectl get pods` polecenia, aby sprawdzić, czy usługa systemu Windows jest uruchomiona w klastrze. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Włącz Azure Dev Spaces

Włącz funkcję Spaces dev w tej samej przestrzeni nazw, która była używana do uruchamiania usługi systemu Windows. Następujące polecenie włącza miejsca deweloperskie w przestrzeni nazw *dev* :

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Aktualizowanie usługi systemu Windows pod kątem funkcji dev Spaces

Po włączeniu funkcji miejsca deweloperskie w istniejącej przestrzeni nazw z kontenerami, które są już uruchomione, funkcja miejsca deweloperskie będzie podejmować próby i instrumentować wszystkie nowe kontenery, które działają w tej przestrzeni nazw. Miejsca deweloperskie również będą próbować i instrumentować wszelkie nowe kontenery utworzone dla usługi już uruchomionej w przestrzeni nazw. Aby zapobiec występowaniu przez funkcję miejsca do magazynowania kontenera działającego w przestrzeni nazw, Dodaj nagłówek *no-proxy* do `deployment.yaml`.

Dodaj `azds.io/no-proxy: "true"` do pliku `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

Użyj `helm list`, aby wyświetlić listę wdrożenia usługi systemu Windows:

```cmd
$ helm list --namespace dev
NAME              REVISION  UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
windows-service 1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

W powyższym przykładzie nazwa wdrożenia to *Windows-Service*. Zaktualizuj usługę systemu Windows przy użyciu nowej konfiguracji `helm upgrade`:

```cmd
helm upgrade windows-service . --namespace dev
```

Ponieważ Zaktualizowaliśmy `deployment.yaml`, spacje deweloperów nie będą próbować i Instrumentacji usługi.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Uruchamianie aplikacji systemu Linux przy użyciu Azure Dev Spaces

Przejdź do katalogu `webfrontend` i Użyj poleceń `azds prep` i `azds up`, aby uruchomić aplikację systemu Linux w klastrze.

```console
cd ../../webfrontend-linux/
azds prep --public
azds up
```

Polecenie `azds prep --public` generuje wykres Helm i wieloetapowe dockerfile dla aplikacji.

> [!TIP]
> [Wykres pliku dockerfile i Helm](../how-dev-spaces-works.md#prepare-your-code) dla projektu jest używany przez Azure dev Spaces do kompilowania i uruchamiania kodu, ale można modyfikować te pliki, jeśli chcesz zmienić sposób kompilowania i wykonywania projektu.

`azds up` polecenie uruchamia usługę w przestrzeni nazw.

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

Możesz zobaczyć, że usługa jest uruchomiona, otwierając publiczny adres URL, który jest wyświetlany w danych wyjściowych polecenia azds. W tym przykładzie publiczny adres URL jest `http://dev.webfrontend.abcdef0123.eus.azds.io/`. Przejdź do usługi w przeglądarce i kliknij pozycję *informacje* w górnej części strony. Sprawdź, czy zostanie wyświetlony komunikat z usługi *mywebapi* zawierającej wersję systemu Windows, z której korzysta kontener.

![Przykładowa aplikacja pokazująca wersję systemu Windows z mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak Azure Dev Spaces ułatwiają tworzenie bardziej złożonych aplikacji w wielu kontenerach i jak można uprościć programowanie do współpracy, pracując z różnymi wersjami lub gałęziami kodu w różnych miejscach.

> [!div class="nextstepaction"]
> [Programowanie zespołowe w Azure Dev Spaces][team-development-qs]

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[team-development-qs]: ../quickstart-team-development.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[team-development]: ../team-development-netcore.md
[using-taints]: ../../aks/use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[windows-container-cli]: ../../aks/windows-container-cli.md
