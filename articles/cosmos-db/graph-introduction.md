---
title: Wprowadzenie do interfejsu API języka Gremlin w usłudze Azure Cosmos DB
description: Dowiedz się, jak można używać usługi Azure Cosmos DB do przechowywania dużych grafów, wykonywania na nich zapytań i przechodzenia ich z krótkim czasem oczekiwania za pomocą języka zapytań dotyczących grafów w środowisku Gremlin witryny Apache TinkerPop.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 07/18/2019
ms.author: lbosq
ms.openlocfilehash: 9151b54d7fa0b64a465aa8384cb4bfdb8e72c482
ms.sourcegitcommit: 801e9118fae92f8eef8d846da009dddbd217a187
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/27/2019
ms.locfileid: "75500009"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Wprowadzenie do usługi Azure Cosmos DB: interfejs API języka Gremlin

[Azure Cosmos DB](introduction.md) to globalnie dystrybuowana, wielomodelowa usługa bazy danych firmy Microsoft dla aplikacji o znaczeniu strategicznym. Jest to wielomodelowa baza danych, która obsługuje modele danych w dokumentach, klucz-wartość, Graf i rodzina kolumn. Azure Cosmos DB interfejs API Gremlin jest używany do przechowywania danych grafu i korzystania z nich w w pełni zarządzanej usłudze bazy danych zaprojektowanej dla dowolnej skali.  

![Architektura grafów w usłudze Azure Cosmos DB](./media/graph-introduction/cosmosdb-graph-architecture.png)

