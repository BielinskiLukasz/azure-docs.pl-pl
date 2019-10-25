---
title: Filtry języka dla zawartości wielojęzycznej w indeksie wyszukiwania
titleSuffix: Azure Cognitive Search
description: Kryteria filtrowania obsługujące wyszukiwanie w wielu językach, określanie zakresu wykonywania zapytań do pól specyficznych dla języka.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2762ce42f0d3f5829682e0910c452746a65ef2f3
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792872"
---
# <a name="how-to-filter-by-language-in-azure-cognitive-search"></a>Jak filtrować według języka na platformie Azure Wyszukiwanie poznawcze 

Kluczowym wymaganiem w aplikacji wyszukiwania wielojęzycznego jest możliwość wyszukiwania i pobierania wyników w języku użytkownika. Na platformie Azure Wyszukiwanie poznawcze jednym ze sposobów spełnienia wymagań językowych aplikacji wielojęzycznej jest utworzenie serii pól przeznaczonych do przechowywania ciągów w określonym języku, a następnie ograniczenie wyszukiwania pełnotekstowego do tylko tych pól w czasie zapytania.

Parametry zapytania w żądaniu są używane do zakresu operacji wyszukiwania, a następnie przycinają wyniki pól, które nie zapewniają zawartości zgodnej z funkcją wyszukiwania, którą chcesz dostarczyć.

| Parametry | Przeznaczenie |
|-----------|--------------|
| **searchFields** | Ogranicza wyszukiwanie pełnotekstowe do listy nazwanych pól. |
| **$select** | Przycina odpowiedź w celu uwzględnienia tylko pól, które określisz. Domyślnie zwracane są wszystkie pola do pobierania. Parametr **$SELECT** umożliwia wybranie, które z nich mają być zwracane. |

Sukces tej techniki zawiasuje integralność zawartości pola. Usługa Azure Wyszukiwanie poznawcze nie tłumaczy ciągów ani nie przeprowadza wykrywania języka. Należy upewnić się, że pola zawierają ciągi, których oczekujesz.

## <a name="define-fields-for-content-in-different-languages"></a>Definiowanie pól dla zawartości w różnych językach

W Wyszukiwanie poznawcze na platformie Azure zapytania mają jeden indeks docelowy. Deweloperzy, którzy chcą podawać ciągi charakterystyczne dla języka w jednym środowisku wyszukiwania, zwykle definiują dedykowane pola do przechowywania wartości: jedno pole dla ciągów w języku angielskim, jeden dla języka francuskiego itd. 

W naszych przykładach, w tym w pokazanym poniżej [przykładzie](search-get-started-portal.md) , można zobaczyć definicje pól podobne do poniższego zrzutu ekranu. Zwróć uwagę, jak w tym przykładzie przedstawiono przypisania analizatora języka dla pól w tym indeksie. Pola zawierające ciągi działają lepiej podczas wyszukiwania pełnotekstowego, gdy jest sparowany z analizatorem zaprojektowanym do obsługi reguł lingwistycznych języka docelowego.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> Przykłady kodu przedstawiające definicje pól z analizatorami języków można znaleźć w temacie [Definiowanie indeksu (.NET)](https://docs.microsoft.com/azure/search/search-create-index-dotnet) i [Definiowanie indeksu (REST)](search-create-index-rest-api.md).

## <a name="build-and-load-an-index"></a>Kompilowanie i ładowanie indeksu

Krok pośredni (i prawdopodobnie oczywisty) jest konieczny do [kompilowania i wypełniania indeksu](https://docs.microsoft.com/azure/search/search-create-index-dotnet) przed wyrażeniem zapytania. Ten krok jest tutaj omawiany w celu zapewnienia kompletności. Jednym ze sposobów ustalenia, czy indeks jest dostępny, jest sprawdzenie listy indeksów w [portalu](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>Ogranicz wyniki zapytania i przycinania

Parametry zapytania służą do ograniczania wyszukiwania do określonych pól, a następnie przycinania wyników wszelkich pól, które nie są przydatne dla Twojego scenariusza. Uwzględniając cel ograniczenia wyszukiwania do pól zawierających ciągi francuski, użyj **searchFields** , aby docelowa kwerenda w polach zawierających ciągi w tym języku. 

Domyślnie wyszukiwanie zwraca wszystkie pola, które są oznaczone jako możliwy do pobierania. W związku z tym możesz chcieć wykluczyć pola, które nie są zgodne ze specyficznymi dla języka interfejsem wyszukiwania, które chcesz podać. W szczególności w przypadku ograniczenia wyszukiwania do pola z ciągami francuskim prawdopodobnie warto wykluczyć z wyników pola z ciągami w języku angielskim. Za pomocą **$SELECT** parametru zapytania daje kontrolę nad tym, które pola są zwracane do aplikacji wywołującej.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Mimo że nie ma $filter argumentu w zapytaniu, ten przypadek użycia jest silnie powiązany z koncepcjami filtru, więc przedstawimy go jako scenariusz filtrowania.

## <a name="see-also"></a>Zobacz także

+ [Filtry na platformie Azure Wyszukiwanie poznawcze](search-filters.md)
+ [Analizatory języków](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Jak działa wyszukiwanie pełnotekstowe na platformie Azure Wyszukiwanie poznawcze](search-lucene-query-architecture.md)
+ [Interfejs API REST dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents)

