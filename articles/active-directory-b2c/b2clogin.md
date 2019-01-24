---
title: Zestaw przekierowania adresów URL z usługi b2clogin.com dla usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o korzystaniu z usługi b2clogin.com w Twojego przekierowania adresów URL dla usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 147d8e80c66bd47ed56400deca024532fc7e39c7
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54844930"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Zestaw przekierowania adresów URL z usługi b2clogin.com dla usługi Azure Active Directory B2C

Po skonfigurowaniu dostawcy tożsamości dla rejestracji i logowania w aplikacji Azure Active Directory (Azure AD) B2C, musisz określić adres URL przekierowania. W przeszłości login.microsoftonline.com został użyty po stronie b2clogin.com powinien być używany.

Korzystanie z usługi b2clogin.com zapewnia dodatkowe korzyści, takich jak:

- Pliki cookie nie są już są współużytkowane z innymi usługami firmy Microsoft.
- Adresami URL nie będzie zawierać odniesienie do firmy Microsoft. Na przykład `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`.

Należy wziąć pod uwagę te ustawienia, które może być konieczna zmiana podczas korzystania z usługi b2clogin.com:

- Ustaw przekierowania adresów URL w aplikacji dostawcy tożsamości z usługi b2clogin.com. 
- Ustaw aplikację usługi Azure AD B2C, aby korzystać z usługi b2clogin.com dla odwołania do przepływu użytkownika i token punktów końcowych. 
- Jeśli używasz biblioteki MSAL, musisz ustawić **ValidateAuthority** właściwość `false`.
- Upewnij się, że wprowadzisz zmiany w dowolnej **dozwolone źródła** zdefiniowanego w ustawieniach mechanizmu CORS [dostosowywania interfejsu użytkownika](active-directory-b2c-ui-customization-custom-dynamic.md).  

## <a name="change-redirect-urls"></a>Zmiana przekierowania adresów URL

Aby korzystać z usługi b2clogin.com, w ustawieniach aplikacji dostawcy tożsamości, Znajdź i zmienić listę zaufanych adresów URL do przekierowania usługi Azure AD B2C.  Obecnie możesz prawdopodobnie jest skonfigurowany do Przekieruj z powrotem do lokacji niektóre login.microsoftonline.com. 

Musisz zmienić adres URL przekierowania tak, aby `your-tenant-name.b2clogin.com` jest autoryzowany. Upewnij się zastąpić `your-tenant-name` o nazwie usługi Azure AD B2C dzierżawy i Usuń `/te` istnienia w adresie URL. Ma niewielkie zmiany do tego adresu URL, dla każdego dostawcy tożsamości, więc zajrzyj odpowiadającą mu stronę, aby uzyskać dokładny adres URL.

Informacje o konfiguracji dla dostawcy tożsamości można znaleźć w następujących artykułach:

- [Konto Microsoft](active-directory-b2c-setup-msa-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Google](active-directory-b2c-setup-goog-app.md)
- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [LinkedIn](active-directory-b2c-setup-li-app.md)
- [Twitter](active-directory-b2c-setup-twitter-app.md)
- [GitHub](active-directory-b2c-setup-github-app.md)
- [Weibo](active-directory-b2c-setup-weibo-app.md)
- [QQ](active-directory-b2c-setup-qq-app.md)
- [WeChat](active-directory-b2c-setup-wechat-app.md)
- [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md)
- [Niestandardowe OIDC](active-directory-b2c-setup-oidc-idp.md)

## <a name="update-your-application"></a>Aktualizowanie aplikacji

Aplikacja usługi Azure AD B2C, ale prawdopodobnie dotyczy `login.microsoftonline.com` w kilku miejscach, takie jak odwołania do przepływu użytkownika i token punktów końcowych.  Upewnij się, że Twoje punkt końcowy autoryzacji punktu końcowego tokenu i Wystawca zostały zaktualizowane na potrzeby `your-tenant-name.b2clogin.com`.  

## <a name="set-the-validateauthority-property"></a>Ustaw właściwość ValidateAuthority

Jeśli używasz biblioteki MSAL **ValidateAuthority** do `false`. Poniższy przykład pokazuje, jak można ustawić właściwości:

W [biblioteki MSAL dla platformy .net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet):

```CSharp
 ConfidentialClientApplication client = new ConfidentialClientApplication(...); // can also be PublicClientApplication
 client.ValidateAuthority = false;
```

A następnie w [biblioteki MSAL dla języka Javascript](https://github.com/AzureAD/microsoft-authentication-library-for-js):

```Javascript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```
