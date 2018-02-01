---
title: "Wirtualne sieci równorzędne platformy Azure | Microsoft Docs"
description: "Dowiedz się więcej na temat wirtualnych sieci równorzędnych na platformie Azure."
services: virtual-network
documentationcenter: na
author: NarayanAnnamalai
manager: jefco
editor: tysonn
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: narayan;anavin
ms.openlocfilehash: f43c95753e2cb190270a25fecd4c490e6fb0ed34
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2018
---
# <a name="virtual-network-peering"></a>Wirtualne sieci równorzędne

Komunikacja równorzędna między sieciami wirtualnymi umożliwia bezproblemowe połączenie dwóch [sieci wirtualnych](virtual-networks-overview.md) platformy Azure. Po nawiązaniu połączenia równorzędnego sieci wirtualne są traktowane jako jedna sieć. Ruch między maszynami wirtualnymi w wirtualnych sieciach równorzędnych odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft — tak jak ruch między maszynami wirtualnymi w tej samej sieci wirtualnej tylko za pośrednictwem *prywatnych* adresów IP. 

Korzystanie z wirtualnych sieci równorzędnych zapewnia m.in. następujące korzyści:

* Ruch sieciowy między wirtualnymi sieciami równorzędnymi jest prywatny. Ruch między sieciami wirtualnymi jest utrzymywany w sieci szkieletowej firmy Microsoft. Do komunikacji między sieciami wirtualnymi nie jest wymagany publiczny Internet, bramy ani szyfrowanie.
* Połączenie o małych opóźnieniach i dużej przepustowości między zasobami w różnych sieciach wirtualnych.
* Możliwość komunikacji zasobów w jednej sieci wirtualnej z zasobami w innej sieci wirtualnej po równorzędnym połączeniu sieci wirtualnych.
* Możliwość przesyłania danych między subskrypcjami platformy Azure, modelami wdrażania i regionami platformy Azure (wersja zapoznawcza).
* Możliwość połączenia sieci wirtualnych utworzonych przy użyciu usługi Azure Resource Manager lub połączenia jednej sieci wirtualnej utworzonej za pomocą usługi Resource Manager z drugą siecią wirtualną utworzoną za pomocą klasycznego modelu wdrażania. Aby dowiedzieć się więcej na temat modeli wdrażania platformy Azure, zapoznaj się z artykułem [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Informacje na temat modeli wdrażania platformy Azure).
* Brak przestojów zasobów w sieci wirtualnej podczas tworzenia komunikacji równorzędnej albo po utworzeniu komunikacji równorzędnej.

## <a name="requirements-constraints"></a>Wymagania i ograniczenia

