---
title: Przegląd języka OData — Azure Search
description: Przegląd języka OData dla filtrów, wybranych i kolejności dla zapytań Azure Search.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
manager: nitinme
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 0bd446b0ffa97a758f68a0f85889b13da6e3d8b0
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650031"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-search"></a>Omówienie języka OData dla `$filter`, `$orderby`i `$select` w Azure Search

Azure Search obsługuje podzestaw składni wyrażenia OData dla wyrażeń **$Filter**, **$OrderBy**i **$SELECT** . Wyrażenia filtru są oceniane podczas analizowania zapytania, ograniczając wyszukiwanie do określonych pól lub dodając kryteria dopasowywania używane podczas skanowania indeksów. Wyrażenia order-by są stosowane jako krok przetwarzania końcowego względem zestawu wyników w celu sortowania zwracanych dokumentów. Wybór wyrażeń określa, które pola dokumentu są uwzględniane w zestawie wyników. Składnia tych wyrażeń różni się od prostej lub [](query-simple-syntax.md) [pełnej](query-lucene-syntax.md) składni zapytań, która jest używana w parametrze **wyszukiwania** , chociaż występuje kilka nakładania się w składni dla pól, do których się odwołuje.

Ten artykuł zawiera omówienie języka wyrażeń OData używany w filtrach, wyrażeniach order-by i SELECT. Język jest prezentowany "u dołu", zaczynając od najbardziej podstawowych elementów i tworząc je. Składnia każdego z parametrów najwyższego poziomu jest opisana w osobnym artykule:

- [Składnia $filter](search-query-odata-filter.md)
- [Składnia $orderby](search-query-odata-orderby.md)
- [Składnia $select](search-query-odata-select.md)

Wyrażenia OData mają zakres od prostego do wysoce złożonej, ale wszystkie współdzielą elementy wspólne. Najbardziej podstawowe elementy wyrażenia OData w Azure Search są następujące:

- **Ścieżki pól**, które odwołują się do określonych pól indeksu.
- **Stałe**, które są wartościami literalnymi określonego typu danych.

