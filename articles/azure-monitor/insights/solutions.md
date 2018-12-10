---
title: Rozwiązania do zarządzania w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Rozwiązania do zarządzania w usłudze Azure Monitor to zbiór reguł nabycia logiki, wizualizacji i danych, które udostępniają metryki dotyczące określonego obszaru problemów.  Ten artykuł zawiera informacje na temat instalowania i za pomocą rozwiązania do zarządzania.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2018
ms.author: bwren
ms.openlocfilehash: 1eac238c2379abf0b46d0975889a71ac7aa6a9ab
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141564"
---
# <a name="management-solutions-in-azure-monitor"></a>Rozwiązania do zarządzania w usłudze Azure Monitor
Rozwiązania do zarządzania korzystać z usług na platformie Azure, aby zapewnić dodatkowy wgląd w działania określonej aplikacji lub usługi. Ten artykuł zawiera krótkie omówienie rozwiązania do zarządzania na platformie Azure i uzyskać szczegółowe informacje na temat używania i instalując je.

Rozwiązania do zarządzania zwykle gromadzić dane dzienników i zapewnić zapytań i widoków do analizowania zebranych danych. Mogą one również korzystać z innych usług, takich jak usługa Azure Automation do wykonania akcji związanych z aplikacji lub usługi.

Rozwiązania do zarządzania można dodać do usługi Azure Monitor dla dowolnej aplikacji i usług, których używasz. Są one zazwyczaj dostępne w żadnych kosztów, ale zbieranie danych, które można wywołać opłaty za użycie. Oprócz rozwiązania firmy Microsoft, partnerów i klientów można [tworzenie rozwiązań do zarządzania](solutions-creating.md) do użycia we własnym środowisku lub udostępniane klientom za pośrednictwem społeczności.

## <a name="use-management-solutions"></a>Użyj rozwiązań do zarządzania
Otwórz **Przegląd** strony dla obszaru roboczego usługi Log Analytics wyświetlanie kafelka dla każdego rozwiązania zainstalowane w obszarze roboczym. 

1. Zaloguj się w witrynie Azure Portal.
1. Otwórz **wszystkich usług** i Znajdź **Monitor**.
1. W obszarze **Insights** menu, wybierz opcję **więcej**.
1. Użyj pola listy rozwijanej w górnej części ekranu, aby zmienić obszar roboczy lub zakres czasu używany w przypadku kafelków.
1. Kliknij Kafelek rozwiązania otworzyć jej widok, który zawiera bardziej szczegółowe analizy jego zebranych danych.

![Przegląd](media/solutions/overview.png)

