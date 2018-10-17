---
title: Porównanie współpracy typu B2B i B2C w usłudze Azure Active Directory | Microsoft Docs
description: Jaka jest różnica między współpracą w usłudze Azure Active Directory B2B i w usłudze Azure AD B2C?
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: overview
ms.date: 03/15/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 42fbb8b08a2dc24ced436c4a6104f03ae3bca1e9
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982814"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Porównanie współpracy typu B2B i B2C w usłudze Azure Active Directory

Współpraca w usłudze Azure Active Directory (Azure AD) B2B i usługa Azure AD B2C umożliwiają pracę z użytkownikami zewnętrznymi w usłudze Azure AD. Ale jak wypada ich porównanie?

Usługa **Azure AD B2B** jest przeznaczona dla firm, które chcą bezpiecznie udostępniać pliki i zasoby użytkownikom zewnętrznym na potrzeby współpracy. Administrator platformy Azure ustawia usługę B2B w witrynie Azure Portal, a usługa Azure AD zajmuje się federacją między firmą i partnerem zewnętrznym. Użytkownicy logują się do udostępnionych zasobów przy użyciu prostego procesu zapraszania i realizacji oraz konta służbowego lub dowolnego konta poczty e-mail.
 
Usługa **Azure AD B2C** jest przeznaczona głównie dla firm i deweloperów zajmujących się tworzeniem aplikacji współpracujących z klientami. Dzięki usłudze Azure AD B2C deweloperzy mogą używać usługi Azure AD jako systemu tożsamości wyposażonego w pełen zestaw funkcji dla swojej aplikacji, pozwalając równocześnie klientom na logowanie przy użyciu wcześniej ustanowionej tożsamości (np. w serwisie Facebook lub usłudze Gmail).

Poniższa tabela zawiera szczegółowe porównanie.


Możliwości współpracy w usłudze B2B |     Autonomiczna oferta usługi Azure AD B2C
-------- | --------
Przeznaczenie: organizacje, które chcą uwierzytelniać użytkowników z organizacji partnerskiej, niezależnie od dostawcy tożsamości. | Przeznaczenie: zapraszanie klientów aplikacji mobilnych i internetowych — osób prywatnych, instytucji lub organizacji — do usługi Azure AD.
Obsługiwane tożsamości: pracownicy z kontami służbowymi, partnerzy z kontami służbowymi lub dowolny adres e-mail. Wkrótce będzie możliwa obsługa federacji bezpośredniej.  | Obsługiwane tożsamości: użytkownicy konsumenci z kontami aplikacji lokalnych (dowolny adres e-mail lub nazwa użytkownika) albo dowolna obsługiwana tożsamość społecznościowa z federacją bezpośrednią.
Katalog, w którym znajdują się użytkownicy partnerscy: użytkownicy partnerscy z organizacji zewnętrznej są zarządzani w tym samym katalogu co pracownicy, ale ze specjalnymi adnotacjami. Można nimi zarządzać tak samo jak pracownikami, dodawać ich do tych samych grup itd.  | Katalog, w którym znajdują się jednostki użytkownika klienta: katalog aplikacji. Zarządzane oddzielnie od katalogu partnerów i pracowników organizacji (jeśli istnieje).
Logowanie jednokrotne do wszystkich aplikacji połączonych z usługą Azure AD jest obsługiwane. Można zapewnić dostęp do usługi Office 365 lub aplikacji lokalnych oraz innych aplikacji SaaS, takich jak Salesforce lub Workday.  |  Logowanie jednokrotne do aplikacji należących do klienta w ramach dzierżaw usługi Azure AD B2C jest obsługiwane. Logowanie jednokrotne do usługi Office 365 lub innych aplikacji SaaS firmy Microsoft i innych firm nie jest obsługiwane.
Cykl życia partnera: zarządzany przez hosta/organizację zapraszającą.  | Cykl życia klienta: samoobsługowy lub zarządzany przez aplikację.
Zasady zabezpieczeń i zgodność: zarządzane przez hosta/organizację zapraszającą (na przykład za pomocą [zasad dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)).  | Zasady zabezpieczeń i zgodność: zarządzane przez aplikację.
Znakowanie: jest używana marka hosta/organizacji zapraszającej.  |    Znakowanie: zarządzane przez aplikację. Zazwyczaj jest używana marka produktu z danymi organizacji w tle.
Więcej informacji: [wpis w blogu](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [dokumentacja](what-is-b2b.md)  | Więcej informacji: [strona produktu](https://azure.microsoft.com/services/active-directory-b2c/), [dokumentacja](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>Następne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [B2B collaboration user properties (Właściwości użytkowników współpracy B2B)](user-properties.md)

