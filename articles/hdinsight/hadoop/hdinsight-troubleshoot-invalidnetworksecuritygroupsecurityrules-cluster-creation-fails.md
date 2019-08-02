---
title: InvalidNetworkSecurityGroupSecurityRules — Tworzenie klastra kończy się niepowodzeniem w usłudze Azure HDInsight
description: Tworzenie klastra kończy się niepowodzeniem z błędem ErrorCode InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/31/2019
ms.openlocfilehash: 35e583fa31ee99c9d6307c01287c2c0fde021280
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700615"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Scenariusz: InvalidNetworkSecurityGroupSecurityRules — Tworzenie klastra kończy się niepowodzeniem w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Kod `InvalidNetworkSecurityGroupSecurityRules` błędu jest wyświetlany z opisem podobnym do "reguły zabezpieczeń w sieciowej grupie zabezpieczeń skonfigurowanej z podsiecią, nie zezwala na dostęp do wymaganych połączeń przychodzących i/lub wychodzących".

## <a name="cause"></a>Przyczyna

Przyczyną jest problem z regułami [sieciowej grupy zabezpieczeń](../../virtual-network/virtual-network-vnet-plan-design-arm.md) dla ruchu przychodzącego skonfigurowanych dla klastra.

## <a name="resolution"></a>Rozwiązanie

Przejdź do Azure Portal i zidentyfikuj sieciowej grupy zabezpieczeń, która jest skojarzona z podsiecią, w której jest wdrażany klaster. W sekcji **reguły zabezpieczeń ruchu przychodzącego** upewnij się, że reguły zezwalają na dostęp przychodzący do portu 443 dla adresów IP wymienionych w [tym miejscu](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip).

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) za pomocą — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
