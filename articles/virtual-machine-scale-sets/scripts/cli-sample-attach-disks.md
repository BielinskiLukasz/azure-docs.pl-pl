---
title: Przykłady interfejsu wiersza polecenia platformy Azure — podłączanie dysków danych i ich używanie | Microsoft Docs
description: Przykłady interfejsu wiersza polecenia platformy Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f7acfb068d000eb76ea526ea7fb3244c585beebf
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54887669"
---
# <a name="attach-and-use-data-disks-with-a-virtual-machine-scale-set-with-the-azure-cli"></a>Dołączanie dysków danych i ich używanie z zestawem skalowania maszyn wirtualnych za pomocą interfejsu wiersza polecenia platformy Azure
Ten skrypt tworzy zestaw skalowania maszyn wirtualnych oraz dołącza dyski danych i je przygotowuje.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/use-data-disks/use-data-disks.sh "Create a virtual machine scale set with data disks")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Uruchom następujące polecenie, aby usunąć grupę zasobów, zestaw skalowania i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu
Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, zestawu skalowania maszyn wirtualnych i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/ad/group#az_ad_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | Tworzy zestaw skalowania maszyn wirtualnych i łączy go z siecią wirtualną, podsiecią i sieciową grupą zabezpieczeń. Zostanie też utworzony moduł równoważenia obciążenia w celu dystrybucji ruchu do wielu wystąpień maszyn wirtualnych. To polecenie określa również obraz maszyny wirtualnej do użycia oraz poświadczenia administracyjne.  |
| [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach) | Tworzy i dołącza dysk danych do zestawu skalowania maszyn wirtualnych. |
| [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) | Instaluje rozszerzenie niestandardowego skryptu platformy Azure w celu uruchomienia skryptu, który przygotowuje dyski danych w każdym wystąpieniu maszyny wirtualnej. |
| [az group delete](/cli/azure/ad/group#delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/overview).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia platformy Azure dotyczących zestawu skalowania maszyn wirtualnych można znaleźć w [dokumentacji zestawu skalowania maszyn wirtualnych platformy Azure](../cli-samples.md).