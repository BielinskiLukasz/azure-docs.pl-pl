---
title: Odnajdywanie, Ocena i analiza zależności — często zadawane pytania
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące odnajdywania, oceny i analizy zależności w programie Azure Migrate.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: e46d1e6ee1dd404e6e040eb394e89dd86a3d4d8e
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082139"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Wykrywanie, Ocena i analiza zależności — typowe pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące odnajdywania, oceny i analizy zależności w programie Azure Migrate. Jeśli masz inne pytania, zapoznaj się z następującymi zasobami:

- [Ogólne pytania](resources-faq.md) dotyczące Azure Migrate
- Pytania dotyczące [urządzenia Azure Migrate](common-questions-appliance.md)
- Pytania dotyczące [migracji serwera](common-questions-server-migration.md)
- Uzyskaj odpowiedzi na pytania na [forum Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Ile maszyn wirtualnych można odnajdywać za pomocą urządzenia?

Możesz odkryć do 10 000 maszyn wirtualnych VMware, do 5 000 maszyn wirtualnych funkcji Hyper-V, a nawet do 250 serwery fizyczne przy użyciu jednego urządzenia. Jeśli masz więcej maszyn, zapoznaj się [z tematem skalowanie oceny funkcji Hyper-V](scale-hyper-v-assessment.md), [skalowanie oceny oprogramowania VMware](scale-vmware-assessment.md)lub [skalowanie oceny serwera fizycznego](scale-physical-assessment.md).

## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>Zmieniono rozmiar maszyny wirtualnej. Czy mogę ponownie uruchomić ocenę?

Urządzenie Azure Migrate ciągle zbiera informacje o środowisku lokalnym.  Ocena to migawka lokalnych maszyn wirtualnych do określonego momentu. Jeśli zmienisz ustawienia na maszynie wirtualnej, która ma zostać oceniona, użyj opcji Oblicz ponownie, aby zaktualizować ocenę przy użyciu najnowszych zmian.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Jak mogę odnajdywania maszyn wirtualnych w środowisku wielodostępnym?

- **VMware**: Jeśli środowisko jest współużytkowane przez dzierżawców i nie chcesz odnajdywać maszyn wirtualnych dzierżawy w ramach subskrypcji innej dzierżawy, Utwórz VMware vCenter Server poświadczenia, które mogą uzyskiwać dostęp tylko do maszyn wirtualnych, które chcesz odnajdywać. Następnie użyj tych poświadczeń po rozpoczęciu odnajdywania na urządzeniu Azure Migrate.
- **Funkcja Hyper-v**: odnajdywanie używa poświadczeń hosta funkcji Hyper-v. Jeśli maszyny wirtualne korzystają z tego samego hosta funkcji Hyper-V, nie ma możliwości oddzielenia odnajdywania.  

## <a name="do-i-need-vcenter-server"></a>Czy potrzebuję vCenter Server?

Tak, Azure Migrate wymaga vCenter Server w środowisku VMware w celu przeprowadzenia odnajdywania. Azure Migrate nie obsługuje odnajdywania hostów ESXi, które nie są zarządzane przez vCenter Server.

## <a name="what-are-the-sizing-options"></a>Jakie są opcje ustalania wielkości?

W przypadku ustalania rozmiarów lokalnych Azure Migrate nie uwzględnia danych wydajności maszyny wirtualnej w celu oceny. Azure Migrate ocenia rozmiary maszyn wirtualnych na podstawie konfiguracji lokalnej. W przypadku ustalania rozmiarów na podstawie wydajności rozmiar jest oparty na danych użycia.

Na przykład jeśli lokalna maszyna wirtualna ma cztery rdzenie i 8 GB pamięci na 50% użycia procesora CPU i 50% wykorzystania pamięci:
- Ustalanie rozmiarów lokalnych będzie zalecane dla jednostki SKU maszyny wirtualnej platformy Azure, która ma cztery rdzenie i 8 GB pamięci.
- Ustalanie wielkości na podstawie wydajności spowoduje zalecanie jednostki SKU maszyny wirtualnej, która ma dwa rdzenie i 4 GB pamięci, ponieważ jest brana pod uwagę procent użycia.

Podobnie rozmiary dysku są zależne od kryteriów ustalania rozmiarów i typu magazynu:
- Jeśli kryteria ustalania wielkości są oparte na wydajności i typ magazynu jest automatyczny, Azure Migrate pobiera wartości IOPS i przepływności dysku do konta, gdy identyfikuje docelowy typ dysku (standardowa lub Premium).
- Jeśli kryterium ustalania rozmiaru jest oparte na wydajności, a typ magazynu to Premium, Azure Migrate zaleca SKU dysku w warstwie Premium na podstawie rozmiaru dysku lokalnego. Ta sama logika jest stosowana do wielkości dysku, gdy wielkość liter jest jako lokalna, a typ magazynu to standardowa lub Premium.

## <a name="does-performance-history-and-utilization-affect-sizing"></a>Czy historia wydajności i wykorzystanie wpływają na rozmiar?

Tak, historia wydajności i wykorzystanie wpływają na rozmiar w Azure Migrate.

### <a name="performance-history"></a>Historia wydajności

W przypadku tylko ustalania rozmiaru na podstawie wydajności Azure Migrate zbiera historię wydajności maszyn lokalnych, a następnie używa jej w celu zaproponowania rozmiaru maszyny wirtualnej i typu dysku na platformie Azure:

1. Urządzenie nieustannie profiluje środowisko lokalne, aby gromadzić dane dotyczące użycia w czasie rzeczywistym co 20 sekund.
1. Urządzenie zbiera zebrane 20-sekundowe próbki i używa ich do tworzenia jednego punktu danych co 15 minut.
1. Aby utworzyć punkt danych, urządzenie wybiera wartość szczytu ze wszystkich próbek 20-sekund.
1. Urządzenie wysyła punkt danych do platformy Azure.

### <a name="utilization"></a>Zużycie

Po utworzeniu oceny na platformie Azure, w zależności od czasu trwania wydajności i wartości percentylu historii wydajności, która jest ustawiona, Azure Migrate oblicza wartość efektywnego wykorzystania, a następnie używa jej do ustalania rozmiarów.

Na przykład jeśli ustawisz czas trwania wydajności na jeden dzień i wartość percentylu na używany 95. percentyl, Azure Migrate sortuje 15-minutowe punkty próbkowania wysyłane przez moduł zbierający dla ostatniego dnia w kolejności rosnącej. Wybiera wartość percentylu używany 95. jako efektywne wykorzystanie.

Użycie wartości percentylu używany 95. gwarantuje, że elementy odstające są ignorowane. Elementy odstające mogą być dołączane, jeśli Azure Migrate używa percentylu 99. Aby wybrać szczytowe użycie dla tego okresu bez braku żadnych wartości odstających, ustaw Azure Migrate na użycie 99 percentyl.

## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>Jak są oceniane oceny oparte na imporcie, które różnią się od ocen ze źródłem odnajdywania jako urządzenie?

Oceny oparte na imporcie są ocenami utworzonymi w przypadku maszyn zaimportowanych do Azure Migrate przy użyciu pliku CSV. Tylko cztery pola są wymagane do zaimportowania: Nazwa serwera, rdzenie, pamięć i system operacyjny. Poniżej przedstawiono kilka kwestii, które należy zwrócić uwagę: 
 - Kryteria gotowości są mniej rygorystyczne w przypadku ocen opartych na imporcie w parametrze typu rozruchu. Jeśli typ rozruchu nie zostanie podany, zakłada się, że maszyna ma typ rozruchu w systemie BIOS, a maszyna nie jest oznaczona jako **warunkowo gotowa**. W obszarze oceny ze źródłem odnajdywania jako urządzenie gotowość jest oznaczona jako **warunkowo gotowa** , jeśli brakuje typu rozruchu. Różnica w obliczaniu gotowości wynika z faktu, że użytkownicy mogą nie mieć wszystkich informacji na maszynach na wczesnych etapach planowania migracji w przypadku wykonywania ocen opartych na imporcie. 
 - Oceny importowe oparte na wydajności używają wartości wykorzystania dostarczonej przez użytkownika do obliczeń o odpowiednim wymiarze. Ponieważ wartość użycia jest dostarczana przez użytkownika, w oknie właściwości oceny są wyłączone opcje **historia wydajności** i **użycie percentylu** . W obszarze oceny ze źródłem odnajdywania jako urządzenie wybrana wartość percentylu jest wybierana z danych wydajności zbieranych przez urządzenie.

## <a name="what-is-dependency-visualization"></a>Co to jest Wizualizacja zależności?

Wizualizacja zależności może pomóc ocenić grupy maszyn wirtualnych do migracji z większą pewnością. Wizualizacja między różnymi kontrolami zależności maszyn przed uruchomieniem oceny. Pozwala to zagwarantować, że nic nie pozostanie w tle i pomaga uniknąć nieoczekiwanego przestoju podczas migracji na platformę Azure. Azure Migrate używa rozwiązania Service Map w Azure Monitor, aby włączyć wizualizację zależności. [Dowiedz się więcej](concepts-dependency-visualization.md).

> [!NOTE]
> Wizualizacja zależności nie jest dostępna w Azure Government.

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Jaka jest różnica między opartymi na agentach i bez wykorzystania agentów?

Różnice między wizualizacją bez agenta i wizualizacją opartą na agentach zostały podsumowane w tabeli.

**Wymaganie** | **Bez agenta** | **Na podstawie agenta**
--- | --- | ---
Pomoc techniczna | Ta opcja jest obecnie w wersji zapoznawczej i jest dostępna tylko dla maszyn wirtualnych VMware. [Przejrzyj](migrate-support-matrix-vmware.md#agentless-dependency-visualization) obsługiwane systemy operacyjne. | Ogólnie dostępna.
Agent | Nie trzeba instalować agentów na maszynach, które mają być sprawdzane krzyżowo. | Agenci do zainstalowania na każdej maszynie lokalnej, którą chcesz analizować: [program Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)i [Agent zależności](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Wymagania wstępne | [Zapoznaj](concepts-dependency-visualization.md#agentless-visualization) się z wymaganiami dotyczącymi wymagań wstępnych i wdrażania. | [Zapoznaj](concepts-dependency-visualization.md#agent-based-visualization) się z wymaganiami dotyczącymi wymagań wstępnych i wdrażania.
Log Analytics | Nie jest wymagane. | Azure Migrate używa rozwiązania [Service map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) w [dziennikach Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) na potrzeby wizualizacji zależności. [Dowiedz się więcej](concepts-dependency-visualization.md#agent-based-visualization).
Jak to działa | Przechwytuje dane połączenia TCP na maszynach z włączoną funkcją wizualizacji zależności. Po odnajdywaniu dane są zbierane w odstępach pięciu minut. | Service Map agenci zainstalowani na komputerze zbierają dane dotyczące procesów TCP oraz połączeń przychodzących/wychodzących dla każdego procesu.
Dane | Nazwa serwera źródłowego, proces, nazwa aplikacji.<br/><br/> Nazwa serwera maszyny docelowej, proces, nazwa aplikacji i port. | Nazwa serwera źródłowego, proces, nazwa aplikacji.<br/><br/> Nazwa serwera maszyny docelowej, proces, nazwa aplikacji i port.<br/><br/> Liczba połączeń, opóźnień i informacji o przesyłaniu danych jest zbieranych i dostępnych dla zapytań Log Analytics. 
Wizualizacja | Mapę zależności pojedynczego serwera można wyświetlać w czasie trwania z przedziału od godziny do 30 dni. | Mapa zależności pojedynczego serwera.<br/><br/> Mapę można wyświetlać tylko w ciągu godziny.<br/><br/> Mapa zależności grupy serwerów.<br/><br/> Dodawanie i usuwanie serwerów w grupie z widoku mapy.
Eksport danych | Nie można obecnie pobrać w formacie tabelarycznym. | Dane można badać przy użyciu Log Analytics.

## <a name="do-i-pay-for-dependency-visualization"></a>Czy płacisz za wizualizację zależności?

Nie. Dowiedz się więcej o [cenach Azure Migrate](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>Co należy zainstalować dla wizualizacji zależności opartej na agentach?

Aby użyć wizualizacji zależności opartej na agentach, Pobierz i zainstaluj agentów na każdej maszynie lokalnej, którą chcesz oszacować:

- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)
- [Agent zależności](../azure-monitor/platform/agents-overview.md#dependency-agent)
- Jeśli masz maszyny, które nie mają łączności z Internetem, Pobierz i zainstaluj na nich bramę Log Analytics.

Te agenci są potrzebne tylko wtedy, gdy jest używana Wizualizacja zależności oparta na agentach.

## <a name="can-i-use-an-existing-workspace"></a>Czy mogę użyć istniejącego obszaru roboczego?

Tak, w przypadku wizualizacji zależności opartej na agentach można dołączyć istniejący obszar roboczy do projektu migracji i użyć go do wizualizacji zależności. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Czy mogę wyeksportować raport wizualizacji zależności?

Nie, nie można wyeksportować raportu wizualizacji zależności w wizualizacji opartej na agentach. Jednak Azure Migrate używa Service Map i można użyć [interfejsu API REST Service map](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) do pobrania zależności w formacie JSON.

## <a name="can-i-automate-agent-installation"></a>Czy można zautomatyzować instalację agenta?

Dla wizualizacji zależności opartej na agentach:

- [Zainstaluj agenta zależności](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)przy użyciu skryptu.
- W przypadku MMA należy [użyć wiersza polecenia lub automatyzacji](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration)lub użyć [skryptu](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Oprócz skryptów można użyć narzędzi wdrażania, takich jak Microsoft Endpoint Configuration Manager i [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) , aby wdrożyć agentów.

## <a name="what-operating-systems-does-mma-support"></a>Jakie systemy operacyjne są obsługiwane przez MMA?

- Zapoznaj się z listą [systemów operacyjnych Windows obsługiwanych przez program MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Zapoznaj się z listą [systemów operacyjnych Linux obsługiwanych przez program MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>Czy mogę wizualizować zależności przez więcej niż godzinę?

W przypadku wizualizacji opartej na agentach można wizualizować zależności przez maksymalnie jedną godzinę. Można wrócić o jeden miesiąc do określonej daty w historii, ale maksymalny czas trwania wizualizacji wynosi godzinę. Na przykład można użyć czasu trwania w mapie zależności, aby wyświetlić zależności dla wczoraj, ale można wyświetlić zależności tylko dla jednogodzinnego okna. Można jednak użyć dzienników Azure Monitor do [wykonywania zapytań o dane zależności](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) przez dłuższy czas.

W przypadku wizualizacji bez wykorzystania agentów można wyświetlić mapę zależności pojedynczego serwera od czasu trwania z przedziału od 1 godziny do 30 dni.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Czy mogę wizualizować zależności dla grup o więcej niż 10 maszynach wirtualnych?

Możesz [wizualizować zależności](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) dla grup, które mają maksymalnie 10 maszyn wirtualnych. Jeśli masz grupę, która ma więcej niż 10 maszyn wirtualnych, zalecamy podział grupy do mniejszych grup, a następnie wizualizację zależności.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [omówieniem Azure Migrate](migrate-services-overview.md).
