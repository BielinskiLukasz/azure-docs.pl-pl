---
title: Tworzenie Azure Machine Learningych zestawów danych w celu uzyskiwania do nich dostępu
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak tworzyć zestawy danych Azure Machine Learning, aby uzyskać dostęp do Twoich operacji w przypadku przebiegów eksperymentów usługi Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.openlocfilehash: ae8dcc02618220750e2d15d815da4b36ea64da2d
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782196"
---
# <a name="create-azure-machine-learning-datasets"></a>Tworzenie zestawów danych usługi Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule opisano sposób tworzenia Azure Machine Learning zestawów danych w celu uzyskania dostępu do danych dla eksperymentów lokalnych lub zdalnych. Aby dowiedzieć się, gdzie zestawy danych mieszczą się w przepływie pracy ogólnego dostępu do danych Azure Machine Learning, zobacz artykuł dotyczący [bezpiecznego dostępu do danych](concept-data.md#data-workflow) .

Tworząc zestaw danych, tworzysz odwołanie do lokalizacji źródła danych wraz z kopią jego metadanych. Ponieważ dane pozostają w istniejącej lokalizacji, nie ponosisz żadnych dodatkowych kosztów magazynu i nie ryzykują integralności źródeł danych. Ponadto zestawy danych są opóźnieniem oceniane, co ułatwia szybkość działania przepływu pracy. Zestawy danych mogą być tworzone z magazynów danych, publicznych adresów URL i [otwartych zestawów danych platformy Azure](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md).

Za pomocą Azure Machine Learning zestawów danych można:

* Przechowywanie pojedynczej kopii danych w magazynie, do której odwołują się zestawy danych.

* Bezproblemowo Uzyskuj dostęp do danych podczas uczenia modelowego bez obaw o parametry połączenia lub ścieżki danych. [Dowiedz się więcej na temat uczenia się z zestawami danych](how-to-train-with-datasets.md).

* Udostępnianie danych i współpraca z innymi użytkownikami.

## <a name="prerequisites"></a>Wymagania wstępne

Aby tworzyć zestawy danych i korzystać z nich, potrzebne są:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

* [Obszar roboczy Azure Machine Learning](how-to-manage-workspace.md).

