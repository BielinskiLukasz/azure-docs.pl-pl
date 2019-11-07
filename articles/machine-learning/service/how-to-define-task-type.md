---
title: Zdefiniuj zadanie uczenia maszynowego dla automatycznego przebiegu uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zdefiniować zadanie uczenia maszynowego dla automatycznego przebiegu uczenia maszynowego
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: c34a8e999267a77fa4aabbd9dc3a73b2c4f5a0ef
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647212"
---
# <a name="how-to-define-a-machine-learning-task"></a>Jak zdefiniować zadanie uczenia maszynowego 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ten artykuł zawiera informacje o obsługiwanych zadaniach uczenia maszynowego i sposobach ich definiowania na potrzeby uruchamiania eksperymentu automatycznego uczenia maszynowego.


## <a name="What is a machine learning task?"></a>Co to jest zadanie uczenia maszynowego?

Zadanie uczenia maszynowego reprezentuje typ problemu, który należy rozwiązać przez utworzenie modelu predykcyjnego. Zautomatyzowana ML obsługuje trzy różne typy zadań, takich jak Klasyfikacja, regresja i prognozowanie szeregów czasowych.

Typ zadania| Opis| Przykład
----|----|----
Klasyfikacja | Zadanie do przewidywania kategorii określonego wiersza w zestawie danych. | Wykrywanie oszustw na karcie kredytowej. W kolumnie Target zostałyby **wykryte oszustwo** z kategoriami *true* lub *false*. W tym przypadku klasyfikujemy każdy wiersz danych jako wartość true lub false.
Ubytk | Zadanie do przewidywania ciągłej ilości danych wyjściowych. | Koszt samochodów w oparciu o funkcje, kolumna docelowa to **Cena**.
Prognozowanie |Zadanie do wykonywania świadomych szacunków w zakresie określania kierunku przyszłych trendów.| Prognozowanie zapotrzebowania na energię na kolejne 48 godzin. Kolumna docelowa będzie **na żądanie** , a przewidywane wartości byłyby używane do wyświetlania wzorców zapotrzebowania na energię.

Automatyczna ML obsługuje następujące algorytmy w trakcie procesu automatyzacji i dostrajania. Jako użytkownik nie ma potrzeby określania algorytmu.

Klasyfikacja | Ubytk | Prognozowanie szeregów czasowych
-- |-- |--
[Regresja logistyczna](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Sieć elastyczna](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Sieć elastyczna](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Lekki GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Lekki GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Lekki GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Zwiększanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Zwiększanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Zwiększanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#regression)|[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#regression)
[Najbliższe sąsiednie sąsiedzi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[Najbliższe sąsiednie sąsiedzi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[Najbliższe sąsiednie sąsiedzi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Liniowy SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C — Klasyfikacja wektorów pomocy technicznej (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Stochastycznego gradientu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Stochastycznego gradientu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Las losowy](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Las losowy](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Las losowy](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Skrajnie losowe drzewa](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Skrajnie losowe drzewa](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Skrajnie losowe drzewa](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Klasyfikator DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN — klasyfikator liniowy](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regresor liniowy](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regresor liniowy](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Algorytm Bayesa Bayesa](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[Stochastycznego gradientu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>Ustawianie typu zadania
Możesz ustawić typ zadania dla zautomatyzowanych eksperymentów ML przy użyciu zestawu SDK lub Azure Machine Learning Studio.

Użyj parametru `task` w konstruktorze `AutoMLConfig`, aby określić typ eksperymentu.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

Możesz ustawić zadanie jako część zautomatyzowanego tworzenia przebiegu eksperymentu ML w Azure Machine Learning Studio. 

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Wybór typu zadania](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>Następne kroki

+ Dowiedz się więcej o [zautomatyzowanych ml](concept-automated-ml.md) w Azure Machine Learning.
+ Dowiedz się więcej o [autoszkoleniu modelu prognozowania szeregów czasowych](how-to-auto-train-forecast.md) w Azure Machine Learning
+ Wypróbuj samouczek [zautomatyzowanej klasyfikacji Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .
+ Wypróbuj przykładowy Notes [Machine Learning regresji automatycznej](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .

