---
title: Jak modelować złożone typy danych
titleSuffix: Azure Cognitive Search
description: Zagnieżdżone lub hierarchiczne struktury danych można modelować w indeksie Wyszukiwanie poznawcze platformy Azure przy użyciu typów danych ComplexType i Collections.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2edd62825de08becf22f2f953a63a7f89f55e0a6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283058"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>Jak modelować złożone typy danych w usłudze Azure Wyszukiwanie poznawcze

Zewnętrzne zestawy danych używane do wypełniania indeksu usługi Azure Wyszukiwanie poznawcze mogą pochodzić z wielu kształtów. Czasami zawierają hierarchiczne lub zagnieżdżone podstruktury. Przykłady mogą obejmować wiele adresów pojedynczego klienta, wiele kolorów i rozmiarów dla pojedynczej jednostki SKU, wielu autorów jednej książki itd. W przypadku warunków modelowania można zobaczyć, że te struktury są określane jako *złożone* *, złożone, złożone*lub *zagregowane* typy danych. Termin korzystania z platformy Azure Wyszukiwanie poznawcze dla tego pojęcia to **typ złożony**. Na platformie Azure Wyszukiwanie poznawcze typy złożone są modelowane przy użyciu **pól złożonych**. Pole złożone to pole, które zawiera elementy podrzędne (podpola), które mogą być dowolnego typu danych, w tym inne typy złożone. Działa to podobnie jak w przypadku typów danych ze strukturą w języku programowania.

Pola złożone reprezentują pojedynczy obiekt w dokumencie lub tablicę obiektów, w zależności od typu danych. Pola typu `Edm.ComplexType` reprezentują pojedyncze obiekty, natomiast pola typu `Collection(Edm.ComplexType)` reprezentują tablice obiektów.

Platforma Azure Wyszukiwanie poznawcze natywnie obsługuje złożone typy i kolekcje. Te typy umożliwiają modelowanie prawie każdej struktury JSON w indeksie Wyszukiwanie poznawcze platformy Azure. W poprzednich wersjach interfejsów API platformy Azure Wyszukiwanie poznawcze można zaimportować tylko spłaszczone zestawy wierszy. W najnowszej wersji indeks może teraz ściśle odpowiadać danych źródłowych. Innymi słowy, jeśli dane źródłowe mają typy złożone, indeks może również zawierać typy złożone.

Aby rozpocząć, zalecamy użycie [zestawu danych hoteli](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md), który można załadować w kreatorze **importowania danych** w Azure Portal. Kreator wykrywa złożone typy w źródle i sugeruje schemat indeksu na podstawie wykrytych struktur.

> [!Note]
> Obsługa typów złożonych jest ogólnie dostępna w `api-version=2019-05-06`. 
>
> Jeśli Twoje rozwiązanie wyszukiwania jest oparte na starszych obejść spłaszczonych zestawów danych w kolekcji, należy zmienić indeks tak, aby zawierał złożone typy jako obsługiwane w najnowszej wersji interfejsu API. Aby uzyskać więcej informacji na temat uaktualniania wersji interfejsu API, zobacz [uaktualnianie do najnowszej wersji interfejsu API REST](search-api-migration.md) lub [uaktualnianie do najnowszej wersji zestawu SDK platformy .NET](search-dotnet-sdk-migration-version-9.md).

## <a name="example-of-a-complex-structure"></a>Przykład struktury złożonej

Poniższy dokument JSON składa się z pól prostych i złożonych. Pola złożone, takie jak `Address` i `Rooms`, mają podpola. `Address` ma jeden zbiór wartości dla tych podpól, ponieważ jest to pojedynczy obiekt w dokumencie. Z kolei `Rooms` ma wiele zestawów wartości dla pól podrzędnych, jeden dla każdego obiektu w kolekcji.

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "Type": "Budget Room",
      "BaseRate": 96.99
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99
    },
  ]
}
```

## <a name="creating-complex-fields"></a>Tworzenie pól złożonych

Podobnie jak w przypadku dowolnej definicji indeksu, można użyć portalu, [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/create-index)lub [zestawu .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) , aby utworzyć schemat zawierający typy złożone. 

Poniższy przykład przedstawia schemat indeksu JSON z prostymi polami, kolekcjami i typami złożonymi. Zwróć uwagę, że w ramach typu złożonego każde pole podrzędne ma typ i może mieć atrybuty, tak jak pola najwyższego poziomu. Schemat odnosi się do przykładowych danych powyżej. `Address` to złożone pole, które nie jest kolekcją (Hotel ma jeden adres). `Rooms` to złożone pole kolekcji (Hotel ma wiele pokojów).

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
      ]
    }
  ]
}
```

