---
title: 'Samouczek: Wykonywanie operacji wyodrębniania, przekształcania i ładowania (ETL) przy użyciu technologii Apache Hive w usłudze Azure HDInsight'
description: W ramach tego samouczka dowiesz się, jak wyodrębnić dane z zestawu danych pierwotnych w postaci pliku CSV, przekształcić je za pomocą technologii Apache Hive w usłudze Azure HDInsight, a następnie załadować przekształcone dane do bazy danych Azure SQL Database za pomocą narzędzia Sqoop.
services: storage
author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jamesbak
ms.openlocfilehash: cdb89b552d0e328e6685e2bd62ea135e15e4b074
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013453"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-apache-hive-on-azure-hdinsight"></a>Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu technologii Apache Hive w usłudze Azure HDInsight

W ramach tego samouczka wykonasz operację ETL: wyodrębnianie, przekształcanie i ładowanie danych. Pobierzesz plik danych pierwotnych w formacie CSV, zaimportujesz te dane do klastra usługi Azure HDInsight, przekształcisz je przy użyciu technologii Apache Hive i załadujesz do usługi Azure SQL Database za pomocą narzędzia Apache Sqoop.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyodrębnianie danych i przekazywanie ich do klastra usługi HDInsight.
> * Przekształcanie danych przy użyciu technologii Apache Hive.
> * Ładowanie danych do usługi Azure SQL Database za pomocą narzędzia Sqoop.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

* **Konto usługi Azure Data Lake Storage Gen2 skonfigurowane pod kątem usługi HDInsight**

    Zobacz [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2).

* **Klaster Hadoop oparty na systemie Linux w usłudze HDInsight**

    Zobacz [Szybki start: Rozpoczynanie pracy z platformami Apache Hadoop i Apache Hive w usłudze Azure HDInsight przy użyciu witryny Azure Portal](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-linux-create-cluster-get-started-portal).

* **Azure SQL Database**: Używasz bazy danych Azure SQL Database jako docelowego magazynu danych. Jeśli nie masz bazy danych SQL, zobacz [Tworzenie bazy danych SQL platformy Azure w witrynie Azure Portal](../../sql-database/sql-database-get-started.md).

* **Interfejs wiersza polecenia platformy Azure**: Jeśli nie zainstalowano interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* **Klient protokołu Secure Shell (SSH)**: Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Hadoop) przy użyciu protokołu SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> Procedura przedstawiona w tym artykule wymaga klastra usługi HDInsight używającego systemu Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą Azure HDInsight w wersji 3.4 lub nowszej. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a name="download-the-flight-data"></a>Pobieranie danych lotów

