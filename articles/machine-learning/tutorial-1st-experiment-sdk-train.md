---
title: 'Samouczek: uczenie swojego pierwszego modelu uczenia maszynowego — Python'
titleSuffix: Azure Machine Learning
description: Część 3 Azure Machine Learning Rozpoczynanie pracy seria pokazuje, jak szkolić model uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: a267231dd447b114c69e6ead20c8ab5252f85d0e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90896722"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-3-of-4"></a>Samouczek: uczenie swojego pierwszego modelu uczenia maszynowego (część 3 z 4)

W tym samouczku przedstawiono sposób uczenia modelu uczenia maszynowego w Azure Machine Learning.

Ten samouczek jest **trzecią częścią serii samouczków z czterema częściami** , w której przedstawiono podstawowe informacje na temat Azure Machine Learning i zakończenia zadań uczenia maszynowego opartych na zadaniach na platformie Azure. Ten samouczek kompiluje pracę wykonaną w [części 1: Konfiguracja](tutorial-1st-experiment-sdk-setup-local.md) i [część 2: przebieg "Hello World"](tutorial-1st-experiment-hello-world.md) serii.

W tym samouczku zostanie przemieszczony następny krok, przesyłając skrypt, który pociągnie za model uczenia maszynowego. Ten przykład pomoże Ci zrozumieć, jak Azure Machine Learning ułatwia zachowanie spójności między debugowaniem lokalnym i zdalnym.

W tym samouczku:

> [!div class="checklist"]
> * Utwórz skrypt szkoleniowy.
> * Użyj Conda, aby zdefiniować środowisko Azure Machine Learning.
> * Utwórz skrypt kontrolny.
> * Poznaj klasy Azure Machine Learning (środowisko, przebieg, metryki).
> * Prześlij i uruchom skrypt szkoleniowy.
> * Wyświetlanie danych wyjściowych kodu w chmurze.
> * Rejestruj metryki do Azure Machine Learning.
> * Wyświetl metryki w chmurze.

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ [część 1](tutorial-1st-experiment-sdk-setup-local.md) , jeśli nie masz jeszcze obszaru roboczego Azure Machine Learning.
* Wstępna wiedza dotycząca języka Python i przepływów pracy uczenia maszynowego.
* Lokalne środowisko programistyczne. Obejmuje to, ale nie jest ograniczony do Visual Studio Code, Jupyter lub platformy PyCharm itd.
* Python (wersja 3.5-3.7).

## <a name="create-training-scripts"></a>Tworzenie skryptów szkoleniowych

Najpierw Zdefiniuj architekturę sieci neuronowych w `model.py` pliku. Cały kod szkoleniowy zostanie uwzględniony w `src` podkatalogu — w tym `model.py` .

Kod poniżej jest pobierany z [tego przykładu wprowadzającego](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) z PyTorch. Należy zauważyć, że pojęcia Azure Machine Learning dotyczą dowolnego kodu uczenia maszynowego, a nie tylko PyTorch.

```python
# tutorial/src/model.py
import torch.nn as nn
import torch.nn.functional as F


class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.conv1 = nn.Conv2d(3, 6, 5)
        self.pool = nn.MaxPool2d(2, 2)
        self.conv2 = nn.Conv2d(6, 16, 5)
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))
        x = self.pool(F.relu(self.conv2(x)))
        x = x.view(-1, 16 * 5 * 5)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x
```

Następnie zdefiniujesz skrypt szkoleniowy. Ten skrypt Pobiera zestaw danych CIFAR10 przy użyciu `torchvision.dataset` interfejsów API PyTorch, konfiguruje sieć zdefiniowaną w `model.py` i pociąga za dwie epoki przy użyciu standardowej SGDu i straty z przecięciami.

Utwórz `train.py` skrypt w `src` podkatalogu:

```python
# tutorial/src/train.py
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net

# download CIFAR 10 data
trainset = torchvision.datasets.CIFAR10(
    root="./data",
    train=True,
    download=True,
    transform=torchvision.transforms.ToTensor(),
)
trainloader = torch.utils.data.DataLoader(
    trainset, batch_size=4, shuffle=True, num_workers=2
)

if __name__ == "__main__":

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)

    # train the network
    for epoch in range(2):

        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # unpack the data
            inputs, labels = data

            # zero the parameter gradients
            optimizer.zero_grad()

            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()

            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:
                loss = running_loss / 2000
                print(f"epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}")
                running_loss = 0.0

    print("Finished Training")

```

Masz teraz strukturę katalogów poniższą poniżej:

```txt
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
```

## <a name="define-a-python-environment"></a>Definiowanie środowiska języka Python

W celach demonstracyjnych będziemy używać środowiska Conda (kroki dla środowiska wirtualnego PIP są prawie identyczne).

Utwórz plik o nazwie `pytorch-env.yml` w `.azureml` ukrytym katalogu:

