---
title: Konfiguruj parametry usługi w usłudze Azure Database for PostgreSQL
description: W tym artykule opisano jak skonfigurować parametry usługi w usłudze Azure Database for PostgreSQL przy użyciu wiersza polecenia wiersza polecenia platformy Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: c88518749129abed1cf43a70b9165035626a780f
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731567"
---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Dostosowywanie parametrów konfiguracji serwera przy użyciu wiersza polecenia platformy Azure
Można wyświetlać, Pokaż i zaktualizuj parametry konfiguracji dla serwera Azure PostgreSQL za pomocą interfejsu wiersza polecenia (Azure CLI). Podzbiór aparatu konfiguracji jest narażony na poziomie serwera i może być modyfikowany. 

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków w tym przewodniku, potrzebne są:
- Tworzenie usługi Azure Database for postgresql w warstwie serwera i bazy danych, postępując zgodnie z [Tworzenie serwera usługi Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Zainstaluj [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) interfejsu wiersza polecenia na maszynie lub użyj [usługi Azure Cloud Shell](../cloud-shell/overview.md) w witrynie Azure portal przy użyciu przeglądarki.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Lista parametrów konfiguracji serwera dla usługi Azure Database dla serwera PostgreSQL
Aby wyświetlić listę wszystkich parametrów można modyfikować w serwera i ich wartości, należy uruchomić [az postgres server configuration list](/cli/azure/postgres/server/configuration) polecenia.

Możesz wyświetlić listę parametrów konfiguracji serwera dla serwera **mydemoserver.postgres.database.azure.com** w ramach grupy zasobów **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Pokaż szczegóły parametrów konfiguracji serwera
Aby wyświetlić szczegółowe informacje dotyczące parametru określonej konfiguracji dla serwera, uruchom [az postgres server configuration show](/cli/azure/postgres/server/configuration) polecenia.

Ten przykład przedstawia szczegółowe informacje o **dziennika\_min\_wiadomości** parametru konfiguracji serwera dla serwera **mydemoserver.postgres.database.azure.com** w ramach grupy zasobów **myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Zmodyfikuj wartość parametru konfiguracji serwera
Można również zmodyfikować wartości parametru niektórych serwera konfiguracji, która aktualizuje podstawowej wartości konfiguracji dla aparatu serwera PostgreSQL. Aby zaktualizować konfigurację, użyj [az postgres server configuration set](/cli/azure/postgres/server/configuration) polecenia. 

Aby zaktualizować **dziennika\_min\_wiadomości** serwera parametru konfiguracji serwera **mydemoserver.postgres.database.azure.com** w ramach grupy zasobów  **myresourcegroup.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Jeśli chcesz zresetować wartość parametru konfiguracji, po prostu chcesz Opuść opcjonalnego `--value` parametr i usługa stosuje się wartością domyślną. W powyżej przykładzie to powiadomienie będzie wyglądać:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
To polecenie resetuje **dziennika\_min\_wiadomości** konfiguracji, wartość domyślna **ostrzeżenie**. Uzyskać więcej informacji na temat konfiguracji serwera i dopuszczalne wartości, zobacz dokumentację PostgreSQL [konfiguracji serwera](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Kolejne kroki
- Aby skonfigurować i dostęp do dzienników serwera, zobacz [dzienników serwera w usłudze Azure Database for PostgreSQL](concepts-server-logs.md)
