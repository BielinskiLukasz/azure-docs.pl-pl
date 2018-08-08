---
title: Używać przepływów pracy programu Oozie usługi Hadoop w HDInsight Azure opartych na systemie Linux
description: Użyj programu Oozie usługi Hadoop w HDInsight opartych na systemie Linux. Dowiedz się, jak zdefiniować przepływ pracy programu Oozie i przesłać zadanie usługi Oozie.
services: hdinsight
ms.service: hdinsight
ms.custom: hdinsightactive
author: omidm1
ms.author: omidm
editor: jasonwhowell
ms.topic: conceptual
ms.date: 06/26/2018
ms.openlocfilehash: a8a1b45d28b8d762a659ccdcc6986dcacb3a9d3e
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39599177"
---
# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Używanie technologii Oozie z usługą Hadoop, aby zdefiniować i uruchomić przepływ pracy na opartą na systemie Linux usługi Azure HDInsight

[!INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Dowiedz się, jak używać programu Apache Oozie z platformą Hadoop w usłudze Azure HDInsight. Oozie jest systemem przepływu pracy i koordynacji, który zarządza zadaniami na platformie Hadoop. Oozie jest zintegrowany ze stosem platformy Hadoop i obsługuje następujące zadania:

* Apache MapReduce
* Apache Pig
* Apache Hive
* Apache Sqoop

Można również użyć programu Oozie do planowania zadań, które są specyficzne dla systemu, np. programów Java lub skryptów powłoki.

> [!NOTE]
> Inną opcję, aby zdefiniować przepływy pracy za pomocą HDInsight jest usługa Azure Data Factory. Aby dowiedzieć się więcej na temat usługi Data Factory, zobacz [korzystanie z języka Pig i Hive z usługą Data Factory][azure-data-factory-pig-hive].


## <a name="prerequisites"></a>Wymagania wstępne

* **Klaster usługi HDInsight**: zobacz [Rozpoczynanie pracy z usługą HDInsight w systemie Linux](/hadoop/apache-hadoop-linux-tutorial-get-started.md)

> [!IMPORTANT]
> Procedura przedstawiona w tym dokumencie wymaga klastra usługi HDInsight używającego systemu Linux. Linux jest jedynym systemem operacyjnym na HDInsight w wersji 3.4 lub nowszej. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a name="example-workflow"></a>Przykładowy przepływ pracy

Przepływ pracy, używane w tym dokumencie zawiera dwie akcje. Akcje są definicje dla zadania, takie jak uruchomienie programu Hive, Sqoop, MapReduce lub inne procesy:

![Diagram przepływu pracy][img-workflow-diagram]

1. Działanie programu Hive jest uruchamiany skrypt HiveQL, aby wyodrębnić rekordy z **hivesampletable** który wchodzi w skład HDInsight. Każdy wiersz danych zawiera opis odwiedziny od określonego urządzenia przenośnego. Format rekordu wygląda następujący tekst:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    Skryptu Hive używany w tym dokumencie jest liczona Suma odwiedzin dotyczącymi poszczególnych platform, takich jak Android i iPhone i przechowywania liczby do nowej tabeli programu Hive.

    Aby uzyskać więcej informacji na temat programu Hive, zobacz temat [Use Hive with HDInsight][hdinsight-use-hive] (Korzystanie z programu Hive z usługą HDInsight).

2. Akcja Sqoop eksportuje zawartość nową tabelę programu Hive do tabeli utworzonej w usłudze Azure SQL Database. Aby uzyskać więcej informacji na temat narzędzia Sqoop zobacz [Hadoop Sqoop korzystanie z HDInsight][hdinsight-use-sqoop].

> [!NOTE]
> Obsługiwane wersje programu Oozie w klastrach HDInsight, zobacz [nowości w wersjach klastra Hadoop dostarczanych przez HDInsight][hdinsight-versions].

## <a name="create-the-working-directory"></a>Utwórz katalog roboczy

Oozie oczekuje, że można przechowywać wszystkie zasoby, które są wymagane dla zadania w tym samym katalogu. W tym przykładzie użyto **wasb: / / / samouczki/useoozie**. Aby utworzyć ten katalog, wykonaj następujące czynności:

1. Połącz się z klastrem HDInsight przy użyciu protokołu SSH:

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Zastąp ciąg `sshuser` nazwą użytkownika protokołu SSH dla klastra. Zastąp `clustername` nazwą klastra. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Można utworzyć katalogu, użyj następującego polecenia:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]
    > `-p` Parametru powoduje utworzenie wszystkich katalogów w ścieżce. **Danych** katalog jest używany do przechowywania danych używanych przez **useooziewf.hql** skryptu.

