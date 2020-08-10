---
title: 'Jak skonfigurować OpenVPN na platformie Azure VPN Gateway: PowerShell'
description: Dowiedz się, jak włączyć protokół OpenVPN na platformie VPN Gateway Azure przy użyciu programu PowerShell dla środowiska punkt-lokacja.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: c13d14ad2d06cbc43d80c05258bdbd3303da4838
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036831"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Skonfiguruj OpenVPN dla usługi Azure Point-to-site VPN Gateway

W tym artykule opisano konfigurowanie **protokołu OpenVPN®** na platformie Azure VPN Gateway. W tym artykule przyjęto założenie, że masz już środowisko pracy typu punkt-lokacja. Jeśli tego nie zrobisz, użyj instrukcji z kroku 1, aby utworzyć sieć VPN typu punkt-lokacja.



## <a name="1-create-a-point-to-site-vpn"></a><a name="vnet"></a>1. Utwórz sieć VPN typu punkt-lokacja

Jeśli nie masz jeszcze działającego środowiska punkt-lokacja, postępuj zgodnie z instrukcjami, aby je utworzyć. Zobacz [Tworzenie sieci VPN typu punkt-lokacja](vpn-gateway-howto-point-to-site-resource-manager-portal.md) , aby utworzyć i skonfigurować bramę sieci VPN typu punkt-lokacja z natywnym uwierzytelnianiem certyfikatu platformy Azure. 

> [!IMPORTANT]
> Podstawowa jednostka SKU nie jest obsługiwana w przypadku OpenVPN.

## <a name="2-enable-openvpn-on-the-gateway"></a><a name="enable"></a>2. Włącz OpenVPN na bramie

Włącz OpenVPN na bramie. Przed uruchomieniem następujących poleceń upewnij się, że brama jest już skonfigurowana dla połączeń punkt-lokacja (IKEv2 lub SSTP):

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować klientów dla usługi OpenVPN, zobacz [Konfigurowanie klientów OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" jest znakiem towarowym OpenVPN Inc.**
