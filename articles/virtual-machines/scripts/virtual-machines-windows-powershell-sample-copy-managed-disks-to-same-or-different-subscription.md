---
title: Przykładowy skrypt interfejsu programu Azure PowerShell — kopiowanie (przenoszenie) dysków zarządzanych do tej samej lub innej subskrypcji | Microsoft Docs
description: Przykładowy skrypt interfejsu programu Azure PowerShell — kopiowanie (przenoszenie) dysków zarządzanych do tej samej lub innej subskrypcji
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
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: 1ad2f8e27f29c55ef1157822d8c757d7ac818c90
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70081178"
---
# <a name="copy-managed-disks-in-the-same-subscription-or-different-subscription-with-powershell"></a>Kopiowanie dysków zarządzanych do tej samej lub innej subskrypcji przy użyciu programu PowerShell

Ten skrypt tworzy kopię istniejącego dysku zarządzanego w tej samej lub innej subskrypcji. Nowy dysk jest tworzony w tym samym regionie, co nadrzędny dysk zarządzany.   

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.ps1 "Copy managed disk")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia nowego dysku zarządzanego w subskrypcji docelowej przy użyciu identyfikatora źródłowego dysku zarządzanego. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Tworzy konfigurację dysku, która jest używana do tworzenia dysku. Zawiera identyfikator zasobu dysku zarządzanego oraz lokalizację, która jest taka sama jak lokalizacja dysku nadrzędnego.  |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Tworzy dysk przy użyciu konfiguracji dysku, nazwy dysku i nazwy grupy zasobów, przekazanych jako parametry. |


## <a name="next-steps"></a>Następne kroki

[Tworzenie maszyny wirtualnej na podstawie dysku zarządzanego](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell na potrzeby maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).