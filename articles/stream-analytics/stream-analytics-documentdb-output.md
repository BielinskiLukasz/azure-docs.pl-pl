---
title: Usługa Azure Stream Analytics dane wyjściowe usługi Azure Cosmos DB
description: W tym artykule opisano sposób użycia usługi Azure Stream Analytics można zapisać danych wyjściowych do usługi Azure Cosmos DB dla danych wyjściowych JSON, archiwizowania danych i zapytań o małych opóźnieniach na danych JSON bez struktury.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/2/2020
ms.custom: seodec18
ms.openlocfilehash: e58e36b3caa5a5ecd137cb9cb61dad7ddb95ff3a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364575"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Usługa Azure Stream Analytics dane wyjściowe usługi Azure Cosmos DB  
Azure Stream Analytics może kierować [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) do danych wyjściowych JSON, co umożliwia archiwizowanie danych i uruchamianie zapytań o małym opóźnieniu na dane JSON bez struktury. W tym dokumencie opisano najlepsze rozwiązania dotyczące wdrażania tej konfiguracji.

Jeśli nie znasz Azure Cosmos DB, zapoznaj się z [dokumentacją Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) , aby rozpocząć pracę. 

> [!Note]
> W tej chwili Stream Analytics obsługuje połączenie Azure Cosmos DB tylko za pomocą *interfejsu API SQL*.
> Innych interfejsów API usługi Azure Cosmos DB nie są jeszcze obsługiwane. Jeśli użytkownik wskaże Stream Analytics kont Azure Cosmos DB utworzonych z innymi interfejsami API, dane mogą nie być prawidłowo przechowywane. 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>Podstawy Azure Cosmos DB jako miejsce docelowe danych wyjściowych
Azure Cosmos DB dane wyjściowe w Stream Analytics umożliwiają zapisywanie wyników przetwarzania strumienia w postaci danych wyjściowych w formacie JSON w kontenerach Azure Cosmos DB. 

