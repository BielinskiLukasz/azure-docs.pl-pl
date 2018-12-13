---
title: Jak wykonywać zapytania względem danych tabeli w usłudze Azure Cosmos DB?
description: Dowiedz się, jak wykonywać zapytania względem danych tabeli w usłudze Azure Cosmos DB
services: cosmos-db
author: kanshiG
ms.author: govindk
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/15/2017
ms.openlocfilehash: 3d50e51a0d4ccbbd9b21cda87dc457ea1cbe02fd
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52879200"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-table-api"></a>Samouczek: Wykonywanie zapytań w usłudze Azure Cosmos DB przy użyciu interfejsu API tabel

[Interfejs API tabel](table-introduction.md) usługi Azure Cosmos DB obsługuje zapytania OData i [LINQ](https://docs.microsoft.com/rest/api/storageservices/fileservices/writing-linq-queries-against-the-table-service) dla danych klucz-wartość (tabeli).  

W tym artykule opisano następujące zadania: 

> [!div class="checklist"]
> * Wykonywanie zapytania o dane przy użyciu interfejsu API tabel

Zapytania w tym artykule korzystają z następującej przykładowej tabeli `People`:

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0101 |
| Smith | Ben | Ben@contoso.com| 425-555-0102 |
| Smith | Jan | Jeff@contoso.com| 425-555-0104 | 

Zobacz [Wykonywanie zapytań względem tabel i jednostek](https://docs.microsoft.com/rest/api/storageservices/fileservices/querying-tables-and-entities), aby uzyskać szczegółowe informacje na temat wykonywania zapytań przy użyciu interfejsu API tabel. 

Aby uzyskać więcej informacji na temat funkcji premium oferowanych przez usługę Azure Cosmos DB, zobacz [Interfejs API tabel usługi Azure Cosmos DB](table-introduction.md) i [Opracowywanie zawartości przy użyciu interfejsu API tabel na platformie .NET](tutorial-develop-table-dotnet.md). 

## <a name="prerequisites"></a>Wymagania wstępne

Aby te zapytania działały, musisz mieć konto usługi Azure Cosmos DB i mieć dane jednostki w kontenerze. Nie spełniasz tych warunków? Ukończ [pięciominutowy przewodnik Szybki start](create-table-dotnet.md) lub [samouczek dewelopera](tutorial-develop-table-dotnet.md), aby utworzyć konto i wypełnić bazę danych.

## <a name="query-on-partitionkey-and-rowkey"></a>Zapytanie dotyczące właściwości PartitionKey i RowKey
Ponieważ właściwości PartitionKey i RowKey tworzą klucz podstawowy jednostki, do zidentyfikowania jednostki można użyć następującej specjalnej składni: 

**Zapytanie**

```
https://<mytableendpoint>/People(PartitionKey='Harp',RowKey='Walter')  
```
**Results**

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0104 |

Można także określić te właściwości jako część opcji `$filter`, jak pokazano w poniższej sekcji. Należy pamiętać, że w nazwach właściwości kluczy i wartościach stałych jest rozróżniana wielkość liter. Właściwości PartitionKey i RowKey są typu String. 

## <a name="query-by-using-an-odata-filter"></a>Wykonywanie zapytań przy użyciu filtru OData
Podczas tworzenia ciągu filtru należy pamiętać o następujących regułach: 

* Aby porównać właściwość z wartością, użyj operatorów logicznych określonych w specyfikacji protokołu OData. Pamiętaj, że nie można porównać właściwości z wartością dynamiczną. Jedna strona wyrażenia musi być stałą. 
* Nazwa właściwości, operator i wartość stała muszą być oddzielone spacjami zakodowanymi w adresie URL. Spacja jest zakodowana w adresie URL jako `%20`. 
* We wszystkich częściach ciągu filtru jest rozróżniana wielkość liter. 
* Wartość stała musi mieć ten sam typ danych co właściwość, aby filtr zwracał prawidłowe wyniki. Aby uzyskać szczegółowe informacje na temat obsługiwanych typów właściwości, zobacz [Omówienie modelu danych usługi Table service](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). 

Poniżej przedstawiono przykładowe zapytanie, które pokazuje sposób filtrowania według właściwości PartitionKey i Email przy użyciu elementu `$filter` OData.

**Zapytanie**

```
https://<mytableapi-endpoint>/People()?$filter=PartitionKey%20eq%20'Smith'%20and%20Email%20eq%20'Ben@contoso.com'
```

Aby uzyskać więcej informacji na temat sposobu tworzenia wyrażenia filtru dla różnych typów danych, zobacz [Wykonywanie zapytań względem tabel i jednostek](https://docs.microsoft.com/rest/api/storageservices/querying-tables-and-entities).

**Results**

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Ben |Smith | Ben@contoso.com| 425-555-0102 |

## <a name="query-by-using-linq"></a>Wykonywanie zapytań za pomocą wyrażenia LINQ 
Możesz także wykonywać zapytania za pomocą wyrażenia LINQ, co oznacza odpowiednie wyrażenia zapytań OData. Oto przykład sposobu tworzenia zapytań przy użyciu zestawu .NET SDK:

```csharp
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("people");

TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
    .Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("Email", QueryComparisons.Equal,"Ben@contoso.com")
    ));

await table.ExecuteQuerySegmentedAsync<CustomerEntity>(query, null);
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> * Przedstawiono sposób wykonywania zapytań przy użyciu interfejsu API tabel

Możesz teraz przejść do następnego samouczka, aby dowiedzieć się, jak dystrybuować swoje dane globalnie.

> [!div class="nextstepaction"]
> [Globalna dystrybucja danych](tutorial-global-distribution-table.md)
