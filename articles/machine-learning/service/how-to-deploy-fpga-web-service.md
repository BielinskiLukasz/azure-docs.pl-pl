---
title: Co to są FPGA — jak wdrożyć
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wdrożyć usługę sieci Web z modelem działającym na FPGA z Azure Machine Learning na potrzeby wnioskowania o bardzo małych opóźnieniach.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: tedway
author: tedway
ms.date: 07/25/2019
ms.custom: seodec18
ms.openlocfilehash: 9c3c844ba7044f8e1c9c313f1ac63b94310ea322
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350535"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>Co to są tablice z bramą programowalną w polu (FPGA) i sposób wdrażania

Ten artykuł zawiera wprowadzenie do wielopolowych tablic bram (FPGA) oraz sposób wdrażania modeli przy użyciu Azure Machine Learning na platformie Azure FPGA. 

FPGA zawierają tablicę programowalnych bloków logiki oraz hierarchię z ponownymi konfigurowalnymi połączeniami. Połączenia międzyłączne umożliwiają skonfigurowanie tych bloków na różne sposoby po zakończeniu produkcji. W porównaniu z innymi mikroukładami FPGA zapewniają kombinację programowania i wydajności.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>Vs układów FPGA. Procesor CPU, procesora GPU i ASIC

Na poniższym diagramie i tabeli przedstawiono sposób porównywania FPGA z innymi procesorami.

