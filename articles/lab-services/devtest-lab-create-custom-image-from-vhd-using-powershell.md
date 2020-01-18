---
title: Tworzenie niestandardowego obrazu z pliku VHD przy użyciu Azure PowerShell
description: Automatyzowanie tworzenia niestandardowego obrazu w Azure DevTest Labs z pliku VHD przy użyciu programu PowerShell
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: cd144659dd8a8e981e267be998c9c783b7482840
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169578"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>Tworzenie obrazu niestandardowego z pliku VHD przy użyciu programu PowerShell

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>Instrukcje krok po kroku

Poniższe kroki przeprowadzą Cię przez proces tworzenia niestandardowego obrazu z pliku VHD przy użyciu programu PowerShell:

1. W wierszu polecenia programu PowerShell Zaloguj się do konta platformy Azure przy użyciu następującego wywołania polecenia cmdlet **Connect-AzAccount** .

    ```powershell
    Connect-AzAccount
    ```

1.  Wybierz żądaną subskrypcję platformy Azure, wywołując polecenie cmdlet **SELECT-AzSubscription** . Zastąp następujący symbol zastępczy dla zmiennej **$subscriptionId** z PRAWIDŁOWYm identyfikatorem subskrypcji platformy Azure.

    ```powershell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  Pobierz obiekt Lab, wywołując polecenie cmdlet **Get-AzResource** . Zastąp następujące symbole zastępcze dla zmiennych **$labRg** i **$labName** wartościami odpowiednimi dla danego środowiska.

    ```powershell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```

1.  Zastąp następujący symbol zastępczy zmiennej **$VHDURI** identyfikatorem URI do przekazanego pliku VHD. Możesz uzyskać identyfikator URI pliku wirtualnego dysku twardego z bloku obiektu BLOB konta magazynu w Azure Portal.

    ```powershell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  Utwórz niestandardowy obraz przy użyciu polecenia cmdlet **New-AzResourceGroupDeployment** . Zastąp następujące symbole zastępcze dla zmiennych **$customImageName** i **$customImageDescription** , aby odznaczenie nazw dla danego środowiska.

    ```powershell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>Skrypt programu PowerShell służący do tworzenia obrazu niestandardowego na podstawie pliku VHD

Poniższy skrypt programu PowerShell może służyć do tworzenia obrazu niestandardowego z pliku VHD. Zamień symbole zastępcze (zaczynające się i kończące na nawiasy kątowe) z odpowiednimi wartościami dla Twoich potrzeb.

```powershell
# Log in to your Azure account.
Connect-AzAccount

# Select the desired Azure subscription.
$subscriptionId = '<Specify your subscription ID here>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Set the URI of the VHD file.
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image.
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>Powiązane wpisy w blogu

- [Obrazy niestandardowe lub formuły?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiowanie obrazów niestandardowych między Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Następne kroki

- [Dodawanie maszyny wirtualnej do laboratorium](devtest-lab-add-vm.md)
