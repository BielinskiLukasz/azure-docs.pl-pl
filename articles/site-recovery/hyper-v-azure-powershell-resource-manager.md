---
title: Konfigurowanie odzyskiwania po awarii na platformie Azure dla maszyn wirtualnych funkcji Hyper-V przy użyciu programu PowerShell i usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Automatyzowanie odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V na platformie Azure przy użyciu usługi Azure Site Recovery przy użyciu programu PowerShell i usługi Azure Resource Manager.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/16/2018
ms.author: sutalasi
ms.openlocfilehash: 4b008cc119951e50567218e332818585fb017e5a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229411"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Konfigurowanie odzyskiwania po awarii na platformie Azure dla maszyn wirtualnych funkcji Hyper-V przy użyciu programu PowerShell i usługi Azure Resource Manager

[Usługa Azure Site Recovery](site-recovery-overview.md) przyczynia się do ciągłości działania i strategia odzyskiwania (BCDR) poprzez organizowanie replikacji, trybu failover i odzyskiwania maszyn wirtualnych (VM) i lokalnych maszyn wirtualnych i serwerów fizycznych.

W tym artykule opisano, jak replikować maszyny wirtualne funkcji Hyper-V na platformie Azure przy użyciu programu Windows PowerShell, wraz z usługi Azure Resource Manager. W przykładzie użyto w tym artykule pokazano, jak replikować pojedyncza maszyna wirtualna uruchomiona na hoście funkcji Hyper-V do platformy Azure.

## <a name="azure-powershell"></a>Azure PowerShell

Program Azure PowerShell udostępnia polecenia cmdlet do zarządzania platformy Azure przy użyciu programu Windows PowerShell. Site Recovery poleceń cmdlet programu PowerShell, dostępne przy użyciu programu Azure PowerShell dla usługi Azure Resource Manager pomóc chronić i odzyskiwać swoje serwery na platformie Azure.

Nie musisz być ekspertem programu PowerShell do użycia w tym artykule, ale należy zrozumieć podstawowe pojęcia, takie jak modułów, poleceń cmdlet i sesje. Odczyt [wprowadzenie do programu Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx), i [przy użyciu programu Azure PowerShell z usługą Azure Resource Manager](../powershell-azure-resource-manager.md).

> [!NOTE]
> Partnerzy firmy Microsoft w programie Cloud Solution Provider (CSP) można skonfigurować i zarządzać ochrony serwerów klienta do ich odpowiednich subskrypcji dostawcy usług Kryptograficznych (subskrypcje dzierżawy).
>
>

## <a name="before-you-start"></a>Przed rozpoczęciem
Upewnij się, że zostały spełnione następujące wymagania wstępne:

