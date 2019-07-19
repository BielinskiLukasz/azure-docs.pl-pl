---
title: Tworzenie strefy prywatnej usługi Azure DNS przy użyciu programu Azure PowerShell
description: W tym artykule opisano tworzenie i testowanie prywatnej strefy i rekordu DNS w Azure DNS. W tym szczegółowym przewodniku pokazano, jak po raz pierwszy utworzyć strefę prywatną i rekord DNS przy użyciu programu Azure PowerShell oraz zarządzać nimi.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 06/14/2019
ms.author: victorh
ms.openlocfilehash: 6603929fa7b4c597a846fc299577a9682d8f54e0
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854131"
---
# <a name="create-an-azure-dns-private-zone-using-azure-powershell"></a>Tworzenie strefy prywatnej usługi Azure DNS przy użyciu programu Azure PowerShell

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

W tym artykule przedstawiono kroki umożliwiające utworzenie po raz pierwszy prywatnej strefy i rekordu DNS przy użyciu programu Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Strefa DNS jest używana do hostowania rekordów DNS dla określonej domeny. Aby rozpocząć hostowanie domeny w usłudze Azure DNS, musisz utworzyć strefę DNS dla tej nazwy domeny. Każdy rekord DNS domeny zostanie utworzony w tej strefie DNS. Aby opublikować prywatną strefę DNS w sieci wirtualnej, musisz określić listę sieci wirtualnych, które mogą rozpoznawać rekordy w strefie.  Są one nazywane *połączonymi* sieciami wirtualnymi. Gdy Autorejestracja jest włączona, Azure DNS aktualizuje także rekordy strefy za każdym razem, gdy maszyna wirtualna jest tworzona, zmienia jej adres IP lub został usunięty.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie strefy prywatnej DNS
> * Tworzenie testowych maszyn wirtualnych
> * Tworzenie dodatkowego rekordu DNS
> * Testowanie strefy prywatnej

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Jeśli wolisz, możesz wykonać tę procedurę za pomocą [interfejsu wiersza polecenia platformy Azure](private-dns-getstarted-cli.md).

## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Najpierw utwórz grupę zasobów, która będzie zawierać strefę DNS: 

```azurepowershell
New-AzResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-dns-private-zone"></a>Tworzenie strefy prywatnej DNS

Strefa DNS jest tworzona za pomocą polecenia cmdlet `New-AzPrivateDnsZone`.

Poniższy przykład tworzy sieć wirtualną o nazwie **myAzureVNet**. Następnie tworzy strefę DNS o nazwie **Private.contoso.com** w grupie zasobów **MyAzureResourceGroup** , łączy strefę DNS z siecią wirtualną **MyAzureVnet** i włącza rejestrację automatyczną.

```azurepowershell
Install-Module -Name Az.PrivateDns -force

$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

$zone = New-AzPrivateDnsZone -Name private.contoso.com -ResourceGroupName MyAzureResourceGroup

$link = New-AzPrivateDnsVirtualNetworkLink -ZoneName private.contoso.com `
  -ResourceGroupName MyAzureResourceGroup -Name "mylink" `
  -VirtualNetworkId $vnet.id -EnableRegistration
```

Jeśli chcesz utworzyć strefę tylko do rozpoznawania nazw (bez automatycznej rejestracji nazwy hosta), możesz pominąć `-EnableRegistration` parametr.

### <a name="list-dns-private-zones"></a>Wyświetlanie listy stref prywatnych DNS

Pomijając nazwę strefy w elemencie `Get-AzPrivateDnsZone`, można wyliczyć wszystkie strefy w grupie zasobów. Ta operacja zwraca tablicę obiektów stref.

```azurepowershell
$zones = Get-AzPrivateDnsZone -ResourceGroupName MyAzureResourceGroup
$zones
```

Pomijając zarówno nazwę strefy, jak i nazwę grupy zasobów w elemencie `Get-AzPrivateDnsZone`, można wyliczyć wszystkie strefy w subskrypcji platformy Azure.

```azurepowershell
$zones = Get-AzPrivateDnsZone
$zones
```

## <a name="create-the-test-virtual-machines"></a>Tworzenie testowych maszyn wirtualnych

Teraz utworzysz dwie maszyny wirtualne, aby umożliwić przetestowanie strefy prywatnej DNS:

```azurepowershell
New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM01" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389

New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM02" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389
```

Ukończenie tej operacji potrwa kilka minut.

## <a name="create-an-additional-dns-record"></a>Tworzenie dodatkowego rekordu DNS

Zestawy rekordów są tworzone za pomocą polecenia cmdlet `New-AzPrivateDnsRecordSet`. Poniższy przykład tworzy rekord z względną nazwą **bazy danych** w strefie DNS **Private.contoso.com**w grupie zasobów **MyAzureResourceGroup**. W pełni kwalifikowana nazwa zestawu rekordów to **DB.private.contoso.com**. Typ rekordu to „A” z adresem IP „10.2.0.4”, a czas wygaśnięcia wynosi 3600 sekund.

```azurepowershell
New-AzPrivateDnsRecordSet -Name db -RecordType A -ZoneName private.contoso.com `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>Wyświetlanie rekordów DNS

Aby wyświetlić listę rekordów DNS w strefie, uruchom polecenie:

```azurepowershell
Get-AzPrivateDnsRecordSet -ZoneName private.contoso.com -ResourceGroupName MyAzureResourceGroup
```

## <a name="test-the-private-zone"></a>Testowanie strefy prywatnej

Teraz można testować rozpoznawanie nazw dla strefy prywatnej **Private.contoso.com** .

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurowanie maszyn wirtualnych w celu zezwolenia na ruch przychodzący protokołu ICMP

Do przetestowania rozpoznawania nazw możesz użyć polecenia ping. W tym celu skonfiguruj zaporę na obydwu maszynach wirtualnych, aby zezwolić na przychodzące pakiety protokołu ICMP.

1. Połącz się z maszyną wirtualną myVM01 i otwórz okno programu Windows PowerShell, używając uprawnień administratora.
2. Uruchom następujące polecenie:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Powtórz dla maszyny wirtualnej myVM02.

### <a name="ping-the-vms-by-name"></a>Wysyłanie polecenia ping do maszyn wirtualnych według nazwy

1. W wierszu polecenia programu Windows PowerShell maszyny wirtualnej myVM02 wyślij polecenie ping do maszyny wirtualnej myVM01 przy użyciu automatycznie zarejestrowanej nazwy hosta:

   ```
   ping myVM01.private.contoso.com
   ```

   Powinny pojawić się dane wyjściowe podobne do następujących:

   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```

2. Teraz wyślij polecenie ping do utworzonej wcześniej nazwy **db**:

   ```
   ping db.private.contoso.com
   ```

   Powinny pojawić się dane wyjściowe podobne do następujących:

   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milliseconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Usuwanie wszystkich zasobów

Gdy nie jest już potrzebne, Usuń grupę zasobów **MyAzureResourceGroup** , aby usunąć zasoby utworzone w tym artykule.

```azurepowershell
Remove-AzResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym artykule wdrożono prywatną strefę DNS, utworzono rekord DNS i przetestowano strefę.
Następnie możesz dowiedzieć się więcej na temat stref prywatnych DNS.

* [Używanie usługi Azure DNS na potrzeby domen prywatnych](private-dns-overview.md)
