---
title: Skalowanie rozmiarów klastrów — Azure HDInsight
description: Skalowanie klastra Apache Hadoop elastycznie w celu dopasowania do obciążenia w usłudze Azure HDInsight
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: dad796c8a7a34a782a4f78260ac38bd966eddde9
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105393"
---
# <a name="scale-azure-hdinsight-clusters"></a>Skalowanie klastrów usługi Azure HDInsight

Usługa HDInsight zapewnia elastyczność, zapewniając możliwość skalowania w górę i w dół liczby węzłów procesu roboczego w klastrach. Elastyczność pozwala zmniejszyć klaster po godzinach lub w weekendach i rozwijać go podczas szczytowego zapotrzebowania na działalność biznesową.

W przypadku okresowego przetwarzania wsadowego klaster usługi HDInsight można skalować w górę w ciągu kilku minut przed tą operacją, aby klaster miał odpowiednią pamięć i moc procesora CPU.  Później po zakończeniu przetwarzania i ponownym przejściu w dół można skalować klaster usługi HDInsight do mniejszej liczby węzłów procesu roboczego.

Klaster można skalować ręcznie przy użyciu jednej z metod opisanych poniżej lub użyć opcji automatycznego [skalowania](hdinsight-autoscale-clusters.md) , aby system automatycznie skalować w górę i w dół w odpowiedzi na procesor, pamięć i inne metryki.

> [!NOTE]  
> Obsługiwane są tylko klastry z usługą HDInsight w wersji 3.1.3 lub nowszej. Jeśli nie masz pewności, jaka jest wersja klastra, możesz sprawdzić stronę właściwości.

## <a name="utilities-to-scale-clusters"></a>Narzędzia do skalowania klastrów

Firma Microsoft udostępnia następujące narzędzia do skalowania klastrów:

