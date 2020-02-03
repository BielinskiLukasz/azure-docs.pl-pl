---
title: Przykłady środowiska Node. js do zarządzania danymi w usłudze Azure Cosmos Database
description: Znajdź w witrynie GitHub przykłady kodu Node.js służące do wykonywania typowych zadań w usłudze Azure Cosmos DB, w tym operacji CRUD.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 08/23/2019
ms.author: dech
ms.openlocfilehash: 4ac3d74cb76a936cc88261a2fedf16764e8ae52f
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718297"
---
# <a name="nodejs-examples-to-manage-data-in-azure-cosmos-db"></a>Przykłady środowiska Node. js do zarządzania danymi w Azure Cosmos DB

> [!div class="op_single_selector"]
> * [Przykłady zestawu SDK dla platformy .NET V2](sql-api-dotnet-samples.md)
> * [Przykłady zestawu SDK dla platformy .NET v3](sql-api-dotnet-v3sdk-samples.md)
> * [Przykłady kodu Java](sql-api-java-samples.md)
> * [Przykłady asynchronicznego kodu Java](sql-api-async-java-samples.md)
> * [Przykłady dla platformy Node.js](sql-api-nodejs-samples.md)
> * [Przykłady kodu Python](sql-api-python-samples.md)
> * [Galeria przykładów kodu dla platformy Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Przykładowe rozwiązania do wykonywania operacji CRUD i innych typowych działań na zasobach usługi Azure Cosmos DB można znaleźć w repozytorium [azure-cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples) w witrynie GitHub. Ten artykuł zawiera:

* Linki do zadań w poszczególnych przykładowych plikach projektów platformy Node.js.
* Linki do powiązanej dokumentacji interfejsu API.

**Wymagania wstępne**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Możesz [aktywować korzyści subskrybenta programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): w ramach subskrypcji programu Visual Studio co miesiąc otrzymasz środki, które można przeznaczyć na płatne usługi platformy Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Potrzebujesz również [zestawu SDK dla języka JavaScript](sql-api-sdk-node.md).
   
   > [!NOTE]
   > Przykłady są niezależne — każdy z nich jest automatycznie konfigurowany i automatycznie czyszczony po użyciu. Przykłady te obejmują wiele wywołań elementu [Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest). W każdej takiej sytuacji w subskrypcji będzie naliczana opłata za 1 godzinę użycia na warstwę wydajności tworzonego kontenera.
   > 
   > 

## <a name="database-examples"></a>Przykłady dotyczące baz danych

Plik [DatabaseManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts) pokazuje, jak wykonać operacje CRUD w bazie danych. Aby dowiedzieć się więcej na temat baz danych usługi Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [Praca z bazami danych, kontenerami i elementami](databases-containers-items.md) . 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie bazy danych, jeśli nie istnieje](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L12-L14) |[Databases.createIfNotExists](/javascript/api/@azure/cosmos/databases?view=azure-node-latest#createifnotexists-databaserequest--requestoptions-) |
| [Wyświetlanie listy baz danych na koncie](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L16-L18) |[Databases.readAll](/javascript/api/@azure/cosmos/databases?view=azure-node-latest#readall-feedoptions-) |
| [Odczytywanie bazy danych na podstawie identyfikatora](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L20-L29) |[Database.read](/javascript/api/@azure/cosmos/database?view=azure-node-latest#read-requestoptions-) |
| [Usuwanie bazy danych](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L31-L32) |[Database.delete](/javascript/api/@azure/cosmos/database?view=azure-node-latest#delete-requestoptions-) |

## <a name="container-examples"></a>Przykłady dotyczące kontenerów

Plik [ContainerManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts) pokazuje, jak wykonywać operacje CRUD w kontenerze. Aby dowiedzieć się więcej na temat kolekcji usługi Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [Praca z bazami danych, kontenerami i elementami](databases-containers-items.md) . 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie kontenera, jeśli nie istnieje](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L14-L15) |[Containers.createIfNotExists](/javascript/api/@azure/cosmos/containers?view=azure-node-latest#createifnotexists-containerrequest--requestoptions-) |
| [Wyświetlanie listy kontenerów dla konta](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L17-L21) |[Containers.readAll](/javascript/api/@azure/cosmos/containers?view=azure-node-latest#readall-feedoptions-) |
| [Odczytaj definicję kontenera](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L23-L26) |[Container.read](/javascript/api/@azure/cosmos/container?view=azure-node-latest#read-requestoptions-) |
| [Usuwanie kontenera](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L28-L30) |[Container.delete](/javascript/api/@azure/cosmos/container?view=azure-node-latest#delete-requestoptions-) |

## <a name="item-examples"></a>Przykłady dotyczące elementów

Plik [ItemManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts) pokazuje, jak wykonać operacje CRUD na elemencie. Aby dowiedzieć się więcej o dokumentach usługi Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [Praca z bazami danych, kontenerami i elementami](databases-containers-items.md) . 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie elementów](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L18-L21) |[Items.create](/javascript/api/@azure/cosmos/items?view=azure-node-latest#create-t--requestoptions-) |
| [Odczytywanie wszystkich elementów w kontenerze](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L23-L28) |[Items.readAll](/javascript/api/@azure/cosmos/items?view=azure-node-latest#readall-feedoptions-) |
| [Odczytywanie elementów na podstawie Identyfikatora](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L30-L33) |[Item.read](/javascript/api/@azure/cosmos/item?view=azure-node-latest#read-requestoptions-) |
| [Odczytywanie elementów tylko wtedy, gdy zostały zmienione](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L45-L56) |[Item.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Wykonywanie zapytań dotyczących dokumentów](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79) |[Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Zastępowanie elementu](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L81-L96) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |
| [Zastępowanie elementu z warunkowym sprawdzaniem elementu ETag](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L98-L135) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Usuwanie elementu](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L137-L140) |[Item.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |

## <a name="indexing-examples"></a>Przykłady dotyczące indeksowania

Plik [IndexManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts) pokazuje, jak zarządzać indeksowanie. Aby dowiedzieć się więcej na temat indeksowania Azure Cosmos DB przed uruchomieniem poniższych przykładów, zobacz [zasady indeksowania](index-policy.md), [typy indeksowania](index-types.md)i [ścieżki indeksowania](index-paths.md) artykuły koncepcyjne. 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Ręczne indeksowanie określonego elementu](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L52-L75) |[RequestOptions.indexingDirective: „include”](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Ręcznie Wyklucz określony element z indeksu](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L17-L29) |[RequestOptions.indexingDirective: „exclude”](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Wyłączanie ścieżki z indeksu](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L142-L167) |[IndexingPolicy.ExcludedPath](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest#excludedpaths) |
| [Tworzenie indeksu zakresu na ścieżce ciągu](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L87-L112) |[IndexKind.Range](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexkind?view=azure-node-latest), [IndexingPolicy](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest), [Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Tworzenie kontenera z domyślnymi zasadami indexPolicy, a następnie aktualizowanie w trybie online](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L13-L15) |[Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest)

## <a name="server-side-programming-examples"></a>Przykłady programowania po stronie serwera

Plik [index. TS](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) projektu [ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat programowania po stronie serwera w Azure Cosmos DB przed uruchomieniem poniższych przykładów, zobacz artykuł dotyczący [procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika](stored-procedures-triggers-udfs.md) . 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie procedury składowanej](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/upsert.js) |[StoredProcedures.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedures?view=azure-node-latest) |
| [Wykonywanie procedury składowanej](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) |[StoredProcedure.execute](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedure?view=azure-node-latest) |

Aby uzyskać więcej informacji na temat programowania po stronie serwera, zobacz [Azure Cosmos DB server-side programming: Stored procedures, database triggers, and UDF (Programowanie po stronie serwera w usłudze Azure Cosmos DB: procedury składowane, wyzwalacze bazy danych i funkcje UDF)](stored-procedures-triggers-udfs.md).

