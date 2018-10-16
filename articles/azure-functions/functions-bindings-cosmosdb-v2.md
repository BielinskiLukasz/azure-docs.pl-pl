---
title: Azure Cosmos DB powiązania funkcji 2.x
description: Dowiedz się, jak używać usługi Azure Cosmos DB, wyzwalaczy i powiązań w usłudze Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: usługi Azure functions, funkcje, przetwarzanie zdarzeń, obliczanie dynamiczne, architektura bez serwera
ms.service: azure-functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: 0726bd3ded0618d7fb45a589c21325717da7a5cf
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319034"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-2x"></a>Azure Cosmos DB powiązania usługi Azure Functions 2.x

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [Wersja 1](functions-bindings-cosmosdb.md)
> * [W wersji 2](functions-bindings-cosmosdb-v2.md)

W tym artykule wyjaśniono, jak pracować z [usługi Azure Cosmos DB](..\cosmos-db\serverless-computing-database.md) powiązań w usłudze Azure Functions 2.x. Usługi Azure Functions obsługuje wyzwalanie, dane wejściowe i wyjściowe powiązań usługi Azure Cosmos DB.

> [!NOTE]
> Ten artykuł jest przeznaczony dla [usługi Azure Functions w wersji 2.x](functions-versions.md).  Aby uzyskać informacje o sposobie używania tych powiązań w funkcjach 1.x, zobacz [powiązań usługi Azure Cosmos DB dla usługi Azure Functions 1.x](functions-bindings-cosmosdb.md).
>
> To powiązanie pierwotnie nosiła nazwę bazy danych DocumentDB. W funkcji w wersji 2.x, wyzwalacz, powiązania i pakietu są wszystkie o nazwie usługi Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-apis"></a>Obsługiwane interfejsy API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Powiązania usługi Azure Cosmos DB dla funkcji w wersji 2.x znajdują się w [Microsoft.Azure.WebJobs.Extensions.CosmosDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) pakietu NuGet w wersji 3.x. Kod źródłowy dla powiązania znajduje się w [zestawu sdk rozszerzenia, usługi azure webjobs w-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) repozytorium GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Wyzwalacz

Wyzwalacz usługi Azure Cosmos DB używa [usługi Azure Cosmos DB kanału informacyjnego zmian](../cosmos-db/change-feed.md) do nasłuchiwania pod kątem operacji wstawienia i aktualizacje w różnych partycjach. Kanał informacyjny zmian publikuje wstawienia i aktualizacje, nie do usunięcia.

