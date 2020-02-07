---
title: 'Architektura: Globalna architektura sieci tranzytowej'
titleSuffix: Azure Virtual WAN
description: Dowiedz się więcej o globalnej architekturze sieci tranzytowej dla wirtualnej sieci WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 17d0e678008c76da32f20562aa795e83e49c80e4
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064975"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Globalna architektura sieci tranzytowej i wirtualna sieć WAN

Nowoczesne przedsiębiorstwa wymagają powszechnej łączności między aplikacjami rozproszonymi, danymi i użytkownikami w chmurze i lokalnymi. Globalna architektura sieci tranzytowej jest wdrażana przez przedsiębiorstwa do konsolidowania, łączenia i kontrolowania nowoczesnego, globalnego przedsiębiorstwa opartego na chmurze.

Globalna architektura sieci tranzytowej jest oparta na klasycznym modelu łączności typu Hub i satelity, w którym sieć hostowana w chmurze "Hub" umożliwia przechodnią łączność między punktami końcowymi, które mogą być dystrybuowane w różnych typach "szprych".

W tym modelu szprychą może być:
* Sieć wirtualna (sieci wirtualnych)
* Lokacja fizyczna
* Użytkownik zdalny
* Internet

![koncentrator i szprycha](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**Rysunek 1: globalna sieć piasty i satelity**

Rysunek 1 przedstawia widok logiczny globalnej sieci tranzytowej, w którym użytkownicy rozproszona geograficznie, lokacje fizyczne i sieci wirtualnych są współłączona za pośrednictwem Centrum sieciowego hostowanego w chmurze. Ta architektura umożliwia logiczne połączenie z jednym przeskokiem między punktami końcowymi sieci.

## <a name="globalnetworktransit"></a>Globalna sieć tranzytowa z wirtualną siecią WAN

Wirtualna sieć WAN platformy Azure to usługa sieci w chmurze zarządzana przez firmę Microsoft. Wszystkie składniki sieci, które składają się na tę usługę, są hostowane i zarządzane przez firmę Microsoft. Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz artykuł [Omówienie wirtualnych sieci WAN](virtual-wan-about.md) .

Wirtualna sieć WAN platformy Azure umożliwia globalną architekturę sieci tranzytowej przez umożliwienie powszechnej łączności między globalnie rozproszonymi zbiorami obciążeń w chmurze w sieci wirtualnych, oddziałach, aplikacjach SaaS i PaaS oraz użytkowników.

![Wirtualna sieć WAN platformy Azure](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**Rysunek 2: globalne sieci tranzytowe i wirtualna sieć WAN**

W architekturze sieci wirtualnej platformy Azure wirtualne centra sieci WAN są inicjowane w regionach platformy Azure, w których można wybrać połączenie z gałęziami, sieci wirtualnych i użytkownikami zdalnymi. Fizyczne Lokacje oddziałów są połączone z centrum za pomocą ExpressRoute Premium lub lokacji-VPN, sieci wirtualnych są połączone z koncentratorem przez połączenia sieci wirtualnej, a użytkownicy zdalni mogą łączyć się bezpośrednio z centrum przy użyciu sieci VPN użytkownika (VPN punkt-lokacja). Wirtualna sieć WAN obsługuje również międzyregionowe połączenie sieci wirtualnej, w którym sieć wirtualna w jednym regionie może być podłączona do koncentratora wirtualnego sieci WAN w innym regionie.

Można nawiązać wirtualną sieć WAN, tworząc pojedyncze wirtualne koncentratory sieci WAN w regionie, który ma największą liczbę szprych (gałęzie, sieci wirtualnych, użytkowników), a następnie łącząc szprychy, które znajdują się w innych regionach z koncentratorem. Jest to dobrym rozwiązaniem, gdy powierzchnia przedsiębiorstwa jest w większości w jednym regionie, z kilkoma zdalnymi szprychami.  
  
## <a name="hubtohub"></a>Łączność między centrami

Obszar chmury przedsiębiorstwa może obejmować wiele regionów chmur i jest optymalny (opóźnienia), aby uzyskać dostęp do chmury z regionu znajdującego się najbliżej ich fizycznej witryny i użytkowników. Jedną z najważniejszych zasad globalnej architektury sieci tranzytowej jest włączenie łączności między regionami i punktami końcowymi sieci lokalnych. Oznacza to, że ruch z gałęzi, która jest połączona z chmurą w jednym regionie, może należeć do innej gałęzi lub sieci wirtualnej w innym regionie przy użyciu połączenia typu Hub-do-Hub włączonego przez [Sieć globalną platformy Azure](https://azure.microsoft.com/global-infrastructure/global-network/).

![między regionami](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**Rysunek 3. wirtualna komunikacja między regionami w sieci WAN**

Jeśli w jednej wirtualnej sieci WAN włączono wiele centrów, koncentratory są automatycznie dołączane za pośrednictwem linków między centrami, co umożliwia globalne połączenie między gałęziami i sieci wirtualnychami rozproszonymi w wielu regionach. 

Ponadto centra, które są częścią tej samej wirtualnej sieci WAN, mogą być skojarzone z różnymi regionalnymi zasadami dostępu i zabezpieczeń. Aby uzyskać więcej informacji, zobacz [zabezpieczenia i kontrola zasad](#security) w dalszej części tego artykułu.

## <a name="anytoany"></a>Łączność dowolna-do-dowolna

Globalna architektura sieci tranzytowej umożliwia korzystanie z dowolnej łączności za pośrednictwem wirtualnych centrów sieci WAN. Ta architektura eliminuje lub zmniejsza potrzebę obsługi pełnej siatki lub częściowej łączności między szprychami, które są bardziej skomplikowane do kompilowania i konserwowania. Ponadto w celu łatwiejszego konfigurowania i konserwowania kontroli routingu w sieciach typu Hub i szprych.

Dowolna z połączeń (w kontekście globalnej architektury) umożliwia przedsiębiorstwom globalnie rozproszonym użytkownikom, rozgałęzień, centrów danych, sieci wirtualnych i aplikacji łączenie się ze sobą za pośrednictwem centrów "przesyłania". Wirtualna sieć WAN platformy Azure pełni rolę globalnego systemu tranzytowego.

![dowolny do dowolnego](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**Rysunek 4. ścieżki ruchu wirtualnego sieci WAN**

Wirtualna sieć WAN platformy Azure obsługuje następujące globalne ścieżki połączeń tranzytowych. Litery w nawiasach są mapowane na rysunek 4.

* Odgałęzienie do sieci wirtualnej (a)
* Rozgałęzienie (b)
  * ExpressRoute Global Reach i wirtualna sieć WAN
* Zdalne użytkownika do sieci wirtualnej (c)
* Zdalny użytkownik do rozgałęzienia (d)
* Sieć wirtualna-sieć wirtualna (e)
* Gałąź-do-Hub-Hub (f)
* Gałąź-do-Hub — piasta-Sieć wirtualna (g)
* Sieć wirtualna-sieć-piasta (h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>Rozgałęzienie między sieciami wirtualnymi (a) i gałęzią między różnymi regionami (g)

Gałąź do sieci wirtualnej jest ścieżką podstawową obsługiwaną przez wirtualną sieć WAN platformy Azure. Ta ścieżka umożliwia łączenie gałęzi z obciążeniami platformy Azure IAAS Enterprise, które są wdrożone w usłudze Azure sieci wirtualnych. Gałęzie mogą być połączone z wirtualną siecią WAN za pośrednictwem ExpressRoute lub sieci VPN typu lokacja-lokacja. Ruch do sieci wirtualnych podłączony do wirtualnych koncentratorów sieci WAN za pośrednictwem połączeń VNet. Jawna [tranzyt bramy](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) nie jest wymagana dla wirtualnej sieci WAN, ponieważ wirtualna sieć WAN automatycznie włącza tranzyt bramy do lokacji oddziału. Zobacz artykuł dotyczący [partnerów wirtualnych sieci WAN](virtual-wan-configure-automation-providers.md) , w jaki sposób połączyć urządzenie z systemem SD-WAN z wirtualną siecią WAN.

### <a name="expressroute-global-reach-and-virtual-wan"></a>ExpressRoute Global Reach i wirtualna sieć WAN

ExpressRoute to prywatny i odporny na połączenie sieci lokalnych z Microsoft Cloud. Wirtualna sieć WAN obsługuje połączenia obwodów tras Express. Połączenie lokacji rozgałęzienia z wirtualną siecią WAN przy użyciu trasy Express wymaga 1) obwodu 2), który ma być w Global Reach lokalizacji.

ExpressRoute Global Reach to funkcja dodatku dla ExpressRoute. Za pomocą Global Reach można połączyć obwody usługi ExpressRoute w celu naprowadzenia prywatnej sieci między sieciami lokalnymi. Gałęzie, które są połączone z wirtualną siecią WAN platformy Azure za pomocą ExpressRoute, wymagają, aby Global Reach ExpressRoute się ze sobą.

W tym modelu każda gałąź, która jest połączona z wirtualnym koncentratorem sieci WAN przy użyciu programu ExpressRoute, może łączyć się z sieci wirtualnych przy użyciu ścieżki gałęzi do sieci wirtualnej. Ruch rozgałęzienia do gałęzi nie będzie przenoszony do centrum, ponieważ ExpressRoute Global Reach zapewnia bardziej optymalną ścieżkę w sieci WAN platformy Azure.

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>Rozgałęzienie (b) i odgałęzienie między regionami (f)

Gałęzie mogą być połączone z koncentratorem wirtualnej sieci WAN platformy Azure przy użyciu obwodów usługi ExpressRoute i/lub połączeń sieci VPN typu lokacja-lokacja. Gałęzie można połączyć z koncentratorem wirtualnej sieci WAN, który znajduje się w regionie najbliższym gałęzi.

Ta opcja umożliwia przedsiębiorstwom wykorzystanie szkieletu platformy Azure do łączenia gałęzi. Jednak mimo że ta funkcja jest dostępna, należy zastanowić się nad korzyściami z łączenia gałęzi za pośrednictwem wirtualnej sieci WAN platformy Azure a korzystanie z prywatnej sieci WAN.  

### <a name="remote-user-to-vnet-c"></a>Zdalne użytkownika do sieci wirtualnej (c)

Możesz włączyć bezpośredni i bezpieczny dostęp zdalny do platformy Azure przy użyciu połączenia typu punkt-lokacja z zdalnego klienta użytkownika z wirtualną siecią WAN. Użytkownicy zdalni przedsiębiorstwa nie muszą już hairpin do chmury przy użyciu firmowej sieci VPN.

### <a name="remote-user-to-branch-d"></a>Zdalny użytkownik do rozgałęzienia (d)

Zdalna ścieżka użytkownika do gałęzi umożliwia użytkownikom zdalnym, którzy korzystają z połączenia typu punkt-lokacja z lokalnymi obciążeniami i aplikacjami dostępnymi na platformie Azure przez przeprowadzenie przesyłania danych przez chmurę. Ta ścieżka zapewnia użytkownikom zdalnym elastyczność dostępu do obciążeń, które są wdrażane na platformie Azure i lokalnie. Przedsiębiorstwa mogą włączyć centralną, opartą na chmurze usługę bezpiecznego dostępu zdalnego w wirtualnej sieci WAN platformy Azure.

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>Tranzyt między sieciami wirtualnymi (e) i między różnymi regionami między sieciami wirtualnymi (h)

Tranzyt między sieciami wirtualnymi umożliwia sieci wirtualnych łączenie się ze sobą w celu łączenia aplikacji wielowarstwowych wdrożonych w wielu sieci wirtualnychach. Opcjonalnie można połączyć sieci wirtualnych ze sobą za pośrednictwem komunikacji równorzędnej sieci wirtualnych, co może być odpowiednie w przypadku niektórych scenariuszy, w których przesyłanie za pośrednictwem centrum VWAN nie jest konieczne.

## <a name="security"></a>Zabezpieczenia i kontrola zasad

Wirtualne centra sieci wirtualnej platformy Azure łączą wszystkie punkty końcowe sieci w sieci hybrydowej i potencjalnie widzą cały ruch w sieci tranzytowej. Wirtualne centra sieci WAN można przekonwertować na zabezpieczone centra wirtualne, wdrażając zaporę platformy Azure wewnątrz centrów VWAN w celu włączenia zabezpieczeń, dostępu i kontroli zasad opartych na chmurze. Aranżacja zapór platformy Azure w wirtualnych centrach sieci WAN może być wykonywana przez Menedżera zapory platformy Azure.

[Menedżer zapory platformy Azure](https://go.microsoft.com/fwlink/?linkid=2107683) oferuje funkcje umożliwiające zarządzanie zabezpieczeniami globalnymi sieciami tranzytowymi i skalowanie ich. Menedżer zapory platformy Azure umożliwia centralne zarządzanie routingiem, globalnym zarządzaniem zasadami, zaawansowanymi usługami zabezpieczeń internetowych za pośrednictwem innych firm wraz z zaporą platformy Azure.

![bezpieczny koncentrator wirtualny z zaporą platformy Azure](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**Rysunek 5. zabezpieczone centrum wirtualne za pomocą zapory platformy Azure**

Zapora platformy Azure z wirtualną siecią WAN obsługuje następujące ścieżki połączeń zabezpieczonych globalnie. Litery w nawiasach są mapowane na rysunek 5.

* Bezpieczne tranzyt połączeń między sieciami wirtualnymi (e)
* Usługa zabezpieczeń sieci wirtualnej lub innej firmy (i)
* Odgałęzienie do Internetu lub usługa zabezpieczeń innych firm (j)

### <a name="vnet-to-vnet-secured-transit-e"></a>Zabezpieczone tranzyt między sieciami wirtualnymi (e)

Zabezpieczone tranzyt między sieciami wirtualnymi umożliwia sieci wirtualnych łączenie się ze sobą za pośrednictwem zapory platformy Azure w ramach wirtualnego koncentratora WAN.

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>Usługa zabezpieczeń sieci wirtualnej lub innej firmy (i)

Połączenie między sieciami wirtualnymi a transportem zewnętrznym umożliwia usłudze sieci wirtualnych łączenie się z Internetem lub obsługiwane usługi zabezpieczeń innych firm za pośrednictwem zapory platformy Azure w wirtualnej sieci WAN.

### <a name="branch-to-internet-or-third-party-security-service-j"></a>Odgałęzienie do Internetu lub usługa zabezpieczeń innych firm (j)
Odgałęzienie do Internetu lub zabezpieczone tranzyt innych firm umożliwia rozgałęzienia do łączenia się z Internetem lub obsługiwanych usług zabezpieczeń innych firm za pośrednictwem zapory platformy Azure w wirtualnym koncentratorze sieci WAN.

## <a name="next-steps"></a>Następne kroki

Utwórz połączenie za pomocą wirtualnej sieci WAN i Wdróż zaporę platformy Azure w centrach VWAN.

* [Połączenia między lokacjami przy użyciu wirtualnej sieci WAN](virtual-wan-site-to-site-portal.md)
* [ExpressRoute połączeń przy użyciu wirtualnej sieci WAN](virtual-wan-expressroute-portal.md)
* [Menedżer zapory platformy Azure do wdrażania usługi Azure PD w VWAN](https://go.microsoft.com/fwlink/?linkid=2107683)
