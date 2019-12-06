---
title: Indeksowanie w usłudze Azure Cosmos DB
description: Dowiedz się, jak indeksowanie działa w Azure Cosmos DB, różne rodzaje indeksów, takie jak zakres, przestrzenny, obsługiwane indeksy złożone.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: thweiss
ms.openlocfilehash: 65186262095560d7ae54d32b218d1c01f1fb921d
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873628"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indeksowanie w Azure Cosmos DB — Omówienie

Azure Cosmos DB to baza danych niezależny od schematu, która pozwala na iterację aplikacji bez konieczności rozwiązywania problemów z zarządzaniem schematami i indeksami. Domyślnie Azure Cosmos DB automatycznie indeksuje każdą właściwość dla wszystkich elementów w [kontenerze](databases-containers-items.md#azure-cosmos-containers) bez konieczności definiowania schematu lub konfigurowania indeksów pomocniczych.

Celem tego artykułu jest wyjaśnienie, jak Azure Cosmos DB indeksów danych i jak używa indeksów w celu zwiększenia wydajności zapytań. Zaleca się przechodzenie przez tę sekcję przed rozpoczęciem dostosowywania [zasad indeksowania](index-policy.md).

## <a name="from-items-to-trees"></a>Z elementów do drzew

Za każdym razem, gdy element jest przechowywany w kontenerze, jego zawartość jest rzutowana jako dokument JSON, a następnie konwertowana na reprezentację drzewa. Oznacza to, że każda Właściwość tego elementu jest reprezentowana jako węzeł w drzewie. Węzeł pseudo root jest tworzony jako element nadrzędny dla wszystkich właściwości pierwszego poziomu elementu. Węzły liścia zawierają rzeczywiste wartości skalarne przenoszone przez element.

Rozważmy na przykład ten element:

```json
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

Powinna być reprezentowana przez następujące drzewo:

![Poprzedni element reprezentowany jako drzewo](./media/index-overview/item-as-tree.png)

Zwróć uwagę, jak tablice są kodowane w drzewie: każdy wpis w tablicy pobiera pośredni węzeł z etykietą z indeksem tego wpisu w tablicy (0, 1 itd.).

## <a name="from-trees-to-property-paths"></a>Z drzew do ścieżek właściwości

Powód, dla którego Azure Cosmos DB przekształcania elementów na drzewa, jest, ponieważ umożliwia odwoływanie się do właściwości przez ich ścieżki w ramach tych drzew. Aby uzyskać ścieżkę do właściwości, można przejść przez drzewo z węzła głównego do tej właściwości i połączyć etykiety każdego przechodzącego węzła.

Poniżej znajdują się ścieżki każdej właściwości z przykładowego elementu opisanego powyżej:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Po zapisaniu elementu Azure Cosmos DB efektywnie indeksuje ścieżkę każdej właściwości i odpowiadającą jej wartość.

## <a name="index-kinds"></a>Rodzaje indeksów

Azure Cosmos DB obecnie obsługuje trzy rodzaje indeksów.

### <a name="range-index"></a>Indeks zakresu

Indeks **zakresu** jest oparty na uporządkowanej strukturze podobnej do drzewa. Rodzaj indeksu zakresu jest używany dla:

- Zapytania o równość:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   Dopasowanie równości w elemencie tablicy
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1”)
    ```

- Zapytania zakresu:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (działa dla `>`, `<`, `>=`, `<=`, `!=`)

- Sprawdzanie obecności właściwości:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- Dopasowanie prefiksu ciągu (zawiera słowo kluczowe nie będzie korzystać z indeksu zakresu):

   ```sql
   SELECT * FROM c WHERE STARTSWITH(c.property, "value")
   ```

- `ORDER BY` zapytania:

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN` zapytania:

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

Indeksów zakresu można używać w przypadku wartości skalarnych (String lub Number).

### <a name="spatial-index"></a>Indeks przestrzenny

Indeksy **przestrzenne** umożliwiają wydajne zapytania dotyczące obiektów geoprzestrzennych, takich jak punkty, linie, wielokąty i MultiPolygon. Te zapytania używają ST_DISTANCE, ST_WITHIN ST_INTERSECTS słów kluczowych. Poniżej przedstawiono kilka przykładów, które używają rodzaju indeksu przestrzennego:

- Zapytania dotyczące odległości geograficznej:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Geoprzestrzenne w ramach zapytań:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

- Zapytania z przecięciem geograficznym:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

Indeksów przestrzennych można używać w poprawnie sformatowanych obiektach [GEOJSON](geospatial.md) . Punkty, LineStrings, wielokąty i wielowielokąty są obecnie obsługiwane.

### <a name="composite-indexes"></a>Indeksy złożone

Indeksy **złożone** zwiększają wydajność podczas wykonywania operacji na wielu polach. Typ indeksu złożonego jest używany dla:

- `ORDER BY` zapytania dotyczące wielu właściwości:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Wykonuje zapytania z filtrem i `ORDER BY`. Te zapytania mogą korzystać z indeksu złożonego, jeśli Właściwość Filter jest dodawana do klauzuli `ORDER BY`.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Wykonuje zapytania z filtrem dla dwóch lub więcej właściwości, w których co najmniej jedna właściwość jest filtrem równości

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

Tak długo, jak jeden predykat filtru używa dla danego rodzaju indeksu, aparat kwerend zostanie oceniony przed skanowaniem reszty. Na przykład jeśli masz zapytanie SQL, takie jak `SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* Powyższe zapytanie najpierw filtruje wpisy, w których firstName = "Andrew" przy użyciu indeksu. Następnie przekazuje wszystkie wpisy firstName = "Andrew" przy użyciu kolejnego potoku, aby obliczyć predykat zawierający filtr.

* Można przyspieszyć zapytania i uniknąć pełnego skanowania kontenera podczas korzystania z funkcji, które nie używają indeksu (np. CONTAINS) przez dodanie dodatkowych predykatów filtru, które używają indeksu. Kolejność klauzul filtru nie jest ważna. Aparat zapytań będzie określać, które predykaty są bardziej wybiórcze i odpowiednio uruchomić zapytanie.


## <a name="querying-with-indexes"></a>Wykonywanie zapytań przy użyciu indeksów

Ścieżki wyodrębnione podczas indeksowania danych ułatwiają wyszukiwanie w indeksie podczas przetwarzania zapytania. Dopasowując klauzulę `WHERE` zapytania z listą indeksowanych ścieżek, można szybko identyfikować elementy, które pasują do predykatu zapytania.

Rozważmy na przykład następujące zapytanie: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. Predykat zapytania (filtrowanie dla elementów, gdzie każda lokalizacja ma wartość "Francja", ponieważ jego kraj) będzie odpowiadał ścieżce wyróżnionej w kolorze czerwonym poniżej:

![Dopasowanie określonej ścieżki w drzewie](./media/index-overview/matching-path.png)

> [!NOTE]
> Klauzula `ORDER BY`, która porządkuje według pojedynczej właściwości *zawsze* wymaga indeksu zakresu i zakończy się niepowodzeniem, jeśli ścieżka, do której się odwołuje, nie ma takiej wartości. Podobnie `ORDER BY` zapytanie, które zamówienie według wielu właściwości *zawsze* wymaga indeksu złożonego.

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej na temat indeksowania w następujących artykułach:

- [Zasady indeksowania](index-policy.md)
- [Jak zarządzać zasadami indeksowania](how-to-manage-indexing-policy.md)
