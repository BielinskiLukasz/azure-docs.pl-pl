---
title: Informacje o metrykach dla chmury wiosennej platformy Azure
description: Dowiedz się, jak przejrzeć metryki w chmurze Azure wiosennej
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.openlocfilehash: bb23afff2b4b449897d8e420934d038938d20205
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389563"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Informacje o metrykach dla chmury wiosennej platformy Azure

Eksplorator metryk platformy Azure to składnik Microsoft Azure Portal, który umożliwia Wykreślanie wykresów, wizualnie skorelowanych trendów oraz badanie skoków i wartości DIP w metrykach. Użyj Eksploratora metryk, aby zbadać kondycję i wykorzystanie zasobów. 

W chmurze Azure wiosną istnieją dwa punkty widoku dla metryk.
* Wykresy na każdej stronie przeglądu aplikacji
* Strona typowe metryki

 ![Wykresy metryk](media/metrics/metrics-1.png)

Wykresy w **przeglądzie** aplikacji zapewniają szybkie sprawdzanie stanu dla każdej aplikacji. Strona typowe **metryki** zawiera wszystkie metryki dostępne dla odwołania. Możesz utworzyć własne wykresy na stronie typowe metryki i przypiąć je na pulpicie nawigacyjnym.

## <a name="application-overview-page"></a>Strona przeglądu aplikacji
Wybierz aplikację w obszarze **Zarządzanie aplikacjami** , aby znaleźć wykresy na stronie Przegląd.  

 ![Zarządzanie metrykami aplikacji](media/metrics/metrics-2.png)

Na stronie **przeglądu aplikacji** każdej aplikacji jest prezentowany wykres metryk, który umożliwia wykonywanie szybkiego sprawdzania stanu aplikacji.  

 ![Przegląd metryk aplikacji](media/metrics/metrics-3.png)

Chmura sprężynowa Azure udostępnia te pięć wykresów z metrykami, które są aktualizowane co minutę:

* **Błędy serwera http**: liczba błędów dla żądań HTTP do aplikacji
* **Dane w**: bajty odebrane przez aplikację
* **Dane wychodzące**: Bajty wysłane przez aplikację
* **Żądania**: żądania odebrane przez aplikację
* **Średni czas odpowiedzi**: Średni czas odpowiedzi z aplikacji

Dla wykresu można wybrać zakres czasu z przedziału od 1 do siedmiu dni.

## <a name="common-metrics-page"></a>Strona typowe metryki

**Metryki** w okienku nawigacji po lewej stronie łączą się ze wspólną metryką.

Najpierw wybierz metryki do wyświetlenia:

![Wybierz widok metryki](media/metrics/metrics-4.png)

