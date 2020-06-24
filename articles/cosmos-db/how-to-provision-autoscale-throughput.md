---
title: Obsługa przepływności automatycznego skalowania w Azure Cosmos DB
description: Dowiedz się, jak udostępnić przepływność automatycznego skalowania na poziomie kontenera i bazy danych w Azure Cosmos DB przy użyciu Azure Portal, interfejsu wiersza polecenia, programu PowerShell i różnych innych zestawów SDK.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/10/2020
ms.openlocfilehash: 59feabce099087edb011df471561229bfa88a289
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2020
ms.locfileid: "85118733"
---
# <a name="provision-autoscale-throughput-on-database-or-container-in-azure-cosmos-db"></a>Zastrzegaj przepływność skalowania automatycznego dla bazy danych lub kontenera w Azure Cosmos DB

W tym artykule wyjaśniono, jak zainicjować przepływność automatycznego skalowania dla bazy danych lub kontenera (kolekcji, grafu lub tabeli) w Azure Cosmos DB. Możesz włączyć funkcję automatycznego skalowania na jednym kontenerze lub udostępnić przepływność automatycznego skalowania w bazie danych i udostępniać ją między wszystkimi kontenerami w bazie danych. 

## <a name="azure-portal"></a>Azure Portal

### <a name="create-new-database-or-container-with-autoscale"></a>Tworzenie nowej bazy danych lub kontenera przy użyciu automatycznego skalowania
1. Zaloguj się do [Azure Portal](https://portal.azure.com) lub [Eksploratora Azure Cosmos DB.](https://cosmos.azure.com/)

1. Przejdź do konta Azure Cosmos DB i Otwórz kartę **Eksplorator danych** .

1. Wybierz pozycję **nowy kontener.** Wprowadź nazwę bazy danych, kontenera i klucza partycji. W obszarze **przepływność**wybierz opcję **automatycznego skalowania** i ustaw [maksymalną przepływność (ru/s)](provision-throughput-autoscale.md#how-autoscale-provisioned-throughput-works) , na którą ma zostać przeskalowana baza danych lub kontener.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/create-new-autoscale-container.png" alt-text="Tworzenie kontenera i Konfigurowanie przepływności aprowizacji automatycznego skalowania":::

1. Wybierz przycisk **OK**.

Aby zainicjować automatyczne skalowanie w udostępnionej bazie danych przepływności, wybierz opcję **zainicjuj przepływność bazy danych** podczas tworzenia nowej bazy danych. 

### <a name="enable-autoscale-on-existing-database-or-container"></a>Włącz automatyczne skalowanie dla istniejącej bazy danych lub kontenera

> [!IMPORTANT]
> W bieżącej wersji Azure Portal jest jedynym sposobem migracji między funkcją automatycznego skalowania i standardową (ręczną). 

1. Zaloguj się do [Azure Portal](https://portal.azure.com) lub [Eksploratora Azure Cosmos DB.](https://cosmos.azure.com/)

1. Przejdź do konta Azure Cosmos DB i Otwórz kartę **Eksplorator danych** .

1. Wybierz pozycję **Skala i ustawienia** dla swojego kontenera albo **Skaluj** do swojej bazy danych.

1. W obszarze **Skala**wybierz opcję **automatycznego skalowania** i **Zapisz**.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/autoscale-scale-and-settings.png" alt-text="Włączanie skalowania automatycznego na istniejącym kontenerze":::

> [!NOTE]
> Po włączeniu funkcji automatycznego skalowania w istniejącej bazie danych lub kontenera wartość początkowa dla Max RU/s jest określana przez system w oparciu o bieżące ręczne ustawienia przepływności i magazyn. Po zakończeniu operacji można zmienić w razie konieczności maksymalną wartość RU/s. [Dowiedz się więcej.](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work) 

## <a name="azure-cosmos-db-net-v3-sdk-for-sql-api"></a>Azure Cosmos DB .NET v3 SDK dla interfejsu API SQL
Aby zarządzać zasobami skalowania automatycznego, użyj [wersji 3,9 lub nowszej](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) zestawu SDK programu Azure Cosmos DB .NET dla interfejsu API SQL. 

> [!IMPORTANT]
> Zestaw SDK platformy .NET umożliwia tworzenie nowych zasobów skalowania automatycznego. Zestaw SDK nie obsługuje migrowania między skalowaniem automatycznym i standardowym (ręcznym). Scenariusz migracji jest obecnie obsługiwany tylko w Azure Portal. 

### <a name="create-database-with-shared-throughput"></a>Tworzenie bazy danych z udostępnioną przepływność
```csharp
// Create instance of CosmosClient
CosmosClient cosmosClient = new CosmosClient(Endpoint, PrimaryKey);
 
// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
database = await cosmosClient.CreateDatabaseAsync(DatabaseName, throughputProperties: autoscaleThroughputProperties);
```

### <a name="create-container-with-dedicated-throughput"></a>Tworzenie kontenera z dedykowaną przepływność
```csharp
// Get reference to database that container will be created in
Database database = await cosmosClient.GetDatabase("DatabaseName");

// Container and autoscale throughput settings
ContainerProperties autoscaleContainerProperties = new ContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
container = await database.CreateContainerAsync(autoscaleContainerProperties, autoscaleThroughputProperties);
```

### <a name="read-the-current-throughput-rus"></a>Odczytaj bieżącą przepływność (RU/s)
```csharp
// Get a reference to the resource
Container container = cosmosClient.GetDatabase("DatabaseName").GetContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = await container.ReadThroughputAsync(requestOptions: null); 

// The autoscale max throughput (RU/s) of the resource
int? autoscaleMaxThroughput = autoscaleContainerThroughput.AutoscaleMaxThroughput;

// The throughput (RU/s) the resource is currently scaled to
int? currentThroughput = autoscaleContainerThroughput.Throughput;
```

### <a name="change-the-autoscale-max-throughput-rus"></a>Zmiana maksymalnej przepływności skalowania automatycznego (RU/s)
```csharp
// Change the autoscale max throughput (RU/s)
await container.ReplaceThroughputAsync(ThroughputProperties.CreateAutoscaleThroughput(newAutoscaleMaxThroughput));
```

## <a name="azure-cosmos-db-java-v4-sdk-for-sql-api"></a>Interfejs API języka Azure Cosmos DB Java v4 SDK
Aby zarządzać zasobami skalowania automatycznego, można użyć [wersji 4,0 lub nowszej](https://mvnrepository.com/artifact/com.azure/azure-cosmos) Azure Cosmos DB Java SDK for SQL API. 

> [!IMPORTANT]
> Za pomocą zestawu SDK języka Java można tworzyć nowe zasoby skalowania automatycznego. Zestaw SDK nie obsługuje migrowania między skalowaniem automatycznym i standardowym (ręcznym). Scenariusz migracji jest obecnie obsługiwany tylko w Azure Portal. 

### <a name="create-database-with-shared-throughput"></a>Tworzenie bazy danych z udostępnioną przepływność

#### <a name="async"></a>[Asynchroniczne](#tab/api-async)

```java
// Create instance of CosmosClient
CosmosAsyncClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(MASTER)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildAsyncClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosAsyncDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).block().getDatabase();
```

#### <a name="sync"></a>[Synchronizuj](#tab/api-sync)

```java
// Create instance of CosmosClient
CosmosClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(MASTER)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).getDatabase();
```

--- 

### <a name="create-container-with-dedicated-throughput"></a>Tworzenie kontenera z dedykowaną przepływność

#### <a name="async"></a>[Asynchroniczne](#tab/api-async)

```java
// Get reference to database that container will be created in
CosmosAsyncDatabase database = client.createDatabase("DatabaseName").block().getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosAsyncContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .block()
                                .getContainer();
```

#### <a name="sync"></a>[Synchronizuj](#tab/api-sync)

```java
// Get reference to database that container will be created in
CosmosDatabase database = client.createDatabase("DatabaseName").getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .getContainer();
```

--- 

### <a name="read-the-current-throughput-rus"></a>Odczytaj bieżącą przepływność (RU/s)

#### <a name="async"></a>[Asynchroniczne](#tab/api-async)

```java
// Get a reference to the resource
CosmosAsyncContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().block().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

#### <a name="sync"></a>[Synchronizuj](#tab/api-sync)

```java
// Get a reference to the resource
CosmosContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

--- 

### <a name="change-the-autoscale-max-throughput-rus"></a>Zmiana maksymalnej przepływności skalowania automatycznego (RU/s)

#### <a name="async"></a>[Asynchroniczne](#tab/api-async)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput)).block();
```

#### <a name="sync"></a>[Synchronizuj](#tab/api-sync)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput));
```

--- 

## <a name="cassandra-api"></a>Interfejs API rozwiązania Cassandra 
Zapoznaj się z tym artykułem, [jak używać poleceń CQL](manage-scale-cassandra.md#use-autoscale) , aby włączyć automatyczne skalowanie.

## <a name="azure-cosmos-db-api-for-mongodb"></a>Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB 
Zapoznaj się z tym artykułem, [jak używać poleceń rozszerzenia MongoDB](mongodb-custom-commands.md) , aby włączyć automatyczne skalowanie.

## <a name="azure-resource-manager"></a>Azure Resource Manager
Możesz użyć szablonu Menedżer zasobów, aby zainicjować przepływność automatycznego skalowania dla bazy danych lub kontenera dla dowolnego interfejsu API. Zobacz ten [artykuł](manage-sql-with-resource-manager.md#azure-cosmos-account-with-autoscale-throughput) , aby zapoznać się z przykładem.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej na temat [korzyści z przedziału IT obsługiwanego przez automatyczne skalowanie](provision-throughput-autoscale.md#benefits-of-autoscale).
* Dowiedz się [, jak wybierać przepływność ręczną i skalowanie automatyczne](how-to-choose-offer.md).
* Przejrzyj [często zadawane pytania dotyczące skalowania automatycznego](autoscale-faq.md).