## <a name="trigger---example"></a>Wyzwalacz — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#trigger---c-example)
* [Skryptu C# (csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)

[Pomiń przykłady wyzwalacza](#trigger---attributes)

### <a name="trigger---c-example"></a>Wyzwalacz — przykład w języku C#

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , wywoływany, gdy istnieją wstawia lub aktualizuje w określonej bazy danych i kolekcji.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

namespace CosmosDBSamplesV2
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents, 
            TraceWriter log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.Info($"Documents modified: {documents.Count}");
                log.Info($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

[Pomiń przykłady wyzwalacza](#trigger---attributes)

### <a name="trigger---c-script-example"></a>Wyzwalacz — przykładowy skrypt w języku C#

W poniższym przykładzie pokazano wyzwalacz usługi Cosmos DB, powiązania w *function.json* pliku i [funkcji skryptu w języku C#](functions-reference-csharp.md) powiązania, który używa. Funkcja zapisuje komunikaty dziennika, po zmodyfikowaniu rekordy usługi Cosmos DB.

Oto powiązanie danych w *function.json* pliku:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Poniżej przedstawiono kod skryptu języka C#:
 
```cs 
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
      log.Verbose("Documents modified " + documents.Count);
      log.Verbose("First document Id " + documents[0].Id);
    }
```

[Pomiń przykłady wyzwalacza](#trigger---attributes)

### <a name="trigger---javascript-example"></a>Wyzwalacz — przykład JavaScript

W poniższym przykładzie pokazano wyzwalacz usługi Cosmos DB, powiązania w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) powiązania, który używa. Funkcja zapisuje komunikaty dziennika, po zmodyfikowaniu rekordy usługi Cosmos DB.

Oto powiązanie danych w *function.json* pliku:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Poniżej przedstawiono kod JavaScript:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

### <a name="trigger---java-example"></a>Wyzwalacz - przykładzie w języku Java

W poniższym przykładzie pokazano wyzwalacz usługi Cosmos DB, powiązania w *function.json* pliku i [funkcja Java](functions-reference-java.md) powiązania, który używa. Funkcja jest zaangażowane w przypadku wstawienia lub aktualizacji w określonej bazy danych i kolekcji.

```json
{
    "type": "cosmosDBTrigger",
    "name": "items",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "AzureCosmosDBConnection",
    "databaseName": "ToDoList",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": false
}
```

Oto kodu Java:

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            reateLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


W [Java funkcje biblioteki środowiska uruchomieniowego](/java/api/overview/azure/functions/runtime), użyj `@CosmosDBTrigger` adnotacji w parametrach, którego wartość może pochodzić z usługi Cosmos DB.  Ta adnotacja mogą być używane z natywnych typów języka Java, obiektów typu Pojo lub wartości dopuszczających wartości null przy użyciu opcjonalnie<T>. 

## <a name="trigger---c-attributes"></a>Wyzwalacz — C# atrybutów

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) atrybutu.

Konstruktor atrybutu ma nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i inne właściwości, które można skonfigurować, zobacz [wyzwalacza — Konfiguracja](#trigger---configuration). Oto `CosmosDBTrigger` przykład atrybutu w podpisie metody:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
        ...
    }
```

Aby uzyskać kompletny przykład, zobacz [wyzwalacza — przykład w języku C#](#trigger---c-example).


## <a name="trigger---configuration"></a>Wyzwalacz — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `CosmosDBTrigger` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** || Musi być równa `cosmosDBTrigger`. |
|**direction** || Musi być równa `in`. Ten parametr jest ustawiany automatycznie po utworzeniu wyzwalacza w witrynie Azure portal. |
|**Nazwa** || Nazwa zmiennej, używany w kodzie funkcji, który reprezentuje listę dokumentów za pomocą zmian. | 
|**connectionStringSetting**|**ConnectionStringSetting** | Nazwa ustawienia aplikacji zawierającego parametry połączenia używane do łączenia z konta usługi Azure Cosmos DB są monitorowane. |
|**databaseName**|**DatabaseName**  | Nazwa bazy danych Azure Cosmos DB za pomocą kolekcji są monitorowane. |
|**collectionName** |**collectionName** | Nazwa kolekcji są monitorowane. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Opcjonalnie) Nazwa ustawienia aplikacji zawierającego parametry połączenia z usługą, która zawiera kolekcję dzierżaw. Gdy nie są ustawione, `connectionStringSetting` wartość jest używana. Ten parametr jest automatycznie ustawiana podczas tworzenia powiązania w portalu. Parametry połączenia dla kolekcji dzierżaw musi mieć uprawnienia do zapisu.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Opcjonalnie) Nazwa bazy danych, który zawiera kolekcję używaną do przechowywania dzierżaw. Jeśli nie ustawiona, wartość `databaseName` ustawienie jest używane. Ten parametr jest automatycznie ustawiana podczas tworzenia powiązania w portalu. |
|**leaseCollectionName** | **LeaseCollectionName** | (Opcjonalnie) Nazwa kolekcji, używany do przechowywania dzierżaw. Kiedy nie ustawiona, wartość `leases` jest używany. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Opcjonalnie) Po ustawieniu `true`, kolekcję dzierżaw zostało automatycznie utworzone po już nie istnieje. Wartość domyślna to `false`. |
|**leasesCollectionThroughput**| **leasesCollectionThroughput**| (Opcjonalnie) Definiuje liczbę jednostek żądania do przypisania, po utworzeniu kolekcji dzierżaw. To ustawienie jest tylko wtedy, gdy używane `createLeaseCollectionIfNotExists` ustawiono `true`. Ten parametr jest automatycznie ustawiana, jeśli wiązanie jest tworzony przy użyciu portalu.
|**leaseCollectionPrefix**| **leaseCollectionPrefix**| (Opcjonalnie) Po ustawieniu dodaje prefiks do dzierżawy utworzone w kolekcję dzierżaw dla tej funkcji, co skutecznie dwie oddzielne funkcje platformy Azure na udostępnianie tej samej kolekcji dzierżawy przy użyciu różnych prefiksów.
|**feedPollDelay**| **feedPollDelay**| (Opcjonalnie) Gdy zestaw, definiuje, w milisekundach, opóźnienie między sondowaniem partycji dla nowych zmian w źródle danych, gdy wszystkie bieżące zmiany są opróżniane. Wartością domyślną jest 5000 (5 sekund).
|**leaseAcquireInterval**| **leaseAcquireInterval**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwał można uruchamiać zadania obliczeniowe, jeśli partycje są dystrybuowane równomiernie między wystąpieniami znanych hostów. Wartość domyślna to 13000 (w sekundach 13).
|**leaseExpirationInterval**| **leaseExpirationInterval**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwał, dla której dzierżawy jest pobierany podczas dzierżawy, reprezentujący partycji. Jeśli dzierżawa nie zostanie odnowiony w tym przedziale czasu, spowoduje jego wygaśnięcia i własność partycji przejdzie do innego wystąpienia. Domyślna to 60 000 (60 sekund).
|**leaseRenewInterval**| **leaseRenewInterval**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwał odnawiania dla wszystkich dzierżaw dla partycji aktualnie trzymana przez wystąpienie. Wartość domyślna to 17000 (17 w sekundach).
|**checkpointFrequency**| **checkpointFrequency**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwał między punktami kontrolnymi dzierżawy. Wartością domyślną jest zawsze po pomyślnym wywołania funkcji.
|**maxItemsPerInvocation**| **maxItemsPerInvocation**| (Opcjonalnie) Po ustawieniu dostosowuje maksymalna ilość elementów odebranych na wywołanie funkcji.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Wyzwalacz — użycie

Wyzwalacz wymaga druga kolekcja, która jest używana do przechowywania _dzierżawy_ poszczególnych partycjach. Kolekcja monitorowanych i kolekcji, która zawiera dzierżaw musi być dostępny dla wyzwalacza do pracy.

>[!IMPORTANT]
> Jeśli wiele funkcji są skonfigurowane do użycia wyzwalacz usługi Cosmos DB na potrzeby tej samej kolekcji, każda z tych funkcji należy używać kolekcji bezpiecznych dedykowanych dzierżawy lub określić inną `LeaseCollectionPrefix` dla każdej funkcji. W przeciwnym razie tylko jeden funkcje zostaną wyzwolone. Aby uzyskać informacji na temat prefiksu, zobacz [sekcji konfiguracji](#trigger---configuration).

Wyzwalacz nie wskazuje, czy zaktualizowane lub wstawić dokumentu, po prostu zapewnia samego dokumentu. Jeśli wymagana jest obsługa aktualizacje i wstawienia w różny sposób, możesz można zrobić, implementując pola sygnatury czasowej dla wstawiania lub aktualizacji.

## <a name="input"></a>Dane wejściowe

Powiązania danych wejściowych usługi Azure Cosmos DB używa interfejsu API SQL do pobrania jednego lub więcej dokumentów usługi Azure Cosmos DB i przekazuje je do parametr wejściowy funkcji. Można określić parametry identyfikator lub kwerendę dokumentu oparte na wyzwalacz, który wywołuje funkcję. 

## <a name="input---examples"></a>Dane wejściowe — przykłady

Zobacz przykłady specyficzny dla języka, które odczytują pojedynczy dokument, określając wartość Identyfikatora:

* [C#](#input---c-examples)
* [Skryptu C# (csx)](#input---c-script-examples)
* [JavaScript](#input---javascript-examples)
* [F#](#input---f-examples)
* [Java](#input---java-examples)

[Pomiń przykłady danych wejściowych](#input---attributes)

### <a name="input---c-examples"></a>Dane wejściowe — przykłady w języku C#

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz kolejki, odnośnika identyfikator z formatu JSON](#queue-trigger-look-up-id-from-json-c)
* [Wyzwalacz HTTP, Sprawdź identyfikator z ciągu zapytania](#http-trigger-look-up-id-from-query-string-c)
* [Wyzwalacz HTTP, wyszukiwania Identyfikatora z danych trasy](#http-trigger-look-up-id-from-route-data-c)
* [Wyzwalacz HTTP, wyszukiwania Identyfikatora z danych trasy, używając SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP wyzwalacza, Uzyskaj dokumentację wielu, za pomocą SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP wyzwalacza, Uzyskaj dokumentację wielu, za pomocą DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Przykłady dotyczą prostego `ToDoItem` typu:

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

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-c"></a>Wyzwalacz kolejki, odnośnika identyfikator z formatu JSON (C#)

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , który pobiera pojedynczego dokumentu. Funkcja jest wyzwalana przez komunikatu w kolejce, która zawiera obiekt JSON. Wyzwalacz kolejki analizuje dane JSON na obiekt o nazwie `ToDoItemLookup`, który zawiera identyfikator aby wyszukać. Identyfikator służy do pobierania `ToDoItem` dokument z określonej bazy danych i kolekcji.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection", 
                Id = "{ToDoItemId}")]ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId}");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c"></a>Wyzwalacz HTTP, Sprawdź identyfikator z ciągu zapytania (C#)

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , który pobiera pojedynczego dokumentu. Funkcja jest wyzwalana przez żądanie HTTP, która wykorzystuje ciąg kwerendy do określenia Identyfikatora do wyszukania. Identyfikator służy do pobierania `ToDoItem` dokument z określonej bazy danych i kolekcji.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection", 
                Id = "{Query.id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c"></a>Wyzwalacz HTTP, wyszukiwania Identyfikatora z danych trasy (C#)

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , który pobiera pojedynczego dokumentu. Funkcja jest wyzwalana przez żądanie HTTP, że używa kierować dane do określenia Identyfikatora do wyszukania. Identyfikator służy do pobierania `ToDoItem` dokument z określonej bazy danych i kolekcji.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", 
                Route = "todoitems/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection", 
                Id = "{id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>Wyzwalacz HTTP, wyszukiwania Identyfikatora z danych trasy, używając SqlQuery (C#)

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , który pobiera pojedynczego dokumentu. Funkcja jest wyzwalana przez żądanie HTTP, że używa kierować dane do określenia Identyfikatora do wyszukania. Identyfikator służy do pobierania `ToDoItem` dokument z określonej bazy danych i kolekcji. 

W przykładzie pokazano sposób użycia wyrażenia wiązania w `SqlQuery` parametru. Możesz przekazać dane trasy do `SqlQuery` parametru, jak pokazano, ale obecnie [nie można przekazać wartości ciągu zapytania](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).


```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", 
                Route = "todoitems2/{id}")]HttpRequest req,
            [CosmosDB("ToDoItems", "Items", 
                ConnectionStringSetting = "CosmosDBConnection", 
                SqlQuery = "select * from ToDoItems r where r.id = {id}")]
                IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c"></a>HTTP wyzwalacza, Uzyskaj dokumentację wielu, za pomocą SqlQuery (C#)

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , który pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Zapytanie jest określona w `SqlQuery` atrybutu właściwości.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

namespace CosmosDBSamplesV2
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection", 
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}

```

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP wyzwalacza, Uzyskaj dokumentację wielu, za pomocą DocumentClient (C#)

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , który pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Kod używa `DocumentClient` podanego przez powiązanie usługi Azure Cosmos DB można odczytać listy dokumentów wystąpienia. `DocumentClient` Wystąpienia może również służyć do operacji zapisu.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace CosmosDBSamplesV2
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", 
                Route = null)]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.Info($"Searching for: {searchterm}");

            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.Info(result.Description);
                }
            }
            return new OkResult();
        }
    }
}
```

[Pomiń przykłady danych wejściowych](#input---attributes)

### <a name="input---c-script-examples"></a>Dane wejściowe — przykłady skryptów w języku C#

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz kolejki, odnośnika identyfikator ciągu](#queue-trigger-look-up-id-from-string-c-script)
* [Wyzwalacz kolejki, Uzyskaj dokumentację wielu, za pomocą SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Wyzwalacz HTTP, Sprawdź identyfikator z ciągu zapytania](#http-trigger-look-up-id-from-query-string-c-script)
* [Wyzwalacz HTTP, wyszukiwania Identyfikatora z danych trasy](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP wyzwalacza, Uzyskaj dokumentację wielu, za pomocą SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP wyzwalacza, Uzyskaj dokumentację wielu, za pomocą DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Przykłady wyzwalacza HTTP można znaleźć prostego `ToDoItem` typu:

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

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-string-c-script"></a>Wyzwalacz kolejki, odnośnika identyfikator ciągu (skrypt języka C#)

Poniższy przykład pokazuje usługi Cosmos DB, powiązania danych wejściowych w *function.json* pliku i [funkcji skryptu w języku C#](functions-reference-csharp.md) powiązania, który używa. Funkcja odczytuje pojedynczy dokument i aktualizuje wartość tekstu dokumentu.

Oto powiązanie danych w *function.json* pliku:

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connectionStringSetting": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```
[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod skryptu języka C#:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding 
    public static void Run(string myQueueItem, dynamic inputDocument)
    {   
      inputDocument.text = "This has changed.";
    }
```

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>Wyzwalacz kolejki, Uzyskaj dokumentację wielu, za pomocą SqlQuery (skrypt języka C#)

W poniższym przykładzie pokazano powiązania danych wejściowych usługi Azure Cosmos DB w *function.json* pliku i [funkcji skryptu w języku C#](functions-reference-csharp.md) powiązania, który używa. Funkcja pobiera wiele dokumentów, określonych przez zapytanie SQL, przy użyciu wyzwalacza kolejki, aby dostosować parametry zapytania.

Wyzwalacz kolejki zawiera parametr `departmentId`. Komunikat kolejki `{ "departmentId" : "Finance" }` zwróci wszystkie rekordy dla działu finansowego. 

Oto powiązanie danych w *function.json* pliku:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod skryptu języka C#:

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {   
        foreach (var doc in documents)
        {
            // operate on each document
        }    
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c-script"></a>Wyzwalacz HTTP, Sprawdź identyfikator z ciągu zapytania (skrypt języka C#)

W poniższym przykładzie przedstawiono [funkcji skryptu w języku C#](functions-reference-csharp.md) , który pobiera pojedynczego dokumentu. Funkcja jest wyzwalana przez żądanie HTTP, która wykorzystuje ciąg kwerendy do określenia Identyfikatora do wyszukania. Identyfikator służy do pobierania `ToDoItem` dokument z określonej bazy danych i kolekcji.

Oto *function.json* pliku:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

Poniżej przedstawiono kod skryptu języka C#:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c-script"></a>Wyzwalacz HTTP, wyszukiwania Identyfikatora z danych trasy (skrypt języka C#)

W poniższym przykładzie przedstawiono [funkcji skryptu w języku C#](functions-reference-csharp.md) , który pobiera pojedynczego dokumentu. Funkcja jest wyzwalana przez żądanie HTTP, że używa kierować dane do określenia Identyfikatora do wyszukania. Identyfikator służy do pobierania `ToDoItem` dokument z określonej bazy danych i kolekcji.

Oto *function.json* pliku:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Poniżej przedstawiono kod skryptu języka C#:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>HTTP wyzwalacza, Uzyskaj dokumentację wielu, za pomocą SqlQuery (skrypt języka C#)

W poniższym przykładzie przedstawiono [funkcji skryptu w języku C#](functions-reference-csharp.md) , który pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Zapytanie jest określona w `SqlQuery` atrybutu właściwości.

Oto *function.json* pliku:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

Poniżej przedstawiono kod skryptu języka C#:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.Info(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>HTTP wyzwalacza, Uzyskaj dokumentację wielu, za pomocą DocumentClient (skrypt języka C#)

W poniższym przykładzie przedstawiono [funkcji skryptu w języku C#](functions-reference-csharp.md) , który pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Kod używa `DocumentClient` podanego przez powiązanie usługi Azure Cosmos DB można odczytać listy dokumentów wystąpienia. `DocumentClient` Wystąpienia może również służyć do operacji zapisu.

Oto *function.json* pliku:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

Poniżej przedstawiono kod skryptu języka C#:

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.Info(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

[Pomiń przykłady danych wejściowych](#input---attributes)

### <a name="input---javascript-examples"></a>Dane wejściowe — przykłady kodu JavaScript

Ta sekcja zawiera następujące przykłady, które odczytują pojedynczy dokument, określając wartość Identyfikatora z różnych źródeł:

* [Wyzwalacz kolejki, odnośnika identyfikator z formatu JSON](#queue-trigger-look-up-id-from-string-javascript)
* [Wyzwalacz HTTP, Sprawdź identyfikator z ciągu zapytania](#http-trigger-look-up-id-from-query-string-javascript)
* [Wyzwalacz HTTP, wyszukiwania Identyfikatora z danych trasy](#http-trigger-look-up-id-from-route-data-javascript)
* [Wyzwalacz kolejki, Uzyskaj dokumentację wielu, za pomocą SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-javascript"></a>Wyzwalacz kolejki, odnośnika identyfikator z formatu JSON (JavaScript)

Poniższy przykład pokazuje usługi Cosmos DB, powiązania danych wejściowych w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) powiązania, który używa. Funkcja odczytuje pojedynczy dokument i aktualizuje wartość tekstu dokumentu.

Oto powiązanie danych w *function.json* pliku:

```json
{
    "name": "inputDocumentIn",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",     
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```
[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod JavaScript:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {   
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-javascript"></a>Wyzwalacz HTTP, Sprawdź identyfikator z ciągu zapytania (JavaScript)

W poniższym przykładzie przedstawiono [funkcji JavaScript](functions-reference-node.md) , który pobiera pojedynczego dokumentu. Funkcja jest wyzwalana przez żądanie HTTP, która wykorzystuje ciąg kwerendy do określenia Identyfikatora do wyszukania. Identyfikator służy do pobierania `ToDoItem` dokument z określonej bazy danych i kolekcji.

Oto *function.json* pliku:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-javascript"></a>Wyzwalacz HTTP, wyszukiwania Identyfikatora z danych trasy (JavaScript)

W poniższym przykładzie przedstawiono [funkcji JavaScript](functions-reference-node.md) , który pobiera pojedynczego dokumentu. Funkcja jest wyzwalana przez żądanie HTTP, która wykorzystuje ciąg kwerendy do określenia Identyfikatora do wyszukania. Identyfikator służy do pobierania `ToDoItem` dokument z określonej bazy danych i kolekcji.

Oto *function.json* pliku:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>Wyzwalacz kolejki, Uzyskaj dokumentację wielu, za pomocą SqlQuery (JavaScript)

W poniższym przykładzie pokazano powiązania danych wejściowych usługi Azure Cosmos DB w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) powiązania, który używa. Funkcja pobiera wiele dokumentów, określonych przez zapytanie SQL, przy użyciu wyzwalacza kolejki, aby dostosować parametry zapytania.

Wyzwalacz kolejki zawiera parametr `departmentId`. Komunikat kolejki `{ "departmentId" : "Finance" }` zwróci wszystkie rekordy dla działu finansowego. 

Oto powiązanie danych w *function.json* pliku:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod JavaScript:

```javascript
    module.exports = function (context, input) {    
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }       
        context.done();
    };
```

[Pomiń przykłady danych wejściowych](#input---attributes)

<a name="infsharp"></a>

### <a name="input---f-examples"></a>Dane wejściowe — przykłady F #

Poniższy przykład pokazuje usługi Cosmos DB, powiązania danych wejściowych w *function.json* pliku i [funkcja języka F #](functions-reference-fsharp.md) powiązania, który używa. Funkcja odczytuje pojedynczy dokument i aktualizuje wartość tekstu dokumentu.

Oto powiązanie danych w *function.json* pliku:

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "connectionStringSetting": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```

[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod F #:

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

W tym przykładzie wymaga `project.json` pliku, który określa `FSharp.Interop.Dynamic` i `Dynamitey` zależności NuGet:

```json
{
    "frameworks": {
        "net46": {
            "dependencies": {
                "Dynamitey": "1.0.2",
                "FSharp.Interop.Dynamic": "3.0.0"
            }
        }
    }
}
```

Aby dodać `project.json` plików, zobacz [zarządzania pakietami języka F #](functions-reference-fsharp.md#package).

### <a name="input---java-examples"></a>Dane wejściowe - przykładów w języku Java

Poniższy przykład przedstawia funkcję języka Java, która pobiera pojedynczego dokumentu. Funkcja jest wyzwalana przez żądanie HTTP, które wykorzystuje ciąg kwerendy do określenia Identyfikatora do wyszukania. Ten identyfikator służy do pobierania dokumentu ToDoItem z określonej bazy danych i kolekcji.

Oto kodu Java:

```java
@FunctionName("getItem")
public String cosmosDbQueryById(
    @HttpTrigger(name = "req",
                  methods = {HttpMethod.GET},
                  authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> dummy,
    @CosmosDBInput(name = "database",
                      databaseName = "ToDoList",
                      collectionName = "Items",
                      leaseCollectionName = "",
                      id = "{Query.id}"
                      connectionStringSetting = "AzureCosmosDBConnection") Optional<String> item,
    final ExecutionContext context
 ) {
    return item.orElse("Not found");
 }
 ```

W [Java funkcje biblioteki środowiska uruchomieniowego](/java/api/overview/azure/functions/runtime), użyj `@CosmosDBInput` adnotacja dla parametrów funkcji, którego wartość może pochodzić z usługi Cosmos DB.  Ta adnotacja mogą być używane z natywnych typów języka Java, obiektów typu Pojo lub wartości dopuszczających wartości null przy użyciu opcjonalnie<T>. 

## <a name="input---attributes"></a>Dane wejściowe — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) atrybutu.

Konstruktor atrybutu ma nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i inne właściwości, które można skonfigurować, zobacz [w poniższej sekcji konfiguracji](#input---configuration). 

## <a name="input---configuration"></a>Dane wejściowe — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `CosmosDB` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type**     || Musi być równa `cosmosDB`.        |
|**direction**     || Musi być równa `in`.         |
|**Nazwa**     || Nazwa parametru powiązania, który reprezentuje dokument w funkcji.  |
|**databaseName** |**DatabaseName** |Baza danych zawiera dokument.        |
|**collectionName** |**collectionName** | Nazwa kolekcji, która zawiera dokument. |
|**id**    | **Identyfikator** | Identyfikator dokumentu do pobrania. Ta właściwość obsługuje [powiązania wyrażeń](functions-triggers-bindings.md#binding-expressions-and-patterns). Nie należy ustawiać zarówno **identyfikator** i **sqlQuery** właściwości. Jeśli nie ustawisz pojedynczo, zostanie pobrana całą kolekcję. |
|**sqlQuery**  |**SqlQuery**  | Zapytania SQL usługi Azure Cosmos DB używane do pobierania wiele dokumentów. Właściwość obsługuje powiązań środowiska uruchomieniowego, jak w poniższym przykładzie: `SELECT * FROM c where c.departmentId = {departmentId}`. Nie należy ustawiać zarówno **identyfikator** i **sqlQuery** właściwości. Jeśli nie ustawisz pojedynczo, zostanie pobrana całą kolekcję.|
|**connectionStringSetting**     |**ConnectionStringSetting**|Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi Azure Cosmos DB.        |
|**właściwości partitionKey**|**właściwości partitionKey**|Określa wartość klucza partycji do wyszukiwania. Może zawierać parametrów wiązania.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Dane wejściowe — użycie

W języku C# i F # funkcji Jeśli funkcja kończy się pomyślnie, wszelkie zmiany wprowadzone do dokument wejściowy za pośrednictwem nazwanych parametrów wejściowych automatycznie są zachowywane. 

W funkcji języka JavaScript nie zostało zaktualizowane automatycznie po wyjściu z funkcji. Zamiast tego należy użyć `context.bindings.<documentName>In` i `context.bindings.<documentName>Out` Aby wprowadzić aktualizacje. Zobacz [przykład JavaScript](#input---javascript-example).

## <a name="output"></a>Dane wyjściowe

Dane wyjściowe usługi Azure Cosmos DB, powiązania pozwala zapisać nowy dokument z bazą danych Azure Cosmos DB przy użyciu interfejsu API SQL. 

## <a name="output---examples"></a>Dane wyjściowe — przykłady

Zobacz przykłady specyficzny dla języka:

* [C#](#output---c-examples)
* [Skryptu C# (csx)](#output---c-script-examples)
* [JavaScript](#output---javascript-examples)
* [F#](#output---f-examples)
* [Java](#output---java-example)

Zobacz też [wejściowych przykład](#input---c-examples) , który używa `DocumentClient`.

[Pomiń przykładowe dane wyjściowe](#output---attributes)

### <a name="ouput---c-examples"></a>Dane wyjściowe — przykłady w języku C#

Ta sekcja zawiera następujące przykłady:

* Wyzwalacz kolejki, doc jeden zapisu
* Wyzwalacz kolejki, dokumentacja zapisu przy użyciu IAsyncCollector

Przykłady dotyczą prostego `ToDoItem` typu:

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

[Pomiń przykładowe dane wyjściowe](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c"></a>Wyzwalacz kolejki, doc jeden zapis (C#)

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , dodanie dokumentu do bazy danych przy użyciu danych podany w wiadomości z usługi Queue storage.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
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
            TraceWriter log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.Info($"C# Queue trigger function inserted one row");
            log.Info($"Description={queueMessage}");
        }
    }
}
```

[Pomiń przykładowe dane wyjściowe](#output---attributes)

#### <a name="queue-trigger-write-docs-using-iasynccollector-c"></a>Wyzwalacz kolejki, dokumentacja zapisu przy użyciu IAsyncCollector (C#)

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) kolekcji dokumentów, który dodaje w bazie danych, przy użyciu danych w komunikatu w kolejce JSON.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;

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
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.Info($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

[Pomiń przykładowe dane wyjściowe](#output---attributes)

### <a name="output---c-script-examples"></a>Dane wyjściowe — przykłady skryptów w języku C#

Ta sekcja zawiera następujące przykłady:

* Wyzwalacz kolejki, doc jeden zapisu
* Wyzwalacz kolejki, dokumentacja zapisu przy użyciu IAsyncCollector

[Pomiń przykładowe dane wyjściowe](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c-script"></a>Wyzwalacz kolejki, doc jeden zapisu (skrypt języka C#)

W poniższym przykładzie pokazano powiązania w danych wyjściowych usługi Azure Cosmos DB *function.json* pliku i [funkcji skryptu w języku C#](functions-reference-csharp.md) powiązania, który używa. Funkcja używa powiązania danych wejściowych w kolejce dla kolejki, która odbiera JSON w następującym formacie:

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

Oto powiązanie danych w *function.json* pliku:

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

[Konfiguracji](#output---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod skryptu języka C#:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
      log.Info($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

#### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wyzwalacz kolejki, dokumentacja zapisu przy użyciu IAsyncCollector

Aby utworzyć wiele dokumentów, można powiązać `ICollector<T>` lub `IAsyncCollector<T>` gdzie `T` jest jednym z obsługiwanych typów.

Ten przykład dotyczy prostego `ToDoItem` typu:

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

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.Info($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

[Pomiń przykładowe dane wyjściowe](#output---attributes)

### <a name="output---javascript-examples"></a>Dane wyjściowe — przykłady kodu JavaScript

W poniższym przykładzie pokazano powiązania w danych wyjściowych usługi Azure Cosmos DB *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) powiązania, który używa. Funkcja używa powiązania danych wejściowych w kolejce dla kolejki, która odbiera JSON w następującym formacie:

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

Oto powiązanie danych w *function.json* pliku:

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

[Konfiguracji](#output---configuration) sekcji opisano te właściwości.

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

[Pomiń przykładowe dane wyjściowe](#output---attributes)

### <a name="output---f-examples"></a>Dane wyjściowe — przykłady F #

W poniższym przykładzie pokazano powiązania w danych wyjściowych usługi Azure Cosmos DB *function.json* pliku i [funkcja języka F #](functions-reference-fsharp.md) powiązania, który używa. Funkcja używa powiązania danych wejściowych w kolejce dla kolejki, która odbiera JSON w następującym formacie:

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

Oto powiązanie danych w *function.json* pliku:

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
[Konfiguracji](#output---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod F #:

```fsharp
    open FSharp.Interop.Dynamic
    open Newtonsoft.Json

    type Employee = {
      id: string
      name: string
      employeeId: string
      address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
      log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
      let employee = JObject.Parse(myQueueItem)
      employeeDocument <-
        { id = sprintf "%s-%s" employee?name employee?employeeId
          name = employee?name
          employeeId = employee?employeeId
          address = employee?address }
```

W tym przykładzie wymaga `project.json` pliku, który określa `FSharp.Interop.Dynamic` i `Dynamitey` zależności NuGet:

```json
{
    "frameworks": {
        "net46": {
          "dependencies": {
            "Dynamitey": "1.0.2",
            "FSharp.Interop.Dynamic": "3.0.0"
           }
        }
    }
}
```

Aby dodać `project.json` plików, zobacz [zarządzania pakietami języka F #](functions-reference-fsharp.md#package).

## <a name="output---java-examples"></a>Dane wyjściowe — przykładów w języku Java

Funkcja języka Java, która dodaje dokumentu do bazy danych przy użyciu danych z wiadomości w usłudze Queue storage można znaleźć w poniższym przykładzie.

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database", databaseName = "ToDoList", collectionName = "Items", connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
     @QueueTrigger(name = "msg", queueName = "myqueue-items", connection = "AzureWebJobsStorage") String message,
     final ExecutionContext context
)  {
     return "{ id: " + System.currentTimeMillis() + ", Description: " + message + " }";
   }
```

W [Java funkcje biblioteki środowiska uruchomieniowego](/java/api/overview/azure/functions/runtime), użyj `@CosmosDBOutput` adnotacji w parametrach, które zostaną zapisane w bazie danych Cosmos DB.  Typ parametru adnotacji powinny być OutputBinding<T>, gdzie T jest typem natywnym Java lub obiektu typu POJO.


## <a name="output---attributes"></a>Dane wyjściowe — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) atrybutu.

Konstruktor atrybutu ma nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i inne właściwości, które można skonfigurować, zobacz [dane wyjściowe — Konfiguracja](#output---configuration). Oto `CosmosDB` przykład atrybutu w podpisie metody:

```csharp
    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Aby uzyskać kompletny przykład, zobacz [dane wyjściowe — przykład w języku C#](#output---c-example).

## <a name="output---configuration"></a>Dane wyjściowe — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `CosmosDB` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type**     || Musi być równa `cosmosDB`.        |
|**direction**     || Musi być równa `out`.         |
|**Nazwa**     || Nazwa parametru powiązania, który reprezentuje dokument w funkcji.  |
|**databaseName** | **DatabaseName**|Baza danych, zawierający kolekcję, w którym zostanie utworzona dokumentu.     |
|**collectionName** |**collectionName**  | Nazwa kolekcji jest tworzona dokumentu. |
|**createIfNotExists**  |**createIfNotExists**    | Wartość logiczna, aby wskazać, czy kolekcja jest tworzona, gdy nie istnieje. Wartość domyślna to *false* ponieważ nowych kolekcji są tworzone z zarezerwowaną przepływnością, co ma koszt skutki. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**właściwości partitionKey**|**właściwości partitionKey** |Gdy `CreateIfNotExists` ma wartość true, określa ścieżkę klucza partycji dla utworzonej kolekcji.|
|**collectionThroughput**|**collectionThroughput**| Gdy `CreateIfNotExists` ma wartość true, określa [przepływności](../cosmos-db/set-throughput.md) utworzonej kolekcji.|
|**connectionStringSetting**    |**ConnectionStringSetting** |Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Dane wyjściowe — użycie

Domyślnie podczas zapisu do parametru wyjściowego w funkcji, tworzony jest dokument w bazie danych. Ten dokument ma automatycznie generowanym identyfikatorze GUID jako identyfikatora dokumentu. Identyfikator dokumentu dokumentu wyjściowego można określić, podając `id` właściwości w obiekcie JSON przekazanego do parametru wyjściowego. 

> [!Note]  
> Po określeniu identyfikator istniejącego dokumentu, pobiera to zastąpione przez nowy dokument danych wyjściowych. 

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie | Informacje ogólne |
|---|---|
| CosmosDB | [Kody błędów CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej o bazie danych bez użycia serwera, obliczeń w usłudze Cosmos DB](..\cosmos-db\serverless-computing-database.md)
* [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
