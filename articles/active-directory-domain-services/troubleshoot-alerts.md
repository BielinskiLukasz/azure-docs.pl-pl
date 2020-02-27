---
title: Typowe alerty i rozwiązania w Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak rozwiązywać typowe alerty wygenerowane w ramach stanu kondycji Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: c83caf31e25ae2212ed120e77e017ac3849898e8
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77612914"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>Znane problemy: typowe alerty i rozwiązania w Azure Active Directory Domain Services

Jako centralna część tożsamości i uwierzytelniania dla aplikacji Azure Active Directory Domain Services (AD DS platformy Azure) czasami występują problemy. Jeśli wystąpią problemy, istnieją pewne typowe alerty i powiązane kroki rozwiązywania problemów, które ułatwiają ponowne uruchomienie. W dowolnym momencie możesz również [otworzyć żądanie pomocy technicznej platformy Azure][azure-support] , aby uzyskać dodatkową pomoc dotyczącą rozwiązywania problemów.

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów dotyczących typowych alertów w usłudze Azure AD DS.

## <a name="aadds100-missing-directory"></a>AADDS100: brak katalogu

### <a name="alert-message"></a>Komunikat alertu

*Katalog usługi Azure AD skojarzony z domeną zarządzaną mógł zostać usunięty. Domena zarządzana nie jest już w obsługiwanej konfiguracji. Firma Microsoft nie może monitorować, poprawiać ani synchronizować domeny zarządzanej.*

### <a name="resolution"></a>Rozwiązanie

Ten błąd jest zwykle spowodowany przeniesieniem subskrypcji platformy Azure do nowego katalogu usługi Azure AD, a stary katalog usługi Azure AD skojarzony z usługą Azure AD DS zostanie usunięty.

Ten błąd jest nieodwracalny. Aby rozwiązać ten alert, [Usuń istniejącą domenę zarządzaną platformy Azure AD DS](delete-aadds.md) i utwórz ją ponownie w nowym katalogu. Jeśli masz problem z usunięciem domeny zarządzanej AD DS platformy Azure, [Otwórz żądanie pomocy technicznej platformy Azure][azure-support] , aby uzyskać dodatkową pomoc dotyczącą rozwiązywania problemów.

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C jest uruchomiony w tym katalogu

### <a name="alert-message"></a>Komunikat alertu

*Nie można włączyć Azure AD Domain Services w katalogu Azure AD B2C.*

### <a name="resolution"></a>Rozwiązanie

Usługa Azure AD DS automatycznie synchronizuje się z katalogiem usługi Azure AD. Jeśli katalog usługi Azure AD jest skonfigurowany dla usługi B2C, nie można wdrożyć i zsynchronizować AD DS platformy Azure.

Aby korzystać z usługi Azure AD DS, należy ponownie utworzyć domenę zarządzaną w katalogu innym niż Azure AD B2C, wykonując następujące czynności:

1. [Usuń domenę zarządzaną AD DS platformy Azure](delete-aadds.md) z istniejącego katalogu usługi Azure AD.
1. Utwórz nowy katalog usługi Azure AD, który nie jest katalogiem Azure AD B2C.
1. [Utwórz zastępczą domenę zarządzaną platformy Azure AD DS](tutorial-create-instance.md).

Kondycja domeny zarządzanej na platformie Azure AD DS automatycznie aktualizuje się w ciągu dwóch godzin i usuwa alert.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: adres należy do publicznego zakresu adresów IP

### <a name="alert-message"></a>Komunikat alertu

*Zakres adresów IP dla sieci wirtualnej, w której włączono Azure AD Domain Services należy do publicznego zakresu adresów IP. Azure AD Domain Services musi być włączona w sieci wirtualnej z zakresem prywatnych adresów IP. Ta konfiguracja ma wpływ na zdolność firmy Microsoft do monitorowania, poprawiania i synchronizowania domeny zarządzanej oraz zarządzania nią.*

### <a name="resolution"></a>Rozwiązanie