|Spuninst | Opis|
|---|---|
|[PowerShell AZ](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -ClusterName \<Nazwa klastra >-TargetInstanceCount \<NewSize >|
|[AzureRM programu PowerShell](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -ClusterName \<Nazwa klastra >-TargetInstanceCount \<NewSize >|
|[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [AZ HDInsight Zmień rozmiar](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --Resource- \<Group Resource Group >-- \<Name Nazwa klastra >--Target-instance- \<Count NewSize >|
|[Interfejs wiersza polecenia platformy Azure](hdinsight-administer-use-command-line.md)|klaster usługi Azure HDInsight \<zmiana rozmiaru klastraname > \<liczba wystąpień docelowych > |
|[Azure Portal](https://portal.azure.com)|Otwórz okienko klastra usługi HDInsight, wybierz pozycję **rozmiar klastra** w menu po lewej stronie, a następnie w okienku rozmiar klastra wpisz liczbę węzłów procesu roboczego i wybierz pozycję Zapisz.|  

![Azure Portal skalowanie — opcja klastra](./media/hdinsight-scaling-best-practices/scale-cluster-blade1.png)

Korzystając z dowolnej z tych metod, można skalować klaster usługi HDInsight w górę lub w dół w ciągu kilku minut.

> [!IMPORTANT]  
> * Klasyczny interfejs wiersza polecenia Aure jest przestarzały i powinien być używany tylko z klasycznym modelem wdrażania. W przypadku wszystkich innych wdrożeń Użyj [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).  
> * Moduł AzureRM programu PowerShell jest przestarzały.  Użyj [AZ module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) wszędzie tam, gdzie to możliwe.

## <a name="impact-of-scaling-operations"></a>Wpływ operacji skalowania

Po **dodaniu** węzłów do uruchomionego klastra usługi HDInsight (skalowanie w górę) wszystkie oczekujące lub uruchomione zadania nie będą miały żadnego oddziaływania. Nowe zadania mogą być bezpiecznie przesłane, gdy proces skalowania jest uruchomiony. Jeśli operacja skalowania nie powiedzie się z jakiegokolwiek powodu, błąd zostanie obsłużony, aby opuścić klaster w stanie funkcjonalnym.

Po **usunięciu** węzłów (skalowanie w dół) wszystkie oczekujące lub uruchomione zadania zakończą się niepowodzeniem po zakończeniu operacji skalowania. Ten błąd jest spowodowany tym, że niektóre usługi są ponownie uruchamiane podczas skalowania. Istnieje również ryzyko, że klaster może zostać zablokowany w trybie awaryjnym podczas operacji skalowania ręcznego.

Wpływ zmiany liczby węzłów danych różni się w zależności od typu klastra obsługiwanego przez usługi HDInsight:

* Apache Hadoop

    Można bezproblemowo zwiększyć liczbę węzłów procesu roboczego w klastrze Hadoop uruchomionym bez wpływu na zadania oczekujące lub uruchomione. Nowe zadania mogą być również przesyłane, gdy operacja jest w toku. Błędy w operacji skalowania są bezpiecznie obsługiwane, aby klaster zawsze pozostawał w stanie funkcjonalności.

    Po skalowaniu klastra Hadoop przez zmniejszenie liczby węzłów danych, niektóre usługi w klastrze są ponownie uruchamiane. To zachowanie powoduje, że wszystkie uruchomione i oczekujące zadania kończą się niepowodzeniem po zakończeniu operacji skalowania. Można jednak ponownie przesłać zadania po zakończeniu operacji.

* Apache HBase

    Można bezproblemowo dodawać lub usuwać węzły do klastra HBase, gdy jest on uruchomiony. Serwery regionalne są automatycznie równoważone w ciągu kilku minut od zakończenia operacji skalowania. Można również ręcznie zrównoważyć serwery regionalne, logując się do węzła głównego klastra i uruchamiając następujące polecenia z poziomu okna wiersza polecenia:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Aby uzyskać więcej informacji na temat używania powłoki HBase, zobacz Rozpoczynanie [pracy z przykładem Apache HBase w usłudze HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Można bezproblemowo dodawać lub usuwać węzły danych do klastra burzy, gdy jest on uruchomiony. Jednak po pomyślnym zakończeniu operacji skalowania trzeba będzie ponownie zrównoważyć topologię.

    Ponowne równoważenie można wykonać na dwa sposoby:

  * Interfejs użytkownika sieci Web burzy
  * Narzędzie interfejsu wiersza polecenia (CLI)

    Więcej informacji można znaleźć w [dokumentacji Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) .

    Interfejs użytkownika sieci Web burzy jest dostępny w klastrze usługi HDInsight:

    ![Ponowne równoważenie skali burzy usługi HDInsight](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Oto przykładowe polecenie interfejsu wiersza polecenia do ponownego zrównoważenia topologii burzy:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>Jak bezpiecznie skalować klaster

### <a name="scale-down-a-cluster-with-running-jobs"></a>Skalowanie w dół klastra z uruchomionymi zadaniami

Aby uniknąć niepowodzenia wykonywanych zadań podczas operacji skalowania w dół, możesz wypróbować trzy rzeczy:

1. Poczekaj na zakończenie zadań przed skalowaniem klastra w dół.
1. Ręcznie Zakończ zadania.
1. Prześlij ponownie zadania po zakończeniu operacji skalowania.

Aby wyświetlić listę oczekujących i uruchomionych zadań, można użyć **interfejsu użytkownika Menedżer zasobów**przędzy, wykonując następujące czynności:

1. Na [Azure Portal](https://portal.azure.com/)wybierz swój klaster.  Aby uzyskać instrukcje, zobacz [listę i wyświetlanie klastrów](./hdinsight-administer-use-portal-linux.md#showClusters) . Klaster zostanie otwarty na nowej stronie portalu.
2. W widoku głównym przejdź do strony **pulpity nawigacyjne** > klastra**Ambari Home**. Wprowadź poświadczenia klastra.
3. Z poziomu interfejsu użytkownika Ambari wybierz pozycję **przędza** na liście usług w menu po lewej stronie.  
4. Na stronie PRZĘDZa wybierz pozycję **szybkie linki** i umieść kursor nad aktywnym węzłem głównym, a następnie wybierz pozycję **interfejs użytkownika usługi ResourceManager**.

    ![Interfejs użytkownika oprogramowania Apache Ambari Quick Links](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

Dostęp do interfejsu użytkownika programu ResourceManager można uzyskać `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`bezpośrednio za pomocą narzędzia.

Zostanie wyświetlona lista zadań wraz z bieżącym stanem. Na zrzucie ekranu jest aktualnie uruchomione jedno zadanie:

![ResourceManager — aplikacje interfejsu użytkownika](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Aby ręcznie skasować tę uruchomioną aplikację, wykonaj następujące polecenie w powłoce SSH:

```bash
yarn application -kill <application_id>
```

Na przykład:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Uruchamianie w trybie awaryjnym

Podczas skalowania w dół klastra Usługa HDInsight używa interfejsów zarządzania Apache Ambari do pierwszej likwidacji dodatkowych węzłów procesu roboczego, które replikują bloki systemu plików HDFS do innych węzłów procesu roboczego online. Następnie Usługa HDInsight bezpiecznie skaluje klaster. System plików HDFS przechodzi do trybu awaryjnego podczas operacji skalowania i powinien zostać wyprowadzony po zakończeniu skalowania. W niektórych przypadkach system plików HDFS jest blokowany w trybie awaryjnym podczas operacji skalowania ze względu na to, że jest on w trakcie replikacji.

Domyślnie system plików HDFS jest skonfigurowany z `dfs.replication` ustawieniem 3, które określa, ile kopii poszczególnych bloków plików jest dostępnych. Każda kopia bloku plików jest przechowywana w innym węźle klastra.

Gdy system plików HDFS wykryje, że oczekiwana liczba kopii bloku jest niedostępna, w systemie HDFS wchodzi tryb awaryjny, a Ambari generuje alerty. Jeśli system plików HDFS przechodzi do trybu awaryjnego dla operacji skalowania, ale nie można wyjść z trybu awaryjnego, ponieważ wymagana liczba węzłów nie zostanie wykryta na potrzeby replikacji, klaster może zostać zablokowany w trybie awaryjnym.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Przykładowe błędy w przypadku włączenia trybu awaryjnego

```
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```
org.apache.http.conn.HttpHostConnectException: Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Można przejrzeć nazwy dzienników węzłów z `/var/log/hadoop/hdfs/` folderu, w czasie, gdy klaster został przeskalowany, aby zobaczyć, kiedy przeszedł tryb awaryjny. Pliki dziennika mają nazwę `Hadoop-hdfs-namenode-hn0-clustername.*`.

Główną przyczyną wcześniejszych błędów jest to, że usługa Hive jest zależna od plików tymczasowych w systemie plików HDFS podczas wykonywania zapytań. Gdy system plików HDFS przechodzi w tryb awaryjny, gałąź nie może uruchamiać zapytań, ponieważ nie może zapisywać w systemie plików HDFS. Pliki tymczasowe w systemie plików HDFS znajdują się na dysku lokalnym zainstalowanym na maszynach wirtualnych węzłów procesu roboczego i replikowane między innymi węzłami procesu roboczego w trzech replikach, co najmniej.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Jak uniemożliwić blokowanie usługi HDInsight w trybie awaryjnym

Istnieje kilka sposobów, aby uniemożliwić pozostawienie usługi HDInsight w trybie awaryjnym:

* Zatrzymaj wszystkie zadania Hive przed skalowaniem w dół usługi HDInsight. Alternatywnie Zaplanuj proces skalowania w dół, aby uniknąć konfliktu z uruchomionymi zadaniami programu Hive.
* Ręcznie Wyczyść pliki katalogów magazynu `tmp` Hive w systemie plików HDFS przed skalowaniem w dół.
* Maksymalnie Skaluj w dół usługi HDInsight do trzech węzłów procesów roboczych. Należy unikać przechodzenia do jednego węzła procesu roboczego.
* Uruchom polecenie, aby wyjść z trybu awaryjnego, w razie potrzeby.

W poniższych sekcjach opisano te opcje.

#### <a name="stop-all-hive-jobs"></a>Zatrzymaj wszystkie zadania Hive

Zatrzymaj wszystkie zadania Hive przed skalowaniem w dół do jednego węzła procesu roboczego. Jeśli obciążenie jest zaplanowane, po wykonaniu operacji Hive wykonaj skalowanie w dół.

Zatrzymywanie zadań programu Hive przed skalowaniem umożliwia zminimalizowanie liczby plików w folderze tmp (jeśli istnieje).

#### <a name="manually-clean-up-hives-scratch-files"></a>Ręczne czyszczenie plików tymczasowych Hive

Jeśli gałąź została pozostawiona za pliki tymczasowe, można ręcznie oczyścić te pliki przed skalowaniem w dół, aby uniknąć trybu awaryjnego.

1. Sprawdź, która lokalizacja jest używana dla plików tymczasowych programu Hive, przeglądając `hive.exec.scratchdir` właściwość konfiguracji. Ten parametr jest ustawiany w `/etc/hive/conf/hive-site.xml`:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Zatrzymaj usługi Hive i upewnij się, że wszystkie zapytania i zadania zostały ukończone.
2. Wystaw zawartość katalogu tymczasowego znalezionego powyżej, aby zobaczyć `hdfs://mycluster/tmp/hive/` , czy zawiera on pliki:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Oto przykładowe dane wyjściowe, gdy istnieją pliki:

    ```output
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Jeśli wiesz, że gałąź jest wykonywana z tymi plikami, możesz je usunąć. Upewnij się, że w ramach programu Hive nie są uruchomione żadne zapytania, szukając na stronie interfejsu użytkownika ResourceManager.

    Przykładowy wiersz polecenia usuwania plików z systemu plików HDFS:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Skalowanie usługi HDInsight do trzech lub większej liczby węzłów procesu roboczego

Jeśli klastry są wykonywane w trybie awaryjnym często podczas skalowania w dół do mniej niż trzech węzłów procesów roboczych, a poprzednie kroki nie działają, można uniknąć, aby klaster przeprowadził się do trybu awaryjnego, zachowując co najmniej trzy węzły procesu roboczego.

Utrzymywanie trzech węzłów procesu roboczego jest droższe niż skalowanie w dół tylko do jednego węzła procesu roboczego, ale uniemożliwi to zablokowanie klastra w trybie awaryjnym.

#### <a name="run-the-command-to-leave-safe-mode"></a>Uruchom polecenie, aby wyjść z trybu awaryjnego

Końcową opcją jest wykonanie polecenia Opuść tryb bezpieczny. Jeśli wiesz, że powód przechodzenia do trybu awaryjnego jest spowodowany przez plik Hive w replikacji, możesz wykonać następujące polecenie, aby wyjść z trybu awaryjnego:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Skalowanie klastra Apache HBase

Serwery regionów są automatycznie równoważone w ciągu kilku minut od zakończenia operacji skalowania. Aby ręcznie zrównoważyć serwery regionów, wykonaj następujące czynności:

1. Połącz się z klastrem usługi HDInsight przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Uruchom powłokę HBase:

    ```bash
    hbase shell
    ```

3. Użyj następującego polecenia, aby ręcznie zrównoważyć serwery regionów:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Następne kroki

* [Automatyczne skalowanie klastrów usługi Azure HDInsight](hdinsight-autoscale-clusters.md)
* [Wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-introduction.md)
