---
title: "Zabezpieczanie usług IIS za pomocą certyfikatów SSL na platformie Azure | Microsoft Docs"
description: "Dowiedz się, jak zabezpieczyć internetowy serwer usług IIS za pomocą certyfikatów SSL na maszynie wirtualnej z systemem Windows na platformie Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: ada0703603df5ae5a324d38cda2b23a060a10992
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2018
---
# <a name="secure-iis-web-server-with-ssl-certificates-on-a-windows-virtual-machine-in-azure"></a>Zabezpieczanie internetowego serwera usług IIS za pomocą certyfikatów SSL na maszynie wirtualnej z systemem Windows na platformie Azure
Aby zabezpieczyć serwery sieci Web, można używać certyfikatu SSL (Secure Sockets Layer) do szyfrowania ruchu w sieci Web. Te certyfikaty SSL mogą być przechowywane w usłudze Azure Key Vault i umożliwiają bezpieczne wdrażanie certyfikatów na maszynach wirtualnych z systemem Windows na platformie Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie usługi Azure Key Vault
> * Generowanie lub przekazywanie certyfikatu do usługi Key Vault
> * Tworzenie maszyny wirtualnej i instalowanie internetowego serwera usług IIS
> * Dodawanie certyfikatu do maszyny wirtualnej i konfigurowanie usług IIS z powiązaniem SSL

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli chcesz zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga modułu Azure PowerShell w wersji 5.3 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure. 


## <a name="overview"></a>Omówienie
Usługa Azure Key Vault chroni klucze kryptograficzne i klucze tajne, takie jak certyfikaty lub hasła. Usługa Key Vault pomaga uprościć proces zarządzania certyfikatami i pozwala zachować kontrolę nad kluczami, które uzyskują dostęp do tych certyfikatów. Możesz utworzyć certyfikat z podpisem własnym wewnątrz usługi Key Vault lub przekazać istniejący zaufany certyfikat, który już posiadasz.

Zamiast używania niestandardowego obrazu maszyny wirtualnej, który zawiera wbudowane certyfikaty, należy wstrzyknąć certyfikaty do uruchomionej maszyny wirtualnej. Ten proces zapewnia, że podczas wdrażania na serwerze sieci Web zostaną zainstalowane najbardziej aktualne certyfikaty. Jeśli odnowisz lub zamienisz certyfikat, nie musisz też tworzyć nowego niestandardowego obrazu maszyny wirtualnej. Najnowsze certyfikaty są automatycznie wstrzykiwane podczas tworzenia dodatkowych maszyn wirtualnych. W trakcie całego procesu certyfikaty nigdy nie opuszczają platformy Azure oraz nie są udostępniane w skrypcie, historii wiersza polecenia ani w szablonie.


## <a name="create-an-azure-key-vault"></a>Tworzenie usługi Azure Key Vault
Aby można było utworzyć usługę Key Vault i certyfikaty, utwórz grupę zasobów za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroupSecureWeb* w lokalizacji *Wschodnie stany USA*:

```azurepowershell-interactive
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Następnie utwórz magazyn Key Vault przy użyciu polecenia [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault). Każda usługa Key Vault wymaga unikatowej nazwy, która powinna zawierać tylko małe litery. Zamień wartość `mykeyvault` w poniższym przykładzie na własną unikatową nazwę usługi Key Vault:

```azurepowershell-interactive
$keyvaultName="mykeyvault"
New-AzureRmKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Generowanie certyfikatu i zapisywanie go w usłudze Key Vault
Do użycia w środowisku produkcyjnym należy zaimportować prawidłowy certyfikat podpisany przez zaufanego dostawcę, używając polecenia [Import-AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate). W tym samouczku poniższy przykład przedstawia, jak można wygenerować certyfikat z podpisem własnym za pomocą polecenia [Add-AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificate), które używa domyślnych zasad certyfikatów z poziomu polecenia [New-AzureKeyVaultCertificatePolicy](/powershell/module/azurerm.keyvault/new-azurekeyvaultcertificatepolicy). 

