---
title: Zarządzanie alertami z System Center Operations Manager, Zabbix i Nagios w Azure Monitor
description: Zarządzanie alertami z System Center Operations Manager, Zabbix i Nagios w Azure Monitor
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.author: robb
ms.date: 09/24/2018
ms.openlocfilehash: 0e774633542f9b7f8b7de9f8f187821bfbe6b9cf
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555621"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Zarządzanie alertami z System Center Operations Manager, Zabbix i Nagios w Azure Monitor

Możesz teraz wyświetlać alerty z Nagios, Zabbix i System Center Operations Manager w [Azure monitor](https://aka.ms/azure-alerts-overview). Te alerty pochodzą z integracji z serwerami Nagios/Zabbix lub System Center Operations Manager w Log Analytics. 

## <a name="prerequisites"></a>Wymagania wstępne
Wszystkie rekordy w repozytorium Log Analytics z typem alertu zostaną zaimportowane do Azure Monitor, więc należy wykonać konfigurację wymaganą do zebrania tych rekordów.
1. W przypadku alertów **Nagios** i **Zabbix** [Skonfiguruj te serwery](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) do [wysyłania alertów](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json) do log Analytics.
1. W przypadku alertów **System Center Operations Manager** [połącz grupę zarządzania Operations Manager z obszarem roboczym log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Poniżej przedstawiono wdrożenie rozwiązania [alert Management](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution) w portalu Azure Solutions Marketplace. Po wykonaniu tych czynności wszystkie alerty utworzone w System Center Operations Manager są importowane do Log Analytics.

## <a name="view-your-alert-instances"></a>Wyświetlanie wystąpień alertów
Po skonfigurowaniu importu do Log Analytics można rozpocząć wyświetlanie wystąpień alertów z tych usług monitorowania w programie [Azure monitor](https://aka.ms/azure-alerts-overview). Gdy są one obecne w Azure Monitor, można [zarządzać wystąpieniami alertów](https://aka.ms/managing-alert-instances), [zarządzać grupami inteligentnymi utworzonymi w tych alertach](https://aka.ms/managing-smart-groups) oraz [zmieniać stan alertów i grup inteligentnych](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  1. To rozwiązanie pozwala tylko wyświetlać wystąpienia alertów System Center Operations Manager/Zabbix/Nagios w programie Azure Monitor. Konfigurację reguły alertu można wyświetlać/edytować tylko w odpowiednich narzędziach do monitorowania. 
>  1. Wszystkie uruchomione wystąpienia alertów będą dostępne zarówno w Azure Monitor, jak i na platformie Azure Log Analytics. Obecnie nie ma możliwości wyboru między dwoma lub pozyskiwania konkretnie wybranych alertów.
>  1. Wszystkie alerty z System Center Operations Manager, Zabbix i Nagios mają typ sygnału "nieznany", ponieważ podstawowy typ telemetrii nie jest dostępny.
>  1. Alerty Nagios nie są stanowe — na przykład [warunek monitora](https://aka.ms/azure-alerts-overview) alertu nie zostanie przeszedł z "wyzwolone" do "rozwiązany". Zamiast tego zarówno "wyzwolone", jak i "rozwiązane" są wyświetlane jako osobne wystąpienia alertów. 

