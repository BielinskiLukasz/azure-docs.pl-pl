---
title: 'Szybki Start: Tworzenie indeksu wyszukiwania w programie Poster przy użyciu interfejsów API REST'
titleSuffix: Azure Cognitive Search
description: W tym przewodniku szybki start interfejsu API REST dowiesz się, jak wywoływać interfejsy API REST platformy Azure Wyszukiwanie poznawcze przy użyciu programu Poster oraz dane przykładowe i definicje.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/04/2019
ms.openlocfilehash: 9014a83596a3eee71f43d12a73188c1c43b832af
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406953"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-postman-using-rest-apis"></a>Szybki Start: Tworzenie indeksu Wyszukiwanie poznawcze platformy Azure w programie Poster przy użyciu interfejsów API REST
> [!div class="op_single_selector"]
> * [Postman](search-get-started-postman.md)
> * [C#](search-create-index-dotnet.md)
> * [Python](search-get-started-python.md)
> * [Portal](search-get-started-portal.md)
> * [Program PowerShell](search-howto-dotnet-sdk.md)
>*

Jednym z najprostszych sposobów eksplorowania [interfejsów API REST platformy Azure wyszukiwanie poznawcze](https://docs.microsoft.com/rest/api/searchservice) jest użycie programu Poster lub innego narzędzia do testowania sieci Web w celu sformułowania żądań HTTP i sprawdzenia odpowiedzi. Za pomocą odpowiednich narzędzi i niniejszej instrukcji możesz wysyłać żądania i wyświetlać odpowiedzi przed napisaniem jakiegokolwiek kodu.

W tym artykule wyjaśniono, jak w sposób interaktywny sformułować żądania. Alternatywnie można [pobrać i zaimportować kolekcję programu Poster](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) , aby użyć wstępnie zdefiniowanych żądań.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start wymaga następujących usług i narzędzi. 

+ [Aplikacja klasyczna programu post](https://www.getpostman.com/) jest używana do wysyłania żądań do usługi Azure wyszukiwanie poznawcze.

+ [Utwórz usługę Azure wyszukiwanie poznawcze](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. Możesz użyć bezpłatnej usługi dla tego przewodnika Szybki Start. 

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona razem z usługą, więc jeśli do subskrypcji dodano Wyszukiwanie poznawcze platformy Azure, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W obszarze **ustawienia** > **klucze**Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

![Pobieranie punktu końcowego HTTP i klucza dostępu](media/search-get-started-postman/get-url-key.png "Pobieranie punktu końcowego HTTP i klucza dostępu")

Wszystkie żądania wymagają klucza API dla każdego żądania wysyłanego do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="connect-to-azure-cognitive-search"></a>Nawiązywanie połączenia z usługą Azure Wyszukiwanie poznawcze

W tej sekcji Użyj wybranego narzędzia sieci Web, aby skonfigurować połączenia z usługą Azure Wyszukiwanie poznawcze. Każde narzędzie utrzymuje informacje nagłówka żądania dla sesji, co oznacza, że wystarczy tylko raz wprowadzić klucz API-Key i Content-Type.

Dla każdego narzędzia należy wybrać polecenie (GET, POST, PUT itd.), podać punkt końcowy adresu URL, a w przypadku niektórych zadań podać kod JSON w treści żądania. Zastąp nazwę usługi wyszukiwania (nazwa użytkownika-SEARCH-SERVICE-NAME) prawidłową wartością. Dodaj `$select=name`, aby zwrócić tylko nazwę każdego indeksu. 

    https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name

Zwróć uwagę na prefiks protokołu HTTPS, nazwę usługi, nazwę obiektu (w tym przypadku kolekcja indeksów) i [wersję interfejsu API-Version](search-api-versions.md). Interfejs API-Version jest wymagana, ponieważ jako `?api-version=2019-05-06` dla bieżącej wersji jest określony ciąg małymi literami. Wersje interfejsu API są regularnie aktualizowane. Uwzględnienie parametru api-version w każdym żądaniu daje pełną kontrolę nad tym, która z nich jest używana.  

Kompozycja nagłówka żądania zawiera dwa elementy, typ zawartości oraz klucz API-Key służący do uwierzytelniania w usłudze Azure Wyszukiwanie poznawcze. Zastąp klucz interfejsu API administratora (parametr-AZURE-SEARCH-ADMIN-API-KEY) prawidłową wartością. 

    api-key: <YOUR-AZURE-SEARCH-ADMIN-API-KEY>
    Content-Type: application/json

W programie Poster należy sformułować żądanie, które wygląda podobnie do poniższego zrzutu ekranu. Wybierz pozycję **Get** jako zlecenie, podaj adres URL, a następnie kliknij pozycję **Wyślij**. To polecenie nawiązuje połączenie z usługą Azure Wyszukiwanie poznawcze, odczytuje kolekcję indeksów i zwraca kod stanu HTTP 200 na pomyślnym połączeniu. Jeśli usługa ma już indeksy, odpowiedź będzie zawierać również definicje indeksów.

![Adres URL i nagłówek żądania post](media/search-get-started-postman/postman-url.png "Adres URL i nagłówek żądania post")

## <a name="1---create-an-index"></a>1 — Tworzenie indeksu

Na platformie Azure Wyszukiwanie poznawcze zwykle tworzysz indeks przed załadowaniem go z danymi. Dla tego zadania jest używany [interfejs API Rest tworzenia indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index) . 

Adres URL jest rozszerzony tak, aby zawierał `hotels` nazwę indeksu.

Aby to zrobić w programie Poster:

1. Zmień czasownik na wartość **Put**.

2. Kopiuj w tym adresie URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06`.

3. Podaj definicję indeksu (kod przygotowany do kopiowania jest podany poniżej) w treści żądania.

4. Kliknij pozycję **Wyślij**.

![Indeksowanie dokumentu JSON w treści żądania](media/search-get-started-postman/postman-request.png "Indeksowanie dokumentu JSON w treści żądania")

### <a name="index-definition"></a>Definicja indeksu

Kolekcja Fields definiuje strukturę dokumentu. Każdy dokument musi mieć te pola, a każde pole musi mieć typ danych. Pola ciągów są używane podczas wyszukiwania pełnotekstowego, możesz więc rzutować dane numeryczne jako ciągi, jeśli potrzebujesz, aby ta zawartość dawała możliwość wyszukiwania.

Atrybuty w polu określają dozwoloną akcję. Interfejsy API REST domyślnie umożliwiają wiele akcji. Na przykład wszystkie ciągi domyślnie umożliwiają wyszukiwanie, pobieranie, filtrowanie i tworzenie aspektów. Często należy ustawić atrybuty tylko wtedy, gdy trzeba wyłączyć zachowanie.

```json
{
    "name": "hotels-quickstart",  
    "fields": [
        {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
        {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
        {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
        {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
        {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
        {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
        {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
        {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
        {"name": "Address", "type": "Edm.ComplexType", 
        "fields": [
        {"name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true},
        {"name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
        {"name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
        {"name": "PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
        {"name": "Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true}
        ]
     }
  ]
}
```

Po przesłaniu tego żądania powinna pojawić się odpowiedź 201 protokołu HTTP wskazująca, że indeks został pomyślnie utworzony. Możesz sprawdzić tę akcję w portalu, ale pamiętaj, że strona portalu ma odstępy czasowe odświeżania, więc wyrównanie może zająć minutę lub dwie.

> [!TIP]
> Jeśli otrzymasz odpowiedź 504 protokołu HTTP, sprawdź, czy adres URL określa protokół HTTPS. Jeśli zobaczysz odpowiedź 400 lub 404 protokołu HTTP, sprawdź treść żądania, aby zweryfikować, czy nie było żadnych błędów podczas kopiowania i wklejania. Odpowiedź 403 protokołu HTTP zazwyczaj wskazuje na problem z kluczem api-key (nieprawidłowy klucz lub problem ze składnią klucza api-key).

## <a name="2---load-documents"></a>2 — ładowanie dokumentów

Tworzenie indeksu i wypełnianie indeksu to oddzielne kroki. Na platformie Azure Wyszukiwanie poznawcze indeks zawiera wszystkie dane, które mogą być możliwe do przeszukania, które można podać jako dokumenty JSON. Dla tego zadania jest używany [interfejs API REST Dodawanie, aktualizowanie lub usuwanie dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) . 

Adres URL jest rozszerzony tak, aby obejmował `docs` kolekcje i `index` operację.

Aby to zrobić w programie Poster:

1. Zmień zlecenie na **POST**.

2. Kopiuj w tym adresie URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06`.

3. Podaj dokumenty JSON (kod przygotowany do kopiowania poniżej) w treści żądania.

4. Kliknij pozycję **Wyślij**.

![Dokumenty JSON w treści żądania](media/search-get-started-postman/postman-docs.png "Dokumenty JSON w treści żądania")

### <a name="json-documents-to-load-into-the-index"></a>Dokumenty JSON do załadowania do indeksu

Obszar Request Body (Treść żądania) zawiera cztery dokumenty, które mają zostać dodane do indeksu hotels.

```json
{
    "value": [
    {
    "@search.action": "upload",
    "HotelId": "1",
    "HotelName": "Secret Point Motel",
    "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
    "Category": "Boutique",
    "Tags": [ "pool", "air conditioning", "concierge" ],
    "ParkingIncluded": false,
    "LastRenovationDate": "1970-01-18T00:00:00Z",
    "Rating": 3.60,
    "Address": 
        {
        "StreetAddress": "677 5th Ave",
        "City": "New York",
        "StateProvince": "NY",
        "PostalCode": "10022",
        "Country": "USA"
        } 
    },
    {
    "@search.action": "upload",
    "HotelId": "2",
    "HotelName": "Twin Dome Motel",
    "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
    "Category": "Boutique",
    "Tags": [ "pool", "free wifi", "concierge" ],
    "ParkingIncluded": false,
    "LastRenovationDate": "1979-02-18T00:00:00Z",
    "Rating": 3.60,
    "Address": 
        {
        "StreetAddress": "140 University Town Center Dr",
        "City": "Sarasota",
        "StateProvince": "FL",
        "PostalCode": "34243",
        "Country": "USA"
        } 
    },
    {
    "@search.action": "upload",
    "HotelId": "3",
    "HotelName": "Triple Landscape Hotel",
    "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
    "Category": "Resort and Spa",
    "Tags": [ "air conditioning", "bar", "continental breakfast" ],
    "ParkingIncluded": true,
    "LastRenovationDate": "2015-09-20T00:00:00Z",
    "Rating": 4.80,
    "Address": 
        {
        "StreetAddress": "3393 Peachtree Rd",
        "City": "Atlanta",
        "StateProvince": "GA",
        "PostalCode": "30326",
        "Country": "USA"
        } 
    },
    {
    "@search.action": "upload",
    "HotelId": "4",
    "HotelName": "Sublime Cliff Hotel",
    "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
    "Category": "Boutique",
    "Tags": [ "concierge", "view", "24-hour front desk service" ],
    "ParkingIncluded": true,
    "LastRenovationDate": "1960-02-06T00:00:00Z",
    "Rating": 4.60,
    "Address": 
        {
        "StreetAddress": "7400 San Pedro Ave",
        "City": "San Antonio",
        "StateProvince": "TX",
        "PostalCode": "78216",
        "Country": "USA"
        }
    }
  ]
}
```

W ciągu kilku sekund na liście sesji powinna zostać wyświetlona odpowiedź HTTP 201. Oznacza to, że dokumenty zostały pomyślnie utworzone. 

Jeśli otrzymasz odpowiedź 207, przekazanie co najmniej jednego dokumentu nie powiodło się. Jeśli otrzymasz odpowiedź 404, wystąpił błąd składniowy w nagłówku lub w treści żądania: zweryfikuj, czy zmiana punktu końcowego uwzględniła `/docs/index`.

> [!Tip]
> Dla wybranych źródeł danych możesz wybrać alternatywne podejście *indeksatora*, które upraszcza i zmniejsza ilość kodu wymaganą do indeksowania. Aby uzyskać więcej informacji, zobacz [Operacje indeksatora](https://docs.microsoft.com/rest/api/searchservice/indexer-operations).


## <a name="3---search-an-index"></a>3 — Przeszukiwanie indeksu

Teraz, gdy indeks i dokumenty są ładowane, można wysyłać zapytania do nich za pomocą [interfejsu API REST dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Adres URL jest rozszerzony, aby uwzględnić wyrażenie zapytania, określone za pomocą operatora wyszukiwania.

Aby to zrobić w programie Poster:

1. Zmień zlecenie, aby **uzyskać**.

2. Kopiuj w tym adresie URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$count=true&api-version=2019-05-06`.

3. Kliknij pozycję **Wyślij**.

To zapytanie jest puste i zwraca liczbę dokumentów w wynikach wyszukiwania. Po kliknięciu przycisku **Wyślij** żądanie i odpowiedź powinny wyglądać podobnie jak na poniższym zrzucie ekranu z narzędzia Postman. Kod stanu powinien być równy 200.

 ![Pobierz ciąg wyszukiwania w adresie URL](media/search-get-started-postman/postman-query.png "Pobierz ciąg wyszukiwania w adresie URL")

Wypróbuj kilka innych przykładów zapytania, aby uzyskać działanie dla składni. Możesz wykonać wyszukiwanie ciągów, Verbatim $filter zapytań, ograniczyć zestaw wyników, określić zakres wyszukiwania do określonych pól itd.

Zamień bieżący adres URL na poniższe, a następnie kliknij pozycję **Wyślij** za każdym razem, aby wyświetlić wyniki.

```
# Query example 1 - Search on restaurant and wifi
# Return only the HotelName, Description, and Tags fields
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=restaurant wifi&$count=true&$select=HotelName,Description,Tags&api-version=2019-05-06

# Query example 2 - Apply a filter to the index to find hotels rated 4 or highter
# Returns the HotelName and Rating. Two documents match
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$filter=Rating gt 4&$select=HotelName,Rating&api-version=2019-05-06

# Query example 3 - Take the top two results, and show only HotelName and Category in the results
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=boutique&$top=2&$select=HotelName,Category&api-version=2019-05-06

# Query example 4 - Sort by a specific field (Address/City) in ascending order
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating&api-version=2019-05-06
```

## <a name="get-index-properties"></a>Pobierz właściwości indeksu
Można również użyć [Get Statistics](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) do wykonywania zapytań dotyczących liczby dokumentów i rozmiaru indeksu: 

```
https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/stats?api-version=2019-05-06
```

Dodanie `/stats` do adresu URL zwraca informacje o indeksie. W narzędziu Postman Twoje żądanie powinno wyglądać podobnie do poniższego, zaś odpowiedź zawiera liczbę dokumentów i użyte miejsce w bajtach.

 ![Pobierz informacje o indeksie](media/search-get-started-postman/postman-system-query.png "Pobierz informacje o indeksie")

Zwróć uwagę, że składnia parametru api-version różni się. Dla tego żądania użyj `?`, aby dołączyć parametr api-version. `?` oddziela ścieżkę URL od ciągu zapytania, podczas & oddziela każdą parę "Name = wartość" w ciągu zapytania. Dla tego zapytania parametr api-version jest pierwszym i jedynym elementem ciągu zapytania.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy pracujesz nad własną subskrypcją, dobrym pomysłem jest zakończenie projektu w celu ustalenia, czy nadal potrzebujesz utworzonych zasobów. Zasoby po lewej stronie mogą być kosztowne. Możesz usunąć zasoby pojedynczo lub usunąć grupę zasobów, aby usunąć cały zestaw zasobów.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit. 

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak wykonywać podstawowe zadania, możesz przejść do przodu z dodatkowymi wywołaniami interfejsu API REST, aby uzyskać bardziej zaawansowane funkcje, takie jak indeksatory, lub [skonfigurować potok wyszukiwania poznawczego](cognitive-search-tutorial-blob.md). W następnym kroku zalecamy następujące łącze:

> [!div class="nextstepaction"]
> [Samouczek REST: indeksowanie i wyszukiwanie danych częściowo strukturalnych (obiektów BLOB JSON) na platformie Azure Wyszukiwanie poznawcze](search-semi-structured-data.md)