Ten artykuł zawiera omówienie interfejsu API języka Gremlin usługi Azure Cosmos DB i przedstawia jak można go użyć do przechowywania dużych grafów zawierających miliardy wierzchołków i krawędzi. Można wysyłać zapytania o wykresy z opóźnieniem milisekund i łatwo rozwijać strukturę wykresu. Interfejs API Gremlin Azure Cosmos DB jest oparty na standardzie usługi [Apache TinkerPop](https://tinkerpop.apache.org) Graph i używa języka zapytań Gremlin. 

Interfejs API Gremlin Azure Cosmos DB łączy możliwości algorytmów bazy danych grafów z wysoce skalowalną, zarządzaną infrastrukturą, zapewniając unikatowe, elastyczne rozwiązanie do najbardziej typowych problemów z danymi, które są związane z brakiem elastyczności i relacyjnych metod. 

## <a name="features-of-azure-cosmos-db-graph-database"></a>Funkcje bazy danych grafów usługi Azure Cosmos DB
 
Usługa Azure Cosmos DB jest w pełni zarządzaną bazą danych grafów, która oferuje globalną dystrybucję, elastyczne skalowanie magazynu i przepływności, automatyczne indeksowanie i wykonywanie zapytań, dostosowywalne poziomy spójności oraz obsługę standardu TinkerPop. 

Poniżej przedstawiono zróżnicowane funkcje, które Azure Cosmos DB Gremlin interfejs API:

* **Elastycznie skalowalna przepływność i magazyn**

  Skalowanie grafów w świecie rzeczywistym wymaga skalowania przekraczającego pojemność pojedynczego serwera. Azure Cosmos DB obsługuje skalowalne w poziomie bazy danych grafów, które mogą mieć praktycznie nieograniczony rozmiar w zakresie magazynu i alokowanej przepływności. Gdy skala bazy danych grafu zostanie powiększona, dane będą automatycznie dystrybuowane przy użyciu [partycjonowania grafu](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning).

* **Replikacja wieloregionowa**

  Azure Cosmos DB mogą automatycznie replikować dane grafu do dowolnego regionu platformy Azure na całym świecie. Replikacja globalna upraszcza opracowywanie aplikacji, które wymagają globalnego dostępu do danych. Oprócz minimalizowania opóźnień odczytu i zapisu w dowolnym miejscu na świecie, Azure Cosmos DB zapewnia automatyczne regionalne mechanizmy trybu failover, które mogą zapewnić ciągłość aplikacji w rzadkich przypadkach przerwania działania usługi w regionie. 

* **Szybkie zapytania i przechodzenie przy użyciu najczęściej przyjętego standardowego zapytania grafowego**

  Przechowywanie heterogenicznych wierzchołków i krawędzi oraz wykonywanie zapytań przy użyciu znanej składni Gremlin. Gremlin to bezwzględny, funkcjonalny język zapytań, który oferuje bogaty interfejs do implementowania typowych algorytmów grafu. 
  
  Azure Cosmos DB włącza zaawansowane zapytania w czasie rzeczywistym i przechodzenia bez konieczności określania wskazówek do schematu, indeksów pomocniczych lub widoków. Więcej informacji znajduje się w temacie [Query graphs by using Gremlin](gremlin-support.md) (Wykonywanie zapytań dla grafów przy użyciu środowiska Gremlin).

* **W pełni zarządzana baza danych grafu**

  Usługa Azure Cosmos DB eliminuje konieczność zarządzania zasobami maszyn i baz danych. Większość istniejących platform baz danych grafów jest związanych z ograniczeniami ich infrastruktury i często wymagają wysokiego stopnia konserwacji w celu zapewnienia jego działania. 
  
  W pełni zarządzana usługa Cosmos DB eliminuje konieczność zarządzania maszynami wirtualnymi, aktualizacji oprogramowania środowiska uruchomieniowego, zarządzania fragmentowaniaą lub replikacją lub rozwiązywania złożonych uaktualnień w warstwie danych. Dla każdego grafu jest automatycznie tworzona kopia zapasowa w celu ochrony przed regionalnymi awariami. Te gwarancje umożliwiają deweloperom skoncentrowanie się na dostarczaniu wartości aplikacji zamiast działania i zarządzania bazami danych grafów. 

* **Automatyczne indeksowanie**

  Domyślnie usługa Azure Cosmos DB automatycznie indeksuje wszystkie właściwości w węzłach oraz krawędziach grafu i nie oczekuje ani nie wymaga żadnego schematu ani tworzenia indeksów pomocniczych. Dowiedz się więcej [na temat indeksowania w Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview). 

* **Zgodność z usługą Apache TinkerPop**

  Azure Cosmos DB obsługuje [Standard Apache TinkerPop dla technologii open source](https://tinkerpop.apache.org/). Standard Tinkerpop ma szeroką integralność aplikacji i bibliotek, które można łatwo zintegrować z interfejsem API Gremlin Azure Cosmos DB. 

* **Możliwość dostosowania poziomy spójności**

  Azure Cosmos DB zapewnia pięć dobrze zdefiniowanych poziomów spójności w celu osiągnięcia odpowiedniej kompromisu między spójnością i wydajnością aplikacji. Dla zapytań i operacji odczytu usługa Azure Cosmos DB oferuje pięć różnych poziomów spójności: „silna”, „powiązana nieaktualność”, „sesja”, „spójny prefiks” i „ostateczna”. Te szczegółowe, dokładnie zdefiniowane poziomy spójności umożliwiają ustalanie optymalnych kompromisów między spójnością, dostępnością i opóźnieniem. Więcej informacji znajduje się w temacie [Tunable data consistency levels in Azure Cosmos DB](consistency-levels.md) (Dostosowywalne poziomy spójności danych w usłudze Azure Cosmos DB).

## <a name="scenarios-that-can-use-gremlin-api"></a>Scenariusze, w których można używać interfejsu API języka Gremlin
Poniżej przedstawiono kilka scenariuszy, w których Azure Cosmos DB mogą być przydatne wykresy obsługiwane przez program Graph:

* **Sieci społecznościowe/klient 365**

  Łącząc dane dotyczące klientów i ich interakcji z innymi osobami, można zaprojektować spersonalizowaną obsługę, przewidzieć zachowanie klientów lub umożliwić nawiązanie kontaktu z innymi osobami o podobnych zainteresowaniach. Usługa Azure Cosmos DB może służyć do zarządzania sieciami społecznościowymi oraz śledzenia preferencji i danych klientów.

* **Aparaty rekomendacji**

  Ten scenariusz jest powszechnie stosowany w branży sprzedaży detalicznej. Przez łączenie informacji na temat produktów, użytkowników oraz interakcji użytkownika, np. zakupów, przeglądania witryn internetowych lub oceny produktów, można tworzyć niestandardowe rekomendacje. Obsługa małych opóźnień, elastycznej skali i natywnych grafów dla Azure Cosmos DB jest idealna dla tych scenariuszy.

* **Lokalizacji**

  Wiele aplikacji stosowanych w telekomunikacji, logistyce i planowaniu podróży musi znaleźć lokalizację będącą przedmiotem zainteresowania na danym obszarze lub zlokalizować najkrótszą/optymalną trasę między dwoma lokalizacjami. Usługa Azure Cosmos DB stanowi naturalne rozwiązanie tych problemów.

* **Internet rzeczy**

  Gdy sieć i połączenia między urządzeniami IoT zostają przedstawione w formie grafu, można lepiej rozumieć stan urządzeń i zasobów. Można także dowiedzieć się, jak zmiany w jednej części sieci mogą teoretycznie wpłynąć na inną część.

## <a name="introduction-to-graph-databases"></a>Wprowadzenie do baz danych programu Graph
W świecie rzeczywistym dane w sposób naturalny łączą się ze sobą. Tradycyjne Modelowanie danych koncentruje się na definiowaniu jednostek osobno i obliczaniu ich relacji w czasie wykonywania. Chociaż ten model ma swoje zalety, wysoce połączone dane mogą być trudne do zarządzania w ramach ograniczeń.  

Podejście do bazy danych programu Graph opiera się na utrwalaniu relacji w warstwie magazynu, co prowadzi do wysoce wydajnych operacji pobierania grafów. Interfejs API Gremlin Azure Cosmos DB obsługuje [model grafu właściwości](https://tinkerpop.apache.org/docs/current/reference/#intro).

### <a name="property-graph-objects"></a>Obiekty grafu właściwości

[Wykres](http://mathworld.wolfram.com/Graph.html) właściwości to struktura, która składa się z [wierzchołków](http://mathworld.wolfram.com/GraphVertex.html) i [krawędzi](http://mathworld.wolfram.com/GraphEdge.html). Oba obiekty mogą mieć dowolną liczbę par klucz-wartość jako właściwości. 

* **Wierzchołki** — wierzchołki oznaczają dyskretne jednostki, takie jak osoba, miejsce lub zdarzenie.

* **Krawędzie** — krawędzie określają relacje między wierzchołkami. Na przykład dana osoba może znać inną osobę, brać udział w wydarzeniu lub była niedawno w danej lokalizacji. 

* **Właściwości** — właściwości zawierają informacje na temat wierzchołków i krawędzi. W wierzchołkach lub krawędziach może być dowolna liczba właściwości, które mogą być używane do opisywania i filtrowania obiektów w zapytaniu. Przykładowe właściwości zawierają wierzchołka o nazwie i wieku albo krawędzi, która może mieć sygnaturę czasową i/lub wagę. 

Bazy danych programu Graph są często uwzględniane w kategorii NoSQL lub nierelacyjnej bazy danych, ponieważ nie ma żadnej zależności od schematu lub ograniczonego modelu danych. Brak schematu pozwala na modelowanie i przechowywanie połączonych struktur naturalnie i wydajnie. 

### <a name="gremlin-by-example"></a>Język Gremlin w przykładach
Przykładowy graf pomoże dowiedzieć się, jak można wyrazić zapytania w języku Gremlin. Na poniższej ilustracji przedstawiono w formie grafu aplikację biznesową, która zarządza danymi o użytkownikach, zainteresowaniach i urządzeniach.  

![Przykładowa baza danych przedstawiająca osoby, urządzenia i zainteresowania](./media/gremlin-support/sample-graph.png) 

Ten wykres ma następujące typy *wierzchołków* (o nazwie "label" w Gremlin):

- **Ludzie**: wykres ma trzy osoby, Robin, Thomas i Ben
- **Zainteresowania**: ich zainteresowania, w tym przykładzie, gra piłkarskia
- **Urządzenia**: urządzenia używane przez osoby
- **Systemy operacyjne**: systemy operacyjne, na których działają urządzenia

Reprezentujemy relacje między tymi jednostkami za pośrednictwem następujących typów *krawędzi* /etykiet:

- **Wie**: na przykład "" Thomas wie Robin "
- **Zainteresowani**: aby reprezentować interesy osób w naszym grafie, na przykład "Ben zainteresuje Cię"
- **RunsOS**: Laptop działa w systemie operacyjnym Windows
- **Używa**: do reprezentowania urządzenia, którego używa osoba. Na przykład Robin używa telefonu firmy Motorola o numerze seryjnym 77

Możemy uruchomić pewne operacje zgodnie z tym grafem przy użyciu [Konsoli Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#gremlin-console). Można również wykonywać te operacje przy użyciu sterowników Gremlin na wybranej platformie (Java, Node.js, Python lub .NET).  Zanim zajmiemy się tym, co jest obsługiwane w usłudze Azure Cosmos DB, przyjrzymy się kilku przykładom, aby zapoznać się ze składnią.

Najpierw zajmijmy się CRUD. Następująca instrukcja języka Gremlin wstawia do grafu wierzchołek „Thomas”:

```java
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Następnie poniższa instrukcja języka Gremlin wstawia krawędź „knows” między wierzchołkami „Thomas” i „Robin”.

```java
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

Poniższe zapytanie zwraca wierzchołki „person” w porządku malejącym według ich imion:
```java
:> g.V().hasLabel('person').order().by('firstName', decr)
```

Wyjątkowość grafów docenia się, gdy trzeba odpowiedzieć na takie pytania jak: „Jakich systemów operacyjnych używają znajomi Thomasa?” Możesz uruchomić to przechodzenie Gremlin, aby uzyskać te informacje z grafu:

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat obsługi grafów w usłudze Azure Cosmos DB, zobacz:

* Rozpocznij pracę od [samouczka dotyczącego grafów usługi Azure Cosmos DB](create-graph-dotnet.md).
* Uzyskaj informacje na temat [wykonywania zapytań dla grafów w usłudze Azure Cosmos DB przy użyciu środowiska Gremlin](gremlin-support.md).