Stream Analytics nie tworzy kontenerów w bazie danych. Zamiast tego wymaga, aby utworzyć je na początku. Następnie można kontrolować koszty rozliczania Azure Cosmos DB kontenerów. Możesz również dostosować wydajność, spójność i pojemność kontenerów bezpośrednio przy użyciu [Azure Cosmos DB interfejsów API](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> Należy dodać 0.0.0.0 do listy dozwolonych adresów IP z zapory Azure Cosmos DBej.

Poniższe sekcje zawierają szczegółowe informacje na temat opcji kontenera dla Azure Cosmos DB.

## <a name="tuning-consistency-availability-and-latency"></a>Dostrajanie spójności, dostępności i opóźnień
Aby dopasować wymagania dotyczące aplikacji, Azure Cosmos DB umożliwia precyzyjne dostosowanie bazy danych i kontenerów oraz wprowadzenie kompromisu między spójnością, dostępnością, opóźnieniami i przepływności. 

Zależności od tego, jakie poziomy spójności odczytu wymagań scenariusza dla zapisu i odczytu czas oczekiwania, możesz wybrać poziom spójności na Twoje konto bazy danych. Przepustowość można zwiększyć przez skalowanie jednostek żądań (jednostek ru) w kontenerze. 

Domyślnie Azure Cosmos DB umożliwia indeksowanie synchroniczne dla każdej operacji CRUD w kontenerze. Jest to kolejna przydatna opcja kontrolowania wydajności zapisu/odczytu w Azure Cosmos DB. 

Aby uzyskać więcej informacji, zapoznaj się z artykułem [Zmienianie poziomów spójności bazy danych i zapytań](../cosmos-db/consistency-levels.md) .

## <a name="upserts-from-stream-analytics"></a>Wykonuje operację UPSERT z usługi Stream Analytics
Integracja Stream Analytics z Azure Cosmos DB umożliwia wstawianie lub aktualizowanie rekordów w kontenerze na podstawie danej kolumny **identyfikatora dokumentu** . Jest to również nazywane *upsert*.

Stream Analytics używa podejścia optymistycznego upsert. Aktualizacje są wykonywane tylko wtedy, gdy operacja wstawiania kończy się niepowodzeniem z powodu konfliktu identyfikatora dokumentu. 

W przypadku poziomu zgodności 1,0, Stream Analytics wykonuje tę aktualizację jako operację PATCH, dzięki czemu umożliwia ona częściowe aktualizacje dokumentu. Stream Analytics dodaje nowe właściwości lub zastępuje istniejącą Właściwość przyrostowo. Jednak zmiany wartości właściwości tablicy w dokumencie JSON powodują zastąpienie całej tablicy. Oznacza to, że tablica nie jest scalona. 

W przypadku 1,2 upsert zachowanie jest modyfikowane w celu wstawienia lub zamiany dokumentu. W dalszej części informacji o poziomie zgodności 1,2 opisano to zachowanie.

Jeśli dokument przychodzącego JSON ma istniejące pole identyfikatora, to pole jest automatycznie używane jako kolumna **Identyfikator dokumentu** w Azure Cosmos DB. Wszelkie kolejne operacje zapisu są obsługiwane w taki sposób, co może prowadzić do jednej z następujących sytuacji:

- Unikatowe identyfikatory powodują wstawienie.
- Zduplikowane identyfikatory i **Identyfikator dokumentu** mają ustawioną wartość **ID** upsert.
- Zduplikowane identyfikatory i **Identyfikator dokumentu** nie zostały ustawione jako potencjalne do błędu po pierwszym dokumencie.

Jeśli chcesz zapisać *wszystkie* dokumenty, w tym te, które mają zduplikowany identyfikator, Zmień nazwę pola ID w zapytaniu (za pomocą słowa kluczowego **as** ). Pozwól Azure Cosmos DB utworzyć pole identyfikatora lub zastąpić identyfikator z inną kolumną (za pomocą słowa kluczowego **as** lub przy użyciu ustawienia **Identyfikator dokumentu** ).

## <a name="data-partitioning-in-azure-cosmos-db"></a>Partycjonowanie danych w Azure Cosmos DB
Azure Cosmos DB automatycznie skaluje partycje na podstawie obciążenia. Dlatego zalecamy używanie [nieograniczonych](../cosmos-db/partition-data.md) kontenerów jako podejścia do partycjonowania danych. Gdy Stream Analytics zapisu do nieograniczoną liczbę kontenerów, używa jako wielu modułów zapisujących równoległych jako poprzedni krok zapytania lub schemat partycjonowania danych wejściowych.

> [!NOTE]
> Azure Stream Analytics obsługuje tylko nieograniczone kontenery z kluczami partycji na najwyższym poziomie. Na przykład `/region` jest obsługiwana. Zagnieżdżone klucze partycji (na przykład `/region/name`) nie są obsługiwane. 

W zależności od wybranego klucza partycji mogą pojawić się następujące _Ostrzeżenie_:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Ważne jest, aby wybrać właściwość klucza partycji, która ma wiele różnych wartości i która umożliwia równomierne dystrybuowanie obciążeń między tymi wartościami. Naturalnym artefaktem partycjonowania żądania, które obejmują ten sam klucz partycji, są ograniczone przez maksymalną przepływność pojedynczej partycji. 

Rozmiar magazynu dla dokumentów należących do tego samego klucza partycji jest ograniczony do 10 GB. Idealnym kluczem partycji jest ten, który pojawia się często jako filtr w zapytaniach i ma wystarczającą Kardynalność, aby zapewnić skalowalność rozwiązania.

Klucz partycji jest również granicą dla transakcji w procedurach składowanych i wyzwalaczy dla Azure Cosmos DB. Należy wybrać klucz partycji, aby dokumenty, które występują razem w transakcjach miały tę samą wartość klucza partycji. [Partycjonowanie artykułu w Azure Cosmos DB](../cosmos-db/partitioning-overview.md) zawiera więcej szczegółów na temat wybierania klucza partycji.

W przypadku kontenerów o stałym Azure Cosmos DB Stream Analytics nie pozwala na skalowanie w górę i w dół po ich zapełnieniu. Mają górny limit równy 10 GB i 10 000 RU/s przepływności. Aby przeprowadzić migrację danych ze stałego kontenera do nieograniczonego kontenera (na przykład jeden z co najmniej 1 000 RU/s i kluczem partycji), użyj narzędzia do [migracji danych](../cosmos-db/import-data.md) lub [biblioteki źródła zmian](../cosmos-db/change-feed.md).

Możliwość zapisu w wielu stałych kontenerach jest przestarzała. Nie zalecamy jej skalowania do Stream Analytics zadania.

## <a name="improved-throughput-with-compatibility-level-12"></a>Zwiększona przepływność z poziomem zgodności 1,2
W przypadku poziomu zgodności 1,2 Stream Analytics obsługuje natywną integrację do zapisu zbiorczego w Azure Cosmos DB. Dzięki tej integracji można efektywnie zapisywać Azure Cosmos DB przy jednoczesnej maksymalizacji przepływności i efektywnej obsłudze żądań ograniczania. 

Ulepszony mechanizm pisania jest dostępny w ramach nowego poziomu zgodności z powodu różnic w zachowaniu upsert. Na poziomach przed 1,2m zachowanie upsert polega na wstawieniu lub scaleniu dokumentu. W przypadku 1,2 upsert zachowanie jest modyfikowane w celu wstawienia lub zamiany dokumentu.

Na poziomach przed 1,2 Stream Analytics używa niestandardowej procedury składowanej do zbiorczych dokumentów upsert na klucz partycji w Azure Cosmos DB. W tym miejscu partia jest zapisywana jako transakcja. Nawet wtedy, gdy pojedynczy rekord osiągnie błąd przejściowy (ograniczanie), cała partia musi być ponowiona. Dzięki temu scenariusze mające nawet rozsądne ograniczenia są stosunkowo wolne.

W poniższym przykładzie przedstawiono dwa identyczne zadania Stream Analytics odczytywane z tego samego danych wejściowych platformy Azure Event Hubs. Oba zadania Stream Analytics są w [pełni podzielone na partycje](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) przy użyciu kwerendy przekazującej i zapisu w identycznych kontenerach Azure Cosmos DB. Metryki po lewej stronie pochodzą z zadania skonfigurowanego z poziomem zgodności 1,0. Metryki po prawej stronie są skonfigurowane z 1,2. Klucz partycji kontenera Azure Cosmos DB jest unikatowym identyfikatorem GUID, który pochodzi ze zdarzenia wejściowego.

![Porównanie metryk Stream Analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Szybkość zdarzeń przychodzących w Event Hubs wynosi dwa razy większe niż Azure Cosmos DB kontenery (20 000 jednostek ru) są skonfigurowane do przydziałania, więc w Azure Cosmos DB. Jednak zadanie o 1,2 jest stale napisywany w wyższej przepływności (zdarzenia wyjściowe na minutę) i z niższym średnim użyciem SU%. W danym środowisku ta różnica będzie zależeć od kilku innych czynników. Te czynniki obejmują wybór formatu zdarzeń, zdarzeń wejściowych/rozmiaru komunikatu, kluczy partycji i zapytania.

![Porównanie metryk Azure Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Dzięki 1,2 Stream Analytics jest bardziej inteligentna w korzystaniu z 100 procent dostępnej przepływności w Azure Cosmos DB z nielicznymi reprocesami przesyłania lub ograniczania szybkości. Zapewnia to lepszy komfort obsługi innych obciążeń, takich jak zapytania uruchomione w kontenerze w tym samym czasie. Jeśli chcesz zobaczyć, jak Stream Analytics skaluje się z Azure Cosmos DB jako ujścia dla 1 000 do 10 000 komunikatów na sekundę, wypróbuj [Ten przykładowy projekt platformy Azure](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb).

Przepływność Azure Cosmos DB danych wyjściowych jest taka sama jak w przypadku 1,0 i 1,1. *Zdecydowanie zalecamy* używanie poziomu zgodności 1,2 w Stream Analytics z Azure Cosmos DB.

## <a name="azure-cosmos-db-settings-for-json-output"></a>Ustawienia Azure Cosmos DB dla danych wyjściowych JSON

Użycie Azure Cosmos DB jako danych wyjściowych w Stream Analytics generuje następujący monit o podanie informacji.

![Pola informacji dla strumienia danych wyjściowych Azure Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Pole           | Opis|
|-------------   | -------------|
|Alias danych wyjściowych    | Alias do odwoływania się do tych danych wyjściowych w kwerendzie Stream Analytics.|
|Subskrypcja    | Subskrypcja platformy Azure.|
|Identyfikator konta      | Nazwa lub identyfikator URI punktu końcowego konta Azure Cosmos DB.|
|Klucz konta     | Współużytkowany klucz dostępu dla konta Azure Cosmos DB.|
|Baza danych        | Nazwa bazy danych Azure Cosmos DB.|
|Nazwa kontenera | Nazwa kontenera, taka jak `MyContainer`. Jeden kontener o nazwie `MyContainer` musi istnieć.  |
|Identyfikator dokumentu     | Opcjonalny. Nazwa kolumny w zdarzeniach wyjściowych służąca jako unikatowego klucza, na które insert nebo update operacji musi być oparta. Jeśli pozostawisz ją puste, zostaną wstawione wszystkie zdarzenia bez opcji aktualizacji.|

Po skonfigurowaniu danych wyjściowych Azure Cosmos DB można użyć jej w zapytaniu jako celu [instrukcji into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Gdy używasz danych wyjściowych Azure Cosmos DB w ten sposób, [klucz partycji musi być ustawiony jawnie](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks). 

Rekord wyjściowy musi zawierać kolumnę z uwzględnieniem wielkości liter o nazwie po kluczu partycji w Azure Cosmos DB. Aby osiągnąć większą przetwarzanie równoległe, instrukcja może wymagać [klauzuli Partition by](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) , która używa tej samej kolumny.

Oto przykładowe zapytanie:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Obsługa błędów oraz wykonywanie ponownych prób

Jeśli wystąpi błąd przejściowy, niedostępność usługi lub ograniczanie przepustowości, podczas gdy Stream Analytics wysyła zdarzenia do Azure Cosmos DB, Stream Analytics ponawianie prób w nieskończoność, aby pomyślnie zakończyć operację. Ale nie próbuje ponowić próby dla następujących błędów:

- Nieautoryzowany (kod błędu HTTP 401)
- NotFound (kod błędu HTTP 404)
- Zabronione (kod błędu HTTP 403)
- Nieprawidłowego żądania (kod błędu HTTP 400)
