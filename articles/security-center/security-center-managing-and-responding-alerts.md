---
title: Zarządzanie alertami zabezpieczeń w usłudze Azure Security Center | Microsoft Docs
description: Ten dokument ułatwia zarządzanie alertami zabezpieczeń i reagowanie na nie przy użyciu funkcji Centrum zabezpieczeń Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: f8b09c71e9ad55528788f97fb986606f21e8b0ec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84769787"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Zarządzanie alertami zabezpieczeń i reagowanie na nie w usłudze Azure Security Center

W tym temacie pokazano, jak wyświetlać i przetwarzać alerty otrzymane w celu ochrony Twoich zasobów. 

* Aby dowiedzieć się więcej o różnych typach alertów, zobacz [typy alertów zabezpieczeń](alerts-reference.md).
* Aby zapoznać się z omówieniem Security Center generowania alertów, zobacz [jak Azure Security Center wykrywać zagrożenia i reagować na](security-center-alerts-overview.md)nie.

> [!NOTE]
> Aby włączyć wykrywanie zaawansowane, przeprowadź uaktualnienie usługi Azure Security Center do wersji Standard. Dostępna jest bezpłatna wersja próbna. W celu uaktualnienia wybierz pozycję warstwa cenowa w obszarze [zasady zabezpieczeń](tutorial-security-policy.md). Aby dowiedzieć się więcej, zobacz [cennik usługi Azure Security Center](security-center-pricing.md).

## <a name="what-are-security-alerts"></a>Czym są alerty zabezpieczeń?
Usługa Security Center automatycznie gromadzi, analizuje i integruje dane dzienników z zasobów platformy Azure, sieci oraz połączonych rozwiązań partnerskich, takich jak rozwiązania zapory i ochrony punktów końcowych, aby wykrywać prawdziwe zagrożenia i redukować liczbę fałszywych alarmów. W usłudze Security Center jest wyświetlana lista alertów zabezpieczeń uporządkowanych według priorytetu oraz informacje potrzebne do szybkiego analizowania problemu i zalecenia dotyczące postępowania w razie ataku.

> [!NOTE]
> Aby uzyskać więcej informacji na temat sposobu działania funkcji wykrywania Security Center, zobacz [jak Azure Security Center wykrywać zagrożenia i reagować na](security-center-alerts-overview.md#detect-threats)nie.

## <a name="manage-your-security-alerts"></a>Zarządzanie alertami zabezpieczeń

1. Na pulpicie nawigacyjnym Security Center zapoznaj się z kafelkiem **Ochrona przed zagrożeniami** , aby wyświetlić i zapoznać się z alertami.

    ![Kafelek Alerty zabezpieczeń w usłudze Security Center](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. Aby wyświetlić więcej szczegółów na temat alertów, kliknij kafelek.

   ![Alerty zabezpieczeń w usłudze Security Center](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Aby odfiltrować wyświetlane alerty, kliknij przycisk **Filtruj**, a następnie w bloku **filtru** , który zostanie otwarty, wybierz opcje filtru, które chcesz zastosować. Lista jest aktualizowana zgodnie z wybranym filtrem. Filtrowanie może być bardzo przydatne. Możesz na przykład sprawdzić alerty zabezpieczeń, które wystąpiły w ciągu ostatnich 24 godzin, ponieważ badasz potencjalne naruszenie zabezpieczeń systemu.

    ![Filtrowanie alertów w usłudze Security Center](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>Odpowiadanie na alerty zabezpieczeń

1. Na liście **alerty zabezpieczeń** Kliknij alert zabezpieczeń. Pokazywane są zasoby i kroki, które należy wykonać w celu rozwiązania ataku.

    ![Odpowiadanie na alerty zabezpieczeń](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. Po przejrzeniu informacji kliknij zaatakowany zasób.

    W lewym okienku na stronie alertu zabezpieczeń znajdują się ogólne informacje dotyczące alertu zabezpieczeń: tytuł, ważność, stan, czas działania, opis podejrzanego działania oraz zaatakowany zasób. Obok zasobu, którego dotyczy problem, są Tagi platformy Azure istotne dla zasobu. Użyj tych elementów do wywnioskowania kontekstu organizacyjnego zasobu podczas badania alertu.

    Prawe okienko zawiera kartę **szczegóły alertu** zawierającą dalsze szczegóły alertu, który ułatwia badanie problemu: adresów IP, plików, procesów i nie tylko.
     
    ![Sugestie dotyczące czynności, które należy wykonać, aby uzyskać informacje o alertach zabezpieczeń](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    Również w prawym okienku jest karta **podjęcie akcji** . Użyj tej karty, aby wykonać dalsze działania dotyczące alertu zabezpieczeń. Akcje, takie jak:
    - *Eliminowanie zagrożeń* — zawiera ręczne kroki korygowania dla tego alertu zabezpieczeń
    - *Zapobiegaj atakom w przyszłości* — zawiera zalecenia dotyczące zabezpieczeń, które pomagają w zmniejszeniu podatności na ataki, zwiększyć bezpieczeństwo stan i w ten sposób zapobiec atakom w przyszłości
    - *Wyzwalanie automatycznej odpowiedzi* — udostępnia opcję wyzwalania aplikacji logiki jako odpowiedzi na ten alert zabezpieczeń
    - *Pomijaj podobne alerty* — oferuje opcję pomijania przyszłych alertów o podobnych cechach, jeśli alert nie jest odpowiedni dla Twojej organizacji.

    ![Przejmij kartę z akcją](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>Zobacz także

W tym dokumencie przedstawiono sposób wyświetlania alertów zabezpieczeń. Pokrewnych materiałów można znaleźć na następujących stronach:

- [Konfigurowanie reguł pomijania alertów](alerts-suppression-rules.md)
- [Automatyzowanie odpowiedzi na alerty i zalecenia z automatyzacją przepływu pracy](workflow-automation.md)
