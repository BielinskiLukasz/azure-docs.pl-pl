---
title: Filtry zabezpieczeń do przycinania wyników — Azure Search
description: Kontrola dostępu do zawartości Azure Search przy użyciu filtrów zabezpieczeń i tożsamości użytkowników.
ms.service: search
ms.topic: conceptual
services: search
ms.date: 05/02/2019
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
ms.custom: seodec2018
ms.openlocfilehash: 4d1ffa5b29a56d32a4f6a8ccf40f5bafd27795e6
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186488"
---
# <a name="security-filters-for-trimming-results-in-azure-search"></a>Filtry zabezpieczeń do przycinania wyników w Azure Search

Filtry zabezpieczeń można stosować do przycinania wyników wyszukiwania w Azure Search na podstawie tożsamości użytkownika. To środowisko wyszukiwania zwykle wymaga porównania tożsamości osoby, która chce przeszukać w odniesieniu do pola zawierającego zasady, które mają uprawnienia do dokumentu. Po znalezieniu dopasowania użytkownik lub podmiot zabezpieczeń (na przykład grupa lub rola) ma dostęp do tego dokumentu.

Jednym ze sposobów osiągnięcia filtrowania zabezpieczeń jest skomplikowane rozłączenie wyrażeń równości: na przykład `Id eq 'id1' or Id eq 'id2'`, i tak dalej. Takie podejście jest podatne na błędy, trudne do utrzymania i w przypadkach, gdy lista zawiera setki lub tysiące wartości, spowalnia czas odpowiedzi kwerendy o wiele sekund. 

Łatwiejszym i szybszym podejściem jest `search.in` użycie funkcji. Jeśli używasz `search.in(Id, 'id1, id2, ...')` zamiast wyrażenia równości, możesz oczekiwać, że czasy odpowiedzi są podrzędne.

W tym artykule pokazano, jak wykonać filtrowanie zabezpieczeń, wykonując następujące czynności:
> [!div class="checklist"]
> * Utwórz pole, które zawiera identyfikatory podmiotu zabezpieczeń 
> * Wypchnij lub zaktualizuj istniejące dokumenty z odpowiednimi identyfikatorami podmiotu zabezpieczeń
> * Wydaj żądanie `search.in` Search`filter`

