---
title: Rejestrowanie aplikacji jednostronicowych (SPA) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć aplikację jednostronicową (Rejestracja aplikacji)
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: hahamil
ms.custom: aaddev
ms.openlocfilehash: 9dc5b446e2ab26ca43c2a300e1af1237353325a3
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682396"
---
# <a name="single-page-application-app-registration"></a>Aplikacja jednostronicowa: Rejestracja aplikacji

Aby zarejestrować aplikację jednostronicową (SPA) na platformie tożsamości firmy Microsoft, wykonaj następujące czynności. Kroki rejestracji różnią się w zależności od MSAL. js 1,0, która obsługuje przepływ niejawnego przydzielenia oraz MSAL. js 2,0, który obsługuje przepływ kodu autoryzacji z PKCE.

## <a name="create-the-app-registration"></a>Utwórz rejestrację aplikacji

W przypadku aplikacji opartych na MSAL. js 1,0 i 2,0 Zacznij od wykonania poniższych kroków, aby utworzyć początkową rejestrację aplikacji.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Jeśli Twoje konto ma dostęp do wielu dzierżawców, wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz dzierżawcę, który powinien zawierać rejestrację aplikacji, którą chcesz utworzyć.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj**wybierz pozycję **rejestracje aplikacji**.
1. Wybierz pozycję **Nowa rejestracja**, wprowadź **nazwę** aplikacji i wybierz **obsługiwane typy kont** dla aplikacji. **Nie** należy wprowadzać **identyfikatora URI przekierowania**. Aby uzyskać opis różnych typów kont, zobacz [Rejestrowanie nowej aplikacji przy użyciu Azure Portal](quickstart-register-app.md#register-a-new-application-using-the-azure-portal).
1. Wybierz pozycję **zarejestruj** , aby utworzyć rejestrację aplikacji.

Następnie skonfiguruj rejestrację aplikacji za pomocą **identyfikatora URI przekierowania** , aby określić, gdzie platforma tożsamości firmy Microsoft powinna przekierować klienta wraz z wszelkimi tokenami zabezpieczeń. Wykonaj kroki odpowiednie dla używanej wersji MSAL. js w aplikacji:

- [MSAL. js 2,0 z przepływem kodu uwierzytelniania](#redirect-uri-msaljs-20-with-auth-code-flow) (zalecane)
- [MSAL. js 1,0 z niejawnym przepływem](#redirect-uri-msaljs-10-with-implicit-flow)

## <a name="redirect-uri-msaljs-20-with-auth-code-flow"></a>Identyfikator URI przekierowania: MSAL. js 2,0 z przepływem kodu uwierzytelniania

Wykonaj następujące kroki, aby dodać identyfikator URI przekierowania dla aplikacji korzystającej z MSAL. js 2,0 lub nowszej. MSAL. js 2.0 + obsługuje przepływ kodu autoryzacji z PKCE i CORS w odpowiedzi na [ograniczenia plików cookie](reference-third-party-cookies-spas.md)innych firm. Niejawny przepływ grantu nie jest obsługiwany w MSAL. js 2.0 +.

1. W Azure Portal wybierz rejestrację aplikacji utworzoną wcześniej w temacie [Tworzenie rejestracji aplikacji](#create-the-app-registration).
1. W obszarze **Zarządzaj**wybierz pozycję **uwierzytelnianie**, a następnie wybierz pozycję **Dodaj platformę**.
1. W obszarze **aplikacje sieci Web**wybierz kafelek **aplikacji jednostronicowej** .
1. W obszarze **identyfikatory URI przekierowania**wprowadź [Identyfikator URI przekierowania](reply-url.md). **Nie** zaznaczaj żadnych pól wyboru w obszarze **niejawne przyznanie**.
1. Wybierz pozycję **Konfiguruj** , aby zakończyć dodawanie identyfikatora URI przekierowania.

Zakończono rejestrację aplikacji jednostronicowej (SPA) i skonfigurowano identyfikator URI przekierowania, do którego zostanie przekierowany klient, i zostaną wysłane wszystkie tokeny zabezpieczające. Konfigurując identyfikator URI przekierowania przy użyciu kafelka **aplikacji jednostronicowej** w okienku **Dodaj platformę** , Rejestracja aplikacji jest skonfigurowana do obsługi przepływu kodu autoryzacji za pomocą PKCE i mechanizmu CORS.

## <a name="redirect-uri-msaljs-10-with-implicit-flow"></a>Identyfikator URI przekierowania: MSAL. js 1,0 z niejawnym przepływem

Wykonaj następujące kroki, aby dodać identyfikator URI przekierowania dla jednostronicowej aplikacji korzystającej z MSAL. js 1,3 lub starszej i niejawnego przepływu dotacji. Aplikacje korzystające z MSAL. js 1,3 lub starszej nie obsługują przepływu kodu uwierzytelniania.

1. W Azure Portal wybierz rejestrację aplikacji utworzoną wcześniej w temacie [Tworzenie rejestracji aplikacji](#create-the-app-registration).
1. W obszarze **Zarządzaj**wybierz pozycję **uwierzytelnianie**, a następnie wybierz pozycję **Dodaj platformę**.
1. W obszarze **aplikacje sieci Web**wybierz kafelek **aplikacji jednostronicowej** .
1. W obszarze **identyfikatory URI przekierowania**wprowadź [Identyfikator URI przekierowania](reply-url.md).
1. Włącz **przepływ niejawny**:
    - Jeśli aplikacja loguje się do użytkowników, wybierz pozycję **identyfikatory tokenów**.
    - Jeśli aplikacja wymaga również wywołania chronionego internetowego interfejsu API, wybierz pozycję **tokeny dostępu**. Aby uzyskać więcej informacji na temat tych typów tokenów, zobacz [identyfikatory tokenów](id-tokens.md) i [tokeny dostępu](access-tokens.md).
1. Wybierz pozycję **Konfiguruj** , aby zakończyć dodawanie identyfikatora URI przekierowania.

Zakończono rejestrację aplikacji jednostronicowej (SPA) i skonfigurowano identyfikator URI przekierowania, do którego zostanie przekierowany klient, i zostaną wysłane wszystkie tokeny zabezpieczające. Wybranie jednego lub obu **tokenów identyfikatorów** i **tokenów dostępu**umożliwiło włączenie niejawnego przepływu dotacji.

## <a name="note-about-authorization-flows"></a>Uwaga dotycząca przepływów autoryzacji

Domyślnie Rejestracja aplikacji utworzona przy użyciu konfiguracji platformy aplikacji jednostronicowej umożliwia przepływ kodu autoryzacji. Aby skorzystać z tego przepływu, aplikacja musi używać MSAL. js 2,0 lub nowszego.

Jak wspomniano wcześniej, aplikacje jednostronicowe używające MSAL. js 1,3 są ograniczone do niejawnego przepływu dotacji. Bieżące [najlepsze rozwiązania związane z uwierzytelnianiem OAuth 2,0](v2-oauth2-auth-code-flow.md) zaleca się użycie przepływu kodu autoryzacji zamiast niejawnego przepływu dla aplikacji jednostronicowych. Posiadanie tokenów odświeżania o ograniczonym okresie istnienia ułatwia również aplikacji dostosowanie do [nowoczesnej ochrony plików cookie w przeglądarce](reference-third-party-cookies-spas.md), takich jak Safari itp.

Jeśli wszystkie produkcyjne aplikacje jednostronicowe reprezentowane przez rejestrację aplikacji używają MSAL. js 2,0 i przepływu kodu autoryzacji, usuń zaznaczenie pola wyboru niejawne ustawienie w okienku **uwierzytelnianie** aplikacji w Azure Portal. Aplikacje korzystające z MSAL. js 1. x i niejawnego przepływu mogą jednak nadal działać, ale w przypadku pozostawienia niejawnego przepływu włączonego (zaznaczone).

## <a name="next-steps"></a>Następne kroki

Następnie skonfiguruj kod aplikacji w taki sposób, aby korzystał z rejestracji aplikacji utworzonej w poprzednich krokach:.

> [!div class="nextstepaction"]
> [Konfiguracja kodu aplikacji](scenario-spa-app-configuration.md)
