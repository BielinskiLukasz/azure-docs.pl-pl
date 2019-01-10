---
title: Eksplorator wyszukiwania w witrynie Azure portal do wykonywania zapytań indeksy — usługa Azure Search
description: Użyj narzędzi witryny Azure portal takich jak Eksplorator wyszukiwania zapytania indeksów w usłudze Azure Search. Wprowadź terminy wyszukiwania lub w pełni kwalifikowana ciągów znaków za pomocą zaawansowanej składni.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2aa372d1f917608de753007cc75ab0d608cafbba
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188729"
---
# <a name="how-to-use-search-explorer-to-query-indexes-in-azure-search"></a>Jak używać Eksploratora wyszukiwania zapytania indeksów w usłudze Azure Search 

W tym artykule dowiesz się, jak wykonywać zapytania dla istniejącej usługi Azure Search indeksu przy użyciu **Eksploratora wyszukiwania** w witrynie Azure portal. Eksploratora wyszukiwania można użyć do przesyłania prostych lub pełnych ciągów zapytań Lucene do dowolnego istniejącego indeksu w usłudze.

## <a name="start-search-explorer"></a>Uruchom Eksploratora wyszukiwania

1. W [witryny Azure portal](https://portal.azure.com), otwórz stronę usługi wyszukiwanie z pulpitu nawigacyjnego lub [Znajdź swoją usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na liście usług.

2. Na stronie Przegląd usługi kliknij **Eksploratora wyszukiwania**.

   ![Wyszukaj polecenie Eksploratora w portalu](./media/search-explorer/search-explorer-cmd2.png "Wyszukaj polecenie Eksploratora w portalu")

3. Wybierz indeks, aby wykonać zapytanie.

   ![Wybierz indeks, aby wykonać zapytanie](./media/search-explorer/search-explorer-changeindex-se2.png "wybierz indeks")

4. Opcjonalnie Ustaw wersję interfejsu API. Domyślnie aktualna jest ogólnie dostępna wersja interfejsu API jest zaznaczone, ale można wybrać wersję zapoznawczą lub starszy interfejs API, jeśli składnię, której chcesz użyć jest specyficzny dla wersji.

5. Gdy indeks i wersja interfejsu API jest zaznaczone, wprowadź terminy wyszukiwania lub wyrażenia zapytania w pełni kwalifikowaną na pasku wyszukiwania i kliknij przycisk **wyszukiwania** do wykonania.

   ![Wprowadź terminy wyszukiwania, a następnie kliknij przycisk Wyszukaj](./media/search-explorer/search-explorer-query-string-example.png "wyszukiwania wprowadź warunki i kliknij przycisk Wyszukaj")

Porady dotyczące wyszukiwania w **Eksploratora wyszukiwania**:

+ Wyniki są zwracane jako pełne dokumentów JSON, dzięki czemu można wyświetlić konstrukcji dokumentów i zawartości, które znajdują się w całości. Można użyć wyrażeń zapytania, pokazano w przykładach, limitu pola, które są zwracane.

+ Dokumenty składają się ze wszystkich pól oznaczonych jako **możliwość pobierania** w indeksie. Aby wyświetlić atrybuty indeksu w portalu, kliknij *realestate-us-sample* w **indeksy** listy na stronie Przegląd wyszukiwania.

+ Dowolnych zapytania, podobnie jak co może wprowadzić w przeglądarce internetowej komercyjnych są przydatne w przypadku testowania środowisko użytkownika końcowego. Na przykład zakładając, że przykładowy indeks realestate wbudowane, możesz wprowadzić nazwę "Waszyngton lake apartamentach Seattle" i można znaleźć terminy w wynikach wyszukiwania Ctrl-F. 

+ Wyrażenia zapytania i filtrowanie musi być strategią składni, obsługiwane przez usługę Azure Search. Wartość domyślna to [prostą składnię](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), ale Opcjonalnie można użyć [pełne Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) bardziej zaawansowanych zapytań. [Wyrażenia filtru](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) są składnia OData.

## <a name="basic-search-strings"></a>Ciągi wyszukiwania podstawowego

W poniższych przykładach założono przykładowy indeks realestate wbudowanych. Aby uzyskać więcej informacji na temat tworzenia tego indeksu, zobacz [Szybki Start: Import, indeksów i zapytań w witrynie Azure portal](search-get-started-portal.md).

### <a name="example-1---empty-search"></a>Przykład 1 — puste wyszukiwanie

Dla Pierwsze spojrzenie na zawartości, należy wykonać to puste wyszukiwanie, klikając **wyszukiwania** nie warunki podane. Puste wyszukiwanie jest przydatne jako pierwszego zapytania, ponieważ zwraca całe dokumenty, aby przejrzeć kompozycji dokumentu. Na puste wyszukiwanie jest nie wyszukiwarkach i dokumenty są zwracane w porządku dowolnego (`"@search.score": 1` dla wszystkich dokumentów). Domyślnie 50 dokumenty są zwracane w żądaniu wyszukiwania.

Jest równoważny składni wyszukiwania puste `*` lub `search=*`.

   ```Input
   search=*
   ```

   **Results**
   
   ![Przykład pustego zapytania](./media/search-explorer/search-explorer-example-empty.png "Unqualified lub przykład pustego zapytania")

### <a name="example-2---free-text-search"></a>Przykład 2 — wyszukiwanie dowolnego tekstu

Dowolnych zapytania, z lub bez operatorów, są przydatne do symulowania zapytań zdefiniowanych przez użytkownika, wysyłane z niestandardowych aplikacji do usługi Azure Search. Należy zauważyć, że jeśli podasz terminów zapytania lub wyrażeń, wyszukiwarkach właśnie. Poniższy przykład ilustruje wyszukiwania dowolnego tekstu.

   ```Input
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Results**

   Ctrl-F służy do wyszukiwania w ramach wyników konkretnych terminów zainteresowania.

   ![Przykład zapytania niezależnych](./media/search-explorer/search-explorer-example-freetext.png "przykład zapytania dowolny tekst")

### <a name="example-3---count-of-matching-documents"></a>Przykład 3 - liczby pasujących dokumentów 

Dodaj **$count** można pobrać liczbę dopasowań znalezionych w indeksie. Na puste wyszukiwanie liczba jest całkowita liczba dokumentów w indeksie. Wyszukiwania kwalifikowany jest liczba dokumentów dopasowywania danych wejściowych zapytania.

   ```Input1
   $count=true
   ```
   **Results**

   ![Liczba dokumentów przykład](./media/search-explorer/search-explorer-example-count.png "liczby pasujących dokumentów w indeksie")

### <a name="example-4---restrict-fields-in-search-results"></a>Przykład 4 - ograniczyć pól w wynikach wyszukiwania

Dodaj **$select** Aby ograniczyć wyniki do wyraźnie nazwane pola czytelność danych wyjściowych **Eksploratora wyszukiwania**. Aby zachować ciągu wyszukiwania i **$count = true**, prefiks argumentów **&**. 

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Results**

   ![Przykład pola limit](./media/search-explorer/search-explorer-example-selectfield.png "ograniczyć pól w wynikach wyszukiwania")

### <a name="example-5---return-next-batch-of-results"></a>Przykład 5 - return następną partię wyników

Usługa Azure Search zwraca górną 50 dopasowaniami oparciu o rangę wyszukiwania. Aby pobrać następny zestaw pasujących dokumentów, należy dołączyć **$top = 100 & $skip = 50** do zwiększenia wyników do 100 dokumentach (wartością domyślną jest 50, maksymalnie 1000), pomijanie 50 pierwszych dokumentów. Odwołania, musisz podać kryteria wyszukiwania, takie jak wyszukiwanego terminu lub wyrażenie, aby pobrać uporządkowanych wyników. Zwróć uwagę, czy w wyszukiwaniu zmniejszyć bardziej dotrzeć do wyników wyszukiwania.

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100,&$skip=50
   ```

   **Results**

   ![Grupowały wyniki wyszukiwania](./media/search-explorer/search-explorer-example-topskip.png "zwracany następną partię wyników wyszukiwania")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Filtruj wyrażenia (równe, większe niż, mniejsze niż)

Użyj **$filter** parametru, jeśli chcesz określić dokładne kryteria zamiast wyszukiwania dowolnego tekstu. W tym przykładzie wyszukuje i wyższej niż 3: `search=seattle condo&$filter=beds gt 3&$count=true`

   ![Wyrażenie filtru](./media/search-explorer/search-explorer-example-filter.png "filtrować według kryteriów")

## <a name="order-by-expressions"></a>Wyrażenia order by

Dodaj **$orderby** Aby posortować wyników według innego pola, oprócz wyniku wyszukiwania. To wyrażenie przykładowe, używanej do testowania tej `search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc`

   ![Wyrażenie OrderBy](./media/search-explorer/search-explorer-example-ordery.png "zmienić kolejność sortowania")

Zarówno **$filter** i **$orderby** wyrażenia są konstrukcje OData. Aby uzyskać więcej informacji, zobacz [Filter OData syntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) (Składnia filtrowania OData).

## <a name="next-steps"></a>Kolejne kroki

W następujących zasobach udostępniono dodatkowe informacje o składni oraz przykłady.

 + [Prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Składnia zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Przykładowe zapytania Lucene](search-query-lucene-examples.md) 
 + [Składnia wyrażeń filtru OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 