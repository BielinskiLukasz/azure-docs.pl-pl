---
title: Optymalizowanie zapytań programu Hive w usłudze Azure HDInsight
description: W tym artykule opisano sposób optymalizowania Apache Hive zapytań dotyczących usługi Hadoop w usłudze HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 144d51d08a61526ec0f183a63e1fdf5658136293
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382111"
---
# <a name="optimize-apache-hive-queries-in-azure-hdinsight"></a>Optymalizowanie Apache Hive zapytań w usłudze Azure HDInsight

W usłudze Azure HDInsight istnieje kilka typów klastrów i technologii, które mogą uruchamiać Apache Hive zapytań. Podczas tworzenia klastra usługi HDInsight wybierz odpowiedni typ klastra, aby pomóc zoptymalizować wydajność dla potrzeb obciążenia.

Na przykład wybierz pozycję **interaktywna kwerenda** typ klastra, aby zoptymalizować zapytania ad hoc. Wybierz typ klastra Apache **Hadoop** , aby zoptymalizować zapytania Hive używane jako proces wsadowy. Typy klastrów **Spark** i **HBase** mogą również uruchamiać zapytania programu Hive. Aby uzyskać więcej informacji na temat uruchamiania zapytań programu Hive w różnych typach klastrów usługi HDInsight, zobacz [co to jest Apache Hive i HiveQL w usłudze Azure HDInsight?](hadoop/hdinsight-use-hive.md).

Klastry usługi HDInsight typu klastra Hadoop nie są domyślnie zoptymalizowane pod kątem wydajności. W tym artykule opisano niektóre typowe metody optymalizacji wydajności Hive, które można zastosować do zapytań.

## <a name="scale-out-worker-nodes"></a>Skalowanie węzłów procesu roboczego

Zwiększenie liczby węzłów procesu roboczego w klastrze usługi HDInsight pozwala pracować równolegle z większymi mapowaniami i zmniejszami. Istnieją dwa sposoby zwiększenia skalowania w poziomie w usłudze HDInsight:

* Podczas tworzenia klastra można określić liczbę węzłów procesu roboczego przy użyciu Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia.  Więcej informacji można znaleźć w artykule [Create HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md) (Tworzenie klastrów usługi HDInsight). Poniższy zrzut ekranu przedstawia konfigurację węzła procesu roboczego na Azure Portal:
  
    ![Azure Portal węzły rozmiaru klastra](./media/hdinsight-hadoop-optimize-hive-query/azure-portal-cluster-configuration.png "scaleout_1")

* Po utworzeniu można także edytować liczbę węzłów procesu roboczego, aby dodatkowo skalować klaster bez ponownego tworzenia:

    ![Azure Portal skalowanie rozmiaru klastra](./media/hdinsight-hadoop-optimize-hive-query/azure-portal-settings-nodes.png "scaleout_2")

Aby uzyskać więcej informacji na temat skalowania usługi HDInsight, zobacz [skalowanie klastrów usługi HDInsight](hdinsight-scaling-best-practices.md) .

## <a name="use-apache-tez-instead-of-map-reduce"></a>Użyj Apache Tez zamiast ograniczenia mapy

[Apache tez](https://tez.apache.org/) to alternatywny aparat wykonywania dla aparatu MapReduce. Klastry HDInsight oparte na systemie Linux mają domyślnie włączone tez.

![Diagram omówienia Apache Tez usługi HDInsight](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-tez-engine.png)

Tez jest szybszy, ponieważ:

* **Wykonaj ukierunkowany wykres acykliczne (DAG) jako pojedyncze zadanie w aparacie MapReduce**. DAG wymaga, aby każdy zestaw funkcji mapowania, po którym następuje jeden zestaw elementów ograniczających. Powoduje to, że wiele zadań MapReduce jest wyłączonych dla każdego zapytania programu Hive. Tez nie ma takiego ograniczenia i może przetwarzać złożone DAG jako jedno zadanie, co minimalizuje obciążenie uruchomienia zadania.
* **Unika niepotrzebnych operacji zapisu**. Do przetwarzania tego samego zapytania Hive w aparacie MapReduce są używane wiele zadań. Dane wyjściowe każdego zadania MapReduce są zapisywane w systemie plików HDFS dla danych pośrednich. Ponieważ tez minimalizuje liczbę zadań dla każdego zapytania programu Hive, można uniknąć niepotrzebnych operacji zapisu.
* **Minimalizuje opóźnienia uruchamiania**. Tez jest lepszym rozwiązaniem w celu zminimalizowania opóźnień uruchamiania przez zmniejszenie liczby wymaganych do uruchomienia, a także usprawnienia optymalizacji.
* Ponownie **używa kontenerów**. Zawsze, gdy to możliwe, tez jest w stanie ponownie używać kontenerów, aby zapewnić skrócenie opóźnienia z powodu początkowych kontenerów.
* **Techniki ciągłej optymalizacji**. Tradycyjna Optymalizacja została wykonana podczas fazy kompilacji. Dostępne są jednak więcej informacji na temat danych wejściowych umożliwiających lepszą optymalizację w czasie wykonywania. Tez używa technik ciągłej optymalizacji, które umożliwiają jej dalsze Optymalizowanie do fazy czasu wykonywania.

Aby uzyskać więcej informacji na temat tych koncepcji, zobacz [Apache tez](https://tez.apache.org/).

Możesz wprowadzić dowolne tez zapytania Hive przez dodanie prefiksu zapytania przy użyciu następującego polecenia Set:

```hive
set hive.execution.engine=tez;
```

## <a name="hive-partitioning"></a>Partycjonowanie Hive

Operacje we/wy są głównymi wąskimi gardłami wydajności do uruchamiania zapytań programu Hive. Wydajność można ulepszyć, jeśli ilość danych, które muszą zostać odczytane, może zostać zmniejszona. Domyślnie zapytania programu Hive są skanowane w całej tabeli programu Hive. Jednak w przypadku zapytań, które wymagają tylko skanowania niewielkiej ilości danych (na przykład zapytania z filtrowaniem), to zachowanie powoduje utworzenie niepotrzebnych kosztów. Partycjonowanie Hive umożliwia kwerendom programu Hive dostęp do wymaganej ilości danych w tabelach Hive.

Partycjonowanie Hive jest implementowane przez reorganizację danych pierwotnych do nowych katalogów. Każda partycja ma swój własny katalog plików. Partycjonowanie jest definiowane przez użytkownika. Na poniższym diagramie przedstawiono partycjonowanie tabeli programu Hive według kolumny *Year*. Nowy katalog jest tworzony dla każdego roku.

![Partycjonowanie Apache Hive usługi HDInsight](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-partitioning.png)

Niektóre zagadnienia dotyczące partycjonowania:

* **Nie można podzielić** na partycje w kolumnach zawierających tylko kilka wartości może spowodować kilka partycji. Na przykład partycjonowanie na płeć tworzy tylko dwie partycje, które mają zostać utworzone (samce i kobieta), co pozwala skrócić czas oczekiwania tylko o maksymalnie połowę.
* **Nie przekraczaj partycji** — w drugiej skrajnej sytuacji utworzenie partycji w kolumnie z unikatową wartością (na przykład UserID) powoduje wystąpienie wielu partycji. Za pośrednictwem partycji powstaje wiele naprężenia namenode klastra, ponieważ musi on obsługiwać dużą liczbę katalogów.
* **Unikanie pochylania danych** — wybierz swój klucz partycjonowania, tak aby wszystkie partycje miały rozmiar nawet. Na przykład partycjonowanie w kolumnie *State* może spowodować pochylenie dystrybucji danych. Ze względu na to, że stan Kalifornii jest niemal 30x z Vermont, rozmiar partycji jest potencjalnie skośny, a wydajność może się nieco różnić.

Aby utworzyć tabelę partycji, użyj *podzielonej na partycje* klauzuli:

```sql
CREATE TABLE lineitem_part
      (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT STRING)
PARTITIONED BY(L_SHIPDATE STRING)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
STORED AS TEXTFILE;
```

Po utworzeniu tabeli partycjonowanej można utworzyć partycjonowanie statyczne lub partycjonowanie dynamiczne.

* **Partycjonowanie statyczne** oznacza, że dane są już podzielonej na fragmenty w odpowiednich katalogach. Partycje statyczne umożliwiają dodawanie partycji Hive ręcznie na podstawie lokalizacji katalogu. Poniższy fragment kodu jest przykładem.
  
   ```sql
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   SELECT * FROM lineitem
   WHERE lineitem.L_SHIPDATE = '5/23/1996 12:00:00 AM'

   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   LOCATION 'wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```

* **Partycjonowanie dynamiczne** polega na tym, że program Hive ma automatycznie tworzyć partycje. Ponieważ tabela partycjonowania została już utworzona z tabeli przemieszczania, Wystarczy wstawić dane do tabeli partycjonowanej:
  
   ```hive
   SET hive.exec.dynamic.partition = true;
   SET hive.exec.dynamic.partition.mode = nonstrict;
   INSERT INTO TABLE lineitem_part
   PARTITION (L_SHIPDATE)
   SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY,
       L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
       L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
       L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as L_RETURNFLAG,
       L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as L_SHIPDATE_PS,
       L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as L_RECEIPTDATE,
       L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as L_SHIPMODE,
       L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;
   ```

Aby uzyskać więcej informacji, zobacz [partycjonowane tabele](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>Użyj formatu ORCFile

Program Hive obsługuje różne formaty plików. Na przykład:

* **Tekst**: domyślny format pliku i działa z większością scenariuszy.
* **Avro**: sprawdza się najlepiej w scenariuszach współdziałania.
* **Orc/Parquet**: najlepiej dopasowane do wydajności.

Format ORC (zoptymalizowany wiersz kolumnowy) to wysoce wydajny sposób przechowywania danych programu Hive. W porównaniu do innych formatów ORC ma następujące zalety:

* Obsługa złożonych typów, w tym typów DateTime i złożonych oraz z częściową strukturą.
* do 70% kompresji.
* indeksuje co 10 000 wierszy, które zezwalają na pomijanie wierszy.
* znacząca porzucanie w czasie wykonywania.

Aby włączyć format ORC, należy najpierw utworzyć tabelę z klauzulą *przechowywaną jako Orc*:

```sql
CREATE TABLE lineitem_orc_part
      (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
PARTITIONED BY(L_SHIPDATE STRING)
STORED AS ORC;
```

Następnie wstawiasz dane do tabeli ORC z tabeli przemieszczania. Na przykład:

```sql
INSERT INTO TABLE lineitem_orc
SELECT L_ORDERKEY as L_ORDERKEY,
         L_PARTKEY as L_PARTKEY ,
         L_SUPPKEY as L_SUPPKEY,
         L_LINENUMBER as L_LINENUMBER,
         L_QUANTITY as L_QUANTITY,
         L_EXTENDEDPRICE as L_EXTENDEDPRICE,
         L_DISCOUNT as L_DISCOUNT,
         L_TAX as L_TAX,
         L_RETURNFLAG as L_RETURNFLAG,
         L_LINESTATUS as L_LINESTATUS,
         L_SHIPDATE as L_SHIPDATE,
         L_COMMITDATE as L_COMMITDATE,
         L_RECEIPTDATE as L_RECEIPTDATE,
         L_SHIPINSTRUCT as L_SHIPINSTRUCT,
         L_SHIPMODE as L_SHIPMODE,
         L_COMMENT as L_COMMENT
FROM lineitem;
```

Więcej informacji można znaleźć w formacie ORC w [podręczniku języka Apache Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Wektoryzacji

Wektoryzacji umożliwia Hive do przetwarzania partii wierszy 1024 razem zamiast przetwarzania jednego wiersza w czasie. Oznacza to, że proste operacje są wykonywane szybciej, ponieważ mniej wewnętrzny kod musi być uruchomiony.

Aby włączyć prefiks wektoryzacji zapytania programu Hive przy użyciu następującego ustawienia:

```hive
set hive.vectorized.execution.enabled = true;
```

Aby uzyskać więcej informacji, zobacz [wektorowe wykonywanie zapytań](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Inne metody optymalizacji

Istnieje więcej metod optymalizacji, które można uwzględnić na przykład:

* **Zasobnikowanie Hive:** technika, która umożliwia klastrowi lub segmentację dużych zestawów danych w celu zoptymalizowania wydajności zapytań.
* **Optymalizacja dołączania:** Optymalizacja planowania wykonania zapytania Hive w celu poprawy wydajności sprzężeń i zmniejszenia potrzeb użytkowników. Aby uzyskać więcej informacji, zobacz sekcję [Optymalizacja optymalizacji](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Zwiększ liczbę zmniejszeń**.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono kilka typowych metod optymalizacji zapytań Hive. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Używanie Apache Hive w usłudze HDInsight](hadoop/hdinsight-use-hive.md)
* [Analizowanie danych dotyczących opóźnień lotów przy użyciu interakcyjnych zapytań w usłudze HDInsight](/azure/hdinsight/interactive-query/interactive-query-tutorial-analyze-flight-data)
* [Analizowanie danych usługi Twitter przy użyciu Apache Hive w usłudze HDInsight](hdinsight-analyze-twitter-data-linux.md)
