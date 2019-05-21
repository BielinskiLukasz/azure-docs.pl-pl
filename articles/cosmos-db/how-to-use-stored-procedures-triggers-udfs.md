---
title: Jak wywoływać procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika przy użyciu zestawów SDK usługi Azure Cosmos DB
description: Dowiedz się, jak rejestrować i wywoływać procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika przy użyciu zestawów SDK usługi Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/21/2019
ms.author: mjbrown
ms.openlocfilehash: b25d65b07420c5be53d952c6f81de5116dc9ec7d
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978928"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Jak rejestrować procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika oraz jak ich używać w usłudze Azure Cosmos DB

Interfejs API SQL w usłudze Azure Cosmos DB obsługuje rejestrowanie i wywoływanie procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika (UDF) napisanych w języku JavaScript. Można użyć interfejsu API SQL [.NET](sql-api-sdk-dotnet.md), [platformy .NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript](sql-api-sdk-node.md), [Node.js](sql-api-sdk-node.md), lub [Python](sql-api-sdk-python.md) zestawy SDK służące do rejestrowania i wywoływać procedur składowanych. Po zdefiniowaniu kilku procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika można je załadować i wyświetlić w witrynie [Azure Portal](https://portal.azure.com/) za pomocą Eksploratora danych.

## <a id="stored-procedures"></a>Jak uruchamiać procedury składowane

Procedury składowane pisze się przy użyciu języka JavaScript. Mogą one tworzyć, aktualizować, odczytywać i usuwać elementy oraz wysyłać o nie zapytania w kontenerze usługi Azure Cosmos. Aby uzyskać więcej informacji na temat sposobu pisania procedur składowanych w usłudze Azure Cosmos DB, zobacz [sposób pisania procedur składowanych w usłudze Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures) artykułu.

Poniższe przykłady pokazują, jak zarejestrować i wywoływać procedurę składowaną przy użyciu zestawów SDK usługi Azure Cosmos DB. Zapoznaj się z tematem [Tworzenie dokumentu](how-to-write-stored-procedures-triggers-udfs.md#create-an-item), ponieważ źródło tej procedury składowanej jest zapisane jako `spCreateToDoItem.js`.

> [!NOTE]
> W przypadku kontenerów podzielonych na partycje podczas wykonywania procedury składowanej w opcjach żądania należy podać wartość klucza partycji. Procedury składowane są zawsze ograniczone do klucza partycji. Elementy, które mają inną wartość klucza partycji, nie będą widoczne dla procedury składowanej. Ma to również zastosowanie do wyzwalaczy.

### <a name="stored-procedures---net-sdk"></a>Procedury składowane — zestaw SDK platformy .NET

Poniższy przykład pokazuje, jak zarejestrować procedurę składowaną przy użyciu zestawu SDK platformy .NET:

```csharp
string storedProcedureId = "spCreateToDoItem";
StoredProcedure newStoredProcedure = new StoredProcedure
   {
       Id = storedProcedureId,
       Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
   };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var response = await client.CreateStoredProcedureAsync(containerUri, newStoredProcedure);
StoredProcedure createdStoredProcedure = response.Resource;
```

Poniższy kod pokazuje, jak wywołać procedurę składowaną przy użyciu zestawu SDK platformy .NET:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri uri = UriFactory.CreateStoredProcedureUri("myDatabase", "myContainer", "spCreateToDoItem");
RequestOptions options = new RequestOptions { PartitionKey = new PartitionKey("Personal") };
var result = await client.ExecuteStoredProcedureAsync<string>(uri, options, newItem);
var id = result.Response;
```

### <a name="stored-procedures---java-sdk"></a>Procedury składowane — zestaw SDK platformy Java

Poniższy przykład pokazuje, jak zarejestrować procedurę składowaną przy użyciu zestawu SDK platformy Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
StoredProcedure newStoredProcedure = new StoredProcedure(
    "{" +
        "  'id':'spCreateToDoItem'," +
        "  'body':" + new String(Files.readAllBytes(Paths.get("..\\js\\spCreateToDoItem.js"))) +
    "}");
//toBlocking() blocks the thread until the operation is complete and is used only for demo.  
StoredProcedure createdStoredProcedure = asyncClient.createStoredProcedure(containerLink, newStoredProcedure, null)
    .toBlocking().single().getResource();
```

Poniższy kod pokazuje, jak wywołać procedurę składowaną przy użyciu zestawu SDK platformy Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String sprocLink = String.format("%s/sprocs/%s", containerLink, "spCreateToDoItem");
final CountDownLatch successfulCompletionLatch = new CountDownLatch(1);

class ToDoItem {
    public String category;
    public String name;
    public String description;
    public boolean isComplete;
}

ToDoItem newItem = new ToDoItem();
newItem.category = "Personal";
newItem.name = "Groceries";
newItem.description = "Pick up strawberries";
newItem.isComplete = false;

RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("Personal"));

Object[] storedProcedureArgs = new Object[] { newItem };
asyncClient.executeStoredProcedure(sprocLink, requestOptions, storedProcedureArgs)
    .subscribe(storedProcedureResponse -> {
        String storedProcResultAsString = storedProcedureResponse.getResponseAsString();
        successfulCompletionLatch.countDown();
        System.out.println(storedProcedureResponse.getActivityId());
    }, error -> {
        System.err.println("an error occurred while executing the stored procedure: actual cause: "
                + error.getMessage());
    });

successfulCompletionLatch.await();
```

### <a name="stored-procedures---javascript-sdk"></a>Procedury składowane — zestaw SDK języka JavaScript

Poniższy przykład pokazuje, jak zarejestrować procedurę składowaną przy użyciu zestawu SDK języka JavaScript

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
await container.storedProcedures.create({
    id: sprocId,
    body: require(`../js/${sprocId}`)
});
```

Poniższy kod pokazuje, jak wywołać procedurę składowaną przy użyciu zestawu SDK języka JavaScript:

```javascript
const newItem = [{
    category: "Personal",
    name: "Groceries",
    description: "Pick up strawberries",
    isComplete: false
}];
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
const {body: result} = await container.storedProcedure(sprocId).execute(newItem, {partitionKey: newItem[0].category});
```

### <a name="stored-procedures---python-sdk"></a>Procedury składowane — zestaw SDK języka Python

Poniższy przykład pokazuje, jak zarejestrować procedurę składowaną przy użyciu zestawu SDK języka Python

```python
with open('../js/spCreateToDoItem.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
sproc_definition = {
            'id': 'spCreateToDoItem',
            'serverScript': file_contents,
        }
sproc = client.CreateStoredProcedure(container_link, sproc_definition)
```

Poniższy kod pokazuje, jak wywołać procedurę składowaną przy użyciu zestawu SDK języka Python

```python
sproc_link = 'dbs/myDatabase/colls/myContainer/sprocs/spCreateToDoItem'
new_item = [{
    'category':'Personal',
    'name':'Groceries',
    'description':'Pick up strawberries',
    'isComplete': False
}]
client.ExecuteStoredProcedure(sproc_link, new_item, {'partitionKey': 'Personal'}
```

## <a id="pre-triggers"></a>Jak uruchamiać wyzwalacze wywoływane przed operacją

Poniższe przykłady pokazują, jak zarejestrować i wywoływać wyzwalacz uruchamiany przed operacją przy użyciu zestawów SDK usługi Azure Cosmos DB. Zapoznaj się z [przykładem wyzwalacza wywoływanego przed operacją](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers), ponieważ źródło tego wyzwalacza jest zapisane jako `trgPreValidateToDoItemTimestamp.js`.

Podczas wykonywania wyzwalacze wywoływane przed operacją są przekazywane w obiekcie RequestOptions przez określenie parametru `PreTriggerInclude`, a następnie przekazanie nazwy wyzwalacza w obiekcie listy.

> [!NOTE]
> Mimo że nazwa wyzwalacza jest przekazywana jako lista, nadal można wykonywać tylko jeden wyzwalacz na operację.

### <a name="pre-triggers---net-sdk"></a>Wyzwalacze wykonywane przed operacją — zestaw SDK platformy .NET

Poniższy kod pokazuje, jak zarejestrować wyzwalacz wykonywany przed operacją przy użyciu zestawu SDK platformy .NET:

```csharp
string triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger
{
    Id =  triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

Poniższy kod pokazuje, jak wywołać wyzwalacz wykonywany przed operacją przy użyciu zestawu SDK platformy .NET:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
RequestOptions requestOptions = new RequestOptions { PreTriggerInclude = new List<string> { "trgPreValidateToDoItemTimestamp" } };
await client.CreateDocumentAsync(containerUri, newItem, requestOptions);
```

### <a name="pre-triggers---java-sdk"></a>Wyzwalacze wykonywane przed operacją — zestaw SDK platformy Java

Poniższy kod pokazuje, jak zarejestrować wyzwalacz wykonywany przed operacją przy użyciu zestawu SDK platformy Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Pre);
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

