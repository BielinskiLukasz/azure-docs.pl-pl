---
title: Uruchamianie skryptów Python za Data Factory
description: Samouczek — informacje o sposobie uruchamiania skryptów języka Python w ramach potoku za pośrednictwem Azure Data Factory przy użyciu Azure Batch.
author: mammask
ms.devlang: python
ms.topic: tutorial
ms.date: 12/11/2019
ms.author: komammas
ms.custom: mvc
ms.openlocfilehash: 49f675a56247433ce92763a69045fb214c7c37dc
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83723072"
---
# <a name="tutorial-run-python-scripts-through-azure-data-factory-using-azure-batch"></a>Samouczek: uruchamianie skryptów Python za pomocą Azure Data Factory przy użyciu Azure Batch

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Uwierzytelnianie przy użyciu kont usług Batch i Storage
> * Opracowywanie i uruchamianie skryptu w języku Python
> * Tworzenie puli węzłów obliczeniowych w celu uruchomienia aplikacji
> * Planowanie obciążeń języka Python
> * Monitorowanie potoku analizy
> * Dostęp do plików dziennika

W poniższym przykładzie jest uruchamiany skrypt języka Python, który odbiera dane wejściowe woluminu CSV z kontenera magazynu obiektów blob, wykonuje proces manipulowania danymi i zapisuje dane wyjściowe do oddzielnego kontenera magazynu obiektów BLOB.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstalowana dystrybucja języka [Python](https://www.python.org/downloads/) do testowania lokalnego.
* Pakiet [platformy Azure](https://pypi.org/project/azure/) `pip` .
* Konto usługi Azure Batch i połączone konto usługi Azure Storage. Zobacz [Tworzenie konta usługi Batch](quick-create-portal.md#create-a-batch-account) , aby uzyskać więcej informacji na temat tworzenia i łączenia kont usługi Batch z kontami magazynu.
* Konto Azure Data Factory. Aby uzyskać więcej informacji na temat tworzenia fabryki danych za pomocą Azure Portal, zobacz temat [Tworzenie fabryki danych](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory) .
* [Batch Explorer](https://azure.github.io/BatchExplorer/).
* [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="create-a-batch-pool-using-batch-explorer"></a>Tworzenie puli usługi Batch przy użyciu Batch Explorer

W tej sekcji użyjesz Batch Explorer do utworzenia puli usługi Batch, która będzie używana przez potok usługi Azure Data Factory. 

1. Zaloguj się, aby Batch Explorer przy użyciu poświadczeń platformy Azure.
1. Wybierz konto w usłudze Batch
1. Utwórz pulę, wybierając pozycję **Pule** na pasku po lewej stronie, a następnie przycisk **Dodaj** nad formularzem wyszukiwania. 
    1. Wybierz identyfikator i nazwę wyświetlaną. Będziemy używać `custom-activity-pool` tego przykładu.
    1. Ustaw typ skali na **stały rozmiar**i ustaw liczbę węzłów dedykowanych na 2.
    1. W obszarze **nauka danych**wybierz pozycję **Dsvm systemu Windows** jako system operacyjny.
    1. Wybierz `Standard_f2s_v2` rozmiar maszyny wirtualnej.
    1. Włącz zadanie uruchamiania i Dodaj polecenie `cmd /c "pip install pandas"` . Tożsamość użytkownika może pozostać jako domyślny **użytkownik puli**.
    1. Wybierz przycisk **OK**.

## <a name="create-blob-containers"></a>Tworzenie kontenerów obiektów BLOB

Tutaj utworzysz kontenery obiektów blob, które będą przechowywać pliki wejściowe i wyjściowe dla zadania wsadowego OCR.

1. Zaloguj się, aby Eksplorator usługi Storage przy użyciu poświadczeń platformy Azure.
1. Korzystając z konta magazynu połączonego z kontem usługi Batch, Utwórz dwa kontenery obiektów BLOB (jeden dla plików wejściowych, jeden dla plików wyjściowych), wykonując czynności opisane w [sekcji Tworzenie kontenera obiektów BLOB](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container).
    * W tym przykładzie wywołamy nasz kontener wejściowy `input` oraz nasz kontener wyjściowy `output` .
1. Przekaż `main.py` i `iris.csv` do kontenera wejściowego `input` przy użyciu Eksplorator usługi Storage, wykonując czynności opisane w temacie [Zarządzanie obiektami BLOB w kontenerze obiektów BLOB](../vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container)


## <a name="develop-a-script-in-python"></a>Opracowywanie skryptu w języku Python

Poniższy skrypt w języku Python ładuje `iris.csv` zestaw danych z `input` kontenera, wykonuje proces manipulowania danymi i zapisuje wyniki z powrotem do `output` kontenera.

``` python
# Load libraries
from azure.storage.blob import BlockBlobService
import pandas as pd

# Define parameters
storageAccountName = "<storage-account-name>"
storageKey         = "<storage-account-key>"
containerName      = "output"

# Establish connection with the blob storage account
blobService = BlockBlobService(account_name=storageAccountName,
                               account_key=storageKey
                               )

# Load iris dataset from the task node
df = pd.read_csv("iris.csv")

# Subset records
df = df[df['Species'] == "setosa"]

# Save the subset of the iris dataframe locally in task node
df.to_csv("iris_setosa.csv", index = False)

# Upload iris dataset
blobService.create_blob_from_text(containerName, "iris_setosa.csv", "iris_setosa.csv")
```

Zapisz skrypt jako `main.py` i przekaż go do kontenera **usługi Azure Storage** . Przed przekazaniem do kontenera obiektów BLOB upewnij się, że funkcja jest testowana i sprawdzana lokalnie.

``` bash
python main.py
```

## <a name="set-up-an-azure-data-factory-pipeline"></a>Konfigurowanie potoku Azure Data Factory

W tej sekcji utworzysz potok i zweryfikujesz go za pomocą skryptu języka Python.

1. Postępuj zgodnie z instrukcjami, aby utworzyć fabrykę danych w sekcji "Tworzenie fabryki danych" w [tym artykule](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).
1. W polu **zasoby fabryki** wybierz przycisk + (plus), a następnie wybierz pozycję **potok**
1. Na karcie **Ogólne** Ustaw nazwę potoku jako "Uruchom Python".

    ![](./media/run-python-batch-azure-data-factory/create-pipeline.png)

1. W polu **działania** rozwiń pozycję **Usługa Batch**. Przeciągnij działanie niestandardowe z przybornika **działania** na powierzchnię projektanta potoku.
1. Na karcie **Ogólne** Określ **testPipeline** dla nazwy

    ![](./media/run-python-batch-azure-data-factory/create-custom-task.png)
1. Na karcie **Azure Batch** Dodaj **konto wsadowe** , które zostało utworzone w poprzednich krokach, i **Test connection** , aby upewnić się, że zakończyło się pomyślnie

    ![](./media/run-python-batch-azure-data-factory/integrate-pipeline-with-azure-batch.png)

1. Na karcie **Ustawienia** wprowadź polecenie `python main.py` .
1. W przypadku **połączonej usługi zasobów**Dodaj konto magazynu, które zostało utworzone w poprzednich krokach. Przetestuj połączenie, aby upewnić się, że zakończyło się pomyślnie.
1. W **ścieżce folderu**wybierz nazwę kontenera **BLOB Storage platformy Azure** , który zawiera skrypt języka Python i skojarzone dane wejściowe. Spowoduje to pobranie wybranych plików z kontenera do wystąpień węzłów puli przed wykonaniem skryptu języka Python.

    ![](./media/run-python-batch-azure-data-factory/create-custom-task-py-script-command.png)
1. Aby sprawdzić poprawność ustawień potoku, kliknij pozycję **Weryfikuj** na pasku narzędzi potoku powyżej kanwy. Sprawdź, czy potok został pomyślnie zweryfikowany. Wybierz przycisk &gt;&gt; (strzałka w prawo), aby zamknąć dane wyjściowe weryfikacji.
1. Kliknij pozycję **Debuguj** , aby przetestować potok i upewnić się, że działa prawidłowo.
1. Kliknij przycisk **Opublikuj** , aby opublikować potok.
1. Kliknij przycisk **Wyzwól** , aby uruchomić skrypt języka Python w ramach procesu wsadowego.

    ![](./media/run-python-batch-azure-data-factory/create-custom-task-py-success-run.png)

### <a name="monitor-the-log-files"></a>Monitorowanie plików dziennika

W przypadku wygenerowania ostrzeżeń lub błędów przez wykonanie skryptu można wyewidencjonować `stdout.txt` lub `stderr.txt` uzyskać więcej informacji na temat danych wyjściowych, które zostały zarejestrowane.

1. Wybierz pozycję **zadania** z lewej strony Batch Explorer.
1. Wybierz zadanie utworzone przez fabrykę danych. Przy założeniu, że Nazwa puli została `custom-activity-pool` wybrana, wybierz opcję `adfv2-custom-activity-pool` .
1. Kliknij zadanie, które miało kod zakończenia błędu.
1. Wyświetl `stdout.txt` i `stderr.txt` Zbadaj swój problem.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono przykład, w którym pokazano, jak uruchamiać skrypty języka Python jako część potoku za pośrednictwem Azure Data Factory przy użyciu Azure Batch.

Aby dowiedzieć się więcej na temat Azure Data Factory, zobacz:

> [!div class="nextstepaction"]
> [Azure Data Factory](../data-factory/introduction.md) 
>  [Potoki i działania](../data-factory/concepts-pipelines-activities.md) 
>  [Działania niestandardowe](../data-factory/transform-data-using-dotnet-custom-activity.md)