Rozwiązania do zarządzania może zawierać wiele typów zasobów platformy Azure i wyświetlić wszystkie zasoby dołączone do rozwiązania, tak jak każdy inny zasób. Na przykład żadnych zapytań log zawarty w rozwiązaniu są wyświetlane w obszarze **zapytania rozwiązania** w [Eksplorator zapytań](../log-query/get-started-portal.md#load-queries) podczas przeprowadzania analizy ad-hoc z usługą Log Analytics, można użyć tych zapytań.

## <a name="list-installed-management-solutions"></a>Wyświetlanie listy zainstalowanych rozwiązań do zarządzania 
Aby wyświetlić listę rozwiązań do zarządzania zainstalowany w ramach subskrypcji, należy użyć następującej procedury.

1. Zaloguj się w witrynie Azure Portal.
1. Otwórz **wszystkich usług** i Znajdź **rozwiązania**.
4. Rozwiązania zainstalowane we wszystkich obszarach roboczych są wyświetlane. Nazwa rozwiązania następuje nazwa obszaru roboczego usługi Log Analytics, który jest instalowany w.
1. Użyj pola listy rozwijanej w górnej części ekranu, aby filtrować według subskrypcji lub grupy zasobów.


![Lista wszystkich rozwiązań](media/solutions/list-solutions-all.png)

Kliknij nazwę rozwiązania, aby otworzyć jej stronę podsumowania. Ta strona wyświetla wszystkie widoki zawarty w rozwiązaniu oraz różne opcje dla rozwiązania wraz z jej obszaru roboczego. Wyświetlić stronę podsumowania dla rozwiązania przy użyciu jednej z powyższych procedur do listy rozwiązań, a następnie kliknij nazwę rozwiązania.

![Właściwości rozwiązania](media/solutions/solution-properties.png)



## <a name="install-a-management-solution"></a>Zainstalować rozwiązanie do zarządzania
Rozwiązania do zarządzania firmy Microsoft i partnerów są dostępne z [portalu Azure Marketplace](https://azuremarketplace.microsoft.com). Możesz wyszukiwać dostępnych rozwiązań i zainstalować je przy użyciu poniższej procedury. Po zainstalowaniu rozwiązania, należy wybrać [obszaru roboczego usługi Log Analytics](../platform/manage-access.md) zainstalowanym rozwiązania i gdzie będą zbierane dane.

1. Z [listę rozwiązań dla Twojej subskrypcji](#list-installed-monitoring-solutions), kliknij przycisk **Dodaj**. 
1. Po prawej stronie **rozwiązań do zarządzania**, kliknij przycisk **więcej**. 
1. Znajdź rozwiązania do zarządzania ma i zapoznaj się z jego opis.
1. Kliknij przycisk **Utwórz** do uruchamiania procesu instalacji.
1. Podczas uruchamiania procesu instalacji, zostanie wyświetlony monit podanie wymaganej konfiguracji, które różni się w poszczególnych rozwiązań.

![Zainstalowanie rozwiązania](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Zainstalowanie rozwiązania od społeczności
Członkowie społeczności mogą przesyłać do platformy Azure — szablony szybkiego startu rozwiązania do zarządzania. Można bezpośrednio zainstalować te rozwiązania lub pobranie szablonów dla nowszych instalacji.

1. Wykonaj czynności opisane w [obszar roboczy usługi Log Analytics i konto usługi Automation](#log-analytics-workspace-and-automation-account) połączyć obszar roboczy i konto.
2. Przejdź do [szablony szybkiego startu platformy](https://azure.microsoft.com/documentation/templates/). 
3. Wyszukaj rozwiązanie, które interesują Cię.
4. Wybierz rozwiązanie z wyników, aby wyświetlić jego szczegóły.
5. Kliknij przycisk **Wdróż na platformie Azure** przycisku.
6. Monit o podanie informacji takich jak grupy zasobów i lokalizacji, oprócz wartości parametrów w rozwiązaniu.
7. Kliknij przycisk **zakupu** może instalować rozwiązanie.


## <a name="log-analytics-workspace-and-automation-account"></a>Obszar roboczy usługi log Analytics i konto usługi Automation
Wszystkie rozwiązania zarządzania wymagają [obszaru roboczego usługi Log Analytics](../platform/manage-access.md) do przechowywania danych zbieranych przez to rozwiązanie i hostowania wyszukiwań w dziennikach i widoków. Niektóre rozwiązania wymagają również [konta usługi Automation](../../automation/automation-security-overview.md#automation-account-overview) zawiera elementy runbook i powiązane zasoby. Obszar roboczy i konto musi spełniać następujące wymagania.

* Każda instalacja rozwiązania można używać tylko jednego obszaru roboczego usługi Log Analytics i jedno konto usługi Automation. Oddzielnie zainstalować rozwiązanie do wielu obszarów roboczych.
* Jeśli to rozwiązanie wymaga konta usługi Automation, następnie obszaru roboczego usługi Log Analytics i konto usługi Automation muszą być połączone ze sobą. Obszar roboczy usługi Log Analytics mogą być łączone tylko na jednym koncie usługi Automation, a konto usługi Automation mogą być łączone tylko z jednym obszarem roboczym usługi Log Analytics.
* Połączone, obszar roboczy usługi Log Analytics i konto usługi Automation musi być w tej samej grupie zasobów i regionie. Wyjątkiem jest obszar roboczy w regionie wschodnie stany USA i konta usługi Automation w regionie wschodnie stany USA 2.

### <a name="create-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Utwórz łącze między obszaru roboczego usługi Log Analytics i konto usługi Automation
Jak określić obszar roboczy usługi Log Analytics i konto usługi Automation zależy od metody instalacji dla danego rozwiązania.

* Po zainstalowaniu rozwiązania w portalu Azure Marketplace, zostanie wyświetlony monit o obszar roboczy i konto usługi Automation. Połączenie między nimi jest tworzony, jeśli już nie są połączone.
* W przypadku rozwiązań poza portalem Azure Marketplace musisz połączyć obszar roboczy usługi Log Analytics i konto usługi Automation przed instalacją rozwiązania. Można to zrobić, wybierając dowolne rozwiązanie w witrynie Azure Marketplace i wybierając obszar roboczy usługi Log Analytics i konto usługi Automation. Nie masz faktycznie zainstalować rozwiązania, ponieważ łącze jest tworzone, tak szybko, jak obszar roboczy usługi Log Analytics i konto usługi Automation są zaznaczone. Po utworzeniu łącza, a następnie można użyć tego obszaru roboczego usługi Log Analytics i konto usługi Automation dla dowolnego rozwiązania.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Sprawdź połączenie między obszaru roboczego usługi Log Analytics i konto usługi Automation
Można sprawdzić połączenie między obszar roboczy usługi Log Analytics i kontem usługi Automation, korzystając z następującej procedury.

1. Wybierz konto usługi Automation w witrynie Azure portal.
1. Przewiń do **powiązane zasoby** części menu.
1. Jeśli **obszaru roboczego** ustawienie jest włączone, a następnie to konto jest połączone z obszarem roboczym usługi Log Analytics. Możesz kliknąć **obszaru roboczego** Aby wyświetlić szczegóły obszaru roboczego.

## <a name="remove-a-management-solution"></a>Usuń to rozwiązanie do zarządzania
Aby usunąć rozwiązanie zainstalowany, znajdź go w [listę zainstalowanych rozwiązań](#list-installed-monitoring-solutions). Kliknij nazwę rozwiązania, aby otworzyć jej stronę podsumowania, a następnie kliknij polecenie **Usuń**.




## <a name="next-steps"></a>Kolejne kroki
* Pobierz [listę rozwiązań do zarządzania przez firmę Microsoft](solutions-inventory.md).
* Dowiedz się, jak [tworzenia zapytań](../../azure-monitor/log-query/log-query-overview.md) analizować dane zbierane przez rozwiązania do zarządzania.

