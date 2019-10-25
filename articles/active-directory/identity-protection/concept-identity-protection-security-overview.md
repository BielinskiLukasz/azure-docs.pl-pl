---
title: Omówienie zabezpieczeń usługi Identity Protection Azure Active Directory (Azure AD)
description: Dowiedz się, jak Omówienie zabezpieczeń zapewnia wgląd w stan zabezpieczeń organizacji.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: adc4acf10fee1b8b2c5e3af18389a49d84e2df14
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887236"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Azure Active Directory Identity Protection — Omówienie zabezpieczeń

[Omówienie zabezpieczeń](https://aka.ms/IdentityProtectionRefresh) w Azure Portal zapewnia wgląd w stan zabezpieczeń organizacji. Pomaga identyfikować potencjalne ataki i zrozumieć skuteczność zasad.

"Przegląd zabezpieczeń" jest szeroko podzielony na dwie sekcje:

- Trendy po lewej stronie zapewniają oś czasu ryzyka w organizacji.
- Kafelki, po prawej stronie, wyróżnianie kluczowych problemów występujących w organizacji i sugerowanie, jak szybko podejmować działania.

![Omówienie zabezpieczeń](./media/concept-identity-protection-security-overview/01.png)
  
## <a name="trends"></a>Trendy

### <a name="new-risky-users-detected"></a>Wykryto nowych ryzykownych użytkowników

Ten wykres pokazuje liczbę nowych użytkowników, których wykryto w wybranym okresie. Widok tego wykresu można filtrować według poziomu ryzyka użytkownika (niski, średni, wysoki). Umieść kursor na początku daty UTC, aby zobaczyć liczbę wykrytych zagrożeń użytkowników w danym dniu. Kliknięcie tego wykresu spowoduje przełączenie do raportu "ryzykowne użytkownicy". Aby skorygować użytkowników, którzy są zagrożeni, należy rozważyć zmianę hasła.

### <a name="new-risky-sign-ins-detected"></a>Wykryto nowe ryzykowne logowania

Ten wykres pokazuje liczbę ryzykownych logowań wykrytych w wybranym okresie. Widok tego wykresu można filtrować według typu ryzyka logowania (w czasie rzeczywistym lub agregacji) oraz poziomu ryzyka logowania (niski, średni, wysoki). Niechronione logowania mają pomyślne logowania w czasie rzeczywistym, które nie wymagały uwierzytelniania wieloskładnikowego. (Uwaga: logowania, które są ryzykowne ze względu na wykrywanie w trybie offline, nie mogą być chronione w czasie rzeczywistym za pomocą zasad ryzyka logowania). Umieść kursor nad przyrostami dat UTC, aby zobaczyć liczbę logowań wykrytych w danym dniu. Kliknięcie tego wykresu spowoduje przełączenie do raportu "ryzykowne logowania".

## <a name="tiles"></a>Okładzin
 
### <a name="high-risk-users"></a>Użytkownicy o wysokim ryzyku

Kafelek "Użytkownicy o wysokim ryzyku" pokazuje najnowszą liczbę użytkowników z wysokim prawdopodobieństwem naruszenia tożsamości. Powinien to być najwyższy priorytet badania. Kliknięcie kafelka "Użytkownicy o wysokim ryzyku" spowoduje przekierowanie do widoku filtrowanego raportu "ryzykownych użytkowników" pokazującego tylko użytkowników z poziomem ryzyka wysoki. Korzystając z tego raportu, można dowiedzieć się więcej i skorygować tych użytkowników przy użyciu resetowania hasła.

![Omówienie zabezpieczeń](./media/concept-identity-protection-security-overview/02.png)

### <a name="medium-risk-users"></a>Użytkownicy średniego ryzyka
Kafelek "średnio narażony użytkownicy" pokazuje najnowszą liczbę użytkowników z średnim prawdopodobieństwem naruszenia tożsamości. Kliknięcie kafelka "Użytkownicy średniego ryzyka" spowoduje przekierowanie do widoku filtrowanego raportu "ryzykowne użytkownicy" pokazującego tylko użytkowników z poziomem ryzyka średniego. Korzystając z tego raportu, można dokładniej zbadać i skorygować tych użytkowników.

### <a name="unprotected-risky-sign-ins"></a>Niechronione ryzykowne logowania

Na kafelku "niechronione ryzykowne logowania" jest wyświetlana liczba udanych i niechronionych logowań w czasie rzeczywistym, które nie zostały zablokowane lub uwierzytelnianie wieloskładnikowe zakończyło się za pomocą zasad dostępu warunkowego, zasad dotyczących ryzyka ochrony tożsamości lub MFA dla poszczególnych użytkowników. Są to potencjalnie zagrożone logowania, które zakończyły się powodzeniem, a nie z wyzwaniami uwierzytelniania wieloskładnikowego. Aby chronić takie logowania w przyszłości, Zastosuj zasady dotyczące ryzyka związanego z logowaniem. Kliknięcie kafelka "niechronione ryzykowne logowania" spowoduje przekierowanie do bloku konfiguracja zasad ryzyka logowania, w którym można skonfigurować zasady ryzyka logowania, aby wymagać uwierzytelniania wieloskładnikowego podczas logowania z określonym poziomem ryzyka.

### <a name="legacy-authentication"></a>Starsza wersja uwierzytelniania

Kafelek "starsze uwierzytelnianie" pokazuje liczbę starszych uwierzytelnień w organizacji w ostatnim tygodniu. Starsze protokoły uwierzytelniania nie obsługują nowoczesnych metod zabezpieczeń, takich jak MFA. Aby uniemożliwić starsze uwierzytelnianie, można zastosować zasady dostępu warunkowego. Kliknięcie kafelka "Starsza wersja" spowoduje przekierowanie do "" bezpiecznej oceny tożsamości ".

### <a name="identity-secure-score"></a>Ocena bezpiecznego tożsamości

Metryka z oceną zabezpieczeń i porównuje stan bezpieczeństwa z wzorcem branżowym. Kliknięcie kafelka "tożsamość Secure Score (wersja zapoznawcza)" spowoduje przekierowanie do bloku "tożsamość Secure Score", w którym można dowiedzieć się więcej o ulepszaniu stan zabezpieczeń.

## <a name="next-steps"></a>Następne kroki

- [Co to jest ryzyko](concept-identity-protection-risks.md)

- [Zasady dostępne w celu ograniczenia ryzyka](concept-identity-protection-policies.md)
