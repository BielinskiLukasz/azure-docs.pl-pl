---
title: Problemy z łącznością Apache Phoenix w usłudze Azure HDInsight
description: Problemy z łącznością między programem Apache HBase i Apache Phoenix w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: b886f51bcb2bb7308c49c76563dcb70148bbc583
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887295"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Scenariusz: Apache Phoenix problemy z łącznością w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Nie można nawiązać połączenia z usługą Apache HBase z Apache Phoenix. Przyczyny mogą się różnić.

## <a name="cause-incorrect-ip"></a>Przyczyna: nieprawidłowy adres IP

Nieprawidłowy adres IP aktywnego węzła dozorcy.

### <a name="resolution"></a>Rozdzielczość

Adres IP aktywnego węzła dozorcy można zidentyfikować z poziomu interfejsu użytkownika Ambari, postępując zgodnie z linkami do **HBase** > **szybkie linki** > **ZK (Active)**  > **dozorcy info**. W razie konieczności popraw adres IP.

---

## <a name="cause-systemcatalog-table-offline"></a>Przyczyna: SYSTEM. Tabela wykazu w trybie offline

Podczas uruchamiania poleceń, takich jak `!tables`, zostanie wyświetlony komunikat o błędzie podobny do:

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

Podczas uruchamiania poleceń, takich jak `count 'SYSTEM.CATALOG'`, zostanie wyświetlony komunikat o błędzie podobny do:

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Rozdzielczość

W interfejsie użytkownika Apache Ambari wykonaj następujące kroki, aby ponownie uruchomić usługę serwera hmaster na wszystkich węzłach dozorcy:

1. W sekcji **Podsumowanie** HBase przejdź do **HBase** > **aktywne HBase Master**.

1. W sekcji **składniki** Uruchom ponownie usługę HBase Master.

1. Powtórz te kroki dla wszystkich pozostałych usług **HBase Master w stanie wstrzymania** .

Zastabilizacja i zakończenie odzyskiwania przez usługę HBase Master może potrwać do 5 minut. Po powrocie do normalnej tabeli `SYSTEM.CATALOG` problem z łącznością Apache Phoenix powinien zostać rozwiązany automatycznie.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego Microsoft Azure konta, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
