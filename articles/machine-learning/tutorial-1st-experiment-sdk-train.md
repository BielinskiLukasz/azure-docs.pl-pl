---
title: 'Samouczek: uczenie pierwszego modelu usługi Azure ML w języku Python'
titleSuffix: Azure Machine Learning
description: W ramach tego samouczka nauczysz się podstawowe wzorce projektowe w Azure Machine Learning i uczenie prostego modelu uczenia scikitego na podstawie zestawu danych cukrzycą.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 02/10/2020
ms.openlocfilehash: aa90655ecb14abe38ec8fdfc6c18e7d292abbef3
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77116539"
---
# <a name="tutorial-train-your-first-ml-model"></a>Samouczek: uczenie swojego pierwszego modelu ML

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ten samouczek jest **drugą częścią dwuczęściowej serii samouczków**. W poprzednim samouczku [utworzono obszar roboczy i wybrano środowisko programistyczne](tutorial-1st-experiment-sdk-setup.md). W ramach tego samouczka nauczysz się podstawowe wzorce projektowe w Azure Machine Learning i uczenie prostego modelu uczenia scikitego na podstawie zestawu danych cukrzycą. Po ukończeniu tego samouczka będziesz mieć praktyczną wiedzę na temat zestawu SDK w celu skalowania w górę w celu opracowywania bardziej złożonych eksperymentów i przepływów pracy.

W tym samouczku nauczysz się wykonywać następujące zadania:

> [!div class="checklist"]
> * Łączenie obszaru roboczego i tworzenie eksperymentu
> * Ładowanie danych i uczenie modeli scikit — uczenie się
> * Wyświetlanie wyników szkoleniowych w portalu
> * Pobieranie najlepszego modelu

## <a name="prerequisites"></a>Wymagania wstępne

Jedynym wymaganiem wstępnym jest uruchomienie części jednego z tego samouczka, [Ustawienia środowiska i obszaru roboczego](tutorial-1st-experiment-sdk-setup.md).

W tej części samouczka uruchamiasz kod w przykładowym samouczku notesu Jupyter */Create-First-ml-Experiment/tutorial-1st-Experiment-SDK-Train. ipynb* otwartym na końcu części pierwszej. Ten artykuł zawiera ten sam kod, który znajduje się w notesie.

## <a name="open-the-notebook"></a>Otwieranie notesu

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com/).

