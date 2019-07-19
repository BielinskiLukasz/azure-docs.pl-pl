---
title: Dodawanie aplikacji wielodostępnej do galerii aplikacji usługi Azure AD | Microsoft Docs
description: Wyjaśnia, w jaki sposób można wyświetlić listę niestandardowej aplikacji wielodostępnej w galerii aplikacji usługi Azure AD.
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8024130b986d111abe3b470d6b06e86ed6419dc4
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320915"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Dodawanie aplikacji wielodostępnej do galerii aplikacji usługi Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>Co to jest Galeria aplikacji usługi Azure AD?

Azure Active Directory (Azure AD) to oparta na chmurze usługa tożsamości. [Galeria aplikacji usługi Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) znajduje się w sklepie z aplikacjami w portalu Azure Marketplace, w którym wszystkie łączniki aplikacji są publikowane pod kątem logowania jednokrotnego i aprowizacji użytkowników. Klienci korzystający z usługi Azure AD jako dostawcy tożsamości mogą znaleźć różne łączniki aplikacji SaaS opublikowane w tym miejscu. Administratorzy IT mogą dodawać łączniki z galerii aplikacji, a następnie konfigurować i używać łączników do logowania jednokrotnego i inicjowania obsługi. Usługa Azure AD obsługuje wszystkie główne protokoły federacyjne, w tym SAML 2,0, OpenID Connect Connect, OAuth i WS-karmione do logowania jednokrotnego. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Jeśli aplikacja obsługuje protokół SAML lub OpenIDConnect
Jeśli masz aplikację wielodostępną, którą chcesz znaleźć w galerii aplikacji usługi Azure AD, musisz najpierw upewnić się, że aplikacja obsługuje jedną z następujących technologii logowania jednokrotnego:

- **OpenID Connect**: Aby można było wyświetlić swoją aplikację, Utwórz aplikację wielodostępną w usłudze Azure AD i zaimplementuj [strukturę zgody na usługę Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) dla swojej aplikacji. Wyślij żądanie logowania do wspólnego punktu końcowego, aby klient mógł wyrazić zgodę na aplikację. Można kontrolować dostęp użytkownika na podstawie identyfikatora dzierżawy oraz nazwy UPN użytkownika otrzymanego w tokenie. Prześlij aplikację, wykonując czynności opisane w temacie Tworzenie [listy aplikacji w galerii aplikacji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

- ELEMENT **SAML**: Jeśli aplikacja obsługuje protokół SAML 2,0, aplikacja może być wymieniona w galerii. Postępuj zgodnie z instrukcjami wyświetlanymi w temacie Tworzenie [aplikacji w galerii aplikacji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Jeśli aplikacja nie obsługuje języka SAML lub OpenIDConnect
Aplikacje, które nie obsługują protokołu SAML ani OpenIDConnect, mogą być nadal zintegrowane z galerią aplikacji za pomocą technologii logowania jednokrotnego przy użyciu hasła.

Logowanie jednokrotne haseł, nazywane również magazynem haseł, umożliwia zarządzanie dostępem użytkowników i hasłami do aplikacji sieci Web, które nie obsługują federacji tożsamości. Jest również przydatne w scenariuszach, w których kilku użytkowników musi udostępniać pojedyncze konto, na przykład na kontach aplikacji Media społecznościowych w organizacji. 

Jeśli chcesz wystawić swoją aplikację za pomocą tej technologii:
1. Utwórz aplikację sieci Web, która zawiera stronę logowania w formacie HTML, aby skonfigurować Logowanie jednokrotne przy użyciu [hasła](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). 
2. Prześlij żądanie zgodnie z opisem w temacie [wyświetlanie aplikacji w galerii aplikacji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="escalations"></a>Ich eskalacji

W przypadku wszelkich eskalacji Wyślij wiadomość e-mail do [zespołu integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) , a my powrócimy do Ciebie najszybciej, jak to możliwe.

## <a name="next-steps"></a>Następne kroki
Dowiedz się [, jak wystawić swoją aplikację w galerii aplikacji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).
