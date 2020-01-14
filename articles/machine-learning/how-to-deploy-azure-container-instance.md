---
title: Jak wdrażać modele w Azure Container Instances
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wdrożyć modele Azure Machine Learning jako usługę sieci Web przy użyciu Azure Container Instances.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 10/25/2019
ms.openlocfilehash: e128e6b5eb72abaac9e51b4cec8bf301eee5b4e7
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75764171"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Wdróż model do Azure Container Instances
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dowiedz się, jak za pomocą Azure Machine Learning wdrożyć model jako usługę sieci Web na Azure Container Instances (ACI). Użyj Azure Container Instances, jeśli spełniony jest jeden z następujących warunków:

- Musisz szybko wdrażać i weryfikacja modelu. Nie ma potrzeby tworzenia kontenerów ACI przed czasem. Są one tworzone w ramach procesu wdrażania.
- W przypadku testowania modelu, który jest w fazie projektowania. 

Aby uzyskać informacje dotyczące przydziału i dostępności regionów dla usługi ACI, zobacz artykuł [dotyczący przydziałów i dostępności regionów dla Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) artykułu.

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

- Model uczenia maszynowego zarejestrowany w obszarze roboczym. Jeśli nie masz zarejestrowanego modelu, zapoznaj [się z tematem jak i gdzie wdrażać modele](how-to-deploy-and-where.md).

- [Rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning Service](reference-azure-machine-learning-cli.md), [Azure Machine Learning SDK języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)lub [rozszerzenia Azure Machine Learning Visual Studio Code](how-to-vscode-tools.md).

- W fragmentach kodu w języku __Python__ w tym artykule założono, że ustawiono następujące zmienne:

    * `ws` — Ustaw obszar roboczy.
    * `model` — ustawiony na zarejestrowany model.
    * `inference_config` — Ustaw konfigurację wnioskowania dla modelu.

    Aby uzyskać więcej informacji na temat ustawiania tych zmiennych, zobacz [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).

- W fragmentach __interfejsu wiersza polecenia__ w tym artykule przyjęto założenie, że utworzono dokument `inferenceconfig.json`. Aby uzyskać więcej informacji na temat tworzenia tego dokumentu, zobacz [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).

## <a name="deploy-to-aci"></a>Wdrażanie w usłudze ACI

Aby wdrożyć model do Azure Container Instances, należy utworzyć __konfigurację wdrożenia__ opisującą wymaganą wartość zasobów obliczeniowych. Na przykład liczba rdzeni i pamięć. Potrzebna jest również __Konfiguracja wnioskowania__opisująca środowisko wymagane do hostowania modelu i usługi sieci Web. Aby uzyskać więcej informacji na temat tworzenia konfiguracji wnioskowania, zobacz [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).

### <a name="using-the-sdk"></a>Używanie zestawu SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Aby uzyskać więcej informacji na temat klas, metod i parametrów używanych w tym przykładzie, zobacz następujące dokumenty referencyjne:

* [AciWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-)
* [Model. deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Usługa WebService. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Korzystanie z interfejsu wiersza polecenia

Aby wdrożyć przy użyciu interfejsu wiersza polecenia, należy użyć poniższe polecenie. Zastąp `mymodel:1` nazwą i wersją zarejestrowanego modelu. Zastąp `myservice` nazwą, aby nadać tej usłudze:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

Aby uzyskać więcej informacji, zobacz [AZ ml model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) Reference. 

## <a name="using-vs-code"></a>Korzystanie z programu VS Code

Zobacz [Wdrażanie modeli przy użyciu vs Code](how-to-vscode-tools.md#deploy-and-manage-models).

> [!IMPORTANT]
> Nie musisz tworzyć kontenera ACI w celu przetestowania z góry. Kontenery ACI są tworzone zgodnie z wymaganiami.

## <a name="update-the-web-service"></a>Aktualizacja usługi sieci web

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Następne kroki

* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Rozwiązywanie problemów z wdrażaniem](how-to-troubleshoot-deployment.md)
* [Zabezpieczania usług sieci web Azure Machine Learning przy użyciu protokołu SSL](how-to-secure-web-service.md)
* [Korzystanie z modelu uczenia Maszynowego, wdrożyć jako usługę sieci web](how-to-consume-web-service.md)
* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
