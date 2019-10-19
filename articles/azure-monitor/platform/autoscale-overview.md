---
title: Omówienie automatycznego skalowania w Virtual Machines, Cloud Services i Web Apps
description: Skalowanie automatyczne w Microsoft Azure. Dotyczy Virtual Machines, zestawów skalowania maszyn wirtualnych, Cloud Services i Web Apps.
ms.service: azure-monitor
ms.subservice: autoscale
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/24/2018
ms.openlocfilehash: 2aeb0bc174eb5a94d485f4eafe000ecb3f693e5f
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72552284"
---
# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Omówienie automatycznego skalowania w Microsoft Azure Virtual Machines, Cloud Services i Web Apps
W tym artykule opisano, co Microsoft Azure automatyczne skalowanie, jego zalety oraz jak rozpocząć korzystanie z niego.  

Automatyczne skalowanie Azure Monitor ma zastosowanie tylko do [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)i [usług API Management](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

> [!NOTE]
> Platforma Azure ma dwie metody skalowania automatycznego. Starsza wersja automatycznego skalowania ma zastosowanie do Virtual Machines (zbiory dostępności). Ta funkcja ma ograniczoną obsługę i zalecamy Migrowanie do zestawów skalowania maszyn wirtualnych w celu szybszego i bardziej niezawodnego wsparcia automatycznego skalowania. W tym artykule znajduje się link dotyczący sposobu korzystania ze starszej technologii.  
>
>

## <a name="what-is-autoscale"></a>Co to jest automatyczne skalowanie?
Funkcja automatycznego skalowania umożliwia korzystanie z odpowiedniej ilości zasobów, aby obsłużyć obciążenie aplikacji. Pozwala to na dodawanie zasobów w celu zwiększenia obciążenia, a także oszczędność pieniędzy dzięki usunięciu zasobów znajdujących się w stanie bezczynności. Należy określić minimalną i maksymalną liczbę wystąpień do uruchomienia oraz automatyczne dodawanie lub usuwanie maszyn wirtualnych na podstawie zestawu reguł. Posiadanie minimum gwarantuje, że aplikacja zawsze działa nawet w przypadku braku obciążenia. Maksymalne ograniczenie łącznego kosztu jest możliwe. Automatyczne skalowanie między tymi dwoma skrajnymi elementami przy użyciu tworzonych reguł.

 ![Wyjaśniono automatyczne skalowanie. Dodawanie i usuwanie maszyn wirtualnych](./media/autoscale-overview/AutoscaleConcept.png)

Gdy warunki reguły są spełnione, wyzwalane są co najmniej jedno działanie skalowania automatycznego. Możesz dodawać i usuwać maszyny wirtualne lub wykonywać inne akcje. Ten proces przedstawiono na poniższym diagramie koncepcyjnym.  

 ![Diagram przepływu automatycznego skalowania](./media/autoscale-overview/Autoscale_Overview_v4.png)

Poniższe wyjaśnienie dotyczy elementów poprzedniego diagramu.   

## <a name="resource-metrics"></a>Metryki zasobu
Zasoby emitują metryki, te metryki są później przetwarzane przez zasady. Metryki są realizowane za pomocą różnych metod.
Zestawy skalowania maszyn wirtualnych wykorzystują dane telemetryczne z agentów diagnostyki platformy Azure, a usługi telemetryczne dla aplikacji sieci Web i usług w chmurze pochodzą bezpośrednio z infrastruktury platformy Azure. Niektóre często używane Statystyki obejmują użycie procesora CPU, użycie pamięci, liczby wątków, Długość kolejki i użycie dysku. Aby uzyskać listę danych telemetrycznych, których można użyć, zobacz [często używane metryki skalowania automatycznego](../../azure-monitor/platform/autoscale-common-metrics.md).

## <a name="custom-metrics"></a>Metryki niestandardowe
Możesz również wykorzystać własne metryki niestandardowe, które mogą emitować aplikacje. Jeśli skonfigurowano aplikacje do wysyłania metryk do Application Insights można wykorzystać te metryki, aby podejmować decyzje dotyczące tego, czy skalowanie ma być skalowane, czy nie.

## <a name="time"></a>Czas
Reguły oparte na harmonogramie są oparte na formacie UTC. W przypadku konfigurowania reguł należy odpowiednio ustawić strefę czasową.  

## <a name="rules"></a>Reguły
Na diagramie jest wyświetlana tylko jedna reguła automatycznego skalowania, ale można jej wiele. Możesz tworzyć złożone nakładające się reguły w zależności od potrzeb.  Typy reguł obejmują  

* **Na podstawie metryk** — na przykład wykonaj tę akcję, gdy użycie procesora CPU przekracza 50%.
* **Oparty na czasie** — na przykład Wyzwól element webhook każdy 8:00 w sobotę w danej strefie czasowej.

Reguły oparte na metrykach mierzą obciążenie aplikacji i dodają lub usuwają maszyny wirtualne na podstawie tego obciążenia. Reguły oparte na harmonogramie umożliwiają skalowanie, gdy widoczne są wzorce czasowe w obciążeniu i które mają być skalowane przed możliwym wzrostem lub zmniejszeniem obciążenia.  

## <a name="actions-and-automation"></a>Akcje i Automatyzacja
Reguły mogą wyzwalać jeden lub więcej typów akcji.

* **Skalowanie** maszyn wirtualnych w poziomie lub na zewnątrz
* Wyślij wiadomość **e-mail** do administratorów subskrypcji, współadministratorów i/lub dodatkowego adresu e-mail określonego przez użytkownika
* **Automatyzowanie za pośrednictwem elementów webhook** — wywoływanie elementów webhook, które mogą wyzwolić wiele złożonych akcji wewnątrz lub na zewnątrz platformy Azure. Na platformie Azure można uruchomić Azure Automation element Runbook, funkcję platformy Azure lub aplikację logiki platformy Azure. Przykładowy adres URL innej firmy poza platformą Azure obejmuje usługi, takie jak zapasy czasu i Twilio.

## <a name="autoscale-settings"></a>Ustawienia skalowania automatycznego
Skalowanie automatyczne wykorzystuje następującą terminologię i strukturę.

- **Ustawienie automatycznego skalowania** jest odczytywane przez aparat skalowania automatycznego w celu określenia, czy skalować w górę czy w dół. Zawiera co najmniej jeden profil, informacje o zasobie docelowym i ustawienia powiadomień.

  - **Profil skalowania automatycznego** jest kombinacją:

    - **ustawienie wydajności**, które wskazuje wartości minimalne, maksymalne i domyślne dla liczby wystąpień.
    - **zestaw reguł**, z których każdy obejmuje wyzwalacz (czas lub metrykę) oraz akcję skalowania (w górę lub w dół).
    - **cykl**wskazujący, że funkcja automatycznego skalowania powinna umieścić ten profil w efekcie.

      Można korzystać z wielu profilów, co pozwala na korzystanie z różnych nakładających się wymagań. Można mieć różne profile skalowania automatycznego dla różnych godzin dnia lub dni tygodnia, na przykład.

  - **Ustawienie powiadomienia** określa, jakie powiadomienia powinny wystąpić w przypadku wystąpienia zdarzenia automatycznego skalowania na podstawie spełnienia kryteriów jednego z profilów ustawień skalowania automatycznego. Funkcja automatycznego skalowania może powiadomić jeden lub więcej adresów e-mail lub nawiązywać wywołania do jednego lub większej liczby elementów webhook.


![Ustawienia, profil i struktura reguły automatycznego skalowania na platformie Azure](./media/autoscale-overview/AzureResourceManagerRuleStructure3.png)

Pełna lista konfigurowalnych pól i opisów jest dostępna w [interfejsie API REST skalowania automatycznego](https://msdn.microsoft.com/library/dn931928.aspx).

Aby zapoznać się z przykładami kodu, zobacz

* [Zaawansowana konfiguracja automatycznego skalowania przy użyciu Menedżer zasobów szablonów dla VM Scale Sets](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md)  
* [Interfejs API REST automatycznego skalowania](https://msdn.microsoft.com/library/dn931953.aspx)

## <a name="horizontal-vs-vertical-scaling"></a>Skalowanie w poziomie i w pionie
Skalowanie automatyczne jest skalowane w poziomie, co zwiększa się ("out") lub zmniejsza ("in") w liczbie wystąpień maszyn wirtualnych.  Poziome jest bardziej elastyczne w sytuacji chmury, ponieważ umożliwia uruchamianie potencjalnie tysięcy maszyn wirtualnych do obsługi obciążenia.

Natomiast skalowanie w pionie jest inne. Zachowuje tę samą liczbę maszyn wirtualnych, ale sprawia, że maszyny wirtualne są bardziej wydajne ("w górę") lub mniej ("w dół"). Moc jest mierzona w pamięci, szybkości procesora, ilości miejsca na dysku itp.  Skalowanie w pionie ma więcej ograniczeń. Jest ona zależna od dostępności większego sprzętu, który szybko trafi górny limit i może różnić się w zależności od regionu. Skalowanie w pionie wymaga również, aby maszyna wirtualna została zatrzymana i ponownie uruchomiona.


## <a name="methods-of-access"></a>Metody dostępu
Można skonfigurować Skalowanie automatyczne za pomocą

* [Azure Portal](../../azure-monitor/platform/autoscale-get-started.md)
* [Program PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)
* [Międzyplatformowy interfejs wiersza polecenia (CLI)](../../azure-monitor/platform/cli-samples.md#autoscale)
* [Interfejs API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## <a name="supported-services-for-autoscale"></a>Obsługiwane usługi skalowania automatycznego
| Usługa | Dokumentacja & schematu |
| --- | --- |
| Aplikacje internetowe |[Skalowanie Web Apps](../../azure-monitor/platform/autoscale-get-started.md) |
| Cloud Services |[Automatyczne skalowanie usługi w chmurze](../../cloud-services/cloud-services-how-to-scale-portal.md) |
| Virtual Machines: klasyczny |[Skalowanie klasycznych zestawów dostępności maszyn wirtualnych](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Virtual Machines: zestawy skalowania systemu Windows |[Skalowanie zestawów skalowania maszyn wirtualnych w systemie Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| Virtual Machines: zestawy skalowania systemu Linux |[Skalowanie zestawów skalowania maszyn wirtualnych w systemie Linux](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| Virtual Machines: przykład systemu Windows |[Zaawansowana konfiguracja automatycznego skalowania przy użyciu Menedżer zasobów szablonów dla VM Scale Sets](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md) |
| Usługa API Management|[Automatyczne skalowanie wystąpienia usługi Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-autoscale)

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat automatycznego skalowania, użyj wymienionych wcześniej przewodników skalowania automatycznego lub zapoznaj się z następującymi zasobami:

* [Azure Monitor metryki automatycznego skalowania](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Najlepsze rozwiązania dotyczące Azure Monitor automatycznego skalowania](../../azure-monitor/platform/autoscale-best-practices.md)
* [Używanie akcji skalowania automatycznego do wysyłania powiadomień o alertach dotyczących wiadomości e-mail i elementów webhook](../../azure-monitor/platform/autoscale-webhook-email.md)
* [Interfejs API REST automatycznego skalowania](https://msdn.microsoft.com/library/dn931953.aspx)
* [Rozwiązywanie problemów Virtual Machine Scale Sets automatyczne skalowanie](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

