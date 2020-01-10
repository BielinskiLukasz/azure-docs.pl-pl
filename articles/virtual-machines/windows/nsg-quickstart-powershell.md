---
title: Otwieranie portów do maszyny wirtualnej przy użyciu Azure PowerShell
description: Dowiedz się, jak otworzyć port/utworzyć punkt końcowy na maszynie wirtualnej z systemem Windows przy użyciu trybu wdrażania usługi Azure Resource Manager i Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: 547ca9c98d77b2aaa6d3630bff4b2ec10dcc5be0
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754172"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Jak otworzyć porty i punkty końcowe na maszynie wirtualnej na platformie Azure przy użyciu programu PowerShell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Szybkie polecenia
Aby utworzyć sieciową grupę zabezpieczeń i reguły listy ACL, potrzebna [jest Najnowsza wersja Azure PowerShell](/powershell/azureps-cmdlets-docs). [Te kroki można również wykonać przy użyciu Azure Portal](nsg-quickstart-portal.md).

Zaloguj się do swojego konta platformy Azure:

```powershell
Connect-AzAccount
```

W poniższych przykładach Zastąp nazwy parametrów własnymi wartościami. Przykładowe nazwy parametrów dołączone do *zasobów*, *myNetworkSecurityGroup*i *myVnet*.

Utwórz regułę przy użyciu elementu [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig). Poniższy przykład tworzy regułę o nazwie *myNetworkSecurityGroupRule* , aby zezwalać na ruch *tcp* na porcie *80*:

```powershell
$httprule = New-AzNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" `
    -Access "Allow" `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority "100" `
    -SourceAddressPrefix "Internet" `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80
```

Następnie utwórz sieciową grupę zabezpieczeń z poleceniem [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) i przypisz utworzoną właśnie regułę protokołu HTTP w następujący sposób. Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup*:

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Teraz przypiszemy do podsieci grupę zabezpieczeń sieci. Poniższy przykład przypisuje istniejącą sieć wirtualną o nazwie *myVnet* do zmiennej *$VNET* za pomocą [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork):

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Skojarz sieciową grupę zabezpieczeń z podsiecią przy użyciu [opcji Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig). Poniższy przykład powoduje skojarzenie podsieci o nazwie Moja *podsieć* z grupą zabezpieczeń sieci:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Na koniec Zaktualizuj swoją sieć wirtualną za pomocą polecenia [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork) , aby zmiany zaczęły obowiązywać:

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Więcej informacji na temat sieciowych grup zabezpieczeń
Szybkie polecenia w tym miejscu umożliwiają rozpoczęcie pracy z ruchem skierowanym do maszyny wirtualnej. Sieciowe grupy zabezpieczeń zapewniają wiele doskonałych funkcji i szczegółowości kontroli dostępu do zasobów. Więcej informacji o [tworzeniu sieciowych grup zabezpieczeń i reguł listy ACL](tutorial-virtual-network.md#secure-network-traffic)można znaleźć tutaj.

W przypadku aplikacji sieci Web o wysokiej dostępności należy umieścić maszyny wirtualne za Azure Load Balancer. Moduł równoważenia obciążenia dystrybuuje ruch do maszyn wirtualnych z sieciową grupą zabezpieczeń, która zapewnia filtrowanie ruchu. Aby uzyskać więcej informacji, zobacz [jak równoważyć obciążenie maszyn wirtualnych z systemem Linux na platformie Azure w celu utworzenia aplikacji o wysokiej](tutorial-load-balancer.md)dostępności.

## <a name="next-steps"></a>Następne kroki
W tym przykładzie utworzono prostą regułę zezwalającą na ruch HTTP. Informacje na temat tworzenia bardziej szczegółowych środowisk można znaleźć w następujących artykułach:

* [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/management/overview.md)
* [Co to jest sieciowa Grupa zabezpieczeń?](../../virtual-network/security-overview.md)
* [Przegląd Azure Load Balancer](../../load-balancer/load-balancer-overview.md)

