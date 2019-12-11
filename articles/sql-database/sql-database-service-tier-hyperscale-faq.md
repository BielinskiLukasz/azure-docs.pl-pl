---
title: Często zadawane pytania — skalowanie (Citus) — Azure Database for PostgreSQL
description: Odpowiedzi na często zadawane pytania dotyczące usługi Azure SQL Database w warstwie usług skalowania — zwykle nazywanej bazą danych w skali.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 10/12/2019
ms.openlocfilehash: 377de93733d94d8cff5518eebb8ebba38154d10d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974023"
---
# <a name="azure-sql-database-hyperscale-faq"></a>Azure SQL Database często zadawane pytania dotyczące skalowania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące klientów rozważających bazę danych w Azure SQL Databaseej warstwie usług, która jest określana jako tylko w pozostałej części często zadawanych pytań. W tym artykule opisano scenariusze obsługiwane przez funkcję skalowania i funkcje, które są zgodne ze skalą.

- Często zadawane pytania są przeznaczone dla czytelników, którzy mają zwięzłe zrozumienie warstwy usługi w ramach skalowania i poszukują określonych pytań i odpowiedzi na nie.
- Te często zadawane pytania nie są przeznaczone do Guidebook lub odpowiedzi na pytania dotyczące korzystania z bazy danych w skali. Aby zapoznać się z wprowadzeniem do skalowania, zalecamy zapoznanie się z dokumentacją w [Azure SQL Database](sql-database-service-tier-hyperscale.md) .

## <a name="general-questions"></a>Pytania ogólne

### <a name="what-is-a-hyperscale-database"></a>Co to jest baza danych ze skalą

Baza danych wieloskali to baza danych Azure SQL Database w warstwie usług w ramach skalowania, która jest obsługiwana przez technologię magazynu skalowalnego w poziomie. Baza danych wieloskalowania obsługuje do 100 TB danych i zapewnia wysoką przepływność i wydajność, a także szybkie skalowanie w celu dostosowania do wymagań dotyczących obciążenia. Skalowanie jest widoczne dla aplikacji — łączności, przetwarzania zapytań itp., podobnie jak w przypadku każdej innej bazy danych SQL Azure.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Jakie typy zasobów i modele zakupów obsługują skalowanie

Warstwa usługi do skalowania jest dostępna tylko dla pojedynczych baz danych korzystających z modelu zakupu opartego na rdzeń wirtualny w Azure SQL Database.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Jak warstwa usługi dla skalowania różni się od Ogólnego przeznaczenia i Krytyczne dla działania firmy warstw usług

Warstwy usług oparte na rdzeń wirtualny są zróżnicowane w zależności od dostępności bazy danych i typu magazynu, wydajności i maksymalnego rozmiaru, zgodnie z opisem w poniższej tabeli.

| | Typ zasobu | Ogólne zastosowanie |  Hiperskalowanie | Krytyczne dla działania firmy |
|:---:|:---:|:---:|:---:|:---:|
| **Najlepsze dla** |Wszystko|Oferuje zorientowane na budżety Opcje obliczeniowe i magazynowe.|Większość obciążeń firmowych. Skalowanie automatyczne magazynu o rozmiarze do 100 TB, szybkie skalowanie w pionie i w poziomie, szybkie przywracanie bazy danych.|Aplikacje OLTP o wysokim współczynniku transakcji i niskim opóźnieniu we/wy. Oferuje największą odporność na błędy i szybkie przełączanie w tryb failover przy użyciu wielu replik synchronicznie zaktualizowanych.|
|  **Typ zasobu** ||Pojedyncza baza danych/Pula elastyczna/wystąpienie zarządzane | Pojedyncza baza danych | Pojedyncza baza danych/Pula elastyczna/wystąpienie zarządzane |
| **Rozmiar obliczeń**|Pojedyncza baza danych/Pula elastyczna * | od 1 do 80 rdzeni wirtualnych | od 1 do 80 rdzeni wirtualnych * | od 1 do 80 rdzeni wirtualnych |
| |Wystąpienie zarządzane | 8, 16, 24, 32, 40, 64, 80 rdzeni wirtualnych | ND | 8, 16, 24, 32, 40, 64, 80 rdzeni wirtualnych |
| **Typ magazynu** | Wszystko |Magazyn zdalny w warstwie Premium (na wystąpienie) | Niepołączony magazyn z lokalną pamięcią podręczną dysków SSD (na wystąpienie) | Lokalny magazyn SSD o wysokiej szybkości (na wystąpienie) |
| **Rozmiar magazynu** | Pojedyncza baza danych/Pula elastyczna *| 5 GB – 4 TB | Do 100 TB | 5 GB – 4 TB |
| | Wystąpienie zarządzane  | 32 GB – 8 TB | ND | 32 GB – 4 TB |
| **IOPS** | Pojedyncza baza danych | 500 operacji we/wy na sekundę z 7000 maksymalną liczbą IOPS | Skalowanie jest architekturą wielowarstwową z buforowaniem na wielu poziomach. Efektywne operacje we/wy będą zależeć od obciążenia. | 5000 operacji we/wy z maksymalną liczbą IOPS 200 000|
| | Wystąpienie zarządzane | Zależy od rozmiaru pliku | ND | 1375 operacji we/wy na sekundę |
|**Dostępność**|Wszystko|1 replika, brak skalowania do odczytu, brak lokalnej pamięci podręcznej | Wiele replik, do 4 odczyt skalowalny w poziomie, częściowa lokalna pamięć podręczna | 3 repliki, odczyt skalowalny w poziomie, strefa nadmiarowa, pełny magazyn lokalny |
|**Kopii zapasowych**|Wszystko|RA-GRS, 7-35 dni (domyślnie 7 dni)| RA-GRS, 7 dni, stałe odzyskiwanie do czasu w czasie (kopie) | RA-GRS, 7-35 dni (domyślnie 7 dni) |

