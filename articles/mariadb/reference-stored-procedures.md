---
title: Procedury składowane zarządzania — Azure Database for MariaDB
description: Dowiedz się, które procedury składowane w Azure Database for MariaDB są przydatne, aby ułatwić Konfigurowanie replikacji danych, ustawianie strefy czasowej i zabicia zapytań.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 2f6d1e20db64cb0c2a64771ea26b971b22031fd9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79529994"
---
# <a name="azure-database-for-mariadb-management-stored-procedures"></a>Procedury składowane zarządzania Azure Database for MariaDB

Procedury składowane są dostępne na serwerach Azure Database for MariaDB, aby ułatwić zarządzanie serwerem MariaDB. Obejmuje to zarządzanie połączeniami serwera, kwerendami i konfigurowaniem replikacja typu data-in.  

## <a name="data-in-replication-stored-procedures"></a>Replikacja typu data-in procedury składowane

Replikacja typu data-in umożliwia synchronizowanie danych z serwera MariaDB uruchomionego lokalnie, na maszynach wirtualnych lub w usługach bazy danych hostowanych przez innych dostawców chmury do usługi Azure Database for MariaDB.

Poniższe procedury składowane służą do konfigurowania lub usuwania replikacja typu data-in między serwerem głównym a repliką.

|**Nazwa procedury składowanej**|**Parametry wejściowe**|**Parametry wyjściowe**|**Uwaga dotycząca użycia**|
|-----|-----|-----|-----|
|*MySQL. az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|Nie dotyczy|Aby przenieść dane z trybem SSL, Przekaż kontekst certyfikatu urzędu certyfikacji do parametru master_ssl_ca. </br><br>Aby przesłać dane bez protokołu SSL, Przekaż pusty ciąg do parametru master_ssl_ca.|
|*Baza danych MySQL. az_replication _start*|Nie dotyczy|Nie dotyczy|Uruchamia replikację.|
|*Baza danych MySQL. az_replication _stop*|Nie dotyczy|Nie dotyczy|Kończy replikację.|
|*Baza danych MySQL. az_replication _remove_master*|Nie dotyczy|Nie dotyczy|Usuwa relację replikacji między serwerem głównym a repliką.|
|*MySQL. az_replication_skip_counter*|Nie dotyczy|Nie dotyczy|Pomija jeden błąd replikacji.|

Aby skonfigurować replikacja typu data-in między wzorcem a repliką w Azure Database for MariaDB, zapoznaj się z [tematem konfigurowanie replikacja typu Data-in](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Inne procedury składowane

Poniższe procedury składowane są dostępne w Azure Database for MariaDB zarządzania serwerem.

|**Nazwa procedury składowanej**|**Parametry wejściowe**|**Parametry wyjściowe**|**Uwaga dotycząca użycia**|
|-----|-----|-----|-----|
|*MySQL. az_kill*|processlist_id|Nie dotyczy|Równoważne [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) polecenie. Nastąpi przerwanie połączenia skojarzonego z podanym processlist_id po zakończeniu wszelkich instrukcji wykonywanych przez połączenie.|
|*MySQL. az_kill_query*|processlist_id|Nie dotyczy|Równoważne [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) polecenie. Zakończy instrukcję, że połączenie jest aktualnie wykonywane. Opuszcza połączenie.|
|*MySQL. az_load_timezone*|Nie dotyczy|Nie dotyczy|Ładuje tabele strefy czasowej, aby zezwolić na `time_zone` ustawienie wartości nazwanych (np. "USA/Pacyfik").|

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak skonfigurować [replikacja typu Data-in](howto-data-in-replication.md)
- Dowiedz się, jak używać [tabel strefy czasowej](howto-server-parameters.md#working-with-the-time-zone-parameter)