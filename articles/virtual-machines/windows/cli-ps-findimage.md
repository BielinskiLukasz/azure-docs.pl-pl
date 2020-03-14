---
title: Wybieranie obrazów maszyn wirtualnych z systemem Windows na platformie Azure
description: Użyj Azure PowerShell, aby określić wydawcę, ofertę, jednostkę SKU i wersję dla obrazów maszyn wirtualnych z witryny Marketplace.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/25/2019
ms.author: cynthn
ms.openlocfilehash: 2388b51c8103b6bcbae0c32d3c4d78a176caf282
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266808"
---
# <a name="find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Znajdź obrazy maszyn wirtualnych z systemem Windows w witrynie Azure Marketplace przy użyciu Azure PowerShell

W tym artykule opisano, jak używać Azure PowerShell do znajdowania obrazów maszyn wirtualnych w portalu Azure Marketplace. Następnie można określić obraz z witryny Marketplace podczas programistycznego tworzenia maszyny wirtualnej przy użyciu programu PowerShell, szablonów Menedżer zasobów lub innych narzędzi.

Dostępne obrazy i oferty można także przeglądać za pomocą [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/) , [Azure Portal](https://portal.azure.com)lub [interfejsu wiersza polecenia platformy Azure](../linux/cli-ps-findimage.md). 

 

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>Tabela często używanych obrazów systemu Windows

W tej tabeli przedstawiono podzestaw dostępnych jednostek SKU dla wskazanych wydawców i ofert.

| Wydawca | Oferta | SKU |
|:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2019 — centrum danych |
| MicrosoftWindowsServer |WindowsServer |2019 — centrum danych — rdzeń |
| MicrosoftWindowsServer |WindowsServer |2019 — Datacenter-with-Containers |
| MicrosoftWindowsServer |WindowsServer |2016 — centrum danych |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016 — Datacenter-with-Containers |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2019 |
| MicrosoftSQLServer |SQL2019-WS2016 |Enterprise |
| MicrosoftRServer |RServer — WS2016 |Enterprise |

## <a name="navigate-the-images"></a>Nawigowanie po obrazach

Jednym ze sposobów znalezienia obrazu w lokalizacji jest uruchomienie poleceń cmdlet [Get-AzVMImagePublisher](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher), [Get-AzVMImageOffer](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer)i [Get-AzVMImageSku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) w kolejności:

1. Wyświetl listę wydawców obrazów.
2. Dla danego wydawcy wyświetl listę ofert.
3. Dla danej oferty wyświetl listę wersji SKU.

Następnie dla wybranej jednostki SKU Uruchom polecenie [Get-AzVMImage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimage) , aby wyświetlić listę wersji do wdrożenia.

1. Wyświetl listę wydawców:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Wypełnij wybraną nazwę wydawcy i Wyświetl listę ofert:

    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```

3. Wypełnij wybraną nazwę oferty i Wyświetl listę jednostek SKU:

    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```

4. Wypełnij wybraną nazwę jednostki SKU i Pobierz wersję obrazu:

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
Na podstawie danych wyjściowych polecenia `Get-AzVMImage` można wybrać obraz wersji, aby wdrożyć nową maszynę wirtualną.

Poniższy przykład przedstawia pełną sekwencję poleceń i ich dane wyjściowe:

```powershell
$locName="West US"
Get-AzVMImagePublisher -Location $locName | Select PublisherName
```

Częściowe dane wyjściowe:

```
PublisherName
-------------
...
abiquo
accedian
accellion
accessdata-group
accops
Acronis
Acronis.Backup
actian-corp
actian_matrix
actifio
activeeon
adgs
advantech
advantech-webaccess
advantys
...
```

Dla wydawcy *MicrosoftWindowsServer* :

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
```

Dane wyjściowe:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

W przypadku oferty *WindowsServer* :

```powershell
$offerName="WindowsServer"
Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
```

Częściowe dane wyjściowe:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Datacenter-with-RDSH
2019-Datacenter
2019-Datacenter-Core
2019-Datacenter-Core-smalldisk
2019-Datacenter-Core-with-Containers
...
```

Następnie dla jednostki SKU *2019-Datacenter* :

```powershell
$skuName="2019-Datacenter"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
```

Teraz można połączyć wybranego wydawcy, oferty, jednostki SKU i wersji na nazwę URN (wartości rozdzielone:). Przekaż tę nazwę URN z parametrem `--image` podczas tworzenia maszyny wirtualnej za pomocą polecenia cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) . Opcjonalnie możesz zamienić numer wersji w nazwie URN na "Najnowsza", aby uzyskać najnowszą wersję obrazu.