![Diagram Azure Machine Learning porównania FPGA](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|Procesor||Opis|
|---|:-------:|------|
|Scalonych specyficzne dla aplikacji|ASICs|Obwody niestandardowe, takie jak Google TensorFlow procesora jednostki (TPU), zapewnia najwyższą efektywnością. Nie można ich ponownie skonfigurować zgodnie z potrzebami zmian.|
|Tablice programowalny bramy|Układów FPGA|FPGA, takie jak te dostępne na platformie Azure, zapewniają wydajność zbliżoną do ASICs. Są one również elastyczne i można je skonfigurować w miarę upływu czasu w celu zaimplementowania nowej logiki.|
|Procesory graficzne|Procesory GPU|Popularny wybór dla obliczeń AI. Procesory GPU oferują równoległe możliwości przetwarzania, przyspieszając renderowanie obrazów niż procesory CPU.|
|Jednostek centralnych|Procesory CPU|Procesor ogólnego przeznaczenia, wydajność, która nie jest idealnym rozwiązaniem w przypadku przetwarzania grafiki i wideo.|

FPGA na platformie Azure są oparte na urządzeniach FPGA firmy Intel, których analityki danych i Deweloperzy używają do przyspieszenia obliczeń w czasie rzeczywistym. Ta architektura z obsługą FPGA oferuje wydajność, elastyczność i skalowalność oraz jest dostępna na platformie Azure.

FPGA umożliwiają osiągnięcie małych opóźnień dla żądań wnioskowania w czasie rzeczywistym (lub oceniania modeli). Żądania asynchroniczne (wsadowe) nie są potrzebne. Przetwarzanie wsadowe może spowodować opóźnienia, ponieważ trzeba przetworzyć więcej danych. Implementacje jednostek przetwarzania neuronowych nie wymagają przetwarzania wsadowego; w związku z tym opóźnienie może być znacznie mniejsze, w porównaniu z procesorami CPU i procesora GPU.

### <a name="reconfigurable-power"></a>Możliwych do ponownego skonfigurowania zasilania
Można ponownie skonfigurować FPGA dla różnych typów modeli uczenia maszynowego. Ta elastyczność ułatwia przyspieszenie aplikacji na podstawie najbardziej optymalną precyzja wartości liczbowych i model pamięci używane. Ze względu na to, że FPGA są modyfikowane, można zachować aktualność z wymaganiami natychmiastowej zmiany algorytmów AI.

## <a name="whats-supported-on-azure"></a>Co to jest obsługiwana na platformie Azure
Microsoft Azure to największe inwestycje chmury w FPGA. Przy użyciu tej architektury włączone FPGA sprzętu, przeszkolonych sieci neuronowych uruchomić szybkie i mniejsze opóźnienie. Platforma Azure może zrównoleglanie wstępnie szkolonych sieci neuronowych (DNN) w całej FPGA w celu skalowania usługi. Sieci mogą być wstępnie przeszkolonych, jako głębokiego featurized transferu uczenia lub dostosowaniu za pomocą wag zaktualizowane.

FPGA na platformie Azure obsługuje:

+ Scenariusze dotyczące klasyfikacji i rozpoznawania obrazu
+ TensorFlow wdrożenia
+ Sprzęt firmy Intel FPGA 

Te modele DNN są obecnie dostępne:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

FPGA są dostępne w tych regionach świadczenia usługi Azure:
  - East US
  - Azja Południowo-Wschodnia
  - Europa Zachodnia
  - Zachodnie stany USA 2

> [!IMPORTANT]
> Aby zoptymalizować opóźnienia i przepływność, klient wysyłający dane do modelu FPGA powinien znajdować się w jednym z powyższych regionów (na którym został wdrożony model).

**Rodzina PBS maszyn wirtualnych platformy Azure** zawiera technologię Intel Arria 10 FPGA. Zostanie on wyświetlony jako "standardowa rodzina PBS procesorów wirtualnych vCPU" podczas sprawdzania przydziału przydziału platformy Azure. Maszyna wirtualna w PB6 ma sześć procesorów wirtualnych vCPU i jeden FPGA i zostanie automatycznie zainicjowana przez usługę Azure ML w ramach wdrażania modelu w FPGA. Jest ona używana tylko z platformą Azure ML i nie może uruchamiać dowolnego bitstreams. Na przykład nie będzie można wykonać czynności Flash FPGA z bitstreams do szyfrowania, kodowania itp.

### <a name="scenarios-and-applications"></a>Scenariusze i aplikacje

Usługa Azure FPGA jest zintegrowana z usługą Azure Machine Learning. Firma Microsoft używa usługi FPGA do oceny DNN, klasyfikacji wyszukiwania Bing i przyspieszenia sieci zdefiniowanej przez oprogramowanie (SDN) w celu skrócenia opóźnień, jednocześnie zwalniając procesory dla innych zadań.

Następujące scenariusze używają FPGA:
+ [Zautomatyzowany system inspekcji optycznych](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Mapowanie cover ziemi](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)



## <a name="example-deploy-models-on-fpgas"></a>Przykład: Wdróż modele na FPGA 

Model można wdrożyć jako usługę sieci Web na FPGA z Modele z przyspieszaniem sprzętowym Azure Machine Learning. Korzystanie z usługi FPGA zapewnia bardzo małe ilości opóźnienia, nawet w przypadku pojedynczego rozmiaru partii. Wnioskowanie lub ocenianie modelu to faza, w której wdrożony model jest używany do prognozowania, najczęściej dotyczący danych produkcyjnych.


### <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure.  Jeśli nie masz takiego konta, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

- Limit przydziału FPGA. Użyj interfejsu wiersza polecenia platformy Azure, aby sprawdzić, czy masz limit przydziału:

    ```shell
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > Inne możliwe lokalizacje to ``southeastasia``, ``westeurope``i ``westus2``.

    Polecenie zwraca tekst podobny do następującego:

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    Upewnij się, że masz co najmniej 6 procesorów wirtualnych vCPU w obszarze __CurrentValue__.

    Jeśli nie masz przydziału, Prześlij żądanie na [https://aka.ms/accelerateAI](https://aka.ms/accelerateAI).

- Zainstalowano obszar roboczy Azure Machine Learning i Azure Machine Learning SDK dla języka Python. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego](how-to-manage-workspace.md).
 
- Zestaw SDK języka Python dla modeli przyspieszanych sprzętowo:

    ```shell
    pip install --upgrade azureml-accel-models
    ```


## <a name="1-create-and-containerize-models"></a>1. Tworzenie i konteneryzowanie modeli

W tym dokumencie opisano sposób tworzenia grafu TensorFlow w celu wstępnego przetworzenia obrazu wejściowego, przeprowadzenia featurized przy użyciu ResNet 50 na FPGA, a następnie uruchomienia funkcji za pomocą klasyfikatora przeszkolonego na zestawie danych ImageNet.

Postępuj zgodnie z instrukcjami, aby:

* Zdefiniuj model TensorFlow
* Konwertuj model
* Wdrażanie modelu
* Używanie wdrożonego modelu
* Usuwanie wdrożonych usług

Użyj [zestawu SDK Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) , aby utworzyć definicję usługi. Definicja usługi jest plikiem opisującym potok grafów (Input, featurized i klasyfikator) oparty na TensorFlow. Polecenie wdrożenia automatycznie kompresuje definicje i wykresy do pliku ZIP i przekazuje je do usługi Azure Blob Storage. DNN jest już wdrożony do uruchamiania na FPGA.

### <a name="load-azure-ml-workspace"></a>Załaduj obszar roboczy usługi Azure ML

Załaduj obszar roboczy usługi Azure ML.

```python
import os
import tensorflow as tf

from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
```

### <a name="preprocess-image"></a>Wstępne przetwarzanie obrazu

Wejście do usługi sieci Web jest obrazem JPEG.  Pierwszym krokiem jest Dekodowanie obrazu JPEG i jego wstępne przetworzenie.  Obrazy JPEG są traktowane jako ciągi, a wynikiem są dwuplikowe dane wejściowe modelu ResNet 50.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Ładuj featurized

Inicjowanie modelu i Pobierz punkt kontrolny TensorFlow wykonywanie kwantyzowanych wersji ResNet50 ma być używany jako featurized.  W poniższym fragmencie kodu można zastąpić "QuantizedResnet50", importując inne sieci głębokiej neuronowych:

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen=True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Dodaj klasyfikator

Po zapoznaniu to klasyfikatora na zestawie danych sieci ImageNet.  Przykłady dotyczące uczenia i szkolenia z zakresu przenoszenia są dostępne w zestawie [przykładowych notesów](https://aka.ms/aml-notebooks).

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>Zapisz model

Teraz, gdy preprocesor, ResNet 50 featurized i klasyfikator został załadowany, Zapisz wykres i skojarzone zmienne jako model.

```python
model_name = "resnet50"
model_save_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_save_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_save_path,
                               inputs={'images': in_images},
                               outputs={'output_alias': classifier_output})
