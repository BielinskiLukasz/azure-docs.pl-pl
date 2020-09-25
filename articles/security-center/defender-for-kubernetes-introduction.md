---
title: Azure Defender for Kubernetes — korzyści i funkcje
description: Dowiedz się więcej o zaletach i funkcjach usługi Azure Defender for Kubernetes.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: a7fb97f1036515f82fa3e61c18f95eadf0af704d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301707"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Wprowadzenie do usługi Azure Defender dla Kubernetes

Usługa Azure Kubernetes Service (AKS) to usługa zarządzana przez firmę Microsoft służąca do opracowywania i wdrażania aplikacji kontenerowych oraz zarządzania nimi.

Azure Security Center i AKS tworzą najlepszą natywną ofertę bezpieczeństwa Kubernetes w chmurze, a razem zapewniają ochronę środowiska, ochronę obciążeń i ochrony w czasie wykonywania, jak opisano poniżej.

W celu wykrywania zagrożeń dla klastrów Kubernetes Włącz **usługę Azure Defender dla Kubernetes**.

Wykrywanie zagrożeń na poziomie hosta dla węzłów AKS systemu Linux jest dostępne po włączeniu [usługi Azure Defender dla serwerów](defender-for-servers-introduction.md).

## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólnie dostępna (GA)|
|Wpisaną|**Usługa Azure Defender for Kubernetes** jest rozliczana zgodnie z opisem na [stronie cennika](security-center-pricing.md)|
|Wymagane role i uprawnienia:|**Administrator zabezpieczeń** może odrzucić alerty.<br>**Czytelnik zabezpieczeń** może przeglądać wyniki.|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Nie](./media/icons/no-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Jakie korzyści zapewnia usługa Azure Defender for Kubernetes?

### <a name="run-time-protection"></a>Ochrona w czasie wykonywania

Dzięki ciągłej analizie następujących źródeł AKS Security Center zapewnia ochronę przed zagrożeniami w czasie rzeczywistym dla środowisk kontenera i generuje alerty dotyczące zagrożeń i złośliwych działań wykrytych na poziomie klastra hosta *i* AKS. Te informacje pozwalają na szybkie rozwiązywanie problemów dotyczących zabezpieczeń i poprawę bezpieczeństwa kontenerów.

Security Center zapewnia ochronę przed zagrożeniami na różnych poziomach: 

- **Poziom hosta (dostarczany przez usługę Azure Defender dla serwerów)** — za pomocą tego samego agenta log Analytics, który Security Center używany na innych maszynach wirtualnych, usługa Azure Defender monitoruje węzły AKS systemu Linux pod kątem podejrzanych działań, takich jak wykrywanie powłoki sieci Web i połączenie z znanymi podejrzanymi adresami IP. Agent monitoruje również dla analiz specyficznych dla kontenera, takich jak tworzenie kontenera uprzywilejowanego, podejrzany dostęp do serwerów interfejsu API i serwery Secure Shell (SSH) działające wewnątrz kontenera Docker.

    Listę alertów na poziomie hosta AKS można znaleźć w [tabeli referencyjnej alertów](alerts-reference.md#alerts-containerhost).

    >[!IMPORTANT]
    > Jeśli zdecydujesz się nie instalować agentów na hostach, będziesz otrzymywać tylko podzbiór korzyści z ochrony przed zagrożeniami i alerty zabezpieczeń. Nadal będziesz otrzymywać alerty dotyczące analizy sieci i komunikacji ze złośliwymi serwerami.

- **Poziom klastra AKS (udostępniony przez usługę Azure Defender dla Kubernetes)** — na poziomie klastra ochrona przed zagrożeniami jest oparta na analizie dzienników inspekcji Kubernetes. Aby włączyć monitorowanie **bez agenta** , Włącz usługę Azure Defender. Aby generować alerty na tym poziomie, Security Center monitoruje usługi zarządzane przez AKS przy użyciu dzienników pobranych przez AKS. Przykłady zdarzeń na tym poziomie obejmują uwidocznione pulpity nawigacyjne Kubernetes, tworzenie uprzywilejowanych ról i tworzenie poufnych instalacji.

    Listę alertów na poziomie klastra AKS można znaleźć w [tabeli referencyjnej alertów](alerts-reference.md#alerts-akscluster).

    >[!NOTE]
    > Security Center generuje alerty zabezpieczeń dotyczące akcji i wdrożeń usługi Azure Kubernetes w przypadku włączenia opcji Kubernetes w ustawieniach subskrypcji. 

Ponadto nasz globalny zespół badaczy ds. zabezpieczeń stale monitoruje zagrożenie w poziomie. Po ich odnalezieniu są dodawane alerty i luki specyficzne dla kontenera.

> [!TIP]
> Alerty kontenera można symulować, postępując zgodnie z instrukcjami zawartymi w [tym wpisie w blogu](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).



## <a name="how-does-security-centers-kubernetes-protection-work"></a>Jak działa Security Center Ochrona Kubernetes?

Poniżej znajduje się Diagram wysokiego poziomu interakcji między Azure Security Center, usługą Azure Kubernetes i Azure Policy.

Można zobaczyć, że elementy otrzymane i przeanalizowane przez Security Center obejmują:

- Inspekcja dzienników z serwera interfejsu API
- nieprzetworzone zdarzenia zabezpieczeń z agenta Log Analytics
- Informacje o konfiguracji klastra z klastra AKS
- Konfiguracja obciążenia z Azure Policy (za pośrednictwem **Azure Policy dodatku dla Kubernetes**). [Dowiedz się więcej o najlepszych rozwiązaniach dotyczących ochrony obciążeń przy użyciu funkcji Kubernetes Admission Control](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Architektura wysokiego poziomu interakcji między Azure Security Center, usługą Azure Kubernetes i Azure Policy" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender for Kubernetes — często zadawane pytania

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Czy nadal mogę uzyskać ochronę AKSą bez agenta Log Analytics?

Jak wspomniano powyżej, opcjonalny plan **usługi Azure Defender for Kubernetes** zapewnia ochronę na poziomie klastra, a agent log Analytics **usługi Azure Defender dla serwerów** chroni Twoje węzły. 

Zalecamy wdrożenie obu tych metod w celu zapewnienia najwyższej możliwej ochrony.

Jeśli nie zdecydujesz się na zainstalowanie agenta na hostach, otrzymasz tylko podzestaw korzyści z ochrony przed zagrożeniami i alertów zabezpieczeń. Nadal będziesz otrzymywać alerty dotyczące analizy sieci i komunikacji ze złośliwymi serwerami.


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje na temat Security Center ochrony Kubernetes, w tym usługi Azure Defender dla Kubernetes. 

W przypadku pokrewnego materiału zapoznaj się z następującymi artykułami: 

- [Włączanie usługi Azure Defender](security-center-pricing.md)
- [Eksportowanie alertów do platformy Azure lub SIEM innej firmy](continuous-export.md)
- [Tabela referencyjna alertów](alerts-reference.md)