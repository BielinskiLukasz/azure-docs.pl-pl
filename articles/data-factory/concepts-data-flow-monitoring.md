---
title: Monitorowanie wizualizacji przepływu danych Azure Data Factory mapowania
description: Jak wizualnie monitorować Azure Data Factory przepływy danych
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 5d69e29c83bcbe433b800d6877ba1c7440eceedc
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387989"
---
# <a name="monitor-data-flows"></a>Monitorowanie przepływów danych



Po zakończeniu kompilowania i debugowania przepływu danych należy zaplanować przepływ danych zgodnie z harmonogramem w kontekście potoku. Potoku można zaplanować z Azure Data Factory przy użyciu wyzwalaczy. Można też użyć opcji Wyzwól teraz z konstruktora potoku Azure Data Factory, aby wykonać pojedyncze uruchomienie w celu przetestowania przepływu danych w kontekście potoku.

Podczas wykonywania potoku będzie można monitorować potok i wszystkie działania zawarte w potoku, w tym działanie przepływu danych. Kliknij ikonę monitora w okienku interfejsu użytkownika z lewej strony Azure Data Factory. Zobaczysz ekran podobny do przedstawionego poniżej. Wyróżnione ikony umożliwią przechodzenie do działań w potoku, w tym działania przepływu danych.

![Monitorowanie przepływu danych](media/data-flow/mon001.png "Monitorowanie przepływu danych")

Na tym poziomie będą widoczne statystyki, a także czasy i Stany uruchomienia. Identyfikator przebiegu na poziomie działania różni się od identyfikatora przebiegu na poziomie potoku. Identyfikator uruchomienia na poprzednim poziomie dotyczy potoku. Kliknięcie okularów przekaże szczegółowe informacje o wykonywaniu przepływu danych.

![Monitorowanie przepływu danych](media/data-flow/mon002.png "Monitorowanie przepływu danych")

Gdy jesteś w widoku monitorowanie węzła graficznego, zobaczysz uproszczoną wersję widoku wykresu przepływu danych.

![Monitorowanie przepływu danych](media/data-flow/mon003.png "Monitorowanie przepływu danych")

## <a name="view-data-flow-execution-plans"></a>Wyświetlanie planów wykonywania przepływu danych

Gdy przepływ danych jest wykonywany w platformie Spark, Azure Data Factory określa optymalne ścieżki kodu na podstawie całości przepływu danych. Ponadto ścieżki wykonywania mogą wystąpić w różnych węzłach skalowalnych w poziomie i partycjach danych. W związku z tym wykres monitorowania reprezentuje projekt przepływu, biorąc pod uwagę ścieżkę wykonywania transformacji. Po kliknięciu poszczególnych węzłów zobaczysz "grupowania", które reprezentują kod, który został wykonany razem w klastrze. Czasy i liczby, które zobaczysz, reprezentują te grupy, w przeciwieństwie do poszczególnych kroków w projekcie.

![Monitorowanie przepływu danych](media/data-flow/mon004.png "Monitorowanie przepływu danych")

* Po kliknięciu obszaru Otwórz w oknie monitorowanie, statystyki w dolnym okienku będą wyświetlały chronometraż i liczby wierszy dla każdego ujścia oraz przekształcenia, które doprowadziły do danych ujścia na potrzeby transformacji.

* Po wybraniu poszczególnych przekształceń otrzymasz dodatkową opinię na temat panelu po prawej stronie, w którym znajdują się statystyki partycji, liczby kolumn, skośność (jak równomiernie są dystrybuowane dane między partycjami) i kurtoza (jak są to dane).

* Po kliknięciu w obszarze zlewu w widoku węzła zostanie wyświetlona kolumna elementy zależne. Istnieją trzy różne metody, w których kolumny są gromadzone w całym przepływie danych pod kątem terenu. Oto one:

  * Obliczono: używasz kolumny na potrzeby przetwarzania warunkowego lub w obrębie wyrażenia w przepływie danych, ale nie należy go wystawić w ujścia
  * Pochodny: kolumna jest nową kolumną wygenerowaną w przepływie, tj. nie znajdowała się w źródle
  * Zamapowana: kolumna pochodzi ze źródła i mapuje ją do pola ujścia
  
## <a name="monitor-icons"></a>Monitoruj ikony

Ta ikona oznacza, że dane przekształcenia zostały już zapisane w pamięci podręcznej, więc czasy i ścieżki wykonywania zostały wzięte pod uwagę:

![Monitorowanie przepływu danych](media/data-flow/mon004.png "Monitorowanie przepływu danych")

W transformację zobaczysz również zielone ikony koła. Reprezentują one liczbę zlewów, do których przepływa dane.
