---
title: Użyj pełnej składni zapytań Lucene
titleSuffix: Azure Cognitive Search
description: Składnia zapytań Lucene dla wyszukiwania rozmytego, wyszukiwania w sąsiedztwie, zwiększania warunków, wyszukiwania wyrażeń regularnych i wyszukiwania symboli wieloznacznych w usłudze Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 745be21c2a7a09a09fdbbfd57a305d09a4fac3ed
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793445"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Użyj "pełnej" składni wyszukiwania Lucene (zapytania zaawansowane w usłudze Azure Wyszukiwanie poznawcze)

Podczas konstruowania zapytań dotyczących usługi Azure Wyszukiwanie poznawcze można zastąpić domyślny [prosty Analizator zapytań](query-simple-syntax.md) , używając [analizatora zapytań rozległych Lucene w usłudze Azure wyszukiwanie poznawcze](query-lucene-syntax.md) do tworzenia specjalnych i zaawansowanych definicji zapytań. 

Parser Lucene obsługuje złożone konstrukcje zapytań, takie jak zapytania o zakresie pól, Wyszukiwanie rozmyte i symbole wieloznaczne, wyszukiwanie w sąsiedztwie, zwiększanie terminów i wyszukiwanie wyrażeń regularnych. Dodatkowa moc jest dostarczana z dodatkowymi wymaganiami dotyczącymi przetwarzania, dlatego należy oczekiwać nieco dłuższego czasu wykonania. W tym artykule można zapoznać się z przykładami pokazującymi operacje zapytania dostępne w przypadku korzystania z pełnej składni.

