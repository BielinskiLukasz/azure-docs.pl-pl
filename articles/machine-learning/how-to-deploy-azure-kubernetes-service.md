---
title: Jak wdrażać modele w usłudze Azure Kubernetes Service
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wdrożyć modele Azure Machine Learning jako usługę sieci Web przy użyciu usługi Azure Kubernetes Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 01/16/2020
ms.openlocfilehash: 78903d8f988efc1b0986f7e48050e63831b68319
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156832"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Wdrażanie modelu w klastrze usługi Azure Kubernetes Service
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dowiedz się, jak za pomocą Azure Machine Learning wdrożyć model jako usługę sieci Web w usłudze Azure Kubernetes Service (AKS). Usługa Azure Kubernetes Service jest dobra w przypadku dużych wdrożeń produkcyjnych. Użyj usługi Azure Kubernetes Service, jeśli potrzebujesz co najmniej jednej z następujących możliwości:

- __Krótki czas odpowiedzi__.
- __Skalowanie__ automatyczne wdrożonej usługi.
- Opcje __przyspieszania sprzętowego__ , takie jak macierze oparte na procesorach GPU i polach (FPGA).

> [!IMPORTANT]
> Skalowanie klastra nie jest obsługiwane za pomocą zestawu SDK Azure Machine Learning. Aby uzyskać więcej informacji na temat skalowania węzłów w klastrze AKS, zobacz [skalowanie liczby węzłów w KLASTRZE AKS](../aks/scale-cluster.md).

Podczas wdrażania w usłudze Azure Kubernetes należy wdrożyć klaster AKS, który jest __połączony z obszarem roboczym__. Istnieją dwa sposoby łączenia klastra AKS z obszarem roboczym:

