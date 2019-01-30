---
title: Co nowego w wersji?
titleSuffix: Azure Machine Learning service
description: Dowiedz się więcej o najnowszych aktualizacjach usługi Azure Machine Learning i uczenia maszynowego i zestawy SDK Python przeznaczonego do przygotowania danych.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
author: hning86
ms.author: haining
ms.reviewer: j-martens
ms.date: 12/20/2018
ms.custom: seodec18
ms.openlocfilehash: 9556a87b8de08dadb211d73e93f3daa9033c2cc1
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221321"
---
# <a name="azure-machine-learning-service-release-notes"></a>Informacje o wersji usługi Azure Machine Learning

Ten artykuł zawiera informacje o wersji usługi Azure Machine Learning. 

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v1.0.10

+ **Dokumentacja zestawu SDK**: https://aka.ms/aml-sdk

+ **Zmiany**: 
  + Zestaw SDK usługi Azure ML nie ma już pakiety wiersza polecenia platformy azure jako zależność. W szczególności zależności azure-cli-core i profilu w przypadku interfejsu wiersza polecenia platformy azure zostały usunięte z usługi Azure ml core. Poniżej przedstawiono użytkownika, wpływ na zmiany:
    + Jeśli wykonanie "az login", a następnie za pomocą zestawu sdk usługi Azure ml, zestaw SDK będzie wykonywać przeglądarki lub logowanie do urządzenia kod jeszcze raz. Nie będzie ona używać każdy stan poświadczeń utworzonych przez "az login".
    + Uwierzytelnianie wiersza polecenia platformy Azure, takiej jak "az login", można użyć _azureml.core.authentication.AzureCliAuthentication_ klasy. Uwierzytelnianie wiersza polecenia platformy Azure, wykonaj _polecenia pip install azure cli_ w środowisku Python, w którym jest zainstalowany zestaw sdk usługi Azure ml.
    + Jeśli macie "az login", przy użyciu nazwy głównej usługi do automatyzacji, zalecamy użycie _azureml.core.authentication.ServicePrincipalAuthentication_ klasy, jak zestaw sdk usługi Azure ml nie będzie używać stan poświadczeń utworzonych przez wiersza polecenia platformy azure. 

+ **Poprawki błędów**: Ta wersja zawiera przede wszystkim poprawki drobnych błędów

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Zestaw SDK v1.0.8 przeznaczonego do przygotowania danych usługi Azure Machine Learning

+ **Dokumentacja zestawu SDK**: https://aka.ms/data-prep-sdk

+ **Poprawki błędów**
  + Znaczne zwiększenie wydajności pobieranie danych profilów.
  + Naprawiono drobnych usterek związane z raportowaniem błędów.
  
### <a name="azure-portal-new-features"></a>Witryna Azure portal: nowe funkcje
+ Nowe przeciągnij i upuść środowisko tworzenia wykresów dla raportów. Użytkownikom można przeciągnąć kolumny lub atrybutu z obszaru do obszaru wykresu, do której system automatycznie wybiera typ odpowiedniego wykresu dla użytkownika, na podstawie typu danych. Użytkownikom można zmienić typ wykresu do innych odpowiednich typów lub dodawać dodatkowe atrybuty.

    Obsługiwane typy wykresów:
    - Wykres liniowy
    - Histogram
    - Skumulowany wykres słupkowy
    - Wykres skrzynkowy
    - Wykres punktowy
    - Wykres bąbelkowy

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v1.0.8

+ **Dokumentacja zestawu SDK**: https://aka.ms/aml-sdk

+ **Poprawki błędów**: Ta wersja zawiera przede wszystkim poprawki drobnych błędów

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Zestaw SDK v1.0.7 przeznaczonego do przygotowania danych usługi Azure Machine Learning

+ **Dokumentacja zestawu SDK**: https://aka.ms/data-prep-sdk

