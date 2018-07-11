---
title: 'Azure AD Connect: Bezproblemowego logowania jednokrotnego — jak działa | Dokumentacja firmy Microsoft'
description: W tym artykule opisano, jak działa funkcja usługi Azure Active Directory bezproblemowego logowania jednokrotnego.
services: active-directory
keywords: Co to jest program Azure AD Connect, instalowanie usługi Active Directory, wymaganych składników dla usługi Azure AD, logowania jednokrotnego, logowanie jednokrotne
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 0b1940894ffb01595d11bc49889c6ec01714816b
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37918258"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Usługa Azure Active Directory bezproblemowe logowanie jednokrotne: techniczne

Ten artykuł zawiera szczegółowe informacje techniczne, w jak działa funkcja usługi Azure Active Directory bezproblemowe logowanie jednokrotne (bezproblemowe logowanie Jednokrotne).

## <a name="how-does-seamless-sso-work"></a>Jak działa bezproblemowe logowanie Jednokrotne

Ta sekcja zawiera trzy części do niego:
1. Ustawienia funkcji bezproblemowego logowania jednokrotnego.
2. Jak transakcji logowania pojedynczego użytkownika w przeglądarce sieci web działa z bezproblemowe logowanie Jednokrotne.
3. Transakcji logowania pojedynczego użytkownika w macierzystym kliencie współdziałania z usługą bezproblemowe logowanie Jednokrotne.

### <a name="how-does-set-up-work"></a>Jak skonfigurować pracy?

Bezproblemowe logowanie Jednokrotne jest włączony, za pomocą usługi Azure AD Connect, jak pokazano [tutaj](active-directory-aadconnect-sso-quick-start.md). Podczas włączania funkcji, wykonywane są następujące kroki:
- Konto komputera o nazwie `AZUREADSSOACC` (który reprezentuje usługę Azure AD) jest tworzony w sieci lokalnej usługi Active Directory (AD).
- Klucz odszyfrowywania protokołu Kerberos konta komputera jest udostępniony w bezpieczny sposób za pomocą usługi Azure AD.
- Ponadto dwa Kerberos głównych nazw usług (SPN) są tworzone do reprezentowania dwa adresy URL, które są używane podczas logowania w usłudze Azure AD.

>[!NOTE]
> Konto komputera, jak i nazwy SPN Kerberos są tworzone w każdym lesie usługi AD, można zsynchronizować z usługą Azure AD (przy użyciu usługi Azure AD Connect) i dla użytkowników, którzy mają bezproblemowe logowanie Jednokrotne. Przenieś `AZUREADSSOACC` konta komputera do organizacji jednostki Organizacyjnej przechowywania innych kont do zapewnienia, że odbywa się w taki sam sposób i nie zostanie usunięta.

>[!IMPORTANT]
>Zdecydowanie zalecamy możesz [przerzucić klucz odszyfrowywania protokołu Kerberos](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account) z `AZUREADSSOACC` konto komputera co najmniej co 30 dni.

Po zakończeniu konfiguracji bezproblemowe logowanie Jednokrotne działa tak samo jak wszystkie inne logowania, która używa zintegrowanego Windows Authentication (Zintegrowane).

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Sposób logowania w przeglądarce sieci web z pracą bezproblemowe logowanie Jednokrotne?

Przepływ logowania w przeglądarce sieci web jest następująca:

