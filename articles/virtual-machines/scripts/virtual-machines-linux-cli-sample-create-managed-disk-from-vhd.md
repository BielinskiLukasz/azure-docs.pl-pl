---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie dysku zarządzanego na podstawie pliku VHD na koncie magazynu w ramach tej samej subskrypcji | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie dysku zarządzanego na podstawie pliku VHD na koncie magazynu w ramach tej samej subskrypcji
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 15a5e3e566b03f29f33c3f21aa6581a03b0abe2e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29851011"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli"></a>Tworzenie dysku zarządzanego na podstawie pliku VHD na koncie magazynu w ramach tej samej subskrypcji przy użyciu interfejsu wiersza polecenia

Ten skrypt tworzy dysk zarządzany na podstawie pliku VHD na koncie magazynu w ramach tej samej subskrypcji. Użyj tego skryptu, aby importować specjalny (nieuogólniony/przygotowany przez program Sysprep) dysk VHD do zarządzanego dysku systemu operacyjnego w celu utworzenia maszyny wirtualnej. Pozwala on również na importowanie dysku VHD danych do zarządzanego dysku danych. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa poniższych poleceń w celu utworzenia dysku zarządzanego na podstawie dysku VHD. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az disk create](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | Tworzy dysk zarządzany przy użyciu identyfikatora URI dysku VHD na koncie magazynu w ramach tej samej subskrypcji |

## <a name="next-steps"></a>Następne kroki

[Tworzenie maszyny wirtualnej przez dołączenie dysku zarządzanego jako dysku systemu operacyjnego](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dysków zarządzanych i maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
