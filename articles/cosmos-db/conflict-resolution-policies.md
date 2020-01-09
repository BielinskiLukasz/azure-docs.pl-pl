---
title: Typy rozwiązywania konfliktów i zasady rozwiązywania w Azure Cosmos DB
description: W tym artykule opisano kategorie konfliktów i zasady rozwiązywania konfliktów w Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: a8ee72f46e1789088e779c10a0824262469ffde8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441986"
---
# <a name="conflict-types-and-resolution-policies"></a>Typy konfliktów i zasady ich rozwiązywania

Konflikty i zasady rozwiązywania konfliktów są stosowane, jeśli konto Azure Cosmos DB zostało skonfigurowane z wieloma regionami zapisu.

W przypadku kont usługi Azure Cosmos skonfigurowanych z wieloma regionami zapisu mogą wystąpić konflikty aktualizacji, gdy moduły zapisujące jednocześnie aktualizują ten sam element w wielu regionach. Konflikty aktualizacji mogą być następujące trzy typy:

* **Konflikty wstawiania**: te konflikty mogą wystąpić, gdy aplikacja jednocześnie wstawia dwa lub więcej elementów z tym samym unikatowym indeksem w co najmniej dwóch regionach. Na przykład ten konflikt może wystąpić z właściwością ID.

* **Zastąp konflikty**: te konflikty mogą wystąpić, gdy aplikacja aktualizuje ten sam element jednocześnie w co najmniej dwóch regionach.

* **Konflikty usuwania**: te konflikty mogą wystąpić, gdy aplikacja jednocześnie usuwa element w jednym regionie i aktualizuje go w innym regionie.

## <a name="conflict-resolution-policies"></a>Zasady rozwiązywania konfliktów

Azure Cosmos DB oferuje elastyczny mechanizm oparty na zasadach do rozwiązywania konfliktów zapisu. Można wybrać jedną z dwóch zasad rozwiązywania konfliktów w kontenerze usługi Azure Cosmos:

* **Ostatni zapis WINS (LWW)** : Ta zasada rozwiązywania domyślnie używa właściwości sygnatury czasowej zdefiniowanej przez system. Jest on oparty na protokole zegara czasu synchronizacji. W przypadku korzystania z interfejsu API SQL można określić dowolną inną niestandardową Właściwość numeryczną (np. własne pojęcie sygnatury czasowej), która ma być używana do rozwiązywania konfliktów. Niestandardowa właściwość numeryczna jest również nazywana *ścieżką rozwiązywania konfliktów*. 

  Jeśli co najmniej dwa elementy powodują konflikt podczas operacji wstawiania lub zamiany, element o najwyższej wartości ścieżki rozwiązywania konfliktów zostanie zwycięzcą. System określa zwycięzcę, jeśli wiele elementów ma taką samą wartość liczbową dla ścieżki rozwiązywania konfliktów. Wszystkie regiony są gwarantowane, aby przeprowadzić zbieżność z pojedynczym zwycięzcą i zakończyć z tą samą wersją zatwierdzonego elementu. W przypadku konfliktów usuwania w usuniętej wersji zawsze jest używana usługa WINS w przypadku konfliktów INSERT lub Replace. Ten wynik występuje niezależnie od wartości ścieżki rozwiązywania konfliktów.

  > [!NOTE]
  > Ostatni zapis w usłudze WINS to domyślne zasady rozwiązywania konfliktów i używa `_ts` sygnatur czasowych dla następujących interfejsów API: SQL, MongoDB, Cassandra, Gremlin i Table. Niestandardowa właściwość numeryczna jest dostępna tylko dla interfejsu API SQL.

  Aby dowiedzieć się więcej, zobacz [przykłady, które używają zasad rozwiązywania konfliktów LWW](how-to-manage-conflicts.md).

* **Niestandardowo**: te zasady rozwiązywania są przeznaczone dla semantyki zdefiniowanej przez aplikację w celu uzgodnienia konfliktów. Po ustawieniu tych zasad w kontenerze usługi Azure Cosmos należy również zarejestrować *procedurę składowaną scalania*. Ta procedura jest wywoływana automatycznie w przypadku wykrycia konfliktów w ramach transakcji bazy danych na serwerze. System zawiera dokładnie raz gwarantuje do wykonania procedury scalania w ramach protokołu zobowiązania.  

  W przypadku skonfigurowania kontenera przy użyciu opcji rozwiązywania niestandardowego i zarejestrowanie procedury scalania w kontenerze lub procedury scalania zgłasza wyjątek w czasie wykonywania, konflikty są zapisywane w *źródle konfliktów*. Aplikacja musi ręcznie rozwiązać konflikty w strumieniowym źródle konfliktów. Aby dowiedzieć się więcej, zobacz [przykłady użycia niestandardowych zasad rozpoznawania i sposób używania źródła konfliktów](how-to-manage-conflicts.md).

  > [!NOTE]
  > Niestandardowe zasady rozwiązywania konfliktów są dostępne tylko dla kont interfejsu API SQL.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak skonfigurować zasady rozwiązywania konfliktów:

* [Jak skonfigurować wiele wzorców w aplikacjach](how-to-multi-master.md)
* [Jak zarządzać zasadami rozwiązywania konfliktów](how-to-manage-conflicts.md)
* [Jak czytać ze źródła konfliktów](how-to-manage-conflicts.md#read-from-conflict-feed)