>[!NOTE]
> Proces pobierania identyfikatorów podmiotu zabezpieczeń nie został uwzględniony w tym dokumencie. Należy uzyskać od dostawcy usługi tożsamości.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz [subskrypcję platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), [usługę Azure Search](https://docs.microsoft.com/azure/search/search-create-service-portal)i [indeks Azure Search](https://docs.microsoft.com/azure/search/search-create-index-portal).  

## <a name="create-security-field"></a>Utwórz pole zabezpieczeń

Dokumenty muszą zawierać pole określające, które grupy mają dostęp. Te informacje staną się kryteriami filtrowania, względem których dokumenty są wybierane lub odrzucane z zestawu wyników zwróconego do wystawcy.
Załóżmy, że mamy indeks zabezpieczonych plików, a każdy plik jest dostępny dla innego zestawu użytkowników.
1. Dodaj pole `group_ids` ( `Collection(Edm.String)`w tym miejscu możesz wybrać dowolną nazwę). Upewnij się, że pole ma `filterable` atrybut ustawiony na `true` tak, aby wyniki wyszukiwania zostały odfiltrowane na podstawie dostępu użytkownika. Na przykład jeśli ustawisz `group_ids` pole na `["group_id1, group_id2"]` dla dokumentu z `file_name` "secured_file_b", tylko użytkownicy, którzy należą do identyfikatorów grup "group_id1" lub "group_id2" mają dostęp do odczytu do tego pliku.
   Upewnij się, że `retrievable` atrybut pola jest ustawiony na `false` tak, aby nie był zwracany jako część żądania wyszukiwania.
2. Dodaj `file_id` także pola `file_name` i dla przykładu.  

```JSON
{
    "name": "securedfiles",  
    "fields": [
        {"name": "file_id", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "file_name", "type": "Edm.String"},
        {"name": "group_ids", "type": "Collection(Edm.String)", "filterable": true, "retrievable": false}
    ]
}
```

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Wypychanie danych do indeksu przy użyciu interfejsu API REST
  
Wydaj żądanie HTTP POST do punktu końcowego adresu URL Twojego indeksu. Treść żądania HTTP jest obiektem JSON zawierającym dokumenty, które mają zostać dodane:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=2019-05-06  
Content-Type: application/json
api-key: [admin key]
```

W treści żądania Określ zawartość dokumentów:

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "file_id": "1",
            "file_name": "secured_file_a",
            "group_ids": ["group_id1"]
        },
        {
            "@search.action": "upload",
            "file_id": "2",
            "file_name": "secured_file_b",
            "group_ids": ["group_id1", "group_id2"]
        },
        {
            "@search.action": "upload",
            "file_id": "3",
            "file_name": "secured_file_c",
            "group_ids": ["group_id5", "group_id6"]
        }
    ]
}
```

Jeśli musisz zaktualizować istniejący dokument z listą grup, możesz użyć `merge` akcji lub: `mergeOrUpload`

```JSON
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "file_id": "3",
            "group_ids": ["group_id7", "group_id8", "group_id9"]
        }
    ]
}
```

Aby uzyskać szczegółowe informacje na temat dodawania lub aktualizowania dokumentów, można odczytać [Edytowanie dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>Zastosuj filtr zabezpieczeń

Aby można było przyciąć dokumenty na podstawie `group_ids` dostępu, należy wydać zapytanie wyszukiwania `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` z filtrem, gdzie "group_id1, group_id2,..." są grupami, do których należy wystawca żądania wyszukiwania.
Ten filtr dopasowuje wszystkie dokumenty, dla `group_ids` których pole zawiera jeden z podanego identyfikatorów.
Aby uzyskać szczegółowe informacje na temat wyszukiwania dokumentów przy użyciu Azure Search, można odczytywać [dokumenty](https://docs.microsoft.com/rest/api/searchservice/search-documents)przeszukiwania.
Należy zauważyć, że w tym przykładzie pokazano, jak przeszukiwać dokumenty przy użyciu żądania POST.

Wydaj żądanie HTTP POST:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=2019-05-06
Content-Type: application/json  
api-key: [admin or query key]
```

Określ filtr w treści żądania:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Należy pobrać dokumenty z powrotem, gdzie `group_ids` zawiera "group_id1" lub "group_id2". Innymi słowy, uzyskasz dokumenty, do których wystawcy żądania ma dostęp do odczytu.

```JSON
{
 [
   {
    "@search.score":1.0,
     "file_id":"1",
     "file_name":"secured_file_a",
   },
   {
     "@search.score":1.0,
     "file_id":"2",
     "file_name":"secured_file_b"
   }
 ]
}
```
## <a name="conclusion"></a>Wniosek

W ten sposób można filtrować wyniki w oparciu o tożsamość użytkownika i funkcję `search.in()` Azure Search. Za pomocą tej funkcji można przekazać identyfikatory zasad dla użytkownika żądającego, aby dopasować je do identyfikatorów podmiotu zabezpieczeń skojarzonych z każdym dokumentem docelowym. Gdy żądanie wyszukiwania jest obsługiwane, `search.in` funkcja filtruje wyniki wyszukiwania, dla których żaden z podmiotów zabezpieczeń użytkownika nie ma dostępu do odczytu. Identyfikatory podmiotów mogą reprezentować elementy, takie jak grupy zabezpieczeń, role, a nawet własna tożsamość użytkownika.
 
## <a name="see-also"></a>Zobacz także

+ [Active Directory kontroli dostępu opartej na tożsamościach przy użyciu filtrów Azure Search](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtry w Azure Search](search-filters.md)
+ [Bezpieczeństwo danych i kontrola dostępu w Azure Search operacjach](search-security-overview.md)