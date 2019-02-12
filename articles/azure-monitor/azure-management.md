---
title: Zarządzanie platformą Azure i pakiet Operations Management Suite (OMS) | Microsoft Docs
description: Omówienie obszarów zarządzania aplikacjami i zasobami platformy Azure oraz linki do zawartości narzędzi zarządzania platformą Azure, które zostały wcześniej połączone w obrębie pakietu Operations Management Suite (OMS).
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/07/2018
ms.author: bwren
ms.openlocfilehash: e0ad6b212e328612d2934562e1292b5c36546478
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990276"
---
# <a name="azure-management---monitoring"></a>Zarządzanie platformą Azure — monitorowanie

Monitorowanie na platformie Azure jest jednym z aspektów zarządzania nią.  W tym artykule krótko opisano różne obszary zarządzania wymagane do wdrażania i obsługi aplikacji i zasobów na platformie Azure oraz podano linki do dokumentacji umożliwiającej rozpoczęcie pracy.

## <a name="management-in-azure"></a>Zarządzanie na platformie Azure

Zarządzanie dotyczy zadań i procesów wymaganych do obsługi aplikacji biznesowych i zasobów, które je obsługują.  Platforma Azure zawiera wiele usług i narzędzi, które współpracują ze sobą w celu zapewnienia pełnego zarządzania nie tylko dla Twoich aplikacji uruchomionych na platformie Azure, ale też dla innych chmur i rozwiązań lokalnych.  Zrozumienie dostępnych narzędzi i sposobu ich wspólnego używania w różnych scenariuszach zarządzania jest pierwszym krokiem projektowania pełnego środowiska zarządzania.

Poniższy diagram przedstawia różne obszary zarządzania wymagane do obsługi dowolnej aplikacji lub zasobu.  Te obszary można rozumieć jako ciągły cykl, w którym każdy obszar jest kolejno wymagany w okresie istnienia zasobu.  Ten cykl zaczyna się od początkowego wdrożenia, po czym następuje działanie, a na koniec wycofanie zasobu.

![Możliwości zarządzania](media/management-overview/management-capabilities.png)


W poniższych sekcjach krótko opisano różne obszary zarządzania oraz podano linki do szczegółowych informacji o głównych usługach platformy Azure dotyczących tych obszarów.

## <a name="monitor"></a>Monitorowanie
Monitorowanie to proces zbierania i analizowania danych w celu ustalania wydajności, kondycji i dostępności aplikacji biznesowych i zasobów, od których one zależą. Skuteczna strategia monitorowania pomoże poznać szczegółowe działanie różnych składników aplikacji i zwiększyć czas aktywności przez proaktywne powiadamianie o istotnych kwestiach w celu umożliwienia rozwiązywania ich, zanim staną się problemami. Monitorowanie na platformie Azure jest realizowane przede wszystkim za pomocą usługi [Azure Monitor](../azure-monitor/overview.md), która zapewnia wspólne magazyny do przechowywania danych monitorowania, wiele źródeł danych do zbierania danych z różnych warstw obsługi aplikacji i funkcje do analizowania zebranych danych oraz do reagowania na nie.

## <a name="configure"></a>Konfigurowanie
Konfigurowanie dotyczy początkowego wdrażania i konfigurowania aplikacji i zasobów oraz ich ciągłej obsługi przy użyciu poprawek i aktualizacji.  Automatyzacja tych zadań za pomocą skryptów i zasad pozwala wyeliminować nadmiarowość, zminimalizować poświęcany czas i wysiłek oraz zwiększyć dokładność i wydajność.  [Usługa Azure Automation](../automation/automation-intro.md) dostarcza większość usług do automatyzowania zadań konfiguracji.  Oprócz elementów Runbook do automatyzowania procesów zapewnia ona zarządzanie konfiguracją i aktualizacjami, co pomaga zarządzać konfiguracją za pośrednictwem zasad oraz identyfikować i wdrażać aktualizacje.

## <a name="govern"></a>Ład
Ład dotyczy mechanizmów i procesów kontrolowania aplikacji i zasobów na platformie Azure.  Obejmuje to planowanie inicjatyw i wyznaczanie strategicznych priorytetów.  Ład na platformie Azure jest zaimplementowany głównie za pomocą dwóch usług.  [Zasady platformy Azure](../governance/policy/overview.md) pozwalają tworzyć i przypisywać definicje zasad oraz zarządzać nimi. Te definicje wymuszają różne reguły i działania dotyczące zasobów, aby pozostały one zgodne ze standardami firmy i umowami dotyczącymi poziomu usług. [Usługa Azure Cost Management firmy Cloudyn](../cost-management/overview.md) umożliwia śledzenie użycia chmury i wydatków na zasoby platformy Azure i innych dostawców rozwiązań w chmurze, w tym AWS i Google.

## <a name="secure"></a>Bezpieczeństwo
Zarządzanie zabezpieczeniami aplikacji, zasobów i danych obejmuje ocenę zagrożeń, zbieranie i analizowanie danych zabezpieczeń oraz zapewnianie, że aplikacje i zasoby są projektowane i konfigurowane w bezpieczny sposób.  Funkcje monitorowania zabezpieczeń i analizy zagrożeń są dostarczane przez usługę [Azure Security Center](../security-center/security-center-intro.md), która obejmuje ujednolicone zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami w różnych obciążeniach chmur hybrydowych.  Zapoznaj się też z tematem [Introduction to Azure Security](../security/azure-security.md) (Wprowadzenie do zabezpieczeń platformy Azure), aby uzyskać obszerne informacje o zabezpieczeniach na platformie Azure oraz wskazówki dotyczące bezpiecznego konfigurowania jej zasobów.


## <a name="protect"></a>Ochrona
Ochrona dotyczy zapewniania, że aplikacje i dane są zawsze dostępne, nawet w przypadku awarii poza Twoją kontrolą.  Ochrona na platformie Azure jest zapewniana przez dwie usługi.  [Usługa Azure Backup](../backup/backup-introduction-to-azure-backup.md) oferuje tworzenie kopii zapasowych i odzyskiwanie danych w chmurze lub lokalnie.    [Usługa Azure Site Recovery](../site-recovery/site-recovery-overview.md) zapewnia wysoką dostępność aplikacji, gwarantując ciągłość działania i natychmiastowe odzyskiwanie w przypadku awarii.

## <a name="migrate"></a>Migrate (Migracja) 
Migracja dotyczy przenoszenia obciążeń działających obecnie lokalnie do chmury platformy Azure.  [Usługa Azure Migrate](../migrate/migrate-overview.md) pomaga oceniać gotowość do migracji maszyn wirtualnych do platformy Azure, uwzględniając dostosowywanie rozmiaru do wydajności oraz szacunkowe koszty.  Usługa Azure Site Recovery może ułatwić rzeczywistą migrację maszyn wirtualnych [ze środowiska lokalnego](../site-recovery/migrate-tutorial-on-premises-azure.md) lub [z usług Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md).  [Usługa Azure Database Migration](../dms/dms-overview.md) pomaga w migracji wielu źródłowych baz danych do platformy danych Azure.

