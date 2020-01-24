---
title: Aplikacje jednostronicowe w Azure Active Directory
description: Opisuje aplikacje jednostronicowe (aplikacji jednostronicowych) i podstawowe informacje dotyczące przepływu protokołu, rejestracji i wygaśnięcia tokenu dla tego typu aplikacji.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: d956bdfe793f2e8fa3ce8ce83e03b9dae8033757
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701437"
---
# <a name="single-page-applications"></a>Aplikacje jednostronicowe

Aplikacje jednostronicowe (aplikacji jednostronicowych) są zwykle uporządkowane jako warstwa prezentacji JavaScript (fronton) uruchamianej w przeglądarce oraz zaplecza interfejsu API sieci Web, który działa na serwerze i implementuje logikę biznesową aplikacji. Aby dowiedzieć się więcej o niejawnym przyznaniu autoryzacji i pomóc w ustaleniu, czy jest to odpowiednie dla scenariusza aplikacji, zobacz [Opis niejawnego przepływu OAuth2 w Azure Active Directory](v1-oauth2-implicit-grant-flow.md).

W tym scenariuszu, gdy użytkownik się zaloguje, fronton języka JavaScript używa [Active Directory Authentication Library języka JavaScript (ADAL. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) i niejawne zezwolenie na uzyskanie tokenu identyfikatora (id_token) z usługi Azure AD. Token jest buforowany i klient dołącza go do żądania jako token okaziciela podczas wykonywania wywołań do zaplecza interfejsu API sieci Web, który jest zabezpieczony przy użyciu oprogramowania pośredniczącego OWIN.

## <a name="diagram"></a>Diagram

![Diagram aplikacji jednostronicowej](./media/authentication-scenarios/single_page_app.png)

## <a name="protocol-flow"></a>Przepływ protokołu

1. Użytkownik nawiguje do aplikacji sieci Web.
1. Aplikacja zwraca fronton języka JavaScript (warstwę prezentacji) do przeglądarki.
1. Użytkownik inicjuje logowanie, na przykład przez kliknięcie linku logowania. Przeglądarka wysyła do punktu końcowego autoryzacji usługi Azure AD żądanie tokenu identyfikatora. To żądanie zawiera identyfikator aplikacji i adres URL odpowiedzi w parametrach zapytania.
1. Usługa Azure AD sprawdza poprawność adresu URL odpowiedzi dla zarejestrowanego adresu URL odpowiedzi, który został skonfigurowany w Azure Portal.
1. Użytkownik loguje się na stronie logowania.
1. W przypadku pomyślnego uwierzytelnienia usługa Azure AD tworzy token identyfikatora i zwraca go jako fragment adresu URL (#) do adresu URL odpowiedzi aplikacji. W przypadku aplikacji produkcyjnej ten adres URL odpowiedzi powinien mieć wartość HTTPS. Zwrócony token zawiera oświadczenia dotyczące użytkownika i usługi Azure AD, które są wymagane przez aplikację do weryfikacji tokenu.
1. Kod klienta JavaScript uruchomiony w przeglądarce wyodrębnia token z odpowiedzi na potrzeby zabezpieczania wywołań do zaplecza internetowego interfejsu API aplikacji.
1. Przeglądarka wywołuje zaplecza interfejsu API sieci Web aplikacji z tokenem ID w nagłówku autoryzacji. Usługa uwierzytelniania usługi Azure AD wystawia token identyfikatora, którego można użyć jako tokenu okaziciela, jeśli zasób jest taki sam jak identyfikator klienta (w tym przypadku jest to prawdziwe, ponieważ internetowy interfejs API jest własnym zapleczem aplikacji).

## <a name="code-samples"></a>Przykłady kodu

Zobacz [przykłady kodu dla scenariuszy aplikacji jednostronicowych](sample-v1-code.md#single-page-applications). Pamiętaj o częstym sprawdzaniu, czy często dodawane są nowe przykłady.

## <a name="app-registration"></a>Rejestracja aplikacji

* Pojedyncze dzierżawy — Jeśli tworzysz aplikację tylko dla organizacji, musi ona być zarejestrowana w katalogu firmy przy użyciu Azure Portal.
* Wiele dzierżawców — w przypadku kompilowania aplikacji, która może być używana przez użytkowników spoza organizacji, musi być ona zarejestrowana w katalogu firmy, ale również musi być zarejestrowana w katalogu każdej organizacji, który będzie używać aplikacji. Aby udostępnić aplikację w swoim katalogu, możesz dołączyć proces rejestracji dla klientów, który umożliwia im zgodę na korzystanie z aplikacji. Po zarejestrowaniu się w aplikacji zostanie wyświetlone okno dialogowe, w którym są wyświetlane uprawnienia wymagane przez aplikację, a następnie opcja wyrażania zgody. W zależności od wymaganych uprawnień administrator w innej organizacji może być zobowiązany do udzielenia zgody. Gdy użytkownik lub administrator wyraził zgodę, aplikacja zostanie zarejestrowana w swoim katalogu.

Po zarejestrowaniu aplikacji należy ją skonfigurować tak, aby korzystała z niejawnego wydawanie protokołu OAuth 2,0. Domyślnie ten protokół jest wyłączony dla aplikacji. Aby włączyć niejawny protokół OAuth2 dla aplikacji, Edytuj manifest aplikacji z Azure Portal i ustaw wartość "oauth2AllowImplicitFlow" na true. Aby uzyskać więcej informacji, zobacz [manifest aplikacji](reference-app-manifest.md).

## <a name="token-expiration"></a>Wygaśnięcie tokenu

Korzystanie z biblioteki ADAL. js ułatwia:

* Odświeżanie wygasłego tokenu
* Żądanie tokenu dostępu w celu wywołania zasobu internetowego interfejsu API

Po pomyślnym uwierzytelnieniu usługa Azure AD zapisuje plik cookie w przeglądarce użytkownika w celu ustanowienia sesji. Zwróć uwagę na to, że sesja istnieje między użytkownikiem a usługą Azure AD (nie między użytkownikiem a aplikacją sieci Web). Po wygaśnięciu tokenu ADAL. js używa tej sesji w celu dyskretnego uzyskania innego tokenu. Biblioteka ADAL. js używa ukrytego elementu iFrame do wysyłania i odbierania żądania przy użyciu niejawnego protokołu uwierzytelniania OAuth. Biblioteka ADAL. js może również używać tego samego mechanizmu, aby w trybie dyskretnym uzyskać tokeny dostępu dla innych zasobów interfejsu API sieci Web, w przypadku których te zasoby obsługują udostępnianie zasobów między źródłami (CORS), są rejestrowane w katalogu użytkownika i wszystkie wymagane zgody zostały określone przez użytkownika podczas logowania.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [typach aplikacji i scenariuszach](app-types.md)
* Poznaj podstawowe informacje na temat [uwierzytelniania](v1-authentication-scenarios.md) usługi Azure AD
