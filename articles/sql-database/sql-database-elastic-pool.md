---
title: Zarządzanie wieloma bazami danych za pomocą pul elastycznych
description: Zarządzanie i skalowanie wielu baz danych SQL — setki i tysiące — przy użyciu pul elastycznych. Jedna cena za zasoby, które można dystrybuować w razie potrzeby.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, carlrab
ms.date: 08/06/2019
ms.openlocfilehash: ba309b864056b10fe6540e85ffbc4c013af00455
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186466"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Pule elastyczne ułatwiają zarządzanie wieloma bazami danych Azure SQL i skalowanie ich

Pule elastyczne usługi SQL Database to proste i niedrogie rozwiązanie do zarządzania i skalowania wielu baz danych, które mają różne i nieprzewidywalne wymagania dotyczące użycia. Bazy danych w puli elastycznej znajdują się na jednym serwerze Azure SQL Database i udostępniają określoną liczbę zasobów z ustawioną ceną. Pule elastyczne pule w usłudze Azure SQL Database umożliwiają deweloperom SaaS optymalizację stosunku ceny do wydajności dla grupy baz danych w ramach określonego budżetu, zapewniając jednocześnie elastyczność wydajności dla każdej bazy danych.

## <a name="what-are-sql-elastic-pools"></a>Co to są pule elastyczne SQL

Deweloperzy SaaS tworzą aplikacje w oparciu o warstwy danych w dużej skali składające się z wielu baz danych. Typowym wzorcem aplikacji jest udostępnianie jednej bazy danych dla każdego klienta. Jednak różni klienci często mają różne i nieprzewidywalne wzorce użycia i trudno przewidzieć wymagania dotyczące zasobów dla każdego indywidualnego użytkownika bazy danych. Tradycyjnie masz dwie opcje:

- Zasoby nadmiernej aprowizacji w oparciu o szczytowe użycie i za pośrednictwem płacenia
- W ramach zastrzegania kosztów, kosztem wydajności i zadowoleniem klientów podczas szczytu.

Pule elastyczne rozwiązują ten problem, upewniając się, że bazy danych uzyskują potrzebne im zasoby wydajności, gdy ich potrzebują. Udostępniają one prosty mechanizm alokacji zasobów w ramach przewidywalnego budżetu. Aby dowiedzieć się więcej na temat wzorców projektowych dla aplikacji SaaS wykorzystujących pule elastyczne, zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

> [!IMPORTANT]
> Nie jest naliczana opłata za bazę danych dla pul elastycznych. Opłaty są naliczane za każdą godzinę, gdy pula istnieje z największą liczbą jednostek eDTU lub rdzeni wirtualnych, niezależnie od użycia lub od tego, czy pula była aktywna przez czas krótszy niż godzina.

Pule elastyczne umożliwiają deweloperom kupowanie zasobów dla puli współdzielonej przez wiele baz danych w celu uwzględnienia nieprzewidywalnych okresów użycia poszczególnych baz danych. Zasoby dla puli można skonfigurować na podstawie [modelu zakupu opartego](sql-database-service-tiers-dtu.md) na jednostkach DTU lub [modelu zakupu opartego na rdzeń wirtualny](sql-database-service-tiers-vcore.md). Wymagania dotyczące zasobów dla puli są określane przez zagregowane wykorzystanie jego baz danych. Ilość zasobów dostępnych dla puli jest kontrolowana przez budżet dewelopera. Deweloper po prostu dodaje bazy danych do puli, ustawia minimalną i maksymalną ilość zasobów dla baz danych (minimalną i maksymalną DTU lub minimalną lub maksymalną rdzeni wirtualnych w zależności od wybranego modelu odzyskania), a następnie ustawia zasoby puli na podstawie ich wartościami. Korzystając z pul, deweloper może bezproblemowo rozwijać swoją usługę od niewielkiego startupu do dojrzałego biznesu w coraz większej skali.

