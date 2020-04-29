---
title: Samouczek — Instalowanie aplikacji w zestawie skalowania za pomocą szablonów platformy Azure
description: Dowiedz się, jak za pomocą szablonów usługi Azure Resource Manager instalować aplikacje w zestawach skalowania maszyn wirtualnych, korzystając z rozszerzenia niestandardowego skryptu
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: jushiman
ms.custom: mvc
ms.openlocfilehash: aa5ce8876675b4332b92ee09295409540a5e6e66
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81011232"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-an-azure-template"></a>Samouczek: instalowanie aplikacji w zestawach skalowania maszyn wirtualnych za pomocą szablonu platformy Azure
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

Aby zobaczyć, jak działa rozszerzenie niestandardowego skryptu, utworzymy zestaw skalowania, który instaluje serwer internetowy NGINX i zwraca nazwę hosta wystąpienia maszyny wirtualnej w zestawie skalowania. Poniższa definicja rozszerzenia niestandardowego skryptu pobiera przykładowy skrypt z witryny GitHub, instaluje wymagane pakiety, a następnie zapisuje nazwę hosta wystąpienia maszyny wirtualnej na prostej stronie HTML.


## <a name="create-custom-script-extension-definition"></a>Tworzenie definicji rozszerzenia niestandardowego skryptu
Podczas definiowania zestawu skalowania maszyn wirtualnych za pomocą szablonu platformy Azure możesz skorzystać z sekcji rozszerzeń udostępnianej przez dostawcę zasobów *Microsoft.Compute/virtualMachineScaleSets*. Sekcja *extensionsProfile* określa, co jest stosowane do wystąpień maszyn wirtualnych w zestawie skalowania. Aby używać rozszerzenia niestandardowego skryptu, wskaż wydawcę *Microsoft.Azure.Extensions* i typ *CustomScript*.

Właściwość *fileUris* służy do definiowania źródłowych skryptów lub pakietów instalacji. Wymagane skrypty są zdefiniowane w sekcji *commandToExecute*, co umożliwia rozpoczęcie procesu instalacji. W poniższym przykładzie zdefiniowano przykładowy skrypt z usługi GitHub, który instaluje i konfiguruje serwer internetowy NGINX:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"
          ],
          "commandToExecute": "bash automate_nginx.sh"
        }
      }
    }
  ]
}
```

Pełny przykład szablonu platformy Azure, który wdraża zestaw skalowania i rozszerzenie niestandardowego skryptu, znajduje się w temacie [https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json). Ten przykładowy szablon zostanie użyty w następnej sekcji.


## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania
Użyjemy przykładowego szablonu, który umożliwia utworzenie zestawu skalowania i zastosowanie rozszerzenia niestandardowego skryptu. Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Teraz utwórz zestaw skalowania maszyn wirtualnych za pomocą polecenia [az group deployment create](/cli/azure/group/deployment). Po wyświetleniu monitu podaj poświadczenia (swoją nazwę użytkownika i hasło) dla poszczególnych wystąpień maszyn wirtualnych:

```azurecli-interactive
az group deployment create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy.json
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.

Każde wystąpienie maszyny wirtualnej w zestawie skalowania pobiera i uruchamia skrypt z usługi GitHub. Bardziej złożony przykład może obejmować instalację wielu składników aplikacji i plików. W przypadku skalowania w górę nowe wystąpienia maszyn wirtualnych automatycznie stosują tę samą definicję rozszerzenia niestandardowego skryptu i instalują wymaganą aplikację.


## <a name="test-your-scale-set"></a>Testowanie zestawu skalowania
Aby zobaczyć, jak działa serwer internetowy, uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [az network public-ip show](/cli/azure/network/public-ip). W poniższym przykładzie pokazano uzyskiwanie adresu IP dla modułu *myScaleSetPublicIP* utworzonego w ramach zestawu skalowania:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetPublicIP \
  --query [ipAddress] \
  --output tsv
