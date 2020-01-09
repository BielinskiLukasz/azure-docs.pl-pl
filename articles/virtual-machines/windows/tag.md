---
title: Jak oznaczyć zasób maszyny wirtualnej z systemem Windows na platformie Azure
description: Dowiedz się więcej na temat tagowania maszyny wirtualnej z systemem Windows utworzonej na platformie Azure przy użyciu modelu wdrażania Menedżer zasobów
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: b17a126ac870e96c79c4c391f5f8381ce70f8d41
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462297"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Jak oznaczyć maszynę wirtualną z systemem Windows na platformie Azure
W tym artykule opisano różne sposoby tagowania maszyny wirtualnej z systemem Windows na platformie Azure za pomocą modelu wdrażania Menedżer zasobów. Tagi to zdefiniowane przez użytkownika pary klucz/wartość, które mogą być umieszczone bezpośrednio w ramach zasobu lub grupy zasobów. Platforma Azure obsługuje obecnie do 15 tagów na zasób i grupę zasobów. Tagi mogą być umieszczane na zasobie w momencie tworzenia lub dodawane do istniejącego zasobu. Należy pamiętać, że Tagi są obsługiwane tylko dla zasobów utworzonych za pośrednictwem modelu wdrażania Menedżer zasobów. Jeśli chcesz oznaczyć maszynę wirtualną z systemem Linux, zobacz [jak oznaczyć maszynę wirtualną z systemem Linux na platformie Azure](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Tagowanie przy użyciu programu PowerShell
Aby tworzyć, dodawać i usuwać Tagi za pomocą programu PowerShell, należy najpierw skonfigurować [środowisko programu PowerShell przy użyciu Azure Resource Manager][PowerShell environment with Azure Resource Manager]. Po zakończeniu instalacji możesz umieścić znaczniki w zasobach obliczeniowych, sieci i magazynu podczas tworzenia lub po utworzeniu zasobu za pośrednictwem programu PowerShell. Ten artykuł koncentruje się na wyświetlaniu i edytowaniu tagów umieszczonych w Virtual Machines.

 

Najpierw przejdź do maszyny wirtualnej za pomocą polecenia cmdlet `Get-AzVM`.

        PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Jeśli maszyna wirtualna zawiera już Tagi, zobaczysz wszystkie Tagi w zasobie:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Jeśli chcesz dodać tagi za pomocą programu PowerShell, możesz użyć polecenia `Set-AzResource`. Uwaga w przypadku aktualizowania tagów za pomocą programu PowerShell Tagi są aktualizowane jako całość. Dlatego jeśli dodajesz jeden tag do zasobu, który ma już Tagi, musisz uwzględnić wszystkie Tagi, które mają zostać umieszczone w zasobie. Poniżej znajduje się przykład dodawania dodatkowych tagów do zasobu za pomocą poleceń cmdlet programu PowerShell.

To pierwsze polecenie cmdlet ustawia wszystkie znaczniki umieszczone w *MyTestVM* do zmiennej *$Tags* przy użyciu właściwości `Get-AzResource` i `Tags`.

        PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Drugie polecenie wyświetla Tagi dla danej zmiennej.

```
    PS C:\> $tags
    
    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
```

Trzecie polecenie dodaje dodatkowy tag do zmiennej *$Tags* . Zwróć uwagę na użycie **+=** , aby dołączyć nową parę klucz/wartość do listy *$Tags* .

        PS C:\> $tags += @{Location="MyLocation"}

Czwarte polecenie ustawia wszystkie Tagi zdefiniowane w zmiennej *$Tags* dla danego zasobu. W tym przypadku jest to MyTestVM.

        PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Piąte polecenie wyświetla wszystkie Tagi w zasobie. Jak widać, *Lokalizacja* jest teraz definiowana jako tag z elementem *weblocation* jako wartość.

```
    PS C:\> (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
    Location      MyLocation
```

Aby dowiedzieć się więcej na temat tagowania za pomocą programu PowerShell, zapoznaj się z [poleceniami cmdlet zasobów platformy Azure][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej o znakowaniu zasobów platformy Azure, zobacz [Azure Resource Manager omówienie][Azure Resource Manager Overview] i [Używanie tagów do organizowania zasobów platformy Azure][Using Tags to organize your Azure Resources].
* Aby dowiedzieć się, jak tagi mogą ułatwić zarządzanie użyciem zasobów platformy Azure, zobacz [Opis rachunku na korzystanie z platformy Azure][Understanding your Azure Bill] i [Uzyskiwanie szczegółowych informacji o zużyciu zasobów Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/manage-resources-powershell.md
[Azure Resource Cmdlets]: https://docs.microsoft.com/powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
