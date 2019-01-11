---
title: Przekształcanie danych przy użyciu działania technologii MapReduce usługi Hadoop w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przetwarzać dane, uruchamiając Hadoop MapReduce programów w klastrze usługi HDInsight platformy Azure z usługi Azure data factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 4543982f731feb44a8f02581c11714dec2b206f9
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214508"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Przekształcanie danych przy użyciu działania technologii MapReduce usługi Hadoop w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-map-reduce.md)
> * [Bieżąca wersja](transform-data-using-hadoop-map-reduce.md)

Działanie HDInsight MapReduce w usłudze Data Factory [potoku](concepts-pipelines-activities.md) wywołuje MapReduce program na [własne](compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klastra HDInsight. W tym artykule opiera się na [działania przekształcania danych](transform-data.md) artykułu, który przedstawia ogólny przegląd działań przekształcania obsługiwanych i przekształcania danych.

Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zapoznaj się z artykułem [wprowadzenie do usługi Azure Data Factory](introduction.md) i wykonać instrukcje z samouczka: [Samouczek: Przekształcanie danych](tutorial-transform-data-spark-powershell.md) przed przeczytaniem tego artykułu.

Zobacz [Pig](transform-data-using-hadoop-pig.md) i [Hive](transform-data-using-hadoop-hive.md) Aby uzyskać szczegółowe informacje o uruchamianiu programów Pig/Hive skrypty w HDInsight klastra z potoku przy użyciu działań HDInsight Pig i Hive.

## <a name="syntax"></a>Składnia

```json
{
    "name": "Map Reduce Activity",
    "description": "Description",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.myorg.SampleClass",
        "jarLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "MyAzureStorage/jars/sample.jar",
        "getDebugInfo": "Failure",
        "arguments": [
            "-SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>Szczegóły składni

| Właściwość          | Opis                              | Wymagane |
| ----------------- | ---------------------------------------- | -------- |
| name              | Nazwa działania                     | Yes      |
| description       | Tekst opisujący przeznaczenie działania | Nie       |
| type              | Dla działania MapReduce typ działania jest HDinsightMapReduce | Yes      |
| linkedServiceName | Odwołanie do klastra HDInsight zarejestrowany jako połączonej usługi w usłudze Data Factory. Aby dowiedzieć się więcej na temat tej połączonej usługi, zobacz [usługi połączone usługi Compute](compute-linked-services.md) artykułu. | Yes      |
| className         | Nazwa klasy do wykonania         | Yes      |
| jarLinkedService  | Odwołanie do połączonej usługi magazynu platformy Azure używane do przechowywania plików Jar. Jeśli nie określisz ta połączona usługa połączona usługa Azure Storage zdefiniowane w połączonej usługi HDInsight jest używany. | Nie       |
| jarFilePath       | Podaj ścieżkę do plików Jar, przechowywane w usłudze Azure Storage, odwołuje się element jarLinkedService. Nazwa pliku jest rozróżniana wielkość liter. | Yes      |
| jarlibs           | Ciąg tablicy ścieżki do plików biblioteki Jar, które są przywoływane przez zadanie w usłudze Azure Storage, zdefiniowane w jarLinkedService. Nazwa pliku jest rozróżniana wielkość liter. | Nie       |
| getDebugInfo      | Określa, kiedy pliki dziennika są kopiowane do usługi Azure Storage używanego przez klaster HDInsight (lub) określony przez jarLinkedService. Dozwolone wartości: Brak zawsze lub niepowodzenie. Wartość domyślna: Brak. | Nie       |
| argumenty         | Określa tablicę argumentów dla zadania usługi Hadoop. Argumenty są przekazywane jako argumenty wiersza polecenia w odniesieniu do każdego zadania. | Nie       |
| Definiuje           | Określ parametry jako pary klucz/wartość do odwoływania się do skryptu programu Hive. | Nie       |



## <a name="example"></a>Przykład
Działania technologii MapReduce w HDInsight służy do uruchamiania dowolnego pliku jar MapReduce w klastrze usługi HDInsight. W poniższej definicji JSON przykładowe potoku działanie HDInsight jest skonfigurowany do uruchomienia pliku Mahout JAR.

```json
{
    "name": "MapReduce Activity for Mahout",
    "description": "Custom MapReduce to generate Mahout result",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
        "jarLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
        "arguments": [
            "-s",
            "SIMILARITY_LOGLIKELIHOOD",
            "--input",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
            "--output",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
            "--maxSimilaritiesPerItem",
            "500",
            "--tempDir",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
        ]
    }
}
```
Można określić argumenty dla programu MapReduce w **argumenty** sekcji. W czasie wykonywania, widzisz kilka dodatkowych argumentów (na przykład: mapreduce.job.tags) z platformy MapReduce. Aby odróżnić argumenty z argumentami MapReduce, należy wziąć pod uwagę przy użyciu opcji i wartości jako argumenty, jak pokazano w poniższym przykładzie (- s, — dane wejściowe, — itp., dane wyjściowe są opcje bezpośrednio następuje ich wartości).

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły, które wyjaśniają, jak przekształcać dane w inny sposób:

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie technologii hive](transform-data-using-hadoop-hive.md)
* [Działania technologii pig](transform-data-using-hadoop-pig.md)
* [Działania przesyłania strumieniowego usługi Hadoop](transform-data-using-hadoop-streaming.md)
* [Działania platformy Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch Execution działania](transform-data-using-machine-learning.md)
* [Działania procedur składowanych](transform-data-using-stored-procedure.md)
