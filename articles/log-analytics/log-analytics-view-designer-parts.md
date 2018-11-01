---
title: Podręcznik informacyjny, do części projektanta widoku w usłudze Azure Log Analytics | Dokumentacja firmy Microsoft
description: Przy użyciu projektanta widoku w usłudze Log Analytics, można tworzyć widoki niestandardowe w witrynie Azure portal, który wyświetlane różne wizualizacje danych w obszarze roboczym usługi Log Analytics. Ten artykuł jest przewodnik odwołanie do ustawienia dla części wizualizacji, które są dostępne w widoki niestandardowe.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 5718d620-b96e-4d33-8616-e127ee9379c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 31ee14369cc3f1c03d13c59e1239aa567bce3552
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419779"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-log-analytics"></a>Podręcznik informacyjny do części wizualizacji projektanta widoków w usłudze Log Analytics
Przy użyciu projektanta widoku w usłudze Azure Log Analytics, można tworzyć widoki niestandardowe w witrynie Azure portal, które przedstawiają różne wizualizacje danych ze swojego obszaru roboczego usługi Log Analytics. Ten artykuł jest przewodnik odwołanie do ustawienia dla części wizualizacji, które są dostępne w widoki niestandardowe.

Aby uzyskać więcej informacji dotyczących projektanta widoków zobacz:

* [Wyświetl projektanta](log-analytics-view-designer.md): zawiera omówienie Projektant widoków i procedur tworzenia i edytowania widoków niestandardowych.
* [Kafelek odwołanie](log-analytics-view-designer-tiles.md): zawiera odwołanie do ustawienia dla każdego kafelka dostępne widoki niestandardowe.


W poniższej tabeli opisano dostępne typy kafelka projektanta widoków:

| Typ widoku | Opis |
|:--- |:--- |
| [Lista zapytań](#list-of-queries-part) |Wyświetla listę zapytań funkcji przeszukiwania dzienników. Możesz wybrać każdej kwerendy, aby wyświetlić wyniki. |
| [Numer i lista](#number-and-list-part) |Nagłówek przedstawia jeden numer, który pokazuje liczbę rekordów z zapytanie wyszukiwania w dzienniku. Lista zawiera najważniejsze dziesięć wyników zapytania, przy użyciu programu graph, która wskazuje względną wartość kolumny liczbowej lub jego zmiana, wraz z upływem czasu. |
| [Dwie liczby i lista](#two-numbers-and-list-part) |Nagłówek przedstawia dwie liczby, przedstawiających liczby rekordów z oddzielnych dziennikach zapytania wyszukiwania. Lista zawiera najważniejsze dziesięć wyników zapytania, przy użyciu programu graph, która wskazuje względną wartość kolumny liczbowej lub jego zmiana, wraz z upływem czasu. |
| [Wykres pierścieniowy i lista](#donut-and-list-part) |Nagłówek przedstawia jeden numer, który podsumowuje kolumnę wartości w zapytaniu dziennika. Wykres pierścieniowy wyświetla w postaci graficznej wyniki pierwszych trzech rekordów. |
| [Dwie osie czasu i lista](#two-timelines-and-list-part) |Nagłówek przedstawia wyniki dwóch zapytań log wraz z upływem czasu jako wykresy kolumnowe, z objaśnieniem, który wyświetla jeden numer, który podsumowuje kolumnę wartości w zapytaniu dziennika. Lista zawiera najważniejsze dziesięć wyników zapytania, przy użyciu programu graph, która wskazuje względną wartość kolumny liczbowej lub jego zmiana, wraz z upływem czasu. |
| [Informacje o](#information-part) |Nagłówek Wyświetla tekst statyczny oraz opcjonalnie łącza. Zostanie wyświetlona lista jednego lub więcej elementów z tytułu statycznych i tekst. |
| [Wykres liniowy, objaśnienie i lista](#line-chart-callout-and-list-part) |Nagłówek przedstawia wykres liniowy z wielu serii, w wyniku zapytania dziennika przestrzeni czasu i objaśnienie przy użyciu wartości podsumowania. Lista zawiera najważniejsze dziesięć wyników zapytania, przy użyciu programu graph, która wskazuje względną wartość kolumny liczbowej lub jego zmiana, wraz z upływem czasu. |
| [Wykres liniowy i lista](#line-chart-and-list-part) |Nagłówek przedstawia wykres liniowy z wielu serii, w wyniku zapytania dziennika wraz z upływem czasu. Lista zawiera najważniejsze dziesięć wyników zapytania, przy użyciu programu graph, która wskazuje względną wartość kolumny liczbowej lub jego zmiana, wraz z upływem czasu. |
| [Stos części wykresy wiersza](#stack-of-line-charts-part) |Przedstawia trzy oddzielne wykresy, przy użyciu wielu serii, w wyniku zapytania dziennika wraz z upływem czasu. |

W kolejnych sekcjach opisano typy kafelków i ich właściwości szczegółowo.

## <a name="list-of-queries-part"></a>Listę części zapytania
Listy zapytań część Wyświetla listę zapytań funkcji przeszukiwania dzienników. Możesz wybrać każdej kwerendy, aby wyświetlić wyniki. Domyślnie widok zawiera jedno zapytanie i można wybrać **+ zapytania** można dodać dodatkowe zapytania.

![Lista widoku zapytań](media/log-analytics-view-designer-parts/view-list-queries.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Stanowisko |Tekst, który jest wyświetlany u góry widoku. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, począwszy od bieżącego widoku. |
| Wstępnie wybrane filtry |Rozdzielana przecinkami lista właściwości, które mają zostać objęte okienko filtru po lewej stronie, po wybraniu zapytania. |
| Tryb renderowania |Widok początkowy jest wyświetlane, gdy zostanie wybrane zapytanie. Po otwarciu kwerendy, można wybrać żadnych dostępnych widoków. |
| **Zapytania** | |
| Zapytanie wyszukiwania |Zapytanie do uruchomienia. |
| Przyjazna nazwa | Opisowa nazwa, która jest wyświetlana. |

## <a name="number-and-list-part"></a>Część liczby i lista
Nagłówek przedstawia jeden numer, który pokazuje liczbę rekordów z zapytanie wyszukiwania w dzienniku. Lista zawiera najważniejsze dziesięć wyników zapytania, przy użyciu programu graph, która wskazuje względną wartość kolumny liczbowej lub jego zmiana, wraz z upływem czasu.

![Lista widoku zapytań](media/log-analytics-view-designer-parts/view-number-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst, który jest wyświetlany u góry widoku. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, począwszy od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wynik w nagłówku. |
| Ikona użycia |Wybierz ten link, aby wyświetlić ikonę. |
| **Tytuł** | |
| Legenda |Tekst, który jest wyświetlany w górnej części nagłówka. |
| Zapytanie |Zapytanie do uruchamiania dla nagłówka. Zostanie wyświetlona liczba rekordów, które są zwracane przez zapytanie. |
| Nawigacja za pomocą kliknięć | Działania wykonywane po kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Lista** | |
| Zapytanie |Zapytanie do uruchamiania na liście. Pierwsze dwie właściwości dla pierwszych dziesięciu rekordów w wynikach są wyświetlane. Pierwsza właściwość jest wartością tekstową i drugą właściwość jest wartością liczbową. Paski są tworzone automatycznie, które są oparte na względnej wartości kolumny liczbowej.<br><br>Użyj `Sort` polecenia w zapytaniu, aby sortować rekordy na liście. Aby uruchomić zapytanie i zwraca wszystkie rekordy, można wybrać **holograficznych**. |
| Ukryj graf |Wybierz ten link, aby wyłączyć wykres z prawej strony kolumny liczbowej. |
| Włącz wykresy przebiegu w czasie |Wybierz ten link, aby wyświetlić wykres przebiegu w czasie zamiast poziomy pasek. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kolor |Kolor słupków lub wykresy przebiegu w czasie. |
| Separator nazw i wartości |Ogranicznik pojedynczych znaków, które można użyć do analizowania właściwość text na wiele wartości. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Nawigacja za pomocą kliknięć | Działania wykonywane po kliknięciu elementu na liście.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Lista** |**> Tytuły kolumn** |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst, który jest wyświetlany w górnej części drugiej kolumny. |
| **Lista** |**> Progi** |
| Włącz progi |Wybierz ten link, aby umożliwić progów. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Dwie liczby i części listy
Nagłówek ma dwie liczby, które wyświetla liczbę rekordów niż zapytania wyszukiwania w oddzielnych dziennikach. Lista zawiera najważniejsze dziesięć wyników zapytania, przy użyciu programu graph, która wskazuje względną wartość kolumny liczbowej lub jego zmiana, wraz z upływem czasu.

![Dwie liczby i widok listy](media/log-analytics-view-designer-parts/view-two-numbers-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst, który jest wyświetlany u góry widoku. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, począwszy od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wynik w nagłówku. |
| Ikona użycia |Wybierz ten link, aby wyświetlić ikonę. |
| **Tytuł nawigacji** | |
| Nawigacja za pomocą kliknięć | Działania wykonywane po kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Tytuł** | |
| Legenda |Tekst, który jest wyświetlany w górnej części nagłówka. |
| Zapytanie |Zapytanie do uruchamiania dla nagłówka. Zostanie wyświetlona liczba rekordów, które są zwracane przez zapytanie. |
| **Lista** | |
| Zapytanie |Zapytanie do uruchamiania na liście. Pierwsze dwie właściwości dla pierwszych dziesięciu rekordów w wynikach są wyświetlane. Pierwsza właściwość jest wartością tekstową i drugą właściwość jest wartością liczbową. Paski są tworzone automatycznie w oparciu o względnej wartości kolumny liczbowej.<br><br>Użyj `Sort` polecenia w zapytaniu, aby sortować rekordy na liście. Aby uruchomić zapytanie i zwraca wszystkie rekordy, można wybrać **holograficznych**. |
| Ukryj graf |Wybierz ten link, aby wyłączyć wykres z prawej strony kolumny liczbowej. |
| Włącz wykresy przebiegu w czasie |Wybierz ten link, aby wyświetlić wykres przebiegu w czasie zamiast poziomy pasek. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kolor |Kolor słupków lub wykresy przebiegu w czasie. |
| Operacja |Operacja do wykonania dla wykresu przebiegu w czasie. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Separator nazw i wartości |Ogranicznik pojedynczych znaków, które można użyć do analizowania właściwość text na wiele wartości. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Nawigacja za pomocą kliknięć | Działania wykonywane po kliknięciu elementu na liście.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Lista** |**> Tytuły kolumn** |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst, który jest wyświetlany w górnej części drugiej kolumny. |
| **Lista** |**> Progi** |
| Włącz progi |Wybierz ten link, aby umożliwić progów. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#thresholds). |

## <a name="donut-and-list-part"></a>Część wykres pierścieniowy i lista
Nagłówek przedstawia jeden numer, który podsumowuje kolumnę wartości w zapytaniu dziennika. Wykres pierścieniowy wyświetla w postaci graficznej wyniki pierwszych trzech rekordów.

![Wyświetl wykres pierścieniowy i lista](media/log-analytics-view-designer-parts/view-donut-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst, który jest wyświetlany w górnej części kafelka. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, począwszy od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wynik w nagłówku. |
| Ikona użycia |Wybierz ten link, aby wyświetlić ikonę. |
| **Nagłówek** | |
| Stanowisko |Tekst, który jest wyświetlany w górnej części nagłówka. |
| Podtytuł |Tekst, który jest wyświetlany w obszarze tytułu w górnej części nagłówka. |
| **Wykres pierścieniowy** | |
| Zapytanie |Zapytanie do uruchamiania na wykres pierścieniowy. Pierwsza właściwość jest wartością tekstową i drugą właściwość jest wartością liczbową. |
| Nawigacja za pomocą kliknięć | Działania wykonywane po kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Wykres pierścieniowy** |**> Center** |
| Tekst |Tekst, który jest wyświetlany w polu wartość w wykres pierścieniowy. |
| Operacja |Operacja do wykonania na właściwości value, aby podsumować go jako pojedyncza wartość.<ul><li>Suma: Dodaje wartości wszystkich rekordów.</li><li>Wartość procentowa: Stosunek rekordów zwróconych przez wartości w **wyniku wartości używane w środku** łączna liczba rekordów w zapytaniu.</li></ul> |
| Wartości wynikowe używane w środku |Opcjonalnie wybierz znak plus (+), aby dodać co najmniej jedną wartość. Wyniki zapytania są ograniczone do rekordów za pomocą wartości właściwości, które określisz. Jeśli żadne wartości nie zostaną dodane, wszystkie rekordy zostaną uwzględnione w zapytaniu. |
| **Dodatkowe opcje** |**> Kolory** |
| Kolor 1<br>Kolor 2<br>Kolor 3 |Wybierz kolor dla każdej wartości, które są wyświetlane w wykres pierścieniowy. |
| **Dodatkowe opcje** |**> Zaawansowane mapowanie kolorów** |
| Wartość pola |Wpisz nazwę pola do wyświetlenia w innym kolorze, jeśli jest on zawarty w wykres pierścieniowy. |
| Kolor |Wybierz kolor unikatowe pola. |
| **Lista** | |
| Zapytanie |Zapytanie do uruchamiania na liście. Zostanie wyświetlona liczba rekordów, które są zwracane przez zapytanie. |
| Ukryj graf |Wybierz ten link, aby wyłączyć wykres z prawej strony kolumny liczbowej. |
| Włącz wykresy przebiegu w czasie |Wybierz ten link, aby wyświetlić wykres przebiegu w czasie zamiast poziomy pasek. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kolor |Kolor słupków lub wykresy przebiegu w czasie. |
| Operacja |Operacja do wykonania dla wykresu przebiegu w czasie. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Separator nazw i wartości |Ogranicznik pojedynczych znaków, które można użyć do analizowania właściwość text na wiele wartości. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Nawigacja za pomocą kliknięć | Działania wykonywane po kliknięciu elementu na liście.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Lista** |**> Tytuły kolumn** |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst, który jest wyświetlany w górnej części drugiej kolumny. |
| **Lista** |**> Progi** |
| Włącz progi |Wybierz ten link, aby umożliwić progów. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Dwie osie czasu i Lista części
Nagłówek przedstawia wyniki dwóch zapytań log wraz z upływem czasu jako wykresy kolumnowe, z objaśnieniem, który wyświetla jeden numer, który podsumowuje kolumnę wartości w zapytaniu dziennika. Lista zawiera najważniejsze dziesięć wyników zapytania, przy użyciu programu graph, która wskazuje względną wartość kolumny liczbowej lub jego zmiana, wraz z upływem czasu.

![Wyświetl dwie osie czasu i lista](media/log-analytics-view-designer-parts/view-two-timelines-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst, który jest wyświetlany w górnej części kafelka. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, począwszy od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wynik w nagłówku. |
| Ikona użycia |Wybierz ten link, aby wyświetlić ikonę. |
| **Tytuł nawigacji** | |
| Nawigacja za pomocą kliknięć | Działania wykonywane po kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Najpierw wykresu<br>drugi wykres** | |
| Legenda |Tekst, który jest wyświetlany w polu objaśnienia dla pierwszej serii. |
| Kolor |Kolor do używania dla kolumn w tej serii. |
| Zapytanie |Zapytanie do uruchamiania dla pierwszej serii. Liczba rekordów w poszczególnych przedziałach czasu jest reprezentowany przez kolumn wykresu. |
| Operacja |Operacja do wykonania na właściwości value, aby podsumować go jako pojedyncze wartości objaśnienia.<ul><li>Suma: Suma wartości wszystkich rekordów.</li><li>Średnia: Średniej wartości wszystkich rekordów.</li><li>Ostatnie przykładowy: wartość od ostatniego interwału, który znajduje się na wykresie.</li><li>Najpierw przykładowy: wartość od pierwszego interwału, który znajduje się na wykresie.</li><li>Liczba: Liczba wszystkie rekordy, które są zwracane przez zapytanie.</li></ul> |
| **Lista** | |
| Zapytanie |Zapytanie do uruchamiania na liście. Zostanie wyświetlona liczba rekordów, które są zwracane przez zapytanie. |
| Ukryj graf |Wybierz ten link, aby wyłączyć wykres z prawej strony kolumny liczbowej. |
| Włącz wykresy przebiegu w czasie |Wybierz ten link, aby wyświetlić wykres przebiegu w czasie zamiast poziomy pasek. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kolor |Kolor słupków lub wykresy przebiegu w czasie. |
| Operacja |Operacja do wykonania dla wykresu przebiegu w czasie. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Nawigacja za pomocą kliknięć | Działania wykonywane po kliknięciu elementu na liście.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Lista** |**> Tytuły kolumn** |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst, który jest wyświetlany w górnej części drugiej kolumny. |
| **Lista** |**> Progi** |
| Włącz progi |Wybierz ten link, aby umożliwić progów. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#thresholds). |

## <a name="information-part"></a>Część informacji
Nagłówek Wyświetla tekst statyczny oraz opcjonalnie łącza. Zostanie wyświetlona lista jednego lub więcej elementów z tytułu statycznych i tekst.

![Wyświetlanie informacji](media/log-analytics-view-designer-parts/view-information.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst, który jest wyświetlany w górnej części kafelka. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, począwszy od bieżącego widoku. |
| Kolor |Kolor tła dla nagłówka. |
| **Nagłówek** | |
| Image (Obraz) |Plik obrazu, który jest wyświetlany w nagłówku. |
| Etykieta |Tekst, który jest wyświetlany w nagłówku. |
| **Nagłówek** |**> Link** |
| Etykieta |Tekst łącza. |
| Url |Adres Url dla tego połączenia. |
| **Elementy informacji** | |
| Stanowisko |Tekst, który jest wyświetlany jako tytuł każdego elementu. |
| Zawartość |Tekst, który jest wyświetlany dla każdego elementu. |

## <a name="line-chart-callout-and-list-part"></a>Wykres liniowy, objaśnienie i listy
Nagłówek przedstawia wykres liniowy z wielu serii, w wyniku zapytania dziennika przestrzeni czasu i objaśnienie przy użyciu wartości podsumowania. Lista zawiera najważniejsze dziesięć wyników zapytania, przy użyciu programu graph, która wskazuje względną wartość kolumny liczbowej lub jego zmiana, wraz z upływem czasu.

![Wykres liniowy, objaśnienie i widok listy](media/log-analytics-view-designer-parts/view-line-chart-callout-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst, który jest wyświetlany w górnej części kafelka. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, począwszy od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wynik w nagłówku. |
| Ikona użycia |Wybierz ten link, aby wyświetlić ikonę. |
| **Nagłówek** | |
| Stanowisko |Tekst, który jest wyświetlany w górnej części nagłówka. |
| Podtytuł |Tekst, który jest wyświetlany w obszarze tytułu w górnej części nagłówka. |
| **Wykres liniowy** | |
| Zapytanie |Zapytanie, aby uruchomić wykresu liniowego. Pierwsza właściwość jest wartością tekstową i drugą właściwość jest wartością liczbową. To zapytanie jest zazwyczaj używa *miary* — słowo kluczowe do podsumowania wyników. Jeśli zapytanie używa *interwał* — słowo kluczowe, osi x wykresu używa tego przedziału czasu. Jeśli zapytanie nie obejmuje *interwał* — słowo kluczowe, odstępów godzinowych używa osi x. |
| Nawigacja za pomocą kliknięć | Działania wykonywane po kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Wykres liniowy** |**> Objaśnienie** |
| Tytuł objaśnienia |Tekst, który jest wyświetlany powyżej wartości objaśnienia. |
| Nazwa serii. |Wartość właściwości serii, aby użyć wartości objaśnienia. Jeśli seria nie zostanie podany, używane są wszystkie rekordy z zapytania. |
| Operacja |Operacja do wykonania na właściwości value, aby podsumować go jako pojedyncze wartości objaśnienia.<ul><li>Średnia: Średniej wartości wszystkich rekordów.</li><li>Liczba: Liczba wszystkie rekordy, które są zwracane przez zapytanie.</li><li>Ostatnie przykładowy: wartość od ostatniego interwału, który znajduje się na wykresie.</li><li>Maks.: Maksymalną wartość z interwałów, które znajdują się na wykresie.</li><li>Minimalna: Wartość minimalna z interwałów, które znajdują się na wykresie.</li><li>Suma: Suma wartości wszystkich rekordów.</li></ul> |
| **Wykres liniowy** |**> Oś y** |
| Użyj skali logarytmicznej |Wybierz ten link, aby użyć skali logarytmicznej dla osi y. |
| Jednostki |Określ jednostki dla wartości, które mają zostać zwrócone przez zapytanie. Te informacje są używane do wyświetlania etykiet wykresu, wskazujące typy wartości i, opcjonalnie, aby konwertować wartości. *Jednostki* typ Określa kategorię jednostki i definiuje dostępnych *bieżącej jednostki* wpisz wartości. Jeśli zostanie wybrana wartość w *przekonwertować*, wartości numeryczne są konwertowane z *bieżącej jednostki* typ *przekonwertować* typu. |
| Etykieta niestandardowa |Tekst, który jest wyświetlany na osi y obok etykiety *jednostki* typu. Jeśli żadna etykieta nie zostanie określona, tylko *jednostki* typ jest wyświetlany. |
| **Lista** | |
| Zapytanie |Zapytanie do uruchamiania na liście. Zostanie wyświetlona liczba rekordów, które są zwracane przez zapytanie. |
| Ukryj graf |Wybierz ten link, aby wyłączyć wykres z prawej strony kolumny liczbowej. |
| Włącz wykresy przebiegu w czasie |Wybierz ten link, aby wyświetlić wykres przebiegu w czasie zamiast poziomy pasek. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kolor |Kolor słupków lub wykresy przebiegu w czasie. |
| Operacja |Operacja do wykonania dla wykresu przebiegu w czasie. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Separator nazw i wartości |Ogranicznik pojedynczych znaków, które można użyć do analizowania właściwość text na wiele wartości. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Nawigacja za pomocą kliknięć | Działania wykonywane po kliknięciu elementu na liście.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Lista** |**> Tytuły kolumn** |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst, który jest wyświetlany w górnej części drugiej kolumny. |
| **Lista** |**> Progi** |
| Włącz progi |Wybierz ten link, aby umożliwić progów. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#thresholds). |

## <a name="line-chart-and-list-part"></a>Część wiersza wykresu i listy
Nagłówek przedstawia wykres liniowy z wielu serii, w wyniku zapytania dziennika wraz z upływem czasu. Lista zawiera najważniejsze dziesięć wyników zapytania, przy użyciu programu graph, która wskazuje względną wartość kolumny liczbowej lub jego zmiana, wraz z upływem czasu.

![Widok wykresu i listy wiersza](media/log-analytics-view-designer-parts/view-line-chart-callout-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst, który jest wyświetlany w górnej części kafelka. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, począwszy od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wynik w nagłówku. |
| Ikona użycia |Wybierz ten link, aby wyświetlić ikonę. |
| **Nagłówek** | |
| Stanowisko |Tekst, który jest wyświetlany w górnej części nagłówka. |
| Podtytuł |Tekst, który jest wyświetlany w obszarze tytułu w górnej części nagłówka. |
| **Wykres liniowy** | |
| Zapytanie |Zapytanie, aby uruchomić wykresu liniowego. Pierwsza właściwość jest wartością tekstową i drugą właściwość jest wartością liczbową. To zapytanie jest zazwyczaj używa *miary* — słowo kluczowe do podsumowania wyników. Jeśli zapytanie używa *interwał* — słowo kluczowe, osi x wykresu używa tego przedziału czasu. Jeśli zapytanie nie obejmuje *interwał* — słowo kluczowe, odstępów godzinowych używa osi x. |
| Nawigacja za pomocą kliknięć | Działania wykonywane po kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Wykres liniowy** |**> Oś y** |
| Użyj skali logarytmicznej |Wybierz ten link, aby użyć skali logarytmicznej dla osi y. |
| Jednostki |Określ jednostki dla wartości, które mają zostać zwrócone przez zapytanie. Te informacje są używane do wyświetlania etykiet wykresu, wskazujące typy wartości i, opcjonalnie, aby konwertować wartości. *Jednostki* typ Określa kategorię jednostki i definiuje dostępnych *bieżącej jednostki* wpisz wartości. Jeśli zostanie wybrana wartość w *przekonwertować*, wartości numeryczne są konwertowane z *bieżącej jednostki* typ *przekonwertować* typu. |
| Etykieta niestandardowa |Tekst, który jest wyświetlany na osi y obok etykiety *jednostki* typu. Jeśli żadna etykieta nie zostanie określona, tylko *jednostki* typ jest wyświetlany. |
| **Lista** | |
| Zapytanie |Zapytanie do uruchamiania na liście. Zostanie wyświetlona liczba rekordów, które są zwracane przez zapytanie. |
| Ukryj graf |Wybierz ten link, aby wyłączyć wykres z prawej strony kolumny liczbowej. |
| Włącz wykresy przebiegu w czasie |Wybierz ten link, aby wyświetlić wykres przebiegu w czasie zamiast poziomy pasek. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kolor |Kolor słupków lub wykresy przebiegu w czasie. |
| Operacja |Operacja do wykonania dla wykresu przebiegu w czasie. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Separator nazw i wartości |Ogranicznik pojedynczych znaków, które można użyć do analizowania właściwość text na wiele wartości. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Nawigacja za pomocą kliknięć | Działania wykonywane po kliknięciu elementu na liście.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Lista** |**> Tytuły kolumn** |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst, który jest wyświetlany w górnej części drugiej kolumny. |
| **Lista** |**> Progi** |
| Włącz progi |Wybierz ten link, aby umożliwić progów. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Stos części wykresy wiersza
Stos wykres liniowy przedstawia trzy oddzielne wykresy, przy użyciu wielu serii, w wyniku zapytania dziennika wraz z upływem czasu, jak pokazano poniżej:

![Stos wykresów liniowych](media/log-analytics-view-designer-parts/view-stack-line-charts.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst, który jest wyświetlany w górnej części kafelka. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, począwszy od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wynik w nagłówku. |
| **Wykres 1<br>wykresu 2<br>wykresu 3** |**> Nagłówek** |
| Stanowisko |Tekst, który jest wyświetlany w górnej części wykresu. |
| Podtytuł |Tekst, który jest wyświetlany w obszarze tytułu w górnej części wykresu. |
| **Wykres 1<br>wykresu 2<br>wykresu 3** |**Wykres liniowy** |
| Zapytanie |Zapytanie, aby uruchomić wykresu liniowego. Pierwsza właściwość jest wartością tekstową i drugą właściwość jest wartością liczbową. To zapytanie jest zazwyczaj używa *miary* — słowo kluczowe do podsumowania wyników. Jeśli zapytanie używa *interwał* — słowo kluczowe, osi x wykresu używa tego przedziału czasu. Jeśli zapytanie nie obejmuje *interwał* — słowo kluczowe, odstępów godzinowych używa osi x. |
| Nawigacja za pomocą kliknięć | Działania wykonywane po kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Wykres** |**> Oś y** |
| Użyj skali logarytmicznej |Wybierz ten link, aby użyć skali logarytmicznej dla osi y. |
| Jednostki |Określ jednostki dla wartości, które mają zostać zwrócone przez zapytanie. Te informacje są używane do wyświetlania etykiet wykresu, wskazujące typy wartości i, opcjonalnie, aby konwertować wartości. *Jednostki* typ Określa kategorię jednostki i definiuje dostępnych *bieżącej jednostki* wpisz wartości. Jeśli zostanie wybrana wartość w *przekonwertować*, wartości numeryczne są konwertowane z *bieżącej jednostki* typ *przekonwertować* typu. |
| Etykieta niestandardowa |Tekst, który jest wyświetlany na osi y obok etykiety *jednostki* typu. Jeśli żadna etykieta nie zostanie określona, tylko *jednostki* typ jest wyświetlany. |

## <a name="common-settings"></a>Typowe ustawienia
W poniższych sekcjach opisano ustawienia, które są wspólne dla kilku części wizualizacji.

### <a name="name-value-separator"></a>Separator nazw i wartości
Separator nazw i wartości to ogranicznik można użyć do analizowania właściwość tekst zapytania o listę na wiele wartości. Jeśli określisz ogranicznik, można podać nazwy dla każdego pola, oddzielone od samego ogranicznika w **nazwa** pole.

Na przykład rozważmy właściwość o nazwie *lokalizacji* , takich jak uwzględnione wartości *41 budynku Redmond* i *12 budynku Bellevue*. Separator nazw i wartości można określić kreski (-) i *budowania miast* dla nazwy. To podejście analizuje każdej wartości w dwie właściwości o nazwie *Miasto* i *budynku*.

### <a name="click-through-navigation"></a>Nawigacja za pomocą kliknięć
Nawigacja za pomocą kliknięć definiuje, jakie działania zostaną wykonane po kliknięciu na nagłówek lub element listy w widoku.  To spowoduje to otwarcie kwerendy w [portalu przeszukiwania dzienników](log-analytics-log-search-portals.md) lub uruchomić inny widok.

W poniższej tabeli opisano ustawienia dla nawigacji za pomocą kliknięć.

| Ustawienie           | Opis |
|:--|:--|
| Rejestruj wyszukiwanie (automatycznie) | Wyszukiwanie w dzienniku do uruchomienia po wybraniu elementu nagłówka.  Jest to to samo wyszukiwanie dziennika, na podstawie elementu.
| Przeszukiwanie dzienników        | Wyszukiwanie w dzienniku do uruchomienia po wybraniu elementu na liście.  Wpisz zapytanie do **zapytanie nawigacji** pole.   Użyj *{wybranego elementu}* obejmujący składni dla elementu, który użytkownik zaznaczył.  Na przykład, jeśli zapytanie zawiera kolumnę o nazwie *komputera* i zapytanie nawigacji *{wybranego elementu}*, zapytanie takie jak *komputer = "Mój komputer"* jest uruchamiany po wybraniu komputer. Jeśli zapytanie nawigacji jest *typu = {wybranego elementu} zdarzeń*, zapytanie *typu = zdarzeń komputer = "Mój komputer"* jest uruchamiany. |
| Widok              | Widok, który chcesz otworzyć, po wybraniu elementu nagłówka lub element na liście.  Wybierz nazwę widoku w obszarze roboczym w **nazwy widoku** pole. |



### <a name="sparklines"></a>Wykresy przebiegu w czasie
Wykres przebiegu w czasie jest wykres liniowy małe, który ilustruje wartość wpis na liście wraz z upływem czasu. Dla części wizualizacji za pomocą listy można wybrać, czy mają być wyświetlane poziomy pasek wskazuje względną wartość kolumny liczbowej lub wykres przebiegu w czasie, co oznacza jego wartość, wraz z upływem czasu.

W poniższej tabeli opisano ustawienia wykresy przebiegu w czasie:

| Ustawienie | Opis |
|:--- |:--- |
| Włącz wykresy przebiegu w czasie |Wybierz ten link, aby wyświetlić wykres przebiegu w czasie zamiast poziomy pasek. |
| Operacja |Wykresy są włączone, to operację do wykonania na każdej właściwości w listy w celu obliczenia wartości dla wykresu przebiegu w czasie.<ul><li>Ostatnie przykładowy: ostatnią wartość serii przedziałach czasu.</li><li>Maks.: Maksymalna wartość dla tej serii przedziałach czasu.</li><li>Minimalna: Wartość minimalna dla serii przedziałach czasu.</li><li>Suma: Suma wartości serii przedziałach czasu.</li><li>Podsumowanie: Używa tych samych `measure` polecenia zapytania w nagłówku.</li></ul> |

### <a name="thresholds"></a>Progi
Za pomocą progów, możesz wyświetlić kolorowe ikony obok każdego elementu na liście. Progi umożliwiają szybkie wizualny wskaźnik informujący elementów, które przekracza określoną wartość lub mieszczą się w określonym zakresie. Na przykład możesz wyświetlić zieloną ikonę dla elementów z dozwolonej wartości, żółty, jeśli wartość znajduje się w zakresie, który wyświetla ostrzeżenie i czerwony, w przypadku przekroczenia wartości błędu.

Po włączeniu progi dla części, należy określić co najmniej jeden progów. Jeśli wartość elementu jest większa niż wartość progowa i mniejsza niż wartość progowa dalej, jest używany kolor dla tej wartości. Jeśli element jest większa niż wartość progowa najwyższy, jest używany inny kolor. 

Każdy zestaw próg ma jeden próg o wartości **domyślne**. Jest to kolor, który jest ustawiona, jeśli żadne inne wartości zostaną przekroczone. Można dodać lub usunąć progi, wybierając **Dodaj** (+) lub **Usuń** (przycisk x).

W poniższej tabeli opisano ustawienia progów:

| Ustawienie | Opis |
|:--- |:--- |
| Włącz progi |Wybierz ten link, aby wyświetlić ikonę kolor po lewej stronie każdej wartości. Ta ikona wskazuje wartości kondycji względem określonego progów. |
| Name (Nazwa) |Nazwa wartości progowej. |
| Próg |Wartość progową. Kolor najwyższą wartość progową, która zostanie przekroczony o wartość elementu jest ustawiony kolor kondycji dla każdego elementu listy. Przekroczeniu wartości progowe, nie jest używany domyślny kolor. |
| Kolor |Kolor, który wskazuje wartość progową. |

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [dziennikach](log-analytics-log-searches.md) do obsługi zapytań w części wizualizacji.
