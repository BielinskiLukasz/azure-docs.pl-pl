---
title: 'Interfejs wiersza polecenia platformy Azure: Przenoszenie bazy danych między elastycznymi pulami'
description: Użyj przykładowego skryptu interfejsu wiersza polecenia platformy Azure, aby utworzyć dwie elastyczne pule i przenieść bazę danych w SQL Database z jednej elastycznej puli do innej.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 7da5e25624a0e3a323e60cdf91660b29f58f31d7
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86514751"
---
# <a name="use-the-azure-cli-to-move-a-database-in-sql-database-in-a-sql-elastic-pool"></a>Używanie interfejsu wiersza polecenia platformy Azure do przenoszenia bazy danych w SQL Database w elastycznej puli SQL

Ten przykładowy skrypt interfejsu wiersza polecenia platformy Azure tworzy dwie pule elastyczne, przenosi bazę danych w puli w SQL Database z jednej elastycznej puli SQL do innej elastycznej puli SQL, a następnie przenosi bazę danych w puli poza pulę elastyczną SQL na jedną bazę danych w SQL Database.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Uruchamianie skryptu

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "Move database between pools")]

### <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj poniższego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Przykładowe odwołanie

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Opis |
|---|---|
| [az sql server](/cli/azure/sql/server) | Polecenia serwera. |
| [AZ SQL Elastic-Pools](/cli/azure/sql/elastic-pool) | Polecenia puli elastycznej. |
| [AZ SQL DB](/cli/azure/sql/db) | Polecenia bazy danych. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi SQL Database można znaleźć w [dokumentacji usługi Azure SQL Database](../az-cli-script-samples-content-guide.md).