Przed rozpoczęciem upewnij się, że znasz [prywatne przestrzenie adresowe IP w wersji 4](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

W sieci wirtualnej maszyny wirtualne mogą wykonywać żądania do zasobów platformy Azure w ramach tego samego zakresu adresów IP, co jest skonfigurowane dla podsieci. W przypadku skonfigurowania zakresu publicznego adresu IP dla podsieci żądania kierowane w ramach sieci wirtualnej mogą nie dotrzeć do zamierzonych zasobów sieci Web. Ta konfiguracja może prowadzić do nieprzewidywalnych błędów w usłudze Azure AD DS.

> [!NOTE]
> Jeśli masz własny zakres adresów IP w Internecie skonfigurowanym w sieci wirtualnej, ten alert może zostać zignorowany. Jednak w przypadku tej konfiguracji Azure AD Domain Services nie można zatwierdzić [umowy SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)], ponieważ może to prowadzić do nieprzewidywalnego błędu.

Aby rozwiązać ten alert, Usuń istniejącą domenę zarządzaną platformy Azure AD DS i utwórz ją ponownie w sieci wirtualnej z zakresem prywatnych adresów IP. Ten proces jest zakłócany, ponieważ domena zarządzana AD DS platformy Azure jest niedostępna, a wszystkie zasoby niestandardowe, które zostały utworzone, takie jak jednostki organizacyjne lub konta usług, są tracone.

1. [Usuń domenę zarządzaną AD DS platformy Azure](delete-aadds.md) z katalogu.
1. Aby zaktualizować zakres adresów IP sieci wirtualnej, Wyszukaj i wybierz pozycję *Sieć wirtualna* w Azure Portal. Wybierz sieć wirtualną dla usługi Azure AD DS, która ma nieprawidłowo ustawiony zakres publicznych adresów IP.
1. W obszarze **Ustawienia**wybierz pozycję *przestrzeń adresowa*.
1. Zaktualizuj zakres adresów, wybierając istniejący zakres adresów i edytując go lub dodając dodatkowy zakres adresów. Upewnij się, że nowy zakres adresów IP należy do zakresu prywatnego adresów IP. Gdy wszystko będzie gotowe, **Zapisz** zmiany.
1. W lewym okienku nawigacji wybierz pozycję **podsieci** .
1. Wybierz podsieć, którą chcesz edytować, lub Utwórz dodatkową podsieć.
1. Zaktualizuj lub określ prywatny zakres adresów IP, a następnie **Zapisz** zmiany.
1. [Utwórz zastępczą domenę zarządzaną platformy Azure AD DS](tutorial-create-instance.md). Upewnij się, że wybrano zaktualizowaną podsieć sieci wirtualnej z zakresem prywatnych adresów IP.

Kondycja domeny zarządzanej na platformie Azure AD DS automatycznie aktualizuje się w ciągu dwóch godzin i usuwa alert.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: nie znaleziono subskrypcji platformy Azure

### <a name="alert-message"></a>Komunikat alertu

*Twoja subskrypcja platformy Azure skojarzona z domeną zarządzaną została usunięta.  Azure AD Domain Services wymaga aktywnej subskrypcji, aby nadal działać prawidłowo.*

### <a name="resolution"></a>Rozwiązanie

Usługa Azure AD DS wymaga aktywnej subskrypcji i nie można jej przenieść do innej subskrypcji. Jeśli subskrypcja platformy Azure, z którą skojarzona jest domena zarządzana przez usługę Azure AD DS, zostanie usunięta, należy ponownie utworzyć subskrypcję platformy Azure i domenę zarządzaną platformy Azure AD DS.

1. [Utwórz subskrypcję platformy Azure](../cost-management-billing/manage/create-subscription.md).
1. [Usuń domenę zarządzaną AD DS platformy Azure](delete-aadds.md) z istniejącego katalogu usługi Azure AD.
1. [Utwórz zastępczą domenę zarządzaną platformy Azure AD DS](tutorial-create-instance.md).

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: subskrypcja platformy Azure jest wyłączona