1. Przejdź do strony [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Na stronie wybierz następujące wartości:

   | Name (Nazwa) | Wartość |
   | --- | --- |
   | Rok filtrowania |2013 |
   | Filter Period (Okres filtrowania) |January (Styczeń) |
   | Pola |Year, FlightDate, Reporting_Airline, IATA_CODE_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   
   Wyczyść wszystkie pozostałe pola.

3. Wybierz przycisk **Download** (Pobierz). Uzyskasz plik zip z wybranymi polami danych.

## <a name="extract-and-upload-the-data"></a>Wyodrębnianie i przekazywanie danych

W tej sekcji przekażesz dane do klastra usługi HDInsight, a następnie skopiujesz te dane na konto usługi Data Lake Storage Gen2.

1. Otwórz wiersz polecenia i za pomocą następującego polecenia Secure Copy (Scp) przekaż plik zip do węzła głównego klastra usługi HDInsight:

   ```bash
   scp <file-name>.zip <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net:<file-name.zip>
   ```

   * Zamień symbol zastępczy `<file-name>` na nazwę pliku zip.
   * Zamień symbol zastępczy `<ssh-user-name>` na nazwę logowania protokołu SSH dla klastra usługi HDInsight.
   * Zamień symbol zastępczy `<cluster-name>` na nazwę klastra usługi HDInsight.

   Jeśli uwierzytelniasz nazwę logowania SSH przy użyciu hasła, zostanie wyświetlony monit o podanie hasła.

   Jeśli używasz klucza publicznego, może być konieczne użycie parametru `-i` w celu określenia ścieżki do zgodnego klucza prywatnego. Na przykład `scp -i ~/.ssh/id_rsa <file_name>.zip <user-name>@<cluster-name>-ssh.azurehdinsight.net:`.

2. Po zakończeniu przekazywania połącz się z klastrem przy użyciu protokołu SSH. W wierszu polecenia wprowadź następujące polecenie:

   ```bash
   ssh <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net
   ```

3. Użyj następującego polecenia, aby rozpakować plik zip:

   ```bash
   unzip <file-name>.zip
   ```

   To polecenie umożliwia wyodrębnienie pliku **csv**.

4. Za pomocą następującego polecenia utwórz system plików usługi Data Lake Storage Gen2.

   ```bash
   hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/
   ```

   Zamień symbol zastępczy `<file-system-name>` na nazwę, którą chcesz nadać systemowi plików.

   Zastąp symbol zastępczy `<storage-account-name>` nazwą konta magazynu.

5. Utwórz katalog za pomocą następującego polecenia.

   ```bash
   hdfs dfs -mkdir -p abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data
   ```

6. Za pomocą następującego polecenia skopiuj plik *csv* do katalogu:

   ```bash
   hdfs dfs -put "<file-name>.csv" abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data/
   ```

   Jeśli nazwa pliku zawiera spacje lub znaki specjalne, należy umieścić ją w cudzysłowie.

## <a name="transform-the-data"></a>Przekształcanie danych

W tej sekcji uruchomisz zadanie Apache Hive za pomocą usługi Beeline.

W ramach zadania Apache Hive zaimportujesz dane z pliku csv do tabeli Apache Hive o nazwie **delays**.

1. W wierszu polecenia SSH, który jest już dostępny dla klastra usługi HDInsight, wydaj następujące polecenie, aby utworzyć i edytować nowy plik o nazwie **flightdelays.hql**:

   ```bash
   nano flightdelays.hql
   ```

2. Zmodyfikuj następujący tekst, zamieniając symbole zastępcze `<file-system-name>` i `<storage-account-name>` na nazwę systemu plików i nazwę konta magazynu. Następnie skopiuj i wklej tekst do konsoli programu nano, klikając prawym przyciskiem myszy przy naciśniętym klawiszu SHIFT.

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION 'abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION 'abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/processed'
    AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

3. Zapisz ten plik przy użyciu kombinacji klawiszy CTRL+X, a następnie wpisz `Y` po wyświetleniu monitu.

4. Aby uruchomić oprogramowanie Hive i uruchomić plik **flightdelays.hql**, użyj następującego polecenia:

   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
   ```

5. Gdy skrypt __flightdelays.hql__ zakończy działanie, użyj następującego polecenia, aby otworzyć interaktywną sesję usługi Beeline:

   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
   ```

6. Po otrzymaniu wiersza `jdbc:hive2://localhost:10001/>` użyj następującego zapytania, aby pobrać dane z zaimportowanych danych opóźnień lotów:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

   To zapytanie pobiera listę miejscowości z opóźnieniami ze względu na pogodę i średni czas opóźnienia, a następnie zapisuje ją w lokalizacji `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`. Później narzędzie Sqoop odczyta dane z tej lokalizacji i wyeksportuje je do bazy danych Azure SQL Database.

7. Aby zakończyć działanie usługi Beeline, wprowadź polecenie `!quit` w wierszu polecenia.

## <a name="create-a-sql-database-table"></a>Tworzenie tabeli bazy danych SQL

Aby wykonać tę operację, musisz dysponować nazwą serwera bazy danych SQL Database. Wykonaj następujące kroki, aby znaleźć nazwę serwera.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

2. Wybierz pozycję **Bazy danych SQL**.

3. Filtruj listę według nazwy bazy danych, która ma zostać użyta. Nazwa serwera jest wyświetlana w kolumnie **Nazwa serwera**.

4. Filtruj listę według nazwy bazy danych, której chcesz użyć. Nazwa serwera jest wyświetlana w kolumnie **Nazwa serwera**.

    ![Uzyskiwanie szczegółów serwera Azure SQL](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Uzyskiwanie szczegółów serwera Azure SQL")

    Istnieje wiele sposobów nawiązywania połączenia z bazą danych SQL i tworzenia tabeli. W poniższej procedurze użyto rozwiązania [FreeTDS](http://www.freetds.org/) z klastra usługi HDInsight.

5. Aby zainstalować rozwiązanie FreeTDS, użyj następującego polecenia dla połączenia SSH z klastrem:

   ```bash
   sudo apt-get --assume-yes install freetds-dev freetds-bin
   ```

6. Po zakończeniu instalacji użyj poniższego polecenia, aby połączyć się z serwerem bazy danych SQL Database.

   ```bash
   TDSVER=8.0 tsql -H '<server-name>.database.windows.net' -U '<admin-login>' -p 1433 -D '<database-name>'
    ```
   * Zamień symbol zastępczy `<server-name>` na nazwę serwera usługi SQL Database.

   * Zamień symbol zastępczy `<admin-login>` na identyfikator logowania administratora usługi SQL Database.

   * Zamień symbol zastępczy `<database-name>` na nazwę bazy danych.

   Gdy zostanie wyświetlony monit, wprowadź hasło dla nazwy logowania administratora bazy danych SQL Database.

   Uzyskasz dane wyjściowe podobne do następującego tekstu:

   ```
   locale is "en_US.UTF-8"
   locale charset is "UTF-8"
   using default charset "UTF-8"
   Default database being set to sqooptest
   1>
   ```

7. W wierszu `1>` wprowadź następujące instrukcje:

   ```hiveql
   CREATE TABLE [dbo].[delays](
   [OriginCityName] [nvarchar](50) NOT NULL,
   [WeatherDelay] float,
   CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
   ([OriginCityName] ASC))
   GO
   ```

8. Jeśli wprowadzono instrukcję `GO`, zostaną obliczone poprzednie instrukcje.

   Zapytanie tworzy tabelę o nazwie **delays** z indeksem klastrowanym.

9. Za pomocą następującego zapytania sprawdź, czy utworzono tabelę:

   ```hiveql
   SELECT * FROM information_schema.tables
   GO
   ```

   Dane wyjściowe będą podobne do następującego tekstu:

   ```
   TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
   databaseName       dbo             delays        BASE TABLE
   ```

10. Wprowadź ciąg `exit` w wierszu `1>`, aby zakończyć działanie narzędzia tsql.

## <a name="export-and-load-the-data"></a>Eksportowanie i ładowanie danych

W poprzednich sekcjach skopiowano przekształcone dane w lokalizacji `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`. W tej sekcji użyjesz narzędzia Sqoop, aby wyeksportować dane z lokalizacji `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` do tabeli utworzonej w bazie danych Azure SQL Database.

1. Aby sprawdzić, czy baza danych SQL jest widoczna w narzędziu Sqoop, użyj następującego polecenia:

   ```bash
   sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
   ```

   To polecenie zwraca listę baz danych, łącznie z bazą danych, w której utworzono tabelę **delays**.

2. Za pomocą następującego polecenia wyeksportuj dane z tabeli **hivesampletable** do tabeli **delays**:

   ```bash
   sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<file-system-name>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
   ```

   Narzędzie Sqoop nawiązuje połączenie z bazą danych zawierającą tabelę **delays** i eksportuje dane z katalogu `/tutorials/flightdelays/output` do tabeli **delays**.

3. Po zakończeniu działania polecenia `sqoop` użyj narzędzia tsql, aby nawiązać połączenie z bazą danych:

   ```bash
   TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
   ```

4. Za pomocą następujących instrukcji sprawdź, czy dane zostały wyeksportowane do tabeli **delays**:

   ```sql
   SELECT * FROM delays
   GO
   ```

   Powinna zostać wyświetlona lista danych w tabeli. Tabela zawiera nazwę miejscowości i średni czas opóźnienia lotów dla tej miejscowości.

5. Wpisz polecenie `exit`, aby wyjść z narzędzia tsql.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Wszystkie zasoby używane w tym samouczku istniały już wcześniej. Oczyszczanie nie jest konieczne.

## <a name="next-steps"></a>Kolejne kroki

Aby poznać więcej sposobów pracy z danymi w usłudze HDInsight, zobacz następujący artykuł:

> [!div class="nextstepaction"]
> [Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu usługi Azure Databricks](./data-lake-storage-use-hdi-cluster.md)
