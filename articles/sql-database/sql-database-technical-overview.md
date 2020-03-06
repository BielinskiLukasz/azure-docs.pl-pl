---
title: Co to jest usługa Azure SQL Database?
description: 'Zapoznaj się z wprowadzeniem do SQL Database: szczegóły techniczne i możliwości systemu zarządzania relacyjnymi bazami danych firmy Microsoft (RDBMS) w chmurze.'
keywords: wprowadzenie do usługi sql, co to jest sql database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/08/2019
ms.openlocfilehash: 3932c22ff003a343e4c32aee117a7ddea922fbdb
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360084"
---
# <a name="what-is-the-azure-sql-database-service"></a>Co to jest usługa Azure SQL Database?

Azure SQL Database to relacyjna baza danych ogólnego przeznaczenia, udostępniana jako usługa zarządzana. Dzięki niej można utworzyć warstwę magazynu danych o wysokiej dostępności i o wysokiej wydajności dla aplikacji i rozwiązań na platformie Azure. SQL Database może być właściwym wyborem dla różnych nowoczesnych aplikacji w chmurze, ponieważ umożliwia przetwarzanie zarówno danych relacyjnych, jak i [nierelacyjnych struktur](sql-database-multi-model-features.md), takich jak wykresy, JSON, przestrzenne i XML.

Jest on oparty na najnowszej stabilnej wersji [aparatu bazy danych Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json). Można korzystać z zaawansowanych funkcji przetwarzania zapytań, takich jak [technologie w pamięci o wysokiej wydajności](sql-database-in-memory.md) i [inteligentne przetwarzanie zapytań](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json). W rzeczywistości najnowsze możliwości SQL Server są najpierw wydane do SQL Database, a dopiero SQL Server. Uzyskasz najnowsze możliwości SQL Server bez narzutu na stosowanie poprawek lub uaktualnianie, przetestowane przez miliony baz danych. 

SQL Database umożliwia łatwe definiowanie i skalowanie wydajności w ramach dwóch różnych modeli zakupów: [modelu zakupu opartego na rdzeń wirtualny](sql-database-service-tiers-vcore.md) i [modelu zakupu opartego](sql-database-service-tiers-dtu.md)na jednostkach DTU. SQL Database to w pełni zarządzana usługa, która ma wbudowaną wysoką dostępność, kopie zapasowe i inne typowe operacje konserwacyjne. Firma Microsoft obsługuje wszystkie poprawki i aktualizacje kodu SQL i systemu operacyjnego. Nie trzeba zarządzać podstawową infrastrukturą.

> [!NOTE]
> Aby zapoznać się z odpowiednimi postanowieniami i ich definicjami, zobacz [słownik terminów SQL Database](sql-database-glossary-terms.md).

## <a name="deployment-models"></a>Modele wdrażania

Usługa Azure SQL Database udostępnia następujące opcje wdrażania na potrzeby bazy danych Azure SQL Database:

![Diagram opcji wdrażania](./media/sql-database-technical-overview/deployment-options.png)

- [Pojedyncza baza danych](sql-database-single-database.md) reprezentuje w pełni zarządzaną, izolowaną bazę danych. Możesz użyć tej opcji, jeśli masz nowoczesne aplikacje i mikrousługi w chmurze, które wymagają pojedynczego wiarygodnego źródła danych. Pojedyncza baza danych jest podobna do [zawartej bazy danych](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) w [aparacie Microsoft SQL Server Database](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json).
- [Wystąpienie zarządzane](sql-database-managed-instance.md) jest w pełni zarządzanym wystąpieniem [aparatu bazy danych Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json). Zawiera zestaw baz danych, które mogą być używane razem. Ta opcja umożliwia łatwe Migrowanie lokalnych baz danych SQL Server do chmury platformy Azure, a także dla aplikacji, które muszą korzystać z funkcji bazy danych, które zapewnia SQL Server aparat bazy danych.
- [Elastyczna Pula](sql-database-elastic-pool.md) to zbiór [pojedynczych baz danych](sql-database-single-database.md) z współdzielonym zestawem zasobów, takich jak procesor CPU lub pamięć. Pojedyncze bazy danych można przenieść do i z puli elastycznej.

> [!IMPORTANT]
> Aby zrozumieć różnice między funkcjami SQL Database i SQL Server, a także różnice między różnymi Azure SQL Database opcjami wdrażania, zobacz temat [funkcje SQL Database](sql-database-features.md).

SQL Database zapewnia przewidywalną wydajność z wieloma typami zasobów, warstwami usług i rozmiarami obliczeniowymi. Zapewnia dynamiczną skalowalność bez przestojów, wbudowaną inteligentną optymalizację, globalną skalowalność i dostępność oraz zaawansowane opcje zabezpieczeń. Te funkcje umożliwiają skoncentrowanie się na szybkim tworzeniu aplikacji i skróceniu czasu wprowadzenia na rynek, a nie na zarządzaniu maszynami wirtualnymi i infrastrukturą. Usługa SQL Database jest obecnie dostępna w 38 centrach danych na całym świecie, dzięki czemu można uruchamiać bazę w centrum w pobliżu użytkownika.

