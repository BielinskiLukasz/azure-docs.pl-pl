---
title: Tworzenie maszyny wirtualnej na podstawie wyspecjalizowanej wersji obrazu przy użyciu interfejsu wiersza polecenia platformy Azure
description: Tworzenie maszyny wirtualnej przy użyciu wyspecjalizowanej wersji obrazu w udostępnionej galerii obrazów przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/23/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 478413132a09334c6b5fe6e4d7c4c31c988cd38d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87501052"
---
# <a name="create-a-vm-using-a-specialized-image-version-with-the-azure-cli"></a>Tworzenie maszyny wirtualnej przy użyciu wyspecjalizowanej wersji obrazu przy użyciu interfejsu wiersza polecenia platformy Azure

Utwórz maszynę wirtualną na podstawie [wersji obrazu specjalistycznej](./linux/shared-image-galleries.md#generalized-and-specialized-images) przechowywanej w galerii obrazów udostępnionych. Jeśli chcesz utworzyć maszynę wirtualną przy użyciu uogólnionej wersji obrazu, zobacz [Tworzenie maszyny wirtualnej na podstawie uogólnionej wersji obrazu](vm-generalized-image-version-cli.md).

W tym przykładzie Zastąp nazwy zasobów zgodnie z wymaganiami. 

Wyświetl listę definicji obrazów w galerii za pomocą polecenia [AZ SIG Image-Definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list) , aby wyświetlić nazwę i identyfikator definicji.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Utwórz maszynę wirtualną za pomocą polecenia [AZ VM Create](/cli/azure/vm#az-vm-create) przy użyciu--wyspecjalizowanego parametru, aby wskazać obraz, jest to wyspecjalizowany obraz. 

Użyj identyfikatora definicji obrazu dla programu, `--image` Aby utworzyć maszynę wirtualną na podstawie najnowszej wersji dostępnego obrazu. Możesz również utworzyć maszynę wirtualną na podstawie określonej wersji, podając identyfikator wersji obrazu dla `--image` . 

W tym przykładzie tworzymy maszynę wirtualną na podstawie najnowszej wersji obrazu *myImageDefinition* .

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```


## <a name="next-steps"></a>Następne kroki
[Usługa Azure Image Builder (wersja zapoznawcza)](./linux/image-builder-overview.md) ułatwia automatyzację tworzenia wersji obrazu. można nawet użyć jej do aktualizacji i [tworzenia nowej wersji obrazu z istniejącej wersji obrazu](./linux/image-builder-gallery-update-image-version.md). 

Możesz również utworzyć zasób udostępnionej galerii obrazów przy użyciu szablonów. Dostępnych jest kilka szablonów szybkiego startu platformy Azure: 

- [Tworzenie galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Tworzenie definicji obrazu w usłudze Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Tworzenie wersji obrazu w usłudze Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Tworzenie maszyny wirtualnej na podstawie wersji obrazu](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)
