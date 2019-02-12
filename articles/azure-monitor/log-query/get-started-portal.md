---
title: Wprowadzenie do usługi log analytics w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera samouczek do pisania zapytań, za pomocą usługi Log Analytics w witrynie Azure portal.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: bwren
ms.openlocfilehash: b9a5c78ff9d6c1e2c7194f5b92511e94dfafb058
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990500"
---
# <a name="get-started-with-azure-monitor-log-analytics"></a>Wprowadzenie do usługi log analytics w usłudze Azure Monitor

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

W tym samouczku dowiesz się, jak usługa Azure Monitor log analytics w witrynie Azure portal umożliwia pisanie zapytań dzienników usługi Azure Monitor. Jego nauczą Cię, jak do:

- Tworzenie prostych zapytań
- Zrozumieć schemat danych
- Filtrowanie, sortowanie i grupowanie wyników
- Stosowanie zakresu czasu
- Tworzenie wykresów
- Zapisywanie i ładowanie zapytań
- Eksportowanie i udostępnianie zapytań


## <a name="meet-log-analytics"></a>Spełnia usługi log analytics
Log analytics jest w sieci web narzędzie służące do zapisu i wykonywania zapytań dzienników usługi Azure Monitor. Otwórz go, wybierając **dzienniki** w menu usługi Azure Monitor. Rozpoczynają się one od nowego pustego zapytania.

![Strona główna](media/get-started-portal/homepage.png)



## <a name="basic-queries"></a>Podstawowe zapytania
Zapytania mogą służyć do terminy wyszukiwania, identyfikację trendów, analizować wzorce i wiele innych informacji na podstawie danych. Uruchom przy użyciu podstawowego zapytania:

```Kusto
Event | search "error"
```

To zapytanie wyszukuje _zdarzeń_ tabeli rekordy, które zawierają termin "error", w dowolnej właściwości.

Zapytania można zacząć od jednej nazwy tabeli lub **wyszukiwania** polecenia. Powyższy przykład zaczyna się od nazwy tabeli _zdarzeń_, która definiuje zakres kwerendy. Znaku kreski pionowej (|) oddziela polecenia, więc dane wyjściowe pierwszy z nich w danych wejściowych następującego polecenia. Możesz dodać dowolną liczbę poleceń do pojedynczego zapytania.

Innym sposobem pisania tego samego zapytania będą:

```Kusto
search in (Event) "error"
```

W tym przykładzie **wyszukiwania** obejmuje _zdarzeń_ tabeli, a wszystkie rekordy w tabeli wyszukiwany termin "error".

## <a name="running-a-query"></a>Uruchamianie zapytania
Uruchom zapytanie, klikając pozycję **Uruchom** przycisku lub naciskając **Shift + Enter**. Należy wziąć pod uwagę następujące informacje, które określają kodu, która będzie uruchamiana i danych, który jest zwracany:

- Podziały wierszy: Podział pojedynczego sprawia, że zapytanie bardziej zrozumiały. Podziały wierszy wielu podzielić ją na oddzielne zapytania.
- Kursor: Umieść kursor gdzieś w zapytaniu do jego wykonania. Bieżące zapytanie jest uważana za kod, aż zostanie znaleziony pusty wiersz.
- Zakres - przedział czasu czasu _ostatnich 24 godzinach_ jest ustawieniem domyślnym. Do używania innego zakresu, użyj selektora czasu, lub Dodaj godzinę jawne filtru zakresu do zapytania.


## <a name="understand-the-schema"></a>Informacje o schemacie
Schemat jest zbiór tabel wizualnie pogrupowane w logiczne kategorii. Kilka kategorii pochodzą z rozwiązania do monitorowania. _LogManagement_ kategoria zawiera wspólne dane, takie jak Windows i zdarzenia dziennika systemowego, dane dotyczące wydajności i pulsu klienta.

![Schemat](media/get-started-portal/schema.png)

W każdej tabeli dane są organizowane w kolumnach zawierających różne typy danych wskazane przez ikony obok nazwy kolumny. Na przykład _zdarzeń_ tabeli pokazano na zrzucie ekranu zawiera kolumny, takie jak _komputera_ czyli tekstu, _EventCategory_ która jest liczbą, i _TimeGenerated_ czyli daty/godziny.

## <a name="filter-the-results"></a>Filtrowanie wyników
Od zebrania wszystko _zdarzeń_ tabeli.

```Kusto
Event
```

