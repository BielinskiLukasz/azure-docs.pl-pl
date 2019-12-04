---
title: Informacje o tym, jak alerty metryk działają w Azure Monitor.
description: Zapoznaj się z informacjami o tym, co możesz zrobić z alertami metryk i jak działają w Azure Monitor.
author: rboucher
ms.author: robb
ms.date: 11/18/2019
ms.topic: conceptual
ms.service: azure-monitor
ms.subservice: alerts
ms.openlocfilehash: aa54b9ce23ffab266ed6403e3525ae1290d99cec
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74775725"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Informacje o tym, jak alerty metryk działają w Azure Monitor

Alerty metryk w Azure Monitor działają na podstawie metryk wielowymiarowych. Te metryki mogą być [metrykami platformy](alerts-metric-near-real-time.md#metrics-and-dimensions-supported), [metrykami niestandardowymi](../../azure-monitor/platform/metrics-custom-overview.md), [popularnymi dziennikami z Azure monitor konwertowane na](../../azure-monitor/platform/alerts-metric-logs.md) metryki i metryki Application Insights. Alerty metryk oceniają w regularnych odstępach czasu, aby sprawdzić, czy warunki dotyczące co najmniej jednej z serii czasu metryk są prawdziwe i powiadamiać o spełnieniu oceny. Alerty metryk są stanowe, oznacza to, że wysyłają powiadomienia tylko wtedy, gdy stan zmieni się.

## <a name="how-do-metric-alerts-work"></a>Jak działają Alerty metryk?

Można zdefiniować regułę alertu metryki, określając zasób docelowy do monitorowania, nazwę metryki, typ warunku (statyczny lub dynamiczny) oraz warunek (operator i próg/czułość) oraz grupę akcji, która ma zostać wyzwolona po wyzwoleniu reguły alertu. Typy warunków mają wpływ na sposób określania progów. [Dowiedz się więcej o opcjach typu i czułości wartości progów dynamicznych](alerts-dynamic-thresholds.md).

### <a name="alert-rule-with-static-condition-type"></a>Reguła alertu z typem warunku statycznego

Załóżmy, że utworzono prostą regułę alertu metryki statycznej progu:

- Zasób docelowy (zasób platformy Azure, który chcesz monitorować): myVM
- Metryka: procentowy procesor CPU
- Typ warunku: statyczny
- Agregacja czasu (statystyka, która jest uruchamiana za pośrednictwem wartości metryk nieprzetworzonych. Agregacje obsługiwanego czasu to min, Max, średnia, suma, liczba): Średnia
- Okres (wyszukanie okna, nad którym są sprawdzane wartości metryk): w ciągu ostatnich 5 minut
- Częstotliwość (częstotliwość, z jaką alert dotyczący metryki sprawdza, czy warunki są spełnione): 1 min
- Operator: większe niż
- Próg: 70

Od momentu utworzenia reguły alertu monitor jest uruchamiany co 1 min i sprawdza wartości metryk dla ostatnich 5 minut i sprawdza, czy średnia z tych wartości przekracza 70. Jeśli spełniony jest warunek, średni procent czasu procesora dla ostatnich 5 minut przekracza 70, reguła alertu wyzwala aktywowane powiadomienie. W przypadku skonfigurowania w grupie akcji skojarzonej z regułą alertu wiadomości e-mail lub elementu webhook zostanie wyświetlone aktywne powiadomienie.

W przypadku korzystania z wielu warunków w jednej regule reguła "ands".  Oznacza to, że alert jest uruchamiany, gdy wszystkie warunki w alercie będą oceniane jako prawdziwe i rozwiązywane, gdy jeden z warunków nie ma już wartości true. Przykładem tego typu alertu będzie alert, gdy "procesor CPU większy niż 90%" i "Długość kolejki przekracza 300 elementów". 

### <a name="alert-rule-with-dynamic-condition-type"></a>Reguła alertu z typem warunku dynamicznego

Załóżmy, że utworzono prostą regułę alertu metryki dynamicznej wartości progowej:

- Zasób docelowy (zasób platformy Azure, który chcesz monitorować): myVM
- Metryka: procentowy procesor CPU
- Typ warunku: dynamiczny
- Agregacja czasu (statystyka, która jest uruchamiana za pośrednictwem wartości metryk nieprzetworzonych. Agregacje obsługiwanego czasu to min, Max, średnia, suma, liczba): Średnia
- Okres (wyszukanie okna, nad którym są sprawdzane wartości metryk): w ciągu ostatnich 5 minut
- Częstotliwość (częstotliwość, z jaką alert dotyczący metryki sprawdza, czy warunki są spełnione): 1 min
- Operator: większe niż
- Czułość: Średni
- Odszukaj okresy wstecz: 4
- Liczba naruszeń: 4

Po utworzeniu reguły alertu algorytm dynamicznej uczenia maszynowego uzyska dostępne dane historyczne, oblicza próg, który najlepiej pasuje do wzorca zachowania serii metryk i będzie stale uczyć się na podstawie nowych danych w celu utworzenia wartość progowa jest bardziej dokładna.

Od momentu utworzenia reguły alertu monitor jest uruchamiany co 1 min i sprawdza wartości metryk w ostatnich 20 minutach pogrupowane w 5 minut i sprawdza, czy średnia wartość okresu w każdym z 4 okresów przekracza oczekiwany próg. Jeśli spełniony jest warunek, średni procent czasu procesora w ostatnich 20 minutach (cztery 5 minut okresów) odbiegał od oczekiwanego zachowania czterokrotnie, reguła alertu wyzwala aktywowane powiadomienie. W przypadku skonfigurowania w grupie akcji skojarzonej z regułą alertu wiadomości e-mail lub elementu webhook zostanie wyświetlone aktywne powiadomienie.

### <a name="view-and-resolution-of-fired-alerts"></a>Wyświetlanie i rozwiązywanie wygenerowanego alertu

Powyższe przykłady reguł alertów można również wyświetlić w Azure Portal w bloku **wszystkie alerty** .

Załóżmy, że użycie "myVM" jest nadal powyżej progu w kolejnych sprawdzeniach, reguła alertu nie zostanie uruchomiona ponownie, dopóki te warunki nie zostaną rozwiązane.

Po pewnym czasie użycie na "myVM" powróci do normalnego (spadnie poniżej wartości progowej). Reguła alertu monitoruje warunek przez dwa razy, aby wysłać rozpoznane powiadomienie. Reguła alertu wysyła komunikat rozwiązany/zdezaktywowany, gdy warunek alertu nie jest spełniony przez trzy kolejne okresy, aby zmniejszyć hałas w przypadku warunków niestabilny.

Gdy rozpoznane powiadomienie jest wysyłane za pośrednictwem elementów webhook lub email, stan wystąpienia alertu (nazywany stanem monitora) w Azure Portal jest również ustawiany jako rozwiązany.

### <a name="using-dimensions"></a>Korzystanie z wymiarów

Alerty metryk w Azure Monitor obsługują również monitorowanie kombinacji wartości wymiarów wielu z jedną regułą. Rozważmy, dlaczego możesz użyć wielu kombinacji wymiarów z pomocą przykładu.

Załóżmy, że masz plan App Service dla witryny sieci Web. Chcesz monitorować użycie procesora CPU w wielu wystąpieniach z uruchomioną witryną sieci Web lub aplikacją. Można to zrobić za pomocą reguły alertu metryk w następujący sposób:

- Zasób docelowy: myAppServicePlan
- Metryka: procentowy procesor CPU
- Typ warunku: statyczny
- Wymiary
  - Wystąpienie = InstanceName1, InstanceName2
- Agregacja czasu: Średnia
- Okres: w ciągu ostatnich 5 minut
- Częstotliwość: 1 min
- Operator: GreaterThan
- Próg: 70

Tak jak wcześniej, ta reguła monitoruje, czy średnie użycie procesora CPU w ciągu ostatnich 5 minut przekracza 70%. Jednak za pomocą tej samej reguły można monitorować dwa wystąpienia z uruchomioną witryną sieci Web. Każde wystąpienie będzie monitorowane pojedynczo i będzie otrzymywać powiadomienia pojedynczo.

Załóżmy, że masz aplikację sieci Web, która widzi ogromne zapotrzebowanie i musisz dodać więcej wystąpień. Powyższa zasada nadal monitoruje tylko dwa wystąpienia. Można jednak utworzyć regułę w następujący sposób:

- Zasób docelowy: myAppServicePlan
- Metryka: procentowy procesor CPU
- Typ warunku: statyczny
- Wymiary
  - Wystąpienie = *
- Agregacja czasu: Średnia
- Okres: w ciągu ostatnich 5 minut
- Częstotliwość: 1 min
- Operator: GreaterThan
- Próg: 70

Ta reguła będzie automatycznie monitorować wszystkie wartości dla wystąpienia, np. wystąpienia można monitorować w miarę ich występowania, nie trzeba ponownie modyfikować reguły alertu metryki.

W przypadku monitorowania wielu wymiarów reguła alertów dynamicznych umożliwia tworzenie dostosowanych progów dla setek serii metrycznych w danym momencie. Progi dynamiczne mają mniejszą liczbę reguł alertów, które umożliwiają zarządzanie i znaczną oszczędność czasu na potrzeby zarządzania i tworzenia reguł alertów.

Załóżmy, że masz aplikację sieci Web z wieloma wystąpieniami i nie wiesz, co to jest najbardziej odpowiedni próg. Powyższe reguły zawsze będą używać progu 70%. Można jednak utworzyć regułę w następujący sposób:

- Zasób docelowy: myAppServicePlan
- Metryka: procentowy procesor CPU
- Typ warunku: dynamiczny
- Wymiary
  - Wystąpienie = *
- Agregacja czasu: Średnia
- Okres: w ciągu ostatnich 5 minut
- Częstotliwość: 1 min
- Operator: GreaterThan
- Czułość: Średni
- Odszukaj okresy wstecz: 1
- Liczba naruszeń: 1

Ta reguła monitoruje, czy średnie użycie procesora CPU w ciągu ostatnich 5 minut przekracza oczekiwane zachowanie dla każdego wystąpienia. Ta sama reguła pozwala monitorować wystąpienia w miarę ich występowania, nie trzeba ponownie modyfikować reguły alertu metryki. Każde wystąpienie uzyska próg, który pasuje do wzorca zachowania serii metryk i ciągle zmienia się w oparciu o nowe dane, aby zapewnić dokładniejszy próg. Podobnie jak przed, każde wystąpienie zostanie monitorowane pojedynczo i otrzymasz powiadomienia pojedynczo.

Rosnące okresy zawracania i liczby naruszeń mogą również umożliwić filtrowanie alertów tylko w przypadku definicji znaczącego odchylenia. [Dowiedz się więcej o opcjach zaawansowanych progów dynamicznych](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean).

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Monitorowanie na dużą skalę przy użyciu alertów metryk w Azure Monitor

Do tej pory dowiesz się, jak można użyć pojedynczego alertu metryki do monitorowania jednej lub wielu serii czasu metryk związanych z pojedynczym zasobem platformy Azure. Wiele razy można chcieć zastosować tę samą regułę alertu do wielu zasobów. Azure Monitor obsługuje również monitorowanie wielu zasobów przy użyciu jednej reguły alertu metryki. Ta funkcja jest obecnie obsługiwana tylko na maszynach wirtualnych. Ponadto pojedynczy alert dotyczący metryki może monitorować zasoby w jednym regionie świadczenia usługi Azure.

Zakres monitorowania można określić za pomocą pojedynczego alertu dotyczącego metryki na jeden z trzech sposobów:

- jako lista maszyn wirtualnych w jednym regionie świadczenia usługi Azure w ramach subskrypcji
- wszystkie maszyny wirtualne (w jednym regionie świadczenia usługi Azure) w jednej lub większej liczbie grup zasobów w subskrypcji
- wszystkie maszyny wirtualne (w jednym regionie świadczenia usługi Azure) w jednej subskrypcji

Tworzenie reguł alertów dotyczących metryk, które monitorują wiele zasobów, jest podobne do [tworzenia dowolnego innego alertu dotyczącego metryki](alerts-metric.md) monitorującego pojedynczy zasób. Tylko różnica polega na tym, że należy wybrać wszystkie zasoby, które mają być monitorowane. Te reguły można również utworzyć za poorednictwem [szablonów Azure Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-a-metric-alert-that-monitors-multiple-resources). Zostaną odebrane indywidualne powiadomienia dla każdej maszyny wirtualnej.

## <a name="typical-latency"></a>Typowe opóźnienia

W przypadku alertów dotyczących metryk, zazwyczaj otrzymasz powiadomienie w ciągu 5 minut, jeśli ustawisz częstotliwość reguły alertu na 1 min. W przypadku dużego obciążenia systemów powiadomień może pojawić się dłuższe opóźnienie.

## <a name="supported-resource-types-for-metric-alerts"></a>Obsługiwane typy zasobów dla alertów metryk

Pełną listę obsługiwanych typów zasobów można znaleźć w tym [artykule](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).


## <a name="next-steps"></a>Następne kroki

- [Dowiedz się, jak tworzyć i wyświetlać alerty metryk na platformie Azure oraz zarządzać nimi](alerts-metric.md)
- [Dowiedz się, jak wdrażać alerty metryk przy użyciu szablonów Azure Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [Dowiedz się więcej na temat grup akcji](action-groups.md)
- [Dowiedz się więcej o typie warunku progi dynamiczne](alerts-dynamic-thresholds.md)
