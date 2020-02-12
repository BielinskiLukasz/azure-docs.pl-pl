---
title: 'Regresja logistyczna na dwie klasy: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać dwuklasowego modułu regresji logistycznej w Azure Machine Learning, aby utworzyć model regresji logistycznej, który może być używany do przewidywania dwóch (i tylko dwóch) rezultatów.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 541d1001f8b5881f2773f795d7bd849704cbd796
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153693"
---
# <a name="two-class-logistic-regression-module"></a>Moduł regresji logistycznej dla dwóch klas

W tym artykule opisano moduł w programie Azure Machine Learning Designer (wersja zapoznawcza).

Użyj tego modułu, aby utworzyć model regresji logistycznej, który może służyć do przewidywania dwóch (i tylko dwóch) wyników. 

Regresja logistyczna to dobrze znana technika statystyczna, która jest używana do modelowania wielu rodzajów problemów. Ten algorytm to *nadzorowana Metoda uczenia* się;  w związku z tym musisz dostarczyć zestaw danych, który zawiera już wyniki do uczenia modelu.  

### <a name="about-logistic-regression"></a>Regresja logistyczna — informacje  

Regresja logistyczna to dobrze znana metoda w statystyce, która jest używana do przewidywania prawdopodobieństwa wyniku i jest szczególnie popularna w przypadku zadań klasyfikacji. Algorytm przewiduje prawdopodobieństwo wystąpienia zdarzenia przez dopasowanie danych do funkcji logistycznej.
  
W tym module algorytm klasyfikacji jest zoptymalizowany pod kątem dichotomous lub zmiennych binarnych. Jeśli potrzebujesz sklasyfikować wiele wyników, użyj modułu [regresja logistyczna](./multiclass-logistic-regression.md) dla wielu klas.

##  <a name="how-to-configure"></a>Jak skonfigurować  

Aby szkolić ten model, należy dostarczyć zestaw danych, który zawiera etykietę lub kolumnę klasy. Ponieważ ten moduł jest przeznaczony do rozwiązywania problemów z dwoma klasami, etykieta lub kolumna klasy musi zawierać dokładnie dwie wartości. 

Na przykład kolumna etykieta może być [głosowana] z możliwymi wartościami "yes" lub "No". Lub może to być [ryzyko kredytowe], z możliwymi wartościami "High" lub "Low". 
  
1.  Dodaj moduł **regresja logistyczna dla dwóch klas** do potoku.  
  
2.  Określ, w jaki sposób ma być szkolony model, ustawiając opcję **tworzenia trybu Trainer** .  
  
    -   **Pojedynczy parametr**: Jeśli wiesz, jak chcesz skonfigurować model, możesz podać określony zestaw wartości jako argumenty.  
  
3.  Aby uzyskać **tolerancję optymalizacji**, określ wartość progową, która ma być używana podczas optymalizowania modelu. Jeśli poprawa między iteracjami spadnie poniżej określonego progu, algorytm jest uznawany za zbieżny w rozwiązaniu, a szkolenie zostanie zatrzymane.  
  
4.  W przypadku **wag uregulowania** i **wagi L2**wpisz wartość, która ma być używana dla parametrów uregulowania L1 i L2. Dla obu tych zaleca się wartość różną od zera.  
     *Uregulowanie* to metoda zapobiegania zastępowaniu poprzez nakładanie się modeli o wartości skrajnego współczynnika. Uregulowanie działa przez dodanie kary, która jest skojarzona z wartościami współczynnika, do błędu hipotezy. W ten sposób dokładny model z wartościami współczynnika skrajnie byłby bardziej karany, ale mniej dokładny model z bardziej bardziej nieprawidłowymi wartościami będzie mniejszy.  
  
     Uregulowanie L1 i L2 mają różne efekty i używa.  
  
    -   L1 można zastosować do modeli rozrzedzonych, co jest przydatne podczas pracy z danymi o dużym wymiarze.  
  
    -   Z kolei w przypadku danych, które nie są rozrzedzone, preferowane jest uregulowanie L2.  
  
     Ten algorytm obsługuje liniową kombinację wartości rozliczania L1 i L2: oznacza to, że jeśli <code>x = L1</code> i <code>y = L2</code>, <code>ax + by = c</code> definiuje zakres liniowy warunków uregulowania.  
  
    > [!NOTE]
    >  Chcesz dowiedzieć się więcej na temat uregulowania L1 i L2? Poniższy artykuł zawiera omówienie sposobu, w jaki uregulowania L1 i L2 są różne i jak wpływają na ich dopasowanie, z przykładami kodu na potrzeby regresji logistycznej i modeli sieci neuronowych: [w przypadku Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Dla modeli regresji logistycznej opracowano różne liniowe kombinacje warunków L1 i L2: na przykład [elastyczne uregulowanie netto](https://wikipedia.org/wiki/Elastic_net_regularization). Sugerujemy, aby odwoływać się do tych kombinacji w celu zdefiniowania kombinacji liniowej, która obowiązuje w modelu.
      
5.  W polu **rozmiar pamięci dla l-BFGS**określ ilość pamięci, która ma być używana dla optymalizacji *l-BFGS* .  
  
     L-BFGS oznacza "Limited Memory Broyden-Fletcher-Goldfarb-Shanno". Jest to algorytm optymalizacji, który jest popularny dla szacowania parametrów. Ten parametr wskazuje liczbę wcześniejszych pozycji i gradientów do przechowania w obliczeniach następnego kroku.  
  
     Ten parametr optymalizacji ogranicza ilość pamięci, która jest używana do obliczania następnego etapu i kierunku. W przypadku określenia mniejszej ilości pamięci szkolenie jest szybsze, ale mniej dokładne.  
  
6.  W przypadku **liczby losowej inicjatora**wpisz wartość całkowitą. Definiowanie wartości inicjatora jest ważne, jeśli chcesz, aby wyniki były odtwarzalne dla wielu przebiegów tego samego potoku.  
  
  
8. Dodaj oznakowany zestaw danych do potoku i Połącz jeden z [modułów szkoleniowych](module-reference.md).  
  
    -   W przypadku ustawienia opcji **Utwórz tryb Trainer** na **pojedynczy parametr**Użyj modułu [uczenie modelu](./train-model.md) .  
  
9. Uruchamianie potoku.  
  
## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:
 
  
+ Aby dokonać prognoz dotyczących nowych danych, użyj przeszkolonego modelu i nowych danych jako danych wejściowych w module [model oceny](./score-model.md) . 


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 