* A [Microsoft Azure](https://azure.microsoft.com/) konta. Możesz rozpocząć od [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/). Ponadto, możesz przeczytać o [cennika usługi Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
* Azure PowerShell 1.0. Aby uzyskać informacji na temat tej wersji i sposobach jego instalacji, zobacz [Azure PowerShell w wersji 1.0.](https://azure.microsoft.com/)
* [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) i [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) modułów. Można uzyskać najnowsze wersje tych modułów z [galerii programu PowerShell](https://www.powershellgallery.com/)

Ponadto konkretnemu przykładowi, opisane w tym artykule ma następujące wymagania wstępne:

* Host funkcji Hyper-V, systemem Windows Server 2012 R2 lub Microsoft Hyper-V Server 2012 R2 zawierającego co najmniej jedną maszynę wirtualną. Serwery funkcji Hyper-V powinien połączony z Internetem, bezpośrednio lub za pośrednictwem serwera proxy.
* Maszyny wirtualne mają być replikowane powinna być zgodna z [tych wymaganiach wstępnych](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>Krok 1: Zaloguj się do konta platformy Azure

1. Otwórz konsolę programu PowerShell i uruchom to polecenie, aby zalogować się do konta platformy Azure. Polecenie cmdlet powoduje wyświetlenie strony wyświetli monit o podanie poświadczeń konta usługi: **Connect-AzureRmAccount**.
    - Alternatywnie można uwzględnić poświadczeń konta jako parametru w **Connect-AzureRmAccount** polecenia cmdlet, za pomocą **-poświadczeń** parametru.
    - Jeśli jesteś partnerem CSP, Praca w imieniu dzierżawy, określenia klienta jako dzierżawca, za pomocą ich identyfikatora dzierżawy lub dzierżawy podstawowej nazwy domeny. Na przykład: **Connect-AzureRmAccount-dzierżawy "fabrikam.com"**
2. Skojarz subskrypcję, której chcesz użyć z konto, ponieważ konto może mieć kilka subskrypcji:

    `Select-AzureRmSubscription -SubscriptionName $SubscriptionName`

3. Sprawdź, czy Twoja subskrypcja jest zarejestrowana na potrzeby dostawcy usługi Azure Recovery Services i usługi Site Recovery, za pomocą następujących poleceń:

    `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

4. Upewnij się, że w danych wyjściowych polecenia **RegistrationState** ustawiono **zarejestrowanej**, możesz przejść do kroku 2. W przeciwnym razie należy zarejestrować dostawcę brakuje w ramach subskrypcji, uruchamiając następujące polecenia:

    `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery` `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices`

5. Sprawdź, czy dostawców pomyślnie zarejestrowane, używając następujących poleceń:

    `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`.

## <a name="step-2-set-up-the-vault"></a>Krok 2: Konfigurowanie magazynu

1. Utwórz grupę zasobów usługi Azure Resource Manager, w której chcesz utworzyć magazynu lub użyj istniejącej grupy zasobów. Utwórz nową grupę zasobów w następujący sposób. Zmienna $ResourceGroupName zawiera nazwę grupy zasobów, której chcesz utworzyć, a zmienna $Geo region platformy Azure, w której chcesz utworzyć grupę zasobów (na przykład "Brazylia Południowa").

    `New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo`

2. Aby uzyskać listę grup zasobów w ramach subskrypcji, uruchom **Get-AzureRmResourceGroup** polecenia cmdlet.
2. Utwórz nowy magazyn usług odzyskiwania Azure w następujący sposób:

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    Można pobrać listy istniejących magazynów z **Get-AzureRmRecoveryServicesVault** polecenia cmdlet.


## <a name="step-3-set-the-recovery-services-vault-context"></a>Krok 3: Ustawić kontekst magazynu usługi Recovery Services

Ustaw kontekst magazynu w następujący sposób:

`Set-AsrVaultSettings -Vault $vault`

## <a name="step-4-create-a-hyper-v-site"></a>Krok 4: Tworzenie lokacji funkcji Hyper-V

1. Utwórz nową lokację funkcji Hyper-V w następujący sposób:

        $sitename = "MySite"                #Specify site friendly name
        New-AsrFabric -Type HyperVSite -Name $sitename

2. To polecenie cmdlet uruchomi zadanie odzyskiwania lokacji, do tworzenia witryny i zwraca obiekt zadania, Usługa Site Recovery. Poczekaj na zakończenie zadania i upewnij się, że zadanie zostało ukończone pomyślnie.
3. Użyj **polecenia cmdlet Get-AsrJob**, aby pobrać obiekt zadania i sprawdzić bieżący stan zadania.
4. Generowanie i pobieranie klucza rejestracji dla witryny, w następujący sposób:

    ```
    $SiteIdentifier = Get-AsrFabric -Name $sitename | Select -ExpandProperty SiteIdentifier
    $path = Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
    ```

5. Skopiuj pobrany klucz do hosta funkcji Hyper-V. Wymagany jest klucz, aby zarejestrować hosta funkcji Hyper-V do lokacji.

## <a name="step-5-install-the-provider-and-agent"></a>Krok 5. Instalowanie dostawcy i agenta

1. Pobierz Instalatora, aby uzyskać najnowszą wersję dostawcy z [Microsoft](https://aka.ms/downloaddra).
2. Uruchom Instalatora na hoście theHyper-V.
3. Po zakończeniu instalacji przejdź do kroku rejestracji.
4. Po wyświetleniu monitu podaj pobrane klucz, a następnie Zakończ rejestrację hosta funkcji Hyper-V.
5. Sprawdź, czy host funkcji Hyper-V jest zarejestrowana do lokacji w następujący sposób:

        $server =  Get-AsrFabric -Name $siteName | Get-AsrServicesProvider -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy"></a>Krok 6. Tworzenie zasad replikacji

Przed rozpoczęciem należy pamiętać, że konto magazynu określone powinna być w tym samym regionie platformy Azure co magazyn i powinna mieć włączoną replikacją geograficzną.

1. Tworzenie zasad replikacji w następujący sposób:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

2. Sprawdź zwrócone zadanie, aby upewnić się, że tworzenie zasad replikacji powiedzie się.

3. Pobierz kontener ochrony, który odnosi się do witryny, w następujący sposób:

        $protectionContainer = Get-AsrProtectionContainer
3. Kojarzenie kontenera ochrony z zasadami replikacji w następujący sposób:

     $Policy = get-AsrPolicy - FriendlyName $PolicyName $associationJob = New-AsrProtectionContainerMapping — nazwa $mappingName-$Policy zasad — PrimaryProtectionContainer $protectionContainer [0]

4. Poczekaj, aż do pomyślnego ukończenia zadania skojarzenia.

## <a name="step-7-enable-vm-protection"></a>Krok 7: Włącz ochronę maszyny Wirtualnej

1. Pobieranie elementu które można objąć ochroną, który odnosi się do maszyny Wirtualnej, który chcesz chronić w następujący sposób:

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $ProtectableItem = Get-AsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. Ochrona maszyny Wirtualnej. Jeśli chronisz maszyny Wirtualnej ma więcej niż jeden dysk dołączony do niego, należy określić dysk systemu operacyjnego za pomocą *OSDiskName* parametru.

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = New-AsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS Windows -RecoveryResourceGroupId

3. Poczekaj, aż maszyny wirtualne dotrzeć do stanu chronionego, po replikacji początkowej. Może to potrwać pewien czas, w zależności od czynników, takich jak ilość danych do replikowania i dostępną przepustowość nadrzędnego na platformie Azure. Gdy stan chronionych znajduje się w miejscu, stan zadania i StateDescription są aktualizowane w następujący sposób:

        PS C:\> $DRjob = Get-AsrJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. Zaktualizuj właściwości odzyskiwania (np. rozmiar roli maszyny Wirtualnej,) i sieci platformy Azure, do którego można dołączyć karty Sieciowej maszyny Wirtualnej po włączeniu trybu failover.

        PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $rpi = Get-AsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AsrReplicationProtectedItem --InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AsrJob -Job $UpdateJob

        PS C:\> $UpdateJob| select -ExpandProperty state
        Get-AsrJob -Job $job | select -ExpandProperty state

        Succeeded



## <a name="step-8-run-a-test-failover"></a>Krok 8. wykonywanie testu trybu failover
1. Uruchom testowanie trybu failover w następujący sposób:

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $rpi = Get-AsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        $TFjob =Start-AsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. Sprawdź, czy test tworzenia maszyny Wirtualnej na platformie Azure. Zadania testowego trybu failover jest zawieszone po utworzeniu testowej maszyny Wirtualnej na platformie Azure.
3. Aby wyczyścić i ukończyć test trybu failover, uruchom polecenie:

        $TFjob = Start-AsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej](https://docs.microsoft.com/powershell/module/azurerm.siterecovery) dotyczące usługi Azure Site Recovery przy użyciu poleceń cmdlet programu PowerShell usługi Resource Manager platformy Azure.
