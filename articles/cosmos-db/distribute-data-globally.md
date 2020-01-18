---
title: Dystrybuuj dane globalnie za pomocą Azure Cosmos DB
description: Dowiedz się więcej na temat replikacji geograficznej globalnej, wielu wzorców, trybu failover i odzyskiwania danych przy użyciu globalnych baz danych z Azure Cosmos DB — globalnie dystrybuowanej, wielomodelowej usługi bazy danych.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 84cd201cd758293082a61a87528332c7d8c58811
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264309"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Globalna dystrybucja danych za pomocą Azure Cosmos DB — Omówienie

Dzisiejsze aplikacje muszą być bardzo dynamiczne i zawsze w trybie online. Aby zapewnić małe opóźnienia i wysoką dostępność, wystąpienia tych aplikacji powinny być wdrażane w centrach danych, które są blisko ich użytkowników. Te aplikacje są zwykle wdrażane w wielu centrach danych i są nazywane globalnie dystrybuowanymi. Aplikacje rozproszone globalnie muszą mieć globalnie rozproszoną bazę danych, która może w sposób przezroczysty replikować dane w dowolnym miejscu na świecie, aby umożliwić aplikacjom działanie na kopii danych znajdujących się blisko użytkowników. 

Azure Cosmos DB to globalnie dystrybuowana usługa bazy danych, która została zaprojektowana w celu zapewnienia małych opóźnień, elastycznej skalowalności, dobrze zdefiniowanej semantyki pod kątem spójności danych i wysokiej dostępności. W krótkim czasie, jeśli aplikacja wymaga zagwarantowania szybkiego czasu odpowiedzi w dowolnym miejscu na świecie, jeśli wymagane jest zawsze w trybie online i wymaga nieograniczonej i elastycznej skalowalności przepływności i magazynu, należy skompilować aplikację na Azure Cosmos DB.

Bazy danych można skonfigurować tak, aby były one dystrybuowane globalnie i dostępne w dowolnym regionie świadczenia usługi Azure. Aby zmniejszyć opóźnienie, umieść dane blisko miejsca, w którym znajdują się użytkownicy. Wybór wymaganych regionów zależy od globalnego zasięgu aplikacji i miejsca, w którym znajdują się użytkownicy. Cosmos DB w sposób przezroczysty replikuje dane do wszystkich regionów skojarzonych z Twoim kontem Cosmos. Udostępnia on pojedynczy obraz systemu dla globalnie rozproszonej bazy danych usługi Azure Cosmos i kontenerów, które aplikacja może odczytać i zapisać lokalnie. 

Za pomocą Azure Cosmos DB możesz w dowolnym momencie dodać lub usunąć regiony skojarzone z Twoim kontem. Aby można było dodać lub usunąć region, nie trzeba wstrzymywać ani ponownie wdrażać aplikacji. Jest ono ciągle dostępne przez cały czas ze względu na funkcje wielomultihostingune zapewniane przez usługę.

![Topologia wdrożenia o wysokiej dostępności](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>Najważniejsze zalety dystrybucji globalnej

**Twórz globalne aplikacje aktywne-aktywne.** Przy użyciu nowej usługi replikacji wielu wzorców każdy region obsługuje zarówno zapis, jak i odczyt. Możliwości wielu wzorców umożliwiają również:

- Nieograniczone elastyczne zapisywanie i skalowalność. 
- 99,999% dostępności odczytu i zapisu na całym świecie.
- Gwarantowane odczyty i zapisy obsługiwane w czasie krótszym niż 10 milisekund w 99 percentylu.

Korzystając z Azure Cosmos DB interfejsów API multihostingu, aplikacja rozpoznaje najbliższy region i może wysyłać żądania do tego regionu. Najbliższy region jest identyfikowany bez żadnych zmian konfiguracji. Po dodaniu i usunięciu regionów do i z konta usługi Azure Cosmos, aplikacja nie musi być ponownie wdrażana ani wstrzymywana, przez cały czas będzie nadal mieć wysoką dostępność.

**Twórz wysoce reagujące aplikacje.** Aplikacja może wykonywać operacje odczytu i zapisu niemal w czasie rzeczywistym względem wszystkich regionów wybranych dla bazy danych. Azure Cosmos DB wewnętrznie obsługuje replikację danych między regionami z gwarancją poziomu spójności wybranego poziomu.

**Twórz aplikacje o wysokiej dostępności.** Uruchamianie bazy danych w wielu regionach na całym świecie zwiększa dostępność bazy danych. Jeśli jeden region jest niedostępny, inne regiony automatycznie obsługują żądania aplikacji. Azure Cosmos DB oferuje dostępność do odczytu i zapisu na 99,999% dla wieloregionowych baz danych.

**Zachowaj ciągłość biznesową podczas awarii regionalnej.** Azure Cosmos DB obsługuje [Automatyczne przełączanie w tryb failover](how-to-manage-database-account.md#automatic-failover) podczas regionalnej awarii. W trakcie regionalnej awarii Azure Cosmos DB nadal utrzymuje czas oczekiwania, dostępność, spójność i przepływność umowy SLA. Aby zapewnić wysoką dostępność całej aplikacji, Cosmos DB oferuje ręczny interfejs API trybu failover w celu symulowania awarii regionalnej. Korzystając z tego interfejsu API, można wykonywać szczegółowe informacje o ciągłości biznesowej.

**Globalne skalowanie przepływności odczytu i zapisu.** Każdy region można włączyć do zapisu i elastycznie skalować operacje odczytu i zapisu na całym świecie. Przepływność, którą aplikacja konfiguruje w usłudze Azure Cosmos Database lub że kontener jest gwarantowany dla wszystkich regionów skojarzonych z kontem usługi Azure Cosmos. Tymczasowa przepływność jest gwarantowana na podstawie [finansów umowy SLA](https://aka.ms/acdbsla).

**Wybieraj spośród kilku dobrze zdefiniowanych modeli spójności.** Protokół replikacji Azure Cosmos DB oferuje pięć dobrze zdefiniowanych, praktycznych i intuicyjnych modeli spójności. Każdy model ma kompromis między spójnością i wydajnością. Te modele spójności umożliwiają łatwe tworzenie globalnie rozproszonych aplikacji.

## <a id="Next Steps"></a>Następne kroki

Więcej informacji na temat dystrybucji globalnej można znaleźć w następujących artykułach:

* [Globalna dystrybucja — pod okapem](global-dist-under-the-hood.md)
* [Jak skonfigurować wiele wzorców w aplikacjach](how-to-multi-master.md)
* [Konfigurowanie klientów dla usługi wieloadresowości](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Dodawanie lub usuwanie regionów z konta usługi Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Utwórz niestandardowe zasady rozwiązywania konfliktów dla kont interfejsu API SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Programowalne modele spójności w Cosmos DB](consistency-levels.md)
* [Wybierz poziom spójności odpowiednie dla twojej aplikacji](consistency-levels-choosing.md)
* [Poziomy spójności w różnych interfejsów API usługi Azure Cosmos DB](consistency-levels-across-apis.md)
* [Dostępność i wydajność kompromisy dla różnych poziomów spójności](consistency-levels-tradeoffs.md)

