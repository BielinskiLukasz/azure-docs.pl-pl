---
title: Usługa Azure Site Recovery — ustawienia i testowania odzyskiwania po awarii dla maszyn wirtualnych platformy Azure przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure za pomocą usługi Azure Site Recovery przy użyciu programu Azure PowerShell.
services: site-recovery
author: bsiva
manager: abhemraj
editor: raynew
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: bsiva
ms.openlocfilehash: 1bf2fe84f9695993dacb6d197d75c18e5db86c4e
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433433"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure przy użyciu programu Azure PowerShell

W tym artykule, możesz dowiedzieć się, jak skonfigurować i przetestować odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure przy użyciu programu Azure PowerShell.

Omawiane kwestie:

> [!div class="checklist"]
> - Utwórz magazyn usługi Recovery Services.
> - W sesji programu PowerShell, należy ustawić kontekst magazynu.
> - Przygotuj magazyn, aby rozpocząć replikowanie maszyn wirtualnych platformy Azure.
> - Tworzenie mapowania sieci.
> - Tworzenie konta magazynu do replikowania maszyn wirtualnych.
> - Replikowanie maszyn wirtualnych platformy Azure w regionie odzyskiwania do odzyskiwania po awarii.
> - Wykonaj test trybu failover, weryfikowanie i wyczyść test pracy awaryjnej.
> - Tryb failover do regionu odzyskiwania.

