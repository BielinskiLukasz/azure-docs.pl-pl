---
title: Przegląd alertów i powiadomień, monitorowanie na platformie Azure
description: Przegląd alertów na platformie Azure. Alerty, alertów klasycznych, interfejs alertów.
author: rboucher
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: 7565c536b5d24c859b164a960f74bd1e2ce97b63
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960638"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Przegląd alertów na platformie Microsoft Azure 

W tym artykule opisano, jakie alerty są korzyści z ich i jak rozpocząć korzystanie z nich.  


## <a name="what-are-alerts-in-microsoft-azure"></a>Co to są alerty na platformie Microsoft Azure?
Aktywne alerty powiadamiają, gdy jest to ważne, że warunki znajdują się w danych monitorowania. Umożliwiają one zidentyfikować i rozwiązać problemy, zanim użytkownicy systemu, zwróć uwagę, ich. 

W tym artykule omówiono ujednoliconego środowiska alertów w usłudze Azure Monitor, który teraz zawiera usługi Log Analytics i usługi Application Insights. [Poprzednie środowisko alertów](monitoring-overview-alerts.md)i typy alertów są nazywane **alertów klasycznych**. Ta starsza obsługa produktu próbnego oraz starsze typu alertu możesz wyświetlić, klikając **wyświetlanie alertów klasycznych** w górnej części strony alertu. 


## <a name="overview"></a>Przegląd

Na poniższym diagramie reprezentuje ogólne warunki i przepływ alertów. 

![Przepływ alertów](media/monitoring-overview-alerts/Azure-Monitor-Alerts.svg)

Reguły alertów są oddzielone od alertów i akcji, które są wykonywane, gdy zostanie wyzwolony alert. 

- **Reguła alertu** -reguły alertu powoduje przechwycenie obiektu docelowego i kryteria dotyczące alertów. Reguła alertu może być w stanie wyłączone lub włączone. Alerty były uruchamiane tylko wtedy, gdy włączone. Najważniejsze atrybuty reguły alertów są:
    - **Zasób docelowy** — element docelowy może być dowolnym zasobem platformy Azure. Zasób docelowy definiuje zakres i sygnałów, które są dostępne dla alertów. Przykład elementów docelowych: maszynę wirtualną, konta magazynu, zestaw skalowania maszyn wirtualnych, obszar roboczy usługi Log Analytics lub zasób usługi Application Insights. W przypadku niektórych zasobów (np. maszyn wirtualnych), można określić wiele zasobów jako obiekt docelowy reguły alertu.
    - **Sygnał** — sygnalizuje są emitowane przez zasób docelowy i może mieć kilka typów. Metryki, działanie dziennika, Application Insights i Log.
    - **Kryteria** — kryteria jest kombinacją sygnał i logiki zastosowany do zasobu docelowego. Przykłady: 
         - Procentowe użycie procesora CPU > 70%
         - Czas odpowiedzi serwera > 4 ms 
         - Liczba wyników zapytania dziennika > 100
- **Nazwa alertu** — specyficzna nazwa dla reguły alertu, skonfigurowany przez użytkownika
- **Opis alertu** — opis reguły alertu, skonfigurowany przez użytkownika
- **Ważność** — ważność alertu, gdy są spełnione kryteria określone w regule alertu. Ważność może wynosić od 0 do 4.
- **Akcja** — określonych działań po wyzwoleniu alertu. Aby uzyskać więcej informacji zobacz grupy akcji.

## <a name="what-you-can-alert-on"></a>Co można alert dla

Alerty dotyczące metryk i dzienników zgodnie z opisem w [monitorowanie źródła danych](monitoring-data-sources.md). Obejmują one nie są ograniczone do:
- Wartości metryk
- Zapytania wyszukiwania w Dzienniku
- Zdarzenia dziennika aktywności
- Kondycja podstawowej platformy Azure
- Testy dostępności witryny sieci web



