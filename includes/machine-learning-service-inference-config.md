---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 8f7798e684a0a144cfe5285a0dd926a3b440934a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559373"
---
Wpisy w dokumencie są `inferenceconfig.json` mapowane na parametry klasy [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . W poniższej tabeli opisano mapowanie między jednostkami w dokumencie JSON a parametrami metody:

| Jednostka JSON | Parametr metody | Opis |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Ścieżka do pliku lokalnego, który zawiera kod do uruchomienia dla obrazu. |
| `sourceDirectory` | `source_directory` | Opcjonalny. Ścieżka do folderów zawierających wszystkie pliki do utworzenia obrazu, co ułatwia dostęp do dowolnych plików w tym folderze lub podfolderze. Można przekazać cały folder z komputera lokalnego jako zależności usługi sieci Web. Uwaga: ścieżki entry_script, conda_file i extra_docker_file_steps są ścieżkami względnymi do ścieżki source_directory. |
| `environment` | `environment` | Opcjonalny.  [Środowisko](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)Azure Machine Learning.|

W pliku konfiguracyjnym wnioskowania można uwzględnić pełne specyfikacje [środowiska](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning. Jeśli to środowisko nie istnieje w obszarze roboczym, Azure Machine Learning utworzy je. W przeciwnym razie Azure Machine Learning zaktualizuje środowisko w razie potrzeby. Poniższy kod JSON jest przykładem:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn==0.22.1",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Można również użyć istniejącego [środowiska](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning w oddzielnych parametrach interfejsu wiersza polecenia i usunąć klucz "Environment" z pliku konfiguracyjnego wnioskowania. Użyj-e dla nazwy środowiska i--EV dla wersji środowiska. Jeśli nie określisz wartość--EV, zostanie użyta Najnowsza wersja. Oto przykład pliku konfiguracyjnego wnioskowania:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

Następujące polecenie pokazuje, jak wdrożyć model przy użyciu poprzedniego pliku konfiguracyjnego wnioskowania (o nazwie myInferenceConfig.json). 

Używa również najnowszej wersji istniejącego [środowiska](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning (o nazwie "Azure-minimum").

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```