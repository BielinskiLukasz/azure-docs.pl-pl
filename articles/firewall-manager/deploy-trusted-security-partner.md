---
title: Wdróż dostawcę partnera zabezpieczeń Menedżera zapory platformy Azure
description: Dowiedz się, jak wdrożyć dostawcę partnera zabezpieczeń Menedżera zapory platformy Azure przy użyciu Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 3323f73c137905fbe677c68d3830d7f609fa0172
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85611581"
---
# <a name="deploy-a-security-partner-provider"></a>Wdrażanie dostawcy partnera w zakresie zabezpieczeń

*Dostawcy usług partnerskich zabezpieczeń* w Menedżerze zapory platformy Azure umożliwiają korzystanie z znanych, najlepszych w ramach organizacji ofert typu "zabezpieczenia jako usługa" (SECaaS) innych firm, aby chronić dostęp do Internetu użytkownikom.

Aby dowiedzieć się więcej o obsługiwanych scenariuszach i wskazówkach dotyczących najlepszych rozwiązań, zobacz [co to są dostawcy partnera zabezpieczeń?](trusted-security-partners.md)


Zintegrowane partnerzy usługi Security AS (SECaaS) innych firm są teraz dostępni we wszystkich regionach chmury publicznej platformy Azure. **Rozwiązania Zscaler** integracja będzie ogólnie dostępna 3 lipca 2020. Wartość **Check Point** to obsługiwany partner SECaaS i będzie on w wersji zapoznawczej 3 lipca 2020. Integracja **iboss** będzie ogólnie dostępna w dniu 31 lipca 2020.

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Wdróż dostawcę zabezpieczeń innych firm w nowym centrum

Pomiń tę sekcję, Jeśli wdrażasz dostawcę innej firmy w istniejącym centrum.

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
2. W polu **wyszukiwania**wpisz **Menedżer zapory** i wybierz go w obszarze **usługi**.
3. Przejdź do **wprowadzenie**. Wybierz pozycję **Wyświetl zabezpieczone centra wirtualne**.
4. Wybierz pozycję **Utwórz nowe zabezpieczone centrum wirtualne**.
5. Wprowadź subskrypcję i grupę zasobów, wybierz obsługiwany region i Dodaj informacje o centrum i wirtualnej sieci WAN. 
6. Wybierz pozycję **Uwzględnij bramę sieci VPN, aby włączyć dostawców partnerskich zabezpieczeń**.
7. Wybierz **jednostki skalowania bramy** odpowiednie dla Twoich wymagań.
8. Wybierz pozycję **Dalej: Zapora platformy Azure**
   > [!NOTE]
   > Dostawcy usługi Security partner nawiązują połączenie z centrum przy użyciu tuneli VPN Gateway. Po usunięciu VPN Gateway połączenia z dostawcami partnerów zabezpieczeń zostaną utracone.
