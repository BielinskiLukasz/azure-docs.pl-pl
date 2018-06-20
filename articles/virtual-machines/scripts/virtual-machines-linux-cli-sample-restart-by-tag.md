---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — ponowne uruchamianie maszyn wirtualnych | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — ponowne uruchamianie maszyn wirtualnych
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: 4c409dfcfba14093d5e2cf28df4bb6c5d1bd9533
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34725976"
---
# <a name="restart-vms"></a>Ponowne uruchamianie maszyn wirtualnych

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

W tym przykładzie pokazano kilka sposobów pobierania niektórych maszyn wirtualnych i ich ponownego uruchamiania.

Pierwszy sposób powoduje ponowne uruchomienie wszystkich maszyn wirtualnych w grupie zasobów.

```bash
az vm restart --ids $(az vm list --resource-group myResourceGroup --query "[].id" -o tsv)
```

Drugi sposób powoduje pobranie oznakowanych maszyn wirtualnych przy użyciu polecenia `az resouce list` i filtrów do zasobów będących maszynami wirtualnymi oraz ponowne uruchomienie tych maszyn wirtualnych.

```bash
az vm restart --ids $(az resource list --tag "restart-tag" --query "[?type=='Microsoft.Compute/virtualMachines'].id" -o tsv)
```

Ten przykład działa w powłoce Bash. Aby uzyskać informacje na temat opcji uruchamiania skryptów interfejsu wiersza polecenia platformy Azure w ramach klienta systemu Windows, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure w systemie Windows](/cli/azure/install-azure-cli-windows).


## <a name="sample-script"></a>Przykładowy skrypt

Przykład obejmuje trzy skrypty.
Pierwszy z nich aprowizuje maszyny wirtualne.
Używa on opcji no-wait, dlatego polecenie jest zwracane bez oczekiwania na aprowizowanie poszczególnych maszyn wirtualnych.
Drugi czeka na pełne aprowizowanie maszyn wirtualnych.
Trzeci skrypt ponownie uruchamia wszystkie aprowizowane maszyny wirtualne, a następnie tylko otagowane maszyny wirtualne.

### <a name="provision-the-vms"></a>Aprowizowanie maszyn wirtualnych

Ten skrypt tworzy grupę zasobów, a następnie tworzy trzy maszyny wirtualne do ponownego uruchomienia.
Dwie z nich są otagowane.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/provision.sh "Provision the VMs")]

### <a name="wait"></a>Oczekiwanie

Ten skrypt sprawdza stan aprowizowania co 20 sekund, aż wszystkie trzy maszyny wirtualne zostaną aprowizowane lub aprowizacja jednej z nich zakończy się niepowodzeniem.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/wait.sh "Wait for the VMs to be provisioned")]

### <a name="restart-the-vms"></a>Ponowne uruchamianie maszyn wirtualnych

Ten skrypt ponownie uruchamia wszystkie maszyny wirtualne w grupie zasobów, a następnie ponownie uruchamia tylko otagowane maszyny wirtualne.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/restart.sh "Restart VMs by tag")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupy zasobów, maszyny wirtualne i wszystkie powiązane zasoby.

```azurecli-interactive 
az group delete -n myResourceGroup --no-wait --yes
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, maszyny wirtualnej, zestawu dostępności, modułu równoważenia obciążenia i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | Tworzy maszyny wirtualne.  |
| [az vm list](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Używane z opcją `--query` w celu upewnienia się, że maszyny wirtualne zostaną aprowizowane przed ponownym uruchomieniem, i pobrania identyfikatorów maszyn wirtualnych na potrzeby ich ponownego uruchomiania. |
| [az resource list](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Używane z opcją `--query` w celu pobrania identyfikatorów maszyn wirtualnych za pomocą tagu. |
| [az vm restart](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Ponownie uruchamia maszyny wirtualne. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
