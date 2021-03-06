---
title: Optymalizowanie oprogramowania Apache HBase z usługą Apache Ambari w usłudze Azure HDInsight
description: Użyj interfejsu użytkownika sieci Web Apache Ambari w celu skonfigurowania i zoptymalizowania oprogramowania Apache HBase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: b262e07bd07320e4b10b12a2f2cf07b97e58c61e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91821691"
---
# <a name="optimize-apache-hbase-with-apache-ambari-in-azure-hdinsight"></a>Optymalizowanie oprogramowania Apache HBase z usługą Apache Ambari w usłudze Azure HDInsight

Apache Ambari to interfejs sieci Web umożliwiający zarządzanie i monitorowanie klastrów usługi HDInsight. Wprowadzenie do interfejsu użytkownika sieci Web Ambari można znaleźć w temacie [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md).

Konfiguracja Apache HBase została zmodyfikowana z karty **configs** . W poniższych sekcjach opisano niektóre ważne ustawienia konfiguracji, które mają wpływ na wydajność HBase.

## <a name="set-hbase_heapsize"></a>Ustaw HBASE_HEAPSIZE

Rozmiar sterty HBase określa maksymalną ilość sterty, która ma być używana w megabajtach według *regionów* i serwerów *głównych* . Wartość domyślna to 1 000 MB. Ta wartość powinna być dostrojona dla obciążenia klastra.

1. Aby zmodyfikować, przejdź do okienka **Zaawansowane HBase-ENV** **na karcie konfiguracje** HBase, a następnie Znajdź `HBASE_HEAPSIZE` ustawienie.

1. Zmień wartość domyślną na 5 000 MB.

    !["Apache Ambari HBase Memory heapsize"](./media/optimize-hbase-ambari/ambari-hbase-heapsize.png)

## <a name="optimize-read-heavy-workloads"></a>Optymalizowanie obciążeń z dużą ilością odczytu

Następujące konfiguracje są ważne, aby poprawić wydajność obciążeń z dużą ilością odczytu.

### <a name="block-cache-size"></a>Rozmiar bloku pamięci podręcznej

Pamięć podręczna bloków jest pamięcią podręczną odczytu. Jego rozmiar jest kontrolowany przez `hfile.block.cache.size` parametr. Wartość domyślna to 0,4, czyli 40 procent całkowitej ilości pamięci serwera regionu. Im większy rozmiar pamięci podręcznej bloku, tym szybsze są odczyty losowe.

1. Aby zmodyfikować ten parametr, przejdź do karty **Ustawienia** na karcie HBase **configs (Konfiguracja** ), a następnie Znajdź **% RegionServer przydzieloną do buforów odczytu**.

    ![Rozmiar pamięci podręcznej blokowania pamięci Apache HBase](./media/optimize-hbase-ambari/hbase-block-cache-size.png)

1. Aby zmienić wartość, wybierz ikonę **edycji** .

### <a name="memstore-size"></a>Rozmiar magazynu

Wszystkie edycje są przechowywane w buforze pamięci o nazwie *magazynu*. Ten bufor zwiększa łączną ilość danych, które mogą być zapisywane na dysku w ramach jednej operacji. Przyspiesza również dostęp do ostatnich zmian. Rozmiar magazynu jest definiowany przez następujące dwa parametry:

* `hbase.regionserver.global.memstore.UpperLimit`: Określa maksymalny procent serwera regionu, który może być używany przez magazynu połączone.

* `hbase.regionserver.global.memstore.LowerLimit`: Określa minimalny procent serwera regionu, który może być używany przez magazynu połączone.

Aby zoptymalizować odczyty losowe, można zmniejszyć górną i dolną granicę magazynu.

### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Liczba wierszy pobranych podczas skanowania z dysku

