---
title: Możliwość interpretacji modelu dla przebiegów lokalnych i zdalnych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uzyskać wyjaśnienia dotyczące sposobu, w jaki model uczenia maszynowego określa ważność funkcji i tworzy prognozy podczas korzystania z sdk usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: a479982eeac325c9774e3858ec51643e8ba699c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064041"
---
# <a name="model-interpretability-for-local-and-remote-runs"></a>Możliwość interpretacji modelu dla przebiegów lokalnych i zdalnych

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule nauczysz się używać pakietu interpretability zestawu SDK języka Azure Machine Learning Python, aby zrozumieć, dlaczego model dokonał jego prognoz. Omawiane kwestie:

* Interpretowanie modeli uczenia maszynowego przeszkolonych zarówno lokalnie, jak i na zdalnych zasobach obliczeniowych.
* Przechowuj lokalne i globalne wyjaśnienia w historii uruchamiania platformy Azure.
* Wyświetlanie wizualizacji interpretacyjnych w [studiu usługi Azure Machine Learning.](https://ml.azure.com)
* Wdrażanie objaśnienia oceniania za pomocą modelu.

Aby uzyskać więcej informacji, zobacz [możliwość interpretacji modelu w usłudze Azure Machine Learning](how-to-machine-learning-interpretability.md).

## <a name="local-interpretability"></a>Lokalna interpretacja

W poniższym przykładzie pokazano, jak używać pakietu interpretability lokalnie bez kontaktowania się z usługami platformy Azure.

1. W razie `pip install azureml-interpret` potrzeby użyj, aby uzyskać pakiet interpretability.

1. Trenuj przykładowy model w lokalnym notatniku Jupyter.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

1. Zadzwoń do objaśnienia lokalnie.
   * Aby zainicjować obiekt objaśnienia, przekaż model i niektóre dane szkoleniowe do konstruktora objaśnienia.
   * Aby twoje wyjaśnienia i wizualizacje były bardziej pouczające, można wybrać przekazywanie nazw funkcji i nazw klas wyjściowych, jeśli robi klasyfikację.

   Następujące bloki kodu pokazują, jak utworzyć wystąpienie `TabularExplainer` `MimicExplainer`obiektu `PFIExplainer` objaśnienia za pomocą programu , i lokalnie.
   * `TabularExplainer`wywołuje jeden z trzech objaśnień SHAP pod spodem (`TreeExplainer`, `DeepExplainer`, lub `KernelExplainer`).
   * `TabularExplainer`automatycznie wybiera najbardziej odpowiedni dla danego przypadku użycia, ale można wywołać każdy z jego trzech podstawowych objaśnień bezpośrednio.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    lub

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns.
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```

    lub

    ```python
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="overall-global-feature-importance-values"></a>Ogólnie rzecz biorąc, globalne wartości ważności funkcji

Zapoznaj się z poniższym przykładem, aby ułatwić uzyskanie globalnych wartości ważności funkcji.

```python

# you can use the training data or the test data here
global_explanation = explainer.explain_global(x_train)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_test)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="instance-level-local-feature-importance-values"></a>Wartości ważności funkcji na poziomie wystąpienia, lokalne wartości elementów

Pobierz lokalne wartości ważności funkcji, wywołując objaśnienia dla pojedynczego wystąpienia lub grupy wystąpień.
> [!NOTE]
> `PFIExplainer`nie obsługuje lokalnych wyjaśnień.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

## <a name="interpretability-for-remote-runs"></a>Możliwość interpretacji dla przebiegów zdalnych

Poniższy przykład pokazuje, jak `ExplanationClient` można użyć klasy, aby włączyć interpretację modelu dla zdalnych przebiegów. Jest koncepcyjnie podobny do procesu lokalnego, z wyjątkiem Ciebie:

* Użyj `ExplanationClient` w zdalnym uruchomieniu, aby przekazać kontekst interpretalności.
* Pobierz kontekst później w środowisku lokalnym.

1. W razie `pip install azureml-contrib-interpret` potrzeby użyj, aby uzyskać niezbędny pakiet.

1. Tworzenie skryptu szkoleniowego w lokalnym notesie Jupyter. Na przykład `train_explain.py`.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=feature_names, 
                                 classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

1. Skonfiguruj obliczenia usługi Azure Machine Learning jako cel obliczeniowy i prześlij przebieg szkolenia. Aby uzyskać [instrukcje, zobacz konfigurowanie obiektów docelowych obliczeń dla szkolenia modelu.](how-to-set-up-training-targets.md#amlcompute) Przykładowe [notesy](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) mogą również okazać się pomocne.

1. Pobierz wyjaśnienie w lokalnym notesie Jupyter.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="raw-feature-transformations"></a>Przekształcenia operacji nieprzetworzonych

Możesz zdecydować się na uzyskanie wyjaśnień w zakresie surowych, nieprzetworzonych funkcji, a nie zaprojektowanych funkcji. W przypadku tej opcji potok transformacji funkcji należy `train_explain.py`przekazać do objaśnienia w programie . W przeciwnym razie objaśnienie zawiera wyjaśnienia w zakresie funkcji inżynierii.

Format obsługiwanych przekształceń jest taki sam, jak opisano w [sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). Ogólnie rzecz biorąc wszelkie przekształcenia są obsługiwane tak długo, jak działają w jednej kolumnie, dzięki czemu jest jasne, że są one jeden do wielu.

Uzyskaj wyjaśnienie nieprzetworzonych `sklearn.compose.ColumnTransformer` operacji przy użyciu lub z listą dopasowanych krotek transformatora. W poniższym `sklearn.compose.ColumnTransformer`przykładzie użyto pliku .

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

W przypadku, gdy chcesz uruchomić przykład z listą dopasowanych krotek transformatora, użyj następującego kodu:

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="visualizations"></a>Wizualizacje

Po pobraniu wyjaśnień w lokalnym notesie Jupyter można użyć pulpitu nawigacyjnego wizualizacji, aby zrozumieć i zinterpretować model.

### <a name="global-visualizations"></a>Globalne wizualizacje

Poniższe wykresy zapewniają globalny widok wyszkolonego modelu wraz z jego prognoz i wyjaśnień.

|Działka|Opis|
|----|-----------|
|Eksploracja danych| Wyświetla przegląd zestawu danych wraz z wartościami przewidywania.|
|Globalne znaczenie|Pokazuje najlepsze K (konfigurowalne K) ważne funkcje na całym świecie. Pomaga zrozumieć globalne zachowanie modelu.|
|Eksploracja wyjaśnień|Pokazuje, jak funkcja wpływa na zmianę wartości prognozowania modelu lub prawdopodobieństwo wartości prognozowania. Pokazuje wpływ interakcji funkcji.|
|Znaczenie podsumowania|Używa lokalnych, wartości ważności funkcji we wszystkich punktach danych, aby pokazać rozkład wpływu każdej funkcji na wartość prognozowania.|

[![Pulpit nawigacyjny wizualizacji globalny](./media/how-to-machine-learning-interpretability-aml/global-charts.png)](./media/how-to-machine-learning-interpretability-aml/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Wizualizacje lokalne

Wykres lokalnego, charakterystycznego dla dowolnego punktu danych można załadować, wybierając pojedynczy punkt danych na wykresie.

|Działka|Opis|
|----|-----------|
|Znaczenie lokalne|Pokazuje najlepsze funkcje K (konfigurowalne K) na całym świecie. Pomaga zilustrować lokalne zachowanie modelu źródłowego w określonym punkcie danych.|
|Eksploracja perturbacji|Umożliwia zmiany wartości elementów wybranego punktu danych i obserwować wynikające z tego zmiany wartości przewidywania.|
|Indywidualne oczekiwania warunkowe (ICE)| Umożliwia zmianę wartości operacji z wartości minimalnej na wartość maksymalną. Pomaga zilustrować, jak zmienia się przewidywanie punktu danych po zmianie funkcji.|

[![Znaczenie funkcji lokalnego pulpitu nawigacyjnego wizualizacji](./media/how-to-machine-learning-interpretability-aml/local-charts.png)](./media/how-to-machine-learning-interpretability-aml/local-charts.png#lightbox)


[![Funkcja perturbacji funkcji pulpitu nawigacyjnego wizualizacji](./media/how-to-machine-learning-interpretability-aml/perturbation.gif)](./media/how-to-machine-learning-interpretability-aml/perturbation.gif#lightbox)


[![Wizualizacja Deska rozdzielcza ICE Wykresy](./media/how-to-machine-learning-interpretability-aml/ice-plot.png)](./media/how-to-machine-learning-interpretability-aml/ice-plot.png#lightbox)

> [!NOTE]
> Przed uruchomieniem jądra Jupyter upewnij się, że włączysz rozszerzenia widżetów dla pulpitu nawigacyjnego wizualizacji.

* Notesy programu Jupyter

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```

* Płyta Jupyter

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

Aby załadować pulpit nawigacyjny wizualizacji, użyj następującego kodu.

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Wizualizacja w studiu usługi Azure Machine Learning

Po wykonaniu kroków [zdalnej interpretacji](#interpretability-for-remote-runs) można wyświetlić pulpit nawigacyjny wizualizacji w [studiu usługi Azure Machine Learning.](https://ml.azure.com) Ten pulpit nawigacyjny jest prostszą wersją pulpitu nawigacyjnego wizualizacji wyjaśnioną powyżej. Obsługuje tylko dwie karty:

|Działka|Opis|
|----|-----------|
|Globalne znaczenie|Pokazuje najlepsze K (konfigurowalne K) ważne funkcje na całym świecie. Pomaga zrozumieć globalne zachowanie modelu.|
|Znaczenie podsumowania|Używa lokalnych, wartości ważności funkcji we wszystkich punktach danych, aby pokazać rozkład wpływu każdej funkcji na wartość prognozowania.|

Jeśli dostępne są objaśnienia globalne i lokalne, dane wypełnia obie karty. Jeśli dostępne jest tylko wyjaśnienie globalne, karta Ważność podsumowania jest wyłączona.

Postępuj zgodnie z jedną z tych ścieżek, aby uzyskać dostęp do pulpitu nawigacyjnego wizualizacji w studio usługi Azure Machine Learning:

* **Okienko Eksperymenty** (wersja zapoznawcza)
  1. Wybierz **eksperymenty** w lewym okienku, aby wyświetlić listę eksperymentów uruchomionych w usłudze Azure Machine Learning.
  1. Wybierz konkretny eksperyment, aby wyświetlić wszystkie przebiegi w tym eksperymencie.
  1. Wybierz bieg, a następnie kartę **Objaśnienia** do pulpitu nawigacyjnego wizualizacji objaśnień.

   [![Znaczenie funkcji lokalnego pulpitu nawigacyjnego wizualizacji](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png)](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png#lightbox)

* **Okienko Modele**
  1. Jeśli zarejestrowany oryginalny model, wykonując kroki w [wdrażaniu modeli z usługi Azure Machine Learning,](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)można wybrać **modele** w lewym okienku, aby go wyświetlić.
  1. Wybierz model, a następnie kartę **Objaśnienia,** aby wyświetlić pulpit nawigacyjny wizualizacji objaśnień.

## <a name="interpretability-at-inference-time"></a>Możliwość interpretacji w czasie wnioskowania

Można wdrożyć objaśnienia wraz z oryginalnego modelu i użyć go w czasie wnioskowania, aby zapewnić lokalne informacje objaśnienia. Oferujemy również objaśnienia punktacji o lżejszej wadze, aby poprawić wydajność tłumaczenia w czasie wnioskowania. Proces wdrażania objaśnienia punktacji o mniejszej masie jest podobny do wdrażania modelu i zawiera następujące kroki:

1. Utwórz obiekt objaśnienia. Można na przykład `TabularExplainer`użyć:

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Utwórz objaśnienie oceniania za pomocą obiektu objaśnienia.

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Konfigurowanie i rejestrowanie obrazu, który używa modelu objaśnienia oceniania.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Jako krok opcjonalny można pobrać objaśnienie oceniania z chmury i przetestować objaśnienia.

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Wdrażanie obrazu w celu obliczenia obiektu docelowego, wykonując następujące kroki:

   1. W razie potrzeby zarejestruj oryginalny model przewidywania, wykonując kroki opisane w [obszarze Wdrażanie modeli za pomocą usługi Azure Machine Learning.](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)

   1. Tworzenie pliku oceniania.

         ```python
         %%writefile score.py
         import json
         import numpy as np
         import pandas as pd
         import os
         import pickle
         from sklearn.externals import joblib
         from sklearn.linear_model import LogisticRegression
         from azureml.core.model import Model
          
         def init():
         
            global original_model
            global scoring_model
             
            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

         def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
         ```
   1. Zdefiniowanie konfiguracji wdrożenia.

         Ta konfiguracja zależy od wymagań modelu. Poniższy przykład definiuje konfigurację, która używa jednego rdzenia procesora CPU i jednego GB pamięci.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Tworzenie pliku z zależnościami środowiska.

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-interpret', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. Utwórz niestandardowy plik dockerfile z zainstalowanym g++.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. Wdrażanie utworzonego obrazu.
   
         Ten proces trwa około pięciu minut.

         ```python
         from azureml.core.webservice import Webservice
         from azureml.core.image import ContainerImage

         # use the custom scoring, docker, and conda files we created above
         image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                         docker_file="dockerfile",
                                                         runtime="python",
                                                         conda_file="myenv.yml")

         # use configs and models generated above
         service = Webservice.deploy_from_model(workspace=ws,
                                             name='model-scoring-service',
                                             deployment_config=aciconfig,
                                             models=[scoring_explainer_model, original_model],
                                             image_config=image_config)

         service.wait_for_deployment(show_output=True)
         ```

1. Przetestuj wdrożenie.

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Posprzątaj.

   Aby usunąć wdrożoną usługę `service.delete()`sieci web, użyj programu .

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o interpretacji modelu](how-to-machine-learning-interpretability.md)