```

### <a name="save-input-and-output-tensors"></a>Zapisz natężenia wejścia i wyjścia
W przypadku konwersji modelu i wnioskowania będą wymagane dwuetapy wejściowe i wyjściowe, które zostały utworzone podczas procesu przetwarzania wstępnego i klasyfikatora.

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> Zapisz natężenia wejścia i wyjścia, ponieważ będą potrzebne do konwersji modeli i żądań wnioskowania.

Dostępne są następujące modele i odpowiednie rozdzielacze domyślne klasyfikatora, które są używane do wnioskowania, jeśli użyto klasyfikatora domyślnego.

+ Resnet50, QuantizedResnet50
  ```python
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152, QuantizedResnet152
  ```python
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ Densenet121, QuantizedDensenet121
  ```python
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16, QuantizedVgg16
  ```python
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg, QuantizedSsdVgg
  ```python
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>Rejestrowanie modelu

[Zarejestruj](./concept-model-management-and-deployment.md) model przy użyciu zestawu SDK z plikiem zip w usłudze Azure Blob Storage. Dodawanie tagów i innych metadanych dotyczących modelu ułatwia śledzenie przeszkolonych modeli.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace=ws,
                                  model_path=model_save_path,
                                  model_name=model_name)

print("Successfully registered: ", registered_model.name,
      registered_model.description, registered_model.version, sep='\t')
```

Jeśli model został już zarejestrowany i chcesz go załadować, możesz go pobrać.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description,
      registered_model.version, sep='\t')
```

### <a name="convert-model"></a>Konwertuj model

