---
title: Utwórz dysk zarządzany na podstawie wersji obrazu
description: Utwórz dysk zarządzany na podstawie wersji obrazu w udostępnionej galerii obrazów.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: 40d3ff736194c4634b949af52ee7b09db20dd06d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026135"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>Utwórz dysk zarządzany na podstawie wersji obrazu

W razie konieczności można wyeksportować system operacyjny lub pojedynczy dysk danych z wersji obrazu jako dysk zarządzany z wersji obrazu przechowywanej w galerii obrazów udostępnionych.


## <a name="cli"></a>Interfejs wiersza polecenia

Wyświetl listę wersji obrazu w galerii za pomocą polecenia [AZ SIG Image-Version list](/cli/azure/sig/image-version#az_sig_image_version_list). W tym przykładzie szukamy wszystkich wersji obrazu, które są częścią definicji obrazu *myImageDefinition* w galerii obrazów w *galerii* .

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

Ustaw `source` zmienną na identyfikator wersji obrazu, a następnie użyj polecenie [AZ Disk Create](/cli/azure/disk?view=azure-cli-latest#az_disk_create) w celu utworzenia dysku zarządzanego. 

W tym przykładzie wyeksportuje dysk systemu operacyjnego z wersji obrazu w celu utworzenia dysku zarządzanego o nazwie *myManagedOSDisk* w regionie *wschodniego* w grupie zasobów o nazwie Moja *zasobów*. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedOSDisk --gallery-image-reference $source 
```



Jeśli chcesz wyeksportować dysk danych z wersji obrazu, Dodaj, `--gallery-image-reference-lun` Aby określić lokalizację LUN dysku danych do wyeksportowania. 

W tym przykładzie wyeksportuje dysk danych znajdujący się w numerze LUN 0 wersji obrazu w celu utworzenia dysku zarządzanego o nazwie *myManagedDataDisk* w regionie *wschodnim* w grupie zasobów o nazwie Moja *zasobów*. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDataDisk --gallery-image-reference $source --gallery-image-reference-lun 0
``` 

## <a name="powershell"></a>PowerShell

Wyświetl listę wersji obrazu w galerii za pomocą polecenia [Get-AzResource](/powershell/module/az.resources/get-azresource). 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```

Gdy uzyskasz wszystkie potrzebne informacje, możesz użyć polecenie [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) , aby pobrać wersję obrazu źródłowego, która ma zostać użyta, i przypisać ją do zmiennej. W tym przykładzie pobieramy `1.0.0` wersję obrazu `myImageDefinition` definicji w `myGallery` galerii źródła w `myResourceGroup` grupie zasobów.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

Po ustawieniu `source` zmiennej na identyfikator wersji obrazu Użyj polecenie [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) , aby utworzyć konfigurację dysku i polecenie [New-AzDisk](/powershell/module/az.compute/new-azdisk) w celu utworzenia dysku. 

W tym przykładzie wyeksportuje dysk systemu operacyjnego z wersji obrazu w celu utworzenia dysku zarządzanego o nazwie *myManagedOSDisk* w regionie *wschodniego* w grupie zasobów o nazwie Moja *zasobów*. 

Utwórz konfigurację dysku.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id}
```

Utwórz dysk.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedOSDisk
```

Jeśli chcesz wyeksportować dysk danych w wersji obrazu, Dodaj identyfikator LUN do konfiguracji dysku, aby określić lokalizację LUN dysku danych do wyeksportowania. 

W tym przykładzie wyeksportuje dysk danych znajdujący się w numerze LUN 0 wersji obrazu w celu utworzenia dysku zarządzanego o nazwie *myManagedDataDisk* w regionie *wschodnim* w grupie zasobów o nazwie Moja *zasobów*. 

Utwórz konfigurację dysku.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=0}
```

Utwórz dysk.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedDataDisk
```

## <a name="next-steps"></a>Następne kroki

Możesz również utworzyć wersję obrazu z dysku zarządzanego przy użyciu [interfejsu wiersza polecenia platformy Azure](image-version-managed-image-cli.md) lub [programu PowerShell](image-version-managed-image-powershell.md).


