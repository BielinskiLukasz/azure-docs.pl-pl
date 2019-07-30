---
title: 'Samouczek: Tworzenie modelu klastrowania w języku R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: W drugiej części tej serii samouczków z trzema częściami utworzysz model K-oznaczający, że w języku R zostanie utworzona usługa klastrowania z Azure SQL Database Machine Learning Services (wersja zapoznawcza).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 07/29/2019
ms.openlocfilehash: 9f16ebc5acff7bbccc9de28e2fab0d223c6e244b
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640017"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Samouczek: Tworzenie modelu klastrowania w języku R z Azure SQL Database Machine Learning Services (wersja zapoznawcza)

W drugiej części tej serii samouczków z trzema częściami utworzysz model K-oznaczający, że w języku R zostanie wykonane tworzenie klastrów. W następnej części tej serii ten model zostanie wdrożony w bazie danych SQL za pomocą Azure SQL Database Machine Learning Services (wersja zapoznawcza).

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Zdefiniuj liczbę klastrów dla algorytmu K-oznacza
> * Wykonaj klaster
> * Analizowanie wyników

W [części pierwszej](sql-database-tutorial-clustering-model-prepare-data.md)przedstawiono sposób przygotowania danych z bazy danych Azure SQL Database w celu przeprowadzenia klastrowania.

W [trzeciej części](sql-database-tutorial-clustering-model-deploy.md)dowiesz się, jak utworzyć procedurę przechowywaną w bazie danych Azure SQL Database, która może wykonywać klastry w języku R na podstawie nowych danych.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* W drugiej części tego samouczka założono, że została wykonana [**część pierwszej**](sql-database-tutorial-clustering-model-prepare-data.md) i jej wymagania wstępne.

## <a name="define-the-number-of-clusters"></a>Zdefiniuj liczbę klastrów

Aby Klastrować dane klienta, należy użyć algorytmu klastrowania **K-oznaczanie** , jednego z najprostszych i najbardziej znanych sposobów grupowania danych.
Więcej informacji na temat K-oznacza, że w [kompletnym przewodniku do k-oznacza algorytm klastrowania](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

Algorytm akceptuje dwa dane wejściowe: Same dane i wstępnie zdefiniowany numer "*k*" reprezentujący liczbę klastrów do wygenerowania.
Dane wyjściowe to *k* klastrów z danymi wejściowymi partycjonowanymi między klastrami.

Aby określić liczbę klastrów używanych przez algorytm, użyj wykresu w obrębie grup sum kwadratów według liczby wyodrębnionych klastrów. Odpowiednia liczba klastrów do użycia to zgięcie lub "kątowe" wykresu.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Wykres łamany](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

Na podstawie grafu, wygląda na to, że *k = 4* byłyby dobrą wartością do wypróbowania. Ta wartość *k* umożliwi grupowanie klientów w czterech klastrach.

## <a name="perform-clustering"></a>Wykonaj klaster

W poniższym skrypcie języka R zostanie użyta funkcja **rxKmeans**, która jest funkcją K-oznacza w pakiecie kolekcję funkcji revoscaler.

```r
# Output table to hold the customer group mappings.
# This is a table where the cluster mappings will be saved in the database.
return_cluster = RxSqlServerData(table = "return_cluster", connectionString = connStr);
# Set the seed for the random number generator for predictability
set.seed(10);
# Generate clusters using rxKmeans and output key / cluster to a table in SQL database
# called return_cluster
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters=4,
                   outFile=return_cluster,
                   outColName="cluster",
                   extraVarsToWrite=c("customer"),
                   overwrite=TRUE);

# Read the customer returns cluster table from the database
customer_cluster <- rxDataStep(return_cluster);
```

## <a name="analyze-the-results"></a>Analizowanie wyników

Teraz, gdy klaster został już wykonany przy użyciu K-oznacza, następnym krokiem jest przeanalizowanie wyniku i sprawdzenie, czy można znaleźć dowolne informacje umożliwiające podjęcie odpowiednich działań.

Obiekt **clust** zawiera wyniki z K-oznacza klastrowanie.

```r
#Look at the clustering details to analyze results
clust
```

```results
Call:
rxKmeans(formula = ~orderRatio + itemsRatio + monetaryRatio + 
    frequency, data = customer_returns, outFile = return_cluster, 
    outColName = "cluster", extraVarsToWrite = c("customer"), 
    overwrite = TRUE, numClusters = 4)
Data: customer_returns
Number of valid observations: 37336
Number of missing observations: 0 
Clustering algorithm:  

K-means clustering with 4 clusters of sizes 31675, 671, 2851, 2139
Cluster means:
   orderRatio   itemsRatio monetaryRatio frequency
1 0.000000000 0.0000000000    0.00000000  0.000000
2 0.007451565 0.0000000000    0.04449653  4.271237
3 1.008067345 0.2707821817    0.49515232  1.031568
4 0.000000000 0.0004675082    0.10858272  1.186068
Within cluster sum of squares by cluster:
         1          2          3          4
    0.0000  1329.0160 18561.3157   363.2188
```

Cztery klastry są określane przy użyciu zmiennych zdefiniowanych w [części pierwszej](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers):

* *orderRatio* = współczynnik zwrotnego zamówienia (całkowita liczba zamówień częściowo lub w pełni zwróconych w stosunku do łącznej liczby zamówień)
* *itemsRatio* = współczynnik elementu powrotu (całkowita liczba zwracanych elementów w porównaniu z liczbą zakupionych elementów)
* *monetaryRatio* = współczynnik kwoty zwrotu (całkowita kwota pieniężna zwróconych elementów w porównaniu z zakupioną ilością)
* *częstotliwość* = częstotliwość powrotu

Wyszukiwanie danych przy użyciu K-oznacza często wymaga dalszej analizy wyników, a także dalsze kroki, aby lepiej zrozumieć każdy klaster, ale może to zapewnić pewne dobre potencjalnego klienta.
Oto kilka sposobów interpretacji tych wyników:

* Klaster 1 (największy klaster) prawdopodobnie jest grupą klientów, którzy nie są aktywni (wszystkie wartości są równe zero).
* Klaster 3 prawdopodobnie jest grupą, która znajduje się w odniesieniu do zachowania zwrotnego.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

***Jeśli nie chcesz kontynuować pracy z tym samouczkiem***, Usuń bazę danych tpcxbb_1gb z serwera Azure SQL Database.

W Azure Portal wykonaj następujące kroki:

1. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** lub **bazy danych SQL**.
1. W polu **Filtruj według nazwy...** wpisz **tpcxbb_1gb**i wybierz swoją subskrypcję.
1. Wybierz bazę danych **tpcxbb_1gb** .
1. Na stronie **Przegląd** wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W drugiej części tej serii samouczków zostały wykonane następujące czynności:

* Zdefiniuj liczbę klastrów dla algorytmu K-oznacza
* Wykonaj klaster
* Analizowanie wyników

Aby wdrożyć utworzony model uczenia maszynowego, wykonaj trzecią część tej serii samouczków:

> [!div class="nextstepaction"]
> [Samouczek: Wdrażanie modelu klastrowania w języku R z Azure SQL Database Machine Learning Services (wersja zapoznawcza)](sql-database-tutorial-clustering-model-deploy.md)