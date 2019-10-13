---
title: Samouczek — zabezpieczanie serwera internetowego systemu Windows za pomocą certyfikatów SSL na platformie Azure | Microsoft Docs
description: Z tego samouczka dowiesz się, jak używać programu Azure PowerShell do zabezpieczania maszyny wirtualnej systemu Windows, na której działa internetowy serwer usług IIS z certyfikatami SSL przechowywanymi w usłudze Azure Key Vault.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: bf4c21616da0b27fb253bbbd24a6bf4e9acd0bf3
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300057"
---
# <a name="tutorial-secure-a-web-server-on-a-windows-virtual-machine-in-azure-with-ssl-certificates-stored-in-key-vault"></a>Samouczek: zabezpieczanie serwera internetowego na maszynie wirtualnej systemu Windows na platformie Azure przy użyciu certyfikatów SSL przechowywanych w usłudze Key Vault

Aby zabezpieczyć serwery sieci Web, można używać certyfikatu SSL (Secure Sockets Layer) do szyfrowania ruchu w sieci Web. Te certyfikaty SSL mogą być przechowywane w usłudze Azure Key Vault i umożliwiają bezpieczne wdrażanie certyfikatów na maszynach wirtualnych z systemem Windows na platformie Azure. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie usługi Azure Key Vault
> * Generowanie lub przekazywanie certyfikatu do usługi Key Vault
> * Tworzenie maszyny wirtualnej i instalowanie internetowego serwera usług IIS
> * Dodawanie certyfikatu do maszyny wirtualnej i konfigurowanie usług IIS z powiązaniem SSL


## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić usługę Cloud Shell w oddzielnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.


## <a name="overview"></a>Przegląd
Usługa Azure Key Vault chroni klucze kryptograficzne i klucze tajne, takie jak certyfikaty lub hasła. Usługa Key Vault pomaga uprościć proces zarządzania certyfikatami i pozwala zachować kontrolę nad kluczami, które uzyskują dostęp do tych certyfikatów. Możesz utworzyć certyfikat z podpisem własnym wewnątrz usługi Key Vault lub przekazać istniejący zaufany certyfikat, który już posiadasz.

Zamiast używania niestandardowego obrazu maszyny wirtualnej, który zawiera wbudowane certyfikaty, należy wstrzyknąć certyfikaty do uruchomionej maszyny wirtualnej. Ten proces zapewnia, że podczas wdrażania na serwerze sieci Web zostaną zainstalowane najbardziej aktualne certyfikaty. Jeśli odnowisz lub zamienisz certyfikat, nie musisz też tworzyć nowego niestandardowego obrazu maszyny wirtualnej. Najnowsze certyfikaty są automatycznie wstrzykiwane podczas tworzenia dodatkowych maszyn wirtualnych. W trakcie całego procesu certyfikaty nigdy nie opuszczają platformy Azure oraz nie są udostępniane w skrypcie, historii wiersza polecenia ani w szablonie.


## <a name="create-an-azure-key-vault"></a>Tworzenie usługi Azure Key Vault
Aby można było utworzyć usługę Key Vault i certyfikaty, utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroupSecureWeb* w lokalizacji *Wschodnie stany USA*:

```azurepowershell-interactive
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Następnie utwórz Key Vault przy użyciu polecenie [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault). Każda usługa Key Vault wymaga unikatowej nazwy, która powinna zawierać tylko małe litery. Zamień wartość `mykeyvault` w poniższym przykładzie na własną unikatową nazwę usługi Key Vault:

```azurepowershell-interactive
$keyvaultName="mykeyvault"
New-AzKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Generowanie certyfikatu i zapisywanie go w usłudze Key Vault
Do użycia w środowisku produkcyjnym należy zaimportować prawidłowy certyfikat podpisany przez zaufanego dostawcę przy użyciu elementu [Import-AzKeyVaultCertificate](https://docs.microsoft.com/powershell/module/az.keyvault/import-azkeyvaultcertificate). W tym samouczku Poniższy przykład pokazuje, jak można wygenerować certyfikat z podpisem własnym za pomocą instrukcji [Add-AzKeyVaultCertificate](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultcertificate) , która używa domyślnych zasad certyfikatów z poziomu funkcji [New-AzKeyVaultCertificatePolicy](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvaultcertificatepolicy). 

```azurepowershell-interactive
$policy = New-AzKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Ustaw nazwę użytkownika i hasło administratora maszyny wirtualnej przy użyciu polecenia [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Następnie utwórz maszynę wirtualną za pomocą polecenia [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). W poniższym przykładzie zostanie utworzona maszyna wirtualna o nazwie *myVM* w lokalizacji *EastUS*. Jeśli zasoby sieciowe jeszcze nie istnieją, zostaną utworzone w tym momencie. To polecenie cmdlet otwiera również port *443* w celu obsługi bezpiecznego ruchu internetowego.

```azurepowershell-interactive
# Create a VM
New-AzVm `
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
Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

Utworzenie maszyny wirtualnej może potrwać kilka minut. W ostatnim kroku niestandardowe rozszerzenie skryptu platformy Azure jest używane w celu zainstalowania internetowego serwera usług IIS przy użyciu polecenia [Set-AzVmExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension).


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Dodawanie certyfikatu do maszyny wirtualnej z poziomu usługi Key Vault
Aby dodać certyfikat z Key Vault do maszyny wirtualnej, Uzyskaj identyfikator certyfikatu za pomocą [Get-AzKeyVaultSecret](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvaultsecret). Dodaj certyfikat do maszyny wirtualnej przy użyciu polecenia [Add-AzVMSecret](https://docs.microsoft.com/powershell/module/az.compute/add-azvmsecret):

```azurepowershell-interactive
$certURL=(Get-AzKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>Konfigurowanie usług IIS do korzystania z certyfikatu
Użyj ponownie niestandardowego rozszerzenia skryptu z poleceniem [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension), aby zaktualizować konfigurację usług IIS. Ta aktualizacja dotyczy certyfikatu dodanego z usługi Key Vault do usług IIS i konfiguruje powiązanie internetowe:

```azurepowershell-interactive
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>Testowanie bezpiecznej aplikacji internetowej
Uzyskaj publiczny adres IP maszyny wirtualnej za pomocą polecenia [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). W poniższym przykładzie uzyskano utworzony wcześniej adres IP `myPublicIP`:

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIPAddress" | select "IpAddress"
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
