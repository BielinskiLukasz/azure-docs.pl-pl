---
title: Przydziały usługi Azure Cosmos DB
description: Azure Cosmos DB przydziały usługi i domyślne limity dla różnych typów zasobów.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 2cce9cbe20121376ad77692773df1d58fcf5257f
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759776"
---
# <a name="azure-cosmos-db-service-quotas"></a>Przydziały usługi Azure Cosmos DB

Ten artykuł zawiera omówienie domyślnych przydziałów oferowanych na potrzeby różnych zasobów w usłudze Azure Cosmos DB.

## <a name="storage-and-throughput"></a>Magazyn i przepływność

Po utworzeniu konta usługi Azure Cosmos w ramach subskrypcji możesz zarządzać danymi na swoim koncie przez [Tworzenie baz danych, kontenerów i elementów](databases-containers-items.md). Przepustowość można zainicjować na poziomie kontenera lub na poziomie bazy danych w warunkach [jednostek żądania (ru/s lub jednostek ru)](request-units.md). W poniższej tabeli wymieniono limity dotyczące magazynu i przepływności dla kontenera/bazy danych.

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalna jednostek ru na kontener ([tryb aprowizacji dedykowanej przepustowości](databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 domyślnie. Możesz ją zwiększyć, [bilet pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) |
| Maksymalna liczba jednostek ru na bazę danych ([tryb aprowizacji udostępnionej przepustowości](databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 domyślnie. Możesz ją zwiększyć, [bilet pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) |
| Maksymalny jednostek ru na (logiczny) klucz partycji | 10 000 |
| Maksymalny rozmiar magazynu dla wszystkich elementów na (logiczne) klucza partycji| 10 GB |
| Maksymalna liczba różnych (logicznych) kluczy partycji | Bez ograniczeń |
| Maksymalny rozmiar magazynu na kontener | Bez ograniczeń |
| Maksymalny rozmiar magazynu na bazę danych | Bez ograniczeń |
| Maksymalny rozmiar załączników na konto (funkcja załącznika jest amortyzowana) | 2 GB |
| Minimalna jednostek ru wymagana na 1 GB | 10 RU/s |

> [!NOTE]
> Aby dowiedzieć się więcej o najlepszych rozwiązaniach dotyczących zarządzania obciążeniami z kluczami partycji wymagającymi wyższych limitów dla magazynu lub przepływności, zobacz [Tworzenie klucza partycji syntetycznej](synthetic-partition-keys.md).
>

Kontener Cosmos (lub udostępniona baza danych przepływności) musi mieć minimalną przepływność wynoszącą 400 jednostek ru. Wraz z rozwojem kontenera Minimalna obsługiwana przepływność zależy również od następujących czynników:

* Minimalna przepływność, którą można ustawić dla kontenera, zależy od maksymalnej przepływności, która jest kiedykolwiek obsługiwana w kontenerze. Na przykład jeśli przepustowość została zwiększona do 10000 jednostek ru, najniższa możliwa przepustowość zainicjowana to 1000 jednostek ru
* Minimalna przepływność dla udostępnionej bazy danych przepływności zależy również od całkowitej liczby kontenerów utworzonych kiedykolwiek w udostępnionej bazie danych przepływności, mierzoną przy 100 jednostek ru na kontener. Jeśli na przykład utworzono pięć kontenerów w ramach udostępnionej bazy danych przepływności, przepływność musi wynosić co najmniej 500 jednostek ru

Bieżącą i minimalną przepływność kontenera lub bazy danych można pobrać z Azure Portal lub zestawów SDK. Aby uzyskać więcej informacji, zobacz temat [udostępnianie przepływności na kontenerach i bazach danych](set-throughput.md). 

> [!NOTE]
> W niektórych przypadkach może być możliwe obniżenie przepływności do mniejszej niż 10%. Użyj interfejsu API w celu uzyskania dokładnej minimalnej jednostek ru na kontener.
>

Poniżej znajduje się podsumowanie minimalnych limitów przydziałów RU. 

| Zasób | Limit domyślny |
| --- | --- |
| Minimalna jednostek ru na kontener ([tryb aprowizacji dedykowanej przepustowości](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Minimalna jednostek ru na bazę danych ([tryb aprowizacji udostępnionej przepustowości](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Minimalna jednostek ru na kontener w ramach udostępnionej bazy danych przepływności | 100 |

Cosmos DB obsługuje elastyczne skalowanie przepływności (jednostek ru) na kontener lub bazę danych za pośrednictwem zestawów SDK lub portalu. Każdy kontener można skalować synchronicznie i od razu w zakresie od 10 do 100 razy od wartości minimalnej i maksymalnej. Jeśli żądana wartość przepływności znajduje się poza zakresem, skalowanie jest wykonywane asynchronicznie. Skalowanie asynchroniczne może zająć kilka minut, w zależności od wymaganej przepływności i rozmiaru magazynu danych w kontenerze.  

## <a name="control-plane-operations"></a>Operacje płaszczyzny kontroli

Możesz [zainicjować konto usługi Azure Cosmos i zarządzać](how-to-manage-database-account.md) nim przy użyciu szablonów Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy azure i Azure Resource Manager. W poniższej tabeli wymieniono limity dla każdej subskrypcji, konta i liczby operacji.

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalna liczba kont bazy danych na subskrypcję | 50 domyślnie. Możesz ją zwiększyć, [bilet pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|
| Maksymalna liczba regionalnych trybu failover | Domyślnie 1 godzinę. Możesz ją zwiększyć, [bilet pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|

> [!NOTE]
> Regionalne przejścia w tryb failover mają zastosowanie tylko do jednego regionu zapisy konta. Konta zapisu w regionie wieloregionowym nie wymagają ani nie mają ograniczeń dotyczących zmiany regionu zapisu.

Cosmos DB automatycznie pobiera kopie zapasowe danych w regularnych odstępach czasu. Aby uzyskać szczegółowe informacje na temat interwałów przechowywania i okien kopii zapasowych, zobacz [kopia zapasowa online i przywracanie danych na żądanie w Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-account-limits"></a>Limity dla konta

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalna liczba baz danych | Bez ograniczeń |
| Maksymalna liczba kontenerów na bazę danych (lub konto) | Bez ograniczeń |
| Maksymalna liczba regionów | Brak limitu (wszystkie regiony platformy Azure) |

## <a name="per-container-limits"></a>Limity dla kontenerów

W zależności od używanego interfejsu API kontener usługi Azure Cosmos może reprezentować kolekcję, tabelę lub wykres. Kontenery obsługują konfiguracje dla [ograniczeń unique Key](unique-keys.md), [procedur składowanych, wyzwalaczy i UDF](stored-procedures-triggers-udfs.md)oraz [zasad indeksowania](how-to-manage-indexing-policy.md). W poniższej tabeli wymieniono limity specyficzne dla konfiguracji w kontenerze. 

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalna długość nazwy bazy danych lub kontenera | 255 |
| Maksymalna liczba procedur składowanych na kontener | 100 <sup>*</sup>|
| Maksymalna UDF na kontener | 25 <sup>*</sup>|
| Maksymalna liczba ścieżek w zasadach indeksowania| 100 <sup>*</sup>|
| Maksymalna liczba unikatowych kluczy na kontener|10 <sup>*</sup>|
| Maksymalna liczba ścieżek na ograniczenie unikatowego klucza|16 <sup>*</sup>|

<sup>*</sup> Można zwiększyć dowolne z tych limitów kontenerów, kontaktując się z pomocą techniczną platformy Azure.

## <a name="per-item-limits"></a>Limity dla każdego elementu

W zależności od używanego interfejsu API element platformy Azure Cosmos może reprezentować dokument w kolekcji, wiersz w tabeli lub węzeł lub krawędź wykresu. W poniższej tabeli przedstawiono limity dla każdego elementu w Cosmos DB. 

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalny rozmiar elementu | 2 MB (długość UTF-8 w reprezentacji JSON) |
| Maksymalna długość wartości klucza partycji | 2048 bajtów |
| Maksymalna długość wartości identyfikatora | 1024 bajtów |
| Maksymalna liczba właściwości na element | Brak praktycznego limitu |
| Maksymalna głębokość zagnieżdżenia | Brak praktycznego limitu |
| Maksymalna długość nazwy właściwości | Brak praktycznego limitu |
| Maksymalna długość wartości właściwości | Brak praktycznego limitu |
| Maksymalna długość wartości właściwości ciągu | Brak praktycznego limitu |
| Maksymalna długość wartości właściwości liczbowej | IEEE754 o podwójnej precyzji 64-bitowej |

Nie ma żadnych ograniczeń dotyczących ładunków elementów, takich jak liczba właściwości i głębokość zagnieżdżenia, z wyjątkiem ograniczeń długości klucza partycji i wartości identyfikatora oraz całkowitego ograniczenia rozmiaru wynoszącego 2 MB. Może być konieczne skonfigurowanie zasad indeksowania dla kontenerów z dużymi lub złożonymi strukturami elementów w celu ograniczenia zużycia RU. Zobacz [modelowanie elementów w Cosmos DB](how-to-model-partition-example.md) , aby zapoznać się z rzeczywistym przykładem i wzorcem do zarządzania dużymi elementami.

## <a name="per-request-limits"></a>Limity żądań na żądanie

Azure Cosmos DB obsługuje [operacje CRUD i zapytań](https://docs.microsoft.com/rest/api/cosmos-db/) wobec zasobów, takich jak kontenery, elementy i bazy danych. Obsługuje ona również [transakcyjne żądania wsadowe](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.transactionalbatch) dla wielu elementów z tym samym kluczem partycji w kontenerze.

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalny czas wykonywania pojedynczej operacji (na przykład wykonanie procedury składowanej lub pobranie pojedynczej strony zapytania)| 5 sekund |
| Maksymalny rozmiar żądania (na przykład procedura składowana, CRUD)| 2 MB |
| Maksymalny rozmiar odpowiedzi (na przykład zapytanie z podziałem na strony) | 4 MB |
| Maksymalna liczba operacji w partii transakcyjnej | 100 |

Gdy operacja, taka jak zapytanie osiągnie limit czasu wykonania lub wartość ograniczenia rozmiaru, zwraca stronę wyników i token kontynuacji klientowi, aby wznowić wykonywanie. Nie ma praktycznego limitu czasu trwania pojedynczego zapytania, które można uruchomić na stronach/kontynuacji.

Cosmos DB używa algorytmu HMAC do autoryzacji. Możesz użyć klucza głównego lub [tokenów zasobów](secure-access-to-data.md) do precyzyjnej kontroli dostępu do zasobów, takich jak kontenery, klucze partycji lub elementy. W poniższej tabeli wymieniono limity tokenów autoryzacji w Cosmos DB.

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalny czas wygaśnięcia tokenu głównego | 15 min  |
| Minimalny czas wygaśnięcia tokenu zasobów | 10 min  |
| Maksymalny czas wygaśnięcia tokenu zasobu | Domyślnie 24 h. Możesz ją zwiększyć, [bilet pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|
| Maksymalne przechylenie zegara na potrzeby autoryzacji tokenu| 15 min |

Cosmos DB obsługuje wykonywanie wyzwalaczy podczas operacji zapisu. Usługa obsługuje maksymalnie jednego wyzwalacza wstępnego i jednego wyzwalacza końcowego dla operacji zapisu. 

## <a name="autopilot-mode-limits"></a>Limity trybu pilotażowego

Zobacz artykuł " [autopilotaż](provision-throughput-autopilot.md#autopilot-limits) ", aby uzyskać limity przepływności i magazynowania w trybie autopilotażu.

## <a name="sql-query-limits"></a>Limity zapytań SQL

Cosmos DB obsługuje wykonywanie zapytań względem elementów przy użyciu [języka SQL](how-to-sql-query.md). W poniższej tabeli opisano ograniczenia dotyczące instrukcji zapytania, na przykład w przypadku liczby klauzul lub długości zapytań.

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalna długość zapytania SQL| 256 KB <sup>*</sup>|
| Maksymalna liczba sprzężeń na zapytanie| 5 <sup>*</sup>|
| Maksymalna liczba ANDs na zapytanie| 2000 <sup>*</sup>|
| Maksymalna liczba ORs na zapytanie| 2000 <sup>*</sup>|
| Maksymalna liczba UDF na zapytanie| 10 <sup>*</sup>|
| Maksymalna liczba argumentów na wyrażenie| 6000 <sup>*</sup>|
| Maksymalna liczba punktów na wielokąt| 4096 <sup>*</sup>|

<sup>*</sup> Możesz zwiększyć dowolny z tych limitów zapytań SQL, kontaktując się z pomocą techniczną platformy Azure.

## <a name="mongodb-api-specific-limits"></a>Limity specyficzne dla interfejsu API MongoDB

Cosmos DB obsługuje protokół sieci szkieletowej MongoDB dla aplikacji, które są zapisywane w MongoDB. Obsługiwane polecenia i wersje protokołu można znaleźć w [obsługiwanych funkcjach i składni MongoDB](mongodb-feature-support.md).

W poniższej tabeli wymieniono limity dotyczące obsługi funkcji MongoDB. Inne limity usług wymienione dla interfejsu API języka SQL (rdzeń) również mają zastosowanie do interfejsu API MongoDB.

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalny rozmiar pamięci zapytania MongoDB | 40 MB |
| Maksymalny czas wykonywania operacji MongoDB| 30 s |

## <a name="try-cosmos-db-free-limits"></a>Wypróbuj Cosmos DB limitów bezpłatnych

W poniższej tabeli przedstawiono limity dla [Azure Cosmos DB Wypróbuj bezpłatną](https://azure.microsoft.com/try/cosmosdb/) wersję próbną.

| Zasób | Limit domyślny |
| --- | --- |
| Okres próbny | 30 dni (można odnowić dowolną liczbę razy) |
| Maksymalna liczba kontenerów na subskrypcję (SQL, Gremlin, interfejs API tabel) | 1 |
| Maksymalna liczba kontenerów na subskrypcję (interfejs API MongoDB) | 3 |
| Maksymalna przepływność na kontener | 5000 |
| Maksymalna przepływność na wspólną bazę danych przepływności | 20000 |
| Maksymalna liczba magazynów na konto | 10 GB |

Wypróbuj Cosmos DB obsługuje dystrybucję globalną tylko w regionach Środkowe stany USA, Europa Północna i Azja Południowo-Wschodnia. Nie można utworzyć biletów pomocy technicznej systemu Azure dla usług try Azure Cosmos DB. Jednak pomoc techniczna jest świadczona dla subskrybentów z istniejącymi planami pomocy technicznej.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [ogólnej dystrybucji](distribute-data-globally.md) i [partycjonowania](partitioning-overview.md) podstawowych pojęć dotyczących [Cosmos DB.](request-units.md)

Rozpocznij pracę z usługą Azure Cosmos DB, korzystając z jednego z naszych przewodników:

* [Rozpoczynanie pracy z interfejsem SQL usługi Azure Cosmos DB](create-sql-api-dotnet.md)
* [Rozpoczynanie pracy z interfejsem MongoDB usługi Azure Cosmos DB](create-mongodb-nodejs.md)
* [Rozpoczynanie pracy z interfejsem API rozwiązania Cassandra usługi Azure Cosmos DB](create-cassandra-dotnet.md)
* [Rozpoczynanie pracy z interfejsem API programu Gremlin usługi Azure Cosmos DB](create-graph-dotnet.md)
* [Rozpoczynanie pracy z interfejsem API tabeli usługi Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Wypróbuj usługę Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/)
