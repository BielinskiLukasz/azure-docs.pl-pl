---
title: Rozszerzanie alertów usługi Log Analytics (kopiowanie) do alertów platformy Azure — omówienie
description: Omówienie procesu kopiowanie alertów z usługi Log Analytics w portalu pakietu OMS do alertów platformy Azure przy użyciu szczegółów adresowania typowych problemów klienta.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 45682caedaaaac1c9d335a45db64f16d713dfd7f
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233501"
---
# <a name="extend-log-analytics-alerts-to-azure-alerts"></a>Rozszerzanie alertów usługi Log Analytics do usługi Azure Alerts
Niedawno, dopóki nie usługi Azure Log Analytics uwzględnione własną funkcję alertu, która może aktywnie powiadomić użytkownika o warunków opartych na danych usługi Log Analytics. Możesz zarządzać reguły alertów w portalu Microsoft Operations Management Suite. Nowe środowisko alertów ma teraz zintegrowane alertów w różnych usługach w systemie Microsoft Azure. Ta opcja jest dostępna jako **alerty** w ramach usługi Azure Monitor w witrynie Azure portal obsługuje alerty z dzienników aktywności, metryk i dzienników z usługi Log Analytics i Azure Application Insights. 

## <a name="benefits-of-extending-your-alerts"></a>Zalety rozszerzanie alertów
Istnieje kilka korzyści wynikające z tworzenia i zarządzania nimi alertów w witrynie Azure portal, takich jak:

- W odróżnieniu od w portalu Operations Management Suite, w którym tylko 250 alertów można można tworzyć i wyświetlać, Azure Alerts ma takie ograniczenie nie.
- Z alertów platformy Azure można zarządzać, wyliczyć i wyświetlić wszystkie typy alertów. Wcześniej można tylko tym alertów usługi Log Analytics.
- Można ograniczyć dostęp do użytkowników tylko monitorowania i alertów za pomocą [roli usługi Azure Monitor](monitoring-roles-permissions-security.md).
- W alertach Azure można używać [grup akcji](monitoring-action-groups.md). Dzięki temu można mieć więcej niż jedną akcję dla każdego alertu. Możesz wiadomość SMS, Wyślij połączenie głosowe, wywoływania elementu runbook usługi Azure Automation, wywołaj element webhook i skonfigurowanie łącznika zarządzania usługi IT (ITSM). 

## <a name="process-of-extending-your-alerts"></a>Proces rozszerzania alertów
Proces przechodzenia alerty z usługą Log Analytics do alertów platformy Azure nie wymaga zmian w definicji alertu, kwerenda lub konfigurację w jakikolwiek sposób. Wymagana zmiana tylko to, że na platformie Azure, możesz wykonywać wszystkie akcje przy użyciu grupy akcji. Jeśli grupy akcji są już skojarzone z alertu, są one uwzględniane podczas rozszerzony na platformę Azure.

> [!NOTE]
> Firma Microsoft automatycznie rozszerzy alerty utworzone w wystąpieniach chmury publicznej usługi Log Analytics do alertów platformy Azure od 14 maja 2018 r. w serii cyklicznych do momentu zakończenia. Jeśli masz problemy z tworzeniem [grup akcji](monitoring-action-groups.md), użyj [te czynności zaradczych](monitoring-alerts-extend-tool.md#troubleshooting) można pobrać grup akcji utworzone automatycznie. Te kroki można użyć do 5 lipca 2018 r. *Nie stosuje się do platformy Azure dla instytucji rządowych i użytkowników należących do suwerennej chmury usługi Log Analytics*. 
> 

Podczas planowania alerty w obszarze roboczym usługi Log Analytics na platformie Azure one nadal działać i nie obsługują w że dowolny sposób naruszyć konfigurację. Podczas planowania alertów może tymczasowo niedostępne do modyfikacji, ale można kontynuować tworzenie nowych alertów platformy Azure, w tym czasie. Jeśli spróbujesz edytować lub tworzyć alerty z portalu Operations Management Suite, masz opcję, aby kontynuować, są one tworzone ze swojego obszaru roboczego usługi Log Analytics. Możesz również utworzyć je z alertów platformy Azure w witrynie Azure portal.

 ![Zrzut ekranu przedstawiający opcję, aby utworzyć alerty z usługi Log Analytics lub Azure Alerts](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Rozszerzanie alertów z usługi Log Analytics na platformie Azure nie są naliczane opłaty za do Twojego konta. Korzystanie z alertów platformy Azure dla zapytań na podstawie alertów usługi Log Analytics nie jest rozliczane, gdy jest używana w ramach limitów i warunki określone w [usługi Azure Monitor polityki cenowej](https://azure.microsoft.com/pricing/details/monitor/).  


### <a name="how-to-extend-your-alerts-voluntarily"></a>Jak rozszerzyć alerty dobrowolnie
Aby rozszerzyć alerty do alertów platformy Azure, można użyć Kreatora dostępne w portalu Operations Management Suite lub zrobić w sposób programowy za pomocą interfejsu API. Aby uzyskać więcej informacji, zobacz [rozszerzanie alertów do platformy Azure przy użyciu portalu Operations Management Suite i interfejsu API](monitoring-alerts-extend-tool.md).

## <a name="experience-after-extending-your-alerts"></a>Występuje po rozszerzeniu alertów
Alerty zostały rozszerzone do alertów platformy Azure, nadal będą znajdować się w portalu Operations Management Suite w celu zarządzania nie inaczej niż wcześniej.

![Zrzut ekranu z pakietu Operations Management Suite portal, za pomocą alertów](./media/monitor-alerts-extend/PostExtendList.png)

Przy próbie edycji istniejący alert lub tworzenia nowego alertu w portalu Operations Management Suite, automatycznie są przekierowywane do alertów platformy Azure.  

> [!NOTE]
> Upewnij się, że uprawnienia przypisane do osoby, które należy dodać lub edytować alerty są prawidłowo przypisany na platformie Azure. Aby dowiedzieć się, jakie uprawnienia należy udzielić, zobacz [uprawnienia dotyczące korzystania z usługi Azure Monitor i alerty](monitoring-roles-permissions-security.md).  
> 

Możesz nadal tworzyć alerty z [interfejsu API usługi Log Analytics](../log-analytics/log-analytics-api-alerts.md) i [szablonu usługi Log Analytics Resource](../monitoring/monitoring-solutions-resources-searches-alerts.md). Jeśli tak zrobisz, musi zawierać grupy akcji.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o narzędziach do [zainicjować rozszerzanie alertów z usługi Log Analytics na platformie Azure](monitoring-alerts-extend-tool.md).
* Dowiedz się więcej o [środowisko Azure Alerts](monitoring-overview-unified-alerts.md).
* Dowiedz się, jak utworzyć [alerty dzienników w alertach Azure](monitor-alerts-unified-log.md).