\* pule elastyczne nie są obsługiwane w warstwie usługi w ramach skalowania

### <a name="who-should-use-the-hyperscale-service-tier"></a>Kto powinien korzystać z warstwy usługi do skalowania

Warstwa usługi do skalowania jest przeznaczona dla klientów, którzy mają duże lokalne SQL Server bazy danych i chcą przeprowadzić modernizację swoich aplikacji, przechodząc do chmury lub dla klientów, którzy już używają Azure SQL Database i chcą znacząco rozwijać możliwość wzrostu bazy danych. Skalowanie jest również przeznaczone dla klientów, którzy poszukują zarówno wysokiej wydajności, jak i wysokiej skalowalności. Dzięki funkcji skalowania uzyskasz następujące korzyści:

- Rozmiar bazy danych do 100 TB
- Szybkie kopie zapasowe bazy danych niezależnie od rozmiaru bazy danych (kopie zapasowe są oparte na migawkach magazynu)
- Szybka baza danych jest przywracana niezależnie od rozmiaru bazy danych (przywraca z migawek magazynu)
- Wyższa przepływność dzienników niezależnie od rozmiaru bazy danych i liczby rdzeni wirtualnych
- Odczytaj skalowanie w poziomie przy użyciu co najmniej jednej repliki tylko do odczytu, używanej do odciążania odczytu i jako rezerwy aktywnej.
- Szybkie skalowanie w górę obliczeń, w stałym czasie, do bardziej wydajnego, aby zapewnić duże obciążenie, a następnie skalować w dół w stałym czasie. Jest to podobne do skalowania w górę i w dół między P6 i P11, na przykład, ale znacznie szybciej, ponieważ nie jest to rozmiar operacji na danych.

### <a name="what-regions-currently-support-hyperscale"></a>Które regiony obsługują teraz skalowanie

