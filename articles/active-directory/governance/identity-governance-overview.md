---
title: Zarządzanie tożsamościami — Azure Active Directory | Microsoft Docs
description: Azure Active Directory Identity Governance pozwala zrównoważyć potrzebę organizacji dla bezpieczeństwa i produktywności pracowników przy użyciu odpowiednich procesów i widoczności.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 08/25/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 014a2c3cff3804657e4e2bf624b97eceef4bf4b2
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70033320"
---
# <a name="what-is-azure-ad-identity-governance"></a>Co to jest usługa Azure AD Identity Governance?

Usługa Azure Active Directory (Azure AD) Zarządzanie tożsamościami pozwala zrównoważyć potrzebę organizacji w zakresie zabezpieczeń i produktywności pracowników z właściwymi procesami i widocznością. Zapewnia to możliwość zapewnienia, że odpowiedni użytkownicy mają odpowiedni dostęp do odpowiednich zasobów i umożliwia ochronę, monitorowanie i inspekcję dostępu do kluczowych elementów zawartości — jednocześnie zapewniając produktywność pracowników.  

Zarządzanie tożsamościami zapewnia organizacjom możliwość wykonywania następujących zadań między pracownikami, partnerami biznesowymi i dostawcami oraz usługami i aplikacjami:

- Zarządzanie cyklem życia tożsamości
- Zarządzanie cyklem życia dostępu
- Bezpieczna Administracja

W odróżnieniu od tych czterech najważniejszych pytań warto ułatwić organizacjom Rozwiązywanie problemów:

- Którzy użytkownicy powinni mieć dostęp do zasobów?
- Jakie są użytkownicy korzystający z tego dostępu?
- Czy istnieją skuteczne kontrole organizacyjne na potrzeby zarządzania dostępem?
- Czy inspekcje mogą sprawdzić, czy kontrolki działają?

## <a name="identity-lifecycle"></a>Cykl życia tożsamości

Zarządzanie tożsamościami pomaga organizacjom w osiągnięciu równowagi między produktywnością — jak szybko można uzyskać dostęp do potrzebnych zasobów, takich jak dołączanie do mojej organizacji? I *zabezpieczenia* — jak należy zmienić ich dostęp z upływem czasu, na przykład ze względu na zmiany stanu zatrudnienia osoby?  Zarządzanie cyklem życia tożsamości jest podstawą do zarządzania tożsamościami, a skuteczne zarządzanie na dużą skalę wymaga modernizacji infrastruktury zarządzania cyklem życia tożsamości dla aplikacji.

![Cykl życia tożsamości](./media/identity-governance-overview/identity-lifecycle.png)

W przypadku wielu organizacji cykl życia tożsamości dla pracowników jest powiązany z reprezentacją tego użytkownika w systemie HCM (zarządzanie dużymi środkami).  Azure AD — wersja Premium automatycznie obsługuje tożsamości użytkowników dla osób przedstawionych w dniach roboczych w Active Directory i Azure Active Directory, zgodnie z opisem w samouczku dotyczącym [obsługi ruchu przychodzącego (wersja zapoznawcza) usługi Workday](../saas-apps/workday-inbound-tutorial.md).  Azure AD — wersja Premium obejmuje również [Microsoft Identity Manager](/microsoft-identity-manager/), które mogą importować rekordy z lokalnych systemów HCM, takich jak SAP, Oracle eBusiness i Oracle PeopleSoft.

W coraz większym stopniu scenariusze wymagają współpracy z osobami spoza organizacji. Współpraca B2B w usłudze [Azure AD](/azure/active-directory/b2b/) umożliwia bezpieczne udostępnianie aplikacji i usług organizacji użytkownikom-Gościom i partnerom zewnętrznym z dowolnej organizacji przy zachowaniu kontroli nad danymi firmowymi.

## <a name="access-lifecycle"></a>Cykl życia dostępu

Organizacje muszą mieć proces zarządzania dostępem poza to, co zostało początkowo zainicjowane dla użytkownika, gdy tożsamość tego użytkownika została utworzona.  Ponadto organizacje korporacyjne muszą być w stanie wydajnie skalować, aby mieć możliwość ciągłego tworzenia i wymuszania zasad dostępu i kontroli.

![Cykl życia dostępu](./media/identity-governance-overview/access-lifecycle.png)

Zwykle deleguje decyzje dotyczące zatwierdzenia dostępu do podmiotów podejmujących decyzje biznesowe.  Ponadto może to dotyczyć samych użytkowników.  Na przykład użytkownicy, którzy uzyskują dostęp do poufnych danych klienta w aplikacji marketingowej firmy w Europie, muszą znać zasady firmy. Użytkownicy-Goście mogą nie wiedzieć, jakie są wymagania dotyczące obsługi danych w organizacji, do której zostały zaproszone.

