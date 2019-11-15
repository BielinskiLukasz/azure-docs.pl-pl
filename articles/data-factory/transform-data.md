---
title: 'Przekształcanie danych przy użyciu Azure Data Factory '
description: Dowiedz się, jak przekształcać dane lub przetwarzać dane w Azure Data Factory przy użyciu usługi Hadoop, Machine Learning lub Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/31/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 268c1e66010720d1da052183165ce1cea50a1095
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092010"
---
# <a name="transform-data-in-azure-data-factory"></a>Przekształcanie danych w Azure Data Factory
> [!div class="op_single_selector"]
> * [Mapowanie przepływu danych](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [Przesyłanie strumieniowe w usłudze HDInsight](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Machine Learning](transform-data-using-machine-learning.md) 
> * [Procedura składowana](transform-data-using-stored-procedure.md)
> * [Język U-SQL usługi Data Lake Analytics](transform-data-using-data-lake-analytics.md)
> * [Notes datakostki](transform-data-databricks-notebook.md)
> * [W jar](transform-data-databricks-jar.md)
> * [Środowiska Python](transform-data-databricks-python.md)
> * [Niestandardowe środowisko .NET](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>Omówienie
W tym artykule opisano działania przekształcania danych w Azure Data Factory, które umożliwiają przekształcanie i przetwarzanie danych pierwotnych w przewidywania i szczegółowe informacje na dużą skalę. Działanie transformacji jest wykonywane w środowisku obliczeniowym, takim jak Azure Databricks lub Azure HDInsight. Zawiera łącza do artykułów ze szczegółowymi informacjami na temat poszczególnych działań transformacji.

Data Factory obsługuje następujące działania przekształcania danych, które można dodać do [potoków](concepts-pipelines-activities.md) pojedynczo lub łączyć z innymi działaniami.

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>Przekształć natywnie w Azure Data Factory z przepływami danych

### <a name="mapping-data-flows"></a>Przepływy danych mapowania

Mapowanie przepływów danych to wizualnie zaprojektowane przekształcenia danych w Azure Data Factory. Przepływy danych umożliwiają inżynierom danych Tworzenie logiki transformacji danych graficznych bez pisania kodu. Wyniki przepływów danych są wykonywane jako działania w ramach potoków Azure Data Factory, które używają skalowanych klastrów Spark. Działania związane z przepływem danych mogą być operacyjne za pośrednictwem istniejących Data Factory planowania, kontroli, przepływu i monitorowania. Aby uzyskać więcej informacji, zobacz [Mapowanie przepływów danych](concepts-data-flow-overview.md).

### <a name="wrangling-data-flows"></a>Przetwarzanie przepływy danych

Przetwarzanie przepływy danych w Azure Data Factory umożliwiają iteracyjne wykonywanie bezobsługowego przygotowywania danych w skali chmury. Przetwarzanie przepływy danych integrują się z usługą [Power Query online](https://docs.microsoft.com/power-query/) i udostępniają funkcje Power Query M dla danych przetwarzanie w skali chmury za pośrednictwem wykonywania platformy Spark. Aby uzyskać więcej informacji, zobacz [Przetwarzanie Data](wrangling-data-flow-overview.md)Flows.

## <a name="external-transformations"></a>Przekształcenia zewnętrzne

Opcjonalnie możesz ręcznie przekształceń kodu i zarządzać zewnętrznym środowiskiem obliczeniowym.

### <a name="hdinsight-hive-activity"></a>Działanie programu Hive w usłudze HDInsight
Działanie programu Hive w usłudze HDInsight w potoku Data Factory wykonuje zapytania Hive na własnym lub lokalnym klastrze usługi HDInsight opartym na systemie Windows/Linux. Aby uzyskać szczegółowe informacje o tym działaniu, zobacz artykuł dotyczący [działań Hive](transform-data-using-hadoop-hive.md) . 

### <a name="hdinsight-pig-activity"></a>Działanie dotyczące usługi HDInsight świni
Działanie świni w usłudze HDInsight w potoku Data Factory wykonuje zapytania dotyczące trzody chlewnej we własnym lub lokalnym klastrze usługi HDInsight opartym na systemie Windows/Linux. Szczegółowe informacje na temat tego działania znajdziesz w artykule dotyczącym działań związanych z [świnią](transform-data-using-hadoop-pig.md) . 

### <a name="hdinsight-mapreduce-activity"></a>Działanie MapReduce usługi HDInsight
Działanie MapReduce usługi HDInsight w potoku Data Factory wykonuje programy MapReduce na własnym lub na żądanie w klastrze HDInsight opartym na systemie Windows/Linux. Aby uzyskać szczegółowe informacje o tym działaniu, zobacz artykuł dotyczący [działań MapReduce](transform-data-using-hadoop-map-reduce.md) .

### <a name="hdinsight-streaming-activity"></a>Działanie przesyłania strumieniowego HDInsight
Działanie przesyłania strumieniowego usługi HDInsight w potoku Data Factory wykonuje programy przesyłania strumieniowego Hadoop we własnym lub lokalnym klastrze usługi HDInsight opartym na systemie Windows/Linux. Zobacz [działanie przesyłania strumieniowego HDInsight](transform-data-using-hadoop-streaming.md) , aby uzyskać szczegółowe informacje o tym działaniu.

### <a name="hdinsight-spark-activity"></a>Działanie usługi HDInsight Spark
Działanie usługi HDInsight Spark w potoku Data Factory wykonuje programy platformy Spark w ramach własnego klastra usługi HDInsight. Aby uzyskać szczegółowe informacje, zobacz [wywoływanie programów Spark z Azure Data Factory](transform-data-using-spark.md). 

### <a name="machine-learning-activities"></a>Działania Machine Learning
Azure Data Factory umożliwia łatwe tworzenie potoków używających opublikowanej usługi sieci Web Azure Machine Learning do analizy predykcyjnej. Za pomocą [działania wykonywania wsadowego](transform-data-using-machine-learning.md) w potoku Azure Data Factory można wywołać usługę sieci Web Machine Learning, aby przetworzyć prognozy dotyczące danych w usłudze Batch.

W miarę upływu czasu modele predykcyjne w eksperymentach oceniających Machine Learning muszą być ponownie przeszkoli przy użyciu nowych wejściowych zestawów danych. Po wykonaniu ponownych szkoleń należy zaktualizować usługę sieci Web oceniania za pomocą Machine Learninggo modelu. Możesz użyć [działania Aktualizuj zasób](update-machine-learning-models.md) , aby zaktualizować usługę sieci Web przy użyciu nowo nauczonego modelu.  

Aby uzyskać szczegółowe informacje o tych Machine Learning działaniach, zobacz temat [używanie Machine Learning działań](transform-data-using-machine-learning.md) . 

### <a name="stored-procedure-activity"></a>Działanie procedury składowanej
Możesz użyć działania procedury składowanej SQL Server w potoku Data Factory, aby wywołać procedurę składowaną w jednym z następujących magazynów danych: Azure SQL Database, Azure SQL Data Warehouse, SQL Server bazą danych w przedsiębiorstwie lub maszynie wirtualnej platformy Azure. Szczegóły można znaleźć w artykule dotyczącym [działania procedury składowanej](transform-data-using-stored-procedure.md) .  

### <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics działanie U-SQL
Data Lake Analytics działanie U-SQL uruchamia skrypt U-SQL w klastrze Azure Data Lake Analytics. Szczegóły można znaleźć w artykule dotyczącym [działania analiza danych U-SQL](transform-data-using-data-lake-analytics.md) . 

### <a name="databricks-notebook-activity"></a>Aktywność notesu datakostki

Działanie notesu Azure Databricks w potoku Data Factory uruchamia Notes datacegły w obszarze roboczym Azure Databricks. Azure Databricks to zarządzana platforma do uruchamiania Apache Spark. Zobacz [Przekształć dane, uruchamiając Notes datakostki](transform-data-databricks-notebook.md).

### <a name="databricks-jar-activity"></a>Aktywność jar

Działanie Azure Databricks jar w potoku Data Factory uruchamia plik JAR platformy Spark w klastrze Azure Databricks. Azure Databricks to zarządzana platforma do uruchamiania Apache Spark. Zobacz [Przekształć dane, uruchamiając działanie jar w Azure Databricks](transform-data-databricks-jar.md).

### <a name="databricks-python-activity"></a>Aktywność języka Python dla elementów datakostek

Działanie języka Python Azure Databricks w potoku Data Factory uruchamia plik w języku Python w klastrze Azure Databricks. Azure Databricks to zarządzana platforma do uruchamiania Apache Spark. Zobacz [Przekształć dane, uruchamiając działanie języka Python w Azure Databricks](transform-data-databricks-python.md).

### <a name="custom-activity"></a>Działanie niestandardowe
Jeśli zachodzi potrzeba przekształcenia danych w sposób, który nie jest obsługiwany przez Data Factory, można utworzyć niestandardowe działanie z własną logiką przetwarzania danych i użyć działania w potoku. Można skonfigurować niestandardowe działanie platformy .NET do uruchamiania przy użyciu usługi Azure Batch lub klastra usługi Azure HDInsight. Aby uzyskać szczegółowe informacje, zobacz artykuł [Używanie działań niestandardowych](transform-data-using-dotnet-custom-activity.md) . 

Możesz utworzyć niestandardowe działanie, aby uruchamiać skrypty w klastrze usługi HDInsight z zainstalowanym językiem R. Zobacz [Uruchamianie skryptów języka R przy użyciu usługi Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

### <a name="compute-environments"></a>Środowiska obliczeniowe
Należy utworzyć połączoną usługę dla środowiska obliczeniowego, a następnie użyć połączonej usługi podczas definiowania działania transformacji. Istnieją dwa typy środowisk obliczeniowych obsługiwanych przez Data Factory. 

- **Na żądanie**: w tym przypadku środowisko komputerowe jest w pełni zarządzane przez Data Factory. Jest automatycznie tworzony przez usługę Data Factory przed przesłaniem zadania w celu przetworzenia danych i usunięciu po zakończeniu zadania. Można skonfigurować i kontrolować szczegółowe ustawienia środowiska obliczeniowego na żądanie na potrzeby wykonywania zadań, zarządzania klastrami i uruchamiania akcji. 
- **Wprowadź swój własny**: w tym przypadku możesz zarejestrować własne środowisko obliczeniowe (na przykład klaster usługi HDInsight) jako połączoną usługę w Data Factory. Środowisko komputerowe jest zarządzane przez Ciebie, a usługa Data Factory używa jej do wykonywania działań. 

Zobacz artykuł dotyczący [połączonych usług](compute-linked-services.md) , aby dowiedzieć się więcej o usługach obliczeniowych obsługiwanych przez Data Factory. 

## <a name="next-steps"></a>Następne kroki
Zobacz poniższy samouczek, aby zapoznać się z przykładem działania przekształcania: [Samouczek: Przekształcanie danych przy użyciu platformy Spark](tutorial-transform-data-spark-powershell.md)
