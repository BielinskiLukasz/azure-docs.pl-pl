---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie maszyny wirtualnej z systemem Linux i platformą WordPress | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie maszyny wirtualnej z systemem Linux i platformą WordPress
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 3e4410a5acb852ea9cd7f5ca24e67e8335a45e30
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692668"
---
# <a name="create-a-vm-with-wordpress"></a>Tworzenie maszyny wirtualnej z platformą WordPress

Ten skrypt tworzy maszynę wirtualną, a następnie używa rozszerzenia niestandardowego skryptu maszyny wirtualnej platformy Azure do zainstalowania systemu WordPress. Po uruchomieniu skryptu można uzyskać dostęp do witryny konfiguracyjnej platformy WordPress pod adresem `http://<public IP of VM>/wordpress`.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-wordpress-mysql/create-wordpress-mysql.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, maszyny wirtualnej i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Tworzy maszynę wirtualną i łączy ją z kartą sieciową, siecią wirtualną, podsiecią i sieciową grupą zabezpieczeń. To polecenie określa również obraz maszyny wirtualnej do użycia oraz poświadczenia administracyjne.  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/vm) | Tworzy regułę sieciowej grupy zabezpieczeń, aby zezwalać na ruch przychodzący. W tym przykładzie port 80 jest otwarty dla ruchu HTTP. |
| [az vm extension set](https://docs.microsoft.com/cli/azure/vm) | Dodaje do maszyny wirtualnej rozszerzenie niestandardowego skryptu, które wywołuje skrypt instalacji platformy WordPress. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
