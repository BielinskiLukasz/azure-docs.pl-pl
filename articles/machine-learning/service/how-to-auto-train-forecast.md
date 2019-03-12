---
title: Auto-train model prognozy
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać usługi Azure Machine Learning to w opracowywaniu prognozowania modelu regresji przy użyciu zautomatyzowanych machine learning.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 03/08/2019
ms.openlocfilehash: 7c34040180cd7c6b635d55e59498908b1373ae1b
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57786619"
---
# <a name="auto-train-a-forecast-model"></a>Auto-train model prognozy

W tym artykule przedstawiono to w opracowywaniu prognozowania modelu regresji przy użyciu zautomatyzowanych usługi machine learning w usłudze Azure Machine Learning. Konfigurowanie modelu prognozowania jest podobny do konfigurowania modelu regresji standard za pomocą automatycznych machine learning, ale niektórych opcjami i wstępne przetwarzanie kroki konfiguracji istnieje do pracy z danymi szeregów czasowych. Poniższe przykłady pokazują sposób do:

* Przygotowuje dane do modelowania serii czasu
* Konfigurowanie określone parametry szeregów czasowych w [ `AutoMLConfig` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) obiektu
* Uruchom prognoz z danymi szeregów czasowych

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby utworzyć obszar roboczy, zobacz [wprowadzenie do usługi Azure Machine Learning](quickstart-get-started.md).
* W tym artykule przyjęto założenie, podstawowe znajomość konfigurowania automatycznych eksperymentu uczenia maszynowego. Postępuj zgodnie z [samouczek](tutorial-auto-train-models.md) lub [porad](how-to-configure-auto-train.md) Aby wyświetlić podstawowe maszyny automatycznych wzorce projektowe eksperymentu uczenia.

## <a name="preparing-data"></a>Trwa przygotowywanie danych

Najważniejsza różnica między prognozowania typ zadania regresji i regresji typu zadania w ramach uczenia maszynowego automatycznych obejmują funkcję danych, która reprezentuje szeregów czasowych prawidłowe. Serie czasu ma częstotliwość dobrze zdefiniowany i spójny i ma wartość w każdym punkcie przykładowe przedział czasu ciągłe. Należy wziąć pod uwagę następującej migawki pliku `sample.csv`.

    week_starting,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/10/2018,A,2300,37
    9/10/2018,B,550,37
    9/17/2018,A,2100,38
    9/17/2018,B,650,38
    9/24/2018,A,2400,39
    9/24/2018,B,700,39
    10/1/2018,A,2450,40
    10/1/2018,B,650,40

