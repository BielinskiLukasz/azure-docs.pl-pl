---
title: Konfigurowanie logowania jednokrotnego aplikacji
description: Jak skonfigurować Logowanie jednokrotne dla aplikacji niestandardowej, którą tworzysz i rejestrujesz w usłudze Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.openlocfilehash: 465adbb71abaa45160399ecba2ebfb692a8307c2
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120681"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Jak skonfigurować Logowanie jednokrotne dla aplikacji

Włączenie federacyjnego logowania jednokrotnego w aplikacji jest automatycznie włączane, gdy federowanie za pomocą usługi Azure AD dla OpenID Connect Connect, SAML 2,0 lub WS-karmione. Jeśli użytkownicy końcowi będą mogli się zalogować pomimo posiadania już istniejącej sesji z usługą Azure AD, prawdopodobnie aplikacja może być błędnie skonfigurowana.

* Jeśli używasz biblioteki ADAL/MSAL, upewnij się, że masz **PromptBehavior** ustawioną na wartość **Autostart** , a nie **zawsze**.

* W przypadku kompilowania aplikacji mobilnej może być konieczne dodanie dodatkowych konfiguracji w celu włączenia obsługi rejestracji Jednokrotnej dla brokera lub nieobsługiwanego przez brokera.

W przypadku systemu Android zobacz [Włączanie logowania jednokrotnego aplikacji w systemie Android](../azuread-dev/howto-v1-enable-sso-android.md).<br>

W przypadku systemu iOS zobacz [Włączanie logowania jednokrotnego aplikacji w systemie iOS](../azuread-dev/howto-v1-enable-sso-ios.md).

## <a name="next-steps"></a>Następne kroki

[Logowanie jednokrotne usługi Azure AD](../manage-apps/what-is-single-sign-on.md)<br>

[Włączanie logowania jednokrotnego aplikacji w systemie Android](../azuread-dev/howto-v1-enable-sso-android.md)<br>

[Włączanie logowania jednokrotnego aplikacji w systemie iOS](../azuread-dev/howto-v1-enable-sso-ios.md)<br>

[Integrowanie aplikacji z usługą AzureAD](./quickstart-register-app.md)<br>

[Uprawnienia i zgoda w punkcie końcowym platformy tożsamości firmy Microsoft](./v2-permissions-and-consent.md)<br>

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)