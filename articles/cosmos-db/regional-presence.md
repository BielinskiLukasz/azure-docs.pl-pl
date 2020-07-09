---
title: Obecność regionalna z Azure Cosmos DB
description: W tym artykule opisano regionalne obecność Azure Cosmos DB i różnych środowisk chmury.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.custom: seodec18
ms.openlocfilehash: 78f02c07932c6a058ba4fa27289640b7efd56169
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85113674"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Obecność regionalna z Azure Cosmos DB

Azure Cosmos DB to fundamentowa usługa na platformie Azure, a domyślnie zawsze jest dostępna we wszystkich regionach, w których platforma Azure jest dostępna. Obecnie platforma Azure jest dostępna w [54 regionach](https://azure.microsoft.com/global-infrastructure/regions/) na całym świecie. 

:::image type="content" source="./media/regional-presence/regional-presence.png" alt-text="Regiony, w których Azure Cosmos DB jest dostępna" lightbox="./media/regional-presence/regional-presence.png" border="false":::

Cosmos DB jest dostępny we wszystkich pięciu różnych środowiskach chmury platformy Azure dostępnych dla klientów:

* Chmura **publiczna platformy Azure** , która jest dostępna globalnie.

* **Usługa Azure Chiny 21Vianet** jest dostępna za pomocą unikatowego powiązania między firmą Microsoft i 21Vianet — jednym z największych dostawców Internetu w danym kraju w Chinach.

* **Platforma Azure (Niemcy** ) udostępnia usługi w ramach modelu zarządca danych, co zapewnia, że dane klienta pozostają w Niemczech pod kontrolą międzyplatformowych usług typu T-Systems.

* **Azure Government** jest dostępna w czterech regionach Stany Zjednoczone do instytucji RZĄDowych Stanów Zjednoczonych i ich partnerów. 

* **Azure Government dla Departamentu Obrony (DoD)** jest dostępny w dwóch regionach w Stany Zjednoczone do Departamentu Obrony Stanów Zjednoczonych.

## <a name="regional-presence-with-global-distribution"></a>Obecność regionalna z dystrybucją globalną

Wszystkie interfejsy API udostępniane przez Azure Cosmos DB (w tym SQL, MongoDB, Cassandra, Gremlin i Table) są domyślnie dostępne we wszystkich regionach platformy Azure. Można na przykład mieć MongoDB i Cassandra interfejsy API udostępniane przez Azure Cosmos DB nie tylko we wszystkich globalnych regionach świadczenia usługi Azure, ale również w przypadku suwerennych chmur, takich jak Chiny, Niemcy, instytucje rządowe i Departament Obrony (DoD).

Azure Cosmos DB to [globalnie dystrybuowana](distribute-data-globally.md) usługa bazy danych. Możesz skojarzyć dowolną liczbę regionów świadczenia usługi Azure z kontem usługi Azure Cosmos, a Twoje dane są automatycznie i w sposób niewidoczny dla użytkownika zreplikowane. Możesz w dowolnym momencie dodać lub usunąć region do konta usługi Azure Cosmos. Dzięki możliwości globalnej dystrybucji gotowe i protokołu replikacji z wieloma wzorcami Azure Cosmos DB oferuje mniej niż 10 operacji odczytu i zapisu w 99 percentylu, 99,999 dostępności i zapisu, a także możliwość elastycznego skalowania przepływności w celu odczytu i zapisu we wszystkich regionach skojarzonych z kontem platformy Azure Cosmos. Azure Cosmos DB, oferuje także pięć dobrze zdefiniowanych modeli spójności i można zastosować konkretny model spójności do danych. Na koniec Azure Cosmos DB jest jedyną usługą bazy danych w branży, która zapewnia kompleksową [Umowa dotycząca poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) obejmującą przepływność, opóźnienie 99 percentylu, wysoką dostępność i spójność. Powyższe możliwości są dostępne we wszystkich chmurach platformy Azure.

## <a name="next-steps"></a>Następne kroki

Teraz możesz zapoznać się z podstawowymi koncepcjami Azure Cosmos DB z następującymi artykułami:

* [Globalna dystrybucja danych](distribute-data-globally.md)
* [Jak zarządzać kontem Azure Cosmos DB](manage-account.md)
* [Obsługa przepływności dla kontenerów i baz danych platformy Azure Cosmos](set-throughput.md)
* [Azure Cosmos DB umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
