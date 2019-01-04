---
title: Alerty dzienników w usłudze Azure Monitor
description: Wyzwalanie wiadomości e-mail, powiadomień, wywołaj adresów URL witryn sieci Web (elementy webhook), oraz automatyzacją po spełnieniu warunki zapytania analityczne, które określisz dla alertów platformy Azure.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: b3949fefac1cc230a98687b3b5ff9c7a01c6a0e9
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/27/2018
ms.locfileid: "53789580"
---
# <a name="log-alerts-in-azure-monitor"></a>Alerty dzienników w usłudze Azure Monitor
Ten artykuł zawiera szczegółowe informacje o alertów dzienników są jednym z typów alertów, które obsługują [Azure Alerts](../../azure-monitor/platform/alerts-overview.md) i zezwolić użytkownikom na stosowanie platforma analiz platformy Azure jako podstawa dla alertów.

Alert dziennika składa się z wyszukiwania w dziennikach reguł utworzonych dla [usługi Azure Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md) lub [usługi Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostic-events). Aby dowiedzieć się więcej na temat jej użycia, zobacz [Tworzenie alertów dziennika na platformie Azure](../../azure-monitor/platform/alerts-log.md)

> [!NOTE]
> Dane dzienników popularnych z [usługi Azure Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md) jest obecnie również dostępna na platformie metryki w usłudze Azure Monitor. Dla widoku szczegółów [alertu metryki dla dzienników](../../azure-monitor/platform/alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Wyszukiwania reguł alertów dzienników — definicja i typów

Reguły przechowywania dzienników są tworzone przez usługę Azure Alerts w celu automatycznego wykonywania określonych zapytań dotyczących dzienników w regularnych odstępach czasu.  Jeśli wyniki zapytania pasują do określonych kryteriów, jest tworzony rekord alertu. Reguła może wtedy automatycznie uruchomić jedną lub więcej akcji przy użyciu [grup akcji](../../azure-monitor/platform/action-groups.md). 

Dziennik wyszukiwania reguł są definiowane przez następujące informacje:
- **Zaloguj się zapytania**.  Zapytanie, które jest uruchamiane za każdym razem, gdy reguła alertu jest uruchamiana.  Rekordów zwróconych przez tę kwerendę są używane do określenia, czy alert jest tworzony. Zapytania usługi Analytics może również obejmować [wywołań między platformami](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery), [wielu wywołań obszaru roboczego i [wywołań między zasobami](../../azure-monitor/log-query/cross-workspace-query.md) podany użytkownik ma wystarczające uprawnienia dostępu do aplikacji zewnętrznych. 

    > [!IMPORTANT]
    > Użytkownik musi mieć [Współautor monitorowania Azure](../../azure-monitor/platform/roles-permissions-security.md) roli do tworzenia, modyfikowania i aktualizowanie alerty dzienników w usłudze Azure Monitor; uzyskasz także dostęp & zapytanie praw wykonywania dla analizy elementy docelowe w reguły alertu lub zapytanie alertu. Jeśli tworzenie użytkownika nie ma dostępu do wszystkich cele analytics reguły alertu lub zapytanie alertu — tworzenie reguł może zakończyć się niepowodzeniem lub reguł alertów dzienników będą wykonywane przy użyciu wyniki częściowe.

- **Przedział czasu**.  Określa zakres czasu dla zapytania. Zapytanie zwraca tylko rekordy utworzone w tym zakresie czasu bieżącego. Okres ogranicza dane pobrana dla zapytania dotyczącego dziennika zapobiec nadużyciu i zmierzone dowolnego polecenia na czas (np. temu) używanych w zapytaniu dziennika. <br>*Na przykład jeśli okres czasu jest ustawiony na 60 minut, a zapytanie jest uruchomione o 13:15, tylko rekordy utworzone z zakresu od 12:15:00 do 13:15 czasu jest zwracana do wykonania zapytania dotyczącego dziennika. Jeśli zapytanie dziennika korzysta z czasu polecenia, takie jak temu teraz (7 dni), zapytania dotyczącego dziennika zostanie uruchomione tylko dla danych zakresu od 12:15:00 do 13:15:00 - tak, jakby istnieją dane dla ostatnich 60 minut. A nie przez siedem dni, danych, jak to określono w dzienniku zapytań.*
- **Częstotliwość**.  Określa, jak często należy uruchamiać zapytanie. Może to być wartość z zakresu od 5 minut do 24 godzin. Powinna być równa lub mniejsza niż okres czasu.  Jeśli wartość jest większa niż okres czasu, istnieje ryzyko rekordów jest pominięte.<br>*Na przykład należy wziąć pod uwagę okres 30 minut i częstotliwość 60 minut.  Jeśli zapytanie jest uruchomione 1:00, zwraca rekordy od 12:30 do 1:00 PM.  Przy następnym uruchomić zapytanie jest 2:00, jeśli zwróci rekordów między 1:30 – 2:00.  Nigdy nie oceniono rekordy między 1:00 i 1:30.*
- **Próg**.  Wyniki przeszukiwania dzienników są oceniane w celu ustalenia, czy należy utworzyć alert.  Próg różni się dla różnych typów reguł alertów wyszukiwania dziennika.

Reguły wyszukiwania dziennika można je [usługi Azure Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md) lub [usługi Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostic-events), mogą być dwojakiego rodzaju. Każdy z tych typów opisano szczegółowo w kolejnych sekcjach.

- **[Liczba wyników](#number-of-results-alert-rules)**. Pojedynczy alert utworzony, jeśli liczba rekordów zwracanych przez wyszukiwanie w dzienniku przekracza określoną liczbę.
- **[Pomiar metryki](#metric-measurement-alert-rules)**.  Alert został utworzony dla każdego obiektu w wynikach wyszukiwania dziennika z wartościami, które przekracza określoną wartość progową.

Dostępne są następujące różnice między typami reguły alertu.

- *Liczba wyników* reguł alertów zawsze tworzy chwilę alertów, pojedynczy *pomiar metryki* alertu zasada tworzy alert dla każdego obiektu, który przekracza wartość progową.
- *Liczba wyników* reguł alertów Tworzenie alertu, po przekroczeniu progu jeden raz. *Pomiar metryki* reguł alertów można utworzyć alertu, po przekroczeniu progu pewną liczbę uruchomień w określonym interwale.

### <a name="number-of-results-alert-rules"></a>Liczba wyników reguły alertów
**Liczba wyników** reguł alertów Utwórz pojedynczy alert, gdy liczba rekordów zwróconych przez zapytanie wyszukiwania przekracza określoną wartość progową. Ten typ alertu jest idealny dla pracy z wydarzeniami takimi jak Windows, dzienniki zdarzeń, usługi Syslog, odpowiedzi aplikacji sieci Web i dzienników niestandardowych.  Można utworzyć alert, gdy tworzony zdarzeń określony błąd lub wielu zdarzeń błędu są tworzone w określonym przedziale czasu.

**Próg**: Próg liczby reguł alertów wyników jest większa lub mniejsza od określonej wartości.  Jeśli liczba rekordów zwracanych przez wyszukiwanie w dzienniku odpowiadają te kryteria, jest tworzony alert.

Aby otrzymywać alerty na pojedyncze zdarzenie, ustaw liczbę wyników na wartość większą niż 0, a następnie wyszukaj wystąpienie pojedyncze zdarzenie, który został utworzony od czasu ostatniego uruchomienia zapytania. Niektóre aplikacje mogą rejestrować okazjonalne błąd, który nie należy koniecznie zgłoszenie alertu.  Na przykład aplikacja może ponów próbę wykonania procesu, który utworzył zdarzenie błędu i kończyło się powodzeniem następnym razem.  W tym przypadku nie można utworzyć alert, chyba że wiele zdarzeń zostaje utworzonych w określonym przedziale czasu.  

W niektórych przypadkach warto utworzyć alert w przypadku braku zdarzenia.  Na przykład procesu mogą rejestrować regularne zdarzenia w celu wskazania, że działa prawidłowo.  Jeżeli nie jego wylogować jedno z tych zdarzeń w danym okresie, powinien zostać utworzony alert.  W takim przypadku należy ustawić wartość progową **mniej niż 1**.

#### <a name="example-of-number-of-records-type-log-alert"></a>Przykład alertu dziennika typu Liczba rekordów
Rozważmy scenariusz, w którym chcesz wiedzieć, kiedy aplikacji opartych na sieci web zapewnia odpowiedzi dla użytkowników z kodem 500 (to znaczy) wewnętrzny błąd serwera. Należy utworzyć regułę alertu z następującymi szczegółami:  
- **Zapytanie:** żądań | gdzie resultCode == "500"<br>
- **Okres:** 30 minut<br>
- **Częstotliwość alertów:** pięć minut<br>
- **Wartość progowa:** Większa niż 0<br>

Alert będzie uruchomi zapytanie co 5 minut, 30 minut danych — do wyszukiwania dowolnego rekordu, gdzie kod wyniku: 500. Jeśli jeszcze jeden taki rekord zostanie znaleziony, wyzwala alert i wyzwala akcję skonfigurowane.

### <a name="metric-measurement-alert-rules"></a>Reguły alertu pomiaru metryki

- **Pomiar metryki** reguł alertów Utwórz alert dla każdego obiektu w zapytaniu o wartości, który przekroczy określony próg.  Mają one różne następujące różnice względem **liczba wyników** reguły alertów.
- **Funkcję agregacji**: Określa, obliczenia, które jest wykonywane i potencjalnie liczbową pola do zagregowania.  Na przykład **count()** zwraca liczbę rekordów w zapytaniu **avg(CounterValue)** zwraca średnią pola CounterValue dla interwału. Funkcja agregująca w zapytaniu muszą być o nazwie wywoływana: Elementy AggregatedValue i podaj wartość liczbową. 
- **Grupy pól**: Rekord z wartością zagregowaną jest tworzony dla każdego wystąpienia to pole, a alert jest generowany dla każdego.  Na przykład, jeśli chcesz generować alert dla każdego komputera, można użyć **według komputera** 

    > [!NOTE]
    > Dla pomiar metryki reguł alertów, które są oparte na usłudze Application Insights można określić pole do grupowania danych. Aby to zrobić, należy użyć **agregowane na** opcja w definicji reguły.   
    
- **Interwał**:  Definiuje przedział czasu, przez który dane są agregowane.  Na przykład, jeśli określono **pięć minut**, może zostać utworzony rekord dla każdego wystąpienia pola grupy agregowane w 5-minutowych interwałach w przedziale czasu określony dla alertu.

    > [!NOTE]
    > Funkcja bin musi używane w zapytaniu, aby określić interwał. Jako bin() może spowodować nierówne interwałami — Alert zostaną automatycznie przekonwertowane bin polecenia polecenie bin_at z odpowiednim czasie w czasie wykonywania, aby upewnić się, wyniki ze stałym punktem. Typ metryki pomiaru alertu dziennika jest przeznaczona do pracy z zapytaniami o pojedynczej bin() polecenia
    
- **Próg**: Próg reguły alertu pomiaru metryki jest definiowany przez wartości zagregowanej i liczbę naruszeń.  Dowolny punkt danych podczas wyszukiwania dziennika przekracza tę wartość, jest uznawane za naruszenia.  Jeśli liczba naruszeń w dla dowolnego obiektu w wynikach przekracza określoną wartość, alert zostanie utworzony dla tego obiektu.

#### <a name="example-of-metric-measurement-type-log-alert"></a>Przykład alertu dziennika typu pomiar metryki
Rozważmy scenariusz, w którym alert potrzebowała przekroczeniu użycie procesora przez 90% trzykrotnie ponad 30 minut, przez dowolny komputer.  Należy utworzyć regułę alertu z następującymi szczegółami:  

- **Zapytanie:** Wydajności | Gdzie ObjectName == "Procesor" i CounterName == "% czasu procesora" | summarize AggregatedValue = avg(CounterValue) przez bin (TimeGenerated, 5 min), komputer<br>
- **Okres:** 30 minut<br>
- **Częstotliwość alertów:** pięć minut<br>
- **Wartość agregacji:** Większa niż 90<br>
- **Wyzwalaj alert na podstawie:** Łączna liczba naruszeń większej niż 2<br>

Zapytanie spowodowałoby średnią wartość dla każdego komputera w 5-minutowych odstępach czasu.  To zapytanie zostałoby uruchomione co 5 minut przez dane zebrane przez poprzednie 30 minut.  Poniżej przedstawiono przykładowe dane na trzech komputerach.

![Przykładowe wyniki zapytania](media/alerts-unified-log/metrics-measurement-sample-graph.png)

W tym przykładzie oddzielne alerty zostałyby utworzone srv02 i srv03, ponieważ ich naruszenia 90% progu trzy razy w okresie czasu.  Jeśli **Wyzwalaj alert na podstawie:** zostały zmienione na **kolejno** , a następnie będzie można utworzyć alertu tylko w przypadku srv03, ponieważ jego naruszenia progu dla trzech kolejnych próbek.

## <a name="log-search-alert-rule---firing-and-state"></a>Wyszukiwania reguł alertów dzienników — uruchomieniu którego i stanu
Reguły alertów wyszukiwania dziennika działa na logice uzależniona przez użytkownika, zgodnie z konfiguracji i zapytania analizy niestandardowych używane. Od logiki dokładny stan lub powód, dlaczego należy reguła alertów wyzwalacza jest hermetyzowany w zapytania usługi Analytics — które mogą się różnić w poszczególnych reguł alertów dzienników. Alertów platformy Azure ma ograniczone informacje o określonej podstawowej przyczyny wewnątrz wyników dziennika, gdy warunek próg reguły alertów wyszukiwania dziennika jest osiągnięciu lub przekroczeniu. Dlatego alertów dzienników są określane na jak stanu bez i nastąpi za każdym razem, gdy wyniki wyszukiwania dziennika jest wystarczająca do przekracza próg określony w alertów dzienników z *liczba wyników* lub *pomiar metryki* typu warunku. I reguł alertów dzienników będą stale zachować wyzwalania, tak długo, jak warunek alertu jest spełnione dzięki wynik zapytania analizy niestandardowych; bez alert co wprowadzenie rozwiązane. Jako logikę dokładne przyczyny niepowodzenia monitorowania są wyświetlane jako znaki wewnątrz zapytania usługi analytics, dostarczone przez użytkownika; istnieje nie oznacza, że przez alerty platformy Azure do ostatecznie ustalić, czy wyniki wyszukiwania dziennika nie spełniają próg wskazuje rozwiązania problemu.

Teraz załóżmy mamy reguł alertów dzienników o nazwie *Contoso-dziennika — Alert*, ponieważ na konfiguracji w [przykładu przewidzianego dla alertu dziennika typu Liczba wyników](#example-of-number-of-records-type-log-alert). 
- O 13:05 po Contoso dziennika alertów została wykonana przez alertów platformy Azure wyniki wyszukiwania dziennika uzyskane rekordy 0; poniżej progu i dlatego nie wyzwalania alertu. 
- W następnej iteracji o 13:10 podczas Contoso dziennika alertów została wykonana przez alertów platformy Azure wyniki wyszukiwania dziennika podane rekordy 5; przekracza wartość progową i wyzwalania alertów, wkrótce po wyzwalając [grupy akcji](../../azure-monitor/platform/action-groups.md) skojarzone. 
- O 13:15 po Contoso dziennika alertów została wykonana przez alertów platformy Azure wyniki wyszukiwania dziennika podane rekordy 2; przekracza wartość progową i wyzwalania alertów, wkrótce po wyzwalając [grupy akcji](../../azure-monitor/platform/action-groups.md) skojarzone.
- Teraz w następnej iteracji o 13:20 po Contoso-dziennika — Alert został wykonany przez alert dotyczący platformy Azure, wyniki wyszukiwania dziennika udostępniany ponownie 0 rekordów poniżej progu i dlatego nie wyzwalania alertu.

Jednak w przypadku wymienionych powyżej o 13:15 - alertów platformy Azure nie może określić, podstawowych problemów, które wystąpienie 1:10 zostaną zachowane, a jeśli jest netto nowe błędy; kwerendy dostarczone przez użytkownika może biorąc pod uwagę wcześniej rekordów - alertów platformy Azure mogą być się. Dlatego aby błąd po stronie ostrożnie, Contoso-dziennika — Alert jest wykonywany o 13:15, skonfigurowany [grupy akcji](../../azure-monitor/platform/action-groups.md) jest uruchamiany ponownie. Teraz o 13:20 Jeśli żadne rekordy nie są widoczne — alertów platformy Azure nie może być pewność, że przyczyną rekordy ma został rozwiązany; Dlatego Contoso-dziennika — Alert będzie nie zmieni się na rozwiązany w pulpit nawigacyjny alertów platformy Azure i/lub powiadomienia wysłane z informacją, rozpoznawanie alertu.


## <a name="pricing-and-billing-of-log-alerts"></a>Cennik i rozliczenia alertów dziennika
Ceny dla dziennika alertów jest podany w [cennik usługi Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) strony. W platformie Azure są naliczane, alertów dzienników są reprezentowane jako typ `microsoft.insights/scheduledqueryrules` za pomocą:
- Alerty dzienników w usłudze Application Insights wyświetlane z dokładną nazwę alertu wraz z grupy zasobów i właściwości alertu
- Zaloguj się alerty w usłudze Log Analytics widoczna nazwa alertu jako `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` wraz z grupy zasobów i właściwości alertu

    > [!NOTE]
    > Nazwy wszystkich zapisanych wyszukiwań, harmonogramy i działań utworzonych za pomocą interfejsu API programu Log Analytics musi być pisane małymi literami. Jeśli występują nieprawidłowe znaki takie jak `<, >, %, &, \, ?, /` są używane — zostaną one zastąpione przy użyciu `_` na rachunku.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [Tworzenie alertów dziennika na platformie Azure](../../azure-monitor/platform/alerts-log.md).
* Zrozumienie [elementami webhook w dzienniku alertów na platformie Azure](alerts-log-webhook.md).
* Dowiedz się więcej o [alertów platformy Azure](../../azure-monitor/platform/alerts-overview.md).
* Dowiedz się więcej o [usługi Application Insights](../../azure-monitor/app/analytics.md).
* Dowiedz się więcej o [usługi Log Analytics](../../azure-monitor/log-query/log-query-overview.md).    