Poszczególne bazy danych w ramach puli mają możliwość elastycznego skalowania automatycznego w określonym zakresie parametrów. Pod dużym obciążeniem baza danych może zużywać więcej zasobów w celu spełnienia wymagań. Bazy danych w mniejszych obciążeniach zużywają mniej, a bazy danych nie zużywają żadnych zasobów. Aprowizacja zasobów dla całej puli zamiast pojedynczych baz danych upraszcza zadania związane z zarządzaniem. Dodatkowo istnieje przewidywalny budżet dla puli. Dodatkowe zasoby można dodać do istniejącej puli bez przestojów związanych z bazą danych, z tą różnicą, że konieczne jest przeniesienie baz danych w celu zapewnienia dodatkowych zasobów obliczeniowych dla nowej rezerwacji jednostek eDTU. Podobnie, jeśli dodatkowe zasoby nie są już potrzebne, można je usunąć z istniejącej puli w dowolnym momencie. Można również dodawać bazy danych do puli lub odejmować je z niej. Jeśli baza danych przewidywalnie niewystarczająco wykorzystuje zasoby, należy ją przenieść.

> [!NOTE]
> Podczas przechodzenia baz danych do lub z puli elastycznej nie ma przestojów z wyjątkiem krótkiego czasu (w kolejności sekund) na końcu operacji, gdy połączenia bazy danych są porzucane.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Kiedy należy uwzględnić pulę elastyczną SQL Database

Pule są odpowiednie dla wielu baz danych o określonych wzorcach użycia. Dla danej bazy danych ten wzorzec charakteryzuje się niskim średnim wykorzystaniem oraz stosunkowo rzadkimi okresami zwiększonego użycia.

Im więcej baz danych możesz dodać do puli, tym większe uzyskujesz oszczędności. W zależności od wzorca użycia aplikacji możliwe jest uzyskanie oszczędności nawet już przy dwóch bazach danych S3.

Poniższe sekcje ułatwiają zrozumienie, jak ocenić, czy dodanie konkretnej kolekcji baz danych do puli może być korzystne. W przykładach użyto pul warstwy Standardowa, ale te same zasady mają zastosowanie także do pul w warstwach Podstawowa i Premium.

### <a name="assessing-database-utilization-patterns"></a>Ocena wzorców użycia bazy danych

Na poniższej ilustracji pokazano przykład bazy danych, która przez większość czasu pozostaje bezczynna, ale okresowo doświadcza dużego wzrostu aktywności. Jest to wzorzec użycia, który jest odpowiedni dla puli:

   ![pojedyncza baza danych odpowiednia dla puli](./media/sql-database-elastic-pool/one-database.png)

W przedstawionym okresie pięciu minut baza danych DB1 odnotowuje wzrost użycia do 90 DTU, ale jej całkowite średnie użycie jest mniejsze niż pięć DTU. Do uruchomienia tego obciążenia w pojedynczej bazie danych jest wymagany rozmiar obliczeń S3, ale nie są one używane w okresach niskiego działania.

Pula umożliwia udostępnienie tych nieużywanych jednostek DTU wielu bazom danych, a tym samym zmniejsza liczbę potrzebnych jednostek DTU i obniża koszty ogólne.

Rozwijając poprzedni przykład, załóżmy, że istnieją dodatkowe bazy danych o podobnych wzorcach użycia jak baza danych DB1. W następnych dwóch poniższych ilustracjach użycie czterech baz danych i 20 baz danych odbywa się na tym samym wykresie, aby zilustrować nienakładający się charakter ich użycia w czasie przy użyciu modelu zakupu opartego na jednostkach DTU:

   ![cztery bazy danych z wzorcem użycia odpowiednim dla puli](./media/sql-database-elastic-pool/four-databases.png)

   ![dwadzieścia baz danych z wzorcem użycia odpowiednim dla puli](./media/sql-database-elastic-pool/twenty-databases.png)

