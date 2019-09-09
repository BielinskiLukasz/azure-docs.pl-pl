---
title: Niska wydajność w Apache Hive LLAP zapytania w usłudze Azure HDInsight
description: Zapytania w Apache Hive LLAP są wykonywane wolniej niż oczekiwano w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: d05febbe6ba9e30ef46f5e961af155d37c76c205
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811367"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>Scenariusz: Niska wydajność w Apache Hive LLAP zapytania w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania interakcyjnych składników zapytań w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Domyślne konfiguracje klastrów nie są wystarczająco dostrojone dla obciążenia. Zapytania w usłudze Hive LLAP są wykonywane wolniej niż oczekiwano.

## <a name="cause"></a>Przyczyna

Przyczyną może być wiele przyczyn.

## <a name="resolution"></a>Rozwiązanie

LLAP jest zoptymalizowany pod kątem zapytań obejmujących sprzężenia i agregacje. Zapytania podobne do następujących nie działają w interaktywnym klastrze Hive:

```
select * from table where column = "columnvalue"
```

Aby poprawić wydajność zapytań punktów w programie Hive LLAP, ustaw następujące konfiguracje:

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

Możesz również zwiększyć użycie pamięci podręcznej LLAP, aby zwiększyć wydajność przy użyciu następującej zmiany konfiguracji:

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) za pomocą — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
