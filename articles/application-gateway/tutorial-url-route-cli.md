---
title: Samouczek — kierowanie ruchu internetowego na podstawie adresu URL — interfejs wiersza polecenia platformy Azure
description: W tym samouczku dowiesz się, jak kierować ruch internetowy do określonych skalowalnych pul serwerów na podstawie adresu URL, korzystając z interfejsu wiersza polecenia platformy Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 10/25/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 4f0c93c41a468b62baf1ec50d030f235d36a8dd2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "58006478"
---
# <a name="tutorial-route-web-traffic-based-on-the-url-using-the-azure-cli"></a>Samouczek: Kierowanie ruchu sieciowego na podstawie adresu URL przy użyciu wiersza polecenia platformy Azure

Jako administrator IT zarządzający ruchem internetowym chcesz pomóc swoim klientom lub użytkownikom w jak najszybszym uzyskaniu potrzebnych im informacji. Jednym ze sposobów zoptymalizowania ich środowiska jest skierowanie różnych rodzajów ruchu internetowego do różnych zasobów serwera. W tym samouczku dowiesz się, jak zainstalować i skonfigurować routing usługi Application Gateway dla różnych typów ruchu z aplikacji za pomocą interfejsu wiersza polecenia platformy Azure. Następnie, na podstawie adresu URL, routing kieruje ruch do różnych pul serwerów.

