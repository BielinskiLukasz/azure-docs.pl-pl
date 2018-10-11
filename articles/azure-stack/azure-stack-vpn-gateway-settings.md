---
title: Ustawienia bramy sieci VPN dla usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o ustawieniach bramy sieci VPN, którego używasz z usługą Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: fa8d3adc-8f5a-4b4f-8227-4381cf952c56
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/14/2018
ms.author: sethm
ms.openlocfilehash: 2fa062621e551ce7182facc45ec84b39d4c2dad7
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078753"
---
# <a name="vpn-gateway-configuration-settings-for-azure-stack"></a>Ustawienia konfiguracji bramy sieci VPN dla usługi Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Tworzenie bramy sieci VPN jest typem bramy sieci wirtualnej, który wysyła zaszyfrowany ruch sieciowy między siecią wirtualną w usłudze Azure Stack i zdalnego bramy sieci VPN. Zdalne bramy sieci VPN może być na platformie Azure, urządzenie w centrum danych lub urządzeń w innej lokacji.  Jeśli istnieje połączenie sieciowe między dwoma punktami końcowymi, można ustanowić bezpiecznego połączenia sieci VPN typu lokacja-lokacja (S2S) między dwiema sieciami.

Połączenie bramy sieci VPN zależy od konfiguracji wielu zasobów, z których każdy zawiera konfigurowalnych ustawień. W tym artykule omówiono, zasoby i ustawienia, które odnoszą się do bramy sieci VPN dla sieci wirtualnej, który zostanie utworzony w modelu wdrażania usługi Resource Manager. Możesz znaleźć opisy i diagramy topologii dla każdego rozwiązania połączenia w [VPN Gateway — informacje dla usługi Azure Stack](azure-stack-vpn-gateway-about-vpn-gateways.md).

## <a name="vpn-gateway-settings"></a>Ustawień usługi VPN gateway

### <a name="gateway-types"></a>Typy bram

Każda sieć wirtualna usługi Azure Stack obsługuje bramy jednej sieci wirtualnej, która musi być typu **Vpn**.  Obsługa ta różni się od platformy Azure, który obsługuje dodatkowe typy.  

Podczas tworzenia bramy sieci wirtualnej, upewnij się, że typ bramy jest prawidłowy dla danej konfiguracji. Brama sieci VPN wymaga `-GatewayType Vpn`, na przykład:

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn
-VpnType RouteBased
```

### <a name="gateway-skus"></a>Jednostki SKU bramy

Podczas tworzenia bramy sieci wirtualnej musisz wybrać jednostkę SKU bramy do użycia. Wybierz jednostki SKU, które spełniają wymagania, na podstawie typów obciążeń, przepustowości, funkcji i umowy SLA.

Usługa Azure Stack oferuje bramy sieci VPN jednostkach SKU wymienionych w poniższej tabeli.

|   | Przepływność bramy sieci VPN |Maksymalna tuneli IPsec bramy sieci VPN |
|-------|-------|-------|
|**Podstawowa jednostka SKU**  | 100 Mb/s  | 10    |
|**Standardowa jednostka SKU**           | 100 Mb/s  | 10    |
|**Jednostka SKU wysokiej wydajności** | 200 Mb/s    | 5 |

### <a name="resizing-gateway-skus"></a>Zmiana rozmiaru jednostki SKU bramy

Usługa Azure Stack nie obsługuje rozmiaru jednostek SKU między obsługiwanych starszych jednostek SKU.

Podobnie usługi Azure Stack nie obsługuje rozmiaru z obsługiwanych starszych jednostek SKU (Basic, Standard i HighPerformance) do nowszej jednostki SKU obsługiwane przez platformę Azure (VpnGw1, VpnGw2 i VpnGw3.)

### <a name="configure-the-gateway-sku"></a>Konfigurowanie bramy jednostki SKU

#### <a name="azure-stack-portal"></a>Portal usługi Azure Stack

Jeśli używasz portalu usługi Azure Stack do tworzenia bramy sieci wirtualnej usługi Resource Manager za pomocą listy rozwijanej można wybrać jednostkę SKU bramy. Opcje, które pojawi się informacja o odpowiadają typu bramy i wybranego typu sieci VPN.

#### <a name="powershell"></a>PowerShell

W poniższym przykładzie programu PowerShell **- GatewaySku** jako VpnGw1.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1
-GatewayType Vpn -VpnType RouteBased
```

### <a name="connection-types"></a>Typy połączeń

W modelu wdrażania usługi Resource Manager każda konfiguracja wymaga typu połączenia bramy określonej sieci wirtualnej. Dostępne środowiska PowerShell usługi Resource Manager wartości **- ConnectionType** są:

* IPsec

W poniższym przykładzie programu PowerShell tworzone jest połączenie S2S wymagającego typu połączenia IPsec.

