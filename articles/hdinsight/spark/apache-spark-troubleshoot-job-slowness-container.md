---
title: Apache Spark wolno, gdy kontener usługi Azure Storage zawiera wiele plików — HDInsight
description: Zadanie Apache Spark działa wolno, gdy kontener usługi Azure Storage zawiera wiele plików w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/21/2019
ms.openlocfilehash: 40c5d023647d3592e44588fbc24bf2743da34373
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088625"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Uruchamianie zadania Apache Spark wolno, gdy kontener usługi Azure Storage zawiera wiele plików w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania składników Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Podczas uruchamiania klastra usługi HDInsight zadanie Apache Spark, które zapisuje w usłudze Azure Storage, działa wolno, gdy istnieje wiele plików/podfolderów. Na przykład trwa 20 sekund podczas zapisywania do nowego kontenera, ale około 2 minut podczas zapisywania do kontenera, który ma pliki 200 tys.

## <a name="cause"></a>Przyczyna

Jest to znany problem z platformą Spark. Spowolnienie pochodzi z `ListBlob` i `GetBlobProperties` operacji podczas wykonywania zadania Spark.

Aby śledzić partycje, platforma Spark musi obsługiwać `FileStatusCache` , która zawiera informacje o strukturze katalogów. Korzystając z tej pamięci podręcznej, platforma Spark może analizować ścieżki i wiedzieć o dostępnych partycjach. Zaletą śledzenia partycji jest to, że platforma Spark dotyka tylko niezbędnych plików podczas odczytywania danych. Aby zapewnić aktualność informacji, podczas pisania nowych danych platforma Spark musi wyświetlić listę wszystkich plików w katalogu i zaktualizować tę pamięć podręczną.

W platformie Spark 2,1, chociaż nie potrzebujemy aktualizacji pamięci podręcznej po każdym zapisie, platforma Spark sprawdzi, czy istniejąca kolumna partycji jest zgodna z proponowaną jedną w bieżącym żądaniu zapisu, więc będzie również prowadzić do tworzenia listy operacji na początku każdego zapisu.

W przypadku platformy Spark 2,2 podczas zapisywania danych przy użyciu trybu Append ten problem z wydajnością powinien zostać rozwiązany.

## <a name="resolution"></a>Rozwiązanie

Podczas tworzenia partycjonowanego zestawu danych należy użyć schematu partycjonowania, który będzie ograniczać liczbę plików, które mają być widoczne na liście, aby zaktualizować `FileStatusCache`.

Dla każdej n-partii, gdzie N% 100 = = 0 (100 jest tylko przykładem), Przenieś istniejące dane do innego katalogu, który może być ładowany przez platformę Spark.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) za pomocą — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
