---
title: Przykład skryptu interfejsu wiersza polecenia platformy Azure — tworzenie konta usługi Batch — usługa Batch | Microsoft Docs
description: Przykład skryptu interfejsu wiersza polecenia platformy Azure — tworzenie konta usługi Batch w trybie usługi Batch
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: danlep
ms.openlocfilehash: d1c3d892e79138e75d93ae024460c3d8394029f8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980204"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>Przykład interfejsu wiersza polecenia: tworzenie konta usługi Batch w trybie usługi Batch

Ten skrypt tworzy konto usługi Azure Batch w trybie usługi Batch oraz przedstawia sposób wykonywania zapytań o różne właściwości konta i aktualizowania ich. Podczas tworzenia konta usługi Batch w domyślnym trybie usługi Batch jego węzły obliczeniowe są przypisywane wewnętrznie przez usługę Batch. Do przydzielonych węzłów obliczeniowych ma zastosowanie odrębny limit przydziału procesorów wirtualnych (rdzeni), a konto można uwierzytelniać za pośrednictwem poświadczeń klucza wspólnego lub tokenu usługi Azure Active Directory.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.20 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

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
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Tworzy konto magazynu. |
| [az batch account set](/cli/azure/batch/account#az-batch-account-set) | Aktualizuje właściwości konta usługi Batch.  |
| [az batch account show](/cli/azure/batch/account#az-batch-account-show) | Pobiera szczegóły określonego konta usługi Batch.  |
| [az batch account keys list](/cli/azure/batch/account/keys#az-batch-account-keys-list) | Pobiera klucze dostępu określonego konta usługi Batch.  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Przeprowadza uwierzytelnianie na określonym koncie usługi Batch na potrzeby dalszej interakcji z interfejsem wiersza polecenia.  |
| [az group delete](/cli/azure/group#az-group-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).
