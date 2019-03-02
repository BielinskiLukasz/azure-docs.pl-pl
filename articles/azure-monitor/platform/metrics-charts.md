---
title: Eksplorator metryk usługi Azure Monitor
description: Informacje o nowych funkcjach w Eksploratorze metryk usługi Azure Monitor
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: e5c7d019a34736cfbc26e245dc54a93374aada9c
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242432"
---
# <a name="azure-monitor-metrics-explorer"></a>Eksplorator metryk usługi Azure Monitor

Eksplorator metryk usługi Azure Monitor to składnik systemu Microsoft Azure portal, która umożliwia wykreślania wykresy i wizualne korelowanie trendów i badanie wzrostów i spadki wartości metryk. Eksplorator metryk jest istotne punkt początkowy, badania różne problemy z wydajnością i dostępności przy użyciu aplikacji i infrastruktury hostowany na platformie Azure lub monitorowane przez usługi Azure Monitor.

## <a name="metrics-in-azure"></a>Metryki na platformie Azure

[Metryki w usłudze Azure Monitor](data-collection.md#metrics) serie mierzonych i liczniki, które są zbierane i przechowywane wraz z upływem czasu. Brak metryk standardowa (lub "platforma") i metryki niestandardowe. Metryki standardowe są dostarczane przez samą platformę Azure. Standardowa metryki odzwierciedlają statystyki użycia i kondycji zasobów platformy Azure. Natomiast metryki niestandardowe są wysyłane do platformy Azure przy użyciu aplikacji przy użyciu [interfejsu API usługi Application Insights dla niestandardowych zdarzeń](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Metryki niestandardowe są przechowywane w zasoby usługi Application Insights wraz z innymi metrykami określonych aplikacji.

## <a name="create-a-new-chart"></a>Utwórz nowy wykres

1. Otwórz witrynę Azure portal
2. Przejdź do nowego **Monitor** , a następnie wybierz pozycję **metryki**.

   ![Obraz metryki](./media/metrics-charts/00001.png)

3. **Metryki selektor** zostanie automatycznie otwarte dla Ciebie. Wybierz zasób z listy, aby wyświetlić jego skojarzonych z nimi metryk. Tylko zasoby z metryki są wyświetlane na liście.

   ![Obraz metryki](./media/metrics-charts/00002.png)

   > [!NOTE]
   >Jeśli masz więcej niż jedną subskrypcję platformy Azure, Eksplorator metryk ściąga się z zasobami we wszystkich subskrypcjach, które są wybrane w ustawieniach portalu -> filtru według listy subskrypcji. Aby je zmienić, kliknij ikonę koła zębatego ustawień portalu na górze ekranu i wybierz pozycję subskrypcje, które chcesz użyć.

4. W przypadku niektórych typów zasobów (kont magazynu i maszyn wirtualnych), przed wybraniem metrykę, musisz wybrać **Namespace**. Każda przestrzeń nazw zawiera swój własny zestaw metryk, które mają zastosowanie tylko w tej przestrzeni nazw, a nie inne przestrzenie nazw.

   Na przykład każdej usługi Azure Storage ma metryki subservices "Blob", "Files", "Kolejki" i "Tabele", które są wszystkie elementy na koncie magazynu. Jednak metryki "Liczba komunikatów w kolejce" dotyczy naturalnie Usługa "W kolejce", a nie innych subservices konta magazynu.

   ![Obraz metryki](./media/metrics-charts/00003.png)

5. Wybierz metrykę, z listy. Jeśli znasz część nazwy metryki, które chcesz, możesz zacząć wpisywać go w będzie filtrowana lista dostępnych metryk:

   ![Obraz metryki](./media/metrics-charts/00004.png)

6. Po wybraniu metrykę, wykres będą renderowane przy użyciu domyślna agregacja dla wybranej metryki. W tym momencie możesz po prostu kliknąć opuszczenie **selektor metryki** go zamknąć. Wykres można również opcjonalnie przełączyć się do różnych agregacji. Niektóre metryki agregacji przełączanie umożliwia można wybrać wartość, która mają być wyświetlane na wykresie. Na przykład można przełączać się między wartość średnią, minimalną i maksymalną. 

7. Klikając **Dodaj metrykę** i powtórzyć kroki od 3 do 6, możesz dodać więcej metryk na tym samym wykresie.

   > [!NOTE]
   > Zwykle nie chcesz mieć metryki różne jednostki miary (np. "milisekund" i "kilobajtów") lub z znacząco odmiennych skalowania na jeden wykres. Zamiast tego należy wziąć pod uwagę przy użyciu wielu wykresów. Kliknij przycisk Dodaj wykres, aby utworzyć wiele wykresów w Eksploratorze metryk.

## <a name="apply-filters-to-charts"></a>Zastosuj filtry do wykresów

Filtry można stosować do wykresów przedstawiających metryk z wymiarami. Na przykład, jeśli metryki "Liczba transakcji" ma wymiar, "Typ odpowiedzi", który wskazuje, czy odpowiedź z transakcji zakończyło się pomyślnie lub nie powiodło się, następnie filtrowanie tego wymiaru będzie wykreślanie wiersz wykresów dla tylko pomyślne (lub tylko nie powiodło się) transakcji. 

### <a name="to-add-a-filter"></a>Aby dodać filtr

1. Wybierz **Dodaj filtr** powyżej wykresu

2. Wybierz, który wymiar (właściwość), do których chcesz filtrować

   ![metryki obrazu](./media/metrics-charts/00006.png)

3. Wybierz wartości wymiarów, które mają zostać uwzględnione podczas wykreślania wykresu (w tym przykładzie przedstawiono filtrowanie transakcje magazynowe pomyślnych):

   ![metryki obrazu](./media/metrics-charts/00007.png)

4. Po wybraniu wartości filtru, kliknij poza selektor filtrów, aby je zamknąć. Teraz wykres pokazuje, ile transakcji magazynu nie powiodło się:

   ![metryki obrazu](./media/metrics-charts/00008.png)

5. Możesz powtórzyć kroki 1 – 4, aby zastosować wiele filtrów do tych samych wykresów.

## <a name="apply-splitting-to-a-chart"></a>Zastosuj rozdzielenie do wykresu

Podziel metryki według wymiaru, aby zwizualizować segmentów jak różne metryki porównania między nimi i zidentyfikować odległe mniejsze segmenty wymiaru. 

### <a name="to-apply-splitting"></a>Aby zastosować podział

1. Kliknij pozycję **zastosować podział** powyżej wykresu.
 
   > [!NOTE]
   > Dzielenie nie można używać z wykresów, które mają wiele metryk. Ponadto można mieć wiele filtrów, ale tylko jeden wymiar podziału stosowane do dowolnego pojedynczego wykresu.

2. Wybierz wymiar, na którym chcesz podzielić wykresu:

   ![metryki obrazu](./media/metrics-charts/00010.png)

   Teraz wykres zawiera teraz wiele wierszy, po jednym dla każdego segmentu wymiaru:

   ![metryki obrazu](./media/metrics-charts/00012.png)

3. Kliknij przycisk opuszczenie **selektor grupowania** go zamknąć.

   > [!NOTE]
   > Aby ukryć segmentów, które są nieodpowiednie dla danego scenariusza i była łatwiejsza do odczytania wykresy, należy użyć filtrowanie i rozdzielenie tego samego wymiaru.

## <a name="lock-boundaries-of-chart-y-axis"></a>Granice blokady osi y wykresu

Blokowanie zakres osi y staje się ważne, gdy na wykresie widać mniejszych wahania większe wartości. 

Na przykład gdy liczba pomyślnych żądań rozwinięta z dostępność przez 99,99% 99,5%, może reprezentować znaczny spadek jakości usług. Jednakże obserwowanie fluktuacje mała wartość liczbowa może być trudne lub niemożliwe nawet z domyślnych ustawień wykresu. W takim przypadku można zablokować najniższy granic wykresu do 99%, co spowoduje, że ten mały spadek bardziej oczywisty. 

Innym przykładem jest fluktuacje w dostępnej pamięci, gdy wartość będzie z technicznego punktu widzenia nigdy nie dotrzeć do 0. Ustalania zakresu na wartość większą może ułatwić spadnie w dostępnej pamięci na ekranie. 

Aby kontrolować zakres osi y, użyj "..." menu wykresu, a następnie wybierz **Edytuj wykres** dostęp do zaawansowanych ustawień wykresu. Zmodyfikuj wartości w sekcji zakres osi y lub użyj **automatycznie** przycisk, aby przywrócić ustawienia domyślne.

![metryki obrazu](./media/metrics-charts/00014-manually-set-granularity.png)

> [!WARNING]
> Blokowanie granice osi y wykresy, które śledzą różne liczby lub sumuje w przedziale czasu (i dlatego liczba używanych, sum, minimalnych i maksymalnych agregacji) zwykle wymagają określania stopnia szczegółowości stały czas, a nie opierając się na automatyczne ustawienia domyślne. Jest to konieczne, ponieważ wartości na wykresach zmianie, gdy stopień szczegółowości czasu automatycznie zostanie zmodyfikowany przez użytkownika zmiany rozmiaru okna przeglądarki lub ruch rozdzielczość ekranu co do innego. Wartość wynikowa zmiana efekty stopień szczegółowości czasu wygląd wykresu, powodując unieważnienie bieżące zaznaczenie zakresu osi y.

## <a name="pin-charts-to-dashboards"></a>Przypnij wykresy do pulpitów nawigacyjnych

Po skonfigurowaniu wykresów, warto dodać do pulpitów nawigacyjnych, aby można go wyświetlić, prawdopodobnie w kontekście innych telemetrii monitorowania, lub udostępnienia swojemu zespołowi.

Aby przypiąć wykres skonfigurowanego do pulpitu nawigacyjnego:

Po skonfigurowaniu wykresu, wybierz polecenie **akcje wykresu** menu w prawym rogu wykresu top, a następnie kliknij przycisk **Przypnij do pulpitu nawigacyjnego**.

![metryki obrazu](./media/metrics-charts/00013.png)

## <a name="create-alert-rules"></a>Tworzenie reguł alertów

Możesz użyć kryteriów, ustawionych w celu wizualizacji metryk, jako podstawę metryki na podstawie reguły alertu. Nowa reguła alertu będzie zawierać Twojego zasobu docelowego, metryki, dzielenie i wymiary filtru z wykresu. Będzie można zmodyfikować te ustawienia później na panelu Tworzenie reguły alertu.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>Aby utworzyć nową regułę alertu, kliknij przycisk **reguły nowy Alert**

![Nowy przycisk reguły alertu wyróżniony czerwonym kolorem](./media/metrics-charts/015.png)

Możesz zostaną pobrane do okienka tworzenia reguły alertu z bazowego wymiarami metryk z wykresu wstępnie wypełniane, aby ułatwić Generowanie niestandardowych reguł alertów.

![Utwórz regułę alertu](./media/metrics-charts/016.png)

Zapoznaj się z tą [artykułu](alerts-metric.md) Aby dowiedzieć się więcej o konfigurowaniu alertów dotyczących metryk.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

*Nie widzę żadnych danych na wykresie.*

* Filtry mają zastosowanie do wszystkich schematów w okienku. Upewnij się, że podczas, gdy są najbardziej istotne na jeden wykres, nie został ustawiony filtr, który nie obejmuje wszystkich danych na innym.

* Jeśli chcesz ustawić różne filtry na różnych wykresów, należy je utworzyć w różnych bloków, zapisać je jako osobne Ulubione. Jeśli chcesz, możesz przypiąć je do pulpitu nawigacyjnego tak, aby można je wyświetlić obok siebie.

* Jeśli wykres pozwala na segmentację według właściwości, która nie jest zdefiniowana w metryki, będą nic na wykresie. Spróbuj wyczyścić segmentacji (dzielenie), lub wybierz inną właściwość.

## <a name="next-steps"></a>Kolejne kroki

  Odczyt [Tworzenie niestandardowych pulpitów nawigacyjnych wskaźników KPI](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) Aby dowiedzieć się więcej na temat najlepszych rozwiązań do tworzenia pulpitów nawigacyjnych informacje z możliwością działania za pomocą metryk.

