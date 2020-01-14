---
title: Co to jest Azure Machine Learning
description: Omówienie Azure Machine Learning zintegrowanego, kompleksowego rozwiązania do nauki o danych dla specjalistów ds. danych w celu opracowywania, eksperymentowania i wdrażania zaawansowanych aplikacji analitycznych na skalę chmury.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 11/04/2019
ms.openlocfilehash: cd94001cfe795e7aa78a1332325052afce6b8ec6
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75762811"
---
# <a name="what-is-azure-machine-learning"></a>Co to jest usługa Azure Machine Learning?

Ten artykuł zawiera informacje o Azure Machine Learning, środowisku opartym na chmurze, które służy do uczenia, wdrażania, automatyzowania i śledzenia modeli ML oraz zarządzania nimi. 

Azure Machine Learning mogą być używane dla dowolnego rodzaju uczenia maszynowego, od klasycznej maszyny do uczenia głębokiego, nadzorowanego i nienadzorowanego uczenia się. Bez względu na to, czy wolisz pisać kod w języku Python lub R, czy też nie ma opcji o kodzie bez kodu, takich jak [Projektant](tutorial-designer-automobile-price-train-score.md), możesz tworzyć, uczeni i śledzić wysoce dokładne modele uczenia maszynowego i uczenia się w obszar roboczy usługi Azure Machine Learning. 

Rozpocznij szkolenie na komputerze lokalnym, a następnie Skaluj w poziomie do chmury. 

Usługa współpracuje również z popularnymi narzędziami typu "open source", takimi jak PyTorch, TensorFlow i scikit — uczenie się.

