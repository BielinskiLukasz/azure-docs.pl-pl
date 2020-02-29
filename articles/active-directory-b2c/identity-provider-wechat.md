---
title: Skonfiguruj konto i zaloguj się przy użyciu konta WeChat
titleSuffix: Azure AD B2C
description: Podaj konto i zaloguj się do klientów za pomocą kont usługi WeChat w swoich aplikacjach przy użyciu Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c53210939358255b20d0e976df9c4bff88580a80
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184439"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania przy użyciu konta usługi WeChat za pomocą Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-wechat-application"></a>Tworzenie aplikacji WeChat

Aby użyć konta WeChat jako dostawcy tożsamości w Azure Active Directory B2C (Azure AD B2C), musisz utworzyć aplikację w swojej dzierżawie, która go reprezentuje. Jeśli nie masz jeszcze konta WeChat, możesz uzyskać informacje w [https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Rejestrowanie aplikacji WeChat

1. Zaloguj się, aby [https://open.weixin.qq.com/](https://open.weixin.qq.com/) przy użyciu poświadczeń WeChat.
1. Wybierz pozycję**管理中心**(centrum zarządzania).
1. Postępuj zgodnie z instrukcjami, aby zarejestrować nową aplikację.
1. Wprowadź `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` w**授权回调域**(adres URL wywołania zwrotnego). Na przykład jeśli nazwa dzierżawy to contoso, ustaw adres URL na `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. Skopiuj **Identyfikator aplikacji** i **klucz aplikacji**. Będą one potrzebne do dodania dostawcy tożsamości do dzierżawy.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Konfigurowanie WeChat jako dostawcy tożsamości w dzierżawie

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **WeChat (wersja zapoznawcza)** .
1. Wprowadź **nazwę**. Na przykład *WeChat*.
1. W polu **Identyfikator klienta**wprowadź identyfikator aplikacji utworzonej wcześniej aplikacji WeChat.
1. Dla **wpisu tajnego klienta**Wprowadź zarejestrowany klucz aplikacji.
1. Wybierz pozycję **Zapisz**.
