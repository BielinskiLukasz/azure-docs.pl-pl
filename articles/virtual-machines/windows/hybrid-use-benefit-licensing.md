---
title: Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server | Microsoft Docs
description: Dowiedz się, jak zmaksymalizować korzyści z pakietu Software Assurance dla systemu Windows, aby zapewnić lokalne licencje na platformę Azure
services: virtual-machines-windows
documentationcenter: ''
author: xujing-ms
manager: gwallace
editor: ''
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing
ms.openlocfilehash: e3928d865178d0afc3d814ae0d7794f981f49d47
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70079410"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server
W przypadku klientów z programem Software Assurance Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server umożliwia korzystanie z lokalnych licencji systemu Windows Server i uruchamianie maszyn wirtualnych z systemem Windows na platformie Azure przy niższych kosztach. Za pomocą Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server można wdrożyć nowe maszyny wirtualne z systemem operacyjnym Windows. W tym artykule opisano procedurę wdrażania nowych maszyn wirtualnych przy użyciu programu Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server i sposobu aktualizowania istniejących uruchomionych maszyn wirtualnych. Aby uzyskać więcej informacji na temat Korzyść użycia hybrydowego platformy Azure licencjonowania i oszczędności w systemie Windows Server, zobacz [stronę licencjonowanie korzyść użycia hybrydowego platformy Azure dla systemu Windows Server](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!Important]
> Każda licencja na 2 procesory lub każdy zestaw licencji na 16 rdzeni jest uprawniony do korzystania z dwóch wystąpień z maksymalnie 8 rdzeniami lub jednego wystąpienia z maksymalnie 16 rdzeniami. Korzyści z używania hybrydowej platformy Azure dla licencji Standard Edition mogą być używane tylko raz lokalnie lub na platformie Azure. Korzyści wersji Datacenter Edition pozwalają na równoczesne używanie zarówno lokalnie, jak i na platformie Azure.
>

> [!Important]
> Używanie Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server z maszynami wirtualnymi z systemem Windows Server OS jest teraz obsługiwane we wszystkich regionach, w tym na maszynach wirtualnych z dodatkowym oprogramowaniem, takim jak SQL Server lub oprogramowanie Marketplace innej firmy. 
>

> [!NOTE]
> W przypadku klasycznych maszyn wirtualnych obsługiwana jest tylko wdrażanie nowej maszyny wirtualnej z lokalnych obrazów niestandardowych. Aby skorzystać z możliwości obsługiwanych w tym artykule, należy najpierw przeprowadzić migrację klasycznych maszyn wirtualnych do modelu Menedżer zasobów.
>

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Sposoby korzystania z Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server
Istnieje kilka sposobów używania maszyn wirtualnych z systemem Windows z Korzyść użycia hybrydowego platformy Azure:

1. Maszyny wirtualne można wdrażać z jednego z dostarczonych obrazów systemu Windows Server w portalu Azure Marketplace
2. Możesz przekazać niestandardową maszynę wirtualną i wdrożyć ją przy użyciu szablonu Menedżer zasobów lub Azure PowerShell
3. Istnieje możliwość przełączenia i przekonwertowania istniejącej maszyny wirtualnej między uruchomionym systemem a Korzyść użycia hybrydowego platformy Azure lub zapłacić koszt na żądanie dla systemu Windows Server
4. Możesz również zastosować Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server w zestawie skalowania maszyn wirtualnych


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Tworzenie maszyny wirtualnej z Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server
Wszystkie obrazy oparte na systemie Windows Server OS są obsługiwane dla Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server. Możesz użyć obrazów obsługi platformy Azure lub przekazać własne niestandardowe obrazy systemu Windows Server. 

### <a name="portal"></a>Portal
Aby utworzyć maszynę wirtualną za pomocą Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server, należy użyć przełącznika w sekcji "Zapisz pieniądze".

### <a name="powershell"></a>PowerShell


```powershell
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -ImageName "Win2016Datacenter" `
    -LicenseType "Windows_Server"
