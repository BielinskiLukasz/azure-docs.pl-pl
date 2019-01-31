---
title: Zarządzanie zasobami usługi Azure Cosmos DB przy użyciu wiersza polecenia platformy Azure
description: Użyj wiersza polecenia platformy Azure do zarządzania kontem usługi Azure Cosmos DB, bazy danych i kontenerów.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: c3028fd18bd9afefaa18f7f515a43a852ddef78a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464403"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Zarządzanie zasobami usługi Azure Cosmos przy użyciu wiersza polecenia platformy Azure

Następujący przewodnik zawiera opis poleceń do automatyzowania zarządzania kontami usługi Azure Cosmos DB, bazy danych i kontenerów za pomocą narzędzia wiersza polecenia platformy Azure. Zawiera on również polecenia do skalowania przepływności kontenerów. Strony referencyjne dla wszystkich poleceń dostępnych w interfejsie wiersza polecenia usługi Azure Cosmos DB są dostępne w [dokumentacji dotyczącej interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/cosmosdb). Można także znaleźć więcej przykładów w [przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Cosmos DB](cli-samples.md), w tym jak utworzyć i zarządzać kont usługi Cosmos DB, bazy danych i kontenerów dla bazy danych MongoDB, Gremlin, Cassandra i interfejsu API tabel.

Ten przykładowy skrypt interfejsu wiersza polecenia tworzy konto, bazę danych i kontener interfejsu API SQL usługi Azure Cosmos DB.  

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

Aby utworzyć konto usługi Azure Cosmos DB z interfejsem API SQL, spójność sesji, wielu wzorzec włączone w regionie wschodnie stany USA i zachodnie stany USA, otworzyć wiersza polecenia platformy Azure lub cloud shell i uruchom następujące polecenie:

```azurecli-interactive
az cosmosdb create \
   –-name "myCosmosDbAccount" \
   --resource-group "myResourceGroup" \
   --kind GlobalDocumentDB \
   --default-consistency-level "Session" \
   --locations "EastUS=0" "WestUS=1" \
   --enable-multiple-write-locations true \
```

## <a name="create-a-database"></a>Tworzenie bazy danych

Aby utworzyć bazę danych Cosmos DB, otworzyć wiersza polecenia platformy Azure lub cloud shell i uruchom następujące polecenie:

```azurecli-interactive
az cosmosdb database create \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup"
```

## <a name="create-a-container"></a>Tworzenie kontenera

Aby utworzyć kontener usługi Cosmos DB przy użyciu jednostek RU/s, 1000 i klucz partycji, otworzyć wiersza polecenia platformy Azure lub cloud shell i uruchom następujące polecenie:

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --partition-key-path = "/myPartitionKey" \
   --throughput 1000
```

## <a name="change-the-throughput-of-a-container"></a>Zmień przepływność kontenera

Aby zmienić przepływność kontenera usługi Cosmos DB jednostek RU/s, 400, otworzyć wiersza polecenia platformy Azure lub cloud shell i uruchom następujące polecenie:

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --throughput 400
```

## <a name="list-account-keys"></a>Wyświetl listę kluczy konta

Podczas tworzenia konta usługi Azure Cosmos DB, Usługa generuje dwa klucze wzorca dostępu, których można użyć do uwierzytelniania podczas uzyskiwania dostępu do konta usługi Azure Cosmos DB. Zapewniając dwa klucze dostępu, usługi Azure Cosmos DB pozwala na ponowne generowanie kluczy nie zakłóceń z kontem usługi Azure Cosmos DB. Dostępne są również klucze tylko do odczytu w celu uwierzytelniania operacji tylko do odczytu. Istnieją dwa klucze odczytu i zapisu (podstawowych i pomocniczych) i dwa klucze tylko do odczytu (podstawowych i pomocniczych). Klucze dla swojego konta można uzyskać, uruchamiając następujące polecenie:

```azurecli-interactive
# List account keys
az cosmosdb list-keys \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="list-connection-strings"></a>Lista parametrów połączenia

Parametry połączenia do łączenia aplikacji z konta usługi Cosmos DB można pobrać przy użyciu następującego polecenia.

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="regenerate-account-key"></a>Ponowne generowanie klucza konta

Należy zmieniać klucze dostępu do swojego konta usługi Azure Cosmos DB, okresowo, aby zabezpieczyć połączenia. Dwa klucze dostępu są przypisywane umożliwia utrzymanie połączeń do konta usługi Azure Cosmos DB przy użyciu jednego klucza dostępu, jednocześnie ponownie generując drugi klucz dostępu.

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup" \
   --key-kind primary
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure zobacz:

- [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
- [Odwołanie do wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Więcej przykładów interfejsu wiersza polecenia platformy Azure dla usługi Azure Cosmos DB](cli-samples.md)
