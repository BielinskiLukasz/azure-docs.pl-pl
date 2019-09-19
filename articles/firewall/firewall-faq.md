---
title: Często zadawane pytania dotyczące zapory platformy Azure
description: Często zadawane pytania dotyczące zapory platformy Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: cb5b8bbb322dc401c7a8b057418d392120ef68e3
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130222"
---
# <a name="azure-firewall-faq"></a>Często zadawane pytania dotyczące zapory platformy Azure

## <a name="what-is-azure-firewall"></a>Co to jest usługa Azure Firewall?

Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network. Jest to w pełni stanowa Zapora jako usługa z wbudowaną wysoką dostępnością i nieograniczoną skalowalnością chmury. Możesz centralnie tworzyć, wymuszać i rejestrować zasady łączności aplikacji i sieci w subskrypcjach i sieciach wirtualnych.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Jakie możliwości są obsługiwane w zaporze platformy Azure?

* Zapora stanowa jako usługa
* Wbudowana wysoka dostępność z nieograniczoną skalowalnością w chmurze
* Filtrowanie według nazw FQDN
* Tagi nazw FQDN
* Reguły filtrowania ruchu sieciowego
* Obsługa translacji adresów sieciowych źródła (SNAT) dla ruchu wychodzącego
* Obsługa technologii DNAT dla ruchu przychodzącego
* Centralne tworzenie, wymuszanie i rejestrowanie zasad łączności aplikacji i sieci w ramach subskrypcji platformy Azure i sieci wirtualnych
* Pełna integracja z usługą Azure Monitor na potrzeby rejestrowania i analizy

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Jaki jest typowy model wdrażania dla zapory platformy Azure?

Zaporę platformy Azure można wdrożyć w dowolnej sieci wirtualnej, ale klienci zazwyczaj wdrażają ją w centralnej sieci wirtualnej i innych sieci wirtualnych w modelu gwiazdy i szprych. Następnie można ustawić trasy domyślnej z równorzędnych sieci wirtualnych w taki sposób, aby wskazywały tę centralną sieć wirtualną zapory. Globalna komunikacja równorzędna sieci wirtualnej jest obsługiwana, ale nie jest zalecana ze względu na potencjalne problemy z wydajnością i opóźnieniami w różnych regionach. Aby uzyskać najlepszą wydajność, wdróż jedną zaporę na region.

Zaletą tego modelu jest możliwość scentralizowanej kontroli nad wieloma szprychami sieci wirtualnych w różnych subskrypcjach. Są również naliczane oszczędności, ponieważ nie trzeba oddzielnie wdrażać zapory w każdej sieci wirtualnej. Oszczędności kosztów należy mierzyć w porównaniu z kosztami powiązania komunikacji równorzędnej na podstawie wzorców ruchu klientów.

## <a name="how-can-i-install-the-azure-firewall"></a>Jak zainstalować zaporę platformy Azure?

Zaporę platformy Azure można skonfigurować przy użyciu Azure Portal, programu PowerShell, interfejsu API REST lub przy użyciu szablonów. Zobacz [samouczek: Wdróż i skonfiguruj zaporę platformy Azure przy](tutorial-firewall-deploy-portal.md) użyciu Azure Portal, aby uzyskać instrukcje krok po kroku.

## <a name="what-are-some-azure-firewall-concepts"></a>Jakie są pojęcia związane z zaporą platformy Azure?

Zapora platformy Azure obsługuje reguły i kolekcje reguł. Kolekcja reguł jest zestawem reguł, które mają takie samo zamówienie i priorytet. Kolekcje reguł są wykonywane w kolejności ich priorytetu. Kolekcje reguł sieciowych są wyższym priorytetem niż kolekcje reguł aplikacji i wszystkie reguły są przerywane.

Istnieją trzy typy kolekcji reguł:

