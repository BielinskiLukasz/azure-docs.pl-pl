---
title: Skrypt interfejsu wiersza polecenia platformy Azure — skalowanie serwera usługi Azure Database for MariaDB
description: Ten przykładowy skrypt interfejsu wiersza polecenia służy do skalowania serwera usługi Azure Database for MariaDB na inny poziom wydajności po wykonaniu zapytania względem metryk.
services: mariadb
author: ajlam
ms.author: andrela
editor: jasonwhowell
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 11/28/2018
ms.openlocfilehash: e5f6364992987e5aeb6cb1ccd2055823efae607e
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585257"
---
# <a name="monitor-and-scale-an-azure-database-for-mariadb-server-using-azure-cli"></a>Monitorowanie i skalowanie serwera usługi Azure Database for MariaDB za pomocą interfejsu wiersza polecenia platformy Azure
Ten przykładowy skrypt interfejsu wiersza polecenia służy do skalowania pojedynczego serwera usługi Azure Database for MariaDB na inny poziom wydajności po wykonaniu zapytania względem metryk.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się uruchomić interfejs wiersza polecenia lokalnie, na potrzeby tego artykułu wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Sprawdź wersję, uruchamiając polecenie `az --version`. Aby zainstalować lub uaktualnić interfejs wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Przykładowy skrypt
W tym przykładowym skrypcie dokonaj edycji wyróżnionych wierszy w celu zmiany nazwy użytkownika i hasła administratora na swoje własne. Zastąp identyfikator subskrypcji używany w poleceniach `az monitor` własnym identyfikatorem subskrypcji. [!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/scale-mariadb-server.sh?highlight=15-16 "Create and scale Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po uruchomieniu skryptu użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu
Ten skrypt używa poleceń opisanych w poniższej tabeli:

| **Polecenie** | **Uwagi** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | Tworzy serwer MariaDB hostujący bazy danych. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Zwraca wartość metryki dla zasobów. |
| [az group delete](/cli/azure/group#az-group-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat interfejsu wiersza polecenia platformy Azure: [dokumentacja interfejsu wiersza polecenia platformy Azure](/cli/azure).
- Wypróbuj dodatkowe skrypty: [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Database for MariaDB](../sample-scripts-azure-cli.md)
- Aby uzyskać więcej informacji na temat skalowania, zobacz [Warstwy cenowe](../concepts-pricing-tiers.md.
