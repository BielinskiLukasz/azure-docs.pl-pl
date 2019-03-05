---
title: Za pomocą usługi Azure Application Gateway przy użyciu wewnętrznego modułu równoważenia obciążenia — PowerShell | Dokumentacja firmy Microsoft
description: Ta strona zawiera instrukcje dotyczące tworzenia, konfigurowania, uruchamiania i usuwania bramy aplikacji platformy Azure za pomocą wewnętrznego modułu równoważenia obciążenia na potrzeby usługi Azure Resource Manager
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 75cfd5a2-e378-4365-99ee-a2b2abda2e0d
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2018
ms.author: victorh
ms.openlocfilehash: 3b9108e08e1b1ad13fac75d00816755043d84672
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2019
ms.locfileid: "57308724"
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Tworzenie bramy aplikacji przy użyciu wewnętrznego modułu równoważenia obciążenia (ILB)

Usługę Azure Application Gateway można skonfigurować z internetowym wirtualnym adresem IP lub wewnętrznym punktem końcowym niepołączonym z Internetem, znanym także jako punkt końcowy wewnętrznego modułu równoważenia obciążenia. Konfigurowanie bramy przy użyciu wewnętrznego modułu równoważenia obciążenia jest pomocne w przypadku wewnętrznych aplikacji LOB niepołączonych z Internetem. Ta opcja jest również przydatna w przypadku usług i warstw w aplikacji wielowarstwowej, która znajduje się w granicach zabezpieczeń bez połączenia z Internetem, ale nadal wymaga dystrybucji obciążenia z działaniem okrężnym, lepkości sesji lub zakończenia protokołu SSL (Secure Sockets Layer).

W tym artykule przeprowadzimy Cię przez proces konfigurowania bramy aplikacji przy użyciu wewnętrznego modułu równoważenia obciążenia.

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Zainstaluj najnowszą wersję modułu Azure PowerShell, wykonując [instrukcje dotyczące instalacji](/powershell/azure/install-az-ps).
2. Utworzysz sieć wirtualną i podsieć dla usługi Application Gateway. Upewnij się, że z podsieci nie korzystają żadne maszyny wirtualne ani wdrożenia w chmurze. Usługa Application Gateway musi sama znajdować się w podsieci sieci wirtualnej.
3. Serwery konfigurowane do używania bramy aplikacji muszą być umieszczone w sieci wirtualnej lub z przypisanym adresem IP/VIP lub mieć w niej utworzone punkty końcowe.

## <a name="what-is-required-to-create-an-application-gateway"></a>Co jest wymagane do utworzenia bramy aplikacji?

* **Pula serwerów zaplecza:** lista adresów IP serwerów zaplecza. Adresy IP na liście powinny należeć do sieci wirtualnej, ale w innej podsieci bramy aplikacji, lub być publicznymi bądź wirtualnymi adresami IP.
* **Ustawienia puli serwerów zaplecza:** każda pula ma ustawienia, takie jak port, protokół i koligacja oparta na plikach cookie. Te ustawienia są powiązane z pulą i są stosowane do wszystkich serwerów w tej puli.
* **Port frontonu:** port publiczny, który jest otwierany w bramie aplikacji. Ruch trafia do tego portu, a następnie jest przekierowywany do jednego z serwerów zaplecza.
* **Odbiornik:** Odbiornik ma port frontonu, protokół (Http lub Https, te jest rozróżniana wielkość liter) oraz nazwę certyfikatu SSL (w przypadku konfigurowania odciążania protokołu SSL).
* **Reguła:** reguła wiąże odbiornik z pulą serwerów zaplecza i umożliwia zdefiniowanie, do której puli serwerów zaplecza ma być przekierowywany ruch w przypadku trafienia do określonego odbiornika. Obecnie jest obsługiwana tylko reguła *podstawowa*. Reguła *podstawowa* to dystrybucja obciążenia z działaniem okrężnym.

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Różnica między klasycznym modelem wdrożenia Azure i usługą Azure Resource Manager polega na kolejności tworzenia bramy aplikacji i elementów, które należy skonfigurować.
W usłudze Resource Manager wszystkie elementy składające się na bramę aplikacji są konfigurowane osobno, a następnie składane w celu utworzenia zasobu bramy aplikacji.

