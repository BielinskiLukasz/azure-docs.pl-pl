---
title: Samouczek — instalowanie aplikacji w zestawie skalowania za pomocą szablonów platformy Azure | Microsoft Docs
description: Dowiedz się, jak za pomocą szablonów usługi Azure Resource Manager instalować aplikacje w zestawach skalowania maszyn wirtualnych, korzystając z rozszerzenia niestandardowego skryptu
services: virtual-machine-scale-sets
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: dddd8ba98e2825d0e0396963e0f8c23059b97c55
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49468640"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-an-azure-template"></a>Samouczek: instalowanie aplikacji w zestawach skalowania maszyn wirtualnych za pomocą szablonu platformy Azure
Aby uruchamiać aplikacje na wystąpieniach maszyn wirtualnych w zestawie skalowania, musisz najpierw zainstalować składniki aplikacji i wymagane pliki. W poprzednim samouczku omówiono tworzenie niestandardowego obrazu maszyny wirtualnej i wdrażanie własnych wystąpień maszyn wirtualnych. Niestandardowy obraz zawierał ręczne instalacje i konfiguracje aplikacji. Można również zautomatyzować instalację aplikacji w zestawie skalowania po wdrożeniu poszczególnych wystąpień maszyn wirtualnych lub zaktualizować już uruchomioną aplikację. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Automatyczne instalowanie aplikacji w zestawie skalowania
> * Używanie rozszerzenia niestandardowego skryptu platformy Azure
> * Aktualizowanie uruchomionej aplikacji w zestawie skalowania

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

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

Aby uzyskać kompletny przykładowy szablon platformy Azure, który umożliwia wdrożenie zestawu skalowania i rozszerzenia niestandardowego skryptu, zobacz [https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json). Ten przykładowy szablon zostanie użyty w następnej sekcji.


## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania
Użyjemy przykładowego szablonu, który umożliwia utworzenie zestawu skalowania i zastosowanie rozszerzenia niestandardowego skryptu. Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Teraz utwórz zestaw skalowania maszyn wirtualnych za pomocą polecenia [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create). Po wyświetleniu monitu podaj poświadczenia (swoją nazwę użytkownika i hasło) dla poszczególnych wystąpień maszyn wirtualnych:

```azurecli-interactive
az group deployment create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy.json
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.

Każde wystąpienie maszyny wirtualnej w zestawie skalowania pobiera i uruchamia skrypt z usługi GitHub. Bardziej złożony przykład może obejmować instalację wielu składników aplikacji i plików. W przypadku skalowania w górę nowe wystąpienia maszyn wirtualnych automatycznie stosują tę samą definicję rozszerzenia niestandardowego skryptu i instalują wymaganą aplikację.


## <a name="test-your-scale-set"></a>Testowanie zestawu skalowania
Aby zobaczyć, jak działa serwer internetowy, uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [az network public-ip show](/cli/azure/network/public-ip#show). W poniższym przykładzie pokazano uzyskiwanie adresu IP dla modułu *myScaleSetPublicIP* utworzonego w ramach zestawu skalowania:

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
W całym cyklu życia zestawu skalowania konieczne może być wdrożenie zaktualizowanej wersji aplikacji. Rozszerzenie niestandardowego skryptu umożliwia odwołanie się do zaktualizowanego skryptu wdrażania i ponowne zastosowanie rozszerzenia do zestawu skalowania. Podczas tworzenia zestawu skalowania w poprzednim kroku parametr *upgradePolicy* ustawiono na wartość *Automatic*. To ustawienie pozwala wystąpieniom maszyn wirtualnych w zestawie skalowania automatycznie aktualizować aplikację i stosować jej najnowszą wersję.

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

Za pomocą polecenia [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create) ponownie zastosuj konfigurację rozszerzenia niestandardowego skryptu do wystąpień maszyn wirtualnych w zestawie skalowania. Plik *azuredeployv2.json* umożliwia zastosowanie zaktualizowanej wersji aplikacji. W praktyce należy zmodyfikować istniejący szablon *azuredeploy.json*, podając odwołanie do zaktualizowanego skryptu instalacji, jak pokazano w poprzedniej sekcji. Po wyświetleniu monitu wprowadź nazwę użytkownika i hasło użyte podczas tworzenia zestawu skalowania:

```azurecli-interactive
az group deployment create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy_v2.json
```

Wszystkie wystąpienia maszyn wirtualnych w zestawie skalowania są automatycznie aktualizowane do najnowszej wersji przykładowej strony internetowej. Aby wyświetlić zaktualizowaną wersję, odśwież witrynę internetową w przeglądarce:

![Zaktualizowana strona internetowa na serwerze Nginx](media/tutorial-install-apps-template/running-nginx-updated.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Aby pozbyć się zestawu skalowania i dodatkowych zasobów, usuń grupę zasobów wraz z całą zawartością za pomocą polecenia [az group delete](/cli/azure/group#az_group_delete). Parametr `--no-wait` zwraca kontrolę do wiersza polecenia bez oczekiwania na zakończenie operacji. Parametr `--yes` potwierdza, że chcesz usunąć zasoby bez wyświetlania dodatkowego monitu.

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
