---
title: Używanie zapytania interakcyjnego za pomocą usługi Azure HDInsight
description: Dowiedz się, jak używać zapytania interakcyjnego (LLAP programu Hive) z HDInsight.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: 73f7e523ed0abc7d0453096cf783761dd6a884ba
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628668"
---
# <a name="use-interactive-query-with-hdinsight"></a>Używanie zapytania interakcyjnego z HDInsight
Zapytania interakcyjnego (nazywane również funkcji LLAP programu Hive, lub [Niskie opóźnienie analitycznego przetwarzania](https://cwiki.apache.org/confluence/display/Hive/LLAP)) jest usługi Azure HDInsight [typ klastra](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types). Zapytanie interakcyjne obsługuje buforowanie w pamięci, co sprawia, że zapytania programu Hive, szybsze i bardziej interaktywny.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)] 

Klaser zapytania interaktywnego różni się od klastra usługi Hadoop. Zawiera on tylko usługi Hive. 

> [!NOTE]
> Możesz uzyskać dostęp do usługi Hive, w to klastra zapytania interakcyjnego tylko przy użyciu widoku Hive narzędzia Ambari, z usługi Beeline i sterownika Microsoft Hive Open Database Connectivity (ODBC programu Hive). Nie masz dostępu do jego za pośrednictwem konsoli programu Hive, Templeton, narzędzie wiersza polecenia platformy Azure (Azure CLI) lub programu Azure PowerShell. 
> 
> 

## <a name="create-an-interactive-query-cluster"></a>Utwórz klaser zapytania interaktywnego
Aby dowiedzieć się, jak tworzenie klastra HDInsight, zobacz [Tworzenie klastrów usługi Hadoop w HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Wybierz typ klastra zapytania interakcyjnego.

## <a name="execute-hive-queries-from-interactive-query"></a>Wykonywanie zapytań Hive z zapytaniem interakcyjnym
Aby wykonać zapytania programu Hive, dostępne są następujące opcje:

* Korzystanie z usługi Power BI

    Zobacz [wizualizować Interactive Hive zapytania danych przy użyciu usługi Power BI w usłudze Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) zobacz [wizualizowanie danych big data dzięki usłudze Power BI w usłudze Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
 
* Korzystanie z rozwiązania Zeppelin

    Zobacz [Zeppelin użycia do uruchamiania zapytań programu Hive w usłudze Azure HDInsight ](../hdinsight-connect-hive-zeppelin.md).

* Korzystanie z programu Visual Studio

    Zobacz [nawiązywanie połączenia z usługi Azure HDInsight i uruchamianie zapytań Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries).

* Użyj programu Visual Studio Code

    Zobacz [Użyj programu Visual Studio Code dla programu Hive, LLAP lub pySpark](../hdinsight-for-vscode.md).
* Uruchamianie programu Hive za pomocą widoku Hive narzędzia Ambari.
  
    Zobacz [korzystanie z widoku Hive z usługą Hadoop w usłudze Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).
* Uruchom Hive, korzystając z usługi Beeline.
  
    Zobacz [korzystanie z programu Hive z usługą Hadoop w HDInsight z usługą Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    Korzystania z usługi Beeline z głównego węzła lub pustego węzła krawędzi. Firma Microsoft zaleca korzystanie z usługi Beeline od pustego węzła krawędzi. Aby uzyskać informacje dotyczące tworzenia klastra usługi HDInsight za pomocą pustego węzła krawędzi, zobacz [używanie pustych węzłów brzegowych w HDInsight](../hdinsight-apps-use-edge-node.md).
* Uruchamianie programu Hive za pomocą Hive ODBC.
  
    Zobacz [Connect Excel to Hadoop przy użyciu sterownika Hive ODBC firmy Microsoft](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Aby znaleźć parametry połączenia łączność bazy danych języka Java (JDBC):

1. Zaloguj się do narzędzia Ambari, korzystając z następującego adresu URL: https://\<nazwy klastra\>. AzureHDInsight.net.
2. W menu po lewej stronie wybierz **Hive**.
3. Aby skopiować adres URL, wybierz ikonę Schowka:
   
   ![JDBC LLAP interakcyjnych zapytań usługi HDInsight Hadoop](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [tworzenie klastrami interakcyjnych zapytań w HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Dowiedz się, jak [wizualizowanie danych big data dzięki usłudze Power BI w usłudze Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Dowiedz się, jak [uruchamianie zapytań programu Hive w usłudze Azure HDInsight za pomocą rozwiązania Zeppelin ](../hdinsight-connect-hive-zeppelin.md).
* Dowiedz się, jak [uruchamianie zapytań Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries).
* Dowiedz się, jak [użycia narzędzi HDInsight Tools for Visual Studio Code](../hdinsight-for-vscode.md).
* Dowiedz się, jak [użyć widoku Hive z usługą Hadoop w HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md)
* Dowiedz się, jak [korzystanie z usługi Beeline do przesyłania zapytań programu Hive w HDInsight](../hadoop/apache-hadoop-use-hive-beeline.md).
* Dowiedz się, jak [łączenie programu Excel z usługą Hadoop przy użyciu sterownika Hive ODBC firmy Microsoft](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

