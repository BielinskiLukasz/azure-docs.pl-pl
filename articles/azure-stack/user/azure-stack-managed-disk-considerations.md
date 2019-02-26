---
title: Różnice i zagadnienia dotyczące dysków Managed Disks w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Poznaj różnice i zagadnienia podczas pracy z usługą Managed Disks w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: ade8ce1cc872c4d954d272f79ef9a3113ef0f683
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820080"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Usługi Azure Managed Disks stosu: różnice i zagadnienia dotyczące

Ten artykuł zawiera podsumowanie znane różnice między [stosu usługi Azure Managed Disks](azure-stack-manage-vm-disks.md) i [Managed Disks na platformie Azure](../../virtual-machines/windows/managed-disks-overview.md). Aby dowiedzieć się więcej o różnicach wysokiego poziomu usługi Azure Stack i platformą Azure, zobacz [kluczowe zagadnienia](azure-stack-considerations.md) artykułu.

Usługa Managed Disks upraszcza zarządzanie dyskami dla maszyn wirtualnych IaaS dzięki zarządzaniu [kont magazynu](../azure-stack-manage-storage-accounts.md) skojarzone z dyskami maszyn wirtualnych.

> [!Note]  
> Dyski zarządzane w usłudze Azure Stack jest dostępna aktualizacja 1808. Włączono domyślnie podczas tworzenia maszyn wirtualnych przy użyciu portalu Azure Stack z 1811 aktualizacji.
  
## <a name="cheat-sheet-managed-disk-differences"></a>Ściągawka: Różnice dysku zarządzanego

| Cecha | Azure (globalna) | Azure Stack |
| --- | --- | --- |
|Szyfrowanie danych magazynowanych |Szyfrowanie usługi Azure Storage (SSE), usługa Azure Disk Encryption (ADE)     |Funkcja BitLocker 128-bitowego szyfrowania AES      |
|Image (Obraz)          | Obsługa zarządzany obraz niestandardowy |Obsługiwane|
|Opcje kopii zapasowych |Obsługa usługi Azure Backup |Nie jest jeszcze obsługiwany |
|Opcje odzyskiwania po awarii |Obsługa usługi Azure Site Recovery |Nie jest jeszcze obsługiwany|
|Typy dysków     |Dysk SSD w warstwie Premium SSD w warstwie standardowa (wersja zapoznawcza) i standardowych dysków Twardych |Premium SSD, HDD standardowe |
|Dyski w warstwie Premium  |W pełni obsługiwane |Mogą być udostępniane, ale bez ograniczeń wydajności lub gwarancji  |
|Dyski Premium operacje We/Wy  |Zależy od rozmiaru dysku  |2300 operacje We/Wy na dysk |
|Przepływność dysków Premium |Zależy od rozmiaru dysku |145 MB na sekundę na dysk |
|Rozmiar dysku  |Azure Premium Disk: P4 (32 GiB) do P80 (32 TiB)<br>Dysk SSD w warstwie standardowa platformy Azure: E10 (128 GiB) do E80 (32 TiB)<br>Dysk Azure standardowych dysków Twardych: S4 (32 GiB) do S80 (32 TiB) |M4: 32 GiB<br>M6: 64 GiB<br>M10: 128 GiB<br>M15: 256 GiB<br>M20: 512 GiB<br>M30: 1024 GiB |
|Kopiowanie migawki dysków|Tworzenie migawki Azure dołączone do uruchomionej maszyny Wirtualnej obsługiwane dyski zarządzane|Nie jest jeszcze obsługiwany |
|Wydajność dysków analitycznych |Agregują metryki i metryki na dysk obsługiwane |Nie jest jeszcze obsługiwany |
|Migracja      |Zapewnia narzędzia do migracji z istniejącego niezarządzanych Menedżera zasobów maszyn wirtualnych platformy Azure bez konieczności ponowne utworzenie maszyny Wirtualnej  |Nie jest jeszcze obsługiwany |

> [!NOTE]  
> Zarządzane dyski operacje We/Wy i Przepływność w usłudze Azure Stack jest liczbą limit zamiast elastycznie numer, który może wpływ na sprzęt i obciążeń działających w usłudze Azure Stack.

## <a name="metrics"></a>Metryki

Istnieją różnice za pomocą metryk usługi storage:

- Dzięki usłudze Azure Stack dane transakcji w metryk magazynowania nie odróżniają przepustowość sieci wewnętrznych lub zewnętrznych.
- Transakcji danych platformy Azure Stack w metryk usługi storage nie ma na maszynie wirtualnej dostęp do dysków zainstalowanych.

## <a name="api-versions"></a>Wersje interfejsu API

Usługa Azure Stack Managed Disks obsługuje następujące wersje interfejsu API:

- 2017-03-30
- 2017-12-01

## <a name="convert-to-managed-disks"></a>Konwertuj na dyski zarządzane

Możesz użyć poniższego skryptu przy użyciu własnych wartości do przekonwertowania obecnie aprowizowanej maszyny Wirtualnej z niezarządzanych do usługi managed disks

