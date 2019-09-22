---
title: Typy i kompozycje zapytań — Azure Search
description: Podstawowe informacje na temat tworzenia zapytania wyszukiwania w Azure Search, przy użyciu parametrów do filtrowania, wybierania i sortowania wyników.
author: HeidiSteen
manager: nitinme
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 4646cb30ef7602da990e24f923c8eceada4debd0
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178016"
---
# <a name="query-types-and-composition-in-azure-search"></a>Typy i kompozycje zapytań w Azure Search

W Azure Search zapytanie jest pełną specyfikacją operacji rundy. Parametry żądania zawierają kryteria dopasowywania do znajdowania dokumentów w indeksie, które pola mają być uwzględniane lub wykluczane, instrukcje wykonywania przenoszone do aparatu i dyrektywy służące do kształtowania odpowiedzi. Nieokreślone (`search=*`), zapytanie jest wykonywane dla wszystkich pól z możliwością wyszukiwania jako operacji wyszukiwania pełnotekstowego, zwracając nieoceniony zestaw wyników w dowolnej kolejności.

Poniższy przykład to zapytanie reprezentatywne skonstruowane w [interfejsie API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents). Ten przykład wskazuje na [indeks demonstracyjny hoteli](search-get-started-portal.md) i zawiera wspólne parametry.

```
{
    "queryType": "simple" 
    "search": "+New York +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

+ **`queryType`** Ustawia parser, który jest [domyślnym prostym analizatorem zapytań](search-query-simple-examples.md) (optymalnym dla wyszukiwania pełnotekstowego) lub [pełnym parserem zapytań Lucene](search-query-lucene-examples.md) , używanym do zaawansowanych konstrukcji zapytań, takich jak wyrażenia regularne, wyszukiwanie w sąsiedztwie, rozmyte i wieloznaczne wyszukiwanie, do nazwy ogranicznik.

+ **`search`** zawiera kryteria dopasowywania, zazwyczaj tekst, ale często towarzyszy operatorom logicznym. Pojedyncze warunki autonomiczne to zapytania warunkowe. Zapytania o wiele części zawarte w cudzysłowie są zapytaniami *fraz kluczowych* . Wyszukiwanie może być niezdefiniowane, jak **`search=*`** w, ale prawdopodobnie zawiera warunki, frazy i operatory podobne do tego, co jest wyświetlane w przykładzie.

+ **`searchFields`** ogranicza wykonywanie zapytania do określonych pól. Każde pole, które jest przypisane do *wyszukiwania* w schemacie indeksu, jest kandydatem dla tego parametru.

Odpowiedzi są również dostosowane do parametrów, które zostały uwzględnione w zapytaniu. W przykładzie zestaw wyników zawiera pola wymienione w **`select`** instrukcji. W instrukcji $select można używać tylko pól oznaczonych jako możliwe do *pobierania* . Ponadto w tym zapytaniu są zwracane tylko **`top`** 10 trafień **`count`** , co oznacza, ile dokumentów jest zgodnych z ogólnymi, co może być większe niż to, co są zwracane. W tej kwerendzie wiersze są sortowane według klasyfikacji w kolejności malejącej.

W Azure Search wykonywanie zapytania jest zawsze w odniesieniu do jednego indeksu uwierzytelnianego przy użyciu klucza API-Key dostarczonego w żądaniu. W pozostałych, oba są dostępne w nagłówkach żądania.

### <a name="how-to-run-this-query"></a>Jak uruchomić to zapytanie

Aby wykonać to zapytanie, użyj [Eksploratora wyszukiwania i indeksu demonstracji hoteli](search-get-started-portal.md). 

Ten ciąg zapytania można wkleić do paska wyszukiwania Eksploratora:`search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Jak operacje zapytań są włączane przez indeks

Projekt indeksu i projekt zapytania są ściśle sprzężone w Azure Search. Podstawowym faktem, że wiadomo, że *schemat indeksu*, z atrybutami każdego pola, określa rodzaj zapytania, które można skompilować. 

