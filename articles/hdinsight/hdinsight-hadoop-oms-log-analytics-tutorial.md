---
title: "Umożliwia monitorowanie klastrów usługi HDInsight Azure Log Analytics | Dokumentacja firmy Microsoft"
description: "Dowiedz się, jak używać usługi Analiza dzienników Azure do monitorowania zadania uruchomione w klastrze usługi HDInsight."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: nitinme
ms.openlocfilehash: 6677b0b3ed047ce011bfbb72c25e45195859830a
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2017
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Analiza dzienników Azure umożliwia monitorowanie klastrów usługi HDInsight

Dowiedz się, jak używać usługi Analiza dzienników Azure do monitorowania działania klastra usługi Hadoop w usłudze HDInsight.

[Zaloguj się Analytics](../log-analytics/log-analytics-overview.md) to usługa w [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) który monitoruje chmury i lokalnych środowiskach utrzymywać ich dostępności i wydajności. Zbiera ona dane generowane przez zasoby w środowiskach chmurowych i lokalnych oraz inne narzędzia do monitorowania, aby przeprowadzać analizę na podstawie wielu źródeł. 

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Przed rozpoczęciem tego samouczka musisz dysponować subskrypcją platformy Azure. Zobacz [Utwórz bezpłatne konto platformy Azure już dzisiaj](https://azure.microsoft.com/free).

* **Klaster Azure HDInsight**. Obecnie można używać Azure Operations Management Suite z następujące typy klastrów usługi HDInsight:

    * Hadoop
    * HBase
    * Zapytanie interakcyjne
    * Kafka
    * platforma Spark
    * Storm

    Aby uzyskać instrukcje dotyczące sposobu tworzenia klastra usługi HDInsight, zobacz [Rozpoczynanie pracy z usługą Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Obszar roboczy analizy dzienników**. Ten obszar roboczy można traktować jako unikatowy środowiska analizy dzienników z własnych danych repozytorium, źródła danych i rozwiązania. Musi mieć jeden taki obszar roboczy już utworzone, które można skojarzyć z klastrami Azure HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie obszaru roboczego analizy dzienników](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace).

## <a name="configure-hdinsight-cluster-to-use-log-analytics"></a>Konfigurowanie klastra usługi HDInsight, aby użyć analizy dzienników

W tej sekcji skonfigurujesz istniejącego klastra usługi HDInsight Hadoop do użycia z obszarem roboczym usługi Analiza dzienników Azure do monitorowania, zadań, dzienników debugowania itd.

1. Otwórz klaster usługi HDInsight w portalu Azure.
2. W okienku po lewej stronie kliknij **monitorowanie**.
3. W okienku po prawej stronie kliknij **włączyć**, wybierz istniejący obszar roboczy analizy dzienników, a następnie kliknij przycisk **zapisać**.

    ![Włącz monitorowanie klastrów usługi HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Włącz monitorowanie klastrów usługi HDInsight")

    Może potrwać kilka minut, aby zapisać ustawienia.  Po zakończeniu widzi **Otwórz pulpit nawigacyjny OMS** przycisk u góry. 

    ![Otwórz pulpit nawigacyjny usługi Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "OMS Otwórz pulpit nawigacyjny")

5. Kliknij przycisk **OMS Otwórz pulpit nawigacyjny**.
6. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia platformy Azure.

    ![Portal Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "portal usługi Operations Management Suite")

## <a name="next-steps"></a>Następne kroki
* [Dodawanie rozwiązania do zarządzania klastrem usługi HDInsight do analizy dzienników](hdinsight-hadoop-oms-log-analytics-management-solutions.md)
