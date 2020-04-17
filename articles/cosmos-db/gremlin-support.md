---
title: Obsługa języka Gremlin w usłudze Azure Cosmos DB
description: Poznaj język Gremlin ze struktury Apache TinkerPop. Dowiedz się, jakie funkcjonalności i kroki są dostępne w usłudze Azure Cosmos DB
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/24/2019
ms.author: lbosq
ms.openlocfilehash: 59c45497ea6d9fcb216c83060a858ee6c96f1151
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81449973"
---
# <a name="azure-cosmos-db-gremlin-graph-support"></a>Obsługa grafu Gremlin w usłudze Azure Cosmos DB
Usługa Azure Cosmos DB obsługuje język przechodzenia wykresu [Apache Tinkerpop,](https://tinkerpop.apache.org) znany jako [Gremlin.](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps) Język Gremlin służy do tworzenia jednostek grafu (wierzchołków i krawędzi), modyfikacji właściwości w ramach tych elementów, wykonywania zapytań i przejść oraz usuwania elementów. 

W tym artykule udostępniamy szybki instruktaż gremlin i wyliczają funkcje Gremlin, które są obsługiwane przez interfejs API Gremlin.

## <a name="compatible-client-libraries"></a>Zgodne biblioteki klienckie

W poniższej tabeli przedstawiono popularne sterowniki Gremlin, których można użyć do usługi Azure Cosmos DB:

| Pobierz | Element źródłowy | Getting Started | Obsługiwana wersja łącznika |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-DotNet) | [Gremlin.NET w witrynie GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Tworzenie grafu przy użyciu platformy .NET](create-graph-dotnet.md) | 3.4.0-RC2 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](https://tinkerpop.apache.org/javadocs/current/full/) | [Tworzenie grafu przy użyciu środowiska Java](create-graph-java.md) | 3.2.0+ |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin-JavaScript w witrynie GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-javascript) | [Tworzenie grafu przy użyciu platformy Node.js](create-graph-nodejs.md) | 3.3.4+ |
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin-Python w witrynie GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Tworzenie grafu przy użyciu środowiska Python](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin-PHP w witrynie GitHub](https://github.com/PommeVerte/gremlin-php) | [Tworzenie grafu przy użyciu środowiska PHP](create-graph-php.md) | 3.1.0 |
| [Konsola Gremlin](https://tinkerpop.apache.org/downloads.html) | [Dokumentacja dotycząca witryny TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Tworzenie grafu przy użyciu Konsoli Gremlin](create-graph-gremlin-console.md) | 3.2.0 + |

## <a name="supported-graph-objects"></a>Obsługiwane obiekty wykresu
TinkerPop jest standardem, który obejmuje szeroki zakres technologii grafów. Dlatego ma standardową terminologię do opisywania, jakie funkcje są udostępniane przez dostawcę grafu. Usługa Azure Cosmos DB zapewnia trwałą, zapisywalną bazę danych grafów o dużej współbieżności, którą można podzielić na partycje w wielu serwerach lub klastrach. 

W poniższej tabeli wymieniono funkcje struktury TinkerPop wdrażane przez usługę Azure Cosmos DB: 

| Kategoria | Wdrożenie usługi Azure Cosmos DB |  Uwagi | 
| --- | --- | --- |
| Funkcjonalności grafu | Zapewnia funkcjonalności Persistence i ConcurrentAccess. Zaprojektowana obsługa funkcjonalności Transactions | Metody komputera mogą być wdrażane przy użyciu łącznika Spark. |
| Funkcjonalności zmiennych | Obsługuje zmienne Boolean, Byte, Double, Float, Integer, Long, String | Obsługuje typy pierwotne, a zgodność z typami złożonymi jest osiągana za pomocą modelu danych |
| Funkcjonalności wierzchołków | Obsługuje funkcje RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty  | Obsługuje tworzenie, modyfikowanie i usuwanie wierzchołków |
| Funkcjonalności właściwości wierzchołków | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Obsługuje tworzenie, modyfikowanie i usuwanie właściwości wierzchołków |
| Funkcjonalności krawędzi | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Obsługuje tworzenie, modyfikowanie i usuwanie krawędzi |
| Funkcjonalności właściwości krawędzi | Properties, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Obsługuje tworzenie, modyfikowanie i usuwanie właściwości krawędzi |

## <a name="gremlin-wire-format"></a>Format drutu Gremlin

Usługa Azure Cosmos DB używa formatu JSON podczas zwracania wyników z operacji Gremlin. Usługa Azure Cosmos DB obsługuje obecnie format JSON. Na przykład poniższy fragment kodu pokazuje reprezentację JSON wierzchołka *zwróconego do klienta* z usługi Azure Cosmos DB:

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

Właściwości używane przez format JSON dla wierzchołków są opisane poniżej:

| Właściwość | Opis | 
| --- | --- | --- |
| `id` | Identyfikator wierzchołka. Musi być unikatowa (w `_partition` połączeniu z wartością, jeśli dotyczy). Jeśli nie zostanie podana żadna wartość, zostanie ona automatycznie dostarczona z identyfikatorem GUID | 
| `label` | Etykieta wierzchołka. Ta właściwość jest używana do opisywania typu jednostki. |
| `type` | Służy do odróżnienia wierzchołków od dokumentów bez grafów |
| `properties` | Pakiet właściwości zdefiniowanych przez użytkownika skojarzonych z wierzchołkiem. Każda właściwość może mieć wiele wartości. |
| `_partition` | Klucz partycji wierzchołka. Służy do [partycjonowania wykresu](graph-partitioning.md). |
| `outE` | Ta właściwość zawiera listę out krawędzi z wierzchołka. Przechowywanie informacji sąsiedztwa razem z wierzchołkiem umożliwia szybkie wykonanie przejść. Krawędzie są pogrupowane w oparciu o etykiety. |

Krawędź zawiera następujące informacje, aby pomóc w nawigacji do innych części grafu.

| Właściwość | Opis |
| --- | --- |
| `id` | Identyfikator krawędzi. Musi być unikatowa (w `_partition` połączeniu z wartością, jeśli dotyczy) |
| `label` | Etykieta krawędzi. Ta właściwość jest opcjonalna i służy do opisu typu relacji. |
| `inV` | Ta właściwość zawiera listę w wierzchołków dla krawędzi. Przechowywanie informacji sąsiedztwa razem z krawędzią umożliwia szybkie wykonanie przejść. Wierzchołki są pogrupowane w oparciu o etykiety. |
| `properties` | Pakiet właściwości zdefiniowanych przez użytkownika skojarzonych z krawędzią. Każda właściwość może mieć wiele wartości. |

Każda właściwość może przechowywać wiele wartości w tablicy. 

| Właściwość | Opis |
| --- | --- |
| `value` | Wartość właściwości.

## <a name="gremlin-steps"></a>Kroki w środowisku Gremlin
Teraz przyjrzyjmy się krokom w środowisku Gremlin obsługiwanym przez usługę Azure Cosmos DB. Aby uzyskać pełną dokumentację dotyczącą języka Gremlin, zobacz [odwołanie do struktury TinkerPop](https://tinkerpop.apache.org/docs/3.3.2/reference).

| krok | Opis | Dokumentacja dotycząca struktury TinkerPop 3.2 |
| --- | --- | --- |
| `addE` | Dodaje krawędź między dwoma wierzchołkami | [krok addE](https://tinkerpop.apache.org/docs/3.3.2/reference/#addedge-step) |
| `addV` | Dodaje wierzchołek do grafu | [krok addV](https://tinkerpop.apache.org/docs/3.3.2/reference/#addvertex-step) |
| `and` | Gwarantuje, że wszystkie przejścia zwracają wartość | [krok and](https://tinkerpop.apache.org/docs/3.3.2/reference/#and-step) |
| `as` | Modulator kroku do przypisania zmiennej do wyniku kroku | [krok as](https://tinkerpop.apache.org/docs/3.3.2/reference/#as-step) |
| `by` | Modulator kroku używany z elementami `group` i `order` | [krok by](https://tinkerpop.apache.org/docs/3.3.2/reference/#by-step) |
| `coalesce` | Zwraca pierwsze przejście, które zwraca wynik | [krok coalesce](https://tinkerpop.apache.org/docs/3.3.2/reference/#coalesce-step) |
| `constant` | Zwraca wartość stałą. Używany z krokiem `coalesce`| [krok constant](https://tinkerpop.apache.org/docs/3.3.2/reference/#constant-step) |
| `count` | Zwraca liczbę z przejścia | [krok count](https://tinkerpop.apache.org/docs/3.3.2/reference/#count-step) |
| `dedup` | Zwraca wartości z usuniętymi duplikatami | [krok dedup](https://tinkerpop.apache.org/docs/3.3.2/reference/#dedup-step) |
| `drop` | Upuszcza wartości (wierzchołek/krawędź) | [krok drop](https://tinkerpop.apache.org/docs/3.3.2/reference/#drop-step) |
| `executionProfile` | Tworzy opis wszystkich operacji generowanych przez wykonany krok Gremlin | [wykonanie Krok profilu](graph-execution-profile.md) |
| `fold` | Działa jak bariera, która oblicza agregację wyników| [krok fold](https://tinkerpop.apache.org/docs/3.3.2/reference/#fold-step) |
| `group` | Grupuje wartości w oparciu o określone etykiety| [krok group](https://tinkerpop.apache.org/docs/3.3.2/reference/#group-step) |
| `has` | Służy do filtrowania właściwości, wierzchołków i krawędzi. Obsługuje warianty `hasLabel`, `hasId`, `hasNot` i `has`. | [krok step](https://tinkerpop.apache.org/docs/3.3.2/reference/#has-step) |
| `inject` | Wstawia wartości do strumienia| [krok inject](https://tinkerpop.apache.org/docs/3.3.2/reference/#inject-step) |
| `is` | Służy do wykonywania filtru przy użyciu wyrażenia logicznego | [krok is](https://tinkerpop.apache.org/docs/3.3.2/reference/#is-step) |
| `limit` | Pozwala ograniczyć liczbę elementów podczas przechodzenia| [krok limit](https://tinkerpop.apache.org/docs/3.3.2/reference/#limit-step) |
| `local` | Krok local opakowuje sekcję przejścia, podobnie jak podzapytanie | [krok local](https://tinkerpop.apache.org/docs/3.3.2/reference/#local-step) |
| `not` | Służy do tworzenia negacji filtru | [krok not](https://tinkerpop.apache.org/docs/3.3.2/reference/#not-step) |
| `optional` | Zwraca wynik określonego przejścia, jeśli wstrzymuje wynik lub zwraca wywołujący element | [opcjonalny krok](https://tinkerpop.apache.org/docs/3.3.2/reference/#optional-step) |
| `or` | Gwarantuje, że co najmniej jedno przejście zwróci wartość | [krok or](https://tinkerpop.apache.org/docs/3.3.2/reference/#or-step) |
| `order` | Zwraca wyniki w określonej kolejności sortowania | [krok order](https://tinkerpop.apache.org/docs/3.3.2/reference/#order-step) |
| `path` | Zwraca pełną ścieżkę przejścia | [krok path](https://tinkerpop.apache.org/docs/3.3.2/reference/#path-step) |
| `project` | Projektuje właściwości jako mapę | [krok project](https://tinkerpop.apache.org/docs/3.3.2/reference/#project-step) |
| `properties` | Zwraca właściwości dla określonych etykiet | [krok properties](https://tinkerpop.apache.org/docs/3.3.2/reference/#_properties_step) |
| `range` | Filtruje do określonego zakresu wartości| [krok range](https://tinkerpop.apache.org/docs/3.3.2/reference/#range-step) |
| `repeat` | Powtarza krok określoną liczbę razy. Używany do zapętlenia | [krok repeat](https://tinkerpop.apache.org/docs/3.3.2/reference/#repeat-step) |
| `sample` | Służy do próbkowania wyników z przejścia | [krok sample](https://tinkerpop.apache.org/docs/3.3.2/reference/#sample-step) |
| `select` | Służy do projektowania wyników z przejścia |  [krok select](https://tinkerpop.apache.org/docs/3.3.2/reference/#select-step) |
| `store` | Używany do nieblokujących agregacji z przejścia | [krok store](https://tinkerpop.apache.org/docs/3.3.2/reference/#store-step) |
| `TextP.startingWith(string)` | Funkcja filtrowania ciągów. Ta funkcja jest używana jako predykat dla kroku, `has()` aby dopasować właściwość z początkiem danego ciągu | [Predykaty textp](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.endingWith(string)` |  Funkcja filtrowania ciągów. Ta funkcja jest używana jako predykat dla kroku, `has()` aby dopasować właściwość z zakończeniem danego ciągu | [Predykaty textp](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.containing(string)` | Funkcja filtrowania ciągów. Ta funkcja jest używana jako predykat dla kroku, `has()` aby dopasować właściwość z zawartością danego ciągu | [Predykaty textp](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notStartingWith(string)` | Funkcja filtrowania ciągów. Ta funkcja jest używana jako predykat `has()` dla kroku, aby dopasować właściwość, która nie zaczyna się od danego ciągu | [Predykaty textp](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notEndingWith(string)` | Funkcja filtrowania ciągów. Ta funkcja jest używana jako predykat `has()` dla kroku, aby dopasować właściwość, która nie kończy się na danym ciągu | [Predykaty textp](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notContaining(string)` | Funkcja filtrowania ciągów. Ta funkcja jest używana jako predykat dla kroku, `has()` aby dopasować właściwość, która nie zawiera danego ciągu | [Predykaty textp](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `tree` | Agreguje ścieżki z wierzchołka do drzewa | [krok tree](https://tinkerpop.apache.org/docs/3.3.2/reference/#tree-step) |
| `unfold` | Odwija iterator w ramach kroku| [krok unfold](https://tinkerpop.apache.org/docs/3.3.2/reference/#unfold-step) |
| `union` | Scalanie wyników z wielu przejść| [krok union](https://tinkerpop.apache.org/docs/3.3.2/reference/#union-step) |
| `V` | Zawiera kroki niezbędne do przejść między wierzchołkami i krawędziami `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV`, oraz `otherV` do | [kroki vertex](https://tinkerpop.apache.org/docs/3.3.2/reference/#vertex-steps) |
| `where` | Służy do filtrowania wyników z przejścia. Obsługuje operatory `eq`, `neq`, `lt`, `lte`, `gt`, `gte` i `between`  | [krok where](https://tinkerpop.apache.org/docs/3.3.2/reference/#where-step) |

Aparat zoptymalizowany pod kątem zapisu oferowany w usłudze Azure Cosmos DB obsługuje domyślnie automatyczne indeksowanie wszystkich właściwości w wierzchołkach i krawędziach. W związku z tym zapytania z filtrami, zapytania zakresu, sortowanie lub agregacje na dowolnej właściwości są przetwarzane z indeksu i skutecznie obsługiwane. Więcej informacji na temat działania indeksowania w usłudze Azure Cosmos DB znajduje się w dokumencie dotyczącym [indeksowania niezależnie od schematu](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

## <a name="next-steps"></a>Następne kroki
* Rozpocznij tworzenie aplikacji grafu [przy użyciu zestawów SDK firmy Microsoft](create-graph-dotnet.md) 
* Dowiedz się więcej na temat [obsługi grafów](graph-introduction.md) w usłudze Azure Cosmos DB
