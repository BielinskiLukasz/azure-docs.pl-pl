---
title: Modelowanie danych programu Graph dla Azure Cosmos DB API Gremlin
description: Dowiedz się, jak modelować bazę danych Graph przy użyciu interfejsu API Azure Cosmos DB Gremlin. W tym artykule opisano, kiedy należy używać bazy danych grafu i najlepszych rozwiązań do modelowania jednostek i relacji.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: lbosq
ms.openlocfilehash: dc9a5616aa2bb1f7e09045b9cfe4f4d7e9c69be2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78898326"
---
# <a name="graph-data-modeling-for-azure-cosmos-db-gremlin-api"></a>Modelowanie danych programu Graph dla Azure Cosmos DB API Gremlin

Poniższy dokument został zaprojektowany, aby zapewnić zalecenia dotyczące modelowania danych grafu. Ten krok jest istotny w celu zapewnienia skalowalności i wydajności systemu bazy danych programu Graph w miarę rozwoju danych. Wydajny model danych jest szczególnie ważny w przypadku wykresów o dużej skali.

## <a name="requirements"></a>Wymagania

Proces przedstawiony w tym przewodniku jest oparty na następujących założeniach:
 * Zidentyfikowano **jednostki** w miejscu problemu. Te jednostki mają być używane w sposób _niepodzielny_ dla każdego żądania. Innymi słowy, system bazy danych nie jest przeznaczony do pobierania danych pojedynczej jednostki w wielu żądaniach zapytań.
 * Istnieje zrozumienie **wymagań dotyczących odczytu i zapisu** dla systemu bazy danych. Wymagania te przeprowadzą optymalizację potrzebną dla modelu danych grafu.
 * Zasady [standardu grafu właściwości programu Apache Tinkerpop](https://tinkerpop.apache.org/docs/current/reference/#graph-computing) są dobrze zrozumiałe.

## <a name="when-do-i-need-a-graph-database"></a>Kiedy potrzebna jest baza danych grafu?

Rozwiązanie bazy danych grafu można optymalnie zastosować, jeśli jednostki i relacje w domenie danych mają jedną z następujących cech: 

* Jednostki są **wysoce połączone** poprzez relacje opisowe. Zaletą w tym scenariuszu jest fakt, że relacje są utrwalane w magazynie.
* Istnieją **relacje cykliczne** lub **jednostki odwołujące się do siebie**. Ten wzorzec jest często wyzwaniem podczas korzystania z baz danych relacyjnych lub dokumentów.
* Istnieją **dynamicznie rozwijane relacje** między jednostkami. Ten wzorzec jest szczególnie stosowany do danych hierarchicznych lub uporządkowanych według drzewa z wieloma poziomami.
* Między jednostkami istnieją **relacje wiele-do-wielu** .
* Istnieją **zarówno wymagania dotyczące zapisu, jak i odczytu dotyczące jednostek i relacji**. 

Jeśli powyższe kryteria są spełnione, prawdopodobnie podejście bazy danych grafu będzie zapewniało korzyści wynikające z **złożoności zapytań**, **skalowalności modelu danych**i **wydajności zapytań**.

Następnym krokiem jest określenie, czy wykres ma być używany do celów analitycznych czy transakcyjnych. Jeśli wykres jest przeznaczony do użycia w przypadku dużych obciążeń obliczeniowych i przetwarzania danych, warto zbadać [Cosmos DB łącznika Spark](https://docs.microsoft.com/azure/cosmos-db/spark-connector) i korzystać z [biblioteki GraphX](https://spark.apache.org/graphx/). 

## <a name="how-to-use-graph-objects"></a>Jak używać obiektów grafów

[Standardowy wykres właściwości Apache Tinkerpop](https://tinkerpop.apache.org/docs/current/reference/#graph-computing) definiuje dwa typy obiektów **wierzchołków** i **krawędzi**. 

Poniżej przedstawiono najlepsze rozwiązania dotyczące właściwości w obiektach grafu:

| Obiekt | Właściwość | Typ | Uwagi |
| --- | --- | --- |  --- |
| Wierzchołka | ID | String | Unikatowo wymuszane na partycję. Jeśli wartość nie zostanie podana podczas wstawiania, zostanie zapisany automatycznie wygenerowany identyfikator GUID. |
| Wierzchołka | label | String | Ta właściwość służy do definiowania typu jednostki reprezentowanej przez wierzchołek. Jeśli wartość nie zostanie podana, zostanie użyta wartość domyślna "wierzchołek". |
| Wierzchołka | properties | Ciąg, wartość logiczna, numeryczna | Lista oddzielnych właściwości przechowywanych jako pary klucz-wartość w każdym wierzchołku. |
| Wierzchołka | klucz partycji | Ciąg, wartość logiczna, numeryczna | Ta właściwość określa miejsce, w którym będą przechowywane wierzchołki i jej krawędzie wychodzące. Przeczytaj więcej na temat [partycjonowania grafów](graph-partitioning.md). |
| Brzeg | ID | String | Unikatowo wymuszane na partycję. Automatycznie generowana domyślnie. Krawędzie zazwyczaj nie muszą być jednoznacznie pobierane przez identyfikator. |
| Brzeg | label | String | Ta właściwość służy do definiowania typu relacji, które mają dwa wierzchołki. |
| Brzeg | properties | Ciąg, wartość logiczna, numeryczna | Lista oddzielnych właściwości przechowywanych jako pary klucz-wartość w każdej krawędzi. |

> [!NOTE]
> Krawędzie nie wymagają wartości klucza partycji, ponieważ jej wartość jest przypisywana automatycznie na podstawie wierzchołka źródłowego. Dowiedz się więcej z artykułu [partycjonowania grafów](graph-partitioning.md) .

## <a name="entity-and-relationship-modeling-guidelines"></a>Wytyczne dotyczące modelowania jednostek i relacji

Poniżej przedstawiono zestaw wytycznych do podejścia do modelowania danych Azure Cosmos DB dla bazy danych Gremlin API Graph. W tych wytycznych przyjęto założenie, że istnieje istniejąca definicja domeny danych i zapytania dla niej.

> [!NOTE]
> Kroki opisane poniżej są przedstawione jako zalecenia. Końcowy model powinien być oceniany i testowany przed rozważeniam jako gotowym do produkcji. Ponadto poniższe zalecenia dotyczą implementacji interfejsu API Gremlin Azure Cosmos DB. 

### <a name="modeling-vertices-and-properties"></a>Modelowanie wierzchołków i właściwości 

Pierwszym krokiem dla modelu danych wykresu jest zamapowanie każdej identyfikowanej jednostki na **obiekt wierzchołka**. Mapowanie jeden do jednego wszystkich jednostek na wierzchołki powinno być pierwszym krokiem i może ulec zmianie.

Jednym z typowych Pitfall jest mapowanie właściwości pojedynczej jednostki jako oddzielnych wierzchołków. Rozważmy poniższy przykład, w którym ta sama jednostka jest reprezentowana na dwa różne sposoby:

* **Właściwości oparte na wierzchołku**: w tym podejściu jednostka używa trzech oddzielnych wierzchołków i dwóch krawędzi do opisywania właściwości. Chociaż takie podejście może zmniejszyć nadmiarowość, zwiększa złożoność modelu. Zwiększenie złożoności modelu może skutkować dodaniem opóźnienia, złożonością zapytania i kosztem obliczeniowym. Ten model może również przedstawiać wyzwania na partycjonowanie.

![Model jednostki z wierzchołkami dla właściwości.](./media/graph-modeling/graph-modeling-1.png)

* **Wierzchołki osadzone właściwości**: to podejście wykorzystuje listę par klucz-wartość do reprezentowania wszystkich właściwości jednostki w wierzchołku. Ta metoda zapewnia ograniczoną złożoność modelu, która będzie prowadzić do uproszczenia zapytań i bardziej wydajnego przechodzenia.

![Model jednostki z wierzchołkami dla właściwości.](./media/graph-modeling/graph-modeling-2.png)

> [!NOTE]
> Powyższe przykłady przedstawiają uproszczony model grafu, aby pokazać tylko porównanie między dwoma sposobami dzielenia właściwości jednostki.

Wzorzec **wierzchołków właściwości osadzonych** zazwyczaj zapewnia bardziej wydajne i skalowalne podejście. Domyślne podejście do nowego modelu danych grafu powinno gravitate do tego wzorca.

Istnieją jednak scenariusze, w których odwołanie do właściwości może mieć zalety. Na przykład: Jeśli przywoływana właściwość jest często aktualizowana. Użycie oddzielnego wierzchołka do reprezentowania właściwości, która jest stale zmieniana, spowodowałoby zminimalizowanie liczby operacji zapisu wymaganych przez aktualizację.

### <a name="relationship-modeling-with-edge-directions"></a>Modelowanie relacji z kierunkami krawędzi

Po modelowaniu wierzchołków można dodać krawędzie, aby zauważyć relacje między nimi. Pierwszy aspekt, który należy ocenić, to **kierunek relacji**. 

Obiekty brzegowe mają domyślny kierunek przechodzenia przy użyciu funkcji `out()` or. `outE()` Użycie tego naturalnego kierunku skutkuje wydajną operacją, ponieważ wszystkie wierzchołki są przechowywane z wychodzącymi krawędziami. 

Jednak przechodzenie w odwrotnym kierunku krawędzi brzegowej przy użyciu `in()` funkcji, zawsze spowoduje powstanie zapytania między partycjami. Dowiedz się więcej o [partycjonowaniu grafów](graph-partitioning.md). Jeśli istnieje potrzeba ciągłego przechodzenia przy użyciu `in()` funkcji, zaleca się dodanie krawędzi w obu kierunkach.

Kierunek krawędzi można określić przy użyciu predykatów `.to()` lub `.from()` do kroku `.addE()` Gremlin. Lub przy użyciu [biblioteki wykonawczy Bulk dla interfejsu API Gremlin](bulk-executor-graph-dotnet.md).

> [!NOTE]
> Obiekty brzegowe mają domyślnie kierunek.

### <a name="relationship-labeling"></a>Etykieta relacji

Korzystanie z opisowych etykiet relacji może poprawić wydajność operacji rozpoznawania krawędzi. Ten wzorzec można zastosować w następujący sposób:
* Aby oznaczyć relację, użyj nieogólnych warunków.
* Skojarz etykietę wierzchołka źródłowego z etykietą wierzchołka docelowego z nazwą relacji.

![Przykłady etykietowania relacji.](./media/graph-modeling/graph-modeling-3.png)

Im bardziej szczegółowa etykieta, która będzie używana przez przechodzenie do filtrowania krawędzi, tym lepiej. Ta decyzja może mieć znaczny wpływ na koszt zapytań. Koszt zapytania można oszacować w dowolnym momencie [przy użyciu kroku executionProfile](graph-execution-profile.md).


## <a name="next-steps"></a>Następne kroki: 
* Zapoznaj się z listą obsługiwanych [kroków Gremlin](gremlin-support.md).
* Dowiedz się więcej na temat [partycjonowania bazy danych grafu](graph-partitioning.md) do obsługi grafów o dużej skali.
* Oceń zapytania Gremlin przy użyciu [kroku profilu wykonania](graph-execution-profile.md).