```yml
# tutorial/.azureml/pytorch-env.yml
name: pytorch-env
channels:
    - defaults
    - pytorch
dependencies:
    - python=3.6.2
    - pytorch
    - torchvision
```

To środowisko ma wszystkie zależności wymagane przez model i skrypt szkoleniowy. Zwróć uwagę na to, że nie ma zależności od zestawu SDK w języku Azure Machine Learning Python.

## <a name="test-locally"></a>Testuj lokalnie

Przetestuj skrypt lokalnie, używając tego środowiska w programie:

```bash
conda env create -f .azureml/pytorch-env.yml    # create conda environment
conda activate pytorch-env             # activate conda environment
python src/train.py                    # train model
```

Po uruchomieniu tego skryptu zobaczysz dane pobrane do katalogu o nazwie `tutorial/data` .

## <a name="create-the-control-script"></a>Utwórz skrypt kontrolny

Różnica w skrypcie kontroli poniżej i użyty do przesłania "Hello world" polega na dodaniu kilku dodatkowych wierszy w celu ustawienia środowiska.

Utwórz nowy plik w języku Python w `tutorial` katalogu o nazwie `04-run-pytorch.py` :

```python
# tutorial/04-run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='src', script='train.py', compute_target='cpu-cluster')

    # set up pytorch environment
    env = Environment.from_conda_specification(name='pytorch-env', file_path='.azureml/pytorch-env.yml')
    config.run_config.environment = env

    run = experiment.submit(config)

    aml_url = run.get_portal_url()
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>Zrozumienie zmian w kodzie

:::row:::
   :::column span="":::
      `env = Environment.from_conda_specification( ... )`
   :::column-end:::
   :::column span="2":::
      Azure Machine Learning oferuje koncepcję [środowiska](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true) , aby reprezentować przemieszczone w wersji środowisko Python w celu uruchamiania eksperymentów. Można łatwo utworzyć środowisko na podstawie lokalnego środowiska Conda lub PIP.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      Dodaje środowisko do [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true).
   :::column-end:::
:::row-end:::

## <a name="submit-run-to-azure-machine-learning"></a>Prześlij przebieg do Azure Machine Learning

W przypadku przełączenia lokalnych środowisk należy koniecznie przełączyć się z powrotem do środowiska z zainstalowanym zestawem SDK języka Python Azure Machine Learning i uruchomić następujące polecenie:

```bash
python 04-run-pytorch.py
```

>[!NOTE] 
> Przy pierwszym uruchomieniu tego skryptu Azure Machine Learning spowoduje skompilowanie nowego obrazu platformy Docker ze środowiska PyTorch. Wykonanie całego przebiegu może potrwać 5-10 minut. Możesz wyświetlić dzienniki kompilacji platformy Docker w Azure Machine Learning Studio: Postępuj zgodnie z linkiem do programu Machine Learning Studio > wybierz kartę "dane wyjściowe + dzienniki", > wybierz pozycję `20_image_build_log.txt` .
Ten obraz zostanie ponownie użyty w przyszłych uruchomieniach, co sprawia, że działają znacznie szybciej.

Po skompilowaniu obrazu wybierz opcję `70_driver_log.txt` wyświetlania danych wyjściowych skryptu szkoleniowego.

```txt
Downloading https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz to ./data/cifar-10-python.tar.gz
...
Files already downloaded and verified
epoch=1, batch= 2000: loss 2.19
epoch=1, batch= 4000: loss 1.82
epoch=1, batch= 6000: loss 1.66
epoch=1, batch= 8000: loss 1.58
epoch=1, batch=10000: loss 1.52
epoch=1, batch=12000: loss 1.47
epoch=2, batch= 2000: loss 1.39
epoch=2, batch= 4000: loss 1.38
epoch=2, batch= 6000: loss 1.37
epoch=2, batch= 8000: loss 1.33
epoch=2, batch=10000: loss 1.31
epoch=2, batch=12000: loss 1.27
Finished Training
```

> [!WARNING]
> Jeśli zobaczysz błąd, `Your total snapshot size exceeds the limit` oznacza to, że `data` katalog znajduje się w `source_directory` używanym w `ScriptRunConfig` .
> Upewnij się, że przesuniesz `data` poza `src` .

Środowiska można rejestrować w obszarze roboczym `env.register(ws)` , dzięki czemu można je łatwo udostępniać, ponownie wykorzystywać i korzystać z wersji. Środowiska ułatwiają odtworzenie poprzednich wyników i współpracę z zespołem.

Azure Machine Learning utrzymuje również kolekcję środowiska nadzorowanego. Te środowiska obejmują typowe scenariusze uczenia maszynowego i są obsługiwane przez buforowane obrazy platformy Docker. Buforowane obrazy platformy Docker sprawiają, że pierwsze uruchomienie zdalne przebiega szybciej.

W krótkim czasie używanie zarejestrowanych środowisk może zaoszczędzić czas. Więcej szczegółów można znaleźć w dokumentacji dotyczącej [środowisk](./how-to-use-environments.md)

## <a name="log-training-metrics"></a>Rejestrowanie metryk szkoleń

Teraz, gdy masz już szkolenia modeli w Azure Machine Learning, Rozpocznij śledzenie niektórych metryk wydajności.
Bieżący skrypt szkoleniowy drukuje metryki do terminalu. Azure Machine Learning udostępnia mechanizm rejestrowania metryk z większą funkcjonalnością. Dodawanie kilku wierszy kodu pozwala uzyskać możliwość wizualizacji metryk w Studio i porównywania metryk między wieloma przebiegami.

### <a name="modify-trainpy-to-include-logging"></a>Modyfikuj `train.py` , aby uwzględnić rejestrowanie

Zmodyfikuj swój `train.py` skrypt, aby uwzględnić dodatkowe dwa wiersze kodu:

```python
# train.py
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net
from azureml.core import Run