## <a name="scalable-performance-and-pools"></a>Skalowalna wydajność i pule

Można zdefiniować przypisaną ilość zasobów. 
- W przypadku pojedynczych baz danych każda baza danych jest odizolowana od innych i przenośna. Każdy z nich ma swoją własną gwarantowaną ilość zasobów obliczeniowych, pamięci i magazynu. Ilość zasobów przypisanych do bazy danych jest przeznaczona dla tej bazy danych i nie jest współdzielona z innymi bazami danych na platformie Azure. Możesz dynamicznie [skalować zasoby pojedynczej bazy danych](sql-database-single-database-scale.md) w górę i w dół. Opcja pojedynczej bazy danych zapewnia różne zasoby obliczeniowe, pamięć i magazyn dla różnych potrzeb. Można na przykład uzyskać od 1 do 80 rdzeni wirtualnych lub 32 GB do 4 TB. [Warstwa usługi](sql-database-service-tier-hyperscale.md) dla jednej bazy danych umożliwia skalowanie do 100 TB przy użyciu funkcji szybkiego tworzenia kopii zapasowych i przywracania.
- W przypadku pul elastycznych można przypisywać zasoby, które są współużytkowane przez wszystkie bazy danych w puli. Można utworzyć nową bazę danych lub przenieść istniejące pojedyncze bazy danych do puli zasobów, aby zmaksymalizować wykorzystanie zasobów i zaoszczędzić pieniądze. Ta opcja zapewnia również możliwość dynamicznego [skalowania zasobów puli elastycznej](sql-database-elastic-pool-scale.md) w górę i w dół.
- Z wystąpieniami zarządzanymi każde wystąpienie zarządzane jest odizolowane od innych wystąpień z gwarantowanymi zasobami. W wystąpieniu zarządzanym bazy danych wystąpienia korzystają z zestawu zasobów. [Zasoby wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md) można dynamicznie skalować w górę i w dół.

Możesz utworzyć swoją pierwszą aplikację w małej, pojedynczej bazie danych przy niskich kosztach miesięcznie w warstwie usług ogólnego przeznaczenia. Następnie można zmienić warstwę usługi ręcznie lub programowo w dowolnym momencie do warstwy usługi o krytycznym znaczeniu dla firmy, aby zaspokoić potrzeby rozwiązania. Wydajność można dostosować bez przestoju aplikacji i przerwy w świadczeniu usługi klientom. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów. Płacisz tylko za zasoby, których potrzebujesz, gdy będą potrzebne.