* *Reguły aplikacji*: Skonfiguruj w pełni kwalifikowane nazwy domen (FQDN), do których można uzyskać dostęp z podsieci.
* *Reguły sieci*: Skonfiguruj reguły, które zawierają adresy źródłowe, protokoły, porty docelowe i adresy docelowe.
* *Reguły NAT*: Skonfiguruj reguły DNAT, aby zezwalać na połączenia przychodzące.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Czy Zapora platformy Azure obsługuje filtrowanie ruchu przychodzącego?

Zapora platformy Azure obsługuje filtrowanie przychodzące i wychodzące. Ochrona ruchu przychodzącego jest dla protokołów innych niż HTTP/S. Na przykład protokoły RDP, SSH i FTP.

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Które usługi rejestrowania i analizy są obsługiwane przez zaporę systemu Azure?

Zapora platformy Azure jest zintegrowana z Azure Monitor do przeglądania i analizowania dzienników zapory. Dzienniki mogą być wysyłane do Log Analytics, usługi Azure Storage lub Event Hubs. Można je analizować w Log Analytics lub przy użyciu różnych narzędzi, takich jak program Excel i Power BI. Aby uzyskać więcej informacji, zobacz [Samouczek: Monitoruj dzienniki](tutorial-diagnostics.md)zapory platformy Azure.

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Jak Zapora platformy Azure różni się od istniejących usług, takich jak urządzeń WUS w portalu Marketplace?

Zapora systemu Azure to podstawowa usługa zapory, która może zająć się pewnymi scenariuszami klientów. Oczekujesz, że będziesz mieć kombinację urządzeń WUS innych firm i zapory platformy Azure. Lepsza współpraca jest priorytetem podstawowym.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Jaka jest różnica między Application Gateway WAF i zaporą platformy Azure?

Zapora aplikacji sieci Web (WAF) to funkcja Application Gateway, która zapewnia scentralizowaną ochronę ruchu przychodzącego aplikacji sieci Web przed typowymi programami wykorzystującymi luki w zabezpieczeniach. Zapora platformy Azure zapewnia ochronę ruchu przychodzącego dla protokołów innych niż HTTP/S (na przykład RDP, SSH, FTP), wychodzącej ochrony na poziomie sieci dla wszystkich portów i protokołów oraz ochrony na poziomie aplikacji dla wychodzącego protokołu HTTP/S.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Czym różnią się sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) i Zapora platformy Azure?

Usługa Zapora platformy Azure uzupełnia funkcjonalność sieciowych grup zabezpieczeń. Wspólnie zapewniają lepsze zabezpieczenia sieci "Ochrona przed bardziej szczegółowymi". Sieciowe grupy zabezpieczeń zapewniają Filtrowanie ruchu warstwy sieci rozproszonej, aby ograniczyć ruch do zasobów w ramach sieci wirtualnych w ramach każdej subskrypcji. Zapora systemu Azure to w pełni stanowa, scentralizowana Zapora sieciowa jako usługa, która zapewnia ochronę na poziomie sieci i aplikacji w różnych subskrypcjach i sieciach wirtualnych.

## <a name="are-network-security-groups-nsgs-supported-on-the-azure-firewall-subnet"></a>Czy sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) są obsługiwane w podsieci zapory platformy Azure?

Zapora systemu Azure to zarządzana usługa z wieloma warstwami ochrony, obejmująca ochronę platformy przy użyciu sieciowych grup zabezpieczeń na poziomie karty sieciowej (niewidoczny).  Sieciowych grup zabezpieczeń poziomu podsieci nie są wymagane w podsieci zapory platformy Azure i są wyłączone w celu zapewnienia braku przerw w świadczeniu usług.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Jak mogę skonfigurować zaporę platformy Azure z punktami końcowymi usługi?

Aby zapewnić bezpieczny dostęp do usług PaaS Services, zalecamy korzystanie z punktów końcowych usługi. Możesz włączyć punkty końcowe usługi w podsieci zapory platformy Azure i wyłączyć je w sieci wirtualnej połączone szprych. Dzięki temu można korzystać z obu funkcji — zabezpieczeń punktu końcowego usługi i centralnego rejestrowania dla całego ruchu.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Jakie są ceny zapory systemu Azure?

