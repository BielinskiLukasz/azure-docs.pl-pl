---
title: RpcTimeoutException dla Apache Spark Thrift Server w usłudze Azure HDInsight
description: Podczas przetwarzania dużych zestawów danych przy użyciu Apache Spark Thrift Server są wyświetlane błędy 502
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 0d4f2f9c8392e5b81f5abb8c67548baefae611c9
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620833"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Scenariusz: RpcTimeoutException dla Apache Spark Thrift Server w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania składników Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Aplikacja Spark kończy się niepowodzeniem z `org.apache.spark.rpc.RpcTimeoutException` powodu wyjątku i komunikatu: `Futures timed out`, jak w poniższym przykładzie:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError`i `overhead limit exceeded` błędy mogą również pojawić się `sparkthriftdriver.log` w jak w poniższym przykładzie:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Przyczyna

Te błędy są spowodowane brakiem zasobów pamięci podczas przetwarzania danych. Jeśli proces odzyskiwania pamięci Java zostanie uruchomiony, może to spowodować wysunięcie aplikacji Spark. Zapytania zaczynają przekroczyć limit czasu i przestaną być przetwarzane. Ten `Futures timed out` błąd wskazuje klaster pod silnym obciążeniem.

## <a name="resolution"></a>Rozwiązanie

Zwiększ rozmiar klastra, dodając więcej węzłów procesów roboczych lub zwiększając pojemność pamięci istniejących węzłów klastra. Możesz również dostosować Potok danych, aby zmniejszyć ilość danych przetwarzanych jednocześnie.

`spark.network.timeout` Kontroluje limit czasu dla wszystkich połączeń sieciowych. Zwiększenie limitu czasu sieci może spowodować więcej czasu na zakończenie niektórych krytycznych operacji, ale nie spowoduje to całkowitego rozwiązania problemu.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) za pomocą — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
