---
title: Wykonywanie operacji zbiorczych w interfejsie API języka Gremlin dla usługi Azure Cosmos DB przy użyciu biblioteki grafów BulkExecutor .NET
description: Dowiedz się, jak korzystać z biblioteki BulkExecutor do masowego importowania danych grafów do kontenera interfejsu API programu Gremlin w usłudze Azure Cosmos DB.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: lbosq
ms.reviewer: sngun
ms.openlocfilehash: 2f949265e3961794e2fc4b0efbce107762a75ef7
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54041564"
---
# <a name="using-the-graph-bulkexecutor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>Wykonywanie operacji zbiorczych w interfejsie API języka Gremlin dla usługi Azure Cosmos DB przy użyciu biblioteki grafów BulkExecutor .NET

Ten samouczek zawiera instrukcje dotyczące importowania i aktualizowania obiektów grafów w kontenerze interfejsu API języka Gremlin w usłudze Azure Cosmos DB przy użyciu biblioteki BulkExecutor .NET dla usługi Azure Cosmos DB. W tym procesie klasa Graph w [bibliotece BulkExecutor](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) jest używana w celu programistycznego utworzenia obiektów Vertex i Edge, a następnie wstawienia wielu takich obiektów w ramach jednego żądania sieciowego. To działanie jest konfigurowane za pomocą biblioteki BulkExecutor w celu optymalnego wykorzystania zasobów zarówno bazy danych, jak i pamięci lokalnej.

Zamiast wysyłać zapytania w języku Gremlin do bazy danych, gdzie polecenia są oceniane, a następnie wykonywane pojedynczo, można użyć biblioteki BulkExecutor, co będzie się wiązało z utworzeniem i zwalidowaniem obiektów lokalnie. Po utworzeniu obiektów ta biblioteka umożliwia sekwencyjne wysłanie obiektów grafów do usługi bazy danych. Dzięki tej metodzie pozyskiwanie danych może być nawet stukrotnie szybsze, dzięki czemu jest to idealna metoda do początkowej migracji danych lub okresowych operacji przenoszenia danych. Dowiedz się więcej na stronie [aplikacji przykładowej Azure Cosmos DB Graph BulkExecutor](https://aka.ms/graph-bulkexecutor-sample) w usłudze GitHub.

## <a name="bulk-operations-with-graph-data"></a>Wykonywanie operacji zbiorczych z użyciem danych grafów

[Biblioteka BulkExecutor](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet) zawiera przestrzeń nazw `Microsoft.Azure.CosmosDB.BulkExecutor.Graph`, która umożliwia tworzenie i importowanie obiektów grafów. 

Następująca procedura przedstawia proces migracji danych w przypadku kontenera interfejsu API języka Gremlin:
1. Pobierz rekordy ze źródła danych.
2. Utwórz obiekty `GremlinVertex` i `GremlinEdge` z uzyskanych rekordów i dodaj je do struktury danych `IEnumerable`. W tej części aplikacji należy wdrożyć logikę wykrywania i dodawania relacji, w razie gdyby źródło danych nie było bazą danych grafów.
3. Użyj [metody Graph BulkImportAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync?view=azure-dotnet), aby wstawić obiekty grafów do kolekcji.

Ten mechanizm zapewnia wyższą wydajność migracji danych w porównaniu z użyciem klienta języka Gremlin. To ulepszenie jest możliwe, ponieważ wstawianie danych przy użyciu języka Gremlin wymaga od aplikacji wysyłania zapytań pojedynczo, po czym każde z zapytań musi zostać zwalidowane, ocenione, a następnie wykonane w celu utworzenia danych. Biblioteka BulkExecutor obsługuje walidację w aplikacji i wysyła wiele obiektów grafów jednocześnie w ramach każdego żądania sieciowego.

### <a name="creating-vertices-and-edges"></a>Tworzenie wierzchołków i krawędzi

Obiekt `GraphBulkExecutor` udostępnia metodę `BulkImportAsync`, która wymaga listy `IEnumerable` obiektów `GremlinVertex` lub `GremlinEdge` zdefiniowanych w przestrzeni nazw `Microsoft.Azure.CosmosDB.BulkExecutor.Graph.Element`. W tym przykładzie rozdzielono krawędzie i wierzchołki na dwa zadania importu biblioteki BulkExecutor. Zapoznaj się z poniższym przykładem:

```csharp

IBulkExecutor graphbulkExecutor = new GraphBulkExecutor(documentClient, targetCollection);

BulkImportResponse vResponse = null;
BulkImportResponse eResponse = null;

try
{
    // Import a list of GremlinVertex objects
    vResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateVertices(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);

    // Import a list of GremlinEdge objects
    eResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateEdges(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);
}
catch (DocumentClientException de)
{
    Trace.TraceError("Document client exception: {0}", de);
}
catch (Exception e)
{
    Trace.TraceError("Exception: {0}", e);
}
```

Aby uzyskać więcej informacji na temat parametrów biblioteki BulkExecutor, zobacz temat [BulkImportData to Azure Cosmos DB (Zbiorcze importowanie danych do usługi Azure Cosmos DB)](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#bulk-import-data-to-azure-cosmos-db).

Ładunek musi zostać utworzony w obiektach `GremlinVertex` i `GremlinEdge`. Oto, w jaki sposób można utworzyć te obiekty:

**Wierzchołki**:
```csharp
// Creating a vertex
GremlinVertex v = new GremlinVertex(
    "vertexId",
    "vertexLabel");

// Adding custom properties to the vertex
v.AddProperty("customProperty", "value");

// Partitioning keys must be specified for all vertices
v.AddProperty("partitioningKey", "value");
```

**Krawędzie**:
```csharp
// Creating an edge
GremlinEdge e = new GremlinEdge(
    "edgeId",
    "edgeLabel",
    "targetVertexId",
    "sourceVertexId",
    "targetVertexLabel",
    "sourceVertexLabel",
    "targetVertexPartitioningKey",
    "sourceVertexPartitioningKey");

// Adding custom properties to the edge
e.AddProperty("customProperty", "value");
```

> [!NOTE]
> Narzędzie BulkExecutor przed dodaniem krawędzi nie sprawdza automatycznie, czy istnieją wierzchołki. Przed uruchomieniem zadań BulkImport należy przeprowadzić odpowiednią walidację w aplikacji.

## <a name="sample-application"></a>Przykładowa aplikacja

### <a name="prerequisites"></a>Wymagania wstępne
* Program Visual Studio 2017 z pakietem roboczym Programowanie na platformie Azure. Pracę możesz rozpocząć bezpłatnie w programie [Visual Studio 2017 Community Edition](https://visualstudio.microsoft.com/downloads/).
* Subskrypcja platformy Azure. [Bezpłatne konto platformy Azure możesz utworzyć tutaj](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db). Możesz również utworzyć konto bazy danych Cosmos DB dzięki ofercie [Wypróbuj usługę Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure.
* Baza danych interfejsu API języka Gremlin w usłudze Azure Cosmos DB z **nieograniczoną kolekcją**. W tym przewodniku pokazano, jak rozpocząć pracę z [interfejsem API języka Gremlin w usłudze Azure Cosmos DB na platformie .NET](https://docs.microsoft.com/azure/cosmos-db/create-graph-dotnet).
* Usługa Git. Aby uzyskać więcej informacji, zapoznaj się ze [stroną plików do pobrania usługi Git](https://git-scm.com/downloads).

### <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji
Ten samouczek przedstawia czynności wymagane w celu rozpoczęcia pracy przy użyciu [przykładowej aplikacji Graph BulkExecutor dla usługi Azure Cosmos DB](https://aka.ms/graph-bulkexecutor-sample) hostowanej w usłudze GitHub. Ta aplikacja składa się z rozwiązania platformy .NET, które losowo generuje obiekty krawędzi i wierzchołków, a następnie wykonuje zbiorcze wstawienia do określonego konta bazy danych grafów. Aby uzyskać aplikację, uruchom poniższe polecenie `git clone`:

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

To repozytorium zawiera przykładową aplikację GraphBulkExecutor z następującymi plikami:

Plik|Opis
---|---
`App.config`|Tutaj są określane parametry aplikacji i bazy danych. Ten plik powinien być zmodyfikowany w pierwszej kolejności, aby nawiązać połączenie z docelową bazą danych i kolekcjami.
`Program.cs`| Ten plik zawiera logikę tworzenia kolekcji `DocumentClient`, obsługi oczyszczania i wysyłania żądań BulkExecutor.
`Util.cs`| Ten plik zawiera klasę pomocnika, która z kolei zawiera logikę generowania danych testowych i sprawdzania, czy baza danych i kolekcje istnieją.

W pliku `App.config` następujące elementy to wartości konfiguracji, które można określić:

Ustawienie|Opis
---|---
`EndPointUrl`|Jest to **punkt końcowy zestawu SDK platformy .NET**, który można znaleźć w bloku Przegląd Twojego konta bazy danych interfejsu API języka Gremlin w usłudze Azure Cosmos DB. Ma następujący format: `https://your-graph-database-account.documents.azure.com:443/`
`AuthorizationKey`|Jest to klucz podstawowy lub pomocniczy wymieniony w obszarze konta usługi Azure Cosmos DB. Dowiedz się więcej na temat [zabezpieczania dostępu do danych usługi Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#master-keys)
`DatabaseName`, `CollectionName`|Są to **nazwy docelowej bazy danych i kolekcji**. Jeśli dla ustawienia `ShouldCleanupOnStart` zostanie podana wartość `true`, te wartości wraz z ustawieniem `CollectionThroughput` zostaną użyte do usunięcia bazy danych i kolekcji, a następnie utworzenia nowych. Podobnie w przypadku podania dla ustawienia `ShouldCleanupOnFinish` wartości `true` zostaną one użyte do usunięcia bazy danych zaraz po zakończeniu pozyskiwania. Pamiętaj, że kolekcja docelowa musi być **nieograniczoną kolekcją**.
`CollectionThroughput`|Ta wartość jest używana do utworzenia nowej kolekcji, jeśli opcja `ShouldCleanupOnStart` jest ustawiona na wartość `true`.
`ShouldCleanupOnStart`|To ustawienie spowoduje usunięcie konta bazy danych i kolekcji przed uruchomieniem programu, a następnie utworzenie nowych przy użyciu wartości `DatabaseName`, `CollectionName` i `CollectionThroughput`.
`ShouldCleanupOnFinish`|To ustawienie spowoduje usunięcie konta bazy danych i kolekcji z określonymi wartościami `DatabaseName` i `CollectionName` po uruchomieniu programu.
`NumberOfDocumentsToImport`|To ustawienie określa liczbę testowych wierzchołków i krawędzi, które zostaną wygenerowane w tym przykładzie. Ta liczba będzie dotyczyć zarówno wierzchołków, jak i krawędzi.
`NumberOfBatches`|To ustawienie określa liczbę testowych wierzchołków i krawędzi, które zostaną wygenerowane w tym przykładzie. Ta liczba będzie dotyczyć zarówno wierzchołków, jak i krawędzi.
`CollectionPartitionKey`|To ustawienie zostanie użyte do utworzenia testowych wierzchołków i krawędzi, a ta właściwość będzie przypisana automatycznie. Zostanie również użyte podczas ponownego tworzenia bazy danych i kolekcji w przypadku ustawienia opcji `ShouldCleanupOnStart` na wartość `true`.

### <a name="run-the-sample-application"></a>Uruchamianie przykładowej aplikacji

1. Dodaj szczegółowe parametry konfiguracji bazy danych w pliku `App.config`. Zostaną one użyte do utworzenia wystąpienia klasy DocumentClient. Jeśli baza danych i kontener nie zostały jeszcze utworzone, zostaną utworzone automatycznie.
2. Uruchom aplikację. Spowoduje to dwukrotne wywołanie polecenia `BulkImportAsync` — raz w celu zaimportowania wierzchołków i raz w celu zaimportowania krawędzi. Jeśli dowolny z obiektów spowoduje błąd podczas wstawiania, zostanie dodany do pliku `.\BadVertices.txt` lub `.\BadEdges.txt`.
3. Oceń wyniki, wysyłając zapytania do bazy danych grafów. Jeśli opcja `ShouldCleanupOnFinish` ma wartość „true”, baza danych zostanie automatycznie usunięta.

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać szczegółowe informacje na temat pakietu Nuget i wersji biblioteki Bulk Executor .NET, zobacz [szczegóły zestawu SDK Bulk Executor](sql-api-sdk-bulk-executor-dot-net.md). 
* Zapoznaj się z [poradami dotyczącymi wydajności](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#performance-tips), aby jeszcze lepiej zoptymalizować użycie narzędzia BulkExecutor.
* Zapoznaj się z artykułem informacyjnym na temat narzędzia [BulkExecutor.Graph](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet), aby uzyskać szczegółowe informacje na temat klas i metod zdefiniowanych w tej przestrzeni nazw.
