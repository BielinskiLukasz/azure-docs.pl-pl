---
title: Omówienie przykładu strategii CAF Foundation
description: Omówienie i architektura strategii Cloud Adoption Framework (CAF) dla usługi Azure Foundation z przykładem.
ms.date: 09/14/2020
ms.topic: sample
ms.openlocfilehash: cce5ea001b32ae92542f38f8f11427e85075cee9
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531699"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Omówienie przykładowej strategii Microsoft Cloud Adoption Framework for Azure Foundation

Strategia Microsoft Cloud Adoption Framework for Azure (CAF) Foundation wdraża zestaw zasobów infrastruktury podstawowej i kontroli zasad, które są wymagane dla pierwszej aplikacji produkcyjnej na platformie Azure. Ta strategia podstawowa jest oparta na zalecanym wzorcu opisanym w przewodniku CAF.

## <a name="architecture"></a>Architektura

Przykładowa strategia CAF Foundation służy do wdrażania zalecanych zasobów infrastruktury na platformie Azure, które mogą być używane przez organizacje do wdrażania podstawowych kontroli niezbędnych do zarządzania ich zasobami w chmurze. Ten przykład służy do wdrażania i wymuszania zasobów, zasad i szablonów, dzięki którym organizacja będzie mogła zacząć korzystać z platformy Azure w zaufany sposób.

:::image type="complex" source="../../media/caf-blueprints/caf-foundation-architecture.png" alt-text="Podstawy struktury CAF — obraz przedstawia elementy instalowane w ramach wytycznych w przewodniku CAF na potrzeby tworzenia podstaw do rozpoczęcia pracy z platformą Azure." border="false":::
   Opisuje architekturę platformy Azure, która jest osiągana poprzez wdrożenie strategii Podstawy struktury CAF.  Ma zastosowanie w przypadku subskrypcji z grupami zasobów, która składa się z konta magazynu do przechowywania dzienników i usługi Log Analytics skonfigurowanej do przechowywania na koncie magazynu. Przedstawia również usługę Azure Key Vault skonfigurowaną przy użyciu standardowej konfiguracji usługi Azure Security Center. Dostęp do wszystkich tych kluczowych infrastruktur jest możliwy za pomocą usługi Azure Active Directory i wymuszany za pomocą usługi Azure Policy.     
:::image-end:::

Ta implementacja obejmuje kilka usług platformy Azure służących do udostępniania bezpiecznych, w pełni monitorowanych podstaw z obsługą przedsiębiorstw. To środowisko zawiera następujące składniki:

- Wystąpienie usługi [Azure Key Vault](../../../../key-vault/general/overview.md) umożliwiające hostowanie wpisów tajnych używanych na potrzeby maszyn wirtualnych wdrożonych w środowisku usług udostępnionych
- Usługa [Log Analytics](../../../../azure-monitor/overview.md) jest wdrażana, aby upewnić się, że wszystkie akcje i usługi od czasu uruchomienia bezpiecznego wdrożenia są rejestrowane w centralnej lokalizacji na [kontach magazynu](../../../../storage/common/storage-introduction.md) do celów rejestracji diagnostycznej
- Wdrożenie usługi [Azure Security Center](../../../../security-center/security-center-intro.md) (wersja standardowa) zapewnia ochronę przed zagrożeniami dla zmigrowanych obciążeń
- Strategia również definiuje i wdraża [zasady platformy Azure](../../../policy/overview.md) do celów: 
  - Tagowania (CostCenter) stosowanego do grup zasobów
  - Dołączania zasobów w grupie zasobów za pomocą tagu CostCenter
  - Dozwolonego regionu świadczenia usługi Azure dla zasobów i grup zasobów
  - Dozwolonych jednostek SKU konta magazynu (wybierz podczas wdrażania)
  - Dozwolonych jednostek SKU maszyny wirtualnej platformy Azure (wybierz podczas wdrażania)
  - Wymagania wdrożenia usługi Network Watcher 
  - Wymagania szyfrowania bezpiecznego transferu konta magazynu platformy Azure
  - Odrzucania typów zasobów (wybierz podczas wdrażania)  
- Inicjatyw
  - Włączanie monitorowania w usłudze Azure Security Center (ponad 100 definicji zasad)

Wszystkie te elementy są zgodne ze sprawdzonymi rozwiązaniami opublikowanymi na stronie [Centrum architektury platformy Azure — architektury referencyjne](/azure/architecture/reference-architectures/).

> [!NOTE]
> Strategia CAF Foundation określa architekturę podstaw dla obciążeń.
> Nadal należy wdrożyć dla niej obciążenia.

Aby uzyskać więcej informacji, zobacz [Przewodnik Microsoft Cloud Adoption Framework dla platformy Azure — gotowość](/azure/cloud-adoption-framework/ready/).

## <a name="next-steps"></a>Następne kroki

Zostały przeczytane omówienie i informacje o architekturze przykładowej strategii CAF Foundation.

> [!div class="nextstepaction"]
> [Strategia CAF Foundation — etapy wdrażania](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
