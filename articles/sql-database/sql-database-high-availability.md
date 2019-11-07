---
title: Wysoka dostępność — usługa Azure SQL Database
description: Dowiedz się więcej o możliwościach i funkcjach wysokiej dostępności usługi Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sashan
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 10/14/2019
ms.openlocfilehash: b34590ca275b6e7254af7820fdc1a03655351cea
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689957"
---
# <a name="high-availability-and-azure-sql-database"></a>Wysoka dostępność i Azure SQL Database

Celem architektury wysokiej dostępności w Azure SQL Database jest zagwarantowanie, że baza danych działa i działa 99,99% czasu, bez zakłócania wpływu operacji konserwacji i przestojów. Platforma Azure automatycznie obsługuje krytyczne zadania obsługi, takie jak stosowanie poprawek, kopii zapasowych, uaktualnienia systemu Windows i programu SQL, a także niezaplanowanych zdarzeń, takich jak sprzętowe, programowe lub awarie sieci.  Gdy bazowe wystąpienie programu SQL Server jest poprawione lub działa w trybie failover, przestoje nie jest zauważalne, jeśli w aplikacji jest stosowana [logika ponawiania](sql-database-develop-overview.md#resiliency) . Azure SQL Database można szybko odzyskać nawet w najbardziej krytycznych okolicznościach, dzięki czemu dane są zawsze dostępne.

Rozwiązanie wysokiej dostępności zostało zaprojektowane z myślą o zapewnieniu, że przekazane dane nigdy nie zostaną utracone z powodu niepowodzeń, że operacje konserwacji nie wpłyną na obciążenie, a baza danych nie będzie single point of failure w architekturze oprogramowania. Nie ma okien obsługi ani przestojów, które powinny wymagać zatrzymania obciążenia, gdy baza danych jest uaktualniana lub utrzymywana. 

Istnieją dwa modele architektoniczne wysokiej dostępności, które są używane w Azure SQL Database:

- Standardowy model dostępności oparty na rozdzieleniu zasobów obliczeniowych i magazynu.  Zależy to od wysokiej dostępności i niezawodności zdalnej warstwy magazynowania. Ta architektura ukierunkowana na aplikacje biznesowe zorientowane na budżet, które mogą tolerować spadek wydajności podczas aktywności konserwacyjnej.
- Model dostępności Premium oparty na klastrze procesów aparatu bazy danych. Opiera się to na faktach, że zawsze jest kworum dostępnych węzłów aparatu bazy danych. Ta architektura ukierunkowana na aplikacje o znaczeniu strategicznym o wysokiej wydajności operacji we/wy, wysoka liczba transakcji i gwarantuje minimalny wpływ na wydajność podczas aktywności konserwacyjnej.

Azure SQL Database jest uruchamiany w najnowszej stabilnej wersji aparatu bazy danych SQL Server i systemu operacyjnego Windows, a większość użytkowników nie zauważy, że uaktualnienia są wykonywane w sposób ciągły.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Dostępność warstwy usługi Basic, standard i Ogólnego przeznaczenia

Te warstwy usług wykorzystują standardową architekturę dostępności. Poniższy rysunek przedstawia cztery różne węzły z oddzielnymi warstwami obliczeniowymi i magazynowymi.

![Rozdzielenie zasobów obliczeniowych i magazynu](media/sql-database-high-availability/general-purpose-service-tier.png)

Standardowy model dostępności obejmuje dwie warstwy:

- Warstwa obliczeń bezstanowych, która uruchamia proces `sqlservr.exe` i zawiera tylko dane przejściowe i buforowane, takie jak TempDB, modeluje bazy danych na dołączonym dysku SSD, oraz pamięć podręczną planu, pulę buforów i pulę magazynu kolumn w pamięci. Ten bezstanowy węzeł jest obsługiwany przez usługę Azure Service Fabric, która inicjuje `sqlservr.exe`, steruje kondycją węzła i wykonuje przejście w tryb failover do innego węzła w razie potrzeby.
- Warstwa danych stanowych z plikami bazy danych (. mdf/. ldf), które są przechowywane w usłudze Azure Blob Storage. Usługa Azure Blob Storage ma wbudowaną funkcję dostępności i nadmiarowości danych. Gwarantuje, że każdy rekord w pliku dziennika lub stronie w pliku danych zostanie zachowany nawet w przypadku awarii procesu SQL Server.

Za każdym razem, gdy aparat bazy danych lub system operacyjny zostanie uaktualniony lub zostanie wykryta awaria, usługa Azure Service Fabric przeniesie bezstanowy proces SQL Server do innego bezstanowego węzła obliczeniowego z wystarczającą ilością wolnego miejsca. Przeniesienie nie ma wpływ na dane w usłudze Azure Blob Storage, a pliki danych/dziennika są dołączone do nowo zainicjowanego procesu SQL Server. Ten proces gwarantuje dostępność na 99,99%, ale duże obciążenie może napotkać spadek wydajności podczas przejścia, ponieważ nowe wystąpienie SQL Server rozpoczyna się od zimnej pamięci podręcznej.

## <a name="premium-and-business-critical-service-tier-availability"></a>Dostępność warstwy usług premium i Krytyczne dla działania firmy

Warstwy usług premium i Krytyczne dla działania firmy korzystają z modelu dostępności Premium, który integruje zasoby obliczeniowe (proces SQL Server Database Engine) i magazyn (lokalnie dołączony dysk SSD) w jednym węźle. Wysoka dostępność jest osiągana przez replikowanie zarówno zasobów obliczeniowych, jak i magazynu do dodatkowych węzłów tworzących klaster z trzema czterema węzłami. 

![Klaster węzłów aparatu bazy danych](media/sql-database-high-availability/business-critical-service-tier.png)

Bazowe pliki bazy danych (. mdf/. ldf) są umieszczane w podłączonym magazynie SSD w celu zapewnienia bardzo małych opóźnień we/wy dla obciążenia. Wysoka dostępność jest implementowana przy użyciu technologii podobnego do SQL Server [zawsze włączonymi grupami dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Klaster zawiera pojedynczą replikę podstawową (proces SQL Server), która jest dostępna do obsługi obciążeń klientów odczytu i zapisu, a także do trzech replik pomocniczych (obliczeniowych i magazynowych) zawierających kopie danych. Węzeł podstawowy ciągle przekazuje zmiany do węzłów pomocniczych w kolejności i gwarantuje, że dane są synchronizowane z co najmniej jedną repliką pomocniczą przed zatwierdzeniem każdej transakcji. Ten proces gwarantuje, że jeśli węzeł podstawowy ulegnie awarii z jakiegokolwiek powodu, zawsze jest w pełni zsynchronizowany węzeł w celu przełączenia w tryb failover. Przełączenie w tryb failover jest inicjowane przez Service Fabric platformy Azure. Gdy replika pomocnicza zostanie nowym węzłem podstawowym, zostanie utworzona inna replika pomocnicza, aby upewnić się, że klaster ma wystarczającą liczbę węzłów (zestaw kworum). Po zakończeniu pracy w trybie failover połączenia SQL są automatycznie przekierowywane do nowego węzła podstawowego.

Dodatkowo model dostępności Premium umożliwia przekierowywanie połączeń SQL tylko do odczytu do jednej z replik pomocniczych. Ta funkcja jest nazywana [skalowaniem do odczytu](sql-database-read-scale-out.md). Zapewnia 100% dodatkowej pojemności obliczeniowej bez dodatkowej opłaty za magazyn operacji tylko do odczytu, na przykład obciążenia analityczne, z repliki podstawowej.

## <a name="hyperscale-service-tier-availability"></a>Dostępność warstwy usługi w ramach skalowania

Architektura warstwy usług w ramach skalowania jest opisana w temacie [Architektura funkcji rozproszonych](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture). 

![Skalowalna architektura funkcjonalna](./media/sql-database-hyperscale/hyperscale-architecture.png)

Model dostępności w obszarze skalowanie obejmuje cztery warstwy:

- Bezstanowa warstwa obliczeniowa, która uruchamia procesy `sqlservr.exe` i zawiera tylko dane przejściowe i buforowane, takie jak nieobejmujący pamięci podręcznej RBPEX, TempDB, Modeling Database itp. na dołączonym dysku SSD, i planowanie pamięci podręcznej, puli buforów i puli magazynu kolumn w pamięci. Ta warstwa bezstanowa obejmuje podstawową replikę obliczeniową i opcjonalnie wiele pomocniczych replik obliczeniowych, które mogą być używane jako obiekty docelowe trybu failover.
- Bezstanowa warstwa magazynowania utworzona przez serwery stronicowania. Ta warstwa jest aparatem magazynu rozproszonego dla procesów `sqlservr.exe` uruchomionych w replikach obliczeniowych. Każdy serwer stron zawiera tylko dane przejściowe i buforowane, takie jak m.in. pamięć podręczna RBPEX na podłączonym dysku SSD, a strony danych w pamięci podręcznej. Każdy serwer stron ma sparowany serwer stronicowania w konfiguracji aktywne-aktywne, aby zapewnić Równoważenie obciążenia, nadmiarowość i wysoką dostępność.
- Warstwa magazynu dziennika transakcji stanowych utworzona przez węzeł obliczeniowy, na którym działa proces usługi log, strefa przeładunku dziennika transakcji oraz magazyn długoterminowy dziennika transakcji. Strefa docelowa i długoterminowy magazyn używają usługi Azure Storage, która zapewnia dostępność i [nadmiarowość](https://docs.microsoft.com/azure/storage/common/storage-redundancy) dla dziennika transakcji, zapewniając trwałość danych dla zatwierdzonych transakcji.
- Stanowa warstwa magazynowania danych z plikami bazy danych (. mdf/. NDF), które są przechowywane w usłudze Azure Storage i są aktualizowane przez serwery stronicowania. Ta warstwa używa funkcji dostępności i [nadmiarowości](https://docs.microsoft.com/azure/storage/common/storage-redundancy) danych usługi Azure Storage. Gwarantuje to, że każda Strona w pliku danych zostanie zachowana, nawet jeśli procesy w innych warstwach uległy awarii architektury ze skalą lub w przypadku awarii węzłów obliczeniowych.

Węzły obliczeniowe we wszystkich warstwach skalowania są uruchamiane na platformie Azure Service Fabric, która kontroluje kondycję każdego węzła i wykonuje przejścia w tryb failover do dostępnych węzłów w dobrej kondycji.

Aby uzyskać więcej informacji na temat wysokiej dostępności w ramach skalowania, zobacz [wysoka dostępność bazy danych w ramach skalowania](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale).

## <a name="zone-redundant-configuration"></a>Konfiguracja nadmiarowa stref

Domyślnie klaster węzłów dla modelu dostępności Premium jest tworzony w tym samym centrum danych. Wprowadzając [strefy dostępności platformy Azure](../availability-zones/az-overview.md), SQL Database mogą umieścić różne repliki bazy danych krytyczne dla działania firmy w różnych strefach dostępności w tym samym regionie. Aby wyeliminować single point of failure, pierścień kontrolny jest również duplikowany w wielu strefach jako trzy pierścienie bramy (GW). Routing do określonego pierścienia bramy jest kontrolowany przez [usługę Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Ponieważ konfiguracja nadmiarowa strefy w warstwach usług premium lub Krytyczne dla działania firmy nie powoduje utworzenia dodatkowej nadmiarowości bazy danych, możesz ją włączyć bez dodatkowych kosztów. Wybierając strefowo nadmiarową konfigurację, można sprawić, aby bazy danych Premium lub Krytyczne dla działania firmy odporne na znacznie większy zestaw błędów, w tym katastrofalne przerwy w działaniu, bez wprowadzania żadnych zmian w logice aplikacji. Istnieje również możliwość przekonwertowania wszelkich istniejących baz danych lub pul w warstwie Premium lub Krytyczne dla działania firmy na strefę nadmiarową.

Ze względu na to, że nadmiarowe bazy danych strefy mają repliki w różnych centrach, z odległości między nimi, zwiększone opóźnienie sieci może wydłużyć czas zatwierdzania i w ten sposób mieć wpływ na wydajność niektórych obciążeń OLTP. Zawsze możesz wrócić do konfiguracji pojedynczej strefy, wyłączając ustawienie nadmiarowości strefy. Ten proces jest operacją online podobną do zwykłego uaktualnienia warstwy usług. Na końcu procesu baza danych lub Pula jest migrowana ze strefy nadmiarowego pierścień do pojedynczego pierścienia strefy lub odwrotnie.

> [!IMPORTANT]
> Nadmiarowe bazy danych stref i pule elastyczne są obecnie obsługiwane tylko w warstwach usług premium i Krytyczne dla działania firmy w wybranych regionach. W przypadku korzystania z warstwy Krytyczne dla działania firmy konfiguracja nadmiarowa strefy jest dostępna tylko po wybraniu sprzętu obliczeniowego 5 rdzeń. Aby uzyskać aktualne informacje o regionach, które obsługują nadmiarowe bazy danych strefy, zobacz temat [Obsługa usług według regionów](../availability-zones/az-overview.md#services-support-by-region).  
> Ta funkcja jest niedostępna w wystąpieniu zarządzanym.

Strefa o wysokiej dostępności nadmiarowa jest zilustrowana na poniższym diagramie:

![Strefa architektury wysokiej dostępności nadmiarowa](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Szybsze odzyskiwanie bazy danych (ADR)

[Szybsze odzyskiwanie bazy danych (ADR)](sql-database-accelerated-database-recovery.md) to nowa funkcja aparatu bazy danych SQL, która znacznie zwiększa dostępność bazy danych, szczególnie w przypadku długotrwałych transakcji. Reguły ADR są obecnie dostępne dla pojedynczych baz danych, pul elastycznych i Azure SQL Data Warehouse.

## <a name="testing-application-fault-resiliency"></a>Testowanie odporności błędów aplikacji

Wysoka dostępność to podstawowa część platformy Azure SQL Database, która działa w sposób przezroczysty dla aplikacji bazy danych. Jednak firma Microsoft rozpoznaje, że można testować, w jaki sposób automatyczne operacje trybu failover inicjowane podczas planowanych lub nieplanowanych zdarzeń byłyby wpływać na aplikację przed wdrożeniem jej w środowisku produkcyjnym. Można wywołać specjalny interfejs API w celu ponownego uruchomienia bazy danych lub puli elastycznej, która spowoduje wyzwolenie pracy w trybie failover. W przypadku strefowo nadmiarowej bazy danych lub puli elastycznej wywołanie interfejsu API spowoduje przekierowanie połączeń klientów do nowego elementu podstawowego w strefie dostępności innej niż strefa dostępności starego elementu podstawowego. W związku z tym oprócz testowania pracy w trybie failover wpływa na istniejące sesje baz danych, można również sprawdzić, czy zmienia ona kompleksową wydajność ze względu na zmiany opóźnienia sieci. Ponieważ operacja ponownego uruchomienia jest niepożądana, a duża liczba z nich może nałożyć na platformę, tylko jedno wywołanie trybu failover jest dozwolone co 30 minut dla każdej bazy danych lub puli elastycznej. 

Przejście w tryb failover można zainicjować za pomocą interfejsu API REST lub programu PowerShell. W przypadku interfejsu API REST zobacz [tryb failover bazy danych](https://docs.microsoft.com/rest/api/sql/databases(failover)/failover) i [tryb failover puli elastycznej](https://docs.microsoft.com/rest/api/sql/elasticpools(failover)/failover). W przypadku programu PowerShell zobacz [Invoke-AzSqlDatabaseFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqldatabasefailover) i [Invoke-AzSqlElasticPoolFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlelasticpoolfailover). Wywołania interfejsu API REST można także wykonywać z interfejsu wiersza polecenia platformy Azure przy użyciu poleceń [AZ REST](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-rest) .

> [!IMPORTANT]
> Polecenie przełączenia w tryb failover nie jest obecnie dostępne w warstwie usługi i w przypadku wystąpienia zarządzanego.

## <a name="conclusion"></a>Podsumowanie

Azure SQL Database funkcje wbudowanego rozwiązania wysokiej dostępności, które jest głęboko zintegrowane z platformą Azure. Jest ona zależna od Service Fabric do wykrywania awarii i odzyskiwania, w usłudze Azure Blob Storage na potrzeby ochrony danych, a na Strefy dostępności w celu uzyskania większej odporności na uszkodzenia. Ponadto usługa Azure SQL Database korzysta z technologii Always On Availability Group w SQL Server na potrzeby replikacji i przełączania do trybu failover. Połączenie tych technologii pozwala aplikacjom w pełni wykorzystać zalety modelu magazynu mieszanego i obsługiwać najbardziej wymaganą umowy SLA.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [strefy dostępności platformy Azure](../availability-zones/az-overview.md)
- Dowiedz się więcej o [Service Fabric](../service-fabric/service-fabric-overview.md)
- Dowiedz się więcej o [usłudze Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
- Aby uzyskać więcej opcji dotyczących wysokiej dostępności i odzyskiwania po awarii, zobacz [ciągłość działania firmy](sql-database-business-continuity.md)
