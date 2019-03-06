---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — eksportowanie z magazynu usługi Azure App Configuration | Microsoft Docs
description: Zawiera informacje i przykładowe skrypty eksportowania z magazynu usługi Azure App Configuration
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 3310dc5d72284e8d94b95b855fee90d560205fa4
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884391"
---
# <a name="export-from-an-azure-app-configuration-store"></a>Eksportowanie z magazynu usługi Azure App Configuration

Ten przykładowy skrypt eksportuje pary klucz-wartość z magazynu usługi Azure App Configuration.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Należy najpierw zainstalować rozszerzenie interfejsu wiersza polecenia usługi Azure App Configuration, wykonując następujące polecenie:

        az extension add -n appconfig

## <a name="sample-script"></a>Przykładowy skrypt

```azurecli-interactive
#!/bin/bash

# Export all key-values
az appconfig kv export --name myTestAppConfigStore --file ~/Export.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa poniższych poleceń do eksportowania magazynu konfiguracji aplikacji. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az appconfig export](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-export) | Eksportuje z zasobu magazynu konfiguracji aplikacji. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia usługi App Configuration można znaleźć w [dokumentacji usługi Azure App Configuration](../cli-samples.md).
