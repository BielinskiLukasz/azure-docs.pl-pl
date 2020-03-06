---
title: Autouczenie modelu prognozowania szeregów czasowych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać Azure Machine Learning do uczenia modelu regresji prognozowania szeregów czasowych przy użyciu funkcji automatycznego uczenia maszynowego.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 93695b407762766b0bdd40c9d99a0bf4965f59a8
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78328783"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Autouczenie modelu prognozowania szeregów czasowych
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak szkolić model regresji prognozowania szeregów czasowych przy użyciu funkcji automatycznego uczenia maszynowego w Azure Machine Learning. Konfigurowanie modelu prognozowania jest podobne do konfigurowania modelu regresji standardowej przy użyciu automatycznego uczenia maszynowego, ale niektóre opcje konfiguracji i wstępne kroki przetwarzania istnieją do pracy z danymi szeregów czasowych. W poniższych przykładach pokazano, jak:

* Przygotowywanie danych do modelowania szeregów czasowych
* Konfigurowanie określonych parametrów szeregów czasowych w obiekcie [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)
* Uruchamianie prognoz z danymi szeregów czasowych

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Możesz użyć zautomatyzowanej ML do łączenia technik i podejścia i uzyskania zalecanej wysokiej jakości prognozy szeregów czasowych. Zautomatyzowany eksperyment szeregów czasowych jest traktowany jako problem z regresją wieloczynnikowa. Poprzednie wartości serii czasowych są "przestawne", aby stać się dodatkowymi wymiarami regresor wraz z innymi predykcyjnymi.

Takie podejście, w przeciwieństwie do klasycznych metod szeregów czasowych, ma zaletę naturalnie dołączania wielu zmiennych kontekstowych i ich relacji do siebie podczas uczenia się. W rzeczywistych aplikacjach do prognozowania można mieć wpływ na prognozę. Na przykład podczas prognozowania sprzedaży, interakcji z tendencjami historycznymi, kursów wymiany i ceny wszystkie wspólnie łączą wynik sprzedaży. Dodatkową korzyścią jest to, że wszystkie najnowsze innowacje w modelach regresji natychmiast stosują się do prognozowania.

Można [skonfigurować](#config) , jak daleko w przyszłość Prognoza powinna zostać rozszerzona (horyzont prognoz), a także spowolnienia i nie tylko. Zautomatyzowana ML zdobywa pojedynczy, ale często wewnętrznie rozgałęzienie modelu dla wszystkich elementów w zestawie danych i prognozowanie Horizons. W tym celu można uzyskać więcej danych w celu oszacowania parametrów modelu i generalizacji do niewidocznej serii.

Funkcje wyodrębnione z danych szkoleniowych odgrywają rolę krytyczną. I, zautomatyzowana ML wykonuje standardowe kroki przetwarzania wstępnego i generuje dodatkowe funkcje szeregów czasowych do przechwytywania efektów sezonowych i maksymalizuje dokładność predykcyjną.

## <a name="time-series-and-deep-learning-models"></a>Serie czasowe i modele uczenia głębokiego


Automatyczna ML zapewnia użytkownikom zarówno natywne, jak i bogate modele uczenia w ramach systemu rekomendacji. Te informacje obejmują:
+ Prophet
+ AutoARIMA
+ ForecastTCN

Uczenie głębokie o rozbudowanej ML umożliwia prognozowanie danych szeregów czasowych univariate i wieloczynnikowa.

Modele uczenia głębokiego mają trzy możliwości wewnętrzne:
1. Mogą uczyć się z dowolnego mapowania z danych wejściowych do wyjścia
1. Obsługują one wiele wejść i wyjść
1. Mogą automatycznie wyodrębniać wzorce w danych wejściowych, które rozciągają się na długie sekwencje

Dane o większej liczbie, modele uczenia głębokiego, takie jak ForecastTCN firmy Microsoft, mogą poprawić wyniki modelu wynikowego. 

Natywne informacje o szeregach czasowych są również udostępniane jako część zautomatyzowanej ML. Prophet działa najlepiej z seriami czasowymi, które mają silne skutki sezonowe i kilka sezonów danych historycznych. Prophet jest dokładny & Szybka, niezawodna do wartości odstających, brakujących danych i znaczących zmian w szeregach czasowych. 

AutoRegressive Integrated (ARIMA) jest popularną metodą statystyczną dla prognozowania szeregów czasowych. Ta technika prognozowania jest często używana w krótkoterminowych scenariuszach prognozowania, w których dane przedstawiają dowód trendów, takich jak cykle, które mogą być nieprzewidywalne i trudne do modelowania lub prognozowania. Funkcja autoARIMA przekształca dane w dane stacjonarne w celu uzyskania spójnych, niezawodnych wyników.

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby utworzyć obszar roboczy, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).
* W tym artykule założono podstawową wiedzę na temat konfigurowania zautomatyzowanego eksperymentu uczenia maszynowego. Postępuj zgodnie z [samouczkiem](tutorial-auto-train-models.md) lub poradami [, aby zobaczyć](how-to-configure-auto-train.md) podstawowe wzorce projektowe eksperymentu w usłudze Machine Learning.

