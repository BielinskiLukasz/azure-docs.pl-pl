---
title: "Należy ponownie wdrożyć maszyn wirtualnych systemu Windows na platformie Azure | Dokumentacja firmy Microsoft"
description: "Jak można wdrożyć ponownie maszyny wirtualne systemu Windows na platformie Azure w celu ograniczenia problemów z połączeniem RDP."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: timlt
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 5002010e58a5d8e901770c780f07f9bd625d5eb4
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2018
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Wdróż ponownie maszynę wirtualną systemu Windows do nowego węzła Azure
Jeśli został skierowany trudności Rozwiązywanie problemów z pulpitu zdalnego (RDP) połączenia lub aplikacji dostęp do systemu Windows Azure maszyny wirtualnej (VM), ponownego wdrażania maszyny Wirtualnej może pomóc. Podczas ponownego wdrażania maszyny Wirtualnej maszyny Wirtualnej są przenoszone do nowego węzła w ramach infrastruktury platformy Azure i uprawnień go ponownie na zachowaniu opcji konfiguracji i skojarzonych zasobów. W tym artykule przedstawiono sposób ponownego wdrażania maszyny Wirtualnej przy użyciu programu Azure PowerShell lub w portalu Azure.

> [!NOTE]
> Po ponownego wdrażania maszyny Wirtualnej, tymczasowego dysku zostaną utracone i dynamicznych adresów IP skojarzonych z interfejsu sieci wirtualnej zostały zaktualizowane. 


## <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell
Upewnij się, że masz najnowszą programu Azure PowerShell 1.x zainstalowana na tym komputerze. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

Poniższy przykład wdraża maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`:

```powershell
Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```


[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Kolejne kroki
Jeśli występują problemy dotyczące nawiązywania połączenia z maszyną Wirtualną, możesz znaleźć pomoc w [Rozwiązywanie problemów z połączeniami RDP](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) lub [szczegółowe kroki rozwiązywania problemów RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Jeśli nie można uzyskać dostęp do aplikacji działających na maszynie Wirtualnej, możesz przeczytać [aplikacji Rozwiązywanie problemów z](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

