---
title: 'Azure AD Connect: bezproblemowe logowanie jednokrotne | Microsoft Docs'
description: W tym temacie Azure Active Directory opisano sposób bezproblemowego logowania jednokrotnego (Azure AD) i sposób, w jaki można zapewnić prawdziwą rejestrację jednokrotną dla użytkowników stacjonarnych w sieci firmowej.
services: active-directory
keywords: Co to jest Azure AD Connect, zainstaluj Active Directory, wymagane składniki usługi Azure AD, logowania jednokrotnego, rejestracji jednokrotnej
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1b7e4716e731e6b73e3ac60b64baa71043906fc
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77483758"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory bezproblemowe logowanie jednokrotne

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Co Azure Active Directory bezproblemowe logowanie jednokrotne?

Bezproblemowe logowanie jednokrotne w usłudze Azure Active Directory zapewnia automatyczne logowanie użytkowników, gdy ich urządzenia są połączone z siecią firmową. Jeśli ta funkcja jest włączona, użytkownicy nie muszą wpisywać swoich haseł, aby zalogować się do usługi Azure AD, a zwykle nawet wpisywać w swoich nazwach użytkownika. Ta funkcja zapewnia użytkownikom łatwy dostęp do aplikacji w chmurze bez konieczności używania dodatkowych składników lokalnych.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Bezproblemowe logowanie jednokrotne może być łączone z [synchronizacją skrótów haseł](how-to-connect-password-hash-synchronization.md) lub metodami logowania przy [użyciu uwierzytelniania przekazywanego](how-to-connect-pta.md) . Bezproblemowe logowanie jednokrotne _nie_ ma zastosowania do Active Directory Federation Services (AD FS).

![Bezproblemowe logowanie jednokrotne](./media/how-to-connect-sso/sso1.png)

>[!IMPORTANT]
>Bezproblemowe logowanie jednokrotne wymaga, aby urządzenie użytkownika zostało przyłączone do **domeny** , ale nie jest używane w [przyłączonych do usługi Azure AD](../devices/concept-azure-ad-join.md) lub w [hybrydowych urządzeniach usługi Azure AD](../devices/concept-azure-ad-join-hybrid.md) . Usługa logowania jednokrotnego w usłudze Azure AD jest dołączona i hybrydowa usługa Azure AD jest oparta na [podstawowym tokenie odświeżania](../devices/concept-primary-refresh-token.md).

## <a name="key-benefits"></a>Najważniejsze korzyści

- *Fantastyczne środowisko pracy użytkownika*
  - Użytkownicy są automatycznie logowani do aplikacji lokalnych i opartych na chmurze.
  - Użytkownicy nie muszą wielokrotnie wprowadzać swoich haseł.