## <a name="updating-complex-fields"></a>Aktualizowanie pól złożonych

Wszystkie reguły ponownego [indeksowania](search-howto-reindex.md) stosowane do pól ogólnie obowiązują nadal dotyczą pól złożonych. W tym miejscu należy ponownie określić kilka głównych reguł, dodanie pola nie wymaga ponownego zakompilowania indeksu, ale większość modyfikacji ma.

### <a name="structural-updates-to-the-definition"></a>Aktualizacje strukturalne do definicji

Nowe pola podrzędne można dodawać do pola złożonego w dowolnym momencie bez konieczności ponownego kompilowania indeksu. Na przykład dodanie elementu "kod pocztowy" do `Address` lub "udogodnień" do `Rooms` jest dozwolone, podobnie jak dodanie pola najwyższego poziomu do indeksu. Istniejące dokumenty mają wartość null dla nowych pól, dopóki te pola nie zostaną jawnie wypełnione przez zaktualizowanie danych.

Zwróć uwagę, że w ramach typu złożonego każde pole podrzędne ma typ i może mieć atrybuty, podobnie jak pola najwyższego poziomu.

### <a name="data-updates"></a>Aktualizacje danych

Aktualizowanie istniejących dokumentów w indeksie z akcją `upload` działa tak samo jak w przypadku złożonych i prostych pól — wszystkie pola są zastępowane. Jednak `merge` (lub `mergeOrUpload` w przypadku zastosowania do istniejącego dokumentu) nie działa tak samo dla wszystkich pól. W `merge` nie obsługuje scalania elementów w obrębie kolekcji. To ograniczenie istnieje dla kolekcji typów pierwotnych i złożonych kolekcji. Aby zaktualizować kolekcję, należy pobrać pełną wartość kolekcji, wprowadzić zmiany, a następnie dołączyć nową kolekcję do żądania interfejsu API indeksu.

## <a name="searching-complex-fields"></a>Wyszukiwanie pól złożonych

Wyrażenia wyszukiwania w dowolnym formacie działają zgodnie z oczekiwaniami w typach złożonych. Jeśli dowolne pole z możliwością przeszukiwania lub dowolne miejsce w dokumencie jest zgodne, sam dokument jest zgodny.

Zapytania uzyskują więcej złożonych, gdy istnieje wiele warunków i operatorów, a niektóre terminy mają określone nazwy pól, jak jest to możliwe przy użyciu [składni Lucene](query-lucene-syntax.md). Na przykład, zapytanie próbuje dopasować dwa warunki, "Portland" i "lub", w odniesieniu do dwóch podpól pola Adres:

    search=Address/City:Portland AND Address/State:OR

Zapytania takie jak takie nie są *skorelowane* dla wyszukiwania pełnotekstowego, w przeciwieństwie do filtrów. W filtrach zapytania dotyczące pól podrzędnych kolekcji złożonej są skorelowane przy użyciu zmiennych zakresu w [`any` lub `all`](search-query-odata-collection-operators.md). Powyższe zapytanie Lucene zwraca dokumenty zawierające zarówno "Portland, Maine" i "Portland, Oregon" oraz inne miasta w Oregon. Dzieje się tak, ponieważ każda klauzula ma zastosowanie do wszystkich wartości pola w całym dokumencie, dlatego nie istnieje koncepcja "bieżącego dokumentu podrzędnego". Aby uzyskać więcej informacji na ten temat, zobacz [Omówienie filtrów kolekcji OData w usłudze Azure wyszukiwanie poznawcze](search-query-understand-collection-filters.md).

## <a name="selecting-complex-fields"></a>Wybieranie pól złożonych

Parametr `$select` służy do wybierania pól, które są zwracane w wynikach wyszukiwania. Aby użyć tego parametru do zaznaczania określonych pól podrzędnych pola złożonego, należy uwzględnić pole nadrzędne i podpole oddzielone ukośnikiem (`/`).

    $select=HotelName, Address/City, Rooms/BaseRate