* Utwórz klaster AKS przy użyciu zestawu SDK Azure Machine Learning, interfejsu wiersza polecenia Machine Learning lub [Azure Machine Learning Studio](https://ml.azure.com). Ten proces automatycznie łączy klaster z obszarem roboczym.
* Dołącz istniejący klaster AKS do obszaru roboczego Azure Machine Learning. Klaster może być dołączany przy użyciu zestawu SDK Azure Machine Learning, Machine Learning interfejsu wiersza polecenia lub Azure Machine Learning Studio.

> [!IMPORTANT]
> Proces tworzenia lub załączników to zadanie jednorazowe. Gdy klaster AKS jest połączony z obszarem roboczym, można go użyć do wdrożeń. Możesz odłączyć lub usunąć klaster AKS, jeśli nie jest już potrzebny. Po detatched lub usunięciu nie będzie już można wdrażać w klastrze.

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

- Model uczenia maszynowego zarejestrowany w obszarze roboczym. Jeśli nie masz zarejestrowanego modelu, zapoznaj [się z tematem jak i gdzie wdrażać modele](how-to-deploy-and-where.md).

- [Rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning Service](reference-azure-machine-learning-cli.md), [Azure Machine Learning SDK języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)lub [rozszerzenia Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

- W fragmentach kodu w języku __Python__ w tym artykule założono, że ustawiono następujące zmienne:

    * `ws` — Ustaw obszar roboczy.
    * `model` — ustawiony na zarejestrowany model.
    * `inference_config` — Ustaw konfigurację wnioskowania dla modelu.

    Aby uzyskać więcej informacji na temat ustawiania tych zmiennych, zobacz [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).

- W fragmentach __interfejsu wiersza polecenia__ w tym artykule przyjęto założenie, że utworzono dokument `inferenceconfig.json`. Aby uzyskać więcej informacji na temat tworzenia tego dokumentu, zobacz [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).

## <a name="create-a-new-aks-cluster"></a>Tworzenie nowego klastra AKS

**Szacowany czas**: około 20 minut.

Tworzenie i dołączanie klastra AKS jest jednym procesem czasu dla Twojego obszaru roboczego. Można ponownie użyć klastra dla wielu wdrożeń. W przypadku usunięcia klastra lub grupy zasobów, która zawiera tę usługę, należy utworzyć nowy klaster przy następnym wdrożeniu. Do obszaru roboczego można dołączyć wiele klastrów AKS.

> [!TIP]
> Jeśli chcesz zabezpieczyć klaster AKS przy użyciu Virtual Network platformy Azure, musisz najpierw utworzyć sieć wirtualną. Aby uzyskać więcej informacji, zobacz temat [bezpieczne eksperymentowanie i wnioskowanie za pomocą usługi Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

Jeśli chcesz utworzyć klaster AKS __na potrzeby tworzenia__, __sprawdzania poprawności__i __testowania__ zamiast produkcji, możesz określić __cel klastra__ dla __testu deweloperskiego__.

> [!WARNING]
> Jeśli ustawisz `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, tworzony klaster nie jest odpowiedni dla ruchu na poziomie produkcyjnym i może zwiększyć czas wnioskowania. Klastry deweloperskie i testowe nie gwarantują odporności na uszkodzenia. Zalecamy co najmniej 2 wirtualne procesory CPU dla klastrów deweloperskich i testowych.

W poniższych przykładach pokazano, jak utworzyć nowy klaster AKS przy użyciu zestawu SDK i interfejsu wiersza polecenia:

**Korzystanie z zestawu SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> W przypadku [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), w przypadku wybrania wartości niestandardowych dla `agent_count` i `vm_size`, a `cluster_purpose` nie `DEV_TEST`, należy upewnić się, że `agent_count` pomnożone przez `vm_size` nie są równe 12 wirtualnym procesorom CPU. Na przykład, jeśli używasz `vm_size` "Standard_D3_v2", który ma 4 procesory wirtualne, należy wybrać `agent_count` z 3 lub większą liczbą.
>
> Zestaw SDK Azure Machine Learning nie zapewnia obsługi skalowania klastra AKS. Aby skalować węzły w klastrze, użyj interfejsu użytkownika dla klastra AKS w programie Azure Machine Learning Studio. Można zmienić tylko liczbę węzłów, a nie rozmiar maszyn wirtualnych klastra.

Aby uzyskać więcej informacji na temat klas, metod i parametrów używanych w tym przykładzie, zobacz następujące dokumenty referencyjne:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute. provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget. wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Korzystanie z interfejsu wiersza polecenia**

```azurecli
az ml computetarget create aks -n myaks
```

Aby uzyskać więcej informacji, zobacz [AZ ml computetarget Create AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) Reference.

## <a name="attach-an-existing-aks-cluster"></a>Dołącz istniejący klaster AKS

**Szacowany czas:** Około 5 minut.

Jeśli klaster AKS jest już w ramach subskrypcji platformy Azure i jest w wersji 1,16 lub niższej, można go użyć do wdrożenia obrazu.

> [!TIP]
> Istniejący klaster AKS może znajdować się w regionie świadczenia usługi Azure innym niż obszar roboczy Azure Machine Learning.
>
> Jeśli chcesz zabezpieczyć klaster AKS przy użyciu Virtual Network platformy Azure, musisz najpierw utworzyć sieć wirtualną. Aby uzyskać więcej informacji, zobacz temat [bezpieczne eksperymentowanie i wnioskowanie za pomocą usługi Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

Podczas dołączania klastra AKS do obszaru roboczego można określić, jak będzie używany klaster, ustawiając parametr `cluster_purpose`.

Jeśli nie ustawisz parametru `cluster_purpose` ani nie ustawisz `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, klaster musi mieć co najmniej 12 dostępnych procesorów wirtualnych.

Jeśli ustawisz `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, klaster nie musi mieć 12 wirtualnych procesorów CPU. Zalecamy co najmniej 2 wirtualne procesory CPU na potrzeby tworzenia i testowania. Jednak klaster skonfigurowany do tworzenia i testowania nie jest odpowiedni dla ruchu na poziomie produkcyjnym i może zwiększyć czas wnioskowania. Klastry deweloperskie i testowe nie gwarantują odporności na uszkodzenia.

> [!WARNING]
> Nie należy tworzyć wielu jednoczesnych załączników do tego samego klastra AKS z obszaru roboczego. Na przykład po dołączeniu jednego klastra AKS do obszaru roboczego przy użyciu dwóch różnych nazw. Każdy nowy załącznik spowoduje przerwanie poprzednich istniejących załączników.
>
> Jeśli chcesz ponownie dołączyć klaster AKS, na przykład aby zmienić ustawienia konfiguracji protokołu SSL lub innego klastra, musisz najpierw usunąć istniejący załącznik przy użyciu [AksCompute. Odłącz ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--).

Aby uzyskać więcej informacji na temat tworzenia klastra AKS przy użyciu interfejsu wiersza polecenia platformy Azure lub portalu, zobacz następujące artykuły:

* [Tworzenie klastra AKS (interfejs wiersza polecenia)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Tworzenie klastra AKS (Portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

W poniższych przykładach pokazano, jak dołączyć istniejący klaster AKS do obszaru roboczego:

**Korzystanie z zestawu SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)
```

Aby uzyskać więcej informacji na temat klas, metod i parametrów używanych w tym przykładzie, zobacz następujące dokumenty referencyjne:

* [AksCompute. attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute. Attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**Korzystanie z interfejsu wiersza polecenia**

Aby dołączyć istniejący klaster przy użyciu interfejsu wiersza polecenia, należy uzyskać identyfikator zasobu istniejącego klastra. Aby uzyskać tę wartość, użyj następującego polecenia. Zastąp `myexistingcluster` nazwą klastra AKS. Zastąp `myresourcegroup` grupą zasobów, która zawiera klaster:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

To polecenie zwraca wartość podobną do następującego tekstu:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Aby dołączyć istniejący klaster do obszaru roboczego, użyj następującego polecenia. Zastąp `aksresourceid` wartością zwracaną przez poprzednie polecenie. Zastąp `myresourcegroup` grupą zasobów, która zawiera obszar roboczy. Zastąp `myworkspace` nazwą obszaru roboczego.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Aby uzyskać więcej informacji, zobacz [AZ ml computetarget Attach AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) Reference.

## <a name="deploy-to-aks"></a>Wdrażanie w usłudze AKS

Aby wdrożyć model w usłudze Azure Kubernetes Service, Utwórz __konfigurację wdrożenia__ opisującą wymaganą wartość zasobów obliczeniowych. Na przykład liczba rdzeni i pamięć. Potrzebna jest również __Konfiguracja wnioskowania__opisująca środowisko wymagane do hostowania modelu i usługi sieci Web. Aby uzyskać więcej informacji na temat tworzenia konfiguracji wnioskowania, zobacz [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).

### <a name="using-the-sdk"></a>Używanie zestawu SDK

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Aby uzyskać więcej informacji na temat klas, metod i parametrów używanych w tym przykładzie, zobacz następujące dokumenty referencyjne:

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Model. deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Usługa WebService. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Korzystanie z interfejsu wiersza polecenia

Aby wdrożyć przy użyciu interfejsu wiersza polecenia, należy użyć poniższe polecenie. Zastąp `myaks` nazwą elementu docelowego obliczeń AKS. Zastąp `mymodel:1` nazwą i wersją zarejestrowanego modelu. Zastąp `myservice` nazwą, aby nadać tej usłudze:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Aby uzyskać więcej informacji, zobacz [AZ ml model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) Reference.

### <a name="using-vs-code"></a>Korzystanie z programu VS Code

Aby uzyskać informacje na temat korzystania z VS Code, zobacz [wdrażanie do AKS za pomocą rozszerzenia vs Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Wdrożenie za pomocą VS Code wymaga, aby klaster AKS został utworzony lub dołączony do obszaru roboczego z wyprzedzeniem.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Wdróż modele do AKS przy użyciu kontrolowanego wdrożenia (wersja zapoznawcza)
Analizuj i podnieś wersje modeli w kontrolowany sposób za pomocą punktów końcowych. Wdróż maksymalnie 6 wersji za pojedynczym punktem końcowym i skonfiguruj% ruchu oceniania w każdej wdrożonej wersji. Możesz włączyć usługi App Insights, aby wyświetlać metryki operacyjne punktów końcowych i wdrożonych wersji.

### <a name="create-an-endpoint"></a>Tworzenie punktu końcowego
Gdy wszystko będzie gotowe do wdrożenia modeli, Utwórz punkt końcowy oceniania i Wdróż swoją pierwszą wersję. W poniższym kroku pokazano, jak wdrożyć i utworzyć punkt końcowy przy użyciu zestawu SDK. Pierwsze wdrożenie zostanie zdefiniowane jako wersja domyślna, co oznacza, że nieokreślony ruch o percentylu w wszystkich wersjach będzie przechodził do wersji domyślnej.  

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint",
version_name= "versiona",
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = true,
                                                              tags = {'sckitlearn':'demo'},
                                                              decription = testing versions,
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Aktualizowanie i Dodawanie wersji do punktu końcowego

Dodaj kolejną wersję do punktu końcowego i skonfiguruj przechodzenie oceniające ruch do wersji. Istnieją dwa typy wersji, kontrolki i wersja leczenia. Może istnieć wiele wersji obróbki, aby pomóc w porównaniu z wersją pojedynczej kontrolki.

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
```

Zaktualizuj istniejące wersje lub usuń je w punkcie końcowym. Można zmienić domyślny typ wersji, typ kontrolki i percentyl ruchu.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)

# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```


## <a name="web-service-authentication"></a>Uwierzytelnianie usługi sieci Web

W przypadku wdrażania w usłudze Azure Kubernetes uwierzytelnianie __oparte na kluczach__ jest domyślnie włączone. Można również włączyć uwierzytelnianie __oparte na tokenach__ . Uwierzytelnianie oparte na tokenach wymaga, aby klienci używali konta Azure Active Directory do żądania tokenu uwierzytelniania, który jest używany do wysyłania żądań do wdrożonej usługi.

Aby __wyłączyć__ uwierzytelnianie, należy ustawić parametr `auth_enabled=False` podczas tworzenia konfiguracji wdrożenia. Poniższy przykład powoduje wyłączenie uwierzytelniania przy użyciu zestawu SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Informacje o uwierzytelnianiu z aplikacji klienckiej można znaleźć w temacie [Korzystanie z modelu Azure Machine Learning wdrożonego jako usługa sieci Web](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Uwierzytelnianie przy użyciu kluczy

Jeśli uwierzytelnianie klucza jest włączone, można użyć metody `get_keys`, aby pobrać podstawowy i pomocniczy klucz uwierzytelniania:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Jeśli musisz ponownie wygenerować klucz, użyj [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Uwierzytelnianie przy użyciu tokenów

Aby włączyć uwierzytelnianie tokenu, należy ustawić parametr `token_auth_enabled=True` podczas tworzenia lub aktualizowania wdrożenia. Poniższy przykład umożliwia uwierzytelnianie tokenu przy użyciu zestawu SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Jeśli jest włączone uwierzytelnianie tokenu, można użyć metody `get_token`, aby pobrać token JWT i czas wygaśnięcia tokenu:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Musisz zażądać nowego tokenu po upływie `refresh_by` tokenu.
>
> Firma Microsoft zdecydowanie zaleca utworzenie obszaru roboczego Azure Machine Learning w tym samym regionie, w którym znajduje się klaster usługi Azure Kubernetes. W celu uwierzytelnienia przy użyciu tokenu usługa sieci Web wykona wywołanie do regionu, w którym jest tworzony obszar roboczy Azure Machine Learning. Jeśli region obszaru roboczego jest niedostępny, nie będzie można pobrać tokenu dla usługi sieci Web nawet wtedy, gdy klaster znajduje się w innym regionie niż obszar roboczy. W efekcie uwierzytelnianie oparte na tokenach jest niedostępne do momentu ponownego udostępnienia regionu obszaru roboczego. Ponadto im większa odległość między regionem klastra a regionem obszaru roboczego, tym dłużej potrwa pobieranie tokenu.

## <a name="update-the-web-service"></a>Aktualizacja usługi sieci web

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Następne kroki

* [Bezpieczne eksperymentowanie i wnioskowanie w sieci wirtualnej](how-to-enable-virtual-network.md)
* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Rozwiązywanie problemów z wdrażaniem](how-to-troubleshoot-deployment.md)
* [Zabezpieczania usług sieci web Azure Machine Learning przy użyciu protokołu SSL](how-to-secure-web-service.md)
* [Korzystanie z modelu uczenia Maszynowego, wdrożyć jako usługę sieci web](how-to-consume-web-service.md)
* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
