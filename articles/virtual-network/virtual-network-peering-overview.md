---
title: Wirtualne sieci równorzędne platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się więcej na temat wirtualnych sieci równorzędnych na platformie Azure.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: jdial
ms.openlocfilehash: 1085be57583762e8f1d419e2aacc699032340d90
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54054558"
---
# <a name="virtual-network-peering"></a>Wirtualne sieci równorzędne

Komunikacja równorzędna między sieciami wirtualnymi umożliwia bezproblemowe połączenie dwóch [sieci wirtualnych](virtual-networks-overview.md) platformy Azure. Po nawiązaniu połączenia równorzędnego sieci wirtualne są traktowane jako jedna sieć. Ruch między maszynami wirtualnymi w wirtualnych sieciach równorzędnych odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft — tak jak ruch między maszynami wirtualnymi w tej samej sieci wirtualnej tylko za pośrednictwem *prywatnych* adresów IP. Obsługiwane przez platformę Azure:
* Wirtualne sieci równorzędne — łączenie sieci wirtualnych w tym samym regionie świadczenia usługi Azure
* Globalne wirtualne sieci równorzędne — łączenie sieci wirtualnych w różnych regionach świadczenia usługi Azure

Korzystanie z wirtualnych sieci równorzędnych, lokalnych lub globalnych, zapewnia m.in. następujące korzyści:

* Ruch sieciowy między wirtualnymi sieciami równorzędnymi jest prywatny. Ruch między sieciami wirtualnymi jest utrzymywany w sieci szkieletowej firmy Microsoft. Do komunikacji między sieciami wirtualnymi nie jest wymagany publiczny Internet, bramy ani szyfrowanie.
* Połączenie o małych opóźnieniach i dużej przepustowości między zasobami w różnych sieciach wirtualnych.
* Możliwość komunikacji zasobów w jednej sieci wirtualnej z zasobami w innej sieci wirtualnej po równorzędnym połączeniu sieci wirtualnych.
* Możliwość przesyłania danych między subskrypcjami platformy Azure, modelami wdrażania i regionami platformy Azure.
* Możliwość połączenia sieci wirtualnych utworzonych przy użyciu usługi Azure Resource Manager lub połączenia jednej sieci wirtualnej utworzonej za pomocą usługi Resource Manager z drugą siecią wirtualną utworzoną za pomocą klasycznego modelu wdrażania. Aby dowiedzieć się więcej na temat modeli wdrażania platformy Azure, zapoznaj się z artykułem [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Informacje na temat modeli wdrażania platformy Azure).
* Brak przestojów zasobów w sieci wirtualnej podczas tworzenia komunikacji równorzędnej albo po utworzeniu komunikacji równorzędnej.

## <a name="connectivity"></a>Łączność

Po połączeniu sieci wirtualnych za pomocą komunikacji równorzędnej zasoby w dowolnej z tych sieci wirtualnych mogą łączyć się bezpośrednio z innymi zasobami w równorzędnej sieci wirtualnej.

Opóźnienie sieciowe między maszynami wirtualnymi w równorzędnych sieciach wirtualnych w tym samym regionie jest takie samo jak opóźnienie w pojedynczej sieci wirtualnej. Przepływność sieci zależy od przepustowości dozwolonej dla maszyny wirtualnej proporcjonalnie do jej rozmiaru. Nie ma żadnych dodatkowych ograniczeń przepustowości w obrębie komunikacji równorzędnej.

Ruch między maszynami wirtualnymi w równorzędnych sieciach wirtualnych odbywa się bezpośrednio za pomocą infrastruktury sieci szkieletowej firmy Microsoft, a nie przy użyciu bramy lub publicznego Internetu.

Sieciowe grupy zabezpieczeń można stosować w dowolnej sieci wirtualnej, aby w razie potrzeby zablokować dostęp do innych sieci wirtualnych lub podsieci.
Podczas konfigurowania wirtualnych sieci równorzędnych można otwierać i zamykać reguły grupy zabezpieczeń sieci między sieciami wirtualnymi. W przypadku otwarcia pełnej łączności między równorzędnymi sieciami wirtualnymi (jest to opcja domyślna) można zablokować lub odmówić określonego rodzaju dostępu do określonych podsieci lub maszyn wirtualnych. Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [Network security groups overview (Omówienie sieciowych grup zabezpieczeń)](security-overview.md).

## <a name="service-chaining"></a>Tworzenie łańcuchów usług

Użytkownicy mogą konfigurować trasy zdefiniowane przez użytkownika prowadzące do maszyn wirtualnych w wirtualnych sieciach równorzędnych (jako adresy IP *kolejnego przeskoku*) lub do bram sieci wirtualnych, aby umożliwić tworzenie łańcucha usług. Tworzenie łańcucha usług umożliwia bezpośrednie kierowanie ruchu z jednej sieci wirtualnej do urządzenia wirtualnego lub do bramy sieci wirtualnej w wirtualnej sieci równorzędnej przy użyciu tras zdefiniowanych przez użytkownika.