> [!Note]
> Wiele wyspecjalizowanych konstrukcji zapytań włączonych za pomocą pełnej składni zapytań Lucene nie jest w trakcie [analizowania tekstu](search-lucene-query-architecture.md#stage-2-lexical-analysis), co może być zaskakującee, jeśli oczekujesz rdzeni lub Lematyzacja. Analiza leksykalna jest wykonywana tylko w przypadku pełnych warunków (zapytania warunkowego zapytania lub frazy). Typy zapytań z niekompletnymi postanowieniami (zapytanie o prefiks, zapytanie symboli wieloznacznych, zapytanie rozmyte) są dodawane bezpośrednio do drzewa zapytań, pomijając etap analizy. Jedyne przekształcenie wykonane na niekompletnych terminach zapytania to lowercasing. 
>

## <a name="formulate-requests-in-postman"></a>Formułowanie żądań w programie Poster

Poniższe przykłady wykorzystują indeks wyszukiwania zadań NYC składający się z zadań dostępnych na podstawie zestawu danych dostarczonego przez [miasto z inicjatywy New York OpenData](https://opendata.cityofnewyork.us/) Initiative. Te dane nie powinny być uważane za bieżące ani ukończone. Indeks znajduje się w usłudze piaskownicy dostarczonej przez firmę Microsoft, co oznacza, że nie potrzebujesz subskrypcji platformy Azure ani usługi Azure Wyszukiwanie poznawcze do wypróbowania tych zapytań.

To, czego potrzebujesz, jest to Poster lub równoważne narzędzie do wystawiania żądań HTTP na potrzeby pobierania. Aby uzyskać więcej informacji, zobacz [Eksplorowanie z klientami REST](search-get-started-postman.md).

### <a name="set-the-request-header"></a>Ustawianie nagłówka żądania

1. W nagłówku żądania ustaw wartość **Typ zawartości** na `application/json`.

2. Dodaj **klucz API-Key**i ustaw go na następujący ciąg: `252044BE3886FE4A8E3BAA4F595114BB`. Jest to klucz zapytania dla usługi wyszukiwania piaskownicy hostującym indeks zadań NYC.

Po określeniu nagłówka żądania można użyć go ponownie dla wszystkich zapytań w tym artykule, zamieniając tylko ciąg **Search =** String. 

  ![Nagłówek żądania narzędzia Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Ustaw adres URL żądania

Żądanie jest poleceniem GET z adresem URL zawierającym punkt końcowy Wyszukiwanie poznawcze platformy Azure i ciąg wyszukiwania.

  ![Nagłówek żądania narzędzia Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

Kompozycja adresów URL ma następujące elementy:

+ **`https://azs-playground.search.windows.net/`** to usługa wyszukiwania piaskownicy obsługiwana przez zespół deweloperów systemu Azure wyszukiwanie poznawcze. 
+ **`indexes/nycjobs/`** to indeks zadań NYC w kolekcji indeksów tej usługi. W żądaniu wymagane są zarówno nazwę usługi, jak i indeks.
+ **`docs`** jest kolekcją dokumentów zawierającą całą zawartość przeszukiwaną. Klucz API Query-Key podany w nagłówku żądania działa tylko na operacjach odczytu dla kolekcji Documents.
+ **`api-version=2019-05-06`** ustawia interfejs API-Version, który jest parametrem wymaganym dla każdego żądania.
+ **`search=*`** jest ciągiem zapytania, który w początkowym zapytaniu ma wartość null, zwracając pierwsze wyniki 50 (domyślnie).

## <a name="send-your-first-query"></a>Wyślij pierwsze zapytanie

W ramach kroku weryfikacji Wklej następujące żądanie do GET i kliknij pozycję **Wyślij**. Wyniki są zwracane w postaci pełnych dokumentów JSON. Zwracane są całe dokumenty, które umożliwiają wyświetlanie wszystkich pól i wszystkich wartości.

Wklej ten adres URL do klienta REST jako krok walidacji i Wyświetl strukturę dokumentu.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=*
  ```

Ciąg zapytania, **`search=*`** , jest nieokreślonym wyszukiwaniem równym null lub pustemu wyszukiwaniu. Jest to najprostszy sposób wyszukiwania.

Opcjonalnie możesz dodać **`$count=true`** do adresu URL, aby zwrócić liczbę dokumentów pasujących do kryteriów wyszukiwania. W przypadku pustego ciągu wyszukiwania jest to wszystkie dokumenty w indeksie (około 2800 w przypadku zadań NYC).

## <a name="how-to-invoke-full-lucene-parsing"></a>Jak wywoływać pełne analizowanie Lucene

Dodaj **zapytania = Full** , aby wywołać pełną składnię zapytania, zastępując domyślną prostą składnię zapytania. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&search=*
```

We wszystkich przykładach w tym artykule określono parametr **querytype = Full** Search wskazujący, że Pełna składnia jest obsługiwana przez Analizator zapytań Lucene. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Przykład 1: zapytanie zakresu zapytania do listy pól

Ten pierwszy przykład nie jest specyficzny dla Lucene, ale prowadzimy do tego, aby wprowadzić pierwszą podstawową koncepcję zapytania: zakres pól. W tym przykładzie zakresy całego zapytania i odpowiedzi na kilka określonych pól. Znajomość sposobu tworzenia struktury odpowiedzi w formacie JSON jest ważna, gdy narzędzie jest dostępne po opublikowaniu lub w Eksploratorze wyszukiwania. 

W przypadku zwięzłości zapytanie odwołuje się tylko do pola *business_title* i określa tylko tytuły biznesowe, które są zwracane. Parametr **searchFields** ogranicza wykonywanie zapytania tylko do pola business_title i **wybiera** , które pola są uwzględniane w odpowiedzi.

### <a name="partial-query-string"></a>Częściowy ciąg zapytania

```http
&search=*&searchFields=business_title&$select=business_title
```

W tym samym zapytaniu z wieloma polami na liście rozdzielanej przecinkami.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

Spacje po przecinkach są opcjonalne.

> [!Tip]
> W przypadku korzystania z interfejsu API REST z kodu aplikacji nie należy zapominać o parametrach kodowania URL, takich jak `$select` i `searchFields`.

### <a name="full-url"></a>Pełny adres URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

Odpowiedź na to zapytanie powinna wyglądać podobnie do poniższego zrzutu ekranu.

  ![Przykładowa odpowiedź Poster](media/search-query-lucene-examples/postman-sample-results.png)

Być może zauważono wynik wyszukiwania w odpowiedzi. Jednolite Punktacja 1 występuje, gdy nie ma żadnej rangi, ponieważ wyszukiwanie nie było wyszukiwaniem pełnotekstowym lub nie zastosowano żadnych kryteriów. W przypadku wyszukiwania wartości null bez kryteriów wiersze są przywracane w dowolnej kolejności. Gdy dołączysz rzeczywiste kryteria wyszukiwania, wyniki wyszukiwania zostaną rozdzielone na znaczące wartości.

## <a name="example-2-fielded-search"></a>Przykład 2: wyszukiwanie polowe

Pełna składnia Lucene obsługuje określanie zakresu poszczególnych wyrażeń wyszukiwania do określonego pola. Ten przykład wyszukuje tytuły biznesowe z terminem starszym w nich, ale nie na żadnym z nich.

### <a name="partial-query-string"></a>Częściowy ciąg zapytania

```http
$select=business_title&search=business_title:(senior NOT junior)
```

To samo zapytanie z wieloma polami.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>Pełny adres URL

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  ![Przykładowa odpowiedź Poster](media/search-query-lucene-examples/intrafieldfilter.png)

Można zdefiniować pole operacji wyszukiwania za pomocą składni **NazwaPola: searchExpression** , gdzie wyrażenie wyszukiwania może być pojedynczym słowem lub frazą lub bardziej skomplikowanym wyrażeniem w nawiasach, opcjonalnie z operatorami logicznymi. Oto kilka przykładów:

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

Pamiętaj, aby umieścić wiele ciągów w cudzysłowie, jeśli chcesz, aby oba ciągi były oceniane jako pojedyncze jednostki, tak jak w tym przypadku wyszukiwanie dwóch odrębnych lokalizacji w polu `state`. Upewnij się również, że operator jest wielką literą, gdy zobaczysz pozycję nie i i.

Pole określone w elemencie **FieldName: searchExpression** musi być polem z możliwością wyszukiwania. Aby uzyskać szczegółowe informacje na temat sposobu używania atrybutów indeksu w definicjach pól, zobacz [create index (interfejs API REST platformy Azure wyszukiwanie poznawcze)](https://docs.microsoft.com/rest/api/searchservice/create-index) .

> [!NOTE]
> W powyższym przykładzie nie musimy używać parametru `searchFields`, ponieważ każda część zapytania ma jawnie określoną nazwę pola. Jednak nadal można użyć parametru `searchFields`, jeśli chcesz uruchomić zapytanie, w którym niektóre części są objęte zakresem określonego pola, a reszta może mieć zastosowanie do kilku pól. Na przykład, `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` kwerendy będzie pasować `senior NOT junior` tylko do pola `business_title`, podczas gdy będzie pasować do pola "External" w polu `posting_type`. Nazwa pola podana w polu **NazwaPola: searchExpression** zawsze ma pierwszeństwo przed parametrem `searchFields`, co w tym przykładzie nie musi zawierać `business_title` w parametrze `searchFields`.

## <a name="example-3-fuzzy-search"></a>Przykład 3: Wyszukiwanie rozmyte

Pełna składnia Lucene obsługuje również Wyszukiwanie rozmyte, które dopasowuje się do warunków, które mają podobną konstrukcję. Aby wykonać Wyszukiwanie rozmyte, Dołącz symbol `~` tyldy na końcu pojedynczego słowa z opcjonalnym parametrem, wartością z zakresu od 0 do 2, która określa odległość edycji. Na przykład `blue~` lub `blue~1` zwróci niebieskie, blues i przyklej.

### <a name="partial-query-string"></a>Częściowy ciąg zapytania

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

Wyrażenia nie są obsługiwane bezpośrednio, ale można określić dopasowanie rozmyte w częściach składnika frazy.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>Pełny adres URL

To zapytanie wyszukuje zadania z terminem "Skojarz" (świadomie błędny):

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Odpowiedź na Wyszukiwanie rozmyte](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> Zapytania rozmyte nie są [analizowane](search-lucene-query-architecture.md#stage-2-lexical-analysis). Typy zapytań z niekompletnymi postanowieniami (zapytanie o prefiks, zapytanie symboli wieloznacznych, zapytanie rozmyte) są dodawane bezpośrednio do drzewa zapytań, pomijając etap analizy. Jedyne przekształcenie wykonane na niekompletnych terminach zapytania to lowercasing.
>

## <a name="example-4-proximity-search"></a>Przykład 4: wyszukiwanie w sąsiedztwie
Wyszukiwania w sąsiedztwie są używane do znajdowania terminów blisko siebie w dokumencie. Wstaw symbol tyldy "~" na końcu frazy, a po niej liczbę słów, które tworzą granicę bliskości. Na przykład "Port lotniczy" w hotelu "~ 5 zawiera warunki hotelowe i lotnisko w 5 wyrazach innych elementów w dokumencie.

### <a name="partial-query-string"></a>Częściowy ciąg zapytania

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>Pełny adres URL

W tym zapytaniu w przypadku zadań z terminem "wyższy analityk", w którym jest oddzielona nie więcej niż jeden wyraz:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  ![Zapytanie o bliskość](media/search-query-lucene-examples/proximity-before.png)

Spróbuj ponownie usunąć słowa między terminem "wyższego analityka". Zwróć uwagę, że 8 dokumentów jest zwracanych dla tego zapytania, a nie do 10 dla poprzedniego zapytania.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Przykład 5: zwiększenie warunków
Zwiększenie warunków dotyczy klasyfikacji dokumentu, jeśli zawiera on podwyższony termin względem dokumentów, które nie zawierają warunków. Aby zwiększyć okres, użyj karetki, "^", symbolu z współczynnikem wzrostu (liczba) na końcu wyszukiwanego okresu. 

### <a name="full-urls"></a>Pełne adresy URL

W tym zapytaniu "Before" Wyszukaj zadania z takim *analitykiem komputerowym* i zwróć uwagę na to, że nie ma żadnych wyników zarówno dla *komputera* , jak i *analityka*, ale zadania *komputera* znajdują się w górnej części wyników.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  ![Trwa zwiększanie warunków](media/search-query-lucene-examples/termboostingbefore.png)

W zapytaniu "After" Powtórz wyszukiwanie, a tym samym czasie zwiększy wyniki za pomocą warunkowego *analityka* na *komputerze* , jeśli oba słowa nie istnieją. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
Bardziej czytelna dla człowieka wersja powyższego zapytania jest `search=business_title:computer analyst^2`. W przypadku zapytania prawidłowo `^2` jest zakodowana jako `%5E2`, co jest trudniejsze do wyświetlenia.

  ![Zwiększenie warunków](media/search-query-lucene-examples/termboostingafter.png)

Zwiększenie okresu różni się od profilów oceniania w tym profilu oceniania, a nie na określonych warunkach. Poniższy przykład pomaga zilustrować różnice.

Rozważmy profil oceniania, który zwiększa dopasowań w określonym polu, takich jak **gatunek** w przykładowym musicstoreindex. Zwiększenie okresu może służyć do dalszej promocji niektórych wyszukiwanych terminów wyższych niż inne. Na przykład "skała ^ 2 elektroniczna" spowoduje zwiększenie poziomu dokumentów zawierających terminy wyszukiwania w polu **gatunek** powyżej innych pól, które można wyszukiwać w indeksie. Ponadto dokumenty zawierające termin wyszukiwania "skały" będą wyższe niż w przypadku innych wyszukiwanych terminów "elektroniczny" w wyniku okresu zwiększenia wartości (2).

W przypadku ustawienia poziomu współczynnika wyższa wartość współczynnika zwiększania istotny termin będzie odnosić się do innych wyszukiwanych terminów. Domyślnie współczynnik zwiększania wynosi 1. Chociaż współczynnik zwiększania wartości musi być dodatni, może być mniejszy niż 1 (na przykład 0,2).


## <a name="example-6-regex"></a>Przykład 6: wyrażenie regularne

Wyszukiwanie w wyrażeniu regularnym wyszukuje dopasowanie na podstawie zawartości między ukośnikami "/", zgodnie z opisem w [klasie RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

### <a name="partial-query-string"></a>Częściowy ciąg zapytania

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>Pełny adres URL

W tym zapytaniu Wyszukaj zadania z terminem "starsze lub młodsze: `search=business_title:/(Sen|Jun)ior/`.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  ![Zapytanie wyrażenia regularnego](media/search-query-lucene-examples/regex.png)

> [!Note]
> Zapytania wyrażenia regularnego nie są [analizowane](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). Jedyne przekształcenie wykonane na niekompletnych terminach zapytania to lowercasing.
>

## <a name="example-7-wildcard-search"></a>Przykład 7: wyszukiwanie przy użyciu symboli wieloznacznych
Można użyć ogólnie rozpoznanej składni dla wielu (\*) lub pojedynczego znaku (?) wyszukiwania symboli wieloznacznych. Zwróć uwagę, że Analizator zapytań Lucene obsługuje używanie tych symboli z pojedynczym terminem, a nie frazą.

### <a name="partial-query-string"></a>Częściowy ciąg zapytania

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>Pełny adres URL

W tym zapytaniu Wyszukaj zadania, które zawierają prefiks "program", który zawiera tytuły biznesowe z programowaniem terminów i programistą. Nie można użyć znaku * ani? Symbol jako pierwszy znak wyszukiwania.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  ![Zapytanie symboli wieloznacznych](media/search-query-lucene-examples/wildcard.png)

> [!Note]
> Zapytania z symbolami wieloznacznymi nie są [analizowane](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). Jedyne przekształcenie wykonane na niekompletnych terminach zapytania to lowercasing.
>

## <a name="next-steps"></a>Następne kroki
Spróbuj określić parser zapytań Lucene w kodzie. Poniższe linki wyjaśniają, jak skonfigurować zapytania wyszukiwania dla platformy .NET i interfejsu API REST. Linki używają domyślnej prostej składni, dlatego należy zastosować informacje uzyskane z tego artykułu, aby określić element **querytype**.

* [Tworzenie zapytań względem indeksu przy użyciu zestawu .NET SDK](search-query-dotnet.md)
* [Tworzenie zapytań względem indeksu przy użyciu interfejsu API REST](search-create-index-rest-api.md)

Dodatkowe informacje na temat składni, architekturę zapytań i przykłady można znaleźć w następujących linkach:

+ [Przykłady prostych zapytań składniowych](search-query-simple-examples.md)
+ [Jak działa wyszukiwanie pełnotekstowe na platformie Azure Wyszukiwanie poznawcze](search-lucene-query-architecture.md)
+ [Prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Pełna składnia zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)