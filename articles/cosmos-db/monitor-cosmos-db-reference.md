---
title: Informacje o danych monitorowania Azure Cosmos DB | Microsoft Docs
description: Informacje o dziennikach i metrykach dotyczące monitorowania danych z Azure Cosmos DB.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: d131523e3031f55a818bb1919f39119bf073cb75
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456527"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Informacje o danych monitorowania Azure Cosmos DB
Ten artykuł zawiera informacje dotyczące danych dzienników i metryk zbieranych w celu przeanalizowania wydajności i dostępności Azure Cosmos DB. Aby uzyskać szczegółowe informacje na temat zbierania i analizowania danych monitorowania dla Azure Cosmos DB, zobacz [Cosmos DB monitorowania](monitor-cosmos-db.md) .


## <a name="resource-logs"></a>Dzienniki zasobów
W poniższej tabeli wymieniono właściwości dzienników zasobów Azure Cosmos DB, które są zbierane w Azure Monitor dziennikach lub usłudze Azure Storage. W dziennikach Azure Monitor są one zbierane w tabeli **AzureDiagnostics** z wartością **ResourceProvider** *firmy Microsoft. DOCUMENTDB*. 

| Usługa Azure Storage pola lub właściwości | Właściwość dzienników Azure Monitor | Opis |
| --- | --- | --- |
| **pierwszym** | **TimeGenerated** | Data i godzina (UTC), gdy wystąpienia operacji. |
| **Identyfikator** | **Zasób** | Konto usługi Azure Cosmos DB, dla którego dzienniki są włączone.|
| **kategorii** | **Kategoria** | W przypadku dzienników Azure Cosmos DB, **DataPlaneRequests**, **MongoRequests**, **QueryRuntimeStatistics**, **PartitionKeyStatistics**, **PartitionKeyRUConsumption**, **ControlPlaneRequests** są dostępne typy dzienników. |
| **operationName** | **OperationName** | Nazwa operacji. Ta wartość może być dowolną z następujących operacji: tworzenia, aktualizacji, odczytu, ReadFeed, Delete, Replace, Execute, SqlQuery, zapytania, JSQuery, Head, HeadFeed lub Upsert.   |
| **aœciwoœci** | Nie dotyczy | Zawartość tego pola są opisane w kolejnych wierszy. |
| **activityId** | **activityId_g** | Unikatowy identyfikator GUID dla zarejestrowanych operacji. |
| **userAgent** | **userAgent_s** | Ciąg, który określa agent użytkownika klienta, który wykonuje żądanie. Format to {nazwa agenta użytkownika} / {version}.|
| **requestResourceType** | **requestResourceType_s** | Typ dostęp do zasobów. Ta wartość może być dowolną z następujących zasobów: bazy danych, kontenerów, dokument, załącznika, użytkownika, uprawnienie, StoredProcedure, wyzwalacza, UserDefinedFunction lub oferty. |
| **Stanu** | **statusCode_s** | Stan odpowiedzi operacji. |
| **requestResourceId** | **Identyfikator** | Identyfikator zasobu, które odnoszą się do żądania. Wartość może wskazywać databaseRid, collectionRid lub documentRid w zależności od operacji wykonywanych.|
| **clientIpAddress** | **clientIpAddress_s** | Adres IP klienta. |
| **requestCharge** | **requestCharge_s** | Liczba jednostek żądań, które są używane przez operację |
| **collectionRid** | **collectionId_s** | Unikatowy identyfikator dla kolekcji.|
| **trwania** | **duration_s** | Czas trwania operacji (w milisekundach). |
| **requestLength** | **requestLength_s** | Długość żądania, w bajtach. |
| **responseLength** | **responseLength_s** | Długość odpowiedzi w bajtach.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Ta wartość nie jest pusta, gdy [tokeny zasobów](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) są używane do uwierzytelniania. Wartość wskazuje identyfikator zasobu użytkownika. |

Aby zapoznać się z listą wszystkich kategorii dzienników Azure Monitor i linków do skojarzonych schematów, zobacz [Azure monitor dzienniki kategorii i schematów](../azure-monitor/platform/diagnostic-logs-schema.md). 

## <a name="metrics"></a>Metryki
W poniższych tabelach przedstawiono metryki platformy zebrane dla usługi Azure CosmOS DB. Wszystkie metryki są przechowywane w przestrzeni nazw **Cosmos DB metrykach standardowych**.

