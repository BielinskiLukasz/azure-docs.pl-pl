---
title: Synonimy, rozszerzenia zapytania nad indeksem wyszukiwania — usługa Azure Search
description: Utwórz mapę synonimów, aby rozszerzyć zakres zapytania wyszukiwania w indeksie usługi Azure Search. Zakres jest rozszerzony do uwzględnienia równoważnych terminów, których udzielasz na liście.
author: brjohnstmsft
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
manager: jlembicz
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 4383cc327d8058ca44acd892f41a7a256e3b1727
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570440"
---
# <a name="synonyms-in-azure-search"></a>Synonimy w usłudze Azure Search

Synonimy w wyszukiwarkach kojarzą równoważne wyrażenia, które niejawnie rozszerzyć zakres kwerendy, bez użytkownika konieczności faktycznego termin. Na przykład biorąc pod uwagę skojarzenia "dog" i synonim termin "canine" i "puppy" dokumenty zawierające "dog", "pies" lub "puppy" będzie wchodzą w zakres kwerendy.

W usłudze Azure Search rozszerzenia synonim odbywa się w czasie wykonywania zapytania. Można dodać mapy synonimów do usługi za pomocą nie przerw w działaniu na istniejące operacje. Możesz dodać **synonymMaps** właściwości definicji pola, bez konieczności ponownego kompilowania indeksu.

## <a name="feature-availability"></a>Dostępność funkcji

Funkcja synonimów jest obsługiwana w najnowszej wersji interfejsu api (interfejs api-version = 2017-11-11). Obecnie witryna Azure Portal nie jest obsługiwana.

## <a name="how-to-use-synonyms-in-azure-search"></a>Jak używać synonimów w usłudze Azure search

W usłudze Azure Search Obsługa synonimów jest oparty na map synonimów, definiujących i Przekaż do usługi. Mapy te stanowią niezależnym zasobem (takich jak indeksy i źródłami danych) i może służyć w dowolnym polu możliwym do przeszukania w dowolnym indeksie w usłudze wyszukiwania.

Mapy synonimów i indeksy są obsługiwane niezależnie. Po zdefiniowaniu mapy synonimów i przekaż go do usługi, można włączyć funkcję synonimów w polu, dodając nową właściwość o nazwie **synonymMaps** w definicji pola. Tworzenie, aktualizowanie i usuwanie mapy synonimów jest zawsze operacji na dokumencie całości, co oznacza, że możesz nie utworzenia, aktualizacji lub usunięcia części mapy synonimów przyrostowo. Aktualizowanie nawet pojedynczy wpis wymaga załadowanie.

Włączenie synonimów aplikacji wyszukiwania jest procesem dwuetapowym:

1.  Dodaj mapę synonimów do usługi wyszukiwania za pomocą interfejsów API poniżej.  

2.  Skonfiguruj polu możliwym do przeszukania, aby używać mapy synonimów w definicji indeksu.

### <a name="synonymmaps-resource-apis"></a>Interfejsy API SynonymMaps zasobów

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Dodać lub zaktualizować mapę synonimów w ramach usługi, za pomocą POST lub PUT.

Mapy synonimów są przekazywane do usługi za pośrednictwem POST lub PUT. Każda reguła muszą być rozdzielone znak nowego wiersza (\n). Można zdefiniować maksymalnie 5000 reguł na mapie synonimów w to bezpłatna usługa i 10 000 reguł w innych jednostkach SKU. Każda reguła może mieć maksymalnie 20 rozszerzenia.