*Dynamiczna skalowalność* różni się od *skalowania automatycznego*. O skalowaniu automatycznym mówimy, gdy usługa jest skalowana automatycznie na podstawie kryteriów, natomiast dynamiczna skalowalność uwzględnia skalowanie ręczne bez przestojów. Opcja pojedynczej bazy danych obsługuje ręczną dynamiczną skalowalność, ale nie Skalowanie automatyczne. Aby zapewnić bardziej automatyczne środowisko, należy rozważyć użycie pul elastycznych, które umożliwiają współużytkowanie zasobów w puli na podstawie indywidualnych potrzeb baz danych. Innym rozwiązaniem jest użycie skryptów, które mogą pomóc zautomatyzować skalowalność dla pojedynczej bazy danych. Aby zapoznać się z przykładem, zobacz [Używanie programu PowerShell do monitorowania i skalowania pojedynczej bazy danych](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Modele zakupów

SQL Database oferuje następujące modele zakupów:
- [Model zakupu oparty na rdzeń wirtualny](sql-database-service-tiers-vcore.md) umożliwia wybranie liczby rdzeni wirtualnych, ilości pamięci oraz ilości i szybkości magazynu. Model zakupu oparty na rdzeń wirtualny umożliwia również korzystanie z [Korzyść użycia hybrydowego platformy Azure SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) w celu uzyskania oszczędności kosztów. Aby uzyskać więcej informacji na temat Korzyść użycia hybrydowego platformy Azure, zobacz sekcję "często zadawane pytania" w dalszej części tego artykułu.
- [Model zakupu oparty na](sql-database-service-tiers-dtu.md) jednostkach DTU oferuje mieszankę zasobów obliczeniowych, pamięci i operacji we/wy w trzech warstwach usług, co pozwala na obsługę lekkich obciążeń związanych z bazami danych. Rozmiary obliczeniowe w poszczególnych warstwach zapewniają inną kombinację tych zasobów, do których można dodać dodatkowe zasoby magazynu.
- [Model bezserwerowy](sql-database-serverless.md) automatycznie skaluje obliczenia na podstawie zapotrzebowania na obciążenia i rachunki dla ilości użytych obliczeń na sekundę. Warstwa obliczeniowa bezserwerowa również automatycznie wstrzymuje bazy danych w trakcie okresów nieaktywnych, gdy są naliczane opłaty za magazyn, a następnie automatycznie wznawiają bazy danych po powrocie działania.

### <a name="service-tiers"></a>Warstwy usług

Azure SQL Database oferuje trzy warstwy usług zaprojektowane dla różnych typów aplikacji:
- [Ogólnego przeznaczenia/standardowa](sql-database-service-tier-general-purpose.md) warstwa usług zaprojektowana pod kątem typowych obciążeń. Oferuje zorientowane na budżety Opcje obliczeniowe i magazynowe.
- Warstwa usługi [krytyczne dla działania firmy/Premium](sql-database-service-tier-business-critical.md) zaprojektowana dla aplikacji OLTP z wysoką szybkością transakcji i operacjami we/wy o najniższym opóźnieniu. Zapewnia największą odporność na błędy przy użyciu kilku izolowanych replik.
- Warstwa usługi do [skalowania](sql-database-service-tier-hyperscale.md) dla bardzo dużych baz danych OLTP oraz możliwość automatycznego skalowania magazynu i skalowania w poziomie płynnie. 

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Pule elastyczne umożliwiające zmaksymalizowanie wykorzystania zasobów

W przypadku wielu firm i aplikacji możliwość tworzenia pojedynczych baz danych i dostosowywania wydajności na żądanie jest wystarczająca, zwłaszcza gdy wzorce użycia są względnie przewidywalne. Nieprzewidywalne wzorce użycia mogą utrudniać zarządzanie kosztami i modelem biznesowym. [Pule elastyczne](sql-database-elastic-pool.md) zostały zaprojektowane, aby rozwiązać ten problem. Zasoby wydajności są przydzielane do puli, a nie do pojedynczej bazy danych. Płacisz za zbiorowe zasoby wydajności puli, a nie na wydajność pojedynczej bazy danych.

   ![Ilustracja przedstawiająca pule elastyczne w wersjach Basic, standard i Premium](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Pule elastyczne nie wymagają koncentrowania się na skalowaniu wydajności bazy danych w górę i w dół w miarę zmian zapotrzebowania na zasoby. Bazy danych w puli używają zasobów wydajności puli elastycznej w miarę potrzeb. Bazy danych w puli zużywają zasoby, ale nie przekraczają limitów puli, więc koszt pozostaje przewidywalny nawet wtedy, gdy nie można przewidzieć użycia poszczególnych baz danych.

Możesz [dodawać i usuwać bazy danych do puli](sql-database-elastic-pool-manage-portal.md), skalować aplikację z kilku baz danych do tysięcy, a wszystko to w ramach budżetu, który kontrolujesz. Można również kontrolować minimalną i maksymalną liczbę zasobów dostępnych dla baz danych w puli, aby upewnić się, że żadna baza danych w puli nie używa wszystkich zasobów puli, a każda baza danych w puli ma gwarantowaną minimalną ilość zasobów. Aby dowiedzieć się więcej o wzorcach projektowych dla aplikacji SaaS (Software as a Service) korzystających z pul elastycznych, zobacz [wzorce projektowe dla wielodostępnych aplikacji SaaS z SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Skrypty mogą ułatwić monitorowanie i skalowanie elastycznych pul baz danych. Aby zapoznać się z przykładem, zobacz temat [Używanie programu PowerShell do monitorowania i skalowania elastycznej puli SQL w Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md).

> [!IMPORTANT]
> Wystąpienie zarządzane nie obsługuje pul elastycznych. Zamiast tego wystąpienie zarządzane jest zbiorem baz danych wystąpień, które współużytkują zasoby wystąpienia zarządzanego.

### <a name="blend-single-databases-with-pooled-databases"></a>Mieszaj pojedyncze bazy danych z bazami danych w puli

Możesz mieszać pojedyncze bazy danych z pulami elastycznymi i zmieniać warstwy usług pojedynczych baz danych i pul elastycznych, aby dostosować je do swojej sytuacji. Możesz również mieszać inne usługi platformy Azure i dopasować je do SQL Database, aby sprostać unikatowym potrzebom związanym z projektowaniem aplikacji, zwiększyć efektywność kosztów i zasobów oraz odblokować nowe możliwości biznesowe.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Możliwości rozbudowanego monitorowania i zgłaszania alertów

Azure SQL Database zapewnia zaawansowane funkcje monitorowania i rozwiązywania problemów, które ułatwiają uzyskiwanie dokładniejszych informacji o cechach obciążenia. Te funkcje i narzędzia obejmują:
 - Wbudowane funkcje monitorowania dostępne w najnowszej wersji aparatu bazy danych SQL Server Database. Umożliwiają one znalezienie szczegółowych informacji o wydajności w czasie rzeczywistym. 
 - PaaS funkcje monitorowania zapewniane przez platformę Azure, które umożliwiają monitorowanie i rozwiązywanie problemów z dużą liczbą wystąpień bazy danych.

[Magazyn zapytań](sql-database-operate-query-store.md), wbudowana funkcja monitorowania SQL Server, rejestruje wydajność zapytań w czasie rzeczywistym i pozwala identyfikować potencjalne problemy z wydajnością oraz najważniejszych użytkowników zasobów. Dostrajanie automatyczne i zalecenia zawierają porady dotyczące zapytań dotyczących wydajności uległa pogorszeniu oraz brakujących lub zduplikowanych indeksów. Dostrajanie automatyczne w SQL Database umożliwia ręczne zastosowanie skryptów, które mogą rozwiązać problemy, lub pozwól SQL Database zastosować poprawkę. SQL Database może również testować i sprawdzać, czy poprawka zapewnia pewne korzyści, i zachowuje lub przywraca zmianę w zależności od wyniku. Oprócz funkcji magazynu zapytań i dostrajania automatycznego, można użyć standardowych [widoków DMV i systemu XEvent](sql-database-monitoring-with-dmvs.md) do monitorowania wydajności obciążeń.

Platforma Azure udostępnia wbudowane narzędzia do monitorowania i [generowania alertów](sql-database-insights-alerts-portal.md) [wydajności](sql-database-performance.md) połączone z ocenami wydajności, które umożliwiają monitorowanie stanu tysięcy baz danych. Za pomocą tych narzędzi możesz szybko ocenić wpływ skalowania w górę lub w dół na podstawie bieżących lub przewidywanych wymagań dotyczących wydajności. Ponadto usługa SQL Database może [tworzyć metryki i dzienniki diagnostyczne](sql-database-metrics-diag-logging.md), które ułatwiają monitorowanie. Usługę SQL Database można skonfigurować do przechowywania danych dotyczących użycia zasobów, pracowników i sesji oraz połączeń z jednym z następujących zasobów platformy Azure:

- **Usługa Azure Storage**: w celu archiwizowania ogromnych ilości danych telemetrycznych za niewielką cenę.
- **Event Hubs platformy Azure**: w celu integracji telemetrii SQL Database z niestandardowym rozwiązaniem monitorowania lub potokami aktywnymi.
- **Dzienniki Azure monitor**: dla wbudowanego rozwiązania do monitorowania z raportowaniem, alertami i możliwościami ograniczającymi.

![Diagram architektury monitorowania platformy Azure](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Możliwości dostępności

W tradycyjnych środowiskach SQL Server zazwyczaj skonfigurowano co najmniej dwa komputery lokalnie. Te maszyny mają dokładne i synchronicznie utrzymywane kopie danych w celu ochrony przed awarią pojedynczego komputera lub składnika. To środowisko zapewnia wysoką dostępność, ale nie chroni przed klęską żywiołową, która niszczy Twoje centrum danych.

Odzyskiwanie awaryjne polega na tym, że krytyczne zdarzenie jest wystarczająco geograficznie lokalizowane, aby można było korzystać z innej maszyny lub zestawu maszyn z kopią danych. W SQL Server można używać zawsze włączonych grup dostępności działających w trybie Async, aby uzyskać tę możliwość. Osoby często nie chcą czekać na odczekanie na replikację przed zatwierdzeniem transakcji, dzięki czemu istnieje możliwość utraty danych w przypadku nieplanowanego przejścia w tryb failover.

Bazy danych w warstwach usługi krytycznej Premium i Business [mają już coś podobne](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) do synchronizacji grupy dostępności. Bazy danych w mniejszych warstwach usług zapewniają nadmiarowość za pośrednictwem magazynu przy użyciu [innego, ale równoważnego mechanizmu](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability). Wbudowana logika pomaga chronić przed awarią pojedynczej maszyny. Aktywna funkcja replikacji geograficznej zapewnia możliwość ochrony przed awarią, w której cały region jest niszczony.

Strefy dostępności platformy Azure próbuje chronić przed awarią pojedynczego budynku centrum danych w jednym regionie. Pomaga chronić przed utratą mocy lub sieci do budynku. W SQL Database należy umieścić różne repliki w różnych strefach dostępności (różne budynki, efektywnie).

W rzeczywistości umowa dotycząca poziomu usług [(SLA)](https://azure.microsoft.com/support/legal/sla/) platformy Azure, obsługiwana przez globalną sieć centrów danych zarządzanych przez firmę Microsoft, pomaga zapewnić, że aplikacja działa 24/7. Platforma Azure w pełni zarządza każdą bazą danych i nie gwarantuje utraty danych i dużej wartości procentowej dostępności danych. Platforma Azure automatycznie obsługuje stosowanie poprawek, kopii zapasowych, replikacji, wykrywania awarii, bazowego potencjalnego sprzętu, błędów oprogramowania lub sieci, wdrażania poprawek błędów, przełączeń do trybu failover, uaktualnień baz danych i innych zadań konserwacyjnych. Dostępność w warstwie Standardowa jest osiągana przez oddzielenie warstw obliczeń i magazynu. Dostępność w warstwie Premium uzyskuje się przez integrację zasobów obliczeniowych i magazynu w jednym węźle w celu uzyskania wydajności, a następnie zaimplementowanie technologii podobnej do zawsze dostępnych grup dostępności. Pełne omówienie możliwości wysokiej dostępności Azure SQL Database można znaleźć w temacie [SQL Database Availability (dostępność](sql-database-high-availability.md)). 

Ponadto SQL Database zapewnia wbudowaną [ciągłość biznesową i globalne funkcje skalowalności](sql-database-business-continuity.md) . Należą do nich:

- [Automatyczne kopie zapasowe](sql-database-automated-backups.md):

  SQL Database automatycznie wykonuje pełne, różnicowe i transakcyjne kopie zapasowe dzienników baz danych SQL, aby umożliwić przywracanie do dowolnego punktu w czasie. W przypadku pojedynczych baz danych i baz danych w puli można skonfigurować SQL Database do przechowywania pełnych kopii zapasowych bazy danych w usłudze Azure Storage na potrzeby długoterminowego przechowywania kopii zapasowych. W przypadku wystąpień zarządzanych można także wykonywać kopie zapasowe tylko do kopiowania na potrzeby długoterminowego przechowywania w usłudze Backup.

- [Przywracanie do punktu w czasie](sql-database-recovery-using-backups.md):

  Wszystkie SQL Database opcje wdrażania obsługują odzyskiwanie do dowolnego punktu w czasie w okresie przechowywania automatycznego tworzenia kopii zapasowej dla dowolnej bazy danych SQL.
- [Aktywna replikacja geograficzna](sql-database-active-geo-replication.md):

  Opcje pojedynczej bazy danych i baz danych w puli umożliwiają skonfigurowanie maksymalnie czterech pomocniczych baz danych w ramach tych samych lub globalnie rozproszonych centrów usługi Azure datasites. Na przykład jeśli masz aplikację SaaS z bazą danych wykazu, która ma dużą ilość współbieżnych transakcji tylko do odczytu, użyj aktywnej replikacji geograficznej, aby włączyć globalną skalę odczytu. Spowoduje to usunięcie wąskich gardeł na podstawowym, które są spowodowane przez odczyt obciążeń. W przypadku wystąpień zarządzanych należy użyć grup z obsługą trybu failover.
- [Grupy autotrybu failover](sql-database-auto-failover-group.md):

  Wszystkie SQL Database opcje wdrażania umożliwiają korzystanie z grup trybu failover w celu zapewnienia wysokiej dostępności i równoważenia obciążenia na skalę globalną. Obejmuje to przezroczystą replikację geograficzną i tryb failover dużych zestawów baz danych, pul elastycznych i wystąpień zarządzanych. Grupy trybu failover umożliwiają tworzenie globalnie dystrybuowanych aplikacji SaaS przy minimalnym obciążeniu administracyjnym. Spowoduje to pozostawienie całej złożonej aranżacji monitorowania, routingu i przejścia w tryb failover do SQL Database.
- [Nadmiarowe bazy danych stref](sql-database-high-availability.md):

  SQL Database pozwala na udostępnianie krytycznych baz danych Premium lub Business lub pul elastycznych w wielu strefach dostępności. Ponieważ te bazy danych i pule elastyczne mają wiele nadmiarowych replik w celu zapewnienia wysokiej dostępności, umieszczenie tych replik w wielu strefach dostępności zapewnia wyższą odporność. Obejmuje to możliwość automatycznego odzyskania z niepowodzeń skalowania centrów danych.

## <a name="built-in-intelligence"></a>Wbudowane narzędzie analizy

Korzystając z SQL Database, uzyskasz wbudowaną analizę, która pomaga znacznie ograniczyć koszty uruchamiania i zarządzania bazami danych oraz zmaksymalizować wydajność i bezpieczeństwo aplikacji. W przypadku uruchamiania milionów obciążeń klientów wokół zegara SQL Database zbiera i przetwarza ogromną ilość danych telemetrycznych, a także w pełni poszanowanie prywatności klientów. Różne algorytmy stale oceniają dane telemetryczne, aby usługa mogła nauczyć się i dostosować ją do swojej aplikacji.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatyczne monitorowanie i dostosowywanie wydajności

Usługa SQL Database udostępnia szczegółowy wgląd w zapytania, których monitorowanie jest wymagane. SQL Database informacje o wzorcach baz danych i umożliwia dostosowanie schematu bazy danych do obciążenia. Usługa SQL Database udostępnia [zalecenia dotyczące dostrajania wydajności](sql-database-advisor.md) pozwalające na przeglądanie i stosowanie akcji dostrajających.

Jednak stałe monitorowanie bazy danych jest żmudnym, szczególnie w przypadku pracy z wieloma bazami danych. [Intelligent Insights](sql-database-intelligent-insights.md) wykonuje to zadanie przez automatyczne monitorowanie SQL Database wydajności w odpowiedniej skali. Informuje ona o problemach z obniżeniem wydajności, określa główną przyczynę każdego problemu i zapewnia zalecenia dotyczące poprawy wydajności, jeśli jest to możliwe.

Zarządzanie ogromną liczbą baz danych może być niemożliwe, nawet ze wszystkimi dostępnymi narzędziami i raportami, które SQL Database i zapewniają platformę Azure. Zamiast monitorowania i dostrajania bazy danych ręcznie można rozważyć delegowanie niektórych działań monitorowania i dostrajania do SQL Database przy użyciu [dostrajania automatycznego](sql-database-automatic-tuning.md). SQL Database automatycznie stosuje zalecenia, testy i weryfikuje każdą z jej akcji dostrajania, aby zapewnić, że wydajność będzie stale ulepszana. W ten sposób SQL Database automatycznie dostosowuje się do obciążenia w kontrolowany i bezpieczny sposób. Automatyczne dostrajanie oznacza, że wydajność bazy danych jest dokładnie monitorowana i porównywana przed i po każdej akcji dostrajania. Jeśli wydajność nie poprawi się, Akcja dostrajania zostanie cofnięta.

Wielu naszych partnerów, którzy uruchamiają [aplikacje SaaS z wieloma dzierżawcami](sql-database-design-patterns-multi-tenancy-saas-applications.md) , w oparciu o SQL Database polegają na automatycznym dostosowywaniu wydajności, aby upewnić się, że ich aplikacje mają zawsze stabilną i przewidywalną wydajność. Z ich punktu widzenia ta funkcja znacznie zmniejsza ryzyko wystąpienia w środku nocy zdarzenia związanego z wydajnością. Ponadto, ze względu na to, że część ich bazy klientów używa również SQL Server, korzystają z tych samych zaleceń dotyczących indeksowania zapewnianych przez SQL Database, które pomagają SQL Server klientom.

[W SQL Database są dostępne](sql-database-automatic-tuning.md)dwa aspekty dostrajania automatycznego:

- **Automatyczne zarządzanie indeksami**: identyfikuje indeksy, które powinny zostać dodane w bazie danych, i indeksy, które powinny zostać z niej usunięte.
- **Automatyczna korekta planu**: identyfikuje plany problematyczne i rozwiązuje problemy z wydajnością planu SQL.

### <a name="adaptive-query-processing"></a>Adaptacyjne przetwarzanie zapytań

Można użyć [adaptacyjnego przetwarzania zapytań](/sql/relational-databases/performance/intelligent-query-processing), w tym z przeplotem w przypadku funkcji z wartościami przechowywanymi w tabeli obejmujących wiele instrukcji, opinii na temat przydziału pamięci w trybie wsadowym i adaptacyjnych sprzężeń w trybie wsadowym. Każda z tych adaptacyjnych funkcji przetwarzania zapytań stosuje podobne techniki „poznaj i dostosuj” ułatwiające dalsze rozwiązywanie problemów dotyczących wydajności związanych z historycznie trudnymi do rozwiązania problemami z optymalizacją zapytań.

## <a name="advanced-security-and-compliance"></a>Zaawansowane zabezpieczenia i zgodność

Usługa SQL Database oferuje szeroką gamę [wbudowanych funkcji zabezpieczeń i zgodności](sql-database-security-overview.md), co ułatwia spełnienie przez aplikację różnych wymagań dotyczących zabezpieczeń i zgodności.

> [!IMPORTANT]
> Firma Microsoft ma certyfikowane Azure SQL Database (wszystkie opcje wdrażania) dotyczące wielu standardów zgodności. Aby uzyskać więcej informacji, zobacz [Centrum zaufania Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), w którym można znaleźć najbardziej aktualną listę SQL Database certyfikatów zgodności.

### <a name="advance-threat-protection"></a>Zaawansowana ochrona przed zagrożeniami

Zaawansowane zabezpieczenia danych to ujednolicony pakiet zaawansowanych funkcji zabezpieczeń SQL. Obejmuje ona funkcje odnajdywania i klasyfikowania poufnych danych, zarządzania lukami w bazie danych i wykrywania nietypowych działań, które mogą wskazywać na zagrożenie dla bazy danych. Zapewnia ona jedną lokalizację do włączania i zarządzania tymi funkcjami.

- [Odnajdywanie i Klasyfikacja danych](sql-database-data-discovery-and-classification.md):

  Ta funkcja udostępnia funkcje wbudowane w Azure SQL Database do odnajdywania, klasyfikowania, etykietowania i ochrony poufnych danych w bazach danych. Zapewnia wgląd w stan klasyfikacji bazy danych i śledzi dostęp do poufnych danych w bazie danych i wykracza poza jego granice.
- [Ocena luk w zabezpieczeniach](sql-vulnerability-assessment.md):

  Ta usługa może wykrywać, śledzić i pomagać w korygowaniu potencjalnych luk w zabezpieczeniach bazy danych. Zapewnia wgląd w stan zabezpieczeń i zapewnia kroki, które można wykonać w celu rozwiązania problemów z zabezpieczeniami oraz zwiększenia bezpieczeństwa bazy danych.
- [Wykrywanie zagrożeń](sql-database-threat-detection.md):

  Ta funkcja wykrywa nietypowe działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do bazy danych lub jej wykorzystania. Stale monitoruje Twoją bazę danych pod kątem podejrzanych działań i zapewnia natychmiastowe alerty zabezpieczeń dotyczące potencjalnych luk w zabezpieczeniach, ataków polegających na wstrzyknięciu kodu SQL oraz anomalii we wzorcach dostępu do bazy danych. Alerty wykrywania zagrożeń zawierają szczegóły podejrzanego działania i zalecają działania dotyczące badania i łagodzenia zagrożeń.

### <a name="auditing-for-compliance-and-security"></a>Inspekcja zgodności i zabezpieczeń

[Inspekcja](sql-database-auditing.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage. Inspekcja pomaga zachować zgodność z przepisami, zrozumieć aktywność bazy danych oraz uzyskać wgląd w niezgodności i anomalie, które mogą wskazywać na problemy biznesowe lub podejrzane naruszenia zabezpieczeń.

### <a name="data-encryption"></a>Szyfrowanie danych

SQL Database pomaga zabezpieczyć dane, zapewniając szyfrowanie. W przypadku danych w ruchu używa protokołu [Transport Layer Security](https://support.microsoft.com/kb/3135244). W przypadku danych przechowywanych przy użyciu [przezroczystego szyfrowania danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Używane dane są [zawsze szyfrowane](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integracja usługi Azure Active Directory z uwierzytelnianiem wieloskładnikowym

Usługa SQL Database umożliwia centralne zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft dzięki funkcji [integracji usługi Azure Active Directory](sql-database-aad-authentication.md). Ta funkcja upraszcza zarządzanie uprawnieniami i zwiększa bezpieczeństwo. Azure Active Directory obsługuje [uwierzytelnianie wieloskładnikowe](sql-database-ssms-mfa-authentication.md) w celu zwiększenia bezpieczeństwa danych i aplikacji, a jednocześnie obsługuje proces logowania jednokrotnego.

## <a name="easy-to-use-tools"></a>Łatwe w użyciu narzędzia

Dzięki usłudze SQL Database tworzenie i konserwowanie aplikacji jest łatwiejsze i bardziej produktywne. Usługa SQL Database pozwala Ci skoncentrować się na tym, co robisz najlepiej: tworzeniu wspaniałych aplikacji. SQL Database można zarządzać i opracowywać przy użyciu narzędzi i umiejętności, które już masz.

- [Azure Portal](https://portal.azure.com/):

  Aplikacja oparta na sieci Web do zarządzania wszystkimi usługami platformy Azure.
- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms):

  Bezpłatna, do pobrania aplikacja kliencka do zarządzania dowolną infrastrukturą SQL, od SQL Server do SQL Database.
- [Narzędzia danych SQL Server w programie Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt):

  Bezpłatna, do pobrania aplikacja kliencka służąca do tworzenia SQL Server relacyjnych baz danych, baz danych SQL, pakietów usług Integration Services, Analysis Services modeli danych i raportów usług Reporting Services.
- [Visual Studio Code](https://code.visualstudio.com/docs):

  Bezpłatny, dostępny do pobrania Edytor kodu typu open source dla systemów Windows, macOS i Linux. Obsługuje ona rozszerzenia, w tym [rozszerzenie MSSQL](https://aka.ms/mssql-marketplace) do wykonywania zapytań dotyczących Microsoft SQL Server, Azure SQL Database i Azure SQL Data Warehouse.

SQL Database obsługuje Kompilowanie aplikacji za pomocą języków Python, Java, Node. js, PHP, Ruby i .NET w systemach macOS, Linux i Windows. Usługa SQL Database obsługuje te same [biblioteki połączeń](sql-database-libraries.md) co program SQL Server.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>SQL Database często zadawane pytania

### <a name="what-is-the-current-version-of-sql-database"></a>Jaka jest bieżąca wersja SQL Database?

Bieżąca wersja SQL Database to V12. Wersja v11 została wycofana.

### <a name="can-i-control-when-patching-downtime-occurs"></a>Czy mogę kontrolować, kiedy występuje poprawka przestoju?

Nie. Skutki zastosowania poprawek zwykle nie są zauważalne, jeśli w aplikacji jest stosowana [logika ponawiania](sql-database-develop-overview.md#resiliency) . Aby uzyskać więcej informacji, zobacz [Planowanie zdarzeń konserwacji platformy Azure w Azure SQL Database](sql-database-planned-maintenance.md).

### <a name="azure-hybrid-benefit-questions"></a>Pytania Korzyść użycia hybrydowego platformy Azure

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Czy istnieją podwójne prawa do korzystania z Korzyść użycia hybrydowego platformy Azure dla SQL Server?

Masz 180 dni od podwójnego prawa do używania licencji, aby zapewnić bezproblemowe działanie migracji. Po upływie tego 180-dniowego okresu można korzystać tylko z licencji SQL Server w chmurze w SQL Database. Nie masz już praw do podwójnego użytkowania lokalnie i w chmurze.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Jak Korzyść użycia hybrydowego platformy Azure dla SQL Server różni się od przenoszenia licencji?

Firma Microsoft oferuje korzyści z zakresu mobilności licencji do SQL Server klientom z programem Software Assurance. Pozwala to na ponowne przypisanie licencji do współużytkowanych serwerów partnerskich. Możesz użyć tej korzyści w przypadku usług Azure IaaS i AWS EC2.

Korzyść użycia hybrydowego platformy Azure SQL Server różni się od przenoszenia licencji w dwóch kluczowych obszarach:

- Zapewnia ekonomiczne korzyści związane z przenoszenium wysoce zwirtualizowanych obciążeń na platformę Azure. Klienci wersji SQL Server Enterprise Edition mogą uzyskać cztery rdzenie na platformie Azure w jednostce SKU Ogólnego przeznaczenia dla każdego rdzenia, które są lokalnie przeznaczone dla wysoce zwirtualizowanych aplikacji. Mobilność licencji nie pozwala na przenoszenie zwirtualizowanych obciążeń do chmury z uwzględnieniem specjalnych kosztów.
- Zapewnia miejsce docelowe PaaS na platformie Azure (SQL Database wystąpienia zarządzanego), które jest wysoce zgodne z SQL Server lokalnymi.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Jakie są określone prawa Korzyść użycia hybrydowego platformy Azure dla SQL Server?

SQL Database klienci mają następujące prawa skojarzone z Korzyść użycia hybrydowego platformy Azure dla SQL Server:

|Wpływ na licencję|Co Korzyść użycia hybrydowego platformy Azure na SQL Server uzyskać użytkownika?|
|---|---|
|Klienci z wersjami SQL Server Enterprise Core z pakietem SA|<li>Może być płacona stawka podstawowa dla Ogólnego przeznaczenia lub Krytyczne dla działania firmy jednostki SKU</li><br><li>1 rdzeń lokalnego = 4 rdzenie w jednostce SKU Ogólnego przeznaczenia</li><br><li>1 rdzeń lokalnego = 1 rdzeń w jednostce SKU Krytyczne dla działania firmy</li>|
|Klienci z wersjami SQL Server Standard Core z pakietem SA|<li>Można uiścić stawkę bazową tylko dla Ogólnego przeznaczenia jednostki SKU</li><br><li>1 rdzeń lokalnego = 1 rdzeń w jednostce SKU Ogólnego przeznaczenia</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>Kontakt z zespołem inżynierów programu SQL Server

- Baza danych [usługi dba Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): zadawanie odpowiedzi na pytania administracyjne.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): zadawaj pytania dotyczące programowania.
- [Fora MSDN](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): zadawaj pytania techniczne.
- [Opinie](https://aka.ms/sqlfeedback): Zgłoś błędy i funkcję żądania.
- [Reddit](https://www.reddit.com/r/SQLServer/): Dyskutuj SQL Server.

## <a name="next-steps"></a>Następne kroki

- Na [stronie cennika](https://azure.microsoft.com/pricing/details/sql-database/) można porównywać koszty i kalkulatory dotyczące pojedynczych baz danych i pul elastycznych.
- Zobacz te Przewodniki Szybki Start, aby rozpocząć pracę:

  - [Tworzenia bazy danych SQL w witrynie Azure Portal](sql-database-single-database-get-started.md)  
  - [Tworzenia bazy danych SQL za pomocą interfejsu wiersza polecenia platformy Azure](sql-database-get-started-cli.md)
  - [Tworzenie bazy danych SQL za pomocą programu PowerShell](sql-database-get-started-powershell.md)

- Aby uzyskać zestaw przykładów interfejsu wiersza polecenia platformy Azure i programu PowerShell, zobacz:
  - [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi SQL Database](sql-database-cli-samples.md)
  - [Przykłady programu Azure PowerShell dla usługi SQL Database](sql-database-powershell-samples.md)

- Aby uzyskać informacje o nowych możliwościach, które są ogłoszone, zobacz [Azure plan for SQL Database](https://azure.microsoft.com/roadmap/?category=databases).
- Zapoznaj się z [blogiem Azure SQL Database](https://azure.microsoft.com/blog/topics/database), gdzie SQL Server członków zespołu produktów SQL Database informacje i funkcje.

