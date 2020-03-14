---
title: Migrowanie puli SQL do Gen2
description: Instrukcje dotyczące migrowania istniejącej puli SQL do Gen2 i harmonogramu migracji według regionów.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.topic: article
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 00180c1791e765240f3f8feac188b9250162408e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256707"
---
# <a name="upgrade-your-sql-pool-to-gen2"></a>Uaktualnij pulę SQL do Gen2

Firma Microsoft pomaga w obniżeniu kosztu uruchamiania puli SQL na poziomie pozycji.  Niższe warstwy obliczeniowe mogące obsługiwać zapytania wymagające obsługi są teraz dostępne dla puli SQL. Przeczytaj pełną obsługę usługi anonsowanie [niższej warstwy obliczeniowej dla Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). Nowa oferta jest dostępna w regionach zanotowanych w poniższej tabeli. W przypadku obsługiwanych regionów istniejące pule SQL Gen1 można uaktualnić do Gen2 przez:

- **Proces uaktualniania automatycznego:** Automatyczne uaktualnienia nie są uruchamiane zaraz po udostępnieniu usługi w regionie.  Po rozpoczęciu uaktualnień automatycznych w określonym regionie poszczególne uaktualnienia magazynu danych będą wykonywane w ramach wybranego harmonogramu konserwacji.
- [**Samodzielne uaktualnienie do Gen2:** ](#self-upgrade-to-gen2) Możesz określić, kiedy należy przeprowadzić uaktualnienie, wykonując samodzielne uaktualnienie do Gen2. Jeśli region nie jest jeszcze obsługiwany, można przywrócić z punktu przywracania bezpośrednio do wystąpienia Gen2 w obsługiwanym regionie.

## <a name="automated-schedule-and-region-availability-table"></a>Automatyczne planowanie i tabela dostępności regionów

Poniższa tabela podsumowuje według regionu, gdy dolna warstwa obliczeniowa Gen2 będzie dostępna i kiedy rozpocznie się automatyczne uaktualnienia. Daty mogą ulec zmianie. Sprawdź ponownie, aby zobaczyć, kiedy region będzie dostępny.

\* wskazuje, że określony harmonogram dla regionu jest obecnie niedostępny.

| **Region** | **Dolna Gen2 dostępna** | **Rozpoczęcie uaktualniania automatycznego** |
|:--- |:--- |:--- |
| Kanada Wschodnia |1 czerwca 2020 |1 lipca 2020 |
| Chiny Wschodnie |\* |\* |
| Chiny Północne |\* |\* |
| Niemcy Środkowe |\* |\* |
| Niemcy Środkowo-Zachodnie |Dostępne |1 maja 2020 |
| Indie Zachodnie |Dostępne |1 maja 2020  |

## <a name="automatic-upgrade-process"></a>Proces automatycznego uaktualniania

Zgodnie z powyższym wykresem dostępności będziemy planować zautomatyzowane uaktualnienia dla wystąpień Gen1. Aby uniknąć nieoczekiwanych przerw w dostępności puli SQL, zautomatyzowane uaktualnienia zostaną zaplanowane w harmonogramie konserwacji. Możliwość tworzenia nowego wystąpienia Gen1 zostanie wyłączona w regionach, które przechodzą na funkcję autouaktualniania do Gen2. Gen1 będzie przestarzałe po zakończeniu automatycznych uaktualnień. Aby uzyskać więcej informacji na temat harmonogramów, zobacz [Wyświetlanie harmonogramu konserwacji](viewing-maintenance-schedule.md)

Proces uaktualniania obejmuje krótki spadek łączności (około 5 minut), co spowoduje ponowne uruchomienie puli SQL.  Po ponownym uruchomieniu puli SQL będzie ona w pełni dostępna do użycia. Jednak może wystąpić spadek wydajności, podczas gdy proces uaktualniania kontynuuje uaktualnianie plików danych w tle. Łączny okres obniżonej wydajności zależy od rozmiaru plików danych.

Proces uaktualniania pliku danych można również przyspieszyć, uruchamiając polecenie [ALTER index Rebuild](sql-data-warehouse-tables-index.md) na wszystkich podstawowych tabelach magazynu kolumn przy użyciu większego poziomu SLO i klasy zasobów po ponownym uruchomieniu.

> [!NOTE]
> Instrukcja ALTER index Rebuild jest operacją w trybie offline, a tabele nie będą dostępne do momentu zakończenia odbudowy.

## <a name="self-upgrade-to-gen2"></a>Samodzielne uaktualnienie do Gen2

Możesz wybrać opcję samouaktualnienia, wykonując następujące kroki w istniejącej puli SQL Gen1. W przypadku wybrania opcji samodzielnego uaktualniania należy ją ukończyć przed rozpoczęciem procesu uaktualniania automatycznego w Twoim regionie. Dzięki temu można uniknąć ryzyka, że automatyczne uaktualnienia powodują konflikt.

Podczas przeprowadzania samodzielnego uaktualniania dostępne są dwie opcje.  Możesz uaktualnić bieżącą pulę SQL w miejscu lub przywrócić pulę Gen1 SQL do wystąpienia Gen2.

- [Uaktualnienie w miejscu](upgrade-to-latest-generation.md) — ta opcja spowoduje uaktualnienie istniejącej puli SQL Gen1 do Gen2. Proces uaktualniania obejmuje krótki spadek łączności (około 5 minut), co spowoduje ponowne uruchomienie puli SQL.  Po ponownym uruchomieniu puli SQL będzie ona w pełni dostępna do użycia. Jeśli wystąpią problemy podczas uaktualniania, Otwórz [żądanie obsługi](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) i odwołuje się do "Gen2 upgrade" jako możliwej przyczyny.
- [Uaktualnienie z punktu przywracania](sql-data-warehouse-restore.md) — Utwórz punkt przywracania zdefiniowany przez użytkownika w bieżącej puli SQL Gen1, a następnie Przywróć bezpośrednio do wystąpienia Gen2. Istniejąca Pula Gen1 SQL zostanie zachowana. Po zakończeniu przywracania Pula SQL Gen2 będzie w pełni dostępna do użycia.  Po uruchomieniu wszystkich procesów testowania i walidacji w przywracanym wystąpieniu Gen2 można usunąć oryginalne wystąpienie Gen1.

   - Krok 1. z Azure Portal [Utwórz punkt przywracania zdefiniowany przez użytkownika](sql-data-warehouse-restore-active-paused-dw.md).
   - Krok 2. podczas przywracania z punktu przywracania zdefiniowanego przez użytkownika Ustaw poziom wydajności na preferowaną warstwę Gen2.

Może nastąpić okresowe pogorszenie wydajności, gdy w tle będzie odbywać się uaktualnianie plików danych. Łączny okres obniżonej wydajności zależy od rozmiaru plików danych.

Aby przyspieszyć proces migracji danych w tle, można natychmiast wymusić przenoszenie danych przez uruchomienie [polecenia ALTER index Rebuild](sql-data-warehouse-tables-index.md) na wszystkich głównych tabelach magazynu kolumn, które zostaną zbadane przy użyciu większego poziomu SLO i klasy zasobów.

> [!NOTE]
> Instrukcja ALTER index Rebuild jest operacją w trybie offline, a tabele nie będą dostępne do momentu zakończenia odbudowy.

Jeśli wystąpią problemy z pulą SQL, Utwórz [żądanie pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md) i odwołuje się do "Gen2 upgrade" jako możliwej przyczyny.

Aby uzyskać więcej informacji, zobacz [uaktualnianie do Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Często zadawane pytania dotyczące migracji

**P: czy Gen2 koszt jest taki sam jak Gen1?**

- Odp. Tak.

**P: w jaki sposób uaktualnienia będą mieć wpływ na moje skrypty automatyzacji?**

- Odp.: każdy skrypt automatyzacji, który odwołuje się do celu poziomu usługi, powinien zostać zmieniony w taki sposób, aby odpowiadał Gen2 równoważnej.  Szczegóły można znaleźć [tutaj](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal).

**P: jak długo trwa samodzielne uaktualnianie?**

- Odp.: można uaktualnić miejsce lub uaktualnić z punktu przywracania.  
   - Uaktualnienie w miejscu spowoduje chwilowe wstrzymanie i wznowienie puli SQL.  Proces w tle będzie kontynuowany, gdy pula SQL jest w trybie online.  
   - Trwa to dłużej, Jeśli uaktualniasz punkt przywracania, ponieważ uaktualnienie przejdzie przez proces pełnego przywracania.

**P: jak długo trwa Autouaktualnianie?**

- Odp.: rzeczywisty czas przestoju w przypadku uaktualnienia to przedział czasu potrzebny do wstrzymania i wznowienia działania usługi, która wynosi od 5 do 10 minut. Po krótkim czasie przestoju proces w tle uruchomi migrację magazynu. Czas trwania procesu w tle zależy od rozmiaru puli SQL.

**P: Kiedy to automatyczne uaktualnienie zostanie wykonane?**

- Odp.: w harmonogramie konserwacji. Korzystanie z wybranego harmonogramu konserwacji pozwoli zminimalizować zakłócenia Twojej firmy.

**P: co należy zrobić, jeśli mój proces uaktualniania w tle wydaje się być zablokowany?**

 - Odp.: należy uruchomić ponowne indeksowanie tabel magazynu kolumn. Należy pamiętać, że ponowne indeksowanie tabeli będzie w trybie offline podczas tej operacji.

**P: co, jeśli Gen2 nie ma celu poziomu usług, mam na Gen1?**
- Odp.: Jeśli używasz wartości DW600 lub DW1200 na Gen1, zaleca się użycie odpowiednio DW500c lub DW1000c, ponieważ Gen2 oferuje więcej pamięci, zasobów i wyższą wydajność niż Gen1.

**P: Czy można wyłączyć geograficzną kopię zapasową?**
- Odp.: Nie. Geograficzna kopia zapasowa to funkcja korporacyjna, która zachowuje dostępność puli SQL w przypadku, gdy region staną się niedostępne. Otwórz [żądanie pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md) , jeśli chcesz uzyskać więcej problemów.

**P: czy istnieje różnica w składni T-SQL między Gen1 i Gen2?**

- Odp.: nie wprowadzono zmian w składni języka T-SQL od Gen1 do Gen2.

**P: czy Gen2 obsługuje okna obsługi?**

- Odp. Tak.

**P: czy będzie można utworzyć nowe wystąpienie Gen1 po uaktualnieniu mojego regionu?**

- Odp.: Nie. Po uaktualnieniu regionu tworzenie nowych wystąpień Gen1 zostanie wyłączone.

## <a name="next-steps"></a>Następne kroki

- [Kroki uaktualniania](upgrade-to-latest-generation.md)
- [Okna obsługi](maintenance-scheduling.md)
- [Monitor kondycji zasobów](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Przejrzyj przed rozpoczęciem migracji](upgrade-to-latest-generation.md#before-you-begin)
- [Uaktualnij w miejscu i Uaktualnij z punktu przywracania](upgrade-to-latest-generation.md)
- [Utwórz punkt przywracania zdefiniowany przez użytkownika](sql-data-warehouse-restore-points.md)
- [Dowiedz się, jak przywrócić do Gen2](sql-data-warehouse-restore-active-paused-dw.md)
- [Otwórz żądanie obsługi SQL Data Warehouse](https://go.microsoft.com/fwlink/?linkid=857950)