`hbase.client.scanner.caching`Ustawienie definiuje liczbę wierszy odczytanych z dysku, gdy `next` Metoda jest wywoływana na skanerze.  Wartość domyślna to 100. Im większa liczba, tym mniej wywołań zdalnych wykonanych z klienta do serwera regionów, co skutkuje szybszymi skanami. Jednak to ustawienie spowoduje również zwiększenie poziomu wykorzystania pamięci na kliencie.

![Liczba pobranych wierszy Apache HBase](./media/optimize-hbase-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Nie ustawiaj wartości w taki sposób, aby czas między wywołaniem następnej metody na skanerze był większy niż limit czasu skanera. Czas trwania skanera jest definiowany przez `hbase.regionserver.lease.period` Właściwość.

## <a name="optimize-write-heavy-workloads"></a>Optymalizowanie dużych obciążeń zapisu

Następujące konfiguracje są ważne do poprawienia wydajności dużych obciążeń zapisu.

### <a name="maximum-region-file-size"></a>Maksymalny rozmiar pliku regionu

HBase przechowuje dane w wewnętrznym formacie pliku o nazwie *HFile*. Właściwość `hbase.hregion.max.filesize` definiuje rozmiar pojedynczego HFile dla regionu.  Region jest podzielony na dwa regiony, jeśli suma wszystkich HFiles w regionie jest większa niż to ustawienie.

!["Apache HBase HRegion max rozmiar pliku"](./media/optimize-hbase-ambari/hbase-hregion-max-filesize.png)

Im większy rozmiar pliku regionu, tym mniejsza jest liczba podziałów. Można zwiększyć rozmiar pliku, aby określić wartość, która powoduje maksymalną wydajność zapisu.

### <a name="avoid-update-blocking"></a>Unikaj blokowania aktualizacji

* Właściwość `hbase.hregion.memstore.flush.size` definiuje rozmiar, przy którym magazynu jest opróżniany na dysk. Domyślny rozmiar to 128 MB.

* Mnożnik bloku regionu HBase jest definiowany przez `hbase.hregion.memstore.block.multiplier` . Wartość domyślna to 4. Maksymalna dozwolona wartość to 8.

* HBase blokuje aktualizacje, jeśli magazynu to ( `hbase.hregion.memstore.flush.size`  *  `hbase.hregion.memstore.block.multiplier` ) b.

    Przy domyślnych wartościach rozmiaru opróżniania i mnożnika bloku aktualizacje są blokowane, gdy magazynu jest 128 * 4 = 512 MB. Aby zmniejszyć liczbę blokowania aktualizacji, zwiększ wartość `hbase.hregion.memstore.block.multiplier` .

![Mnożnik bloku regionu Apache HBase](./media/optimize-hbase-ambari/hbase-hregion-memstore-block-multiplier.png)

## <a name="define-memstore-size"></a>Zdefiniuj rozmiar magazynu

Rozmiar magazynu jest definiowany przez `hbase.regionserver.global.memstore.upperLimit` Parametry i `hbase.regionserver.global.memstore.lowerLimit` . Ustawienie tych wartości w taki sposób, że zmniejsza przerwy podczas operacji zapisu (co również powoduje częstsze opróżnianie) i skutkuje zwiększoną wydajnością zapisu.

## <a name="set-memstore-local-allocation-buffer"></a>Ustaw bufor przydziału lokalnego magazynu

Użycie magazynu lokalnego buforu alokacji jest określane przez właściwość `hbase.hregion.memstore.mslab.enabled` . Po włączeniu tego ustawienia zapobiega fragmentacji sterty podczas ciężkiej operacji zapisu. Wartością domyślną jest true.

![HBase. hregion. magazynu. mslab. Enabled](./media/optimize-hbase-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Interfejs API REST usługi Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Optymalizowanie klastrów](./hdinsight-changing-configs-via-ambari.md)
* [Optymalizowanie technologii Apache Hive](./optimize-hive-ambari.md)
* [Optymalizowanie platformy Apache Pig](./optimize-pig-ambari.md)