```PowerShell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

### <a name="vpn-types"></a>Typy sieci VPN

Podczas tworzenia bramy sieci wirtualnej dla konfiguracji bramy sieci VPN, należy określić typ sieci VPN. Typ sieci VPN, który wybierzesz, zależy od topologii połączenia, który chcesz utworzyć.  Typ sieci VPN może także zależeć od sprzętu, którego używasz. Konfiguracje S2S wymagają urządzenia sieci VPN. Niektóre urządzenia sieci VPN obsługują tylko określonego typu sieci VPN.

> [!IMPORTANT]  
> Obecnie usługa Azure Stack obsługuje tylko typ trasy sieci VPN opartym na. Jeśli urządzenie obsługuje tylko sieci VPN oparte na zasadach, połączenia dla tych urządzeń z usługi Azure Stack nie są obsługiwane.  
>
> Ponadto usługi Azure Stack nie obsługuje korzystania z selektorów ruchu na podstawie zasad dla bram na podstawie tras w tej chwili, ponieważ konfiguracje niestandardowe zasady protokołu IPSec/IKE nie są obsługiwane.

* **Oparte na zasadach**: sieci VPN oparte na zasadach szyfrują i kierowania pakietów przez tunel protokołu IPsec na podstawie zasad protokołu IPsec, które są skonfigurowane przy użyciu kombinacji prefiksów adresów między siecią lokalną i sieci wirtualnej usługi Azure Stack. Zasady lub selektor ruchu jest zwykle Lista dostępu w konfiguracji urządzenia sieci VPN.

  >[!NOTE]
  >Oparte na zasadach jest obsługiwane na platformie Azure, ale nie w usłudze Azure Stack.

* **RouteBased**: sieci VPN typu RouteBased użycie tras, które są konfigurowane w IP przesyłania dalej lub tabeli routingu do kierowania pakietów do odpowiednich interfejsów tuneli. W dalszej kolejności interfejsy tuneli szyfrują lub odszyfrowują pakiety wchodzące do tuneli lub wychodzące z nich. Zasady lub selektor ruchu dla sieci VPN typu RouteBased są skonfigurowane jako dowolna dowolna (lub użyj symbole wieloznaczne). Domyślnie nie można zmienić. Wartość dla typu sieci VPN typu RouteBased jest RouteBased.

W poniższym przykładzie programu PowerShell **- VpnType** jako RouteBased. Podczas tworzenia bramy, upewnij się, że **- VpnType** jest prawidłowy dla danej konfiguracji.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig
-GatewayType Vpn -VpnType RouteBased
```

### <a name="gateway-requirements"></a>Wymagania dotyczące bramy

Poniższa tabela zawiera listę wymagań dotyczących bram sieci VPN.

| |Brama sieci VPN typu PolicyBased Basic | Brama sieci VPN typu RouteBased Basic | Brama sieci VPN typu RouteBased Standard | Bramy sieci VPN usługi RouteBased o wysokiej wydajności|
|--|--|--|--|--|
| **Łączność lokacja-lokacja (S2S połączenia)** | Nieobsługiwane | Konfiguracja sieci VPN typu RouteBased | Konfiguracja sieci VPN typu RouteBased | Konfiguracja sieci VPN typu RouteBased |
| **Metoda uwierzytelniania**  | Nieobsługiwane | Klucz wstępny dla połączeń S2S  | Klucz wstępny dla połączeń S2S  | Klucz wstępny dla połączeń S2S  |   
| **Maksymalna liczba połączeń S2S**  | Nieobsługiwane | 10 | 10| 5|
|**Aktywna Obsługa routingu (BGP)** | Nieobsługiwane | Nieobsługiwane | Obsługiwane | Obsługiwane |

### <a name="gateway-subnet"></a>Podsieć bramy

Przed utworzeniem bramy sieci VPN, należy utworzyć podsieć bramy. Podsieć bramy zawiera adresy IP, które używają bramy sieci wirtualnej maszyny wirtualne i usługi. Podczas tworzenia bramy sieci wirtualnej maszyny wirtualne bramy są wdrażane w podsieci bramy i skonfigurować wymagane ustawienia bramy sieci VPN. **Nie** należy wdrażać żadnych elementów else (na przykład dodatkowych maszyn wirtualnych) w podsieci bramy.

>[!IMPORTANT]
>Aby podsieć bramy działała prawidłowo, musi nosić nazwę **GatewaySubnet**. Usługa Azure Stack używa tej nazwy do identyfikacji podsieci, do wdrożenia bramy sieci wirtualnej maszyn wirtualnych i usług.

Podczas tworzenia podsieci bramy należy określić liczbę zawartych w niej adresów IP. Adresy IP w podsieci bramy są przydzielane do usług bramy i maszyny wirtualne bramy. Niektóre konfiguracje wymagają większej liczby adresów IP niż inne. Przyjrzyj się instrukcje dotyczące konfiguracji, który chcesz utworzyć i sprawdź, czy podsieci bramy, w której chcesz utworzyć spełnia te wymagania.