W przypadku wdrożenia maszyny wirtualnej z szablonem Menedżer zasobów, parametry obrazu są ustawiane indywidualnie we właściwościach `imageReference`. Zobacz [dokumentację szablonu](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Wyświetl właściwości planu

Aby wyświetlić informacje o planie zakupu obrazu, uruchom polecenie cmdlet `Get-AzVMImage`. Jeśli właściwość `PurchasePlan` w danych wyjściowych nie jest `null`, obraz zawiera warunki, które należy zaakceptować przed wdrożeniem programistycznym.  

Na przykład obraz *systemu Windows Server 2016 Datacenter* nie ma dodatkowych warunków, więc `PurchasePlan` informacje są `null`:

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

Dane wyjściowe:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

W poniższym przykładzie pokazano podobne polecenie dla obrazu *Data Science Virtual Machine-Windows 2016* , który ma następujące właściwości `PurchasePlan`: `name`, `product`i `publisher`. Niektóre obrazy mają również właściwość `promotion code`. Aby wdrożyć ten obraz, zapoznaj się z następującymi sekcjami, aby zaakceptować warunki i włączyć wdrażanie programistyczne.

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Dane wyjściowe:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMImage/Offers/windows-data-science-vm/Skus/windows2016/Versions/19.01.14
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 19.01.14
FilterExpression :
Name             : 19.01.14
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

### <a name="accept-the-terms"></a>Zaakceptuj warunki

Aby wyświetlić postanowienia licencyjne, użyj polecenia cmdlet [Get-AzMarketplaceterms](https://docs.microsoft.com/powershell/module/az.marketplaceordering/get-azmarketplaceterms) i przekaż parametry planu zakupu. Dane wyjściowe zawierają link do warunków dla obrazu z witryny Marketplace i pokazują, czy zostały wcześniej zaakceptowane warunki. Upewnij się, że w wartości parametrów są używane wszystkie małe litery.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Dane wyjściowe:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

Użyj polecenia cmdlet [Set-AzMarketplaceterms](https://docs.microsoft.com/powershell/module/az.marketplaceordering/set-azmarketplaceterms) , aby zaakceptować lub odrzucić warunki. Musisz tylko raz zaakceptować warunki dla każdej subskrypcji obrazu. Upewnij się, że w wartości parametrów są używane wszystkie małe litery. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```

Dane wyjściowe:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```

### <a name="deploy-using-purchase-plan-parameters"></a>Wdrażanie przy użyciu parametrów planu zakupu

Po zaakceptowaniu postanowień dotyczących obrazu można wdrożyć maszynę wirtualną w tej subskrypcji. Jak pokazano w poniższym fragmencie kodu, użyj polecenia cmdlet [Set-AzVMPlan](https://docs.microsoft.com/powershell/module/az.compute/set-azvmplan) , aby ustawić informacje o planie witryny Marketplace dla obiektu maszyny wirtualnej. Aby uzyskać kompletny skrypt służący do tworzenia ustawień sieci dla maszyny wirtualnej i dokończyć wdrożenie, zobacz [przykłady skryptów programu PowerShell](powershell-samples.md).

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace plan information

$publisherName = "microsoft-ads"

$productName = "windows-data-science-vm"

$planName = "windows2016"

$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

$cred=Get-Credential

$vmConfig = Set-AzVMOperatingSystem -Windows -VM $vmConfig -ComputerName "myVM" -Credential $cred

# Set the Marketplace image

$offerName = "windows-data-science-vm"

$skuName = "windows2016"

$version = "19.01.14"

$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version
...
```
Następnie należy przekazać konfigurację maszyny wirtualnej wraz z obiektami konfiguracji sieci do polecenia cmdlet `New-AzVM`.

## <a name="next-steps"></a>Następne kroki

Aby szybko utworzyć maszynę wirtualną za pomocą polecenia cmdlet `New-AzVM` przy użyciu podstawowych informacji o obrazie, zobacz [Tworzenie maszyny wirtualnej z systemem Windows przy użyciu programu PowerShell](quick-create-powershell.md).


Zobacz przykład skryptu programu PowerShell, aby [utworzyć w pełni skonfigurowaną maszynę wirtualną](../scripts/virtual-machines-windows-powershell-sample-create-vm.md).


