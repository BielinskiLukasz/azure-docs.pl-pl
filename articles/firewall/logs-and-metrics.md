---
title: Przegląd dzienników zapory usługi Azure
description: W tym artykule przedstawiono omówienie dzienników diagnostycznych zapory usługi Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: c129c394f3d694b832722287027c1f9e58028a33
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957695"
---
# <a name="azure-firewall-logs"></a>Dzienniki zapory platformy Azure

Usługę Azure Firewall możesz monitorować przy użyciu dzienników zapory. Ponadto dzienniki aktywności umożliwiają inspekcję operacji wykonywanych względem zasobów usługi Azure Firewall.

Niektóre z tych dzienników są dostępne za pośrednictwem portalu. Dzienniki mogą być wysyłane do [dzienniki usługi Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md), magazynu i centrów zdarzeń i przeanalizować w dziennikach w usłudze Azure Monitor lub przy użyciu różnych narzędzi, takich jak program Excel i Power BI.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

 Dla usługi Azure Firewall są dostępne następujące dzienniki diagnostyczne:

* **Dziennik reguł aplikacji**

   Dziennik reguły aplikacji są zapisywane na koncie magazynu, przesyłane strumieniowo do usługi Event hubs i/lub wysyłane do usługi Azure Monitor dzienników, tylko wtedy, gdy włączono dla każdej zapory usługi Azure. W wyniku każdego nowego połączenia, które jest zgodne z jedną ze skonfigurowanych reguł aplikacji, jest tworzony dziennik dla zaakceptowanego/odrzuconego połączenia. Dane są rejestrowane w formacie JSON, jak pokazano w poniższym przykładzie:

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **Dziennik reguł sieci**

   Dziennik reguły sieci są zapisywane na koncie magazynu, przesyłane strumieniowo do usługi Event hubs i/lub wysyłane do usługi Azure Monitor dzienników, tylko wtedy, gdy włączono dla każdej zapory usługi Azure. W wyniku każdego nowego połączenia, które jest zgodne z jedną ze skonfigurowanych reguł sieci, jest tworzony dziennik dla zaakceptowanego/odrzuconego połączenia. Dane są rejestrowane w formacie JSON, jak pokazano w poniższym przykładzie:

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

Masz trzy opcje przechowywania dzienników:

* **Konto magazynu**: Konta magazynu są używane dla dzienników najlepiej, gdy dzienniki są przechowywane przez dłuższy czas i sprawdzone w razie.
* **Usługa Event hubs**: Usługa Event hubs to doskonałe rozwiązanie umożliwiające integrację z innymi informacjami i zdarzeniami (SEIM) narzędzia do zarządzania zabezpieczeniami, aby otrzymywać alerty dotyczące zasobów.
* **Dzienniki platformy Azure Monitor**: Dzienniki platformy Azure Monitor najlepiej nadaje się do ogólnego monitorowania w czasie rzeczywistym, w aplikacji lub przyglądanie się trendom.

## <a name="activity-logs"></a>Dzienniki aktywności

   Wpisy dziennika aktywności są zbierane domyślnie i można je wyświetlać w witrynie Azure Portal.

   Korzystając z [dzienników aktywności platformy Azure](../azure-resource-manager/resource-group-audit.md) (znanych wcześniej jako dzienniki operacyjne i dzienniki inspekcji), możesz wyświetlać wszystkie operacje przesyłane do Twojej subskrypcji platformy Azure.


## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak monitorować dzienniki zapory platformy Azure i metryk, zobacz [samouczka: Monitoruj dzienniki zapory usługi Azure](tutorial-diagnostics.md).