> [!NOTE]
> Nie wszystkie funkcje scenariusz dostępne za pośrednictwem portalu mogą być dostępne za pośrednictwem programu Azure PowerShell. Niektóre funkcje scenariusza, nie są obecnie obsługiwane za pośrednictwem programu Azure PowerShell są:
> - Możliwość określenia, że wszystkie dyski na maszynie wirtualnej powinny być replikowane bez konieczności jawnego określania każdego dysku maszyny wirtualnej.  

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:
- Przeanalizuj informacje o [składnikach i architekturze scenariusza](azure-to-azure-architecture.md).
- Zapoznaj się z [wymaganiami dotyczącymi obsługi](azure-to-azure-support-matrix.md) wszystkich składników.
- Jest to wersja 5.7.0 jest większy niż moduł AzureRm PowerShell. Jeśli potrzebujesz zainstalować lub uaktualnić programu Azure PowerShell, postępuj zgodnie z tym [Przewodnik instalowania i konfigurowania programu Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Zaloguj się do subskrypcji usługi Microsoft Azure

Zaloguj się do subskrypcji platformy Azure przy użyciu polecenia cmdlet Connect-AzureRmAccount

```azurepowershell
Connect-AzureRmAccount
```
Wybierz swoją subskrypcję platformy Azure. Aby uzyskać listę subskrypcji platformy Azure, do których masz dostęp do, użyj polecenia cmdlet Get-AzureRmSubscription. Wybierz subskrypcję platformy Azure, aby pracować przy użyciu polecenia cmdlet Select-AzureRmSubscription.

```azurepowershell
Select-AzureRmSubscription -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>Uzyskaj szczegółowe informacje o maszyny wirtualne do replikacji

W przykładzie w tym artykule będą replikowane do i odzyskana w regionie zachodnie stany USA 2 maszynę wirtualną w regionie wschodnie stany USA. Maszyna wirtualna jest replikowana jest maszyną wirtualną za pomocą dysku systemu operacyjnego i dysk z danymi pojedynczego. Nazwa maszyny wirtualnej użytych w tym przykładzie jest AzureDemoVM.

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzureRmVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

Pobieranie szczegółów dysku dla dysków maszyny wirtualnej. Szczegóły dysku będzie używana później podczas uruchamiania replikacji dla maszyny wirtualnej.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Utwórz grupę zasobów, w której chcesz utworzyć magazyn usługi Recovery Services.

> [!IMPORTANT]
> * Magazyn usługi Recovery services i maszyny wirtualne chronione, muszą być w różnych lokalizacjach platformy Azure.
> * Grupa zasobów magazynu usługi Recovery services i virtual machines chroniony, musi być w różnych lokalizacjach platformy Azure.
> * Magazyn usługi Recovery services i grupę zasobów, do której on należy, może być w tej samej lokalizacji platformy Azure.

W tym przykładzie w tym artykule chroniona maszyna wirtualna jest w regionie wschodnie stany USA. Region odzyskiwania wybrane do odzyskania po awarii jest regionu zachodnie stany USA 2. Magazyn usługi recovery services i grupę zasobów magazynu, znajdują się w regionie odzyskiwania (zachodnie stany USA 2)

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```
```
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```

Utwórz magazyn usługi Recovery services. W przykładzie poniżej w magazynie usługi Recovery Services o nazwie a2aDemoRecoveryVault zostało utworzone w regionie zachodnie stany USA 2.

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzureRmRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```
```
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```
## <a name="set-the-vault-context"></a>Ustaw kontekst magazynu

> [!TIP]
> Moduł Azure PowerShell odzyskiwania lokacji (moduł AzureRm.RecoveryServices.SiteRecovery) jest dostarczany za pomocą łatwych w użyciu aliasów dla większości poleceń cmdlet. Polecenia cmdlet w module mieć postać  *\<operacji >-**ciągu AzureRmRecoveryServicesAsr**\<obiektu >* i mieć równoważne aliasy, które będzie mieć postać  *\<Operacji >-**ASR**\<obiektu >*. W tym artykule używa aliasy polecenia cmdlet w celu ułatwienia odczytu.

Ustaw kontekst magazynu do użytku w sesji programu PowerShell. Aby to zrobić, Pobierz plik ustawień magazynu, a następnie zaimportuj pobrany plik w sesji programu PowerShell, aby ustawić kontekst magazynu.

Po ustawieniu kolejne operacje usługi Azure Site Recovery w sesji programu PowerShell są wykonywane w kontekście wybranego magazynu.

 ```azurepowershell
#Download the vault settings file for the vault.
$Vaultsettingsfile = Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteRecovery -Path C:\users\user\Documents\

#Import the downloaded vault settings file to set the vault context for the PowerShell session.
Import-AzureRmRecoveryServicesAsrVaultSettingsFile -Path $Vaultsettingsfile.FilePath

```
```
ResourceName         ResourceGroupName ResourceNamespace          ResouceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults     
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```
## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Przygotowanie magazynu, aby rozpocząć replikowanie maszyn wirtualnych platformy Azure

####<a name="1-create-a-site-recovery-fabric-object-to-represent-the-primarysource-region"></a>1. Utwórz obiekt sieci szkieletowej Site Recovery do reprezentowania primary(source) region

Obiekt sieci szkieletowej, w magazynie reprezentuje region platformy Azure. Obiekt podstawowy sieci szkieletowej jest obiektem sieci szkieletowej, tworzone do reprezentowania regionu platformy Azure, której należy maszyn wirtualnych chronionych w magazynie. W tym przykładzie w tym artykule chroniona maszyna wirtualna jest w regionie wschodnie stany USA.

> [!NOTE]
> Operacjami usługi Azure Site Recovery są wykonywane asynchronicznie. Po zainicjowaniu operacji przesyłania zadania usługi Azure Site Recovery i zwracany jest zadaniem śledzenia obiektu. Pobierz najnowszy stan zadania (Get-ASRJob) i monitorowanie stanu operacji, należy użyć zadania śledzenia obiektu.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AsrFabric -Name "A2Ademo-EastUS"
```
Jeśli chronione maszyny wirtualne z różnych regionów platformy Azure w tym samym magazynie, należy utworzyć jeden obiekt sieci szkieletowej dla każdego źródła region platformy Azure.

####<a name="2-create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>2. Utwórz obiekt sieci szkieletowej Site Recovery do reprezentowania region odzyskiwania

Obiekt sieci szkieletowej odzyskiwania reprezentuje odzyskiwania lokalizacji platformy Azure. Maszyny wirtualne będą replikowane do i odzyskana (w przypadku pracy awaryjnej) regionie odzyskiwania, reprezentowane przez sieć szkieletową odzyskiwania. Odzyskiwanie region platformy Azure, w tym przykładzie jest zachodnie stany USA 2.

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AsrFabric -Name "A2Ademo-WestUS"

```

####<a name="3-create-a-site-recovery-protection-container-in-the-primary-fabric"></a>3. Tworzenie kontenera ochrony Usługa Site Recovery w podstawowej sieci szkieletowej

Kontener ochrony jest kontenerem, używane do grupowania zreplikowane elementy w sieci szkieletowej.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzureRmRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-ASRProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```
####<a name="4-create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>4. Tworzenie kontenera ochrony Usługa Site Recovery w sieci szkieletowej odzyskiwania

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzureRmRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-ASRProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

####<a name="5-create-a-replication-policy"></a>5. Tworzenie zasad replikacji

```azurepowershell
#Create replication policy
$TempASRJob = New-ASRPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-ASRPolicy -Name "A2APolicy"
```
####<a name="6-create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>6. Utwórz mapowanie kontenera ochrony między podstawowymi i odzyskiwania w kontenerze ochrony

Do mapowania kontenera ochrony mapuje kontener ochronę podstawową za pomocą kontenera ochrony odzyskiwania i zasady replikacji. Utwórz jedno mapowanie dla każdej zasady replikacji, które będzie używane do replikacji maszyn wirtualnych między dwoma kontenera ochrony.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-ASRProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

####<a name="7-create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>7. Tworzenie mapowania kontenera ochrony w przypadku powrotu po awarii (replikacja odwrotna po przejściu w tryb failover)

Po przejściu w tryb failover, gdy jesteś gotowy przywrócić nieudane przez maszynę wirtualną do oryginalnego region platformy Azure, można przeprowadzić powrotu po awarii. Powrót po awarii, nie powiodło się za pośrednictwem maszyny wirtualnej jest replikowany z nie powiodło się w regionie do regionu oryginalnego. Do replikacji odwrotnej przełączyć role oryginalnego region i region odzyskiwania. Oryginalny region staje się teraz nowy region odzyskiwania, a jaki był pierwotnie regionie odzyskiwania teraz staje się regionu podstawowego. Mapowanie kontenera ochrony do replikacji odwrotnej reprezentuje przełączono stan role regionów oryginału i odzyskiwania.

```azurepowershell
#Create Protection container mapping (for failback) between the Recovery and Primary Protection Containers with the Replication policy
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

## <a name="create-cache-storage-accounts-and-target-storage-accounts"></a>Tworzenie kont magazynu pamięci podręcznej i docelowych kont magazynu

Konto magazynu pamięci podręcznej jest standardowe konto magazynu w tym samym regionie platformy Azure jako maszyna wirtualna jest replikowana. Konto magazynu pamięci podręcznej jest używany do przechowywania replikacji zmian tymczasowo niedostępny, zanim zmiany zostaną przeniesione do odzyskiwania region platformy Azure. Użytkownik może (ale nie musi) określania kont magazynu pamięci podręcznej różne dla różnych dysków maszyny wirtualnej.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzureRmStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

W przypadku maszyn wirtualnych **nie używa usługi managed disks**, docelowe konto magazynu to konta magazynu w regionie odzyskiwania, do której są replikowane dyski maszyny wirtualnej. Docelowe konto magazynu może być standardowe konto magazynu lub konta usługi premium storage. Wybierz rodzaj wymagane jest konto magazynu, w oparciu o częstotliwość zmian danych (szybkość zapisu We/Wy) dla dysków i limity zmian usługi Azure Site Recovery obsługiwane dla typu magazynu.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzureRmStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage

```

## <a name="create-network-mappings"></a>Tworzenie mapowania sieci

Mapowanie sieci działa sieciami wirtualnymi w regionie podstawowym, sieciami wirtualnymi w regionie odzyskiwania. Mapowanie sieci określa sieć wirtualna platformy Azure w regionie odzyskiwania, które maszyny wirtualnej w wirtualnej sieci podstawowej należy przejściu w tryb failover. Jedna sieć wirtualna platformy Azure mogą być mapowane do tylko jednej sieci wirtualnej platformy Azure w regionie odzyskiwania.

- Tworzenie sieci wirtualnej platformy Azure w regionie odzyskiwania przejścia w tryb failover

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzureRmVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzureRmVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzureRmVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```
- Pobieranie podstawowego sieci wirtualnej (vnet maszyna wirtualna jest połączona z)
   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to

    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resourec name
    $NIC = Get-AzureRmNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")  
   ```
- Utwórz mapowanie sieci między podstawowym sieć wirtualną i siecią wirtualną odzyskiwania
   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
    Write-Output $TempASRJob.State

   ```
- Tworzenie mapowania sieci na potrzeby zorientowanych w kierunku odwrotnym (powrót po awarii)
    ```azurepowershell
    #Create an ASR network mapping for failback between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Replikowanie maszyn wirtualnych platformy Azure

Replikowanie maszyn wirtualnych platformy Azure za pomocą **usługi managed disks**.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -managed -LogStorageAccountId $storageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $ RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryOSDiskAccountType $RecoveryOSDiskAccountType

# Data disk
$datadiskId1  = $vm.StorageProfile.DataDisks[0].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[0]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0]. StorageAccountType