```

### <a name="cli"></a>Interfejs wiersza polecenia
```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --license-type Windows_Server
```

### <a name="template"></a>Szablon
W szablonach Menedżer zasobów należy określić dodatkowy parametr `licenseType` . Aby dowiedzieć się więcej na temat [tworzenia szablonów Azure Resource Manager](../../resource-group-authoring-templates.md) , zobacz
```json
"properties": {
    "licenseType": "Windows_Server",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Konwertowanie istniejącej maszyny wirtualnej przy użyciu Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server
Jeśli masz istniejącą maszynę wirtualną, którą chcesz przekonwertować, aby skorzystać z Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server, możesz zaktualizować typ licencji maszyny wirtualnej, postępując zgodnie z poniższymi instrukcjami.

> [!NOTE]
> Zmiana typu licencji na maszynie wirtualnej nie powoduje ponownego uruchomienia systemu lub spowodowania usługi interuption.  Jest po prostu aktualizacją flagi metadanych.
> 

### <a name="portal"></a>Portal
W bloku maszyny wirtualnej portalu możesz zaktualizować maszynę wirtualną, aby używała Korzyść użycia hybrydowego platformy Azure, wybierając opcję "Konfiguracja" i przełączając opcję "korzyść użycia hybrydowego platformy Azure"

### <a name="powershell"></a>PowerShell
- Konwertuj istniejące maszyny wirtualne z systemem Windows Server na Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Konwertuj maszyny wirtualne z systemem Windows Server z korzyścią z używania z powrotem na płatność zgodnie z rzeczywistym użyciem

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>Interfejs wiersza polecenia
- Konwertuj istniejące maszyny wirtualne z systemem Windows Server na Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```

### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Jak sprawdzić, czy maszyna wirtualna korzysta z korzyści z licencjonowania
Po wdrożeniu maszyny wirtualnej za pomocą programu PowerShell, Menedżer zasobów szablonu lub portalu można sprawdzić ustawienia w następujących metodach.

### <a name="portal"></a>Portal
W bloku maszyny wirtualnej portalu można wyświetlić przełącznik dla Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server, wybierając kartę "Konfiguracja".

### <a name="powershell"></a>PowerShell
W poniższym przykładzie przedstawiono typ licencji dla jednej maszyny wirtualnej
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Dane wyjściowe:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Ten wynik różni się od następującej wdrożonej maszyny wirtualnej bez Korzyść użycia hybrydowego platformy Azure na potrzeby licencjonowania systemu Windows Server:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

### <a name="cli"></a>Interfejs wiersza polecenia
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query "[?licenseType=='Windows_Server']" -o table
```

> [!NOTE]
> Zmiana typu licencji na maszynie wirtualnej nie powoduje ponownego uruchomienia systemu lub spowodowania usługi interuption. Jest to tylko flaga licencjonowania metadanych.
>

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Wyświetl listę wszystkich maszyn wirtualnych z Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server w ramach subskrypcji
Aby wyświetlić i zliczyć wszystkie maszyny wirtualne wdrożone za pomocą Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server, możesz uruchomić następujące polecenie w ramach subskrypcji:

### <a name="portal"></a>Portal
W bloku zasobów maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych można wyświetlić listę wszystkich maszyn wirtualnych i typ licencjonowania, konfigurując kolumnę tabeli w celu uwzględnienia "Korzyść użycia hybrydowego platformy Azure". Ustawienie maszyny wirtualnej może być w stanie "włączone", "niewłączone" lub "nieobsługiwane".

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzVM
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>Interfejs wiersza polecenia
```azurecli
az vm list --query "[?licenseType=='Windows_Server']" -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Wdrażanie zestawu skalowania maszyn wirtualnych za pomocą Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server
W ramach zestawu skalowania maszyn wirtualnych Menedżer zasobów szablonów należy określić dodatkowy `licenseType` parametr we właściwości VirtualMachineProfile. Możesz to zrobić podczas tworzenia lub aktualizowania zestawu skalowania za pomocą szablonu ARM, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub REST.

Poniższy przykład używa szablonu ARM z obrazem systemu Windows Server 2016 Datacenter:
```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
Możesz również dowiedzieć się więcej na temat [modyfikowania zestawu skalowania maszyn wirtualnych](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) , aby uzyskać więcej sposobów aktualizowania zestawu skalowania.

## <a name="next-steps"></a>Następne kroki
- Przeczytaj więcej o [tym, jak zaoszczędzić pieniądze w korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- Przeczytaj więcej [na temat często zadawanych pytań dotyczących korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- Dowiedz się więcej [na temat szczegółowych wskazówek dotyczących licencjonowania systemu Windows Server korzyść użycia hybrydowego platformy Azure](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)
- Dowiedz się więcej [na temat korzyść użycia hybrydowego platformy Azure dla systemu Windows Server i Azure Site Recovery Migrowanie aplikacji na platformę Azure jeszcze bardziej opłacalne](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)
- Dowiedz się więcej o [systemie Windows 10 na platformie Azure z prawem hostingu](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) wielodostępnego
- Dowiedz się więcej o [korzystaniu z szablonów Menedżer zasobów](../../azure-resource-manager/resource-group-overview.md)
