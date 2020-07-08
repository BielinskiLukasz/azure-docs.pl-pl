---
title: Tworzenie Azure Machine Learningych zestawów danych w celu uzyskiwania do nich dostępu
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak tworzyć zestawy danych Azure Machine Learning, aby uzyskać dostęp do Twoich operacji w przypadku przebiegów eksperymentów usługi Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 06/29/2020
ms.openlocfilehash: baa238f36c41b5f494e8748cd5cd563bd212f483
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610714"
---
# <a name="create-azure-machine-learning-datasets"></a>Tworzenie zestawów danych Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule opisano sposób tworzenia Azure Machine Learning zestawów danych w celu uzyskania dostępu do danych dla eksperymentów lokalnych lub zdalnych. Aby dowiedzieć się, gdzie zestawy danych mieszczą się w przepływie pracy ogólnego dostępu do danych Azure Machine Learning, zobacz artykuł dotyczący [bezpiecznego dostępu do danych](concept-data.md#data-workflow) .

Za pomocą Azure Machine Learning zestawów danych można:

* Przechowywanie pojedynczej kopii danych w magazynie, do której odwołują się zestawy danych.

* Bezproblemowo Uzyskuj dostęp do danych podczas uczenia modelowego bez obaw o parametry połączenia lub ścieżki danych.

* Udostępnianie danych i współpraca z innymi użytkownikami.

## <a name="prerequisites"></a>Wymagania wstępne
"Aby utworzyć zestawy danych i korzystać z nich, potrzebne są:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

* [Obszar roboczy Azure Machine Learning](how-to-manage-workspace.md).

* [Zestaw Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), który obejmuje pakiet usługi Azure DataSets.

> [!NOTE]
> Niektóre klasy zestawu danych mają zależności w pakiecie [Azure preprodukcyjnym](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) , który jest zgodny z 64-bitowym językiem Python. W przypadku użytkowników systemu Linux te klasy są obsługiwane tylko w następujących dystrybucjach: Red Hat Enterprise Linux, Ubuntu, Fedora i CentOS.

## <a name="compute-size-guidance"></a>Wskazówki dotyczące rozmiaru obliczeń

Podczas tworzenia zestawu danych sprawdź moc obliczeniową obliczeniową i rozmiar danych w pamięci. Rozmiar danych w magazynie nie jest taki sam jak rozmiar danych w ramce Dataframe. Na przykład dane w plikach CSV można rozszerzyć o do 10X w ramce Dataframe, dzięki czemu plik CSV o pojemności 1 GB może być 10 GB w ramce Dataframe. 

Głównym czynnikiem jest to, jak duży zestaw danych znajduje się w pamięci, tj. jako ramka Dataframe. Zalecamy, aby rozmiar obliczeń i moc obliczeniowa zawierały rozmiar pamięci RAM. Dlatego jeśli ramka danych ma rozmiar 10GB, chcesz, aby obiekt docelowy obliczeń miał 20 + GB pamięci RAM, aby upewnić się, że ramka danych może być dopasowana do pamięci i przetworzona. Jeśli dane są skompresowane, można zwiększyć ich rozmiar. 20 GB stosunkowo rozrzedzonych danych przechowywanych w skompresowanym formacie Parquet można zwiększyć do ~ 800 GB w pamięci. Ponieważ pliki Parquet przechowują dane w formacie kolumnowym, w przypadku, gdy potrzebna jest tylko połowa kolumn, wystarczy załadować ~ 400 GB w pamięci.
 
Jeśli używasz Pandas, nie ma potrzeby używania więcej niż 1 vCPU, ponieważ będzie on używany przez cały siebie. W razie potrzeby można łatwo zrównoleglanie wiele procesorów wirtualnych vCPU na jednym Azure Machine Learning wystąpienia obliczeniowego/węźle za pośrednictwem Modin i Dask/ray oraz skalować w poziomie do dużego klastra, po prostu zmieniając opcję `import pandas as pd` na `import modin.pandas as pd` . 
 
Jeśli nie możesz uzyskać wystarczającej ilości danych wirtualnych na dużą skalę, masz dwie opcje: Użyj struktury, takiej jak Spark lub Dask, do przeprowadzenia przetwarzania danych "Brak pamięci", tj. element Dataframe jest ładowany do partycji RAM przez partycję i przetworzone, a końcowy wynik jest zbierany na końcu. Jeśli jest za mała, platforma Spark lub Dask umożliwia skalowanie w poziomie do klastra, który nadal może być używany interaktywnie. 

## <a name="dataset-types"></a>Typy zestawów danych