```azurepowershell-interactive
$policy = New-AzureKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzureKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Ustaw nazwę użytkownika i hasło administratora maszyny wirtualnej przy użyciu polecenia [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Następnie utwórz maszynę wirtualną za pomocą polecenia [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). W poniższym przykładzie zostanie utworzona maszyna wirtualna o nazwie *myVM* w lokalizacji *EastUS*. Jeśli zasoby sieciowe jeszcze nie istnieją, zostaną utworzone w tym momencie. To polecenie cmdlet otwiera również port *443* w celu obsługi bezpiecznego ruchu internetowego.

```azurepowershell-interactive
# Create a VM
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred `
    -OpenPorts 443

# Use the Custom Script Extension to install IIS
Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

Utworzenie maszyny wirtualnej może potrwać kilka minut. W ostatnim kroku niestandardowe rozszerzenie skryptu platformy Azure jest używane w celu zainstalowania internetowego serwera usług IIS przy użyciu polecenia [Set-AzureRmVmExtension](/powershell/module/azurerm.compute/set-azurermvmextension).


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Dodawanie certyfikatu do maszyny wirtualnej z poziomu usługi Key Vault
Aby dodać certyfikat z usługi Key Vault do maszyny wirtualnej, uzyskaj identyfikator certyfikatu przy użyciu polecenia [Get-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret). Dodaj certyfikat do maszyny wirtualnej przy użyciu polecenia [Add-AzureRmVMSecret](/powershell/module/azurerm.compute/add-azurermvmsecret):

```azurepowershell-interactive
$certURL=(Get-AzureKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzureRMVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzureRmKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzureRmVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>Konfigurowanie usług IIS do korzystania z certyfikatu
Użyj ponownie niestandardowego rozszerzenia skryptu z poleceniem [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension), aby zaktualizować konfigurację usług IIS. Ta aktualizacja dotyczy certyfikatu dodanego z usługi Key Vault do usług IIS i konfiguruje powiązanie internetowe:

```azurepowershell-interactive
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>Testowanie bezpiecznej aplikacji sieci Web
Uzyskaj publiczny adres IP maszyny wirtualnej za pomocą polecenia [Get-AzureRmPublicIPAddress](/powershell/resourcemanager/azurerm.network/get-azurermpublicipaddress). W poniższym przykładzie uzyskano utworzony wcześniej adres IP `myPublicIP`:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIPAddress" | select "IpAddress"
```

Teraz możesz otworzyć przeglądarkę internetową i wprowadzić ciąg `https://<myPublicIP>` na pasku adresu. Aby zaakceptować ostrzeżenie o zabezpieczeniach, jeśli używasz certyfikatu z podpisem własnym, wybierz pozycję **Szczegóły**, a następnie pozycję **Przejdź do strony internetowej**:

![Akceptowanie ostrzeżenia dotyczącego zabezpieczeń w przeglądarce sieci Web](./media/tutorial-secure-web-server/browser-warning.png)

Zostanie wyświetlona zabezpieczona witryna internetowa usług IIS, tak jak w poniższym przykładzie:

![Wyświetlanie uruchomionej zabezpieczonej witryny usług IIS](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>Następne kroki
W tym samouczku internetowy serwer usługi IIS został zabezpieczony za pomocą certyfikatu SSL przechowywanego w usłudze Azure Key Vault. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie usługi Azure Key Vault
> * Generowanie lub przekazywanie certyfikatu do usługi Key Vault
> * Tworzenie maszyny wirtualnej i instalowanie internetowego serwera usług IIS
> * Dodawanie certyfikatu do maszyny wirtualnej i konfigurowanie usług IIS z powiązaniem SSL

Użyj tego linku, aby wyświetlić przykłady wstępnie utworzonych skryptów maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Przykłady skryptów maszyn wirtualnych z systemem Windows](./powershell-samples.md)
