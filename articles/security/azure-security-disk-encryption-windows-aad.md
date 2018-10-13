---
title: Włączanie usługi Azure Disk Encryption przy użyciu usługi Azure AD aplikacji dla Windows IaaS VMs (poprzedniej wersji) | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera instrukcje dotyczące włączania Microsoft dysku szyfrowanie dla Windows IaaS maszyn wirtualnych platformy Azure.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 10/04/2018
ms.openlocfilehash: 8439998e0919dd22665e3e4d4e9c0e04f0703056
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310763"
---
#  <a name="enable-azure-disk-encryption-for-windows-iaas-vms-previous-release"></a>Włącz dysku szyfrowanie dla Windows IaaS maszyn wirtualnych platformy Azure (poprzedniej wersji)

**Nowa wersja usługi Azure Disk Encryption eliminuje wymaganie zapewniające parametrem aplikacji usługi Azure AD, aby włączyć szyfrowanie dysku maszyny Wirtualnej. Za pomocą nowej wersji już nie są wymagane poświadczenia usługi Azure AD podczas wykonywania kroku Włączanie szyfrowania. Wszystkie nowe maszyny wirtualne muszą być szyfrowane bez parametrów aplikacji usługi Azure AD przy użyciu nowej wersji. Aby wyświetlić instrukcje, aby włączyć szyfrowanie dysku maszyny Wirtualnej przy użyciu nowej wersji, zobacz [dysku Encryption for Windows maszyn wirtualnych platformy Azure](azure-security-disk-encryption-windows.md). Maszyny wirtualne, które już zostały zaszyfrowane za pomocą aplikacji usługi Azure AD, parametry są nadal obsługiwane i powinno być kontynuowane przy użyciu składni usługi AAD.**


Można włączyć wiele scenariuszy szyfrowania dysku i kroki zależą od scenariusza. W poniższych częściach omówiono scenariuszach szczegółowo dla maszyn wirtualnych IaaS Windows. Zanim będzie można użyć szyfrowania dysku [wymagania wstępne dotyczące usługi Azure Disk Encryption](../security/azure-security-disk-encryption-prerequisites-aad.md) należy wykonać. 

Wykonaj [migawki](../virtual-machines/windows/snapshot-copy-managed-disk.md) i/lub wykonać kopię zapasową przed dyski są szyfrowane. Tworzenie kopii zapasowych upewnij się, że opcja odzyskiwania może wystąpić, jeśli wystąpił nieoczekiwany błąd występuje podczas szyfrowania. Maszyny wirtualne z dyskami zarządzanymi wymagają kopię zapasową przed zaszyfrowaniem. Po nawiązaniu kopia zapasowa służy polecenie cmdlet polecenia Set-AzureRmVMDiskEncryptionExtension do szyfrowania dysków zarządzanych, określając parametr - skipVmBackup. Aby uzyskać więcej informacji na temat tworzenia kopii zapasowej i przywracanie zaszyfrowanych maszyn wirtualnych, zobacz [kopia zapasowa Azure](../backup/backup-azure-vms-encryption.md) artykułu. 

>[!WARNING]
>Upewnij się, że klucze tajne szyfrowania nie przekracza granic regionalnych, usługa Azure Disk Encryption musi znajdować się w tym samym regionie usługi Key Vault i maszyn wirtualnych. Tworzenie i używanie usługi Key Vault, który znajduje się w tym samym regionie co maszyna wirtualna do zaszyfrowania. 


## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Włącz szyfrowanie dla nowych maszyn wirtualnych IaaS utworzone w portalu Marketplace
Można włączyć szyfrowania dysków na nowej maszynie Wirtualnej Windows IaaS z rynku na platformie Azure przy użyciu szablonu usługi Resource Manager. Ten szablon tworzy nową zaszyfrowanych Windows maszynę Wirtualną przy użyciu obrazu z galerii systemu Windows Server 2012.

1. Na [szablonu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image), kliknij przycisk **Wdróż na platformie Azure**.

2. Wybieranie subskrypcji, grupy zasobów, lokalizację grupy zasobów, parametry, postanowienia prawne i umowy. Kliknij przycisk **zakupu** do wdrażania nowych maszyn wirtualnych IaaS gdzie szyfrowanie jest włączone.