Zagregowane użycie jednostek DTU we wszystkich 20 bazach danych przedstawia czarna linia na poprzednim rysunku. Pokazuje ona, że zagregowane użycie jednostek DTU nigdy nie przekracza 100 DTU i wskazuje, że te 20 baz danych może współużytkować 100 jednostek eDTU w tym okresie. Wynikiem tego jest spadek 20x w DTU i obniżka ceny trzynastokrotnie niższą w porównaniu do umieszczania każdej bazy danych w rozmiarach S3 dla pojedynczych baz danych.

Ten przykład jest idealny z następujących przyczyn:

- Istnieją duże różnice między użyciem szczytowym a średnim użyciem na bazę danych.
- Użycie szczytowe dla poszczególnych baz danych występuje w różnych punktach w czasie.
- Jednostki eDTU są współdzielone przez wiele baz danych.

Cena puli jest funkcją jednostek eDTU puli. Chociaż cena jednostki eDTU dla puli jest 1,5 raza większa niż cena jednostki DTU dla pojedynczej bazy danych, **jednostki eDTU puli mogą być współdzielone przez wiele baz danych, a tym samym potrzebna jest mniejsza całkowita liczba jednostek eDTU**. Te różnice w cenie i współużytkowanie jednostek eDTU są podstawą potencjalnych oszczędności, które mogą zapewnić pule.

Poniższe reguły dotyczące liczby baz danych i wykorzystania baz danych pomagają zapewnić, że Pula zapewnia zredukowany koszt w porównaniu z użyciem rozmiarów obliczeniowych dla pojedynczych baz danych.

### <a name="minimum-number-of-databases"></a>Minimalna liczba baz danych

Jeśli zagregowana ilość zasobów dla pojedynczych baz danych jest większa niż 1,5 x zasobów wymaganych dla puli, wówczas elastyczna Pula jest bardziej opłacalna.

***Przykład modelu zakupu opartego na jednostkach DTU***<br>
Co najmniej dwie bazy danych S3 lub co najmniej 15 baz danych S0 są konieczne, aby Pula jednostek eDTU 100 była bardziej opłacalna niż użycie rozmiarów obliczeń dla pojedynczych baz danych.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Maksymalna liczba baz danych z równoczesnymi szczytami użycia

Udostępniając zasoby, nie wszystkie bazy danych w puli mogą jednocześnie używać zasobów do limitu dostępnego dla pojedynczych baz danych. Im mniejsza liczba baz danych, które jednocześnie są szczytowe, tym niższe zasoby puli można ustawić, a obciążenie puli stanie się coraz bardziej ekonomiczne. Ogólnie rzecz biorąc, nie więcej niż 2/3 (lub 67%) bazy danych w puli powinny jednocześnie mieć wartość szczytową dla limitów zasobów.

***Przykład modelu zakupu opartego na jednostkach DTU***

Aby zmniejszyć koszty dla trzech baz danych S3 w puli 200 eDTU, co najwyżej dwie z tych baz danych mogą jednocześnie osiągać szczytowe użycie. W przeciwnym razie, jeśli więcej niż dwie z tych czterech baz danych S3 jednocześnie osiągają szczytowe użycie, rozmiar puli musiałby zostać zwiększony do ponad 200 eDTU. Jeśli rozmiar puli zostanie zmieniony na ponad 200 jednostek eDTU, do puli należy dodać więcej baz danych S3, aby zachować koszty mniejsze niż rozmiary obliczeniowe dla pojedynczych baz danych.

Należy zauważyć, że w tym przykładzie nie jest rozważane użycie innych baz danych w puli. Jeśli wszystkie bazy danych mają jakiś poziom użycia w dowolnym danym momencie czasu, wówczas mniej niż 2/3 (lub 67%) baz danych może jednocześnie osiągać szczytowe użycie.

### <a name="resource-utilization-per-database"></a>Wykorzystanie zasobów na bazę danych

Duża różnica między szczytowym i średnim użyciem bazy danych wskazuje na dłuższe okresy niewielkiego wykorzystania i krótkie okresy wysokiego wykorzystania. Ten wzorzec użycia jest idealny dla współużytkowania zasobów między bazami danych. Bazę danych należy rozważyć do umieszczenia w puli, jeśli jej szczytowe użycie jest ponad 1,5 raza większe niż jej średnie użycie.