3. Aby upewnić się, że Oozie może przyjąć tożsamość konta użytkownika, użyj następującego polecenia:

    ```bash
    sudo adduser username users
    ```

    Zastąp `username` z Twoją nazwą użytkownika SSH.

    > [!NOTE]
    > Można zignorować błędy, które wskazują, użytkownik jest już członkiem `users` grupy.

## <a name="add-a-database-driver"></a>Dodaj sterownik bazy danych

Ponieważ ten przepływ pracy używa Sqoop, aby wyeksportować dane do bazy danych SQL, należy podać kopię sterownik JDBC, używane do interakcji z usługą SQL database. Aby skopiować sterownik JDBC do katalogu roboczego, użyj następującego polecenia w sesji SSH:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /tutorials/useoozie/
```

> [!NOTE]
> Może zostać wyświetlony komunikat, że plik już istnieje.

Jeśli przepływ pracy używane inne zasoby, takie jak plik jar, który zawiera aplikację MapReduce, należy dodać także te zasoby.

## <a name="define-the-hive-query"></a>Definiowanie zapytania programu Hive

Wykonaj następujące kroki, aby utworzyć skrypt języka (HiveQL) zapytania programu Hive, który definiuje zapytanie. W przepływie pracy programu Oozie w dalszej części tego dokumentu, zostanie użyte zapytanie.

1. Z poziomu połączenia SSH, użyj następującego polecenia, aby utworzyć plik o nazwie `useooziewf.hql`:

    ```bash
    nano useooziewf.hql
    ```

3. Po otwarciu edytora nano GNU, użyj następującego zapytania, jako zawartość pliku:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Istnieją dwie zmienne używane w skrypcie:

    * `${hiveTableName}`: Zawiera nazwę tabeli, która ma zostać utworzony.

    * `${hiveDataFolder}`Zawiera lokalizację do przechowywania plików danych dla tabeli.

    Plik definicji przepływu pracy workflow.xml w tym samouczku przekazuje te wartości do tego skryptu HiveQL w czasie wykonywania.

4. Aby zakończyć działanie edytora, wybierz klawisze Ctrl + X. Po wyświetleniu monitu wybierz `Y` można zapisać pliku, wprowadź `useooziewf.hql` jako nazwę pliku, a następnie wybierz **Enter**.

5. Użyj następujących poleceń, aby skopiować `useooziewf.hql` do `wasb:///tutorials/useoozie/useooziewf.hql`:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    To polecenie zapisuje `useooziewf.hql` plik z magazynu zgodnego z systemem HDFS dla klastra.

## <a name="define-the-workflow"></a>Zdefiniuj przepływ pracy

Definicje przepływów pracy programu Oozie są zapisywane w Hadoop proces definicji języka (hPDL), który jest język definicji procesu XML. Zdefiniuj przepływ pracy, wykonaj następujące kroki:

1. Aby tworzyć i edytować plik, należy użyć następującej instrukcji:

    ```bash
    nano workflow.xml
    ```

2. Po otwarciu edytora nano, wprowadź następujący kod XML jako zawartość pliku:

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>
        <action name="RunHiveScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScript}</script>
            <param>hiveTableName=${hiveTableName}</param>
            <param>hiveDataFolder=${hiveDataFolder}</param>
        </hive>
        <ok to="RunSqoopExport"/>
        <error to="fail"/>
        </action>
        <action name="RunSqoopExport">
        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.compress.map.output</name>
                <value>true</value>
            </property>
            </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveDataFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\t"</arg>
            <archive>sqljdbc41.jar</archive>
            </sqoop>
        <ok to="end"/>
        <error to="fail"/>
        </action>
        <kill name="fail">
        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

    Istnieją dwie akcje zdefiniowane w przepływie pracy:

   * `RunHiveScript`: Ta akcja jest to Akcja rozpoczęcia i uruchamia `useooziewf.hql` wykonanie skryptu technologii Hive.

   * `RunSqoopExport`: Ta akcja eksportuje dane utworzone skryptu programu Hive do usługi SQL database za pomocą narzędzia Sqoop. Ta akcja działa tylko wtedy, gdy `RunHiveScript` akcji zakończy się pomyślnie.

     Przepływ pracy ma kilka wpisów `${jobTracker}`. Te wpisy spowoduje zastąpienie wartości, których używasz w definicji zadania. Definicja zadania utworzy w dalszej części tego dokumentu.

     Należy również zauważyć `<archive>sqljdbc4.jar</archive>` wpis w sekcji narzędzia Sqoop. Ten wpis powoduje, że Oozie, aby udostępnić to archiwum Sqoop po uruchomieniu tej akcji.