Warstwa usługi do skalowania w górę jest obecnie dostępna w regionach wymienionych w obszarze [Przegląd preskalowania Azure SQL Database](sql-database-service-tier-hyperscale.md#regions).

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>Czy można utworzyć wiele baz danych na jednym serwerze logicznym

Tak. Aby uzyskać więcej informacji i limitów dotyczących liczby baz danych w skali na serwerze logicznym, zobacz [SQL Database limitów zasobów dla jednej i puli baz danych na serwerze logicznym](sql-database-resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Jakie są charakterystyki wydajności bazy danych w ramach skalowania

Architektura w ramach skalowania zapewnia wysoką wydajność i przepływność, a jednocześnie obsługuje duże rozmiary baz danych. 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Co to jest skalowalność bazy danych w ramach skalowania

Funkcja przedskalowania zapewnia szybką skalowalność na podstawie zapotrzebowania na obciążenie.

- **Skalowanie w górę/w dół**

  Korzystając ze skalowania, można skalować podstawowy rozmiar obliczeń w zakresie zasobów, takich jak procesor CPU i pamięć, a następnie skalować w dół w stałym czasie. Ponieważ magazyn jest współużytkowany, skalowanie w górę i skalowanie w dół nie jest rozmiarem operacji na danych.  
- **Skalowanie w górę/w dół**

  Dzięki funkcji wieloskalowaniu można również udostępniać jedną lub więcej dodatkowych replik obliczeniowych, których można użyć do obsługi żądań odczytu. Oznacza to, że można użyć tych dodatkowych replik obliczeniowych jako replik tylko do odczytu w celu odciążenia obciążenia odczytu od podstawowego obliczenia. Poza trybem tylko do odczytu te repliki również pełnią rolę rezerwy gorącą w przypadku przejścia do trybu failover z poziomu podstawowego.

  Inicjowanie obsługi każdej z tych dodatkowych replik obliczeniowych może odbywać się w stałym czasie i jest operacją online. Można połączyć się z tymi dodatkowymi replikami obliczeniowymi tylko do odczytu przez ustawienie argumentu `ApplicationIntent` w ciągu połączenia, aby `ReadOnly`. Wszystkie połączenia z zamiarem `ReadOnly` aplikacji są automatycznie kierowane do jednej z dodatkowych replik obliczeniowych tylko do odczytu.

## <a name="deep-dive-questions"></a>Głębokie pytania szczegółowe

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>Czy mogę mieszać pojedyncze bazy danych na jednym serwerze logicznym

Tak, możesz.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>Czy funkcja skalowania wymaga zmiany modelu programowania aplikacji

Nie, model programowania aplikacji pozostaje w takiej postaci. Parametry połączenia są używane w zwykły sposób, a inne, jak na bieżąco z bazami danych w skali.

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>Jaki poziom izolacji transakcji jest wartością domyślną w bazie danych w skali

W replice podstawowej domyślnym poziomem izolacji transakcji jest RCSI (odczyt zatwierdzono izolacja migawki). W odniesieniu do replik pomocniczych w skali odczytu domyślny poziom izolacji to migawka.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>Czy mogę uzyskać licencję lokalną lub IaaS SQL Server na potrzeby skalowania

Tak, [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) jest dostępny do skalowania. Każdy SQL Server Standard rdzeń można mapować na 1 rdzeni wirtualnych w skali. Każdy SQL Server Enterprise rdzeń można zamapować na 4 rdzeni wirtualnych. Nie potrzebujesz licencji SQL dla replik pomocniczych. Korzyść użycia hybrydowego platformy Azure cena zostanie automatycznie zastosowana do odczytu replik skalowalnych w poziomie (pomocniczych).

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>Jakiego rodzaju obciążenia są zaprojektowane dla

Funkcja skalowania obsługuje wszystkie SQL Server obciążenia, ale jest głównie zoptymalizowana pod kątem OLTP. Można również wprowadzać obciążenia hybrydowe (HTAP) i analityczne (składnicy danych).

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-azure-sql-database-hyperscale"></a>Jak mogę wybrać między Azure SQL Data Warehouse i skalowaniem Azure SQL Database

Jeśli obecnie uruchamiasz interaktywne zapytania analityczne przy użyciu SQL Server jako magazynu danych, funkcja wieloskalowania jest świetną opcją, ponieważ umożliwia hostowanie małych i średnich magazynów danych (takich jak kilka TB do 100 TB) przy niższych kosztach. można migrować SQL Server dane War ehouse obciążenia do skalowania przy minimalnych zmianach kodu T-SQL.

W przypadku korzystania z analizy danych na dużą skalę z złożonymi zapytaniami i pozyskanymi szybkościami pozyskiwania większymi niż 100 MB/s lub przy użyciu usługi Parallel Data Warehouse (PDW), Teradata lub innych magazynów danych z możliwością przetwarzania równoległego (MPP), SQL Data Warehouse mogą być najlepszym wyborem.
  
## <a name="hyperscale-compute-questions"></a>Pytania obliczeniowe w ramach skalowania

### <a name="can-i-pause-my-compute-at-any-time"></a>Czy mogę wstrzymywać obliczenia w dowolnym momencie

W tej chwili można jednak skalować obliczenia i liczbę replik w dół, aby zmniejszyć koszty w czasie poza szczytem.

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>Czy mogę zainicjować replikę obliczeniową z dodatkową ilością pamięci RAM w przypadku obciążeń intensywnie korzystających z pamięci

Nie. Aby uzyskać więcej pamięci RAM, należy przeprowadzić uaktualnienie do wyższego rozmiaru. Aby uzyskać więcej informacji, zobacz temat [skalowanie magazynu i rozmiarów obliczeniowych](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5).

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>Czy mogę zainicjować obsługę wielu replik obliczeniowych o różnych rozmiarach

Nie.

### <a name="how-many-read-scale-out-replicas-are-supported"></a>Ile replik skalowalnych w poziomie jest obsługiwanych

Bazy danych w ramach skalowania są tworzone z jedną repliką skalowalną w poziomie (dwie repliki, w tym podstawowe) domyślnie. Liczbę replik tylko do odczytu można skalować między 0 a 4 przy użyciu [Azure Portal](https://portal.azure.com) lub [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>Aby zapewnić wysoką dostępność, należy zastanowić się nad dodatkowymi replikami obliczeniowymi

W bazach danych w ramach skalowania odporność na dane jest zapewniana na poziomie magazynu. Wymagana jest tylko jedna replika w celu zapewnienia odporności. Gdy replika obliczeniowa nie działa, Nowa replika jest tworzona automatycznie bez utraty danych.

Jeśli jednak istnieje tylko jedna replika, może upłynąć trochę czasu, aby utworzyć lokalną pamięć podręczną w nowej replice po przejściu w tryb failover. W fazie odbudowywania pamięci podręcznej baza danych pobiera dane bezpośrednio z serwerów stronicowania, co skutkuje wyższym opóźnieniem magazynu i obniżeniem wydajności zapytań.

W przypadku aplikacji o krytycznym znaczeniu, które wymagają wysokiej dostępności przy minimalnym wpływie na pracę w trybie failover, należy udostępnić co najmniej 2 repliki obliczeniowe, w tym podstawową replikę obliczeniową. To jest konfiguracja domyślna. W ten sposób dostępna jest replika gorąca w stanie gotowości, która służy jako docelowy tryb failover.

## <a name="data-size-and-storage-questions"></a>Rozmiar danych i pytania dotyczące magazynu

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>Jaki jest maksymalny rozmiar bazy danych obsługiwany przez funkcja wieloskalowania

100 TB.

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Jaki jest rozmiar dziennika transakcji ze skalą

Dziennik transakcji ze skalą jest praktycznie nieskończony. Nie trzeba martwić się o uruchamianie miejsca w dzienniku w systemie, który ma wysoką przepływność dzienników. Jednak szybkość generowania dzienników może być ograniczona w przypadku ciągłego agresywnego zapisywania obciążeń. Szczytowa szybkość generowania dziennika to 100 MB/s.

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>`tempdb` skalowanie w miarę wzrostu rozmiaru bazy danych

Baza danych `tempdb` znajduje się w lokalnym magazynie dysków SSD i jest konfigurowana na podstawie wymaganego rozmiaru obliczeń. `tempdb` jest zoptymalizowany pod kątem zapewniania maksymalnej wydajności. nie można skonfigurować rozmiaru `tempdb` i jest on zarządzany przez Ciebie.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>Czy rozmiar bazy danych jest automatycznie zwiększany, czy muszę zarządzać rozmiarem plików danych

Rozmiar bazy danych jest automatycznie zwiększany podczas wstawiania/pozyskiwania większej ilości danych.

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>Co to jest najmniejszy rozmiar bazy danych, który jest obsługiwany przez funkcję preskalowania lub zaczyna się od

10 GB.

### <a name="in-what-increments-does-my-database-size-grow"></a>W jaki sposób zwiększa się rozmiar bazy danych

Każdy plik danych powiększa się o 10 GB. Jednocześnie można zwiększyć wiele plików danych.

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>Jest magazynem na poziomie lokalnym lub zdalnym.

W obszarze skalowanie pliki danych są przechowywane w usłudze Azure Storage w warstwie Standardowa. Dane są w pełni buforowane na lokalnym magazynie dysków SSD na serwerach stron, które znajdują się blisko replik obliczeniowych. Ponadto repliki obliczeń mają pamięć podręczną danych na lokalnym dysku SSD i w pamięci, aby zmniejszyć częstotliwość pobierania danych ze zdalnych serwerów stron.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Czy mogę zarządzać plikami lub grupami plików lub ich definiować przy użyciu funkcji skalowania

Nie. Pliki danych są dodawane automatycznie. Typowe przyczyny tworzenia dodatkowych grup plików nie dotyczą architektury magazynu w skali.

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Czy mogę zainicjować twarde zakończenie wzrostu ilości danych dla mojej bazy danych

Nie.

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>Jak pliki danych są ustanawiane za pomocą funkcji tworzenia i skalowania

Pliki danych są kontrolowane przez serwery stronicowania, z jednym serwerem stronicowania na plik danych. Wraz ze wzrostem rozmiaru danych dodawane są pliki danych i skojarzone serwery stron.

### <a name="is-database-shrink-supported"></a>Czy zmniejszenie rozmiaru bazy danych jest obsługiwane

Nie.

### <a name="is-data-compression-supported"></a>Czy kompresja danych jest obsługiwana

Tak, łącznie z kompresją wiersza, strony i magazynu kolumn.

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Jeśli mam ogromną tabelę, dane tabeli są rozłożone na wiele plików danych

Tak. Strony danych skojarzone z daną tabelą mogą kończyć się wieloma plikami danych, które są częścią tej samej grupy plików. SQL Server używa [strategii wypełnienia proporcjonalnego](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) do dystrybuowania danych za pośrednictwem plików danych.

## <a name="data-migration-questions"></a>Pytania dotyczące migracji danych

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>Czy mogę przenieść istniejące bazy danych Azure SQL Database do warstwy usługi w ramach skalowania

Tak. Istniejące bazy danych Azure SQL można przenieść do skalowania. Jest to jednokierunkowa migracja. Nie można przenieść baz danych ze skalowania do innej warstwy usług. W przypadku dowodu koncepcji (POCs) zalecamy wykonanie kopii bazy danych i migrację kopii do obszaru skalowania.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>Czy mogę przenieść bazy danych mojego skalowania do innych warstw usług

Nie. W tej chwili nie można przenieść bazy danych w ramach skalowania do innej warstwy usług.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Czy po migracji do warstwy usługi w ramach skalowania

Tak. Niektóre funkcje Azure SQL Database nie są jeszcze obsługiwane w ramach skalowania, w tym między innymi długoterminowe przechowywanie kopii zapasowych. Po przeprowadzeniu migracji baz danych do skalowania te funkcje przestaną działać.  Oczekujemy, że te ograniczenia będą tymczasowe.

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>Czy mogę przenieść lokalną bazę danych SQL Server lub moją SQL Serverą bazę danych na maszynę wirtualną w chmurze w celu przeskalowania

Tak. Możesz użyć wszystkich istniejących technologii migracji, aby przeprowadzić migrację do skalowania, w tym replikację transakcyjną, oraz inne technologie przenoszenia danych (kopia zbiorcza, Azure Data Factory, Azure Databricks, SSIS). Zobacz również [Azure Database Migration Service](../dms/dms-overview.md), który obsługuje wiele scenariuszy migracji.

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Jaki jest mój przestój podczas migracji ze środowiska lokalnego lub maszyny wirtualnej do skalowania i jak można go zminimalizować

Przestoje związane z migracją do funkcji skalowania są takie same, jak przestoje w przypadku migrowania baz danych do innych Azure SQL Database warstw usług. Za pomocą [replikacji transakcyjnej](replication-to-sql-database.md#data-migration-scenario
) można zminimalizować czas przestoju dla baz danych o rozmiarze do kilku TB. W przypadku bardzo dużych baz danych (10 + TB) można rozważyć Migrowanie danych przy użyciu funkcji ADF, Spark lub innych technologii przenoszenia danych.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>Ile czasu zajmie potrzeba przełączenia wartości X ilości danych do skali

Funkcja skalowania może zużywać 100 MB/s nowych/zmienionych danych, ale czas wymagany do przeniesienia danych do bazy danych Azure SQL ma wpływ na dostępną przepustowość sieci, szybkość odczytu źródła i docelowy poziom usługi bazy danych.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-sql-data-warehouse"></a>Czy mogę odczytywać dane z magazynu obiektów blob i wykonywać szybkie ładowanie (na przykład baza danych w SQL Data Warehouse)

Aplikacja kliencka może odczytywać dane z usługi Azure Storage i ładować obciążenia danych do bazy danych w formie wieloskali (podobnie jak w przypadku dowolnej innej usługi Azure SQL Database). Baza Base nie jest obecnie obsługiwana w Azure SQL Database. Alternatywnie, aby zapewnić szybkie ładowanie, można użyć [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/)lub użyć zadania spark w [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) z [łącznikiem Spark dla SQL](sql-database-spark-connector.md). Łącznik platformy Spark do bazy danych SQL obsługuje wstawianie zbiorcze.

Istnieje również możliwość zbiorczego odczytywania danych z magazynu obiektów blob platformy Azure przy użyciu BULK INSERT lub OPENROWSET: [przykłady dostępu zbiorczego do danych w usłudze Azure Blob Storage](https://docs.microsoft.com/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage?view=sql-server-2017#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location).

Proste odzyskiwanie lub model rejestrowania zbiorczego nie jest obsługiwany w ramach skalowania. Model odzyskiwania pełnego jest wymagany w celu zapewnienia wysokiej dostępności i odzyskiwania do punktu w czasie. Jednak architektura dziennika z przeskalowaniem zapewnia lepszą szybkość pozyskiwania danych w porównaniu z innymi Azure SQL Database warstwami usług.

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>Czy funkcja skalowania umożliwia obsługę wielu węzłów w celu równoległego pozyskiwania dużych ilości danych

Nie. Funkcja Moje skalowanie jest symetrycznej architektury przetwarzania wieloprocesowego (SMP) i nie jest masowym przetwarzaniem równoległym (MPP) ani architekturą wielu wzorców. Można utworzyć tylko wiele replik w celu skalowania obciążeń tylko do odczytu.

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>Co to jest najstarsza wersja SQL Server obsługiwana w przypadku migracji do skalowania

SQL Server 2005. Aby uzyskać więcej informacji, zobacz [Migrowanie do pojedynczej bazy danych lub bazy danych w puli](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database). Aby poznać problemy ze zgodnością, zobacz [Rozwiązywanie problemów ze zgodnością migracji bazy danych](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues).

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>Czy skalowanie obsługuje migrację z innych źródeł danych, takich jak Amazon Aurora, MySQL, PostgreSQL, Oracle, DB2 i innych platform baz danych

Tak. [Azure Database Migration Service](../dms/dms-overview.md) obsługuje wiele scenariuszy migracji.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Ciągłość działania i odzyskiwanie po awarii — pytania

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Jakie umowy SLA są udostępniane dla bazy danych w ramach skalowania

Zapoznaj się z umową [SLA dla Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/). Dodatkowe pomocnicze repliki obliczeniowe zwiększają dostępność, do 99,99% w przypadku bazy danych z co najmniej dwiema replikami obliczeniowymi.

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Są kopiami zapasowymi bazy danych zarządzanymi dla mnie przez usługę Azure SQL Database

Tak.

### <a name="how-often-are-the-database-backups-taken"></a>Jak często wykonywane są kopie zapasowe bazy danych

Nie ma tradycyjnych pełnych, różnicowych i dzienników kopii zapasowych dla baz danych w skali. Zamiast tego istnieją regularne migawki magazynu plików danych. Wygenerowany dziennik jest po prostu zachowywany jako skonfigurowany okres przechowywania, umożliwiając przywracanie do dowolnego punktu w czasie w okresie przechowywania.

### <a name="does-hyperscale-support-point-in-time-restore"></a>Czy przywracanie punktu pomocy technicznej w czasie

Tak.

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>Jaki jest cel punktu odzyskiwania (RPO)/Recovery cel (RTO) do przywracania bazy danych w ramach skalowania

Cel punktu odzyskiwania to 0 min. Cel RTO jest krótszy niż 10 minut, niezależnie od rozmiaru bazy danych. 

### <a name="do-backups-of-large-databases-affect-compute-performance-on-my-primary"></a>Czy kopie zapasowe dużych baz danych wpływają na wydajność obliczeń na serwerze podstawowym

Nie. Kopie zapasowe są zarządzane przez podsystem magazynowania i wykorzystują migawki magazynu. Nie wpływają one na obciążenie użytkownika podstawowego.

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>Czy można wykonać przywracanie geograficzne za pomocą bazy danych w ramach skalowania

Tak.  Przywracanie geograficzne jest w pełni obsługiwane.

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>Czy można skonfigurować replikację geograficzną za pomocą bazy danych ze skalowaniem

Obecnie nie.

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>Czy można wykonać kopię zapasową bazy danych i przywrócić ją na serwerze lokalnym lub na SQL Server na maszynie wirtualnej

Nie. Format magazynu dla baz danych w ramach skalowania różni się od żadnej wydanej wersji SQL Server i nie kontrolujesz kopii zapasowych ani nie masz do nich dostępu. Aby wyjmowanie danych z bazy danych wieloskali, można wyodrębnić dane przy użyciu dowolnych technologii przenoszenia danych, takich jak Azure Data Factory, Azure Databricks, SSIS itd.

## <a name="cross-feature-questions"></a>Pytania dotyczące wielu funkcji

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Czy po migracji do warstwy usługi w ramach skalowania

Tak. Niektóre funkcje Azure SQL Database nie są obsługiwane w ramach skalowania, w tym między innymi długoterminowe przechowywanie kopii zapasowych. Po przeprowadzeniu migracji baz danych do skalowania te funkcje przestaną działać.

### <a name="will-polybase-work-with-hyperscale"></a>Czy baza będzie działała ze skalą

Nie. Baza Base nie jest obsługiwana w Azure SQL Database.

### <a name="does-hyperscale-have-support-for-r-and-python"></a>Czy funkcja wieloskalowania obsługuje język R i Python

Nie. Język R i Python nie są obsługiwane w Azure SQL Database.

### <a name="are-compute-nodes-containerized"></a>Czy węzły obliczeniowe są kontenerami

Nie. Procesy skalowania są uruchamiane na [Service Fabric](https://azure.microsoft.com/services/service-fabric/) węzłach (maszynach wirtualnych), a nie w kontenerach.

## <a name="performance-questions"></a>Pytania dotyczące wydajności

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>Jak dużo przepływności zapisu można wypchnąć w bazie danych w skali

Limit przepływności dziennika transakcji jest ustawiany na 100 MB/s dla dowolnego rozmiaru obliczeń w skali. Możliwość osiągnięcia tego współczynnika zależy od wielu czynników, w tym między innymi typu obciążenia, konfiguracji klienta i posiadania wystarczającej pojemności obliczeniowej w podstawowej replice obliczeniowej w celu utworzenia dziennika na tym wskaźniku.

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>Ile operacji we/wy na największe obliczenie

Liczba operacji we/wy i czas oczekiwania na sekundę różnią się w zależności od wzorców obciążenia. Jeśli dostęp do danych jest buforowany w replice obliczeniowej, zostanie wyświetlona taka sama wydajność we/wy jak w przypadku lokalnego dysku SSD.

### <a name="does-my-throughput-get-affected-by-backups"></a>Czy na moją przepływność wpływają kopie zapasowe

Nie. Obliczenia są oddzielone od warstwy magazynowania. Eliminuje to wpływ na wydajność tworzenia kopii zapasowych.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>Czy moja przepływność jest narażona na dostęp w przypadku udostępniania dodatkowych replik obliczeniowych

Ponieważ magazyn jest współużytkowany i nie ma żadnej bezpośredniej replikacji fizycznej między podstawową i pomocniczą repliką obliczeniową, techniczną nie wpłynie to na przepływność na replice podstawowej. Firma Microsoft może jednak ograniczyć ciągłość zapisywania obciążenia, aby umożliwić wykonywanie dzienników na replikach pomocniczych i serwerach stron, a nawet uniknąć niskiej wydajności odczytu w replikach pomocniczych.

## <a name="scalability-questions"></a>Pytania dotyczące skalowalności

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>Jak długo trwa skalowanie w górę i w dół repliki obliczeniowej

Skalowanie obliczeniowe w górę lub w dół powinno trwać 5-10 minut niezależnie od rozmiaru danych.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Czy moja baza danych jest w trybie offline, gdy trwa skalowanie w górę/w dół

Nie. Skalowanie w górę i w dół będzie przełączane w tryb online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Czy należy oczekiwać, że połączenie jest porzucane, gdy operacje skalowania są w toku

Skalowanie w górę lub w dół powoduje, że istniejące połączenia są usuwane, gdy nastąpi przejście do trybu failover na końcu operacji skalowania. Dodanie replik pomocniczych nie powoduje porzucania połączeń.

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>To skalowanie w górę i w dół replik obliczeniowych automatycznie lub dla operacji wyzwalanej przez użytkownika końcowego

Użytkownik końcowy. Nie automatycznie.  

### <a name="does-the-size-of-my-tempdb-database-also-grow-as-the-compute-is-scaled-up"></a>Czy rozmiar bazy danych `tempdb` zwiększa się również, gdy obliczenia są skalowane

Tak. `tempdb` baza danych będzie skalowana automatycznie w miarę wzrostu obliczeń.  

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Czy można zainicjować obsługę wielu podstawowych replik obliczeniowych, takich jak system z wieloma wzorcami, gdzie wiele podstawowych głowic obliczeniowych może obsługiwać wyższy poziom współbieżności

Nie. Tylko podstawowa replika obliczeniowa akceptuje żądania odczytu i zapisu. Pomocnicze repliki obliczeniowe akceptują tylko żądania tylko do odczytu.

## <a name="read-scale-out-questions"></a>Odczytuj pytania skalowalne w poziomie

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>Ile pomocniczych replik obliczeniowych można udostępnić

Domyślnie tworzymy jedną replikę pomocniczą dla baz danych. Jeśli chcesz dostosować liczbę replik, możesz to zrobić za pomocą [Azure Portal](https://portal.azure.com) lub [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>Jak mogę połączyć się z tymi dodatkowymi replikami obliczeniowymi

Można połączyć się z tymi dodatkowymi replikami obliczeniowymi tylko do odczytu przez ustawienie argumentu `ApplicationIntent` w ciągu połączenia, aby `ReadOnly`. Wszystkie połączenia oznaczone `ReadOnly` są automatycznie kierowane do jednej z dodatkowych replik obliczeniowych tylko do odczytu.  

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>Jak mogę sprawdzić, czy pomyślnie nawiązano połączenie z pomocniczą repliką obliczeń przy użyciu programu SSMS lub innych narzędzi klienta?

Można wykonać następujące zapytanie T-SQL: `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')`.
Wynik jest `READ_ONLY`, jeśli masz połączenie z repliką pomocniczą tylko do odczytu i `READ_WRITE`, jeśli nawiązano połączenie z repliką podstawową. Należy pamiętać, że kontekst bazy danych musi być ustawiony na nazwę bazy danych w ramach skalowania, a nie do bazy danych `master`.

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>Czy można utworzyć dedykowany punkt końcowy dla repliki skalowalnej w poziomie

Nie. Można łączyć się tylko z odczytaniem replik skalowalnych w poziomie przez określenie `ApplicationIntent=ReadOnly`.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>Czy system ma inteligentne Równoważenie obciążenia podczas odczytu obciążenia

Nie. Połączenie z intencją tylko do odczytu jest przekierowywane do dowolnej repliki skalowalnej w poziomie.

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>Czy można skalować w górę/w dół pomocnicze repliki obliczeniowe niezależnie od repliki podstawowej

Nie. Pomocnicza replika obliczeniowa jest również używana jako cele trybu failover wysokiej dostępności, dlatego muszą mieć taką samą konfigurację jak podstawowa, aby zapewnić oczekiwaną wydajność po przejściu do trybu failover.

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>Czy mogę uzyskać różne rozmiary `tempdb` dla moich podstawowych obliczeń i dodatkowych pomocniczych replik obliczeniowych

Nie. Baza danych `tempdb` jest konfigurowana na podstawie obsługi rozmiaru obliczeń, ale pomocnicze repliki obliczeniowe są takie same jak w przypadku podstawowego obliczenia.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>Czy mogę dodawać indeksy i widoki w ramach pomocniczych replik obliczeń

Nie. Bazy danych w ramach skalowania mają magazyn udostępniony, co oznacza, że wszystkie repliki obliczeniowe zobaczą te same tabele, indeksy i widoki. Jeśli chcesz, aby dodatkowe indeksy były zoptymalizowane pod kątem odczytów na serwerze pomocniczym, należy je dodać na serwerze podstawowym.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>Jak dużo opóźnić między podstawową i pomocniczą repliką obliczeniową

Od momentu, gdy transakcja jest zatwierdzana na poziomie podstawowym, w zależności od bieżącej stawki generacji dziennika, może być chwilowo lub w niskiej wartości milisekundowej.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat warstwy usługi do skalowania, zobacz sekcję [skalowanie warstwy usług](sql-database-service-tier-hyperscale.md).
