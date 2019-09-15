---
title: Używanie Apache Spark do analizowania danych w Azure Data Lake Storage Gen1
description: Uruchamianie Apache Spark zadań w celu analizowania danych przechowywanych w Azure Data Lake Storage Gen1
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: b8e830cb187b375e17b3dc33c582126adfa32f3e
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002490"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-storage-gen1"></a>Analizowanie danych w Data Lake Storage Gen1 przy użyciu klastra usługi HDInsight Spark

W tym artykule opisano [Jupyter Notebook](https://jupyter.org/) dostępne z klastrami usługi HDInsight Spark do uruchamiania zadania, które odczytuje dane z konta Data Lake Storage.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto Azure Data Lake Storage Gen1. Postępuj zgodnie z instrukcjami w punkcie wprowadzenie [do Azure Data Lake Storage Gen1 przy użyciu Azure Portal](../../data-lake-store/data-lake-store-get-started-portal.md).

* Azure HDInsight Spark klaster z Data Lake Storage Gen1 jako magazyn. Postępuj zgodnie z instrukcjami w artykule [Szybki start: Konfigurowanie klastrów w usłudze HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

## <a name="prepare-the-data"></a>Przygotowywanie danych

> [!NOTE]  
> Nie trzeba wykonywać tego kroku, jeśli utworzono klaster usługi HDInsight z Data Lake Storage jako magazyn domyślny. Proces tworzenia klastra dodaje przykładowe dane na koncie Data Lake Storage określonym podczas tworzenia klastra. Przejdź do sekcji Korzystanie z klastra usługi HDInsight Spark z Data Lake Storage.

Jeśli klaster usługi HDInsight został utworzony przy użyciu Data Lake Storage jako dodatkowego magazynu i Azure Storage Blob jako magazyn domyślny, należy najpierw skopiować kilka przykładowych danych do konta Data Lake Storage. Możesz użyć przykładowych danych z Azure Storage Blob skojarzonych z klastrem usługi HDInsight. Aby to zrobić, możesz użyć [Narzędzia ADLCopy](https://aka.ms/downloadadlcopy) . Pobierz i zainstaluj narzędzie z linku.

1. Otwórz wiersz polecenia i przejdź do katalogu, w którym zainstalowano AdlCopy, `%HOMEPATH%\Documents\adlcopy`zwykle.

2. Uruchom następujące polecenie, aby skopiować konkretny obiekt BLOB z kontenera źródłowego do Data Lake Storage:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Skopiuj przykładowy plik danych **HVAC. csv** z **/HdiSamples/HdiSamples/SensorSampleData/hvac/** na konto Azure Data Lake Storage. Fragment kodu powinien wyglądać następująco:

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]  
   > Upewnij się, że nazwy pliku i ścieżki używają odpowiedniej wielkości liter.

3. Zostanie wyświetlony monit o wprowadzenie poświadczeń dla subskrypcji platformy Azure, w ramach której masz konto Data Lake Storage. Zostaną wyświetlone dane wyjściowe podobne do następującego fragmentu kodu:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    Plik danych (**HVAC. csv**) zostanie skopiowany do folderu **/hvac** na koncie Data Lake Storage.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-storage-gen1"></a>Korzystanie z klastra usługi HDInsight Spark z Data Lake Storage Gen1

1. W [Azure Portal](https://portal.azure.com/), w tablicy startowej, kliknij kafelek klastra Apache Spark (jeśli przypięto go do tablicy startowej). Możesz także przejść do klastra, wybierając polecenia **Przeglądaj wszystko** > **Klastry usługi HDInsight**.

2. W bloku klastra Spark kliknij pozycję **Szybkie linki**, a następnie w bloku **Pulpit nawigacyjny klastra** kliknij pozycję **Jupyter Notebook**. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia administratora klastra.

   > [!NOTE]  
   > Można również przejść do aplikacji Jupyter Notebook dla klastra, otwierając następujący adres URL w przeglądarce. Zastąp ciąg **CLUSTERNAME** nazwą klastra:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Utwórz nowy notes. Kliknij opcję **New** (Nowy), a następnie kliknij pozycję **PySpark**.

    ![Tworzenie nowego notesu Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "Tworzenie nowego notesu Jupyter")

4. Ponieważ notes został utworzony z użyciem jądra PySpark, nie ma konieczności jawnego tworzenia kontekstów. Konteksty Spark i Hive zostaną automatycznie utworzone po uruchomieniu pierwszej komórki kodu. Możesz zacząć od importowania typów wymaganych w tym scenariuszu. W tym celu wklej poniższy fragment kodu w komórce i naciśnij klawisze **SHIFT + ENTER**.

        from pyspark.sql.types import *

    Przy każdym uruchomieniu zadania w oprogramowaniu Jupyter w tytule okna przeglądarki sieci Web będzie wyświetlony stan **(Busy)** (Zajęty) wraz z tytułem notesu. Widoczne będzie także pełne kółko obok tekstu **PySpark** w prawym górnym rogu. Po zakończeniu zadania zmieni się ono w pusty okrąg.

     ![Stan zadania notesu Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "Stan zadania notesu Jupyter")

5. Załaduj dane przykładowe do tabeli tymczasowej przy użyciu pliku **HVAC. csv** skopiowanego do konta Data Lake Storage Gen1. Dostęp do danych na koncie Data Lake Storage można uzyskać przy użyciu następującego wzorca adresu URL.

   * Jeśli masz Data Lake Storage Gen1 jako magazyn domyślny, plik HVAC. CSV będzie wyglądać pod ścieżką podobną do następującego adresu URL:

           adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

       Można również użyć skróconego formatu, takiego jak:

           adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

   * Jeśli masz Data Lake Storage jako dodatkowy magazyn, plik HVAC. CSV będzie znajdować się w lokalizacji, do której został skopiowany, na przykład:

           adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     W pustej komórce wklej następujący przykład kodu, Zastąp **MYDATALAKESTORE** nazwą konta Data Lake Storage i naciśnij klawisze **SHIFT + ENTER**. Ten przykład kodu rejestruje dane w tabeli tymczasowej o nazwie **hvac**.

           # Load the data. The path below assumes Data Lake Storage is default storage for the Spark cluster
           hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/cluster/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

           # Create the schema
           hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

           # Parse the data in hvacText
           hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

           # Create a data frame
           hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

           # Register the data fram as a table to run queries against
           hvacdf.registerTempTable("hvac")

6. Ponieważ używane jest jądro PySpark, można teraz bezpośrednio uruchomić zapytanie SQL w tabeli tymczasowej **hvac** utworzonej przed chwilą za pomocą polecenia magicznego `%%sql`. Aby uzyskać więcej informacji na `%%sql` temat Magic, a także innych magicznych Magic dostępnych w jądrze PySpark, zobacz [jądra dostępne w notesach Jupyter z klastrami usługi HDInsight Apache Spark](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. Po pomyślnym ukończeniu zadania domyślnie wyświetlane są następujące tabelaryczne dane wyjściowe.

      ![Wyniki zapytania w tabeli danych wyjściowych](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "Wyniki zapytania w tabeli danych wyjściowych")

     Wyniki można również przeglądać w postaci innych wizualizacji. Na przykład wykres warstwowy tych samych danych wyjściowych będzie wyglądać w następujący sposób.

     ![Wykres warstwowy wyników zapytania](./media/apache-spark-use-with-data-lake-store/jupyter-area-output1.png "Wykres warstwowy wyników zapytania")

8. Po zakończeniu działania aplikacji należy ją zamknąć, aby zwolnić zasoby. W tym celu w menu **File** (Plik) w notesie kliknij polecenie **Close and Halt** (Zamknij i zatrzymaj). Spowoduje to zakończenie pracy i zamknięcie notesu.


## <a name="next-steps"></a>Następne kroki

* [Tworzenie autonomicznej aplikacji Scala do uruchamiania w klastrze Apache Spark](apache-spark-create-standalone-application.md)
* [Korzystanie z narzędzi usługi HDInsight w Azure Toolkit for IntelliJ do tworzenia aplikacji Apache Spark dla klastra usługi HDInsight Spark Linux](apache-spark-intellij-tool-plugin.md)
* [Korzystanie z narzędzi usługi HDInsight w Azure Toolkit for Eclipse do tworzenia aplikacji Apache Spark dla klastra usługi HDInsight Spark Linux](apache-spark-eclipse-tool-plugin.md)
* [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
