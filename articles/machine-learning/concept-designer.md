---
title: Tworzenie modeli ML przy użyciu narzędzia Projektant
titleSuffix: Azure Machine Learning
description: Poznaj warunki, koncepcje i przepływ pracy, które tworzą projektanta dla Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 11/12/2019
ms.openlocfilehash: 8208bbf4d196091a6fe4cd962ddc7373d303e125
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76312434"
---
# <a name="what-is-azure-machine-learning-designer"></a>Co to jest Azure Machine Learning Designer? 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Projektant Azure Machine Learning umożliwia wizualne łączenie [zestawów danych](#datasets) i [modułów](#module) na interaktywnej kanwie w celu tworzenia modeli uczenia maszynowego. Aby dowiedzieć się, jak rozpocząć pracę z projektantem, zobacz [Samouczek: przewidywanie ceny za samochód dla urządzeń przenośnych za pomocą projektanta](tutorial-designer-automobile-price-train-score.md)

![Przykład projektanta Azure Machine Learning](./media/concept-designer/designer-drag-and-drop.gif)

Projektant używa [obszaru roboczego](concept-workspace.md) Azure Machine Learning do organizowania zasobów udostępnionych, takich jak:

+ [Potoki](#pipeline)
+ [Zestawy danych](#datasets)
+ [Zasoby obliczeniowe](#compute)
+ [Zarejestrowane modele](concept-azure-machine-learning-architecture.md#models)
+ [Opublikowane potoki](#publish)
+ [Punkty końcowe w czasie rzeczywistym](#deploy)

## <a name="model-training-and-deployment"></a>Modelowanie szkoleń i wdrożeń

Projektant umożliwia wizualizację kanwy do kompilowania, testowania i wdrażania modeli uczenia maszynowego. Za pomocą projektanta można:

+ Przeciągnij i upuść [zestawy danych](#datasets) i [moduły](#module) na kanwę.
+ Połącz moduły ze sobą, aby utworzyć [wersję roboczą potoku](#pipeline-draft).
+ Prześlij [przebieg potoku](#pipeline-run) przy użyciu zasobów obliczeniowych w obszarze roboczym Azure Machine Learning.
+ Przekonwertuj **potoki szkoleniowe** na **potoki wnioskowania**.
+ [Opublikuj](#publish) potoki w **punkcie końcowym potoku** REST, aby przesłać nowe uruchomienia potoku z innymi parametrami i zestawami danych.
    + Publikuj **potok szkoleniowy** , aby ponownie wykorzystać pojedynczy potok do uczenia wielu modeli przy zmianie parametrów i zestawów danych.
    + Publikuj **potok wnioskowania o partiach** , aby przetworzyć prognozy dotyczące nowych danych przy użyciu wcześniej nauczonego modelu.
+ [Wdróż](#deploy) **potok wnioskowania** w czasie rzeczywistym do punktu końcowego w czasie rzeczywistym, aby przetworzyć prognozy dotyczące nowych danych w czasie rzeczywistym.

![Diagram przepływu pracy na potrzeby szkoleń, wnioskowania partii i wnioskowania w czasie rzeczywistym w projektancie](./media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>Potok

[Potok](concept-azure-machine-learning-architecture.md#ml-pipelines) składa się z zestawów danych i modułów analitycznych, które łączą się ze sobą. Potoki mają wiele użycia: można utworzyć potok, który pociąga za sobą jeden model, lub jeden z wielu modeli. Można utworzyć potok, który dokonuje prognoz w czasie rzeczywistym lub w partii, lub utworzyć potok, który czyści dane. Potoki umożliwiają ponowne użycie pracy i zorganizowanie projektów.

### <a name="pipeline-draft"></a>Wersja robocza potoku

Podczas edytowania potoku w projektancie postęp jest zapisywany jako **wersja robocza potoku**. Można edytować wersję roboczą potoku w dowolnym momencie przez dodanie lub usunięcie modułów, skonfigurowanie obiektów docelowych obliczeń, utworzenie parametrów itd.

Prawidłowy potok ma następujące cechy:

* Zestawy danych mogą łączyć się tylko z modułami.
* Moduły mogą łączyć się tylko z zestawami danych lub innymi modułami.
* Wszystkie porty wejściowe dla modułów muszą mieć połączenie z przepływem danych.
* Należy ustawić wszystkie wymagane parametry dla każdego modułu.

Gdy wszystko będzie gotowe do uruchomienia potoku, przesyłasz uruchomienie potoku.

### <a name="pipeline-run"></a>Uruchomienie potoku

Za każdym razem, gdy uruchamiasz potok, konfiguracja potoku i jego wyniki są przechowywane w obszarze roboczym jako **uruchomienie potoku**. Można wrócić do dowolnego uruchomienia potoku, aby sprawdzić go pod kątem rozwiązywania problemów lub inspekcji. **Sklonuj** uruchomienie potoku, aby utworzyć nową wersję roboczą potoku do edycji.

Uruchomienia potoków są pogrupowane w [eksperymenty](concept-azure-machine-learning-architecture.md#experiments) w celu zorganizowania historii uruchamiania. Możesz ustawić eksperyment dla każdego uruchomienia potoku. 

## <a name="datasets"></a>Zestawy danych

Zestaw danych uczenia maszynowego ułatwia uzyskiwanie dostępu do danych i korzystanie z nich. W projektancie zawarto kilka przykładowych zestawów danych, za pomocą których można eksperymentować. W razie potrzeby możesz [zarejestrować](how-to-create-register-datasets.md) więcej zestawów danych.

## <a name="module"></a>Moduł

Moduł jest algorytmem, który można wykonać na danych. Projektant ma wiele modułów niż funkcje transferu danych przychodzących do szkoleń, oceniania i procesów walidacji.

Moduł może zawierać zestaw parametrów, za pomocą których można konfigurować wewnętrzne algorytmy modułu. Po wybraniu modułu na kanwie Parametry modułu są wyświetlane w okienku właściwości po prawej stronie kanwy. Te parametry można modyfikować w okienku, aby dostosowywać model. Można ustawić zasoby obliczeniowe dla poszczególnych modułów w projektancie. 

![Właściwości modułu](./media/concept-designer/properties.png)

Aby uzyskać pomoc dotyczącą przechodzenia przez bibliotekę dostępnych algorytmów uczenia maszynowego, zobacz [algorytm & — informacje o module](algorithm-module-reference/module-reference.md)

## <a name="compute"></a>Zasoby obliczeniowe

Użyj zasobów obliczeniowych z obszaru roboczego, aby uruchomić potok i hostować wdrożone modele jako punkty końcowe w czasie rzeczywistym lub punkty końcowe potoku (na potrzeby wnioskowania wsadowego). Są obsługiwane obliczeniowych elementów docelowych:

| Docelowy zasób obliczeniowy | Szkolenie | Wdrażanie |
| ---- |:----:|:----:|
| Obliczeniowe platformy Azure Machine Learning | ✓ | |
| Azure Kubernetes Service | | ✓ |

Elementy docelowe obliczeń są dołączone do [obszaru roboczego Azure Machine Learning](concept-workspace.md). Obiektami docelowymi obliczeń można zarządzać w obszarze roboczym w [Azure Machine Learning Studio (klasyczny)](https://ml.azure.com).

## <a name="deploy"></a>Implementacja

Aby wykonać inferencing w czasie rzeczywistym, należy wdrożyć potok jako **punkt końcowy w czasie rzeczywistym**. Punkt końcowy w czasie rzeczywistym tworzy interfejs między aplikacją zewnętrzną a modelem oceniania. Wywołanie punktu końcowego w czasie rzeczywistym zwraca wyniki prognozowania do aplikacji w czasie rzeczywistym. Aby wykonać wywołanie do punktu końcowego w czasie rzeczywistym, należy przekazać klucz interfejsu API, który został utworzony podczas wdrażania punktu końcowego. Punkt końcowy jest oparty na pozostałej, popularnej architekturze dla projektów programowania w sieci Web.

Punkty końcowe w czasie rzeczywistym muszą zostać wdrożone w klastrze usługi Azure Kubernetes Service.

Aby dowiedzieć się, jak wdrożyć model, zobacz [Samouczek: Wdrażanie modelu uczenia maszynowego za pomocą projektanta](tutorial-designer-automobile-price-deploy.md).

## <a name="publish"></a>Publikuj

Możesz również opublikować potok w **punkcie końcowym potoku**. Podobnie jak w przypadku punktu końcowego w czasie rzeczywistym, punkt końcowy potoku umożliwia przesyłanie nowych uruchomień potoków z aplikacji zewnętrznych przy użyciu wywołań REST. Nie można jednak wysyłać ani odbierać danych w czasie rzeczywistym za pomocą punktu końcowego potoku.

Opublikowane potoki są elastyczne, ale mogą być używane do uczenia lub ponownego uczenia modeli, [wykonywania zadań wsadowych inferencing](how-to-run-batch-predictions-designer.md), przetwarzania nowych danych i wiele innych. Można opublikować wiele potoków w jednym punkcie końcowym potoku i określić, która wersja potoku ma zostać uruchomiona.

Opublikowany potok jest uruchamiany na zasobach obliczeniowych zdefiniowanych w wersji roboczej potoku dla każdego modułu.

Projektant tworzy ten sam obiekt [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) , co zestaw SDK.


## <a name="moving-from-the-visual-interface-to-the-designer"></a>Przechodzenie z interfejsu wizualizacji do projektanta

Interfejs wizualny (wersja zapoznawcza) został zaktualizowany i teraz Azure Machine Learning projektanta. Projektant został przeprojektowany tak, aby korzystał z zaplecza opartego na potoku, który jest w pełni zintegrowany z innymi funkcjami Azure Machine Learning. 

W wyniku tych aktualizacji niektóre koncepcje i terminy dla interfejsu wizualizacji zostały zmienione lub zmieniono ich nazwy. Zapoznaj się z poniższą tabelą, aby poznać najważniejsze zmiany pojęciowe. 

| Koncepcja projektanta | Wcześniej w interfejsie wizualizacji |
| ---- |:----:|
| Wersja robocza potoku | Experiment |
| Punkt końcowy w czasie rzeczywistym | Usługa sieci Web |

### <a name="migrating-to-the-designer"></a>Migrowanie do projektanta

Istniejące eksperymenty interfejsu wizualizacji i usługi sieci Web można przekonwertować na potoki i punkty końcowe w czasie rzeczywistym w projektancie. Wykonaj następujące kroki, aby przeprowadzić migrację zasobów interfejsu wizualizacji:

[!INCLUDE [migrate from the visual interface](../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>Następne kroki

* Poznaj podstawy analizy predykcyjnej i uczenia maszynowego za pomocą [samouczka: przewidywanie ceny za samochód z użyciem projektanta](tutorial-designer-automobile-price-train-score.md)
* Użyj jednego z przykładów i zmodyfikuj, aby wymusić Twoje potrzeby:

- [Przykład 1-regresja: przewidywanie ceny za samochód](how-to-designer-sample-regression-automobile-price-basic.md)
- [Przykład 2-Regresja: porównanie algorytmów do prognozowania cen samochodów](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Przykład 3 — Klasyfikacja z wyborem funkcji: prognozowanie dochodu](how-to-designer-sample-classification-predict-income.md)
- [Przykład 4 — Klasyfikacja: przewidywanie ryzyka kredytowego (z uwzględnieniem kosztów)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Przykład 5 — Klasyfikacja: przewidywalność zmian](how-to-designer-sample-classification-churn.md)
- [Przykład 6 — Klasyfikacja: przewidywanie opóźnień lotów](how-to-designer-sample-classification-flight-delay.md)
- [Przykład 7 — Klasyfikacja tekstu: zestaw danych witryny Wikipedia SP 500](how-to-designer-sample-text-classification.md)