3. Po wdrożeniu szablonu, sprawdź stan szyfrowania maszyny Wirtualnej przy użyciu preferowanej metody:
     - Weryfikuj za pomocą wiersza polecenia platformy Azure przy użyciu [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) polecenia. 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
         ```

     - Weryfikuj za pomocą programu Azure PowerShell przy użyciu [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) polecenia cmdlet. 

         ```azurepowershell-interactive
         Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
         ```

     -  Wybierz maszynę Wirtualną, a następnie kliknij pozycję **dysków** w obszarze **ustawienia** nagłówek, aby zweryfikować stan szyfrowania w portalu. Wykres w obszarze **szyfrowania**, zobaczysz, czy jest włączony. 
           ![Witryna Azure portal — włączone szyfrowanie dysków](./media/azure-security-disk-encryption/disk-encryption-fig2.png) w poniższej tabeli przedstawiono parametry szablonu usługi Resource Manager dla nowych maszyn wirtualnych w scenariuszu witryny Marketplace przy użyciu Identyfikatora klienta usługi Azure AD:

| Parametr | Opis | 
| --- | --- |
| adminUserName | Nazwa użytkownika administratora dla maszyny wirtualnej. |
| adminPassword | Hasło użytkownika administratora dla maszyny wirtualnej. |
| newStorageAccountName | Nazwa konta magazynu do przechowywania systemu operacyjnego i danych wirtualnych dysków twardych. |
| vmSize | Rozmiar maszyny Wirtualnej. Obecnie są obsługiwane tylko standardowe A, D i G serii. |
| virtualNetworkName | Nazwa sieci wirtualnej, która karty Sieciowej maszyny Wirtualnej powinny należeć do. |
| subnetName | Nazwa podsieci w sieci wirtualnej, która karty Sieciowej maszyny Wirtualnej powinny należeć do. |
| AADClientID | Identyfikator klienta aplikacji usługi Azure AD, które ma uprawnienia do zapisu kluczy tajnych w magazynie kluczy. |
| AADClientSecret | Klucz tajny klienta aplikacji usługi Azure AD, która ma uprawnienia do zapisu kluczy tajnych w magazynie kluczy. |
| keyVaultURL | Adres URL klucza funkcji BitLocker, należy przekazać do magazyn kluczy. Możesz pobrać go za pomocą polecenia cmdlet `(Get-AzureRmKeyVault -VaultName,-ResourceGroupName).VaultURI` lub wiersza polecenia platformy Azure `az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| KeyEncryptionKeyURL | Adres URL klucza szyfrowania klucza, który jest używany do szyfrowania wygenerowanego klucza funkcji BitLocker (opcjonalnie). </br> </br>KeyEncryptionKeyURL jest parametrem opcjonalnym. Przenoszenie własnych KEK do dalszych zabezpieczenie klucza szyfrowania danych (klucz tajny hasło) w magazynie kluczy. |
| keyVaultResourceGroup | Grupa zasobów magazynu kluczy. |
| vmName | Nazwa maszyny Wirtualnej, można wykonać w operacji szyfrowania. |


## <a name="bkmk_RunningWinVM"></a> Włącz szyfrowanie dla istniejących lub działających maszyn wirtualnych Windows IaaS
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia. W następujących sekcjach opisano szczegółowo sposób włączania usługi Azure Disk Encryption. 

>[!IMPORTANT]
 >Jest to konieczne do migawki i/lub kopii zapasowej dysku zarządzanego na podstawie wystąpienia maszyny Wirtualnej poza i przed włączeniem usługi Azure Disk Encryption. Migawkę dysku zarządzanego, może zostać pobrany z portalu lub [kopia zapasowa Azure](../backup/backup-azure-vms-encryption.md) mogą być używane. Tworzenie kopii zapasowych upewnij się, że opcja odzyskiwania możliwe w przypadku dowolnego nieoczekiwany błąd podczas szyfrowania. Po nawiązaniu kopii zapasowej polecenia cmdlet polecenia Set-AzureRmVMDiskEncryptionExtension może służyć do szyfrowania dysków zarządzanych, określając parametr - skipVmBackup. Polecenie polecenia Set-AzureRmVMDiskEncryptionExtension zakończy się niepowodzeniem dla maszyn wirtualnych dysku zarządzanego na podstawie, dopóki nie wykonano kopii zapasowej, a ten parametr został określony. 
