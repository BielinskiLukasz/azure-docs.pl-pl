---
title: Rozwiązywanie problemów z systemem plików HDFS w usłudze Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące pracy z systemem plików HDFS i usługą Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: 6de9e31c3e79f6d704ef8b4749d41329dcc0bddb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82190688"
---
# <a name="troubleshoot-apache-hadoop-hdfs-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z systemem HDFS usługi Apache Hadoop za pomocą usługi Azure HDInsight

Poznaj najważniejsze problemy i rozwiązania podczas pracy z usługą Hadoop rozproszony system plików (HDFS). Aby zapoznać się z pełną listą poleceń, zobacz [Przewodnik po poleceniach](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html) [systemu plików HDFS i podręczniku](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)dotyczącego powłoki.

## <a name="how-do-i-access-the-local-hdfs-from-inside-a-cluster"></a><a name="how-do-i-access-local-hdfs-from-inside-a-cluster"></a>Jak mogę uzyskać dostęp do lokalnego systemu plików HDFS z klastra?

### <a name="issue"></a>Problem

Dostęp do lokalnego systemu plików HDFS z poziomu wiersza polecenia i kodu aplikacji zamiast programu przy użyciu magazynu obiektów blob platformy Azure lub Azure Data Lake Storage z klastra usługi HDInsight.

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. W wierszu polecenia Użyj `hdfs dfs -D "fs.default.name=hdfs://mycluster/" ...` dosłownie, jak w poniższym poleceniu:

    ```output
    hdfs dfs -D "fs.default.name=hdfs://mycluster/" -ls /
    Found 3 items
    drwxr-xr-x   - hdiuser hdfs          0 2017-03-24 14:12 /EventCheckpoint-30-8-24-11102016-01
    drwx-wx-wx   - hive    hdfs          0 2016-11-10 18:42 /tmp
    drwx------   - hdiuser hdfs          0 2016-11-10 22:22 /user
    ```

2. Z kodu źródłowego Użyj identyfikatora URI `hdfs://mycluster/` dosłownie, tak jak w przypadku następującej przykładowej aplikacji:

    ```Java
    import java.io.IOException;
    import java.net.URI;
    import org.apache.commons.io.IOUtils;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.*;

    public class JavaUnitTests {

        public static void main(String[] args) throws Exception {

            Configuration conf = new Configuration();
            String hdfsUri = "hdfs://mycluster/";
            conf.set("fs.defaultFS", hdfsUri);
            FileSystem fileSystem = FileSystem.get(URI.create(hdfsUri), conf);
            RemoteIterator<LocatedFileStatus> fileStatusIterator = fileSystem.listFiles(new Path("/tmp"), true);
            while(fileStatusIterator.hasNext()) {
                System.out.println(fileStatusIterator.next().getPath().toString());
            }
        }
    }
    ```

3. Uruchom skompilowany plik JAR (na przykład plik o nazwie `java-unit-tests-1.0.jar`) w klastrze usługi HDInsight przy użyciu następującego polecenia:

    ```apache
    hadoop jar java-unit-tests-1.0.jar JavaUnitTests
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.info
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.lck
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.info
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.lck
    ```

## <a name="storage-exception-for-write-on-blob"></a>Wyjątek magazynu do zapisu na obiekcie blob

### <a name="issue"></a>Problem

W przypadku używania `hadoop` poleceń `hdfs dfs` lub do zapisywania plików, które są ~ 12 GB lub większe w klastrze HBase, może wystąpić następujący błąd:

```error
ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
copyFromLocal: java.io.IOException
        at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
        at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
        at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
        at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
        at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
        at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
        ... 7 more
```

### <a name="cause"></a>Przyczyna

HBase w klastrach usługi HDInsight domyślnie ma rozmiar bloku 256 KB podczas zapisywania w usłudze Azure Storage. Chociaż działa w przypadku interfejsów API HBase lub interfejsów API REST, powoduje to błąd podczas korzystania z `hadoop` narzędzi `hdfs dfs` wiersza polecenia lub.

### <a name="resolution"></a>Rozwiązanie

Użyj `fs.azure.write.request.size` , aby określić większy rozmiar bloku. Tę modyfikację można wykonać przy użyciu `-D` parametru. Następujące polecenie jest przykładem przy użyciu tego parametru z `hadoop` poleceniem:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Możesz również zwiększyć wartość `fs.azure.write.request.size` globalnie za pomocą platformy Apache Ambari. Poniższe kroki umożliwiają zmianę wartości w interfejsie użytkownika sieci Web Ambari:

1. W przeglądarce przejdź do interfejsu użytkownika sieci Web Ambari dla klastra. Adres URL to `https://CLUSTERNAME.azurehdinsight.net`, gdzie `CLUSTERNAME` jest nazwą klastra. Po wyświetleniu monitu wprowadź nazwę i hasło administratora klastra.
2. Z lewej strony ekranu wybierz pozycję **HDFS**, a następnie **Wybierz kartę konfiguracje** .
3. W polu **Filtr...** wprowadź `fs.azure.write.request.size`wartość.
4. Zmień wartość z 262144 (256 KB) na nową wartość. Na przykład 4194304 (4 MB).

    ![Obraz zmiany wartości za pomocą interfejsu użytkownika sieci Web Ambari](./media/hdinsight-troubleshoot-hdfs/hbase-change-block-write-size.png)

Aby uzyskać więcej informacji na temat korzystania z programu Ambari, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="du"></a>du

[`-du`](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#du) Polecenie wyświetla rozmiary plików i katalogów znajdujących się w danym katalogu lub długość pliku w przypadku, gdy jest tylko plikiem.

`-s` Opcja generuje zagregowane podsumowanie długości plików, które są wyświetlane.  
`-h` Opcja formatuje rozmiary plików.

Przykład:

```bash
hdfs dfs -du -s -h hdfs://mycluster/
hdfs dfs -du -s -h hdfs://mycluster/tmp
```

## <a name="rm"></a>RM

Polecenie [-RM](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#rm) usuwa pliki określone jako argumenty.

Przykład:

```bash
hdfs dfs -rm hdfs://mycluster/tmp/testfile
```

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) z programem — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
