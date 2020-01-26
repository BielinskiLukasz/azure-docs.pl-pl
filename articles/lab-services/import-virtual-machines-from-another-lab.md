---
title: Importowanie maszyn wirtualnych z innego laboratorium w Azure DevTest Labs
description: W tym artykule opisano sposób importowania maszyn wirtualnych z innego laboratorium do bieżącego laboratorium w Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 299d5c8758a13edded63b99abb2f12ddf9fa14be
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759520"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Importowanie maszyn wirtualnych z innego laboratorium w Azure DevTest Labs
Ten artykuł zawiera informacje o sposobach importowania maszyn wirtualnych z innego laboratorium do laboratorium.

## <a name="scenarios"></a>Scenariusze
Poniżej przedstawiono kilka scenariuszy, w których należy zaimportować maszyny wirtualne z jednego laboratorium do innego laboratorium:

- Osoba w zespole jest przenoszona do innej grupy w przedsiębiorstwie i chce przenieść pulpit dewelopera do DevTest laboratoriów.
- Grupa osiągnęła [limit przydziału na poziomie subskrypcji](../azure-resource-manager/management/azure-subscription-service-limits.md) i chce podzielić zespoły na kilka subskrypcji
- Firma jest przenoszona do trasy Express (lub innej nowej topologii sieci), a zespół chce przenieść Virtual Machines do korzystania z tej nowej infrastruktury

## <a name="solution-and-constraints"></a>Rozwiązanie i ograniczenia
Ta funkcja umożliwia importowanie maszyn wirtualnych w jednym laboratorium (źródłowym) do innego laboratorium (miejsce docelowe). Opcjonalnie możesz podać nową nazwę docelowej maszyny wirtualnej w procesie. Proces importowania obejmuje wszystkie zależności, takie jak dyski, harmonogramy, ustawienia sieci i tak dalej.

Proces zajmuje trochę czasu i ma wpływ na następujące czynniki:

- Liczba/rozmiar dysków dołączonych do maszyny źródłowej (ponieważ jest to operacja kopiowania, a nie Operacja przenoszenia)
- Odległość do miejsca docelowego (np. region Wschodnie stany USA do Azji Południowo-Wschodnia).

Po zakończeniu procesu źródłowa maszyna wirtualna pozostanie ZAMKNIĘTA, a nowa jest uruchomiona w laboratorium docelowym.

Istnieją dwa kluczowe ograniczenia, które należy wziąć pod uwagę podczas planowania importowania maszyn wirtualnych z jednego laboratorium do innego laboratorium:

- Obsługiwane są Importy maszyn wirtualnych między subskrypcjami i między regionami, ale subskrypcje muszą być skojarzone z tą samą dzierżawą Azure Active Directory.
- Virtual Machines nie może być w stanie "możliwego do zaewidencjonowania" w laboratorium źródłowym.
- Jesteś właścicielem maszyny wirtualnej w laboratorium źródłowym i właścicielem laboratorium w laboratorium docelowym.
- Obecnie ta funkcja jest obsługiwana tylko za poorednictwem programu PowerShell i interfejsu API REST.

## <a name="use-powershell"></a>Używanie programu PowerShell
Pobierz plik ImportVirtualMachines. ps1 z usługi [GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). Możesz użyć skryptu, aby zaimportować pojedynczą maszynę wirtualną lub wszystkie maszyny wirtualne w laboratorium źródłowym do laboratorium docelowego.

### <a name="use-powershell-to-import-a-single-vm"></a>Importowanie pojedynczej maszyny wirtualnej przy użyciu programu PowerShell
Wykonanie tego skryptu programu PowerShell wymaga zidentyfikowania źródłowej maszyny wirtualnej i laboratorium docelowego, a opcjonalnie podania nowej nazwy do użycia dla maszyny docelowej:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Zaimportuj wszystkie maszyny wirtualne w laboratorium źródłowym przy użyciu programu PowerShell
Jeśli źródłowa maszyna wirtualna nie jest określona, skrypt automatycznie importuje wszystkie maszyny wirtualne w DevTest Labs.  Przykład:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Importowanie maszyny wirtualnej przy użyciu protokołu HTTP REST
Wywołanie REST jest proste. Podajesz wystarczającą ilość informacji do identyfikowania zasobów źródłowych i docelowych. Należy pamiętać, że operacja odbywa się w docelowym zasobie laboratorium.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Ustawianie zasad dla laboratorium](devtest-lab-get-started-with-lab-policies.md)
- [Często zadawane pytania](devtest-lab-faq.md)