* Łączenie sieci wirtualnych za pomocą komunikacji równorzędnej w tym samym regionie jest ogólnie dostępne. Możliwość łączenia sieci wirtualnych za pomocą komunikacji równorzędnej w różnych regionach jest aktualnie dostępna w wersji zapoznawczej w następujących regionach: Zachodnio-środkowe stany USA, Kanada Środkowa, Zachodnie stany USA 2, Korea Południowa, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo, Kanada Wschodnia, Indie Południowe, Indie Środkowe i Indie Zachodnie. Przed rozpoczęciem łączenia sieci wirtualnych za pomocą komunikacji równorzędnej w różnych regionach należy [zarejestrować subskrypcję](virtual-network-create-peering.md#register) w celu uzyskania dostępu do wersji zapoznawczej. Próba połączenia sieci wirtualnych za pomocą komunikacji równorzędnej w różnych regionach zakończy się niepowodzeniem, jeśli rejestracja w celu uzyskania dostępu do wersji zapoznawczej nie została zakończona.
    > [!WARNING]
    > Połączenia sieci wirtualnych za pomocą komunikacji równorzędnej między regionami mogą nie mieć takiego samego poziomu dostępności i niezawodności jak połączenia za pomocą komunikacji równorzędnej w wersji ogólnodostępnej. Połączenia sieci wirtualnych za pomocą komunikacji równorzędnej mogą mieć ograniczone możliwości i mogą nie być dostępne we wszystkich regionach platformy Azure. Najbardziej aktualne powiadomienia dotyczące dostępności i stanu tej funkcji można znaleźć na stronie [aktualizacji usługi Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network).

* Wirtualne sieci równorzędne muszą mieć nienakładające się przestrzenie adresów IP.
* Nie można dodać ani usunąć zakresów adresów z przestrzeni adresowej sieci wirtualnej po połączeniu sieci wirtualnej z inną siecią wirtualną za pomocą komunikacji równorzędnej. Jeśli trzeba dodać zakresy adresów do przestrzeni adresowej sieci wirtualnej połączonej za pomocą komunikacji równorzędnej, musisz usunąć połączenie komunikacji równorzędnej, dodać przestrzeń adresową, a następnie ponownie dodać połączenie komunikacji równorzędnej.
* Wirtualne sieci równorzędne obejmują dwie sieci wirtualne. Nie istnieje żadna pochodna relacja przechodnia między tymi sieciami. Na przykład: jeśli wirtualna sieć A jest połączona z wirtualną siecią B, a wirtualna sieć B jest połączona z wirtualną siecią C, to sieć A *nie* jest połączona z siecią C.
* Komunikacja równorzędna może zostać nawiązana między sieciami wirtualnymi istniejącymi w dwóch różnych subskrypcjach, o ile połączenie zostanie autoryzowane przez uprawnionego użytkownika (zobacz [konkretne uprawnienia](create-peering-different-deployment-models-subscriptions.md#permissions)) w obu subskrypcjach, a subskrypcje będą skojarzone z tą samą dzierżawą usługi Azure Active Directory. Do łączenia sieci wirtualnych w ramach subskrypcji skojarzonych z różnymi dzierżawcami usługi Active Directory można użyć usługi [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V).
* Komunikacja równorzędna może zostać nawiązana między sieciami wirtualnymi, jeśli obie sieci zostały utworzone za pomocą modelu wdrażania przy użyciu usługi Resource Manager lub jeśli jedna z nich została utworzona w ten sposób, a druga za pomocą klasycznego modelu wdrażania. Nie można jednak nawiązać komunikacji równorzędnej między sieciami wirtualnymi utworzonymi za pomocą klasycznego modelu wdrażania. Do połączenia sieci wirtualnych utworzonych za pomocą klasycznego modelu wdrażania można użyć usługi [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V).
* Chociaż komunikacja między maszynami wirtualnymi w wirtualnych sieciach równorzędnych nie ma żadnych dodatkowych ograniczeń co do przepustowości, należy pamiętać, że nadal obowiązuje ograniczenie maksymalnej przepustowości sieci uzależnione od rozmiaru maszyny wirtualnej. Aby dowiedzieć się więcej o maksymalnej przepustowości dla różnych rozmiarów maszyn wirtualnych, przeczytaj artykuły dotyczące rozmiarów maszyn wirtualnych w systemach [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

     ![Podstawowe wirtualne sieci równorzędne](./media/virtual-networks-peering-overview/figure03.png)

## <a name="connectivity"></a>Łączność

Po połączeniu sieci wirtualnych za pomocą komunikacji równorzędnej zasoby w dowolnej z tych sieci wirtualnych mogą łączyć się bezpośrednio z innymi zasobami w równorzędnej sieci wirtualnej.

Opóźnienie sieciowe między maszynami wirtualnymi w równorzędnych sieciach wirtualnych w tym samym regionie jest takie samo jak opóźnienie w pojedynczej sieci wirtualnej. Przepływność sieci zależy od przepustowości dozwolonej dla maszyny wirtualnej proporcjonalnie do jej rozmiaru. Nie ma żadnych dodatkowych ograniczeń przepustowości w obrębie komunikacji równorzędnej.

Ruch między maszynami wirtualnymi w równorzędnych sieciach wirtualnych odbywa się bezpośrednio za pomocą infrastruktury sieci szkieletowej firmy Microsoft, a nie przy użyciu bramy lub publicznego Internetu.

Maszyny wirtualne w sieci wirtualnej mogą uzyskiwać dostęp do wewnętrznego modułu równoważenia obciążenia w równorzędnej sieci wirtualnej w tym samym regionie. Obsługa wewnętrznego modułu równoważenia obciążenia w wersji zapoznawczej nie jest rozszerzana na globalne równorzędne sieci wirtualne. W wersji ogólnodostępnej globalne sieci wirtualne połączone za pomocą komunikacji równorzędnej będą oferować obsługę wewnętrznego modułu równoważenia obciążenia.

Sieciowe grupy zabezpieczeń można stosować w dowolnej sieci wirtualnej, aby w razie potrzeby zablokować dostęp do innych sieci wirtualnych lub podsieci.
Podczas konfigurowania wirtualnych sieci równorzędnych można otwierać i zamykać reguły grupy zabezpieczeń sieci między sieciami wirtualnymi. W przypadku otwarcia pełnej łączności między równorzędnymi sieciami wirtualnymi (jest to opcja domyślna) można zablokować lub odmówić określonego rodzaju dostępu do określonych podsieci lub maszyn wirtualnych. Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [Network security groups overview (Omówienie sieciowych grup zabezpieczeń)](virtual-networks-nsg.md).

## <a name="service-chaining"></a>Tworzenie łańcuchów usług

Użytkownicy mogą konfigurować trasy zdefiniowane przez użytkownika prowadzące do maszyn wirtualnych w wirtualnych sieciach równorzędnych (jako adresy IP *kolejnego przeskoku*) lub do bram sieci wirtualnych, aby umożliwić tworzenie łańcucha usług. Tworzenie łańcucha usług umożliwia bezpośrednie kierowanie ruchu z jednej sieci wirtualnej do urządzenia wirtualnego lub do bramy sieci wirtualnej w wirtualnej sieci równorzędnej przy użyciu tras zdefiniowanych przez użytkownika.

Można również wdrażać sieci typu gwiazdy, w których centralna sieć wirtualna może hostować składniki infrastruktury, takie jak sieciowe urządzenie wirtualne lub brama sieci VPN. Następnie wszystkie sieci wirtualne typu gwiazda można połączyć za pomocą komunikacji równorzędnej z centralną siecią wirtualną. Ruch może przepływać za pośrednictwem wirtualnych urządzeń sieciowych lub bram sieci VPN działających w centralnej sieci wirtualnej. 

Wirtualne sieci równorzędne umożliwiają użycie kolejnego przeskoku w trasie zdefiniowanej przez użytkownika jako adresu IP maszyny wirtualnej w wirtualnej sieci równorzędnej lub bramie sieci VPN. Nie można jednak wyznaczać trasy między sieciami wirtualnymi w trasie zdefiniowanej przez użytkownika, określając bramę usługi ExpressRoute jako typ następnego przeskoku. Aby dowiedzieć się więcej o trasach definiowanych przez użytkownika, zobacz [User-defined routes overview](virtual-networks-udr-overview.md#user-defined) (Omówienie tras definiowanych przez użytkownika). Aby dowiedzieć się, jak utworzyć topologię sieci typu gwiazda, zobacz [Hub and spoke network topology (Topologia sieci typu gwiazda)](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering).

## <a name="gateways-and-on-premises-connectivity"></a>Bramy i łączność lokalna

Każda sieć wirtualna — niezależnie od tego, czy jest połączona za pomocą komunikacji równorzędnej z inną siecią wirtualną — może mieć własną bramę i używać jej do łączenia się z lokalną infrastrukturą sieciową. Użytkownicy mogą również konfigurować [połączenia między sieciami wirtualnymi](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) przy użyciu bram, nawet jeśli te sieci wirtualne zostały połączone za pomocą komunikacji równorzędnej.

Po skonfigurowaniu obu opcji łączności między sieciami wirtualnymi ruch między tymi sieciami wirtualnymi jest oparty na konfiguracji komunikacji równorzędnej (to znaczy odbywa się za pośrednictwem sieci szkieletowej platformy Azure).

Gdy sieci wirtualne są połączone za pomocą komunikacji równorzędnej w tym samym regionie, użytkownicy mogą również skonfigurować bramę w wirtualnej sieci równorzędnej jako punkt tranzytowy do infrastruktury lokalnej. W tym przypadku sieć wirtualna korzystająca z bramy zdalnej nie może mieć własnej bramy. Jedna sieć wirtualna może mieć tylko jedną bramę. Brama może być lokalna lub zdalna (w wirtualnej sieci równorzędnej), jak przedstawiono na poniższej ilustracji:

![przesyłanie w równorzędnych sieciach wirtualnych](./media/virtual-networks-peering-overview/figure04.png)

Przesyłanie danych za pomocą bramy nie jest obsługiwane w relacji komunikacji równorzędnej między sieciami wirtualnymi utworzonymi za pomocą różnych modeli wdrażania lub różnych regionów. Aby przesyłanie danych za pomocą bramy działało, obie sieci wirtualne będące w relacji komunikacji równorzędnej muszą zostać utworzone za pomocą usługi Resource Manager i muszą znajdować się w tym samym regionie. Sieci wirtualne globalnie połączone za pomocą sieci równorzędnych obsługują przesyłanie danych za pomocą bramy.

W przypadku połączenia za pomocą komunikacji równorzędnej sieci wirtualnych współużytkujących jedno połączenie sługi Azure ExpressRoute ruch między nimi jest oparty na relacji komunikacji równorzędnej (to znaczy odbywa się za pośrednictwem sieci szkieletowej platformy Azure). Użytkownicy mogą nadal korzystać z bram lokalnych w poszczególnych sieciach wirtualnych, aby łączyć się z obwodem lokalnym. Można również użyć bramy współdzielonej i skonfigurować tranzyt dla łączności lokalnej.

## <a name="permissions"></a>Uprawnienia

Wirtualne sieci równorzędne wymagają odpowiednich uprawnień. Jest to oddzielna funkcja w przestrzeni nazw VirtualNetworks. Użytkownik może uzyskać określone uprawnienia do autoryzowania komunikacji równorzędnej. Użytkownik mający dostęp do odczytu i zapisu do sieci wirtualnej automatycznie dziedziczy te uprawnienia.

Użytkownik będący administratorem lub użytkownikiem mającym uprawienia do komunikacji równorzędnej może zainicjować operację komunikacji równorzędnej w innej sieci wirtualnej. Wymagany minimalny poziom uprawnień to współautor sieci. Jeśli po drugiej stronie istnieje zgodne żądanie komunikacji równorzędnej i spełnione są pozostałe wymagania, połączenie za pomocą komunikacji równorzędnej zostanie nawiązane.

Jeśli na przykład łączysz sieci wirtualne o nazwach myVirtualNetworkA i myVirtualNetworkB, do konta musisz przypisać minimalnie następującą rolę lub uprawnienia dla każdej sieci wirtualnej:

|Sieć wirtualna|Model wdrażania|Rola|Uprawnienia|
|---|---|---|---|
|myVirtualNetworkA|Resource Manager|[Współautor sieci](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Wdrożenie klasyczne|[Współautor klasycznej sieci](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Nie dotyczy|
|myVirtualNetworkB|Resource Manager|[Współautor sieci](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Wdrożenie klasyczne|[Współautor klasycznej sieci](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

## <a name="monitor"></a>Monitorowanie

W przypadku łączenia dwóch sieci wirtualnych utworzonych w usłudze Resource Manager za pomocą komunikacji równorzędnej należy skonfigurować komunikację równorzędną dla każdej objętej nią sieci wirtualnej. Możesz monitorować stan połączenia za pomocą komunikacji równorzędnej. Stan komunikacji równorzędnej jest jednym z następujących stanów:

* **Zainicjowano**: ten stan jest sygnalizowany podczas tworzenia połączenia za pomocą komunikacji równorzędnej z pierwszej sieci wirtualnej do drugiej sieci wirtualnej.
* **Połączono**: ten stan jest sygnalizowany po utworzeniu połączenia za pomocą komunikacji równorzędnej z drugiej sieci wirtualnej do pierwszej sieci wirtualnej. Stan komunikacji równorzędnej dla pierwszej sieci wirtualnej zmienia się z *Zainicjowano* na *Połączono*. Komunikacja równorzędna w sieci wirtualnej nie zostanie pomyślnie nawiązana, aż do momentu, gdy stanem komunikacji równorzędnej w obydwu sieciach wirtualnych będzie *Połączono*.
* **Rozłączono**: stan sygnalizowany, gdy komunikacja równorzędna z jednej sieci wirtualnej do innej zostanie usunięta po wcześniejszym nawiązaniu komunikacji równorzędnej między dwiema sieciami wirtualnymi.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Aby potwierdzić komunikację równorzędną w sieci wirtualnej, możesz [sprawdzić efektywne trasy](virtual-network-routes-troubleshoot-portal.md) dla interfejsu sieciowego w dowolnej podsieci w sieci wirtualnej. Jeśli istnieje komunikacja równorzędna w sieci wirtualnej, wszystkie podsieci w tej sieci wirtualnej mają trasy z typem następnego przeskoku *Komunikacja równorzędna sieci wirtualnych* dla każdej przestrzeni adresowej w każdej równorzędnej sieci wirtualnej.

Problemy dotyczące łączności z maszyną wirtualną w równorzędnej sieci wirtualnej można również rozwiązywać przy użyciu funkcji [monitorowania połączeń](../network-watcher/network-watcher-connectivity-portal.md) usługi Network Watcher. Funkcja monitorowania połączeń pozwala zobaczyć, jak ruch jest przekierowywany z interfejsu sieciowego źródłowej maszyny wirtualnej do interfejsu sieciowego docelowej maszyny wirtualnej.

## <a name="limits"></a>Limity

Istnieją limity liczby dozwolonych połączeń za pomocą komunikacji równorzędnej dla jednej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Azure networking limits (Ograniczenia sieci platformy Azure)](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="pricing"></a>Cennik

Istnieje nominalna opłata za ruch przychodzący i wychodzący w połączeniach wirtualnych sieciach równorzędnych. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="next-steps"></a>Następne kroki

* Ukończ samouczek dotyczący równorzędnych sieci wirtualnych. Równorzędne sieci wirtualne tworzy się między sieciami wirtualnymi utworzonymi za pomocą tych samych lub różnych modeli wdrażania istniejących w tej samej lub w różnych subskrypcjach. Ukończ samouczek dla jednego z następujących scenariuszy:

    |Model wdrażania platformy Azure  | Subskrypcja  |
    |---------|---------|
    |Resource Manager — w obu przypadkach |[Ta sama](virtual-network-create-peering.md)|
    | |[Różne](create-peering-different-subscriptions.md)|
    |Jedna sieć — Resource Manager, druga — model klasyczny     |[Ta sama](create-peering-different-deployment-models.md)|
    | |[Różne](create-peering-different-deployment-models-subscriptions.md)|

* Dowiedz się jak, utworzyć [topologię sieciową typu gwiazda](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering).
* Zapoznaj się ze wszystkimi [ustawieniami równorzędnych sieci wirtualnych oraz sposobami ich zmiany](virtual-network-manage-peering.md)
