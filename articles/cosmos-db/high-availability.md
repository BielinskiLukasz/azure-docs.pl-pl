---
title: High availability in Azure Cosmos DB (Wysoka dostępność w usłudze Azure Cosmos DB)
description: W tym artykule opisano, jak Azure Cosmos DB zapewnia wysoką dostępność
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 2f464a0b519dd7f378790c135a05d04e02704ac8
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027736"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Wysoka dostępność w usłudze Azure Cosmos DB

Azure Cosmos DB w sposób przezroczysty replikuje dane do wszystkich regionów świadczenia usługi Azure skojarzonych z Twoim kontem usługi Azure Cosmos. Azure Cosmos DB wykorzystuje wiele warstw nadmiarowości danych, jak pokazano na poniższej ilustracji:

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="Partycjonowanie fizyczne" border="false":::

- Dane w kontenerach usługi Azure Cosmos są [podzielone na partycje w poziomie](partitioning-overview.md).

- Zestaw partycji jest kolekcją wielu zestawów replik. W każdym regionie każda partycja jest chroniona przez zestaw replik, a wszystkie zapisy są replikowane i trwale zatwierdzone przez większość replik. Repliki są dystrybuowane w wielu domenach błędów 10-20.

- Każda partycja we wszystkich regionach jest replikowana. Każdy region zawiera wszystkie partycje danych kontenera usługi Azure Cosmos i może akceptować zapisy i obsłużyć odczyty.  

Jeśli Twoje konto usługi Azure Cosmos jest dystrybuowane w obrębie *N* regionów platformy Azure, wszystkie dane będą mieć co najmniej *N* x 4 kopii. Ogólnie rzecz biorąc, konto usługi Azure Cosmos w więcej niż 2 regionach zwiększa dostępność aplikacji i zapewnia małe opóźnienia w skojarzonych regionach. 

## <a name="slas-for-availability"></a>Umowy SLA do dostępności

Jako globalnie dystrybuowana baza danych Azure Cosmos DB zapewnia kompleksową umowy SLA, która obejmuje przepływność, opóźnienie w 99 percentylu, spójności i wysokiej dostępności. W poniższej tabeli przedstawiono gwarancje dotyczące wysokiej dostępności zapewnianej przez Azure Cosmos DB w przypadku kont z pojedynczym i wieloregionem. Aby zapewnić wysoką dostępność, należy zawsze skonfigurować konta usługi Azure Cosmos tak, aby zawierały wiele regionów zapisu (nazywanych również wieloma wzorcami).

|Typ operacji  | Pojedynczy region |Wiele regionów (zapis w jednym regionie)|Wiele regionów (zapisy w regionie wieloregionowym) |
|---------|---------|---------|-------|
|Powoduje    | 99,99    |99,99   |99,999|
|Odczytywan     | 99,99    |99,999  |99,999|

> [!NOTE]
> W ramach tej usługi rzeczywista dostępność zapisu dla nieodświeżonego, sesji, spójnego prefiksu i wzorów spójności ostatecznej jest znacznie wyższa niż opublikowana umowy SLA. Rzeczywista dostępność odczytu dla wszystkich poziomów spójności jest znacznie wyższa niż opublikowana umowy SLA.

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>Wysoka dostępność z Azure Cosmos DB w przypadku awarii regionalnej

W rzadkich przypadkach regionalnych awarii Azure Cosmos DB gwarantuje, że baza danych zawsze ma wysoką dostępność. Poniższe informacje szczegółowe przechwytują zachowanie Azure Cosmos DB podczas przestoju, w zależności od konfiguracji konta usługi Azure Cosmos:

- Gdy Azure Cosmos DB, przed potwierdzeniem operacji zapisu do klienta dane są trwale przez kworum replik w regionie, który akceptuje operacje zapisu.

- Konta wieloregionowe skonfigurowane z regionami wielokrotnego zapisu/wielu wzorców będą wysoce dostępne dla operacji zapisu i odczytu. Regionalne przejścia w tryb failover są chwilowe i nie wymagają żadnych zmian w aplikacji.

- Konta w jednym regionie mogą utracić dostępność po awarii regionalnej. Zawsze zaleca się skonfigurowanie **co najmniej dwóch regionów** (najlepiej co najmniej dwóch regionów zapisu) przy użyciu konta usługi Azure Cosmos, aby zapewnić wysoką dostępność.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Konta wieloregionowe z regionem jednokrotnego zapisu (przestój w regionie zapisu)

