---
title: Azure Cosmos DB powiązanie danych wyjściowych dla funkcji 2. x
description: Dowiedz się, jak używać powiązania danych wyjściowych Azure Cosmos DB w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: 9360a90b457f99cb9c15deda80dce8233069100d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357475"
---
# <a name="azure-cosmos-db-output-binding-for-azure-functions-2x"></a>Azure Cosmos DB powiązanie danych wyjściowych dla Azure Functions 2. x

Dane wyjściowe usługi Azure Cosmos DB, powiązania pozwala zapisać nowy dokument z bazą danych Azure Cosmos DB przy użyciu interfejsu API SQL.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz kolejki, napisz jeden dokument](#queue-trigger-write-one-doc-c)
* [Wyzwalacz kolejki, Zapisz dokumenty przy użyciu IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c)

Przykłady odnoszą się do prostego typu `ToDoItem`:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-write-one-doc-c"></a>

### <a name="queue-trigger-write-one-doc"></a>Wyzwalacz kolejki, doc jeden zapisu

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która dodaje dokument do bazy danych przy użyciu danych z usługi queue storage.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            ILogger log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.LogInformation($"C# Queue trigger function inserted one row");
            log.LogInformation($"Description={queueMessage}");
        }
    }
}
```

<a id="queue-trigger-write-docs-using-iasynccollector-c"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wyzwalacz kolejki, dokumentacja zapisu przy użyciu IAsyncCollector

W poniższym przykładzie pokazano [ C# funkcję](functions-dotnet-class-library.md) , która dodaje kolekcję dokumentów do bazy danych przy użyciu danych udostępnionych w formacie JSON komunikatu w kolejce.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.LogInformation($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[C#Napisy](#tab/csharp-script)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz kolejki, napisz jeden dokument](#queue-trigger-write-one-doc-c-script)
* [Wyzwalacz kolejki, Zapisz dokumenty przy użyciu IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

### <a name="queue-trigger-write-one-doc"></a>Wyzwalacz kolejki, doc jeden zapisu

Poniższy przykład pokazuje Azure Cosmos DB powiązanie danych wyjściowych w pliku *Function. JSON* i [ C# funkcji skryptu](functions-reference-csharp.md) , która używa powiązania. Funkcja używa powiązania danych wejściowych w kolejce dla kolejki, która odbiera JSON w następującym formacie:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funkcja tworzy dokumentów usługi Azure Cosmos DB w następującym formacie, dla każdego rekordu:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Oto dane powiązania w pliku *Function. JSON* :

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

Poniżej przedstawiono kod skryptu języka C#:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(string myQueueItem, out object employeeDocument, ILogger log)
    {
      log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

<a id="queue-trigger-write-docs-using-iasynccollector-c-script"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wyzwalacz kolejki, dokumentacja zapisu przy użyciu IAsyncCollector

Aby utworzyć wiele dokumentów, można powiązać z `ICollector<T>` lub `IAsyncCollector<T>`, gdzie `T` jest jednym z obsługiwanych typów.

Ten przykład dotyczy prostego typu `ToDoItem`:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

Poniżej przedstawiono plik function.json:

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connectionStringSetting": "AzureWebJobsStorage"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Poniżej przedstawiono kod skryptu języka C#:

```cs
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.LogInformation($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład pokazuje Azure Cosmos DB powiązanie danych wyjściowych w pliku *Function. JSON* i [funkcję języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja używa powiązania danych wejściowych w kolejce dla kolejki, która odbiera JSON w następującym formacie:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funkcja tworzy dokumentów usługi Azure Cosmos DB w następującym formacie, dla każdego rekordu:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Oto dane powiązania w pliku *Function. JSON* :

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

Poniżej przedstawiono kod JavaScript:

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie pokazano, jak napisać dokument do bazy danych usługi Azure CosmosDB jako dane wyjściowe funkcji.

Definicja powiązania jest zdefiniowana w *funkcji Function. JSON* , w której *typ* ma wartość `cosmosDB`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "cosmosDB",
      "direction": "out",
      "name": "doc",
      "databaseName": "demodb",
      "collectionName": "data",
      "createIfNotExists": "true",
      "connectionStringSetting": "AzureCosmosDBConnectionString"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Aby zapisać dane w bazie danych, przekaż obiekt dokumentu do metody `set` parametru Database.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

* [Wyzwalacz kolejki, Zapisz komunikat do bazy danych za pośrednictwem wartości zwracanej](#queue-trigger-save-message-to-database-via-return-value-java)
* [Wyzwalacz HTTP, Zapisz jeden dokument do bazy danych za pośrednictwem wartości zwracanej](#http-trigger-save-one-document-to-database-via-return-value-java)
* [Wyzwalacz HTTP, Zapisz jeden dokument do bazy danych za pośrednictwem kodu Wyjściowegobinding](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [Wyzwalacz HTTP, zapisanie wielu dokumentów do bazy danych za pośrednictwem kodu Wyjściowegobinding](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>Wyzwalacz kolejki, Zapisz komunikat do bazy danych za pośrednictwem wartości zwracanej

Poniższy przykład pokazuje funkcję języka Java, która dodaje dokument do bazy danych z danymi z wiadomości w usłudze queue storage.

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database",
  databaseName = "ToDoList",
  collectionName = "Items",
  connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
    @QueueTrigger(name = "msg",
      queueName = "myqueue-items",
      connection = "AzureWebJobsStorage")
    String message,
    final ExecutionContext context)  {
     return "{ id: \"" + System.currentTimeMillis() + "\", Description: " + message + " }";
   }
```
<a id="http-trigger-save-one-document-to-database-via-return-value-java"></a>

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>Wyzwalacz HTTP, Zapisz jeden dokument do bazy danych za pośrednictwem wartości zwracanej

Poniższy przykład pokazuje funkcję języka Java, której podpis jest oznaczony ```@CosmosDBOutput``` i ma zwracaną wartość typu ```String```. Dokument JSON zwrócony przez funkcję zostanie automatycznie zapisany w odpowiedniej kolekcji CosmosDB.

```java
    @FunctionName("WriteOneDoc")
    @CosmosDBOutput(name = "database",
      databaseName = "ToDoList",
      collectionName = "Items",
      connectionStringSetting = "Cosmos_DB_Connection_String")
    public String run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        return jsonDocument;
    }
```

<a id="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>Wyzwalacz HTTP, Zapisz jeden dokument do bazy danych za pośrednictwem kodu Wyjściowegobinding

Poniższy przykład pokazuje funkcję języka Java, która zapisuje dokument do CosmosDB za pośrednictwem parametru wyjściowego ```OutputBinding<T>```. W tym przykładzie do parametru ```outputItem``` należy dodać adnotację przy użyciu ```@CosmosDBOutput```, a nie sygnatury funkcji. Korzystanie z ```OutputBinding<T>``` umożliwia korzystanie z funkcji powiązania w celu zapisania dokumentu w CosmosDB, a jednocześnie pozwala zwrócić inną wartość do obiektu wywołującego funkcji, takiego jak dokument JSON lub XML.

```java
    @FunctionName("WriteOneDocOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<String> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        // Set outputItem's value to the JSON document to be saved
        outputItem.setValue(jsonDocument);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Document created successfully.")
                      .build();
    }
```

<a id="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>Wyzwalacz HTTP, zapisanie wielu dokumentów do bazy danych za pośrednictwem kodu Wyjściowegobinding

Poniższy przykład pokazuje funkcję języka Java, która zapisuje wiele dokumentów do CosmosDB za pośrednictwem parametru wyjściowego ```OutputBinding<T>```. W tym przykładzie parametr ```outputItem``` jest oznaczony przy użyciu ```@CosmosDBOutput```, a nie sygnatury funkcji. Parametr wyjściowy, ```outputItem``` ma listę obiektów ```ToDoItem``` jako typ parametru szablonu. Używanie ```OutputBinding<T>``` umożliwia korzystanie z funkcji powiązania w celu zapisywania dokumentów w CosmosDB, a jednocześnie pozwala zwrócić inną wartość do obiektu wywołującego funkcji, takiego jak dokument JSON lub XML.

```java
    @FunctionName("WriteMultipleDocsOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<List<ToDoItem>> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate documents
        List<ToDoItem> items = new ArrayList<>();

        for (int i = 0; i < 5; i ++) {
          // Generate random ID
          final int id = Math.abs(new Random().nextInt());

          // Create ToDoItem
          ToDoItem item = new ToDoItem(String.valueOf(id), name);

          items.add(item);
        }

        // Set outputItem's value to the list of POJOs to be saved
        outputItem.setValue(items);
        context.getLogger().info("Document to be saved: " + items);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Documents created successfully.")
                      .build();
    }
```

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj adnotacji `@CosmosDBOutput` w parametrach, które będą zapisywane w Cosmos DB.  Typ parametru adnotacji powinien mieć wartość ```OutputBinding<T>```, gdzie T jest natywnym typem Java lub POJO.

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj atrybutu [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) .

Konstruktor atrybutu ma nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i innych właściwościach, które można skonfigurować, zobacz [wyjście-konfiguracja](#configuration). Oto przykład atrybutu `CosmosDB` w sygnaturze metody:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

# <a name="c-script"></a>[C#Napisy](#tab/csharp-script)

Atrybuty nie są obsługiwane przez C# skrypt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="java"></a>[Java](#tab/java)

Adnotacja `CosmosDBOutput` jest dostępna do zapisywania danych do Cosmos DB. Adnotację można zastosować do funkcji lub do poszczególnych parametrów funkcji. W przypadku użycia na metodzie funkcji zwracana wartość funkcji jest zapisywana w Cosmos DB. Jeśli używasz adnotacji z parametrem, typ parametru musi być zadeklarowany jako `OutputBinding<T>`, gdzie `T` natywny typ Java lub POJO.

---

## <a name="configuration"></a>Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* i `CosmosDB` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type**     | Nie dotyczy | Musi być ustawiony na `cosmosDB`.        |
|**direction**     | Nie dotyczy | Musi być ustawiony na `out`.         |
|**Nazwij**     | Nie dotyczy | Nazwa parametru powiązania, który reprezentuje dokument w funkcji.  |
|**Bazy** | **Bazy**|Baza danych, zawierający kolekcję, w którym zostanie utworzona dokumentu.     |
|**CollectionName** |**CollectionName**  | Nazwa kolekcji jest tworzona dokumentu. |
|**Metodę createifnotexists**  |**Metodę createifnotexists**    | Wartość logiczna, aby wskazać, czy kolekcja jest tworzona, gdy nie istnieje. Wartość domyślna to *false* , ponieważ nowe kolekcje są tworzone z zarezerwowaną przepływność, która ma wpływ na koszty. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**partitionKey**|**PartitionKey** |Gdy `CreateIfNotExists` ma wartość true, definiuje ścieżkę klucza partycji dla utworzonej kolekcji.|
|**collectionThroughput**|**CollectionThroughput**| Gdy `CreateIfNotExists` ma wartość true, definiuje [przepływność](../cosmos-db/set-throughput.md) utworzonej kolekcji.|
|**connectionStringSetting**    |**ConnectionStringSetting** |Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Sposób użycia

Domyślnie podczas zapisu do parametru wyjściowego w funkcji, tworzony jest dokument w bazie danych. Ten dokument ma automatycznie generowanym identyfikatorze GUID jako identyfikatora dokumentu. Możesz określić identyfikator dokumentu dla dokumentu wyjściowego, określając właściwość `id` w obiekcie JSON przekazaną do parametru Output.

> [!Note]
> Po określeniu identyfikator istniejącego dokumentu, pobiera to zastąpione przez nowy dokument danych wyjściowych.

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie | Dokumentacja |
|---|---|
| CosmosDB | [Kody błędów CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>Ustawienia host.JSON

W tej sekcji opisano globalne ustawienia konfiguracji dostępne dla tego powiązania w wersji 2.x. Aby uzyskać więcej informacji na temat ustawień konfiguracji globalnej w wersji 2. x, zobacz informacje dotyczące pliku [host. JSON dla Azure Functions wersji 2. x](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------|
|GatewayMode|Brama|Tryb połączenia używany przez funkcję podczas nawiązywania połączenia z usługą Azure Cosmos DB. Opcje są `Direct` i `Gateway`|
|Protokół|Schemat|Protokół połączenia używany przez funkcję podczas nawiązywania połączenia z usługą Azure Cosmos DB.  Przeczytaj [tutaj, aby uzyskać wyjaśnienie obu trybów](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|Nie dotyczy|Prefiks dzierżawy do użycia we wszystkich funkcjach w aplikacji.|

## <a name="next-steps"></a>Następne kroki

- [Uruchom funkcję w przypadku utworzenia lub zmodyfikowania dokumentu Azure Cosmos DB (wyzwalacz)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Odczytaj dokument Azure Cosmos DB (powiązanie danych wejściowych)](./functions-bindings-cosmosdb-v2-input.md)