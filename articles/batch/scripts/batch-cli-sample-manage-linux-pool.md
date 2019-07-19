---
title: Przykład skryptu interfejsu wiersza polecenia platformy Azure — pula systemu Linux w usłudze Batch | Microsoft Docs
description: Przykład skryptu interfejsu wiersza polecenia platformy Azure — tworzenie puli systemu Linux w usłudze Batch i zarządzanie nią
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: lahugh
ms.openlocfilehash: 13d519c1814cfa1e2038efd6c3828931e8332ec8
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322086"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>Przykład użycia interfejsu wiersza polecenia: tworzenie puli systemu Linux w usłudze Azure Batch i zarządzanie nią

Ten skrypt demonstruje niektóre polecenia dostępne w interfejsie wiersza polecenia platformy Azure służące do tworzenia puli węzłów obliczeniowych z systemem Linux w usłudze Azure Batch i zarządzania nią.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.20 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Linux Virtual Machine Pool")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Tworzy konto usługi Batch. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Przeprowadza uwierzytelnianie na określonym koncie usługi Batch na potrzeby dalszej interakcji z interfejsem wiersza polecenia.  |
| [az batch pool node-agent-skus list](https://docs.microsoft.com/cli/azure/batch/pool/node-agent-skus#az-batch-pool-node-agent-skus-list) | Zwraca listę dostępnych jednostek SKU agenta węzła oraz informacje o obrazie.  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-create) | Tworzy pulę węzłów obliczeniowych.  |
| [az batch pool resize](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-resize) | Zmienia rozmiar przez zmianę liczby maszyn wirtualnych uruchomionych w określonej puli.  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-show) | Wyświetla właściwości puli.  |
| [az batch node list](https://docs.microsoft.com/cli/azure/batch/node#az-batch-node-list) | Zwraca listę wszystkich węzłów obliczeniowych w określonej puli.  |
| [az batch node reboot](https://docs.microsoft.com/cli/azure/batch/node#az-batch-node-reboot) | Ponownie uruchamia określony węzeł obliczeniowy.  |
| [az batch node delete](https://docs.microsoft.com/cli/azure/batch/node#az-batch-node-delete) | Usuwa wymienione węzły z określonej puli.  |
| [az group delete](/cli/azure/group#az-group-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).
