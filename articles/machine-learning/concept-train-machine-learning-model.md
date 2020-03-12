---
title: Metody szkoleń modelu
titleSuffix: Azure Machine Learning
description: Poznaj różne metody, których można użyć do uczenia modelu z Azure Machine Learning. Szacowania zapewnia łatwy sposób pracy z popularnymi strukturami, takimi jak Scikit — uczenie się, TensorFlow, Keras, PyTorch i łańcucha. Potoki Machine Learning ułatwiają planowanie nienadzorowanych uruchomień, używanie heterogenicznych środowisk obliczeniowych i ponowne używanie części przepływu pracy. I uruchamiania konfiguracje zapewniają szczegółową kontrolę nad celami obliczeniowymi, na których działa proces szkoleniowy.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 68ad9cc47d68f7bc3ae952f7e458781cdc1c4ab2
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129769"
---
# <a name="train-models-with-azure-machine-learning"></a>Szkolenie modeli za pomocą usługi Azure Machine Learning

Azure Machine Learning oferuje kilka sposobów uczenia modeli — od pierwszego rozwiązania kodu przy użyciu zestawu SDK do małych rozwiązań kodu, takich jak automatyczne Uczenie maszynowe i projektant wizualny. Skorzystaj z poniższej listy, aby określić, która metoda szkoleniowa jest dla Ciebie odpowiednia:

+ [Azure Machine Learning SDK dla języka Python](#python-sdk): zestaw SDK języka Python oferuje kilka sposobów uczenia modeli, z których każdy może mieć różne możliwości.

    | Metoda uczenia | Opis |
    | ----- | ----- |
    | [Uruchom konfigurację](#run-configuration) | **Ogólny sposób uczenia modeli** polega na użyciu skryptu szkoleniowego i uruchomienia konfiguracji. Konfiguracja uruchamiania zawiera informacje konieczne do skonfigurowania środowiska szkoleniowego używanego do uczenia modelu. Możesz utworzyć konfigurację uruchamiania, skrypt szkoleniowy i cel obliczeń (środowisko szkoleniowe) i uruchomić zadanie szkoleniowe. |
    | [Automatyczne Uczenie maszynowe](#automated-machine-learning) | Automatyczne Uczenie maszynowe umożliwia **uczenie modeli bez obszernej wiedzy o nauce lub programowaniu**. W przypadku osób z tłem i programowaniem danych zapewnia ona sposób oszczędzania czasu i zasobów dzięki automatyzowaniu wyboru algorytmu i dostrajania parametrów. Nie musisz martwić się o Definiowanie konfiguracji uruchamiania podczas korzystania z funkcji automatycznego uczenia maszynowego. |
    | [Szacowania](#estimators) | Klasy szacowania ułatwiają **uczenie modeli opartych na popularnych strukturach uczenia maszynowego**. Istnieją klasy szacowania dla **Scikit-uczenia**, **PyTorch**, **TensorFlow**i **łańcucha**. Istnieje również ogólny szacowania, który może być używany z platformami, które nie mają jeszcze dedykowanej klasy szacowania. Nie musisz martwić się o Definiowanie konfiguracji uruchamiania podczas korzystania z szacowania. |
    | [Potok uczenia maszynowego](#machine-learning-pipeline) | Potoki nie są różnymi metodami szkoleniowymi, ale **sposób definiowania przepływu pracy przy użyciu modularnych, wielokrotnych kroków**, które mogą obejmować szkolenie w ramach przepływu pracy. Potoki usługi Machine Learning obsługują korzystanie z funkcji automatycznego uczenia maszynowego, szacowania i uruchamiania konfiguracji w celu uczenia modeli. Ponieważ potoki nie są ukierunkowane na szkolenie, przyczyny użycia potoku są bardziej zróżnicowane niż inne metody uczenia się. Ogólnie rzecz biorąc, można użyć potoku, gdy:<br>* Chcesz **zaplanować procesy nienadzorowane** , takie jak długotrwałe zadania szkoleniowe lub Przygotowywanie danych.<br>* Należy użyć **wielu kroków** , które są skoordynowane w niejednorodnych zasobach obliczeniowych i lokalizacjach magazynu.<br>* Użyj potoku jako **szablonu wielokrotnego użytku** dla konkretnych scenariuszy, takich jak przeszkolenie lub wsadowe ocenianie.<br>* **śledzenie i wersje źródeł danych, wejść i wyjść** dla przepływu pracy.<br>* Przepływ pracy jest **implementowany przez różne zespoły, które pracują niezależnie od konkretnych kroków**. Kroki można następnie połączyć w potoku w celu zaimplementowania przepływu pracy. |

+ [Azure Machine Learning SDK dla języka Python](#r-sdk): zestaw SDK używa pakietu reticulate do utworzenia powiązania z zestawem SDK języka Python Azure Machine Learning. Dzięki temu można uzyskać dostęp do podstawowych obiektów i metod zaimplementowanych w zestawie Python SDK z dowolnego środowiska języka R.

+ **Projektant**: Program Azure Machine Learning Designer (wersja zapoznawcza) udostępnia łatwą funkcję uczenia maszynowego na potrzeby tworzenia wyników weryfikacji koncepcji lub dla użytkowników z niewielkim doświadczeniem związanym z kodowaniem. Pozwala to na uczenie modeli przy użyciu funkcji przeciągania i upuszczania interfejsu użytkownika opartego na sieci Web. Możesz użyć kodu w języku Python jako części projektu lub pouczenia modeli bez pisania kodu.

+ **CLI**: interfejs wiersza polecenia usługi Machine Learning umożliwia wykonywanie typowych zadań przy użyciu Azure Machine Learning i jest często używany do **tworzenia skryptów i automatyzowania zadań**. Na przykład po utworzeniu skryptu szkoleniowego lub potoku można użyć interfejsu wiersza polecenia, aby rozpocząć szkolenie zgodnie z harmonogramem lub w przypadku aktualizowania plików danych używanych do szkoleń. W przypadku modeli szkoleniowych dostępne są polecenia, które przesyłają zadania szkoleniowe. Może przesyłać zadania przy użyciu konfiguracji uruchamiania lub potoków.

Każda z tych metod szkoleniowych może używać różnych typów zasobów obliczeniowych do szkoleń. Zbiorowo te zasoby są określane jako [__cele obliczeniowe__](concept-azure-machine-learning-architecture.md#compute-targets). Obiekt docelowy obliczeń może być maszyną lokalną lub zasobem w chmurze, takim jak Azure Machine Learning COMPUTE, Azure HDInsight lub zdalną maszynę wirtualną.

## <a name="python-sdk"></a>Zestaw SDK dla języka Python

Zestaw Azure Machine Learning SDK dla języka Python umożliwia tworzenie i uruchamianie przepływów pracy uczenia maszynowego przy użyciu Azure Machine Learning. Można korzystać z usługi z interaktywnej sesji języka Python, Jupyter notesów, Visual Studio Code lub innego środowiska IDE.

* [Co to jest zestaw Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Instalowanie/Aktualizowanie zestawu SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Skonfiguruj środowisko programistyczne dla Azure Machine Learning](how-to-configure-environment.md)

### <a name="run-configuration"></a>Uruchom konfigurację

Ogólne zadanie szkoleniowe z Azure Machine Learning można zdefiniować za pomocą [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py). Następnie zostanie użyta Konfiguracja przebiegu wraz ze skryptami szkoleniowymi do uczenia modelu w obiekcie docelowym obliczeń.

Możesz rozpocząć od konfiguracji uruchamiania komputera lokalnego, a następnie w razie potrzeby przełączyć się na jeden dla docelowej wartości obliczeniowej opartej na chmurze. Podczas zmieniania elementu docelowego obliczeń można zmienić tylko używaną konfigurację uruchamiania. Uruchomienie rejestruje również informacje o zadaniu szkoleniowym, takie jak dane wejściowe, wyjściowe i dzienniki.

* [Co to jest Konfiguracja przebiegu?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Samouczek: uczenie swojego pierwszego modelu ML](tutorial-1st-experiment-sdk-train.md)
* [Przykłady: Jupyter Notebook Przykładowe modele szkoleniowe](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [Instrukcje: Konfigurowanie celów obliczeniowych i używanie ich do uczenia modelu](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Zautomatyzowane uczenie maszynowe

Zdefiniuj iteracje, ustawienia parametrów, cechowania i inne ustawienia. Podczas uczenia Azure Machine Learning próbuje jednocześnie różne algorytmy i parametry. Szkolenie zostaje zatrzymane po trafieniu zdefiniowanych kryteriów zakończenia. Nie musisz martwić się o Definiowanie konfiguracji uruchamiania podczas korzystania z szacowania.

> [!TIP]
> Oprócz zestawu SDK języka Python można także użyć zautomatyzowanej ML za pomocą [Azure Machine Learning Studio](https://ml.azure.com).

* [Co to jest zautomatyzowane uczenie maszynowe?](concept-automated-ml.md)
* [Samouczek: Tworzenie pierwszego modelu klasyfikacji przy użyciu automatycznej uczenia maszynowego](tutorial-first-experiment-automated-ml.md)
* [Samouczek: Używanie automatycznego uczenia maszynowego do przewidywania opłat za taksówkę](tutorial-auto-train-models.md)
* [Przykłady: Jupyter Notebook przykłady dla automatycznej uczenia maszynowego](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Instrukcje: Konfigurowanie zautomatyzowanych eksperymentów ML w języku Python](how-to-configure-auto-train.md)
* [Instrukcje: autouczenie modelu prognozy szeregów czasowych](how-to-auto-train-forecast.md)
* [Instrukcje: Tworzenie, eksplorowanie i wdrażanie zautomatyzowanych eksperymentów uczenia maszynowego za pomocą programu Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md)

### <a name="estimators"></a>Szacowania

Szacowania ułatwiają uczenie modeli przy użyciu popularnych platform ML. Jeśli używasz **Scikit-uczenia**się, **PyTorch**, **TensorFlow**lub **łańcucha**, należy rozważyć użycie szacowania do szkolenia. Istnieje również ogólny szacowania, który może być używany z platformami, które nie mają jeszcze dedykowanej klasy szacowania. Nie musisz martwić się o Definiowanie konfiguracji uruchamiania podczas korzystania z szacowania.

* [Co to są szacowania?](concept-azure-machine-learning-architecture.md#estimators)
* [Samouczek: uczenie modeli klasyfikacji obrazów przy użyciu MNIST ręcznie danych i scikit — uczenie się przy użyciu Azure Machine Learning](tutorial-train-models-with-aml.md)
* [Przykłady: Jupyter Notebook przykłady użycia szacowania](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [Instrukcje: Tworzenie szacowania w szkoleniu](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>Potok uczenia maszynowego

Potoki uczenia maszynowego mogą korzystać z wymienionych wcześniej metod szkoleniowych (uruchamiania konfiguracji, szacowania i automatycznej uczenia maszynowego). Potoki zawierają więcej informacji na temat tworzenia przepływu pracy, co obejmuje więcej niż tylko szkolenia modeli. W potoku można nauczyć model przy użyciu zautomatyzowanej uczenia maszynowego, szacowania lub uruchamiania konfiguracji.

* [Co to są potoki ML w Azure Machine Learning?](concept-ml-pipelines.md)
* [Tworzenie i uruchamianie potoków uczenia maszynowego za pomocą zestawu SDK Azure Machine Learning](how-to-create-your-first-pipeline.md)
* [Samouczek: korzystanie z potoków Azure Machine Learning na potrzeby oceniania partii](tutorial-pipeline-batch-scoring-classification.md)
* [Przykłady: Jupyter Notebook przykłady potoków uczenia maszynowego](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Przykłady: potok z automatyczną usługą uczenia maszynowego](https://aka.ms/pl-automl)
* [Przykłady: potok z szacowania](https://aka.ms/pl-estimator)

## <a name="r-sdk"></a>Zestaw SDK języka R

Zestaw R SDK umożliwia korzystanie z języka R z Azure Machine Learning. Zestaw SDK używa pakietu reticulate do powiązania z zestawem Python SDK Azure Machine Learning. Dzięki temu można uzyskać dostęp do podstawowych obiektów i metod zaimplementowanych w zestawie Python SDK z dowolnego środowiska języka R.

Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Samouczek: Tworzenie modelu regresji logistycznej](tutorial-1st-r-experiment.md)
* [Dokumentacja zestawu Azure Machine Learning SDK dla języka R](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Projektant Azure Machine Learning

Projektant umożliwia uczenie modeli przy użyciu interfejsu przeciągania i upuszczania w przeglądarce internetowej.

+ [Co to jest projektant?](concept-designer.md)
+ [Samouczek: przewidywanie ceny samochodów](tutorial-designer-automobile-price-train-score.md)
+ [Regresja: cena predykcyjna](how-to-designer-sample-regression-automobile-price-basic.md)
+ [Klasyfikacja: przewidywanie przychodu](how-to-designer-sample-classification-predict-income.md)
+ [Klasyfikacja: przewidywanie zmian, pragnienie i kupowanie](how-to-designer-sample-classification-churn.md)
+ [Klasyfikacja przy użyciu niestandardowego skryptu języka R: przewidywanie opóźnień lotów](how-to-designer-sample-classification-flight-delay.md)
+ [Klasyfikacja tekstu: zestaw danych witryny Wikipedia SP 500](how-to-designer-sample-text-classification.md)

## <a name="cli"></a>Interfejs wiersza polecenia

Interfejs wiersza polecenia uczenia maszynowego to rozszerzenie interfejsu wiersza polecenia platformy Azure. Udostępnia on Międzyplatformowe polecenia interfejsu CLI do pracy z Azure Machine Learning. Zazwyczaj używasz interfejsu wiersza polecenia do automatyzowania zadań, takich jak uczenie modelu uczenia maszynowego.

* [Użyj rozszerzenia interfejsu wiersza polecenia dla Azure Machine Learning](reference-azure-machine-learning-cli.md)
* [MLOps na platformie Azure](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [skonfigurować środowiska szkoleniowe](how-to-set-up-training-targets.md).
