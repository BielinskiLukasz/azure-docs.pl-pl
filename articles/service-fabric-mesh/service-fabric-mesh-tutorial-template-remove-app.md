---
title: Samouczek — usuwanie aplikacji uruchomionej w usłudze Azure Service Fabric Mesh | Microsoft Docs
description: Z tego samouczka dowiesz się, jak usuwać aplikację uruchomionej w usłudze Service Fabric oraz zasoby.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/15/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: f366413ae5f758601dfebc2a29ff848feb756083
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960434"
---
# <a name="tutorial-remove-an-application-and-resources"></a>Samouczek: usuwanie aplikacji i zasobów

Niniejszy samouczek jest czwartą częścią serii. Dowiesz się, jak usunąć uruchomioną aplikację, która została [wcześniej wdrożona w usłudze Service Fabric Mesh](service-fabric-mesh-tutorial-template-deploy-app.md). 

Część czwarta serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Usuwanie aplikacji uruchomionej w usłudze Service Fabric Mesh
> * Usuwanie zasobów aplikacji

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Wdrażanie aplikacji w usłudze Service Fabric Mesh przy użyciu szablonu](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [Skalowanie aplikacji uruchomionej w usłudze Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Uaktualnianie aplikacji uruchomionej w usłudze Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * Usuwanie aplikacji

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem możesz [utworzyć bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Otwórz usługę [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md) lub [zainstaluj lokalnie interfejs wiersza polecenia platformy Azure oraz interfejs wiersza polecenia usługi Service Fabric Mesh](service-fabric-mesh-howto-setup-cli.md#install-the-service-fabric-mesh-cli-locally).

## <a name="delete-the-resource-group-and-all-the-resources"></a>Usuwanie grupy zasobów i wszystkich zasobów

Jeśli już nie potrzebujesz żadnych utworzonych zasobów, usuń je. Wcześniej w celu hostowania zasobów wystąpienia usługi Azure Container Registry i aplikacji usługi Service Fabric Mash [tworzono nową grupę zasobów](service-fabric-mesh-tutorial-template-deploy-app.md#create-a-container-registry).  Tę grupę zasobów można usunąć, co spowoduje usunięcie wszystkich skojarzonych zasobów.

```azurecli
az group delete --resource-group myResourceGroup
```

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="individually-delete-the-resources"></a>Indywidualne usuwanie zasobów
Wystąpienie usługi ACR, aplikację usługi Service Fabric Mesh i zasoby sieciowe można usuwać indywidualnie.

Aby usunąć wystąpienie usługi ACR:

```azurecli
az acr delete --resource-group myResourceGroup --name myContainerRegistry
```

Aby usunąć aplikację usługi Service Fabric Mesh:

```azurecli
az mesh app delete --resource-group myResourceGroup --name todolistapp
```

Aby usunąć sieć:
```azurecli
az mesh network delete --resource-group myResourceGroup --name todolistappNetwork
```

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Usuwanie aplikacji uruchomionej w usłudze Service Fabric Mesh
> * Usuwanie zasobów aplikacji