## <a name="preparing-data"></a>Przygotowywanie danych

Najważniejszym różnicą między typem zadania regresja prognozowania a typem zadania regresji w ramach automatycznego uczenia maszynowego jest dołączenie funkcji w danych, która reprezentuje prawidłową serię czasową. Zwykła seria czasowa ma dobrze zdefiniowaną i spójną częstotliwość i ma wartość w każdym punkcie próbki w ciągłym przedziale czasu. Rozważmy poniższą migawkę pliku `sample.csv`.

    day_datetime,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/4/2018,A,2300,36
    9/4/2018,B,550,36
    9/5/2018,A,2100,36
    9/5/2018,B,650,36
    9/6/2018,A,2400,36
    9/6/2018,B,700,36
    9/7/2018,A,2450,36
    9/7/2018,B,650,36

Ten zestaw danych to prosty przykład codziennych danych sprzedaży dla firmy, która ma dwa różne sklepy, a i B. Ponadto istnieje funkcja `week_of_year`, która umożliwi modelowi wykrywanie cotygodniowych sezonowości. Pole `day_datetime` reprezentuje czystą serię czasową z częstotliwością dzienną, a pole `sales_quantity` jest kolumną docelową dla uruchomionych prognoz. Odczytaj dane do ramki dataPandas, a następnie użyj funkcji `to_datetime`, aby upewnić się, że szeregi czasowe są typu `datetime`.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

W takim przypadku dane są już sortowane rosnąco według pola czasu `day_datetime`. Jednak podczas konfigurowania eksperymentu upewnij się, że kolumna żądana godzina jest posortowana w kolejności rosnącej, aby utworzyć prawidłową serię czasową. Załóżmy, że dane zawierają 1 000 rekordów i czynią deterministyczną podział w danych w celu tworzenia zestawów danych szkoleniowych i testowych. Zidentyfikuj nazwę kolumny etykieta i ustaw ją na etykieta. W tym przykładzie etykieta zostanie `sales_quantity`. Następnie należy oddzielić pole Etykieta od `test_data`, aby utworzyć zestaw `test_target`.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> Podczas uczenia modelu do prognozowania przyszłych wartości upewnij się, że wszystkie funkcje używane w szkoleniu mogą być używane podczas przewidywania dla zamierzonego horyzontu. Na przykład podczas tworzenia prognozy popytu, w tym funkcji dla bieżącej ceny zapasowej, można w znacznym stopniu zwiększyć dokładność szkolenia. Jeśli jednak planujesz prognozowanie za pomocą długich horyzontów, możesz nie być w stanie dokładnie przewidzieć przyszłe wartości giełdowe odpowiadające przyszłym punktom szeregów czasowych, a dokładność modelu może być niepoprawna.

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>Konfigurowanie i uruchamianie eksperymentu

W przypadku zadań prognozowania automatyczne Uczenie maszynowe korzysta z kroków wstępnego przetwarzania i szacowania, które są specyficzne dla danych szeregów czasowych. Zostaną wykonane następujące kroki przetwarzania wstępnego:

* Wykrywaj częstotliwość próbkowania szeregów czasowych (na przykład co godzinę, codziennie, co tydzień) i Utwórz nowe rekordy dla nieobecnych punktów czasowych, aby zapewnić ciągłość serii.
* Nie ma wartości w elemencie docelowym (za pośrednictwem przekazywania) i kolumn funkcji (przy użyciu wartości kolumn mediany).
* Tworzenie funkcji opartych na ziarnach w celu włączenia stałych efektów w różnych seriach
* Tworzenie funkcji opartych na czasie, które ułatwiają uczenie wzorców sezonowych
* Koduj zmienne kategorii na liczby liczbowe

Obiekt [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) definiuje ustawienia i dane niezbędne do automatycznego zadania uczenia maszynowego. Podobnie jak w przypadku problemu z regresją, definiuje się standardowe parametry szkolenia, takie jak typ zadania, liczba iteracji, dane szkoleniowe i liczba operacji krzyżowych. W przypadku zadań prognozowania należy ustawić dodatkowe parametry, które mają wpływ na eksperyment. W poniższej tabeli opisano każdy parametr i jego użycie.