1. Otwórz **samouczek — pierwszy eksperyment — zestaw SDK — uczenie. ipynb** w folderze, jak pokazano w [części pierwszej](tutorial-1st-experiment-sdk-setup.md#open).


> [!Warning]
> **Nie** Twórz *nowego* notesu w interfejsie Jupyter. *Samouczki notesu/Create-First-ml-Experiment/tutorial-1st-Experiment-SDK-Train. ipynb* obejmują **Wszystkie kod i dane, które są zbędne** dla tego samouczka.

## <a name="connect-workspace-and-create-experiment"></a>Połącz obszar roboczy i Utwórz eksperyment

> [!Important]
> Pozostała część tego artykułu zawiera tę samą zawartość, która jest wyświetlana w notesie.  
>
> Przełącz się do notesu Jupyter teraz, jeśli chcesz czytać wraz z uruchamianiem kodu. 
> Aby uruchomić pojedynczą komórkę kodu w notesie, kliknij komórkę kod i naciśnij **klawisze SHIFT + ENTER**. Lub Uruchom cały Notes, wybierając pozycję **Uruchom wszystkie** z górnego paska narzędzi.

Zaimportuj klasę `Workspace` i Załaduj informacje o subskrypcji z pliku `config.json` przy użyciu funkcji, `from_config().` to szuka domyślnie pliku JSON w bieżącym katalogu, ale można także określić parametr ścieżki, aby wskazywał plik przy użyciu `from_config(path="your/file/path")`. W przypadku serwera notesu w chmurze plik jest automatycznie w katalogu głównym.

Jeśli Poniższy kod pyta o dodatkowe uwierzytelnianie, wystarczy wkleić link w przeglądarce i wprowadzić token uwierzytelniania.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Teraz Utwórz eksperyment w obszarze roboczym. Eksperyment jest innym podstawnym zasobem w chmurze, który reprezentuje kolekcję prób (poszczególne modele są uruchamiane). W tym samouczku pokazano, jak utworzyć przebiegi i śledzić szkolenia modeli w programie Azure Machine Learning Studio przy użyciu eksperymentu. Parametry obejmują odwołanie do obszaru roboczego i nazwę ciągu dla eksperymentu.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>Ładowanie danych i przygotowanie do szkolenia

W tym samouczku użyjesz zestawu danych cukrzycą, który korzysta z funkcji, takich jak wiek, płeć i BMI, aby przewidzieć postęp chorób cukrzycą. Załaduj dane z klasy [otwarte zestawy danych platformy Azure](https://azure.microsoft.com/services/open-datasets/) i podziel je na zestawy szkoleniowe i testowe przy użyciu `train_test_split()`. Ta funkcja dzieli dane, aby model miał niewidoczne dane, które są używane do testowania po szkoleniu.


```python
from azureml.opendatasets import Diabetes
from sklearn.model_selection import train_test_split

x_df = Diabetes.get_tabular_dataset().to_pandas_dataframe().dropna()
y_df = x_df.pop("Y")

X_train, X_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=66)
```

## <a name="train-a-model"></a>Szkolenie modelu

Uczenie prostego modelu uczenia się w scikit można łatwo wykonać lokalnie w przypadku szkolenia na małą skalę, ale w przypadku szkolenia wielu iteracji z dziesiątami z różnych permutacji funkcji i ustawień parametrów, można łatwo utracić śledzenie modeli, które zostały przeszkolone i jak przeszkolone. Poniższy Wzorzec projektowy pokazuje, jak korzystać z zestawu SDK, aby łatwo śledzić szkolenia w chmurze.

Utwórz skrypt, który pociąga za siebie modele pierścieniowe w pętli za pomocą różnych wartości alfanumerycznych parametru.


```python
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.externals import joblib
import math

alphas = [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]

for alpha in alphas:
    run = experiment.start_logging()
    run.log("alpha_value", alpha)

    model = Ridge(alpha=alpha)
    model.fit(X=X_train, y=y_train)
    y_pred = model.predict(X=X_test)
    rmse = math.sqrt(mean_squared_error(y_true=y_test, y_pred=y_pred))
    run.log("rmse", rmse)

    model_name = "model_alpha_" + str(alpha) + ".pkl"
    filename = "outputs/" + model_name

    joblib.dump(value=model, filename=filename)
    run.upload_file(name=model_name, path_or_stream=filename)
    run.complete()
```

Powyższy kod wykonuje następujące czynności:

1. Dla każdej wartości parametru alfa w tablicy `alphas` zostanie utworzony nowy przebieg w ramach eksperymentu. Wartość alfa jest zarejestrowana w celu rozróżnienia między każdym przebiegiem.
1. W każdym przebiegu model pierścieniowy jest skonkretyzowany, szkolony i używany do uruchamiania prognoz. Element główny-średni-kwadratowy jest obliczany dla wartości rzeczywistych i przewidywanych, a następnie rejestrowanych w ramach uruchomienia. W tym momencie przebieg ma metadane dołączone zarówno do wartości alfa, jak i dokładności RMSE.
1. Następnie model każdego przebiegu jest serializowany i przekazywany do przebiegu. Pozwala to na pobranie pliku modelu z przebiegu w portalu.
1. Po zakończeniu każdej iteracji przebieg jest wykonywany przez wywołanie `run.complete()`.

Po zakończeniu szkolenia Wywołaj zmienną `experiment`, aby pobrać link do eksperymentu w portalu.

```python
experiment
```

<table style="width:100%"><tr><th>Name (Nazwa)</th><th>Obszar roboczy</th><th>Strona raportu</th><th>Strona docs</th></tr><tr><td>cukrzycą — eksperyment</td><td>Nazwa Twojego obszaru roboczego</td><td>Łącze do Azure Portal</td><td>Link do dokumentacji</td></tr></table>

## <a name="view-training-results-in-portal"></a>Wyświetlanie wyników szkolenia w portalu

Poniższe **łącze do Azure Portal** przeprowadzi Cię do strony głównej eksperymentu. W tym miejscu zobaczysz wszystkie uruchomienia indywidualne w eksperymentie. Wszystkie wartości zarejestrowane przez użytkownika (w tym przypadku`alpha_value` i `rmse`) stają się polami dla każdego przebiegu, a także staną się dostępne dla wykresów i kafelków w górnej części strony eksperymentu. Aby dodać zarejestrowanej metrykę do wykresu lub kafelka, umieść kursor nad nim, kliknij przycisk Edytuj i Znajdź metrykę zarejestrowaną niestandardowo.

Gdy szkolenia modeli są przeprowadzane na dużą skalę i tysiące różnych uruchomień, ta strona ułatwia przeglądanie każdego modelu, który jest przeszkolony, a w odróżnieniu od czasu, w jaki zostały one przeszkolone, oraz sposobu zmiany unikatowych metryk w czasie.

![Strona głównej eksperymentu w portalu](./media/tutorial-1st-experiment-sdk-train/experiment-main.png)

Kliknięcie linku numeru uruchamiania w kolumnie `RUN NUMBER` spowoduje przejście do strony dla każdego uruchomienia pojedynczego. Na **karcie domyślne znajdują** się szczegółowe informacje dotyczące poszczególnych przebiegów. Przejdź do karty dane **wyjściowe** i zobaczysz plik `.pkl` dla modelu, który został przekazany do przebiegu podczas każdej iteracji szkoleniowej. W tym miejscu możesz pobrać plik modelu, zamiast konieczności ręcznego ponownego uczenia go.

![Strona szczegóły uruchamiania w portalu](./media/tutorial-1st-experiment-sdk-train/model-download.png)

## <a name="get-the-best-model"></a>Uzyskaj najlepszy model

Oprócz możliwości pobierania plików modelu z eksperymentu w portalu można również pobrać je programowo. Poniższy kod wykonuje iterację w każdym przebiegu eksperymentu i uzyskuje dostęp do zarejestrowanych metryk uruchomienia i szczegółów uruchomienia (które zawierają run_id). Pozwala to na śledzenie najlepszego przebiegu, w tym przypadku przebieg z najniższym średnim elementem głównym-kwadratowym.

```python
minimum_rmse_runid = None
minimum_rmse = None

for run in experiment.get_runs():
    run_metrics = run.get_metrics()
    run_details = run.get_details()
    # each logged metric becomes a key in this returned dict
    run_rmse = run_metrics["rmse"]
    run_id = run_details["runId"]

    if minimum_rmse is None:
        minimum_rmse = run_rmse
        minimum_rmse_runid = run_id
    else:
        if run_rmse < minimum_rmse:
            minimum_rmse = run_rmse
            minimum_rmse_runid = run_id

print("Best run_id: " + minimum_rmse_runid)
print("Best run_id rmse: " + str(minimum_rmse))
```

    Best run_id: 864f5ce7-6729-405d-b457-83250da99c80
    Best run_id rmse: 57.234760283951765

Użyj najlepszego identyfikatora przebiegu, aby pobrać pojedynczy przebieg przy użyciu konstruktora `Run` wraz z obiektem eksperymentu. Następnie Wywołaj `get_file_names()`, aby wyświetlić wszystkie pliki dostępne do pobrania z tego przebiegu. W takim przypadku przekazano tylko jeden plik dla każdego przebiegu podczas szkolenia.

```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

    ['model_alpha_0.1.pkl']

Wywołaj `download()` w obiekcie Run, określając nazwę pliku modelu do pobrania. Domyślnie ta funkcja jest pobierana do bieżącego katalogu.

```python
best_run.download_file(name="model_alpha_0.1.pkl")
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Nie wykonuj tej sekcji, jeśli planujesz Uruchamianie innych samouczków Azure Machine Learning.

### <a name="stop-the-compute-instance"></a>Zatrzymaj wystąpienie obliczeniowe

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Usuń wszystko

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Możesz też zachować grupę zasobów i usunąć jeden obszar roboczy. Wyświetl właściwości obszaru roboczego i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące zadania:

> [!div class="checklist"]
> * Nawiązano połączenie z obszarem roboczym i utworzono eksperyment
> * Ładowane dane i przeszkolone modele uczenia scikitego
> * Oglądane wyniki szkolenia w portalu i pobrane modele

[Wdróż model](tutorial-deploy-models-with-aml.md) za pomocą usługi Azure Machine Learning.
Dowiedz się, jak opracowywać zautomatyzowane eksperymenty [uczenia maszynowego](tutorial-auto-train-models.md) .