## <a name="manage-alerts"></a>Zarządzanie alertami
Można ustawić stan alertu, aby określić, gdzie są dostępne w procesie rozpoznawania. Gdy kryteria określone w regule alertu zostaną spełnione, alert jest tworzony lub uruchamiany, ma ona stan *New*. Stan można zmienić, gdy użytkownik potwierdza alertu i po zamknięciu. Wszystkie zmiany stanu są przechowywane w historii alertu.

Obsługiwane są następujące stany alertów.

| Stan | Opis |
|:---|:---|
| Nowa | Problem został wykryty i jeszcze nie została sprawdzona. |
| Potwierdzono | Administrator przejrzał alertu i do pracy w niej. |
| Zamknięte | Problem został rozwiązany. Po zamknięciu alertu możesz otworzyć go ponownie, zmieniając go do innego stanu. |

Stan alertu jest inny niż warunek monitora. Stan alertu jest ustawiony przez użytkownika i jest niezależna od warunek monitora. Gdy warunek wyzwolenia alertu czyści, warunek monitora alertu ustawiono rozwiązane. Mimo że system można ustawić warunek monitora rozwiązany, stan alertu nie jest zmieniany, do momentu zmiany przez użytkownika. Dowiedz się, [jak zmienić stan alertów i grup inteligentne](https://aka.ms/managing-alert-smart-group-states).

## <a name="smart-groups"></a>Inteligentne grup 
Inteligentne grupy są w wersji zapoznawczej. 

Inteligentne grupy są agregacji alerty oparte na algorytmów uczenia maszynowego, które mogą ułatwić ograniczenia liczby niepotrzebnych alertów i pomocy w rozwiązywania problemów. [Dowiedz się więcej na temat grup inteligentne](https://aka.ms/smart-groups) i [jak zarządzać grupami inteligentne](https://aka.ms/managing-smart-groups).


## <a name="alerts-experience"></a>Środowisko alertów 
Na stronie alertów domyślny zawiera podsumowanie alertów, które są tworzone w danym przedziale czasu. Wyświetla łączna liczba alertów w przypadku każdej wagi z kolumnami, które identyfikują łączną liczbę alertów w każdym stanie w przypadku każdej wagi. Wybierz dowolne ważności, aby otworzyć [wszystkie alerty](#all-alerts-page) strona jest filtrowana według tego ważności.

Pokaż lub nie Śledź starsze [alertów klasycznych](#classic-alerts). Można zmienić subskrypcje lub filtrować parametrów do aktualizacji strony. 

![Strona alertów](media/monitoring-overview-alerts/alerts-page.png)

Ten widok można filtrować, wybierając wartości w menu rozwijanych w górnej części strony.

| Kolumna | Opis |
|:---|:---|
| Subskrypcja | Wybierz maksymalnie pięć subskrypcji platformy Azure. Tylko w wybranych subskrypcjach są uwzględniane alerty w widoku. |
| Grupa zasobów | Wybierz pojedynczą grupę zasobów. Tylko alerty z usługą obiektów docelowych w wybranej grupie zasobów znajdują się w widoku. |
| Przedział czasu | Tylko alerty wyzwalane w przedziale czasu wybranego znajdują się w widoku. Obsługiwane wartości to ostatniej godziny, ostatnich 24 godzin, w ciągu ostatnich 7 dni i ostatnich 30 dni. |

Wybierz następujące wartości w górnej części strony alerty, aby otworzyć innej strony.

| Wartość | Opis |
|:---|:---|
| Łączna liczba alertów | Całkowita liczba alertów, które spełniają wybrane kryteria. Wybierz tę wartość, aby otworzyć widok wszystkich alertów z żadnego filtru. |
| Inteligentne grup | Całkowita liczba grup inteligentne, które zostały utworzone przy użyciu alertów, które spełniają wybrane kryteria. Wybierz tę wartość, aby otworzyć listę grup inteligentne w widoku wszystkie alerty.
| Łączna liczba reguł alertów | Całkowita liczba reguł alertów w wybranej subskrypcji i grupie zasobów. Wybierz tę wartość, aby otworzyć widok reguł filtrowane według wybranej subskrypcji i grupie zasobów.


## <a name="manage-alert-rules"></a>Zarządzaj regułami alertów
Kliknij pozycję **Zarządzaj regułami alertów** pokazanie **reguły** strony. **Reguły** jest w jednym miejscu na potrzeby zarządzania wszystkich reguł alertów w subskrypcji platformy Azure. On zawiera listę wszystkich reguł alertów i może być sortowane w oparciu o zasoby docelowe, grupy zasobów, nazwę reguły lub stanu. Reguły alertów można również można edytować, włączone lub wyłączone na tej stronie.  

 ![reguły alertów](./media/monitoring-overview-alerts/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Tworzenie reguły alertu
Alerty można tworzyć w spójny sposób niezależnie od tego, usługi monitorowania lub sygnał typu. Wszystkie wyzwolone alerty i powiązanych szczegółów znajduje się w jednej strony.
 
Utworzysz nową regułę alertu z trzech kroków:
1. Wybierz _docelowej_ alertu.
1. Wybierz _sygnału_ z dostępne sygnały dla elementu docelowego.
1. Określ _logiki_ mają być stosowane do danych z sygnałów.
 
To uproszczony proces tworzenia pakietów administracyjnych nie wymaga już znajomości źródło monitorowania lub sygnałów, które są obsługiwane przed wybraniem zasobu platformy Azure. Lista sygnałów dostępnych jest automatycznie filtrowany według zasobu docelowego, który można wybrać i prowadzi użytkownika przez definiowanie logiki reguły alertu.

Dowiedz się więcej na temat sposobu tworzenia reguły alertów w [Utwórz, Wyświetl, alerty i zarządzaj nimi przy użyciu usługi Azure Monitor](monitor-alerts-unified-usage.md).

Alerty są dostępne przez kilka usługi monitorowania platformy Azure. Aby uzyskać informacje o tym, jak i kiedy należy używać każdego z tych usług, zobacz [monitorowania platformy Azure, aplikacje i zasoby](./monitoring-overview.md). Poniższa tabela zawiera listę typów reguł alertów, które są dostępne na platformie Azure. Zawiera również listę co to jest obecnie obsługiwany w jakiego środowiska korzystania z alertu.

Usługi Azure Monitor, Application Insights, Log Analytics i kondycji usług miała wcześniej, oddzielne możliwości alertów. Z czasem platforma Azure ulepszone i interfejsu użytkownika i różnych metod alertów w połączeniu. Dzięki konsolidacji jest nadal w toku. Co w efekcie nadal istnieją pewne możliwości alertów nie jeszcze w nowym systemie alertów.  

| **Źródło monitora** | **Typ sygnału**  | **Opis** | 
|-------------|----------------|-------------|
| Kondycja usługi | Dziennik aktywności  | Nieobsługiwane. Zobacz [Tworzenie alertów dziennika aktywności dla powiadomień dotyczących usług](monitoring-activity-log-alerts-on-service-notifications.md).  |
| Application Insights | Testy dostępności sieci Web | Nieobsługiwane. Zobacz [alerty testu internetowego](../application-insights/app-insights-monitor-web-app-availability.md). Dostępne w dowolnej witrynie sieci Web, który został zinstrumentowany na wysyłanie danych do usługi Application Insights. Otrzymuj powiadomienie, gdy dostępności i czasu odpowiedzi witryny sieci Web znajduje się poniżej oczekiwania. |


## <a name="all-alerts-page"></a>Wszystkie strony alerty 
Polecenie łączna liczba alertów, aby zobaczyć wszystkie stronie alertów. W tym miejscu można wyświetlić listy alertów, które zostały utworzone w przedziale czasu wybranego. Możesz wyświetlić listę poszczególnych alertów lub listę grup inteligentne, które zawierają alertów. Wybierz Baner w górnej części strony Aby przełączać się między widokami.

![Wszystkie strony alerty](media/monitoring-overview-alerts/all-alerts-page.png)

Widok można filtrować, wybierając następujące wartości w menu rozwijanych w górnej części strony.

| Kolumna | Opis |
|:---|:---|
| Subskrypcja | Wybierz maksymalnie pięć subskrypcji platformy Azure. Tylko w wybranych subskrypcjach są uwzględniane alerty w widoku. |
| Grupa zasobów | Wybierz pojedynczą grupę zasobów. Tylko alerty z usługą obiektów docelowych w wybranej grupie zasobów znajdują się w widoku. |
| Typ zasobu | Wybierz jeden lub więcej typów zasobów. Tylko alerty o celach wybranego typu znajdują się w widoku. W tej kolumnie jest dostępna tylko po określono grupę zasobów. |
| Zasób | Wybierz zasób. Tylko alerty o zasobu jako obiekt docelowy znajdują się w widoku. W tej kolumnie jest dostępna tylko po został określony jako typ zasobu. |
| Ważność | Wybierz alert o ważności lub *wszystkich* obejmujący alerty wszystkie poziomy ważności. |
| Warunek monitora | Wybierz warunek monitora lub *wszystkich* obejmujący alerty warunków. |
| Stan alertu | Wybierz stan alertu lub *wszystkich* obejmujący alerty stanów. |
| Monitorowanie usługi | Wybierz usługę, lub wybierz *wszystkich* obejmujący wszystkie usługi. Uwzględniane są tylko alerty tworzone przez reguły, które korzystają z usługi jako element docelowy. |
| Przedział czasu | Tylko alerty wyzwalane w przedziale czasu wybranego znajdują się w widoku. Obsługiwane wartości to ostatniej godziny, ostatnich 24 godzin, w ciągu ostatnich 7 dni i ostatnich 30 dni. |

Wybierz **kolumn** w górnej części strony Aby wybrać kolumny do wyświetlenia. 

## <a name="alert-detail-page"></a>Strona szczegółów alertu
Po wybraniu alertu, zostanie wyświetlona strona szczegółów alertu. Ona zawiera szczegóły dotyczące alertu i umożliwia zmianę stanu.

![Szczegóły alertu](media/monitoring-overview-alerts/alert-detail2.png)

Strona szczegółów alertu zawiera następujące sekcje.

| Sekcja | Opis |
|:---|:---|
| Podstawy | Wyświetla właściwości i inne istotne informacje dotyczące alertu. |
| Historia | Wyświetla listę każdej akcji podjętej przez alert i wszelkie zmiany wprowadzone do alertu. To jest obecnie ograniczona do zmiany stanu. |
| Inteligentne grupy | Informacje na temat inteligentnych grupy alertu znajduje się w. *Liczba alertów* odnosi się do liczby alertów, które znajdują się w grupie inteligentne. Obejmuje to innych alertów w tej samej grupie inteligentne, które zostały utworzone w ciągu ostatnich 30 dni.  Jest to niezależnie od tego, filtr czasu na stronie listy alertów. Wybierz alert, aby wyświetlić jego szczegóły. |
| Więcej informacji | Umożliwia wyświetlanie dodatkowych informacji kontekstowych dla alertu, który jest zazwyczaj specyficzny dla typu źródła, która utworzyła alert. |


## <a name="classic-alerts"></a>Alertów klasycznych 

Usługi Azure Monitor metryki i dziennika działań alerty możliwości przed czerwca 2018 r. nosi nazwę "Alerty (klasyczne)". 

Aby uzyskać więcej informacji, zobacz [alerty klasyczne](./monitoring-overview-alerts-classic.md)


## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej na temat inteligentnych grup](https://aka.ms/smart-groups)
- [Dowiedz się więcej o grupach akcji](monitoring-action-groups.md)
- [Zarządzanie wystąpieniami alertów na platformie Azure](https://aka.ms/managing-alert-instances)
- [Zarządzanie grupami inteligentne](https://aka.ms/managing-smart-groups)