![Przykład routingu adresów URL](./media/tutorial-url-route-cli/scenario.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie grupy zasobów dla potrzebnych zasobów sieciowych
> * Tworzenie zasobów sieciowych
> * Tworzenie bramy aplikacji na potrzeby ruchu pochodzącego z aplikacji
> * Określanie pul serwerów i reguł rozsyłania dla różnych typów ruchu
> * Tworzenie zestawów skalowania dla poszczególnych pul umożliwiających ich automatycznie skalowanie
> * Uruchamianie testu sprawdzającego, czy różne typy ruchu trafiają do poprawnych pul

Jeśli chcesz, możesz wykonać ten samouczek przy użyciu [programu Azure PowerShell](tutorial-url-route-powershell.md) lub [witryny Azure Portal](create-url-route-portal.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Za pomocą polecenia `az group create` utwórz grupę zasobów.

W poniższym przykładzie pokazano sposób tworzenia grupy zasobów o nazwie *myResourceGroupAG* w lokalizacji *eastus*.

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

Utwórz sieć wirtualną o nazwie *myVNet* i podsieć o nazwie *myAGSubnet* przy użyciu polecenia `az network vnet create`. Następnie dodaj podsieć o nazwie *myBackendSubnet* wymaganą przez serwery zaplecza przy użyciu polecenia `az network vnet subnet create`. Za pomocą polecenia `az network public-ip create` utwórz publiczny adres IP o nazwie *myAGPublicIPAddress*.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-the-app-gateway-with-a-url-map"></a>Tworzenie bramy aplikacji z mapą adresów URL

Użyj polecenia `az network application-gateway create`, aby utworzyć bramę aplikacji o nazwie *myAppGateway*. Podczas tworzenia bramy aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure należy podać informacje o konfiguracji, takie jak pojemność, jednostka SKU i ustawienia protokołu HTTP. Brama aplikacji jest przypisywana do wcześniej utworzonej podsieci *myAGSubnet* i adresu *myAGPublicIPAddress*.

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 Tworzenie bramy aplikacji może potrwać kilka minut. Po utworzeniu bramy aplikacji możesz zauważyć następujące nowe funkcje:


|Cecha  |Opis  |
|---------|---------|
|appGatewayBackendPool     |Brama aplikacji musi mieć co najmniej jedną pulę adresów zaplecza.|
|appGatewayBackendHttpSettings     |Określa, że port 80 i protokół HTTP są używane do komunikacji.|
|appGatewayHttpListener     |Domyślny odbiornik skojarzony z pulą appGatewayBackendPool.|
|appGatewayFrontendIP     |Przypisuje adres myAGPublicIPAddress do odbiornika appGatewayHttpListener.|
|rule1     |Domyślna reguła rozsyłania skojarzona z odbiornikiem appGatewayHttpListener.|

### <a name="add-image-and-video-backend-pools-and-a-port"></a>Dodawanie pul zaplecza i portu na potrzeby obsługi obrazów i wideo

Dodaj pule zaplecza o nazwach *imagesBackendPool* i *videoBackendPool* do bramy aplikacji, używając polecenia `az network application-gateway address-pool create`. Port frontonu jest dodawany do pul za pomocą polecenia `az network application-gateway frontend-port create`.

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name imagesBackendPool

az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name videoBackendPool

az network application-gateway frontend-port create \
  --port 8080 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name port8080
```

### <a name="add-a-backend-listener"></a>Dodawanie odbiornika zaplecza

Dodaj odbiornik zaplecza o nazwie *backendListener*, wymagany do kierowania ruchu, używając polecenia `az network application-gateway http-listener create`.


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-a-url-path-map"></a>Dodawanie mapy ścieżek URL

Mapy ścieżek URL zapewniają kierowanie określonych adresów URL do określonych pul zaplecza. Utwórz mapy ścieżek URL o nazwach *imagePathRule* i *videoPathRule* za pomocą poleceń `az network application-gateway url-path-map create` i `az network application-gateway url-path-map rule create`.

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name myPathMap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --address-pool imagesBackendPool \
  --default-address-pool appGatewayBackendPool \
  --default-http-settings appGatewayBackendHttpSettings \
  --http-settings appGatewayBackendHttpSettings \
  --rule-name imagePathRule

az network application-gateway url-path-map rule create \
  --gateway-name myAppGateway \
  --name videoPathRule \
  --resource-group myResourceGroupAG \
  --path-map-name myPathMap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-a-routing-rule"></a>Dodawanie reguły rozsyłania

Reguła routingu kojarzy mapę adresów URL z utworzonym odbiornikiem. Dodaj regułę o nazwie *rule2* przy użyciu polecenia `az network application-gateway rule create`.

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map myPathMap \
  --address-pool appGatewayBackendPool
```

## <a name="create-vm-scale-sets"></a>Tworzenie zestawów skalowania maszyn wirtualnych

W tym samouczku utworzysz trzy zestawy skalowania maszyn wirtualnych do obsługi trzech utworzonych pul zaplecza. Utworzysz zestawy skalowania o nazwach *myvmss1*, *myvmss2* i *myvmss3*. Każdy zestaw skalowania zawiera dwa wystąpienia maszyny wirtualnej, na których instaluje się serwer NGINX.

```azurecli-interactive
for i in `seq 1 3`; do

  if [ $i -eq 1 ]
  then
    poolName="appGatewayBackendPool" 
  fi

  if [ $i -eq 2 ]
  then
    poolName="imagesBackendPool"
  fi

  if [ $i -eq 3 ]
  then
    poolName="videoBackendPool"
  fi

  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>Instalowanie serwera NGINX

```azurecli-interactive
for i in `seq 1 3`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Aby uzyskać publiczny adres IP bramy aplikacji, użyj polecenia az network public-ip show. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Takie jak `http://40.121.222.19`, `http://40.121.222.19:8080/images/test.htm`, lub `http://40.121.222.19:8080/video/test.htm`.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testowanie podstawowego adresu URL w bramie aplikacji](./media/tutorial-url-route-cli/application-gateway-nginx.png)

Zmień adres URL na http://&lt;adres-ip&gt;:8080/images/test.html, zamieniając ciąg &lt;adres-ip&gt; na odpowiedni adres IP. Wynik powinien przypominać następujący przykład:

![Testowanie adresu URL obrazów w bramie aplikacji](./media/tutorial-url-route-cli/application-gateway-nginx-images.png)

Zmień adres URL na http://&lt;adres-ip&gt;:8080/video/test.html, zamieniając ciąg &lt;adres-ip&gt; na odpowiedni adres IP. Wynik powinien przypominać następujący przykład.

![Testowanie adresu URL wideo w bramie aplikacji](./media/tutorial-url-route-cli/application-gateway-nginx-video.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, brama aplikacji i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie bramy aplikacji z przekierowywaniem na podstawie ścieżki URL](./tutorial-url-redirect-cli.md)