### <a name="alert-message"></a>Komunikat alertu

*Twoja subskrypcja platformy Azure skojarzona z Twoją domeną zarządzaną nie jest aktywna.  Azure AD Domain Services wymaga aktywnej subskrypcji, aby nadal działać prawidłowo.*

### <a name="resolution"></a>Rozwiązanie

Usługa Azure AD DS wymaga aktywnej subskrypcji. Jeśli subskrypcja platformy Azure, z którą skojarzona jest domena zarządzana przez platformę Azure AD DS, nie jest aktywna, należy ją odnowić, aby ponownie aktywować subskrypcję.

1. [Odnów subskrypcję platformy Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Po odnowieniu subskrypcji powiadomienie AD DS platformy Azure umożliwi ponowne włączenie domeny zarządzanej.

Gdy domena zarządzana zostanie ponownie włączona, kondycja domeny zarządzanej przez platformę Azure AD DS automatycznie aktualizuje się w ciągu dwóch godzin i usuwa alert.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: przeniesiono katalogi subskrypcji

### <a name="alert-message"></a>Komunikat alertu

*Subskrypcja używana przez Azure AD Domain Services została przeniesiona do innego katalogu. Azure AD Domain Services musi mieć aktywną subskrypcję w tym samym katalogu, aby działała prawidłowo.*

### <a name="resolution"></a>Rozwiązanie

Usługa Azure AD DS wymaga aktywnej subskrypcji i nie można jej przenieść do innej subskrypcji. Jeśli subskrypcja platformy Azure, z którą skojarzona jest domena zarządzana przez platformę Azure AD DS, została przeniesiona, przenieś ją z powrotem do poprzedniego katalogu lub [Usuń domenę zarządzaną](delete-aadds.md) z istniejącego katalogu i [Utwórz zastępczą domenę zarządzaną platformy Azure AD DS w wybranej subskrypcji](tutorial-create-instance.md).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: nie można odnaleźć zasobów dla domeny zarządzanej

### <a name="alert-message"></a>Komunikat alertu

*Zasób, który jest używany przez domenę zarządzaną, został usunięty. Ten zasób jest wymagany do poprawnego działania Azure AD Domain Services.*

### <a name="resolution"></a>Rozwiązanie

Usługa Azure AD DS tworzy dodatkowe zasoby do poprawnego działania, takie jak publiczne adresy IP, wirtualne interfejsy sieciowe i moduł równoważenia obciążenia. Jeśli którykolwiek z tych zasobów zostanie usunięty, domena zarządzana jest w nieobsługiwanym stanie i uniemożliwia zarządzanie domeną. Aby uzyskać więcej informacji na temat tych zasobów, zobacz [zasoby sieciowe używane przez usługę Azure AD DS](network-considerations.md#network-resources-used-by-azure-ad-ds).

Ten alert jest generowany, gdy jeden z tych wymaganych zasobów zostanie usunięty. Jeśli zasób został usunięty mniej niż 4 godziny temu, istnieje możliwość, że platforma Azure może automatycznie odtworzyć usunięty zasób. Poniższe kroki przedstawiają sposób sprawdzania stanu kondycji i sygnatury czasowej usunięcia zasobu:

1. W Azure Portal Wyszukaj i wybierz pozycję **usługi domenowe**. Wybierz domenę zarządzaną platformy Azure AD DS, na przykład *aaddscontoso.com*.
1. W okienku nawigacji po lewej stronie wybierz pozycję **kondycja**.
1. Na stronie kondycja wybierz alert z IDENTYFIKATORem *AADDS109*.
1. Alert ma sygnaturę czasową dla momentu jego pierwszego znalezienia. Jeśli sygnatura czasowa jest krótsza niż 4 godziny temu, platforma Azure może być w stanie automatycznie ponownie utworzyć zasób i rozwiązać alert przez siebie.

    Jeśli alert jest wcześniejszy niż 4 godziny, domena zarządzana AD DS platformy Azure jest w stanie nieodwracalnym. [Usuń domenę zarządzaną AD DS platformy Azure](delete-aadds.md) , a następnie [Utwórz zastępczą domenę zarządzaną](tutorial-create-instance.md).

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: podsieć skojarzona z domeną zarządzaną jest pełna

### <a name="alert-message"></a>Komunikat alertu

*Podsieć wybrana do wdrożenia Azure AD Domain Services jest pełna i nie ma miejsca na dodatkowy kontroler domeny, który należy utworzyć.*

### <a name="resolution"></a>Rozwiązanie

Podsieć sieci wirtualnej dla usługi Azure AD DS wymaga odpowiednich adresów IP dla automatycznie utworzonych zasobów. Ta przestrzeń adresów IP obejmuje konieczność tworzenia zasobów zastępczych w przypadku wystąpienia zdarzenia konserwacji. Aby zminimalizować ryzyko braku dostępnych adresów IP, nie Wdrażaj dodatkowych zasobów, takich jak własne maszyny wirtualne, w tej samej podsieci sieci wirtualnej co usługa Azure AD DS.

Ten błąd jest nieodwracalny. Aby rozwiązać ten alert, [Usuń istniejącą domenę zarządzaną platformy Azure AD DS](delete-aadds.md) i utwórz ją ponownie. Jeśli masz problem z usunięciem domeny zarządzanej AD DS platformy Azure, [Otwórz żądanie pomocy technicznej platformy Azure][azure-support] , aby uzyskać dodatkową pomoc dotyczącą rozwiązywania problemów.

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Nazwa główna usługi nie jest autoryzowana

### <a name="alert-message"></a>Komunikat alertu

*Jednostka usługi, której Azure AD Domain Services używa do obsługi domeny, nie ma autoryzacji do zarządzania zasobami w ramach subskrypcji platformy Azure. Jednostka usługi musi uzyskać uprawnienia do obsługi domeny zarządzanej.*

### <a name="resolution"></a>Rozwiązanie

Niektóre automatycznie generowane jednostki usługi są używane do zarządzania i tworzenia zasobów dla domeny zarządzanej AD DS platformy Azure. Jeśli uprawnienia dostępu dla jednej z tych jednostek usługi zostaną zmienione, domena nie będzie mogła prawidłowo zarządzać zasobami. Poniższe kroki pokazują, jak zrozumieć, a następnie udzielić uprawnień dostępu do jednostki usługi:

1. Przeczytaj o [kontroli dostępu opartej na rolach i sposobach udzielania dostępu do aplikacji w Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
2. Przejrzyj dostęp, który jednostka usługi o IDENTYFIKATORze *abba844e-bc0e-44b0-947a-dc74e5d09022* , i Udziel dostępu, który został odrzucony w wcześniejszym terminie.

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: brak wystarczającej liczby adresów IP w domenie zarządzanej

### <a name="alert-message"></a>Komunikat alertu

*Zidentyfikowano, że podsieć sieci wirtualnej w tej domenie może nie mieć wystarczającej liczby adresów IP. Azure AD Domain Services potrzebuje co najmniej dwóch dostępnych adresów IP w podsieci, w której jest włączona. Zalecamy używanie co najmniej 3-5 zapasowych adresów IP w podsieci. Mogło to nastąpić, jeśli inne maszyny wirtualne zostaną wdrożone w podsieci, co spowoduje wyczerpanie liczby dostępnych adresów IP lub ograniczenie liczby dostępnych adresów IP w podsieci.*

### <a name="resolution"></a>Rozwiązanie

Podsieć sieci wirtualnej dla usługi Azure AD DS wymaga wystarczającej liczby adresów IP dla automatycznie utworzonych zasobów. Ta przestrzeń adresów IP obejmuje konieczność tworzenia zasobów zastępczych w przypadku wystąpienia zdarzenia konserwacji. Aby zminimalizować ryzyko braku dostępnych adresów IP, nie Wdrażaj dodatkowych zasobów, takich jak własne maszyny wirtualne, w tej samej podsieci sieci wirtualnej co usługa Azure AD DS.

Aby rozwiązać ten alert, Usuń istniejącą domenę zarządzaną platformy Azure AD DS i utwórz ją ponownie w sieci wirtualnej z dużym zakresem adresów IP. Ten proces jest zakłócany, ponieważ domena zarządzana AD DS platformy Azure jest niedostępna, a wszystkie zasoby niestandardowe, które zostały utworzone, takie jak jednostki organizacyjne lub konta usług, są tracone.

1. [Usuń domenę zarządzaną AD DS platformy Azure](delete-aadds.md) z katalogu.
1. Aby zaktualizować zakres adresów IP sieci wirtualnej, Wyszukaj i wybierz pozycję *Sieć wirtualna* w Azure Portal. Wybierz sieć wirtualną dla AD DS platformy Azure, która ma mały zakres adresów IP.
1. W obszarze **Ustawienia**wybierz pozycję *przestrzeń adresowa*.
1. Zaktualizuj zakres adresów, wybierając istniejący zakres adresów i edytując go lub dodając dodatkowy zakres adresów. Upewnij się, że nowy zakres adresów IP jest wystarczająco duży dla zakresu podsieci AD DS platformy Azure. Gdy wszystko będzie gotowe, **Zapisz** zmiany.
1. W lewym okienku nawigacji wybierz pozycję **podsieci** .
1. Wybierz podsieć, którą chcesz edytować, lub Utwórz dodatkową podsieć.
1. Zaktualizuj lub określ wystarczająco duży zakres adresów IP, a następnie **Zapisz** zmiany.
1. [Utwórz zastępczą domenę zarządzaną platformy Azure AD DS](tutorial-create-instance.md). Upewnij się, że wybrano zaktualizowaną podsieć sieci wirtualnej z dużym zakresem adresów IP.

Kondycja domeny zarządzanej na platformie Azure AD DS automatycznie aktualizuje się w ciągu dwóch godzin i usuwa alert.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: zasoby są nieodwracalne

### <a name="alert-message"></a>Komunikat alertu

*Zasoby używane przez Azure AD Domain Services zostały wykryte w nieoczekiwanym stanie i nie można ich odzyskać.*

### <a name="resolution"></a>Rozwiązanie

Ten błąd jest nieodwracalny. Aby rozwiązać ten alert, [Usuń istniejącą domenę zarządzaną platformy Azure AD DS](delete-aadds.md) i utwórz ją ponownie. Jeśli masz problem z usunięciem domeny zarządzanej AD DS platformy Azure, [Otwórz żądanie pomocy technicznej platformy Azure][azure-support] , aby uzyskać dodatkową pomoc dotyczącą rozwiązywania problemów.

## <a name="aadds114-subnet-invalid"></a>AADDS114: nieprawidłowa podsieć

### <a name="alert-message"></a>Komunikat alertu

*Podsieć wybrana do wdrożenia Azure AD Domain Services jest nieprawidłowa i nie można jej użyć.*

### <a name="resolution"></a>Rozwiązanie

Ten błąd jest nieodwracalny. Aby rozwiązać ten alert, [Usuń istniejącą domenę zarządzaną platformy Azure AD DS](delete-aadds.md) i utwórz ją ponownie. Jeśli masz problem z usunięciem domeny zarządzanej AD DS platformy Azure, [Otwórz żądanie pomocy technicznej platformy Azure][azure-support] , aby uzyskać dodatkową pomoc dotyczącą rozwiązywania problemów.

## <a name="aadds115-resources-are-locked"></a>AADDS115: zasoby są zablokowane

### <a name="alert-message"></a>Komunikat alertu

*Co najmniej jeden z zasobów sieciowych używany przez domenę zarządzaną nie może być obsługiwany w przypadku, gdy zakres docelowy został zablokowany.*

### <a name="resolution"></a>Rozwiązanie

Blokad zasobów można zastosować do zasobów platformy Azure, aby zapobiec zmianie lub usunięciu. Ponieważ usługa Azure AD DS jest usługą zarządzaną, platforma Azure musi mieć możliwość wprowadzania zmian w konfiguracji. Jeśli na niektórych składnikach AD DS platformy Azure zostanie zastosowana blokada zasobów, platforma Azure nie będzie mogła wykonywać zadań zarządzania.

Aby sprawdzić blokady zasobów w składnikach AD DS platformy Azure i usunąć je, wykonaj następujące czynności:

1. W przypadku wszystkich składników sieci AD DS platformy Azure w grupie zasobów, takich jak sieć wirtualna, interfejs sieciowy lub publiczny adres IP, Sprawdź dzienniki operacji w Azure Portal. Te dzienniki operacji powinny wskazywać, dlaczego operacja kończy się niepowodzeniem i kiedy jest stosowana blokada zasobu.
1. Wybierz zasób, w którym zastosowana jest blokada, a następnie w obszarze **blokady**wybierz i Usuń blokady.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: zasoby są bezużyteczne

### <a name="alert-message"></a>Komunikat alertu

*Nie można obsługiwać co najmniej jednego z zasobów sieciowych używanych przez domenę zarządzaną z powodu ograniczeń zasad.*

### <a name="resolution"></a>Rozwiązanie

Zasady są stosowane do zasobów platformy Azure i grup zasobów, które kontrolują, jakie akcje konfiguracyjne są dozwolone. Ponieważ usługa Azure AD DS jest usługą zarządzaną, platforma Azure musi mieć możliwość wprowadzania zmian w konfiguracji. Jeśli zasady są stosowane w przypadku niektórych składników AD DS platformy Azure, platforma Azure może nie być w stanie wykonywać zadań zarządzania.

Aby sprawdzić zastosowane zasady dla składników usługi Azure AD DS i zaktualizować je, wykonaj następujące czynności:

1. W przypadku wszystkich składników sieci AD DS platformy Azure w grupie zasobów, takich jak sieć wirtualna, karta sieciowa lub publiczny adres IP, Sprawdź dzienniki operacji w Azure Portal. Te dzienniki operacji powinny wskazywać, dlaczego operacja kończy się niepowodzeniem i gdzie stosowane są restrykcyjne zasady.
1. Wybierz zasób, do którego zastosowano zasady, a następnie w obszarze **zasady**wybierz i Edytuj zasady, aby było mniej restrykcyjne.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: synchronizacja nie została ukończona w czasie

### <a name="alert-message"></a>Komunikat alertu

*Domena zarządzana została Ostatnia synchronizacja z usługą Azure AD w dniu [Date]. Użytkownicy mogą nie być w stanie zalogować się w domenie zarządzanej lub członkostwa w grupach mogą nie być zsynchronizowane z usługą Azure AD.*

### <a name="resolution"></a>Rozwiązanie

[Sprawdź kondycję usługi Azure AD DS](check-health.md) pod kątem alertów wskazujących na problemy z konfiguracją domeny zarządzanej. Problemy z konfiguracją sieci mogą blokować synchronizację z usługi Azure AD. Jeśli możesz rozwiązać alerty wskazujące problem z konfiguracją, zaczekaj dwie godziny i sprawdź, czy synchronizacja została pomyślnie ukończona.

Następujące typowe przyczyny powodują zatrzymanie synchronizacji w domenach zarządzanych AD DS platformy Azure:

* Wymagana łączność sieciowa jest zablokowana. Aby dowiedzieć się więcej o tym, jak sprawdzić, czy usługa Azure Virtual Network rozwiązuje problemy i jakie są wymagane, zobacz [Rozwiązywanie problemów z sieciowymi grupami zabezpieczeń](alert-nsg.md) i [wymagania sieciowe dotyczące Azure AD Domain Services](network-considerations.md).
*  Synchronizacja haseł nie została skonfigurowana lub zakończyła się pomyślnie podczas wdrażania domeny zarządzanej AD DS platformy Azure. Synchronizację haseł można skonfigurować dla [użytkowników tylko w chmurze](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) lub dla [użytkowników hybrydowych z Premium](tutorial-configure-password-hash-sync.md).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: nie wykonano kopii zapasowej w czasie

### <a name="alert-message"></a>Komunikat alertu

*Utworzono ostatnią kopię zapasową domeny zarządzanej w dniu [Date].*

### <a name="resolution"></a>Rozwiązanie

[Sprawdź kondycję usługi Azure AD DS](check-health.md) pod kątem alertów wskazujących na problemy z konfiguracją domeny zarządzanej. Problemy z konfiguracją sieci mogą blokować pomyślne tworzenie kopii zapasowych przez platformę Azure. Jeśli możesz rozwiązać alerty wskazujące problem z konfiguracją, zaczekaj dwie godziny i sprawdź, czy synchronizacja została pomyślnie ukończona.

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: zawieszenie z powodu wyłączenia subskrypcji

### <a name="alert-message"></a>Komunikat alertu

*Domena zarządzana jest wstrzymana, ponieważ subskrypcja platformy Azure skojarzona z domeną nie jest aktywna.*

### <a name="resolution"></a>Rozwiązanie

> [!WARNING]
> Jeśli domena zarządzana AD DS platformy Azure jest zawieszona przez dłuższy czas, wystąpi niebezpieczeństwo jego usunięcia. Rozwiąż powód zawieszenia tak szybko, jak to możliwe. Aby uzyskać więcej informacji, zobacz temat [Omówienie zawieszonych Stanów usługi Azure AD DS](suspension.md).

Usługa Azure AD DS wymaga aktywnej subskrypcji. Jeśli subskrypcja platformy Azure, z którą skojarzona jest domena zarządzana przez platformę Azure AD DS, nie jest aktywna, należy ją odnowić, aby ponownie aktywować subskrypcję.

1. [Odnów subskrypcję platformy Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Po odnowieniu subskrypcji powiadomienie AD DS platformy Azure umożliwi ponowne włączenie domeny zarządzanej.

Gdy domena zarządzana zostanie ponownie włączona, kondycja domeny zarządzanej przez platformę Azure AD DS automatycznie aktualizuje się w ciągu dwóch godzin i usuwa alert.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: zawieszenie z powodu nieprawidłowej konfiguracji

### <a name="alert-message"></a>Komunikat alertu

*Domena zarządzana jest zawieszona z powodu nieprawidłowej konfiguracji. Usługa nie była w stanie zarządzać, poprawiać ani aktualizować kontrolerów domeny dla domeny zarządzanej przez dłuższy czas.*

### <a name="resolution"></a>Rozwiązanie

> [!WARNING]
> Jeśli domena zarządzana AD DS platformy Azure jest zawieszona przez dłuższy czas, wystąpi niebezpieczeństwo jego usunięcia. Rozwiąż powód zawieszenia tak szybko, jak to możliwe. Aby uzyskać więcej informacji, zobacz temat [Omówienie zawieszonych Stanów usługi Azure AD DS](suspension.md).

[Sprawdź kondycję usługi Azure AD DS](check-health.md) pod kątem alertów wskazujących na problemy z konfiguracją domeny zarządzanej. Jeśli możesz rozwiązać alerty wskazujące problem z konfiguracją, zaczekaj dwie godziny i sprawdź, czy synchronizacja została ukończona. Gdy wszystko będzie gotowe, [Otwórz żądanie pomocy technicznej platformy Azure][azure-support] , aby ponownie włączyć domenę zarządzaną platformy Azure AD DS.

## <a name="next-steps"></a>Następne kroki

Jeśli nadal masz problemy, [Otwórz żądanie pomocy technicznej platformy Azure][azure-support] , aby uzyskać dodatkową pomoc dotyczącą rozwiązywania problemów.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
