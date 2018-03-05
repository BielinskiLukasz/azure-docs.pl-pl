---
title: "Jak skonfigurować parametry serwera w bazie danych systemu Azure dla programu MySQL"
description: "W tym artykule opisano sposób konfigurowania parametrów serwera MySQL w bazie danych Azure dla programu MySQL przy użyciu portalu Azure."
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: b3510c616d2a9ba66cb83cb998c42e03fdbb0f2b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Jak skonfigurować parametry serwera w bazie danych Azure dla programu MySQL przy użyciu portalu Azure

Bazy danych platformy Azure dla programu MySQL obsługuje konfigurowanie niektórych parametrów serwera. W tym artykule opisano sposób konfigurowania tych parametrów przy użyciu portalu Azure. Nie wszystkie parametry serwera można dostosować. 

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Przejdź do parametrów serwera w portalu Azure
1. Zaloguj się do portalu Azure, a następnie zlokalizuj bazy danych Azure, aby serwer MySQL.
2. W obszarze **ustawienia** kliknij **parametry serwera** aby otworzyć stronę parametry serwera bazy danych Azure dla programu MySQL.
![Strona parametrów serwera portalu Azure](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Znajdź wszystkie ustawienia, które należy dostosować. Przegląd **opis** kolumny zrozumienie przeznaczenia i dozwolone wartości. 
![Wyliczanie listy w dół](./media/howto-server-parameters/3-toggle_parameter.png)
4. Kliknij przycisk **zapisać** Aby zapisać zmiany.
![Zapisz lub Odrzuć zmiany](./media/howto-server-parameters/4-save_parameters.png)
5. Zapisanie nowej wartości parametrów można przywrócić wszystko, co do wartości domyślnych, wybierając **Resetuj wszystkie domyślne**.
![Resetuj wszystkie domyślne](./media/howto-server-parameters/5-reset_parameters.png)


## <a name="list-of-configurable-server-parameters"></a>Lista parametrów można skonfigurować serwera

Lista parametrów obsługiwanym serwerze stale rośnie. Karta serwera parametrów w portalu Azure można pobrać definicji i skonfiguruj parametry serwera, w zależności od wymagań aplikacji. 

## <a name="nonconfigurable-server-parameters"></a>Parametry serwera Nonconfigurable
Pula buforów InnoDB i maksymalna liczba połączeń nie są konfigurowalne i związane z [warstwy cenowej](concepts-service-tiers.md). 

|**Warstwa cenowa**| **Generowanie obliczeniowe**|**vCore(s)**|**Pula buforów InnoDB (MB)**| **Maksymalna liczba połączeń**|
|---|---|---|---|--|
|Podstawowa| Gen 4| 1| 1024| 50 |
|Podstawowa| Gen 4| 2| 2560| 100 |
|Podstawowa| Gen 5| 1| 1024| 50 |
|Podstawowa| Gen 5| 2| 2560| 100 |
|Ogólne zastosowanie| Gen 4| 2| 2560| 200|
|Ogólne zastosowanie| Gen 4| 4| 5120| 400|
|Ogólne zastosowanie| Gen 4| 8| 10240| 800|
|Ogólne zastosowanie| Gen 4| 16| 20480| 1600|
|Ogólne zastosowanie| Gen 4| 32| 40960| 3200|
|Ogólne zastosowanie| Gen 5| 2| 2560| 200|
|Ogólne zastosowanie| Gen 5| 4| 5120| 400|
|Ogólne zastosowanie| Gen 5| 8| 10240| 800|
|Ogólne zastosowanie| Gen 5| 16| 20480| 1600|
|Ogólne zastosowanie| Gen 5| 32| 40960| 3200|
|Pamięć| Gen 5| 2| 7168| 600|
|Pamięć| Gen 5| 4| 15360| 1250|
|Pamięć| Gen 5| 8| 30720| 2500|
|Pamięć| Gen 5| 16| 62464| 5000|
|Pamięć| Gen 5| 32| 125952| 10 000| 

Te parametry dodatkowego serwera nie są konfigurowane w systemie:

|**Parameter**|**Stała wartość**|
| :------------------------ | :-------- |
|innodb_file_per_table w warstwie podstawowa|WYŁ.|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Inne parametry serwera, które nie są wymienione w tym miejscu są ustawione na wartości domyślne out-of-box MySQL dla wersji [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) i [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="next-steps"></a>Kolejne kroki
- [Biblioteki połączeń dla bazy danych Azure dla programu MySQL](concepts-connection-libraries.md).
