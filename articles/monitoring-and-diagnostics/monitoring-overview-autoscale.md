---
title: Omówienie automatycznego skalowania maszyn wirtualnych, usług w chmurze i aplikacji sieci Web
description: Automatyczne skalowanie na platformie Microsoft Azure. Ma zastosowanie do maszyn wirtualnych, Virtual machine Scale sets, usługi w chmurze i aplikacji sieci Web.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: robb
ms.component: autoscale
ms.openlocfilehash: 8491f3f61746374b67e174ca5efff2fbf2fe5fbe
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281901"
---
# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Omówienie automatycznego skalowania w Microsoft Azure Virtual Machines, Cloud Services i Web Apps
W tym artykule opisano, jakie Microsoft Azure Skalowanie automatyczne to, jego zalety i sposób rozpoczynania korzystania z niego.  

Skalowanie automatyczne platformy Azure Monitor ma zastosowanie tylko do [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [usług w chmurze](https://azure.microsoft.com/services/cloud-services/), [App Service — Web Apps](https://azure.microsoft.com/services/app-service/web/), i [usługi API Management](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

> [!NOTE]
> Platforma Azure ma dwie metody automatycznego skalowania. Starszą wersję automatycznego skalowania ma zastosowanie do maszyn wirtualnych (zestawy dostępności). Ta funkcja ma ograniczoną obsługę i zalecamy przeprowadzić migrację do zestawów skalowania maszyn wirtualnych, obsługi automatycznego skalowania szybszy i bardziej niezawodny. Łącze dotyczące sposobu używania starszych technologiach znajduje się w tym artykule.  
>
>

## <a name="what-is-autoscale"></a>Co to jest funkcja automatycznego skalowania?
Automatyczne skalowanie pozwala mieć odpowiednią ilość zasobów do obsługi obciążenia w swojej aplikacji. Umożliwia dodawanie zasobów do obsługi zwiększenia obciążenia, a także Oszczędzaj pieniądze, usuwając zasoby, które są obecne bezczynności. Możesz określić minimalną i maksymalną liczbą wystąpień do uruchamiania i dodawanie lub usuwanie maszyn wirtualnych automatycznie na podstawie zestawu reguł. O minimalnej sprawia, że się, że Twoja aplikacja zawsze działa nawet przy braku obciążenia. Mającą co najwyżej ogranicza możliwości całkowity koszt na godzinę. Automatyczne skalowanie między tymi dwoma skrajnymi poziomami przy użyciu reguł, które można utworzyć.

 ![Wyjaśniono skalowania automatycznego. Dodawanie i usuwanie maszyn wirtualnych](./media/monitoring-overview-autoscale/AutoscaleConcept.png)

Po spełnieniu warunków reguły, co najmniej jednej akcji skalowania automatycznego są wyzwalane. Możesz dodać i usunąć maszyny wirtualne lub wykonywania innych akcji. Poniższy diagram koncepcyjny przedstawia ten proces.  

 ![Diagram przepływu automatycznego skalowania](./media/monitoring-overview-autoscale/Autoscale_Overview_v4.png)

Wyjaśnienia poniżej mają zastosowanie do rodzajów poprzedniego diagramu.   

## <a name="resource-metrics"></a>Metryki zasobów
Zasoby emitują metryki, te metryki są później przetwarzane przez zasady. Metryki są dostarczane za pośrednictwem różnych metod.
Zestawy skalowania maszyn wirtualnych Użyj danych telemetrycznych z agentów dla diagnostyki platformy Azure, natomiast dane telemetryczne dla aplikacji sieci Web i usług w chmurze pochodzi bezpośrednio z infrastruktury platformy Azure. Niektóre powszechnie używane statystyki obejmują użycie procesora CPU, użycie pamięci, liczby wątków, długość kolejki i użycie dysku. Aby uzyskać listę danych telemetrycznych, jakie można użyć, zobacz [typowe metryki automatycznego skalowania](insights-autoscale-common-metrics.md).

## <a name="custom-metrics"></a>Metryki niestandardowe
Można też skorzystać z własnych metryki niestandardowe, że Twoje aplikacje mogą emitowania. Jeśli zostały skonfigurowane w Twojej aplikacji na wysyłanie metryk do usługi Application Insights mogą korzystać z tych metryk przy podejmowaniu decyzji, czy wykonać skalowanie, czy nie.

## <a name="time"></a>Time
Reguły na podstawie harmonogramu są oparte na czasie UTC. Musisz prawidłowo swoją strefę czasową, podczas konfigurowania reguł.  

## <a name="rules"></a>Reguły
Na diagramie przedstawiono tylko jedna reguła skalowania automatycznego, ale można mieć wiele z nich. Można utworzyć złożone nakładających się reguły, zgodnie z potrzebami w danej sytuacji.  Obejmują typy reguł  

* **Opartą na metryce** — na przykład, wykonaj tę akcję, gdy użycie Procesora przekracza 50%.
* **Na podstawie czasu** — na przykład wyzwalacza elementu webhook co 8: 00 w sobotę w danej strefie czasowej.

Reguły na podstawie metryki pomiaru obciążenia aplikacji, a następnie dodaj lub usuń maszyny wirtualne na podstawie tego obciążenia. Reguły na podstawie harmonogramu umożliwiają skalowanie, gdy widać wzorce czasu w obciążenia i skalowania przed wzrost obciążenia możliwe lub występuje spadek.  

## <a name="actions-and-automation"></a>Akcje i automatyzacja
Zasady można uruchomić co najmniej jeden typ akcji.

* **Skala** -skalowania maszyn wirtualnych, wewnątrz lub na zewnątrz
* **Adres e-mail** — Wyślij wiadomość e-mail do administratorów subskrypcji, co Administratorzy i/lub określony adres e-mail dodatkowe
* **Automatyzacja za pośrednictwem elementów webhook** — wywoływanie elementów webhook, co może wyzwolić wiele akcji złożonych wewnątrz lub na zewnątrz platformy Azure. Wewnątrz platformy Azure można uruchomić elementu runbook usługi Azure Automation, funkcja platformy Azure lub aplikacji logiki platformy Azure. Przykładowy adres URL firm poza systemem Azure obejmują usług, takich jak Slack i Twilio.

## <a name="autoscale-settings"></a>Ustawienia skalowania automatycznego
Funkcja automatycznego skalowania, użyj poniższe terminy i struktury.

- **Ustawienie skalowania automatycznego** jest odczytywany przez aparat skalowania automatycznego, aby określić, czy wykonać skalowanie w górę lub w dół. Zawiera on jeden lub więcej profilów, informacje dotyczące zasobu docelowego i ustawień powiadomień.

    - **Profilu skalowania automatycznego** jest kombinacją Odp.:

        - **Ustawienia pojemności**, co oznacza, minimalna, maksymalna i wartości domyślne dla liczby wystąpień.
        - **zestaw reguł**, z których każdy zawiera wyzwalacz (czas lub metryki) oraz akcję skalowania (górę lub w dół).
        - **Cykl**, co oznacza, że po umieszczeniu tego profilu w życie skalowania automatycznego.

        Może mieć wiele profilów, które pozwalają zadbać o różnych wymaganiach nakładających się. Istnieje możliwość użycia profile automatycznego skalowania różne dla różnych porach dnia lub dni tygodnia, na przykład.

    - A **ustawienie powiadomień** definiuje, jakie powiadomienia mają być powinny być wykonywane, gdy wystąpi zdarzenie automatyczne skalowanie zależnie od spełniające kryteria profile ustawienie skalowania automatycznego. Skalowanie automatyczne można powiadomienie co najmniej jeden adres e-mail lub wykonywanie wywołań do jednego lub więcej elementów webhook.


![Ustawienie skalowania automatycznego platformy Azure, profilu i struktury reguły](./media/monitoring-overview-autoscale/AzureResourceManagerRuleStructure3.png)

Pełną listę można konfigurować pola i opisy jest dostępna w [interfejsu API REST skalowania automatycznego](https://msdn.microsoft.com/library/dn931928.aspx).

Aby uzyskać przykłady kodu zobacz

* [Zaawansowanej konfiguracji skalowania automatycznego dla zestawów skalowania maszyn wirtualnych przy użyciu szablonów usługi Resource Manager](insights-advanced-autoscale-virtual-machine-scale-sets.md)  
* [Interfejs API REST automatycznego skalowania](https://msdn.microsoft.com/library/dn931953.aspx)

## <a name="horizontal-vs-vertical-scaling"></a>Skalowanie w pionie poziomy vs
Tylko skalowania automatycznego w poziomie, jest to wzrost ("out") lub spadek ("") w liczbie wystąpień maszyn wirtualnych.  W poziomie jest bardziej elastyczna w sytuacji, w chmurze, ponieważ pozwala ono potencjalnie uruchomienia tysięcy maszyn wirtualnych do obsługi obciążenia.

Z kolei skalowanie w pionie jest inny. Zapewnia taką samą liczbę maszyn wirtualnych, ale sprawia, że maszyny wirtualne więcej ("do góry") lub mniej ("nie działa") zaawansowane. Moc jest mierzony w pamięci, szybkości Procesora, miejsca na dysku itp.  Skalowanie w pionie ma więcej ograniczeń. Jest on zależny od dostępności sprzętu większych i szybko osiągnie górny limit i może różnić między regionami. Skalowanie w pionie również zwykle wymaga maszyny Wirtualnej w celu zatrzymania i ponownego uruchomienia.

Aby uzyskać więcej informacji, zobacz [skalowanie w pionie maszyn wirtualnych platformy Azure z usługą Azure Automation](../virtual-machines/linux/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="methods-of-access"></a>Metod dostępu
Możesz skonfigurować automatyczne skalowanie za pomocą

* [Azure Portal](monitoring-autoscale-get-started.md)
* [Program PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)
* [Międzyplatformowy interfejs wiersza polecenia (CLI)](insights-cli-samples.md#autoscale)
* [Interfejs API REST usługi Azure Monitor](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## <a name="supported-services-for-autoscale"></a>Obsługiwane usługi dotyczące automatycznego skalowania
| Usługa | Schemat i dokumenty |
| --- | --- |
| Web Apps |[Skalowanie aplikacji sieci Web](monitoring-autoscale-get-started.md) |
| Cloud Services |[Automatyczne skalowanie usługi w chmurze](../cloud-services/cloud-services-how-to-scale-portal.md) |
| Maszyny wirtualne: klasyczny |[Skalowanie zestawów dostępności klasycznej maszyny wirtualnej](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Virtual Machines: Zestawy skalowania Windows |[Skalowanie skalowania maszyn wirtualnych zestawów w Windows](../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| Maszyny wirtualne: Zestawy skalowania systemu Linux |[Skalowanie skalowania maszyn wirtualnych ustawia w systemie Linux](../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| Virtual Machines: Przykład Windows |[Zaawansowanej konfiguracji skalowania automatycznego dla zestawów skalowania maszyn wirtualnych przy użyciu szablonów usługi Resource Manager](insights-advanced-autoscale-virtual-machine-scale-sets.md) |
| Usługi API Management|[Automatyczne skalowanie wystąpienia usługi Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-autoscale)

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat automatycznego skalowania, użyj wskazówki dotyczące skalowania automatycznego wymienionych powyżej, lub zobacz następujące zasoby:

* [Usługa Azure Monitor skalowania automatycznego często używane metryki](insights-autoscale-common-metrics.md)
* [Najlepsze rozwiązania dotyczące skalowania automatycznego usługi Azure Monitor](insights-autoscale-best-practices.md)
* [Użyj akcji skalowania automatycznego, aby wysyłać wiadomości e-mail i elementy webhook powiadomienia o alertach](insights-autoscale-to-webhook-email.md)
* [Interfejs API REST automatycznego skalowania](https://msdn.microsoft.com/library/dn931953.aspx)
* [Rozwiązywania problemów automatyczne skalowanie zestawów skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)