Istnieją dwa typy zestawów danych, w zależności od tego, jak użytkownicy zużywają je w szkoleniu:

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) reprezentuje dane w formacie tabelarycznym przez analizowanie dostarczonego pliku lub listy plików. Zapewnia to możliwość zmaterializowania danych do Pandas lub Spark Dataframe. Można utworzyć `TabularDataset` obiekt z plików CSV,. tsv,. Parquet,. JSON i z wyników zapytania SQL. Aby uzyskać pełną listę, zobacz [TabularDatasetFactory Class](https://aka.ms/tabulardataset-api-reference).

* Klasa [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) odwołuje się do jednego lub wielu plików w magazynach danych lub publicznych adresach URL. Za pomocą tej metody można pobrać lub zainstalować pliki do obliczeń jako obiekt FileDataset. Pliki mogą być w dowolnym formacie, co umożliwia szersze korzystanie z wielu scenariuszy uczenia maszynowego, w tym uczenie głębokie. 

## <a name="create-datasets"></a>Tworzenie zestawów danych

Tworząc zestaw danych, tworzysz odwołanie do lokalizacji źródła danych wraz z kopią jego metadanych. Ponieważ dane pozostają w istniejącej lokalizacji, nie pociąga to za sobą dodatkowych kosztów magazynu. Zestawy danych można tworzyć zarówno przy `TabularDataset` `FileDataset` użyciu zestawu SDK języka Python, jak i programu Azure Machine Learning Studio https://ml.azure.com .

Aby można było uzyskać dostęp do danych przez Azure Machine Learning, zestawy danych muszą zostać utworzone na podstawie ścieżek w [usłudze Azure datastores](how-to-access-data.md) lub publicznych adresów URL sieci Web. 

### <a name="use-the-sdk"></a>Korzystanie z zestawu SDK

Aby utworzyć zestawy danych z [usługi Azure datastore](how-to-access-data.md) przy użyciu zestawu SDK języka Python:

1. Sprawdź, czy masz `contributor` lub masz `owner` dostęp do zarejestrowanego magazynu danych platformy Azure.

2. Utwórz zestaw danych, odwołując się do ścieżek w magazynie danych.

> [!Note]
> Można utworzyć zestaw danych z wielu ścieżek w wielu magazynach. Nie ma żadnego sztywnego limitu liczby plików lub rozmiaru danych, z których można utworzyć zestaw danych. Jednak dla każdej ścieżki danych do usługi magazynu wysyłane są kilka żądań, aby sprawdzić, czy wskazuje ona plik lub folder. To obciążenie może prowadzić do obniżenia wydajności lub niepowodzenia. Zestaw danych odwołujący się do jednego folderu z plikami 1000 wewnątrz jest traktowany jako odwołujący się do jednej ścieżki danych. Zalecamy utworzenie zestawu danych, do którego odwołuje się mniej niż 100 ścieżek w magazynach datastores w celu uzyskania optymalnej wydajności.

#### <a name="create-a-tabulardataset"></a>Utwórz TabularDataset

Użyj [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-) metody `TabularDatasetFactory` klasy, aby odczytać pliki w formacie CSV lub TSV oraz utworzyć niezarejestrowane TabularDataset. W przypadku odczytywania z wielu plików wyniki zostaną zagregowane w jednej reprezentacji tabelarycznej. 

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

Domyślnie podczas tworzenia TabularDataset typy danych kolumny są wywnioskowane automatycznie. Jeśli wywnioskowane typy nie pasują do oczekiwań, można określić typy kolumn za pomocą następującego kodu. Ten parametr `infer_column_type` ma zastosowanie tylko w przypadku zestawów danych utworzonych na podstawie rozdzielanych plików. Możesz również [dowiedzieć się więcej na temat obsługiwanych typów danych](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py).

> [!IMPORTANT] 
> Jeśli magazyn znajduje się za siecią wirtualną lub zaporą, obsługiwane jest tylko tworzenie zestawu danych za pomocą zestawu SDK. Aby utworzyć zestaw danych, pamiętaj o uwzględnieniu parametrów `validate=False` i `infer_column_types=False` w `from_delimited_files()` metodzie. Spowoduje to Pominięcie sprawdzania poprawności początkowej i gwarantuje, że można utworzyć zestaw danych z tych bezpiecznych plików. 

```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Ocalałe|Pclass|Nazwa|Biciu|Wiek|SibSp|Parch|Bilet|Bezprzewodow|Kabin|Zaokrętowanie
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Fałsz|3|Braund, Mr. Owen Harris|mężczyzna|22,0|1|0|A/5 21171|7,2500||S
1|2|Prawda|1|Cumings, Pani. Jan Bradley (Florencji Briggs th...|kobieta|38,0|1|0|KOMPUTER 17599|71,2833|C85|C
2|3|Prawda|3|Heikkinen, chybień. Laina|kobieta|26,0|0|0|STON/O2. 3101282|7,9250||S

Aby utworzyć zestaw danych na podstawie Pandas pamięci Dataframe, Zapisz dane w lokalnym pliku, takim jak wolumin CSV, i Utwórz zestaw danych z tego pliku. Poniższy kod demonstruje ten przepływ pracy.

```python
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)
upload the local file to a datastore on the cloud
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required
from azureml.core import Workspace, Dataset

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

