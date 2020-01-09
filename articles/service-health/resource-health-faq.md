---
title: Azure Resource Health często zadawane pytania
description: Omówienie Azure Resource Health
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 5a15ded1c279e700fb71e5ea7aa866e5afc29f8a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426521"
---
# <a name="azure-resource-health-faq"></a>Azure Resource Health często zadawane pytania
Poznaj odpowiedzi na często zadawane pytania dotyczące Azure Resource Health.

## <a name="what-is-azure-resource-health"></a>Co to jest Azure Resource Health?
Usługa Resource Health ułatwia diagnozowanie i uzyskanie pomocy technicznej, gdy problem z platformą Azure ma wpływ na zasoby. Informuje ona o bieżącej i wcześniejszej kondycji zasobów oraz pomaga ograniczyć skutki problemów. Usługa Resource Health zapewnia pomoc techniczną, gdy potrzebujesz pomocy podczas rozwiązywania problemów z usługami platformy Azure.  

## <a name="what-is-the-resource-health-intended-for"></a>Do czego służy Resource Health?
Po wykryciu problemu z zasobem Resource Health może pomóc zdiagnozować główną przyczynę. Zapewnia ona pomoc w rozwiązywaniu problemów i pomocy technicznej, gdy potrzebna jest dalsza pomoc w zakresie usług platformy Azure.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Jakie kontrole kondycji są wykonywane przez Resource Health?
Kondycja zasobów wykonuje różne sprawdzenia na podstawie [typu zasobu](resource-health-checks-resource-types.md). Te testy zostały zaprojektowane w celu zaimplementowania trzech typów problemów: 
- Niezaplanowanych zdarzeń, na przykład nieoczekiwany ponowny rozruch hosta
- Planowane zdarzenia, takie jak zaplanowane aktualizacje systemu operacyjnego hosta
- Zdarzenia wyzwalane przez akcje użytkownika, na przykład ponowne uruchamianie maszyny wirtualnej przez użytkownika

## <a name="what-does-each-of-the-health-status-mean"></a>Co oznacza każdy stan kondycji?
Istnieją trzy różne stany kondycji:
- Dostępne: nie ma żadnych znanych problemów z platformą Azure, które mogą mieć wpływ na ten zasób
- Niedostępne: Kondycja zasobów wykryła problemy, które mają wpływ na zasób
- Nieznane: kondycja zasobu nie może określić kondycji zasobu, ponieważ spowodowało ona zatrzymanie uzyskiwania informacji o nim. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>Co oznacza stan nieznany? Czy wystąpił problem z moim zasobem?
Stan kondycji jest ustawiany na nieznany, gdy Resource Health przestaje odbierać informacje o konkretnym zasobie. Chociaż ten stan nie jest ostatecznym oznaczeniem stanu zasobu, w przypadkach, w których występują problemy, może to wskazywać na problem z platformą Azure.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Jak uzyskać pomoc dotyczącą zasobu, który jest niedostępny?
Żądanie pomocy technicznej można przesłać z bloku Resource Health. Nie potrzebujesz umowy pomocy technicznej z firmą Microsoft, aby otworzyć żądanie, gdy zasób jest niedostępny z powodu zdarzeń platformy.

## <a name="does-resource-health-differentiate-between-unavailability-cased-by-platform-problems-versus-something-i-did"></a>Czy Resource Health różnią się od niedostępności w przypadku problemów z platformą w porównaniu z czymś?
Tak, gdy zasób jest niedostępny, Resource Health identyfikuje główną przyczynę w ramach jednej z następujących kategorii: 
-   Akcja zainicjowana przez użytkownika
-   Planowane zdarzenie 
-   Niezaplanowane zdarzenie