Organizacje mogą zautomatyzować proces cyklu życia dostępu za pomocą technologii, takich jak [grupy dynamiczne](../users-groups-roles/groups-dynamic-membership.md), w połączeniu z obsługą użytkowników, aby [SaaS aplikacje](../saas-apps/tutorial-list.md) lub [Aplikacje zintegrowane z usługą Standard scim](../manage-apps/use-scim-to-provision-users-and-groups.md).  Organizacje mogą również kontrolować, którzy [Użytkownicy-Goście mają dostęp do aplikacji lokalnych](../b2b/hybrid-cloud-to-on-premises.md).  Te prawa dostępu można następnie regularnie przeglądać przy użyciu cyklicznych [przeglądów dostępu do usługi Azure AD](access-reviews-overview.md).

Gdy użytkownik próbuje uzyskać dostęp do aplikacji, usługa Azure AD wymusza zasady [dostępu warunkowego](/azure/active-directory/conditional-access/) . Na przykład zasady dostępu warunkowego mogą zawierać [warunki użytkowania](../conditional-access/terms-of-use.md) i upewnić się [, że użytkownik wyraził zgodę na te warunki](../conditional-access/require-tou.md) przed uzyskaniem dostępu do aplikacji.

## <a name="privileged-access-lifecycle"></a>Cykl życia uprzywilejowanego dostępu

W przeszłości dostęp uprzywilejowany został opisany przez innych dostawców jako osobną możliwość od ładu zarządzania tożsamościami. Jednak firma Microsoft uważa, że zarządzanie uprzywilejowanym dostępem jest kluczową częścią zarządzania tożsamościami — szczególnie w przypadku, gdy potencjalną przyczyną nieprawidłowego użycia są skojarzone z tymi prawami administratora. Pracownicy, dostawcy i wykonawcy, którzy podejmują prawa administracyjne, muszą być zarządzani.

![Cykl życia uprzywilejowanego dostępu](./media/identity-governance-overview/privileged-access-lifecycle.png)

Azure AD Privileged Identity Management (PIM) oferuje dodatkowe funkcje kontroli dostosowane do zabezpieczania praw dostępu do zasobów w usłudze Azure AD, na platformie Azure i innych usługach online firmy Microsoft.  Dostęp just-in-Time i funkcje alertów o zmianie roli udostępniane przez usługę Azure AD PIM, oprócz uwierzytelniania wieloskładnikowego i dostępu warunkowego, zapewniają kompleksowy zestaw kontrolek ładu, który pomaga zabezpieczyć zasoby firmy (katalog, Office 365 i role zasobów platformy Azure). Podobnie jak w przypadku innych form dostępu, organizacje mogą korzystać z przeglądów dostępu w celu skonfigurowania cyklicznej certyfikacji dostępu dla wszystkich użytkowników w rolach administratora.

## <a name="getting-started"></a>Wprowadzenie

Chociaż nie ma doskonałego rozwiązania lub rekomendacji dla każdego klienta, następujące konfiguracje zawierają przewodnik, w jaki sposób zasady podstawowe firma Microsoft zalecają, aby zapewnić bardziej bezpieczny i wydajny pracownik.

- [Konfiguracje dostępu tożsamości i urządzenia](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Zabezpieczanie dostępu uprzywilejowanego](../users-groups-roles/directory-admin-roles-secure.md)

Możesz również zapoznać się z kartą wprowadzenie w temacie **Zarządzanie tożsamościami** w Azure Portal, aby rozpocząć korzystanie z funkcji zarządzania prawami, przeglądów dostępu, Privileged Identity Management i warunki użytkowania.

![Wprowadzenie do zarządzania tożsamościami](./media/identity-governance-overview/getting-started.png)

Jeśli masz opinię na temat funkcji zarządzania tożsamościami, kliknij przycisk **otrzymasz opinię?** w Azure Portal przesłać swoją opinię. Zespół regularnie przegląda swoją opinię.

## <a name="next-steps"></a>Następne kroki

- [Co to jest zarządzanie prawami w usłudze Azure AD? (wersja zapoznawcza)](entitlement-management-overview.md)
- [Co to są przeglądy dostępu w usłudze Azure AD?](access-reviews-overview.md)
- [Co to jest Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [Do czego można wykorzystać warunki użytkowania?](active-directory-tou.md)
