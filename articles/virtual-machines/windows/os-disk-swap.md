---
title: Zamień dysk systemu operacyjnego na maszynę wirtualną platformy Azure z programem PowerShell '
description: Zmień dysk systemu operacyjnego używany przez maszynę wirtualną platformy Azure przy użyciu programu PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: ec66892804f3c2d1f831168a2955f2498462cbf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033023"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Zmienianie dysku systemu operacyjnego używanego przez maszynę wirtualną platformy Azure przy użyciu programu PowerShell

Jeśli masz istniejącą maszynę wirtualną, ale chcesz zamienić dysk na dysk kopii zapasowej lub inny dysk systemu operacyjnego, możesz zamienić dyski systemu operacyjnego za pomocą programu Azure PowerShell. Nie trzeba usuwać i ponownie utworzyć maszynę wirtualną. Można nawet użyć dysku zarządzanego w innej grupie zasobów, o ile nie jest on jeszcze używany.

 

Maszyna wirtualna musi zostać zatrzymana\cofnięto alokację, a następnie identyfikator zasobu dysku zarządzanego można zastąpić identyfikatorem zasobu innego dysku zarządzanego.

Upewnij się, że rozmiar maszyny Wirtualnej i typ magazynu są zgodne z dyskiem, który chcesz dołączyć. Na przykład jeśli dysk, którego chcesz użyć, znajduje się w magazynie w wersji Premium, maszyna wirtualna musi być zdolna do magazynu w wersji Premium (na przykład rozmiaru serii DS). Oba dyski również muszą mieć ten sam rozmiar.

Pobierz listę dysków w grupie zasobów przy użyciu [programu Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk)

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Jeśli masz nazwę dysku, którego chcesz użyć, ustaw go jako dysk systemu operacyjnego dla maszyny Wirtualnej. W tym przykładzie stop\deallokuje maszynę wirtualną o nazwie *myVM* i przypisuje dysk o nazwie *newDisk* jako nowy dysk systemu operacyjnego. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Następne kroki**

Aby utworzyć kopię dysku, zobacz [Migawka dysku](snapshot-copy-managed-disk.md).
