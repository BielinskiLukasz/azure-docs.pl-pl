---
title: Przetwarzanie OLTP w pamięci zwiększa wydajność programu SQL transakcja | Microsoft Docs
description: Wdrażaj OLTP w pamięci, aby zwiększyć wydajność transakcyjną w istniejącej bazie danych SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 11/07/2018
ms.openlocfilehash: e869b2bba3bd64b58d9063e9445889ef709efdc3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567937"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>Użycie OLTP w pamięci w celu poprawy wydajności aplikacji w SQL Database

[OLTP w pamięci](sql-database-in-memory.md) może służyć do ulepszania wydajności przetwarzania transakcji, pozyskiwania danych i przejściowych scenariuszy danych w [warstwach Premium i krytyczne dla działania firmy](sql-database-service-tiers-vcore.md) baz danych bez zwiększania warstwy cenowej. 

> [!NOTE] 
> Dowiedz się [, jak kworum podwaja obciążenie bazy danych przy jednoczesnym obniżeniu liczby jednostek DTU o 70% z SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Wykonaj następujące kroki, aby wdrożyć OLTP w pamięci w istniejącej bazie danych.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>Krok 1: Upewnij się, że korzystasz z bazy danych w warstwie Premium i Krytyczne dla działania firmy

Przetwarzanie OLTP w pamięci jest obsługiwane tylko w bazach danych w warstwach Premium i Krytyczne dla działania firmy. W pamięci jest obsługiwana, jeśli zwrócony wynik wynosi 1 (nie 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* oznacza *Przetwarzanie transakcji Extreme*



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Krok 2: Identyfikowanie obiektów do migracji do przetwarzania OLTP w pamięci
Program SSMS zawiera raport z **omówieniem analizy wydajności transakcji** , który można uruchomić względem bazy danych z aktywnym obciążeniem. Raport identyfikuje tabele i procedury składowane, które są kandydatami do migracji do przetwarzania OLTP w pamięci.

W programie SSMS w celu wygenerowania raportu:

* W **Eksplorator obiektów**kliknij prawym przyciskiem myszy węzeł bazy danych.
* Kliknij kolejno pozycje **raporty** > **standardowe raporty** > **Analiza wydajności — Omówienie**.

Aby uzyskać więcej informacji, zobacz [Określanie, czy tabela lub procedura składowana powinna być przypisana do OLTP w pamięci](https://msdn.microsoft.com/library/dn205133.aspx).

## <a name="step-3-create-a-comparable-test-database"></a>Krok 3: Tworzenie porównywalnej bazy danych testowych
Załóżmy, że raport wskazuje, że baza danych ma tabelę, która mogłaby skorzystać z konwersji na tabelę zoptymalizowaną pod kątem pamięci. Zalecamy, aby najpierw przeprowadzić test w celu potwierdzenia wskazywania przez testowanie.

Potrzebna jest testowa kopia produkcyjnej bazy danych. Testowa baza danych powinna znajdować się na tym samym poziomie warstwy usług, co w produkcyjnej bazie danych.

Aby ułatwić testowanie, Dostosuj bazę danych testowych w następujący sposób:

1. Połącz się z testową bazą danych za pomocą programu SSMS.
2. Aby uniknąć potrzeby opcji WITH (migawka) w zapytaniach, należy ustawić opcję bazy danych, jak pokazano w następującej instrukcji języka T-SQL:
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Krok 4: Migrowanie tabel
Należy utworzyć i wypełnić kopię zoptymalizowaną pod kątem pamięci tabeli, która ma zostać przetestowana. Można go utworzyć przy użyciu jednej z:

* Kreator przydatnej optymalizacji pamięci w programie SSMS.
* Ręczna instrukcja T-SQL.

#### <a name="memory-optimization-wizard-in-ssms"></a>Kreator optymalizacji pamięci w programie SSMS
Aby użyć tej opcji migracji:

1. Połącz się z testową bazą danych za pomocą programu SSMS.
2. W **Eksplorator obiektów**kliknij prawym przyciskiem myszy tabelę, a następnie kliknij pozycję **Doradca optymalizacji pamięci**.
   
   * Zostanie wyświetlony Kreator **doradcy pamięci tabeli** .
3. W Kreatorze kliknij pozycję **Walidacja migracji** (lub przycisk **dalej** ), aby sprawdzić, czy tabela zawiera jakiekolwiek Nieobsługiwane funkcje, które nie są obsługiwane w tabelach zoptymalizowanych pod kątem pamięci. Aby uzyskać więcej informacji, zobacz:
   
   * *Lista kontrolna optymalizacji pamięci* w [klasyfikatorze optymalizacji pamięci](https://msdn.microsoft.com/library/dn284308.aspx).
   * [Konstrukcje języka Transact-SQL nie są obsługiwane przez OLTP w pamięci](https://msdn.microsoft.com/library/dn246937.aspx).
   * [Migrowanie do przetwarzania OLTP w pamięci](https://msdn.microsoft.com/library/dn247639.aspx).
4. Jeśli tabela nie zawiera żadnych nieobsługiwanych funkcji, Klasyfikator może wykonać rzeczywiste migracje schematu i danych.

#### <a name="manual-t-sql"></a>Ręczna instrukcja T-SQL
Aby użyć tej opcji migracji:

1. Połącz się z testową bazą danych za pomocą programu SSMS (lub podobnego narzędzia).
2. Uzyskaj kompletny skrypt T-SQL dla tabeli i jej indeksów.
   
   * W programie SSMS kliknij prawym przyciskiem myszy węzeł tabeli.
   * Kliknij pozycję **tabela skryptów,**  > **Aby** > utworzyć**nowe okno zapytania**.
3. W oknie Skrypt Dodaj wyrażenie WITH (MEMORY_OPTIMIZED = ON) do instrukcji CREATE TABLE.
4. Jeśli istnieje indeks KLASTROWANY, zmień go na nieklastrowany.
5. Zmień nazwę istniejącej tabeli przy użyciu SP_RENAME.
6. Utwórz nową, zoptymalizowaną pod kątem pamięci kopię tabeli, uruchamiając edytowany skrypt CREATE TABLE.
7. Skopiuj dane do tabeli zoptymalizowanej pod kątem pamięci za pomocą instrukcji INSERT... WYBIERZ * W:

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Krok 5 (opcjonalny): Migruj procedury składowane
Funkcja w pamięci może również zmodyfikować procedurę przechowywaną w celu zwiększenia wydajności.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Zagadnienia dotyczące procedur składowanych skompilowanych w sposób macierzysty
Procedura składowana skompilowana w sposób macierzysty musi mieć następujące opcje w klauzuli T-SQL WITH:

* NATIVE_COMPILATION
* SCHEMAbinding: oznacza tabele, których definicje kolumn w procedurze składowanej nie mogą zostać zmienione w jakikolwiek sposób, który mógłby mieć wpływ na procedurę składowaną, chyba że porzucasz procedurę składowaną.

Moduł macierzysty musi używać jednego dużego [bloku](https://msdn.microsoft.com/library/dn452281.aspx) do zarządzania transakcjami. Brak roli dla jawnej BEGIN TRANSACTION lub dla transakcji WYCOFYWANia. Jeśli kod wykrywa naruszenie reguły biznesowej, może przerwać blok niepodzielny za pomocą instrukcji [throw](https://msdn.microsoft.com/library/ee677615.aspx) .

### <a name="typical-create-procedure-for-natively-compiled"></a>Typowa procedura tworzenia dla skompilowanej natywnie
Zazwyczaj T-SQL do utworzenia procedury składowanej skompilowanej w sposób macierzysty jest podobny do następującego szablonu:

```
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

* W przypadku TRANSACTION_ISOLATION_LEVEL migawka jest najbardziej powszechną wartością dla procedury składowanej skompilowanej w sposób macierzysty. Jednak obsługiwane są również podzbiór innych wartości:
  
  * ODCZYT POWTARZALNY
  * SERIALIZABLE
* Wartość języka musi być obecna w widoku sys. Languages.

### <a name="how-to-migrate-a-stored-procedure"></a>Jak przeprowadzić migrację procedury składowanej
Kroki migracji są następujące:

1. Uzyskaj skrypt procedury tworzenia do zwykłej interpretacji procedury składowanej.
2. Napisz ponownie nagłówek, aby dopasować poprzedni szablon.
3. Należy upewnić się, czy kod T-SQL procedury składowanej korzysta z funkcji, które nie są obsługiwane przez procedury składowane skompilowane w sposób macierzysty. Zaimplementuj obejścia w razie potrzeby.
   
   * Aby uzyskać szczegółowe informacje, zobacz [problemy z migracją dla procedur składowanych skompilowanych w](https://msdn.microsoft.com/library/dn296678.aspx)sposób macierzysty.
4. Zmień nazwę starej procedury składowanej przy użyciu SP_RENAME. Lub po prostu upuść go.
5. Uruchom edytowaną procedurę tworzenia skryptu T-SQL.

## <a name="step-6-run-your-workload-in-test"></a>Krok 6: Uruchamianie obciążenia w teście
Uruchom obciążenie w testowej bazie danych, która jest podobna do obciążenia działającego w produkcyjnej bazie danych. Powinno to ujawnić wzrost wydajności osiągnięty przez użycie funkcji w pamięci dla tabel i procedur składowanych.

Główne atrybuty obciążenia są następujące:

* Liczba jednoczesnych połączeń.
* Współczynnik odczytu i zapisu.

W celu dopasowania i uruchomienia obciążenia testowego należy rozważyć użycie narzędzia ostress. exe, które zostało przedstawione w [tym miejscu](sql-database-in-memory.md).

Aby zminimalizować opóźnienie sieci, należy uruchomić test w tym samym regionie geograficznym platformy Azure, w którym znajduje się baza danych.

## <a name="step-7-post-implementation-monitoring"></a>Krok 7: Monitorowanie po implementacji
Rozważ monitorowanie efektów wydajności implementacji w pamięci w środowisku produkcyjnym:

* [Monitoruj magazyn w pamięci](sql-database-in-memory-oltp-monitoring.md).
* [Monitorowanie usługi Azure SQL Database przy użyciu dynamicznych widoków zarządzania](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Linki pokrewne
* [Przetwarzanie OLTP w pamięci (Optymalizacja w pamięci)](https://msdn.microsoft.com/library/dn133186.aspx)
* [Wprowadzenie do procedur składowanych skompilowanych w sposób macierzysty](https://msdn.microsoft.com/library/dn133184.aspx)
* [Klasyfikator optymalizacji pamięci](https://msdn.microsoft.com/library/dn284308.aspx)