>
>Szyfrowanie lub wyłączenie szyfrowania może spowodować ponowne uruchomienie maszyny Wirtualnej. 
>

### <a name="bkmk_RunningWinVMPSH"></a> Włącz szyfrowanie dla istniejących lub działających maszyn wirtualnych przy użyciu programu Azure PowerShell 
Użyj [polecenia Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) polecenia cmdlet, aby włączyć szyfrowanie na uruchomionej maszyny wirtualnej IaaS na platformie Azure. Aby uzyskać informacje na temat włączania szyfrowania przy użyciu usługi Azure Disk Encryption przy użyciu poleceń cmdlet programu PowerShell, zobacz wpisy w blogu [Eksplorowanie usługi Azure Disk Encryption przy użyciu programu Azure PowerShell — część 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) i [Eksplorowanie usługi Azure Disk Encryption za pomocą programu Azure PowerShell — część 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

-  **Szyfruj uruchomionej maszyny Wirtualnej za pomocą klucza tajnego klienta:** poniższy skrypt inicjuje zmiennych i uruchamia polecenie cmdlet polecenia Set-AzureRmVMDiskEncryptionExtension. Grupy zasobów, maszyna wirtualna, magazyn kluczy, aplikacji usługi AAD i klucz tajny klienta powinny zostały już utworzone jako warunki wstępne. Zastąp MySecureRg, MySecureVM, MySecureVault, My-AAD-client-ID, a My-AAD-client-secret z własnymi wartościami.
     ```azurepowershell-interactive
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Szyfruj uruchomionej maszyny Wirtualnej przy użyciu klucza KEK zawijania wpisu tajnego klienta:** usługi Azure Disk Encryption umożliwia określenie istniejącego klucza w magazynie kluczy na zawijanie kluczy tajnych szyfrowania dysków, które zostały wygenerowane podczas włączania szyfrowania. Jeśli klucz szyfrowania jest określony, usługi Azure Disk Encryption używa tego klucza do opakowania wpisów tajnych szyfrowania przed zapisaniem w usłudze Key Vault. 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = ‘MyExtraSecureVM’;
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > Składnia służąca do wartości parametru dysku — szyfrowanie — magazyn kluczy jest ciągiem pełny identyfikator: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Składnia wartości parametru klucza szyfrowania jest pełny identyfikator URI do KEK, podobnie jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Sprawdź dyski są szyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, należy użyć [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) polecenia cmdlet. 
     ```azurepowershell-interactive
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
     ```
    
- **Wyłącz szyfrowanie dysków:** umożliwia wyłączenie szyfrowania, należy użyć [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) polecenia cmdlet. 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>Włącz szyfrowanie dla istniejących lub działających maszyn wirtualnych przy użyciu wiersza polecenia platformy Azure
Użyj [Włącz az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-enable) polecenie, aby włączyć szyfrowanie na uruchomionej maszyny wirtualnej IaaS na platformie Azure.

-  **Szyfruj uruchomionej maszyny Wirtualnej za pomocą klucza tajnego klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Szyfruj uruchomionej maszyny Wirtualnej przy użyciu klucza KEK zawijania wpisu tajnego klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Składnia służąca do wartości parametru dysku — szyfrowanie — magazyn kluczy jest ciągiem pełny identyfikator: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> Składnia wartości parametru klucza szyfrowania jest pełny identyfikator URI do KEK, podobnie jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Sprawdź dyski są szyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, należy użyć [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) polecenia. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **Wyłącz szyfrowanie:** umożliwia wyłączenie szyfrowania, należy użyć [az vm encryption, wyłącz](/cli/azure/vm/encryption#az-vm-encryption-disable) polecenia. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
 
 > [!NOTE]
 >Jest to konieczne do migawki i/lub kopii zapasowej dysku zarządzanego na podstawie wystąpienia maszyny Wirtualnej poza i przed włączeniem usługi Azure Disk Encryption. Migawkę dysku zarządzanego, może zostać pobrany z portalu lub [kopia zapasowa Azure](../backup/backup-azure-vms-encryption.md) mogą być używane. Tworzenie kopii zapasowych upewnij się, że opcja odzyskiwania możliwe w przypadku dowolnego nieoczekiwany błąd podczas szyfrowania. Po nawiązaniu kopii zapasowej polecenia cmdlet polecenia Set-AzureRmVMDiskEncryptionExtension może służyć do szyfrowania dysków zarządzanych, określając parametr - skipVmBackup. To polecenie zakończy się niepowodzeniem dla maszyn wirtualnych dysku zarządzanego na podstawie, dopóki nie wykonano kopii zapasowej, a ten parametr został określony. 
>
>Szyfrowanie lub wyłączenie szyfrowania może spowodować ponowne uruchomienie maszyny Wirtualnej. 

### <a name="bkmk_RunningWinVMwRM"> </a>Przy użyciu szablonu usługi Resource Manager
Można włączyć szyfrowanie dysków dla istniejących lub uruchomionych maszyn wirtualnych Windows IaaS na platformie Azure przy użyciu [szablonu usługi Resource Manager w celu zaszyfrowania uruchomionej maszyny Wirtualnej Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).


1. Szablon szybkiego startu platformy Azure, wybierz polecenie **Wdróż na platformie Azure**.

2. Wybieranie subskrypcji, grupy zasobów, lokalizację grupy zasobów, parametry, postanowienia prawne i umowy. Kliknij przycisk **zakupu** Aby włączyć szyfrowanie na istniejące lub uruchomionej maszyny Wirtualnej IaaS.

Poniższa tabela zawiera listę parametrów szablonu usługi Resource Manager dla istniejących lub uruchomione maszyny wirtualne korzystające z usługi Azure AD identyfikator klienta:

| Parametr | Opis |
| --- | --- |
| AADClientID | Identyfikator klienta aplikacji usługi Azure AD, które ma uprawnienia do zapisu kluczy tajnych w magazynie kluczy. |
| AADClientSecret | Klucz tajny klienta aplikacji usługi Azure AD, która ma uprawnienia do zapisu kluczy tajnych w magazynie kluczy. |
| keyVaultName | Nazwa klucza funkcji BitLocker, należy przekazać do magazyn kluczy. Możesz pobrać go za pomocą polecenia cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <MyResourceGroupName>). Vaultname` lub wiersza polecenia platformy Azure polecenia "az keyvault list--resource-group"MySecureGroup" |Convertfrom-JSON "|
|  KeyEncryptionKeyURL | Adres URL klucza szyfrowania klucza, który jest używany do szyfrowania wygenerowanego klucza funkcji BitLocker. Ten parametr jest opcjonalny w przypadku wybrania **nokek** na liście rozwijanej UseExistingKek. Jeśli wybierzesz **kek** na liście rozwijanej UseExistingKek należy wprowadzić _keyEncryptionKeyURL_ wartości. |
| VolumeType | Typ operacji szyfrowania odbywa się na wolumin. Prawidłowe wartości to _OS_, _danych_, i _wszystkich_. |
| SequenceVersion | Wersja sekwencji operacji funkcji BitLocker. Za każdym razem, gdy operacja szyfrowania dysku jest wykonywana na tej samej maszyny Wirtualnej, należy zwiększyć numer wersji. |
| vmName | Nazwa maszyny Wirtualnej, można wykonać w operacji szyfrowania. |


## <a name="bkmk_VHDpre"> </a>Nowe maszyny wirtualne IaaS utworzone na podstawie szyfrowane klienta wirtualnego dysku twardego i kluczy szyfrowania
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu usługi Resource Manager, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia. W poniższych sekcjach opisano szczegółowo szablonu usługi Resource Manager i interfejsu wiersza polecenia. 

Postępuj zgodnie z instrukcjami w dodatku do przygotowania obrazów zaszyfrowane wstępnie, których można użyć na platformie Azure. Po utworzeniu obrazu, do tworzenia zaszyfrowanych maszyn wirtualnych platformy Azure za pomocą kroki opisane w następnej sekcji.

* [Przygotowywanie wirtualnego dysku twardego Windows zaszyfrowane wstępnie](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Przygotowywanie wirtualnego dysku twardego systemu Linux zaszyfrowane wstępnie](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Jest to konieczne do migawki i/lub kopii zapasowej dysku zarządzanego na podstawie wystąpienia maszyny Wirtualnej poza i przed włączeniem usługi Azure Disk Encryption. Migawkę dysku zarządzanego, może zostać pobrany z portalu lub [kopia zapasowa Azure](../backup/backup-azure-vms-encryption.md) mogą być używane. Tworzenie kopii zapasowych upewnij się, że opcja odzyskiwania możliwe w przypadku dowolnego nieoczekiwany błąd podczas szyfrowania. Po nawiązaniu kopii zapasowej polecenia cmdlet polecenia Set-AzureRmVMDiskEncryptionExtension może służyć do szyfrowania dysków zarządzanych, określając parametr - skipVmBackup. Polecenie polecenia Set-AzureRmVMDiskEncryptionExtension zakończy się niepowodzeniem dla maszyn wirtualnych dysku zarządzanego na podstawie, dopóki nie wykonano kopii zapasowej, a ten parametr został określony. 
>
>Szyfrowanie lub wyłączenie szyfrowania może spowodować ponowne uruchomienie maszyny Wirtualnej. 



### <a name="bkmk_VHDprePSH"> </a> Szyfrowanie maszyn wirtualnych przy użyciu wstępnie zaszyfrowanych dysków VHD za pomocą programu Azure PowerShell
Można włączyć szyfrowanie dysków na zaszyfrowane wirtualnego dysku twardego za pomocą polecenia cmdlet programu PowerShell [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples). W poniższym przykładzie zapewnia niektórych wspólnych parametrów. 

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResouceGroupName "MySecureRG"
```


### <a name="bkmk_VHDpreRM"> </a>Szyfrowanie maszyn wirtualnych IaaS z zaszyfrowane wstępnie dyskami VHD za pomocą szablonu usługi Resource Manager 
Można włączyć szyfrowanie dysków na zaszyfrowane wirtualnego dysku twardego za pomocą [szablonu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm).

1. Szablon szybkiego startu platformy Azure, wybierz polecenie **Wdróż na platformie Azure**.

2. Wybieranie subskrypcji, grupy zasobów, lokalizację grupy zasobów, parametry, postanowienia prawne i umowy. Kliknij przycisk **Utwórz** Aby włączyć szyfrowanie na nowej maszynie Wirtualnej IaaS.

Poniższa tabela zawiera listę parametrów szablonu usługi Resource Manager dla zaszyfrowanych wirtualnego dysku twardego:

| Parametr | Opis |
| --- | --- |
| newStorageAccountName | Nazwa konta magazynu do przechowywania wirtualnego dysku twardego systemu operacyjnego zaszyfrowane. To konto magazynu powinny już został utworzony w tej samej grupie zasobów i tej samej lokalizacji co maszyna wirtualna. |
| osVhdUri | Identyfikator URI wirtualnego dysku twardego systemu operacyjnego z konta magazynu. |
| osType | Typ produktu systemu operacyjnego (Windows/Linux). |
| virtualNetworkName | Nazwa sieci wirtualnej, która karty Sieciowej maszyny Wirtualnej powinny należeć do. Nazwa powinna już została utworzona w tej samej grupie zasobów i tej samej lokalizacji co maszyna wirtualna. |
| subnetName | Nazwa podsieci w sieci wirtualnej, której karty Sieciowej maszyny Wirtualnej powinna należeć. |
| vmSize | Rozmiar maszyny Wirtualnej. Obecnie są obsługiwane tylko standardowe A, D i G serii. |
| keyVaultResourceID | Identyfikator zasobu, która identyfikuje zasób magazynu kluczy w usłudze Azure Resource Manager. Możesz pobrać go przy użyciu polecenia cmdlet programu PowerShell `(Get-AzureRmKeyVault -VaultName &lt;MyKeyVaultName&gt; -ResourceGroupName &lt;MyResourceGroupName&gt;).ResourceId` lub za pomocą polecenia wiersza polecenia platformy Azure `az keyvault show --name "MySecureVault" --query id`|
| keyVaultSecretUrl | Adres URL klucza szyfrowania dysku, który jest skonfigurowany w usłudze key vault. |
| keyVaultKekUrl | Adres URL klucza szyfrowania do szyfrowania klucza wygenerowanego szyfrowania dysków. |
| vmName | Nazwa maszyny Wirtualnej IaaS. |

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Włącz szyfrowanie dla dysków nowo dodane dane
Możesz [Dodaj nowy dysk do maszyny Wirtualnej Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md), lub [za pośrednictwem witryny Azure portal](../virtual-machines/windows/attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Włącza szyfrowanie na nowo dodany dysk przy użyciu programu Azure PowerShell
 Szyfrowanie nowego dysku dla maszyn wirtualnych Windows za pomocą programu Powershell, należy określić nową wersję sekwencji. Wersja sekwencji musi być unikatowa. Poniższy skrypt generuje identyfikator GUID wersji sekwencji. W niektórych przypadkach dysk nowo dodane dane mogą być szyfrowane automatycznie przez rozszerzenie usługi Azure Disk Encryption. Automatyczne szyfrowanie występuje przeważnie, po ponownym uruchomieniu maszyny Wirtualnej, po nowy dysk przejściu do trybu online. Jest to zazwyczaj spowodowane tym, ponieważ określono "All" dla typu woluminu podczas szyfrowania dysku wcześniej został uruchomiony na maszynie Wirtualnej. W przypadku automatycznego szyfrowania na dysku nowo dodane dane, firma Microsoft zaleca, ponownie uruchom polecenie cmdlet polecenia Set-AzureRmVmDiskEncryptionExtension nowej wersji sekwencji. Jeśli nowy dysk danych jest automatycznie zaszyfrowane i nie powinna być szyfrowane, najpierw odszyfrować wszystkie dyski, a następnie ponownie zaszyfrować przy użyciu nowej wersji sekwencji, określając system operacyjny dla typu woluminu. 
 

-  **Szyfruj uruchomionej maszyny Wirtualnej za pomocą klucza tajnego klienta:** poniższy skrypt inicjuje zmiennych i uruchamia polecenie cmdlet polecenia Set-AzureRmVMDiskEncryptionExtension. Grupy zasobów, maszyna wirtualna, magazyn kluczy, aplikacji usługi AAD i klucz tajny klienta powinny zostały już utworzone jako warunki wstępne. Zastąp MySecureRg, MySecureVM, MySecureVault, My-AAD-client-ID, a My-AAD-client-secret z własnymi wartościami. W tym przykładzie użyto "All" dla parametru - VolumeType zawiera woluminy zarówno systemu operacyjnego i danych. Jeśli chcesz szyfrowania woluminu systemu operacyjnego, należy użyć "System operacyjny" dla parametru - VolumeType. 

     ```azurepowershell-interactive
      $sequenceVersion = [Guid]::NewGuid();
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;
    ```
- **Szyfruj uruchomionej maszyny Wirtualnej przy użyciu klucza KEK zawijania wpisu tajnego klienta:** usługi Azure Disk Encryption umożliwia określenie istniejącego klucza w magazynie kluczy na zawijanie kluczy tajnych szyfrowania dysków, które zostały wygenerowane podczas włączania szyfrowania. Jeśli klucz szyfrowania jest określony, usługi Azure Disk Encryption używa tego klucza do opakowania wpisów tajnych szyfrowania przed zapisaniem w usłudze Key Vault. W tym przykładzie użyto "All" dla parametru - VolumeType zawiera woluminy zarówno systemu operacyjnego i danych. Jeśli chcesz szyfrowania woluminu systemu operacyjnego, należy użyć "System operacyjny" dla parametru - VolumeType. 

     ```azurepowershell-interactive
     $sequenceVersion = [Guid]::NewGuid();
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > Składnia służąca do wartości parametru dysku — szyfrowanie — magazyn kluczy jest ciągiem pełny identyfikator: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Składnia wartości parametru klucza szyfrowania jest pełny identyfikator URI do KEK, podobnie jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Włącza szyfrowanie na nowo dodany dysk przy użyciu wiersza polecenia platformy Azure
  Po uruchomieniu polecenia, aby włączyć szyfrowanie polecenia wiersza polecenia platformy Azure automatycznie zapewni nowej wersji sekwencji dla Ciebie. Dopuszczalne wartości dla parametru yype woluminu to wszystkie, systemu operacyjnego i danych. Może być konieczna zmiana parametru typu woluminu do systemu operacyjnego i danych, jeśli tylko Szyfrujesz jeden typ dysku maszyny wirtualnej. W przykładach użyto "All" dla parametru typu woluminu. 

-  **Szyfruj uruchomionej maszyny Wirtualnej za pomocą klucza tajnego klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Szyfruj uruchomionej maszyny Wirtualnej przy użyciu klucza KEK zawijania wpisu tajnego klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Włącz szyfrowanie przy użyciu uwierzytelniania opartego na certyfikatach klientów usługi Azure AD.
Można użyć uwierzytelniania certyfikatu klienta, z lub bez klucza KEK. Skrypty wymagającą [wymagania wstępne dotyczące usługi Azure Disk Encryption](azure-security-disk-encryption-prerequisites-aad.md) są kompletne. Przed użyciem skryptów programu PowerShell, powinno mieć już certyfikat przekazany do magazynu kluczy i wdrożone na maszynie Wirtualnej. Jeśli używasz klucza KEK zbyt, klucza KEK powinna już istnieć. Aby uzyskać więcej informacji, zobacz [uwierzytelniania opartego na certyfikatach dla usługi Azure AD](azure-security-disk-encryption-prerequisites-aad.md#bkmk_Cert) w dalszej części artykułu wymagań wstępnych.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Włącz szyfrowanie przy użyciu uwierzytelniania opartego na certyfikatach za pomocą programu Azure PowerShell

```powershell
## Fill in 'MySecureRg', 'My-AAD-client-ID', 'MySecureVault, and ‘MySecureVM’.

$rgName = 'MySecureRg';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = ‘MySecureVM’;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

# Fill in the certificate path and the password so the thumbprint can be set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
```
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Włącz szyfrowanie przy użyciu uwierzytelniania opartego na certyfikatach i KEK przy użyciu programu Azure PowerShell

```powershell
# Fill in 'MySecureRg', 'My-AAD-client-ID', 'MySecureVault,, 'MySecureVM’, and "KEKName.

$rgName = 'MySecureRg';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$keyEncryptionKeyName ='KEKName';
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

## Fill in the certificate path and the password so the thumbprint can be read and set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint and a KEK

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

## <a name="disable-encryption"></a>Wyłącz szyfrowanie
Można wyłączyć szyfrowanie przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub przy użyciu szablonu usługi Resource Manager. 

- **Wyłącz szyfrowanie dysków za pomocą programu Azure PowerShell:** umożliwia wyłączenie szyfrowania, należy użyć [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) polecenia cmdlet. 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```

- **Wyłącz szyfrowanie za pomocą wiersza polecenia platformy Azure:** umożliwia wyłączenie szyfrowania, należy użyć [az vm encryption, wyłącz](/cli/azure/vm/encryption#az-vm-encryption-disable) polecenia. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
- **Wyłącz szyfrowanie za pomocą szablonu usługi Resource Manager:** 

    1. Kliknij przycisk **Wdróż na platformie Azure** z [wyłączenie szyfrowania dysków na uruchomioną maszynę Wirtualną Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) szablonu.
    2. Wybierz subskrypcję, grupy zasobów, lokalizację maszyny Wirtualnej, postanowienia prawne i umowy.
    3.  Kliknij przycisk **zakupu** umożliwia wyłączenie szyfrowania dysku na uruchomionej maszynie Wirtualnej Windows. 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Włączanie usługi Azure Disk Encryption dla systemu Linux](azure-security-disk-encryption-linux-aad.md)
