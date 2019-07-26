---
title: Wybieranie odpowiedniego poziomu spójności dla aplikacji korzystającej z Azure Cosmos DB
description: Wybieranie odpowiedniego poziomu spójności dla aplikacji w Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 89c81e978c5f3dbbb8fac1ea5e75fc506612308f
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384903"
---
# <a name="choose-the-right-consistency-level"></a>Wybieranie właściwego poziomu spójności 

Rozproszone bazy danych opierają się na replikacji w celu zapewnienia wysokiej dostępności, małych opóźnień lub obu — zasadniczego kompromisu między spójnością odczytu a dostępnością, czasem oczekiwania i przepływności. Najbardziej komercyjnie dostępne bazy danych umożliwiają deweloperom wybór między dwoma ekstremalnymi modelami spójności  : silną spójnością i spójnością *ostateczną* . Azure Cosmos DB pozwala deweloperom wybierać spośród pięciu dobrze zdefiniowanych modeli spójności: silnej , *ograniczonej*nieodświeżoności, *sesji*, *spójnego prefiksu* i *ostateczności*. Każdy z tych modeli spójności jest dobrze zdefiniowany, intuicyjny i może być używany w określonych rzeczywistych scenariuszach. Każdy z pięciu modeli spójności zapewnia precyzyjne [wady dostępności i wydajności](consistency-levels-tradeoffs.md) . są one obsługiwane przez kompleksową umowy SLA. Poniższe proste zagadnienia ułatwią wybór w wielu typowych scenariuszach.

## <a name="sql-api-and-table-api"></a>Interfejs API SQL i interfejs API tabel

Jeśli aplikacja jest skompilowana przy użyciu interfejsu API SQL lub interfejs API tabel, należy wziąć pod uwagę następujące kwestie:

- W przypadku wielu rzeczywistych scenariuszy spójność sesji jest optymalna i jest to zalecana opcja. Aby uzyskać więcej informacji, zobacz, [jak zarządzać tokenem sesji dla aplikacji](how-to-manage-consistency.md#utilize-session-tokens).

- Jeśli aplikacja wymaga silnej spójności, zaleca się użycie ograniczonego poziomu spójności.

- Jeśli potrzebujesz bardziej rygorystycznych gwarancji spójności niż podane przez spójność sesji i opóźnienie jednej cyfry milisekund dla operacji zapisu, zalecamy użycie ograniczonego poziomu spójności.  

- Jeśli aplikacja wymaga spójności ostatecznej, zaleca się używanie spójnego poziomu spójności prefiksu.

- Jeśli potrzebujesz mniej rygorystycznych gwarancji spójności niż podane przez spójność sesji, zaleca się używanie spójnego poziomu spójności prefiksu.

- Jeśli potrzebujesz najwyższej dostępności i najmniejszego opóźnienia, użyj poziomu spójności ostatecznej.

- Jeśli potrzebujesz jeszcze wyższej trwałości danych bez utraty wydajności, możesz utworzyć niestandardowy poziom spójności w warstwie aplikacji. Aby uzyskać więcej informacji, zobacz [jak zaimplementować synchronizację niestandardową w aplikacjach](how-to-custom-synchronization.md).

## <a name="cassandra-mongodb-and-gremlin-apis"></a>Interfejsy API Cassandra, MongoDB i Gremlin

- Aby uzyskać szczegółowe informacje dotyczące mapowania między opcją "poziom spójności odczytu" oferowaną w ramach poziomów spójności usługi Apache Cassandra i Cosmos DB, zobacz [poziomy spójności i interfejsy api Cosmos DB](consistency-levels-across-apis.md#cassandra-mapping).

- Aby uzyskać szczegółowe informacje dotyczące mapowania między elementami MongoDB i Azure Cosmos DB poziomów spójności, zobacz [poziomy spójności i interfejsy api Cosmos DB](consistency-levels-across-apis.md#mongo-mapping).

## <a name="consistency-guarantees-in-practice"></a>Gwarancje spójności w programie

W tym przypadku często można uzyskać silniejsze gwarancje spójności. Gwarancje spójności operacji odczytu odnoszą się do aktualności i kolejności żądanego stanu bazy danych. Spójność odczytu jest związana z porządkowaniem i propagacją operacji zapisu/aktualizacji.  

* Gdy poziom spójności jest ustawiony na nieaktualność, Cosmos DB gwarantuje, że klienci zawsze odczytują wartość poprzedniego zapisu z opóźnieniem ograniczonym przez okno przestarzałe.

* Gdy poziom spójności jest ustawiony na **silnie**, okno nieodświeżone jest równoważne zeru, a klienci mają gwarancję odczytu najnowszej zatwierdzonej wartości operacji zapisu.

* W przypadku pozostałych trzech poziomów spójności okno przestarzałe jest w dużym stopniu zależne od obciążenia. Na przykład, jeśli nie ma żadnych operacji zapisu w bazie danych, operacja **odczytu z**możliwością, **sesja**lub **spójne prefiksy** mogą dać te same wyniki co operacja odczytu o silnym poziomie spójności.

Jeśli Twoje konto usługi Azure Cosmos jest skonfigurowane z poziomem spójności innym niż silna spójność, możesz sprawdzić prawdopodobieństwo, że klienci mogą uzyskać mocne i spójne odczyty dla obciążeń, patrząc na *probabilistically* Wartość przestarzałej (PBS). Ta Metryka jest dostępna w Azure Portal, aby dowiedzieć się więcej, zobacz [monitorowanie metryki probabilistically ograniczonej (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Probabilistyczne ograniczone nieaktualność pokazuje, jak to jest spójność ostateczna. Ta Metryka zawiera szczegółowe informacje o tym, jak często można uzyskać większą spójność niż poziom spójności skonfigurowany obecnie na koncie usługi Azure Cosmos. Innymi słowy, można zobaczyć prawdopodobieństwo (mierzone w milisekundach), aby uzyskać silnie spójne odczyty dla kombinacji regionów zapisu i odczytu.

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj więcej na temat poziomów spójności w następujących artykułach:

* [Mapowanie poziomu spójności w interfejsach API Cosmos DB](consistency-levels-across-apis.md)
* [Dostępność i wydajność kompromisy dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
* [Jak zarządzać tokenem sesji dla aplikacji](how-to-manage-consistency.md#utilize-session-tokens)
* [Monitorowanie metryki probabilistically ograniczonej (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
