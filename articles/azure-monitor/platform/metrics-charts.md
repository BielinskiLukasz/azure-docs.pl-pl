---
title: Zaawansowane funkcje Eksploratora metryk platformy Azure
description: Dowiedz się więcej na temat zaawansowanych funkcji Azure Monitor Eksplorator metryk
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: e6ff33b6a23cb85649a8811a8bef27ab455ab9e6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82980902"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Zaawansowane funkcje Eksploratora metryk platformy Azure

> [!NOTE]
> W tym artykule założono, że znasz podstawowe funkcje Eksplorator metryk. Jeśli jesteś nowym użytkownikiem i chcesz dowiedzieć się, jak utworzyć pierwszy wykres pomiarowy, zobacz [Rozpoczynanie pracy z usługą Azure Eksplorator metryk](metrics-getting-started.md).

## <a name="metrics-in-azure"></a>Metryki na platformie Azure

[Metryki w Azure monitor](data-platform-metrics.md) są serią mierzonych wartości i liczb, które są zbierane i przechowywane w czasie. Istnieją metryki standardowe (lub platformy) i metryki niestandardowe. Standardowe metryki są udostępniane przez samą platformę Azure. Standardowe metryki odzwierciedlają dane statystyczne dotyczące kondycji i użycia zasobów platformy Azure. Niestandardowe metryki są wysyłane do platformy Azure przez aplikacje przy użyciu [interfejsu api Application Insights dla niestandardowych zdarzeń i metryk](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics), [rozszerzenia Windows Diagnostyka Azure (funkcji wad)](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview)lub [interfejsu API REST Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-store-custom-rest-api).

## <a name="create-views-with-multiple-metrics-and-charts"></a>Tworzenie widoków z wieloma metrykami i wykresami

Można utworzyć wykresy, które kreślą wiele linii metryk lub jednocześnie wyświetlają wiele wykresów metryk. Ta funkcja umożliwia:

- skorelować powiązane metryki na tym samym wykresie, aby zobaczyć, jak jedna wartość jest powiązana z inną
- Wyświetl metryki z różnymi jednostkami miary w bliskim sąsiedztwie
- wizualnie Agreguj i porównuj metryki z wielu zasobów

Jeśli na przykład masz 5 kont magazynu i chcesz dowiedzieć się, ile łącznego obszaru są używane między nimi, możesz utworzyć wykres warstwowy (skumulowany), który pokazuje indywidualną i sumę wszystkich wartości w określonych punktach w czasie.

### <a name="multiple-metrics-on-the-same-chart"></a>Wiele metryk na tym samym wykresie