| Nazwa&nbsp;parametru | Opis | Wymagane |
|-------|-------|-------|
|`time_column_name`|Służy do określania kolumny DateTime w danych wejściowych używanych do kompilowania szeregów czasowych i wywnioskowania jej częstotliwości.|✓|
|`grain_column_names`|Nazwy definiujące poszczególne grupy serii w danych wejściowych. Jeśli ziarno nie jest zdefiniowane, zakłada się, że zestaw danych jest jedną serią czasową.||
|`max_horizon`|Definiuje maksymalny żądany zakres prognozy w jednostkach częstotliwości szeregów czasowych. Jednostki są oparte na przedziale czasu na dane szkoleniowe, na przykład co miesiąc, co tydzień, co Prognoza powinna przewidzieć.|✓|
|`target_lags`|Liczba wierszy do rozłożeniu wartości docelowych na podstawie częstotliwości danych. Opóźnienie jest reprezentowane jako lista lub jedna liczba całkowita. Zwłoki należy używać, gdy relacja między zmiennymi niezależnymi i zmienną zależną nie jest zgodna ani nie jest domyślnie skorelowana. Na przykład podczas próby prognozowania zapotrzebowania na produkt zapotrzebowanie w dowolnym miesiącu może zależeć od ceny określonych cen w ciągu 3 miesięcy. W tym przykładzie możesz chcieć zażądać negatywnego opóźnienia (popytu) przez 3 miesiące, aby model był szkoleniowy dla poprawnej relacji.||
|`target_rolling_window_size`|*n* okresy historyczne używane do generowania prognozowanych wartości, < = rozmiar zestawu szkoleniowego. W przypadku pominięcia *n* to pełny rozmiar zestawu szkoleniowego. Określ ten parametr, jeśli chcesz wziąć pod uwagę tylko określoną ilość historii podczas uczenia modelu.||
|`enable_dnn`|Włącz prognozowanie DNNs.||

Aby uzyskać więcej informacji, zobacz [dokumentację referencyjną](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) .