Atrybuty indeksu w polu ustawiają dozwolone operacje — czy pole można wyszukiwać w indeksie, *pobrać* z wyników, do *sortowania*, *filtrować*i tak dalej. W przykładowym ciągu zapytania działa `"$orderby": "Rating"` tylko, ponieważ pole Rating jest oznaczone jako do *sortowania* w schemacie indeksu. 

![Definicja indeksu dla przykładu hotelu](./media/search-query-overview/hotel-sample-index-definition.png "Definicja indeksu dla przykładu hotelu")

Powyższy zrzut ekranu jest częściową listą atrybutów indeksu dla przykładu hoteli. Cały schemat indeksu można wyświetlić w portalu. Aby uzyskać więcej informacji na temat atrybutów indeksu, zobacz [create index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index).

> [!Note]
> Niektóre funkcje zapytań są włączone na poziomie indeksu, a nie na podstawie poszczególnych pól. Te możliwości obejmują: [mapy synonimów](search-synonyms.md), [Niestandardowe analizatory](index-add-custom-analyzers.md), [konstrukcje sugerujące (dla autouzupełniania i sugerowanych zapytań)](index-add-suggesters.md), [logika oceniania dla wyników rankingu](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>Elementy żądania zapytania

Zapytania są zawsze kierowane w pojedynczym indeksie. Nie można przyłączyć indeksów ani tworzyć niestandardowych lub tymczasowych struktur danych jako obiektów docelowych zapytań. 

Wymagane elementy w żądaniu zapytania obejmują następujące składniki:

+ Punkt końcowy usługi i kolekcja dokumentów indeksu, wyrażone jako adres URL zawierający stałe i zdefiniowane przez użytkownika składniki: **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`** (Tylko REST) jest konieczne, ponieważ wiele wersji interfejsu API jest dostępnych przez cały czas. 
+ **`api-key`** , zapytanie lub klucz API-Key, uwierzytelnia żądanie do usługi.
+ **`queryType`** — prosta lub pełna, która może zostać pominięta, jeśli używasz wbudowanej domyślnej składni prostej.
+ **`search`** lub **`filter`** zawiera kryteria dopasowania, które można określić, jeśli chcesz przeprowadzić puste wyszukiwanie. Oba typy zapytań są omówione w sekcji prostego analizatora, ale nawet zapytania zaawansowane wymagają parametru wyszukiwania do przekazywania złożonych wyrażeń zapytania.

Wszystkie inne parametry wyszukiwania są opcjonalne. Aby zapoznać się z pełną listą atrybutów, zobacz [create index (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index). Aby lepiej sprawdzić, jak parametry są używane podczas przetwarzania, zobacz [jak wyszukiwanie pełnotekstowe działa w Azure Search](search-lucene-query-architecture.md).

## <a name="choose-apis-and-tools"></a>Wybierz interfejsy API i narzędzia

W poniższej tabeli przedstawiono interfejsy API i oparte na narzędziach podejścia do przesyłania zapytań.

| Metodologia | Opis |
|-------------|-------------|
| [Eksplorator wyszukiwania (Portal)](search-explorer.md) | Udostępnia pasek wyszukiwania i opcje dotyczące opcji indeks i wersja interfejsu API. Wyniki są zwracane jako dokumenty JSON. Zalecane do eksploracji, testowania i weryfikacji. <br/>[Dowiedz się więcej.](search-get-started-portal.md#query-index) | 
| [Ogłoś lub inne narzędzia REST](search-get-started-postman.md) | Narzędzia do testowania sieci Web to doskonały wybór w zakresie opracowywania wywołań REST. Interfejs API REST obsługuje wszystkie możliwe operacje w Azure Search. W tym artykule dowiesz się, jak skonfigurować nagłówek i treść żądania HTTP w celu wysyłania żądań do Azure Search.  |
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Klient, który może służyć do wykonywania zapytań względem indeksu Azure Search.  <br/>[Dowiedz się więcej.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Wyszukaj dokumenty (interfejs API REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | Pobieranie lub OGŁASZAnie metod na indeksie przy użyciu parametrów zapytania dla dodatkowych danych wejściowych.  |

## <a name="choose-a-parser-simple--full"></a>Wybierz parser: prosty | szczegółowe

Azure Search znajduje się na szczycie Apache Lucene i umożliwia wybór między dwoma analizatorami zapytań obsługujących typowe i wyspecjalizowane zapytania. Żądania przy użyciu prostego analizatora są formułowane przy użyciu [prostej składni zapytania](query-simple-syntax.md), wybranej jako wartości domyślnej dla jego szybkości i skuteczności w bezpłatnych zapytaniach tekstowych formularza. Ta składnia obsługuje wiele typowych operatorów wyszukiwania, w tym operatory AND, OR, NOT, phrase, sufiks i pierwszeństwo.

[Pełna składnia zapytań Lucene](query-Lucene-syntax.md#bkmk_syntax), którą można włączyć po `queryType=full` dodaniu żądania, uwidacznia powszechnie przyjęty i wyraźny język zapytań opracowany w ramach oprogramowania [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Pełna składnia rozszerza prostą składnię. Wszystkie zapytania zapisane dla prostej składni są uruchamiane w ramach pełnego analizatora Lucene. 

Poniższe przykłady ilustrują punkt: te same zapytania, ale z różnymi ustawieniami querytype, dają różne wyniki. W pierwszym zapytaniu polecenie `^3` After `historic` jest traktowane jako część wyszukiwanego terminu. Górny wynik tego zapytania to "Marquis plac & Suites", który ma *Ocean* w opisie

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

To samo zapytanie używające pełnego analizatora Lucene `^3` interpretuje jako Detonator długoterminowy jako pole. Przełączenie analizatorów zmienia rangę, z wynikami zawierającymi termin *historyczny* w górnej części.

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Typy zapytań

Azure Search obsługuje szeroką gamę typów zapytań. 

| Typ zapytania | Sposób użycia | Przykłady i więcej informacji |
|------------|--------|-------------------------------|
| Wyszukiwanie tekstu w dowolnym formacie | Parametry wyszukiwania i parser| Wyszukiwanie pełnotekstowe skanuje jeden lub więcej terminów we wszystkich polach z *możliwością wyszukiwania* w indeksie i działa w taki sposób, aby aparat wyszukiwania, taki jak Google lub Bing, mógł działać. Przykład we wprowadzeniu jest wyszukiwaniem pełnotekstowym.<br/><br/>Wyszukiwanie pełnotekstowe jest poddawana analizie tekstu przy użyciu standardowego analizatora Lucene (domyślnie) w przypadku małych i średnich wyrazów, Usuń słowa Stop podobne do "". Wartość domyślną można zastąpić [analizatorami w językach innych niż angielski](index-add-language-analyzers.md#language-analyzer-list) lub [wyspecjalizowanymi analizatorami niezależny od](index-add-custom-analyzers.md#AnalyzerTable) , które modyfikują analizę tekstu. Przykładem jest [słowo kluczowe](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) , które traktuje całą zawartość pola jako pojedynczy token. Jest to przydatne w przypadku danych, takich jak kody ZIP, identyfikatory i nazwy produktów. | 
| Wyszukiwanie filtrowane | [Wyrażenie filtru OData](query-odata-filter-orderby-syntax.md) i każdy parser | Zapytania filtrujące obliczają wyrażenie logiczne dla wszystkich pól z możliwością *filtrowania* w indeksie. W przeciwieństwie do wyszukiwania, zapytanie filtru dopasowuje dokładną zawartość pola, w tym uwzględnianie wielkości liter w polach ciągów. Inną różnicą jest to, że zapytania filtru są wyrażane w składni protokołu OData. <br/>[Przykład wyrażenia filtru](search-query-simple-examples.md#example-3-filter-queries) |
| Wyszukiwanie geograficzne | [Typ EDM. geographyPoint względem](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) w polu, wyrażenie filtru i parser | Współrzędne przechowywane w polu z obiektem EDM. geographyPoint względem są używane dla kontrolek wyszukiwania "Znajdź w pobliżu" lub "mapowania". <br/>[Przykład wyszukiwania geograficznego](search-query-simple-examples.md#example-5-geo-search)|
| Wyszukiwanie zakresu | wyrażenie filtru i prosty parser | W Azure Search, zapytania zakresu są kompilowane przy użyciu parametru filtru. <br/>[Przykład filtru zakresu](search-query-simple-examples.md#example-4-range-filters) | 
| [Wyszukiwanie polowe](query-lucene-syntax.md#bkmk_fields) | Parametr wyszukiwania i pełny analizator składni | Utwórz złożone wyrażenie zapytania określające jedno pole. <br/>[Przykład wyszukiwania w polu](search-query-lucene-examples.md#example-2-fielded-search) |
| [Wyszukiwanie rozmyte](query-lucene-syntax.md#bkmk_fuzzy) | Parametr wyszukiwania i pełny analizator składni | Dopasowuje się do warunków mających podobną konstrukcję lub pisownię. <br/>[Przykład wyszukiwania rozmytego](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [Wyszukiwanie w sąsiedztwie](query-lucene-syntax.md#bkmk_proximity) | Parametr wyszukiwania i pełny analizator składni | Znajduje terminy, które są blisko siebie w dokumencie. <br/>[Przykład wyszukiwania w sąsiedztwie](search-query-lucene-examples.md#example-4-proximity-search) |
| [zwiększenie warunków](query-lucene-syntax.md#bkmk_termboost) | Parametr wyszukiwania i pełny analizator składni | Określa wyższy poziom dokumentu, jeśli zawiera on podwyższony termin względem innych, które nie są. <br/>[Przykład zwiększania warunków](search-query-lucene-examples.md#example-5-term-boosting) |
| [Wyszukiwanie wyrażeń regularnych](query-lucene-syntax.md#bkmk_regex) | Parametr wyszukiwania i pełny analizator składni | Dopasowuje się w oparciu o zawartość wyrażenia regularnego. <br/>[Przykład wyrażenia regularnego](search-query-lucene-examples.md#example-6-regex) |
|  [Wyszukiwanie przy użyciu symboli wieloznacznych lub prefiksów](query-lucene-syntax.md#bkmk_wildcard) | Parametr wyszukiwania i pełny analizator składni | Dopasowuje się na podstawie prefiksu i`~`tyldy () lub`?`pojedynczego znaku (). <br/>[Przykład wyszukiwania symboli wieloznacznych](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Zarządzanie wynikami wyszukiwania 

Wyniki zapytania są przesyłane strumieniowo jako dokumenty JSON w interfejsie API REST, ale jeśli korzystasz z interfejsów API platformy .NET, Serializacja jest wbudowana. Wyniki można kształtować przez ustawienie parametrów zapytania, wybierając określone pola dla odpowiedzi.

Parametry zapytania mogą służyć do tworzenia struktury zestawu wyników w następujący sposób:

+ Ograniczanie lub przetwarzanie wsadowe liczby dokumentów w wynikach (domyślnie 50)
+ Wybieranie pól do uwzględnienia w wynikach
+ Ustawianie kolejności sortowania
+ Dodawanie świateł trafień, aby zwrócić uwagę na pasujące warunki w treści wyników wyszukiwania

### <a name="tips-for-unexpected-results"></a>Porady dotyczące nieoczekiwanych wyników

Czasami substancja, a nie struktura wyników, nie są oczekiwane. Gdy wyniki zapytania nie są oczekiwane, możesz spróbować wykonać te modyfikacje kwerendy, aby zobaczyć, czy rezultaty rosną:

+ Zmień **`searchMode=any`** (domyślnie **`searchMode=all`** ), aby wymagać dopasowania wszystkich kryteriów zamiast kryteriów. Jest to szczególnie prawdziwe, gdy operatory logiczne są uwzględnione w zapytaniu.

+ Zmień technikę zapytania, jeśli konieczne jest przeprowadzenie analizy tekstu lub leksykalnia, ale typ zapytania wyklucza przetwarzanie lingwistyczne. W wyszukiwaniu pełnotekstowym tekst lub analiza leksykalna są Autokorekty w przypadku błędów pisowni, formularzy wyrazów z pojedynczą plural, a nawet nieregularnych czasowników lub rzeczowników. W przypadku niektórych zapytań, takich jak rozmyte lub wyszukiwanie przy użyciu symboli wieloznacznych, analiza tekstu nie jest częścią potoku analizowania zapytań. W przypadku niektórych scenariuszy wyrażenia regularne zostały użyte jako obejście. 

### <a name="paging-results"></a>Stronicowanie wyników
Usługa Azure Search ułatwia implementowanie stronicowania wyników wyszukiwania. Korzystając z **`top`** parametrów i **`skip`** , można bezproblemowo wystawiać żądania wyszukiwania, które umożliwiają otrzymanie całkowitego zestawu wyników wyszukiwania w sposób umożliwiający zarządzanie, uporządkowane podzestawy, które łatwo umożliwiają dobre metody interfejsu użytkownika wyszukiwania. Razem z mniejszymi podzbiorami wyników można również odbierać liczbę dokumentów w całym zbiorze wyników wyszukiwania.

Więcej informacji o stronicowaniu wyników wyszukiwania można znaleźć w artykule [How to page search results in Azure Search](search-pagination-page-layout.md) (Sposoby stronicowania wyników wyszukiwania w usłudze Azure Search).

### <a name="ordering-results"></a>Porządkowanie wyników
Usługa Azure Search udostępnia możliwość przekazania wyników zapytania wyszukiwania uporządkowanych według wartości w określonym polu. Domyślnie usługa Azure Search porządkuje wyniki w oparciu o rangę wyniku wyszukiwania poszczególnych dokumentów, który jest określany na podstawie wagi [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Jeśli chcesz, aby Azure Search zwracał wyniki uporządkowane według wartości innej niż wynik wyszukiwania, możesz użyć **`orderby`** parametru wyszukiwania. Można określić wartość **`orderby`** parametru, aby uwzględnić nazwy pól i wywołania [ **`geo.distance()` funkcji**](query-odata-filter-orderby-syntax.md) dla wartości geoprzestrzennych. Po każdym wyrażeniu można następować `asc` , aby wskazać, że wyniki są żądane w kolejności rosnącej, i **`desc`** wskazywać, że wyniki są żądane w kolejności malejącej. Domyślnie jest stosowana kolejność rosnąca.


### <a name="hit-highlighting"></a>Wyróżnianie trafień
W Azure Search, podkreślając dokładne części wyników wyszukiwania, które pasują do zapytania wyszukiwania **`highlight`** , przy użyciu parametrów, **`highlightPreTag`** i **`highlightPostTag`** . Można wskazać, w których polach *z możliwością wyszukiwania* ma zostać wyróżniony dopasowany tekst, a także dokładnie określić tagi ciągów, które mają zostać dodane na początku i na końcu dopasowanego tekstu zwracanego przez usługę Azure Search.

## <a name="see-also"></a>Zobacz także

+ [Jak działa wyszukiwanie pełnotekstowe w Azure Search (architektura analizy zapytań)](search-lucene-query-architecture.md)
+ [Eksplorator wyszukiwania](search-explorer.md)
+ [Jak wykonywać zapytania w programie .NET](search-query-dotnet.md)
+ [Jak wykonywać zapytania w usłudze REST](search-create-index-rest-api.md)
