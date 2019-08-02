---
title: Sieć platformy Azure | Microsoft Docs
description: Dowiedz się więcej na temat usług sieciowych na platformie Azure i ich możliwości.
services: networking
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2019
ms.author: kumud
ms.openlocfilehash: 857b38693ca85d6ab397cbe850f0cd530fefc88c
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598394"
---
# <a name="azure-networking"></a>Sieć platformy Azure

Usługi sieciowe na platformie Azure zapewniają różne możliwości sieci, które mogą być używane razem lub oddzielnie. Kliknij dowolną z następujących funkcji, aby dowiedzieć się więcej na ich temat:
- [**Usługi łączności**](#connect): Połącz zasoby platformy Azure i zasoby lokalne przy użyciu dowolnej lub kombinacji tych usług sieciowych na platformie Azure — Virtual Network (VNet), wirtualnej sieci WAN, ExpressRoute, VPN Gateway, Azure DNS lub Azure bastionu.
- [**Usługi ochrony aplikacji**](#protect) Ochrona aplikacji przy użyciu dowolnej lub kombinacji tych usług sieciowych na platformie Azure — DDoS ochronę, zaporę, sieciowe grupy zabezpieczeń, zaporę aplikacji sieci Web lub Virtual Network punkty końcowe.
- [**Usługi dostarczania aplikacji**](#deliver) Dostarczaj aplikacje w sieci platformy Azure przy użyciu dowolnej lub kombinacji tych usług sieciowych na platformie Azure — Content Delivery Network (CDN), usługi Azure front-drzwi, Traffic Manager, Application Gateway lub Load Balancer.
- [**Monitorowanie sieci**](#monitor) — Monitoruj zasoby sieciowe przy użyciu dowolnej lub kombinacji tych usług sieciowych na platformie Azure — Network Watcher, monitor ExpressRoute, Azure monitor lub punkt dostępu do terminalu sieci wirtualnej (TAP).

## <a name="connect"></a>Usługi łączności
 
W tej sekcji opisano usługi, które zapewniają łączność między zasobami platformy Azure, łącznością z siecią lokalną i zasobami platformy Azure oraz gałęzią połączenia z gałęzią w usłudze Azure Virtual Network, ExpressRoute, VPN Gateway, wirtualnej sieci WAN, systemie DNS i platformie Azure Bastionu.

|Usługa|Dlaczego warto używać?|Scenariusze|
|---|---|---|
|[Sieć wirtualna](#vnet)|Umożliwia zasobom platformy Azure bezpieczne komunikowanie się ze sobą, Internetem i sieciami lokalnymi.| <p>[Filtrowanie ruchu sieciowego](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[Routing ruchu sieciowego](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[Ograniczenie dostępu przez sieć do zasobów](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[Łączenie sieci wirtualnych](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[ExpressRoute](#expressroute)|Rozszerza sieci lokalne do chmury firmy Microsoft przez połączenie prywatne obsługiwane przez dostawcę połączenia.|<p>[Tworzenie i modyfikowanie obwodu usługi ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[Tworzenie i modyfikowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[Link a VNet to an ExpressRoute circuit (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute)](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[Konfigurowanie filtrów tras dla obwodów usługi ExpressRoute i zarządzanie nimi](../expressroute/how-to-routefilter-portal.md)</p>|
|[VPN Gateway](#vpngateway)|Wysyła zaszyfrowany ruch sieciowy między siecią wirtualną platformy Azure a lokalizacją lokalną za pośrednictwem publicznego Internetu.|<p>[Lokacja-lokacja — połączenia](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[Połączenia między sieciami wirtualnymi](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[Połączenia punkt-lokacja](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[Wirtualna sieć WAN](#virtualwan)|Optymalizuje i automatyzuje połączenia gałęzi z, i za pośrednictwem platformy Azure. Regiony platformy Azure służą jako centra, do których można połączyć gałęzie.|<p>[Połączenia lokacja-lokacja](../virtual-wan/virtual-wan-site-to-site-portal.md), [Połączenia ExpressRoute](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[System DNS platformy Azure](#dns)|Hostuje domeny DNS, które zapewniają rozpoznawanie nazw przy użyciu infrastruktury Microsoft Azure.|<p>[Hostowanie własnej domeny w usłudze Azure DNS](../dns/dns-delegate-domain-azure-dns.md)</p><p>[Tworzenie rekordów DNS dla aplikacji sieci Web](../dns/dns-web-sites-custom-domain.md)</p> <p>[Utwórz rekord aliasu dla Traffic Manager](../dns/tutorial-alias-tm.md)</p> <p>[Utwórz rekord aliasu dla publicznego adresu IP](../dns/tutorial-alias-pip.md)</p> <p>[Utwórz rekord aliasu dla rekordu zasobu strefy](../dns/tutorial-alias-rr.md)</p>|
|[Azure bastionu (wersja zapoznawcza)](#bastion)|Skonfiguruj bezpieczne i bezproblemowe połączenie RDP/SSH z maszynami wirtualnymi bezpośrednio w witrynie Azure Portal za pośrednictwem protokołu SSL. Po nawiązaniu połączenia za pośrednictwem usługi Azure bastionu maszyny wirtualne nie potrzebują publicznego adresu IP|<p>[Tworzenie hosta usługi Azure bastionu](../bastion/bastion-create-host-portal.md)</p><p>[Nawiązywanie połączenia przy użyciu protokołu SSH z maszyną wirtualną z systemem Linux](../bastion/bastion-connect-vm-ssh.md)</p><p>[Nawiązywanie połączenia przy użyciu protokołu RDP z maszyną wirtualną z systemem Windows](../bastion/bastion-connect-vm-rdp.md)</p>|
||||


### <a name="vnet"></a>Sieć wirtualna

Usługa Azure Virtual Network (VNet) to podstawowy blok konstrukcyjny dla sieci prywatnej na platformie Azure. Możesz użyć sieci wirtualnych, aby:
- **Komunikacja między zasobami platformy Azure**: możesz wdrożyć maszyny wirtualne i kilka innych typów zasobów platformy Azure w sieci wirtualnej, np. środowiskach Azure App Service Environment, usłudze Azure Kubernetes Service (AKS) oraz zestawach usługi Azure Virtual Machine Scale Sets. Aby wyświetlić kompletną listę zasobów platformy Azure, które można wdrożyć w sieci wirtualnej, zobacz [Integracja sieci wirtualnej z usługą](../virtual-network/virtual-network-for-azure-services.md).
- **Komunikuj się między sobą**: Sieci wirtualne możesz łączyć ze sobą, co umożliwi komunikację pomiędzy zasobami w każdej z sieci wirtualnych przy użyciu komunikacji równorzędnej sieci wirtualnych. Łączone sieci wirtualne mogą znajdować się w tym samym regionie lub w różnych regionach świadczenia usługi Azure. Aby uzyskać więcej informacji, zobacz [wirtualne sieci równorzędne](../virtual-network/virtual-network-peering-overview.md).
- **Komunikacja z Internetem**: Wszystkie zasoby w sieci wirtualnej mogą domyślnie komunikować się z Internetem. Z zasobem w ruchu przychodzącym możesz komunikować się przez przypisanie publicznego adresu IP lub publicznego modułu równoważenia obciążenia. Możesz również użyć [publicznych adresów IP](../virtual-network/virtual-network-public-ip-address.md) lub [Load Balancer](../load-balancer/load-balancer-overview.md) publicznych do zarządzania połączeniami wychodzącymi.
- **Komunikuj się z sieciami lokalnymi**: Lokalne komputery i sieci można połączyć z siecią wirtualną przy użyciu [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [ExpressRoute](../expressroute/expressroute-introduction.md).

Aby uzyskać więcej informacji, zobacz [co to jest platforma Azure Virtual Network?](../virtual-network/virtual-networks-overview.md).

### <a name="expressroute"></a>ExpressRoute
Usługa ExpressRoute umożliwia rozbudowa sieci lokalnych do chmury firmy Microsoft przez połączenie prywatne przez dostawcę połączenia. To połączenie jest prywatne. Ruch nie przechodzi przez Internet. Dzięki usłudze ExpressRoute można ustanowić połączenia z usługami Microsoft w chmurze, np. Microsoft Azure, Office 365 i Dynamics 365.  Aby uzyskać więcej informacji, zobacz [co to jest ExpressRoute?](../expressroute/expressroute-introduction.md).

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpngateway"></a>VPN Gateway
VPN Gateway ułatwia tworzenie szyfrowanych połączeń między różnymi lokalizacjami do sieci wirtualnej z lokalizacji lokalnych lub tworzenie szyfrowanych połączeń między sieci wirtualnych. Dostępne są różne konfiguracje połączeń VPN Gateway, takich jak lokacja, lokacja, punkt-lokacja lub Sieć wirtualna z siecią wirtualną.
Na poniższym diagramie przedstawiono wiele połączeń sieci VPN typu lokacja-lokacja z tą samą siecią wirtualną.

![Połączenia VPN Gateway platformy Azure między lokacjami](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

Aby uzyskać więcej informacji na temat różnych typów połączeń sieci VPN, zobacz [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="virtualwan"></a>Wirtualna sieć WAN
Wirtualna sieć WAN platformy Azure to usługa sieciowa, która zapewnia zoptymalizowaną i zautomatyzowaną łączność z gałęzią z usługami i i za pośrednictwem platformy Azure. Regiony platformy Azure służą jako centra, do których można połączyć gałęzie. Korzystając ze szkieletu platformy Azure, można również łączyć gałęzie i korzystać z połączeń między sieciami wirtualnymi. Wirtualna sieć WAN platformy Azure oferuje wiele usług łączności w chmurze platformy Azure, takich jak sieci VPN typu lokacja-lokacja, Sieć VPN typu punkt-lokacja w jednym interfejsie operacyjnym. Połączenie z usługą Azure sieci wirtualnych jest nawiązywane przy użyciu połączeń sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [co to jest Azure Virtual WAN?](../virtual-wan/virtual-wan-about.md).

![Diagram usługi Virtual WAN](./media/networking-overview/virtualwan1.png)

### <a name="dns"></a>Azure DNS
Azure DNS to usługa hostingowa przeznaczona dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury platformy Microsoft Azure. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure. Aby uzyskać więcej informacji, zobacz [co to jest Azure DNS?](../dns/dns-overview.md).

### <a name="bastion"></a>Azure bastionu (wersja zapoznawcza)
Usługa Azure bastionu to nowa w pełni oparta na platformie usługa PaaS, która jest dostarczana w ramach sieci wirtualnej. Zapewnia bezpieczną i bezproblemową łączność protokołu RDP/SSH z maszynami wirtualnymi bezpośrednio w Azure Portal za pośrednictwem protokołu SSL. Gdy łączysz się za pomocą usługi Azure Bastion, maszyny wirtualne nie muszą mieć publicznego adresu IP. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure bastionu?](../bastion/bastion-overview.md).

![Architektura usługi Azure bastionu](./media/networking-overview/architecture.png)


## <a name="protect"></a>Usługi ochrony aplikacji

W tej sekcji opisano usługi sieciowe na platformie Azure, które pomagają chronić zasoby sieciowe — DDoS Protection, zaporę aplikacji sieci Web, zaporę platformy Azure, sieciowe grupy zabezpieczeń i punkty końcowe usługi.

|Usługa|Dlaczego warto używać?|Scenariusz|
|---|---|---|
|[Ochrona DDoS](#ddosprotection) |Wysoka dostępność dla aplikacji z ochroną przed nadmiernymi opłatami za ruch IP|[Zarządzaj Azure DDoS Protection](../virtual-network/manage-ddos-protection.md)|
|[Zapora aplikacji sieci Web](#waf)|<p>[Usługa Azure WAF z Application Gateway](../application-gateway/waf-overview.md) zapewnia ochronę regionalną jednostek w publicznych i prywatnych przestrzeni adresowej</p><p>[Usługa Azure WAF z przednimi drzwiami](../frontdoor/waf-overview.md) zapewnia ochronę w granicach sieci z publicznymi punktami końcowymi.</p>|<p>[Konfigurowanie reguł ochrony bot](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[Konfigurowanie niestandardowego kodu odpowiedzi](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[Konfigurowanie reguł ograniczeń adresów IP](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[Konfigurowanie reguły limitu szybkości](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Zapora platformy Azure](#firewall)|Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network. Jest to w pełni stanowa Zapora jako usługa z wbudowaną wysoką dostępnością i nieograniczoną skalowalnością chmury.|<p>[Wdrażanie zapory platformy Azure w sieci wirtualnej](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[-Wdrażanie zapory platformy Azure w sieci hybrydowej](../firewall/tutorial-hybrid-ps.md)</p> <p>[Filtrowanie ruchu przychodzącego za pomocą usługi Azure firewall DNAT](../firewall/tutorial-firewall-dnat.md)</p>|
|[Sieciowe grupy zabezpieczeń](#nsg)|Pełny szczegółowy formant węzła końcowego rozproszonego na maszynie wirtualnej/podsieci dla wszystkich przepływów ruchu sieciowego|[Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)|
|[Punkty końcowe usługi dla sieci wirtualnej](#serviceendpoints)|Umożliwia ograniczenie dostępu sieciowego do niektórych zasobów usługi platformy Azure do podsieci sieci wirtualnej|[Ograniczanie dostępu sieciowego do zasobów PaaS](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
|||
### <a name="ddosprotection"></a>DDoS Protection 
[Azure DDoS Protection](../virtual-network/manage-ddos-protection.md) zapewnia środki zaradcze względem najbardziej zaawansowanych zagrożeń DDoS. Usługa zapewnia ulepszone możliwości ograniczania DDoS dla aplikacji i zasobów wdrożonych w sieciach wirtualnych. Ponadto klienci korzystający z usługi Azure DDoS Protection mają dostęp do DDoSej szybkiej odpowiedzi, aby skontaktować się z ekspertami DDoS podczas aktywnego ataku.

![DDoS Protection](./media/networking-overview/ddos-protection.png)

### <a name="waf"></a>Zapora aplikacji sieci Web

Zapora aplikacji sieci Web (WAF) platformy Azure zapewnia ochronę aplikacji sieci Web przed typowymi atakami sieci Web i lukami w zabezpieczeniach, takimi jak iniekcja SQL i wykonywanie skryptów między lokacjami. Usługa Azure WAF zapewnia ochronę poza Box od OWASP 10 najważniejszych luk w zabezpieczeniach za pośrednictwem reguł zarządzanych. Dodatkowo klienci mogą również konfigurować reguły niestandardowe, które są regułami zarządzanymi przez klienta, aby zapewnić dodatkową ochronę na podstawie źródłowego zakresu adresów IP, a także atrybuty żądania, takie jak nagłówki, pliki cookie, pola danych formularza lub parametry ciągu zapytania.

Klienci mogą wybrać wdrożenie [usługi Azure WAF z Application Gateway](../application-gateway/waf-overview.md) , która zapewnia ochronę regionalną jednostek w publicznej i prywatnej przestrzeni adresowej. Klienci mogą również wybrać wdrożenie [usługi Azure WAF z przednimi drzwiami](../frontdoor/waf-overview.md) , które zapewniają ochronę w granicach sieci z publicznymi punktami końcowymi.

![Zapora aplikacji internetowej](./media/networking-overview/waf-overview.png)


### <a name="firewall"></a>Zapora platformy Azure
Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network. Korzystając z zapory platformy Azure, można centralnie tworzyć, wymuszać i rejestrować zasady łączności aplikacji i sieci w ramach subskrypcji i sieci wirtualnych. Usługa Azure Firewall korzysta ze statycznego publicznego adresu IP dla zasobów sieci wirtualnej, co umożliwia zewnętrznym zaporom identyfikowanie ruchu pochodzącego z sieci wirtualnej. 

Aby uzyskać więcej informacji na temat zapory platformy Azure, zobacz [dokumentację dotyczącą zapory platformy Azure](../firewall/overview.md).

![Omówienie zapory](./media/networking-overview/firewall-threat.png)

### <a name="nsg"></a>Sieciowe grupy zabezpieczeń
Ruch sieciowy do i z zasobów platformy Azure można filtrować w sieci wirtualnej platformy Azure z sieciową grupą zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Omówienie zabezpieczeń](../virtual-network/security-overview.md).

### <a name="serviceendpoints"></a>Punkty końcowe usługi
Punkty końcowe usługi sieci wirtualnej rozszerzają prywatną przestrzeń adresową i tożsamość sieci wirtualnej do usług platformy Azure za pośrednictwem bezpośredniego połączenia. Punkty końcowe umożliwiają zabezpieczanie krytycznych zasobów usługi platformy Azure tylko do sieci wirtualnych. Ruch z sieci wirtualnej do usługi platformy Azure zawsze pozostaje w sieci szkieletowej platformy Microsoft Azure. Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md).

![Punkty końcowe usługi dla sieci wirtualnej](./media/networking-overview/vnet-service-endpoints-overview.png)

## <a name="deliver"></a>Usługi dostarczania aplikacji

W tej sekcji opisano usługi sieciowe na platformie Azure, które ułatwiają dostarczanie aplikacji — Content Delivery Network (CDN), usługi Azure front-drzwi, Traffic Manager, Application Gateway i Load Balancer.

|Usługa|Dlaczego warto używać?|Scenariusz|
|---|---|---|
|[Content Delivery Network](#cdn)|Dostarcza użytkownikom zawartość o wysokiej przepustowości. Sieci CDN Przechowuj zawartość pamięci podręcznej na serwerach brzegowych w lokalizacjach punktów obecności (POP), które znajdują się blisko użytkowników końcowych, aby zminimalizować opóźnienia|<p>[Dodawanie usługi CDN do aplikacji sieci Web](../cdn/cdn-add-to-web-app.md)</p> <p>[Dostęp do obiektów blob magazynu przy użyciu Azure CDN domeny niestandardowej za pośrednictwem protokołu HTTPS](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[Dodawanie domeny niestandardowej do punktu końcowego Azure CDN](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[Konfigurowanie protokołu HTTPS w domenie niestandardowej usługi Azure CDN](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate)</p>|
|[Usługa frontonu platformy Azure](#frontdoor)|Umożliwia definiowanie i monitorowanie globalnego routingu dla ruchu w sieci Web oraz zarządzanie nim przez optymalizację w celu uzyskania najlepszej wydajności i natychmiastowego globalnego trybu failover w celu zapewnienia wysokiej dostępności.|<p>[Dodaj domenę niestandardową do usługi Azure Front drzwiczk](../frontdoor/front-door-custom-domain.md)</p> <p>[Konfigurowanie protokołu HTTPS w domenie niestandardowej drzwi zewnętrznych](../frontdoor/front-door-custom-domain-https.md)</p><p>[Konfigurowanie zasad zapory aplikacji sieci Web do filtrowania geograficznego](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[Traffic Manager](#trafficmanager)|Dystrybuuje ruch oparty na systemie DNS do usług w globalnych regionach platformy Azure, zapewniając wysoką dostępność i czas odpowiedzi|<p> [Kierowanie ruchem w celu uzyskania niskich opóźnień](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[Kierowanie ruchu do priorytetowego punktu końcowego](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [Kontrolowanie ruchu za pomocą punktów końcowych z wagami](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[Kierowanie ruchu na podstawie lokalizacji geograficznej punktu końcowego](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [Kierowanie ruchem na podstawie podsieci użytkownika](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[Load Balancer](#loadbalancer)|Zapewnia regionalne Równoważenie obciążenia przez kierowanie ruchu między strefami dostępności i sieci wirtualnych. Zapewnia wewnętrzne równoważenie obciążenia przez kierowanie ruchu między zasobami i między nimi w celu skompilowania aplikacji regionalnej.|<p> [Równoważenie obciążenia ruchem internetowym przez skierowanie go do maszyn wirtualnych](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[Równoważenie obciążenia ruchu między maszynami wirtualnymi w sieci wirtualnej](../load-balancer/tutorial-load-balancer-basic-internal-portal.md)<p>[Ruch przekazujący port do określonego portu na określonych maszynach wirtualnych](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [Konfigurowanie równoważenia obciążenia i reguł ruchu wychodzącego](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[Application Gateway](#applicationgateway)|Azure Application Gateway to moduł równoważenia obciążenia ruchu internetowego, który umożliwia zarządzanie ruchem kierowanym do aplikacji internetowych.|<p>[Bezpośredni ruch internetowy za pomocą usługi Azure Application Gateway](../application-gateway/quick-create-portal.md)</p><p>[Konfigurowanie bramy aplikacji z terminacją SSL](../application-gateway/create-ssl-portal.md)</p><p>[Tworzenie bramy aplikacji z przekierowywaniem na podstawie ścieżki URL](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="cdn"></a>Content Delivery Network
Usługa Azure Content Delivery Network (CDN) oferuje deweloperom globalne rozwiązanie umożliwiające szybkie dostarczanie użytkownikom zawartości wymagającej wysokiej przepustowości przez zapisywanie zawartości w pamięci podręcznej w węzłach fizycznych strategicznie rozmieszczonych na całym świecie. Aby uzyskać więcej informacji na temat Azure CDN, zobacz [Azure Content Delivery Network](../cdn/cdn-overview.md)

![Azure CDN](./media/networking-overview/cdn-overview.png)

### <a name="frontdoor"></a>Usługa frontonu platformy Azure
Usługa Azure Front Door Service umożliwia definiowanie i monitorowanie globalnego routingu ruchu internetowego oraz zarządzanie nim przez optymalizowanie pod kątem uzyskiwania najlepszej wydajności i natychmiastowego przechodzenia w tryb failover w celu zapewnienia wysokiej dostępności. Dzięki usłudze Front Door można przekształcić globalne usługi dla konsumentów (wiele regionów) i aplikacji w niezawodne, spersonalizowane, nowoczesne, wysokowydajne aplikacje, interfejsy API i zawartość, które dotrą do odbiorców globalnych za pomocą platformy Azure. Aby uzyskać więcej informacji, zobacz [zewnętrzne drzwi platformy Azure](../frontdoor/front-door-overview.md).


### <a name="trafficmanager"></a>Traffic Manager

Usługa Azure Traffic Manager to oparty na systemie DNS moduł równoważenia obciążenia ruchu, który umożliwia optymalną dystrybucję ruchu do usług w wielu regionach platformy Azure na świecie, przy jednoczesnym zapewnieniu wysokiej dostępności i krótkiego czasu odpowiedzi. Traffic Manager zapewnia szereg metod routingu ruchu w celu dystrybucji ruchu, takiego jak priorytet, waga, wydajność, geograficzna, wiele wartości lub podsieć. Aby uzyskać więcej informacji o metodach routingu ruchu, zobacz [Traffic Manager metod routingu](../traffic-manager/traffic-manager-routing-methods.md).

Na poniższym diagramie przedstawiono routing oparty na priorytetach punktów końcowych z Traffic Manager:

![Usługa Azure Traffic Manager "Priority" — Metoda routingu](./media/networking-overview/priority.png)

Aby uzyskać więcej informacji na temat Traffic Manager, zobacz [co to jest usługa Azure Traffic Manager?](../traffic-manager/traffic-manager-overview.md)

### <a name="loadbalancer"></a>Load Balancer
Azure Load Balancer w przypadku wszystkich protokołów UDP i TCP zapewnia wysoką wydajność i niskie opóźnienie warstwy 4. Służy do zarządzania połączeniami przychodzącymi i wychodzącymi. Można skonfigurować publiczne i wewnętrzne punkty końcowe ze zrównoważonym obciążeniem. Można zdefiniować reguły mapowania połączeń przychodzących do miejsc docelowych puli zaplecza przy użyciu opcji badania kondycji protokołów TCP i HTTP w celu zarządzania dostępnością usługi. Aby dowiedzieć się więcej na temat Load Balancer, przeczytaj artykuł [omówienie Load Balancer](../load-balancer/load-balancer-overview.md) .

Na poniższej ilustracji przedstawiono wielowarstwową aplikację internetową, która wykorzystuje zarówno zewnętrzne, jak i wewnętrzne moduły równoważenia obciążenia:

![Przykład Azure Load Balancer](./media/networking-overview/IC744147.png)


### <a name="applicationgateway"></a>Application Gateway
Azure Application Gateway to moduł równoważenia obciążenia ruchu internetowego, który umożliwia zarządzanie ruchem kierowanym do aplikacji internetowych. Jest to kontroler dostarczania aplikacji (ADC) jako usługa, oferując różne możliwości równoważenia obciążenia warstwy 7 dla aplikacji. Aby uzyskać więcej informacji, zobacz [co to jest platforma Azure Application Gateway?](../application-gateway/overview.md).

Na poniższym diagramie przedstawiono routing oparty na ścieżce URL przy użyciu Application Gateway.

![Przykład Application Gateway](./media/networking-overview/figure1-720.png)

## <a name="monitor"></a>Usługi monitorowania sieci
W tej sekcji opisano usługi sieciowe na platformie Azure, które ułatwiają monitorowanie zasobów sieciowych — Network Watcher, monitor ExpressRoute, Azure Monitor i Virtual Network.

|Usługa|Dlaczego warto używać?|Scenariusz|
|---|---|---|
|[Network Watcher](#networkwatcher)|Pomaga monitorować i rozwiązywać problemy z łącznością, pomaga zdiagnozować problemy z siecią VPN, sieciowej grupy zabezpieczeń i routingu, przechwycić pakiety na maszynie wirtualnej, automatyzując diagnostykę narzędzi diagnostycznych przy użyciu Azure Functions i Logic Apps|<p>[Diagnozowanie problemu z filtrem ruchu maszyn wirtualnych](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[Diagnozowanie problemu z routingiem maszyny wirtualnej](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[Monitorowanie komunikacji między maszynami wirtualnymi](../network-watcher/connection-monitor.md)</p><p>[Diagnozowanie problemów z komunikacją między sieciami](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[Rejestrowanie ruchu sieciowego do i z maszyny wirtualnej](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[ExpressRoute Monitor](#expressroutemonitor)|Zapewnia monitorowanie wydajności, dostępności i wykorzystania sieci w czasie rzeczywistym, ułatwia odnajdywanie topologii sieci, zapewnia szybszą izolację błędów, wykrywa przejściowe problemy z siecią i pozwala analizować historyczną wydajność sieci. cechy, które obsługują wiele subskrypcji|<p>[Konfigurowanie monitora wydajności sieci dla usługi ExpressRoute](../expressroute/how-to-npm.md)</p><p>[ExpressRoute monitorowanie, metryki i alerty](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure Monitor](#azuremonitor)|Pomaga zrozumieć, w jaki sposób aplikacje są wykonywane i aktywnie identyfikują problemy wpływające na nie i zasoby, od których zależą.|<p>[Traffic Manager metryki i alerty](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[Diagnostyka usługi Azure monitor dla usługa Load Balancer w warstwie Standardowa](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[Monitorowanie dzienników i metryk zapory platformy Azure](../firewall/tutorial-diagnostics.md)</p><p>[Monitorowanie i rejestrowanie zapory aplikacji internetowej platformy Azure](../frontdoor/waf-front-door-monitor.md)</p>|
|[Virtual Network naciśnij pozycję](#vnettap)|Zapewnia stałe przesyłanie strumieniowe ruchu sieciowego maszyn wirtualnych do modułu zbierającego pakiety, a także oferuje rozwiązania do zarządzania wydajnością sieci i aplikacji oraz narzędzia analizy zabezpieczeń|[Tworzenie zasobu sieci wirtualnej](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="networkwatcher"></a>Network Watcher
Azure Network Watcher to usługa, która udostępnia narzędzia umożliwiające monitorowanie, diagnozowanie, wyświetlanie metryk i włączanie lub wyłączanie dzienników zasobów w sieci wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [co to jest Network Watcher?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="expressroutemonitor"></a>Monitor ExpressRoute
Aby dowiedzieć się więcej na temat wyświetlania metryk obwodu ExpressRoute, dzienników diagnostycznych i alertów, zobacz [ExpressRoute monitoring, Metrics and Alerts](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="azuremonitor"></a>Azure Monitor
Azure Monitor maksymalizuje dostępność i wydajność aplikacji, udostępniając kompleksowe rozwiązanie do zbierania, analizowania i działania na telemetrii z chmur i środowisk lokalnych. Pomaga interpretować działanie aplikacji i proaktywnie identyfikuje problemy dotyczące aplikacji i zasobów, od których zależą. Aby uzyskać więcej informacji, zobacz [Azure monitor przegląd](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="vnettap"></a>Virtual Network naciśnij pozycję
Usługa Azure Virtual Network TAP (punkt dostępu terminalu) umożliwia ciągłą przesyłanie strumieniowego ruchu sieciowego maszyny wirtualnej do narzędzia do zbierania pakietów sieciowych lub narzędzi analitycznych. Moduł zbierający lub narzędzie analityczne jest dostarczany przez partnera [wirtualnego urządzenia sieciowego](https://azure.microsoft.com/solutions/network-appliances/) . 

Na poniższej ilustracji przedstawiono sposób działania narzędzia Virtual Network TAP. 

![Jak działa NACIŚNIĘCIe sieci wirtualnej](./media/networking-overview/virtual-network-tap-architecture.png)

Aby uzyskać więcej informacji, zobacz [co to jest Virtual Network TAP](../virtual-network/virtual-network-tap-overview.md).

## <a name="next-steps"></a>Kolejne kroki

- Utwórz pierwszą sieć wirtualną i Połącz z nim kilka maszyn wirtualnych, wykonując kroki opisane w artykule [Tworzenie pierwszej sieci wirtualnej](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) .
- Połącz komputer z siecią wirtualną, wykonując kroki opisane w [artykule Konfigurowanie połączenia punkt-lokacja](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Równoważenie obciążenia ruchu internetowego do serwerów publicznych przez wykonanie kroków opisanych w artykule [Tworzenie modułu równoważenia obciążenia](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) dostępnego z Internetu.
 
 
   
