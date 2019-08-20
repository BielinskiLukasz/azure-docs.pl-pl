---
title: Rozwiązywanie problemów z programem Hive przy użyciu usługi Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące pracy z usługami Apache Hive i Azure HDInsight.
keywords: Azure HDInsight, Hive, często zadawane pytania, Przewodnik rozwiązywania problemów, często zadawane pytania
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: ca1e3e11ad5458e8e7f7072b7d3dd561853029fe
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575693"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z Apache Hive przy użyciu usługi Azure HDInsight

Zapoznaj się z najważniejszymi pytaniami i ich rozwiązaniami podczas pracy z Apache Hive ładunku w programie Apache Ambari.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Jak mogę wyeksportować magazyn metadanych Hive i zaimportować go do innego klastra?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Nawiąż połączenie z klastrem HDInsight przy użyciu klienta Secure Shell (SSH). Aby uzyskać więcej informacji, zobacz [dodatkowe materiały](#additional-reading-end).

2. Uruchom następujące polecenie w klastrze usługi HDInsight, z którego chcesz wyeksportować magazyn metadanych:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   To polecenie generuje plik o nazwie allatables. SQL.

3. Skopiuj plik alltables. SQL do nowego klastra usługi HDInsight, a następnie uruchom następujące polecenie:

    ```apache
    hive -f alltables.sql
    ```

Kod w krokach rozwiązania założono, że ścieżki danych w nowym klastrze są takie same jak ścieżki danych w starym klastrze. Jeśli ścieżki danych są różne, można ręcznie edytować wygenerowany `alltables.sql` plik w celu odzwierciedlenia zmian.

### <a name="additional-reading"></a>Materiały uzupełniające

- [Nawiązywanie połączenia z klastrem usługi HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Jak mogę zlokalizować dzienników Hive w klastrze?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Połącz się z klastrem usługi HDInsight przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz **dodatkowe materiały**.

2. Aby wyświetlić dzienniki klienta programu Hive, użyj następującego polecenia:

   ```apache
   /tmp/<username>/hive.log
   ```

3. Aby wyświetlić dzienniki magazyn metadanych Hive, użyj następującego polecenia:

   ```apache
   /var/log/hive/hivemetastore.log
   ```

4. Aby wyświetlić dzienniki serwera Hive, użyj następującego polecenia:

   ```apache
   /var/log/hive/hiveserver2.log
   ```

### <a name="additional-reading"></a>Materiały uzupełniające

- [Nawiązywanie połączenia z klastrem usługi HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Jak mogę uruchomić powłokę programu Hive z określonymi konfiguracjami w klastrze?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Określ parę klucz-wartość konfiguracji podczas uruchamiania powłoki programu Hive. Aby uzyskać więcej informacji, zobacz [dodatkowe materiały](#additional-reading-end).

   ```apache
   hive -hiveconf a=b
   ```

2. Aby wyświetlić listę wszystkich efektywnych konfiguracji w usłudze Hive Shell, użyj następującego polecenia:

   ```apache
   hive> set;
   ```

   Na przykład użyj następującego polecenia, aby uruchomić powłokę programu Hive z włączonym rejestrowaniem debugowania w konsoli programu:

   ```apache
   hive -hiveconf hive.root.logger=ALL,console
   ```

### <a name="additional-reading"></a>Materiały uzupełniające

- [Właściwości konfiguracji programu Hive](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)

## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Jak mogę analizować Apache Tez DAG danych w ścieżce krytycznej dla klastra?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Aby analizować Apache Tez ukierunkowany wykres (DAG) na wykresie krytycznym dla klastra, Połącz się z klastrem usługi HDInsight przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz [dodatkowe materiały](#additional-reading-end).

2. W wierszu polecenia uruchom następujące polecenie:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. Aby wyświetlić listę innych analizatorów, których można użyć do analizowania tez DAG, użyj następującego polecenia:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   Musisz podać Przykładowy program jako pierwszy argument.

   Prawidłowe nazwy programów to:
    - **ContainerReuseAnalyzer**: Szczegóły ponownego użycia kontenera drukowania w DAG
    - **CriticalPath**: Znajdowanie ścieżki krytycznej DAG
    - **LocalityAnalyzer**: Szczegóły lokalnego drukowania w DAG
    - **ShuffleTimeAnalyzer**: Analizuj szczegóły czasu losowego w DAG
    - **SkewAnalyzer**: Analizowanie szczegółów pochylenia w DAG
    - **SlowNodeAnalyzer**: Drukowanie szczegółów węzła w DAG
    - **SlowTaskIdentifier**: Drukuj wolne szczegóły zadania w DAG
    - **SlowestVertexAnalyzer**: Drukuj najwolniejsze szczegóły wierzchołka w DAG
    - **SpillAnalyzer**: Drukuj szczegóły rozlania w DAG
    - **TaskConcurrencyAnalyzer**: Drukuj szczegóły współbieżności zadania w DAG
    - **VertexLevelCriticalPathAnalyzer**: Znajdowanie ścieżki krytycznej na poziomie wierzchołka w DAG

### <a name="additional-reading"></a>Materiały uzupełniające

- [Nawiązywanie połączenia z klastrem usługi HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Jak mogę pobrać danych tez DAG z klastra?

#### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

Istnieją dwa sposoby zbierania danych tez DAG:

- W wierszu polecenia:

    Połącz się z klastrem usługi HDInsight przy użyciu protokołu SSH. W wierszu polecenia Uruchom następujące polecenie:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId>
  ```

- Użyj widoku Ambari tez:

  1. Przejdź do Ambari.
  2. Przejdź do widoku tez (w obszarze ikony kafelków w prawym górnym rogu).
  3. Wybierz DAG, które chcesz wyświetlić.
  4. Wybierz pozycję **Pobierz dane**.

### <a name="additional-reading-end"></a>Materiały uzupełniające

[Nawiązywanie połączenia z klastrem usługi HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

- Połącz się [@AzureSupport](https://twitter.com/azuresupport) z programem — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

- Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
