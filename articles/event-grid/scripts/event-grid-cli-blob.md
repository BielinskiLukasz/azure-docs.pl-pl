---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — subskrybowanie do konta usługi Blob Storage | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — subskrybowanie do konta usługi Blob Storage
services: event-grid
documentationcenter: na
author: tfitzmac
ms.service: event-grid
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2018
ms.author: tomfitz
ms.openlocfilehash: aa920552e4fa90666e3554a01475c4da8bb82035
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2019
ms.locfileid: "55727607"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-azure-cli"></a>Subskrybowanie do zdarzeń dla konta usługi Blob Storage przy użyciu interfejsu wiersza polecenia platformy Azure

Ten skrypt tworzy subskrypcję usługi Event Grid na potrzeby zdarzeń związanych z kontem usługi Blob Storage. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.sh "Subscribe to Blob storage")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następującego polecenia w celu utworzenia subskrypcji zdarzeń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | Tworzy subskrypcję usługi Event Grid. |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) — wersja rozszerzenia | Tworzy subskrypcję usługi Event Grid. |

## <a name="next-steps"></a>Następne kroki

* Informacje na temat wykonywania zapytań dotyczących subskrypcji można znaleźć w artykule [Query Event Grid subscriptions (Wykonywanie zapytań dotyczących subskrypcji usługi Event Grid)](../query-event-subscriptions.md).
* Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).
