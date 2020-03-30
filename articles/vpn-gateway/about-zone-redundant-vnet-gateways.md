---
title: Bramy sieci wirtualnej nadmiarowe stref w strefach dostępności platformy Azure — informacje
description: Dowiedz się więcej o bramach bramy sieci VPN i u klientach usługi ExpressRoute w strefach dostępności.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: cherylmc
ms.openlocfilehash: f1bbaab99b6422de4053839e2099869d2d08db95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864302"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Bramy sieci wirtualnej nadmiarowe stref w strefach dostępności platformy Azure — informacje

Bramy sieci VPN i usługi ExpressRoute można wdrażać w [strefach dostępności platformy Azure.](../availability-zones/az-overview.md) Zapewni to elastyczność, skalowalność i większą dostępność bram sieci wirtualnej. Wdrażanie bram w strefach dostępności platformy Azure fizycznie i logicznie dzieli bramy w danym regionie, chroniąc jednocześnie lokalną łączność sieci z platformą Azure przed błędami na poziomie strefy.

### <a name="zone-redundant-gateways"></a><a name="zrgw"></a>Bramy strefowo nadmiarowe

Aby automatycznie wdrażać bramy sieci wirtualnej w strefach dostępności, można użyć bramy sieci wirtualnej nadmiarowe strefy. Dzięki bramom nadmiarowym strefowym możesz korzystać z odporności strefy, aby uzyskać dostęp do krytycznych, skalowalnych usług na platformie Azure.

<br>
<br>

![grafika bram nadmiarowych stref](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zonal-gateways"></a><a name="zgw"></a>Bramy strefowe

Aby wdrożyć bramy w określonej strefie, można użyć bram strefowych. Podczas wdrażania bramy strefowej wszystkie wystąpienia bramy są wdrażane w tej samej strefie dostępności.

<br>
<br>

![grafika bram strefowych](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gateway-skus"></a><a name="gwskus"></a>Jednostki SKU bramy

Bramy strefowe i strefowe są dostępne jako nowe jednostki SKU bramy. Dodaliśmy nowe jednostki SKU bramy sieci wirtualnej w regionach usługi Azure AZ. Te jednostki SKU są podobne do odpowiednich istniejących jednostek SKU dla usługi ExpressRoute i bramy sieci VPN, z tą różnicą, że są specyficzne dla bram strefowych i strefowych. Te jednostki SKU można zidentyfikować za pomocą "AZ" w nazwie jednostki SKU.

Aby uzyskać informacje na temat jednostek SKU bramy, zobacz [jednostki SKU bramy sieci VPN](vpn-gateway-about-vpngateways.md#gwsku) i [jednostki SKU bramy usługi ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md#gwsku).

## <a name="public-ip-skus"></a><a name="pipskus"></a>Publiczne jednostki SKU IP

Bramy nadmiarowe strefowe i bramy strefowe opierają się na *standardowej* jednostce SKU publicznego zasobu IP platformy Azure. Konfiguracja publicznego zasobu IP platformy Azure określa, czy wdrażana brama jest nadmiarowa lub strefowa. Jeśli utworzysz publiczny zasób IP z *podstawową* jednostką SKU, brama nie będzie miała nadmiarowości strefy, a zasoby bramy będą regionalne.

### <a name="zone-redundant-gateways"></a><a name="pipzrg"></a>Bramy strefowo nadmiarowe

Podczas tworzenia publicznego adresu IP przy użyciu **standardowej** publicznej jednostki SKU IP bez określania strefy zachowanie różni się w zależności od tego, czy brama jest bramą sieci VPN, czy bramą usługi ExpressRoute. 

* W przypadku bramy sieci VPN dwa wystąpienia bramy zostaną wdrożone w dowolnym 2 z tych trzech stref w celu zapewnienia nadmiarowości strefy. 
* Dla bramy usługi ExpressRoute, ponieważ może istnieć więcej niż dwa wystąpienia, brama może obejmować wszystkie trzy strefy.

### <a name="zonal-gateways"></a><a name="pipzg"></a>Bramy strefowe

Podczas tworzenia publicznego adresu IP przy użyciu **standardowej** publicznej jednostki SKU IP i określenia strefy (1, 2 lub 3), wszystkie wystąpienia bram zostaną wdrożone w tej samej strefie.

### <a name="regional-gateways"></a><a name="piprg"></a>Bramy regionalne

Podczas tworzenia publicznego adresu IP przy użyciu **podstawowej** publicznej jednostki SKU IP brama jest wdrażana jako brama regionalna i nie ma żadnej nadmiarowości strefy wbudowanej w bramę.

## <a name="faq"></a><a name="faq"></a>Najczęściej zadawane pytania

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Co się zmieni po wdrożeniu tych nowych jednostek SKU?

Z twojej perspektywy można wdrażać bramy z nadmiarowością stref. Oznacza to, że wszystkie wystąpienia bram zostaną wdrożone w strefach dostępności platformy Azure, a każda strefa dostępności jest inną domeną błędów i aktualizacji. Dzięki temu bramy są bardziej niezawodne, dostępne i odporne na awarie stref.

### <a name="can-i-use-the-azure-portal"></a>Czy mogę korzystać z witryny Azure portal?

Tak, można użyć witryny Azure Portal do wdrożenia nowych jednostek SKU. Jednak te nowe jednostki SKU będą widoczne tylko w tych regionach platformy Azure, które mają strefy dostępności platformy Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Jakie regiony są dostępne dla mnie do korzystania z nowych jednostek SKU?

Nowe jednostki SKU są dostępne w regionach platformy Azure, które mają strefy dostępności platformy Azure — środkowe stany USA, Francja Środkowa, Europa Północna, Europa Zachodnia i Zachodnie stany USA 2 regiony, wschodnie stany USA, wschodnie stany USA 2, Azja Południowo-Wschodnia, Japonia Wschodnia, Południowa Wielka Brytania. W przyszłości udostępnimy bramy nadmiarowe stref w innych regionach publicznych platformy Azure.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Czy mogę zmienić/przeprowadzić migrację/uaktualnić istniejące bramy sieci wirtualnej do bram strefowych lub strefowych?

Migracja istniejących bram sieci wirtualnej do bram strefowych lub strefowych nie jest obecnie obsługiwana. Można jednak usunąć istniejącą bramę i ponownie utworzyć bramę strefową lub strefową.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Czy mogę wdrożyć bramy sieci VPN i express route w tej samej sieci wirtualnej?

Współistnienie bram sieci VPN i express route w tej samej sieci wirtualnej jest obsługiwane. Należy jednak zarezerwować zakres adresów IP /27 dla podsieci bramy.

## <a name="next-steps"></a>Następne kroki

[Tworzenie strefowo nadmiarowej bramy sieci wirtualnej](create-zone-redundant-vnet-gateway.md)
