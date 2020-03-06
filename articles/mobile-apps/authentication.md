---
title: Dodawanie uwierzytelniania do aplikacji mobilnych za pomocą usług Visual Studio App Center i Azure
description: Dowiedz się więcej na temat usług takich jak Visual Studio App Center, które ułatwiają Konfigurowanie uwierzytelniania użytkowników i umożliwiają uwierzytelnianie aplikacji mobilnych przy użyciu kont społecznościowych, Azure Active Directory i uwierzytelniania niestandardowego.
author: elamalani
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 261c12e63f8f348e1673dbc46c89c614caf3283b
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78296358"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Dodawanie uwierzytelniania i zarządzanie tożsamościami użytkowników w aplikacjach mobilnych

Udostępnienie użytkownikowi i ich zachowania w aplikacji pozwala deweloperom lepiej zaangażować użytkowników, tworząc dostosowane do nich środowiska. Niezależnie od tego, czy jesteś deweloperem aplikacji tworzącym aplikację do współpracy dla użytkowników w organizacji, czy tworzysz następną platformę sieci społecznościowej, musisz mieć możliwość uwierzytelniania użytkowników i zarządzania tożsamościami użytkowników. Usługa zarządzania tożsamościami jest jedną z najważniejszych funkcji mobilnej usługi zaplecza.

Użyj następujących usług, aby włączyć uwierzytelnianie użytkowników w aplikacjach mobilnych.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center auth](/appcenter/auth/) to oparta na chmurze usługa zarządzania tożsamościami, której deweloperzy mogą używać do uwierzytelniania użytkowników i zarządzania tożsamościami użytkowników. Uwierzytelnianie App Center integruje się również z innymi częściami Visual Studio App Center. Deweloperzy mogą używać tożsamości użytkownika do [wyświetlania danych użytkownika](/appcenter/data/index) w innych usługach, a nawet [wysyłać powiadomienia wypychane do użytkowników zamiast poszczególnych urządzeń](/appcenter/push/push-to-user#setting-user-identity). 

**Najważniejsze funkcje**
- Obsługiwane przez Azure Active Directory B2C (Azure AD B2C). 
    - Klasy korporacyjnej.
    - Wysoka dostępność.
    - Usługa zabezpieczona i globalna.
- Skorzystaj z własnej tożsamości i opcji, aby korzystać z innych popularnych dostawców zarządzania tożsamościami i dostępem, takich jak rozwiązanie Auth0 i Firebase.
- Obsługa Azure Active Directory.
    - Połącz istniejących dzierżawców usługi Azure AD. 
    - Włącz uwierzytelnianie w domenie firmowej.
    - Zarządzanie dostępem do poufnych danych.
- Proste środowisko użytkownika i środowisko Magical SDK dzięki zapakowaniu biblioteki uwierzytelniania firmy Microsoft z zestawem SDK Visual Studio App Center.
- Obsługa platformy dla systemów iOS, Android, Xamarin i reagowanie na środowisko natywne.

**Wołują**
- [Zarejestruj się w usłudze Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [Wprowadzenie do uwierzytelniania App Center](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) to usługa zarządzania tożsamościami między konsumentami (B2C), której deweloperzy mogą używać do uwierzytelniania klientów. Ta usługa białych etykiet umożliwia deweloperom Dostosowywanie i kontrolowanie sposobu, w jaki użytkownicy bezpiecznie współdziałają z aplikacjami sieci Web, komputerami przenośnymi lub urządzeniami jednostronicowymi. Za pomocą usługi Azure AD B2C użytkownicy mogą tworzyć konta, logować się, resetować hasła i edytować profile. W usłudze Azure AD B2C zaimplementowano formę protokołów OpenID Connect i OAuth 2.0. 

**Najważniejsze funkcje**
- Bezpieczne uwierzytelnianie klientów przy użyciu preferowanego dostawcy tożsamości.
- Zarządzanie tożsamościami i dostępem klientów.
- Uzyskaj pomoc w zakresie logowania dla mediów społecznościowych, takich jak Facebook, GitHub, Google, LinkedIn, Twitter, WeChat i Weibo.
- Połącz się z kontami użytkowników przy użyciu standardowych protokołów branżowych, takich jak OpenID Connect Connect lub SAML, aby umożliwić zarządzanie tożsamościami na różnych platformach.
- Zapewnianie rejestracji markowej i logowania.
- Łatwa integracja z bazami danych programu CRM, narzędziami do analizy marketingowej i systemami weryfikacji kont.
- Przechwyć dane logowania, preferencji i konwersji dla klientów.

**Wołują**
- [Azure Portal](https://portal.azure.com/)
- [Dokumentacja Azure AD B2C](/azure/active-directory-b2c/)
- [Przewodniki Szybki start](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Przykłady](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) to usługa zarządzania tożsamościami i dostępem w chmurze firmy Microsoft, która ułatwia pracownikom zalogowanie się i uzyskanie dostępu do:
- Zasoby zewnętrzne, takie jak Microsoft Office 365, Azure Portal i tysiące innych aplikacji oprogramowania jako usługi (SaaS).
- Zasoby wewnętrzne, takie jak aplikacje w sieci firmowej i intranecie, a także aplikacje w chmurze opracowane przez organizację.

**Najważniejsze funkcje**
- Bezproblemowe i wysoce bezpieczny dostęp dzięki połączeniu użytkowników z wymaganymi aplikacjami.
- Kompleksowa ochrona tożsamości oraz lepsze zabezpieczenia tożsamości i dostępu oparte na użytkowniku, lokalizacji, urządzeniu, danych i kontekście aplikacji.
- Tysiące wstępnie zintegrowanych aplikacji dla aplikacji komercyjnych i niestandardowych, takich jak Office 365, Salesforce.com i Box.
- Możliwość zarządzania dostępem w dużej skali.

**Wołują**
- [Azure Portal](https://portal.azure.com/)
- [Co to jest usługa Azure AD?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Rozpoczynanie pracy z usługą Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
- [Przewodniki Szybki start](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)