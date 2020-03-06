---
title: Limity zasobów jednostek DTU ograniczają pule elastyczne
description: Ta strona zawiera opis niektórych typowych limitów zasobów jednostek DTU dla pul elastycznych w Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
ms.date: 03/14/2019
ms.openlocfilehash: 3f6bbef8f274c5efb99d5b98961450aa7b001527
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359935"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-purchasing-model"></a>Limity zasobów dla pul elastycznych przy użyciu modelu zakupu jednostek DTU

Ten artykuł zawiera szczegółowe limity zasobów Azure SQL Database pul elastycznych i baz danych w puli przy użyciu modelu zakupu jednostek DTU.

Limity zasobów modelu zakupu jednostek DTU dla pojedynczych baz danych można znaleźć w temacie [limity zasobów jednostek DTU — pojedyncze bazy danych](sql-database-vcore-resource-limits-elastic-pools.md). Aby uzyskać limity zasobów rdzeń wirtualny, zobacz [limity zasobów rdzeń wirtualny — pojedyncze bazy danych](sql-database-vcore-resource-limits-single-databases.md) i [limity zasobów rdzeń wirtualny — pule elastyczne](sql-database-vcore-resource-limits-elastic-pools.md).

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Pula elastyczna: rozmiary magazynu i rozmiary obliczeń

