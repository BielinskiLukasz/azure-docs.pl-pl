---
title: Wytyczne dotyczące oznaczania aplikacji marką | Microsoft Docs
description: Kompletny przewodnik dotyczący zasobów dla deweloperów usługi Azure Active Directory
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 72f4e464-1352-4a49-a18f-c37f58e7d5c4
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: arielgo
ms.custom: aaddev, signin_art
ms.openlocfilehash: 37d61b89dfe6c38124894ed286b67c4724f4efcd
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2019
ms.locfileid: "55093479"
---
# <a name="branding-guidelines-for-applications"></a>Wytyczne dotyczące oznaczania aplikacji marką

Podczas opracowywania aplikacji przy użyciu usługi Azure Active Directory (Azure AD) będziesz musieć udzielać pomocy klientom, którzy chcą używać konta służbowego zarządzanego przez usługę Azure AD lub konta osobistego na potrzeby tworzenia konta i logowania w aplikacji.

W tym artykule:

- Poznasz dwa rodzaje kont użytkowników zarządzanych przez firmę Microsoft i dowiesz się, jak odwoływać się do konta usługi Azure AD w aplikacji.
- Dowiesz się, co jest potrzebne, aby dodać logo firmy Microsoft do użycia w aplikacji.
- Pobierzesz oficjalne obrazy **Zaloguj się** lub **Zaloguj się przy użyciu konta Microsoft** do wykorzystania w aplikacji.
- Poznasz zalecenia oraz zakazy dotyczące znakowania i nawigacji.

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Konta osobiste a służbowe firmy Microsoft

Firma Microsoft zarządza dwoma rodzajami kont użytkowników:

- **Konta osobiste** (wcześniej konta Windows Live ID). Te konta reprezentują relację między *poszczególnymi* użytkownikami i firmą Microsoft oraz umożliwiają uzyskiwanie dostępu do urządzeń klientów i usług firmy Microsoft. Te konta są przeznaczone do użytku osobistego.
- **Konta służbowe.** Te konta są zarządzane przez firmę Microsoft w imieniu organizacji, która używa usługi Azure Active Directory. Te konta umożliwiają logowanie się do usługi Office 365 i innych usług biznesowych firmy Microsoft.

Konta służbowe firmy Microsoft zwykle są przypisane do użytkowników końcowych (pracowników, uczniów, studentów, pracowników federalnych) przez ich organizacje (firmy, szkoły, agencje rządowe). Te konta są zarządzane bezpośrednio w chmurze (na platformie Azure AD) lub synchronizowane z usługą Azure AD z poziomu katalogu lokalnego, takiego jak usługa Active Directory systemu Windows Server. Firma Microsoft jest *nadzorcą* kont służbowych, ale należą one do organizacji i są przez nią kontrolowane.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Odwoływanie się do kont usługi Azure AD w aplikacji

Firma Microsoft nie pokazuje użytkownikom końcowym nazw marek platformy Azure lub usługi Active Directory i nie należy tego robić.

- Gdy użytkownicy są zalogowani, należy możliwie najczęściej używać nazwy i logo organizacji. Jest to lepsze rozwiązanie niż używanie ogólnych terminów, takich jak „Twoja organizacja”.
- Gdy użytkownicy nie są zalogowani, należy nazywać ich konta „kontami służbowymi” i używać logo firmy Microsoft, aby pokazać, że te konta są zarządzane przez firmę Microsoft. Nie należy używać takich terminów jak „konto przedsiębiorstwa”, „konto biznesowe” lub „konto firmowe”, które są mylące dla użytkowników.

## <a name="user-account-pictogram"></a>Piktogram konta użytkownika

We wcześniejszej wersji tych wytycznych zalecamy używanie piktogramu niebieskiej odznaki. Na podstawie opinii użytkowników i deweloperów teraz zalecamy używanie logo firmy Microsoft zamiast tego. Logo firmy Microsoft wskazuje, że użytkownicy mogą zalogować się do aplikacji za pomocą konta używanego w usłudze Office 365 lub innych usługach biznesowych firmy Microsoft.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Tworzenie konta i logowanie się za pomocą usługi Azure AD