> [!NOTE]
> Terminologia w Azure Search różni się od [standardu OData](https://www.odata.org/documentation/) na kilka sposobów. To, co dzwonimy do **pola** w Azure Search jest nazywana właściwością w protokole OData, i podobnie dla ścieżki **pola** w porównaniu z **ścieżką właściwości**. **Indeks** zawierający **dokumenty** w Azure Search jest bardziej ogólnie opisany w protokole OData jako **zestaw jednostek** zawierający **jednostki**. W tym odwołaniu jest używana terminologia Azure Search.

## <a name="field-paths"></a>Ścieżki pól

Następujący EBNF ([Extended back-Naura form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatykę ścieżek pól.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

Dostępny jest również interaktywny diagram składni:

> [!div class="nextstepaction"]
> [Diagram składni OData dla Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Zapoznaj się z informacjami o [składni wyrażenia OData dla Azure Search](search-query-odata-syntax-reference.md) , aby uzyskać pełną EBNF.

Ścieżka pola składa się z jednego lub więcej **identyfikatorów** rozdzielonych ukośnikami. Każdy identyfikator jest sekwencją znaków, które muszą zaczynać się literą lub podkreśleniem ASCII i zawierać tylko litery ASCII, cyfry i znaki podkreślenia. Litery mogą być wielkie lub małe.

Identyfikator może odwoływać się do nazwy pola lub **zmiennej zakresu** w kontekście [wyrażenia kolekcji](search-query-odata-collection-operators.md) (`any` lub `all`) w filtrze. Zmienna zakresu jest jak zmienna pętli, która reprezentuje bieżący element kolekcji. W przypadku złożonych kolekcji ta zmienna reprezentuje obiekt, dlatego można użyć ścieżek pól do odwoływania się do podpól zmiennej. Jest to analogiczny do notacji kropki w wielu językach programowania.

Przykłady ścieżek pól przedstawiono w poniższej tabeli:

| Ścieżka pola | Opis |
| --- | --- |
| `HotelName` | Odwołuje się do pola najwyższego poziomu indeksu |
| `Address/City` | Odwołuje się `City` do podpola złożonego pola w indeksie; `Address` jest typu`Edm.ComplexType` w tym przykładzie |
| `Rooms/Type` | Odwołuje się `Type` do pola podrzędnego złożonego pola kolekcji w indeksie; `Rooms` jest typu`Collection(Edm.ComplexType)` w tym przykładzie |
| `Stores/Address/Country` | Odwołuje się `Country` do podpola `Address` w polu podrzędnym złożonego pola kolekcji w indeksie; `Stores` jest typu `Collection(Edm.ComplexType)` i jest`Address` typu`Edm.ComplexType` w tym przykładzie |
| `room/Type` | Odwołuje się `Type` do podpola `room` zmiennej zakresu, na przykład w wyrażeniu filtru`Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Odwołuje się `Country` do podpola `Address` `store` podrzędnego podpola zmiennej zakresu, na przykład w wyrażeniu filtru.`Stores/any(store: store/Address/Country eq 'Canada')` |

Znaczenie ścieżki pola różni się w zależności od kontekstu. W filtrach ścieżka pola odnosi się do wartości *pojedynczego wystąpienia* pola w bieżącym dokumencie. W innych kontekstach, takich jak **$OrderBy**, **$SELECT**lub w wyszukiwaniu w [polu Pełna składnia](query-lucene-syntax.md#bkmk_fields), ścieżka pola odnosi się do samego pola. Różnica ta ma pewne konsekwencje dla sposobu używania ścieżek pól w filtrach.

Rozważ użycie ścieżki `Address/City`pola. W filtrze odnosi się to do jednego miasta dla bieżącego dokumentu, takiego jak "San Francisco". W przeciwieństwie `Rooms/Type` odnosi się `Type` do podpola dla wielu pokojów (na przykład "Standardowa" w pierwszym pokoju, "Deluxe" dla drugiego pokoju itd.). Ponieważ `Rooms/Type` nie odwołuje się do *pojedynczego wystąpienia* podpola `Type`, nie można go używać bezpośrednio w filtrze. Zamiast tego, aby odfiltrować typ pokoju, należy użyć [wyrażenia lambda](search-query-odata-collection-operators.md) z zmienną zakresu, takiej jak:

    Rooms/any(room: room/Type eq 'deluxe')

W tym przykładzie zmienna `room` zakresu pojawia się `room/Type` w ścieżce pola. W ten sposób `room/Type` , odnosi się do typu bieżącego pokoju w bieżącym dokumencie. Jest to pojedyncze wystąpienie `Type` podpola, dlatego może być używane bezpośrednio w filtrze.

### <a name="using-field-paths"></a>Używanie ścieżek pól

Ścieżki pól są używane w wielu parametrach [interfejsu API Azure Search](https://docs.microsoft.com/rest/api/searchservice/). W poniższej tabeli wymieniono wszystkie miejsca, w których można ich używać oraz ograniczenia dotyczące ich użycia:

| interfejs API | Nazwa parametru | Ograniczenia |
| --- | --- | --- |
| [Utwórz](https://docs.microsoft.com/rest/api/searchservice/create-index) lub [zaktualizuj](https://docs.microsoft.com/rest/api/searchservice/update-index) indeks | `suggesters/sourceFields` | Brak |
| [Utwórz](https://docs.microsoft.com/rest/api/searchservice/create-index) lub [zaktualizuj](https://docs.microsoft.com/rest/api/searchservice/update-index) indeks | `scoringProfiles/text/weights` | Może odwoływać się tylko do pól z **możliwością wyszukiwania** |
| [Utwórz](https://docs.microsoft.com/rest/api/searchservice/create-index) lub [zaktualizuj](https://docs.microsoft.com/rest/api/searchservice/update-index) indeks | `scoringProfiles/functions/fieldName` | Może odwoływać się tylko do pól z możliwością **filtrowania** |
| [Wyszukiwanie](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search`gdy `queryType` jest`full` | Może odwoływać się tylko do pól z **możliwością wyszukiwania** |
| [Wyszukiwanie](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Może odwoływać się tylko do pól **kroju** |
| [Wyszukiwanie](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Może odwoływać się tylko do pól z **możliwością wyszukiwania** |
| [Wyszukiwanie](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Może odwoływać się tylko do pól z **możliwością wyszukiwania** |
| [Sugeruj](https://docs.microsoft.com/rest/api/searchservice/suggestions) i [Autouzupełnianie](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Może odwoływać się tylko do pól, które są [](index-add-suggesters.md) częścią sugestii |
| [Wyszukaj](https://docs.microsoft.com/rest/api/searchservice/search-documents), [Sugeruj](https://docs.microsoft.com/rest/api/searchservice/suggestions)i [Autouzupełnianie](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Może odwoływać się tylko do pól z możliwością **filtrowania** |
| [Wyszukaj](https://docs.microsoft.com/rest/api/searchservice/search-documents) i [Sugeruj](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Może odwoływać się tylko do pól do **sortowania** |
| [Wyszukiwanie](https://docs.microsoft.com/rest/api/searchservice/search-documents), [sugerowanie](https://docs.microsoft.com/rest/api/searchservice/suggestions)i [Wyszukiwanie](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Może odwoływać się tylko do pól do **pobierania** |

## <a name="constants"></a>Stałe

Stałe w protokole OData są wartościami literalnymi dla danego typu [Entity Data Model](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM). Zobacz [obsługiwane typy danych](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) , aby uzyskać listę obsługiwanych typów w Azure Search. Stałe typów kolekcji nie są obsługiwane.

W poniższej tabeli przedstawiono przykłady stałych dla każdego z typów danych obsługiwanych przez Azure Search:

| Typ danych | Przykładowe stałe |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

Następujący EBNF ([Extended back-Naura form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatykę dla większości stałych przedstawionych w powyższej tabeli. Gramatykę typów geoprzestrzennych można znaleźć w funkcjach [geograficznych OData w Azure Search](search-query-odata-geo-spatial-functions.md).

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'
```

Dostępny jest również interaktywny diagram składni:

> [!div class="nextstepaction"]
> [Diagram składni OData dla Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Zapoznaj się z informacjami o [składni wyrażenia OData dla Azure Search](search-query-odata-syntax-reference.md) , aby uzyskać pełną EBNF.

## <a name="building-expressions-from-field-paths-and-constants"></a>Kompilowanie wyrażeń ze ścieżek pól i stałych

Ścieżki i stałe pól są najbardziej podstawową częścią wyrażenia OData, ale są już całkowicie pełnymi wyrażeniami. W rzeczywistości parametr **$SELECT** w Azure Search ma wartość Nothing, ale rozdzieloną przecinkami listę ścieżek pól i **$OrderBy** nie jest znacznie bardziej skomplikowany niż **$SELECT**. Jeśli chcesz mieć pole typu `Edm.Boolean` w indeksie, możesz nawet napisać filtr, który ma wartość Nothing, ale ścieżkę do tego pola. Stałe `true` i`false` są analogiczne do prawidłowych filtrów.

Jednak większość czasu potrzebuje bardziej złożonych wyrażeń, które odwołują się do więcej niż jednego pola i stałej. Wyrażenia te są tworzone na różne sposoby w zależności od parametru.

Następujący EBNF ([Extended back-Naura form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatykę dla parametrów **$Filter**, **$OrderBy**i **$SELECT** . Są one tworzone na podstawie prostszych wyrażeń odwołujących się do ścieżek pól i stałych:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

Dostępny jest również interaktywny diagram składni:

> [!div class="nextstepaction"]
> [Diagram składni OData dla Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Zapoznaj się z informacjami o [składni wyrażenia OData dla Azure Search](search-query-odata-syntax-reference.md) , aby uzyskać pełną EBNF.

Parametry **$OrderBy** i **$SELECT** są rozdzielanymi przecinkami listami prostszych wyrażeń. **$Filter** parametr jest wyrażeniem logicznym, które składa się z prostszych wyrażeń podrzędnych. Te wyrażenia podrzędne są łączone za pomocą operatorów logicznych, takich [ `and`jak `or`,, `not`i ](search-query-odata-logical-operators.md), operatorów porównania, takich jak [ `eq`, `lt` `gt`,, i tak dalej](search-query-odata-comparison-operators.md), i kolekcji Operatory takie jak [ `any` i `all` ](search-query-odata-collection-operators.md).

Parametry **$Filter**, **$OrderBy**i **$SELECT** zostały omówione bardziej szczegółowo w następujących artykułach:

- [Składnia $filter OData w Azure Search](search-query-odata-filter.md)
- [Składnia $orderby OData w Azure Search](search-query-odata-orderby.md)
- [Składnia $select OData w Azure Search](search-query-odata-select.md)

## <a name="see-also"></a>Zobacz także  

- [Nawigacja aspektowa w Azure Search](search-faceted-navigation.md)
- [Filtry w Azure Search](search-filters.md)
- [Wyszukaj dokumenty &#40;Azure Search interfejs API REST usługi&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Składnia zapytań Lucene](query-lucene-syntax.md)
- [Prosta Składnia zapytania w Azure Search](query-simple-syntax.md)
