---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie maszyny wirtualnej przez dołączenie dysku zarządzanego jako dysk systemu operacyjnego | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie maszyny wirtualnej przez dołączenie dysku zarządzanego jako dysk systemu operacyjnego
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 5d86710fd9173cd0bc3416fedec226f97f12d9d2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29847679"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-cli"></a>Tworzenie maszyny wirtualnej przy użyciu istniejącego zarządzanego dysku systemu operacyjnego za pomocą interfejsu wiersza polecenia

Ten skrypt tworzy maszynę wirtualną przez dołączenie istniejącego dysku zarządzanego jako dysku systemu operacyjnego. Użyj tego skryptu w poprzednich scenariuszach:
* Tworzenie maszyny wirtualnej na podstawie istniejącego zarządzanego dysku systemu operacyjnego, który został skopiowany z dysku zarządzanego z innej subskrypcji
* Tworzenie maszyny wirtualnej na podstawie istniejącego dysku zarządzanego utworzonego z wyspecjalizowanego pliku VHD 
* Tworzenie maszyny wirtualnej na podstawie istniejącego zarządzanego dysku systemu operacyjnego utworzonego z migawki 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-attach-existing-managed-os-disk/create-vm-attach-existing-managed-os-disk.sh "Create VM from a managed disk")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu uzyskania właściwości dysku zarządzanego, dołączenia dysku zarządzanego do nowej maszyny wirtualnej i utworzenia maszyny wirtualnej. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk#az_disk_show) | Pobiera właściwości dysku zarządzanego przy użyciu nazwy dysku i nazwy grupy zasobów. W celu dołączenia dysku zarządzanego do nowej maszyny wirtualnej jest używana właściwość Id |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Tworzy maszynę wirtualną przy użyciu zarządzanego dysku systemu operacyjnego. |
## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
