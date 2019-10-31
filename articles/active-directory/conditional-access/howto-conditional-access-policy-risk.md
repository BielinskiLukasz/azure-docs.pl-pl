---
title: Dostęp warunkowy — dostęp warunkowy oparty na ryzyku — Azure Active Directory
description: Tworzenie zasad dostępu warunkowego w celu włączenia rozszerzeń do zasad ochrony tożsamości
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 102371f8af45a1a51715dbfb11afc3f0f4e457d7
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150698"
---
# <a name="conditional-access-risk-based-conditional-access"></a>Dostęp warunkowy: dostęp warunkowy oparty na ryzyku

Organizacje z licencjami na Azure AD — wersja Premium P2 mogą tworzyć zasady dostępu warunkowego, które uwzględniają Azure AD Identity Protection wykrywania zagrożeń. Istnieją trzy domyślne zasady, które można włączyć z poziomu pola. 

* Wymagaj, aby wszyscy użytkownicy rejestrowali się w usłudze Azure Multi-Factor Authentication.
* Wymagaj zmiany hasła dla użytkowników, którzy są wysokim ryzykiem.
* Wymagaj uwierzytelniania wieloskładnikowego dla użytkowników o średnim lub wysokim ryzyku związanym z logowaniem.

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>Wymagaj, aby wszyscy użytkownicy rejestrowali się w usłudze Azure Multi-Factor Authentication

Włączenie tych zasad będzie wymagało, aby wszyscy użytkownicy rejestrowali się w usłudze Azure Multi-Factor Authentication w ciągu 14 dni. 

1. Zaloguj się do **portalu Azure**.
1. Kliknij pozycję **Wszystkie usługi**, a następnie przejdź do usługi **Azure AD Identity Protection**.
1. Kliknij pozycję **Rejestracja w usłudze MFA**.
1. W obszarze **przypisania**wybierz pozycję **Użytkownicy**.
   1. W obszarze **dołączanie**wybierz pozycję **Wszyscy użytkownicy**.
   1. W obszarze **Wyklucz**wybierz opcję **Wybierz wykluczonych użytkowników**, wybierz pozycję dostęp awaryjny lub konta w firmie, a następnie wybierz pozycję **Wybierz**. 
   1. Wybierz pozycję **Done** (Gotowe).
1. Ustaw ustawienie **Wymuszaj zasady** na **włączone**.
1. Kliknij przycisk **Save** (Zapisz).

## <a name="require-a-password-change-high-risk-users"></a>Wymagaj zmiany hasła dla użytkowników o wysokim ryzyku

Firma Microsoft współpracuje z naukowcami, organami ścigania, różnymi zespołami ds. zabezpieczeń w firmie Microsoft i innymi zaufanymi źródłami w celu wyszukiwania par „nazwa użytkownika i hasło”. Gdy jedna z takich par jest zgodna z kontem w Twoim środowisku, można wyzwolić zmianę hasła opartą na ryzykach, używając następujących zasad.

1. Zaloguj się do **portalu Azure**.
1. Kliknij pozycję **Wszystkie usługi**, a następnie przejdź do usługi **Azure AD Identity Protection**.
1. Kliknij pozycję **zasady ryzyka użytkownika**.
1. W obszarze **przypisania**wybierz pozycję **Użytkownicy** .
   1. W obszarze **dołączanie**wybierz pozycję **Wszyscy użytkownicy**.
   1. W obszarze **Wyklucz**wybierz opcję **Wybierz wykluczonych użytkowników**, wybierz pozycję dostęp awaryjny lub konta w firmie, a następnie wybierz pozycję **Wybierz**.
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **warunki**wybierz pozycję **ryzyko użytkownika**, a następnie wybierz pozycję **wysoki**.
   1. Kliknij przycisk **Wybierz** , a następnie pozycję **gotowe**.
1. W obszarze **kontrolki** > **dostęp**wybierz opcję **Zezwalaj na dostęp**, a następnie wybierz pozycję **Wymagaj zmiany hasła**.
   1. Kliknij pozycję **Wybierz**.
1. Ustaw ustawienie **Wymuszaj zasady** na **włączone**.
1. Kliknij przycisk **Save** (Zapisz).

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>Wymagaj użytkowników o średnim lub wysokim ryzyku związanym z logowaniem usługi MFA

Większości użytkowników dotyczy zachowanie normalne, które można śledzić. Gdy ta norma zostanie przekroczona, zezwolenie tym użytkownikom na zwykłe logowanie się może stanowić ryzyko. Możesz chcieć zablokować tego użytkownika lub po prostu poproś go o przeprowadzenie uwierzytelniania wieloskładnikowego, aby udowodnić, że są one rzeczywiście napisane. Aby włączyć zasady wymagające uwierzytelniania wieloskładnikowego, gdy zostanie wykryte ryzykowne logowanie, użyj następujących zasad.

1. Zaloguj się do **portalu Azure**.
1. Kliknij pozycję **Wszystkie usługi**, a następnie przejdź do usługi **Azure AD Identity Protection**.
1. Kliknij **zasady dotyczące ryzyka związanego z logowaniem**
1. W obszarze **przypisania**wybierz pozycję **Użytkownicy** .
   1. W obszarze **dołączanie**wybierz pozycję **Wszyscy użytkownicy**.
   1. W obszarze **Wyklucz**wybierz opcję **Wybierz wykluczonych użytkowników**, wybierz pozycję dostęp awaryjny lub konta w firmie, a następnie wybierz pozycję **Wybierz**.
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **warunki**wybierz pozycję **ryzyko związane z logowaniem**, a następnie wybierz pozycję **średni i powyżej**.
   1. Kliknij przycisk **Wybierz** , a następnie pozycję **gotowe**.
1. W obszarze **kontrolki** > **dostęp**wybierz opcję **Zezwalaj na dostęp**, a następnie wybierz opcję **Wymagaj uwierzytelniania wieloskładnikowego**.
   1. Kliknij pozycję **Wybierz**.
1. Ustaw ustawienie **Wymuszaj zasady** na **włączone**.
1. Kliknij przycisk **Save** (Zapisz).

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Symulowanie zachowania logowania za pomocą narzędzia What If dostępu warunkowego](troubleshoot-conditional-access-what-if.md)

[Jak to działa: usługa Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)

[Co to jest Azure Active Directory Identity Protection?](../identity-protection/overview.md)