Aby uzyskać listę wszystkich Azure Monitor metryki pomocy technicznej (w tym CosmosDB), zobacz [Azure monitor obsługiwane metryki](../azure-monitor/platform/metrics-supported.md). 

#### <a name="request-metrics"></a>Metryki żądania
            
|Metryka (nazwa wyświetlana metryki)|Jednostka (typ agregacji) |Opis|Wymiary| Stopień szczegółowości czasu| Mapowanie starszej metryki | Sposób użycia |
|---|---|---|---| ---| ---| ---|
| TotalRequests (całkowita liczba żądań) | Liczba (liczba) | Liczba wykonanych żądań| DatabaseName, CollectionName, region, StatusCode| Wszystkie | TotalRequests, http 2xx, http 3xx, http 400, HTTP 401, wewnętrzny błąd serwera, Usługa niedostępna, żądania ograniczone, średnia liczba żądań na sekundę | Służy do monitorowania żądań według kodu stanu, kontenera z dokładnością do minuty. Aby uzyskać średnią liczbę żądań na sekundę, użyj agregacji Count na minutę i Podziel ją na 60. |
| MetadataRequests (żądania metadanych) |Liczba (liczba) | Liczba żądań metadanych. Azure Cosmos DB utrzymuje kontener metadanych systemu dla każdego konta, który umożliwia Wyliczenie kolekcji, baz danych itp., a ich konfiguracje, bez opłat. | DatabaseName, CollectionName, region, StatusCode| Wszystkie| |Służy do monitorowania ograniczania przepustowości ze względu na żądania metadanych.|
| MongoRequests (żądania Mongo) | Liczba (liczba) | Liczba wykonanych żądań Mongo | DatabaseName, CollectionName, region, CommandName, ErrorCode| Wszystkie |Częstotliwość żądań zapytań Mongo, szybkość żądania aktualizacji Mongo, częstotliwość żądań usunięcia Mongo, Mongo wstawiania liczby żądań, Mongo| Służy do monitorowania błędów żądania Mongo, użycia na typ polecenia. |

#### <a name="request-unit-metrics"></a>Metryki jednostki żądania

|Metryka (nazwa wyświetlana metryki)|Jednostka (typ agregacji)|Opis|Wymiary| Stopień szczegółowości czasu| Mapowanie starszej metryki | Sposób użycia |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo żądania) | Liczba (łącznie) |Wykorzystane jednostki żądania Mongo| DatabaseName, CollectionName, region, CommandName, ErrorCode| Wszystkie |Mongo naliczanie żądań zapytań, opłata za żądanie aktualizacji Mongo, opłata za żądanie usunięcia żądania, Mongo, opłata za żądanie| Służy do monitorowania Mongo zasobów jednostek RU w ciągu minuty.|
| TotalRequestUnits (łączna liczba jednostek żądań)| Liczba (łącznie) | Wykorzystane jednostki żądania| DatabaseName, CollectionName, region, StatusCode |Wszystkie| TotalRequestUnits| Służy do monitorowania całkowitego użycia RU z dokładnością do minuty. Aby obliczyć średnią liczbę zużytych jednostek RU na sekundę, użyj łącznej agregacji na minutę i Podziel ją na 60.|
| ProvisionedThroughput (zainicjowana przepływność)| Liczba (maksimum) |Elastyczna przepływność na poziomie szczegółowości kontenera| DatabaseName, ContainerName| 5 M| | Służy do monitorowania zainicjowanej przepływności na kontener.|

#### <a name="storage-metrics"></a>Metryki magazynu

