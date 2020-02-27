---
title: Migrowanie Azure AD Domain Services z klasycznej sieci wirtualnej | Microsoft Docs
description: Dowiedz się, jak migrować istniejące Azure AD Domain Services wystąpienie domeny zarządzanej z modelu klasycznej sieci wirtualnej do sieci wirtualnej opartej na Menedżer zasobów.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: 114a460b3db67af278f813de2e7a18d571cf3c28
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77613441"
---
# <a name="migrate-azure-ad-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>Przeprowadź migrację Azure AD Domain Services z modelu klasycznej sieci wirtualnej do Menedżer zasobów

Azure Active Directory Domain Services (AD DS) obsługuje jednorazowe przeniesienie dla klientów korzystających obecnie z klasycznego modelu sieci wirtualnej do modelu sieci wirtualnej Menedżer zasobów. Domeny zarządzane AD DS platformy Azure korzystające z modelu wdrażania Menedżer zasobów oferują dodatkowe funkcje, takie jak szczegółowe zasady haseł, dzienniki inspekcji i ochrona blokady konta.

W tym artykule przedstawiono zalety i kwestie związane z migracją, a następnie wymagane kroki w celu pomyślnego przeprowadzenia migracji istniejącego wystąpienia usługi Azure AD DS.

## <a name="overview-of-the-migration-process"></a>Omówienie procesu migracji

Proces migracji obejmuje istniejące wystąpienie usługi Azure AD DS, które działa w klasycznej sieci wirtualnej i przenosi je do istniejącej Menedżer zasobów sieci wirtualnej. Migracja odbywa się przy użyciu programu PowerShell i ma dwa główne etapy wykonywania — *przygotowanie* i *migrację*.

![Przegląd procesu migracji AD DS platformy Azure](media/migrate-from-classic-vnet/migration-overview.png)

Na etapie *przygotowania* usługa Azure AD DS wykonuje kopię zapasową domeny w celu uzyskania najnowszej migawki użytkowników, grup i haseł synchronizowanych z domeną zarządzaną. Synchronizacja jest następnie wyłączona, a usługa w chmurze, która obsługuje domenę zarządzaną platformy Azure AD DS, zostanie usunięta. Na etapie przygotowywania domena zarządzana przez usługę Azure AD DS nie może uwierzytelnić użytkowników.

![Etap przygotowywania migracji AD DS platformy Azure](media/migrate-from-classic-vnet/migration-preparation.png)

Na etapie *migracji* źródłowe dyski wirtualne dla kontrolerów domeny z klasycznej domeny zarządzanej platformy Azure AD DS są kopiowane w celu utworzenia maszyn wirtualnych przy użyciu Menedżer zasobów modelu wdrażania. Zostanie utworzona ponowna domena zarządzana AD DS platformy Azure, w tym LDAPs i Konfiguracja DNS. Synchronizacja z usługą Azure AD została uruchomiona ponownie, a certyfikaty LDAP są przywracane. Nie ma potrzeby ponownego przyłączania żadnych maszyn do domeny zarządzanej AD DS platformy Azure — są one nadal przyłączone do domeny zarządzanej i uruchamiane bez zmian.

![Migracja AD DS platformy Azure](media/migrate-from-classic-vnet/migration-process.png)

## <a name="migration-benefits"></a>Zalety migracji

Podczas przenoszenia domeny zarządzanej AD DS platformy Azure przy użyciu tego procesu migracji można uniknąć konieczności ponownego przyłączania maszyn do domeny zarządzanej lub usunięcia wystąpienia usługi Azure AD DS i utworzenia go od podstaw. Maszyny wirtualne nadal są przyłączone do domeny zarządzanej usługi Azure AD DS na końcu procesu migracji.

Po migracji usługa Azure AD DS udostępnia wiele funkcji, które są dostępne tylko dla domen używających Menedżer zasobów sieci wirtualnych, takich jak:

* Szczegółowe wsparcie dla zasad haseł.
* Ochrona blokady konta usługi AD.
* Powiadomienia e-mail o alertach w domenie zarządzanej AD DS platformy Azure.
* Inspekcja dzienników przy użyciu Azure Monitor.
* Integracja Azure Files
* Integracja usługi HD Insights

Domeny zarządzane AD DS platformy Azure, które używają Menedżer zasobów sieci wirtualnej, pomagają zapewnić aktualność przy użyciu najnowszych nowych funkcji. Obsługa AD DS platformy Azure przy użyciu klasycznych sieci wirtualnych jest przestarzała w przyszłości.

## <a name="example-scenarios-for-migration"></a>Przykładowe scenariusze migracji