Jeśli chcesz, aby były one widoczne w wynikach wyszukiwania, pola muszą być oznaczone jako możliwe do pobierania w indeksie. W instrukcji `$select` można używać tylko pól oznaczonych jako możliwe do pobierania.

## <a name="filter-facet-and-sort-complex-fields"></a>Filtrowanie, zestaw reguł i sortowanie pól złożonych

Tej samej [składni ścieżki OData](query-odata-filter-orderby-syntax.md) używanej do filtrowania i wyszukiwania pól można także użyć do tworzenia aspektów, sortowania i wybierania pól w żądaniu wyszukiwania. W przypadku typów złożonych reguły mają zastosowanie, które określają, które pola podrzędne mogą być oznaczone jako do sortowania lub do tworzenia. Aby uzyskać więcej informacji na temat tych reguł, zobacz temat [Tworzenie indeksu interfejsu API](/rest/api/searchservice/create-index).

### <a name="faceting-sub-fields"></a>Podpola aspektów

Każde podpole może być oznaczone jako element, chyba że jest typu `Edm.GeographyPoint` lub `Collection(Edm.GeographyPoint)`.

Liczby dokumentów zwracane w wynikach aspektu są obliczane dla dokumentu nadrzędnego (Hotel), a nie do dokumentów podrzędnych w złożonej kolekcji (pokoje). Załóżmy na przykład, że Hotel ma 20 pokojów typu "Suite". Podaną `facet=Rooms/Type`tego parametru aspektu liczba aspektów będzie jedną dla hotelu, a nie 20 dla pokojów.

### <a name="sorting-complex-fields"></a>Sortowanie pól złożonych

Operacje sortowania dotyczą dokumentów (Hotele), a nie dokumentów podrzędnych (pokojów). Gdy istnieje kolekcja typu złożonego, taka jak pokoje, ważne jest, aby pamiętać, że nie można sortować w pokojach. W rzeczywistości nie można sortować według żadnej kolekcji.

Operacje sortowania działają, gdy pola mają jedną wartość na dokument, niezależnie od tego, czy pole jest polem prostym, czy też podpolem w typie złożonym. Na przykład `Address/City` można sortować, ponieważ istnieje tylko jeden adres na Hotel, więc `$orderby=Address/City` sortuje Hotele według miejscowości.

### <a name="filtering-on-complex-fields"></a>Filtrowanie w polach złożonych

Można odwoływać się do podpól złożonego pola w wyrażeniu filtru. Po prostu Użyj tej samej [składni ścieżki OData](query-odata-filter-orderby-syntax.md) , która jest używana do tworzenia aspektów, sortowania i wybierania pól. Na przykład następujący filtr zwróci wszystkie hotele w Kanadzie:

    $filter=Address/Country eq 'Canada'

Aby odfiltrować w polu kolekcji złożonej, można użyć **wyrażenia lambda** z [operatorami`any` i `all`](search-query-odata-collection-operators.md). W takim przypadku **zmienna zakresu** wyrażenia lambda jest obiektem z podpolami. Można odwołać się do tych podpól ze standardową składnią ścieżki OData. Na przykład następujący filtr zwróci wszystkie hotele z co najmniej jedną usługą Deluxe i wszystkie pokoje nieprzeznaczone do palenia:

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

Podobnie jak w przypadku pól prostych najwyższego poziomu, proste pola podrzędne pól złożonych mogą być uwzględniane tylko w filtrach, jeśli mają atrybut z możliwością **filtrowania** ustawioną `true` w definicji indeksu. Aby uzyskać więcej informacji, zobacz [Dokumentacja interfejsu API tworzenia indeksu](/rest/api/searchservice/create-index).

## <a name="next-steps"></a>Następne kroki

Wypróbuj [zestaw danych hoteli](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) w kreatorze **importu danych** . Do uzyskania dostępu do danych wymagane są informacje o połączeniu Cosmos DB podane w pliku Readme.

Wraz z tymi informacjami pierwszym krokiem w Kreatorze jest utworzenie nowego Azure Cosmos DBgo źródła danych. Oprócz tego w kreatorze, gdy uzyskasz dostęp do docelowej strony indeksu, zobaczysz indeks z typami złożonymi. Utwórz i Załaduj ten indeks, a następnie wykonaj zapytania, aby zrozumieć nową strukturę.

> [!div class="nextstepaction"]
> [Szybki Start: Kreator portalu do importowania, indeksowania i zapytań](search-get-started-portal.md)
