---
title: Limity zasobów — wystąpienie zarządzane
description: Ten artykuł zawiera omówienie limitów zasobów Azure SQL Database dla wystąpień zarządzanych.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 02/25/2020
ms.openlocfilehash: 12d457d8d5e57dc4db16d9a191c7795a5f013574
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605014"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Przegląd Azure SQL Database limitów zasobów wystąpienia zarządzanego

Ten artykuł zawiera omówienie parametrów technicznych i limitów zasobów dla Azure SQL Database wystąpienia zarządzanego i zawiera informacje o sposobach żądania wzrostu do tych limitów.

> [!NOTE]
> Aby uzyskać różnice w obsługiwanych funkcjach i instrukcjach języka T-SQL, zobacz temat [różnice w funkcjach](sql-database-features.md) i [Obsługa instrukcji języka t-SQL](sql-database-managed-instance-transact-sql-information.md). Aby uzyskać ogólną różnicę między warstwami usług w pojedynczej bazie danych i wystąpieniu zarządzanym, zobacz [porównanie warstwy usług](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Charakterystyki generowania sprzętu

Wystąpienie zarządzane ma cechy i limity zasobów, które są zależne od podstawowej infrastruktury i architektury. Wystąpienie zarządzane Azure SQL Database można wdrożyć na dwóch generacjach sprzętu: obliczenia i 5 rdzeń. Generacji sprzętu mają różne cechy, zgodnie z opisem w poniższej tabeli:

|   | **Obliczenia** | **5 rdzeń** |
| --- | --- | --- |
| Sprzęt | Procesor Intel E5-2673 v3 (Haswell) 2,4 GHz, dołączony dysk SSD rdzeń wirtualny = 1 PP (rdzeń fizyczny) | Procesor Intel E5-2673 v4 (Broadwell) 2,3-GHz i Intel SP-8160 (Skylake), Fast NVMe SSD, rdzeń wirtualny = 1 LP (Hyper-Thread) |
| Liczba rdzeni wirtualnych | 8, 16, 24 rdzeni wirtualnych | 4, 8, 16, 24, 32, 40, 64, 80 rdzeni wirtualnych |
| Maksymalna ilość pamięci (stosunek pamięci/rdzeni) | 7 GB na rdzeń wirtualny<br/>Aby uzyskać więcej pamięci, Dodaj więcej rdzeni wirtualnych. | 5,1 GB na rdzeń wirtualny<br/>Aby uzyskać więcej pamięci, Dodaj więcej rdzeni wirtualnych. |
| Maksymalna pamięć OLTP w pamięci | Limit wystąpień: 1 – 1,5 GB na rdzeń wirtualny| Limit wystąpień: 0,8 – 1,65 GB na rdzeń wirtualny |
| Maksymalna ilość zarezerwowanego wystąpienia |  Ogólnego przeznaczenia: 8 TB<br/>Krytyczne dla działania firmy: 1 TB | Ogólnego przeznaczenia: 8 TB<br/> Krytyczne dla działania firmy 1 TB, 2 TB lub 4 TB w zależności od liczby rdzeni |

> [!IMPORTANT]
> - Sprzęt obliczenia jest stopniowo wycofywany i nie jest już dostępny dla nowych wdrożeń. Wszystkie nowe wystąpienia zarządzane muszą zostać wdrożone na sprzęcie 5 rdzeń.
> - Rozważ [przeniesienie wystąpień zarządzanych do sprzętu gen 5](sql-database-service-tiers-vcore.md) w celu uzyskania szerszego zakresu skalowalności rdzeń wirtualny i pamięci masowej, przyspieszonej sieci, najlepszej wydajności operacji we/wy i minimalnych opóźnień.

### <a name="in-memory-oltp-available-space"></a>Dostępne miejsce w pamięci OLTP 

Ilość miejsca OLTP w pamięci w [krytyczne dla działania firmy](sql-database-service-tier-business-critical.md) warstwy usług zależy od liczby rdzeni wirtualnych i generowania sprzętu. W poniższej tabeli wymieniono limity pamięci, które mogą być używane dla obiektów OLTP w pamięci.

| Przestrzeń OLTP w pamięci  | **5 rdzeń** | **Obliczenia** |
| --- | --- | --- |
| 4 rdzeni wirtualnych  | 3,14 GB | |   
| 8 rdzeni wirtualnych  | 6,28 GB | 8 GB |
| 16 rdzeni wirtualnych | 15,77 GB | 20 GB |
| 24 rdzeni wirtualnych | 25,25 GB | 36 GB |
| 32 rdzeni wirtualnych | 37,94 GB | |
| 40 rdzeni wirtualnych | 52,23 GB | |
| 64 rdzeni wirtualnych | 99,9 GB    | |
| 80 rdzeni wirtualnych | 131,68 GB| |

## <a name="service-tier-characteristics"></a>Charakterystyki warstwy usług

Wystąpienie zarządzane ma dwie warstwy usług: [ogólnego przeznaczenia](sql-database-service-tier-general-purpose.md) i [krytyczne dla działania firmy](sql-database-service-tier-business-critical.md). Te warstwy zapewniają [różne możliwości](sql-database-service-tiers-general-purpose-business-critical.md), zgodnie z opisem w poniższej tabeli.

> [!Important]
> Krytyczne dla działania firmy warstwa usługi oferuje dodatkową wbudowaną kopię wystąpienia (replikę pomocniczą), która może być używana do obsługi obciążeń tylko do odczytu. Jeśli możesz oddzielić zapytania odczytu i zapisu oraz zapytania tylko do odczytu/analizy/raportowania, otrzymujesz dwa razy rdzeni wirtualnych i pamięć za tę samą cenę. Replika pomocnicza może potrwać kilka sekund za wystąpieniem podstawowym, więc jest przeznaczona do odciążania obciążeń raportowanie/analitycznych, które nie wymagają dokładnego stanu danych. W poniższej tabeli **zapytania tylko do odczytu** są zapytania, które są wykonywane w replice pomocniczej.

| **Funkcja** | **Ogólnego przeznaczenia** | **Krytyczne dla działania firmy** |
| --- | --- | --- |
| Liczba rdzeni wirtualnych\* | Obliczenia: 8, 16, 24<br/>5 rdzeń: 4, 8, 16, 24, 32, 40, 64, 80 | Obliczenia: 8, 16, 24 <br/> 5 rdzeń: 4, 8, 16, 24, 32, 40, 64, 80 <br/>\*ta sama liczba rdzeni wirtualnych jest przeznaczona dla zapytań tylko do odczytu. |
| Maksymalna pamięć | Obliczenia: 56 GB – 168 GB (7GB/rdzeń wirtualny)<br/>5 rdzeń: 20,4 GB – 408 GB (5.1 GB/rdzeń wirtualny)<br/>Aby uzyskać więcej pamięci, Dodaj więcej rdzeni wirtualnych. | Obliczenia: 56 GB – 168 GB (7GB/rdzeń wirtualny)<br/>5 rdzeń: 20,4 GB-408 GB (5.1 GB/rdzeń wirtualny) dla zapytań do odczytu i zapisu<br/>+ dodatkowe 20,4 GB – 408 GB (5.1 GB/rdzeń wirtualny) dla zapytań tylko do odczytu.<br/>Aby uzyskać więcej pamięci, Dodaj więcej rdzeni wirtualnych. |
| Maksymalny rozmiar magazynu wystąpienia (zarezerwowany) | -2 TB dla 4 rdzeni wirtualnych (tylko 5 rdzeń)<br/>-8 TB dla innych rozmiarów | Obliczenia: 1 TB <br/> 5 rdzeń <br/>-1 TB dla 4, 8, 16 rdzeni wirtualnych<br/>-2 TB przez 24 rdzeni wirtualnych<br/>-4 TB dla 32, 40, 64, 80 rdzeni wirtualnych |
| Maksymalny rozmiar bazy danych | Do aktualnie dostępnego rozmiaru wystąpienia (maksymalnie 2 TB – 8 TB w zależności od liczby rdzeni wirtualnych). | Do aktualnie dostępnego rozmiaru wystąpienia (maksymalnie 1 TB — 4 TB w zależności od liczby rdzeni wirtualnych). |
| Maksymalny rozmiar bazy danych tempDB | Ograniczone do 24 GB/rdzeń wirtualny (96 – 1 920 GB) i aktualnie dostępnego rozmiaru magazynu wystąpień.<br/>Dodaj więcej rdzeni wirtualnych, aby uzyskać więcej przestrzeni TempDB.<br/> Rozmiar pliku dziennika jest ograniczony do 120 GB.| Do aktualnie dostępnego rozmiaru magazynu wystąpień. |
| Maksymalna liczba baz danych na wystąpienie | 100, chyba że osiągnięto limit rozmiaru magazynu wystąpień. | 100, chyba że osiągnięto limit rozmiaru magazynu wystąpień. |
| Maksymalna liczba plików bazy danych na wystąpienie | Do 280, o ile nie osiągnięto rozmiaru magazynu wystąpienia lub limitu [przestrzeni dyskowej usługi Azure Premium](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files) . | 32 767 plików na bazę danych, o ile nie osiągnięto limitu rozmiaru magazynu wystąpień. |
| Maksymalny rozmiar pliku danych | Ograniczone do aktualnie dostępnego rozmiaru magazynu wystąpień (maksymalnie 2 TB-8 TB) i [miejsca alokacji dysku Azure Premium Storage](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files). | Ograniczone do aktualnie dostępnego rozmiaru magazynu wystąpień (do 1 TB – 4 TB). |
| Maksymalny rozmiar pliku dziennika | Ograniczone do 2 TB i aktualnie dostępnego rozmiaru magazynu wystąpień. | Ograniczone do 2 TB i aktualnie dostępnego rozmiaru magazynu wystąpień. |
| Operacje we/wy danych/dziennika (przybliżone) | Do 30-40 K operacji we/wy na wystąpienie *, 500-7500 na plik<br/>\*[zwiększyć rozmiar pliku, aby uzyskać więcej operacji we/wy na sekundę](#file-io-characteristics-in-general-purpose-tier)| 10 k-200 K (2500 IOPS/rdzeń wirtualny)<br/>Dodaj więcej rdzeni wirtualnych, aby uzyskać lepszą wydajność operacji we/wy. |
| Limit przepływności zapisu dziennika (na wystąpienie) | 3 MB/s na rdzeń wirtualny<br/>Maks. 22 MB/s | 4 MB/s na rdzeń wirtualny<br/>Maks 48 MB/s |
| Przepływność danych (przybliżona) | 100 – 250 MB/s na plik<br/>\*[zwiększyć rozmiar pliku, aby uzyskać lepszą wydajność operacji we/wy](#file-io-characteristics-in-general-purpose-tier) | Nieograniczone. |
| Opóźnienie operacji we/wy magazynu (w przybliżeniu) | 5-10 ms | 1-2 MS |
| Przetwarzanie OLTP danych w pamięci | Nieobsługiwane | Dostępne, [rozmiar zależy od liczby rdzeń wirtualny](#in-memory-oltp-available-space) |
| Maksymalna liczba sesji | 30000 | 30000 |
| [Repliki tylko do odczytu](sql-database-read-scale-out.md) | 0 | 1 (wliczone w cenę) |

> [!NOTE]
> - **Obecnie dostępny rozmiar magazynu wystąpienia** to różnica między zarezerwowanym rozmiarem wystąpienia i ilością miejsca do magazynowania.
> - Rozmiar pliku danych i dziennika w bazach danych użytkownika i systemu jest uwzględniany w rozmiarze magazynu wystąpienia, który jest porównywany z maksymalnym limitem rozmiaru magazynu. Użyj widoku <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys. master_files</a> system w celu określenia łącznej ilości miejsca używanego przez bazy danych. Dzienniki błędów nie są utrwalane i nie zostały uwzględnione w rozmiarze. Kopie zapasowe nie są uwzględniane w rozmiarze magazynu.
> - Przepływność i operacje we/wy w warstwie Ogólnego przeznaczenia również zależą od [rozmiaru pliku](#file-io-characteristics-in-general-purpose-tier) , który nie jest jawnie ograniczony przez wystąpienie zarządzane.
> - Można utworzyć kolejną replikę do odczytu w innym regionie świadczenia usługi Azure przy użyciu grup Autotryb failover.
> - Maksymalna liczba operacji we/wy wystąpienia zależy od układu pliku i rozkładu obciążenia. Jeśli na przykład utworzysz 7 x 1 TB plików z maksymalną 5 K IOPS każdy i 7 małych plików (mniejszym niż 128 GB) z 500 IOPS każdy, możesz uzyskać 38500 operacji we/wy na wystąpienie (7x5000 + 7x500), jeśli obciążenie może korzystać ze wszystkich plików. Należy zauważyć, że niektóre liczby operacji we/wy są również używane do tworzenia kopii zapasowych.

> [!NOTE]
> Więcej informacji na temat [limitów zasobów w pulach wystąpień zarządzanych w tym artykule](sql-database-instance-pools.md#instance-pools-resource-limitations).

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Charakterystyka we/wy pliku w warstwie Ogólnego przeznaczenia

W Ogólnego przeznaczenia warstwy usług każdy plik bazy danych uzyskuje dedykowane operacje we/wy i przepływność, które są zależne od rozmiaru pliku. Większe pliki zwiększają liczbę IOPS i przepływność. Właściwości we/wy plików bazy danych przedstawiono w poniższej tabeli:

| Rozmiar pliku | > = 0 i < = 128 GiB | > 128 i < = 256 GiB | > 256 i < = 512 GiB | > 0,5 i < = 1 TiB    | > 1 i < = 2 TiB    | > 2 i < = 4 TiB | > 4 i < = 8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Liczba operacji we/wy na plik       | 500   | 1100 | 2300              | 5000              | 7500              | 7500              | 12 500   |
| Przepływność na plik | 100 MiB/s | 125 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s | 480 MiB/s | 

Jeśli zauważysz wysokie opóźnienia we/wy dla niektórych plików bazy danych lub widzisz, że liczba IOPS/przepływność osiąga limit, można zwiększyć wydajność, [zwiększając rozmiar pliku](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337).

Istnieją również limity na poziomie wystąpienia, takie jak maksymalna przepływność zapisu dziennika 22 MB/s, dzięki czemu możesz nie mieć możliwości uzyskania dostępu do pliku w pliku dziennika, ponieważ zbliża się limit przepływności wystąpienia.

## <a name="supported-regions"></a>Obsługiwane regiony

Wystąpienia zarządzane można tworzyć tylko w [obsługiwanych regionach](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Aby utworzyć wystąpienie zarządzane w regionie, który nie jest obecnie obsługiwany, można [wysłać żądanie pomocy technicznej za pośrednictwem Azure Portal](quota-increase-request.md).

## <a name="supported-subscription-types"></a>Obsługiwane typy subskrypcji

Wystąpienie zarządzane obecnie obsługuje tylko wdrożenie następujących typów subskrypcji:

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Dostawca usług w chmurze (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Subskrypcje z miesięcznymi środkami platformy Azure dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Ograniczenia zasobów regionalnych

Obsługiwane typy subskrypcji mogą zawierać ograniczoną liczbę zasobów na region. Wystąpienie zarządzane ma dwa domyślne limity dla regionu platformy Azure (które można zwiększyć na żądanie, tworząc specjalne [żądanie pomocy technicznej w Azure Portal w](quota-increase-request.md) zależności od typu subskrypcji:

- **Limit podsieci**: Maksymalna liczba podsieci, w których wystąpienia zarządzane są wdrażane w jednym regionie.
- **limit jednostek rdzeń wirtualny**: Maksymalna liczba jednostek rdzeń wirtualny, które mogą zostać wdrożone we wszystkich wystąpieniach w jednym regionie. Jedna z zasad GP rdzeń wirtualny korzysta z jednej jednostki rdzeń wirtualny, a jedna z nich ma rdzeń wirtualny rdzeń wirtualny jednostek. Łączna liczba wystąpień nie jest ograniczona, o ile mieści się w limicie jednostek rdzeń wirtualny.

> [!Note]
> Te limity to ustawienia domyślne, a nie ograniczenia techniczne. Limity można zwiększyć na żądanie, tworząc specjalne [żądanie pomocy technicznej w Azure Portal,](quota-increase-request.md) Jeśli potrzebujesz więcej wystąpień zarządzanych w bieżącym regionie. Alternatywnie można tworzyć nowe wystąpienia zarządzane w innym regionie świadczenia usługi Azure bez wysyłania żądań pomocy technicznej.

W poniższej tabeli przedstawiono **domyślne limity** dla obsługiwanych typów subskrypcji (domyślne limity można rozszerzyć przy użyciu żądania pomocy technicznej opisanego poniżej):

|Typ subskrypcji| Maksymalna liczba podsieci wystąpienia zarządzanego | Maksymalna liczba jednostek rdzeń wirtualny * |
| :---| :--- | :--- |
|Płatność zgodnie z rzeczywistym użyciem|3|320|
|CSP |8 (15 w niektórych regionach * *)|960 (1440 w niektórych regionach * *)|
|Płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie|3|320|
|Enterprise — tworzenie i testowanie|3|320|
|EA|8 (15 w niektórych regionach * *)|960 (1440 w niektórych regionach * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional i Platformy MSDN|2|32|

\* w planowaniu wdrożeń, weź pod uwagę, że warstwa usług Krytyczne dla działania firmy (BC) wymaga czterech (4) razy więcej rdzeń wirtualny pojemności niż w warstwie usług Ogólnego przeznaczenia (GP). Na przykład: 1 GP rdzeń wirtualny = 1 rdzeń wirtualny Unit i 1 BC rdzeń wirtualny = 4 jednostki rdzeń wirtualny. Aby uprościć analizę zużycia pod kątem domyślnych limitów, należy podsumować jednostki rdzeń wirtualny we wszystkich podsieciach w regionie, w którym wdrożono wystąpienia zarządzane i porównać wyniki z limitami jednostek wystąpienia dla danego typu subskrypcji. **Maksymalna liczba jednostek rdzeń wirtualny** dotyczy każdej subskrypcji w regionie. Nie ma żadnego limitu dla poszczególnych podsieci, z tą różnicą, że suma wszystkich rdzeni wirtualnych wdrożonych w wielu podsieciach musi być mniejsza lub równa **maksymalnej liczbie jednostek rdzeń wirtualny**.

\*\* większe limity podsieci i rdzeń wirtualny są dostępne w następujących regionach: Australia Wschodnia, Wschodnie stany USA, Wschodnie stany USA 2, Europa Północna, Południowo-środkowe stany USA, Azja Południowo-Wschodnia Południowe Zjednoczone Królestwo, Europa Zachodnia, zachodnie stany USA 2.

## <a name="request-a-quota-increase-for-sql-managed-instance"></a>Zażądaj zwiększenia limitu przydziału dla wystąpienia zarządzanego SQL

Jeśli potrzebujesz więcej wystąpień zarządzanych w Twoich bieżących regionach, Wyślij żądanie obsługi, aby zwiększyć przydział przy użyciu Azure Portal. Aby uzyskać więcej informacji, zobacz [zwiększenie przydziału żądań dla Azure SQL Database](quota-increase-request.md).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji o wystąpieniu zarządzanym, zobacz [co to jest wystąpienie zarządzane?](sql-database-managed-instance.md)
- Aby uzyskać informacje o cenach, zobacz [SQL Database cenach wystąpienia zarządzanego](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Aby dowiedzieć się, jak utworzyć pierwsze wystąpienie zarządzane, zobacz [Przewodnik Szybki Start](sql-database-managed-instance-get-started.md).
