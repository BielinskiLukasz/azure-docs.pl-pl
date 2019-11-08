---
title: 'Konfigurowanie Global Reach-ExpressRoute: Azure | Microsoft Docs'
description: Ten artykuł ułatwia łączenie obwodów usługi ExpressRoute w celu zapewnienia sieci prywatnej między sieciami lokalnymi i włączania Global Reach.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 64abf820a502db0ee0033ce52ed148bae6b8ffc2
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748171"
---
# <a name="configure-expressroute-global-reach"></a>Konfigurowanie usługi ExpressRoute Global Reach

W tym artykule opisano sposób konfigurowania ExpressRoute Global Reach przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz [ExpressRouteRoute Global REACH](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji Potwierdź następujące kwestie:

* Rozumiesz [przepływy pracy](expressroute-workflows.md)inicjowania obsługi obwodu ExpressRoute.
* Obwody usługi ExpressRoute są w stanie aprowizacji.
* Prywatna Komunikacja równorzędna Azure jest konfigurowana na obwodach usługi ExpressRoute.
* Jeśli chcesz uruchomić program PowerShell lokalnie, sprawdź, czy na komputerze jest zainstalowana najnowsza wersja Azure PowerShell.

### <a name="working-with-azure-powershell"></a>Praca z Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>Identyfikuj obwody

1. Aby rozpocząć konfigurację, zaloguj się do konta platformy Azure i wybierz subskrypcję, której chcesz użyć.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Zidentyfikuj obwody usługi ExpressRoute, które mają być używane. Można włączyć ExpressRoute Global Reach między dowolnymi dwoma obwodami ExpressRoute, o ile znajdują się one w obsługiwanych krajach/regionach i zostały utworzone w różnych lokalizacjach komunikacji równorzędnej. 

   * Jeśli Twoja subskrypcja jest właścicielem obu obwodów, możesz wybrać opcję obwód, aby uruchomić konfigurację w poniższych sekcjach.
   * Jeśli dwa obwody znajdują się w różnych subskrypcjach platformy Azure, musisz mieć autoryzację z jednej subskrypcji platformy Azure. Następnie można przekazać klucz autoryzacji po uruchomieniu polecenia konfiguracji w innej subskrypcji platformy Azure.

## <a name="enable-connectivity"></a>Włączanie łączności

Włącz łączność między sieciami lokalnymi. Istnieją oddzielne zestawy instrukcji dla obwodów znajdujących się w tej samej subskrypcji platformy Azure oraz obwodów, które są w różnych subskrypcjach.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>Obwody usługi ExpressRoute w tej samej subskrypcji platformy Azure

1. Użyj następujących poleceń, aby uzyskać obwód 1 i 2. Dwa obwody znajdują się w tej samej subskrypcji.

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. Uruchom następujące polecenie w stosunku do obwodu 1 i przekaż prywatny identyfikator komunikacji równorzędnej obwodu 2. Podczas uruchamiania polecenia należy zwrócić uwagę na następujące kwestie:

   * Prywatny identyfikator komunikacji równorzędnej wygląda podobnie do poniższego przykładu: 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-AddressPrefix* musi być podsieci IPv4/29, na przykład "10.0.0.0/29". Adresy IP w tej podsieci są używane do nawiązywania łączności między dwoma obwodami usługi ExpressRoute. Adresów w tej podsieci nie należy używać w sieciach wirtualnych platformy Azure ani w sieci lokalnej.

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. Zapisz konfigurację w ramach obwodu 1 w następujący sposób:

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Po zakończeniu poprzedniej operacji będziesz mieć łączność między sieciami lokalnymi po obu stronach przez dwa obwody usługi ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Obwody usługi ExpressRoute w różnych subskrypcjach platformy Azure

Jeśli dwa obwody nie znajdują się w tej samej subskrypcji platformy Azure, musisz mieć autoryzację. W poniższej konfiguracji Autoryzacja jest generowana w ramach subskrypcji obwodu 2, a klucz autoryzacji jest przesyłany do obwodu 1.

1. Wygeneruj klucz autoryzacji.

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   Zanotuj identyfikator prywatnego komunikacji równorzędnej obwodu 2, a także klucz autoryzacji.
2. Uruchom następujące polecenie w stosunku do obwodu 1. Przekaż prywatny identyfikator komunikacji równorzędnej obwodu 2 i klucz autoryzacji.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. Zapisz konfigurację w ramach obwodu 1.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Po zakończeniu poprzedniej operacji będziesz mieć łączność między sieciami lokalnymi po obu stronach przez dwa obwody usługi ExpressRoute.

## <a name="verify-the-configuration"></a>Sprawdzanie konfiguracji

Użyj poniższego polecenia, aby zweryfikować konfigurację obwodu, w którym została wprowadzona konfiguracja (na przykład obwód 1 w poprzednim przykładzie).
```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Jeśli po prostu uruchomisz *$CKT 1* w programie PowerShell, zobaczysz *CircuitConnectionStatus* w danych wyjściowych. Informuje o tym, czy połączenie zostało nawiązane, "połączone" lub "rozłączone". 

## <a name="disable-connectivity"></a>Wyłącz łączność

Aby wyłączyć łączność między sieciami lokalnymi, uruchom polecenia w ramach obwodu, w którym została wprowadzona konfiguracja (na przykład obwód 1 w poprzednim przykładzie).

```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Aby sprawdzić stan, można uruchomić operację pobierania.

Po zakończeniu poprzedniej operacji nie masz już łączności między siecią lokalną za pośrednictwem obwody usługi ExpressRoute.

## <a name="next-steps"></a>Następne kroki
1. [Dowiedz się więcej o ExpressRoute Global Reach](expressroute-global-reach.md)
2. [Weryfikowanie łączności ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Łączenie obwodu usługi ExpressRoute z siecią wirtualną platformy Azure](expressroute-howto-linkvnet-arm.md)
