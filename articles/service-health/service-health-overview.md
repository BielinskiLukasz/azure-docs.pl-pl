---
title: Przegląd Service Health | Microsoft Docs
description: Spersonalizowane informacje o tym, w jaki sposób aplikacje platformy Azure wpływają na bieżące i przyszłe problemy z usługą i konserwację platformy Azure.
ms.topic: article
ms.date: 05/10/2019
ms.openlocfilehash: 6a06ae22493292e33c94cc83cba1f17361bce45f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451572"
---
# <a name="service-health-overview"></a>Przegląd Service Health

Service Health udostępnia dostosowywalny pulpit nawigacyjny, który śledzi kondycję usług platformy Azure w regionach, w których są używane. Na tym pulpicie nawigacyjnym można śledzić aktywne zdarzenia, takie jak trwające problemy z usługą, nadchodząca planowana konserwacja lub odpowiednie doradcy kondycji. Gdy zdarzenia staną się nieaktywne, są one umieszczane w historii kondycji przez maksymalnie 90 dni. Na koniec możesz użyć pulpitu nawigacyjnego Service Health, aby utworzyć alerty kondycji usługi i zarządzać nimi, które aktywnie powiadamiają o problemach z usługą.

## <a name="service-health-events"></a>Zdarzenia Service Health

Service Health śledzi trzy typy zdarzeń dotyczących kondycji, które mogą mieć wpływ na zasoby:

1. **Problemy z usługą** — problemy z usługami platformy Azure, które już teraz mają wpływ. 
2. **Planowana konserwacja** — nadchodząca konserwacja, która może mieć wpływ na dostępność usług w przyszłości.  
3. **Klasyfikatory kondycji** — zmiany w usługach platformy Azure, które wymagają Twojej uwagi. Przykłady obejmują, kiedy funkcje platformy Azure są przestarzałe lub przekroczenia limitu przydziału użycia.

> [!NOTE]
> Aby wyświetlać zdarzenia Service Health, użytkownicy muszą mieć [przydaną rolę czytelnika](../role-based-access-control/role-assignments-portal.md) w ramach subskrypcji.

## <a name="get-started-with-service-health"></a>Wprowadzenie do Service Health

Aby uruchomić Pulpit nawigacyjny Service Health, wybierz kafelek Service Health na pulpicie nawigacyjnym portalu. Jeśli kafelek został wcześniej usunięty lub używasz niestandardowego pulpitu nawigacyjnego, Wyszukaj usługę Service Health w obszarze "więcej usług" (w lewym dolnym rogu pulpitu nawigacyjnego).

![Wprowadzenie do Service Health](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Zobacz bieżące problemy, które mają wpływ na usługi

W widoku **problemy dotyczące usługi** są wyświetlane wszystkie bieżące problemy dotyczące usług platformy Azure, które mają wpływ na zasoby. Można zrozumieć, kiedy problem pojawił się i jakie usługi i regiony mają wpływ. Możesz również przeczytać najnowszą aktualizację, aby zrozumieć, co platforma Azure wykonuje, aby rozwiązać ten problem. 

![Zarządzanie problemem z usługą](./media/service-health-overview/azure-service-health-overview-2.png)

Wybierz kartę **potencjalny wpływ** , aby wyświetlić konkretną listę zasobów, które mogą mieć wpływ na problem. Możesz pobrać listę woluminów CSV tych zasobów do udostępnienia zespołowi.

![Zarządzanie problemem z usługą](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Pobierz linki i wyjaśnień do pobrania 

Możesz uzyskać link do problemu, który będzie używany w systemie zarządzania problemami. Możesz pobrać plik PDF i czasami pliki CSV, aby udostępnić je osobom, które nie mają dostępu do Azure Portal.   

![Zarządzanie problemem z usługą — Zarządzanie problemami](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Uzyskaj pomoc techniczną od firmy Microsoft

Skontaktuj się z pomocą techniczną, jeśli zasób jest pozostawiony w nieprawidłowym stanie nawet po rozwiązaniu problemu.  Użyj linków pomocy technicznej znajdujących się po prawej stronie.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Przypinanie spersonalizowanej mapy kondycji do pulpitu nawigacyjnego

Filtruj Service Health, aby wyświetlić krytyczne subskrypcje, regiony i typy zasobów firmy. Zapisz filtr i Przypnij spersonalizowany świat mapy kondycji na pulpicie nawigacyjnym portalu. 

![Filtruj spersonalizowaną mapę kondycji](./media/service-health-overview/azure-service-health-overview-6a.png)

![Przypinanie spersonalizowanej mapy kondycji](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Konfigurowanie alertów dotyczących kondycji usługi

Service Health integruje się z Azure Monitor, aby ostrzec użytkownika za pośrednictwem wiadomości e-mail, wiadomości SMS i powiadomień elementu webhook w przypadku, gdy mają wpływ zasoby o krytycznym znaczeniu dla firmy. Skonfiguruj alert dziennika aktywności dla odpowiedniego zdarzenia kondycji usługi. Kierowanie tego alertu do odpowiednich osób w organizacji za pomocą grup akcji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie alertów dla Service Health](../azure-monitor/platform/alerts-activity-log-service-notifications.md)

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="next-steps"></a>Następne kroki

Skonfiguruj alerty, aby otrzymywać powiadomienia o problemach z kondycją. Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące konfigurowania Azure Service Health alertów](https://www.youtube.com/watch?v=k5d5ca8K6tc&list=PLLasX02E8BPBBSqygdRvlTnHfp1POwE8K&index=6&t=0s). 