Ponadto należy pamiętać, że podsieć bramy zawiera wystarczającą liczbę adresów IP do obsługi dodatkowych przyszłych konfiguracji. Chociaż można utworzyć podsieci bramy tak małej jak/29, zaleca się tworzenie podsieci bramy/28 i większych (/ 28, / 27, / 26 itp.) W ten sposób w przypadku dodania funkcji w przyszłości, nie masz trwające bramy, a następnie usunięcie i ponowne utworzenie podsieci bramy, aby umożliwić większej liczby adresów IP.

W poniższym przykładzie programu PowerShell usługi Resource Manager zawiera podsieć bramy o nazwie GatewaySubnet. Widać, że notacja CIDR Określa rozmiar/27, który daje wystarczającą liczbę adresów IP w przypadku większości konfiguracji, które obecnie istnieją.

```PowerShell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> Podczas pracy z podsieciami bramy należy unikać kojarzenia sieciowej grupy zabezpieczeń (NSG, Network Security Group) z podsiecią bramy. Skojarzenie sieciowej grupy zabezpieczeń z tą podsiecią spowoduje, że brama sieci VPN przestanie działać zgodnie z oczekiwaniami. Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [co to jest sieciowa grupa zabezpieczeń?](/azure/virtual-network/virtual-networks-nsg).

### <a name="local-network-gateways"></a>Bramy sieci lokalnej

Podczas tworzenia konfiguracji bramy sieci VPN na platformie Azure, Brama sieci lokalnej reprezentuje często Twojej lokalizacji lokalnej. W usłudze Azure Stack reprezentuje zdalne urządzenie sieci VPN znajduje się poza usługi Azure Stack. Może to być urządzenia sieci VPN w taki sposób, w centrum danych (lub zdalne centrum danych) lub bramy sieci VPN na platformie Azure.

Nazwij bramy sieci lokalnej, publiczny adres IP urządzenia sieci VPN i określić prefiksy adresów, które znajdują się w lokalizacji lokalnej. Azure porównaniem z prefiksy adresów docelowych dla ruchu sieciowego, konsultacje dotyczące konfiguracji, określony dla bramy sieci lokalnej i w związku z tym kieruje pakiety.

Następny przykład programu PowerShell tworzy nową bramę sieci lokalnej:

```PowerShell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Czasami zachodzi potrzeba modyfikowanie ustawień bramy sieci lokalnej. Na przykład podczas dodawania lub modyfikowania zakres adresów, lub jeśli zmieni się adres IP urządzenia sieci VPN. Zobacz [modyfikowanie ustawień lokalnej bramy sieci przy użyciu programu PowerShell](/azure/vpn-gateway/vpn-gateway-modify-local-network-gateway).

## <a name="ipsecike-parameters"></a>Parametry protokołu IPsec/IKE

Po skonfigurowaniu połączenia sieci VPN w usłudze Azure Stack musisz skonfigurować połączenie obu końców.  Jeśli konfigurujesz połączenie sieci VPN między usługi Azure Stack i urządzenia sprzętowego, takie jak przełącznik lub router, który działa jako brama sieci VPN tego urządzenia może poprosić dodatkowe ustawienia.

W przeciwieństwie do platformy Azure, który obsługuje wiele ofert zarówno jako inicjator i obiekt odpowiadający w trybie, usługa Azure Stack obsługuje tylko jedną ofertę.

### <a name="ike-phase-1-main-mode-parameters"></a>Parametry protokołu IKE — faza 1 (tryb główny)

| Właściwość              | Wartość|
|-|-|
| Wersja IKE           | IKEv2 |
|Grupa Diffie’ego-Hellmana   | Grupa 2 (1024 bity) |
| Metoda uwierzytelniania | Klucz wstępny |
|Szyfrowanie i algorytmy wyznaczania wartości skrótu | AES256, SHA256 |
|Okres istnienia skojarzeń zabezpieczeń (czas)     | 28 800 sekund|

### <a name="ike-phase-2-quick-mode-parameters"></a>Parametry protokołu IKE — faza 2 (tryb szybki)

| Właściwość| Wartość|
|-|-|
|Wersja IKE |IKEv2 |
|Oferty szyfrowania i wyznaczania wartości skrótu algorytmy (szyfrowanie)     | GCMAES256|
|Oferty szyfrowania i (uwierzytelnianie) algorytmy wyznaczania wartości skrótu | GCMAES256|
|Okres istnienia skojarzeń zabezpieczeń (czas)  | 27 000 sekund  |
|Okres istnienia skojarzeń zabezpieczeń (bajty) | 33,553,408     |
|Doskonałe utajnienie przekazywania (PFS) |Brak<sup>patrz Uwaga 1</sup> |
|Wykrywanie nieaktywnych elementów równorzędnych | Obsługiwane|  

* *Uwaga 1:* wcześniejszymi niż wersja 1807 usługi Azure Stack używa wartości PFS2048 dla Perfect Forward Secrecy przekazywania (PFS).

## <a name="next-steps"></a>Kolejne kroki

[Nawiązywanie połączenia przy użyciu usługi ExpressRoute](azure-stack-connect-expressroute.md)
