---
title: 'Samouczek: wprowadzenie do analizy przy użyciu platformy Spark'
description: W tym samouczku przedstawiono podstawowe kroki konfigurowania usługi Azure Synapse Analytics i korzystania z niej.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 5c6b35c1d9f00cae8fc688569e3a491679900995
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101608"
---
# <a name="analyze-with-apache-spark"></a>Analizowanie za pomocą Apache Spark

W tym samouczku przedstawiono podstawowe kroki służące do ładowania i analizowania danych za pomocą Apache Spark dla usługi Azure Synapse.

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Ładowanie danych z NYC taksówki do bazy danych Spark nyctaxi

Dostępne są dane w tabeli w **SQLDB1**. Załaduj je do bazy danych Spark o nazwie **nyctaxi**.

1. W programie Synapse Studio przejdź do centrum **opracowywania** .
1. Wybierz pozycję **+**  >  **Notes**.
1. W górnej części notesu ustaw wartość **Dołącz do** na **Spark1**.
1. Wybierz pozycję **Dodaj kod** , aby dodać komórkę kodu notesu, a następnie wklej następujący tekst:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Przejdź do centrum **danych** , kliknij prawym przyciskiem myszy pozycję **bazy danych**, a następnie wybierz polecenie **Odśwież**. Powinny zostać wyświetlone następujące bazy danych:

    - **SQLDB1** (Pula SQL)
    - **nyctaxi** (Spark)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analizowanie danych z NYC taksówki przy użyciu platformy Spark i notesów

1. Wróć do notesu.
1. Utwórz nową komórkę kodu i wprowadź następujący tekst. Następnie uruchom komórkę, aby wyświetlić dane z NYC taksówki załadowane do bazy danych **nyctaxi** Spark.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Uruchom następujący kod, aby wykonać tę samą analizę, która była wcześniej z pulą SQL **SQLDB1**. Ten kod zapisuje wyniki analizy w tabeli o nazwie **nyctaxi. passengercountstats** i wizualizowa wyniki.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. W obszarze wyniki komórki wybierz pozycję **Wykres** , aby wyświetlić wizualizację danych.

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>Dostosowywanie wizualizacji danych za pomocą platformy Spark i notesów

Możesz kontrolować sposób renderowania wykresów za pomocą notesów. Poniższy kod przedstawia prosty przykład. Używa popularnych bibliotek **matplotlib** i **Seaborn**. Kod renderuje ten sam rodzaj wykresu liniowego jako zapytania SQL, które zostały wcześniej uruchomione.

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```



## <a name="load-data-from-a-spark-table-into-a-sql-pool-table"></a>Ładowanie danych z tabeli platformy Spark do tabeli puli SQL

Wcześniej skopiowano dane z tabeli puli SQL **SQLDB1. dbo. Trip** do tabeli Spark **nyctaxi. Trip**. Następnie używając platformy Spark, agregujemy dane do tabeli Spark **nyctaxi. passengercountstats**. Teraz skopiujemy dane z **nyctaxi. passengercountstats** do tabeli puli SQL o nazwie **SQLDB1. dbo. passengercountstats**.

Uruchom następującą komórkę w notesie. Kopiuje tabelę zagregowanych danych platformy Spark z powrotem do tabeli puli SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analizowanie danych w magazynie](get-started-analyze-storage.md)