- *Łatwe wdrażanie & administrowanie*
  - Nie trzeba wykonywać żadnych dodatkowych składników w środowisku lokalnym, aby to zrobić.
  - Działa z dowolną metodą uwierzytelniania w chmurze — [synchronizacją skrótów haseł](how-to-connect-password-hash-synchronization.md) lub [uwierzytelnianiem przekazującym](how-to-connect-pta.md).
  - Mogą być przeprowadzone do niektórych lub wszystkich użytkowników przy użyciu zasady grupy.
  - Rejestrowanie urządzeń z systemem innym niż Windows 10 w usłudze Azure AD bez konieczności używania infrastruktury AD FS. Ta funkcja wymaga użycia wersji 2,1 lub nowszej [klienta dołączania do miejsca pracy](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Najważniejsze funkcje

- Nazwa użytkownika logowania może być lokalną domyślną nazwą użytkownika (`userPrincipalName`) lub innym atrybutem skonfigurowanym w Azure AD Connect (`Alternate ID`). Oba przypadki użycia działają, ponieważ bezproblemowe logowanie jednokrotne korzysta z `securityIdentifier`ego żądania w biletu protokołu Kerberos w celu wyszukania odpowiedniego obiektu użytkownika w usłudze Azure AD.
- Bezproblemowe logowanie jednokrotne jest funkcją oportunistyczną. Jeśli z jakiegoś powodu nie powiedzie się, środowisko logowania użytkownika powróci do regularnego zachowania — oznacza to, że użytkownik musi wprowadzić hasło na stronie logowania.
- Jeśli aplikacja (na przykład `https://myapps.microsoft.com/contoso.com`) przekaże parametr `domain_hint` (OpenID Connect Connect) lub `whr` (SAML) identyfikujący dzierżawcę lub `login_hint` parametr-identyfikujący użytkownika w żądaniu logowania usługi Azure AD, użytkownicy są automatycznie zalogowani bez wprowadzania nazw użytkowników ani haseł.
- Użytkownicy mogą również korzystać z dyskretnego logowania, jeśli aplikacja (na przykład `https://contoso.sharepoint.com`) wysyła żądania logowania do punktów końcowych usługi Azure AD skonfigurowanych jako dzierżawcy — czyli `https://login.microsoftonline.com/contoso.com/<..>` lub `https://login.microsoftonline.com/<tenant_ID>/<..>`-zamiast wspólnego punktu końcowego usługi Azure AD, czyli `https://login.microsoftonline.com/common/<...>`.
- Wylogowywanie jest obsługiwane. Dzięki temu użytkownicy mogą wybrać inne konto usługi Azure AD, aby zalogować się za pomocą programu, a nie automatycznie logować się automatycznie za pomocą bezproblemowego logowania jednokrotnego.
- Klienci Win32 pakietu Office 365 (Outlook, Word, Excel i inne) z wersjami 16.0.8730. xxxx i nowszymi są obsługiwani przy użyciu nieinteraktywnego przepływu. W przypadku usługi OneDrive konieczne będzie aktywowanie [funkcji konfiguracji dyskretnej usługi OneDrive](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) w celu zalogowania dyskretnego.
- Można ją włączyć za pośrednictwem Azure AD Connect.
- Jest to bezpłatna funkcja i nie są potrzebne żadne płatne wersje usługi Azure AD do użycia.
- Jest ona obsługiwana w klientach opartych na przeglądarce sieci Web i klientach pakietu Office, którzy obsługują [nowoczesne uwierzytelnianie](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) na platformach i przeglądarkach obsługujących uwierzytelnianie Kerberos:

| OS\Browser |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Tak\*|Yes|Yes|Tak\*\*\*|Brak
|Windows 8.1|Tak\*|Brak|Yes|Tak\*\*\*|Brak
|Windows 8|Tak\*|Brak|Yes|Tak\*\*\*|Brak
|Windows 7|Tak\*|Brak|Yes|Tak\*\*\*|Brak
|System Windows Server 2012 R2 lub nowszy|Tak\*\*|Brak|Yes|Tak\*\*\*|Brak
|Mac OS X|Brak|Brak|Tak\*\*\*|Tak\*\*\*|Tak\*\*\*


\*wymaga programu Internet Explorer w wersji 10 lub nowszej

\*\*wymaga programu Internet Explorer w wersji 10 lub nowszej. Wyłącz rozszerzony tryb chroniony

\*\*\*wymaga [dodatkowej konfiguracji](how-to-connect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>W przypadku systemu Windows 10 zaleceniem jest użycie funkcji [Dołącz do usługi Azure AD](../devices/concept-azure-ad-join.md) w celu zapewnienia optymalnego logowania jednokrotnego w usłudze Azure AD.

## <a name="next-steps"></a>Następne kroki

- [**Szybki Start**](how-to-connect-sso-quick-start.md) — Uzyskaj i korzystaj z bezproblemowego logowania jednokrotnego usługi Azure AD.
- [**Plan wdrożenia — plan**](https://aka.ms/deploymentplans/sso) wdrożenia krok po kroku.
- [**Szczegółowee techniczne głębokie**](how-to-connect-sso-how-it-works.md) — zrozumienie, jak działa ta funkcja.
- [**Często zadawane pytania**](how-to-connect-sso-faq.md) — odpowiedzi na często zadawane pytania.
- [**Rozwiązywanie problemów**](tshoot-connect-sso.md) — Dowiedz się, jak rozwiązywać typowe problemy z funkcją.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) — w przypadku zgłaszania nowych żądań funkcji.

