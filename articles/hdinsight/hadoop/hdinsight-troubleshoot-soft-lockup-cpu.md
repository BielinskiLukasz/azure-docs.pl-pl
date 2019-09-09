---
title: USTERKa nietrwałego zablokowania błędów procesora CPU z klastra usługi Azure HDInsight
description: W dziennikach systemu Azure HDInsight występuje procesor CPU nietrwałego zablokowania błędów licznika wydajności
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/05/2019
ms.openlocfilehash: 618a871bee762900d4c99700cde42d759506b4b9
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810584"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Scenariusz: "licznik alarmowy: USTERKa: blokowanie miękkie — błąd procesora CPU z klastra usługi Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Dziennik systemowy jądra zawiera komunikat o błędzie: `watchdog: BUG: soft lockup - CPU`.

## <a name="cause"></a>Przyczyna

[Usterka](https://bugzilla.kernel.org/show_bug.cgi?id=199437) w jądrze systemu Linux powoduje nietrwałe zawieszanie procesora.

## <a name="resolution"></a>Rozwiązanie

Zastosuj poprawkę jądra. Poniższy skrypt uaktualnia jądro systemu Linux i ponownie uruchamia maszyny w różnym czasie w ciągu 24 godzin. Wykonaj akcję skryptu w dwóch partiach. Pierwsza partia znajduje się na wszystkich węzłach poza węzłem głównym. Druga partia jest w węźle głównym. Nie uruchamiaj na węzłach głównych i w innych węzłach w tym samym czasie.

1. Przejdź do klastra usługi HDInsight z Azure Portal.

1. Przejdź do akcji skryptu.

1. Wybierz pozycję **Prześlij nowy** i wprowadź dane wejściowe w następujący sposób:

    | Właściwość | Value |
    | --- | --- |
    | Typ skryptu | -Niestandardowe |
    | Name |Poprawka problemu dotyczącego nieelastycznego blokowania jądra |
    | Identyfikator URI skryptu powłoki systemowej |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Typy węzłów |Proces roboczy, dozorcy |
    | Parametry |ND |

    Wybierz pozycję **Utrwalaj tę akcję skryptu..** ., jeśli chcesz wykonać skrypt po dodaniu nowych węzłów.

1. Wybierz pozycję **Utwórz**.

1. Poczekaj na pomyślne wykonanie.

1. Wykonaj akcję skryptu w węźle głównym, wykonując te same kroki co krok 3, ale tym razem z typami węzłów: MTP.

1. Poczekaj na pomyślne wykonanie.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) za pomocą — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