- Podczas przestoju w regionie zapisu konto usługi Azure Cosmos automatycznie promuje region pomocniczy jako nowy podstawowy region zapisu, gdy w ramach konta usługi Azure Cosmos zostanie skonfigurowane **Automatyczne przełączanie do trybu failover** . Po włączeniu tryb failover nastąpi w innym regionie w podanej kolejności regionu.
- Gdy wcześniej zmieniony region jest w trybie online, wszelkie dane zapisu, które nie zostały zreplikowane w przypadku niepowodzenia regionu, są udostępniane za pomocą [źródła konfliktów](how-to-manage-conflicts.md#read-from-conflict-feed). Aplikacje mogą odczytywać źródła konfliktów, rozwiązywać konflikty na podstawie logiki specyficznej dla aplikacji, a następnie zapisywać zaktualizowane dane z powrotem do kontenera platformy Azure Cosmos zgodnie z potrzebami.
- Po odzyskaniu wcześniej zmienionego regionu zapisu zostanie on automatycznie udostępniony jako region odczytu. Można wrócić do odzyskanego regionu jako region zapisu. Regiony można przełączyć przy użyciu [programu PowerShell, interfejsu wiersza polecenia platformy Azure lub Azure Portal](how-to-manage-database-account.md#manual-failover). Przed, w trakcie lub po przełączeniu regionu zapisu **nie ma utraty danych ani dostępności** , a aplikacja nadal będzie o wysokiej dostępności.

> [!IMPORTANT]
> Zdecydowanie zaleca się skonfigurowanie kont usługi Azure Cosmos używanych na potrzeby obciążeń produkcyjnych, aby **umożliwić automatyczne przełączanie do trybu failover**. Ręczne przełączenie w tryb failover wymaga łączności między dodatkowym i podstawowym regionem zapisu, aby ukończyć sprawdzanie spójności, aby upewnić się, że podczas pracy w trybie failover nie ma utraty Jeśli region podstawowy jest niedostępny, to sprawdzanie spójności nie będzie możliwe, a ręczne przełączenie w tryb failover nie powiedzie się, co spowodowało utratę dostępności zapisu.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Konta wieloregionowe z regionem jednokrotnego zapisu (awaria regionu odczytu)

- Podczas awarii regionu odczytu konta usługi Azure Cosmos korzystające z dowolnego poziomu spójności lub silnej spójności z co najmniej trzema regionami odczytu pozostaną wysoce dostępne do odczytu i zapisu.
- Konta usługi Azure Cosmos z silną spójnością w przypadku dwóch lub mniej regionów odczytu (w tym regionu zapisu & odczytu) utracą dostępność zapisu podczas awarii regionu odczytu, ale będą mieć dostęp do odczytu dla pozostałych regionów.
- Region, którego dotyczy problem, jest automatycznie rozłączany i zostanie oznaczony jako w trybie offline. [Zestawy sdk Azure Cosmos DB](sql-api-sdk-dotnet.md) będą przekierowywać wywołania odczytu do następnego dostępnego regionu na liście preferowanych regionów.
- Jeśli żaden z regionów na liście preferowanych regionów nie jest dostępny, wywołania automatycznie wracają do bieżącego regionu zapisu.
- W kodzie aplikacji nie są wymagane żadne zmiany w celu obsługi awarii regionu odczytu. Gdy region odczytu, którego dotyczy problem, zostanie przywrócony do trybu online, zostanie automatycznie zsynchronizowany z bieżącym regionem zapisu i będzie ponownie dostępny do obsłużynia żądań odczytu.
- Dalsze operacje odczytu są przekierowywane do odzyskanego regionu bez konieczności wprowadzania jakichkolwiek zmian w kodzie aplikacji. Podczas pracy w trybie failover i ponownego przyłączania do poprzedniego regionu, gwarancje spójności odczytu są nadal uznawane przez Azure Cosmos DB.
- Nawet w rzadkich i niezbyt krótkich przypadkach, gdy region platformy Azure jest nieodwracalnie nieodwracalny, nie ma utraty danych, jeśli konto usługi Azure Cosmos dla wieloregionu jest skonfigurowane z *silną* spójnością. W przypadku trwałego odzyskania regionu zapisu w wieloregionowym koncie usługi Azure Cosmos skonfigurowanym ze spójnością nieodświeżoną, potencjalne okno utraty danych jest ograniczone do okna nieodświeżone (*K* lub *T*), gdzie K = 100 000 aktualizacji i T = 5 minut. W przypadku sesji, spójnego i ostatecznego poziomu spójności, potencjalne okno utraty danych jest ograniczone do maksymalnie 15 minut. Aby uzyskać więcej informacji na temat elementów docelowych RTO i RPO dla Azure Cosmos DB, zobacz [poziomy spójności i trwałość danych](consistency-levels-tradeoffs.md#rto)

## <a name="availability-zone-support"></a>Obsługa strefy dostępności

Oprócz odporności między regionami można teraz włączyć **nadmiarowość stref** podczas wybierania regionu do skojarzenia z bazą danych Azure Cosmos.

W przypadku obsługi stref dostępności Azure Cosmos DB zapewnia, że repliki są umieszczane w wielu strefach w danym regionie w celu zapewnienia wysokiej dostępności i odporności podczas awarii stref. W tej konfiguracji nie wprowadzono żadnych zmian w czasie oczekiwania i innych umowy SLA. W przypadku awarii pojedynczej strefy nadmiarowość stref zapewnia pełną trwałość danych z elementem RPO = 0 i dostępnością z RTO = 0.

Nadmiarowość stref jest *dodatkową możliwością* [replikacji w wieloregionowej funkcji zapisów](how-to-multi-master.md) . W celu uzyskania odporności regionalnej nie można korzystać z samej nadmiarowości strefy. Na przykład w przypadku regionalnego systemu przestoju lub małego opóźnienia w regionach zaleca się, aby oprócz nadmiarowości strefy była dostępna wiele regionów zapisu.

Podczas konfigurowania wieloregionowych zapisów dla konta usługi Azure Cosmos możesz zrezygnować z nadmiarowości strefy bez dodatkowych kosztów. W przeciwnym razie zapoznaj się z poniższymi uwagami dotyczącymi cen obsługi nadmiarowości stref. Nadmiarowość strefy można włączyć w istniejącym regionie konta usługi Azure Cosmos, usuwając region i dodając go ponownie z włączonym nadmiarowością strefy.

Ta funkcja jest dostępna w: *Południowe Zjednoczone Królestwo, Azja Południowo-Wschodnia, Wschodnie stany USA, Wschodnie stany USA 2, środkowe stany USA, Europa Zachodnia, zachodnie stany USA 2, Japonia Wschodnia, Europa Północna, Francja środkowa* .

> [!NOTE]
> Włączenie Strefy dostępności dla jednego regionu konta platformy Azure Cosmos spowoduje naliczenie opłat, które są równoznaczne z dodaniem dodatkowego regionu do konta. Aby uzyskać szczegółowe informacje o cenach, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/) oraz [Koszt dla wieloregionu w](optimize-cost-regions.md) artykułach Azure Cosmos DB.

Poniższa tabela zawiera podsumowanie możliwości wysokiej dostępności różnych konfiguracji konta:

|KPI  |Pojedynczy region bez Strefy dostępności (nie AZ)  |Pojedynczy region z Strefy dostępności (AZ)  |Zapisy w wielu regionach z Strefy dostępności (AZ, 2 regiony) — najbardziej zalecanym ustawieniem |
|---------|---------|---------|---------|
|Umowa SLA dotycząca dostępności zapisu | 99,99% | 99,99% | 99.999% |
|Umowa SLA dotycząca dostępności odczytu  | 99,99% | 99,99% | 99.999% |
|Price | Stawka rozliczenia w jednym regionie | Stawka rozliczenia strefy dostępności pojedynczego regionu | Wieloregionowa stawka rozliczeniowa |
|Awarie stref — utrata danych | Utrata danych | Brak utraty danych | Brak utraty danych |
|Awarie stref — dostępność | Utrata dostępności | Brak utraty dostępności | Brak utraty dostępności |
|Opóźnienie odczytu | Między regionami | Między regionami | Małe |
|Opóźnienie zapisu | Między regionami | Między regionami | Małe |
|Awaria regionalna — utrata danych | Utrata danych |  Utrata danych | Utrata danych <br/><br/> W przypadku używania ograniczonej spójności niezgodności z wieloma regionami zapisu i więcej niż jednym regionem utrata danych jest ograniczona do ograniczenia nieaktualnego skonfigurowanego na Twoim koncie. <br /><br />Można uniknąć utraty danych podczas regionalnej awarii, konfigurując silną spójność z wieloma regionami. Ta opcja ma wpływ na dostępność i wydajność. Można ją skonfigurować tylko na kontach, które są skonfigurowane do zapisu w jednym regionie. |
|Awaria regionalna — dostępność | Utrata dostępności | Utrata dostępności | Brak utraty dostępności |
|Przepływność | X RU/s zainicjowana przepływność | X RU/s zainicjowana przepływność | przepustowość z obsługą jednostki RU/s <br/><br/> Ten tryb konfiguracji wymaga dwukrotnej ilości przepływności w porównaniu do jednego regionu z Strefy dostępności, ponieważ istnieją dwa regiony. |

> [!NOTE]
> Aby włączyć obsługę strefy dostępności dla konta usługi Azure Cosmos dla regionu wieloregionowego, konto musi mieć włączone zapisywanie zapisów w ramach wieloregionu.

Nadmiarowość strefy można włączyć podczas dodawania regionu do nowych lub istniejących kont usługi Azure Cosmos. Aby włączyć nadmiarowość strefy na koncie usługi Azure Cosmos, należy ustawić `isZoneRedundant` flagę na `true` dla określonej lokalizacji. Tę flagę można ustawić we właściwości Locations. Na przykład poniższy fragment kodu programu PowerShell umożliwia nadmiarowość strefy dla regionu "Azja Południowo-Wschodnia":

```powershell
$locations = @(
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" },
    @{ "locationName"="East US"; "failoverPriority"=1; "isZoneRedundant"= "true" }
)
```

Następujące polecenie pokazuje, jak włączyć nadmiarowość strefy dla regionów "Wschodnie" i "WestUS2":

```azurecli-interactive
az cosmosdb create \
  --name mycosmosdbaccount \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --default-consistency-level Session \
  --locations regionName=EastUS failoverPriority=0 isZoneRedundant=True \
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True
```

Strefy dostępności można włączyć przy użyciu Azure Portal podczas tworzenia konta usługi Azure Cosmos. Podczas tworzenia konta, upewnij się, że włączono **geograficzną**i **Wieloregionową zapis**, i wybierz region, w którym strefy dostępności są obsługiwane:

:::image type="content" source="./media/high-availability/enable-availability-zones-using-portal.png" alt-text="Włącz Strefy dostępności przy użyciu Azure Portal"::: 

## <a name="building-highly-available-applications"></a>Tworzenie aplikacji o wysokiej dostępności

- Aby zapewnić wysoką dostępność i odczyt, skonfiguruj konto usługi Azure Cosmos tak, aby obejmowało co najmniej dwa regiony z regionami wielokrotnego zapisu. Ta konfiguracja zapewnia najwyższą dostępność, najniższy czas oczekiwania i najlepszą skalowalność dla operacji odczytu i zapisu, które są obsługiwane przez umowy SLA. Aby dowiedzieć się więcej, zobacz jak [skonfigurować konto platformy Azure Cosmos z wieloma regionami zapisu](tutorial-global-distribution-sql-api.md).

- Dla wieloregionowych kont usługi Azure Cosmos, które są skonfigurowane za pomocą regionu jednokrotnego zapisu, [Włącz automatyczne przełączanie do trybu failover przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure Portal](how-to-manage-database-account.md#automatic-failover). Po włączeniu automatycznego trybu failover, gdy wystąpi awaria regionalna, Cosmos DB automatycznie przejdzie w tryb failover na koncie.  

- Nawet jeśli Twoje konto usługi Azure Cosmos ma wysoką dostępność, aplikacja może nie być prawidłowo zaprojektowana tak, aby pozostawała wysoce dostępna. Aby przetestować kompleksową wysoką dostępność aplikacji, w ramach przechodzenia do testowania aplikacji lub odzyskiwania po awarii (DR), tymczasowo wyłącz automatyczne przełączanie do trybu failover dla konta, wywołaj [ręczną pracę awaryjną przy użyciu programu PowerShell, interfejsu wiersza polecenia platformy Azure lub Azure Portal](how-to-manage-database-account.md#manual-failover), a następnie Monitoruj tryb failover aplikacji. Po zakończeniu można przeprowadzić powrót po awarii do regionu podstawowego i przywrócić automatyczne przejście w tryb failover dla konta.

- W całym globalnie rozproszonym środowisku bazy danych istnieje bezpośrednia relacja między poziomem spójności a trwałością danych w przypadku awarii całego regionu. Podczas opracowywania planu ciągłości biznesowej należy zrozumieć maksymalny akceptowalny czas, po upływie którego aplikacja zostanie w pełni odzyskana po zdarzeniu zakłócania. Czas wymagany do pełnego odzyskania aplikacji jest znany jako cel czasu odzyskiwania (RTO). Należy również zrozumieć maksymalny okres ostatnich aktualizacji danych, które aplikacja może tolerować podczas odzyskiwania po wystąpieniu zdarzenia zakłócenia. Okres aktualizacji, którego utrata może być tolerowana, jest określany jako cel punktu odzyskiwania (RPO, recovery point objective). Aby zobaczyć cel punktu odzyskiwania i RTO dla Azure Cosmos DB, zobacz [poziomy spójności i trwałość danych](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Następne kroki

Następnie możesz zapoznać się z następującymi artykułami:

- [Wady dostępności i wydajności dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
- [Globalne skalowanie aprowizowanej przepływności](scaling-throughput.md)
- [Dystrybucja globalna — szczegóły działania](global-dist-under-the-hood.md)
- [Poziomy spójności w Azure Cosmos DB](consistency-levels.md)
- [Jak skonfigurować konto Cosmos z wieloma regionami zapisu](how-to-multi-master.md)
