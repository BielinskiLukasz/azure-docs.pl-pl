---
title: Azure Monitor aktualizacje terminologii | Microsoft Docs
description: Opisuje ostatnie zmiany terminologii dotyczące usług monitorowania platformy Azure.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 5324c17cdf2593d8e1022e6ddbf52b4e24ee64e8
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597823"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Azure Monitor nazewnictwo i terminologia
Wprowadzono znaczące zmiany Azure Monitor ostatnio z różnymi usługami, które są konsolidowane w celu uproszczenia monitorowania dla klientów platformy Azure. W tym artykule opisano ostatnie zmiany nazwy i terminologii w dokumentacji Azure Monitor.

## <a name="october-2019---diagnostic-log-to-resource-log"></a>Październik 2019 — dziennik diagnostyczny do dziennika zasobów
Dzienniki diagnostyczne zostały zmienione na "dzienniki zasobów", aby lepiej odpowiadały to, co jest w rzeczywistości zbierane. Termin "Ustawienia diagnostyczne" pozostaje taki sam.  

## <a name="february-2019---log-analytics-terminology"></a>Luty 2019 — terminologia Log Analytics
Po konsolidacji różnych usług w ramach Azure Monitor wykonujemy następny krok, modyfikując terminologię w naszej dokumentacji, aby lepiej opisać usługę Azure Monitor i jej różne składniki. 

### <a name="log-analytics"></a>Log Analytics
Azure Monitor dane dziennika są nadal przechowywane w Log Analytics obszarze roboczym i są nadal zbierane i analizowane przez tę samą usługę Log Analytics, ale zmieniamy termin _log Analytics_ w wielu miejscach na _Azure monitor dzienniki_. Ten termin lepiej odzwierciedla swoją rolę w Azure Monitor i zapewnia lepszą spójność przy użyciu [metryk w Azure monitor](platform/data-platform-metrics.md).

Termin usługi _log Analytics_ dotyczy teraz głównie strony w Azure Portal używanej do zapisywania i uruchamiania zapytań oraz analizowania danych dziennika. Jest to funkcjonalny odpowiednik [Eksploratora metryk](platform/metrics-charts.md), który jest stroną w Azure Portal używany do analizowania danych metryk.

### <a name="log-analytics-workspaces"></a>Obszary robocze usługi Log Analytics
[Obszary robocze](platform/manage-access.md) , które przechowują dane dziennika w Azure monitor są nadal określane jako log Analytics obszary robocze. Nazwa menu **log Analytics** w Azure portal została zmieniona na **log Analytics obszary robocze** i służy do [tworzenia nowych obszarów roboczych](learn/quick-create-workspace.md) i konfigurowania źródeł danych. Analizuj dzienniki i inne dane monitorowania w **Azure monitor** i skonfiguruj obszar roboczy w **obszarze log Analytics obszary robocze**.

### <a name="management-solutions"></a>Rozwiązania do zarządzania
Zmieniono nazwy [rozwiązań zarządzania](insights/solutions.md) na _rozwiązania do monitorowania_, które lepiej opisują ich funkcjonalność.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>Sierpień 2018 — konsolidacja usług monitorowania do Azure Monitor
Log Analytics i Application Insights zostały skonsolidowane w Azure Monitor w celu zapewnienia pojedynczego zintegrowanego środowiska monitorowania zasobów platformy Azure i środowisk hybrydowych. Nie usunięto żadnych funkcji z tych usług, a użytkownicy mogą wykonywać te same scenariusze, które były zawsze wykonywane bez utraty ani naruszenia jakichkolwiek funkcji.

Dokumentacja dla każdej z tych usług została skonsolidowana w jeden zestaw zawartości dla Azure Monitor. Ułatwi to czytelnikowi znalezienie całej zawartości dla określonego scenariusza monitorowania w jednej lokalizacji, a nie na konieczności odwoływania się do wielu zestawów zawartości. W miarę rozwoju usługi skonsolidowanej zawartość stanie się bardziej zintegrowana.

Inne funkcje, które zostały uznane za część Log Analytics, takie jak agenci i widoki, również zostały zmienione jako funkcje Azure Monitor. Ich funkcjonalność nie zmieniła się w sposób inny niż potencjalne ulepszenia środowiska w Azure Portal.


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>Kwiecień 2018 — wycofanie marki pakietu Operations Management Suite
Pakiet Operations Management Suite (OMS) to zbiór następujących usług zarządzania platformy Azure do celów licencjonowania:

- Application Insights
- Azure Automation
- Usługa Azure Backup
- Log Analytics
- Odzyskiwanie witryn

[Dla tych usług wprowadzono nowe ceny](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/), a pakiet OMS nie jest już dostępny dla nowych klientów. Żadna z usług, które były częścią pakietu OMS, nie zmieniła się, z wyjątkiem konsolidacji do Azure Monitor opisanej powyżej. 




## <a name="next-steps"></a>Następne kroki

- Zapoznaj się [z omówieniem Azure monitor](overview.md) opisującym różne składniki i funkcje.
- Dowiedz się więcej o [przejściu portalu pakietu OMS](../log-analytics/log-analytics-oms-portal-transition.md).
