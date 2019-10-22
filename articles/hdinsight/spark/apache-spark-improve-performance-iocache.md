---
title: Apache Spark obciążenie wydajności za pomocą pamięci podręcznej we/wy usługi Azure HDInsight (wersja zapoznawcza)
description: Informacje o pamięci podręcznej we/wy usługi Azure HDInsight i sposobach ich użycia w celu poprawy wydajności Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2018
ms.openlocfilehash: ecb393ea1f64897f17ce73170da1673886ef8916
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "71266185"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache"></a>Poprawianie wydajności Apache Spark obciążeń przy użyciu pamięci podręcznej we/wy usługi Azure HDInsight

Pamięć podręczna we/wy to usługa buforowania danych dla usługi Azure HDInsight, która zwiększa wydajność Apache Spark zadań. Pamięć podręczna we/wy działa również w przypadku obciążeń [Apache tez](https://tez.apache.org/) i [Apache Hive](https://hive.apache.org/) , które można uruchamiać w klastrach [Apache Spark](https://spark.apache.org/) . Pamięć podręczna we/wy używa składnika pamięci podręcznej Open Source o nazwie RubiX. RubiX to lokalna pamięć podręczna dysku do użycia z aparatami analizy danych Big Data, które uzyskują dostęp do danych z systemów magazynu w chmurze. RubiX jest unikatowy w systemach buforowania, ponieważ używa dysków półprzewodnikowych (dysków SSD), a nie rezerwowej pamięci operacyjnej do celów buforowania. Usługa pamięci podręcznej we/wy uruchamia i zarządza serwerami metadanych RubiX w każdym węźle procesu roboczego klastra. Konfiguruje także wszystkie usługi klastra na potrzeby przezroczystego użycia pamięci podręcznej RubiX.

Większość dysków SSD zapewnia więcej niż 1 GByte na sekundę przepustowości. Ta przepustowość, dostosowana przez system operacyjny w pamięci podręcznej plików, zapewnia wystarczającą przepustowość do ładowania aparatów przetwarzania obliczeń danych Big Data, takich jak Apache Spark. Pamięć operacyjna jest dostępna do Apache Spark, aby przetwarzać silnie zależne od pamięci zadania, takie jak losowo. Korzystanie z wyłącznej pamięci operacyjnej pozwala Apache Spark uzyskać optymalne użycie zasobów.  

> [!Note]  
> Pamięć podręczna we/wy używa obecnie RubiX jako składnika buforowania, ale może się to zmienić w przyszłych wersjach usługi. Użyj interfejsów pamięci podręcznej we/wy i nie podejmuj żadnych zależności bezpośrednio w implementacji RubiX.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Zalety pamięci podręcznej we/wy usługi Azure HDInsight

Użycie pamięci podręcznej we/wy zapewnia wzrost wydajności zadań odczytywania danych z usługi Azure Blob Storage.

Nie musisz wprowadzać żadnych zmian w zadaniach platformy Spark, aby zobaczyć wzrost wydajności podczas korzystania z pamięci podręcznej we/wy. Gdy pamięć podręczna we/wy jest wyłączona, ten kod platformy Spark odczytuje dane zdalnie z usługi Azure Blob Storage: `spark.read.load('wasbs:///myfolder/data.parquet').count()`. Gdy pamięć podręczna we/wy jest aktywowana, ten sam wiersz kodu powoduje zbuforowane odczyty z pamięci podręcznej we/wy. Na następujących odczytach dane są odczytywane lokalnie z dysku SSD. Węzły procesu roboczego w klastrze usługi HDInsight są wyposażone w lokalne, dedykowane dyski SSD. Pamięć podręczna we/wy usługi HDInsight używa tych lokalnych dysków SSD do buforowania, co zapewnia najniższy poziom opóźnienia i maksymalizuje przepustowość.

## <a name="getting-started"></a>Wprowadzenie

Pamięć podręczna we/wy usługi HDInsight Azure jest domyślnie zdezaktywowana w wersji zapoznawczej. Pamięć podręczna we/wy jest dostępna w klastrach usługi Azure HDInsight 3.6 i Spark, w których działa Apache Spark 2,3.  Aby aktywować pamięć podręczną we/wy, wykonaj następujące czynności:

1. Wybierz klaster usługi HDInsight w [Azure Portal](https://portal.azure.com).

1. Na stronie **Przegląd** (domyślnie otwieranej po wybraniu klastra) wybierz pozycję **Ambari Home** w obszarze **pulpity nawigacyjne klastra**.

1. Wybierz usługę **pamięci podręcznej we/wy** po lewej stronie.

1. Wybierz pozycję **Akcje** i **Aktywuj**.

    ![Włączanie usługi pamięci podręcznej we/wy w Ambari](./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png "Włączanie usługi pamięci podręcznej we/wy w Ambari")

1. Potwierdź ponowne uruchomienie wszystkich usług, których dotyczy ten klaster.

>[!NOTE]  
> Mimo że pasek postępu pokazuje aktywowany, pamięć podręczna we/wy nie jest faktycznie włączona do momentu ponownego uruchomienia innych usług, których to dotyczy.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
  
Po włączeniu pamięci podręcznej we/wy może wystąpić błąd miejsca na dysku z uruchomionymi zadaniami platformy Spark. Te błędy występują, ponieważ platforma Spark używa również lokalnego magazynu dyskowego do przechowywania danych podczas operacji Shuffling. Gdy pamięć podręczna we/wy jest włączona, w usłudze Spark może być wolne miejsce, a miejsce na magazyn Spark jest ograniczone. Ilość miejsca używana przez pamięć podręczną we/wy jest domyślnie równa połowie całkowitego miejsca na dysku SSD. Użycie miejsca na dysku dla pamięci podręcznej we/wy można skonfigurować w Ambari. Jeśli wystąpią błędy miejsca na dysku, Zmniejsz ilość miejsca na dyski SSD używaną dla pamięci podręcznej we/wy i ponownie uruchom usługę. Aby zmienić zestaw miejsc dla pamięci podręcznej we/wy, wykonaj następujące czynności:

1. W obszarze Apache Ambari wybierz usługę **HDFS** po lewej stronie.

1. Wybierz pozycję **konfiguracje i** karty **Zaawansowane** .

    ![Edytuj zaawansowaną konfigurację systemu plików HDFS](./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png "Edytuj zaawansowaną konfigurację systemu plików HDFS")

1. Przewiń w dół i rozwiń obszar **niestandardowe Lokacje podstawowe** .

1. Znajdź właściwość **Hadoop. cache. Data. Full. PERCENTAGE**.

1. Zmień wartość w polu.

    ![Edytuj procent całkowitej ilości pamięci podręcznej we/wy](./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png "Edytuj procent całkowitej ilości pamięci podręcznej we/wy")

1. Wybierz pozycję **Zapisz** w prawym górnym rogu.

1. Wybierz pozycję **Uruchom ponownie**  > **ponownie uruchom wszystkie**.

    ![System Apache Ambari](./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png "Uruchom ponownie wszystkie uwzględnione")

1. Wybierz pozycję **Potwierdź ponowne uruchomienie wszystkich**.

Jeśli to nie zadziała, Wyłącz pamięć podręczną we/wy.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj więcej na temat pamięci podręcznej we/wy, włącznie z wynikami testów wydajności w tym wpisie w blogu: [Apache Spark zadania mogą uzyskać do 9X szybkość dzięki pamięci podręcznej usługi HDInsight](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/)
