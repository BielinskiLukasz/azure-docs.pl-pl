---
title: Przekształcanie danych za pomocą języka Python — platforma Azure | Microsoft Docs
description: Dowiedz się, jak przetwarzać lub przekształcać dane, uruchamiając w języku Python.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: craigg
ms.openlocfilehash: 02c4644c4440c3a00a21ef22674bcc0d00902ac2
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70140791"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Przekształcanie danych przez uruchomienie działania języka Python w Azure Databricks

Działanie języka Python Azure Databricks w [potoku Data Factory](concepts-pipelines-activities.md) uruchamia plik w języku Python w klastrze Azure Databricks. W tym artykule przedstawiono artykuł [działania](transform-data.md) przekształcania danych, który zawiera ogólne omówienie transformacji danych i obsługiwanych działań transformacji. Azure Databricks to zarządzana platforma do uruchamiania Apache Spark.

Poniższy klip wideo zawiera jedenastominutowe wprowadzenie i demonstrację tej funkcji:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Definicja działania w języku Python

Poniżej znajduje się przykładowa definicja JSON działania w języku Python:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Właściwości działania Python dla elementów datakostek

W poniższej tabeli opisano właściwości JSON używane w definicji JSON:

|Właściwość|Opis|Wymagane|
|---|---|---|
|name|Nazwa działania w potoku.|Tak|
|description|Tekst opisujący działanie działania.|Nie|
|type|Dla działania języka Python dla elementów datakostks typem działania jest DatabricksSparkPython.|Tak|
|linkedServiceName|Nazwa połączonej usługi datakostki, w której działa działanie języka Python. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz artykuł dotyczący [połączonych usług](compute-linked-services.md) obliczeniowych.|Tak|
|pythonFile|Identyfikator URI pliku języka Python, który ma zostać wykonany. Obsługiwane są tylko ścieżki DBFS.|Tak|
|parameters|Parametry wiersza polecenia, które zostaną przesłane do pliku języka Python. To jest tablica ciągów.|Nie|
|bibliotece|Lista bibliotek do zainstalowania w klastrze, w którym będą wykonywane zadania. Może to być tablica < String, Object >|Nie|

## <a name="supported-libraries-for-databricks-activities"></a>Biblioteki obsługiwane dla działań datakostek

W powyższej definicji działań datacegły należy określić następujące typy biblioteki: *jar*, *jaja*, *Maven*, *PyPi*, *Cran*.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Aby uzyskać więcej szczegółowych informacji, zapoznaj się z [dokumentacją](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) dla typów bibliotek.

## <a name="how-to-upload-a-library-in-databricks"></a>Jak przekazać bibliotekę w kostkach

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Korzystanie z interfejsu użytkownika obszaru roboczego](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Aby uzyskać ścieżkę dBfs biblioteki dodanej przy użyciu interfejsu użytkownika, można użyć elementu [CLI (instalacja)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Zazwyczaj biblioteki jar są przechowywane w obszarze dBfs:/FileStore/Jars przy użyciu interfejsu użytkownika. Wszystkie te listę można wyświetlić za pomocą interfejsu wiersza polecenia: datakosteks *FS LS dBfs:/FileStore/Jars* 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Kopiowanie biblioteki przy użyciu interfejsu wiersza polecenia datakosteks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Przykład: *datakosteks FS CP sparkpi-Assembly-0,1. jar dBfs:/FileStore/Jars*