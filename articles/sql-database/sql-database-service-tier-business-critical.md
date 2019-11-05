---
title: Usługa Azure SQL Database warstwy Krytyczne dla działania firmy | Microsoft Docs
description: Dowiedz się więcej na temat warstwy Krytyczne dla działania firmy Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: ef1f420e4c4dbd38ad703eb0573fae36af078edb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496231"
---
# <a name="business-critical-tier---azure-sql-database"></a>Warstwa Krytyczne dla działania firmy — Azure SQL Database

> [!NOTE]
> Warstwa Krytyczne dla działania firmy jest nazywana Premium w modelu zakupu jednostek DTU. Aby zapoznać się z porównaniem modelu zakupu opartego na rdzeń wirtualny z modelem zakupów opartym na jednostkach DTU, zobacz [Azure SQL Database kupowanie modeli i zasobów](sql-database-purchase-models.md).

Azure SQL Database jest oparta na architekturze SQL Server Database Engine, która jest dostosowywana do środowiska chmury w celu zapewnienia dostępności 99,99% nawet w przypadku awarii infrastruktury. Istnieją trzy modele architektury, które są używane w Azure SQL Database:
- Ogólnego przeznaczenia/Standard 
- Krytyczne dla działania firmy/Premium
- Hiperskala

Model warstwy usług premium/Krytyczne dla działania firmy jest oparty na klastrze procesów aparatu bazy danych. Ten model architektury polega na tym, że zawsze jest kworum dostępnych węzłów aparatu bazy danych i ma minimalny wpływ na wydajność, nawet w trakcie czynności konserwacyjnych.

Platforma Azure uaktualnia i aktualizuje podstawowy system operacyjny, sterowniki oraz aparat SQL Server Database w sposób niewidoczny dla użytkowników końcowych w minimalnym czasie. 

Dostępność Premium jest włączona w warstwach usług premium i Krytyczne dla działania firmy Azure SQL Database i jest przeznaczona do intensywnych obciążeń, które nie mogą tolerować wpływu na wydajność wynikające z trwających operacji konserwacyjnych.

W modelu Premium usługa Azure SQL Database integruje obliczenia i magazyn w jednym węźle. Wysoka dostępność w tym modelu architektury jest osiągana przez replikację operacji obliczeniowych (SQL Server procesie aparatu bazy danych) i magazynu (lokalnie dołączony dysk SSD) wdrożonego w czterech węzłach klastra przy użyciu technologii podobnej do SQL Server [zawsze włączone grupy dostępności ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Klaster węzłów aparatu bazy danych](media/sql-database-managed-instance/business-critical-service-tier.png)

