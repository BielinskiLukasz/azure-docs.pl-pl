---
title: Uaktualnij do najnowszej wersji interfejsu API REST usługi Azure Search Azure Search
description: Zapoznaj się z różnicami w wersji interfejsu API i Dowiedz się, które akcje są wymagane do migrowania istniejącego kodu do najnowszej wersji interfejsu API REST usługi Azure Search.
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.openlocfilehash: 6c1f7fdb1f349c9e31ba63d79a9b9e26ea9f09da
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182385"
---
# <a name="upgrade-to-the-latest-azure-search-service-rest-api-version"></a>Uaktualnij do najnowszej wersji interfejsu API REST usługi Azure Search
Jeśli używasz starszej wersji [interfejsu API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/), ten artykuł pomoże Ci uaktualnić aplikację pod kątem korzystania z najnowszej, dostępnej w wersji interfejsu api, 2019-05-06.

Wersja 2019-05-06 interfejsu API REST zawiera pewne zmiany z wcześniejszych wersji. Są one głównie zgodne z poprzednimi wersjami, więc zmiana kodu powinna wymagać tylko minimalnego nakładu pracy, w zależności od używanej wersji. [Procedura uaktualniania](#UpgradeSteps) przedstawia zmiany kodu wymagane do korzystania z nowych funkcji.

> [!NOTE]
> Wystąpienie usługi Azure Search obsługuje wiele wersji interfejsu API REST, w tym wcześniejszych. Możesz nadal używać tych wersji interfejsu API, ale zalecamy Migrowanie kodu do najnowszej wersji, aby uzyskać dostęp do nowych funkcji.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>Co nowego w wersji 2019-05-06
Wersja 2019-05-06 to najnowsza ogólnie dostępna wersja interfejsu API REST usługi Azure Search. Funkcje, które przeszły do ogólnie dostępnego stanu w tej wersji interfejsu API, obejmują:

* [Autouzupełnianie](index-add-suggesters.md) jest funkcją typeahead, która kończy częściowo określone dane wejściowe.

* [Typy złożone](search-howto-complex-data-types.md) zapewniają natywną obsługę danych strukturalnych obiektów w indeksie Azure Search.

* [Tryby analizy JsonLines](search-howto-index-json-blobs.md), część indeksowania obiektów blob platformy Azure, tworzy jeden dokument wyszukiwania na jednostkę JSON rozdzieloną wierszem.

* [Wyszukiwanie poznawcze](cognitive-search-concept-intro.md) zapewnia indeksowanie, które wykorzystuje silniki wzbogacania AI z Cognitive Services.

Niektóre wersje funkcji w wersji zapoznawczej pokrywają się z ogólną dostępną aktualizacją. Aby zapoznać się z listą nowych funkcji w wersji zapoznawczej, zobacz [interfejs API REST usługi Search — wersja 2019-05-06-Preview](search-api-preview.md).

## <a name="breaking-changes"></a>Zmiany powodujące niezgodność

Istniejący kod zawierający następujące funkcje spowoduje przerwanie działania interfejsu API-Version = 2019-05-06.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indeksator dla Azure Cosmos DB-DataSource jest teraz "Type": "cosmosdb"

Jeśli używasz [indeksatora Cosmos DB](search-howto-index-cosmosdb.md ), musisz zmienić `"type": "documentdb"` na. `"type": "cosmosdb"`

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Błędy wyniku wykonywania indeksatora nie mają już stanu

Struktura błędów dla wykonywania indeksatora wcześniej zawierała `status` element. Ten element został usunięty, ponieważ nie dostarcza użytecznych informacji.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Interfejs API źródła danych indeksatora nie zwraca już parametrów połączenia

Od interfejsu API w wersji 2019-05-06 i 2019-05-06 — wersja zapoznawcza, interfejs API źródła danych nie zwraca już parametrów połączenia w odpowiedzi na jakąkolwiek operację REST. W poprzednich wersjach interfejsu API dla źródeł danych utworzonych za pomocą polecenia POST Azure Search zwrócić **201** , a następnie odpowiedzi OData, która zawierała ciąg połączenia w postaci zwykłego tekstu.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Umiejętność rozpoznawania nazwanych jednostek jest teraz nieobsługiwana

Jeśli wywołasz w kodzie umiejętność [rozpoznawania jednostek nazw](cognitive-search-skill-named-entity-recognition.md) , wywołanie zakończy się niepowodzeniem. Funkcja wymiany jest [rozpoznawaniem jednostek](cognitive-search-skill-entity-recognition.md). Powinno być możliwe zamienienie odwołania do umiejętności bez żadnych innych zmian. Sygnatura interfejsu API jest taka sama dla obu wersji. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki do uaktualnienia
W przypadku uaktualniania z wcześniejszej wersji systemu, 2017-11-11 lub 2016-09-01, prawdopodobnie nie trzeba wprowadzać żadnych zmian w kodzie, innym niż zmienić numer wersji. Jedyne sytuacje, w których może zajść potrzeba zmiany kodu, są następujące:

* Kod kończy się niepowodzeniem, gdy nierozpoznane właściwości są zwracane w odpowiedzi interfejsu API. Domyślnie aplikacja powinna ignorować właściwości, które nie są zrozumiałe.

* Kod utrzymuje żądania interfejsu API i próbuje ponownie wysłać je do nowej wersji interfejsu API. Na przykład może się tak zdarzyć, jeśli aplikacja utrzymuje tokeny kontynuacji zwrócone przez interfejs API wyszukiwania (Aby uzyskać więcej informacji, `@search.nextPageParameters` poszukaj w [dokumentacji interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Jeśli jedna z tych sytuacji dotyczy użytkownika, może być konieczne odpowiednie zmiany kodu. W przeciwnym razie zmiany nie powinny być konieczne, chyba że chcesz zacząć korzystać z [nowych funkcji](#WhatsNew) w wersji 2019-05-06.

Jeśli uaktualniasz program z wersji zapoznawczej interfejsu API, powyższe ma również zastosowanie, ale trzeba również pamiętać, że niektóre funkcje w wersji zapoznawczej nie są dostępne w programie Version 2019-05-06:

* ["Bardziej podobne zapytania"](search-more-like-this.md)
* [Indeksowanie obiektów BLOB CSV](search-howto-index-csv-blobs.md)
* [Obsługa interfejsu API MongoDB dla indeksatorów Cosmos DB](search-howto-index-cosmosdb.md)

Jeśli kod korzysta z tych funkcji, nie będzie można przeprowadzić uaktualnienia do interfejsu API w wersji 2019-05-06 bez usuwania ich użycia.

> [!IMPORTANT]
> Interfejsy API w wersji zapoznawczej są przeznaczone do testowania i oceny i nie powinny być używane w środowiskach produkcyjnych.
> 

### <a name="upgrading-complex-types"></a>Uaktualnianie typów złożonych

Jeśli kod korzysta z typów złożonych ze starszym interfejsem API wersji zapoznawczej 2017-11-11 — wersja zapoznawcza lub 2016-09-01 — wersja zapoznawcza, istnieją pewne nowe i zmienione limity w wersji 2019-05-06, z których należy pamiętać:

+ Limity głębokości pól podrzędnych i liczby złożonych kolekcji na indeks zostały obniżone. Jeśli utworzono indeksy, które przekraczają te limity przy użyciu wersji zapoznawczej interfejsu API, każda próba zaktualizowania lub ponownego utworzenia ich przy użyciu interfejsu API w wersji 2019-05-06 zakończy się niepowodzeniem. Jeśli ma to zastosowanie, konieczne będzie ponowne zaprojektowanie schematu w celu dopasowania go do nowych limitów, a następnie odbudowanie indeksu.

+ W wersji API-Version 2019-05-06 występuje nowy limit liczby elementów złożonych kolekcji na dokument. W przypadku utworzenia indeksów z dokumentami, które przekraczają te limity przy użyciu wersji zapoznawczej interfejsu API, wszelkie próby ponownego indeksowania danych przy użyciu interfejsu API w wersji 2019-05-06 będą kończyć się niepowodzeniem. Jeśli ma to zastosowanie, należy zmniejszyć liczbę złożonych elementów kolekcji dla dokumentu przed ponownym indeksem danych.

Aby uzyskać więcej informacji, zobacz [limity usługi dla Azure Search](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Jak uaktualnić starą strukturę typu złożonego

Jeśli kod korzysta z typów złożonych z jedną ze starszych wersji interfejsu API podglądu, może być używany format definicji indeksu, który wygląda następująco:

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

W interfejsie API w wersji 2017-11-11-Preview wprowadzono nowszy format przypominający drzewo służący do definiowania pól indeksów. W nowym formacie każde pole złożone zawiera kolekcję pól, w której są zdefiniowane pola podrzędne. W interfejsie API w wersji 2019-05-06 ten nowy format jest używany wyłącznie i próba utworzenia lub zaktualizowania indeksu przy użyciu starego formatu zakończy się niepowodzeniem. Jeśli masz indeksy utworzone przy użyciu starego formatu, musisz użyć interfejsu API w wersji 2017-11-11-Preview, aby zaktualizować je do nowego formatu, zanim będzie można nimi zarządzać przy użyciu interfejsu API w wersji 2019-05-06.

Za pomocą interfejsu API w wersji 2017-11-11-Preview można zaktualizować indeksy "Flat" w nowym formacie:

1. Wykonanie żądania GET w celu pobrania indeksu. Jeśli jest już w nowym formacie, wszystko jest gotowe.

2. Przetłumacz indeks z formatu "Flat" na nowy format. Musisz napisać kod, ponieważ w momencie pisania nie ma dostępnego przykładowego kodu.

3. Wykonaj żądanie PUT, aby zaktualizować indeks do nowego formatu. Pamiętaj, aby nie zmieniać żadnych szczegółów indeksu, takich jak możliwości wyszukiwania/filtrowania pól, ponieważ nie jest to dozwolone przez interfejs API indeksu aktualizacji.

> [!NOTE]
> Nie jest możliwe zarządzanie indeksami utworzonymi przy użyciu starego formatu "Flat" z Azure Portal. Uaktualnij indeksy z "płaskiej" reprezentacji do "drzewa" w najkrótszej wygodie.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z dokumentacją interfejsu API REST usługi Azure Search. Jeśli wystąpią problemy, poproś nas o pomoc dotyczącą usługi [StackOverflow](https://stackoverflow.com/) lub [skontaktuj się z pomocą techniczną](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST usługi Search](https://docs.microsoft.com/rest/api/searchservice/)