$DataDisk1ReplicationConfig  = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -managed -LogStorageAccountId $storageAccount.Id `
         -DiskId $datadiskId1 -RecoveryResourceGroupId  $ RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig


#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

```

Replikowanie maszyn wirtualnych platformy Azure za pomocą **niezarządzane dyski**.

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.  
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}


#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

Po rozpoczęcia replikacji powiedzie się, dane maszyny wirtualnej są replikowane w regionie odzyskiwania.

Proces replikacji rozpoczyna się przez wstępnie rozmieszczania kopię replikowanych dyskach maszyny wirtualnej w regionie odzyskiwania. Ta faza nosi nazwę fazy replikacji początkowej.

Po zakończeniu replikacji początkowej replikacji przenosi faza synchronizacji różnicowej. W tym momencie maszyna wirtualna jest chroniona i testu pracy awaryjnej operację można wykonać na nim. Stan replikacji zreplikowany element reprezentujący maszyna wirtualna przechodzi do stanu "Protected", po zakończeniu replikacji początkowej.

Trwa pobieranie szczegółów chronionego elementu replikacji odpowiadającego mu monitorować stan replikacji i kondycję replikacji dla maszyny wirtualnej.
```azurepowershell
 Get-ASRReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal           
```

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>Wykonaj test trybu failover, weryfikowanie i wyczyść test pracy awaryjnej

