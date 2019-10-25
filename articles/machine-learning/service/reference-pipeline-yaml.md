---
title: YAML potoku Machine Learning
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zdefiniować potok uczenia maszynowego przy użyciu pliku YAML. Definicje potoku YAML są używane z rozszerzeniem uczenia maszynowego dla interfejsu wiersza polecenia platformy Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: sanpil
author: sanpil
ms.date: 10/15/2019
ms.openlocfilehash: a98a0e75c7a03baa663ccb4215e918a87bcc5df7
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821777"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Definiowanie potoków uczenia maszynowego w YAML

Dowiedz się, jak definiować potoki uczenia maszynowego w [YAML](https://yaml.org/). W przypadku korzystania z rozszerzenia Uczenie maszynowe dla interfejsu wiersza polecenia platformy Azure wiele poleceń związanych z potokiem oczekuje pliku YAML, który definiuje potok.

W poniższej tabeli przedstawiono, co jest i nie jest obecnie obsługiwane podczas definiowania potoku w YAML:

| Typ kroku | Obsługiwane? |
| ----- | :-----: |
| PythonScriptStep | Tak |
| AdlaStep | Tak |
| AzureBatchStep | Tak |
| DatabricksStep | Tak |
| DataTransferStep | Tak |
| AutoMLStep | Nie |
| HyperDriveStep | Nie |
| ModuleStep | Nie |
| MPIStep | Nie |
| EstimatorStep | Nie |

## <a name="pipeline-definition"></a>Definicja potoku

Definicja potoku używa następujących kluczy, które odpowiadają klasie [potoków](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py) :

| Klucz YAML | Opis |
| ----- | ----- |
| `name` | Opis potoku. |
| `parameters` | Parametrów do potoku. |
| `data_reference` | Definiuje sposób i miejsce, w którym dane powinny być dostępne w przebiegu. |
| `default_compute` | Domyślny element docelowy obliczeń, w którym wykonywane są wszystkie kroki w potoku. |
| `steps` | Kroki używane w potoku. |

Następujący YAML jest przykładową definicją potoku:

## <a name="parameters"></a>Parametry

Sekcja `parameters` używa następujących kluczy, które odpowiadają klasie [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py) :

| Klucz YAML | Opis |
| ---- | ---- |
| `type` | Typ wartości parametru. Prawidłowe typy to `string`, `int`, `float`, `bool`lub `datapath`. |
| `default` | Wartość domyślna. |

Każdy parametr ma nazwę. Na przykład poniższy fragment kodu YAML definiuje trzy parametry o nazwie `NumIterationsParameter`, `DataPathParameter`i `NodeCountParameter`:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        NumIterationsParameter:
            type: int
            default: 40
        DataPathParameter:
            type: datapath
            default:
                datastore: workspaceblobstore
                path_on_datastore: sample2.txt
        NodeCountParameter:
            type: int
            default: 4
```

## <a name="data-reference"></a>Odwołanie do danych

W sekcji `data_references` są używane następujące klucze, które odnoszą się do [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py):

| Klucz YAML | Opis |
| ----- | ----- |
| `datastore` | Magazyn danych do odwołania. |
| `path_on_datastore` | Ścieżka względna w magazynie zapasowym dla odwołania do danych. |

Każde odwołanie do danych jest zawarte w kluczu. Na przykład poniższy fragment kodu YAML definiuje odwołanie do danych przechowywane w kluczu o nazwie `employee_data`:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
```

## <a name="steps"></a>Kroki

Kroki definiują środowisko obliczeniowe, a także pliki do uruchomienia w środowisku. Definicja YAML reprezentuje następujące kroki:

| Klucz YAML | Opis |
| ----- | ----- |
| `adla_step` | Uruchamia skrypt U-SQL z Azure Data Lake Analytics. Odpowiada klasie [AdlaStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?view=azure-ml-py) . |
| `azurebatch_step` | Uruchamia zadania przy użyciu Azure Batch. Odpowiada klasie [AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?view=azure-ml-py) . |
| `databricks_step` | Dodaje Notes, skrypt w języku Python lub JAR. Odpowiada klasie [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?view=azure-ml-py) . |
| `data_transfer_step` | Przesyła dane między opcjami magazynu. Odpowiada klasie [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) . |
| `python_script_step` | Uruchamia skrypt języka Python. Odpowiada klasie [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) . |

### <a name="adla-step"></a>ADLA — krok

| Klucz YAML | Opis |
| ----- | ----- |
| `script_name` | Nazwa skryptu U-SQL (względem `source_directory`). |
| `compute_target` | Obiekt docelowy obliczeń Azure Data Lake do użycia w tym kroku. |
| `parameters` | [Parametry](#parameters) do potoku. |
| `inputs` | Dane wejściowe mogą być [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)lub [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Wynikami może być [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) lub [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | Katalog zawierający skrypt, zestawy itp. |
| `priority` | Wartość priorytetu, która ma być używana w bieżącym zadaniu. |
| `params` | Słownik par nazwa-wartość. |
| `degree_of_parallelism` | Stopień równoległości dla tego zadania. |
| `runtime_version` | Wersja środowiska uruchomieniowego aparatu Data Lake Analytics. |
| `allow_reuse` | Określa, czy krok ma ponownie używać poprzednich wyników po ponownym uruchomieniu z tymi samymi ustawieniami. |

Poniższy przykład zawiera definicję kroku ADLA:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
    default_compute: adlacomp
    steps:
        Step1:
            runconfig: "yaml/default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            adla_step:
                name: "AdlaStep"
                script_name: "sample_script.usql"
                source_directory: "helloworld"
            inputs:
                employee_data:
                    source: employee_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: adftestadla
                    bind_mode: mount
```

### <a name="azure-batch-step"></a>Azure Batch krok

| Klucz YAML | Opis |
| ----- | ----- |
| `compute_target` | Obiekt docelowy obliczeń Azure Batch do użycia w tym kroku. |
| `inputs` | Dane wejściowe mogą być [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)lub [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Wynikami może być [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) lub [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | Katalog zawierający pliki binarne modułu, pliki wykonywalne, zestawy itp. |
| `executable` | Nazwa polecenia/pliku wykonywalnego, który zostanie uruchomiony w ramach tego zadania. |
| `create_pool` | Flaga logiczna wskazująca, czy należy utworzyć pulę przed uruchomieniem zadania. |
| `delete_batch_job_after_finish` | Flagę logiczną, aby wskazać, czy zadanie ma zostać usunięte z konta usługi Batch po zakończeniu. |
| `delete_batch_pool_after_finish` | Flaga logiczna wskazująca, czy należy usunąć pulę po zakończeniu zadania. |
| `is_positive_exit_code_failure` | Flaga logiczna wskazująca, czy zadanie kończy się niepowodzeniem, jeśli zadanie zostanie zakończone z kodem pozytywnym. |
| `vm_image_urn` | Jeśli `create_pool` jest `True`, a maszyna wirtualna używa `VirtualMachineConfiguration`. |
| `pool_id` | Identyfikator puli, w której zostanie uruchomione zadanie. |
| `allow_reuse` | Określa, czy krok ma ponownie używać poprzednich wyników po ponownym uruchomieniu z tymi samymi ustawieniami. |

Poniższy przykład zawiera definicję Azure Batch krok:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        input:
            datastore: workspaceblobstore
            path_on_datastore: "input.txt"
    default_compute: testbatch
    steps:
        Step1:
            runconfig: "D:\\AzureMlCli\\cli_testing\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            azurebatch_step:
                name: "AzureBatchStep"
                pool_id: "MyPoolName"
                create_pool: true
                executable: "azurebatch.cmd"
                source_directory: "D:\\AzureMlCli\\cli_testing"
                allow_reuse: false
            inputs:
                input:
                    source: input
            outputs:
                output:
                    destination: output
                    datastore: workspaceblobstore
```

### <a name="databricks-step"></a>Krok z datakostki

| Klucz YAML | Opis |
| ----- | ----- |
| `compute_target` | Obiekt docelowy obliczeń Azure Databricks do użycia w tym kroku. |
| `inputs` | Dane wejściowe mogą być [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)lub [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Wynikami może być [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) lub [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `run_name` | Nazwa w datakostkach dla tego przebiegu. |
| `source_directory` | Katalog zawierający skrypt i inne pliki. |
| `num_workers` | Statyczna liczba procesów roboczych dla elementów datakostks uruchamia klaster. |
| `runconfig` | Ścieżka do pliku `.runconfig`. Ten plik jest reprezentacją YAML klasy [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) . Aby uzyskać więcej informacji na temat struktury tego pliku, zobacz [runconfigschema. JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Określa, czy krok ma ponownie używać poprzednich wyników po ponownym uruchomieniu z tymi samymi ustawieniami. |

Poniższy przykład zawiera krok datakostki:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "dbtest"
    default_compute: mydatabricks
    steps:
        Step1:
            runconfig: "D:\\AzureMlCli\\cli_testing\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            databricks_step:
                name: "Databrickstep"
                run_name: "DatabrickRun"
                python_script_name: "train-db-local.py"
                source_directory: "D:\\AzureMlCli\\cli_testing\\databricks_train"
                num_workers: 1
                allow_reuse: true
            inputs:
                blob_test_data:
                    source: blob_test_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="data-transfer-step"></a>Krok transferu danych

| Klucz YAML | Opis |
| ----- | ----- |
| `compute_target` | Obiekt docelowy obliczeń Azure Data Factory do użycia w tym kroku. |
| `source_data_reference` | Połączenie wejściowe, które służy jako źródło operacji transferu danych. Obsługiwane wartości to [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)lub [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `destination_data_reference` | Połączenie wejściowe służące jako miejsce docelowe operacji transferu danych. Obsługiwane wartości to [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) i [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `allow_reuse` | Określa, czy krok ma ponownie używać poprzednich wyników po ponownym uruchomieniu z tymi samymi ustawieniami. |

Poniższy przykład zawiera krok transferu danych:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "testdata"
    default_compute: adftest
    steps:
        Step1:
            runconfig: "yaml/default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            data_transfer_step:
                name: "DataTransferStep"
                adla_compute_name: adftest
            source_data_reference:
                adls_test_data:
                    source: adls_test_data
            destination_data_reference:
                blob_test_data:
                    source: blob_test_data
```

### <a name="python-script-step"></a>Krok skryptu języka Python

| Klucz YAML | Opis |
| ----- | ----- |
| `compute_target` | Obiekt docelowy obliczeń do użycia w tym kroku. Obiektem docelowym obliczeń może być Azure Machine Learning obliczeniowa, maszyna wirtualna (taka jak Data Science VM) lub HDInsight. |
| `inputs` | Dane wejściowe mogą być [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)lub [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Wynikami może być [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) lub [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `script_name` | Nazwa skryptu języka Python (względem `source_directory`). |
| `source_directory` | Katalog zawierający skrypt, środowisko Conda itp. |
| `runconfig` | Ścieżka do pliku `.runconfig`. Ten plik jest reprezentacją YAML klasy [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) . Aby uzyskać więcej informacji na temat struktury tego pliku, zobacz [runconfig. JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Określa, czy krok ma ponownie używać poprzednich wyników po ponownym uruchomieniu z tymi samymi ustawieniami. |

Poniższy przykład zawiera krok skryptu języka Python:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        DataReference1:
            datastore: workspaceblobstore
            path_on_datastore: testfolder/sample.txt
    default_compute: cpu-cluster
    steps:
        Step1:
            runconfig: "yaml/default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            python_script_step:
                name: "PythonScriptStep"
                script_name: "train.py"
                allow_reuse: True
                source_directory: "helloworld"
            inputs:
                InputData:
                    source: DataReference1
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

## <a name="schedules"></a>Harmonogramy

Podczas definiowania harmonogramu dla potoku może to być magazyn danych — wyzwolony lub cykliczny na podstawie interwału czasu. Poniżej przedstawiono klucze służące do definiowania harmonogramu:

| Klucz YAML | Opis |
| ----- | ----- |
| `description` | Opis harmonogramu. |
| `recurrence` | Zawiera ustawienia cyklu, jeśli harmonogram jest cykliczny. |
| `pipeline_parameters` | Wszystkie parametry, które są wymagane przez potok. |
| `wait_for_provisioning` | Określa, czy czekać na zakończenie aprowizacji harmonogramu. |
| `wait_timeout` | Liczba sekund oczekiwania przed upływem limitu czasu. |
| `datastore_name` | Magazyn danych do monitorowania dla zmodyfikowanych/dodanych obiektów BLOB. |
| `polling_interval` | Czas trwania sondowania dla zmodyfikowanych/dodanych obiektów BLOB w minutach. Wartość domyślna: 5 minut. Obsługiwane tylko w przypadku harmonogramów magazynu danych. |
| `data_path_parameter_name` | Nazwa parametru potoku ścieżki danych do ustawienia ze zmienioną ścieżką obiektu BLOB. Obsługiwane tylko w przypadku harmonogramów magazynu danych. |
| `continue_on_step_failure` | Czy kontynuować wykonywanie innych kroków w przesłanych PipelineRun w przypadku niepowodzenia kroku. Jeśli ta opcja jest określona, zastąpi ustawienie `continue_on_step_failure` potoku.
| `path_on_datastore` | Opcjonalny. Ścieżka do magazynu danych do monitorowania dla zmodyfikowanych/dodanych obiektów BLOB. Ścieżka znajduje się w kontenerze dla magazynu danych, więc rzeczywista ścieżka monitorów harmonogramu to Container/`path_on_datastore`. Jeśli nie, kontener magazynu danych jest monitorowany. Dodatki/modyfikacje wprowadzone w podfolderze `path_on_datastore` nie są monitorowane. Obsługiwane tylko w przypadku harmonogramów magazynu danych. |

Poniższy przykład zawiera definicję harmonogramu wyzwalanego przez magazyn danych:

```yaml
Schedule: 
      description: "Test create with datastore" 
      recurrence: ~ 
      pipeline_parameters: {} 
      wait_for_provisioning: True 
      wait_timeout: 3600 
      datastore_name: "workspaceblobstore" 
      polling_interval: 5 
      data_path_parameter_name: "input_data" 
      continue_on_step_failure: None 
      path_on_datastore: "file/path" 
```

Podczas definiowania **harmonogramu cyklicznego**Użyj następujących kluczy w obszarze `recurrence`:

| Klucz YAML | Opis |
| ----- | ----- |
| `frequency` | Częstotliwość powtarzania harmonogramu. Prawidłowe wartości to `"Minute"`, `"Hour"`, `"Day"`, `"Week"`lub `"Month"`. |
| `interval` | Jak często wyzwalany jest harmonogram. Wartość całkowita to liczba jednostek czasu oczekiwania do momentu ponownego uruchomienia harmonogramu. |
| `start_time` | Godzina rozpoczęcia harmonogramu. Format ciągu wartości jest `YYYY-MM-DDThh:mm:ss`. Jeśli nie podano czasu rozpoczęcia, pierwsze obciążenie jest uruchamiane natychmiast, a przyszłe obciążenia są uruchamiane zgodnie z harmonogramem. Jeśli czas rozpoczęcia jest w przeszłości, pierwsze obciążenie zostanie uruchomione przy następnym obliczonym czasie wykonywania. |
| `time_zone` | Strefa czasowa godziny rozpoczęcia. Jeśli nie podano strefy czasowej, używany jest czas UTC. |
| `hours` | Jeśli `frequency` jest `"Day"` lub `"Week"`, można określić co najmniej jedną liczbę całkowitą z przedziału od 0 do 23, rozdzieloną przecinkami, jako godziny dnia, w którym potok powinien zostać uruchomiony. Można używać tylko `time_of_day` lub `hours` i `minutes`. |
| `minutes` | Jeśli `frequency` jest `"Day"` lub `"Week"`, można określić co najmniej jedną liczbę całkowitą z przedziału od 0 do 59, oddzieloną przecinkami, jako minuty godziny, kiedy potok powinien zostać uruchomiony. Można używać tylko `time_of_day` lub `hours` i `minutes`. |
| `time_of_day` | Jeśli `frequency` jest `"Day"` lub `"Week"`, możesz określić godzinę, o której ma być uruchamiany harmonogram. Format ciągu wartości jest `hh:mm`. Można używać tylko `time_of_day` lub `hours` i `minutes`. |zzs
| `week_days` | Jeśli `frequency` jest `"Week"`, możesz określić jeden lub więcej dni rozdzielonych przecinkami, gdy harmonogram powinien zostać uruchomiony. Prawidłowe wartości to `"Monday"`, `"Tuesday"`, `"Wednesday"`, `"Thursday"`, `"Friday"`, `"Saturday"`i `"Sunday"`. |

Poniższy przykład zawiera definicję harmonogramu cyklicznego:

```yaml
Schedule: 
    description: "Test create with recurrence" 
    recurrence: 
        frequency: Week # Can be "Minute", "Hour", "Day", "Week", or "Month". 
        interval: 1 # how often fires 
        start_time: 2019-06-07T10:50:00 
        time_zone: UTC 
        hours: 
        - 1 
        minutes: 
        - 0 
        time_of_day: null 
        week_days: 
        - Friday 
    pipeline_parameters: 
        'a': 1 
    wait_for_provisioning: True 
    wait_timeout: 3600 
    datastore_name: ~ 
    polling_interval: ~ 
    data_path_parameter_name: ~ 
    continue_on_step_failure: None 
    path_on_datastore: ~ 
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak używać rozszerzenia interfejsu wiersza polecenia dla Azure Machine Learning](reference-azure-machine-learning-cli.md).