Poniższy kod pokazuje, jak wywołać wyzwalacz wykonywany przed operacją przy użyciu zestawu SDK platformy Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
    Document item = new Document("{ "
            + "\"category\": \"Personal\", "
            + "\"name\": \"Groceries\", "
            + "\"description\": \"Pick up strawberries\", "
            + "\"isComplete\": false, "
            + "}"
            );
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPreTriggerInclude(Arrays.asList("trgPreValidateToDoItemTimestamp"));
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="pre-triggers---javascript-sdk"></a>Wyzwalacze wykonywane przed operacją — zestaw SDK języka JavaScript

Poniższy kod pokazuje, jak zarejestrować wyzwalacz wykonywany przed operacją przy użyciu zestawu SDK języka JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "pre"
});
```

Poniższy kod pokazuje, jak wywołać wyzwalacz wykonywany przed operacją przy użyciu zestawu SDK języka JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.items.create({
    category: "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
}, {preTriggerInclude: [triggerId]});
```

### <a name="pre-triggers---python-sdk"></a>Wyzwalacze wykonywane przed operacją — zestaw SDK języka Python

Poniższy kod pokazuje, jak zarejestrować wyzwalacz wykonywany przed operacją przy użyciu zestawu SDK języka Python:

```python
with open('../js/trgPreValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
            'id': 'trgPreValidateToDoItemTimestamp',
            'serverScript': file_contents,
            'triggerType': documents.TriggerType.Pre,
            'triggerOperation': documents.TriggerOperation.Create
        }
trigger = client.CreateTrigger(container_link, trigger_definition)
```