```powershell
$subscriptionId = 'subid'

#Provide the name of your resource group
$resourceGroupName ='rgmgd'

#Provide the name of the Managed Disk
$diskName = 'unmgdvm'

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '50'

#Provide the URI of the VHD file that will be used to create Managed Disk. 
# VHD file can be deleted as soon as Managed Disk is created.
$vhdUri = 'https://rgmgddisks347.blob.local.azurestack.external/vhds/unmgdvm20181109013817.vhd' 

#Provide the storage type for the Managed Disk. PremiumLRS or StandardLRS.
$accountType = 'StandardLRS'

#Provide the Azure Stack location where Managed Disk will be located. 
#The location should be same as the location of the storage account where VHD file is stored.

$location = 'local'
$virtualMachineName = 'mgdvm'
$virtualMachineSize = 'Standard_D1'
$pipname = 'unmgdvm-ip'
$virtualNetworkName = 'rgmgd-vnet'
$nicname = 'unmgdvm295'

#Set the context to the subscription Id where Managed Disk will be created
Select-AzureRmSubscription -SubscriptionId $SubscriptionId

$diskConfig = New-AzureRmDiskConfig -AccountType $accountType  -Location $location -DiskSizeGB $diskSize -SourceUri $vhdUri -CreateOption Import

#Create Managed disk
New-AzureRmDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
$disk = get-azurermdisk -DiskName $diskName -ResourceGroupName $resourceGroupName
$VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize

#Use the Managed Disk Resource Id to attach it to the virtual machine. Please change the OS type to linux if OS disk has linux OS
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $disk.Id -CreateOption Attach -Linux

#Create a public IP for the VM
$publicIp = Get-AzureRmPublicIpAddress -Name $pipname -ResourceGroupName $resourceGroupName 

#Get the virtual network where virtual machine will be hosted
$vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName

# Create NIC in the first subnet of the virtual network
$nic = Get-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $resourceGroupName

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id

#Create the virtual machine with Managed Disk
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
```

## <a name="managed-images"></a>Zarządzane obrazów

Platforma Azure obsługuje stosu *zarządzanych obrazów*, umożliwiające dysku należy utworzyć obiekt obrazu zarządzanego uogólnionej maszyny wirtualnej (zarówno niezarządzane i zarządzane), można tworzyć tylko zarządzane maszyny wirtualne w przyszłości. Obrazy zarządzanych umożliwiają dwa następujące scenariusze:

- Zostały uogólnione niezarządzanych maszyn wirtualnych i chcesz używać dysków zarządzanych w przyszłości.
- Masz zarządzanego uogólnionej maszyny Wirtualnej i chcesz utworzyć wiele, podobne zarządzanych maszyn wirtualnych.

### <a name="migrate-unmanaged-vms-to-managed-disks"></a>Migrowanie maszyn wirtualnych niezarządzanych do dysków zarządzanych

Postępuj zgodnie z instrukcjami [tutaj](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-vhd-in-a-storage-account) do tworzenie obrazu zarządzanego na podstawie uogólnionego wirtualnego dysku twardego w ramach konta magazynu. Ten obraz może służyć do tworzenia zarządzanych maszyn wirtualnych w przyszłości.

### <a name="create-managed-image-from-vm"></a>Tworzenie obrazu zarządzanego z maszyny Wirtualnej

Po utworzeniu obrazu z istniejącego zarządzanego dysku maszyny Wirtualnej przy użyciu skryptu [tutaj](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-managed-disk-using-powershell) , następujący skrypt przykładowy tworzy podobne maszyny Wirtualnej systemu Linux na podstawie istniejącego obiektu obrazu:

```powershell
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "redmond"
$vmName = "myVM"
$imagerg = "managedlinuxrg"
$imagename = "simplelinuxvmm-image-2019122"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

$image = get-azurermimage -ResourceGroupName $imagerg -ImageName $imagename
# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred | `
Set-AzureRmVMSourceImage -Id $image.Id | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Aby uzyskać więcej informacji, zobacz platformy Azure zarządzanego obrazu artykuły [utworzenie obrazu zarządzanego uogólnionej maszyny wirtualnej na platformie Azure](../../virtual-machines/windows/capture-image-resource.md) i [Utwórz Maszynę wirtualną z obrazu zarządzanego](../../virtual-machines/windows/create-vm-generalized-managed.md).

## <a name="configuration"></a>Konfigurowanie

Po zastosowaniu 1808 aktualizacji lub nowszej, należy wykonać następującą konfigurację, przed rozpoczęciem korzystania z dysków zarządzanych:

- Jeśli subskrypcja została utworzona przed aktualizacją 1808, wykonaj poniższe kroki, aby zaktualizować subskrypcji. W przeciwnym razie wdrażanie maszyn wirtualnych w ramach tej subskrypcji może zakończyć się komunikat o błędzie "Błąd wewnętrzny w Menedżerze dysków."
   1. W portalu dzierżawcy, przejdź do **subskrypcje** i Znajdź subskrypcji. Kliknij przycisk **dostawców zasobów**, następnie kliknij przycisk **Microsoft.Compute**, a następnie kliknij przycisk **ponownie zarejestrować**.
   2. W ramach tej samej subskrypcji, przejdź do **kontrola dostępu (IAM)** i upewnij się, że **usługi Azure Stack — dysk zarządzany** znajduje się na liście.
- Jeśli używasz środowiska z wieloma dzierżawami, poproś o swojej chmury operatora (która może znajdować się w Twojej organizacji lub od dostawcy usług) Zmień konfigurację wszystkich katalogów gościa czynności opisane w [w tym artykule](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory). W przeciwnym razie wdrażanie maszyn wirtualnych w ramach subskrypcji skojarzonych z nim gościa może zakończyć się komunikat o błędzie "Błąd wewnętrzny w Menedżerze dysków."

## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej o maszynach wirtualnych usługi Azure Stack](azure-stack-compute-overview.md)