Automatycznego logowania analytics zakresów wyniki według:

- Zakres czasu:  Domyślnie kwerendy są ograniczone do ostatnich 24 godzin.
- Liczba wyników: Wyniki są ograniczone do maksymalnie 10 000 rekordów.

To zapytanie jest bardzo ogólny i zwraca zbyt wiele wyników były przydatne. Możesz filtrować wyniki za pomocą elementów tabeli lub przez jawne dodanie filtru do zapytania. Filtrowanie wyników za pomocą elementów tabeli stosowana do istniejącego zestawu wyników, gdy filtr, aby samo zapytanie zwróci nowy wynik filtrowanego zestawu i w związku z tym można wygenerować bardziej precyzyjne wyniki.

### <a name="add-a-filter-to-the-query"></a>Dodawanie filtru do zapytania
Brak strzałkę na lewo od każdego rekordu. Strzałki można otworzyć szczegóły dla określonego rekordu.

Umieść kursor nad nazwę kolumny "+" i "-", aby wyświetlić ikon. Aby dodać filtr, który zwraca tylko rekordy z taką samą wartość, kliknij znak "+". Kliknij przycisk "-" wykluczania rekordów o tej wartości, a następnie kliknij przycisk **Uruchom** ponownie uruchomić zapytanie.

![Dodawanie filtru do zapytania](media/get-started-portal/add-filter.png)

### <a name="filter-through-the-table-elements"></a>Filtrują elementy tabeli
Teraz możemy skoncentrować się na zdarzeń o ważności _błąd_. To jest określona w kolumnie o nazwie _EventLevelName_. Musisz przewiń w prawo, aby wyświetlić tę kolumnę.

Kliknięcie ikony filtra umieszczonych obok nazw kolumn, a następnie w oknie podręcznym wybierz wartości _rozpoczyna się od_ tekst _błąd_:

![Filtr](media/get-started-portal/filter.png)


## <a name="sort-and-group-results"></a>Sortuj i Grupuj wyniki
Wyniki są zawężona obejmujący tylko zdarzenia błędu z programu SQL Server utworzone w ciągu ostatnich 24 godzin. Wyniki nie są sortowane w dowolny sposób. Aby posortować wyniki według określonej kolumny, takie jak _sygnatura czasowa_ na przykład kliknąć tytuł kolumny. Jedno kliknięcie sortuje w kolejności rosnącej, a drugie kliknięcie pozwala na sortowanie malejąco.

![Sortuj kolumny](media/get-started-portal/sort-column.png)

Innym sposobem organizowania wyników jest grup. Aby grupowanie wyników według określonej kolumny, po prostu przeciągnij nagłówek kolumny powyżej innych kolumn. Aby utworzyć podgrupy, przeciągnij innych kolumn w górnym pasku także.

![Grupy](media/get-started-portal/groups.png)

## <a name="select-columns-to-display"></a>Wybierz kolumny do wyświetlenia
Tabela wyników zawiera często wiele kolumn. Może okazać się, że niektóre kolumny zwracane nie są wyświetlane domyślnie lub może być usunięcie niektórych kolumn, które są wyświetlane. Aby wybrać kolumny do pokazania, kliknij przycisk kolumn:

![Wybieranie kolumn](media/get-started-portal/select-columns.png)


## <a name="select-a-time-range"></a>Wybierz zakres czasu
Domyślnie stosuje usługa log analytics _ostatnich 24 godzinach_ zakres czasu. Aby użyć innego zakresu, wybierz inną wartość za pomocą selektora czasu, a następnie kliknij przycisk **Uruchom**. Oprócz wstępnie zdefiniowane wartości, można użyć _niestandardowego przedziału czasu_ możliwość dokonania wyboru bezwzględny zakres zapytania.

![Selektor godziny](media/get-started-portal/time-picker.png)

Po wybraniu niestandardowego zakresu czasu, wybranych wartości są w formacie UTC, może być inny niż lokalnej strefy czasowej.

Jeśli zapytanie zawiera jawnie filtr _TimeGenerated_, czas selektora tytuł zostanie wyświetlony _Ustaw w zapytaniu_. Ręczne wybranie zostaną wyłączone w celu uniknięcia konfliktu.


## <a name="charts"></a>Wykresy
Oprócz zwracania wyników w tabeli, wyniki zapytania mogą być przedstawiane na formaty wizualne. Użyj następującego zapytania, na przykład:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Domyślnie wyniki są wyświetlane w tabeli. Kliknij przycisk _wykresu_ do wyświetlania wyników w graficzny widok:

![Wykres słupkowy](media/get-started-portal/bar-chart.png)

Wyniki są wyświetlane w skumulowany wykres słupkowy. Kliknij przycisk _skumulowany kolumnowy_ i wybierz _kołowy_ do innego widoku wyników do wyświetlenia:

![Wykres kołowy](media/get-started-portal/pie-chart.png)

Inne właściwości widoku, takich jak x i osi y lub grupowanie i rozdzielanie preferencji, można ją zmienić ręcznie na pasku sterowania.

Można również ustawić preferowany widok w zapytaniu, za pomocą operatora renderowania.

### <a name="smart-diagnostics"></a>Inteligentna Diagnostyka
Na wykres czasu w przypadku nagłym skokiem lub krok w danych, mogą pojawić się wyróżniony punktu, w wierszu. Oznacza to, że _inteligentna Diagnostyka_ zidentyfikowała kombinacji właściwości, które odfiltrować nagła zmiana. Kliknij punkt, aby uzyskać więcej szczegółów na temat filtr i wyświetlić filtrowanej wersji. To może pomóc w zidentyfikowaniu, co spowodowało zmianę:

![Inteligentna Diagnostyka](media/get-started-portal/smart-diagnostics.png)

## <a name="pin-to-dashboard"></a>Przypnij do pulpitu nawigacyjnego
Aby przypiąć na diagramie lub tabeli na jedną z udostępnionych pulpitów nawigacyjnych platformy Azure, kliknij ikonę pinezki.

![Przypnij do pulpitu nawigacyjnego](media/get-started-portal/pin-dashboard.png)

Niektóre definiowaniu są stosowane do wykresu, gdy możesz przypiąć do pulpitu nawigacyjnego:

- Wierszy i kolumn tabeli: Aby przypiąć tabelę do pulpitu nawigacyjnego, musi on mieć cztery lub mniejszą liczbę kolumn. Wyświetlanych jest tylko pierwszych wierszy siedem.
- Ograniczenie czasu: Zapytania są automatycznie ograniczone do ostatnich 14 dni.
- Ograniczenie liczby bin: Jeśli możesz wyświetlić wykres, który ma wiele pojemniki dyskretnych, mniej pojemniki wypełnione automatycznie są grupowane w jednym _innych_ bin.

## <a name="save-queries"></a>Zapisywanie zapytań
Po utworzeniu użytecznego zapytania, można go zapisać lub udostępnienia innym osobom. **Zapisz** ikonę znajduje się na górnym pasku.

Na stronie całe zapytanie lub pojedynczego zapytania można zapisać jako funkcja. Funkcje są zapytania, które również mogą być przywoływane przez inne zapytania. Aby zapisać zapytanie jako funkcję, musisz podać alias funkcji, która jest to nazwa używana do wywołania tego zapytania, gdy odwołują się inne zapytania.

![Zapisz — funkcja](media/get-started-portal/save-function.png)

Zapytania analityczne dziennika są zawsze zapisywane w wybranym obszarze roboczym i udostępnione dla innych użytkowników tego obszaru roboczego.

## <a name="load-queries"></a>Ładowanie zapytań
Ikona Eksplorator zapytań, znajduje się w prawym górnym rogu obszaru. Ta lista zawiera wszystkie zapisane zapytania według kategorii. Umożliwia również oznaczanie określonego zapytania jako ulubione, aby szybko znaleźć je w przyszłości. Kliknij dwukrotnie zapisane zapytanie, aby dodać go do bieżącego okna.

![Eksplorator zapytań](media/get-started-portal/query-explorer.png)

## <a name="export-and-share-as-link"></a>Eksportuj i Udostępnij jako łącze
Usługa log analytics obsługuje kilka metod eksportowania:

- Excel: Zapisz wyniki jako plik CSV.
- Power BI: Eksportuj wyniki do power BI. Zobacz [dane dziennika importu usługi Azure Monitor do usługi Power BI](../../azure-monitor/platform/powerbi.md) Aby uzyskać szczegółowe informacje.
- Link udostępniania: Samo zapytanie może być udostępniane jako łącze, które następnie mogą być wysyłane i wykonywane przez innych użytkowników, które mają dostęp do tego samego obszaru roboczego.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [Pisanie zapytań dzienników w usłudze Azure Monitor](get-started-queries.md).
