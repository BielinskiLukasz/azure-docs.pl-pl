---
title: Przekształcanie danych przy użyciu działania programu Hive w usłudze Hadoop w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć działania programu Hive w usłudze Azure data factory w celu uruchomienia zapytania programu Hive na na — żądanie/swój własny klaster HDInsight.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: douglasl
ms.openlocfilehash: e77b8c89954064fb143f04f0f901d1512d4fc50d
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55491012"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Przekształcanie danych przy użyciu działania programu Hive w usłudze Hadoop w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-hive-activity.md)
> * [Bieżąca wersja](transform-data-using-hadoop-hive.md)

Działanie HDInsight Hive w usłudze Data Factory [potoku](concepts-pipelines-activities.md) wykonuje zapytania programu Hive na [własne](compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klastra HDInsight. W tym artykule opiera się na [działania przekształcania danych](transform-data.md) artykułu, który przedstawia ogólny przegląd działań przekształcania obsługiwanych i przekształcania danych.

Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zapoznaj się z artykułem [wprowadzenie do usługi Azure Data Factory](introduction.md) i wykonaj [samouczek: Przekształcanie danych](tutorial-transform-data-spark-powershell.md) przed przeczytaniem tego artykułu. 

## <a name="syntax"></a>Składnia

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\HiveScripts\\MyHiveSript.hql",
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```
## <a name="syntax-details"></a>Szczegóły składni
| Właściwość            | Opis                                                  | Wymagane |
| ------------------- | ------------------------------------------------------------ | -------- |
| name                | Nazwa działania                                         | Yes      |
| description         | Tekst opisujący przeznaczenie działania                | Nie       |
| type                | Dla działania programu Hive typ działania jest HDinsightHive        | Yes      |
| linkedServiceName   | Odwołanie do klastra HDInsight zarejestrowany jako połączonej usługi w usłudze Data Factory. Aby dowiedzieć się więcej na temat tej połączonej usługi, zobacz [usługi połączone usługi Compute](compute-linked-services.md) artykułu. | Yes      |
| scriptLinkedService | Odwołanie do połączoną usługę Azure Storage używany do przechowywania skryptu programu hive służącego do wykonania. Jeśli nie określisz ta połączona usługa połączona usługa Azure Storage zdefiniowane w połączonej usługi HDInsight jest używany. | Nie       |
| scriptPath          | Podaj ścieżkę do pliku skryptu w usłudze Azure Storage, określane za pomocą elementu scriptLinkedService. Nazwa pliku jest rozróżniana wielkość liter. | Yes      |
| getDebugInfo        | Określa, kiedy pliki dziennika są kopiowane do usługi Azure Storage używanego przez klaster HDInsight (lub) określonej za pomocą elementu scriptLinkedService. Dozwolone wartości: Brak zawsze lub niepowodzenie. Wartość domyślna: Brak. | Nie       |
| argumenty           | Określa tablicę argumentów dla zadania usługi Hadoop. Argumenty są przekazywane jako argumenty wiersza polecenia w odniesieniu do każdego zadania. | Nie       |
| Definiuje             | Określ parametry jako pary klucz/wartość do odwoływania się do skryptu programu Hive. | Nie       |
| queryTimeout        | Zapytania wartość limitu czasu (w minutach). Zastosowanie w przypadku klastra HDInsight z pakietem Enterprise Security włączone. | Nie       |

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły, które wyjaśniają, jak przekształcać dane w inny sposób: 

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działania technologii pig](transform-data-using-hadoop-pig.md)
* [Działania technologii MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działania przesyłania strumieniowego usługi Hadoop](transform-data-using-hadoop-streaming.md)
* [Działania platformy Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch Execution działania](transform-data-using-machine-learning.md)
* [Działania procedur składowanych](transform-data-using-stored-procedure.md)