Przekonwertuj wykres TensorFlow na format Open neuronowych Network Exchange ([ONNX](https://onnx.ai/)).  Należy podać nazwy natężeń wejściowych i wyjściowych, a te nazwy będą używane przez klienta podczas korzystania z usługi sieci Web.

```python
from azureml.accel import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(
    ws, registered_model, input_tensors, output_tensors)

# If it fails, you can run wait_for_completion again with show_output=True.
convert_request.wait_for_completion(show_output=False)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
      converted_model.id, converted_model.created_time, '\n')
```

### <a name="create-docker-image"></a>Tworzenie obrazu platformy Docker

Przekonwertowany model i wszystkie zależności są dodawane do obrazu platformy Docker.  Ten obraz platformy Docker można następnie wdrożyć i utworzyć jego wystąpienie.  Obsługiwane cele wdrożenia obejmują AKS w chmurze lub urządzenie brzegowe, takie jak [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  Możesz również dodać tagi i opisy dla zarejestrowanego obrazu platformy Docker.

```python
from azureml.core.image import Image
from azureml.accel import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
# Image name must be lowercase
image_name = "{}-image".format(model_name)

image = Image.create(name=image_name,
                     models=[converted_model],
                     image_config=image_config,
                     workspace=ws)
image.wait_for_creation(show_output=False)
```

Wyświetl listę obrazów według tagów i uzyskaj szczegółowe dzienniki dla dowolnego debugowania.

```python
for i in Image.list(workspace=ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(
        i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="2-deploy-to-cloud-or-edge"></a>2. Wdróż w chmurze lub Edge

### <a name="deploy-to-the-cloud"></a>Wdrażanie w chmurze

Aby wdrożyć modelu w postaci usługi sieci web o dużej skali w środowisku produkcyjnym, należy użyć usługi Azure Kubernetes Service (AKS). Nowy element można utworzyć przy użyciu zestawu Azure Machine Learning SDK, interfejsu wiersza polecenia [Azure Portal](https://portal.azure.com) lub [strony docelowej obszaru roboczego (wersja zapoznawcza)](https://ml.azure.com).

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2”. If no value is specified, the default is "eastus".
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1,
                                                    location = "eastus")

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)
```

Wdrożenie AKS może potrwać około 15 minut.  Sprawdź, czy wdrożenie zakończyło się pomyślnie.

```python
aks_target.wait_for_completion(show_output=True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

Wdróż kontener w klastrze AKS.
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled=False)

aks_service_name = 'my-aks-service'

aks_service = Webservice.deploy_from_image(workspace=ws,
                                           name=aks_service_name,
                                           image=image,
                                           deployment_config=aks_config,
                                           deployment_target=aks_target)
aks_service.wait_for_deployment(show_output=True)
```

#### <a name="test-the-cloud-service"></a>Testowanie usługi w chmurze
Obraz platformy Docker obsługuje gRPC i TensorFlow obsługujący interfejs API "przewidywania".  Użyj przykładowego klienta do wywołania obrazu platformy Docker w celu uzyskania prognoz z modelu.  Kod przykładowego klienta jest dostępny:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Jeśli chcesz korzystać z TensorFlow, możesz [pobrać przykładowego klienta](https://www.tensorflow.org/serving/setup).

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize AzureML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Ponieważ ten klasyfikator został przeszkolony w zestawie danych [ImageNet](http://www.image-net.org/) , Mapuj klasy na etykiety czytelne dla człowieka.

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg",
                            input_name=input_tensors,
                            outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-the-service"></a>Czyszczenie usługi
Usuń usługę sieci Web, obraz i model (należy wykonać w tej kolejności, ponieważ istnieją zależności).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

### <a name="deploy-to-a-local-edge-server"></a>Wdrażanie na lokalnym serwerze brzegowym

Wszystkie [urządzenia](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) Azure Data Box Edge zawierają FPGA do uruchamiania modelu.  Tylko jeden model może być uruchomiony w FPGA jednocześnie.  Aby uruchomić inny model, po prostu wdróż nowy kontener. Instrukcje i przykładowy kod można znaleźć w [tym przykładzie platformy Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

## <a name="secure-fpga-web-services"></a>Zabezpieczania usług sieci web FPGA

Aby zabezpieczyć FPGA usługi sieci Web, zobacz dokument [Secure Web Services](how-to-secure-web-service.md) .

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z tymi notesami, klipami wideo i blogami:

+ Kilka [przykładowych notesów](https://aka.ms/aml-accel-models-notebooks).

+ [Sprzęt ze skalowaniem: ML na dużą skalę na platformie Azure i FPGA: Kompilacja 2018 (wideo)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [W firmie Microsoft na układach FPGA konfigurowalnej chmury (wideo)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Project Brainwave dla sztucznej Inteligencji w czasie rzeczywistym: Strona główna projektu](https://www.microsoft.com/research/project/project-brainwave/)
