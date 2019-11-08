---
title: 'Resetowanie komunikacji równorzędnej obwodu — ExpressRoute: Azure | Microsoft Docs'
description: Jak wyłączyć i włączyć komunikację równorzędną obwodu ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 2258bfbf2a1bf6a8dccf9d274e6e89e4c53a978a
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748228"
---
# <a name="reset-expressroute-circuit-peerings"></a>Resetowanie komunikacji równorzędnej obwodu ExpressRoute

W tym artykule opisano sposób wyłączania i włączania komunikacji równorzędnej obwodu usługi ExpressRoute przy użyciu programu PowerShell. Wyłączenie komunikacji równorzędnej spowoduje wyłączenie sesji protokołu BGP zarówno w ramach połączenia podstawowego, jak i pomocniczego obwodu usługi ExpressRoute. Utracisz łączność za pośrednictwem tej komunikacji równorzędnej z firmą Microsoft. Po włączeniu komunikacji równorzędnej zostanie przeprowadzona sesja protokołu BGP z połączeniem podstawowym i pomocniczym połączeniem obwodu usługi ExpressRoute. Połączenie zostanie odwiązane za pośrednictwem komunikacji równorzędnej z firmą Microsoft. Komunikacja równorzędna firmy Microsoft i prywatna Komunikacja równorzędna Azure można włączyć i wyłączyć niezależnie od obwodu usługi ExpressRoute. Podczas pierwszej konfiguracji komunikacji równorzędnej w obwodzie ExpressRoute, Komunikacja równorzędna jest domyślnie włączona.

Istnieje kilka scenariuszy, w których może być przydatne Resetowanie komunikacji równorzędnej ExpressRoute.
* Przetestuj projekt i implementację odzyskiwania po awarii. Na przykład masz dwa obwody usługi ExpressRoute. Można wyłączyć komunikację równorzędną jednego obwodu i wymusić przełączenie ruchu sieciowego do trybu failover w innym obwodzie.
* Włącz wykrywanie dwukierunkowego przekazywania (BFD) w prywatnej komunikacji równorzędnej Azure lub komunikacji równorzędnej firmy Microsoft w obwodzie usługi ExpressRoute. BFD jest domyślnie włączona w prywatnej komunikacji równorzędnej Azure, jeśli obwód usługi ExpressRoute został utworzony po 1 2018 sierpnia i w przypadku komunikacji równorzędnej firmy Microsoft, jeśli obwód ExpressRoute jest tworzony po dniu 1 2019 października. Jeśli obwód został utworzony przed tym, BFD nie został włączony. Możesz włączyć BFD, wyłączając komunikację równorzędną i włączając ją ponownie. 

### <a name="working-with-azure-powershell"></a>Praca z Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Resetowanie komunikacji równorzędnej

1. Jeśli używasz programu PowerShell lokalnie, Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i Połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

   ```azurepowershell
   Connect-AzAccount
   ```
2. Jeśli masz wiele subskrypcji platformy Azure, wyświetl subskrypcje dla konta.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
3. Wskaż subskrypcję, której chcesz użyć.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
4. Uruchom następujące polecenia, aby pobrać obwód ExpressRoute.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. Określ komunikację równorzędną, którą chcesz wyłączyć lub włączyć. *Komunikacja równorzędna* jest tablicą. W poniższym przykładzie Komunikacja równorzędna [0] polega na prywatnej komunikacji równorzędnej Azure i komunikacji równorzędnej [1] komunikacji równorzędnej firmy Microsoft.

   ```azurepowershell-interactive
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"cd011bef-dc79-49eb-b4c6-81fb6ea5d178"
   ProvisioningState                : Succeeded
   Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             :
   ServiceProviderProperties        : {
                                     "ServiceProviderName": "Coresite",
                                     "PeeringLocation": "Los Angeles",
                                     "BandwidthInMbps": 50
                                   }
   ServiceKey                       : ########-####-####-####-############
   Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "10.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "10.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 789,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "NotConfigured",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     },
                                     {
                                       "Name": "MicrosoftPeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/MicrosoftPeering",
                                       "PeeringType": "MicrosoftPeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "3.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "3.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 345,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [
                                           "3.0.0.3/32"
                                         ],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "ValidationNeeded",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
   Authorizations                   : []
   AllowClassicOperations           : False
   GatewayManagerEtag               :
   ```
6. Uruchom następujące polecenia, aby zmienić stan komunikacji równorzędnej.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   Komunikacja równorzędna powinna znajdować się w stanie ustawionym przez użytkownika. 

## <a name="next-steps"></a>Następne kroki
Jeśli potrzebujesz pomocy w rozwiązywaniu problemów z programem ExpressRoute, zapoznaj się z następującymi artykułami:
* [Weryfikowanie połączenia usługi ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Rozwiązywanie problemów z wydajnością sieci](expressroute-troubleshooting-network-performance.md)