Niektóre typowe scenariusze migracji AD DS domeny zarządzanej platformy Azure obejmują następujące przykłady.

> [!NOTE]
> Nie Konwertuj klasycznej sieci wirtualnej do momentu potwierdzenia pomyślnej migracji. Konwersja sieci wirtualnej eliminuje opcję wycofania lub przywrócenia domeny zarządzanej AD DS platformy Azure, jeśli wystąpią jakieś problemy podczas etapów migracji i weryfikacji.

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>Migrowanie AD DS platformy Azure do istniejącej Menedżer zasobów sieci wirtualnej (zalecane)

Typowy scenariusz polega na tym, że inne istniejące zasoby klasyczne zostały już przeniesione do Menedżer zasobów modelu wdrażania i sieci wirtualnej. Komunikacja równorzędna jest następnie używana z Menedżer zasobów sieci wirtualnej do klasycznej sieci wirtualnej, która kontynuuje uruchamianie usługi Azure AD DS. Takie podejście umożliwia Menedżer zasobów aplikacjom i usługom korzystanie z funkcji uwierzytelniania i zarządzania w domenie zarządzanej AD DS platformy Azure w klasycznej sieci wirtualnej. Po przeprowadzeniu migracji wszystkie zasoby są uruchamiane przy użyciu modelu wdrażania Menedżer zasobów i sieci wirtualnej.

