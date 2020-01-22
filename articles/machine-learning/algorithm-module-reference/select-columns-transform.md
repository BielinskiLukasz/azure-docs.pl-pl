---
title: 'Wybierz kolumny Przekształć: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu przekształcanie kolumn w Azure Machine Learning, aby utworzyć transformację, która wybiera ten sam podzestaw kolumn jak w danym zestawie danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: c8d58180b11c12afb256dc888406c7c0d58fb119
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314321"
---
# <a name="select-columns-transform"></a>Wybieranie przekształcenia kolumn

W tym artykule opisano sposób korzystania z modułu SELECT Columns Transform w programie Azure Machine Learning Designer. Celem modułu SELECT Columns Transform jest upewnienie się, że przewidywalna spójny zestaw kolumn jest używany w operacjach uczenia maszynowego.

Ten moduł jest pomocny w przypadku zadań, takich jak ocenianie, które wymagają określonych kolumn. Zmiany w dostępnych kolumnach mogą spowodować przerwanie potoku lub zmianę wyników.

Użyj opcji Przekształć kolumny do, aby utworzyć i zapisać zestaw kolumn. Następnie użyj modułu [zastosowania przekształcenia](apply-transformation.md) , aby zastosować te wybory do nowych danych.

## <a name="how-to-use-select-columns-transform"></a>Jak używać transformacji SELECT Columns

W tym scenariuszu przyjęto założenie, że chcesz użyć wyboru funkcji do wygenerowania dynamicznego zestawu kolumn, który będzie używany do uczenia modelu. Aby upewnić się, że wybory kolumn są takie same dla procesu oceniania, należy użyć modułu przekształcanie kolumn w celu przechwycenia wybranych kolumn i zastosowania ich w innym miejscu potoku.

1. Dodaj wejściowy zestaw danych do potoku w projektancie.

2. Dodaj wystąpienie [wyboru funkcji opartej na filtrze](filter-based-feature-selection.md).

3. Połącz moduły i skonfiguruj Moduł wyboru funkcji, aby automatycznie znajdował szereg najlepszych funkcji w wejściowym zestawie danych.

4. Dodaj wystąpienie [modelu uczenia](train-model.md) i Użyj danych wyjściowych [wyboru funkcji opartych na filtrowaniu](filter-based-feature-selection.md) jako danych wejściowych do szkoleń.

    > [!IMPORTANT]
    > Ponieważ ważność funkcji opiera się na wartościach w kolumnie, nie można już wiedzieć, które kolumny mogą być dostępne dla danych wejściowych do [modelu uczenia](train-model.md).  
5. Dołącz wystąpienie modułu SELECT Columns Transform. 

    Ten krok powoduje wygenerowanie zaznaczenia kolumny jako przekształcenia, która może zostać zapisana lub zastosowana do innych zestawów danych. Ten krok zapewnia, że kolumny zidentyfikowane w wyborze funkcji zostaną zapisane dla innych modułów do ponownego użycia.

6. Dodaj moduł [modelu wyników](score-model.md) . 

   *Nie łącz wejściowego zestawu danych.* Zamiast tego należy dodać moduł [przekształcenia](apply-transformation.md) i połączyć dane wyjściowe transformacji wyboru funkcji.

   > [!IMPORTANT]
   > Nie można oczekiwać, aby zastosować [wybór funkcji oparty na filtrach](filter-based-feature-selection.md) do zestawu danych oceniania i uzyskać te same wyniki. Ponieważ wybór funkcji jest oparty na wartościach, może wybrać inny zestaw kolumn, co spowoduje niepowodzenie operacji oceniania.
7. Uruchamianie potoku.

Ten proces zapisywania i stosowania wybranej kolumny zapewnia, że ten sam schemat danych jest dostępny do szkolenia i oceniania.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
