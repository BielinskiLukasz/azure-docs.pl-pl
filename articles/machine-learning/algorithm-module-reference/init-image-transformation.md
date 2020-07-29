---
title: Przekształcenie obrazu Transformationply init Image
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zainicjować transformację obrazu przy użyciu modułu transformacji obrazu init.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: f54dd09ac82ddefeb2528462c7131aa921592d2a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84487954"
---
# <a name="init-image-transformation"></a>Inicjowanie transformacji obrazów

W tym artykule opisano, jak używać modułu **transformacji obrazu init** w programie Azure Machine Learning Designer (wersja zapoznawcza) w celu zainicjowania przekształcenia obrazu w celu określenia, w jaki sposób ma zostać przekształcony obraz.

## <a name="how-to-configure-init-image-transformation"></a>Jak skonfigurować transformację obrazu init

1.  Dodaj moduł **przekształcenia obrazu init** do potoku w projektancie. 

2.  W polu **rozmiar Zmień**rozmiar obrazu wejściowego PIL na dany rozmiar. W przypadku wybrania wartości "true" można określić **żądany rozmiar obrazu wyjściowego, domyślnie**256. 

3.  W przypadku **kadrowania centrum**Określ, czy dany obraz PIL ma zostać przycięty do środka. W przypadku wybrania wartości "true" można określić żądany rozmiar obrazu wyjściowego przycinania w **rozmiarze przycinania**, domyślnie 224.  

4.  Dla **konsoli**Określ, czy dany obraz PIL ma być konsolą na wszystkich stronach z wartością pad 0. W przypadku wybrania wartości "true" można określić uzupełnienie (liczbę pikseli do dodania) dla każdego obramowania w **pełni**.

5.  W celu **wahania kolorów**Określ, czy chcesz losowo zmienić jasność, kontrast i nasycenie obrazu.

6.  W obszarze **Skala szarości**Określ, czy ma zostać przekonwertowany obraz do skali szarości.

7.  W przypadku **przycinania losowego o zmienionym rozmiarze**Określ, czy przycinanie danego obrazu PIL do wielkości losowej i współczynnika proporcji. Zostanie przycięty rozmiar losowy (zakres od 0,08 do 1,0) oryginalnego rozmiaru i losowy współczynnik proporcji (zakres od 3/4 do 4/3) oryginalnego współczynnika proporcji. To przycinanie jest ostatecznie zmieniane na przyznany rozmiar.
    Jest to często używane w szkoleniu sieci w trakcie. W przypadku wybrania wartości "true" można określić oczekiwany rozmiar danych wyjściowych każdej krawędzi w **rozmiarze losowym**, domyślnie 256.

8.  W przypadku **kadrowania losowego**Określ, czy dany obraz PIL ma zostać przycięty do lokalizacji losowej. W przypadku wybrania wartości "true" można określić żądany rozmiar wyjściowy kadrowania w **rozmiarze losowej uprawy**, domyślnie 224.

9.  W przypadku **losowego przerzucania poziomego**należy określić, czy w poziomie należy losowo przerzucić dany obraz PIL z prawdopodobieństwem 0,5.

10.  W przypadku **losowego przerzucania pionowego**Określ, czy w pionie przerzucasz dany obraz PIL z prawdopodobieństwem 0,5.

11.  W celu **przypadkowego obrotu**Określ, czy obraz ma być obracany według kąta. W przypadku wybrania wartości "true" można określić w zakresie stopni, ustawiając **losowe stopnie rotacji**, co oznacza (-stopnie, + stopnie), domyślnie 0.

12.  W przypadku **losowych afinicznym**Określ, czy losowo przekształcenie afinicznym obrazu ma być niezmienny. W przypadku wybrania wartości "true" można określić zakres stopni, który ma być wybierany **losowo afinicznym stopni**, co oznacza (-stopnie, + stopnie), domyślnie 0.

13.  W przypadku **losowej skali odcieni szarości**Określ, czy losowo skonwertować obraz do skali odcieni szarości z prawdopodobieństwem 0,1.

14.  W przypadku **losowej perspektywy**Określ, czy program ma wykonywać transformację perspektywy danego obrazu PIL losowo z prawdopodobieństwem 0,5.


16.  Połącz się z [zastosowaniem modułu transformacji obrazu](apply-image-transformation.md) , aby zastosować transformację określoną powyżej do zestawu danych obrazu wejściowego.