Aplikacja może mieć różne procedury tworzenia konta i logowania. W poniższych sekcjach przedstawiono wizualne wskazówki dla obu scenariuszy.

**Jeśli aplikacja obsługuje rejestrację użytkownika końcowego (np. w celu skorzystania z bezpłatnej wersji próbnej lub w modelu freemium)**: Możesz wyświetlić przycisk **logowania**, który umożliwia użytkownikom uzyskanie dostępu do aplikacji za pomocą konta służbowego lub konta osobistego. Przy pierwszym uzyskaniu dostępu do aplikacji w usłudze Azure AD jest wyświetlany monit o wyrażenie zgody.

**Jeśli organizacja wymaga uprawnień, na które zgodę mogą wyrazić tylko administratorzy, lub aplikacja wymaga licencjonowania organizacji**: Należy oddzielić dostęp administratora od logowania użytkowników. Użycie **przycisku „Pobierz tę aplikację”** powoduje przekierowanie administratorów do logowania, a następnie wyświetlenie monitu o wyrażenie zgody w imieniu użytkowników w organizacji, co ma dodatkową zaletę w postaci pomijania monitów o wyrażenie zgody użytkowników końcowych w aplikacji.

## <a name="visual-guidance-for-app-acquisition"></a>Wizualne wskazówki dotyczące pozyskiwania aplikacji

Użycie linku „Pobierz aplikację” musi powodować przekierowanie użytkownika do strony udzielania dostępu (autoryzacji) usługi Azure AD, aby umożliwić administratorowi organizacji autoryzowanie aplikacji na potrzeby dostępu do danych organizacji hostowanych przez firmę Microsoft. Szczegółowe informacje dotyczące żądania dostępu przedstawiono w artykule [Integrating Applications with Azure Active Directory (Integrowanie aplikacji w usłudze Azure Active Directory)](quickstart-v1-integrate-apps-with-azure-ad.md).

