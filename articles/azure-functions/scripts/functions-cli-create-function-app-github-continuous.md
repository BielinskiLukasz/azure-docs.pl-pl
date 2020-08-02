---
title: Tworzenie aplikacji funkcji przy użyciu wdrożenia GitHub — interfejs wiersza polecenia platformy Azure
description: Tworzenie aplikacji funkcji i wdrażanie kodu funkcji z repozytorium GitHub przy użyciu usługi Azure Functions.
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7f7c272fa95d2697d41cb751e39ef72b0da86a12
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498527"
---
# <a name="create-a-function-app-in-azure-that-is-deployed-from-github"></a>Tworzenie aplikacji funkcji wdrażanej z repozytorium GitHub na platformie Azure

Ten Azure Functions przykładowy skrypt tworzy aplikację funkcji przy użyciu [planu zużycia](../functions-scale.md#consumption-plan)wraz z powiązanymi zasobami. Konfiguruje również kod funkcji na potrzeby ciągłego wdrażania z repozytorium GitHub. 

Do pracy z tym przykładem potrzebne są:

* Repozytorium GitHub zawierające kod funkcji, w którym masz uprawnienia administracyjne.
* [Osobisty token dostępu](https://help.github.com/articles/creating-an-access-token-for-command-line-use) do konta w witrynie GitHub.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli wolisz użyć interfejsu wiersza polecenia platformy Azure lokalnie, zainstaluj i uruchom wersję 2.0 lub nowszą. Aby określić wersję interfejsu wiersza polecenia platformy Azure, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Przykładowy skrypt

Ten przykładowy skrypt umożliwia utworzenie aplikacji funkcji platformy Azure i wdrożenie kodu funkcji z repozytorium GitHub.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github-continuous/deploy-function-app-with-function-github-continuous.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Każde polecenie w tabeli stanowi link do dokumentacji polecenia. W tym skrypcie użyto następujących poleceń:

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Tworzy konto magazynu wymagane przez aplikację funkcji. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Tworzy aplikację funkcji w [planie zużycia](../functions-scale.md#consumption-plan) bezserwerowego i kojarzy ją z repozytorium Git lub Mercurial. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi Azure Functions można znaleźć w [dokumentacji usługi Azure Functions](../functions-cli-samples.md).
