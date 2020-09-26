---
title: 'Samouczek: Włączanie dodatku transferu danych przychodzących dla istniejącego klastra AKS z istniejącym Application Gateway platformy Azure'
description: Skorzystaj z tego samouczka, aby włączyć dodatek usługi transferu danych przychodzących dla istniejącego klastra AKS z istniejącym Application Gateway
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 09/24/2020
ms.author: caya
ms.openlocfilehash: d0ce58c5bb6de4712117959f10b48ae3449f0b97
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91285660"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway-through-azure-cli-preview"></a>Samouczek: Włączanie dodatku Application Gateway transferu danych przychodzących dla istniejącego klastra AKS z istniejącym Application Gateway za pomocą interfejsu wiersza polecenia platformy Azure (wersja zapoznawcza)

Możesz użyć interfejsu wiersza polecenia platformy Azure, aby włączyć dodatek [Application Gateway (AGIC)](ingress-controller-overview.md) , który jest obecnie w wersji zapoznawczej dla klastra [usługi Azure KUBERNETES Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/) . W tym samouczku dowiesz się, jak korzystać z dodatku AGIC w celu udostępnienia aplikacji Kubernetes w istniejącym klastrze AKS za pomocą istniejącego Application Gateway wdrożonego w oddzielnych sieciach wirtualnych. Zacznij od utworzenia klastra AKS w jednej sieci wirtualnej i Application Gateway w oddzielnej sieci wirtualnej, aby symulować istniejące zasoby. Następnie włącz dodatek AGIC, równorzędne dwie sieci wirtualne, a następnie wdróż przykładową aplikację, która zostanie udostępniona za pośrednictwem Application Gateway przy użyciu dodatku AGIC. Jeśli włączysz dodatek AGIC dla istniejącego Application Gateway i istniejącego klastra AKS w tej samej sieci wirtualnej, możesz pominąć krok komunikacji równorzędnej poniżej. Dodatek zapewnia znacznie szybszy sposób wdrażania AGIC dla klastra AKS niż [poprzednio za pomocą usługi Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) , a także oferuje w pełni zarządzane środowisko.  

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie grupy zasobów 
> * Tworzenie nowego klastra AKS 
> * Utwórz nowy Application Gateway 
> * Włącz dodatek AGIC w istniejącym klastrze AKS przy użyciu istniejącego Application Gateway 
> * Komunikacja równorzędna Application Gateway sieci wirtualnej z siecią wirtualną klastra AKS
> * Wdrażanie przykładowej aplikacji przy użyciu usługi AGIC na potrzeby ruchu przychodzącego w klastrze AKS
> * Sprawdź, czy aplikacja jest dostępna za pomocą Application Gateway

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Zarejestruj flagę funkcji *AKS-IngressApplicationGatewayAddon* przy użyciu polecenia [AZ Feature Register](https://docs.microsoft.com/cli/azure/feature#az-feature-register) , jak pokazano w poniższym przykładzie. Wystarczy wykonać tę czynność tylko raz dla każdej subskrypcji, gdy dodatek jest nadal w wersji zapoznawczej:
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace microsoft.containerservice
```

Wyświetlenie zarejestrowanego stanu może potrwać kilka minut. Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list](https://docs.microsoft.com/cli/azure/feature#az-feature-register) :
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'microsoft.containerservice/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestrację dostawcy zasobów Microsoft. ContainerService za pomocą polecenia [AZ Provider Register](https://docs.microsoft.com/cli/azure/provider#az-provider-register) :
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Pamiętaj, aby zainstalować/zaktualizować rozszerzenie AKS-Preview dla tego samouczka. Użyj następujących poleceń interfejsu wiersza polecenia platformy Azure
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *canadacentral* (region). 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>Wdróż nowy klaster AKS

Teraz zostanie wdrożony nowy klaster AKS, aby symulować istnienie istniejącego klastra AKS, dla którego chcesz włączyć dodatek AGIC.  

W poniższym przykładzie zostanie wdrożony nowy klaster *AKS o nazwie* przy użyciu [usługi Azure CNI](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking) i [zarządzanych tożsamości](https://docs.microsoft.com/azure/aks/use-managed-identity) w utworzonej grupie *zasobów.*    

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

Aby skonfigurować dodatkowe parametry dla `az aks create` polecenia, zapoznaj się z odwołaniami [tutaj](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create). 

## <a name="deploy-a-new-application-gateway"></a>Wdróż nowy Application Gateway 

Teraz zostanie wdrożony nowy Application Gateway, aby symulować posiadanie istniejącej *Application Gateway, która*ma być używana do równoważenia obciążenia ruchu sieciowego do klastra AKS. Nazwa Application Gateway będzie *myApplicationGateway*, ale musisz najpierw utworzyć zasób publicznego adresu IP o nazwie *myPublicIp*i nową sieć wirtualną o nazwie *myVnet* z przestrzenią adresową 11.0.0.0/8 i podsieć z przestrzenią adresową 11.1.0.0/16 o nazwie Moja *podsieć*, a następnie wdrożyć Application Gateway w *podsieci* przy użyciu *myPublicIp*. 

W przypadku korzystania z klastra AKS i Application Gateway w oddzielnych sieciach wirtualnych, przestrzenie adresowe dwóch sieci wirtualnych nie mogą nakładać się na siebie. Domyślną przestrzenią adresową, w której wdrażany jest klaster AKS, jest 10.0.0.0/8, więc ustawimy prefiks adresu sieci wirtualnej Application Gateway na 11.0.0.0/8. 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> Dodatek Application Gateway Management Controller (AGIC) obsługuje **tylko** wersje SKU Application Gateway v2 (standard i WAF), a **nie** jednostki SKU Application Gateway v1. 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-with-existing-application-gateway"></a>Włącz dodatek AGIC w istniejącym klastrze AKS z istniejącym Application Gateway 

Teraz włączmy dodatek AGIC w utworzonym klastrze AKS, *Webclustering*i określisz dodatek AGIC, aby korzystał z istniejących Application Gateway utworzonych przez Ciebie, *myApplicationGateway*. Upewnij się, że dodano/Zaktualizowano rozszerzenie AKS-Preview na początku tego samouczka. 

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="peer-the-two-virtual-networks-together"></a>Łączenie równorzędne dwóch sieci wirtualnych

Ze względu na to, że klaster AKS został wdrożony we własnej sieci wirtualnej, a Application Gateway w innej sieci wirtualnej, należy połączyć równorzędnie dwie sieci wirtualne w celu przepływu ruchu z Application Gateway do zasobników w klastrze. Komunikacja równorzędna między dwiema sieciami wirtualnymi wymaga ponownego uruchomienia polecenia platformy Azure w celu zapewnienia dwukierunkowego połączenia. Pierwsze polecenie spowoduje utworzenie połączenia komunikacji równorzędnej z Application Gateway sieci wirtualnej z siecią wirtualną AKS. Drugie polecenie spowoduje utworzenie połączenia komunikacji równorzędnej w innym kierunku. 

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```
## <a name="deploy-a-sample-application-using-agic"></a>Wdrażanie przykładowej aplikacji przy użyciu AGIC 

Teraz wdrożono przykładową aplikację do utworzonego klastra AKS, który będzie korzystał z dodatku AGIC dla ruchu przychodzącego i połączyć Application Gateway z klastrem AKS. Najpierw uzyskasz poświadczenia do wdrożonego klastra AKS, uruchamiając `az aks get-credentials` polecenie. 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Po utworzeniu poświadczeń do utworzonego klastra uruchom następujące polecenie, aby skonfigurować przykładową aplikację, która używa AGIC na potrzeby ruchu przychodzącego do klastra. Application Gateway program AGIC zaktualizuje wcześniej wdrożoną aplikację przy użyciu odpowiednich reguł routingu do nowej przykładowej aplikacji.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Sprawdź, czy aplikacja jest dostępna

Teraz, gdy Application Gateway jest skonfigurowany do obsługi ruchu do klastra AKS, sprawdźmy, czy aplikacja jest dostępna. Najpierw uzyskasz adres IP ruchu przychodzącego. 

```azurecli-interactive
kubectl get ingress
```

Sprawdź, czy utworzona Przykładowa aplikacja jest uruchomiona, albo odszukaj adres IP Application Gateway, z którego korzystasz z powyższego polecenia, lub sprawdź `curl` . Pobieranie aktualizacji może potrwać Application Gateway minutę, więc jeśli Application Gateway nadal jest w stanie "Aktualizacja" w portalu, niech zakończy się przed próbą uzyskania dostępu do adresu IP. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, brama aplikacji i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć.

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat wyłączania dodatku AGIC](./ingress-controller-disable-addon.md)