Utwórz ustawienia szeregów czasowych jako obiekt słownika. Ustaw `time_column_name` na pole `day_datetime` w zestawie danych. Zdefiniuj parametr `grain_column_names`, aby mieć pewność, że dla danych są tworzone **dwie osobne grupy szeregów czasowych** . jeden dla sklepu A i B. na koniec Ustaw `max_horizon` na 50, aby przewidzieć cały zestaw testów. Ustaw okno prognozy na 10 okresów z `target_rolling_window_size`, a następnie określ pojedyncze opóźnienie dla wartości docelowych dla dwóch okresów z parametrem `target_lags`. Zalecane jest ustawienie `max_horizon`, `target_rolling_window_size` i `target_lags` na wartość "automatycznie", co spowoduje automatyczne wykrycie tych wartości. W poniższym przykładzie dla tych parametrów użyto ustawień "Auto". 

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": "auto",
    "target_lags": "auto",
    "target_rolling_window_size": "auto",
    "preprocess": True,
}
```

> [!NOTE]
> Zautomatyzowane kroki wstępnego przetwarzania w usłudze Machine Learning (normalizacja funkcji, obsługa brakujących danych, konwertowanie tekstu na liczbowe itp.) staje się częścią modelu źródłowego. Przy użyciu modelu dla prognoz te same kroki przetwarzania wstępnego zastosowane podczas uczenia są automatycznie stosowane do danych wejściowych.

Definiując `grain_column_names` w powyższym fragmencie kodu, AutoML utworzy dwie osobne grupy szeregów czasowych, znane także jako wiele szeregów czasowych. Jeśli nie zdefiniowano żadnego ziarna, AutoML założenie, że zestaw danych jest pojedynczą serią czasową. Aby dowiedzieć się więcej o pojedynczych seriach czasowych, zobacz [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

Teraz można utworzyć standardowy obiekt `AutoMLConfig`, określając typ zadania `forecasting` i przesłać eksperyment. Po zakończeniu działania modelu Pobierz iterację najlepszego przebiegu.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

Zapoznaj się z [przykładami prognozowanych notesów](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) , aby zapoznać się ze szczegółowymi przykładami konfiguracji prognozowania, w tym:

* [Wykrywanie świąt i cechowania](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [krzyżowe sprawdzanie poprawności źródła](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [Konfigurowalne spowolnienia](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [funkcje agregujące okna stopniowego](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>Konfigurowanie eksperymentu włączania prognozowania DNN

> [!NOTE]
> Obsługa DNN w przypadku prognozowania w programie zautomatyzowanym Machine Learning jest w wersji zapoznawczej i nie jest obsługiwana w przypadku uruchomień lokalnych.

Aby można było korzystać z DNNs do prognozowania, należy ustawić parametr `enable_dnn` w AutoMLConfig na true. 

Zalecamy używanie klastra obliczeniowego AML z jednostkami SKU procesora GPU i co najmniej dwoma węzłami jako elementem docelowym obliczeń. Aby zapewnić wystarczającą ilość czasu na ukończenie szkolenia DNN, zalecamy ustawienie limitu czasu eksperymentu na co najmniej kilka godzin.
Aby uzyskać więcej informacji o rozmiarach obliczeniowych i maszyn wirtualnych AML, które obejmują procesor GPU, zapoznaj się z dokumentacją [obliczeń AML](how-to-set-up-training-targets.md#amlcompute) i [rozmiarem maszyny wirtualnej zoptymalizowanej pod kątem procesora GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Aby zapoznać się ze szczegółowym przykładem kodu korzystającego z DNNs, zobacz [Notes prognozowania produkcji napojów](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) .

### <a name="view-feature-engineering-summary"></a>Wyświetlanie podsumowania inżynierów funkcji

W przypadku typów zadań szeregów czasowych w ramach automatycznego uczenia maszynowego można wyświetlić szczegóły procesu inżynierii funkcji. Poniższy kod przedstawia każdą nieprzetworzoną funkcję wraz z następującymi atrybutami:

* Pierwotna nazwa funkcji
* Liczba skonstruowanych funkcji utworzonych z tej funkcji nieprzetworzonej
* Wykryto typ
* Czy funkcja została porzucona
* Lista przekształceń funkcji dla funkcji RAW

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Prognozowanie przy użyciu najlepszego modelu

Użyj najlepszej iteracji modelu do prognozowania wartości dla zestawu danych testowych.

```python
predict_labels = fitted_model.predict(test_data)
actual_labels = test_labels.flatten()
```

Alternatywnie można użyć funkcji `forecast()` zamiast `predict()`, która będzie zezwalała na specyfikacje po rozpoczęciu prognoz. W poniższym przykładzie należy najpierw zastąpić wszystkie wartości w `y_pred` z `NaN`. Podstawą prognozy będzie na końcu danych szkoleniowych w tym przypadku, tak jak zwykle w przypadku korzystania z `predict()`. Jednak jeśli zamienisz tylko drugą połowę `y_pred` z `NaN`, funkcja spowodowałaby pozostawienie wartości liczbowych w pierwszej połowie niemodyfikowanej, ale prognozowanie wartości `NaN` w drugiej połowie. Funkcja zwraca zarówno wartości prognozowane, jak i wyrównane funkcje.

Można również użyć parametru `forecast_destination` w funkcji `forecast()` do prognozowania wartości aż do określonej daty.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Oblicz RMSE (główny błąd oznaczający pierwiastek) między wartościami rzeczywistymi `actual_labels` i prognozowanie wartości w `predict_labels`.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Teraz, gdy ogólna dokładność modelu została określona, najbardziej realistycznym następnym krokiem jest użycie modelu do prognozowania nieznanych przyszłych wartości. Podaj zestaw danych w tym samym formacie co zestaw testów, `test_data` ale z przyszłymi datetimemi, a wynikiem jest przewidywane wartości dla każdego kroku szeregów czasowych. Załóżmy, że ostatnie rekordy szeregów czasowych w zestawie danych były 12/31/2018. Aby prognozować zapotrzebowanie na następny dzień (lub wiele okresów potrzebnych do prognozowania, < = `max_horizon`), Utwórz pojedynczy rekord szeregu czasowego dla każdego magazynu dla 01/01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Powtórz kroki niezbędne do załadowania tych przyszłych danych do ramki Dataframe, a następnie uruchom `best_run.predict(test_data)`, aby przewidzieć przyszłe wartości.

> [!NOTE]
> Wartości nie mogą być przewidywane dla liczby okresów większych niż `max_horizon`. Model musi być przeszkolony z większym horyzontem, aby przewidzieć przyszłe wartości poza bieżącym horyzontem.

## <a name="next-steps"></a>Następne kroki

* Postępuj zgodnie z [samouczkiem](tutorial-auto-train-models.md) , aby dowiedzieć się, jak tworzyć eksperymenty przy użyciu automatycznej uczenia maszynowego
* Zapoznaj się z dokumentacją [zestawu Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) .