Mapy synonimów musi być w formacie Apache Solr, co zostało wyjaśnione poniżej. Jeśli masz istniejący słownik synonimów w innym formacie i chcesz używać go bezpośrednio, Daj nam znać na [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Można utworzyć nowej mapy synonimów używanie żądania POST protokołu HTTP, jak w poniższym przykładzie:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2017-11-11
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

Alternatywnie można użyć PUT i określ nazwę mapy synonimów w identyfikatorze URI. Mapy synonimów nie istnieje, zostanie utworzony.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2017-11-11
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Format synonim Apache Solr

Solr format obsługuje mapowań synonimów swoimi odpowiednikami i jawne. Reguły mapowania stosować się do synonimu specyfikacja filtru "open source" Apache Solr, opisane w tym dokumencie: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Poniżej znajduje się przykładowa reguła równoważne synonimów.
```
USA, United States, United States of America
```

Z regułą powyżej zapytania wyszukiwania rozwinie "USA", "USA" lub "United States" lub "USA".

Strzałka jest oznaczona jawnego mapowania "= >". Jeśli zostanie określony, sekwencja termin zapytanie wyszukiwania, który odpowiada po lewej stronie "= >" zostanie zamieniony alternatywy po prawej stronie. Biorąc pod uwagę poniższe reguły, wyszukiwanie zapytania "Waszyngton", "Wash." lub "WA" będą wszystkie zostać przepisane, aby "WA". Jawnego mapowania tylko stosuje się w określonym kierunku i nie ponownie napisz zapytanie "WA" do "Waszyngton" w tym przypadku.
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>Mapy synonimów listy w ramach usługi.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2017-11-11
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>Pobierz mapę synonimów w ramach usługi.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2017-11-11
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>Usuń mapę synonimów w ramach usługi.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2017-11-11
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Skonfiguruj polu możliwym do przeszukania, aby używać mapy synonimów w definicji indeksu.

Nowe właściwości pola **synonymMaps** może służyć do określania mapę synonimów do użytku w polu możliwym do przeszukania. Mapy synonimów są zasoby na poziomie usługi i mogą być przywoływane przez dowolne pole indeksu w usłudze.

    POST https://[servicename].search.windows.net/indexes?api-version=2017-11-11
    api-key: [admin key]

    {
       "name":"myindex",
       "fields":[
          {
             "name":"id",
             "type":"Edm.String",
             "key":true
          },
          {
             "name":"name",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"en.lucene",
             "synonymMaps":[
                "mysynonymmap"
             ]
          },
          {
             "name":"name_jp",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"ja.microsoft",
             "synonymMaps":[
                "japanesesynonymmap"
             ]
          }
       ]
    }

**synonymMaps** można określić dla pola z możliwością wyszukiwania typu "Edm.String" lub "Collection(Edm.String)".

> [!NOTE]
> Może mieć tylko jeden synonim mapowanie jednego pola. Jeśli chcesz użyć wiele map synonimów, Daj nam znać na [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Wpływ synonimy dla innych funkcji wyszukiwania

Funkcję synonimów ponownie zapisuje oryginalnego zapytania z synonimy za pomocą operatora OR. Z tego powodu wyróżnianie trafień i profile oceniania traktować oryginalnego terminów i synonimy jako równoważne.

Funkcja synonimów dotyczy zapytań wyszukiwania tworzonych i nie ma zastosowania do filtrów lub zestawów reguł. Podobnie sugestie dotyczą tylko oryginalny warunek; synonim dopasowania nie są wyświetlane w odpowiedzi.

Synonim rozszerzenia nie dotyczą terminy wyszukiwania symboli wieloznacznych; Prefiks rozmyte oraz postanowienia wyrażenie regularne nie są rozwinięte.

Jeśli zachodzi potrzeba zrobić pojedyncze zapytanie, które mają zastosowanie rozszerzenia synonimów i symboli wieloznacznych, wyrażenie regularne lub wyszukiwania rozmytego, możesz połączyć zapytań przy użyciu składni OR. Na przykład aby połączyć synonimy z symbolami wieloznacznymi dla prosta składnia zapytań, termin będzie `<query> | <query>*`.

## <a name="tips-for-building-a-synonym-map"></a>Porady dotyczące tworzenia mapy synonimów

- Mapę synonimów zwięzły, skierowany do dobrze zaprojektowane jest bardziej wydajne niż to pełna lista możliwych dopasowań. Zbyt duże lub zbyt złożone słowników dłużej do analizy i mają wpływ na opóźnienie zapytań, jeśli zapytanie jest rozszerzany, aby wiele synonimów. Zamiast odgadnięcia warunki mogą być używane, można uzyskać rzeczywistych warunków za pośrednictwem [raport analizy ruchu wyszukiwania](search-traffic-analytics.md).

- Jak wykonywać zarówno wersja wstępna, jak i sprawdzania poprawności, Włącz, a następnie użycie tego raportu można określić dokładne warunki, które będą korzystać z dopasowania synonimów i następnie w dalszym ciągu używać go jako weryfikacji, czy mapę synonimów jest tworzenie lepszych wyników. Wstępnie zdefiniowanego raportu Kafelki "najczęściej występujące zapytania wyszukiwania" i "zapytania wyszukiwania z zerowym wynikiem" zapewni niezbędne informacje.

- Możesz utworzyć wiele map synonimów aplikacji wyszukiwania (na przykład za pomocą języka, jeśli aplikacja obsługuje wielu języków bazy klientów). Obecnie pole można używać tylko jednego z nich. Właściwość synonymMaps pola może aktualizować w dowolnym momencie.

## <a name="next-steps"></a>Kolejne kroki

- Jeśli masz istniejący indeks w środowisku deweloperskim (nie produkcyjnych) eksperymentować z małych słownika, aby zobaczyć, jak dodanie synonimów zmienia funkcję wyszukiwania, w tym wpływu na profile oceniania, wyróżnianie trafień i sugestie.

- [Włącz analiza ruchu wyszukiwania](search-traffic-analytics.md) użyć wstępnie zdefiniowanego raportu usługi Power BI, aby dowiedzieć się, których terminy są używane najczęściej i te, które zwracają zero dokumenty. Korzystając z wglądów, sprawdź, czy słownik obejmujący synonimy nieproduktywne zapytań, które powinny rozpoznawania dokumentów w indeksie.
