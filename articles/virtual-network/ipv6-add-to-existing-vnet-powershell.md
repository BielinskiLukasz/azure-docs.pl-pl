---
title: Uaktualnianie aplikacji IPv4 do protokołu IPv6 na platformie Azure Virtual Network — PowerShell
titlesuffix: Azure Virtual Network
description: W tym artykule przedstawiono sposób wdrażania adresów IPv6 w istniejącej aplikacji w usłudze Azure Virtual Network przy użyciu programu Azure PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/21/2019
ms.author: kumud
ms.openlocfilehash: 907a6de2ff89ddd3c2cb5bdab67e1deb984141dc
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965236"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell-preview"></a>Uaktualnianie aplikacji IPv4 do protokołu IPv6 w usłudze Azure Virtual Network — PowerShell (wersja zapoznawcza)

W tym artykule opisano sposób dodawania łączności IPv6 do istniejącej aplikacji IPv4 w sieci wirtualnej platformy Azure z usługa Load Balancer w warstwie Standardowa i publicznego adresu IP. Uaktualnienie w miejscu obejmuje następujące:
- Przestrzeń adresowa IPv6 dla sieci wirtualnej i podsieci
- usługa Load Balancer w warstwie Standardowa z konfiguracjami frontonu IPv4 i IPV6
- Maszyny wirtualne z kartami sieciowymi z konfiguracją IPv4 + IPv6
- IPv publiczny adres IP, aby moduł równoważenia obciążenia miał łączność z Internetem za pośrednictwem Internetu

> [!Important]
> Obsługa protokołu IPv6 dla usługi Azure Virtual Network jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować program PowerShell i używać go lokalnie, ten artykuł będzie wymagał modułu Azure PowerShell w wersji 6.9.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="register-the-service"></a>Rejestrowanie usługi

Przed wdrożeniem aplikacji o podwójnej stercie na platformie Azure należy skonfigurować subskrypcję tej funkcji w wersji zapoznawczej, korzystając z następującej Azure PowerShell:

Zarejestruj się w następujący sposób:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Ukończenie rejestracji funkcji może potrwać do 30 minut. Stan rejestracji można sprawdzić, uruchamiając następujące polecenie Azure PowerShell: Sprawdź rejestrację w następujący sposób:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Po zakończeniu rejestracji Uruchom następujące polecenie:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

### <a name="create-a-standard-load-balancer"></a>Tworzenie usługi Load Balancer w warstwie Standardowa
W tym artykule przyjęto założenie, że wdrożono usługa Load Balancer w warstwie Standardowa zgodnie z opisem w [przewodniku szybki start: tworzenie usługa Load Balancer w warstwie Standardowa Azure PowerShell](../load-balancer/quickstart-create-standard-load-balancer-powershell.md).

## <a name="retrieve-the-resource-group"></a>Pobieranie grupy zasobów