Oto kroki wymagane do utworzenia bramy aplikacji:

1. Tworzenie grupy zasobów dla usługi Resource Manager
2. Tworzenie sieci wirtualnej i podsieci dla bramy aplikacji
3. Tworzenie obiektu konfiguracji bramy aplikacji
4. Tworzenie zasobu bramy aplikacji

## <a name="create-a-resource-group-for-resource-manager"></a>Tworzenie grupy zasobów dla usługi Resource Manager

Upewnij się, że program PowerShell został przełączony do trybu umożliwiającego korzystanie z poleceń cmdlet usługi Azure Resource Manager. Więcej informacji znajduje się w artykule [Using Windows PowerShell with Resource Manager](../powershell-azure-resource-manager.md) (Używanie programu Windows PowerShell z usługą Resource Manager).

### <a name="step-1"></a>Krok 1

```powershell
Connect-AzAccount
```

### <a name="step-2"></a>Krok 2

Sprawdź subskrypcje dostępne na koncie.

```powershell
Get-AzSubscription
```

Zostanie wyświetlony monit o uwierzytelnienie przy użyciu własnych poświadczeń.

### <a name="step-3"></a>Krok 3

Wybierz subskrypcję platformy Azure do użycia.

```powershell
Select-AzSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Krok 4

Utwórz nową grupę zasobów (ten krok można pominąć, jeśli używasz istniejącej grupy zasobów).

```powershell
New-AzResourceGroup -Name appgw-rg -location "West US"
```

Usługa Azure Resource Manager wymaga, żeby wszystkie grupy zasobów miały lokalizację. Będzie ona używana jako domyślna lokalizacja zasobów w danej grupie. Upewnij się, że we wszystkich poleceniach służących do tworzenia bramy aplikacji jest używana ta sama grupa zasobów.

W powyższym przykładzie utworzyliśmy grupę zasobów o nazwie "appgw-rg" i lokalizacji "Zachodnie stany USA".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Tworzenie sieci wirtualnej i podsieci dla bramy aplikacji

W poniższym przykładzie pokazano, jak utworzyć sieć wirtualną przy użyciu usługi Resource Manager:

### <a name="step-1"></a>Krok 1

```powershell
$subnetconfig = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

W tym kroku przypisuje zakresu adresów 10.0.0.0/24 do zmiennej podsieci służącej do tworzenia sieci wirtualnej.

### <a name="step-2"></a>Krok 2

```powershell
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig
```

Ten krok umożliwia utworzenie sieci wirtualnej o nazwie "appgwvnet" w grupie zasobów "appgw-rg" dla regionu zachodnie stany USA, użyciu prefiksu 10.0.0.0/16 i podsieci 10.0.0.0/24.

### <a name="step-3"></a>Krok 3

```powershell
$subnet = $vnet.subnets[0]
```

Ten krok umożliwia przypisanie obiektu podsieci do zmiennej $subnet na potrzeby następnych kroków.

## <a name="create-an-application-gateway-configuration-object"></a>Tworzenie obiektu konfiguracji bramy aplikacji

### <a name="step-1"></a>Krok 1

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

Ten krok powoduje utworzenie konfiguracji adresu IP bramy aplikacji o nazwie "gatewayIP01". Uruchomiona usługa Application Gateway wybierze adres IP ze skonfigurowanej podsieci i skieruje ruch sieciowy do adresów IP w puli adresów IP zaplecza. Pamiętaj, że każde wystąpienie będzie mieć jeden adres IP.

### <a name="step-2"></a>Krok 2

```powershell
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.1.1.8,10.1.1.9,10.1.1.10
```

Ten krok obejmuje skonfigurowanie puli adresów IP zaplecza o nazwie "pool01" z adresem IP adresów "10.1.1.8 10.1.1.9, 10.1.1.10". Są to adresy IP odbierające ruch sieciowy pochodzący z punktu końcowego adresu IP frontonu. Powyższe adresy IP można zastąpić własnymi dodawanymi punktami końcowymi adresów IP aplikacji.

### <a name="step-3"></a>Krok 3