**Przykład modelu zakupu opartego na**jednostkach DTU: baza danych S3, która ma szczyt do 100 DTU i średnio używa 67 DTU lub mniej, jest dobrym kandydatem do udostępniania jednostek eDTU w puli. Również baza danych S1, która osiąga szczytowe użycie 20 DTU, a przeciętnie używa 13 DTU lub mniej, jest dobrym kandydatem do umieszczenia w puli.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Jak mogę wybrać poprawnego rozmiaru puli

Najlepszy rozmiar puli zależy od zagregowanych zasobów potrzebnych dla wszystkich baz danych w puli. Obejmuje to określenie następujących czynności:

- Maksymalna liczba zasobów używanych przez wszystkie bazy danych w puli (maksymalna DTU lub maksymalna rdzeni wirtualnych w zależności od wybranego modelu odzyskania).
- Maksymalna liczba bajtów magazynu wykorzystana przez wszystkie bazy danych w puli.

W przypadku dostępnych warstw usług dla każdego modelu zasobów zobacz [model zakupu oparty na](sql-database-service-tiers-dtu.md) jednostkach DTU lub [model zakupu oparty na rdzeń wirtualny](sql-database-service-tiers-vcore.md).

W przypadkach, gdy nie można użyć narzędzi, skorzystanie z następujących instrukcji krok po kroku może ułatwić oszacowanie, czy pula jest bardziej opłacalna niż pojedyncza baza danych:

1. Oszacuj jednostek eDTU lub rdzeni wirtualnych wymagany dla puli w następujący sposób:

   W przypadku modelu zakupu opartego na jednostkach DTU: MAX (<*całkowita liczba baz danych* X *średnia użycie jednostek DTU na bazę danych*>,<br>  
   <*liczba baz danych jednocześnie osiągających szczyt użycia* X *użycie szczytowe jednostek DTU na bazę danych*)

   W przypadku modelu zakupu opartego na rdzeń wirtualny: MAX (<*całkowita liczba baz danych* X *średnia użycie rdzeń wirtualny na bazę danych*>,<br>  
   <*liczba równoczesnych szczytowych baz danych* X *szczytowego użycia rdzeń wirtualny na bazę danych*)

2. Oszacuj miejsce do magazynowania wymagane dla puli przez dodanie liczby bajtów potrzebnych dla wszystkich baz danych w puli. Następnie określ rozmiar puli (w jednostkach eDTU), który zapewni tę ilość miejsca przechowywania.
3. W przypadku modelu zakupu opartego na jednostkach DTU należy uzyskać więcej wartości szacunkowych jednostek eDTU z kroku 1 i 2. W przypadku modelu zakupu opartego na rdzeń wirtualny należy uzyskać oszacowanie rdzeń wirtualny z kroku 1.
4. Zapoznaj się z [cennikiem SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) i Znajdź najmniejszy rozmiar puli większy niż oszacowanie od kroku 3.
5. Porównaj cenę puli z kroku 5 z ceną za korzystanie z odpowiednich rozmiarów obliczeń dla pojedynczych baz danych.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Korzystanie z innych funkcji SQL Database z elastycznymi pulami

### <a name="elastic-jobs-and-elastic-pools"></a>Zadania elastyczne i elastyczne pule

W przypadku puli zadania zarządzania są uproszczone dzięki uruchamianiu skryptów w **[zadaniach elastycznych](elastic-jobs-overview.md)** . Zadanie elastyczne eliminuje większość monotonnych czynności związanych z dużą liczbą baz danych.

