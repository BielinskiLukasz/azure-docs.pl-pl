---
title: 'Azure Cosmos DB: asynchroniczny interfejs API języka Java SQL, zasoby & zestawu SDK'
description: Dowiedz się więcej o interfejsie API i zestawie SDK asynchronicznego języka SQL, w tym o datach wydania, datach wycofania i zmianach między poszczególnymi wersjami Azure Cosmos DB SQL Async Java SDK.
author: moderakh
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 07/01/2019
ms.author: moderakh
ms.openlocfilehash: 61824d1f979d54f49d23f3805ff249c75a62baa4
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063940"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Async SDK Java dla interfejsu API SQL: informacje o wersji i zasoby
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Źródło zmian platformy .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java (asynchroniczny)](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Wykonawca zbiorczy — .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Moduł wykonawczy zbiorczej — Java](sql-api-sdk-bulk-executor-java.md)

Pakiet asynchroniczny interfejs Java interfejsu API języka SQL różni się od zestawu SDK Java interfejsu SQL API, zapewniając asynchroniczne operacje z obsługą [biblioteki](https://netty.io/)sieci. Wstępnie istniejący [zestaw SDK Java interfejsu API języka SQL](sql-api-sdk-java.md) nie obsługuje operacji asynchronicznych. 

| |  |
|---|---|
| **Pobieranie zestawu SDK** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**Dokumentacja interfejsu API** |[Dokumentacja interfejsu API języka Java](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**Współtworzenie zestawu SDK** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Wprowadzenie** | [Wprowadzenie do asynchronicznego zestawu Java SDK](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Przykładowy kod** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Porady dotyczące wydajności**| [Plik Readme usługi GitHub](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Minimalne obsługiwane środowisko uruchomieniowe**|[JDK 8](https://aka.ms/azure-jdks) | 

[!INCLUDE[Release notes](~/azure-cosmosdb-java-v2/changelog/README.md)]
## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) .

