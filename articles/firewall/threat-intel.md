---
title: Filtrowanie na podstawie analizy zagrożeń zapory platformy Azure
description: Filtrowanie na podstawie analizy zagrożeń może być włączone, aby zapora mogła zgłaszać i odrzucać ruch z/do znanych złośliwych adresów IP i domen.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/12/2020
ms.author: victorh
ms.openlocfilehash: e51cc8905a7b4a88bb7f7dabaf24bb30159ff86c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83655072"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Filtrowanie na podstawie analizy zagrożeń zapory platformy Azure

Filtrowanie na podstawie analizy zagrożeń może być włączone, aby zapora mogła zgłaszać i odrzucać ruch z/do znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą ze źródła analizy zagrożeń firmy Microsoft. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) zapewnia program Microsoft Threat Intelligence i jest używany przez wiele usług, w tym Azure Security Center.<br>
<br>

:::image type="content" source="media/threat-intel/firewall-threat.png" alt-text="Analiza zagrożeń zapory" border="false":::

W przypadku włączenia filtrowania opartego na analizie zagrożeń skojarzone reguły są przetwarzane przed dowolnymi regułami NAT, regułami sieciowymi lub regułami aplikacji.

Można wybrać opcję rejestrowania alertu, gdy reguła jest wyzwalana, lub wybrać opcję alert i tryb Odmów.

Domyślnie filtrowanie oparte na analizie zagrożeń jest włączone w trybie alertu. Nie można wyłączyć tej funkcji ani zmienić trybu do momentu udostępnienia interfejsu portalu w Twoim regionie.

:::image type="content" source="media/threat-intel/threat-intel-ui.png" alt-text="Interfejs portalu filtrowania oparty na analizie zagrożeń":::

## <a name="logs"></a>Dzienniki

Poniższy fragment dziennika przedstawia wyzwalaną regułę:

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Testowanie

- **Testowanie wychodzące** — alerty ruchu wychodzącego powinny być rzadkimi wystąpieniami, ponieważ oznacza to naruszenie bezpieczeństwa środowiska. Aby można było przetestować alerty wychodzące, została utworzona testowa nazwa FQDN, która wyzwala alert. Użyj **testmaliciousdomain.eastus.cloudapp.Azure.com** dla testów wychodzących.

- **Testowanie przychodzące** — możesz oczekiwać, że alerty dotyczące ruchu przychodzącego są wyświetlane, jeśli reguły DNAT są skonfigurowane na zaporze. Jest to prawdziwe, nawet jeśli tylko określone źródła są dozwolone w regule DNAT, a ruch jest odrzucany w inny sposób. Zapora platformy Azure nie ma alertu dotyczącego wszystkich znanych skanerów portów; tylko w przypadku skanerów, które są znane również w przypadku złośliwego działania.

## <a name="next-steps"></a>Następne kroki

- Zobacz [przykłady log Analytics zapory platformy Azure](log-analytics-samples.md)
- Dowiedz się [, jak wdrożyć i skonfigurować zaporę platformy Azure](tutorial-firewall-deploy-portal.md)
- Przejrzyj [Raport analizy zabezpieczeń firmy Microsoft](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)