17. Prześlij potok.

## <a name="results"></a>Wyniki

Po zakończeniu transformacji można znaleźć przekształcone obrazy w danych wyjściowych modułu [Zastosuj transformację obrazu](apply-image-transformation.md) .


## <a name="technical-notes"></a>Uwagi techniczne  

Aby [https://pytorch.org/docs/stable/torchvision/transforms.html](https://pytorch.org/docs/stable/torchvision/transforms.html) uzyskać więcej informacji na temat transformacji obrazu, zobacz.

###  <a name="module-parameters"></a>Parametry modułu  

| Nazwa                    | Zakres   | Typ    | Domyślne | Opis                              |
| ----------------------- | ------- | ------- | ------- | ---------------------------------------- |
| Zmiana rozmiaru                  | Dowolne     | Boolean | Prawda    | Zmień rozmiar obrazu wejściowego PIL na dany rozmiar |
| Rozmiar                    | >= 1     | Integer | 256     | Określ żądany rozmiar wyjściowy          |
| Przytnij do środka             | Dowolne     | Boolean | Prawda    | Przycina dany obraz PIL w środku  |
| Rozmiar przycinania               | >= 1     | Integer | 224     | Określ żądany rozmiar wyjściowy kadrowania |
| Pad                     | Dowolne     | Boolean | Fałsz   | Zablokuj dany obraz PIL na wszystkich stronach z daną wartością "pad" |
| Dopełnienie                 | >= 0     | Integer | 0       | Uzupełnienie dla każdego obramowania                   |
| Wahanie kolorów            | Dowolne     | Boolean | Fałsz   | Losowa zmiana jasności, kontrastu i nasycenia obrazu |
| Czarn               | Dowolne     | Boolean | Fałsz   | Konwertuj obraz na skalę odcieni szarooci               |
| Przycinanie losowe o zmienionym rozmiarze     | Dowolne     | Boolean | Fałsz   | Przytnij dany obraz PIL do wielkości losowej i współczynnika proporcji |
| Rozmiar losowy             | >= 1     | Integer | 256     | Oczekiwany rozmiar danych wyjściowych każdej krawędzi        |
| Uprawa Losowa             | Dowolne     | Boolean | Fałsz   | Przycinanie danego obrazu PIL w losowej lokalizacji |
| Rozmiar losowego przycinania        | >= 1     | Integer | 224     | Żądany rozmiar wyjściowy kadrowania          |
| Losowe Przerzucanie w poziomie  | Dowolne     | Boolean | Prawda    | W poziomie przerzucaj dany obraz PIL losowo z danym prawdopodobieństwem |
| Losowe Przerzucanie pionowe    | Dowolne     | Boolean | Fałsz   | Przerzucanie w pionie danego obrazu PIL losowo z danym prawdopodobieństwem |
| Obrót losowy         | Dowolne     | Boolean | Fałsz   | Obróć obraz o kąt                |
| Losowe stopnie rotacji | [0180] | Integer | 0       | Zakres stopni do wyboru          |
| Losowo afinicznym           | Dowolne     | Boolean | Fałsz   | Losowe przekształcenie afinicznym w centrum obrazu niezmiennej |
| Losowe afinicznym stopni   | [0180] | Integer | 0       | Zakres stopni do wyboru          |
| Losowa Skala odcieni szarooci        | Dowolne     | Boolean | Fałsz   | Losowa konwersja obrazu do skali odcieni szarości z prawdopodobieństwem 0,1 |
| Perspektywa Losowa      | Dowolne     | Boolean | Fałsz   | Wykonuje transformację perspektywy danego obrazu PIL losowo z prawdopodobieństwem 0,5 |
| Losowe wymazywanie          | Dowolne     | Boolean | Fałsz   | Losowo wybiera region prostokąta w obrazie i wymazuje jego piksele z prawdopodobieństwem 0,5 |

###  <a name="output"></a>Dane wyjściowe  

| Nazwa                        | Typ                    | Opis                              |
| --------------------------- | ----------------------- | ---------------------------------------- |
| Transformacja obrazu wyjściowego | TransformationDirectory | Transformacja obrazu wyjściowego, która może być połączona z **zastosowaniem modułu transformacji obrazu** . |

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 