Użyj [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-) metody `TabularDatasetFactory` klasy, aby odczytać z Azure SQL Database:

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

W TabularDatasets można określić sygnaturę czasową z kolumny w danych lub z dowolnego miejsca, w którym są przechowywane dane wzorca ścieżki, aby włączyć cechę szeregów czasowych. Ta specyfikacja umożliwia łatwe i wydajne filtrowanie według czasu.

Użyj [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) metody `TabularDataset` klasy, aby określić kolumnę sygnatury czasowej i włączyć filtrowanie według czasu. Aby uzyskać więcej informacji, zobacz [Tabelaryczny pokaz interfejsu API związany z serią czasową z danymi pogody NOAA](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

```Python
# create a TabularDataset with time series trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with time-series-trait-specific methods
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1))
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))
```

#### <a name="create-a-filedataset"></a>Utwórz FileDataset

Użyj [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) metody `FileDatasetFactory` klasy, aby załadować pliki w dowolnym formacie i utworzyć niezarejestrowane FileDataset. Jeśli magazyn znajduje się za siecią wirtualną lub zaporą, należy ustawić parametr `validate =False` w `from_files()` metodzie. Pomija początkowy krok walidacji i gwarantuje, że można utworzyć zestaw danych z tych bezpiecznych plików.

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

#### <a name="on-the-web"></a>W Internecie 
Poniższe kroki i animacje pokazują, jak utworzyć zestaw danych w programie Azure Machine Learning Studio https://ml.azure.com .

![Tworzenie zestawu danych przy użyciu interfejsu użytkownika](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Aby utworzyć zestaw danych w programie Studio:
1. Zaloguj się pod adresem https://ml.azure.com .
1. Wybierz pozycję **zestawy danych** w sekcji **elementy zawartości** okienka po lewej stronie. 
1. Wybierz pozycję **Utwórz zestaw danych** , aby wybrać źródło zestawu danych. To źródło może być plikami lokalnymi, magazynem danych lub publicznymi adresami URL.
1. Wybierz **tabelaryczną** lub **plik** dla typu zestawu danych.
1. Wybierz pozycję **dalej** , aby otworzyć formularz **Magazyn danych i wybór pliku** . Na tym formularzu wybierz lokalizację, w której ma zostać zachowany zestaw danych, a także wybierz pliki danych, które mają być używane dla zestawu danych. 
1. Wybierz pozycję **dalej** , aby wypełnić **Ustawienia, Podgląd** i formularze **schematów** ; są one inteligentnie wypełniane na podstawie typu pliku i można później skonfigurować zestaw danych przed utworzeniem w tych formularzach. 
1. Wybierz pozycję **dalej** , aby przejrzeć formularz **Potwierdź szczegóły** . Sprawdź wybrane opcje i Utwórz opcjonalny profil danych dla zestawu danych. Dowiedz się więcej na temat [profilowania danych](how-to-use-automated-ml-for-ml-models.md#profile). 
1. Wybierz pozycję **Utwórz** , aby zakończyć tworzenie zestawu danych.

## <a name="register-datasets"></a>Rejestrowanie zestawów danych

Aby ukończyć proces tworzenia, zarejestruj zestawy danych w obszarze roboczym. Użyj [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) metody, aby zarejestrować zestawy danych w obszarze roboczym, aby udostępnić je innym osobom i ponownie wykorzystać je w ramach eksperymentów w obszarze roboczym:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> Zestawy danych utworzone za pomocą Azure Machine Learning Studio są automatycznie rejestrowane w obszarze roboczym.

## <a name="create-datasets-with-azure-open-datasets"></a>Tworzenie zestawów danych za pomocą otwartych zestawów danych platformy Azure

[Otwarte zestawy danych platformy Azure](https://azure.microsoft.com/services/open-datasets/) mają nadzorowane zestawy danych, których można użyć do dodawania funkcji specyficznych dla scenariusza do rozwiązań uczenia maszynowego w celu uzyskania dokładniejszych modeli. Zestawy danych obejmują dane z domeny publicznej na potrzeby pogodowych, spisu, świąt, bezpieczeństwa publicznego i lokalizacji, które ułatwiają uczenie modeli uczenia maszynowego i wzbogacanie rozwiązań predykcyjnych. Otwarte zestawy danych znajdują się w chmurze w Microsoft Azure i znajdują się zarówno w zestawie SDK, jak i w interfejsie użytkownika obszaru roboczego.

### <a name="use-the-sdk"></a>Korzystanie z zestawu SDK

Aby utworzyć zestawy danych przy użyciu otwartych zestawów danych platformy Azure z zestawu SDK, upewnij się, że pakiet został zainstalowany przy użyciu programu `pip install azureml-opendatasets` . Każdy zestaw danych dyskretnych jest reprezentowany przez własną klasę w zestawie SDK, a niektóre klasy są dostępne jako `TabularDataset` , `FileDataset` lub obie. Pełną listę klas można znaleźć w [dokumentacji referencyjnej](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) .

Niektóre klasy można pobrać z `TabularDataset` lub `FileDataset` , co pozwala na manipulowanie i/lub pobranie plików bezpośrednio. Inne klasy mogą uzyskać zestaw danych **tylko** przy użyciu jednej `get_tabular_dataset()` lub `get_file_dataset()` funkcji. Poniższy przykład kodu pokazuje kilka przykładów tych typów klas.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Po zarejestrowaniu zestawu danych utworzonego na podstawie otwartych zestawów danych żadne dane nie są natychmiast pobierane, ale dane będą dostępne później na żądanie (na przykład na potrzeby szkolenia) z centralnej lokalizacji magazynu.

### <a name="use-the-ui"></a>Korzystanie z interfejsu użytkownika

Zestawy danych można także tworzyć z klas otwartych zestawów danych za pośrednictwem interfejsu użytkownika. W obszarze roboczym wybierz kartę **zestawy danych** w obszarze **zasoby**. Z menu rozwijanego **Utwórz zestaw danych** wybierz pozycję **z otwarte zestawy**danych.

![Otwórz zestaw danych przy użyciu interfejsu użytkownika](./media/how-to-create-register-datasets/open-datasets-1.png)

Wybierz zestaw danych, wybierając jego kafelek. (Istnieje możliwość filtrowania przy użyciu paska wyszukiwania). Wybierz pozycję **dalej**.

![Wybierz zestaw danych](./media/how-to-create-register-datasets/open-datasets-2.png)

Wybierz nazwę, pod którą ma zostać zarejestrowany zestaw danych, i opcjonalnie odfiltruj dane przy użyciu dostępnych filtrów. W tym przypadku dla zestawu danych dni wolnych, należy odfiltrować przedział czasu do jednego roku i kod kraju tylko do USA. Wybierz pozycję **Utwórz**.

![Ustaw parametry zestawu danych i Utwórz zestaw danych](./media/how-to-create-register-datasets/open-datasets-3.png)

Zestaw danych jest teraz dostępny w obszarze roboczym w obszarze **zestawy danych**. Można go użyć w taki sam sposób jak inne utworzone zestawy danych.

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

## <a name="access-datasets-in-your-script"></a>Dostęp do zestawów danych w skrypcie

Zarejestrowane zestawy danych są dostępne lokalnie i zdalnie w klastrach obliczeniowych, takich jak Azure Machine Learning COMPUTE. Aby uzyskać dostęp do zarejestrowanego zestawu danych w ramach eksperymentów, użyj następującego kodu, aby uzyskać dostęp do obszaru roboczego i zarejestrowano zestaw danych według nazwy. Domyślnie [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) Metoda `Dataset` klasy zwraca najnowszą wersję zestawu danych, który jest zarejestrowany w obszarze roboczym.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="access-datasets-in-a-virtual-network"></a>Dostęp do zestawów danych w sieci wirtualnej

Jeśli obszar roboczy znajduje się w sieci wirtualnej, musisz skonfigurować zestaw danych, aby pominąć walidację. Aby uzyskać więcej informacji na temat sposobu korzystania z magazynów danych i ich zestawów w sieci wirtualnej, zapoznaj się z tematem [izolacja sieci podczas uczenia & wnioskowania z prywatnymi sieciami wirtualnymi](how-to-enable-virtual-network.md#use-datastores-and-datasets).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak uczenie się z zestawami danych](how-to-train-with-datasets.md).
* Używaj automatycznej uczenia maszynowego do [uczenia się z TabularDatasets](https://aka.ms/automl-dataset).
* Aby uzyskać więcej przykładów szkoleniowych dotyczących zestawu danych, zobacz [przykładowe notesy](https://aka.ms/dataset-tutorial).