Gdy administratorzy wyrażą zgodę na aplikację, będą mogli dodać ją do środowiska uruchamiania aplikacji usługi Office 365 użytkowników (dostępne w menu typu „gofr” i na stronie [https://portal.office.com/myapps](https://portal.office.com/myapps)). Aby informować o tej możliwości, można użyć takich terminów jak „Dodaj tę aplikację do organizacji” i wyświetlić przycisk, jak w poniższym przykładzie:

![Scenariusze i typy aplikacji](./media/howto-add-branding-in-azure-ad-apps/add-to-my-org.png)

Jednak zalecamy napisanie tekstu objaśnienia, aby nie trzeba było używać tylko przycisków. Na przykład:

> *Jeśli używasz już usługi Office 365 lub innej usługi biznesowej firmy Microsoft, możesz udzielić aplikacji <nazwa_aplikacji> dostępu do danych organizacji. Umożliwi to użytkownikom dostęp do aplikacji <nazwa_aplikacji> przy użyciu istniejących kont służbowych.*

Aby pobrać oficjalne logo firmy Microsoft do użycia w aplikacji, kliknij prawym przyciskiem myszy logo, którego chcesz użyć, a następnie zapisz je w komputerze.

| Element zawartości                                | Format PNG | Format SVG |
| ------------------------------------ | ---------- | ---------- |
| Logo firmy Microsoft  | ![Logo firmy Microsoft (PNG)](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_mssymbol_19.png) | ![Logo firmy Microsoft (SVG)](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_mssymbol_19.svg) |

## <a name="visual-guidance-for-sign-in"></a>Wizualne wskazówki dotyczące logowania

W aplikacji powinien być wyświetlany przycisk logowania, którego użycie powoduje przekierowanie użytkowników do punktu końcowego logowania zgodnego z protokołem używanym na potrzeby integracji z usługą Azure AD. W poniższej sekcji przedstawiono szczegółowe zalecenia dotyczące wyglądu tego przycisku.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Piktogram i tekst „Zaloguj się przy użyciu konta Microsoft”

Połączenie logo firmy Microsoft i tekstu „Zaloguj się przy użyciu konta Microsoft” pozwalają jednoznacznie odróżnić usługę Azure AD od innych dostawców tożsamości obsługiwanych przez aplikację. Jeśli nie masz wystarczającej ilości miejsca na tekst „Zaloguj się przy użyciu konta Microsoft”, możesz skrócić go do tekstu „Zaloguj się”. Możesz użyć jasnego lub ciemnego schematu kolorów przycisków.

Na poniższym diagramie pokazano granice zalecane przez firmę Microsoft podczas używania elementów zawartości w aplikacji. Te granice dotyczą tekstu „Zaloguj się przy użyciu konta Microsoft” i jego krótszej wersji („Zaloguj się”).

![Granice dla tekstu Zaloguj się przy użyciu konta Microsoft](./media/howto-add-branding-in-azure-ad-apps/sign-in-with-microsoft-redlines.png)

Aby pobrać oficjalne obrazy do użycia w aplikacji, kliknij prawym przyciskiem myszy obraz, którego chcesz użyć, a następnie zapisz go w komputerze.

| Element zawartości                                | Format PNG | Format SVG |
| ------------------------------------ | ---------- | ---------- |
| Zaloguj się przy użyciu konta Microsoft (motyw ciemny)  | ![Przycisk „Zaloguj się” w formacie PNG (motyw ciemny)](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark.png) | ![Przycisk „Zaloguj się przy użyciu konta Microsoft” w formacie SVG (motyw ciemny)](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark.svg) |
| Zaloguj się przy użyciu konta Microsoft (motyw jasny) | ![Przycisk „Zaloguj się” w formacie PNG (motyw jasny)](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light.png) | ![Przycisk „Zaloguj się przy użyciu konta Microsoft” w formacie SVG (motyw jasny)](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light.svg) |
| Zaloguj się (motyw ciemny)                 | ![Krótki przycisk „Zaloguj się” w formacie PNG (motyw ciemny)](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark_short.png) | ![Krótki przycisk „Zaloguj się” w formacie SVG (motyw ciemny)](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark_short.svg) |
| Zaloguj się (motyw jasny)                | ![Krótki przycisk „Zaloguj się” w formacie PNG (motyw jasny)](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light_short.png) | ![Krótki przycisk „Zaloguj się” w formacie SVG (motyw jasny)](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light_short.svg) |

## <a name="branding-dos-and-donts"></a>Zalecenia dotyczące oznaczania marką

**NALEŻY** używać konta służbowego w połączeniu z przyciskiem „Zaloguj się przy użyciu konta Microsoft” w celu dodatkowego wyjaśnienia, aby pomóc użytkownikom końcowym określić, czy mogą użyć konta. **NIE NALEŻY** używać innych zwrotów, takich jak „konto przedsiębiorstwa”, „konto biznesowe” lub „konto firmowe”.

**NIE NALEŻY** używać zwrotu „Identyfikator usługi Office 365” lub „Identyfikator platformy Azure”. Office 365 jest również nazwą oferty firmy Microsoft dla użytkowników indywidualnych, która nie korzysta z usługi Azure AD na potrzeby uwierzytelniania.

**NIE NALEŻY** zmieniać logo firmy Microsoft.

**NIE NALEŻY** pokazywać użytkownikom końcowym marki platformy Azure lub usługi Active Directory. Można jednak używać tych zwrotów podczas zwracania się do deweloperów, informatyków i administratorów.

## <a name="navigation-dos-and-donts"></a>Zalecenia dotyczące nawigacji

**NALEŻY** umożliwić użytkownikom wylogowanie się i przełączenie się do innego konta użytkownika. Większość osób ma jedno konto osobiste Microsoft/Facebook/Google/Twitter, jednak użytkownicy są często skojarzeni z więcej niż jedną organizacją. Wkrótce zostanie wprowadzona obsługa wielu zalogowanych użytkowników.