3. Aby zapisać plik, wybierz klawisze Ctrl + X, należy wprowadzić `Y`, a następnie wybierz pozycję **Enter**. 

4. Użyj następującego polecenia, aby skopiować `workflow.xml` plik `/tutorials/useoozie/workflow.xml`:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-the-database"></a>Tworzenie bazy danych

Aby utworzyć bazę danych SQL, wykonaj kroki opisane w [utworzyć bazę danych SQL](../sql-database/sql-database-get-started.md) dokumentu. Podczas tworzenia bazy danych, użyj `oozietest` jako nazwa bazy danych. Ponadto Zanotuj nazwę serwera bazy danych.

### <a name="create-the-table"></a>Utwórz tabelę

> [!NOTE]
> Istnieje wiele sposobów, aby nawiązać połączenie z bazą danych SQL, aby utworzyć tabelę. W poniższej procedurze użyto rozwiązania [FreeTDS](http://www.freetds.org/) z klastra usługi HDInsight.


1. Użyj następującego polecenia, aby zainstalować pakiet FreeTDS w klastrze HDInsight:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Po zainstalowaniu FreeTDS Użyj następującego polecenia, aby nawiązać połączenie serwera bazy danych SQL, która została utworzona wcześniej:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -P <sqlPassword> -p 1433 -D oozietest
    ```

    Otrzymasz dane wyjściowe podobne do następującego tekstu:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

3. W wierszu `1>` wprowadź następujące wiersze:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    Jeśli wprowadzono instrukcję `GO`, zostaną obliczone poprzednie instrukcje. Te instrukcje tworzenia tabeli o nazwie **mobiledata**, które jest używane przez przepływ pracy.

    Aby sprawdzić, czy tabela została utworzona, użyj następujących poleceń:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Zostaną wyświetlone dane wyjściowe podobne do następującego tekstu:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo     mobiledata      BASE TABLE

4. Aby zamknąć narzędzie tsql, wprowadź `exit` na `1>` wiersza.

## <a name="create-the-job-definition"></a>Utwórz definicję zadania

Definicja zadania opisano, gdzie można znaleźć workflow.xml. Gdzie można znaleźć inne pliki używane przez przepływ pracy, takie jak omówiono także `useooziewf.hql`. Ponadto definiuje wartości dla właściwości używane w ramach przepływu pracy i skojarzone pliki.

1. Aby uzyskać pełny adres domyślny magazyn, należy użyć następującego polecenia. Ten adres jest używany w pliku konfiguracji, który zostanie utworzony w następnym kroku.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    To polecenie zwraca informacje, takie jak następujący kod XML:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]
    > Jeśli klaster HDInsight używa usługi Azure Storage jako magazynu domyślnego `<value>` zawartość elementu zaczynają się od `wasb://`. Jeśli usługi Azure Data Lake Store jest używana zamiast tego, rozpoczyna się od `adl://`.

    Zapisywanie zawartości `<value>` elementu, ponieważ jest używany w następnych krokach.

2. Aby utworzyć konfigurację definicji zadania programu Oozie, użyj następującego polecenia:

    ```bash
    nano job.xml
    ```

3. Po otwarciu edytora nano, należy użyć następujący kod XML jako zawartość pliku:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>headnodehost:8050</value>
        </property>

        <property>
        <name>queueName</name>
        <value>default</value>
        </property>

        <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
        </property>

        <property>
        <name>hiveScript</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=adminLogin;password=adminPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>YourName</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

   * Zastąp wszystkie wystąpienia zmiennej `wasb://mycontainer@mystorageaccount.blob.core.windows.net` używając wartości odebranej wcześniej dla domyślnego magazynu.

     > [!WARNING]
     > Jeśli ścieżka jest `wasb` ścieżki, musisz podać pełną ścieżkę. Nie Skróć ją tylko `wasb:///`.

   * Zastąp `YourName` nazwą logowania dla klastra HDInsight.
   * Zastąp `serverName`, `adminLogin`, i `adminPassword` informacjami dla usługi SQL database.

     Większość informacji w tym pliku jest używany do wypełniania wartości używane w plikach workflow.xml lub ooziewf.hql, takich jak `${nameNode}`.

     > [!NOTE]
     > `oozie.wf.application.path` Definiuje ona, gdzie można znaleźć pliku workflow.xml. Ten plik zawiera przepływ pracy, który został uruchomiony przez to zadanie.

5. Aby zapisać plik, wybierz klawisze Ctrl + X, należy wprowadzić `Y`, a następnie wybierz pozycję **Enter**.

## <a name="submit-and-manage-the-job"></a>Przesyłaj i zarządzanie nim

Następujące polecenie Oozie do przesyłania i zarządzanie przepływami pracy programu Oozie w klastrze. Polecenie Oozie umieszczeniu przyjazny interfejs [interfejsu API REST programu Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]
> Korzystając z polecenia programu Oozie, należy użyć nazwy FQDN węzła głównego HDInsight. Ta nazwa FQDN jest dostępna tylko w klastrze, lub jeśli klaster znajduje się w sieci wirtualnej platformy Azure, z innych komputerów w tej samej sieci.


1. Aby uzyskać adres URL usługi Oozie, użyj następującego polecenia:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Spowoduje to zwrócenie informacji, na przykład następujący kod XML:

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` Fragment jest adres URL za pomocą polecenia programu Oozie.

2. Aby utworzyć zmienną środowiskową dla adresu URL, należy użyć następującego, więc nie trzeba wprowadzić ich dla każdego polecenia:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

    Zastąp adres URL, który otrzymałeś wcześniej.
3. Aby przesłać zadanie, użyj następującego polecenia:

    ```bash
    oozie job -config job.xml -submit
    ```

    To polecenie ładuje informacje o zadaniu z `job.xml` i przesyła je do programu Oozie, ale nie jest uruchamiany.

    Po zakończeniu działania polecenia go powinien zwrócić identyfikator zadania, na przykład `0000005-150622124850154-oozie-oozi-W`. Ten identyfikator jest używany do zarządzania zadaniem.

4. Aby wyświetlić stan zadania, użyj następującego polecenia:

    ```bash
    oozie job -info <JOBID>
    ```

    > [!NOTE]
    > Zastąp `<JOBID>` o identyfikatorze, w zwróconym w poprzednim kroku.

    Spowoduje to zwrócenie informacji, na przykład następujący tekst:

        Job ID : 0000005-150622124850154-oozie-oozi-W
        ------------------------------------------------------------------------------------------------------------------------------------
        Workflow Name : useooziewf
        App Path      : wasb:///tutorials/useoozie
        Status        : PREP
        Run           : 0
        User          : USERNAME
        Group         : -
        Created       : 2015-06-22 15:06 GMT
        Started       : -
        Last Modified : 2015-06-22 15:06 GMT
        Ended         : -
        CoordAction ID: -
        ------------------------------------------------------------------------------------------------------------------------------------

    To zadanie ma stan `PREP`. Ten stan wskazuje, czy zadanie została utworzona, ale nie jest uruchomiona.

5. Aby uruchomić zadanie, użyj następującego polecenia:

    ```bash
    oozie job -start JOBID
    ```

    > [!NOTE]
    > Zastąp `<JOBID>` o identyfikatorze zwracane wcześniej.

    Jeśli po wykonaniu tego polecenia można sprawdzić stan, jest w stanie uruchomienia, a informacje są zwracane do akcji w ramach zadania.

6. Po pomyślnym zakończeniu zadania, można sprawdzić, czy dane został wygenerowany i wyeksportowane do tabeli bazy danych SQL za pomocą następującego polecenia:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest
    ```

    W `1>` monit, wprowadź następujące zapytanie:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    Zwracane informacje dotyczą przypomina następujący tekst:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Aby uzyskać więcej informacji na temat polecenia Oozie, zobacz [narzędzie wiersza polecenia programu Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>Interfejs API REST programu Oozie

Za pomocą interfejsu API REST programu Oozie możesz tworzyć własne narzędzia, które działają przy użyciu programu Oozie. Poniżej przedstawiono HDInsight specyficzne informacje dotyczące użycia interfejsu API REST programu Oozie:

* **Identyfikator URI**: dostęp można uzyskać interfejsu API REST z poza klastrem pod adresem `https://CLUSTERNAME.azurehdinsight.net/oozie`.

* **Uwierzytelnianie**: do uwierzytelniania, użyj interfejsu API klastra HTTP konto (administratora) i hasło. Na przykład:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Aby uzyskać więcej informacji na temat korzystania z interfejsu API REST programu Oozie, zobacz [API usług sieci Web programu Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Interfejs użytkownika sieci web programu Oozie

Interfejs użytkownika sieci web programu Oozie zapewnia widok stanu zadań Oozie opartych na sieci web w klastrze. Za pomocą interfejsu użytkownika sieci web można wyświetlić następujące informacje:

   * Stan zadania
   * Definicja zadania
   * Konfigurowanie
   * Wykres działań w ramach zadania
   * Dzienniki dla zadania

Można również wyświetlić szczegółowe informacje dotyczące działań w ramach danego zadania.

Aby uzyskać dostęp do interfejsu użytkownika sieci web programu Oozie, wykonaj następujące czynności:

1. Tworzenie tunelu SSH z klastrem HDInsight. Aby uzyskać więcej informacji, zobacz [użycie tunelowania SSH z HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Po utworzeniu tunelu, Otwórz interfejs użytkownika sieci web Ambari w przeglądarce sieci web. Identyfikator URI dla witryny Ambari jest `https://CLUSTERNAME.azurehdinsight.net`. Zastąp `CLUSTERNAME` nazwą klastra usługi HDInsight opartych na systemie Linux.

3. W lewej części strony wybierz **Oozie** > **szybkich łączy** > **interfejs użytkownika sieci Web programu Oozie**.

    ![Obraz menu](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. Interfejs użytkownika sieci web programu Oozie domyślnie Wyświetl uruchomione zadania przepływu pracy. Aby wyświetlić wszystkie zadania przepływu pracy, wybierz **wszystkie zadania**.

    ![Wszystkie zadania wyświetlane](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. Aby wyświetlić więcej informacji o zadaniu, wybierz zadanie.

    ![Informacje o stanowisku](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. Z **informacji o zadaniu** karcie, można zobaczyć informacje o zadaniu podstawowe i poszczególne akcje w ramach zadania. Kart u góry służy do wyświetlania **definicji zadania**, **konfiguracji zadania**, dostępu **dziennik zadań**, lub wyświetlać skierowanym grafie acyklicznym (DAG) zadania w obszarze **Zadania DAG**.

   * **Dziennik zadań**: Wybierz **Pobierz dzienniki** przycisk, aby pobrać wszystkie dzienniki dla zadania, lub użyj **Wprowadź filtr wyszukiwania** pole ma zostać przefiltrowane dzienniki.

       ![Dziennik zadań](./media/hdinsight-use-oozie-linux-mac/joblog.png)

   * **Zadanie DAG**: DAG to graficzny przegląd ścieżki danych wykonywane przez przepływ pracy.

       ![Zadanie DAG](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. Po wybraniu jednej akcji z **informacji o zadaniu** kartę, zapewnia informacje dotyczące akcji. Na przykład wybierz **RunSqoopExport** akcji.

    ![Informacje o akcji](./media/hdinsight-use-oozie-linux-mac/action.png)

8. Aby wyświetlić szczegóły dla akcji, np. łącze do **adres URL konsoli**. Ten link umożliwia wyświetlenie informacji o śledzeniu zadania dla zadania.

## <a name="schedule-jobs"></a>Planowanie zadań

Koordynator służy do określania rozpoczęcia, zakończenia i częstotliwości występowania dla zadań. Aby zdefiniować harmonogram dla przepływu pracy, wykonaj następujące czynności:

1. Użyj następującego polecenia, aby utworzyć plik o nazwie **coordinator.xml**:

    ```bash
    nano coordinator.xml
    ```

    Użyj następujący kod XML jako zawartość pliku:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    > [!NOTE]
    > `${...}` Zmienne są zastępowane przez wartości w definicji zadania w czasie wykonywania. Dostępne są następujące zmienne:
    >
    > * `${coordFrequency}`: Czas pomiędzy uruchomione wystąpienia zadania.
    > * `${coordStart}`: Czas rozpoczęcia zadania.
    > * `${coordEnd}`Godzina zakończenia zadania.
    > * `${coordTimezone}`: Koordynator zadań znajdują się w stałej strefy czasowej nie czasu letniego, zwykle reprezentowany przez przy użyciu czasu UTC. Ta strefa czasowa jest określane jako *Oozie przetwarzania w strefie czasowej.*
    > * `${wfPath}`: Ścieżka do workflow.xml.

2. Aby zapisać plik, wybierz klawisze Ctrl + X, należy wprowadzić `Y`, a następnie wybierz pozycję **Enter**.

3. Aby skopiować plik do katalogu roboczego dla tego zadania, użyj następującego polecenia:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Aby zmodyfikować `job.xml` plików, użyj następującego polecenia:

    ```
    nano job.xml
    ```

    Wprowadź następujące zmiany:

   * Aby nakazać Oozie, aby uruchomić plik koordynator zamiast przepływu pracy, należy zmienić `<name>oozie.wf.application.path</name>` do `<name>oozie.coord.application.path</name>`.

   * Aby ustawić `workflowPath` zmiennej używanej przez koordynatora, Dodaj następujący kod XML:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Zastąp `wasb://mycontainer@mystorageaccount.blob.core.windows` tekstem wartość używana przez inne wpisy w pliku job.xml.

   * Aby zdefiniować rozpoczęcia, zakończenia i częstotliwość dla koordynatora, Dodaj następujący kod XML:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2017-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2017-05-12T12:00Z</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>1440</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>UTC</value>
        </property>
        ```

       Te wartości ustawić godzinę rozpoczęcia, do 12:00 PM 10 maja 2017 r. i czas zakończenia 12 maja 2017 r. Interwał wykonywania tego zadania jest ustawiona na codziennie. Częstotliwość jest w ciągu kilku minut, więc 24 godz. x 60 minut = 1440 minut. Na koniec strefy czasowej jest ustawiana na czas UTC.

5. Aby zapisać plik, wybierz klawisze Ctrl + X, należy wprowadzić `Y`, a następnie wybierz pozycję **Enter**.

6. Aby uruchomić zadanie, użyj następującego polecenia:

    ```
    oozie job -config job.xml -run
    ```

    To polecenie przesyła i uruchomi zadanie.

7. Jeśli przejdziesz do Oozie interfejs użytkownika sieci web, a następnie wybierz pozycję **koordynator zadań** kartę, zobacz informacje, takie jak na poniższej ilustracji:

    ![Koordynator kartę zadania](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

    **Dalej Materializacja** wpis zawiera przy następnym uruchomieniu zadania.

8. Podobnie jak wcześniej zadania przepływu pracy Jeśli wybierzesz wpisem zadań w internetowym interfejsie użytkownika Wyświetla informacje w zadaniu:

    ![Informacje o zadaniu koordynatora](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

    > [!NOTE]
    > Ten obraz zawiera tylko pomyślnych uruchomień zadania nie poszczególne akcje w ramach zaplanowanego przepływu pracy. Aby wyświetlić poszczególne akcje, wybierz jedną z **akcji** wpisów.

    ![Informacje o akcji](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Za pomocą interfejsu użytkownika programu Oozie możesz wyświetlić dzienniki programu Oozie. Interfejs użytkownika programu Oozie zawiera także łącza do dzienników JobTracker zadań MapReduce, które zostały uruchomione przez przepływ pracy. Wzorzec do rozwiązywania problemów powinny być następujące:

   1. Wyświetl zadanie w Oozie interfejsu użytkownika sieci web.

   2. Jeśli występuje błąd lub awaria określonej akcji, wybierz akcję, aby sprawdzić, czy **komunikat o błędzie** pole zawiera więcej informacji o błędzie.

   3. Jeśli to możliwe, należy użyć adresu URL z akcji, aby wyświetlić więcej szczegółów, takich jak dzienniki JobTracker w akcji.

Poniżej przedstawiono określonych błędów, które mogą wystąpić i ich rozwiązania.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: Nie można zainicjować klastra

**Objawy**: stan zadania zmieni się na **zawieszone**. Szczegóły dotyczące Pokaż zadanie `RunHiveScript` stan jako **START_MANUAL**. Wybierając akcję wyświetla następujący komunikat o błędzie:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Przyczyna**: adresy magazynu obiektów Blob platformy Azure w **job.xml** plik nie zawiera kontenera magazynu lub nazwa konta magazynu. Format adresu magazynu obiektów Blob musi być `wasb://containername@storageaccountname.blob.core.windows.net`.

**Rozpoznawanie**: zmienianie adresów magazynu obiektów Blob, które korzysta z zadania.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltuser"></a>JA002: Oozie nie może spersonifikować &lt;użytkownika >

**Objawy**: stan zadania zmieni się na **zawieszone**. Szczegóły dotyczące Pokaż zadanie `RunHiveScript` stan jako **START_MANUAL**. Wybranie akcji zawiera następujący komunikat o błędzie:

    JA002: User: oozie is not allowed to impersonate <USER>

**Przyczyna**: nie zezwalaj na bieżące ustawienia uprawnień Oozie personifikować określonego konta użytkownika.

**Rozpoznawanie**: Oozie mogą personifikować użytkowników w **użytkowników** grupy. Użyj `groups USERNAME` wyświetlić konto użytkownika jest członkiem grupy. Jeśli użytkownik nie jest członkiem **użytkowników** grupy, użyj następującego polecenia, aby dodać użytkownika do grupy:

    sudo adduser USERNAME users

> [!NOTE]
> Może upłynąć kilka minut, zanim HDInsight rozpoznaje, że użytkownik został dodany do grupy.

### <a name="launcher-error-sqoop"></a>Uruchamianie błąd (Sqoop)

**Objawy**: stan zadania zmieni się na **KILLED**. Szczegóły dotyczące Pokaż zadanie `RunSqoopExport` stan jako **błąd**. Wybranie akcji zawiera następujący komunikat o błędzie:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Przyczyna**: Sqoop nie może załadować sterownik bazy danych, wymagane do dostępu do bazy danych.

**Rozpoznawanie**: podczas korzystania z narzędzia Sqoop z zadania usługi Oozie, musi zawierać ten sterownik bazy danych z innych zasobów, takich jak workflow.xml, używa zadania. Ponadto odwoływać się do archiwum, zawierającą sterownik bazy danych z `<sqoop>...</sqoop>` sekcji workflow.xml.

Na przykład dla zadania w tym dokumencie, możesz użyć następujące czynności:

1. Kopiuj `sqljdbc4.1.jar` plik **/samouczki/useoozie** katalogu:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /tutorials/useoozie/sqljdbc41.jar
    ```

2. Modyfikowanie `workflow.xml` można dodać następujący kod XML w nowym wierszu powyżej `</sqoop>`:

    ```xml
    <archive>sqljdbc41.jar</archive>
    ```

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku pokazano, jak zdefiniować przepływ pracy programu Oozie i sposób uruchamiania zadania programu Oozie. Aby dowiedzieć się więcej na temat jak pracować z usługą HDInsight, zobacz następujące artykuły:

* [Używanie koordynatora technologii Oozie opartych na czasie za pomocą HDInsight][hdinsight-oozie-coordinator-time]
* [Przekazywanie danych na potrzeby zadań usługi Hadoop w HDInsight][hdinsight-upload-data]
* [Z usługą Hadoop w HDInsight przy użyciu narzędzia Sqoop][hdinsight-use-sqoop]
* [Korzystanie z programu Hive z usługą Hadoop w HDInsight][hdinsight-use-hive]
* [Korzystanie z języka Pig z platformą Hadoop w HDInsight][hdinsight-use-pig]
* [Opracowywanie programów MapReduce w języku Java dla HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563
[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: sql-database-create-configure.md
[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
