---
title: Konfigurowanie replikacji danych — Azure Database for MySQL
description: W tym artykule opisano sposób konfigurowania replikacja typu data-in Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 8/7/2020
ms.openlocfilehash: 8ebb524a5297380fca575ce6849fe4c5f15507cb
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90903991"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Jak skonfigurować replikacja typu data-in Azure Database for MySQL

W tym artykule opisano sposób konfigurowania [replikacja typu Data-in](concepts-data-in-replication.md) w Azure Database for MySQL przez skonfigurowanie serwerów głównych i replik. W tym artykule założono, że masz pewne doświadczenie w korzystaniu z serwerów i baz danych MySQL.

> [!NOTE]
> Komunikacja bezpłatna bez opłat
>
> Firma Microsoft obsługuje różnorodne i dołączane środowiska. Ten artykuł zawiera odwołania do programu Word _podrzędny_. Przewodnik po [stylu firmy Microsoft dla komunikacji bezpłatnej](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) jest rozpoznawany jako wykluczony wyraz. Słowo jest używane w tym artykule w celu zapewnienia spójności, ponieważ jest to obecnie słowo, które jest wyświetlane w oprogramowaniu. W przypadku zaktualizowania oprogramowania w celu usunięcia wyrazu ten artykuł zostanie zaktualizowany w celu wyrównania.
>

