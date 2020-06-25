---
title: Co to jest usługa Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Omówienie funkcji, architektury i implementacji usługi Azure Load Balancer. Dowiedz się, jak działa Load Balancer i jak korzystać z niego w chmurze.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/14/2020
ms.author: allensu
ms.openlocfilehash: 598df2f0a1e9badebbb1bf8e7ec0f93ff98d55ce
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85341781"
---
# <a name="what-is-azure-load-balancer"></a>Co to jest usługa Azure Load Balancer?

*Równoważenie obciążenia* polega na równomiernym rozłożeniu obciążenia (ruch sieciowy przychodzący) między grupą zasobów lub serwerów zaplecza. 

Azure Load Balancer działa w warstwie cztery z modelem połączenia typu Open Systems (OSI). Jest to pojedynczy punkt kontaktu dla klientów. Load Balancer dystrybuuje przepływy przychodzące, które docierają do frontonu modułu równoważenia obciążenia z wystąpieniami puli zaplecza. Te przepływy są zależne od skonfigurowanych reguł równoważenia obciążenia i sond kondycji. Wystąpienia puli zaplecza mogą być Virtual Machinesami platformy Azure lub wystąpieniami w zestawie skalowania maszyn wirtualnych.

**[Publiczny moduł równoważenia obciążenia](./components.md#frontend-ip-configurations)** może zapewnić połączenia wychodzące maszyn wirtualnych w sieci wirtualnej. Te połączenia są realizowane przez przetłumaczenie prywatnych adresów IP na publiczne adresy IP. Publiczne usługi równoważenia obciążenia są używane do równoważenia obciążenia ruchu internetowego na maszynach wirtualnych.

**[Wewnętrzny (lub prywatny) moduł równoważenia obciążenia](./components.md#frontend-ip-configurations)** jest używany w przypadku, gdy prywatne adresy IP są używane tylko na frontonie. Wewnętrzne moduły równoważenia obciążenia są używane do równoważenia obciążenia ruchu w sieci wirtualnej. Dostęp do frontonu modułu równoważenia obciążenia można uzyskać z sieci lokalnej w scenariuszu hybrydowym.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" width="512" title="Azure Load Balancer">
</p>

*Ilustracja: Równoważenie aplikacji wielowarstwowych przy użyciu Load Balancer publicznego i wewnętrznego*

Aby uzyskać więcej informacji na temat poszczególnych składników usługi równoważenia obciążenia, zobacz [Azure Load Balancer Components](./components.md).

## <a name="why-use-azure-load-balancer"></a>Dlaczego warto używać Azure Load Balancer?
Za pomocą usługa Load Balancer w warstwie Standardowa można skalować aplikacje i tworzyć usługi o wysokiej dostępności. Moduł równoważenia obciążenia obsługuje scenariusze przychodzące i wychodzące. Moduł równoważenia obciążenia zapewnia małe opóźnienia i wysoką przepływność oraz skaluje do milionów przepływów dla wszystkich aplikacji TCP i UDP.

Kluczowe scenariusze, które można wykonać za pomocą usługa Load Balancer w warstwie Standardowa obejmują:

- Równoważyć obciążenie ruchem **[wewnętrznym](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-internal-portal)** i **[zewnętrznym](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal)** z maszynami wirtualnymi platformy Azure.

- Zwiększ dostępność przez dystrybucję zasobów **[w ramach](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zonal-portal)** i **[między](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal)** strefami.

- Skonfiguruj **[łączność wychodzącą](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)** dla maszyn wirtualnych platformy Azure.

- Do monitorowania zasobów o zrównoważonym obciążeniu służy **[sondy kondycji](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** .

- Zastosowanie **[przekazywania portów](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** w celu uzyskania dostępu do maszyn wirtualnych w sieci wirtualnej przez publiczny adres IP i port.

- Włącz obsługę **[równoważenia obciążenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell)** **[protokołu IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)**.

- Usługa Load Balancer w warstwie Standardowa udostępnia metryki wielowymiarowe za [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview).  Te metryki można filtrować, grupować i rozdzielić w danym wymiarze.  Zapewniają one bieżące i historyczne informacje o wydajności i kondycji usługi.  Obsługiwane są również Resource Health. Aby uzyskać więcej informacji, przejrzyj **[diagnostykę usługa Load Balancer w warstwie Standardowa](load-balancer-standard-diagnostics.md)** .

- Równoważyć obciążenie usług na **[wielu portach, wielu adresach IP lub obu](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview)**.

- Przenoszenie **[wewnętrznych](https://docs.microsoft.com/azure/load-balancer/move-across-regions-internal-load-balancer-portal)** i **[zewnętrznych](https://docs.microsoft.com/azure/load-balancer/move-across-regions-external-load-balancer-portal)** zasobów modułu równoważenia obciążenia w regionach platformy Azure.

- Równoważ obciążenie protokołów TCP i UDP na wszystkich portach jednocześnie przy użyciu **[portów ha](https://docs.microsoft.com/azure/load-balancer/load-balancer-ha-ports-overview)**.

### <a name="secure-by-default"></a><a name="securebydefault"></a>Zabezpieczenie domyślne

Usługa Load Balancer w warstwie Standardowa jest oparty na modelu zabezpieczeń sieci o zerowym zaufaniu. Usługa Load Balancer w warstwie Standardowa domyślnie zabezpieczone i jest częścią sieci wirtualnej. Sieć wirtualna jest siecią prywatną i izolowaną.  Oznacza to, że standardowe usługi równoważenia obciążenia i standardowe publiczne adresy IP są zamykane dla przepływów przychodzących, chyba że są otwierane przez sieciowe grupy zabezpieczeń. Sieciowych grup zabezpieczeń są używane do jawnego zezwalania na dozwolony ruch.  Jeśli nie masz sieciowej grupy zabezpieczeń w podsieci lub karcie sieciowej zasobu maszyny wirtualnej, ruch nie może nawiązać połączenia z tym zasobem. Aby dowiedzieć się więcej o sieciowych grup zabezpieczeń i sposobach ich stosowania w danym scenariuszu, zobacz [sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md).
Podstawowa Load Balancer jest domyślnie otwarta w Internecie.

## <a name="pricing-and-sla"></a>Cennik i Umowa SLA

Aby uzyskać informacje o cenach usługa Load Balancer w warstwie Standardowa, zobacz [Cennik usługi Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).
Za użycie modułu równoważenia obciążenia w warstwie Podstawowa nie są naliczane opłaty.
Zapoznaj się z umową [SLA dla Load Balancer](https://aka.ms/lbsla). Podstawowa Load Balancer nie ma umowy SLA.

## <a name="next-steps"></a>Następne kroki
Zobacz [uaktualnianie podstawowego Load Balancer](upgrade-basic-standard.md) , aby uaktualnić podstawowe Load Balancer do usługa Load Balancer w warstwie Standardowa.

Aby rozpocząć korzystanie z Load Balancer, zobacz [tworzenie usługa Load Balancer w warstwie Standardowa publicznego](quickstart-load-balancer-standard-public-portal.md) .

Aby uzyskać więcej informacji o Azure Load Balancer ograniczeniach i składnikach, zobacz [składniki Azure Load Balancer](./components.md) i [koncepcje Azure Load Balancer](./concepts.md)

Aby zapoznać się z porównaniem opcji równoważenia obciążenia platformy Azure, zobacz [Omówienie opcji równoważenia obciążenia na platformie Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).
