---
title: Importowanie i pojmowanie danych w indeksach wyszukiwania
titleSuffix: Azure Cognitive Search
description: Wypełnianie i przekazywanie danych do indeksu w usłudze Azure Cognitive Search z zewnętrznych źródeł danych.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc3f38e9bb96ce76263a3124f8bfdc49dc638bfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282759"
---
# <a name="data-import-overview---azure-cognitive-search"></a>Omówienie importu danych — usługa Azure Cognitive Search

W usłudze Azure Cognitive Search kwerendy są wykonywane za pomocą zawartości załadowanej i zapisanej w [indeksie wyszukiwania.](search-what-is-an-index.md) W tym artykule przeanalizowano dwa podstawowe podejścia do wypełniania indeksu: *wypychanie* danych do indeksu programowo lub punkt [indeksatora usługi Azure Cognitive Search](search-indexer-overview.md) w obsługiwanym źródle danych, aby *wyciągnąć* dane.

Przy każdym podejściu celem jest *załadowanie danych* z zewnętrznego źródła danych do indeksu usługi Azure Cognitive Search. Usługa Azure Cognitive Search umożliwia utworzenie pustego indeksu, ale dopóki nie wypchniesz lub nie wyciągniesz do niego danych, nie można go zbadać.

## <a name="pushing-data-to-an-index"></a>Wypychanie danych do indeksu
Model wypychania, używany do programowego wysyłania danych do usługi Azure Cognitive Search, jest najbardziej elastycznym podejściem. Po pierwsze nie ma żadnych ograniczeń dotyczących typu źródła danych. Każdy zestaw danych składający się z dokumentów JSON może być wypychany do indeksu usługi Azure Cognitive Search, przy założeniu, że każdy dokument w zestawie danych ma pola mapujące do pól zdefiniowanych w schemacie indeksu. Po drugie nie ma żadnych ograniczeń dotyczących częstotliwości wykonywania. Możesz wypychać zmiany do indeksu tak często, jak tylko chcesz. W przypadku aplikacji wymagających bardzo niskich opóźnień (na przykład w razie konieczności synchronizowania operacji wyszukiwania z dynamicznymi bazami danych zapasów) model wypychania jest jedynym rozwiązaniem.

To podejście jest bardziej elastyczne niż model polegający na ściąganiu, ponieważ możesz przekazywać dokumenty pojedynczo lub w partiach (maksymalnie 1000 dokumentów na partię lub 16 MB zależnie od tego, który limit zostanie osiągnięty jako pierwszy). Model wypychania umożliwia również przekazywanie dokumentów do usługi Azure Cognitive Search niezależnie od tego, gdzie znajdują się dane.

### <a name="how-to-push-data-to-an-azure-cognitive-search-index"></a>Jak wypychać dane do indeksu usługi Azure Cognitive Search

Możesz załadować jeden lub wiele dokumentów do indeksu przy użyciu następujących interfejsów API:

