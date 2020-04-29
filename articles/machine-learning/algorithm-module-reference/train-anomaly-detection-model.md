---
title: 'Model wykrywania anomalii szkolenia: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć szkolony model wykrywania anomalii przy użyciu modułu wykrywania anomalii w modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 78ed2d85729cce94e8dfa579545f558d2cfe4651
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502973"
---
# <a name="train-anomaly-detection-model"></a>Trenowanie modelu wykrywania anomalii

W tym artykule opisano, jak używać modułu **wykrywania anomalii** w programie Azure Machine Learning Designer (wersja zapoznawcza) w celu utworzenia przeszkolonego modelu wykrywania anomalii.

Moduł przyjmuje jako wejściowy zestaw parametrów modelu dla modelu wykrywania anomalii i nieoznaczonego zestawu danych. Zwraca on szkolony model wykrywania anomalii wraz z zestawem etykiet dla danych szkoleniowych.  

Aby uzyskać więcej informacji na temat algorytmów wykrywania anomalii w projektancie, zobacz następujące tematy: 

+ [Wykrywanie anomalii oparte na analizie głównych składowych](pca-based-anomaly-detection.md)  

## <a name="how-to-configure-train-anomaly-detection-model"></a>Jak skonfigurować model wykrywania anomalii dla uczenia 

1.  Dodaj moduł **wykrywania anomalii** do potoku w projektancie. Ten moduł można znaleźć w kategorii **wykrywania anomalii** .

2. Połącz jeden z modułów przeznaczonych do wykrywania anomalii, takich jak [wykrywanie anomalii oparte na UPW](pca-based-anomaly-detection.md)

    Inne typy modeli nie są obsługiwane; przy uruchamianiu potoku zostanie wyświetlony błąd: wszystkie modele muszą mieć ten sam typ informacji.  

3.  Skonfiguruj moduł wykrywania anomalii, wybierając kolumnę etykieta i ustawiając inne parametry specyficzne dla algorytmu.  

4.  Dołączanie zestawu danych szkoleniowych do prawego wejścia do **modelu wykrywania anomalii w szkoleniu**.  

5.  Prześlij potok.  

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby wyświetlić parametry modelu, kliknij prawym przyciskiem myszy moduł i wybierz polecenie **Wizualizuj**. 

+ Aby utworzyć prognozy, użyj [modelu oceny](score-model.md) z nowymi danymi wejściowymi.

+ Aby zapisać migawkę przeszkolonego modelu, wybierz moduł, a następnie kliknij ikonę **zarejestruj zestaw danych** na karcie dane **wyjściowe + dzienniki** na prawym panelu.   

 
## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 

Zobacz [wyjątki i kody błędów dla projektanta (wersja zapoznawcza)](designer-error-codes.md) , aby zapoznać się z listą błędów specyficznych dla modułów projektanta.
'