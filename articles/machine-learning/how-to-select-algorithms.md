---
title: Jak wybrać algorytm uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Jak wybrać algorytmy Azure Machine Learning dla nadzorowanych i nienadzorowanych uczenia się w przypadku eksperymentów, klasyfikacji lub regresji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 05/07/2020
ms.openlocfilehash: 98f7edac5bbec7a88999c728b2e4db8be7a3d2b5
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891341"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>Jak wybrać algorytmy dla Azure Machine Learning

Typowym pytaniem jest "którego algorytmu uczenia maszynowego należy użyć?". Wybrany algorytm zależy głównie od dwóch różnych aspektów scenariusza analizy danych:

 - **Co chcesz zrobić z danymi?** Co to jest pytanie biznesowe, na które chcesz odpowiedzieć na podstawie przeszłych danych?

 - **Jakie są wymagania scenariusza analizy danych?** W odróżnieniu od tego, jaka jest dokładność, czas uczenia, liniowość, liczba parametrów i liczba funkcji obsługiwanych przez rozwiązanie?

 ![Zagadnienia dotyczące wybierania algorytmów: co chcesz wiedzieć? Jakie są wymagania dotyczące scenariusza?](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>Scenariusze biznesowe i arkusz Machine Learning Ściągawka algorytmu

[Arkusz Azure Machine Learning Ściągawka Algorithm](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri) pomaga z pierwszym uwzględnieniem: **co chcesz zrobić z danymi**? Na Ściągawka algorytmu Machine Learning, Wyszukaj zadanie, które chcesz wykonać, a następnie Znajdź algorytm [Azure Machine Learning Designer](https://docs.microsoft.com/azure/machine-learning/concept-designer?WT.mc_id=docs-article-lazzeri) dla rozwiązania do analizy predykcyjnej. 

Program Machine Learning Designer zapewnia kompleksowy portfel algorytmów, takich jak [Las decyzyjny wieloklasowe](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri), [systemy rekomendacji](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-recommender?WT.mc_id=docs-article-lazzeri), [regresja sieci neuronowych](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri), [wieloklasowa sieć neuronowych](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri)i [K-oznacza klastrowanie](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri). Każdy algorytm jest przeznaczony do rozwiązywania innego typu problemu uczenia maszynowego. Aby uzyskać pełną listę wraz z dokumentacją dotyczącą sposobu działania poszczególnych algorytmów i sposobu dostrajania parametrów w celu optymalizacji algorytmu, zobacz Opis [algorytmu Machine Learning projektanta i odwołania do modułu](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri) .

> [!NOTE]
> Aby pobrać arkusz ściągawkay algorytmu uczenia maszynowego, przejdź do [arkusza Ściągawka (algorytm usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri)).
> 
> 

Wraz ze wskazówkami Azure Machine Learning w arkuszu Ściągawka Algorithm, pamiętaj o innych wymaganiach podczas wybierania algorytmu uczenia maszynowego dla rozwiązania. Poniżej znajdują się dodatkowe czynniki, które należy wziąć pod uwagę, takie jak dokładność, czas uczenia, liniowość, liczba parametrów i liczba funkcji.

## <a name="comparison-of-machine-learning-algorithms"></a>Porównanie algorytmów uczenia maszynowego

Niektóre algorytmy uczenia składają szczególne założenia dotyczące struktury danych lub żądanych wyników. Jeśli znajdziesz je, która odpowiada Twoim potrzebom, może to dać bardziej przydatne wyniki, dokładniejsze przewidywania lub szybszy czas uczenia się.

Poniższa tabela zawiera podsumowanie najważniejszych cech algorytmów z rodziny klasyfikacji, regresji i klastrowania:

| **Algorytm** | **Odpowiedni** | **Czas trenowania** | **Liniowość** | **Parametry** | **Uwagi** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Rodzina klasyfikacji** | | | | | |
| [Regresja logistyczna dla dwóch klas](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri) |Dobrze  |Fast |Tak |4 | |
| [Las decyzyjny dwóch klas](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-decision-forest?WT.mc_id=docs-article-lazzeri) |Excellent (Doskonały) |Średni |Nie |5 |Pokazuje wolniejsze czasy oceniania. Sugeruj, że nie pracuje z moduł wieloklasowy "jeden przeciw wszystkim", z powodu wolniejszych czasów oceniania spowodowanych przez blokowanie bieżnika w przypadku prognozowania drzewa |
| [Dwuklasowe drzewo decyzyjne](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-boosted-decision-tree?WT.mc_id=docs-article-lazzeri) |Excellent (Doskonały) |Średni |Nie |6 |Duże rozmiary pamięci |
| [Sieć neuronowych z dwiema klasami](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-neural-network?WT.mc_id=docs-article-lazzeri) |Dobrze |Średni |Nie |8 | |
| [Średnia Perceptron średniej klasy](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-averaged-perceptron?WT.mc_id=docs-article-lazzeri) |Dobrze |Średni |Tak |4 | |
| [Maszyna wektorowa obsługi dwóch klas](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri) |Dobrze |Fast |Tak |5 |Dobre dla dużych zestawów funkcji |
| [Wieloklasowa regresja logistyczna](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri) |Dobrze |Fast |Tak |4 | |
| [Las decyzyjny wieloklasowej](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri) |Excellent (Doskonały) |Średni |Nie |5 |Pokazuje wolniejsze czasy oceniania |
| [Wieloklasowe drzewo decyzyjne](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-boosted-decision-tree?WT.mc_id=docs-article-lazzeri) |Excellent (Doskonały) |Średni |Nie |6 | Pozwala zwiększyć dokładność z niewielkim ryzykiem mniejszego pokrycia |
| [Wieloklasowa sieć neuronowych](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri) |Dobrze |Średni |Nie |8 | |
| [Jeden — i wiele klas](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/one-vs-all-multiclass?WT.mc_id=docs-article-lazzeri) | - | - | - | - |Zobacz właściwości wybranej metody dwuklasowej |
| **Rodzina regresji** | | | | | |
| [Regresja liniowa](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/linear-regression?WT.mc_id=docs-article-lazzeri) |Dobrze |Fast |Tak |4 | |
| [Regresja lasu decyzyjnego](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/decision-forest-regression?WT.mc_id=docs-article-lazzeri)|Excellent (Doskonały) |Średni |Nie |5 | |
| [Regresja drzewa decyzyjnej](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/boosted-decision-tree-regression?WT.mc_id=docs-article-lazzeri) |Excellent (Doskonały) |Średni |Nie |6 |Duże rozmiary pamięci |
| [Regresja sieci neuronowych](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri) |Dobrze |Średni |Nie |8 | |
| **Rodzina klastrowania** | | | | | |
| [K-oznacza klastrowanie](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri) |Excellent (Doskonały) |Średni |Tak |8 |Algorytm klastrowania |

## <a name="requirements-for-a-data-science-scenario"></a>Wymagania dotyczące scenariusza analizy danych

Gdy wiesz, co chcesz zrobić z danymi, musisz określić dodatkowe wymagania dotyczące rozwiązania. 

Wprowadź opcje i ewentualne wady, które są dostępne dla następujących wymagań:

- Odpowiedni
- Czas trenowania
- Liniowość
- Liczba parametrów
- Liczba funkcji

## <a name="accuracy"></a>Odpowiedni

Dokładność w uczeniu maszynowym mierzy efektywność modelu jako stosunek rzeczywistych wyników do łącznej liczby przypadków. W programie Machine Learning Designer [moduł oceny modelu](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri) oblicza zestaw metryk oceny standardowej w branży. Ten moduł służy do mierzenia dokładności nauczonego modelu.

Uzyskanie najdokładniejszej możliwej odpowiedzi nie zawsze jest konieczne. Czasami przybliżenie jest wystarczające, w zależności od tego, czego chcesz używać. W takim przypadku może być możliwe skrócenie czasu przetwarzania przez naklejenie do bardziej przybliżonych metod. Przybliżone metody również są w naturalny sposób zadbać o uniknięcie przerastania.

Istnieją trzy sposoby używania modułu szacowania modelu:

- Generuj wyniki na danych szkoleniowych, aby oszacować model
- Generuj wyniki na modelu, ale Porównaj te wyniki z wynikami w zestawie testów zarezerwowanych
- Porównanie wyników dla dwóch różnych, ale powiązanych modeli, przy użyciu tego samego zestawu danych

Aby uzyskać pełną listę metryk i metod, których można użyć do obliczenia dokładności modeli uczenia maszynowego, zobacz temat [Oceń model modułu](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri).

## <a name="training-time"></a>Czas trenowania

W szkole nadzorowanej uczenie się to przy użyciu danych historycznych do kompilowania modelu uczenia maszynowego, który minimalizuje błędy. Liczba minut lub godzin koniecznych do uczenia modelu różni się bardzo znaczącą różnicą między algorytmami. Czas uczenia jest często ściśle związany z dokładnością; zwykle jest to jedna z nich. 

Ponadto niektóre algorytmy są bardziej czułe na liczbę punktów danych niż inne. Można wybrać konkretny algorytm, ponieważ obowiązuje ograniczenie czasu, szczególnie w przypadku dużego zestawu danych.

W programie Machine Learning Designer Tworzenie modelu uczenia maszynowego i korzystanie z niego jest zwykle procesem trójwymiarowym:

1.  Skonfiguruj model, wybierając określony typ algorytmu, a następnie definiując jego parametry lub parametr. 

2.  Podaj zestaw danych, który ma etykietę i zawiera dane zgodne z algorytmem. Połącz zarówno dane, jak i model, aby przeprowadzić [Uczenie modułu modelu](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/train-model?WT.mc_id=docs-article-lazzeri).

3.  Po zakończeniu szkolenia Użyj przeszkolonego modelu z jednym z [modułów oceniania](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/score-model?WT.mc_id=docs-article-lazzeri) , aby tworzyć przewidywania dotyczące nowych danych.

## <a name="linearity"></a>Liniowość

Liniowość statystyk i uczenie maszynowe oznacza, że istnieje liniowa relacja między zmienną i stałą w zestawie danych. Na przykład algorytmy klasyfikacji liniowej zakładają, że klasy mogą być oddzielone linią prostą (lub bardziej trójwymiarową metodą analogową).

Wiele algorytmów uczenia maszynowego wykorzystuje liniowość. W programie Azure Machine Learning Designer obejmują: 

- [Wieloklasowa regresja logistyczna](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Regresja logistyczna dla dwóch klas](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Obsługa maszyn wektorowych](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)  

Algorytmy regresji liniowej zakładają, że trendy danych są zgodne z prostą linią. To założenie nie jest złe w przypadku niektórych problemów, ale dla innych osób zmniejsza dokładność. Pomimo ich wad, algorytmy liniowe są popularne jako pierwsza strategia. Są one algorithmically proste i szybkie do uczenia się.

![Granica klasy nieliniowej](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

***Nieliniowa granica klasy***: *poleganie na algorytmie klasyfikacji liniowej spowodowałoby niską dokładność.*

![Dane z trendem nieliniowym](./media/how-to-select-algorithms/nonlinear-trend.png)

***Dane z trendem nieliniowym***: *użycie metody regresji liniowej spowoduje wygenerowanie znacznie większych błędów niż jest to konieczne.*

## <a name="number-of-parameters"></a>Liczba parametrów

Parametry są pokrętłami, które są włączane przez analityka danych podczas konfigurowania algorytmu. Są to liczby, które mają wpływ na zachowanie algorytmu, takie jak odporność na błędy lub liczba iteracji, lub opcje między wariantami zachowania algorytmu. Czas uczenia i dokładność algorytmu mogą czasami być wrażliwe na uzyskanie tylko odpowiednich ustawień. Zazwyczaj algorytmy o dużej liczbie parametrów wymagają największej wersji próbnej i błędu, aby znaleźć dobrą kombinację.

Istnieje również możliwość, że [moduł strojenia modelu](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/tune-model-hyperparameters?WT.mc_id=docs-article-lazzeri) jest w programie Machine Learning Designer: celem tego modułu jest określenie optymalnych parametrów dla modelu uczenia maszynowego. Moduł kompiluje i testuje wiele modeli przy użyciu różnych kombinacji ustawień. Porównuje metryki dla wszystkich modeli w celu uzyskania kombinacji ustawień. 

Chociaż jest to świetny sposób, aby upewnić się, że połączono miejsce parametru, czas wymagany do uczenia modelu rośnie wykładniczo z liczbą parametrów. Do góry jest, że wiele parametrów zwykle wskazuje, że algorytm ma większą elastyczność. Często można uzyskać bardzo dobrą dokładność, pod warunkiem znalezienia odpowiedniej kombinacji ustawień parametrów.

## <a name="number-of-features"></a>Liczba funkcji

W uczeniu maszynowym funkcja jest zmienną wymierną zjawiska, którą próbujesz analizować. W przypadku niektórych typów danych liczba funkcji może być bardzo duża w porównaniu do liczby punktów danych. Często dzieje się tak w przypadku danych genetycznych lub tekstowych. 

Wiele funkcji może ją przeciążyć niektóre algorytmy uczenia, co wydłuża czas uczenia unfeasibly. [Maszyny wektorowe obsługi](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri) są szczególnie dostosowane do scenariuszy o dużej liczbie funkcji. Z tego powodu zostały one użyte w wielu aplikacjach na podstawie pobierania informacji do klasyfikacji tekstu i obrazu. Maszyny wektorowe obsługi mogą służyć do zadań klasyfikacji i regresji.

Wybór funkcji odnosi się do procesu zastosowania testów statystycznych do danych wejściowych, z uwzględnieniem określonych danych wyjściowych. Celem jest określenie, które kolumny są bardziej predykcyjne w danych wyjściowych. [Moduł wyboru funkcji oparty na filtrach](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/filter-based-feature-selection?WT.mc_id=docs-article-lazzeri) w programie Machine Learning Designer udostępnia wiele algorytmów wyboru funkcji do wyboru. Moduł zawiera metody korelacji, takie jak korelacja Pearsona i wartości chi-kwadrat.

Można również użyć [modułu ważności funkcji permutacji](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/permutation-feature-importance?WT.mc_id=docs-article-lazzeri) do obliczenia zestawu ocen ważności funkcji dla zestawu danych. Następnie można wykorzystać te wyniki, aby pomóc w ustaleniu najlepszych funkcji do użycia w modelu.

## <a name="next-steps"></a>Następne kroki

 - [Dowiedz się więcej o programie Azure Machine Learning Designer](https://docs.microsoft.com/azure/machine-learning/service/concept-designer?WT.mc_id=docs-article-lazzeri)
 - Opisy wszystkich algorytmów uczenia maszynowego dostępnych w projektancie Azure Machine Learning można znaleźć w temacie [Machine Learning projektanta i dokumentacja modułu](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri)
 - Aby poznać relacje między uczeniem szczegółowym, uczeniem maszynowym i AI, zobacz [uczenie głębokie a Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-deep-learning-vs-machine-learning?WT.mc_id=docs-article-lazzeri)
