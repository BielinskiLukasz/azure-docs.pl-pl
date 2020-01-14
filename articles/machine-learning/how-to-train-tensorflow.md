---
title: Uczenie sieci neuronowych za pomocą TensorFlow
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uruchamiać skrypty szkoleniowe TensorFlow na dużą skalę przy użyciu Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 9604259d0610faeee36b9ad80dfb4aa2ae83d19a
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834845"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Twórz TensorFlow model uczenia głębokiego na dużą skalę dzięki Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule pokazano, jak uruchamiać skrypty szkoleniowe [TensorFlow](https://www.tensorflow.org/overview) na dużą skalę przy użyciu klasy [TensorFlow szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) Azure Machine Learning. Ten przykład pociąga za siebie i rejestruje model TensorFlow do klasyfikowania cyfr pisanych ręcznie przy użyciu sieci głębokiej neuronowych (DNN).

Bez względu na to, czy tworzysz model TensorFlow z podstaw, czy przenosisz [istniejący model](how-to-deploy-existing-model.md) do chmury, możesz użyć Azure Machine Learning, aby skalować zadania szkoleniowe typu "open source" w celu kompilowania, wdrażania, obsługi i monitorowania modeli klasy produkcyjnej.

Dowiedz się więcej na temat uczenia głębokiego i uczenia [maszynowego](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w dowolnym z następujących środowisk:

 - Wystąpienie obliczeniowe Azure Machine Learning — nie jest wymagane pobieranie ani instalacja

     - Ukończ [Samouczek: Zainstaluj środowisko i obszar roboczy](tutorial-1st-experiment-sdk-setup.md) , aby utworzyć dedykowany serwer notesu wstępnie załadowany z zestawem SDK i przykładowym repozytorium.
    - W folderze przykłady głębokiego uczenia na serwerze notesu Znajdź ukończony i rozwinięty Notes, przechodząc do tego katalogu: **How to-use-azure > ml-frameworks > tensorflow > deployment > uczenie się-parametr-tensorflow** 
 
 - Własny serwer Jupyter Notebook

    - [Zainstaluj zestaw SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Utwórz plik konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).
    - [Pobierz pliki przykładowego skryptu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` i `utils.py`
     
    Ukończoną [wersję Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) tego przewodnika można również znaleźć na stronie przykładów usługi GitHub. Notes obejmuje rozwinięte sekcje obejmujące dostrajanie inteligentnego parametru, wdrożenie modelu i widżety notesu.

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

### <a name="create-a-deep-learning-experiment"></a>Utwórz eksperyment uczenia głębokiego

Utwórz eksperyment i folder do przechowywania skryptów szkoleniowych. W tym przykładzie Utwórz eksperyment o nazwie "TF-mnist ręcznie".

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="create-a-file-dataset"></a>Utwórz plik DataSet

Obiekt `FileDataset` odwołuje się do jednego lub wielu plików w magazynie danych obszaru roboczego lub publicznych adresów URL. Pliki mogą być w dowolnym formacie, a Klasa oferuje możliwość pobierania lub instalowania plików do obliczeń. Tworząc `FileDataset`, można utworzyć odwołanie do lokalizacji źródła danych. Jeśli zastosowano jakiekolwiek przekształcenia do zestawu danych, zostaną one zapisane również w zestawie danych. Dane pozostają w istniejącej lokalizacji, więc nie są naliczane żadne dodatkowe koszty związane z magazynem. Aby uzyskać więcej informacji, [Zobacz przewodnik](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) dotyczący `Dataset` pakietu.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

Użyj metody `register()`, aby zarejestrować zestaw danych w obszarze roboczym, aby można było udostępnić go innym osobom, ponownie używać w różnych eksperymentach i nazwach w skrypcie szkoleniowym.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

## <a name="create-a-compute-target"></a>Utworzyć cel obliczenia

Utwórz obiekt docelowy obliczeń dla zadania TensorFlow. W tym przykładzie należy utworzyć klaster obliczeniowy z obsługą procesora GPU Azure Machine Learning.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Aby uzyskać więcej informacji na temat obiektów docelowych obliczeń, zobacz artykuł [co to jest Target COMPUTE](concept-compute-target.md) .

## <a name="create-a-tensorflow-estimator"></a>Tworzenie TensorFlow szacowania

[TensorFlow szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) zapewnia prosty sposób uruchamiania zadania szkolenia TensorFlow na obiekcie docelowym obliczeń.

TensorFlow szacowania jest implementowane za pomocą klasy generycznej [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) , która może być używana do obsługi dowolnej struktury. Aby uzyskać więcej informacji o modelach szkoleniowych przy użyciu generycznej szacowania, zobacz [uczenie modeli Azure Machine Learning przy](how-to-train-ml-models.md) użyciu usługi szacowania

Jeśli do uruchomienia skryptu szkoleniowego wymagane są dodatkowe pakiety PIP lub Conda, można je zainstalować na utworzonym obrazie platformy Docker, przekazując ich nazwy za pomocą argumentów `pip_packages` i `conda_packages`.

```python
script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True,
                 pip_packages=['azureml-dataprep[pandas,fuse]')
```

> [!TIP]
> Dodano obsługę **Tensorflow 2,0** do klasy Tensorflow szacowania. Zobacz [wpis w blogu](https://azure.microsoft.com/blog/tensorflow-2-0-on-azure-fine-tuning-bert-for-question-tagging/), aby uzyskać więcej informacji.

Aby uzyskać więcej informacji na temat dostosowywania środowiska języka Python, zobacz [Tworzenie środowisk i zarządzanie nimi na potrzeby szkolenia i wdrażania](how-to-use-environments.md). 

## <a name="submit-a-run"></a>Prześlij przebieg

[Obiekt Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) udostępnia interfejs do historii uruchamiania, gdy zadanie jest uruchomione i po jego zakończeniu.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Gdy przebieg jest wykonywany, przechodzi przez następujące etapy:

- **Przygotowywanie**: obraz platformy Docker jest tworzony zgodnie z TensorFlow szacowania. Obraz zostanie przekazany do rejestru kontenerów obszaru roboczego i zapisany w pamięci podręcznej do późniejszego uruchomienia. Dzienniki są również przesyłane strumieniowo do historii uruchamiania i mogą być przeglądane w celu monitorowania postępu.

- **Skalowanie**: klaster próbuje skalować w górę, jeśli klaster Batch AI wymaga większej liczby węzłów do uruchomienia przebiegu, niż jest to obecnie dostępne.

- **Uruchamianie**: wszystkie skrypty w folderze skryptów są przekazywane do obiektu docelowego obliczeń, magazyny danych są instalowane lub kopiowane, a entry_script jest wykonywane. Dane wyjściowe z stdout i folder./Logs są przesyłane strumieniowo do historii uruchamiania i mogą być używane do monitorowania przebiegu.

- **Przetwarzanie końcowe**: folder./Outputs przebiegu jest kopiowany do historii uruchamiania.

## <a name="register-or-download-a-model"></a>Rejestrowanie lub pobieranie modelu

Po przeszkoleniu modelu możesz zarejestrować go w obszarze roboczym. Rejestracja modelu umożliwia przechowywanie modeli i ich wersji w obszarze roboczym w celu uproszczenia [zarządzania modelami i ich wdrażania](concept-model-management-and-deployment.md). Określając parametry `model_framework`, `model_framework_version`i `resource_configuration`, wdrożenie modelu No-Code nie zostanie udostępnione. Dzięki temu można bezpośrednio wdrożyć model jako usługę sieci Web z zarejestrowanego modelu, a obiekt `ResourceConfiguration` definiuje zasób obliczeniowy dla usługi sieci Web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-dnn-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='1.13.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Możesz również pobrać lokalną kopię modelu przy użyciu obiektu Run. W `mnist-tf.py`skrypcie szkoleniowym obiekt wygaszacza TensorFlow utrzymuje model do folderu lokalnego (lokalnie dla elementu docelowego obliczeń). Aby pobrać kopię, można użyć obiektu Run.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Trenowanie rozproszone

[`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) szacowania obsługuje również szkolenia rozproszone między procesorami i klastrami GPU. Można łatwo uruchomić rozproszone zadania TensorFlow, a Azure Machine Learning będzie zarządzać aranżacją.

Usługa Azure Machine Learning obsługuje dwie metody rozproszonego szkolenia w TensorFlow:

- Szkolenie rozproszone [oparte na MPI](https://www.open-mpi.org/) za pomocą platformy [Horovod](https://github.com/uber/horovod)
- Natywne [rozproszone TensorFlow](https://www.tensorflow.org/deploy/distributed) przy użyciu metody serwera parametrów

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) to struktura "open source" dla szkolenia rozproszonego opracowanego przez Uber. Oferuje łatwą ścieżkę do dystrybuowanych zadań TensorFlow procesora GPU.

Aby użyć Horovod, Określ obiekt [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) dla parametru `distributed_training` w konstruktorze TensorFlow. Ten parametr zapewnia, że biblioteka Horovod jest zainstalowana do użycia w skrypcie szkoleniowym.

```Python
from azureml.core.runconfig import MpiConfiguration
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]')
```

### <a name="parameter-server"></a>Serwer parametrów

Można również uruchomić [native rozproszonych TensorFlow](https://www.tensorflow.org/deploy/distributed), parametr modelem serwera, który używa. W przypadku tej metody szkolenie w klastrze serwerów parametru i procesów roboczych. Procesy robocze obliczania gradientów podczas szkolenia, gdy serwery parametr agregacji gradientów.

Aby użyć metody serwera parametrów, należy określić obiekt [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) dla parametru `distributed_training` w konstruktorze TensorFlow.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]')

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>Zdefiniuj specyfikacje klastra w "TF_CONFIG"

Potrzebne są również adresy sieciowe i porty klastra dla [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), więc Azure Machine Learning ustawia zmienną środowiskową `TF_CONFIG`.

`TF_CONFIG` Zmienna środowiskowa jest ciąg JSON. Oto przykład zmiennej serwera parametru:

```JSON
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

W przypadku interfejsu API [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) wysokiego poziomu TensorFlow TensorFlow analizuje zmienną `TF_CONFIG` i kompiluje specyfikację klastra.

W przypadku podstawowych interfejsów API TensorFlow na niższym poziomie na potrzeby szkoleń należy analizować zmienną `TF_CONFIG` i utworzyć `tf.train.ClusterSpec` w kodzie szkoleniowym.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="deployment"></a>Wdrażanie

Właśnie zarejestrowany model można wdrożyć w taki sam sposób jak każdy inny zarejestrowany model w Azure Machine Learning, niezależnie od tego, który szacowania używany do uczenia się. Wdrożenie How-to zawiera sekcję dotyczącą rejestrowania modeli, ale możesz przejść bezpośrednio do tworzenia celu [obliczeń](how-to-deploy-and-where.md#choose-a-compute-target) dla wdrożenia, ponieważ istnieje już zarejestrowany model.

### <a name="preview-no-code-model-deployment"></a>Przeglądania Wdrożenie modelu bez kodu

Zamiast tradycyjnej trasy wdrożenia można również użyć funkcji wdrażania bez kodu (wersja zapoznawcza) dla Tensorflow. Rejestrując model, jak pokazano powyżej, za pomocą parametrów `model_framework`, `model_framework_version`i `resource_configuration`, można po prostu użyć funkcji statycznej `deploy()` do wdrożenia modelu.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

Pełne [Omówienie](how-to-deploy-and-where.md) wdrażania w Azure Machine Learning.

## <a name="next-steps"></a>Następne kroki

W tym artykule został przeszkolony i zarejestrowany model TensorFlow oraz zapoznaj się z opcjami wdrażania. Zapoznaj się z innymi artykułami, aby dowiedzieć się więcej na temat Azure Machine Learning.

* [Śledzenie metryk są uruchamiane podczas szkolenia](how-to-track-experiments.md)
* [Dostosowywanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Architektura referencyjna na potrzeby rozproszonego szkolenia uczenia głębokiego na platformie Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