Ten zestaw danych jest prosty przykład tygodniowe dane sprzedaży dla firmy, która ma dwa różne sklepy A i B. Dodatkowo, jest funkcji `week_of_year` modelu do wykrywania sezonowości co tydzień, które pozwolą. Pole `week_starting` reprezentuje szeregów czasowych czyste z częstotliwością tygodniową i polem `sales_quantity` jest kolumna docelowa dla wykonywania prognoz. Odczyt danych do elementów Pandas dataframe, a następnie użyj `to_datetime` funkcję, aby upewnić się, jest szeregów czasowych `datetime` typu.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["week_starting"] = pd.to_datetime(data["week_starting"])
```

W tym przypadku dane są już sortowane rosnąco przez wartość pola Czas `week_starting`. Jednak podczas konfigurowania eksperymentu, upewnij się, że kolumna żądany czas jest posortowanej rosnąco tworzenie szeregów czasowych prawidłowe. Przyjęto założenie, że dane zawierają 1000 rekordów i deterministyczna podziału w danych, tworzenie, szkolenie i testowanie zestawów danych. Następnie oddzielić pola docelowego `sales_quantity` w celu tworzenia prognoz szkolenie i testowanie zestawów.

```python
X_train = data.iloc[:950]
X_test = data.iloc[50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
```

> [!NOTE]
> Podczas uczenia modelu do prognozowania przyszłych wartości, upewnij się, wszystkie funkcje używane szkolenia i mogą być używane podczas wykonywania prognoz dla Twojego zamierzony horyzoncie. Na przykład podczas tworzenia prognozę, funkcji bieżąca cena akcji w tym może wysoce zwiększyć dokładność szkolenia. Jednak jeśli zamierzasz Prognozuj z długich horizon, nie może być możliwość dokładnego przewidywania przyszłego podstawowych wartości odpowiadające przyszłych punktów szeregów czasowych i dokładności modelu może to spowodować obniżenie.

## <a name="configure-experiment"></a>Konfigurowanie eksperymentu

Do prognozowania zadania, uczenie maszynowe automatyczne wykorzystuje przetwarzania wstępnego i Szacowanie czynności, które są specyficzne dla danych szeregów czasowych. Zostaną wykonane następujące kroki przetwarzania wstępnego:

* Wykrywanie szeregów czasowych przykładowy częstotliwość (np. co godzinę, codziennie, co tydzień) i tworzenia nowych rekordów dla braku punktami czasu zapewnienie ciągłej serii.
* Naliczenie brakuje wartości docelowej (za pośrednictwem wypełnienia do przodu) i kolumnami funkcji (przy użyciu wartości w środkowej kolumnie)
* Utwórz funkcje umożliwiające stały efekty w różnych seriach ziarna
* Tworzenie na podstawie czasu funkcji ułatwiających uczenia sezonowych wzorców
* Kodowanie podzielonych na kategorie Zmienne liczbowe ilości

`AutoMLConfig` Obiektu definiuje ustawienia i dane niezbędne dla zadania uczenia automatycznych maszyny. Podobnie jak problem regresji, należy zdefiniować szkolenia standardowe parametry, takie jak typ zadania, liczba iteracji, szkolenia, danych i liczba ocen wielu. Do prognozowania zadania, istnieją dodatkowe parametry, które musi być ustawiona, które wpływają na eksperyment. W poniższej tabeli opisano każdy parametr i sposób jej użycia.

| Param | Opis | Wymagane |
|-------|-------|-------|
|`time_column_name`|Służy do określania kolumn daty/godziny w danych wejściowych, używane do kompilowania szeregów czasowych i wnioskowanie jej częstotliwość.|✓|
|`grain_column_names`|Nazwy, definiując grupy poszczególnych serii danych wejściowych. Jeśli nie zdefiniowano ziarna, zestaw danych zakłada się, że jeden szeregów czasowych.||
|`max_horizon`|Maksymalną żądaną horizon prognozy w jednostkach częstotliwości szeregów czasowych.|✓|

Utwórz ustawienia Szeregi czasowe jako obiekt słownika. Ustaw `time_column_name` do `week_starting` pole w zestawie danych. Zdefiniuj `grain_column_names` parametru, aby upewnić się, że **dwa odrębne grupy szeregów czasowych** są tworzone dla zestawu danych; jeden dla magazynu, A i B. na koniec `max_horizon` 50 w celu przewidywania dla całego testu zestawu.

```python
time_series_settings = {
    "time_column_name": "week_starting",
    "grain_column_names": ["store"],
    "max_horizon": 50
}
```

Teraz Utwórz standardowy `AutoMLConfig` obiektu, określając `forecasting` typ zadania i przesyłanie eksperymentu. Po zakończeniu modelu, należy pobrać najlepiej wykonywania iteracji.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

> [!NOTE]
> Procedurę krzyżowego sprawdzania poprawności (CV) dane szeregów czasowych może naruszyć podstawowe założenia statystyczne strategii canonical zwijania K krzyżowa Weryfikacja, więc automatyczne uczeniu implementuje stopniowe procedurze weryfikacji źródła, aby utworzyć krzyżowa Weryfikacja złożeń danych szeregów czasowych. Aby użyć tej procedury, należy określić `n_cross_validations` parametru w `AutoMLConfig` obiektu. Można pominąć sprawdzanie poprawności i użycia własnego weryfikacji zestawów z `X_valid` i `y_valid` parametrów.

## <a name="forecasting-with-best-model"></a>Prognozowanie za pomocą najlepszy model

Użyj najlepsze iteracji modelu do prognozowania wartości dla zestawu danych testowych.

```python
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

Oblicz RMSE (błąd kwadrat oznacza główny) między `y_test` wartościami rzeczywistymi a przewidywane wartości w `y_pred`.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Teraz, gdy ogólnych dokładności modelu w ustaleniu, najbardziej realistyczne następnym krokiem jest użycie modelu do prognozowania nieznany przyszłych wartości. Po prostu podać wartość zestawu danych, w tym samym formacie co zestaw testów `X_test` , ale przyszłych Data/Godzina i prognozowania Wynikowy zestaw jest przewidywane wartości dla każdego kroku szeregów czasowych. Przyjęto założenie, ostatnie rekordy szeregów czasowych w zestawie danych było uruchamianie tydzień 12, 31 stycznia 2018. Do prognozowania popytu w następnym tygodniu (lub dowolną liczbę okresów muszą prognozować, < = `max_horizon`), utworzyć pojedynczy czas serii rekord dla każdego sklepu uruchamiania tydzień 07/01/2019.

    week_starting,store,week_of_year
    01/07/2019,A,2
    01/07/2019,A,2

Powtórz czynności niezbędnych do załadowania tych danych przyszłych ramkę danych, a następnie uruchom `best_run.predict(X_test)` do przewidywania przyszłych wartości.

> [!NOTE]
> Wartości nie można przewidzieć liczbę okresów większa `max_horizon`. Model musi być ponownie uczony przy użyciu większych horizon do przewidywania przyszłych wartości poza bieżącym horyzoncie.

## <a name="next-steps"></a>Kolejne kroki

* Postępuj zgodnie z [samouczek](tutorial-auto-train-models.md) dowiesz się, jak tworzenie eksperymentów przy użyciu zautomatyzowanego uczenia maszynowego.
* Widok [Azure Machine Learning SDK dla języka Python](https://aka.ms/aml-sdk) dokumentację referencyjną.
