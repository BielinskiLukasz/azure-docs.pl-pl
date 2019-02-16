---
title: Wysoka dostępność w usłudze Azure Cosmos DB
description: W tym artykule opisano, jak usługa Azure Cosmos DB zapewnia wysoką dostępność
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 2/13/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: b5e99b421e66f087a1793f5301736e192ef75c08
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311243"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Wysoka dostępność dzięki usłudze Azure Cosmos DB

Usługa Azure Cosmos DB w sposób przezroczysty replikuje dane we wszystkich regionach platformy Azure skojarzony z Twoim kontem Cosmos. Usługa cosmos DB wykorzystuje wiele warstw nadmiarowości danych, jak pokazano na poniższej ilustracji:

![Podział na partycje fizyczne](./media/high-availability/cosmosdb-data-redundancy.png)

- Dane w kontenerach Cosmos w poziomie jest podzielona na partycje.

- W każdym regionie każdej partycji jest chroniony za pomocą wszystkie zapisy replikowane i trwałym zatwierdzeniu większością replik zestawu replik. Repliki są dystrybuowane na maksymalnie 10-20 domen błędów.

- Każda partycja we wszystkich regionach są replikowane. Każdy region zawiera wszystkie partycje dane kontenera Cosmos i mogą akceptować zapisów i obsługiwać operacje odczytu.  

Jeśli Twoje konta usługi Cosmos jest rozłożona na regiony platformy Azure N, będzie co najmniej N x cztery kopie wszystkich danych. Oprócz zapewnienia dostępu do danych z małymi opóźnieniami i skalowanie przepływności odczytu/zapisu między regionami skojarzonych z Twoim kontem Cosmos, również o większej liczbie regionów (N wyższej) zwiększa dostępność.  

## <a name="slas-for-availability"></a>Umowy SLA dla dostępności

Jako globalnie rozproszonej bazy danych Cosmos DB zapewnia kompleksowe umowy SLA, które obejmują przepustowość, opóźnienie w 99. percentylu, spójności i wysokiej dostępności. W poniższej tabeli przedstawiono gwarancje dotyczące wysokiej dostępności oferowanej przez Cosmos DB dla kont pojedynczych i wielu regionów. Wysoką dostępność należy skonfigurować konta Cosmos mieć wiele regionów zapisu.

|Typ operacji  | Pojedynczy region |Multiregionalne (zapisuje pojedynczy region)|Multiregionalne (zapisuje wielu regionów) |
|---------|---------|---------|-------|
|Operacje zapisu    | 99.99    |99.99   |99.999|
|Operacje odczytu     | 99.99    |99.999  |99.999|

> [!NOTE]
> W praktyce dostępność rzeczywiste zapisu powiązana nieaktualność, sesja, spójny prefiks i spójność ostateczna modeli jest znacznie wyższa niż opublikowanych umowy SLA. Rzeczywiste dostępność do odczytu dla wszystkich poziomów spójności jest znacznie wyższa niż opublikowanych umowy SLA.

## <a name="high-availability-with-cosmos-db-in-the-face-of-regional-outages"></a>Wysoka dostępność za pomocą usługi Cosmos DB w przypadku awarii regionalnej

Regionalnej awarii nie są niczym niezwykłym, a usługi Azure Cosmos DB zapewnia, że baza danych jest zawsze dostępna. Poniższe szczegóły Przechwyć zachowanie usługi Cosmos DB podczas awarii, w zależności od konfiguracji konta usługi Cosmos:

- Za pomocą usługi Cosmos DB zanim operacji zapisu zostało potwierdzone, do klienta, danych jest trwałym zatwierdzeniu przez kworum replik w regionie, który akceptuje operacji zapisu.

- Skonfigurowano regiony odczytu dla wielu kont w wielu regionach będzie o wysokiej dostępności dla odczytów i zapisów. Wywoływania regionalnego trybu failover są natychmiastowe i nie wymaga dokonywania żadnych zmian w aplikacji.

- Multiregionalne konta z regionem zapisu jednego: Podczas awarii region zapisu tych kont będzie pozostawać stale dostępnymi dla odczytów. Jednak opłata za zapisywanie należy "włączyć automatyczny tryb failover" na Twoim koncie Cosmos, do których to dotyczy regionu do innego regionu skojarzonego z trybu failover. Przełączenie w tryb failover miało miejsce w kolejności priorytet regionu, który został określony. Po pewnym czasie po objęte wpływem region jest wróci do trybu online, nie zostały zreplikowane dane w regionie zapisu dotyczy problem, w czasie awarii jest udostępniany za pośrednictwem konflikty źródła danych. Aplikacje mogą odczytywać konflikty źródła danych, rozwiąż konflikty, w oparciu o logikę specyficzną dla aplikacji i zapisywać zaktualizowane dane z powrotem do kontenera Cosmos zgodnie z potrzebami. Po odzyskaniu region zapisu wcześniej objęte wpływem staje się automatycznie dostępne jako region odczytu. Można wywołać ręcznej pracy awaryjnej i skonfigurować objęte wpływem region jako regionu zapisu. Ręczna praca awaryjna można zrobić za pomocą [wiersza polecenia platformy Azure lub w witrynie Azure portal](how-to-manage-database-account.md#manual-failover). Brak **bez utraty danych lub dostępności** przed, podczas lub po ręcznej pracy awaryjnej. Aplikacja jest nadal o wysokiej dostępności. 

- Multiregionalne konta z regionem zapisu jednego: Podczas awarii regionem odczytu te konta będzie pozostawać stale dostępnymi dla operacji odczytu i zapisu. Objęte wpływem region jest automatycznie rozłączany z regionu zapisu i zostanie oznaczona w trybie offline. Zestawy SDK Cosmos DB będzie przekierowywać odczytu wywołania do następnego dostępnego regionu na liście preferowany region. Jeśli żaden z regionów na liście preferowany region jest dostępny, wywołania automatycznie wrócić do bieżącego regionu zapisu. Żadne zmiany nie są wymagane w kodzie aplikacji do obsługi awarii w regionie odczytu. Po pewnym czasie gdy objęte wpływem region jest wróci do trybu online, wcześniej objęte wpływem odczytu z regionu zostanie automatycznie zsynchronizowana z bieżącego regionu zapisu i będzie można ponownie obsługiwać żądań odczytu. Dalsze operacje odczytu są przekierowywane do regionu odzyskane bez wprowadzania jakichkolwiek zmian w kodzie aplikacji. Podczas pracy awaryjnej i ponowne przyłączanie regionu zakończyły się niepowodzeniem gwarancji spójności odczytu nadal uznawane przez usługi Cosmos DB.

- Kont w jednym regionie mogą zostać utracone po awarii regionalnej dostępności. Zalecane jest, aby skonfigurować co najmniej dwóch regionach (najlepiej, co najmniej dwa regiony zapisu) przy użyciu konta usługi Cosmos w celu zapewnienia wysokiej dostępności przez cały czas.

- Nawet w przypadku bardzo rzadkie i niefortunne gdy region platformy Azure jest trwale nieodwracalny, istnieje nie ryzyko utraty danych skonfigurowanie Twojego konta usługi Cosmos wielu regionów za pomocą domyślnego poziomu spójności silne. W przypadku region zapisu trwale nieodwracalny, dla kont Cosmos w wielu regionach skonfigurowano spójności powiązana nieaktualność potencjalnych okno utraty danych jest ograniczony do okna nieaktualność; w sesji, poziom spójny prefiks i spójność ostateczna potencjalnych okno utraty danych jest ograniczony do maksymalnie pięć sekund.

## <a name="building-highly-available-applications"></a>Tworzenie aplikacji o wysokiej dostępności

- Aby upewnić się wysoką zapisu, a także dostępność do odczytu, skonfiguruj konto usługi Cosmos, na co najmniej dwóch regionach za pomocą zapisu wielu regionów. Ta konfiguracja zapewnia dostępność, najmniejszego opóźnienia i skalowalności, zarówno dla odczytuje i zapisuje je to jest poparte umowy SLA. Aby dowiedzieć się więcej, zobacz temat jak [Skonfiguruj konto usługi Cosmos z wieloma regionami zapisu](tutorial-global-distribution-sql-api.md). Aby skonfigurować Multi-Master w swoich aplikacjach, zobacz [sposobu konfigurowania Multi-Master](how-to-multi-master.md).

- Dla konta usługi Cosmos wielu regionów, które są skonfigurowane z regionem zapisu pojedynczego [włączyć automatyczny tryb failover przy użyciu wiersza polecenia platformy Azure lub w witrynie Azure portal](how-to-manage-database-account.md#automatic-failover). Po włączeniu automatycznej pracy awaryjnej, zawsze, gdy występuje regionalnej awarii, Cosmos DB zostanie automatycznie trybu failover Twoje konto.  

- Nawet w przypadku Twojego konta usługi Cosmos o wysokiej dostępności, aplikacja może nie być poprawnie zaprojektowana pozostaje o wysokiej dostępności. Aby przetestować end-to-end wysokiej dostępności dla aplikacji, okresowo wywoływać [ręcznej pracy awaryjnej przy użyciu wiersza polecenia platformy Azure lub w witrynie Azure portal](how-to-manage-database-account.md#manual-failover), jako część testowania aplikacji lub odzyskiwania po awarii (DR) awarii.

- W środowisku globalnie rozproszona baza danych ma bezpośrednią relację między trwałości danych i na spójność obecności awarii całego regionu. Podczas opracowywania planem ciągłości biznesowej, należy zrozumieć maksymalnego dopuszczalnego czasu oczekiwania na pełne odzyskanie aplikacji po wystąpieniu zdarzenia powodującego zakłócenia. Czas wymagany do przeprowadzenia pełnego odzyskania aplikacji jest znany jako cel czasu odzyskiwania (RTO). Należy również zrozumieć maksymalny okres najnowszych aktualizacji danych, aplikacja może tolerować utraty podczas odzyskiwania po wystąpieniu zdarzenia powodującego zakłócenia. Okres aktualizacji, które mogą umożliwić utratę jest określany jako cel punktu odzyskiwania (RPO). Aby wyświetlić cel punktu odzyskiwania i cel czasu odzyskiwania dla usługi Azure Cosmos DB, zobacz [spójności poziomy i danych trwałości](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Kolejne kroki

Następnie informacje na temat skalowania przepływności w następującym artykule:

* [Dostępność i wydajność kompromisy dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
* [Globalnie skalowanie aprowizowana przepływność](scaling-throughput.md)
* [Dystrybucja globalna - kulisy](global-dist-under-the-hood.md)
* [Poziomy spójności w usłudze Azure Cosmos DB](consistency-levels.md)
* [Jak skonfigurować Multi-Master w swoich aplikacjach](how-to-multi-master.md)
