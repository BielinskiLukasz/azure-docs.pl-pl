---
title: Arkusz Ściągawka algorytmu Machine Learning
titleSuffix: Azure Machine Learning
description: Arkusz Ściągawkaego algorytmu Machine Learningowego umożliwia wybranie odpowiedniego algorytmu dla modelu predykcyjnego w programie Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 11/04/2019
ms.openlocfilehash: 10714e155bebdd7acc5d7febb345c17fa81bab13
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516294"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Arkusz Machine Learning Ściągawka algorytmu dla projektanta Azure Machine Learning

**Arkusz Azure Machine Learning algorytm Ściągawka** umożliwia wybranie odpowiedniego algorytmu dla modelu analizy predykcyjnej.

Azure Machine Learning ma dużą bibliotekę algorytmów z ***klasyfikacji***, ***systemów zalecanych***, ***klastrowania***, ***wykrywania anomalii***, ***regresji*** i ***analizy tekstu*** . Każdy z nich został zaprojektowany w celu rozwiązania innego typu problemu uczenia maszynowego.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Pobierz: Ściągawka algorytm Machine Learning arkusz

**Pobierz arkusz Ściągawka tutaj: [Machine Learning algorytm Ściągawka arkusz (11x17 cali)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![Arkusz Machine Learning algorytm Ściągawka: informacje na temat wybierania algorytmu Machine Learning.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.svg)

Pobierz i wydrukuj arkusz Machine Learning algorytm Ściągawka w rozmiarze Tabloid, aby zapewnić jego użyteczność i uzyskać pomoc w wyborze algorytmu.

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>Jak korzystać z arkusza Ściągawka algorytmu Machine Learning

Sugestie oferowane przez ten algorytm Ściągawka arkusz są przybliżonymi regułami przewijania. Niektóre mogą być wygięte, a niektóre z nich mogą zostać naruszone. Jest to przeznaczone do sugerowania punktu początkowego. Nie należy boisz do uruchamiania konkursów między kilkoma algorytmami danych. Nie jest to po prostu podstawą do poznania zasad poszczególnych algorytmów i systemu, który wygenerował dane.

Każdy algorytm uczenia maszynowego ma własny styl lub odchylenia indukcyjne. W przypadku konkretnego problemu mogą być odpowiednie różne algorytmy, a jeden algorytm może być lepszym rozwiązaniem niż inne. Ale nie zawsze jest możliwe, aby wcześniej wiedzieć, co jest najlepszym rozwiązaniem. W takich przypadkach kilka algorytmów jest wymienionych razem w arkuszu Ściągawka. Odpowiednią strategią jest wypróbowanie jednego algorytmu, a jeśli wyniki nie są jeszcze zadowalające, wypróbuj inne. 

Aby dowiedzieć się więcej na temat algorytmów w Azure Machine Learning, przejdź do [algorytmu i odwołania do modułu](algorithm-module-reference/module-reference.md).

## <a name="kinds-of-machine-learning"></a>Rodzaje uczenia maszynowego

Istnieją trzy główne kategorie uczenia maszynowego: *uczenie nadzorowane*, *nienadzorowane uczenie*się i *wzmacnianie*.

### <a name="supervised-learning"></a>Uczenie nadzorowane

W uczeniu nadzorowanym każdy punkt danych ma etykietę lub jest skojarzony z kategorią lub wartością zainteresowania. Przykładem etykiety kategorii jest przypisanie obrazu jako "Cat" lub "Dog". Przykładem etykiety wartości jest cena sprzedaży skojarzona z używanym samochodem. Celem szkolenia nadzorowanego jest zbadanie wielu przykładów z etykietami, takich jak te, a następnie umożliwienie przewidywania przyszłych punktów danych. Na przykład identyfikacja nowych zdjęć z właściwym zwierzęciem lub przypisanie dokładnych cen sprzedaży do innych używanych samochodów. Jest to popularny i przydatny typ uczenia maszynowego.

### <a name="unsupervised-learning"></a>Uczenie nienadzorowane

W przypadku nienadzorowanej uczenia punkty danych nie mają skojarzonych z nimi etykiet. Zamiast tego celem nienadzorowanego algorytmu uczenia jest organizowanie danych w jakiś sposób lub opisywanie jej struktury. Może to oznaczać, że jest to zgrupowanie do klastrów, jako K-oznacza lub znalezienie różnych sposobów na spojrzenie na złożone dane, tak aby były prostsze.

### <a name="reinforcement-learning"></a>Uczenie przez wzmacnianie

W nauce wzmacniania algorytm pozwala wybrać akcję w odpowiedzi na każdy punkt danych. Jest to typowy sposób w przypadku, gdy zestaw odczytów czujników w jednym punkcie czasu jest punktem danych, a algorytm musi wybrać następną akcję robota. Jest to również naturalna dopasowanie do Internet rzeczy aplikacji. Algorytm uczenia również otrzymuje sygnał zarobku krótki czas później, wskazując na to, jak jest to dobre. Na podstawie tego algorytm modyfikuje swoją strategię w celu osiągnięcia najwyższej nagrody. 

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej na temat Studio w Azure Machine Learning i Azure Portal](service/overview-what-is-azure-ml.md).

* Zobacz listę algorytmów i modułów w [Kompendium algorytmu i modułu](algorithm-module-reference/module-reference.md).

* [Samouczek: Tworzenie modelu predykcyjnego w programie Azure Machine Learning Designer](service/ui-tutorial-automobile-price-train-score.md).

* [Poznaj uczenie głębokie i uczenie maszynowe](service/concept-deep-learning-vs-machine-learning.md).