1. Użytkownik próbuje uzyskać dostęp do aplikacji sieci web (na przykład Outlook Web App - https://outlook.office365.com/owa/) z przyłączonym do domeny urządzenia firmowe w sieci firmowej.
2. Jeśli użytkownik nie jest już zalogowany, użytkownik jest przekierowany do strony logowania usługi Azure AD.
3. Użytkownik wpisze nazwy użytkownika do strony logowania usługi Azure AD.

  >[!NOTE]
  >Aby uzyskać [niektóre aplikacje](./active-directory-aadconnect-sso-faq.md#what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso), są pomijane kroki 2 i 3.

4. Przy użyciu języka JavaScript w tle, usługa Azure AD wyzwań przeglądarki, za pośrednictwem nieautoryzowanych uzyskanie odpowiedzi 401, aby zapewnić biletu protokołu Kerberos.
5. Z kolei przeglądarki, żąda biletu z usługi Active Directory dla `AZUREADSSOACC` konto komputera, (który reprezentuje usługę Azure AD).
6. Usługi Active Directory lokalizuje konto komputera i zwraca biletu protokołu Kerberos w przeglądarce zaszyfrowany przy użyciu hasła konta komputera.
7. Przeglądarka przesyła dalej biletu protokołu Kerberos, który go pobrać z usługi Active Directory do usługi Azure AD.
8. Usługa Azure AD odszyfrowuje biletu protokołu Kerberos, w tym tożsamości użytkownika, w przypadku zalogowania się do urządzeń firmowych, aby za pomocą klucza współużytkowanego wcześniej.
9. Po dokonaniu oceny usługi Azure AD zwraca token z powrotem do aplikacji lub z monitem o wykonywać dodatkowe dowody, takie jak uwierzytelnianie wieloskładnikowe.
10. Jeśli logowanie użytkownika powiedzie się, użytkownik będzie mógł uzyskiwać dostęp do aplikacji.

Na poniższym diagramie przedstawiono wszystkie składniki i kroki do wykonania.

![Bezproblemowe logowanie na — Web przepływu aplikacji](./media/active-directory-aadconnect-sso/sso2.png)

Bezproblemowe logowanie Jednokrotne jest oportunistyczne, co oznacza, że jeśli nie powiedzie się, środowisko logowania powraca do regularnych zachowań — tzn, użytkownik musi wprowadzić hasło do logowania.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Sposób logowania na komputerze klienckim natywne z pracą bezproblemowe logowanie Jednokrotne?

Przepływ logowania na komputerze klienckim natywnych jest następująca:

1. Użytkownik próbuje uzyskać dostęp do natywnych aplikacji (na przykład klient programu Outlook) z przyłączonym do domeny urządzenia firmowe w sieci firmowej.
2. Jeśli użytkownik nie jest już zalogowany, aplikacji natywnej pobiera nazwa użytkownika z sesji Windows urządzenia.
3. Aplikacja wysyła nazwę użytkownika do usługi Azure AD i pobiera punktu końcowego MEX WS-Trust Twojej dzierżawy.
4. Następnie aplikacja wykonuje zapytanie punktu końcowego MEX WS-Trust, aby sprawdzić, czy zintegrowane uwierzytelnianie punkt końcowy jest dostępny.
5. Jeśli krok 4 zakończy się powodzeniem, wystawiono wyzwanie protokołu Kerberos.
6. Jeśli aplikacja jest w stanie pobrać biletu protokołu Kerberos, przekazuje go do endpoint zintegrowane uwierzytelnianie usługi Azure AD.
7. Usługa Azure AD odszyfrowuje biletu protokołu Kerberos i zweryfikuje go.
8. Usługa Azure AD podpisuje użytkownika w i wystawia SAML token do aplikacji.
9. Aplikacja przesyła następnie token SAML do punktu końcowego tokenu protokołu OAuth2 w usłudze Azure AD.
10. Usługa Azure AD sprawdza poprawność tokenu języka SAML i wysyła do aplikacji, token dostępu i token odświeżania dla określonego zasobu oraz identyfikator tokenu.
11. Użytkownik uzyskuje dostęp do zasobów aplikacji.

Na poniższym diagramie przedstawiono wszystkie składniki i kroki do wykonania.

![Bezproblemowe pojedynczego logowania — przepływ aplikacji natywnej](./media/active-directory-aadconnect-sso/sso14.png)

## <a name="next-steps"></a>Kolejne kroki

- [**Przewodnik Szybki Start** ](active-directory-aadconnect-sso-quick-start.md) — Rozpocznij pracę, a systemem Azure bezproblemowe logowanie Jednokrotne usługi AD.
- [**Często zadawane pytania** ](active-directory-aadconnect-sso-faq.md) — odpowiedzi na często zadawane pytania.
- [**Rozwiązywanie problemów z** ](active-directory-aadconnect-troubleshoot-sso.md) — Dowiedz się, jak rozwiązać typowe problemy z funkcją.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) — w przypadku zgłaszania sugestie dotyczące nowych funkcji.
