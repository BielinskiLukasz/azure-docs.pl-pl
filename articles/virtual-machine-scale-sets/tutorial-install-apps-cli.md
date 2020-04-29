---
title: Samouczek — Instalowanie aplikacji w zestawie skalowania przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure instalować aplikacje w zestawach skalowania maszyn wirtualnych, korzystając z rozszerzenia niestandardowego skryptu
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: jushiman
ms.custom: mvc
ms.openlocfilehash: 6faaa3bc5149d4245d026e6f91e358993e15421d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81011224"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-the-azure-cli"></a>Samouczek: instalowanie aplikacji w zestawach skalowania maszyn wirtualnych za pomocą interfejsu wiersza polecenia platformy Azure
Aby uruchamiać aplikacje na wystąpieniach maszyn wirtualnych w zestawie skalowania, musisz najpierw zainstalować składniki aplikacji i wymagane pliki. W poprzednim samouczku omówiono tworzenie niestandardowego obrazu maszyny wirtualnej i wdrażanie własnych wystąpień maszyn wirtualnych. Niestandardowy obraz zawierał ręczne instalacje i konfiguracje aplikacji. Można również zautomatyzować instalację aplikacji w zestawie skalowania po wdrożeniu poszczególnych wystąpień maszyn wirtualnych lub zaktualizować już uruchomioną aplikację. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Automatyczne instalowanie aplikacji w zestawie skalowania
> * Używanie rozszerzenia niestandardowego skryptu platformy Azure
> * Aktualizowanie uruchomionej aplikacji w zestawie skalowania

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.29 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 


## <a name="what-is-the-azure-custom-script-extension"></a>Co to jest rozszerzenie niestandardowego skryptu platformy Azure?
Rozszerzenie niestandardowego skryptu pobiera i wykonuje skrypty na maszynach wirtualnych platformy Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalowania oprogramowania lub każdego innego zadania związanego z konfiguracją lub zarządzaniem. Skrypty można pobrać z usługi Azure Storage lub GitHub bądź można je dostarczyć do witryny Azure Portal w czasie wykonywania rozszerzenia.

Rozszerzenie niestandardowego skryptu można zintegrować z szablonami usługi Azure Resource Manager, a także uruchamiać je przy użyciu interfejsu wiersza polecenia platformy Azure, programu Azure PowerShell, witryny Azure Portal lub interfejsu API REST. Aby uzyskać więcej informacji, zobacz [Omówienie niestandardowego rozszerzenia skryptu](../virtual-machines/linux/extensions-customscript.md).

Korzystanie z rozszerzenia niestandardowego skryptu za pomocą interfejsu wiersza polecenia platformy Azure wymaga utworzenia pliku JSON, który definiuje uzyskiwane pliki i wykonywane polecenia. Definicji JSON można używać we wszystkich wdrożeniach zestawu skalowania, co pozwala stosować spójne instalacje aplikacji.


## <a name="create-custom-script-extension-definition"></a>Tworzenie definicji rozszerzenia niestandardowego skryptu
Aby zobaczyć, jak działa rozszerzenie niestandardowego skryptu, utworzymy zestaw skalowania, który instaluje serwer internetowy NGINX i zwraca nazwę hosta wystąpienia maszyny wirtualnej w zestawie skalowania. Poniższa definicja rozszerzenia niestandardowego skryptu pobiera przykładowy skrypt z witryny GitHub, instaluje wymagane pakiety, a następnie zapisuje nazwę hosta wystąpienia maszyny wirtualnej na prostej stronie HTML.

W bieżącej powłoce utwórz plik o nazwie *customConfig.json* i wklej poniższą konfigurację. Na przykład utwórz plik w usłudze Cloud Shell, a nie na maszynie lokalnej. Możesz użyć dowolnego edytora. W usłudze Cloud Shell wprowadź `sensible-editor customConfig.json`, aby utworzyć plik i wyświetlić listę dostępnych edytorów.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```


## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania
Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Teraz utwórz zestaw skalowania maszyn wirtualnych przy użyciu polecenia [az vmss create](/cli/azure/vmss). Poniższy przykład tworzy zestaw skalowania o nazwie *myScaleSet*i GENERUJE klucze SSH, jeśli nie istnieją:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.


## <a name="apply-the-custom-script-extension"></a>Stosowanie rozszerzenia niestandardowego skryptu
Za pomocą polecenia [az vmss extension set](/cli/azure/vmss/extension) zastosuj konfigurację rozszerzenia niestandardowego skryptu do wystąpień maszyn wirtualnych w zestawie skalowania. W następującym przykładzie konfiguracja *customConfig.json* jest stosowana do wystąpień maszyn wirtualnych zestawu *myScaleSet* w grupie zasobów *myResourceGroup*:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings @customConfig.json
```

