---
title: Rozszerzenie sterownika GPU NVIDIA — maszyny wirtualne platformy Azure z systemem Windows
description: Microsoft Azure rozszerzenie do instalowania sterowników NVIDIA GPU na maszynach wirtualnych obliczeniowych serii N z systemem Windows.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: akjosh
ms.openlocfilehash: c388f433327b5328483f10fbef637a6fdfd08832
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79250545"
---
# <a name="nvidia-gpu-driver-extension-for-windows"></a>Rozszerzenie sterownika GPU NVIDIA dla systemu Windows

## <a name="overview"></a>Omówienie

To rozszerzenie powoduje zainstalowanie sterowników NVIDIA GPU na maszynach wirtualnych z serii N systemu Windows. W zależności od rodziny maszyn wirtualnych rozszerzenie instaluje sterowniki CUDA lub GRID. Jeśli instalujesz sterowniki firmy NVIDIA przy użyciu tego rozszerzenia, akceptujesz i wyrażasz zgodę na warunki [umowy licencyjnej użytkownika końcowego firmy NVIDIA](https://go.microsoft.com/fwlink/?linkid=874330). Podczas instalacji maszyna wirtualna może zostać ponownie przetworzony w celu ukończenia instalacji sterownika.

Instrukcje dotyczące ręcznej instalacji sterowników i aktualnie obsługiwanych wersji są dostępne [tutaj](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup).
Rozszerzenie jest również dostępne do zainstalowania sterowników NVIDIA GPU na [maszynach wirtualnych z serii N systemu Linux](hpccompute-gpu-linux.md).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

To rozszerzenie obsługuje następujące elementy OSs:

| Dystrybucja | Wersja |
|---|---|
| Windows 10 | Podstawowe |
| Windows Server 2016 | Podstawowe |
| Windows Server 2012 R2 | Podstawowe |

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie Microsoft Azure dla sterowników procesora GPU firmy NVIDIA wymaga, aby docelowa maszyna wirtualna była połączona z Internetem i miała dostęp.

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniższy kod JSON pokazuje schemat rozszerzenia.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Właściwości

| Name (Nazwa) | Wartość / przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft. HpcCompute | ciąg |
| typ | NvidiaGpuDriverWindows | ciąg |
| typeHandlerVersion | 1.2 | int |


## <a name="deployment"></a>Wdrożenie

### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager 

Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager. Szablony są idealne do wdrożenia co najmniej jednej maszyny wirtualnej, która wymaga konfiguracji po wdrożeniu.

Konfiguracji JSON dla rozszerzenia maszyny wirtualnej mogą być zagnieżdżone wewnątrz zasobu maszyny wirtualnej lub umieszczone w katalogu głównego lub najwyższego poziomu szablon JSON usługi Resource Manager. Położenie konfiguracji JSON ma wpływ na wartości nazwy i typu zasobu. Aby uzyskać więcej informacji, zobacz [Ustawianie nazwy i typu dla zasobów podrzędnych](../../azure-resource-manager/resource-manager-template-child-resource.md). 

W poniższym przykładzie przyjęto założenie, że rozszerzenie jest zagnieżdżone w ramach zasobu maszyny wirtualnej. Podczas zagnieżdżania zasobu rozszerzenia kod JSON jest umieszczany w obiekcie `"resources": []` maszyny wirtualnej.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>Program PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverWindows" `
    -ExtensionType "NvidiaGpuDriverWindows" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverWindows `
  --publisher Microsoft.HpcCompute `
  --version 1.2 `
  --settings '{ `
  }'
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomocy technicznej

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z Azure Portal i przy użyciu Azure PowerShell i interfejsu wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia dla danej maszyny wirtualnej, uruchom następujące polecenie.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Dane wyjściowe wykonania rozszerzenia są rejestrowane w następującym katalogu:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverMicrosoft\
```

### <a name="error-codes"></a>Kody błędów

| Kod błędu | Znaczenie | Możliwe działania |
| :---: | --- | --- |
| 0 | Operacja zakończona pomyślnie |
| 1 | Operacja powiodła się. Wymagany jest ponowny rozruch. |
| 100 | Operacja nie jest obsługiwana lub nie można jej ukończyć. | Możliwe przyczyny: nieobsługiwana wersja programu PowerShell, rozmiar maszyny wirtualnej nie jest maszyną wirtualną serii N, pobieranie danych nie powiodło się. Sprawdź pliki dziennika, aby ustalić przyczynę błędu. |
| 240, 840 | Limit czasu operacji. | Ponów próbę wykonania operacji. |
| -1 | Wystąpił wyjątek. | Sprawdź pliki dziennika, aby ustalić przyczynę wyjątku. |
| -pięciokrotną | Operacja została przerwana z powodu oczekującego ponownego uruchomienia. | Uruchom ponownie maszynę wirtualną. Instalacja będzie kontynuowana po ponownym uruchomieniu. Dezinstalacja powinna zostać wywołana ręcznie. |


### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/community/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [rozszerzenia i funkcje maszyny wirtualnej dla systemu Windows](features-windows.md).

Aby uzyskać więcej informacji o maszynach wirtualnych z serii N, zobacz [rozmiary maszyn wirtualnych zoptymalizowanych pod kątem procesora GPU](../windows/sizes-gpu.md).
