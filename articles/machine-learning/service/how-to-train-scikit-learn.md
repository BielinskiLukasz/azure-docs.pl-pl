---
title: Uczenie scikit — uczenie modeli uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uruchamiać skrypty szkoleniowe scikit w skali przedsiębiorstwa przy użyciu klasy Azure Machine Learning skryptu sklearn szacowania. Przykładowe skrypty klasyfikują obrazy kwiatów z przesłoną w celu utworzenia modelu uczenia maszynowego na podstawie zestawu danych Iris scikit.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/02/2019
ms.custom: seodec18
ms.openlocfilehash: 2b6cdf9350d95de901e8a0f1e875d90513b33f1a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976115"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Kompiluj scikit — Poznaj modele na dużą skalę dzięki Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak uruchamiać skrypty szkoleniowe scikit na poziomie przedsiębiorstwa przy użyciu klasy Azure Machine Learning [skryptu sklearn szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) . 

Przykładowe skrypty w tym artykule służą do klasyfikowania obrazów kwiatów w formacie Iris w celu utworzenia modelu uczenia maszynowego na podstawie [zestawu danych Iris](https://archive.ics.uci.edu/ml/datasets/iris)scikit.

Bez względu na to, czy przeprowadzasz szkolenia z modelu uczenia maszynowego scikit — uczenie się od podstaw lub przenosisz istniejący model do chmury, możesz użyć Azure Machine Learning, aby skalować zadania szkoleniowe typu "open source" przy użyciu elastycznych zasobów obliczeniowych w chmurze. Możesz tworzyć, wdrażać i monitorować modele klasy produkcyjnej przy użyciu Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w dowolnym z następujących środowisk:
 - Maszyna wirtualna w Azure Machine Learning Notes — nie jest wymagane pobieranie ani instalacja

    - Ukończ [Samouczek: Zainstaluj środowisko i obszar roboczy](tutorial-1st-experiment-sdk-setup.md) , aby utworzyć dedykowany serwer notesu wstępnie załadowany z zestawem SDK i przykładowym repozytorium.
    - W folderze uczenie przykładów na serwerze notesu Znajdź ukończony i rozwinięty Notes, przechodząc do tego katalogu: How to- **use-azure > ml-frameworks > scikit-uczenie > uczenie > uczenie-parametr-dostrajania-Deploy-with-skryptu sklearn** folder.

 - Własny serwer Jupyter Notebook

    - [Zainstaluj zestaw SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Utwórz plik konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).
    - Pobierz zestaw danych i przykładowy plik skryptu 
        - [zestaw danych Iris](https://archive.ics.uci.edu/ml/datasets/iris)
        - [`train_iris.py`](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
    - Ukończoną [wersję Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) tego przewodnika można również znaleźć na stronie przykładów usługi GitHub. Notes zawiera rozszerzoną sekcję obejmującą dostrajanie inteligentnego parametru i pobierającą najlepszy model przy użyciu metryk głównych.

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

Ta sekcja umożliwia skonfigurowanie eksperymentu szkoleniowego przez załadowanie wymaganych pakietów języka Python, zainicjowanie obszaru roboczego, utworzenie eksperymentu i przekazanie danych szkoleniowych i skryptów szkoleniowych.

### <a name="import-packages"></a>Importowanie pakietów

Najpierw zaimportuj niezbędne biblioteki języka Python.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicjowanie obszaru roboczego

[Obszar roboczy Azure Machine Learning](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi. Zapewnia ono scentralizowane miejsce do pracy ze wszystkimi tworzonymi artefaktami. W zestawie SDK języka Python można uzyskać dostęp do artefaktów obszaru roboczego przez utworzenie obiektu [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) .

Utwórz obiekt obszaru roboczego z pliku `config.json` utworzonego w [sekcji wymagania wstępne](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Tworzenie eksperymentu uczenia maszynowego

Utwórz eksperyment i folder do przechowywania skryptów szkoleniowych. W tym przykładzie Utwórz eksperyment o nazwie "skryptu sklearn-Iris".

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>Przygotuj skrypt szkoleniowy

W tym samouczku skrypt szkoleniowy **train_iris. PR** został już udostępniony. W tym celu należy mieć możliwość wykonania dowolnego niestandardowego skryptu szkoleniowego i uruchomienia go z platformą Azure ML bez konieczności modyfikowania kodu.

Aby skorzystać z możliwości śledzenia i metryk platformy Azure ML, Dodaj niewielką ilość kodu platformy Azure ML w ramach skryptu szkoleniowego.  Skrypt szkoleniowy **train_iris. PR** pokazuje, w jaki sposób rejestrować niektóre metryki w ramach uruchomienia usługi Azure ml przy użyciu obiektu `Run` w skrypcie.

Podany skrypt szkoleniowy używa przykładowych danych z funkcji `iris = datasets.load_iris()`.  W przypadku własnych danych może być konieczne wykonanie kroków takich jak [przekazywanie zestawu danych i skryptów](how-to-train-keras.md#data-upload) w celu udostępnienia danych podczas szkoleń.

Skopiuj skrypt szkoleniowy **train_iris. PR** do katalogu projektu.

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>Utwórz lub Pobierz obiekt docelowy obliczeń

Utwórz obiekt docelowy obliczeń dla zadania scikit-Dowiedz się, aby uruchomić. Scikit — uczenie obsługuje tylko jeden węzeł, przetwarzanie procesora CPU.

Poniższy kod tworzy Azure Machine Learning Managed COMPUTE (AmlCompute) dla zasobu obliczeń zdalnego szkolenia. Tworzenie AmlCompute trwa około 5 minut. Jeśli AmlCompute o tej nazwie znajduje się już w obszarze roboczym, ten kod pominie proces tworzenia.

```Python
cluster_name = "cpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Aby uzyskać więcej informacji na temat obiektów docelowych obliczeń, zobacz artykuł [co to jest Target COMPUTE](concept-compute-target.md) .

## <a name="create-a-scikit-learn-estimator"></a>Tworzenie scikit — uczenie się szacowania

[Scikit — uczenie szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) zapewnia prostą metodę uruchamiania zadania szkoleniowego scikit-uczenia na obiekcie docelowym obliczeń. Jest implementowana za pomocą klasy [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) , która może być używana do obsługi szkolenia procesora jednowęzłowego.

Jeśli do uruchomienia skryptu szkoleniowego wymagane są dodatkowe pakiety PIP lub Conda, można je zainstalować na utworzonym obrazie platformy Docker, przekazując ich nazwy za pomocą argumentów `pip_packages` i `conda_packages`.

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py'
                    pip_packages=['joblib']
                   )
```

## <a name="submit-a-run"></a>Prześlij przebieg

[Obiekt Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) udostępnia interfejs do historii uruchamiania, gdy zadanie jest uruchomione i po jego zakończeniu.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Gdy przebieg jest wykonywany, przechodzi przez następujące etapy:

- **Przygotowywanie**: obraz platformy Docker jest tworzony zgodnie z TensorFlow szacowania. Obraz zostanie przekazany do rejestru kontenerów obszaru roboczego i zapisany w pamięci podręcznej do późniejszego uruchomienia. Dzienniki są również przesyłane strumieniowo do historii uruchamiania i mogą być przeglądane w celu monitorowania postępu.

- **Skalowanie**: klaster próbuje skalować w górę, jeśli klaster Batch AI wymaga większej liczby węzłów do uruchomienia przebiegu, niż jest to obecnie dostępne.

- **Uruchamianie**: wszystkie skrypty w folderze skryptów są przekazywane do obiektu docelowego obliczeń, magazyny danych są instalowane lub kopiowane, a entry_script jest wykonywane. Dane wyjściowe z stdout i folder./Logs są przesyłane strumieniowo do historii uruchamiania i mogą być używane do monitorowania przebiegu.

- **Przetwarzanie końcowe**: folder./Outputs przebiegu jest kopiowany do historii uruchamiania.

## <a name="save-and-register-the-model"></a>Zapisz i zarejestruj model

Po przeszkoleniu modelu możesz go zapisać i zarejestrować w obszarze roboczym. Rejestracja modelu umożliwia przechowywanie modeli i ich wersji w obszarze roboczym w celu uproszczenia [zarządzania modelami i ich wdrażania](concept-model-management-and-deployment.md).

Dodaj następujący kod do skryptu szkoleniowego, train_iris. PR, aby zapisać model. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Zarejestruj model w obszarze roboczym przy użyciu następującego kodu. Określając parametry `model_framework`, `model_framework_version`i `resource_configuration`, wdrożenie modelu No-Code nie zostanie udostępnione. Dzięki temu można bezpośrednio wdrożyć model jako usługę sieci Web z zarejestrowanego modelu, a obiekt `ResourceConfiguration` definiuje zasób obliczeniowy dla usługi sieci Web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Wdrażanie

Właśnie zarejestrowany model można wdrożyć w taki sam sposób jak każdy inny zarejestrowany model w Azure Machine Learning, niezależnie od tego, który szacowania używany do uczenia się. Wdrożenie How-to zawiera sekcję dotyczącą rejestrowania modeli, ale możesz przejść bezpośrednio do tworzenia celu [obliczeń](how-to-deploy-and-where.md#choose-a-compute-target) dla wdrożenia, ponieważ istnieje już zarejestrowany model.

### <a name="preview-no-code-model-deployment"></a>Przeglądania Wdrożenie modelu bez kodu

Zamiast tradycyjnej trasy wdrożenia można również użyć funkcji wdrażania bez kodu (wersja zapoznawcza) dla scikit — uczenie się. Nie jest obsługiwane wdrożenie modelu bez kodu dla wszystkich wbudowanych typów modeli scikit. Rejestrując model, jak pokazano powyżej, za pomocą parametrów `model_framework`, `model_framework_version`i `resource_configuration`, można po prostu użyć funkcji statycznej `deploy()` do wdrożenia modelu.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

Uwaga: te zależności są zawarte we wstępnie skompilowanym kontenerze wnioskowania scikit.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

Pełne [Omówienie](how-to-deploy-and-where.md) wdrażania w Azure Machine Learning.


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono przeszkolony i zarejestrowany model uczenia scikit oraz informacje o opcjach wdrażania. Zapoznaj się z innymi artykułami, aby dowiedzieć się więcej na temat Azure Machine Learning.

* [Śledzenie metryk są uruchamiane podczas szkolenia](how-to-track-experiments.md)
* [Dostosowywanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Architektura referencyjna na potrzeby rozproszonego szkolenia uczenia głębokiego na platformie Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