> [!VIDEO https://channel9.msdn.com/Events/Connect/Microsoft-Connect--2018/D240/player]

> [!Tip]
> **Bezpłatna wersja próbna!**  Jeśli nie masz subskrypcji na platformie Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj. Możesz uzyskać środki do wydania na usługi platformy Azure. Nawet po ich wyczerpaniu możesz zachować konto i korzystać z [bezpłatnych usług platformy Azure](https://azure.microsoft.com/free/). Karta kredytowa nie zostanie obciążona, chyba że jawnie zmienisz ustawienia i poprosisz o jej obciążenie.


## <a name="what-is-machine-learning"></a>Co to jest uczenie maszynowe?

Uczenie maszynowe to technika przetwarzania danych, która umożliwia wykorzystanie przez komputery istniejących danych w celu przewidywania przyszłych zachowań, rezultatów i trendów. Przy użyciu techniki uczenia maszynowego komputery uczą się bez ich jawnego programowania.

Dzięki prognozom lub przewidywaniom uzyskiwanym za pomocą uczenia maszynowego aplikacje i urządzenia są bardziej inteligentne. Na przykład podczas zakupów w Internecie uczenie maszynowe wspomaga proces rekomendowania innych produktów, którymi kupujący może być zainteresowany, na podstawie jego dotychczasowych zakupów. W przypadku płacenia kartą kredytową uczenie maszynowe porównuje transakcję z bazą danych transakcji i ułatwia wykrycie oszustwa. Gdy robot odkurzający sprząta pomieszczenie, uczenie maszynowe pomaga mu zdecydować, czy praca została wykonana.

## <a name="machine-learning-tools-to-fit-each-task"></a>Narzędzia uczenia maszynowego do dopasowania poszczególnych zadań 

Azure Machine Learning udostępnia wszystkie narzędzia dla deweloperów i analityków danych, które są potrzebne dla swoich przepływów pracy uczenia maszynowego, w tym:
+ [Projektant Azure Machine Learning](tutorial-designer-automobile-price-train-score.md) (wersja zapoznawcza): Przeciągnij i upuść moduły, aby skompilować eksperymenty, a następnie wdrożyć potoki.

+ Notesy Jupyter: Użyj naszych [przykładowych notesów](https://aka.ms/aml-notebooks) lub Utwórz własne notesy, aby korzystać z naszego <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">zestawu SDK dla</a> przykładów języka Python dla uczenia maszynowego. 

+ Skrypty i notesy języka r, w których używasz <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">zestawu SDK dla języka r</a> , do pisania własnego kodu lub korzystania z modułów r w projektancie.

+ [Visual Studio Code rozszerzenie](how-to-vscode-tools.md)

+ [Interfejs wiersza polecenia uczenia maszynowego](reference-azure-machine-learning-cli.md)

+ Platformy typu open source, takie jak PyTorch, TensorFlow i scikit — uczyć się i wielu innych

Możesz nawet używać [MLflow do śledzenia metryk i wdrażania modeli](how-to-use-mlflow.md) lub Kubeflow, aby [tworzyć kompleksowe potoki przepływu pracy](https://www.kubeflow.org/docs/azure/).

## <a name="build-ml-models-in-python-or-r"></a>Tworzenie modeli ML w języku Python lub R

Rozpocznij szkolenie na maszynie lokalnej przy użyciu <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">zestawu SDK</a> języka Azure Machine Learning Python lub <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">zestawu R SDK</a>. Następnie można skalować w poziomie do chmury. 

Dzięki wielu dostępnym [docelowym obiektom obliczeniowym](how-to-set-up-training-targets.md), takim jak środowisko obliczeniowe usługi Azure Machine Learning i [usługa Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), oraz [zaawansowanym usługom dostrajania hiperparametrów](how-to-tune-hyperparameters.md), można tworzyć lepsze modele szybciej, wykorzystując możliwości chmury.

Możesz również [zautomatyzować szkolenia i dostrajanie modeli](tutorial-auto-train-models.md) za pomocą zestawu SDK.

## <a name="build-ml-models-with-no-code-tools"></a>Tworzenie modeli ML przy użyciu narzędzi bez kodu

W przypadku szkolenia i wdrożenia z niską ilością kodu, wypróbuj następujące rozwiązania:

+ **Projektant Azure Machine Learning (wersja zapoznawcza)**

  Za pomocą projektanta można przygotowywać dane, uczenie, testować, wdrażać i śledzić modele uczenia maszynowego bez konieczności pisania kodu. Nie ma potrzeby programowania, można wizualnie połączyć zestawy danych i moduły, aby utworzyć model. Wypróbuj [samouczek projektanta](tutorial-designer-automobile-price-train-score.md).

  Więcej informacji znajduje się w [artykule Omówienie projektanta Azure Machine Learning](concept-designer.md). 

  ![Przykład projektanta Azure Machine Learning](./media/overview-what-is-azure-ml/designer-drag-and-drop.gif)

+ **Zautomatyzowany interfejs użytkownika uczenia maszynowego**

  Dowiedz się, jak utworzyć [zautomatyzowane eksperymenty ml](tutorial-first-experiment-automated-ml.md) w łatwym w użyciu interfejsie. 

  [okienko nawigacji ![Azure Machine Learning Studio](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

## <a name="mlops-deploy--lifecycle-management"></a>MLOps: wdrażanie & zarządzania cyklem życia
Jeśli dysponujesz odpowiednim modelem, możesz go łatwo używać w usłudze sieci Web, na urządzeniu IoT lub z Power BI. Aby uzyskać więcej informacji, zobacz artykuł opisujący, [jak i gdzie wdrażać](how-to-deploy-and-where.md).

Następnie możesz zarządzać wdrożonymi modelami przy użyciu [zestawu SDK Azure Machine Learning dla języka Python](https://aka.ms/aml-sdk), [Azure Machine Learning Studio](https://ml.azure.com)lub [interfejsu wiersza polecenia Machine Learning](reference-azure-machine-learning-cli.md).

Te modele mogą być używane i zwracać przewidywania w [czasie rzeczywistym](how-to-consume-web-service.md) lub [asynchronicznie](how-to-run-batch-predictions.md) w przypadku dużych ilości danych.

Dzięki zaawansowanym [potokom uczenia maszynowego](concept-ml-pipelines.md)można współpracować nad każdym krokiem w procesie przygotowywania danych, uczenia modelu i oceny, dzięki wdrożeniu. Potoki umożliwiają:

* Automatyzuj kompleksowy proces uczenia maszynowego w chmurze
* Ponownie Użyj składników i ponownie wykonaj kroki w razie konieczności
* Użyj innych zasobów obliczeniowych w każdym kroku
* Uruchamianie zadań oceniania partii

Jeśli chcesz używać skryptów do automatyzowania przepływu pracy uczenia maszynowego, [interfejs wiersza polecenia uczenia maszynowego](reference-azure-machine-learning-cli.md) udostępnia narzędzia do obsługi typowych zadań, takie jak przesyłanie szkoleń lub Wdrażanie modelu.

Aby rozpocząć korzystanie z Azure Machine Learning, zobacz [następne kroki](#next-steps).

## <a name="integration-with-other-services"></a>Integracja z innymi usługami

Azure Machine Learning współpracuje z innymi usługami na platformie Azure, a także integruje się z narzędziami typu open source, takimi jak Git i MLFlow.

+ Cele obliczeniowe, takie jak __usługa Azure Kubernetes__, __Azure Container Instances__, __Azure Databricks__, __Azure Data Lake Analytics__i __Azure HDInsight__. Aby uzyskać więcej informacji na temat obiektów docelowych obliczeń, zobacz [co to są cele obliczeniowe?](concept-compute-target.md).
+ __Azure Event Grid__. Aby uzyskać więcej informacji, zobacz temat [Korzystanie z zdarzeń Azure Machine Learning](concept-event-grid-integration.md).
+ __Azure monitor__. Aby uzyskać więcej informacji, zobacz [monitorowanie Azure Machine Learning](monitor-azure-machine-learning.md).
+ Magazyny danych, takie jak __konta usługi Azure Storage__, __Azure Data Lake Storage__, __Azure SQL Database__, __Azure Database for PostgreSQL__i __otwarte zestawy danych platformy Azure__. Aby uzyskać więcej informacji, zobacz [dostęp do danych w usługach Azure Storage](how-to-access-data.md) i [Tworzenie zestawów danych za pomocą otwartych zestawów danych platformy Azure](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets).
+ __Sieci wirtualne platformy Azure__. Aby uzyskać więcej informacji, zobacz temat [bezpieczne eksperymentowanie i wnioskowanie w sieci wirtualnej](how-to-enable-virtual-network.md).
+ __Azure Pipelines__. Aby uzyskać więcej informacji, zobacz [uczenie i wdrażanie modeli uczenia maszynowego](/azure/devops/pipelines/targets/azure-machine-learning).
+ __Dzienniki repozytorium git__. Aby uzyskać więcej informacji, zobacz Integracja z usługą [git](concept-train-model-git-integration.md).
+ __MLFlow__. Aby uzyskać więcej informacji, zobacz [MLflow do śledzenia metryk i wdrażania modeli](how-to-use-mlflow.md) 
+ __Kubeflow__. Aby uzyskać więcej informacji, zobacz [Tworzenie kompleksowych potoków przepływu pracy](https://www.kubeflow.org/docs/azure/).

### <a name="secure-communications"></a>Bezpieczna komunikacja

Twoje konto usługi Azure Storage, cele obliczeniowe i inne zasoby mogą być bezpiecznie używane w sieci wirtualnej do uczenia modeli i wykonywania wnioskowania. Aby uzyskać więcej informacji, zobacz temat [bezpieczne eksperymentowanie i wnioskowanie w sieci wirtualnej](how-to-enable-virtual-network.md).

## <a name="sku"></a>Wersje Basic & Enterprise

Azure Machine Learning oferuje dwie wersje dostosowane do potrzeb uczenia maszynowego:
+ Podstawowa (ogólnie dostępna)
+ Enterprise (wersja zapoznawcza)

Te wersje określają, które narzędzia uczenia maszynowego są dostępne dla deweloperów i naukowców danych z ich obszaru roboczego.   

Podstawowe obszary robocze umożliwiają kontynuowanie korzystania z Azure Machine Learning i płatność tylko za zasoby platformy Azure używane w ramach procesu uczenia maszynowego. W obszarze roboczym wersji Enterprise Edition będą naliczane opłaty za użycie platformy Azure, gdy wersja jest w wersji zapoznawczej. Dowiedz się więcej o tym, co jest dostępne w [przeglądzie Azure Machine Learning wersji & stronie cennika](https://azure.microsoft.com/pricing/details/machine-learning/). 

Wydanie jest przypisywane po każdym utworzeniu obszaru roboczego. Wcześniej istniejące obszary robocze zostały przekonwertowane na wersję podstawową. Wersja podstawowa obejmuje wszystkie funkcje, które są już ogólnie dostępne od października 2019. Wszystkie eksperymenty w tych obszarach roboczych, które zostały skompilowane przy użyciu funkcji wersji Enterprise Edition, będą nadal dostępne w trybie tylko do odczytu do momentu uaktualnienia do wersji Enterprise. Dowiedz się, jak [uaktualnić podstawowy obszar roboczy do wersji Enterprise](how-to-manage-workspace.md#upgrade). 

W tym czasie klienci są zobowiązani do ponoszenia kosztów związanych z obliczeniami i innymi zasobami platformy Azure.

## <a name="next-steps"></a>Następne kroki

- Utwórz pierwszy eksperyment przy użyciu preferowanej metody:
  + [Korzystanie z notesów Python do uczenia & Wdrażanie modeli ML](tutorial-1st-experiment-sdk-setup.md)
  + [Używanie R Markdown do uczenia & wdrażania modeli ML](tutorial-1st-r-experiment.md) 
  + [Używanie automatycznej uczenia maszynowego do uczenia & Wdrażanie modeli ML](tutorial-first-experiment-automated-ml.md) 
  + [Korzystanie z funkcji upuszczania & przez projektanta w celu uczenia & wdrożenia](tutorial-designer-automobile-price-train-score.md) 
  + [Korzystanie z interfejsu wiersza polecenia Machine Learning w celu uczenia i wdrożenia modelu](tutorial-train-deploy-model-cli.md)

- Dowiedz się więcej o [potokach uczenia maszynowego](concept-ml-pipelines.md) w celu tworzenia i optymalizowania scenariuszy uczenia maszynowego oraz zarządzania nimi.

- Zapoznaj się z szczegółowymi artykułami [dotyczącymi architektury i koncepcji Azure Machine Learning](concept-azure-machine-learning-architecture.md) .