W przypadku pul elastycznych SQL Database w poniższych tabelach przedstawiono zasoby dostępne w każdej warstwie usług i rozmiarze obliczeniowym. Możesz ustawić warstwę usług, rozmiar obliczeń i ilość miejsca do magazynowania przy użyciu [Azure Portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [programu PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), interfejsu [wiersza polecenia platformy Azure](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases)lub [interfejsu API REST](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> Aby uzyskać wskazówki dotyczące skalowania i zagadnienia, zobacz [skalowanie elastycznej puli](sql-database-elastic-pool-scale.md)
> [!NOTE]
> Limity zasobów poszczególnych baz danych w pulach elastycznych są generalnie takie same jak w przypadku pojedynczych baz danych poza pulami na podstawie DTU i warstwy usług. Na przykład maksymalna liczba współbieżnych procesów roboczych dla bazy danych S2 to 120 procesów roboczych. W związku z tym Maksymalna liczba współbieżnych procesów roboczych dla bazy danych w puli standardowej to 120 pracowników, jeśli Maksymalna liczba jednostek DTU na bazę danych w puli to 50 DTU (co jest równoważne S2).

### <a name="basic-elastic-pool-limits"></a>Limity podstawowych pul elastycznych

| Jednostki eDTU na pulę | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Uwzględniony magazyn na pulę (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maksymalna liczba opcji magazynu na pulę (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maksymalna pojemność magazynu OLTP w pamięci na pulę (GB) | Nie dotyczy | Nie dotyczy | Nie dotyczy | Nie dotyczy | Nie dotyczy | Nie dotyczy | Nie dotyczy | Nie dotyczy |
| Maksymalna liczba baz danych na pulę | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Maksymalna liczba współbieżnych procesów roboczych (żądań) na pulę | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Maksymalna liczba współbieżnych sesji na pulę | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Minimalna liczba opcji jednostek eDTU na bazę danych | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Maksymalna liczba opcji jednostek eDTU na bazę danych | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Maksymalny rozmiar magazynu na bazę danych (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

### <a name="standard-elastic-pool-limits"></a>Limity standardowych pul elastycznych

| Jednostki eDTU na pulę | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| Uwzględniony magazyn na pulę (GB) | 50 | 100 | 200 | 300 | 400 | 800 |
| Maksymalna liczba opcji magazynu na pulę (GB) | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 |
| Maksymalna pojemność magazynu OLTP w pamięci na pulę (GB) | Nie dotyczy | Nie dotyczy | Nie dotyczy | Nie dotyczy | Nie dotyczy | Nie dotyczy |
| Maksymalna liczba baz danych na pulę | 100 | 200 | 500 | 500 | 500 | 500 |
| Maksymalna liczba współbieżnych procesów roboczych (żądań) na pulę | 100 | 200 | 400 | 600 | 800 | 1600 |
| Maksymalna liczba współbieżnych sesji na pulę | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Minimalna liczba opcji jednostek eDTU na bazę danych | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Maksymalna liczba opcji jednostek eDTU na bazę danych | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Maksymalny rozmiar magazynu na bazę danych (GB) | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Limity standardowych pul elastycznych (ciąg dalszy)

| Jednostki eDTU na pulę | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Uwzględniony magazyn na pulę (GB) | 1200 | 1600 | 2000 | 2500 | 3000 |
| Maksymalna liczba opcji magazynu na pulę (GB) | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Maksymalna pojemność magazynu OLTP w pamięci na pulę (GB) | Nie dotyczy | Nie dotyczy | Nie dotyczy | Nie dotyczy | Nie dotyczy |
| Maksymalna liczba baz danych na pulę | 500 | 500 | 500 | 500 | 500 |
| Maksymalna liczba współbieżnych procesów roboczych (żądań) na pulę | 2400 | 3200 | 4000 | 5000 | 6000 |
| Maksymalna liczba współbieżnych sesji na pulę | 30000 | 30000 | 30000 | 30000 | 30000 |
| Minimalna liczba opcji jednostek eDTU na bazę danych | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Maksymalna liczba opcji jednostek eDTU na bazę danych | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Maksymalna liczba opcji magazynu na bazę danych (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

### <a name="premium-elastic-pool-limits"></a>Limity pul elastycznych Premium

| Jednostki eDTU na pulę | **125** | **250** | **500** | **1000** | **1500**|
|:---|---:|---:|---:| ---: | ---: |
| Uwzględniony magazyn na pulę (GB) | 250 | 500 | 750 | 1024 | 1536 |
| Maksymalna liczba opcji magazynu na pulę (GB) | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Maksymalna pojemność magazynu OLTP w pamięci na pulę (GB) | 1 | 2 | 4 | 10 | 12 |
| Maksymalna liczba baz danych na pulę | 50 | 100 | 100 | 100 | 100 |
| Maksymalna liczba współbieżnych procesów roboczych (żądań) na pulę | 200 | 400 | 800 | 1600 | 2400 |
| Maksymalna liczba współbieżnych sesji na pulę | 30000 | 30000 | 30000 | 30000 | 30000 |
| Minimalna liczba jednostek eDTU na bazę danych | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000|
| Maksymalna liczba jednostek eDTU na bazę danych | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000|
| Maksymalny rozmiar magazynu na bazę danych (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

### <a name="premium-elastic-pool-limits-continued"></a>Limity pul elastycznych Premium (ciąg dalszy)

| Jednostki eDTU na pulę | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| Uwzględniony magazyn na pulę (GB) | 2048 | 2560 | 3072 | 3548 | 4096 |
| Maksymalna liczba opcji magazynu na pulę (GB) | 2048 | 2560 | 3072 | 3548 | 4096|
| Maksymalna pojemność magazynu OLTP w pamięci na pulę (GB) | 16 | 20 | 24 | 28 | 32 |
| Maksymalna liczba baz danych na pulę | 100 | 100 | 100 | 100 | 100 |
| Maksymalna liczba współbieżnych procesów roboczych (żądań) na pulę | 3200 | 4000 | 4800 | 5600 | 6400 |
| Maksymalna liczba współbieżnych sesji na pulę | 30000 | 30000 | 30000 | 30000 | 30000 |
| Minimalna liczba opcji jednostek eDTU na bazę danych | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Maksymalna liczba opcji jednostek eDTU na bazę danych | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Maksymalny rozmiar magazynu na bazę danych (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

> [!IMPORTANT]
> Więcej niż 1 TB magazynu w warstwie Premium jest obecnie dostępne we wszystkich regionach z wyjątkiem: Chiny Wschodnie, Chiny Północne, Niemcy środkowe, Niemcy północno-zachodnie stany USA, regiony US DoD oraz centralne stany USA. W tych regionach maksymalna wielkość magazynu w warstwie Premium jest ograniczona do 1 TB.  Aby uzyskać więcej informacji, zobacz [bieżące ograniczenia poziomów P11–P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).

Jeśli używane są wszystkie jednostki DTU puli elastycznej, każda baza danych w puli otrzymuje taką samą ilość zasobów do przetwarzania zapytań. Usługa SQL Database zapewnia sprawiedliwe udostępnianie zasobów między bazami danych przez zapewnienie równych okresów czasu obliczeń. Sprawiedliwe udostępnianie zasobów puli elastycznej jest wykonywane oprócz zapewniania dowolnej ilości zasobów w przeciwnym razie gwarantowanej dla każdej bazy danych, gdy minimalna liczba jednostek DTU na bazę danych jest ustawiona na wartość różną od zera.

> [!NOTE]
> Aby uzyskać `tempdb` limitów, zobacz [limity tempdb](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

### <a name="database-properties-for-pooled-databases"></a>Właściwości bazy danych w puli

W poniższej tabeli opisano właściwości dla baz danych w puli.

| Właściwość | Opis |
|:--- |:--- |
| Maksymalna liczba jednostek eDTU na bazę danych |Maksymalna liczba jednostek eDTU, z których może korzystać dowolna baza danych w puli, jeśli są dostępne na podstawie użycia innych baz danych w puli. Maksymalna liczba jednostek eDTU na bazę danych nie jest gwarancją zasobów dla bazy danych. To ustawienie jest ustawieniem globalnym, które ma zastosowanie do wszystkich baz danych w puli. Należy ustawić odpowiednio wysoką maksymalną liczbę jednostek eDTU na bazę danych, aby obsłużyć szczytowe użycia baz danych. Oczekiwany jest pewien stopień nadmiernego przydzielania, ponieważ pula ogólnie zakłada wzorce gorącego i zimnego użycia dla baz danych, w których nie wszystkie bazy danych jednocześnie osiągają szczytowe użycie. Załóżmy na przykład, że użycie szczytowe na bazę danych wynosi 20 eDTU i tylko 20% ze 100 baz danych w puli osiąga szczytowe użycie w tym samym czasie. Jeśli maksymalna liczba jednostek eDTU na bazę danych wynosi 20 eDTU, zasadne jest nadmiarowe przydzielenie pięciokrotnie większej liczby eDTU dla puli i ustawienie liczby 400 jednostek eDTU na pulę. |
| Minimalna liczba jednostek eDTU na bazę danych |Minimalna liczba jednostek eDTU gwarantowana dla każdej bazy danych w puli. To ustawienie jest ustawieniem globalnym, które ma zastosowanie do wszystkich baz danych w puli. Minimalna liczba jednostek eDTU na bazę danych może być ustawiona na 0 i jest to również wartość domyślna. Tę właściwość można ustawić na dowolną wartość między 0 a średnim użyciem jednostek eDTU na bazę danych. Iloczyn liczby baz danych w puli i minimalnej liczby jednostek eDTU na bazę danych nie może przekraczać liczby jednostek eDTU na pulę. Na przykład jeśli pula zawiera 20 baz danych, a minimalna liczba jednostek eDTU na bazę danych wynosi 10 eDTU, liczba jednostek eDTU na pulę musi wynosić co najmniej 200 eDTU. |
| Maksymalny rozmiar magazynu na bazę danych |Maksymalny rozmiar bazy danych ustawiony przez użytkownika dla bazy danych w puli. Jednak bazy danych w puli korzystają z przydzielony magazyn pul. Nawet jeśli łączny maksymalny rozmiar magazynu *na bazę danych* jest ustawiony na wartość większą niż całkowita ilość dostępnego *miejsca do*magazynowania w puli, całkowite miejsce używane przez wszystkie bazy danych nie będzie mogło przekroczyć limitu dostępnej puli. Maksymalny rozmiar bazy danych odnosi się do maksymalnego rozmiaru plików danych i nie obejmuje przestrzeni używanej przez pliki dziennika. |
|||

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać limity zasobów rdzeń wirtualny dla pojedynczej bazy danych, zobacz [limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu rdzeń wirtualny](sql-database-vcore-resource-limits-single-databases.md)
- W przypadku limitów zasobów jednostek DTU dla pojedynczej bazy danych zobacz [limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu jednostek DTU](sql-database-dtu-resource-limits-single-databases.md) .
- Aby uzyskać limity zasobów rdzeń wirtualny dla pul elastycznych, zobacz [limity zasobów dla pul elastycznych przy użyciu modelu zakupu rdzeń wirtualny](sql-database-vcore-resource-limits-elastic-pools.md)
- W przypadku limitów zasobów dla wystąpień zarządzanych zobacz [limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md).
- Aby uzyskać informacje na temat ogólnych limitów platformy Azure, zobacz [limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Aby uzyskać informacje na temat limitów zasobów na serwerze bazy danych, zobacz [Omówienie limitów zasobów na serwerze SQL Database](sql-database-resource-limits-database-server.md) , aby uzyskać informacje na temat limitów na poziomie serwera i subskrypcji.