Przed utworzeniem sieci wirtualnej o podwójnym stosie należy pobrać grupę zasobów za pomocą [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell
 $rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>Tworzenie adresów IP IPv6

Utwórz publiczny adres IPv6 przy użyciu elementu [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) dla usługa Load Balancer w warstwie Standardowa. Poniższy przykład tworzy publiczny adres IP IPv6 o nazwie *PublicIP_v6* w grupie zasobów *myResourceGroupSLB* :

```azurepowershell
  
  $PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>Konfigurowanie frontonu modułu równoważenia obciążenia

Pobierz istniejącą konfigurację usługi równoważenia obciążenia, a następnie Dodaj nowy adres IP IPv6 przy użyciu polecenia [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) w następujący sposób:

```azurepowershell
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"
# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6
#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>Konfigurowanie puli zaplecza modułu równoważenia obciążenia

Utwórz pulę zaplecza w lokalnej kopii konfiguracji modułu równoważenia obciążenia i zaktualizuj uruchomioną usługę równoważenia obciążenia przy użyciu nowej konfiguracji puli zaplecza w następujący sposób:

```azurepowershell
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"
# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>Konfigurowanie reguł modułu równoważenia obciążenia
Pobierz istniejącą konfigurację frontonu i puli zaplecza modułu równoważenia obciążenia, a następnie Dodaj nowe reguły równoważenia obciążenia przy użyciu polecenia [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig).

```azurepowershell
# Retrieve the updated (live) versions of the frontend and backend pool
$frontendIPv6 = Get-AzLoadBalancerFrontendIpConfig -Name "dsLbFrontEnd_v6" -LoadBalancer $lb
$backendPoolv6 = Get-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6" -LoadBalancer $lb
# Create new LB rule with the frontend and backend
$lb | Add-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
#Finalize all the load balancer updates on the running load balancer
$lb | Set-AzLoadBalancer
```
## <a name="add-ipv6-address-ranges"></a>Dodawanie zakresów adresów IPv6

Dodaj zakresy adresów IPv6 do sieci wirtualnej i podsieci obsługujące maszyny wirtualne w następujący sposób:

```azurepowershell
#Add IPv6 ranges to the VNET and subnet
#Retreive the VNET object
$vnet = Get-AzVirtualNetwork  -ResourceGroupName $rg.ResourceGroupName -Name "myVnet" 
#Add IPv6 prefix to the VNET
$vnet.addressspace.addressprefixes.add("ace:cab:deca::/48")
#Update the running VNET
$vnet |  Set-AzVirtualNetwork

#Retrieve the subnet object from the local copy of the VNET
$subnet= $vnet.subnets[0]
#Add IPv6 prefix to the Subnet (subnet of the VNET prefix, of course)
$subnet.addressprefix.add("ace:cab:deca::/64")
#Update the running VNET with the new subnet configuration
$vnet |  Set-AzVirtualNetwork

```
## <a name="add-ipv6-configuration-to-nic"></a>Dodawanie konfiguracji protokołu IPv6 do karty sieciowej

Skonfiguruj wszystkie karty sieciowe maszyn wirtualnych z adresem IPv6 przy użyciu polecenia [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) w następujący sposób:

```azurepowershell

#Retrieve the NIC objects
$NIC_1 = Get-AzNetworkInterface -Name "myNic1" -ResourceGroupName $rg.ResourceGroupName
$NIC_2 = Get-AzNetworkInterface -Name "myNic2" -ResourceGroupName $rg.ResourceGroupName
$NIC_3 = Get-AzNetworkInterface -Name "myNic3" -ResourceGroupName $rg.ResourceGroupName
#Add an IPv6 IPconfig to NIC_1 and update the NIC on the running VM
$NIC_1 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_1 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_2 and update the NIC on the running VM
$NIC_2 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_2 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_3 and update the NIC on the running VM
$NIC_3 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_3 | Set-AzNetworkInterface

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Wyświetlanie sieci wirtualnej podwójnego stosu IPv6 w Azure Portal
Sieć wirtualną o podwójnym stosie IPv6 można wyświetlić w Azure Portal w następujący sposób:
1. Na pasku wyszukiwania portalu wprowadź *myVnet*.
2. Gdy **myVnet** pojawia się w wynikach wyszukiwania, wybierz ją. Spowoduje to uruchomienie strony **Przegląd** sieci wirtualnej o podwójnym stosie o nazwie *myVNet*. Sieć wirtualna o podwójnym stosie pokazuje trzy karty sieciowe z konfiguracją protokołów IPv4 i IPv6 znajdującą się w podsieci o podwójnym stosie o nazwie Moja *podsieć*.

  ![Sieć wirtualna o podwójnym stosie IPv6 na platformie Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

> [!NOTE]
> Protokół IPv6 dla usługi Azure Virtual Network jest dostępny w Azure Portal w ramach tej wersji zapoznawczej tylko do odczytu.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Następne kroki

W tym artykule zostały zaktualizowane istniejące usługa Load Balancer w warstwie Standardowa z konfiguracją adresów IP frontonu IPv4 do konfiguracji podwójnego stosu (IPv4 i IPv6). Konfiguracje protokołu IPv6 dodano także do kart sieciowych maszyn wirtualnych w puli zaplecza i do Virtual Network, które je obsługują. Aby dowiedzieć się więcej o obsłudze protokołu IPv6 w sieciach wirtualnych platformy Azure, zobacz [co to jest protokół IPv6 dla systemu azure Virtual Network?](ipv6-overview.md)