Aby utworzyć replikę w usłudze Azure Database for MySQL, [replikacja typu Data-in](concepts-data-in-replication.md)  synchronizuje dane z głównego serwera MySQL lokalnie, na maszynach wirtualnych lub w usługach bazy danych w chmurze. Replikacja typu data-in jest wykonywana za pomocą technologii replikacji opartej na pozycji w pliku dziennika binarnego (binlog) natywnej dla programu MySQL. Aby dowiedzieć się więcej na temat replikacji binlog, zobacz [Omówienie replikacji MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Przed wykonaniem kroków opisanych w tym artykule Przejrzyj [ograniczenia i wymagania](concepts-data-in-replication.md#limitations-and-considerations) replikacji danych.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Utwórz serwer MySQL, który ma być używany jako replika

1. Utwórz nowy serwer Azure Database for MySQL

   Utwórz nowy serwer MySQL (np. "replica.mysql.database.azure.com"). Zapoznaj się z tematem [Tworzenie serwera Azure Database for MySQL przy użyciu Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) do tworzenia serwera. Ten serwer jest serwerem "Replica" w replikacja typu data-in.

   > [!IMPORTANT]
   > Serwer Azure Database for MySQL należy utworzyć w warstwach cenowych Ogólnego przeznaczenia lub zoptymalizowanych pod kątem pamięci.
   > 

1. Tworzenie tych samych kont użytkowników i odpowiednich uprawnień

   Konta użytkowników nie są replikowane z serwera głównego do serwera repliki. Jeśli planujesz udostępnienie użytkownikom dostępu do serwera repliki, musisz ręcznie utworzyć wszystkie konta i odpowiednie uprawnienia na tym nowo utworzonym Azure Database for MySQL serwerze.

1. Dodaj adres IP serwera głównego do reguł zapory repliki. 

   Zaktualizuj reguły zapory za pomocą [witryny Azure Portal](howto-manage-firewall-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure](howto-manage-firewall-using-cli.md).

## <a name="configure-the-master-server"></a>Konfigurowanie serwera głównego
Poniższe kroki dotyczą przygotowania i skonfigurowania serwera MySQL hostowanego lokalnie, na maszynie wirtualnej lub usługi bazy danych hostowanej przez innych dostawców chmury dla replikacja typu data-in. Ten serwer jest "głównym" w replikacji danych.


1. Przed kontynuowaniem Przejrzyj [wymagania dotyczące serwera głównego](concepts-data-in-replication.md#requirements) . 

   Na przykład upewnij się, że serwer główny zezwala na ruch przychodzący i wychodzący na porcie 3306 i że serwer główny ma **publiczny adres IP**, usługa DNS jest publicznie dostępna lub ma w pełni kwalifikowaną nazwę domeny (FQDN). 
   
   Przetestuj łączność z serwerem głównym, próbując nawiązać połączenie za pomocą narzędzia, takiego jak wiersz polecenia MySQL hostowany na innym komputerze lub z [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) dostępnego w Azure Portal.

1. Włącz rejestrowanie binarne

   Sprawdź, czy na serwerze głównym włączono rejestrowanie binarne, uruchamiając następujące polecenie: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Jeśli zmienna [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) jest zwracana z wartością "on", rejestrowanie binarne jest włączone na serwerze. 

   Jeśli `log_bin` zwracana jest wartość "off", należy włączyć rejestrowanie binarne, edytując plik my. cnf, aby `log_bin=ON` zmiany zaczęły obowiązywać.

1. Ustawienia serwera głównego

   Replikacja typu data-in wymaga `lower_case_table_names` , aby parametr był spójny między serwerem głównym a serwerami repliki. Domyślnie ten parametr jest 1 w Azure Database for MySQL. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

1. Utwórz nową rolę replikacji i skonfiguruj uprawnienie

   Utwórz konto użytkownika na serwerze głównym, który jest skonfigurowany z uprawnieniami replikacji. Można to zrobić za pomocą poleceń SQL lub narzędzi, takich jak MySQL Workbench. Należy rozważyć, czy ma być przeprowadzana replikacja przy użyciu protokołu SSL, ponieważ należy ją określić podczas tworzenia użytkownika. Zapoznaj się z dokumentacją programu MySQL, aby dowiedzieć się, jak [dodać konta użytkowników](https://dev.mysql.com/doc/refman/5.7/en/user-names.html) na serwerze głównym. 

   W poniższych poleceniach Nowa rola replikacji jest w stanie uzyskać dostęp do serwera głównego z dowolnego komputera, a nie tylko z komputera, który hostuje wzorzec. W tym celu należy określić "syncuser@"% "w poleceniu Create User. Zapoznaj się z dokumentacją programu MySQL, aby dowiedzieć się więcej o [określaniu nazw kont](https://dev.mysql.com/doc/refman/5.7/en/account-names.html).

   **Polecenie SQL**

   *Replikacja przy użyciu protokołu SSL*

   Aby wymagać protokołu SSL dla wszystkich połączeń użytkowników, użyj następującego polecenia, aby utworzyć użytkownika: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replikacja bez protokołu SSL*

   Jeśli protokół SSL nie jest wymagany dla wszystkich połączeń, użyj następującego polecenia, aby utworzyć użytkownika:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   Aby utworzyć rolę replikacji w programie MySQL Workbench, Otwórz panel **Użytkownicy i uprawnienia** w panelu **zarządzania** . Następnie kliknij pozycję **Dodaj konto**. 
 
   :::image type="content" source="./media/howto-data-in-replication/users_privileges.png" alt-text="Użytkownicy i uprawnienia":::

   Wpisz nazwę użytkownika w polu **Nazwa logowania** . 

   :::image type="content" source="./media/howto-data-in-replication/syncuser.png" alt-text="Użytkownik synchronizacji":::
 
   Kliknij panel **role administracyjne** , a następnie wybierz pozycję **replikacja podrzędna** z listy **uprawnień globalnych**. Następnie kliknij przycisk **Zastosuj** , aby utworzyć rolę replikacji.

   :::image type="content" source="./media/howto-data-in-replication/replicationslave.png" alt-text="Replikacja podrzędna":::

1. Ustaw serwer główny do trybu tylko do odczytu

   Przed rozpoczęciem zrzutu bazy danych należy umieścić serwer w trybie tylko do odczytu. W trybie tylko do odczytu, główny nie będzie w stanie przetwarzać żadnych transakcji zapisu. Oceń wpływ na swoją firmę i w razie potrzeby Zaplanuj okno tylko do odczytu w czasie poza godzinami szczytu.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

1. Pobieranie binarnej nazwy pliku dziennika i przesunięcia

   Uruchom [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) polecenie, aby określić bieżącą nazwę pliku dziennika binarnego i przesunięcia.
    
   ```sql
   show master status;
   ```
   Wyniki powinny wyglądać jak poniżej. Pamiętaj, aby zanotować nazwę pliku binarnego, ponieważ zostanie ona użyta w kolejnych krokach.

   :::image type="content" source="./media/howto-data-in-replication/masterstatus.png" alt-text="Wyniki stanu głównego":::
 
## <a name="dump-and-restore-master-server"></a>Zrzuć i przywróć serwer główny

1. Ustal, które bazy danych i tabele, które mają być replikowane, do Azure Database for MySQL i wykonaj Zrzut z serwera głównego.
 
    Możesz użyć mysqldump, aby zrzucić bazy danych z serwera głównego. Aby uzyskać szczegółowe informacje, zobacz [zrzut & przywracanie](concepts-migrate-dump-restore.md). Nie jest konieczne zrzucanie biblioteki MySQL i biblioteki testowej.

1. Ustaw serwer główny w trybie odczytu/zapisu

   Po zrzucie bazy danych Zmień serwer główny MySQL z powrotem na tryb odczytu/zapisu.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

1. Przywróć plik zrzutu na nowy serwer

   Przywróć plik zrzutu do serwera utworzonego w usłudze Azure Database for MySQL. Zapoznaj się z artykułem [zrzut & Przywróć](concepts-migrate-dump-restore.md) , aby przywrócić plik zrzutu do serwera MySQL. Jeśli plik zrzutu jest duży, przekaż go do maszyny wirtualnej na platformie Azure w tym samym regionie, w którym znajduje się serwer repliki. Przywróć go do serwera Azure Database for MySQL z maszyny wirtualnej.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>Łączenie serwerów głównych i replik w celu uruchomienia replikacja typu data-in

1. Ustaw serwer główny

   Wszystkie funkcje replikacja typu data-in są wykonywane przez procedury składowane. Wszystkie procedury można znaleźć w [replikacja typu Data-in procedurach składowanych](reference-data-in-stored-procedures.md). Procedury składowane można uruchamiać w programie MySQL Shell lub MySQL Workbench. 

   Aby połączyć dwa serwery i rozpocząć replikację, zaloguj się do docelowego serwera repliki w usłudze Azure DB dla MySQL i ustaw wystąpienie zewnętrzne jako serwer główny. W tym celu należy użyć `mysql.az_replication_change_master` procedury składowanej na serwerze usługi Azure DB dla programu MySQL.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: Nazwa hosta serwera głównego
   - master_user: Nazwa użytkownika serwera głównego
   - master_password: hasło dla serwera głównego
   - master_log_file: nie uruchomiono binarnej nazwy pliku dziennika `show master status`
   - master_log_pos: uruchamianie binarnej lokalizacji dziennika `show master status`
   - master_ssl_ca: kontekst certyfikatu urzędu certyfikacji. Jeśli nie korzystasz z protokołu SSL, Przekaż pusty ciąg.
       - Zalecane jest, aby przekazać ten parametr jako zmienną. Więcej informacji można znaleźć w poniższych przykładach.

   > [!NOTE]
   > Jeśli serwer główny jest hostowany na maszynie wirtualnej platformy Azure, ustaw opcję "Zezwalaj na dostęp do usług platformy Azure" na wartość "włączone", aby umożliwić serwerom głównym i replice komunikację ze sobą. To ustawienie można zmienić przy użyciu opcji **zabezpieczeń połączenia** . Aby uzyskać więcej informacji, zapoznaj się z tematem [Zarządzanie regułami zapory przy użyciu portalu](howto-manage-firewall-using-portal.md) .
      
   **Przykłady**
   
   *Replikacja przy użyciu protokołu SSL*
   
   Zmienna `@cert` jest tworzona przez uruchomienie następujących poleceń MySQL: 
   
      ```sql
      SET @cert = '-----BEGIN CERTIFICATE-----
      PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
      -----END CERTIFICATE-----'
      ```
   
   Replikacja przy użyciu protokołu SSL jest skonfigurowana między serwerem głównym hostowanym w domenie "companya.com" i serwerem repliki hostowanym w Azure Database for MySQL. Ta procedura składowana jest uruchamiana w replice. 
   
      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
      ```
   *Replikacja bez protokołu SSL*
   
   Replikacja bez protokołu SSL jest skonfigurowana między serwerem głównym hostowanym w domenie "companya.com" i serwerem repliki hostowanym w Azure Database for MySQL. Ta procedura składowana jest uruchamiana w replice.
   
      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
      ```

1. Filtrowanie 
 
   Jeśli chcesz pominąć replikację niektórych tabel z poziomu głównego, zaktualizuj `replicate_wild_ignore_table` parametr serwera na serwerze repliki. Można podać więcej niż jeden wzorzec tabeli przy użyciu listy rozdzielanej przecinkami.

   Zapoznaj się z [dokumentacją programu MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table) , aby dowiedzieć się więcej o tym parametrze. 
    
   Aby zaktualizować parametr, można użyć [Azure Portal](howto-server-parameters.md) lub [interfejsu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md).

1. Rozpocznij replikację

   Wywołaj `mysql.az_replication_start` procedurę składowaną, aby zainicjować replikację.

   ```sql
   CALL mysql.az_replication_start;
   ```

1. Sprawdź stan replikacji

   Wywołaj [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) polecenie na serwerze repliki, aby wyświetlić stan replikacji.
    
   ```sql
   show slave status;
   ```

   Jeśli stan i ma `Slave_IO_Running` `Slave_SQL_Running` wartość "yes", a wartością `Seconds_Behind_Master` jest "0", replikacja działa prawidłowo. `Seconds_Behind_Master` wskazuje, jak późna jest replika. Jeśli wartość nie jest równa "0", oznacza to, że replika przetwarza aktualizacje. 

## <a name="other-stored-procedures"></a>Inne procedury składowane

### <a name="stop-replication"></a>Zatrzymywanie replikacji

Aby zatrzymać replikację między serwerem głównym a programem repliki, należy użyć następującej procedury składowanej:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Usuń relację replikacji

Aby usunąć relację między serwerem głównym a programem repliki, należy użyć następującej procedury składowanej:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Pomiń błąd replikacji

Aby pominąć błąd replikacji i umożliwić wykonanie replikacji, należy użyć następującej procedury składowanej:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat [replikacja typu data-in](concepts-data-in-replication.md) Azure Database for MySQL. 