Aby uzyskać więcej informacji na temat innych narzędzi do pracy z wieloma bazami danych, zobacz artykuł dotyczący [skalowania w poziomie za pomocą usługi Azure SQL Database](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Opcje ciągłości biznesowej dla baz danych w puli elastycznej

Bazy danych w puli obsługują te same [funkcje ciągłości działalności biznesowej](sql-database-business-continuity.md), które są dostępne dla pojedynczych baz danych.

- **Przywracanie do punktu w czasie**

  Przywracanie do określonego momentu używa automatycznych kopii zapasowych bazy danych w celu odzyskania bazy danych w puli do konkretnego punktu w czasie. Zobacz [Przywracanie do punktu w czasie](sql-database-recovery-using-backups.md#point-in-time-restore).

- **Przywracanie geograficzne**

  Przywracanie geograficzne zapewnia domyślną opcję odzyskiwania, gdy baza danych jest niedostępna z powodu zdarzenia w regionie, w którym jest hostowana baza danych. Zobacz [Restore an Azure SQL Database or failover to a secondary](sql-database-disaster-recovery.md) (Przywracanie usługi Azure SQL Database lub przełączanie w trybie failover do pomocniczej bazy danych).

- **Aktywna replikacja geograficzna**

  W przypadku aplikacji, które mają bardziej agresywne wymagania dotyczące odzyskiwania niż w przypadku przywracania geograficznego, można skonfigurować [aktywną replikację geograficzną](sql-database-active-geo-replication.md) lub [grupę autotrybu failover](sql-database-auto-failover-group.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Tworzenie nowej SQL Database elastycznej puli przy użyciu Azure Portal

Istnieją dwa sposoby tworzenia elastycznej puli w Azure Portal.

1. Wybierz pozycję **Azure SQL** w menu po lewej stronie Azure Portal. Jeśli na liście nie ma usługi Azure SQL, wybierz pozycję **wszystkie usługi**, a następnie wpisz *SQL Azure* w polu wyszukiwania.
2. Wybierz pozycję **+ Dodaj** , aby otworzyć stronę **Wybieranie opcji wdrożenia SQL** . Dodatkowe informacje na temat pul elastycznych można wyświetlić, wybierając pozycję **Pokaż szczegóły** na kafelku **bazy danych** .
3. Na kafelku **bazy danych** wybierz pozycję **Pula elastyczna** na liście rozwijanej **Typ zasobu** , a następnie wybierz pozycję **Utwórz**:

   ![Tworzenie puli elastycznej](./media/sql-database-elastic-pool/create-elastic-pool.png)


1. Można też utworzyć pulę elastyczną, przechodząc do istniejącego serwera SQL Azure, a następnie klikając pozycję **+ Nowa pula** , aby utworzyć pulę bezpośrednio na tym serwerze.

> [!NOTE]
> Można utworzyć wiele pul na serwerze, ale nie można dodawać baz danych z różnych serwerów do tej samej puli.

Warstwa usługi puli określa funkcje dostępne dla elastycznych pul i maksymalną ilość zasobów dostępnych dla każdej bazy danych. Aby uzyskać szczegółowe informacje, zobacz limity zasobów dla pul elastycznych w [modelu DTU](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes). W przypadku limitów zasobów opartych na rdzeń wirtualny dla pul elastycznych zobacz [limity zasobów opartych na rdzeń wirtualny — pule elastyczne](sql-database-vcore-resource-limits-elastic-pools.md).

Aby skonfigurować zasoby i Cennik puli, kliknij pozycję **Konfiguruj pulę**. Następnie wybierz warstwę usług, Dodaj bazy danych do puli i skonfiguruj limity zasobów dla puli i jej baz danych.

Po zakończeniu konfigurowania puli można kliknąć pozycję "Zastosuj", nazwę puli, a następnie kliknąć przycisk "OK", aby utworzyć pulę.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Monitorowanie puli elastycznej i jej baz danych

W Azure Portal można monitorować wykorzystanie puli elastycznej i baz danych w tej puli. Możesz również wprowadzić zestaw zmian w puli elastycznej i przesłać wszystkie zmiany w tym samym czasie. Te zmiany obejmują dodawanie lub usuwanie baz danych, zmienianie ustawień puli elastycznej lub zmienianie ustawień bazy danych.

Aby rozpocząć monitorowanie puli elastycznej, Znajdź i Otwórz pulę elastyczną w portalu. Zobaczysz ekran, który zawiera przegląd stanu puli elastycznej. Obejmuje to:

- Wykresy monitorowania przedstawiające użycie zasobów puli elastycznej
- Ostatnie alerty i zalecenia, jeśli są dostępne dla puli elastycznej

Na poniższej ilustracji przedstawiono przykładową pulę elastyczną:

![Widok puli](./media/sql-database-elastic-pool-manage-portal/basic.png)

Aby uzyskać więcej informacji na temat puli, można kliknąć dowolne z dostępnych informacji w tym omówieniu. Kliknięcie wykresu **wykorzystania zasobów** spowoduje przejście do widoku monitorowanie platformy Azure, w którym można dostosować metryki i przedziały czasu wyświetlane na wykresie. Kliknięcie dowolnego z dostępnych powiadomień spowoduje przejście do bloku zawierającego szczegółowe informacje o tym alercie lub rekomendacji.

Jeśli chcesz monitorować bazy danych w puli, kliknij pozycję **użycie zasobów bazy danych** w sekcji **monitorowanie** w menu zasób po lewej stronie.

![Strona użycia zasobów bazy danych](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>Aby dostosować wyświetlanie wykresu

Można edytować wykres i stronę metryki, aby wyświetlić inne metryki, takie jak procent procesora CPU, procent operacji we/wy danych i użyta wartość procentowa operacji we/wy dziennika.

Na formularzu **edytowania wykresu** można wybrać stały zakres czasu lub kliknąć opcję **niestandardowy** , aby zaznaczyć okno 24-godzinne w ciągu ostatnich dwóch tygodni, a następnie wybrać zasoby do monitorowania.

### <a name="to-select-databases-to-monitor"></a>Aby wybrać bazy danych do monitorowania

Domyślnie wykres w bloku **użycie zasobów bazy danych** będzie przedstawiał 5 najważniejszych baz danych według jednostek DTU lub CPU (w zależności od warstwy usług). Możesz przełączać bazy danych na tym wykresie, wybierając i usuwając zaznaczenie baz danych z listy poniżej wykresu za pomocą pól wyboru po lewej stronie.

Możesz również wybrać więcej metryk, aby wyświetlić je obok siebie w tej tabeli bazy danych, aby uzyskać pełniejszy widok wydajności baz danych.

Aby uzyskać więcej informacji, zobacz [tworzenie alertów SQL Database w Azure Portal](sql-database-insights-alerts-portal.md).

## <a name="customer-case-studies"></a>Analizy przypadków klientów

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  SnelStart używane pule elastyczne z Azure SQL Database, aby szybko rozwijać swoje usługi biznesowe według stawki 1 000 nowej bazy danych Azure SQL na miesiąc.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Umbraco używa pul elastycznych z Azure SQL Database, aby szybko inicjować i skalować usługi dla tysięcy dzierżawców w chmurze.

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)    

   Daxko/CSI korzysta z elastycznych pul z Azure SQL Database, aby przyspieszyć cykl programowania i zwiększyć wydajność usług i klientów.   

## <a name="next-steps"></a>Następne kroki

- Aby skalować pule elastyczne, zobacz [skalowanie pul elastycznych](sql-database-elastic-pool-scale.md) i [skalowanie elastycznej puli — przykładowego kodu](scripts/sql-database-monitor-and-scale-pool-powershell.md)
- Film wideo zawiera temat kurs wideo dotyczący usługi [Microsoft Virtual Academy na Azure SQL Database elastycznych możliwościach](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
- Aby dowiedzieć się więcej na temat wzorców projektowych dla aplikacji SaaS wykorzystujących pule elastyczne, zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).
- Aby zapoznać się z samouczkiem SaaS przy użyciu pul elastycznych, zobacz [wprowadzenie do aplikacji Wingtip SaaS](sql-database-wtp-overview.md).
- Aby dowiedzieć się więcej o zarządzaniu zasobami w pulach elastycznych z wieloma bazami danych, zobacz [Zarządzanie zasobami w ramach gęstych pul elastycznych](sql-database-elastic-pool-resource-management.md).
