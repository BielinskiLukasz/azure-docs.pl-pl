---
title: 'Samouczek: Tworzenie i testowanie bramy translatora adresów sieciowych — Azure PowerShell'
titlesuffix: Azure Virtual Network NAT
description: W tym samouczku pokazano, jak utworzyć bramę NAT przy użyciu Azure PowerShell i przetestować usługę translatora adresów sieciowych
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: bb75631beed73a6ebd9d1cf2c00c375726fed387
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586992"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell-and-test-the-nat-service"></a>Samouczek: Tworzenie bramy NAT przy użyciu Azure PowerShell i testowanie usługi translatora adresów sieciowych

W tym samouczku utworzysz bramę translatora adresów sieciowych, aby zapewnić łączność wychodzącą dla maszyn wirtualnych na platformie Azure. Aby przetestować bramę NAT, należy wdrożyć źródłową i docelową maszynę wirtualną. Przetestujesz bramę translatora adresów sieciowych, wykonując połączenia wychodzące z publicznym adresem IP. Te połączenia będą pochodzić z lokalizacji źródłowej dla docelowej maszyny wirtualnej. W tym samouczku przedstawiono lokalizację źródłową i docelową dwóch różnych sieci wirtualnych w tej samej grupie zasobów dla uproszczenia.