+ **Nowe funkcje**
  + Ulepszenia magazynu danych (udokumentowane w artykule [magazyn danych jak-to-przewodnik](https://github.com/Microsoft/AMLDataPrepDocs/tree/master/how-to-guides/datastore.ipynb))
    + Dodano możliwość odczytu i zapisu do udziału plików platformy Azure i magazynów danych usługi ADLS w skalowanie w górę.
    + Korzystając z magazynów danych, przygotowywanie danych obsługuje teraz przy użyciu uwierzytelniania jednostki usługi zamiast przeprowadzić uwierzytelnianie interakcyjne.
    + Dodano obsługę wasb i wasbs adresów URL.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Zestaw SDK v1.0.6 przeznaczonego do przygotowania danych usługi Azure Machine Learning

+ **Dokumentacja zestawu SDK**: https://aka.ms/data-prep-sdk

+ **Poprawki błędów**
  + Usunięto usterkę podczas odczytywania z publiczne kontenery obiektów Blob platformy Azure do odczytu, na platformie Spark

## <a name="2018-12-20"></a>2018-12-20 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v1.0.6

+ **Dokumentacja zestawu SDK**: https://aka.ms/aml-sdk

+ **Poprawki błędów**: Ta wersja zawiera przede wszystkim poprawki drobnych błędów

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Zestaw SDK v1.0.4 przeznaczonego do przygotowania danych usługi Azure Machine Learning

+ **Dokumentacja zestawu SDK**: https://aka.ms/data-prep-sdk

+ **Nowe funkcje**
  + `to_bool` Teraz dzięki funkcji niezgodne wartości są konwertowane na wartości błędów. Jest to nowe zachowanie niezgodność dla `to_bool` i `set_column_types`, podczas gdy poprzednie zachowanie domyślne można przekonwertować niezgodne wartości FALSE.
  + Podczas wywoływania `to_pandas_dataframe`, istnieje nowa opcja do interpretacji null/Brak wartości w kolumnach liczbowych jako NaN.
  + Dodano możliwość sprawdzenia, zwracany typ niektóre wyrażenia w celu zapewnienia spójności typu i wczesna Porażka.
  + Teraz można wywołać `parse_json` przeanalizować wartości w kolumnie jako obiekty JSON i rozszerzać je na wiele kolumn.

+ **Poprawki błędów**
  + Naprawiono usterkę, która uległa awarii `set_column_types` w języku Python 3.5.2.
  + Naprawiono usterkę, która uległa awarii podczas nawiązywania połączenia z magazynem danych przy użyciu obrazu AML.

+ **Aktualizacje**
  * [Przykład notesów](https://aka.ms/aml-data-prep-notebooks) w celu uzyskania pracy — samouczki, analiz przypadków i przewodniki z instrukcjami.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Ogólna dostępność

Usługa Azure Machine Learning jest teraz ogólnie dostępna.

### <a name="azure-machine-learning-compute"></a>Usługi Azure Machine Learning obliczeń
W tej wersji, Pragniemy zaprezentować nowe środowisko obliczeniowe zarządzane za pośrednictwem [obliczeniowego usługi Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute). Ten cel obliczenia zastępuje usługi Azure Batch AI zasoby obliczeniowe na potrzeby usługi Azure Machine Learning. 

Ten element docelowy obliczeniowych:
+ Służy do modelu wnioskowania szkolenie i usługi batch
+ Jest jednym — do wielu - node obliczeń
+ Obsługuje zarządzanie klastrowania i planowania zadań dla użytkownika
+ Skalowania domyślnie
+ Obsługa zasobów procesora CPU i procesora GPU 
+ Umożliwia korzystanie z maszyn wirtualnych o niskim priorytecie w obniżonej cenie

Usługa Azure obliczeniowego usługi Machine Learning można tworzyć w języku Python, za pomocą witryny Azure portal lub interfejsu wiersza polecenia. Należy utworzyć w regionie Twojego obszaru roboczego i nie można dołączyć z innym obszarem roboczym. Ten cel obliczenia używa kontenera platformy Docker na potrzeby przebieg, a pakiety zależności replikowanie tym samym środowisku na wszystkie węzły.

> [!Warning]
> Zalecamy utworzenie nowego obszaru roboczego, aby użyć obliczeniowego usługi Azure Machine Learning. Brak zdalnego prawdopodobieństwo, że w trakcie tworzenia obliczeniowego usługi Azure Machine Learning z istniejącym obszarem roboczym użytkownicy będą widzieć błąd. Istniejących zasobów obliczeniowych, w obszarze roboczym będą nadal działać bez zmian.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v1.0.2
+ **Fundamentalne zmiany**
  + W tej wersji zostaną usunięte obsługę tworzenia maszyny Wirtualnej z usługi Azure Machine Learning. Nadal możesz dołączyć istniejącej chmury maszyny Wirtualnej lub zdalnej na serwerze lokalnym. 
  + Obsługa BatchAI, które powinny być wspierane za pośrednictwem obliczeniowego usługi Azure Machine Learning teraz również zostaną usunięte.

+ **Nowy**
  + Dla potoków uczenia maszynowego:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **zaktualizowane**
  + Dla potoków uczenia maszynowego:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) teraz akceptuje runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) teraz kopiuje do i z źródła danych SQL
    + Planowanie funkcji zestawu SDK do tworzenia i aktualizowania harmonogramy uruchamiania potoków opublikowane

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Zestaw SDK v0.5.2 przeznaczonego do przygotowania danych usługi Azure Machine Learning
+ **Fundamentalne zmiany** 
  * `SummaryFunction.N` Nazwa została zmieniona na `SummaryFunction.Count`.
  
+ **Poprawki błędów**
  * Użyj najnowszej AML Uruchom tokenu podczas odczytywanie z oraz zapisywanie do magazynów danych na zdalnych przebiegów. Wcześniej Jeśli tokenu Uruchom AML zostanie zaktualizowany w języku Python, przygotowywanie danych środowiska uruchomieniowego nie zostanie zaktualizowany przy użyciu zaktualizowanych AML Uruchom tokenu.
  * Dodatkowe przejrzyste komunikaty o błędach
  * to_spark_dataframe() już ulegnie awarii, jeśli korzysta z platformy Spark `Kryo` serializacji
  * Liczba wartości Inspector umożliwia teraz wyświetlanie więcej niż 1000 unikatowe wartości
  * Losowe podziału nie jest już nie powiedzie się jeśli oryginalny przepływu danych nie ma nazwy  

+ **Więcej informacji**
  * [Zestaw SDK przygotowywania danych usługi Azure Machine Learning](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Dokumenty i notesów
+ Potokach uczenia Maszynowego
  + Nowe i zaktualizowane notesów dla klientów zaczynających się od potoki, określania zakresu usługi batch i styl transferu przykłady: https://aka.ms/aml-pipeline-notebooks
  + Dowiedz się, jak [Tworzenie pierwszego potoku](how-to-create-your-first-pipeline.md)
  + Dowiedz się, jak [Uruchom prognozy usługi batch przy użyciu potoków](how-to-run-batch-predictions.md)
+ Cel obliczenia w usłudze Azure Machine Learning
  + [Przykładowy notesów](https://aka.ms/aml-notebooks) są teraz aktualizowane do użycia nowe obliczenie zarządzanych.
  + [Dowiedz się więcej o tym obliczeń](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Witryna Azure portal: nowe funkcje
+ Tworzenie i zarządzanie nimi [obliczeniowego usługi Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) typów w portalu.
+ Monitoruj użycie przydziału i [zażądać przydziału](how-to-manage-quotas.md) Azure obliczeniowego usługi Machine Learning.
+ Wyświetl stan klastra obliczeniowego usługi Azure Machine Learning w czasie rzeczywistym.
+ Tworzenie obliczeniowego usługi Azure Machine Learning i usługi Azure Kubernetes Service została dodana obsługa sieci wirtualnej.
+ Należy ponownie uruchomić potoków opublikowanych przy użyciu istniejących parametrów.
+ Nowe [zautomatyzowane machine learning wykresy](how-to-track-experiments.md#auto) modeli klasyfikacji (przyrostu zyski, odwzorowania, funkcja znaczenie na wykresie słupkowym: explainability modelu) i modele regresji (reszty i funkcja znaczenie wykresu za pomocą modelu explainability). 
+ Potoki mogą być wyświetlane w witrynie Azure portal




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v0.1.80

+ **Fundamentalne zmiany** 
  * *azureml.Train.widgets* przestrzeń nazw została przeniesiona do *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* następujące klasy — na podstawie której zaniechane *azureml.core.compute.BatchAICompute* i *azureml.core.compute.DSVMCompute*. Ostatnie klasy zostaną usunięte w następnych wersjach. Klasa AmlCompute ma teraz łatwiej definicję po prostu potrzebuje vm_size i max_nodes i automatycznie skaluje klaster z zakresu od 0 do max_nodes po przesłaniu zadania. Nasze [przykładowy notesów](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) zostały zaktualizowane przy użyciu tych informacji i powinny zawierać przykłady użycia. Mamy nadzieję, że możesz np. to uproszczenie i wiele więcej fascynujących funkcji są dostępne w nowszej wersji!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Zestaw SDK v0.5.1 przeznaczonego do przygotowania danych usługi Azure Machine Learning 

Więcej informacji na temat zestawu SDK usługi Data Prep, czytając [odwołania docs](https://aka.ms/data-prep-sdk).
+ **Nowe funkcje**
   * Utworzony nowy interfejs wiersza polecenia przygotowania danych do wykonywania pakietów przygotowania danych i wyświetlić profil danych dla zestawu danych i przepływu danych
   * Przeprojektowana SetColumnType interfejsu API w celu zwiększenia użyteczności
   * Zmieniono nazwę smart_read_file do auto_read_file
   * Udostępnia teraz przesunięcia czasowego i kurtoza profilu danych
   * Próbkowanie z włączonym próbkowaniem stratyfikowana
   * Może odczytywać dane z plików zip, które zawierają pliki CSV
   * Można podzielić zestawów danych row-wise za pomocą losowych podziału (na przykład zestawy testów train)
   * Można uzyskać wszystkie kolumny typów danych z przepływu danych lub profil danych przez wywołanie metody `.dtypes`
   * Można pobrać liczbę wierszy z przepływu danych lub profil danych przez wywołanie metody `.row_count`

+ **Poprawki błędów**
   * Stałą Długa podwójna konwersja 
   * Naprawiono assert po dowolnej Dodaj kolumnę 
   * Rozwiązano problem z FuzzyGrouping, gdzie nie może nie wykryć grup w niektórych przypadkach
   * Funkcja stałej sortowania przestrzegają kolejność sortowania wielokolumnowych
   * Naprawiono i/lub wyrażenia są podobne do jak `pandas` je obsługuje
   * Naprawiono odczytu ze ścieżki dbfs
   * Wprowadzone bardziej zrozumiałe komunikaty o błędach 
   * Teraz już nie kończy się niepowodzeniem podczas odczytywania na zdalnym obliczeniowych elementów docelowych przy użyciu tokenu AML
   * Teraz już nie kończy się niepowodzeniem na DSVM systemu Linux
   * Teraz już ulega awarii podczas wartości innych niż ciąg znajdują się w ciągu predykatów
   * Teraz obsługuje błędy potwierdzenia, gdy przepływ danych powinna zakończyć się niepowodzeniem poprawnie
   * Obsługuje teraz lokalizacje przechowywania dbutils zainstalowanego w usłudze Azure Databricks

## <a name="2018-11-05"></a>05-11-2018 r.

### <a name="azure-portal"></a>Azure Portal 
Witryny Azure portal dla usługi Azure Machine Learning ma następujące aktualizacje:
  * Nowy **potoki** kartę opublikowanych potoków.
  * Dodano obsługę dołączania do istniejącego klastra HDInsight, jako cel obliczenia.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v0.1.74

+ **Fundamentalne zmiany** 
  * * Workspace.compute_targets, magazynów danych, eksperymentów, obrazy, modele i *usług sieci Web* właściwości zamiast metod. Na przykład Zastąp ciąg *Workspace.compute_targets()* z *Workspace.compute_targets*.
  * *Run.get_context* traktuje jako przestarzałą *Run.get_submitted_run*. Druga metoda zostaną usunięte w następnych wersjach.
  * *PipelineData* klasy teraz oczekuje obiektu magazynu danych jako parametr zamiast datastore_name. Podobnie *potoku* akceptuje default_datastore zamiast default_datastore_name.

+ **Nowe funkcje**
  * Potoków uczenia maszynowego Azure [przykładowy notes](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) używa teraz kroki MPI.
  * Element widget RunDetails dla notesów programu Jupyter jest aktualizowana w celu wyświetlenia wizualizacja potoku.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Zestaw SDK v0.4.0 przeznaczonego do przygotowania danych usługi Azure Machine Learning 
 
+ **Nowe funkcje**
  * Liczba typów dodawane do profilu danych 
  * Liczba wartości i histogramu jest teraz dostępna
  * Więcej percentyle w profilu danych
  * Mediana jest dostępna w podsumowanie
  * Python 3.7 jest teraz obsługiwane.
  * Po zapisaniu przepływu danych, który zawiera magazynów danych na pakiet przygotowania danych jako część pakietu przygotowania danych zostaną utrwalone dane magazynu danych
  * Zapisywanie do magazynu danych jest teraz obsługiwane. 
        
+ **Naprawiono usterkę**
  * 64-bitowej nieoznaczonej liczby całkowitej przepełnienia są teraz obsługiwane poprawnie w systemie Linux
  * Stały tekst Nieprawidłowa etykieta plikami ze zwykłym tekstem w smart_read
  * Typ kolumny ciągu teraz zostaną wyświetlone w widoku metryk
  * Liczba typów jest rozwiązano problem, aby pokazać ValueKinds mapowany do jednego typu pola, a nie pojedyncze pliki
  * Write_to_csv już nie kończy się niepowodzeniem podczas ścieżka jest podana jako ciąg
  * Korzystając z Zastąp, pozostawiając "wyszukiwanie" puste nie są już nie powiedzie się 

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v0.1.68

+ **Nowe funkcje**
  * Obsługa wielu dzierżaw, podczas tworzenia nowego obszaru roboczego.

+ **Usterki usunięte**
  * Nie trzeba przypiąć wersji biblioteki pynacl podczas wdrażania usługi sieci web.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Zestaw SDK v0.3.0 przeznaczonego do przygotowania danych usługi Azure Machine Learning

+ **Nowe funkcje**
  * Dodano transform_partition_with_file(script_path) metody, która umożliwia użytkownikom na przekazywanie, ścieżkę pliku Python do wykonywania

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v0.1.65
[Wersja 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) zawiera nowe funkcje, więcej dokumentacji, poprawki i [przykładowy notesów](https://aka.ms/aml-notebooks).

Zobacz [listę znanych problemów](resource-known-issues.md) informacje na temat znanych błędów i rozwiązania problemu.

+ **Fundamentalne zmiany**
  * Workspace.experiments, Workspace.models, Workspace.compute_targets Workspace.images Workspace.web_services słownika zwrotu, poprzednio zwróconej liście. Zobacz [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) dokumentacji interfejsu API.

  * Automatyczne usługi Machine Learning usunąć błąd znormalizowane średniej kwadratowej z podstawowe metryki.

+ **HyperDrive**
  * Poprawki błędów różnych HyperDrive Bayesowskie, ulepszenia wydajności dotyczące pobrania wywołania metryki. 
  * Uaktualnienie Tensorflow 1.10 1.9 
  * Optymalizacja obrazów platformy docker do zimnego. 
  * Zadania teraz raport prawidłowy stan, nawet wtedy, gdy ich zakończyć pracę z kodem błędu niż 0. 
  * Sprawdzanie poprawności atrybutów RunConfig w zestawie SDK. 
  * Obiekt HyperDrive Uruchom obsługuje anulowanie podobne do regularnego wykonywania: nie trzeba przekazywać żadnych parametrów. 
  * Widżet ulepszenia dotyczące utrzymania stan z listy rozwijanej wartości dla rozproszonej przebiegów oraz uruchomień HyperDrive. 
  * Narzędzia TensorBoard i innych dziennik, który obsługuje pliki Naprawiono parametr serwera. 
  * Obsługa MPI Intel(R) po stronie usługi. 
  * Poprawka do szczegółowego dostosowywania parametrów dla rozproszonego wykonywania awarii podczas sprawdzania poprawności w BatchAI. 
  * Kontekst Manager identyfikuje teraz podstawowe wystąpienie. 

+ **Środowisku witryny Azure portal**
  * log_table() i log_row() są obsługiwane w szczegóły przebiegu. 
  * Automatycznie tworzyć wykresy, tabele i wiersze, 1, 2 lub 3 kolumny liczbowe i opcjonalna kolumna podzielonych na kategorie.

+ **Automatyczne Machine Learning**
  * Ulepszona obsługa błędów i dokumentacji 
  * Naprawiono pobieranie właściwości uruchamiania problemy z wydajnością. 
  * Naprawiono kontynuować wykonywania problem. 
  * Rozwiązano ensembling problemy iteracji.
  * Naprawiono szkolenia zwisa błędów w systemie MAC OS.
  * Próbkowanie — makro średni żądania Ściągnięcia/WIELOKLASOWA krzywa w scenariuszu niestandardowego sprawdzania poprawności.
  * Usunięty indeks dodatkowej logiki.
  * Usunąć filtr z get_output interfejsu API.

+ **Potoki**
  * Dodaje metody Pipeline.publish() publikowanie potoku bezpośrednio, bez konieczności wykonywania są uruchamiane jako pierwsze.   
  * Dodaje metody PipelineRun.get_pipeline_runs() można pobrać uruchomień potoków, które zostały wygenerowane z opublikowanych potoku.

+ **Project Brainwave**
  * Zaktualizowano obsługę na układów FPGA nowe modele sztucznej Inteligencji.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Zestaw SDK v0.2.0 przeznaczonego do przygotowania danych usługi Azure Machine Learning
[Wersja 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) obejmuje następujące funkcje i poprawki:

+ **Nowe funkcje**
  * Obsługa hot jeden kodowania
  * Obsługa przekształcania kwantyl
   
+ **Usterka naprawiona:**
  * Działa z dowolną wersją Tornado nie ma potrzeby obniżyć wersji Tornado
  * Liczba wartości dla wszystkich wartości nie tylko trzy pierwsze

## <a name="2018-09-public-preview-refresh"></a>2018-09 (w wyniku odświeżenia publiczna wersja zapoznawcza)

Nowy, odświeżana wersji usługi Azure Machine Learning: Dowiedz się więcej na temat tej wersji: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z omówieniem dla [usługi Azure Machine Learning](../service/overview-what-is-azure-ml.md).
