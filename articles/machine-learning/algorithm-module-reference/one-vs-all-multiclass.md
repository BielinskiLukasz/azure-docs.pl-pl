---
title: moduł wieloklasowy "jeden przeciw wszystkim"
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób używać modułu moduł wieloklasowy "jeden przeciw wszystkim" w programie Azure Machine Learning do tworzenia wieloklasowego modelu klasyfikacji na podstawie kompletnych modeli klasyfikacji binarnych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: bf4f176eef502cdd1c68f19904a24bd2500e1302
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75428511"
---
# <a name="one-vs-all-multiclass"></a>moduł wieloklasowy "jeden przeciw wszystkim"

W tym artykule opisano sposób korzystania z modułu moduł wieloklasowy "jeden przeciw wszystkim" w programie Azure Machine Learning Designer (wersja zapoznawcza). Celem jest utworzenie modelu klasyfikacji, który może przewidzieć wiele klas, przy użyciu podejścia *jednokierunkowego* .

Ten moduł jest przydatny do tworzenia modeli predykcyjnych trzy lub więcej wyników, gdy wynik zależy od zmiennych predykcyjnych ciągłych lub kategorii. Ta metoda umożliwia także używanie binarnych metod klasyfikacji dla problemów wymagających wielu klas wyjściowych.

### <a name="more-about-one-versus-all-models"></a>Więcej informacji o modelach jeden-do-wszystkie

Niektóre algorytmy klasyfikacji umożliwiają użycie więcej niż dwóch klas według konstrukcji. Inne ograniczają możliwe wyniki do jednej z dwóch wartości (binarny lub dwuklasowy model). Jednak nawet binarne algorytmy klasyfikacji można dostosować do wieloklasowych zadań klasyfikacji za pomocą różnych strategii. 

Ten moduł implementuje metodę "one-a-All", w której jest tworzony model binarny dla każdej klasy danych wyjściowych. Moduł ocenia każdy z tych modeli binarnych dla poszczególnych klas przed jego uzupełnieniem (wszystkie inne klasy w modelu), tak jakby był to binarny problem z klasyfikacją. Następnie moduł wykonuje prognozowanie, uruchamiając te klasyfikatory binarne i wybierając prognozę z najwyższym wynikiem ufności.  

W zasadzie moduł tworzy kompletność poszczególnych modeli, a następnie scala wyniki, aby utworzyć jeden model, który przewiduje wszystkie klasy. Dowolny klasyfikator binarny może być używany jako podstawa dla modelu typu jeden-do-wszystkich.  

Załóżmy na przykład, że skonfigurowano model [maszyny wektorowej obsługujący dwie klasy](two-class-support-vector-machine.md) i zapewnia to, że jako dane wejściowe do modułu moduł wieloklasowy "jeden przeciw wszystkim". Moduł utworzy modele maszyn wektorowych obsługujące dwie klasy dla wszystkich elementów członkowskich klasy wyjściowej. Następnie zastosuj metodę "jeden do wszystkiego", aby połączyć wyniki dla wszystkich klas.  

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>Jak skonfigurować klasyfikator moduł wieloklasowy "jeden przeciw wszystkim"  

Ten moduł tworzy kompletną liczbę binarnych modeli klasyfikacji do analizowania wielu klas. Aby użyć tego modułu, należy najpierw skonfigurować i szkolić *binarny model klasyfikacji* . 

Model binarny jest połączony z modułem moduł wieloklasowy "jeden przeciw wszystkim". Następnie należy wyszkolić zestaw modeli przy użyciu [modelu uczenia](train-model.md) z zestawu danych szkolenia z etykietą.

W przypadku łączenia modeli moduł wieloklasowy "jeden przeciw wszystkim" tworzy wiele binarnych modeli klasyfikacji, optymalizuje algorytm dla każdej klasy, a następnie scala modele. Moduł wykonuje te zadania mimo tego, że zestaw danych szkoleniowych może mieć wiele wartości klas.

1. Dodaj moduł moduł wieloklasowy "jeden przeciw wszystkim" do potoku w projektancie. Ten moduł można znaleźć w obszarze **Machine Learning-Initialize**w kategorii **Klasyfikacja** .

   Klasyfikator moduł wieloklasowy "jeden przeciw wszystkim" nie ma samodzielnie skonfigurowanych parametrów. Wszelkie dostosowania należy wykonać w modelu klasyfikacji binarnej, który jest dostarczany jako dane wejściowe.

2. Dodaj binarny model klasyfikacji do potoku i skonfiguruj ten model. Można na przykład użyć [dwuklasowej maszyny wektorowej](two-class-support-vector-machine.md) lub [dwuklasowego drzewa decyzyjnego](two-class-boosted-decision-tree.md).

3. Dodaj moduł [uczenie modelu](train-model.md) do potoku. Połącz niepociąg klasyfikatora, który jest danymi wyjściowymi moduł wieloklasowy "jeden przeciw wszystkim".

4. Na innym wejściu [modelu uczenia](train-model.md)Połącz zestaw danych szkoleń z etykietą, który ma wiele wartości klas.

5. Uruchamianie potoku.

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia możesz użyć modelu, aby utworzyć prognozy wieloklasowe.

Alternatywnie, można przekazać [niesprawdzony klasyfikator do modelu krzyżowego](cross-validate-model.md) walidacji w celu krzyżowego sprawdzania poprawności względem zestawu danych walidacji z etykietami.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
