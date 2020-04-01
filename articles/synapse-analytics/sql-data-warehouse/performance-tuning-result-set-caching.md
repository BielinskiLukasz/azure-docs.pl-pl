---
title: Strojenie wydajności za pomocą buforowania zestawu wyników
description: Omówienie funkcji buforowania zestawu wyników dla usługi SQL Analytics w usłudze Azure Synapse Analytics
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
ms.openlocfilehash: da476dc14949ebab1a054a9624d91acb25b9f2b4
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474483"
---
# <a name="performance-tuning-with-result-set-caching"></a>Strojenie wydajności za pomocą buforowania zestawu wyników  
Gdy buforowanie zestawu wyników jest włączone, sql analytics automatycznie buforuje wyniki kwerendy w bazie danych użytkowników do powtarzalnego użycia.  Dzięki temu kolejne wykonania kwerendy, aby uzyskać wyniki bezpośrednio z utrwalonych pamięci podręcznej, więc ponowneuwkomczenie nie jest potrzebne.   Buforowanie zestawu wyników zwiększa wydajność zapytań i zmniejsza użycie zasobów obliczeniowych.  Ponadto kwerendy przy użyciu zestawu wyników buforowanych nie używają żadnych gniazd współbieżności i w związku z tym nie są wliczane do istniejących limitów współbieżności. Dla zabezpieczeń użytkownicy mogą uzyskiwać dostęp do wyników w pamięci podręcznej tylko wtedy, gdy mają takie same uprawnienia dostępu do danych, jak użytkownicy tworzący wyniki w pamięci podręcznej.  

## <a name="key-commands"></a>Polecenia klawiszy
[Włączanie/WYŁĄCZanie buforowania zestawu wyników dla bazy danych użytkowników](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[Włączanie/WYŁĄCZanie buforowania zestawu wyników dla sesji](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[Sprawdzanie rozmiaru zestawu wyników buforowanych](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[Czyszczenie pamięci podręcznej](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Co nie jest buforowane  

Po włączeniu buforowania zestawu wyników dla bazy danych wyniki są buforowane dla wszystkich zapytań, dopóki pamięć podręczna nie zostanie zapełniona, z wyjątkiem następujących zapytań:
- Kwerendy używające niedeterministycznych funkcji, takich jak DateTime.Now()
- Kwerendy używające funkcji zdefiniowanych przez użytkownika
- Kwerendy używające tabel z włączonymi zabezpieczeniami na poziomie wiersza lub poziomem kolumny
- Kwerendy zwracające dane o rozmiarze wiersza większym niż 64 KB

> [!IMPORTANT]
> Operacje tworzenia pamięci podręcznej zestawu wyników i pobierania danych z pamięci podręcznej odbywać się w węźle sterowania wystąpienia usługi SQL Analytics.
> Gdy buforowanie zestawu wyników jest włączone, uruchamianie kwerend zwracających duży zestaw wyników (na przykład >1 mln wierszy) może spowodować wysokie użycie procesora CPU w węźle sterowania i spowolnić ogólną odpowiedź zapytania w wystąpieniu.  Te zapytania są powszechnie używane podczas eksploracji danych lub operacji ETL. Aby uniknąć stresu węzła sterowania i spowodować problem z wydajnością, użytkownicy powinni wyłączyć buforowanie zestawu wyników w bazie danych przed uruchomieniem tych typów zapytań.  

Uruchom tę kwerendę dla czasu pobranego przez operacje buforowania zestawu wyników dla kwerendy:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command 
FROM sys.dm_pdw_request_steps 
WHERE request_id  = <'request_id'>; 
```

Oto przykład danych wyjściowych dla kwerendy wykonywanej z wyłączonym buforowaniem zestawu wyników.

![Query-steps-with-rsc-disabled](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Oto przykład danych wyjściowych dla kwerendy wykonywanej z włączoną buforowania zestawu wyników.

![Query-steps-with-rsc-enabled](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>Gdy używane są buforowane wyniki

Zestaw wyników w pamięci podręcznej jest ponownie odtwarzany dla kwerendy, jeśli wszystkie następujące wymagania są spełnione:
- Użytkownik, który uruchamia kwerendę ma dostęp do wszystkich tabel, do których odwołuje się w kwerendzie.
- Istnieje dokładne dopasowanie między nową kwerendą a poprzednią kwerendą, która wygenerowała pamięć podręczną zestawu wyników.
- Nie ma żadnych zmian danych lub schematu w tabelach, z których został wygenerowany zestaw wyników w pamięci podręcznej.

Uruchom to polecenie, aby sprawdzić, czy kwerenda została wykonana z trafieniem lub pominięciem pamięci podręcznej wyników. Kolumna result_set_cache zwraca wartość 1 dla trafienia w pamięci podręcznej, 0 dla pominięcia pamięci podręcznej i wartości ujemne z powodów, dla których buforowanie zestawu wyników nie zostało użyte. Sprawdź [szczegóły na dm_pdw_exec_requests.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)

```sql
SELECT request_id, command, result_set_cache FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Zarządzanie wynikami w pamięci podręcznej 

Maksymalny rozmiar pamięci podręcznej zestawu wyników wynosi 1 TB na bazę danych.  Buforowane wyniki są automatycznie unieważniane po zmianie danych kwerendy źródłowej.  

Eksmisja pamięci podręcznej jest zarządzana przez usługę SQL Analytics automatycznie zgodnie z tym harmonogramem: 
- Co 48 godzin, jeśli zestaw wyników nie został użyty lub został unieważniony. 
- Gdy pamięć podręczna zestawu wyników zbliża się do maksymalnego rozmiaru.

Użytkownicy mogą ręcznie opróżnić całą pamięć podręczną zestawu wyników przy użyciu jednej z następujących opcji: 
- Wyłącz funkcję pamięci podręcznej zestawu wyników dla bazy danych 
- Uruchamianie DBCC DROPRESULTSETCACHE po podłączeniu do bazy danych

Wstrzymanie bazy danych nie spowoduje opróżnienia zestawu wyników w pamięci podręcznej.  

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej wskazówek dotyczących rozwoju, zobacz [omówienie rozwoju](sql-data-warehouse-overview-develop.md). 
