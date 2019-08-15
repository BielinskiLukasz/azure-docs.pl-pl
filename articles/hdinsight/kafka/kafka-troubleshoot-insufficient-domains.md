---
title: Tworzenie klastra nie powiodło się z powodu niewystarczających domen błędów w regionie w usłudze Azure HDInsight
description: Tworzenie klastra nie powiodło się z powodu niewystarczających domen błędów w regionie w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/09/2019
ms.openlocfilehash: 945bd6aff5bba96f8b72570442d3b1dcd035cb62
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947710"
---
# <a name="scenario-cluster-creation-failed-due-to-not-sufficient-fault-domains-in-region-in-azure-hdinsight"></a>Scenariusz: Tworzenie klastra nie powiodło `not sufficient fault domains in region` się z powodu usługi Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Podczas próby utworzenia klastra Apache Kafka `not sufficient fault domains in region` należy otrzymać komunikat o błędzie podobny do tego.

## <a name="cause"></a>Przyczyna

Domena błędów to logiczna grupa bazowego sprzętu w centrum danych platformy Azure. Wszystkie domeny błędów korzystają ze wspólnego źródła zasilania i przełącznika sieciowego. Maszyny wirtualne i dyski zarządzane, które implementują węzły w klastrze usługi HDInsight są rozdzielone między te domeny błędów. Taka architektura ogranicza wpływ potencjalnych awarii sprzętu fizycznego.

W każdym regionie świadczenia usługi Azure znajduje się określona liczba domen błędów. Aby uzyskać listę domen i liczbę zawartych w nich domen błędów, zapoznaj się z dokumentacją dotyczącą [zestawów dostępności](../../virtual-machines/windows/manage-availability.md).

W usłudze HDInsight Klastry Kafka są wymagane do aprowizacji w regionie z co najmniej trzema domenami błędów.

## <a name="resolution"></a>Rozwiązanie

Jeśli region, w którym chcesz utworzyć klaster, nie ma wystarczających domen błędów, skontaktuj się z zespołem produktu, aby zezwolić na udostępnianie klastra, nawet jeśli nie ma trzech domen błędów.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) z programem — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