Najpierw [Utwórz nowy wykres](metrics-getting-started.md#create-your-first-metric-chart). Kliknij przycisk **Dodaj metrykę** i powtórz kroki, aby dodać kolejną metrykę na tym samym wykresie.

   > [!NOTE]
   > Zwykle nie chcesz mieć metryk z różnymi jednostkami miary (tj. "milisekund" i "kilobajtów") lub z znacznie różną skalą na jednym wykresie. Zamiast tego należy rozważyć użycie wielu wykresów. Kliknij przycisk Dodaj wykres, aby utworzyć wiele wykresów w Eksploratorze metryk.

### <a name="multiple-charts"></a>Wiele wykresów

Kliknij przycisk **Dodaj wykres** i Utwórz inny wykres z inną metryką.

### <a name="order-or-delete-multiple-charts"></a>Kolejność lub usuwanie wielu wykresów

Aby zamówić lub usunąć wiele wykresów, kliknij symbol wielokropka ( **...** ), aby otworzyć menu Wykres i wybrać odpowiedni element menu **Przenieś w górę**lub **Przenieś w dół**lub **Usuń**.

## <a name="changing-aggregation"></a>Zmiana agregacji

Po dodaniu metryki do wykresu Eksplorator metryk automatycznie wybiera swoją domyślną agregację. Wartość domyślna ma sens w podstawowych scenariuszach, ale można użyć innej agregacji, aby uzyskać dodatkowe informacje o metryki. Wyświetlanie różnych agregacji na wykresie wymaga zrozumienia sposobu, w jaki Eksplorator metryk je obsługuje. 

Metryki to serie pomiarów (lub "wartości metryk") przechwytywane w danym okresie czasu. Podczas kreolenia wykresu wartości wybranej metryki są agregowane w postaci *ziarna czasu*. Rozmiar ziarna można wybrać [za pomocą panelu selektora czasu Eksplorator metryk](metrics-getting-started.md#select-a-time-range). Jeśli nie wprowadzisz jawnego wyboru ziarna czasu, stopień szczegółowości czasu zostanie automatycznie wybrany na podstawie aktualnie wybranego zakresu czasu. Po ustaleniu ziarna czasu wartości metryk, które zostały przechwycone podczas każdego interwału ziarna, są agregowane i umieszczane na wykresie — jeden punkt danych na ziarno.

Załóżmy na przykład, że wykres pokazuje metrykę **czasu odpowiedzi serwera** przy użyciu **średniej** agregacji w ciągu **ostatnich 24 godzin** :

- Jeśli stopień szczegółowości czasu jest ustawiony na 30 minut, wykres jest rysowany z 48 zagregowanych punktów kontrolnych (np. wykres liniowy łączy 48 kropek w obszarze wykresów wykresu). Oznacza to, że 24-godzinny x 2 punkty kontrolne na godzinę. Każdy punkt danych reprezentuje *średnią* wszystkich przechwyconych czasów odpowiedzi dla żądań serwera, które wystąpiły w ciągu każdego z odpowiednich 30 okresów czasu.
- Po przełączeniu stopnia szczegółowości czasu na 15 minut otrzymujesz 96 zagregowanych punktów.  Oznacza to, że 24-godzinny x 4 punkty kontrolne na godzinę.

W Eksploratorze metryk dostępne są pięć podstawowych typów agregacji statystyk: **sum**, **Count**, **min**, **Max**i **Average**. Agregacja **sum** jest czasami określana jako **Suma** agregacji. W przypadku wielu metryk Eksplorator metryk będzie ukrywać agregacje, które są całkowicie nieistotne i nie mogą być używane.

- **Sum** — suma wszystkich wartości przechwyconych w interwale agregacji
- **Count** — liczba pomiarów przechwyconych w interwale agregacji. Należy zauważyć, że **Liczba** będzie równa **sumie** w przypadku, gdy Metryka jest zawsze przechwytywana z wartością 1. Jest to typowe, gdy Metryka śledzi liczbę różnych zdarzeń, a każda pomiar reprezentuje jedno zdarzenie (tzn. kod wygeneruje rekord metryk za każdym razem, gdy występuje nowe żądanie)
- **Average** — średnia wartości metryk przechwyconych w interwale agregacji
- **Min** — najmniejsza wartość przechwycona przez interwał agregacji
- **Max** — największa wartość przechwycona przez interwał agregacji

## <a name="apply-filters-to-charts"></a>Zastosuj filtry do wykresów

Można zastosować filtry do wykresów, które wyświetlają metryki z wymiarami. Na przykład jeśli Metryka "liczba transakcji" ma wymiar "typ odpowiedzi", który wskazuje, czy odpowiedź z transakcji zakończyła się powodzeniem, czy niepowodzeniem, filtrowanie w tym wymiarze spowoduje wykreślenie wiersza wykresu dla transakcji zakończonych powodzeniem (lub tylko zakończonych niepowodzeniem). 

### <a name="to-add-a-filter"></a>Aby dodać filtr

1. Wybierz pozycję **Dodaj filtr** powyżej wykresu

2. Wybierz wymiar, który ma być filtrowany

   ![obraz metryki](./media/metrics-charts/00006.png)

3. Wybierz wartości wymiaru, które mają zostać uwzględnione podczas kreolenia wykresu (w tym przykładzie pokazano, jak odfiltrować pomyślne transakcje magazynu):

   ![obraz metryki](./media/metrics-charts/00007.png)

4. Po wybraniu wartości filtru kliknij poza selektorem filtru, aby go zamknąć. Teraz wykres pokazuje, ile transakcji magazynu nie powiodło się:

   ![obraz metryki](./media/metrics-charts/00008.png)

5. Możesz powtórzyć kroki 1-4, aby zastosować wiele filtrów do tych samych wykresów.



## <a name="apply-splitting-to-a-chart"></a>Zastosuj podział do wykresu

Można podzielić metrykę według wymiaru, aby wizualizować, jak różne segmenty są porównywane z innymi, i identyfikować nieleżące segmenty wymiaru.

### <a name="apply-splitting"></a>Zastosuj podział

1. Kliknij przycisk **Zastosuj podział** powyżej wykresu.
 
   > [!NOTE]
   > Nie można używać dzielenia z wykresami, które mają wiele metryk. Ponadto można mieć wiele filtrów, ale tylko jeden wymiar podziału jest zastosowany do każdego pojedynczego wykresu.

2. Wybierz wymiar, dla którego chcesz podzielić wykres:

   ![obraz metryki](./media/metrics-charts/00010.png)

   Teraz wykres zawiera teraz wiele wierszy, po jednym dla każdego segmentu wymiaru:

   ![obraz metryki](./media/metrics-charts/00012.png)

3. Kliknij poza **selektorem grupowania** , aby go zamknąć.

   > [!NOTE]
   > Użyj filtrowania i dzielenia w tym samym wymiarze, aby ukryć segmenty nieistotne dla danego scenariusza i ułatwić odczytywanie wykresów.

## <a name="lock-boundaries-of-chart-y-axis"></a>Blokuj granice osi y wykresu

Zablokowanie zakresu osi y jest ważne, gdy wykres pokazuje mniejsze wahania większych wartości. 

Na przykład, gdy liczba pomyślnych żądań spadnie w dół od 99,99% do 99,5%, może to oznaczać znaczącą redukcję jakości usługi. Obserwowanie jednak niewielkie wahania wartości liczbowych byłyby trudne lub nawet niemożliwe z domyślnych ustawień wykresu. W takim przypadku można zablokować najniższą granicę wykresu do 99%, co sprawia, że to małe powiększanie. 

Innym przykładem jest Wahanie dostępnej pamięci, gdzie wartość będzie technicznie nigdy nie dociera do 0. Naprawianie zakresu do wyższej wartości może spowodować, że przerwy w dostępnej pamięci są łatwiejsze. 

Aby kontrolować zakres osi y, użyj "..." menu Wykres i wybierz pozycję **Edytuj wykres** , aby uzyskać dostęp do zaawansowanych ustawień wykresu. Zmodyfikuj wartości w sekcji Zakres osi Y **lub użyj przycisku** Auto, aby przywrócić wartości domyślne.

![obraz metryki](./media/metrics-charts/00014-manually-set-granularity.png)

> [!WARNING]
> Zablokowanie granic osi y dla wykresów, które śledzą różne liczby lub sumy w danym okresie czasu (w tym przypadku użycie metody Count, sum, minimum lub Maximum) zwykle wymaga określenia stałego stopnia szczegółowości, zamiast polegania na automatycznym wykorzystaniu wartości domyślnych. Jest to konieczne, ponieważ wartości na wykresach zmieniają się, gdy poziom szczegółowości czasu jest automatycznie modyfikowany przez okno przeglądarki zmiany rozmiarów użytkownika lub przejście z jednej rozdzielczości ekranu do innej. Zmiana stopnia szczegółowości w czasie wpływa na wygląd wykresu, co unieważnia bieżący wybór zakresu osi y.

## <a name="change-colors-of-chart-lines"></a>Zmień kolory linii wykresu

Po skonfigurowaniu wykresów linie wykresu zostaną automatycznie przypisane do koloru z domyślnej palety. Można zmienić te kolory.

Aby zmienić kolor linii wykresu, kliknij kolorowy pasek w legendzie, który odnosi się do wykresu. Zostanie otwarte okno dialogowe selektora kolorów. Użyj próbnika kolorów, aby skonfigurować kolor linii.

Po skonfigurowaniu kolorów wykresu pozostaną one w taki sposób, gdy przypinasz wykres do pulpitu nawigacyjnego. W poniższej sekcji pokazano, jak przypiąć wykres.

![obraz metryki](./media/metrics-charts/018.png)

## <a name="pin-charts-to-dashboards"></a>Przypinanie wykresów do pulpitów nawigacyjnych

Po skonfigurowaniu wykresów możesz dodać go do pulpitów nawigacyjnych, aby można było wyświetlić go ponownie, prawdopodobnie w kontekście innej telemetrii monitorowania lub udostępnić zespołowi.

Aby przypiąć skonfigurowany wykres do pulpitu nawigacyjnego:

Po skonfigurowaniu wykresu kliknij menu **Akcje wykresu** w prawym górnym rogu wykresu, a następnie kliknij pozycję **Przypnij do pulpitu nawigacyjnego**.

![obraz metryki](./media/metrics-charts/00013.png)

## <a name="create-alert-rules"></a>Tworzenie reguł alertów

Możesz użyć kryteriów ustawionych do wizualizacji metryk jako podstawy reguły alertu opartej na metrykach. Nowa reguła alertów będzie obejmować zasób docelowy, metrykę, podział i wymiary filtru z wykresu. Później będzie można modyfikować te ustawienia w okienku tworzenia reguły alertu.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>Aby utworzyć nową regułę alertu, kliknij pozycję **Nowa reguła alertu** .

![Przycisk nowej reguły alertu wyróżniony czerwonymi](./media/metrics-charts/015.png)

Nastąpi przekierowanie do okienka Tworzenie reguły alertów z bazowymi wymiarami metryk z wykresu wstępnie wypełnionym, aby ułatwić generowanie niestandardowych reguł alertów.

![Tworzenie reguły alertu](./media/metrics-charts/016.png)

Zapoznaj się z tym [artykułem](alerts-metric.md) , aby dowiedzieć się więcej o konfigurowaniu alertów dotyczących metryk.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

*Nie widzę żadnych danych na wykresie.*

* Filtry stosują się do wszystkich wykresów w okienku. Upewnij się, że podczas skoncentrowania się na jednym wykresie nie ustawiono filtru, który wyklucza wszystkie dane z innego.

* Jeśli chcesz ustawić inne filtry na różnych wykresach, utwórz je w różnych blokach, a następnie Zapisz je jako osobne Ulubione. Jeśli chcesz, możesz przypiąć je do pulpitu nawigacyjnego, aby były widoczne obok siebie.

* W przypadku segmentacji wykresu na podstawie właściwości, która nie jest zdefiniowana w metryce, na wykresie nie będzie niczego. Spróbuj usunąć segmentację (dzielenie) lub wybierz inną właściwość.

## <a name="next-steps"></a>Następne kroki

  Przeczytaj temat [Tworzenie niestandardowych pulpitów nawigacyjnych wskaźników KPI](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) , aby dowiedzieć się więcej o najlepszych rozwiązaniach dotyczących tworzenia pulpitów nawigacyjnych z możliwością podejmowania działań z