* [Zestaw Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), który obejmuje pakiet usługi Azure DataSets.

    * Utwórz [wystąpienie obliczeniowe Azure Machine Learning](concept-compute-instance.md#managing-a-compute-instance), które jest w pełni skonfigurowane i zarządzane środowisko programistyczne, które zawiera zintegrowane notesy oraz już zainstalowany zestaw SDK.

    **ORAZ**

    * Pracuj nad własnym notesem Jupyter i samodzielnie Instaluj zestaw SDK, korzystając z [tych instrukcji](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

> [!NOTE]
> Niektóre klasy zestawu danych mają zależności w pakiecie [Azure preprodukcyjnym](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) , który jest zgodny z 64-bitowym językiem Python. W przypadku użytkowników systemu Linux te klasy są obsługiwane tylko w następujących dystrybucjach: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) i CentOS (7).

## <a name="compute-size-guidance"></a>Wskazówki dotyczące rozmiaru obliczeń

Podczas tworzenia zestawu danych Przejrzyj moc obliczeniową obliczeniową i rozmiar danych w pamięci. Rozmiar danych w magazynie nie jest taki sam jak rozmiar danych w ramce Dataframe. Na przykład dane w plikach CSV można rozszerzyć o do 10X w ramce Dataframe, dzięki czemu plik CSV o pojemności 1 GB może być 10 GB w ramce Dataframe. 

Jeśli dane są skompresowane, można zwiększyć ich rozmiar. 20 GB stosunkowo rozrzedzonych danych przechowywanych w skompresowanym formacie Parquet można zwiększyć do ~ 800 GB w pamięci. Ponieważ pliki Parquet przechowują dane w formacie kolumnowym, w przypadku, gdy potrzebna jest tylko połowa kolumn, wystarczy załadować ~ 400 GB w pamięci.

[Dowiedz się więcej na temat optymalizowania przetwarzania danych w Azure Machine Learning](concept-optimize-data-processing.md).

## <a name="dataset-types"></a>Typy zestawów danych

Istnieją dwa typy zestawów danych, w zależności od tego, jak użytkownicy zużywają je w szkoleniu. FileDatasets i TabularDatasets. Oba typy mogą być używane w Azure Machine Learning szkoleń dotyczących przepływów pracy obejmujących, szacowania, AutoML, moje napędy i potoki. 

### <a name="filedataset"></a>FileDataset

[FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) odwołuje się do jednego lub wielu plików w magazynach danych lub publicznych adresach URL. Jeśli dane zostały już oczyszczone i gotowe do użycia w eksperymentach szkoleniowych, możesz [pobrać lub zainstalować](how-to-train-with-datasets.md#mount-vs-download) pliki do obliczeń jako obiekt FileDataset. 

Zalecamy FileDatasets dla przepływów pracy usługi Machine Learning, ponieważ pliki źródłowe mogą być w dowolnym formacie, co pozwala na szersze scenariusze uczenia maszynowego, w tym uczenie głębokie.

Tworzenie FileDataset przy użyciu [zestawu SDK języka Python](#create-a-filedataset) lub [Azure Machine Learning Studio](#create-datasets-in-the-studio)

### <a name="tabulardataset"></a>TabularDataset

[TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) reprezentuje dane w formacie tabelarycznym przez analizowanie dostarczonego pliku lub listy plików. Zapewnia to możliwość zmaterializowania danych w Pandas lub Spark Dataframe, dzięki czemu możesz współpracować ze znanymi bibliotekami przygotowywania i uczenia danych bez konieczności opuszczania Twojego notesu. Można utworzyć `TabularDataset` obiekt z plików CSV,. tsv,. Parquet,. JSON i z [wyników zapytania SQL](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-).

Za pomocą TabularDatasets można określić sygnaturę czasową z kolumny w danych lub z dowolnego miejsca, w którym są przechowywane dane wzorca ścieżki, aby włączyć cechę szeregów czasowych. Ta specyfikacja umożliwia łatwe i wydajne filtrowanie według czasu. Aby zapoznać się z przykładem, zobacz [Tabelaryczny pokaz interfejsu API związany z szeregiem czasowym z danymi pogody NOAA](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

Utwórz TabularDataset za pomocą [zestawu SDK języka Python](#create-a-tabulardataset) lub [Azure Machine Learning Studio](#create-datasets-in-the-studio).

>[!NOTE]
> Przepływy pracy AutoML wygenerowane za pośrednictwem Azure Machine Learning Studio obsługują obecnie tylko TabularDatasets. 

## <a name="access-datasets-in-a-virtual-network"></a>Dostęp do zestawów danych w sieci wirtualnej

Jeśli obszar roboczy znajduje się w sieci wirtualnej, musisz skonfigurować zestaw danych, aby pominąć walidację. Aby uzyskać więcej informacji na temat sposobu korzystania z magazynów danych i ich zestawów w sieci wirtualnej, zapoznaj się z tematem [izolacja sieci podczas uczenia & wnioskowania z prywatnymi sieciami wirtualnymi](how-to-enable-virtual-network.md#use-datastores-and-datasets).

<a name="datasets-sdk"></a>

## <a name="create-datasets-via-the-sdk"></a>Tworzenie zestawów danych za pośrednictwem zestawu SDK

 Aby można było uzyskać dostęp do danych przez Azure Machine Learning, zestawy danych muszą zostać utworzone na podstawie ścieżek w [usłudze Azure datastores](how-to-access-data.md) lub publicznych adresów URL sieci Web. 

Aby utworzyć zestawy danych z [usługi Azure datastore](how-to-access-data.md) za pomocą zestawu SDK języka Python:

1. Sprawdź, czy masz `contributor` lub masz `owner` dostęp do zarejestrowanego magazynu danych platformy Azure.

2. Utwórz zestaw danych, odwołując się do ścieżek w magazynie danych. Można utworzyć zestaw danych z wielu ścieżek w wielu magazynach. Nie ma żadnego sztywnego limitu liczby plików lub rozmiaru danych, z których można utworzyć zestaw danych. 

> [!Note]
> Dla każdej ścieżki danych do usługi magazynu wysyłane są kilka żądań, aby sprawdzić, czy wskazuje ona plik lub folder. To obciążenie może prowadzić do obniżenia wydajności lub niepowodzenia. Zestaw danych odwołujący się do jednego folderu z plikami 1000 wewnątrz jest traktowany jako odwołujący się do jednej ścieżki danych. Zalecamy utworzenie zestawu danych, do którego odwołuje się mniej niż 100 ścieżek w magazynach datastores w celu uzyskania optymalnej wydajności.

### <a name="create-a-filedataset"></a>Utwórz FileDataset

Użyj [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) metody `FileDatasetFactory` klasy, aby załadować pliki w dowolnym formacie i utworzyć niezarejestrowane FileDataset. 

Jeśli magazyn znajduje się za siecią wirtualną lub zaporą, należy ustawić parametr `validate=False` w `from_files()` metodzie. Pomija początkowy krok walidacji i gwarantuje, że można utworzyć zestaw danych z tych bezpiecznych plików. Dowiedz się więcej o sposobach używania [magazynów danych i zestawów w sieci wirtualnej](how-to-enable-virtual-network.md#use-datastores-and-datasets).

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

### <a name="create-a-tabulardataset"></a>Utwórz TabularDataset

Użyj [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) metody `TabularDatasetFactory` klasy, aby odczytać pliki w formacie CSV lub TSV oraz utworzyć niezarejestrowane TabularDataset. W przypadku odczytywania z wielu plików wyniki zostaną zagregowane w jednej reprezentacji tabelarycznej. 

Jeśli magazyn znajduje się za siecią wirtualną lub zaporą, należy ustawić parametr `validate=False` w `from_delimited_files()` metodzie. Pomija początkowy krok walidacji i gwarantuje, że można utworzyć zestaw danych z tych bezpiecznych plików. Dowiedz się więcej o sposobach używania [magazynów danych i zestawów w sieci wirtualnej](how-to-enable-virtual-network.md#use-datastores-and-datasets).

Poniższy kod pobiera istniejący obszar roboczy i żądany magazyn danych według nazwy. Następnie przekazuje magazyn danych i lokalizacje plików do `path` parametru, aby utworzyć nowy TabularDataset `weather_ds` .

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Domyślnie podczas tworzenia TabularDataset typy danych kolumny są wywnioskowane automatycznie. Jeśli wywnioskowane typy nie pasują do oczekiwań, można określić typy kolumn za pomocą następującego kodu. Ten parametr `infer_column_type` ma zastosowanie tylko w przypadku zestawów danych utworzonych na podstawie rozdzielanych plików. [Dowiedz się więcej na temat obsługiwanych typów danych](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py).


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|Indeks|PassengerId|Ocalałe|Pclass|Nazwa|Biciu|Wiek|SibSp|Parch|Bilet|Bezprzewodow|Kabin|Zaokrętowanie
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Fałsz|3|Braund, Mr. Owen Harris|mężczyzna|22,0|1|0|A/5 21171|7,2500||S
1|2|Prawda|1|Cumings, Pani. Jan Bradley (Florencji Briggs th...|kobieta|38,0|1|0|KOMPUTER 17599|71,2833|C85|C
2|3|Prawda|3|Heikkinen, chybień. Laina|kobieta|26,0|0|0|STON/O2. 3101282|7,9250||S

### <a name="create-a-dataset-from-pandas-dataframe"></a>Tworzenie zestawu danych z Pandas Dataframe

Aby utworzyć TabularDataset z ramki datapandas Dataframe, Zapisz dane w lokalnym pliku, takim jak wolumin CSV, i Utwórz zestaw danych z tego pliku. Poniższy kod demonstruje ten przepływ pracy.

```python
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required

from azureml.core import Workspace, Dataset
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)
upload the local file to a datastore on the cloud

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')

# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(datastore.path('data/prepared.csv'))
```

## <a name="register-datasets"></a>Rejestrowanie zestawów danych

Aby ukończyć proces tworzenia, zarejestruj zestawy danych w obszarze roboczym. Użyj [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) metody, aby zarejestrować zestawy danych w obszarze roboczym, aby udostępnić je innym osobom i ponownie wykorzystać je w ramach eksperymentów w obszarze roboczym:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

<a name="datasets-ui"></a>
## <a name="create-datasets-in-the-studio"></a>Tworzenie zestawów danych w programie Studio
Poniższe kroki i animacje pokazują, jak utworzyć zestaw danych w programie [Azure Machine Learning Studio](https://ml.azure.com).

> [!Note]
> Zestawy danych utworzone za pomocą Azure Machine Learning Studio są automatycznie rejestrowane w obszarze roboczym.

![Tworzenie zestawu danych przy użyciu interfejsu użytkownika](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Aby utworzyć zestaw danych w programie Studio:
1. Zaloguj się pod adresem https://ml.azure.com .
1. Wybierz pozycję **zestawy danych** w sekcji **elementy zawartości** okienka po lewej stronie. 
1. Wybierz pozycję **Utwórz zestaw danych** , aby wybrać źródło zestawu danych. To źródło może być plikami lokalnymi, magazynem danych lub publicznymi adresami URL.
1. Wybierz **tabelaryczną** lub **plik** dla typu zestawu danych.
1. Wybierz pozycję **dalej** , aby otworzyć formularz **Magazyn danych i wybór pliku** . Na tym formularzu wybierz lokalizację, w której ma zostać zachowany zestaw danych, a także wybierz pliki danych, które mają być używane dla zestawu danych. 
    1. Włącz weryfikację pomijania, jeśli dane są w sieci wirtualnej. Dowiedz się więcej o [izolacji i prywatności sieci wirtualnej](how-to-enable-virtual-network.md#machine-learning-studio).
1. Wybierz pozycję **dalej** , aby wypełnić **Ustawienia, Podgląd** i formularze **schematów** ; są one inteligentnie wypełniane na podstawie typu pliku i można później skonfigurować zestaw danych przed utworzeniem w tych formularzach. 
1. Wybierz pozycję **dalej** , aby przejrzeć formularz **Potwierdź szczegóły** . Sprawdź wybrane opcje i Utwórz opcjonalny profil danych dla zestawu danych. Dowiedz się więcej na temat [profilowania danych](how-to-use-automated-ml-for-ml-models.md#profile). 
1. Wybierz pozycję **Utwórz** , aby zakończyć tworzenie zestawu danych.

## <a name="create-datasets-with-azure-open-datasets"></a>Tworzenie zestawów danych za pomocą otwartych zestawów danych platformy Azure

[Otwarte zestawy danych platformy Azure](https://azure.microsoft.com/services/open-datasets/) mają nadzorowane zestawy danych, których można użyć do dodawania funkcji specyficznych dla scenariusza do rozwiązań uczenia maszynowego w celu uzyskania dokładniejszych modeli. Zestawy danych obejmują dane z domeny publicznej na potrzeby pogodowych, spisu, świąt, bezpieczeństwa publicznego i lokalizacji, które ułatwiają uczenie modeli uczenia maszynowego i wzbogacanie rozwiązań predykcyjnych. Otwarte zestawy danych znajdują się w chmurze w Microsoft Azure i znajdują się w zestawie SDK i Studio.

Dowiedz się, jak tworzyć [zestawy danych Azure Machine Learning z poziomu otwartych zestawów danych platformy Azure](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md). 

## <a name="train-with-datasets"></a>Szkolenie przy użyciu zestawów danych

Używaj Twoich zestawów danych w eksperymentach uczenia maszynowego do uczenia modeli ML. [Dowiedz się więcej na temat uczenia się z zestawami danych](how-to-train-with-datasets.md)

## <a name="version-datasets"></a>Zestawy danych wersji

Nowy zestaw danych można zarejestrować pod tą samą nazwą, tworząc nową wersję. Wersja zestawu danych to sposób tworzenia zakładek stanu danych, dzięki czemu można zastosować określoną wersję zestawu danych na potrzeby eksperymentowania lub kopiowania w przyszłości. Dowiedz się więcej o [wersjach zestawu danych](how-to-version-track-datasets.md).
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak uczenie się z zestawami danych](how-to-train-with-datasets.md).
* Używaj automatycznej uczenia maszynowego do [uczenia się z TabularDatasets](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).
* Aby uzyskać więcej przykładów szkoleniowych dotyczących zestawu danych, zobacz [przykładowe notesy](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).