Zobacz [Cennik usługi Azure firewall](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Jak zatrzymać i uruchomić zaporę platformy Azure?

Można użyć Azure PowerShell *alokacji* i *alokacji* metod.

Na przykład:

```azurepowershell
# Stop an existing firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
# Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Musisz ponownie przydzielić zaporę i publiczny adres IP do oryginalnej grupy zasobów i subskrypcji.

## <a name="what-are-the-known-service-limits"></a>Jakie są znane limity usługi?

Aby uzyskać ograniczenia dotyczące usługi Zapora platformy Azure, zobacz [limity dotyczące subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Czy Zapora platformy Azure w sieci wirtualnej centrum przekazuje i filtruje ruch sieciowy między dwiema sieciami wirtualnymi szprych?

Tak, możesz użyć zapory platformy Azure w sieci wirtualnej Hub do kierowania i filtrowania ruchu między dwiema sieciami wirtualnymi szprych. Podsieci w każdej z sieci wirtualnych szprych muszą mieć UDR wskazujące na zaporę platformy Azure jako bramę domyślną, aby ten scenariusz działał poprawnie.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Czy Zapora platformy Azure przekazuje i filtruje ruch sieciowy między podsieciami w tej samej sieci wirtualnej lub równorzędnej sieci wirtualnej?

Tak. Jednak skonfigurowanie UDR do przekierowywania ruchu między podsieciami w tej samej wirtualnej wymaga dodatkowej uwagi. Użycie zakresu adresów sieci wirtualnej jako prefiksu docelowego dla UDR jest wystarczające, ale również kieruje cały ruch z jednej maszyny do innej maszyny w tej samej podsieci za pośrednictwem wystąpienia zapory platformy Azure. Aby tego uniknąć, należy uwzględnić trasę dla podsieci w UDR z typem następnego przeskoku sieci **wirtualnej**. Zarządzanie tymi trasami może być uciążliwe i podatne na błędy. Zalecaną metodą segmentacji sieci wewnętrznej jest użycie sieciowych grup zabezpieczeń, które nie wymagają UDR.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>Czy ruch wychodzący między sieciami prywatnymi jest zaporą platformy Azure?

Zapora platformy Azure nie ma protokołu IPSec, gdy docelowy adres IP jest prywatnym zakresem adresów IP na [organizację IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Jeśli Twoja organizacja używa zakresu publicznego adresu IP dla sieci prywatnych, Zapora platformy Azure SNATs ruch do jednego z prywatnych adresów IP zapory w AzureFirewallSubnet.

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Czy jest wymuszane tunelowanie/łączenie do obsługiwanego sieciowego urządzenia wirtualnego?

Wymuszone tunelowanie nie jest obecnie obsługiwane. Zapora platformy Azure musi mieć bezpośrednią łączność z Internetem. Jeśli AzureFirewallSubnet nauczy trasy domyślnej do sieci lokalnej za pośrednictwem protokołu BGP, należy przesłonić ten element przy użyciu wartości 0.0.0.0/0 UDR z wartością **NextHopType** ustawioną jako **Internet** w celu utrzymania bezpośredniej łączności z Internetem.

Jeśli konfiguracja wymaga wymuszonego tunelowania do sieci lokalnej i można określić docelowe prefiksy adresów IP dla miejsc docelowych Internetu, można skonfigurować te zakresy przy użyciu sieci lokalnej jako następnego skoku za pośrednictwem trasy zdefiniowanej przez użytkownika na stronie AzureFirewallSubnet. Lub można użyć protokołu BGP, aby zdefiniować te trasy.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Czy istnieją jakieś ograniczenia grupy zasobów zapory?

Tak. Wszystkie zapory, podsieci, sieci wirtualnej i publiczny adres IP muszą znajdować się w tej samej grupie zasobów.

## <a name="when-configuring-dnat-for-inbound-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Czy podczas konfigurowania DNAT na potrzeby przychodzącego ruchu sieciowego należy również skonfigurować odpowiednią regułę sieci, aby zezwolić na ten ruch?

Nie. Reguły translatora adresów sieciowych niejawnie Dodaj odpowiednią regułę sieci, aby zezwolić na ruch przetłumaczony. Aby przesłonić to zachowanie, jawnie dodaj kolekcję reguł sieci z regułami odmowy zgodnymi z przetłumaczonym ruchem. Aby dowiedzieć się więcej na temat logiki przetwarzania reguł usługi Azure Firewall, zobacz [Azure Firewall rule processing logic (Logika przetwarzania reguł usługi Azure Firewall)](rule-processing.md).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Jak działają symbole wieloznaczne w nazwie FQDN docelowej reguły aplikacji?

Skonfigurowanie * **. contoso.com**umożliwia *anyvalue*. contoso.com, ale nie contoso.com (wierzchołk domeny). Jeśli chcesz zezwolić na wierzchołk domeny, musisz jawnie skonfigurować go jako docelową nazwę FQDN.

## <a name="what-does-provisioning-state-failed-mean"></a>Co to *jest stan aprowizacji: Co* się nie powiodło?

Za każdym razem, gdy stosowana jest zmiana konfiguracji, Zapora platformy Azure próbuje zaktualizować wszystkie jej bazowe wystąpienia zaplecza. W rzadkich przypadkach nie można zaktualizować jednego z tych wystąpień zaplecza przy użyciu nowej konfiguracji, a proces aktualizacji zostanie zatrzymany z nieprawidłowym stanem aprowizacji. Zapora platformy Azure nadal działa, ale zastosowana konfiguracja może być w niespójnym stanie, w którym niektóre wystąpienia mają poprzednią konfigurację, gdzie inne mają zaktualizowany zestaw reguł. W takim przypadku spróbuj zaktualizować konfigurację jeszcze raz, dopóki operacja się nie powiedzie, a Zapora *zakończyła się pomyślnie* .

### <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Jak Zapora platformy Azure obsługuje planowaną konserwację i nieplanowane błędy?
Zapora platformy Azure składa się z kilku węzłów zaplecza w konfiguracji aktywne-aktywne.  W przypadku każdej planowanej konserwacji mamy do bezpiecznego aktualizowania węzłów opróżnianie logiki.  Aktualizacje są planowane w godzinach poza godzinami pracy dla każdego regionu platformy Azure w celu dodatkowego ograniczenia ryzyka zakłócenia.  W przypadku nieplanowanych problemów tworzymy nowy węzeł, który zastąpi węzeł zakończony niepowodzeniem.  Połączenie z nowym węzłem jest zwykle ponownie nawiązane w ciągu 10 sekund od momentu awarii.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>Czy istnieje limit znaków nazwy zapory?

Tak. Dla nazwy zapory istnieje limit znaków 50.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Dlaczego Zapora platformy Azure potrzebuje rozmiaru podsieci/26?

Zapora platformy Azure musi obsługiwać więcej wystąpień maszyn wirtualnych w miarę skalowania. Przestrzeń adresowa (/26) gwarantuje, że Zapora ma wystarczającą liczbę adresów IP, aby obsłużyć skalowanie.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>Czy rozmiar podsieci zapory musi ulec zmianie w miarę skalowania usługi?

Nie. Zapora platformy Azure nie potrzebuje podsieci większej niż/26.

## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>Czy Zapora platformy Azure domyślnie zezwala na dostęp do Active Directory?

Nie. Zapora systemu Azure domyślnie blokuje dostęp Active Directory. Aby zezwolić na dostęp, skonfiguruj tag usługi usługi azureactivedirectory. Aby uzyskać więcej informacji, zobacz [Tagi usługi Zapora platformy Azure](service-tags.md).
