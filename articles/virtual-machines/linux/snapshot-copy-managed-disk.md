---
title: Tworzenie migawki dysku VHD na platformie Azure
description: Dowiedz się, jak utworzyć kopię dysku twardego vhd na platformie Azure w celu utworzenia kopii zapasowej lub rozwiązywania problemów.
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 15696469ca3861586617e9f418f8a55a7ea90467
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034797"
---
# <a name="create-a-snapshot"></a>Tworzenie migawki 

Wykonaj migawkę systemu operacyjnego lub dysku danych w celu tworzenia kopii zapasowej lub rozwiązywania problemów z maszyną wirtualną. Migawka jest pełną, tylko do odczytu kopię dysku VHD. 

## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure 

Poniższy przykład wymaga użycia [usługi Cloud Shell](https://shell.azure.com/bash) lub zainstalowania interfejsu wiersza polecenia platformy Azure.

Poniższe kroki pokazują, jak wykonać migawkę przy użyciu **polecenia tworzenia migawki az** z parametrem **--source-disk.** W poniższym przykładzie przyjęto założenie, że w grupie zasobów *myResourceGroup* znajduje się maszyna wirtualna o nazwie *myVM.*

Pobierz identyfikator dysku za pomocą [programu az vm show](/cli/azure/vm#az-vm-show).

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Zrób migawkę o nazwie *osDisk-backup* przy użyciu [migawki az create](/cli/azure/snapshot#az-snapshot-create).

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Jeśli chcesz przechowywać migawkę w magazynie odpornym na strefy, musisz utworzyć ją w regionie, który obsługuje [strefy dostępności](../../availability-zones/az-overview.md) i zawiera parametr **--sku Standard_ZRS.**

Możesz zobaczyć listę migawek przy użyciu [listy migawek az](/cli/azure/snapshot#az-snapshot-list).

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Zaczynając od lewej górnej części kliknij pozycję **Utwórz zasób** i wyszukaj **migawkę**. Z wyników wyszukiwania wybierz **pozycję Migawka.**
3. W bloku **Migawka** kliknij pozycję **Utwórz**.
4. Wprowadź **nazwę** migawki.
5. Wybierz istniejącą grupę zasobów lub wpisz nazwę nowej. 
7. W obszarze **Dysk źródłowy**wybierz dysk zarządzany do migawki.
8. Wybierz **typ konta,** który ma być używany do przechowywania migawki. Użyj **standardowego dysku twardego,** chyba że jest on przechowywany na wydajnym dysku SSD.
9. Kliknij przycisk **Utwórz**.


## <a name="next-steps"></a>Następne kroki

 Utwórz maszynę wirtualną na podstawie migawki, tworząc dysk zarządzany z migawki, a następnie dołączając nowy dysk zarządzany jako dysk systemu operacyjnego. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny Wirtualnej ze skryptu migawki.](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json)

