---
title: Przekształcanie danych z użyciem jar
description: Dowiedz się, jak przetwarzać lub przekształcać dane, uruchamiając plik JAR.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 4a01a21259c4957b6f497bf213a3ef53f940bab7
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893964"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Przekształcanie danych przez uruchomienie działania jar w Azure Databricks

Działanie Azure Databricks jar w [potoku Data Factory](concepts-pipelines-activities.md) uruchamia plik JAR platformy Spark w klastrze Azure Databricks. W tym artykule opisano [działania przekształcania danych](transform-data.md) artykuł, który przedstawia ogólne omówienie transformacji danych i obsługiwanych działań transformacji. Azure Databricks to zarządzana platforma do uruchamiania Apache Spark.

Poniższy klip wideo zawiera jedenastominutowe wprowadzenie i demonstrację tej funkcji:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Definicja działania jar

Poniżej znajduje się przykładowa definicja JSON działania dotyczącego jar elementów datakostek:

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Właściwości działania jar

W poniższej tabeli opisano właściwości JSON używane w definicji JSON:

|Właściwość|Opis|Wymagane|
|:--|---|:-:|
|name|Nazwa działania w potoku.|Tak|
|description|Tekst opisujący działanie działania.|Nie|
|type|W przypadku działania jar dla datakostki typ działania to DatabricksSparkJar.|Tak|
|linkedServiceName|Nazwa połączonej usługi datakostki, w której działa działanie jar. Aby dowiedzieć się więcej na temat tej połączonej usługi, zobacz artykuł informacje o [obliczeniu usługi połączonej](compute-linked-services.md) .|Tak|
|mainClassName|Pełna nazwa klasy zawierającej metodę Main, która ma zostać wykonana. Ta klasa musi być zawarta w formacie JAR udostępnionym jako biblioteka.|Tak|
|parameters|Parametry, które zostaną przesłane do metody Main.  To jest tablica ciągów.|Nie|
|biblioteki|Lista bibliotek do zainstalowania w klastrze, w którym będą wykonywane zadania. Może to być tablica < String, Object >|Tak (co najmniej jeden zawierający metodę mainClassName)|

> [!NOTE]
> **Znany problem** — w przypadku używania tego samego [interaktywnego klastra](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) do uruchamiania współbieżnych elementów danych jar (bez ponownego uruchomienia klastra) występuje znany problem w kostkach danych, gdzie w parametrach pierwszego działania będą używane również następujące działania. Z tego powodu nieprawidłowe parametry są przesyłane do kolejnych zadań. Aby uniknąć tego problemu, użyj [klastra zadań](compute-linked-services.md#example---using-new-job-cluster-in-databricks) . 

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

Zazwyczaj biblioteki jar są przechowywane w obszarze dBfs:/FileStore/Jars przy użyciu interfejsu użytkownika. Wszystkie te listę można wyświetlić za pomocą interfejsu wiersza polecenia: *datakosteks FS LS dBfs:/FileStore/Job-Jars* 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Kopiowanie biblioteki przy użyciu interfejsu wiersza polecenia datakosteks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
Użyj interfejsu wiersza polecenia datakosteks [(kroki instalacji)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Przykład: kopiowanie w postaci JAR do dBfs. *dBfs CP sparkpi-Assembly-0,1. jar dBfs:/docs/SparkPi. jar*