9. Jeśli chcesz wdrożyć zaporę platformy Azure, aby odfiltrować ruch prywatny razem z dostawcą usług innych firm w celu filtrowania ruchu internetowego, wybierz zasady zapory platformy Azure. Zapoznaj się z [obsługiwanymi scenariuszami](trusted-security-partners.md#key-scenarios).
10. Jeśli chcesz wdrożyć dostawcę zabezpieczeń innych firm w centrum, wybierz opcję **Zapora systemu Azure: włączone/wyłączone** , aby ustawić ustawienie na **wyłączone**. 
11. Wybierz pozycję  **Dalej: dostawca partnera zabezpieczeń**.
12. Ustaw **dostawcę partnera zabezpieczeń** na **włączony**. 
13. Wybierz partnera. 
14. Wybierz pozycję **Dalej: Przeglądanie i tworzenie**. 
15. Przejrzyj zawartość, a następnie wybierz pozycję **Utwórz**.

Wdrożenie bramy sieci VPN może potrwać ponad 30 minut.

Aby sprawdzić, czy centrum zostało utworzone, przejdź do Menedżera zapory platformy Azure — >zabezpieczone centra. Wybierz stronę omówienia >centrum, aby wyświetlić nazwę partnera i stan jako **oczekujące połączenia zabezpieczeń**.

Po utworzeniu centrum i skonfigurowaniu partnera zabezpieczeń Kontynuuj, aby podłączyć dostawcę zabezpieczeń do centrum.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Wdrażanie dostawcy zabezpieczeń innej firmy w istniejącym centrum

Możesz również wybrać istniejące centrum w wirtualnej sieci WAN i przekonwertować je na *zabezpieczone centrum wirtualne*.

1. W **wprowadzenie**wybierz pozycję **Wyświetl zabezpieczone centra wirtualne**.
2. Wybierz pozycję **Konwertuj istniejące centra**.
3. Wybierz subskrypcję i istniejące centrum. Wykonaj pozostałe kroki, aby wdrożyć dostawcę innych firm w nowym centrum.

Należy pamiętać, że należy wdrożyć bramę sieci VPN, aby przekonwertować istniejące centrum na zabezpieczone centrum z dostawcami innych firm.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Konfigurowanie dostawców zabezpieczeń innych firm do łączenia się z zabezpieczonym centrum

Aby skonfigurować tunele do VPN Gateway koncentratora wirtualnego, dostawcy innych firm muszą mieć prawa dostępu do centrum. W tym celu Skojarz jednostkę usługi z subskrypcją lub grupą zasobów i Udziel praw dostępu. Następnie należy podać te poświadczenia innym firmom przy użyciu portalu.

### <a name="create-and-authorize-a-service-principal"></a>Tworzenie i Autoryzowanie jednostki usługi

1. Utwórz nazwę główną usługi Azure Active Directory (AD): możesz pominąć adres URL przekierowania. 

   [Instrukcje: używanie portalu do tworzenia aplikacji usługi Azure AD i jednostki usługi w celu uzyskiwania dostępu do zasobów](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. Dodawanie praw dostępu i zakresu dla jednostki usługi.
   [Instrukcje: używanie portalu do tworzenia aplikacji usługi Azure AD i jednostki usługi w celu uzyskiwania dostępu do zasobów](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)

   > [!NOTE]
   > Możesz ograniczyć dostęp tylko do grupy zasobów, aby uzyskać bardziej szczegółową kontrolę.

### <a name="visit-partner-portal"></a>Odwiedź portal dla partnerów

1. Postępuj zgodnie z instrukcjami dostarczonymi przez partnera, aby zakończyć instalację. Obejmuje to przesyłanie informacji usługi AAD w celu wykrywania i łączenia się z centrum, aktualizowania zasad ruchu wychodzącego oraz sprawdzania stanu i dzienników łączności.

   - [Rozwiązania Zscaler: Skonfiguruj integrację wirtualnej sieci WAN Microsoft Azure](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration).
   - [Check Point (wersja zapoznawcza): konfigurowanie Microsoft Azure integracji wirtualnej sieci WAN](https://sc1.checkpoint.com/documents/Infinity_Portal/WebAdminGuides/EN/CloudGuard-Connect-Azure-Virtual-WAN/Default.htm).
   - [iboss (wersja zapoznawcza): Konfigurowanie integracji wirtualnej sieci WAN Microsoft Azure](https://www.iboss.com/blog/securing-microsoft-azure-with-iboss-saas-network-security). 
   
2. Stan tworzenia tunelu można sprawdzić w portalu Azure Virtual WAN na platformie Azure. Po pobraniu przez tunele **połączenia** na platformie Azure i w portalu dla partnerów przejdź do następnych kroków, aby skonfigurować trasy do wybierania gałęzi i sieci wirtualnych powinny wysyłać ruch internetowy do partnera.

## <a name="configure-route-settings"></a>Konfigurowanie ustawień trasy

1. Przejdź do Menedżera zapory platformy Azure — > zabezpieczone centra. 
2. Wybierz centrum. Stan centrum powinien teraz wskazywać, że **zainicjowano Inicjowanie obsługi administracyjnej** zamiast **połączenia zabezpieczeń**.

   Upewnij się, że dostawca innej firmy może połączyć się z centrum. Tunele w bramie sieci VPN powinny znajdować się w stanie **połączonym** . Ten stan zwiększa odzwierciedlenie kondycji połączenia między centrum a partnerem innej firmy w porównaniu z poprzednim stanem.
3. Wybierz centrum i przejdź do **ustawień trasy**.

   Podczas wdrażania dostawcy innej firmy w centrum program konwertuje koncentrator do *zabezpieczonego koncentratora wirtualnego*. Dzięki temu dostawca innych firm anonsuje trasę 0.0.0.0/0 (domyślnie) do centrum. Jednak połączenia sieci wirtualnej i lokacje połączone z centrum nie otrzymują tej trasy, chyba że użytkownik zdecyduje się na to, które połączenia powinny uzyskać tę trasę domyślną.
4. W obszarze **ruch internetowy**wybierz opcję połączenie między sieciami **wirtualnymi** lub **rozgałęzienia z Internetem** albo skonfiguruj trasy wysyłane przez inną firmę.

   Wskazuje to, który typ ruchu powinien być kierowany do centrum, ale nie ma to wpływu na trasy w sieci wirtualnych lub gałęziach. Trasy te nie są propagowane do wszystkich sieci wirtualnychów/gałęzi, które są domyślnie dołączone do centrum.
5. Musisz wybrać opcję **bezpieczne połączenia** i wybrać połączenia, dla których mają zostać ustawione te trasy. Wskazuje, które sieci wirtualnych/gałęzie mogą rozpocząć wysyłanie ruchu internetowego do dostawcy innej firmy.
6. W obszarze **Ustawienia trasy**wybierz pozycję **bezpieczne połączenia** w obszarze ruch internetowy, a następnie wybierz sieć wirtualną lub gałęzie (*Lokacje* w wirtualnej sieci WAN), które mają być zabezpieczone. Wybierz pozycję **bezpieczny ruch internetowy**.
   ![Bezpieczny ruch internetowy](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. Przejdź z powrotem do strony centrów. Stan **dostawcy partnera zabezpieczeń** centrum powinien być teraz  **zabezpieczony**.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Gałąź lub ruch internetowy w sieci wirtualnej za pośrednictwem usługi innej firmy

Następnie można sprawdzić, czy maszyny wirtualne sieci wirtualnej lub lokacja oddziału mogą uzyskać dostęp do Internetu, i sprawdzić, czy ruch przepływów do usługi innej firmy.

Po zakończeniu kroków ustawień trasy maszyny wirtualne sieci wirtualnej oraz Lokacje oddziałów są wysyłane 0/0 do trasy usługi innej firmy. Nie można nawiązać połączenia RDP lub SSH z tymi maszynami wirtualnymi. Aby się zalogować, można wdrożyć usługę [Azure bastionu](../bastion/bastion-overview.md) w równorzędnej sieci wirtualnej.

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Zabezpieczanie sieci w chmurze za pomocą Menedżera zapory platformy Azure przy użyciu Azure Portal](secure-cloud-network.md)