Poniższy kod pokazuje, jak wywołać wyzwalacz wykonywany przed operacją przy użyciu zestawu SDK języka Python:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = { 'category': 'Personal', 'name': 'Groceries', 'description':'Pick up strawberries', 'isComplete': False}
client.CreateItem(container_link, item, { 'preTriggerInclude': 'trgPreValidateToDoItemTimestamp'})
```

## <a id="post-triggers"></a>Jak uruchamiać wyzwalacze wywoływane po operacji

Poniższe przykłady pokazują, jak zarejestrować wyzwalacz uruchamiany po operacji przy użyciu zestawów SDK usługi Azure Cosmos DB. Zapoznaj się z [przykładem wyzwalacza wywoływanego po operacji](how-to-write-stored-procedures-triggers-udfs.md#post-triggers), ponieważ źródło tego wyzwalacza jest zapisane jako `trgPostUpdateMetadata.js`.

### <a name="post-triggers---net-sdk"></a>Wyzwalacze wykonywane po operacji — zestaw SDK platformy .NET

Poniższy kod pokazuje, jak zarejestrować wyzwalacz wykonywany po operacji przy użyciu zestawu SDK platformy .NET:

```csharp
string triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger
{
    Id = triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

Poniższy kod pokazuje, jak wywołać wyzwalacz wykonywany po operacji przy użyciu zestawu SDK platformy .NET:

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

var options = { postTriggerInclude: "trgPostUpdateMetadata" };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.createDocumentAsync(containerUri, newItem, options);
```

### <a name="post-triggers---java-sdk"></a>Wyzwalacze wykonywane po operacji — zestaw SDK platformy Java

Poniższy kod pokazuje, jak zarejestrować wyzwalacz wykonywany po operacji przy użyciu zestawu SDK platformy Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)))));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Post);
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

Poniższy kod pokazuje, jak wywołać wyzwalacz wykonywany po operacji przy użyciu zestawu SDK platformy Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Document item = new Document(String.format("{ "
    + "\"name\": \"artist_profile_1023\", "
    + "\"artist\": \"The Band\", "
    + "\"albums\": [\"Hellujah\", \"Rotators\", \"Spinning Top\"]"
    + "}"
));
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPostTriggerInclude(Arrays.asList("trgPostUpdateMetadata"));
//toBlocking() blocks the thread until the operation is complete, and is used only for demo.
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="post-triggers---javascript-sdk"></a>Wyzwalacze wykonywane po operacji — zestaw SDK języka JavaScript

Poniższy kod pokazuje, jak zarejestrować wyzwalacz wykonywany po operacji przy użyciu zestawu SDK języka JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "post"
});
```

Poniższy kod pokazuje, jak wywołać wyzwalacz wykonywany po operacji przy użyciu zestawu SDK języka JavaScript:

```javascript
const item = {
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.items.create(item, {postTriggerInclude: [triggerId]});
```

### <a name="post-triggers---python-sdk"></a>Wyzwalacze wykonywane po operacji — zestaw SDK języka Python

Poniższy kod pokazuje, jak zarejestrować wyzwalacz wykonywany po operacji przy użyciu zestawu SDK języka Python:

```python
with open('../js/trgPostUpdateMetadata.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
            'id': 'trgPostUpdateMetadata',
            'serverScript': file_contents,
            'triggerType': documents.TriggerType.Post,
            'triggerOperation': documents.TriggerOperation.Create
        }
trigger = client.CreateTrigger(container_link, trigger_definition)
```

Poniższy kod pokazuje, jak wywołać wyzwalacz wykonywany po operacji przy użyciu zestawu SDK języka Python:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = { 'name': 'artist_profile_1023', 'artist': 'The Band', 'albums': ['Hellujah', 'Rotators', 'Spinning Top']}
client.CreateItem(container_link, item, { 'postTriggerInclude': 'trgPostUpdateMetadata'})
```

## <a id="udfs"></a>Jak pracować z funkcjami zdefiniowanymi przez użytkownika

Poniższe przykłady pokazują, jak zarejestrować funkcję zdefiniowaną przez użytkownika przy użyciu zestawów SDK usługi Azure Cosmos DB. Zapoznaj się z tym [przykładem funkcji zdefiniowanej przez użytkownika](how-to-write-stored-procedures-triggers-udfs.md#udfs), ponieważ źródło tego wyzwalacza jest zapisane jako `udfTax.js`.

### <a name="user-defined-functions---net-sdk"></a>Funkcje zdefiniowane przez użytkownika — zestaw SDK platformy .NET

Poniższy kod pokazuje, jak zarejestrować funkcję zdefiniowaną przez użytkownika przy użyciu zestawu SDK platformy .NET:

```csharp
string udfId = "Tax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js"),
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

