---
title: Odwołanie operatora porównania OData
titleSuffix: Azure Cognitive Search
description: Składnia i dokumentacja referencyjna dotycząca korzystania z operatorów porównania OData (EQ, ne, gt, lt, GE i Le) w usłudze Azure Wyszukiwanie poznawcze zapytania.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: 62c8c93e07326e776cbe089042abc481544794bc
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113223"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>Operatory porównania OData w usłudze Azure Wyszukiwanie poznawcze — `eq`, `ne`, `gt`, `lt`, `ge`i `le`

Najbardziej podstawową operacją w [wyrażeniu filtru OData](query-odata-filter-orderby-syntax.md) na platformie Azure wyszukiwanie poznawcze jest porównanie pola z daną wartością. Dwa typy porównania są możliwym porównaniem równości i porównywaniem zakresu. Aby porównać pole z wartością stałą, można użyć następujących operatorów:

Operatory równości:

- `eq`: testowanie, czy pole jest **równe** wartości stałej
- `ne`: testowanie, czy pole **nie jest równe** wartości stałej

Operatory zakresu:

- `gt`: testowanie, czy pole jest **większe niż** wartość stała
- `lt`: Sprawdź, czy pole jest **mniejsze niż** wartość stała
- `ge`: Sprawdź, czy pole jest **większe lub równe** wartości stałej
- `le`: Sprawdź, czy pole jest **mniejsze niż lub równe** wartości stałej