Po replikacji maszyny wirtualnej osiągnęła stanu chronionego, test pracy awaryjnej operację można wykonać na maszynie wirtualnej (w obrębie chronionego elementu replikacji maszyny wirtualnej.)

```azurepowershell
#Create a seperate network for test failover (not connected to my DR network)
$TFOVnet = New-AzureRmVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzureRmVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzureRmVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Wykonaj test trybu failover.
```azurepowershell
$ReplicationProtectedItem = Get-ASRReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery


```

Poczekaj na zakończenie operacji testu trybu failover.
```azurepowershell
Get-ASRJob -Job $TFOJob
```

```
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```
Po zadania testowego trybu failover zakończy się pomyślnie, można nawiązać połączenia testowego w trybie Failover maszyny wirtualnej i sprawdź poprawność testowy tryb failover.

Po zakończeniu testowania na test przełączone w tryb failover maszyny wirtualnej oczyszczania kopiowania testu przez uruchomienie czyszczenia Testuj operację trybu failover. Ta operacja usuwa testu kopię maszyny wirtualnej, która została utworzona przez testowy tryb failover.

```azurepowershell
$Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJob -Job $Job_TFOCleanup | Select State
```
```
State    
-----    
Succeeded
```

## <a name="failover-to-azure"></a>Tryb failover na platformie Azure

Tryb failover maszyny wirtualnej, aby określony punkt odzyskiwania.

```azurepowershell
$RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```
```
CrashConsistent 4/24/2018 11:10:25 PM
```


```azurepowershell
#Start the failover job
$Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-ASRJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```
```
Succeeded
```

Po pomyślnie przełączone w tryb failover, należy zatwierdzić operacji trybu failover.
```azurepowershell
$CommitFailoverJOb = Start-ASRCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJOb -Job $CommitFailoverJOb
```

```
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

Po przejściu w tryb failover gdy jesteś gotowy wrócić do oryginalnego regionie, Uruchom replikację odwrotną dla chronionego elementu replikacji za pomocą polecenia cmdlet Update-AzureRmRecoveryServicesAsrProtectionDirection.

## <a name="next-steps"></a>Kolejne kroki
Widok [dokumentacja programu PowerShell odzyskiwania witryn Azure ](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery) Aby dowiedzieć się, jak można wykonywać inne zadania, takie jak tworzenie planów odzyskiwania i testowania pracy w trybie failover planów odzyskiwania za pomocą programu PowerShell.