Poniższy kod pokazuje, jak wywołać funkcję zdefiniowaną przez użytkownika przy użyciu zestawu SDK platformy .NET:

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---java-sdk"></a>Funkcje zdefiniowane przez użytkownika — zestaw SDK platformy Java

Poniższy kod pokazuje, jak zarejestrować funkcję zdefiniowaną przez użytkownika przy użyciu zestawu SDK platformy Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String udfId = "Tax";
UserDefinedFunction udf = new UserDefinedFunction();
udf.setId(udfId);
udf.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", udfId)))));
//toBlocking() blocks the thread until the operation is complete and is used only for demo.
UserDefinedFunction createdUDF = client.createUserDefinedFunction(containerLink, udf, new RequestOptions()).toBlocking().single().getResource();
```

Poniższy kod pokazuje, jak wywołać funkcję zdefiniowaną przez użytkownika przy użyciu zestawu SDK platformy Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(containerLink, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000", new FeedOptions());
final CountDownLatch completionLatch = new CountDownLatch(1);
queryObservable.subscribe(
        queryResultPage -> {
            System.out.println("Got a page of query result with " +
                    queryResultPage.getResults().size());
        },
        // terminal error signal
        e -> {
            e.printStackTrace();
            completionLatch.countDown();
        },

        // terminal completion signal
        () -> {
            completionLatch.countDown();
        });
completionLatch.await();
```

### <a name="user-defined-functions---javascript-sdk"></a>Funkcje zdefiniowane przez użytkownika — zestaw SDK języka JavaScript

Poniższy kod pokazuje, jak zarejestrować funkcję zdefiniowaną przez użytkownika przy użyciu zestawu SDK języka JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const udfId = "Tax";
await container.userDefinedFunctions.create({
    id: udfId,
    body: require(`../js/${udfId}`)
```

Poniższy kod pokazuje, jak wywołać funkcję zdefiniowaną przez użytkownika przy użyciu zestawu SDK języka JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const sql = "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000";
const {result} = await container.items.query(sql).toArray();
```

### <a name="user-defined-functions---python-sdk"></a>Funkcje zdefiniowane przez użytkownika — zestaw SDK języka Python

Poniższy kod pokazuje, jak zarejestrować funkcję zdefiniowaną przez użytkownika przy użyciu zestawu SDK języka Python:

```python
with open('../js/udfTax.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
udf_definition = {
            'id': 'Tax',
            'serverScript': file_contents,
        }
udf = client.CreateUserDefinedFunction(container_link, udf_definition)
```

Poniższy kod pokazuje, jak wywołać funkcję zdefiniowaną przez użytkownika przy użyciu zestawu SDK języka Python:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
results = list(client.QueryItems(container_link, 'SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000'))
```

## <a name="next-steps"></a>Kolejne kroki

Poznaj dodatkowe pojęcia i dowiedz się, jak pisać procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika w usłudze Azure Cosmos DB:

- [Praca z procedurami składowanymi, wyzwalaczami i funkcjami zdefiniowanymi przez użytkownika w usłudze Azure Cosmos DB](stored-procedures-triggers-udfs.md)
- [Praca ze zintegrowanym interfejsem API zapytań języka JavaScript w usłudze Azure Cosmos DB](javascript-query-api.md)
- [Jak pisać procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika w usłudze Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md)
- [Jak pisać procedury składowane i wyzwalacze przy użyciu interfejsu API zapytań języka JavaScript w usłudze Azure Cosmos DB](how-to-write-javascript-query-api.md)