Można użyć operatorów zakresu w połączeniu z [operatorami logicznymi](search-query-odata-logical-operators.md) , aby sprawdzić, czy pole znajduje się w określonym zakresie wartości. Zobacz [przykłady](#examples) w dalszej części tego artykułu.

> [!NOTE]
> Jeśli wolisz, możesz umieścić wartość stałą po lewej stronie operatora i nazwę pola po prawej stronie. Dla operatorów zakresu, znaczenie porównania zostaje cofnięte. Na przykład, jeśli stała wartość jest po lewej stronie, `gt` przetestować, czy wartość stała jest większa niż pole. Można również użyć operatorów porównania, aby porównać wynik funkcji, np. `geo.distance`, z wartością. W przypadku funkcji logicznych, takich jak `search.ismatch`, porównanie wyniku `true` lub `false` jest opcjonalne.

## <a name="syntax"></a>Składnia

Następujący EBNF ([Extended back-Naura form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatykę wyrażenia OData, które korzysta z operatorów porównania.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

Dostępny jest również interaktywny diagram składni:

> [!div class="nextstepaction"]
> [Diagram składni OData dla Wyszukiwanie poznawcze platformy Azure](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Zapoznaj się z informacjami o [składni wyrażenia OData dla usługi Azure wyszukiwanie poznawcze](search-query-odata-syntax-reference.md) , aby uzyskać pełną EBNF.

Istnieją dwie formy wyrażeń porównania. Jedyną różnicą między nimi jest to, czy stała pojawia się po lewej lub prawej stronie operatora. Wyrażenie po drugiej stronie operatora musi być **zmienną** lub wywołaniem funkcji. Zmienna może być albo nazwą pola lub zmienną zakresu w przypadku [wyrażenia lambda](search-query-odata-collection-operators.md).

## <a name="data-types-for-comparisons"></a>Typy danych do porównania

Typy danych po obu stronach operatora porównania muszą być zgodne. Na przykład, jeśli po lewej stronie jest polem typu `Edm.DateTimeOffset`, po prawej stronie musi być stała daty i godziny. Numeryczne typy danych są bardziej elastyczne. Można porównać zmienne i funkcje dowolnego typu liczbowego ze stałymi dowolnego innego typu liczbowego z kilkoma ograniczeniami, zgodnie z opisem w poniższej tabeli.

| Typ zmiennej lub funkcji | Typ stałej wartości | Ograniczenia |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Porównanie podlega [specjalnym regułom dla `NaN`](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Stała jest konwertowana na `Edm.Double`, co spowodowało utratę dokładności dla wartości o dużej wielkości |
| `Edm.Double` | `Edm.Int32` | Nie dotyczy |
| `Edm.Int64` | `Edm.Double` | Porównania do `NaN`, `-INF`lub `INF` są niedozwolone |
| `Edm.Int64` | `Edm.Int64` | Nie dotyczy |
| `Edm.Int64` | `Edm.Int32` | Stała jest konwertowana na `Edm.Int64` przed porównaniem |
| `Edm.Int32` | `Edm.Double` | Porównania do `NaN`, `-INF`lub `INF` są niedozwolone |
| `Edm.Int32` | `Edm.Int64` | Nie dotyczy |
| `Edm.Int32` | `Edm.Int32` | Nie dotyczy |

W przypadku porównań, które nie są dozwolone, takich jak porównywanie pola typu `Edm.Int64` do `NaN`, interfejs API REST platformy Azure Wyszukiwanie poznawcze zwróci błąd "HTTP 400: złe żądanie".

> [!IMPORTANT]
> Mimo że porównania typu liczbowego są elastyczne, zdecydowanie zalecamy zapisanie porównań w filtrach, aby wartość stała była tego samego typu danych jak zmienna lub funkcja, do której jest porównywana. Jest to szczególnie ważne w przypadku mieszania wartości zmiennoprzecinkowych i liczb całkowitych, w przypadku których niejawne konwersje mające na celu utratę dokładności są możliwe.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Specjalne przypadki `null` i `NaN`

W przypadku korzystania z operatorów porównania ważne jest, aby pamiętać, że wszystkie pola niebędące kolekcjami w usłudze Azure Wyszukiwanie poznawcze mogą być `null`. W poniższej tabeli przedstawiono wszystkie możliwe wyniki dla wyrażenia porównania, w którym można `null`obu stron:

| Operator | Wynik, gdy tylko pole lub zmienna są `null` | Wynik, gdy jest `null` tylko stała | Wynik, gdy pole lub zmienna i stała są `null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: błąd nieprawidłowego żądania | HTTP 400: błąd nieprawidłowego żądania |
| `lt` | `false` | HTTP 400: błąd nieprawidłowego żądania | HTTP 400: błąd nieprawidłowego żądania |
| `ge` | `false` | HTTP 400: błąd nieprawidłowego żądania | HTTP 400: błąd nieprawidłowego żądania |
| `le` | `false` | HTTP 400: błąd nieprawidłowego żądania | HTTP 400: błąd nieprawidłowego żądania |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

Podsumowując, `null` jest równa tylko siebie i nie jest mniejsza ani większa niż jakakolwiek inna wartość.

Jeśli indeks zawiera pola typu `Edm.Double` i przekazano wartości `NaN` do tych pól, należy uwzględnić to podczas pisania filtrów. Platforma Azure Wyszukiwanie poznawcze implementuje standard IEEE 754 do obsługi `NaN` wartości, a porównania z takimi wartościami tworzą niejasne wyniki, jak pokazano w poniższej tabeli.

| Operator | Wynik w przypadku `NaN` co najmniej jednego operandu |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Podsumowując, `NaN` nie jest równa żadnej wartości, łącznie z samym sobą.

### <a name="comparing-geo-spatial-data"></a>Porównywanie danych przestrzennych geograficznie

Nie można bezpośrednio porównać pola typu `Edm.GeographyPoint` z wartością stałą, ale można użyć funkcji `geo.distance`. Ta funkcja zwraca wartość typu `Edm.Double`, więc można ją porównać z stałą numeryczną w celu filtrowania na podstawie odległości od stałych współrzędnych geograficznych. Zapoznaj się z poniższymi [przykładami](#examples) .

### <a name="comparing-string-data"></a>Porównywanie danych ciągu

Ciągi można porównać w filtrach w celu uzyskania dokładnych dopasowań przy użyciu operatorów `eq` i `ne`. W tych porównaniach jest rozróżniana wielkość liter.

## <a name="examples"></a>Przykłady

Dopasuj dokumenty, w których pole `Rating` ma wartość od 3 do 5 włącznie:

    Rating ge 3 and Rating le 5

Dopasuj dokumenty, w których pole `Location` jest mniejsze niż 2 kilometry od danego położenia i długości geograficznej:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Dopasuj dokumenty, w których pole `LastRenovationDate` ma wartość większą lub równą 1 stycznia, 2015, północ czasu UTC:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Dopasuj dokumenty, w których pole `Details/Sku` nie jest `null`:

    Details/Sku ne null

Dopasuj dokumenty dla hoteli, w których co najmniej jedno pomieszczenie ma typ "Pokój Deluxe", gdzie ciąg pola `Rooms/Type` dopasowuje dokładnie filtr:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>Następne kroki  

- [Filtry na platformie Azure Wyszukiwanie poznawcze](search-filters.md)
- [Omówienie języka wyrażeń OData dla platformy Azure Wyszukiwanie poznawcze](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Wyszukiwanie poznawcze](search-query-odata-syntax-reference.md)
- [Wyszukaj dokumenty &#40;w interfejsie API REST usługi Azure wyszukiwanie poznawcze&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
