---
title: Korzystanie z programu Azure Image Builder z galerią obrazów dla maszyn wirtualnych z systemem Windows (wersja zapoznawcza)
description: Tworzenie wersji obrazu udostępnionej galerii platformy Azure przy użyciu narzędzia Azure Image Builder i Azure PowerShell.
author: cynthn
ms.author: cynthn
ms.date: 01/14/2020
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: d5856780d0d9f1a1943bca1c2f076bb3ec914e1d
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263357"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Wersja zapoznawcza: Tworzenie obrazu systemu Windows i dystrybuowanie go do galerii obrazów udostępnionych 

W tym artykule opisano, jak można użyć programu Azure Image Builder i Azure PowerShell, aby utworzyć wersję obrazu w [galerii obrazów udostępnionych](shared-image-galleries.md), a następnie dystrybuować obraz globalnie. Można to również zrobić przy użyciu [interfejsu wiersza polecenia platformy Azure](../linux/image-builder-gallery.md).

Aby skonfigurować obraz, będziemy używać szablonu JSON. Używany plik JSON jest tutaj: [armTemplateWinSIG. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). Będziemy pobierać i edytować lokalną wersję szablonu, więc ten artykuł zostanie zapisany przy użyciu lokalnej sesji programu PowerShell.

W celu rozesłania obrazu do galerii obrazów udostępnionych szablon używa [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) jako wartości sekcji `distribute` szablonu.

Usługa Azure Image Builder automatycznie uruchamia program Sysprep w celu uogólnienia obrazu. jest to ogólne polecenie programu Sysprep, które można [przesłonić](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#vms-created-from-aib-images-do-not-create-successfully) w razie potrzeby. 

Dowiedzieć się, ile razy są dostosowywane warstwy. Można uruchomić polecenie Sysprep do 8 razy w pojedynczym obrazie systemu Windows. Po uruchomieniu programu Sysprep 8 należy ponownie utworzyć obraz systemu Windows. Aby uzyskać więcej informacji, zobacz [limity czasu, w którym można uruchomić program Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep). 

> [!IMPORTANT]
> Usługa Azure Image Builder jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Rejestrowanie funkcji
Aby korzystać z usługi Azure Image Builder w wersji zapoznawczej, należy zarejestrować nową funkcję.

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Sprawdź stan rejestracji funkcji.

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Poczekaj, aż `RegistrationState` zostanie `Registered` przed przejściem do następnego kroku.

Sprawdź rejestracje dostawcy. Upewnij się, że każdy zwraca `Registered`.

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
```

Jeśli nie zwracają `Registered`, wykonaj następujące czynności w celu zarejestrowania dostawców:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="create-variables"></a>Tworzenie zmiennych

Będziemy wielokrotnie używać niektórych informacji, więc utworzymy pewne zmienne do przechowywania tych informacji. Zastąp wartości dla zmiennych, takich jak `username` i `vmpassword`, własnymi informacjami.

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"
```



## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów i nadaj usłudze Azure Image Builder uprawnienia do tworzenia zasobów w tej grupie zasobów.

```powershell
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
New-AzRoleAssignment `
   -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 `
   -Scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup `
   -RoleDefinitionName Contributor
```



## <a name="create-the-shared-image-gallery"></a>Tworzenie galerii obrazów udostępnionych

Aby użyć konstruktora obrazów z udostępnioną galerią obrazów, musisz mieć istniejącą galerię obrazów i definicję obrazu. Konstruktor obrazów nie utworzy galerii obrazów i definicji obrazu.

Jeśli nie masz jeszcze galerii i definicji obrazu do użycia, Zacznij od utworzenia ich. Najpierw utwórz galerię obrazów.

```powershell
# Image gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>Pobieranie i Konfigurowanie szablonu

Pobierz szablon JSON i skonfiguruj go przy użyciu zmiennych.

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath

```


## <a name="create-the-image-version"></a>Utwórz wersję obrazu

Szablon musi zostać przesłany do usługi. spowoduje to pobranie wszelkich artefaktów zależnych, takich jak skrypty, i zapisanie ich w tymczasowej grupie zasobów, z prefiksem *IT_* .

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -api-version "2019-05-01-preview" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

W celu skompilowania obrazu potrzebnego do wywołania elementu "Run" w szablonie.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2019-05-01-preview" `
   -Action Run
```

Tworzenie obrazu i replikowanie go do obu regionów może chwilę potrwać. Przed przejściem do tworzenia maszyny wirtualnej Zaczekaj, aż ta część zostanie zakończona.

Aby uzyskać informacje na temat opcji automatyzacji pobierania stanu kompilacji obrazu, zobacz [plik Readme](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/readme.md#get-status-of-the-image-build-and-query) dla tego szablonu w witrynie GitHub.


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną na podstawie wersji obrazu, która została utworzona przez usługę Azure Image Builder.

Pobierz utworzoną wersję obrazu.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

Utwórz maszynę wirtualną w drugim regionie, w której zainstalowano obraz.

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>Sprawdzanie dostosowania
Utwórz połączenie Pulpit zdalny z maszyną wirtualną przy użyciu nazwy użytkownika i hasła ustawione podczas tworzenia maszyny wirtualnej. Na maszynie wirtualnej Otwórz wiersz polecenia i wpisz:

```console
dir c:\
```

Powinien zostać wyświetlony katalog o nazwie `buildActions`, który został utworzony podczas dostosowywania obrazu.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli chcesz teraz spróbować ponownie dostosować wersję obrazu, aby utworzyć nową wersję tego samego obrazu, **Pomiń ten krok** i przejdź do, aby [użyć usługi Azure Image Builder do utworzenia innej wersji obrazu](image-builder-gallery-update-image-version.md).


Spowoduje to usunięcie utworzonego obrazu wraz ze wszystkimi innymi plikami zasobów. Przed usunięciem zasobów upewnij się, że to wdrożenie zostało zakończone.

Najpierw należy usunąć szablon grupy zasobów. w przeciwnym razie tymczasowa Grupa zasobów (*IT_* ) używana przez AIB nie zostanie wyczyszczona.

Pobieranie ResourceID szablonu obrazu. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2019-05-01-preview"
```

Usuń szablon obrazu.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

Usuń grupę zasobów.

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zaktualizować utworzoną wersję obrazu, zobacz [Korzystanie z programu Azure Image Builder w celu utworzenia innej wersji obrazu](image-builder-gallery-update-image-version.md).