Można również wdrażać sieci typu gwiazdy, w których centralna sieć wirtualna może hostować składniki infrastruktury, takie jak sieciowe urządzenie wirtualne lub brama sieci VPN. Następnie wszystkie sieci wirtualne typu gwiazda można połączyć za pomocą komunikacji równorzędnej z centralną siecią wirtualną. Ruch może przepływać za pośrednictwem wirtualnych urządzeń sieciowych lub bram sieci VPN działających w centralnej sieci wirtualnej. 

Wirtualne sieci równorzędne umożliwiają użycie kolejnego przeskoku w trasie zdefiniowanej przez użytkownika jako adresu IP maszyny wirtualnej w wirtualnej sieci równorzędnej lub bramie sieci VPN. Nie można jednak wyznaczać trasy między sieciami wirtualnymi w trasie zdefiniowanej przez użytkownika, określając bramę usługi ExpressRoute jako typ następnego przeskoku. Aby dowiedzieć się więcej o trasach definiowanych przez użytkownika, zobacz [User-defined routes overview](virtual-networks-udr-overview.md#user-defined) (Omówienie tras definiowanych przez użytkownika). Aby dowiedzieć się, jak utworzyć topologię sieci typu gwiazda, zobacz [Hub and spoke network topology (Topologia sieci typu gwiazda)](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Bramy i łączność lokalna

Każda sieć wirtualna — niezależnie od tego, czy jest połączona za pomocą komunikacji równorzędnej z inną siecią wirtualną — może mieć własną bramę i używać jej do łączenia się z lokalną infrastrukturą sieciową. Użytkownicy mogą również konfigurować [połączenia między sieciami wirtualnymi](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) przy użyciu bram, nawet jeśli te sieci wirtualne zostały połączone za pomocą komunikacji równorzędnej.

Po skonfigurowaniu obu opcji łączności między sieciami wirtualnymi ruch między tymi sieciami wirtualnymi jest oparty na konfiguracji komunikacji równorzędnej (to znaczy odbywa się za pośrednictwem sieci szkieletowej platformy Azure).

Gdy sieci wirtualne są połączone za pomocą komunikacji równorzędnej w tym samym regionie, użytkownicy mogą również skonfigurować bramę w wirtualnej sieci równorzędnej jako punkt tranzytowy do infrastruktury lokalnej. W tym przypadku sieć wirtualna korzystająca z bramy zdalnej nie może mieć własnej bramy. Jedna sieć wirtualna może mieć tylko jedną bramę. Brama może być lokalna lub zdalna (w wirtualnej sieci równorzędnej), jak przedstawiono na poniższej ilustracji:

![przesyłanie w równorzędnych sieciach wirtualnych](./media/virtual-networks-peering-overview/figure04.png)

Przesyłanie danych za pomocą bramy nie jest obsługiwane w relacji komunikacji równorzędnej między sieciami wirtualnymi utworzonymi w różnych regionach. Aby przesyłanie danych za pomocą bramy działało, obie sieci wirtualne będące w relacji komunikacji równorzędnej muszą znajdować się w tym samym regionie. Przesyłanie danych za pomocą bramy między sieciami wirtualnym utworzonymi przy użyciu różnych modeli wdrażania (klasycznego i za pomocą usługi Resource Manager) jest obsługiwane tylko wtedy, gdy brama znajduje się w sieci wirtualnej (Resource Manager). Aby dowiedzieć się więcej na temat przesyłania danych za pomocą bramy, zobacz [Konfigurowanie bramy sieci VPN na potrzeby przesyłania danych w komunikacji równorzędnej sieci wirtualnej](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

W przypadku połączenia za pomocą komunikacji równorzędnej sieci wirtualnych współużytkujących jedno połączenie sługi Azure ExpressRoute ruch między nimi jest oparty na relacji komunikacji równorzędnej (to znaczy odbywa się za pośrednictwem sieci szkieletowej platformy Azure). Użytkownicy mogą nadal korzystać z bram lokalnych w poszczególnych sieciach wirtualnych, aby łączyć się z obwodem lokalnym. Można również użyć bramy współdzielonej i skonfigurować tranzyt dla łączności lokalnej.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Aby potwierdzić komunikację równorzędną w sieci wirtualnej, możesz [sprawdzić efektywne trasy](diagnose-network-routing-problem.md) dla interfejsu sieciowego w dowolnej podsieci w sieci wirtualnej. Jeśli istnieje komunikacja równorzędna w sieci wirtualnej, wszystkie podsieci w tej sieci wirtualnej mają trasy z typem następnego przeskoku *Komunikacja równorzędna sieci wirtualnych* dla każdej przestrzeni adresowej w każdej równorzędnej sieci wirtualnej.

Problemy dotyczące łączności z maszyną wirtualną w równorzędnej sieci wirtualnej można również rozwiązywać przy użyciu funkcji [monitorowania połączeń](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) usługi Network Watcher. Funkcja monitorowania połączeń pozwala zobaczyć, jak ruch jest przekierowywany z interfejsu sieciowego źródłowej maszyny wirtualnej do interfejsu sieciowego docelowej maszyny wirtualnej.

## <a name="requirements-and-constraints"></a>Wymagania i ograniczenia

Sieci wirtualne globalnie połączone za pomocą sieci równorzędnych mają następujące ograniczenia:
- Sieci wirtualne mogą istnieć w dowolnym regionie chmury publicznej platformy Azure, ale nie w krajowych chmurach platformy Azure.
- Zasoby w jednej sieci wirtualnej nie mogą komunikować się z adresem IP frontonu wewnętrznego modułu równoważenia obciążenia platformy Azure w sieci wirtualnej globalnie połączonej za pomocą sieci równorzędnych. Moduł obciążenia równoważenia i zasoby, które się z nim komunikują, muszą być w tym samym regionie.
- Nie można używać zdalnych bram ani zezwalać na tranzyt bramy. Aby używać zdalnych bram lub zezwalać na tranzyt bramy, równorzędne sieci wirtualne muszą być w tym samym regionie.

Aby dowiedzieć się więcej na temat wymagań i ograniczeń, zobacz [Wymagania i ograniczenia dotyczące komunikacji równorzędnej między sieciami wirtualnymi](virtual-network-manage-peering.md#requirements-and-constraints). Aby dowiedzieć się więcej na temat limitów liczby komunikacji równorzędnych, które można utworzyć dla sieci wirtualnej, zobacz [Ograniczenia sieci platformy Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

## <a name="permissions"></a>Uprawnienia

Aby uzyskać informacje dotyczące uprawnień wymaganych do utworzenia komunikacji równorzędnej między sieciami wirtualnymi, zobacz [Uprawnienia wymagane do komunikacji równorzędnej między sieciami wirtualnymi](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Cennik

Istnieje nominalna opłata za ruch przychodzący i wychodzący w połączeniach wirtualnych sieciach równorzędnych. Aby uzyskać więcej informacji na temat cen wirtualnych sieci równorzędnych i globalnych wirtualnych sieci równorzędnych, zobacz [stronę cennika](https://azure.microsoft.com/pricing/details/virtual-network).

Tranzyt bramy jest właściwością komunikacji równorzędnej, która umożliwia sieci wirtualnej wykorzystanie bramy sieci VPN w równorzędnej sieci wirtualnej na potrzeby połączeń obejmujących wiele lokalizacji lub połączeń między sieciami wirtualnymi. Ruch przechodzący przez bramę zdalną w tym scenariuszu podlega [opłatom za bramę sieci VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). [Opłaty za wirtualne sieci równorzędne](https://azure.microsoft.com/pricing/details/virtual-network) nie są naliczane. Jeśli na przykład sieć VNetA ma bramę sieci VPN obsługującą łączność lokalną, a sieć VnetB komunikuje się równorzędnie z siecią VNetA za pośrednictwem odpowiednio skonfigurowanych właściwości, w przypadku ruchu z sieci VNetB do środowiska lokalnego są naliczane tylko opłaty za ruch wychodzący zgodnie z cennikiem bramy sieci VPN. Opłaty za wirtualne sieci równorzędne nie są naliczane. Dowiedz się, jak [skonfigurować tranzyt bramy sieci VPN na potrzeby wirtualnych sieci równorzędnych](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="next-steps"></a>Następne kroki

* Równorzędne sieci wirtualne tworzy się między sieciami wirtualnymi utworzonymi za pomocą tych samych lub różnych modeli wdrażania istniejących w tej samej lub w różnych subskrypcjach. Ukończ samouczek dla jednego z następujących scenariuszy:

    |Model wdrażania platformy Azure             | Subskrypcja  |
    |---------                          |---------|
    |Resource Manager — w obu przypadkach              |[Ta sama](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Różne](create-peering-different-subscriptions.md)|
    |Jedna sieć — Resource Manager, druga — model klasyczny  |[Ta sama](create-peering-different-deployment-models.md)|
    |                                   |[Różne](create-peering-different-deployment-models-subscriptions.md)|

* Dowiedz się jak, utworzyć [topologię sieciową typu gwiazda](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Zapoznaj się ze wszystkimi [ustawieniami wirtualnych sieci równorzędnych oraz sposobami ich zmiany](virtual-network-manage-peering.md).
* Aby uzyskać odpowiedzi na najczęściej zadawane pytania dotyczące wirtualnych sieci równorzędnych oraz sieci wirtualnych globalnie połączonych za pomocą sieci równorzędnych, zobacz [VNet Peering FAQ (Wirtualne sieci równorzędne — często zadawane pytania)](virtual-networks-faq.md#vnet-peering).