+ [Dodawanie, aktualizowanie lub usuwanie dokumentów (interfejs API REST)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Klasa indexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) lub [klasa indexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

Obecnie nie istnieje wsparcie narzędziowe wypychania danych za pośrednictwem portalu.

Aby zapoznać się z wprowadzeniem do każdej metodologii, zobacz [Szybki start: Tworzenie indeksu usługi Azure Cognitive Search przy użyciu programu PowerShell](search-create-index-rest-api.md) lub [Szybkiego startu w języku C#: Tworzenie indeksu usługi Azure Cognitive Search przy użyciu pliku .NET SDK.](search-get-started-dotnet.md)

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Akcje indeksowania: przesyłanie, scalanie, scalanieLubUpload, usuwanie

Typ akcji indeksowania można kontrolować na podstawie dla dokumentu, określając, czy dokument powinien zostać przekazany w całości, scalony z istniejącą zawartością dokumentu, czy usunięty.

W interfejsie API REST należy wystawiać żądania HTTP POST z obiektami żądań JSON do adresu URL punktu końcowego indeksu usługi Azure Cognitive Search. Każdy obiekt JSON w tablicy "value" zawiera klucz dokumentu i określa, czy akcja indeksowania dodaje, aktualizuje lub usuwa zawartość dokumentu. Przykładowy kod można znaleźć [w folderze Load documents](search-get-started-dotnet.md#load-documents).

W pliku .NET SDK należy spakować dane do obiektu. `IndexBatch` Hermetyzuje `IndexBatch` kolekcję `IndexAction` obiektów, z których każdy zawiera dokument i właściwość, która informuje usługi Azure Cognitive Search, jakie działania wykonać w tym dokumencie. Przykładowy kod można znaleźć w przewodniku [Szybki start języka C#.](search-get-started-dotnet.md)


| @search.action | Opis | Wymagane pola dla każdego dokumentu | Uwagi |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |Akcja `upload` jest podobna do akcji „upsert”, co oznacza, że dokument zostanie wstawiony, jeśli jest nowy, albo zaktualizowany/zastąpiony, jeśli już istnieje. |pole klucza oraz inne pola, które chcesz zdefiniować |Podczas aktualizowania/zastępowania istniejącego dokumentu każde pole, które nie jest określone w żądaniu, zostanie ustawione na wartość `null`. Dzieje się tak nawet wtedy, gdy pole było wcześniej ustawione na wartość inną niż null. |
| `merge` |Aktualizuje istniejący dokument o określone pola. Jeśli dokument nie istnieje w indeksie, scalanie zakończy się niepowodzeniem. |pole klucza oraz inne pola, które chcesz zdefiniować |Wszystkie pola, które określisz w żądaniu scalania, zastąpią istniejące pola w dokumencie. W zestawie SDK .NET obejmuje `DataType.Collection(DataType.String)`to pola typu . W interfejsie API REST obejmuje `Collection(Edm.String)`to pola typu . Jeśli na przykład dokument zawiera pole `tags` o wartości `["budget"]` i wykonywane jest scalanie z wartością `["economy", "pool"]` dla pola `tags`, końcowa wartość pola `tags` będzie równa `["economy", "pool"]`. Nie będzie to `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Ta akcja działa jak akcja `merge`, jeśli dokument o danym kluczu już istnieje w indeksie. Jeśli dokument nie istnieje, działa jak akcja `upload` dla nowego dokumentu. |pole klucza oraz inne pola, które chcesz zdefiniować |- |
| `delete` |Usuwa określony dokument z indeksu. |tylko pole klucza |Wszystkie pola, które określisz oprócz pola klucza, zostaną zignorowane. Jeśli chcesz usunąć pojedyncze pole z dokumentu, zamiast tej akcji użyj akcji `merge` i po prostu jawnie ustaw dla pola wartość null. |

## <a name="decide-which-indexing-action-to-use"></a>Wybieranie akcji indeksowania do użycia
Aby zaimportować dane przy użyciu pliku .NET SDK (przekazywanie, scalanie, usuwanie i scalanieOrUpload). W zależności od tego, którą z poniższych akcji wybierzesz, tylko określone pola muszą być uwzględnione w danym dokumencie:


### <a name="formulate-your-query"></a>Formułowanie zapytania
Istnieją dwie metody [przeszukiwania indeksu przy użyciu interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Pierwsza z nich polega na wysłaniu żądania HTTP POST, w ramach którego parametry zapytania są definiowane w obiekcie JSON w treści żądania. Druga metoda obejmuje wysłanie żądania HTTP GET, w ramach którego parametry zapytania są definiowane w adresie URL żądania. W przypadku żądania POST limity dotyczące rozmiaru parametrów zapytania są [luźniejsze](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) niż dla żądania GET. Z tego powodu zaleca się używanie żądania POST, o ile nie występują specjalne okoliczności, w których korzystanie z żądania GET jest wygodniejsze.

Zarówno dla żądania POST, jak i GET zawartość adresu URL żądania musi obejmować *nazwę usługi*, *nazwę indeksu* oraz odpowiednią *wersję interfejsu API* (w momencie publikowania tego dokumentu aktualna wersja interfejsu API to `2019-05-06`). W przypadku żądania GET parametry zapytania są określane w *ciągu zapytania* na końcu adresu URL. Format adresu URL został przedstawiony poniżej:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06

Format dla żądania POST jest taki sam, ale parametry ciągu zapytania zawierają tylko element api-version.


## <a name="pulling-data-into-an-index"></a>Ściąganie danych do indeksu
Model polegający na ściąganiu obejmuje przeszukiwanie obsługiwanego źródła danych i automatyczne przekazywanie danych do indeksu. W usłudze Azure Cognitive Search ta funkcja jest implementowana za pośrednictwem *indeksatorów,* obecnie dostępnych dla tych platform:

+ [Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](https://aka.ms/documentdb-search-indexer)
+ [Baza danych Azure SQL Database i program SQL Server na maszynach wirtualnych platformy Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Indeksatory łączą indeks ze źródłem danych (zwykle tabelą, widokiem lub równoważną strukturą) i mapują pola źródłowe na równoważne pola w indeksie. W czasie wykonywania zestaw wierszy jest automatycznie przekształcany w dane w formacie JSON i ładowany do określonego indeksu. Wszystkie indeksatory obsługują planowanie, dzięki czemu możesz określić, jak często dane mają być odświeżane. Większość indeksatorów udostępnia śledzenie zmian, jeśli źródło danych obsługuje tę funkcję. Dzięki śledzeniu zmian i usuwania istniejących dokumentów oraz rozpoznawaniu nowych dokumentów indeksatory eliminują konieczność aktywnego zarządzania danymi w indeksie. 


### <a name="how-to-pull-data-into-an-azure-cognitive-search-index"></a>Jak pobierać dane do indeksu usługi Azure Cognitive Search

Funkcja indeksatora jest udostępniona w witrynie [Azure Portal](search-import-data-portal.md), interfejsie [API REST](/rest/api/searchservice/Indexer-operations) i zestawie [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperationsextensions). 

Zaletą korzystania z portalu jest to, że usługa Azure Cognitive Search zwykle może generować domyślny schemat indeksu, odczytywanie metadanych źródłowego zestawu danych. Wygenerowany indeks można modyfikować, dopóki indeks jest przetwarzany, po czym dozwolone są tylko te zmiany schematu, które nie wymagają ponownego indeksowania. Jeśli żądane zmiany wpływają bezpośrednio na schemat, konieczne będzie odbudowanie indeksu. 

## <a name="verify-data-import-with-search-explorer"></a>Weryfikowanie importu danych za pomocą Eksploratora wyszukiwania

Szybkim sposobem przeprowadzenia wstępnego sprawdzenia przekazywania dokumentu jest użycie **Eksploratora wyszukiwania** w portalu. Eksplorator umożliwia wykonywanie zapytań względem indeksu bez konieczności pisania kodu. Środowisko wyszukiwania jest oparte na ustawieniach domyślnych, takich jak [prosta składnia](/rest/api/searchservice/simple-query-syntax-in-azure-search) i [domyślny parametr zapytania searchMode](/rest/api/searchservice/search-documents). Wyniki są zwracane w formacie JSON, co umożliwia inspekcję całego dokumentu.

> [!TIP]
> Wiele [przykładów kodu usługi Azure Cognitive Search](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) zawiera osadzone lub łatwo dostępne zestawy danych, oferując łatwy sposób na rozpoczęcie pracy. Portal udostępnia również przykładowy indeksator i źródło danych składające się z małego zestawu danych nieruchomości (o nazwie „realestate-us-sample”). Po uruchomieniu wstępnie skonfigurowanego indeksatora w przykładowym źródle danych indeks jest tworzony i ładowany z dokumentami, które następnie mogą być wyszukiwane w Eksploratorze wyszukiwania lub według kodu, który piszesz.

## <a name="see-also"></a>Zobacz też

+ [Omówienie indeksatora](search-indexer-overview.md)
+ [Przewodnik po portalu: tworzenie i ładowanie indeksu oraz wykonywanie na nim zapytań](search-get-started-portal.md)