```

Wprowadź publiczny adres IP modułu równoważenia obciążenia w przeglądarce internetowej. Moduł równoważenia obciążenia kieruje ruch do jednego z wystąpień maszyn wirtualnych, jak pokazano w poniższym przykładzie:

![Podstawowa strona internetowa na serwerze Nginx](media/tutorial-install-apps-template/running-nginx.png)

Nie zamykaj przeglądarki internetowej, aby w następnym kroku można było zobaczyć zaktualizowaną wersję.


## <a name="update-app-deployment"></a>Aktualizowanie wdrożenia aplikacji
W całym cyklu życia zestawu skalowania konieczne może być wdrożenie zaktualizowanej wersji aplikacji. Rozszerzenie niestandardowego skryptu umożliwia odwołanie się do zaktualizowanego skryptu wdrażania i ponowne zastosowanie rozszerzenia do zestawu skalowania. Gdy zestaw skalowania został utworzony w poprzednim kroku, *upgradePolicy* został ustawiony na *automatyczny*. To ustawienie pozwala wystąpieniom maszyn wirtualnych w zestawie skalowania automatycznie aktualizować aplikację i stosować jej najnowszą wersję.

Aby zaktualizować definicję rozszerzenia niestandardowego skryptu, zmodyfikuj szablon, podając odwołanie do nowego skryptu instalacji. Dla rozszerzenia niestandardowego skryptu należy podać nową nazwę pliku w celu umożliwienia rozpoznania zmiany. Rozszerzenie niestandardowego skryptu nie analizuje zawartości skryptu pod kątem zmian. Następująca definicja używa zaktualizowanego skryptu instalacji, do którego nazwy dołączono element *_v2*:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"
          ],
          "commandToExecute": "bash automate_nginx_v2.sh"
        }
      }
    }
  ]
}
```

Za pomocą polecenia [az group deployment create](/cli/azure/group/deployment) ponownie zastosuj konfigurację rozszerzenia niestandardowego skryptu do wystąpień maszyn wirtualnych w zestawie skalowania. Plik *azuredeployv2.json* umożliwia zastosowanie zaktualizowanej wersji aplikacji. W praktyce należy zmodyfikować istniejący szablon *azuredeploy.json*, podając odwołanie do zaktualizowanego skryptu instalacji, jak pokazano w poprzedniej sekcji. Po wyświetleniu monitu wprowadź nazwę użytkownika i hasło użyte podczas tworzenia zestawu skalowania:

```azurecli-interactive
az group deployment create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy_v2.json
```

Wszystkie wystąpienia maszyn wirtualnych w zestawie skalowania są automatycznie aktualizowane do najnowszej wersji przykładowej strony internetowej. Aby wyświetlić zaktualizowaną wersję, odśwież witrynę internetową w przeglądarce:

![Zaktualizowana strona internetowa na serwerze Nginx](media/tutorial-install-apps-template/running-nginx-updated.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Aby usunąć zestaw skalowania i dodatkowe zasoby, Usuń grupę zasobów i wszystkie jej zasoby za pomocą polecenie [AZ Group Delete](/cli/azure/group). Parametr `--no-wait` zwraca kontrolę do wiersza polecenia bez oczekiwania na zakończenie operacji. Parametr `--yes` potwierdza, że chcesz usunąć zasoby bez wyświetlania dodatkowego monitu.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono automatyczne instalowanie i aktualizowanie aplikacji w zestawie skalowania za pomocą szablonów platformy Azure:

> [!div class="checklist"]
> * Automatyczne instalowanie aplikacji w zestawie skalowania
> * Używanie rozszerzenia niestandardowego skryptu platformy Azure
> * Aktualizowanie uruchomionej aplikacji w zestawie skalowania

Przejdź do następnego samouczka, aby dowiedzieć się, jak automatycznie skalować zestaw skalowania.

> [!div class="nextstepaction"]
> [Automatically scale your scale sets (Automatyczne skalowanie zestawów skalowania)](tutorial-autoscale-template.md)