![Migrowanie AD DS platformy Azure do istniejącej Menedżer zasobów sieci wirtualnej](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

Kroki wysokiego poziomu związane z tym przykładem scenariusza migracji obejmują następujące części:

1. Usuń istniejące bramy sieci VPN lub wirtualne sieci równorzędne skonfigurowane w klasycznej sieci wirtualnej.
1. Przeprowadź migrację domeny zarządzanej AD DS platformy Azure, wykonując kroki opisane w tym artykule.
1. Przetestuj i Potwierdź pomyślną migrację, a następnie usuń klasyczną sieć wirtualną.

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>Migrowanie wielu zasobów, w tym AD DS platformy Azure

W tym przykładowym scenariuszu migrujesz usługę Azure AD DS i inne skojarzone zasoby z klasycznego modelu wdrażania do modelu wdrażania Menedżer zasobów. Jeśli niektóre zasoby nadal działają w klasycznej sieci wirtualnej wraz z domeną zarządzaną AD DS platformy Azure, mogą oni korzystać z migracji do modelu wdrażania Menedżer zasobów.

![Migrowanie wielu zasobów do modelu wdrażania Menedżer zasobów](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

Kroki wysokiego poziomu związane z tym przykładem scenariusza migracji obejmują następujące części:

1. Usuń istniejące bramy sieci VPN lub wirtualne sieci równorzędne skonfigurowane w klasycznej sieci wirtualnej.
1. Przeprowadź migrację domeny zarządzanej AD DS platformy Azure, wykonując kroki opisane w tym artykule.
1. Skonfiguruj komunikację równorzędną sieci wirtualnej między klasyczną siecią wirtualną a siecią Menedżer zasobów.
1. Przetestuj i Potwierdź pomyślną migrację.
1. [Przenoszenie dodatkowych zasobów klasycznych, takich jak maszyny wirtualne][migrate-iaas].

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>Migrowanie AD DS platformy Azure, ale utrzymywanie innych zasobów w klasycznej sieci wirtualnej

W tym przykładowym scenariuszu masz minimalny czas przestoju w jednej sesji. Migrujesz AD DS platformy Azure do Menedżer zasobów sieci wirtualnej i utrzymujesz istniejące zasoby w klasycznym modelu wdrażania i sieci wirtualnej. W następującym okresie konserwacji można przeprowadzić migrację dodatkowych zasobów z klasycznego modelu wdrażania i sieci wirtualnej zgodnie z potrzebami.

![Migrowanie tylko AD DS platformy Azure do modelu wdrażania Menedżer zasobów](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

Kroki wysokiego poziomu związane z tym przykładem scenariusza migracji obejmują następujące części:

1. Usuń istniejące bramy sieci VPN lub wirtualne sieci równorzędne skonfigurowane w klasycznej sieci wirtualnej.
1. Przeprowadź migrację domeny zarządzanej AD DS platformy Azure, wykonując kroki opisane w tym artykule.
1. Skonfiguruj komunikację równorzędną sieci wirtualnych między klasyczną siecią wirtualną i nową Menedżer zasobów sieci wirtualnej.
1. Później należy [przeprowadzić migrację dodatkowych zasobów][migrate-iaas] z klasycznej sieci wirtualnej zgodnie z wymaganiami.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Podczas przygotowywania i migrowania domeny zarządzanej AD DS platformy Azure istnieją pewne kwestie dotyczące dostępności usług uwierzytelniania i zarządzania. Domena zarządzana AD DS platformy Azure jest niedostępna przez pewien czas podczas migracji. Aplikacje i usługi, które opierają się na usłudze Azure AD DS na czas przestoju podczas migracji.

> [!IMPORTANT]
> Przeczytaj wszystkie te artykuły i wskazówki dotyczące migracji przed rozpoczęciem procesu migracji. Proces migracji ma wpływ na dostępność kontrolerów domeny AD DS platformy Azure przez okresy czasu. Użytkownicy, usługi i aplikacje nie mogą uwierzytelniać się w domenie zarządzanej podczas procesu migracji.

### <a name="ip-addresses"></a>Adresy IP

Adresy IP kontrolera domeny dla domeny zarządzanej AD DS platformy Azure zmienią się po migracji. Ta zmiana obejmuje publiczny adres IP dla punktu końcowego bezpiecznego protokołu LDAP. Nowe adresy IP znajdują się w zakresie adresów dla nowej podsieci w sieci wirtualnej Menedżer zasobów.

W przypadku wycofania adresy IP mogą ulec zmianie po wycofaniu.

Usługa Azure AD DS zwykle używa pierwszych dwóch dostępnych adresów IP w zakresie adresów, ale nie jest to gwarantowane. Obecnie nie można określić adresów IP, które mają być używane po migracji.

### <a name="downtime"></a>Przestój

Proces migracji obejmuje kontrolery domeny w trybie offline przez pewien czas. Kontrolery domeny są niedostępne podczas migrowania AD DS platformy Azure do modelu wdrażania Menedżer zasobów i sieci wirtualnej. Średnio czas przestoju wynosi od 1 do 3 godzin. Ten okres jest od momentu przełączenia kontrolerów domeny do momentu, gdy pierwszy kontroler domeny wróci do trybu online. Ta średnia nie obejmuje czasu potrzebnego na replikację drugiego kontrolera domeny lub czasu, jaki może upłynąć do migracji dodatkowych zasobów do modelu wdrażania Menedżer zasobów.

### <a name="account-lockout"></a>Blokada konta

Na platformie Azure AD DS domeny zarządzane, które działają w klasycznych sieciach wirtualnych, nie mają zasad blokowania kont usługi AD. Jeśli maszyny wirtualne są uwidocznione w Internecie, osoby atakujące mogą wykorzystać metody rozpylania hasła, aby wymusić ich sposób na konta. Nie ma zasad blokady konta, aby zatrzymać te próby. W przypadku domen zarządzanych AD DS platformy Azure, które korzystają z modelu wdrażania Menedżer zasobów i sieci wirtualnych, zasady blokowania kont usługi AD chronią przed tymi atakami polegającymi na rozpylaniu hasła.

Domyślnie 5 nieudanych prób wprowadzenia hasła przez 2 minuty zablokowanie konta przez 30 minut.

Nie można użyć zablokowanego konta do logowania, co może zakłócać możliwość zarządzania domeną zarządzaną przez usługę Azure AD DS lub aplikacjami zarządzanymi przez konto. Po przeprowadzeniu migracji domeny zarządzanej za pomocą usługi Azure AD DS na kontach mogą wystąpić informacje, które są takie same, jak stałe blokady, z powodu powtórzonych nieudanych prób logowania. Dwa typowe scenariusze po migracji obejmują następujące elementy:

* Konto usługi korzystające z wygasłego hasła.
    * Konto usługi wielokrotnie próbuje zalogować się przy użyciu wygasłego hasła, które blokuje konto. Aby rozwiązać ten problem, Znajdź aplikację lub maszynę wirtualną z wygasłymi poświadczeniami i zaktualizuj hasło.
* Złośliwa jednostka używa prób zalogowania się do kont.
    * Gdy maszyny wirtualne są uwidocznione w Internecie, atakujący często próbują użyć wspólnych kombinacji nazwy użytkownika i hasła podczas próby podpisania. Te powtórzone nieudane próby logowania mogą blokować konta. Nie zaleca się używania kont administratorów z nazwami ogólnymi, takimi jak *administrator* czy *administrator*, na przykład w celu zminimalizowania blokowania kont administracyjnych.
    * Zminimalizuj liczbę maszyn wirtualnych, które są dostępne w Internecie. Możesz użyć [usługi Azure bastionu][azure-bastion] , aby bezpiecznie połączyć się z maszynami wirtualnymi przy użyciu Azure Portal.

Jeśli podejrzewasz, że niektóre konta mogą być zablokowane po migracji, końcowa procedura migracji w zarysie przedstawia sposób włączania inspekcji lub zmiany szczegółowych ustawień zasad haseł.

### <a name="roll-back-and-restore"></a>Wycofywanie i przywracanie

Jeśli migracja nie powiedzie się, proces wycofywania lub przywrócenia domeny zarządzanej usługi Azure AD DS. Wycofywanie to opcja samoobsługowa, która umożliwia natychmiastowe zwrócenie stanu domeny zarządzanej do przed próbą migracji. Inżynierowie pomocy technicznej systemu Azure mogą również przywrócić domenę zarządzaną z kopii zapasowej jako ostatnią. Aby uzyskać więcej informacji, zobacz [wycofywanie lub przywracanie z migracji zakończonej niepowodzeniem](#roll-back-and-restore-from-migration).

### <a name="restrictions-on-available-virtual-networks"></a>Ograniczenia dotyczące dostępnych sieci wirtualnych

Istnieją pewne ograniczenia dotyczące sieci wirtualnych, do których można migrować domenę zarządzaną platformy Azure AD DS. Docelowa sieć wirtualna Menedżer zasobów musi spełniać następujące wymagania:

* Menedżer zasobów sieci wirtualnej musi znajdować się w tej samej subskrypcji platformy Azure co klasyczna Sieć wirtualna, w której obecnie jest wdrażana usługa Azure AD DS.
* Menedżer zasobów sieci wirtualnej musi znajdować się w tym samym regionie co klasyczna Sieć wirtualna, w której usługa Azure AD DS jest obecnie wdrażana.
* Podsieć Menedżer zasobów sieci wirtualnej powinna mieć co najmniej 3-5 dostępnych adresów IP.
* Podsieć Menedżer zasobów sieci wirtualnej powinna być dedykowaną podsiecią dla usługi Azure AD DS i nie może obsługiwać żadnych innych obciążeń.

Aby uzyskać więcej informacji o wymaganiach dotyczących sieci wirtualnej, zobacz temat [zagadnienia dotyczące projektowania sieci wirtualnej i opcje konfiguracji][network-considerations].

## <a name="migration-steps"></a>Kroki migracji

Migracja do modelu wdrażania Menedżer zasobów i sieci wirtualnej jest podzielona na 5 głównych kroków:

| Krok    | Wykonywane przez  | Szacowany czas  | Przestój  | Czy wycofać/przywrócić? |
|---------|--------------------|-----------------|-----------|-------------------|
| [Krok 1 — aktualizowanie i lokalizowanie nowej sieci wirtualnej](#update-and-verify-virtual-network-settings) | Portalu Azure | 15 minut | Brak wymaganego przestoju | Nie dotyczy |
| [Krok 2. Przygotowanie domeny zarządzanej AD DS platformy Azure do migracji](#prepare-the-managed-domain-for-migration) | Program PowerShell | średnio 15 – 30 minut | Czas przestoju AD DS platformy Azure zostanie uruchomiony po zakończeniu tego polecenia. | Wycofaj i Przywróć dostępne. |
| [Krok 3. Przenoszenie domeny zarządzanej platformy Azure AD DS do istniejącej sieci wirtualnej](#migrate-the-managed-domain) | Program PowerShell | 1 – 3 godziny średnio | Po zakończeniu tego polecenia jest dostępny jeden kontroler domeny, przestoje zakończy się. | W przypadku niepowodzenia dostępne są zarówno wycofywanie (samoobsługowe), jak i przywracanie. |
| [Krok 4. testowanie i oczekiwanie na replikę kontrolera domeny](#test-and-verify-connectivity-after-the-migration)| PowerShell i Azure Portal | 1 godzina lub więcej, w zależności od liczby testów | Oba kontrolery domeny są dostępne i powinny działać normalnie. | Nie dotyczy. Po pomyślnym przeprowadzeniu migracji pierwszej maszyny wirtualnej nie jest dostępna opcja wycofywania ani przywracania. |
| [Krok 5 — opcjonalne kroki konfiguracji](#optional-post-migration-configuration-steps) | Azure Portal i maszyny wirtualne | Nie dotyczy | Brak wymaganego przestoju | Nie dotyczy |

> [!IMPORTANT]
> Aby uniknąć dodatkowego przestoju, przed rozpoczęciem procesu migracji Przeczytaj wszystkie te artykuły i wskazówki dotyczące migracji. Proces migracji ma wpływ na dostępność kontrolerów domeny AD DS platformy Azure przez pewien czas. Użytkownicy, usługi i aplikacje nie mogą uwierzytelniać się w domenie zarządzanej podczas procesu migracji.

## <a name="update-and-verify-virtual-network-settings"></a>Aktualizowanie i weryfikowanie ustawień sieci wirtualnej

Przed rozpoczęciem procesu migracji wykonaj następujące wstępne sprawdzenia i aktualizacje. Te kroki mogą wystąpić w dowolnym momencie przed migracją i nie wpływają na działanie domeny zarządzanej AD DS platformy Azure.

1. Zaktualizuj lokalne środowisko Azure PowerShell do najnowszej wersji. Aby wykonać kroki migracji, musisz mieć co najmniej wersję *2.3.2*.

    Aby uzyskać informacje na temat sprawdzania i aktualizowania wersji programu PowerShell, zobacz [Azure PowerShell Omówienie][azure-powershell].

1. Utwórz lub wybierz istniejącą Menedżer zasobów sieci wirtualnej.

    Upewnij się, że ustawienia sieci nie blokują wymaganych portów wymaganych przez usługę Azure AD DS. Porty muszą być otwarte zarówno w klasycznej sieci wirtualnej, jak i w sieci wirtualnej Menedżer zasobów. Te ustawienia obejmują tabele tras (chociaż nie zaleca się używania tabel tras) i sieciowych grup zabezpieczeń.

    Aby wyświetlić wymagane porty, zobacz [sieciowe grupy zabezpieczeń i wymagane porty][network-ports]. Aby zminimalizować problemy z komunikacją z sieci, zaleca się zaczekanie i zastosowanie sieciowej grupy zabezpieczeń lub tabeli tras do Menedżer zasobów sieci wirtualnej po pomyślnym zakończeniu migracji.

    Zanotuj tę docelową grupę zasobów, docelową sieć wirtualną i docelową podsieć sieci wirtualnej. Te nazwy zasobów są używane podczas procesu migracji.

1. Sprawdź kondycję domeny zarządzanej przez usługę Azure AD DS w Azure Portal. Jeśli masz jakieś alerty dla domeny zarządzanej, Rozwiąż je przed rozpoczęciem procesu migracji.
1. Opcjonalnie, jeśli planujesz przenieść inne zasoby do Menedżer zasobów modelu wdrażania i sieci wirtualnej, upewnij się, że te zasoby można migrować. Aby uzyskać więcej informacji, zobacz [Migrowanie zasobów IaaS z platformy klasycznej do Menedżer zasobów][migrate-iaas].

    > [!NOTE]
    > Nie Konwertuj klasycznej sieci wirtualnej na Menedżer zasobów sieci wirtualnej. Jeśli to zrobisz, nie ma możliwości wycofania ani przywrócenia domeny zarządzanej AD DS platformy Azure.

## <a name="prepare-the-managed-domain-for-migration"></a>Przygotuj domenę zarządzaną do migracji

Azure PowerShell służy do przygotowania domeny zarządzanej AD DS platformy Azure do migracji. Te kroki obejmują tworzenie kopii zapasowej, wstrzymywanie synchronizacji oraz usuwanie usługi w chmurze, która hostuje AD DS platformy Azure. Po zakończeniu tego kroku usługa Azure AD DS jest przełączana w tryb offline przez pewien czas. Jeśli krok przygotowania zakończy się niepowodzeniem, możesz [przywrócić poprzedni stan](#roll-back).

Aby przygotować domenę zarządzaną platformy Azure AD DS na potrzeby migracji, wykonaj następujące czynności:

1. Zainstaluj skrypt `Migrate-Aaads` z [Galeria programu PowerShell][powershell-script]. Ten skrypt migracji programu PowerShell jest podpisany cyfrowo przez Zespół inżynieryjny usługi Azure AD.

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. Utwórz zmienną do przechowywania poświadczeń przez skrypt migracji za pomocą polecenia cmdlet [Get-Credential][get-credential] .

    Podane konto użytkownika musi mieć uprawnienia *administratora globalnego* w dzierżawie usługi Azure AD, aby umożliwić AD DS platformy Azure, a następnie utworzyć uprawnienia *współautora* w ramach subskrypcji platformy Azure w celu utworzenia wymaganych zasobów usługi Azure AD DS.

    Po wyświetleniu monitu wprowadź odpowiednie konto użytkownika i hasło:

    ```powershell
    $creds = Get-Credential
    ```

1. Teraz uruchom polecenie cmdlet `Migrate-Aadds` przy użyciu parametru *-Prepare* . Podaj wartość *-ManagedDomainFqdn* dla własnej domeny zarządzanej platformy Azure AD DS, na przykład *aaddscontoso.com*:

    ```powershell
    Migrate-Aadds `
        -Prepare `
        -ManagedDomainFqdn aaddscontoso.com `
        -Credentials $creds
    ```

## <a name="migrate-the-managed-domain"></a>Migrowanie domeny zarządzanej

Po przygotowaniu i wykonaniu kopii zapasowej domeny zarządzanej AD DS platformy Azure można migrować domenę. Ten krok umożliwia odtworzenie maszyn wirtualnych Azure AD Domain Services kontrolera domeny przy użyciu modelu wdrażania Menedżer zasobów. Wykonanie tego kroku może potrwać od 1 do 3 godzin.

Uruchom polecenie cmdlet `Migrate-Aadds` przy użyciu parametru *-commit* . Podaj wartość *-ManagedDomainFqdn* dla własnej domeny zarządzanej AD DS platformy Azure, która została przygotowana w poprzedniej sekcji, na przykład *aaddscontoso.com*:

Określ docelową grupę zasobów zawierającą sieć wirtualną, do której chcesz migrować AD DS platformy Azure, na przykład grupa *zasobów*. Podaj docelową sieć wirtualną, taką jak *myVnet*i podsieć, taką jak *DomainServices*.

Po uruchomieniu tego polecenia nie można wycofać:

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn aaddscontoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds
```

Po sprawdzeniu przez skrypt, że domena zarządzana jest gotowa do migracji, wprowadź wartość *Y* , aby rozpocząć proces migracji.

> [!IMPORTANT]
> Nie Konwertuj klasycznej sieci wirtualnej na Menedżer zasobów sieci wirtualnej podczas procesu migracji. W przypadku konwersji sieci wirtualnej nie można wycofać ani przywrócić domeny zarządzanej AD DS platformy Azure, ponieważ oryginalna Sieć wirtualna już nie istnieje.

Co dwie minuty podczas procesu migracji wskaźnik postępu zgłasza bieżący stan, jak pokazano w następujących przykładowych danych wyjściowych:

![Wskaźnik postępu migracji AD DS platformy Azure](media/migrate-from-classic-vnet/powershell-migration-status.png)

Proces migracji jest kontynuowany, nawet jeśli zostanie zamknięty skrypt programu PowerShell. W Azure Portal stanie się raporty *w ramach domeny zarządzanej.*

Po pomyślnym zakończeniu migracji można wyświetlić adres IP pierwszego kontrolera domeny w Azure Portal lub Azure PowerShell. Wyświetlany jest również szacowany czas na drugim dostępnym kontrolerze domeny.

Na tym etapie można opcjonalnie przenieść inne istniejące zasoby z klasycznego modelu wdrażania i sieci wirtualnej. Można też zachować zasoby w klasycznym modelu wdrażania i równorzędne sieci wirtualne po zakończeniu migracji AD DS platformy Azure.

## <a name="test-and-verify-connectivity-after-the-migration"></a>Testowanie i weryfikowanie łączności po migracji

Pomyślna instalacja drugiego kontrolera domeny może zająć trochę czasu i będzie można go używać w domenie zarządzanej AD DS platformy Azure.

W modelu wdrażania Menedżer zasobów zasoby sieciowe dla domeny zarządzanej platformy Azure AD DS są wyświetlane w Azure Portal lub Azure PowerShell. Aby dowiedzieć się więcej o tym, co to są zasoby sieciowe i jak to zrobić, zobacz [zasoby sieciowe używane przez usługę Azure AD DS][network-resources].

Gdy jest dostępny co najmniej jeden kontroler domeny, wykonaj następujące czynności konfiguracyjne dla łączności sieciowej z maszynami wirtualnymi:

* **Zaktualizuj ustawienia serwera DNS** Aby umożliwić innym zasobom w Menedżer zasobów sieci wirtualnej rozpoznawanie i używanie domeny zarządzanej AD DS platformy Azure, zaktualizuj ustawienia DNS przy użyciu adresów IP nowych kontrolerów domeny. Azure Portal mogą automatycznie konfigurować te ustawienia. Aby dowiedzieć się więcej o konfigurowaniu Menedżer zasobów sieci wirtualnej, zobacz temat [Aktualizowanie ustawień DNS dla sieci wirtualnej platformy Azure][update-dns].
* **Uruchom ponownie przyłączone do domeny maszyny wirtualne** — w przypadku zmiany adresów IP serwera DNS dla kontrolerów domeny AD DS platformy Azure ponownie uruchom wszystkie maszyny wirtualne przyłączone do domeny, tak aby korzystały z nowych ustawień serwera DNS. Jeśli aplikacje lub maszyny wirtualne ręcznie skonfigurowali ustawienia DNS, należy je zaktualizować ręcznie przy użyciu nowych adresów IP serwerów DNS kontrolerów domeny, które są wyświetlane w Azure Portal.

Teraz Przetestuj połączenie z siecią wirtualną i rozpoznawanie nazw. Na maszynie wirtualnej, która jest połączona z siecią wirtualną Menedżer zasobów lub z nią komunikacji równorzędnej, wypróbuj następujące testy komunikacji sieciowej:

1. Sprawdź, czy można wysłać polecenie ping do adresu IP jednego z kontrolerów domeny, takich jak `ping 10.1.0.4`
    * Adresy IP kontrolerów domeny są wyświetlane na stronie **Właściwości** dla domeny zarządzanej platformy Azure AD DS w Azure Portal.
1. Sprawdź rozpoznawanie nazw domen zarządzanych, takich jak `nslookup aaddscontoso.com`
    * Określ nazwę DNS swojej domeny zarządzanej AD DS platformy Azure, aby sprawdzić, czy ustawienia DNS są poprawne i rozwiązuje.

Drugi kontroler domeny powinien być dostępny 1-2 godzin po zakończeniu migracji. Aby sprawdzić, czy drugi kontroler domeny jest dostępny, sprawdź na stronie **Właściwości** dla domeny zarządzanej platformy Azure AD DS w Azure Portal. Jeśli pokazane są dwa adresy IP, drugi kontroler domeny jest gotowy.

## <a name="optional-post-migration-configuration-steps"></a>Opcjonalne kroki konfiguracji po migracji

Po pomyślnym zakończeniu procesu migracji niektóre opcjonalne czynności konfiguracyjne obejmują włączenie dzienników inspekcji lub powiadomień e-mail lub zaktualizowanie szczegółowych zasad haseł.

### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>Subskrybowanie dzienników inspekcji przy użyciu Azure Monitor

Usługa Azure AD DS udostępnia dzienniki inspekcji w celu ułatwienia rozwiązywania problemów i wyświetlania zdarzeń na kontrolerach domeny. Aby uzyskać więcej informacji, zobacz [Włączanie i używanie dzienników inspekcji][security-audits].

Za pomocą szablonów można monitorować ważne informacje uwidocznione w dziennikach. Na przykład szablon skoroszyt dziennika inspekcji może monitorować możliwe blokady konta w domenie zarządzanej AD DS platformy Azure.

### <a name="configure-azure-ad-domain-services-email-notifications"></a>Konfigurowanie Azure AD Domain Services powiadomień e-mail

Aby otrzymywać powiadomienia o wykryciu problemu w domenie zarządzanej AD DS platformy Azure, zaktualizuj ustawienia powiadomień e-mail w Azure Portal. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień powiadomień][notifications].

### <a name="update-fine-grained-password-policy"></a>Aktualizuj szczegółowe zasady haseł

W razie potrzeby można zaktualizować szczegółowe zasady haseł w taki sposób, aby były mniej restrykcyjne niż konfiguracja domyślna. Możesz użyć dzienników inspekcji, aby określić, czy ustawienie mniej restrykcyjne ma sens, a następnie skonfiguruj zasady zgodnie z potrzebami. Poniższe kroki wysokiego poziomu służą do przeglądania i aktualizowania ustawień zasad dla kont, które są wielokrotnie blokowane po migracji:

1. [Skonfiguruj zasady haseł][password-policy] dla mniejszej liczby ograniczeń w domenie zarządzanej AD DS platformy Azure i obserwuj zdarzenia w dziennikach inspekcji.
1. Jeśli jakieś konta usług używają wygasłych haseł określonych w dziennikach inspekcji, zaktualizuj te konta przy użyciu poprawnego hasła.
1. Jeśli maszyna wirtualna jest dostępna w Internecie, przejrzyj nazwy kont ogólnych, takich jak *administrator*, *użytkownik*lub *gość* z dużymi próbami logowania. Jeśli to możliwe, zaktualizuj te maszyny wirtualne tak, aby używały mniej ogólnych nazw kont.
1. Użyj funkcji śledzenia sieci na maszynie wirtualnej, aby zlokalizować źródło ataków i zablokować te adresy IP, aby umożliwić podejmowanie prób logowania.
1. W przypadku wystąpienia minimalnych problemów z blokadą należy zaktualizować szczegółowe zasady haseł, aby były tak restrykcyjne, jak to konieczne.

### <a name="creating-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Aby zabezpieczyć porty wymagane dla domeny zarządzanej i zablokować cały ruch przychodzący, usługa Azure AD DS wymaga sieciowej grupy zabezpieczeń. Ta sieciowa Grupa zabezpieczeń działa jako dodatkowa warstwa ochrony w celu blokowania dostępu do domeny zarządzanej i nie jest tworzona automatycznie. Aby utworzyć grupę zabezpieczeń sieci i otworzyć wymagane porty, zapoznaj się z następującymi krokami:

1. W Azure Portal wybierz zasób AD DS platformy Azure. Na stronie Przegląd zostanie wyświetlony przycisk, aby utworzyć grupę zabezpieczeń sieci, jeśli nie ma żadnych skojarzonych z Azure AD Domain Services.
1. W przypadku używania bezpiecznego protokołu LDAP Dodaj regułę do sieciowej grupy zabezpieczeń, aby zezwalać na ruch przychodzący dla portu *TCP* *636*. Aby uzyskać więcej informacji, zobacz [Konfigurowanie bezpiecznego protokołu LDAP][secure-ldap].

## <a name="roll-back-and-restore-from-migration"></a>Wycofywanie i przywracanie z migracji

Do pewnego momentu w procesie migracji można wycofać lub przywrócić domenę zarządzaną platformy Azure AD DS.

### <a name="roll-back"></a>Wycofywanie

Jeśli wystąpi błąd podczas uruchamiania polecenia cmdlet programu PowerShell w celu przygotowania do migracji w kroku 2 lub migracji w kroku 3, domena zarządzana AD DS platformy Azure może przywrócić pierwotną konfigurację. Ta wycofywanie wymaga oryginalnej klasycznej sieci wirtualnej. Należy pamiętać, że adresy IP nadal mogą ulec zmianie po wycofaniu.

Uruchom polecenie cmdlet `Migrate-Aadds` za pomocą parametru *-Abort* . Podaj wartość *-ManagedDomainFqdn* dla własnej domeny zarządzanej AD DS platformy Azure, która została przygotowana w poprzedniej sekcji, takiej jak *aaddscontoso.com*, i nazwę klasycznej sieci wirtualnej, na przykład *myClassicVnet*:

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn aaddscontoso.com `
    -ClassicVirtualNetworkName myClassicVnet `
    -Credentials $creds
```

### <a name="restore"></a>Przywracanie

W ostatnim przypadku Azure AD Domain Services można przywrócić z ostatniej dostępnej kopii zapasowej. Kopia zapasowa jest wykonywana w kroku 1 migracji w celu upewnienia się, że najbardziej aktualna kopia zapasowa jest dostępna. Ta kopia zapasowa jest przechowywana przez 30 dni.

Aby przywrócić domenę zarządzaną platformy Azure AD DS z kopii zapasowej, [Otwórz bilet przypadku pomocy technicznej przy użyciu Azure Portal][azure-support]. Podaj identyfikator katalogu, nazwę domeny i powód przywracania. Proces obsługi i przywracania może potrwać kilka dni.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli masz problemy po migracji do modelu wdrażania Menedżer zasobów, zapoznaj się z następującymi typowymi obszarami rozwiązywania problemów:

* [Rozwiązywanie problemów z przyłączaniem do domeny][troubleshoot-domain-join]
* [Rozwiązywanie problemów z blokadą konta][troubleshoot-account-lockout]
* [Rozwiązywanie problemów z logowaniem do konta][troubleshoot-sign-in]
* [Rozwiązywanie problemów z łącznością za pośrednictwem protokołu LDAP][tshoot-ldaps]

## <a name="next-steps"></a>Następne kroki

Po przeprowadzeniu migracji domeny zarządzanej AD DS platformy Azure do modelu wdrażania Menedżer zasobów [Utwórz i Dołącz do domeny maszynę wirtualną z systemem Windows][join-windows] , a następnie [Zainstaluj narzędzia do zarządzania][tutorial-create-management-vm].

<!-- INTERNAL LINKS -->
[azure-bastion]: ../bastion/bastion-overview.md
[network-considerations]: network-considerations.md
[azure-powershell]: /powershell/azure/overview
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Set-AzContext]: /powershell/module/az.accounts/set-azcontext
[Get-AzResource]: /powershell/module/az.resources/get-azresource
[Set-AzResource]: /powershell/module/az.resources/set-azresource
[network-resources]: network-considerations.md#network-resources-used-by-azure-ad-ds
[update-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[security-audits]: security-audit-events.md
[notifications]: notifications.md
[password-policy]: password-policy.md
[secure-ldap]: tutorial-configure-ldaps.md
[migrate-iaas]: ../virtual-machines/windows/migration-classic-resource-manager-overview.md
[join-windows]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[troubleshoot-domain-join]: troubleshoot-domain-join.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[tshoot-ldaps]: tshoot-ldaps.md
[get-credential]: /powershell/module/microsoft.powershell.security/get-credential

<!-- EXTERNAL LINKS -->
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/