|Metryka (nazwa wyświetlana metryki)|Jednostka (typ agregacji)|Opis|Wymiary| Stopień szczegółowości czasu| Mapowanie starszej metryki | Sposób użycia |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (dostępny magazyn) |Bajty (łącznie) | Łączna ilość dostępnego magazynu zgłoszona z dokładnością do 5 minut na region| DatabaseName, CollectionName, region| 5 M| Dostępny magazyn| Służy do monitorowania dostępnej pojemności magazynu (dotyczy tylko stałych kolekcji magazynów) minimalny stopień szczegółowości powinien wynosić 5 minut.| 
| Datausage (użycie danych) |Bajty (łącznie) |Całkowite użycie danych zgłoszone z dokładnością do 5 minut na region| DatabaseName, CollectionName, region| 5 M |Rozmiar danych | Służy do monitorowania łącznego użycia danych w kontenerze i regionie, minimalny stopień szczegółowości powinien wynosić 5 minut.|
| IndexUsage (użycie indeksu) | Bajty (łącznie) |Całkowite użycie indeksów zgłoszone w 5-minutowych stopnia szczegółowości na region| DatabaseName, CollectionName, region| 5 M| Rozmiar indeksu| Służy do monitorowania łącznego użycia danych w kontenerze i regionie, minimalny stopień szczegółowości powinien wynosić 5 minut. |
| DocumentQuota (przydział dokumentu) | Bajty (łącznie) | Łączny przydział magazynu zgłoszony z dokładnością do 5 minut na region.| DatabaseName, CollectionName, region| 5 M |Pojemność magazynu| Służy do monitorowania łącznego limitu przydziału w kontenerze i regionie. minimalny stopień szczegółowości powinien wynosić 5 minut.|
| DocumentCount (liczba dokumentów) | Liczba (łącznie) |Łączna liczba dokumentów zgłoszona z dokładnością do 5 minut na region| DatabaseName, CollectionName, region| 5 M |Liczba dokumentów|Używane do monitorowania liczby dokumentów w kontenerze i regionie, minimalny stopień szczegółowości powinien wynosić 5 minut.|

#### <a name="latency-metrics"></a>Metryki opóźnienia

|Metryka (nazwa wyświetlana metryki)|Jednostka (typ agregacji)|Opis|Wymiary| Stopień szczegółowości czasu| Sposób użycia |
|---|---|---|---| ---| ---|
| ReplicationLatency (opóźnienie replikacji)| Milisekundy (minimum, maksimum, średnia) | Opóźnienie replikacji poziomie P99 w regionach źródłowym i docelowym dla konta z obsługą geograficzną| SourceRegion, TargetRegion| Wszystkie | Służy do monitorowania opóźnienia replikacji poziomie P99 między dowolnymi dwoma regionami dla konta z replikacją geograficzną. |


#### <a name="availability-metrics"></a>Metryki dostępności

|Metryka (nazwa wyświetlana metryki) |Jednostka (typ agregacji)|Opis| Stopień szczegółowości czasu| Mapowanie starszej metryki | Sposób użycia |
|---|---|---|---| ---| ---|
| Serviceavailability (dostępność usługi)| Procent (minimum, maksimum) | Dostępność żądań konta z dokładnością do 1 godziny| 1H | Dostępność usługi | Reprezentuje procent całkowitej liczby żądań zakończonych niepowodzenie. Żądanie jest uznawane za niepowodzenie z powodu błędu systemu, jeśli kod stanu to 410, 500 lub 503 używany do monitorowania dostępności konta na poziomie szczegółowości godzin. |


#### <a name="cassandra-api-metrics"></a>Metryki interfejs API Cassandra

|Metryka (nazwa wyświetlana metryki)|Jednostka (typ agregacji)|Opis|Wymiary| Stopień szczegółowości czasu| Sposób użycia |
|---|---|---|---| ---| ---|
| CassandraRequests (żądania Cassandra) | Liczba (liczba) | Liczba wykonanych żądań interfejs API Cassandra| DatabaseName, CollectionName, ErrorCode, region, OperationType, ResourceType| Wszystkie| Służy do monitorowania żądań Cassandra z dokładnością do minuty. Aby uzyskać średnią liczbę żądań na sekundę, użyj agregacji Count na minutę i Podziel ją na 60.|
| CassandraRequestCharges (Cassandra żądania) | Count (sum, min, Max, AVG) | Jednostki żądań używane przez żądania interfejs API Cassandra| DatabaseName, CollectionName, region, OperationType, ResourceType| Wszystkie| Służy do monitorowania jednostek ru używanych na minutę przez konto interfejs API Cassandra.|
| CassandraConnectionClosures (zamknięcia połączeń Cassandra) |Liczba (liczba) |Liczba zamkniętych połączeń Cassandra| ClosureReason, region| Wszystkie | Służy do monitorowania łączności między klientami i interfejs API Cassandra Azure Cosmos DB.|

## <a name="see-also"></a>Zobacz też

- Aby uzyskać opis Azure Cosmos DB monitorowania, zobacz [Azure Cosmos DB monitorowania](monitor-cosmos-db.md) .
- Aby uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure, zobacz [monitorowanie zasobów platformy Azure za pomocą Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) .
