---
title: Tworzenie bramy sieci wirtualnej strefowo nadmiarowy w strefy dostępności platformy Azure | Dokumentacja firmy Microsoft
description: Wdrażanie bramy VPN Gateway i ExpressRoute w strefach dostępności
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: ade5fa78191fe670bd898e8edcbb1fab3b9b2379
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2018
ms.locfileid: "52274001"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Tworzenie bramy sieci wirtualnej strefowo nadmiarowy w strefy dostępności platformy Azure

Można wdrożyć bramy sieci VPN i ExpressRoute w strefach dostępności platformy Azure. To zapewniają elastyczność, skalowalność i większą dostępność bramy sieci wirtualnej. Wdrażanie bramy w strefach dostępności Azure fizycznie i logicznie dzieli bramy w danym regionie, przy jednoczesnej ochronie łączności sieci środowiska lokalnego do platformy Azure przed awariami poziomu strefy. Aby uzyskać informacje, zobacz [o bramach sieci wirtualnej strefowo nadmiarowe](about-zone-redundant-vnet-gateways.md) i [strefy dostępności platformy Azure o](../availability-zones/az-overview.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Możesz użyć programu PowerShell zainstalowane lokalnie na komputerze lub w usłudze Azure Cloud Shell. Jeśli zdecydujesz się zainstalować i korzystać z programu PowerShell lokalnie, ta funkcja wymaga najnowszej wersji modułu programu PowerShell.

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>Przy użyciu programu PowerShell lokalnie

Jeśli używasz programu PowerShell lokalnie na komputerze, a nie przy użyciu usługi Cloud Shell, musisz zainstalować moduł PowerShell 6.1.1 lub nowszej. Aby sprawdzić wersję programu PowerShell, który został zainstalowany, użyj następującego polecenia:

```azurepowershell
Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
```

Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="variables"></a>1. Zadeklarowanie zmiennych

Poniżej przedstawiono wartości używanych na potrzeby procedury w przykładzie. Ponadto niektóre przykłady używać zmiennych zadeklarowanych w ramach kroków. Jeśli używane są następujące kroki we własnym środowisku, koniecznie Zastąp te wartości własnymi. Podczas określania lokalizacji, należy sprawdzić, czy jest obsługiwany region, który określisz. Aby uzyskać więcej informacji, zobacz [— często zadawane pytania](#faq).

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="configure"></a>2. Tworzenie sieci wirtualnej

Utwórz grupę zasobów.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

Utwórz sieć wirtualną.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzureRmVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="gwsub"></a>3. Dodaj podsieć bramy

Podsieć bramy zawiera zastrzeżone adresy IP, z których korzystają usługi bramy sieci wirtualnej. Aby dodać i ustawić podsieć bramy można użyć następujących przykładów:

Dodaj podsieć bramy.

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Ustaw konfigurację podsieci bramy dla sieci wirtualnej.

```azurepowershell-interactive
$getvnet | Set-AzureRmVirtualNetwork
```
## <a name="publicip"></a>4. Przesłanie żądania dotyczącego publicznego adresu IP
 
W tym kroku wybierz instrukcje, które są stosowane do bramy, która ma zostać utworzona. Wybór strefy do wdrażania bramy zależy od strefy, określony dla publicznego adresu IP.

### <a name="ipzoneredundant"></a>Strefowo nadmiarowe bram

Żądanie publicznego adresu IP za pomocą **standardowa** jednostki SKU publicznego adresu IP, a nie określać żadnej strefy. W tym przypadku standardowego publicznego adresu IP utworzonego będzie strefowo nadmiarowy publiczny adres IP.   

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>Strefowy bram

Żądanie publicznego adresu IP za pomocą **standardowa** jednostki SKU publicznego adresu IP. Określenie strefy (1, 2 lub 3). Wszystkie wystąpienia bramy zostanie wdrożony w tej strefie.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>Regionalne bram

Żądanie publicznego adresu IP za pomocą **podstawowe** jednostki SKU publicznego adresu IP. W takich przypadkach brama jest wdrażana jako brama regionalnych i nie ma żadnych strefy nadmiarowości wbudowanej w bramie. Bramy są tworzone wystąpienia w żadnych stref odpowiednio.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>5. Utwórz konfigurację adresów IP

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>6. Tworzenie bramy

Tworzenie bramy sieci wirtualnej.

### <a name="for-expressroute"></a>Dla usługi ExpressRoute

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>Dla bramy sieci VPN

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="faq"></a>Często zadawane pytania

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Co się zmieni podczas wdrażania tych nowych jednostek SKU?

Z perspektywy można wdrożyć bram dzięki nadmiarowości strefy. Oznacza to, że wszystkie wystąpienia bramy zostanie wdrożone w różnych strefach dostępności platformy Azure, a każda strefa dostępności w innej domenie błędów i aktualizacji. Dzięki temu bram niezawodne, dostępna i odporna na awarie strefy.

### <a name="can-i-use-the-azure-portal"></a>Czy można użyć witryny Azure portal?

Tak, można użyć witryny Azure portal do wdrażania nowych jednostek SKU. Jednak zobaczą te nowe jednostki SKU tylko w tych regionach platformy Azure, które mają strefy dostępności platformy Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Jakie regiony są dostępne w użyciu nowych jednostek SKU?

Nowe jednostki SKU są dostępne w regionach platformy Azure, które mają strefy dostępności platformy Azure — środkowe stany USA, Francja środkowa, Europa Północna, Europa Zachodnia i zachodnie stany USA 2 regionach. Idąc dalej, firma Microsoft będzie udostępniać strefowo nadmiarowe bramy do Ciebie w innych regionach publicznych platformy Azure.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Czy mogę zmiany/migracja/uaktualnić Moje istniejącej bramy sieci wirtualnej do bram strefowo nadmiarowego lub strefowych?

Migrowanie istniejących bram sieci wirtualnej do bram strefowo nadmiarowego lub strefowych nie jest obecnie obsługiwane. Można jednak usunąć istniejącą bramę i ponownie utworzyć bramę strefowo nadmiarowego lub strefowych.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Czy można wdrożyć bramy sieci VPN i Expressroute w tej samej sieci wirtualnej?

Współistnienie bram sieci VPN i Expressroute w tej samej sieci wirtualnej jest obsługiwana. Jednak należy zarezerwować/27 zakres adresów IP podsieci bramy.
