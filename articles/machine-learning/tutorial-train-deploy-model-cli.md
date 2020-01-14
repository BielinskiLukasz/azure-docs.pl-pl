---
title: Uczenie i wdrażanie modeli z interfejsu wiersza polecenia
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać rozszerzenia Uczenie maszynowe dla interfejsu wiersza polecenia platformy Azure, aby uczenie, zarejestrować i wdrożyć model z wiersza poleceń.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: f920df20a8dc1cace76f641ce1c71f9b91a30bf4
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867666"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Samouczek: uczenie i wdrażanie modelu z poziomu interfejsu wiersza polecenia
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym samouczku użyjesz rozszerzenia Machine Learning dla interfejsu wiersza polecenia platformy Azure, aby nauczyć, zarejestrować i wdrożyć model.

Skrypty szkoleniowe dotyczące języka Python w tym samouczku wykorzystują [scikit — Dowiedz się, jak](https://scikit-learn.org/) nauczyć model podstawowy. Fokus tego samouczka nie znajduje się na skryptach lub modelu, ale proces używania interfejsu wiersza polecenia do pracy z Azure Machine Learning.

Dowiedz się, jak wykonać następujące czynności:

> [!div class="checklist"]
> * Zainstaluj rozszerzenie uczenia maszynowego
> * Tworzenie obszaru roboczego usługi Azure Machine Learning
> * Utwórz zasób obliczeniowy służący do uczenia modelu
> * Definiowanie i rejestrowanie zestawu danych używanego do uczenia modelu
> * Rozpocznij pracę szkoleniową
> * Rejestrowanie i pobieranie modelu
> * Wdróż model jako usługę sieci Web
> * Dane oceny przy użyciu usługi sieci Web

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

* Aby korzystać z poleceń interfejsu wiersza polecenia w tym dokumencie ze **środowiska lokalnego**, wymagany jest [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Jeśli używasz [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/), interfejs wiersza polecenia jest dostępny za pomocą przeglądarki i przebywa w chmurze.

## <a name="download-the-example-project"></a>Pobierz przykładowy projekt

Na potrzeby tego samouczka Pobierz projekt [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) . Pliki w katalogu `examples/cli-train-deploy` są używane przez kroki opisane w tym samouczku.

Aby uzyskać kopię lokalną plików, [Pobierz archiwum zip](https://github.com/microsoft/MLOps/archive/master.zip)lub użyj następującego polecenia git, aby sklonować repozytorium:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Pliki szkoleniowe

Katalog `examples/cli-train-deploy` z projektu zawiera następujące pliki, które są używane podczas uczenia modelu:

* `.azureml\mnist.runconfig`: plik __konfiguracji uruchomieniowej__ . Ten plik definiuje środowisko uruchomieniowe potrzebne do uczenia modelu. W tym przykładzie instaluje również dane używane do uczenia modelu w środowisku szkoleniowym.
* `scripts\train.py`: skrypt szkoleniowy. Ten plik pociąga za model.
* `scripts\utils.py`: plik pomocnika używany przez skrypt szkoleniowy.
* `.azureml\conda_dependencies.yml`: definiuje zależności oprogramowania wymagające do uruchomienia skryptu szkoleniowego.
* `dataset.json`: definicja zestawu danych. Służy do rejestrowania zestawu danych MNIST ręcznie w obszarze roboczym Azure Machine Learning.

### <a name="deployment-files"></a>Pliki wdrożenia

Repozytorium zawiera następujące pliki, które są używane do wdrożenia przeszkolonego modelu jako usługi sieci Web:

* `aciDeploymentConfig.yml`: plik __konfiguracji wdrożenia__ . Ten plik definiuje środowisko hostingu, które jest niezbędne dla modelu.
* `inferenceConfig.yml`: wnioskowanie configuration__ pliku. Ten plik definiuje środowisko oprogramowania używane przez usługę do oceny danych w modelu.
* `score.py`: skrypt języka Python, który akceptuje dane przychodzące, ocenia je przy użyciu modelu, a następnie zwraca odpowiedź.
* `scoring-env.yml`: Conda zależności, które są konieczne do uruchomienia modelu i `score.py` skryptu.
* `testdata.json`: plik danych, którego można użyć do przetestowania wdrożonej usługi sieci Web.

## <a name="connect-to-your-azure-subscription"></a>Nawiązywanie połączenia z subskrypcją platformy Azure

Istnieje kilka sposobów uwierzytelniania w ramach subskrypcji platformy Azure z poziomu interfejsu wiersza polecenia. Najbardziej podstawowa jest możliwość interakcyjnego uwierzytelniania przy użyciu przeglądarki. Aby uwierzytelnić interaktywnie, Otwórz wiersz polecenia lub terminal i użyj następującego polecenia:

```azurecli-interactive
az login
```

Jeśli interfejs wiersza polecenia może otworzyć Twoją domyślną przeglądarkę, zrobi to i załaduje stronę logowania. W przeciwnym razie musisz otworzyć przeglądarkę i postępować zgodnie z instrukcjami w wierszu polecenia. Instrukcje obejmują przechodzenie do [https://aka.ms/devicelogin](https://aka.ms/devicelogin) i wprowadzanie kodu autoryzacji.

## <a name="install-the-machine-learning-extension"></a>Zainstaluj rozszerzenie uczenia maszynowego

Aby zainstalować rozszerzenie Uczenie maszynowe, użyj następującego polecenia:

```azurecli-interactive
az extension add -n azure-cli-ml
```

Jeśli zostanie wyświetlony komunikat o tym, że rozszerzenie jest już zainstalowane, użyj następującego polecenia, aby zaktualizować najnowszą wersję:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to podstawowy kontener zasobów na platformie Azure. Podczas pracy z Azure Machine Learning Grupa zasobów będzie zawierać obszar roboczy Azure Machine Learning. Będzie również zawierać inne usługi platformy Azure używane przez obszar roboczy. Na przykład jeśli nauczysz model przy użyciu zasobów obliczeniowych opartych na chmurze, ten zasób zostanie utworzony w grupie zasobów.

Aby __utworzyć nową grupę zasobów__, użyj następującego polecenia. Zastąp `<resource-group-name>` nazwą, która ma być używana dla tej grupy zasobów. Zastąp `<location>` w regionie świadczenia usługi Azure, który ma być używany dla tej grupy zasobów:

> [!TIP]
> Należy wybrać region, w którym Azure Machine Learning jest dostępna. Aby uzyskać więcej informacji, zobacz [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Odpowiedź z tego polecenia jest podobna do następującej:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Aby uzyskać więcej informacji na temat pracy z grupami zasobów, zobacz [AZ Group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Aby utworzyć nowy obszar roboczy, użyj następującego polecenia. Zastąp `<workspace-name>` nazwą, której chcesz użyć dla tego obszaru roboczego. Zastąp `<resource-group-name>` nazwą grupy zasobów:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

Dane wyjściowe tego polecenia są podobne do następujących:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="connect-local-project-to-workspace"></a>Połącz projekt lokalny z obszarem roboczym

Z poziomu terminalu lub wiersza polecenia Użyj następujących poleceń zmień katalogi na katalog `cli-train-deploy`, a następnie połącz się z obszarem roboczym:

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

Dane wyjściowe tego polecenia są podobne do następujących:

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

To polecenie tworzy plik `.azureml/config.json`, który zawiera informacje, które są konieczne do nawiązania połączenia z obszarem roboczym. Pozostałe polecenia `az ml` użyte w tym samouczku będą używać tego pliku, więc nie trzeba dodawać obszaru roboczego i grupy zasobów do wszystkich poleceń.

## <a name="create-the-compute-target-for-training"></a>Utwórz element docelowy obliczeń na potrzeby szkolenia

W tym przykładzie zastosowano Azure Machine Learning klaster obliczeniowy do uczenia modelu. Aby utworzyć nowy klaster obliczeniowy, użyj następującego polecenia:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

Dane wyjściowe tego polecenia są podobne do następujących:

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

To polecenie tworzy nowy obiekt docelowy obliczeń o nazwie `cpu`, z maksymalnie czterema węzłami. Wybrany rozmiar maszyny wirtualnej zapewnia maszynę wirtualną z zasobem procesora GPU. Aby uzyskać informacje na temat rozmiaru maszyny wirtualnej, zobacz [typy maszyn wirtualnych i rozmiary].

> [!IMPORTANT]
> Nazwa elementu docelowego obliczeń (`cpu` w tym przypadku) jest ważna; odwołuje się do niego plik `.azureml/mnist.runconfig` używany w następnej sekcji.

## <a name="define-the-dataset"></a>Zdefiniuj zestaw danych

Aby szkolić model, możesz dostarczyć dane szkoleniowe za pomocą zestawu danych. Aby utworzyć zestaw danych z interfejsu wiersza polecenia, należy podać plik definicji zestawu danych. Plik `dataset.json` podany w repozytorium tworzy nowy zestaw danych przy użyciu danych MNIST ręcznie. Tworzony zestaw danych ma nazwę `mnist-dataset`.

Aby zarejestrować zestaw danych przy użyciu pliku `dataset.json`, użyj następującego polecenia:

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

Dane wyjściowe tego polecenia są podobne do następujących:

```json
{
  "definition": [
    "GetFiles"
  ],
  "registration": {
    "description": "mnist dataset",
    "id": "a13a4034-02d1-40bd-8107-b5d591a464b7",
    "name": "mnist-dataset",
    "tags": {
      "sample-tag": "mnist"
    },
    "version": 1,
    "workspace": "Workspace.create(name='myworkspace', subscription_id='mysubscriptionid', resource_group='myresourcegroup')"
  },
  "source": [
    "http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz"
  ]
}
```


> [!IMPORTANT]
> Skopiuj wartość wpisu `id`, ponieważ jest ona używana w następnej sekcji.

Aby zapoznać się z bardziej szczegółowym szablonem pliku JSON opisującym zestaw danych, użyj następującego polecenia:
```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>Odwołuje się do zestawu danych

Aby udostępnić zestaw danych w środowisku szkoleniowym, należy odwołać się do niego z pliku runconfig. Plik `.azureml/mnist.runconfig` zawiera następujące wpisy YAML:

```yaml
# The arguments to the script file.
arguments:
- --data-folder
- DatasetConsumptionConfig:mnist

.....

# The configuration details for data.
data:
  mnist:
# Data Location
    dataLocation:
# the Dataset used for this run.
      dataset:
# Id of the dataset.
        id: a13a4034-02d1-40bd-8107-b5d591a464b7
# the DataPath used for this run.
      datapath:
# Whether to create new folder.
    createOutputDirectories: false
# The mode to handle
    mechanism: mount
# Point where the data is download or mount or upload.
    environmentVariableName: mnist
# relative path where the data is download or mount or upload.
    pathOnCompute:
# Whether to overwrite the data if existing.
    overwrite: false
```

Zmień wartość wpisu `id`, aby odpowiadała wartości zwróconej podczas rejestrowania zestawu danych. Ta wartość jest używana do ładowania danych do obiektu docelowego obliczeń podczas szkolenia.

YAML wykonuje następujące czynności:

* Instaluje zestaw danych (na podstawie identyfikatora zestawu danych) w środowisku szkoleniowym i zapisuje ścieżkę do punktu instalacji w zmiennej środowiskowej `mnist`.
* Przekazuje lokalizację danych (punkt instalacji) w środowisku szkoleniowym do skryptu przy użyciu argumentu `--data-folder`.

Plik runconfig zawiera również informacje służące do konfigurowania środowiska używanego w ramach przebiegu szkoleniowego. Po sprawdzeniu tego pliku zobaczysz, że odwołuje się do utworzonego wcześniej elementu docelowego obliczeń `cpu-compute`. Zawiera również listę liczby węzłów, które mają być używane podczas uczenia (`"nodeCount": "4"`) i zawiera sekcję `"condaDependencies"`, w której znajdują się pakiety języka Python niezbędne do uruchomienia skryptu szkoleniowego.

> [!TIP]
> Chociaż istnieje możliwość ręcznego utworzenia pliku runconfig, w tym przykładzie został utworzony za pomocą pliku `generate-runconfig.py` zawartego w repozytorium. Ten plik pobiera odwołanie do zarejestrowanego zestawu danych, tworzy konfigurację uruchomieniową programowo, a następnie utrwala ją z plikiem.

Aby uzyskać więcej informacji na temat uruchamiania plików konfiguracji, zobacz [Konfigurowanie elementów docelowych obliczeń i używanie ich do uczenia modelu](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli)lub odwoływanie się do tego [pliku JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) , aby zobaczyć pełny schemat dla runconfig.

## <a name="submit-the-training-run"></a>Prześlij przebieg szkolenia

Aby rozpocząć szkolenie w miejscu docelowym obliczeń `cpu-compute`, użyj następującego polecenia:

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

To polecenie określa nazwę eksperymentu (`myexperiment`). Eksperyment zapisuje informacje o tym przebiegu w obszarze roboczym.

`-c mnist` parametr określa plik `.azureml/mnist.runconfig`.

Parametr `-t` przechowuje odwołanie do tego przebiegu w pliku JSON i zostanie użyte w następnych krokach w celu zarejestrowania i pobrania modelu.

W miarę przebiegu szkolenia program przesyła strumieniowo informacje z sesji szkoleniowej do zdalnego zasobu obliczeniowego. Część informacji jest podobna do poniższego tekstu:

```text
Predict the test set
Accuracy is 0.9185
```

Ten tekst jest rejestrowany ze skryptu szkoleniowego i zawiera dokładność modelu. Inne modele będą mieć różne metryki wydajności.

W przypadku inspekcji skryptu szkoleniowego należy zauważyć, że program używa również wartości alfa, gdy przechowuje przeszkolony model do `outputs/sklearn_mnist_model.pkl`.

Model został zapisany w katalogu `./outputs` w miejscu docelowym obliczeń, w którym został przeszkolony. W tym przypadku wystąpienie obliczeniowe Azure Machine Learning w chmurze platformy Azure. Proces uczenia automatycznie przekazuje zawartość katalogu `./outputs` z elementu docelowego obliczeń, w którym szkolenia odbywają się w obszarze roboczym Azure Machine Learning. Jest ona przechowywana w ramach eksperymentu (`myexperiment` w tym przykładzie).

## <a name="register-the-model"></a>Rejestrowanie modelu

Aby zarejestrować model bezpośrednio z wersji zapisanej w eksperymentie, użyj następującego polecenia:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

To polecenie rejestruje plik `outputs/sklearn_mnist_model.pkl` utworzony w ramach szkolenia przebiegu jako nowy model rejestracji o nazwie `mymodel`. `--assets-path` odwołuje się do ścieżki w eksperymentie. W takim przypadku eksperyment i informacje o przebiegu są ładowane z pliku `runoutput.json` utworzonego przez polecenie szkolenia. `-t registeredmodel.json` tworzy plik JSON, który odwołuje się do nowego zarejestrowanego modelu utworzonego przez to polecenie, i jest używany przez inne polecenia CLI, które działają z zarejestrowanymi modelami.

Dane wyjściowe tego polecenia są podobne do następujących:

```json
{
  "createdTime": "2019-09-19T15:25:32.411572+00:00",
  "description": "",
  "experimentName": "myexperiment",
  "framework": "Custom",
  "frameworkVersion": null,
  "id": "mymodel:1",
  "name": "mymodel",
  "properties": "",
  "runId": "myexperiment_1568906070_5874522d",
  "tags": "",
  "version": 1
}
```

### <a name="model-versioning"></a>Przechowywanie wersji modelu

Zwróć uwagę na numer wersji zwrócony dla modelu. Ta wersja jest zwiększana za każdym razem, gdy rejestrujesz nowy model o tej nazwie. Na przykład możesz pobrać model i zarejestrować go z pliku lokalnego przy użyciu następujących poleceń:

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

Pierwsze polecenie pobiera zarejestrowany model do bieżącego katalogu. Nazwa pliku to `sklearn_mnist_model.pkl`, który jest plikiem, do którego odwołuje się po zarejestrowaniu modelu. Drugie polecenie rejestruje model lokalny (`-p "sklearn_mnist_model.pkl"`) o takiej samej nazwie jak poprzednia rejestracja (`mymodel`). Tym razem dane JSON zwracają listę wersji jako 2.

## <a name="deploy-the-model"></a>Wdrażanie modelu

Aby wdrożyć model, użyj następującego polecenia:

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.yml --dc aciDeploymentConfig.yml
```

> [!NOTE]
> Może pojawić się ostrzeżenie dotyczące "nie można sprawdzić istnienia LocalWebservice". Można bezpiecznie zignorować ten element, ponieważ nie jest wdrażana lokalna usługa sieci Web.

To polecenie służy do wdrażania nowej usługi o nazwie `myservice`przy użyciu wersji 1 modelu, który został wcześniej zarejestrowany.

Plik `inferenceConfig.yml` zawiera informacje dotyczące sposobu wykonywania wnioskowania, takie jak skrypt wprowadzania (`score.py`) i zależności oprogramowania. Aby uzyskać więcej informacji na temat struktury tego pliku, zobacz [Schemat konfiguracji wnioskowania](reference-azure-machine-learning-cli.md#inference-configuration-schema). Aby uzyskać więcej informacji na temat skryptów wprowadzania, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](how-to-deploy-and-where.md#prepare-to-deploy).

`aciDeploymentConfig.yml` opisuje środowisko wdrożenia używane do hostowania usługi. Konfiguracja wdrożenia jest specyficzna dla typu obliczenia używanego na potrzeby wdrożenia. W takim przypadku używane jest wystąpienie kontenera platformy Azure. Aby uzyskać więcej informacji, zobacz [Schemat konfiguracji wdrożenia](reference-azure-machine-learning-cli.md#deployment-configuration-schema).

Ukończenie procesu wdrażania potrwa kilka minut.

> [!TIP]
> W tym przykładzie użyto Azure Container Instances. Wdrożenia do ACI automatycznie tworzą wymagany zasób ACI. Jeśli zamiast tego chcesz przeprowadzić wdrożenie w usłudze Azure Kubernetes Service, należy najpierw utworzyć klaster AKS i określić go jako część polecenia `az ml model deploy`. Aby zapoznać się z przykładem wdrażania do AKS, zobacz [Wdrażanie modelu w klastrze usługi Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

Po kilku minutach zwracane są informacje podobne do następującego kodu JSON:

```json
ACI service creation operation finished, operation "Succeeded"
{
  "computeType": "ACI",
  {...ommitted for space...}
  "runtimeType": null,
  "scoringUri": "http://6c061467-4e44-4f05-9db5-9f9a22ef7a5d.eastus2.azurecontainer.io/score",
  "state": "Healthy",
  "tags": "",
  "updatedAt": "2019-09-19T18:22:32.227401+00:00"
}
```

### <a name="the-scoring-uri"></a>Identyfikator URI oceniania

`scoringUri` zwróconych z wdrożenia to punkt końcowy REST dla modelu wdrożonego jako usługa sieci Web. Ten identyfikator URI można także uzyskać za pomocą następującego polecenia:

```azurecli-interactive
az ml service show -n myservice
```

To polecenie zwraca ten sam dokument JSON, w tym `scoringUri`.

Punkt końcowy REST może służyć do wysyłania danych do usługi. Aby uzyskać informacje na temat tworzenia aplikacji klienckiej, która wysyła dane do usługi, zobacz temat [Korzystanie z modelu Azure Machine Learning wdrożonego jako usługa sieci Web](how-to-consume-web-service.md) .

### <a name="send-data-to-the-service"></a>Wyślij dane do usługi

Chociaż można utworzyć aplikację kliencką do wywoływania punktu końcowego, interfejs wiersza polecenia Machine Learning udostępnia narzędzie, które może działać jako klient testowy. Użyj następującego polecenia, aby wysłać dane do usługi w pliku `testdata.json`:

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> Jeśli używasz programu PowerShell, użyj następującego polecenia zamiast:
>
> ```powershell
> az ml service run -n myservice -d `@testdata.json
> ```

Odpowiedź polecenia jest podobna do `[ 3 ]`.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

> [!IMPORTANT]
> Utworzone zasoby mogą być używane jako wstępnie wymagane składniki w innych samouczkach usługi Azure Machine Learning i artykułach z instrukcjami.

### <a name="delete-deployed-service"></a>Usuń wdrożoną usługę

Jeśli planujesz kontynuować korzystanie z obszaru roboczego Azure Machine Learning, ale chcesz pozbyć się wdrożonej usługi w celu obniżenia kosztów, użyj następującego polecenia:

```azurecli-interactive
az ml service delete -n myservice
```

To polecenie zwraca dokument JSON zawierający nazwę usuniętej usługi. Usunięcie usługi może potrwać kilka minut.

### <a name="delete-the-training-compute"></a>Usuń obliczenia szkoleniowe

Jeśli planujesz kontynuować korzystanie z obszaru roboczego Azure Machine Learning, ale chcesz pozbyć się `cpu-compute` celu obliczeń utworzonych na potrzeby szkolenia, użyj następującego polecenia:

```azurecli-interactive
az ml computetarget delete -n cpu
```

To polecenie zwraca dokument JSON zawierający identyfikator usuniętego elementu docelowego obliczeń. Usunięcie elementu docelowego obliczeń może potrwać kilka minut.

### <a name="delete-everything"></a>Usuń wszystko

Jeśli nie planujesz używania utworzonych zasobów, usuń je, aby nie nawiązać dodatkowych opłat.

Aby usunąć grupę zasobów i wszystkie zasoby platformy Azure utworzone w tym dokumencie, użyj następującego polecenia. Zastąp `<resource-group-name>` nazwą utworzonej wcześniej grupy zasobów:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku Azure Machine Learning użyto interfejsu wiersza polecenia usługi Machine Learning do wykonywania następujących zadań:

> [!div class="checklist"]
> * Zainstaluj rozszerzenie uczenia maszynowego
> * Tworzenie obszaru roboczego usługi Azure Machine Learning
> * Utwórz zasób obliczeniowy służący do uczenia modelu
> * Definiowanie i rejestrowanie zestawu danych używanego do uczenia modelu
> * Rozpocznij pracę szkoleniową
> * Rejestrowanie i pobieranie modelu
> * Wdróż model jako usługę sieci Web
> * Dane oceny przy użyciu usługi sieci Web

Aby uzyskać więcej informacji na temat korzystania z interfejsu wiersza polecenia, zobacz [Używanie rozszerzenia interfejsu wiersza polecenia dla Azure Machine Learning](reference-azure-machine-learning-cli.md).
