---
title: Dostosowywanie reguł zapory aplikacji sieci web w usłudze Azure Application Gateway — PowerShell | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje na temat sposobu Dostosowywanie reguł zapory aplikacji sieci web w usłudze Application Gateway przy użyciu programu PowerShell.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: victorh
ms.openlocfilehash: dfcd82a17a399f213f5c4e32326a8995d26e8458
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218273"
---
# <a name="customize-web-application-firewall-rules-through-powershell"></a>Dostosowywanie reguł zapory aplikacji sieci web za pomocą programu PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [Program PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](application-gateway-customize-waf-rules-cli.md)

Zapora aplikacji sieci web usługi Azure Application Gateway (WAF) zapewnia ochronę aplikacji sieci web. Te zabezpieczenia stosowane są dostarczane przez Otwórz sieci Web aplikacji Security Project (OWASP) podstawowych reguł Ustaw (CRS). Niektóre reguły może spowodować, że wyniki fałszywie dodatnie i blokować ruch rzeczywistych. Z tego powodu Application Gateway oferuje możliwości dostosowywania grup reguł i reguł. Aby uzyskać więcej informacji na temat grup określonych reguł i reguł, zobacz [listę grup reguł CRS zapory aplikacji sieci web i reguły](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Widok grup reguł i reguł

W poniższych przykładach kodu pokazano, jak wyświetlić reguły i grup reguł, które można skonfigurować na bramie aplikacji z obsługą zapory aplikacji sieci Web.

### <a name="view-rule-groups"></a>Wyświetl reguły grupy

Poniższy przykład pokazuje sposób wyświetlania grup reguł:

```powershell
Get-AzureRmApplicationGatewayAvailableWafRuleSets
```

Poniższe dane wyjściowe są obcięte odpowiedzi z poprzedniego przykładu:

```
OWASP (Ver. 3.0):

    General:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            200004     Possible Multipart Unmatched Boundary.

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            911012     Rule 911012
            911100     Method is not allowed by policy
            911013     Rule 911013
            911014     Rule 911014
            911015     Rule 911015
            911016     Rule 911016
            911017     Rule 911017
            911018     Rule 911018

    REQUEST-913-SCANNER-DETECTION:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            913011     Rule 913011
            913012     Rule 913012
            913100     Found User-Agent associated with security scanner
            913110     Found request header associated with security scanner
            913120     Found request filename/argument associated with security scanner
            913013     Rule 913013
            913014     Rule 913014
            913101     Found User-Agent associated with scripting/generic HTTP client
            913102     Found User-Agent associated with web crawler/bot
            913015     Rule 913015
            913016     Rule 913016
            913017     Rule 913017
            913018     Rule 913018

            ...        ...
```

## <a name="disable-rules"></a>Wyłączanie reguł

Poniższy przykład powoduje wyłączenie reguł `911011` i `911012` na bramę aplikacji:

```powershell
$disabledrules=New-AzureRmApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

## <a name="next-steps"></a>Kolejne kroki

Po skonfigurowaniu reguł wyłączonych, możesz dowiedzieć się, jak wyświetlić dzienniki zapory aplikacji sieci Web. Aby uzyskać więcej informacji, zobacz [diagnostyki bramy aplikacji](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