Zarówno proces aparatu bazy danych SQL, jak i bazowe pliki MDF/ldf są umieszczane w tym samym węźle, w którym lokalnie dołączony magazyn SSD zapewnia małe opóźnienia w obciążeniu. Wysoka dostępność jest implementowana przy użyciu technologii podobnego do SQL Server [zawsze włączonymi grupami dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Każda baza danych jest klastrem węzłów bazy danych z jedną podstawową bazą danych, która jest dostępna do obciążania klienta, oraz trzech procesów pomocniczych zawierających kopie danych. Węzeł podstawowy ciągle wypychanie zmian do węzłów pomocniczych w celu upewnienia się, że dane są dostępne w replikach pomocniczych, jeśli węzeł podstawowy ulegnie awarii z dowolnego powodu. Tryb failover jest obsługiwany przez aparat bazy danych SQL Server — jedna replika pomocnicza jest węzłem podstawowym, a Nowa replika pomocnicza jest tworzona w celu zapewnienia wystarczającej liczby węzłów w klastrze. Obciążenie jest automatycznie przekierowywane do nowego węzła podstawowego.

Ponadto klaster Krytyczne dla działania firmy ma wbudowaną funkcję [odczytu](sql-database-read-scale-out.md) w poziomie, która zapewnia bezpłatny, wbudowany węzeł tylko do odczytu, który może służyć do uruchamiania zapytań tylko do odczytu (na przykład raportów), które nie powinny wpływać na wydajność podstawowego roboczych.

## <a name="when-to-choose-this-service-tier"></a>Kiedy należy wybrać tę warstwę usług?

Krytyczne dla działania firmy warstwa usług została zaprojektowana dla aplikacji, które wymagają odpowiedzi o małym opóźnieniu z bazowego magazynu SSD (średnio 1-2 ms), szybkiego odzyskiwania w przypadku awarii infrastruktury podstawowej lub konieczności wyłączania raportów, analiz i tylko do odczytu. wykonuje zapytania dotyczące bezpłatnej repliki pomocniczej do odzyskania podstawowej bazy danych.

Najważniejsze przyczyny wyboru Krytyczne dla działania firmy warstwy usług zamiast warstwy Ogólnego przeznaczenia są następujące:
-   Niskie wymagania dotyczące opóźnień we/wy — obciążenie wymagające szybkiej odpowiedzi z warstwy magazynowania (średnia 1-2 milisekund) powinno korzystać z warstwy Krytyczne dla działania firmy. 
-   Częsta komunikacja między aplikacjami i bazami danych. Aplikacja, która nie może korzystać z buforowania warstwy aplikacji lub [żądania wsadowego](sql-database-use-batching-to-improve-performance.md) i musi wysyłać wiele zapytań SQL, które muszą być szybko przetwarzane, są dobrym kandydatami do warstwy krytyczne dla działania firmy.
-   Duża liczba aktualizacji — operacje INSERT, Update i DELETE modyfikują strony danych w pamięci (zanieczyszczoną stronę), które muszą zostać zapisane w plikach danych z `CHECKPOINT` operacji. Potencjalna awaria procesu aparatu bazy danych lub przełączenia w tryb failover bazy danych z dużą liczbą zanieczyszczonych stron może zwiększyć czas odzyskiwania w warstwie Ogólnego przeznaczenia. Użyj warstwy Krytyczne dla działania firmy, jeśli masz obciążenie, które powoduje wiele zmian w pamięci. 
-   Długotrwałe transakcje, które modyfikują dane. Transakcje, które są otwierane przez dłuższy czas, uniemożliwiają Obcinanie pliku dziennika, co może spowodować zwiększenie rozmiaru dziennika i liczby [wirtualnych plików dziennika (VLF)](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch). Duża liczba VLF może spowolnić odzyskiwanie bazy danych po przejściu do trybu failover.
-   Obciążenie przy użyciu zapytań i raportów analitycznych, które można przekierować do bezpłatnej repliki tylko do odczytu.
- Wyższa odporność i szybsze odzyskiwanie po awarii. W przypadku awarii systemu baza danych w wystąpieniu podstawowym zostanie wyłączona, a jedna z replik pomocniczych stanie się natychmiast nową podstawową bazą danych do odczytu i zapisu, która jest gotowa do przetwarzania zapytań. Aparat bazy danych nie musi analizować i ponawiać transakcji z pliku dziennika i ładować wszystkich danych w buforze pamięci.
- Zaawansowana ochrona przed uszkodzeniem danych — warstwa Krytyczne dla działania firmy korzysta z replik bazy danych w tle w celu zapewnienia ciągłości działania, a więc usługa korzysta z automatycznej naprawy strony, która jest tą samą technologią, jak w przypadku bazy danych SQL Server Database. [dublowanie i grupy dostępności](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring). W przypadku, gdy replika nie może odczytać strony z powodu problemu z integralnością danych, Nowa kopia strony zostanie pobrana z innej repliki, zastępując nieczytelną stronę bez utraty danych lub przestoju klienta. Ta funkcja ma zastosowanie w warstwie Ogólnego przeznaczenia, jeśli baza danych ma replikę geograficzną.
- Wyższa dostępność — warstwa Krytyczne dla działania firmy w ramach konfiguracji z obsługą wiele-AZ — zapewnia dostępność na 99,995% w porównaniu do 99,99% warstwy Ogólnego przeznaczenia.
- Szybka replikacja geograficzna — warstwa Krytyczne dla działania firmy skonfigurowana z replikacją geograficzną ma gwarantowany cel punktu odzyskiwania (RPO) wynoszący 5 sekund i cel czasu odzyskiwania (RTO) wynoszący 30 sekund przez 100% czasu wdrożenia.

## <a name="next-steps"></a>Następne kroki

- Znajdź charakterystykę zasobu (liczbę rdzeni, operacji we/wy, pamięci) warstwy Krytyczne dla działania firmy w [wystąpieniu zarządzanym](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), pojedynczą bazę danych w [modelu rdzeń wirtualny](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) lub [modelu DTU](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier)lub elastyczną pulę w [modelu rdzeń wirtualny](sql-database-vcore-resource-limits-elastic-pools.md#business-critical---provisioned-compute---gen4) i [modelu DTU](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits).
- Dowiedz się więcej na temat warstw [ogólnego przeznaczenia](sql-database-service-tier-general-purpose.md) i [skalowania](sql-database-service-tier-hyperscale.md) .
- Dowiedz się więcej na temat [Service Fabric](../service-fabric/service-fabric-overview.md).
- Aby uzyskać więcej opcji dotyczących wysokiej dostępności i odzyskiwania po awarii, zobacz [ciągłość](sql-database-business-continuity.md)działania.
