---
title: 'Usuwanie bramy sieci wirtualnej: klasyczny Azure'
description: Usuń bramę sieci wirtualnej przy użyciu programu PowerShell w klasycznym modelu wdrażania.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/11/2017
ms.author: cherylmc
ms.openlocfilehash: 0ff8e42cecb705e57ce85c92e84a0ad9b78929a5
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75778502"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Usuwanie bramy sieci wirtualnej przy użyciu programu PowerShell (wersja klasyczna)

> [!div class="op_single_selector"]
> * [Resource Manager — witryna Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager — program PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Klasyczny — PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>

W tym artykule opisano usuwanie bramy sieci VPN w klasycznym modelu wdrażania przy użyciu programu PowerShell. Po usunięciu bramy sieci wirtualnej zmodyfikuj plik konfiguracji sieci, aby usunąć elementy, które nie są już używane.

## <a name="connect"></a>Krok 1. Nawiązywanie połączenia z platformą Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Zainstaluj najnowsze polecenia cmdlet programu PowerShell.

Pobierz i zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell dla usługi Azure Service Management (SM). Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Nawiąż połączenie z kontem platformy Azure. 

Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Add-AzureAccount
```

## <a name="export"></a>Krok 2. Eksportowanie i wyświetlanie pliku konfiguracji sieci

Utwórz katalog na komputerze, a następnie wyeksportuj plik konfiguracji sieci do tego katalogu. Ten plik jest używany do wyświetlania bieżących informacji o konfiguracji, a także do modyfikowania konfiguracji sieci.

W tym przykładzie plik konfiguracji sieci zostanie wyeksportowany do katalogu C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Otwórz plik z edytorem tekstu i Wyświetl nazwę klasycznej sieci wirtualnej. Po utworzeniu sieci wirtualnej w Azure Portal pełna nazwa użyta przez platformę Azure nie jest widoczna w portalu. Na przykład Sieć wirtualna o nazwie "ClassicVNet1" w Azure Portal może mieć znacznie dłuższą nazwę w pliku konfiguracyjnym sieci. Nazwa może wyglądać następująco: "Group ClassicRG1 ClassicVNet1". Nazwy sieci wirtualnych są wyświetlane jako **"VirtualNetworkSite Name ="** . Podczas uruchamiania poleceń cmdlet programu PowerShell Użyj nazw w pliku konfiguracji sieci.

## <a name="delete"></a>Krok 3. Usuwanie bramy sieci wirtualnej

Po usunięciu bramy sieci wirtualnej wszystkie połączenia z siecią wirtualną za pomocą bramy są odłączone. Jeśli masz klientów P2S podłączonych do sieci wirtualnej, zostaną one rozłączone bez ostrzeżenia.

Ten przykład usuwa bramę sieci wirtualnej. Upewnij się, że w pliku konfiguracji sieci użyto pełnej nazwy sieci wirtualnej.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

Jeśli to się powiedzie, zwraca:

```
Status : Successful
```

## <a name="modify"></a>Krok 4. Modyfikowanie pliku konfiguracji sieci

Po usunięciu bramy sieci wirtualnej polecenie cmdlet nie modyfikuje pliku konfiguracji sieci. Należy zmodyfikować plik, aby usunąć elementy, które nie są już używane. Poniższe sekcje ułatwiają modyfikację pobranego pliku konfiguracji sieci.

### <a name="lnsref"></a>Odwołania do lokacji sieci lokalnej

Aby usunąć informacje o odwołaniu do lokacji, wprowadź zmiany w konfiguracji **ConnectionsToLocalNetwork/LocalNetworkSiteRef**. Usunięcie odwołania do lokacji lokalnej wyzwala platformę Azure w celu usunięcia tunelu. W zależności od utworzonej konfiguracji możesz nie mieć **LocalNetworkSiteRef** na liście.

```
<Gateway>
   <ConnectionsToLocalNetwork>
     <LocalNetworkSiteRef name="D1BFC9CB_Site2">
       <Connection type="IPsec" />
     </LocalNetworkSiteRef>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

Przykład:

```
<Gateway>
   <ConnectionsToLocalNetwork>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

### <a name="lns"></a>Lokalne lokacje sieciowe

Usuń wszystkie lokacje lokalne, których już nie używasz. W zależności od utworzonej konfiguracji można nie mieć **LocalNetworkSite** na liście.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
  <LocalNetworkSite name="Site3">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>57.179.18.164</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

W tym przykładzie usunięto tylko Site3.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

### <a name="clientaddresss"></a>Pula adresów klienta

Jeśli masz połączenie P2S z siecią wirtualną, będziesz mieć **VPNClientAddressPool**. Usuń pule adresów klientów odpowiadające usuniętej bramie sieci wirtualnej.

```
<Gateway>
    <VPNClientAddressPool>
      <AddressPrefix>10.1.0.0/24</AddressPrefix>
    </VPNClientAddressPool>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

Przykład:

```
<Gateway>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

### <a name="gwsub"></a>GatewaySubnet

Usuń **GatewaySubnet** odnoszące się do sieci wirtualnej.

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
   <Subnet name="GatewaySubnet">
     <AddressPrefix>10.11.1.0/29</AddressPrefix>
   </Subnet>
 </Subnets>
```

Przykład:

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
 </Subnets>
```

## <a name="upload"></a>Krok 5. Przekazywanie pliku konfiguracji sieci

Zapisz zmiany i Przekaż plik konfiguracji sieci na platformę Azure. Upewnij się, że ścieżka pliku jest zmieniana w zależności od potrzeb środowiska.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Jeśli to się powiedzie, zwracany jest podobny do przedstawionego w tym przykładzie:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
```
