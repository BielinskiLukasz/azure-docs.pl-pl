---
title: Co to jest Azure Service Health?
description: Spersonalizowane informacje o tym, w jaki sposób aplikacje platformy Azure wpływają na bieżące i przyszłe problemy z usługą i konserwację platformy Azure.
ms.topic: article
ms.date: 05/10/2019
ms.openlocfilehash: 039583d9243f8ce76b33afcee098e71a670b5285
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76939197"
---
# <a name="what-is-azure-service-health"></a>Co to jest Azure Service Health?

Platforma Azure oferuje pakiet środowisk, dzięki którym możesz uzyskać informacje o kondycji zasobów w chmurze. Te informacje obejmują bieżące i nadchodzące problemy, takie jak zdarzenia wpływające na usługę, planowana konserwacja i inne zmiany, które mogą mieć wpływ na dostępność.

Azure Service Health jest kombinacją trzech oddzielnych mniejszych usług.

[Stan platformy Azure](azure-status-overview.md) informuje o awarii usługi na platformie Azure na **[stronie stanu platformy Azure](https://status.azure.com)** . Strona to globalny widok kondycji wszystkich usług platformy Azure we wszystkich regionach świadczenia usługi Azure. Strona stanu jest dobrym odwołaniem do zdarzeń o szerokim wpływie, ale zdecydowanie zalecamy, aby bieżący użytkownicy platformy Azure korzystali z usługi Azure Service Health do uzyskiwania informacji o zdarzeniach i obsłudze platformy Azure.

[Usługa Azure Service Health](service-health-overview.md) oferuje spersonalizowany widok kondycji usług i regionów platformy Azure, których używasz. Jest to najlepsze miejsce do sprawdzania komunikatów mających wpływ na usługi o przestojach i działaniach planowanej konserwacji oraz innych porad dotyczących kondycji, ponieważ uwierzytelnione środowisko usługi Azure Service Health zna obecnie używane usługi i zasoby. Najlepszym sposobem używania usługi Service Health jest skonfigurowanie alertów, aby otrzymywać powiadomienia za pośrednictwem preferowanych kanałów komunikacyjnych w przypadku problemów z usługą, planowanej konserwacji lub innych zmian, które mogą mieć wpływ na używane usługi platformy Azure i regiony.

[Usługa Azure Resource Health](resource-health-overview.md) zawiera informacje o kondycji poszczególnych zasobów w chmurze, takich jak określone wystąpienie maszyny wirtualnej. Za pomocą usługi Azure Monitor można również skonfigurować alerty informujące o zmianach dostępności zasobów w chmurze. Usługa Azure Resource Health wraz z powiadomieniami w usłudze Azure Monitor pomogą Ci w uzyskiwaniu aktualnych informacji na temat dostępności zasobów minuta po minucie i szybko ocenić, czy problem jest spowodowany problemem po Twojej stronie czy jest związany ze zdarzeniem platformy Azure.

Razem te środowiska umożliwiają kompleksowy wgląd w kondycję platformy Azure, na poziomie szczegółowości najbardziej odpowiednim dla Ciebie.

**Obejrzyj przegląd strony stanu platformy Azure, Azure Service Health i Azure Resource Health**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]