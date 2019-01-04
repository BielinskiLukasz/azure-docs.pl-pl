---
title: Przekształcanie danych przy użyciu działania technologii Pig w usłudze Hadoop w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć działania technologii Pig w usłudze Azure data factory w celu uruchomienia skryptów usługi Pig na na — żądanie/swój własny klaster HDInsight.
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
ms.openlocfilehash: a29bd64c6b18d41028c8952f531698bbfa9e01e2
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014713"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Przekształcanie danych przy użyciu działania technologii Pig w usłudze Hadoop w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-pig-activity.md)
> * [Bieżąca wersja](transform-data-using-hadoop-pig.md)

Działanie HDInsight Pig w usłudze Data Factory [potoku](concepts-pipelines-activities.md) wykonuje zapytania Pig na [własne](compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klastra HDInsight. W tym artykule opiera się na [działania przekształcania danych](transform-data.md) artykułu, który przedstawia ogólny przegląd działań przekształcania obsługiwanych i przekształcania danych.

Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zapoznaj się z artykułem [wprowadzenie do usługi Azure Data Factory](introduction.md) i wykonaj [samouczek: Przekształcanie danych](tutorial-transform-data-spark-powershell.md) przed przeczytaniem tego artykułu. 

## <a name="syntax"></a>Składnia

```json
{
    "name": "Pig Activity",
    "description": "description",
    "type": "HDInsightPig",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\PigScripts\\MyPigSript.pig",
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

| Właściwość            | Opis                              | Wymagane |
| ------------------- | ---------------------------------------- | -------- |
| name                | Nazwa działania                     | Yes      |
| description         | Tekst opisujący przeznaczenie działania | Nie       |
| type                | Dla działania programu Hive typ działania jest HDinsightPig | Yes      |
| linkedServiceName   | Odwołanie do klastra HDInsight zarejestrowany jako połączonej usługi w usłudze Data Factory. Aby dowiedzieć się więcej na temat tej połączonej usługi, zobacz [usługi połączone usługi Compute](compute-linked-services.md) artykułu. | Yes      |
| scriptLinkedService | Odwołanie do połączonej usługi magazynu platformy Azure używane do przechowywania skryptu Pig. Jeśli nie określisz ta połączona usługa połączona usługa Azure Storage zdefiniowane w połączonej usługi HDInsight jest używany. | Nie       |
| scriptPath          | Podaj ścieżkę do pliku skryptu w usłudze Azure Storage, określane za pomocą elementu scriptLinkedService. Nazwa pliku jest rozróżniana wielkość liter. | Nie       |
| getDebugInfo        | Określa, kiedy pliki dziennika są kopiowane do usługi Azure Storage używanego przez klaster HDInsight (lub) określonej za pomocą elementu scriptLinkedService. Dozwolone wartości: Brak zawsze lub niepowodzenie. Wartość domyślna: Brak. | Nie       |
| argumenty           | Określa tablicę argumentów dla zadania usługi Hadoop. Argumenty są przekazywane jako argumenty wiersza polecenia w odniesieniu do każdego zadania. | Nie       |
| Definiuje             | Określ parametry jako pary klucz/wartość do odwoływania się do skryptu języka Pig. | Nie       |

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły, które wyjaśniają, jak przekształcać dane w inny sposób: 

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie technologii hive](transform-data-using-hadoop-hive.md)
* [Działania technologii MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działania przesyłania strumieniowego usługi Hadoop](transform-data-using-hadoop-streaming.md)
* [Działania platformy Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch Execution działania](transform-data-using-machine-learning.md)
* [Działania procedur składowanych](transform-data-using-stored-procedure.md)
