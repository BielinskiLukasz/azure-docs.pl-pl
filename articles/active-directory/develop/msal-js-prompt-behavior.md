---
title: Zachowanie interakcyjnego monitu o żądanie (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak dostosować zachowanie monitu w wywołaniach interaktywnych przy użyciu biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL. js).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6654b523cef4623a55bf9f857fd5a723fae2dc47
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921966"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Zachowanie monitu w MSAL. js Interactive Requests

Gdy użytkownik ustanowił aktywną sesję usługi Azure AD z wieloma kontami użytkowników, na stronie logowania usługi Azure AD zostanie domyślnie wyświetlony monit o wybranie konta przed kontynuowaniem logowania. Jeśli istnieje tylko jedna uwierzytelniona sesja z usługą Azure AD, użytkownicy nie będą widzieć środowiska wyboru konta.

Biblioteka MSAL. js (rozpoczynająca się 0.2.4) nie wysyła parametru monitu podczas żądań interaktywnych (`loginRedirect`, `loginPopup`, `acquireTokenRedirect` i `acquireTokenPopup`), a tym samym nie wymusza żadnego zachowania monitu. W przypadku żądań tokenu dyskretnego za pomocą metody `acquireTokenSilent` MSAL. js przekazuje parametr monitu ustawiony na `none`.

Na podstawie scenariusza aplikacji można kontrolować zachowanie monitu dla żądań interaktywnych przez ustawienie parametru monitu w parametrach żądania przesłanych do metod. Na przykład jeśli chcesz wywołać środowisko wybierania konta:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Podczas uwierzytelniania za pomocą usługi Azure AD można przekazywać następujące wartości monitu:

**Logowanie:** Ta wartość spowoduje wymuszenie wprowadzenia przez użytkownika poświadczeń w żądaniu uwierzytelniania.

**select_account:** Ta wartość umożliwi użytkownikowi wybranie wszystkich kont w sesji.

**zgoda:** Ta wartość spowoduje wyzwolenie dialogu uwierzytelniania OAuth, który umożliwia użytkownikom Przyznawanie uprawnień do aplikacji.

**Brak:** Ta wartość zapewni, że użytkownik nie zobaczy żadnego interakcyjnego monitu. Zaleca się, aby nie przekazywać tej wartości do interaktywnych metod w MSAL. js, ponieważ mogą one mieć nieoczekiwane zachowania. Zamiast tego należy użyć metody `acquireTokenSilent`, aby uzyskać dyskretne wywołania.

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej na temat `prompt` parametru w [niejawnym protokole OAuth 2,0](v2-oauth2-implicit-grant-flow.md) , który jest używany przez bibliotekę MSAL. js.
