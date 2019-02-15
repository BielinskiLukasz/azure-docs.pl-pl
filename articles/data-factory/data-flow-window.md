---
title: Mapowanie transformacji okna przepływu danych w usłudze Azure Data Factory
description: Mapowanie transformacji okna przepływu danych w usłudze Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 329125d39c0da403ff078182af2eee74bcd9c84d
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56272026"
---
# <a name="azure-data-factory-mapping-data-flow-window-transformation"></a>Mapowanie transformacji okna przepływu danych w usłudze Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Przekształcenie okna jest na tym, gdzie określi okienkowych agregacje kolumn w strumienie danych. W Konstruktorze wyrażeń można zdefiniować różne typy agregacji, które są oparte na danych lub czas systemu windows (klauzuli SQL OVER) takich jak potencjalny klient, opóźnienie, NTILE, CUMEDIST, RANGI itp.). Nowe pole zostanie wygenerowany w danych wyjściowych, który zawiera tych agregacji. Możesz również uwzględnić opcjonalne grupowania według pola.

![Opcje okna](media/data-flow/windows1.png "1 systemu windows")

## <a name="over"></a>Tryb failover
Ustaw, partycjonowanie danych w kolumnach dla swoją transformację okna. Jest to równoważne SQL ```Partition By``` w klauzuli Over w języku SQL. Jeśli chcesz utworzyć obliczenia lub wyrażenie służące do partycjonowania można to zrobić, ustawiając kursor nad nazwa kolumny i wybierz pozycję "kolumna obliczana".

![Opcje okna](media/data-flow/windows4.png "windows 4")

## <a name="sort"></a>Sortuj
Ustawia innej części klauzuli Over ```Order By```. Spowoduje to ustawienie kolejności sortowania danych. Wyrażenie wartości calculate można też utworzyć w tym pole kolumny sortowania.

![Opcje okna](media/data-flow/windows5.png "windows 5")

## <a name="range-by"></a>Zakres przez
Następnym etapem jest skonfigurowanie ramki okna jako niepowiązane lub powiązana. Aby ustawić ramki unbounded okna, ustaw suwak niepowiązane na obu końcach. Wybranie ustawienia między Unbounded i Current Row należy ustawić start przesunięcie i kończyć się wartości. Obie wartości będą dodatnie liczby całkowite. Względna numerów lub wartości można użyć z Twoich danych.

Suwak okno ma dwie wartości, aby ustawić: wartości przed bieżący wiersz i wartości po elemencie bieżącego wiersza. Przesunięcie początkowe i końcowe dopasowuje dwa selektory na suwaku.

![Opcje okna](media/data-flow/windows6.png "windows 6")

## <a name="window-columns"></a>Kolumny okna
Na koniec użyj Konstruktor wyrażeń do definiowania agregacji, którą chcesz nawiązać za pomocą danych systemu windows, takich jak RANGĘ, COUNT, MIN, MAX, GĘSTĄ RANGĘ, potencjalny klient, opóźnienie, itp.

![Opcje okna](media/data-flow/windows7.png "systemu windows 7")

Poniżej przedstawiono pełną listę agregacji i funkcje analitycznych, które są dostępne do użycia w ADF przepływu wyrażenie język danych za pomocą Kreatora wyrażeń: https://aka.ms/dataflowexpressions.