```powershell
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Ten krok umożliwia skonfigurowanie ruch sieciowy bramy ustawienie "poolsetting01" dla obciążenia zrównoważone aplikacji w puli zaplecza.

### <a name="step-4"></a>Krok 4

```powershell
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

Ten krok umożliwia skonfigurowanie portu adresu IP frontonu o nazwie "frontendport01" dla wewnętrznego modułu równoważenia obciążenia.

### <a name="step-5"></a>Krok 5

```powershell
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet
```

Ten krok powoduje utworzenie konfiguracji adresów IP frontonu o nazwie "fipconfig01" i kojarzy ją z prywatnym adresem IP z bieżącej podsieci sieci wirtualnej.

### <a name="step-6"></a>Krok 6

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

Ten krok umożliwia utworzenie odbiornika o nazwie "listener01" i skojarzenie portu frontonu z konfiguracją IP frontonu.

### <a name="step-7"></a>Krok 7

```powershell
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

Spowoduje to utworzenie reguły routingu modułu równoważenia obciążenia o nazwie "rule01", która służy do konfigurowania zachowania modułu równoważenia obciążenia.

### <a name="step-8"></a>Krok 8

```powershell
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

Ten krok umożliwia skonfigurowanie rozmiaru wystąpienia bramy aplikacji.

> [!NOTE]
> Wartością domyślną dla pojemności to 2. Nazwa jednostki Sku można wybrać między Standard_Small, Standard_Medium i Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Tworzenie bramy aplikacji przy użyciu polecenia New-AzureApplicationGateway

Umożliwia utworzenie bramy aplikacji przy użyciu wszystkich elementów konfiguracji z poprzednich kroków. W tym przykładzie brama aplikacji ma nazwę „appgwtest”.

```powershell
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Ten krok umożliwia utworzenie bramy aplikacji przy użyciu wszystkich elementów konfiguracji z poprzednich kroków. W przykładzie brama aplikacji ma nazwę „appgwtest”.

## <a name="delete-an-application-gateway"></a>Usuwanie bramy aplikacji

Aby usunąć bramę aplikacji, należy wykonać następujące kroki w kolejności:

1. Użyj polecenia cmdlet `Stop-AzApplicationGateway`, aby zatrzymać bramę.
2. Użyj polecenia cmdlet `Remove-AzApplicationGateway`, aby usunąć bramę.
3. Aby sprawdzić, czy brama została usunięta, użyj polecenia cmdlet `Get-AzureApplicationGateway`.

### <a name="step-1"></a>Krok 1

Pobierz obiekt bramy aplikacji i skojarz go ze zmienną „$getgw”.

```powershell
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Krok 2

Użyj polecenia `Stop-AzApplicationGateway`, aby zatrzymać bramę aplikacji. W tym przykładzie pokazano `Stop-AzApplicationGateway` polecenia cmdlet w pierwszym wierszu, a następnie dane wyjściowe.

```powershell
Stop-AzApplicationGateway -ApplicationGateway $getgw  
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

Po zatrzymaniu bramy aplikacji użyj polecenia cmdlet `Remove-AzApplicationGateway`, aby usunąć usługę.

```powershell
Remove-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force
```

```
VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301
```

> [!NOTE]
> Przełącznik **-force** umożliwia pomijanie komunikatu potwierdzającego usunięcie.

Aby sprawdzić, czy usługa została usunięta, możesz użyć polecenia cmdlet `Get-AzApplicationGateway`. Ten krok nie jest wymagany.

```powershell
Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz skonfigurować odciążanie protokołu SSL, zobacz artykuł [Configure an application gateway for SSL offload](application-gateway-ssl.md) (Konfigurowanie bramy aplikacji na potrzeby odciążania protokołu SSL).

Jeśli chcesz skonfigurować bramę aplikacji do użycia z wewnętrznym modułem równoważenia obciążenia, zobacz artykuł [Create an application gateway with an internal load balancer (ILB)](application-gateway-ilb.md) (Tworzenie bramy aplikacji z wewnętrznym modułem równoważenia obciążenia).

Więcej ogólnych informacji na temat opcji równoważenia obciążenia możesz znaleźć w następujących artykułach:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