# ADDITIONAL CODE: get Azure Machine Learning run from the current context
run = Run.get_context()

# download CIFAR 10 data
trainset = torchvision.datasets.CIFAR10(root='./data', train=True, download=True, transform=torchvision.transforms.ToTensor())
trainloader = torch.utils.data.DataLoader(trainset, batch_size=4, shuffle=True, num_workers=2)

if __name__ == "__main__":

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)

    # train the network
    for epoch in range(2):

        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # unpack the data
            inputs, labels = data

            # zero the parameter gradients
            optimizer.zero_grad()

            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()

            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:
                loss = running_loss / 2000
                run.log('loss', loss) # ADDITIONAL CODE: log loss metric to Azure Machine Learning
                print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                running_loss = 0.0

    print('Finished Training')
```

#### <a name="understand-the-additional-two-lines-of-code"></a>Zapoznaj się z dodatkowymi dwoma wierszami kodu

W programie `train.py` dostęp do obiektu Run można uzyskać z _poziomu_ samego skryptu szkoleniowego przy użyciu `Run.get_context()` metody i używać go do rejestrowania metryk:

```python
# in train.py
run = Run.get_context()

...

run.log('loss', loss)
```

Metryki w Azure Machine Learning są:

- Zorganizowane przez eksperymenty i przebiegi, aby można było łatwo śledzić i porównywać metryki.
- Wyposażone w interfejs użytkownika, dzięki któremu można wizualizować wydajność szkoleń w programie Studio.
- Zaprojektowana do skalowania, dzięki czemu można zachować te korzyści nawet w przypadku uruchamiania setek eksperymentów.

### <a name="update-the-conda-environment-file"></a>Aktualizowanie pliku środowiska Conda

`train.py`Skrypt właśnie przejął nową zależność od `azureml.core` . Zaktualizuj `pytorch-env.yml` , aby uwzględnić tę zmianę:

```yaml
# tutorial/.azureml/pytorch-env.yml
name: pytorch-env
channels:
    - defaults
    - pytorch
dependencies:
    - python=3.6.2
    - pytorch
    - torchvision
    - pip
    - pip:
        - azureml-sdk
```

### <a name="submit-run-to-azure-machine-learning"></a>Prześlij przebieg do Azure Machine Learning
Prześlij ten skrypt jeszcze raz:

```bash
python 04-run-pytorch.py
```

Tym razem podczas odwiedzania programu Studio przejdź do karty "metryki", w której możesz teraz zobaczyć aktualizacje na żywo w ramach modelu strata szkoleniowego.

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="Wykres strat szkoleniowych na karcie metryki":::

## <a name="next-steps"></a>Następne kroki

W tej sesji uaktualniono z poziomu podstawowego "Witaj świecie!" skrypt do bardziej realistycznego skryptu szkoleniowego, który wymagał uruchomienia określonego środowiska języka Python. Dowiesz się, jak przełączyć lokalne środowisko Conda do chmury za pomocą środowisk Azure Machine Learning. Na koniec pokazano, jak w kilku wierszach kodu można rejestrować metryki do Azure Machine Learning.

Istnieją inne sposoby tworzenia środowisk Azure Machine Learning, w tym [z requirements.txtPIP ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#from-pip-requirements-name--file-path-), lub nawet [z istniejącego lokalnego środowiska Conda](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#from-existing-conda-environment-name--conda-environment-name-).

W następnej sesji zobaczysz, jak korzystać z danych w Azure Machine Learning, przekazując zestaw danych CIFAR10 na platformę Azure.

> [!div class="nextstepaction"]
> [Samouczek: przenoszenie własnych danych](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> Jeśli chcesz zakończyć serię samouczków, a nie postępować w następnym kroku, pamiętaj o [oczyszczeniu zasobów](tutorial-1st-experiment-bring-data.md#clean-up-resources)