---
title: Azure Disk Encryption przykładowe skrypty
description: Ten artykuł zawiera dodatek do Microsoft Azure szyfrowania dysków dla maszyn wirtualnych z systemem Windows.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 6874372d64fdbb667a23f2ded37a68cd68e32993
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245086"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Azure Disk Encryption przykładowe skrypty 

Ten artykuł zawiera przykładowe skrypty do przygotowywania wstępnie zaszyfrowanych dysków VHD i innych zadań.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="list-vms-and-secrets"></a>Wyświetlanie listy maszyn wirtualnych i wpisów tajnych

Wyświetl listę wszystkich szyfrowanych maszyn wirtualnych w ramach subskrypcji:

```azurepowershell-interactive
$osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
$dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
```
Wyświetl listę wszystkich kluczy tajnych szyfrowania dysku używanych do szyfrowania maszyn wirtualnych w magazynie kluczy:

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
```

## <a name="the-azure-disk-encryption-prerequisites-scripts"></a>Azure Disk Encryption skrypty wymagań wstępnych
Jeśli masz już doświadczenie z wymaganiami wstępnymi dotyczącymi Azure Disk Encryption, możesz użyć [skryptu programu PowerShell dla Azure Disk Encryption wymagań wstępnych](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Przykład korzystania z tego skryptu programu PowerShell można znaleźć w temacie [szyfrowanie maszyny wirtualnej — szybki start](disk-encryption-powershell-quickstart.md). Możesz usunąć komentarze z sekcji skryptu, zaczynając od wiersza 211, aby zaszyfrować wszystkie dyski dla istniejących maszyn wirtualnych w istniejącej grupie zasobów. 

W poniższej tabeli przedstawiono parametry, których można użyć w skrypcie programu PowerShell: 

|Parametr|Opis|Wypełnione?|
|------|------|------|
|$resourceGroupName| Nazwa grupy zasobów, do której należy Magazyn kluczy.  Jeśli jeszcze nie istnieje, zostanie utworzona nowa grupa zasobów o tej nazwie.| True|
|$keyVaultName|Nazwa magazynu kluczy, w którym mają zostać umieszczone klucze szyfrowania. Nowy magazyn o tej nazwie zostanie utworzony, jeśli taki nie istnieje.| True|
|$location|Lokalizacja magazynu kluczy. Upewnij się, że magazyn kluczy i maszyny wirtualne, które mają być szyfrowane, znajdują się w tej samej lokalizacji. Pobierz listę lokalizacji za pomocą polecenia `Get-AzLocation`.|True|
|$subscriptionId|Identyfikator subskrypcji platformy Azure, która ma zostać użyta.  Możesz pobrać identyfikator subskrypcji za pomocą polecenia `Get-AzSubscription`.|True|
|$aadAppName|Nazwa aplikacji usługi Azure AD, która będzie używana do zapisywania wpisów tajnych w magazynie kluczy. Jeśli taka aplikacja nie istnieje, zostanie utworzona nowa aplikacja o podanej nazwie. Jeśli ta aplikacja już istnieje, Przekaż parametr aadClientSecret do skryptu.|False|
|$aadClientSecret|Wpis tajny klienta aplikacji usługi Azure AD, który został utworzony wcześniej.|False|
|$keyEncryptionKeyName|Nazwa opcjonalnego klucza szyfrowania klucza w magazynie kluczy. Nowy klucz o tej nazwie zostanie utworzony, jeśli taki nie istnieje.|False|

## <a name="resource-manager-templates"></a>Szablony Menedżera zasobów

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Szyfrowanie lub odszyfrowywanie maszyn wirtualnych bez aplikacji usługi Azure AD

- [Włączanie szyfrowania dysków na istniejącej lub uruchomionej maszynie wirtualnej z systemem Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)  
- [Wyłączanie szyfrowania na działającej maszynie wirtualnej z systemem Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) 

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Szyfrowanie lub odszyfrowywanie maszyn wirtualnych za pomocą aplikacji usługi Azure AD (poprzednia wersja) 
 
- [Włączanie szyfrowania dysków na istniejącej lub uruchomionej maszynie wirtualnej z systemem Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)    
- [Wyłączanie szyfrowania na działającej maszynie wirtualnej z systemem Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 
- [Utwórz nowy zaszyfrowany dysk zarządzany na podstawie wstępnie zaszyfrowanego obiektu BLOB dysku VHD/magazynu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Tworzy nowy zaszyfrowany dysk zarządzany przy użyciu wstępnie zaszyfrowanego wirtualnego dysku twardego i odpowiednich ustawień szyfrowania

## <a name="prepare-a-pre-encrypted-windows-vhd"></a>Przygotowywanie wstępnie zaszyfrowanego wirtualnego dysku twardego systemu Windows
Poniższe sekcje są niezbędne do przygotowania wstępnie zaszyfrowanego wirtualnego dysku twardego systemu Windows na potrzeby wdrożenia jako zaszyfrowanego wirtualnego dysku twardego w usłudze Azure IaaS. Korzystając z tych informacji, przygotuj i uruchom nową maszynę wirtualną systemu Windows (VHD) na Azure Site Recovery lub na platformie Azure. Aby uzyskać więcej informacji na temat przygotowywania i przekazywania dysku VHD, zobacz [przekazywanie uogólnionego wirtualnego dysku twardego i używanie go do tworzenia nowych maszyn wirtualnych na platformie Azure](upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Aktualizowanie zasad grupy, aby umożliwić ochronę systemu operacyjnego bez modułu TPM
Skonfiguruj ustawienia zasady grupy funkcji BitLocker **szyfrowanie dysków funkcją BitLocker**, które znajdują się w obszarze **zasady komputera lokalnego** > **konfiguracja komputera** > **Szablony administracyjne** > **Windows Składniki**. Zmień to ustawienie na **dyski systemu operacyjnego** > **Wymagaj dodatkowego uwierzytelniania przy uruchamianiu** > **Zezwalaj na używanie funkcji BitLocker bez zgodnego modułu TPM**, jak pokazano na poniższym rysunku:

![Ochrona przed złośliwym kodem zapewniana przez Microsoft na platformie Azure](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Zainstaluj składniki funkcji funkcji BitLocker
W przypadku systemu Windows Server 2012 i nowszych Użyj następującego polecenia:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

W przypadku systemu Windows Server 2008 R2 Użyj następującego polecenia:

    ServerManagerCmd -install BitLockers

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Przygotowanie woluminu systemu operacyjnego na potrzeby funkcji BitLocker przy użyciu `bdehdcfg`
Aby skompresować partycję systemu operacyjnego i przygotować maszynę do obsługi funkcji BitLocker, należy w razie potrzeby wykonać [BdeHdCfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment) :

    bdehdcfg -target c: shrink -quiet 

### <a name="protect-the-os-volume-by-using-bitlocker"></a>Ochrona woluminu systemu operacyjnego za pomocą funkcji BitLocker
Użyj polecenia [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) , aby włączyć szyfrowanie na woluminie rozruchowym przy użyciu funkcji ochrony klucza zewnętrznego. Umieść również klucz zewnętrzny (plik. klucz szyfrowania bloków) na dysku zewnętrznym lub woluminie. Szyfrowanie jest włączone w woluminie systemowym/rozruchowym po następnym ponownym uruchomieniu.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Przygotuj maszynę wirtualną z osobnym dyskiem VHD danych/zasobów na potrzeby uzyskiwania klucza zewnętrznego przy użyciu funkcji BitLocker.

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Przekazywanie zaszyfrowanego wirtualnego dysku twardego do konta usługi Azure Storage
Po włączeniu szyfrowania DM-Crypt należy przekazać lokalny zaszyfrowany wirtualny dysk twardy do konta magazynu.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```

## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Przekaż klucz tajny wstępnie zaszyfrowanej maszyny wirtualnej do magazynu kluczy
Klucz tajny szyfrowania dysku, który uzyskano wcześniej, musi zostać przekazany jako wpis tajny w magazynie kluczy.  Wymaga to przyznania uprawnienia Ustaw klucz tajny oraz uprawnienia wrapkey do konta, które przekaże wpisy tajne.

```powershell 
# Typically, account Id is the user principal name (in user@domain.com format)
$upn = (Get-AzureRmContext).Account.Id
Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# In cloud shell, the account ID is a managed service identity, so specify the username directly 
# $upn = "user@domain.com" 
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# When running as a service principal, retrieve the service principal ID from the account ID, and set access policy to that 
# $acctid = (Get-AzureRmContext).Account.Id
# $spoid = (Get-AzureRmADServicePrincipal -ServicePrincipalName $acctid).Id
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $spoid -BypassObjectIdValidation -PermissionsToKeys wrapKey -PermissionsToSecrets set

```

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Wpis tajny szyfrowania dysku nie został zaszyfrowany za pomocą elementu KEK
Aby skonfigurować wpis tajny w magazynie kluczy, użyj polecenie [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). Hasło jest kodowane jako ciąg Base64, a następnie przekazywane do magazynu kluczy. Ponadto należy się upewnić, że następujące znaczniki są ustawione podczas tworzenia wpisu tajnego w magazynie kluczy.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Użyj `$secretUrl` w następnym kroku, aby [dołączyć dysk systemu operacyjnego bez używania KEK](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Wpis tajny szyfrowania dysku zaszyfrowany za pomocą KEK
Przed przekazaniem wpisu tajnego do magazynu kluczy można opcjonalnie go zaszyfrować przy użyciu klucza szyfrowania klucza. Użyj [interfejsu API](https://msdn.microsoft.com/library/azure/dn878066.aspx) Otocz, aby najpierw zaszyfrować klucz tajny przy użyciu klucza szyfrowania klucza. Dane wyjściowe tej operacji zawijania to ciąg zakodowany przez adres URL w formacie Base64, który można następnie przekazać jako wpis tajny za pomocą polecenia cmdlet [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) .

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Użyj `$KeyEncryptionKey` i `$secretUrl` w następnym kroku, aby [dołączyć dysk systemu operacyjnego za pomocą KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Określ tajny adres URL podczas dołączania dysku systemu operacyjnego

###  <a name="without-using-a-kek"></a>Bez użycia KEK
Podczas dołączania dysku systemu operacyjnego należy przekazać `$secretUrl`. Adres URL został wygenerowany w sekcji "wpis tajny dysku bez szyfrowania za pomocą KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>Korzystanie z KEK
Po dołączeniu dysku systemu operacyjnego należy przekazać `$KeyEncryptionKey` i `$secretUrl`. Ten adres URL został wygenerowany w sekcji "klucz tajny szyfrowania dysku zaszyfrowany za pomocą KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
