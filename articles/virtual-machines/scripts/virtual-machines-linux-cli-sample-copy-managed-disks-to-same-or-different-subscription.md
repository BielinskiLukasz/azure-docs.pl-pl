---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — kopiowanie (przenoszenie) dysków zarządzanych do tej samej lub innej subskrypcji | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — kopiowanie (przenoszenie) dysków zarządzanych do tej samej lub innej subskrypcji
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
ms.openlocfilehash: a514359edcf21d5882b2361d10c06214d8e39502
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2018
ms.locfileid: "52274870"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Kopiowanie dysków zarządzanych do tej samej lub innej subskrypcji za pomocą interfejsu wiersza polecenia

Ten skrypt kopiuje dysk zarządzany do tej samej lub innej subskrypcji w obrębie jednego regionu. Kopiowanie działa tylko wtedy, gdy subskrypcje należą do tej samej dzierżawy usługi AAD.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia nowego dysku zarządzanego w subskrypcji docelowej przy użyciu identyfikatora źródłowego dysku zarządzanego. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk#az_disk_show) | Pobiera wszystkie właściwości dysku zarządzanego przy użyciu nazwy i właściwości grupy zasobów dysku zarządzanego. Do kopiowania dysku zarządzanego do innej subskrypcji jest używana właściwość Id.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | Kopiuje dysk zarządzany przez utworzenie nowego dysku zarządzanego w innej subskrypcji przy użyciu identyfikatora i nazwy nadrzędnego dysku zarządzanego.  |

## <a name="next-steps"></a>Następne kroki

[Tworzenie maszyny wirtualnej na podstawie dysku zarządzanego](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dysków zarządzanych i maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
