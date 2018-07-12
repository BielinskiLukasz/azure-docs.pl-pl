---
title: Jak oznaczyć zasób maszynę Wirtualną Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o znakowaniu Windows maszyny wirtualnej utworzonej na platformie Azure przy użyciu modelu wdrażania usługi Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: 5044bda855b6ac88eb5784f257686bf8a1838222
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38531324"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Jak oznaczyć maszynę wirtualną Windows na platformie Azure
W tym artykule opisano różne sposoby, aby oznaczyć maszynę wirtualną Windows na platformie Azure za pomocą modelu wdrażania usługi Resource Manager. Tagi to pary klucz/wartość zdefiniowanych przez użytkownika, które mogą być umieszczone bezpośrednio na zasób lub grupa zasobów. Platforma Azure obsługuje obecnie maksymalnie 15 tagów na zasób i grupy zasobów. Znaczniki może być umieszczone na zasób w czasie tworzenia lub dodawane do istniejącego zasobu. Należy pamiętać, że tagi są obsługiwane w przypadku zasobów utworzonych za pomocą modelu wdrażania usługi Resource Manager tylko. Jeśli chcesz oznaczyć maszynę wirtualną systemu Linux, zobacz [jak oznaczyć maszynę wirtualną z systemem Linux na platformie Azure](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Znakowanie przy użyciu programu PowerShell
Aby utworzyć, dodawanie i usuwanie tagów za pomocą programu PowerShell, najpierw trzeba skonfigurować swoje [środowiska PowerShell z usługą Azure Resource Manager][PowerShell environment with Azure Resource Manager]. Po zakończeniu instalacji można umieścić znaczniki dla zasobów obliczeniowych, sieci i magazynu podczas tworzenia lub po utworzeniu zasobu za pomocą programu PowerShell. Ten artykuł koncentruje się na wyświetlanie oraz edytowanie tagów umieszczone na maszynach wirtualnych.

Przejdź do maszyny wirtualnej za pośrednictwem `Get-AzureRmVM` polecenia cmdlet.

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Jeśli maszyna wirtualna zawiera już znaczników, następnie zobaczysz wszystkie tagi zasobu:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Jeśli chcesz dodać tagi za pomocą programu PowerShell, możesz użyć `Set-AzureRmResource` polecenia. Należy pamiętać podczas aktualizowania tagów za pomocą programu PowerShell, tagi zostaną zaktualizowane jako całości. Więc jeden tag w przypadku dodawania do zasobu, który ma już tagi, konieczne będzie obejmować wszystkie tagi, które mają zostać umieszczone w zasobie. Poniżej przedstawiono przykładowy sposób dodać dodatkowe tagi do zasobu za pomocą poleceń cmdlet programu PowerShell.

To pierwsze polecenie cmdlet Ustawia wszystkie tagi umieszczone na *MyTestVM* do *$tags* zmiennej za pomocą `Get-AzureRmResource` i `Tags` właściwości.

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Drugie polecenie wyświetla znaczniki dla danego zmiennej.

        PS C:\> $tags

        Name        Value
        ----                           -----
        Value        MyDepartment
        Name        Department
        Value        MyApp1
        Name        Application
        Value        MyName
        Name        Created By
        Value        Production
        Name        Environment

Trzecie polecenie dodaje tag dodatkowe *$tags* zmiennej. Zwróć uwagę na użycie **+=** można dołączyć nową parę kluczy/wartości do *$tags* listy.

        PS C:\> $tags += @{Name="Location";Value="MyLocation"}

Czwarty polecenie ustawia parametry logowania wszystkich znaczników zdefiniowane w *$tags* zmiennej do danego zasobu. W tym przypadku jest to MyTestVM.

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Piąty polecenie wyświetla listę wszystkich tagów w zasobie. Jak widać, *lokalizacji* teraz jest zdefiniowany jako tag *MyLocation* jako wartość.

        PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

        Name        Value
        ----                           -----
        Value        MyDepartment
        Name        Department
        Value        MyApp1
        Name        Application
        Value        MyName
        Name        Created By
        Value        Production
        Name        Environment
        Value        MyLocation
        Name        Location

Aby dowiedzieć się więcej o znakowaniu za pomocą programu PowerShell, zapoznaj się z [polecenia cmdlet usługi Azure Resource][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej na temat tagowania zasobów platformy Azure, zobacz [Omówienie usługi Azure Resource Manager] [ Azure Resource Manager Overview] i [za pomocą tagów do organizowania zasobów platformy Azure] [ Using Tags to organize your Azure Resources].
* Aby dowiedzieć się, jak tagi zarządzanie może ułatwić korzystanie z zasobów platformy Azure, zobacz [informacje o rachunku Azure] [ Understanding your Azure Bill] i [wgląd w użycie zasobów usługi Microsoft Azure] [Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/powershell-azure-resource-manager.md
[Azure Resource Cmdlets]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