W portalu akcje zainicjowane przez użytkownika są wyświetlane przy użyciu niebieską ikonę powiadomienia, podczas gdy planowane i niezaplanowane zdarzenia są wyświetlane przy użyciu czerwonej ikony ostrzeżenia. Więcej szczegółowych informacji znajduje się w [Resource Health przegląd](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Czy mogę zintegrować Resource Health z narzędziami monitorowania?
W obszarze Kondycja zasobów jest [obsługiwana wersja zapoznawcza](resource-health-alert-arm-template-guide.md) alertów opartych na dzienniku aktywności. Alerty dziennika [aktywności służą](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) do powiadamiania użytkowników o wyzwoleniu alertu. Grupy akcji obsługują wiele kanałów powiadomień, takich jak wiadomości e-mail, wiadomości SMS, elementy webhook i akcje narzędzia ITSM.

## <a name="where-do-i-find-resource-health"></a>Gdzie mogę znaleźć Resource Health?
Po zalogowaniu się do Azure Portal istnieje wiele sposobów uzyskiwania dostępu do Resource Health:
- Przejdź do zasobu. W okienku nawigacji po lewej stronie wybierz pozycję **Kondycja zasobów**
- Przejdź do bloku Azure Service Health.  W okienku nawigacji po lewej stronie wybierz pozycję **kondycja zasobu**.
- Otwórz blok **Pomoc i obsługa** , wybierając znak zapytania w prawym górnym rogu portalu, a następnie wybierając pozycję **Pomoc i obsługa techniczna**. Po otwarciu bloku wybierz pozycję **zasób kondycja**

Możesz również użyć interfejsu API Resource Health, aby uzyskać informacje o kondycji zasobów.

## <a name="is-resource-health-available-for-all-resource-types"></a>Czy Resource Health jest dostępny dla wszystkich typów zasobów?
Listę kontroli kondycji i typów zasobów obsługiwanych za pomocą Resource Health można znaleźć [tutaj](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>Co należy zrobić, jeśli zasób jest wyświetlany, ale uważasz, że nie jest? "
Sprawdzając kondycję zasobu, kliknij prawym przyciskiem myszy stan kondycji, a następnie kliknij pozycję **Zgłoś nieprawidłowy stan kondycji**. Przed przesłaniem raportu można podać dodatkowe informacje na temat tego, dlaczego bieżący stan kondycji jest nieprawidłowy.

## <a name="is-resource-health-available-for-all-azure-regions"></a>Czy Resource Health jest dostępny dla wszystkich regionów świadczenia usługi Azure? 
Kondycja zasobów jest dostępna we wszystkich georegionyach platformy Azure.

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Jak różni się Resource Health od stanu platformy Azure lub pulpitu nawigacyjnego Service Health?
Informacje podane przez Resource Health są bardziej szczegółowe niż podane przez stan platformy Azure lub pulpit nawigacyjny Service Health.

[Stan platformy Azure](https://status.azure.com) i pulpit nawigacyjny Service Health informują o problemach z usługą, które mają wpływ na szeroki zestaw klientów (na przykład w regionie świadczenia usługi Azure), Resource Health uwidacznia bardziej szczegółowe zdarzenia, które są istotne tylko dla konkretnego zasobu. Na przykład w przypadku nieoczekiwanego ponownego uruchomienia hosta program Resource Health ostrzega tylko klientów, których maszyna wirtualna była uruchomiona na tym hoście.

Należy pamiętać, że w celu zapewnienia pełnego wglądu w zdarzenia mające wpływ na zasoby, Resource Health również przedstawia zdarzenia opublikowane na pulpicie nawigacyjnym Service Health.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>Czy muszę aktywować Resource Health dla każdego zasobu?
Nie. Informacje o kondycji są dostępne dla wszystkich typów zasobów dostępnych za poorednictwem Resource Health. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Czy konieczne jest włączenie Resource Health dla mojej organizacji?
Nie.  Azure Resource Health jest dostępny w ramach Azure Portal bez żadnych wymagań dotyczących instalacji.

## <a name="is-resource-health-available-free-of-charge"></a>Jest Resource Health dostępne bezpłatnie?
Tak.  Azure Resource Health jest bezpłatny.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Jakie są zalecenia, które Resource Health zapewnia?
Na podstawie stanu kondycji Resource Health zapewnia rekomendacje w celu skrócenia czasu poświęcanego na rozwiązywanie problemów. W przypadku dostępnych zasobów zaleceń koncentruje się na sposobach rozwiązywania najczęstszych problemów napotykanych przez klientów. Jeśli zasób jest niedostępny z powodu niezaplanowanych zdarzeń platformy Azure, fokus będzie w trakcie procesu odzyskiwania i po nim. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o Resource Health:
-  [Przegląd Azure Resource Health](Resource-health-overview.md)
-  [Typy zasobów i kontrole kondycji dostępne w usłudze Azure Resource Health](resource-health-checks-resource-types.md)