Szczegóły wszystkich opcji metryk można znaleźć w [sekcji](#user-metrics-options) poniżej.

Następnie wybierz typ agregacji dla każdej metryki:

![Agregacja metryk](media/metrics/metrics-5.png)

Typ agregacji wskazuje sposób agregowania punktów metryk na wykresie według czasu. Istnieje jeden nieprzetworzony punkt metryki co minutę, a typ przed agregacją w minutach jest wstępnie zdefiniowany przez typ metryki.
* Sum: Sumuj wszystkie wartości jako docelowe dane wyjściowe.
* Średnia: Użyj średniej wartości w okresie jako docelowy wynik.
* Max/min: Użyj wartości max/min w okresie jako docelowego wyjścia.

Można również zmodyfikować zakres czasu do pokazania. Zakres czasu można wybrać z ostatnich 30 minut do ostatnich 30 dni lub z niestandardowym zakresem czasu.

![Modyfikacja metryki](media/metrics/metrics-6.png)

Widok domyślny obejmuje wszystkie metryki application's usługi chmurowej platformy Azure ze sobą. Metryki jednej aplikacji lub wystąpienia można filtrować na ekranie.  Kliknij przycisk **Dodaj filtr**, ustaw właściwość na wartość **aplikacja**i wybierz aplikację docelową, którą chcesz monitorować, w polu tekstowym **wartości** . 

Można użyć dwóch rodzajów filtrów (właściwości):
* Aplikacja: Filtruj według nazwy aplikacji
* Wystąpienie: Filtruj według wystąpienia aplikacji

![Filtry metryk](media/metrics/metrics-7.png)

Można również użyć opcji **Zastosuj dzielenie** , która spowoduje narysowanie wielu wierszy dla jednej aplikacji:

![Podział metryk](media/metrics/metrics-8.png)

>[!TIP]
> Możesz tworzyć własne wykresy na stronie metryk i przypinać je do **pulpitu nawigacyjnego**. Zacznij od zmiany nazwy wykresu.  Następnie wybierz pozycję **Przypnij do pulpitu nawigacyjnego w prawym górnym rogu**. Teraz możesz sprawdzić swoją aplikację na **pulpicie nawigacyjnym**portalu.

## <a name="user-metrics-options"></a>Opcje metryk użytkownika

W poniższych tabelach przedstawiono dostępne metryki i szczegóły.

### <a name="error"></a>Błąd
>[!div class="mx-tdCol2BreakAll"]
>| Name (Nazwa) | Nazwa metryki siłownika | Jednostka | Szczegóły |
>|----|----|----|------------|
>| Błąd globalny Tomcat | Tomcat. Global. Error | Licznik | Liczba błędów przetworzonych żądań |

### <a name="performance"></a>Wydajność
>[!div class="mx-tdCol2BreakAll"]
>| Name (Nazwa) | Nazwa metryki siłownika | Jednostka | Szczegóły |
>|----|----|----|------------|
>|Procent użycia procesora systemu | System. CPU. Usage | Procent | Ostatnie użycie procesora CPU całego systemu. Ta wartość jest podwójnym interwałem [0.0, 1.0]. Wartość 0,0 oznacza, że wszystkie procesory były bezczynne w trakcie ostatniego zaobserwowanego okresu, a wartość 1,0 oznacza, że wszystkie procesory aktywnie działały 100% czasu w trakcie ostatniego zaobserwowanego okresu.|
>| Procent użycia procesora aplikacji | Procent użycia procesora aplikacji | Procent | Ostatnie użycie procesora CPU przez proces wirtualna maszyna Java. Ta wartość jest podwójnym interwałem [0.0, 1.0]. Wartość 0,0 oznacza, że żaden z procesorów CPU nie był uruchomiony z procesu JVM w trakcie ostatniego zaobserwowanego okresu, a wartość 1,0 oznacza, że wszystkie procesory aktywnie działały wątki z JVM 100% czasu podczas ostatniego zaobserwowanego okresu. Wątki z JVM obejmują wątki aplikacji oraz wewnętrzne wątki JVM.|
>| Przypisana pamięć aplikacji | JVM. Memory. Committed | Bajty | Reprezentuje ilość pamięci, która ma być dostępna do użycia przez JVM. JVM może zwolnić pamięć do systemu, a zatwierdzenie może być mniejsze niż init. wartość zatwierdzone będzie zawsze większa lub równa używanej. |
>| Użyta pamięć aplikacji | JVM. Memory. użyty | Bajty | Reprezentuje ilość pamięci aktualnie używaną w bajtach. |
>| Maksymalna ilość pamięci aplikacji | JVM. Memory. Max | Bajty | Reprezentuje maksymalną ilość pamięci, która może być używana do zarządzania pamięcią. Ilość użytych i przekazanych pamięci będzie zawsze mniejsza lub równa Max, jeśli zdefiniowano wartość Max. Alokacja pamięci może zakończyć się niepowodzeniem, jeśli próbuje zwiększyć użytą pamięć, taką jak > zatwierdzone, nawet jeśli użyto < = Max (na przykład gdy w systemie brakuje pamięci wirtualnej). |
>| Maksymalna dostępna wartość starego rozmiaru danych generacji | JVM. GC. max. Data. size | Bajty | Szczytowe użycie pamięci przez starszą pulę pamięci generacji od momentu uruchomienia maszyny wirtualnej Java. |
>| Stary rozmiar danych generacji | JVM. GC. Live. Data. size | Bajty | Rozmiar starej puli pamięci generacji po pełnej operacji GC. |
>| Podwyższ poziom do starego rozmiaru danych generacji | JVM. GC. Memory. awansowana | Bajty | Liczba pozytywnych wzrostów rozmiaru starej puli pamięci generacji przed GC do po GC. |
>| Podwyższ poziom do rozmiaru danych dla małych generacji | JVM. GC. Memory. przydzielono | Bajty | Zwiększa się w celu zwiększenia rozmiaru puli pamięci młodej generacji po jednej operacji GC do wcześniejszej. |
>| Liczba wstrzymań GC | JVM. GC. Pause (łącznie-Count) | Licznik | Łączna liczba GC po rozpoczęciu tego JMV, w tym w przypadku młodych i starych wykazów GC. |
>| Całkowity czas wstrzymania odzyskiwania pamięci | JVM. GC. Pause (całkowity czas) | MS | Łączny czas, zużyty przez GC po rozpoczęciu tego JMV, w tym dla młodych i starych WYKAZów. |

### <a name="request"></a>Żądanie
>[!div class="mx-tdCol2BreakAll"]
>| Name (Nazwa) | Nazwa metryki siłownika | Jednostka | Szczegóły |
>|----|----|----|------------|
>| Całkowita liczba wysłanych bajtów Tomcat | Tomcat. Global. sent | Bajty | Liczba wysłanych serwerów sieci Web Tomcat danych |
>| Całkowita liczba odebranych bajtów Tomcat | Tomcat. Global. Receives | Bajty | Liczba odebranych danych Tomcat serwera sieci Web |
>| Łączny czas żądania Tomcat | Tomcat. Global. Request (łącznie-Time) | MS | Łączny czas przetwarzania żądań przez serwer sieci Web Tomcat |
>| Łączna liczba żądań Tomcat | Tomcat. Global. Request (Total-Count) | Licznik | Łączna liczba żądań przetworzonych przez serwer sieci Web Tomcat |
>| Maksymalny czas żądania Tomcat | Tomcat. Global. Request. Max | MS | Maksymalny czas przetwarzania żądania przez serwer sieci Web Tomcat |

### <a name="session"></a>Sesja
>[!div class="mx-tdCol2BreakAll"]
>| Name (Nazwa) | Nazwa metryki siłownika | Jednostka | Szczegóły |
>|----|----|----|------------|
>| Maksymalna liczba aktywnych sesji Tomcat | Tomcat. Sessions. Active. Max | Licznik | Maksymalna liczba sesji, które były aktywne w tym samym czasie |
>| Maksymalny czas aktywności sesji Tomcat | Tomcat. Sessions. Alive. Max | MS | Najdłuższy czas (w sekundach) aktywności wygasłej sesji |
>| Liczba utworzonych sesji Tomcat | Tomcat. Sessions. Created | Licznik | Liczba utworzonych sesji |
>| Liczba wygasłych sesji Tomcat | Tomcat. Sessions. wygasła | Licznik | Liczba wygasłych sesji |
>| Liczba odrzuconych sesji Tomcat | Tomcat. Sessions. rejected | Licznik | Liczba sesji, które nie zostały utworzone, ponieważ osiągnięto maksymalną liczbę aktywnych sesji. |

## <a name="see-also"></a>Zobacz także
* [Wprowadzenie do Eksploratora metryk platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [Analizowanie dzienników i metryk przy użyciu ustawień diagnostycznych](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>Następne kroki
* [Samouczek: monitorowanie źródeł wiosennych w chmurze przy użyciu alertów i grup akcji](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Limity przydziału i plany usług dla chmury wiosennej platformy Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

