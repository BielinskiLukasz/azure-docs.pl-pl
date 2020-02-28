---
title: Korzystanie z platformy Apache Pig
titleSuffix: Azure HDInsight
description: Dowiedz się, jak używać świni z Apache Hadoop w usłudze HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: ea960a92aee1c9447bb12d27cffdc42de9fd907a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77672127"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>Korzystanie z usługi Apache świni z usługą Apache Hadoop w usłudze HDInsight

Dowiedz się, jak używać oprogramowania [Apache świni](https://pig.apache.org/) z usługą HDInsight.

Apache świni to platforma służąca do tworzenia programów dla Apache Hadoop przy użyciu języka proceduralnego znanego jako *świnie*. Świnie są alternatywą dla języka Java do tworzenia rozwiązań *MapReduce* i są dołączone do usługi Azure HDInsight. Skorzystaj z poniższej tabeli, aby poznać różne sposoby używania świni z usługą HDInsight:

## <a id="why"></a>Dlaczego warto używać oprogramowania Apache świni

Jednym z wyzwań związanych z przetwarzaniem danych przy użyciu MapReduce w usłudze Hadoop jest implementowanie logiki przetwarzania przy użyciu tylko mapy i funkcji ograniczania. W przypadku przetwarzania złożonego często trzeba przerwać przetwarzanie w wielu operacjach MapReduce, które są połączone ze sobą, aby osiągnąć żądany wynik.

Świnie umożliwiają definiowanie przetwarzania w postaci serii transformacji, w których dane są przetwarzane przez program w celu wygenerowania żądanych danych wyjściowych.

Język łaciński (wieprzowina) pozwala na opisywanie przepływu danych z nieprzetworzonego wejścia, przez jedno lub więcej transformacji, w celu wygenerowania żądanych danych wyjściowych. Programy z wieprzowiną są zgodne z tym ogólnym wzorcem:

* **Ładowanie**: odczytywanie danych do manipulacji z systemu plików.

* **Przekształcanie**: manipulowanie danymi.

* **Zrzut lub przechowywanie**: dane wyjściowe na ekranie lub przechowywane do przetworzenia.

### <a name="user-defined-functions"></a>Funkcje zdefiniowane przez użytkownika

Wieprzowina również obsługuje funkcje zdefiniowane przez użytkownika (UDF), które umożliwiają wywoływanie zewnętrznych składników implementujących logikę, które są trudne do modelowania w przypadku trzody chlewnej.

Aby uzyskać więcej informacji na temat wieprzowiny, zobacz artykuł dotyczący odniesienia w postaci [wieprzowiny](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) — Podręcznik 1 i [Dokumentacja dotycząca tuszu łacińskiego nr 2](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html).

## <a id="data"></a>Przykładowe dane

Usługa HDInsight oferuje różne przykładowe zestawy danych, które są przechowywane w katalogach `/example/data` i `/HdiSamples`. Te katalogi znajdują się w domyślnym magazynie klastra. Przykładowa świnia w tym dokumencie używa pliku *Log4J* z `/example/data/sample.log`.

Każdy dziennik wewnątrz pliku składa się z wierszy pól zawierających `[LOG LEVEL]` pole, aby wyświetlić typ i ważność, na przykład:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

W poprzednim przykładzie poziom dziennika jest błąd.

> [!NOTE]  
> Możesz również wygenerować plik Log4J za pomocą narzędzia rejestrowania usługi [Apache Log4J](https://en.wikipedia.org/wiki/Log4j) , a następnie przekazać ten plik do obiektu BLOB. Aby uzyskać instrukcje [, zobacz Przekazywanie danych do usługi HDInsight](hdinsight-upload-data.md) . Aby uzyskać więcej informacji na temat sposobu używania obiektów BLOB w usłudze Azure Storage w usłudze HDInsight, zobacz [Korzystanie z usługi azure BLOB Storage w usłudze HDInsight](hdinsight-hadoop-use-blob-storage.md).

## <a id="job"></a>Przykładowe zadanie

Następujące zadanie w postaci wieprzowiny ładuje plik `sample.log` z domyślnego magazynu dla klastra usługi HDInsight. Następnie wykonuje serię transformacji, które powodują, ile razy każdy poziom dziennika miał miejsce w danych wejściowych. Wyniki są zapisywane w strumieniu STDOUT.

    ```
    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;
    ```

Na poniższej ilustracji przedstawiono podsumowanie poszczególnych transformacji danych.

![Graficzna reprezentacja przekształceń][image-hdi-pig-data-transformation]

## <a id="run"></a>Uruchamianie zadania łacińskie

Usługa HDInsight może uruchamiać zadania łacińskie z tusz przy użyciu różnych metod. Skorzystaj z poniższej tabeli, aby zdecydować, która metoda jest dla Ciebie odpowiednia, a następnie postępuj zgodnie z linkiem do przewodnika.

## <a name="pig-and-sql-server-integration-services"></a>Świnie i SQL Server Integration Services

Do uruchomienia zadania świni można użyć SQL Server Integration Services (SSIS). Pakiet Feature Pack dla usług SSIS oferuje następujące składniki, które pracują z zadaniami dotyczącymi trzody chlewnej w usłudze HDInsight.

* [Zadanie usługi Azure HDInsight świnie][pigtask]

* [Menedżer połączeń subskrypcji platformy Azure][connectionmanager]

Więcej informacji na temat pakietu Azure Feature Pack dla usług SSIS [znajdziesz tutaj][ssispack].

## <a id="nextsteps"></a>Następne kroki

Teraz, gdy wiesz już, jak używać świni z usługą HDInsight, Skorzystaj z poniższych linków, aby poznać inne sposoby pracy z usługą Azure HDInsight.

* [Przekazywanie danych do usługi HDInsight](hdinsight-upload-data.md)
* [Korzystanie z Apache Hive z usługą HDInsight](./hadoop/hdinsight-use-hive.md)
* [Korzystanie z platformy Apache Sqoop z usługą HDInsight](hdinsight-use-sqoop.md)
* [Korzystanie z zadań MapReduce z usługą HDInsight](./hadoop/hdinsight-use-mapreduce.md)

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: https://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: https://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: https://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/use-pig/hdi-data-transformation.gif
