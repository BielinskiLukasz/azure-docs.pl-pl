---
title: Znajdź opłatę jednostkową żądania (RU) w Azure Cosmos DB
description: Dowiedz się, jak znaleźć opłatę jednostki żądania (RU) dla każdej operacji wykonywanej względem kontenera usługi Azure Cosmos.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: thweiss
ms.openlocfilehash: 86e9854cb5a522a56cca09a6e1ea155fd8a62f14
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585904"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>Znajdź koszt jednostkowy żądania w Azure Cosmos DB

W tym artykule przedstawiono różne sposoby znajdowania zużycia [jednostek żądań](request-units.md) (ru) dla każdej operacji wykonywanej względem kontenera w Azure Cosmos DB. Obecnie można mierzyć to użycie tylko przy użyciu Azure Portal lub przez sprawdzenie odpowiedzi wysyłanej z Azure Cosmos DB za pomocą jednego z zestawów SDK.

## <a name="sql-core-api"></a>Interfejs API SQL (podstawowy)

Jeśli używasz interfejsu API SQL, masz wiele opcji znajdowania zużycia RU dla operacji w odniesieniu do kontenera usługi Azure Cosmos.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Obecnie opłaty za żądanie można znaleźć w Azure Portal tylko dla zapytania SQL.

1. Zaloguj się do [Azure portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-sql-api-dotnet.md#create-account) i podawanie danych z danymi lub Wybierz istniejące konto usługi Azure Cosmos, które zawiera już dane.

1. Przejdź do okienka **Eksplorator danych** , a następnie wybierz kontener, w którym chcesz rozpocząć pracę.

1. Wybierz pozycję **nowe zapytanie SQL**.

1. Wprowadź prawidłowe zapytanie, a następnie wybierz pozycję **wykonaj zapytanie**.

1. Wybierz pozycję **Statystyka zapytania** , aby wyświetlić rzeczywistą opłatę za żądanie dla wykonywanego żądania.

![Zrzut ekranu przedstawiający opłaty za żądanie zapytania SQL w Azure Portal](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk"></a>Korzystanie z zestawu SDK dla platformy .NET
### <a name="net-v2-sdk"></a>Zestaw .NET V2 SDK

Obiekty zwracane z [zestawu .NET SDK V2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) uwidaczniają Właściwość `RequestCharge`:

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

### <a name="net-v3-sdk"></a>Zestaw SDK dla platformy .NET v3

Obiekty zwracane z [zestawu .NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) uwidaczniają `RequestCharge` Właściwość:

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs" id="GetRequestCharge":::

Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie aplikacji sieci Web platformy .NET przy użyciu konta interfejsu API SQL w Azure Cosmos DB](create-sql-api-dotnet.md).

### <a name="use-the-java-sdk"></a>Korzystanie z zestawu SDK języka Java

Obiekty, które są zwracane z [zestawu Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) uwidaczniają metodę `getRequestCharge()`:

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie aplikacji Java przy użyciu konta interfejsu API SQL Azure Cosmos DB](create-sql-api-java.md).

### <a name="use-the-nodejs-sdk"></a>Korzystanie z zestawu SDK środowiska Node. js

Obiekty, które są zwracane z [zestawu Node. js SDK](https://www.npmjs.com/package/@azure/cosmos) uwidaczniają `headers` podobiektem, który mapuje wszystkie nagłówki zwrócone przez podstawowy interfejs API protokołu HTTP. Opłata za żądanie jest dostępna w kluczu `x-ms-request-charge`:

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie aplikacji node. js przy użyciu Azure Cosmos DB konta interfejsu API SQL](create-sql-api-nodejs.md). 

### <a name="use-the-python-sdk"></a>Korzystanie z zestawu SDK języka Python

Obiekt `CosmosClient` z [zestawu SDK języka Python](https://pypi.org/project/azure-cosmos/) uwidacznia słownik `last_response_headers`, który mapuje wszystkie nagłówki zwrócone przez podstawowy interfejs API protokołu HTTP dla ostatniej wykonanej operacji. Opłata za żądanie jest dostępna w kluczu `x-ms-request-charge`:

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie aplikacji w języku Python przy użyciu konta interfejsu API SQL Azure Cosmos DB](create-sql-api-python.md). 

## <a name="azure-cosmos-db-api-for-mongodb"></a>Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB

Opłata za RU jest udostępniana przez niestandardowe [polecenie bazy danych](https://docs.mongodb.com/manual/reference/command/) o nazwie `getLastRequestStatistics`. Polecenie zwraca dokument zawierający nazwę wykonanej ostatniej operacji, jej koszt żądania i czas trwania. Jeśli używasz interfejsu API Azure Cosmos DB dla MongoDB, masz wiele opcji pobierania opłaty za RU.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Obecnie opłaty za żądanie można znaleźć w Azure Portal tylko dla zapytania.

1. Zaloguj się do [Azure portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) i podawanie danych z danymi lub Wybierz istniejące konto, które zawiera już dane.

1. Przejdź do okienka **Eksplorator danych** , a następnie wybierz kontener, w którym chcesz rozpocząć pracę.

1. Wybierz pozycję **Nowe zapytanie**.

1. Wprowadź prawidłowe zapytanie, a następnie wybierz pozycję **wykonaj zapytanie**.

1. Wybierz pozycję **Statystyka zapytania** , aby wyświetlić rzeczywistą opłatę za żądanie dla wykonywanego żądania.

![Zrzut ekranu przedstawiający opłaty za żądanie zapytania MongoDB w Azure Portal](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>Korzystanie ze sterownika MongoDB .NET

W przypadku korzystania z [oficjalnego sterownika MongoDB .NET](https://docs.mongodb.com/ecosystem/drivers/csharp/)można wykonać polecenia, wywołując metodę `RunCommand` na obiekcie `IMongoDatabase`. Ta metoda wymaga implementacji klasy abstrakcyjnej `Command<>`:

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie aplikacji sieci Web platformy .NET przy użyciu interfejsu API Azure Cosmos DB dla MongoDB](create-mongodb-dotnet.md).

### <a name="use-the-mongodb-java-driver"></a>Korzystanie ze sterownika Java MongoDB


W przypadku korzystania z [oficjalnego sterownika Java MongoDB](https://mongodb.github.io/mongo-java-driver/)można wykonać polecenia, wywołując metodę `runCommand` na obiekcie `MongoDatabase`:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie aplikacji sieci Web za pomocą interfejsu API Azure Cosmos DB dla MongoDB i zestawu Java SDK](create-mongodb-java.md).

### <a name="use-the-mongodb-nodejs-driver"></a>Korzystanie ze sterownika MongoDB Node. js

W przypadku korzystania z [oficjalnego sterownika Node. js MongoDB](https://mongodb.github.io/node-mongodb-native/)można wykonać polecenia, wywołując metodę `command` na obiekcie `db`:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Aby uzyskać więcej informacji, zobacz [Szybki Start: Migrowanie istniejącej aplikacji sieci Web Node. js MongoDB do Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="cassandra-api"></a>Interfejs API rozwiązania Cassandra

Gdy wykonujesz operacje dotyczące interfejs API Cassandra Azure Cosmos DB, opłata RU jest zwracana w ładunku przychodzącym jako pole o nazwie `RequestCharge`. Istnieje wiele opcji pobierania opłaty za jednostkę żądania.

### <a name="use-the-net-sdk"></a>Korzystanie z zestawu SDK dla platformy .NET

W przypadku korzystania z [zestawu SDK platformy .NET](https://www.nuget.org/packages/CassandraCSharpDriver/)można pobrać ładunek przychodzący poniżej właściwości `Info` obiektu `RowSet`:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie aplikacji Cassandra przy użyciu zestawu .NET SDK i Azure Cosmos DB](create-cassandra-dotnet.md).

### <a name="use-the-java-sdk"></a>Korzystanie z zestawu SDK języka Java

W przypadku korzystania z [zestawu SDK języka Java](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core)można pobrać ładunek przychodzący, wywołując metodę `getExecutionInfo()` na obiekcie `ResultSet`:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie aplikacji Cassandra przy użyciu zestawu Java SDK i Azure Cosmos DB](create-cassandra-java.md).

## <a name="gremlin-api"></a>Interfejs API języka Gremlin

Korzystając z interfejsu API Gremlin, dostępnych jest wiele opcji znajdowania zużycia RU dla operacji w odniesieniu do kontenera usługi Azure Cosmos. 

### <a name="use-drivers-and-sdk"></a>Korzystanie z sterowników i zestawu SDK

Nagłówki zwracane przez interfejs API Gremlin są mapowane na niestandardowe atrybuty stanu, które obecnie są przedstawiane przez Gremlin .NET i zestaw Java SDK. Opłata za żądanie jest dostępna pod kluczem `x-ms-request-charge`.

### <a name="use-the-net-sdk"></a>Korzystanie z zestawu SDK dla platformy .NET

Gdy używasz [zestawu SDK Gremlin.NET](https://www.nuget.org/packages/Gremlin.Net/), atrybuty stanu są dostępne pod właściwością `StatusAttributes` obiektu `ResultSet<>`:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie aplikacji .NET Framework lub Core przy użyciu konta interfejsu API Azure Cosmos DB Gremlin](create-graph-dotnet.md).

### <a name="use-the-java-sdk"></a>Korzystanie z zestawu SDK języka Java

W przypadku używania [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver)można pobrać atrybuty stanu, wywołując metodę `statusAttributes()` na obiekcie `ResultSet`:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie bazy danych grafu w Azure Cosmos dB przy użyciu zestawu Java SDK](create-graph-java.md).

## <a name="table-api"></a>Interfejs API tabel

Obecnie jedynym zestawem SDK, który zwraca opłatę RU dla operacji w tabeli, jest [zestaw .NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). Obiekt `TableResult` uwidacznia Właściwość `RequestCharge`, która jest wypełniana przez zestaw SDK, gdy jest używana w odniesieniu do Azure Cosmos DB interfejs API tabel:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie aplikacji interfejs API tabel przy użyciu zestawu .NET SDK i Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat optymalizowania użycia RU, zobacz następujące artykuły:

* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)
* [Optymalizacja zaaprowizowanej przepływności w usłudze Azure Cosmos DB](optimize-cost-throughput.md)
* [Optymalizacja kosztów zapytania w usłudze Azure Cosmos DB](optimize-cost-queries.md)
* [Globalnie skalowanie przepływności aprowizacji](scaling-throughput.md)
* [Aprowizacja przepływności kontenerów i baz danych](set-throughput.md)
* [Obsługa przepływności dla kontenera](how-to-provision-container-throughput.md)
* [Monitoruj i Debuguj przy użyciu metryk w Azure Cosmos DB](use-metrics.md)
