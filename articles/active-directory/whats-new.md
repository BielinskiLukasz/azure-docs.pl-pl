---
title: "Co nowego? Informacje o wersji dla usługi Azure Active Directory | Dokumentacja firmy Microsoft"
description: "Poznaj nowe usłudze Azure Active Directory (Azure AD), takie jak informacje o najnowszej wersji, znane problemy, poprawki, przestarzałe funkcje i nadchodzących zmianach."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: a30b5d08377594b8ad7e10b63a23e2a9d168af9c
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="whats-new-in-azure-active-directory"></a>Nowości w usłudze Azure Active Directory


> Bądź na bieżąco z nowości w usłudze Azure Active Directory (Azure AD) Subskrybuj [ ![RSS](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us) [źródła danych](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us).



Usługi Azure AD odbiera ulepszenia w sposób ciągły. Aby bądź na bieżąco o najnowszych zmianach, w tym artykule przedstawiono informacje o:

-   Najnowsze wersje
-   Znane problemy
-   Poprawki błędów
-   Funkcje uznane za przestarzałe
-   Plany zmiany

Ta strona jest aktualizowana co miesiąc, więc ponownie regularnie.



## <a name="february-2018"></a>2018 lutego
 

### <a name="improved-navigation-for-managing-users-and-groups"></a>Ulepszone nawigacji do zarządzania użytkownikami i grupami

**Typ:** Planowanie zmian  
**Kategoria usług:** Directory Management  
**Możliwości produktu:** katalogu
 

Usprawniono środowisko nawigacji do zarządzania użytkownikami i grupami. Z przeglądu katalogu można teraz przejść bezpośrednio do listy wszystkich użytkowników, których łatwiejszy dostęp do listy usuniętych użytkowników. Na stronie przeglądu katalogu można także przechodzić bezpośrednio do listy wszystkich grup z łatwiejszy dostęp do grupy zarządzania. A także na stronie Przegląd katalogu można wyszukiwać użytkowników, grupy, aplikacja całościowa lub rejestracji aplikacji.
 

---


### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Raporty dostępności logowania i inspekcji w programie Microsoft Azure przez 21Vianet (21Vianet w Chinach Azure)

**Typ:** nowej funkcji  
**Kategoria usług:** suwerenne chmury  
**Możliwości produktu:** monitorowanie i raportowanie
 

Azure AD działania dziennika raporty są teraz dostępne w systemie Microsoft Azure przez 21Vianet wystąpień (21Vianet w Chinach Azure). Uwzględniono następujące dzienniki:

- **Dzienniki aktywności logowania** — obejmuje wszystkie sesje logowania dzienniki skojarzony z dzierżawą.

- **Samoobsługowego dzienniki inspekcji hasła** — obejmuje wszystkie dzienniki inspekcji SSPR.

- **Dzienniki inspekcji zarządzania katalogu** -obejmuje zarządzanie katalogu związane z dziennika, takich jak użytkownika zarządzania, zarządzanie aplikacjami i innych inspekcji.

Za pomocą tych dzienników można uzyskać wgląd w sposób wykonuje środowiska. Na podstawie udostępnionych danych można:

- Określ, jak usługi i aplikacje są wykorzystywane przez użytkowników.

- Rozwiązywanie problemów uniemożliwia pobieranie pracy użytkowników.

Aby uzyskać więcej informacji na temat używania tych raportów, zobacz [raportowania usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).
 

---


### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Wyświetlanie raportów działania usługi Azure AD za pomocą roli "Raport czytnika" (rola bez uprawnień administratora)

**Typ:** nowej funkcji  
**Kategoria usług:** raportowania  
**Możliwości produktu:** monitorowanie i raportowanie
 

Jako część opinie klientów do Włącz role bez uprawnień administratora na dostęp do usługi Azure AD działania dzienniki, możemy włączono możliwości dla użytkowników, którzy są w roli "Raport czytnika" z dostępem do logowania i przeprowadzanie inspekcji aktywności w portalu Azure, jak i za pomocą naszych interfejsów API Graph. 

Aby uzyskać więcej informacji jak używać tych raportów, zobacz [raportowania usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---
 


### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Identyfikator pracownika oświadczeń dostępne jako atrybut użytkownika i identyfikator użytkownika

**Typ:** nowej funkcji  
**Kategoria usług:** aplikacje przedsiębiorstwa  
**Możliwości produktu:** logowania jednokrotnego
 

Można skonfigurować **identyfikator pracownika** jako identyfikator użytkownika i atrybut użytkownika dla użytkowników i gości B2B w SAML logowania jednokrotnego w aplikacjach z aplikacją interfejsu użytkownika.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wydanych w tokenie SAML dla aplikacji przedsiębiorstwa w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).
 

---


### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Uproszczone zarządzanie aplikacjami przy użyciu symboli wieloznacznych w serwera Proxy aplikacji usługi Azure AD

**Typ:** nowej funkcji  
**Kategoria usług:** serwera Proxy aplikacji  
**Możliwości produktu:** uwierzytelnianie użytkownika
 

Aby ułatwić wdrożenie aplikacji i ograniczyć czynności administracyjnych, firma Microsoft obsługują możliwości publikowania aplikacji przy użyciu symboli wieloznacznych. Aby opublikować aplikację symboli wieloznacznych, może wykonaj przepływu publikowania standardowej aplikacji, ale użyć symbolu wieloznacznego w adresach URL wewnętrznych i zewnętrznych.

Aby uzyskać więcej informacji, zobacz [aplikacji symbolu wieloznacznego w serwer proxy aplikacji usługi Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-application-proxy-wildcard)

 

---
 
### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nowe polecenia cmdlet w celu zapewnienia obsługi konfiguracji serwera proxy aplikacji

**Typ:** nowej funkcji  
**Kategoria usług:** serwera Proxy aplikacji  
**Możliwości produktu:** platformy
 

Najnowszej wersji modułu AzureAD programu PowerShell w wersji zapoznawczej zawiera nowe polecenia cmdlet, które pozwala konfigurować aplikacje serwera Proxy aplikacji przy użyciu programu PowerShell.

Dostępne są następujące nowe polecenia cmdlet: 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup


 

---
 

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Nowe polecenia cmdlet do obsługi konfiguracji grup

**Typ:** nowej funkcji  
**Kategoria usług:** serwera Proxy aplikacji  
**Możliwości produktu:** platformy
 

Najnowszej wersji modułu programu AzureAD PowerShell zawiera polecenia cmdlet do zarządzania grupami w usłudze Azure AD. Te polecenia cmdlet były wcześniej dostępne w AzureADPreview module i są teraz dodane do modułu AzureAD

Polecenia cmdlet grupy, które są teraz wersji dla ogólnej dostępności są: 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup
 

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Dostępna jest nowa wersja programu Azure AD Connect

**Typ:** nowej funkcji  
**Kategoria usług:** AD Sync  
**Możliwości produktu:** platformy
 

Azure AD Connect jest preferowanym narzędzia do synchronizacji danych między usługami Azure AD i źródeł danych lokalnych, w tym, jak i usługi Active Directory systemu Windows Server.

**Ważne**
 
Ta kompilacja wprowadza schematu i synchronizacji zmian reguły. Usługa Azure AD Connect synchronizacji wyzwala pełny Import i pełną synchronizację kroki po uaktualnieniu. Aby uzyskać informacje na temat zmienić to zachowanie, zobacz [jak mają być odroczone pełną synchronizację po uaktualnieniu](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Ta wersja zawiera następujące aktualizacje i zmiany:

**Rozwiązane problemy**

- Usuń okna czasowego na zadania w tle dla strony filtrowania Paritition podczas przełączania do następnej strony.
- Stałe błędu, który spowodował naruszenie zasad dostępu podczas ConfigDB akcji niestandardowej.
- Stałe błędów pozwoli na odzyskanie limit czasu połączenia sql.
- Stałe błędu, jeżeli certyfikatów z symbolami wieloznacznymi sieci SAN nie wyboru dotyczące wymagań wstępnych.
- Stałe błędów, co powoduje, że miiserver.exe awarii podczas eksportowania łącznika usługi AAD.
- Stałe błędów próba nieprawidłowe hasło, które rejestrowane na kontrolerze domeny, podczas uruchamiania usługi AAD w kreatorze, aby zmienić konfigurację connect

**Nowe funkcje i ulepszenia**

- GDPR wprowadziliśmy oznacza rodzaje danych klienta, które są współużytkowane z firmą Microsoft (danych telemetrycznych kondycji, itp.), zawierają łącza do szczegółową dokumentację w trybie online i umożliwiają użytkownikowi zmienianie preferencji dotyczących.  Tego wyboru w dodaje następujące czynności:
    - Udostępnianie danych i powiadomienie o prywatności w czystej instalacji strony umowy licencyjnej.

    - Udostępnianie i ochrona prywatności powiadomienia danych na stronie uaktualnienia.

    - Nowe zadanie dodatkowe **ustawienia prywatności** której użytkownik może zmienić swoje preferencje.
 
- Dane telemetryczne aplikacji — Administratorzy można przełączać tej klasy danych wł. / wył.

- Azure AD kondycji dane — Administratorzy muszą odwiedź portal kondycji do kontrolowania ustawień kondycji. Po zmianie zasad usługi agentów odczytu i wymuszania go.

- Dodaje akcje konfiguracji funkcji zapisywania zwrotnego urządzeń i pasek postępu inicjowania strony.

- Ulepszone ogólne diagnostyki raportu w formacie HTML i zbierania danych pełnego tekstu ZIP / raportu w formacie HTML.

- Większą niezawodność automatycznego uaktualniania i dodać dodatkowe telemetrię, aby upewnić się, że można ustalić kondycję serwera.

- Ogranicz uprawnienia dostępne do kont uprzywilejowanych w łączniku AD konta. W przypadku nowych instalacji Kreator ogranicza uprawnienia, które uprzywilejowane konta ma konta MSOL po utworzeniu konta MSOL. Zmiany wpływają na instalacje niestandardowe z automatycznego tworzenia konta oraz instalacji ekspresowej.

- Zmienić Instalatora, aby nie wymaga uprawnień administratora systemu w czystej instalacji programu AADConnect.

- Nowe narzędzia do rozwiązywania problemów z synchronizacją dla konkretnego obiektu. Obecnie to narzędzie sprawdza, czy:

    - UserPrincipalName niezgodność między obiektów synchronizowanych użytkowników i konta użytkowników w dzierżawie programu Azure AD.
  
    - Jeśli obiekt jest filtrowana z synchronizacji z powodu filtrowania domeny
  
    - Jeśli obiekt jest filtrowana z synchronizacji z powodu jednostki organizacyjnej (OU) filtrowania

- Nowe narzędzie do synchronizacji bieżącego skrót hasła przechowywane w lokalnej usłudze Active Directory dla określonego konta użytkownika. Narzędzie nie wymaga zmiany hasła. 
 

---
 

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Dodano obsługi zasad ochrony aplikacji usługi Intune dla aplikacji za pomocą dostępu warunkowego na podstawie aplikacji usługi Azure AD

**Typ:** funkcji zmienione  
**Kategoria usług:** dostępu warunkowego  
**Możliwości produktu:** tożsamości bezpieczeństwa i ochrony
 

Dodano więcej aplikacji, które obsługują dostępu warunkowego na podstawie aplikacji. Teraz możesz uzyskać dostęp do usługi Office 365 i innych aplikacji chmury Azure połączone AD przy użyciu tych aplikacji klienta zatwierdzone.

Następujące aplikacje zostaną dodane do końca lutego 

- Microsoft PowerBI

- Microsoft Launcher

- Microsoft Invoicing

Aby uzyskać więcej informacji, zobacz:

- [Wymagania aplikacji zatwierdzonych klienta](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD na podstawie aplikacji dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)

 

---
 

### <a name="terms-of-use-update-to-mobile-experience"></a>Warunki użytkowania zaktualizować na korzystanie z urządzeń przenośnych 

**Typ:** funkcji zmienione  
**Kategoria usług:** warunki użytkowania  
**Możliwości produktu:** ładu
 

Po wyświetleniu warunki użytkowania, możesz teraz kliknąć **o problemy z wyświetlaniem? Kliknij tutaj**. Kliknięcie tego łącza otwiera warunki użytkowania w sposób macierzysty na urządzeniu. Niezależnie od tego, czy rozmiar czcionki w dokumencie lub rozmiaru ekranu urządzenia można powiększyć i odczytu dokumentu zgodnie z potrzebami. 
 

---
 
## <a name="january-2018"></a>2018 stycznia
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nowe federacyjnych aplikacje dostępne w galerii aplikacji Azure AD 

**Typ:** nowej funkcji  
**Kategoria usług:** aplikacje przedsiębiorstwa  
**Możliwości produktu:** 3 integracji strony
 

W styczniu 2018 dodano następujące nowe aplikacje z obsługą federacji w galerii aplikacji:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [oprogramowanie do zarządzania prywatności OneTrust](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk federacyjnych katalogu](https://go.microsoft.com/fwlink/?linkid=864699) i [wierności NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Aby uzyskać pełny przegląd wszystkich dostępnych samouczków, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial).
 

---
 


### <a name="sign-in-with-additional-risk-detected"></a>Wykryto logowanie z dodatkowym ryzykiem

**Typ:** nowej funkcji  
**Kategoria usług:** Identity Protection  
**Możliwości produktu:** tożsamości bezpieczeństwa i ochrony
 

Wgląd w informacje o uzyskać zdarzenie wykryte zagrożenie jest powiązany z subskrypcją usługi Azure AD. Możesz uzyskać bardziej szczegółowe informacje o wszystkich wykryć podstawowej wersji Azure AD Premium P2.

Z wersji Azure AD Premium P1 wykryć, które nie są objęte licencję są wyświetlane jako zdarzenie ryzyka logowania z dodatkowe zagrożenie wykrywane.

Aby uzyskać więcej informacji, zobacz [Zdarzenia o podwyższonym ryzyku w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Ukryj aplikacji usługi Office 365 z paneli dostępu użytkownika końcowego

**Typ:** nowej funkcji  
**Kategoria usług:** Moje aplikacje  
**Możliwości produktu:** logowania jednokrotnego
 

Można teraz lepiej zarządzać jak aplikacje pakietu Office 365 widoczne na panele dostępu użytkownika przy użyciu nowego ustawienia użytkownika. Ta opcja jest przydatna dla ograniczenie liczby aplikacji w panele dostępu użytkownika, jeśli wolisz Pokaż tylko aplikacje pakietu Office w portalu usługi Office. To ustawienie znajduje się w **ustawienia użytkownika** i nosi nazwę **użytkownicy widzą tylko aplikacje usługi Office 365 w portalu usługi Office 365**.
 

Aby uzyskać więcej informacji, zobacz [Ukryj aplikację za pomocą środowiska użytkownika w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 


### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Bezproblemowe Zaloguj się do aplikacji włączone dla rejestracji Jednokrotnej hasło bezpośrednio z adresu URL aplikacji 

**Typ:** nowej funkcji  
**Kategoria usług:** Moje aplikacje  
**Możliwości produktu:** logowania jednokrotnego
 

Moje aplikacje rozszerzenia przeglądarki jest teraz dostępna za pośrednictwem wygodne narzędzie zapewniające jednokrotnego Moje aplikacje na możliwości jako skrót w przeglądarce. Po zainstalowaniu przez użytkownika zostanie wyświetlona ikona waffle w przeglądarce zapewniający ich szybki dostęp do aplikacji. Użytkownicy mogą teraz korzystać z:

- Aplikacji ze strony logowania aplikacji opartych na możliwość bezpośrednio Zaloguj się do logowania jednokrotnego hasła
- Uruchamianie dowolnej aplikacji przy użyciu funkcji szybkiego wyszukiwania
- Skróty do ostatnio używanych aplikacji z rozszerzenia
- Rozszerzenie jest dostępny na krawędzi, Chrome i Firefox.
 
Aby uzyskać więcej informacji, zobacz [Moje aplikacje bezpiecznego logowania rozszerzenia](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction#my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Administrowanie usługami Azure AD czynności w klasycznym portalu Azure została wycofana.

**Typ:** przestarzałe   
**Kategoria usług:** usługi Azure AD  
**Możliwości produktu:** katalogu
 

Począwszy od 8 stycznia 2018, administracja programu Azure AD czynności w klasycznym portalu Azure została wycofana. Odbyło się w połączeniu z wycofanie klasycznego portalu Azure, samej siebie. Idąc dalej, należy użyć [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) wszystkie Twoje portalu administrowanie w oparciu o usługi Azure AD.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Portal sieci web PhoneFactor została wycofana.

**Typ:** przestarzałe  
**Kategoria usług:** usługi Azure AD  
**Możliwości produktu:** katalogu
 

Począwszy od 8 stycznia 2018 PhoneFactor portalu sieci web została wycofana. Ten portal została użyta do celów administracji serwera usługi MFA, ale te funkcje zostały przeniesione do portalu Azure w portal.azure.com. 

Konfiguracja uwierzytelniania Wieloskładnikowego znajduje się pod adresem: **usługi Azure Active Directory \> serwera usługi MFA**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Zastąpić raportów usługi Azure AD


**Typ:** przestarzałe  
**Kategoria usług:** raportowania  
**Możliwości produktu:** zarządzania cyklem życia tożsamości  


Z ogólnej dostępności nowej konsoli administrowania Azure Active Directory i nowych interfejsów API, które są teraz dostępne dla raportów zarówno działania, jak i zabezpieczenia, raport interfejsów API w obszarze "/ reports" punktu końcowego został wycofany koniec 31 grudnia 2017 r.


**Co to jest dostępny?**

W ramach przejścia do nowej konsoli administracyjnej wprowadzono 2 nowych interfejsów API dostępne do pobierania dzienników aktywności w usłudze Azure AD. Nowy zestaw interfejsów API zawierają bardziej zaawansowane funkcje filtrowania i sortowania funkcje oprócz bardziej zaawansowane funkcje inspekcji i rejestrowania działania. Obecnie jest możliwy wcześniej dostępne raporty dotyczące zabezpieczeń danych za pośrednictwem zdarzenia ryzyka Identity Protection interfejsu API programu Microsoft Graph.

Aby uzyskać więcej informacji, zobacz:

- [Wprowadzenie do usługi Azure Active Directory raportowania interfejsu API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Wprowadzenie do usługi Azure Active Directory Identity Protection oraz Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)


---


## <a name="december-2017"></a>2017 grudnia
 

### <a name="terms-of-use-in-the-access-panel"></a>Warunki użytkowania w panelu dostępu

**Typ:** nowej funkcji  
**Kategoria usług:** warunki użytkowania  
**Możliwości produktu:** ładu/zgodności
 
Możesz teraz przejść do panelu dostępu i wyświetlić warunki użytkowania, które zostały wcześniej zaakceptowane.

Wykonaj następujące kroki:

1. Przejdź do [MyApps portal](https://myapps.microsoft.com)i zaloguj się.

2. W prawym górnym rogu, wybierz nazwę, a następnie wybierz **profilu** z listy. 

3. W Twojej **profilu**, wybierz pozycję **Przejrzyj warunki użytkowania**. 

4. Teraz możesz przejrzeć warunki użytkowania akceptowane. 

Aby uzyskać więcej informacji, zobacz [warunki usługi Azure AD, użyj funkcji (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 

### <a name="new-azure-ad-sign-in-experience"></a>Nowego środowiska logowania usługi Azure AD

**Typ:** nowej funkcji  
**Kategoria usług:** usługi Azure AD  
**Możliwości produktu:** uwierzytelnianie użytkownika
 
Azure AD i systemu tożsamości konta Microsoft UI uległy zmianie, aby miały spójny wygląd i zachowanie. Ponadto strony logowania usługi Azure AD zbiera nazwy użytkownika najpierw następuje poświadczeń na drugi ekranu.

Aby uzyskać więcej informacji, zobacz [nowego środowiska logowania usługi Azure AD jest teraz w publicznej wersji zapoznawczej](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Mniejszą liczbę monitów logowania: środowisko do nowego "wylogowuj mnie" dla logowania w usłudze Azure AD

**Typ:** nowej funkcji  
**Kategoria usług:** usługi Azure AD  
**Możliwości produktu:** uwierzytelnianie użytkownika
 
**Wylogowuj mnie** pole wyboru na stronie logowania w usłudze Azure AD został zastąpiony nowego wiersza, wyświetlany po pomyślnie uwierzytelnić. 

Jeśli użytkownik odpowie **tak** na ten monit, usługa zapewnia token odświeżania trwałych. To zachowanie jest takie samo jak w przypadku wybrania **wylogowuj mnie** pole wyboru w starym środowisko. W przypadku dzierżaw federacyjnych ten wiersz zawiera po pomyślnie wykonać uwierzytelnienia w usłudze federacyjnej.

Aby uzyskać więcej informacji, zobacz [mniejszą liczbę monitów logowania: nowe środowisko "wylogowuj mnie" dla usługi Azure AD jest w wersji zapoznawczej](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---
 

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Dodaj konfigurację, aby wymagać warunki użytkowania można rozszerzyć przed rozpoczęciem akceptowania

**Typ:** nowej funkcji  
**Kategoria usług:** warunki użytkowania  
**Możliwości produktu:** ładu/zgodności
 
Opcja dla administratorów wymaga użytkownikom rozwinąć warunki użytkowania, przed rozpoczęciem akceptowania warunków.

Wybierz opcję **na** lub **poza** aby wymagać od użytkowników rozwinąć warunki użytkowania. **Na** użytkownikom na wyświetlanie warunki użytkowania, przed rozpoczęciem akceptowania ich wymaga ustawienia.

Aby uzyskać więcej informacji, zobacz [warunki usługi Azure AD, użyj funkcji (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 

### <a name="scoped-activation-for-eligible-role-assignments"></a>Zakresie aktywacji dla przypisania ról kwalifikujących się

**Typ:** nowej funkcji  
**Kategoria usług:** Privileged Identity Management  
**Możliwości produktu:** Privileged Identity Management
 
Do aktywowania przypisań ról kwalifikujących się zasobów platformy Azure z mniej niezależność od oryginalnej wartości domyślnych przydziału można użyć aktywacji zakresami. Przykładem jest, jeśli jest przypisany właściciel subskrypcji w dzierżawie. Z zakresie aktywacji można aktywować rolę właściciela zasobów do pięciu zawartych w subskrypcji (takie jak grupy zasobów i maszyn wirtualnych). Określania zakresu proces aktywacji może zmniejszyć prawdopodobieństwo wystąpienia wykonywania niepożądanych zmian do kluczowych zasobów platformy Azure.

Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne w galerii aplikacji Azure AD

**Typ:** nowej funkcji  
**Kategoria usług:** aplikacje przedsiębiorstwa  
**Możliwości produktu:** 3 integracji strony
 
W 2017 grudnia następujących nowych aplikacji z obsługą federacji zostały dodane w galerii aplikacji:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Menedżer środowisko Adobe, [sklepu cyfrowe EFI](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [WORKS obrazu](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [integracji usługi Azure AD MobileIron](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [logowania jednokrotnego SAML dla Bambus przez rozpoznawania GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [Logowania jednokrotnego SAML dla Bitbucket przez rozpoznawania GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, integracji z usługą AD Zenegy Azure.

Aby uzyskać pełny przegląd wszystkich dostępnych samouczków, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial).

 
---
 

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Przepływów pracy dla ról katalogu usługi Azure AD

**Typ:** funkcji zmienione  
**Kategoria usług:** Privileged Identity Management  
**Możliwości produktu:** Privileged Identity Management
 
Przepływ pracy zatwierdzania dla ról katalogu usługi Azure AD jest ogólnie dostępna.

Z procedury zatwierdzania roli uprzywilejowanej Administratorzy mogą wymagać członków roli kwalifikujących się do żądania aktywacji ról, przed użyciem roli uprzywilejowanej. Wielu użytkowników i grup może być delegowane zatwierdzenia obowiązki. Członkowie roli kwalifikujących się otrzymywanie powiadomień o zakończeniu zatwierdzenia i ich rolą jest aktywny.

---
 

### <a name="pass-through-authentication-skype-for-business-support"></a>Uwierzytelniania przekazywanego: Skype dla firm pomocy technicznej

**Typ:** funkcji zmienione  
**Kategoria usług:** uwierzytelnienia (logowania do)  
**Możliwości produktu:** uwierzytelnianie użytkownika


Teraz uwierzytelniania przekazywanego obsługuje logowania użytkowników do usługi Skype dla firm aplikacji klienckich, które obsługują nowoczesnego uwierzytelniania, w tym w trybie online i hybrydowych topologii. 

Aby uzyskać więcej informacji, zobacz [Skype dla firm topologie obsługiwane z nowoczesnego uwierzytelniania](https://technet.microsoft.com/library/mt803262.aspx).
 
---
 

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Aktualizacje usługi Azure AD Privileged Identity Management dla Azure RBAC (wersja zapoznawcza)

**Typ:** funkcji zmienione  
**Kategoria usług:** Privileged Identity Management  
**Możliwości produktu:** Privileged Identity Management
 
Za pomocą odświeżanie w publicznej wersji zapoznawczej usługi Azure AD Privileged Identity Management (PIM) based kontroli dostępu (RBAC) można teraz:

* Użyj wystarczającego administracji.
* Wymagaj zatwierdzenia Uaktywnij role zasobów.
* Zaplanuj przyszłe aktywacji rolę, która wymaga zatwierdzenia dla obu usługi Azure AD i Azure RBAC ról.

 
Aby uzyskać więcej informacji, zobacz [Privileged Identity Management zasobów Azure (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

 
---
 
## <a name="november-2017"></a>2017 listopada
 
### <a name="access-control-service-retirement"></a>Wycofanie usługi kontroli dostępu



**Typ:** Planowanie zmian  
**Kategoria usług:** usługi kontroli dostępu  
**Możliwości produktu:** usługi kontroli dostępu 


 Azure Active kontroli dostępu katalogu (znaną również jako usługa kontroli dostępu) zostaną wycofane w 2018 opóźnione. Więcej informacji, które zawiera szczegółowy harmonogram i wskazówki dotyczące migracji wysokiego poziomu zostanie podany w następnych kilku tygodni. Na tej stronie można pozostawić komentarze z odpowiedzi na pytania dotyczące usługi kontroli dostępu i będzie odpowiadać członka zespołu.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Ogranicz dostęp przeglądarki do usługi Intune Managed Browser 


**Typ:** Planowanie zmian  
**Kategoria usług:** dostępu warunkowego  
**Możliwości produktu:** tożsamości zabezpieczenia i ochrona




Można ograniczyć dostęp za pomocą przeglądarki do usługi Office 365 i innych aplikacji w chmurze Azure AD połączonych za pomocą jako zatwierdzonych aplikacji Intune Managed Browser. 

Teraz można skonfigurować dla dostępu warunkowego opartego na aplikacji następujący warunek:

**Aplikacje klienta:** przeglądarki

**Jaki jest wpływ tej zmiany?**

Obecnie dostęp będzie zablokowany, korzystając z tego warunku. Gdy Podgląd jest dostępny, wszystkie dostępu wymaga użycia aplikacji programu managed browser. 

Poszukaj tej możliwości i dodatkowe informacje w nadchodzących blogi i release notes. 

Aby uzyskać więcej informacji, zobacz [dostępu warunkowego w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe aplikacje zatwierdzone klienta dla dostępu warunkowego na podstawie aplikacji usługi Azure AD

 
**Typ:** Planowanie zmian  
**Kategoria usług:** dostępu warunkowego  
**Możliwości produktu:** tożsamości zabezpieczenia i ochrona




Następujące aplikacje są planowane do dodania do listy [zatwierdzonych aplikacji klienta](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


Aby uzyskać więcej informacji, zobacz:

- [Wymagania aplikacji zatwierdzonych klienta](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD na podstawie aplikacji dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>Warunki użytkowania obsługi wielu języków



**Typ:** nowej funkcji    
**Kategoria usług:** warunki użytkowania  
**Możliwości produktu:** ładu/zgodności





Administratorzy mogą teraz tworzyć nowe warunki użytkowania, które zawierają wiele dokumentów PDF. Można oznaczać tych dokumentów PDF z odpowiedniego języka. Użytkownicy otrzymują plik PDF z zgodny język, w oparciu o ich preferencje. Jeśli nie są niezgodne, jest wyświetlany w języku domyślnym.


---
 

### <a name="real-time-password-writeback-client-status"></a>Stan klienta zapisywania zwrotnego haseł w czasie rzeczywistym



**Typ:** nowej funkcji  
**Kategoria usług:** samoobsługowego resetowania hasła  
**Możliwości produktu:** uwierzytelnianie użytkownika


 

Teraz można sprawdzić stan klienta zapisywania zwrotnego haseł lokalnych. Ta opcja jest dostępna w **integracji lokalnego** sekcji [resetowania hasła](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) strony. 

Jeśli występują problemy z połączeniem klienta lokalnego zapisywania zwrotnego, pojawić się komunikat o błędzie, który zawiera program:

- Informacje na temat Dlaczego nie można nawiązać połączenia lokalnego klienta zapisywania zwrotnego.
- Łącze do dokumentacji, która pomaga w rozwiązaniu problemu. 


Aby uzyskać więcej informacji, zobacz [integracji lokalnego](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Azure AD na podstawie aplikacji dostępu warunkowego 



 
**Typ:** nowej funkcji  
**Kategoria usług:** usługi Azure AD  
**Możliwości produktu:** tożsamości zabezpieczenia i ochrona





Teraz można ograniczyć dostęp do innych aplikacji chmury Azure połączone AD i Office 365 [zatwierdzonych aplikacji klienta](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) obsługujących zasad ochrony aplikacji usługi Intune przy użyciu [dostępu warunkowego na podstawie aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam). Zasady ochrony aplikacji usługi Intune służą do konfigurowania i ochrony danych firmy na tych aplikacji klienckich.

Łącząc [aplikacji na podstawie](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) z [oparta na urządzeniach](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) zasady dostępu warunkowego, masz możliwość ochrony danych osobistych i firmowych urządzeń.

Poniższe warunki i formanty są teraz dostępne do użycia z dostępu warunkowego opartego na aplikacji:

**Warunek obsługiwanych platform**

- iOS
- Android

**Stan aplikacji klienta**

- Aplikacje mobilne i klienci stacjonarni

**Kontrola dostępu**

- Wymagaj zatwierdzonej aplikacji klienckiej


Aby uzyskać więcej informacji, zobacz [dostępu warunkowego na podstawie aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam).

 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Zarządzanie urządzeniami w usłudze Azure AD w portalu Azure



**Typ:** nowej funkcji  
**Kategoria usług:** rejestracji urządzeń i zarządzanie  
**Możliwości produktu:** tożsamości zabezpieczenia i ochrona

 



Teraz można znaleźć wszystkie urządzenia podłączone do usługi Azure AD i działania dotyczące urządzeń w jednym miejscu. Brak nowych czynności administracyjne do zarządzania tożsamościami urządzenia i ustawienia w portalu Azure. W tej wersji można wykonywać następujące czynności:

- Wyświetl wszystkie urządzenia, które są dostępne dla dostępu warunkowego w usłudze Azure AD.
- Wyświetlać właściwości, które obejmują sieci hybrydowe Azure urządzeń dołączonych do usługi AD.
- Znajdowanie kluczy funkcji BitLocker dla urządzeń przyłączonych do usługi AD platformy Azure, zarządzać urządzenie z usługi Intune i inne.
- Zarządzaj ustawieniami związanymi z urządzenia usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [zarządzania urządzeniami za pomocą portalu Azure](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).



 
---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Obsługa macOS jako platforma urządzenia dla dostępu warunkowego dla usługi Azure AD 



**Typ:** nowej funkcji    
**Kategoria usług:** dostępu warunkowego  
**Możliwości produktu:** tożsamości zabezpieczenia i ochrona 
 

Teraz można uwzględnić (lub wykluczyć) macOS jako warunek platformy urządzeń w usłudze Azure AD zasady dostępu warunkowego. Z dodatkiem macOS na platformach obsługiwanych urządzeń można:

- **Rejestrowanie urządzeń i zarządzanie nimi macOS za pomocą usługi Intune.** Podobnie jak w innych platform, takich jak systemy iOS i Android, aplikacji portalu firmy jest dostępne dla macOS ujednoliconego rejestracji. Nowa aplikacja portal firmy dla macOS służy do rejestrowania urządzeń w usłudze Intune i zarejestrowanie go za pomocą usługi Azure AD.
- **Upewnij się, że urządzenia macOS obowiązują zasady zgodności organizacji zdefiniowane w usłudze Intune.** W usłudze Intune w portalu Azure możesz teraz można skonfigurować zasady zgodności dla urządzeń macOS. 
- **Ograniczanie dostępu do aplikacji w usłudze Azure AD tylko do macOS zgodnych urządzeń.** Tworzenie zasad dostępu warunkowego ma macOS jako opcji platformy urządzenia oddzielne. Teraz można tworzyć zasady dostępu warunkowego macOS specyficzne dla aplikacji docelowej ustawiony na platformie Azure.

Aby uzyskać więcej informacji, zobacz:

- [Tworzenie zasad zgodności urządzeń dla urządzeń z systemem macOS w usłudze Intune](https://aka.ms/macoscompliancepolicy)
- [Dostęp warunkowy w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)


 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Rozszerzenie serwera zasady sieciowe uwierzytelnianie wieloskładnikowe Azure 


**Typ:** nowej funkcji    
**Kategoria usług:** uwierzytelnianie wieloskładnikowe  
**Możliwości produktu:** uwierzytelnianie użytkownika




Rozszerzenia serwera zasad sieciowych, uwierzytelnianie wieloskładnikowe Azure dodaje oparte na chmurze usługi Multi-Factor authentication możliwości do infrastruktury uwierzytelniania przy użyciu istniejących serwerów. Z rozszerzeniem serwera zasad sieciowych można dodać połączenie telefoniczne, wiadomość tekstowa lub weryfikacji aplikacji telefonicznej z istniejących przepływ uwierzytelniania. Nie masz instalowania, konfigurowania i konserwacji nowe serwery. 

To rozszerzenie został utworzony dla organizacji, które chcesz chronić połączenia sieci VPN bez wdrażania serwera usługi Azure Multi-Factor Authentication. Serwer zasad sieciowych, które rozszerzenia działa jako karta między usługi RADIUS i oparte na chmurze usługi Azure Multi-Factor Authentication, aby zapewnić drugi składnik uwierzytelniania federacyjnego lub zsynchronizowanych użytkowników.


Aby uzyskać więcej informacji, zobacz [integracji istniejącej infrastrukturze serwera zasad sieciowych z usługi Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Przywróć lub trwale usunąć usunięci użytkownicy


**Typ:** nowej funkcji    
**Kategoria usług:** Zarządzanie użytkownikami  
**Możliwości produktu:** katalogu 



W Centrum administracyjnym usługi Azure AD można teraz:

- Przywrócenie usuniętego użytkownika. 
- Trwałe usunięcie użytkownika.


**Aby wypróbować jej możliwości:**

1. W Centrum administracyjnym usługi Azure AD, wybierz [wszyscy użytkownicy](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) w **Zarządzaj** sekcji. 

2. Z **Pokaż** listy, wybierz **niedawno usunięta użytkowników**. 

3. Wybierz co najmniej jeden użytkownik niedawno usunięte, a następnie przywrócić je lub trwale usunąć je.

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe aplikacje zatwierdzone klienta dla dostępu warunkowego na podstawie aplikacji usługi Azure AD

 
**Typ:** funkcji zmienione  
**Kategoria usług:** dostępu warunkowego  
**Możliwości produktu:** tożsamości zabezpieczenia i ochrona


Następujące aplikacje zostały dodane do listy [zatwierdzonych aplikacji klienta](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- Microsoft Planner
- Azure Information Protection 


Aby uzyskać więcej informacji, zobacz:

- [Wymagania aplikacji zatwierdzonych klienta](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD na podstawie aplikacji dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Użyj "Lub" między formantami w zasadach dostępu warunkowego 


**Typ:** funkcji zmienione    
**Kategoria usług:** dostępu warunkowego  
**Możliwości produktu:** tożsamości zabezpieczenia i ochrona

 
Możesz teraz użyć "lub" (wymagają wybranych formantów) kontroli dostępu warunkowego. Ta funkcja służy do tworzenia zasad z "lub" między kontroli dostępu. Na przykład służy tej funkcji można utworzyć zasadę, która wymaga od użytkownika, zaloguj się przy użyciu usługi Multi-Factor authentication "lub" na zgodnych urządzeniach.

Aby uzyskać więcej informacji, zobacz [kontroli dostępu warunkowego dla usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).

 
---

### <a name="aggregation-of-real-time-risk-events"></a>Agregacja zdarzenia w czasie rzeczywistym ryzyka


**Typ:** funkcji zmienione    
**Kategoria usług:** Identity protection  
**Możliwości produktu:** tożsamości zabezpieczenia i ochrona


W usłudze Azure AD Identity Protection wszystkie zdarzenia w czasie rzeczywistym ryzyka, które pochodzą z tego samego adresu IP w danym dniu teraz są agregowane dla każdego typu zdarzenia ryzyka. Ta zmiana ogranicza ilość zdarzenia o podwyższonym ryzyku pokazano bez zmiany zabezpieczeń użytkownika.

Podstawowy wykrywanie w czasie rzeczywistym działa zawsze, gdy użytkownik się zaloguje. Jeśli zasady zabezpieczeń logowania ryzyka ustawioną usługi Multi-Factor authentication lub blokowanie dostępu, nadal jest wyzwalane podczas każdego ryzykowne logowania.

 
---
 




## <a name="october-2017"></a>2017 października


### <a name="deprecate-azure-ad-reports"></a>Zastąpić raportów usługi Azure AD


**Typ:** Planowanie zmian  
**Kategoria usług:** raportowania  
**Możliwości produktu:** zarządzania cyklem życia tożsamości  



Azure portal udostępnia:

- Nowy konsoli administracyjnej usługi Azure AD.
- Nowe interfejsy API dla raportów działania i zabezpieczeń.
 
Z powodu nowych funkcji raport interfejsów API w punkcie końcowym/Reports zostały wycofane na 10 grudnia 2017 r. 

---

### <a name="automatic-sign-in-field-detection"></a>Wykrywanie automatyczne logowanie pola


**Typ:** stałej   
**Kategoria usług:** Moje aplikacje  
**Możliwości produktu:** logowanie jednokrotne  



Usługi Azure AD obsługuje wykrywanie pola automatycznego logowania dla aplikacji, które renderują pola nazwy i hasła użytkownika HTML. Te kroki są udokumentowane w artykule [automatycznie Przechwytywanie pola logowania dla aplikacji](https://docs.microsoft.com/azure/active-directory/application-config-sso-problem-configure-password-sso-non-gallery#how-to-manually-capture-sign-in-fields-for-an-application). Tej funkcji można znaleźć, dodając *Non-galerii* aplikację na **aplikacje dla przedsiębiorstw** strony [portalu Azure](http://aad.portal.azure.com). Ponadto można skonfigurować **rejestracji jednokrotnej** tryb tej nowej aplikacji do **opartego na hasłach rejestracji jednokrotnej**, wprowadź adres URL sieci web, a następnie Zapisz strony.
 
Ze względu na problem dotyczący usługi został tymczasowo wyłączyć tej funkcji. Problem został rozwiązany i wykrywanie automatyczne logowanie pole jest dostępne ponownie.

---

### <a name="new-multi-factor-authentication-features"></a>Nowe funkcje usługi Multi-Factor authentication


**Typ:** nowej funkcji  
**Kategoria usług:** uwierzytelnianie wieloskładnikowe  
**Możliwości produktu:** tożsamości zabezpieczenia i ochrona  



Uwierzytelnianie wieloskładnikowe (MFA) jest integralną część ochrony Twojej organizacji. Aby bardziej adaptacyjną poświadczeń i doświadczenia płynniejszą, dodano następujące funkcje: 

- Wyniki żądania wieloskładnikowego bezpośrednio są zintegrowane usługi Azure AD logowania w raporcie, który obejmuje programowy dostęp do usługi MFA wyników.
- Konfiguracja uwierzytelniania Wieloskładnikowego głębsze jest zintegrowana w konfiguracji usługi Azure AD doświadczenie w portalu Azure.

Z publicznej wersji zapoznawczej MFA zarządzania i raportowania są integralną częścią środowisko konfiguracji podstawowej usługi Azure AD. Teraz można zarządzać funkcje portalu zarządzania usługi MFA w ramach obsługi usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [dokumentacja dotycząca raportowania usługi MFA w portalu Azure](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 


---

### <a name="terms-of-use"></a>Warunki użytkowania



**Typ:** nowej funkcji  
**Kategoria usług:** warunki użytkowania  
**Możliwości produktu:** ładu/zgodności  



Możesz użyć usługi Azure AD warunki użytkowania do prezentowania informacje, takie jak zastrzeżenia odpowiednie wymagania prawne lub zgodności dla użytkowników.

Warunki użytkowania usługi Azure AD można użyć w następujących scenariuszach:

- Ogólne warunki użytkowania dla wszystkich użytkowników w organizacji
- Określone warunki użytkowania na podstawie atrybutów użytkownika (na przykład lekarzy a pielęgniarek) lub domowych a międzynarodowych pracowników, programach grup dynamicznych
- Dostęp do aplikacji biznesowych o dużym znaczeniu, takie jak Salesforce określone warunki użytkowania

Aby uzyskać więcej informacji, zobacz [warunki użytkowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-tou).


---

### <a name="enhancements-to-privileged-identity-management"></a>Ulepszenia Privileged Identity Management


**Typ:** nowej funkcji  
**Kategoria usług:** Privileged Identity Management  
**Możliwości produktu:** Privileged Identity Management  


Azure AD Privileged Identity Management możesz zarządzanie, sterowanie i monitorowanie dostępu do zasobów platformy Azure (wersja zapoznawcza) w Twojej organizacji, aby:

- Subskrypcje
- Grupy zasobów
- Maszyny wirtualne 

Wszystkie zasoby w portalu Azure, korzystających z funkcji Azure RBAC można korzystać z zabezpieczeń i możliwości zarządzania cyklem życia dostępnych Azure AD Privileged Identity Management.

Aby uzyskać więcej informacji, zobacz [Privileged Identity Management zasobów Azure](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).


---

### <a name="access-reviews"></a>Przeglądy dostępu


**Typ:** nowej funkcji  
**Kategoria usług:** dostęp do przeglądu  
**Możliwości produktu:** ładu/zgodności  



Organizacje umożliwia efektywne zarządzanie członkostwa w grupach i uzyskiwania dostępu do aplikacji przedsiębiorstwa przeglądami dostęp (wersja zapoznawcza): 

- Przeprowadzając przeglądy dostępu, można ponownie certyfikować dostęp użytkowników-gości do aplikacji i ich członkostwo w grupach. Osoby dokonujące przeglądu wydajnie można zdecydować, czy gościom stały dostęp oparte na szczegółowych danych dostarczonych przez przeglądy dostępu.
- Przeprowadzając przeglądy dostępu, można ponownie certyfikować dostęp do aplikacji i członkostwo w grupach pracowników.

Kontrole z przeglądem dostępu można łączyć w programy odpowiednie dla danej organizacji, aby śledzić przeglądy dla aplikacji dotyczących zgodności lub aplikacji narażonych na ryzyko.

Aby uzyskać więcej informacji, zobacz [sprawdza dostęp do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).


---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Ukryj aplikacje innych firm Moje aplikacje i uruchamiania aplikacji usługi Office 365



**Typ:** nowej funkcji  
**Kategoria usług:** Moje aplikacje  
**Możliwości produktu:** logowanie jednokrotne  



Teraz można lepiej zarządzać aplikacje, które są widoczne dla portali użytkowników za pomocą nowej **Ukryj aplikacji** właściwości. Aplikacje, aby pomóc w przypadkach, gdy kafelków aplikacji wyświetlane dla usług zaplecza lub zduplikowanych fragmentów i przyciski Uruchom aplikacji użytkownicy bałaganu można ukryć. Przełącznik jest **właściwości** części aplikacji innych producentów i etykietą **widoczny dla użytkownika?** Można również ukryć aplikacji programowo przy użyciu programu PowerShell. 

Aby uzyskać więcej informacji, zobacz [ukrywanie aplikacji innych producentów, od środowiska użytkownika w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Co to jest dostępny?**

 W ramach przejścia na Nowa Konsola administracyjna dwóch nowych interfejsów API do pobierania działania usługi Azure AD dzienniki są dostępne. Nowy zestaw interfejsów API zapewnia bardziej zaawansowane funkcje filtrowania i sortowania funkcje oprócz bardziej zaawansowane funkcje inspekcji i rejestrowania działania. Wcześniej dostępne za pośrednictwem zabezpieczeń raporty teraz danych jest możliwy za pośrednictwem tożsamości zagrożenie zdarzenia interfejsu API ochrony w programie Microsoft Graph.


## <a name="september-2017"></a>2017 września

### <a name="hotfix-for-identity-manager"></a>Poprawka dla programu Identity Manager


**Typ:** funkcji zmienione  
**Kategoria usług:** Identity Manager  
**Możliwości produktu:** zarządzania cyklem życia tożsamości  



Pakiet zbiorczy poprawek (kompilacja 4.4.1642.0) jest dostępny od 25 września 2017 dla dodatku Service Pack 1 dla programu Identity Manager 2016. Ten pakiet zbiorczy:

- Rozwiązuje problemy i dodaje ulepszenia.
- Jest aktualizacją zbiorczą, który zastępuje wszystkie aktualizacje dodatku Service Pack 1 dla programu Identity Manager 2016 do kompilacji 4.4.1459.0 programu Identity Manager 2016. 
- Wymagane jest posiadanie Identity Manager 2016 4.4.1302.0 kompilacji. 

Aby uzyskać więcej informacji, zobacz [pakiet zbiorczy poprawek (kompilacja 4.4.1642.0) jest dostępna dla dodatku Service Pack 1 dla programu Identity Manager 2016](https://support.microsoft.com/help/4021562). 

---
