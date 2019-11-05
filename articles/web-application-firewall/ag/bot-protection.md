---
title: Konfigurowanie ochrony bot dla zapory aplikacji sieci Web platformy Azure (WAF)
description: Dowiedz się, jak skonfigurować ochronę bot dla zapory aplikacji sieci Web (WAF) w usłudze Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516866"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Konfigurowanie ochrony bot dla zapory aplikacji sieci Web w usłudze Azure Application Gateway (wersja zapoznawcza)

W tym artykule opisano sposób konfigurowania reguły ochrony bot w zaporze aplikacji sieci Web platformy Azure (WAF) dla Application Gateway przy użyciu Azure Portal. 

Możesz włączyć zarządzaną regułę ochrony bot dla WAF, aby blokować lub rejestrować żądania ze znanych złośliwych adresów IP. Adresy IP pochodzą ze źródła analizy zagrożeń firmy Microsoft. Rozwiązanie Intelligent Security Graph obsługuje analizę zagrożeń firmy Microsoft i jest używane przez wiele usług, w tym usługę Azure Security Center.

> [!NOTE]
> Zestaw reguł ochrony bot jest obecnie w publicznej wersji zapoznawczej i jest dostarczany z umową dotyczącą poziomu usług w wersji zapoznawczej. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) zapoznawczych Microsoft Azure .

## <a name="prerequisites"></a>Wymagania wstępne

Utwórz podstawowe zasady WAF dla Application Gateway, postępując zgodnie z instrukcjami opisanymi w temacie [Tworzenie zasad zapory aplikacji sieci Web dla Application Gateway](create-waf-policy-ag.md).

## <a name="enable-bot-protection-rule-set"></a>Włącz zestaw reguł ochrony bot

1. Na stronie zasady **podstawowe** , która została wcześniej utworzona, w obszarze **Ustawienia**wybierz pozycję **reguły**.  

2. Na stronie Szczegóły w sekcji **Zarządzanie regułami** z menu rozwijanego wybierz pole wyboru dla reguły ochrony bot, a następnie wybierz pozycję **Zapisz**.

> [!div class="mx-imgBorder"]
> ![](../media/bot-protection/bot-protection.png) ochrony bot

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat reguł niestandardowych, zobacz [reguły niestandardowe dla zapory aplikacji sieci Web V2 na platformie Azure Application Gateway](custom-waf-rules-overview.md).