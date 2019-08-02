---
title: Apache Spark zadanie przesyłania strumieniowego, które odczytuje dane z klastra Apache Kafka, kończy się niepowodzeniem z NoClassDefFoundError w usłudze Azure HDInsight
description: Apache Spark zadanie przesyłania strumieniowego, które odczytuje dane z klastra Apache Kafka, kończy się niepowodzeniem z NoClassDefFoundError w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 986b1dd2e749a0968c744f861feb0ac0bf2376e8
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700485"
---
# <a name="scenario-apache-spark-streaming-job-that-reads-data-from-an-apache-kafka-cluster-fails-with-a-noclassdeffounderror-in-azure-hdinsight"></a>Scenariusz: Apache Spark zadanie przesyłania strumieniowego, które odczytuje dane z klastra Apache Kafka, kończy się niepowodzeniem z NoClassDefFoundError w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania składników Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Klaster Apache Spark uruchamia zadanie przesyłania strumieniowego Spark, które odczytuje dane z klastra Apache Kafka. Zadanie przesyłania strumieniowego Spark kończy się niepowodzeniem, jeśli kompresja strumienia Kafka jest włączona. W takim przypadku application_1525986016285_0193 aplikacji przędzy strumienia Spark nie powiodła się z powodu błędu:

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany określeniem wersji `spark-streaming-kafka` pliku JAR, który jest inny niż wersja klastra Kafka, który jest uruchomiony.

Na przykład jeśli używasz Kafka klastra w wersji 0.10.1, następujące polecenie spowoduje wystąpienie błędu:

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>Rozwiązanie

Użyj polecenia Spark-Submit z `–packages` opcją i upewnij się, że wersja pliku JAR-Streaming-Kafka jest taka sama jak wersja klastra Kafka, który jest uruchomiony.

## <a name="next-steps"></a>Kolejne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) za pomocą — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
