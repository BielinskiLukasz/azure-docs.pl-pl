---
title: Migrowanie zasad dostępu warunkowego — Azure Active Directory
description: Dowiedz się, co musisz wiedzieć, aby przeprowadzić migrację klasycznych zasad w Azure Portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33b1f76dd1489e00115d0f805add8d754038df84
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77185907"
---
# <a name="conditional-access-classic-policy-migration"></a>Migracja zasad dostępu warunkowego do klasycznej zasady

Dostęp warunkowy jest narzędziem używanym przez Azure Active Directory do przenoszenia sygnałów ze sobą, podejmowania decyzji i wymuszania zasad organizacji. Dostęp warunkowy jest sercem nowej płaszczyzny kontroli sterowanej tożsamością. Mimo że przeznaczenie jest takie samo, wydanie nowej Azure Portal wprowadziło znaczące ulepszenia dotyczące sposobu działania dostępu warunkowego.

Rozważ Migrowanie zasad, które nie zostały utworzone w Azure Portal, ponieważ:

- Teraz możesz rozwiązywać scenariusze, których nie można obsłużyć wcześniej.
- Aby zmniejszyć liczbę zasad, którymi trzeba zarządzać, można je skonsolidować.
- Można zarządzać wszystkimi zasadami dostępu warunkowego w jednej centralnej lokalizacji.
- Klasyczny portal Azure zostanie wycofany.

W tym artykule wyjaśniono, co należy wiedzieć, aby przeprowadzić migrację istniejących zasad dostępu warunkowego do nowej struktury.

## <a name="classic-policies"></a>Zasady klasyczne

