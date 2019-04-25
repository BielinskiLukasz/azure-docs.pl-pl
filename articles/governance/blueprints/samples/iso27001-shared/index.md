---
title: Przykłady — strategia usług udostępnionych ISO 27001 — omówienie
description: Omówienie i architektura przykładu strategii usług udostępnionych ISO 27001.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: c54d8aedb9464364f93a087de4bdb00c693a96ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60875161"
---
# <a name="overview-of-the-iso-27001-shared-services-blueprint-sample"></a>Omówienie przykładu strategii usług udostępnionych ISO 27001.

Przykład strategii usług udostępnionych ISO 27001 zawiera zestaw wzorców zgodnej infrastruktury i ochronę w postaci zasad, które ułatwiają zaświadczanie za pomocą standardu ISO 27001. Ta strategia pomaga klientom wdrażać architektury chmurowe, które oferują rozwiązania dla scenariuszy z wymaganiami w zakresie akredytacji lub zgodności.

Przykład strategii [obciążenia środowiska App Service Environment/bazy danych SQL ISO 27001](../iso27001-ase-sql-workload/index.md) stanowi rozszerzenie tego przykładu.

## <a name="architecture"></a>Architektura

Przykład strategii usług udostępnionych ISO 27001 wdraża na platformie Azure podstawową infrastrukturę, która umożliwia organizacjom hostowanie wielu obciążeń zgodnie z metodą wirtualnego centrum danych.
Wirtualne centrum danych to zestaw sprawdzonych architektur referencyjnych, narzędzi automatyzacji i modeli zaangażowania używanych przez firmę Microsoft u jej największych klientów korporacyjnych. Przykład strategii usług udostępnionych jest oparty na w pełni natywnym środowisku wirtualnego centrum danych platformy Azure, które pokazano poniżej.

![Przykładowy projekt strategii usług udostępnionych ISO 27001](../../media/sample-iso27001-shared/iso27001-shared-services-blueprint-sample-design.png)

To środowisko składa się z kilku usług platformy Azure, które udostępniają bezpieczną, w pełni monitorowaną infrastrukturę usług udostępnionych z obsługą przedsiębiorstw zgodną ze standardami ISO 27001. To środowisko zawiera następujące składniki:

- [Kontrola dostępu oparta na rolach](../../../../role-based-access-control/overview.md) — role używane do dzielenia zadań z perspektywy płaszczyzny kontroli. Przed wdrożeniem infrastruktury są definiowane trzy role:
  - Rola NetOps ma uprawnienia do zarządzania środowiskiem sieci, w tym ustawieniami zapory, ustawieniami sieciowej grupy zabezpieczeń, routingiem i innymi funkcjami sieci
  - Rola SecOps ma uprawnienia niezbędne do wdrażania usługi [Azure Security Center](../../../../security-center/security-center-intro.md) i zarządzania nią oraz definiowania [zasad platformy Azure](../../../policy/overview.md), a także inne prawa związane z zabezpieczeniami
  - Rola SysOps ma uprawnienia niezbędne do zdefiniowania [zasad platformy Azure](../../../policy/overview.md) w ramach subskrypcji i zarządzania usługą [Log Analytics](../../../../azure-monitor/overview.md) dla całego środowiska oraz inne prawa operacyjne
- Usługa [Log Analytics](../../../../azure-monitor/overview.md) jest wdrażana jako pierwsza usługa platformy Azure, aby upewnić się, że wszystkie akcje i usługi są rejestrowane w centralnej lokalizacji od czasu uruchomienia bezpiecznego wdrożenia
- Sieć wirtualna obsługująca podsieci na potrzeby łączności z lokalnym centrum danych, stos przychodzący i wychodzący na potrzeby łączności z Internetem oraz podsieć usługi udostępnionej używającą sieciowych grup zabezpieczeń i grup zabezpieczeń aplikacji na potrzeby pełnej mikrosegmentacji, która zawiera następujące elementy:
  - Host przesiadkowy lub host bastionu używany do zarządzania, który jest dostępny tylko za pośrednictwem usługi [Azure Firewall](../../../../firewall/overview.md) wdrożonej w podsieci stosu przychodzącego
  - Dwie maszyny wirtualne z uruchomionymi usługami Active Directory Domain Services (ADDS) i systemem DNS dostępne tylko za pośrednictwem serwera przesiadkowego, które można skonfigurować tylko do replikacji usługi AD za pośrednictwem sieci VPN lub połączenia usługi [ExpressRoute](../../../../expressroute/expressroute-introduction.md) (nie są wdrażane w ramach strategii)
  - Korzystanie z usługi [Azure Net Watcher](../../../../network-watcher/network-watcher-monitoring-overview.md) i standardowej ochrony przed atakami DDoS
- Wystąpienie usługi [Azure Key Vault](../../../../key-vault/key-vault-whatis.md) umożliwiające hostowanie wpisów tajnych używanych na potrzeby maszyn wirtualnych wdrożonych w środowisku usług udostępnionych

Wszystkie te elementy są zgodne ze sprawdzonymi rozwiązaniami opublikowanymi na stronie [Centrum architektury platformy Azure — architektury referencyjne](/azure/architecture/reference-architectures/).

> [!NOTE]
> Infrastruktura usług udostępnionych ISO 27001 wdraża podstawową architekturę dla obciążeń.
> Nadal należy wdrożyć dla niej obciążenia.

Więcej informacji można znaleźć w [dokumentacji wirtualnego centrum danych](/azure/architecture/vdc/).

## <a name="next-steps"></a>Następne kroki

Przeczytano omówienie i informacje o architekturze przykładu strategii usług udostępnionych ISO 27001.
Następnie przeczytaj następujące artykuły, aby poznać mapowanie kontrolek i sposób wdrażania tego przykładu:

> [!div class="nextstepaction"]
> [Strategia usług udostępnionych ISO 27001 — mapowanie kontrolek](./control-mapping.md)
> [Strategia usług udostępnionych ISO 27001 — procedura wdrażania](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).