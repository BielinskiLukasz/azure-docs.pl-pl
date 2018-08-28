---
title: Use Hadoop Hive za pomocą programu Curl w HDInsight — Azure
description: Dowiedz się, jak zdalnie przesyłania zadań Pig na HDInsight przy użyciu programu Curl.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: jasonh
ms.openlocfilehash: d8816965fb1ab870d7bd93cd1ace45c4e6e57de6
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43040917"
---
# <a name="run-hive-queries-with-hadoop-in-hdinsight-using-rest"></a>Uruchamianie zapytań Hive z usługą Hadoop w HDInsight przy użyciu usługi REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Dowiedz się, jak za pomocą interfejsu API REST usługi WebHCat, uruchamianie zapytań Hive z usługą Hadoop w klastrze Azure HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Opartą na systemie Linux platformą Hadoop w wersji klastra HDInsight 3.4 lub nowszą.

  > [!IMPORTANT]
  > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* Klient REST. Ten dokument używa programu Windows PowerShell i [Curl](http://curl.haxx.se/) przykłady.

    > [!NOTE]
    > Program Azure PowerShell zawiera polecenia cmdlet przeznaczone do pracy z technologią Hive w HDInsight. Aby uzyskać więcej informacji, zobacz [korzystanie z programu Hive przy użyciu programu Azure PowerShell](apache-hadoop-use-hive-powershell.md) dokumentu.

W tym dokumencie używa również środowiska Windows PowerShell i [Jq](http://stedolan.github.io/jq/) do procesu zwrócone dane JSON z żądania REST.

## <a id="curl"></a>Uruchomienie zapytania programu Hive

> [!NOTE]
> Używając programu cURL lub innego połączenia REST z usługą WebHCat, należy uwierzytelnić żądania, podając nazwę użytkownika i hasło administratora klastra HDInsight.
>
> Interfejs API REST jest zabezpieczony za pomocą [uwierzytelniania podstawowego](http://en.wikipedia.org/wiki/Basic_access_authentication). Aby upewnić się, że poświadczenia są bezpiecznie wysyłane do serwera, należy zawsze tworzyć żądania przy użyciu protokołu HTTP Secure (HTTPS).

1. Aby ustawić logowania do klastra, który jest używany przez skrypty w tym dokumencie, użyj jednej z następujących poleceń:

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. Aby ustawić nazwę klastra, użyj jednej z następujących poleceń:

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. Aby sprawdzić, czy możesz nawiązać połączenie klastra usługi HDInsight, użyj jednej z następujących poleceń:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status)
    ```
    
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Pojawi się odpowiedź podobna do następującego tekstu:

    ```json
    {"status":"ok","version":"v1"}
    ```

    W tym poleceniu są używane następujące parametry:

    * `-u` Nazwa użytkownika i hasło używane do uwierzytelnienia żądania.
    * `-G` — Wskazuje, że to żądanie jest operację pobierania.

   Adres URL, na początek `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`, jest taka sama dla wszystkich żądań. Ścieżka, `/status`, wskazuje, czy żądanie jest próbę zwrócenia stanu usługi WebHCat (znanej także jako Templeton) dla serwera. Możesz również poprosić o wersji programu Hive przy użyciu następującego polecenia:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    To żądanie zwraca odpowiedź podobna do następującego tekstu:

    ```json
        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}
    ```

4. Aby utworzyć tabelę o nazwie należy użyć następujących **log4jLogs**:

    ```bash
    JOBID=`curl -s -u $LOGIN -d user.name=$LOGIN -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/hive | jq .id`
    echo $JOBID
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="set hive.execution.engine=tez;DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    To żądanie używa metody POST, który wysyła dane jako część żądania interfejsu API REST. Następujące wartości danych są wysyłane z żądaniem:

     * `user.name` Użytkownik, który uruchamia polecenie.
     * `execute` Instrukcje HiveQL do wykonania.
     * `statusdir` -Katalogu, który stan dla tego zadania jest zapisywany.

   Te instrukcje, wykonaj następujące czynności:
   
   * `DROP TABLE` -Jeśli tabela już istnieje, zostanie usunięta.
   * `CREATE EXTERNAL TABLE` -Tworzy nową tabelę "external" w gałęzi. Tabele zewnętrzne przechowywać w definicji tabeli w gałęzi. Dane pozostaną w oryginalnej lokalizacji.

     > [!NOTE]
     > Jeśli potrzebujesz danych bazowych do zaktualizowania za pomocą zewnętrznego źródła, należy używać tabel zewnętrznych. Na przykład proces przekazywania danych lub inna operacja MapReduce.
     >
     > Usunięcie tabeli zewnętrznej jest **nie** usunąć dane w definicji tabeli.

   * `ROW FORMAT` — W jaki sposób dane są sformatowane. Pola w każdym dzienniku są oddzielone spacją.
   * `STORED AS TEXTFILE LOCATION` — Gdzie dane są przechowywane (katalog przykład/danych) i które są przechowywane jako tekst.
   * `SELECT` -Wybiera liczbę wszystkich wierszy gdzie kolumna **t4** zawiera wartość **[Błąd]**. Ta instrukcja zwraca wartość **3** , jak istnieją trzy wiersze, które zawierają tę wartość.

     > [!NOTE]
     > Należy zauważyć, że spacji między instrukcje HiveQL są zastępowane przez `+` znaku w przypadku korzystania z programu Curl. Wartości w cudzysłowie, zawierające spację, takich jak ogranicznik, nie powinna zostać zastąpiona przez `+`.

      To polecenie zwraca identyfikator zadania, który może służyć do sprawdzania stanu zadania.

5. Aby sprawdzić stan zadania, użyj następującego polecenia:

    ```bash
    curl -G -u $LOGIN -d user.name=$LOGIN https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/$JOBID | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    Jeśli zadanie zostało zakończone, stan jest **Powodzenie**.

6. Gdy stan zadania został zmieniony na **Powodzenie**, możesz pobrać wyniki zadania z usługi Azure Blob storage. `statusdir` Parametr przekazany z zapytaniem zawiera lokalizację pliku wyjściowego; w tym przypadku `/example/rest`. Ten adres są przechowywane dane wyjściowe w `example/curl` katalogu w klastrach domyślny magazyn.

    Można wyświetlić listę i pobierać te pliki przy użyciu [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Aby uzyskać więcej informacji na temat korzystania z wiersza polecenia platformy Azure z usługą Azure Storage, zobacz [użycia Azure CLI 2.0 z usługą Azure Storage](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs) dokumentu.

## <a id="nextsteps"></a>Następne kroki

Aby uzyskać ogólne informacje na temat programu Hive z HDInsight:

* [Korzystanie z programu Hive z usługą Hadoop w HDInsight](hdinsight-use-hive.md)

Więcej informacji dotyczących innych sposobów korzystania z usługi Hadoop w HDInsight:

* [Korzystanie z języka Pig z platformą Hadoop w HDInsight](hdinsight-use-pig.md)
* [Korzystanie z technologii MapReduce z platformą Hadoop w HDInsight](hdinsight-use-mapreduce.md)

Jeśli używasz aplikacji Tez przy użyciu technologii Hive, zobacz następujące dokumenty, aby uzyskać informacje o debugowaniu:

* [Użyj widoku Ambari Tez w HDInsight opartych na systemie Linux](../hdinsight-debug-ambari-tez-view.md)

Aby uzyskać więcej informacji na temat interfejsu API REST używanego w tym dokumencie, zobacz [odwołanie do usługi WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) dokumentu.

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


