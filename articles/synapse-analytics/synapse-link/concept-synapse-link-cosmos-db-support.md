---
title: Link usługi Azure Synapse (wersja zapoznawcza) dla Azure Cosmos DB obsługiwanych funkcji
description: Zapoznaj się z bieżącą listą akcji obsługiwanych przez link Synapse platformy Azure dla Azure Cosmos DB
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: fa05f8a8b05a0acddae906a93c90c42424466969
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322605"
---
# <a name="azure-synapse-link-preview-for-azure-cosmos-db-supported-features"></a>Link usługi Azure Synapse (wersja zapoznawcza) dla Azure Cosmos DB obsługiwanych funkcji

W tym artykule opisano funkcje, które są obecnie obsługiwane w usłudze Azure Synapse Link dla usługi Azure Cosmos DB.

## <a name="azure-synapse-support"></a>Pomoc techniczna platformy Azure Synapse

Istnieją dwa typy kontenerów w Azure Cosmos DB:
* Kontener HTAP — kontener z włączonym linkiem Synapse. Ten kontener ma magazyn transakcyjny i magazyn analityczny. 
* Kontener OLTP — nie włączono kontenera z linkiem Synaspe. Ten kontener ma tylko magazyn transakcyjny i nie ma magazynu analitycznego.

> [!IMPORTANT]
> Link Synapse platformy Azure dla Azure Cosmos DB jest obecnie obsługiwany w obszarach roboczych Synapse, w których nie włączono zarządzanej sieci wirtualnej. 

Można nawiązać połączenie z kontenerem Azure Cosmos DB bez włączania linku Synapse. W tym scenariuszu można tylko odczytywać i zapisywać dane w magazynie transakcyjnym. Poniżej znajduje się lista obecnie obsługiwanych funkcji w ramach linku Synapse dla Azure Cosmos DB. 

| Kategoria              | Opis |[Spark](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [Bezserwerowy SQL](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- |
| **Obsługa czasu wykonywania** |Obsługiwane środowisko uruchomieniowe usługi Azure Synapse w celu uzyskania dostępu Azure Cosmos DB| ✓ | ✓ |
| **Obsługa interfejsu API Azure Cosmos DB** | Obsługiwany Azure Cosmos DB rodzaj interfejsu API | SQL/MongoDB | SQL/MongoDB |
| **Stream**  |Obiekty takie jak tabela, która może być utworzona, wskazująca bezpośrednio na kontener Azure Cosmos DB| Dataframe, widok, tabela | Widok |
| **Odczyt**    | Typ kontenera Azure Cosmos DB, który może zostać odczytany | OLTP/HTAP | HTAP  |
| **Prawem**   | Czy środowisko uruchomieniowe usługi Azure Synapse jest używane do zapisywania danych w kontenerze Azure Cosmos DB | Tak | Nie |

* Jeśli zapisujesz dane do kontenera Azure Cosmos DB z platformy Spark, ten proces odbywa się za pośrednictwem transakcyjnego magazynu Azure Cosmos DB. Będzie to miało wpływ na transakcyjną wydajność Azure Cosmos DB przez zużywanie jednostek żądania.
* Integracja dedykowanej puli SQL za poorednictwem tabel zewnętrznych nie jest obecnie obsługiwana.
 
## <a name="supported-code-generated-actions-for-spark"></a>Obsługiwane akcje generowane przez kod dla platformy Spark

| Gest              | Opis |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Załaduj do ramki Dataframe** |Ładowanie i odczytywanie danych w ramce Dataframe platformy Spark |✓| ✓ |
| **Utwórz tabelę platformy Spark** |Tworzenie tabeli wskazującej kontener Azure Cosmos DB|✓| ✓ |
| **Zapisz ramkę danych do kontenera** |Zapisywanie danych w kontenerze|✓| ✓ |
| **Załaduj ramkę danych przesyłania strumieniowego z kontenera** |Przesyłanie strumieniowe danych przy użyciu kanału informacyjnego zmiany Azure Cosmos DB|✓| ✓ |
| **Zapisz ramkę danych przesyłania strumieniowego do kontenera** |Przesyłanie strumieniowe danych przy użyciu kanału informacyjnego zmiany Azure Cosmos DB|✓| ✓ |


## <a name="supported-code-generated-actions-for-sql-serverless"></a>Obsługiwane akcje generowane w kodzie dla programu SQL Server

| Gest              | Opis |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Eksplorowanie danych** |Eksplorowanie danych z kontenera przy użyciu znanej składni języka T-SQL i automatycznego wnioskowania schematu|X| ✓ |
| **Tworzenie widoków i raportów analizy biznesowej** |Utwórz widok SQL, aby uzyskać bezpośredni dostęp do kontenera dla analizy biznesowej za pośrednictwem usługi Synapse SQL Server |X| ✓ |
| **Dołącz do różnych źródeł danych wraz z danymi Cosmos DB** | Przechowuj wyniki zapytania dotyczące odczytywania danych z kontenerów Cosmos DB i danych na platformie Azure Blob Storage lub Azure Data Lake Storage za pomocą CETAS |X| ✓ |

## <a name="next-steps"></a>Następne kroki

* Zobacz, jak [nawiązać połączenie z Synapse linkiem dla Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
* [Dowiedz się, jak wykonywać zapytania dotyczące magazynu analitycznego Cosmos DB przy użyciu platformy Spark](how-to-query-analytical-store-spark.md)
