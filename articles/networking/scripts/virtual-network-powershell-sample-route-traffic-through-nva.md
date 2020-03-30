---
title: Przykładowy skrypt programu Azure PowerShell — kierowanie ruchu przez wirtualne urządzenie sieciowe | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell — kierowanie ruchu przez wirtualne urządzenie sieciowe w ramach zapory.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: kumud
ms.openlocfilehash: b31714e62ba0e7a0e4882d9b9fa830b3a14fe855
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888509"
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>Kierowanie ruchu przez wirtualne urządzenie sieciowe

Ten przykładowy skrypt tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Tworzy również maszynę wirtualną z włączonym przekazywaniem adresu IP, aby przekierowywać ruch między dwiema podsieciami. Po uruchomieniu skryptu można wdrażać oprogramowanie sieciowe, takie jak aplikacja zapory, na maszynie wirtualnej.

W razie potrzeby zainstaluj program Azure PowerShell przy użyciu instrukcji znalezionych `Connect-AzAccount` w [przewodniku programu Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/), a następnie uruchom w celu utworzenia połączenia z platformą Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.ps1 "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```
## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, sieci wirtualnej i sieciowych grup zabezpieczeń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Tworzy sieć wirtualną i podsieć frontonu platformy Azure. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Tworzy podsieci zaplecza i strefy DMZ. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Tworzy publiczny adres IP, aby uzyskać dostęp do maszyny Wirtualnej z Internetu. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Tworzy interfejs sieci wirtualnej i włącza przekazywanie adresów IP. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Tworzy sieciową grupę zabezpieczeń. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Tworzy reguły sieciowych grup zabezpieczeń, które zezwalają na użycie portów HTTP i HTTPS dla ruchu przychodzącego do maszyny wirtualnej. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)| Tworzy skojarzenie sieciowych grup zabezpieczeń i tabel tras z podsieciami. |
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)| Tworzy tabelę tras dla wszystkich tras. |
| [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)| Tworzy trasy do kierowania ruchu między podsieciami i Internet za pośrednictwem maszyny Wirtualnej. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Tworzy maszynę wirtualną i dołącza do niej kartę sieciową. To polecenie określa również obraz maszyny wirtualnej do użycia oraz poświadczenia administracyjne. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)  | Usuwa grupę zasobów i wszystkie zasoby, które zawiera. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Dodatkowe przykłady skryptów programu PowerShell dla sieci można znaleźć w [dokumentacji i omówieniu sieci platformy Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
