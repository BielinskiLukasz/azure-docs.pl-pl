---
title: Filtry języków w usłudze Azure Search | Dokumentacja firmy Microsoft
description: Filtruj kryteria przez tożsamość zabezpieczeń użytkownika, języka, lokalizacji geograficznej lub wartości liczbowych, aby ograniczyć wyniki wyszukiwania dla zapytań w usłudze Azure Search, Usługa wyszukiwania w hostowanej chmurze Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: heidist
ms.openlocfilehash: 2bacffe64fed3e2ee0cc2eb983776b4ab7086e51
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466589"
---
# <a name="how-to-filter-by-language-in-azure-search"></a>Jak filtrować za pomocą języka w usłudze Azure Search 

Decydujące znaczenie w aplikacji wielojęzycznych wyszukiwania jest możliwość wyszukiwanie i pobieranie wyników w języku przez użytkownika. W usłudze Azure Search jednym ze sposobów spełniają wymagania językowe wielojęzycznych aplikacji jest utworzyć serię pola przeznaczone do przechowywania ciągów w określonym języku, a następnie ograniczyć wyszukiwanie pełnotekstowe do tylko tych pól w czasie wykonywania zapytania.

Parametry zapytania dla żądania są używane do określania zakresu operacji wyszukiwania i następnie przyciąć wyniki wszystkich pól, które nie udostępniają zgodny z wyników wyszukiwania, które chcesz dostarczać zawartość.

| Parametry | Przeznaczenie |
|-----------|--------------|
| **searchFields** | Limity pełne wyszukiwanie pełnotekstowe listę nazwanych pól. |
| **$select** | Przycina odpowiedzi, aby uwzględnić tylko pola, które określisz. Domyślnie zwracane są wszystkie pola z możliwością pobierania. **$Select** parametr pozwala wybrać kolumny do zwrócenia. |

Powodzenie korzystania z tej techniki zawiasy od integralności zawartość pola. Usługa Azure Search nie tłumaczenia ciągów lub wykonywać wykrywanie języka. Jest należy się upewnić, że pola zawierają ciągi, których oczekujesz.

## <a name="define-fields-for-content-in-different-languages"></a>Definiowanie pól dla zawartości w różnych językach

W usłudze Azure Search zapytania docelowe jednego indeksu. Deweloperzy, którzy mają zwykle zapewnienie ciągów specyficznych dla języka w jednym wyszukiwania zdefiniować dedykowanych pól do przechowywania wartości: ciągi jedno pole dla języka angielskiego, jeden dla języka francuskiego i tak dalej. 

W naszym próbnym, w tym [przykładowe nieruchomości](search-get-started-portal.md) pokazano poniżej, możesz już znać definicje pól, podobnie jak poniższy zrzut ekranu. Zwróć uwagę, jak w tym przykładzie języka przypisania analizatora dla pól tego indeksu. Pola, które zawierają ciągi działać lepiej wyszukiwanie pełnotekstowe w połączeniu z analizatora zaprojektowany do obsługi językowej reguły języka docelowego.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> Przykłady kodu, przedstawiający definicje pól z analizatory języków, zobacz [definiowania indeksu (.NET)](https://docs.microsoft.com/azure/search/search-create-index-dotnet#define-your-azure-search-index) i [definiowania indeksu (REST)](https://docs.microsoft.com/azure/search/search-create-index-rest-api#define-your-azure-search-index-using-well-formed-json).

## <a name="build-and-load-an-index"></a>Tworzenie i ładowanie indeksu

Etap pośredni (i prawdopodobnie oczywisty) jest, że trzeba [kompilacji i wypełnić indeks](https://docs.microsoft.com/azure/search/search-create-index-dotnet#create-the-index) przed formułowanie zapytania. Firma Microsoft wspominać o tym kroku, w tym miejscu aby informacje były kompletne. Jednym ze sposobów, aby ustalić, czy indeks jest dostępna jest sprawdzanie listy indeksów w [portal](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>Ograniczenie zapytania i przycinany wyników

Parametry zapytania są używane do Ogranicz wyszukiwanie do określonych pól, a następnie przyciąć wyniki wszystkich pól, które nie są przydatne w przypadku Twojego scenariusza. Do pola zawierające francuskie ciągi, biorąc pod uwagę cel ograniczający wyszukiwania, należy użyć **searchFields** pod kątem zapytania na pola zawierające ciągi w tym języku. 

Domyślnie wyszukiwanie zwraca wszystkie pola, które są oznaczone jako możliwe do pobierania. W efekcie można wykluczyć pola, które nie są zgodne ze środowiska wyszukiwania specyficznych dla języka, który chcesz udostępnić. W szczególności jeśli wyszukiwania do pola z ciągami francuskim jest ograniczona, prawdopodobnie chcesz wykluczyć pól zawierających ciągi w języku polskim z wyników. Za pomocą **$select** zapytania parametr zapewnia kontrolę nad pola, które są zwracane do aplikacji wywołującej.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Mimo że jest no $filter argument zapytania, na ten przypadek użycia silnie zostało powiązane z pojęcia filtru, dzięki czemu możemy przedstawić jako filtrowania scenariusza.

## <a name="see-also"></a>Zobacz także

+ [Filtry w usłudze Azure Search](search-filters.md)
+ [Analizatory języków](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Search](search-lucene-query-architecture.md)
+ [Wyszukiwanie w dokumentach interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents)

