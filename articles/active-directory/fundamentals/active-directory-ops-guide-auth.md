---
title: Przewodnik dotyczący operacji zarządzania uwierzytelnianiem Azure Active Directory
description: W tym przewodniku odwołuje się opis operacji sprawdzania i działań, które należy wykonać w celu zabezpieczenia zarządzania uwierzytelnianiem
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 85281088692d1c4b0245eb9d069519198f8f315d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919345"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Przewodnik dotyczący operacji zarządzania uwierzytelnianiem Azure Active Directory

W tej części [przewodnika dotyczącego odwołań usługi Azure AD](active-directory-ops-guide-intro.md) opisano testy i akcje, które należy wykonać w celu zabezpieczenia i zarządzania poświadczeniami, Definiowanie środowiska uwierzytelniania, przypisywanie delegatów, użycie miar i definiowanie zasad dostępu w oparciu o stan zabezpieczeń przedsiębiorstwa.

> [!NOTE]
> Te zalecenia są aktualne w dniu opublikowania, ale mogą ulec zmianie w czasie. Organizacje powinny stale sprawdzać swoją tożsamość jako produkty i usługi firmy Microsoft, które są roznoszone wraz z upływem czasu.

## <a name="key-operational-processes"></a>Najważniejsze procesy operacyjne

### <a name="assign-owners-to-key-tasks"></a>Przypisywanie właścicieli do kluczowych zadań

Zarządzanie Azure Active Directory wymaga ciągłego wykonywania kluczowych zadań operacyjnych i procesów, które mogą nie być częścią projektu wdrożenia. Nadal ważne jest, aby skonfigurować te zadania w celu zoptymalizowania środowiska. Najważniejsze zadania i ich zalecani właściciele obejmują:

| Zadanie | Właściciel |
| :- | :- |
| Zarządzanie cyklem życia konfiguracji logowania jednokrotnego (SSO) w usłudze Azure AD | Zespół operacji IAM |
| Projektowanie zasad dostępu warunkowego dla aplikacji usługi Azure AD | Zespół architektury InfoSec |
| Archiwizowanie aktywności logowania w systemie SIEM | Zespół ds. operacji InfoSec |
| Archiwizowanie zdarzeń ryzyka w systemie SIEM | Zespół ds. operacji InfoSec |
| Klasyfikacja i zbadaj raporty zabezpieczeń | Zespół ds. operacji InfoSec |
| Klasyfikacja i zbadaj zdarzenia ryzyka | Zespół ds. operacji InfoSec |
| Klasyfikacja i zbadaj użytkowników oflagowanych pod kątem ryzyka i luk w zabezpieczeniach z Azure AD Identity Protection | Zespół ds. operacji InfoSec |

