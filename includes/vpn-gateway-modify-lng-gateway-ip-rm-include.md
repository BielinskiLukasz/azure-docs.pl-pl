---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/15/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6a90b23c10e08e8b14a18f9619cff5aaeb003cab
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045677"
---
### <a name="gwipnoconnection"></a>Aby zmodyfikować element „GatewayIpAddress” bramy sieci lokalnej — brak połączenia bramy

W przypadku zmiany publicznego adresu IP urządzenia sieci VPN, z którym chcesz nawiązać połączenie, musisz zmodyfikować bramę sieci lokalnej w celu odzwierciedlenia tej zmiany. Użyj przykładu, aby zmodyfikować bramę sieci lokalnej bez połączenia bramy.

Podczas modyfikowania tej wartości możesz również zmodyfikować prefiksy adresów. Użyj istniejącej nazwy bramy sieci lokalnej w celu zastąpienia bieżących ustawień. Jeśli użyjesz innej nazwy, utworzysz nową bramę sieci lokalnej, a nie zastąpisz istniejącej.

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name Site1 `
-Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName TestRG1
```

### <a name="gwipwithconnection"></a>Aby zmodyfikować element „GatewayIpAddress” bramy sieci lokalnej — istniejące połączenie bramy

W przypadku zmiany publicznego adresu IP urządzenia sieci VPN, z którym chcesz nawiązać połączenie, musisz zmodyfikować bramę sieci lokalnej w celu odzwierciedlenia tej zmiany. Jeśli istnieje już połączenie bramy, musisz je najpierw usunąć. Po usunięciu połączenia możesz zmodyfikować adres IP bramy i ponownie utworzyć nowe połączenie. Jednocześnie możesz również zmodyfikować prefiksy adresów. Spowoduje to pewien przestój połączenia sieci VPN. W przypadku modyfikowania adresu IP bramy nie musisz usuwać bramy sieci VPN. Musisz usunąć tylko połączenie.
 

1. Usuń połączenie. Nazwę połączenia można znaleźć za pomocą polecenia cmdlet "Get-AzVirtualNetworkGatewayConnection".

   ```azurepowershell-interactive
   Remove-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 `
   -ResourceGroupName TestRG1
   ```
2. Zmodyfikuj wartość klasy „GatewayIpAddress”. Jednocześnie możesz również zmodyfikować prefiksy adresów. Pamiętaj, aby użyć istniejącej nazwy bramy sieci lokalnej w celu zastąpienia bieżących ustawień. Jeśli tego nie zrobisz, utworzysz nową bramę sieci lokalnej, a nie zastąpisz istniejącej.

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name Site1 `
   -Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
   -GatewayIpAddress "104.40.81.124" -ResourceGroupName TestRG1
   ```
3. Utwórz połączenie. W tym przykładzie skonfigurujemy połączenie typu IPsec. Podczas ponownego tworzenia połączenia należy użyć typu połączenia, który został określony dla danej konfiguracji. O dodatkowych typach połączeń można przeczytać na stronie [PowerShell cmdlet](https://msdn.microsoft.com/library/mt603611.aspx) (Polecenia cmdlet programu PowerShell).  Aby uzyskać nazwę VirtualNetworkGateway, możesz uruchomić polecenie cmdlet "Get-AzVirtualNetworkGateway".
   
    Ustaw zmienne.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1

   $vnetgw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
   ```
   
    Utwórz połączenie.

   ```azurepowershell-interactive 
   New-AzVirtualNetworkGatewayConnection -Name VNet1Site1 -ResourceGroupName TestRG1 `
   -Location "East US" `
   -VirtualNetworkGateway1 $vnetgw `
   -LocalNetworkGateway2 $local `
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```