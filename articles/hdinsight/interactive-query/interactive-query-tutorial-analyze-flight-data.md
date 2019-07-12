---
title: 'Samouczek: Wykonaj extract, transform, operacji ładowania (ETL), za pomocą interakcyjnych zapytań w usłudze Azure HDInsight'
description: Samouczek — Dowiedz się, jak wyodrębnianie danych z zestawu danych pierwotnych CSV, przekształcać je za pomocą interakcyjnych zapytań na HDInsight, a następnie załadować przekształcone dane do bazy danych Azure SQL database przy użyciu narzędzia Apache Sqoop.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 07/02/2019
ms.author: hrasheed
ms.custom: hdinsightactive,mvc
ms.openlocfilehash: fbab8502c088c2ae7a4b8e87285d7e4cac1de4c0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807400"
---
# <a name="tutorial-extract-transform-and-load-data-using-interactive-query-in-azure-hdinsight"></a>Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych za pomocą interakcyjnych zapytań w usłudze Azure HDInsight

W tym samouczku pierwotne pliku danych CSV lotu publicznie dostępnych danych, zaimportuj go do magazynu klastra HDInsight i następnie przekształcić dane za pomocą interakcyjnych zapytań w usłudze Azure HDInsight. Przekształcone dane można załadować do bazy danych Azure SQL Database przy użyciu narzędzia [Apache Sqoop](https://sqoop.apache.org/).

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Pobieranie przykładowych danych lotów
> * Przekazywanie danych do klastra usługi HDInsight
> * Przekształcanie danych za pomocą interakcyjnych zapytań
> * Tworzenie tabeli w bazie danych Azure SQL
> * Aby wyeksportować dane do usługi Azure SQL database przy użyciu narzędzia Sqoop

## <a name="prerequisites"></a>Wymagania wstępne

* Klaser zapytania interaktywnego HDInsight. Zobacz [tworzenie technologii Apache Hadoop clusters, przy użyciu witryny Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md) i wybierz **Interactive Query** dla **typ klastra**.

* Usługi Azure SQL Database. Używasz bazy danych Azure SQL Database jako docelowego magazynu danych. Jeśli nie masz bazy danych SQL, zobacz [Tworzenie bazy danych Azure SQL Database w witrynie Azure Portal](/azure/sql-database/sql-database-single-database-get-started).

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Pobieranie danych lotów

1. Przejdź do strony [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Na stronie usuń zaznaczenie wszystkich pól, a następnie wybierz następujące wartości:

   | Name (Nazwa) | Wartość |
   | --- | --- |
   | Rok filtrowania |2019 |
   | Okres filtrowania |January (Styczeń) |
   | Pola |Rok, FlightDate, Reporting_Airline, DOT_ID_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, pochodzenia, OriginCityName, OriginState, DestAirportID, miejsca docelowego, DestCityName, DestState, DepDelayMinutes, ArrDelay ArrDelayMinutes, CarrierDelay WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |

3. Wybierz przycisk **Download** (Pobierz). Uzyskasz plik zip z wybranymi polami danych.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Przekazywanie danych do klastra usługi HDInsight

Istnieje wiele sposobów przekazywania danych do magazynu skojarzonego z klastrem usługi HDInsight. W tej sekcji przekażesz dane za pomocą polecenia `scp`. Aby dowiedzieć się więcej o innych sposobach przekazywania danych, zobacz [Upload data to HDInsight (przekazywanie danych do usługi HDInsight)](../hdinsight-upload-data.md).

1. Przekaż plik zip do węzła głównego klastra HDInsight. Edytuj poniższe polecenie, zastępując `FILENAME` z nazwą pliku .zip i `CLUSTERNAME` o nazwie klastra HDInsight. Następnie otwórz okno wiersza polecenia, ustawić katalogu roboczego do lokalizacji pliku, a następnie wprowadź polecenie.

    ```cmd
    scp FILENAME.zip sshuser@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.zip
    ```

    Jeśli zostanie wyświetlony monit o podanie tak lub nie, aby kontynuować, tak, wpisz w wierszu polecenia i naciśnij klawisz enter. Tekst nie jest widoczna w oknie, podczas wpisywania.

2. Po zakończeniu przekazywania połącz się z klastrem przy użyciu protokołu SSH. Edytuj poniższe polecenie, zastępując `CLUSTERNAME` o nazwie klastra HDInsight. Wprowadź następujące polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Ustawienie zmiennej środowiskowej po ustanowieniu połączenia SSH. Zastąp `FILE_NAME`, `SQL_SERVERNAME`, `SQL_DATABASE`, `SQL_USER`, i `SQL_PASWORD` odpowiednimi wartościami. Następnie wpisz polecenie:

    ```bash
    export FILENAME=FILE_NAME
    export SQLSERVERNAME=SQL_SERVERNAME
    export DATABASE=SQL_DATABASE
    export SQLUSER=SQL_USER
    export SQLPASWORD='SQL_PASWORD'
    ```

4. Rozpakuj plik zip, wprowadzając poniższe polecenie:

    ```bash
    unzip $FILENAME.zip
    ```

5. Utwórz katalog magazynu HDInsight, a następnie skopiuj plik CSV do katalogu, wprowadzając poniższe polecenie:

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put $FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>Przekształcanie danych za pomocą zapytania oprogramowania Hive

Istnieje wiele sposobów uruchamiania zadania oprogramowania Hive w klastrze usługi HDInsight. W tej sekcji uruchomisz zadanie oprogramowania Hive za pomocą usługi [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell). Aby uzyskać informacje o innych metodach uruchamiania zadania Hive, zobacz [Używanie oprogramowania Apache Hive w usłudze HDInsight](../hadoop/hdinsight-use-hive.md).

W ramach zadania oprogramowania Hive można zaimportować dane z pliku csv do tabeli oprogramowania Hive o nazwie **Delays**.

1. W wierszu polecenia SSH, którą już masz dla klastra HDInsight, użyj następującego polecenia, aby utworzyć i edytować plik o nazwie **flightdelays.hql**:

    ```bash
    nano flightdelays.hql
    ```

2. Użyj następującego tekstu jako zawartości tego pliku:

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
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
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

3. Aby zapisać plik, naciśnij klawisz **Ctrl + X**, następnie **y**, wprowadź.

4. Aby uruchomić oprogramowanie Hive i uruchomić plik **flightdelays.hql**, użyj następującego polecenia:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

5. Gdy skrypt **flightdelays.hql** zakończy działanie, użyj następującego polecenia, aby otworzyć interaktywną sesję usługi Beeline:

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

    To zapytanie pobiera listę miejscowości z opóźnieniami ze względu na pogodę i średni czas opóźnienia, a następnie zapisuje ją w lokalizacji `/tutorials/flightdelays/output`. Później narzędzie Sqoop odczyta dane z tej lokalizacji i wyeksportuje je do bazy danych Azure SQL Database.

7. Aby zakończyć działanie usługi Beeline, wprowadź polecenie `!quit` w wierszu polecenia.

## <a name="create-a-sql-database-table"></a>Tworzenie tabeli bazy danych SQL

Istnieje wiele sposobów nawiązywania połączenia z bazą danych SQL i tworzenia tabeli. W poniższej procedurze użyto rozwiązania [FreeTDS](http://www.freetds.org/) z klastra usługi HDInsight.

1. Aby zainstalować pakiet FreeTDS, użyj następującego polecenia z otwartego połączenia SSH do klastra:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Po zakończeniu instalacji użyj poniższego polecenia, aby połączyć się z serwerem usługi SQL Database.

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    Uzyskasz dane wyjściowe podobne do następującego tekstu:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to <yourdatabase>
    1>
    ```

3. W wierszu `1>` wprowadź następujące wiersze:

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
    ([origin_city_name] ASC))
    GO
    ```

    Jeśli wprowadzono instrukcję `GO`, zostaną obliczone poprzednie instrukcje. Ta instrukcja tworzy tabelę o nazwie **opóźnienia**, przy użyciu indeksu klastrowanego.

    Użyj następującego zapytania, aby sprawdzić, czy utworzono tabelę:

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    Dane wyjściowe będą podobne do następującego tekstu:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

4. Wprowadź ciąg `exit` w wierszu `1>`, aby zakończyć działanie narzędzia tsql.

## <a name="export-data-to-sql-database-using-apache-sqoop"></a>Eksportowanie danych do bazy danych SQL przy użyciu narzędzia Apache Sqoop

W poprzednich sekcjach skopiowano przekształcone dane w lokalizacji `/tutorials/flightdelays/output`. W tej sekcji użyjesz narzędzia Sqoop, aby wyeksportować dane z lokalizacji `/tutorials/flightdelays/output` do tabeli utworzonej w bazie danych Azure SQL Database.

1. Sprawdź, czy Sqoop widzą bazy danych SQL, wprowadzając poniższe polecenie:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433 --username $SQLUSER --password $SQLPASWORD
    ```

    To polecenie zwraca listę baz danych, łącznie z bazą danych, w którym utworzono `delays` tabeli wcześniej.

2. Eksportowanie danych z `/tutorials/flightdelays/output` do `delays` tabeli, wprowadzając poniższe polecenie:

    ```bash
    sqoop export --connect "jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433;database=$DATABASE" --username $SQLUSER --password $SQLPASWORD --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop nawiązanie połączenia z bazą danych, który zawiera `delays` tabeli i eksportuje dane z `/tutorials/flightdelays/output` do katalogu `delays` tabeli.

3. Po zakończeniu działania polecenia sqoop, należy użyć narzędzia tsql do łączenia z bazą danych, wprowadzając poniższe polecenie:

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    Aby sprawdzić, czy dane zostały wyeksportowane do tabeli delays, użyj następujących instrukcji:

    ```sql
    SELECT * FROM delays
    GO
    ```

    Powinna zostać wyświetlona lista danych w tabeli. Tabela zawiera nazwę miejscowości i średni czas opóźnienia lotów dla tej miejscowości. 

    Wpisz `exit`, aby zakończyć działanie narzędzia tsql.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu korzystania z samouczka warto usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Aby usunąć klaster, zobacz [usunąć klaster usługi HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały podjęte w pierwotnych pliku danych CSV zaimportowany do magazynu klastra HDInsight i następnie przekształcić dane za pomocą interakcyjnych zapytań w usłudze Azure HDInsight.  Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat łącznika magazynu Apache Hive.

> [!div class="nextstepaction"]
>[Integrowanie platformy Apache Spark i Apache Hive za pomocą łącznika magazynu Hive](./apache-hive-warehouse-connector.md)
