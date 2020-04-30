---
title: Strojenie wydajności za pomocą buforowania zestawu wyników
description: Omówienie funkcji buforowania zestawu wyników dla puli SQL Synapse w usłudze Azure Synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: eadbe13269ce1259b4560af117f5b15b3b294151
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81730602"
---
# <a name="performance-tuning-with-result-set-caching"></a>Strojenie wydajności za pomocą buforowania zestawu wyników

Gdy buforowanie zestawu wyników jest włączone, funkcja analizy SQL automatycznie buforuje wyniki zapytania w bazie danych użytkownika w celu powtarzanego użycia.  Dzięki temu kolejne wykonania zapytania będą uzyskiwać wyniki bezpośrednio z utrwalonej pamięci podręcznej, więc ponowne obliczenie nie jest konieczne.   Buforowanie zestawu wyników zwiększa wydajność zapytań i zmniejsza użycie zasobów obliczeniowych.  Ponadto zapytania korzystające z zbuforowanego zestawu wyników nie używają żadnych miejsc współbieżności, więc nie są wliczane do istniejących limitów współbieżności. W celu zapewnienia bezpieczeństwa użytkownicy mogą uzyskiwać dostęp do buforowanych wyników tylko wtedy, gdy mają one takie same uprawnienia dostępu do danych, jak użytkownicy tworzący buforowane wyniki.  

## <a name="key-commands"></a>Polecenia kluczowe

[Włącz/Wyłącz buforowanie zestawu wyników dla bazy danych użytkownika](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Włącz/Wyłącz buforowanie zestawu wyników dla bazy danych użytkownika](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Włącz/Wyłącz buforowanie zestawu wyników dla sesji](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Sprawdź rozmiar buforowanego zestawu wyników](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

[Czyszczenie pamięci podręcznej](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Co nie jest buforowane  

Po włączeniu buforowania zestawu wyników dla bazy danych wyniki są buforowane dla wszystkich zapytań do momentu zapełnienia pamięci podręcznej, z wyjątkiem tych zapytań:

- Zapytania korzystające z funkcji niedeterministycznych, takich jak DateTime. Now ()
- Zapytania korzystające z funkcji zdefiniowanych przez użytkownika
- Zapytania korzystające z tabel z włączonymi zabezpieczeniami na poziomie wierszy lub zabezpieczeniami
- Zapytania zwracające dane z rozmiarem wiersza większym niż 64 KB
- Zapytania zwracające duże ilości danych (>10 GB) 

> [!IMPORTANT]
> Operacje tworzenia pamięci podręcznej zestawu wyników i pobierania danych z pamięci podręcznej odbywają się w węźle kontrolnym wystąpienia puli SQL Synapse.
> Gdy buforowanie zestawu wyników jest włączone, uruchomione zapytania, które zwracają duży zestaw wyników (na przykład >1 GB) mogą spowodować wysokie ograniczenia w węźle kontrolnym i spowalniać ogólną odpowiedź na zapytanie w wystąpieniu.  Te zapytania są często używane podczas operacji eksploracji danych lub ETL. Aby uniknąć naciskania węzła kontrolnego i spowodować problem z wydajnością, użytkownicy powinni wyłączyć buforowanie zestawu wyników w bazie danych przed uruchomieniem tych typów zapytań.  

Uruchom to zapytanie przez czas wykonywania operacji buforowania przez zestaw wyników dla zapytania:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command
FROM sys.dm_pdw_request_steps
WHERE request_id  = <'request_id'>;
```

Oto przykładowe dane wyjściowe zapytania wykonanego z wyłączonym buforowaniem zestawu wyników.

![Zapytanie-kroki-with-RSC-disabled](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Oto przykładowe dane wyjściowe zapytania wykonywanego z włączonym buforowaniem zestawu wyników.

![Zapytanie-kroki-with-RSC-Enabled](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>Gdy są używane buforowane wyniki

Buforowany zestaw wyników jest ponownie używany w przypadku zapytania, jeśli wszystkie poniższe wymagania zostały spełnione:

- Użytkownik, który uruchamia zapytanie, ma dostęp do wszystkich tabel, do których odwołuje się zapytanie.
- Istnieje dokładne dopasowanie między nowym zapytaniem i poprzednim zapytaniem, które spowodowało wygenerowanie pamięci podręcznej zestawu wyników.
- Brak danych lub schematu w tabelach, w których Wygenerowano buforowany zestaw wyników.

Uruchom to polecenie, aby sprawdzić, czy zapytanie zostało wykonane z trafieniem lub chybień pamięci podręcznej wyników. Kolumna result_cache_hit zwraca wartość 1 dla trafienia pamięci podręcznej, 0 dla chybień w pamięci podręcznej oraz wartości ujemne z przyczyn, dla których buforowanie zestawu wyników nie było używane. Sprawdź szczegóły w [tabeli sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Zarządzanie wynikami buforowanymi

Maksymalny rozmiar pamięci podręcznej zestawu wyników wynosi 1 TB na bazę danych.  Buforowane wyniki zostaną automatycznie unieważnione po zmianie źródłowych danych zapytania.  

Wykluczanie pamięci podręcznej jest zarządzane przez usługę SQL Analytics automatycznie zgodnie z tym harmonogramem:

- Co 48 godzin, jeśli zestaw wyników nie został użyty lub został unieważniony.
- Gdy pamięć podręczna zestawu wyników zbliża się do maksymalnego rozmiaru.

Użytkownicy mogą ręcznie opróżnić całą pamięć podręczną zestawu wyników, korzystając z jednej z następujących opcji:

- Wyłącz funkcję pamięci podręcznej zestawu wyników dla bazy danych
- Uruchom polecenie DBCC DROPRESULTSETCACHE podczas połączenia z bazą danych

Wstrzymywanie bazy danych nie będzie pustym zestawem wyników w pamięci podręcznej.  

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej porad programistycznych, zobacz [Omówienie projektowania](sql-data-warehouse-overview-develop.md).