Każde wystąpienie maszyny wirtualnej w zestawie skalowania pobiera i uruchamia skrypt z usługi GitHub. Bardziej złożony przykład może obejmować instalację wielu składników aplikacji i plików. W przypadku skalowania w górę nowe wystąpienia maszyn wirtualnych automatycznie stosują tę samą definicję rozszerzenia niestandardowego skryptu i instalują wymaganą aplikację.


## <a name="test-your-scale-set"></a>Testowanie zestawu skalowania
Aby zezwolić na ruch internetowy na serwerze, utwórz regułę modułu równoważenia obciążenia za pomocą polecenia [az network lb rule create](/cli/azure/network/lb/rule). W poniższym przykładzie pokazano tworzenie reguły o nazwie *myLoadBalancerRuleWeb*:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Aby zobaczyć, jak działa serwer internetowy, uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [az network public-ip show](/cli/azure/network/public-ip). W poniższym przykładzie pokazano uzyskiwanie adresu IP dla modułu *myScaleSetLBPublicIP* utworzonego w ramach zestawu skalowania:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Wprowadź publiczny adres IP modułu równoważenia obciążenia w przeglądarce internetowej. Moduł równoważenia obciążenia kieruje ruch do jednego z wystąpień maszyn wirtualnych, jak pokazano w poniższym przykładzie:

![Podstawowa strona internetowa na serwerze Nginx](media/tutorial-install-apps-cli/running-nginx.png)

Nie zamykaj przeglądarki internetowej, aby w następnym kroku można było zobaczyć zaktualizowaną wersję.


## <a name="update-app-deployment"></a>Aktualizowanie wdrożenia aplikacji
W całym cyklu życia zestawu skalowania konieczne może być wdrożenie zaktualizowanej wersji aplikacji. Rozszerzenie niestandardowego skryptu umożliwia odwołanie się do zaktualizowanego skryptu wdrażania i ponowne zastosowanie rozszerzenia do zestawu skalowania. Podczas tworzenia zestawu skalowania w poprzednim kroku parametr `--upgrade-policy-mode` ustawiono na wartość *automatic*. To ustawienie pozwala wystąpieniom maszyn wirtualnych w zestawie skalowania automatycznie aktualizować aplikację i stosować jej najnowszą wersję.

W bieżącej powłoce utwórz plik o nazwie *customConfigv2.json* i wklej poniższą konfigurację. Ta definicja umożliwia uruchomienie zaktualizowanej wersji *v2* skryptu instalacji aplikacji:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"],
  "commandToExecute": "./automate_nginx_v2.sh"
}
```

Za pomocą polecenia [az vmss extension set](/cli/azure/vmss/extension) ponownie zastosuj konfigurację rozszerzenia niestandardowego skryptu do wystąpień maszyn wirtualnych w zestawie skalowania. Plik *customConfigv2.json* umożliwia zastosowanie zaktualizowanej wersji aplikacji:

```azurecli-interactive
az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroup \
    --vmss-name myScaleSet \
    --settings @customConfigv2.json
```

Wszystkie wystąpienia maszyn wirtualnych w zestawie skalowania są automatycznie aktualizowane do najnowszej wersji przykładowej strony internetowej. Aby wyświetlić zaktualizowaną wersję, odśwież witrynę internetową w przeglądarce:

![Zaktualizowana strona internetowa na serwerze Nginx](media/tutorial-install-apps-cli/running-nginx-updated.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Aby usunąć zestaw skalowania i dodatkowe zasoby, Usuń grupę zasobów i wszystkie jej zasoby za pomocą polecenie [AZ Group Delete](/cli/azure/group). Parametr `--no-wait` zwraca kontrolę do wiersza polecenia bez oczekiwania na zakończenie operacji. Parametr `--yes` potwierdza, że chcesz usunąć zasoby bez wyświetlania dodatkowego monitu.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono automatyczne instalowanie i aktualizowanie aplikacji w zestawie skalowania za pomocą interfejsu wiersza polecenia platformy Azure:

> [!div class="checklist"]
> * Automatyczne instalowanie aplikacji w zestawie skalowania
> * Używanie rozszerzenia niestandardowego skryptu platformy Azure
> * Aktualizowanie uruchomionej aplikacji w zestawie skalowania

Przejdź do następnego samouczka, aby dowiedzieć się, jak automatycznie skalować zestaw skalowania.

> [!div class="nextstepaction"]
> [Automatically scale your scale sets (Automatyczne skalowanie zestawów skalowania)](tutorial-autoscale-cli.md)
