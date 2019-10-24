---
title: Interfejs API Azure Cosmos DB dla MongoDB (wersja 3,6) obsługiwane funkcje i składnia
description: Dowiedz się więcej o obsługiwanych funkcjach i składni interfejsu API Azure Cosmos DB (wersja 3,6).
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 10/16/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 12311fa476d069d2c866fac82ed2bac25ce88ef4
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72758001"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-36-version-supported-features-and-syntax"></a>Azure Cosmos DB API for MongoDB (wersja 3,6): obsługiwane funkcje i składnia

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Możesz komunikować się z interfejsem API usługi Azure Cosmos DB dla bazy danych MongoDB, używając dowolnych [sterowników](https://docs.mongodb.org/ecosystem/drivers) klienta bazy danych MongoDB typu „open source”. Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB umożliwia korzystanie z istniejących sterowników klienta dzięki przestrzeganiu [protokołu przewodowego](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) MongoDB.

Używając interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB, można korzystać z dobrze znanych zalet bazy danych MongoDB oraz wszystkich funkcji na poziomie korporacyjnym dostarczanych przez usługę Cosmos DB: [globalnej dystrybucji](distribute-data-globally.md), [automatycznego fragmentowania](partition-data.md), gwarancji dostępności i opóźnień, automatycznego indeksowania każdego pola, szyfrowania danych magazynowanych, tworzenia kopii zapasowych itd.

## <a name="protocol-support"></a>Obsługa protokołu

Interfejs API Azure Cosmos DB dla MongoDB jest domyślnie zgodny z serwerem MongoDB w wersji **3,6** dla nowych kont. Poniżej wymieniono obsługiwane operacje wraz z ewentualnymi ograniczeniami lub wyjątkami. Dowolny sterownik klienta działający zgodnie z tymi protokołami umożliwia połączenie z interfejsem API usługi Azure Cosmos DB dla bazy danych MongoDB.

## <a name="query-language-support"></a>Obsługa języka zapytań

Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB zapewnia niemal pełną obsługę konstrukcji języka zapytań bazy danych MongoDB. Poniżej można znaleźć szczegółową listę obecnie obsługiwanych operacji, operatorów, etapów, poleceń i opcji.

## <a name="database-commands"></a>Polecenia bazy danych

Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB obsługuje następujące polecenia bazy danych:

### <a name="query-and-write-operation-commands"></a>Polecenia operacji zapytań i zapisu

- delete
- find
- findAndModify
- getLastError
- getMore
- insert
- update

### <a name="authentication-commands"></a>Polecenia uwierzytelniania

- wyloguj
- authenticate
- getnonce

### <a name="administration-commands"></a>Polecenia administracyjne

- dropDatabase
- listDatabases
- listCollections
- drop
- create
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- connectionStatus
- reIndex
- killCursors

### <a name="diagnostics-commands"></a>Polecenia diagnostyki

- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Potok agregacji</a>

### <a name="aggregation-commands"></a>Polecenia agregacji

- aggregate
- count
- distinct

### <a name="aggregation-stages"></a>Etapy agregacji

- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count
- $addFields
- $redact
- $replaceRoot

### <a name="aggregation-expressions"></a>Wyrażenia agregacji

#### <a name="boolean-expressions"></a>Wyrażenia logiczne

- $and
- $or
- $not

#### <a name="set-expressions"></a>Stałe wyrażenia

- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>Wyrażenia porównania

- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>Wyrażenia arytmetyczne

- $abs
- $add
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiply
- $pow
- $sqrt
- $subtract
- $trunc

#### <a name="string-expressions"></a>Wyrażenia ciągu

- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>Wyrażenia tablicy

- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $in

#### <a name="date-expressions"></a>Wyrażenia daty

- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $second
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>Wyrażenia warunkowe

- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>Akumulatory agregacji

Cosmos DB obsługuje wszystkie akumulatory MongoDB v 3.6, z wyjątkiem:

- $stdDevPop
- $stdDevSamp

## <a name="operators"></a>Operatory

Poniżej znajdują się obsługiwane operatory razem z odpowiednimi przykładami ich użycia. Warto zapoznać się z przykładowym dokumentem użytym w zapytaniach poniżej:

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

Operator | Przykład |
--- | --- |
$eq | `{ "Volcano Name": { $eq: "Rainier" } }` |  | -
$gt | `{ "Elevation": { $gt: 4000 } }` |  | -
$gte | `{ "Elevation": { $gte: 4392 } }` |  | -
$lt | `{ "Elevation": { $lt: 5000 } }` |  | -
$lte | `{ "Elevation": { $lte: 5000 } }` | | -
$ne | `{ "Elevation": { $ne: 1 } }` |  | -
$in | `{ "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } }` |  | -
$nin | `{ "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } }` | | -
$or | `{ $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] }` |  | -
$and | `{ $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] }` |  | -
$not | `{ "Elevation": { $not: { $gt: 5000 } } }`|  | -
$nor | `{ $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] }` |  | -
$exists | `{ "Status": { $exists: true } }`|  | -
$type | `{ "Status": { $type: "string" } }`|  | -
$mod | `{ "Elevation": { $mod: [ 4, 0 ] } }` |  | -
$regex | `{ "Volcano Name": { $regex: "^Rain"} }`|  | -

### <a name="notes"></a>Uwagi

W zapytaniach $regex wyrażenia zakotwiczone z lewej strony umożliwiają wyszukiwanie indeksu. Jednak użycie modyfikatora „i” (wielkość liter nie ma znaczenia) oraz modyfikatora „m” (wiele wierszy) powoduje skanowanie kolekcji we wszystkich wyrażeniach.
Jeśli istnieje potrzeba dołączenia „$” lub „|”, najlepiej utworzyć dwa lub więcej zapytań regex.
Jeśli na przykład oryginalne zapytanie jest następujące: ```find({x:{$regex: /^abc$/})``` należy je zmodyfikować w następujący sposób: ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
Pierwsza część użyje indeksu, aby ograniczyć wyszukiwanie do dokumentów, które rozpoczynają się od ^abc, a druga część będzie dokładnie dopasowywana do wpisów.
Operator kreski „|” działa jako funkcja „or” — zapytanie ```find({x:{$regex: /^abc|^def/})``` pasuje do dokumentów, w których pole „x” ma wartości zaczynające się od „abc” lub „def”. Aby korzystać z indeksu, zaleca się podzielenie zapytania na dwa różne zapytania połączone operatorem $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="update-operators"></a>Operatory aktualizacji

#### <a name="field-update-operators"></a>Operatory do aktualizacji pól

- $inc
- $mul
- $rename
- $setOnInsert
- $set
- $unset
- $min
- $max
- $currentDate

#### <a name="array-update-operators"></a>Operatory do aktualizacji tablic

- $addToSet
- $pop
- $pullAll
- $pull
- $pushAll
- $push
- $each
- $slice
- $sort
- $position

#### <a name="bitwise-update-operator"></a>Operator do aktualizacji Bitwise

- $bit

### <a name="geospatial-operators"></a>Operatory danych geoprzestrzennych

Operator | Przykład | |
--- | --- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Tak |
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Tak |
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Tak |
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Tak |
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Tak |
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | Tak |
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Tak |
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | Tak |
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Tak |
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | Tak |
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Tak |

## <a name="sort-operations"></a>Operacje sortowania

W przypadku używania operacji `findOneAndUpdate` obsługiwane są operacje sortowania względem pojedynczego pola, ale nie względem wielu pól.

## <a name="additional-operators"></a>Dodatkowe operatory

Operator | Przykład | Uwagi
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` |
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |  
$size | ```{ "Location.coordinates": { $size: 2 } }``` |
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` |
$text |  | Nieobsługiwane. Zamiast tego użyj operatora $regex.

## <a name="unsupported-operators"></a>Nieobsługiwane operatory

Operatory ```$where``` i ```$eval``` nie są obsługiwane w usłudze Azure Cosmos DB.

### <a name="methods"></a>Metody

Obsługiwane są następujące metody:

#### <a name="cursor-methods"></a>Metody kursora

Metoda | Przykład | Uwagi
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | Dokumenty bez klucza sortowania nie zostaną zwrócone

## <a name="unique-indexes"></a>Indeksy unikatowe

Indeksy unikatowe zapewniają, że wartości w danym polu nie dublują się we wszystkich dokumentach w kolekcji; również klucz domyślny „_id” jest kluczem unikatowym. W usłudze Cosmos DB można tworzyć niestandardowe indeksy, używając polecenia createIndex, w którym można stosować ograniczenie „unique”.

## <a name="time-to-live-ttl"></a>Czas wygaśnięcia (TTL)

Usługa Cosmos DB obsługuje czas wygaśnięcia (TTL) na podstawie znacznika czasu dokumentu. Czas wygaśnięcia można włączyć dla kolekcji, przechodząc do witryny [Azure Portal](https://portal.azure.com).

## <a name="user-and-role-management"></a>Zarządzanie użytkownikami i rolami

Usługa Cosmos DB nie obsługuje jeszcze użytkowników i ról. Usługa Cosmos DB obsługuje jednak kontrolę dostępu opartą na rolach (RBAC) oraz hasła/klucze do odczytu-zapisu i tylko do odczytu, które można uzyskać w witrynie [Azure Portal](https://portal.azure.com) (na stronie Parametry połączenia).

## <a name="replication"></a>Replikacja

Usługa Cosmos DB obsługuje automatyczną, natywną replikację na najniższych warstwach. Ta logika została rozszerzona, aby osiągnąć małe opóźnienia oraz replikację globalną. Usługa Cosmos DB nie obsługuje poleceń dotyczących replikacji ręcznej.

## <a name="write-concern"></a>Ustawienie Write Concern

Niektóre aplikacje polegają na ustawieniu [Write Concern](https://docs.mongodb.com/manual/reference/write-concern/), które określa liczbę odpowiedzi wymaganych podczas operacji zapisu. Ze względu na sposób obsługi replikacji w tle w usłudze Cosmos DB wszystkie operacje zapisu mają domyślnie automatycznie ustawioną opcję Quorum (Kworum). Ustawienia Write Concern określone w kodzie klienta są ignorowane. Aby dowiedzieć się więcej, zobacz [Maksymalizowanie dostępności i wydajności za pomocą poziomów spójności](consistency-levels.md).

## <a name="sharding"></a>Dzielenie na fragmenty

Usługa Azure Cosmos DB obsługuje automatyczne dzielenie na fragmenty po stronie serwera. Zarządza on fragmentu tworzeniem, umieszczaniem i zrównoważeniem automatycznie. Azure Cosmos DB nie obsługuje ręcznych poleceń fragmentowania, co oznacza, że nie trzeba Wywoływanie poleceń takich jak addShard, balancerStart, moveChunk itp. Wystarczy określić klucz fragmentu podczas tworzenia kontenerów lub wykonywania zapytań dotyczących danych.

## <a name="sessions"></a>Sesje

Azure Cosmos DB nie obsługuje jeszcze poleceń sesji po stronie serwera.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Dowiedz się, jak [korzystać z programu Robo 3T](mongodb-robomongo.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.

<sup>Uwaga: w tym artykule opisano funkcję Azure Cosmos DB, która zapewnia zgodność protokołu telekomunikacyjnych z bazami danych MongoDB. Firma Microsoft nie uruchamia MongoDB baz danych w celu zapewnienia tej usługi. Azure Cosmos DB nie jest powiązany z MongoDB, Inc.</sup>
