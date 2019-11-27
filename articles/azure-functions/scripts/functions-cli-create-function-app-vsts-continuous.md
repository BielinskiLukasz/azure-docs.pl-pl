---
title: Tworzenie aplikacji funkcji przy użyciu wdrożenia DevOps — interfejs wiersza polecenia platformy Azure
description: Tworzenie aplikacji funkcji i wdrażanie kodu funkcji z usługi Azure DevOps
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc
ms.openlocfilehash: 3fa11d5cd81d93b89b6e8ae63fd491842be78633
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74532801"
---
# <a name="create-a-function-in-azure-that-is-deployed-from-azure-devops"></a>Tworzenie funkcji na platformie Azure wdrożonej z usługi Azure DevOps

W tym temacie przedstawiono instrukcje tworzenia [bezserwerowej](https://azure.microsoft.com/solutions/serverless/) aplikacji funkcji korzystającej z [planu Zużycie](../functions-scale.md#consumption-plan) w usłudze Azure Functions. Aplikacja funkcji, która jest kontenerem dla funkcji, jest ciągle wdrażana z repozytorium usługi Azure DevOps. 

Do wykonania instrukcji w tym temacie potrzebne są:

* Repozytorium usługi Azure DevOps zawierające projekt aplikacji funkcji, w którym masz uprawnienia administracyjne.
* [Osobisty token dostępu](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) do repozytorium usługi Azure DevOps.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli wolisz użyć interfejsu wiersza polecenia platformy Azure lokalnie, zainstaluj i uruchom wersję 2.0 lub nowszą. Aby określić wersję interfejsu wiersza polecenia platformy Azure, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Przykładowy skrypt

Ten przykładowy skrypt umożliwia utworzenie aplikacji funkcji platformy Azure i wdrożenie kodu funkcji z usługi Azure DevOps.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia, służące do utworzenia grupy zasobów, konta magazynu, aplikacji funkcji i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Tworzy konto magazynu wymagane przez aplikację funkcji. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Tworzy aplikację funkcji w bezserwerowym [planie Zużycie](../functions-scale.md#consumption-plan). |
| [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | Kojarzy aplikację funkcji z repozytorium Git lub Mercurial. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi Azure Functions można znaleźć w [dokumentacji usługi Azure Functions](../functions-cli-samples.md).
