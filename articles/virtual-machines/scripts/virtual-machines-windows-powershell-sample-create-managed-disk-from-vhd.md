---
title: Tworzenie dysku zarządzanego z pliku VHD na koncie magazynu w ramach subskrypcji — Przykład programu PowerShell
description: Przykładowy skrypt programu Azure PowerShell — tworzenie dysku zarządzanego na podstawie pliku VHD na koncie magazynu w ramach tej samej lub innej subskrypcji
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 3fe13751d75d1b4cf549b4921934922ddccfb211
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75368627"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>Tworzenie dysku zarządzanego na podstawie pliku VHD na koncie magazynu w ramach tej samej lub innej subskrypcji za pomocą programu PowerShell

Ten skrypt tworzy dysk zarządzany na podstawie pliku VHD na koncie magazynu w ramach tej samej lub innej subskrypcji. Użyj tego skryptu, aby importować specjalny (nieuogólniony/przygotowany przez program Sysprep) dysk VHD do zarządzanego dysku systemu operacyjnego w celu utworzenia maszyny wirtualnej. Pozwala on również na importowanie dysku VHD danych do zarządzanego dysku danych. 

Nie należy tworzyć wielu identycznych dysków zarządzanych na podstawie pliku VHD w krótkim czasie. Aby utworzyć dyski zarządzane na podstawie pliku VHD, należy utworzyć migawkę obiektu blob pliku VHD, a następnie użyć tej migawki do tworzenia dysków zarządzanych. W ciągu jednej minuty można utworzyć tylko jedną migawkę obiektu blob, co powoduje błędy tworzenia dysku z powodu dławienia. Aby uniknąć tego ograniczania, utwórz [migawkę zarządzaną z pliku VHD](virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json), a następnie użyj migawki zarządzanej, aby utworzyć wiele dysków zarządzanych w krótkim czasie. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Create managed disk from VHD")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa poniższych poleceń w celu utworzenia dysku zarządzanego na podstawie wirtualnego dysku twardego w innej subskrypcji. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Tworzy konfigurację dysku, która jest używana do tworzenia dysku. Zawiera ona typ magazynu, lokalizację, identyfikator zasobu konta magazynu, w którym przechowywany jest nadrzędny wirtualny dysk twardy, oraz identyfikator URI nadrzędnego wirtualnego dysku twardego. |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Tworzy dysk przy użyciu konfiguracji dysku, nazwy dysku i nazwy grupy zasobów, przekazanych jako parametry. |

## <a name="next-steps"></a>Następne kroki

[Tworzenie maszyny wirtualnej przez dołączenie dysku zarządzanego jako dysku systemu operacyjnego](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell na potrzeby maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
