---
title: Połączenie MapReduce i SSH z usługą Apache Hadoop — Azure HDInsight
description: Dowiedz się, jak używać protokołu SSH do uruchamiania zadań MapReduce przy użyciu Apache Hadoop w usłudze HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: 543bc29adc85bd767de9479607d067fadf7b0078
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934697"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>Korzystanie z MapReduce z usługą Apache Hadoop w usłudze HDInsight przy użyciu protokołu SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Dowiedz się, jak przesyłać zadania MapReduce z połączenia Secure Shell (SSH) do usługi HDInsight.

> [!NOTE]
> Jeśli znasz już serwery Apache Hadoop oparte na systemie Linux, ale jesteś nowym elementem usługi HDInsight, zobacz [wskazówki dotyczące usługi HDInsight opartej](../hdinsight-hadoop-linux-information.md)na systemie Linux.

## <a name="prerequisites"></a>Wymagania wstępne

Klaster Apache Hadoop w usłudze HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="use-hadoop-commands"></a>Korzystanie z poleceń Hadoop

1. Użyj [polecenia SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) do nawiązania połączenia z klastrem. Edytuj poniższe polecenie, zastępując wartość CLUSTERname nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Po nawiązaniu połączenia z klastrem usługi HDInsight Użyj następującego polecenia, aby uruchomić zadanie MapReduce:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    To polecenie uruchamia klasę `wordcount`, która jest zawarta w pliku `hadoop-mapreduce-examples.jar`. Używa `/example/data/gutenberg/davinci.txt` dokumentu jako danych wejściowych, a dane wyjściowe są przechowywane w `/example/data/WordCountOutput`.

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat tego zadania MapReduce i przykładowych danych, zobacz [Korzystanie z MapReduce w Apache Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md).

    Zadanie emituje szczegóły w miarę procesów i zwraca informacje podobne do następującego tekstu po zakończeniu zadania:

    ```output
    File Input Format Counters
    Bytes Read=1395666
    File Output Format Counters
    Bytes Written=337623
    ```

1. Po zakończeniu zadania użyj następującego polecenia, aby wyświetlić listę plików wyjściowych:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    To polecenie wyświetla dwa pliki, `_SUCCESS` i `part-r-00000`. Plik `part-r-00000` zawiera dane wyjściowe dla tego zadania.

    > [!NOTE]  
    > Niektóre zadania MapReduce mogą dzielić wyniki w wielu plikach **części-r-#** # # #. Jeśli tak, użyj sufiksu # # # # #, aby wskazać kolejność plików.

1. Aby wyświetlić dane wyjściowe, użyj następującego polecenia:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    To polecenie wyświetla listę wyrazów, które znajdują się w pliku **wasbs://example/Data/Gutenberg/DaVinci.txt** oraz liczbę przypadków wystąpienia każdego wyrazu. Następujący tekst to przykład danych zawartych w pliku:

    ```output
    wreathed        3
    wreathing       1
    wreaths         1
    wrecked         3
    wrenching       1
    wretched        6
    wriggling       1
    ```

## <a name="next-steps"></a>Następne kroki

Jak widać, polecenia usługi Hadoop umożliwiają łatwe uruchamianie zadań MapReduce w klastrze usługi HDInsight, a następnie wyświetlanie danych wyjściowych zadania. Aby uzyskać informacje o innych sposobach pracy z usługą Hadoop w usłudze HDInsight:

* [Korzystanie z MapReduce w usłudze HDInsight Hadoop](hdinsight-use-mapreduce.md)
* [Używanie Apache Hive z Apache Hadoop w usłudze HDInsight](hdinsight-use-hive.md)
