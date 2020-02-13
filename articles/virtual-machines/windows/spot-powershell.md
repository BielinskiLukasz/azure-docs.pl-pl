---
title: Wdrażanie maszyn wirtualnych na platformie Azure za pomocą programu PowerShell
description: Dowiedz się, w jaki sposób używać Azure PowerShell do wdrażania maszyn wirtualnych w celu oszczędzania kosztów.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 17186d1d7b50ea872dc47eca8c2c4491787d2a38
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77158948"
---
# <a name="preview-deploy-spot-vms-using-azure-powershell"></a>Wersja zapoznawcza: wdrażanie maszyn wirtualnych przy użyciu Azure PowerShell


Korzystanie z [maszyn wirtualnych na miejscu](spot-vms.md) pozwala korzystać z nieużywanej pojemności przy znaczącym obciążeniu kosztów. W dowolnym momencie, gdy platforma Azure wymaga przywrócenia pojemności, infrastruktura platformy Azure wyłączy maszyny wirtualne. W związku z tym maszyny wirtualne są doskonałe dla obciążeń, które mogą obsłużyć przerwy, takie jak zadania przetwarzania wsadowego, środowiska deweloperskie/testowe, duże obciążenia obliczeniowe i inne.

Ceny maszyn wirtualnych na miejscu są zmienne, na podstawie regionu i jednostki SKU. Aby uzyskać więcej informacji, zobacz cennik maszyn wirtualnych dla [systemów](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i Windows. Aby uzyskać więcej informacji na temat ustawiania ceny maksymalnej, zobacz [punkt maszyny wirtualne — Cennik](spot-vms.md#pricing).

Dla maszyny wirtualnej można ustawić maksymalną cenę, która ma być płacona za godzinę. Maksymalna cena maszyny wirtualnej na miejscu może być ustawiona w dolarach amerykańskich (USD) przy użyciu maksymalnie 5 miejsc dziesiętnych. Na przykład wartość `0.98765`będzie cena maksymalna $0,98765 USD za godzinę. Jeśli ustawisz maksymalną wartość `-1`, maszyna wirtualna nie zostanie wykluczona na podstawie ceny. Cena maszyny wirtualnej to aktualna cena za ilość miejsca lub cena standardowej maszyny wirtualnej, która kiedykolwiek jest mniejsza, o ile jest dostępna pojemność i przydział.

> [!IMPORTANT]
> Wystąpienia punktowe są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>



## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz element spotVM przy użyciu polecenia [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) , aby utworzyć konfigurację. Uwzględnij `-Priority Spot` i ustaw `-MaxPrice` na:
- `-1` tak, aby maszyna wirtualna nie została wykluczona na podstawie ceny.
- kwota dolara (do 5 cyfr). Na przykład `-MaxPrice .98765` oznacza, że zostanie cofnięta alokacja maszyny wirtualnej po cenie za spotVM o godzinie 98765 USD.


Ten przykład tworzy spotVM, które nie zostaną cofnięte w oparciu o Cennik (tylko wtedy, gdy platforma Azure wymaga powrotu do tyłu pojemności).

```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be a Spot VM

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1| `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Po utworzeniu maszyny wirtualnej można wykonać zapytanie, aby zobaczyć maksymalną cenę dla wszystkich maszyn wirtualnych w grupie zasobów.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="next-steps"></a>Następne kroki

Możesz również utworzyć maszynę wirtualną na miejscu przy użyciu [interfejsu wiersza polecenia platformy Azure](../linux/spot-cli.md) lub [szablonu](../linux/spot-template.md).

Jeśli wystąpi błąd, zobacz [kody błędów](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).