>[!NOTE] 
>Usługa Azure Virtual Network translator adresów sieciowych jest teraz dostępna jako publiczna wersja zapoznawcza i dostępna w ograniczonym zestawie [regionów](./nat-overview.md#region-availability). Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ten samouczek można wykonać przy użyciu Azure Cloud Shell lub uruchomić odpowiednie polecenia lokalnie.  Jeśli nie korzystasz z Azure Cloud Shell, [Zaloguj się teraz](https://shell.azure.com).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](https://docs.microsoft.com/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie **myResourceGroupNAT** w lokalizacji **eastus2** :


```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>Tworzenie bramy translatora adresów sieciowych

### <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Aby uzyskać dostęp do Internetu, wymagany jest co najmniej jeden publiczny adres IP dla bramy translatora adresów sieciowych. Użyj [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) , aby utworzyć zasób publicznego adresu IP o nazwie **myPublicIPsource** w **myResourceGroupNAT**. Wynik tego polecenia będzie przechowywany w zmiennej o nazwie **$publicIPsource** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pips = 'myPublicIPsource'
$alm = 'Static'
$sku = 'Standard'

$publicIPsource = 
New-AzPublicIpAddress -Name $pips -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Tworzenie publicznego prefiksu adresu IP

 Użyj [New-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) , aby utworzyć zasób publicznego PREFIKSU adresu IP o nazwie **myPublicIPprefixsource** w **myResourceGroupNAT**.  Wynik tego polecenia będzie przechowywany w zmiennej o nazwie **$publicIPPrefixsource** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$prips = 'mypublicIPprefixsource'

$publicIPPrefixsource = 
New-AzPublicIpPrefix -Name $prips -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Tworzenie zasobu bramy NAT

W tej sekcji szczegółowo opisano, jak utworzyć i skonfigurować następujące składniki usługi NAT przy użyciu zasobu bramy translatora adresów sieciowych:
  - Publiczna Pula adresów IP i publiczny prefiks IP do użycia dla przepływów wychodzących przetłumaczonych przez zasób bramy translatora adresów sieciowych.
  - Zmień limit czasu bezczynności z wartości domyślnej wynoszącej 4 minuty na 10 minut.

Utwórz globalną bramę usługi Azure NAT przy użyciu elementu [New-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway). Wynik tego polecenia spowoduje utworzenie zasobu bramy o nazwie **myNATgateway** , który używa publicznego adresu IP **myPublicIPsource** i publicznego prefiksu adresu IP **myPublicIPprefixsource**. Limit czasu bezczynności jest ustawiony na 10 minut.  Wynik tego polecenia będzie przechowywany w zmiennej o nazwie **$natGateway** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$nnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $nnm -ResourceGroupName $rsg -PublicIpAddress $publicIPsource -PublicIpPrefix $publicIPPrefixsource -Location $loc -Sku $sku -IdleTimeoutInMinutes 10      
  ```

W tym momencie Brama translatora adresów sieciowych jest funkcjonalna i nie ma potrzeby konfigurowania podsieci sieci wirtualnej.

## <a name="prepare-the-source-for-outbound-traffic"></a>Przygotuj Źródło dla ruchu wychodzącego

Przeprowadzimy Cię przez proces instalacji pełnego środowiska testowego. Należy skonfigurować test przy użyciu narzędzi typu "open source", aby zweryfikować bramę translatora adresów sieciowych. Zaczniemy od źródła, które będzie używać wcześniej utworzonej bramy translatora adresów sieciowych.

### <a name="configure-virtual-network-for-source"></a>Skonfiguruj sieć wirtualną dla źródła

Utwórz sieć wirtualną i skojarz ją z bramą.

Utwórz sieć wirtualną o nazwie **myVnetsource** z podsiecią o nazwie **mySubnetsource** przy użyciu polecenia [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) w **myResourceGroupNAT** przy użyciu polecenia [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). Przestrzeń adresów IP dla sieci wirtualnej to **192.168.0.0/16**. Podsieć w sieci wirtualnej to **192.168.0.0/24**.  Wyniki poleceń będą przechowywane w zmiennych o nazwie **$subnetsource** i **$vnetsource** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$sbn = 'mySubnetsource'
$spfx = '192.168.0.0/24'
$vnm = 'myVnetsource'
$vpfx = '192.168.0.0/16'
$loc = 'eastus2'

$subnetsource = 
New-AzVirtualNetworkSubnetConfig -Name $sbn -AddressPrefix $spfx -NatGateway $natGateway

$vnetsource = 
New-AzVirtualNetwork -Name $vnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnet
```

Cały ruch wychodzący do miejsc docelowych w Internecie używa teraz usługi translatora adresów sieciowych.  Nie trzeba konfigurować UDR.

Aby można było przetestować bramę NAT, musimy utworzyć źródłową maszynę wirtualną.  Przypiszemy zasób publicznego adresu IP jako publiczny adres IP na poziomie wystąpienia, aby uzyskać dostęp do tej maszyny wirtualnej z zewnątrz. Ten adres jest używany tylko w celu uzyskania dostępu do niego dla testu.  Pokazujemy, jak usługa translatora adresów sieciowych ma pierwszeństwo przed innymi opcjami wychodzącymi.

Możesz również utworzyć tę maszynę wirtualną bez publicznego adresu IP i utworzyć kolejną maszynę wirtualną do użycia jako serwera przesiadkowego bez publicznego adresu IP jako ćwiczenia.

### <a name="create-public-ip-for-source-vm"></a>Utwórz publiczny adres IP dla źródłowej maszyny wirtualnej

Tworzymy publiczny adres IP, który będzie używany do uzyskiwania dostępu do maszyny wirtualnej.  Użyj [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) , aby utworzyć zasób publicznego adresu IP o nazwie **myPublicIPVM** w **myResourceGroupNAT**.  Wynik tego polecenia będzie przechowywany w zmiennej o nazwie **$publicIpsourceVM** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pipvm = 'myPublicIpsourceVM'
$alm = 'Static'

$publicIpsourceVM = 
New-AzPublicIpAddress -Name $pipvm -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Tworzenie sieciowej grupy zabezpieczeń i Uwidacznianie punktu końcowego SSH dla maszyny wirtualnej

Ze względu na to, że standardowe publiczne adresy IP są "zabezpieczone domyślnie", tworzymy sieciowej grupy zabezpieczeń, aby zezwolić na dostęp przychodzący do protokołu SSH. Usługa translatora adresów sieciowych jest ukierunkowana na kierunek przepływu. Ta sieciowej grupy zabezpieczeń nie zostanie użyta dla ruchu wychodzącego, gdy Brama translatora adresów sieciowych zostanie skonfigurowana w tej samej podsieci. Użyj [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) , aby utworzyć zasób sieciowej grupy zabezpieczeń o nazwie **myNSGsource**. Użyj [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) , aby utworzyć regułę sieciowej grupy zabezpieczeń dla dostępu SSH o nazwie **SSH** w **myResourceGroupNAT**. Wynik tego polecenia zostanie zapisany w zmiennej o nazwie **$nsgsource** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$rnm = 'ssh'
$rdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$nsnm = 'myNSGsource'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$nsgsource = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-source-vm"></a>Utwórz kartę sieciową dla źródłowej maszyny wirtualnej

Utwórz interfejs sieciowy przy użyciu elementu [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) o nazwie **myNicsource**. To polecenie spowoduje skojarzenie publicznego adresu IP z grupą zabezpieczeń sieci. Wynik tego polecenia będzie przechowywany w zmiennej o nazwie **$nicsource** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nin = 'myNicsource'

$nicsource = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nin -NetworkSecurityGroupID $nsgsource.Id -PublicIPAddressID $publicIPVMsource.Id -SubnetID $vnetsource.Subnets[0].Id -Location $loc
```

### <a name="create-a-source-vm"></a>Tworzenie źródłowej maszyny wirtualnej

#### <a name="create-ssh-key-pair"></a>Tworzenie pary kluczy SSH

Do wykonania kroków tego przewodnika Szybki start konieczne jest posiadanie pary kluczy SSH. Jeśli masz już parę kluczy SSH, możesz pominąć ten krok.

Użyj protokołu ssh-keygen do utworzenia pary kluczy SSH.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Aby uzyskać bardziej szczegółowe informacje na temat tworzenia par kluczy SSH, łącznie z użyciem programu PuTTy, zobacz [Jak używać kluczy SSH w systemie Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

W przypadku utworzenia pary kluczy SSH przy użyciu Cloud Shell para kluczy jest przechowywana w obrazie kontenera. To [konto magazynu jest tworzone automatycznie](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Nie usuwaj konta magazynu ani udziału plików w programie, aż po pobraniu kluczy.

#### <a name="create-vm-configuration"></a>Utwórz konfigurację maszyny wirtualnej

Aby utworzyć maszynę wirtualną w programie PowerShell, należy utworzyć konfigurację obejmującą ustawienia, takie jak obraz do użycia, rozmiar i opcje uwierzytelniania. Następnie konfiguracja jest używana do utworzenia maszyny wirtualnej.

Zdefiniuj poświadczenia protokołu SSH, informacje o systemie operacyjnym i rozmiar maszyny wirtualnej. W tym przykładzie klucz SSH jest przechowywany w ~/.ssh/id_rsa. pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmn = 'myVMsource'
$vms = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigsource = 
New-AzVMConfig -VMName $vmn -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigsource -Linux -ComputerName $vmn -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigsource -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigsource -Id $nicsource.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigsource -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Połącz definicje konfiguracji, aby utworzyć maszynę wirtualną o nazwie **myVMsource** z opcją [New-AzVM]((https://docs.microsoft.com/powershell/module/az.compute/new-azvm?view=azps-2.8.0)) w **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigsource
```

Gdy polecenie zostanie zwrócone natychmiast, wdrożenie maszyny wirtualnej może potrwać kilka minut.

## <a name="prepare-destination-for-outbound-traffic"></a>Przygotuj miejsce docelowe dla ruchu wychodzącego

Teraz utworzysz miejsce docelowe dla ruchu wychodzącego przetłumaczonego przez usługę NAT, aby umożliwić jego przetestowanie.

### <a name="configure-virtual-network-for-destination"></a>Skonfiguruj sieć wirtualną dla miejsca docelowego

Musimy utworzyć sieć wirtualną, w której będzie docelowa maszyna wirtualna.  Te polecenia są takie same jak dla źródłowej maszyny wirtualnej. Dodano małe zmiany w celu udostępnienia docelowego punktu końcowego.

Utwórz sieć wirtualną o nazwie **myVnetdestination** z podsiecią o nazwie **mySubnetdestination** przy użyciu polecenia [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) w **myResourceGroupNAT** przy użyciu polecenia [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). Przestrzeń adresów IP dla sieci wirtualnej to **192.168.0.0/16**. Podsieć w sieci wirtualnej to **192.168.0.0/24**.  Wyniki poleceń będą przechowywane w zmiennych o nazwie **$subnetdestination** i **$vnetdestination** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sbdn = 'mySubnetdestination'
$spfx = '192.168.0.0/24'
$vdn = 'myVnetdestination'
$vpfx = '192.168.0.0/16'

$subnetdestination = 
New-AzVirtualNetworkSubnetConfig -Name $sbdn -AddressPrefix $spfx

$vnetdestination = 
New-AzVirtualNetwork -Name $vdn -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnetdestination
```

### <a name="create-public-ip-for-destination-vm"></a>Utwórz publiczny adres IP dla docelowej maszyny wirtualnej

Tworzymy publiczny adres IP, który będzie używany do uzyskiwania dostępu do źródłowej maszyny wirtualnej.  Użyj [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) , aby utworzyć zasób publicznego adresu IP o nazwie **myPublicIPdestinationVM** w **myResourceGroupNAT**.  Wynik tego polecenia będzie przechowywany w zmiennej o nazwie **$publicIpdestinationVM** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$all = 'Static'
$pipd = 'myPublicIPdestinationVM'

$publicIpdestinationVM = 
New-AzPublicIpAddress -Name $pipd -ResourceGroupName $rsg -AllocationMethod $all -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-and-http-endpoint-for-vm"></a>Tworzenie sieciowej grupy zabezpieczeń i udostępnianie punktu końcowego protokołu SSH i HTTP dla maszyny wirtualnej

Standardowe publiczne adresy IP są "zabezpieczone domyślnie". tworzymy sieciowej grupy zabezpieczeń, aby zezwolić na dostęp przychodzący do protokołu SSH. Użyj [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) , aby utworzyć zasób sieciowej grupy zabezpieczeń o nazwie **myNSGdestination**. Użyj [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) , aby utworzyć regułę sieciowej grupy zabezpieczeń dla dostępu SSH o nazwie **SSH**.  Użyj [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) , aby utworzyć regułę sieciowej grupy zabezpieczeń dla dostępu http o nazwie **http**. Obie reguły zostaną utworzone w **myResourceGroupNAT**. Wynik tego polecenia będzie przechowywany w zmiennej o nazwie **$nsgdestination** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$snm = 'ssh'
$sdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$hnm = 'http'
$hdsc = 'HTTP access'
$nsnm = 'myNSGdestination'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $snm -Description $sdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$httprule = 
New-AzNetworkSecurityRuleConfig -Name $hnm -Description $hdsc -Access $acc -Protocol $prt -Direction $dir -Priority 101 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$nsgdestination = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule,$httprule
```

### <a name="create-nic-for-destination-vm"></a>Utwórz kartę sieciową dla docelowej maszyny wirtualnej

Utwórz interfejs sieciowy przy użyciu elementu [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) o nazwie **myNicdestination**. To polecenie spowoduje skojarzenie z publicznym adresem IP i grupą zabezpieczeń sieci. Wynik tego polecenia będzie przechowywany w zmiennej o nazwie **$nicdestination** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nnm = 'myNicdestination'

$nicdestination = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nnm -NetworkSecurityGroupID $nsgdestination.Id -PublicIPAddressID $publicIPdestinationVM.Id -SubnetID $vnetdestination.Subnets[0].Id -Location $loc
```

### <a name="create-a-destination-vm"></a>Utwórz docelową maszynę wirtualną

#### <a name="create-vm-configuration"></a>Utwórz konfigurację maszyny wirtualnej

Aby utworzyć maszynę wirtualną w programie PowerShell, należy utworzyć konfigurację obejmującą ustawienia, takie jak obraz do użycia, rozmiar i opcje uwierzytelniania. Następnie konfiguracja jest używana do utworzenia maszyny wirtualnej.

Zdefiniuj poświadczenia protokołu SSH, informacje o systemie operacyjnym i rozmiar maszyny wirtualnej. W tym przykładzie klucz SSH jest przechowywany w ~/.ssh/id_rsa. pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$vmd = 'myVMdestination'
$vms = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigdestination = New-AzVMConfig -VMName $vmd -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigdestination -Linux -ComputerName $vmd -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigdestination -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigdestination -Id $nicdestination.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigdestination -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Połącz definicje konfiguracji, aby utworzyć maszynę wirtualną o nazwie **myVMdestination** z opcją [New-AzVM]((https://docs.microsoft.com/powershell/module/az.compute/new-azvm?view=azps-2.8.0)) w **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigdestination
```

Gdy polecenie zostanie zwrócone natychmiast, wdrożenie maszyny wirtualnej może potrwać kilka minut.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Przygotowywanie serwera sieci Web i ładunku testowego na docelowej maszynie wirtualnej

Najpierw musimy odnaleźć adres IP docelowej maszyny wirtualnej.  Aby uzyskać publiczny adres IP maszyny wirtualnej, użyj polecenie [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPdestinationVM'
  
Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Skopiuj publiczny adres IP, a następnie wklej go do Notatnika, aby można było go użyć w kolejnych krokach. Wskaż, że jest to docelowa maszyna wirtualna.

### <a name="sign-in-to-destination-vm"></a>Zaloguj się na docelowej maszynie wirtualnej

Poświadczenia SSH powinny być przechowywane w Cloud Shell z poprzedniej operacji.  Otwórz [Azure Cloud Shell](https://shell.azure.com) w przeglądarce. Użyj adresu IP pobranego w poprzednim kroku do połączenia SSH z maszyną wirtualną. 

```bash
ssh azureuser@<ip-address-destination>
```

Skopiuj i wklej następujące polecenia po zalogowaniu się.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

Te polecenia zaktualizują maszynę wirtualną, instalują Nginx i tworzą plik 100-kilobajtów. Ten plik zostanie pobrany ze źródłowej maszyny wirtualnej przy użyciu usługi translatora adresów sieciowych.

Zamknij sesję SSH z docelową maszyną wirtualną.

## <a name="prepare-test-on-source-vm"></a>Przygotuj test na źródłowej maszynie wirtualnej

Najpierw musimy odnaleźć adres IP źródłowej maszyny wirtualnej.  Aby uzyskać publiczny adres IP maszyny wirtualnej, użyj polecenie [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPsourceVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Skopiuj publiczny adres IP, a następnie wklej go do Notatnika, aby można było go użyć w kolejnych krokach. Wskaż, że jest to źródłowa maszyna wirtualna.

### <a name="log-into-source-vm"></a>Zaloguj się do źródłowej maszyny wirtualnej

Ponownie poświadczenia SSH są przechowywane w Cloud Shell. Otwórz nową kartę [Azure Cloud Shell](https://shell.azure.com) w przeglądarce.  Użyj adresu IP pobranego w poprzednim kroku do połączenia SSH z maszyną wirtualną. 

```bash
ssh azureuser@<ip-address-source>
```

Skopiuj i wklej następujące polecenia, aby przygotować się do testowania usługi translatora adresów sieciowych.

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Te polecenia zaktualizują maszynę wirtualną, zainstalujesz go, [zainstalujemy z usługi](https://github.com/rakyll/hey) GitHub i zaktualizujesz środowisko powłoki.

Teraz można przystąpić do testowania usługi translatora adresów sieciowych.

## <a name="validate-nat-service"></a>Weryfikowanie usługi translatora adresów sieciowych

Po zalogowaniu się do źródłowej maszyny wirtualnej można użyć jej **zwinięcie** i **Hej** do generowania żądań na docelowy adres IP.

Użyj zwinięciea, aby pobrać plik 100-kilobajtów.  W poniższym przykładzie Zastąp **\<IP-Address-destination >** z docelowym wcześniej skopiowanym adresem IP.  Parametr **--Output** wskazuje, że pobrany plik zostanie odrzucony.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Możesz również generować serię żądań za pomocą polecenia **Hej**. Ponownie Zastąp **\<IP-Address-destination >** adresem IP, który został wcześniej skopiowany.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

To polecenie spowoduje wygenerowanie 100 żądań, 10 współbieżnie, z limitem czasu wynoszącym 30 sekund. Połączenie TCP nie zostanie ponownie użyte.  Każde żądanie spowoduje pobranie 100 kilobajtów.  Na końcu przebiegu **zostanie** zaraportowana Statystyka dotycząca tego, jak dobrze była usługa translatora adresów sieciowych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) .

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzono bramę translatora adresów sieciowych, utworzono źródłową i docelową maszynę wirtualną, a następnie przetestowano bramę translatora adresów sieciowych.

Przejrzyj metryki w Azure Monitor, aby zobaczyć, jak działa usługa translatora adresów sieciowych. Diagnozuj problemy, takie jak wyczerpanie zasobów dostępnych portów.  Wyczerpanie zasobów portów protokołu IPSec jest łatwo rozwiązywane przez dodanie dodatkowych zasobów publicznego adresu IP lub publicznych zasobów prefiksu IP lub obu tych elementów.

- Dowiedz się więcej o [Virtual Network translatora adresów sieciowych](./nat-overview.md)
- Dowiedz się więcej o [zasobach bramy translatora adresów sieciowych](./nat-gateway-resource.md).
- Przewodnik Szybki Start dotyczący wdrażania [zasobu bramy NAT przy użyciu interfejsu wiersza polecenia platformy Azure](./quickstart-create-nat-gateway-cli.md).
- Przewodnik Szybki Start dotyczący wdrażania [zasobu bramy NAT przy użyciu Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Przewodnik Szybki Start dotyczący wdrażania [zasobu bramy NAT przy użyciu Azure Portal](./quickstart-create-nat-gateway-portal.md).
- [Prześlij opinię na temat publicznej wersji zapoznawczej](https://aka.ms/natfeedback).

> [!div class="nextstepaction"]