W [Azure Portal](https://portal.azure.com)zasady dostępu warunkowego można znaleźć w obszarze **Azure Active Directory** > **zabezpieczenia** > **dostęp warunkowy**. Twoja organizacja może również mieć starsze zasady dostępu warunkowego, które nie zostały utworzone przy użyciu tej strony. Te zasady są znane jako *zasady klasyczne*. Klasyczne zasady są zasadami dostępu warunkowego, które zostały utworzone w programie:

- Klasyczny portal Azure
- Klasyczny portal usługi Intune
- Portal Intune App Protection

Na stronie **dostęp warunkowy** możesz uzyskać dostęp do klasycznych zasad, klikając pozycję [**zasady klasyczne**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) w sekcji **Zarządzanie** . 

![Dostęp warunkowy w usłudze Azure AD prezentujący widok zasad klasycznych](./media/policy-migration/71.png)

Widok **zasady klasyczne** zapewnia następujące możliwości:

- Filtrowanie klasycznych zasad.
- Wyłącz zasady klasyczne.
- Przejrzyj ustawienia klasycznych zasad i wyłącz je.

   ![Klasyczne Szczegóły zasad, w tym istniejąca konfiguracja zasad](./media/policy-migration/74.png)

> [!WARNING]
> Po wyłączeniu zasad klasycznych nie można ponownie włączyć.

Widok szczegółów klasycznych zasad umożliwia dokumentowanie ustawień, modyfikowanie uwzględnionych lub wykluczonych grup oraz wyłączenie zasad.

![Szczegóły zasad-grupy do dołączenia lub wykluczenia](./media/policy-migration/75.png)

Zmieniając wybrane grupy lub wyłączając określone grupy, można testować efekt wyłączonych zasad klasycznych dla kilku użytkowników testowych przed wyłączeniem zasad dla wszystkich uwzględnionych użytkowników i grup.
 
## <a name="migration-considerations"></a>Zagadnienia dotyczące migracji

W tym artykule zasady dostępu warunkowego usługi Azure AD są również określane jako *nowe zasady*.
Twoje klasyczne zasady nadal pracują obok nowych zasad, dopóki nie zostaną wyłączone lub usunięte. 

Następujące aspekty są istotne w kontekście konsolidacji zasad:

- Chociaż zasady klasyczne są powiązane z określoną aplikacją w chmurze, możesz wybrać dowolną liczbę aplikacji w chmurze, które będą potrzebne w ramach nowych zasad.
- Kontrolki klasycznych zasad i nowe zasady dla aplikacji w chmurze wymagają, aby wszystkie kontrolki (*i*) zostały spełnione. 
- W nowych zasadach można:
   - W razie potrzeby należy połączyć wiele warunków w danym scenariuszu. 
   - Wybierz kilka wymagań dotyczących dotacji jako kontroli dostępu i połącz je z logicznym *lub* (wymaga jednego z wybranych kontrolek) lub z logicznym *i* (Wymagaj wszystkich zaznaczonych kontrolek).

### <a name="office-365-exchange-online"></a>Pakiet Office 365 Exchange Online

Jeśli chcesz przeprowadzić migrację klasycznych zasad dla **pakietu Office 365 Exchange Online** , który zawiera **Exchange Active Sync** jako warunek aplikacji klienckich, możesz nie być w stanie skonsolidować ich w jednej nowej zasadzie. 

Dotyczy to na przykład sytuacji, w której chcesz obsługiwać wszystkie typy aplikacji klienta. W nowych zasadach, które mają **Exchange Active Sync** jako warunek aplikacji klienckich, nie można wybrać innych aplikacji klienckich.

![Dostęp warunkowy Wybieranie aplikacji klienckich](./media/policy-migration/64.png)

Konsolidacja do jednej nowej zasady jest również niemożliwa, jeśli zasady klasyczne zawierają kilka warunków. Nowe zasady, które mają skonfigurowany warunek **Exchange Active Sync** jako aplikacje klienckie, nie obsługują innych warunków:   

![Program Exchange ActiveSync nie obsługuje wybranych warunków.](./media/policy-migration/08.png)

Jeśli masz nowe zasady, dla których skonfigurowano warunek aplikacji klienta **Exchange Active Sync** , musisz upewnić się, że wszystkie inne warunki nie są skonfigurowane. 

![Warunki dostępu warunkowego](./media/policy-migration/16.png)
 
Klasyczne zasady oparte na aplikacji dla pakietu Office 365 Exchange Online, które zawierają **Exchange Active Sync** jako warunek aplikacji klienckich Zezwalaj na **obsługiwane** i **nieobsługiwane** platformy urządzeń. Chociaż nie można skonfigurować poszczególnych platform urządzeń w odniesieniu do pokrewnych nowych zasad, można ograniczyć obsługę tylko do [obsługiwanych platform urządzeń](concept-conditional-access-conditions.md#device-platforms) . 

![Dostęp warunkowy wybierz pozycję Exchange ActiveSync](./media/policy-migration/65.png)

Można skonsolidować wiele klasycznych zasad, które zawierają **Exchange Active Sync** jako warunek aplikacji klienckich, jeśli mają:

- Tylko **Exchange Active Sync** jako warunek 
- Skonfigurowano kilka wymagań dotyczących przyznawania dostępu

Typowym scenariuszem jest konsolidacja:

- Klasyczne zasady oparte na urządzeniach z klasycznego portalu Azure 
- Klasyczne zasady oparte na aplikacji w portalu ochrony aplikacji usługi Intune 
 
W takim przypadku można skonsolidować zasady klasyczne do jednej nowej zasady, która ma wybrane wymagania.

![Kontrola dostępu warunkowego](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Platformy urządzeń

Zasady klasyczne z kontrolkami opartymi na aplikacji są wstępnie skonfigurowane z systemami iOS i Android jako warunek platformy urządzenia. 

W nowych zasadach należy wybrać [platformy urządzeń](concept-conditional-access-conditions.md#device-platforms) , które mają być obsługiwane pojedynczo.

![Wybór platform urządzeń z dostępem warunkowym](./media/policy-migration/41.png)

## <a name="next-steps"></a>Następne kroki

- [Użyj trybu tylko do raportowania dla dostępu warunkowego, aby określić wpływ nowych decyzji dotyczących zasad.](concept-conditional-access-report-only.md)
- Jeśli chcesz dowiedzieć się, jak skonfigurować zasady dostępu warunkowego, zobacz [reguły dostępu warunkowego](concept-conditional-access-policy-common.md).
- Aby skonfigurować zasady dostępu warunkowego dla środowiska, zobacz artykuł [How to: Planowanie wdrożenia dostępu warunkowego w Azure Active Directory](plan-conditional-access.md). 