> [!NOTE]
> Azure AD Identity Protection wymaga licencji na Azure AD — wersja Premium P2. Aby znaleźć odpowiednią licencję dla wymagań, zobacz [porównanie ogólnie dostępnych funkcji wersji Azure AD — wersja bezpłatna i Azure AD — wersja Premium](https://azure.microsoft.com/pricing/details/active-directory/).

Podczas przeglądania listy może być konieczne przypisanie właściciela do zadań, w których brakuje właściciela, lub dostosować własność do zadań z właścicielami, które nie są wyrównane do powyższych zaleceń.

#### <a name="owner-recommended-reading"></a>Odczytywanie zalecanego przez właściciela

- [Przypisywanie ról administratorów w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Nadzór na platformie Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="credentials-management"></a>Zarządzanie poświadczeniami

### <a name="password-policies"></a>Zasady dotyczące haseł

Bezpieczne zarządzanie hasłami jest jednym z najważniejszych części zarządzania tożsamościami i dostępem oraz często największym celem ataków. Usługa Azure AD obsługuje kilka funkcji, które mogą pomóc zapobiec pomyślnym ataku.

Skorzystaj z poniższej tabeli, aby znaleźć zalecane rozwiązanie do ograniczania problemu, który należy rozwiązać:

| Problem | Zalecenie |
| :- | :- |
| Brak mechanizmu ochrony przed słabymi hasłami | Włącz samoobsługowe [Resetowanie hasła (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) usługi Azure AD i [ochronę hasłem](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) |
| Brak mechanizmu wykrywania nieujawnionych haseł | Włącz funkcję [synchronizacji skrótów haseł](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS) w celu uzyskania szczegółowych informacji |
| Używanie AD FS i nie można przenieść do uwierzytelniania zarządzanego | Włącz [AD FS ekstranetu inteligentnego blokowania](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) i/lub [inteligentnego blokowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout) |
| Zasady haseł używają reguł opartych na dużej złożoności, takich jak długość, wiele zestawów znaków lub wygaśnięcie | Zapoznaj się z [zaleceniami zalecanymi przez firmę Microsoft](https://aka.ms/passwordguidance) i Zmień podejście do zarządzania hasłami i Wdróż [ochronę hasłem usługi Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad). |
| Użytkownicy nie są zarejestrowani do korzystania z uwierzytelniania wieloskładnikowego (MFA) | [Zarejestruj informacje o zabezpieczeniach wszystkich użytkowników](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-mfa-policy) , aby można było ich używać jako mechanizmu do weryfikowania tożsamości użytkownika wraz z ich hasłem |
| Nie istnieje odwołanie hasła na podstawie ryzyka użytkownika | Wdrażanie [zasad ryzyka dla użytkowników](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy) usługi Azure AD Identity Protection w celu wymuszenia zmiany hasła dla przecieków poświadczeń przy użyciu SSPR |
| Nie istnieje mechanizm inteligentnego blokowania, aby chronić złośliwe uwierzytelnianie od nieprawidłowych uczestników pochodzących ze zidentyfikowanych adresów IP | Wdrażanie uwierzytelniania zarządzanego przez chmurę z użyciem synchronizacji skrótów haseł lub [uwierzytelniania przekazywanego](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA) |

#### <a name="password-policies-recommended-reading"></a>Zalecane odczytywanie zasad haseł

- [Najlepsze rozwiązania dotyczące usługi Azure AD i AD FS: obrona przed atakami polegającymi na rozpylaniu hasła — Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Włącz Samoobsługowe resetowanie haseł i ochronę hasłem

Użytkownicy, którzy chcą zmieniać lub resetować swoje hasła, są jednym z największych źródeł ilości i kosztów wywołań pomocy technicznej. Oprócz kosztów, zmiana hasła jako narzędzia służącego do ograniczenia ryzyka użytkownika to podstawowy etap ulepszania stan zabezpieczeń organizacji.

Zaleca się wdrożenie [funkcji samoobsługowego resetowania haseł](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) (SSPR) usługi Azure AD oraz [ochronę hasłem](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) lokalnym w celu osiągnięcia:

- Odkształcenie połączeń pomocy technicznej.
- Zastąp użycie haseł tymczasowych.
- Zastąp wszelkie istniejące rozwiązanie do samoobsługowego zarządzania hasłami, które opiera się na rozwiązaniu lokalnym.
- [Eliminowanie słabych haseł](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) w organizacji.

> [!NOTE]
> W przypadku organizacji z subskrypcją Azure AD — wersja Premium P2 zaleca się wdrożenie SSPR i użycie jej w ramach [zasad ryzyka użytkownika ochrony tożsamości](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy).

### <a name="strong-credential-management"></a>Silne zarządzanie poświadczeniami

Hasła nie są wystarczająco bezpieczne, aby zapobiec uzyskiwaniu dostępu do środowiska przez złe podmioty. Na potrzeby uwierzytelniania wieloskładnikowego (MFA) musi być włączony co najmniej każdy użytkownik z kontem uprzywilejowanym. W idealnym przypadku należy włączyć [rejestrację łączną](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined) i wymagać od wszystkich użytkowników rejestracji usługi MFA i SSPR przy użyciu [połączonego środowiska rejestracji](https://docs.microsoft.com/azure/active-directory/user-help/user-help-security-info-overview). Na koniec zalecamy stosowanie strategii w celu [zapewnienia odporności](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls) na zmniejszenie ryzyka blokady z powodu nieprzewidzianych okoliczności.

![Przepływ pracy połączonej użytkownika](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Odporność uwierzytelniania w przypadku awarii lokalnej

Oprócz korzyści z uproszczenia i włączenia wykrywania nieujawnionych poświadczeń, synchronizacja skrótów haseł w usłudze Azure AD (PHS) i usługa Azure MFA umożliwiają użytkownikom dostęp do aplikacji SaaS i pakietu Office 365 w przypadku awarii w środowisku lokalnym z powodu cyberattacks, takich jak [NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/). Możliwe jest również włączenie PHS w połączeniu z Federacją. Włączenie PHS umożliwia rezerwowe uwierzytelnianie, gdy usługi federacyjne nie są dostępne.

Jeśli Twoja organizacja lokalna nie ma strategii odporności na awarie lub ma taką, która nie jest zintegrowana z usługą Azure AD, należy wdrożyć usługę Azure AD PHS i zdefiniować plan odzyskiwania po awarii, który obejmuje PHS. Włączenie usługi Azure AD PHS umożliwi użytkownikom uwierzytelnianie w usłudze Azure AD, jeśli lokalne Active Directory będą niedostępne.

![przepływ synchronizacji skrótów haseł](./media/active-directory-ops-guide/active-directory-ops-img5.png)

Aby lepiej zrozumieć opcje uwierzytelniania, zobacz [Wybieranie odpowiedniej metody uwierzytelniania dla Azure Active Directory rozwiązanie do tworzenia tożsamości hybrydowej](https://docs.microsoft.com/azure/security/azure-ad-choose-authn).

### <a name="programmatic-usage-of-credentials"></a>Programowe użycie poświadczeń

Skrypty usługi Azure AD korzystające z programu PowerShell lub aplikacji przy użyciu interfejs API programu Graph wymagają bezpiecznego uwierzytelniania. Słabe zarządzanie poświadczeniami wykonuje te skrypty i narzędzia zwiększają ryzyko kradzieży poświadczeń. Jeśli używasz skryptów lub aplikacji korzystających z haseł zakodowanych lub monitów o hasło, należy najpierw przejrzeć hasła w plikach konfiguracyjnych lub kodzie źródłowym, zastąpić te zależności i korzystać z tożsamości zarządzanych przez platformę Azure, zintegrowanego uwierzytelniania systemu Windows lub [certyfikatów](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-access-api-with-certificates) wszędzie tam, gdzie to możliwe. W przypadku aplikacji, w których poprzednie rozwiązania nie są możliwe, należy rozważyć użycie [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Jeśli okaże się, że istnieją jednostki usługi z poświadczeniami hasła i nie masz pewności, jak te poświadczenia hasła są zabezpieczone przez skrypty lub aplikacje, skontaktuj się z właścicielem aplikacji, aby lepiej zrozumieć wzorce użycia.

Firma Microsoft zaleca również kontaktowanie się z właścicielami aplikacji w celu zrozumienia wzorców użycia, jeśli istnieją jednostki usługi z poświadczeniami hasła.

## <a name="authentication-experience"></a>Środowisko uwierzytelniania

### <a name="on-premises-authentication"></a>Uwierzytelnianie lokalne

Uwierzytelnianie federacyjne ze zintegrowanym uwierzytelnianiem systemu Windows (IWA) lub bezproblemowe uwierzytelnianie logowania jednokrotnego przy użyciu funkcji synchronizacji skrótów haseł lub uwierzytelniania przekazywanego to najlepsze środowisko użytkownika w sieci firmowej z Szczegółowe informacje na temat lokalnych kontrolerów domeny. Minimalizuje to zmęczenie i zmniejsza ryzyko związane z atakami z wykorzystaniem wyłudzania informacji przez użytkowników Prey. Jeśli używasz już uwierzytelniania zarządzanego przez chmurę z PHS lub PTA, ale użytkownicy nadal muszą wpisać swoje hasło podczas uwierzytelniania lokalnego, należy natychmiast [wdrożyć bezproblemową rejestrację jednokrotną](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso). Z drugiej strony, jeśli obecnie są federacyjne z planami, aby ostatecznie migrować do uwierzytelniania zarządzanego przez chmurę, należy zaimplementować bezproblemowe logowanie jednokrotne w ramach projektu migracji.

### <a name="device-trust-access-policies"></a>Zasady dostępu zaufania urządzeń

Podobnie jak w przypadku użytkownika w organizacji, urządzenie to podstawowa tożsamość, która ma być chroniona. Możesz użyć tożsamości urządzenia do ochrony zasobów w dowolnym momencie i z dowolnej lokalizacji. Uwierzytelnianie urządzenia i ich typ zaufania zwiększa stan zabezpieczeń i użyteczność dzięki:

- Unikanie tarcia, na przykład z uwierzytelnianiem MFA, gdy urządzenie jest zaufane
- Blokowanie dostępu z niezaufanych urządzeń
- W przypadku urządzeń z systemem Windows 10 bezpośrednie [Logowanie do zasobów lokalnych jest bezproblemowo](https://docs.microsoft.com/azure/active-directory/devices/azuread-join-sso).

W tym celu można przenieść tożsamości urządzeń i zarządzać nimi w usłudze Azure AD za pomocą jednej z następujących metod:

- Organizacje mogą używać [Microsoft Intune](https://docs.microsoft.com/intune/what-is-intune) do zarządzania urządzeniem i wymuszania zasad zgodności, zaświadczania kondycji urządzenia i ustawienia zasad dostępu warunkowego w zależności od tego, czy urządzenie jest zgodne. Microsoft Intune mogą zarządzać urządzeniami z systemem iOS, komputerami Mac (za pośrednictwem integracji JAMF), komputerami stacjonarnymi z systemem Windows (natywnie przy użyciu zarządzania urządzeniami przenośnymi w systemie Windows 10 i współzarządzaniem z programem Microsoft Endpoint Manager/System Center Configuration Manager) i urządzeniami przenośnymi z systemem Android.
- [Sprzężenie hybrydowe usługi Azure AD](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-managed-domains) zapewnia zarządzanie przy użyciu zasad grupy, System Center Configuration Manager lub programu Microsoft Endpoint Manager w środowisku z Active Directory urządzeniami przyłączonymi do domeny. Organizacje mogą wdrażać środowisko zarządzane za pomocą PHS lub PTA z bezproblemowym logowaniem jednokrotnym. Przełączenie urządzeń do usługi Azure AD maksymalizuje produktywność użytkowników przez logowanie jednokrotne w chmurze i zasobach lokalnych, umożliwiając jednocześnie bezpieczny dostęp do zasobów w chmurze i lokalnych przy użyciu  [dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) .

W przypadku urządzeń z systemem Windows przyłączonych do domeny, które nie są zarejestrowane w chmurze, lub urządzeń z systemem Windows przyłączonych do domeny, które są zarejestrowane w chmurze, ale bez zasad dostępu warunkowego, należy zarejestrować niezarejestrowane urządzenia i w obu przypadkach [używać sprzężenia hybrydowego usługi Azure AD jako formantu](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) w zasadach dostępu warunkowego.

![Zrzut ekranu przedstawiający udzielenie zasad dostępu warunkowego wymagający urządzenia hybrydowego](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Jeśli zarządzasz urządzeniami za pomocą rozwiązania MDM lub Microsoft Intune, ale nie korzystasz z formantów urządzeń w zasadach dostępu warunkowego, zalecamy użycie opcji [Wymagaj, aby urządzenie było oznaczone jako zgodne](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices#require-device-to-be-marked-as-compliant) jako kontrolka w tych zasadach.

![Zrzut ekranu przydzielenia w zasadach dostępu warunkowego wymagający zgodności urządzenia](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Zalecane odczytywanie zasad dostępu zaufania urządzeń

- [Instrukcje: planowanie implementacji dołączania hybrydowego Azure Active Directory](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
- [Konfiguracje dostępu tożsamości i urządzenia](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello dla firm

W systemie Windows 10 funkcja [Windows Hello dla firm](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) zastępuje hasła silnym uwierzytelnianiem dwuskładnikowym na komputerach. Funkcja Windows Hello dla firm umożliwia korzystanie z bardziej usprawnień usługi MFA dla użytkowników i zmniejsza zależność od haseł. Jeśli nie rozpoczęto wdrażania urządzeń z systemem Windows 10 lub zostały one tylko częściowo wdrożone, zalecamy uaktualnienie do systemu Windows 10 i [włączenie usługi Windows Hello dla firm](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) na wszystkich urządzeniach.

Jeśli chcesz dowiedzieć się więcej o uwierzytelnianiu bez hasła, zapoznaj się z artykułem [na świecie, w którym nie ma hasła Azure Active Directory](https://aka.ms/passwordlessdoc).

## <a name="application-authentication-and-assignment"></a>Uwierzytelnianie i przypisywanie aplikacji

### <a name="single-sign-on-for-apps"></a>Logowanie jednokrotne dla aplikacji

Zapewnienie standardowego mechanizmu logowania jednokrotnego do całego przedsiębiorstwa ma kluczowe znaczenie dla najlepszego środowiska użytkownika, zmniejszenia ryzyka, możliwości zgłaszania i zarządzania. Jeśli używasz aplikacji obsługujących Logowanie jednokrotne w usłudze Azure AD, ale obecnie są one skonfigurowane do korzystania z kont lokalnych, należy ponownie skonfigurować te aplikacje do korzystania z logowania jednokrotnego w usłudze Azure AD. Podobnie, jeśli korzystasz z aplikacji, które obsługują Logowanie jednokrotne w usłudze Azure AD, ale korzystasz z innego dostawcy tożsamości, należy ponownie skonfigurować te aplikacje do korzystania z logowania jednokrotnego w usłudze Azure AD. W przypadku aplikacji, które nie obsługują protokołów federacyjnych, ale obsługują uwierzytelnianie oparte na formularzach, zalecamy skonfigurowanie aplikacji w taki sposób, aby korzystała z funkcji [magazynowania haseł](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-password-vaulting) w usłudze Azure serwer proxy aplikacji usługi Azure AD.

![Logowanie oparte na hasłach AppProxy](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Jeśli nie masz mechanizmu odnajdywania niezarządzanych aplikacji w organizacji, zalecamy wdrożenie procesu odnajdywania przy użyciu rozwiązania brokera zabezpieczeń dostępu w chmurze (CASB), takiego jak [Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security).

Jeśli masz galerię aplikacji usługi Azure AD i używasz aplikacji, które obsługują Logowanie jednokrotne w usłudze Azure AD, zalecamy [wyświetlanie aplikacji w galerii aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing).

#### <a name="single-sign-on-recommended-reading"></a>Odczyt zalecający Logowanie jednokrotne

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Migracja aplikacji AD FS do usługi Azure AD

[Migrowanie aplikacji z AD FS do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure) umożliwia korzystanie z dodatkowych funkcji zabezpieczeń, bardziej spójnego zarządzania i lepszego środowiska współpracy. Jeśli masz skonfigurowane aplikacje w AD FS, które obsługują Logowanie jednokrotne w usłudze Azure AD, należy ponownie skonfigurować te aplikacje do korzystania z logowania jednokrotnego w usłudze Azure AD. Jeśli masz aplikacje skonfigurowane w AD FS z nietypowymi konfiguracjami nieobsługiwanymi przez usługę Azure AD, skontaktuj się z właścicielami aplikacji, aby dowiedzieć się, czy specjalna konfiguracja jest bezwzględnym wymaganiem aplikacji. Jeśli nie jest to wymagane, należy ponownie skonfigurować aplikację do korzystania z logowania jednokrotnego w usłudze Azure AD.

![Usługa Azure AD jako podstawowy dostawca tożsamości](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [Azure AD Connect Health dla usług AD FS](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs) może służyć do zbierania szczegółowych informacji konfiguracyjnych dotyczących każdej aplikacji, która może zostać poddana migracji do usługi Azure AD.

### <a name="assign-users-to-applications"></a>Przypisywanie użytkowników do aplikacji

[Przypisywanie użytkowników do aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups) najlepiej jest zamapowane przy użyciu grup, ponieważ umożliwiają one większą elastyczność i możliwość zarządzania w odpowiedniej skali. Korzyści wynikające z używania grup obejmują [dynamiczne członkostwo w grupach](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership) i [delegowanie do właścicieli aplikacji](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-accessmanagement-managing-group-owners). W związku z tym, jeśli już używasz grup i zarządzasz nimi, zalecamy wykonanie następujących czynności w celu usprawnienia zarządzania w odpowiedniej skali:

- Delegowanie zarządzania grupami i nadzoru do właścicieli aplikacji.
- Zezwalaj na samoobsługowe dostęp do aplikacji.
- Zdefiniuj grupy dynamiczne, jeśli atrybuty użytkownika mogą spójnie określić dostęp do aplikacji.
- Zaimplementuj zaświadczenie grupom używanym do uzyskiwania dostępu do aplikacji za pomocą [przeglądów dostępu do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview).

Z drugiej strony, jeśli znajdziesz aplikacje, które mają przypisanie do poszczególnych użytkowników, pamiętaj, aby zaimplementować [Zarządzanie](https://docs.microsoft.com/azure/active-directory/governance/index) tymi aplikacjami.

#### <a name="assign-users-to-applications-recommended-reading"></a>Przypisywanie użytkowników do zalecanych odczytów

- [Przypisywanie użytkowników i grup do aplikacji w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)
- [Delegowanie uprawnień rejestracji aplikacji w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-app-roles)
- [Reguły członkostwa dynamicznego dla grup w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

## <a name="access-policies"></a>Zasady dostępu

### <a name="named-locations"></a>Nazwane lokalizacje

Z [nazwanymi lokalizacjami](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) w usłudze Azure AD można oznaczyć zaufane zakresy adresów IP w organizacji. Usługa Azure AD używa nazwanych lokalizacji w następujących celach:

- Zapobiegaj zwracaniu fałszywych zdarzeń o podwyższonym ryzyku. Zalogowanie się z zaufanej lokalizacji sieciowej zmniejsza ryzyko związane z logowaniem użytkownika.
- Konfigurowanie [dostępu warunkowego opartego na lokalizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations).

![Nazwana lokalizacja](./media/active-directory-ops-guide/active-directory-ops-img10.png)

Na podstawie priorytetu Skorzystaj z poniższej tabeli, aby znaleźć zalecane rozwiązanie, które najlepiej spełnia wymagania organizacji:

| **Priorytet** | **Scenariusz** | **Zalecenie** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Jeśli używasz PHS lub PTA i nazwane lokalizacje nie zostały zdefiniowane | Definiowanie nazwanych lokalizacji w celu usprawnienia wykrywania zdarzeń o podwyższonym ryzyku |
| 2 | Jeśli jesteś federacyjnym i nie używasz żądania "insideCorporateNetwork" i nazwanych lokalizacji nie zostały zdefiniowane | Definiowanie nazwanych lokalizacji w celu usprawnienia wykrywania zdarzeń o podwyższonym ryzyku |
| 3 | Jeśli nie używasz nazwanych lokalizacji w zasadach dostępu warunkowego i nie ma żadnych elementów w zasadach dostępu warunkowego | Konfigurowanie zasad dostępu warunkowego w celu uwzględnienia nazwanych lokalizacji |
| 4 | Jeśli jesteś federacyjnym i używasz żądania "insideCorporateNetwork" i nazwanych lokalizacji nie zostały zdefiniowane | Definiowanie nazwanych lokalizacji w celu usprawnienia wykrywania zdarzeń o podwyższonym ryzyku |
| 5 | Jeśli używasz zaufanych adresów IP z uwierzytelnianiem MFA zamiast nazwanych lokalizacji i oznaczasz je jako zaufane | Zdefiniuj nazwane lokalizacje i oznacz je jako zaufane, aby poprawić wykrywanie zdarzeń ryzyka |

### <a name="risk-based-access-policies"></a>Zasady dostępu oparte na ryzyku

Usługa Azure AD może obliczyć ryzyko związane z każdym logowaniem i każdym użytkownikiem. Użycie ryzyka jako kryterium w zasadach dostępu może zapewnić lepszy interfejs użytkownika, na przykład mniejszą liczbę monitów uwierzytelniania i lepsze zabezpieczenia, na przykład Monituj użytkowników tylko wtedy, gdy są one używane, i automatyzując odpowiedzi i korekty.

![Zasady dotyczące ryzyka związanego z logowaniem](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Jeśli masz Azure AD — wersja Premium już stan licencje P2, które obsługują korzystanie z ryzyka w zasadach dostępu, ale nie są używane, zdecydowanie zalecamy dodanie ryzyka do zabezpieczeń.

#### <a name="risk-based-access-policies-recommended-reading"></a>Zalecane odczytywanie zasad dostępu opartych na ryzyku

- [Instrukcje: Konfigurowanie zasad dotyczących ryzyka związanego z logowaniem](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)
- [Instrukcje: Konfigurowanie zasad ryzyka dla użytkowników](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)

### <a name="client-application-access-policies"></a>Zasady dostępu aplikacji klienta

Microsoft Intune Zarządzanie aplikacjami (MAM) zapewnia możliwość wypychania kontroli ochrony danych, takich jak szyfrowanie magazynu, kod PIN, czyszczenie zdalnego magazynu itp. do zgodnych aplikacji mobilnych klienta, takich jak Outlook Mobile. Ponadto można utworzyć zasady dostępu warunkowego, aby [ograniczyć dostęp](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) do usług w chmurze, takich jak Exchange Online, z zatwierdzonych lub zgodnych aplikacji.

Jeśli pracownicy instalują aplikacje obsługujące MAM, takie jak aplikacje mobilne pakietu Office, aby uzyskiwać dostęp do zasobów firmy, takich jak usługa Exchange Online lub SharePoint Online, a także BYOD (korzystać z własnych urządzeń), zalecamy wdrożenie zasad MAM aplikacji do zarządzania Konfiguracja aplikacji na urządzeniach osobistych bez rejestracji w usłudze MDM, a następnie aktualizacja zasad dostępu warunkowego w taki sposób, aby zezwalały na dostęp z MAM klientów.

![Kontrola dostępu warunkowego](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Jeśli pracownicy będą instalować aplikacje obsługujące MAM z zasobami firmowymi, a dostęp do nich jest ograniczony na urządzeniach zarządzanych przez usługę Intune, należy rozważyć wdrożenie zasad usługi Application MAM w celu zarządzania konfiguracją aplikacji dla urządzeń osobistych. Zaktualizuj zasady dostępu warunkowego, aby zezwalać na dostęp tylko z klientów obsługujących MAM.

### <a name="conditional-access-implementation"></a>Implementacja dostępu warunkowego

Dostęp warunkowy jest ważnym narzędziem do ulepszania stan zabezpieczeń organizacji. W związku z tym ważne są następujące najlepsze rozwiązania:

- Upewnij się, że wszystkie aplikacje SaaS mają zastosowaną co najmniej jedną zasadę
- Należy unikać łączenia filtru **wszystkie aplikacje** z kontrolką **bloku** , aby uniknąć ryzyka związanego z blokowaniem
- Unikaj używania **wszystkich użytkowników** jako filtru i przypadkowo Dodaj **Gości**
- **Przeprowadź migrację wszystkich "starszych" zasad do Azure Portal**
- Przechwyć wszystkie kryteria dla użytkowników, urządzeń i aplikacji
- Korzystanie z zasad dostępu warunkowego w celu [zaimplementowania usługi MFA](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)zamiast korzystania z usługi **MFA dla poszczególnych użytkowników**
- Mają niewielki zestaw podstawowych zasad, które mogą być stosowane do wielu aplikacji
- Zdefiniuj puste grupy wyjątków i Dodaj je do zasad, aby uzyskać strategię wyjątku
- Planowanie kont [szkła z podziałem](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency) bez kontroli MFA
- Zapewnij spójne środowisko aplikacji klienckich pakietu Office 365, na przykład zespoły, OneDrive dla firm, Outlook itd.) przez implementację tego samego zestawu formantów dla usług, takich jak Exchange Online i SharePoint Online
- Przypisanie do zasad powinno być implementowane za poorednictwem grup, a nie użytkowników
- Wykonaj regularne przeglądy grup wyjątków używanych w zasadach, aby ograniczyć czas, w którym użytkownicy znajdują się w stan zabezpieczeń. Jeśli jesteś właocicielem usługi Azure AD P2, możesz użyć przeglądów dostępu do zautomatyzowania procesu

#### <a name="conditional-access-recommended-reading"></a>Zalecane odczytywanie dostępu warunkowego

- [Najlepsze rozwiązania dotyczące dostępu warunkowego w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)
- [Konfiguracje dostępu tożsamości i urządzenia](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Azure Active Directory informacje o ustawieniach dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference)
- [Typowe zasady dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)

## <a name="access-surface-area"></a>Obszar powierzchni dostępu

### <a name="legacy-authentication"></a>Starsza wersja uwierzytelniania

Silne poświadczenia, takie jak MFA, nie mogą chronić aplikacji przy użyciu starszych protokołów uwierzytelniania, co sprawia, że jest to preferowany wektor ataków złośliwych podmiotów. Zablokowanie starszego uwierzytelniania ma kluczowe znaczenie dla poprawy zabezpieczeń dostępu stan.

Starsza wersja uwierzytelniania to termin, który odnosi się do protokołów uwierzytelniania używanych przez aplikacje takie jak:

- Starsi klienci pakietu Office, którzy nie korzystają z nowoczesnego uwierzytelniania (na przykład klient pakietu Office 2010)
- Klienci używający protokołów poczty, takich jak IMAP/SMTP/POP

Osoby atakujące zdecydowanie preferują te protokoły — w rzeczywistości niemal [100% ataki z rozpylaczem hasła korzystają ze](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) starszych protokołów uwierzytelniania. Hakerzy korzystają ze starszych protokołów uwierzytelniania, ponieważ nie obsługują logowania interaktywnego, co jest potrzebne do dodatkowych wyzwań związanych z zabezpieczeniami, takich jak uwierzytelnianie wieloskładnikowe i uwierzytelnianie urządzeń.

Jeśli starsze uwierzytelnianie jest powszechnie używane w danym środowisku, należy zaplanować migrację starszych klientów do klientów obsługujących [nowoczesne uwierzytelnianie](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) tak szybko, jak to możliwe. W tym samym tokenie, jeśli niektórzy użytkownicy już używają nowoczesnego uwierzytelniania, ale inni nadal korzystają ze starszego uwierzytelniania, należy wykonać następujące czynności, aby zablokować klientów starszego uwierzytelniania:

1. Używaj [raportów działań związanych z logowaniem](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) , aby identyfikować użytkowników, którzy nadal korzystają ze starszej wersji uwierzytelniania i planowania korygowania:

   a. Uaktualnij do nowoczesnych klientów z możliwością uwierzytelniania do odpowiednich użytkowników.
   
   b. Zaplanuj przedział czasu uruchomienie produkcyjne, aby zablokować na kolejne kroki.
   
   d. Zidentyfikuj starsze aplikacje mające twardą zależność od starszego uwierzytelniania. Zobacz Krok 3 poniżej.

2. Wyłącz starsze protokoły w źródle (na przykład skrzynek pocztowych programu Exchange) dla użytkowników, którzy nie korzystają ze starszej wersji uwierzytelniania, aby uniknąć większej ekspozycji.
3. W przypadku pozostałych kont (najlepiej związanych z tożsamościami nieludzkimi, takimi jak konta usług) Użyj [dostępu warunkowego, aby ograniczyć starsze protokoły](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) po uwierzytelnieniu.

#### <a name="legacy-authentication-recommended-reading"></a>Zalecane odczytywanie starszego uwierzytelniania

- [Włączanie lub wyłączanie dostępu POP3 lub IMAP4 do skrzynek pocztowych w programie Exchange Server](https://docs.microsoft.com/exchange/clients/pop3-and-imap4/configure-mailbox-access?view=exchserver-2019)

### <a name="consent-grants"></a>Udzielanie zgody

W przypadku ataku z nielegalną zgodą osoba atakująca tworzy aplikację zarejestrowaną w usłudze Azure AD, która żąda dostępu do danych, takich jak informacje kontaktowe, poczta e-mail lub dokumenty. Użytkownicy mogą udzielać zgody na złośliwe aplikacje za pośrednictwem ataków wyłudzaniających podczas wydawania złośliwych witryn sieci Web.

Poniżej znajduje się lista aplikacji z uprawnieniami, które mogą być Scrutinize dla usług w chmurze firmy Microsoft:

- Aplikacje z aplikacją lub \*delegowane. Uprawnienia ReadWrite
- Aplikacje z delegowanymi uprawnieniami mogą odczytywać, wysyłać i zarządzać pocztą e-mail w imieniu użytkownika
- Aplikacje, którym przyznano następujące uprawnienia:

| Zasób | Uprawnienie |
| :- | :- |
| Office 365 Exchange Online | Posiada. AccessAsUser. All |
| | Interfejs. AccessAsUser. All |
| | Poczta. Przeczytaj |
| Microsoft Graph | Poczta. Przeczytaj |
| | Mail. Read. Shared |
| | Mail. ReadWrite |

- Aplikacje otrzymują pełną personifikację użytkownika zalogowanego użytkownika. Na przykład:

|Zasób | Uprawnienie |
| :- | :- |
| Wykres usługi Azure AD | Katalog. AccessAsUser. All |
| Microsoft Graph | Katalog. AccessAsUser. All |
| Interfejs API REST platformy Azure | user_impersonation |

Aby uniknąć tego scenariusza, należy odnieść się do [wykrywania i korygowania nielegalnych dotacji do zgody w pakiecie Office 365](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) w celu identyfikowania i rozwiązywania wszelkich aplikacji z nielegalnymi dotacjami lub aplikacjami, które mają więcej dotacji niż jest to konieczne. Następnie [Usuń](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-user-consent) samoobsługowe i [Ustal procedury ładu](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow). Na koniec Zaplanuj regularne przeglądy uprawnień aplikacji i usuń je, gdy nie są potrzebne.

#### <a name="consent-grants-recommended-reading"></a>Zgoda udziela zalecanego odczytu

- [Microsoft Graph permissions (Uprawnienia w programie Microsoft Graph)](https://docs.microsoft.com/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Ustawienia użytkownika i grupy

Poniżej znajdują się ustawienia użytkownika i grupy, które można zablokować w przypadku niejawnego potrzeby prowadzenia działalności:

#### <a name="user-settings"></a>Ustawienia użytkownika

- **Użytkownicy zewnętrzni** — zewnętrzna współpraca może być organicznie w przedsiębiorstwie z usługami takimi jak zespoły, Power BI, SharePoint Online i Azure Information Protection. Jeśli istnieją jawne ograniczenia dotyczące kontrolowania współpracy zewnętrznej inicjowanej przez użytkownika, zaleca się włączenie użytkowników zewnętrznych przy użyciu funkcji [zarządzania prawami usługi Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) lub kontrolowanej operacji, takiej jak pomoc techniczna. Jeśli nie chcesz zezwalać na współdziałanie z zewnętrznymi elementami organicznymi dla usług, możesz [zablokować możliwość całkowitego zapraszania użytkowników zewnętrznych](https://docs.microsoft.com/azure/active-directory/b2b/delegate-invitations). Alternatywnie można również [zezwalać na określone domeny i blokować](https://docs.microsoft.com/azure/active-directory/b2b/allow-deny-list) je w zaproszeniach użytkowników zewnętrznych.
- **Rejestracje aplikacji** — Jeśli rejestracje aplikacji są włączone, użytkownicy końcowi mogą dołączać aplikacje i przyznawać dostęp do swoich danych. Typowym przykładem rejestracji aplikacji jest umożliwienie użytkownikom dodatków plug-in programu Outlook lub asystentów głosowych, takich jak Alexa i Siri, odczytywanie wiadomości e-mail i kalendarza oraz wysyłanie wiadomości e-mail w ich imieniu. Jeśli klient zdecyduje się wyłączyć rejestrację aplikacji, zespoły InfoSec i IAM muszą być objęte zarządzaniem wyjątkami (rejestracje aplikacji, które są potrzebne w oparciu o wymagania biznesowe), ponieważ wymagają one zarejestrowania aplikacji przy użyciu konta administratora. najprawdopodobniej wymagają projektowania procesu operacjonalizować proces.
- **Portal administracyjny** — organizacje mogą zablokować blok usługi Azure ad w Azure Portal tak, aby inni administratorzy nie mogli uzyskiwać dostępu do usługi Azure ad w Azure Portal i odmylić. Przejdź do ustawień użytkownika w portalu zarządzania usługi Azure AD, aby ograniczyć dostęp:

![Portal administracyjny z ograniczonym dostępem](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> Administratorzy niebędący administratorami mogą nadal uzyskać dostęp do interfejsów zarządzania usługi Azure AD za pośrednictwem wiersza polecenia i innych interfejsów programistycznych.

#### <a name="group-settings"></a>Ustawienia grupy

**Samoobsługowe zarządzanie grupami/użytkownicy mogą tworzyć grupy zabezpieczeń/grupy usługi O365.** Jeśli nie ma żadnej obecnej inicjatywy samoobsługowej dla grup w chmurze, klienci mogą zdecydować się na jej wyłączenie do momentu, gdy nie będą gotowi do korzystania z tej funkcji.

#### <a name="groups-recommended-reading"></a>Zalecane odczytywanie grup

- [Co to jest Azure Active Directory współpracy B2B?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Integrowanie aplikacji z Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)
- [Aplikacje, uprawnienia i zgody w Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)
- [Używanie grup do zarządzania dostępem do zasobów w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)
- [Konfigurowanie samoobsługowego zarządzania dostępem do aplikacji w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)

### <a name="traffic-from-unexpected-locations"></a>Ruch z nieoczekiwanych lokalizacji

Osoby atakujące pochodzą z różnych części świata. Zarządzanie tym ryzykiem przy użyciu zasad dostępu warunkowego z lokalizacją jako warunek. [Warunek lokalizacji](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) zasad dostępu warunkowego umożliwia zablokowanie dostępu do lokalizacji z tego miejsca, w którym nie istnieje powód biznesowy, z którego można się zalogować.

![Utwórz nową nazwę lokalizacji](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Jeśli jest dostępna, użyj rozwiązania do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM) w celu analizowania i znajdowania wzorców dostępu między regionami. Jeśli nie korzystasz z produktu SIEM lub nie pozyskasz informacji o uwierzytelnianiu z usługi Azure AD, zalecamy używanie [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) do identyfikowania wzorców dostępu między regionami.

## <a name="access-usage"></a>Użycie dostępu

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Dzienniki usługi Azure AD zarchiwizowane i zintegrowane z planami reagowania na zdarzenia

Mając dostęp do działania związanego z logowaniem, inspekcje i zdarzenia związane z ryzykiem dla usługi Azure AD mają kluczowe znaczenie dla rozwiązywania problemów, analizy użycia i badań dowodowych. Usługa Azure AD zapewnia dostęp do tych źródeł za pomocą interfejsów API REST, które mają ograniczony okres przechowywania. System informacji o zabezpieczeniach i systemie zarządzania zdarzeniami (SIEM) albo równoważna technologia archiwizowania to klucz służący do długoterminowego przechowywania inspekcji i możliwości obsługi. Aby włączyć długoterminowe przechowywanie dzienników usługi Azure AD, należy dodać je do istniejącego rozwiązania SIEM lub użyć [Azure monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor). Archiwizuj dzienniki, które mogą być używane jako część planów i badań odpowiedzi na zdarzenia.

#### <a name="logs-recommended-reading"></a>Rejestruje zalecaną odczyt

- [Dokumentacja interfejsu API inspekcji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)
- [Dokumentacja interfejsu API raportów działań związanych z logowaniem Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-sign-in-activity-reference)
- [Pobieranie danych przy użyciu interfejsu API raportowania usługi Azure AD z certyfikatami](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-with-certificates)
- [Microsoft Graph Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)
- [Dokumentacja interfejsu API działania zarządzania pakietu Office 365](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference)
- [Jak używać pakietu zawartości Azure Active Directory Power BI](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Podsumowanie

Istnieją 12 aspektów bezpiecznej infrastruktury tożsamości. Ta lista pomoże Ci dodatkowo zabezpieczyć i zarządzać poświadczeniami, definiować środowisko uwierzytelniania, przypisywać delegatów, mierzyć użycie i definiować zasady dostępu w oparciu o stan zabezpieczeń przedsiębiorstwa.

- Przypisywanie właścicieli do kluczowych zadań.
- Zaimplementuj rozwiązania wykrywające słabe lub wycieki hasła, zwiększanie możliwości zarządzania hasłami i ich ochrony oraz bardziej bezpieczny dostęp użytkowników do zasobów.
- Zarządzanie tożsamościami urządzeń w celu ochrony zasobów w dowolnym momencie i z dowolnej lokalizacji.
- Zaimplementuj uwierzytelnianie bezhasłem.
- Zapewnienie standardowego mechanizmu logowania jednokrotnego w całej organizacji.
- Migrowanie aplikacji z AD FS do usługi Azure AD w celu zapewnienia lepszych zabezpieczeń i bardziej spójnego zarządzania.
- Przypisz użytkowników do aplikacji przy użyciu grup, aby zapewnić większą elastyczność i możliwość zarządzania w odpowiedniej skali.
- Konfigurowanie zasad dostępu opartych na ryzyku.
- Zablokuj starsze protokoły uwierzytelniania.
- Wykrywaj i Koryguj nielegalne dotacje zgody.
- Zablokuj ustawienia użytkowników i grup.
- Umożliwia długoterminowe przechowywanie dzienników usługi Azure AD na potrzeby rozwiązywania problemów, analizy użycia i dowodowych.

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z [testami operacyjnymi i akcjami nadzoru tożsamości](active-directory-ops-guide-govern.md).
