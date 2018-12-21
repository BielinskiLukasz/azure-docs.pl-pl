---
title: 'Samouczek dotyczący klasyfikacji obrazów: Wdrażanie modeli'
titleSuffix: Azure Machine Learning service
description: W tym samouczku pokazano, jak za pomocą usługi Azure Machine Learning wdrożyć model klasyfikacji obrazów z pakietem scikit-learn w notesie Jupyter języka Python. Ten samouczek jest drugą częścią dwuczęściowej serii.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: hning86
ms.author: haining
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: ea446c89fc74fca444793a5e0f803a54fa251ed1
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53312174"
---
# <a name="tutorial--deploy-an-image-classification-model-in-azure-container-instance"></a>Samouczek:  wdrażanie modelu klasyfikacji obrazów w wystąpieniu kontenera platformy Azure

Ten samouczek jest **drugą częścią dwuczęściowej serii samouczków**. W [poprzednim samouczku](tutorial-train-models-with-aml.md) przeszkoliliśmy modele uczenia maszynowego, a następnie zarejestrowaliśmy model w Twoim obszarze roboczym w chmurze.  

Teraz możesz przystąpić do wdrażania modelu jako usługi internetowej w [usłudze Azure Container Instances](https://docs.microsoft.com/azure/container-instances/). Usługa internetowa jest obrazem, w tym przypadku obrazem platformy Docker, który hermetyzuje logikę oceniania i sam model. 

W tej części samouczka użyjesz usługi Azure Machine Learning, aby wykonać następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie środowiska testowego
> * Pobieranie modelu z obszaru roboczego
> * Testowanie modelu w środowisku lokalnym
> * Wdrażanie modelu w usłudze Container Instances
> * Testowanie wdrożonego modelu

Usługa Container Instances nie jest idealnym rozwiązaniem w przypadku wdrożeń produkcyjnych, ale znakomicie nadaje się do testowania i interpretowania przepływu pracy. W przypadku skalowalnych wdrożeń produkcyjnych rozważ skorzystanie z usługi Azure Kubernetes Service. Aby uzyskać więcej informacji, zobacz [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).

## <a name="get-the-notebook"></a>Pobieranie notesu

Dla Twojej wygody ten samouczek jest dostępny jako [notes Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part2-deploy.ipynb). Uruchom notes `tutorials/img-classification-part2-deploy.ipynb` w usłudze Azure Notebooks lub na własnym serwerze notesów Jupyter.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

>[!NOTE]
> Kod w tym artykule został przetestowany przy użyciu zestawu Azure Machine Learning SDK w wersji 1.0.2.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj szkolenie modelu przy użyciu następującego notesu: [Samouczek nr 1: uczenie modelu klasyfikacji obrazów za pomocą usługi Azure Machine Learning](tutorial-train-models-with-aml.md).  


## <a name="set-up-the-environment"></a>Konfigurowanie środowiska

Zacznij od skonfigurowania środowiska testowego.

### <a name="import-packages"></a>Importowanie pakietów

Zaimportuj pakiety języka Python potrzebne w tym samouczku.

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
 
import azureml
from azureml.core import Workspace, Run

# display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="retrieve-the-model"></a>Pobieranie modelu

W poprzednim samouczku w Twoim obszarze roboczym został zarejestrowany model. Teraz załaduj ten obszar roboczy i pobierz model do katalogu lokalnego.


```python
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model=Model(ws, 'sklearn_mnist')
model.download(target_dir = '.')
import os 
# verify the downloaded model file
os.stat('./sklearn_mnist_model.pkl')
```

## <a name="test-model-locally"></a>Testowanie modelu w środowisku lokalnym

Przed wdrożeniem upewnij się, że model działa w środowisku lokalnym, wykonując następujące czynności:
* Ładowanie danych testowych
* Przewidywanie danych testowych
* Badanie macierzy pomyłek

### <a name="load-test-data"></a>Ładowanie danych testowych

Załaduj dane testowe z katalogu **./data/** utworzonego w ramach samouczka dotyczącego szkolenia.

```python
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster

X_test = load_data('./data/test-images.gz', False) / 255.0
y_test = load_data('./data/test-labels.gz', True).reshape(-1)

```

### <a name="predict-test-data"></a>Przewidywanie danych testowych

Dostarcz do modelu zestaw danych testowych, aby uzyskać przewidywania.

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load('./sklearn_mnist_model.pkl')
y_hat = clf.predict(X_test)
```

###  <a name="examine-the-confusion-matrix"></a>Badanie macierzy pomyłek

Wygeneruj macierz pomyłek, aby sprawdzić, ile próbek z zestawu testowego zostało poprawnie sklasyfikowanych. Zwróć uwagę na błędnie sklasyfikowane wartości niepoprawnych przewidywań. 

```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

Dane wyjściowe przedstawiają macierz pomyłek:

    [[ 960    0    1    2    1    5    6    3    1    1]
     [   0 1112    3    1    0    1    5    1   12    0]
     [   9    8  920   20   10    4   10   11   37    3]
     [   4    0   17  921    2   21    4   12   20    9]
     [   1    2    5    3  915    0   10    2    6   38]
     [  10    2    0   41   10  770   17    7   28    7]
     [   9    3    7    2    6   20  907    1    3    0]
     [   2    7   22    5    8    1    1  950    5   27]
     [  10   15    5   21   15   27    7   11  851   12]
     [   7    8    2   13   32   13    0   24   12  898]]
    Overall accuracy: 0.9204
   

Użyj polecenia `matplotlib`, aby wyświetlić macierz pomyłek w postaci wykresu. Na tym wykresie oś X reprezentuje wartości rzeczywiste, a oś Y — przewidywane. Kolor w każdej siatce oznacza współczynnik błędów. Im jaśniejszy kolor, tym wyższy współczynnik błędów. Na przykład wiele wartości 5 zostało błędnie sklasyfikowanych jako 3. Dlatego w położeniu (5,3) widać jasny element siatki.

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8,5))
ax = fig.add_subplot(111)
cax = ax.matshow(norm_conf_mx, cmap=plt.cm.bone)
ticks = np.arange(0, 10, 1)
ax.set_xticks(ticks)
ax.set_yticks(ticks)
ax.set_xticklabels(ticks)
ax.set_yticklabels(ticks)
fig.colorbar(cax)
plt.ylabel('true labels', fontsize=14)
plt.xlabel('predicted values', fontsize=14)
plt.savefig('conf.png')
plt.show()
```

![Wykres przedstawiający macierz pomyłek](./media/tutorial-deploy-models-with-aml/confusion.png)

## <a name="deploy-as-web-service"></a>Wdrażanie w postaci usługi internetowej

Gdy model został przetestowany i wyniki są zadowalające, wdróż model jako usługę internetową hostowaną w usłudze Container Instances. 

Aby utworzyć odpowiednie środowisko dla usługi Container Instances, dostarcz następujące elementy:
* Skrypt oceniania pokazujący, jak korzystać z modelu
* Plik środowiska pokazujący, jakie pakiety trzeba zainstalować
* Plik konfiguracji umożliwiający utworzenie wystąpienia kontenera
* Przeszkolony wcześniej model

<a name="make-script"></a>

### <a name="create-scoring-script"></a>Tworzenie skryptu oceniania

Utwórz skrypt oceniania o nazwie score.py. Wywołanie usługi internetowej używa tego skryptu jako instrukcji korzystania z modelu.

W skrypcie oceniania muszą znaleźć się dwie wymagane funkcje:
* Funkcja `init()`, która zazwyczaj ładuje model do obiektu globalnego. Ta funkcja jest uruchamiana tylko raz, po uruchomieniu kontenera platformy Docker. 

* Funkcja `run(input_data)` używa modelu do przewidywania wartości w oparciu o dane wejściowe. Dane wejściowe i wyjściowe do uruchomienia zazwyczaj używają formatu JSON na potrzeby serializacji i deserializacji, ale obsługiwane są też inne formaty.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression

from azureml.core.model import Model

def init():
    global model
    # retreive the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

<a name="make-myenv"></a>

### <a name="create-environment-file"></a>Tworzenie pliku środowiska

Następnie utwórz plik środowiska o nazwie myenv.yml, który określa wszystkie zależności pakietu skryptu. Ten plik gwarantuje zainstalowanie tych wszystkich zależności w obrazie platformy Docker. Ten model wymaga pakietów `scikit-learn` i `azureml-sdk`.

```python
from azureml.core.conda_dependencies import CondaDependencies 

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
```
Przejrzyj zawartość pliku `myenv.yml`.

```python
with open("myenv.yml","r") as f:
    print(f.read())
```

### <a name="create-configuration-file"></a>Tworzenie pliku konfiguracji

Utwórz plik konfiguracji wdrożenia i określ liczbę procesorów oraz wielkość pamięci RAM (w GB) wymaganej dla kontenera usługi Container Instances. Chociaż zależy to od modelu, wartość domyślna określająca 1 rdzeń i 1 GB pamięci RAM zazwyczaj jest wystarczająca dla wielu modeli. Jeśli w przyszłości będzie potrzeba więcej, trzeba będzie ponownie utworzyć obraz i jeszcze raz wdrożyć usługę.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-container-instances"></a>Wdrażanie w usłudze Container Instances
Szacowany czas trwania: **ok. 7–8 min**

Skonfiguruj i wdróż obraz. Poniższy kod wykonuje następujące kroki:

1. Utworzenie obrazu z następujących elementów:
   * Plik oceniania (`score.py`)
   * Plik środowiska (`myenv.yml`)
   * Plik modelu
1. Zarejestrowanie tego obrazu w obszarze roboczym. 
1. Wysłanie obrazu do kontenera usługi Container Instances.
1. Uruchomienie kontenera w usłudze Container Instances przy użyciu obrazu.
1. Pobranie punktu końcowego HTTP usługi internetowej.


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage

# configure the image
image_config = ContainerImage.image_configuration(execution_script="score.py", 
                                                  runtime="python", 
                                                  conda_file="myenv.yml")

service = Webservice.deploy_from_model(workspace=ws,
                                       name='sklearn-mnist-svc',
                                       deployment_config=aciconfig,
                                       models=[model],
                                       image_config=image_config)

service.wait_for_deployment(show_output=True)
```

Pobierz punkt końcowy HTTP usługi internetowej oceniania akceptujący wywołania klienta REST. Ten punkt końcowy można udostępnić każdemu, kto chce przetestować usługę internetową lub zintegrować ją z aplikacją. 

```python
print(service.scoring_uri)
```


## <a name="test-deployed-service"></a>Testowanie wdrożonej usługi

Wcześniej wszystkie dane testowe zostały ocenione za pomocą lokalnej wersji modelu. Teraz możesz przetestować wdrożony model przy użyciu losowej próbki 30 obrazów z danych testowych.  

Poniższy kod wykonuje następujące kroki:
1. Wysłanie danych w postaci tablicy JSON do usługi internetowej hostowanej w usłudze Container Instances. 

1. Wywołanie usługi przy użyciu interfejsu API `run` zestawu SDK. Możesz również wykonać wywołania nieprzetworzone, używając dowolnego narzędzia HTTP, takiego jak curl.

1. Wydrukowanie zwróconych przewidywań i wykreślenie ich razem z obrazami wejściowymi. Do wyróżniania błędnie sklasyfikowanych próbek służy czerwona czcionka i odwrócony obraz (biały na czarnym tle). 

 Ponieważ dokładność modelu jest wysoka, może być konieczne kilkukrotne uruchomienie poniższego kodu, zanim uda się zobaczyć błędnie sklasyfikowaną próbkę.

```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding = 'utf8')

# predict using the deployed model
result = json.loads(service.run(input_data=test_samples))

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize = (20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')
    
    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys
    
    plt.text(x=10, y =-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

Oto wynik jednej losowej próbki obrazów testowych: ![Grafika przedstawiająca wyniki](./media/tutorial-deploy-models-with-aml/results.png)

W celu przetestowania usługi internetowej możesz również wysłać nieprzetworzone żądanie HTTP.

```python
import requests
import json

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type':'application/json'}

# for AKS deployment you'd need to the service key in the header as well
# api_key = service.get_key()
# headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ api_key)} 

resp = requests.post(service.scoring_uri, input_data, headers=headers)

print("POST to url", service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby pozostawić grupę zasobów i obszar roboczy na potrzeby innych samouczków i eksploracji, możesz usunąć tylko wdrożenie usługi Container Instances, używając następującego wywołania interfejsu API:

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Następne kroki

+ Poznaj wszystkie [opcje wdrażania usługi Azure Machine Learning](how-to-deploy-and-where.md), w tym usługę ACI, usługę Azure Kubernetes Service, układy FPGA i usługę IoT Edge.

+ Zobacz, jak usługa Azure Machine Learning automatycznie wybiera i dostosowuje najlepszy algorytm dla modelu i tworzy ten model dla Ciebie. Wypróbuj samouczek dotyczący [algorytmu automatycznego wyboru](tutorial-auto-train-models.md). 