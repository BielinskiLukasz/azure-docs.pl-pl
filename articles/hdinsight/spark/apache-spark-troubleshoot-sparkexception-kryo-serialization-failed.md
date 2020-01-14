---
title: Problemy z JDBC/ODBC & Apache Thrift Framework — Azure HDInsight
description: Nie można pobrać dużych zestawów danych przy użyciu funkcji JDBC/ODBC i oprogramowania Apache Thrift w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 23693dcae2f361b88440ec88ca39fd8ed229d85a
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894258"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>Nie można pobrać dużych zestawów danych za pomocą JDBC/ODBC i oprogramowania Apache Thrift w usłudze HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania składników Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Podczas próby pobrania dużych zestawów danych przy użyciu JDBC/ODBC i środowiska oprogramowania Apache Thrift w usłudze Azure HDInsight zostanie wyświetlony komunikat o błędzie podobny do poniższego:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Przyczyna

Ten wyjątek jest spowodowany przez proces serializacji próbujący użyć większej ilości miejsca w buforze niż dozwolona. W przypadku platformy Spark 2.0.0 Klasa `org.apache.spark.serializer.KryoSerializer` jest używana do serializacji obiektów, gdy dostęp do danych odbywa się za pomocą platformy Apache Thrift oprogramowania. Inna klasa jest używana w przypadku danych, które będą wysyłane za pośrednictwem sieci lub w pamięci podręcznej w postaci serializowanej.

## <a name="resolution"></a>Rozdzielczość

Zwiększ wartość bufora `Kryoserializer`. Dodaj klucz o nazwie `spark.kryoserializer.buffer.max` i ustaw go na `2048` w konfiguracji spark2 w obszarze `Custom spark2-thrift-sparkconf`.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z [@AzureSupport](https://twitter.com/azuresupport) — oficjalne Microsoft Azure konto, aby usprawnić obsługę klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
