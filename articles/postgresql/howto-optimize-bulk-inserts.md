---
title: Optymalizowanie zbiorcze operacje wstawiania na usługi Azure Database for postgresql w warstwie serwera
description: W tym artykule opisano, jak można optymalizować operacji wstawiania zbiorczego w usłudze Azure Database dla serwera PostgreSQL.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: fba109e04369c05f98e863b7dd0fa3d51f40d0ad
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55810245"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql-server"></a>Optymalizuj zbiorcze operacje wstawiania i używać danych przejściowych w usłudze Azure Database dla serwera PostgreSQL 
W tym artykule opisano, jak zoptymalizować operacji wstawiania zbiorczego i używać danych przejściowych w usłudze Azure Database dla serwera PostgreSQL.

## <a name="use-unlogged-tables"></a>Korzystanie z tabel niezarejestrowanych
Jeśli masz obciążenie operacje pociągające danych przejściowych lub dużych zestawów danych, wstawianie zbiorcze, należy rozważyć użycie tabel niezarejestrowanych.

Tabele niezarejestrowanych jest funkcją PostgreSQL, która można skutecznie zoptymalizować zbiorcze operacje wstawiania. PostgreSQL używa zapisu z wyprzedzeniem rejestrowania (WAL). Udostępnia niepodzielność i odporność, domyślnie. Niepodzielności, spójności, izolacji i trwałości tworzą właściwości ACID. 

Wstawianie do tabeli niezarejestrowanych oznacza że PostgreSQL wstawia bez pisania na transakcji dziennika, która sama jest operacji We/Wy. Co w efekcie te tabele są znacznie szybsze niż zwykłe tabel.

Aby utworzyć tabelę niezarejestrowanych, należy użyć następujących opcji:
- Utwórz nową tabelę niezarejestrowanych przy użyciu składni `CREATE UNLOGGED TABLE <tableName>`.
- Konwertuj istniejącą rejestrowane tabeli niezarejestrowanych tabeli przy użyciu składni `ALTER <tableName> SET UNLOGGED`.  

Aby cofnąć ten proces, należy użyć składni `ALTER <tableName> SET LOGGED`.

## <a name="unlogged-table-tradeoff"></a>Kosztem niezarejestrowanych tabeli
Niezarejestrowanych tabele nie są bezpieczne pod względem awarii. Tabela niezarejestrowanych automatycznie został obcięty po awarii lub zastrzeżeniem nieczyste zamknięcie. Zawartość niezarejestrowanych tabeli nie są replikowane serwery w stanie wstrzymania. Żadnych indeksów utworzona na niezarejestrowanych tabeli są także automatycznie niezarejestrowanych. Insert — po zakończeniu operacji, przekonwertuj tabelę rejestrowane tak, aby insert jest trwały.

Niektórych obciążeń klientów wystąpić około 15 do 20 procent zwiększenie wydajności stosowania niezarejestrowanych tabel.

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj swoje obciążenia do użycia dla danych przejściowych i dużych zbiorcze operacje wstawiania. Znajduje się poniższej dokumentacji PostgreSQL:
 
- [Tworzenie poleceń tabeli SQL](https://www.postgresql.org/docs/current/static/sql-createtable.html)